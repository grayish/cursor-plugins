> 이 문서는 [sentry.md](./sentry.md)를 한국어로 번역한 문서입니다. 원본과 내용이 다르면 원본을 기준으로 합니다.

# Sentry 오류 이력

## 검색 방법

Sentry MCP를 사용합니다.

1. **방향을 잡습니다.** 프로젝트 슬러그와 조직을 모른다면 다음을 실행합니다.

   ```
   find_organizations
   find_projects
   ```

2. **대상과 관련된 이슈를 검색합니다.**

   ```
   search_issues (natural language, e.g., "errors in PaymentService timeout", "unhandled exceptions in uploadFile")
   ```

   대상 코드가 처리하는 예외 클래스 이름, 대상의 함수 또는 클래스 이름, 대상이 확인하는 오류 메시지 문자열, 대상의 파일 경로로 쿼리를 구성합니다.

3. **릴리스와 기간으로 범위를 좁힙니다.**

   ```
   search_issue_events (filter by release, time, environment, trace ID, tags)
   get_issue_tag_values (for an issue, see distribution across versions, users, environments)
   ```

   의심되는 이슈에 대해 **first seen**과 **last seen**(오류가 멈춘 시점이 대상의 배포 날짜와 맞아떨어지는지), **영향을 받은 릴리스**(어느 릴리스에서 발생했고, 어느 릴리스가 수정이었는지), **빈도 추이**(급증했다가 해결되었는지)를 확인합니다.

4. **맥락을 파악하기 위해 전체 이벤트를 가져옵니다.**

   ```
   get_sentry_resource (pass a Sentry URL or type+ID)
   ```

   스택 트레이스가 대상 코드를 거쳐 가는지 확인합니다. 태그와 브레드크럼이 대상이 방어하는 조건과 일치하는지 확인합니다.

5. **대상 근처에 반영된 릴리스를 확인합니다.**

   ```
   find_releases (around the commit date of the target)
   ```

   릴리스 버전을 PR의 머지 날짜와 대조합니다.

6. **Seer는 꼭 필요할 때만 사용합니다.**

   ```
   analyze_issue_with_seer
   ```

   Seer의 AI 근본 원인 분석은 가설을 만들어 내는 도구입니다. 권위 있는 결론이 아니라 추론으로 취급합니다. 실제 이벤트와 스택 트레이스가 1차 증거입니다.

## 반환할 내용

관련 있는 이슈마다 다음을 반환합니다.
- 이슈 ID와 제목
- 프로젝트와 조직
- first seen / last seen 타임스탬프
- 이벤트 수(알고 있다면 샘플링 비율도 함께)
- 영향을 받은 릴리스
- 대상과의 관련성을 보여 주는 대표 스택 트레이스 일부(요약이 아니라 원문 그대로 발췌)
- first/last seen 시점과 대상의 배포 날짜 사이의 상관관계
- 이슈 링크
- 작성자 댓글이나 해결 메모
