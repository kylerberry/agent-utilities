---
name: craft-hitl
command: craft-hitl
argument-hint: Optional mode, such as "full" or "lite"
icon: Hand
description: >-
  Phase-gate execution workflow for non-trivial HITL tasks. Same full flow
  (C→R→A→F→T→S) as /craft, but includes mandatory human-in-the-loop gating at
  TODO(human) seams during Render.
---

# CRAFTS HITL Workflow Skill

## When to use

Invoke this skill for issue slices explicitly labeled **HITL implementation** or **HITL design/review**, or any task where the PRD reserves a critical decision for human judgment.

This is the same CRAFTS phase-gate workflow as `/craft`, including its plan counsel gate, security-trigger policy, and global JSON report contract, but the **R — Render** phase includes mandatory human-in-the-loop gating. Work with non-empty `security_triggers` receives the same independent `craft-plan-security` review after C and before any Render edit; Tighten uses `craft-security-review`; untriggered work keeps the current HITL flow. The security agents use bundled guidance and no external security skill dependency. The agent scaffolds to the seam, pauses for human input, then resumes and completes the remaining phases.

If the task is unambiguously autonomous, use `/craft` instead.

## Overview

CRAFTS is a sequential phase-gate workflow. Do not plan or execute phases in parallel per feature or issue; finish the current phase before moving to the next one.

In HITL mode, the Render phase contains a mandatory pause. The human owns the critical decision-bearing logic; the agent owns everything before and after it.

Delegate each phase to its matching global subagent when the AgentSpawn tool is available, one call at a time. Full-flow work runs the same plan counsel gate as `/craft` — `craft-plan-feasibility` (feasibility and coherence) and `craft-plan-scope` on every task, plus `craft-plan-security` when `security_triggers` is non-empty — after C and before R. Wait for each report before proceeding, fixing blockers, or asking for clarification. Do not run CRAFTS subagents in parallel, except the counsel reviewers with each other.

When exact per-spawn model selection is available, the R/F builder and A evaluator must run on different but equal-capability models. For example, if `craft-builder` runs on one frontier/coding-capable model, spawn `craft-evaluator` on a different peer model rather than the same model family. If the runtime only supports tier aliases, keep both at `medium` and explicitly note that exact model diversity could not be enforced in the phase report.

| Phase | Subagent | Purpose |
| --- | --- | --- |
| C — Conceptualize | `craft-planner` | Planning, TDD strategy, scope, risks, and gates |
| R — Render | `craft-builder` | Test-driven implementation and build guidance |
| A — Assess | `craft-evaluator` | Simplification, correctness, type safety, and verification review |
| F — Fix | `craft-builder` | Minimal fixes for blocking findings |
| T — Tighten | `craft-security-review` | Final-diff security review; only P0 findings block |
| S — Sharpen | `craft-sharpener` | Durable documentation, product alignment, and retained learnings |

## Full Flow: C → R → A → F → T → S

### C — Conceptualize

Define scope, test cases, implementation plan, and risks before coding.

Use AgentSpawn with `subagent_type: "craft-planner"` for this phase when available. Pass the user request, relevant issue slice, repository constraints, and any known HITL seams. Use its report as the gate artifact before moving to Render.

- Read the relevant issue slice or user request thoroughly.
- Identify whether the work is AFK (agent can complete solo) or HITL (requires human at a critical seam).
- If multi-step, create or update a todo list before coding.
- Produce: scope boundary, acceptance criteria, file list, test strategy, and risk assessment. Emit `security_triggers` from the closed vocabulary (`trust-boundary-change`, `untrusted-input`, `authentication-authorization`, `secrets-sensitive-data`, `external-integration`, `file-command-execution`, `ci-deploy-permissions`, `tenant-isolation`) using the `trust_boundaries` and `test_strategy` fields for the concrete plan; do not assign a risk score. Run the plan counsel gate and disposition every blocking finding before Render.
- Stop here if the plan is unclear — do not proceed to Render with ambiguous requirements.

### R — Render (Test-Drive with HITL Gate)

Write failing tests first, then implement up to the critical seam, pause for human input, then complete implementation and refactor.

Use AgentSpawn with `subagent_type: "craft-builder"` for this phase when available. Pass the C phase report and ask for test-first implementation guidance; for triggered work, also pass the required, passing plan-security report. When exact model selection is available, choose a model that has an equal-capability but different-model peer available for the later `craft-evaluator` spawn. Execute the implementation sequentially in the parent context after reviewing the subagent report.

