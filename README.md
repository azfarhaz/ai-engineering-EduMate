# EduMate — AI-Powered Course Material Learning Assistant

> EduMate helps university students understand course materials by answering questions and explaining concepts using AI.

## Problem Statement

University students often struggle to understand complex concepts and find specific information in lengthy lecture notes and textbooks. EduMate uses AI to retrieve relevant information from course documents and generate simple, understandable explanations supported by source references.

## Evidence of the Problem

- Open educational resources provide students with textbooks and supplementary learning materials.
- Research on educational RAG systems identifies hallucination and information retrieval as important challenges for AI-based learning support.

## Initial Related Work / Existing Approaches

- **Traditional educational chatbots:** Can answer predefined questions but may struggle with flexible questions.
- **RAG-based educational systems:** Retrieve relevant information to support AI-generated answers but may still produce unsupported information.
- **Open educational resources:** Provide accessible textbooks, but students may still need help locating and understanding specific concepts.

## Engineering Gap / Limitation

Existing learning resources do not always provide interactive explanations, while general-purpose AI assistants may generate answers that are not supported by the student's course materials. EduMate aims to provide document-grounded answers, simplified explanations, and source references.

## Project Objectives

- **O1:** To design an AI learning assistant that answers questions using selected course materials.
- **O2:** To develop a RAG-based system with semantic search and document retrieval.
- **O3:** To evaluate answer correctness, source relevance, groundedness and response time.

## Target Users

Undergraduate university students, initially focusing on selected computing and engineering subjects.

## Main Features

- PDF course material processing
- Question answering using natural language
- Simplified explanations of difficult concepts
- Study material summaries
- Source references for generated answers

## Data Sources

- [OpenStax Higher Education](https://openstax.org/higher-education)
- [OpenStax textbook collection](https://openstax.org/subjects)
- Additional publicly accessible textbooks relevant to the selected subject will be documented before implementation.

## Technology Stack

- Python
- Streamlit
- Sentence Transformers
- FAISS or ChromaDB
- Large Language Model (LLM)
- Retrieval-Augmented Generation (RAG)

## Evaluation Plan

The system will be evaluated using predefined questions and reference answers. Evaluation will consider answer correctness, retrieval relevance, groundedness, response time and user feedback.

## Responsible AI

- Display source references to support verification.
- Avoid generating unsupported answers when evidence is insufficient.
- Use reputable learning materials and record document versions.
- Encourage students to verify answers using original course materials.

## Weekly Progress

| Week | Focus | Deliverable | Status |
|---|---|---|---|
| W1 | Project scoping and GitHub setup | README and project scope | In progress |