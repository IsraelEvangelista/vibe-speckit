---
name: "openclaw-secure-agent"
description: "Implementa OpenClaw (gateway + channels + workspace) com hardening. Invoke quando instalar/configurar OpenClaw, expor remoto (Tailscale/proxy) ou auditar riscos."
---

# OpenClaw (Secure & Scalable)

## Objetivo

Projetar, instalar e operar uma instância do OpenClaw de forma segura e escalável (multi‑canal, multi‑modelo e multi‑agente), com foco em:

- reduzir superfícies de ataque (rede, UI, plugins, ferramentas, browser, nós remotos)
- evitar vazamento de contexto entre pessoas/canais (isolamento de sessões)
- limitar blast radius de prompt injection e automações (sandbox + políticas)
- garantir observabilidade e resposta a incidentes (audit, logs, rotação)

Esta skill foi escrita para ser “global” (TRAE, Claude, Gemini, Kimi, Codex): ela descreve decisões, checks, padrões e artefatos reusáveis sem depender de uma IDE específica.

## Quando invocar

- Você precisa instalar/configurar o OpenClaw em laptop, homelab ou VPS
- Você quer expor a Control UI remotamente com segurança (Tailscale Serve / proxy)
- Você vai conectar WhatsApp/Telegram/Discord/iMessage e precisa “lockdown”
- Você quer rodar tarefas com ferramentas (exec/browser/web) sem se expor a prompt injection
- Você vai rodar múltiplas pessoas usando o mesmo bot (multi‑user) e precisa isolamento
- Você quer integrar OpenClaw com AgentKit‑Pro (catálogo de skills/MCP) sem ampliar risco

## Referências oficiais (leitura obrigatória)

- Docs: https://docs.openclaw.ai/
- Getting started / quickstart: https://docs.openclaw.ai/start/quickstart
- “Personal assistant setup” (setup recomendado): https://docs.openclaw.ai/start/openclaw
- Security (threat model + hardening + audit): https://docs.openclaw.ai/gateway/security
- Formal verification (modelos de segurança): https://docs.openclaw.ai/security/formal-verification
- Security policy (Node mínimo + postura): https://github.com/openclaw/openclaw/security
- Advisory (exemplo real de 1‑click RCE/roubo de token via Control UI): https://github.com/openclaw/openclaw/security/advisories/GHSA-g8p2-7wf7-98mq

## Modelo mental (o que é “OpenClaw” na prática)

OpenClaw é um gateway auto‑hospedado que:

- recebe mensagens de canais (WhatsApp/Telegram/Discord/iMessage/etc.)
- roteia mensagens para um “agente” (Pi) com sessões e memória em disco
- opcionalmente habilita ferramentas (exec, browser, web fetch/search, nós remotos)
- publica respostas de volta aos canais

O gateway é a “single source of truth” para sessões, roteamento e conexões.

### Onde ficam os riscos principais

- Ingress: quem consegue mandar mensagem para o bot (DMs, grupos, guilds)
- Egress: para onde o bot consegue enviar mensagens e anexos
- Ferramentas: shell, filesystem, browser e web são caminhos diretos para dano/exfiltração
- Rede: bind em LAN/internet, reverse proxy mal configurado, headers spoofáveis
- Control UI: token, device auth, phishing/link hijack, superfícies de admin
- Plugins: código de terceiros roda in‑process (equivalente a “rodar npm install em produção”)
- Disco: logs/transcritos e credenciais vivem em ~/.openclaw (ou $OPENCLAW_STATE_DIR)

## Hardening: checklist resumido (baseline)

1) **Runtime seguro**
- Node.js **22.12.0+** (LTS), por conter patches de segurança relevantes (ver security policy).

2) **Acesso: “access control before intelligence”**
- Nunca rode com DMs “open” por padrão.
- Use **dmPolicy: "pairing"** ou **"allowlist"**.
- Em grupos, prefira **requireMention: true** (bot “não é always‑on”).
- Considere **session.dmScope: "per-channel-peer"** quando multi‑user.

