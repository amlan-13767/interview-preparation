# Project 2 --- SkillSwap

## IMPORTANT REPOSITORY / RESUME RECONCILIATION

Your resume lists:

> NextJS, ReactJS, TypeScript, Tailwind CSS, ExpressJS, MongoDB, JWT

However, the **uploaded repository** shows a different implementation:

-   React + Vite frontend
-   Express backend
-   TypeScript server
-   Drizzle ORM schema for PostgreSQL
-   `@neondatabase/serverless` dependency
-   `MemStorage` in the current storage implementation
-   Passport/session-related dependencies
-   no MongoDB driver
-   no JWT implementation in the inspected authentication routes

Therefore:

**Do not blindly claim MongoDB/JWT/Next.js internals if the interviewer
asks you to explain the uploaded implementation.** Reconcile the resume
with the version you actually want to present.

------------------------------------------------------------------------

# 1. Product goal

SkillSwap is a peer-to-peer skills exchange platform.

A user can:

-   create a profile
-   specify skills offered
-   specify skills wanted
-   specify availability
-   discover public users
-   send swap requests
-   accept/reject requests
-   manage swap-request state

``` mermaid
flowchart TD
    U[User] --> P[Profile]
    P --> O[Skills Offered]
    P --> W[Skills Wanted]
    P --> A[Availability]
    U --> S[Search / Discover]
    S --> M[Potential Match]
    M --> R[Swap Request]
    R -->|accept| X[Accepted Swap]
    R -->|reject| Y[Rejected]
```

------------------------------------------------------------------------

# 2. Actual uploaded architecture

``` mermaid
flowchart LR
    B[Browser] --> F[React + Vite]
    F --> API[Express API]
    API --> Z[Zod Validation]
    API --> ST[Storage Layer]
    ST --> MS[MemStorage Maps]
    API --> SCH[Drizzle/PostgreSQL Schema]
```

The repository's `package.json` includes React, Express, TypeScript,
Drizzle, PostgreSQL-related packages, Passport and session packages.

------------------------------------------------------------------------

# 3. Frontend

The repository uses:

-   React
-   TypeScript
-   Vite
-   Tailwind CSS
-   Radix UI
-   React Query
-   Wouter

Main areas include:

-   home page
-   login
-   signup
-   profile card
-   profile modal
-   search filters
-   authentication modal

------------------------------------------------------------------------

# 4. Backend

`server/index.ts` starts the application.

`server/routes.ts` registers API routes.

`server/storage.ts` provides the storage abstraction.

This is a good architecture concept:

``` text
Routes
  ↓
Storage interface
  ↓
Storage implementation
```

The route layer doesn't need to know exactly how the data is stored.

------------------------------------------------------------------------

# 5. Schema

`shared/schema.ts` defines PostgreSQL-style Drizzle tables.

## users

Fields include:

-   id
-   username
-   password
-   name
-   email
-   location
-   avatar
-   skillsOffered
-   skillsWanted
-   availability
-   rating
-   isPublic

## swap_requests

Fields:

-   id
-   fromUserId
-   toUserId
-   status
-   message

Status:

``` text
pending
accepted
rejected
```

------------------------------------------------------------------------

# 6. Why a schema layer?

The schema gives a single typed definition that can be reused for:

-   DB structure
-   validation
-   TypeScript types

The repository uses `drizzle-zod` to create Zod schemas from the
database definitions.

------------------------------------------------------------------------

# 7. Authentication

The current uploaded `routes.ts` has:

``` text
POST /api/auth/login
POST /api/auth/register
```

The implementation checks the supplied email/password against storage.

The comment in the code explicitly notes:

> In a real app, you'd set up proper session management.

The repository also contains Passport/session-related dependencies
elsewhere.

### Interview warning

The current code should **not** be described as a production-grade JWT
authentication implementation unless you have another version/branch
containing JWT.

------------------------------------------------------------------------

# 8. User APIs

``` text
GET /api/users/public
GET /api/users/:id
```

The public-users endpoint obtains users from the storage layer.

------------------------------------------------------------------------

# 9. Swap-request APIs

``` text
POST  /api/swap-requests
GET   /api/swap-requests/user/:userId
PATCH /api/swap-requests/:id/status
DELETE /api/swap-requests/:id
```

Lifecycle:

``` mermaid
stateDiagram-v2
    [*] --> pending
    pending --> accepted
    pending --> rejected
    pending --> [*]
    accepted --> [*]
    rejected --> [*]
```

------------------------------------------------------------------------

# 10. Storage abstraction

`IStorage` exposes operations such as:

``` text
getUser
getUserByUsername
getUserByEmail
createUser
getPublicUsers
createSwapRequest
getSwapRequestsByUser
updateSwapRequestStatus
deleteSwapRequest
```

