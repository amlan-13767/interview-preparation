# SkillSwap Platform

## Project Overview

SkillSwap is a full stack peer to peer skill exchange platform where users list the skills they can teach and the skills they want to learn. Users can discover compatible people through search, filters, and a rule based matching system. They can then send skill swap requests and accept, reject, or cancel those requests.

The main idea is two way learning.

Example:

```text
User A
Can teach: Python
Wants to learn: React

User B
Can teach: React
Wants to learn: Python
```

The platform identifies that both users can help each other.

```text
A teaches Python to B
B teaches React to A
```

This makes SkillSwap more than a normal CRUD application because it contains authentication, authorization, business rules, recommendation logic, request state management, validation, database constraints, search, filtering, pagination, and testing.

---

## Problem Statement

Most learning platforms follow a teacher to student model:

```text
Teacher -> Course -> Student
```

SkillSwap uses a peer to peer model:

```text
Person A <-> Person B
```

Both people can be teachers and learners.

The goal is to find people who have complementary skills and can exchange knowledge with each other.

---

## Main Features

### User Authentication

- User registration
- User login
- User logout
- Session based authentication
- Secure password hashing using bcrypt
- PostgreSQL backed session storage

### User Profiles

Users can maintain:

- Name
- Username
- Email
- Location
- Profile image
- Skills they can teach
- Skills they want to learn
- Availability
- Profile visibility
- Rating

### Skill Discovery

Users can:

- Search users
- Filter by offered skill
- Filter by wanted skill
- Filter by availability
- Sort results
- Browse public profiles
- Use pagination

### Matching System

The platform calculates a compatibility score using:

- Skills the current user wants and the candidate offers
- Skills the current user offers and the candidate wants
- Availability overlap
- Location match
- Candidate rating

### Skill Swap Requests

Users can:

- Send a request
- Accept a request
- Reject a request
- Cancel a request

Request lifecycle:

```text
             PENDING
             /  |  \
            /   |   \
           v    v    v
      ACCEPTED REJECTED CANCELLED
```

### Security

- Password hashing
- HTTP only session cookies
- Authentication middleware
- Authorization checks
- Backend input validation
- Public profile protection
- Database constraints
- Safe public user responses

### Testing

The matching engine has unit tests covering:

- Mutual skill compatibility
- Compatibility scoring
- No meaningful skill match

---

# Technology Stack

## Frontend

| Technology | Purpose |
|---|---|
| React | User interface |
| Vite | Frontend development and build |
| Wouter | Client side routing |
| TanStack React Query | Server state management |
| Tailwind CSS | Styling |
| Radix UI | Accessible UI components |
| Lucide React | Icons |
| TypeScript / JSX | Application development |

## Backend

| Technology | Purpose |
|---|---|
| Node.js | JavaScript runtime |
| Express | REST API server |
| TypeScript | Type safety |
| Zod | Runtime input validation |
| bcryptjs | Password hashing |
| express-session | Session authentication |
| connect-pg-simple | PostgreSQL session storage |

## Database

| Technology | Purpose |
|---|---|
| PostgreSQL | Relational database |
| Neon | Hosted PostgreSQL |
| Drizzle ORM | Database access |
| Drizzle Kit | Database schema and migrations |

---

# High Level Architecture

```text
                         Browser
                            |
                            v
                 +----------------------+
                 |    React Frontend   |
                 |                      |
                 | React + Vite         |
                 | Wouter               |
                 | React Query          |
                 | Context API          |
                 +----------+-----------+
                            |
                       HTTP / REST
                            |
                            v
                 +----------------------+
                 |    Express Backend  |
                 |                      |
                 | Routes               |
                 | Authentication      |
                 | Authorization       |
                 | Validation          |
                 | Business Logic      |
                 +----------+-----------+
                            |
                            v
                 +----------------------+
                 |    Storage Layer    |
                 |                      |
                 |      Drizzle ORM    |
                 +----------+-----------+
                            |
                            v
                 +----------------------+
                 |     PostgreSQL      |
                 |                      |
                 | users               |
                 | swap_requests       |
                 | sessions            |
                 +----------------------+
```

The architecture separates the frontend, API, business logic, storage, and database layers.

---

# Why This Architecture?

The application uses a modular full stack architecture:

```text
Frontend
   |
   v
REST API
   |
   v
Business Logic
   |
   v
Storage Layer
   |
   v
Database
```

