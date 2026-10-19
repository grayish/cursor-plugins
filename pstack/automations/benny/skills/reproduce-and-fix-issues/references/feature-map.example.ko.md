# Feature map 예시

> 이 문서는 [feature-map.example.md](./feature-map.example.md)를 한국어로 번역한 문서입니다. 원본과 내용이 다르면 원본을 기준으로 합니다.

Benny가 재현할 수 있는 모든 사용자 대상 기능을 매핑합니다. 앱을 조작하기 전에 관련 섹션을 읽습니다. 이 맵은 사용자 관점으로 유지합니다. 내부 구조와 현재 코드 경로는 여기에 고정해 두지 말고 런타임에 파악합니다.

이 파일을 `.cursor/automations/benny/` 바깥(예: `.cursor/benny/feature-map.md`)에 복사하고, `control.feature_map_path`를 그 복사본으로 설정합니다. 팩을 새로 고칠 때 이 파일을 덮어써서는 안 됩니다.

## 기능별 템플릿

### `<feature name>`

`<one-line user-visible purpose>`

#### 사용자가 도달하는 방법

- 클릭 경로: `<screen> -> <menu, tab, or panel> -> <control>`
- 키보드 단축키: `<shortcut or none>`

#### Control adapter가 조작하는 방법

- `<inputs>`를 사용한 `<adapter action>`은 `<visible result>`를 만들어야 합니다.
- 초기화: `<how the adapter returns to a fresh state>`.

#### 안정적인 selector

- `<role and accessible name>`
- `<ARIA relationship>`
- `<data-component or purpose-named data attribute>`

생성된 CSS 또는 StyleX 클래스, 동적 해시, 자식 인덱스, 깨지기 쉬운 DOM 위치는 절대 사용하지 않습니다.

#### 점검할 상태

- Default, hover, focus-visible, active, disabled
- Loading, empty, error
- Selected, open, expanded
- `<relevant feature-specific variants>`

해당되지 않는 상태는 표시해 둡니다.

#### 전제 조건과 준비

- 인증: `<account state>`
- 데이터: `<fixture>`
- 권한: `<role>`
- 플래그: `<flag or none>`
- 서비스: `<required availability>`

#### 증거와 교차 확인

- 스크린샷: `<app identity, feature, and discriminating state>`
- 동영상: `<entry path, interaction, and final state>`
- 교차 확인: `<read-only state or value that confirms the UI>`

#### 주의할 점

- `<known dead end or wrong surface>`
- `<safe environment translation>`

## 가상 예시

이 기능들은 가상의 작업 관리 앱에 속합니다. 이것들은 예시이며 Benny에 필수인 기능이 아닙니다.

### 로그인

사용자가 작업 관리 앱에 들어갈 수 있게 합니다.

#### 사용자가 도달하는 방법

- 앱을 열고 `Sign in`을 선택합니다. 단축키는 없습니다.

#### Control adapter가 조작하는 방법

- `open_app`, `click Sign in`, `fill credentials`, `click Continue`를 실행하면 항목 목록이 열려야 합니다.
- 로그아웃하고 일회용 세션을 지워 초기화합니다.

#### 안정적인 selector

- 버튼 `Sign in`, 텍스트 상자 `Email`과 `Password`, `data-component="sign-in-form"`

#### 점검할 상태

- Default, focus-visible, submitting, disabled, loading, error

#### 전제 조건과 준비

- 일회용 계정과 사용 가능한 인증 서비스

#### 증거와 교차 확인

- 랜딩 페이지부터 항목 목록까지 녹화합니다. 읽기 전용 세션 상태를 확인합니다.

#### 주의할 점

- 마케팅 페이지는 잘못된 화면입니다. 인증 서비스가 없으면 막힌 상태입니다.

### 항목 목록과 상세

사용자가 항목을 둘러보고 하나를 열 수 있게 합니다.

#### 사용자가 도달하는 방법

- `Items` 탭을 연 다음 행 하나를 선택합니다.

