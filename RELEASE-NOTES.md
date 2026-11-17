# Cadence skill: source-verified release candidate

The `skills/cadence/` folder contains a portable Agent Skills workflow for creation and execution. It has been aligned with Cadence source commit `411544960c68a0ddfd4153ce073340a2cf99d36b`. It has not been authenticated against production or tested through a fresh end-user installation. Cadence application source has not been modified.

## Confirmed choices

- MCP endpoint: https://cadence.codecapsules.io/mcp (Streamable HTTP; OAuth or agent bearer token).
- Dependencies: obra/superpowers and anthropics/skills → frontend-design.
- Estimated effort: Fibonacci story points, within Cadence's integer 1–999 field.
- Token effort: measured execution usage at reviewed PR, including review fixes; leave ticket In Review.
- Portable skill core; host-specific setup and capabilities. Full functionality cannot be guaranteed for every agent that merely reads skills.

## What currently works in the server

Identity/project discovery, sprints/backlog, ticket search/read/create/update, assignees, swimlane movement, comments/subtasks, effort, and tokenEffort. The skill uses real tool inputs and version handling. It resolves human ticket keys to UUIDs, verifies project membership, and distinguishes comment tokensSpent from cumulative ticket tokenEffort.

## Gaps requiring Cadence product/MCP changes for the full requested experience

| Capability | Current source | Required change |
| --- | --- | --- |
| Custom New ticket template | No project field/tool | Persist project template and return it through MCP |
| Sprint enable/disable | No project setting | Add and expose flag; retain backlog behavior |
| Epics | No model/tool/ticket field | Add project feature flag, epic listing, and ticket association |
| Structured dependencies | No relationship model/tool | Add prerequisite relations, read/write support, and cycle handling |
| Repository mapping | Not exposed | Persist or document verified project/repository association |
| Direct ticket metadata | get_ticket omits projectId and URL | Return both; optionally allow exact key lookup |
| Backlog filter | REST has a sentinel; MCP requires UUID | Expose a null/backlog filter in MCP |
| Exact token telemetry | Storage exists; no host usage collector | Add per-host runtime collection and attributable execution records |
| Retry safety | No create/comment idempotency keys | Add keys or run IDs for retry-safe writes |

The skill handles the inspected version honestly: default template, sprint/backlog question, no unsupported epic mutation, explicit textual prerequisites, verified key resolution, and source-derived canonical ticket links. These fallbacks do not implement the missing product features.

## Workflow improvements

- Restored explicit engineering requirements from the original build guide: architecture/reuse investigation; success/failure/edge-case and risk planning; SOLID/DRY/KISS and focused code; validation/error/security handling; observed RED → GREEN → REFACTOR; test integrity; root-cause debugging; real-app and console/network checks; internal review; and automated/manual/criterion re-verification after corrections. These requirements now live directly in the execution reference rather than being implied by a dependency.
- Restored actionable human testing instructions, staged-file checks, independent assessment of external review findings, and an evidence-based engineering completion gate.
- Prepare branch/worktree before saving the plan; use safe isolated worktrees for unrelated dirty work.
- Preserve existing co-assignees when assigning the current user.
- Separate outcome-focused tickets from execution plans.
- Preserve the human implementation approval gate; avoid repeated placement and planning questions already answered.
- Apply frontend-design within existing design systems and ticket scope.
- Plan dependent branches/PRs explicitly; execution order alone does not integrate prerequisite code.
- Reconcile writes and review triggers after interruptions; count three review rounds across resumptions.
- Report engineering completion separately from missing telemetry. Never invent token numbers or confuse effort with value.

## Installation and distribution

See [INSTALL.md](INSTALL.md). The core skill is distributed in this Git repository. Provide host-specific plugin packaging only for hosts whose manifests/install flows have been tested. The included Codex agents/openai.yaml declares the verified MCP dependency; that declaration does not authenticate users or install other skills universally.

An existing `.claude/skills/cadence/SKILL.md` in the app covers broader project-management operations. Do not install both under the same name without an intentional replacement/rename decision. The new skill is narrower: create/build/setup. The older skill also says an omitted sprint filter means backlog, which contradicts the current server; this candidate corrects that behavior.

## Behavioral release checks

1. Unknown project asks; supplied project validates; UUID from another project is rejected.
2. Ticket key search picks exact key, handles pagination, and deduplicates key/UUID aliases.
3. Omitted sprint in a list is not mistaken for backlog. Creation sends explicit effort.
4. Missing capability is reported; default template is used only for the known unsupported-template version or a confirmed absent configuration.
5. Vague product requirements clarify; routine implementation choices stay in planning.
6. Timed-out creation/comments reconcile before retry; version conflicts re-read.
7. Co-assignees survive assignment; denied assignment is not bypassed.
8. Dependency cycle/out-of-set prerequisite/unmerged prerequisite is surfaced.
9. Existing work is preserved; plans are saved only on an isolated ticket branch.
10. No approval means no commit/push/PR; PR failure means no In Review transition.
11. Pending reviews are not passed; resumed work does not duplicate triggers; no fourth round.
12. Final reviewed head matches current head. Review fixes receive appropriate verification/reapproval.
13. Missing token telemetry remains unset; per-turn and cumulative usage are not added twice.
14. Reviewed PR records measured tokenEffort without moving to Done or merging.

Structural validation and local reference checks passed. These are not live end-to-end tests. Before publishing, run the cases above in a test Cadence project/repository and verify each supported host's installation, authentication, skill discovery, and usage telemetry.
