> 이 문서는 [reproduce-automation-prompt.md](./reproduce-automation-prompt.md)를 한국어로 번역한 문서입니다. 원본과 내용이 다르면 원본을 기준으로 합니다.

# 재현 자동화 프롬프트

> 복사된 설정 워크플로에서 쓰는 원본 자료입니다. 자동화가 실행될 저장소에 복사된 팩이 커밋되어 있다고 `automate`가 확인한 뒤, 이 의도를 바꾸어 표현해 내장 `automate` 초안으로 만듭니다.

이번 실행에서는 `.cursor/automations/benny/skills/reproduce-and-fix-issues/SKILL.md`를 읽고 따릅니다.

설정 소스입니다. 이 저장소 상대 경로는 같은 대상 저장소에 커밋되어 있을 때만 포함합니다. 그렇지 않으면 설정된 값을 바꾸어 표현해 적습니다. 플러그인 소스나 캐시 경로는 절대 사용하지 않습니다.

```text
{{BENNY_CONFIG_PATH}}
```

트리거:

```json
{
	"source_channel_id": "{{SLACK_CHANNEL_ID}}",
	"message_ts": "{{SLACK_MESSAGE_TS}}",
	"thread_ts": "{{SLACK_THREAD_TS_OR_EMPTY}}"
}
```

생성 의도에는 이것이 설정된 원본 Slack 채널에 올라온 새로운 최상위 보고라고 설명해야 합니다. 설정된 저장소, 기본 브랜치, 이슈 트래커, 제어 어댑터, 기능 맵, draft PR 기능도 포함해야 합니다.

원본 채널과 루트 스레드 타임스탬프는 변경할 수 없는 값으로 취급합니다. 둘 중 하나라도 없거나 설정과 일치하지 않으면 게시하지 않고 중단합니다.

바로 이 스레드에서 설정된 분류 계정(identity)이 설정된 분류 마커를 남길 때까지 기다립니다. `[benny:bug]` 또는 `[benny:performance]`인 경우에만 진행합니다.

재현을 시도하기 전에 설정된 제어 어댑터 스킬을 반드시 갖추어야 합니다. 실제 UI를 통해 구별되는 정확한 증상을 두 번 재현합니다. 기존 PR이나 커밋은 그 위에 덮어쓰지 않고 검증합니다. 재현이 확인되고 운영 파일의 수정 관문을 통과한 뒤에만 범위를 제한한 수정을 시도합니다.

Slack에 게시하는 주체는 코디네이터 하나뿐입니다. 모든 하위 프롬프트는 `SendSlackMessage`, `PostToSlack`, `chat.postMessage`와 그 밖의 모든 Slack 쓰기를 금지해야 합니다. 하위 작업자는 조사 결과만 반환합니다.

원본 채널에 루트 메시지를 절대 게시하지 않습니다.
