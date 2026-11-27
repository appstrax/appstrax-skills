# Setup and readiness check

Installing this skill does not connect Time Tracker. It needs the `timetracker` MCP server (Streamable HTTP, `https://time-tracker-api-nxms.codecapsules.co.za/mcp`) connected and authenticated. Never claim setup is complete until `whoami` succeeds. Never ask for the token in chat.

1. **Find the tools.** Look for the Time Tracker MCP tools (`whoami`, `list_projects`, `list_categories`, `list_time_entries`, `log_time_entry`). If none exist, tell the user the MCP server is not connected and point them to "Connect Time Tracker" in the skill README. Stop.
2. **Check identity.** Call `whoami`. A 401 means a missing, wrong, expired or revoked token: tell the user to create a new agent token and reconnect. Do not guess an endpoint.
3. **Check access.** Call `list_projects`. Report the projects the token can see. An empty list means the token is scoped to no projects or the user has no memberships.
4. **Check write ability.** Do not test with a disposable entry. Instead, say whether `log_time_entry` is exposed, and remind the user that logging needs the `time:write` scope and a `contributor` or `manager` role on the project (a 403 on the first real log reveals a missing scope).

Return a compact result: connected as (name/email), projects visible, logging available or unknown, and the precise missing items. Recheck live on use; do not repeatedly ask once confirmed.
