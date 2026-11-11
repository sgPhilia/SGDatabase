# V01-C01 — What Is Data? From Observations to Stored Representations

**Volume:** I — Data, Computing, and Database Foundations  
**Part:** I — Data and Computing  
**Status:** COMPLETED  
**Prerequisites:** None  
**Next:** V01-C02 — Information, Representation, and Meaning (planned)

## Learning objectives

After studying this chapter, you should be able to:

1. Explain what this book means by *data*, *information*, *representation*, and *interpretation*.
2. Trace a real-world observation through a stored representation and back to a human-readable result.
3. Distinguish a value from its encoding, a field from its meaning, and a database from a DBMS.
4. Explain why storing data requires explicit choices about types, units, identity, missingness, and time.
5. Identify ways that a representation can lose information or cause an incorrect interpretation.
6. Describe why persistent data management becomes difficult when information is large, shared, evolving, or safety-critical.

## 1. A starting point: a value is not yet a useful record

Suppose a weather station reports the number `18.4`. By itself, that number is not enough to answer even a basic question. It might mean 18.4 degrees Celsius, 18.4 degrees Fahrenheit, 18.4 millimeters of rain, a wind speed, or a sensor voltage. It also lacks a place, a time, a sensor identity, and any statement about whether the measurement passed quality checks.

Now place it in a record:

| station_id | observed_at | temperature_c | quality |
|---|---|---:|---|
| ST-07 | 2026-11-11T09:00:00+07:00 | 18.4 | accepted |

This record is more interpretable because its fields provide names and context. But several questions remain: Is `ST-07` globally unique or only unique within one organization? Does the timestamp describe the station's local clock or a normalized instant? Does `accepted` mean the sensor passed calibration, or merely that the software parsed the reading? Can `temperature_c` be absent? If present, how many decimal places are meaningful?

This simple example introduces the central lesson of the book: stored symbols do not carry their full meaning by themselves. A system stores representations under conventions. People and programs interpret them using schemas, documentation, constraints, and shared assumptions. Database engineering is partly the work of making these conventions explicit and maintaining them as data is stored, queried, changed, shared, and recovered after failures.

## 2. Data, information, representation, and interpretation

The word *data* is used in several ways in everyday speech and in technical writing. This book uses the following working distinctions.

- **Observation:** something recorded about a person, object, event, or process. Examples include a payment, a temperature reading, or a book title.
- **Value:** one recorded item, such as `18.4`, `"ST-07"`, or a timestamp.
- **Data:** values or symbols represented so they can be stored, transmitted, transformed, or processed.
- **Representation:** the chosen form for data: a text string, integer, binary floating-point number, table row, JSON document, or bytes in a file.
- **Interpretation:** the rules that connect a representation to a meaning. For instance, the bytes `31 38 2E 34` may be interpreted as the UTF-8 text `18.4`; a schema may then say that this text is a decimal temperature in Celsius.
- **Information:** data interpreted in a context that makes it useful for some question or decision.

These are useful teaching distinctions, not universal formal definitions. Different disciplines define *data* and *information* differently. In database engineering, the practical concern is to record enough structure and rules that software can interpret values consistently, including after the original programmer or operator is gone.

### 2.1 Data does not explain itself

Consider a CSV row:

```text
ST-07,2026-11-11T09:00:00+07:00,18.4,accepted
```

The row is a sequence of characters. To use it, a program needs conventions:

1. Commas separate fields, unless quoted according to the file's rules.
2. The first field is a station identifier.
3. The second field is a timestamp with a numeric UTC offset.
4. The third field is a temperature in degrees Celsius.
5. The fourth field belongs to a controlled set of quality labels.

If one application assumes the temperature is Celsius and another assumes Fahrenheit, both may parse the same characters successfully and still disagree about the world. Parsing answers “can I recognize a representation?” It does not answer “is this the right representation or meaning?”

### 2.2 Schema: a shared description of structure

A **schema** is a declared description of some data's structure and rules. A schema might specify that a station observation has an identifier, an instant, a decimal temperature, and a quality state. Schemas can include types, constraints, relationships, and naming conventions.

Schemas are not merely paperwork. They help software reject invalid values, choose operations, and communicate expectations between independently written components. A schema is also not a complete description of reality: it records selected aspects of the world for a purpose. If an observation's real precision is only about one degree, storing six decimal places does not create six decimal places of measurement accuracy.

## 3. From the world to bits and back

A database ultimately relies on physical devices and software representations. The path from an event in the world to a query result has several conceptual stages:

