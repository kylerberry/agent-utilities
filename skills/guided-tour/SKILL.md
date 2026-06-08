---
description: >-
  Help the user build a mental model of a codebase through a guided tour,
  walking one key idea at a time with small code excerpts and clear
  explanations. Use when the user wants to review recent work, catch up on
  implementation progress, explore an unfamiliar area, find examples of a code
  pattern, or understand how pieces of a project connect.
argument-hint: Optional focus, such as an area of code, concept, issue, feature, file, symbol, pattern, or question
icon: Sparkles
command: guided-tour
---

# Guided Tour

Guide the user through a codebase by teaching one key idea at a time.

The goal is not to exhaustively document the project. The goal is to help the human build durable mental models.

## Inputs

The command accepts an optional freeform focus argument. Treat everything after `/guided-tour` as the requested tour focus.

Examples:

- `/guided-tour`
- `/guided-tour for examples of webhooks in this codebase`
- `/guided-tour red tests`
- `/guided-tour src/session.ts`
- `/guided-tour how does challenge resume work?`

If the argument starts with conversational filler such as `for`, `about`, `on`, or `around`, strip that mentally and use the rest as the focus.

The focus may be:

- An issue or feature: `Issue 5`, `red tests`, `BYOK setup`
- A file or symbol: `src/engine.ts`, `captureColdAnswer`
- A broad area: `workspace materialization`, `session persistence`
- A code pattern: `examples of webhooks`, `database writes`, `CLI command parsing`
- A question: `how does the REPL know what challenge I am in?`

If no argument is provided, infer a useful starting point from recent issue status, docs, diffs, or the project's current implementation state.

## Workflow

1. **Orient first**
   - Read relevant durable docs such as `ISSUES.md`, `PRD.md`, `README.md`, or plans.
   - Inspect the relevant source using code-navigation tools before explaining behavior.
   - Prefer definitions and references over broad grep when looking for source symbols.
   - If code-index tools are unavailable, use file outlines and targeted line reads.
2. **Pick one key idea**
   - Choose a concept that explains how the system works, not just a random function.
   - Favor ideas that connect product intent to implementation.
   - If the user supplied a focus argument, choose the first key idea inside that scope.
3. **Show a small code excerpt**
   - Present fewer than 50 lines of code.
   - Use real code from the project.
   - Include a path reference and starting line when possible.
   - Do not paste huge files or unrelated context.
4. **Explain briefly**
   - Cover what it does in plain language.
   - Cover who it connects with: callers, collaborators, persisted state, docs, tests, or user flows.
   - Cover why it matters: the mental model, product boundary, or architectural significance.
5. **Stop and wait**
   - End by inviting questions or a `next`-style prompt.
   - Do not continue to the next key idea until the user asks.
   - If the user asks a question, answer it before moving on.

## Response Shape

Use this structure:

````md
## Key Idea N: <Name>

```<language>
<under 50 lines of code>
```

**What it does**

- ...

**Who it connects with**

- ...

**Why it matters**

- ...

Say `next` and I'll walk through another key idea.
````

## Teaching Style

- Be concise, direct, and explanatory.
- Build from product intent toward code mechanics.
- Use project vocabulary consistently.
- Prefer mental model summaries over implementation trivia.
- Avoid over-validating or cheerleading.
- Do not modify files unless the user explicitly asks for implementation work.
- Do not turn the guided tour into a refactor, audit, or bug-fix session unless asked.

## Good Key Ideas

Examples of useful key ideas:

- The deterministic engine owns progression.
- The model teaches but does not decide gates.
- Workspace-local `.reskill/` state separates learner work from global config.
- Course manifests are validated data, not executable course code.
- `/test` is context-aware and classifies red/green by exit code.
- The REPL is a thin interaction layer over session and engine primitives.
- Transcripts are append-only learning memory.
- HITL seams mark learner-owned critical work.

## Guardrails

- Keep each turn focused on one concept.
- Keep code excerpts under 50 lines.
- Ground explanations in actual code before making claims.
- If uncertain, say what was verified and what remains uncertain.
- If the user gives a narrow focus, do not wander into unrelated architecture.
