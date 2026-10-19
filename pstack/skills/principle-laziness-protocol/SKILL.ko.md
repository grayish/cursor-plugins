---
name: principle-laziness-protocol
description: "리팩터링할 때, diff 크기를 평가할 때, 또는 추상화, 계층, 신호 전달 경로를 추가하고 싶어질 때 적용합니다. 삭제와 문제를 해결하는 가장 작은 변경 쪽으로 기웁니다."
disable-model-invocation: true
---

> 이 문서는 [SKILL.md](./SKILL.md)를 한국어로 번역한 문서입니다. 원본과 내용이 다르면 원본을 기준으로 합니다.

# Laziness Protocol

- **결정을 한곳에 모읍니다.** 같은 선택을 여러 곳에서 반복하지 않습니다. 그 선택을 단일 진실 공급원(single source of truth) 뒤에 두고, 결과는 단순한 플래그로 전달합니다.
- **diff를 최소화합니다.** 문제를 해결하는 가장 작은 변경을 합니다. "우아한" 보일러플레이트보다 적은 줄 수가 낫습니다.
