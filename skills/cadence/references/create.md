# Create tickets

## Resolve placement

Honor an explicitly supplied project, sprint, or epic after validating it belongs to the target project and is usable. Do not ask again for a valid choice already supplied.

Apply these capability-based rules together with the current-version behavior in [the MCP contract](mcp-contract.md). Missing tools are not proof a feature is disabled in a newer deployment; distinguish known inspected-version limitations from unknown live capabilities.

- If sprints are enabled and no sprint is specified, fetch valid sprints and ask which to use. Offer backlog only when supported by the project. Do not silently choose the current sprint.
- If sprints are disabled, use backlog without a sprint question. If the user supplied a sprint despite disabled sprints, explain the conflict.
- If epics are enabled and no epic is specified, fetch valid epics and ask; include “No epic”. If disabled, omit the epic and explain any conflicting explicit selection.
- Batch unresolved placement questions with substantive requirement questions where practical. Do not ask for IDs the MCP can resolve from an unambiguous name.

## Ground and refine

Read repository instructions, relevant implementation, similar features, and tests. Read related Cadence tickets/dependencies and search for likely duplicates. Inspect enough code to describe current behavior and constraints accurately; do not exhaustively scan unrelated code.

Clarify materially missing outcomes, users, business rules, scope boundaries, or acceptance behavior before creating. Routine implementation choices belong to execution planning and do not require ticket-author questions. If the codebase is inaccessible, report the limitation and request access before claiming a code-grounded ticket.

Use multiple tickets when outcomes are independently useful and verifiable. Do not invent adjacent features. Preserve explicit dependency relationships, and flag existing overlapping work instead of automatically duplicating it.

## Write and estimate

Fetch the project's current **New ticket template**. Use it exactly as the project expects. Only if no template is configured, use [the default template](default-template.md). An unreadable template is not evidence that no template exists.

Write a short action-oriented title and concise, human-readable body. Describe the desired outcome, observed current behavior, expected behavior, essential constraints, testable acceptance criteria, and practical verification. Include relevant links and code references when useful. Omit empty optional context. Do not put a detailed implementation plan or speculative file-by-file design in a new ticket.

Estimate ticket effort in Fibonacci story points using comparable project tickets. Use 1, 2, 3, 5, 8, 13, 21 and larger Fibonacci values only when the scope warrants them and the live schema permits them. Consider scope, complexity, uncertainty, and testing; briefly explain unusual estimates. If project configuration explicitly conflicts with Fibonacci, resolve that conflict before writing. Always send `effort` explicitly; the current server defaults omitted effort to 1. Do not represent effort as business value or predicted token usage. Leave `tokenEffort` unset during creation.

## Create and confirm

Once requirements and placement are resolved, the user's create request authorizes creation; do not add a routine approval gate. Use MCP to create the ticket(s) with their selected placement, template-based body, and effort. Add supported dependency links after IDs exist. Reconcile partial success before retrying.

Read back the result and report each ticket's actual ID/link, concise title, sprint/backlog, epic/no epic, and effort. If any metadata failed to save, identify that failure rather than claiming full success.
