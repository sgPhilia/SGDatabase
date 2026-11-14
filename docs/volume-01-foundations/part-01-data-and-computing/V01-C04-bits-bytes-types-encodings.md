# V01-C04 — Bits, Bytes, Types, and Encodings

**Volume:** I — Data, Computing, and Database Foundations
**Part:** I — Data and Computing
**Status:** COMPLETED
**Prerequisites:** [V01-C01 — What Is Data?](V01-C01-what-is-data.md); [V01-C02 — Meaning, Models, and Information](V01-C02-meaning-models-information.md); [V01-C03 — Information Theory](V01-C03-information-theory-entropy-coding.md)
**Next:** [V01-C05 — Files, Memory, and Persistent Storage](V01-C05-files-memory-persistent-storage.md)
**Glossary:** [Canonical terms](../../../glossary.md)
**Concept index:** [Index](../../../INDEX.md)

## Learning objectives

After this chapter, you should be able to:

1. Read and convert small binary and hexadecimal values using positional notation.
2. Calculate the value ranges of unsigned and two's-complement signed integers.
3. Explain byte order and why a file or network format must specify it.
4. Explain how floating-point formats approximate real numbers and why decimal fractions may not be exact.
5. Distinguish a character, Unicode code point, scalar value, UTF-8 byte sequence, and user-perceived grapheme.
6. Explain why a programming-language type and an in-memory layout do not automatically define a portable database format.
7. Identify how representation choices affect database correctness, storage, comparison, indexing, and interoperability.

| Objective | Assessment evidence |
|---|---|
| 1 | E1; positional-notation calculation in Section 2 |
| 2 | E2–E3; range derivations and signed interpretation in Sections 3–4 |
| 3 | E4; byte sequence and decoder contract in Section 5 |
| 4 | E5; binary fraction and domain selection in Section 6 |
| 5 | E6–E7; Unicode layers, normalization, and Rust string distinctions in Section 7 |
| 6 | E8; versioned record format in Section 8 |
| 7 | E4–E8 and the representation audit lab; consequences in Section 9 |

## 1. From values to bit patterns

Computers store and move information using physical states that software treats as binary digits. A **bit** takes one of two abstract values, conventionally written `0` and `1`. A **byte** is a group of eight bits in the common modern usage. Network and file specifications often use *octet* when they require exactly eight bits; RFC 3629, for example, defines UTF-8 sequences in octets [R-013]. Some older systems used different byte sizes, so specifications must state the unit they mean. In this book, unless stated otherwise, a byte is eight bits.

A sequence of bits does not announce what it represents. The same eight bits might be interpreted as an unsigned integer, a signed integer, a character code fragment, part of a floating-point number, or flags. An **encoding** assigns bit patterns to values in a defined system. A **type** constrains how a program interprets and operates on values; it does not by itself define a durable interchange format. Rust's Reference describes a type as defining the interpretation of the memory holding a value and the operations available to it; layout is a separate issue [R-016].

The complete representation contract must answer at least:

- what values are permitted;
- how each value maps to bits/bytes;
- what byte order and framing are used;
- how invalid or reserved patterns are handled;
- how a reader discovers the type and version;
- how the encoding will remain interpretable by other programs and future software.

## 2. Binary and hexadecimal notation

In base `b`, a numeral with digits `dₖ…d₁d₀` denotes:

\[
\sum_{i=0}^{k}d_i b^i,
\qquad 0\le d_i<b.
\]

Binary is base 2, so each digit is 0 or 1. Hexadecimal is base 16, with digits `0` through `9` and `A` through `F`. One hexadecimal digit corresponds to four bits because `16 = 2⁴`; this makes hexadecimal a compact way to inspect binary data.

### Worked example: convert `1101 0110₂`

The place values from right to left are 1, 2, 4, 8, 16, 32, 64, and 128. Thus:

\[
11010110_2 = 128+64+16+4+2 = 214_{10}.
\]

