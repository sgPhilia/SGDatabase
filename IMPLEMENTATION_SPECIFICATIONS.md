# Database Implementation Roadmap — Milestone Specifications

**Baseline:** the 22 ordered milestones in `ROADMAP.md`. This document specifies educational scope and acceptance; it does not authorize or begin implementation. The dependency map is in `CURRICULUM_DEPENDENCY_MATRIX.md`. All milestones remain NOT STARTED; specifications are proposed for review, not accepted designs.

## Shared specification and test rules

- Rust Edition 2024 is the primary implementation language. Pin the Rust toolchain and dependencies only when a crate is approved. Explain ownership, borrowing, synchronization, and error choices where they affect the design.
- State the supported operating system/filesystem/device assumptions. A passing test demonstrates only the behavior it exercises.
- Every persistent format defines versioning, byte order, bounds, corruption behavior, and compatibility policy before data is written.
- Separate logical transaction locks from in-memory page latches/pins. Define thread-safety and lock ordering before adding concurrency.
- For each milestone, tests include ordinary behavior, boundary cases, malformed input, and reopen/restart cases as applicable. Persistent components need injected I/O errors and enumerated crash points before any durability claim.
- Acceptance means API behavior, invariants, tests, docs, and known limitations are checked; it does not imply production readiness.

## M1 — In-memory key-value store

- **Purpose / curriculum:** establish explicit key/value semantics and basic Rust API construction; V01-C04, V02 data-model basics; foundational Rust/algorithms prerequisite remains to be taught.
- **Prerequisites:** Rust Edition 2024 basics, ownership/borrowing, collections, `Result`, equality/hash or ordering contracts.
- **Structures / algorithms:** `HashMap` baseline or ordered map; get/put/delete; no persistence or concurrency.
- **Invariants:** one current value per key; get returns the most recent successful put until delete; missing-key behavior is explicit; no mutable alias escapes the API.
- **Errors / failures:** invalid/oversized keys if constrained; allocation failure policy is documented; process exit loses all state by design.
- **Testing:** insert/update/get/delete, absent keys, key equality/hash contract examples, large and empty values, deterministic randomized operation sequence vs a reference map.
- **Acceptance:** documented semantics and complexity, idiomatic error handling, `cargo fmt/check/test/clippy` on a created project, tests pass, in-memory-only limitation stated.

## M2 — Persistent key-value store

- **Purpose / curriculum:** distinguish successful process writes from durable/recoverable state; V01-C05, Volume V; M1.
- **Prerequisites:** M1, file I/O, byte encoding, OS persistence caveats.
- **Structures / algorithms:** choose a simple append-only record log or another explicitly justified format; key lookup index; record framing/checksum as scoped.
- **Invariants:** a recovered key maps to the latest complete valid operation; torn/truncated final records are handled deterministically; file format version and byte order are specified.
- **Errors / failures:** short writes, ENOSPC, permission errors, corrupt length/checksum, truncated tail, process crash, power-loss model and directory metadata limits.
- **Testing:** round-trip/reopen, overwrite/delete semantics, truncation at every record byte boundary, injected I/O failures, corruption detection; distinguish process-crash tests from power-loss durability.
- **Acceptance:** reopening behavior and recovery policy are specified and tested; claims are limited to tested filesystem and sync protocol; no full transaction guarantee is claimed.

## M3 — Record storage

- **Purpose / curriculum:** encode typed records independent of language memory layout; V01-C04 and proposed type/relational prerequisites.
- **Prerequisites:** M1–2, schema/type basics, explicit serialization.
- **Structures / algorithms:** record header or field directory, record identifier, encoder/decoder, version tag as needed.
- **Invariants:** decode(encode(x)) equals x for supported values; decoder never reads beyond input; unsupported type/version fails explicitly; layout is independent of Rust struct padding.
- **Errors / failures:** malformed lengths, invalid tags, overflow, duplicate field IDs, unknown version, invalid UTF-8 or numeric encoding.
- **Testing:** golden byte vectors, round-trip property tests, malformed/fuzzed inputs, version mismatch, boundary values.
- **Acceptance:** format documented byte-by-byte; type limits and migration story stated; portable encoding test vectors pass.

## M4 — Page-based storage