```mermaid
flowchart LR
  A[World event or observation] --> B[Chosen meaning and units]
  B --> C[Logical fields and constraints]
  C --> D[Program values and types]
  D --> E[Encoded bytes]
  E --> F[Memory, files, or database pages]
  F --> G[Read and decode]
  G --> H[Query result]
  H --> I[Human or application interpretation]
```

Every transformation is a possible place for error. A Fahrenheit value may be placed in a Celsius field; a local wall-clock time may be treated as a universal instant; a decimal may be rounded to binary floating point; a string may be truncated; a byte sequence may be decoded using the wrong character encoding; a query may silently exclude missing values.

Later volumes study specific representations in detail. For now, keep these layers separate:

1. **Meaning:** What real-world fact or event is intended?
2. **Logical structure:** What fields, entities, and relationships describe it?
3. **Program representation:** What types and values do software components manipulate?
4. **Physical representation:** What bytes are placed in memory, files, pages, logs, or network messages?
5. **Presentation:** How a query result is shown to a person or consumed by another program?

One logical value may have multiple physical representations. A database may store a date as an integer count, a text string, or several binary fields. Conversely, identical bytes can mean different things under different schemas. A crucial database goal is to let users reason at an appropriate logical level without having to manually manage every physical detail.

## 4. Why persistent data management is hard

A small program can write values to a text file. As needs grow, however, the application must solve an expanding set of problems.

### 4.1 Finding and organizing data

If a file contains a million observations, scanning every line for station `ST-07` may be too slow. The application can build a lookup structure, but then it must keep that structure consistent when records are inserted, changed, or deleted. If several questions require different orderings, the application may need several structures and an update procedure for each.

### 4.2 Sharing data among programs and people

Suppose a monitoring application, a reporting tool, and an alert service all use the same observations. If each program has its own copy, updates can diverge. If they share a file, simultaneous writes can overwrite one another or expose partially written records unless a coordination protocol is designed and obeyed by every client.

### 4.3 Keeping data valid

An observation may refer to a station that does not exist, use an unsupported quality label, or duplicate an event identifier. An application can check these cases, but every writer must apply compatible checks. A second script, an import job, or a future service can bypass an assumption that only lived in the first application's code.

### 4.4 Changing software while preserving data

Requirements evolve. A station may gain altitude and manufacturer fields; a timestamp policy may change; an identifier may need a larger range. If data layout and application code are tightly coupled, every change can require coordinated rewrites. Data often outlives the program that first created it.

### 4.5 Surviving failures

Programs can stop during an update. Operating systems can crash; machines can lose power; storage devices can return errors. If an update changes several related values, a failure halfway through may leave an inconsistent state. A system must define what counts as committed and how incomplete changes are handled.

### 4.6 Coordinating concurrent operations

Two users may update the same account or inventory item at the same time. A report may read while an update is underway. The system must decide what each operation can observe, which operations can proceed together, and how conflicts are resolved. These rules are called concurrency control and transaction semantics; they are developed much later in Volumes VIII and IX.

These needs are not proof that every task requires a large database server. A small configuration file can be the right tool for a tiny, single-writer workload. The point is that persistence, sharing, correctness, and recovery create engineering responsibilities. Database systems package many such responsibilities behind a data model and interface.

## 5. Database, database contents, and DBMS

Technical writing sometimes uses *database* to mean the stored collection, and sometimes uses it to mean the software system. This ambiguity causes confusion, so this book uses these terms carefully:

- A **database** is an organized collection of data maintained for some purpose. It may also refer, in a product's terminology, to a named logical container inside a larger system.
- A **database management system (DBMS)** is software that defines, stores, retrieves, updates, and manages databases, while providing interfaces and rules for users or programs.
- A **database system** is the combination of a DBMS, the databases it manages, client programs, and the operating environment needed to use them.

Different products use these words differently. For example, PostgreSQL uses *database cluster* for a collection of databases managed under a shared data area; its server process accepts client connections. In SQLite, an application uses a library to operate on a database file, so the boundary between application and database engine looks different. These examples are product-specific; neither defines the universal meaning of DBMS.

The logical distinction remains useful: the **data** is not the same thing as the **software that manages it**. Losing the distinction is like confusing a document with the word processor that edits it, though databases add concurrency, recovery, query, and integrity machinery that makes the analogy incomplete.

## 6. What a DBMS contributes

A DBMS typically brings together several capabilities. The exact division of components differs across systems, but the responsibilities recur:

