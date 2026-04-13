# Copilot Instructions — Architecture Interview Prep Docs

This is an MkDocs documentation site covering microservices and architectural patterns for senior developers preparing for architecture-role interviews.

---

## Project Structure

```
architectureStyle/
├── mkdocs.yml                          ← Site config and nav
├── requirements.txt                    ← Python deps (mkdocs + pymdown)
├── docs/
│   ├── index.md                        ← Master overview + learning path
│   ├── js/mermaid-init.js              ← Mermaid JS renderer
│   ├── 01-foundations.md               ← Architecture styles, CAP, Conway's Law
│   ├── 02-ddd.md                       ← Strategic + Tactical DDD
│   ├── 03-microservices-patterns.md    ← Decomposition, Integration, Sidecar, Outbox
│   ├── 04-event-driven.md              ← Kafka, CQRS, Event Sourcing, Saga
│   ├── 05-api-communication.md         ← REST, gRPC, GraphQL, Gateway, BFF, Mesh
│   ├── 06-resilience.md                ← Circuit Breaker, Retry, Bulkhead, Throttle
│   ├── 07-observability.md             ← Logs, Metrics, Traces, SLO, OTel
│   ├── 08-security.md                  ← OAuth2, JWT, mTLS, Secrets, Zero Trust [SUMMARY]
│   ├── 08.01-oauth2-and-oidc.md        ← Grant types, PKCE, SAML vs OAuth2 [DEEP DIVE]
│   ├── 08.02-jwt-deep-dive.md          ← RS256 vs HS256, JWKS, vulnerabilities [DEEP DIVE]
│   ├── 08.03-service-to-service-auth.md← mTLS, SPIFFE/SPIRE, K8s SA tokens [DEEP DIVE]
│   ├── 08.04-api-security-patterns.md  ← CORS, rate limiting, OWASP Top 10 [DEEP DIVE]
│   ├── 08.05-secrets-management.md     ← Vault, AWS SM, K8s secrets hardening [DEEP DIVE]
│   ├── 08.06-zero-trust.md             ← Five pillars, OPA, maturity model [DEEP DIVE]
│   ├── 09-deployment.md                ← K8s, Helm, GitOps, Strategies, 12-Factor
│   └── 10-interview.md                 ← Q&A, Trade-offs, ADRs, Spring Boot map
```

---

## How to Add or Expand Content

### Add a new top-level section
1. Create `docs/NN-section-name.md`
2. Add an entry to `nav:` in `mkdocs.yml`
3. Follow the content style guide below

### Add a sub-article within an existing section
When a topic grows large enough to split, use a numbered sub-file pattern:
1. Create `docs/NN-section-name.md` for the main article (e.g. `04-event-driven.md`)
2. Create `docs/04.01-topic-name.md` for the sub-article (e.g. `04.01-kafka-deep-dive.md`)
3. Update `nav:` in `mkdocs.yml`:
```yaml
- "04 · Event-Driven Architecture": 04-event-driven.md
- "04.01 · Kafka Deep Dive": 04.01-kafka-deep-dive.md
```

---

## Deep-Dive Article Pattern (Summary + Sub-articles)

The site uses a **two-tier content model**:

- **`NN-section.md`** — the summary: breadth-first, tables, quick reference. **Never remove or modify existing content here.** Only add `→ Deep Dive:` links.
- **`NN.XX-topic.md`** — focused deep-dives: one topic per file, progressively deeper.

### How 08-Security Was Expanded (Reference Implementation)

08-security.md is the completed example. Apply the same pattern to any other section.

**Step 1 — Identify 5–7 major topic clusters in the summary.**

For 08-security these were: OAuth2/OIDC, JWT, Service-to-Service Auth, API Security, Secrets Management, Zero Trust.

**Step 2 — Create one sub-article per cluster**, numbered `NN.01`, `NN.02`, etc:

```
08-security.md          ← summary, untouched
08.01-oauth2-and-oidc.md
08.02-jwt-deep-dive.md
08.03-service-to-service-auth.md
08.04-api-security-patterns.md
08.05-secrets-management.md
08.06-zero-trust.md
```

**Step 3 — Add a `→ Deep Dive:` link** at the end of each corresponding section in the summary:

```markdown
## OAuth2 & OIDC

... (existing summary content unchanged) ...

→ **[Deep Dive: OAuth2 & OIDC](08.01-oauth2-and-oidc.md)** — Grant types, PKCE flow, SAML vs OAuth2, common mistakes
```

**Step 4 — Structure each sub-article** with this template:

