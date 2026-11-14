# V01-C02 — Worked Solutions and Grading Guidance

This file accompanies [V01-C02](../V01-C02-meaning-models-information.md). These are model answers. A different schema or recommendation can receive full credit when its purpose, assumptions, and limits are coherent.

## Exercise 1 — Interpret `0042`

Two valid interpretations include:

| Case | Syntax | Type | Meaning and context |
|---|---|---|---|
| `0042` as a product code | Four ASCII/UTF-8 digits | Text identifier | Product number whose leading zero is significant; lookup is scoped to a catalog version |
| `0042` as a count | Digits in a numeric grammar | Nonnegative integer | Quantity forty-two; leading zero has no numeric significance |

For the same product-code meaning, one representation could be the four text characters `0`, `0`, `4`, `2` encoded as UTF-8. Another could be a packed-decimal or fixed-width binary representation if a format specifies digit order and decoding. These encodings are not self-describing: the consumer needs a schema or protocol rule to know which one is present.

**Rubric:** 10 points as specified in the chapter. Do not award full credit if the answer treats an identifier as numeric and silently discards a significant leading zero.

## Exercise 2 — Library-loan model

One possible circulation model is:

- `Patron(patron_id, account_state)`
- `Item(item_id, title_id, copy_state)`
- `Loan(loan_id, item_id, patron_id, checked_out_at, due_at, returned_at?)`

The intended question is “which physical copy is currently loaned to which patron, and when is it due?” Three deliberate omissions could be: (1) the item's shelf-move history, making past locations unavailable; (2) renewal/hold history, making service and policy analyses unavailable; (3) patron contact preference, preventing notification planning. Other omissions are acceptable when tied to a stated scope. The model should distinguish a title from a physical copy if a library owns multiple copies and circulation is copy-specific.

**Rubric:** 15 points as specified in the chapter. Full credit requires consequences of the omissions, not merely a list of fields.

## Exercise 3 — Age versus birth date

For a one-time eligibility decision at time `t`, a stored age is meaningful only if its definition and as-of date are known. A cached age can become stale after a birthday. A birth date supports recomputation at `t`, but it is more sensitive and creates collection, access-control, retention, and breach risks. If the business needs only an age threshold, a derived eligibility flag or a coarse age band may be enough, depending on audit and legal requirements. The system should avoid keeping the birth date indefinitely if no justified use remains.

No recommendation is universal: an audit requirement may require retaining evidence, while another use case may prohibit collecting birth date. State the decision time, policy version, source/provenance, and retention rule.

**Rubric:** 15 points as specified in the chapter. Accept alternatives that explain both temporal semantics and data-minimization trade-offs.

## Exercise 4 — Entropy and identifier claims

“128-bit” describes the size of the representation, not the probability distribution that generated it. A claim of 128 bits of entropy would require assumptions about the generator and the identifier distribution; a deterministic counter encoded in 128 bits can have little uncertainty if its state is predictable. Even a uniformly sampled 128-bit value does not state what entity it identifies, who issued it, whether the requester is authorized to use it, or whether it appears in the database. Uniqueness is a separate property and depends on the number of generated values, collision handling, and scope. Randomness can make guessing harder under an appropriate threat model, but it is not authentication or proof of ownership.

**Rubric:** 10 points as specified in the chapter. Distinguish bit width from entropy and entropy from semantics, authorization, and uniqueness.

## Exercise 5 — Ambiguous-field audit

There is no single dataset-specific answer. Example for a field named `date`:

| Ambiguity | Possible meanings | Clarifying rule |
|---|---|---|
| Which date? | Event date, record creation date, import date | Rename to `occurred_at` or `imported_at` and define the event |
| Which calendar/time basis? | Local calendar day, UTC instant, date without time | Specify the type, time zone/offset policy, and whether time-of-day exists |
| What does missing mean? | Unknown, not applicable, not collected | Define an explicit missingness state; do not overload a valid date sentinel |

The reader test should capture three actual answers before revision and compare them with the intended meaning. A strong report links each change to a recorded disagreement and lists any remaining ambiguity. It must not claim that improved documentation validates the source records.

**Rubric:** 20 points as specified in the chapter.

## Practical experiment — verification guide

For each of the five audited fields, check that the artifact states intended concept, type/format, relevant unit or temporal basis, identity scope, missingness, producer/provenance, and version context when applicable. Compare the first and revised reader answers. Success means the revised description resolves the targeted ambiguity or explicitly preserves it as an open question. It does not mean every possible interpretation has been eliminated.
