# Project State Snapshot

Last reconciled: 2026-11-14 (Asia/Bangkok).

## Derived counts

`ROADMAP.md` owns planned scope; `CHAPTER_STATUS.md` owns chapter statuses. Counts are computed from those sources and the checkout.

| Measure | Current value | Basis |
|---|---:|---|
| Planned volumes | 13 | Scope headings in `ROADMAP.md` |
| Parts explicitly defined | 2 | Volume I Part I and Part II |
| Stable-ID chapter entries | 11 | Volume I only |
| Chapter files present | 6 | V01-C01 through V01-C06; C01–C05 solution guides are supporting files, not chapters |
| COMPLETED | 3 | V01-C02 (QR-009), V01-C03 (QR-010), V01-C04 (QR-011) |
| IN PROGRESS | 0 | No chapter is currently being authored |
| NEEDS REVIEW | 2 | V01-C01 and V01-C05; Mermaid parser validation is open for both |
| DRAFT | 1 | V01-C06 |
| NOT STARTED | 5 | V01-C07 through V01-C11 |
| Implementation milestones | 22 | Existing numbered roadmap list |
| Milestone specifications present | 22 | `IMPLEMENTATION_SPECIFICATIONS.md`; planning specs only, all implementation milestones remain NOT STARTED |
| Research records | 34 | R-001–R-034 in `RESEARCH_INDEX.md`; R-032 retains NEEDS REVIEW/access limitations |
| Implementation code | 0 | No source tree, Cargo manifest, or tests |
| Standalone lab/exercise assets | 0 | Exercises are embedded in chapters only |

The 130–175 figure is a long-range estimate, not a chapter registry total. Volumes II–XII have topic outlines without stable chapter IDs; Volume XIII maps to milestone learning units and does not yet have chapter IDs.

## Phase and task

- Phase A — repository/research audit: COMPLETED.
- Phase B — structural integrity and curriculum architecture: COMPLETED; validation recorded in `QUALITY_REPORT.md` QR-007.
- Phase C — autonomous textbook development: IN PROGRESS; authorized by the user on 2026-11-14.
- Current task: C-05 — complete V01-C05 acceptance by validating its two Mermaid diagrams with an actual parser. C01 has a separate open parser check; C02–C04 passed their evidence gates (QR-009–QR-011). See `SESSION_HANDOFF.md`.

## Git snapshot

At the initial Phase B inspection, `main` tracked `origin/main`, HEAD was `1aaefeb` (`Update project`), and the working tree was clean. This is an inspection baseline, not a permanent commit-count claim. Current task edits are uncommitted unless later recorded otherwise.

## Open issues

1. Chapter statuses were reconciled conservatively. Earlier “COMPLETED” claims remain in historical records; current registry requires new acceptance evidence. C01 and C05 have substantive reviewed content but each still has an open Mermaid parser check. C02–C04 passed their current acceptance gates as documented in QR-009–QR-011.
2. Research-to-claim mapping is a curriculum planning map, not chapter-level citation evidence; R-032 needs primary full-text access.
3. Proposed topic gaps (math/computing prerequisites and implementation components) require review before new chapter or milestone IDs are assigned.
4. The exact total chapter count remains unknown until topic outlines are decomposed and approved.
