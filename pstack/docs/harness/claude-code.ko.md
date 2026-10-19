# Claude Code에서 pstack 사용하기

> 이 문서는 [claude-code.md](./claude-code.md)를 한국어로 번역한 문서입니다. 원본과 내용이 다르면 원본을 기준으로 합니다.

pstack 스킬은 Cursor 이름을 사용합니다. Claude Code에서는 세션마다 이 파일을 한 번 읽은 다음, 스킬에 나오는 각 Cursor 이름을 아래 표에 따라 대응시킵니다. `<pstack>`은 `skills/`와 `docs/`를 담고 있는 폴더를 뜻합니다. 어떤 Cursor 필드에 해당하는 행이 없으면 그 필드를 빼고, 뺐다는 사실을 알립니다. 도구 스키마가 거부하는 필드나 값은 절대 전달하지 않습니다.

## 도구

| Cursor | Claude Code |
| --- | --- |
| `Task` | `Agent` |
| `AskQuestion` | `AskUserQuestion`입니다. `-p` 모드에서는 이 도구가 없으므로 일반 텍스트로 질문합니다. |
| todolist 열기 | 세션이 `TodoWrite`를 제공하면 그것을 사용합니다. 그렇지 않으면 목록을 응답 안에 두고 단계마다 갱신합니다. |
| 다른 pstack 스킬 실행(`/name` 또는 "**name** 스킬") | `<pstack>/skills/<name>/SKILL.md`를 `Read`로 읽고 그 내용을 따릅니다. `Skill` 도구는 대부분의 pstack 스킬을 거부합니다. 사용자에게 직접 입력하라고 안내할 때는 `/pstack:<name>`으로 적습니다. |
| `Grep`, `Glob` | 세션에 `Grep`이나 `Glob`이 없으면 `Bash`에서 `rg`를 사용합니다. |

## 서브에이전트 필드

| Cursor | Claude Code |
| --- | --- |
| `subagent_type: generalPurpose` | `subagent_type: "general-purpose"` |
| `poteto-agent`, `comment-sicko` | `pstack:poteto-agent`, `pstack:comment-sicko` |
| `readonly: true` | `subagent_type: "Explore"`입니다. 이 유형은 `Bash`를 그대로 가지고 있으므로, 브리프에 "파일을 편집하지 마세요"라고도 적고 작업이 끝난 뒤 `git status`를 확인합니다. |
| `readonly: false` | `general-purpose`입니다. 이 유형은 세션의 MCP 서버를 그대로 사용합니다. |
| `environment: "cloud"` | `isolation: "worktree"`입니다. 워커가 체크아웃할 브랜치나 커밋을 명시합니다. |
| `environment: "local"` | 생략합니다. |
| `cloud_base_branch: <b>` | 워커에게 먼저 `<b>`를 체크아웃하라고 지시합니다. |
| `run_in_background: true` | 생략합니다. 서브에이전트는 기본적으로 백그라운드에서 실행되며, 작업이 끝나면 알려 줍니다. |
| `resume` | 새 에이전트를 띄우고 이전 결과를 그 브리프에 붙여 넣습니다. |
| `file_attachments` | 브리프에 경로를 나열합니다. |

## 모델

| Cursor | Claude Code |
| --- | --- |
| `claude-opus-5-5-xhigh`와 같은 `claude-<family>-<version>-<effort>[-fast]` | `model: "opus"`와 `effort: "xhigh"`입니다. `model`에는 `opus`, `sonnet`, `haiku`, `fable`만 들어갈 수 있습니다. `-fast`는 버립니다. |
| `grok-*`, `gpt-*` 또는 그 밖의 Claude가 아닌 슬러그 | 에이전트가 세션 모델로 실행되도록 `model`을 생략하고, 생략했다는 사실을 알립니다. |
| `auto` 또는 `inherit-parent` | `model`을 생략합니다. |
| `pstack-models.mdc` 규칙, `~/.cursor/rules/pstack-models.mdc` | `~/.claude/rules/pstack-models.md`입니다. Claude Code는 새 세션마다 이 파일을 불러오므로, 역할 줄이 이미 컨텍스트에 들어 있습니다. 들어 있지 않으면 파일을 읽습니다. 파일이 없거나 해당 역할 줄이 없으면 스킬의 기본 슬러그를 사용합니다. |
| `opus high`와 같은 `<model> <effort>` 형식의 역할 값 | `model: "opus"`와 `effort: "high"`입니다. |

## setup-pstack

