# V01-C04 — Worked Solutions and Grading Guidance

This file accompanies [V01-C04 — Bits, Bytes, Types, and Encodings](../V01-C04-bits-bytes-types-encodings.md). Other formats and examples can receive full credit when they state the encoding contract unambiguously and justify the decisions.

## C04-E1 — Convert a bit pattern

`1011_0010₂ = 128 + 32 + 16 + 2 = 178₁₀`. Grouping into nibbles gives `1011 0010₂ = B2₁₆`. Thus `0xB2 = 11·16 + 2 = 178`.

**Rubric:** decimal 4, hexadecimal 3, place-value/nibble trace 3.

## C04-E2 — Fixed-width ranges

An unsigned `n`-bit integer ranges from 0 to `2ⁿ−1`, so for 12 bits the range is 0 through 4095. Two's-complement signed range is `−2ⁿ⁻¹` through `2ⁿ⁻¹−1`, so 12 bits represents −2048 through 2047. The asymmetry is because there is one additional negative value.

**Rubric:** unsigned range 4, signed range 4, derivation 2.

## C04-E3 — Encode a negative value

`13 = 00001101₂`. Invert: `11110010₂`; add one: `11110011₂`. Interpreting the top bit as a negative two's-complement contribution gives `−128+64+32+16+2+1 = −13`. Equivalently, the unsigned pattern is 243 and `243−256=−13`.

**Rubric:** encoding derivation 4, correct pattern 3, verification 3.

## C04-E4 — Byte-order contract

The four bytes of `0xA1B2C3D4` are `A1 B2 C3 D4` big-endian and `D4 C3 B2 A1` little-endian. The decoder needs at least the field width (32 bits), whether the bytes represent an integer and which signed interpretation applies, and the byte-order rule. A record format additionally needs field boundaries/version and invalid-input behavior.

**Rubric:** big-endian 3, little-endian 3, decoder contract 4.

## C04-E5 — Floating-point limits

`1/10` is already reduced, with denominator 10 containing factor 5. A rational has a terminating base-2 expansion only when its reduced denominator has no prime factors other than 2; repeated multiplication by 2 can never eliminate the factor 5. Thus binary floating point stores a rounded nearby value. Approximate scientific measurements or graphics calculations may use binary floating point when an error budget is acceptable. Currency accounting may require fixed-scale integers or decimal arithmetic with explicit currency, scale, rounding, and overflow rules. Neither type alone guarantees domain correctness.

**Rubric:** denominator proof 6, justified application examples 4, error/rounding caveat 5.

## C04-E6 — Text equality layers

Precomposed U+00E9 is UTF-8 `C3 A9`; decomposed U+0065 U+0301 is `65 CC 81`. They have different scalar sequences and bytes although many render similarly. A system may normalize canonically equivalent text before equality or key construction, but the exact normalization form, Unicode version, and collation/case rules must be part of the contract. Normalization does not prove that strings are linguistically identical, solve locale-aware ordering, or make compatibility distinctions safe to discard. Identifier domains may need to preserve distinctions that compatibility normalization would fold.

**Rubric:** byte/scalar analysis 6, policy reasoning 5, limitation 4.

## C04-E7 — Rust representation audit lab

An acceptable result for the integer is little-endian bytes `[0x78, 0x56, 0x34, 0x12]` and big-endian bytes `[0x12, 0x34, 0x56, 0x78]`. The signed 8-bit representation of −13 is `11110011`. Precomposed `é` has UTF-8 `[0xC3,0xA9]`, byte length 2, scalar count 1; decomposed `e` plus U+0301 has `[0x65,0xCC,0x81]`, byte length 3, scalar count 2. `f64::to_bits()` should be reported from the declared toolchain rather than guessed from formatted decimal output. A valid decoder contract rejects short records and unknown versions before reading fields and validates numeric ranges before use. The report must distinguish measured tool output from an architecture-independent format specification.

**Rubric:** reproducibility 2, integer/signed outputs 3, text outputs 3, float observation 2, record/error contract 3, limitation statement 2.

## C04-E8 — Versioned record design

One valid design is six octets: byte 0 is version `1`; bytes 1–2 are an unsigned 16-bit measurement ID in big-endian order; bytes 3–5 are a signed 24-bit two's-complement integer in big-endian order, measured in tenths of a degree Celsius. The ID range is 0–65535 and the signed field range is −8,388,608 through 8,388,607 tenths. Decoder behavior: reject lengths other than 6; reject unknown version; parse fields only after these checks; validate application-specific ID and temperature bounds; reject rather than wrap on out-of-range values. A test vector for version 1, ID `0x1234`, temperature −13 tenths has bytes `01 12 34 FF FF F3`; a truncated five-byte vector must be rejected. Other field designs are valid when six-octet length, signed encoding, scale/unit, version, and errors are defined.

**Rubric:** unambiguous format 5, validation/errors 4, portability/version 3, valid and invalid test vectors 3.

## Laboratory self-checks

For Rust, `0x1234_5678u32.to_le_bytes()` yields `[0x78,0x56,0x34,0x12]`, while `.to_be_bytes()` yields `[0x12,0x34,0x56,0x78]`. `(-13i8) as u8` is `0xF3` under the specified cast semantics; use a bit-formatting step if displaying eight bits. The `f64` bit pattern must be captured from the stated compiler/platform, and the explanation should identify it as a binary format. Text expected byte/scalar counts are listed under E7. The lab's six-byte serialization contract is a design deliverable; it must not rely on raw struct layout.
