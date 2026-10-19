> 이 문서는 [multi-phase-plan.md](./multi-phase-plan.md)를 한국어로 번역한 문서입니다. 원본과 내용이 다르면 원본을 기준으로 합니다.

### 여러 단계 또는 여러 PR로 이루어진 계획

**당신이 맡는 것은 코드가 아니라 계획입니다. 계획은 오너가 상자를 하나씩 처리하고 운영자가 증거를 보고 감사하는 체크리스트입니다.** 계획 자체가 산출물입니다. 구현하지 않습니다.

1. 변경이 파일 한두 개이고 접근 방식이 명확하면 계획을 건너뜁니다. 그렇다고 말하고 멈춥니다.
2. 작성하기 전에 열린 질문을 프로토타입으로 해결합니다. 각 질문마다 `playbooks/prototype.md`를 실행합니다. 브랜치, SHA, 스크린샷은 부록 A에 쓰기 위해 보관합니다. 어떤 실행으로도 결론 낼 수 없는 제품 판단이나 선호 판단에 대해서만 운영자에게 묻습니다. 선택지를 제시합니다(**never-block-on-the-human** 원칙 스킬).
3. `subagent_type: "poteto-agent"`와 Subagents 섹션에 따른 명시적인 모델을 지정한 서브에이전트에서 탐색합니다(**guard-the-context-window** 원칙 스킬). Subagents 섹션에 따라 각 서브에이전트의 추론 수준과 fast 또는 standard 속도를 작업에 맞게 고릅니다. 각 서브에이전트는 파일 위치, 컨벤션, 테스트 명령어, 진입점을 반환합니다. 내용을 통째로 붙여 넣지 않습니다.
4. 아래 골격을 계획 파일에 복사하고 모든 자리 표시자를 채웁니다. 운영자가 경로를 지정하지 않으면 에이전트 저장소의 `docs/` 아래에 파일을 작성합니다. 모든 제목과 모든 하위 블록을 표시된 순서대로 유지합니다. PR 하나마다 섹션 하나를 둡니다. PR 하나는 자체 증거를 갖춘 변경 하나입니다(**sequence-verifiable-units** 원칙 스킬). **How to read this**에 실행 플레이북 이름을 적습니다. `playbooks/autopilot-full.md`와 `playbooks/autopilot-stack.md` 중 하나를 `playbooks/autopilot-stack.md` 끝에 있는 규칙에 따라 고릅니다. 상시 운영되는 프로그램은 `playbooks/orchestrate.md`를 사용합니다.
5. `/technical-writing`에 따라 전체를 작성한 다음 `/unslop`을 실행합니다. 본문은 Diátaxis 모드 중 하나인 how-to로 씁니다. 부록에는 설명과 참조 내용을 담습니다. 각 제목은 작업이나 발견 사항을 서술합니다. 긴 대시를 쓰지 않습니다. 문장 중간에 콜론을 쓰지 않습니다.
6. `node pstack/skills/poteto-mode/scripts/check-plan.mjs <plan.md>`를 실행하고 출력되는 모든 줄을 수정합니다(**encode-lessons-in-structure** 원칙 스킬).
7. 결과를 넘깁니다. 계획 경로와 스크립트 출력을 게시한 다음 멈춥니다. 실행은 운영자가 명시적으로 진행을 지시한 뒤에, 계획이 지정한 실행 플레이북에 따라 시작합니다.

**검증.** 테스트만으로는 충분한 검증이 되지 않습니다. PR은 unit, live, perf 상자가 모두 체크되었을 때만 검증된 것입니다(**prove-it-works** 원칙 스킬). 이 문장이 검증 규칙입니다. 모든 검증 블록은 이 문장으로 시작합니다. live 블록은 필수입니다. PR head에서 레인 열 개가 **swarm** 스킬에 따라 `swarm workers` 모델(기본값 `grok-4.7-xhigh-fast`)로 제어 스킬을 통해 실제 화면을 구동합니다. 각 레인은 구체적인 시나리오, 저장할 스크린샷, 통과 조건을 갖춘 상자 하나입니다. 레인 하나는 **트렁크 대비 회귀 레인(Regression lane against trunk)** 입니다. 이 레인은 핵심이 되는 동일한 시나리오를 트렁크와 head에서 실행합니다. 트렁크에 해당 기능이 없으면, 레인은 트렁크 결과를 지어내지 않고 그 사실을 기록한 뒤 diff가 추가하는 동작과 사용자가 기다리는 최종 상태를 대신 검증 기준으로 삼습니다. perf 게이트는 양쪽을 모두 측정합니다. 트렁크와 head 모두 지정된 지표를 산출해야 합니다. 트렁크에 기능이 없으면 diff가 추가하는 작업도 따로 분리하고, 그 작업과 사용자가 기다리는 종단 간 상태에 대해 절대 예산을 정합니다. 서로 다른 시나리오 사이의 비율을 주장하지 않습니다. perf 블록에는 지표, 교차 실행하는 프로브, 먼저 측정한 트렁크 기준값, 실패로 판정하는 수치를 담은 규칙을 적습니다. 상호작용을 바꾸는 PR은 리뷰 게이트를 거칩니다. 운영자가 병합 전에 채팅에서 스크린샷과 동영상으로 리뷰합니다. 상호작용을 바꾸지 않는 PR은 `**Review gate.** None. <PR id> is not review-gated.`라고 쓰고 그 아래에 상자를 두지 않습니다.

