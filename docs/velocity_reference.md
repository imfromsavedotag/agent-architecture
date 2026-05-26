# Velocity Reference

This document records velocity data from the original project this methodology was built for — a production system. The plan histories below are real, not synthetic. They are included because benchmark documents without evidence histories are hard to trust. Your numbers will differ — your plans will have different complexity, your phases will fit different work, your tools may shift. Use the complexity tiers and scaling factors as starting calibration; replace the histories with your own as you accumulate them.

This document captures the demonstrated velocity and capabilities of the development process (human architect + Claude Code CLI with automated plan lifecycle). Its purpose is to provide accurate baselines for future time estimates.

**Last updated**: 2026-05-12 — extended through May 12 (29 closed plans). Durations for the original April 24-30 cohort are tracked time. Durations for all other cohorts are estimated from complexity signals in retrospective artifacts. Platform Trust (April 28-30) was retroactively added — it was omitted from the original version.

---

## Process Overview

- **Architect**: Single human, senior judgment, editorial discipline, comfortable directing experts they aren't one of
- **Execution**: Claude Code CLI (Opus) with automated subagent review chain
- **Review gates**: Each phase passes through test-gate, code reviewer, constitutional auditor, and phase-completer before advancing
- **Tooling**: 8 subagents, 4 skills, 8 templates, git pre-commit guard — all running locally in Claude Code
- **Work pattern**: Intensive sessions (typically 4-10 hours of active engagement per day), not calendar-spread work

---

## Completed Plans (April 24-30, 2026) — Tracked Time

### 1. Workflow Automation Infrastructure
- **Duration**: ~12 hours (evening April 23 through morning April 24)
- **Phases**: 6
- **Scope**: Built the entire plan automation lifecycle from scratch — 8 templates, 8 subagents, 4 skills, 1 git hook, constitutional amendment, operational guide
- **Complexity**: Architectural — designed and implemented a multi-agent orchestration system with serial gates, short-circuit logic, template conformance, and persistent learning capture
- **What makes this non-trivial**: Creating an automation framework that subsequent plans would rely on. Required constitutional amendment (Article X), multi-model prompt restructuring, and integration testing against a synthetic plan.

### 2. Diagnose Deployment (CSS Cache)
- **Duration**: ~30 minutes
- **Phases**: 2 (Phase 2 skipped — unnecessary after diagnosis)
- **Scope**: Diagnosed why CSS changes weren't reflecting on production. Root cause was a 1-year static asset cache header. Fix: one line change (max-age 31536000 to 300).
- **Complexity**: Low — diagnostic only, no multi-file changes
- **What makes this instructive**: Demonstrates that diagnostic plans can complete in under an hour when root cause is outside the initial candidate list.

### 3. Codebase Audit
- **Duration**: ~8 hours across April 24-25
- **Phases**: 8 (Discovery, Linting, Code Review x2, Security, Docs x3)
- **Scope**: Full-codebase audit of 765 Python files and 378 frontend files. Applied 7,955 ruff auto-fixes + 844 black reformats across ~875 files. Produced 32 code review findings, 198 security findings, and 11 subsystem reference documents. Created 29 audit output files.
- **Complexity**: High breadth, moderate depth — systematic sweep across all subsystems with quantitative reduction metrics (92.7% lint reduction)
- **What makes this non-trivial**: Required Bandit security scanning, manual security checks (CSP, auth boundary, input handling), post-phase remediation (18 CVEs, deprecated service cleanup), and creating durable subsystem documentation.

### 4. Codebase Repair (Audit Findings)
- **Duration**: ~4 hours on April 25
- **Phases**: 6 (Recon, Logging, Config extraction, Dead code removal, Connection cleanup, Shared helper)
- **Scope**: Targeted repair of 5 significant audit findings. Added logging to 4 swallowed-exception sites, moved hardcoded config to config.yaml, deleted ~1,000 lines of dead code (4 stale files), wrapped 3 unprotected DB connections in try/finally, created shared DB helper and migrated 5 scripts.
- **Complexity**: Moderate — surgical changes across 13 files, scope corrections discovered during implementation (14 expected unprotected calls turned out to be 3 actual)
- **What makes this instructive**: Shows how reconnaissance phases can dramatically reduce scope (14 to 3) and why implementation should start with investigation, not assumption.

### 5. Permies Content Optimization
- **Duration**: ~4 hours on April 28
- **Phases**: 5 (Phase 5 deferred to deployment bundle)
- **Scope**: Restricted permies.com crawl scope, created 3 database migrations, marked 45,691 rows as RAG-ineligible (66% corpus reduction), updated 2 RAG query paths with COALESCE NULL safety.
- **Complexity**: Moderate — cross-system (crawl config + DB schema + RAG queries), required understanding of data quality patterns and deduplication logic
- **What makes this non-trivial**: Corpus changes affect downstream RAG quality. Required careful migration sequencing (schema first, then quality floor, then dedup). Code was written NULL-safe so it works before and after migration apply.

### 6. Umami Analytics Setup
- **Duration**: ~2 hours on April 27 for initial deploy, ~1 hour on April 28 for production activation
- **Phases**: 3 (DB provisioning, app deployment, admin setup + activation)
- **Scope**: Deployed Umami as separate Fly.io app, provisioned database (with fallback due to Fly.io API bug), configured secrets, verified live pageview collection.
- **Complexity**: Low-moderate — infrastructure deployment with an external service, required operational troubleshooting (Fly.io API 500, Prisma OOM at 256MB)

### 7. Analytics Instrumentation
- **Duration**: ~3 hours across April 26-27
- **Phases**: 2
- **Scope**: Added tracking script to frontend with CSP nonce compliance, instrumented 5 custom events across JS files, added data-page-type attributes to templates, scaled instances from 2 to 1 for cost savings.
- **Complexity**: Low-moderate — cross-layer (Flask config + JS + templates), CSP compliance requirement

