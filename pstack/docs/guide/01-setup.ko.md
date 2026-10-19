> 이 문서는 [01-setup.md](./01-setup.md)을 한국어로 번역한 문서입니다. 원본과 내용이 다르면 원본을 기준으로 합니다.

# pstack 설정하기

이 페이지에서는 플러그인을 설치하고, pstack이 사용할 모델을 고르고, 첫 작업을 실행합니다. 설정은 명령어 하나와 짧은 대화로 끝납니다.

## 플러그인 설치하기

Cursor 채팅에서 다음을 실행합니다.

```text
/add-plugin pstack
```

Cursor가 플러그인이 설치되었다고 확인해 줍니다.

Claude Code에서는 `/plugin marketplace add cursor/plugins`를 실행한 다음 `/plugin install pstack@cursor-plugins`를 실행합니다. Claude Code는 플러그인 명령어에 접두사를 붙이므로, 이 가이드에 나오는 각 명령어 앞에 `pstack:` 접두사가 붙습니다. 예를 들어 `/pstack:setup-pstack`이 그렇고, 이 명령어는 모델 규칙을 `~/.claude/rules/pstack-models.md`에 기록합니다. 그 밖의 차이점은 [Claude Code 어댑터](../harness/claude-code.md)에서 다룹니다.

## 모델 고르기

다음을 실행합니다.

```text
/setup-pstack
```

[`/setup-pstack`](../../skills/setup-pstack/SKILL.md)은 사용할 수 있는 모델을 감지하고, 추론 예산(reasoning budget)을 묻고, 각 역할(코드 위임, 판단, 리뷰 패널)을 보여 준 뒤 원하는 설정을 묻습니다. 질문에 답하면 됩니다. 그러면 모든 pstack 스킬이 읽는 작은 규칙 파일인 `~/.cursor/rules/pstack-models.mdc`를 작성합니다.

기본값은 `large` 예산과 같은 `xhigh` 추론 수준으로 실행됩니다. `unlimited`는 각 모델을 `max`까지 가능한 최고 단계로 올립니다. Opus는 `max`까지 올라갑니다. Grok은 최고 단계가 `xhigh`이므로 그대로 `xhigh`에 머뭅니다. `medium`과 `small`은 추론 수준을 낮추고 토큰을 더 적게 사용합니다.

신경 쓰이는 부분만 재정의하면 됩니다. 규칙에 해당 줄이 없는 역할은 스킬의 기본값을 유지합니다. 기본값으로 되돌리려면 그 역할의 줄을 삭제합니다. `/setup-pstack`을 다시 실행하면 모델이 기본값과 다른 역할은 그대로 유지됩니다. 기본값이 바뀌어도 변경 전에 작성된 규칙은 여전히 이전 기본값을 고정하고 있으므로, 해당 역할 줄을 삭제하거나 파일을 삭제한 뒤 `/setup-pstack`을 다시 실행합니다.

Auto를 사용하면 어떻게 되는지 궁금할 수 있습니다. 역할을 `inherit-parent` 또는 `auto`로 설정하면 pstack은 서브에이전트의 `model` 필드를 생략하므로, 서브에이전트가 부모 채팅의 모델을 물려받습니다. 두 값은 같은 의미이며, 둘 다 모델 슬러그(slug)가 아닙니다. 패널 역할의 값은 목록이고, 항목마다 서브에이전트 하나가 실행되므로 목록 길이가 패널 크기를 결정합니다. 설정 과정에서는 `swarm workers`도 구성합니다. 이것은 경주(race)가 각 갈래(arm)마다 모델을 지정하지 않는 한 모든 `/swarm` 워커가 사용하는 기본 모델입니다.

## 검증 제안을 수락하거나 거절하기

설정이 끝날 무렵 `/setup-pstack`은 프로젝트에서 앱 동작을 증명할 방법, 즉 `verify-*` 스킬이나 기존 하네스를 찾습니다. 둘 다 찾지 못하면 [`/create-verification-skill`](../../skills/create-verification-skill/SKILL.md)로 하나를 생성하겠다고 한 번 제안합니다.

