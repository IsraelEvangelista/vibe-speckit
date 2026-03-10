---
tags: [meta, setup, guide]
created: 2026-03-02
updated: 2026-03-02
type: reference
status: active
---

# Guia de Instalacao — AI-Brain Vault

Guia passo a passo para configurar o Obsidian e integrar com Claude Code, Codex e Gemini.

## Passo 1: Instalar Obsidian

1. Acessar https://obsidian.md/download
2. Baixar para Windows
3. Instalar e abrir o Obsidian
4. Na tela inicial, escolher **"Open folder as vault"**
5. Selecionar a pasta `AI-Brain` que ja foi criada

> Apos abrir, o Obsidian vai detectar automaticamente toda a estrutura de pastas e notas .md.

## Passo 2: Configurar Obsidian

### Configuracoes essenciais (Settings > Editor)
- **Default new note location**: "In the folder specified below" → `03-Knowledge-Base`
- **New link format**: "Shortest path when possible"
- **Use Wikilinks**: ON

### Configuracoes essenciais (Settings > Files & Links)
- **Detect all file extensions**: ON
- **Default location for new attachments**: `00-Meta/attachments`

### Templates (Settings > Core plugins > Templates)
- Ativar o plugin "Templates"
- Template folder location: `00-Meta/Templates`

### Daily Notes (Settings > Core plugins > Daily notes)
- Ativar o plugin "Daily notes"
- New file location: `05-Daily-Operations/daily-notes`
- Template file location: `00-Meta/Templates/_template-daily`
- Date format: `YYYY-MM-DD`

## Passo 3: Instalar Community Plugins Recomendados

Ir em Settings > Community plugins > Turn on community plugins > Browse

### Essenciais

1. **Dataview** — Queries dentro das notas (listar todas as personas ativas, etc.)
2. **Templater** — Templates avancados com variaveis dinamicas
3. **Calendar** — Visualizacao de daily notes em calendario

### Para Integracao com AI

4. **Local REST API** — Necessario para MCP via REST (Claude Code)
   - Apos instalar, ir nas settings do plugin e copiar a API Key
   - URL padrao: `https://127.0.0.1:27123`

5. **Smart Connections** — RAG local com embeddings (busca semantica)
   - Permite buscar por significado, nao apenas palavras exatas
   - Funciona com obsidian-semantic-mcp

### Opcionais

6. **Git** — Versionamento automatico do vault
7. **Excalidraw** — Diagramas e sketches dentro do vault
8. **Kanban** — Boards para gerenciamento visual de tarefas

## Passo 4: Integrar com Claude Code

### Opcao A: Via Filesystem (simples, recomendado para comecar)

Basta navegar ate o vault e abrir o Claude Code:

```bash
cd C:\Users\isa_e\Documents\AI-Brain
claude
```

O Claude Code detecta automaticamente o `CLAUDE.md` na raiz e carrega o contexto.

### Opcao B: Via MCP (mais poderoso)

Requer o plugin **Local REST API** instalado no Obsidian (Passo 3).

1. Copiar a API Key do plugin Local REST API
2. Editar `~/.claude/settings.json` (ou `%USERPROFILE%\.claude\settings.json` no Windows):

```json
{
  "mcpServers": {
    "obsidian": {
      "command": "npx",
      "args": ["-y", "mcp-obsidian"],
      "env": {
        "OBSIDIAN_API_KEY": "SUA_API_KEY_AQUI",
        "OBSIDIAN_API_URL": "https://127.0.0.1:27123"
      }
    }
  }
}
```

3. Reiniciar o Claude Code
4. Testar: `claude "liste as notas do vault"`

### Opcao C: Via MCP Semantico (busca inteligente)

Requer **Smart Connections** + **Local REST API**.

```json
{
  "mcpServers": {
    "obsidian-semantic": {
      "command": "npx",
      "args": ["-y", "obsidian-semantic-mcp"],
      "env": {
        "OBSIDIAN_API_KEY": "SUA_API_KEY_AQUI",
        "OBSIDIAN_API_URL": "https://127.0.0.1:27123"
      }
    }
  }
}
```

## Passo 5: Integrar com Codex (OpenAI)

### Via Filesystem

Codex CLI acessa o vault diretamente:

```bash
cd C:\Users\isa_e\Documents\AI-Brain
codex "leia AGENTS.md e liste as personas disponiveis"
```

### Via codex.json (contexto automatico)

Criar `codex.json` na raiz do vault:

```json
{
  "context": {
    "files": [
      "AGENTS.md",
      "04-Memory/context-index.md"
    ]
  },
  "instructions": "Siga o metodo PREVC. Consulte 01-Personas/ para comportamento adequado."
}
```

## Passo 6: Integrar com Gemini

### Via Plugin Obsidian

1. Instalar **Gemini Scribe** ou **Smart Connections** (com Gemini como provider) via Community Plugins
2. Configurar API Key do Google AI nas settings do plugin

### Via Google AI Studio

1. Fazer upload de notas relevantes como contexto
2. Usar system instruction baseado no conteudo de `AGENTS.md`

### Via API (programatico)

```python
import google.generativeai as genai
from pathlib import Path

# Carregar contexto do vault
agents_md = Path("AGENTS.md").read_text()
brain_index = Path("04-Memory/context-index.md").read_text()

genai.configure(api_key="$GEMINI_API_KEY")
model = genai.GenerativeModel(
    "gemini-2.0-flash",
    system_instruction=f"{agents_md}\n\n{brain_index}"
)
```

## Passo 7: Sincronizacao Cowork <-> Claude Code

O script `sync-brain.py` (disponivel em `.claude/skills/obsidian-brain/scripts/`) sincroniza entre o vault e o Claude Code:

```bash
# Vault → Claude Code
python sync-brain.py --vault "C:\Users\isa_e\Documents\AI-Brain" --claude-home "%USERPROFILE%\.claude"

# Dry-run (simular sem modificar)
python sync-brain.py --vault "C:\Users\isa_e\Documents\AI-Brain" --claude-home "%USERPROFILE%\.claude" --dry-run
```

## Verificacao Final

Checklist para confirmar que tudo funciona:

- [ ] Obsidian abre o vault e mostra todas as pastas
- [ ] Graph view mostra conexoes entre notas
- [ ] Templates funcionam (criar nova nota com template)
- [ ] Daily notes criam com formato correto
- [ ] Plugin Local REST API esta ativo (se usando MCP)
- [ ] Claude Code reconhece CLAUDE.md ao abrir o vault
- [ ] Codex acessa AGENTS.md via filesystem
- [ ] Gemini integrado via plugin ou API


## Navegacao

- Convencoes: [[00-Meta/Conventions]]
- Orquestrador: [[00-Meta/orchestrator]]
- MCP Schema: [[00-Meta/Schemas/mcp-server-schema]]