# Cadence MCP contract

Source inspected at Cadence commit `411544960c68a0ddfd4153ce073340a2cf99d36b`. Live tool schemas take precedence if the deployment changes. This is source verification, not an authenticated live test.

Connection: Streamable HTTP at `https://cadence.codecapsules.io/mcp`. Source supports OAuth and personal agent bearer tokens. Never store secrets in this skill or repository. Cadence results contain JSON in MCP text content; `isError: true` means failure even when transport succeeded.

## Read and resolve

| Purpose | Tool and inputs | Relevant result |
| --- | --- | --- |
| Identity/access | `whoami {}` | `userId`, scopes, allowed projects with IDs/keys/names |
| Project | `get_project {projectId}` | Identity and swimlanes; no template, feature flags, or repo mapping |
| Lanes | `list_swimlanes {projectId}` | Existing lanes with `isDone` and `isDefaultBacklog` |
| Sprints | `list_sprints {projectId}` | IDs, names, planned/active/completed status |
| Search tickets | `list_tickets {projectId, text?, limit?, cursor?}` | Tickets, `hasMore`, `nextCursor`; text matches title OR key |
| Ticket | `get_ticket {ticketId}` | UUID/key, description, version, effort, tokenEffort, assignees, comments, subtasks |

Resolve `CAD-12` by searching within the verified project and selecting exact `key === 'CAD-12'`, not the first substring hit. Follow pagination when necessary. Use the returned UUID for ticket tools. A key prefix can identify a candidate project from `whoami`, but still verify against the explicit/repository mapping.

`get_ticket` currently omits `projectId`. For a raw UUID, verify its membership by finding that exact ID through the target project's `list_tickets` (search by its returned key, then compare ID). A successful UUID lookup alone proves visibility, not membership in the chosen project.

Omitting `list_tickets.sprintId` returns ALL project tickets, not just backlog. The MCP schema accepts a UUID sprint filter, not REST's `none` sentinel. For backlog, paginate the project list and select `sprintId === null`. Do not send `none` to this MCP schema or silently switch to REST.

## Mutations used by this skill

| Tool | Inputs and constraints |
| --- | --- |
| `create_ticket` | `projectId`, title (1–300), description, type (`story/task/bug`), priority (`urgent/high/medium/low`), optional sprint/lane, assignees, labels, effort |
| `update_ticket` | `ticketId`, current `version`, changed title/description/type/priority/effort/tokenEffort only |
| `move_ticket` | `ticketId`, current `version`, `swimlaneId` and/or `sprintId`; explicit null sprint means backlog |
| `assign_ticket` | `ticketId`, `userIds`; replaces entire list; no version argument |
| `add_comment` | `ticketId`, Markdown `body`, optional measured `tokensSpent`; no idempotency-key argument |

Effort accepts integers 1–999; this skill chooses Fibonacci points. Token effort accepts null or integer 0–4,294,967,295. Do not clamp an overflowing measurement; report the storage limitation. Omit unknown usage rather than writing zero.

Only versioned tools take `version`; do not apply the server introduction's blanket “every write” wording literally. Re-read and reassess on conflict. Creation/comments/assignment have no exposed idempotency key: after uncertain results, read and reconcile before retrying. Stop if duplicate-versus-new intent remains ambiguous.

Ticket status is represented by its swimlane, not a `status` property. Resolve In Progress/In Review against actual lane names and ask if ambiguous. Never use an `isDone: true` lane for this build workflow.

Creation requires manager-or-higher project standing plus `tickets:write`; read access requires `read`. Comments require `comments:write`. Updates/moves depend on assignment/standing. Treat actual server authorization as decisive; a token scope alone does not confer project permission. Do not attempt self-assignment through another API after a denial.

## Current capability limits and behavior

- **Sprints:** available for projects; no enable/disable flag exists in this source. Ask sprint versus backlog when unspecified. An empty sprint list is not evidence of a disabled feature; offer backlog and report no available sprint.
- **Epics:** no model, tools, or ticket epic field exists. Report epics unsupported; omit the epic question on this version. If the user explicitly requires an epic, clarify before creating a ticket without it. Never substitute a label without agreement.
- **Template:** no project template field exists in this source. Use the supplied default template. If a newer live server exposes a template, use it; an access error is not permission to fall back silently.
- **Dependencies:** no structured ticket-dependency tools/model are exposed. Preserve explicit prerequisite keys from the request/body/comments as task context, and order by those. Do not claim structured links were saved. If required dependency relationships cannot be represented, disclose that before creating affected tickets.
- **Attachments:** `get_ticket` does not return attachments. Ask for material missing attachment content/access rather than saying it was reviewed.
- **Repository mapping:** absent from MCP. Use an existing trusted project instruction mapping or ask the user; do not infer solely from a chat/folder name.
- **Ticket URLs:** responses omit a URL. The source's `SlackNotifier.ticketUrl` and app routing establish `/projects/{projectId}/board/ticket/{ticketId}`. For this deployment, use its configured origin plus that path with MCP-verified UUIDs. This is a source-derived canonical link, not a server-returned field. For other deployments, verify their origin and route first.

## Source locations for maintainers

Paths relative to the Cadence app repository:

- `cadence-api/src/modules/agent/mcp/tools/{project,ticket,sprint}-tools.ts`
- `cadence-api/src/modules/agent/mcp/tool-context.ts`
- `cadence-api/src/modules/projects/models/database/project.ts`
- `cadence-api/src/modules/tickets/models/filters/ticket-list.filters.ts`
- `cadence-api/src/modules/notifications/services/slack-notifier.ts`
- `cadence-api/src/platform/http/application.ts`

Do not require an end user's target repository to contain Cadence's own source code.