```markdown
# Topic Name — Deep Dive

> **Level:** Beginner | Intermediate | Advanced
> **Pre-reading:** [NN · Parent Summary](NN-section.md) · [NN.XX · Related Article](NN.XX-related.md)

---

## Section 1 ...

## Section 2 ...

---

??? question "Interview question about this topic?"
    Concise answer, 2–4 lines.
```

**Step 5 — Add all sub-articles to `mkdocs.yml`** under the same nav group as the summary:

```yaml
- Operations & Security:
  - "08 · Security": 08-security.md
  - "08.01 · OAuth2 & OIDC": 08.01-oauth2-and-oidc.md
  - "08.02 · JWT Deep Dive": 08.02-jwt-deep-dive.md
  - "08.03 · Service-to-Service Auth": 08.03-service-to-service-auth.md
  - "08.04 · API Security Patterns": 08.04-api-security-patterns.md
  - "08.05 · Secrets Management": 08.05-secrets-management.md
  - "08.06 · Zero Trust": 08.06-zero-trust.md
  - "09 · Deployment & Infrastructure": 09-deployment.md
```

### Learning Level Guidelines

| Level | Who It's For | Depth |
|:------|:------------|:------|
| **Beginner** | Concepts new to the reader | Definitions, diagrams, why it exists |
| **Intermediate** | Reader knows the concept, wants practical detail | Trade-offs, patterns, code examples, common mistakes |
| **Advanced** | Reader wants production-grade knowledge | Full flows, security vulnerabilities, configuration, comparisons |

### Sub-article Content Rules

- Always start with `> **Level:**` and `> **Pre-reading:**` breadcrumb links
- Include at least one Mermaid diagram per sub-article
- End with 2–3 `??? question` interview Q&A blocks
- Keep to the topic of the file — cross-link to other sub-articles rather than duplicating content
- Use `!!! tip`, `!!! warning`, `!!! note` callouts for key insights and gotchas

### Add a Mermaid diagram
Use triple-backtick mermaid blocks:
````
```mermaid
graph TD
    A[ServiceA] --> B[ServiceB]
```
````

---

## Content Style Guide

**Goal:** Breadth over depth. This is a prep guide, not a textbook.

| Element                                    | Usage                                                                             |
|:-------------------------------------------|:----------------------------------------------------------------------------------|
| **Tables**                                 | Primary format for concept comparisons and definitions. Keep columns left aligned |
| **Mermaid diagrams**                       | Architecture flows, state machines, sequence diagrams                             |
| **`??? question`**                         | Collapsible interview Q&A blocks                                                  |
| **`!!! tip` / `!!! note` / `!!! warning`** | Callout boxes for key insights                                                    |
| **Bold**                                   | First occurrence of a key term                                                    |
| **Description length**                     | 1–2 lines per concept maximum                                                     |

### Admonition blocks
```markdown
!!! tip "Title"
    Key insight or recommendation.

!!! warning "Watch out"
    Common mistake or gotcha.

??? question "Interview question text?"
    Short answer, 2–4 lines maximum.
```

---

## Mermaid Diagram Rules

1. **NEVER use `|` inside node labels** `[ ]` — it breaks the mermaid parser. Use `·` (middle dot U+00B7) instead: `[Partition 0 · Partition 1]`
2. Supported start keywords: `graph`, `sequenceDiagram`, `classDiagram`, `stateDiagram`, `erDiagram`, `pie`, `gitGraph`
3. Keep diagrams simple — maximum ~10–12 nodes for rendering reliability
4. Test all diagrams in live preview (`mkdocs serve`) before finalizing
5. Use `--` for dotted edges: `A -.-> B` (dotted), `A --> B` (solid)

---

## Build & Serve Commands

```bash
# First-time setup
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt

# Local preview (hot reload)
python3 -m mkdocs serve
# → http://127.0.0.1:8000

# Production build
python3 -m mkdocs build
# → output in site/
```

---

## Adding Interview Questions

In `docs/10-interview.md`, append to the Q&A section:

```markdown
??? question "What is X?"
    Concise answer in 2–4 lines. Include the key distinction or gotcha.
```

## Adding Spring Boot Mappings

In the Spring Boot mapping table in `docs/10-interview.md`:

```markdown
| Concept Name | Spring Boot library or module |
```

## Adding a Trade-off Entry

In the trade-off table in `docs/10-interview.md`:

```markdown
| **X vs Y** | When to pick X | When to pick Y |
```

---

## Tone & Voice

- Senior-developer-to-senior-developer; skip the basics
- State the pattern, the trade-off, and when NOT to use it
- Prefer concrete examples over abstract descriptions
- Use past tense for events ("OrderPlaced"), imperative for commands ("PlaceOrder")
