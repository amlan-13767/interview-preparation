# Paytm Software Engineer Interview --- Master Preparation

This repository is a **technical interview revision pack** prepared for
a Software Engineer interview.

## Study order

``` mermaid
flowchart TD
    A[DSA] --> B[OOP + C++]
    B --> C[DBMS + SQL]
    C --> D[Operating Systems]
    D --> E[Computer Networks]
    E --> F[Project 1: AI Learning Path Generator]
    F --> G[Project 2: SkillSwap]
    G --> H[Machine Learning + DL/LLM/RAG]
    H --> I[Akamai Internship: Crypto-AI]
    I --> J[Mock Technical Round 1]
    J --> K[Mock Technical Round 2]
```

## Files

  -------------------------------------------------------------------------
  File                                  Purpose
  ------------------------------------- -----------------------------------
  `01_DSA_INTERVIEW.md`                 DSA concepts, patterns,
                                        complexities, coding templates and
                                        interview Q&A

  `02_OOP_CPP_INTERVIEW.md`             C++ OOP concepts, internals,
                                        examples and interview Q&A

  `03_DBMS_SQL_INTERVIEW.md`            DBMS, transactions, indexing,
                                        normalization and SQL

  `04_OPERATING_SYSTEMS_INTERVIEW.md`   Processes, threads, scheduling,
                                        synchronization, memory and
                                        deadlocks

  `05_COMPUTER_NETWORKS_INTERVIEW.md`   OSI/TCP-IP, TCP, HTTP/HTTPS, DNS,
                                        routing and REST

  `06_AI_LEARNING_PATH_GENERATOR.md`    Actual uploaded project
                                        architecture and
                                        implementation-level explanation

  `07_SKILLSWAP_PROJECT.md`             Actual uploaded SkillSwap
                                        repository explanation and
                                        interview questions

  `08_MACHINE_LEARNING_INTERVIEW.md`    ML fundamentals, algorithms,
                                        evaluation, DL, embeddings,
                                        transformers and RAG

  `09_AKAMAI_CRYPTO_AI_INTERNSHIP.md`   Internship architecture, MCP, agent
                                        workflow, guardrails, testing and
                                        Q&A
  -------------------------------------------------------------------------

## Important rule

For project/intership questions, **answer from the actual
repository/documentation first**. Do not claim a technology or feature
that is not present in the uploaded implementation.

For example, the uploaded SkillSwap repository currently uses
React/Vite + Express + TypeScript + Drizzle/PostgreSQL schema
definitions + in-memory storage, while the resume lists Next.js, MongoDB
and JWT. This discrepancy is explicitly documented in
`07_SKILLSWAP_PROJECT.md` and should be reconciled before the interview.

## Interview answer structure

For architecture questions:

> **Problem → Requirements → Architecture → Data flow → Technology
> choice → Trade-offs → Failure handling → Security → Scalability**

For project questions:

> **What I built → Why I built it → How it works → My contribution →
> Hardest problem → Fix → Trade-off → Improvement**

For coding questions:

> **Clarify → Brute force → Optimize → Explain data structure → Code →
> Complexity → Edge cases → Test**

## Primary references

The coursework structure follows the supplied GeeksforGeeks DSA, C++
OOP, DBMS, Computer Networks, Operating Systems and Machine Learning
tutorials, with additional project-specific material derived from the
uploaded project repositories and Akamai internship documents.
