# Using Codex Cloud for AgentHarness

## Current status

This repository contains the initial brief, policies and scoped implementation plan. **It does not yet contain an implemented AgentHarness CLI.** Codex Cloud is the *development environment used to build it*, not a dependency of the finished product.

## Setup (operator action)

1. Open [Codex](https://chatgpt.com/codex) and connect/select the GitHub repository `TyTran/AgentHarness`.
2. If requested, grant Codex access to this repository; do not grant access to the separate private application merely to build this project.
3. Create a cloud environment for AgentHarness using the `main` branch. At bootstrap, the repo has only docs; no install or test script should be assumed to exist.
4. Start with [issue #2 — CLI foundation, project init and tests](https://github.com/TyTran/AgentHarness/issues/2), one bounded task at a time. Paste or reference that exact issue URL.
5. In each task, tell Codex to read `AGENTS.md`, `docs/MVP.md` and `docs/ARCHITECTURE.md` first. Request a task-scoped change, tests and reviewable PR/diff; review before merge.

## Suggested first cloud task

> Implement [AgentHarness issue #2](https://github.com/TyTran/AgentHarness/issues/2): the CLI foundation, safe project init and deterministic tests. Read AGENTS.md, docs/MVP.md and docs/ARCHITECTURE.md before coding. Keep the implementation small, local-first and fully offline-capable. Use the isolated workspace provided by Codex Cloud; do not create nested worktrees unless strictly needed. Do not touch the separate private reference repository. Add deterministic tests, run them, report the exact results and submit a reviewable change. Do not auto-merge.

When the issue #2 PR is reviewed and merged, follow the dependency order in the [v0.1 tracker](https://github.com/TyTran/AgentHarness/issues/1).

## Development and product separation

Codex Cloud tasks can use GitHub as the development host. That **does not** justify a GitHub dependency in the installed local AgentHarness product. Do not introduce GitHub APIs, tokens, or network checks in core tests.

## Limitations

Codex Cloud environment features, tool access and repository authorisation are managed in the Codex product and must be configured by the operator. These files and GitHub issues do **not** by themselves start a Codex Cloud job. Never claim a cloud task is running until its job is actually launched and confirmed.
