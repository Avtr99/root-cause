# Changelog

All notable changes to `antidote` are documented here. The format follows [Keep a Changelog](https://keepachangelog.com/en/1.1.0/), and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [1.1.0] - 2026-08-20

### Added
- `.cursor-plugin/plugin.json` — native Cursor plugin install support.
- `.devin-plugin/plugin.json` — native Devin plugin install support.
- `.grok-plugin/plugin.json` and `.grok-plugin/marketplace.json` — native Grok Build plugin install support.
- `plugin.json` (repo root) — canonical plugin manifest for Antigravity, Qwen Code, Factory Droid, and other root-native agents.
- `repository` field added to all adapter manifests.
- `metadata` block (author, source, version) added to `SKILL.md` frontmatter per the Agent Skills spec.
- `docs/benchmark.svg` — visual benchmark chart with model logos and grouped bar comparison.
- Cursor, Devin, and Grok Build install sections in README.
- Cursor, Devin, and Grok Build entries in `docs/agent-portability.md`.

### Fixed
- Claude Code skill discovery: marketplace `source` changed from `./skills/root-cause` to `./` so Claude Code finds `skills/root-cause/SKILL.md` via the standard `skills/` convention. Previously the plugin installed but exposed no skills.
- Removed fabricated "75+ agents" claim from README and docs.
- Removed false "search for root-cause in the Cursor plugin marketplace" claim (not published there).

## [1.0.0] - 2026-08-18

### Added
- `skills/root-cause/SKILL.md` — the skill prompt. Evaluated with the [skill-eval-harness](https://github.com/adewale/skill-eval-harness) across five models (Claude Haiku 4.5, Claude Sonnet 5, GLM 5.2, tencent/hy3, moonshotai/kimi-k3). On a 4-case benchmark where a script oracle compiles and runs the model's fix to check it is structural (not a patch), the skill raised the structural-fix pass rate from 0% to 56% on Sonnet 5 and GLM 5.2, 0% to 50% on kimi-k3, 0% to 44% on hy3, and 31% to 50% on Haiku.
- Adapter manifests for Claude Code, Codex/ChatGPT, and GitHub Copilot CLI.
- `docs/agent-portability.md` — host adapter reference.
- `docs/benchmark.md` — evaluation methodology and per-model results.
- CI workflow validating the skill against the Agent Skills spec via `skills-ref`.
- MIT license.
