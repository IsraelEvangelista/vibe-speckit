---
tags: [meta, orchestrator, core, graph-root]
aliases: [Codex Brain Index, Codex Brain]
created: 2026-03-02
updated: 2026-03-07
type: orchestrator
status: active
version: "1.1"
description: Ponto de entrada canonico para qualquer agente AI. Navega o grafo de personas, skills e submapas por referencia semantica.
---

# Orquestrador AI-Brain

> [!important] Ponto de Entrada
> Todo agente AI deve iniciar aqui. Este arquivo e o no raiz canonico do grafo de conhecimento.
> Leia este arquivo -> identifique a persona -> siga os links para as skills necessarias.

## Como Funciona

```text
PEDIDO DO USUARIO
       |
       v
  [Orquestrador] <- voce esta aqui
       |
       +-- Identifica DOMINIO do pedido
       |
       +-- Seleciona PERSONA adequada
       |        |
       |        +-- Persona carrega suas SKILLS
       |                    |
       |                    +-- Skills podem ENCADEAR outras skills
       |
       +-- Executa seguindo PREVC
```

## Trilha Codex CLI/Desktop

> [!tip] Dominio especializado
> A malha Codex entra por este orquestrador raiz. Os artefatos Codex abaixo sao submapas especializados, nao um segundo hub central.

- Registry de roteamento Codex: [[00-Meta/codex-routing-registry|Codex Routing Registry]]
- Grafo do time Codex: [[03-Knowledge-Base/AI-Development/Codex/codex-team-graph|Codex Team Graph]]
- Times corporativos Codex: [[03-Knowledge-Base/AI-Development/Codex/codex-enterprise-teams|Codex Enterprise Teams]]
- Catalogo de skills Codex: [[03-Knowledge-Base/AI-Development/Codex/codex-skill-catalog|Codex Skill Catalog]]

### Personas nucleares do Codex

- [[01-Personas/codex/persona-orchestrator-finmanage|Codex Orchestrator]]
- [[01-Personas/codex/persona-mcp-ops-specialist|Codex MCP Ops]]
- [[01-Personas/codex/persona-researcher|Codex Researcher]]
- [[01-Personas/codex/persona-frontend-specialist|Codex Frontend Specialist]]
- [[01-Personas/codex/persona-backend-data-specialist|Codex Backend and Data Specialist]]
- [[01-Personas/codex/persona-qa-performance-specialist|Codex QA and Performance Specialist]]
- [[01-Personas/codex/persona-deploy-security-specialist|Codex Deploy and Security Specialist]]
- [[01-Personas/codex/persona-local-cua-operator|Codex Local CUA Operator]]

## Mapa de Dominios -> Personas

### Ciencia de Dados e Analise
> Pedidos sobre dados, datasets, metricas, graficos, SQL, ML, EDA

| Agente | Persona | Forca |
|--------|---------|-------|
| Claude Code | [[01-Personas/claude-code/persona-data-analyst]] | Raciocinio analitico profundo |
| Codex | [[01-Personas/codex/persona-data-analyst]] | Geracao rapida de scripts Python |
| Gemini | [[01-Personas/gemini/persona-data-analyst]] | Analise multimodal e contexto longo |

### AI Development
> Pedidos sobre agentes, prompts, MCP, APIs de LLM, RAG, deployment

| Agente | Persona | Forca |
|--------|---------|-------|
| Claude Code | [[01-Personas/claude-code/persona-ai-developer]] | Arquitetura e orquestracao complexa |
| Codex | [[01-Personas/codex/persona-ai-developer]] | Codigo robusto com testes |
| Gemini | [[01-Personas/gemini/persona-ai-developer]] | Ecossistema Google e multimodal |

### Pesquisa e Investigacao
> Pedidos sobre pesquisar, comparar, avaliar, fact-check, revisar

