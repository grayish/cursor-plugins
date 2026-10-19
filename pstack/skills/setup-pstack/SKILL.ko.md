---
name: setup-pstack
description: pstack이 역할별로 어떤 모델을 어떤 추론 예산으로 사용할지 설정합니다. 사용 가능한 모델을 감지하고, 스킬 기본값을 덮어쓰는 항상 적용 규칙을 작성합니다. /setup-pstack, "configure pstack models", "pstack budget", 또는 pstack의 모델 선택을 바꿀 때 사용합니다.
---

> 이 문서는 [SKILL.md](./SKILL.md)를 한국어로 번역한 문서입니다. 원본과 내용이 다르면 원본을 기준으로 합니다.

# Setup pstack

pstack의 역할별 모델을 지정하는 항상 적용 규칙인 `~/.cursor/rules/pstack-models.mdc`를 작성합니다.

## 단계

### 1. 사용 가능한 모델 감지하기

이 세션에서 `Task` 서브에이전트에 전달할 수 있는 모델 slug를 나열합니다. 이것이 믿을 수 있는 출처입니다. Cursor가 사용자에게 권한이 있는 모델을 나열하는 models API나 CLI도 제공한다면, 빠짐없이 확인하기 위해 그쪽을 우선합니다. 아무것도 감지할 수 없으면 사용자에게 접근 가능한 slug를 붙여 넣어 달라고 요청합니다. 사용 가능하다고 확인하지 않은 실제 slug는 절대 쓰지 않습니다. 별칭 `inherit-parent`와 `auto`는 감지된 slug가 아니어도 항상 유효합니다.

### 2. 현재 상태 불러오기

기본 역할-모델 매핑은 아래 5단계에 나온 규칙 형태입니다. `~/.cursor/rules/pstack-models.mdc`가 이미 있으면 그 파일을 읽고, 파일의 `# budget` 줄과 역할 값을 현재 선택으로 간주합니다. 파일이 없으면 그 기본값에서 시작합니다. `how critics`처럼 역할이 5단계에 없는 줄은 폐기된 역할의 줄입니다. 그런 줄은 삭제합니다.

### 3. 예산 정하기, 매핑하기, 확인하기

**(a) 예산을 묻습니다.** 자유 텍스트보다 AskQuestion을 우선합니다. 다음 네 가지 선택지를 정확히 이 레이블로 제시하고, 규칙에 예산이 기록되어 있으면 현재 예산을 알려 줍니다. 규칙이 없으면 `large`가 스킬 기본값과 같다고 알려 줍니다.

- `unlimited — max reasoning`
- `large — xhigh reasoning`
- `medium — high reasoning`
- `small — medium reasoning`

**(b) 예산을 적용합니다.** 스킬 기본값으로 작업 표를 만들고, 다시 실행하는 경우에는 사용자가 계열, 목록, 별칭(`inherit-parent`, `auto`)으로 바꾼 역할을 유지합니다. `unlimited`, `large`, `medium`, `small`은 패널 항목을 포함한 모든 실제 slug의 effort 토큰을 각각 `max`, `xhigh`, `high`, `medium`으로 설정합니다. effort 토큰은 마지막 토큰이거나, 끝에 `fast`가 붙어 있으면 그 앞 토큰이며, 순서는 `max` > `xhigh` > `high` > `medium` > `low`입니다. 결과가 감지된 slug가 아니면, 같은 계열의 감지된 slug 중 목표 이하에서 effort가 가장 높은 것을 사용하고, 그런 slug도 없으면 해당 역할을 선택이 필요한 역할로 표시합니다. `inherit-parent`와 `auto`는 바뀌지 않습니다. 따라서 `unlimited`는 `claude-opus-5-5-xhigh`를 `claude-opus-5-5-max`로 바꿉니다. Grok slug는 최고 수준이 `xhigh`이므로, `unlimited`에서는 대체 규칙에 따라 Grok이 `xhigh`가 되고 `grok-4.7-xhigh-fast`가 그대로 유지됩니다. `large`는 두 기본값을 모두 유지합니다. `small`은 두 기본값을 `claude-opus-5-5-medium`과 `grok-4.7-medium-fast`로 바꿉니다.

