---
name: "project-organization-architecture"
description: "Define estrutura (Vertical Slicing, Hexagonal, Clean) e isolamento lógico. Invoque antes de criar/mover arquivos, iniciar uma feature ou decidir monorepo/multirepo."
---

# Organização de Projeto (Slicing + Hexagonal + Clean)

## Objetivo

Padronizar como agentes de codificação estruturam e isolam lógicas, arquivos e dependências para manter:

- alta coesão por feature (vertical slicing),
- baixo acoplamento entre domínios,
- dependências apontando para dentro (Clean Architecture),
- integrações isoladas via portas/adapters (Hexagonal).

## Quando invocar (gatilhos)

Invoque esta skill como PRIMEIRO passo quando:

- for criar uma nova feature, módulo, página ou integração;
- for mover pastas/arquivos, “reorganizar arquitetura” ou reduzir acoplamento;
- surgir dúvida entre monorepo vs multirepo;
- houver crescimento de lógica em hooks/components/pages sem um lugar claro.

## POO (POO serve às fronteiras, não o contrário)

POO é uma forma de modelar e proteger invariantes:

- **Encapsulamento**: regras de negócio ficam em entidades/value objects/use cases, não espalhadas em UI.
- **Polimorfismo**: adapters diferentes implementam a mesma porta (ex.: `AuthRepository` via Supabase hoje, outro provedor amanhã).
- **Abstrações por contrato**: “interfaces/ports” existem para isolar decisões externas (HTTP, DB, SDKs).

Não force classes em tudo (especialmente em UI React). Use classes/objetos onde houver invariantes e comportamento; caso contrário, use funções puras e tipos.

## Monorepo vs Multirepo (e variações)

### Monorepo (um repositório, múltiplos pacotes/apps)

Útil quando há:

- muitos projetos que precisam evoluir juntos,
- compartilhamento intenso (design system, libs, tipos),
- padronização forte de tooling (lint/test/build),
- necessidade de refactors atômicos cross-repo.

Variações comuns:

- **Monorepo por apps + packages**: `apps/*` e `packages/*`.
- **Monorepo modular (um app, muitos módulos)**: ainda um `src/` único, mas com boundaries rígidos por feature/domínio.
- **Monorepo com “internal packages”**: features/domínios viram pacotes internos quando passam de um tamanho/criticidade.

### Multirepo (vários repositórios)

Útil quando há:

- autonomia e cadência diferentes entre times/produtos,
- exigência de isolamento forte (segurança/compliance),
- releases e versionamento independentes,
- pouca necessidade de mudanças atômicas cross-projetos.

Variações comuns:

- **Multirepo com libs versionadas** (semver) publicadas (privadas ou públicas).
- **Multirepo com mono-contratos** (ex.: repo só de schemas/SDK) para alinhar integrações.

### Híbrido (recomendação pragmática)

- Comece com **monorepo modular** (um app bem organizado).
- Promova módulos maduros para **packages internos** quando:
  - a feature vira plataforma,
  - reuso é alto,
  - há necessidade de versionar/bloquear dependências.

## Diagrama de isolamento lógico (dependências)

### Clean Architecture (direção das dependências)

```text
┌───────────────────────────────┐
│ Frameworks & Drivers          │  React, Router, Supabase SDK, HTTP, DB, FS
└───────────────▲───────────────┘
                │ (depende de)
┌───────────────┴───────────────┐
│ Interface Adapters            │  controllers, presenters, repositories (impl)
└───────────────▲───────────────┘
                │ (depende de)
┌───────────────┴───────────────┐
│ Application (Use Cases)       │  orquestra regras e transações
│ + Ports (interfaces)          │  contratos para o “mundo externo”
└───────────────▲───────────────┘
                │ (depende de)
┌───────────────┴───────────────┐
│ Domain (Entities/VO/Policies) │  regras invariantes, sem I/O
└───────────────────────────────┘
```

Regra: **camadas internas não importam camadas externas**. Acesso a I/O sempre via portas (interfaces) definidas “para dentro”.

### Hexagonal (Ports & Adapters)