This provides:

- Better maintainability
- Easier testing
- Clear responsibility between components
- Better security
- Easier future changes
- Less duplicated database logic

The frontend does not directly communicate with the database.

---

# Frontend Architecture

The main frontend starts from:

```text
client/src/main.tsx
```

and the main application is managed through:

```text
client/src/App.tsx
```

The application uses providers for:

- React Query
- Authentication context
- UI behavior
- Routing

Conceptually:

```text
main.tsx
   |
   v
App.tsx
   |
   +-- QueryClientProvider
   |
   +-- AuthProvider
   |
   +-- UI Providers
   |
   +-- Router
```

---

# React Query

The project uses TanStack React Query for server state.

React Query provides:

- Loading state
- Error handling
- Caching
- Refetching
- Query invalidation
- Mutation handling

It is useful for server data such as:

- Users
- Matches
- Requests
- Profile information

Redux could also be used, but React Query is more appropriate for caching and synchronizing API data.

---

# Authentication Context

The frontend authentication context provides information such as:

```text
user
isLoggedIn
loading
login()
register()
logout()
updateUser()
```

Components can access the current user without passing the data through many levels of props.

Context is appropriate because authentication state is global but relatively small.

---

# Routing

The application uses Wouter for client side routing.

Typical routes include:

```text
/
 /login
 /signup
 /browse
 /profile
 /matches
 /requests
 /settings
 /notifications
```

## Why Wouter?

The routing requirements are relatively simple.

Wouter is lightweight and provides the routing functionality required by this application.

React Router would also be a valid alternative, especially for a larger application with complex nested routing.

---

# Protected Routes

Pages that require authentication are protected.

```text
User opens protected page
        |
        v
Is authentication loading?
        |
       Yes
        |
        v
Show loading state

       No
        |
        v
Is user logged in?
     /       \
   Yes        No
    |          |
    v          v
 Open page   Login page
```

Frontend protection improves the user experience, but the backend is the actual security boundary.

The backend also verifies authentication for protected APIs.

---

# Authentication Architecture

SkillSwap uses session based authentication.

Login flow:

```text
User enters email and password
              |
              v
POST /api/auth/login
              |
              v
Find user in PostgreSQL
              |
              v
bcrypt.compare()
              |
              v
Password correct?
              |
              v
Regenerate session
              |
              v
Store userId in session
              |
              v
HTTP only session cookie
              |
              v
Browser
```

For later requests:

```text
Browser
   |
   | Session Cookie
   v
Express
   |
   v
Session Store
   |
   v
userId
```

---

# Password Security

Passwords are never stored directly.

Bad:

```text
password = "mypassword123"
```

Instead:

```text
password
   |
   v
bcrypt.hash()
   |
   v
passwordHash
```

During login:

```text
entered password
       |
       v
bcrypt.compare()
       |
       v
stored password hash
```

## Why bcrypt?

bcrypt is designed for password hashing and is intentionally computationally expensive, making large scale password guessing more difficult.

The project uses a cost factor of 12.

## Why not SHA 256?

SHA 256 is a fast general purpose cryptographic hash. For passwords, being fast is not desirable because attackers can perform many guesses quickly.

---

# Sessions vs JWT

The project uses server side sessions instead of JWT.

Session flow:

```text
Browser
   |
   | Cookie
   v
Server
   |
   v
Session Store
   |
   v
User ID
```

## Why sessions?

This is a centralized web application.

Sessions provide:

- Simple authentication
- Easy logout
- Easy session invalidation
- Sensitive session information stays on the server
- Natural integration with cookies

JWT can be useful for stateless APIs, mobile clients, and distributed systems, but it introduces access token expiry, refresh tokens, revocation, and token storage concerns.

---

# Backend Architecture

The backend uses Express.

```text
HTTP Request
     |
     v
Express
     |
     +-- Authentication
     |
     +-- Validation
     |
     +-- Authorization
     |
     +-- Routes
     |
     v
Business Logic
     |
     v
Storage Layer
     |
     v
Database
```

---

# REST API

## Authentication

```text
POST /api/auth/register
POST /api/auth/login
POST /api/auth/logout
GET  /api/auth/me
```

## Users

```text
GET   /api/users/public
GET   /api/users/me
PATCH /api/users/me
GET   /api/users/:id
```

## Matching

```text
GET /api/matches
```

## Swap Requests

