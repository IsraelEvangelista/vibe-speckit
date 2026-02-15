---
name: gotenberg-pdf-generation
description: "Guia de integração e implementação do Gotenberg para geração de PDFs"
---

# Integração Gotenberg (PDF Generation)

Este workflow descreve como configurar e utilizar o Gotenberg para geração de arquivos PDF a partir de HTML/Chromium, integrando-o ao backend (Supabase Edge Functions).

## 1. Setup do Serviço (Gotenberg)
A maneira recomendada de rodar o Gotenberg é via Docker.

### 1.1 Docker Compose
Adicione ao seu `docker-compose.yml` (se existir) ou rode isoladamente:
```yaml
version: '3'
services:
  gotenberg:
    image: gotenberg/gotenberg:8
    ports:
      - "3000:3000"
    command:
      - "gotenberg"
      - "--api-port=3000"
      - "--api-port-from-env"
```

## 2. API Edge Function Integration
Para comunicar com o Gotenberg via Edge Function (Deno), utilize `multipart/form-data`.

### 2.1 Exemplo de Função Deno
Crie uma função helper para montar o FormData:

```typescript
// utils/gotenberg.ts

export async function generatePdfFromHtml(html: string, gotenbergUrl: string) {
  const formData = new FormData();
  
  // HTML principal
  const blob = new Blob([html], { type: "text/html" });
  formData.append("files", blob, "index.html");

  // Configurações opcionais (margens, formato de papel)
  formData.append("marginTop", "0");
  formData.append("marginBottom", "0");
  formData.append("marginLeft", "0");
  formData.append("marginRight", "0");
  formData.append("paperWidth", "8.27");  // A4
  formData.append("paperHeight", "11.7"); // A4

  try {
    const response = await fetch(`${gotenbergUrl}/forms/chromium/convert/html`, {
      method: "POST",
      body: formData,
    });

    if (!response.ok) {
      throw new Error(`Gotenberg error: ${response.statusText}`);
    }

    return await response.arrayBuffer(); // Retorna o binário do PDF
  } catch (error) {
    console.error("PDF Generation failed:", error);
    throw error;
  }
}
```

## 3. Uso no Canvas/Frontend
1. O Frontend envia os dados (JSON) para a Edge Function.
2. A Edge Function renderiza o template HTML (usando Handlebars ou string template).
3. A Edge Function chama `generatePdfFromHtml`.
4. A Edge Function retorna o binário com `Content-Type: application/pdf`.

## 4. Debug e Health Check
- Verifique se o serviço está up: `GET http://localhost:3000/health`
- Em produção, configure a variável de ambiente `GOTENBERG_URL` apontando para o serviço hospedado (ex: Railway, Fly.io ou VPS).

## 5. Boas Práticas
- Use **assets embarcados** (data:image/png;base64) no HTML para evitar problemas de rede no container do Gotenberg.
- Defina margens zero no Gotenberg e controle o layout via CSS `@page` no HTML.
