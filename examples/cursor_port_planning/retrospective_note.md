# Retrospective Note — Cursor Port Planning Analysis

## What was surprising

**The automation/discipline distinction was sharper than expected.**

When starting the analysis, the assumption was that most of the methodology would need adaptation because it was built on Claude Code primitives. The actual finding was the opposite: most of the methodology's value is in the discipline (gate structure, constitution, learnings loop, config-first principle), and almost all of the discipline translates directly. The automation is a layer on top of the discipline, not the discipline itself.

This matters for how to frame the port: a Cursor adopter is not losing most of the methodology when they lose the subagent automation. They are losing convenience and enforcement, not the core value. Framing it correctly changes the adopter's decision calculus.

**The review chain enforcement problem was harder to articulate than expected.**

The concern about "the review chain becomes optional without automation" was obvious in retrospect but took some time to surface during the analysis. The instinct when reasoning about Cursor was to say "you just do the same steps manually" and move on. But the non-optionality of the review chain in Claude Code is doing real work — it is why the chain actually runs on every phase rather than only when the developer feels like it. A prompt template cannot replicate that enforcement.

This is the strongest argument for eventually building automation in Cursor (a VS Code extension or similar) rather than relying on manual discipline indefinitely.

**Substrate feature deferral was cleaner than expected.**

The knowledge graph and doc-routing infrastructure sounded complex to evaluate, but the analysis was straightforward once the question was framed correctly: "Would a new adopter on a new project have this infrastructure already?" The answer was clearly no for both features, which made the deferral recommendation easy. The `.aiag/config.yaml` substrate feature flags (`substrate_features.knowledge_graph: false`, `substrate_features.doc_routing: false`) are exactly the right mechanism for this — the methodology already has the right abstraction.

## What was deferred

**Cursor-specific implementation details** — this analysis identifies what categories of work need doing but does not specify the exact Cursor configuration syntax, rule format, or task definition structure. A real Cursor port plan would need Phase 2 to work through those specifics, and they may have changed between the time this was written and when an adopter reads it.

**A concrete onboarding script for Cursor** — the `/onboard` skill in Claude Code conducts an interview and writes `.aiag/config.yaml`. For Cursor, this would need to be a shell script or a more manual process. The analysis notes this but does not design the replacement.

**Testing the discipline commitment claim** — the assertion that "a Cursor adopter must treat the review chain as a hard commitment" is a hypothesis, not an observed fact. It could be tested by actually running the methodology in Cursor for a few plans and observing whether the review chain erodes. That experiment was not run.

## What would change if done again

The three-category framework (direct translation / adaptation / deferral) was productive but the "adaptation" category ended up doing a lot of work. A refinement would split it into two: "adaptation — convert to prompt template" (most agent/skill functionality) and "adaptation — requires new tooling" (hooks, enforcement). The distinction matters because prompt-template adaptations are available on day one, while new-tooling adaptations require investment.

The analysis also did not evaluate Cursor's capabilities at a specific version. Cursor's custom commands and rules features have been evolving, and some of what is described under "adaptation" may already have better platform support than assumed here. A real port plan would start with a Cursor version audit.