**제어 스킬.** 화면 종류에 따라 고릅니다. 브라우저, Electron, 웹 UI는 `cursor-team-kit`의 `control-ui`를 사용합니다. CLI와 TUI는 `cursor-team-kit`의 `control-cli`를 사용합니다. 네이티브 모바일은 저장소에 있는 시뮬레이터 구동 스킬을 사용합니다. 두 화면을 건드리는 PR은 양쪽 모두에 레인을 둡니다. 제어 스킬이 없는 화면은 부록 C에 위험으로 적고, 그 경우에도 live 블록에는 각 레인이 화면을 어떻게 구동하는지 적습니다.

````markdown
# <Program> plan

<Under ten lines. What changes, for whom, the rule the program enforces, and the PR ids in order.>

## How to read this

One box is one unit of work. Every box names the evidence that checks it. A nested box is a sub-step of the box above it. Check a box only when its evidence exists, a file, a log line, a screenshot, a test run, or a SHA. The body is a how-to. The appendices explain and record.

The program runs `pstack/skills/poteto-mode/playbooks/<execution playbook>.md`. <Who merges, and which PR ids are the operator's items that stop at merge-ready.>

Tests alone are not sufficient verification. A PR is verified only when its unit, live, and perf boxes are all checked.

## Program checklist

### Arm the program

- [ ] State the protocol and this plan to the operator, then stop. Start execution only on the operator's explicit go.
- [ ] Read these from trunk at program start. Re-read them at every tick.
  - [ ] `git show origin/main:pstack/skills/poteto-mode/playbooks/<execution playbook>.md`
  - [ ] `git show origin/main:pstack/skills/swarm/SKILL.md`
  - [ ] `git show origin/main:<control skill path>`
  - [ ] `git show origin/main:pstack/skills/poteto-mode/playbooks/opening-a-pr.md`
  - [ ] `git show origin/main:pstack/skills/<each other leaf skill the program uses>`
- [ ] On the operator's go, arm the audit tick as `/loop 1h` with the tick prompt below. Never leave the cadence to memory.
- [ ] Use this tick prompt, verbatim. "Re-read the execution playbook from trunk. Audit the operation against it and fix drift in this tick. Probe every active lane and judge progress by side effects only. Stand down a stuck lane and dispatch its replacement now. Then post a short status message to the operator in chat only when the audit found a tracked change that no earlier status message reported, such as a PR opened, a code-ready head, a round launched or closed, a verdict, a merge, a stuck agent and the action taken, a blocker added or cleared, or a decision only the operator can make. Name every such change and nothing else. Do not repeat a table, the merged list, or an unchanged blocker. If the audit found none, end the turn with no reply text. Either way, log this tick's row in your decision trail. The row names the items reported, or none."
- [ ] On the operator's hold or stand-down, send every owner a zero-writes order at once.

### Spawn owners

- [ ] Spawn one owner per PR with the full lifecycle the execution playbook names.
- [ ] Follow this dependency graph. Start dependent work only after its parent merges, or base it on the parent branch when the execution playbook stacks.
  - [ ] <PR id> and <PR id> are independent and first. Both branch from `main`.
  - [ ] <PR id> after <PR id>.
- [ ] Hold the file boundaries. <PR id or class> touches only `<glob>`.
- [ ] Hold the review gate. <PR ids> change an interaction. They wait for the operator's review in chat with screenshots and a video before merge.

### PR mechanics, for every PR

- [ ] Resolve the forge once. Default to `gh`; if `command -v origin` succeeds and Origin can resolve the repository, use `origin pr` for every PR operation. Record any fallback to `gh`. Never require `gt`.
- [ ] Open the PR ready, never draft, per **Opening a PR**. Use the run's built-in PR tool when it has one, else `origin pr create --status open --base <base-branch>` or `gh pr create --base <base-branch>` according to the resolved forge. A stack child targets its parent branch.
- [ ] Run the repo's lint and typecheck once before the PR-facing push. Push with hooks on.
- [ ] Run `/deslop` before each commit and `/no-comments` before review.
- [ ] Triage every Bugbot and security-reviewer comment per `../references/bugbot-triage.md`.
- [ ] Rebase onto current trunk before the code-ready report and babysit. Keep that merge base in fix rounds. Rebase again only at merge prep, on a `git merge-tree` conflict with trunk, or on a CI failure that comes from a change on trunk.

### Verdict and merge, for every PR

