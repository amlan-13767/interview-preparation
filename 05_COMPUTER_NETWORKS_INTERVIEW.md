# Computer Networks --- Interview Notes

## 1. Network model

``` mermaid
flowchart TD
    A[Application] --> B[Transport]
    B --> C[Network]
    C --> D[Data Link]
    D --> E[Physical]
```

## 2. OSI model

``` text
7 Application
6 Presentation
5 Session
4 Transport
3 Network
2 Data Link
1 Physical
```

Mnemonic:

> All People Seem To Need Data Processing

Know the role of each layer rather than only memorizing names.

------------------------------------------------------------------------

# 3. TCP/IP model

Common simplified model:

``` text
Application
Transport
Internet
Link / Network Access
```

------------------------------------------------------------------------

# 4. Physical layer

Deals with transmission of raw bits.

Topics:

-   transmission media
-   topology
-   signaling
-   bandwidth

------------------------------------------------------------------------

# 5. Data Link layer

Responsibilities include:

-   framing
-   MAC addressing
-   local delivery
-   error detection
-   flow control
-   switching concepts

### Error detection

Know parity and CRC at a high level.

### ARQ

Automatic Repeat reQuest.

Important forms:

-   Stop-and-Wait
-   Go-Back-N
-   Selective Repeat

------------------------------------------------------------------------

# 6. Network layer

Responsible for logical addressing and routing.

Topics:

-   IPv4
-   IPv6
-   subnetting
-   CIDR
-   routing
-   NAT
-   ICMP
-   ARP

------------------------------------------------------------------------

# 7. IP addresses

IPv4 = 32 bits.

IPv6 = 128 bits.

Private IPv4 ranges commonly used internally include:

``` text
10.0.0.0/8
172.16.0.0/12
192.168.0.0/16
```

------------------------------------------------------------------------

# 8. Subnetting

Subnet mask separates network and host portions.

CIDR:

``` text
192.168.1.0/24
```

`/24` means 24 network-prefix bits.

For IPv4, total addresses in a `/n` block are:

``` text
2^(32-n)
```

Traditional subnet calculations often reserve network/broadcast
addresses; modern networking has special cases such as `/31`
point-to-point links.

------------------------------------------------------------------------

# 9. Routing

### Static routing

Configured manually.

### Dynamic routing

Routers learn/update routes.

### Distance Vector

Routers exchange distance information.

### Link State

Routers build a topology view and compute shortest paths.

Know the high-level ideas behind RIP, OSPF and BGP.

------------------------------------------------------------------------

# 10. Transport layer

Two major protocols:

## TCP

-   connection-oriented
-   reliable byte stream
-   ordered delivery
-   retransmission
-   flow control
-   congestion control

## UDP

-   connectionless datagrams
-   no TCP-style delivery/order guarantee
-   lower overhead
-   useful where latency and application-level control matter

------------------------------------------------------------------------

# 11. TCP 3-way handshake

``` mermaid
sequenceDiagram
    participant C as Client
    participant S as Server
    C->>S: SYN
    S->>C: SYN + ACK
    C->>S: ACK
```

Purpose: establish TCP connection state and synchronize sequence
numbers.

------------------------------------------------------------------------

# 12. TCP termination

Common four-segment pattern:

``` text
FIN
ACK
FIN
ACK
```

Exact behavior can vary depending on who closes first and
simultaneous-close cases.

------------------------------------------------------------------------

# 13. Flow control vs congestion control

### Flow control

Protects the receiver from being overwhelmed.

TCP uses receive-window mechanisms.

### Congestion control

Protects the network from overload.

Know concepts such as:

-   congestion window
-   slow start
-   congestion avoidance
-   retransmission
-   fast retransmit/recovery

------------------------------------------------------------------------

# 14. HTTP

HTTP is an application-layer protocol.

Methods:

-   GET
-   POST
-   PUT
-   PATCH
-   DELETE
-   HEAD
-   OPTIONS

### Safe/idempotent concepts

GET is safe and idempotent by intended semantics.

PUT is idempotent by intended semantics.

POST is generally not idempotent by default.

------------------------------------------------------------------------

# 15. HTTP status codes

``` text
2xx success
3xx redirection
4xx client error
5xx server error
```

Important:

-   200 OK
-   201 Created
-   204 No Content
-   400 Bad Request
-   401 Unauthorized
-   403 Forbidden
-   404 Not Found
-   409 Conflict
-   429 Too Many Requests
-   500 Internal Server Error
-   502 Bad Gateway
-   503 Service Unavailable

------------------------------------------------------------------------

# 16. HTTPS

