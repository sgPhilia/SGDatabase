# Curriculum Dependency Matrix

**Purpose:** Map the current 13-volume roadmap to learning objectives, prerequisites, engineering outcomes, research needs, and implementation milestones. This is a curriculum planning artifact, not a chapter registry. It does not assign chapter IDs where none exist.

**Sources of scope:** topic names come from `ROADMAP.md`; Volume I identifiers/statuses come from `CHAPTER_STATUS.md`; the 22 milestone numbers are the existing ROADMAP baseline. `M#` refers to that milestone list, not a chapter ID.

## Status key

- `NEEDS REVIEW`: existing chapter file; current acceptance evidence is incomplete.
- `IN PROGRESS`: active authoring or substantive revision; not yet ready for acceptance review.
- `DRAFT`: chapter file exists but has not completed review.
- `NOT STARTED`: a Volume I ID exists but no chapter file exists.
- `OUTLINE ONLY`: planned topic has no stable chapter ID or chapter content.
- `SPECIFICATION IN PROGRESS`: implementation unit has a roadmap milestone, but its detailed specification is not yet accepted.

Math, algorithm, engineering, and research entries describe expected teaching needs, not claims that content already exists.

## Volume I — Data, Computing, and Database Foundations

| ID | Planned topic / objective | Prerequisites and recommended earlier chapters | Math prerequisites | Algorithms / structures | Engineering application | Milestone | Research requirement | Content status |
|---|---|---|---|---|---|---|---|---|
| V01-C01 | What Is Data? Explain representation, context, schema, and data-management problems. | None | Units, ordering, and simple quantitative reasoning | Records and field/value distinctions | Identify data contracts and invalid assumptions | M3, M9 | Foundational data/database terminology; claim-mapped product examples | NEEDS REVIEW |
| V01-C02 | Meaning, Models, and Information. Separate semantic interpretation from statistical measures. | C01 | Sets, functions, basic probability vocabulary | Model/schema/instance distinction | Audit data meaning and provenance | M3, M9 | Semantics and modeling sources | COMPLETED |
| V01-C03 | Information Theory. Derive entropy, conditional entropy, mutual information, and coding bounds. | C02; probability notation introduced locally | Discrete probability, logarithms, expectation, inequalities | Prefix trees; Huffman coding | Estimate compressibility without confusing entropy with semantics | M3, M5 | Shannon and coding theory primary sources | COMPLETED |
| V01-C04 | Bits, Bytes, Types, and Encodings. Explain integer, float, text, and portable encodings. | C01–C03 | Positional notation, modular arithmetic, rounding | Fixed-width representations; explicit serializers | Inspect bytes and define portable formats | M3, M5, M10 | Standards and Rust language references; claim scope recorded | COMPLETED |
| V01-C05 | Files, Memory, and Persistent Storage. Explain address spaces, file I/O, buffering, synchronization scope. | C04 | Size, offset, throughput/latency | Page/block abstraction; buffers | Observe writes and persistence boundaries | M2, M4, M5, M16–17 | OS manuals, filesystem/storage documentation, Rust I/O docs | NEEDS REVIEW |
| V01-C06 | Programs, Processes, and Client–Server Basics. Explain isolation, sockets, framing, timeout ambiguity. | C05 | Basic rates and timeout reasoning | Request/response state machine; framing | Specify a bounded local protocol | M21 | OS/network standards and official language docs | DRAFT |
| V01-C07 | What Is a Database? Define database meanings and boundaries. | C01–C06 | Sets and relation vocabulary as needed | Data model distinctions | Compare dataset, service, and DBMS boundaries | M9, M21 | Database textbooks and system definitions | NOT STARTED |
| V01-C08 | What Is a DBMS and Why Use One? Explain services and guarantees. | C07 | Logic of guarantees and invariants | Catalog, storage, execution, transaction component map | Translate requirements into DBMS responsibilities | M9, M15–17 | Architecture survey plus official system docs | NOT STARTED |
| V01-C09 | Files and Application-Managed Data vs DBMS. Compare design alternatives. | C05, C07–C08 | Cost/benefit comparisons | Locking, indexing, validation as concepts | Select file or DBMS approach for a workload | M1–9 | Comparative cases from official docs | NOT STARTED |
| V01-C10 | Database Workloads and System Families. Classify OLTP, OLAP, embedded, key-value, etc. | C07–C08 | Rates, distributions, summary statistics | Workload characterization | Choose candidate system and measurements | M1, M21–22 | Workload papers, benchmarks, official system positioning | NOT STARTED |
| V01-C11 | A First Map of a Database System. Trace request through major subsystems. | C06–C10 | No new math; dependency graph | Parser, catalog, planner, executor, storage, transaction managers | Trace a query and identify failure boundaries | M9–21 | Architecture surveys and contrasting system docs | NOT STARTED |

## Volume II — Data Modeling and Relational Theory

No stable chapter IDs have been assigned. Learning objectives are to model requirements precisely, reason formally about relations and constraints, and justify schema decomposition.