수락하면 `.cursor/skills/verify-<app>/`를 작성합니다. 이것은 사용자가 앱을 조작하는 방식 그대로 에이전트가 앱을 조작하도록 가르치는 프로젝트 로컬 스킬입니다. 스킬을 넘겨주기 전에 스킬이 동작한다는 것을 한 번 증명합니다. 거절하면 설정은 다음 단계로 넘어갑니다. `/create-verification-skill`은 언제든지 직접 실행할 수 있습니다. 자세한 내용은 [검증하고 출시하기](./06-verify-and-ship.md#create-a-project-verification-skill)에서 다룹니다.

pstack을 처음 사용한다면 수락하세요. 자기 작업을 스스로 확인할 수 있는 에이전트는 확인이 통과할 때까지 작업을 계속합니다. 확인할 수 없는 에이전트는 모든 결과를 사용자에게 넘겨 직접 확인하게 합니다. 이 가이드에 나오는 모든 것 중에서 검증 스킬이 가장 큰 효과를 냅니다.

그다음 설정은 프로젝트에 형식 검증 스킬이 이미 있지 않다면 [`/create-formal-verification-skill`](../../skills/create-formal-verification-skill/SKILL.md)로 형식 검증 스킬을 생성하겠다고 한 번 제안합니다. 테스트는 누군가 작성할 생각을 한 경우만 확인합니다. 형식 검증 스킬은 "작업이 동시에 두 번 실행되지 않는다"처럼 코드의 핵심 속성 하나를 명시한 범위 안의 모든 경우에 대해 확인합니다. 코드에 동시성, 재시도, 상태 기계, 파서가 있어서 버그 비용이 크고 테스트가 문제를 일으키는 경우에 거의 도달하지 못한다면 수락하세요. 그렇지 않다면 거절하고, 상황이 바뀌면 나중에 실행하세요. 이 내용은 [검증하고 출시하기](./06-verify-and-ship.md#prove-a-property-with-a-formal-verification-skill)에서 다룹니다.

설정이 끝나면 새 채팅을 시작합니다. 모델 규칙은 새 세션에 적용됩니다.

## 비용 관리하기

pstack은 서브에이전트와 리뷰 패널에 토큰을 추가로 사용합니다. 이것이 엄밀함을 얻는 대가입니다. 토큰을 덜 쓰려면 다음과 같이 합니다.

- `/setup-pstack`을 다시 실행하고 더 작은 추론 예산이나 더 저렴한 모델을 고릅니다. 메인 채팅에는 강력한 모델을 쓰고 코드 역할에는 더 저렴하고 빠른 모델을 쓰는 조합이 좋습니다.
- 역할을 `auto` 또는 `inherit-parent`로 설정해 채팅 자체의 모델로 실행되게 합니다.
- 패널 목록을 줄입니다. 항목마다 서브에이전트 하나가 실행됩니다.
- `/poteto-mode`는 엄밀함이 필요한 작업에만 사용합니다. 작고 명백한 수정에는 필요하지 않습니다.

## 첫 작업 실행하기

실제 작업이지만 작은 것을 골라, 동료에게 설명하듯이 설명합니다.

```text
/poteto-mode 이 명령어에 --json 플래그를 추가해. 텍스트 출력은 바이트 단위로 동일하게 유지해. 둘 다 검증해.
```

할 일 목록을 지켜보세요. 처음 항목들은 매칭된 플레이북의 단계를 복사해 온 것이며, 이 프롬프트에서는 Feature 플레이북입니다. `/poteto-mode`가 어떤 단계를 건너뛰면 그 단계는 `skip: <reason>`과 함께 목록에 남으므로, 무엇을 하지 않기로 했는지 확인할 수 있습니다.

여기서부터는 평소처럼 후속 메시지를 입력하면 됩니다. 채팅 전체에서 `/poteto-mode`를 켜 두려면 `/` 메뉴에서 Enter 대신 Option+Enter(Mac) 또는 Alt+Enter(Windows)로 선택합니다. 그러면 [Custom Mode](https://cursor.com/docs/skills#using-a-skill-as-a-custom-mode)가 되어, 종료할 때까지 매 턴마다 컨텍스트에 남아 있습니다. Custom Mode는 Agents Window와 CLI에서 사용할 수 있습니다. 그냥 Enter를 누르면 스킬이 메시지 하나에만 첨부되고, 채팅이 진행될수록 영향력이 약해집니다.

다음: [`/poteto-mode`로 작업 라우팅하기](./02-poteto-mode.md).