| Responsibility | Question the system helps answer |
|---|---|
| Data definition | What structures, types, and constraints are declared? |
| Data manipulation and queries | How can users request, insert, update, or remove selected data? |
| Storage and access methods | How are records organized, found, and moved between memory and persistent storage? |
| Integrity enforcement | Which values and relationships are allowed? |
| Transaction processing | Which groups of operations succeed or fail as a unit? |
| Concurrency control | What happens when operations overlap? |
| Logging and recovery | What survives a crash, and how is a consistent state restored? |
| Security and authorization | Which users may see or change which data? |
| Catalog and metadata | What structures exist, and what does the system know about them? |
| Administration and observability | How can operators inspect, back up, tune, and diagnose the system? |

This table is a conceptual map, not a claim that every DBMS implements each responsibility in the same module or offers the same guarantees. A DBMS can centralize checks that otherwise would be scattered across applications. That centralization has costs too: the DBMS must be configured, monitored, secured, and operated; clients must understand its semantics; and a poorly chosen or poorly used DBMS can add needless complexity.

## 7. A brief historical perspective

Database systems grew from the need to manage persistent, structured data, especially when data volumes and the number of users exceeded what ad hoc files and individual programs could comfortably support. Early systems commonly exposed navigational structures: an application followed predefined links or access paths through records. Such approaches can be efficient, but application logic may depend on how records are physically arranged or connected.

In 1970, Edgar F. Codd published “A Relational Model of Data for Large Shared Data Banks.” The paper argued for a data model based on relations and for insulating users and many application programs from changes in internal representation. It also introduced relational operations and discussed redundancy and consistency. This did not instantly replace existing systems; relational ideas required years of theory, prototypes, commercial products, language development, and engineering before broad adoption.

The lasting lesson for this introductory chapter is not that one model solved every data problem. It is that a data model can create a boundary between how users describe data and how an engine physically stores it. Later books will compare relational, document, key-value, graph, columnar, and other models, and will show where abstractions help or impose constraints.

## 8. A worked trace: recording a payment

Consider a purchase of 12.50 units of currency by customer `C-104` at 10:15. A careless design might store a single string:

```text
C-104 paid 12.50 at 10:15
```

This is readable, but software must guess where fields begin, what currency applies, whether `10:15` includes a date or time zone, and whether the payment succeeded. A more structured logical record could be:

| payment_id | customer_id | amount_minor_units | currency | occurred_at | state |
|---|---|---:|---|---|---|
| P-9931 | C-104 | 1250 | THB | 2026-11-11T10:15:00+07:00 | captured |

Why might amount be represented as 1250 minor units instead of a binary floating-point value `12.50`? For many currencies and payment rules, integer minor units avoid common rounding surprises, provided the currency's scale is explicitly known. This is not universal: currencies and business calculations may have different precision requirements, and some financial systems use exact decimal arithmetic. The representation must match the domain's rules.

Now trace one field:

1. A payment event occurs in the world.
2. A business rule identifies the amount owed and currency.
3. The schema assigns fields and requires a payment identifier.
4. Application code represents `1250` as an integer and `THB` as a currency code.
5. The DBMS encodes fields in its own on-disk format and may maintain indexes or logs.
6. A query retrieves the payment and decodes values into program types.
7. The application formats the value as `12.50 THB` for a receipt.

The displayed text is not necessarily the physical stored bytes. Nor does successful storage prove that the transaction was authorized, charged exactly once, or durably committed; those require additional integrity, transaction, and recovery design.

## 9. Correctness questions that begin at the data layer

Even before learning transactions, data representation raises correctness questions:

- **Identity:** What makes two records refer to the same real-world entity? Is an identifier stable, unique, and never reused?
- **Units:** Are measurements accompanied by units? Can different producers use different scales?
- **Time:** Is a timestamp a calendar label, local clock reading, or instant? What happens at daylight-saving transitions?
- **Precision:** Does the representation preserve the accuracy actually needed? Can conversion round values?
- **Missingness:** Does an absent value mean unknown, not applicable, not yet measured, or intentionally withheld?
- **Validity:** Which ranges and relationships are permitted? Who enforces them?
- **Evolution:** If a field changes meaning, how will old records be recognized and migrated?
- **Provenance:** Which instrument, user, or process supplied the value, and when?

These questions will return in data modeling, SQL, schema evolution, transaction processing, and database security. A reliable system cannot repair a concept that was never defined. Database engineering therefore starts before pages and indexes: it starts by agreeing on what data means and what properties must remain true.

## 10. Common misconceptions

### “Data is just numbers or strings.”

