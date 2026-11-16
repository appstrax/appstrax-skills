# Execute tickets

## Intake and ordering

Parse comma-separated keys or UUIDs, trim whitespace, and deduplicate after resolving to UUIDs while preserving order. Resolve keys and verify project membership as described in [the MCP contract](mcp-contract.md). Read descriptions, criteria, relevant comments, subtasks, and any exposed attachments/dependency links. The current MCP lacks attachment and structured dependency tools; use explicit relationships in the ticket/request and report gaps that affect implementation. Do not claim a complete dependency graph from incomplete data. Do not reopen closed/completed tickets without clarification.

Order requested tickets by dependency, preserving input order among independent tickets. Report cycles, inaccessible dependencies, and prerequisites outside the requested set. Do not automatically implement unrequested prerequisites. A prerequisite is usable only when its required code is available in the dependent ticket's base—not merely because its ticket status changed.

Default to separate branches and PRs per ticket. If dependent code has not reached the configured base, explain the block and ask whether to wait for integration or use an explicit stacked-branch/PR arrangement. Never merge automatically to unblock a dependency. Independent requested tickets may proceed while another is blocked. Track approval, review rounds, and effort per ticket.

## Start and investigate

Confirm setup readiness and read [dependency integration](dependencies.md). Capture trustworthy per-ticket usage before substantive execution when available; do not fabricate a baseline. Retrieve the authenticated Cadence user and ensure they are assigned, preserving existing co-assignees. Because `assign_ticket` replaces the list, send the union of existing assignees and the authenticated user; skip if already present. Reconcile concurrent changes and verify afterward. If permissions prevent assignment, request a manager's help rather than bypassing it. Move to the existing In Progress swimlane and verify it. If identity or lane mapping cannot be resolved, stop the affected ticket.

Inspect architecture, relevant code, reusable components, APIs/data models, similar behavior, repository instructions, and tests. Use the ticket as the functional specification; clarify material gaps rather than inventing requirements. Read relevant installed Superpowers planning skills and the configured UI/UX skill for interface work.

## Branch before persisting the plan

Inspect Git status before repository edits. Preserve existing uncommitted work. If unrelated work is present, use a separate clean worktree from the configured base when available; stop only when safe isolation is unavailable or the ticket requires uncommitted changes whose intended treatment is unclear. Do not overwrite, discard, or stash user work without authorization. Fetch the configured remote, then create and verify a descriptive ticket branch from the latest configured base (`origin/development` by default). Never work directly on the base branch, force-push, or rewrite published history.

Create an implementation plan before implementation: requirements and criterion mapping, technical approach and reuse, ordered tasks, meaningful tests, and material risks. For nontrivial work, persist it in the project's planning directory, otherwise `docs/plans/`. Keep the ticket's original body intact; link or summarize the plan separately through MCP where supported. Update the plan when discoveries change it. Proceed without a separate plan approval unless a material product/architecture choice needs the user.

## Implement and verify

Use relevant Superpowers implementation, TDD, and systematic-debugging guidance. Apply TDD where practical and add meaningful behavior/regression coverage. Follow project architecture and UI standards; avoid unrelated refactoring and dependencies. Scale verification to the change rather than creating tests that merely mirror implementation.

Run applicable automated tests, lint/type/build checks, and manual verification of the real user journey or backend behavior. Use the available supported browser tools for UI verification. Distinguish pre-existing failures from regressions. Record unavailable checks and limitations; never claim they passed.

Perform internal review and verification before completion using relevant Superpowers guidance. Fix valid defects and rerun affected checks. Map each acceptance criterion to PASS, FAIL, or BLOCKED with evidence. Known defects prevent a readiness claim; blocked verification remains explicitly blocked.

## Human testing gate

Before any commit, push, PR creation, or In Review transition, present the implemented result: branch, concise changes, checks actually run, criterion evidence, limitations, and practical local testing steps. Request explicit human testing and approval of this implementation. Explain that the Cadence build workflow requires this gate. Silence and automated checks are not approval.

If changes are requested, implement and verify them, then request approval of the updated result. Do not repeatedly ask for approval already given to unchanged work.

## Publish after approval

Review the final diff and commit only intended changes, with a ticket-referencing commit message. Push the feature branch without force. Create a PR against the configured target (`development` by default). The first line of the PR description must be `Ticket: ` followed by the canonical ticket URL. Prefer a server-returned URL; the inspected server omits it, so use only the source-verified route and MCP-verified IDs described in [the MCP contract](mcp-contract.md). Include a concise implementation and verification summary below it.

Verify the PR exists and targets the correct branch. Only then move the Cadence ticket to the existing In Review status and verify the update. Keep its assignment. If PR creation fails, reconcile GitHub before retrying and do not transition the ticket prematurely.

## External review: at most three rounds per ticket/PR

When the configured external review integration supports it, post the exact command `/code-review` as a new PR comment. This workflow authorizes that review request and technical replies to review findings. If the reviewer is unavailable, report BLOCKED rather than claiming a review occurred.

Record each trigger's comment ID, round, and head commit. Confirm submission before retrying uncertain writes. Monitor the triggered review in the active session with bounded waits. No feedback is not evidence of completion. If it cannot complete in-session, report PENDING and save enough state to resume without another trigger.

For completed reviews, inspect inline feedback, threads, and relevant checks. Fix valid findings, test, review, commit, and push corrections to the same branch. Explain rejected findings with evidence; do not repeatedly reply to identical rejected findings without new evidence. If corrections materially change approved functionality, obtain renewed human approval before committing/pushing those material changes.

After corrections are pushed, trigger the next review only if the previous review completed and fewer than three total triggers exist. Never trigger a fourth round. Fix remaining valid issues where practical after round three, but report that new commits have not received another external review.

Use these review outcomes: PASSED (completed review covers current head and no valid findings remain), PENDING, BLOCKED, or REVIEW LIMIT REACHED. Do not claim a clean review of an earlier commit applies to later changes.

## Checkpoint and report

Preserve the ticket ID, branch/base, PR URL, head commit, criterion evidence, approval scope, review trigger IDs/count, reviewed commit, finding dispositions, and usage checkpoint in project-appropriate resumable state. Do not store secrets. On resume, reconcile this state against Cadence, Git, and GitHub before acting. Do not commit tracking-only local state unintentionally.

Follow [token accounting](tokens.md) for Token effort. A missing usage number must remain explicitly unavailable; it is not zero.

Report actual ticket link/status/assignee, implementation summary, branch/PR, test and criterion evidence, approval state, external review outcome, token-accounting status, and remaining action. Use AWAITING HUMAN APPROVAL, IN REVIEW, REVIEW PASSED, REVIEW LIMIT REACHED, or BLOCKED as appropriate. Keep the report concise and link detailed evidence.

Never automatically merge or mark the ticket Done/Completed. Successful execution ends at a verified reviewed PR with required human approval; delivery/merge remains a separate lifecycle event. At REVIEW PASSED, write the measured cumulative execution usage into `tokenEffort`, including implementation and review corrections. Keep the ticket In Review. If telemetry is unavailable, report engineering completion separately from unavailable token accounting. For pending/blocked/review-limit outcomes, retain measured usage checkpoints for resume rather than claiming final effort.