Group bits into nibbles: `1101 0110₂ = D6₁₆`, since `1101₂=13=D` and `0110₂=6`.

The subscript indicates the base. A programming language may use prefixes such as `0b` and `0x`, but those prefixes are syntax, not part of the numeric value.

## 3. Unsigned integers

An `n`-bit unsigned integer uses every bit to represent magnitude. The smallest value is all zeroes, `0`; the largest is all ones:

\[
\sum_{i=0}^{n-1}2^i = 2^n-1.
\]

Therefore its range is `0` through `2ⁿ−1`, inclusive. An 8-bit unsigned value ranges from 0 to 255; a 16-bit one ranges from 0 to 65,535.

### Worked example: adding in a fixed-width type

In 8-bit unsigned arithmetic, the mathematical sum `250+10=260` does not fit. A language or API must define what happens: reject/raise an error, wrap modulo `2⁸` to 4, saturate at 255, or use a wider type. These behaviors are not interchangeable. A file parser that silently wraps a length field may turn a malformed large length into a small allocation request, corrupt boundaries, or create a security vulnerability.

For a database format, the decoder should validate lengths and ranges before using them for allocation, offsets, or indexing. The fact that an arithmetic operation is defined by a language does not mean its result is valid for the data format.

## 4. Signed integers and two's complement

Modern general-purpose processors commonly represent signed binary integers using **two's complement**. For an `n`-bit signed value, the range is:

\[
-2^{n-1}\;\text{through}\;2^{n-1}-1.
\]

One bit pattern is reserved as the high-order sign contribution; the pattern for the minimum value has no positive counterpart of the same magnitude. In 8 bits the range is −128 through 127.

For a nonnegative `n`-bit number `x`, its negative is encoded as `2ⁿ−x` (for `x>0`), equivalently invert the bits and add one. For example, `5` is `00000101₂`; invert to `11111010₂`, add one, and obtain `11111011₂`, which represents −5 in 8-bit two's complement.

### Why the same bits can mean different values

The byte `11111011₂` is 251 under unsigned interpretation and −5 under 8-bit two's-complement interpretation. A serialized record must provide enough schema/format context to choose correctly. A column's SQL type or a file's schema can supply that context; the raw bytes cannot.

### Overflow and database consequences

Signed overflow is a correctness boundary. Some programming languages define wraparound in some contexts; others may trap, reject, or make some overflow behavior undefined. In Rust, integer `+`, `-`, and `*` panic on overflow with debug assertions enabled; `-C overflow-checks` and `-C debug-assertions` control checks. APIs such as `checked_add`, `wrapping_add`, and `saturating_add` make intent explicit. Division by zero and signed-minimum divided by −1 are special cases. Consult the Rust Reference for precise operator and compiler-flag rules [R-016]. Database code should choose behavior explicitly for counters, offsets, lengths, transaction identifiers, and constraint arithmetic.

## 5. Byte order: ordering bytes inside multi-byte values

For a multi-byte integer, **endianness** specifies the order in which its bytes appear. Consider the 32-bit value `0x12345678`:

| Convention | Bytes at increasing addresses |
|---|---|
| Big-endian | `12 34 56 78` |
| Little-endian | `78 56 34 12` |

Neither sequence is inherently more correct. A CPU may use one native byte order, while a file format or network protocol chooses another. Portable formats specify byte order, and readers convert explicitly. If a program writes a native in-memory integer directly and another machine reads it under the opposite convention, the value changes.

Endianness is a property of a multi-byte representation, not a rule about whether bits inside each byte are written left-to-right in source code. Bit numbering conventions in standards and hardware documentation must be read separately.

### Rust example: choose an explicit wire/file encoding

```rust
fn main() {
    let value: u32 = 0x1234_5678;
    let bytes: [u8; 4] = value.to_le_bytes();
    assert_eq!(bytes, [0x78, 0x56, 0x34, 0x12]);

    let restored = u32::from_le_bytes(bytes);
    assert_eq!(restored, value);
}
```

