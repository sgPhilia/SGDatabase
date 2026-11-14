# V01-C03 — Information Theory: Uncertainty, Entropy, and Coding

**Volume:** I — Data, Computing, and Database Foundations
**Part:** I — Data and Computing
**Status:** COMPLETED
**Prerequisites:** [V01-C01 — What Is Data?](V01-C01-what-is-data.md); [V01-C02 — Meaning, Models, and Information](V01-C02-meaning-models-information.md); basic algebra and probability notation reviewed below
**Next:** [V01-C04 — Bits, Bytes, Types, and Encodings](V01-C04-bits-bytes-types-encodings.md)
**Glossary:** [Canonical terms](../../../glossary.md)
**Concept index:** [Index](../../../INDEX.md)

This chapter is under focused review. Exercise solutions are provided in the [separate solution guide](solutions/V01-C03-solutions.md); status remains IN PROGRESS until the acceptance record is complete.

## Learning objectives

After this chapter, you should be able to:

1. Define self-information and Shannon entropy for a discrete random variable.
2. Compute entropy from a probability distribution and interpret its units.
3. Derive conditional entropy, mutual information, and the chain rule for a small example.
4. Explain why an optimal lossless code has average length at least the source entropy.
5. Construct and evaluate a Huffman code for a small source.
6. Distinguish uncertainty, semantic meaning, data quality, privacy, and value to a user.
7. Explain why entropy is a useful but limited lens on database compression and storage.

| Objective | Assessment evidence |
|---|---|
| 1 | E1–E2; definitions and boundary cases in Sections 3–4 |
| 2 | E1–E4; numerical entropy calculations and units |
| 3 | E4; derivation and worked joint-distribution example in Sections 5–6 |
| 4 | E3; code-length bound derivation in Section 7 |
| 5 | E3; Huffman trace, pseudocode, cost, and optimality sketch in Section 7.1 |
| 6 | E5–E6; distinctions and decision limits in Section 8 |
| 7 | E7; reproducible payload experiment and engineering caveats in Sections 8–9 |

## 1. Why quantify uncertainty?

Suppose a device emits one of four symbols. In one operating mode it emits each symbol with equal probability. In another mode it emits `A` 97% of the time and the others share the remaining 3%. Both modes have four possible symbols. Yet the second source is easier to predict and may be encoded with fewer bits on average if its probability pattern is known.

Information theory provides mathematical tools for measuring uncertainty in such probabilistic sources and analyzing representations and communication. Claude Shannon's 1948 work connected source statistics to coding and communication limits. The focus is not the human interpretation of the symbol. A source can be statistically predictable while carrying important meaning, or statistically unpredictable while carrying none.

This chapter develops the discrete, finite-alphabet case used in introductory coding examples. Continuous variables, noisy-channel coding, and advanced information measures are noted but not derived fully.

## 2. Probability model and notation

A **random variable** is a mathematical description of an outcome whose value is not known in advance. Let `X` take values from a finite set `𝒳 = {x₁, …, xₙ}` with probabilities `pᵢ = P(X = xᵢ)`. Probabilities satisfy:

$$
0 \le p_i \le 1, \qquad \sum_{i=1}^{n} p_i = 1.
$$

The model is a description of a source or experiment. It may be estimated from observed data, stipulated for an exercise, or derived from an application. A calculation is only as representative as the probability model. If the source changes over time or the measured sample is biased, an entropy computed from the model may poorly describe future data.

Unless otherwise stated, logarithms use base 2. Information is then measured in **bits**. A bit is the information unit associated with a binary choice under this mathematical convention; physical storage systems group bits into bytes and larger units, which Volume I-C04 will cover.

## 3. Self-information: surprise of one outcome under a model

For an outcome `x` with probability `p(x) > 0`, define its self-information as:

$$
I(x) = -\log_2 p(x) = \log_2\!\left(\frac{1}{p(x)}\right).
$$

An outcome assigned probability 1 has self-information 0 bits: under the model it was certain. An outcome with probability 1/2 has 1 bit; an outcome with probability 1/8 has 3 bits. Less probable outcomes receive larger values.