**(c) 역할을 보여 주고 확인합니다.** 모든 역할과 그 모델을 보여 주고, 감지된 집합에 없는 실제 slug는 선택이 필요하다고 표시합니다. 2단계에서 삭제한 각 줄도 나열합니다. 그대로 받아들일지, 특정 역할을 바꿀지 묻고, 선택지로 감지된 모델과 `inherit-parent`, `auto`를 제시합니다(두 별칭 모두 해당 역할이 부모 채팅 모델에서 실행된다는 뜻이며, Auto 사용자는 이 방식으로 Auto를 계속 사용합니다). 자유 텍스트보다 AskQuestion을 우선합니다. 패널 역할(arena runners, architect runners, interrogate reviewers)의 값은 목록이며, 별칭 항목을 포함해 항목마다 서브에이전트가 하나씩 실행되므로 목록 길이가 실행 개수를 결정합니다. `arena cross-judge pool`도 목록이지만, Arena는 가능하면 그 목록에서 부모와 모델 계열이 다른 값 하나를 고릅니다. `swarm workers`는 경쟁이나 비교에서 갈래마다 다른 모델을 지정하지 않는 한 모든 worker의 기본 모델입니다.

### 4. 검증하기

작성하는 모든 실제 slug는 감지된 집합에 있어야 합니다. `inherit-parent`와 `auto`는 항상 통과합니다. 선택한 실제 slug를 사용할 수 없으면 멈추고 다시 묻습니다.

### 5. 규칙 작성하기

`alwaysApply: true`, 선택한 레이블과 그 목표 effort를 담은 `# budget` 줄, 그리고 poteto-mode가 사용하는 것과 같은 레이블로 역할마다 한 줄씩 넣어 `~/.cursor/rules/pstack-models.mdc`를 작성합니다. 다시 실행해도 결과가 같도록 파일 전체를 덮어씁니다. 형태는 다음과 같습니다.

```
---
description: pstack per-role model choices (overrides skill defaults)
alwaysApply: true
---
# pstack model configuration. One line per role. Delete a line to fall back to the skill default.
# `inherit-parent` or `auto` as a value: the role runs on the parent chat model (omit Task `model`). Alias entries in a panel list still count toward its fan-out.
# budget: large (xhigh)
feature, refactoring: grok-4.7-xhigh-fast
bug-fix: grok-4.7-xhigh-fast
perf-issue: grok-4.7-xhigh-fast
hillclimb: grok-4.7-xhigh-fast
judgment and prose: claude-opus-5-5-xhigh
hardest tasks: claude-opus-5-5-xhigh
how explorer: grok-4.7-xhigh-fast
how explainer: claude-opus-5-5-xhigh
why investigators: grok-4.7-xhigh-fast
why synthesizer: claude-opus-5-5-xhigh
reflect tooling: grok-4.7-xhigh-fast
reflect judgment, divergent, synthesizer: claude-opus-5-5-xhigh
arena runners: claude-opus-5-5-xhigh, grok-4.7-xhigh-fast
arena cross-judge pool: claude-opus-5-5-xhigh, grok-4.7-xhigh-fast
swarm workers: grok-4.7-xhigh-fast
architect runners: claude-opus-5-5-xhigh, grok-4.7-xhigh-fast
interrogate reviewers: claude-opus-5-5-xhigh, grok-4.7-xhigh-fast
```

### 6. 확인하기

사용자에게 규칙을 작성했으며 새 세션부터 적용된다고 알립니다. 이 스킬을 다시 실행하면 규칙이 갱신됩니다.

### 7. 검증 스킬 제안하기(선택)

프로젝트에 실제 앱을 구동해 증명할 방법(`verify-*` 스킬 또는 기존 하네스)이 있는지 확인합니다. 없으면 한 번만 다음과 같이 제안합니다. "에이전트가 사용자처럼 앱을 구동해서 변경이 제대로 동작하는지 증명할 수 있도록, 프로젝트 전용 검증 스킬을 만들어 드릴까요? /create-verification-skill로 생성할 수 있습니다." 사용자가 수락하면 `/create-verification-skill`을 실행합니다(pstack이 워크스페이스, 사용자, 플러그인 중 어디에 설치되어 있든 찾아서 실행됩니다). 거절하면 더 권하지 않고 넘어갑니다.

### 8. 형식 검증 스킬 제안하기(선택)

프로젝트에 이미 형식 검증 스킬(모델 체커, 증명, 또는 다른 검증 도구를 코드에 대해 실행하는 프로젝트 스킬)이 있는지 확인합니다. 없으면 한 번만 다음과 같이 제안합니다. "테스트가 닿는 경우뿐 아니라 범위 안의 모든 경우에서 코드의 핵심 속성이 성립한다는 것을 에이전트가 증명할 수 있도록, 형식 검증 스킬을 만들어 드릴까요? /create-formal-verification-skill로 생성할 수 있습니다." 사용자가 수락하면 `/create-formal-verification-skill`을 실행합니다(pstack이 워크스페이스, 사용자, 플러그인 중 어디에 설치되어 있든 찾아서 실행됩니다). 거절하면 더 권하지 않고 넘어갑니다.
