# LearnDatabase

LearnDatabase is a research-backed, university-level textbook project for understanding database systems and the engineering choices behind them. It is intended to read as a collection of technical books, not a set of short tutorials. It pairs detailed theory with practical experiments, with Rust (Edition 2024) as the primary implementation language and C++ as an occasional comparison.

## Current state

The initial repository assessment is complete, and the textbook-scale structure and standards are now established. Volume I begins with a chapter on what data and database systems are. See [PROGRESS.md](PROGRESS.md) for current status and [ROADMAP.md](ROADMAP.md) for the proposed sequence.

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
- `ROADMAP.md`, `PROGRESS.md`, `RESEARCH.md`, `SUMMARY.md` — project planning, status, research method, and concise project knowledge map.

The first textbook chapter is in `docs/volume-01-foundations/`. The remaining structure will be added as substantive chapters are approved; empty chapter placeholders are not used.

## Start here

1. Read [ROADMAP.md](ROADMAP.md) for the proposed curriculum, technical dependencies, milestones, and initial task breakdown.
2. Read [RESEARCH.md](RESEARCH.md) for the assessment of the supplied report, source limitations, and verification policy.
3. Read [PROGRESS.md](PROGRESS.md) for explicit current statuses and open decisions.

## Scope and quality

This is a learning and research repository, not an attempt to create a production database. Examples will state their simplifications; implementation-specific behavior will be distinguished from general theory. No chapter, implementation, test suite, or source verification is considered complete merely because it appears in the plan.

The textbook-scale foundation has been revised to match the clarified vision. Work now begins progressively at the absolute fundamentals; see [PROGRESS.md](PROGRESS.md).