```text
GET    /api/swap-requests
POST   /api/swap-requests
PATCH  /api/swap-requests/:id/status
DELETE /api/swap-requests/:id
```

## Why REST?

REST is simple and appropriate for the application's CRUD and resource based operations.

```text
GET     -> Read
POST    -> Create
PATCH   -> Update
DELETE  -> Delete
```

Alternatives include GraphQL, gRPC, and WebSockets. GraphQL is useful for flexible data queries, while WebSockets are useful for real time features. REST keeps the current application simple.

---

# Zod Validation

Zod validates API input before business logic.

Examples:

- Username
- Email
- Password
- Skills
- Availability
- Request message
- Request status

Flow:

```text
Request
   |
   v
Zod validation
   |
   +-- Invalid -> 400
   |
   v
Business logic
```

## Why validate on the backend?

Frontend validation can be bypassed. A malicious client can directly call the API.

Therefore validation must exist at the API boundary.

## Why Zod?

Alternatives include Joi, Yup, Ajv, and manual validation. Zod integrates well with TypeScript and provides clear schemas.

---

# Database Architecture

PostgreSQL is the primary database.

Main entities:

```text
users
swap_requests
sessions
```

---

# Why PostgreSQL?

The application has relational data:

```text
User
 |
 +---- Swap Request
          |
          +---- Sender
          |
          +---- Recipient
```

PostgreSQL provides:

- Foreign keys
- Unique constraints
- Transactions
- Indexes
- Strong consistency
- Array support
- Relational queries

## Why not MongoDB?

MongoDB could work, but the application's relationships and integrity requirements are naturally relational. PostgreSQL makes relationships and constraints explicit.

---

# Users Table

Conceptually:

```text
id
username
password_hash
name
email
location
avatar
skills_offered[]
skills_wanted[]
availability[]
rating
is_public
created_at
updated_at
```

---

# Why Store Skills as Arrays?

The project stores offered and wanted skills as PostgreSQL arrays.

Example:

```text
skills_offered:
[
    "python",
    "react"
]
```

This is simple for the current MVP.

A normalized alternative would use:

```text
users
skills
user_skills
```

Normalization would become more useful for a very large skill catalog, skill categories, skill metadata, synonyms, and complex analytics.

---

# Skill Normalization

The application normalizes skill names.

Examples:

```text
JS
js
JavaScript
```

can be mapped to:

```text
javascript
```

Aliases can also be handled:

```text
reactjs -> react
nodejs  -> node.js
ts      -> typescript
js      -> javascript
```

Process:

```text
Input
  |
  v
Trim
  |
  v
Lowercase
  |
  v
Normalize spaces
  |
  v
Apply aliases
  |
  v
Canonical skill
```

This prevents false mismatches.

---

# Database Constraints

Important integrity rules include:

```text
UNIQUE username
UNIQUE email
Foreign keys
from_user_id != to_user_id
```

Database constraints provide a final layer of protection even if application code contains a bug.

---

# Indexes

Indexes are used for frequently accessed fields such as:

```text
users.is_public
users.name
swap_requests(from_user_id, to_user_id)
swap_requests.status
```

Indexes improve lookup performance.

However, indexes also consume storage and add overhead to inserts and updates, so they should be used where they provide value.

---

# Partial Unique Index

The application uses an active request uniqueness rule.

Conceptually:

```text
pending  -> active
accepted -> active
rejected -> inactive
cancelled -> inactive
```

The goal is to prevent duplicate active requests between the same users while allowing a new request after an old request is rejected or cancelled.

This is implemented using a partial unique index.

---

# Storage Layer

Database access is separated into a storage layer.

```text
routes.ts
    |
    v
storage.ts
    |
    v
Drizzle
    |
    v
PostgreSQL
```

Typical operations include:

```text
getUser()
createUser()
updateUser()
getPublicUsers()
createSwapRequest()
getSwapRequests()
updateSwapRequest()
```

This keeps database logic out of route handlers and makes the application easier to maintain and test.

---

# Drizzle ORM

Drizzle is used to communicate with PostgreSQL.

## Why Drizzle?

Drizzle provides:

- TypeScript integration
- Type inference
- SQL like querying
- Schema definitions
- Relational database support
- Lightweight abstraction

## Alternative: Prisma

Prisma is another strong option and provides a high level developer experience.

Drizzle is suitable here because it stays relatively close to SQL while providing strong TypeScript support.

