# Description
O Uazapi é uma API de integração com WhatsApp baseada em instâncias. Cada instância representa uma sessão WhatsApp conectada a um número de telefone.

# Uazapi GO v2 - Documentação de Integração WhatsApp
> **Versão**: Uazapi GO v2.0
> **Documentação Oficial**: https://docs.uazapi.com
> **Última Atualização**: 2026-01-06
---
## 📋 Visão Geral
O Uazapi é uma API de integração com WhatsApp baseada em instâncias. Cada instância representa uma sessão WhatsApp conectada a um número de telefone.
### Arquitetura
```
[Aplicação] → [Uazapi API] → [WhatsApp Web] → [Dispositivo]
```
### Principais Conceitos
| Conceito | Descrição |
|----------|-----------|
| **Instância** | Uma sessão WhatsApp única vinculada a um número |
| **Instance Token** | Token de autenticação específico por instância |
| **QR Code** | Código para pareamento com o WhatsApp |
| **Pairing Code** | Código numérico alternativo ao QR Code |
| **JID** | Identificador WhatsApp (ex: `5511999999999@s.whatsapp.net`) |
---
## 🔐 Autenticação
O Uazapi usa autenticação via header `token` (lowercase, não `apikey` ou `Authorization Bearer`).
### Headers Obrigatórios
```http
Content-Type: application/json
token: {INSTANCE_TOKEN}
```
### Variáveis de Ambiente
```env
# URL base da API Uazapi (sem barra final)
VITE_UAZAPI_URL=https://agilenebula.uazapi.com
# Token específico da instância (obtido no painel)
VITE_UAZAPI_INSTANCE_TOKEN=e1b723fa-be79-4aa6-9d9b-66911ae97fed
```
> ⚠️ **IMPORTANTE**: O token de instância é sensível. Nunca exponha em código versionado.
---
## 📡 Endpoints Principais
### 1. Conectar Instância (Gerar QR Code)
```http
POST /instance/connect
```
**Resposta Sucesso (200)**:
```json
{
  "instance": {
    "id": "rd1ce6c5ad20af3",
    "token": "e1b723fa-be79-4aa6-9d9b-66911ae97fed",
    "status": "connecting",
    "qrcode": "2@gR8m1W6q...",
    "paircode": "7K2M-4X9P",
    "name": "rakel-pediatria",
    "profileName": null,
    "profilePicUrl": null,
    "isBusiness": false,
    "owner": null
  }
}
```
**Campos Relevantes**:
- `instance.qrcode`: String para gerar imagem QR Code
- `instance.paircode`: Código de 8 dígitos para pareamento manual
---
### 2. Verificar Status da Instância
```http
GET /instance/status
```
**Resposta Sucesso (200) - Conectado**:
```json
{
  "instance": {
    "id": "rd1ce6c5ad20af3",
    "token": "e1b723fa-be79-4aa6-9d9b-66911ae97fed",
    "status": "connected",
    "paircode": "",
    "qrcode": "",
    "name": "rakel-pediatria",
    "profileName": "Nébula",
    "profilePicUrl": "https://pps.whatsapp.net/...",
    "isBusiness": false,
    "plataform": "android",
    "systemName": "agilenebula",
    "owner": "558588199740",
    "current_presence": "available",
    "lastDisconnect": "2026-01-06 01:50:05.635Z",
    "lastDisconnectReason": "QR Code timeout",
    "chatbot_enabled": false,
    "created": "2026-01-06 00:35:21.825Z",
    "updated": "2026-01-06 00:35:21.825Z",
    "currentTime": "2026-01-06 02:11:41.554Z"
  },
  "status": {
    "connected": true,
    "jid": "558588199740:11@s.whatsapp.net",
    "loggedIn": true
  }
}
```
**Mapeamento de Status**:
| Valor `instance.status` | Estado | Descrição |
|-------------------------|--------|-----------|
| `connected` | ✅ Conectado | WhatsApp conectado e operacional |
| `connecting` | 🔄 Sincronizando | Aguardando escaneamento do QR |
| `disconnected` | ❌ Desconectado | Sem conexão ativa |
| `qrcode` | 📱 QR Code | QR Code disponível para escanear |
**Extração do Número de Telefone**:
```typescript
// Prioridade de extração:
// 1. instance.owner (número puro: "558588199740")
// 2. status.jid (formato JID: "558588199740:11@s.whatsapp.net")
// 3. instance.jid
// 4. data.jid
const phone = instance.owner 
  || (typeof data.status === 'object' && data.status?.jid?.split('@')[0])
  || instance.jid?.split('@')[0]
  || data.jid?.split('@')[0];
```
---
### 3. Desconectar Instância
```http
POST /instance/disconnect
```
> **IMPORTANTE**: O endpoint correto para a API v2 é `/instance/disconnect`, NÃO `/instance/logout`.

