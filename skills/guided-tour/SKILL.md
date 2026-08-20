---
description: >-
  Help the user build a mental model of a codebase through one grounded code
  concept at a time. Use for implementation reviews, unfamiliar areas, pattern
  examples, or questions about how project pieces connect.
argument-hint: Optional feature, file, symbol, pattern, or question
icon: Sparkles
command: guided-tour
---

# Guided Tour

Teach one codebase idea per turn. The goal is a durable mental model, not exhaustive documentation.

## Focus

Treat everything after `/guided-tour` as the focus. It may be a feature, file, symbol, pattern, or question. Ignore conversational prefixes such as `for`, `about`, or `around`. If no focus is supplied, infer a useful starting point from current docs, issues, or diffs.

## Workflow

1. Read relevant durable docs and inspect the source before explaining behavior. Prefer definitions and references to broad searches.
2. Choose one concept that connects product intent to implementation.
3. Show a real excerpt under 50 lines with its file path and starting line when available.
4. Explain what it does, its important collaborators, and why the concept matters.
5. Stop. Answer questions before advancing; continue only when the user asks.

## Response

````md
## Key Idea N: <Name>

```<language>
<real excerpt under 50 lines>
```

**What it does**

- ...

**Who it connects with**

- ...

**Why it matters**

- ...

Say `next` and I'll walk through another key idea.
````

Use repository vocabulary and ground claims in inspected code. State any remaining uncertainty. Do not turn the tour into an audit, refactor, or implementation session unless asked.
