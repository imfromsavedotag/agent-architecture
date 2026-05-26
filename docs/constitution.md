## Foundational Principle: Excellence Over Expediency

**This principle supersedes all others**: Quality and correctness take absolute precedence over speed, convenience, or perceived progress. There are no acceptable shortcuts. Every error must be fixed when found. Every solution must be validated before it is considered successful.

---

## Preamble

This constitution establishes the non-negotiable principles governing this project's development. These principles ensure we build a system of uncompromising quality. All code, decisions, and architectural choices must comply with these articles.

**Version**: 3.0.0
**Established**: 2025-09-21
**Last Amendment**: 2026-05-18

---

## The Core Principles

### Article I: Configuration Over Code
All variables, paths, thresholds, and settings MUST be externalized to configuration files. No hardcoded values are permitted in production code.

**Data Externalization Requirements**:
1. **Operational Configuration** (paths, thresholds, API endpoints, feature flags) → YAML files (e.g., `config.yaml`)
2. **Reference Data** (lookup tables, mappings, classifications, validation schemas, country codes, precision mappings) → JSON files in `data/` directories
3. **Secrets** (API keys, passwords) → Environment variables loaded via direnv from encrypted secrets store (never in code or config)

**Violations include**:
- Dictionaries or mappings embedded in code that should be data files
- Hardcoded thresholds, limits, or magic numbers that should be in YAML
- Database check constraints that duplicate values defined elsewhere
- Inline CSS, hardcoded URLs, or environment-specific paths in code

### Article II: Isolated Environments
Each pipeline step must operate in its own isolated virtual environment to prevent dependency conflicts and ensure reproducibility.

### Article III: Auditability
Every AI decision, processing step, and quality determination must be fully auditable, with structured logs and clear confidence scores.

### Article IV: Isolated Testing
All testing must occur in isolated, temporary environments. Production data may be used, but must never be modified during testing.

### Article V: Knowledge Evolution
The knowledge base grows only through validated discoveries, not assumptions. Additions require a minimum frequency and context validation.

### Article VI: Token Efficiency
AI responses must be complete, parseable, and honor token limits. Truncated or incomplete responses are considered a critical failure.

### Article VII: Separation of Concerns
Each module, function, and pipeline step has a single, well-defined responsibility. Code must be modular with clear interface boundaries.

### Article VIII: Documentation as Code
Documentation is the source of truth and must be maintained in perfect synchronization with the code. No code without documentation, no documentation without code. CLAUDE.md files describe **current structure** — what exists, how it connects, and how to use it. Change history (dated entries, bug fix narratives, phase completion records, migration timelines) belongs in `docs/CHANGELOG.md`, not in CLAUDE.md files. Subsystem reference docs describe module inventories, route maps, and entry points; they are updated when structure changes.

### Article IX: Phased Planning Discipline
All non-trivial work proceeds through phased plans with explicit human gates. Each plan has a primer block that orients any session to the current state. Phases are sized to fit within a context window, carry checklists with verification criteria, and pass through a review chain (test-gate, code review, constitutional audit) before completion. A persistent learnings file accumulates patterns across plans so that future planning benefits from past experience.

---

## Governance

### Constitutional Supremacy
The constitution is the supreme law of the project. No request or decision can violate it without an explicit, documented override.

### Violation Protocol
When a violation is detected:
1. **STOP** and identify the specific article violated.
2. **EXPLAIN** the violation and its implications.
3. **REQUIRE** either an amendment to the constitution or a documented override before proceeding.

### Amendment Process
Modifications to this constitution require a formal proposal, a technical review, and stakeholder approval. All amendments must be versioned.

---
