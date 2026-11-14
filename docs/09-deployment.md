# Deployment & Infrastructure

> Ship confidently. From a Dockerfile to a production canary deployment via GitOps.

## Containers

| Concept             | Description                                                                   |
|:--------------------|:------------------------------------------------------------------------------|
| **Docker image**    | Layered, immutable artifact built from a Dockerfile; build once, run anywhere |
| **Dockerfile**      | Recipe for the image; use multi-stage builds to minimize final image size     |
| **Container vs VM** | Containers share the host OS kernel; lighter and faster but less isolated     |
| **Registry**        | Stores and distributes images; Docker Hub, AWS ECR, GCR, Harbor               |

**Dockerfile best practices:** non-root user, minimal base image (distroless/alpine), no secrets in layers, multi-stage builds, pin versions.

→ **[Deep Dive: Containers and Docker](09.01-containers-and-docker.md)** — Dockerfile best practices, multi-stage builds, image security

## Kubernetes Core Concepts

```mermaid
graph TD
    ING[Ingress] --> SVC[Service - Stable Endpoint]
    SVC --> P1[Pod 1]
    SVC --> P2[Pod 2]
    SVC --> P3[Pod 3]
    DEP[Deployment] --> P1
    DEP --> P2
    DEP --> P3
    CM[ConfigMap] --> P1
    SEC[Secret] --> P1
```


| Object            | Description                                                                       |
|:------------------|:----------------------------------------------------------------------------------|
| **Pod**           | Smallest deployable unit; 1+ containers sharing network and storage               |
| **Deployment**    | Manages ReplicaSet; desired-state declarative updates; rollback support           |
| **Service**       | Stable network endpoint for a set of pods (ClusterIP, NodePort, LoadBalancer)     |
| **Ingress**       | HTTP/HTTPS routing to services; TLS termination; path-based routing               |
| **ConfigMap**     | Non-sensitive configuration as key-value or config files                          |
| **Secret**        | Sensitive config; base64 encoded at rest — enable encryption at rest with KMS     |
| **Namespace**     | Logical isolation; multi-tenancy within a cluster                                 |
| **HPA**           | Horizontal Pod Autoscaler; scale replicas based on CPU, memory, or custom metrics |
| **VPA**           | Vertical Pod Autoscaler; right-size CPU/memory requests and limits                |
| **PVC / PV**      | PersistentVolumeClaim requests storage; PersistentVolume is provisioned storage   |
| **RBAC**          | Role-based access control for the Kubernetes API; least privilege                 |
| **NetworkPolicy** | Firewall rules for pod-to-pod communication (east-west traffic control)           |

→ **[Deep Dive: Kubernetes Core](09.02-kubernetes-core.md)** — Pods, Deployments, Services, ConfigMaps, Secrets, RBAC, HPA

## Deployment Strategies


| Strategy                 | Description                                                            | Downtime | Rollback Speed  |
|:-------------------------|:-----------------------------------------------------------------------|:---------|:----------------|
| **Recreate**             | Stop all old pods, start new                                           | Yes      | N/A — commit    |
| **Rolling Update**       | Replace pods in batches; old and new briefly coexist                   | No       | Slow            |
| **Blue-Green**           | Two identical environments; flip load balancer instantly               | No       | Instant         |
| **Canary**               | Route small % of traffic to new version; observe; expand gradually     | No       | Fast            |
| **A/B Testing**          | Different versions for different user segments (by header, ID, region) | No       | Per segment     |
| **Shadow (Dark Launch)** | Copy live traffic to new version; ignore its responses                 | No       | N/A             |
| **Feature Flags**        | Toggle features in code without deployment; runtime control            | No       | Instant toggle  |

```mermaid
graph LR
    LB[Load Balancer] -->|90 percent| BLUE[Blue - v1 stable]
    LB -->|10 percent| GREEN[Green - v2 canary]
```

→ **[Deep Dive: Deployment Strategies](09.03-deployment-strategies.md)** — Rolling, Blue-Green, Canary, Feature Flags

## Helm

