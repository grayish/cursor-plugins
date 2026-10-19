---
name: poteto-help
description: pstack 설정, /poteto-mode 사용법, 작업에 맞는 스킬, 플레이북, 원칙 선택을 안내합니다. 질문과 함께 /poteto-help를 입력하세요.
disable-model-invocation: true
---

> 이 문서는 [SKILL.md](./SKILL.md)를 한국어로 번역한 문서입니다. 원본과 내용이 다르면 원본을 기준으로 합니다.

# Poteto help

pstack에 관한 사용자의 질문에 답하고, 사용자가 그대로 보낼 수 있는 프롬프트를 건네고, 답의 출처가 된 파일을 링크합니다. 도움 질문이라면 작업을 시작하지 않습니다. 사용자는 방법을 물었을 뿐이고 pstack 실행은 실제로 토큰을 소모하므로, 프롬프트는 사용자가 직접 보내게 합니다.

"pstack으로 이 버그를 고쳐 줘"처럼 작업을 요청하는 메시지는 도움 질문이 아닙니다. 이때는 [`poteto-mode`](../poteto-mode/SKILL.md)를 읽고 그 스킬에 따라 작업을 수행하며, Custom Mode를 쓰면 모드가 계속 켜져 있다는 점을 한 번 언급합니다.

이 파일은 질문을 그 답을 담고 있는 스킬과 가이드 페이지로 연결합니다. 세부 내용은 해당 파일이 책임집니다. 연결할 파일은 인용하기 전에 읽고, 그 파일이 이 지도와 다르면 그 파일을 믿습니다. 여기 있는 링크는 설치된 플러그인 내부를 가리키는데 사용자는 그 위치를 열지 못할 수 있으므로, 사용자에게는 파일의 공개 사본 주소를 줍니다. 주소는 `https://github.com/cursor/plugins/blob/main/pstack/` 뒤에 해당 파일 경로를 붙인 형태입니다.

## 필요한 것을 파악하기

메시지와 대화 내용에서 필요한 것을 추론합니다. "PR을 리뷰하는 스킬은 무엇인가요?"처럼 상황이 명시되어 있으면 바로 해당 섹션으로 갑니다. 그래도 필요한 것이 불분명하면 다음 선택지로 객관식 질문을 하나만 하고, 사용자가 고른 섹션에만 답합니다.

- 설정하기
- `/poteto-mode`로 작업 시작하기
- 상황에 맞는 스킬 고르기
- 잘못된 실행 바로잡기
- pstack을 내 것으로 만들기

답을 바꾸는 상태를 확인하고, 실제로 답이 바뀔 때만 언급합니다.

- `~/.cursor/rules/pstack-models.mdc`가 없다면 이 사용자에 대해 `/setup-pstack`이 실행된 적이 없다는 뜻이므로, 모든 역할이 기본 모델을 사용합니다.
- 프로젝트에 `verify-*` 스킬이나 다른 앱 하네스가 없다면 에이전트가 스크립트로 앱을 조작할 방법이 없다는 뜻입니다. 질문이 변경 사항이 동작함을 증명하는 것에 관한 것이라면 `/create-verification-skill`을 언급합니다.
- 프로젝트에 형식 검증 스킬이 없다면 테스트가 닿는 범위를 넘어서는 속성은 검사되지 않는다는 뜻입니다. 질문이 경쟁 상태(race), 불변 조건, 또는 테스트로는 거의 걸리지 않는 다른 경우에 관한 것이라면 `/create-formal-verification-skill`을 언급합니다. 형식 검증 스킬이 있고 그 스킬이 다루는 코드가 스킬에 마지막 동기화 시점으로 기록된 커밋 이후에 바뀌었다면, 질문이 그 코드와 관련될 때 `/maintain-formal-verification-skill`을 언급합니다.

