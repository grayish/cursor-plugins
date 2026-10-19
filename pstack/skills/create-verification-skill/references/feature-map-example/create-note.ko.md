# 노트 만들기

> 이 문서는 [create-note.md](./create-note.md)를 한국어로 번역한 문서입니다. 원본과 내용이 다르면 원본을 기준으로 합니다.

노트 만들기 기능으로 사용자는 브라우저나 CLI에서 제목이 있는 노트를 저장하고, 완성하지 않은 초안을 취소하고, 사용자에게 보이는 두 번째 화면에서 저장된 노트를 확인할 수 있습니다.

## 하위 기능

- `create-open`은 브라우저의 각 진입점에서 빈 편집기를 엽니다.
- `create-save`는 제목과 본문을 저장합니다.
- `create-cancel`은 완성하지 않은 브라우저 초안을 버립니다.
- `create-cli`는 터미널에서 같은 형태의 노트를 만듭니다.

## 접근 방법 (사용자 관점)

- 브라우저 툴바에서 `New note` 버튼을 선택합니다.
- 편집 가능한 필드 밖에 포커스가 있을 때 브라우저에서 `n`을 누릅니다.
- 터미널에서 `notes create --title <title> --body <body>`를 실행합니다.

## control-notes로 조작하기

사전 조건:

- Notes가 `http://127.0.0.1:4173`에서 정상적으로 동작합니다.
- 제목이 `Release checklist`인 노트가 없습니다.
- `control-notes doctor`가 예상한 URL과 일회용 데이터 디렉터리를 보고합니다.

- **편집기 열기.** `New note`를 선택합니다. `control-notes browser click --role button --name "New note"`를 실행합니다. `Note editor`라는 이름의 폼이 나타나고 `Title` 텍스트 상자에 포커스가 있습니다.
- **내용 입력.** 제목과 본문을 입력합니다. `control-notes browser fill --role textbox --name "Title" --value "Release checklist"`와 `control-notes browser fill --role textbox --name "Body" --value "Tag and publish"`를 실행합니다. `Save note` 버튼이 활성화됩니다.
- **노트 저장.** `Save note`를 선택합니다. `control-notes browser click --role button --name "Save note"`를 실행합니다. `Note saved`라는 이름의 상태가 나타나고 제목에 `Release checklist`가 표시됩니다.
- **저장 여부 확인.** 노트 목록으로 돌아가서 노트를 다시 엽니다. `control-notes browser click --role link --name "All notes"`와 `control-notes browser click --role link --name "Release checklist"`를 실행합니다. 편집기에 저장된 두 값이 모두 표시됩니다.
- **초안 취소.** 새 노트를 열고 `Discard me`를 입력한 다음 `Cancel`을 선택합니다. `control-notes browser click --role button --name "New note"`, `control-notes browser fill --role textbox --name "Title" --value "Discard me"`, `control-notes browser click --role button --name "Cancel"`을 실행합니다. 노트 목록으로 돌아오고 목록에 `Discard me` 링크가 없습니다.
- **CLI 진입.** 두 번째 노트를 만듭니다. `control-notes cli -- notes create --title "CLI note" --body "Created from terminal" --format json`을 실행합니다. 종료 코드는 `0`이고 stdout에 새 노트 ID와 제목이 들어 있습니다.
- **증거.** `All notes`에서 저장된 두 노트를 다시 엽니다. `control-notes browser snapshot --aria --path artifacts/create-note/list.aria.txt`와 `control-notes browser screenshot --path artifacts/create-note/list.png`를 실행합니다. 산출물에 `Release checklist`와 `CLI note`가 표시됩니다.

## 주의할 점

- 텍스트 상자에 포커스가 있을 때 `n`을 누르면 새 편집기가 열리지 않고 문자가 입력됩니다.
- 제목은 저장할 때 앞뒤 공백이 제거됩니다. 초안 입력값이 아니라 화면에 렌더링된 제목을 검증합니다.
- 저장 상태 표시만으로는 증거가 충분하지 않습니다. 목록에서 노트를 다시 엽니다.
- 픽스처 정리 단계에서 `Release checklist`와 `CLI note`를 삭제하되, 이 노트들의 증거 산출물은 보존합니다.
