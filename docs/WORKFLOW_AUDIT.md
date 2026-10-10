# Workflow audit: portable engineering patterns

**Scope:** design-level audit based on the current documented workflow of a separate private application project (reviewed October 2026). This document deliberately does not reproduce private code, proprietary product contracts, identifying machine paths or configuration details. Documented rules are **not** proof that every behavior currently runs automatically.

## Observed useful patterns

| Pattern | Reusable principle | AgentHarness interpretation |
| --- | --- | --- |
| Bounded tasks with clear outcome, acceptance and proof | Agents need explicit finish conditions | Canonical task record, review/evidence |
| One task-focused branch / workspace | Avoid clobbering concurrent changes | Safe Git branch/worktree or provided cloud checkout |
| Progress tracked through implementation and review | Work state must be recoverable | Durable lifecycle and claims |
| Verification before handoff | A PR/diff alone is not completion | Executed checks, recorded revision/results |
| Explicit merge authority | Automation must not infer permission | Policy-backed human vs on-green gate |
| Validation of documentation impact | Operational behavior needs current docs | Lightweight doc impact declaration/check |
| Failed CI investigated and repaired rather than ignored | Completion depends on delivery health | Bounded retries with visible blocked states |
| Issue reconciliation and cleanup after delivery | Avoid orphaned work or premature closure | Explicit completion and safe teardown |

## What not to transplant

- GitHub Issues, Project board, PRs or GitHub Actions as **required infrastructure**: they are provider implementations, not the core.
- Product-specific release gates, deployment environments, code ownership, complex CI suites and templates.
- Machine-specific worktree paths, private coding skills, secrets or user identities.
- Project-specific label catalogs and test device pools.
- A previously **retired autonomous-supervisor experiment**. It is not an active or proven subsystem and must not be implicitly restored.
- Any assumption that instructions alone mechanically enforce a safety property.

## Gaps the new implementation must test

1. Can claims and state changes survive interrupted sessions and races across worktrees?
2. Is the local-only review/completion flow useful without a pull request?
3. Does approval become invalid after a diff changes?
4. Can an agent running in a cloud-provided branch use the harness without a nested Git worktree?
5. Are logged proof and docs grounded in executed checks, with missing evidence made explicit?
6. Can a second agent follow the contract without relying on Codex-specific conventions?

## Extraction rule

Translate **behaviors**, do not copy private implementation. Use synthetic tasks and a new sandbox repository as the first proof target. Do not modify or wire CI against the private application.
