---
name: craft-hitl
command: craft-hitl
argument-hint: "Optional mode: full or lite"
icon: Hand
description: >-
  CRAFTS with mandatory human-in-the-loop gating at approved TODO(human)
  seams during Render.
---

# CRAFTS HITL Adapter

Before execution, read the sibling `../craft/SKILL.md` completely and apply its canonical workflow in **HITL mode**. This file defines only the entry condition and override; `/craft` owns every phase, counsel, security, reporting, and escalation contract.

Use HITL mode when the task, issue, or user reserves a consequential design or implementation decision for a human. Use autonomous `/craft` when no such seam exists.

During Render:

1. Scaffold and test everything around the approved human-owned seam.
2. Leave one specific `TODO(human)` explaining the decision or implementation required.
3. Report the prepared context and relevant acceptance criteria, then stop.
4. Resume only after the human supplies the work or explicitly delegates it back.
5. Read and verify the human's contribution before completing Render; remove the marker once integrated.

For a simple change, use lite HITL (`R → S`). Escalate to full HITL before editing if scope grows or any `/craft` security trigger applies.
