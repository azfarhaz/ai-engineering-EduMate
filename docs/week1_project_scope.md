# EduMate — AI-Powered Course Material Learning Assistant

## 1. Project Name

EduMate

## 2. Domain

Higher Education / AI-Assisted Learning

## 3. Problem Statement

University students often need to study lecture notes, textbooks, and PDF slides from different sources. They may find it difficult to understand complex concepts and locate specific information in lengthy course materials. EduMate aims to solve this problem by using AI to retrieve relevant information from course documents and explain it in simple language. The system will also provide source references so students can check the information themselves.

## 4. Evidence of the Problem

**Evidence 1:** Educational materials are available across different digital resources, including textbooks, lecture materials, and supplementary learning resources. Students may need to navigate these resources to find relevant information.

Source: OpenStax, Higher Education Textbooks  
<https://openstax.org/higher-education>

**Evidence 2:** Research on retrieval-augmented generation (RAG) in education identifies hallucination, outdated knowledge, and limitations in information retrieval as challenges for educational AI systems.

Source: A Systematic Survey of RAG for Educational Applications  
<https://doi.org/10.1016/J.CAEAI.2025.100417>

## 5. Initial Related Work / Existing Approaches

### Study / Solution 1: Traditional Educational Chatbots

**Source:** Towards Effective Teaching Assistants: From Intent-Based Chatbots to LLM-Powered Teaching Assistants  
<https://www.sciencedirect.com/science/article/pii/S2949719124000499>

**Key limitation:** Traditional intent-based chatbots depend on predefined questions and responses. They may struggle to understand questions that are phrased differently or require more flexible explanations.

### Study / Solution 2: Retrieval-Augmented Generation for Educational Applications

**Source:** Retrieval-Augmented Generation for Educational Application: A Systematic Survey  
<https://doi.org/10.1016/J.CAEAI.2025.100417>

**Key limitation:** RAG can improve contextual relevance, but educational systems still face challenges such as hallucinations, outdated information, and limitations in handling different types of learning materials.

### Study / Solution 3: Open Educational Resources

**Source:** OpenStax — Free College Textbooks  
<https://openstax.org/higher-education>

**Key limitation:** Open educational resources provide accessible learning content, but students may still need to search through the materials and interpret difficult concepts independently.

## 6. Engineering Gap / Limitation

Existing educational resources provide learning content, while traditional chatbots may be limited by predefined responses. General-purpose AI assistants may also produce answers that are not supported by a student's actual course materials. EduMate will address this gap by combining semantic search and AI-generated explanations with retrieval from selected course documents. It will provide source references and indicate when the available documents do not contain enough information to answer a question reliably.

## 7. Initial Project Objectives

**O1:** To design an AI-powered learning assistant that answers student questions using selected course materials.

**O2:** To develop a document-grounded question-answering system using semantic search and retrieval-augmented generation (RAG).

**O3:** To evaluate the system based on answer correctness, source relevance, groundedness, and response time.

## 8. Target Users

Undergraduate university students who need help understanding lecture notes, course textbooks, and PDF learning materials. The initial prototype will focus on students studying selected computing and engineering subjects.

## 9. Data Sources

**Source 1:** OpenStax College Physics 2e  
URL: <https://openstax.org/details/books/college-physics-2e>

**Source 2:** OpenStax Introduction to Python Programming  
URL: <https://openstax.org/subjects>

**Source 3:** OpenStax Higher Education Textbook Collection  
URL: <https://openstax.org/higher-education>

Note: Select specific publicly accessible documents relevant to the chosen subject before implementation. Confirm the exact textbook title and access permissions before including it in the final dataset.

## 10. Similar to EduAdvisor

- Both systems use AI to answer questions based on documents.
- Both retrieve relevant information before generating answers.
- Both aim to provide useful answers with supporting source references.
- Both require evaluation of answer quality, groundedness, and response time.

## 11. Different from EduAdvisor

- EduAdvisor focuses on academic advising, such as course registration, prerequisites, and graduation requirements.
- EduMate focuses on learning support, including understanding difficult concepts and finding information in lecture notes and textbooks.
- EduMate will provide simplified explanations and summaries rather than academic planning advice.
- EduMate will initially use selected course materials instead of university academic policies.

## 12. Responsible AI Risks

**Risk 1 — Hallucination:** The AI may generate an incorrect explanation or information that is not supported by the uploaded materials.  
**Mitigation:** Use RAG, show source references, test answers against reference answers, and allow the system to abstain when evidence is insufficient.

**Risk 2 — Incorrect or incomplete source material:** The assistant may provide misleading answers if the documents contain errors, outdated information, or incomplete explanations.  
**Mitigation:** Use reputable educational sources, record document titles and editions, and make clear that answers depend on the available materials.

**Risk 3 — Over-reliance on AI:** Students may accept generated explanations without checking the original materials.  
**Mitigation:** Display supporting references, encourage students to review the original content, and position EduMate as a supplementary learning tool rather than a replacement for lecturers or textbooks.
