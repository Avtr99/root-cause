# Agent Portability

`antidote` is an agent-portable skill distribution. The file in `skills/antidote/SKILL.md` holds the core behavior; host-specific files are thin adapters that make that behavior easy to load in a given agent. The GitHub Copilot package is an isolated mirror validated against this canonical skill.

## Supported adapters

| Host | Files | Notes |
|------|-------|-------|
| **Universal** | `skills/antidote/SKILL.md` | `npx skills add Avtr99/antidote` auto-detects supported agents and installs into each one's native skills dir. No manifest required. |
| **Claude Code** | `.claude-plugin/marketplace.json`, `.claude-plugin/plugin.json` | Full plugin install via `claude plugin marketplace add Avtr99/antidote`, then `claude plugin install antidote@avtr-antidote`. |
| **Codex / ChatGPT** | `.codex-plugin/plugin.json`, `.agents/plugins/marketplace.json` | Plugin install via `codex plugin marketplace add Avtr99/antidote`, then `codex plugin install antidote@avtr-antidote`. |
| **GitHub Copilot CLI** | `.github/plugin/marketplace.json`, `.github/plugin/antidote/plugin.json` | `copilot plugin marketplace add Avtr99/antidote`, then `copilot plugin install antidote@avtr-antidote`. |
| **Cursor** | `.cursor-plugin/plugin.json` | `cursor plugin install Avtr99/antidote`, or search "antidote" in the plugin marketplace. |
| **Devin** | `plugin.json` (root), `.devin-plugin/plugin.json` | `devin plugins install Avtr99/antidote`. The root `plugin.json` is the canonical manifest; `.devin-plugin/plugin.json` mirrors it for Devin's native discovery. |
| **Grok Build** | `.grok-plugin/plugin.json`, `.grok-plugin/marketplace.json` | `grok plugin marketplace add Avtr99/antidote`, then `grok plugin install antidote@avtr-antidote`. Or install directly: `grok plugin install Avtr99/antidote`. |
| **Pi** | `package.json`, `.pi/extensions/antidote.ts` | Pi extension that injects the antidote skill bootstrap on every session and registers `skills/` for native skill discovery. The `package.json` `pi` field tells Pi where to find the extension and skills. |
| **OpenCode** | `package.json`, `.opencode/plugins/antidote.js`, `.opencode/INSTALL.md` | OpenCode plugin that registers `skills/` and injects the antidote skill bootstrap. The `package.json` `main` points to the plugin entry point. Install by adding `antidote@git+https://github.com/Avtr99/antidote.git` to `opencode.json`. |
| **Generic agents** (Cline, Qoder, Codewhale…) | `skills/*/SKILL.md` | Read `skills/` natively. `npx skills add` copies `SKILL.md` into the agent's native skills dir. |

## Principles

- **Single source of truth.** `skills/antidote/SKILL.md` is the only file that defines behavior. Everything else points at it.
- **Keep adapters thin.** When a host supports skills or hooks, point it at the existing `skills/` files.
- **No runtime dependencies in the core skill.** `antidote` is a pure-prompt skill — no scripts, no network, no bundled code. Most adapters are static JSON manifests; hosts that require native plugins (Pi, OpenCode) use thin code shims that still load the canonical `SKILL.md`.

## Plugin-root conventions

Each host uses a different plugin-root convention. This is by design — each host's manifest format is different:

- **Claude Code**: The marketplace `source` points at `./` (the repo root). The plugin manifest lives at `.claude-plugin/plugin.json`. Claude Code treats the repo root as the plugin root and discovers `skills/antidote/SKILL.md` via the `skills/` directory convention.
- **Cursor**: The plugin manifest at `.cursor-plugin/plugin.json` points at `./skills/`. Cursor reads the `skills/` directory and discovers `antidote/SKILL.md`.
- **Devin**: The root `plugin.json` is the canonical manifest. `.devin-plugin/plugin.json` mirrors it for Devin's native plugin discovery. Both point at `./skills/`.
- **Grok Build**: The plugin manifest at `.grok-plugin/plugin.json` points at `./skills/`. The marketplace manifest at `.grok-plugin/marketplace.json` uses a `{ "source": "url", "url": ... }` source pointing at the Git repo, per the Grok marketplace format.
- **GitHub Copilot**: The marketplace `source` points at `./.github/plugin/antidote`, an isolated plugin package containing its own root `plugin.json` and `skills/antidote/SKILL.md`. The packaged skill is synchronized with the canonical `skills/antidote/SKILL.md` file.
- **Codex / ChatGPT**: The marketplace `source` is `{ "source": "local", "path": "./" }` (the repo root, `./`-prefixed per Codex docs). The plugin manifest lives at `.codex-plugin/plugin.json` and points at the skills directory via `"skills": "./skills/"` — Codex scans that directory for skill subdirectories and finds `antidote/SKILL.md`. This is the [official Codex marketplace format](https://developers.openai.com/codex/plugins/build/plugins) — the `{ "source": "local", "path": ... }` object form is required by Codex, and the plugin root is the repo root because Codex expects skills in subdirectories of the plugin root.
- **Pi and OpenCode**: Both rely on the root `package.json` as a packaging hook. Pi reads the `pi` field to locate `extensions` and `skills` paths. OpenCode installs the package as an npm-style module and loads the file declared in `main` (`.opencode/plugins/antidote.js`) as the plugin entry point.

## Adding a new host

1. If the host reads the universal `SKILL.md` format → no work needed. `npx skills add` already covers it.
2. If the host has a native plugin/extension format → add a thin manifest in a host-specific directory that points at the canonical skill, or add a synchronized package when the host requires components to live inside the plugin root. Document it in the table above.
