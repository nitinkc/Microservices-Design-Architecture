# Microservices Design Patterns

> The catalog of solutions to recurring problems in distributed systems.

---

## Pattern Categories

```mermaid
graph TD
    A[Microservices Patterns] --> B[Decomposition]
    A --> C[Integration]
    A --> D[Data Management]
    A --> E[Structural · Infra]
    A --> F[Communication]
```

---

## Decomposition Patterns

| Pattern | Description | Use When |
|---------|-------------|---------|
| **Decompose by Business Capability** | Each service owns one business capability aligned with org structure | Conway's Law alignment; stable domain |
| **Decompose by Subdomain** | Use DDD subdomains as service boundaries | More precise; domain model-driven |
| **Strangler Fig** | Gradually replace monolith — new services absorb functionality over time via a facade | Safe monolith migration; no big-bang rewrite |
| **Branch by Abstraction** | Introduce abstraction layer over existing code, swap implementation behind it | Large in-place refactoring |

→ **[Deep Dive: Decomposition Patterns](03.01-decomposition-patterns.md)** — Strangler Fig, business capability vs subdomain decomposition

```mermaid
graph LR
    Client --> Facade[Strangler Fig Facade]
    Facade -->|old routes| Mono[Monolith]
    Facade -->|new routes| MS[New Microservice]
```

---

## Integration Patterns

| Pattern | Description |
|---------|-------------|
| **API Gateway** | Single entry point; handles routing, auth, rate limiting, SSL termination |
| **Backend for Frontend (BFF)** | Separate gateway per client type — mobile BFF, web BFF, partner BFF |
| **Client-Side Service Discovery** | Client queries service registry (Eureka) and picks an instance |
| **Server-Side Service Discovery** | Load balancer queries registry and routes (Kubernetes DNS + kube-proxy) |
| **Anti-Corruption Layer** | Translate between your domain model and an external/legacy model |
| **Gateway Aggregation** | Gateway fans out to multiple services and merges responses |
| **Gateway Offloading** | Push cross-cutting concerns (auth, logging, rate limiting) to the gateway layer |

→ **[Deep Dive: Integration Patterns](03.02-integration-patterns.md)** — API Gateway, BFF, Service Discovery, Gateway Aggregation

---

## Data Management Patterns

| Pattern | Problem Solved |
|---------|----------------|
| **Database per Service** | Each service owns its schema; no direct DB sharing between services |
| **API Composition** | Aggregate data from multiple services at query time (join in application layer) |
| **CQRS** | Separate write model (commands) from read model (queries) for performance |
| **Event Sourcing** | Persist events as source of truth; derive current state by replaying them |
| **Saga Pattern** | Manage distributed transactions across services without 2-phase commit |
| **Outbox Pattern** | Guarantee atomic write to DB AND event publication via same transaction |
| **Shared Database** | **(Anti-pattern)** Multiple services share a DB — creates tight coupling; avoid |

### The Outbox Pattern — Atomic DB Write + Event Publish

```mermaid
sequenceDiagram
    participant App
    participant DB
    participant Poller
    participant Kafka
    App->>DB: Write business data + outbox record (same TX)
    Poller->>DB: Poll outbox table
    Poller->>Kafka: Publish event to topic
    Poller->>DB: Mark record as published
```

> Without this pattern: you can write to DB but crash before publishing — or publish but DB write fails. The outbox removes the race condition.

Tools: **Debezium** (CDC-based), **Spring Modulith** outbox support, Transactional Outbox libraries.

→ **[Deep Dive: Data Management Patterns](03.03-data-management-patterns.md)** — Database per Service, API Composition, CQRS

→ **[Deep Dive: Saga and Outbox Patterns](03.04-saga-and-outbox.md)** — Choreography vs orchestration, CDC, Debezium

---

## Structural / Sidecar Patterns

| Pattern | What It Does | Example |
|---------|-------------|---------|
| **Sidecar** | Co-deployed helper container alongside main app | Envoy proxy, log forwarder, config reloader |
| **Ambassador** | Sidecar that proxies *outgoing* requests — adds retry, circuit breaking, mTLS | Envoy as egress proxy |
| **Adapter** | Sidecar that translates app output/metrics to what the platform expects | Prometheus exporter sidecar |
| **Init Container** | Runs to completion *before* main container starts | DB schema migration, config bootstrap, secret fetch |

```mermaid
graph LR
    subgraph Pod
        App[Main App] --> SC[Sidecar - Envoy]
    end
    SC --> Other[Other Service]
    Monitor[Prometheus] --> SC
```

→ **[Deep Dive: Sidecar Patterns](03.05-sidecar-patterns.md)** — Sidecar, Ambassador, Adapter, Init Container

---

## Service Discovery & Load Balancing

When services scale to multiple instances, clients need to find them dynamically.

| Pattern | Description | When to Use |
|---------|-------------|-----------|
| **Kubernetes DNS** | CoreDNS; zero code changes; transparent | Kubernetes environments (easiest) |
| **Client-Side Discovery** | Client queries registry (Eureka); picks instance | Non-K8s; custom load balancing logic |
| **Server-Side Discovery** | Load balancer queries registry; routes transparently | Separation of concerns |
| **Service Mesh** | Sidecar proxy handles discovery + routing + observability | Large scale; language diversity |

→ **[Deep Dive: Service Discovery & Load Balancing](03.06-service-discovery-and-load-balancing.md)** — Kubernetes DNS, Eureka, Consul, Service Mesh, load balancing algorithms