Claude Code는 새 세션마다 `~/.claude/rules/` 안의 모든 `.md` 파일을 불러오므로, `setup-pstack`은 규칙을 그곳에 작성합니다. 다음과 같이 바꾸어 그 단계를 따릅니다.

- 감지. 감지된 모델은 `Agent` 도구 스키마가 나열하는 `model` 값이며, 각 모델에는 `effort` 값 중 하나가 붙습니다. 실제 모델은 `opus xhigh`처럼 `<model> <effort>` 형식으로 적습니다. effort가 마지막 토큰이므로 예산 단계는 적힌 그대로 동작합니다.
- 기본값. 위의 행에 따라 각 기본 슬러그를 대응시킵니다. `claude-opus-5-5-xhigh`는 `opus xhigh`이고, `grok-4.7-xhigh-fast`는 `inherit-parent`입니다.
- 작성. frontmatter 없이 `~/.claude/rules/pstack-models.md`를 작성하고, 이어서 주석 줄, `# budget` 줄, 그리고 5단계에서 정한 역할마다 한 줄씩 적습니다. 파일 전체를 덮어씁니다. 역할 줄은 절대 `CLAUDE.md`나 `AGENTS.md`에 넣지 않습니다.

## 경로

| Cursor | Claude Code |
| --- | --- |
| `.cursor/skills/`, `~/.cursor/skills/` | `.claude/skills/`, `~/.claude/skills/` |
| `~/.cursor/plugins/`, `.cursor-plugin/plugin.json` | `~/.claude/plugins/`, `.claude-plugin/plugin.json` |
| `.cursor/rules/*.mdc`, 항상 적용되는 규칙 | `CLAUDE.md`이며, `CLAUDE.md`가 없으면 `AGENTS.md`입니다. Claude Code는 `.mdc` 파일을 절대 읽지 않습니다. 모델 규칙은 예외입니다. 모델 섹션을 참고하세요. |
| `https://github.com/cursor/plugins/blob/main/pstack/<path>` | 해당 파일이 있으면 `<pstack>/<path>`입니다. 플러그인에는 스킬, `docs/guide/`, `README.md`가 함께 들어 있습니다. 로컬 사본을 읽고 절대 원격에서 가져오지 않습니다. 사용자에게 링크가 필요하면 URL을 알려 줍니다. |
| `~/.cursor/projects/<slug>/agent-transcripts/` 안의 트랜스크립트 | `~/.claude/projects/<slug>/<session>.jsonl`입니다. 슬러그는 워크스페이스 경로에서 각 `/`와 `.`을 `-`로 바꾼 것이므로, `/Users/you/proj`는 `-Users-you-proj`가 됩니다. 그 폴더 바로 아래에 있는 가장 최근 `.jsonl`이 현재 세션입니다. 그 세션의 `<session>/subagents/` 폴더에는 서브에이전트 트랜스크립트가 들어 있습니다. 이 워크스페이스의 폴더만 읽습니다. |

## Cursor 전용 기능

Claude Code에는 Cursor Automations, Bugbot, Cursor 클라우드 에이전트, 내장 PR 도구가 없습니다. 어떤 단계에 이 중 하나가 필요하면 응답에 `Skipped in Claude Code: <step>. <reason>.`이라는 한 줄을 넣고 다음 단계로 넘어갑니다. 멈추거나, 질문하거나, 재시도하지 않습니다. 건너뛴 단계가 만들었어야 하는 URL이나 키 같은 결과물이 이후 단계에 필요하면, 자리 표시자를 남기고 그 줄에서 이름을 밝힙니다.

| Cursor | Claude Code |
| --- | --- |
| Cursor Automations: 루틴, `update_state` 루틴 호출, `api2.cursor.sh/automations` 웹훅, 루틴 깨우기, `SendToUser` 시크릿 요청 | 위의 한 줄을 남기고 건너뜁니다. |
| Bugbot | GitHub PR에 달린 Bugbot 댓글은 일반 리뷰 스레드이므로, 해당 단계가 제시하는 forge 명령으로 분류합니다. PR에 Bugbot 체크나 댓글이 없으면 Bugbot을 시작하거나, 기다리거나, 설정하는 단계는 건너뜁니다. |
| Cursor 클라우드 에이전트 | 서브에이전트 필드에서 설명한 것처럼 `environment: "cloud"`는 워크트리에 대응합니다. 대시보드 상태나 전용 머신처럼 클라우드 에이전트 자체가 필요한 단계는 건너뜁니다. |
| 내장 PR 도구 | `gh pr create`처럼 해당 단계가 제시하는 forge 명령을 사용합니다. 이것은 건너뛰기가 아니므로 한 줄을 적지 않습니다. |
