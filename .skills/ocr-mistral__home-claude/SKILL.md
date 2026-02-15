---
name: ocr-mistral
description: "Guia de integração com a API Mistral OCR para análise de documentos"
---

# Mistral OCR Integration

Este workflow fornece o padrão para utilizar o Mistral OCR API para extrair texto estruturado (Markdown) e analisar documentos (PDFs, Imagens) no contexto do Assistente IA.

## 1. Pré-requisitos
- Chave de API da Mistral (`MISTRAL_API_KEY`) configurada no `.env`.
- Biblioteca cliente ou `fetch` padrão.

## 2. Fluxo de Processamento (Edge Function)

### 2.1 Upload e URL
O Mistral OCR funciona melhor com URLs acessíveis ou base64. No Supabase:
1. O usuário faz upload do arquivo para o bucket `ai-uploads`.
2. A Edge Function gera uma **Signed URL** temporária (ex: 5 minutos).

### 2.2 Chamada à API Mistral OCR
Utilize o endpoint de OCR (ou multimodal chat se for analysis-on-the-fly).

**Endpoint Sugerido**: `https://api.mistral.ai/v1/ocr` (conforme documentação oficial recente) ou via SDK.

Exemplo conceitual (REST):

```typescript
// utils/mistral-ocr.ts

export async function processDocumentWithMistral(fileUrl: string) {
  const payload = {
    model: "mistral-ocr-latest", // Confirme o nome do modelo na doc oficial
    document: {
      url: fileUrl,
      type: "pdf" // ou 'image_url' dependendo do formato
    },
    include_image_base64: false // Se precisar das imagens extraídas
  };

  const response = await fetch("https://api.mistral.ai/v1/ocr", {
    method: "POST",
    headers: {
      "Authorization": `Bearer ${Deno.env.get("MISTRAL_API_KEY")}`,
      "Content-Type": "application/json"
    },
    body: JSON.stringify(payload)
  });

  if (!response.ok) {
    throw new Error(`Mistral OCR Error: ${response.statusText}`);
  }

  const result = await response.json();
  return result.pages.map(p => p.markdown).join("\n\n"); // Retorna Markdown estruturado
}
```

## 3. Armazenamento e RAG
1. **Output**: O Mistral OCR retorna markdown rico (tabelas, headers).
2. **Chunking**: Utilize um splitter de markdown (ex: `MarkdownTextSplitter` do LangChain) para quebrar o resultado respeitando a estrutura.
3. **Vetorização**: Gere embeddings dos chunks e armazene na tabela `ai_documents`.

## 4. Análise Direta (Sem RAG)
Para "Conversar com o PDF" (Context Window permitindo):
1. Extraia o markdown completo via OCR.
2. Injete no `system` prompt ou primeira mensagem user:
   `"O usuário fez upload do seguinte documento:\n\n{markdown_content}\n\nResponda perguntas sobre ele."`
3. Use um modelo com janela de contexto grande (Mistral Large ou Small).

## 5. Custo e Performance
- O OCR é cobrado por página.
- Cacheie o resultado do OCR na tabela `ai_documents` para evitar reprocessamento do mesmo arquivo (hash MD5).
