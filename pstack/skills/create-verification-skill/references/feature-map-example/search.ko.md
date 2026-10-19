# 노트 검색

> 이 문서는 [search.md](./search.md)를 한국어로 번역한 문서입니다. 원본과 내용이 다르면 원본을 기준으로 합니다.

검색 기능으로 사용자는 제목이나 본문 텍스트로 노트를 찾고, 일치하는 노트를 살펴보고, 일치 항목이 없는 경우와 검색을 사용할 수 없는 경우를 구별할 수 있습니다.

## 하위 기능

- `search-open`은 지원하는 브라우저의 각 진입점에서 검색을 엽니다.
- `search-match`는 노트 데이터를 바꾸지 않고 제목과 본문이 일치하는 항목을 반환합니다.
- `search-open-result`는 결과 하나를 노트 편집기에서 엽니다.
- `search-empty`는 일치 항목이 없는 검색어에 대해 완전한 빈 상태 화면을 보여 줍니다.
- `search-clear`는 검색어를 지우고 최근 노트 화면을 복원합니다.
- `search-cli`는 터미널에서 같은 일치 노트를 반환합니다.

## 접근 방법 (사용자 관점)

- 브라우저 툴바에서 `Search` 버튼을 선택합니다.
- 편집 가능한 필드 밖에 포커스가 있을 때 브라우저에서 `/`를 누릅니다.
- 터미널에서 `notes search <query>`를 실행합니다.

## control-notes로 조작하기

사전 조건:

- Notes가 `http://127.0.0.1:4173`에서 정상적으로 동작합니다.
- 일회용 데이터 디렉터리에 본문 텍스트가 `Draft budget`인 `Quarterly plan`이 들어 있습니다.
- `control-notes doctor`가 예상한 URL과 데이터 디렉터리를 보고합니다.

- **툴바 진입.** `Search` 버튼을 선택합니다. `control-notes browser click --role button --name "Search"`를 실행합니다. `Search notes`라는 이름의 대화 상자가 나타나고 검색 상자에 포커스가 있습니다.
- **키보드 진입.** 대화 상자를 닫고 페이지에 포커스를 둔 다음 `/`를 누릅니다. `control-notes browser press --key "/"`를 실행합니다. 같은 대화 상자가 나타나고 페이지에 슬래시가 입력되지 않습니다.
- **제목 일치.** `quarterly`를 입력합니다. `control-notes browser fill --role searchbox --name "Search notes" --value "quarterly"`를 실행합니다. `Search results` 목록에 `Quarterly plan`이 있고 `Grocery list`는 없습니다.
- **본문 일치.** 검색어를 `budget`으로 바꿉니다. `control-notes browser fill --role searchbox --name "Search notes" --value "budget"`을 실행합니다. `Quarterly plan` 결과가 본문 일치 발췌문과 함께 계속 표시됩니다.
- **결과 열기.** `Quarterly plan`을 선택합니다. `control-notes browser click --role link --name "Quarterly plan"`을 실행합니다. 대화 상자가 닫히고 편집기 제목에 `Quarterly plan`이 표시됩니다.
- **빈 상태.** 검색을 다시 열고 `volcano`를 입력합니다. `control-notes browser fill --role searchbox --name "Search notes" --value "volcano"`를 실행합니다. 검색이 끝나면 `No matching notes`라는 이름의 상태가 나타납니다.
- **검색어 지우기.** `Clear search`를 선택합니다. `control-notes browser click --role button --name "Clear search"`를 실행합니다. 검색 상자가 비고 결과 목록 자리에 `Recent notes` 영역이 표시됩니다.
- **CLI 일치.** 터미널에서 검색합니다. `control-notes cli -- notes search "quarterly" --format json`을 실행합니다. 종료 코드는 `0`이고 stdout에 제목이 `Quarterly plan`인 객체 하나가 들어 있습니다.
- **CLI 불일치.** 없는 값을 검색합니다. `control-notes cli -- notes search "volcano" --format json`을 실행합니다. 종료 코드는 `0`이고 stdout은 `[]`입니다.
- **증거.** 결과가 채워진 상태를 캡처합니다. `control-notes browser snapshot --aria --path artifacts/search/results.aria.txt`와 `control-notes browser screenshot --path artifacts/search/results.png`를 실행합니다. 두 산출물 모두에서 Notes, 검색어, `Quarterly plan`을 확인할 수 있습니다.

## 주의할 점

- 편집기나 검색 상자에 포커스가 있을 때 `/`를 누르면 검색이 열리지 않고 텍스트가 입력됩니다.
- 결과는 짧은 디바운스 후에 갱신됩니다. 고정된 시간만큼 기다리지 말고 결과 목록이나 빈 상태 표시가 나타날 때까지 기다립니다.
- 사용자가 `Include archived`를 켜지 않으면 보관된 노트는 결과에서 제외됩니다.
- CLI는 기본적으로 사람이 읽기 쉬운 형식으로 출력합니다. 안정적으로 검증하려면 `--format json`을 사용합니다.
- 결과를 열면 브라우저 상태가 바뀝니다. 다른 검색어를 증명하기 전에 검색을 다시 엽니다.