---

# Public User Data

Sensitive information should not be returned from public APIs.

Examples:

```text
passwordHash
private email information
```

Conceptually:

```text
Database User
      |
      v
toPublicUser()
      |
      v
Safe API response
```

This protects privacy and prevents accidental data leakage.

---

# Public Profiles

Users can control whether their profile is public.

Public discovery filters for:

```text
is_public = true
```

Privacy is enforced by the backend rather than only hiding the profile in React.

---

# Browse and Search

The Browse page supports:

- Search
- Offered skill filter
- Wanted skill filter
- Availability filter
- Sorting
- Pagination

Flow:

```text
React filters
      |
      v
GET /api/users/public
      |
      v
Backend query
      |
      v
PostgreSQL
      |
      v
Filtered results
```

---

# Why Server Side Filtering?

With one million users, it would be inefficient to send all users to the browser.

Better:

```text
Database
   |
   v
SQL filtering
   |
   v
20 relevant users
   |
   v
Browser
```

This reduces network traffic, browser memory, rendering work, and response size.

---

# Pagination

Pagination uses:

```text
offset = (page - 1) * limit
```

Example:

```text
Page 1, limit 12
offset = 0

Page 2, limit 12
offset = 12

Page 3, limit 12
offset = 24
```

Pagination prevents the API from returning too many records at once.

---

# Matching Engine

The matching engine is one of the most important parts of SkillSwap.

It is implemented in:

```text
server/matching.ts
```

It is a rule based recommendation system.

It is not currently a machine learning model.

---

# Matching Concept

Suppose:

```text
User A

Offers:
Python

Wants:
React
```

and:

```text
User B

Offers:
React

Wants:
Python
```

Then:

```text
A wants React
B offers React
       |
       v
      +40

A offers Python
B wants Python
       |
       v
      +40
```

This creates strong mutual compatibility.

---

# Matching Score

The current scoring system is approximately:

```text
Score =
    40 × wanted skills matched
  + 40 × offered skills matched
  + 10 × availability overlap
  + 5 × location overlap
  + rating contribution
```

The score is capped at:

```text
100
```

Candidates without meaningful skill overlap are removed.

The remaining candidates are sorted by compatibility score.

---

# Example Matching Calculation

Suppose:

```text
User A wants:
React

User A offers:
Python

User B offers:
React

User B wants:
Python

Both availability:
Evenings

Both location:
Austin

User B rating:
45
```

Score:

```text
React match            +40
Python match           +40
Availability overlap   +10
Location match          +5
Rating contribution     +2
--------------------------------
Total                   97
```

Compatibility:

```text
97%
```

---

# Why Mutual Skill Matching Gets the Highest Weight?

The core purpose of SkillSwap is skill exchange.

The most important questions are:

```text
Can I teach something they want?
Can they teach something I want?
```

Availability, location, and rating are secondary.

Therefore skill compatibility receives the highest weight.

The weights can later be tuned using real user behavior.

---

# Why Rule Based Matching Instead of Machine Learning?

The initial system does not have enough historical interaction data.

A recommendation model would need data such as:

```text
Who accepted whose request?
Who completed exchanges?
Who rated whom?
Which recommendations led to successful exchanges?
```

Without enough data, a machine learning system may not provide meaningful improvements.

The rule based approach provides:

- Explainability
- Deterministic behavior
- Easy testing
- Easy debugging
- No model dependency
- No training data requirement

A future version can add ML after collecting enough interaction data.

---

# Why Not Embeddings?

Embeddings could identify semantic similarity.

For example:

```text
"frontend development"
"React web development"
```

could be semantically related.

An embedding architecture could be:

```text
Skill
  |
  v
Embedding model
  |
  v
Vector
  |
  v
Vector database
  |
  v
Similarity search
```

However, embeddings introduce model dependency, additional infrastructure, cost, and more difficult debugging.

The current deterministic approach is simpler and more explainable.

---

# Matching Complexity

Let:

```text
N = number of candidate users
S = average number of skills
```

The matching process is approximately:

```text
O(N × S)
```

because each candidate is evaluated.

This is acceptable for a smaller application.

At larger scale, candidate generation should be optimized before ranking.

---

# Scaling the Matching System

For larger datasets:

1. Filter candidates at the database level.
2. Use normalized skill tables and indexes.
3. Use PostgreSQL array indexes if arrays are retained.
4. Introduce Redis caching.
5. Move expensive recommendation work into a dedicated service or background worker.
6. Add semantic matching using embeddings.
7. Eventually introduce a learned ranking model using real user interaction data.

---

# Swap Request System

A swap request contains:

```text
id
from_user_id
to_user_id
status
message
created_at
updated_at
```

The request lifecycle is:

```text
PENDING
   |
   +----> ACCEPTED
   |
   +----> REJECTED
   |
   +----> CANCELLED
```

This can be treated as a small state machine.

---

# Why State Validation Matters

A request should not be modified after reaching a final state.

For example:

```text
ACCEPTED -> REJECTED
```

should not be allowed.

The backend checks that a request is still pending before accepting, rejecting, or cancelling it.

---

# Authentication vs Authorization

Authentication answers:

```text
Who are you?
```

Authorization answers:

```text
What are you allowed to do?
```

Example:

```text
Authentication:
Is the user logged in?

Authorization:
Is this user allowed to accept this request?
```

SkillSwap uses both.

---

# Request Authorization

For accepting or rejecting:

```text
Only the recipient
```

should be allowed.

For cancelling:

```text
Only the sender
```

should be allowed.

The backend verifies ownership before changing the request.

---

# Why Not Trust the Frontend?

A malicious user can ignore the UI and directly call an API.

For example:

```text
PATCH /api/swap-requests/123/status
```

Therefore the backend must check:

```text
Is the user authenticated?
Is the request valid?
Is the request still pending?
Is the user the recipient?
Is the requested state transition valid?
```

The frontend is never the security boundary.

---

# Complete Signup Flow

```text
User
 |
 v
Signup Form
 |
 v
POST /api/auth/register
 |
 v
Zod validation
 |
 v
Normalize input
 |
 v
bcrypt password hashing
 |
 v
PostgreSQL
 |
 v
Create session
 |
 v
HTTP only cookie
 |
 v
Authenticated user
```

---

# Complete Browse Flow

```text
Browse Page
 |
 v
React Query
 |
 v
GET /api/users/public
 |
 v
Validate query
 |
 v
Apply public visibility
 |
 v
Apply search and filters
 |
 v
Database pagination
 |
 v
Return public users
 |
 v
React profile cards
```

---

# Complete Matching Flow

```text
Matches Page
 |
 v
GET /api/matches
 |
 v
Authentication check
 |
 v
Current user
 |
 v
Public candidates
 |
 v
Normalize skills
 |
 v
Calculate compatibility
 |
 v
Remove irrelevant candidates
 |
 v
Sort by score
 |
 v
Return matches
 |
 v
React UI
```

---

# Complete Request Flow

```text
Click Connect
 |
 v
POST /api/swap-requests
 |
 v
Authentication
 |
 v
Zod validation
 |
 v
Check recipient
 |
 v
Prevent self request
 |
 v
Check duplicate active request
 |
 v
Create database record
 |
 v
Return response
```

---

# Complete Accept Flow

```text
Recipient clicks Accept
 |
 v
PATCH /api/swap-requests/:id/status
 |
 v
Authentication
 |
 v
Find request
 |
 v
Check pending state
 |
 v
Check recipient ownership
 |
 v
Update status
 |
 v
ACCEPTED
```

---

# Testing

The project includes tests for the matching engine.

Tests cover:

```text
Mutual compatibility
Expected score
No meaningful compatibility
```

## Why Unit Test Matching?

Matching is business critical logic.

A UI test can tell us that the Matches page opens. It cannot prove that a calculated score such as 97 is correct.

Unit testing the matching function makes the business logic deterministic and easy to verify.

---

# Testing Improvements

A production version should also have:

## Unit Tests

```text
normalizeSkill()
normalizeSkills()
rankMatches()
```

## Integration Tests

```text
Register
Login
Logout
Profile update
Create request
Accept request
Reject request
Cancel request
```

## Authorization Tests

```text
User A attempts to modify User B's request
                |
                v
              403
```

## Edge Cases

```text
Empty skills
Duplicate skills
Different capitalization
Private profiles
Self requests
Duplicate requests
Invalid request status
Expired sessions
Invalid email
Long messages
```

## End to End Tests

Playwright or a similar framework can test complete user journeys through the browser.

---

# Production Security Improvements

The project can be strengthened with:

## CSRF Protection

Important because authentication uses cookies and sessions.

## Rate Limiting

