# Data Inventory — EduMate

## 1. Required Data (from Topic 2 requirements and V1 architecture)

| Requirement ID | Needed information / feature | Candidate source | Why usable / constraint |
|---|---|---|---|
| FR1, FR2: Answer from selected course materials | Explanations and definitions of introductory programming concepts (Chapters 1–15) | `data/Introduction_to_Python_Programming-WEB.pdf` (official OpenStax PDF) | Peer-reviewed, free, numbered sections. CC BY-NC-SA 4.0. Tables, formulas, and code layout are damaged in text extraction. |
| FR3, RAI2: Source references | Section number and page for each passage (e.g. "1.2 Input/output") | Same PDF: section headings and printed page numbers | Consistent headings make references checkable. Printed page differs from PDF page, so store both. |
| FR1 (secondary source) | Equation-heavy physics content to stress-test parsing | College Physics 2e (OpenStax PDF; file name to be recorded) | Broad coverage. **Restricted:** OpenStax requires written permission to ingest the book into LLM or generative AI offerings. Not used until resolved. |
| NFR2, NFR3: Correctness and retrieval | ≥ 50 answerable questions with reference answers and source locations | Team-written from the Python PDF (D4) | Answers come directly from the book. Needs a second person to check. Include table- and code-based questions. |
| FR6, RAI3, FR7, RAI4: Abstention and scope | ≥ 20 unanswerable questions and ≥ 20 out-of-scope queries | Team-written (D4) | Project-created, so size is controllable. Each unanswerable question must be confirmed absent from the book. |
| RAI6, NFR5: Provenance and version | Title, edition, ISBN, year, URL, licence, download date, page count, checksum | Document register (D3) plus PDF front matter | OpenStax states ISBN 978-1-961584-45-7, original year 2024, ©2026. Checksum and page count come from the local file. |

## 2. Source Records

| Field | D1 — Introduction to Python Programming | D2 — College Physics 2e |
|---|---|---|
| **Source identity** | OpenStax (Rice University). Authors: Das, Lawson, Mayfield, Norouzi. | OpenStax (Rice University). Authors: Urone, Hinrichs. |
| **URL** | <https://openstax.org/details/books/introduction-python-programming> | <https://openstax.org/details/books/college-physics-2e> |
| **Data type** | PDF: text, code listings, tables, figures, quiz questions, exercises. Preface, 15 chapters, Answer Key (p. 361), Index (p. 385). | PDF expected: text, equations, tables, figures, problem sets. |
| **Purpose** | Primary source for question answering, explanations, summaries, and references. | Secondary source for equation-heavy content. |
| **Version / date** | Original 2024; PDF notice ©2026; digital ISBN 978-1-961584-45-7. | Second edition; published 13 July 2022. |
| **Access rights** | CC BY-NC-SA 4.0 (PDF front matter). Attribution: "Access for free at openstax.org". | CC BY-NC-SA 4.0 (current OpenStax page). **Prior written permission needed for LLM or generative AI ingestion.** |
| **Inspected** | First ~68 printed pages of the official PDF (extracted text). | Web-view Preface page only; PDF not yet inspected. |
| **Local file details** | Size, page count, SHA-256: **To confirm** | Size, page count, SHA-256: **To confirm** |

Third-party copies of D1 report different page counts (397 and 415), so only the official OpenStax PDF should be used.

## 3. Inspection Evidence

### Inspection 1 — D1 PDF (front matter, Chapters 1–2, start of Chapter 3)

| # | Observation | Evidence | Implication |
|---|---|---|---|
| 1 | Running headers and footers repeat on every page. | "Access for free at openstax.org" and labels like "8 1 • Statements" sit between paragraphs. | Strip from chunk text; keep attribution in metadata and the interface. |
| 2 | Printed page numbers differ from PDF positions. | Preface is page 1; Chapter 1 starts at printed page 7. | Store both numbers for citations (FR3). |
| 3 | Quiz questions appear without answers. | "CONCEPTS IN PRACTICE" lists a/b/c options; answers are only in the Answer Key at the end. | Exclude or tag these blocks so bare questions are not retrieved as content. |
| 4 | Tables lose row and column structure. | Table 1.1 (print examples) and Table 2.4 (math functions) come out as run-together text. | Add table questions to the test set to measure retrieval. |
| 5 | Formulas are partly missing. | Section 2.4 reads "...values that can be represented are and , respectively." | Formula-based answers may be wrong; record as a known limitation. |
| 6 | Code and non-text content are degraded or absent. | Example 1.2 shows inline line numbers; video and code-runner links appear as "Access multimedia content (http://...)". | Check indentation in later chapters; video content is unavailable to the system. |

### Inspection 2 — D2 web view (Preface page)

| # | Observation | Evidence | Implication |
|---|---|---|---|
| 1 | Explicit AI-use restriction. | The citation panel says the book may not be used to train or be ingested into LLMs or generative AI offerings without OpenStax's written permission, and links a reuse request form. | Resolve before use: ask the lecturer or submit the request form. |
| 2 | Licence differs from older sources. | Current page says CC BY-NC-SA 4.0; older listings say CC BY 4.0. | Record the current statement and retrieval date. |
| 3 | Exercise answers are not in the book. | The preface says answers are in an instructor guide, with selected answers in a student guide. | Keep exercises out of the answerable set (FR7). |

## 4. Open Items

- [ ] Download the PDFs into `/data/` and record size, page count, and SHA-256.
- [ ] Inspect a middle chapter and the Answer Key in the local D1 file; inspect the D2 PDF if permission is confirmed.
- [ ] Check the D1 web-view citation panel for the same AI-use restriction, and resolve the D2 permission.
