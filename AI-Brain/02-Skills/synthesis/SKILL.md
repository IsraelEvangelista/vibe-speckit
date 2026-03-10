---
tags: [skill, synthesis, research]
compatible-agents: [claude-code, gemini]
version: "1.0"
trigger-phrases: [sintetizar, consolidar, resumir achados, combinar fontes, criar framework, overview]
created: 2026-03-02
type: skill
status: active
---

# Skill: Synthesis

## Trigger
Ativar quando multiplas fontes de informacao precisam ser consolidadas em uma visao coerente.

## Procedimento

1. **Coletar**: Reunir todos os achados relevantes de fontes distintas.
2. **Deduplicar**: Remover informacoes redundantes.
3. **Organizar**: Agrupar por tema/categoria. Identificar consensos e controversias.
4. **Sintetizar**: Criar narrativa coerente que integra as fontes. Priorizar por relevancia.
5. **Apresentar**: Formato adequado (resumo executivo, tabela comparativa, framework).

## Encadeamento

- Chamada POR: [[02-Skills/research/SKILL]], [[02-Skills/fact-checking/SKILL]]
- Chama: [[02-Skills/document-creation/SKILL]] (para relatorio formal)
- Chama: [[02-Skills/knowledge-consolidation/SKILL]] (para integrar ao vault)

## Ferramentas
Markdown, tabelas comparativas, diagramas Mermaid

## Navegacao
- Orquestrador: [[00-Meta/orchestrator]]