The point is not that little-endian is universally preferred. The format must choose and document a convention, and both encoder and decoder must follow it. A storage format also needs versioning, field boundaries, validation, and checks for corrupt or truncated inputs.

## 6. Floating-point values: a finite approximation to a real number

The real numbers are unbounded and have arbitrary precision. A fixed-width computer format has finitely many bit patterns, so it cannot represent every real number exactly. Binary floating-point formats represent a finite subset using a sign, exponent, and significand (often informally called a fraction or mantissa). IEEE 754 specifies binary and decimal floating-point formats, operations, rounding, and exception behavior; implementations may realize them in hardware, software, or both [R-012]. The public IEEE record establishes the standard's scope, but the normative text was not accessed for this review, so this chapter stays introductory.

Common binary interchange formats include binary32 (often called single precision) and binary64 (often called double precision). Rust's `f64` is documented as the 64-bit binary64 type [R-016]. For the JVM, the official specification associates `float` and `double` with these formats and gives their format parameters; the table below reflects that binary32/binary64 layout [R-033]. These language/platform sources are cited for their own scope, not as a claim about every runtime.

| Format | Sign | Exponent | Fraction field | Total bits |
|---|---:|---:|---:|---:|
| binary32 | 1 | 8 | 23 | 32 |
| binary64 | 1 | 11 | 52 | 64 |

For normal finite binary32/binary64 values, the exponent is stored with a bias and the significand has an implicit leading 1. Subnormal values use a different interpretation near zero; special encodings represent signed zeros, infinities, and NaNs [R-033]. This is an overview, not the complete IEEE 754 specification.

### Worked example: why 0.1 is not exact in binary

The decimal fraction `1/10` has a terminating decimal expansion because 10 is composed of factors 2 and 5. A finite binary fraction has a denominator that is a power of 2. Since `1/10` has an irreducible factor 5 in its denominator, its binary expansion repeats and must be rounded to fit a finite significand. The stored binary floating-point number is therefore a nearby representable value, not exactly 0.1.

This is why a calculation such as `0.1 + 0.2` may produce a result close to, but not exactly equal to, `0.3`. The discrepancy is a property of finite representation and arithmetic, not evidence that the hardware malfunctioned. Comparisons and tolerances must be chosen for the domain; a universal epsilon is usually not a correct substitute for a numerical error analysis.

### Choosing a numeric representation

- Use an integer when the domain is an exact count within a known range.
- Use binary floating point for approximate real-valued computation when its error model is acceptable.
- Use fixed-scale integer or decimal arithmetic for values whose decimal rules require exact base-10 behavior, such as many currency calculations; define scale, rounding, overflow, and currency explicitly.
- Do not infer measurement accuracy from the number of stored digits.

Database systems often expose exact-decimal and approximate-floating types separately. Their semantics, ranges, rounding, and storage formats are product-specific and must be read from the relevant system documentation.

## 7. Text: characters, code points, and byte encodings

Text is not a sequence of abstract “characters” stored directly in a file. It passes through several layers:

1. **Grapheme cluster:** a sequence that a user-perceived text operation treats as one unit; one visible symbol may contain several code points.
2. **Unicode code point:** a numbered position in the Unicode codespace.
3. **Unicode scalar value:** a code point excluding the surrogate range reserved for UTF-16 mechanics.
4. **Encoding form:** a rule such as UTF-8 or UTF-16 that maps scalar values to code units.
5. **Encoded bytes:** the actual octets stored or transmitted by a particular byte serialization.

Unicode assigns code points and properties; an encoding form describes how values are represented in code units [R-014]. UTF-8 uses one to four bytes per scalar value for the current Unicode range and preserves the ASCII byte values for the ASCII subset, as specified by RFC 3629 [R-013]. UTF-8 is variable-width, so “one character equals one byte” is false for general text.

