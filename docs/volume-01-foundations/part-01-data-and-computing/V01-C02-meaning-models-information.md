# V01-C02 — Meaning, Models, and Information

**Volume:** I — Data, Computing, and Database Foundations  
**Part:** I — Data and Computing  
**Status:** COMPLETED  
**Prerequisites:** [V01-C01 — What Is Data?](V01-C01-what-is-data.md)  
**Next:** [V01-C03 — Information Theory: Uncertainty, Entropy, and Coding](V01-C03-information-theory-entropy-coding.md)  
**Glossary:** [Canonical terms](../../../glossary.md)  
**Concept index:** [Index](../../../INDEX.md)

## Learning objectives

After studying this chapter, you should be able to:

1. Explain why a stored representation does not determine its meaning without a context and interpretation rule.
2. Distinguish a data model, schema, vocabulary, value, and real-world referent.
3. Explain how a model selects aspects of a domain and why every model has a purpose and limits.
4. Separate semantic information (“what does this mean?”) from Shannon information (“how uncertain is the outcome?”).
5. Analyze how two systems can parse the same bytes but assign different meanings.
6. Design a small data description that makes units, time, identity, missingness, and provenance explicit.

## 1. The same symbol can say different things

Imagine two applications receive the bytes `31 32 2E 35 30`. Under UTF-8, these bytes represent the characters `12.50`. That decoding tells us which symbols are present. It does not tell us whether the value is a price, a temperature, a measurement, a version number, or a string identifier. Even after deciding it is money, we still need a currency, rounding policy, and a distinction between an amount owed and an amount actually paid.

Now imagine that one service interprets `12.50` as dollars and another as euros. Both parse successfully. Their disagreement is semantic: the systems use the same sign sequence with different interpretation rules. A database can store a well-formed value while the organization using it still has a meaning problem.

This chapter studies the bridge between **representation** and **meaning**. That bridge is built from a model of the relevant domain, names for the model's parts, rules for interpretation, and shared agreements among producers and consumers.

## 2. Meaning is relational: symbol, rule, context, referent

A practical way to reason about meaning is to identify four elements:

1. **Symbol:** the visible or encoded form, such as `12.50`, `THB`, or `2026-11-11T10:15:00+07:00`.
2. **Interpretation rule:** the convention that says what the symbol denotes, such as “amount in Thai baht, expressed in major currency units.”
3. **Context:** the setting in which the rule applies, including the application, schema version, locale, time zone, and business process.
4. **Referent or intended concept:** the thing, event, or abstraction being described, such as an order total or a measured temperature.

An interpretation can fail even when every byte is intact. A producer may label a local time as UTC; a consumer may use a newer schema than the producer intended; an identifier may be reused for a different person; or a unit may be implicit in a spreadsheet column title that disappears during export.

This is why “the database stored it correctly” is not a complete correctness argument. Storage correctness may establish that the recorded bits survived. Semantic correctness asks whether the bits still denote what the system's users intended.

### 2.1 Syntax, type, and semantics

These three checks answer different questions:

- **Syntax:** Does the representation conform to a grammar or encoding? Is `12.50` a valid decimal string?
- **Type:** Which operations are allowed by the declared category? Is this value treated as an integer, date, text, or decimal?
- **Semantics:** What domain concept does it represent, under what units and rules, and what does it mean in this application?

A database can reject malformed syntax and invalid types. It can also enforce some semantic constraints when these are expressed as schema constraints or application rules. But a type called `DECIMAL` does not intrinsically tell us whether its values are temperatures, prices, probabilities, or distances. A `TIMESTAMP` type may have product-specific behavior around time zones; the product name alone does not establish what the business intended.

## 3. Models: purposeful simplifications of a domain

A **model** is a structured representation of selected aspects of some domain, built for a purpose. A model of a library might include books, patrons, loans, and due dates. It might omit the color of each book, the history of every shelf move, or the reader's personal rating because those facts are not needed for circulation control.