Especially for:

```text
/login
/register
```

## Password Reset

Allow users to recover accounts securely.

## Email Verification

Verify ownership of email addresses.

## Security Headers

Add appropriate security headers and Content Security Policy.

## Logging and Monitoring

Track important application events and failures.

---

# Scalability Strategy

A larger deployment could look like:

```text
                  Load Balancer
                       |
             +---------+---------+
             |                   |
          API 1                API 2
             |                   |
             +---------+---------+
                       |
                  PostgreSQL
                       |
                    Redis
                       |
              Background Jobs
```

Possible improvements:

- Redis caching
- Database indexes
- Read replicas
- Background workers
- Dedicated recommendation service
- WebSockets
- Load balancing
- Better candidate generation
- Vector search
- ML ranking

---

# Why Not Microservices?

The application is currently cohesive.

A modular monolith is easier to:

- Develop
- Test
- Deploy
- Debug
- Maintain

Microservices would introduce:

- Network communication
- Service discovery
- Deployment complexity
- Distributed tracing
- Data consistency challenges
- More infrastructure

Microservices would make sense only when scale or organizational boundaries justify them.

---

# Why Not Redis?

Redis could be added later for:

- Caching
- Rate limiting
- Session storage
- Background job queues
- Real time communication support

It is not necessary for the initial application because PostgreSQL can handle the current requirements.

---

# Why Not WebSockets?

REST is enough for the current request workflow.

WebSockets would become useful for:

- Real time chat
- Instant notifications
- Online status
- Live request updates

A future architecture could use:

```text
React
  |
WebSocket
  |
Node
  |
Redis Pub/Sub
```

---

# Important Current Limitations

The current application can be improved in several areas:

1. Matching is calculated in application memory.
2. There is no full real time chat system.
3. Notifications can be expanded.
4. Password reset is not implemented.
5. Email verification is not implemented.
6. Skill matching is deterministic rather than semantic.
7. More automated test coverage is needed.
8. Request concurrency can be strengthened with database transactions and stronger constraints.
9. Skill storage could eventually be normalized.
10. Frontend request categorization should rely directly on the current user's ID.

---

# Concurrency Improvement

The application checks for duplicate requests before insertion.

However, two requests arriving at exactly the same time can create a race condition if both pass the check before either inserts.

A production implementation should enforce the invariant at the database level.

One possible approach is to canonicalize the user pair:

```text
user_low  = min(userA, userB)
user_high = max(userA, userB)
```

Then enforce uniqueness on:

```text
(user_low, user_high)
```

for active requests.

Transactions can provide additional protection.

---

# Important Interview Distinction

SkillSwap currently uses:

```text
Rule Based Recommendation
```

not:

```text
Machine Learning Recommendation
```

The system is deterministic.

That is intentional.

A future ML version could learn from:

```text
Request acceptance
Successful exchanges
Ratings
Response rates
Repeated interactions
User activity
```

and use those signals for ranking.

---

# Potential Future ML Architecture

```text
User interactions
        |
        v
Feature engineering
        |
        v
Training dataset
        |
        v
Ranking model
        |
        v
Recommendation score
        |
        v
Top candidates
```

Potential models:

- Logistic Regression
- Gradient Boosting
- XGBoost
- LightGBM
- Neural ranking models

Embeddings could also be used for semantic skill matching.

---

# Most Important Interview Questions

## 1. Explain your project.

SkillSwap is a full stack peer to peer skill exchange platform. Users create profiles containing skills they can teach and skills they want to learn, along with availability and visibility preferences. They can browse public users through search and filters or view personalized matches generated by a backend weighted scoring algorithm. Users can then send skill swap requests and recipients can accept or reject them.

The frontend uses React and Vite, while the backend uses Node.js and Express. PostgreSQL stores application data, Drizzle is used as the ORM, Zod validates API inputs, and bcrypt with server side sessions handles authentication.

## 2. Why PostgreSQL?

The application has relational data because users are connected to swap requests. PostgreSQL provides foreign keys, constraints, indexes, transactions, and strong consistency, which help maintain data integrity.

## 3. Why not MongoDB?

MongoDB could work, but the application's relationships are naturally relational. PostgreSQL makes relationships and integrity constraints explicit.

## 4. Explain your matching algorithm.

