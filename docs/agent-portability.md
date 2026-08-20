# Agent Portability

`root-cause` is an agent-portable skill distribution. The file in `skills/root-cause/SKILL.md` holds the core behavior; host-specific files are thin adapters that make that behavior easy to load in a given agent.

## Supported adapters

| Host | Files | Notes |
|------|-------|-------|
| **Universal (75+ agents)** | `skills/root-cause/SKILL.md` | `npx skills add Avtr99/root-cause` auto-detects every supported agent and installs into its native skills dir. No manifest required. |
| **Claude Code** | `.claude-plugin/marketplace.json`, `.claude-plugin/plugin.json` | Full plugin install via `claude plugin marketplace add Avtr99/root-cause`, then `claude plugin install root-cause@avtr-root-cause`. |
| **Codex / ChatGPT** | `.codex-plugin/plugin.json`, `.agents/plugins/marketplace.json` | Plugin install via `codex plugin marketplace add Avtr99/root-cause`, then `codex plugin install root-cause@avtr-root-cause`. |
| **GitHub Copilot CLI** | `.github/plugin/marketplace.json` | `copilot plugin marketplace add Avtr99/root-cause`, then `copilot plugin install root-cause@avtr-root-cause`. |
| **Cursor** | `.cursor-plugin/plugin.json` | `cursor plugin install Avtr99/root-cause`, or search "root-cause" in the plugin marketplace. |
| **Devin** | `plugin.json` (root), `.devin-plugin/plugin.json` | `devin plugins install Avtr99/root-cause`. The root `plugin.json` is the canonical manifest; `.devin-plugin/plugin.json` mirrors it for Devin's native discovery. |
| **Generic agents** (OpenCode, Cline, Qoder, Codewhale…) | `skills/*/SKILL.md` | Read `skills/` natively. `npx skills add` copies `SKILL.md` into the agent's native skills dir. |

## Principles

- **Single source of truth.** `skills/root-cause/SKILL.md` is the only file that defines behavior. Everything else points at it.
- **Keep adapters thin.** When a host supports skills or hooks, point it at the existing `skills/` files.
- **No runtime dependencies.** `root-cause` is a pure-prompt skill — no scripts, no network, no bundled code. Every adapter is a static JSON manifest.

## Plugin-root conventions

Each host uses a different plugin-root convention. This is by design — each host's manifest format is different:

- **Claude Code**: The marketplace `source` points at `./` (the repo root). The plugin manifest lives at `.claude-plugin/plugin.json`. Claude Code treats the repo root as the plugin root and discovers `skills/root-cause/SKILL.md` via the `skills/` directory convention.
- **Cursor**: The plugin manifest at `.cursor-plugin/plugin.json` points at `./skills/`. Cursor reads the `skills/` directory and discovers `root-cause/SKILL.md`.
- **Devin**: The root `plugin.json` is the canonical manifest. `.devin-plugin/plugin.json` mirrors it for Devin's native plugin discovery. Both point at `./skills/`.
- **GitHub Copilot**: The marketplace `source` points at `./skills/root-cause`. Copilot reads `SKILL.md` directly — no plugin manifest needed.
- **Codex / ChatGPT**: The marketplace `source` is `{ "source": "local", "path": "./" }` (the repo root, `./`-prefixed per Codex docs). The plugin manifest lives at `.codex-plugin/plugin.json` and points at the skills directory via `"skills": "./skills/"` — Codex scans that directory for skill subdirectories and finds `root-cause/SKILL.md`. This is the [official Codex marketplace format](https://developers.openai.com/codex/plugins/build/plugins) — the `{ "source": "local", "path": ... }` object form is required by Codex, and the plugin root is the repo root because Codex expects skills in subdirectories of the plugin root.

## Adding a new host

1. If the host reads the universal `SKILL.md` format → no work needed. `npx skills add` already covers it.
2. If the host has a native plugin/extension format → add a tiny manifest file at the repo root (or in a host-specific directory) that points at `skills/root-cause/`. Document it in the table above.
