![GitHub last commit](https://img.shields.io/github/last-commit/yelbolt/unoff-claude-plugin?color=informational) ![GitHub](https://img.shields.io/github/license/yelbolt/unoff-claude-plugin?color=informational)

# unoff on Claude Code

A [Claude Code](https://code.claude.com/) plugin for building, debugging, and extending **Figma** and **Penpot** plugins on the unoff stack — Preact UI, Canvas bridge, `@unoff/ui`, Nanostores, Tolgee, Supabase, and the dual Vite build.

It ships the [unoff skill library](https://github.com/yelbolt/unoff-skills) plus five layer-specialized agents that plan, implement, and gate changes across the architecture.

## Install

From inside Claude Code, add the [yelbolt marketplace](https://github.com/yelbolt/claude-marketplace) once, then install the plugin:

```bash
/plugin marketplace add yelbolt/claude-marketplace
/plugin install unoff@yelbolt
```

The marketplace is shared with the other yelbolt plugins, so `/plugin marketplace add` is only needed the first time. To pick up a new release later:

```bash
/plugin marketplace update yelbolt
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
| `unoff-create-plugin` | Master index for the unoff plugin skill library — Canvas, Bridge, UI, Config, and Externals layers   | [yelbolt/unoff-skills](https://github.com/yelbolt/unoff-skills)          |

Skills are sourced from [skills.sh](https://skills.sh) and vendored into `skills/`, with provenance tracked in `skills-lock.json`. Installing the plugin is enough — you do not need to add them separately.

### Using the skills without the plugin

The same library can be pulled into an unoff plugin project on its own, for a different agent or a pinned copy in the repo:

```bash
unoff add skills                      # git submodule, via @unoff/cli
npx skills add yelbolt/unoff-skills   # skills.sh, any agent
```

`unoff add skills` tracks the library as a git submodule you update with `git submodule update --remote` — best when you want the version pinned in your project's history. `npx skills add` copies the files and installs into every detected agent directory (`.claude/`, `.cursor/`, …) — best for quick, agent-agnostic use.

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
git clone https://github.com/yelbolt/unoff-claude-plugin.git
cd unoff-claude-plugin
claude plugin validate .
```

To refresh the vendored skills from source:

```bash
npx skills add yelbolt/unoff-skills -s unoff-create-plugin -y --copy
mv .claude/skills/unoff-create-plugin skills/unoff-create-plugin
rm -rf .agents .claude .kiro
```

The CLI fans out into per-agent directories (`.agents/`, `.claude/`, `.kiro/`); a plugin needs the skill in `skills/`, so the payload is relocated and the extra directories dropped. Keep `skills-lock.json` — its `skillPath` is rewritten to the plugin destination (`skills/unoff-create-plugin/SKILL.md`).

## License

[Mozilla Public License 2.0](LICENSE)
