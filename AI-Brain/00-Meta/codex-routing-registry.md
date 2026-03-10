---
tags: [codex, routing, registry, orchestration]
created: 2026-03-07
updated: 2026-03-07
type: reference
status: active
---

# Codex Routing Registry

> [!tip] Submapa de rota
> O ponto de entrada canonico do vault continua sendo [[00-Meta/orchestrator|Orquestrador AI-Brain]]. Esta registry existe apenas como mapa operacional da trilha Codex.

## Modelo operacional

- Fonte de verdade organizacional: vault `AI-Brain`
- Fonte de verdade de runtime local: `C:/Israel/Projetos Trae/FinManage/.codex`
- Padrao de execucao: hub-and-spoke
- Hub raiz do vault: [[00-Meta/orchestrator|Orquestrador AI-Brain]]
- Lider padrao: [[01-Personas/codex/persona-orchestrator-finmanage|Codex Orchestrator]]
- Paralelismo: somente quando houver ownership separado ou frentes independentes

## Ordem de roteamento

1. Classificar a demanda em uma trilha principal: runtime, product-delivery, governance-legal, finance-procurement, process-automation, data-intelligence, experience-design.
2. Escolher a persona lider da trilha.
3. Ativar os clusters de skill minimos para a demanda.
4. Decidir `solo` ou `parallel`.
5. Encerrar em verificacao objetiva e registro de decisao.

## Registro de personas

| Persona | Trilha | Skills principais | MCPs preferenciais | Delega para | Parallel safe |
|---|---|---|---|---|---|
| Codex Orchestrator | platform-runtime | Codex Orchestration, Codex Product Delivery, Codex Research | `ai-context`, `context7`, `perplexity`, `sequential-thinking`, `linear` | Product Owner, DPO, Data Scientist, Frontend Design, MCP Ops, Automation and Process | no |
| Product Owner | product-delivery | Codex Product Delivery, Codex Analytics BI, Codex Frontend Design | `ai-context`, `linear`, `context7` | Scrum Master, Frontend Design, Backend and Data, Data Analyst | yes |
| Scrum Master | product-delivery | Codex Product Delivery | `ai-context`, `linear` | Product Owner, QA and Performance | yes |
| DPO | governance-legal | Codex Governance Legal, Codex Product Delivery | `ai-context`, `linear`, `perplexity`, `sequential-thinking` | Legal Analyst, Business Risk and Compliance | yes |
| Legal Analyst | governance-legal | Codex Governance Legal | `ai-context`, `perplexity`, `linear`, `sequential-thinking` | Legal Labor, Legal Legislative, Legal Contracts | yes |
| Business Risk and Compliance Analyst | governance-legal | Codex Governance Legal, Codex Product Delivery, Codex Public Procurement | `ai-context`, `linear`, `sequential-thinking` | DPO, Legal Analyst, Financial and Accounting, Public Procurement, Automation and Process | yes |
| Financial and Accounting Specialist | finance-procurement | Codex Finance Accounting, Codex Analytics BI | `linear`, `perplexity`, `supabase-mcp-server` | Data Analyst, Public Procurement | yes |
| Public Procurement Specialist | finance-procurement | Codex Public Procurement, Codex Governance Legal, Codex Finance Accounting | `ai-context`, `linear`, `perplexity`, `sequential-thinking` | Legal Legislative, Financial and Accounting | yes |
| Automation and Process Specialist | process-automation | Codex Process Automation, Codex Backend Data, Codex Product Delivery | `ai-context`, `linear`, `supabase-mcp-server`, `sequential-thinking`, `context7` | Business Risk and Compliance Analyst, Backend and Data, Python Specialist, Deploy and Security | yes |
| Data Scientist | data-intelligence | Codex Data Science Python, Codex Analytics BI | `context7`, `supabase-mcp-server`, `sequential-thinking` | Python Specialist, Data Analyst, Backend and Data | yes |
| Python Specialist | data-intelligence | Codex Data Science Python, Codex Backend Data | `context7`, `supabase-mcp-server` | Data Scientist, Backend and Data | yes |
| Data Analyst | data-intelligence | Codex Analytics BI, Codex Data Science Python | `linear`, `supabase-mcp-server` | Product Owner, Financial and Accounting, Frontend Design | yes |
| Frontend Design Specialist | experience-design | Codex Frontend Design, Codex Frontend UI, Codex Analytics BI | `figma`, `chrome-devtools`, `linear` | Frontend Specialist, QA and Performance, DPO | yes |

## Presets de teams

### Feature Delivery

- Lead: Codex Orchestrator
- Core: Product Owner, Frontend Design Specialist, Backend and Data Specialist, QA and Performance Specialist
- Opcional: Deploy and Security Specialist

### Compliance Delivery

- Lead: DPO
- Core: Legal Analyst, Business Risk and Compliance Analyst, Product Owner
- Opcional: Backend and Data Specialist, Frontend Design Specialist

### Data Initiative

- Lead: Data Scientist
- Core: Python Specialist, Data Analyst, Backend and Data Specialist
- Opcional: Financial and Accounting Specialist

### Procurement Bid

- Lead: Public Procurement Specialist
- Core: Legal Analyst, Business Risk and Compliance Analyst, Financial and Accounting Specialist
- Opcional: Product Owner

### Automation Flows

- Lead: Automation and Process Specialist
- Core: Business Risk and Compliance Analyst, Backend and Data Specialist, Python Specialist
- Opcional: Deploy and Security Specialist, Product Owner

### Codex Ops

- Lead: Codex Orchestrator
- Core: Codex MCP Ops, Researcher, Deploy and Security Specialist
- Opcional: Local CUA Operator

## Export para runtime

- Skill de entrada runtime: `C:/Israel/Projetos Trae/FinManage/.codex/skills/codex-brain-orchestrator/SKILL.md`
- Team runtime: `C:/Israel/Projetos Trae/FinManage/.codex/teams/codex-team.json`
- Contexto global: `C:/Israel/Projetos Trae/FinManage/AGENTS.md`

## Regra de consistencia

Sempre que uma nova persona ou skill entrar nesta malha:

- apontar para pelo menos um cluster de skill;
- declarar MCPs preferenciais;
- declarar para quem delega e de quem recebe handoff;
- indicar se pode trabalhar em paralelo sem compartilhar ownership.
