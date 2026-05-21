# Cursor Port Planning — Worked Example

This is an illustrative, incomplete worked example. It shows how the methodology's own planning tools can be used to reason about porting the methodology to a different AI coding assistant platform. The example is not a finished port plan and should not be used as a deployment specification.

**Why Cursor?** Cursor has enough structural differences from Claude Code — no subagent protocol, a different file-editing model, and a different extension mechanism — to make the planning exercise non-trivial. Those differences force the planner to make real categorization decisions (what translates, what needs adaptation, what gets deferred) rather than producing a mechanical one-to-one mapping.

**What this example demonstrates:**
- How to apply the methodology's reconnaissance-first planning discipline to a platform adaptation question
- How the three-category framework (direct translation / adaptation / deferral) works in practice
- What substrate features look like when evaluated from a new adopter's perspective rather than the original project's

**Files in this directory:**
- `foundation.md` — the analysis document that a planner would produce at Phase 1 of a Cursor port plan: what translates directly, what needs adaptation, what gets deferred
- `retrospective_note.md` — a short note capturing what was surprising or harder than expected when reasoning through the port

**Related reading:**
- `BOOTSTRAP.md` — the methodology entry point a Cursor adopter would read
- `.aiag/config.yaml.example` — the full configuration schema they would fill out

This example was produced as part of the methodology-normalization plan (Phase 7). It reflects the state of the methodology at that point — subsequent changes to the methodology may not be reflected here.