### Worked examples: UTF-8

- The ASCII character `A` (U+0041) encodes as one byte: `41` hexadecimal.
- `é` can be represented as the single scalar U+00E9, which UTF-8 encodes as `C3 A9`.
- The visible string `e` followed by a combining acute accent is U+0065 U+0301 and encodes as `65 CC 81`.

The latter two strings may render similarly but have different code-point sequences and bytes. Unicode normalization defines forms that transform canonically equivalent sequences into normalized forms [R-015]. A database that compares raw bytes, code points, normalized text, or locale-specific collation keys can produce different equality and ordering behavior. Normalization and collation must be chosen according to application semantics; compatibility normalization can intentionally fold distinctions, so it must not be treated as harmless cleanup.

### Rust's string types

In Rust, `str`/`String` contain valid UTF-8 text [R-016]. A `str` length is measured in bytes, not Unicode scalar values or user-perceived grapheme clusters. Rust `char` represents one Unicode scalar value; a grapheme can require multiple `char` values [R-016]. Therefore byte indexing, scalar iteration, and user-visible cursor movement are different operations.

```rust
fn main() {
    let text = "é";
    assert_eq!(text.len(), 2); // UTF-8 bytes
    assert_eq!(text.chars().count(), 1); // Unicode scalar values
    assert_eq!(text.as_bytes(), &[0xC3, 0xA9]);
}
```

The composed and decomposed forms can have different byte lengths and scalar counts even if they look the same. If a database key is intended to compare normalized text, that policy belongs in the format/schema/application contract; `String` alone does not impose it.

## 8. A programming-language value is not a portable storage format

An in-memory object can contain padding, alignment, pointers, implementation-specific enum layouts, or platform-dependent fields. A programming language may intentionally leave the default layout unspecified so compilers can optimize it. Copying raw memory bytes to disk can therefore create a format that changes across compiler versions, architectures, or builds. Pointers are especially unsuitable as persistent references because their addresses are meaningful only within a process's address space and lifetime.

Rust's default `repr(Rust)` layout is not a stable serialization contract: the Reference documents that type layout can change between compilations and documents only current guarantees [R-016]. `#[repr(C)]` exists for C-compatible layout needs, but even a C layout declaration does not automatically solve endianness, versioning, validation, pointer persistence, or long-term compatibility. Database formats should serialize fields explicitly or use a documented, versioned serialization format with well-defined semantics.

### Example: explicit fields instead of raw struct bytes

Suppose a record contains `id: u32` and `temperature_tenths: i16`. A format might specify six bytes: four bytes of `id` in little-endian order followed by two bytes of signed two's-complement `temperature_tenths` in little-endian order. The decoder must reject a truncated record and validate domain ranges after decoding. This specification is longer than “write the struct,” but another implementation can reproduce it without knowing the compiler's layout.

## 9. Database engineering implications

Representation choices affect more than storage size:

- **Comparison and ordering:** integer byte order may or may not preserve numeric order; text comparison depends on encoding and collation.
- **Index keys:** canonicalization and type semantics must match equality/range-query behavior.
- **Interoperability:** explicit encodings allow different languages and CPU architectures to exchange records.
- **Schema evolution:** version tags or migration rules distinguish old and new layouts.
- **Security:** unchecked lengths, invalid encodings, integer overflow, and parser disagreement can cause memory exhaustion, bounds errors, or inconsistent access checks.
- **Durability:** a persisted bit pattern is useful only if future readers can determine its encoding and meaning.
- **Compression:** representation redundancy affects compressibility, but access patterns, CPU, and random access also matter.

These consequences connect this foundations chapter to storage files, page serialization, index ordering, SQL types, recovery, and system interoperability later in the textbook.

## 10. Practical laboratory: inspect a value's representation

