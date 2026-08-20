# antidote

[![License: MIT](https://img.shields.io/badge/license-MIT-green)](./LICENSE)

> Stop AI agents from patching symptoms. Fix the root cause instead of masking the problem.

When code breaks, AI agents default to band-aids: null checks, swallowed errors, and 30-line workarounds. They hide bugs under layers of guards and sanitizers that rot over time.

`antidote` forces the agent to fix the actual cause:
- **Validate once at the boundary** instead of checking everywhere downstream.
- **Use the right type or schema** so invalid data can't reach internal code.
- **Delete the broken path** instead of writing glue code around it.

One clean fix replaces a dozen defensive guards.

The skill prompt and its rules live in [`skills/antidote/SKILL.md`](./skills/antidote/SKILL.md). This README covers why you'd install it, the benchmark results, and how to install it.

---

## Benchmark

The skill was tested with the [skill-eval-harness](https://github.com/adewale/skill-eval-harness) across five models. Each test gives the model bugged code and asks it to fix the root cause; a script oracle compiles and runs the output to check that the fix is structural, not a patch. Four cases, 2 runs per arm. Pass rate = share of runs that passed the oracle.

![Benchmark results: structural-fix pass rate by model, with vs without the antidote skill](./docs/benchmark.svg)

<details>
<summary>Raw numbers</summary>

| Model | With skill | Without skill | Improvement |
|---|---|---|---|
| Claude Sonnet 5 | 56% | 0% | **+56 points** |
| GLM 5.2 | 56% | 0% | **+56 points** |
| moonshotai/kimi-k3 | 50% | 0% | **+50 points** |
| Claude Haiku 4.5 | 50% | 31% | **+19 points** |
| tencent/hy3 | 44% | 0% | **+44 points** |

</details>

Four of the five models passed zero runs without the skill — they patch symptoms by default. With the skill, they find the structural fix instead. Full methodology and reproduction commands: [`docs/benchmark.md`](./docs/benchmark.md).

---

## Install

This skill follows the open [Agent Skills](https://agentskills.io) standard. It works in Claude Code, Codex, Cursor, Devin, Cline, GitHub Copilot, and other popular AI coding platforms.

### Universal (recommended)

```bash
npx skills add Avtr99/antidote
```

This command detects supported agents on your machine and installs the skill into each agent's native skills directory.

```bash
npx skills add Avtr99/antidote --global          # all projects, not just this one
npx skills add Avtr99/antidote --skill antidote  # install only this skill
npx skills add Avtr99/antidote --list            # preview without installing
```

### Claude Code

```bash
claude plugin marketplace add Avtr99/antidote
claude plugin install antidote@avtr-antidote
```

### Codex / ChatGPT

```bash
codex plugin marketplace add Avtr99/antidote
codex plugin install antidote@avtr-antidote
```

Then install from the Plugins directory in ChatGPT Work or the Codex desktop app.

### GitHub Copilot CLI

```bash
copilot plugin marketplace add Avtr99/antidote
copilot plugin install antidote@avtr-antidote
```

### Cursor

```bash
cursor plugin install Avtr99/antidote
```

### Devin

```bash
devin plugins install Avtr99/antidote
```

Verify with `devin plugins list`. Plugins load at session start, so start a new Devin session after installing.

### Grok Build CLI

```bash
grok plugin marketplace add Avtr99/antidote
grok plugin install antidote@avtr-antidote
```

Or install directly: `grok plugin install Avtr99/antidote`.

### Pi

Copy [`.pi/extensions/antidote.ts`](./.pi/extensions/antidote.ts) into your Pi workspace's `.pi/extensions/` directory and restart Pi.

### OpenCode

Add antidote to the `plugin` array in your `opencode.json` and restart OpenCode. See [`.opencode/INSTALL.md`](./.opencode/INSTALL.md) for full instructions.

```json
{
  "plugin": ["antidote@git+https://github.com/Avtr99/antidote.git"]
}
```

### Manual

Copy [`skills/antidote/`](./skills/antidote/) into the skills directory of your agent:

| Agent | Path |
|---|---|
| Claude Code | `.claude/skills/antidote/` |
| Codex | `.agents/skills/antidote/` |
| Cursor | `.cursor/skills/antidote/` |
| Devin | `.devin/skills/antidote/` |
| Generic / cross-agent | `.agents/skills/antidote/` |

---

## Usage

The skill activates on its own when the agent writes or reviews code. You can also invoke it by name:

- **Slash command** (Claude Code, Codex, Cursor, Devin): `/antidote`
- **Natural language:** "use antidote", "audit for overcomplication", "find the root cause"

It has two modes — **fix** (one bug) and **audit** (a whole codebase) — described in [`SKILL.md`](./skills/antidote/SKILL.md).

## What is inside

| File | Purpose |
|---|---|
| [`skills/antidote/SKILL.md`](./skills/antidote/SKILL.md) | The skill — the prompt that the agent reads |
| [`skills/antidote/README.md`](./skills/antidote/README.md) | Human-facing docs for the skill |
| [`plugin.json`](./plugin.json) | Root plugin manifest (Devin, Antigravity, and other root-native agents) |
| [`package.json`](./package.json) | NPM package manifest for Pi and OpenCode plugin installs |
| [`.claude-plugin/marketplace.json`](./.claude-plugin/marketplace.json) | Claude Code plugin marketplace manifest |
| [`.claude-plugin/plugin.json`](./.claude-plugin/plugin.json) | Claude Code plugin manifest |
| [`.codex-plugin/plugin.json`](./.codex-plugin/plugin.json) | Codex / ChatGPT plugin manifest |
| [`.agents/plugins/marketplace.json`](./.agents/plugins/marketplace.json) | Codex / ChatGPT plugin marketplace manifest |
| [`.github/plugin/marketplace.json`](./.github/plugin/marketplace.json) | GitHub Copilot CLI plugin marketplace manifest |
| [`.github/plugin/antidote/plugin.json`](./.github/plugin/antidote/plugin.json) | Isolated GitHub Copilot CLI plugin manifest and package |
| [`.cursor-plugin/plugin.json`](./.cursor-plugin/plugin.json) | Cursor plugin manifest |
| [`.devin-plugin/plugin.json`](./.devin-plugin/plugin.json) | Devin plugin manifest |
| [`.grok-plugin/plugin.json`](./.grok-plugin/plugin.json) | Grok Build plugin manifest |
| [`.grok-plugin/marketplace.json`](./.grok-plugin/marketplace.json) | Grok Build plugin marketplace manifest |
| [`docs/agent-portability.md`](./docs/agent-portability.md) | Which adapter each host uses |
| [`docs/benchmark.md`](./docs/benchmark.md) | Evaluation methodology and results |
| [`.pi/extensions/antidote.ts`](./.pi/extensions/antidote.ts) | Pi coding agent extension |
| [`.opencode/INSTALL.md`](./.opencode/INSTALL.md) | OpenCode installation guide |
| [`.opencode/plugins/antidote.js`](./.opencode/plugins/antidote.js) | OpenCode plugin |

## Compatibility

`antidote` is a pure-prompt skill. It has no scripts, no network calls, and no dependencies. It works in any agent that reads the `SKILL.md` format. The adapter manifests and plugins in this repo (`.claude-plugin/`, `.codex-plugin/`, `.cursor-plugin/`, `.devin-plugin/`, `.grok-plugin/`, `.github/plugin/`, `.pi/`, `.opencode/`) add native install support on top.

## License

MIT — see [LICENSE](./LICENSE).