3) **Rede**
- gateway.bind: **"loopback"** como padrão.
- Remoto: prefira **Tailscale Serve** em vez de bind “lan”.
- Reverse proxy: configure **gateway.trustedProxies**; proxy deve sobrescrever X‑Forwarded‑For.

4) **Control UI**
- Evite “insecure auth” e “dangerouslyDisableDeviceAuth”.
- Mantenha OpenClaw atualizado e trate tokens como credencial de admin.
- Atenção ao advisory GHSA‑g8p2‑7wf7‑98mq: token pode vazar via link malicioso se UI estiver logada (mitigado em versões corrigidas).

5) **Ferramentas / sandbox**
- Use sandboxing para exec/browser sempre que possível.
- Mantenha **tools.elevated** restrito (ou desabilitado) e com allowFrom pequeno.
- Não habilite browser control em perfil do seu navegador “daily‑driver”.

6) **Plugins**
- Só instale plugins que você auditou; prefira allowlist explícita.

7) **Disco (trust boundary)**
- Permissões: ~/.openclaw **700**, config **600**, credenciais e sessões não podem ser world-readable.
- Logs/transcritos podem conter dados sensíveis: redaction e retenção importam.

8) **Auditoria contínua**
- Rode `openclaw security audit` / `--deep` e aplique `--fix` como guardrail.

## Configuração segura (copy/paste baseline)

Use como ponto de partida e personalize com seus IDs/allowlists.

```jsonc
{
  "gateway": {
    "mode": "local",
    "bind": "loopback",
    "port": 18789,
    "auth": { "mode": "token", "token": "${OPENCLAW_GATEWAY_TOKEN}" }
  },
  "channels": {
    "whatsapp": {
      "dmPolicy": "pairing",
      "allowFrom": ["+15555550123"],
      "groups": { "*": { "requireMention": true } }
    }
  },
  "routing": {
    "groupChat": { "mentionPatterns": ["@openclaw", "openclaw"] }
  },
  "session": {
    "scope": "per-sender",
    "dmScope": "per-channel-peer",
    "resetTriggers": ["/new", "/reset"]
  },
  "agents": {
    "defaults": {
      "heartbeat": { "every": "0m" }
    }
  }
}
```

Notas:
- `dmPolicy: "pairing"` cria um handshake para desconhecidos; o bot ignora até aprovação.
- `requireMention: true` reduz risco de prompt injection em grupos por “drive‑by messages”.
- `dmScope: "per-channel-peer"` evita que DMs de pessoas diferentes colapsem em um único contexto.
- `heartbeat.every: "0m"` começa seguro; habilite quando confiar na operação.

## Pesquisa profunda de segurança (o que documentar sempre)

Quando você aplicar esta skill, capture e registre (em docs internos) pelo menos:

### 1) Superfícies de ataque e controles existentes
- Quais canais estão conectados (WhatsApp/Telegram/Discord/etc.) e quais políticas (dmPolicy, allowFrom, groupPolicy, requireMention).
- Como a Control UI está exposta (localhost, tailnet, proxy, túnel).
- Quais ferramentas estão habilitadas (exec, browser, web_fetch/search, nodes.run, cron).
- Se há sandboxing e qual o escopo (por agente/sessão/compartilhado) e workspaceAccess (none/ro/rw).
- Se plugins estão habilitados e de onde vieram (npm vs path local).

### 2) Credenciais e dados sensíveis (onde vivem)
Mapeie caminhos/locais de armazenamento para backup/auditoria (exemplos citados nos docs):

- WhatsApp creds: `~/.openclaw/credentials/whatsapp/<accountId>/creds.json`
- Pairing allowlists: `~/.openclaw/credentials/<channel>-allowFrom.json`
- Model auth profiles: `~/.openclaw/agents/<agentId>/agent/auth-profiles.json`
- Sessões/transcritos: `~/.openclaw/agents/<agentId>/sessions/*.jsonl`

Regra: trate esses arquivos como segredos/dados pessoais. Permissões e criptografia de disco são parte do modelo de segurança.

