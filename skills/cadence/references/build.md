# Execute tickets

## Intake and ordering

Parse comma-separated keys or UUIDs, trim whitespace, and deduplicate after resolving to UUIDs while preserving order. Resolve keys and verify project membership as described in [the MCP contract](mcp-contract.md). Read descriptions, criteria, relevant comments, subtasks, and any exposed attachments/dependency links. The current MCP lacks attachment and structured dependency tools; use explicit relationships in the ticket/request and report gaps that affect implementation. Do not claim a complete dependency graph from incomplete data. Do not reopen closed/completed tickets without clarification.

Order requested tickets by dependency, preserving input order among independent tickets. Report cycles, inaccessible dependencies, and prerequisites outside the requested set. Do not automatically implement unrequested prerequisites. A prerequisite is usable only when its required code is available in the dependent ticket's base—not merely because its ticket status changed.

Default to separate branches and PRs per ticket. If dependent code has not reached the configured base, explain the block and ask whether to wait for integration or use an explicit stacked-branch/PR arrangement. Never merge automatically to unblock a dependency. Independent requested tickets may proceed while another is blocked. Track approval, review rounds, and effort per ticket.

## Start and investigate

Confirm setup readiness and read [dependency integration](dependencies.md). Capture trustworthy per-ticket usage before substantive execution when available; do not fabricate a baseline. Retrieve the authenticated Cadence user and ensure they are assigned, preserving existing co-assignees. Because `assign_ticket` replaces the list, send the union of existing assignees and the authenticated user; skip if already present. Reconcile concurrent changes and verify afterward. If permissions prevent assignment, request a manager's help rather than bypassing it. Move to the existing In Progress swimlane and verify it. If identity or lane mapping cannot be resolved, stop the affected ticket.

Before designing or implementing, investigate the codebase:

- Inspect repository structure, technology stack, architecture, and naming conventions. Read applicable `AGENTS.md`, `CLAUDE.md`, and project engineering instructions.
- Read the source affected by the ticket and existing implementations of similar behavior. Identify reusable components, services, utilities, and abstractions before proposing new ones.
- Trace relevant APIs, data models, state management, and integration points. Identify compatibility constraints and likely regression paths into related features.
- Inspect test infrastructure, fixtures, and conventions so new verification fits the project.

Use the ticket's business problem, expected behavior, scope, and acceptance criteria as the functional specification. Clarify material gaps rather than inventing requirements. Preserve existing behavior unless the ticket explicitly calls for a change. Read relevant installed Superpowers planning skills and the configured UI/UX skill for interface work.

## Branch before persisting the plan

Inspect Git status before repository edits. Preserve existing uncommitted work. If unrelated work is present, use a separate clean worktree from the configured base when available; stop only when safe isolation is unavailable or the ticket requires uncommitted changes whose intended treatment is unclear. Do not overwrite, discard, or stash user work without authorization. Fetch the configured remote, then create and verify a descriptive ticket branch from the latest configured base (`origin/development` by default). Never work directly on the base branch, force-push, or rewrite published history.

Create an implementation plan before code changes, covering:

- **Requirements:** the requested outcome, every acceptance criterion, and expected success, failure, and edge-case behavior.
- **Technical design:** approach, important architectural decisions, existing components to reuse, and necessary API, model, service, or database changes.
- **Ordered tasks:** small, logically sequenced steps, likely files/components, and dependencies between tasks.
- **Testing strategy:** appropriate unit and integration coverage, regression scenarios, and concrete manual browser/API verification steps.
- **Risks:** relevant security, data integrity, backward compatibility, dependencies, and assumptions.

Scale detail to the ticket while keeping each criterion verifiable. For nontrivial work, persist the plan in the project's planning directory, otherwise `docs/plans/`. Keep the ticket's original body intact; link or summarize the plan separately through MCP where supported. Track completed steps and update the plan when discoveries change the approach. Proceed without a separate plan approval unless a material product/architecture choice needs the user.

## Implement incrementally

Use relevant Superpowers implementation, TDD, and systematic-debugging guidance. The following engineering requirements apply directly to Cadence builds; do not leave them implicit in a dependency's instructions.

### Code quality and architecture