The algorithm compares both directions. It checks whether the candidate offers skills that the current user wants and whether the current user offers skills that the candidate wants. Mutual skill matches receive the highest weights, while availability, location, and rating provide smaller contributions. Candidates without meaningful skill overlap are removed and the remaining users are sorted by compatibility.

## 5. Why 40 points for skills?

Skill exchange is the main purpose of the application, so mutual skill compatibility should dominate secondary factors such as location and rating. The weights are configurable and can later be tuned using actual user behavior.

## 6. Why rule based matching instead of ML?

There is not enough historical interaction data initially to train a meaningful recommendation model. A rule based system is deterministic, explainable, easy to test, and does not require training data. ML can be added after enough interaction data is collected.

## 7. What is the time complexity?

Approximately O(N × S), where N is the number of candidate users and S is the average number of skills considered.

## 8. How would you scale matching?

I would first filter candidates at the database level, use indexes and normalized skill relationships, then add caching. At larger scale I could introduce a recommendation service, embeddings, and eventually a learned ranking model.

## 9. How do you protect passwords?

Passwords are hashed using bcrypt and never stored in plain text. Login uses bcrypt comparison against the stored hash.

## 10. Why bcrypt?

bcrypt is specifically designed for password hashing and is intentionally computationally expensive, making brute force guessing more difficult.

## 11. Why sessions instead of JWT?

This is a centralized web application, so server side sessions provide simple authentication and easy invalidation. JWT would be more useful for a stateless or highly distributed architecture.

## 12. Authentication vs authorization?

Authentication answers "Who are you?"

Authorization answers "What are you allowed to do?"

## 13. How do you authorize requests?

The backend verifies the logged in user and checks whether they are the sender or recipient. Only the recipient can accept or reject a request, while the sender can cancel it.

## 14. Why Zod?

Zod provides runtime validation at the API boundary and integrates well with TypeScript.

## 15. What happens if someone bypasses the frontend?

The backend still validates authentication, authorization, input, privacy, and business rules. The frontend is not considered a security boundary.

## 16. Why React Query?

It manages server state and provides caching, refetching, loading states, error handling, and query invalidation.

## 17. Why Context?

Authentication state is global but small, so Context provides a simple way to expose the current user and authentication actions.

## 18. Why Wouter?

The routing requirements are simple, so a lightweight router is sufficient. React Router would also be a valid alternative for a larger routing system.

## 19. Why Drizzle?

Drizzle provides strong TypeScript support and stays relatively close to SQL while providing type safe database access.

## 20. Why not microservices?

The application is small and cohesive. Microservices would introduce unnecessary operational complexity without enough benefit at this stage.

## 21. How would you scale the application?

I would improve database indexes and filtering first, then introduce caching, background processing, load balancing, read replicas, and potentially a separate recommendation service. Real time functionality could use WebSockets.

## 22. What happens if two users send requests simultaneously?

The current application performs duplicate checks before insertion, but application level checks alone can have race conditions. A production implementation should enforce the invariant at the database level using canonical user pairs, unique partial indexes, and appropriate transaction handling.

## 23. What would you improve?

I would add real time notifications and chat, improve test coverage, add rate limiting and CSRF protection, implement password reset and email verification, optimize candidate generation, support semantic skill matching, and strengthen concurrency handling.

## 24. What was the most interesting technical part?

The matching engine was the most interesting because it required balancing mutual skill compatibility with secondary factors such as availability, location, and rating while keeping the recommendations explainable.

## 25. What makes this more than a CRUD project?

The project contains business logic beyond CRUD. The matching engine performs recommendation ranking, swap requests follow state transitions, authorization controls operations, and the backend enforces privacy and data integrity.

---

# 30 Second Project Explanation

> SkillSwap is a full stack peer to peer skill exchange platform where users list skills they can teach and skills they want to learn. The platform uses React and Vite on the frontend and Node.js, Express, PostgreSQL, and Drizzle on the backend. Its main feature is an explainable weighted matching engine that compares mutual skills and also considers availability, location, and rating. I also implemented session based authentication with bcrypt, backend validation using Zod, authorization for swap requests, search, filtering, pagination, database constraints, and unit tests for the matching logic.

---

# 60 Second Project Explanation

