# Chapter Status Registry

This registry counts chapters explicitly assigned stable IDs in `ROADMAP.md`. Unnumbered topic groupings and the broad scope estimate do not count until decomposed. Update all component fields and date when a chapter changes.

Statuses: NOT STARTED, IN PROGRESS, DRAFT, NEEDS RESEARCH, NEEDS REVIEW, COMPLETED, BLOCKED.

| ID | Title | Volume / part | Prerequisites | Theory | Engineering | Research | Diagrams | Exercises | References | Status | Updated | Notes |
|---|---|---|---|---|---|---|---|---|---|---|---|---|
| V01-C01 | What Is Data? From Observations to Stored Representations | I / Part I | None | Complete | N/A: conceptual foundations experiment | Complete for scope | Complete | Complete | Verified | COMPLETED | 2026-11-11 | Acceptance record in chapter. |
| V01-C02 | Meaning, Models, and Information | I / Part I | C01 | Complete | Experiment complete; implementation N/A | Verified | Complete | Complete | Verified | COMPLETED | 2026-11-11 | Semantic/contextual meaning distinguished from Shannon measures. |
| V01-C03 | Information Theory: Uncertainty, Entropy, and Coding | I / Part I | C02; probability concepts introduced | Complete | Coding experiment/walkthrough complete | Verified | Complete | Complete | Verified | COMPLETED | 2026-11-11 | Entropy/coding examples independently recalculated; source limits stated. |
| V01-C04 | Bits, Bytes, Types, and Encodings | I / Part I | C01–C03 | Complete | Rust byte-conversion example/lab complete | Verified | Complete | Complete | Verified | COMPLETED | 2026-11-11 | Representation examples and local links checked; IEEE normative text limitation noted. |
| V01-C05 | Files, Memory, and Persistent Storage | I / Part I | C04 | Complete | Rust file synchronization example/experiment complete | Verified | Complete | Complete | Verified | COMPLETED | 2026-11-11 | Linux/POSIX behaviors scoped; Rust API limits described. |
| V01-C06 | Programs, Processes, and Client–Server Basics | I / Part I | C05 | Draft | Protocol design experiment and Rust API example drafted | Verified | Complete | Complete | Verified | DRAFT | 2026-11-11 | Linux APIs and TCP semantics scoped; requires link review. |
| V01-C07 | What Is a Database? | I / Part II | C01–C06 | Not started | Not started | Not started | Not started | Not started | Not started | NOT STARTED | — | Database meaning and boundaries. |
| V01-C08 | What Is a DBMS and Why Use One? | I / Part II | C07 | Not started | Not started | Not started | Not started | Not started | Not started | NOT STARTED | — | Responsibilities and guarantees vs assumptions. |
| V01-C09 | Files and Application-Managed Data vs DBMS | I / Part II | C05–C08 | Not started | Not started | Not started | Not started | Not started | Not started | NOT STARTED | — | Trade-offs, avoid false dichotomy. |
| V01-C10 | Database Workloads and System Families | I / Part II | C07–C08 | Not started | Not started | Not started | Not started | Not started | Not started | NOT STARTED | — | Workloads and broad system categories. |
| V01-C11 | A First Map of a Database System | I / Part II | C06–C10 | Not started | Not started | Not started | Not started | Not started | Not started | NOT STARTED | — | Preview only; detailed architecture in Volume IV. |

## Count snapshot

As of 2026-11-11: 11 chapter entries; 5 COMPLETED; 1 DRAFT; 5 NOT STARTED; 0 IN PROGRESS, NEEDS RESEARCH, NEEDS REVIEW, or BLOCKED. Six chapter files are present for five completed entries and one draft. This registry does not enumerate every eventual chapter because the remaining volumes have topic outlines rather than stable IDs.