모델 규칙이 없고 그것이 중요한 상황이라면, 지금 역할별 모델과 추론 예산을 고르고 싶은지 사용자에게 묻습니다. 중요한 상황이란 사용자가 처음 쓰는 경우, 질문이 설정이나 비용에 관한 경우, 또는 어떤 모델이 실행되는지에 따라 답이 달라지는 경우를 말합니다. 이 질문은 한 채팅에서 최대 한 번만 합니다. 필요한 것도 불분명하다면 두 질문을 함께 묻습니다. 선택지는 두 가지를 제시합니다.

- 지금: 입력할 `/setup-pstack`을 알려 주고, 질문에도 답합니다.
- 나중에: 질문에 답하고, `/setup-pstack`을 실행하기 전까지 모든 역할이 기본 모델을 유지한다는 한 줄을 덧붙입니다.

## 설정하기

1. 채팅에서 `/add-plugin pstack`을 입력하거나, 사이드바의 Customize에서 설치합니다.
2. [`/setup-pstack`](../setup-pstack/SKILL.md)을 실행합니다. 이 스킬은 추론 예산을 묻고, 역할마다 모델을 지정하고, 규칙 하나를 작성합니다. 이 규칙은 새 채팅부터 적용됩니다. 그다음 프로젝트에 이미 검증 스킬과 형식 검증 스킬이 있지 않다면 각각을 생성할지 한 번씩 제안합니다.
3. `/poteto-mode`와 목표, 그리고 통과 또는 실패로 판정할 수 있는 검사를 함께 주어 실제 작업을 시작합니다.

설치만으로는 사용자가 스킬을 호출하기 전까지 아무것도 바뀌지 않습니다. 사용자의 말만으로 로드되는 스킬은 `/setup-pstack`뿐입니다. 자세한 내용은 [README](../../README.md)와 [가이드 1페이지](../../docs/guide/01-setup.md)에 있습니다. [`references/prompting.md`](references/prompting.md)에 따라 사용자의 첫 프롬프트를 함께 다듬어 주겠다고 제안합니다.

비용이 걱정이라면 토큰이 어디에 쓰이는지와 덜 쓰는 방법을 알려 줍니다. pstack은 서브에이전트와 리뷰 패널에 추가 토큰을 씁니다. `/setup-pstack`을 다시 실행해 더 작은 예산이나 더 저렴한 모델을 고릅니다. `auto` 또는 `inherit-parent`로 설정한 역할은 채팅의 모델로 실행되므로, 채팅이 Auto나 더 저렴한 모델로 실행될 때 토큰을 아낄 수 있습니다. 패널 목록을 줄이면 항목마다 하나씩 실행되는 서브에이전트 수가 줄어듭니다. `/poteto-mode`는 엄밀함이 필요한 작업에만 씁니다.

pstack은 Cursor용으로 만들어졌지만, Claude Code에서도 `/plugin marketplace add cursor/plugins`를 실행한 다음 `/plugin install pstack@cursor-plugins`를 실행해 설치할 수 있습니다. Claude Code에서는 각 명령에 `/pstack:poteto-mode`처럼 `pstack:` 접두사가 붙습니다. [Claude Code 어댑터](../../docs/harness/claude-code.md)가 Cursor 이름을 대응시키며, Bugbot이나 Automations처럼 Cursor 전용 기능이 필요한 단계는 한 줄짜리 안내와 함께 건너뜁니다.

스킬은 Agent Skills 형식을 사용하므로 다른 도구도 읽을 수 있습니다. 하지만 `/poteto-mode`, `/how`, `/why`, `/teach`를 비롯한 대부분의 워크플로 스킬은 역할별 모델로 Cursor 서브에이전트를 생성하고, Custom Modes와 `/loop`는 Cursor 기능이므로, 이런 부분은 다른 도구에서 동작하지 않을 수 있습니다.

## `/poteto-mode`로 작업 시작하기

