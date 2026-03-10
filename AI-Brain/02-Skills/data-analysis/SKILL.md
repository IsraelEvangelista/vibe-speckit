---
tags: [skill, data-analysis]
compatible-agents: [claude-code, codex, gemini]
version: "1.0"
trigger-phrases: [analisar dados, explorar dataset, EDA, criar grafico, metricas, insights, CSV, dataframe]
created: 2026-03-02
type: skill
status: active
---

# Skill: Data Analysis

## Trigger
Ativar quando o usuario pedir para analisar dados, explorar datasets, criar visualizacoes, calcular metricas ou gerar insights a partir de dados tabulares.

## Procedimento

1. **Carregar dados**: Identificar formato (CSV, Excel, SQL, JSON). Usar pandas para leitura.
2. **Inspecionar**: shape, dtypes, head(), describe(), missing values, duplicatas.
3. **Limpar**: Tratar missing values, outliers, tipos incorretos. Documentar cada transformacao.
4. **Analisar**: Calcular metricas relevantes. Criar visualizacoes exploratórias.
5. **Validar**: Verificar consistencia numerica. Cross-check com fonte original.
6. **Reportar**: Apresentar insights com graficos. Declarar limitacoes e premissas.

## Output Esperado
- Resumo estatistico do dataset
- Visualizacoes relevantes (plotly interativo ou matplotlib)
- Lista de insights acionaveis
- Limitacoes e proximos passos sugeridos

## Ferramentas
pandas, numpy, plotly, matplotlib, seaborn, scipy, scikit-learn


## Conhecimento Relacionado

- EDA Checklist: [[03-Knowledge-Base/Data-Science/techniques/eda-checklist]]
- Python Stack: [[03-Knowledge-Base/Data-Science/tools/python-stack]]