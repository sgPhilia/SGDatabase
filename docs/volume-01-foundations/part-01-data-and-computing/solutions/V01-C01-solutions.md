# V01-C01 — Worked Solutions and Grading Guidance

This file accompanies [V01-C01](../V01-C01-what-is-data.md). The examples are model answers, not the only acceptable responses. Award points for technically sound alternatives that state their assumptions and limits.

## Exercise 1 — Trace representation and interpretation

One defensible data dictionary is:

| Field | Example interpretation | Type/rule | Unresolved question |
|---|---|---|---|
| `station_id` | Identifier `ST-07` | Text identifier, not a measured quantity | Is it unique globally, within one operator, or only in this file? |
| `observed_at` | Local timestamp at offset +07:00 | Timestamp with numeric UTC offset | Is it the event instant, device clock time, or time the record was received? |
| `18.4` | Numeric observation | Decimal-looking token | Which unit, precision, sensor, calibration state, and missing-value convention apply? |
| `accepted` | Quality classification | Value from a controlled vocabulary | Which checks define “accepted,” and can that policy change? |

Trace: a physical process changes; a sensor samples it; software produces the row; a parser splits fields and converts tokens; a schema assigns names/types/rules; a query returns selected values; a client formats them; a person interprets the display. Plausible unresolved assumptions include Celsius versus Fahrenheit and whether `observed_at` denotes measurement time or ingestion time. Parsing confirms only that the syntax was recognized under a parser's rules.

**Rubric:** 10 points as specified in the chapter. Do not award full credit if an answer treats field names as proof of units, truth, or uniqueness.

## Exercise 2 — Find information loss

The producer's hundredth-degree distinctions are discarded when each value is rounded to a whole degree. The consumer cannot reconstruct the original hundredths from that integer because many possible source values map to the same rounded result. Displaying six digits after the decimal point adds formatting detail, not recovered information. If the consumer converts the integer to a floating-point type, it may represent that integer exactly within the type's exact-integer range, but this does not reverse the earlier rounding; the chapter's prompt supplies no range or binary format from which to claim more.

Rounding could be acceptable when the source sensor and application decision only warrant whole-degree resolution and preserving finer values would not change any permitted calculation. That decision should be explicit and supported by the domain's measurement and error requirements. It is not acceptable to infer that the original measurement had whole-degree accuracy merely because the export discarded more detail.

**Rubric:** 10 points as specified in the chapter. Full credit identifies loss at the rounding stage, distinguishes formatting precision from information content and measurement accuracy, and gives a scoped acceptability condition.

## Exercise 3 — Design and test a small data dictionary

There is no single domain-specific answer. For a meal log, a strong artifact might define:

| Field | Meaning/type | Rule | Missingness | Invalid example |
|---|---|---|---|---|
| `meal_id` | Identifier for one logged meal | Unique within the dataset; never reused | Not allowed | Duplicate `meal_id` |
| `eaten_at` | Instant when meal began | ISO 8601 timestamp with offset | Not allowed | Date/time without the required offset |
| `food_name` | User-entered label | UTF-8 text, trim policy stated | Not allowed | Empty string |
| `mass_g` | Estimated consumed mass in grams | Positive decimal, scale and estimation status stated | Unknown allowed and distinct from zero | Negative value |

For the reader test, the learner should collect an actual interpretation, not merely ask “is this clear?” A useful report quotes the reader's understanding, points to the missing or ambiguous rule, revises the data dictionary, and records whether the second reading changed. Remaining disagreement should be preserved as an open issue rather than hidden.

**Rubric:** 20 points as specified in the chapter. A compact dataset with fewer fields may earn full credit if it meets all stated requirements and the examples are meaningful.

## Exercise 4 — Select a storage boundary for a workload

A reasonable design is to capture records locally on each device while offline and synchronize them later to a shared service. This suggests different local and central responsibilities, but does not settle conflict resolution, duplicate delivery, identity assignment, authorization, retention, or how users see records that have not synchronized. An alternative may be appropriate if devices must coordinate live, if data sensitivity forbids central transfer, or if synchronization cannot tolerate the expected delay.

Use R-006 to characterize SQLite's own embedded/local-storage guidance and R-005 for the PostgreSQL 18 server architecture example. Neither source establishes a universal workload-selection rule. Do not present SQLite as inherently “single-user” or client/server software as automatically correct; requirements and verified product behavior must decide.

**Rubric:** 15 points as specified in the chapter. Give full source-use credit for accurately describing the scope and limitation of the official guidance. A justified alternative architecture can earn full credit.

## Reflection prompts — self-check guidance

1. Two events can share displayed values if the representation omits an event identifier, sufficient time precision, source identity, or another distinguishing attribute. Which identifier is correct depends on the domain and its reuse rules.
2. Removing an offset can leave a wall-clock label whose corresponding instant is ambiguous; the result depends on the time-zone and daylight-saving conventions used. An offset is not identical to a named time zone's historical rules.
3. A decimal string requires a grammar, decimal separator, scale/rounding rule, currency or unit where relevant, sign convention, and a policy for whitespace and invalid text.
4. Domain assumptions include what an observation means and what counts as a valid event. DBMS-specific assumptions include syntax, supported types, storage behavior, and documented transaction guarantees. Some policies are implemented in the DBMS but originate as domain requirements.