| Planned topic | Objectives | Prerequisites / recommended chapters | Math | Algorithms / structures | Engineering applications | Milestones | Research needs | Status |
|---|---|---|---|---|---|---|---|---|
| Modeling requirements and entities | Convert domain statements into entities, attributes, relationships, and constraints. | V01-C01–C02; proposed sets/logic primer | Sets, predicates, functions | ER model, cardinalities | Requirement interviews and conceptual schema | M9 | Database design texts and case studies | OUTLINE ONLY |
| ER diagrams and constraints | Represent participation, cardinality, weak entities, and constraints. | Requirements/entities | Sets and logic | Graph-like schema representation | Map conceptual design to relational schema | M9 | Textbook and formal-model sources | OUTLINE ONLY |
| Relational model, relations/tuples/domains | Define relation instances, schemas, domains, and semantics. | ER model; sets/functions | Set theory, predicates | Relations, tuples, mappings | Design base relations and reason about set semantics | M9–14 | Codd (1970), formal texts | OUTLINE ONLY |
| Keys and integrity | Derive key constraints and entity/referential integrity. | Relational model | Logic, set inclusion | Key search as a reasoning procedure | Declare and validate constraints | M9 | Standards and database theory sources | OUTLINE ONLY |
| Functional dependencies | Define and test dependencies as schema constraints. | Relations, keys | Logic and set notation | Attribute-set reasoning | Discover design constraints from domain rules | M9 | Normalization theory sources | OUTLINE ONLY |
| Inference and candidate keys | Compute closure, candidate keys, and implication. | Functional dependencies | Set closure, proof reasoning | Attribute closure algorithm | Validate schemas and dependency claims | M9 | Formal database theory | OUTLINE ONLY |
| Normalization: 1NF, 2NF, 3NF, BCNF | Diagnose redundancy and derive decompositions. | FDs, keys | Set logic | Decomposition algorithms | Reduce update anomalies while preserving requirements | M9 | Foundational papers/textbook chapters | OUTLINE ONLY |
| Decomposition, lossless join, dependency preservation | Prove decomposition properties and their trade-offs. | FDs, normalization | Relational joins, implication | Chase or closure-based tests as selected | Evaluate schema decomposition | M9 | Formal sources and worked proofs | OUTLINE ONLY |
| Relational algebra | Define operators and compose queries formally. | Relational model | Set algebra | Selection, projection, join, union, difference | Connect declarative requests to plans | M13–14, M20 | Codd and query-processing texts | OUTLINE ONLY |
| Relational calculus | Express queries using tuple/domain variables and safe formulas. | Relational model, logic | First-order logic, quantifiers | Formula evaluation / equivalence reasoning | Understand declarative expressiveness | M10–14 | Original theory and textbook sources | OUTLINE ONLY |
| Logical data independence | Explain logical/physical separation and its limits. | Relational model, schema | Abstraction and equivalence | View mappings | Evolve physical design without application rewrites | M9, M14 | Codd, architecture sources | OUTLINE ONLY |
| Schema design case studies | Apply modeling and formal checks to realistic domains. | All preceding Volume II topics | Relevant math from above | ER-to-relational translation, FD analysis | Produce and critique a schema | M9 | Verified domain requirements and design sources | OUTLINE ONLY |

## Volume III — SQL and Database Use

| Planned topic | Objectives | Prerequisites / recommended chapters | Math | Algorithms / structures | Engineering applications | Milestones | Research needs | Status |
|---|---|---|---|---|---|---|---|---|
| SQL history and standards | Locate SQL dialect behavior and standard scope. | V02 relational model/calculus | Logic | Language version/dialect comparison | Choose and document a supported dialect | M10–12 | Standards history and vendor docs | OUTLINE ONLY |
| Schemas, types, DDL | Define objects and type constraints. | Relational model; V01-C04 | Domains and value sets | Catalog entries and type descriptors | Create schema and evolve it | M9–12 | SQL standard and product docs | OUTLINE ONLY |
| Constraints and keys | Explain declarative constraints and enforcement timing. | V02 keys/integrity | Predicate logic | Constraint validation | Prevent invalid database states | M9, M12–13 | Standards and vendor docs | OUTLINE ONLY |
| SELECT and expressions | Interpret projection and expression semantics. | Relational algebra; basic SQL | Expressions and types | Parse tree and expression evaluation | Query and inspect results | M10–14 | SQL standard and dialect docs | OUTLINE ONLY |
| Filtering, NULL, three-valued logic | Reason about predicate truth with NULL. | SELECT, types | Propositional logic | Truth tables | Avoid incorrect filters and constraints | M10–14 | SQL standard and system behavior docs | OUTLINE ONLY |
| Joins | Explain join semantics, NULL behavior, and cardinality. | Relational algebra, SELECT | Set/cartesian product | Join algorithms introduced later | Build correct multi-table queries | M13–14, M20 | Textbooks and optimizer studies | OUTLINE ONLY |
| Aggregation | Explain grouping, aggregates, and empty/null cases. | SELECT, grouping | Functions, counts | Hash/sort aggregation | Analytical queries | M13, M20 | Standard plus product docs | OUTLINE ONLY |
| Subqueries and CTEs | Explain scope and equivalent formulations. | SELECT, joins | Logic/quantification | Subquery plans | Express multi-stage queries | M10–14, M20 | Standards and optimizer docs | OUTLINE ONLY |
| Set operations | Apply union/intersect/except semantics. | Relational algebra | Set operations | Duplicate elimination | Combine query results | M13–14 | SQL standard | OUTLINE ONLY |
| Window functions | Explain partition, ordering, frames, and peers. | Aggregation, ordering | Sequences and intervals | Window operator | Ranking and running aggregates | M13–14, M20 | Standards and official docs | OUTLINE ONLY |
| Views | Explain logical views and update/security limits. | Schemas, SELECT | Relational mappings | View expansion | Encapsulate queries and permissions | M9–14 | Standards and system docs | OUTLINE ONLY |
| DML and data change | Explain insert/update/delete and constraint effects. | Types, constraints | State transitions | Write operators | Maintain records safely | M9, M13, M15–17 | Standard and product docs | OUTLINE ONLY |
| Transaction statements | Use begin/commit/rollback and understand scope. | DML, V08 foundations | Histories and state | Transaction state machine | Bound a unit of work | M15–19 | SQL standard and vendor docs | OUTLINE ONLY |
| SQL security and privileges | Explain principals, grants, and least privilege. | Schemas, views, client/server | Set-based policy reasoning | ACL/role graph | Secure database access | No current milestone; propose placement in M9/M21 scope | Security standards and official docs | OUTLINE ONLY |
| Reading execution plans | Interpret physical operators and estimates. | Joins, aggregation, V04 architecture | Cost and cardinality basics | Plan tree/DAG | Diagnose query behavior | M13–14, M20 | Product-specific EXPLAIN docs | OUTLINE ONLY |
| Scoped SQL dialect comparison | Compare selected semantics across products. | Core SQL topics | Relevant semantic models | Dialect feature matrix | Portability testing | M10–15 | Version-pinned product docs | OUTLINE ONLY |

