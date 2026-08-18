# Changelog

All notable changes to `root-cause` are documented here. The format follows [Keep a Changelog](https://keepachangelog.com/en/1.1.0/), and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [1.0.0] - 2026-08-18

### Added
- `skills/root-cause/SKILL.md` — the skill prompt. Evaluated with the [skill-eval-harness](https://github.com/adewale/skill-eval-harness) across five models (Claude Haiku 4.5, Claude Sonnet 5, GLM 5.2, tencent/hy3, moonshotai/kimi-k3). On a 4-case benchmark where a script oracle compiles and runs the model's fix to check it is structural (not a patch), the skill raised the structural-fix pass rate from 0% to 56% on Sonnet 5 and GLM 5.2, 0% to 50% on kimi-k3, 0% to 44% on hy3, and 31% to 50% on Haiku.
- Adapter manifests for Claude Code, Codex/ChatGPT, and GitHub Copilot CLI.
- `docs/agent-portability.md` — host adapter reference.
- `docs/benchmark.md` — evaluation methodology and per-model results.
- CI workflow validating the skill against the Agent Skills spec via `skills-ref`.
- MIT license.
