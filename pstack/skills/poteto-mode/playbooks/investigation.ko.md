> 이 문서는 [investigation.md](./investigation.md)을 한국어로 번역한 문서입니다. 원본과 내용이 다르면 원본을 기준으로 합니다.

### Investigation

**답은 여러분이 책임집니다. 계획하고, 라우팅하고, 작성합니다.**

조사 요청은 읽기 전용입니다. 조사 요청의 결과물은 코드 변경이 아니라 출처를 밝힌 설명이나 권고안입니다.

1. **how** 스킬을 통해 라우팅합니다. 동기를 묻는 질문이라면 **why** 스킬을 통해서도 라우팅합니다.
2. 처리량 체크포인트는 한 줄로 유지합니다: `throughput checkpoint: n/a, read-only investigation`.
3. `how` 형식의 결과물(Overview / Key Concepts / How It Works / Where Things Live / Gotchas)을 작성합니다. 요청이 여러 대안 중 하나를 고르는 결정이라면 트레이드오프 표를 포함한 권고안을 작성합니다.
4. 응답에 **unslop** 스킬을 적용합니다.

조사가 코드 변경보다 먼저 이루어지는 경우가 아니라면 PR, babysit, `architect`를 사용하지 않습니다. 코드 변경이 뒤따르는 경우에는 사용자에게 다시 넘기고 Bug fix나 Feature로 다시 라우팅합니다.

**응답:** 조사 결과물을 담습니다. "확실한가요?"라는 질문에 답할 때는 이유와 함께 여러분의 실제 판단을 포함합니다. 전제가 틀렸다면 반박합니다(Autonomy 참고).