### 3) Riscos reais já vistos “no mundo”

- **Roubo de token / 1‑click RCE via Control UI**: houve um advisory oficial em que a UI confiava em `gatewayUrl` vindo da query string e auto‑conectava, enviando o token armazenado no payload do WebSocket. Um link/página maliciosa podia exfiltrar o token e então o atacante obteria acesso de operador ao gateway, incluindo mudanças de config e execução. Mitigação: manter OpenClaw atualizado, revisar/confirmar mudanças de gateway URL na UI, e tratar sessão logada na Control UI como “credencial ativa” (evite navegar em sites não confiáveis com a UI logada). Referência: GHSA‑g8p2‑7wf7‑98mq.

### 4) Prompt injection: o risco “normal” que vira incidente

Mesmo com allowlists, prompt injection pode vir de:
- links/URLs (web_fetch/search)
- páginas controladas via browser
- emails/attachments/arquivos que o agente lê

Contenção recomendada:
- usar um agente “reader” com ferramentas desabilitadas para resumir conteúdo não confiável
- manter web_search/web_fetch/browser off por padrão em agentes com exec habilitado
- sandbox + denylist de ferramentas para contextos não‑owner

### 5) Verificação formal (quando precisar máxima confiança)

O OpenClaw mantém modelos formais (TLA+/TLC) para claims de segurança como:
- exposição do gateway
- pipeline nodes.run
- pairing store
- ingress gating (mention)
- isolamento de sessões e precedence

Use esses modelos como regressão de política (não como prova total do runtime).

## Integração segura com AgentKit‑Pro (sem ampliar blast radius)

O AgentKit‑Pro já expõe um MCP (token por usuário) para listar e carregar skills.
Integração recomendada com OpenClaw:

1) Trate o token do MCP como segredo (nunca em chat/logs).
2) Use um “helper” local que faz chamadas HTTP ao MCP e retorna JSON mínimo.
3) Rode esse helper dentro do sandbox do OpenClaw (ou com workspaceAccess ro) quando possível.

Artefatos prontos neste repositório:

- `scripts/openclaw/agentkit-mcp-cli.js` (CLI local para consumir o MCP do AgentKit‑Pro)
- `scripts/openclaw/openclaw-skill-agentkit-pro/SKILL.md` (template de skill do OpenClaw que chama o helper)

## Blocos de prompt (compatíveis com TRAE/Claude/Gemini/Kimi/Codex)

### Bloco A — “Setup seguro”
Cole quando o objetivo for instalar/configurar:

```text
Você vai configurar OpenClaw com foco em segurança. Regras:
1) gateway.bind=loopback; remoto somente via Tailscale Serve; nunca expor publicamente.
2) DMs: dmPolicy=pairing ou allowlist; grupos: requireMention=true.
3) Isolar DMs com session.dmScope=per-channel-peer em setups multi-usuário.
4) Habilitar sandbox para exec/browser; tools.elevated somente para owner.
5) Rodar openclaw security audit (--deep) e corrigir findings críticos.
6) Não registrar/printar tokens, credenciais ou conteúdo de ~/.openclaw em logs/chat.
Entregue: openclaw.json baseline + runbook de rotação + checklist de incident response.
```

### Bloco B — “Auditoria”
Cole quando o objetivo for auditoria/hardening:

```text
Faça um threat model do OpenClaw: ingress (DM/grupos), egress, ferramentas, rede, Control UI, plugins e disco.
Identifique configurações de alto risco e proponha mitigação com prioridade.
Inclua medidas contra: prompt injection, token theft (Control UI), reverse proxy spoofing, sessão multi-user leak, browser takeover.
```

## Critérios de pronto (DoD)

- Config baseline documentada e revisada contra o security doc oficial
- Checklist de hardening + incident response anexados
- Integração com AgentKit‑Pro feita via helper local (sem tokens em chat)
- Tudo versionado sem segredos (sem `.env`, tokens, creds, dumps de sessão)
