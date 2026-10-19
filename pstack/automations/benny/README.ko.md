> 이 문서는 [README.md](./README.md)를 한국어로 번역한 문서입니다. 원본과 내용이 다르면 원본을 기준으로 합니다.

# benny

benny는 slack 이슈 보고를 위한 cursor 자동화 두 개를 제공합니다. 하나는 각 보고를 분류합니다. 다른 하나는 확인된 버그를 재현하고 작은 초안 수정을 준비할 수 있습니다.

이 디렉터리의 파일은 설정과 자동화의 원본이며, 그 자체로는 동작하지 않습니다. 슬래시 스킬로 나타나지 않습니다.

## 설정하기

1. cursor가 [`FOR_AGENTS.md`](./FOR_AGENTS.md)를 보도록 지정하고 대상 저장소를 알려 줍니다.
2. 설정 과정이 이 디렉터리 전체를 대상의 `.cursor/automations/benny/`에 병합하게 합니다. 이때 대상에만 있는 파일을 보존해야 하고, 로컬 수정 내용을 덮어쓰지 말고 충돌을 리뷰해야 합니다.
3. 설정 과정이 공유 의존성을 위해 대상 저장소의 `.cursor/settings.json`에서 pstack을 활성화하게 합니다.

```json
{
	"plugins": {
		"pstack": { "enabled": true }
	}
}
```

4. 사용자가 소유한 설정은 복사한 팩 밖의 위치(예: `.cursor/benny/`)에 둡니다. [`configuration.example.yaml`](./templates/configuration.example.yaml)과 [`feature-map.example.md`](./skills/reproduce-and-fix-issues/references/feature-map.example.md)를 상황에 맞게 수정합니다.
5. 두 자동화 중 어느 것이든 활성화하기 전에 `.cursor/settings.json`, `.cursor/automations/benny/`, 비밀 값이 없는 설정을 커밋합니다.
6. 새 자동화 초안을 각각 리뷰하거나, 기존 자동화를 편집기에서 업데이트합니다. 그런 다음 무해한 테스트 보고를 보내고, 소스 채널에 올라오는 모든 글이 원래 스레드 안에 머무는지 검증합니다.
