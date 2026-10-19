> 이 문서는 [databricks.md](./databricks.md)를 한국어로 번역한 문서입니다. 원본과 내용이 다르면 원본을 기준으로 합니다.

# Databricks 분석 및 시스템 테이블

## 이 소스에 담긴 내용

Databricks는 제품 분석, 데이터 파이프라인, 웨어하우스 텔레메트리 계층입니다. Datadog을 보완합니다. Datadog이 *인프라/런타임* 관점이라면 Databricks는 *제품/데이터* 관점입니다(사용자가 무엇을 했는지, 어떤 실험이 실행되었는지, 기능 사용량이 어떻게 변해 왔는지, 임곗값 상수가 어디에서 왔는지를 보여 줍니다).

- **제품 분석 이벤트.** `your_warehouse.events.analytics_track_event`(원시 테이블)와 `<your_analytics_db>.<schema>.<table>`에 있는, 타입이 지정되고 중복이 제거된 이벤트별 dbt 모델입니다. 기능 호출, 클릭, 수락/거절, 제출, 클라이언트가 보고한 오류 같은 사용자 행동을 담고 있습니다.
- **사용량 및 과금 이벤트.** `your_warehouse.events.usage_event` / `<your_analytics_db>.<schema>.stg_usage_events`, `your_warehouse.events.raw_model_event` / `<your_analytics_db>.<schema>.stg_raw_model_events`입니다. 비용이나 사용량에 근거한 결정을 조사할 때 사용합니다.
- **실험 / feature flag 데이터.** 노출(exposure) 테이블과 결과(outcome) 테이블입니다. **스키마는 회사마다 다릅니다.** 이름을 가정하기 전에 `SHOW TABLES`로 확인합니다.
- **시스템 테이블.** `system.query.history`, `system.compute.warehouses`, `system.billing.*`, `system.access.audit`입니다. "이 쿼리는 비용이 컸는가?", "누군가 이 쿼리를 얼마나 자주 실행했는가?", "웨어하우스 부하가 언제 급증했는가?" 같은 질문에 답합니다.
- **dbt 계보(lineage).** `<your_analytics_db>.<schema>`에 있는 모델을 보면 어떤 파이프라인이 특정 테이블/필드에 의존하는지 알 수 있습니다. 상위(upstream) 변경이 소비자 코드 변경의 동기가 되는 경우가 많습니다.
- **Databricks 노트북.** 엔지니어가 코드를 변경하기 전에 작성한 탐색적 분석입니다. **SQL MCP로는 조회할 수 없습니다.** 근거가 노트북에 있다고 의심되면 공백(gap)으로 명시합니다.

## 검색 방법

Databricks SQL MCP를 사용합니다. 주요 도구는 `execute_sql_read_only`입니다. 이 도구가 `statement_id`를 반환하면 다시 실행하지 말고 `poll_sql_result`로 결과를 폴링합니다.

**쿼리하기 전에 구조를 파악합니다.** 스키마는 회사마다 다릅니다. 테이블 이름을 믿기 전에 먼저 확인합니다.

```sql
SHOW TABLES IN <your_analytics_db>.<schema> LIKE '*<keyword>*';
DESCRIBE TABLE <your_analytics_db>.<schema>.stg_<event>;
```

**모든 쿼리에 시간 범위를 지정합니다.** 이 테이블들은 매우 커서 조건 없이 스캔하면 시간 초과가 발생합니다. `_timestamp`(이벤트) 또는 `start_time`(`system.query.history`)으로 필터링하고, 배포 날짜를 앞뒤로 감싸는 기간을 사용합니다. 보통 앞뒤로 약 30일이며, 강한 이유가 있을 때만 더 넓힙니다.

**원시 테이블보다 타입이 지정된 dbt 모델을 우선합니다.** `<your_analytics_db>.<schema>.<table>`은 중복이 제거되어 있고, 타입이 지정되어 있으며, liquid clustering이 적용되어 있습니다. `your_warehouse.events.analytics_track_event`에는 중복이 있고 `properties_json`에 타입이 지정되어 있지 않습니다. 모델 이름 패턴은 `stg_<source>_<event_name_with_underscores>`이며, `<source>`는 `app`, `backend`, `website`, `cli` 중 하나입니다. 패턴만으로 정확한 모델 이름을 알 수 없으면 `SHOW TABLES`로 확인합니다. 아직 dbt 모델이 없거나 dbt 갱신 지연 구간 안의 이벤트가 필요할 때만 원시 테이블로 내려갑니다.

**타입이 지정된 dbt 모델의 컬럼 규칙** (이 규칙을 알면 `DESCRIBE`를 한 번 더 실행하지 않아도 됩니다):

- `_timestamp`, `_id`, `_auth_id`, `_request_id`, `event_name`. 모든 모델에 공통으로 있는 컬럼
- `properties_<name>`. 타입이 지정되고 밑줄 표기로 바뀐 이벤트 속성(`properties_entrypoint`, `properties_size_bytes`, …)
- `context_team_id`, `context_client_version`, `context_country`, `context_client_os`. 미리 추출된 클라이언트 컨텍스트

### 성과를 내기 쉬운 조사 패턴

대상에 맞는 테이블과 컬럼 조합을 고릅니다.

