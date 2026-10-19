> 이 문서는 [worktree-cleanup.md](./worktree-cleanup.md)을 한국어로 번역한 문서입니다. 원본과 내용이 다르면 원본을 기준으로 합니다.

### 워크트리와 시뮬레이터 정리

1. 현재 상태를 기록하고 점검합니다. `df -h /` 결과를 기록한 다음 `scripts/worktree-audit.sh [-b <pr-base-ref>] [repo-path]`를 실행합니다(principle-build-the-lever). `-b`가 없으면 `origin/HEAD`를 기준으로 `MERGED`를 판단하므로, PR이 다른 브랜치를 대상으로 한다면 `-b origin/<branch>`를 넘깁니다. 이 스크립트는 경로를 직접 입력하지 않고 `git worktree list`에서 읽습니다. 직접 입력한 `myrepo-worktrees/x`로는 `.cursor/worktrees/myrepo/x`에 있는 워크트리를 놓치기 때문입니다(principle-encode-lessons-in-structure). 스크립트는 각 워크트리를 크기, 생성 후 경과 시간, 머지 상태, 커밋되지 않은 작업, PR 상태, 그 워크트리를 마지막으로 사용한 채팅을 기준으로 분류하고, 분류 버킷을 제안합니다. 트랜스크립트 스캔은 느리므로 백그라운드에서 실행합니다.
2. 버킷은 권고일 뿐 허가가 아닙니다. 실제 근거는 고정(pinned)된 채팅과 진행 중인 채팅입니다(principle-prove-it-works). 그 목록을 사용자나 사이드바에서 받아 모든 후보와 대조합니다. 이 도구가 사용자가 고정해 둔 워크트리를 `safe`로 표시한 적이 있으므로, 고정된 목록을 우선합니다.
3. 삭제하기 전에 사용 여부를 검증합니다. 모든 `verify-recent-chat` 행과 확신이 서지 않는 항목에 대해, 서브에이전트를 병렬로 분산해 트랜스크립트를 읽게 하고 해당 채팅이 고정되었는지 또는 진행 중인지, 어떤 워크트리를 사용하는지 보고하게 합니다(principle-guard-the-context-window, 트랜스크립트는 분량이 많습니다). 고정된 채팅은 백그라운드 서브에이전트를 통해 arena와 재현용 트리를 인접한 워크트리에 만들며, 이런 워크트리는 이름이 사이드바에 전혀 나타나지 않아도 사용 중입니다. Subagents 섹션에 따라 각 서브에이전트의 추론 수준과 fast 또는 standard 속도를 작업에 맞게 고릅니다.
4. 되돌릴 수 없는 손실 앞에서는 멈춥니다. `wip:N`은 추적 중인 파일에 커밋되지 않은 수정이 N개 있다는 뜻입니다. 먼저 diff를 보여 주고 결정을 받습니다. 깨끗한 워크트리는 삭제해도 브랜치에서 복구할 수 있지만, 커밋되지 않은 작업은 사라지기 때문입니다. `scratch:N`은 추적되지 않는 임시 파일이므로 삭제해도 안전하지만, 파일 이름은 밝힙니다. Autonomy 규칙에 따라 깨끗하고, 머지되었고, 사용 중이 아닌 워크트리는 그대로 진행합니다. `wip`이거나 사용 중인 워크트리는 멈추고 확인합니다.
5. 확인된 목록을 정리합니다. 경로마다 `git worktree remove --force <path>`를 실행합니다. 무시된 빌드 산출물 때문에 디렉터리가 남아 있으면 `rm -rf`로 지운 다음 `git worktree prune`을 실행합니다. 브랜치 ref는 남아 있으므로 커밋은 사라지지 않습니다. `df -h /`와 목록 재조회로 결과를 확인합니다.
6. 시뮬레이터와 그 밖에 공간을 확보할 수 있는 대상을 정리합니다. 보통 시뮬레이터가 그다음으로 큰 공간을 확보해 줍니다. `xcrun simctl --set testing delete all`(XCTestDevices 복제본), `xcrun simctl delete unavailable`, 그리고 오래된 런타임에는 `xcrun simctl runtime list` 다음에 `runtime delete <id>`를 실행합니다. 필요하면 더 정리할 수 있습니다. Xcode `DerivedData`와 `iOS DeviceSupport`, `~/Library/Application Support/Cursor`(`state.vscdb.backup`, 그리고 작업 공간으로 연 폴더 이름을 딴 `<root>`가 크게 불어나는 `snapshots/roots/<root>`), 패키지 캐시(pnpm, uv, brew, yarn)가 대상입니다. 사용자가 남겨 두라고 하지 않은 캐시만 지웁니다.

이 플레이북은 실수를 잡아 줄 코드 리뷰 없이 사용자 상태를 삭제하는 유일한 플레이북이므로, 위의 확인 절차가 곧 리뷰 역할을 합니다.

**응답:** 정리 전후의 `df -h /` 결과와 확보한 공간, 정리한 워크트리, 그리고 남겨 둔 각 워크트리에 대한 한 줄 사유(어떤 채팅이 사용 중인지, 또는 커밋되지 않은 작업이 있는지)를 보고합니다.
