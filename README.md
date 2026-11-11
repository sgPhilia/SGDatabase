# SGDatabase — LearnDatabase Textbook

SGDatabase is the repository for LearnDatabase: a research-backed, university-level textbook and practical engineering knowledge base for understanding database systems and the engineering choices behind them. It is intended to read as a collection of technical books, not a set of short tutorials. It pairs detailed theory with practical experiments, with Rust Edition 2024 as the primary implementation language and C++ as an occasional comparison.

## Current state

The initial repository assessment is complete, the textbook-scale structure is established, and Volume I is underway. Read [AI_CONTEXT.md](AI_CONTEXT.md) to resume work, [SESSION_HANDOFF.md](SESSION_HANDOFF.md) for the next action, and [PROGRESS.md](PROGRESS.md) for high-level status.

## Learning approach

Each major topic is developed as one or more connected textbook chapters, with two learning tracks:

- **Theory:** historical context, concepts, problems, algorithms, invariants, mathematical foundations where relevant, trade-offs, and system-specific choices.
- **Engineering:** worked traces, experiments, exercises, Rust implementations, correctness requirements, and selected C++ comparisons.

The path begins with computer and data foundations, proceeds through relational theory and SQL, then develops storage, indexing, query processing, transactions, recovery, and distributed systems. Complex topics may span several chapters. Prerequisites, diagrams, glossary terms, cross-references, exercises, and claim-level references are explicit.

## Proposed repository map

- `docs/` — textbook volumes, parts, and numbered chapters.
- `implementations/rust/` — staged Rust learning projects, introduced when their prerequisites are covered.
- `implementations/cpp/` — focused comparisons where C++ clarifies systems concepts.
- `labs/` and `exercises/` — practical work linked to chapters.
- `research/` — source studies and research notes, organized by source type.
- `references/` — curated reading path and structured research catalog.
- `diagrams/` — editable Mermaid diagrams used by the curriculum.
- `AI_CONTEXT.md`, `PROJECT_STATE.md`, `SESSION_HANDOFF.md`, `TODO.md`, `WORK_LOG.md`, `DECISIONS.md`, `CHAPTER_STATUS.md`, `RESEARCH_INDEX.md`, `QUALITY_REPORT.md` — persistent project memory and operational records.
- `ROADMAP.md`, `PROGRESS.md`, `RESEARCH.md`, `SUMMARY.md` — authoritative plan, high-level status, research method, and project overview.

The first textbook chapter is in `docs/volume-01-foundations/`. The remaining structure will be added as substantive chapters are approved; empty chapter placeholders are not used.

## Start here

1. Read [AI_CONTEXT.md](AI_CONTEXT.md) and [SESSION_HANDOFF.md](SESSION_HANDOFF.md) to resume.
2. Read [PROJECT_STATE.md](PROJECT_STATE.md), [TODO.md](TODO.md), and [CHAPTER_STATUS.md](CHAPTER_STATUS.md) for actual state and work queue.
3. Read [ROADMAP.md](ROADMAP.md) for planned scope and [RESEARCH_INDEX.md](RESEARCH_INDEX.md) for source records.

## Scope and quality

This is a learning and research repository, not an attempt to create a production database. Examples will state their simplifications; implementation-specific behavior will be distinguished from general theory. No chapter, implementation, test suite, or source verification is considered complete merely because it appears in the plan.

The textbook-scale foundation has been revised to match the clarified vision. Work now begins progressively at the absolute fundamentals; see [PROGRESS.md](PROGRESS.md).