- [ ] A full swarm runs once, at the code-ready head. Another full swarm runs only for new behavior, a redesign, or a large new diff. A rebase, a merge-conflict resolution, or a minor fix gets a single verifier at the new head. Run each full swarm per `pstack/skills/swarm/SKILL.md`. One gates lane. The ten live lanes from the PR's **Verify, live** block. The perf lane from its **Verify, perf** block. Two or more audit lanes, each with its own focus, that read the diff and the receipts and distrust the PR body. The root audits the receipts in the merge-ready report before the verdict.
- [ ] Clean only when every lane is `PASS`. Findings go back to the owner, including a defect that a lane filed as a note. A new head gets a fresh verdict, except for results that stay valid under the patch-id rule in `playbooks/shipping.md`.
- [ ] <The merge or append rule from the execution playbook, with the patch-id rule from `playbooks/shipping.md`.>

### Boot recipe, for every live lane

Each live lane runs on its own cloud VM at the PR head. Drive through `control-ui` or `control-cli` from `cursor-team-kit`.

- [ ] `git fetch origin <head-branch> && git checkout <head SHA>`.
- [ ] <Start the backend and the surface. Wait for ready.>
- [ ] <Deliver input only through the control skill's commands. Name the read-only diagnostics.>
- [ ] Save every screenshot to `/tmp/swarm-<pr-id>/worker-<n>/<slug>.png` and return the paths with the report.

## <Task as a verb phrase> (<PR id>)

**Depends on.** <PR id, or None.>

**Files.**

- [ ] Edit `<path>`.
- [ ] Create `<path>`.
- [ ] Delete `<path>`.

**Build.**

- [ ] <One change. Name the symbol and the file.>

**You see.**

- [ ] <One observable result, with the exact log line or screen state.>

**Verify, unit.** Tests alone are not sufficient verification. A PR is verified only when its unit, live, and perf boxes are all checked.

- [ ] <Test file and the case it gains.> Run `<command>`.

**Verify, live.** Tests alone are not sufficient verification. A PR is verified only when its unit, live, and perf boxes are all checked. Ten lanes on `<swarm workers model>` at the PR head, per the boot recipe.

- [ ] Lane 1. Regression lane against trunk. Run <the same load-bearing scenario> at trunk and head. If trunk lacks the feature, record that and gate <the behavior the diff adds plus the end state the user waits for>. Save `<slug>.png`. Pass when <predicate>.
- [ ] Lane 2. <Scenario.> Save `<slug>.png`. Pass when <predicate>.
- [ ] Lane 3. <Scenario.> Save `<slug>.png`. Pass when <predicate>.
- [ ] Lane 4. <Scenario.> Save `<slug>.png`. Pass when <predicate>.
- [ ] Lane 5. <Scenario.> Save `<slug>.png`. Pass when <predicate>.
- [ ] Lane 6. <Scenario.> Save `<slug>.png`. Pass when <predicate>.
- [ ] Lane 7. <Scenario.> Save `<slug>.png`. Pass when <predicate>.
- [ ] Lane 8. <Scenario.> Save `<slug>.png`. Pass when <predicate>.
- [ ] Lane 9. <Scenario.> Save `<slug>.png`. Pass when <predicate>.
- [ ] Lane 10. <Scenario.> Save `<slug>.png`. Pass when <predicate>.

**Verify, perf.** Tests alone are not sufficient verification. A PR is verified only when its unit, live, and perf boxes are all checked.

- [ ] Metric. <What is measured at both trunk and head. If trunk lacks the feature, also name the diff-added work and the end-to-end state the user waits for.>
- [ ] Probe. <The command or procedure, run at trunk and at the head, interleaved. Both sides must produce the metric.>
- [ ] Baseline. Record the trunk <value> first.
- [ ] Rule. <Head against trunk, with the number that fails. If the scenarios differ, add absolute budgets for the diff-added work and the user-visible end state instead of an invalid ratio.>

**Review gate.** The operator reviews before merge.

- [ ] Copy lane <n> screenshots into `<media path>/<pr-id>-review-<slug>.png`.
- [ ] Record a 30 to 60 second video of the change on a lane VM. Save it as `<media path>/<pr-id>-review.mp4`.
- [ ] Post the screenshots and the video in chat. Stop at merge-ready. Wait for the operator's click.

**Merge.**

- [ ] Root's clean verdict at the exact head SHA.
- [ ] Bugbot triage done.
- [ ] Rebased onto current trunk after the verdict, patch-id unchanged.
- [ ] <The owner squash-merges its own PR, or the root appends it to the base-branch stack and the operator lands it bottom-up.>

## Close the program

- [ ] Every box above is checked with its evidence.
- [ ] Reply to the operator with the report the execution playbook names.

## Appendix A. Prototype evidence

<Each open question a prototype answered, with the branch, the SHA, and the artifact links. Each question that stays unproven.>

## Appendix B. Alternatives rejected

<Each approach weighed and why it lost.>

## Appendix C. Risks

<Each risk with the PR it lands in and what the owner watches.>

## Appendix D. Links and reading list

<Docs to read before editing. Which PRs get `pstack/skills/how/SKILL.md` and `pstack/skills/interrogate/SKILL.md`. The trail per `pstack/skills/show-me-your-work/SKILL.md`.>
````

**답변:** 계획 경로, 의존 관계를 포함한 PR id 목록과 리뷰 게이트 대상 집합, 프로토타입이 증명한 것과 증명되지 않은 채 남은 것, 그리고 검사 스크립트의 출력을 적습니다.
