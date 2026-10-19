# 원칙 이름으로 방향 잡기

> 이 문서는 [08-principles.md](./08-principles.md)를 한국어로 번역한 문서입니다. 원본과 내용이 다르면 원본을 기준으로 합니다.

pstack은 24개의 원칙을 각각 개별 스킬로 제공합니다. `/poteto-mode`는 여러 단계로 이루어진 작업을 시작할 때마다 원칙 색인을 읽고, 작업이 해당되는 원칙을 적용하며, 답변에서 적용한 원칙의 이름과 그 원칙이 바꾼 결정을 함께 밝힙니다.

원칙을 직접 호출하지는 않습니다. 원칙의 이름을 사용해 방향을 잡습니다. 각 이름은 에이전트가 이미 읽은 완전한 규칙을 가리키므로, 한 구절만으로도 한 단락 분량의 지시보다 더 정확하게 작업 방향을 바꿀 수 있습니다.

## 실제로 방향 잡기

에이전트가 기존 어댑터 세 개 위에 새 어댑터를 덧붙이려 한다고 가정합니다.

```text
subtract before you add를 적용해. 쓸모없어진 어댑터부터 삭제하고, 그다음에 남은 것을 설계해.
```

빌드가 통과했다는 이유로 에이전트가 성공을 주장한다고 가정합니다.

```text
prove it works를 적용해. 실제 가져오기(import) 흐름을 실행하고 기록된 레코드를 보여 줘.
```

병렬로 진행되는 두 시도가 같은 브랜치에 쓰려 한다고 가정합니다.

```text
separate before serializing shared state를 적용해. 각 시도에 별도의 워크트리를 주고, 락은 쓰지 마.
```

각 구절이 효과를 내는 이유는 그 뒤에 있는 규칙이 구체적이기 때문입니다. 에이전트는 여전히 답변에서 그 규칙이 어떤 결정을 바꿨는지 말해야 합니다. 바뀐 결정 없이 원칙만 인용했다면, 에이전트가 규칙을 적용하지 않고 이름만 언급했다는 뜻입니다.

## 24개 원칙 간단히 보기

핵심 원칙은 얼마나 만들지, 그리고 언제 설계를 다시 생각할지를 결정합니다.

- [Laziness Protocol](../../skills/principle-laziness-protocol/SKILL.md)은 삭제를 우선하고, 문제를 해결하는 가장 작은 변경을 선호합니다.
- [Foundational Thinking](../../skills/principle-foundational-thinking/SKILL.md)은 로직을 작성하기 전에 핵심 자료 구조부터 정합니다.
- [Redesign from First Principles](../../skills/principle-redesign-from-first-principles/SKILL.md)는 새 요구 사항을 처음부터 있었던 것처럼 통합합니다.
- [Attack the Premise](../../skills/principle-attack-the-premise/SKILL.md)는 두 번 이상 실패한 수정들이 공유한 전제를 적어 두고, 다음 수정에 앞서 그 전제를 직접 테스트합니다.
- [Subtract Before You Add](../../skills/principle-subtract-before-you-add/SKILL.md)는 그 위에 무언가를 쌓기 전에 불필요한 부분을 먼저 제거합니다.
- [Minimize Reader Load](../../skills/principle-minimize-reader-load/SKILL.md)는 읽는 사람이 머릿속에 담아 두어야 하는 계층과 숨은 상태를 줄입니다.
- [Outcome-Oriented Execution](../../skills/principle-outcome-oriented-execution/SKILL.md)은 일회성 호환 상태를 유지하는 대신 재작성을 목표 설계로 수렴시킵니다.
- [Experience First](../../skills/principle-experience-first/SKILL.md)는 구현의 편의보다 사용자가 얻는 결과를 선택합니다.
- [Exhaust the Design Space](../../skills/principle-exhaust-the-design-space/SKILL.md)는 선례가 없을 때 서로 경쟁하는 프로토타입을 두세 개 만듭니다.
- [Build the Lever](../../skills/principle-build-the-lever/SKILL.md)는 작업을 수행하거나 증명하는 스크립트를 만들어, 리뷰어가 그것을 다시 실행할 수 있게 합니다. 에이전트가 같은 일을 계속 손으로 반복한다면, 있었으면 좋겠다고 생각하는 도구나 스킬을 직접 작성하게 하세요. 스크립트가 어떤 단계를 매번 같은 방식으로 수행할 수 있다면 스크립트를 사용하고, 에이전트는 판단이 필요한 일에 남겨 두세요.