```text
        ┌───────────────┐
        │   UI / API     │  (adapter de entrada)
        └───────┬───────┘
                │ chama
        ┌───────▼───────┐
        │   Use Cases    │
        └───┬───────┬───┘
            │ usa   │ usa
     ┌──────▼───┐  ┌▼───────────┐
     │ Port A    │  │ Port B     │  (interfaces)
     └──────┬───┘  └──┬─────────┘
            │ impl     │ impl
   ┌────────▼───┐   ┌──▼────────┐
   │ Adapter A   │   │ Adapter B │  Supabase, HTTP, fila, etc.
   └─────────────┘   └───────────┘
```

Regra: adapters implementam portas; use cases só conhecem portas.

## Vertical Slicing (feature-based) — estrutura recomendada

Organize por feature (valor de negócio), não por tipo técnico global.

Estrutura base sugerida:

```text
src/
  app/                         (composition root: rotas, providers, wiring)
  shared/                      (cross-cutting: UI kit, utils, tipos genéricos)
  features/
    <feature>/
      index.ts                 (API pública da feature)
      ui/                      (componentes, páginas, rotas locais)
      application/             (use cases, DTOs, ports)
      domain/                  (entidades, VOs, regras)
      infra/                   (adapters/clients: supabase, http, storage)
      __tests__/               (opcional: testes por feature)
```

Regras práticas:

- `features/<feature>/ui` pode importar `application` e `domain`.
- `application` pode importar `domain` e `application/ports`.
- `infra` implementa `application/ports` e pode importar SDKs.
- `shared` não deve virar “depósito”; só aquilo que tem **uso real em 2+ features** e é estável.

## Como nomear e isolar contratos (Ports)

Coloque portas em `features/<feature>/application/ports`:

- `AuthRepository`, `InsumosRepository`, `AnalyticsGateway`
- portas orientadas ao caso de uso (contratos do que o app precisa), não ao detalhe do provedor

DTOs (entrada/saída) ficam em `application/dto` e não devem vazar tipos do provedor (ex.: tipos do Supabase).

## Adapters (Infra)

Implementações ficam em `features/<feature>/infra`:

- `supabaseAuthRepository.ts` implementa `AuthRepository`
- `supabaseInsumosRepository.ts` implementa `InsumosRepository`

Regra: se o arquivo importa SDK externo, ele provavelmente é “infra”.

## UI e Camada de Interface

UI é onde ficam:

- componentes React, páginas, rotas
- estado de tela e formatação de dados para apresentação

UI não contém regra de negócio central. Se aparecer regra que “decide”, “valida”, “calcula” ou “garante invariantes”, mova para `domain` ou `application`.

## Public API por feature (evitar imports profundos)

Cada feature expõe apenas o que é necessário via `features/<feature>/index.ts`.

Regra: preferir:

- `import { createFoo } from "@/features/foo"`

Evitar:

- `import { createFoo } from "@/features/foo/application/useCases/createFoo"`

Isso reduz acoplamento e permite reorganizar internamente sem quebrar consumidores.

## Heurísticas para decidir “isolar ou compartilhar”

Isolar (fica na feature) quando:

- tem regras específicas do domínio daquela feature,
- muda com frequência junto daquela feature,
- tem dependência forte do contexto (campos, nomes, validações).

Compartilhar (vai para `shared`) quando:

- é utilitário puro e genérico,
- é um componente UI realmente reutilizável,
- é um tipo transversal (ex.: `Result<T>`, `NonEmptyString`),
- existe 2+ features usando e existe expectativa real de reuso contínuo.

## Antipadrões a evitar

- “pasta services global” com tudo misturado (vira acoplamento implícito).
- tipos do DB/SDK vazando para `domain` e `application`.
- hooks com regra de negócio grande (hooks devem orquestrar UI e chamar use cases).
- circular dependencies entre features (quase sempre sinal de boundaries errados).

## Checklist rápido (antes de concluir uma mudança)

- A regra de dependências aponta para dentro (UI/infra dependem de application/domain).
- Integrações externas só aparecem em `infra`.
- A feature expõe uma API pública (index) e consumidores não importam internamente.
- `shared` não cresceu sem justificativa de reuso.

