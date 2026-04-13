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
│   ├── 08-security.md                  ← OAuth2, JWT, mTLS, Secrets, Zero Trust
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

| Element | Usage |
|---------|-------|
| **Tables** | Primary format for concept comparisons and definitions |
| **Mermaid diagrams** | Architecture flows, state machines, sequence diagrams |
| **`??? question`** | Collapsible interview Q&A blocks |
| **`!!! tip` / `!!! note` / `!!! warning`** | Callout boxes for key insights |
| **Bold** | First occurrence of a key term |
| **Description length** | 1–2 lines per concept maximum |

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