This quantity is not a universal measure of how surprising a person feels. It is defined relative to a probability model. If the model says a rare event is common, or misses a relevant condition, the numerical result may fail to reflect an observer's expectations.

### Worked example 1: a biased coin

Let `P(H) = 3/4` and `P(T) = 1/4`. Then:

- `I(H) = -log₂(3/4) ≈ 0.415` bits.
- `I(T) = -log₂(1/4) = 2` bits.

The rare outcome has more self-information under this model. A single realized outcome has a self-information value; entropy, next, is its expected value before the outcome is known.

## 4. Entropy: expected self-information

The Shannon entropy of a finite discrete random variable `X` is:

$$
H(X) = -\sum_{x \in \mathcal{X}} p(x)\log_2 p(x),
$$

with the convention `0 log₂ 0 = 0`, justified by the limiting value as `p` approaches zero. Equivalently, entropy is the expected self-information:

$$
H(X) = \mathbb{E}[I(X)].
$$

Entropy measures uncertainty in the distribution, not the identity of a realized value. If an event has already been observed and is known, there may be no uncertainty about that event for the observer, even though the source distribution had positive entropy before observation.

### 4.1 Two-outcome source

For a binary variable with `P(1)=p` and `P(0)=1-p`,

$$
H_2(p) = -p\log_2 p -(1-p)\log_2(1-p).
$$

At `p=1/2`, both outcomes are equally likely and `H₂(1/2)=1` bit. At `p=1`, the outcome is certain and entropy is 0. The curve is symmetric around one half and reaches its maximum there.

### 4.2 Uniform and skewed distributions

For `n` equally likely outcomes, `pᵢ=1/n`, so:

$$
H(X) = -\sum_{i=1}^{n}\frac{1}{n}\log_2\frac{1}{n}=\log_2 n.
$$

For four equally likely symbols the entropy is 2 bits per symbol. If probabilities are `(0.97, 0.01, 0.01, 0.01)`, the entropy is approximately 0.242 bits per symbol. Although a fixed-length code for four distinct symbols needs 2 bits per symbol, a variable-length code can assign a short code to the common symbol and longer codes to rare ones, lowering the average length across a long sequence.

The finite-alphabet entropy bounds are:

$$
0 \le H(X) \le \log_2 |\mathcal{X}|.
$$

The lower bound occurs when one outcome has probability 1. The upper bound occurs for a uniform distribution. This upper bound assumes a finite set with `|𝒳|` distinguishable outcomes.

#### Why the uniform distribution maximizes entropy

Let `u(x)=1/n` be the uniform distribution on the same `n` outcomes. The Kullback–Leibler divergence from `p` to `u` is

$$
D_{2}(p\Vert u)=\sum_x p(x)\log_2\frac{p(x)}{u(x)}
=\log_2 n-H(X).
$$

The log-sum inequality implies `D₂(p||u) ≥ 0`, with equality only when `p=u` on the support. Therefore `H(X) ≤ log₂ n`, with equality at the uniform distribution. This short derivation relies on a standard inequality from probability/information theory; the chapter does not prove the log-sum inequality itself.

### Worked example 2: a four-symbol source

Let `P(A)=1/2`, `P(B)=1/4`, `P(C)=1/8`, and `P(D)=1/8`. Then:

$$
\begin{aligned}
H(X) &= -\left(\frac12\log_2\frac12 + \frac14\log_2\frac14
 + \frac18\log_2\frac18 + \frac18\log_2\frac18\right)\\
&= \frac12(1)+\frac14(2)+\frac18(3)+\frac18(3)\\
&= 1.75\text{ bits per symbol.}
\end{aligned}
$$

The number 1.75 is an average over the modeled source, not the number of bits in every encoded symbol. A particular codeword may have length 1, 2, or 3 bits.

## 5. Multiple variables: joint and conditional entropy

For two discrete variables `X` and `Y`, their **joint entropy** is:

$$
H(X,Y) = -\sum_{x,y} p(x,y)\log_2 p(x,y).
$$

It measures uncertainty about the pair. The **conditional entropy** is:

