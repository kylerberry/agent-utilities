# CRAFTS Workflow Skill

## When to use

Invoke this skill for every non-trivial task. Use the **full flow** for business logic, multi-file work, and anything crossing domain boundaries. Use the **lite flow** for config, scaffolding, and simple single-file fixes.

Start lite, then escalate to full if the task grows.

## Overview

CRAFTS is a sequential phase-gate workflow. Do not plan or execute phases in parallel per feature or issue; finish the current phase before moving to the next one.

## Full Flow: C → R → A → F → T → S

### C — Conceptualize

Define scope, test cases, implementation plan, and risks before coding.

- Read the relevant issue slice or user request thoroughly.
- Identify whether the work is AFK (agent can complete solo) or HITL (requires human at a critical seam).
- If multi-step, create or update a todo list before coding.
- Produce: scope boundary, acceptance criteria, file list, test strategy, and risk assessment.
- Stop here if the plan is unclear — do not proceed to Render with ambiguous requirements.

### R — Render (Test-Drive)

Write failing tests first, then implement the minimum change to pass, then refactor.

- **Red:** write the failing test from the plan. If you can't write it, return to Conceptualize.
- **Green:** write the minimum implementation to pass. No more.
- **Refactor:** clean up without breaking green. Repeat for each test case.
- Run lint, type checks, and format when all tests pass.
- If the slice requires a `TODO(human)` seam, pause here and invoke the `/craft-hitl` skill.

### A — Assess

Review the diff for quality, reuse, efficiency, and type correctness.

- Check for duplicated logic, missed edge cases, unclear naming.
- Verify type safety if applicable.
- Flag anything that should be fixed before proceeding.

### F — Fix

Address blocking issues from Assess. Re-run quality checks.

- High and medium severity first.
- Disagree with a finding? Document why instead of blindly fixing.

### T — Tighten

Run the security-hardening review for the diff and fix findings.

- Scan for injection risks, unsafe defaults, exposed secrets.
- Verify boundary enforcement where applicable.

### S — Sharpen

Capture durable lessons, gotchas, process updates, and any documentation changes so repo docs stay evergreen and aligned to code.

- Update the relevant domain docs (README, ADR, CLAUDE.md, PRD, ISSUES) with patterns established, gotchas discovered, conventions set during this task.
- Commit and push if applicable.

## Lite Flow: R → S

For config, scaffolding, and simple single-file fixes:

1. **R — Render:** make the smallest correct change. Write or update tests if the codebase already has them.
2. **S — Sharpen:** capture any doc updates and commit.

## Escalation Rules

- Start lite. If the task grows beyond a single file or requires domain reasoning, escalate to full.
- Never skip Assess and Tighten on code that crosses a trust boundary or handles user input.
- If Render reaches a `TODO(human)` seam, invoke `/craft-hitl` and pause for human input before continuing.
