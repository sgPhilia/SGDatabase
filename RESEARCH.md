# Research Assessment and Method

**Status:** Initial assessment complete; source verification and catalog construction remain IN PROGRESS.

## Materials inspected

The supplied research material consists of one eight-page PDF titled *LearnDatabase — Comprehensive Database Engineering Research Plan* (the PDF itself is headed “AI Passport Deep Research”) and the project Master Prompt in `Text File.txt`. The PDF includes an executive summary, research approach, findings labeled Deliverables A–J, analysis, limitations, and a 27-entry source listing. No papers, books, course files, database documentation snapshots, datasets, or source-code checkouts were present. The working directory contains no existing curriculum tree, implementation, roadmap, progress log, or usable Git repository metadata.

The PDF has been read as a full text extraction across all eight pages. Its research findings are treated as a preliminary, AI-generated synthesis rather than independently established evidence. In particular, the References section frequently records only a domain name and generic “Reading” label, with malformed Markdown-like URL text and no complete title/author/year/version. This prevents reliable claim-to-source auditing for most assertions.

## What the report provides

- A broad domain map from computing foundations and relational theory through storage, indexes, query execution, transactions, recovery, and distributed systems.
- The right high-level teaching principle for this project: pair theory with practical implementation.
- A useful initial system set: PostgreSQL, SQLite, MySQL/InnoDB, DuckDB, RocksDB, LevelDB, Redis, and FoundationDB.
- Early research questions around Rust design/performance, vectorized execution versus JIT, and isolation-level terminology across vendors.
- Project recommendations for Mermaid diagrams, milestone-specific correctness work, and staged Rust experiments.
- Acknowledgment that version-sensitive system behavior, terminology ambiguity, and simplification boundaries need careful handling.

These points inform the roadmap, but their presence in the report does not independently verify the claims.

## Gaps, contradictions, and quality risks

1. **Weak traceability.** Most source entries do not provide a usable direct URL or bibliographic record. Some important claims cite Wikipedia, generic home domains, DeepWiki, or unrelated secondary sources where an original paper, official manual, or source tree is needed. At present, source-to-claim links cannot be audited.
2. **The report calls its approach primary-source-led without demonstrating it.** The reference list contains few complete original papers or course artifacts and includes general or low-authority material. Its authority claim is therefore unsupported by its catalog.
3. **Curriculum ordering is compressed and inconsistent.** One section pairs SQL with fundamentals; another starts storage labs in Levels 3–4; the “comprehensive curriculum” omits several important prompt topics, while the report's recommendation jumps directly to storage/indexing deep dives. The proposed roadmap adds computing prerequisites and relational modeling before internals, then begins storage after a minimum conceptual foundation.
4. **Implementation roadmap omits substantial dependencies.** It moves from an in-memory KV store to pages and buffer management but does not clearly separate persistent KV, record representation, schemas, transaction semantics, recovery, and concurrency. The proposed roadmap stages these explicitly.
5. **Rust safety is overstated.** Rust can prevent classes of memory-safety and data-race bugs in safe code; it does not itself provide transaction isolation, crash consistency, durability, or database correctness. The Master Prompt states this correctly, so future content must retain that distinction.
6. **Specific system claims are underspecified or overgeneralized.** “Strong ACID via MVCC,” “SQLite uses a B-tree storage model and journal-based recovery,” “PostgreSQL process-per-client,” and the report's description of DuckDB are presented without version, workload, mode, subsystem scope, or precise source. SQLite supports rollback-journal and WAL modes; PostgreSQL's current docs describe MVCC plus explicit locks and SSI for serializable isolation; DuckDB documents vectorized execution. These distinctions matter and should be stated narrowly.
7. **An internal contradiction appears in the DuckDB claim.** The report says DuckDB relies “exclusively” on vectorized execution while its source description is broad and unqualified. The official documentation describes its execution model in more concrete terms; future material should avoid an absolute claim unless the exact version and scope are documented.
8. **Terms and standards need correction/qualification.** “ANSI SQL compliance” is too vague for a project teaching a selected SQL subset; name the supported syntax and semantics instead. “CAP theorem” and “PACELC” should be taught with precise assumptions and original/authoritative references, not as simplistic database labels.
9. **Citation defects.** Several references have mismatched or malformed links; the report cites the Raft Wikipedia entry for a Rust implementation claim and gives incomplete details for Codd, Hellerstein et al., Abadi, and GetReal. No exact system source paths are verified.
10. **Freshness is unknown.** The PDF has no clear research cutoff date, database release versions, or per-source verification dates. Product and implementation facts must be rechecked against current official documentation when used.
11. **Breadth exceeds immediate foundation needs.** It mentions many advanced systems/topics without a curated path, prerequisites, or acceptance criteria. The planned catalog will distinguish core reading from the extended collection.