**Objective:** make the difference between a typed in-memory value and an explicit byte contract visible for integer, floating-point, and text values. **Prerequisites:** Sections 2–8, a language with fixed-width integer and byte-encoding APIs; Rust is recommended. **Environment:** record OS/architecture, compiler and version, language edition, and commands. For Rust, use Edition 2024 and the standard library only. **Inputs:** the values `0x12345678u32`, `-13i8`, `0.1f64`, precomposed `é` (U+00E9), and decomposed `e` + U+0301. **Safety boundary:** use a temporary program and synthetic values; do not inspect private or production database files.

1. Print or record the declared type and explicit little- and big-endian byte arrays for `0x12345678u32`.
2. Record the two's-complement bit pattern for `-13i8`; explain why it is a typed interpretation rather than a universal signed-byte contract.
3. Display the `f64::to_bits()` hexadecimal pattern for `0.1`, and explain that the pattern is a binary64 representation under the target language's documented behavior, not a decimal encoding.
4. For each text string, record UTF-8 bytes, `String::len()` or equivalent byte length, Unicode scalar count, and (if a grapheme library is deliberately added) grapheme count. Do not add dependencies merely to claim grapheme behavior.
5. Define an explicit decoder contract for a six-byte record, including version, field order, endianness, valid ranges, truncation behavior, and unknown-version behavior. No unsafe raw-struct serialization is allowed.

**Expected checkpoints:** integer bytes `[78,56,34,12]` little-endian and `[12,34,56,78]` big-endian; `-13i8` has bits `11110011`; precomposed `é` is UTF-8 `C3 A9` (2 bytes, 1 scalar), decomposed `e` + U+0301 is `65 CC 81` (3 bytes, 2 scalars). The floating point bits depend on type/format; the lab must state `f64` and its environment. **Verification:** compare explicit expected bytes, run the program twice on the same toolchain, and include source and command output. **Deliverable:** short report, code, environment record, test output, and a format-specification table. **Assessment:** E7 rubric below (15 points); no performance claim is expected.

### Exercises

Worked calculations, sample answers, and grading guidance are in the [separate solution guide](solutions/V01-C04-solutions.md). Unless a task says otherwise, show intermediate steps and state assumptions.

| ID | Level | Objectives | Prerequisites | Deliverable and assessment |
|---|---|---|---|---|
| C04-E1 — Convert a bit pattern | 1–2 | 1 | Positional notation | Convert `1011_0010₂` to decimal and hexadecimal. Show place-value work. 10 points: decimal 4, hexadecimal 3, trace/notation 3. |
| C04-E2 — Fixed-width ranges | 2 | 2 | Powers of two | Give unsigned and two's-complement ranges for 12 bits and explain endpoint derivation. 10 points: unsigned range 4, signed range 4, derivation 2. |
| C04-E3 — Encode a negative value | 2 | 2 | E1–E2 | Encode −13 in 8-bit two's complement and verify by weighted interpretation. 10 points: inversion/add-one or modular derivation 4, bit pattern 3, verification 3. |
| C04-E4 — Byte-order contract | 2–3 | 3, 7 | Hexadecimal; byte grouping | Give byte sequences for `0xA1B2C3D4` in both orders and specify decoder assumptions. 10 points: each order 3, width/order/type contract 4. |
| C04-E5 — Floating-point limits | 3 | 4, 7 | Rational numbers; powers of two | Explain why `1/10` has no terminating binary expansion; compare an approximate domain with one requiring exact decimal semantics. 15 points: denominator proof 6, appropriate cases 4, error/rounding caveat 5. |
| C04-E6 — Text equality layers | 3–4 | 5, 7 | Unicode layers in Section 7 | Explain the distinct UTF-8 bytes for precomposed/decomposed forms; assess a proposed normalization policy and state at least one thing it does not guarantee. 15 points: byte/codepoint analysis 6, policy reasoning 5, limitation 4. |
| C04-E7 — Rust representation audit lab | 4–5 | 3–7 | Basic Rust; sections 5–8 | Run or carefully trace all lab checkpoints; submit environment, integer/signed/text/float observations, a six-byte decoder contract, and verification output. 15 points: reproducibility 2, integer/signed outputs 3, text outputs and distinctions 3, typed float observation 2, record/error contract 3, limitations 2. |
| C04-E8 — Versioned record design | 4–5 | 6, 7 | Integer encoding, validation, byte order | Design a six-byte measurement record with version, field widths/types, byte order, valid range, and malformed-input behavior. Deliver a format table and two valid/invalid test vectors. 15 points: unambiguous format 5, validation/errors 4, portability/version 3, test vectors 3. |

