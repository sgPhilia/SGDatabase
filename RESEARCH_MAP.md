# Research-to-Curriculum Map

**Catalog baseline:** R-001–R-034, as recorded in `RESEARCH_INDEX.md` on 2026-11-14. This map establishes planning relevance, not per-claim proof in future chapters.

`RESEARCH_INDEX.md` is authoritative for bibliographic records and verification status. This file maps sources to the scope they can support. A source supports only its documented claim scope; it does not establish product behavior outside the cited version, nor make an untested engineering inference a fact.

## Source class key

- **Primary research:** original paper/report presenting theory, algorithm, or system design.
- **Academic book:** textbook or scholarly monograph; use the specific chapter/edition where claims are drawn.
- **University course:** syllabus, lecture, assignment, or course text; useful for curriculum coverage and pedagogy, not automatically primary evidence for technical claims.
- **Standard/specification:** normative protocol, language, encoding, or interface document.
- **Official documentation/source:** documents or code published by the system/language maintainers; product-specific and version-sensitive.
- **Secondary source:** survey, catalog, engineering article, or synthesis; context only unless an original source is unavailable and limits are explicit.

## Verified source-to-claim map

| Source ID | Class and verified access state | Claim / scope it can support | Curriculum topics | Chapter mapping | Implementation components |
|---|---|---|---|---|---|
| R-001 | Primary research; IBM bibliographic record checked | Codd's relational model and data-independence motivation; historical scope only | Relational theory, data models | V01-C01; Volume II | M9, M13–14 |
| R-002 | Academic survey; publisher record/abstract accessed; full text paywalled | DBMS component roles and architecture alternatives as a survey, not a universal diagram | Introductory DBMS map, architecture, query lifecycle | V01-C01, V01-C11; Volume IV | M9–21 |
| R-003 | Primary research; 55-page full paper accessible and metadata checked 2026-11-14 | Shannon's communication framework, source uncertainty, and coding; semantic aspects explicitly set aside in the engineering formulation | Information theory, entropy | V01-C02–C04 | M3, M5 |
| R-004 | Official product page; checked | PostgreSQL product identity and officially stated characteristics only | Real-system orientation | V01-C01; Volume XII PostgreSQL | Context only; map to M9+ after specific docs |
| R-005 | Official PostgreSQL 18 documentation; page/version checked 2026-11-14 | Server process and database-cluster terminology scoped to PostgreSQL 18 | Client/server, architecture | V01-C01, Volume IV, XII PostgreSQL | M21 |
| R-006 | Official SQLite guidance; accessed 2026-11-14 | SQLite's documented appropriate-use guidance, not universal embedded DB rules | Workloads and deployment | V01-C01, V01-C10; Volume XII SQLite | M1–9 |
| R-007 | Official PostgreSQL docs; version context recorded | PostgreSQL's documented MVCC/concurrency behavior; do not generalize to all engines | MVCC, isolation | Volume VIII, XII PostgreSQL | M18–19 |
| R-008 | Official DuckDB docs; current page checked | Documented DuckDB architecture overview and execution descriptions | Query processing | Volume VII, XII DuckDB | M13, M20 |
| R-009 | Official DuckDB docs; current page checked | Vector/data-chunk execution representation described by DuckDB | Vectorized execution | Volume VII, XI–XII DuckDB | M13 |
| R-010 | Official SQLite docs; mode-specific page checked | SQLite rollback-journal locking behavior; WAL mode requires separate source | Recovery and concurrency | Volume IX, XII SQLite | M15–18 |
| R-011 | Primary research; 1952 full-paper copy hosted by Stanford accessed; DOI endpoint unavailable | Huffman's binary construction, optimality argument, and stated extension to D-ary codes within the paper's model | Coding/compression | V01-C03; Volume XI | M3, M5 |
| R-012 | Standard metadata/scope page checked; normative text not accessed | IEEE 754 scope and metadata only; detailed normative claims remain bounded | Floating point | V01-C04; Volume V | M3, M5 |
| R-013 | IETF standard; full RFC accessible | UTF-8 encoding rules within RFC scope | Text encoding | V01-C04; Volume V | M3, M5, M10 |
| R-014 | Official Unicode standard; chapter page checked | Unicode code points/encoding forms within the cited version | Text representation | V01-C04; Volume III | M3, M10 |
| R-015 | Normative Unicode annex; revision recorded | Unicode normalization forms and versioned rules | Equality, normalization | V01-C04; Volume III, VI | M3, M10–12 |
| R-016 | Official Rust reference/library docs; pages checked | Rust layout and string API behavior; not portable file-format guarantees | Rust representation | V01-C04; Volume XIII | M3, M5, M10–12 |
| R-017 | Linux manual; current page checked | Linux `write(2)` behavior, partial writes and reported errors | File I/O | V01-C05; Volume V, IX | M2, M4, M16 |
| R-018 | Linux manual; current page checked | Linux `fsync(2)` behavior and documented caveats | Persistence boundaries | V01-C05; Volume IX | M2, M16–17 |
| R-019 | Official Linux kernel documentation checked | Linux page-cache concepts; not all filesystem implementations | Memory and I/O | V01-C05; Volume V | M2, M4, M6 |
| R-020 | Official Rust standard library docs checked | Rust `File`, `Read`, and `Write` API contracts | File I/O | V01-C05; Volume XIII | M2, M4, M16 |
| R-021 | Linux manual checked | Linux `fork(2)` semantics and inherited descriptor details | Processes | V01-C06; Volume IV | M21 |
| R-022 | IETF Internet Standard; full RFC accessible | TCP connection byte-stream semantics, not application framing or transaction commit | Networking | V01-C06; Volume IV, X | M21–22 |
| R-023 | Linux manual checked | Linux `accept(2)` behavior | Socket server lifecycle | V01-C06; Volume IV | M21 |
| R-024 | Official Rust standard library docs checked | Rust `TcpListener` API contract | Rust networking | V01-C06; Volume XIII | M21 |
| R-025 | University course; current Fall 2026 syllabus directly accessed | Course prerequisites, learning outcomes, topics, and project-oriented teaching structure; not primary research claims | Curriculum coverage across V02–XII | Volumes II–XII | M1–22 as comparative curriculum map |
| R-026 | Academic textbook; official author/publisher resource page checked | Textbook coverage and bibliographic identity; cite the actual chapter for substantive claims | Introductory database theory and systems | Volumes I–X | M1–22 |
| R-027 | Primary research; ACM metadata plus complete course-hosted paper checked | System R access-path selection for simple and complex queries in the paper's historical system | Query optimization | Volume VII | M14, M20 |
| R-028 | Primary research; SIGMOD Record page and full paper PDF accessed | Isolation phenomena, Snapshot Isolation definition, and limits of phenomenon-only characterization in the paper's model | Transactions, isolation | Volume VIII | M18–19 |
| R-029 | Primary research; full paper copy and journal metadata cross-checked | Original LSM-tree architecture and its stated write/read/I/O trade-offs under the paper's disk model | Storage engines, indexes | Volumes V–VI | M2, M7, M16 |
| R-030 | Primary research; author-hosted full paper and USENIX venue page checked | Raft replicated-log design, leader election, replication, and safety claims within paper assumptions | Consensus and replication | Volume X | M22 |
| R-031 | Primary research; full VLDB archive paper and metadata record checked | C-Store column-oriented design and paper-specific architecture/performance claims | Column stores, analytical systems | Volumes V, VII, XI–XII | M3, M5, M13, M20 |
| R-032 | Primary research; metadata verified, publisher access failed | ARIES bibliographic identity only until primary full-text access is confirmed | Recovery, WAL | Volumes VIII–IX | M16–18; source claims remain NEEDS REVIEW |
| R-033 | Official Oracle platform specification; §2.3.2 accessed 2026-11-14 | JVM `float`/`double` associated with binary32/binary64; format parameter values in JVM scope | Binary floating-point fields | V01-C04 | No JVM implementation planned; supports representation comparison only |
| R-034 | Linux man-page; current read(2) text accessed 2026-11-14 | Linux/POSIX short-read, EOF, and error behavior | File reads and byte-stream parsing | V01-C05 | M2, M4, M5 |