- **Purpose / curriculum:** define page IDs, page-file organization, and I/O boundary; Volume V; M2–3.
- **Prerequisites:** file operations, offsets, record encoding, storage failure model.
- **Structures / algorithms:** fixed-size page abstraction, page ID to offset mapping, allocator/free-space metadata or explicit append-only allocation.
- **Invariants:** page ID maps to one aligned bounded region; no out-of-range page read/write; allocation state survives restart if claimed persistent.
- **Errors / failures:** short I/O, invalid page ID, allocation metadata corruption, partial page write, file growth failure.
- **Testing:** allocate/read/write/reopen, bounds, invalid IDs, injected short I/O/errors, page corruption handling.
- **Acceptance:** page size and compatibility limits declared; page allocator behavior deterministic; durability is described only within the chosen sync/failure model.

## M5 — Page layout and serialization

- **Purpose / curriculum:** store variable-length records within pages while preserving bounds; Volume V; M3–4.
- **Prerequisites:** page format, record codec, integer/offset arithmetic.
- **Structures / algorithms:** page header, slot directory, payload area, free-space accounting, compaction; explicit deletion/reuse policy.
- **Invariants:** slot offsets/lengths stay in the page; slots do not overlap live payload; free-space counters agree with layout; record IDs do not silently alias reused slots if generation tracking is promised.
- **Errors / failures:** page-full, corrupt slot, integer overflow, stale identifier, compaction failure.
- **Testing:** empty/full page, variable record sizes, delete/reinsert, fragmentation/compaction, malformed headers/slots, encode/decode property tests.
- **Acceptance:** layout diagram and invariants published; all mutating operations preserve invariant under error; corruption returns errors, not panic/UB.

## M6 — Buffer pool

- **Purpose / curriculum:** manage bounded in-memory page frames and I/O; Volume V; M4–5.
- **Prerequisites:** page store, Rust concurrency/synchronization, pin/latch distinction.
- **Structures / algorithms:** frame table, page table, pin count/guard, dirty state, replacer (choose and justify LRU/CLOCK/etc.), I/O synchronization.
- **Invariants:** pinned frames cannot be evicted; one canonical frame per resident page unless design explicitly permits duplicate reads; dirty eviction follows write policy; pins/latches released on all error paths.
- **Errors / failures:** all frames pinned, page read/write failure, duplicate concurrent load, dirty flush error, poisoned synchronization primitive.
- **Testing:** capacity/eviction traces, pin guards, dirty flush ordering, concurrent fetch of same page, injected I/O failures, no leaked pins.
- **Acceptance:** observable replacement behavior, concurrency contract, pin/latch semantics, and dirty-page failure behavior documented and tested.

## M7 — Hash index

- **Purpose / curriculum:** implement equality access and collision handling; Volume VI; M3–6, hashing prerequisite.
- **Prerequisites:** page layout, key serialization/comparison, persistence policy.
- **Structures / algorithms:** bucket pages, collision chains or extendible hashing if chosen; directory and split policy explicitly scoped.
- **Invariants:** each indexed key resolves to correct record(s); collisions preserve all entries; split/overflow updates are recoverable or explicitly in-memory-only.
- **Errors / failures:** corrupt bucket, duplicate key policy, split allocation failure, hash-version incompatibility.
- **Testing:** collisions, growth/split, duplicate keys, delete/reinsert, reopen, reference-map comparison, corruption cases.
- **Acceptance:** equality-only scope and key semantics explicit; no range-query claim; index/table consistency checks defined.

## M8 — B+ tree

- **Purpose / curriculum:** ordered point/range access and balanced tree invariants; Volume VI; M5–7, tree and I/O-cost foundations.
- **Prerequisites:** page layout, comparison semantics, buffer pool; concurrency/recovery variants are deferred or explicitly scoped.
- **Structures / algorithms:** internal and leaf pages, separator keys, sibling links if selected, search, insert, split, delete, redistribution/merge, root changes.
- **Invariants:** node key order, child/separator ranges, occupancy bounds except root, equal leaf depth, leaf-chain order, parent-child consistency.
- **Errors / failures:** page full, split allocation failure, corrupt pointer/separator, duplicate-key policy, crash during structural change.
- **Testing:** hand-computed split/merge traces, randomized reference-map operations, range scans, root grow/shrink, persistence/reopen, structural validator, injected failures.
- **Acceptance:** correctness proof sketches and structural validator; separate single-threaded baseline from later latch/recovery variants; persisted index matches table state.

## M9 — Tables and schemas

