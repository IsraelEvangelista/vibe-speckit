---
tags: [skill, data-cleaning, data-science]
compatible-agents: [claude-code, codex]
version: "1.0"
trigger-phrases: [limpar dados, tratar missing, remover duplicatas, normalizar, encoding, outliers]
created: 2026-03-02
type: skill
status: active
---

# Skill: Data Cleaning

## Trigger
Ativar quando dados brutos precisam ser limpos antes de analise.

## Procedimento

1. **Diagnosticar**: Identificar problemas (missing, duplicatas, tipos errados, outliers, encoding).
2. **Planejar**: Definir estrategia por problema (drop, impute, transform, clip).
3. **Executar**: Aplicar transformacoes documentando cada uma.
4. **Validar**: Comparar shape antes/depois. Verificar distribuicoes. Confirmar que nao houve perda indevida.
5. **Documentar**: Registrar todas as transformacoes aplicadas e justificativas.

## Encadeamento

- Chamada POR: [[02-Skills/data-analysis/SKILL]] (etapa 3 da analise)
- Chama: [[02-Skills/statistical-testing/SKILL]] (para decidir sobre outliers)
- Saida PARA: [[02-Skills/visualization/SKILL]] (dados limpos para graficos)

## Ferramentas
pandas, numpy, scikit-learn (imputers), pyjanitor

## Navegacao
- Orquestrador: [[00-Meta/orchestrator]]
