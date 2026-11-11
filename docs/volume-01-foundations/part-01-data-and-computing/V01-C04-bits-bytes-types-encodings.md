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

## 1. From values to bit patterns

Computers store and move information using physical states that software treats as binary digits. A **bit** takes one of two abstract values, conventionally written `0` and `1`. A **byte** is a group of eight bits in the common modern usage. Some older systems used different byte sizes, so standards and format specifications must state the unit they mean. In this book, unless stated otherwise, a byte is eight bits.

A sequence of bits does not announce what it represents. The same eight bits might be interpreted as an unsigned integer, a signed integer, a character code fragment, part of a floating-point number, or flags. An **encoding** assigns bit patterns to values in a defined system. A **type** constrains how a program interprets and operates on values; it does not by itself define a durable interchange format.

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

Signed overflow is a correctness boundary. Some programming languages define wraparound in some contexts; others may trap, reject, or make some overflow behavior undefined. Rust's ordinary integer operations, checked/saturating/wrapping variants, and build modes must be understood from the language documentation for the specific operation. Database code should make the intended behavior explicit, especially for counters, page offsets, lengths, transaction identifiers, and arithmetic in constraints.

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
let value: u32 = 0x1234_5678;
let bytes: [u8; 4] = value.to_le_bytes();
assert_eq!(bytes, [0x78, 0x56, 0x34, 0x12]);

let restored = u32::from_le_bytes(bytes);
assert_eq!(restored, value);
```

The point is not that little-endian is universally preferred. The format must choose and document a convention, and both encoder and decoder must follow it. A storage format also needs versioning, field boundaries, validation, and checks for corrupt or truncated inputs.

## 6. Floating-point values: a finite approximation to a real number

The real numbers are unbounded and have arbitrary precision. A fixed-width computer format has finitely many bit patterns, so it cannot represent every real number exactly. Binary floating-point formats represent a finite subset using a sign, exponent, and significand (often informally called a fraction or mantissa). IEEE 754 specifies binary and decimal floating-point formats, operations, rounding, and exception behavior; implementations may realize them in hardware, software, or both.

Common binary interchange formats include binary32 (often called single precision) and binary64 (often called double precision). Their usual field widths are:

| Format | Sign | Exponent | Fraction field | Total bits |
|---|---:|---:|---:|---:|
| binary32 | 1 | 8 | 23 | 32 |
| binary64 | 1 | 11 | 52 | 64 |

For normal finite values, the exponent is stored with a bias and the significand has an implicit leading 1. Subnormal values use a different interpretation near zero; special encodings represent signed zeros, infinities, and NaNs. This is an overview, not the complete IEEE 754 specification.

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

Unicode assigns code points and properties; an encoding form describes how values are represented in code units. UTF-8 uses one to four bytes per scalar value for the current Unicode range and preserves the ASCII byte values for the ASCII subset. UTF-8 is variable-width, so “one character equals one byte” is false for general text.

### Worked examples: UTF-8

- The ASCII character `A` (U+0041) encodes as one byte: `41` hexadecimal.
- `é` can be represented as the single scalar U+00E9, which UTF-8 encodes as `C3 A9`.
- The visible string `e` followed by a combining acute accent is U+0065 U+0301 and encodes as `65 CC 81`.

The latter two strings may render similarly but have different code-point sequences and bytes. Unicode normalization defines forms that transform canonically equivalent sequences into normalized forms. A database that compares raw bytes, code points, normalized text, or locale-specific collation keys can produce different equality and ordering behavior. Normalization and collation must be chosen according to application semantics; normalizing indiscriminately can also erase distinctions relevant to compatibility or identifiers.

### Rust's string types

In Rust, `str`/`String` contain valid UTF-8 text. A `str` length is measured in bytes, not Unicode scalar values or user-perceived grapheme clusters. Rust `char` represents one Unicode scalar value; a grapheme can require multiple `char` values. Therefore byte indexing, scalar iteration, and user-visible cursor movement are different operations.

```rust
let text = "é";
assert_eq!(text.len(), 2); // UTF-8 bytes
assert_eq!(text.chars().count(), 1); // Unicode scalar values
assert_eq!(text.as_bytes(), &[0xC3, 0xA9]);
```

The composed and decomposed forms can have different byte lengths and scalar counts even if they look the same. If a database key is intended to compare normalized text, that policy belongs in the format/schema/application contract; `String` alone does not impose it.

## 8. A programming-language value is not a portable storage format

An in-memory object can contain padding, alignment, pointers, implementation-specific enum layouts, or platform-dependent fields. A programming language may intentionally leave the default layout unspecified so compilers can optimize it. Copying raw memory bytes to disk can therefore create a format that changes across compiler versions, architectures, or builds. Pointers are especially unsuitable as persistent references because their addresses are meaningful only within a process's address space and lifetime.

Rust's default `repr(Rust)` layout is not a stable serialization contract. `#[repr(C)]` exists for C-compatible layout needs, but even a C layout declaration does not automatically solve endianness, versioning, validation, pointer persistence, or long-term compatibility. Database formats should serialize fields explicitly or use a documented, versioned serialization format with well-defined semantics.

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

