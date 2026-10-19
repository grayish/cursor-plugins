> 이 문서는 [incident-postmortem.md](./incident-postmortem.md)를 한국어로 번역한 문서입니다. 원본과 내용이 다르면 원본을 기준으로 합니다.

# 인시던트 및 포스트모템 컨텍스트

- **Linear**: `incident`, `sev-*`, `postmortem-action-item`, `reliability` 레이블이 붙은 티켓을 찾습니다
- **Slack**: 대상 코드가 추가된 날짜 무렵의 `#sev-*` 및 `#incident-*` 채널을 검색합니다
- **Git**: "fix for incident", "add defensive check" 같은 메시지의 커밋이나, "revert" 뒤에 "re-apply with..."가 이어지는 커밋은 강력한 신호입니다
