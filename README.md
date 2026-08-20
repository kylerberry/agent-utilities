# Agent Utilities

A distributable CRAFTS toolkit for AI coding agents. It mirrors the current global `~/.agents` CRAFTS workflow, its role agents, and the `security-and-hardening` dependency skill.

## Contents

```text
skills/
├── craft/                    # Autonomous CRAFTS workflow
├── craft-hitl/               # CRAFTS with a TODO(human) Render seam
└── security-and-hardening/   # Threat modeling and secure-code review guidance

agents/
├── craft-planner.md          # C — Conceptualize
├── craft-builder.md          # R/F — Render and Fix
├── craft-evaluator.md        # A — Assess
├── craft-security.md         # Security counsel lens and T — Tighten
├── craft-sharpener.md        # S — Sharpen
├── craft-plan-feasibility.md # Plan counsel: executable here & internally consistent
├── craft-plan-scope.md       # Plan counsel: exactly the criteria, no more
└── crafts-builder.md         # End-to-end CRAFTS implementation agent
```

## CRAFTS at a glance

CRAFTS is a sequential delivery workflow:

`C → R → A → F → T → S`

| Phase | Role | Purpose |
| --- | --- | --- |
| **C**onceptualize | `craft-planner` | Scope, acceptance criteria, tests, plan, and security triggers |
| *Plan counsel* | `craft-plan-feasibility` · `craft-plan-scope` (+ `craft-security` when triggered) | Independent one-pass review of the C plan before Render |
| **R**ender | `craft-builder` | Test-first implementation: Red → Green → Refactor |
| **A**ssess | `craft-evaluator` | Independent review of implementation and tests |
| **F**ix | `craft-builder` | Minimal fixes for blocking findings |
| **T**ighten | `craft-security` | Security review with `security-and-hardening` |
| **S**harpen | `craft-sharpener` | Durable documentation and process learning |

Use `/craft` for autonomous work. Use `/craft-hitl` when Render must pause at a specific `TODO(human)` seam.

### Plan counsel gate and security triggers

C emits `security_triggers` from a closed vocabulary (`trust-boundary-change`, `untrusted-input`, `authentication-authorization`, `secrets-sensitive-data`, `external-integration`, `file-command-execution`, `ci-deploy-permissions`, `tenant-isolation`) instead of a subjective risk score; an empty list means low-risk work.

Every full-flow task then runs the **plan counsel gate** between C and R:

1. The C report goes verbatim to independent read-only reviewers: feasibility-and-coherence and scope guardian always; security only when a trigger is declared. They may run in parallel; none sees another's findings first.
2. Any blocking finding returns all reports to C, which revises once and dispositions every blocking finding: `adopted` (with the plan change) or `rejected` (with rationale).
3. Render begins only when every blocking finding has a disposition — dispositions are the gate, not agreement. There is no counsel re-review round.
4. Feasibility reports `probe_required` instead of guessing when an assumption needs execution to settle; the user supplies evidence, descopes, or confirms.
5. Counsel reports and dispositions forward to Assess, which treats thin rejections or cosmetic adoptions as blocking findings.

Tighten still maps every declared trust boundary to evidence, a finding, or explicit non-applicability, and Sharpen records one disposition—`guidance-update`, `owned-follow-up`, or `documented-non-generalizable`—only when Tighten finds a reusable security issue. The included global roles use JSON C, counsel, plan-security, and Tighten reports; these are workflow contracts, not a substitute for runtime enforcement.

## Installation

Copy the directories into either a project's `.agents/` folder or your global `~/.agents/` folder:

```bash
# From this repository
cp -R skills/* /path/to/project/.agents/skills/
cp -R agents/* /path/to/project/.agents/agents/
```

Then invoke `/craft` or `/craft-hitl`. Ensure the host supports the agent frontmatter and that `craft-security` can load the bundled `security-and-hardening` skill.

## Design principles

- **Acceptance criteria remain the reference.** Assess reviews the test suite against original criteria, not just passing tests.
- **Independent review reduces correlated blind spots.** Builders do not approve their own work; plan counsel challenges the plan before code exists, and elevated plan review is independent of planning and implementation.
- **Security starts in planning.** Threat boundaries and abuse cases are considered before code exists, then rechecked against the final diff.
- **Knowledge compounds.** Sharpen records durable lessons without turning ordinary fixes into documentation churn.
- **Humans own consequential judgment.** HITL reserves explicit seams for people while the agent handles surrounding implementation and verification.

## License

MIT