| Concept                | Description                                           |
|:-----------------------|:------------------------------------------------------|
| **Chart**              | Package of Kubernetes manifests with Go templating    |
| **Values**             | `values.yaml` — parameters that customize the chart   |
| **Release**            | A deployed instance of a chart in a cluster           |
| **Repository**         | Chart storage; Artifact Hub, AWS ECR, OCI registries  |
| **Upgrade / Rollback** | `helm upgrade` / `helm rollback <release> <revision>` |

→ **[Deep Dive: Helm](09.04-helm.md)** — Charts, values, releases, environment overrides

## GitOps

| Concept                 | Description                                                                           |
|:------------------------|:--------------------------------------------------------------------------------------|
| **GitOps principle**    | Git is the single source of truth for all infrastructure and app config               |
| **Pull-based delivery** | An agent inside the cluster pulls changes from Git (safer; no external push access)   |
| **Push-based delivery** | CI pipeline pushes changes to cluster; simpler but requires cluster credentials in CI |
| **ArgoCD**              | Kubernetes-native GitOps; UI, sync policies, RBAC, drift detection                    |
| **Flux**                | Lightweight GitOps toolkit; Helm and Kustomize support; CNCF graduated                |

→ **[Deep Dive: GitOps](09.05-gitops.md)** — Pull vs Push, ArgoCD, Flux, image update automation

## Service Mesh — Istio Key Concepts

| Component               | Role                                                                       |
|:------------------------|:---------------------------------------------------------------------------|
| **Envoy sidecar**       | Proxy injected into every pod; intercepts all inbound and outbound traffic |
| **Istiod**              | Control plane; pushes config to all sidecars; manages certificates         |
| **VirtualService**      | Traffic routing: canary weights, fault injection, retry rules, timeouts    |
| **DestinationRule**     | Load balancing algorithm, circuit breaking, connection pool settings       |
| **PeerAuthentication**  | Enforce STRICT mTLS between all services in a namespace                    |
| **AuthorizationPolicy** | L7 access control; allow/deny by service account, namespace, HTTP method   |

→ **[Deep Dive: Service Mesh with Istio](09.06-service-mesh-istio.md)** — Envoy, Istiod, VirtualService, mTLS

## The 12-Factor App

| Factor                       | Principle                                                           |
|:-----------------------------|:--------------------------------------------------------------------|
| 1. **Codebase**              | One repo; multiple deploys (dev, staging, prod)                     |
| 2. **Dependencies**          | Explicitly declared in manifest; no implicit system packages        |
| 3. **Config**                | In environment variables; never in code                             |
| 4. **Backing services**      | Attached resources accessed via URL (DB, cache, queue)              |
| 5. **Build / Release / Run** | Strictly separated stages; immutable releases                       |
| 6. **Processes**             | Stateless; share-nothing; state in external backing service         |
| 7. **Port binding**          | App exports via port; no web server injection                       |
| 8. **Concurrency**           | Scale out via process model; horizontal scaling                     |
| 9. **Disposability**         | Fast startup; graceful shutdown on SIGTERM                          |
| 10. **Dev/Prod parity**      | Keep all environments as similar as possible                        |
| 11. **Logs**                 | Treat as event streams; write to stdout; aggregator handles routing |
| 12. **Admin processes**      | Run as one-off processes in the same environment                    |

→ **[Deep Dive: 12-Factor App and IaC](09.07-twelve-factor-and-iac.md)** — 12-Factor principles, Terraform, Pulumi

## CI/CD Pipeline Stages

```mermaid
graph LR
    A[Code Push] --> B[Build]
    B --> C[Unit Tests]
    C --> D[SAST Scan]
    D --> E[Container Build]
    E --> F[Container Scan]
    F --> G[Push Registry]
    G --> H[Deploy Dev]
    H --> I[Integration Tests]
    I --> J[Deploy Staging]
    J --> K[Smoke Tests]
    K --> L[Deploy Prod Canary]
```

**Infrastructure as Code tools:**

| Tool          | Description                                      |
|:--------------|:-------------------------------------------------|
| **Terraform** | Declarative HCL; multi-cloud; state management   |
| **Pulumi**    | IaC with real languages (TypeScript, Python, Go) |
| **AWS CDK**   | TypeScript/Python → CloudFormation               |
| **Helm**      | K8s-specific; package and deploy K8s manifests   |
| **Kustomize** | K8s config overlay patches without templating    |

---

## 📝 Knowledge Check

