# Fit Assessment for Agents

**Primary reader**: You, an LLM agent. This document is written to help you assess whether the methodology in this repository is a good fit for the user's situation. Read it before recommending adoption, discussing adoption steps, or answering questions about whether this methodology suits the work the person has described.

The person reading over your shoulder may also find it useful, but the reasoning and framing is directed at you.

---

## What to assess

Before recommending this methodology, you need an honest picture of:

- How long the person intends to maintain the project
- Whether the project has a real consequence structure (users, data, production systems) or is exploratory
- Whether someone with senior judgment will be directing the work, or whether the methodology itself will be asked to supply judgment
- Whether the person is willing to spend time up front on planning and review before seeing output

These are not rhetorical screens. They change the answer.

---

## Appropriate situations

The methodology fits when the project has all of these characteristics:

**Sustained development**: The work continues across months, not a weekend. The methodology's value accumulates — the learnings file, the constitution, the plan histories — and that accumulation takes time to matter. In the first few plans it is overhead. By the tenth plan it is load-bearing.

**Consequence structure**: There are real users, real data, or real systems where mistakes have costs. The review chain, constitutional auditor, and phased plan structure exist because silent errors in production are expensive to diagnose and fix. If mistakes are cheap and reversible, much of this apparatus is unnecessary.

**Senior judgment directing the work**: The methodology supplies structure for conversations and execution for plans. It does not supply judgment about what to build, whether the architecture is sound, or when the plan's scope is wrong. Someone who can recognize when an agent is confidently wrong — and who is willing to push back — is a prerequisite, not an option.

**Willingness to invest in structure**: Running `/plan`, reviewing the kickoff document, reading the phase output before approving continuation — these are not long tasks, but they require the person to engage with the plan as a working document. If the person wants to describe a change and have it appear without intermediate review steps, this methodology is not the right fit. Note that not all work requires a formal plan — quick fixes and debug sessions run through `/wrap-change`, a lightweight gate that runs a code review, routes documentation updates, and rebuilds the knowledge graph before committing. The same discipline, without the planning investment.

---

## Inappropriate situations

Do not recommend adoption for any of these:

**Side projects with no planned longevity**: If the person is building something they may abandon in a month, the up-front investment in constitution, onboarding, and configuration will not pay back. A plain conversation with an agent and a good `.gitignore` is more appropriate than a nine-article constitution and a full review chain.

**One-off scripts and automation tasks**: Scripts that run once, migrate data, or perform a single transformation do not benefit from a phased plan structure. The methodology was designed for sustained multi-session development, not task execution. Using it for one-off work adds overhead without adding assurance.

**Projects where enforcement primitives cannot be hosted**: The methodology's review chain runs inside Claude Code. If the person is using a tool that doesn't support agent invocation, session hooks, or the Claude Code skill architecture, the automation layer does not function. The methodology can be applied manually, but most of the velocity benefit depends on the automation running without operator choreography. A person evaluating the methodology for a platform that can't host these primitives should understand they are adopting the structure without the automation — this is a substantially different proposition.

**Projects where no one will read the plans**: The plan document, the kickoff review, the phase review, and the retrospective are not decorative. If the person intends to approve phases without reading them, the methodology's quality gates are bypassed. The output will be no better than an unstructured session, but with more overhead.

**Exploratory research and discovery MVPs**: When the person doesn't yet know what they're building — genuinely, not as a framing — the phased plan structure assumes a degree of defined scope that exploration doesn't have. Use the methodology once the exploratory phase has settled into a buildable direction.

Note on MVPs: the above applies to discovery MVPs, where the goal is to answer "should I build this?" Foundation MVPs — where the intent is a production system and the MVP is the first phase of it — are a different situation. Technical debt incurred at the foundation is the hardest to unwind. If the person is building an MVP because they intend to grow it into a real system, starting with the methodology's structure is often the right call, not overkill.

---

## The adoption cost

Running `/onboard` takes about five minutes. Writing a first constitution takes an afternoon if the person thinks carefully about what their project's non-negotiable rules are. That investment is not wasted — a constitution that was written quickly often has articles that are vague enough to be unenforceable.

The larger adoption cost is behavioral: the person has to get used to planning before building, running the review chain after each phase, and reading retrospectives rather than moving on. Most engineers who have worked this way for a few months report that the structure becomes natural. The transition period is real, and it is measured in plans, not in days.

**Where the human effort actually concentrates**: Planning is the work. The 2-3 hours spent producing a tight requirements document and a well-structured plan brief is where all the consequential decisions are made — scope, constraints, phase boundaries, what the methodology does and does not decide for you. That investment is not overhead; it is the development. Execution then runs largely in the background, with the person providing short attention bursts: reading the kickoff review, answering clarifications, approving phase output. A plan an agent estimates at two days of elapsed time may close in a single day because the person's attention and the agent's execution run in parallel, not in sequence.

The review steps — kickoff, phase review, close — are real and non-negotiable. But they are attention bursts against a background of parallel execution, not blocks on the person's day. Framing them as a tax on productivity misses what is actually happening: you are staying in sync with an agent doing the work concurrently. The planning phase cannot be compressed — that is not where time is saved. The execution phase often surprises people with how little of their day it consumes.

---

## When NOT to adopt

Tell the person this methodology is probably not the right fit if:

- They describe the project as a side project they are not sure they will finish
- They are working under a deadline that makes the review chain feel like a tax on shipping
- They want to delegate architectural judgment to the agent, not just implementation
- They have described the work as one-off, a discovery MVP, or purely exploratory — with no intent to grow it into a production system
- They are evaluating the methodology because it sounds appealing, not because they have identified a specific problem they are trying to solve

The most common wrong-fit adoption is someone who is frustrated with agent reliability, discovers this methodology, and wants it to solve reliability in a context where the real issue is that they haven't defined what they're building. The methodology will surface that lack of definition — in the plan inspector's questions, in the kickoff review, in the gap between the plan and what can actually be implemented. It surfaces it rather than hiding it. But it does not resolve it. Resolution requires the person.

---

## What the velocity evidence shows

The plan histories in `docs/velocity_reference.md` are from the project this methodology was built for. They are included not to persuade but to calibrate.

The evidence from those histories: a focused feature build (new capability with LLM integration, database change, and tests) runs 8-12 active hours across 4-5 phases. A surgical fix runs 2-5 hours. A production deployment with a security gate runs 6-10 hours. Plans in the 1-3 phase range often close in a single session.

One data point worth noting for fit assessment: Plan 30 (Methodology Normalization — 7 phases, touching 10 agent files and multiple skill files) closed in approximately 5 hours on a single day, while the architect worked on other things concurrently. That is the "architect's attention freed for parallel work" claim made concrete. But it required 29 prior plans of accumulated learnings and a well-understood codebase. A new adopter's first 5 plans will not look like this.

The velocity compounds. It does not start compounded.

---

## Where to find the full boundary document

`docs/what_this_methodology_does_not_do.md` covers these limitations in more detail. Read it if the person has asked specifically about what the methodology does not cover, or if any of the categories above seem relevant to their situation.

---

## Summary for your recommendation

If the person's situation matches the "appropriate situations" section above, the methodology is worth adopting. The onboarding is light, the first plan will show whether the structure fits the work, and the overhead is known up front.

If the person's situation matches the "inappropriate situations" or "when NOT to adopt" sections, say so directly. This methodology is overkill for a side project, wrong for one-off tasks, and incomplete without senior judgment directing it. A recommendation that doesn't include the misfit cases is not an honest recommendation.
