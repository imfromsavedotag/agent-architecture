# What the Plan Records Show

The velocity reference (`docs/velocity_reference.md`) is a raw history — durations, phase counts, scope summaries, and "what makes this non-trivial" notes for each of 30 closed plans. This document does the interpretation work: what patterns appeared across those plans, what they mean for how the methodology actually performs, and where the evidence supports or complicates the claims in the essay.

The operating records are the production project's private artifacts — plan documents, kickoff reviews, phase retrospectives, reviewer findings. They are not published. The velocity reference is the synthesized form that is. This document extracts the findings that are most useful for evaluation from that synthesis.

---

## What each gate catches — and what it misses

The essay describes a gate chain: plan inspector → code reviewer → live tests → human. The plan histories are specific about what each gate's failure mode actually looks like.

**The inspector's signal is strong on interface concerns, weak on mechanics.** Across the May 5-12 cohort (Plans 17-29), the inspector raised concerns about regex precision, connection-pool capacity, HNSW index efficiency, deduplication key choice, and integration test data availability. None of these materialized as defects. By contrast, every concern the inspector raised about interface contracts between adjacent phases — what shape does Phase N's output need to take for Phase N+1 to consume it — was correct. Person Three-Layer Phase 2 predicted a missing JSONB column before any code was written; the column gap was confirmed immediately at implementation. Author Retrieval: the inspector's question about the semantics of `'hidden'` in the `_ROLE_MATRIX` was the only concern worth acting on. The inspector finds the right things when it looks at phase boundaries and interface semantics, and overestimates when it looks at implementation mechanics.

**The reviewer catches what the inspector's sampling behavior misses.** The most consistent pattern in the dataset: when a phase creates multiple instances of the same construct, the inspector examines one representative instance and treats it as the whole. Auth Foundation — rate-limit strings hardcoded in four of five routes despite the plan correctly externalizing one. Brief Response Summarization — Article I violation in each of two phases despite an explicit Phase 2 checklist item to fix Phase 1's instance. Institution Retrieval — dead globals from pattern-cloning survived inspection, code review... and the constitutional audit. The reviewer reading the full diff at commit catches the pattern the inspector missed. This is by design; the reviewer's position — reading everything after it's written rather than sampling from a plan document before it is — is what makes the catch possible.

**Live tests find what neither gate can see.** Across every plan in the dataset that included a live execution phase, that phase found bugs that static analysis and code review had missed. Author Retrieval: a `Decimal`-to-`float` type coercion error and an `authors` field treated as a string when psycopg2 had deserialized it as a list — both invisible to unit tests that mock database responses. Citation Balance: a cache key mismatch and an undersized token limit, both silent until real queries ran. Brief Response Summarization: a Python `global` declaration inside an `if` block that misbehaved at runtime. Institution Retrieval: four production code fixes found during Phase 6 test execution, after the code had passed inspection, review, and the constitutional audit. The dead-code row in the essay's defect table — the one where a single defect beat every automated gate and surfaced only under live testing — is not an isolated example. It is representative.

---

## Recurring defect classes

Three defect patterns appeared across enough plans to constitute categories:

**Article I violations on hardcoded strings.** Across Auth Foundation, Brief Response Summarization, RAG Scope Boundary, and others: implementers who externalize one literal string reliably inline the next one in a new function. The pattern holds even when a checklist item explicitly names the string to fix. Externalizing one instance does not generalize to other instances in the same phase. This defect class is reliably caught by code review; it is not reliably prevented by the plan, the checklist, or the operating constitution.

**Pattern-cloning residue.** When an implementation clones an existing pattern (a new route that follows the shape of an existing route, a new synthesis path built from a prior one), it often carries artifacts forward: stale config keys that have no consumers, dead globals, documentation comments that describe the old pattern rather than the new one. Browser Fetch Mode's reviewer found three stale CLAUDE.md entries when the implementation edited the file. Institution Retrieval's test phase found dead globals from a synthesis path clone. This defect class is most reliably caught at review, not by the inspector reading a plan before code is written.

