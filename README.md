# Claude Utilities

A skill-based toolkit for building software with AI coding agents — installable slash commands that enforce disciplined, repeatable workflows.

## Skills

```
skills/
├── craft/           # Phase-gate execution workflow (C → R → A → F → T → S)
└── craft-hitl/      # Human-in-the-loop gating at the TODO(human) seam
```

### `/craft` — CRAFTS Workflow

Invoke for every non-trivial task. CRAFTS is a sequential phase-gate workflow: finish the current phase before moving to the next.

**Full flow** (business logic, multi-file work, domain boundaries):

| Phase | What happens |
|-------|-------------|
| **C**onceptualize | Scope, test cases, implementation plan, risks before coding |
| **R**ender | TDD strictly: Red → Green → Refactor |
| **A**ssess | Review diff for quality, reuse, efficiency, type correctness |
| **F**ix | Address blocking issues from Assess |
| **T**ighten | Security-hardening review of the diff |
| **S**harpen | Update docs with lessons learned; commit |

**Lite flow** (config, scaffolding, single-file fixes): **R**ender → **S**harpen.

Start lite, escalate to full if the task grows.

### `/craft-hitl` — Human-in-the-Loop Gating

Invoke during **R — Render** when the issue slice requires a human at a critical decision point. This is the only required HITL gate in CRAFTS.

The agent scaffolds to the seam, leaves exactly one `TODO(human)` marker with specific context, then pauses. The human fills the critical logic; the agent resumes verification and completion.

**When to pause:**
- Issue labeled **HITL implementation** — agent scaffolds/tests, human owns critical logic
- Issue labeled **HITL design/review** — requires human taste/content/design review
- Subjective choice (naming, UX copy, algorithmic trade-off) explicitly reserved for human judgment

**When NOT to pause:** routine refactoring, clear-cut implementation, or decisions inferable from the PRD.

## Installation

Copy skills into your project's `.agents/skills/` directory (or your agent's skill path):

```bash
cp -r skills/craft      /path/to/your-project/.agents/skills/
cp -r skills/craft-hitl /path/to/your-project/.agents/skills/
```

Then invoke with `/craft` and `/craft-hitl` from your agent interface.

## Philosophy

**Planning beats replanning.** Wrong assumptions caught before code exists cost minutes. Caught after implementation they cost hours. CRAFTS front-loads the thinking in Conceptualize.

**Self-review is impossible.** The builder can't see their own blind spots. The Assess and Tighten phases are fresh-context reviews because the agent that wrote the code is the worst reviewer of it.

**Security is a gate, not an afterthought.** Tighten runs before every commit. Vulnerabilities found pre-commit cost minutes; found post-deploy they cost far more.

**Knowledge compounds.** Sharpen updates domain docs with lessons learned after every task. Week 1 those files are stubs. Week 8 they're handbooks that make every subsequent task faster and less error-prone.

**The right human at the right seam.** HITL gating isn't about distrusting the agent — it's about reserving human judgment for the decisions that actually need it, while the agent handles everything else autonomously.

## License

MIT
