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
├── craft-security.md         # Plan-security checkpoint and T — Tighten
├── craft-sharpener.md        # S — Sharpen
└── crafts-builder.md         # End-to-end CRAFTS implementation agent
```

## CRAFTS at a glance

CRAFTS is a sequential delivery workflow:

`C → R → A → F → T → S`

| Phase | Role | Purpose |
| --- | --- | --- |
| **C**onceptualize | `craft-planner` | Scope, acceptance criteria, tests, plan, and risks |
| **R**ender | `craft-builder` | Test-first implementation: Red → Green → Refactor |
| **A**ssess | `craft-evaluator` | Independent review of implementation and tests |
| **F**ix | `craft-builder` | Minimal fixes for blocking findings |
| **T**ighten | `craft-security` | Security review with `security-and-hardening` |
| **S**harpen | `craft-sharpener` | Durable documentation and process learning |

Use `/craft` for autonomous work. Use `/craft-hitl` when Render must pause at a specific `TODO(human)` seam.

### Elevated-risk work

C classifies full-flow work as `low`, `medium`, or `high`. Medium and high use the same elevated controls:

1. C records the risk rationale, trust boundaries, assets, abuse cases, and planned security tests.
2. A fresh, independent `craft-security` **plan-security** review runs after C and before R.
3. Render cannot begin until that review reports `status: "passed"`.
4. Tighten maps every declared trust boundary to evidence, a finding, or explicit non-applicability.
5. Sharpen records one disposition—`guidance-update`, `owned-follow-up`, or `documented-non-generalizable`—only when Tighten finds a reusable security issue.

Low-risk work retains the normal CRAFTS and lite-flow behavior. The included global roles use JSON C, plan-security, and Tighten reports; these are workflow contracts, not a substitute for runtime enforcement.

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
- **Independent review reduces correlated blind spots.** Builders do not approve their own work; elevated plan review is independent of planning and implementation.
- **Security starts in planning.** Threat boundaries and abuse cases are considered before code exists, then rechecked against the final diff.
- **Knowledge compounds.** Sharpen records durable lessons without turning ordinary fixes into documentation churn.
- **Humans own consequential judgment.** HITL reserves explicit seams for people while the agent handles surrounding implementation and verification.

## License

MIT