Modeling is selective. It is not a process of copying reality whole. A model must decide:

- what entities or events matter;
- which properties are recorded;
- which distinctions are collapsed together;
- which relationships are represented;
- what changes are allowed over time;
- what questions and operations the model should support;
- what must remain true for the model to be useful.

Consider a delivery service. A single field `address` might be enough to display a label, but insufficient to route a delivery or analyze service regions. A richer model may separate recipient, street, unit, district, postal code, country, and delivery instructions. This introduces more structure and validation, but also increases the number of rules that must be maintained. The “best” model depends on the work the system is meant to support.

### 3.1 Abstraction and loss

When a model omits a distinction, downstream users cannot recover it from that model alone. If two temperatures are stored after rounding to the nearest degree, their original tenths are lost. If all failed payments are represented as simply `false`, the system may no longer distinguish a declined card, a timeout, a cancellation, and an invalid request.

Some loss is intentional. A daily average does not preserve each measurement, but it may be the right representation for a summary report. The engineering question is not “does this model contain every possible fact?” It is “does the model preserve the distinctions required by its intended tasks, and are its omissions known?”

### 3.2 Model, schema, and instance

These terms describe different layers:

- A **data model** provides concepts for describing data and operations over it. The relational model, graph models, and document models offer different concepts and constraints.
- A **schema** is a particular structural description made using a model: for example, the tables `Customer` and `Order`, their attributes, and their constraints.
- A **database instance** is the actual data currently represented under a schema at a particular time.

The distinction resembles a grammar, a particular language design, and a text written in that language, but the analogy is imperfect. A schema may evolve, some systems allow records with varying structure, and constraints may be enforced partially or at different times. We will refine these definitions in Volume II when studying relational theory.

## 4. A worked example: “the customer is 18”

Suppose an application stores:

```text
customer_id = 4812
age = 18
```

At first glance this appears clear. Yet several interpretation questions remain:

1. Is the age measured in completed years, or rounded from a fractional number?
2. Is it the age at account creation, the current age, or age at a particular event?
3. What reference date is used to calculate it?
4. What happens if the birth date is unknown or legally withheld?
5. Is `18` an exact claim, a user-provided assertion, or a value calculated from another source?

If this value is used to determine eligibility, the distinction between birth date and a cached age may matter. A stored age can become stale; a stored date of birth raises privacy and retention concerns. Different models support different operations and expose different risks.

One possible event-oriented representation is:

| customer_id | birth_date | source | verified_at |
|---|---|---|---|
| 4812 | 2008-11-10 | identity_check | 2026-11-10T14:00:00Z |

An eligibility decision can then be computed relative to a decision date. This does not mean this schema is always preferable: it stores more sensitive information and may be inappropriate if only an age band is needed. Data minimization and access control become part of modeling because the chosen representation affects both usefulness and exposure.

## 5. Database schemas as shared contracts

A schema serves as a contract between the system and the programs or people that use it. It can describe names, types, allowed values, relationships, and constraints. For a payment, the schema might distinguish:

- `requested_amount` from `captured_amount`;
- `currency_code` from a display symbol;
- an attempt from a settled transaction;
- a local time from a UTC instant;
- an unknown outcome from a rejected outcome.

Those distinctions affect query correctness. A report that sums requested amounts when it intends to sum captured amounts may execute perfectly and answer the wrong question. A schema should therefore be evaluated against the questions users need to ask and the rules they need to enforce.

Schemas also have versions and histories. When a field's meaning changes, the old and new meanings must not be confused merely because both are named `status`. A migration may require transforming old values, retaining version metadata, or publishing a new field. Schema evolution will recur in SQL, architecture, and distributed systems.

### 5.1 Names are not enough

Names help people recognize intent, but names cannot carry all required rules. `created_at` is ambiguous unless the system defines which event “created” refers to, what clock and time zone are used, and whether the value can change. `amount` is incomplete without currency and scale. `user_id` is incomplete without a scope and uniqueness rule.