## Limited external source spot-checks

These checks were performed during the initial assessment to test a few representative system claims. They are not a complete validation of the report.

- PostgreSQL's current concurrency chapter describes MVCC and separately covers transaction isolation, explicit locking, deadlocks, and serializable behavior. The learning material should not imply that “MVCC” alone explains all locking or isolation semantics. [PostgreSQL 18 Concurrency Control](https://www.postgresql.org/docs/current/mvcc.html)
- DuckDB's official internals documentation describes a push-based vectorized operator model and vector chunks. That supports a vectorization claim, but not the report's blanket “exclusively” wording. [DuckDB Internals Overview](https://duckdb.org/docs/current/internals/overview) and [Execution Format](https://duckdb.org/internals/vector)
- SQLite's official locking documentation describes rollback-journal behavior and notes that WAL mode has a separate mechanism. A generic statement that SQLite uses journal-based recovery must identify the journal mode. [SQLite File Locking and Concurrency](https://www.sqlite.org/lockingv3.html)

No attempt was made to fully verify each system, paper, benchmark, or performance claim in the PDF. This remains open work.

### Follow-up source verification — foundations (2026-11-11)

For Volume I, original records and direct sources were checked for Codd's relational model paper, Hellerstein/Stonebraker/Hamilton's database architecture survey, Shannon's 1948 communication theory paper, and Huffman's 1952 minimum-redundancy code paper. Official product references were checked for PostgreSQL and SQLite. Full bibliographic metadata and links are in [RESEARCH_INDEX.md](RESEARCH_INDEX.md). This is a small, chapter-scoped update and does not change the overall limitation that a complete bibliography remains unbuilt.

For V01-C04, official references were checked for IEEE 754-2019's scope, UTF-8 in IETF RFC 3629, Unicode code-point/encoding distinctions and UAX #15, and Rust's type/layout/`char`/`str` documentation. The IEEE page confirms the active standard and its scope, but the normative full text was not accessed; the chapter limits claims accordingly. See source records R-012–R-016.

For V01-C05, Linux man-pages were checked for partial-write and `fsync`/directory-sync behavior, Linux kernel documentation for the page cache, and Rust standard-library documentation for file synchronization APIs. These are explicitly presented as Linux/POSIX or Rust API behavior, not universal filesystem guarantees; see R-017–R-020.

For V01-C06, Linux `fork(2)` and `accept(2)` manuals, the current IETF TCP standard RFC 9293, and Rust's `TcpListener` documentation were checked. The chapter distinguishes TCP transport from application framing and database transaction outcomes; see R-021–R-024.

## Research authority and catalog policy

Use this source priority for claims: original papers/seminal publications; official system documentation; university courses; original source code/design documents; established textbooks; reputable engineering articles; tutorials. A source's authority is claim-specific: official product docs establish documented behavior for a product/version, while an original paper may establish the historical algorithm or theoretical contribution.

Each catalog record should contain:

| Field | Meaning |
|---|---|
| Title; author/organization; year | Verifiable bibliographic identity; use “unknown” if not verified |
| Resource type | Paper, book, course, official docs, source repository/design doc, article, tutorial |
| Topic and subtopic | Curriculum concepts supported |
| Difficulty and prerequisites | Learner readiness |
| Authority tier | 1 seminal/classic; 2 university; 3 official docs/source; 4 reputable engineering resource |
| Importance | Essential, Recommended, Optional, Historical, or Advanced |
| Chapter mapping | Planned or published repository destinations |
| Direct URL | Exact stable source/page, not just a domain |
| Verification status | UNVERIFIED, VERIFIED, NEEDS REVIEW, or BROKEN |
| Last verified | Date checked, with version context for changing documentation |

Maintain two views: a short, dependency-ordered Core Reading Path and a topic-filterable Extended Research Catalog. Do not inflate the catalog with low-quality or duplicate links. Preserve source provenance and distinguish claims made by the source from the repository's interpretation.

## Initial research task queue

1. Verify complete bibliographic data and direct URLs for the core readings: Codd's relational model paper; *Architecture of a Database System*; a recognized database systems textbook; a reputable systems course; and primary materials for B+ trees, LSM trees, ARIES/WAL, Raft, and column stores.
2. Add official documentation entry points and version policy for each target system.
3. Build a topic-to-source matrix before drafting advanced chapters.
4. For every system study, verify behavior and source paths against a pinned release or current official documentation and record the date.
5. Revisit the report's open questions only after sources define the exact comparison: Rust safe/unsafe design and measured workload; vectorized execution/JIT by system and version; vendor isolation guarantees by anomaly/phenomenon.

## Research limitations

The initial assessment relies on one supplied synthesis report and a small official-documentation spot-check. There is not yet enough verified evidence to call the provided source set comprehensive or authoritative. No findings in this document should be read as an exhaustive literature review.
