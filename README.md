# Appstrax skills

Reusable agent skills maintained by Appstrax. Each skill lives in its own folder and can be installed independently into supported coding agents.

## Available skills

| Skill | Purpose | Setup and usage |
| --- | --- | --- |
| `cadence` | Create code-informed tickets and implement them through reviewed pull requests using Cadence MCP. | [Cadence README](skills/cadence/README.md) |
| `timetracker` | Log and review time entries in Time Tracker using its MCP server. | [Time Tracker README](skills/timetracker/README.md) |

## Install

List the available skills:

```sh
npx skills add appstrax/appstrax-skills --list
```

Install a selected skill into Claude Code and Codex:

```sh
npx skills add appstrax/appstrax-skills --skill cadence -a claude-code -a codex
```

Run from the target project for a project installation. Add `-g` to install globally. Remove either agent option if you only want one; omit the agent options for interactive selection. The Skills CLI currently requires Node.js 22.20 or later. See its [installation reference](https://github.com/vercel-labs/skills/blob/main/docs/commands.md).

Each skill's README documents its required connections, dependencies, and supported workflows. Installation copies the skill files; it does not automatically authenticate services or install every dependency. If Cadence is missing from Claude Code on macOS, follow the [discovery fix](skills/cadence/README.md#macos-cadence-does-not-appear-in-claude-code).

## Repository structure

```text
appstrax-skills/
├── README.md                         # Catalog and repository-wide guidance
└── skills/
    └── cadence/
        ├── SKILL.md                  # Agent instructions and metadata
        ├── README.md                 # Human setup, usage, and troubleshooting
        ├── INSTALL.md                # Detailed Cadence dependency/MCP setup
        ├── RELEASE-NOTES.md          # Cadence release status and limitations
        ├── agents/
        │   └── openai.yaml           # Optional host metadata
        └── references/               # Workflow details loaded when needed
```

Add each future skill as a sibling: `skills/<skill-name>/`. Only Cadence exists today. The `skills/` convention lets installers discover multiple skills while `--skill <name>` selects the desired one.

Keep each skill self-contained: runtime instructions, references, scripts, and assets it needs must live inside its folder. Installers may copy only that folder. Keep repository-wide documentation at the root and skill-specific documentation with its skill. Create optional `scripts/`, `assets/`, or `agents/` directories only when a skill needs them; do not create empty placeholder skills.

## Add a skill

1. Create `skills/<skill-name>/SKILL.md` with YAML `name` and `description`. Use a unique lowercase hyphenated name matching the folder.
2. Add a README with install/usage instructions, requirements, and limitations.
3. Keep the agent entrypoint focused; put substantial conditional guidance in `references/` and link it from `SKILL.md`.
4. Add the skill to the catalog above. Verify local links, frontmatter, and realistic requests before publishing.
5. Verify discovery with `npx skills add ./ --list` and test installation in an isolated target project/agent.

Follow the [Agent Skills specification](https://agentskills.io/specification). Do not put credentials or machine-specific paths in skills, and do not require files from another skill folder at runtime unless its installation is explicitly managed. No root `SKILL.md` is needed: the repository is a collection, not one skill.

## Updates

```sh
# Run from the project for project-installed skills
npx skills update cadence -p

# For a global installation
npx skills update cadence -g
```

Use the installed skill's name to update a different skill. If your installation records the old repository name, rerun `skills add` against `appstrax/appstrax-skills` using the same scope and agent choices. See each skill's README for any additional upgrade steps.