아키텍처 원칙은 상태, 유효성 검사, 호환성을 어디에 둘지를 결정합니다.

- [Model the Domain](../../skills/principle-model-the-domain/SKILL.md)은 반복되는 규칙을 흩어진 조건문이 아니라 하나의 구조에 담습니다.
- [Boundary Discipline](../../skills/principle-boundary-discipline/SKILL.md)은 경계에서 유효성을 검사하고, 내부 타입은 신뢰합니다.
- [Type System Discipline](../../skills/principle-type-system-discipline/SKILL.md)은 잘못된 상태를 표현할 수 없게 만듭니다.
- [Make Operations Idempotent](../../skills/principle-make-operations-idempotent/SKILL.md)는 재시도가 같은 최종 상태로 수렴하게 합니다.
- [Migrate Callers Then Delete Legacy APIs](../../skills/principle-migrate-callers-then-delete-legacy-apis/SKILL.md)는 마이그레이션과 삭제를 한 번에 진행합니다.
- [Separate Before Serializing Shared State](../../skills/principle-separate-before-serializing-shared-state/SKILL.md)는 조정 장치를 추가하기 전에 공유 자체를 제거합니다.

검증 원칙은 무엇을 증명으로 인정할지를 정의합니다.

- [Prove It Works](../../skills/principle-prove-it-works/SKILL.md)는 대리 지표가 아니라 실제 결과물을 검증합니다.
- [Fix Root Causes](../../skills/principle-fix-root-causes/SKILL.md)는 코드를 바꾸기 전에 문제를 재현하고 원인까지 추적합니다.
- [Sequence Work into Verifiable Units](../../skills/principle-sequence-verifiable-units/SKILL.md)는 작은 단위마다 확인으로 마무리한 뒤에 다음 단위를 시작합니다.
- [Test Behavior, Not Implementation](../../skills/principle-test-behavior-not-implementation/SKILL.md)은 코드 사용자가 관찰할 수 있는 것을 단언하므로, 테스트는 그 동작이 깨질 때 실패하고 내부 구현이 바뀌어도 살아남습니다.
- [Explain the Number](../../skills/principle-explain-the-number/SKILL.md)는 누군가 측정된 숫자를 신뢰하거나 보고하기 전에, 그 숫자를 제한하는 요인을 밝히고 다른 것을 측정했을 가능성을 배제합니다. [`/benchmark-checklist`](../../skills/benchmark-checklist/SKILL.md)는 이 원칙을 실제 실행 결과로 답하는 일곱 가지 질문으로 바꿉니다.

위임 원칙은 병렬 작업이 엉망이 되지 않도록 합니다.

- [Guard the Context Window](../../skills/principle-guard-the-context-window/SKILL.md)는 대량의 읽기 작업을 서브에이전트에게 넘기고, 발견한 내용은 메인 대화에 남깁니다.
- [Never Block on the Human](../../skills/principle-never-block-on-the-human/SKILL.md)은 되돌릴 수 있는 작업이라면 그대로 진행하고 결과를 제시합니다.

그리고 메타 원칙이 하나 있습니다.

- [Encode Lessons in Structure](../../skills/principle-encode-lessons-in-structure/SKILL.md)는 두 번 반복한 조언을 린트, 검사, 스크립트로 바꿉니다. [`/correct`](../../skills/correct/SKILL.md)는 [나만의 방식으로 만들기](./09-make-it-yours.md#fix-the-environment-with-correct)에서 보여 주듯이 이 원칙을 저장소 전체에 적용합니다.

목록을 외우지 마세요. 지금은 훑어보기만 하고, 여기 있는 이름 하나로 막을 수 있었던 일을 에이전트가 하는 것을 발견했을 때 다시 돌아오세요. 그렇게 해야 이 어휘가 몸에 익습니다.

다음: [나만의 방식으로 만들기](./09-make-it-yours.md).