`/poteto-mode`는 작업에 맞는 플레이북을 고르고, 플레이북의 단계를 할 일 목록에 복사하고, 단계에서 필요할 때 다른 스킬을 실행합니다. 건너뛴 단계는 `skip: <reason>` 형태로 목록에 남습니다. 좋은 프롬프트는 목표와 완료를 판단하는 방법을 적습니다. 스킬 목록은 적지 않습니다. 손으로 적은 순서는 플레이북이라면 유지했을 단계를 빠뜨리거나 순서를 바꾸기 쉽기 때문입니다. 프롬프트 작성을 돕기 전에 [`references/prompting.md`](references/prompting.md)를 읽습니다. [가이드 2페이지](../../docs/guide/02-poteto-mode.md)에 예시가 있습니다.

`/poteto-mode`가 계속 켜져 있는지는 사용자가 어떻게 시작했는지에 따라 달라집니다.

- `/poteto-mode`에서 Enter를 누르면 스킬이 메시지 하나에만 붙습니다. 채팅이 진행될수록 효력이 약해집니다.
- Mac에서는 Option+Enter, Windows에서는 Alt+Enter를 누르거나 스킬 항목에서 Use as Mode를 선택하면 Custom Mode가 됩니다. 이 경우 사용자가 모드를 끝낼 때까지 매 턴 컨텍스트에 남아 있으며, 가벼운 대화 턴에는 끼어들지 않습니다.
- Cursor 문서에 따르면 Custom Modes는 Agents Window와 CLI에서 사용할 수 있습니다. 그 밖의 환경에서는 새 작업을 시작할 때마다 `/poteto-mode`를 입력합니다.

