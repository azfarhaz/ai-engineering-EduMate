# Evaluation Criteria — EduMate


## Step B1 — Evaluation Criteria Table

| Objective / Requirement ID | Evaluation Criterion | Capability Bucket | Measurement Method | Metric | Threshold | User / Business Outcome |
|---|---|---|---|---|---|---|
| O3 / NFR2, FR2 | Answer Correctness | Domain-specific capability | Score answers to a curated set of ≥ 50 answerable questions against reference answers using a correct / partially correct / incorrect rubric. | Correct response rate (%) | ≥ 80% | Students receive reliable answers to routine questions about their course material. |
| O2 / NFR3, FR1 | Retrieval Relevance | Domain-specific capability | For each question in the evaluation set, check whether the top 5 retrieved passages include the reference source location. | Hit rate @ top 5 passages | ≥ 85% | The system finds the right part of the textbook, so answers rest on relevant evidence. |
| O2 / RAI1, FR2 | Groundedness | Generation capability | Manually review ≥ 50 answers, checking each factual claim against the retrieved passages. | Groundedness rate (% of answers with all claims supported) | ≥ 90% | Students are not given invented or unsupported information presented as course content. |
| O1 / FR3, RAI2 | Source Citation and Support | Instruction-following capability | Inspect all document-grounded answers for a reference, then verify whether the cited page or section supports the answer. | Citation presence rate; citation–source match rate | 100% presence; ≥ 95% match | Students can verify answers against the original materials. |
| O2 / FR6, RAI3 | Abstention When Evidence Is Insufficient | Instruction-following capability | Run ≥ 20 questions whose answers are absent from the materials, and use the answerable set to check false refusals. | Correct abstention rate; false abstention rate | ≥ 90%; ≤ 15% | Students are told when the materials cannot answer, instead of being misled. |
| O1 / FR7, RAI4 | Out-of-Scope Refusal | Instruction-following capability | Run a scope test set of ≥ 20 out-of-scope queries (advising, graded-assignment solutions, non-course topics). | Refusal-and-redirect success rate | ≥ 95% | The tool stays a learning aid and does not complete graded work or give advice outside its scope. |
| O1 / FR4, NFR6 | Explanation Clarity | Generation capability | Rate simplified explanations on the clarity rubric below (see Optional Rubric), using ≥ 5 student testers over a set task list. | Mean clarity score (1–5) | ≥ 4.0 | Students can understand difficult concepts in simpler language. |
| O3 / NFR1 | Response Latency | Cost and latency | Measure end-to-end response time (question submitted to answer displayed) over a workload of ≥ 50 questions. | p90 latency | < 8,000 ms | Students get responses fast enough for interactive study. |
| O3 / NFR4 | Inference Cost | Cost and latency | Record token and API usage over the same evaluation workload. | Cost per 100 queries | < US$1.00 (illustrative) | The prototype can be run and tested within the project budget. |

## Shared Evaluation Datasets

| Dataset | Size | Used for |
|---|---|---|
| Answerable questions with reference answers and source locations | ≥ 50 | Correctness, retrieval relevance, groundedness, citation, clarity, latency, cost |
| Unanswerable questions (answer absent from the materials) | ≥ 20 | Abstention |
| Out-of-scope queries | ≥ 20 | Out-of-scope refusal |

The answerable set will mix factual, conceptual, and rephrased questions across the selected documents. The same workload is reused for latency and cost so that results are comparable.

## Optional Rubric — Explanation Clarity (1–5)

| Score | Description |
|---|---|
| 5 | Clear and easy to follow; accurate; uses plain language; no unexplained jargon. |
| 4 | Mostly clear; accurate; minor jargon or wording issues. |
| 3 | Understandable but needs effort; some unclear or overly technical parts. |
| 2 | Confusing or partly inaccurate. |
| 1 | Unclear, inaccurate, or unrelated to the question. |

## Reflection

**Which evaluation criterion is most important for determining whether EduMate will be useful to its intended users?**

Groundedness (RAI1) is the most important criterion. EduMate was designed to close the gap left by general-purpose AI assistants, which can produce answers that are not supported by a student's own course materials. A system that is fast and fluent but sometimes unsupported would fail this central purpose. Students also tend to use a tool like this because they do not yet understand the topic so they are poorly placed to notice an unsupported claim on their own.

Groundedness also underpins the other criteria. An answer can match the reference answer by chance while still containing claims the sources do not support and a citation is only useful if the cited page actually backs the answer. Latency, cost and clarity all affect the user experience but they only matter if the answers can be trusted.
