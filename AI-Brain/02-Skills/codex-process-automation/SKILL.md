---
tags: [skill, codex, automation, process, workflows]
compatible-agents: [codex]
version: "1.0"
created: 2026-03-07
type: skill
status: active
---

# Skill: Codex Process Automation

## Trigger

Usar quando a demanda envolver workflow operacional, automacao de processo, n8n, Uazapi, Edge Functions, cronjobs, handoff entre sistemas, ou transformacao de regra manual em fluxo executavel.

## Procedimento

1. Mapear gatilho, entrada, estado, excecao, dono e SLA do processo.
2. Separar o que pertence a orquestracao visual, job server-side, Edge Function ou script Python.
3. Definir riscos, observabilidade, retry, fallback e acao humana de contingencia.
4. Escolher stack minima para o fluxo:
   - `n8n` para encadeamento visual e integracoes rapidas;
   - `supabase-edge-functions` para automacao server-side, webhooks e cron seguro;
   - `python-expert` para ETL, fila, enriquecimento ou jobs analiticos;
   - `use-uazapi` para WhatsApp e automacao conversacional operacional.
5. Fechar com criterio de monitoramento, ponto de rollback e evidencia de execucao.

## Fontes reais do ecossistema

- `C:/Users/isa_e/.agents/skills/n8n-expert/SKILL.md`
- `C:/Users/isa_e/.agents/skills/use-uazapi/SKILL.md`
- `C:/Users/isa_e/.agents/skills/uazapi-media-sending/SKILL.md`
- `C:/Users/isa_e/.agents/skills/supabase-edge-functions/SKILL.md`
- `C:/Users/isa_e/.agents/skills/python-expert/SKILL.md`
- `C:/Users/isa_e/.agents/skills/data-engineering-expert/SKILL.md`
- `C:/Users/isa_e/.agents/skills/risk-management-specialist/SKILL.md`
- `C:/Users/isa_e/.agents/skills/workflow-orchestration/SKILL.md`

## Personas que consomem

- Automation and Process Specialist
- Business Risk and Compliance Analyst
- Backend and Data Specialist
- Product Owner

## Regra operacional

Automacao boa nao e a que apenas executa. E a que deixa claro quem dispara, quem monitora, quando falha e como volta para um estado seguro.
