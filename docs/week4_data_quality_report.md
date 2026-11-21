# Data Quality & Bias Report — EduMate (Week 4)

Author: Muhammad Haziem Azfar Bin Mohd Ransaimon (A24AI0057)

**Evidence labels:** **Observed** (seen in a file, output, or code), **Inferred risk** (plausible, not tested), **N.V.** (Not Verified, with the smallest next check). **Evidence limit:** only three chunks were inspected by hand (below the 10–20 recommended), so most findings are counts, code inspection, or N.V.

## 1. Project and Data Overview

| Item | Record |
|---|---|
| **Project and purpose** | EduMate, an AI learning assistant for undergraduate students. AI task: answer questions from selected course materials with source references (retrieval is built in later topics and is not audited here). |
| **Two Topic 2 requirements** | **FR3:** every answer includes a source reference (document title and page or section) and the supporting passage. *Needs:* a source and page/section locator on each chunk. **NFR3:** retrieved passages contain the information needed to answer (hit rate ≥ 85% at top 5). *Needs:* readable, self-contained chunks without noise. |
| **Topic 3 pathway** | Pathway A (PDF / text); unstructured text stored as chunks. |
| **Audited output and manifest** | Part C output `data/pipeline_v1/documents/prepared_text.jsonl` (1,117 chunks) with `data/pipeline_v1/documents/pipeline_manifest.json`. Part B output (`data/processed/prepared_text.jsonl`, `data/week3_manifest.json`) is referenced for the consistency check only. |
| **Source** | S01: *Introduction to Python Programming*, OpenStax, official PDF, 397 pages, ISBN 978-1-961584-45-7, original 2024, ©2026 notice (`docs/week3_data_inventory.md`). |
| **Sample and limits** | Unit: one chunk (up to 500 characters). **Observed sample:** 3 consecutive chunks (ids 301–303), read in full by hand. Selection was a convenience sample, so it is not representative. It sits around the middle of the book and happens to include a table, a quiz block, and mid-sentence cuts. **Population counts:** 1,117 chunks (total), 187 with footer text, and the Topic 3 chunking counts (1,109 fixed vs 1,117 paragraph-aware). Nothing is generalised beyond these. |

## 2. Five-Dimension Quality Scorecard

| Dimension | Finding | Evidence | Impact on the task | Decision |
|---|---|---|---|---|
| **Completeness** | No page or section locator is stored; `page` is `None` by design. | **Observed** in the Cell 8A code. Count in the exported file: **N.V.** (next check: count null `page` values). | FR3 references can name the document, not the page. | Warning |
| **Consistency** | Part B and Part C records differ: text `record_id` vs numeric `id`; relative vs full local `source` path; checksum per record only in Part B. | **Observed** in the Topic 3 code. Identical chunk text is **Inferred** (same configuration) and **N.V.** | Later code must handle two formats; full paths expose the local folder. | Warning |
| **Accuracy** | Faithfulness to the PDF has not been compared. Chunk 302 (a truth table) reads correctly. Formula values were missing in one passage in the Topic 3 inspection. | **Observed** (chunk 302; Topic 3 inventory). Spot-check against the PDF: **N.V.** (next check: Ctrl+F 10 chunks in the PDF). | Missing values or scrambled tables could give wrong answers (NFR2, NFR3). | N.V. |
| **Timeliness** | Source is 2024, ©2026; OpenStax updates the web view periodically. The manifest marks the version "Not verified". | **Observed** (PDF front matter; manifest code). Download date: **N.V.** | No freshness requirement in Topic 2; the risk is that the exact edition cannot be proven later (RAI6). | Review |
| **Uniqueness** | Exact duplicate chunks not counted. The footer repeats across chunks but is not a duplicate record. | 187 chunks contain the footer (**Observed**). Duplicates: **N.V.** (next check: count identical chunk texts). | Duplicates would take several top-5 slots (NFR3). | N.V. |

## 3. Validation Findings

| Rule | Condition | Tested | Result | Action on failure |
|---|---|---|---|---|
| **V1** (FR3, RAI2) | Every record has a non-empty `source` and a non-null `page` or section. | Code review of the record writer; 3 chunks read by hand | The code writes `page: None` for every record, so all 1,117 are expected to fail the page condition (**Inferred** from code; measured count **N.V.**). `source` is set from the file path in the code (measured **N.V.**). | Hard fail if `source` is empty. Warning if `page` is null (known Topic 3 limitation); hard fail once a page-aware splitter exists. |
| **V2** (NFR3) | No chunk contains the text "Access for free at openstax.org". | All 1,117 chunks | **Observed:** 187 fail (16.7%), 930 pass. Example chunk id was not recorded (**N.V.**). Chunks empty after stripping: **N.V.** | Review: strip the footer and keep the chunk. Quarantine any chunk that is empty after stripping. |

## 4. Duplicate, Anomaly and Coverage Findings

**Duplicates:** **N.V.** Smallest next check: count identical chunk texts in `prepared_text.jsonl`.

