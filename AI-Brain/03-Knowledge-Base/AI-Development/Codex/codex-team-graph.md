---
tags: [graph, codex, personas]
created: 2026-03-07
updated: 2026-03-07
type: map
status: active
---

# Codex Team Graph

## Entrypoint

- Hub canonico do vault: [[00-Meta/orchestrator|Orquestrador AI-Brain]]
- Registry de rota Codex: [[00-Meta/codex-routing-registry|Codex Routing Registry]]
- Skill de entrada do runtime Codex: [[02-Skills/codex-orchestration/SKILL|Codex Orchestration]]
- Runtime local: `C:/Israel/Projetos Trae/FinManage/.codex/skills/codex-brain-orchestrator/SKILL.md`

```mermaid
flowchart LR
  O["Codex Orchestrator"] --> R["Researcher"]
  O --> M["MCP Ops"]
  O --> A["Automation and Process"]
  O --> F["Frontend"]
  O --> B["Backend and Data"]
  O --> Q["QA and Performance"]
  O --> D["Deploy and Security"]
  R --> C["Local CUA Operator"]
  M --> C
  A --> B
  A --> D
  F --> Q
  B --> Q
  D --> C
  M --> D
  R --> B
```

## Grafo expandido de negocio e especialistas

```mermaid
flowchart LR
  O["Codex Orchestrator"] --> PO["Product Owner"]
  O --> DPO["DPO"]
  O --> DS["Data Scientist"]
  O --> FD["Frontend Design"]
  O --> FA["Financial and Accounting"]
  O --> BRC["Business Risk and Compliance"]
  O --> AP["Automation and Process"]

  PO --> SM["Scrum Master"]
  PO --> FD
  PO --> BRC
  PO --> DA["Data Analyst"]

  DPO --> LA["Legal Analyst"]
  LA --> LL["Legal Labor"]
  LA --> LG["Legal Legislative"]
  LA --> LC["Legal Contracts"]

  BRC --> DPO
  BRC --> LA
  BRC --> PP["Public Procurement"]
  BRC --> FA
  BRC --> AP

  PP --> LG
  PP --> FA

  AP --> B["Backend and Data"]
  AP --> PY["Python Specialist"]
  AP --> D["Deploy and Security"]
  AP --> DPO

  DS --> PY["Python Specialist"]
  DS --> DA
  DS --> B["Backend and Data"]

  FD --> F["Frontend"]
  FD --> Q["QA and Performance"]
  FD --> DPO

  FA --> DA
  FA --> B
```

## Navegacao

- [[01-Personas/codex/persona-orchestrator-finmanage|Codex Orchestrator]]
- [[01-Personas/codex/persona-researcher|Codex Researcher]]
- [[01-Personas/codex/persona-mcp-ops-specialist|Codex MCP Ops]]
- [[01-Personas/codex/persona-frontend-specialist|Codex Frontend Specialist]]
- [[01-Personas/codex/persona-backend-data-specialist|Codex Backend and Data Specialist]]
- [[01-Personas/codex/persona-qa-performance-specialist|Codex QA and Performance Specialist]]
- [[01-Personas/codex/persona-deploy-security-specialist|Codex Deploy and Security Specialist]]
- [[01-Personas/codex/persona-local-cua-operator|Codex Local CUA Operator]]
- [[01-Personas/codex/persona-product-owner|Product Owner]]
- [[01-Personas/codex/persona-scrum-master|Scrum Master]]
- [[01-Personas/codex/persona-dpo|DPO]]
- [[01-Personas/codex/persona-legal-analyst|Legal Analyst]]
- [[01-Personas/codex/persona-legal-labor-specialist|Legal Labor Specialist]]
- [[01-Personas/codex/persona-legal-legislative-specialist|Legal Legislative Specialist]]
- [[01-Personas/codex/persona-legal-contracts-specialist|Legal Contracts Specialist]]
- [[01-Personas/codex/persona-business-risk-compliance-analyst|Business Risk and Compliance Analyst]]
- [[01-Personas/codex/persona-financial-accounting-specialist|Financial and Accounting Specialist]]
- [[01-Personas/codex/persona-public-procurement-specialist|Public Procurement Specialist]]
- [[01-Personas/codex/persona-automation-process-specialist|Automation and Process Specialist]]
- [[01-Personas/codex/persona-data-scientist|Data Scientist]]
- [[01-Personas/codex/persona-python-specialist|Python Specialist]]
- [[01-Personas/codex/persona-data-analyst|Data Analyst]]
- [[01-Personas/codex/persona-frontend-design-specialist|Frontend Design Specialist]]

## Times de trabalho

- Plataforma e runtime: Orchestrator, MCP Ops, Deploy Security, Local CUA
- Product and delivery: Product Owner, Scrum Master
- Governance and legal: DPO, Legal Analyst, Legal Labor, Legal Legislative, Legal Contracts, Business Risk and Compliance
- Finance and public sector: Financial and Accounting, Public Procurement
- Automation and process: Automation and Process
- Data and intelligence: Data Scientist, Python Specialist, Data Analyst
- Experience and design: Frontend Design, Frontend Specialist, QA and Performance

## Regra do grafo

Cada nova persona deve declarar:

- qual demanda ela resolve;
- quais clusters de skill ela usa;
- para quais outras personas ela delega.
