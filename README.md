![GitHub last commit](https://img.shields.io/github/last-commit/a-ng-d/unoff-claude-plugin?color=informational) ![GitHub](https://img.shields.io/github/license/a-ng-d/unoff-claude-plugin?color=informational)

# unoff on Claude Code

A [Claude Code](https://code.claude.com/) plugin for building, debugging, and extending **Figma** and **Penpot** plugins on the unoff stack — Preact UI, Canvas bridge, `@unoff/ui`, Nanostores, Tolgee, Supabase, and the dual Vite build.

It ships the [unoff skill library](https://github.com/a-ng-d/unoff-skills) plus five layer-specialized agents that plan, implement, and gate changes across the architecture.

## Install

```bash
/plugin install unoff
```

## Agents

| Agent                        | Owns                                                                                                 |
| ---------------------------- | ---------------------------------------------------------------------------------------------------- |
| `unoff-plugin-architect`     | **Default.** Resolves the target platform, decomposes work by layer, sequences it, delegates, assembles |
| `unoff-canvas-bridge`        | `src/index.ts`, `src/canvas/`, `src/bridges/` — Canvas APIs, storage, document generation, UI↔Canvas messaging |
| `unoff-ui`                   | `src/app/` — Preact components, `@unoff/ui`, Nanostores, theming, Tolgee i18n, a11y, types            |
| `unoff-platform-services`    | `global.config.ts`, Vite build, feature flags, credits, Supabase/Sentry/Mixpanel, payments, design implementation |
| `unoff-conformance-reviewer` | Quality gate — stack conventions, message-contract completeness, Figma/Penpot parity, build integrity |

The architect always finishes by handing the diff to the reviewer.

## Skills

| Skill                 | Description                                                                                          | Source                                                                |
| --------------------- | ---------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------- |
| `unoff-create-plugin` | Master index for the unoff plugin skill library — Canvas, Bridge, UI, Config, and Externals layers   | [a-ng-d/unoff-skills](https://github.com/a-ng-d/unoff-skills)          |

Skills are sourced from [skills.sh](https://skills.sh) and vendored into `skills/`, with provenance tracked in `skills-lock.json`.

## MCP servers

| Server          | Transport | URL                                                        | Notes                                     |
| --------------- | --------- | ---------------------------------------------------------- | ----------------------------------------- |
| `figma`         | http      | `https://mcp.figma.com/mcp`                                | Design context for `implement-design`     |
| `figma-desktop` | http      | `http://127.0.0.1:3845/mcp`                                | Local Figma desktop app                   |
| `penpot`        | http      | `${penpot_url}/mcp/stream?userToken=${penpot_token}`       | Requires the two user config values below |

## Configuration

| Key            | Required | Description                                                              |
| -------------- | -------- | ------------------------------------------------------------------------ |
| `penpot_url`   | no       | Penpot base URL — `https://design.penpot.app` for Cloud, or your own host |
| `penpot_token` | no       | Penpot user token — Account → Integrations → MCP Server                   |

## Development

```bash
git clone https://github.com/a-ng-d/unoff-claude-plugin.git
cd unoff-claude-plugin
claude plugin validate .
```

To refresh the vendored skills from source:

```bash
npx skills add a-ng-d/unoff-skills -s unoff-create-plugin -y --copy
mv .claude/skills/unoff-create-plugin skills/unoff-create-plugin
rm -rf .agents .claude .kiro
```

The CLI fans out into per-agent directories (`.agents/`, `.claude/`, `.kiro/`); a plugin needs the skill in `skills/`, so the payload is relocated and the extra directories dropped. Keep `skills-lock.json` — its `skillPath` is rewritten to the plugin destination (`skills/unoff-create-plugin/SKILL.md`).

## License

[Mozilla Public License 2.0](LICENSE)
