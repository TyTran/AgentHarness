# Instructions for coding agents working on AgentHarness

## Purpose and read order

Build a small, reliable, **local-first** reusable coding-agent workflow. Start by reading:

1. `README.md` for philosophy and project status.
2. `docs/MVP.md` for current scope, acceptance criteria and delivery order.
3. `docs/ARCHITECTURE.md` for lifecycle/adapter boundaries.
4. The specific GitHub issue or local task you were asked to implement.
5. `docs/CODEX_CLOUD.md` when operating in Codex Cloud.

Documents describe **intended design**, not existing software. Never claim an unimplemented command works.

## Non-negotiable boundaries

- Do **not** change the private application repository used as historical reference. It is read-only; do not require it to build or test AgentHarness.
- Do not copy private source, internal paths, secrets, account details, deployment configurations or proprietary examples into this public repository. Use generic, synthetic fixtures.
- Local mode must remain fully functional offline without a GitHub login, network access, running daemon, hosted database or external service.
- GitHub integration must be an optional adapter. The portable task lifecycle cannot import GitHub-specific terminology, identities or APIs as hard dependencies.
- Codex is the first supported agent, not the definition of the core contract. Keep human-readable task instructions vendor-neutral and isolate agent-specific guidance.
- Keep the implementation small: prefer Node.js built-ins and explicit data structures; justify dependencies. Do not introduce a server, scheduler, dashboard, database or fleet orchestration in v0.1.
- Fail closed when verification evidence, required approval, or workspace safety is missing. Log failure and provide a recovery path. Do not hide errors or fabricate results.
- Do not execute untrusted project-supplied commands without explicit configuration/consent. Never print secrets in logs or evidence.
- Approval policies are configurable; initial **human approval is the safe default**. Never infer unattended merge authorisation from a generic request to implement a feature.

## Working discipline

- One bounded implementation issue/task per change. Write an explicit outcome, acceptance checks, exclusions and any approval dependency before coding.
- **Respect the checkout provided by your host.** In Codex Cloud, work on the task's provided branch/workspace. Do not assume you can create a nested worktree or need to do so. The local harness should separately implement safe worktree creation for ordinary local Git usage.
- Do not modify unrelated paths or commit generated temporary files, credentials or test artifacts.
- Add deterministic tests for any lifecycle, concurrency, path safety, adapter or verification logic.
- When modifying behavior or config, update relevant documentation and examples in the same change.
- Run the narrow tests during implementation, then the complete required validation before handoff. Record exact commands and outcomes; if a test cannot run, identify the blocker.
- Before review, show changes, proof, limitations, security considerations and any decision the owner needs to make.
- Do not auto-merge pull requests or deploy from this repository without explicit project-owner authorisation. Avoid direct implementation commits to `main` after initial repository bootstrap.

## Definition of done for implementation tasks

- Scope meets the linked task's explicit acceptance criteria.
- Tests demonstrate both successful path and failure/recovery path; no simulated green evidence.
- Local-only flows never call a network provider.
- Required review/approval and change record are present.
- Docs reflect actual working commands and supported limitations.
- Agent leaves task status accurately reported (blocked, review-ready or complete), including unfinished work.

## Architecture decisions

Follow `docs/ARCHITECTURE.md`; propose amendments in the task/PR if experiments invalidate assumptions. Prefer a tested narrow vertical slice over broad interfaces with no working implementation. Do not resurrect any retired autonomous supervisor from the private reference project.