> SkillSwap is a full stack peer to peer skill exchange platform that I built to support two way learning. Users create profiles containing the skills they can teach, the skills they want to learn, their availability, and their visibility preferences.
>
> The frontend is built with React and Vite, with Wouter for routing and React Query for server state. The backend uses Node.js and Express, while PostgreSQL stores users and swap requests through Drizzle ORM.
>
> The main feature is a rule based recommendation engine. Instead of simply searching for people who have one skill, it compares both directions. It checks whether another person offers something I want and whether I offer something they want. Skill compatibility receives the highest weight, while availability, location, and rating provide smaller contributions.
>
> Authentication uses bcrypt and server side sessions with PostgreSQL backed session storage. API input is validated with Zod and authorization checks ensure that only the correct users can accept, reject, or cancel requests. I also added pagination, filtering, database constraints, and unit tests for the matching logic.

---

# Final Architecture

```text
                         SKILLSWAP
                            |
            +---------------+---------------+
            |                               |
        FRONTEND                         BACKEND
            |                               |
          React                          Express
            |                               |
          Vite                       Middleware
            |                    +----------+----------+
        Wouter                   |          |          |
            |                   Auth       Zod       Routes
     React Query                |          |          |
            |                   +----------+----------+
     Context Auth                         |
            |                       Business Logic
            |                         /          \
            |                   Matching       Requests
            |                       \          /
            |                        \        /
            |                       Storage
            |                           |
            |                        Drizzle
            |                           |
            +-------- HTTP ------------+
                                        |
                                   PostgreSQL
                                  /     |      \
                               Users Requests Sessions
```

---

# Core Engineering Story

The entire project can be remembered as:

```text
IDENTITY
   |
   v
Create secure account
   |
   v
PROFILE
   |
   v
What can I teach?
What do I want to learn?
   |
   v
DISCOVERY
   |
   v
Search + Filters + Pagination
   |
   v
MATCHING
   |
   v
Mutual skill compatibility
+ Availability
+ Location
+ Rating
   |
   v
CONNECTION
   |
   v
Send Swap Request
   |
   v
Authorization + State Validation
   |
   v
Accept / Reject / Cancel
   |
   v
LEARNING PARTNERS
```

---

# Quick Revision Checklist

Before the interview, make sure you can explain:

- [ ] React architecture
- [ ] Vite
- [ ] Wouter
- [ ] React Query
- [ ] Context API
- [ ] Express
- [ ] REST APIs
- [ ] Middleware
- [ ] Authentication
- [ ] Authorization
- [ ] Sessions
- [ ] Cookies
- [ ] bcrypt
- [ ] Zod
- [ ] PostgreSQL
- [ ] Drizzle ORM
- [ ] Database relationships
- [ ] Foreign keys
- [ ] Unique constraints
- [ ] Indexes
- [ ] Partial unique indexes
- [ ] PostgreSQL arrays
- [ ] Skill normalization
- [ ] Search
- [ ] Pagination
- [ ] Matching algorithm
- [ ] Matching complexity
- [ ] Rule based recommendation
- [ ] Why not ML
- [ ] Why not embeddings
- [ ] Request state machine
- [ ] Authorization rules
- [ ] Testing
- [ ] Security improvements
- [ ] Scalability
- [ ] Redis
- [ ] WebSockets
- [ ] Microservices vs monolith
- [ ] Race conditions
- [ ] Future ML recommendation system

---

# Most Important Things to Memorize

If you have very little time, focus on these five areas.

## 1. Architecture

```text
React
  ↓
Express
  ↓
Business Logic
  ↓
Drizzle
  ↓
PostgreSQL
```

## 2. Authentication

```text
bcrypt
+
express-session
+
PostgreSQL session store
+
HTTP only cookie
```

## 3. Matching

```text
40 → wanted skill match
40 → offered skill match
10 → availability
5  → location
rating → small additional score
```

## 4. Security

```text
Authentication
Authorization
Validation
Privacy
Database constraints
```

## 5. Scaling

```text
Database filtering
→ Indexes
→ Redis
→ Background jobs
→ Recommendation service
→ Embeddings / ML
```

---

# Conclusion

SkillSwap demonstrates a complete software engineering workflow rather than only frontend development.

It combines:

```text
Frontend Engineering
        +
Backend Engineering
        +
Database Design
        +
Authentication
        +
Authorization
        +
Validation
        +
Recommendation Logic
        +
Testing
        +
Security
        +
Scalability
```

The most important technical story is the matching system. It starts with a simple explainable rule based approach because there is no initial training data, while leaving a clear path toward semantic embeddings and machine learning once enough user interaction data becomes available.
