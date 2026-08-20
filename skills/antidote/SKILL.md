---
name: antidote
description: "Stop AI agents from patching symptoms. Use this skill when coding, reviewing, refactoring, auditing, committing fixes, or adding guards, validation, workarounds, or 20+ lines without deleting code."
license: MIT
metadata:
  author: Avtr99
  source: https://github.com/Avtr99/antidote
  version: "1.1.0"
---

# Antidote

AI agents often fix symptoms. A bug appears, and the agent adds a check for that one path. The check does not remove the bug. It only hides it on that path. The next path that uses the same data does not have the check. The bug comes back. After many patches, the code holds together with guards that no one remembers.

Before you write a fix, ask this: "What is the smallest change that makes this fix unnecessary?" Use a stricter type, delete a code path, or use a library. This work looks larger than a patch, but it costs less over time. A patch is cheap today and expensive tomorrow. A structural fix is the reverse.

## Stop and rethink before you

- Write a function that only checks, cleans, or coerces a value before the rest of the code uses it. Can a stricter type, schema, or interface make the bad shape impossible to build? Then nothing must check for it.
- Add a defensive guard, such as a null check, an empty catch block, or a "this should not happen" branch. Ask why that state is reachable. Fix the cause, not the symptom.
- Work around a library or framework behavior. Read the docs for the intended pattern first. A workaround breaks on the next upgrade. The intended pattern does not.
- Duplicate logic in a new code path. Can both paths use one implementation?
- Write 50 or more lines for a "simple" bug. You likely fix a symptom. Find the root cause.
- Add a config flag for an edge case. The edge case may only exist because the design is wrong.

## A library is one option, not the default answer

The goal is the smallest correct fix. Sometimes the fix is a library. Sometimes it is a type. Sometimes it is a deleted code path. Sometimes it is a few honest lines. Do not add a dependency by reflex.

A library helps when the problem is hard and already solved: retries, caching, pagination, circuit breakers, state machines, config validation, and date parsing. Check what the project already uses before you add a new one.

A library does not help when the custom code it replaces is only a few lines. Importing a package to avoid one conditional is its own kind of overcomplication. Now you carry its API, its versions, and its bugs. The line to watch is not "custom code exists." It is "custom code re-implements something hard that a good library already does well."

## Two examples of the same fix

```python
# PATCH: clear the field on every write path; miss one, and the bug returns
def sanitize_order(order):
    if order["kind"] == "digital":
        order["shipping_address"] = None
    return order

# FIX: the variant has no such field, so nothing can set it
class DigitalOrder(BaseModel):
    kind: Literal["digital"]
    download_url: str

class PhysicalOrder(BaseModel):
    kind: Literal["physical"]
    shipping_address: Address
```

```python
# PATCH: every caller defends against the same bad shape
def process(data):
    if data is None: return
    if "items" not in data: return
    for item in data.get("items") or []:
        if not item or "name" not in item: continue
        ...

# FIX: validate once at the boundary, then trust the type
class RequestData(BaseModel):
    items: list[Item]

def process(data: RequestData):
    for item in data.items:
        ...  # item.name is guaranteed
```

You can use the same mechanism in any language. TypeScript, Zod, Go structs, Rust enums, Kotlin sealed classes, and Java records let you make an invalid state impossible. You do not need to detect it later.

## Checklist before you write the fix

1. State the root cause in one sentence. If you cannot, you do not understand it yet.
2. Check the structure. Would a stricter type, a merged code path, or a deleted feature make this fix unnecessary?
3. Check the library. Use a library only if the problem is hard and already solved. Skip it if a few lines will do.
4. Check what to delete. What code does this fix let you remove? If nothing, you add complexity, not resolve it.

## Red flags that you are patching, not fixing

- The fix is 20 or more lines and deletes nothing.
- It is a catch block that swallows an error or silently defaults.
- It guards against a state that "should not happen."
- It re-implements something hard that a good library already does well.
- It works around framework behavior instead of using the framework's pattern.
- It is a new write path instead of reusing the existing one.

## Audit mode

When the user asks for an audit, do these steps:

1. Scan for the patterns above. Record the file and line, the pattern, the risk, and the simpler option.
2. Rank by impact. Start with small structural changes that remove large amounts of defensive code.
3. Flag custom retry, cache, validation, or state-machine code. Replace it with a stricter type, structure, or library, whichever is smaller.
4. Output a prioritized plan. Do not start fixing until the user approves it.

## Fix mode

1. State the root cause in one sentence.
2. Propose the simplest fix that addresses it. If it is not simpler than what is there, it is the wrong fix.
3. Check if a type change, a structure change, or a library removes the need for custom logic. Pick the smallest option. Do not default to either.
4. Run tests before and after. If there are no tests, write a characterization test first.
5. Delete whatever the fix makes unnecessary.
6. Confirm that the bug is now structurally impossible, not just currently prevented.

## The test

> If a new developer adds a code path tomorrow without reading your fix, does the bug come back?

If the answer is yes, you wrote a patch. Go back and find the root cause.
