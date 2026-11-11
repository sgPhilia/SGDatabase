# V01-C03 — Information Theory: Uncertainty, Entropy, and Coding

**Volume:** I — Data, Computing, and Database Foundations  
**Part:** I — Data and Computing  
**Status:** COMPLETED  
**Prerequisites:** [V01-C01 — What Is Data?](V01-C01-what-is-data.md); [V01-C02 — Meaning, Models, and Information](V01-C02-meaning-models-information.md); basic algebra and probability notation reviewed below  
**Next:** [V01-C04 — Bits, Bytes, Types, and Encodings](V01-C04-bits-bytes-types-encodings.md)  
**Glossary:** [Canonical terms](../../../glossary.md)  
**Concept index:** [Index](../../../INDEX.md)

## Learning objectives

After this chapter, you should be able to:

1. Define self-information and Shannon entropy for a discrete random variable.
2. Compute entropy from a probability distribution and interpret its units.
3. Derive conditional entropy, mutual information, and the chain rule for a small example.
4. Explain why an optimal lossless code has average length at least the source entropy.
5. Construct and evaluate a Huffman code for a small source.
6. Distinguish uncertainty, semantic meaning, data quality, privacy, and value to a user.
7. Explain why entropy is a useful but limited lens on database compression and storage.

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

For a memoryless source with known symbol probabilities and a suitable uniquely decodable code, the average code length cannot be less than the source entropy. For prefix codes, codeword lengths `lᵢ` obey Kraft's inequality:

$$
\sum_i 2^{-l_i} \le 1.
$$

Shannon's source coding results show how entropy bounds the achievable average description length for long sequences; practical block codes can approach the entropy limit, while finite-block and code-structure constraints create overhead. A direct lower-bound argument for a symbol-by-symbol prefix code follows from Kraft's inequality. For code lengths `lᵢ`, let `K = Σᵢ 2⁻ˡⁱ ≤ 1` and define `qᵢ = 2⁻ˡⁱ/K` over outcomes with positive probability. Since KL divergence is nonnegative,

$$
0\le D_2(p\Vert q)=\sum_i p_i\log_2\frac{p_i}{q_i}
=-H(X)+L+\log_2 K,
$$

where `L=Σᵢpᵢlᵢ` is expected code length. Thus `L ≥ H(X) - log₂K ≥ H(X)`. The inequality can be strict because code lengths are integers and code constraints may prevent matching every ideal length `-log₂pᵢ`.

This is a statement about coding symbols generated by a probability model. It does not say the data can always be compressed to exactly `H(X)` bits per row, nor that a database's whole file can attain the entropy rate of one independently modeled column.

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

### Exercises

1. Compute self-information for probabilities `1`, `1/2`, `1/4`, and `1/16` in bits.
2. Compute the entropy of a binary source with probabilities `(1/2,1/2)`, `(3/4,1/4)`, and `(1,0)`. Explain the limiting convention for zero probability.
3. A four-symbol source has probabilities `(1/2,1/4,1/8,1/8)`. Verify the Huffman code above is prefix-free and calculate its expected length.
4. Two variables have a deterministic relationship. What is their conditional entropy? What additional assumptions are needed before making that claim from observed database records?
5. Give an example of a high-entropy field that is meaningless and a low-entropy field that is operationally important.
6. Explain why mutual information between a customer attribute and churn does not establish causation or justify storing that attribute.
7. Design a compression comparison for a database column. Identify access/update patterns and metadata costs that an entropy-only calculation omits.

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
2. Huffman, D. A. (1952). “A Method for the Construction of Minimum-Redundancy Codes.” *Proceedings of the IRE*, 40(9), 1098–1101. [DOI: 10.1109/JRPROC.1952.273898](https://doi.org/10.1109/JRPROC.1952.273898). Original method underlying the optimal symbol-by-symbol prefix code discussed in Section 7.1.
3. Codd, E. F. (1970). “A Relational Model of Data for Large Shared Data Banks.” *Communications of the ACM*, 13(6), 377–387. [IBM Research record](https://research.ibm.com/publications/a-relational-model-of-data-for-large-shared-data-banks). Included for the distinction between logical data organization and physical representation that motivates database abstractions.

## Chapter review record

- Mathematical definitions and examples: checked for consistency using base-2 logarithms.
- Entropy maximum and code-length lower bound: derivations included through KL nonnegativity and Kraft's inequality; the log-sum inequality is assumed, not proved.
- Huffman example: the provided lengths yield expected length 1.75 bits/symbol, matching this distribution's entropy.
- Scope: discrete finite-alphabet source coding only; continuous differential entropy and noisy-channel coding are not developed here.
- Semantic boundary: explicitly cross-referenced to V01-C02.
- References: Shannon's original source and Huffman's original minimum-redundancy-code paper are indexed and linked.
- Validation: local links/anchors checked repository-wide; entropy and Huffman worked averages independently recalculated as 1.75 bits/symbol; display-math delimiters balanced.
- Acceptance: applicable chapter criteria met. No runnable implementation is appropriate here; the coding experiment and derivations provide the engineering connection.
