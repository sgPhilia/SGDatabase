# Educational Standards: Chapter Acceptance, Exercises, and Laboratories

These standards apply to future chapter reviews and to re-evaluation of existing chapters. They do not imply that any current chapter already meets the requirements. Chapter status is authoritative in `CHAPTER_STATUS.md`; acceptance evidence belongs in the chapter's review record and `QUALITY_REPORT.md`.

## 1. Chapter status policy

| Status | Meaning | Minimum evidence / transition rule |
|---|---|---|
| NOT STARTED | No substantial chapter draft exists. | A roadmap entry alone is not work started. |
| IN PROGRESS | Authoring or substantive revision is actively underway; the chapter is not ready for acceptance review. | Record the current task and next checkpoint; move to DRAFT when a coherent reviewable draft exists, or NEEDS REVIEW when a focused review has identified evidence gaps. |
| DRAFT | A substantial draft exists, but research, explanation, examples, or structure are incomplete. | Objectives and scope exist; authoring may continue. No acceptance claim. |
| NEEDS RESEARCH | Important claims depend on missing or unverified evidence. | Name claims/sources to resolve; move to NEEDS REVIEW only when evidence-backed draft is ready for review. |
| NEEDS REVIEW | A coherent draft is ready for a focused technical/editorial review, or identified acceptance evidence is missing. | Review checklist states exact open items, reviewer/date if available, and evidence needed. |
| COMPLETED | All applicable acceptance gates below have recorded evidence and no unresolved material issue remains. | Chapter review record plus matching `QUALITY_REPORT.md` entry; update registry, navigation and progress consistently. |
| BLOCKED | A necessary resource or external decision is unavailable. | State the blocker and independent work that can continue. |

Status is not inferred from file existence, length, a prior status label, or a passing automated check. A previous status can be downgraded when its evidence does not meet the current policy; preserve historical records and log the reason.

## 2. Chapter acceptance checklist

Each applicable item is marked PASS with a citation to evidence, NOT APPLICABLE with a reason, or OPEN. Any material OPEN item prevents COMPLETED.

1. **Purpose and objectives:** purpose, audience, prerequisites, and measurable learning objectives are stated; each objective is addressed in the chapter and assessed by an exercise or explicit explanation.
2. **Prerequisites and progression:** required ideas are taught earlier or introduced locally; terminology is defined before use; cross-chapter dependencies are accurate.
3. **Technical accuracy:** central claims have traceable support or are labeled as an inference/teaching simplification. Product claims specify product/version/date and scope. Algorithms state inputs, outputs, preconditions, invariants, edge cases, and relevant costs.
4. **Conceptual depth:** explains the problem, mechanism, assumptions, alternatives, trade-offs, limitations, and failure conditions at a depth appropriate to the topic.
5. **Mathematics:** symbols and variables are defined; assumptions and domains are explicit; derivations are valid; numerical examples are independently recalculated; boundary cases and model limits are considered where relevant.
6. **Reproducible examples:** examples state input, steps, and expected result. Source code is labeled as compilable code or pseudocode. Compilable examples are run with the declared toolchain or a documented minimal harness; expected outputs are checked.
7. **Engineering/correctness:** applicable data structures, state transitions, invariants, errors, and failure cases are explained. Rust safety is not substituted for database-level correctness. Experiments specify environment and observable outcomes.
8. **Exercises:** exercise set includes varied reasoning, not only recall; every exercise has an objective, difficulty level, required deliverable, and answer guidance or grading criteria.
9. **Solutions policy:** full worked solutions are provided separately for mathematical derivations, numerical problems, and algorithm traces where a unique or checkable result exists. For open design/research tasks, publish a rubric, evaluation criteria, and at least one acceptable reasoning path or example outcome. For implementation assignments, provide interface/behavior specifications and tests or test oracles, not a complete implementation that defeats the assignment.
10. **References:** bibliographic metadata and access state are accurate; references map to claims; access limitations are stated. A bibliography list alone is not claim support.
11. **Internal links and navigation:** stable ID, title, prerequisites, prior/next links where assigned, glossary/index links, volume/part placement, and references are checked.
12. **Diagrams:** diagrams are technically meaningful, labeled, explained, and syntactically checked when used; mark N/A only where a diagram would add no explanatory value.
13. **Editorial review:** a documented review checks terminology, notation, organization, examples, references, limitations, and consistency with connected chapters. Formal external peer review is recorded separately and is not implied.
14. **Validation record:** checks performed, results, scope, and limitations are recorded in the chapter review record and quality report. Automated checks are not described as academic peer review.

### Completion record template

```markdown
## Chapter acceptance record

- Status: NEEDS REVIEW | COMPLETED
- Review date:
- Reviewer(s):
- Objectives and assessment links:
- Technical claim/source review:
- Mathematical checks (or N/A rationale):
- Code/example execution and toolchain (or pseudocode/N/A):
- Exercises and solution/rubric evidence:
- Internal-link/navigation check:
- Diagram check (or N/A rationale):
- Editorial review:
- Open issues and limitations:
- Quality report entry:
```

