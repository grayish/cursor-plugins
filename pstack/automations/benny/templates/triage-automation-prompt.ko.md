> 이 문서는 [triage-automation-prompt.md](./triage-automation-prompt.md)를 한국어로 번역한 문서입니다. 원본과 내용이 다르면 원본을 기준으로 합니다.

# 분류 자동화 프롬프트

> 복사된 설정 워크플로에서 쓰는 원본 자료입니다. 자동화가 실행될 저장소에 복사된 팩이 커밋되어 있다고 `automate`가 확인한 뒤, 이 의도를 바꾸어 표현해 내장 `automate` 초안으로 만듭니다.

이번 실행에서는 `.cursor/automations/benny/skills/triage-issue-reports/SKILL.md`를 읽고 따릅니다.

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

생성 의도에는 이것이 설정된 원본 Slack 채널에 올라온 새로운 최상위 보고라고 설명해야 합니다.

원본 채널과 루트 스레드 타임스탬프는 변경할 수 없는 값으로 취급합니다. 둘 중 하나라도 없거나 설정과 일치하지 않으면 게시하거나 이슈 트래커에 쓰지 않고 중단합니다.

분류, 첨부 파일 검토, 원인 추적, 라우팅, 중복 제거, 트래커 쓰기, 최종 판정은 커밋된 운영 파일이 담당합니다. 진행 상황 메시지는 게시하지 않습니다. 원본 채널에 루트 메시지를 절대 게시하지 않습니다.

Slack에 게시하는 주체는 코디네이터 하나뿐입니다. 위임받은 워커는 모두 읽기 전용이어야 하고, 조사 결과만 반환해야 하며, 모든 Slack 쓰기 작업을 금지한다는 명시적인 지시를 받아야 합니다.

하나뿐인 판정은 설정된 마커 중 정확히 하나로 끝냅니다.

```text
[benny:bug]
[benny:performance]
[benny:other]
```

버그 또는 성능 마커에는 `tracker=<URL>`을 덧붙일 수 있습니다.
