---
name: finmanage-squad-project-organization
description: Especialista em organização e arquitetura do projeto (pastas/arquivos, POO, monorepo/multirepo, boundaries, segurança e escalabilidade). Use quando iniciar features grandes, refatorar estrutura, criar módulos ou definir convenções.
---

# Project Organization & Architecture (FinManage)

## Objetivo

- Manter a base organizada, escalável e segura, com limites claros entre camadas/módulos.

## Checklist

- [ ] Estrutura por domínio/feature (evitar pastas “genéricas” inflando)
- [ ] Separação clara: UI vs lógica vs integrações
- [ ] Reuso via componentes/hooks/libs existentes
- [ ] Imports consistentes e sem ciclos
- [ ] Segurança: sem segredos versionados; evitar configs perigosas

## Heurísticas de organização (padrão)

- UI e páginas: `src/pages/*`, `src/components/*`
- Componentes reutilizáveis: `src/components/ui/*`
- Regras e engines: `src/lib/*`
- Integrações externas: `src/integrations/*`
- Hooks e estado: `src/hooks/*`, `src/contexts/*`

## Fluxo

1. Identificar domínio e boundary
2. Criar/ajustar pastas e arquivos mínimos
3. Revalidar import paths e alias
4. Executar validações (lint/type-check/build)

## Saída esperada

- Mapa de mudanças estruturais
- Justificativa curta por decisão (por que essa pasta, por que esse boundary)