---

## Cache Patterns

Cache frequently accessed data to reduce database load and latency.

| Pattern | Behavior | Use Case |
|---------|----------|----------|
| **Cache-Aside** | Check cache → miss → load from DB → store in cache | Read-heavy; app controls what to cache |
| **Write-Through** | Write to cache AND DB together | Strong consistency required |
| **Write-Behind** | Write to cache immediately; async flush to DB | High-write; non-critical data; analytics |
| **Distributed Cache** | Multi-node cache (Redis Cluster); sharded data | Horizontal scale; shared cache across services |

**Cache Invalidation:** TTL-based (automatic expiry), Event-based (explicit invalidation), Versioning (cache key versioning).

→ **[Deep Dive: Cache Patterns & Strategies](03.07-cache-patterns.md)** — Cache-aside, Write-through, Write-behind, Cache stampede, Redis sharding, Database sharding

---

## Communication Patterns

| Style | Examples | Trade-off |
|-------|---------|-----------|
| **Synchronous** | REST, gRPC | Simple mental model; tight temporal coupling |
| **Asynchronous** | Kafka, RabbitMQ, SQS | Decoupled; harder to trace; eventual consistency |
| **Async Request-Reply** | Command on request topic, reply on reply topic + correlation ID | Async with response semantics |
| **Publish-Subscribe** | Publisher broadcasts to topic; N consumers | Fan-out; publisher unaware of consumers |
| **Event Streaming** | Continuous event log; consumers replay at their own pace | Kafka; audit trails; reprocessing |

---


---

## 📝 Knowledge Check

<quiz>
A checkout request must write the order row AND publish `OrderCreated`. The service crashes between the DB commit and the Kafka publish. Which pattern closes this hole?
- [ ] Retry the publish manually on restart
- [x] The Outbox pattern: write the event to an `outbox` table in the same transaction as the order — a relay (poller or CDC like Debezium) then publishes it, turning 'DB + broker' into an atomic operation with at-least-once delivery
- [ ] Distributed transaction (2PC) across Postgres and Kafka
- [ ] Increase Kafka producer `acks`

this is the dual-write problem, the single most common data-corruption bug in event-driven systems. The outbox makes the event part of the local transaction; the relay guarantees delivery. Consumers must still be idempotent because delivery is at-least-once. `acks=all` doesn't help — the crash happened *before* the publish call.
</quiz>
<quiz>
Which decomposition gives services that map to 'things the business does' (Order Management, Inventory, Shipping) rather than technical layers — and what's the anti-pattern alternative?
- [ ] Decompose by DAO/entity — one service per table
- [x] Decompose by business capability (or DDD subdomain): services own a whole vertical slice of a business function; the anti-pattern is technical decomposition (UI service, 'business logic' service, 'data' service) which maximizes cross-service calls for every feature
- [ ] Decompose by HTTP verb
- [ ] Decompose randomly — boundaries don't matter

capability-based services let a team own a function end-to-end: most changes land in one service. Entity services (one CRUD service per table) are the seductive anti-pattern — every request choreographs five 'services' that are just table wrappers, and no single service can enforce business invariants.
</quiz>
<quiz>
Your mobile app needs 3 fields; the desktop app needs 30 from the same endpoint. Two responses diverge. Which pattern resolves this without bloating the core API?
- [ ] Make everyone use the 30-field response
- [x] Backend-for-Frontend (BFF): a per-client-type API layer that aggregates/tailors backend calls — mobile BFF returns 3 fields, web BFF returns 30 — each owned by the client team that consumes it
- [ ] GraphQL is mandatory here
- [ ] Add a query parameter `?fields=`

BFF exists because 'one API for all clients' forces either a lowest-common-denominator response or client-specific if/else in the core service. Per-client BFFs keep the domain services generic and let each client team control its own contract. `?fields=` helps slightly but can't fix *shape* differences or orchestration needs.
</quiz>
<quiz>
Client-side vs server-side service discovery — what's the operational difference when a new instance registers?
- [ ] No difference — registration is instant
- [x] Client-side (Eureka + Ribbon-style): each client caches the registry and picks an instance — fast, no extra hop, but discovery logic is in every client and refresh lag can send traffic to dead pods; server-side (K8s DNS/LoadBalancer): the platform routes, clients stay dumb, but you get an extra hop and depend on the LB's health propagation
- [ ] Server-side is always better
- [ ] Client-side requires a service mesh

the choice is where intelligence lives. Client-side puts routing in the app (language-specific, needs heartbeat/refresh handling for stale entries); server-side puts it in the platform (K8s Service DNS is the zero-effort default). In Kubernetes you mostly get server-side discovery free; meshes add L7 routing on top.
</quiz>
<quiz>
Cache-aside vs write-through for a hot product-price cache. What changes on write and on failure?
- [ ] They're identical except naming
- [x] Cache-aside: writes go to DB only; reads populate cache on miss (stale window until TTL/invalidation; DB never blocked by cache writes). Write-through: every write updates DB and cache synchronously (cache always fresh, but write latency includes the cache and a cache failure can fail the write)
- [ ] Write-through means writes skip the DB
- [ ] Cache-aside requires Redis Cluster

cache-aside optimizes write latency and tolerates staleness; you must handle stampede-on-miss and invalidation. Write-through optimizes read freshness at the cost of write coupling to cache availability. Pick based on staleness tolerance and whether writes can afford the extra hop — and for high-read skew, combine with versioned keys or event-based invalidation.
</quiz>