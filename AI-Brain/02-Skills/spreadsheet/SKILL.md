---
tags: [skill, spreadsheet, xlsx, espelho-cowork]
compatible-agents: [claude-code, codex, gemini]
version: "1.0"
trigger-phrases: [planilha, Excel, xlsx, csv, tabela de dados, formulas, pivot]
mirrors: anthropic-skills:xlsx
created: 2026-03-02
type: skill
status: active
---

# Skill: Spreadsheet (Espelho xlsx)

> [!info] Espelho
> Esta skill espelha `anthropic-skills:xlsx` do Cowork. Use no Claude Code/Cowork para execucao direta. Use esta nota para referencia e encadeamento no grafo.

## Trigger
Ativar quando o pedido envolver criar, editar ou analisar planilhas (.xlsx, .csv, .tsv).

## Procedimento

1. **Entender**: Tipo de planilha (analise, relatorio, dashboard). Dados de entrada.
2. **Estruturar**: Definir colunas, tipos, formulas necessarias.
3. **Popular**: Inserir dados com formatacao adequada.
4. **Formatar**: Aplicar estilos, graficos, formatacao condicional.
5. **Validar**: Verificar formulas, totais, consistencia.

## Encadeamento

- Chamada POR: [[02-Skills/data-analysis/SKILL]] (quando output e planilha)
- Chama: [[02-Skills/visualization/SKILL]] (para graficos embedded)

## Navegacao

- Orquestrador: [[00-Meta/orchestrator]]
- Personas que usam: [[01-Personas/claude-code/persona-data-analyst]]
- Cowork skill: anthropic-skills:xlsx (executar via Cowork)
