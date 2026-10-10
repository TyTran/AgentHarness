# AgentHarness architecture (design contract, not implementation)

## System boundary

AgentHarness is a workflow kit and lightweight CLI, **not** a standalone autonomous coding agent or always-on orchestrator. The agent executes implementation; AgentHarness supplies durable task context, state transitions, workspace safety, verification evidence and review/approval gates.

The core must work in an existing **local Git repository with network disabled**. Hosting, agent runtimes and external issue trackers are optional integrations.

```text
   Coding agent (Codex first; others later)     Human operator
                   |                                  |
                   v                                  v
       Agent-facing Markdown instructions     CLI / task records
                   \                          /
                    v                        v
               Canonical workflow / policy
               - task state & transitions
               - claim / retry / recovery
               - proof & approval checks
               - audit and task completion
                     |             |
             Task provider     Change provider
              /        \         /        \
      Local files    GitHub*   Local Git    GitHub PR*
                     (* optional hosted integration)
```

Do not make an adapter call required for constructing or using the core.

## Canonical lifecycle

```text
ready -> claimed -> implementing -> verifying -> review -> complete
            |          |              |         |
            +----------+--------------+---------+--> blocked
                                  |
                    failed checks --> implementing
blocked -- explicit resume/reclaim --> last safe eligible state
ready / blocked -- deliberate decision --> cancelled
```

- **ready**: scoped, schedulable task with explicit acceptance criteria.
- **claimed**: actor identified; claim stored with timestamp/ownership; prevents silent competing execution.
- **implementing**: isolated workspace created or a host-provided task workspace verified.
- **verifying**: required checks being executed; capture actual command, working directory, exit code, timestamp, revision and log reference.
- **review**: evidence assembled, diff/base known and approval policy evaluated.
- **complete**: every required check and approval is satisfied, change delivered according to selected mode, task outcome recorded and cleanup safe.
- **blocked**: unmet prerequisite, unsafe workspace, failed external dependency or explicit human gate; store reason and recovery action. Failed tests normally return to implementation, not `complete`.
- **cancelled**: deliberate terminal outcome with audit reason; never silently delete abandoned work.

Transitions should be explicit, idempotent where possible, and reject invalid state changes. Retry/reclaim of stale work must not destroy unmerged code. Evidence cannot be reused after the underlying commit/diff or verification inputs change.

## Provider boundaries

Implement only methods justified by a working vertical slice. Conceptual responsibilities:

| Responsibility | Local implementation | Optional GitHub implementation |
| --- | --- | --- |
| Task lookup, creation, claim, transition | Versioned local files and atomic claims | GitHub Issues + status mapping |
| Isolation, base comparison and delivery | Local Git branches/worktrees, diff, merge rules | Git branches + pull requests |
| Verification and evidence | Configured local commands + durable summary | Local results + required CI checks |
| Approval | Local review decision with actor/timestamp/base/head | PR review / repository policy |

These are **capability contracts**, not commitments to build a complex interface hierarchy. The CLI should delegate to the selected provider and expose the same state machine. Choose exactly one authoritative task provider per project; do not attempt automatic two-way syncing in v0.1.

## Local persistence proposal

Prefer human-readable repository files:

```text
.agentharness/
  config.json                 # selected provider, checks, approval policy
  tasks/TASK-0001.md          # task identity, metadata and body
  evidence/TASK-0001.json     # machine-readable verification/review summary
```

Structured task metadata can use a deliberately restricted frontmatter format or a separately validated JSON schema, with Markdown for human-facing task content. Choose one in the initial task-store implementation and document the exact contract and schema. Do not invent a full YAML parser.

- Version **task definitions and final evidence** in Git. Keep verbose logs, claims/locks and temporary artifacts out of source control by default; reference retained logs honestly when possible.
- Protect concurrent local claims using an atomic operation in a **shared repository-level location**, not a worktree-local lock. Check ownership before mutation and use explicit stale-claim recovery.
- Avoid ambiguous cross-branch writes: identify which checkout owns authoritative task records and serialise mutations. A versioned audit record must survive workspace cleanup.
- Redact credentials and user-controlled secrets in recorded commands/logs; use output size limits and allow logging to be disabled for sensitive checks.
- Never interpret task-body text as shell commands.

These details are a v0.1 design starting point; test concurrency, crash recovery and multi-worktree behavior before freezing storage layout.

## Git workspace contract

- For **ordinary local mode**, create a dedicated task branch/worktree from a known clean base. Refuse dirty or ambiguous base states; do not overwrite or clean user changes.
- For **Codex Cloud and other pre-isolated hosts**, detect/accept the host-provided workspace. Do **not** force nested worktrees, mutate another checkout or assume permissions to access the host's base checkout.
- Record task ID, base revision and current head; refuse completion on changed/unreviewed diffs.
- Delivery is distinct from implementation. Local mode should support a documented approved merge or review-ready handoff; cleanup only after delivered and verified. Never silently merge on an unknown target.
- Worktree removal must verify ownership, clean/safe state and preservation of unmerged work.

## Verification & review contract

Verification output is **observed evidence**, not narrative assertion. Check commands are project-configured and executed with timeout and bounded output; no dynamic shell text from task records. Verification results include required vs optional classification and checked revision. Failure blocks completion.

Review includes a readable diff or diff reference, documented risks, required approvals and a decision linked to the **exact reviewed revision**. Default policy is `human-review`. An explicit `on-green` policy may allow local completion of low-risk scoped work once all gates pass; elevated-risk actions still require deliberate human approval. Never implement the policy merely as unchecked prose.

## Security model and limitations

This is a workflow and guardrail system, **not a sandbox**. Agent instructions and CLI checks do not prevent an independently privileged agent from running arbitrary commands. Do not claim to provide execution isolation/security boundaries beyond what Git, the OS, and the hosting platform actually enforce. Explicitly document trust assumptions and command-execution risk.

## Deferred design

- Autonomous task scheduler, background supervisor and self-repair daemon.
- Dashboard, hosted database, distributed queue, central multi-agent coordination.
- Automatic provider sync, deployment automation, platform-specific releases.
- Multi-agent parallel orchestration; support for a second agent is a portability **test**, not a fleet manager.

See [MVP](MVP.md) for acceptance criteria.
