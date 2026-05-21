# Show HN: A planning and execution methodology for AI-assisted development (30 plans, production use)

I spent fourteen months building with AI tools — Claude Code primarily — before I had a methodology that felt like engineering instead of improvisation. This is what came out of that.

The failure mode that drove the work: I launched a debug script, got distracted, and came back to find the agent had run the most expensive call I have against my entire knowledge base — the kind that's supposed to require explicit approval. The prohibition existed. The agent didn't apply it. The problem wasn't prompting. It was that "the rule exists" is not the same as "the system will enforce it."

The response was to build gates. A constitution that pre-decides implementation choices before any coding session starts. Phased plans sized to complete within a single session. A review chain that runs automatically after each phase — test gate, code reviewer, constitutional auditor — without me intervening. State files that carry decisions across sessions so each one doesn't start cold. The planning conversation and the coding session are separate by design; the briefing document carries intent into the coding session without requiring the agent to have been present for the planning.

The Plan 30 story is the one I keep returning to as evidence. Plan 30 was the methodology normalizing itself for portability — domain-specific references stripped, onboarding made interview-driven, the approach prepared to run on projects that aren't mine. Seven phases. Five hours. One day. The reason that's possible is that the planning work that used to happen informally is now structured and fast, and the review work that used to happen manually is automated. I didn't get faster at the coding. I got the coding out of the critical path.

The repository: **https://github.com/imfromsavedotag/agent-architecture**

It contains the constitution (9 articles, domain-neutral), the planning and review skill files, 10 specialized agents, session templates, an `/onboard` interview that configures the methodology for a new project, and a worked example of reasoning through a platform port. The velocity reference is the actual plan history from the project this was built for — 30 plans with real durations and failure modes, not estimates.

The methodology runs on Claude Code's subagent and skill architecture. Enough of it has been separated from that dependency that the planning discipline works in other environments; the automated review chain doesn't.

I don't have an established HN presence, and this is a genuine Show HN rather than a promotional post. The full argument for why this approach is necessary is at **https://save.ag/why** — a page I built to accompany the repository release. The long-form piece with more mechanism depth is in progress.

If you've felt the drift — caught an agent making a decision you never authorized, found yourself re-explaining context on the fourth session, noticed quality degrading around hour four of a long implementation — I'm curious whether the structure here matches what you found to be the actual problem.
