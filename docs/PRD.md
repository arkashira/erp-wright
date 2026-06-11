# Product Requirements Document – erp‑wright

---

## 1. Executive Summary  

**erp‑wright** is an AI‑driven automation platform that empowers small‑to‑medium manufacturers (< 500 employees) to modernize legacy ERP systems. By combining intelligent workflow agents, a universal connector layer, and a low‑code visual builder, the product eliminates repetitive data entry, unifies fragmented back‑ends, and delivers measurable ROI in a resource‑constrained environment.

---

## 2. Problem Statement  

1. **Manual, error‑prone processes** – Small manufacturers rely on spreadsheets and manual entry for purchase orders, inventory reconciliation, and production scheduling, consuming 70 % of labor hours that could be automated.  
2. **Legacy ERP fragmentation** – Existing systems (SAP, Oracle EBS, custom COBOL, etc.) are siloed, lack modern APIs, and require costly custom integrations.  
3. **Limited IT expertise** – SMEs lack dedicated developers to build and maintain automation workflows, leading to low adoption of digital tools.  
4. **High cost of digital transformation** – Traditional ERP upgrades or replacements are prohibitively expensive and disruptive for small plants.

---

## 3. Target Users  

| Persona | Role | Pain Points | Desired Outcomes |
|---------|------|-------------|------------------|
| **Plant Manager** | Oversees daily operations | Manual data entry, lack of real‑time visibility | Reduce labor hours, improve accuracy, quick decision‑making |
| **IT Lead / Ops Engineer** | Maintains legacy systems | Complex integration, limited budget | One‑stop connector layer, minimal maintenance |
| **Process Engineer** | Designs workflows | No code required, need rapid prototyping | Drag‑and‑drop builder, instant deployment |
| **Finance Officer** | Tracks cost & ROI | Inconsistent data, slow reporting | Real‑time dashboards, audit‑ready logs |

---

## 4. Goals & Success Metrics  

| Goal | Success Metric | Target |
|------|----------------|--------|
| **Reduce manual labor** | % of repetitive tasks automated | ≥ 70 % reduction in manual PO creation & inventory checks |
| **Improve data accuracy** | Error rate in automated transactions | < 0.5 % |
| **Accelerate integration** | Time to connect a new ERP | ≤ 2 weeks (including schema mapping) |
| **Enable low‑code adoption** | Number of workflows created by non‑developers | ≥ 10 per month per plant |
| **Deliver measurable ROI** | Cost savings vs. baseline | 30 % reduction in operational costs within 6 months |
| **Maintain compliance** | Audit trail completeness | 100 % of transactions logged with timestamps |

---

## 5. Key Features (Prioritized)

| Priority | Feature | Description | Dependencies |
|----------|---------|-------------|--------------|
| **P1** | **AI Workflow Agents** | Trained agents that detect and execute repetitive tasks (PO creation, inventory reconciliation, exception handling). | LangGraph, Llama3‑70B fine‑tuned on manufacturing data |
| **P2** | **Universal ERP Connectors** | Pre‑built adapters for SAP, Oracle EBS, Infor, and custom databases with auto‑schema mapping and secure, no‑downtime connectivity. | Connector SDK, secure gateway |
| **P3** | **Visual Workflow Builder** | Drag‑and‑drop interface for building, testing, and deploying automations without code. | React 18, TypeScript, state‑management library |
| **P4** | **Real‑Time Ops Dashboard** | Live monitoring of automated processes, exception alerts, and ROI metrics. | PostgreSQL 15, Grafana or custom dashboard |
| **P5** | **Hybrid Deployment** | Core AI engine in the cloud, connectors on‑prem for data compliance. | Kubernetes, Helm, ArgoCD |
| **P6** | **Audit & Compliance Module** | Immutable logs, role‑based access, exportable reports. | PostgreSQL, audit‑logging library |
| **P7** | **Marketplace of Pre‑built Workflows** | Curated library of industry‑specific automations. | Community contribution platform |
| **P8** | **Multi‑language Support** | Thai, Vietnamese, English UI and documentation. | Localization framework |

---

## 6. Scope

| Item | In‑Scope | Out‑of‑Scope |
|------|----------|--------------|
| **Core AI Engine** | Development of LangGraph agents, fine‑tuning Llama3‑70B | Deployment of other LLMs |
| **Connector Layer** | SAP, Oracle EBS, Infor, custom DB adapters | Integration with non‑ERP systems (CRM, MES) |
| **UI/UX** | Visual builder, dashboard, admin console | Native mobile app (will be considered for future release) |
| **Deployment** | Hybrid on‑prem + cloud | Dedicated on‑prem only (full stack) |
| **Compliance** | GDPR, local data residency | Industry‑specific certifications (ISO 27001) – to be added later |
| **Marketplace** | Initial set of 5 pre‑built workflows | Continuous marketplace expansion (post‑MVP) |

---

## 7. Deliverables & Timeline (MVP)

| Milestone | Deliverable | Date |
|-----------|-------------|------|
| **1. Architecture & Tech Stack Lock** | Finalized tech stack doc, architecture diagram | 2026‑07‑15 |
| **2. Core AI Engine** | Trained agents, LangGraph integration | 2026‑08‑31 |
| **3. Universal Connectors** | SAP & Oracle adapters, schema mapper | 2026‑09‑30 |
| **4. Visual Builder Prototype** | Drag‑and‑drop UI, workflow editor | 2026‑10‑31 |
| **5. Ops Dashboard** | Real‑time monitoring, exception alerts | 2026‑11‑15 |
| **6. Hybrid Deployment** | Cloud + on‑prem installation guide | 2026‑11‑30 |
| **7. MVP Release** | Full product with 3 pre‑built workflows | 2026‑12‑15 |
| **8. Post‑MVP Review** | Success metrics, user feedback | 2027‑01‑15 |

---

## 8. Risks & Mitigations  

| Risk | Impact | Mitigation |
|------|--------|------------|
| **Data privacy in on‑prem connectors** | Non‑compliance | Use secure, isolated containers; provide encryption at rest |
| **LLM hallucination in agents** | Transaction errors | Implement human‑in‑the‑loop review, confidence thresholds |
| **Legacy ERP API instability** | Integration delays | Use adapter SDK with retry/back‑off, fallback to batch jobs |
| **Adoption barrier for low‑code UI** | Low workflow creation | Offer guided tutorials, pre‑built templates, in‑app help |
| **Resource constraints (SME IT)** | Limited support | Provide managed service option, extensive documentation |

---

## 9. Dependencies on Axentx Portfolio  

- **AI Automation** – Builds on existing AI‑driven workflow orchestration expertise.  
- **Legacy Integration** – Leverages prior connector research from the “product‑synth” services.  
- **Data Validation** – Uses Axentx’s validation pipeline to ensure real‑world pain points are addressed before shipping.

---

## 10. Acceptance Criteria  

1. **Automated PO creation** – 70 % reduction in manual entry time, verified by time‑study.  
2. **Connector success** – 95 % of test cases pass schema mapping for SAP & Oracle.  
3. **Workflow builder** – Users can create and deploy a new automation in < 30 minutes.  
4. **Dashboard** – Real‑time metrics update within 5 seconds; exception alerts trigger email.  
5. **Compliance** – Audit logs are tamper‑evident and exportable in CSV/JSON.  

---

## 11. Future Enhancements (Post‑MVP)

- Expand connector library to include MES, CRM, and IoT gateways.  
- Introduce a marketplace for community‑shared workflows.  
- Add native mobile app for on‑the‑go monitoring.  
- Implement multi‑tenant SaaS offering with strict data isolation.  

---
