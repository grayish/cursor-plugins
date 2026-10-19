> 이 문서는 [README.md](./README.md)를 한국어로 번역한 문서입니다. 원본과 내용이 다르면 원본을 기준으로 합니다.

# pstack

저는 [poteto](https://x.com/poteto)입니다. 대통령이나 CEO는 아니지만, Meta, Netflix, Cursor에서 수백만 줄의 코드를 다뤄 왔습니다. 또한 react core 팀에서 react compiler를 만들고 유지보수하는 일을 돕고 있습니다.

AI가 슬롭(slop) 코드를 너무 많이 쓴다는 인식이 커지고 있습니다. 저도 동의합니다. 저는 슬롭 아티스트 스무 명으로 이루어진 팀처럼 출시하고 싶지 않습니다. 품질 없는 처리량은 제가 추구하는 목표가 아닙니다. 빠르게 가고 싶다면 먼저 깊게 들어가야 합니다.

**pstack은 이에 대한 제 답입니다.** 이 스킬들은 제가 Cursor에서 고품질 코드를 출시하기 위해 매일 사용하는 바로 그 스킬입니다. pstack은 cursor를 진짜 엔지니어링 팀으로 바꿔 줍니다. 목표는 코드 줄 수(loc)를 최대화하는 것이 아니며, 사실 그 반대입니다. pstack은 코드를 더 적게, 하지만 더 높은 품질로 쓰도록 도와줍니다.

**pstack은 두려움 없는 병렬 작업을 가능하게 합니다.** 에이전트 하나에 깊게 집중하고, 그 에이전트가 좋고 검증 가능한 코드를 쓸 것이라고 신뢰할 수 있으면, 확신을 가지고 진정한 병렬 작업을 할 수 있습니다. `poteto-mode`로 여러 에이전트를 시작하고, 각 에이전트가 엄격한 엔지니어링 원칙을 작업에 적용할 것이라고 신뢰하세요.

**cursor는 모든 장점을 한데 모아 줍니다.** 모든 프런티어 모델에는 각자의 강점과 약점이 있습니다. pstack은 어떤 모델과도 함께 사용할 수 있습니다. 실제로 제 스킬 중 다수는 각 모델의 고유한 강점을 활용하기 위해 다중 모델 워크플로를 사용합니다.

포크하세요. 개선하세요. 자기 것으로 만드세요. PR은 언제나 환영합니다!

## 설치

```bash
/add-plugin pstack
```

claude code에서는 다음과 같이 설치합니다.

```bash
/plugin marketplace add cursor/plugins
/plugin install pstack@cursor-plugins
```

claude code는 플러그인 명령과 에이전트 앞에 접두사를 붙이므로, `/poteto-mode`는 `/pstack:poteto-mode`가 됩니다. [claude code 어댑터](./docs/harness/claude-code.md)가 그 밖의 모든 cursor 이름을 대응시킵니다. claude code에서 `/pstack:setup-pstack`은 모델 선택을 `~/.claude/rules/pstack-models.md`에 기록하며, bugbot이나 automations처럼 cursor 전용 기능이 필요한 단계는 한 줄짜리 안내와 함께 건너뜁니다.

claude code는 저장소 루트의 [`harness/pstack/`](https://github.com/cursor/plugins/tree/main/harness/pstack)에서 이 readme와 가이드를 포함해 생성된 pstack 사본을 설치합니다. pstack을 변경한 뒤에는 저장소 루트에서 `node scripts/generate-pstack-harness-trees.mjs`를 실행하고 그 결과를 커밋하세요. 생성된 사본이 최신 상태가 아니면 ci가 실패합니다.

## 시작하기

두 단계면 됩니다.

1. [`/setup-pstack`](./skills/setup-pstack/SKILL.md)을 실행하고, 추론 예산(reasoning budget)을 고른 뒤, 사용할 모델을 선택합니다.
2. 엄격함이 필요한 작업을 할 때마다 [`/poteto-mode`](./skills/poteto-mode/SKILL.md)를 사용합니다.

처음이신가요? [pstack 가이드](./docs/guide/README.md)는 설정과 프롬프트 작성부터 검증과 야간 작업까지, 첫 번째 실제 작업을 단계별로 안내합니다. 막혔거나 어떤 스킬이 맞는지 확실하지 않다면 [`/poteto-help`](./skills/poteto-help/SKILL.md)에 물어보세요.

이것으로 끝입니다. 나머지 스킬은 상황에 따라 쓰이며, 모드 스킬이 필요할 때 대신 사용합니다. 기본 설정에서 모드는 모델의 강점에 따라 작업을 나눕니다. 코드 위임 작업(feature, refactoring, bug fix, perf, hillclimb)은 grok이 맡고, 가장 어려운 변경, 산문, 판단은 opus 5.5가 맡습니다. 기본 패널은 opus 5.5 / grok입니다. [`/setup-pstack`](./skills/setup-pstack/SKILL.md)으로 이 중 무엇이든 바꿀 수 있습니다.

## 사용법

작업을 시작할 때 [`/poteto-mode`](./skills/poteto-mode/SKILL.md)를 사용하세요. 이 스킬은 요청을 읽고, 플레이북 세트 중에서 하나를 고르며, 단계에서 필요할 때 다른 스킬을 실행합니다.

작업 내용을 입력하기 전에 `/` 메뉴에서 `/poteto-mode`를 고르세요. 그래야 cursor가 메시지에 스킬을 첨부합니다.

### 그냥 [`/poteto-mode`](./skills/poteto-mode/SKILL.md)를 사용하세요

이 스킬은 주요 지름길입니다. 저는 에이전트가 엄격한 엔지니어링 작업을 해야 할 때마다 이 스킬을 사용합니다. 스물세 개의 플레이북이 함께 제공됩니다.

```
/poteto-mode 이 pr에는 유휴 상태에서도 750ms마다 스크롤이 어긋나는 미묘한 버그가 있어. 먼저
재현하고, 그다음 고치고 검증해.
```

```
/poteto-mode 나 이제 잘 거야. ci가 불안정(flaky)하더라도 스택을 머지해. 아침까지 전부 머지되어
있으면 좋겠어.
```

<details>
<summary>스물세 개의 플레이북</summary>

| 플레이북 | 용도 |
|---|---|
| [investigation](./skills/poteto-mode/playbooks/investigation.md) | 읽기 전용 질문입니다. x는 어떻게 동작하는지, y는 왜 이렇게 만들어졌는지, 정말 확실한지 등을 다룹니다. |
| [bug fix](./skills/poteto-mode/playbooks/bug-fix.md) | 결함을 재현하고, 근본 원인을 찾고, 런타임 증거를 바탕으로 수정합니다. |
| [perf](./skills/poteto-mode/playbooks/perf-issue.md) | 측정된 느림을 트레이스하고, 기준선(baseline)과 비교해 개선합니다. |
| [hillclimb](./skills/poteto-mode/playbooks/hillclimb.md) | 하나의 지표를 목표치에 맞춰 지속적이고 과학적으로 개선합니다. 전후 측정으로 가설을 반복 검증하고, 채택된 개선마다 커밋 하나를 남깁니다. |
| [runtime forensics](./skills/poteto-mode/playbooks/runtime-forensics.md) | 계측(instrumentation)을 통해 실행 중인 증상(누수, 유휴 상태의 CPU 과점유, 글리치)을 진단합니다. |
| [trace forensics](./skills/poteto-mode/playbooks/trace-forensics.md) | 캡처된 프로파일링 결과물(cpuprofile, trace, spindump, heap snapshot)을 진단합니다. |
| [feature](./skills/poteto-mode/playbooks/feature.md) | 이름이 붙은 데이터 형태에서 출발해 새로운 동작이나 변경된 동작을 만듭니다. |
| [refactoring](./skills/poteto-mode/playbooks/refactoring.md) | 동작은 유지하면서 구조나 형태를 바꿉니다. |
| [prototype](./skills/poteto-mode/playbooks/prototype.md) | 설계나 동작에 대한 결정을 적은 비용으로 내리거나, 실험적인 갈림길을 직접 관찰해 결론짓기 위한 일회용 스케치를 만듭니다. |
| [visual parity](./skills/poteto-mode/playbooks/visual-parity.md) | 두 구현 사이에서 픽셀 단위로 정확히 같은 ui를 맞춥니다. |
| [authoring a skill](./skills/poteto-mode/playbooks/authoring-a-skill.md) | SKILL.md를 작성하거나 편집합니다. |
| [eval](./skills/poteto-mode/playbooks/eval.md) | 스킬이나 프롬프트 변경이 에이전트 동작에 미치는 영향을 블라인드 방식으로 테스트합니다. |
| [babysit](./skills/poteto-mode/playbooks/babysit.md) | pr이나 스택을 머지 가능한 상태로 이끕니다. 충돌, 리뷰 스레드, ci를 처리합니다. |
| [shipping](./skills/poteto-mode/playbooks/shipping.md) | 통과한(green) 스택을 독립적으로 검증한 뒤, 검증된 연속 구간을 아래에서부터 위로 머지합니다. 기본적으로 github를 거치며, 가능하면 origin을 사용합니다. |
| [autonomous run](./skills/poteto-mode/playbooks/autonomous-run.md) | 긴 작업을 멈추지 않고 완료까지 진행합니다. |
| [orchestrate](./skills/poteto-mode/playbooks/orchestrate.md) | 하나의 코디네이터 채팅에 맡겨진 지속적인 프로젝트를 다룹니다. 여러 날에 걸치며, 쌓인 pr이 많고, 서브에이전트 무리를 운용합니다. |
| [autopilot-full](./skills/poteto-mode/playbooks/autopilot-full.md) | 독립적인 pr들을 pr마다 담당자 한 명을 두고 머지까지 진행합니다. 스웜이 코드 준비가 끝난 각 헤드를 검증하고, 이후 패치가 바뀔 때마다 새로 검사합니다. |
| [autopilot-stack](./skills/poteto-mode/playbooks/autopilot-stack.md) | 운영자가 리뷰하고 머지할 수 있도록 베이스 브랜치 위에 선형 스택 하나를 만들고 검증합니다. |
| [session pickup](./skills/poteto-mode/playbooks/session-pickup.md) | 이전 에이전트가 진행 중이던 작업을 재개하거나 이어받습니다. |
| [pause safely](./skills/poteto-mode/playbooks/pause-safely.md) | 나중에 재개할 수 있도록 진행 중인 작업을 깔끔하게 중단합니다. |
| [multi-phase plan](./skills/poteto-mode/playbooks/multi-phase-plan.md) | 여러 단계나 쌓인 PR에 걸친 작업을 다룹니다. |
| [worktree cleanup](./skills/poteto-mode/playbooks/worktree-cleanup.md) | 머지되었거나 버려진 워크트리와 오래된 ios 시뮬레이터를 정리해 디스크 공간을 확보합니다. 안전 장치를 거쳐 진행합니다. |
| [opening a pr](./skills/poteto-mode/playbooks/opening-a-pr.md) | 작고 순서가 정해진 커밋들로 준비된 pr을 엽니다. 제목은 conventional commits 형식으로, 본문은 브리핑 형식으로 작성합니다. 다른 모든 플레이북의 마지막에 호출됩니다. |

</details>



호출되면 다음을 수행합니다.

1. 작업을 [플레이북](./skills/poteto-mode/playbooks/)에 대응시키고, 그 플레이북의 단계를 그대로 복사해 첫 항목으로 넣은 할 일 목록을 엽니다.
2. 단계가 실행될 때마다 다른 스킬로 연결합니다.
3. 사용하는 사람과 유지보수하는 사람의 관점에 맞춘, 슬롭이 제거된 답변을 작성합니다.

전체 규칙과 플레이북은 [`skills/poteto-mode/SKILL.md`](./skills/poteto-mode/SKILL.md)에 있습니다.

[`/poteto-mode`](./skills/poteto-mode/SKILL.md)를 여러 턴에 걸쳐 켜 두려면, `/` 메뉴에서 고른 뒤 enter 대신 option+enter(mac) 또는 alt+enter(windows)를 누르세요. 그러면 [커스텀 모드](https://cursor.com/docs/skills#using-a-skill-as-a-custom-mode)가 되며, cursor는 이 기능을 agents 창과 cli에서 제공합니다. 커스텀 모드는 모드를 종료할 때까지 매 턴 컨텍스트에 남아 있습니다. 그냥 enter를 누르면 메시지 하나에만 첨부됩니다.

[`/poteto-mode`](./skills/poteto-mode/SKILL.md)는 cursor의 `/loop` 명령과 매우 잘 어울립니다. 엄격함을 희생하지 않고도 cursor가 여러 시간 동안 작업하게 만들 수 있습니다.

## 스킬

단계에서 필요할 때 [`/poteto-mode`](./skills/poteto-mode/SKILL.md)가 이 스킬들 대부분을 대신 실행합니다(`how`, `why`, `architect`, `arena`, `swarm`, `interrogate`, `unslop`, `no-comments`, `technical-writing`, `tdd`, 그리고 원칙들). 아래 표는 스킬 하나를 직접 쓰고 싶을 때를 위한 것입니다.

```
/how 실행(run)은 어떻게 취소하지? 취소할 실행을 하나하나 조회할 때 n+1 문제가 있나?
```

```
/interrogate 이 pr을 리뷰해 줘.
```

<details>
<summary>모든 스킬</summary>

| 스킬 | 사용 시점 |
|---|---|
| [`/poteto-mode`](./skills/poteto-mode/SKILL.md) | 사소하지 않은 모든 작업의 기본 진입점입니다. |
| [`/poteto-help`](./skills/poteto-help/SKILL.md) | pstack이 처음이거나, 어떤 스킬, 플레이북, 원칙이 맞는지 확실하지 않을 때 사용합니다. 무엇을 하려는지 파악하고, 그 부분에 답한 뒤, 입력할 프롬프트를 건네줍니다. `/poteto-help`를 입력할 때만 실행됩니다. |
| [`/in-your-own-words`](./skills/in-your-own-words/SKILL.md) | 에이전트가 시작하기 전에 목표와 문제를 다시 설명하게 하고 싶을 때 사용합니다. `/in-your-own-words`를 입력할 때만 실행됩니다. |
| [`/sharpen`](./skills/sharpen/SKILL.md) | 과도하게 명세하거나 선택을 대신 내리지 않으면서, 정말로 원하는 것을 겨냥해 최고 전문가가 접근하는 방식으로 작업에 접근하게 하고 싶을 때 사용합니다. |
| [`/how`](./skills/how/SKILL.md) | 서브시스템이 어떻게 동작하는지 단계별 설명을 원할 때 사용합니다. |
| [`/why`](./skills/why/SKILL.md) | 무언가가 왜 이렇게 만들어졌는지 알고 싶을 때 사용합니다. 실행 시점에 사용 가능한 MCP를 찾아내고, 증거 범주(소스 컨트롤, 이슈 트래커, 장문 문서, 실시간 채팅, 인프라 관측성, 오류 추적, 분석 웨어하우스)마다 병렬로 조회합니다. |
| [`/recall`](./skills/recall/SKILL.md) | 작업을 시작하거나 재개하면서, 자신의 채팅 기록과 공유된 기록을 바탕으로 한 주제의 최근 맥락을 다시 구성해 간결한 현재 상태 브리핑으로 받고 싶을 때 사용합니다. |
| [`/ketchup`](./skills/ketchup/SKILL.md) | 마지막 메시지 이후 이 채팅에서 무엇이 바뀌었는지 쉬운 말과 작은 시각 자료로 알고 싶을 때 사용합니다. 답변이 길어지더라도, 사용자의 결정을 기다리는 모든 항목을 나열하고 답변만 보고도 결정할 수 있게 합니다. |
| [`/blast-radius`](./skills/blast-radius/SKILL.md) | 작아 보이는 변경이 있는데 그 변경이 다른 무엇을 깨뜨릴 수 있는지 알고 싶을 때 사용합니다. 변경이 안전하다는 근거가 되는 단 하나의 사실을 주장만 하지 않고 코드를 실행해 증명합니다. |
| [`/architect`](./skills/architect/SKILL.md) | 함수 경계를 넘는 코드를 쓰기 직전에, 호출자의 사용 방식, 타입, 모듈 형태를 먼저 확정하고 싶을 때 사용합니다. |
| [`/arena`](./skills/arena/SKILL.md) | 같은 작업을 N번 병렬로 시도한 뒤, 각 시도에서 가장 좋은 부분을 가져오고 싶을 때 사용합니다. |
| [`/swarm`](./skills/swarm/SKILL.md) | 서로 다른 부분이나 경쟁 시도에 N개의 작업자를 병렬로 배치한 뒤, 하나로 취합된 보고서를 받고 싶을 때 사용합니다. |
| [`/interrogate`](./skills/interrogate/SKILL.md) | diff가 있고, 엄격한 코드 품질 관점을 포함해 서로 다른 모델들이 그 diff를 깨뜨려 보게 하고 싶을 때 사용합니다. |
| [`/automate-me`](./skills/automate-me/SKILL.md) | 자신이 실제로 일해 온 방식을 바탕으로 초안을 만든 자기만의 `-mode` 스킬을 원할 때 사용합니다. |
| [`/make-bot-ui`](./skills/make-bot-ui/SKILL.md) | 버튼을 누르면 webhook을 통해 Grok Bot을 깨우는 페이지나 대시보드를 원할 때 사용합니다. sender-key 전달과 Tailscale 설정을 포함합니다. |
| [`/setup-pstack`](./skills/setup-pstack/SKILL.md) | pstack이 역할마다 어떤 모델을 쓸지 고르고 싶을 때 사용합니다. 사용 가능한 모델을 감지하고 설정 규칙을 작성합니다. |
| [`/reflect`](./skills/reflect/SKILL.md) | 긴 작업이 완료되었고, 그 방법을 스킬 수정으로 남기고 싶을 때 사용합니다. |
| [`/correct`](./skills/correct/SKILL.md) | 같은 실수 때문에 에이전트를 계속 바로잡고 있을 때 사용합니다. 기록에서 실수 유형을 찾아내고, 각각을 효과가 있는 가장 높은 수준(아키텍처, 그다음 타입, lint, ci, 그다음 테스트, 마지막으로 문서)에서 고치며, 각 규칙과 그 규칙을 강제하는 수단을 짝지은 표를 유지합니다. |
| [`/teach`](./skills/teach/SKILL.md) | 변경이나 서브시스템을 요약만 받는 것이 아니라 실제로 이해하고 싶을 때 사용합니다. how와 why를 실행하고, 다이어그램을 하나씩 쌓아 가며 쉬운 설명 하나로 엮어 냅니다. |
| [`/tdd`](./skills/tdd/SKILL.md) | 버그를 고치는 중이고 비용이 적게 드는 로컬 테스트 경로가 있을 때 사용합니다. 실패하는 테스트를 먼저 작성한 뒤 수정합니다. |
| [`/benchmark-checklist`](./skills/benchmark-checklist/SKILL.md) | 벤치마크를 실행했거나 속도 향상 또는 회귀를 측정했을 때 사용합니다. 보고하거나 그에 따라 행동하기 전에 수치를 점검합니다(제한 요인, 튜닝, 오류, 반복 실행, 종단 간 관련성). |
| [`/no-comments`](./skills/no-comments/SKILL.md) | 리뷰 전에 주석을 제거합니다. Comment Sicko를 띄우고, 받아들인 지적 사항을 고치며, 주장된 제약 조건을 코드로 표현할 방법을 제안합니다. |
| [`/typescript-best-practices`](./skills/typescript-best-practices/SKILL.md) | typescript를 읽거나 편집할 때 사용합니다. type-system-discipline 원칙을 구체적인 문법으로 뒷받침합니다. |
| [`/figure-it-out`](./skills/figure-it-out/SKILL.md) | 번들된 플레이북 중 맞는 것이 없을 때 사용합니다. 작업에 맞는 엄격하고 감사 가능한 플레이북을 설계합니다. |
| [`/show-me-your-work`](./skills/show-me-your-work/SKILL.md) | 리뷰 가능한 결정 기록을 원할 때 사용합니다. 결정 사항을 커밋할 수 있는 tsv에 기록합니다. |
| [`/create-verification-skill`](./skills/create-verification-skill/SKILL.md) | 프로젝트에 앱 동작을 증명할 스크립트 방식이 없을 때 사용합니다. 언어나 플랫폼에 관계없이 기능 맵을 갖춘 프로젝트 로컬 verify 스킬을 생성합니다. |
| [`/maintain-verification-skill`](./skills/maintain-verification-skill/SKILL.md) | verify 스킬의 기능 맵이 앱과 어긋났을 때 사용합니다. 소스를 한 차례 훑고 실제 실행을 한 번 거쳐, 증명된 수정 사항을 최대 PR 하나로 만듭니다. |
| [`/create-formal-verification-skill`](./skills/create-formal-verification-skill/SKILL.md) | 경쟁 상태, 재시도, 상태 머신, 파서, unsafe 코드를 깨뜨리는 경우에 테스트가 닿지 못할 때 사용합니다. 적합한 도구로 속성 하나를 검증하고, 변경이 있을 때마다 그 속성을 다시 검사하는 프로젝트 로컬 스킬을 생성합니다. |
| [`/maintain-formal-verification-skill`](./skills/maintain-formal-verification-skill/SKILL.md) | 형식 검증이 다루는 코드가 마지막 검사 이후 바뀌었을 때 사용합니다. 모델, 하네스, 속성을 코드에 맞게 갱신하고, 다시 실행하며, 각 실패가 검증 쪽의 어긋남인지 코드의 버그인지 알려 줍니다. |
| [`/unslop`](./skills/unslop/SKILL.md) | 글을 다듬을 때 사용합니다. AI가 쓴 티가 나는 표현을 제거합니다. |
| [`/bro`](./skills/bro/SKILL.md) | 마지막 메시지를 전문 용어 없이 사람이 쓰는 쉬운 말로 다시 듣고 싶을 때 사용합니다. |
| [`/technical-writing`](./skills/technical-writing/SKILL.md) | 문서, RFC, readme, PR 설명, 커밋 메시지를 위한 계층형 문서 표준(Diátaxis + Google developer style + STE + Global English)입니다. |

</details>



### 예시

저는 대부분 작업을 시작할 때 [`/poteto-mode`](./skills/poteto-mode/SKILL.md)를 입력하고 플레이북으로 연결되게 둡니다. 다른 스킬은 단계에서 필요할 때 실행됩니다. 몇 가지는 직접 꺼내 씁니다.


<details>
<summary>모든 예시</summary>

```
bug fix:           /poteto-mode 이 pr에는 유휴 상태에서도 750ms마다 스크롤이 어긋나는 미묘한
                   버그가 있어. 먼저 재현하고, 그다음 고치고 검증해.
perf:              /poteto-mode 가상화를 했는데도 큰 목록이 로드되는 데 1~2초가 걸려.
                   cpu 트레이스를 실행하고 이유를 알려 줘.
feature:           /poteto-mode feature flag 뒤에 작은 기능을 만들어. 정말 동작하는지 검증해.
prototype:         /poteto-mode 비교할 수 있게 markdown 렌더러 프로토타입을 두 개 만들어.
                   각각 에이전트를 하나씩 띄워.
multi-phase:       /poteto-mode 이 스킬들을 플러그인으로 오픈 소스화해. 내부 정보가 새면 안 되고,
                   임시 디렉터리에서 작업하고, 의존성 그래프를 먼저 보여 줘.
overnight run:     /poteto-mode 나 이제 잘 거야. ci가 불안정(flaky)하더라도 스택을 머지해.
                   아침까지 전부 머지되어 있으면 좋겠어.
babysit:           /poteto-mode pr 123 확인해 줘. 남은 거 있어?
visual parity:     /poteto-mode 이 flag가 켜져 있으면 행 간격이 너무 높아. 두 번째 이미지가
                   맞는 거야. 재현하고 일치할 때까지 고쳐.
figure it out:     /poteto-mode 나 잠깐 자리 비울게. 동기 store를 쓰는 모든 호출자를 새 비동기
                   store로 옮기되, 동작은 완전히 똑같이 유지해. 돌아왔을 때 제대로 됐다고
                   믿을 수 있으면 좋겠어.
how:               /how 실행(run)은 어떻게 취소하지? 취소할 실행을 하나하나 조회할 때 n+1 문제가 있나?
why:               /why 이 feature flag는 왜 아직 안 켜져 있어?
architect:         이 계측을 오탐 없이 신호가 높도록 설계해. 먼저 /architect로
                   설계해.
arena:             /arena 내 프롬프트를 그대로 arena에 가져가. 그쪽 제안들을 네 제안과
                   비교하고 싶어.
swarm:             /swarm packages/ 아래의 모든 패키지를 각자의 check.sh로 검사해. 패키지마다
                   작업자 하나. 보고서는 하나로.
interrogate:       /interrogate 이 pr을 리뷰해 줘.
tdd:               /tdd 구현해
unslop:            새 변경 사항에서 슬롭을 걷어내고 더 간결하게 다듬을 수 있을까?
reflect:           /reflect 너무 오래 걸렸어. 다음 실행에서 반복하지 않도록 배운 것을
                   기록해 둬.
correct:           /correct
show-me-your-work: /show-me-your-work 돌아왔을 때 리뷰할 수 있게 결정 기록을 남겨 줘.
automate-me:       /automate-me
help:              /poteto-help 이 브랜치를 리뷰하려면 어떤 스킬을 써야 해?
```

</details>

## `poteto-agent`와 Comment Sicko 서브에이전트

[`/poteto-mode`](./skills/poteto-mode/SKILL.md)는 이를 받은 채팅에서 실행됩니다. 그 채팅이 플레이북, 할 일 목록, 모든 서브에이전트 생성을 담당합니다.

pstack은 실행 중에 생성되는 위임 에이전트를 위한 래퍼로 [`subagent_type: "poteto-agent"`](./agents/poteto-agent.md)도 함께 제공합니다. 이 에이전트는 어떤 작업을 하기 전에 인라인 원칙 색인을 포함해 `poteto-mode`를 전부 읽습니다. 대신 `generalPurpose`를 쓰면 그 읽기 과정을 건너뛰게 되어 방향이 어긋납니다. 플레이북 자체를 실행하기 위해 `poteto-agent`를 생성하지 마세요.

[`/poteto-mode`](./skills/poteto-mode/SKILL.md)와 [`subagent_type: "poteto-agent"`](./agents/poteto-agent.md)는 같은 래퍼를 거칩니다.

pstack은 또한 `subagent_type: "comment-sicko"`로 사용할 수 있는 읽기 전용 주석 리뷰어인 [Comment Sicko](./agents/comment-sicko.md)도 제공합니다. 보통은 직접 호출하지 않고 [`/no-comments`](./skills/no-comments/SKILL.md)를 통해 호출합니다.

## 원칙

짧은 스킬 스물네 개가 있으며, 각 스킬은 원칙 하나씩을 담고 있습니다. `poteto-mode`는 이 원칙들을 인라인으로 색인하고 작업 시작 시 그 색인을 읽습니다. 별도 파일이 있는 이유는 다른 스킬이 원칙을 이름으로 참조할 수 있게 하고, 색인이 각 원칙의 전체 규칙을 가리킬 수 있게 하기 위해서입니다.

<details>
<summary>스물네 개의 원칙 전체</summary>

| 원칙 | 그룹 | 규칙 |
|---|---|---|
| [laziness-protocol](./skills/principle-laziness-protocol/SKILL.md) | core | 삭제와, 문제를 해결하는 가장 작은 변경을 우선합니다. |
| [foundational-thinking](./skills/principle-foundational-thinking/SKILL.md) | core | 로직을 쓰기 전에 적용합니다. 핵심 타입과 데이터 구조를 고르고, 뼈대 작업과 기능 작업의 순서를 정하고, 동시에 동작하는 주체들이 무엇을 공유하는지 묻습니다. 데이터 구조를 제대로 잡으면 그 뒤의 코드는 자명해집니다. |
| [redesign-from-first-principles](./skills/principle-redesign-from-first-principles/SKILL.md) | core | 요구 사항을 덧붙이는 대신, 그 요구 사항이 처음부터 기본 전제였던 것처럼 다시 설계합니다. |
| [attack-the-premise](./skills/principle-attack-the-premise/SKILL.md) | core | 하나의 전제를 공유하는 수정 두 개 이상이 같은 관문에서 실패했을 때 적용합니다. 그 전제를 가정하는 수정을 또 작성하기 전에, 전제를 적어 두고 직접 테스트합니다. |
| [subtract-before-you-add](./skills/principle-subtract-before-you-add/SKILL.md) | core | 불필요한 부분, 중복된 검증기, 스텁 참조를 먼저 제거한 뒤, 더 단순해진 기반 위에 만듭니다. |
| [minimize-reader-load](./skills/principle-minimize-reader-load/SKILL.md) | core | 질문과 답 사이의 계층 수와, 읽는 사람이 머릿속에 담아 두어야 하는 숨은 상태를 셉니다. 호출자가 하나뿐인 래퍼는 없애고 변경 가능한 범위는 줄입니다. |
| [outcome-oriented-execution](./skills/principle-outcome-oriented-execution/SKILL.md) | core | 단계 경계가 명시된 계획된 재작성과 마이그레이션 중에 적용합니다. 목표 아키텍처로 수렴하며, 일회용 호환성 코드로 매끄러운 중간 상태를 유지하지 않습니다. |
| [experience-first](./skills/principle-experience-first/SKILL.md) | core | 구현의 편의보다 사용자의 즐거움을 택합니다. 다듬어지지 않은 기능을 많이 내놓기보다 다듬어진 기능을 적게 내놓습니다. |
| [exhaust-the-design-space](./skills/principle-exhaust-the-design-space/SKILL.md) | core | 확정하기 전에 경쟁하는 프로토타입을 2~3개 만들어 나란히 비교합니다. |
| [build-the-lever](./skills/principle-build-the-lever/SKILL.md) | core | 대량 작업뿐 아니라 사소하지 않은 모든 작업(편집, 마이그레이션, 분석, 검사)에 적용합니다. 손으로 작업하는 대신, 작업을 수행하거나 증명하는 도구(codemod, 스크립트, 생성기, 또는 서브에이전트가 따르는 스킬)를 만듭니다. 그 도구는 리뷰어가 다시 실행해 볼 수 있는 결과물입니다. |
| [model-the-domain](./skills/principle-model-the-domain/SKILL.md) | architecture | 흩어진 조건문 대신 구조 안에 도메인을 표현합니다. |
| [boundary-discipline](./skills/principle-boundary-discipline/SKILL.md) | architecture | 방어 코드는 시스템 경계(CLI, 설정, 네트워크, 외부 API)에 모읍니다. 내부 타입은 신뢰하고 비즈니스 로직은 순수 함수에 둡니다. |
| [type-system-discipline](./skills/principle-type-system-discipline/SKILL.md) | architecture | 잘못된 상태를 표현할 수 없게 만들고, 의미가 있는 원시 타입에 브랜드를 붙이고, 외부 데이터는 경계에서 파싱하고, 모든 변형을 빠짐없이 처리하고, 권위 있는 스키마에서 타입을 도출합니다. |
| [make-operations-idempotent](./skills/principle-make-operations-idempotent/SKILL.md) | architecture | 이전에 일부만 실행되었더라도 같은 최종 상태로 수렴하게 합니다. |
| [migrate-callers-then-delete-legacy-apis](./skills/principle-migrate-callers-then-delete-legacy-apis/SKILL.md) | architecture | 호환성 계층을 유지하는 대신, 호출자를 마이그레이션하고 같은 작업 묶음에서 이전 API를 삭제합니다. |
| [separate-before-serializing-shared-state](./skills/principle-separate-before-serializing-shared-state/SKILL.md) | architecture | 먼저 공유 자체를 없앱니다. 공유된 쓰기 주체가 하나라는 것이 실제 불변 조건일 때만 구조적으로 직렬화합니다. |
| [prove-it-works](./skills/principle-prove-it-works/SKILL.md) | verification | 작업을 마친 뒤, 완료를 선언하기 전에 적용합니다. 대리 지표, 자체 보고, '컴파일된다'가 아니라 실제 결과물(기능을 실행하고, 실제 값을 읽고, diff를 살펴봄)에 대해 검증합니다. |
| [fix-root-causes](./skills/principle-fix-root-causes/SKILL.md) | verification | 각 증상을 시스템이 처음 잘못된 지점까지 거슬러 올라가 그곳에서 고칩니다. 방어 코드는 그것이 처리하는 상태가 정당한 경우에만 수정으로 인정됩니다. |
| [sequence-verifiable-units](./skills/principle-sequence-verifiable-units/SKILL.md) | verification | 여러 단계로 이루어진 작업(일괄 정리, 마이그레이션, 비슷한 편집의 연속)과 커밋 및 PR을 쌓는 방식에 적용합니다. 작업을 각각 검증 가능한 상태로 끝나는 작은 단위로 나누고, 다음 단위로 넘어가기 전에 각 단위를 확인하고, 그 순서 자체가 리뷰어에게 스스로를 증명하도록 전달 순서를 정합니다. |
| [test-behavior-not-implementation](./skills/principle-test-behavior-not-implementation/SKILL.md) | verification | 테스트를 작성하거나, 변경하거나, 유지할 때 적용합니다. 코드 사용자가 관찰할 수 있는 것을 단언해서, 그 동작이 깨지면 테스트가 실패하고 내부만 바뀌면 계속 통과하게 합니다. |
| [explain-the-number](./skills/principle-explain-the-number/SKILL.md) | verification | 측정한 수치(속도 향상, 회귀, 처리량, 지연 시간, 평가(eval) 결과)를 신뢰하거나, 보고하거나, 그에 따라 행동하기 전에 적용합니다. 무엇이 그 수치를 제한하는지 찾고, 그 수치가 생각하는 작업과 다른 무언가를 측정한 것이 아님을 확인합니다. |
| [guard-the-context-window](./skills/principle-guard-the-context-window/SKILL.md) | delegation | 대량 작업은 서브에이전트로 보냅니다. 메인 스레드에는 원본 데이터가 아니라 요약을 둡니다. |
| [never-block-on-the-human](./skills/principle-never-block-on-the-human/SKILL.md) | delegation | 진행하고, 결과를 제시하고, 사람(사용자)이 나중에 방향을 바로잡게 합니다. 확인 요청은 되돌릴 수 없는 작업에만 사용합니다. |
| [encode-lessons-in-structure](./skills/principle-encode-lessons-in-structure/SKILL.md) | meta | 규칙을 글로 더 쓰는 대신 lint, 메타데이터 플래그, 런타임 검사, 스크립트로 표현합니다. |

</details>

## 여기에 포함되지 않은 것

`poteto-mode`가 참조하지만 번들하지 않는 것이 몇 가지 있습니다.

- `/deslop`과 `deslop` 스킬은 `cursor-team-kit` 플러그인에 포함되어 있습니다.
- `control-cli`(CLI와 TUI용)와 `control-ui`(브라우저, Electron, 웹용)도 `cursor-team-kit`에 포함되어 있습니다.
- `/create-skill`은 cursor 내장 기능입니다. cursor는 pr을 지켜보는 내장 `/autopilot`도 제공합니다. `poteto-mode` 안에서는 pr 상태 요청이 대신 [babysit 플레이북](./skills/poteto-mode/playbooks/babysit.md)으로 갑니다.

전체 구성을 원한다면 pstack과 함께 `cursor-team-kit`을 설치하세요.

## 왜 계획 스킬이 없나요?

cursor에는 이미 pstack과 잘 어울리는 훌륭한 plan 모드가 있습니다. 하지만 개인적으로 저는 계획을 믿지 않습니다. 최고의 명세는 코드입니다. 계획을 세우고 싶다면 [`/poteto-mode`](./skills/poteto-mode/SKILL.md)가 그것도 다루지만, 기본 동작은 아닙니다.

## 자기 것으로 만들기

`poteto-mode`는 제 스타일입니다. 여러분이 원하는 것과 정확히 같지 않을 수 있습니다.

[`/automate-me`](./skills/automate-me/SKILL.md)를 입력하세요. 최근 대화 기록을 분석해, 실제로 일해 온 방식을 바탕으로 `<your-name>-mode` 스킬 초안을 만들고, 그 아래에서 pstack을 거치도록 연결합니다. pstack은 기반으로 그대로 두고, `poteto-mode`와 나란히 자기만의 라우팅 스킬을 갖게 됩니다.

모델도 설정할 수 있습니다. [`/setup-pstack`](./skills/setup-pstack/SKILL.md)을 입력하세요. 접근 가능한 모델을 감지하고, 각 역할(코드, 판단, 리뷰 패널)을 모델에 대응시키는 작은 상시 적용 규칙을 작성합니다. 모든 스킬이 이 규칙을 읽으며, 규칙이 없으면 합리적인 기본값으로 대체하므로, 원하는 부분만 덮어쓰면 됩니다.

기본값이 바뀌어도, 변경 전에 작성된 규칙은 여전히 이전 기본값을 고정합니다. 해당 역할 줄을 삭제하거나 파일을 삭제한 뒤 `/setup-pstack`을 다시 실행하세요. 다시 실행하면 모델이 기본값과 다른 역할은 그대로 유지됩니다.

## 자동화

pstack은 비활성 상태의 [benny 자동화 팩](./automations/benny/)도 제공합니다. benny는 slack 이슈 보고를 분류한 뒤, 확인된 버그를 실제 ui 증거와 함께 재현하고 수정합니다. 이 팩의 파일은 슬래시 스킬로 등록되지 않습니다.

설정하려면 cursor가 [`FOR_AGENTS.md`](./automations/benny/FOR_AGENTS.md)를 보게 하세요. 설정 과정에서 팩을 대상 저장소의 `.cursor/automations/benny/`에 복사하고, 공유 스킬을 위해 그 저장소에서 pstack을 활성화하며, 사용자 설정은 복사된 팩 바깥에 둡니다. benny는 cursor automations 위에서 실행되므로 cursor 전용입니다.

## 라이선스

MIT
