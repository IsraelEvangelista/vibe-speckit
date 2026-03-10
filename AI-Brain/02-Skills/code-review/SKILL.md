---
tags: [skill, code-review, programming]
compatible-agents: [claude-code, codex]
version: "1.0"
trigger-phrases: [revisar codigo, code review, refatorar, melhorar codigo, otimizar, bugs]
created: 2026-03-02
type: skill
status: active
---

# Skill: Code Review

## Trigger
Ativar quando codigo existente precisar ser revisado, refatorado ou otimizado.

## Procedimento

1. **Ler**: Entender o proposito do codigo. Identificar entrada/saida.
2. **Analisar**: Verificar legibilidade, complexidade, patterns, seguranca, performance.
3. **Listar**: Criar lista de issues por severidade (critico, medio, sugestao).
4. **Propor**: Sugerir refatoracoes concretas com justificativa.
5. **Validar**: Garantir que refatoracoes nao quebram funcionalidade (rodar testes).

## Encadeamento

- Chamada POR: [[02-Skills/code-generation/SKILL]], [[02-Skills/testing/SKILL]]
- Chama: [[02-Skills/testing/SKILL]] (validar apos refatoracao)

## Ferramentas
pylint, flake8, mypy, black, bandit (security)

## Navegacao
- Orquestrador: [[00-Meta/orchestrator]]
