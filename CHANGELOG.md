# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [0.2.4] - 2026-10-18

### Added

- `.claude-plugin/icon.png` — plugin icon for the marketplace listing
- `.claude-plugin/plugin.json` — `documentationUrl`, `supportUrl`, `privacyPolicyUrl` and `termsOfServiceUrl` listing metadata
- `agents/README.md` — YAML frontmatter with a `description`, to satisfy the plugin validator

### Removed

- `README.md` — the **MCP servers** and **Configuration** sections, stale since the MCP servers and user configuration were removed in 0.1.1; they still documented `penpot_url` and `penpot_token`, which the plugin validator flagged as a credential sent to a remote URL
- `README.md` — the shields.io badges, flagged by the validator as a remote URL host

## [0.2.3] - 2026-09-03

### Changed

- `ui/i18n.md` — Best Practices gains an **Avoid Em Dashes and En Dashes** rule: translation strings must not use em dashes (—) or en dashes (–), with a comma, colon, parentheses, or period as the replacement, and a hyphen-minus for ranges

## [0.2.2] - 2026-09-01

### Changed

- **`unoff-create-plugin` skill library documents the `private get features()` pattern**, matching the refactor shipped in the Figma and Penpot templates (`@unoff/cli` 0.4.6). Every reference that used to show `const features = Component.features(planStatus, config, service, editor)` computed once in `render()` now shows a `private get features()` getter declared right after `static features` and right before `constructor` — every call site reads `this.features.X` instead of the four-argument static call
  - `config/feature-flags.md` gains a new **Always Go Through the `features` Getter** section with a ✅/❌ comparison and the rationale: a component often checks the same feature in more than one place (`isActive`/`isBlocked`/`isNew` on one entry, or the same entry reused in a `Menu` option and in `render()`), and the getter is the one place that needs to know whether the call args come from `this.props` or `this.state`
  - `ui/component-patterns.md` gains the matching **Always Read It Through `this.features`** section
  - `config/credits-system.md`, `ui/component-library.md`, `ui/components/actions.md`, `ui/components/forms.md`, `ui/accessibility.md`, `ui/components/layout.md` and `ui/performance.md` — every code sample updated from `features.X` to `this.features.X`, and the "Use Static Features Pattern" best-practice examples in `component-library.md` and `performance.md` now show the getter declared alongside the static method