1. **이벤트 사용량 추이.** PR 병합 시점 전후 ±30일 동안 관련 `stg_*` 모델의 일별 건수를 확인합니다. 병합 후 하루이틀 안에 0에서 꾸준한 사용량으로 계단식으로 증가했다면, 그 PR이 기능을 출시했다는 강력한 정황 증거입니다. 0으로 서서히 줄어들었다면 지원 중단이나 삭제를 시사합니다.
2. **보호 장치 / 방어적 검사의 기원.** PR *이전* 14일 동안 관련 `properties_<name>` 컬럼의 분포(중앙값 / p99 / 최댓값)를 확인합니다. p99가 대상 코드의 임곗값 상수와 일치한다면 그 숫자가 데이터를 근거로 선택되었음을 시사합니다.
3. **실험 / feature flag 조회.** `SHOW TABLES ... LIKE '*experiment*'`로 노출 테이블을 찾은 다음, PR 날짜 무렵 관련 flag 키에 대한 변형(variant)별 노출 건수를 가져옵니다.
4. **마이그레이션, 백필, 성능 개선 재작성에 대한 쿼리 기록 증거.** `system.query.history`를 `statement_text ILIKE '%<table_or_symbol>%'`와 좁은 `start_time` 기간으로 필터링하면 변경의 동기가 되었을 가능성이 높은 비용이 큰 쿼리가 드러납니다(`total_duration_ms`로 정렬하거나 `SUM(read_bytes)`, `COUNT(*)`로 집계합니다).
5. **dbt 계보.** 대상이 `<your_analytics_db>.<schema>` 모델을 읽거나 그 모델에 쓴다면, 해당 모델 자체의 git 기록(이 저장소 안에 있습니다)에 근거가 담겨 있는 경우가 많습니다. 그 단서는 직접 추적하지 말고 git 조사자에게 넘깁니다.

## 이 소스에서 좋은 증거의 모습

위의 패턴 외에도 다음과 같은 증거가 있습니다.

- 방어적 코드 PR 이후 며칠 동안 오류를 분류하는 이벤트의 건수가 거의 0으로 떨어진 경우. 그 PR이 해당 오류 유형을 해결했음을 시사합니다
- 노출 테이블의 행이 대상 코드의 feature flag 키를 언급하고, PR 배포 날짜 무렵에 "shipped" / "concluded" 결정이 기록된 경우

## 흔한 함정

- **계측되었다고 해서 원인인 것은 아닙니다.** 이벤트가 존재한다는 것은 누군가 그것을 기록할 만큼 신경 썼다는 뜻일 뿐, 대상 코드가 그 이벤트 *때문에* 존재한다는 뜻은 아닙니다. 인과 관계를 주장하기 전에 git 조사자가 찾은 PR/커밋 인용과 짝지어 확인합니다.
- **조용한 계측 변경.** 이벤트 양이 계단식으로 변했다면 사용자 행동이 바뀐 것이 아니라 새 이벤트가 기록되기 시작한 것일 수 있습니다. 증가 추세를 기능 출시 신호로 해석하기 전에 같은 기간에 계측 관련 PR이 있었는지 확인합니다.
- **스키마 변화.** 이벤트 속성은 계속 바뀝니다. 오늘 타입이 지정된 dbt 모델에 있는 컬럼이 대상 코드가 작성될 당시에는 없었을 수 있습니다. 오래된 데이터에서는 해당 속성이 원시 `properties_json` 안에만 들어 있을 수 있습니다.
- **dbt 갱신 지연.** `<your_analytics_db>.<schema>.*`는 일정에 따라 다시 빌드됩니다(보통 매시간/매일). 최근 몇 시간 동안의 이벤트가 필요하면 `your_warehouse.events.*`로 대신 조회하고 `_id`로 중복을 제거합니다.
- **회사마다 다른 테이블.** 실험, feature flag, 과금, 사용량 테이블은 회사마다 다릅니다. 존재를 확인하지 않은 테이블에서 나온 결과를 보고하는 것은 전형적인 실패 유형입니다. 먼저 `SHOW TABLES` / `DESCRIBE TABLE`로 확인합니다.
- **보존 기간 한계.** 관련 기간이 테이블 보존 기간보다 이전이거나 dbt 모델 생성일보다 이전이라면, 그것은 결과 없음이 아니라 *공백*입니다. 종합자(synthesizer)가 "결과 없음"을 "활동 없음"으로 읽지 않도록 이를 명시적으로 밝힙니다.
- **노트북은 조회할 수 없습니다.** SQL MCP는 Databricks 노트북을 볼 수 없습니다. 근거가 노트북에 있다고 의심되면 공백으로 반환합니다.

## 반환할 내용

관련된 발견마다 다음을 포함합니다.
- 유형(제품 이벤트 / 실험 노출 / 사용량 또는 과금 이벤트 / 시스템 테이블 행 / dbt 모델)
- 완전한 테이블 이름과 실행한 정확한 쿼리
- 조회한 기간
- 간결한 수치 요약(건수, 백분위수, 처음/마지막으로 관측된 타임스탬프). **원시 행을 그대로 쏟아 내지 않습니다.**
- 대상 코드의 배포 날짜와의 시간적 상관관계(예: "첫 행 2024-08-15, PR #49074 병합 2024-08-14")
- 관련성과 강도: 직접 / 정황 / 약함
