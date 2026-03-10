---
tags: [skill, code-generation]
compatible-agents: [claude-code, codex, gemini]
version: "1.0"
trigger-phrases: [gerar codigo, criar script, implementar, programar, desenvolver modulo, criar funcao]
created: 2026-03-02
type: skill
status: active
---

# Skill: Code Generation

## Trigger
Ativar quando o usuario pedir para gerar codigo, criar scripts, implementar funcionalidades ou desenvolver modulos.

## Procedimento

1. **Requisitos**: Entender input, output, restricoes. Perguntar se ambiguo.
2. **Arquitetura**: Definir modulos, interfaces, dependencias. Escolher patterns.
3. **Implementar**: Codigo modular com type hints, docstrings, error handling.
4. **Testar**: Criar testes unitarios. Verificar edge cases.
5. **Documentar**: README com instrucoes de uso, dependencias, exemplos.

## Output Esperado
- Codigo funcional e testado
- Testes unitarios
- Documentacao de uso (README ou docstrings)
- Lista de dependencias

## Ferramentas
Python, JavaScript/TypeScript, SQL, pytest, unittest, mypy


## Conhecimento Relacionado

- Coding Standards: [[03-Knowledge-Base/Programming/python/coding-standards]]
- Agent Orchestration: [[03-Knowledge-Base/AI-Development/patterns/agent-orchestration]]