$$
H(Y\mid X) = -\sum_{x,y}p(x,y)\log_2 p(y\mid x),
$$

where terms with zero joint probability contribute zero. It measures the remaining average uncertainty about `Y` after `X` is known under the same joint distribution.

The chain rule states:

$$
H(X,Y)=H(X)+H(Y\mid X)=H(Y)+H(X\mid Y).
$$

This follows by writing `p(x,y)=p(x)p(y|x)` in the logarithm and expanding the sum. The rule is useful because a sequence can be encoded by describing one variable and then describing what remains given the earlier variables.

In detail:

$$
\begin{aligned}
H(X,Y)
&=-\sum_{x,y}p(x,y)\log_2[p(x)p(y\mid x)]\\
&=-\sum_{x,y}p(x,y)\log_2p(x)
  -\sum_{x,y}p(x,y)\log_2p(y\mid x)\\
&=H(X)+H(Y\mid X).
\end{aligned}
$$

The first term reduces to `H(X)` because summing the joint probabilities over `y` gives the marginal `p(x)`.

### Worked example 3: correlated device status

Suppose a monitoring system records a warning bit `W` and a fault code `F`. If each warning deterministically implies a particular fault code in this simplified model, knowing `W` may reduce uncertainty about `F`. In the extreme case where `F` is a deterministic function of `W`, `H(F|W)=0`; the joint entropy is just `H(W)`. Real systems may have exceptions, stale data, or measurement errors, so the deterministic rule must be justified rather than assumed from correlation in one sample.

If `X` and `Y` are independent, `p(x,y)=p(x)p(y)`, and the chain rule gives `H(Y|X)=H(Y)` and `H(X,Y)=H(X)+H(Y)`. Knowing one independent variable does not reduce uncertainty about the other under the model.

### Worked example 4: conditional entropy and mutual information

Let binary `X` and `Y` have joint probabilities `P(0,0)=3/8`, `P(0,1)=1/8`, `P(1,0)=1/8`, and `P(1,1)=3/8`. Both marginals are fair, so `H(X)=H(Y)=1` bit. Given either value of `X`, `Y` matches it with probability `3/4` and differs with probability `1/4`. Thus:

$$H(Y|X)=H_2(1/4)=-\frac14\log_2\frac14-\frac34\log_2\frac34\approx0.811278.$$

By the chain rule `H(X,Y)=1+0.811278≈1.811278` bits, and `I(X;Y)=H(Y)-H(Y|X)≈0.188722` bits. The dependence is positive but not deterministic. The same result follows directly by summing the four joint-probability terms.

## 6. Mutual information: statistical dependence

The **mutual information** between `X` and `Y` is:

$$
I(X;Y)=H(X)-H(X\mid Y)=H(Y)-H(Y\mid X)
=\sum_{x,y}p(x,y)\log_2\frac{p(x,y)}{p(x)p(y)}.
$$

It quantifies how much observing one variable reduces uncertainty about the other, under the specified joint distribution. It is zero exactly when the variables are independent (for the discrete case with the usual support conventions) and is nonnegative.

The final expression is the KL divergence `D₂(p(X,Y) || p(X)p(Y))`. Nonnegativity of KL divergence gives `I(X;Y) ≥ 0`. Mutual information is symmetric because the joint distribution and product of marginals are unchanged when `X` and `Y` exchange roles. It is measured in bits when the logarithm is base 2.

Mutual information detects statistical dependence, not causation. If a database column `region` predicts `shipping_delay`, that association does not prove region causes the delay. A third variable, selection effect, or data collection rule may explain the relationship. Nor does mutual information establish that either column is correctly interpreted or measured.

### 6.1 Conditional mutual information

Conditional mutual information `I(X;Y|Z)` measures dependence between `X` and `Y` after conditioning on `Z`:

$$
I(X;Y\mid Z)=\sum_{x,y,z}p(x,y,z)\log_2\frac{p(x,y\mid z)}{p(x\mid z)p(y\mid z)}.
$$

Equivalently, it is the expectation over `z` of the mutual information in the conditional distribution at that `z`. It can help express whether an association remains after accounting for a variable. It does not by itself solve causal inference; the choice of variables and causal assumptions remain essential.

