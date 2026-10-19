# 라우팅 맵 예시

> 이 문서는 [routing.example.md](./routing.example.md)를 한국어로 번역한 문서입니다. 원본과 내용이 다르면 원본을 기준으로 합니다.

이 파일을 `.cursor/automations/benny/` 바깥, 예를 들어 `.cursor/benny/routing.md`로 복사하고 모든 플레이스홀더를 교체합니다. `routing.map_path`가 복사본을 가리키도록 합니다. 팩을 새로 고칠 때 이 복사본을 덮어쓰면 안 됩니다.

트리아지 스킬은 이 파일을 데이터로 취급합니다. 라우트에는 보고 내용이나 원인 트레이스에서 나온 증거가 필요합니다. 키워드가 일치하는 것만으로는 충분하지 않습니다.

```yaml
routes:
  - name: "billing-example"
    match:
      product_areas:
        - "billing-area-placeholder"
      code_paths:
        - "billing-code-path-placeholder"
      error_signatures:
        - "billing-error-placeholder"
    destination:
      slack_channel: "billing-channel-placeholder"
      tracker_team: "billing-team-placeholder"
    owners:
      - "billing-owner-placeholder"
    allow_feature_owner_ping: false

  - name: "desktop-example"
    match:
      product_areas:
        - "desktop-area-placeholder"
      code_paths:
        - "desktop-code-path-placeholder"
      error_signatures:
        - "desktop-error-placeholder"
    destination:
      slack_channel: "desktop-channel-placeholder"
      tracker_team: "desktop-team-placeholder"
    owners:
      - "desktop-owner-placeholder"
    allow_feature_owner_ping: false

fallback:
  destination: ""
  owners: []
  allow_feature_owner_ping: false

ping_policy:
  default: "off"
  allow:
    - "configured-feature-owner"
    - "confirmed-regression-author"
  deny:
    - "broad-on-call-group"
    - "unverified-owner"
```

## 규칙

- 한 팀이 일치하지 않는 모든 보고를 받기로 하지 않았다면 `fallback.destination`은 비워 둡니다.
- 안정적인 제품 영역, 코드 경로, 오류 시그니처를 사용합니다.
- 공개 복사본에는 비공개 데이터를 포함하지 않습니다.
- 게시될 예시에 원시 사용자 ID나 채널 ID를 붙여 넣지 않습니다.
- 대상 팀이 동의할 때까지 기능 담당자 핑은 꺼 둡니다.
- 재라우팅은 보고자에게 어디로 가야 하는지 알려 줍니다. 자동화는 절대 교차 게시하지 않습니다.
