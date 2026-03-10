---
tags: [skill, visualization, data-science]
compatible-agents: [claude-code, codex, gemini]
version: "1.0"
trigger-phrases: [grafico, visualizacao, plot, chart, dashboard, histogram, scatter, boxplot]
created: 2026-03-02
type: skill
status: active
---

# Skill: Visualization

## Trigger
Ativar quando o pedido envolver criacao de graficos, dashboards ou visualizacoes de dados.

## Procedimento

1. **Objetivo**: Que historia os dados devem contar? Qual a pergunta a responder visualmente?
2. **Tipo**: Escolher tipo de grafico adequado ao dado (categorico → bar, temporal → line, distribuicao → histogram, relacao → scatter).
3. **Criar**: Implementar com plotly (interativo) ou matplotlib (estatico).
4. **Estilizar**: Titulo, labels nos eixos, legenda, cores significativas, anotacoes.
5. **Validar**: O grafico responde a pergunta? Os dados estao corretos? A escala e adequada?

## Encadeamento

- Chamada POR: [[02-Skills/data-analysis/SKILL]], [[02-Skills/document-creation/SKILL]], [[02-Skills/presentation/SKILL]], [[02-Skills/spreadsheet/SKILL]]
- Chama: [[02-Skills/data-cleaning/SKILL]] (se dados precisam de limpeza antes)

## Ferramentas
plotly, matplotlib, seaborn, altair

## Navegacao
- Orquestrador: [[00-Meta/orchestrator]]
