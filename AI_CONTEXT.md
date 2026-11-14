# AI Context — Read First

SGDatabase is the repository for **LearnDatabase**, owned by **sgPhilia**: a long-term, university-level database engineering textbook and practical knowledge base. The goal is comprehensive independent learning from computing/data foundations through database internals, distributed systems, real-world implementations, and a staged Rust Edition 2024 build-your-own-database track. C++ is secondary and selective. Existing documentation language is English; preserve it.

## Authoritative state

- Planned scope and prerequisites: [ROADMAP.md](ROADMAP.md) (authoritative).
- Topic-by-topic dependencies: `CURRICULUM_DEPENDENCY_MATRIX.md` (derived map; it does not assign new chapter IDs).
- Research claims-to-curriculum links: `RESEARCH_MAP.md`; bibliographic records remain in [RESEARCH_INDEX.md](RESEARCH_INDEX.md).
- Chapter acceptance and exercise/lab standards: `EDUCATIONAL_STANDARDS.md`.
- Per-milestone engineering specs: `IMPLEMENTATION_SPECIFICATIONS.md`.
- High-level status: [PROGRESS.md](PROGRESS.md) (authoritative).
- Exact chapter registry and component status: [CHAPTER_STATUS.md](CHAPTER_STATUS.md).
- Detailed actual counts, priorities, issues: [PROJECT_STATE.md](PROJECT_STATE.md).
- Immediate task queue: [TODO.md](TODO.md).
- Exact next action: [SESSION_HANDOFF.md](SESSION_HANDOFF.md).
- Research evidence and policy: [RESEARCH.md](RESEARCH.md), [RESEARCH_INDEX.md](RESEARCH_INDEX.md).
- Decisions/history/checks: [DECISIONS.md](DECISIONS.md), [WORK_LOG.md](WORK_LOG.md), [QUALITY_REPORT.md](QUALITY_REPORT.md).

## Architecture and conventions

Textbook is organized into numbered volumes, parts, and chapters. Stable chapter IDs use `Vnn-Cnn`; filenames include the ID and a descriptive slug. Content advances by prerequisites. A complex subject receives multiple chapters. Each chapter develops theory and engineering tracks, with claim-level references and system/version labels. No empty placeholder chapters. Mermaid is the editable diagram format. No code/workspace is created before its theory and correctness scope are specified. Rust safety must not be represented as a guarantee of database correctness, durability, isolation, or crash consistency.

## Workflow and quality

Inspect `git status` and relevant files before editing. Research primary sources, record metadata, distinguish theory from implementation and inference, draft one logically complete unit, check links/terms/citations/cross-references, and update all affected state files plus `WORK_LOG.md` and `SESSION_HANDOFF.md`. Continue autonomously through ordinary roadmap tasks. Do not push or perform destructive changes. Do not mark a chapter complete unless the chapter standard in `ROADMAP.md` and its registry checks are satisfied.

## Current project snapshot

At Phase B start on 2026-11-14, `main` tracked `origin/main` at `1aaefeb` and the tree was clean; Phase B and Phase C edits remain uncommitted. Phase B structural deliverables are complete; Phase C textbook development is authorized and IN PROGRESS. V01-C02–C04 passed their acceptance gates (QR-009–QR-011). V01-C01 and C05 remain NEEDS REVIEW pending Mermaid parser validation. Current task: validate the two C05 diagrams with a parser, then proceed to C06 only if C05 passes. No implementation crate exists. See `SESSION_HANDOFF.md` for exact resumption instructions.
