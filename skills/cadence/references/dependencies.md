# Dependency integration

Required upstream sources:

- Superpowers: https://github.com/obra/superpowers
- Frontend Design: https://github.com/anthropics/skills/tree/main/skills/frontend-design

The supplied AgenticSkills listing resolves to Anthropic's `frontend-design`; use its upstream source. Install both as part of the user's setup, but load only guidance relevant to the current work. Creation does not need the build-methodology bundle loaded. UI design guidance is not required for backend-only execution.

## Superpowers within Cadence

Use installed skills for brainstorming when material design is open, writing plans, executing plans, TDD, systematic debugging, requesting/receiving code review, and verification before completion. Load the installed versions rather than relying on copied summaries. Use host-native tools; do not invent a `Skill` or subagent tool when unavailable. Inline execution is the portable baseline. Delegation depends on host support and authorization.

Cadence's user-requested lifecycle governs this integration: no commits, pushes, or PRs before human implementation approval; no automatic merge or worktree deletion; at most three external review triggers. Upstream examples may suggest intermediate commits, design approval rounds, or branch finishing choices. Apply their engineering guidance without bypassing these explicit lifecycle choices. Surface a material unresolved conflict rather than running incompatible processes simultaneously.

Use the ticket's already-defined scope as planning input. Ask only about material unresolved choices; do not repeat discovery questions the ticket has answered. Preserve working code and user changes: an upstream testing workflow does not authorize destructive resets or deleting someone else's work.

## Frontend Design within an existing product

Use frontend-design for interface changes. The existing project's design system, components, accessibility requirements, and ticket brief constrain the design. Do not introduce a new visual identity, new framework, typography system, or dramatic redesign merely to satisfy the dependency's aesthetic examples. Keep feature changes consistent and review actual rendered behavior at relevant screen sizes. Include keyboard/focus, loading, empty, error, and success behavior where relevant.

Frontend-design is visual-design guidance, not a replacement for product requirements, interaction design, accessibility verification, or project engineering standards.

## Installation boundary

Agent Skills defines portable skill files; it does not standardize universal dependency installation, MCP connection configuration, slash-command registration, or usage telemetry. Use the host's supported installation mechanism. Prefer Superpowers' official host-specific install when available because it includes integration hooks; raw skill installation is not a guarantee of identical plugin behavior.

Before claiming setup complete, confirm the installed required skills are discoverable in the target agent and the Cadence MCP `whoami` call succeeds. On hosts without required MCP/repository/Git capabilities, report that creation/build is unavailable rather than advertising full compatibility from skill-file support alone.
