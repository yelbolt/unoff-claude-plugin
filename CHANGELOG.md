# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).


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
