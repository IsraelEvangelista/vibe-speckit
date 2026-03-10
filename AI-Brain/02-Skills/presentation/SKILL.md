---
tags: [skill, presentation, pptx, espelho-cowork]
compatible-agents: [claude-code, codex, gemini]
version: "1.0"
trigger-phrases: [apresentacao, slides, PowerPoint, pptx, deck, pitch]
mirrors: anthropic-skills:pptx
created: 2026-03-02
type: skill
status: active
---

# Skill: Presentation (Espelho pptx)

> [!info] Espelho
> Esta skill espelha `anthropic-skills:pptx` do Cowork.

## Trigger
Ativar quando o pedido envolver criar ou editar apresentacoes (.pptx).

## Procedimento

1. **Escopo**: Definir audiencia, numero de slides, mensagem principal.
2. **Outline**: Criar estrutura de slides com titulos e bullet points.
3. **Conteudo**: Redigir texto conciso, selecionar dados/graficos.
4. **Design**: Aplicar template, cores, tipografia consistente.
5. **Revisar**: Verificar fluxo narrativo, ortografia, dados citados.

## Encadeamento

- Chamada POR: [[02-Skills/document-creation/SKILL]] (quando formato e slides)
- Chama: [[02-Skills/visualization/SKILL]] (para graficos nos slides)
- Chama: [[02-Skills/research/SKILL]] (para conteudo de pesquisa)

## Navegacao

- Orquestrador: [[00-Meta/orchestrator]]
- Cowork skill: anthropic-skills:pptx
