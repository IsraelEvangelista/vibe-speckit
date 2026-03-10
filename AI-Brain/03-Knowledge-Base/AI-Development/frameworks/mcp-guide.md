---
tags: [ai-development, mcp, framework]
created: 2026-03-02
updated: 2026-03-02
type: note
status: active
---

# MCP (Model Context Protocol)

Protocolo aberto da Anthropic para conectar agentes de IA a fontes de dados e ferramentas externas.

## Conceitos Fundamentais

### Arquitetura
- **Host**: Aplicacao que hospeda o agente (Claude Desktop, Claude Code, IDE)
- **Client**: Componente dentro do host que gerencia conexoes MCP
- **Server**: Servico que expoe recursos (tools, resources, prompts) via MCP

### Componentes
- **Tools**: Funcoes que o agente pode chamar (ex: buscar notas, executar SQL)
- **Resources**: Dados que o agente pode acessar (ex: arquivos, databases)
- **Prompts**: Templates de prompt pre-definidos

## Servers para Obsidian

| Server | Metodo | Requisitos |
|--------|--------|-----------|
| mcp-obsidian | REST API | Plugin Local REST API no Obsidian |
| obsidian-claude-code-mcp | WebSocket | Plugin Claude Code MCP no Obsidian |
| obsidian-mcp-tools | Filesystem | Acesso direto ao vault |
| obsidian-semantic-mcp | REST + Embeddings | Smart Connections plugin |

## Configuracao Claude Code

Adicionar ao ~/.claude/settings.json:
- Ver [[02-Skills/code-generation/SKILL]] para implementacao
- Referencia completa na documentacao Anthropic

## Links Uteis

- Documentacao oficial: https://modelcontextprotocol.io
- Repositorio de servers: https://github.com/modelcontextprotocol/servers
- SDK TypeScript: https://github.com/modelcontextprotocol/typescript-sdk
- SDK Python: https://github.com/modelcontextprotocol/python-sdk


## Navegacao

- Orquestrador: [[00-Meta/orchestrator]]
- MCP Schema: [[00-Meta/Schemas/mcp-server-schema]]
- Agent Orchestration: [[03-Knowledge-Base/AI-Development/patterns/agent-orchestration]]