- **Purpose / curriculum:** connect relational schema, records, heap storage, and declared constraints; Volumes II–IV; M3–8.
- **Prerequisites:** relational model, types/NULL, catalog concepts, storage APIs.
- **Structures / algorithms:** catalog/schema descriptors, table heap, row IDs, indexes, constraint metadata; name resolution is specified but deeper semantic analysis may be a subphase.
- **Invariants:** inserted tuple conforms to schema; primary/unique constraints hold; references resolve if foreign keys are included; catalog and physical objects agree.
- **Errors / failures:** type mismatch, missing table/column, duplicate key, invalid constraint, catalog/storage partial failure.
- **Testing:** schema create/open, scan/insert, constraint rejection, schema versioning policy, catalog corruption and consistency checks.
- **Acceptance:** supported schema subset explicit; constraint enforcement semantics and transactional limits stated; no SQL parser implied.

## M10 — SQL lexer

- **Purpose / curriculum:** tokenize a deliberately scoped SQL subset; Volume IV/VII; M9.
- **Prerequisites:** character encoding, lexical rules, Rust string/byte handling.
- **Structures / algorithms:** token enum, source spans, scanner state.
- **Invariants:** every consumed input byte belongs to a token, whitespace, or documented error; token spans are valid; scanner always advances or returns an error.
- **Errors / failures:** unterminated strings/comments, invalid character, numeric overflow if lexed numerically, Unicode policy.
- **Testing:** golden token streams, whitespace/comments, escaped quotes, malformed inputs, source-span boundaries.
- **Acceptance:** supported tokens/dialect and error locations documented; no claim of SQL-standard lexer completeness.

## M11 — SQL parser

- **Purpose / curriculum:** parse grammar with deterministic errors; Volume IV/VII; M10.
- **Prerequisites:** lexer, grammar notation, precedence/associativity.
- **Structures / algorithms:** recursive descent/Pratt or other justified parser, syntax nodes or direct AST construction.
- **Invariants:** accepted input conforms to published grammar; parser consumes all input or explicitly supports trailing statements; precedence yields expected tree.
- **Errors / failures:** unexpected token, missing delimiter, nesting/stack limits, ambiguous grammar.
- **Testing:** positive grammar cases, precedence, malformed inputs, nested expressions, fuzz/property tests, error-span accuracy.
- **Acceptance:** grammar and error policy published; only declared subset accepted; parser is panic-free for arbitrary input.

## M12 — Abstract syntax tree

- **Purpose / curriculum:** define typed representation of supported SQL syntax for later semantic phases; Volume IV/VII; M11.
- **Prerequisites:** parser, syntax/AST concepts.
- **Structures / algorithms:** expression and statement enums, identifiers/spans, traversal/visitor as needed.
- **Invariants:** AST nodes represent only syntactically valid supported constructs; child relationships and source spans are coherent.
- **Errors / failures:** unsupported node/statement, excessively nested structure, invalid spans.
- **Testing:** parse-to-AST golden tests, traversal tests, round-trip formatting if implemented, deep nesting limits.
- **Acceptance:** syntax AST clearly separated from bound/typed logical plan; semantic analysis is a downstream stage, not silently mixed in.

## M13 — Query executor

- **Purpose / curriculum:** execute a small relational operator set; Volume VII; M9 and M12 plus relational algebra.
- **Prerequisites:** tuple/schema model, AST or prebuilt plan input, iterator semantics, error model.
- **Structures / algorithms:** scan, filter, projection, bounded joins; iterator or vector interface selected and justified.
- **Invariants:** operators preserve declared bag/set and NULL semantics; output rows satisfy schema; child resources close on error.
- **Errors / failures:** bad plan, type mismatch, child I/O error, cancellation/resource exhaustion.
- **Testing:** operator-level fixtures, empty/null/duplicate cases, differential comparison against a reference query engine for selected semantics, early-stop/cleanup tests.
- **Acceptance:** executor scope and semantics fixed; plans are explicit input at first; no optimizer implication.

## M14 — Basic query planner

- **Purpose / curriculum:** produce valid plans from a narrow logical query subset; Volume VII; M9–13.
- **Prerequisites:** relational algebra, schemas/binding, executor operators.
- **Structures / algorithms:** logical/physical plan nodes, simple rule selection and deterministic plan construction.
- **Invariants:** generated plan is semantically equivalent to input query under supported semantics; every plan node is executable; required columns/types align.
- **Errors / failures:** unsupported query, missing relation/column, invalid type, no feasible plan.
- **Testing:** logical-plan golden tests, equivalence comparisons, plan executor integration, invalid binding cases.
- **Acceptance:** basic planner is not conflated with cost-based optimizer; semantics and supported SQL subset documented.

