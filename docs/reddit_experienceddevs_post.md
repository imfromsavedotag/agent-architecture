# Title: I built a planning methodology for AI-assisted development after 18 months of watching context drift compound — here's the architecture I arrived at

I've been building a production system with AI tools — Claude Code primarily — as the primary engineering resource. No team. One founder. Real stack: PostgreSQL with pgvector, Flask, a 70-table schema, 30+ pipeline steps, live users. I'm not a career software engineer, which is relevant context for what follows.

The problem that drove the work: you can't run engineering discipline through declaration. Write a rule, place it well, put it in context — the agent will comply most of the time and violate it when it matters. The conventional response is to write the rule better. More specific, more emphatic. I tried that long enough to know it's insufficient. Declaration without enforcement doesn't hold. You don't rely on developers remembering not to push unreviewed — you build a gate.

What I built is a methodology around that observation. A constitution that pre-decides implementation choices before any coding session starts. A planning layer that runs in a separate conversation, where the planner's job is to push back when framing is wrong and refuse when premises are off. Phased plans sized to complete within a single session. An automated review chain — test gate, code reviewer, constitutional auditor — without me deciding each time. State files that carry decisions across sessions, so the agent doesn't start cold.

This is a lot of structure for a solo project. The counterargument: without it, the solo project is most exposed to drift, because there's no one else watching the codebase for compounding decisions.

I've run 30 plans under this in production. The velocity reference in the repository is the actual record — real plan names, real durations, failure modes, not examples.

The honest limits are documented at length. This is not for weekend projects or developers who want to move fast without worrying about architectural debt. The overhead is real.

The full argument is at **https://save.ag/why**. The long-form piece with more mechanism depth — the defense-in-depth framing, how the gates catch different failure categories — is at [URL — to be added] for people who want to go deeper.

The repository: https://github.com/imfromsavedotag/agent-architecture. Constitution, agents, skills, templates — configured for Claude Code, planning discipline separating cleanly from the automation. `/onboard` runs an interview to configure it for a new project.

I expect skepticism toward the AI tooling framing here, and think it's warranted. What I'd push back on: the drift problem isn't a tooling problem. It's structural. The tools change. The requirement that a prohibition be verifiable to be reliable doesn't.