<quiz>
Which Dockerfile practices are non-negotiable for production: `FROM node:latest`, running as root, copying the whole repo?
- [ ] All are fine for prod
- [x] None: pin a digest/version (`latest` is a moving target — rebuilds differ silently); run as a non-root USER (container breakout then isn't root-on-host); COPY only what's needed (context size, layer cache, and no leaking source/.git/secrets into the image); add a HEALTHCHECK and multi-stage builds to keep the runtime image minimal
- [ ] Only pinning matters
- [ ] Root is required for permissions

production images are about reproducibility and least privilege: `latest` makes 'the same build' non-deterministic; root-in-container often means root-adjacent on escape; fat COPYs bake build tools and secrets into the runtime attack surface. Multi-stage builds separate 'build deps' from 'runtime image' — the shipped image should contain only what runs.
</quiz>
<quiz>
A Deployment's readiness probe never passes — pods exist but get no traffic. vs liveness failing — pods restart in a loop. Explain the difference.
- [ ] Same probe, different name
- [x] Readiness: 'may I receive traffic?' — failing = pod stays up but is pulled from Service endpoints (for warm-up, or to shed a temporarily-overloaded pod without killing it). Liveness: 'am I alive?' — failing = kubelet restarts the container (for deadlocks/stuck states). Mis-wiring them (readiness checks that fail under load = traffic death spiral) is a classic self-inflicted outage
- [ ] Liveness gates traffic; readiness restarts
- [ ] Both restart the pod

readiness ≠ liveness, and confusing them causes real outages: a readiness probe that fails under load removes endpoints exactly when you need capacity (cascading 'unready' storm); a liveness probe on a slow-starting app creates a crash-loop. Readiness should reflect 'can serve,' liveness should reflect 'needs restart' — they're different questions.
</quiz>
<quiz>
Rolling update vs blue-green vs canary — pick for: (a) routine low-risk release, (b) a risky payments change needing instant rollback, (c) validating on 1% of real traffic first.
- [ ] Canary for everything
- [x] (a) Rolling — gradual, cheap, default. (b) Blue-green — full parallel environment, traffic flips atomically, instant rollback by flipping back (at 2× infra cost). (c) Canary — real-traffic validation at small blast radius before committing the full rollout
- [ ] Blue-green for everything — safest
- [ ] Rolling is only for dev

match the strategy to the risk and the rollback story: rolling is free but mixes versions and rolls back slowly; blue-green doubles cost for a safe atomic cutover; canary buys *evidence* — metrics on 1% before 100%. The error is choosing by fashion: blue-green for every release wastes infra; rolling-only for a risky payments change has no fast 'undo.'
</quiz>
<quiz>
GitOps vs 'CI pushes to cluster': what does the pull model (ArgoCD/Flux) give you that `kubectl apply` from Jenkins doesn't?
- [ ] Same outcome, different tool
- [x] Git as the desired-state source of truth + a controller continuously reconciling actual→desired: drift is auto-corrected (someone's manual `kubectl edit` gets reverted), rollback is `git revert`, the cluster needs no CI credentials pushing in (pull model = safer), and the audit trail IS git history — not a Jenkins log
- [ ] GitOps is just YAML in git
- [ ] Push is more secure

the difference is *who converges and where truth lives*: CI-push is a one-shot write (drift after that is invisible); GitOps makes desired state declarative and continuously enforced, with git as the audit/rollback mechanism. Push also requires CI to hold god-mode cluster credentials; pull keeps credentials inside the cluster.
</quiz>
<quiz>
'Helm vs Kustomize' — when does templating beat overlaying?
- [ ] Always Helm — it's the standard
- [x] Helm (templates + values): package distribution, parameterization, release tracking/rollback, hooks — right for *shipping a chart others install* or complex parameterized apps. Kustomize (overlays on plain YAML): no templating language, simpler diffs, environment overlays — right for *your own* manifests across environments where you value readability over package features
- [ ] Kustomize for third-party charts
- [ ] Helm can't do environments

Helm's power is distribution + release management; its cost is template logic that hides what the manifest actually is. Kustomize keeps YAML as YAML — every overlay is a readable patch. Many teams use both: Helm for third-party charts (public repos publish charts, not kustomizations), Kustomize for their own env-specific config.
</quiz>