**Reconnaissance-revealed scope correction.** A recurring pattern across the dataset, not a defect: Plans estimated at a certain scope routinely had that scope corrected by a reconnaissance phase. Codebase Repair started with 14 expected unprotected database connections and found 3. RAG Gains started as a 10-issue audit; reconnaissance invalidated 5 and found 2 already fixed. RAG Scope Boundary's complexity "was diagnostic, not implementational." This is the methodology working as intended — reconnaissance phases produce better implementations because they eliminate unnecessary work before it starts.

---

## Inspector concern calibration: the empirical record

The velocity reference is explicit enough to produce a rough calibration table:

**Concerns that consistently did not materialize**: implementation mechanics (regex precision, vector-index efficiency, pool capacity, JOIN shape, score strategy). In the Author Retrieval retrospective, all five of the inspector's mechanics concerns resolved without issues.

**Concerns that consistently did materialize**: interface contracts between phases, missing config keys, scope drift when prerequisites were unclear, and any concern that cited a directly observable divergence between two modules. Person Three-Layer's JSONB column concern was raised before implementation and confirmed immediately. Brand Handling's conceptual correction ("the plan implies a numeric threshold that doesn't exist") was raised by the inspector and prevented a fundamental design error.

**The practical implication**: when writing inspector prompts or reading inspector output, weight interface/contract concerns and discount mechanics concerns. The pattern is consistent enough across 30 plans to be a calibration rule, not a heuristic.

---

## Velocity compounding: what the data shows

Plan 1 (Workflow Automation Infrastructure) took ~12 hours to build a framework from scratch — 8 subagents, 4 skills, 8 templates, 1 git hook, and the constitutional amendment that authorized it.

By Plan 30 (Methodology Normalization), the same architect ran 7 phases covering 10 agent files, 5 skill files, 6 configuration files, and 3 new documentation files in approximately 5 active hours — while working on other things concurrently.

The per-phase average held stable at ~1.6 hours throughout the May 5-12 cohort (Plans 18-29), which included both the largest plan in the dataset by phase count (Person Layer, 10 phases) and some of the fastest surgical plans. Throughput did not come from phases getting faster. It came from three things the velocity reference makes explicit: (1) the elimination of "figure out how it works" time as codebase familiarity deepens; (2) the review chain running automatically, without operator coordination overhead; (3) deferred work being documented rather than blocking — Plans 10 and 11 demonstrate this, where phases 3-4 of Platform Trust were superseded by more focused dedicated plans rather than blocking the original.

The essay's claim — "the fluency behind those numbers took thirty prior plans to accumulate" — is the honest version of the velocity story. The seven-phases-one-morning result is real; it is also the end of a compound curve, not the beginning of one.

---

## The self-binding in practice

The essay describes giving the agent "standing to disagree" through a written constitution. The plan records contain specific instances of what that looks like in practice.

Brand Handling Improvements, Phase 4: the plan text implied that the LLM classification step had a numeric confidence threshold. The inspector surfaced the conceptual gap — there is no principled threshold to choose; the prompt either classifies correctly or it doesn't — and the design was corrected before implementation. This is not a checklist item being caught. It is the agent stopping the conversation to identify a wrong premise.

The constitution has been amended since its original version. Article IX (Phased Planning Discipline) was added during Plan 1 specifically because the original articles described what the methodology does but not how plans should be structured. Article X (Documentation as Code) was added during Script Guide Maintenance when the methodology's own documentation practices were audited. Constitutional amendments are themselves executed as plans, with the same gate structure as any other work.

The operating constitution referenced in the essay is at `docs/constitution.md` in this repository. The save.ag-specific extension (two additional enforcement articles for the original production project) is at `docs/save_ag_extension.md`. The base constitution is domain-neutral by design — the extension model is how project-specific rules are layered on top without modifying the base.