**Permitted tools:** calculator for E1–E6; any language/compiler for E7, with versions stated. For E8, pseudocode or a precise decoder state table is sufficient. Alternatives receive full credit when assumptions and compatibility implications are explicit. Rubrics are point-based as shown; the solution guide gives expected outputs and common failure modes.

## 11. Common misconceptions

### “A byte is the same thing as a character.”

Bytes are encoded storage units. Text characters may use multiple bytes, and a user-perceived grapheme may consist of multiple Unicode scalar values.

### “A signed integer just adds a sign bit.”

Two's complement assigns a specific value to every bit pattern and has an asymmetric range. The encoding and arithmetic rules must be understood together.

### “A floating-point value is a real number stored exactly.”

Most real numbers cannot be represented in finite precision. IEEE formats define rounded approximations and special values.

### “The same Rust struct has the same bytes everywhere.”

Default Rust layout is not a stable file format; pointers, padding, endianness, and compiler/layout rules make raw memory unsuitable as a portable database encoding.

### “UTF-8 makes text equality and sorting universal.”

UTF-8 defines byte encoding of scalar values. Normalization, case handling, locale collation, and domain-specific equality remain separate choices.

## 12. Summary

Bits acquire meaning only through an encoding and type context. Binary and hexadecimal notation expose bit patterns; unsigned and two's-complement integers assign numeric values to fixed-width patterns; endianness orders bytes for multi-byte values. Floating-point formats represent many real values approximately. Unicode separates code points from encoding, UTF-8 maps scalar values into variable-length byte sequences, and user-perceived text units may span several scalar values.

Programming-language types help programs interpret and constrain values, but in-memory layout is not automatically a portable, durable database format. A persistent representation must define field encoding, byte order, framing, validity, versioning, and interpretation. Those decisions affect interoperability, indexes, correctness, security, and future readability.

## Further reading

- IEEE Std 754-2019 for floating-point formats and arithmetic semantics.
- RFC 3629 and the Unicode Standard for UTF-8; UAX #15 for normalization forms.
- Rust Reference: types and layout; standard library documentation for `char` and `str`.
- V01-C05 for files, memory, and persistence; Volume V for database pages and serialization.

## References

