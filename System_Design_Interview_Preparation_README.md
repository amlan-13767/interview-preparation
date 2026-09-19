# System Design Interview Preparation

A complete interview-focused System Design guide covering fundamentals, scalability, databases, caching, distributed systems, networking, messaging, security, observability, and important system design interview questions.

---

# Table of Contents

1. [What is System Design?](#1-what-is-system-design)
2. [HLD vs LLD](#2-hld-vs-lld)
3. [Functional and Non Functional Requirements](#3-functional-and-non-functional-requirements)
4. [Scalability](#4-scalability)
5. [Vertical vs Horizontal Scaling](#5-vertical-vs-horizontal-scaling)
6. [Stateless vs Stateful Systems](#6-stateless-vs-stateful-systems)
7. [Monolithic Architecture](#7-monolithic-architecture)
8. [Microservices Architecture](#8-microservices-architecture)
9. [Latency and Throughput](#9-latency-and-throughput)
10. [Load Balancer](#10-load-balancer)
11. [Databases](#11-databases)
12. [Replication](#12-database-replication)
13. [Sharding](#13-database-sharding)
14. [Caching](#14-caching)
15. [Cache Eviction](#15-cache-eviction)
16. [CDN](#16-cdn)
17. [API Gateway](#17-api-gateway)
18. [Message Queues](#18-message-queues)
19. [Rate Limiting](#19-rate-limiting)
20. [DNS](#20-dns)
21. [CAP Theorem](#21-cap-theorem)
22. [Consistency](#22-consistency)
23. [Availability](#23-availability)
24. [Reliability and Fault Tolerance](#24-reliability-and-fault-tolerance)
25. [Event Driven Architecture](#25-event-driven-architecture)
26. [WebSockets and Polling](#26-websockets-and-polling)
27. [Security](#27-security)
28. [Monitoring and Observability](#28-monitoring-and-observability)
29. [System Design Trade Offs](#29-system-design-trade-offs)
30. [Important Interview Questions and Answers](#30-important-interview-questions-and-answers)
31. [System Design Case Studies](#31-system-design-case-studies)
32. [Interview Framework](#32-system-design-interview-framework)

---

# 1. What is System Design?

System design is the process of deciding how the components of a software system should be organized and how they should communicate so that the system satisfies functional and non functional requirements.

For example, if we are asked to design YouTube, we need to think about:

- How users upload videos
- Where videos are stored
- How videos are delivered
- How millions of users can watch videos simultaneously
- How videos are searched
- How recommendations work
- How failures are handled
- How databases scale
- How latency is reduced
- How abuse is prevented

A simple architecture may look like:

```text
Users
  |
  v
Load Balancer
  |
  +--------+--------+
  |        |        |
Server 1 Server 2 Server 3
  |        |        |
  +--------+--------+
           |
        Database
```

Large systems add caches, queues, CDNs, object storage, databases, monitoring and other components.

---

# 2. HLD vs LLD

## High Level Design

HLD focuses on the overall architecture.

It answers:

> What components do we need and how do they communicate?

Typical HLD components:

- Clients
- Load balancers
- API gateways
- Application servers
- Microservices
- Databases
- Caches
- Message queues
- CDNs
- Object storage

Example:

```text
Client
  |
  v
Load Balancer
  |
  v
Application Servers
  |
  +---- Cache
  |
  +---- Database
```

## Low Level Design

LLD focuses on the internal design of components.

It answers:

> What classes, objects, interfaces and relationships do we need?

Example:

```text
ParkingLot
    |
    +--- ParkingFloor
            |
            +--- ParkingSpot
            |
            +--- Vehicle
```

LLD involves:

- Classes
- Objects
- Interfaces
- Relationships
- SOLID principles
- Design patterns
- UML

### Interview Answer

HLD defines the architecture and major components of a system, while LLD defines the internal implementation structure such as classes, interfaces, objects and design patterns.

---

# 3. Functional and Non Functional Requirements

## Functional Requirements

Functional requirements describe what the system should do.

Example for a URL shortener:

1. User enters a long URL.
2. System generates a short URL.
3. User opens the short URL.
4. System redirects the user to the original URL.

## Non Functional Requirements

Non functional requirements describe how well the system should work.

Examples:

- Low latency
- High availability
- Scalability
- Reliability
- Security
- Durability
- Maintainability

Example:

```text
Functional:
User can upload a photo.

Non Functional:
Photo upload should be reliable and complete quickly.
```

### Interview Answer

Functional requirements define the features and behavior of the system, while non functional requirements define quality attributes such as scalability, availability, latency, reliability and security.

---

# 4. Scalability

Scalability is the ability of a system to handle increasing workload by adding resources.

Example:

```text
1,000 users
    |
10,000 users
    |
100,000 users
    |
1,000,000 users
```

A scalable system should continue to operate as traffic and data increase.

There are two major forms:

- Vertical scaling
- Horizontal scaling

---

# 5. Vertical vs Horizontal Scaling

## Vertical Scaling

Vertical scaling means increasing the resources of an existing machine.

```text
Before:

4 CPU
16 GB RAM

After:

32 CPU
128 GB RAM
```

### Advantages

- Simple
- Easy to implement
- Less architectural complexity

### Disadvantages

- Hardware has limits
- Expensive at higher levels
- Single machine can become a bottleneck
- Failure of that machine can affect the service

## Horizontal Scaling

Horizontal scaling means adding more machines.

```text
             Load Balancer
             /     |     \
            v      v      v
          S1      S2      S3
```

### Advantages

- Can scale to very large workloads
- Better fault isolation
- Resources can be added gradually

### Disadvantages

- More complex
- Requires distributed architecture
- Requires load balancing and coordination

### Interview Answer

Vertical scaling increases the capacity of one machine, while horizontal scaling increases capacity by adding more machines. Large distributed systems generally rely heavily on horizontal scaling because it provides greater scalability and fault tolerance.

---

# 6. Stateless vs Stateful Systems

## Stateful

A stateful server stores client information locally.

```text
User -> Server 1

Server 1 stores:
User session
Cart
Preferences
```

If the next request goes to Server 2, Server 2 may not know the state.

## Stateless

State is stored externally.

```text
             +---- Redis
             |
User -> Load Balancer -> Servers
             |
             +---- Database
```

Any server can handle any request.

### Why Stateless Systems Are Useful

They make horizontal scaling easier because requests do not depend on a particular application server.

---

# 7. Monolithic Architecture

In a monolithic architecture, major application functionality exists inside one application.

```text
             Application
  +-----------------------------+
  | User | Order | Payment      |
  | Auth | Search | Product     |
  +-----------------------------+
               |
             Database
```

### Advantages

- Simple development
- Simple deployment
- Easy local debugging
- Good for smaller systems

### Disadvantages

- Large codebase
- Difficult to scale individual components
- Deployment becomes riskier as the system grows
- Failure in one part may affect the application

---

# 8. Microservices Architecture

The application is divided into independently deployable services.

```text
                 API Gateway
                      |
       +--------------+--------------+
       |              |              |
    User Service  Order Service  Payment Service
       |              |              |
      DB             DB             DB
```

### Advantages

- Independent deployment
- Independent scaling
- Clear ownership
- Fault isolation
- Teams can work independently

### Disadvantages

- Network communication
- Distributed debugging
- More operational complexity
- Data consistency challenges
- Service discovery
- Monitoring becomes more complex

### Interview Answer

Microservices are not automatically better than monoliths. The choice depends on system scale, team structure, domain boundaries, deployment requirements and operational complexity.

---

# 9. Latency and Throughput

## Latency

Latency is the time taken to complete an operation.

Example:

```text
Request sent
    |
    | 100 ms
    v
Response received
```

Latency = 100 ms.

## Throughput

Throughput is the amount of work a system can process per unit of time.

Example:

```text
10,000 requests/second
```

### Simple Analogy

Restaurant:

- Latency = waiting time for one customer
- Throughput = number of customers served per hour

### Interview Answer

Latency measures how long an individual operation takes, while throughput measures how much work the system can process over a period of time.

---

# 10. Load Balancer

A load balancer distributes incoming traffic across multiple servers.

```text
                  Load Balancer
                 /      |      \
                v       v       v
              S1       S2       S3
```

Without a load balancer:

```text
Users -> One Server
```

That server can become overloaded.

## Common Load Balancing Algorithms

### Round Robin

```text
S1 -> S2 -> S3 -> S1 -> S2 -> S3
```

### Least Connections

Send the request to the server with the fewest active connections.

### Weighted Round Robin

More powerful servers receive more traffic.

```text
S1 = 50%
S2 = 30%
S3 = 20%
```

### IP Hash

A client's IP can be used to determine the destination server.

---

# 11. Databases

Databases provide persistent storage.

Two major categories important for system design are SQL and NoSQL.

## SQL

Examples:

- PostgreSQL
- MySQL
- Oracle

Data is stored in relational tables.

```text
Users

id | name | email
---+------+------
1  | A    | a@x
2  | B    | b@x
```

SQL is useful when:

- Relationships matter
- Transactions are important
- Schema is structured
- Complex queries are required

## NoSQL

Examples:

- MongoDB
- Cassandra
- DynamoDB
- Redis

Types include:

- Document
- Key value
- Column family
- Graph

NoSQL can be useful for:

- Large scale workloads
- Flexible schemas
- High throughput
- Specific access patterns

### SQL vs NoSQL

| SQL | NoSQL |
|---|---|
| Relational | Often non relational |
| Structured schema | Often flexible schema |
| Strong transaction support | Depends on database |
| Complex joins supported | Usually optimized for specific access patterns |
| Good for relational data | Good for large scale or specialized workloads |

---

# 12. Database Replication

Replication means keeping copies of data on multiple database nodes.

```text
              Primary
             /       \
            v         v
       Replica 1   Replica 2
```

If one replica fails, other replicas may continue serving requests.

Replication can improve:

- Availability
- Read scalability
- Fault tolerance

A common architecture is:

```text
Writes -> Primary

Reads -> Replicas
```

Potential challenge:

Replication may introduce replication lag, so a replica might temporarily contain stale data.

---

# 13. Database Sharding

Sharding divides data across multiple database nodes.

Suppose we have one billion users.

Instead of:

```text
All Users -> One Database
```

we can have:

```text
             Users
               |
       +-------+-------+
       |       |       |
      DB1     DB2     DB3
     A-H     I-P     Q-Z
```

Each database stores part of the data.

## Shard Key

The shard key determines which shard stores a record.

Possible shard keys:

- user_id
- geographic region
- customer_id

A good shard key should distribute data evenly and support common access patterns.

### Bad Shard Key

A poor shard key can create a hot shard where one database receives most of the traffic.

---

# 14. Caching

Caching stores frequently accessed data in faster storage.

Without cache:

```text
Client -> Application -> Database
```

With cache:

```text
Client -> Application -> Cache
                         |
                         v
                      Database
```

If data exists in cache:

```text
Application -> Cache -> Response
```

The database is not accessed.

Redis is a common distributed cache.

## Why Use Cache?

- Lower latency
- Reduce database load
- Increase throughput
- Improve scalability

## Cache Aside

Common pattern:

```text
1. Application checks cache.
2. If found -> return data.
3. If not found -> query database.
4. Store result in cache.
5. Return result.
```

---

# 15. Cache Eviction

Cache memory is limited.

When it becomes full, some data must be removed.

## LRU

Least Recently Used.

Remove the item that has not been accessed recently.

## LFU

Least Frequently Used.

Remove the item used least frequently.

## FIFO

First In First Out.

Remove the oldest inserted item.

### Interview Question

Why is cache invalidation difficult?

Because cached data can become stale when the underlying database changes. The system needs a strategy for updating or removing stale cache entries.

---

# 16. CDN

CDN stands for Content Delivery Network.

A CDN stores content at geographically distributed edge locations.

```text
                Origin Server
                    |
        +-----------+-----------+
        |                       |
    CDN India                CDN USA
        |                       |
   Indian users            US users
```

CDNs are useful for:

- Images
- Videos
- CSS
- JavaScript
- Static files

Benefits:

- Lower latency
- Reduced origin traffic
- Better global performance

---

# 17. API Gateway

An API Gateway provides a single entry point for clients in many microservice architectures.

```text
Client
  |
  v
API Gateway
  |
  +------ User Service
  |
  +------ Order Service
  |
  +------ Payment Service
```

Responsibilities can include:

- Routing
- Authentication
- Authorization
- Rate limiting
- Logging
- Request transformation
- Load balancing

### API Gateway vs Load Balancer

Load balancer mainly distributes traffic across servers.

API Gateway operates at the API/service level and can provide routing, authentication, rate limiting and request transformation.

---

# 18. Message Queues

A message queue allows producers and consumers to communicate asynchronously.

```text
Producer
   |
   v
Message Queue
   |
   +------ Consumer 1
   +------ Consumer 2
   +------ Consumer 3
```

Examples:

- Kafka
- RabbitMQ
- Amazon SQS

Example:

```text
Order Created
      |
      v
Message Queue
      |
 +----+----+----+
 |         |    |
Email   Inventory Invoice
```

The user does not need to wait for every background task to finish.

## Benefits

- Asynchronous processing
- Decoupling
- Better scalability
- Buffering during traffic spikes

---

# 19. Rate Limiting

Rate limiting controls how many requests a client can make in a period.

Example:

```text
100 requests/minute/user
```

If the user exceeds the limit, the system can reject, delay or otherwise control additional requests.

## Common Algorithms

### Fixed Window

Count requests in fixed time intervals.

### Sliding Window

Consider requests over a moving time interval.

### Token Bucket

Tokens are added to a bucket at a fixed rate.

Each request consumes a token.

```text
Token generation
       |
       v
+---------------+
| Token Token   |
| Token Token   |
+---------------+
       |
     Request
       |
       v
    Allowed
```

If there are no tokens, the request is rejected or delayed.

### Leaky Bucket

Requests enter a queue and are processed at a controlled rate.

---

# 20. DNS

DNS stands for Domain Name System.

It maps domain names to IP addresses.

```text
example.com
     |
     v
DNS
     |
     v
IP Address
```

Without DNS, users would need to remember IP addresses.

DNS also uses caching and TTL.

## TTL

TTL means Time To Live.

It specifies how long a DNS result can be cached before it needs to be refreshed.

---

# 21. CAP Theorem

CAP stands for:

- Consistency
- Availability
- Partition Tolerance

In a distributed system, when a network partition occurs, the system cannot simultaneously guarantee both perfect consistency and availability.

## Consistency

Every read gets the appropriate latest value according to the system's consistency model.

## Availability

Every request receives a response.

## Partition Tolerance

The system continues operating despite network communication failures between nodes.

Example:

```text
Server A  X  Server B

Network connection is broken.
```

The system must decide how to behave while the partition exists.

### Important Interview Point

CAP is about behavior during a network partition. It should not be simplified into "choose any two of C, A and P" for every situation.

---

# 22. Consistency

Consistency describes what values clients can observe in a distributed system.

## Strong Consistency

After a successful write, subsequent reads return the updated value according to the chosen consistency guarantee.

## Eventual Consistency

Updates may take time to propagate, but replicas eventually converge if no new updates occur.

Example:

```text
Write -> Primary

Replica 1 -> updated
Replica 2 -> temporarily stale
Replica 3 -> updated later
```

Eventual consistency can be useful when availability and scalability are more important than immediate global consistency.

---

# 23. Availability

Availability describes whether a system is operational and able to respond to requests.

Common targets:

```text
99%
99.9%
99.99%
99.999%
```

More availability usually requires additional infrastructure, redundancy and operational investment.

---

# 24. Reliability and Fault Tolerance

## Reliability

Reliability describes how consistently a system performs correctly over time.

## Availability

Availability asks whether the system is currently accessible.

## Fault Tolerance

Fault tolerance means the system continues operating when components fail.

Example:

```text
             Load Balancer
             /            \
        Server 1        Server 2
           X                |
        Failed          Serving traffic
```

Techniques:

- Replication
- Redundancy
- Failover
- Health checks
- Retries
- Timeouts
- Circuit breakers

---

# 25. Event Driven Architecture

In event driven systems, components communicate through events.

Instead of:

```text
Service A -> directly calls Service B
```

we can have:

```text
Service A
   |
   v
Event Broker
   |
   +---- Service B
   +---- Service C
   +---- Service D
```

Example:

```text
OrderCreated
     |
     +---- Email Service
     +---- Inventory Service
     +---- Analytics Service
     +---- Notification Service
```

Benefits:

- Loose coupling
- Asynchronous processing
- Independent consumers
- Easier scaling

Challenges:

- Event ordering
- Duplicate events
- Eventual consistency
- Debugging
- Message delivery guarantees

---

# 26. WebSockets and Polling

## Normal HTTP

```text
Client -> Request
Server -> Response
```

## Short Polling

The client repeatedly asks:

```text
Client -> Anything new?
Server -> No

Client -> Anything new?
Server -> No
```

This can create unnecessary traffic.

## Long Polling

The server keeps the request open until new information becomes available or a timeout occurs.

## WebSocket

WebSocket creates a persistent two way communication channel.

```text
Client <================> Server
```

Useful for:

- Chat
- Gaming
- Live notifications
- Real time dashboards
- Collaborative applications

---

# 27. Security

Important system design security concepts:

## Authentication

Answers:

> Who are you?

Examples:

- Password authentication
- JWT
- OAuth
- OpenID Connect

## Authorization

Answers:

> What are you allowed to do?

Example:

```text
Admin -> Delete user
User  -> Cannot delete other users
```

Other important concepts:

- HTTPS/TLS
- Encryption
- Password hashing
- Input validation
- Access control
- Rate limiting
- Secrets management
- Secure session handling
- Backup and disaster recovery

---

# 28. Monitoring and Observability

Large systems require observability.

Three important areas:

## Logs

Detailed records of application events.

Example:

```text
2026-09-20 ERROR Payment failed
```

## Metrics

Numerical measurements.

Examples:

- CPU usage
- Memory usage
- Request rate
- Error rate
- Latency
- Queue depth

## Traces

Track a request across multiple services.

```text
Request
  |
  v
API Gateway
  |
  v
Order Service
  |
  v
Payment Service
  |
  v
Database
```

Distributed tracing helps identify where latency or failures occur.

---

# 29. System Design Trade Offs

There is rarely one universally best architecture.

Common trade offs:

```text
SQL vs NoSQL
Consistency vs Availability
Latency vs Throughput
Cost vs Performance
Monolith vs Microservices
Synchronous vs Asynchronous
Vertical vs Horizontal Scaling
```

Good system design means explaining why a particular choice fits the requirements.

Do not say:

> Kafka is always better.

Instead say:

> Kafka may be useful when we need durable high throughput event streaming and independent consumers, but it adds operational and consistency complexity.

---

# 30. Important Interview Questions and Answers

## Level 1: Fundamentals

### 1. What is System Design?

System design is the process of defining the architecture, components, interfaces and communication patterns of a software system so that it satisfies functional and non functional requirements.

### 2. HLD vs LLD?

HLD defines the overall architecture and major components. LLD defines internal implementation details such as classes, interfaces, objects and design patterns.

### 3. Functional vs Non Functional Requirements?

Functional requirements describe what the system does. Non functional requirements describe qualities such as scalability, availability, latency, security and reliability.

### 4. What is Scalability?

Scalability is the ability of a system to handle increasing workload by adding resources without unacceptable degradation.

### 5. Horizontal vs Vertical Scaling?

Vertical scaling makes one machine more powerful. Horizontal scaling adds more machines.

### 6. What is Latency?

Latency is the time required to complete an operation or receive a response.

### 7. What is Throughput?

Throughput is the amount of work processed per unit time, such as requests per second.

### 8. What is Availability?

Availability is the ability of a system to remain operational and respond to requests.

### 9. What is Reliability?

Reliability is the ability of a system to consistently perform correctly over time.

### 10. What is Fault Tolerance?

Fault tolerance is the ability of a system to continue operating when one or more components fail.

### 11. Stateless vs Stateful?

A stateful server stores client state locally. A stateless server does not depend on locally stored client state and generally stores state in shared external systems.

### 12. Monolith vs Microservices?

A monolith contains major functionality in one application. Microservices divide functionality into independently deployable services.

---

# Level 2: Core System Design

### 13. What is a Load Balancer?

A load balancer distributes incoming traffic across multiple servers.

### 14. How does a Load Balancer work?

It receives requests and selects an available backend server according to an algorithm such as round robin or least connections.

### 15. What is Round Robin?

Requests are distributed sequentially among servers.

```text
S1 -> S2 -> S3 -> S1 -> S2
```

### 16. What is Least Connections?

The request is sent to the server with the smallest number of active connections.

### 17. What is Consistent Hashing?

Consistent hashing maps keys and nodes onto a logical hash ring. It minimizes the number of keys that need to move when nodes are added or removed.

It is useful in distributed caches and partitioned systems.

### 18. What is Caching?

Caching stores frequently accessed data in faster storage to reduce latency and backend load.

### 19. What is Cache Aside?

The application first checks the cache. If the data is absent, it reads from the database and then populates the cache.

### 20. What is Write Through Cache?

Data is written to the cache and backing store as part of the write operation.

### 21. What is Write Back Cache?

Data is first written to cache and later persisted to the backing store.

This can improve write performance but introduces durability and consistency risks.

### 22. What is Cache Invalidation?

Cache invalidation means removing or updating cached data when the underlying source changes.

### 23. LRU vs LFU?

LRU removes data that has not been used recently. LFU removes data that has been accessed least frequently.

### 24. What is CDN?

A CDN is a geographically distributed network that serves cached content from locations closer to users.

### 25. CDN vs Cache?

A cache is a general mechanism for storing frequently used data closer to computation or users. A CDN is a distributed infrastructure specifically designed to deliver content from geographically distributed edge locations.

### 26. What is a Reverse Proxy?

A reverse proxy sits in front of backend servers and forwards client requests to them.

It can provide:

- Load balancing
- TLS termination
- Caching
- Routing
- Security controls

### 27. Forward Proxy vs Reverse Proxy?

A forward proxy represents clients when accessing external servers.

A reverse proxy represents backend servers when clients access the application.

### 28. What is an API Gateway?

An API Gateway is an entry point for API clients and can handle routing, authentication, authorization, rate limiting and request transformation.

### 29. API Gateway vs Load Balancer?

A load balancer primarily distributes traffic across backend instances. An API Gateway operates at the API/service layer and can implement additional API-related policies and routing.

### 30. What is Rate Limiting?

Rate limiting controls the number of requests a client can make during a defined period.

### 31. Token Bucket vs Leaky Bucket?

Token Bucket allows requests when tokens are available and can permit controlled bursts. Leaky Bucket processes requests at a relatively controlled rate and can smooth traffic.

---

# Level 3: Databases

### 32. SQL vs NoSQL?

SQL databases use relational models and are well suited for structured data and transactional workloads. NoSQL databases provide different data models and are often optimized for particular large scale access patterns.

### 33. When would you choose SQL?

Choose SQL when strong transactional semantics, relational data, constraints and complex queries are important.

### 34. When would you choose NoSQL?

Choose NoSQL when flexible schemas, very large scale, high throughput or specialized access patterns make a particular NoSQL model appropriate.

### 35. What is Database Replication?

Replication creates copies of database data on multiple nodes.

### 36. What is Primary Replica Architecture?

One database handles writes while one or more replicas can serve reads.

### 37. What is Database Sharding?

Sharding horizontally partitions data across multiple database nodes.

### 38. Sharding vs Partitioning?

Partitioning divides data into logical pieces. Sharding distributes those partitions across different machines or database nodes.

### 39. What is a Shard Key?

A shard key determines which shard stores a record.

### 40. What happens if the Shard Key is bad?

Data or traffic can become unevenly distributed, creating hot shards and reducing scalability.

### 41. What is Database Indexing?

An index is a data structure that allows the database to locate rows more efficiently for supported queries.

### 42. Why can indexes make writes slower?

Indexes must be updated when indexed data changes, which adds write overhead and consumes storage.

### 43. Normalization vs Denormalization?

Normalization reduces redundancy and improves consistency. Denormalization intentionally duplicates data to improve read performance or simplify access patterns.

### 44. When should you Denormalize?

Denormalize when read performance or query simplicity is important and the additional consistency and storage cost is acceptable.

### 45. How do you Scale a Database?

Common approaches include:

- Read replicas
- Sharding
- Partitioning
- Indexing
- Caching
- Denormalization
- Query optimization
- Database clustering
- Archiving old data

---

# Level 4: Distributed Systems

### 46. Explain CAP Theorem.

During a network partition, a distributed system cannot simultaneously guarantee both perfect consistency and availability.

### 47. Consistency vs Availability?

Consistency means clients observe data according to the system's consistency guarantee. Availability means requests continue receiving responses.

### 48. Strong vs Eventual Consistency?

Strong consistency provides a stronger guarantee that reads observe recent successful writes. Eventual consistency allows temporary differences between replicas but expects convergence.

### 49. What happens when a server fails?

A well designed system detects the failure using health checks and redirects traffic to healthy replicas or instances.

### 50. What is Replication?

Replication maintains multiple copies of data or services to improve availability, fault tolerance and sometimes read scalability.

### 51. What is Failover?

Failover is the process of switching traffic or responsibility from a failed component to a healthy backup.

### 52. What is Redundancy?

Redundancy means having additional components or copies so that the system can continue when one component fails.

### 53. What is a Distributed System?

A distributed system consists of multiple independent computers that communicate over a network and work together as one logical system.

### 54. What is Consensus?

Consensus is the process by which distributed nodes agree on a value or decision despite failures and communication delays.

Examples of consensus algorithms include Raft and Paxos.

### 55. What is Distributed Tracing?

Distributed tracing tracks a request across multiple services and components.

### 56. What is Idempotency?

An operation is idempotent if repeating the same operation produces the same intended final effect.

Example:

```text
PUT /users/10
name = A
```

Repeating the request should leave the resource in the same state.

Idempotency is especially important for retries in payment and order systems.

### 57. Why are Retries Dangerous?

If a request partially succeeds but the client does not receive the response, retrying may execute the operation again.

Example:

```text
Payment succeeds
       |
Response lost
       |
Client retries
       |
Second payment attempt
```

Idempotency keys can help prevent duplicate effects.

### 58. What is a Circuit Breaker?

A circuit breaker temporarily stops requests to an unhealthy dependency after failures exceed a threshold.

It prevents a failing dependency from causing cascading failures.

### 59. What is a Dead Letter Queue?

A dead letter queue stores messages that could not be successfully processed after configured retry attempts.

### 60. How do you Handle Duplicate Messages?

Use techniques such as:

- Idempotent consumers
- Unique event IDs
- Deduplication tables
- Idempotency keys
- Transactional processing where appropriate

---

# Level 5: Messaging

### 61. What is a Message Queue?

A message queue stores messages between producers and consumers and allows asynchronous processing.

### 62. Kafka vs RabbitMQ?

Kafka is designed primarily for high throughput distributed event streaming and durable ordered logs.

RabbitMQ is a traditional message broker with flexible routing and queueing semantics.

The choice depends on requirements rather than one being universally better.

### 63. Synchronous vs Asynchronous Communication?

Synchronous communication makes the caller wait for a response.

Asynchronous communication allows the caller to continue while processing occurs separately.

### 64. What is Event Driven Architecture?

Components communicate through events rather than requiring direct synchronous calls between every component.

### 65. What is Pub/Sub?

Publishers send messages to a topic, and multiple subscribers receive messages from that topic.

### 66. What is Event Sourcing?

Event sourcing stores state changes as an append only sequence of events rather than storing only the latest state.

Example:

```text
AccountCreated
MoneyDeposited
MoneyWithdrawn
MoneyDeposited
```

Current state can be reconstructed from events.

### 67. Event Sourcing vs Event Streaming?

Event streaming is about continuously producing and consuming events.

Event sourcing uses events as the source of truth for reconstructing application state.

### 68. How do you Guarantee Message Delivery?

Choose an appropriate delivery model and use durable storage, acknowledgements, retries, idempotent consumers and monitoring.

### 69. At Most Once vs At Least Once vs Exactly Once?

At most once:

```text
Message may be lost.
No duplicate processing.
```

At least once:

```text
Message should be delivered.
Duplicates may occur.
```

Exactly once:

```text
Each logical message is processed exactly once.
```

In distributed systems, exactly once semantics are difficult and often require carefully designed end to end guarantees.

### 70. How do you Handle Failed Consumers?

Use:

- Retries
- Dead letter queues
- Consumer monitoring
- Backoff
- Idempotency
- Partition reassignment where applicable

---

# Level 6: Networking

### 71. What happens when you type google.com?

A simplified flow is:

```text
Browser
  |
DNS lookup
  |
IP address
  |
TCP/TLS connection
  |
HTTP request
  |
Server
  |
HTTP response
  |
Browser renders content
```

In modern protocols and architectures the exact sequence can vary, but DNS, transport/security setup and HTTP request/response are key concepts.

### 72. Explain DNS.

DNS maps domain names to IP addresses and other records.

### 73. What is DNS Caching?

DNS results can be cached by browsers, operating systems, resolvers and other infrastructure to avoid repeated lookups.

### 74. What is TTL?

TTL specifies how long a DNS record can be cached before it should be refreshed.

### 75. HTTP vs HTTPS?

HTTPS is HTTP carried over TLS, providing encryption and authentication properties for the connection.

### 76. TCP vs UDP?

TCP provides reliable ordered byte-stream delivery with congestion and flow control.

UDP provides connectionless datagrams without TCP's built-in reliability and ordering guarantees.

### 77. HTTP/1.1 vs HTTP/2 vs HTTP/3?

HTTP/1.1 commonly uses persistent TCP connections but has limitations in request multiplexing.

HTTP/2 supports multiplexed streams and header compression over a connection.

HTTP/3 uses QUIC over UDP and provides transport features designed to improve connection establishment and behavior under packet loss.

### 78. WebSocket vs HTTP?

HTTP is primarily request-response. WebSocket provides a persistent two-way communication channel.

### 79. Short Polling vs Long Polling?

Short polling repeatedly sends requests at intervals.

Long polling keeps a request open until data is available or a timeout occurs.

### 80. What is TLS?

TLS provides cryptographic protection for network communication, including confidentiality and server authentication.

### 81. What is SSL Termination?

SSL termination means decrypting TLS traffic at a proxy, load balancer or gateway before forwarding the request internally.

---

# 31. System Design Case Studies

The best way to learn system design is to apply concepts to complete systems.

## Beginner

### 1. URL Shortener

Concepts:

- Hashing
- Database
- Cache
- Load balancer
- ID generation
- Read scalability

### 2. Pastebin

Concepts:

- Object storage
- Database
- Short URLs
- Expiration
- Caching

### 3. Parking Lot

Concepts:

- LLD
- OOP
- Design patterns
- Class relationships

### 4. Rate Limiter

Concepts:

- Redis
- Token bucket
- Distributed counters
- API gateway

### 5. File Storage System

Concepts:

- Object storage
- Metadata database
- CDN
- Chunking
- Replication

## Intermediate

### 6. WhatsApp

Concepts:

- WebSockets
- Message queues
- Presence
- Message delivery
- Database sharding
- Push notifications

### 7. Instagram

Concepts:

- Image storage
- CDN
- Feed generation
- Caching
- Database
- Fanout

### 8. Twitter/X

Concepts:

- Timeline
- Fanout
- Caching
- Sharding
- Event streams

### 9. YouTube

Concepts:

- Video upload
- Transcoding
- Object storage
- CDN
- Metadata
- Search
- Recommendations

### 10. Netflix

Concepts:

- Video storage
- CDN
- Streaming
- Recommendation systems
- Microservices
- Caching

### 11. Uber

Concepts:

- Location tracking
- Geospatial indexing
- Real time communication
- Matching
- Event driven architecture

### 12. Dropbox

Concepts:

- File storage
- Chunking
- Synchronization
- Metadata
- Object storage

### 13. Google Drive

Concepts:

- File metadata
- Object storage
- Sharing
- Permissions
- Synchronization
- Search

### 14. Notification System

Concepts:

- Queues
- Workers
- Email
- SMS
- Push notifications
- Retry
- Dead letter queues

### 15. News Feed

Concepts:

- Fanout on write
- Fanout on read
- Ranking
- Caching
- Sharding

## Advanced

### 16. Google Search

Concepts:

- Crawling
- Indexing
- Distributed storage
- Ranking
- Caching
- Query processing

### 17. Distributed Cache

Concepts:

- Consistent hashing
- Replication
- Partitioning
- Eviction
- Failover

### 18. Kafka

Concepts:

- Distributed logs
- Partitions
- Replication
- Consumer groups
- Ordering
- Durability

### 19. Distributed Rate Limiter

Concepts:

- Redis
- Distributed counters
- Token bucket
- Atomic operations
- Consistency

### 20. Real Time Chat System

Concepts:

- WebSockets
- Message queues
- Presence
- Delivery status
- Offline messages
- Push notifications

### 21. Payment System

Concepts:

- Idempotency
- Transactions
- Security
- Audit logs
- Retries
- Reconciliation

### 22. E Commerce System

Concepts:

- Product service
- Cart
- Orders
- Inventory
- Payments
- Search
- Caching

### 23. Ride Sharing System

Concepts:

- Geospatial search
- Real time location
- Matching
- WebSockets
- Event streaming

### 24. Video Streaming Platform

Concepts:

- Object storage
- Transcoding
- CDN
- Adaptive bitrate streaming
- Metadata
- Caching

### 25. Distributed Job Scheduler

Concepts:

- Queues
- Workers
- Scheduling
- Retries
- Leader election
- Idempotency
- Fault tolerance

---

# 32. System Design Interview Framework

When an interviewer gives you a system design problem, follow this structure.

## Step 1: Clarify Requirements

Ask:

- Who are the users?
- What are the major features?
- What scale are we targeting?
- Is the system global?
- What latency is expected?
- What availability is required?
- What consistency is required?

Do not immediately start drawing architecture.

---

## Step 2: Functional Requirements

List the main features.

Example for URL shortener:

```text
1. Create short URL
2. Redirect short URL
3. Optional expiration
4. Optional analytics
```

---

## Step 3: Non Functional Requirements

Example:

```text
High availability
Low read latency
Large scale
Durability
Security
```

---

## Step 4: Capacity Estimation

Estimate:

- Number of users
- Requests per second
- Storage
- Bandwidth
- Read/write ratio

Example:

```text
10 million users
100 requests/user/day

Total requests:
1 billion/day

Average RPS:
1,000,000,000 / 86,400
≈ 11,574 requests/sec
```

Then account for peak traffic.

Capacity estimation helps determine how many servers, database capacity, cache capacity and network bandwidth may be needed.

---

## Step 5: Define APIs

Example:

```text
POST /shorten

Request:
{
    "url": "https://example.com"
}

Response:
{
    "shortUrl": "https://short.ly/abc123"
}
```

---

## Step 6: Design Data Model

Example:

```text
URL

id
short_code
original_url
created_at
expires_at
user_id
```

---

## Step 7: Draw High Level Architecture

Start simple:

```text
Client
  |
Load Balancer
  |
Application
  |
Database
```

Then add components only when requirements justify them:

```text
Client
  |
CDN
  |
Load Balancer
  |
API Gateway
  |
Application Servers
  |
+--------+---------+
|        |         |
Cache   Queue    Database
          |
        Workers
```

---

## Step 8: Scaling

Ask:

> What will become the bottleneck first?

Possible bottlenecks:

- CPU
- Memory
- Database
- Network
- Disk
- Cache
- Queue
- External dependency

Then explain how you would scale it.

---

## Step 9: Failure Handling

Discuss:

- Server failure
- Database failure
- Cache failure
- Queue failure
- Network partition
- External API failure

Use:

- Replication
- Failover
- Retry
- Timeout
- Circuit breaker
- Dead letter queue

---

## Step 10: Security

Discuss:

- Authentication
- Authorization
- Encryption
- HTTPS/TLS
- Rate limiting
- Input validation
- Secrets management

---

## Step 11: Identify Bottlenecks

Always ask:

> What happens when traffic becomes 10x?

Then:

> What happens when traffic becomes 100x?

This shows system design thinking.

---

# Final Interview Checklist

Before your interview, make sure you can explain these without notes:

```text
[ ] HLD vs LLD
[ ] Functional vs Non Functional Requirements
[ ] Vertical Scaling
[ ] Horizontal Scaling
[ ] Stateless Architecture
[ ] Monolith
[ ] Microservices
[ ] Load Balancer
[ ] Round Robin
[ ] Least Connections
[ ] Consistent Hashing
[ ] SQL
[ ] NoSQL
[ ] Replication
[ ] Sharding
[ ] Partitioning
[ ] Indexing
[ ] Normalization
[ ] Denormalization
[ ] Redis
[ ] Cache Aside
[ ] Cache Eviction
[ ] LRU
[ ] LFU
[ ] CDN
[ ] Reverse Proxy
[ ] API Gateway
[ ] Rate Limiting
[ ] Token Bucket
[ ] Message Queue
[ ] Kafka
[ ] RabbitMQ
[ ] Pub/Sub
[ ] Event Driven Architecture
[ ] WebSockets
[ ] Polling
[ ] DNS
[ ] TCP
[ ] UDP
[ ] HTTP
[ ] HTTPS
[ ] TLS
[ ] CAP Theorem
[ ] Strong Consistency
[ ] Eventual Consistency
[ ] Availability
[ ] Reliability
[ ] Fault Tolerance
[ ] Failover
[ ] Idempotency
[ ] Circuit Breaker
[ ] Dead Letter Queue
[ ] Distributed Tracing
[ ] Authentication
[ ] Authorization
[ ] Monitoring
[ ] Logging
[ ] Metrics
[ ] Capacity Estimation
```

---

# Recommended Study Order

Study in this order:

```text
1. System Design Fundamentals
        |
        v
2. Functional + Non Functional Requirements
        |
        v
3. Capacity Estimation
        |
        v
4. HLD
        |
        v
5. Scalability
        |
        v
6. Load Balancers
        |
        v
7. Databases
        |
        v
8. Replication + Sharding
        |
        v
9. Caching + Redis
        |
        v
10. CDN + Reverse Proxy
        |
        v
11. API Gateway + Rate Limiting
        |
        v
12. Message Queues
        |
        v
13. Kafka + Event Driven Systems
        |
        v
14. CAP + Consistency
        |
        v
15. Distributed Systems
        |
        v
16. Security + Observability
        |
        v
17. Complete System Design Problems
```

---

# Most Important Topics for Interviews

If your preparation time is limited, prioritize:

1. Scalability
2. Load Balancing
3. Caching and Redis
4. SQL vs NoSQL
5. Database Replication
6. Database Sharding
7. Consistent Hashing
8. CAP Theorem
9. Consistency Models
10. Message Queues
11. Kafka
12. API Gateway
13. Rate Limiting
14. Idempotency
15. Fault Tolerance
16. Microservices
17. CDN
18. WebSockets
19. Capacity Estimation
20. System Design Case Studies

---

# Final Goal

The goal is not to memorize architecture diagrams.

You should be able to look at a problem and reason:

```text
Requirements
     |
     v
Scale
     |
     v
Bottlenecks
     |
     v
Architecture
     |
     v
Database
     |
     v
Cache
     |
     v
Queue
     |
     v
Scaling
     |
     v
Failure Handling
     |
     v
Security
     |
     v
Trade Offs
```

A strong system design interview answer explains not only:

> "What component should I use?"

but also:

> "Why do I need it, what problem does it solve, what are its alternatives, and what trade offs does it introduce?"
