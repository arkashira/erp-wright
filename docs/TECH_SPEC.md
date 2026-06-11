# Technical Specification – erp‑wright

> **Version**: 1.0.0  
> **Author**: Axentx Engineering Lead  
> **Date**: 2026‑06‑11  

---

## 1. Overview

erp‑wright is an AI‑driven automation platform that empowers small‑to‑medium manufacturers to modernize legacy ERP environments. It delivers:

* **AI Workflow Agents** – autonomous agents that learn and execute repetitive manufacturing tasks.  
* **Universal ERP Connectors** – secure, zero‑downtime adapters for SAP, Oracle EBS, Infor, and legacy COBOL databases.  
* **Low‑code Workflow Builder** – visual designer for non‑technical factory managers.  
* **Real‑time Operations Dashboard** – KPI monitoring, exception handling, and ROI analytics.  
* **Hybrid Deployment** – core AI engine in the cloud, connectors on‑prem for data‑compliance.

The platform is built to run on resource‑constrained production environments (<500 employees) and is designed for rapid, low‑code deployment.

---

## 2. Architecture

```
┌───────────────────────┐
│ 1. Frontend (Web)     │
│   • React + TS        │
│   • Redux Toolkit     │
│   • Ant Design        │
└─────────────┬─────────┘
              │
              ▼
┌───────────────────────┐
│ 2. API Gateway         │
│   • FastAPI (Python3.11)│
│   • OAuth2 + JWT       │
│   • Rate limiting      │
└─────────────┬─────────┘
              │
              ▼
┌───────────────────────┐
│ 3. Core AI Engine      │
│   • LangGraph          │
│   • Llama‑3‑70B (FT)   │
│   • vLLM for inference│
│   • Agent orchestration│
└─────────────┬─────────┘
              │
              ▼
┌───────────────────────┐
│ 4. Connector Layer     │
│   • SAP RFC SDK        │
│   • Oracle OCI SDK     │
│   • Infor API SDK      │
│   • Custom COBOL bridge│
│   • Data schema mapper │
└─────────────┬─────────┘
              │
              ▼
┌───────────────────────┐
│ 5. Data Store          │
│   • PostgreSQL 15      │
│   • TimescaleDB (metrics)│
│   • Redis (cache)      │
└─────────────┬─────────┘
              │
              ▼
┌───────────────────────┐
│ 6. Monitoring & Ops    │
│   • Prometheus         │
│   • Grafana            │
│   • Loki (logs)        │
└───────────────────────┘
```

### 2.1 Frontend

* **Framework**: React 18 + TypeScript  
* **State**: Redux Toolkit with RTK Query for API calls  
* **UI Library**: Ant Design (responsive, accessible)  
* **Deployment**: Docker image served via Nginx reverse proxy

### 2.2 API Gateway

* **Framework**: FastAPI (Python 3.11)  
* **Auth**: OAuth2 Resource Owner Password Flow + JWT; role‑based access control (Admin, Manager, Operator)  
* **Endpoints**  
  * `/api/workflows` – CRUD, deploy, test  
  * `/api/connectors` – list, configure, health  
  * `/api/metrics` – real‑time KPI streams (WebSocket)  
  * `/api/docs` – OpenAPI UI

### 2.3 Core AI Engine

* **Agent Framework**: LangGraph (Python) – state‑ful graph of tasks  
* **LLM**: Llama‑3‑70B fine‑tuned on manufacturing domain (≈ 200 k tokens)  
* **Inference**: vLLM for low‑latency, multi‑tenant inference  
* **Orchestration**: Celery‑beat + Redis for task scheduling; workers auto‑scale in Kubernetes  

### 2.4 Connector Layer

* **Adapters**:  
  * SAP: RFC SDK (Java) wrapped in Python via JPype  
  * Oracle EBS: OCI SDK (Python)  
  * Infor: REST/Soap SDK (Python)  
  * COBOL: Custom Java bridge exposing JDBC‑style API  
* **Schema Mapper**: Auto‑detect entity relationships, expose as GraphQL schema for frontend  
* **Security**: TLS 1.3, HSM‑backed key storage, audit logging

### 2.5 Data Store