- Apply **SOLID, DRY, KISS, and clean code principles where appropriate**. Prefer the simplest readable solution that meets the ticket; these principles are not a reason to add layers or generalize for hypothetical future needs.
- Follow the project's architecture, design patterns, naming, and UI standards. Introduce a new pattern only when it solves a concrete problem that existing approaches do not.
- Keep functions, services, classes, and components focused. Reuse existing utilities and shared components; avoid duplicated logic, dead code, and unnecessary abstractions.
- Include appropriate input validation, error handling, and security controls. Handle expected failure scenarios and boundary conditions, not just the happy path.
- Preserve existing behavior and interfaces unless a change is required by the ticket. Consider effects on callers, stored data, and related features when changing contracts.
- Avoid unnecessary dependencies and unrelated refactoring. Keep changes isolated to the ticket's feature branch and scope.

### Tests and debugging

Use RED → GREEN → REFACTOR wherever practical:

1. Write a test describing the desired observable behavior or reproducing the bug.
2. Run it and confirm that it fails for the expected behavioral reason, not a broken fixture or unrelated environment error.
3. Implement the simplest correct solution.
4. Run the test and verify that it passes.
5. Refactor while keeping the relevant tests passing.

Provide appropriate automated coverage for new functionality and bug fixes: acceptance behavior, edge/boundary cases, validation/errors, relevant integrations, and regressions. Prefer observable behavior over tests coupled to implementation details. Reuse sufficient existing coverage; do not add meaningless tests for a documentation-only or similarly low-impact change. If automated coverage or test-first execution is impractical, explain the limitation and provide the strongest feasible verification; do not imply TDD occurred.

Do not weaken, remove, skip, or bypass tests to manufacture a passing result. Update an existing expectation only when the ticket intentionally changes the behavior, and explain that change. Investigate unexpected failures systematically, identify the root cause, and correct it rather than layering speculative patches. Update the plan when the diagnosis changes the approach; do not expand into unrelated fixes.

## Automated and manual verification

Run the relevant project-supported checks: unit tests, integration tests, regression tests, linting/formatting checks, type checking, and production build or equivalent validation. Use the project's documented commands and required checks. Investigate failures, fix regressions introduced by this change, and distinguish pre-existing failures with evidence. Record commands, results, and checks that could not run; never claim a check passed without executing it successfully.

For changes affecting runtime behavior, start the application using its documented local development setup and verify the actual behavior:

- **Frontend/user-facing changes:** use available supported browser tools to exercise the complete user journey, form validation and expected errors, relevant edge cases, and interactions with related features. Inspect console errors and failed network requests. Apply the project's design system and relevant frontend-design guidance, including accessibility and responsive behavior.
- **Backend changes:** exercise the relevant APIs, integrations, database behavior, or background processes with appropriate tools; compilation alone is not functional verification.
- Use safe test data. Do not make destructive changes in production or shared environments. Do not use production data or credentials for local testing without explicit authorization and safe handling.

For documentation-only or other changes with no runtime impact, mark runtime verification not applicable and give the reason; verify their acceptance criteria through suitable inspection or other checks. If applicable runtime verification is unavailable, say what could not be tested and why. Record affected acceptance criteria as BLOCKED; do not convert unavailable verification into a pass.

## Internal code review and acceptance evidence

Use the relevant Superpowers code-review and verification-before-completion guidance. Review the full diff against the ticket and surrounding code for:

- Functional correctness and completeness against every acceptance criterion.
- Architectural consistency, readability, maintainability, focused responsibilities, and appropriate SOLID/DRY/KISS application.
- Unnecessary complexity, abstractions, dependencies, duplicated functionality, and dead code.
- Missing validation/error handling, security weaknesses, data integrity concerns, and backward-compatibility regressions relevant to the change.
- Meaningful test coverage, expected failure/edge-case behavior, and unintended changes outside scope.

Resolve legitimate findings. After corrections, rerun affected automated checks, repeat relevant manual verification, and recheck affected acceptance criteria. Do not rely on results from code that has since changed.

Create an acceptance checklist or table with **every criterion** and supporting test/runtime evidence:

- **PASS:** implemented and verified.
- **FAIL:** verification demonstrates incorrect behavior.
- **BLOCKED:** verification cannot be completed; state the reason and needed action.

Do not declare the implementation ready for approval while known implementation defects remain. If verification is blocked, request help to unblock it and make the limitation explicit; human approval alone does not turn a BLOCKED criterion into PASS.

## Human testing gate

Before any commit, push, PR creation, or In Review transition, present the implemented result: branch, concise changes, checks actually run, criterion evidence, limitations, and practical local testing steps. Request explicit human testing and approval of this implementation. Explain that the Cadence build workflow requires this gate. Silence and automated checks are not approval.

