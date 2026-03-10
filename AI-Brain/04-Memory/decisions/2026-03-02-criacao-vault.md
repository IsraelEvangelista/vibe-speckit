---
tags: [memory, decision, vault, architecture]
created: 2026-03-02
type: decision
status: active
---

# Decisao: Criacao do Vault AI-Brain

**Data**: 2026-03-02
**Decisor**: Israel Evangelista
**Contexto**: Necessidade de uma base de conhecimento modular e persistente para multiplos agentes de IA.

## Problema

Agentes de IA (Claude Code, Codex, Gemini) operam em silos sem memoria compartilhada ou conhecimento persistente entre sessoes. Cada conversa comeca do zero.

## Opcoes Avaliadas

1. **Notion + API** — Lock-in proprietario, API limitada para agentes
2. **Google Docs** — Integracao complexa, formato nao otimizado para agentes
3. **Obsidian (vault local)** — Arquivos .md locais, zero vendor lock-in, qualquer agente le/escreve
4. **Custom database** — Complexidade desnecessaria para o caso de uso

## Decisao

**Obsidian** — Vault local com arquivos Markdown, integrado via filesystem direto e MCP.

## Rationale

- Zero vendor lock-in: tudo sao arquivos .md em pastas
- Qualquer agente com acesso a filesystem pode ler/escrever
- MCP disponivel para integracao mais rica
- Obsidian fornece interface visual com graph view, search, e plugins
- Comunidade ativa com plugins para AI (Smart Connections, Local REST API, etc.)
- Custo: gratuito (Obsidian Sync opcional)

## Estrutura Escolhida

Vault com 7 pastas numeradas (00-06), personas por agente, skills modulares, memoria em camadas (Brain Index + per-persona + decisoes + padroes).

## Riscos Identificados

- Sincronizacao entre maquinas requer solucao (Git, Obsidian Sync, ou cloud drive)
- Agentes podem criar notas duplicadas se nao houver convencao clara
- MCP depende do Obsidian estar aberto (para servers que usam REST API)

## Proximos Passos

1. Instalar Obsidian e abrir o vault
2. Configurar MCP para Claude Code
3. Testar fluxo completo: consultar persona → executar tarefa → atualizar memoria


## Navegacao

- Brain Index: [[04-Memory/context-index]]
- Orquestrador: [[00-Meta/orchestrator]]
- Obsidian Integration: [[03-Knowledge-Base/AI-Development/best-practices/obsidian-ai-integration]]