## Source-to-component crosswalk

| Component group | Primary/standard sources | Books/courses | Official implementation evidence still needed |
|---|---|---|---|
| Data models, relational algebra, schemas | R-001; add further theory sources before drafting Volume II | R-026, R-025 | PostgreSQL/SQLite SQL semantics when comparing actual behavior |
| Query optimizer | R-027 | R-026, R-025 | Versioned EXPLAIN/planner docs and pinned source for each system study |
| Storage and B+ trees | R-029 for LSM; R-026/R-025 for teaching map; foundational B-tree primary work still to curate | R-026, R-025 | Page layout/source docs for each product; not generalized across engines |
| Transactions/isolation | R-028; R-032 metadata only pending source access | R-026, R-025 | Current PostgreSQL, SQLite, InnoDB isolation docs and schedule experiments |
| Recovery/WAL | R-032 metadata only pending publisher access; R-018 for OS API scope | R-026, R-025 | Product-specific WAL/logging/recovery docs and source tags |
| Distributed consensus | R-030 | R-025 | FoundationDB or other product design docs/source; consensus paper does not establish product architecture |
| Columnar execution | R-031 | R-026, R-025 | DuckDB versioned internals docs/source and workload-specific claims |
| Implementation language/networking | R-013, R-016, R-022 | R-025 | Rust version/toolchain pin and protocol-specific requirements |

## Research items still required before later chapters

- B-tree insertion/deletion/concurrency/recovery primary literature and current product implementations.
- Transaction processing foundations, locking/serializability, MVCC lineage, and recovery beyond one algorithm paper.
- Distributed systems foundations for failure detectors, clocks, quorums, consistency, and two-phase commit.
- Normalization, relational calculus, SQL semantics, and standards-level behavior.
- Official docs and pinned source-code locations for all eight target systems.
- Observability and benchmark methodology sources, including benchmark specifications and reproducible workload design.
- A direct publisher-access attempt for ARIES; keep R-032 marked NEEDS REVIEW until verified access or an explicitly approved alternative is documented.
