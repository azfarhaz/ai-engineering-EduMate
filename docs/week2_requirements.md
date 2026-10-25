# EduMate — Week 2 Requirements Specification

## Section 0 — Project Framing & Application Scope Checkpoint

| Framing element | EduMate |
|---|---|
| **Problem Evidence** | **Evidence 1:** OpenStax provides free higher-education textbooks across many subjects ([OpenStax Higher Education](https://openstax.org/higher-education)). Students must still navigate long textbooks, lecture notes and PDF slides to find and understand specific information. **Evidence 2:** A systematic survey of RAG for educational applications identifies hallucination, outdated knowledge and limitations in information retrieval as key challenges for educational AI systems ([Systematic Survey, 2025](https://doi.org/10.1016/J.CAEAI.2025.100417)). |
| **Existing Approaches** | **1. Traditional intent-based educational chatbots** ([ScienceDirect](https://www.sciencedirect.com/science/article/pii/S2949719124000499)): answer predefined questions with predefined responses. **2. RAG-based educational systems** ([Systematic Survey](https://doi.org/10.1016/J.CAEAI.2025.100417)): retrieve relevant content to give AI answers better context. **3. Open Educational Resources, e.g. OpenStax** ([OpenStax](https://openstax.org/higher-education)): provide free, accessible textbook content. |
| **Project / Engineering Gap** | Traditional chatbots handle flexible questions poorly. General-purpose AI assistants can produce answers not supported by the student's own course materials. RAG systems still risk unsupported answers, and OER textbooks offer no interactive explanation. The remaining limitation is the lack of a **document-grounded, source-referenced, plain-language explanation tool** that says when evidence is insufficient. This is a practical engineering gap, not a claim of scientific novelty. |
| **Objective O1** | To design an AI-powered learning assistant that answers student questions using selected course materials. |
| **Objective O2** | To develop a document-grounded question-answering system using semantic search and retrieval-augmented generation (RAG). |
| **Objective O3** | To evaluate the system on answer correctness, source relevance, groundedness and response time. |
| **Intended Users** | Undergraduate university students who need help understanding lecture notes, textbooks and PDF learning materials. The prototype focuses on selected computing and engineering subjects. |
| **Supported Tasks** | (1) Process and index PDF course materials. (2) Answer natural-language questions using retrieved content. (3) Explain difficult concepts in simplified language. (4) Generate summaries of study material. (5) Show source references (document, page/section) for each answer. (6) State clearly when the materials do not contain enough evidence to answer. |
| **Information / Knowledge Domain** | Selected publicly accessible textbooks in computing and engineering, e.g. OpenStax *College Physics 2e* and *Introduction to Python Programming*. Only content from the indexed documents is in scope. |
| **Operating Context** | A Streamlit web application used by individual students for self-study, on a laptop or desktop with internet access. It acts as a supplementary tool alongside lectures and original textbooks. |
| **Important Constraints** | **Data:** only open or publicly accessible documents, with exact titles, editions and access permissions confirmed before use. **Time:** a single semester, with the prototype built from Week 3 onwards. **Budget:** limited, so open-source tools (Sentence Transformers, FAISS/ChromaDB) are preferred and LLM API usage must stay low-cost. **Technical:** Python and Streamlit; answer quality is limited by retrieval quality and the available documents; PDF extraction may handle tables, equations and figures poorly. |
| **Explicit Out-of-Scope Tasks** | Academic advising, course registration, prerequisites or graduation planning (EduAdvisor's domain). Answering from knowledge outside the indexed materials. Grading, assessing or completing assignments and exams for students. Replacing lecturers or original textbooks. Handling copyrighted or private documents without permission. Storing personal student data beyond what is needed for the session. |
| **Evaluation Focus** | Does EduMate give **correct, grounded answers** to student questions with **relevant source references**, within an **acceptable response time** and does it **abstain** when evidence is insufficient, compared against predefined questions and reference answers? |

---

### Traceability Check (Objectives → Scope → Evaluation)

| Objective | Scope decisions it drives | Evaluation focus | Requirements |
|---|---|---|---|
| **O1** — Design a learning assistant using selected course materials | Intended users, supported tasks, knowledge domain, out-of-scope tasks | Answer correctness; usefulness of simplified explanations; user feedback | FR2, FR4, FR5, FR7; NFR2, NFR6 |
| **O2** — Develop a RAG-based QA system | PDF processing, semantic search, retrieval, source references, abstention | Retrieval relevance; groundedness | FR1, FR2, FR3, FR6; NFR3, NFR5; RAI1, RAI2, RAI3 |
| **O3** — Evaluate correctness, relevance, groundedness, response time | Test question set with reference answers; performance constraints | All four metrics above plus response time | NFR1, NFR2, NFR3, NFR4; RAI1–RAI7 (all measurable) |

### Notes to Resolve Before Implementation

- Confirm the exact textbook titles and URLs. In the current data sources, "Introduction to Python Programming" links to the general OpenStax subjects page rather than the book's own page.
- Decide which subject(s) the prototype will cover first, so the document set stays small and testable.

---

## Section 1 — Functional Requirements

# Requirements Specification — EduMate

| # | Requirement — The system SHALL | Scope Boundary — SHALL NOT (where relevant) |
|---|---|---|
| FR1 | Accept PDF course materials selected for the project and make their content available for question answering. | Answer from documents that have not been selected and registered as course materials. |
| FR2 | Answer natural-language questions about concepts, definitions and procedures using content found in the selected course materials, including questions phrased differently from the source text. | Present knowledge from outside the selected materials as if it came from them, or invent facts, formulas, or examples not supported by the materials. |
| FR3 | Provide a source reference (document title and page or section) and the supporting passage for every answer based on the course materials. | Cite a document, page, or section that was not used to produce the answer. |
| FR4 | Provide a simplified explanation of a concept when the student asks for one (e.g. "explain this simply"), staying consistent with the source content. | Add claims in the simplified explanation that the source content does not support. |
| FR5 | Generate a summary of a chapter, section, or topic that the student specifies from the selected materials. | Summarise topics or documents that are not in the selected materials. |
| FR6 | State clearly that it cannot answer reliably, and suggest rephrasing or checking the original materials, when the selected materials do not contain sufficient supporting evidence. | Guess or produce a confident answer when evidence is insufficient. |
| FR7 | Decline requests outside the defined learning-support scope (e.g. academic advising, course registration, non-course topics) and redirect the student appropriately. | Complete or provide final answers to graded assignments or exams, or give medical, legal, financial, or mental health advice. |

---

## Section 2 — Non-Functional Requirements

| # | Quality Attribute | Requirement | Measurement Method | Metric | Threshold |
|---|---|---|---|---|---|
| NFR1 | Performance | The system shall return answers within an interactive response time. | Measure end-to-end latency (question submitted to answer displayed) over a test workload of ≥ 50 questions. | p90 latency | < 8,000 ms |
| NFR2 | Correctness | Answers shall be factually consistent with the selected course materials. | Score a curated evaluation set of ≥ 50 questions with reference answers using a defined rubric (correct / partially correct / incorrect). | Correct response rate (%) | ≥ 80% |
| NFR3 | Retrieval relevance | The retrieved passages shall contain the information needed to answer the question. | Compare retrieved passages with the reference source location for each question in the evaluation set. | Hit rate @ top 5 passages | ≥ 85% |
| NFR4 | Cost | Model and API usage shall stay within the project budget. | Record token and API usage over the evaluation workload. | Cost per 100 queries | < US$1.00 (illustrative) |
| NFR5 | Maintainability | A new course document shall be added and become searchable without rebuilding unrelated components or changing application code. | Run a controlled document-addition test with a PDF of up to 300 pages. | Time from upload to searchable | < 15 min |
| NFR6 | Usability | Students shall find the answers and explanations clear and helpful. | Collect a short rating questionnaire from ≥ 5 student testers over a set task list. | Mean helpfulness rating (1–5) | ≥ 4.0 |

---

## Section 3 — Responsible AI Requirements

| # | Responsible AI Risk | Requirement | Measurement Method | Metric | Threshold |
|---|---|---|---|---|---|
| RAI1 | Hallucination / Groundedness | Every factual claim in a document-grounded answer shall be supported by the retrieved passages. | Manually review ≥ 50 answers, checking each claim against the cited passages. | Groundedness rate (% of answers with all claims supported) | ≥ 90% |
| RAI2 | Transparency / Verifiability | The system shall provide a verifiable source reference for every document-grounded answer. | Check all evaluation answers for a reference, then check whether the cited page or section supports the answer. | Citation presence rate; citation–source match rate | 100% presence; ≥ 95% match |
| RAI3 | Unsupported answers | The system shall abstain and say that evidence is insufficient when the materials do not support an answer. | Run ≥ 20 questions whose answers are absent from the materials, plus the answerable set to check false refusals. | Correct abstention rate; false abstention rate on answerable questions | ≥ 90%; ≤ 15% |
| RAI4 | Scope / Academic integrity | The system shall decline out-of-scope requests, including requests to complete graded work, and redirect the student. | Run a scope test set of ≥ 20 out-of-scope queries. | Refusal-and-redirect success rate | ≥ 95% |
| RAI5 | Over-reliance on AI | The system shall tell students that answers are AI-generated and should be verified against the original materials. | Inspect all responses in the evaluation set for the notice and a link or reference to the original source. | Responses showing notice and reference (%) | 100% |
| RAI6 | Incorrect or outdated source material | The system shall record the title, edition or version, and source URL for every indexed document. | Audit the document register against the indexed documents. | Indexed documents with complete metadata (%) | 100% |
| RAI7 | Privacy | The system shall not store personal identifiers from student queries beyond the active session. | Run test sessions containing dummy personal data, then inspect stored data and logs. | Number of persisted personal identifiers | 0 |

---

## Reflection

**Which Responsible AI requirement will be the most difficult to measure or enforce technically?**

RAI1 (groundedness) will be the hardest. An answer can cite a correct page yet still include a claim that page does not support, so checking it means comparing each individual claim with the retrieved text. Automated checks, such as using an LLM as a judge, are imperfect and can themselves be wrong, so I plan to rely on manual review, which is slow and limits how many answers I can test. It is also hard to enforce, the language model can blend retrieved text with its own background knowledge and a plain-language explanation (FR4) can drift from the source without the change being obvious. RAI3 (abstention) is closely related, because a stricter evidence threshold reduces hallucination but increases false refusals so the two thresholds will need tuning together.
