# V01-C03 — Worked Solutions and Grading Guidance

This file accompanies [V01-C03 — Information Theory](../V01-C03-information-theory-entropy-coding.md). These are model answers; equivalent derivations and code assignments receive credit when correct and their assumptions are explicit.

## C03-E1 — Self-information

`I(1) = -log₂1 = 0` bits; `I(1/2)=1` bit; `I(1/4)=2` bits; `I(1/16)=4` bits. These values are assigned by the stipulated probability model, not by a universal psychological measure of surprise.

**Rubric:** 6 points for four correct values, 4 for units and model-relative interpretation.

## C03-E2 — Entropy boundary cases

- `(1/2,1/2)`: `H=1` bit.
- `(3/4,1/4)`: `H₂(1/4)=-(3/4)log₂(3/4)-(1/4)log₂(1/4)≈0.811278` bits.
- `(1,0)`: `H=0` bits because the sole possible outcome is certain. The zero-probability term is defined by continuity: `lim(p→0+) p log₂p=0`.

**Rubric:** calculations 9, boundary explanation 3, units 3.

## C03-E3 — Huffman construction

Merge `C(1/8)` with `D(1/8)`, yielding `CD(1/4)`. Merge `B(1/4)` with `CD(1/4)`, yielding `BCD(1/2)`. Merge `A(1/2)` with `BCD(1/2)`. One resulting code is `A=0, B=10, C=110, D=111`. No complete codeword is a prefix of another: the root's `0` branch terminates at A, while the `1` branch continues; on that branch `10` terminates at B and `11` continues to C or D. Its expected length is `(1/2)1+(1/4)2+(1/8)3+(1/8)3=1.75` bits/symbol, equal to entropy for this dyadic distribution.

**Rubric:** merge trace 6, code 5, prefix argument 3, expected length and entropy comparison 6.

## C03-E4 — Joint distribution

The marginal for each variable is `(1/2,1/2)`, so `H(X)=H(Y)=1` bit. For either value of X, Y has conditional probabilities `(3/4,1/4)` up to outcome order, so `H(Y|X)=H₂(1/4)≈0.811278`. The chain rule gives `H(X,Y)=H(X)+H(Y|X)≈1.811278` bits. Then `I(X;Y)=H(Y)-H(Y|X)≈0.188722` bits. A direct joint calculation is `-2(3/8 log₂(3/8))-2(1/8 log₂(1/8))≈1.811278`.

**Rubric:** marginals 4, conditional entropy 5, joint/chain check 5, mutual information 4, units 2.

## C03-E5 — Meaning versus uncertainty

Examples: a random diagnostic nonce that is discarded and never identifies or affects anything can have high entropy but no business meaning; a Boolean `is_emergency_stop_active` may be almost always false (low entropy) but operationally critical when true. State the system context, observation window, and assumed distribution. Credit other examples when the semantic claim is justified rather than inferred from entropy.

**Rubric:** examples 4, distinction 3, assumptions 3.

## C03-E6 — Association and decisions

Mutual information establishes statistical dependence under a joint distribution; it does not establish that the attribute causes churn. A confounder (for example, account tenure) or selection in which customers are observed could produce the association. Even a predictive field does not automatically justify retaining it: purpose limitation, privacy, fairness, consent, retention, and whether the feature changes a defensible decision must be considered. Any recommendation needs a stated population, measurement process, and decision objective.

**Rubric:** dependence/causation distinction 4, alternative explanation 3, decision limits 3.

## C03-E7 — Column compression lab

For `AAAAAABBAABBCCDD`, counts are A=8, B=4, C=2, D=2 out of 16. Thus probabilities are `(1/2,1/4,1/8,1/8)` and entropy is `1.75` bits/symbol. Applying `A=0, B=10, C=110, D=111` emits 8+8+6+6=28 payload bits. Two-bit fixed width uses 32 payload bits. This excludes codebook and framing overhead. A proper measurement report should choose a concrete format and record total bytes, encoding/decoding time, workload (scan, point lookup, update), metadata, page effects, and hardware/software versions; theoretical payload arithmetic alone is not benchmark evidence.

**Rubric:** counts/entropy 4, payload counts 3, overhead/workload plan 4, limitations/reproducibility 4. Alternative well-specified experiment designs are acceptable.
