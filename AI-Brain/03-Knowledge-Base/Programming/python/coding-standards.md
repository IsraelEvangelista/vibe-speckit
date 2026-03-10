---
tags: [programming, python, standards]
created: 2026-03-02
updated: 2026-03-02
type: note
status: active
---

# Python Coding Standards

Convencoes de codigo para projetos Python neste vault.

## Estilo
- PEP 8 como base
- Black como formatter (line length 88)
- isort para imports
- Type hints obrigatorios em funcoes publicas

## Estrutura de Projeto
```
project/
├── src/
│   └── package/
│       ├── __init__.py
│       ├── main.py
│       └── utils.py
├── tests/
│   └── test_main.py
├── pyproject.toml
├── README.md
└── .env.example
```

## Seguranca
- NUNCA hardcodar credenciais
- Usar python-dotenv ou variaveis de ambiente
- Incluir .env no .gitignore
- Validar inputs com pydantic

## Testes
- pytest como framework padrao
- Coverage minimo: 80%
- Nomear testes descritivamente: test_should_return_error_when_input_empty

## Logging
- Usar logging stdlib (nao print)
- Niveis: DEBUG para dev, INFO para prod
- Incluir contexto relevante nas mensagens


## Navegacao

- Python Stack: [[03-Knowledge-Base/Data-Science/tools/python-stack]]
- Code Generation: [[02-Skills/code-generation/SKILL]]
- Code Review: [[02-Skills/code-review/SKILL]]