# Architecture Decisions — EduMate

## Step C1 — Simplest Architecture

```
User Query → AI Application → Language Model Service → Response
```

- **Main user interaction:** A student types a natural-language question about their course material and reads the answer.
- **AI capability needed:** A language model service that generates an answer or explanation.

**Can this simple architecture satisfy the Part A and Part B requirements?**

| Requirement / Criterion | Supported by simple architecture? | Why |
|---|---|---|
| FR4 / NFR6 — Simplified explanations, clarity | Yes | The model can explain concepts in plain language. |
| NFR1 — Latency | Yes | One model call per question. |
| FR2 / NFR2 — Answers consistent with *selected course materials* | **No** | The model only knows general information and has no access to the student's documents. |
| FR3 / RAI2 — Verifiable source references | **No** | The model has no document source to cite and may invent references. |
| FR6 / RAI3 — Abstain when evidence is insufficient | **No** | Nothing tells the model whether the materials contain an answer. |
| FR7 / RAI4 — Refuse out-of-scope requests | **Partly** | An instruction can ask the model to refuse, but this is not reliable or testable on its own. |
| FR1 / NFR5 / RAI6 — Add documents and record versions | **No** | There is no document handling or document register. |
| RAI1 — Groundedness | **No** | Answers cannot be tied to retrieved evidence. |
| NFR1–NFR4 / RAI1–3 — Evaluation evidence | **No** | There is nowhere to capture questions, evidence, answers, latency, and cost. |

**Conclusion:** The simple flow supports explanation and latency, but not grounding, source tracing, abstention, reliable scope control, document management, or evaluation. Components are added in C2 to cover these gaps.

---

## Step C2 — Architecture Needs from Requirements

| Requirement ID | What does the requirement need? | Architecture Response |
|---|---|---|
| FR1 / NFR5 | Selected PDF course materials must be added and become searchable without rebuilding unrelated parts. | Document Processing component that prepares documents for search, separate from answering. |
| FR1 / RAI6 | Each document's title, version, and URL must be recorded. | Document metadata held alongside the indexed content in the Knowledge Source. |
| FR2 / NFR2 / NFR3 | Answers must come from the selected materials and the right passages must be found. | Knowledge Source plus Knowledge Retrieval that returns relevant passages for each question. |
| FR3 / RAI2 | Answers must include a reference and the supporting passage. | Passage and document metadata carried through to the response, so the model's answer can be traced to its source. |
| FR6 / RAI3 | The system must say when evidence is insufficient. | Evidence Sufficiency Check between retrieval and generation, with an abstain message path. |
| FR7 / RAI4 | Out-of-scope requests must be refused and redirected. | Scope / Safety Control placed before retrieval and generation. |
| FR4 / FR5 / NFR6 | Simplified explanations and summaries must stay consistent with the sources. | Language Model Service that generates only from the retrieved passages provided to it. |
| RAI1 | Claims must be supported by the retrieved passages. | Generation restricted to retrieved context; Evaluation Point stores passages and answers for claim-level review. |
| RAI5 | Students must be told to verify against original materials. | Response format in the Application Interface that includes a verification notice and source references. |
| NFR1 / NFR4 | Latency and cost must stay within thresholds. | Evaluation Point that records latency and usage; a small number of components on the main path. |
| RAI7 | Personal identifiers must not be stored beyond the session. | Evaluation Point limited to non-personal data; no persistent user profile. |

---

## Step C3 — V1 Conceptual Architecture

![EduMate V1 architecture](week2_architecture_v1.png)

### Component Summary

| Component | Function | Justified by |
|---|---|---|
| Student | Asks questions and reads answers. | Target users (O1) |
| Application Interface | Accepts questions; displays answers, sources, and the verification notice. | FR2–FR5, RAI5 |
| Scope / Safety Control | Checks whether a request is in scope; refuses and redirects if not. | FR7, RAI4 |
| Knowledge Retrieval | Finds passages in the selected materials that are relevant to the question. | FR2, NFR3 |
| Knowledge Source | Holds the indexed course content and document metadata. | FR1, FR3, RAI6 |
| Evidence Sufficiency Check | Decides whether the retrieved passages are enough to answer; triggers abstention if not. | FR6, RAI3 |
| Language Model Service | Generates answers, simplified explanations, and summaries from the retrieved passages. | FR2, FR4, FR5, RAI1 |
| Response | Answer with source references and verification notice, or a refusal or abstain message. | FR3, FR6, FR7, RAI2, RAI5 |
| Selected Course Materials | Approved PDF documents with recorded versions. | FR1, RAI6 |
| Document Processing | Prepares new documents so they can be searched (offline). | FR1, NFR5 |
| Evaluation Point | Records questions, retrieved passages, answers, latency, and cost for evaluation. | NFR1–NFR4, RAI1–RAI3, RAI7 |

