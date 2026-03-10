---
tags: [map, codex, teams, personas]
created: 2026-03-07
updated: 2026-03-07
type: map
status: active
---

# Codex Enterprise Teams

## Objetivo

Agrupar as personas do Codex por cadeia de trabalho, combinando MCPs reais, clusters de skill e relacoes de chamada.

## Entrypoint e governanca

- Entrypoint unico: [[02-Skills/codex-orchestration/SKILL|Codex Orchestration]]
- Registry de rota: [[00-Meta/codex-routing-registry|Codex Routing Registry]]
- Runtime local do projeto: `C:/Israel/Projetos Trae/FinManage/.codex/skills/codex-brain-orchestrator/SKILL.md`

## Matriz de times

| Time | Personas centrais | MCPs mais usados | Clusters principais |
|---|---|---|---|
| Plataforma e Runtime | Codex Orchestrator, Codex MCP Ops, Deploy and Security, Local CUA | `ai-context`, `context7`, `chrome-devtools`, `perplexity`, `linear` | Codex Orchestration, Codex MCP Ops, Codex Deploy Security |
| Product and Delivery | Product Owner, Scrum Master | `ai-context`, `linear`, `perplexity`, `context7` | Codex Product Delivery, Codex Analytics BI |
| Governance and Legal | DPO, Legal Analyst, Legal Labor, Legal Legislative, Legal Contracts, Business Risk and Compliance | `ai-context`, `perplexity`, `linear`, `sequential-thinking` | Codex Governance Legal, Codex Product Delivery |
| Finance and Public Sector | Financial and Accounting, Public Procurement | `ai-context`, `linear`, `perplexity`, `supabase-mcp-server` | Codex Finance Accounting, Codex Public Procurement |
| Automation and Process | Automation and Process, Backend and Data, Business Risk and Compliance | `ai-context`, `linear`, `supabase-mcp-server`, `context7`, `sequential-thinking` | Codex Process Automation, Codex Backend Data, Codex Product Delivery |
| Data and Intelligence | Data Scientist, Python Specialist, Data Analyst | `ai-context`, `context7`, `supabase-mcp-server`, `sequential-thinking` | Codex Data Science Python, Codex Analytics BI |
| Experience and Design | Frontend Design, Frontend Specialist, QA and Performance | `figma`, `chrome-devtools`, `linear`, `TestSprite` | Codex Frontend Design, Codex Frontend UI, Codex QA Debug |

## Handoffs dominantes

- Product Owner -> Frontend Design / Backend and Data / Data Analyst
- DPO -> Legal Analyst -> subpersonas juridicas
- Business Risk and Compliance -> Public Procurement / Financial and Accounting / DPO
- Business Risk and Compliance -> Automation and Process -> Backend and Data / Python / Deploy and Security
- Data Scientist -> Python Specialist -> Backend and Data
- Frontend Design -> Frontend Specialist -> QA and Performance

## Presets operacionais para Codex Teams

| Preset | Lead | Membros principais | Uso |
|---|---|---|---|
| `feature-delivery` | Codex Orchestrator | Product Owner, Frontend Design, Backend and Data, QA and Performance | features completas com discovery, execucao e validacao |
| `compliance-delivery` | DPO | Legal Analyst, Business Risk and Compliance, Product Owner | fluxos com LGPD, politicas, contrato e impacto regulatorio |
| `data-initiative` | Data Scientist | Python Specialist, Data Analyst, Backend and Data | EDA, modelagem, pipeline e produto de dados |
| `procurement-bid` | Public Procurement Specialist | Legal Analyst, Business Risk and Compliance, Financial and Accounting | edital, proposta tecnica, matriz de risco e requisitos documentais |
| `automation-flows` | Automation and Process Specialist | Business Risk and Compliance, Backend and Data, Python Specialist, Deploy and Security | workflow operacional, n8n, Uazapi, Edge Functions e cronjobs |
| `codex-ops` | Codex Orchestrator | MCP Ops, Researcher, Deploy and Security, Local CUA | runtime local, MCPs, auth, wrappers e adocao de CUA |

## Expansoes sugeridas

- criar persona fiscal-tributaria se o volume de regra fiscal crescer;
- criar persona pricing-and-revenue se precificacao ganhar trilha propria;
- criar persona public-policy-monitor se houver forte carga de mudanca regulatoria.
