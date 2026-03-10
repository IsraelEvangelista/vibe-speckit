---
tags: [skill, fact-checking, research]
compatible-agents: [claude-code, gemini]
version: "1.0"
trigger-phrases: [verificar, fact-check, e verdade, confirmar informacao, validar claim, fonte confiavel]
created: 2026-03-02
type: skill
status: active
---

# Skill: Fact-Checking

## Trigger
Ativar quando uma informacao precisa ser verificada antes de ser aceita como fato.

## Procedimento

1. **Isolar**: Identificar o claim especifico a verificar.
2. **Buscar**: Encontrar fontes primarias independentes (minimo 2-3).
3. **Comparar**: Confrontar claim com evidencias. Identificar contradicoes.
4. **Classificar**: Confirmado / Parcialmente Confirmado / Nao Confirmado / Falso.
5. **Documentar**: Registrar claim, fontes consultadas, veredicto e confianca.

## Encadeamento

- Chamada POR: [[02-Skills/research/SKILL]] (etapa de validacao)
- Chama: [[02-Skills/synthesis/SKILL]] (consolidar verificacoes)

## Ferramentas
Web search, documentacao oficial, papers academicos, bases de dados publicas

## Navegacao
- Orquestrador: [[00-Meta/orchestrator]]