## 3. Exercise and solution policy

Exercises remain linked to chapters but are classified by the primary skill they assess and by difficulty:

1. **Level 1 — Understanding:** explain distinctions, interpret a trace, identify assumptions.
2. **Level 2 — Application:** execute a method or use a definition on a new example.
3. **Level 3 — Analysis:** compare alternatives, find a counterexample, analyze complexity or failure.
4. **Level 4 — Design:** define interfaces, invariants, data formats, or system trade-offs.
5. **Level 5 — Implementation:** implement a bounded component and verify its behavior.
6. **Level 6 — Research/evaluation:** compare sources/systems, reproduce a measurement, or defend a claim.

**Solution policy:** publish the questions with concise exercise-specific grading guidance in or beside the chapter. Publish complete worked solutions in separate, clearly labeled solution material to support self-study without revealing answers in the exercise flow. Mathematical/numerical/algorithmic problems require worked solutions; open-ended designs and research tasks require a detailed rubric and examples of acceptable evidence; implementation tasks require behavior specs and tests/oracles, not a full answer implementation. When solutions are not distributable due to licensing or integrity constraints, give enough instructor/self-check criteria and state the limitation.

Every exercise record must include:

- Exercise identifier and difficulty level.
- Learning objective(s) assessed.
- Prerequisites and permitted tools/materials.
- Prompt, assumptions, constraints, and input data.
- Required output/deliverable.
- Expected reasoning/result and common errors.
- Grading rubric with observable criteria and points or mastery levels.
- Solution location or an explicit reason for rubric-only assessment.

## 4. Reusable assignment templates

These are standards templates, not populated assignments. Omit fields only with an explicit reason.

### A. Chapter exercise template

```markdown
### Exercise [ID]: [descriptive title]
- Level: 1–6
- Objectives:
- Prerequisites:
- Tools/materials:
- Problem and assumptions:
- Tasks:
- Deliverable:
- Expected result / self-check:
- Assessment rubric:
- Solution or grading-guide location:
- Common mistakes:
```

### B. Mathematical problem template

```markdown
### Problem [ID]: [concept]
- Objectives and prerequisites:
- Definitions, notation, domains, and assumptions:
- Given data:
- Tasks (derive/prove/calculate/counterexample):
- Required intermediate reasoning:
- Edge cases/model limitations to address:
- Final result format:
- Independent check or numerical verification:
- Grading rubric / partial-credit criteria:
- Worked solution location:
```

### C. Algorithm-analysis assignment template

```markdown
### Analysis [ID]: [algorithm]
- Objective and prerequisites:
- Inputs, outputs, preconditions:
- Algorithm/pseudocode under analysis:
- Required invariant and correctness argument:
- Best/average/worst-case assumptions:
- Time, space, and I/O cost requested:
- Boundary cases and counterexamples:
- Comparison baseline:
- Deliverable and reproducible trace:
- Assessment rubric / reference analysis:
```

### D. Implementation assignment template

```markdown
### Implementation [ID]: [component]
- Objective and prerequisites:
- Language/toolchain/version and starter state:
- Scope and explicit non-goals:
- Public interface and required semantics:
- Data structures and allowed dependencies:
- Invariants and error behavior:
- Required tests and expected outputs:
- Failure/concurrency/fault cases:
- Performance experiment (if relevant):
- Deliverables and reproducibility instructions:
- Acceptance tests and grading rubric:
- Known simplifications and unsafe-code policy:
```

### E. Research assignment template

```markdown
### Research [ID]: [question]
- Research question and learning objective:
- Prerequisites and scope boundaries:
- Required source classes (primary, standard, official docs, etc.):
- Source/version/access requirements:
- Claim-evidence matrix fields:
- Competing designs or hypotheses to compare:
- Required uncertainty/limitation statement:
- Deliverable and citation format:
- Reproducibility/archive details:
- Evaluation rubric (accuracy, evidence, comparison, reasoning):
```

### F. Practical laboratory template

```markdown
# Lab [ID]: [title]
- Objectives and concepts:
- Prerequisites:
- Environment, versions, resources, and setup:
- Safety/data-loss boundaries:
- Starting files/dataset:
- Tasks in ordered steps:
- Expected behavior/output at each checkpoint:
- Verification commands, tests, or manual observations:
- Failure cases and troubleshooting:
- Cleanup/reproducibility instructions:
- Deliverables:
- Assessment criteria and rubric:
- Limitations and extension questions:
```

## 5. Review workflow

1. Confirm prerequisites and evidence before drafting.
2. Design objectives, examples, practice, and references together.
3. Write only material that advances an objective.
4. Verify factual, mathematical, and code claims at their appropriate level.
5. Run local structure/link/diagram checks and required code checks.
6. Conduct editorial and technical review; separate these from automated checks.
7. Record evidence and unresolved items. Update status only after the gate.