## M15 — Transaction management

- **Purpose / curriculum:** define transaction lifecycle and single-process atomic unit before concurrency; Volumes VIII–IX.
- **Prerequisites:** tables/schemas, failure model, histories/ACID theory.
- **Structures / algorithms:** transaction context/state, begin/commit/abort, undo buffer or explicitly chosen baseline.
- **Invariants:** transaction state transitions are legal; aborted operations do not appear committed within stated model; commit boundary semantics are explicit.
- **Errors / failures:** double commit/abort, operation after finish, partial operation error, process crash before persistence support.
- **Testing:** lifecycle transitions, abort rollback, multiple changes, operation failures; fault cases before WAL only claim volatile atomicity.
- **Acceptance:** distinguish atomicity from durability; transaction isolation/concurrency not claimed until M18–19.

## M16 — Write-ahead logging

- **Purpose / curriculum:** enforce persistence ordering in an explicit failure model; Volumes V/IX.
- **Prerequisites:** M2, M4–6, M15, log ordering/persistence concepts.
- **Structures / algorithms:** log sequence numbers, records, append/flush interface, page LSN if used, commit record.
- **Invariants:** before a dirty page with LSN L reaches stable storage, log through L is stable; committed transaction acknowledgment follows the declared log flush rule.
- **Errors / failures:** partial log record, checksum mismatch, flush error, sequence wrap/overflow, disk full.
- **Testing:** write-order instrumentation, append/reopen, torn-tail handling, injected flush errors, crash at log/page/ack boundaries.
- **Acceptance:** platform and device assumptions documented; WAL ordering verified in tests; no durability claim beyond tested assumptions.

## M17 — Crash recovery

- **Purpose / curriculum:** restore consistent state after enumerated crashes; Volume IX; M16.
- **Prerequisites:** transaction/log format, undo/redo, checkpoints, failure model.
- **Structures / algorithms:** recovery metadata, analysis/redo/undo or named simpler algorithm, checkpoint state.
- **Invariants:** recovery is repeatable/idempotent for its supported model; committed updates survive as specified; loser transactions are undone as specified; pages/log references remain valid.
- **Errors / failures:** corrupt log, missing page, interrupted recovery, invalid checkpoint, torn page.
- **Testing:** crash injection at every state transition, repeated recovery, dirty-page cases, commit/abort cases, corrupt/truncated log.
- **Acceptance:** recovery trace, assumptions, and tested crash matrix published; recovery tests do not claim exhaustive physical durability.

## M18 — Concurrency control

- **Purpose / curriculum:** enforce chosen isolation guarantee among concurrent transactions; Volume VIII; M15–17.
- **Prerequisites:** schedules, serializability, locks/2PL, latches, logging.
- **Structures / algorithms:** lock table/queues, compatibility matrix, wait-for graph or timeout strategy, transaction scheduler.
- **Invariants:** granted locks satisfy compatibility; selected protocol guarantees its stated serializability/isolation; abort releases resources; no data races.
- **Errors / failures:** deadlocks, starvation, timeout, cancellation, lock upgrade conflicts.
- **Testing:** deterministic anomaly schedules, serialization checker, deadlock cycles, stress/race tests, crash/abort interaction.
- **Acceptance:** exact isolation policy and anomalies stated; latches are not treated as transaction locks; tests demonstrate only enumerated guarantees.

## M19 — MVCC

- **Purpose / curriculum:** implement version visibility for a specified snapshot policy; Volume VIII; M15–18 foundations.
- **Prerequisites:** timestamp/transaction IDs, snapshots, version storage, isolation anomalies, recovery.
- **Structures / algorithms:** version chains, visibility metadata, snapshot selection, garbage collection/vacuum rule.
- **Invariants:** snapshot reads see a stable permitted version; uncommitted/invisible versions are not exposed; concurrent writes obey selected conflict rule; reclaiming versions cannot invalidate active snapshots.
- **Errors / failures:** transaction ID wraparound, chain corruption, cleanup failure, long-lived snapshot/resource retention.
- **Testing:** visibility matrix, write-write conflicts, snapshot anomalies, active snapshot cleanup, crash/recovery interplay.
- **Acceptance:** chosen isolation guarantee named and tested; MVCC is not labeled serializable unless serializability is demonstrated.

## M20 — Query optimization