Numbers and strings are representations. Their meaning depends on types, units, schema, and context. Two identical strings may denote different things; two different encodings may denote the same value.

### “If the program can parse it, the data is valid.”

Parsing checks syntax and perhaps a type. It may not check whether a value is plausible, authorized, unique, correctly sourced, or consistent with other records.

### “A database is a file.”

A database may be stored in one or more files, but the database concept concerns organized data and rules. A DBMS may use files, logs, memory, indexes, remote storage, or combinations of these. A file is a storage object; a DBMS is a system managing representations and operations.

### “Using a DBMS automatically makes data correct and safe.”

A DBMS can enforce declared constraints and provide documented guarantees, but it cannot infer every business rule. Users must select and configure the system correctly, understand its semantics, control access, and test failure behavior.

### “More decimal places mean more accurate data.”

Precision of representation is not the same as accuracy of measurement. Storing `18.400000` does not make a sensor accurate to one-millionth of a degree.

## 11. Practical experiment: make meaning explicit

Create a small table or CSV containing observations from a familiar domain: books, meals, exercise sessions, or weather. For each field, write down:

1. Field name and intended meaning.
2. Type and allowed values.
3. Units, if applicable.
4. Whether the field may be absent and what absence means.
5. Identifier or relationship rules.
6. One invalid example and how a consumer should detect it.

Then ask a second person to interpret the data without extra verbal explanation. Record mismatches. This experiment tests whether meaning was captured in the schema and documentation instead of remaining only in the author's head.

### Reflection questions

1. Could two rows with the same visible values represent different events? What additional identity is needed?
2. What information is lost if a timestamp's offset is removed?
3. If an amount is stored as a decimal string, what conventions must a reader know?
4. Which assumptions in your data description belong to the domain, and which belong to a specific DBMS?

## 12. Summary

Data consists of represented values interpreted through shared conventions. Schemas make some of those conventions explicit by naming fields, types, constraints, and relationships, but a schema is still a model of selected aspects of reality. Data passes through semantic, logical, program, physical, and presentation layers, and every conversion can lose or distort meaning.

Persistent information becomes difficult to manage when it must be searched, shared, validated, changed over time, protected, or recovered after failure. A DBMS packages capabilities for defining and manipulating data, storing it, enforcing rules, coordinating concurrent work, and recovering from failures. Products differ in how they provide these capabilities. The first engineering task is to be precise about what is being represented and which properties the system must preserve.

## Further reading

- Edgar F. Codd, “A Relational Model of Data for Large Shared Data Banks” (1970): motivation for a relational model and independence from physical representation.
- Joseph M. Hellerstein, Michael Stonebraker, and James Hamilton, “Architecture of a Database System” (2007): broad overview of the responsibilities and design alternatives inside DBMSs.
- PostgreSQL documentation, “What Is PostgreSQL?” and server process documentation: a concrete client/server system and its terminology.
- SQLite, “Appropriate Uses For SQLite”: a primary-source discussion of embedded database use cases and workload trade-offs.

## References

1. Codd, E. F. (1970). “A Relational Model of Data for Large Shared Data Banks.” *Communications of the ACM*, 13(6), 377–387. [IBM Research publication record](https://research.ibm.com/publications/a-relational-model-of-data-for-large-shared-data-banks). Historical source; supports the stated motivation and relational-model contribution.
2. Hellerstein, J. M., Stonebraker, M., & Hamilton, J. (2007). “Architecture of a Database System.” *Foundations and Trends in Databases*, 1(2), 141–259. [DOI: 10.1561/1900000002](https://doi.org/10.1561/1900000002). Survey; supports the functional map of DBMS components, not a universal implementation template.
3. PostgreSQL Global Development Group. [About PostgreSQL](https://www.postgresql.org/about/) and [The PostgreSQL Server Application](https://www.postgresql.org/docs/current/app-postgres.html). Official documentation; product-specific terminology and current server behavior, accessed 2026-11-11.
4. SQLite Consortium. [Appropriate Uses For SQLite](https://www.sqlite.org/whentouse.html). Official documentation; product-specific use guidance, accessed 2026-11-11.

## Chapter review record

- Structure and objectives: checked against the textbook chapter standard.
- Concept progression: checked from data representation toward DBMS responsibilities.
- References: bibliographic records and direct links included for cited claims; product-specific claims labeled.
- Diagrams: one conceptual representation pipeline; no binary-layout diagram is claimed at this introductory level.
- Remaining limits: this chapter introduces rather than formally defines information theory, relational algebra, storage encodings, or DBMS guarantees; those subjects are developed in later chapters.
