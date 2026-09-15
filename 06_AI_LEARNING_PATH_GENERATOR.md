# Project 1 --- AI Learning Path Generator

> **Interview rule:** this document is based on the uploaded repository.
> Where the resume wording differs from the implementation, the
> repository is the source of truth for technical details.

## 1. Resume-level description

The project is an AI-powered personalized learning platform that
generates structured learning paths, milestones and resources based on:

-   topic
-   expertise level
-   learning goals
-   learning style
-   available time

The resume describes the stack as Python, React.js, Flask, LangChain,
ChromaDB, Sentence Transformers, PostgreSQL and Redis/RQ, and describes
a RAG-based semantic retrieval pipeline. The uploaded repository
confirms these major components, while also containing additional
caching, observability, BM25/FAISS-related and chatbot functionality.

------------------------------------------------------------------------

# 2. High-level architecture

``` mermaid
flowchart TD
    U[User] --> FE[React + Vite Frontend]
    FE --> API[Flask Backend]
    API --> AUTH[Flask-Login / Auth API]
    API --> DB[(PostgreSQL)]
    API --> Q[Redis Queue]
    Q --> W[RQ Worker]
    W --> AG[Learning Agent / Path Generator]
    AG --> RET[Retrieval Layer]
    RET --> EMB[Sentence Transformer / Embeddings]
    EMB --> VDB[(Chroma / Vector Store)]
    AG --> LLM[Gemini Model Orchestrator]
    AG --> RES[Perplexity Resource Search]
    LLM --> OUT[Structured LearningPath]
    RES --> OUT
    OUT --> Q
    Q --> API
    API --> DB
    FE --> API
```

------------------------------------------------------------------------

# 3. User request lifecycle

``` mermaid
sequenceDiagram
    participant U as User
    participant R as React
    participant F as Flask
    participant Redis as Redis/RQ
    participant W as Worker
    participant A as Learning Agent
    participant V as Vector Store
    participant L as LLM
    participant DB as PostgreSQL

    U->>R: Enter topic/profile/goals
    R->>F: POST /api/generate
    F->>Redis: enqueue learning-path job
    F-->>R: 202 + task_id
    Redis->>W: execute task
    W->>A: generate_path(...)
    A->>V: semantic retrieval
    V-->>A: relevant context
    A->>L: prompt + context + user requirements
    L-->>A: structured output
    A-->>W: LearningPath
    W-->>Redis: result
    R->>F: GET /api/status/{task_id}
    F-->>R: status/result
    R->>F: GET /api/result/{task_id}
    F->>DB: persist path + progress
    F-->>R: learning path
```

------------------------------------------------------------------------

# 4. Frontend

The uploaded frontend uses:

-   React 18
-   Vite
-   Axios
-   Tailwind CSS
-   Radix UI components
-   Vitest

The frontend collects learning requirements and communicates with Flask
APIs.

### Why React?

-   component-based UI
-   stateful interactions
-   reusable components
-   good ecosystem

### Why Vite?

Fast development server and modern frontend build pipeline.

------------------------------------------------------------------------

# 5. Backend

Flask provides:

-   authentication APIs
-   learning path APIs
-   progress APIs
-   chatbot-related APIs
-   orchestration endpoints

The backend is integrated with:

-   Flask-SQLAlchemy
-   Flask-Migrate
-   Flask-Login
-   Flask-CORS
-   PostgreSQL
-   Redis/RQ

------------------------------------------------------------------------

# 6. Why background jobs?

Learning path generation can involve:

-   retrieval
-   multiple model calls
-   external resource search
-   parsing/validation
-   job-market information
-   resource validation

Doing everything synchronously would keep the HTTP request open.

Instead:

``` text
HTTP request
   ↓
enqueue job
   ↓
return 202 + task_id
   ↓
worker performs generation
   ↓
client polls status/result
```

This improves responsiveness and isolates expensive work.

------------------------------------------------------------------------

# 7. Redis + RQ

Redis acts as the queue backend.