Current implementation:

``` text
MemStorage
  ├── users: Map<number, User>
  └── swapRequests: Map<number, SwapRequest>
```

This is an excellent interview concept because it demonstrates
separation of concerns.

You could replace the storage implementation with a PostgreSQL
implementation without rewriting route handlers.

------------------------------------------------------------------------

# 11. Data flow: login

``` mermaid
sequenceDiagram
    participant U as User
    participant R as React
    participant E as Express
    participant S as Storage

    U->>R: Enter email/password
    R->>E: POST /api/auth/login
    E->>E: Zod validation
    E->>S: getUserByEmail
    S-->>E: User / undefined
    E->>E: Verify credentials
    E-->>R: 200 user / 401 error
```

------------------------------------------------------------------------

# 12. Data flow: swap request

``` mermaid
sequenceDiagram
    participant A as User A
    participant UI as React
    participant API as Express
    participant DB as Storage

    A->>UI: Select User B
    UI->>API: POST /api/swap-requests
    API->>API: Validate request
    API->>DB: createSwapRequest
    DB-->>API: pending request
    API-->>UI: request
```

------------------------------------------------------------------------

# 13. Validation

The repository uses Zod schemas.

Validation is important because API input is untrusted.

For example:

-   email format
-   required strings
-   field types
-   request shape

Never rely only on frontend validation.

------------------------------------------------------------------------

# 14. How matching can work

The current repository mainly provides discovery and skill fields rather
than a sophisticated ML matching engine.

A simple deterministic match score could be:

``` text
score =
  weight1 * overlap(user.skillsWanted, other.skillsOffered)
+ weight2 * overlap(user.skillsOffered, other.skillsWanted)
+ weight3 * availability_match
+ weight4 * location_match
```

Example:

``` text
A wants React and offers Python.
B wants Python and offers React.

A ↔ B = strong reciprocal match
```

------------------------------------------------------------------------

# 15. If interviewer asks "How did you improve match success by 60%?"

Do not invent experimental methodology.

A safe answer:

> "The resume metric refers to the project outcome, but I would explain
> the concrete mechanism behind the improvement rather than claim a
> controlled statistical experiment unless I have the experiment data.
> The main idea was improving discovery and swap-request handling so
> users could find relevant partners more efficiently."

------------------------------------------------------------------------

# 16. Security weaknesses in current repository

These are important because interviewers may ask how you would
productionize it.

Current code has obvious areas to improve:

-   password comparison is direct in `server/routes.ts`
-   no robust authorization check on swap-request mutation
-   request status accepts a generic string rather than an enum/schema
-   current storage is in-memory
-   no production session configuration visible in the inspected route
-   no rate limiting
-   no strong password policy shown
-   user IDs are accepted directly in URLs/request bodies
-   no ownership verification before updating/deleting requests

### How to improve

-   hash passwords with Argon2/bcrypt
-   secure sessions or JWT depending on architecture
-   authorize every request
-   enforce state transitions
-   validate status with enum
-   persist in PostgreSQL
-   use transactions for sensitive updates
-   rate-limit auth endpoints
-   secure cookies
-   audit important operations

------------------------------------------------------------------------

# 17. Why PostgreSQL over MongoDB?

If presenting the uploaded implementation:

> "The domain is strongly relational: users have profiles, and swap
> requests reference sender and receiver users. PostgreSQL gives foreign
> keys, constraints and transactional consistency."

If presenting a MongoDB version from another branch:

> "MongoDB can be useful when profile documents evolve frequently and
> schema flexibility is valuable. The choice depends on access patterns
> and consistency requirements."

------------------------------------------------------------------------

# 18. Strong interview Q&A

### Q: Why use a storage interface?

**Answer:** It separates business/API logic from persistence. The same
route layer can work with in-memory storage, PostgreSQL or another
implementation.

### Q: Why validate on backend if frontend validates?

**Answer:** Frontend validation improves UX, but clients are untrusted.
Backend validation is mandatory for correctness and security.

### Q: How would you secure swap requests?

**Answer:** Authenticate the caller, verify they are authorized to
create/update/delete that request, validate allowed state transitions,
and enforce ownership at the database/query level.

### Q: How would you prevent two users accepting the same request simultaneously?

**Answer:** Use a transaction and conditional update such as "update
only where status = pending", then check affected rows. This prevents
both concurrent requests from successfully transitioning the same
pending record.

### Q: How would you scale matching?

**Answer:** Start with indexed deterministic filtering, then introduce a
ranking layer using skill normalization/embeddings if semantic matching
becomes valuable. Cache frequently requested recommendations and
precompute candidates for large user populations.