* **Operational DB**: PostgreSQL 15 (schema‑first, migrations via Alembic)  
* **Metrics**: TimescaleDB for time‑series KPI data  
* **Cache**: Redis 7 for session tokens, LLM cache, connector health

### 2.6 Monitoring & Ops

* **Metrics**: Prometheus exporters on all services  
* **Dashboards**: Grafana dashboards for latency, error rates, resource usage  
* **Logging**: Loki + FluentBit, structured JSON logs  
* **Alerting**: Alertmanager with Slack/Email notifications

---

## 3. Data Model

| Table | Purpose | Key Columns | Notes |
|-------|---------|-------------|-------|
| `users` | Auth & RBAC | `id`, `email`, `hashed_pw`, `role` | |
| `workflows` | Workflow definitions | `id`, `name`, `description`, `graph_json`, `status` | `graph_json` stores LangGraph state |
| `workflow_runs` | Execution logs | `id`, `workflow_id`, `started_at`, `ended_at`, `status`, `metrics_json` | |
| `connectors` | Adapter configs | `id`, `erp_type`, `config_json`, `status` | `config_json` holds credentials, endpoints |
| `erp_entities` | Unified schema | `id`, `erp_type`, `entity_name`, `schema_json` | Generated by schema mapper |
| `metrics` | KPI time‑series | `id`, `workflow_id`, `timestamp`, `metric_name`, `value` | Stored in TimescaleDB |
| `audit_logs` | Security audit | `id`, `user_id`, `action`, `timestamp`, `details_json` | |

---

## 4. Key APIs / Interfaces

### 4.1 Workflow API

| Method | Path | Description | Auth |
|--------|------|-------------|------|
| POST | `/api/workflows` | Create new workflow | Admin |
| GET | `/api/workflows/{id}` | Retrieve workflow | Admin/Manager |
| PUT | `/api/workflows/{id}` | Update workflow | Admin |
| DELETE | `/api/workflows/{id}` | Delete workflow | Admin |
| POST | `/api/workflows/{id}/deploy` | Deploy workflow to runtime | Admin |
| POST | `/api/workflows/{id}/test` | Run dry‑run | Manager |
| GET | `/api/workflows/{id}/runs` | List executions | Manager |

### 4.2 Connector API

| Method | Path | Description | Auth |
|--------|------|-------------|------|
| GET | `/api/connectors` | List all connectors | Admin |
| POST | `/api/connectors` | Add new connector | Admin |
| GET | `/api/connectors/{id}` | Get connector config | Admin |
| PUT | `/api/connectors/{id}` | Update connector | Admin |
| DELETE | `/api/connectors/{id}` | Remove connector | Admin |
| POST | `/api/connectors/{id}/health` | Run health check | Admin |

### 4.3 Metrics API

| Method | Path | Description | Auth |
|--------|------|-------------|------|
| GET | `/api/metrics/workflow/{id}` | Stream KPI via SSE | Manager |
| GET | `/api/metrics/summary` | Aggregated KPI | Manager |

### 4.4 Auth API

* `/auth/login` – OAuth2 token endpoint  
* `/auth/logout` – revoke token

---

## 5. Tech Stack

| Layer | Technology | Rationale |
|-------|------------|-----------|
| **Frontend** | React 18, TypeScript, Redux Toolkit, Ant Design | Rapid UI, type safety, enterprise‑grade components |
| **API Gateway** | FastAPI, Python 3.11 | High performance, async, auto‑docs |
| **AI Engine** | LangGraph, Llama‑3‑70B (FT), vLLM | State‑ful agent orchestration, low‑latency inference |
| **Connectors** | Java (SAP RFC), Python SDKs, custom COBOL bridge | Native SDK support, secure communication |
| **Database** | PostgreSQL 15, TimescaleDB, Redis | ACID, time‑series, caching |
| **Infra** | Kubernetes 1.30, Helm, ArgoCD | Declarative deployment, CI/CD |
| **CI/CD** | GitHub Actions, Docker Buildx | Automated builds, tests, image push |
| **Monitoring** | Prometheus, Grafana, Loki | Observability, alerting |
| **Security** | OAuth2, JWT, TLS 1.3, HSM | Compliance, data protection |

---

## 6. Deployment