RQ (Redis Queue) executes Python jobs asynchronously.

In `backend/routes.py`:

``` python
q = Queue('learning-paths', connection=redis_client)
job = q.enqueue(
    'worker.tasks.generate_learning_path_for_worker',
    data
)
```

The API returns the job ID.

The worker generates the path.

### Why RQ instead of doing it in Flask?

-   avoids blocking request thread
-   supports retries/job states
-   separates web/API and compute workloads
-   can scale workers independently

------------------------------------------------------------------------

# 8. LearningAgent

`src/agent.py` contains `LearningAgent`.

It initializes:

-   `LearningPathGenerator`
-   `ModelOrchestrator`
-   `DocumentStore`
-   `VectorStore`

It also maintains state such as:

-   current path
-   user profile
-   session history
-   context
-   goal

Request types include:

-   `generate_path`
-   `modify_path`
-   `ask_question`
-   `get_resources`

------------------------------------------------------------------------

# 9. RAG pipeline

RAG = Retrieval-Augmented Generation.

``` mermaid
flowchart LR
    Q[User query/topic] --> E[Embedding]
    E --> S[Similarity Search]
    S --> K[Top relevant documents]
    K --> C[Context]
    C --> P[Prompt]
    Q --> P
    P --> L[LLM]
    L --> A[Answer / Learning Path]
```

## Why RAG?

Pure LLM generation can:

-   hallucinate
-   miss current/domain-specific information
-   generate generic resources

Retrieval supplies relevant context.

------------------------------------------------------------------------

# 10. Embeddings

An embedding converts text into a vector.

Conceptually:

``` text
"learn Python"
        ↓
[0.12, -0.73, 0.21, ...]
```

Semantically similar texts tend to have nearby representations.

Sentence Transformers are used for embedding/search functionality in the
project.

------------------------------------------------------------------------

# 11. Vector search

A vector store allows semantic retrieval.

Typical process:

``` text
documents
 → chunk/represent
 → embeddings
 → vector index

query
 → embedding
 → nearest-neighbor search
 → top-k documents
```

The repository includes Chroma-related storage and FAISS/BM25-related
retrieval components.

### Why semantic search instead of only keyword matching?

Keyword search depends heavily on exact terms.

Semantic retrieval can connect related wording such as:

``` text
"learn neural networks"
```

with:

``` text
"deep learning fundamentals"
```

even when words differ.

------------------------------------------------------------------------

# 12. ModelOrchestrator

The uploaded implementation's current `ModelOrchestrator` is configured
for **Gemini** through `GeminiClient`.

It also implements:

-   prompt optimization
-   token counting
-   caching
-   latency tracking
-   cost estimation
-   observability
-   streaming response support
-   structured response generation

Important interview point:

> Do not say the current code calls OpenAI as the primary provider. The
> uploaded implementation explicitly rejects unsupported providers and
> configures Gemini.

------------------------------------------------------------------------

# 13. Structured output

`LearningPath` is a Pydantic model.

It contains:

-   id
-   title
-   description
-   topic
-   expertise level
-   learning style
-   time commitment
-   duration
-   goals
-   milestones
-   schedule
-   prerequisites
-   total hours
-   job-market data

Each milestone contains:

-   title
-   description
-   estimated hours
-   resources
-   skills gained
-   job-market data

This gives the LLM a predictable application-level schema.

------------------------------------------------------------------------

# 14. Why Pydantic?

Without structured validation, an LLM might return:

``` json
{"foo": "random output"}
```

The application expects a known schema.

Pydantic provides:

-   validation
-   typed fields
-   required fields
-   default values
-   conversion/validation logic

------------------------------------------------------------------------

# 15. Resource search

The project contains a Perplexity-based resource search.

The search prompt explicitly requests:

-   real working resources
-   free resources
-   exact topic relevance
-   direct URLs
-   JSON output

Resources are then filtered/sanitized.

This is an important example of **LLM output validation** rather than
blindly trusting generated URLs.

------------------------------------------------------------------------

# 16. Caching

