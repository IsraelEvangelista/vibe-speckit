---
tags: [meta, schema, mcp, architecture]
created: 2026-03-02
updated: 2026-03-02
type: reference
status: active
---

# MCP Server Schema — AI-Brain Vault

> Blueprint para o MCP server unico que expoe o vault para todos os agentes.

## Arquitetura

```
┌──────────────────────────────────────────────┐
│              AI-Brain MCP Server              │
│                                               │
│  Tools (o que o agente pode FAZER):           │
│  ├── navigate_graph()                         │
│  ├── get_orchestrator()                       │
│  ├── get_persona()                            │
│  ├── get_skill()                              │
│  ├── follow_chain()                           │
│  ├── search_semantic()                        │
│  ├── update_memory()                          │
│  └── list_skills_for_persona()                │
│                                               │
│  Resources (o que o agente pode LER):         │
│  ├── vault://orchestrator                     │
│  ├── vault://persona/{agent}/{name}           │
│  ├── vault://skill/{name}                     │
│  ├── vault://memory/{name}                    │
│  ├── vault://knowledge/{path}                 │
│  └── vault://brain-index                      │
│                                               │
│  Prompts (templates pre-definidos):           │
│  ├── start_session                            │
│  ├── activate_persona                         │
│  └── chain_skills                             │
└──────────────────────────────────────────────┘
         │           │            │
         v           v            v
    Claude Code   Codex CLI   Gemini CLI
```

## Tools Detalhados

### navigate_graph(start_node, direction)
Navega o grafo de wikilinks a partir de um no.
- `start_node`: path relativo da nota (ex: "01-Personas/claude-code/persona-data-analyst")
- `direction`: "outgoing" (links que esta nota faz) | "incoming" (notas que linkam para esta) | "both"
- Return: lista de nos conectados com tipo (persona, skill, memory, etc.)

### get_orchestrator()
Retorna o conteudo de `00-Meta/orchestrator.md`.
Usado como ponto de entrada para qualquer pedido.

### get_persona(agent, name)
Retorna conteudo de uma persona especifica.
- `agent`: "claude-code" | "codex" | "gemini"
- `name`: "data-analyst" | "ai-developer" | "researcher"

### get_skill(name)
Retorna conteudo de uma skill especifica.
- `name`: nome da pasta da skill (ex: "data-analysis", "visualization")

### follow_chain(skill_name, depth)
Segue o encadeamento de skills a partir de uma skill inicial.
- `skill_name`: skill de partida
- `depth`: profundidade maxima (default: 3, max: 5)
- Return: arvore de skills encadeadas com procedimentos

### search_semantic(query, scope)
Busca semantica no vault (requer Smart Connections).
- `query`: texto de busca por significado
- `scope`: "all" | "personas" | "skills" | "knowledge" | "memory"
- Return: notas relevantes ordenadas por similaridade

### update_memory(section, content)
Atualiza uma secao da memoria.
- `section`: "context-index" | "data-analyst-memory" | "ai-developer-memory" | "researcher-memory" | "decisions"
- `content`: conteudo a adicionar (append, nao overwrite)

### list_skills_for_persona(agent, persona_name)
Lista todas as skills vinculadas a uma persona (primarias + secundarias).
- Return: lista de skills com tipo de vinculo (primaria/secundaria/delegacao)

## Resources

Cada resource mapeia para um caminho no vault:

| URI | Caminho | Descricao |
|-----|---------|-----------|
| `vault://orchestrator` | `00-Meta/orchestrator.md` | Ponto de entrada |
| `vault://persona/claude-code/data-analyst` | `01-Personas/claude-code/persona-data-analyst.md` | Persona especifica |
| `vault://skill/data-analysis` | `02-Skills/data-analysis/SKILL.md` | Skill especifica |
| `vault://memory/context-index` | `04-Memory/context-index.md` | Brain Index |
| `vault://knowledge/AI-Development/patterns` | `03-Knowledge-Base/AI-Development/patterns/` | Base de conhecimento |
| `vault://brain-index` | `04-Memory/context-index.md` | Alias para brain index |

## Prompts

### start_session
Template para inicio de sessao:
```
Leia vault://brain-index para contexto atualizado.
Leia vault://orchestrator para mapa de personas e skills.
Identifique o dominio do pedido e selecione a persona adequada.
```

### activate_persona
Template para ativar uma persona:
```
Leia vault://persona/{agent}/{name} para carregar comportamento.
Liste skills vinculadas com list_skills_for_persona().
Siga o metodo PREVC descrito na persona.
```

### chain_skills
Template para encadear skills:
```
Execute follow_chain({skill_name}, depth=3).
Para cada skill na cadeia, execute o procedimento descrito.
Passe o output de cada skill como input da proxima.
```

## Configuracao para Claude Code

```json
{
  "mcpServers": {
    "ai-brain": {
      "command": "node",
      "args": ["ai-brain-mcp-server/index.js"],
      "env": {
        "VAULT_PATH": "C:\\Users\\isa_e\\Documents\\AI-Brain",
        "ENABLE_SEMANTIC_SEARCH": "true",
        "SMART_CONNECTIONS_PATH": "C:\\Users\\isa_e\\Documents\\AI-Brain\\.obsidian\\plugins\\smart-connections"
      }
    }
  }
}
```

## Configuracao para Codex CLI

```json
{
  "context": {
    "mcp_servers": {
      "ai-brain": {
        "command": "node",
        "args": ["ai-brain-mcp-server/index.js"],
        "env": {
          "VAULT_PATH": "C:\\Users\\isa_e\\Documents\\AI-Brain"
        }
      }
    }
  }
}
```

## Implementacao

O MCP server pode ser implementado em TypeScript usando o SDK oficial:
- `@modelcontextprotocol/sdk` — SDK TypeScript para MCP servers
- Cada tool mapeia para operacoes de leitura de filesystem + parsing de Markdown + extracao de wikilinks
- Busca semantica via Smart Connections embeddings (`.obsidian/plugins/smart-connections/.smart-env/`)

## Proximos Passos

1. Implementar o MCP server como projeto TypeScript
2. Publicar no npm para facilitar instalacao (`npx ai-brain-mcp`)
3. Testar com Claude Code, Codex e Gemini
4. Iterar baseado em uso real


## Navegacao

- Orquestrador: [[00-Meta/orchestrator]]
- Graph Map: [[00-Meta/graph-map]]
- Setup Guide: [[00-Meta/SETUP-GUIDE]]
- MCP Guide: [[03-Knowledge-Base/AI-Development/frameworks/mcp-guide]]