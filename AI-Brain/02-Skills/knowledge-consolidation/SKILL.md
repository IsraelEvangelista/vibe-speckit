---
tags: [skill, knowledge-consolidation, memory, meta]
compatible-agents: [claude-code, codex, gemini]
version: "1.0"
trigger-phrases: [consolidar conhecimento, atualizar base, integrar aprendizado, criar framework, organizar notas]
created: 2026-03-02
type: skill
status: active
---

# Skill: Knowledge Consolidation

## Trigger
Ativar periodicamente ou quando houver acumulo de notas dispersas que precisam ser integradas em frameworks coerentes.

## Procedimento

1. **Escanear**: Listar notas recentes em 03-Knowledge-Base/ e 04-Memory/ nao consolidadas.
2. **Classificar**: Agrupar por dominio e tema. Identificar padroes recorrentes.
3. **Consolidar**: Criar ou atualizar notas de referencia que integram os achados.
4. **Linkar**: Adicionar wikilinks bidirecionais entre notas consolidadas e fontes.
5. **Atualizar**: Atualizar [[04-Memory/context-index]] com novos frameworks.

## Encadeamento

- Chamada POR: [[02-Skills/synthesis/SKILL]], [[02-Skills/research/SKILL]]
- Chama: [[02-Skills/fact-checking/SKILL]] (validar antes de consolidar)
- Saida PARA: [[04-Memory/context-index]] (atualizar brain index)

## Ferramentas
Obsidian graph view, Dataview queries, Smart Connections (busca semantica)

## Navegacao
- Orquestrador: [[00-Meta/orchestrator]]
