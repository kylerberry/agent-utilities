# CRAFTS HITL Gating Skill

## When to use

Invoke this skill during the **R — Render** phase of `/craft` when the current issue slice explicitly requires a human to implement or choose the critical decision-bearing logic. This is the only required human-in-the-loop gate in the CRAFTS workflow.

## Overview

The HITL (Human-In-The-Loop) gate exists at the `TODO(human)` seam during Render. The agent scaffolds and tests up to the critical logic, leaves exactly one `TODO(human)` marker at the decision point, then pauses. The human fills the critical logic; the agent resumes verification and completion.

## When to pause

Pause for human input when:

- The issue slice is labeled **HITL implementation** — the agent scaffolds/tests, human owns the critical logic.
- The issue slice is labeled **HITL design/review** — implementation is agent-driven but requires human taste/content/design review.
- The critical logic involves a subjective choice (naming, UX copy, visual design, algorithmic trade-off) that the PRD or issue plan explicitly reserves for human judgment.
- The agent cannot meaningfully proceed without a decision that only the human can make.

Do NOT pause for:

- Routine refactoring, formatting, or test fixes.
- Clear-cut implementation where the acceptance criteria are unambiguous.
- Decisions that the agent can reasonably infer from the PRD or existing code patterns.

## How to pause

1. **Scaffold to the seam:** write all code up to but not including the critical logic. Write the surrounding tests, types, and structure.
2. **Leave exactly one `TODO(human)` marker** at the critical decision point. Make it specific:
   - Bad: `// TODO(human): implement this`
   - Good: `// TODO(human): decide the threshold for todo_filled — should we require marker removal, or is a substantive edit sufficient?`
3. **Summarize the context:** tell the human what has been scaffolded, what decision is needed, and what the acceptance criteria are.
4. **Stop processing.** Do not write code past the `TODO(human)` marker.

## How to resume

After the human fills the `TODO(human)` marker and signals readiness:

1. **Read the human's implementation** carefully. Do not assume it matches what you would have written.
2. **Run the tests** scoped to the changed area. Fix any test failures caused by integration mismatches.
3. **Continue the Render phase:** complete any remaining implementation, run the full test suite, then proceed to Assess.
4. **Remove the `TODO(human)` marker** from comments once the seam is filled and verified.

## After resuming

Return to the `/craft` full flow at the point where you left off:

- If you were mid-Render, finish Render (remaining tests, implementation, refactor).
- If Render is complete, proceed to **A — Assess**.
- `C`, `A`, `F`, `T`, and `S` remain agent-driven unless the human explicitly chooses to pause for review.
