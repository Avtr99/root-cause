# antidote

Stop AI agents from patching symptoms. Fix the root cause instead of masking the problem.

## The one-line rule

> Before you write any fix, ask: "What is the simplest change that makes this fix unnecessary?"

The mechanism works in any language. TypeScript and Zod, Go structs, Rust enums, Kotlin sealed classes, and Java records all let you make an invalid state unrepresentable instead of detecting it after the fact. Worked examples are in [`SKILL.md`](./SKILL.md).

## The test

> If a new developer adds a code path tomorrow without reading your fix, does the bug come back?

If yes, it was a patch. Go back and find the root cause.

## More

See the [root README](../../README.md) for install instructions, benchmark results, and the full file list.
