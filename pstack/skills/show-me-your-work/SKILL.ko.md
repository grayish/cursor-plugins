---
name: show-me-your-work
description: "오래 걸리거나 사람이 지켜보지 않는 작업에서 리뷰할 수 있는 결정 기록을 남깁니다. 결정 하나당 한 행(무엇을, 왜, 근거, 결과)으로 된 TSV 로그입니다. 기본적으로 로컬에만 두고, 리뷰어가 결과를 신뢰하는 데 기록이 필요할 때 커밋합니다. /show-me-your-work, 자율 실행이나 여러 단계로 이루어진 실행, 또는 사람(사용자)이 자리를 비운 뒤 리뷰하는 작업에 사용합니다."
disable-model-invocation: true
---

> 이 문서는 [SKILL.md](./SKILL.md)를 한국어로 번역한 문서입니다. 원본과 내용이 다르면 원본을 기준으로 합니다.

# Show me your work

정본 로그 하나를 유지합니다.

## 형식

TSV 파일 하나에 결정 하나당 한 행을 씁니다. 각 셀은 한 줄로 유지합니다. 근거는 설명 문장이 아니라 위치를 가리키는 포인터입니다.

깨끗한 로그를 시작하려면 `references/decision-log-template.tsv`(헤더 행)를 복사합니다. 열은 다음과 같습니다.

- **ts.** ISO8601 타임스탬프입니다.
- **phase.** 단계 또는 작업 흐름입니다.
- **decision.** 무엇을 선택했거나 실행했는지 한 줄로 씁니다.
- **why.** 이유를 쉬운 말로 씁니다. 어떤 원칙이 결정을 이끌었다면 전문 용어 태그가 아니라 쉬운 말로 설명합니다.
- **evidence.** 이를 증명하는 링크나 경로입니다. 커밋 SHA, PR 번호, `file:line`, 또는 산출물, 트레이스, 스크린샷 경로를 씁니다. 단락은 절대 쓰지 않습니다.
- **result.** 결과 또는 판정 조건의 상태입니다. 예: `tests green`, `reverted`, `pixel-diff 0`, `INCONCLUSIVE`, `open`.

다음은 리뷰어가 한눈에 읽을 수 있도록 쉬운 말로 쓴 예시입니다.

```
ts	phase	decision	why	evidence	result
2026-05-24T09:02:00Z	frame	counted the work first, about 100 components and roughly 75 hours	wanted to know the size before starting a long run	commit 3a9f1c2	found 5 things to sort out before starting
2026-05-24T09:40:00Z	harness	took screenshots of the old version before changing anything	so we can compare old against new and catch any visual change	scripts/snapshot.sh, baseline/	saved 120 reference screenshots
2026-05-24T11:15:00Z	widget	moved the widget styles over without changing how it looks	keep the change small and the result identical	commit 7c21e0a, pixel-diff 0	looks identical, tests pass
2026-05-24T12:30:00Z	widget	threw out a helper's work because its screenshots were blank	checked the real files instead of trusting its summary	worktree reset	reverted, tightened the instructions for next time
```

## 행 기록하기

각 항목은 동료에게 무엇을 했는지 말하듯이 씁니다. 쉬운 말과 구체적인 행동으로 쓰고, AI 말투나 추상적인 전문 용어는 쓰지 않습니다(**unslop** 스킬은 로그 텍스트에도 적용됩니다).

헬퍼 `scripts/log.sh <logfile> <phase> <decision> <why> <evidence> <result>`를 사용합니다. 이 헬퍼는 `ts`를 찍고, 처음 사용할 때 헤더를 쓰고, 셀에 섞인 탭과 줄바꿈을 제거하며, `=`, `+`, `-`, `@`로 시작하는 셀 앞에 작은따옴표 하나를 붙입니다. `printf`만으로 행을 덧붙여도 되지만, 셀 내용이 생성된 텍스트나 사용자가 입력한 텍스트에서 온다면 같은 바이트들에 주의해야 합니다.

모든 행동이 아니라 결정 지점과 체크포인트를 기록합니다. 예를 들어 갈림길에서 선택한 방향, 검증 결과와 함께 완료한 작업 단위, 계기와 함께 기록한 방향 전환이나 되돌림, 드러난 차단 요인, 고친 게이트를 기록합니다. 루프 실행에서는 반복 한 번당 한 행을 씁니다. 사소하거나 자명한 것은 건너뜁니다.

실행(run)은 에이전트 대화 하나이며, 그 대화의 이후 턴과 그 대화의 요약도 포함합니다. 작업 인계, 교체된 에이전트, 새 채팅은 새 실행을 시작합니다. 실행이 이미 행이 있는 로그에 행을 추가할 때, 그 실행의 첫 행은 phase가 `start`입니다. 다른 실행의 `start` 행 다음에 오는 그 실행의 첫 행도 마찬가지입니다. 따라서 이후 턴에서 로그로 돌아온 실행은 먼저 로그의 마지막 행들을 읽고, 그사이에 다른 실행이 기록했는지 확인합니다. `start` 행에는 그 앞에 있는 행들 가운데 이 실행이 쓰지 않은 행들의 `ts` 범위를 적고, evidence에는 에이전트 id처럼 이 실행을 식별하는 값을 적습니다. phase `start`는 다른 용도로 사용하지 않습니다.

