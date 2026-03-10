---
tags: [data-science, technique, eda]
created: 2026-03-02
updated: 2026-03-02
type: note
status: active
---

# EDA Checklist — Analise Exploratoria de Dados

Checklist padrao para analise exploratoria antes de qualquer modelagem.

## 1. Visao Geral
- [ ] shape (linhas x colunas)
- [ ] dtypes de cada coluna
- [ ] head() e tail() para inspeção visual
- [ ] describe() para estatisticas basicas
- [ ] info() para uso de memoria

## 2. Qualidade dos Dados
- [ ] Missing values por coluna (% e contagem)
- [ ] Duplicatas (linhas identicas)
- [ ] Valores unicos por coluna categorica
- [ ] Outliers via IQR ou z-score
- [ ] Tipos inconsistentes (numeros como strings, etc.)

## 3. Distribuicoes
- [ ] Histograma de variaveis numericas
- [ ] Contagem de categorias para variaveis categoricas
- [ ] Skewness e kurtosis
- [ ] Boxplots para detectar outliers visuais

## 4. Relacoes
- [ ] Matriz de correlacao (numericas)
- [ ] Scatter plots para pares relevantes
- [ ] Crosstabs para categoricas vs categoricas
- [ ] Analise de variancia (categorica vs numerica)

## 5. Temporalidade (se aplicavel)
- [ ] Tendencia ao longo do tempo
- [ ] Sazonalidade
- [ ] Gaps temporais

## 6. Documentar
- [ ] Premissas sobre os dados
- [ ] Transformacoes aplicadas
- [ ] Limitacoes identificadas
- [ ] Proximos passos recomendados


## Navegacao

- Data Analysis Skill: [[02-Skills/data-analysis/SKILL]]
- Visualization Skill: [[02-Skills/visualization/SKILL]]
- Python Stack: [[03-Knowledge-Base/Data-Science/tools/python-stack]]