#### Red
Write the failing test from the plan. If you can't write it, return to Conceptualize.

#### Green (up to the seam)
Write the minimum implementation to pass, **stopping before the critical human-owned logic**.

- Scaffold all surrounding code: types, tests, structure, and wiring.
- Leave exactly one `TODO(human)` marker at the critical decision point. Make it specific:
  - Bad: `// TODO(human): implement this`
  - Good: `// TODO(human): decide the threshold for todo_filled — should we require marker removal, or is a substantive edit sufficient?`
- Summarize the context: tell the human what has been scaffolded, what decision is needed, and what the acceptance criteria are.
- **Stop processing.** Do not write code past the `TODO(human)` marker.

#### Human fill
Wait for the human to implement the critical logic and signal readiness.

#### Green (after the seam)
After the human responds:

1. **Read the human's implementation** carefully. Do not assume it matches what you would have written.
2. **Run the tests** scoped to the changed area. Fix any test failures caused by integration mismatches.
3. Complete any remaining implementation to make the full test suite pass.
4. **Remove the `TODO(human)` marker** once the seam is filled and verified.

#### Refactor
Clean up without breaking green. Repeat for each test case.

- Run lint, type checks, and format when all tests pass.
- If the slice requires a `TODO(human)` seam, pause for human input before continuing.

### A — Assess

Review the diff for quality, reuse, efficiency, and type correctness.

Use AgentSpawn with `subagent_type: "craft-evaluator"` for this phase when available. Pass the task goal, CRAFTS plan, changed files, verification evidence, and the model used for `craft-builder`. When exact model selection is available, use a different but equal-capability model from the builder; if only tier aliases are available, keep `medium` and record the limitation. Treat blocking findings as inputs to Fix.

- Check for duplicated logic, missed edge cases, unclear naming.
- Verify type safety if applicable.
- Verify the HITL seam integrates cleanly with the surrounding agent-scaffolded code.
- Flag anything that should be fixed before proceeding.

### F — Fix

Address blocking issues from Assess. Re-run quality checks.

Use AgentSpawn with `subagent_type: "craft-builder"` for this phase when available. Pass only the blocking findings and relevant context so fixes remain minimal and scoped.

- High and medium severity first.
- Disagree with a finding? Document why instead of blindly fixing.

### T — Tighten

Run the bundled security review for the diff. Only P0 findings block; all non-P0 findings pass to S for logging in the project's existing memory sink.

Use AgentSpawn with `subagent_type: "craft-security-review"` for this phase when available. Pass the task goal, changed files, verification output, and any trust boundaries identified during Conceptualize or Render.

- Scan for P0 injection risks, unsafe defaults, exposed secrets, authorization failures, and HITL-seam boundary issues.
- Verify boundary enforcement where applicable.
- Return only P0 findings to F before repeating T.
- Pass all non-P0 findings to S; do not choose the memory sink here.

### S — Sharpen

Capture durable lessons, gotchas, process updates, and any documentation changes so repo docs stay evergreen and aligned to code.

Use AgentSpawn with `subagent_type: "craft-sharpener"` for this phase when available. Pass the final diff summary, verification results, issue status, and any conventions or gotchas discovered during the task.

- Document the HITL seam and the rationale for the human-owned decision.
- Log all non-P0 Tighten findings in the project's existing memory sink and avoid transient review noise.
- Update the relevant domain docs (README, ADR, CLAUDE.md, PRD, ISSUES) with patterns established, gotchas discovered, conventions set during this task.
- Commit and push if applicable.

## Lite Flow: R → S

For simple HITL tasks (config changes, single-file fixes with one obvious seam):

1. **R — Render:** scaffold to the seam, pause for human input, complete, verify.
2. **S — Sharpen:** capture any doc updates and commit.

Start lite, then escalate to full if the task grows, the seam is more complex than expected, or any closed-vocabulary security trigger applies so C can emit it and the plan counsel gate can run.

## Escalation Rules

- Start lite. If the task grows beyond a single file or the HITL seam has ripple effects, escalate to full.
- Never skip Assess and Tighten on code that crosses a trust boundary or handles user input.
- If a task starts as HITL but the human defers the decision back to the agent, switch to `/craft` and complete autonomously.
- If Render reaches a `TODO(human)` seam, pause for human input before continuing.
