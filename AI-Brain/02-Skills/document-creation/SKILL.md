---
tags: [skill, document-creation, espelho-cowork]
compatible-agents: [claude-code, codex, gemini]
version: "1.1"
trigger-phrases: [criar documento, relatorio, Word, docx, memo, letter, report]
mirrors: anthropic-skills:docx
created: 2026-03-02
updated: 2026-03-02
type: skill
status: active
---

# Skill: Document Creation (Espelho docx)

> [!info] Espelho
> Esta skill espelha `anthropic-skills:docx` do Cowork.

## Trigger
Ativar quando o pedido envolver criar ou editar documentos Word, relatorios, memos ou qualquer material formatado.

## Procedimento

1. **Escopo**: Definir tipo (relatorio, memo, carta), audiencia, tom e formato.
2. **Estrutura**: Criar outline com secoes principais. Validar com usuario se complexo.
3. **Conteudo**: Redigir com foco em clareza. Incluir dados e visualizacoes quando relevante.
4. **Formatar**: Aplicar formatacao profissional (headers, ToC, numeracao).
5. **Revisar**: Verificar ortografia, consistencia, dados citados.

## Encadeamento

- Chamada POR: [[01-Personas/claude-code/persona-data-analyst]] (relatorios de analise)
- Chamada POR: [[01-Personas/claude-code/persona-researcher]] (relatorios de pesquisa)
- Chama: [[02-Skills/visualization/SKILL]] (graficos para o documento)
- Chama: [[02-Skills/data-analysis/SKILL]] (dados para o documento)
- Alternativas de formato:
  - [[02-Skills/presentation/SKILL]] (se formato for slides)
  - [[02-Skills/pdf-processing/SKILL]] (se formato for PDF)
  - [[02-Skills/spreadsheet/SKILL]] (se formato for planilha)

## Navegacao

- Orquestrador: [[00-Meta/orchestrator]]
- Cowork skill: anthropic-skills:docx
