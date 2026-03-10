---
tags: [skill, codex, orchestration]
compatible-agents: [codex]
version: "2.0"
created: 2026-03-07
type: skill
status: active
---

# Skill: Codex Orchestration

## Trigger

Usar como skill unica de entrada quando a demanda precisar ser roteada entre produto, compliance, automacao de processos, dados, frontend, runtime do Codex ou especialistas atuando em paralelo.

## Procedimento

1. Ler o contexto global em `AGENTS.md` e a registry em [[00-Meta/codex-routing-registry|Codex Routing Registry]].
2. Classificar a demanda em uma trilha principal:
   - `platform-runtime`
   - `product-delivery`
   - `governance-legal`
   - `finance-procurement`
   - `process-automation`
   - `data-intelligence`
   - `experience-design`
3. Escolher a persona lider e os handoffs secundarios.
4. Ativar apenas os clusters de skill necessarios para a demanda.
5. Decidir entre:
   - `solo`: um especialista resolve a maior parte da tarefa
   - `parallel`: multiplos especialistas com ownership separado
6. Encerrar em verificacao objetiva e registrar a rota usada.

## Matriz de rota minima

| Demanda | Persona lider | Skills principais | Apoio paralelo |
|---|---|---|---|
| Backlog, discovery, criterio de aceite | Product Owner | Codex Product Delivery | Scrum Master, Frontend Design |
| LGPD, politica, contrato, parecer | DPO | Codex Governance Legal | Legal Analyst, Business Risk and Compliance |
| Processo, risco, evidencia, compliance | Business Risk and Compliance Analyst | Codex Governance Legal, Codex Public Procurement | DPO, Financial and Accounting |
| Workflow, n8n, Uazapi, cronjob, automacao de processo | Automation and Process Specialist | Codex Process Automation | Business Risk and Compliance, Backend and Data, Python Specialist |
| Financeiro, contabil, margem, impacto | Financial and Accounting Specialist | Codex Finance Accounting | Data Analyst |
| Edital, matriz de risco, proposta tecnica | Public Procurement Specialist | Codex Public Procurement | Legal Analyst, Financial and Accounting |
| EDA, ML, clustering, regressao | Data Scientist | Codex Data Science Python | Python Specialist, Data Analyst |
| BI, KPI, dashboard, storytelling | Data Analyst | Codex Analytics BI | Product Owner, Frontend Design |
| UI, UX, Figma, handoff visual | Frontend Design Specialist | Codex Frontend Design | Frontend Specialist, QA and Performance |
| Runtime Codex, MCPs, auth, wrappers | Codex MCP Ops | Codex MCP Ops | Researcher, Deploy and Security |

## Fontes reais do Codex

- `C:/Israel/Projetos Trae/FinManage/AGENTS.md`
- `C:/Users/isa_e/.claude/skills/AI-Brain/00-Meta/codex-routing-registry.md`
- `C:/Users/isa_e/.codex/teams/codex-team.json`
- `C:/Israel/Projetos Trae/FinManage/.codex/teams/codex-team.json`
- `C:/Israel/Projetos Trae/FinManage/.codex/skills/codex-brain-orchestrator/SKILL.md`
- `C:/Israel/Projetos Trae/FinManage/.codex/skills/finmanage-squad-orchestrator/SKILL.md`
- `C:/Users/isa_e/.agents/skills/planning-with-files/SKILL.md`

## Regra operacional

Esta skill nao implementa tudo. Ela classifica, roteia, limita o escopo e aciona as outras skills e personas certas.
