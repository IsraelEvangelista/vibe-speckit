---
tags: [skill, testing, programming]
compatible-agents: [claude-code, codex]
version: "1.0"
trigger-phrases: [testar, testes unitarios, pytest, coverage, TDD, test case, assertion]
created: 2026-03-02
type: skill
status: active
---

# Skill: Testing

## Trigger
Ativar quando codigo precisar de testes automatizados.

## Procedimento

1. **Mapear**: Identificar funcoes/metodos a testar. Listar cenarios (happy path, edge cases, errors).
2. **Estruturar**: Criar arquivo de teste com pytest. Organizar por classe/funcao.
3. **Implementar**: Escrever tests com assertions claras. Usar fixtures para setup.
4. **Executar**: Rodar testes. Verificar coverage.
5. **Reportar**: Listar testes passando/falhando. Sugerir melhorias de coverage.

## Encadeamento

- Chamada POR: [[02-Skills/code-generation/SKILL]] (etapa 4)
- Chama: [[02-Skills/code-review/SKILL]] (se testes revelam problemas)

## Ferramentas
pytest, unittest, coverage, pytest-cov, hypothesis

## Navegacao
- Orquestrador: [[00-Meta/orchestrator]]
