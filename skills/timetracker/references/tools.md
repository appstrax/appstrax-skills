# Time Tracker MCP tools

All tools act as the token owner; there is no user id argument. Unknown fields are rejected.

| Tool | Scope | Input | Notes |
| --- | --- | --- | --- |
| `whoami` | `read` | none | Owner id, email, roles, name. |
| `list_projects` | `read` | none | Projects the caller is a member of. |
| `list_categories` | `read` | `projectId?` | Distinct categories from the caller's own entries. |
| `list_time_entries` | `read` | `start`, `end`, `projectId?` | ISO dates; the caller's own entries. |
| `log_time_entry` | `time:write` | `projectId`, `date`, `hours`, `description`, `category` | Creates an entry with `approved: false`. |

Project roles: `contributor` and `manager` may log time; `viewer` may not (403). A token limited to specific projects can only use those projects.
