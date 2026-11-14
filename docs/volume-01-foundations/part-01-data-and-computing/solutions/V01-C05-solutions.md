# V01-C05 — Worked Solutions and Grading Guidance

This file accompanies [V01-C05 — Files, Memory, and Persistent Storage](../V01-C05-files-memory-persistent-storage.md). Answers are scoped to stated platform assumptions. Linux and Rust examples here do not define every OS's persistence contract.

## C05-E1 — Trace a file write

One illustrative path is: Rust `Vec<u8>` in process virtual memory → `Write` implementation / possible userspace adapter buffer → OS write interface → Linux page cache and filesystem writeback → block layer/device controller cache → nonvolatile media. A plain `File` write is not itself buffered by Rust `File`; a `BufWriter` introduces a userspace buffer. Normal Linux buffered filesystem I/O uses the page cache, but direct I/O, memory mapping, remote filesystems, and virtual devices change details. The arrows indicate possible movement and responsibility, not an invariant physical sequence. The process buffer is owned by the application; kernel caches and device caches are managed by lower layers.

**Rubric:** layers 5, ownership 3, limitations 2.

## C05-E2 — API guarantees

- `write` attempts one operation and may return a positive short count; the caller must process the remainder or handle an error.
- `write_all` repeatedly calls `write` until the entire buffer is accepted or a non-retryable error occurs. It does not mean the bytes are durable.
- `flush` on a Rust buffered writer pushes that adapter's buffered bytes to its underlying writer. `File` itself does not buffer `Read`/`Write` operations; its `flush` is not a filesystem durability operation.
- `sync_data` requests synchronization of content and metadata needed to retrieve that content; platform implementations may use behavior equivalent to `sync_all`.
- `sync_all` attempts to synchronize file content and metadata to the filesystem and returns an error if the request fails. It does not by itself make a containing directory entry durable, make a logical transaction atomic, or cover arbitrary hardware that violates its documented contract.

**Rubric:** distinctions 8, error behavior 4, limits 3.

## C05-E3 — Durable name creation

Under Linux, write the new file and check the complete-write result; synchronize the file so its content/required metadata are requested; rename it to `db`; then synchronize the containing directory to request persistence of the namespace change. The file sync alone does not necessarily persist a directory entry. If the rename crosses directories, reason about the relevant directory entries (and filesystem guarantees) rather than treating the rename as a database transaction. The exact contract depends on Linux, filesystem, mount/device stack, and error handling; the exercise is not a universal recipe.

**Rubric:** file-vs-directory distinction 6, sequence/reasoning 6, platform scope 3.

## C05-E4 — Short I/O and interrupted format

For a 600-byte write that returns 400, advance the source slice/file offset by 400 and attempt the remaining 200 bytes, handling further short counts, interruption, zero progress, and errors. For reading a fixed-size field, loop until the target is filled, EOF occurs, or an error occurs; a short positive read does not prove EOF. At EOF before 600 bytes, the application must report an incomplete structure rather than parse uninitialized or stale bytes. Protections include a length/header plus checksum to detect a torn record, and a write-ahead log or copy-on-write/atomic-replacement protocol to recover from interruption. Detection alone is not repair; state that distinction.

**Rubric:** write continuation 5, read behavior 4, two protections 7, edge cases 4.

## C05-E5 — Failure models

A process crash stops the application but leaves the kernel running; dirty cached pages may still be written later. An OS crash loses volatile kernel state and in-flight operations. Sudden power loss can also lose device/controller cache contents or expose ordering/flush limitations. Killing only the process exercises application interruption, not loss of kernel/device volatile state, so it cannot establish power-loss durability. A defensible claim states the tested failure, storage/filesystem/device assumptions, synchronization result, and untested cases.

**Rubric:** failure models 6, evidence boundary 5, assumptions 4.

## C05-E6 — Append-only durability contract lab

**Reproducible API check:** compile the Section 9 Rust program with Edition 2024; successful execution prints `readback=[83, 71, 68, 66, 45, 67, 48, 53, 10]`. `write_all` and `sync_all` errors propagate via `?`; the sample reads back the payload and removes the file. If `strace` exists, the synchronized variant should include a synchronization request. Both variants can read back while the OS remains running. This is not a power-failure test, device-level trace, or benchmark. The sample does not sync the temporary directory, so namespace durability is outside scope.

**Example durability contract:** Each appended record has a magic value, format version, fixed-endian length, payload, and checksum. The writer does not report an append as committed until all record bytes have been accepted and the selected synchronization call returns success under the documented Linux/filesystem/device assumptions. A successful sync is the API commit point for this toy contract, not proof against hardware that lies about flush completion. If an append is interrupted before a successful sync, recovery scans from the last known-valid boundary: a record with valid framing, length, and checksum may be retained according to policy; an incomplete or invalid suffix is rejected/truncated to that boundary. If synchronization returns an error, the outcome is uncertain; reopen and recover rather than assuming the record is absent. A process-kill test can check application recovery paths, but not OS crash or power-loss behavior. A production-grade system would need validated assumptions, ordering, checksums, fault injection, and hardware/filesystem-specific testing.

**Rubric:** reproducible experiment 5, contract precision 7, recovery behavior 5, tests/failure cases 5, evidence limits 3. Other contracts receive full credit when the commit point and interrupted-write behavior are explicit.
