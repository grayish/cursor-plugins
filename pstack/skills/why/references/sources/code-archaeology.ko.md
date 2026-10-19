# 코드 고고학 (git + 저장소 내부)

> 이 문서는 [code-archaeology.md](./code-archaeology.md)을 한국어로 번역한 문서입니다. 원본과 내용이 다르면 원본을 기준으로 합니다.

## 이 소스에 담긴 내용

- 커밋 이력(메시지, 날짜, 작성자, diff)
- PR 설명, 리뷰 댓글, 토론 스레드(`gh`로 조회)
- 인라인 코드 주석, TODO, FIXME, 지원 중단(deprecation) 메모
- 저장소에 ADR(architectural decision record, 아키텍처 결정 기록)이 있다면 ADR
- 테스트: 테스트 이름과 단언(assertion)에는 변경의 계기가 된 엣지 케이스가 담겨 있는 경우가 많습니다
- 같은 커밋에서 함께 수정된 관련 파일(동시 변경 신호)
- 저장소 안의 CHANGELOG 항목과 릴리스 노트
- 커밋 메시지와 PR 본문에 있는 이슈/티켓 ID

코드와 직접 연결되어 있어서 가장 신뢰할 수 있는 소스이며, 가장 완전한 소스이기도 합니다. 저장소를 거쳐 간 모든 것이 여기에 있어야 합니다.

## 검색 방법

초기 커밋 목록을 확장합니다.

```bash
# History through renames
git log --follow --oneline -- <file>

# Pickaxe: commits that added or removed this exact text
git log -S '<exact_string_from_code>' -- <file>

# Or for patterns:
git log -G '<regex>' -- <file>

# Who wrote each line and when
git blame -L <start>,<end> <file>

# One commit's full diff
git show <hash>

# Commits between two points affecting this file
git log <old>..<new> -p -- <file>
```

커밋 이력과 별도로 존재하는 문서(out-of-band docs)를 찾습니다.

```bash
# ADRs often live in docs/adr/ or similar
rg -l -i 'architecture.decision' --glob '*.md'

# TODOs and FIXMEs near the target
rg -n -C2 '(TODO|FIXME|HACK|XXX|NOTE)' <target_file>

# Related tests
rg -l '<symbol>' --glob '*test*'
```