1. **R-012 — Standard.** IEEE. (2019). *IEEE Standard for Floating-Point Arithmetic (IEEE 754-2019).* [Official standard page](https://standards.ieee.org/ieee/754/6210/). The page identifies the active standard and its scope; the full normative text requires access. Rechecked 2026-11-14.
2. **R-033 — Official platform specification.** Oracle. *The Java Virtual Machine Specification, Java SE 26*, §2.3.2, Floating-Point Types and Values. [Official specification](https://docs.oracle.com/en/java/javase/26/docs/specs/jvms/jvms-2.html#jvms-2.3.2). Accessed 2026-11-14 for the JVM's binary32/binary64 association and format parameters. It is an implementation-platform specification, not a substitute for the IEEE normative text.
3. **R-013 — Standard.** Yergeau, F. (2003). RFC 3629, “UTF-8, a transformation format of ISO 10646.” [IETF RFC Editor](https://www.rfc-editor.org/rfc/rfc3629). Standards-track specification for UTF-8 octet sequences, rechecked 2026-11-14.
4. **R-014 — Official standard.** Unicode Consortium. *The Unicode Standard*, Chapter 1, “Introduction.” [Current chapter](https://www.unicode.org/versions/latest/ch01.pdf). Official description of code points and encoding forms; accessed 2026-11-14 (Unicode 18.0.0 page).
5. **R-015 — Normative annex.** Unicode Consortium. Unicode Standard Annex #15, “Unicode Normalization Forms,” Revision 58 / Unicode 18.0.0. [UAX #15](https://www.unicode.org/reports/tr15/). Defines normalization forms; accessed 2026-11-14.
6. **R-016 — Official language/library documentation.** The Rust Project. [The Rust Reference: Types](https://doc.rust-lang.org/reference/types.html), [Type Layout](https://doc.rust-lang.org/reference/type-layout.html), [Operator Expressions](https://doc.rust-lang.org/reference/expressions/operator-expr.html), and standard library [`char`](https://doc.rust-lang.org/std/primitive.char.html), [`str`](https://doc.rust-lang.org/std/primitive.str.html), and [`f64`](https://doc.rust-lang.org/std/primitive.f64.html) documentation. Relevant pages rechecked 2026-11-14; library docs identify Rust 1.100.0 where versioned.

## Earlier draft review

The 2026-11-11 draft inspection checked selected conversion, byte-order, signed-integer, UTF-8, and Rust string examples. It was not an acceptance review under the later measurable policy; no exercise rubrics or separate solutions were then recorded. IEEE normative text was not accessed. This historical note does not establish COMPLETED status.

## Chapter acceptance record

- **Status:** COMPLETED.
- **Review date:** 2026-11-14 (Asia/Bangkok).
- **Reviewer:** Internal technical/editorial review; no independent external peer review is implied.
- **Objectives and assessment:** All seven objectives map to E1–E8 and the laboratory. Each exercise records level, prerequisites, deliverables, expected results, and a point rubric; separate solutions are in `solutions/V01-C04-solutions.md`.
- **Technical claim/source review:** Binary32/binary64 field claims map to R-033's official Java SE 26 specification section and IEEE scope to R-012; RFC 3629 R-013, Unicode R-014/R-015, and Rust R-016 support text/layout/API/overflow details within their stated scopes. The IEEE normative text was not accessed and this limit is explicit. Claims are not generalized across products without evidence.
- **Mathematics and examples:** Binary/hex conversion, integer ranges, two's-complement representation, endianness, rational termination argument, binary64 bit pattern, UTF-8 byte sequences, and Rust length/scalar behavior were independently checked.
- **Code and environment:** Both Rust code examples were compiled and executed separately with `rustc 1.97.1 (8bab26f4f 2026-07-14)`, `--edition=2024`; assertions passed. The code does not serialize a struct's raw memory. These snippet checks do not test a database implementation.
- **Laboratory:** Inputs, environment record, ordered tasks, expected byte checkpoints, verification, deliverable, limitations, and assessment criteria are explicit. No benchmark claim is made.
- **Navigation and glossary:** Prerequisite/next links, stable ID, solution link, glossary, index, reference IDs, and chapter navigation status checked.
- **Diagrams:** Not applicable; the tables and explicit byte sequences show representation differences more precisely than a general architecture diagram would.
- **Editorial review:** Terminology layers (value/type/encoding, code point/scalar/grapheme), assumptions, normalization risks, overflow boundaries, decoder validation, and portability implications were reviewed.
- **Validation:** Repository-wide Markdown structure/link checks and C04-specific objective/source/status checks recorded in QR-011. No Cargo workspace exists, so `cargo fmt/check/test/clippy` do not apply. `git diff --check` passes.
- **Limitations:** The IEEE normative document remains paywalled/unaccessed; R-033 is a scoped official-platform cross-check for binary32/64 widths. Unicode collation algorithms and comprehensive floating-point error analysis are deferred.
