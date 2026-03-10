---
tags: [skill, research]
compatible-agents: [claude-code, codex, gemini]
version: "1.0"
trigger-phrases: [pesquisar, investigar, comparar, avaliar, revisar, fact-check, analisar opcoes]
created: 2026-03-02
type: skill
status: active
---

# Skill: Research

## Trigger
Ativar quando o usuario pedir para pesquisar, investigar, comparar opcoes, avaliar ferramentas ou verificar informacoes.

## Procedimento

1. **Definir**: Pergunta de pesquisa clara. Criterios de avaliacao. Fontes prioritarias.
2. **Buscar**: Multiplas fontes. Documentacao oficial primeiro, depois comunidade.
3. **Avaliar**: Credibilidade (autor, data, vies). Triangular claims entre fontes.
4. **Sintetizar**: Organizar por relevancia. Tabelas comparativas quando aplicavel.
5. **Validar**: Verificar claims principais com fontes independentes.
6. **Reportar**: Citar fontes. Separar fato/opiniao/especulacao. Declarar confianca.

## Output Esperado
- Resumo executivo (3-5 paragrafos)
- Tabela comparativa (se aplicavel)
- Lista de fontes com links e datas
- Nivel de confianca por achado
- Lacunas identificadas e sugestoes de investigacao

## Ferramentas
Web search, documentacao oficial, arXiv, GitHub, HuggingFace, forums
