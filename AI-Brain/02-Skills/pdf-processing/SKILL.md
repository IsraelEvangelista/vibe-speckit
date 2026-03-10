---
tags: [skill, pdf, espelho-cowork]
compatible-agents: [claude-code, codex, gemini]
version: "1.0"
trigger-phrases: [PDF, extrair PDF, preencher formulario, merge PDF, criar PDF]
mirrors: anthropic-skills:pdf
created: 2026-03-02
type: skill
status: active
---

# Skill: PDF Processing (Espelho pdf)

> [!info] Espelho
> Esta skill espelha `anthropic-skills:pdf` do Cowork.

## Trigger
Ativar quando o pedido envolver criar, ler, extrair, combinar ou manipular PDFs.

## Procedimento

1. **Identificar**: Tipo de operacao (criar, extrair, merge, split, form fill).
2. **Processar**: Executar operacao com ferramentas adequadas.
3. **Validar**: Verificar output (paginas corretas, texto legivel, forms preenchidos).
4. **Entregar**: Salvar PDF final com nome descritivo.

## Encadeamento

- Chamada POR: [[02-Skills/document-creation/SKILL]] (quando formato e PDF)
- Chama: [[02-Skills/data-analysis/SKILL]] (para extrair tabelas de PDFs)

## Navegacao

- Orquestrador: [[00-Meta/orchestrator]]
- Cowork skill: anthropic-skills:pdf
