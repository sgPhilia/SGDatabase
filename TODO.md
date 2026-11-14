# Active Task List

`ROADMAP.md` is authoritative for planned scope; `CHAPTER_STATUS.md` is authoritative for chapter-level status. Completed work moves to the append-only `WORK_LOG.md`; QA evidence is appended to `QUALITY_REPORT.md`.

## Critical — Phase C

- **C-01 — Audit and improve V01-C01, What Is Data?** Content review and revisions complete; chapter status remains NEEDS REVIEW. Remaining acceptance check: validate the Mermaid diagram with a parser and record evidence. Do not mark complete before that check.
- **C-02 — Audit and improve V01-C02, Meaning, Models, and Information.** Status: COMPLETED; acceptance evidence in QR-009. Full-paper Shannon source checked; Codd access limitation disclosed; exercises and separate solutions added.
- **C-03 — Audit and improve V01-C03, Information Theory.** Status: COMPLETED; evidence in QR-010 and chapter acceptance record. Dependencies: C02 complete.
- **C-04 — Audit and improve V01-C04, Bits, Bytes, Types, and Encodings.** Status: COMPLETED; evidence in QR-011 and chapter acceptance record. IEEE normative-text access limit remains disclosed; R-033 and Rust primary docs support scoped claims.
- **C-05 — Audit and improve V01-C05, Files, Memory, and Persistent Storage.** Content and evidence work are complete; status: NEEDS REVIEW. Remaining acceptance check: parse both Mermaid diagrams with a Mermaid parser and record the result. No parser/package is present in the current environment. Dependencies: C-04 complete.
- **C-06 — Audit and improve V01-C06, Programs, Processes, and Client–Server Basics.** Status: NOT STARTED. Dependencies: C-05 acceptance. Acceptance: verify process/network examples, framing, timeout ambiguity, API behavior, exercises and references.
- **C-07 — Review Volume I closure and prerequisite gaps.** Status: NOT STARTED. Dependencies: C-01–C-06. Acceptance: resolve remaining acceptance issues; update Volume I navigation, glossary/index, and state before continuing to C07.

## Ongoing maintenance

- Keep chapter IDs/statuses and evidence synchronized across registry, chapter metadata, navigation, and `QUALITY_REPORT.md` after each chapter task.

## Deferred

- Database implementation and Cargo workspace: wait until relevant theory chapters and milestone gates are ready.
- Proposed prerequisite topics and component boundaries: review proposals in `CURRICULUM_DEPENDENCY_MATRIX.md` and `IMPLEMENTATION_SPECIFICATIONS.md` before assigning new chapter or milestone IDs.
- Full vendor-by-vendor system studies: wait until relevant theory is covered and sources are verified.
- New Volume II–XIII chapter IDs: propose after matrix review; do not invent IDs without approval.