### 8. Ask Results Rendering Restructure
- **Duration**: ~12 hours on April 27 (single day, 5 phases)
- **Phases**: 5
- **Scope**: 4 structural changes to RAG results page. Source grouping (videos, curated, community, research collapse into domain cards), similarity floor for Learn block, 4-role item classification with differentiated rendering (full cards vs pill strips), and table-driven conditional section ordering. Modified 19 files (Python, templates, CSS). 2 new test files. Deferred a 5th change (stance-diversity) with a 4-phase roadmap document.
- **Complexity**: High — multi-layer rendering changes (Python logic + Jinja templates + CSS + config), each change building on the prior, with 11-query calibration and WCAG accessibility compliance throughout. Required understanding of the full SSE rendering path.
- **What makes this non-trivial**: This is a single-day, 5-phase, 19-file rendering restructure with empirical calibration — the kind of work that in a traditional team might be estimated at 1-2 sprints.

### 9. Citation Balance (Minimum Dissent Floor)
- **Duration**: ~12 hours across April 29-30
- **Phases**: 4
- **Scope**: Built a minimum dissent floor for controversial RAG queries. Enriched controversy registry with spectrum positions, built batch LLM side classifier, integrated dissent floor enforcement with framing notes, added write-back cache with new DB table (migration 159), and ran 12-query diagnostic + 67-question benchmark.
- **Complexity**: High — multi-module LLM integration (classification, caching, synthesis), new database table with TTL, cross-module pipeline changes (retrieval to assembly to synthesis to rendering). Uncovered and fixed silent bugs in Phase 4 (cache key mismatch, token limit undersize).
- **What makes this non-trivial**: LLM-in-the-loop feature with caching layer, requiring integration across 6+ modules, a new DB migration, and empirical validation that no existing queries regressed.

### 10. Platform Trust *(2 of 4 phases — phases 3-4 deferred)*
- **Duration**: ~4 hours across April 28-30
- **Phases**: 2 active (phases 3-4 deferred; subsequent brand and content work absorbed into dedicated plans)
- **Scope**: Phase 1: About page rewrite with trust-pillar content, institutional query routing so platform-identity questions answer instead of rejecting as off-topic, migration 158 (`page` type added to learn_documents CHECK constraint), About page content inserted as RAG document. Phase 2: Brand handling foundation — `data/rag/brand_to_category.json` with category/supplier/alias entries, new `brand_handling` config section, brand matching in `query_analyzer.py` heuristic extraction, conditional disclaimer builder in `synthesis.py`.
- **Complexity**: Cross-layer (config + templates + RAG query path + synthesis)
- **What makes this instructive**: Phase 2 established the brand-handling pattern (config-driven JSON lookup → heuristic match → synthesis injection) that subsequent plans built on. A plan that closes at 2/4 phases is not a failure — phases 3-4 were superseded by more focused plans rather than abandoned.

---

## Completed Plans (May 1-3, 2026) — Estimated Time

Seven plans closed in the three days immediately following the original cohort. Durations are estimates derived from phase count, retrospective artifact density, and complexity tier — not tracked time.

