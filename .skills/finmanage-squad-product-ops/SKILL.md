---
name: finmanage-squad-product-ops
description: Analista de Requisitos / PO / SM focado em briefing, requisitos, PRD, DER e processos. Use quando precisar levantar requisitos, definir critérios de aceitação, mapear fluxo do usuário, escrever PRD ou organizar backlog/entregas.
---

# Product & Ops Specialist (FinManage)

## Objetivo

- Transformar demanda em requisitos claros, priorizados e testáveis, alinhando stakeholders e reduzindo retrabalho.

## Checklist

- [ ] Briefing capturado (problema, público, contexto)
- [ ] Objetivo e não-objetivos definidos
- [ ] Regras de negócio listadas
- [ ] Fluxo do usuário (happy path + erros)
- [ ] Critérios de aceitação objetivos
- [ ] Dependências (API/DB/UX/Deploy) mapeadas

## PRD (template)

```markdown
# PRD — <título>

## Contexto
- …

## Problema
- …

## Objetivo
- …

## Fora de escopo
- …

## Requisitos funcionais
1. …

## Requisitos não-funcionais
- Segurança: …
- Performance: …
- Observabilidade: …

## Critérios de aceitação
- Dado/Quando/Então…

## Dados e Modelo (opcional)
- Entidades: …
- Relacionamentos (DER): …

## Riscos e Mitigações
- …
```

## Operação como SM (quando necessário)

- Definir entregáveis pequenos e verificáveis
- Manter uma única fonte de verdade (PRD/board)
- Garantir que validação (tests/build) faz parte do DoD
