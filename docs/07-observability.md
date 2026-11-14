# Observability

> You can't fix what you can't see. Observability is the foundation of reliable operations.

---

## The Three Pillars

```mermaid
graph TD
    O[Observability] --> L[Logs - What happened]
    O --> M[Metrics - How much and how fast]
    O --> T[Traces - Where did time go]
```

| Pillar | Question Answered | Tool Stack |
|--------|-----------------|-----------|
| **Logs** | What happened? What errors occurred? | ELK Stack, Grafana Loki, Splunk |
| **Metrics** | How many requests? CPU? Error rate? | Prometheus + Grafana, Datadog |
| **Traces** | Where did this request spend its time across services? | Jaeger, Zipkin, AWS X-Ray, Honeycomb |

!!! tip "Observability vs Monitoring"
    Monitoring answers *known* questions (is CPU high?). Observability lets you ask *unknown* questions about system behavior from the outside.

---

## Logging

| Best Practice | Description |
|---------------|-------------|
| **Structured logging** | Emit JSON; machine-parseable; fields queryable by log aggregator |
| **Correlation ID** | Unique per-request ID propagated through all services via headers |
| **Log levels** | ERROR (needs action), WARN (degraded), INFO (business events), DEBUG (dev only — never in prod) |
| **Never log secrets** | No PII, tokens, passwords, or card numbers in logs — ever |
| **Centralize** | Ship to central store (not pod local); pods are ephemeral |

**ELK Stack flow:**
```
App → Logstash / Fluentd / Fluent Bit → Elasticsearch ← Kibana (visualize + alert)
```

→ **[Deep Dive: Logging](07.01-logging.md)** — Structured logging, correlation IDs, ELK stack, log levels

---

## Metrics

| Metric Type | Description | Example |
|-------------|-------------|---------|
| **Counter** | Monotonically increasing; resets on restart | Total HTTP requests, total errors |
| **Gauge** | Current snapshot; can go up or down | Active connections, memory used |
| **Histogram** | Distribution of values in configurable buckets | Request latency (50ms, 100ms, 500ms buckets) |
| **Summary** | Pre-calculated quantiles client-side | P50, P95, P99 latency |

### RED Method — For Service Health

| Letter | Metric |
|--------|--------|
| **R** — Rate | Requests per second |
| **E** — Errors | Error rate (%) |
| **D** — Duration | Latency distribution (P50, P95, P99) |

### USE Method — For Resource Health

| Letter | Metric |
|--------|--------|
| **U** — Utilization | How busy is the resource (%) |
| **S** — Saturation | How much work is queued / waiting |
| **E** — Errors | Errors on the resource |

→ **[Deep Dive: Metrics](07.02-metrics.md)** — Counter/Gauge/Histogram, RED and USE methods, Prometheus

---

## Distributed Tracing

```mermaid
sequenceDiagram
    participant Client
    participant GW as API Gateway
    participant OS as Order Service
    participant PS as Payment Service
    Client->>GW: Request TraceId=abc SpanId=1
    GW->>OS: TraceId=abc SpanId=2 ParentId=1
    OS->>PS: TraceId=abc SpanId=3 ParentId=2
    PS-->>OS: 200 OK
    OS-->>GW: 200 OK
    GW-->>Client: 200 OK
```

| Concept | Description |
|---------|-------------|
| **Trace** | Full end-to-end journey of one request across all services |
| **Span** | One operation within a trace; has start time, duration, and metadata |
| **Context Propagation** | TraceId + SpanId passed via HTTP headers (B3 format or W3C TraceContext) |
| **OpenTelemetry (OTel)** | CNCF vendor-neutral standard; collects logs, metrics, and traces |
| **Head-based sampling** | Decide to trace at request entry; consistent but may miss errors |
| **Tail-based sampling** | Decide *after* seeing full trace; captures errors; more complex |

→ **[Deep Dive: Distributed Tracing](07.03-distributed-tracing.md)** — Spans, context propagation, sampling strategies

---

## SLI · SLO · SLA · Error Budget

| Term | Definition | Example |
|------|-----------|---------|
| **SLI** (Indicator) | The metric being measured | `successful_requests / total_requests` |
| **SLO** (Objective) | Target value for the SLI | 99.9% availability |
| **SLA** (Agreement) | Contractual commitment with penalties | 99.5% or 10% refund |
| **Error Budget** | `100% - SLO target` | 0.1% = ~43.8 min/month allowed downtime |

> Error budgets drive decisions: if the budget is burning fast, freeze feature deployments and focus on reliability.

→ **[Deep Dive: SLI, SLO, SLA](07.04-sli-slo-sla.md)** — Error budgets, burn rate alerting, SRE approach

---

## Health Checks & Kubernetes Probes

| Probe | Question | Pod Action if Fails |
|-------|---------|---------------------|
| **livenessProbe** | Is the app alive (not stuck/deadlocked)? | Restart the pod |
| **readinessProbe** | Is the app ready to receive traffic? | Remove from Service endpoints |
| **startupProbe** | Has slow-starting app finished initializing? | Pauses liveness/readiness checks |

---

## Alerting Best Practices

| Principle | Description |
|-----------|-------------|
| **Alert on symptoms, not causes** | Alert on SLO burn rate (user impact), not individual CPU spikes |
| **Avoid alert fatigue** | Too many alerts → all ignored; tune and reduce ruthlessly |
| **Actionable alerts only** | Every alert should require a human action |
| **On-call rotation** | Use PagerDuty / OpsGenie; document runbooks for every alert |
| **Escalation paths** | Define who gets paged and when escalation triggers |

