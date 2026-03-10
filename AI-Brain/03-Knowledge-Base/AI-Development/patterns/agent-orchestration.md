---
tags: [ai-development, pattern, agent-orchestration]
created: 2026-03-02
updated: 2026-03-02
type: note
status: active
---

# Pattern: Agent Orchestration

Como organizar multiplos agentes de IA para trabalhar de forma complementar.

## Principio

Agentes especializados (menores, focados) sao mais confiaveis que agentes generalistas monoliticos. Cada agente tem forca em um dominio e delega o resto.

## Mapa de Forcas

| Agente | Forca | Limitacao |
|--------|-------|-----------|
| Claude Code | Raciocinio complexo, orquestracao, MCP | Custo por token mais alto |
| Codex | Geracao de codigo rapida, testes | Raciocinio multi-step limitado |
| Gemini | Multimodal, contexto longo, grounding | Menor controle fino de prompts |

## Patterns de Orquestracao

### 1. Router Pattern
Um agente central (Claude) recebe o pedido e delega para o agente mais adequado.

### 2. Pipeline Pattern
Agentes em sequencia: Researcher → Developer → Tester.

### 3. Ensemble Pattern
Multiplos agentes respondem a mesma pergunta. Melhor resposta e selecionada ou combinada.

### 4. Specialist Pattern
Cada agente opera em seu dominio sem coordenacao central. Vault compartilhado como meio de comunicacao.

## Aplicacao no AI-Brain

Este vault usa o **Specialist Pattern** com **vault compartilhado**:
- Cada agente tem suas personas em 01-Personas/[agente]/
- Todos leem/escrevem em 03-Knowledge-Base/ e 04-Memory/
- Brain Index serve como estado compartilhado


## Navegacao

- Orquestrador: [[00-Meta/orchestrator]]
- MCP Guide: [[03-Knowledge-Base/AI-Development/frameworks/mcp-guide]]
- Best Practices: [[03-Knowledge-Base/AI-Development/best-practices/obsidian-ai-integration]]