## Volume IV — Database Architecture

| Planned topic | Objectives | Prerequisites / recommended chapters | Math | Algorithms / structures | Engineering applications | Milestones | Research needs | Status |
|---|---|---|---|---|---|---|---|---|
| System boundaries and interfaces | Identify DBMS responsibilities and contracts. | V01-C08, SQL foundations | Interface invariants | Component boundaries | Trace client-to-engine calls | M9, M21 | Architecture surveys and source studies | OUTLINE ONLY |
| Client/server and embedded models | Compare process and API deployment choices. | V01-C06–C10 | Failure probability vocabulary | IPC/network service boundary | Choose deployment architecture | M21 | SQLite/PostgreSQL docs; version context | OUTLINE ONLY |
| Connection/process/thread models | Explain worker, thread, and connection lifecycles. | OS process/thread foundations (partly C06) | Concurrency reasoning | Queues, pools, synchronization | Bound resources and coordinate clients | M21 | Official docs/source code | OUTLINE ONLY |
| Query lifecycle | Trace SQL from receive to result. | SQL, architecture boundaries | State transitions | Parser-to-executor pipeline | Diagnose failures by stage | M10–14 | Hellerstein et al.; system docs | OUTLINE ONLY |
| Lexing, parsing, AST | Explain grammar processing and syntax structures. | SQL syntax, programming language basics | Grammars/induction | Lexer, parser, AST | Implement defined SQL subset | M10–12 | Compiler texts and verified source | OUTLINE ONLY |
| Catalog and metadata | Explain metadata model and lookup. | Relational model, schemas | Relations and constraints | Catalog tables/indexes | Define objects and bind names | M9–12 | System catalog docs/source | OUTLINE ONLY |
| Logical and physical plans | Distinguish semantic query plans from execution choices. | Relational algebra, query lifecycle | Equivalence | Plan tree, operator graph | Build and transform plans | M14, M20 | Selinger and later optimizer work | OUTLINE ONLY |
| Executor and operator interfaces | Explain iterator/vectorized operator contracts. | Plans, relational operators | Streams and cardinalities | Volcano, vector batches | Implement query operators | M13 | System papers/docs | OUTLINE ONLY |
| Storage/buffer/transaction managers | Define component contracts and interactions. | Storage, indexes, transactions | Invariants/state machines | Pages, frames, transaction tables | Integrate engine subsystems | M2–9, M15–19 | Architecture papers and code studies | OUTLINE ONLY |
| Monitoring and utilities | Explain operational status surfaces and maintenance. | Architecture, security, workloads | Metrics/distributions | Counters, histograms, event logs | Diagnose and maintain service | No explicit milestone; propose placement under advanced/ops scope | Official observability docs | OUTLINE ONLY |

## Volume V — Storage Engines

| Planned topic | Objectives | Prerequisites | Math | Algorithms / structures | Engineering applications | Milestones | Research needs | Status |
|---|---|---|---|---|---|---|---|---|
| Storage hierarchy and latency | Model latency, bandwidth, and cost at storage layers. | V01-C04–C05; architecture basics | Units, distributions, amortized cost | Queue/buffer models | Predict I/O bottlenecks | M2, M4, M6 | Hardware and storage-system studies | OUTLINE ONLY |
| Records and tuple formats | Design typed record representation. | Types, schemas | Length/offset arithmetic | Serialization, record IDs | Read/write heap records | M3, M5 | Formats and system docs | OUTLINE ONLY |
| Binary encoding and portability | Specify endian, version, and compatibility rules. | V01-C04 | Modular arithmetic | Encoders/decoders | Portable page and log formats | M3, M5, M16 | Standards and format docs | OUTLINE ONLY |
| Pages and blocks | Explain fixed-size I/O and page identifiers. | File I/O, records | Bounds/offsets | Page allocator, page table | Create page-oriented files | M4 | OS/database storage sources | OUTLINE ONLY |
| Slotted-page layout | Design variable-length record placement. | Pages, records | Offset arithmetic | Slot directory, free-space tracking | Insert/delete/compact records | M5 | Textbook and system source studies | OUTLINE ONLY |
| Heap files and free-space management | Manage unordered records and free pages. | Pages, records | Space accounting | Free lists/maps | Scan and insert tuples | M3–5, M9 | System docs/source | OUTLINE ONLY |
| Buffer pool, pinning, latching | Manage frames and distinguish logical pins from physical latches. | Pages, I/O, concurrency basics | State invariants | Frame table, page table, guards | Cache disk pages safely | M6 | Buffer management research/docs | OUTLINE ONLY |
| Replacement policies | Compare eviction policies and workload effects. | Buffer pool | Probability and traces | LRU, CLOCK, LRU-K | Measure hit rates and anomalies | M6 | Primary policy papers/course material | OUTLINE ONLY |
| I/O paths and write buffering | Explain buffered/direct I/O and write-behind. | OS/filesystems, buffer pool | Ordering | Queues, dirty-page lists | Choose flush strategy | M2, M6, M16 | OS docs and system implementation sources | OUTLINE ONLY |
| Checksums and page integrity | Detect corruption and understand limits. | Page formats, errors | Probability of collision | Checksums, checks | Validate pages on read/write | M5, M17 | Standards and system docs | OUTLINE ONLY |
| Large values | Store oversized values and indirections. | Records/pages | Size thresholds | Overflow pages/chains | Store large objects | M3–5 | Official format docs | OUTLINE ONLY |
| Row vs column layouts | Compare access granularity and update/read workloads. | Records, workloads | Compression/statistics | Row/column encodings | Choose layout and explain trade-offs | M3, M5 | C-Store and vendor docs | OUTLINE ONLY |
| LSM storage overview | Explain buffered writes, levels, compaction, and read/write costs. | Pages, sorting, workload models | Amortized I/O analysis | Memtables, SSTables, compaction | Storage-engine trade-off study | M2, M7, M16 | O’Neil et al. and official RocksDB/LevelDB docs | OUTLINE ONLY |
| Storage testing and crash boundaries | Define crash points and storage invariants. | Page formats, I/O, recovery basics | State-transition reasoning | Fault injection, recovery traces | Reopen after injected failures | M4–6, M16–17 | Recovery papers and test methodology | OUTLINE ONLY |

