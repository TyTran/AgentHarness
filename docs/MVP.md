# AgentHarness v0.1 delivery plan

**Current state:** specifications bootstrapped; no functioning CLI, installers or adapters are implemented yet.

## Objective

Demonstrate a repeatable, trustworthy **local-only Codex task lifecycle** in a fresh sandbox Git repository without GitHub credentials, any network request or a service running in the background. Ship working behavior and tests before exposing a package installation command.

## Acceptance criteria

1. A user can initialise AgentHarness in an existing Git repository without clobbering existing files or Git state; an uninstall/rollback path is documented.
2. A local Markdown task can be created, read, claimed, transitioned, blocked and resumed with a validated ID, clear owner, timestamps and history. Malformed data fails clearly; tasks remain understandable without the CLI.
3. Two competing claims cannot both acquire the same task; a killed or stale agent can be recovered by explicit operator action without destroying its unfinished changes.
4. A local task uses a dedicated safe Git branch/worktree, or accepts a documented **already isolated** cloud-agent workspace. An unsafe/dirty checkout cannot be overwritten.
5. Configured deterministic verification runs locally; a failing or unexecuted required check cannot be recorded as passing. Results are tied to the checked Git revision.
6. A reviewer can inspect changes and record approval or rejection for the exact reviewed revision. Default `human-review` is enforced; changing the diff invalidates prior approval.
7. A task reaches `complete` only after verified checks, required approval, explicitly chosen delivery, durable evidence and safe cleanup. Errors and interrupted tasks remain recoverable.
8. A scripted end-to-end scenario works **offline**, in a new sandbox project, demonstrating success plus failure/recovery; tests cover race conditions, invalid transitions, path safety and stale evidence.
9. Installation and CLI documentation are tested against real commands. No claim of package publication before publication occurs.

## Scope guardrails

**In v0.1:** Codex guidance, portable workflow, local file task store, Git checkout safety, local checks/evidence/review, explicit approval, basic CLI, end-to-end tests, small docs.

**Not in the first vertical slice:** GitHub adapter, Claude Code adapter, autonomous scheduler, GUI, SaaS, remote database, distributed execution, AI model API, blanket code auto-merge.

The GitHub integration and second-agent portability test are immediate follow-ups once the local vertical slice is proven. They must not require rewriting the core.

## Implementation sequence

### Stage 1 — Minimal executable foundation
- Decide a small Node.js CLI packaging/runtime target (prefer Node.js built-ins; document compatibility).
- Write tests for config loading, task ID/schema validation, state transition guards and non-destructive init.
- Deliver basic CLI help and one command that makes a real valid task. No fake commands or illustrative CLI output presented as existing functionality.

### Stage 2 — Local workflow and workspace
- Durable local task transitions and shared atomic claim/lease with explicit recovery.
- Safe local Git branch/worktree ownership or verify pre-provisioned cloud workspace.
- Scope all actions to a selected task and repository; do not mutate other user worktrees.

### Stage 3 — Actual proof and approval
- Run configured commands with timeouts and bounded redacted output.
- Bind evidence and local review to exact Git revisions; enforce default human approval.
- Prevent completion on missing proof, rejected review, unsupported delivery or dirty/unsafe checkout.
- Provide explicit recovery and clean-up commands that do not discard unfinished changes.

### Stage 4 — Vertical slice and release readiness
- Execute offline sandbox test: create -> claim -> implement -> verify -> review -> approve -> deliver -> complete.
- Execute adverse cases: failed checks, unapproved change, changed HEAD after approval, racing agents, abrupt interruption, dirty Git state.
- Document actual installation, CLI reference, threat model and limitations; run CI without network-dependent product services.

### Stage 5 — Subsequent adapters
- GitHub Issues/PR/CI adapter with no new lifecycle states.
- A second coding agent used in a real test on another project; agent-specific guidance must stay isolated.

## Definition of done

v0.1 does not pass until a person unfamiliar with the repository can follow the documented setup, run the offline scenario, inspect evidence and recover from an interrupted task. If only the happy path works, it is not complete.

## Decision posture

- Choose the narrowest structure that satisfies tests; avoid interface factories, plugin registries or config formats without a concrete use.
- All releases and completion claims must reflect observed proof.
- The private reference application remains unchanged and is **not a test target**.
