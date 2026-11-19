# Install and distribute Cadence

## Compatibility

The `skills/cadence/` directory follows the Agent Skills format. A compatible agent must also have Streamable HTTP MCP and access to the target repository. Build needs Git, GitHub PR access, test/runtime tools, and the required skills. Exact Token effort additionally needs reliable runtime usage counters. Skill support alone is insufficient for all of these capabilities.

Keep the skill portable. Ship optional plugin wrappers per tested host for easier MCP/dependency setup. Do not advertise a universal slash command or universal plugin installer.

## Installation

See the [Cadence README](README.md) for quick installation, updates, and the [macOS Claude Code discovery fix](README.md#macos-cadence-does-not-appear-in-claude-code).

1. Install `skills/cadence/` into the target agent's supported skill directory or use its skill installer with this local folder. Resolve the existing Cadence skill-name collision deliberately; do not overwrite an existing skill silently.
2. Install Superpowers using its host-specific guide: https://github.com/obra/superpowers#installation . This is preferred over copying its individual skills because host integration may include hooks.
3. Install frontend-design from its upstream source: https://github.com/anthropics/skills/tree/main/skills/frontend-design . The supplied AgenticSkills page is a directory listing for this package.
4. Connect Cadence MCP and authenticate as described below.
5. Start/reload the agent as required for discovery and run `cadence setup` using its native skill invocation. Confirm identity, project/repository mapping, permissions, build dependencies, and token-accounting readiness.

For agents supported by the open Skills CLI, an interactive installation path is available (Node.js 22.20+ according to its current documentation). Run from the target repository and choose the same intended agent(s) in each prompt. The Cadence command explicitly selects Claude Code and Codex; remove an agent option if it is not needed:

```sh
npx skills add appstrax/appstrax-skills --skill cadence -a claude-code -a codex
npx skills add obra/superpowers
npx skills add anthropics/skills --skill frontend-design
```

These commands install skill files; they do not replace Superpowers' host integration or set up MCP. End-to-end installation and authenticated production execution have not yet been verified. Do not use an “all agents” flag to install into unrelated environments by default.

Use the installer-generated lockfile and recorded upstream commits for repeatable team installation. Pin reviewed dependency and installer versions for a published release rather than silently tracking moving branches. Upstream licenses are MIT for Superpowers and Apache-2.0 in frontend-design's LICENSE.txt; retain the relevant upstream license/notice files if distributing copies. This package links dependencies and does not vendor their contents.

## MCP connection

Endpoint: `https://cadence.codecapsules.io/mcp`

Transport: Streamable HTTP. Source supports OAuth 2.1 with consent and personal agent bearer tokens. Prefer the host's OAuth flow when compatible. For personal tokens, use Cadence **Settings → Agent tokens** to select scopes/projects and store the secret through the host's supported credential mechanism. Never put credentials in the skill, a shared repository, or a support transcript.

For Codex CLI with OAuth support:

```sh
codex mcp add cadence --url https://cadence.codecapsules.io/mcp
codex mcp login cadence
```

For Claude Code, Cadence's Agent tokens screen supplies the exact bearer-token connection command. Other agents need their native remote-MCP settings. Do not assume a JSON/TOML configuration snippet is portable between hosts.

Minimum workflow scopes are `read`, `tickets:write`, and `comments:write`; project standing and project allowlists also apply. Creating tickets requires manager-or-higher standing. Broader administrative scopes are not needed for this skill.

Connection verification is `whoami`, followed by a read of the selected project. No test ticket needs to be created merely to check setup. Production authentication was not tested while preparing this package.

## Usage

Use the host's skill selector or native syntax:

```text
$cadence setup
$cadence create Add CSV export for the sprint report
$cadence build CAD-12,CAD-15
```

In Claude Code, use the equivalent `/cadence` slash command; the installed skill provides it automatically. Plain language such as “Use the Cadence skill to build CAD-12” remains a portable invocation intent.

Setup asks for a project when no reliable repository mapping exists. Creation asks for sprint/backlog when unspecified. The inspected Cadence version lacks epics and project templates: it uses the default template and reports epics unsupported. The complete desired feature set needs the server changes listed in RELEASE-NOTES.md.

## Publishing

Publish a versioned repository containing `skills/cadence/` and the installation documentation. End users can install from that repository with their supported skill installer. A single onboarding flow can guide installation of all three packages plus MCP authentication, but the Agent Skills format has no portable transitive-dependency installation contract.

For one-click installation on a particular host, build and test that host's plugin manifest, dependency handling, and OAuth connection. Keep that wrapper separate from the portable core. The source repository is https://github.com/appstrax/appstrax-skills. No host-specific marketplace listing is included.

References:

- Agent Skills specification: https://agentskills.io/specification
- Open Skills CLI: https://github.com/vercel-labs/skills
- Superpowers install guide: https://github.com/obra/superpowers#installation
- Frontend Design source: https://github.com/anthropics/skills/tree/main/skills/frontend-design
- Codex MCP setup: https://learn.chatgpt.com/docs/extend/mcp?surface=cli