## Volume VI — Indexes and Access Methods

| Planned topic | Objectives | Prerequisites | Math | Algorithms / structures | Engineering applications | Milestones | Research needs | Status |
|---|---|---|---|---|---|---|---|---|
| Index purpose and cost model | Explain access paths and maintenance costs. | Relational algebra, storage, workloads | Selectivity and I/O cost | Search structures | Decide whether index helps | M7–8, M20 | Selinger, database texts | OUTLINE ONLY |
| Ordered indexes | Define ordering, duplicate keys, range scans. | Keys, pages | Order relations | Sorted pages | Ordered lookup | M8 | B-tree primary sources | OUTLINE ONLY |
| B-tree/B+ tree page anatomy | Explain node shape and separator invariants. | Pages, ordered indexes | Tree height/logarithms | B-tree variants | Inspect index pages | M8 | Bayer/McCreight; product docs | OUTLINE ONLY |
| Search and range scans | Trace point and range operations. | Tree anatomy | Height and output-sensitive cost | Tree traversal | Key and range lookup | M8 | Primary research and implementation docs | OUTLINE ONLY |
| Insertion, splitting, propagation | Preserve balance and occupancy. | Search, node layout | Invariants and bounds | Split propagation | Persist inserts | M8 | Primary papers/texts | OUTLINE ONLY |
| Deletion, redistribution, merging | Restore occupancy and maintain references. | Insert/split | Invariants | Borrow/merge propagation | Delete and range-scan consistency | M8 | Primary papers/texts | OUTLINE ONLY |
| Concurrency and latching | Protect structural operations under concurrency. | B+ trees, locks vs latches | Interleaving/state reasoning | Crabbing/latch coupling | Concurrent index tests | M8, M18 | Concurrency papers and system source | OUTLINE ONLY |
| Recovery and structural changes | Recover splits/merges safely. | WAL, index structure | State transitions | Structural logging | Crash-safe index maintenance | M8, M16–17 | ARIES/IM and product docs | OUTLINE ONLY |
| Hash indexes | Explain hashing, collisions, and equality-only access. | Hashing foundations, pages | Probability/load factor | Buckets/chains | Exact-match index | M7 | Hashing papers/system docs | OUTLINE ONLY |
| Extendible/linear hashing | Grow hash structures incrementally. | Hash indexes | Bit prefixes, load analysis | Directory splitting/linear expansion | Dynamic persistent hashing | M7 | Original algorithm sources | OUTLINE ONLY |
| LSM trees, memtables, SSTables, compaction | Derive component lifecycle and read/write trade-offs. | Sorting, storage, Bloom filters | Amortized I/O | Levels, merge policy | Key-value engine design | M2, M7, M16 | O’Neil et al., RocksDB/LevelDB docs | OUTLINE ONLY |
| Bloom filters and skip lists | Explain probabilistic membership and ordered in-memory structure. | Hashing, probability, trees | False-positive probability | Bloom filter, skip list | Reduce reads; memtable indexing | M7 | Original papers and system docs | OUTLINE ONLY |
| Bitmap, inverted, spatial indexes | Compare specialized access methods. | Workloads, ordering, sets/geometry | Bitset/cardinality/spatial bounds | Bitmap, inverted, R-tree | Analytical, text, spatial retrieval | M7–8 | Primary papers and product docs | OUTLINE ONLY |
| Index selection and maintenance | Reason about write amplification and workload fit. | All index types, query plans | Cost/statistics | Index advisor concepts | Benchmark and maintain indexes | M7–8, M20 | Vendor docs, workload studies | OUTLINE ONLY |

## Volume VII — Query Processing and Optimization

| Planned topic | Objectives | Prerequisites | Math | Algorithms / structures | Engineering applications | Milestones | Research needs | Status |
|---|---|---|---|---|---|---|---|---|
| Relational algebra review | Map SQL to algebraic expressions. | Volume II relational algebra; SQL | Set operations | Operator trees | Logical query representation | M13–14, M20 | Codd, optimizer texts | OUTLINE ONLY |
| Parsing and binding | Convert syntax to resolved expressions. | SQL, grammars, types/catalog | Grammar and name resolution | AST, symbol tables | Semantic analysis of SQL | M10–12; missing semantic-analysis scope | Compiler/database sources | OUTLINE ONLY |
| Logical plans and equivalence | Apply semantics-preserving rewrites. | Relational algebra | Equivalence proofs | Logical plan tree | Reorder/project/filter | M14, M20 | Query rewrite papers | OUTLINE ONLY |
| Physical operators | Explain algorithms behind operators. | Logical plans, storage/indexes | Cost, cardinality | Scans, joins, aggregation | Execute plans | M13–14 | System docs/source studies | OUTLINE ONLY |
| Iterator/Volcano execution | Explain pull-based operator pipelines. | Physical operators | Streams and tuples | Iterator interfaces | Build composable executor | M13 | Graefe/Volcano sources | OUTLINE ONLY |
| Vectorized/push-based execution | Compare batch and push execution. | Operators, CPU/memory foundations | Batch/cost analysis | Vectors, chunks | Analytical execution | M13 | C-Store, DuckDB papers/docs | OUTLINE ONLY |
| Scans and filters | Implement sequential/index scan and predicates. | Pages, indexes, SQL predicates | Selectivity | Scan operators | Query table rows | M13 | System docs | OUTLINE ONLY |
| Sorting and aggregation | Analyze external sorting and grouping. | Storage, operators | Complexity, memory/I/O | Sort runs, hash tables | ORDER BY/GROUP BY | M13 | Algorithms and system sources | OUTLINE ONLY |
| Nested-loop, hash, sort-merge joins | Compare correctness and cost domains. | Relational joins, sorting, hashing | Cardinality, I/O complexity | Three join algorithms | Execute joins | M13 | Join algorithm papers/texts | OUTLINE ONLY |
| Cardinality/selectivity estimation | Estimate output sizes and uncertainty. | Probability/statistics, SQL | Distributions, sampling | Histograms/sketches | Feed optimizer decisions | M20 | Estimation research | OUTLINE ONLY |
| Cost models | Estimate CPU, memory, I/O, network. | Algorithms, storage, workload stats | Asymptotic and resource models | Cost vectors | Compare plans | M14, M20 | Optimizer literature/system docs | OUTLINE ONLY |
| Rule-based/cost-based optimization | Choose valid transformations and access paths. | Algebra, plans, estimates | Search and cost minimization | Dynamic programming/rules | Plan selection | M20 | Selinger and modern optimizer research | OUTLINE ONLY |
| Predicate/projection pushdown | Explain when early filtering is valid/useful. | Plan equivalence, NULL/type semantics | Predicate logic | Rewrite rules | Reduce data movement | M20 | Primary optimization papers/system docs | OUTLINE ONLY |
| Parallel execution and JIT | Explain execution parallelism and compilation boundaries. | Operators, concurrency, CPU architecture | Work partitioning | Pipelines, code generation | Accelerate plans safely | M13, M20 | Version-specific system papers/docs | OUTLINE ONLY |
| Plan inspection and benchmarking | Interpret plans and build controlled comparisons. | SQL, cost models, statistics | Experimental design/statistics | Bench harness/plan tree | Reproduce query performance | M20; benchmarking gap | Official explain docs, benchmark methodology | OUTLINE ONLY |