이 주제가 나오면 [Cursor 스킬 문서](https://cursor.com/docs/skills)를 링크합니다. 채팅 도중에 "new task"라고 말하면 모드가 새 플레이북을 고릅니다. `/poteto-mode`는 플레이북 단계가 생성하는 서브에이전트에 이미 `poteto-agent`를 사용합니다. 직접 만드는 서브에이전트에서도 같은 스타일을 얻으려면 `subagent_type: "poteto-agent"`로 생성합니다.

## 스킬 고르기

기본 답은 `/poteto-mode`이며, 이 스킬은 단계에서 필요할 때 다른 스킬 대부분을 실행합니다. 사용자가 플레이북이 주는 것보다 무언가를 더 원하거나 덜 원할 때는 스킬 이름을 직접 알려 줍니다. 스킬을 추천하기 전에 그 스킬을 읽고, 예시 프롬프트를 하나 제시합니다.

| 사용자가 원하는 것 | 스킬 |
|---|---|
| 사소하지 않은 작업을 엄밀하게 수행하기 | [`/poteto-mode`](../poteto-mode/SKILL.md) |
| 에이전트가 시작하기 전에 목표와 문제를 제대로 이해했는지 확인하기 | [`/in-your-own-words`](../in-your-own-words/SKILL.md) |
| 시작하기 전에 작업의 수준을 높이기: 더 전문적이고, 결과에 더 집중하며, 과도하게 지정하지 않기 | [`/sharpen`](../sharpen/SKILL.md) |
| 코드가 지금 어떻게 동작하는지, 또는 새 코드를 어디에 두어야 하는지 알기 | [`/how`](../how/SKILL.md) |
| 코드가 왜 이런 형태인지, 또는 어떤 숫자가 어디서 왔는지 알기 | [`/why`](../why/SKILL.md) |
| 변경 사항이나 하위 시스템을 쉬운 말로 설명받아 이해하기 | [`/teach`](../teach/SKILL.md) |
| 어떤 주제에 대해 자신이 최근에 한 작업을 따라잡기 | [`/recall`](../recall/SKILL.md) |
| 이 채팅에서 마지막 메시지 이후 바뀐 것을 쉬운 말과 작은 시각 자료로 따라잡고, 사용자를 기다리는 모든 항목을 목록으로 받아 응답만 보고 결정하기 | [`/ketchup`](../ketchup/SKILL.md) |
| 작은 diff가 그 바깥에서 무엇을 망가뜨릴 수 있는지 알기 | [`/blast-radius`](../blast-radius/SKILL.md) |
| 함수 경계를 넘는 코드를 작성하기 전에 타입과 모듈 구조를 확정하기 | [`/architect`](../architect/SKILL.md) |
| 하나의 브리프에 대해 여러 번 시도하고 가장 좋은 결과로 병합하기 | [`/arena`](../arena/SKILL.md) |
| 클라우드 에이전트로 여러 조각에 대해 병렬 검사를 하거나 작업자끼리 경주시키기 | [`/swarm`](../swarm/SKILL.md) |
| 서로 다른 모델이 diff를 리뷰하고 깨뜨려 보게 하기 | [`/interrogate`](../interrogate/SKILL.md) |
| 비용이 적은 로컬 테스트가 있을 때 테스트를 먼저 작성해 버그 고치기 | [`/tdd`](../tdd/SKILL.md) |
| `.ts` 또는 `.tsx` 작업에 TypeScript 규칙 적용하기 | [`/typescript-best-practices`](../typescript-best-practices/SKILL.md) |
| 주석을 작성하지 않은 리뷰어를 사용해 리뷰 전에 주석 제거하기 | [`/no-comments`](../no-comments/SKILL.md) |
| 글에서 AI가 쓴 티가 나는 부분 걷어 내기 | [`/unslop`](../unslop/SKILL.md) |
| 문서, RFC, README, PR 설명, 커밋 메시지를 기준에 맞게 작성하기 | [`/technical-writing`](../technical-writing/SKILL.md) |
| 마지막 응답을 쉬운 말로 다시 듣기 | [`/bro`](../bro/SKILL.md) |
| 에이전트에게 스크립트로 앱을 조작하고 동작을 증명할 방법 주기 | [`/create-verification-skill`](../create-verification-skill/SKILL.md) |
| 검증 스킬과 그 기능 지도를 앱과 다시 맞추기 | [`/maintain-verification-skill`](../maintain-verification-skill/SKILL.md) |
| 테스트로는 거의 닿지 않는 경쟁 상태, 불변 조건, 기타 속성을 형식 검증하기 | [`/create-formal-verification-skill`](../create-formal-verification-skill/SKILL.md) |
| 다루는 코드가 바뀐 뒤 형식 검증을 다시 맞추고, 실패가 드리프트인지 버그인지 알아내기 | [`/maintain-formal-verification-skill`](../maintain-formal-verification-skill/SKILL.md) |
| 성능 수치를 보고하거나 그에 따라 행동하기 전에 검토하기 | [`/benchmark-checklist`](../benchmark-checklist/SKILL.md) |
| 규모가 크거나 여러 영역에 걸친 변경, 또는 자리를 비웠다가 돌아와 리뷰할 변경 실행하기 | [`/figure-it-out`](../figure-it-out/SKILL.md) |
| 실행 중에 의사결정 기록을 남기고 나중에 리뷰하기 | [`/show-me-your-work`](../show-me-your-work/SKILL.md) |
| 역할별 모델과 추론 예산 고르기 | [`/setup-pstack`](../setup-pstack/SKILL.md) |
| 자신의 작업 습관을 개인 모드 스킬로 만들기 | [`/automate-me`](../automate-me/SKILL.md) |
| 끝난 작업에서 배운 것을 스킬 수정으로 옮기기 | [`/reflect`](../reflect/SKILL.md) |
| 이 저장소에서 에이전트가 같은 실수를 반복하지 않게 하기 | [`/correct`](../correct/SKILL.md) |
| 버튼을 누르면 webhook으로 Grok Bot을 깨우는 페이지 만들기 | [`/make-bot-ui`](../make-bot-ui/SKILL.md) |
| pstack 안에서 길 찾기 | `/poteto-help` |

이 디렉터리 옆에 있는 스킬 디렉터리가 표에 없다면, 그 스킬의 frontmatter를 읽고 description에 따라 연결합니다. `principle-*` 디렉터리는 아래 원칙 부분에서 다룹니다.

헷갈리기 쉬운 경우:

- `/how`는 코드가 무엇을 하는지 설명합니다. `/why`는 그 이유를 설명합니다. `/teach`는 둘 중 하나 또는 둘 다를 실행하고 결과를 쉬운 말로 설명합니다.
- `/arena`는 모든 작업자에게 같은 브리프를 주고 가장 좋은 부분을 병합합니다. `/swarm`은 작업을 여러 조각이나 경주로 나누고 보고서 하나를 돌려줍니다.
- `/architect`는 설계를 확정한 직후 바로 구현합니다. 코드를 작성하기 전에 설계를 리뷰하려면 "with checkpoint"를 덧붙입니다.
- `/interrogate`는 diff를 리뷰합니다. `/blast-radius`는 diff 바깥에서 깨지는 부분을 찾고, 변경을 안전하게 만드는 사실 하나를 증명합니다.
- `/recall`은 최근 여러 채팅에 걸쳐 맥락을 다시 구성합니다. `/ketchup`은 이 채팅에서 사용자의 마지막 메시지 이후만 다룹니다. 특정 채팅이나 브랜치 하나를 이어받는 것은 Session pickup 플레이북입니다.
- `/figure-it-out`은 엄밀한 실행 하나를 설계합니다. Orchestrate 플레이북은 며칠에 걸쳐 여러 PR로 이어지는 프로그램을 실행합니다. Autonomous run 플레이북은 작업 하나를 완료 조건까지 끌고 갑니다.

pstack에 없는 것:

- `/deslop`, `control-cli`, `control-ui`는 `cursor-team-kit` 플러그인에 들어 있습니다.
- `/loop`와 `/create-skill`은 Cursor 내장 기능입니다.
- pstack에는 `/orchestrate` 스킬이 없습니다. Orchestrate는 `/poteto-mode` 플레이북입니다. 슬래시 메뉴에 `/orchestrate`가 보인다면 다른 플러그인이 제공하는 것입니다.

## 플레이북과 원칙

플레이북은 스킬이 아니라 `/poteto-mode` 안에 있는 단계 목록이므로 슬래시 명령이 없습니다. `/poteto-mode` 안에서는 작업을 설명하면 플레이북이 하나 선택되며, 다음 문구는 플레이북을 직접 지정합니다.

- "babysit this pr" 또는 "check on pr 123"은 Babysit을 실행합니다. 이 플레이북은 PR을 병합 가능한 상태까지 만들고 거기서 멈춥니다. 사용자가 merge, land, ship을 요청하지 않는 한 병합하지 않습니다.
- "land the stack"은 Shipping을 실행합니다.
- "take over this branch"는 Session pickup을 실행합니다.
- "pause safely"는 Pause safely를 실행합니다.
- "full autopilot on this queue"는 Autopilot-full을 실행합니다. "stack them, don't ship"은 Autopilot-stack을 실행합니다.
- "run the eval playbook"은 Eval을 실행합니다.

`/poteto-mode` 없이 "babysit this pr" 같은 문구를 쓰면 같은 일을 하는 Cursor 자체 스킬이 대신 시작될 수 있습니다. [`poteto-mode`](../poteto-mode/SKILL.md)의 Playbooks 섹션에 모든 플레이북과 각각이 적용되는 경우가 나와 있습니다. [가이드 6페이지](../../docs/guide/06-verify-and-ship.md)는 PR을 열고, 돌보고, 반영하는 과정을 다룹니다.

pstack에는 계획 스킬이 없습니다. Cursor의 Plan Mode를 함께 사용할 수 있습니다. 여러 단계나 쌓인 PR에 걸치는 작업이라면 `/poteto-mode`에 계획을 요청할 때 [Multi-phase plan 플레이북](../poteto-mode/playbooks/multi-phase-plan.md)이 실행되며, 이 플레이북은 계획을 작성하되 구현하지는 않습니다. 설계 질문이라면 Prototype 플레이북이나 `/architect`가 먼저 코드로 결론을 냅니다.

원칙은 규칙 하나짜리 스킬로, `/poteto-mode`가 읽고 응답에서 인용합니다. 사용자가 원칙을 직접 호출하는 경우는 드뭅니다. 대신 "apply prove it works. show me the real output."처럼 이름으로 방향을 잡습니다. `/principle-<name>`을 입력하면 필요할 때 원칙 하나를 로드할 수도 있습니다. [가이드 8페이지](../../docs/guide/08-principles.md)에 원칙 목록이 있습니다.

## 잘못된 실행 바로잡기

| 증상 | 해결 방법 |
|---|---|
| 몇 턴 뒤에 모드가 더 이상 적용되지 않습니다 | Enter로 시작했기 때문입니다. Custom Mode로 시작하거나, 작업마다 `/poteto-mode`로 시작합니다. |
| 질문이 이전 작업의 다음 단계로 처리되었습니다 | "new task"라고 말하거나, 이번 턴에는 모드가 필요 없다고 말합니다. |
| 새로 고른 모델이 적용되지 않았습니다 | `/setup-pstack`이 만든 규칙은 새 채팅부터 적용됩니다. 새 채팅을 시작합니다. |
| 실행 비용이 예상보다 많이 듭니다 | 설정하기 섹션의 비용 단락을 참고합니다. |
| 스킬이 저절로 로드되지 않았습니다 | 사용자의 말만으로 로드되는 스킬은 `/setup-pstack`뿐입니다. 나머지는 사용자가 직접 입력하거나 `/poteto-mode`가 실행할 때 로드되며, `/poteto-mode`가 모든 스킬을 실행하지는 않습니다. |
| 병렬 에이전트가 서로의 작업을 덮어썼습니다 | 에이전트마다 별도의 워크트리를 주거나, 각자 별도의 머신을 받는 클라우드 에이전트로 실행합니다. |
| 야간 작업 실행이 진행은 했지만 아무것도 끝내지 못했습니다 | `/loop`에는 지속 시간이 아니라 통과 또는 실패로 판정할 수 있는 검사가 필요합니다. [가이드 7페이지](../../docs/guide/07-overnight.md)를 참고합니다. |
| 응답이 빌드 성공만 보고 성공했다고 주장합니다 | 실제 명령, 흐름, 저장된 값, 프로파일을 요청합니다. 이것이 prove-it-works 원칙입니다. |

실행이 방향을 잃을 때 쓸 한 줄짜리 방향 지시는 [`references/prompting.md`](references/prompting.md)에 있습니다. [가이드 10페이지](../../docs/guide/10-recipes-and-pitfalls.md)에는 더 많은 함정과 따라 할 만한 레시피가 있습니다.

## pstack을 내 것으로 만들기

- [`/automate-me`](../automate-me/SKILL.md)는 사용자 자신의 기록을 바탕으로 `/poteto-mode`와 함께 쓸 개인 모드 스킬 초안을 작성합니다.
- 세션이 끝난 뒤 [`/reflect`](../reflect/SKILL.md)를 실행하면 세션에서 얻은 교훈을 사용자가 승인하는 스킬 수정으로 바꿉니다.
- `/poteto-mode write a skill for <workflow>`는 작성(authoring) 플레이북을 실행합니다. eval 플레이북은 스킬 변경을 블라인드로 테스트합니다.
- 오작동하는 스킬은 문제가 생긴 기능 작업 안에서 고치지 말고 별도의 PR로 고칩니다.

[가이드 9페이지](../../docs/guide/09-make-it-yours.md)에서 이 내용을 각각 다룹니다.

## 응답

답을 먼저 제시합니다. 예시 프롬프트는 코드 블록으로 최대 하나만 주고, 맞는 것이 있으면 [`references/recipes.md`](references/recipes.md)에서 가져와 다듬은 뒤 그 파일의 링크를 붙입니다. 사용자가 전체 지도를 요청하지 않았다면 짧게 답합니다.