- `skills-lock.json` — `unoff-create-plugin` skill hash refreshed to match the updated source in [yelbolt/unoff-skills](https://github.com/yelbolt/unoff-skills)

## [0.2.1] - 2026-08-24

### Added

- `agents/README.md` — the five agents documented in one place: what each owns, how the architect decides whom to spawn, and how context is loaded. The directory is described as the **neutral source of truth** shared with [`@unoff/cli`](https://github.com/yelbolt/unoff-cli) (`unoff add agents`), which emits the same definitions in the format each assistant expects — so a change to an agent is made here once instead of once per di<stribution

### Changed

- **`unoff-plugin-architect` now spawns only the specialists a change actually needs.** Each subagent is a cold start that reloads its own context, so an unnecessary one costs as much as a real one and returns nothing. The architect carries an explicit routing table — a change confined to `src/app/` goes to `unoff-ui` alone, a storage fix to `unoff-canvas-bridge` alone — and is told to make small, contained edits itself rather than delegate them. Concurrency applies to the specialists a change genuinely needs, and is explicitly not a reason to fan out to all three
  - `unoff-conformance-reviewer` is no longer invoked unconditionally. It gates multi-layer work, anything touching the message contract or platform parity, and any explicit request — but not a one-line fix, where a review that can only confirm what is already known is cost without information
- **`unoff-ui` and `unoff-canvas-bridge` drop to `effort: medium`.** Together they account for the majority of subagent usage, and most of their work is applying documented patterns rather than novel reasoning — the skill library already carries the decisions. `unoff-plugin-architect` and `unoff-platform-services` stay at `effort: high`, where planning and build/config changes still benefit

## [0.2.0] - 2026-08-15

### Added

- **`unoff-create-plugin/core.md` — one file every agent loads first, and the single source of truth for the facts they all need**: stack conventions, the architecture, the four-point message contract, and the Figma/Penpot differences table. Those tables previously lived in up to four places at once (the skill index, the agent definitions, the rules template, individual layer files), so every agent paid for them repeatedly and any correction had to be made four times to stick
- `ui/components/` — the `@unoff/ui` reference split by family (`actions`, `forms`, `layout`, `feedback`, `css-classes`), reached from `ui/component-library.md`
- `externals/design/` — the Figma and Penpot design-implementation workflows as separate files, reached from `externals/implement-design.md`. A Figma task no longer loads the Penpot procedure, or the reverse

### Changed

- **The skill library is now loaded by need instead of by layer, cutting what an agent reads for a typical task by roughly 43%.** Measured on representative tasks: choosing and using a form component 51.5 KB → 28.9 KB, implementing a design on Penpot 42.5 KB → 18.7 KB, on Figma 42.5 KB → 25.1 KB, wiring a Penpot bridge action 22.7 KB → 17.5 KB
  - `SKILL.md` is a pure routing index — 12.8 KB → 6.5 KB, paid by *every* agent on *every* task, so it was the single most expensive file in the library
  - Routing is two-level: the index points at entry files, and entry files point at their own detail files. Splitting a large reference no longer touches the index
  - `ui/component-library.md` 38.7 KB → 8.5 KB and `externals/implement-design.md` 29.7 KB → 4 KB are now entry files that route on
  - Deduplication was deliberately conservative where content only looked repeated. The Canvas and Bridge files gave up 9.3%, and three were left untouched after review — a smaller saving than a mechanical pass would have claimed, and no lost information
- **`unoff-plugin-architect` fixes the message contract before delegating**, writing out every `type`, its payload shape, its direction, and the union members to add. Because the contract is settled up front, `unoff-canvas-bridge`, `unoff-ui` and `unoff-platform-services` no longer negotiate through the architect and run **concurrently** on disjoint paths. Serialisation is now the exception, reserved for real data dependencies
- **`unoff-conformance-reviewer` is scoped to the diff and runs its mechanical checks first.** It ran all seven checklist sections against every change and re-read layer documentation to confirm rules it already had; it now maps changed paths to the sections worth running, greps for the anti-patterns it used to look for by reading, and drops to `effort: medium`. It is the last gate before work is declared done, so its cost sat entirely on the critical path
- Agent definitions keep short guardrails inline and link out for the full tables. A system prompt is loaded once and cached, whereas reading `core.md` costs a tool call — so the duplication that matters to remove is between *files*, not between a prompt and a file
- The rules template no longer hardcodes the list of skill files. It points at `core.md` and `SKILL.md` instead, so future reorganisation of the library cannot leave it describing paths that moved

## [0.1.1] - 2026-08-03

### Removed

- MCP servers - Legacy from the UI Color Palette Claude plugin
- User configuration - Legacy from the UI Color Palette Claude plugin

## [0.1.0] - 2026-08-03

### Added

- Initial plugin manifest for `unoff` (skills, agents, MCP servers, Penpot user config)
- `unoff-create-plugin` skill library vendored into `skills/` from [skills.sh](https://skills.sh) (`npx skills add yelbolt/unoff-skills`), with provenance in `skills-lock.json`
- `unoff-plugin-architect` agent — platform resolution, layer decomposition, sequencing, delegation
- `unoff-canvas-bridge` agent — Canvas API, storage, document generation, UI↔Canvas messaging, Figma/Penpot parity
- `unoff-ui` agent — Preact components, `@unoff/ui`, Nanostores, theming, Tolgee i18n, accessibility, types
- `unoff-platform-services` agent — global config, feature flags, credits, Vite build, external services, payments, design implementation
- `unoff-conformance-reviewer` agent — quality gate for stack conventions, message contracts, platform parity, and build integrity
- MCP servers: `figma`, `figma-desktop`, `penpot`
- `settings.json` defaulting to the `unoff-plugin-architect` agent
- Distribution through the [yelbolt marketplace](https://github.com/yelbolt/claude-marketplace) — `/plugin install unoff@yelbolt`
- README and Mozilla Public License 2.0