## Volume VIII — Transactions and Concurrency

| Planned topic | Objectives | Prerequisites | Math | Algorithms / structures | Engineering applications | Milestones | Research needs | Status |
|---|---|---|---|---|---|---|---|---|
| Transaction model and ACID | Define transactions and qualified ACID guarantees. | V01-C08, SQL DML | Invariants/state | Transaction state machine | State required guarantees | M15 | Gray, textbooks, standards | OUTLINE ONLY |
| Histories and schedules | Model interleavings and anomalies. | Transaction model | Partial orders/graphs | Schedule traces | Reproduce anomalies | M15, M18 | Concurrency theory | OUTLINE ONLY |
| Conflict/view serializability | Test serial equivalence. | Histories | Graph cycles and equivalence | Precedence graph | Prove schedules correct/incorrect | M18 | Theory papers/texts | OUTLINE ONLY |
| Lock modes and compatibility | Explain lock manager operations. | Histories, data granularity | Compatibility relations | Lock table/queues | Implement transactional locking | M18 | Locking research/vendor docs | OUTLINE ONLY |
| 2PL and strictness | State protocol guarantees and variants. | Lock modes | Serializability proof | Lock state machine | Prevent cascading effects | M18 | Primary papers/texts | OUTLINE ONLY |
| Deadlocks | Detect, prevent, or resolve wait cycles. | Lock manager, graph theory | Graph cycles | Wait-for graph | Handle blocked transactions | M18 | OS/database sources | OUTLINE ONLY |
| Timestamp ordering | Order operations with logical timestamps. | Histories | Order relations | Read/write timestamps | Alternative concurrency control | M18 | Original concurrency papers | OUTLINE ONLY |
| Optimistic concurrency | Separate read, validation, and write phases. | Histories, state machines | Set intersections | Read/write sets | Validate low-contention workloads | M18 | Original OCC work | OUTLINE ONLY |
| MVCC versions and visibility | Define version chains and snapshots. | Transactions, storage, timestamps | Ordering/visibility predicates | Version chains | Snapshot reads and cleanup | M19 | Primary MVCC research and vendor docs | OUTLINE ONLY |
| Snapshot isolation | Explain guarantees and write-skew limits. | MVCC, serializability | Histories/anomalies | Snapshot sets | Compare isolation | M19 | Berenson et al. and vendor docs | OUTLINE ONLY |
| SQL isolation anomalies and standards | Connect standard labels to phenomena and histories. | Schedules, SQL transactions | Logic/partial orders | Anomaly traces | Predict application-visible behavior | M18–19 | SQL standard, Berenson et al. | OUTLINE ONLY |
| Vendor isolation mappings | Compare named levels by observed guarantees. | Prior topic, system versions | Anomaly matrix | Versioned behavior table | Avoid false portability assumptions | M18–19 | Current official product docs | OUTLINE ONLY |
| Latches vs locks | Distinguish short physical protection from transaction locks. | OS concurrency, storage manager | Critical-section reasoning | Mutex/rwlock/latch | Protect memory/page structures | M6, M8, M18 | Systems and database sources | OUTLINE ONLY |
| Concurrency testing | Test races, schedules, and invariant preservation. | All concurrency topics | State-space reasoning | Deterministic scheduler/model tests | Reproduce concurrency bugs | M18–19 | Testing research/tool docs | OUTLINE ONLY |

## Volume IX — Logging, Recovery, and Durability

