<div align="center">

# 🚀 Enterprise GenAI & Agentic DevOps Platform

### *Production-Grade Cloud Infrastructure Architecture for Multi-LLM, RAG & Agentic AI Systems*

![GCP](https://img.shields.io/badge/GCP-4285F4?style=for-the-badge&logo=google-cloud&logoColor=white)
![Kubernetes](https://img.shields.io/badge/Kubernetes-326CE5?style=for-the-badge&logo=kubernetes&logoColor=white)
![Terraform](https://img.shields.io/badge/Terraform_1.5.7-7B42BC?style=for-the-badge&logo=terraform&logoColor=white)
![Helm](https://img.shields.io/badge/Helm_v3-0F1689?style=for-the-badge&logo=helm&logoColor=white)
![Harness](https://img.shields.io/badge/Harness_IACM_--_CD-000000?style=for-the-badge&logo=harness&logoColor=white)
![HashiCorp Vault](https://img.shields.io/badge/HashiCorp_Vault-000000?style=for-the-badge&logo=vault&logoColor=white)
![Sentry](https://img.shields.io/badge/Sentry_APM-362D59?style=for-the-badge&logo=sentry&logoColor=white)
![OpenTelemetry](https://img.shields.io/badge/OpenTelemetry-000000?style=for-the-badge&logo=opentelemetry&logoColor=white)
![LangChain](https://img.shields.io/badge/LangChain_--_LangGraph-1C3C3C?style=for-the-badge&logo=langchain&logoColor=white)

---

</div>

## 🌟 Architecture Overview

This showcase repository presents the architectural blueprint for a **Fortune-500 Standard Enterprise GenAI Platform**. Engineered on Google Kubernetes Engine (GKE), HashiCorp Vault, Harness IACM/CD, and OpenTelemetry, this platform provides the foundational cloud infrastructure to deploy, scale, and secure complex **Generative AI, Agentic AI, RAG (Retrieval-Augmented Generation), and Multi-LLM workloads**.

Designed with strict separation of concerns, zero-trust security governance, keyless OIDC authentication, multi-AZ resilience, and automated cost optimization, this architecture allows enterprise engineering teams to onboard and deploy new custom AI agents in under **5 minutes**.

---

## 🏛️ System Topology

```
                                      ┌────────────────────────────────────────────────────────┐
                                      │            NGINX Ingress / TLS Gateway                 │
                                      └───────────────────────────┬────────────────────────────┘
                                                                  │
                        ┌─────────────────────────────────────────┼────────────────────────────────────────┐
                        │                                         │                                        │
                        ▼                                         ▼                                        ▼
        ┌───────────────────────────────┐         ┌───────────────────────────────┐        ┌───────────────────────────────┐
        │        Security Layer         │         │     GenAI & Agentic Core      │        │     Observability Stack       │
        ├───────────────────────────────┤         ├───────────────────────────────┤        ├───────────────────────────────┤
        │ • Safety Guardrails Proxy     │ ──────► │ • AI Gateway & Token Metering │ ─────► │ • OpenTelemetry Collector     │
        │ • Vault Key Auto-Rotation     │         │ • Agentic Prompt Orchestrator │        │   └─► Sentry Error Exporter   │
        │ • OPA Policy Guardrails       │         │ • RAG Vector Ingestion Engine │        │   └─► Prometheus Metrics      │
        └───────────────────────────────┘         │ • Spark Vector Indexer Cluster│        │ • LangSmith LLM Tracing       │
                                                  └───────────────┬───────────────┘        └───────────────────────────────┘
                                                                  │
                                                  ┌───────────────┴───────────────┐
                                                  │     High-Speed Data Layer     │
                                                  ├───────────────────────────────┤
                                                  │ • Redis Semantic Response Cache│
                                                  │ • Kafka Event Streaming Bus   │
                                                  │ • Model Context Protocol (MCP)│
                                                  └───────────────────────────────┘
```

---

## 🏗️ Complete Unified Terraform Infrastructure Stack (IaC)

> [!IMPORTANT]
> **Complete End-to-End Infrastructure-as-Code**: Under the hood, this platform maintains a **single, unified Terraform stack** that provisions 100% of the underlying GCP infrastructure and CI/CD pipelines in a fully parameterized, repeatable manner.

### What the Terraform Stack Provisions Under the Hood:
- **GKE Cluster & Tiered Node Pools**: Control plane, VPC-native networking, CPU node pool, dedicated GPU pool (NVIDIA L4/T4), Spot preemptible pool, and Node Auto-Provisioning (NAP).
- **Multi-Instance Cloud SQL Topology**: 3 workload-isolated database instances (REST Transactional DB, Vector Search DB with `pgvector` & HNSW indexing, and Analytics/Audit DB) with dynamic pipeline targeting (`TARGET_DB`).
- **Keyless OIDC Workload Identity Connectors**: Pipelines authenticate keylessly via OIDC Workload Identity Federation impersonating `sa-devops-admin` with zero static JSON keys.
- **HashiCorp Vault Dynamic Submodule Secret Wiring**: Zero-Trust secret integration automatically writing dynamic database credentials, GKE endpoints, and SA emails directly to submodule Vault paths (`secret/data/ggl/prod/databases/<submodule>`).
- **Dynamic Service Account Engine**: Template-driven GCP Service Account engine provisioning ~50+ IAM role-bound accounts dynamically without code modifications.
- **Kubernetes Namespace & Vault Secrets**: K8s namespace (`prod-apps`), dynamic Vault secret consumption reading directly from submodule Vault paths, GCS document storage buckets, and private DNS zones (`.ai.internal.ggl.cloud`).
- **GKE Workload Identity**: Explicit IAM bindings connecting Kubernetes Service Accounts (`k8s-sa-{service}`) to GCP Service Accounts via `iam.gke.io/gcp-service-account`.
- **Automated IACM Pipelines**: Standardized 3-stage Harness IACM pipelines executing `init` $\rightarrow$ `plan` $\rightarrow$ `OPA Policy Scan` $\rightarrow$ `apply` across isolated workspaces (`<ENV>_<RESOURCE>_<TARGET_DB>_workspace`).

---

## 🔥 Key Enterprise Innovations & High Availability (HA) Capabilities

### 1. Multi-LLM Gateway Resilience & Automated Circuit Breakers
- **Intelligent Fallback Hierarchy**: OpenAI (`gpt-4o`) $\rightarrow$ Anthropic (`claude-3-5-sonnet`) $\rightarrow$ Google (`gemini-1.5-pro`) $\rightarrow$ Local GPU Inference (`vllm` running `llama-3.1-70b`).
- **Circuit Breaker Failover**: If commercial APIs experience timeouts (>5000ms) or `5xx` errors, the gateway automatically trips circuit breakers and reroutes traffic with **zero application downtime**.

### 2. High Availability (HA) Multi-AZ Topology & Resiliency
- **Multi-AZ `podAntiAffinity`**: Pod replicas are scheduled across distinct availability zones (`topology.kubernetes.io/zone`) to prevent single-zone cloud outages from disrupting services.
- **Dual-Metric HPA & Stabilization**: HPA scales on dual metrics (CPU @ 70% + Memory @ 75%) with a 300s scale-down stabilization window to eliminate metric flapping.
- **Pod Disruption Budgets (PDBs)**: Configured with `minAvailable: 1` to guarantee zero downtime during GKE cluster upgrades and node drains.

### 3. Workload-Isolated Multi-Instance Database Topology
- **REST Transactional Cloud SQL**: Dedicated PostgreSQL 15 instance (`e2-standard-4`) for microservices (`payment`, `user`, `order`).
- **Vector Search Cloud SQL**: Dedicated instance (`db-custom-4-16384`) with native `pgvector` extensions, **`maintenance_work_mem = 1GB`**, **`shared_buffers = 4GB`**, and **HNSW Cosine Vector Indexing** (`vector(1536)`).
- **Analytics & Audit Cloud SQL**: Dedicated instance (`db-custom-8-32768`) for telemetry & audit log archiving.
- **Redis Semantic Caching**: Caches identical prompt responses in-memory, returning results in **<10ms** and cutting LLM token API spending by up to 90%.

### 4. Dual Telemetry Engine & Deep LLM Evaluation
- **Error Observability**: OpenTelemetry Collector streams gRPC OTLP traces and routes exception spans directly to **Sentry** for real-time alerting.
- **GenAI Tracing**: Integrated **LangSmith** engine tracks prompt/response executions, RAG context precision/recall, and token cost telemetry per department.

### 5. Zero-Trust Security & Automated OPA Governance
- **Open Policy Agent (OPA)**: Automated Rego security policies evaluated during infrastructure pipelines, enforcing strict GKE Workload Identity bindings before deployment.
- **NeMo Safety Guardrails Proxy**: Intercepts requests to filter prompt injection attacks, jailbreak attempts, toxic content, and PII leaks.
- **HashiCorp Vault Integration**: Auto-injects and rotates API keys (`OPENAI_API_KEY`, `ANTHROPIC_API_KEY`) directly in pod memory with zero static secrets.

---

## 💼 Demonstration & Private Repository Access

Due to proprietary architectural implementations, the underlying IaC modules, Helm library definitions, and pipeline execution files reside in a **private enterprise repository**.

If you are a **recruiter, hiring manager, engineering leader, or technology partner** interested in exploring the complete codebase, reviewing a live demonstration, or discussing architectural implementation details, feel free to reach out directly:

<div align="center">

| Contact Channel | Link / Address |
| :--- | :--- |
| **📧 Email** | [pavan.chinthaginjala07@gmail.com](mailto:pavan.chinthaginjala07@gmail.com) |
| **💼 LinkedIn** | [Pavan Kumar Chinthaginjala](https://www.linkedin.com/in/pavankumar557/) |

---

*Available for Live Technical Walkthroughs & Architecture Review Sessions*

</div>