#### Control adapter가 조작하는 방법

- `select_tab Items`와 `click <fixture item>`을 실행하면 해당 항목의 상세가 열려야 합니다.
- 상세를 닫고 선택을 해제해 초기화합니다.

#### 안정적인 selector

- 이름이 `Items`인 탭과 목록, fixture 이름이 붙은 행, `data-component="item-detail"`

#### 점검할 상태

- Loading, empty, error, selected, open, expanded

#### 전제 조건과 준비

- 이름이 지정된 fixture 항목, 읽기 권한, 사용 가능한 항목 서비스

#### 증거와 교차 확인

- 선택 상태와 일치하는 상세 제목을 보여 줍니다. 선택된 항목 ID를 확인합니다.

#### 주의할 점

- 검색 결과는 비슷해 보일 수 있지만 다른 경로를 사용합니다.

### 항목 편집기

사용자가 항목을 만들거나 편집할 수 있게 합니다.

#### 사용자가 도달하는 방법

- 상세에서 `Edit`를 선택하거나 목록에서 `New item`을 선택합니다.

#### Control adapter가 조작하는 방법

- `click Edit`, `fill <field>`, `click Save`를 실행하면 상세가 갱신되어야 합니다.
- fixture를 복원해 초기화합니다.

#### 안정적인 selector

- 버튼 `Edit`, `New item`, `Save`, 폼 `Item editor`, label과 연결된 필드

#### 점검할 상태

- Default, focus-visible, dirty, validating, disabled, saving, error, success

#### 전제 조건과 준비

- 편집 가능한 fixture, 쓰기 권한, 사용 가능한 저장 서비스

#### 증거와 교차 확인

- 필드 변경부터 갱신된 상세까지 보여 줍니다. 저장된 항목 값을 읽기 전용으로 확인합니다.

#### 주의할 점

- 폼 상태를 주입하지 않습니다. 읽기 전용 상세 필드는 편집기가 아닙니다.

### 설정

사용자가 개인 환경설정을 바꿀 수 있게 합니다.

#### 사용자가 도달하는 방법

- 프로필 메뉴를 연 다음 `Settings`를 선택합니다.

#### Control adapter가 조작하는 방법

- `open_menu Profile`, `click Settings`, `toggle <preference>`를 실행하면 컨트롤이 갱신되어야 합니다.
- 시작 시점의 환경설정을 복원해 초기화합니다.

#### 안정적인 selector

- 버튼 `Profile`, 메뉴 항목 `Settings`, 영역 `Settings`, 목적이 드러나는 이름의 환경설정 속성

#### 점검할 상태

- Closed, open, selected, focus-visible, disabled, loading, error

#### 전제 조건과 준비

- 로그인된 테스트 계정, 알려진 환경설정, 사용 가능한 환경설정 서비스

#### 증거와 교차 확인

- 메뉴 경로와 최종 컨트롤 상태를 보여 줍니다. 환경설정 값을 읽기 전용으로 확인합니다.

#### 주의할 점

- 운영체제 설정은 다른 화면입니다.

## 완성도 체크리스트

- 재현 가능한 모든 사용자 대상 기능에 섹션이 있습니다.
- 모든 섹션에 사용자 경로, adapter 동작, 초기화 방법이 적혀 있습니다.
- selector는 role, 이름, ARIA, 안정적인 컴포넌트 마커, 목적이 드러나는 이름의 속성을 사용합니다.
- 생성된 클래스나 DOM 위치를 사용하는 selector가 없습니다.
- 관련된 상호작용, loading, empty, error, selected, expanded 상태를 다룹니다.
- 인증, fixture, 권한, 플래그, 서비스가 명시되어 있습니다.
- 스크린샷, 동영상, 근거가 되는 교차 확인 요구 사항이 명시되어 있습니다.
- 잘못된 화면, 막다른 경로, 안전한 환경 변환이 나열되어 있습니다.
- 구현 세부 사항은 런타임에 파악할 대상으로 남겨 둡니다.