---

## OpenTelemetry (OTel) in Spring Boot

| Component | Description |
|-----------|-------------|
| **SDK** | Instrument your app (auto or manual) |
| **Collector** | Receives, processes, and exports telemetry |
| **Exporters** | Send to Jaeger, Zipkin, Prometheus, Datadog, etc. |
| **Spring Boot support** | `spring-boot-starter-actuator` + Micrometer + OTel integration |

→ **[Deep Dive: OpenTelemetry](07.05-opentelemetry.md)** — SDK, Collector, auto-instrumentation, Spring Boot integration

---

## DORA Metrics

DevOps Research and Assessment (DORA) measures software delivery performance:

| Metric | Measures | Target |
|:-------|:---------|:--------|
| **Deployment Frequency** | How often code reaches production | 3+ per day (elite) |
| **Lead Time for Changes** | Code commit → production | < 1 hour (elite) |
| **Change Failure Rate** | % of deployments causing incidents | 0–15% (elite) |
| **Mean Time to Recovery** | Time to restore service after incident | < 1 hour (elite) |

Elite performers ship 3+ times per day with low defect rates and recover from incidents in under an hour.

→ **[Deep Dive: DORA Metrics](07.06-dora-metrics.md)** — Deployment frequency, lead time, change failure rate, MTTR measurement and improvement


---

## 📝 Knowledge Check

<quiz>
Logs, metrics, traces — which pillar answers which question at 3 a.m.?
- [ ] They're interchangeable — pick your favorite tool
- [x] Metrics: 'IS something wrong and how much' (dashboards, alerts — cheap aggregates, bad at 'why'). Traces: 'WHERE in the request path is it wrong' (span waterfall across services). Logs: 'WHY exactly — the detailed evidence' (rich, expensive, per-event). You navigate metrics → trace → log, not one pillar alone
- [ ] Logs answer everything — just grep harder
- [ ] Traces are only for debugging latency

the pillars are complementary zoom levels: metrics detect (RED on dashboards), traces localize (which span is red), logs explain (the exception inside the span). A system with only logs drowns you in detail with no detection; only metrics tells you it's broken, not where; only traces requires you to already know where to look.
</quiz>
<quiz>
RED vs USE — which do you apply to a service vs. a database node?
- [ ] RED for everything
- [x] RED (Rate, Errors, Duration) for request-driven *services* — the user-facing symptom triad. USE (Utilization, Saturation, Errors) for *resources* — CPU, disk, DB pool, network — where 'how busy is the thing' predicts failure before requests error
- [ ] USE is for users only
- [ ] RED is only for Redis

RED sees the *demand* side ('how are calls doing'), USE sees the *supply* side ('how full is the resource'). A DB at 95% pool saturation (USE) shows trouble before errors spike (RED) — leading vs lagging indicators. Monitor services with RED, infrastructure with USE, and connect the two when root-causing.
</quiz>
<quiz>
Why does a trace ID in every log line change your life during an incident?
- [ ] It makes logs prettier
- [x] It converts 'search 50 services' logs for clues' into 'follow one request's exact path': filter by trace ID and you see the full causal chain of a single failing request across every service — the difference between hours of correlation and a single query
- [ ] It's required for GDPR
- [ ] It reduces log volume

correlation is the entire game in distributed debugging: without a propagated ID, five services logging the same failing request are five uncorrelated streams. Context propagation (W3C traceparent, B3) threading one ID through every hop is what makes 'distributed' debuggable — and it's free once instrumentation adds it to your log MDC.
</quiz>
<quiz>
SLO 99.9% monthly — how much error budget is that, and what's it FOR?
- [ ] Zero tolerance — always aim for no errors
- [x] ~43 minutes/month of downtime-equivalent: the budget is your *permission slip* for risk — deploys, experiments, and retries that consume budget are allowed; when the budget burns too fast, you freeze feature work for reliability. It's a negotiation instrument between speed and stability, not a failure metric
- [ ] It's the SLA penalty clause
- [ ] It resets weekly

100% reliability is infinitely expensive and usually unnecessary — the budget formalizes 'how wrong can we be.' Teams that treat 99.9% as 'must never error' can't ship; the error-budget framing turns it into 'here's the risk currency we can spend.' SLA ≠ SLO: SLA is the contract with consequences, SLO is the internal target you alert on *before* breaching the SLA.
</quiz>
<quiz>
Alert fatigue: on-call ignores the dashboard because 90% of pages are noise. What's the fix?
- [ ] More alerts — coverage is safety
- [x] Alert on symptoms users feel (SLO burn rate, error budget consumption), not causes (CPU high); every alert must be actionable ('if this pages me, I do X'), else it's a dashboard metric not a page — and routinely delete/quiet alerts that fire without consequence
- [ ] Route alerts to email
- [ ] Set thresholds higher so they page less

pages are for 'human must act now': symptom-based alerting (users can't check out) not cause-based (node at 85% — which is fine until it isn't). An alert that fires and resolves without action trains the team to ignore it — every non-actionable page is a bug in your monitoring, not in the system. Burn-rate alerting on SLOs is the modern standard for exactly this reason.
</quiz>