| Planned topic | Objectives | Prerequisites | Math | Algorithms / structures | Engineering applications | Milestones | Research needs | Status |
|---|---|---|---|---|---|---|---|---|
| Failure models | Classify process, machine, storage, and network failures. | OS/storage foundations | Probability only if quantified | Failure-state model | State system assumptions | M16–17 | Recovery papers and storage docs | OUTLINE ONLY |
| Volatile/stable storage assumptions | Distinguish persistence guarantees and hardware limits. | V01-C05, Volume V | Ordering and persistence semantics | Flush/barrier model | State durability contract | M16–17 | Standards, OS, vendor docs | OUTLINE ONLY |
| WAL rule | Derive log-before-data ordering requirement. | Transactions, storage | Ordering | Log sequence numbers | Enforce durable log prefix | M16 | ARIES and official docs | OUTLINE ONLY |
| Log record formats | Define records, LSNs, checksums, and framing. | Encoding, WAL | Sequence/order | Append-only log | Parse and validate logs | M16 | ARIES, product format docs | OUTLINE ONLY |
| Undo/redo | Explain compensation and repeat-history ideas. | Transaction model, WAL | State transitions | Undo/redo records | Roll back and recover updates | M16–17 | ARIES primary paper | OUTLINE ONLY |
| Checkpoints | Bound recovery scanning and define checkpoint semantics. | WAL, dirty pages, transactions | Snapshot/ordering | Checkpoint metadata | Resume after restart | M17 | Recovery research/vendor docs | OUTLINE ONLY |
| ARIES analysis/redo/undo | Trace recovery passes and invariants. | Logs, undo/redo, checkpoints | LSN ordering | Transaction/dirty-page tables | Implement bounded recovery model | M17 | Mohan et al. (metadata and access to verify) | OUTLINE ONLY |
| Torn pages/doublewrite | Explain partial writes and mitigation trade-offs. | Page formats, failure models | Atomicity boundaries | Doublewrite buffers/checksums | Detect/repair page corruption | M17 | Vendor docs and storage papers | OUTLINE ONLY |
| Group commit and flush semantics | Explain batching commits and persistence fences. | WAL, fsync semantics | Queueing/latency | Commit queue | Tune latency-throughput trade-off | M16–17 | Official docs, papers | OUTLINE ONLY |
| Recovery algorithms in systems | Compare implemented strategies by version. | Recovery theory | State model | System-specific logs | Read real storage/recovery behavior | M17 | Pinned source/docs per product | OUTLINE ONLY |
| Crash testing | Enumerate failpoints and verify invariants after reopen. | Failure models, recovery | State coverage | Failpoint harness | Validate persistence properties | M17 | Crash consistency research | OUTLINE ONLY |
| Durability claims/hardware caveats | Bound what “durable” means under configured stack. | OS/storage, logs | Reliability models | Persistence stack | Document deployment guarantees | M16–17 | Hardware/OS/vendor specs | OUTLINE ONLY |

## Volume X — Distributed Database Systems

| Planned topic | Objectives | Prerequisites | Math | Algorithms / structures | Engineering applications | Milestones | Research needs | Status |
|---|---|---|---|---|---|---|---|---|
| Network and failure models | State timing, omission, partition, and crash assumptions. | V01-C06, distributed basics | Probabilities and temporal reasoning | Failure detector abstraction | Specify protocol assumptions | M21–22 | Distributed systems foundations | OUTLINE ONLY |
| Partitioning/sharding | Compare range/hash/consistent partitioning. | Relational/key models | Hash/range distributions | Partition maps | Scale storage and route requests | M22 | Primary systems literature/product docs | OUTLINE ONLY |
| Replication topologies | Explain leader/follower and multi-leader behavior. | Networks, consistency | Ordering | Replication log | Availability/failover design | M22 | System papers/docs | OUTLINE ONLY |
| Consistency models | Define read/write guarantees and histories. | Distributed failure model | Partial order | Consistency histories | Select client-visible semantics | M22 | Primary theory papers | OUTLINE ONLY |
| Quorums | Derive read/write intersections and assumptions. | Replication | Set intersections | Quorum protocols | Replicated reads/writes | M22 | Primary research/system docs | OUTLINE ONLY |
| Consensus foundations | Explain agreement, validity, termination, safety/liveness. | Distributed systems, state machines | Logic/proofs | Consensus state machine | Order replicated commands | M22 | Foundational consensus papers | OUTLINE ONLY |
| Raft | Explain terms, elections, append, commit, membership limits. | Consensus foundations | Invariants/temporal logic | Replicated log | Simulate a replicated state machine | M22 | Ongaro/Ousterhout primary paper | OUTLINE ONLY |
| Distributed transactions and 2PC | Explain prepare/commit and blocking failure cases. | Transactions, networks, replication | State machines | Coordinator/participant protocol | Coordinate multiple shards | M22 | Primary transaction papers/system docs | OUTLINE ONLY |
| Clocks and ordering | Compare physical/logical clocks and causality. | Distributed failure model | Partial order | Lamport clocks/vector clocks | Order events and reason about freshness | M22 | Lamport primary work | OUTLINE ONLY |
| Distributed query processing | Explain data locality, shipping, and aggregation. | Query plans, partitions | Cost/network rates | Exchange operators | Execute distributed plans | M22; optimizer link | Distributed DB literature | OUTLINE ONLY |
| CAP assumptions and limits | State theorem model and avoid slogans. | Consistency, partitions | Impossibility reasoning | Replication model | Evaluate partition behavior | M22 | Gilbert/Lynch and formal treatments | OUTLINE ONLY |
| PACELC | Compare latency/consistency tradeoffs under normal/failure modes. | CAP, cost models | Decision trade-offs | Design taxonomy | Analyze system choices | M22 | Abadi primary paper | OUTLINE ONLY |
| Distributed SQL | Explain SQL semantics over distributed execution. | SQL, transactions, distributed query | Cost and ordering | Distributed plans | Compare products | M22 | Versioned official system docs | OUTLINE ONLY |
| Rebalancing/operational failures | Reason about movement, recovery, skew, and overload. | Partitioning, replication | Load distributions | Rebalance state machine | Operate a changing cluster | M22 | Product docs and systems papers | OUTLINE ONLY |

## Volume XI — Advanced Database Engineering

