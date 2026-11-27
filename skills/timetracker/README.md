# Time Tracker

Log and review time in Appstrax Time Tracker from your coding agent, via the Time Tracker MCP server.

## Install

```sh
npx skills add appstrax/appstrax-skills --skill timetracker -a claude-code -a codex
```

Add `-g` for a user-wide install. Installation copies the skill files only. It does not connect or authenticate the Time Tracker MCP server, which the skill requires.

## Connect Time Tracker

Configure a Streamable HTTP MCP connection named `timetracker` to:

```text
https://time-tracker-api-nxms.codecapsules.co.za/mcp
```

Authenticate with a personal agent token: in Time Tracker open **Settings → Agent access → Manage tokens → New token**, choose scopes `read` and `time:write` and the projects it may use, and copy the `tt_pat_...` token (shown once). Do not commit credentials.

```sh
# Claude Code
export TT_TOKEN=tt_pat_...
claude mcp add --transport http timetracker https://time-tracker-api-nxms.codecapsules.co.za/mcp --header "Authorization: Bearer $TT_TOKEN"
```

For Codex add to `~/.codex/config.toml`:

```toml
[mcp_servers.timetracker]
url = "https://time-tracker-api-nxms.codecapsules.co.za/mcp"
bearer_token_env_var = "TT_TOKEN"
```

If you already have the Time Tracker MCP connected, the skill uses it as is.

## Use

Run `/timetracker setup` (Codex: `$timetracker setup`) to check the connection, your identity and project access. Then ask in plain language, e.g. "log 2 hours on Project X for code review", or "what did I log this week?". Native invocation: `/timetracker` in Claude Code, `$timetracker` in Codex.

## Actions

- **log**: log hours on a project, with confirmation.
- **list**: show what you logged for a date range.
- **summary**: a timesheet by project, category and day, with an optional target.
- **gaps**: find weekdays with missing hours and help fill them.
- **log from git**: draft entries from your commits, which you review before anything is logged.
- **standup**: a status update from yesterday's entries.
- **setup**: check the connection and your access.

## Update

```sh
npx skills update timetracker -p   # project install
npx skills update timetracker -g   # global install
```
