# Cadence skills

Create well-defined tickets and implement them through reviewed pull requests using the [Cadence](https://cadence.codecapsules.io/) MCP server.

## Install

Run from the project where you want to use Cadence:

```sh
npx skills add appstrax/cadence-skills --skill cadence
```

Select your supported coding agent when prompted. Add `-g` for a user-wide installation. The Skills CLI currently requires Node.js 22.20 or later.

Install [Superpowers](https://github.com/obra/superpowers#installation) using its instructions for your agent, and install Frontend Design for UI work:

```sh
npx skills add anthropics/skills --skill frontend-design
```

The Cadence install command installs the skill files. It does not automatically install these dependencies or authenticate the MCP connection.

## Connect Cadence

Configure a Streamable HTTP MCP connection to:

```text
https://cadence.codecapsules.io/mcp
```

Authenticate using your agent's supported OAuth flow or a personal agent token from **Cadence → Settings → Agent tokens**. Do not commit credentials.

See the [installation guide](INSTALL.md) for connection instructions, required permissions, dependencies, and compatibility.

## Use

Use your agent's native skill invocation. For example, in Codex:

```text
$cadence setup
$cadence create Add CSV export for the sprint report
$cadence build CAD-12,CAD-15
```

Agents that support a `/cadence` command can use the equivalent syntax. You can also ask your agent to use the Cadence skill in plain language.

- **Create:** inspects the repository, clarifies material ambiguity, resolves project/sprint placement, writes outcome-focused tickets, and estimates Fibonacci story points.
- **Build:** resolves ticket dependencies, investigates and plans, implements and verifies, requests human approval before committing/publishing, and follows up on external review with a three-round limit.
- **Setup:** checks the MCP connection, project mapping, permissions, skills, and token-accounting support.

Successful builds leave the ticket **In Review**. Measured Token effort is recorded when the PR has passed review; the workflow does not merge or mark tickets Done automatically.

## Current limitations

This release candidate is aligned with the inspected Cadence source. Structural checks passed; authenticated end-to-end execution and installation across agent hosts remain unverified.

The inspected Cadence version does not support epics, project ticket templates, sprint enable/disable settings, or structured ticket dependencies. The skill uses the supplied default template and reports unsupported capabilities. Exact token accounting requires trustworthy host telemetry; it never invents a number.

See [release notes and capability gaps](RELEASE-NOTES.md) and the [MCP contract](skills/cadence/references/mcp-contract.md).

## Repository layout

```text
skills/cadence/
  SKILL.md
  agents/openai.yaml
  references/
```

The skill core follows the [Agent Skills format](https://agentskills.io/specification). MCP setup, plugin installation, command syntax, and token telemetry vary by agent host. Third-party skill contents are not bundled in this repository.