| Planned topic | Objectives | Prerequisites | Math | Algorithms / structures | Engineering applications | Milestones | Research needs | Status |
|---|---|---|---|---|---|---|---|---|
| Columnar systems | Explain column layout and scan/encoding trade-offs. | Storage, query execution | Compression/statistics | Column chunks, projections | Analytical workload design | M3, M5, M13 | C-Store and modern system papers | OUTLINE ONLY |
| Compression and encoding | Compare lossless codecs and database encodings. | Information theory, pages | Entropy/rates | Dictionary/RLE/bit packing | Reduce storage and I/O | M3, M5 | Primary compression work/system docs | OUTLINE ONLY |
| Vectorization and compilation | Compare vector batches and JIT/codegen. | Query operators, CPU architecture | Batch/cost | Vectors, code generation | Query CPU optimization | M13, M20 | Research and versioned docs | OUTLINE ONLY |
| In-memory systems | Explain memory-first design and persistence options. | Memory hierarchy, storage, recovery | Latency/working-set analysis | Indexes, logging | Select memory-resident architecture | M1–8, M16 | Papers and system docs | OUTLINE ONLY |
| Time-series, graph, search, event data | Compare specialized models and access patterns. | Data modeling, indexes | Time/order/graph basics | LSM, graph indexes, inverted indexes | Select specialized DB architecture | M7–9 | Primary systems papers | OUTLINE ONLY |
| Cloud-native storage/compute separation | Explain remote storage, caches, and failure boundaries. | Distributed systems, storage | Network/queueing cost | Cache hierarchies | Analyze managed architectures | M21–22 | Official architecture docs/research | OUTLINE ONLY |
| Security and threat modeling | Identify assets, boundaries, attack surfaces, and controls. | DBMS architecture, SQL privileges, networks | Risk models | AuthN/AuthZ, audit structures | Harden service and data | M9, M21; missing explicit security component | Standards and official docs | OUTLINE ONLY |
| Observability | Design logs, metrics, traces, and diagnostic evidence. | Architecture, operations | Statistics/time series | Counters, histograms, event schemas | Operate and troubleshoot DBMS | No explicit milestone; placement proposed in engine project | Official telemetry docs/research | OUTLINE ONLY |
| Benchmarking/workload design | Design representative, repeatable experiments. | Statistics, workload characterization | Sampling, variance, confidence | Harnesses/workload models | Compare system changes fairly | No explicit milestone; placement proposed in engine project | TPC specs, benchmark methodology | OUTLINE ONLY |
| Performance diagnosis | Locate bottlenecks from measurements and plans. | Storage, query, concurrency | Queueing/statistics | Profilers, counters | Tune without cargo-culting | M20 and proposed observability work | Reproducible systems studies | OUTLINE ONLY |
| Admission/resource governance | Explain resource budgets and contention. | Concurrency, workloads, observability | Queueing/fairness | Schedulers, quotas | Bound noisy-neighbor impact | No explicit milestone | Research and system docs | OUTLINE ONLY |
| Emerging research | Evaluate new techniques against prior foundations. | Relevant advanced topics | Topic-dependent | Topic-dependent | Research replication/critique | No fixed milestone | Current primary papers selected at drafting | OUTLINE ONLY |

## Volume XII — Real-World Database System Internals

Each study must teach the same comparison dimensions while preserving product-specific distinctions. Status is OUTLINE ONLY for each; no system-specific chapter ID exists.

| System | Objective and recommended prerequisites | Math / structures | Engineering focus | Milestones | Research evidence required | Status |
|---|---|---|---|---|---|---|
| PostgreSQL | Trace a mature general-purpose relational engine; prior SQL, storage, query, transaction, recovery topics | Heap/MVCC pages, B+ trees, WAL, executor | Version-pinned architecture, source paths, tests and trade-offs | M6, M8–20 | Current official docs plus source tag and seminal papers where relevant | OUTLINE ONLY |
| SQLite | Analyze embedded architecture, file format, locking, journals/WAL | B-trees, pager, page cache | Single-file behavior, concurrency modes, recovery claims | M2–9, M15–17 | Official docs, file format, source tag | OUTLINE ONLY |
| MySQL / InnoDB | Explain server and storage-engine boundary, clustered indexes, locking/recovery | B+ trees, undo/redo, MVCC | Version-pinned SQL-to-engine path | M6–20 | Official manuals and source tag | OUTLINE ONLY |
| DuckDB | Explain embedded analytical layout and vectorized execution. | Columnar chunks, vectors, compression | Query execution and analytical workload | M3, M5, M13, M20 | Official docs, papers, versioned source | OUTLINE ONLY |
| RocksDB | Explain embedded LSM key-value architecture and tuning. | Memtables, SSTables, Bloom filters, compaction | Write amplification, tuning, recovery | M2, M7, M16–17 | Official docs/design docs and source release | OUTLINE ONLY |
| LevelDB | Explain core LSM design and contrast with RocksDB. | Sorted runs, memtable, compaction | Narrow source study and API semantics | M2, M7, M16 | Official repository/docs at pinned revision | OUTLINE ONLY |
| Redis | Explain in-memory data structures, persistence and replication modes. | Hashes, skip lists, compact encodings | Latency, persistence modes, operational constraints | M1, M16–17, M21–22 | Official docs/source release; distinguish data structures from relational engine | OUTLINE ONLY |
| FoundationDB | Explain ordered key-value API, transaction layer and layered architecture. | Ordered keys, logs, consensus | Layered design, distributed transactions, fault model | M15–19, M22 | Official architecture papers/docs/source where available | OUTLINE ONLY |

## Volume XIII — Build a Database from Scratch

There are no chapter IDs for this volume. Each row maps the existing M# roadmap entry; detailed specifications are in `IMPLEMENTATION_SPECIFICATIONS.md`. Objectives and design notes below state the intended gate, not completed work.

