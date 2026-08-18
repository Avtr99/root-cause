# Benchmark

## How the skill was tested

The `root-cause` skill was evaluated with the [skill-eval-harness](https://github.com/adewale/skill-eval-harness), an open-source CLI that measures how much an Agent Skill improves model output. The harness runs the same case, model, and repetition with and without the skill, then reports the difference in pass rate.

### Test cases

Each test gives the model a piece of bugged code and asks it to fix the root cause. A script oracle then parses, compiles, and runs the model output. The oracle checks that the fix is *structural* — the bad state is made impossible — not a *patch* that hides the bug behind a guard on one code path.

Four cases, each run 2 times with the skill and 2 times without it (8 runs per arm):

1. **c-process-guards** — refactor a guard-bloated function into structural typing. The fix replaces scattered `if data is None` / `if "items" not in data` guards with a Pydantic model that makes invalid data impossible to construct.
2. **c-config-leak** — a `base_url` field leaks into requests. The fix removes the field and uses `Literal["gemini"]` for the provider, so the field cannot exist.
3. **c-state-machine** — order status transitions are checked with scattered string comparisons across files. The fix replaces the strings with an Enum and a single transition method.
4. **c-adversarial-quickfix** — the prompt tells the model "production is down, just add try/except." The fix rejects the patch and uses a typed model instead.

A run passes when the oracle confirms the output is a structural fix. A run fails when the output is a patch (a guard, a sanitizer, a swallowed error) or when it does not compile.

### Models

Five models were tested through the harness's `claude` runner (Claude Code's `claude -p` non-interactive mode):

- Claude Haiku 4.5
- Claude Sonnet 5
- GLM 5.2
- tencent/hy3
- moonshotai/kimi-k3

## Results

Pass rate = the share of runs where the model's output passed the script oracle (the fix was structural, compiled, and made the bad state impossible).

| Model | Pass rate with skill | Pass rate without skill | Improvement |
|---|---|---|---|
| Claude Sonnet 5 | 56% | 0% | **+56 points** |
| GLM 5.2 | 56% | 0% | **+56 points** |
| moonshotai/kimi-k3 | 50% | 0% | **+50 points** |
| Claude Haiku 4.5 | 50% | 31% | **+19 points** |
| tencent/hy3 | 44% | 0% | **+44 points** |

## What the numbers mean

Four of the five models — GLM 5.2, Sonnet 5, tencent/hy3, and kimi-k3 — passed **zero** runs without the skill. They patch symptoms by default: a bug appears, they add a guard. With the skill, they find the structural fix instead, jumping to 44–56% pass rates.

Claude Haiku 4.5 already finds structural fixes on its own some of the time (31% without the skill). The skill raised that to 50%. There is less room to improve when the model already has some structural-fix instinct. 

The skill's value depends on the model. A model that already favors structural fixes benefits less. A model that defaults to patching benefits a lot.

## How to reproduce

The harness and run data live in the [skill-eval-harness](https://github.com/adewale/skill-eval-harness) repository.

```bash
# Install the harness
uv tool install git+https://github.com/adewale/skill-eval-harness.git@main

# Run the skill against a model (Claude Haiku example)
python scripts/run_round_robin.py --runner claude --model haiku \
  --runs-per-variant 2 --output round-robin-results-haiku.json
```

Results are saved as `round-robin-results-<model>.json` in the harness repo root. Each row records the model, the pass rate with the skill, the pass rate without the skill, and the improvement.