Good schema design combines names with types, constraints, documentation, and operational conventions. It also avoids pretending that naming alone proves correctness.

## 6. Semantic information and Shannon information

The word *information* has a technical meaning in information theory that differs from ordinary statements about meaning or usefulness.

Claude Shannon's 1948 communication theory studies mathematical properties of sources, messages, encodings, and noisy channels. It gives a way to measure uncertainty over possible outcomes and reason about the limits of compression and reliable communication. Its central quantities depend on probabilities and distinguishable symbols. They do not, by themselves, measure whether a message is true, useful, relevant, or meaningful to a person.

For example, if a fair coin has two equally likely outcomes, learning the result removes one bit of uncertainty under Shannon's measure. The result could be heads or tails, but the calculation does not tell us whether the coin toss matters to a medical decision, whether the coin is biased in reality, or whether a person understands the words used to report it.

The distinctions are:

| Question | Kind of question | Example |
|---|---|---|
| What does `18` describe here? | Semantic interpretation | Age in completed years at a defined date |
| How uncertain was the outcome before observation? | Shannon information | Entropy of a specified probability distribution |
| Does this fact improve a decision? | Utility/relevance | Whether age affects a particular eligibility rule |
| Is the recorded value accurate? | Data quality/provenance | Whether a verified source supports the birth date |

These questions can interact, but one answer cannot substitute for another. A highly unpredictable random token may have high entropy and no useful business meaning. A predictable code such as `0` may be crucial if it means “payment settled.”

Chapter V01-C03 develops information theory mathematically. Here the essential boundary is that Shannon's theory abstracts away semantic interpretation to study communication limits. Database engineering needs both kinds of reasoning: information theory can help analyze encoding or compression, while schema and domain knowledge explain what stored values mean.

## 7. Context, provenance, and time

Interpretation often depends on context that should be recorded or made stable:

- **Units and scales:** Celsius vs Fahrenheit; meters vs feet; major vs minor currency units.
- **Temporal context:** event time vs ingestion time; local time vs UTC; valid time vs transaction time.
- **Identity scope:** local customer number vs globally unique identifier; tenant-specific vs system-wide uniqueness.
- **Provenance:** producer, instrument, import job, source document, or transformation that created the value.
- **Version:** schema version, rule version, software version, or taxonomy in effect.
- **Missingness:** unknown, not applicable, withheld, not measured, or not yet available.

If a fact changes over time, a single field may obscure whether the system is recording the current state or the history of states. This is especially important for addresses, prices, permissions, diagnoses, and account status. Later work on temporal databases and MVCC will distinguish domain time from the system's record of when a version was stored.

Context also has costs. Recording every possible detail increases storage, collection burden, privacy exposure, and maintenance. The design task is to preserve the context required for correctness, auditability, and intended analysis while limiting unnecessary collection.

## 8. A compact interpretation workflow

When reviewing a data field or exchange format, use this sequence:

1. Identify the concrete question the data is supposed to answer.
2. Name the real-world concept or event being represented.
3. State the unit, time basis, identity scope, and allowed states.
4. Specify the logical type and encoding separately.
5. Identify who creates, transforms, validates, and consumes the value.
6. State what missing, invalid, stale, and contradictory values mean.
7. List which distinctions the model intentionally omits.
8. Test interpretation by asking an independent consumer to explain sample values.

This is not a formal modeling notation. It is a review technique to expose assumptions before selecting tables, document structures, or binary layouts.

## 9. Practical experiment: the ambiguous field audit

Choose a small dataset you know well. Find five field names that appear self-explanatory, such as `date`, `status`, `value`, `name`, or `amount`. For each, write two plausible interpretations and identify what extra schema rule would eliminate the ambiguity.

Then exchange the data description with someone who did not design it. Ask them to answer three questions using only the documented schema. Record disagreements. A disagreement demonstrates that the representation did not fully communicate the intended interpretation; it does not necessarily mean the consumer made an unreasonable mistake.