| M# / unit | Objective | Prerequisites | Math | Required structures / algorithms | Engineering application | Research | Current status |
|---|---|---|---|---|---|---|---|
| M1 In-memory key-value store | Define get/put/delete semantics and in-memory state. | Rust, types, error handling | Key equality, complexity | HashMap or ordered map | Executable API and tests | Rust docs; map semantics | SPECIFICATION IN PROGRESS |
| M2 Persistent key-value store | Persist values and reopen state. | M1, files, encoding | Byte offsets/order | File layout, append/index choices | Persistence boundary experiments | OS/Rust I/O docs | SPECIFICATION IN PROGRESS |
| M3 Record storage | Encode typed records and stable identifiers. | M1–2, schemas/types | Length/offset bounds | Codec, record ID | Record round-trip and validation | Format references | SPECIFICATION IN PROGRESS |
| M4 Page-based storage | Manage page IDs and page I/O. | M2–3 | Bounds and allocation | Page file, allocator | Page reads/writes | Storage source literature | SPECIFICATION IN PROGRESS |
| M5 Page layout/serialization | Store variable records within pages. | M3–4 | Offset arithmetic, invariants | Header, slots, free space | Slotted page | Texts and implementations | SPECIFICATION IN PROGRESS |
| M6 Buffer pool | Cache bounded pages and manage guards. | M4–5, concurrency basics | State invariants | Frames, pins, replacer, latches | Eviction and dirty-page policy | Buffer policy research | SPECIFICATION IN PROGRESS |
| M7 Hash index | Provide persistent equality lookup. | M3–6, hashing | Load factor/probability | Buckets, collisions, splits | Index lookups | Hashing papers | SPECIFICATION IN PROGRESS |
| M8 B+ tree | Provide ordered and range lookup. | M5–7, trees | Height/occupancy | Internal/leaf pages, split/merge | Persistent ordered index | Bayer/McCreight and later work | SPECIFICATION IN PROGRESS |
| M9 Tables and schemas | Bind tuples to schema and enforce declared constraints. | M3–8, relational model | Predicate constraints | Catalog, types, heap/index links | Table create/scan/insert | Relational/system docs | SPECIFICATION IN PROGRESS |
| M10 SQL lexer | Tokenize explicitly scoped SQL. | M9, grammar/text | Character classes | Token stream | Lexer with errors | Language/compiler references | SPECIFICATION IN PROGRESS |
| M11 SQL parser | Parse grammar and precedence. | M10 | Grammar/induction | Parser, AST construction | Statements parse deterministically | Compiler references | SPECIFICATION IN PROGRESS |
| M12 AST | Represent parsed syntax for later phases. | M11 | Tree traversal | Typed AST nodes | Inspect/transform supported syntax | Compiler/database sources | SPECIFICATION IN PROGRESS |
| M13 Query executor | Execute a small set of relational operators. | M9, M12, operator theory | Cardinality/cost | Scan/filter/project/join operators | Execute fixed logical plans | Volcano/vectorization literature | SPECIFICATION IN PROGRESS |
| M14 Basic query planner | Create/select valid logical and physical plans. | M13, algebra | Cost estimates | Plan tree and rules | Select among scoped alternatives | Selinger paper/course | SPECIFICATION IN PROGRESS |
| M15 Transaction management | Define transaction lifecycle and atomic units. | M9, histories/ACID | Invariants | Transaction state/context | Single-process transaction semantics | Transaction texts/papers | SPECIFICATION IN PROGRESS |
| M16 Write-ahead logging | Enforce log-before-data in declared failure model. | M2, M4–6, M15 | LSN/order | Log records, flush protocol | Durable log append | ARIES and OS docs | SPECIFICATION IN PROGRESS |
| M17 Crash recovery | Recover state after enumerated crashes. | M16, undo/redo theory | State transitions | Analysis/redo/undo metadata | Deterministic restart | ARIES primary paper | SPECIFICATION IN PROGRESS |
| M18 Concurrency control | Prevent invalid interleavings under chosen policy. | M15, locks/schedules | Serializability | Lock table, wait graph | Concurrent transactions | Concurrency papers | SPECIFICATION IN PROGRESS |
| M19 MVCC | Provide snapshot visibility over versions. | M15–18, version theory | Timestamp order | Version chains, visibility rules | Snapshot reads and cleanup | MVCC sources | SPECIFICATION IN PROGRESS |
| M20 Query optimization | Rewrite/select plans with explicit estimates. | M13–14, statistics | Cost/search | Rules, estimator | Explainable plan choices | Selinger and modern research | SPECIFICATION IN PROGRESS |
| M21 Client/server protocol | Expose bounded request/response service. | M9–20, network framing/security | Timeouts/resource limits | Framing, errors, connection state | Local client/server | RFCs and protocol docs | SPECIFICATION IN PROGRESS |
| M22 Replication | Explore ordered state replication under a failure model. | M16–21, consensus foundations | Safety/liveness | Replicated log, quorum/consensus | Simulation then scoped protocol | Raft and distributed systems papers | SPECIFICATION IN PROGRESS |

## Proposed missing prerequisite topics — no IDs assigned

These are recommendations for review, not additions to the stable chapter registry:

1. **Discrete mathematics bridge:** sets, relations, functions, predicates, proof, combinatorics, and asymptotic notation. Needed before formal relational theory, complexity analysis, and correctness arguments. Prefer targeted sections integrated into Volume II or companion notes unless a diagnostic review shows a standalone unit is necessary.
2. **Algorithms and data structures bridge:** arrays/maps, hashing, trees, graphs, sorting, complexity, invariants. Needed before indexes, planners, and implementation milestones M1–M20.
3. **Computer architecture and performance foundations:** CPU caches, memory hierarchy, storage devices, latency/bandwidth, I/O paths. Volume V introduces storage hierarchy but does not yet map CPU/memory-level prerequisites.
4. **Concurrency foundations:** threads, synchronization, atomicity, races, and scheduling before Volume VIII and M6/M8/M18. V01-C06 introduces processes/networking but is not a concurrency course.
5. **Statistics and experimental design:** distributions, sampling, histograms, variance, confidence, and workload design before cardinality estimation and benchmarking.
6. **Programming-language/compiler foundations:** grammar, AST, name binding, type checking, and semantic analysis before SQL parser/planner milestones.

## Candidate implementation components not currently explicit milestones

- **Semantic analysis and name/type binding:** place after M12 AST and before M13 execution; likely expand M12 or define a subphase, not a new numbered milestone yet.
- **Type checking and expression semantics:** define with semantic analysis before planning/execution; connected to M9 and M12–14.
- **Constraint enforcement:** make explicit acceptance scope within M9 and transaction semantics M15; test under concurrent writes later.
- **Observability:** define a minimal event/metrics interface alongside M1/M6/M13, with integrated coverage by M21; production-style dashboards remain out of scope.
- **Benchmarking:** add reproducible harness, workload, warmup, latency percentiles, throughput, and environment capture to M6/M8/M13/M20 completion criteria; do not compare results without methodology.

These placements preserve the 22-milestone baseline. Phase B specifies them in `IMPLEMENTATION_SPECIFICATIONS.md` and leaves any milestone-count change for separate approval.
