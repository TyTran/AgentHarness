# AgentHarness

**Local-first, portable engineering workflow for AI coding agents.**

**Project status: planning and bootstrap. No working CLI has been implemented yet.**

AgentHarness helps a coding agent take a scoped development task from intake through implementation, verification, review and safe completion. It prioritises clear Markdown instructions and a small number of deterministic guardrails rather than a heavy orchestration platform.

## Design principles

- **Local first:** Markdown task files, Git branches/worktrees, local tests, evidence and review work offline. GitHub is optional.
- **Portable:** the workflow is independent of the coding-agent vendor. Codex is the initial implementation target.
- **Safe by default:** changes stay task-scoped; tests and approvals are recorded, never assumed. Human review is the initial approval default.
- **Recoverable:** interrupted, blocked and failed tasks remain inspectable and resumable.
- **Small:** no mandatory daemon, hosted backend, database or dashboard.

## Planned workflow

Task -> Claim -> Implement in isolated workspace -> Verify -> Review -> Deliver -> Complete.

The same lifecycle will support a local file provider and an optional GitHub provider. Git is required for code history and diffs; GitHub is not.

## Start here

- [MVP scope and acceptance criteria](docs/MVP.md)
- [Architecture and workflow contracts](docs/ARCHITECTURE.md)
- [Workflow audit and extraction boundaries](docs/WORKFLOW_AUDIT.md)
- [Codex Cloud setup and handoff](docs/CODEX_CLOUD.md)
- [Coding agent instructions](AGENTS.md)
- [Implementation issues](https://github.com/TyTran/AgentHarness/issues)

**No installation command is currently available.** We will document real, tested commands once the first executable local workflow is built.

## Milestone

Prove an end-to-end local-only Codex task in a clean sandbox Git repository, with no GitHub credentials or network dependency. Add the GitHub workflow adapter and a second-agent portability test afterward.

## Security and scope

The design is informed by experience with a separate private application. This public repository must contain only generalised patterns and synthetic examples, not private source, internal infrastructure or credentials. The reference application remains unchanged.

## Licence

[Apache-2.0](LICENSE).
