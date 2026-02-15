---
name: kiwify-api
description: Esta skill capacita o agente a projetar, implementar e depurar integrações com a API Pública da Kiwify (versão 2026). Ela fornece protocolos estritos para autenticação OAuth 2.0, manipulação de Webhooks, gestão de Rate Limits (Leaky Bucket) e segurança de dados (LGPD). O agente deve priorizar arquiteturas baseadas em Node.js e middleware assíncrono.
---

# AGENT SKILL: Kiwify API Integration Architect (v2026)

## DESCRIPTION
Esta skill capacita o agente a projetar, implementar e depurar integrações com a API Pública da Kiwify (versão 2026). Ela fornece protocolos estritos para autenticação OAuth 2.0, manipulação de Webhooks, gestão de Rate Limits (Leaky Bucket) e segurança de dados (LGPD). O agente deve priorizar arquiteturas baseadas em Node.js e middleware assíncrono.

## CONTEXT & SCOPE
- [cite_start]**Base URL:** `https://public-api.kiwify.com` [cite: 9]
- [cite_start]**Protocolo:** HTTPS Obrigatório (TLS) [cite: 6, 10]
- [cite_start]**Formato de Dados:** JSON (UTF-8) [cite: 11]
- [cite_start]**Arquitetura:** RESTful stateless [cite: 5]
- **Uso:** Automação de vendas, gestão de alunos, dashboards financeiros e áreas de membros personalizadas.

## CRITICAL PROCEDURES

### 1. Protocolo de Autenticação e Segurança (OAuth 2.0)
[cite_start]O agente deve implementar o ciclo de vida de tokens para evitar interrupções, dado que o `client_secret` rotaciona a cada 96 horas[cite: 34].

* [cite_start]**Credenciais:** Obter `client_id`, `client_secret` e `account_id` no Dashboard Kiwify[cite: 29].
* **Geração de Token:**
    * POST endpoint: `/v1/oauth/token` (implícito na doc de auth)
    * [cite_start]Header: `Content-Type: application/x-www-form-urlencoded` [cite: 38]
    * Body: `client_id={ID}&client_secret={SECRET}`
    * [cite_start]Output: Armazenar `access_token` (validade ~24h) em cache seguro (ex: Redis)[cite: 32, 40].
* **Requisições Autenticadas:**
    Todas as chamadas subsequentes DEVEM conter os headers:
    1.  `Authorization: Bearer {ACCESS_TOKEN}` [cite: 42]
    2.  `x-kiwify-account-id: {ACCOUNT_ID}` [cite: 43]

### 2. Arquitetura de Webhooks (Event-Driven)
[cite_start]O agente deve tratar webhooks como a fonte primária de verdade para eventos em tempo real, evitando polling excessivo[cite: 45].

* **Configuração de Endpoint:** O servidor receptor (Node.js/Express) deve aceitar POST requests.
* **Validação de Segurança:**
    * [cite_start]Verificar o `signature token` enviado no payload contra o token definido na criação do webhook[cite: 60, 61].
* **Fluxo de Processamento:**
    1.  Receber o payload.
    2.  Validar o token.
    3.  [cite_start]**Responder `200 OK` IMEDIATAMENTE** para evitar retentativas da Kiwify[cite: 62].
    4.  [cite_start]Enfileirar o processamento (Queue/Job) para execução assíncrona (evitar timeout)[cite: 63, 73].
* **Mapeamento de Triggers Críticos:**
    * [cite_start]`compra_aprovada`: Liberar acesso/entregar produto[cite: 52].
    * [cite_start]`subscription_renewed`: Manter acesso (LTV)[cite: 56, 58].
    * [cite_start]`chargeback`: Disparar alerta jurídico[cite: 55].

### 3. Gestão de Rate Limits (Resiliência)
[cite_start]A API utiliza o algoritmo "Leaky Bucket"[cite: 92]. O agente deve instruir a implementação de resiliência.

* [cite_start]**Monitoramento:** Observar headers de limite (se disponíveis) e o status `429 Too Many Requests`[cite: 23].
* **Estratégia de Backoff:**
    * [cite_start]Se receber `429`, aplicar **Exponential Backoff** (espera progressiva) antes de tentar novamente[cite: 27].
    * [cite_start]Implementar filas de requisição (Throttling) para respeitar a "taxa de vazamento" do balde[cite: 96, 97].

### 4. Endpoints Essenciais e Consultas
O agente deve saber consultar recursos para enriquecimento de dados.

* [cite_start]**Vendas (`GET /v1/sales/{id}`):** Recuperar metadados, UTMs e dados do comprador[cite: 65].
* [cite_start]**Participantes (`GET /v1/events/{product_id}/participants`):** Validar acesso à área de membros externa[cite: 69].
    * *Nota:* Paginação é obrigatória para grandes volumes[cite: 66].

## CODING STANDARDS (Node.js Focus)
[cite_start][cite: 71, 72]

1.  **Estrutura de Middleware:** Usar Express.js para rotas de webhook.
2.  **Segurança de Chaves:** NUNCA hardcoded. Usar variáveis de ambiente (`.env`)[cite: 76].
3.  **Tratamento de Erros Semânticos:**
    * [cite_start]`400`: Erro de sintaxe (não reenviar sem correção)[cite: 19].
    * [cite_start]`401`: Token expirado (forçar refresh do token e reenviar)[cite: 20].
    * `5xx`: Erro no servidor Kiwify (tentar novamente mais tarde)[cite: 24].

## REFERENCE DATA: HTTP STATUS CODES
- `200`: Sucesso (Payload no corpo)[cite: 17].
- `201`: Recurso Criado[cite: 18].
- `401`: Unauthorized (Token inválido/ausente)[cite: 20].
- `403`: Forbidden (Sem permissão de escopo)[cite: 21].
- `429`: Rate Limit Exceeded (Leaky Bucket cheio)[cite: 23].