### Exercises

1. **Interpretation layers:** For the symbol `0042`, give two different meanings and two different encodings that could represent the same meaning. Identify syntax, type, and semantic rules for each.
2. **Model boundaries:** Design a minimal model for a library loan. State three facts you omit and explain which questions become unanswerable because of those omissions.
3. **Staleness:** Compare storing a person's current age with storing a birth date. List correctness, privacy, and operational trade-offs. State what reference time is required for each answer.
4. **Semantic vs statistical information:** A 128-bit random identifier is difficult to predict. Explain what a high-entropy property can and cannot establish about the identifier's meaning, ownership, or validity.
5. **Review:** Audit one field from an existing CSV or spreadsheet using the eight-step interpretation workflow. Propose a schema note and one validation rule.

## 10. Common misconceptions

### “Information theory measures how meaningful data is.”

Shannon's quantities characterize uncertainty and communication under a probabilistic model. They do not automatically measure truth, importance, understanding, or business value.

### “A schema is the meaning.”

A schema records selected structural and constraint rules. Meaning also depends on domain concepts, producers, consumers, operational conventions, and time. A misleading schema can consistently enforce the wrong model.

### “If a field name is clear to its author, it is clear to the system.”

Software sees encoded values and declared rules. Unwritten assumptions do not become machine-enforced constraints simply because a name looks descriptive.

### “More detailed models are always better.”

More detail can preserve useful distinctions, but it also has privacy, collection, storage, and maintenance costs. Model detail should answer a justified need.

## 11. Summary

Meaning arises from an interpretation of symbols in a context, with reference to a model of some domain. A data model selects concepts and operations; a schema makes a particular structure and some rules explicit; an instance contains values under that schema. Syntax, type, and semantics are separate layers, and each can be correct while another is wrong.

Shannon information theory supplies a mathematical measure of uncertainty and communication limits, not a universal measure of semantic meaning or usefulness. Database engineers need to know which question a measure answers. Sound data design makes important context explicit—units, time, identity, provenance, versions, and missingness—while documenting intentional omissions and managing privacy costs.

## Further reading

- Claude E. Shannon, “A Mathematical Theory of Communication” (1948), especially its opening discussion of communication systems and later treatment of source uncertainty and coding.
- E. F. Codd, “A Relational Model of Data for Large Shared Data Banks” (1970), for the motivation to describe data independently of internal representation.
- V01-C03 develops Shannon entropy, source coding, and related measures with worked probability examples.

## References

1. Shannon, C. E. (1948). “A Mathematical Theory of Communication.” *The Bell System Technical Journal*, 27, 379–423 and 623–656. [Full paper hosted by Harvard](https://people.math.harvard.edu/~ctm/home/text/others/shannon/entropy/entropy.pdf); [DOI for October part](https://doi.org/10.1002/j.1538-7305.1948.tb00917.x). Primary source for the mathematical communication framework; it does not define semantic value for database applications.
2. Codd, E. F. (1970). “A Relational Model of Data for Large Shared Data Banks.” *Communications of the ACM*, 13(6), 377–387. [IBM Research record](https://research.ibm.com/publications/a-relational-model-of-data-for-large-shared-data-banks). Primary historical source for relational representation and data independence motivation.

## Chapter review record

- Prerequisites and cross-references: linked to V01-C01 and planned V01-C03.
- Concept progression: symbol/context -> model/schema -> semantic vs statistical information -> practical interpretation workflow.
- Technical claims: Shannon is used for uncertainty/communication framing; no claim that entropy captures semantic meaning.
- Practice: one audit experiment and five exercises included.
- Glossary/index: canonical links added to [glossary](../../../glossary.md) and [concept index](../../../INDEX.md).
- Local links: checked in the repository-wide documentation check recorded in `QUALITY_REPORT.md`.
- Acceptance: all applicable chapter criteria are met; code is not necessary for this semantic/modeling chapter.
