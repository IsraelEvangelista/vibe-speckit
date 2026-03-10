---
tags: [meta, graph, map, visualization]
created: 2026-03-02
updated: 2026-03-02
type: reference
status: active
---

# Mapa do Grafo AI-Brain

## Visao Geral

```mermaid
graph TD
    ORC["00-Meta/orchestrator<br/>ORQUESTRADOR"]

    subgraph Personas Claude Code
        PDA_CC["persona-data-analyst<br/>Claude Code"]
        PAI_CC["persona-ai-developer<br/>Claude Code"]
        PRE_CC["persona-researcher<br/>Claude Code"]
    end

    subgraph Personas Codex
        PDA_CX["persona-data-analyst<br/>Codex"]
        PAI_CX["persona-ai-developer<br/>Codex"]
        PRE_CX["persona-researcher<br/>Codex"]
    end

    subgraph Personas Gemini
        PDA_GM["persona-data-analyst<br/>Gemini"]
        PAI_GM["persona-ai-developer<br/>Gemini"]
        PRE_GM["persona-researcher<br/>Gemini"]
    end

    ORC --> PDA_CC & PAI_CC & PRE_CC
    ORC --> PDA_CX & PAI_CX & PRE_CX
    ORC --> PDA_GM & PAI_GM & PRE_GM

    subgraph Skills Core
        S_DA["data-analysis"]
        S_CG["code-generation"]
        S_RE["research"]
        S_PE["prompt-engineering"]
    end

    subgraph Skills Complementares
        S_DC["data-cleaning"]
        S_VZ["visualization"]
        S_ST["statistical-testing"]
        S_TS["testing"]
        S_CR["code-review"]
        S_FC["fact-checking"]
        S_SY["synthesis"]
        S_PT["prompt-testing"]
        S_KC["knowledge-consolidation"]
    end

    subgraph Skills Documento
        S_DOC["document-creation"]
        S_XLS["spreadsheet"]
        S_PPT["presentation"]
        S_PDF["pdf-processing"]
    end

    PDA_CC --> S_DA & S_VZ & S_DC
    PAI_CC --> S_CG & S_PE & S_RE
    PRE_CC --> S_RE & S_FC & S_SY

    S_DA --> S_DC & S_VZ & S_ST
    S_CG --> S_TS & S_CR
    S_RE --> S_FC & S_SY
    S_PE --> S_PT
    S_SY --> S_DOC & S_KC
    S_FC --> S_SY
    S_KC --> S_FC

    S_DOC --> S_VZ & S_DA
    S_XLS --> S_VZ
    S_PPT --> S_VZ & S_RE
    S_PDF --> S_DA

    PDA_CC <-.->|delega| PAI_CC
    PDA_CC <-.->|delega| PRE_CC
    PAI_CC <-.->|delega| PRE_CC
```

## Grafo de Encadeamento de Skills

```mermaid
graph LR
    subgraph Pipeline Data Science
        DA["data-analysis"] --> DC["data-cleaning"]
        DC --> VZ["visualization"]
        DA --> ST["statistical-testing"]
        DA --> VZ
        ST --> VZ
    end

    subgraph Pipeline AI Development
        CG["code-generation"] --> TS["testing"]
        TS --> CR["code-review"]
        CR --> TS
        PE["prompt-engineering"] --> PT["prompt-testing"]
    end

    subgraph Pipeline Research
        RE["research"] --> FC["fact-checking"]
        FC --> SY["synthesis"]
        SY --> KC["knowledge-consolidation"]
        SY --> DOC["document-creation"]
    end

    subgraph Pipeline Documentos
        DOC --> VZ
        DOC --> DA
        XLS["spreadsheet"] --> VZ
        PPT["presentation"] --> VZ
        PDF["pdf-processing"] --> DA
    end

    DA -.-> DOC
    DA -.-> XLS
    RE -.-> DOC
    RE -.-> PPT
```

## Estatisticas do Grafo

| Metrica | Valor |
|---------|-------|
| Total de nos (notas) | ~50 |
| Personas | 9 (3 agentes x 3 dominios) |
| Skills Core | 4 |
| Skills Complementares | 9 |
| Skills Documento (espelho) | 4 |
| Shared rules | 2 |
| Memory nodes | 5 |
| Knowledge Base nodes | 6+ |
| Orquestrador | 1 |
| Media de conexoes por no | ~4-6 |

## Como Usar no Obsidian

1. Abrir o vault no Obsidian
2. Ir em Graph View (Ctrl+G)
3. Filtrar por tags para ver subgrafos:
   - `tag:#persona` — grafo de personas
   - `tag:#skill` — grafo de skills
   - `tag:#memory` — grafo de memoria
4. Clicar em um no para navegar para a nota
5. Usar Local Graph (na sidebar direita) para ver conexoes de uma nota especifica


## Navegacao

- Orquestrador: [[00-Meta/orchestrator]]
- MCP Schema: [[00-Meta/Schemas/mcp-server-schema]]