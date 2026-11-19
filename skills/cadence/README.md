# Cadence

Create well-defined tickets and implement them through reviewed pull requests using the [Cadence](https://cadence.codecapsules.io/) MCP server.

## Install

Run from the project where you want to use Cadence:

```sh
npx skills add appstrax/appstrax-skills --skill cadence -a claude-code -a codex
```

This command explicitly targets Claude Code and Codex. Remove either `-a` option to target only the other agent; omit both to choose interactively. Run it from your target project for a project installation, or add `-g` for a user-wide installation. The Skills CLI currently requires Node.js 22.20 or later.

Install [Superpowers](https://github.com/obra/superpowers#installation) using its instructions for your agent, and install Frontend Design for UI work:

```sh
npx skills add anthropics/skills --skill frontend-design
```

The Cadence install command installs the skill files. It does not automatically install these dependencies or authenticate the MCP connection.

## macOS: Cadence does not appear in Claude Code

The installer can target only the agent it detects, so installing for Codex does not guarantee a Claude Code entry. First rerun installation with Claude Code selected explicitly:

```sh
# For this project: run in the project's root
npx skills add appstrax/appstrax-skills --skill cadence -a claude-code

# Alternatively, make it available in all local projects
npx skills add appstrax/appstrax-skills --skill cadence -a claude-code -g
```

If you already installed **globally**, `~/.agents/skills/cadence/SKILL.md` exists, and Claude Code still cannot find it, link that installation into Claude Code's personal skills directory. This guarded version of `ln -s ~/.agents/skills/cadence ~/.claude/skills/cadence` preserves any existing entry:

```sh
mkdir -p "$HOME/.claude/skills"
if [ ! -f "$HOME/.agents/skills/cadence/SKILL.md" ]; then
  echo "Cadence is not installed globally in ~/.agents/skills/cadence. Install it with -g first."
elif [ -e "$HOME/.claude/skills/cadence" ] || [ -L "$HOME/.claude/skills/cadence" ]; then
  echo "A Claude Code Cadence entry already exists. Inspect it before changing it:"
  ls -ld "$HOME/.claude/skills/cadence"
else
  ln -s ~/.agents/skills/cadence ~/.claude/skills/cadence
fi
```

Do not force-replace an existing directory or symlink. This fallback is for **global installations**; for a project-only installation, use the project command above instead of linking a project skill into your global folder. A symlink keeps Claude Code pointed at the same installed files used by Codex.

Start a fresh Claude Code session if `/cadence` has not appeared, then run `/cadence setup`. This fixes skill discovery; Cadence MCP authentication and build dependencies are still required. Claude Code documents its [personal/project skill locations and symlink support](https://code.claude.com/docs/en/skills#choose-where-skills-load).

## Update

```sh
# Project install: run inside that project
npx skills update cadence -p

# Global install
npx skills update cadence -g
```

If you installed from the previous repository name, rerun the install command using `appstrax/appstrax-skills` with the same scope and intended agents to refresh the recorded source. Review any local customizations before replacing installed files. Start a fresh agent chat after updating.

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

In Claude Code, use `/cadence setup`, `/cadence create ...`, or `/cadence build CAD-12,CAD-15`. Installing the skill in a Claude Code skill location exposes `/cadence`; no separate command registration is required. You can also ask your agent to use the skill in plain language.

- **Create:** inspects the repository, clarifies material ambiguity, resolves project/sprint placement, writes outcome-focused tickets, and estimates Fibonacci story points.
- **Build:** resolves ticket dependencies, investigates and plans, implements and verifies, requests human approval before committing/publishing, and follows up on external review with a three-round limit.
- **Setup:** checks the MCP connection, project mapping, permissions, skills, and token-accounting support.

Successful builds leave the ticket **In Review**. Measured Token effort is recorded when the PR has passed review; the workflow does not merge or mark tickets Done automatically.

## Engineering quality

The [build reference](references/build.md) explicitly requires investigation of existing architecture and reuse, a criterion-driven implementation plan, appropriate SOLID/DRY/KISS application, input validation and error/security handling, practical RED → GREEN → REFACTOR, meaningful regression coverage, real application verification, and internal review. Review corrections must be reverified against affected tests, user flows, and acceptance criteria. Missing evidence remains BLOCKED; a passing review or human approval cannot substitute for verification.

## Current limitations

This release candidate is aligned with the inspected Cadence source. Structural checks passed; authenticated end-to-end execution and installation across agent hosts remain unverified.

The inspected Cadence version does not support epics, project ticket templates, sprint enable/disable settings, or structured ticket dependencies. The skill uses the supplied default template and reports unsupported capabilities. Exact token accounting requires trustworthy host telemetry; it never invents a number.

See [release notes and capability gaps](RELEASE-NOTES.md) and the [MCP contract](references/mcp-contract.md).

## Skill layout

```text
cadence/
  SKILL.md
  README.md
  INSTALL.md
  RELEASE-NOTES.md
  agents/openai.yaml
  references/
```

`SKILL.md` is the agent entrypoint; this README is for people. Installation, references, and metadata stay with the skill so it can be installed independently from other skills in the [Appstrax catalog](https://github.com/appstrax/appstrax-skills).