## 저장 위치

기본적으로 로그는 커밋하지 않는 작업용 산출물입니다. 작업 디렉터리의 `decisions.tsv`에 두거나, 여러 작업이 동시에 진행될 때는 `.audit/<task-slug>.tsv`에 두고, git에는 포함하지 않습니다.

리뷰어가 결과를 신뢰하는 데 기록이 필요할 만큼 작업 규모가 클 때만 커밋합니다.

## 규칙

- 추가만 합니다. 잘못된 결정은 그것을 대체하는 새 행으로 바로잡습니다. 기록을 절대 수정하거나 삭제하지 않습니다.
- 직접 만든 일회성 근거보다 커밋된 스크립트가 만든 근거를 우선합니다(**encode-lessons-in-structure** 원칙 스킬).

## 트랜스크립트와 대조해 로그 감사하기

실행이 끝나고 결과를 넘기기 전에, 로그가 사실을 기록했는지 확인합니다. 활성 워크스페이스의 `agent-transcripts/` 디렉터리 아래에 있는 이 실행의 트랜스크립트를 읽습니다(시스템 프롬프트에 경로가 나와 있습니다). `~/.cursor/projects/*/` 전체를 glob으로 훑지 않습니다. 그렇게 하면 관련 없는 비공개 채팅까지 읽게 됩니다. 이 실행의 행들을 실제로 일어난 일과 하나씩 대조합니다. 각 구간은 이 실행의 `start` 행 중 하나에서 시작하거나, 이 실행이 로그를 만들었다면 첫 행에서 시작하고, 다른 실행의 다음 `start` 행에서 끝납니다.

- 모든 행이 실제 결정이나 행동에 대응하는지 확인합니다.
- 각 행의 근거가 실제로 열리고, 행이 주장하는 내용을 보여 주는지 확인합니다.
- 작업에 영향을 주었지만 기록되지 않은 갈림길, 방향 전환, 포기한 접근 방식은 누락입니다. 이를 추가합니다.

이야기가 아니라 로그를 바로잡습니다. 감사는 지어낸 행이라도 행을 수정하거나 제거하지 않습니다. 어떤 행이 실제 결정도 실제 행동도 기록하지 않았거나, 그 주장이나 근거가 틀렸다면, 실제로 일어난 일과 실제로 열리는 포인터를 담은 행을 추가해 그 행을 대체합니다. 이 감사는 이 실행의 구간 밖에 있는 행은 확인하지 않습니다. 이 실행의 작업으로 그런 행 가운데 하나가 틀렸다는 것이 드러나면, 다른 잘못된 결정과 마찬가지로 대체합니다.

## 다른 모델의 기록 리뷰

결과를 넘기기 전에, 작업을 수행한 모델과 다른 모델 계열의 서브에이전트를 실행합니다. 자기 리뷰는 이를 대신할 수 없습니다. 서브에이전트는 감사 기록과 실행 트랜스크립트를 읽고, 사용자가 주의해야 할 점을 표시합니다. 작업을 다시 하는 것이 아니라, 최선이 아니거나 위험한 부분을 찾아 훑어보는 것입니다. 각 서브에이전트의 추론 수준과 fast 또는 standard 속도는 **poteto-mode** 스킬의 Subagents 섹션에 따라 작업에 맞게 고릅니다.

- 근거가 약하거나 없는 채로 기록된 결정
- 건너뛰었거나, 트랜스크립트에 증거 없이 수행했다고 주장한 검증 단계
- 돌이켜 보면 위험해 보이는 선택(성급한 선택, 범위를 넓히는 선택, 증상만 덮는 선택)
- 사용자가 대충 훑어볼 때 놓칠 만한 누락

기록을 만든 실행에 대한 모든 답변은 "Attention" 섹션으로 끝납니다. 첫 줄에는 리뷰어 모델을 따로 적고(`reviewed by <model>`), 이어서 각 지적 사항을 특정 행이나 시점을 가리키며 나열합니다. "No flags"도 유효한 값입니다. 모델 이름은 그렇지 않습니다.

## 기록 리뷰하기

위에서 아래로 읽고, 근거 포인터를 따라가며 표본을 골라 확인합니다. GitHub는 커밋된 TSV를 표로 렌더링합니다. 터미널에서는 `column -s$'\t' -t decisions.tsv`로 렌더링할 수 있습니다.

## 이 스킬 조합하기

다른 스킬은 감사 기록을 새로 만들지 않고 이 스킬로 보냅니다. 이름으로 이 스킬을 참조하고 형식은 이 스킬에 맡깁니다. 열을 다시 나열하지 않습니다.