- **Purpose / curriculum:** choose semantics-preserving rewrites and scoped cost decisions; Volume VII.
- **Prerequisites:** M13–14, relational equivalence, statistics/cardinality foundations.
- **Structures / algorithms:** rule set, cardinality estimator, cost model, bounded plan enumeration (e.g. dynamic programming for declared query size).
- **Invariants:** rewrites preserve supported SQL semantics including NULL/duplicates; selected plan is valid; estimates/cost units are internally consistent.
- **Errors / failures:** missing/stale stats, estimate overflow, search-space explosion, unsupported expression.
- **Testing:** equivalence tests against reference execution, hand-computed costs, adversarial estimates, plan stability within declared constraints, benchmarks.
- **Acceptance:** distinguish estimated from measured cost; optimization correctness checked independently from performance; benchmark method recorded.

## M21 — Client/server protocol

- **Purpose / curriculum:** expose bounded request/response service and process boundary; Volumes IV/X.
- **Prerequisites:** M9–20 as applicable, networking/framing, authentication/security basics.
- **Structures / algorithms:** message framing/version, request IDs, connection state, worker/resource limits, error representation.
- **Invariants:** frame limits checked before allocation; malformed input cannot desynchronize parser state; responses correlate to requests; shutdown releases resources.
- **Errors / failures:** partial reads/writes, disconnect, timeout, duplicate retry, oversized frame, slow client, auth failure, server crash.
- **Testing:** fragmented/coalesced frames, EOF mid-frame, malformed lengths, resource exhaustion, client retry ambiguity, loopback integration tests.
- **Acceptance:** protocol spec and security limits documented; no claim that transport success means transaction commit; TLS/auth scope explicit.

## M22 — Replication

- **Purpose / curriculum:** explore replicated state machine or another selected replication model under a stated failure model; Volume X.
- **Prerequisites:** M16–21, distributed failure/consistency/consensus fundamentals.
- **Structures / algorithms:** begin with deterministic simulation; if protocol chosen, define log, term/epoch, quorum, leader, apply index, membership scope.
- **Invariants:** safety properties (e.g., one committed value/order) hold under modeled failures; state machine application is deterministic; acknowledgment semantics are clear.
- **Errors / failures:** partitions, node crashes/restarts, message loss/reordering/delay, stale leader, membership changes, storage failure.
- **Testing:** exhaustive small-state or deterministic fault schedules, log divergence, election races, restart, partition healing; model checking where feasible.
- **Acceptance:** simulation precedes networking; assumptions and safety/liveness claims are explicit; no production consensus/durability claim from a toy implementation.

## Missing components and proposed placement

These are subphases/acceptance scope proposals, not new milestones. Preserve 22 baseline numbers unless later review finds a dependency-driven reason to change them.

| Component | Placement proposal | Rationale and needed gate |
|---|---|---|
| Semantic analysis/name binding | After M12 AST, before M13 executor; expand M12 or define M12a without changing baseline numbering | Resolve table/column names, scope, and types before constructing executable plans. Gate: catalog resolution and precise error cases. |
| Type checking | M9 schema/type model plus semantic-analysis subphase | SQL expressions need typed operators, coercions, NULL rules. Gate: operator/type matrix and boundary tests. |
| Constraint enforcement | M9 table/schema scope, transactionally integrated with M15 and concurrency in M18 | Constraints can race or fail partway; declarative metadata must map to checked invariants. Gate: uniqueness/referential cases under concurrent transactions. |
| Observability | Small counters/events added in M1/M6/M13, integrated/assessed in M21 and final project | Diagnostic visibility should evolve with components; avoid a separate observability milestone unless scope expands. Gate: metrics have definitions, units, reset/overhead semantics. |
| Benchmarking | Measurement plan attached to M6/M8/M13/M20 acceptance and M22 experiments | Performance claims require workloads, warmup, environment, repetitions, percentiles, and baselines. Gate: reproducible harness and methodology. |
| Catalog/type implementation | M9, with semantic name/type resolution before M13 | Catalog objects must be durable and versioned; type checking needs a stable catalog contract. |
| Checkpointing | M17 recovery subphase, supported by M16 WAL | Checkpoint correctness depends on log/page ordering and active transactions; verify restart from every checkpoint boundary. |

## Specification status and approval

The above is a planning baseline. No milestone is marked implementation-ready until its course prerequisites are covered, decisions such as storage format/concurrency/failure model are resolved, and a milestone-specific acceptance plan is reviewed. All 22 implementations remain NOT STARTED.