## 7. Entropy and lossless coding

A **code** maps source symbols or sequences to codewords. A code is **prefix-free** if no codeword is the prefix of another; then a decoder can determine where each codeword ends without a separate delimiter. Variable-length prefix codes can use shorter codewords for more probable symbols and longer codewords for less probable ones.

For a memoryless source with known symbol probabilities and a uniquely decodable code, the average code length cannot be less than the source entropy. The derivation immediately below is specifically for prefix codes, whose lengths `lᵢ` obey Kraft's inequality. Extending the result to all uniquely decodable codes requires McMillan's inequality; that separate theorem is not derived here. Shannon's source-coding treatment establishes the broader source coding context (R-003).

$$
\sum_i 2^{-l_i} \le 1.
$$

Shannon's source coding results show how entropy bounds the achievable average description length for long sequences; practical block codes can approach the entropy limit, while finite-block and code-structure constraints create overhead. A direct lower-bound argument for a symbol-by-symbol prefix code follows from Kraft's inequality. For code lengths `lᵢ`, let `K = Σᵢ 2⁻ˡⁱ ≤ 1` and define `qᵢ = 2⁻ˡⁱ/K` over outcomes with positive probability. Since KL divergence is nonnegative,

$$
0\le D_2(p\Vert q)=\sum_i p_i\log_2\frac{p_i}{q_i}
=-H(X)+L+\log_2 K,
$$

where `L=Σᵢpᵢlᵢ` is expected code length. Thus `L ≥ H(X) - log₂K ≥ H(X)`. The inequality can be strict because code lengths are integers and code constraints may prevent matching every ideal length `-log₂pᵢ`.

This is a statement about coding symbols generated by a probability model. It does not say the data can always be compressed to exactly `H(X)` bits per row, nor that a database's whole file can attain the entropy rate of one independently modeled column. Kraft's inequality is necessary for prefix-code lengths; McMillan's inequality is the corresponding length bound for uniquely decodable codes, including codes that are not prefix-free.

### 7.1 Huffman coding walkthrough

Use the four-symbol distribution from Worked example 2:

| Symbol | Probability |
|---|---:|
| A | 1/2 |
| B | 1/4 |
| C | 1/8 |
| D | 1/8 |

Huffman construction repeatedly combines the two least-probable nodes:

1. Combine `C(1/8)` and `D(1/8)` into node `CD(1/4)`.
2. The remaining weights are `A(1/2)`, `B(1/4)`, `CD(1/4)`. Combine `B` and `CD` into `BCD(1/2)`.
3. Combine `A(1/2)` and `BCD(1/2)` at the root.
4. Assign 0/1 to each branch. One valid code is `A=0`, `B=10`, `C=110`, `D=111`.

The expected code length is:

$$
L=\frac12(1)+\frac14(2)+\frac18(3)+\frac18(3)=1.75\text{ bits/symbol}.
$$

It equals the entropy for this distribution because all probabilities are exact negative powers of two and the code lengths match `-log₂ pᵢ`. Other branch assignments produce different bit patterns but the same lengths. Huffman coding minimizes expected length among symbol-by-symbol prefix codes for the given probabilities; it does not prove that the probabilities are correct, that the source is stationary, or that the code is optimal for every block-level or constrained storage format.

The construction can be written abstractly using a min-priority queue:

```text
Huffman(symbols with positive weights):
    Q ← min-priority queue of leaf nodes keyed by weight
    while size(Q) > 1:
        left  ← extract-min(Q)
        right ← extract-min(Q)
        parent ← internal node(weight = left.weight + right.weight,
                               children = (left, right))
        insert(Q, parent)
    traverse the remaining root, appending 0 on left edges and 1 on right
    return each leaf's path as its codeword
```

With a binary heap this uses `O(n log n)` time and `O(n)` tree/queue space for `n` positive-probability symbols; emitting codewords also costs time proportional to their total length. Zero-weight symbols are excluded from the expected-cost optimization and must be handled separately if the application still needs to represent them. Ties can yield different codewords with the same optimal expected length; a stable tie-break is useful for reproducibility but does not change the guarantee.