**Anomalies (Observed):**
- Chunks 301 and 302 split mid-sentence ("...if a business'" / "'s two condition operands...").
- Chunk 303 holds multiple-choice quiz questions with no answers (the answers are in the Answer Key elsewhere in the book).
- Paragraph-aware chunking produced only 8 more chunks than fixed (1,117 against 1,109), so it mostly behaves like fixed-size cutting.

**Coverage of two task-relevant cases**

| Case | Expected evidence | Observed evidence / gap | Possible effect |
|---|---|---|---|
| **Code-based questions** | Code listings with line breaks and indentation kept | The Topic 3 inspection of the first ~68 pages of the official PDF (not the local file) showed inline line numbers and one listing with lost indentation (**Observed**). Code chunks in the prepared output: **N.V.** | Answers about code may be weaker than answers about definitions (**Inferred risk**). |
| **Table and formula questions** | Tables kept in order; formulas and exponents present | Chunk 302 (truth table) is readable (**Observed**). Formula values were missing in one Topic 3 passage (**Observed**). Table chunks counted and checked against the PDF: **N.V.** | Answers relying on a missing value or scrambled table could be incomplete (**Inferred risk**). |

*Limit:* these findings come from counts, three chunks, and one book. They do not show how the system will perform.

## 5. Bias Assessment

**Pipeline-induced risk (Inferred).** *Preparation decision:* text-only extraction, whitespace cleaning, no footer removal, fixed 500-character cutting. 

→ *Gap:* plain prose survives well, while code layout, formulas, and tables are partly degraded, and 187 chunks carry boilerplate. 

→ *Effect:* retrieval and generation may serve definition-style questions better than code, table, or formula questions, giving uneven quality by question type. 

→ *Action:* add code, table, and formula questions to the evaluation set, report results by question type, strip footers, and review table and code chunks.

**Representation risk (Inferred).** Only one textbook was processed, so the application covers one subject and one teaching style. This is a scope limit, not proven unfairness. Action: state the scope in the interface and add a second approved source when permission is clear.

Neither risk is a measured system result; performance by question type is evaluated later.

## 6. Privacy, Rights and Governance

| Question | Record |
|---|---|
| **Provenance and version** | S01 as in Section 1. The manifest stores the source and output SHA-256 checksums (**Observed**, values in the manifest) but marks the version "Not verified". Download date: **N.V.** |
| **Rights and permissions** | CC BY-NC-SA 4.0 per the PDF front matter (**Observed**): attribution, non-commercial use, same-licence adaptations. OpenStax states on its College Physics 2e page that the book may not be ingested into LLMs or generative AI offerings without written permission (**Observed**, Topic 3 inventory). Whether this applies to S01: **N.V.** (next check: read the citation panel on the book's web page). Repository visibility: **N.V.** |
| **Privacy** | No personal data is needed or expected. The front matter names authors and reviewers (public professional information). Part C records store full local file paths (**Observed**, code). |
| **Consent or collection basis** | Not applicable: no human participants. The basis for use is the published open licence, subject to the unresolved permission above. |
| **Maintenance** | The project owner reviews the data. Revalidate on a new edition or errata, a pipeline or chunking change, a new source, or a permission change. Retire the prepared text if AI use is refused. |

**Required safeguards:** (1) check the S01 citation panel and obtain permission or the lecturer's confirmation before any chunk goes to a language model service; (2) replace local paths with relative paths before sharing.

## 7. Mitigation and Impact on Requirements

| Priority improvement | Owner | When | Requirements affected | Risk that remains |
|---|---|---|---|---|
| **1.** Strip footer text and add a page or section locator in the next pipeline iteration. | Project owner | Before the Topic 5 retrieval work starts | FR3, RAI2, NFR3 | Mid-sentence cuts; degraded tables, formulas, and code |
| **2.** Resolve the AI-use permission, record download date and edition in the manifest, and complete the N.V. checks (duplicate count, accuracy spot-check on 10–20 chunks). | Project owner, with the lecturer for permission | Before any chunk is sent to a model service | RAI6, RAI1, NFR2 | Single-source coverage; quiz questions without answers |

## 8. Quality-Gate Decision

**GO WITH CONDITIONS.**

**Scope:** the Part C prepared text (1,117 chunks from S01 only), for developing and testing the later retrieval prototype in this coursework. Not for public release, other sources, or College Physics 2e.

**Evidence:** (1) 187 of 1,117 chunks (16.7%) contain footer text (V2, **Observed**); (2) no page or section locator is stored, so FR3 page-level references cannot yet be produced (V1, **Observed** in code); (3) AI-use permission, accuracy against the PDF, and duplicates are **N.V.**

**Conditions:** complete both improvements in Section 7.

**Re-review triggers:** a new chunking method or pipeline version; a new edition or source; duplicates found or any spot-check mismatch; a change in the permission outcome.

**Supporting evidence:** Topic 3 notebook outputs, `docs/week3_data_inventory.md`, `docs/week3_pipeline_decisions.md`, and the manifests listed in Section 1.