HTTPS = HTTP protected by TLS.

TLS provides:

-   confidentiality
-   integrity
-   authentication through certificates

High-level handshake:

``` mermaid
sequenceDiagram
    participant C as Client
    participant S as Server
    C->>S: ClientHello
    S->>C: ServerHello + certificate + key exchange data
    C->>S: Key exchange / handshake messages
    C->>S: Encrypted HTTP
    S->>C: Encrypted HTTP
```

Exact TLS 1.3 details differ from older versions; understand the purpose
rather than memorizing obsolete handshake sequences.

------------------------------------------------------------------------

# 17. DNS

Maps names to network addresses.

Simplified:

``` text
Browser
 ↓
OS/browser cache
 ↓
Recursive resolver
 ↓
Root
 ↓
TLD
 ↓
Authoritative DNS
 ↓
IP
```

Common record types:

-   A
-   AAAA
-   CNAME
-   MX
-   NS
-   TXT

------------------------------------------------------------------------

# 18. REST

REST is an architectural style for resource-oriented APIs.

Example:

``` text
GET    /users/10
POST   /users
PATCH  /users/10
DELETE /users/10
```

Good REST APIs generally use:

-   meaningful resources
-   HTTP methods
-   status codes
-   stateless request semantics
-   representations such as JSON

------------------------------------------------------------------------

# 19. Cookies, sessions and tokens

### Cookie

Data stored by browser and sent according to cookie rules.

### Session

Server maintains authenticated state, with client typically carrying a
session identifier.

### Token

Client carries a credential/token that server validates.

Know trade-offs around:

-   HttpOnly
-   Secure
-   SameSite
-   expiration
-   CSRF
-   XSS

------------------------------------------------------------------------

# 20. CORS

Cross-Origin Resource Sharing controls browser access between origins.

A cross-origin request can trigger a preflight using `OPTIONS`,
especially for non-simple requests.

Important distinction:

**CORS is a browser security mechanism, not server-to-server
authorization.**

------------------------------------------------------------------------

# 21. WebSocket

Provides persistent bidirectional communication over a connection.

Useful for:

-   chat
-   live dashboards
-   multiplayer applications
-   real-time notifications

------------------------------------------------------------------------

# 22. Important Q&A

### Q: TCP vs UDP?

TCP provides reliable ordered byte-stream delivery with congestion/flow
control. UDP provides lightweight datagrams without TCP's built-in
delivery/order guarantees.

### Q: What happens when you type a URL?

High-level:

``` text
URL parsing
→ cache checks
→ DNS
→ connection setup (TCP or QUIC depending on protocol)
→ TLS if HTTPS
→ HTTP request
→ server processing
→ response
→ browser rendering
```

### Q: 401 vs 403?

401 indicates authentication is required/failed. 403 means the server
understood the request but refuses authorization.

### Q: DNS vs HTTP?

DNS resolves names to network addresses. HTTP carries application-level
web requests/responses.

### Q: What is CORS?

A browser-enforced mechanism controlling cross-origin resource sharing
based on server response headers and preflight rules.


# 23. Additional CN topics

## MAC vs IP

MAC address identifies a network interface at the local-link level.

IP address provides logical network-layer addressing/routing.

## ARP

Maps an IPv4 address to a MAC address on a local network.

IPv6 uses Neighbor Discovery instead of ARP.

## DHCP

Automatically configures hosts with network parameters.

Common sequence:

```text
Discover → Offer → Request → ACK
```

## NAT

Network Address Translation maps addresses/ports between network domains.

Common home routers translate private addresses to a public address.

## IPv4 vs IPv6

IPv4: 32-bit.

IPv6: 128-bit.

IPv6 provides a much larger address space and changes many protocol details.

## QUIC

Modern transport protocol built over UDP, used by HTTP/3.

Provides reliable streams and integrates TLS 1.3-style security into the protocol design.

## HTTP/1.1 vs HTTP/2 vs HTTP/3

High level:

- HTTP/1.1: persistent connections, textual framing
- HTTP/2: binary framing, multiplexed streams, header compression
- HTTP/3: HTTP over QUIC/UDP

## Proxy vs reverse proxy

Proxy acts on behalf of clients.

Reverse proxy acts on behalf of servers.

Reverse proxies can provide:

- TLS termination
- load balancing
- caching
- routing
- security controls

## Load balancing

Distributes requests among backend instances.

Algorithms:

- round robin
- least connections
- weighted strategies
- hash-based routing

## Latency vs throughput

Latency = time for an operation/request.

Throughput = amount of work/data completed per unit time.

A system can have high throughput but poor individual-request latency.