Components are labelled by function. Specific models, retrieval methods, databases, and frameworks will be selected in later topics.

---

## Step C4 — Architecture Decision Records (ADR)

| Decision | Requirement / Constraint | Alternatives Considered | Selected Option | Trade-off Accepted |
|---|---|---|---|---|
| **ADR1:** Include knowledge access from the selected materials | FR2, FR3, NFR2, RAI1: answers must be grounded in the selected course materials and traceable to sources. | (a) Language model only; (b) pass the whole textbook to the model on every question; (c) retrieve relevant passages first. | Retrieve relevant passages from a Knowledge Source before generation. | More system complexity, and answer quality now depends on retrieval quality. In return, answers are better grounded and traceable. |
| **ADR2:** Include an explicit evidence sufficiency check | FR6, RAI3, RAI1: the system must not give unsupported answers. | (a) Prompt instruction only; (b) explicit check before generation. | Explicit Evidence Sufficiency Check with an abstain path. | Some answerable questions will be refused wrongly (false abstentions) and there is a little extra processing. In return, fewer unsupported answers. |
| **ADR3:** Include an explicit scope control | FR7, RAI4: out-of-scope and graded-work requests must be refused. | (a) Instruction to the model only; (b) explicit scope-control component. | Explicit Scope / Safety Control before retrieval. | Possible false refusals of borderline learning questions and added latency (NFR1). In return, a testable, repeatable control. |
| **ADR4:** Keep document processing separate from answering | FR1, NFR5, RAI6: new documents must be added without rebuilding the application, with versions recorded. | (a) Process documents at question time; (b) separate offline processing with a document register. | Separate Document Processing step feeding the Knowledge Source. | Documents must be prepared before use, and PDF parsing may handle tables or equations poorly. In return, faster answers and easier maintenance. |
| **ADR5:** Include an Evaluation Point from V1 | NFR1–NFR4, RAI1–RAI3, RAI7: evidence is needed to show thresholds are met. | (a) Add evaluation later; (b) capture evaluation data from the start. | Evaluation Point that records non-personal interaction data. | Extra logging effort now, and care is needed to avoid storing personal data. In return, evaluation evidence is available from the first prototype. |

---

## Initial Requirements Traceability Matrix

| Objective | Requirement | Evaluation Criterion | Architecture Element | Later Evidence |
|---|---|---|---|---|
| O1 | FR7, RAI4 | Out-of-Scope Refusal (≥ 95%) | Scope / Safety Control | Results from the ≥ 20 out-of-scope query test |
| O1 | FR3, RAI2 | Source Citation and Support (100% presence; ≥ 95% match) | Knowledge Source + Response format | Citation audit of the evaluation set |
| O1 | FR4, NFR6 | Explanation Clarity (mean ≥ 4.0) | Language Model Service | Student tester ratings on the clarity rubric |
| O2 | FR2, NFR3 | Retrieval Relevance (hit rate @ 5 ≥ 85%) | Knowledge Retrieval + Knowledge Source | Retrieval test results against reference source locations |
| O2 | RAI1 | Groundedness (≥ 90%) | Retrieval + Language Model Service + Evaluation Point | Manual claim-level review of ≥ 50 answers |
| O2 | FR6, RAI3 | Abstention (≥ 90% correct; ≤ 15% false) | Evidence Sufficiency Check | Results from the ≥ 20 unanswerable question test and false refusal check |
| O2 | FR1, NFR5, RAI6 | Document addition time (< 15 min); metadata complete (100%) | Document Processing + Knowledge Source | Document-addition test and document register audit |
| O3 | NFR2 | Answer Correctness (≥ 80%) | Whole answering path | Rubric scoring of the ≥ 50 question set |
| O3 | NFR1 | Response Latency (p90 < 8,000 ms) | Evaluation Point | Latency log from the evaluation workload |
| O3 | NFR4 | Inference Cost (< US$1.00 per 100 queries) | Evaluation Point | Usage log from the evaluation workload |


## Reflection

**Which architecture decision involved the most important trade-off? Which requirement drove the decision, and what trade-off did you accept?**

ADR2, the explicit evidence sufficiency check, involved the most important trade-off. It was driven by RAI3 (abstain when evidence is insufficient) and supports RAI1 (groundedness), which I identified as the most important evaluation criterion. A system that always answers will sometimes produce unsupported claims, and students may not notice them. The check lets EduMate say "the materials do not cover this" instead.

The trade-off I accepted is false abstentions. A stricter check reduces unsupported answers but also refuses questions that the materials could have answered, which makes the tool less helpful and adds a little processing time. This is why RAI3 has two thresholds (≥ 90% correct abstention and ≤ 15% false abstention), and why the check's strictness will have to be tuned during evaluation rather than fixed now.