Provide a practical way to test: how to start/access the application, required setup or safe test data, where to navigate, actions to perform, expected results for the acceptance criteria, and important edge cases. Do not expose a local server publicly without explicit permission.

If changes are requested, implement the corrections, add/update meaningful tests where appropriate, rerun affected checks and manual verification, and review the changed code before requesting approval of the updated result. Do not repeatedly ask for approval already given to unchanged work.

## Publish after approval

Review the final diff and staged files; exclude secrets, credentials, unintended generated files, and unrelated work. Commit only intended changes, with a ticket-referencing commit message. Push the feature branch without force. Create a PR against the configured target (`development` by default). The first line of the PR description must be `Ticket: ` followed by the canonical ticket URL. Prefer a server-returned URL; the inspected server omits it, so use only the source-verified route and MCP-verified IDs described in [the MCP contract](mcp-contract.md). Include a concise implementation and verification summary below it.

Verify the PR exists and targets the correct branch. Only then move the Cadence ticket to the existing In Review status and verify the update. Keep its assignment. If PR creation fails, reconcile GitHub before retrying and do not transition the ticket prematurely.

## External review: at most three rounds per ticket/PR

When the configured external review integration supports it, post the exact command `/code-review` as a new PR comment. This workflow authorizes that review request and technical replies to review findings. If the reviewer is unavailable, report BLOCKED rather than claiming a review occurred.

Record each trigger's comment ID, round, and head commit. Confirm submission before retrying uncertain writes. Monitor the triggered review in the active session with bounded waits. No feedback is not evidence of completion. If it cannot complete in-session, report PENDING and save enough state to resume without another trigger.

For completed reviews, read each finding in full and inspect its code and surrounding context. Assess correctness, security, maintainability, architecture, and regression risk; exercise independent judgment rather than blindly following suggestions. Fix valid findings at the root cause, add/update appropriate tests, and apply the same automated/manual/criterion re-verification and internal review used above. Commit and push verified corrections to the same branch. Reply to invalid findings with technical reasoning/evidence; do not change correct code merely to satisfy a reviewer. Track rejected findings and do not repeatedly reply to identical duplicates; reconsider only when materially new evidence appears. If corrections materially change approved functionality, obtain renewed human approval before committing/pushing those material changes.

After corrections are pushed, trigger the next review only if the previous review completed and fewer than three total triggers exist. Never trigger a fourth round. Fix remaining valid issues where practical after round three, but report that new commits have not received another external review.

Use these review outcomes: PASSED (completed review covers current head and no valid findings remain), PENDING, BLOCKED, or REVIEW LIMIT REACHED. Do not claim a clean review of an earlier commit applies to later changes.

## Checkpoint and report

Preserve the ticket ID, branch/base, PR URL, head commit, criterion evidence, approval scope, review trigger IDs/count, reviewed commit, finding dispositions, and usage checkpoint in project-appropriate resumable state. Do not store secrets. On resume, reconcile this state against Cadence, Git, and GitHub before acting. Do not commit tracking-only local state unintentionally.

Follow [token accounting](tokens.md) for Token effort. A missing usage number must remain explicitly unavailable; it is not zero.

Report actual ticket link/status/assignee, implementation summary, branch/PR, test and criterion evidence, approval state, external review outcome, token-accounting status, and remaining action. Use AWAITING HUMAN APPROVAL, IN REVIEW, REVIEW PASSED, REVIEW LIMIT REACHED, or BLOCKED as appropriate. Keep the report concise and link detailed evidence.

Successful engineering completion requires implemented scope, appropriate passing checks, relevant runtime verification, PASS evidence for every acceptance criterion, resolved internal review findings, explicit human approval (including required reapproval), a verified PR/target, the ticket in In Review, and a completed external review of the current head with no outstanding valid findings. A pending review, unresolved defect, or BLOCKED criterion is not completion. A human-authorized exception must be reported as an exception, never as verification that occurred.

Never automatically merge or mark the ticket Done/Completed. Successful execution ends at a verified reviewed PR with required human approval; delivery/merge remains a separate lifecycle event. At REVIEW PASSED, write the measured cumulative execution usage into `tokenEffort`, including implementation and review corrections. Keep the ticket In Review. If telemetry is unavailable, report engineering completion separately from unavailable token accounting. For pending/blocked/review-limit outcomes, retain measured usage checkpoints for resume rather than claiming final effort.