**Why the greedy merge is optimal (proof sketch).** An optimal binary prefix tree has a deepest pair of sibling leaves. The two least-probable symbols can be exchanged into those deepest positions without increasing weighted path length: the least weights incur the greatest depths. Collapse that pair to a pseudo-symbol with their combined weight. Its contribution in the reduced tree corresponds to the two original leaves one level deeper. The resulting problem has one fewer symbol and the same objective form. Repeating this reduction yields the Huffman merges; induction on alphabet size establishes optimality among binary prefix codes. This argument assumes a finite alphabet and additive expected length. It does not establish optimality under extra constraints such as a maximum codeword length or update cost. The original construction and scope appear in Huffman's paper (R-011).

## 8. What entropy does and does not tell a database engineer

Entropy is useful in storage and communication because compression exploits regularity and repeated structure. If a column contains a small set of values with a skewed distribution, dictionary or entropy-based encoding may reduce storage. If values are highly variable, an encoding may need more bits or may have little benefit. Correlation between adjacent values can create additional compression opportunities that a single-symbol entropy estimate misses; the relevant object may be a sequence model or conditional entropy rate.

Database compression is not simply “store each value in its theoretical minimum number of bits.” An engine must also support decoding, random access, updates, vectorized scans, checksums, indexes, page boundaries, and compatibility. A compact encoding may cost CPU time or complicate modification. A column's marginal entropy may not represent the joint structure across columns or rows. Metadata and framing also consume space.

Entropy also does not measure:

- **Truth:** A false claim can be statistically rare or common.
- **Accuracy:** A sensor may output high-entropy noise.
- **Usefulness:** A random value can be irrelevant to every query.
- **Privacy:** Entropy alone is not a complete privacy guarantee; correlations and auxiliary data matter.
- **Semantic richness:** A predictable value can carry critical domain meaning.

The probability model and unit must accompany any reported entropy. If logs use natural logarithms, units are nats; if base 10, units are hartleys; with base 2, units are bits. Numerical values across bases are scaled, not contradictory.

## 9. Practical experiment: estimate a column's entropy

Take a small categorical column from a public or synthetic dataset. Count each value, calculate empirical probabilities `pᵢ = countᵢ/N`, and compute `H=-Σpᵢlog₂pᵢ`. Compare:

1. the entropy to the fixed-width bound `log₂ k` for `k` distinct values;
2. the entropy to the observed number of bits in a simple fixed-width representation;
3. results from the whole dataset with results from separate subsets or time periods;
4. whether repeated neighboring values suggest sequence structure not captured by marginal entropy.

Record the alphabet definition, treatment of null/missing values, sample size, and time window. Empirical entropy is an estimate for the chosen sample and categories, not a universal property of the business concept.

For a reproducible hand-check, use the 16-symbol sequence `AAAAAABBAABBCCDD` (first eight symbols: `AAAAAABB`; last eight: `AABBCCDD`). Whole-sample counts are `A:8, B:4, C:2, D:2`, giving probabilities `(1/2,1/4,1/8,1/8)` and entropy `1.75` bits/symbol. The first half has entropy `H₂(1/4)≈0.811278`; the second half is uniform over four symbols and has entropy `2` bits/symbol. With the Huffman code in Section 7.1, this sequence emits 28 bits, versus 32 bits for a fixed two-bit code, excluding codebook, framing, and format metadata. This finite-sample illustration does not imply a complete database representation saves 12.5%; headers, grouping, access requirements, and model costs can erase the difference. Reproduce by counting symbols, applying the entropy formula, then encoding each symbol in order.

### Exercises

For all exercises, use base-2 logarithms unless stated otherwise. Submit calculations with enough intermediate work to reproduce the result. Worked solutions are in the [separate solution guide](solutions/V01-C03-solutions.md).