### 11. Brand Handling Improvements
- **Duration**: ~7 hours (multi-session, late April into May 2)
- **Phases**: 5 active (phases 6-7 superseded by plan #12)
- **Scope**: End-to-end brand handling pipeline: reconnaissance + logging brand detections to DB (new `rag_query_log` columns + migration) + unrecognized brand fallback (heuristic CamelCase/compound detection + synthesis template) + catalog builder (scraping regen supplier URLs + LLM extraction + classification + enrichment) + disambiguation (rapidfuzz fuzzy matching against catalog, query-log write-back).
- **Complexity**: Feature build — multi-layer RAG feature touching query_analyzer, synthesis, database_client, run.py, and a new catalog pipeline of four scripts.
- **What makes this non-trivial**: The LLM classification step has no numeric threshold — the plan text initially implied one, and the Phase 4 inspector had to surface the conceptual correction before implementation. The catalog pipeline (extract → classify → build → enrich) is a mini-pipeline inside a plan.

### 12. Brand Catalog Conventional Product Supplemental
- **Duration**: ~4 hours (May 2)
- **Phases**: 3
- **Scope**: Corrective plan: removed an erroneous company-name exclusion filter introduced in Brand Handling plan Phase 5. Created `extract_conventional_products.py` (transition-relevance category allowlist, all LLM params config-driven) and `press_brand_research.py` (report-back only, delta of press-mentioned products not in catalog). Expanded catalog to 10,344 products. Validation phase ran 5 diagnostic queries including conventional product recognition (Roundup, Pivot Bio).
- **Complexity**: Moderate — two new scripts with careful LLM prompt design; no DB migrations; the challenge was scope discipline (conventional brands are intentionally included, not filtered).
- **What makes this instructive**: A plan whose primary job is to undo a prior plan's decision. The reconnaissance need is minimal because the codebase is already well understood; the intellectual work is defining "transition-relevant conventional product" at prompt level. Demonstrates that follow-on corrective plans can be compact (3 phases, ~4h) even when the original plan was substantial.

### 13. RAG Scope Boundary Improvement
- **Duration**: ~3 hours (May 2)
- **Phases**: 3 (reconnaissance, fix + routing, benchmark validation)
- **Scope**: Traced three failing query classifications (L1 large-scale grain, L2 farm-store business plan, L3 lawn care). Fixed: task-request heuristic that was silenced by any ag keyword in the query; scope instruction in synthesis externalized from hardcoded string to config (Article I violation); early-exit task-phrase list externalized. Phase 3 discovered a latent merge-logic regression — the extraction fix lacked an authoritative override flag — and fixed it within the benchmark validation phase.
- **Complexity**: Surgical — targeted changes in query_analyzer, synthesis.py, config.yaml, prompts/query_analysis.txt. The complexity was diagnostic, not implementational.
- **What makes this instructive**: A latent regression (missing `_task_request_override` flag) was invisible to all static analysis and only surfaced under live benchmark execution. Phase 3's "benchmark validation" phase produced a three-line code fix that wasn't planned. Validates the rule that validation phases should be "no planned code changes" not "no code changes permitted."

### 14. Brief Response Summarization
- **Duration**: ~5 hours (May 1)
- **Phases**: 2
- **Scope**: Phase 1: heuristic brief-intent detection (25 keyword/phrase patterns, config-driven), synthesis injection via `brief_format` config key, 4 tests. Phase 2: `answer_text` column in `rag_query_log` (migration), summarization routing that detects `prev_id` context, `summarize_prior_answer()` with TTL-based purge (PgBouncer-safe), `ask-stream.js` `complete` event updated for `prev_id` threading.
- **Complexity**: Feature build spanning heuristics, synthesis, DB storage, streaming path, and frontend JS event handling.
- **What makes this non-trivial**: Article I violations on hardcoded LLM instruction strings occurred twice — once per phase — even though Phase 2 had an explicit checklist item to fix Phase 1's violation. The pattern is systemic: implementers who externalise one string reliably inline the next one in a new function. Phase 2 also introduced a Python `global` scope bug caught only at review (global declaration inside an `if` block).

### 15. Auth Foundation
- **Duration**: ~9 hours (May 1)
- **Phases**: 5 (reconnaissance, migrations + encryption + config, email service + registration + verification + password reset, account deletion + data export + verification banner, integration testing + edge cases + documentation)
- **Scope**: Full auth system for public site: 2 DB migrations (auth_user_profiles, users with bcrypt hashes), shared encryption module (`profile_encryption.py`, Fernet + key rotation runbook), email service with Resend SDK in log mode (no live account), registration + email verification + password reset routes, rate limiting (5 routes, `flask-limiter`), WCAG-compliant auth templates (5 pages), account deletion with JSONB export, session-cached email_verified banner, 15+ integration tests against live DB with cleanup guards. Two latent bugs surfaced only under live test execution.
- **Complexity**: Feature build at the high end of the tier — 15+ files, WCAG compliance, encryption, rate limiting, multi-step email flows, and a thorough integration test phase that found bugs static review missed.
- **What makes this non-trivial**: Encryption module uses import-time fail-fast that cascades through Flask startup and every route that imports it — each of the three implementation phases had to use lazy imports specifically because of this. Rate limit strings appeared as hardcoded literals in four of five routes despite the plan correctly externalising one. The pattern: when a phase creates multiple rate-limited routes, inspectors analyse one representative route and assume the pattern generalises — it doesn't.

### 16. RAG Gains — Latency, Complexity, and Inefficiency Reduction
- **Duration**: ~3.5 hours (May 2)
- **Phases**: 4 (reconnaissance + baseline, zone translation + BM25 gate, dead code removal, config consolidation)
- **Scope**: Started as a 10-issue audit; reconnaissance invalidated 5 issues and found 2 more already fixed. Remaining work: CHANGELOG for pre-landed fixes (zone translation, BM25 gate), removal of `_build_essence_summary` and its 4 entangled artifacts (config key, global, test imports, config block comment), consolidation of two `direct_fetch` config sections into one.
- **Complexity**: Surgical — primarily administrative after Phase 1 scope correction.
- **What makes this instructive**: Phase 2 became an administrative phase (both code changes already existed in the codebase) rather than an implementation phase. The reconnaissance document itself was stale — it described the zone translation as having three queries when the code already had two. Demonstrates that reconnaissance docs can mis-state the current state if they rely on reading source without running verification commands. Fastest multi-phase plan in the dataset other than Diagnose Deployment.

### 17. Author Retrieval
- **Duration**: ~7 hours (May 3)
- **Phases**: 5 (GIN index migration, query analysis extension, retrieval branch, synthesis routing, tests + benchmark validation)
- **Scope**: End-to-end new query capability: GIN + `gin_trgm_ops` index on `academic_abstracts.authors` (migration 163), `author_mention` field in `QueryAnalysis` (regex heuristic + LLM backstop + 4 positive / 4 negative prompt examples), parallel `fetch_abstracts_by_author()` branch in retrieval (ILIKE last-name + pg_trgm similarity, joins to `abstract_scoring_reviews` and `environmental_profiles`), `author_research` intent in synthesis (Priority 0, paper-list format from config, clean-miss guard in both blocking and streaming paths), 25 new tests across 4 modules. Two production bugs found during Phase 5: `%s AS similarity` → `%s::float AS similarity` (numeric→Decimal TypeError), and `format_abstract_citation` assuming `authors` is a string when psycopg2 deserializes JSONB arrays as Python lists.
- **Complexity**: Feature build — 6 modules modified, new DB migration, new test file, benchmark extended to 70 questions across 16 categories.
- **What makes this non-trivial**: Production bugs only surfaced during the test phase, not during implementation or code review. Both were type-coercion issues invisible to unit tests that mock DB responses. The inspector's concerns about implementation mechanics (pool capacity, JOIN shape, score strategy) were all overcautious — the concerns about interface semantics ("what does 'hidden' mean in `_ROLE_MATRIX`?") were the valuable ones.

---

## Completed Plans (May 5-12, 2026) — Estimated Time

Eleven plans closed across May 5-12. Durations are estimates derived from phase count, retrospective artifact density, and complexity tier — not tracked time.

### 18. Institution Retrieval
- **Duration**: ~18 hours total (Phases 1-2: ~8h data acquisition + config; Phases 3-6: ~10h full RAG integration)
- **Phases**: 6 (WWW pipeline onboarding → institution-domain config → query analysis extension → retrieval branch → synthesis routing → tests + benchmark)
- **Scope**: End-to-end institution-sourced retrieval. Phase 1 onboarded 15 institutional domains (IPES-Food, Land Institute, CIFOR-ICRAF, Bioversity/CIAT, American Farmland Trust, OFRF, Biodynamic Association, IFOAM, IATP, Quivira Coalition, Winrock, Organic Research Centre, IFAD, WRI, UCS) through the WWW pipeline (W0 site-rules, W1–W3 scoring). Phase 2 added config-driven institution-to-domain map with feature gate. Phases 3-5 added `institution_mention` field to `QueryAnalysis` (heuristic pre-detection + LLM extraction with 4 few-shot examples), parallel `fetch_content_by_institution()` retrieval branch deduplicating against vector-search results, and a dedicated `institution_research` synthesis intent with corpus-scope disclosure and config-driven clean-miss guard. Phase 6 added 12+ unit tests and confirmed benchmark scores held within variance. Five W0 failure modes documented (flat CMS pattern, bare "/" in exclude_patterns, BROAD site over-inclusion, malformed sitemap entries, no-sitemap seed fallback gap).
- **Complexity**: Feature build (data acquisition + full RAG integration)
- **What makes this non-trivial**: Phase 1 was operationally the most labor-intensive phase in the dataset — heavy per-domain judgment, six canonical domain corrections, multiple W0 bug patterns discovered, manual JSON edits, and psql seed insertions. Phase 6 live testing found 4 production code fixes (architecture detection, format template prohibition, detected_architecture ternary, rubric guidance) invisible to all prior review. Pattern-cloning the author_research synthesis path without verifying all populated globals had consumers produced dead code that survived inspection, code review, and the constitutional audit — caught only by Phase 6 test execution.

### 19. Browser Fetch Mode
- **Duration**: ~4 hours (May 5)
- **Phases**: 3 (reconnaissance + config-ahead, browser fetch implementation, domain configuration + validation)
- **Scope**: Added Crawl4AI-based browser fetch path to `trafilatura_manager.py`, enabling the WWW pipeline to retrieve content from Cloudflare-protected and JavaScript-heavy domains that block the existing Trafilatura HTTP path. Configured two target institutional domains (alliancebioversityciat.org, www.ifad.org). Fixed a pre-existing `UnboundLocalError` in the `--url` flag path of `crawl_batch()` discovered during live validation.
- **Complexity**: Infrastructure (WWW pipeline extension)
- **What makes this instructive**: Phase 2 reviewer found 3 stale CLAUDE.md entries carried forward when the phase edited the file — a phantom function reference, a stale Pydantic field, and a misplaced config snippet from a different pipeline step. This is the canonical "editing a file copies its stale documentation forward" failure mode. The pre-existing `UnboundLocalError` was invisible to static analysis and unit tests; it only appeared when the new `--url` code path was exercised under operational validation.

### 20. Public Docs Personalization Sync
- **Duration**: ~7 hours (May 5)
- **Phases**: 7 (third-party service audit, privacy policy revision, open source + attributions, terms of service draft, about page targeted edits, cross-document consistency pass, publication prep)
- **Scope**: Synchronized four public-facing documents (Privacy Policy, Open Source & Attributions, Terms of Service, About) with the reality of the personalization feature and the platform's legal entity status as a California Public Benefit Corporation. A structured third-party service audit (Phase 1) enumerated all AI providers, analytics tools, and data-touching services as ground truth. Phases 2-5 revised each document. Phase 6 ran a cross-document consistency pass. Phase 7 wired the new Terms of Service route, footer link, and sitemap entry.
- **Complexity**: Content revision (low code, high editorial judgment)
- **What makes this instructive**: The Phase 1 third-party service audit pattern — enumerate every external service, classify by data exposure, verify against actual code — is reusable as a pre-launch checklist for any platform with personalization or analytics. Four documents that reference each other must be revised in a structured order (audit first, broadest policy second, specific policies after); ad-hoc revision creates inconsistencies that require a full consistency pass to find.

### 21. Learnings Consolidation System
- **Duration**: ~3.5 hours (May 5)
- **Phases**: 3 (audit retrospective artifacts + identify gap, enhance learnings-consolidator agent, wire into close-plan + document workflow)
- **Scope**: Audited 19 completed-plan retrospectives to identify patterns not yet promoted to persistent memory. Extended the learnings-consolidator agent to produce human-reviewable memory promotion recommendations at close-plan time. Updated `/close-plan` to surface those recommendations at Gate 3. Added `memory-recommendations.md` to the lifecycle table and the plan-closer deletion list. Updated `docs/workflow/workflow_automation.md` to document the complete learnings-to-memory feedback loop.
- **Complexity**: Tooling (workflow automation enhancement — agent instruction files, not Python code)
- **What makes this instructive**: Implementation work here was prompt engineering and Markdown template editing, not Python. The review gate still ran (constitutional auditor confirmed applicable articles pass). A 3-phase tooling plan with zero Python changes clocks in at ~1.2h/phase — below the dataset average — because the bottleneck is editorial judgment, not code construction.

### 22. Person Layer
- **Duration**: ~22 hours (multi-session, May 5-9)
- **Phases**: 10 (foundation + DB migration, corpus mining ×3, candidate aggregation + LLM gate, stub generation + learn_documents insertion, RAG retrieval integration, cohort B gap-finder + verification, roster mining [Phase 7.5], measurement hooks + evaluation plan, routine advancement pipeline)
- **Scope**: Complete pipeline for discovering, vetting, and surfacing domain-relevant people in RAG retrieval. 13 new backend scripts (3 corpus miners, derivation, filtering, aggregation, stub generation, person_mentions population, cohort-B LLM gap-finder, mention verification CLI, organization discovery, roster miner, advancement pipeline), 1 new frontend script, 6 modified RAG modules (config.yaml, database_client, learn_supplement, retrieval, run.py, RAG CLAUDE.md), 3 new test files, DB migration 164 (person_mentions table), 22 data artifact files. Scheduler registered as daily merged/3 and merged/4 pipeline steps. Evaluation plan and 120-day spot-check documentation produced.
- **Complexity**: Feature build (largest plan in dataset by phase count)
- **What makes this non-trivial**: Three-pool corpus mining (abstracts, web, transcripts) → union-find aggregation → LLM inclusion gate → stub generation → RAG retrieval integration → scheduler registration is a complete mini-pipeline inside a plan. Phase 7.5 (roster mining) was added mid-plan to fill a practitioner coverage gap discovered during Phase 7 — in-plan scope expansion executed cleanly without derailing subsequent phases. The evaluation plan (Phase 8) formalizes a 120-day post-cutover review process that most plans defer or skip.

### 23. Person Three-Layer ("Who Is X?" Architecture)
- **Duration**: ~10 hours (May 10)
- **Phases**: 6 (reconnaissance + schema, citation leak fix + authorship signal lift, LLM stub enrichment, corpus gap analysis, person-query synthesis path, benchmark validation)
- **Scope**: Built the "Who Is X?" answer architecture directly on top of Person Layer. Resolved a citation leak (person profile pages appearing as citable destinations in sidebar). Added structured `identity`/`standing`/`expected_presence_shape` fields to all 1,703 person stubs via Gemini Flash Lite LLM enrichment. DB migration 170 added `content` JSONB + GIN index to `learn_documents`. Classified stubs into 12 canonical topic areas. Wired `person_query` detection and a three-layer synthesis path (person record → learn_supplement context → corpus passage citations) with honest corpus-depth framing. Category R benchmark: mean 4.59/5.00 across 3 runs; no regressions in other categories.
- **Complexity**: Feature build (builds directly on Person Layer)
- **What makes this non-trivial**: The bulk LLM enrichment run (1,703 stubs, Gemini Flash Lite) was the most operationally expensive step in this plan — requires dedicated execution time and output quality spot-check. Phase 2's `content` JSONB column gap was predicted by the inspector before implementation and confirmed immediately — one of the few cases where an interface-contract concern was exactly right and prevented a production error rather than surfacing at test time.

### 24. Corpus Gap Phase 1 — Disambiguation, Audit, and Bucketing
- **Duration**: ~5 hours (May 11)
- **Phases**: 2 (diagnostic + disambiguation + individual/institution split, mining audit + sunset filter + output bucketing)
- **Scope**: Processed 629 raw person stubs from Person Layer into deployment-ready cohorts. Phase 1: fuzzy disambiguation via rapidfuzz (30 merges confirmed), individual/institution classification split. Phase 2: mining audit against known channels/abstracts, sunset filter for low-signal stubs, output bucketing into advancement tiers (ready/pending/sunset).
- **Complexity**: Data/surgical
- **What makes this instructive**: Reviewer found two false-positive fuzzy matches (`person-eric`/`person-erica`, score 88.9; `person-john`/`person-jon`, score 85.7) — single-token names without a two-token minimum guard are not safe for automated merge. An institution-chapter false-positive (UC Master Gardeners of Napa County vs. Santa Clara County) demonstrates that institution-to-institution candidates always require LLM review regardless of score. An Article I violation was raised for module-level Python keyword lists (`INSTITUTION_KEYWORDS`, `INSTITUTION_HEURISTIC_WORDS`) that should live in JSON data files.

### 25. Corpus Gap Phase 2 — Attribution Backfill and Channel Additions
- **Duration**: ~3 hours (May 11)
- **Phases**: 1 (attribution backfill, channel additions, domain additions, follow-through specs)
- **Scope**: Backfilled attribution for person stubs missing source links. Added channels.json entries for individual practitioners discovered through corpus mining. Added domain entries for practitioner-affiliated organizations. Produced YouTube channel lookup results and documented IDs with confidence levels for human review gate.
- **Complexity**: Data (backfill + channel acquisition)
- **What makes this instructive**: A 1-phase plan whose primary constraint was data verification rather than code. The YouTube channel ID lookup problem (no live web access during tool execution) required the executor to surface IDs from training knowledge and document confidence levels explicitly — three channels were flagged for human ID verification before activation. Single-phase plans close with constitutional audit only (no phase-level review artifact); this is acceptable when scope is narrow and the audit has no blocking findings.

### 26. RAG Answer Altitude
- **Duration**: ~8 hours (May 11)
- **Phases**: 6 (pre-phase variance baseline, altitude heuristic + signal validation, branched synthesis path, rubric expansion + benchmark, gate path, documentation + closeout)
- **Scope**: Added an answer-altitude system to RAG that branches synthesis based on query complexity signals — specific questions get direct answers; complex/exploratory questions get expanded rubric treatment with additional source context. Components: pre-phase variance baseline locked regression thresholds; altitude heuristic in `query_analyzer.py` (composite signal: query length + missing context + source diversity); branched synthesis path in `synthesis.py`; rubric expansion in gate config; `derive_answer_altitude` wired through `rag_client.py` (frontend module). Gate path added to allow config-driven altitude suppression for specific query categories.
- **Complexity**: Feature build (branched synthesis system)
- **What makes this non-trivial**: A pre-phase variance measurement phase (not part of the original plan structure) was added defensively to lock regression thresholds before code changed — caught a stale plan count discrepancy before any implementation started. Count inaccuracies propagated across multiple artifacts throughout the plan (67-question vs 77-question header in dry-run report, CHANGELOG contradicting benchmark output) — a pattern that inspection consistently misses and review consistently catches.

### 27. Script Guide Maintenance
- **Duration**: ~7 hours (May 12)
- **Phases**: 3 (reconnaissance + module-to-doc mapping, hook infrastructure + /wrap-change skill + HTML builder, module annotation pass)
- **Scope**: Built the infrastructure for keeping script guide documentation current with code changes. Phase 1 produced a canonical module-to-doc mapping of 516 verified entries across 13 script guide files (558 raw entries → 42 explicit disposition decisions for phantom entries, deprecated modules, and symlinked paths). Phase 2 delivered PostToolUse/Stop session hooks in `.claude/settings.local.json`, the `/wrap-change` skill for routing doc updates after ad-hoc code edits, and `scripts/build_script_guide.py` generating a browsable HTML index at `docs/script_guide/index.html`. Phase 3 used a generated automation script (`annotate_doc_comments.py`) to add `# doc: docs/script_guide/<filename>.md` header comments to all 516 modules; script was archived after use.
- **Complexity**: Tooling/infrastructure (documentation maintenance system)
- **What makes this non-trivial**: This is the first plan in the dataset where a significant portion of implementation work was done by a generated automation script rather than direct file editing — the script touched 516 files in one pass, then was archived. Phase 1 mapping required 42 explicit disposition decisions (not mechanical extraction) because script guide files contain non-module headings, symlinked entries, and deprecated modules that require human judgment to classify.

### 28. Office Homepage Cleanup + Script Guide Integration
- **Duration**: ~2 hours (May 12)
- **Phases**: 1
- **Scope**: Removed stale Deployment Management and Creator Candidates cards from the office homepage. Archived the Deployment Management subsystem (`routes/deployment.py`, modules, templates, tests) to `frontend/office/archive/deployment/`. Added a Script Guide card linking to a new `script_guide_bp` blueprint with GET dashboard, POST build trigger, and GET view routes. All tunables (build script path, output path, timeout) in `config.yaml` per Article I.
- **Complexity**: Surgical (single-phase archive + replace)
- **What makes this instructive**: Archiving a subsystem (rather than deleting) takes the same effort as deletion but preserves institutional history. The single-phase structure is appropriate when scope is tightly bounded and the only risk is forgetting to update CLAUDE.md — which the checklist covered explicitly.

### 29. MVP Deployment (Class E Production Launch)
- **Duration**: ~8 hours (May 12)
- **Phases**: 4 (enrichment validation + data inventory, frontend data rebuild, security audit + CVE remediation, Fly.io production deployment)
- **Scope**: Full Class E production deployment of the production system. Phase 1 audited 12 sync tables against production baseline, documented enrichment coverage across 5 types, identified 28 orphan pages, produced `validation_report.md`. Phase 2 rebuilt `learn_documents` (13,098 rows, 0 null embeddings), regenerated autocomplete file, refreshed all 13 materialized views. Phase 3 ran `pip-audit`, patched Flask/Werkzeug/Jinja2/requests stack, patched 8 CVEs across www and learn_enrichment pipelines (path traversal, SSRF, command injection, ReDoS, content-type bypass), produced `security_signoff.md` (PASS WITH ACCEPTED RISKS). Phase 4 applied 16 pending migrations (155-170) via Fly.io proxy, synced all 13 production tables, rebuilt 4 HNSW indexes, refreshed materialized views, deployed application, verified `/health` and `/health/deep` returning 200 healthy.
- **Complexity**: Production deployment (operational execution of pre-validated plan)
- **What makes this instructive**: A deployment plan's phases are operational, not architectural — the intellectual work happened in prior plans; this plan executes and verifies. Phase sequencing matters: validation report first, data rebuild second, security audit third, deploy last. The security audit phase as a mandatory pre-deploy gate (not a separate plan) ensures CVEs are patched before traffic lands.

---

## Velocity Summary Table

| Plan | Phases | Hours | Files Changed | Complexity |
|------|--------|-------|---------------|------------|
| Workflow Automation | 6 | ~12h | 20+ new | Architectural |
| Diagnose Deployment | 2 | ~0.5h | 2 | Diagnostic |
| Codebase Audit | 8 | ~8h | 875+ modified, 29 new docs | Broad sweep |
| Codebase Repair | 6 | ~4h | 13 | Surgical |
| Permies Optimization | 5 | ~4h | 6 + 3 migrations | Cross-system data |
| Umami Setup | 3 | ~3h | 0 (infra only) | Infrastructure |
| Analytics Instrumentation | 2 | ~3h | 13 | Cross-layer |
| Ask Results Restructure | 5 | ~12h | 19 | Multi-layer rendering |
| Citation Balance | 4 | ~12h | 15+ + 1 migration | LLM pipeline integration |
| Platform Trust *(2/4 phases)* | 2† | ~4h | 8 + 1 migration | Cross-layer |
| Brand Handling Improvements | 5 active‡ | ~7h* | 10+ (query pipeline + 4 catalog scripts) | Feature build |
| Brand Catalog Conventional | 3 | ~4h* | 2 new scripts + catalog JSON | Feature build |
| RAG Scope Boundary | 3 | ~3h* | 4 (query_analyzer, synthesis, config, prompts) | Surgical |
| Brief Response Summarization | 2 | ~5h* | 6 + 1 migration + JS | Feature build |
| Auth Foundation | 5 | ~9h* | 15+ (auth system, templates, migrations) | Feature build |
| RAG Gains | 4 | ~3.5h* | 5 (dead code removal + config consolidation) | Surgical |
| Author Retrieval | 5 | ~7h* | 14 (6 modules + 4 tests + migration + docs) | Feature build |
| Institution Retrieval | 6 | ~18h* | web_sources.json + 15 site_rules + 5 RAG modules + 12+ tests | Feature build (data acquisition + full integration) |
| Browser Fetch Mode | 3 | ~4h* | trafilatura_manager + requirements + 2 domain configs + docs | Infrastructure |
| Public Docs Sync | 7 | ~7h* | 4 HTML templates + route + footer + sitemap | Content revision |
| Learnings Consolidation | 3 | ~3.5h* | workflow docs + agent templates | Tooling |
| Person Layer | 10 | ~22h* | 13 new scripts + 1 frontend script + 6 RAG modules + 3 tests + 1 migration + 22 data files | Feature build |
| Person Three-Layer | 6 | ~10h* | synthesis + database_client + 1 migration + 5 scripts + tests | Feature build |
| Corpus Gap Phase 1 | 2 | ~5h* | 2 scripts + data artifacts | Data/surgical |
| Corpus Gap Phase 2 | 1 | ~3h* | channels.json + 3 data files + 2 scripts | Data |
| RAG Answer Altitude | 6 | ~8h* | query_analyzer + synthesis + rag_client + config + prompt template | Feature build |
| Script Guide Maintenance | 3 | ~7h* | 516 annotated modules + build script + HTML index + hooks + skill | Tooling |
| Office Homepage Cleanup | 1 | ~2h* | 1 new route + template + config + archive | Surgical |
| MVP Deployment | 4 | ~8h* | 9 source files (CVE patches) + 5 deployment docs | Production deployment |
| Methodology Normalization | 7 | ~5h§ | 10 agent files + 5 skill files + 6 config/template files + 3 new docs + 3 worked example files | Tooling/workflow |

† Phases 3-4 deferred; subsequent brand and content work absorbed into dedicated plans.  
‡ Phases 6-7 superseded by Brand Catalog Conventional plan.  
\* Estimated from complexity signals — not tracked time.  
§ Methodology-only work — no application code changed.

**Totals (closed plans)**: 30 plans, ~129 phases, ~205 active hours across 25 calendar days (April 24 – May 18, 2026).

---

## Completed Plans (May 18, 2026) — Estimated Time

### 30. Methodology Normalization
- **Duration**: ~5 hours (single day, 2026-05-18)
- **Phases**: 7 (Constitutional Split, Path Elimination, Subagent Normalization, Skill Modularization, Graph/Hook Configurability, Interview-Driven Onboarding, Port-Planning Example + Validation)
- **Scope**: Decoupled the planning methodology from its original production project. Split the 11-article constitution into a 9-article domain-neutral base (`docs/constitution.md`, v3.0.0) and a project-specific extension (`docs/save_ag_extension.md`). Replaced all `/mnt/aiag` hardcoded paths across 10 agent files and 5 skill files with config-driven project-root resolution. Parameterized domain framing, documentation-path references, and CHANGELOG routing via `.aiag/config.yaml`. Conditioned substrate features (knowledge graph, doc-routing) on config flags. Created BOOTSTRAP.md as the methodology entry point and `.claude/commands/onboard.md` as an interview-driven setup skill. Produced a Cursor port-planning worked example in `examples/cursor_port_planning/`.
- **Complexity**: Tooling/workflow — pure prompt engineering and Markdown authoring; no application code, no database changes, no production systems touched
- **What makes this instructive**: Decoupling a methodology from a live project while the project continues to use it requires distinguishing between "this is project convention" and "this is methodology convention." The constitution split is the clearest example — the original 11-article document had two types of articles (domain-neutral principles and project-specific enforcement), and separating them made the distinction explicit for the first time. The seven phases are ordered from most structural (constitution, paths) to most additive (onboarding, worked example), which meant later phases built on solid foundations rather than racing to fix earlier ones. Phase 7 (this phase) found and fixed a residual coupling issue — stale migration notes in 8 agent files containing `/mnt/aiag` — that earlier phases had explicitly accepted as "correct instructional prose." The verify criterion's zero-line requirement forced the cleanup.

---

## Derived Velocity Benchmarks

Use these as baselines when estimating future work:

### By complexity tier

| Tier | Description | Typical Duration | Example |
|------|-------------|-----------------|---------|
| **Diagnostic** | Root-cause investigation, minimal code change | 0.5-1 hour | Diagnose Deployment |
| **Infrastructure** | Deploy/configure external service, no app logic | 2-5 hours | Umami Setup, Browser Fetch Mode |
| **Surgical** | Targeted fixes across known locations | 2-5 hours | Codebase Repair, Office Homepage Cleanup |
| **Cross-system** | Changes spanning config + DB + app logic | 4-6 hours | Permies Optimization |
| **Content revision** | Multiple documents requiring editorial consistency; low code complexity | 6-10 hours | Public Docs Sync |
| **Tooling/workflow** | Automation infrastructure — hooks, skills, agent templates | 3-8 hours | Script Guide Maintenance, Learnings Consolidation |
| **Feature build** | New capability with LLM/DB/UI integration | 8-22 hours | Citation Balance, Person Layer |
| **Rendering restructure** | Multi-file UI/template/CSS overhaul | 8-12 hours | Ask Results Restructure |
| **Broad sweep** | Full-codebase analysis + remediation | 6-10 hours | Codebase Audit |
| **Data acquisition** | WWW/pipeline onboarding with per-domain judgment calls | 5-10 hours | Institution Retrieval Phase 1 |
| **Production deployment** | Operational execution: data rebuild + security gate + live deploy | 6-10 hours | MVP Deployment |
| **Architectural** | Framework/infrastructure from scratch | 10-14 hours | Workflow Automation |

### Per-phase throughput

- **Average time per phase**: ~1.6 hours (200h / 122 phases — up from 1.4h at the May 3 snapshot, driven by larger feature builds and bulk LLM enrichment phases in the May 5-12 cohort)
- **Fastest phases**: Diagnostic/recon phases (~20-30 min), config-only phases (~30 min), administrative phases where code changes pre-landed (~30 min), single-phase surgical plans (~1-1.5h total)
- **Slowest phases**: LLM integration phases (~3h), broad linting/security sweep phases (~2h), large auth/registration phases with 8-10 files (~2.5h), bulk LLM enrichment runs on 1,000+ items (~2-3h execution + spot-check)
- **Review gate overhead**: ~10-15 min per phase (test-gate + reviewer + auditor + phase-completer chain runs automatically)

### Scaling factors

- **Database migration in the plan**: Add ~30-60 min (schema design + write + test + verify)
- **LLM-in-the-loop feature**: Add ~2-4 hours (prompt engineering + batch handling + caching + validation)
- **WCAG compliance requirement**: Add ~30-60 min (touch targets, focus indicators, contrast checks)
- **Production deployment included**: Add ~1-2 hours (sync scripts, constraint fixes, verification)
- **Deferred phase**: Saves ~2-4 hours per deferred phase (documented for future plan, not blocked)
- **Encryption or import-time fail-fast module**: Add ~30-60 min per phase that touches it (lazy-import discipline required at every route that imports the module)
- **Rate-limited endpoints (multiple routes)**: Add ~30 min to enumerate each endpoint's config key explicitly — inspectors consistently miss the n-1 routes after analyzing the first
- **Benchmark validation phase**: Add ~1-2 hours (3 required runs minimum, plus baseline identification and regression check across non-target categories)
- **Corrective plan (undoing a prior decision)**: Base complexity is lower than the original feature, but scope-definition work at prompt/design level absorbs time that would otherwise go to coding (~60-70% of a comparable new-feature plan)
- **WWW pipeline domain onboarding (per domain)**: ~20-40 min per domain for W0 review + populate. Add ~60 min per BROAD site (mandatory manual JSON override). Add ~30 min per Cloudflare-blocked domain that needs Crawl4AI bypass or psql seed insertion. 15-domain batch = 5-8 hours of Phase 1 operational work.
- **robots.txt compliance check**: Add ~10-15 min per domain at risk. `ai-train=no` signal or blanket AI-crawler Disallow forces removal regardless of technical accessibility (Article VII). Factor in ~1-2 domain removals per 15-domain batch.
- **Domain research pre-phase for data acquisition plans**: A pre-phase that audits canonical domains, verifies redirect chains, documents required include/exclude paths, and identifies known 403 risks saves ~2-3 hours of trial-and-error during execution. Institution Retrieval's `phase-1-research.md` is the template.
- **W1 Cloudflare aiohttp block (per domain)**: ~30 min to diagnose, then blocked indefinitely until W1 infrastructure fix (Crawl4AI or browser-like headers). Budget these domains as "pending" for content yield purposes. ~20% of institutional domains may be affected.
- **Bulk LLM enrichment pass (500+ items)**: Add ~2-4 hours for execution time + output quality spot-check. Requires dedicated subtask with failure-mode handling (checkpoint/resume). Person Three-Layer's 1,703-stub enrichment run is the reference.
- **Multi-plan series (phased continuation)**: Cross-plan boundary (e.g., corpus-gap-phase1 → corpus-gap-phase2) adds ~30 min per transition for context review and prerequisite verification. Combined estimate is ~10% less than the sum of individual plan estimates due to shared familiarity.
- **Automation-assisted bulk file annotation**: First-time setup of annotation script ~1-2h; bulk execution ~10 min. Should include an archive step for the one-use script. The generate-then-archive pattern (Script Guide Maintenance Phase 3) is the reference.

---

## What This Process Does Well (and What Takes Longer)

### Fast
- Automated lint/format across hundreds of files (875 files in ~1 hour)
- Config-driven changes (config.yaml + module reads it — Article I pattern)
- Template/CSS changes when the rendering path is well-understood
- Database migrations when the schema is clear
- Diagnostic plans with clear reproduction steps
- Reconnaissance phases that narrow scope (14 expected issues to 3 actual)
- Single-phase surgical plans with tightly bounded scope (~1-2h total)
- Automation-assisted bulk annotation when the mapping is pre-established

### Moderate
- Cross-module Python logic changes (tracing data flow through 4-6 modules)
- LLM prompt construction and calibration (empirical tuning required)
- Infrastructure deployment with external service quirks
- Content revision plans when a structured Phase 1 audit establishes ground truth

### Slower than expected
- Silent bugs in LLM integration (cache key mismatches, token limits, type coercion — only found by running real queries or live integration tests)
- Documentation updates that span multiple files (CHANGELOG, deployment guides, CLAUDE.md)
- Plans where Phase 1 recon reveals the scope is different from initial assumptions
- Article I compliance on LLM instruction strings — implementers who externalize one string reliably inline the next one in a new function, requiring a review fix pass
- Stale numeric counts in documentation — every phase that adds items to files containing count references leaves stale sibling documents; reviewers catch it every time, inspectors never do

---

## Process Characteristics That Enable This Velocity

1. **Single-developer decision authority**: No PR review cycles, no standups, no context-switching between reviewers. The human architect makes design decisions; Claude Code executes.

2. **Automated review chain**: test-gate + reviewer + auditor + phase-completer runs as a serial pipeline per phase. Catches regressions without manual test runs.

3. **Constitution-driven consistency**: 11 articles enforce patterns (config-first, filesystem-first, WCAG, etc.) so implementation choices are pre-decided, not debated per-phase.

4. **Phased plans with short-circuit**: Each phase has a clear checklist and verify step. If a phase discovers the scope is wrong, it corrects before proceeding (not after).

5. **Deferred work is documented, not blocked**: When a change is out of scope or premature, it gets a design document (e.g., stance-diversity-deferred.md) rather than blocking the plan.

6. **Deep codebase familiarity**: The architect knows the full system — pipeline structure, database schema, deployment topology, RAG retrieval path. Claude Code has persistent memory and subsystem documentation. Combined, this eliminates most "figure out how it works" time.

7. **Integration test phases find what static analysis cannot**: Across both cohorts, every plan that included a live execution phase found bugs that static analysis and code review missed. Type coercion errors, `global` declaration scope bugs, merge-logic regressions, and dead globals from pattern-cloning all surfaced only under real invocation. Test phases are not rubber stamps — they are the highest-signal quality gate in the chain.

8. **Reconnaissance phase investment consistently correct-scopes plans**: Of the May 1-3 cohort plans, four included a dedicated Phase 1 recon. In three of those, recon materially changed the implementation scope. This pattern held in the May 5-12 cohort (RAG Answer Altitude added a pre-phase variance baseline; Person Layer used Phase 1 to establish DB schema before any corpus mining). Return on recon is reliably positive.

9. **Data acquisition phases surface infrastructure defects that code phases miss**: Institution Retrieval Phase 1 found five distinct W0 failure modes and one W1 infrastructure class bug (Cloudflare aiohttp fingerprinting) that would have been invisible in any Python-only development session. Operational phases that exercise the full pipeline under real conditions are the data-layer equivalent of live integration tests.

10. **Domain redirect corrections require pre-research investment, not trial-and-error**: Six of fifteen successfully onboarded domains had canonical domain names that differed from the plan's original assumptions (permanent redirects, www-prefix normalization, organizational mergers). The `phase-1-research.md` pattern — auditing redirect chains and robots.txt before execution — transforms domain onboarding from investigative to confirmatory.

11. **Large plans (9-10 phases) execute as naturally as small ones**: Person Layer completed 10 phases across 4 calendar days without structural overhead beyond what 4-phase plans require. The phased structure and review gates scale proportionally — there is no inflection point where plan size creates coordination friction. The per-phase average cost stays stable regardless of plan length.

12. **Inspector concerns about implementation mechanics are overcautious; interface/contract concerns are reliably predictive**: Across the May 5-12 cohort, concerns about regex precision, config loading timing, HNSW efficiency, deduplication key choice, and integration test data availability all resolved without issues. By contrast, concerns about interface semantics between adjacent phases (what shape does Phase N's output need to be for Phase N+1 to consume it?) were exactly right every time they were raised. When writing inspector prompts, weight interface/contract concerns and discount mechanics concerns.

---

## How to Use This Document for Estimation

When estimating a new plan:

1. **Identify the complexity tier** from the table above
2. **Count the expected phases** — multiply by ~1.6h for a rough baseline (up from 1.4h; the higher figure reflects larger feature builds)
3. **Apply scaling factors** for migrations, LLM work, WCAG, deployment
4. **Check for scope uncertainty** — if Phase 1 recon might change the scope significantly, add a buffer of 1-2 hours
5. **Round to the nearest half-day** (4h or 8h) for communication purposes

Example: "Add a new RAG feature with DB migration and LLM classification"
- Tier: Feature build (8-22h range; target 8-12h for a focused 4-5 phase plan)
- Phases: ~4 (recon, core logic, cache/DB, validation)
- Scaling: +1h migration, +2h LLM integration = already in base
- Estimate: **8-12 active hours, completable in 1-2 working days**

Do NOT estimate in sprints, story points, or calendar weeks. This process operates in active hours within intensive sessions.

---

## Interpreted findings

`docs/what_the_records_show.md` draws out the patterns that are visible across these histories but require reading the full set to see: what each gate's failure-mode signature looks like, which defect classes recur plan-to-plan, what the inspector calibration data shows, and what velocity compounding actually looks like in the numbers. If you are trying to evaluate the methodology's claims rather than calibrate your own estimates, start there.