Using a language of your choice, inspect one integer, one negative signed integer, one approximate decimal fraction, and one non-ASCII string. Record:

1. The abstract value and declared type.
2. The bytes produced by an explicitly chosen encoding.
3. Whether the encoding is architecture-independent.
4. How decoding detects invalid or truncated input.
5. Which assumptions would need to be added before storing the bytes as a database record.

In Rust, compare `to_le_bytes()` and `to_be_bytes()` for a multi-byte integer; inspect `str::as_bytes()` for composed and decomposed text; and compare `len()` with `chars().count()`. Do not use raw struct memory as the serialization experiment.

### Exercises

1. Convert `1011_0010₂` to decimal and hexadecimal. Show the place-value calculation.
2. Find the range of an unsigned 12-bit integer and a signed 12-bit two's-complement integer.
3. Represent −13 in 8-bit two's complement. Verify by interpreting its bit pattern.
4. Write the bytes of `0xA1B2C3D4` in big-endian and little-endian order. Explain what a decoder must know.
5. Explain why binary floating point cannot exactly represent `1/10` using the denominator argument. Give one domain where approximate arithmetic is appropriate and one where exact decimal semantics may be required.
6. Explain how two visually identical strings can have different UTF-8 bytes. Describe when normalization may help and what it cannot guarantee.
7. In Rust, explain why `String::len()` may differ from both `chars().count()` and a user's perceived character count.
8. Design a versioned six-byte record format for a small measurement. Specify field types, byte order, valid ranges, and malformed-input behavior.

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

1. IEEE. (2019). *IEEE Standard for Floating-Point Arithmetic (IEEE 754-2019).* [Official standard page](https://standards.ieee.org/ieee/754/6210/). The page identifies the active standard and its scope; the full normative text requires access. Accessed 2026-11-11.
2. Yergeau, F. (2003). RFC 3629, “UTF-8, a transformation format of ISO 10646.” [IETF RFC Editor](https://www.rfc-editor.org/rfc/rfc3629). Standards-track specification for UTF-8 byte sequences, accessed 2026-11-11.
3. Unicode Consortium. *The Unicode Standard*, Chapter 1, “Introduction.” [Current chapter PDF](https://www.unicode.org/versions/latest/ch01.pdf). Official description of code points and encoding forms, accessed 2026-11-11.
4. Unicode Consortium. Unicode Standard Annex #15, “Unicode Normalization Forms,” Revision 58 / Unicode 18.0.0. [UAX #15](https://www.unicode.org/reports/tr15/). Defines normalization forms; accessed 2026-11-11.
5. The Rust Project. [The Rust Reference: Types](https://doc.rust-lang.org/reference/types.html), [Type Layout](https://doc.rust-lang.org/reference/type-layout.html), and standard library [`char`](https://doc.rust-lang.org/std/primitive.char.html) and [`str`](https://doc.rust-lang.org/std/primitive.str.html) documentation. Official language/library behavior; accessed 2026-11-11.

## Chapter review record

- Technical examples: binary/hex conversion, integer ranges, two's-complement negation, byte order, UTF-8 byte sequences, and Rust byte/scalar counts reviewed against cited specifications/docs.
- Scope: IEEE 754 details are introductory; no complete floating-point numerical analysis or Unicode collation algorithm is claimed.
- Engineering example: explicit Rust byte conversion shown; raw struct layout explicitly rejected as an unversioned persistence contract.
- References: IEEE 754, RFC 3629, Unicode/UAX #15, and Rust primary/official references are indexed; normative full IEEE text was not accessed and the chapter's claims remain within reviewed source scope.
- Validation: repository-wide local targets/anchors checked with zero failures. Binary/hex, endianness, signed value, UTF-8 byte sequences, entropy and Huffman worked values recalculated independently. Display-math delimiters are balanced.
- Acceptance: applicable chapter criteria met; no runnable code project is required at this foundational stage.