The model orchestrator checks cache before calling the LLM.

Benefits:

-   lower latency
-   lower API cost
-   fewer repeated calls

The repository also contains semantic caching utilities.

### Interview question

**Why not cache every response forever?**

Because outputs may depend on:

-   user profile
-   prompt
-   retrieved context
-   model version
-   temperature
-   freshness

Therefore cache keys/TTL/invalidation matter.

------------------------------------------------------------------------

# 17. PostgreSQL

PostgreSQL persists:

-   users
-   learning paths
-   progress
-   chat messages
-   path modifications
-   related application state

The uploaded model layer contains tables/entities for learning paths and
progress as well as conversational features.

------------------------------------------------------------------------

# 18. Authentication

The repository uses Flask-Login.

The React JSON auth API supports:

``` text
POST /api/auth/register
POST /api/auth/login
POST /api/auth/logout
GET  /api/auth/me
```

The login response creates/restores a Flask session.

Passwords are handled through the application's password hashing
mechanism rather than returning raw credentials.

------------------------------------------------------------------------

# 19. Task ownership/security

The RQ API stores `user_id` in job metadata.

When status/result is requested, `_owned_job()` checks that the current
authenticated user owns the job.

This prevents one authenticated user from simply retrieving another
user's job by task ID.

------------------------------------------------------------------------

# 20. Error handling

The backend distinguishes:

-   missing input
-   unknown task
-   task not complete
-   failed generation
-   external API quota/credit failure

The project contains explicit handling for an exhausted API-credit
condition.

------------------------------------------------------------------------

# 21. Docker architecture

``` mermaid
flowchart TD
    F[Frontend] --> B[Backend Container]
    B --> P[(PostgreSQL)]
    B --> R[(Redis)]
    R --> W[Worker Container]
    W --> V[(Vector DB / mounted data)]
```

The compose configuration defines:

-   PostgreSQL 16
-   Redis 7
-   backend
-   worker

Health checks are used for PostgreSQL and Redis.

------------------------------------------------------------------------

# 22. Strong interview questions

### Q1. Why RAG?

**Answer:** The system needs relevant learning resources and contextual
information rather than only generic LLM generation. Retrieval supplies
evidence/context before generation, reducing unsupported generic answers
and allowing the resource corpus to evolve independently of the model.

### Q2. Why embeddings?

**Answer:** They transform text into vectors where semantic
relationships can be measured, allowing similarity-based retrieval even
when the query and document use different words.

### Q3. Why vector DB?

**Answer:** A vector index makes nearest-neighbor semantic retrieval
practical over an embedding collection and can associate metadata with
retrieved documents.

### Q4. Why background jobs?

**Answer:** Generation can be slow and involves external calls. RQ
prevents long-running AI work from blocking the Flask request and lets
workers scale separately.

### Q5. Why Pydantic?

**Answer:** To validate the LLM-generated structured object against a
known schema before the rest of the application persists or displays it.

### Q6. What if the LLM fails?

**Answer:** The worker job enters a failed state; the API exposes a
user-safe error. The repository also detects specific quota/credit
exhaustion messages and returns a clearer message.

### Q7. How would you scale?

**Answer:** - multiple RQ workers - Redis-backed queue - PostgreSQL
connection pooling - vector DB scaling/partitioning as corpus grows -
caching - rate limiting - async external calls - observability - model
fallback - retries with backoff

### Q8. How would you reduce hallucinations?

**Answer:** - RAG - trusted resource sources - output schemas - URL
validation - explicit prompts - retrieval quality checks -
citations/evidence - fallback behavior

------------------------------------------------------------------------

# 23. Improvements you should mention

-   hybrid retrieval: BM25 + dense embeddings
-   reranking
-   better chunking
-   evaluation dataset
-   retrieval precision/recall measurement
-   hallucination evaluation
-   model fallback
-   retry/backoff
-   streaming generation
-   rate limiting
-   stronger prompt-injection defenses for retrieved documents
-   background-job monitoring
-   test coverage for retrieval and structured output