**Resposta Sucesso (200)**:
```json
{
  "status": "success",
  "message": "Disconnected successfully"
}
```
---
### 4. Endpoints de Mensagens
#### Enviar Mensagem de Texto
```http
POST /message/text/{instanceName}
```
**Body**:
```json
{
  "number": "5511999999999",
  "message": "Olá, sua consulta está confirmada para amanhã às 14h."
}
```
#### Enviar Mídia (Imagem/Documento/Áudio)
```http
POST /message/media/{instanceName}
```
**Body**:
```json
{
  "number": "5511999999999",
  "media": "data:image/png;base64,...",
  "caption": "Resultado do exame",
  "fileName": "exame.pdf"
}
```
---
## 🏗️ Estrutura de Dados
### Interface InstanceData
```typescript
export interface InstanceData {
  id?: string;
  token?: string;
  status?: string;      // 'connected' | 'connecting' | 'disconnected' | 'qrcode'
  state?: string;
  paircode?: string;
  qrcode?: string;
  name?: string;        // Nome da instância
  profileName?: string; // Nome do perfil WhatsApp
  profilePicUrl?: string;
  isBusiness?: boolean;
  jid?: string;
  owner?: string;       // Número de telefone puro (ex: "558588199740")
  plataform?: string;   // 'android' | 'ios' | 'web'
  current_presence?: string; // 'available' | 'unavailable'
  lastDisconnect?: string;
  lastDisconnectReason?: string;
}
```
### Interface StatusResponse
```typescript
export interface StatusResponse {
  connected?: boolean;
  loggedIn?: boolean;
  jid?: string;  // ex: "558588199740:11@s.whatsapp.net"
  instance?: InstanceData;
  status?: StatusObject | string;
  state?: string;
  qrcode?: string;
}
export interface StatusObject {
  connected?: boolean;
  jid?: string;
  loggedIn?: boolean;
  state?: string;
  status?: string;
}
```
### Interface ProfileData (UI)
```typescript
export interface ProfileData {
  name: string;        // profileName ou name
  phoneNumber: string; // Formatado: "+55 85 88199-740"
  profilePicUrl?: string;
  isBusiness: boolean;
}
```
---
## 🔧 Funções Utilitárias
### Formatação de Número de Telefone
```typescript
/**
 * Converte JID para formato de telefone brasileiro
 * Ex: "558588199740@s.whatsapp.net" → "+55 85 88199-740"
 */
export const formatPhoneFromJid = (jid: string | undefined): string => {
  if (!jid) return 'Não disponível';
  
  const phoneRaw = jid.split('@')[0].split(':')[0];
  
  if (phoneRaw.startsWith('55') && phoneRaw.length >= 12) {
    const country = phoneRaw.substring(0, 2);
    const ddd = phoneRaw.substring(2, 4);
    const part1 = phoneRaw.substring(4, 9);
    const part2 = phoneRaw.substring(9);
    return `+${country} ${ddd} ${part1}-${part2}`;
  }
  
  return `+${phoneRaw}`;
};
```
### Mascaramento de Dados Sensíveis
```typescript
/**
 * Mascara dados sensíveis para exibição na UI
 * Ex: "abc123xyz" → "abc***xyz"
 */
export const maskSensitiveData = (
  data: string | undefined, 
  visibleChars: number = 3
): string => {
  if (!data) return '';
  if (data.length <= visibleChars * 2) return '*'.repeat(data.length);
  
  const start = data.substring(0, visibleChars);
  const end = data.substring(data.length - visibleChars);
  const middle = '*'.repeat(Math.min(data.length - visibleChars * 2, 10));
  
  return `${start}${middle}${end}`;
};
```
### Extração de Dados do Perfil
```typescript
/**
 * Extrai dados do perfil conectado para exibição na UI
 */
export const extractProfileData = (
  data: StatusResponse | ConnectResponse | null
): ProfileData | null => {
  if (!data?.instance) return null;
  
  const instance = data.instance;
  
  // Prioridade: owner > status.jid > instance.jid > data.jid
  let phoneSource: string | undefined;
  
  if (instance.owner) {
    phoneSource = instance.owner;
  } else if (typeof data.status === 'object' && data.status && 'jid' in data.status) {
    phoneSource = data.status.jid;
  } else if (instance.jid) {
    phoneSource = instance.jid;
  } else if (data.jid) {
    phoneSource = data.jid;
  }
  
  return {
    name: instance.profileName || instance.name || 'Perfil WhatsApp',
    phoneNumber: formatPhoneFromJid(phoneSource),
    profilePicUrl: instance.profilePicUrl,
    isBusiness: instance.isBusiness || false
  };
};
```
---
## 🖥️ Implementação no MedCheck
### Arquivos Principais
| Arquivo | Descrição |
|---------|-----------|
| `src/services/uazapiService.ts` | Serviço com chamadas à API |
| `src/hooks/useUazapi.ts` | Hook React para estado e ações |
| `src/components/administration/UazapiTab.tsx` | Componente de UI |
### Hook useUazapi
```typescript
interface UseUazapiReturn {
  qrCode: string | null;
  pairingCode: string | null;
  connectionState: ConnectionState; // 'disconnected' | 'connecting' | 'connected'
  status: SessionStatus;            // 'CONNECTED' | 'DISCONNECTED' | 'QRCODE_READY' | etc
  isLoading: boolean;
  error: string | null;
  profile: ProfileData | null;      // Dados do perfil conectado
  isConfigured: boolean;
  refetchQr: () => Promise<void>;   // Solicitar novo QR Code
  disconnect: () => Promise<void>;  // Desconectar instância
}
```
### Estados da UI
1. **Desconectado**: Botão "Sincronizar WhatsApp"
2. **Sincronizando**: QR Code + Instruções + Pairing Code
3. **Conectado**: Avatar + Nome + Telefone + Badge Business
---
## ⚠️ Tratamento de Erros
### Códigos HTTP
| Código | Significado | Ação |
|--------|-------------|------|
| 200 | Sucesso | Processar resposta |
| 401 | Não autorizado | Verificar token |
| 404 | Endpoint não encontrado | Verificar URL |
| 500 | Erro interno | Retry ou reportar |
### Erros Comuns
1. **"Invalid token"**: Token de instância incorreto ou expirado
2. **"Instance not found"**: Instância não existe no servidor
3. **"QR Code timeout"**: QR expirou sem ser escaneado (gerar novo)
4. **"Already connected"**: Já existe sessão ativa
---
## 🔜 Features Futuras (Não Implementadas)
### Estatísticas de Contatos/Chats
A API Uazapi **não retorna estatísticas** no endpoint `/instance/status`. Para obter:
- **Contatos**: `GET /contacts` → conta total
- **Chats**: `GET /chats` → conta total
- **Mensagens**: Não disponível diretamente
### Webhooks
Configurar webhooks para receber eventos em tempo real:
```http
POST /webhook/set
```
```json
{
  "url": "https://seu-dominio.com/webhook/whatsapp",
  "events": ["messages", "status", "connection"]
}
```
---
## 📚 Referências
- [Documentação Oficial Uazapi](https://docs.uazapi.com)
- [Endpoint /instance/connect](https://docs.uazapi.com/endpoint/post/instance~connect)
- [Endpoint /instance/status](https://docs.uazapi.com/endpoint/get/instance~status)
- [Endpoint /instance/logout](https://docs.uazapi.com/endpoint/post/instance~logout)
- [Tag Administração](https://docs.uazapi.com/tag/Administração)
---
## 🧪 Debugging
### Logs no Console
O serviço emite logs prefixados com `[uazapiService v2]`:
```
[uazapiService v2] Configuração: {API_URL, instanceName, hasToken}
[uazapiService v2] Verificando status...
[uazapiService v2] Status recebido: {resposta JSON}
[uazapiService v2] Status parseado: CONNECTED
```
### Verificação Manual no DevTools
1. Abrir Network tab
2. Filtrar por `/instance/`
3. Verificar Response para estrutura real da API
---
*Documento criado para o projeto MedCheck - Integração WhatsApp via Uazapi GO v2*
