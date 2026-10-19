---
name: reflect
description: 현재 트랜스크립트를 대상으로 리뷰 서브에이전트 세 개를 병렬로 실행하고, 배운 점을 찾아낸 뒤 각각을 기존 스킬에 대한 구체적인 수정으로 연결합니다. 사용자가 reflect라고 말할 때 사용합니다.
disable-model-invocation: true
---

> 이 문서는 [SKILL.md](./SKILL.md)를 한국어로 번역한 문서입니다. 원본과 내용이 다르면 원본을 기준으로 합니다.

# Reflect

현재 대화에서 오래 유효할 배운 점을 찾아내고, 이를 스킬 수정으로 연결합니다.

사용자가 "reflect" 또는 "/reflect"라고 말할 때 실행합니다. 대화가 사소하거나, 주제와 무관하거나, 부모 에이전트가 올바르게 따른 기존 스킬이 이미 다루는 내용이면 건너뜁니다. 한 번만 일어난 일은 배운 점이 아닙니다.

## 1. 현재 트랜스크립트 찾기

병렬로 분산하기 전에, 시스템 프롬프트가 지정한 `agent-transcripts/` 디렉터리에서 이 대화의 트랜스크립트를 찾습니다. `~/.cursor/projects/*/` 전체를 glob으로 검색하지 않습니다. 그렇게 하면 워크스페이스 경계를 넘어 관련 없는 프로젝트의 비공개 대화까지 읽게 됩니다.

```bash
ls -t <agent-transcripts>/*.jsonl <agent-transcripts>/*/*.jsonl <agent-transcripts>/*/subagents/*.jsonl 2>/dev/null | head -10
```

이 명령은 레거시 평면 구조(`<id>.jsonl`), 현재 중첩 구조(`<id>/<id>.jsonl`), 서브에이전트 구조(`<parent>/subagents/<child>.jsonl`)를 모두 포함합니다. 각 후보 파일의 첫 번째 JSONL 줄을 읽습니다. `message.content[0].text`에 대화를 시작한 사용자 프롬프트가 들어 있는 파일을 선택합니다. 일치하는 파일이 없으면 대신 세션 내용을 간결하게 요약해서 전달합니다.

## 2. 리뷰어 세 명을 병렬로 실행하기

메시지 하나에 `Task` 호출 세 개를 넣고, `subagent_type: generalPurpose`, 에이전트 모드(`readonly: false`)로 실행합니다. 리뷰어는 트랜스크립트가 참조하는 티켓, 채팅 스레드, 관측성(observability) 트레이스를 찾아보기 위해 MCP 접근이 필요하며, readonly 모드에서는 MCP가 제거됩니다.

각 리뷰어와 종합자(synthesizer)는 `pstack-models.mdc` 규칙의 역할 줄 하나와 기본값 하나를 지정합니다. `model`을 그 줄의 값으로 설정하고, 규칙이나 해당 줄이 없으면 기본값으로 설정합니다. 값이 `auto` 또는 `inherit-parent`이면 `model`을 설정하지 않습니다. Task 도구가 slug를 거부하면 기본값을 사용하고 그 사실을 알립니다. 기본값까지 거부하면 오류 메시지에 나온 같은 계열의 유효한 slug 중 가장 가까운 것을 사용합니다. 각 서브에이전트의 추론 수준과 fast 또는 standard 속도는 **poteto-mode** 스킬의 Subagents 섹션에 따라 작업에 맞게 고릅니다.

| 관점 | 역할 줄 | 기본 `model` | 프롬프트 템플릿 |
|---|---|---|---|
| 판단(Judgment) | `reflect judgment, divergent, synthesizer` | `claude-opus-5-5-xhigh` | `references/judgment-reviewer.md` |
| 도구(Tooling) | `reflect tooling` | `grok-4.7-xhigh-fast` | `references/tooling-reviewer.md` |
| 발산(Divergent) | `reflect judgment, divergent, synthesizer` | `claude-opus-5-5-xhigh` | `references/divergent-reviewer.md` |

각 템플릿을 그대로 전달하되, 표시된 위치에 트랜스크립트 경로나 요약을 넣습니다. 리뷰어는 결과를 `Task` 응답 본문으로 반환합니다.

## 3. 종합하기

`Task` 호출 하나를 `subagent_type: generalPurpose`, `reflect judgment, divergent, synthesizer` 줄에서 가져온 `model`(기본값 `claude-opus-5-5-xhigh`), 에이전트 모드(`readonly: false`)로 실행합니다. 종합자는 MCP로 인용을 표본 검증하며, readonly 모드에서는 MCP가 제거됩니다. `references/synthesizer.md`를 그대로 전달하고, 표시된 위치에 각 리뷰어의 전체 출력을 넣습니다. 종합자는 Accepted / Rejected / Backlog 목록을 반환합니다.

## 4. 구조적 강제 점검

lint 규칙, 스크립트, 메타데이터 플래그, 런타임 검사로 더 확실하게 강제할 수 있는 Accepted 항목은 Backlog로 옮깁니다. **encode-lessons-in-structure** 원칙 스킬을 참고합니다.

## 5. 적용하기

종합자의 Accepted / Rejected / Backlog 출력 전체를 보여 주고, Accepted 수정을 적용하기 전에 명시적인 승인을 기다립니다. 사용자는 일부만 고를 수 있고 연결 대상을 바꿀 수도 있습니다. 스킬 변경은 조직의 이후 모든 에이전트에 영향을 줍니다. 자동으로 적용하지 않습니다.

각 Backlog 항목은 기다리지 않고 팀의 devex 또는 backlog 트래커에 등록합니다. 승인을 기다리는 것은 Accepted 목록뿐입니다.

승인된 각 행의 Routing을 정확히 따릅니다.

- 사소한 기존 스킬 수정(한 줄짜리 항목, 다듬은 문장, 오래된 사실 정정): 부모 에이전트가 직접 처리합니다.
- 실질적인 기존 스킬 수정(새 섹션, 새 패턴 표, 약 10줄 초과): Cursor에 내장된 `create-skill` 스킬에 넘기고, 그 스킬의 초안 작성 / 테스트 / 반복 루프를 실행합니다.
- `tune description: <skill path>`(스킬은 있지만 실행되어야 할 때 실행되지 않은 경우): `create-skill`에 넘기고, 그 스킬의 description 최적화 루프를 실행합니다.
- `new skill via create-skill: <kebab-name>`: 생성을 `create-skill`에 넘깁니다. 형태를 즉흥적으로 만들지 않습니다.

환경에 SKILL.md 검증 도구가 있으면, 완료를 선언하기 전에 수정한 모든 스킬에 대해 실행합니다.

## 6. 사용자에게 요약하기

서두 없이 짧은 목록으로 작성합니다.

- 적용한 수정: `<skill path>`. 무엇이 바뀌었는지 항목마다 한 줄로 씁니다.
- 새로 만든 스킬: `<skill path>`. 항목마다 한 줄로 씁니다(드문 경우).
- devex 트래커에 등록한 Backlog: `<issue title>` (`<tags>`). 항목마다 한 줄로 씁니다.
- 제외한 항목: 거부된 결과마다 한 줄과 종합자가 제시한 이유를 씁니다.
