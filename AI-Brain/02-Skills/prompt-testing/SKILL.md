---
tags: [skill, prompt-testing, ai-development]
compatible-agents: [claude-code, codex]
version: "1.0"
trigger-phrases: [testar prompt, avaliar prompt, benchmark prompt, comparar prompts, prompt eval]
created: 2026-03-02
type: skill
status: active
---

# Skill: Prompt Testing

## Trigger
Ativar quando um prompt precisa ser avaliado sistematicamente antes de ser usado em producao.

## Procedimento

1. **Definir**: Criterios de sucesso (accuracy, formato, tom, safety).
2. **Preparar**: Criar conjunto de test cases (5-10 inputs variados).
3. **Executar**: Rodar prompt contra cada test case. Registrar outputs.
4. **Avaliar**: Comparar outputs contra criterios. Calcular taxa de sucesso.
5. **Iterar**: Ajustar prompt baseado em falhas. Re-testar.
6. **Documentar**: Salvar versao final com metadata (modelo, score, limitacoes).

## Encadeamento

- Chamada POR: [[02-Skills/prompt-engineering/SKILL]] (etapa de validacao)
- Saida PARA: [[04-Memory/ai-developer-memory]] (registrar prompt validado)

## Ferramentas
API calls diretas, promptfoo, custom eval scripts

## Navegacao
- Orquestrador: [[00-Meta/orchestrator]]