| ID | Level | Objectives | Prerequisites | Deliverable and assessment |
|---|---|---|---|---|
| C03-E1 — Self-information | 1–2 | 1, 2 | Probability notation; logarithms | Calculate `I(x)` for probabilities `1`, `1/2`, `1/4`, `1/16`; explain the model-relative interpretation. 10 points: values 6, units and interpretation 4. |
| C03-E2 — Entropy boundary cases | 2 | 1, 2 | E1 | Calculate entropy for binary distributions `(1/2,1/2)`, `(3/4,1/4)`, and `(1,0)`; explain `0 log 0`. 15 points: calculations 9, boundary convention 3, units 3. |
| C03-E3 — Huffman construction | 2–3 | 4, 5 | Entropy, binary trees, weighted averages | For `(1/2,1/4,1/8,1/8)`, construct a code, show merge trace, verify prefix property, and calculate expected length. 20 points: trace 6, valid code 5, prefix argument 3, average and comparison with entropy 6. |
| C03-E4 — Joint distribution | 2–3 | 3 | E2; summation of joint probabilities | Use Worked example 4 to find marginals, `H(X)`, `H(Y|X)`, `H(X,Y)`, and `I(X;Y)`; check chain-rule equality. 20 points: marginals 4, conditional calculation 5, joint/chain 5, MI 4, units 2. |
| C03-E5 — Meaning versus uncertainty | 3 | 6 | V01-C02 | Give a high-entropy but semantically useless field and a low-entropy but operationally critical field; state assumptions and why entropy alone is insufficient. 10 points: examples 4, semantic distinction 3, assumptions 3. |
| C03-E6 — Association and decisions | 3–4 | 3, 6 | Mutual information; V01-C02 | Explain why mutual information between a customer attribute and churn establishes neither causation nor a decision to collect/store the attribute. Name confounding/selection and privacy or purpose considerations. 10 points: dependence/causation distinction 4, plausible alternative explanation 3, decision limits 3. |
| C03-E7 — Column compression lab | 4–6 | 7 | E2–E3; basic tabular data handling | Reproduce Section 9 with the stated sequence, then propose a database comparison recording representation, access/update workload, metadata, CPU, and size. Deliver a short report with formulas and a measurement plan; 15 points: counts/entropy 4, coded bit counts 3, overhead/workload plan 4, limitations/reproducibility 4. |

**Permitted tools:** calculator or a short script for E1–E4; any language for E7, but label calculations as measured or theoretical. **Grading:** the point allocations above are the rubric; alternative valid code assignments and well-reasoned examples receive credit. For E7, no performance claim is expected unless a concrete format, dataset, and measurement are supplied.

### Lab protocol — empirical entropy and encoding comparison

Use the fixed sequence in Section 9 or a documented synthetic/public categorical column. Preserve the raw input, state the alphabet and missing-value policy, and record tool/language version if scripting. Calculate counts and entropy, encode with the specified Huffman code, and compare payload bits to fixed width. A valid result for the hand-check is counts `8,4,2,2`, entropy `1.75` bits/symbol, Huffman payload `28` bits, and fixed-width payload `32` bits. Report separately all excluded costs (codebook, framing, page layout and headers); do not call payload-only savings end-to-end storage savings. Assessment uses E7's 15-point rubric.

## 10. Common misconceptions

### “Entropy is the amount of meaning in a record.”

Entropy quantifies uncertainty under a probability model. Semantic meaning requires interpretation and domain context (V01-C02).

### “A source with four symbols always needs two bits per value.”

Two bits suffice for a fixed-width code over four symbols. A variable-length code may have a smaller average length for skewed probabilities, with longer codewords for rare values and framing/model costs.

### “The entropy of my sample is the true entropy of the production source.”

It is an empirical estimate from a chosen sample, category mapping, and time period. Distribution drift and sampling bias can change it.

### “Mutual information proves one column affects another.”

It measures statistical dependence, not causal effect.

### “Compression can always reach exactly the entropy rate for every row.”

Coding bounds concern averages and model assumptions. Finite blocks, code constraints, metadata, access patterns, and implementation requirements impose overhead.

## 11. Summary

Self-information `-log₂p(x)` measures the information assigned to an outcome by a probability model; Shannon entropy is its expectation. Entropy ranges from zero for a certain outcome to `log₂|𝒳|` for a uniform finite alphabet. Joint and conditional entropy describe uncertainty across variables, while mutual information describes statistical dependence. The chain rule decomposes joint uncertainty into a first variable and what remains given it.

