# Architecture and Engineering Decisions

Significant decisions are recorded here. Superseded decisions remain in the log with a new decision explaining the change.

## ADR-001 — Use a multi-volume textbook structure

- **Date:** 2026-11-11
- **Status:** Accepted
- **Context/problem:** A single flat tutorial sequence would not support the requested textbook depth or extensive subject complexity.
- **Options:** Flat topic directories; single monolithic book; volumes/parts/chapters.
- **Decision:** Use 13 planned volumes, subdivided into parts and stable-ID chapters; allow additional chapters when the subject needs them.
- **Reasoning:** Separates prerequisite paths and gives a readable book structure while retaining Markdown/Git navigation.
- **Trade-offs/consequences:** Requires volume indexes and cross-reference upkeep. The 130–175 estimate is provisional; only explicitly enumerated chapter IDs count as planned chapters.

## ADR-002 — Stable volume-prefixed chapter IDs

- **Date:** 2026-11-11
- **Status:** Accepted
- **Context/problem:** Filenames and chapter ordering can evolve; cross-volume citations need stable references.
- **Options:** Number chapters globally; number within each volume; rely on filenames only.
- **Decision:** Use `Vnn-Cnn` IDs and descriptive filenames.
- **Reasoning:** IDs are concise and stable across file renames; volume indexes map them to current paths.
- **Consequences:** A registry must track every chapter ID/title/status and prevent reuse.

## ADR-003 — Primary-source, claim-specific reference policy

- **Date:** 2026-11-11
- **Status:** Accepted
- **Context/problem:** The supplied research synthesis has vague or malformed citations and mixes sources of differing authority.
- **Decision:** Prefer original papers, official documentation, university courses, source code/design documents, and established textbooks according to the claim being supported. Record exact link, metadata, authority, verification status/date, and chapter mapping.
- **Trade-offs:** Verification takes time and sometimes a product/version must be pinned. A broad topic overview cannot substitute for claim-level evidence.

## ADR-004 — Keep theory and implementation tracks paired but independently gated

- **Date:** 2026-11-11
- **Status:** Accepted
- **Context/problem:** Code-first treatment can obscure assumptions and correctness requirements; theory without experiments can remain abstract.
- **Decision:** Every major concept has theory and engineering learning outcomes, but code is only introduced when relevant prerequisites and correctness scope are established.
- **Consequences:** Some conceptual chapters have no Rust code; implementation milestones refer back to theory chapters.

## ADR-005 — Rust Edition 2024 as primary implementation language

- **Date:** 2026-11-11
- **Status:** Accepted
- **Decision:** Rust Edition 2024 for the staged engine-learning work; C++ only for comparisons that materially teach systems concepts.
- **Consequence:** Do not create a Cargo workspace until the first implementation milestone is designed and approved by its own acceptance criteria. Rust memory safety is not equivalent to database correctness.

## ADR-006 — Repository and project name distinction

- **Date:** 2026-11-11
- **Status:** Accepted
- **Context:** The Master Execution Prompt names the repository SGDatabase and project LearnDatabase; existing README called the whole project LearnDatabase.
- **Decision:** Present the repository as “SGDatabase — LearnDatabase Textbook”; use LearnDatabase for the educational project identity.
- **Consequence:** Preserve this naming unless owner revises it.

## ADR-007 — Record actual chapterized scope separately from broad estimate

- **Date:** 2026-11-11
- **Status:** Accepted
- **Context:** The roadmap has a 130–175 chapter estimate but only Volume I titles are currently enumerated.
- **Decision:** `PROJECT_STATE.md` and `CHAPTER_STATUS.md` count only stable-ID chapter entries actually in the roadmap/registry. Treat 130–175 as an uncommitted complexity estimate.
- **Consequence:** Do not report exact total planned chapters until the remaining volume outlines are decomposed.

## ADR-008 — Chapter acceptance requires recorded evidence

- **Date:** 2026-11-14
- **Status:** Accepted for Phase B
- **Context/problem:** Existing COMPLETED labels were not consistently backed by solutions/grading guidance and chapter-level acceptance evidence; C05's prior QA record explicitly said draft review only.
- **Decision:** Apply a measurable acceptance policy before marking or retaining COMPLETED. Existing C01–C05 are set to NEEDS REVIEW pending policy review; C06 remains DRAFT. Status transitions require evidence in the chapter and `QUALITY_REPORT.md`.
- **Consequences:** Historical status claims remain in append-only logs, while current status is conservative. Chapter count of accepted material is zero until the review gates are met.

## ADR-009 — Status sources of truth

- **Date:** 2026-11-14
- **Status:** Accepted
- **Decision:** `ROADMAP.md` owns planned scope and milestone baseline; `CHAPTER_STATUS.md` owns individual chapter status; `PROGRESS.md` owns phase/workstream status; `PROJECT_STATE.md` contains derived counts and open issues; `QUALITY_REPORT.md` records checks; `TODO.md` owns active tasks; `SESSION_HANDOFF.md` owns immediate resume instructions. `SUMMARY.md` is descriptive and must not become a competing status ledger.
- **Consequences:** Repeated counts are derived and reconciled. Historical WORK_LOG/QUALITY_REPORT entries are not rewritten; corrections are recorded in new entries.

## ADR-010 — Preserve 22 implementation milestones as the baseline

- **Date:** 2026-11-14
- **Status:** Accepted
- **Context/problem:** ROADMAP.md stated 19 milestone chapters while defining 22 implementation milestones.
- **Decision:** Keep the 22 milestone baseline. Treat them as learning units/specifications rather than claiming one finalized chapter per milestone; any later chapter split requires curriculum review and stable-ID assignment.
- **Consequences:** Volume XIII scope wording is corrected; detailed specifications remain in `IMPLEMENTATION_SPECIFICATIONS.md`.

## ADR-011 — Define IN PROGRESS as an active chapter status

- **Date:** 2026-11-14
- **Status:** Accepted
- **Context/problem:** The chapter registry allowed IN PROGRESS, but the educational acceptance policy did not define the status or its transition rules.
- **Decision:** IN PROGRESS means active authoring or substantive revision, not ready for acceptance review. Move to DRAFT when a coherent reviewable draft exists, or NEEDS REVIEW when focused review identifies evidence gaps. Keep per-chapter status authoritative in `CHAPTER_STATUS.md`.
- **Consequences:** The acceptance standard, progress definitions, and curriculum status key now share this meaning. C02 used IN PROGRESS during substantive revision, then moved to COMPLETED after QR-009 evidence; C01 remains NEEDS REVIEW due its open diagram check.