| Agente | Persona | Forca |
|--------|---------|-------|
| Claude Code | [[01-Personas/claude-code/persona-researcher]] | Raciocinio critico e sintese |
| Codex | [[01-Personas/codex/persona-researcher]] | Scripts de benchmark e scraping |
| Gemini | [[01-Personas/gemini/persona-researcher]] | Google grounding e docs longos |

### Criacao de Documentos
> Pedidos sobre relatorios, apresentacoes, PDFs, Word, planilhas

Persona: usar a persona do dominio relevante + skill de documento:
- [[02-Skills/document-creation/SKILL]]
- [[02-Skills/spreadsheet/SKILL]]
- [[02-Skills/presentation/SKILL]]
- [[02-Skills/pdf-processing/SKILL]]

## Mapa de Skills (Grafo Completo)

### Skills Core (encadeiaveis)

```text
[[02-Skills/data-analysis/SKILL]]
    +-- chama -> [[02-Skills/data-cleaning/SKILL]]
    +-- chama -> [[02-Skills/visualization/SKILL]]
    +-- chama -> [[02-Skills/statistical-testing/SKILL]]

[[02-Skills/code-generation/SKILL]]
    +-- chama -> [[02-Skills/testing/SKILL]]
    +-- chama -> [[02-Skills/code-review/SKILL]]

[[02-Skills/research/SKILL]]
    +-- chama -> [[02-Skills/fact-checking/SKILL]]
    +-- chama -> [[02-Skills/synthesis/SKILL]]

[[02-Skills/prompt-engineering/SKILL]]
    +-- chama -> [[02-Skills/prompt-testing/SKILL]]
```

### Skills de Documento (espelho do Cowork)

```text
[[02-Skills/document-creation/SKILL]]    <- espelho docx
[[02-Skills/spreadsheet/SKILL]]          <- espelho xlsx
[[02-Skills/presentation/SKILL]]         <- espelho pptx
[[02-Skills/pdf-processing/SKILL]]       <- espelho pdf
```

### Skills Complementares

```text
[[02-Skills/data-cleaning/SKILL]]        <- nova
[[02-Skills/visualization/SKILL]]        <- nova
[[02-Skills/statistical-testing/SKILL]]  <- nova
[[02-Skills/testing/SKILL]]              <- nova
[[02-Skills/code-review/SKILL]]          <- nova
[[02-Skills/fact-checking/SKILL]]        <- nova
[[02-Skills/synthesis/SKILL]]            <- nova
[[02-Skills/prompt-testing/SKILL]]       <- nova
[[02-Skills/knowledge-consolidation/SKILL]] <- nova
```

## Regras de Orquestracao

1. **Identificar dominio** do pedido usando as palavras-chave em cada secao acima.
2. **Selecionar agente** baseado na forca listada, ou usar o agente que esta executando.
3. **Carregar persona** seguindo o wikilink; a persona lista suas skills.
4. **Executar skill** seguindo o wikilink da persona; a skill tem procedimento completo.
5. **Encadear** se a skill referenciar outra skill como dependencia.
6. **Seguir PREVC** em toda execucao.
7. **Atualizar memoria** em [[04-Memory/context-index]] se houve aprendizado.

## Regras de Seguranca

- Ver [[01-Personas/shared/security-policies]] - SEMPRE se aplica.
- Ver [[01-Personas/shared/common-rules]] - SEMPRE se aplica.

## Referencias do Vault

- Setup: [[00-Meta/SETUP-GUIDE]]
- Grafo: [[00-Meta/graph-map]]
- MCP Schema: [[00-Meta/Schemas/mcp-server-schema]]
- Convencoes: [[00-Meta/Conventions]]
- Contexto Agentes: [[AGENTS]]
- Analise 2026: [[03-Knowledge-Base/AI-Development/best-practices/analise-obsidian-local-2026]]

## Estado Atual

- Consultar [[04-Memory/context-index]] para projetos ativos e decisoes recentes.
- Consultar pasta `05-Daily-Operations/daily-notes/` para contexto do dia.