Lossless coding relates source entropy to minimum achievable average description length. Huffman coding provides an optimal symbol-by-symbol prefix code for a known finite distribution, though real storage has costs and constraints beyond that model. For database engineering, entropy informs compression but does not establish meaning, correctness, utility, causality, or privacy.

## Further reading

- Shannon's original paper (1948), cited below, for the source/channel model, entropy, and coding bounds.
- V01-C02 for the distinction between mathematical uncertainty and semantic interpretation.
- Later storage-engine chapters for dictionary encoding, page compression, columnar storage, and the interaction between compression and query execution.

## References

1. Shannon, C. E. (1948). “A Mathematical Theory of Communication.” *The Bell System Technical Journal*, 27, 379–423 (July) and 623–656 (October). [Full paper hosted by Harvard](https://people.math.harvard.edu/~ctm/home/text/others/shannon/entropy/entropy.pdf); [October part DOI](https://doi.org/10.1002/j.1538-7305.1948.tb00917.x). Primary source for entropy and communication/source coding theory.
2. **R-011 — Primary research.** Huffman, D. A. (1952). “A Method for the Construction of Minimum-Redundancy Codes.” *Proceedings of the IRE*, 40(9), 1098–1101. [Stanford-hosted full paper copy](https://web.stanford.edu/class/ee398a/handouts/papers/Huffman%20-%20Min%20Redundancy%20Codes%20-%20IRE52.pdf); [DOI: 10.1109/JRPROC.1952.273898](https://doi.org/10.1109/JRPROC.1952.273898). The metadata and full-paper copy were checked 2026-11-14; the DOI endpoint was not accessible in that check. The paper supports the stated construction and its minimum-redundancy result within the paper's coding model.
3. Codd, E. F. (1970). “A Relational Model of Data for Large Shared Data Banks.” *Communications of the ACM*, 13(6), 377–387. [IBM Research record](https://research.ibm.com/publications/a-relational-model-of-data-for-large-shared-data-banks). Included for the distinction between logical data organization and physical representation that motivates database abstractions.

## Chapter review record

- **Review date:** 2026-11-14 (Asia/Bangkok).
- **Reviewer:** Internal technical/editorial review; no independent external peer review is implied.
- **Objectives and assessment:** All seven objectives are mapped above to assessed exercises and chapter explanations; separate worked solutions and rubrics are in `solutions/V01-C03-solutions.md`.
- **Technical claims and research:** Entropy/source-coding claims map to R-003 (full paper accessed); construction and optimality scope map to R-011 (full paper copy accessed; DOI endpoint unavailable). Codd R-001 is included only for relational-model context, with access limited to its record/abstract as noted in C02. See research index/map.
- **Mathematics:** Base-2 equations, conditional example, and expected code lengths independently checked; the maximum-entropy derivation invokes but does not prove log-sum inequality. The prefix-code lower-bound proof uses Kraft; the broader uniquely-decodable statement invokes McMillan's inequality without proving it.
- **Examples/engineering:** Huffman algorithm is pseudocode, not executable code. The finite sequence lab has expected counts, entropy, and payload sizes and discloses excluded overhead; no benchmark or end-to-end database compression claim is made.
- **Exercises and solutions:** Seven exercises map all objectives, state level, prerequisites, deliverables, and point rubrics. Mathematical/algorithmic solutions and open-task grading guidance are in the separate guide.
- **Navigation and references:** Stable ID, prerequisite/next links, glossary/index, solution guide, source metadata, access limits, and source identifiers checked.
- **Diagrams:** Not applicable; the probability table, equations, and explicit code-tree paths communicate the examples without a system/process diagram.
- **Editorial review:** Terminology, assumptions, notation, code bounds, limitations, exercise progression, and cross-chapter semantic distinction reviewed.
- **Validation:** See QR-010 for exact structural checks and limitations. This evidence supports internal completion; it is not formal peer review or a proof of every cited theorem.
- **Status:** COMPLETED after the checks recorded in QR-010.