### 6.1 On‑Prem Connector Deployment

* **Container**: Docker image `erp-wright/connector:<tag>`  
* **Runtime**: Kubernetes pod with host‑network mode for legacy DB access  
* **Secrets**: Stored in Vault, mounted as env vars  
* **Health**: Liveness/Readiness probes against `/api/connectors/{id}/health`

### 6.2 Cloud Core Deployment

* **Cluster**: EKS (AWS) or GKE (GCP) – 3‑node control plane, 2‑node worker pool (auto‑scaling)  
* **Namespace**: `erp-wright-core`  
* **Services**:  
  * `api-gateway` – LoadBalancer  
  * `ai-engine` – StatefulSet (Celery workers)  
  * `postgres` – StatefulSet (Patroni)  
  * `timescaledb` – StatefulSet  
  * `redis` – StatefulSet  
* **Ingress**: Nginx Ingress Controller with TLS termination (cert‑manager)  
* **Secrets**: Kubernetes Secrets + external Vault integration  
* **Observability**: Prometheus Operator, Grafana, Loki

### 6.3 CI/CD Pipeline

1. **Commit** → GitHub Actions  
2. **Lint** (flake8, eslint)  
3. **Unit Tests** (pytest, jest)  
4. **Build Docker** (frontend, backend, connector)  
5. **Push** to GitHub Container Registry  
6. **Helm Chart** update → ArgoCD sync  
7. **Smoke Test** (API health, connector health)  
8. **Deploy** to staging → production

---

## 7. Security & Compliance

| Requirement | Implementation |
|-------------|----------------|
| Data encryption at rest | PostgreSQL pgcrypto, TimescaleDB encryption, EBS snapshots |
| Data encryption in transit | TLS 1.3 everywhere, mutual TLS for connector‑core communication |
| Access control | Role‑based JWT scopes, audit logs |
| Compliance | ISO 27001 baseline, GDPR for EU customers, local data residency for Thailand/Vietnam |

---

## 8. Roadmap (High‑Level)

| Phase | Milestone | Target |
|-------|-----------|--------|
| 1 | Tech stack lock, repo scaffolding | 2026‑07‑01 |
| 2 | Core AI Engine + LangGraph integration | 2026‑08‑15 |
| 3 | SAP & Oracle connectors | 2026‑09‑30 |
| 4 | Low‑code workflow builder | 2026‑11‑15 |
| 5 | Real‑time dashboard & metrics | 2026‑12‑31 |
| 6 | Beta release to 5 pilot manufacturers | 2027‑02‑28 |
| 7 | Public launch | 2027‑04‑30 |

---

## 9. Dependencies & Licensing

| Dependency | Version | License | Notes |
|------------|---------|---------|-------|
| FastAPI | 0.95.1 | MIT | |
| LangGraph | 0.1.0 | Apache‑2.0 | |
| vLLM | 0.2.0 | Apache‑2.0 | |
| Llama‑3‑70B | 3.1 | Llama‑Meta | |
| PostgreSQL | 15.4 | PostgreSQL | |
| TimescaleDB | 2.10 | PostgreSQL | |
| Redis | 7.2 | BSD | |
| React | 18.2 | MIT | |
| Ant Design | 5.0 | MIT | |
| Helm | 3.12 | Apache‑2.0 | |
| ArgoCD | 2.7 | Apache‑2.0 | |
| Prometheus | 2.47 | Apache‑2.0 | |
| Grafana | 10.0 | AGPL‑3.0 | |
| Loki | 2.8 | AGPL‑3.0 | |

All open‑source components are MIT/Apache‑2.0 licensed; the erp‑wright codebase is MIT licensed.

---

## 10. Appendix

### 10.1 Glossary

* **Workflow Agent** – autonomous LLM‑powered process that interacts with ERP systems.  
* **Connector** – adapter that translates ERP API calls into platform‑native operations.  
* **GraphQL Schema** – auto‑generated schema exposing ERP entities to the UI.  
* **Metric** – KPI such as “orders processed per hour” or “exception rate”.

### 10.2 Contact

* **Engineering Lead** – <lead@axentx.com>  
* **Product Manager** – <pm@axentx.com>  
* **Support** – <support@axentx.com>

---
