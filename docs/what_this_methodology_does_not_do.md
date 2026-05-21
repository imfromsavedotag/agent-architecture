# What This Methodology Does Not Do

This methodology is bounded in ways worth naming explicitly. Misunderstanding what it is can cause as much damage as not using it — a team that believes the methodology covers something it doesn't will skip the work of covering it themselves. The four categories below are the significant ones.

Throughout this document, "the architect" means the person who holds the strategic intent for the project and directs the work — not a job title, not a technical credential. It is the human who decides what gets built and why, reads the phase output before approving continuation, and is ultimately accountable for what ships.

---

## It does not ask the pre-deployment questions your project needs to answer

The methodology will plan and execute the technical work you bring to it. It will not raise the questions a project has to answer before any of that work matters to a real user.

Is the system secure? Where are credentials stored? How is user input validated? What is the response plan when a vulnerability is reported?

Where does it deploy, and how? Is the deployment repeatable from a clean environment by someone who isn't the original developer? What is the rollback procedure when a release goes wrong?

Is the project operating under an appropriate legal structure for what it actually does? Have you assessed the liability implications of the kinds of failures that could plausibly occur?

Are you handling user data appropriately, and does any privacy policy or terms of service match what the system actually does? Boilerplate that doesn't reflect reality provides no protection.

These are the questions that, gotten wrong, can sink an otherwise well-built project. The methodology will not raise them. Knowing they need to be on the table is the architect's responsibility.

---

## It does not know everything

The agents in this methodology have broad capability and persistent memory of your codebase, but they do not automatically know every specialized library, internal API, esoteric tool, or domain-specific process your project may need. Treating any LLM agent as omniscient is a category error.

When you intend to work with something the agent cannot reasonably be assumed to know — a library with subtle behavior, an internal service with unusual conventions, a deployment target with specific constraints — do this first: have the agent search, build an overview document, and produce a reference guide for the process. When you plan work that touches that process, make sure the planning session is told the guide exists and where to find it. The methodology supplies the structure for capturing knowledge; you have to initiate the capture.

---

## It is not a substitute for your own judgment

The methodology supplies structure for conversations and execution for plans. It does not supply judgment about what to build, whether the architecture is sound, or when a plan's scope is wrong. The quality of the output is bounded by the quality of the judgment directed at it.

If you skip the gap analysis at kickoff, accept the first draft of a plan without probing it, or approve phases without reading them, the review chain cannot compensate. The constitutional auditor can flag a configuration violation; it cannot flag a bad product decision. The plan inspector can surface assumption gaps; it can only surface the gaps it has been given context to see. The discipline you bring to the system is the ceiling on what the system produces.

This is the limitation that tends to get discovered the hard way: someone runs several plans and is satisfied with the output, then hits a phase that required a judgment call the agent made silently, and the call was wrong. The gate chain catches implementation errors well. It does not catch strategic errors.

---

## The planning investment does not drop to zero in steady state

Once the methodology is configured and running, there is a persistent requirement for focused human attention. The effort is not evenly distributed across the session — it concentrates upstream, in planning.

The 2-3 hours spent producing a tight requirements document, defining phase boundaries, and setting acceptance criteria cannot be compressed. That is where all consequential decisions are made and where judgment lives. Once a plan is tight, execution runs largely in the background: the architect reads the kickoff review, answers clarifications, approves phase output. Those are attention bursts, not time blocks — they run against a background of concurrent agent execution, not in sequence with it.

This means "going faster" by skipping kickoff reviews or bypassing phase gates does not save meaningful time. The agent was running concurrently anyway. What skipping does is remove the safety gates from an autonomous process while maintaining the appearance of a full process — which is worse than either a rigorous process or an explicitly informal one.

The right response to a rhythm that feels too heavy is to examine whether the scope of the review chain matches the scope of the work. A one-file surgical fix does not need the same gate chain as a five-module feature build. The methodology supports that simplification. Silently skipping steps to save nominal attention bursts is a different thing.

---

## What the planning process can help with — but only if you initiate it

Each of the categories above can become a productive planning session if you bring it to the methodology deliberately. A pre-deployment review session can walk through the security and legal questions. A "build me a guide" session can capture unfamiliar technology before it becomes a problem. The structured conversation that the methodology is built around generalizes beyond feature work.

But it generalizes only when the architect decides to start that session. The system supplies the structure. The prompt that opens the right conversation is yours to write.

One anxiety this document tends to surface: "how do I keep everything in sync when I don't have two hours for every task?" The answer is that not every task requires a formal plan. Quick fixes, debug sessions, and one-file changes run through `/wrap-change` — a lightweight gate that runs a code review, enforces documentation routing, and rebuilds the knowledge graph before the change is committed. Documenting poor code is not a viable outcome; the review gate is there to prevent it. The underlying discipline extends to ad-hoc work; the investment scales to match. The codebase stays reviewed and self-documenting because the gate is mechanical, not because the developer remembers.
