# Uazapi Media Sending - Envio de Mídias (Áudio, Imagens, Documentos, Vídeos)

> **Skill Category**: Integration Specialist
> **Documentation**: `.trae/documents/uazapi-media-sending-guide.md`
> **Last Updated**: 31/01/2026
> **API Version**: uazapiGO v2.0

## Overview

Esta skill especializada fornece conhecimento completo sobre envio de mídias (áudio, imagens, documentos e vídeos) através da API Uazapi/WhatsApp Business API para o projeto FinManage.

## When to Use

Use this skill when:

- Implementing or debugging media sending via Uazapi API (audio, image, document, video)
- Choosing the right method for sending media (URL, Base64, upload local, media ID)
- Handling errors and rate limits for WhatsApp media messages
- Implementing media upload in frontend (React/Vite) or backend (Edge Functions)
- Troubleshooting WhatsApp media sending issues

## Core Knowledge

### Media Types Supported

| Type | Formats | Max Size | Notes |
|--------|---------|----------|-------|
| **Images** | JPEG, PNG | 5 MB (WhatsApp limit) | Supports HD (high-resolution) |
| **Documents** | PDF, DOCX, XLSX, etc. | 2 GB | Any document format |
| **Videos** | .3gp, .mp4 (H.264) | 100 MB | MP4 requires H.264 codec |
| **Audio** | MP3, OGG, AAC | 16 MB (estimated) | For audio messages |
| **Voice Notes** | OGG (Opus codec required) | 16 MB | PPT audio recording format |

### HD Image Feature

Uazapi supports sending **high-resolution (HD) images**. These images are automatically marked with the **HD** label in the recipient's WhatsApp interface.

This allows you to deliver images in maximum quality, similar to manually sending HD photos in the official WhatsApp application.

### Authentication

All regular API endpoints require a `token` header with the instance token:

```typescript
headers: {
  'Authorization': `Bearer ${instanceToken}`
}
```

Administrative endpoints require an `admintoken` header.

### Base URL

```typescript
const baseUrl = `https://${instanceSubdomain}.uazapi.com`;
```

## Available Endpoints

| Endpoint | Method | URL | Description |
|----------|--------|-----|-------------|
| Send Image | POST | `/messages/image` | Send image with optional caption |
| Send Document | POST | `/messages/document` | Send any document format |
| Send Audio | POST | `/messages/audio` | Send audio file |
| Send Video | POST | `/messages/video` | Send video file |
| Send Voice | POST | `/messages/voice` | Send voice recording (PPT/OGG) |

## Sending Methods

### Method 1: By URL (Direct Link)

Send media using a direct accessible URL. The URL must be publicly accessible on the internet.

**Use for:**
- Files already hosted (S3, Cloudinary, etc.)
- When you don't have file storage

**Do NOT use:**
- Local file paths or `localhost` URLs - API cannot access local systems

### Method 2: By Base64

Convert the file to Base64 string and send it. Useful for small files or when no upload system is available.

**Use for:**
- Dynamically generated files
- Screenshots or captures
- Small files (<10MB recommended)

**Important:** If using Base64 for audio/video before binary, add `data:` prefix:
- Images: `data:image/png;base64,{base64}`
- Audio: `data:audio/mp3;base64,{base64}`

### Method 3: Upload from Local (multipart/form-data)

Send the file as binary data using `multipart/form-data`. Most efficient method for large files.

**Use for:**
- Large files
- User uploads
- When you have file system access

**Important:** Do NOT send file paths - send actual file content as binary.

### Method 4: By Media ID (Reuse)

After uploading a file, you get a `media_id` that can be reused for future sends. Files are stored in Uazapi cloud for **30 days**.

**Use for:**
- Sending same file to multiple recipients
- Reducing bandwidth usage
- Processing resources optimization

**Expiration:** After 30 days, the `media_id` expires and can no longer be used.

## Implementation Examples

### TypeScript/Node.js

#### Send Image by URL

```typescript
import axios from 'axios';

interface SendImageByUrlParams {
  instanceToken: string;
  instanceSubdomain: string;
  phoneNumber: string;
  imageUrl: string;
  caption?: string;
}

async function sendImageByUrl({
  instanceToken,
  instanceSubdomain,
  phoneNumber,
  imageUrl,
  caption
}: SendImageByUrlParams) {
  const url = `https://${instanceSubdomain}.uazapi.com/messages/image`;
  
  const response = await axios.post(url, {
    to: phoneNumber,
    media: imageUrl,
    caption: caption || 'Confira esta imagem!',
  }, {
    headers: {
      'Authorization': `Bearer ${instanceToken}`
    }
  });
  
  return response.data;
}

// Usage
const result = await sendImageByUrl(
  'seu_token_aqui',
  'finmanage-001',
  '5511999988777',
  'https://exemplo.com/foto.jpg',
  'Veja este relatório financeiro!'
);
```

#### Send Image by Base64

```typescript
import fs from 'fs';
import axios from 'axios';

interface SendImageByBase64Params {
  instanceToken: string;
  instanceSubdomain: string;
  phoneNumber: string;
  imagePath: string;
  caption?: string;
}

async function sendImageByBase64({
  instanceToken,
  instanceSubdomain,
  phoneNumber,
  imagePath,
  caption
}: SendImageByBase64Params) {
  // Read local file
  const imageBuffer = fs.readFileSync(imagePath);
  
  // Convert to Base64
  const base64 = imageBuffer.toString('base64');
  
  // Detect MIME type automatically
  const mimeType = 'image/jpeg'; // or 'image/png'
  
  // Create data URL string
  const media = `data:${mimeType};name=image.jpg;base64,${base64}`;
  
  const url = `https://${instanceSubdomain}.uazapi.com/messages/image`;
  
  const response = await axios.post(url, {
    to: phoneNumber,
    media: media,
    caption: caption || 'Imagem anexada',
  }, {
    headers: {
      'Authorization': `Bearer ${instanceToken}`
    }
  });
  
  return response.data;
}

// Usage
const result = await sendImageByBase64(
  'seu_token_aqui',
  'finmanage-001',
  '5511999988777',
  './caminho/para/imagem.jpg',
  'Gráfico de despesas mensais'
);
```

#### Send Document (Upload Local)

```typescript
import FormData from 'form-data';
import fs from 'fs';
import axios from 'axios';

interface SendDocumentParams {
  instanceToken: string;
  instanceSubdomain: string;
  phoneNumber: string;
  documentPath: string;
  fileName?: string;
}

async function sendDocument({
  instanceToken,
  instanceSubdomain,
  phoneNumber,
  documentPath,
  fileName
}: SendDocumentParams) {
  const url = `https://${instanceSubdomain}.uazapi.com/messages/document`;
  const formData = new FormData();
  
  // Add binary file
  formData.append('to', phoneNumber);
  formData.append('media', fs.createReadStream(documentPath));
  formData.append('fileName', fileName || documentPath.split('/').pop());
  
  const response = await axios.post(url, formData, {
    headers: {
      'Authorization': `Bearer ${instanceToken}`,
      ...formData.getHeaders() // FormData adds Content-Type: multipart/form-data
    }
  });
  
  return response.data;
}

// Usage
const result = await sendDocument(
  'seu_token_aqui',
  'finmanage-001',
  '5511999988777',
  './relatorios/financeiro.pdf',
  'Relatorio_Financeiro_Janeiro.pdf'
);
```

#### Send Audio

```typescript
import axios from 'axios';

interface SendAudioParams {
  instanceToken: string;
  instanceSubdomain: string;
  phoneNumber: string;
  audioUrl: string;
}

async function sendAudio({
  instanceToken,
  instanceSubdomain,
  phoneNumber,
  audioUrl
}: SendAudioParams) {
  const url = `https://${instanceSubdomain}.uazapi.com/messages/audio`;
  
  const response = await axios.post(url, {
    to: phoneNumber,
    media: audioUrl,
  }, {
    headers: {
      'Authorization': `Bearer ${instanceToken}`
    }
  });
  
  return response.data;
}

// Usage
const result = await sendAudio(
  'seu_token_aqui',
  'finmanage-001',
  '5511999988777',
  'https://exemplo.com/gravacao.mp3'
);
```

#### Send Voice Note (Base64)

```typescript
import axios from 'axios';

interface SendVoiceNoteParams {
  instanceToken: string;
  instanceSubdomain: string;
  phoneNumber: string;
  audioBase64: string;
}

async function sendVoiceNote({
  instanceToken,
  instanceSubdomain,
  phoneNumber,
  audioBase64
}: SendVoiceNoteParams) {
  const url = `https://${instanceSubdomain}.uazapi.com/messages/audio`;
  
  const response = await axios.post(url, {
    to: phoneNumber,
    media: `data:audio/ogg;name=nota.ogg;base64,${audioBase64}`,
  }, {
    headers: {
      'Authorization': `Bearer ${instanceToken}`
    }
  });
  
  return response.data;
}
```

#### Send Video

```typescript
import axios from 'axios';

interface SendVideoParams {
  instanceToken: string;
  instanceSubdomain: string;
  phoneNumber: string;
  videoUrl: string;
  caption?: string;
}

async function sendVideo({
  instanceToken,
  instanceSubdomain,
  phoneNumber,
  videoUrl,
  caption
}: SendVideoParams) {
  const url = `https://${instanceSubdomain}.uazapi.com/messages/video`;
  
  const response = await axios.post(url, {
    to: phoneNumber,
    media: videoUrl,
    caption: caption || 'Vídeo anexado',
  }, {
    headers: {
      'Authorization': `Bearer ${instanceToken}`
    }
  });
  
  return response.data;
}

// Usage
const result = await sendVideo(
  'seu_token_aqui',
  'finmanage-001',
  '5511999988777',
  'https://exemplo.com/tutorial.mp4',
  'Tutorial de uso do FinManage'
);
```

#### Send by Media ID (Reuse)

```typescript
import axios from 'axios';

interface SendByMediaIdParams {
  instanceToken: string;
  instanceSubdomain: string;
  phoneNumber: string;
  mediaId: string;
  caption?: string;
  mediaType?: 'image' | 'document' | 'audio' | 'video';
}

async function sendByMediaId({
  instanceToken,
  instanceSubdomain,
  phoneNumber,
  mediaId,
  caption,
  mediaType = 'image'
}: SendByMediaIdParams) {
  const url = `https://${instanceSubdomain}.uazapi.com/messages/${mediaType}`;
  
  const response = await axios.post(url, {
    to: phoneNumber,
    media_id: mediaId,
    caption: caption || 'Arquivo anexado',
  }, {
    headers: {
      'Authorization': `Bearer ${instanceToken}`
    }
  });
  
  return response.data;
}

// Usage
const result = await sendByMediaId(
  'seu_token_aqui',
  'finmanage-001',
  '5511999988777',
  'media_abc123def456',
  'Consulte o gráfico em anexo'
);
```

### Python

#### Send Image by URL

```python
import requests

def send_image_by_url(instance_token: str, instance_subdomain: str, phone_number: str, image_url: str, caption: str = None):
    url = f"https://{instance_subdomain}.uazapi.com/messages/image"
    
    headers = {
        'Authorization': f'Bearer {instance_token}'
    }
    
    data = {
        'to': phone_number,
        'media': image_url,
        'caption': caption or 'Confira esta imagem!'
    }
    
    response = requests.post(url, json=data, headers=headers)
    return response.json()

# Usage
result = send_image_by_url(
    'seu_token_aqui',
    'finmanage-001',
    '5511999988777',
    'https://exemplo.com/foto.jpg',
    'Veja este relatório!'
)
```

#### Send Document (Upload Local)

```python
import requests

def send_document_local(instance_token: str, instance_subdomain: str, phone_number: str, document_path: str):
    url = f"https://{instance_subdomain}.uazapi.com/messages/document"
    
    headers = {
        'Authorization': f'Bearer {instance_token}'
    }
    
    with open(document_path, 'rb') as file:
        files = {
            'media': file
        }
        data = {
            'to': phone_number,
            'fileName': document_path.split('/')[-1]
        }
        
        response = requests.post(url, files=files, data=data, headers=headers)
        return response.json()

# Usage
result = send_document_local(
    'seu_token_aqui',
    'finmanage-001',
    '5511999988777',
    './relatorios/financeiro.pdf'
)
```

## Error Handling

### Common HTTP Status Codes

| HTTP Code | Description | Solution |
|------------|-------------|------------|
| 401 Unauthorized | Invalid or expired token | Check `instanceToken` |
| 403 Forbidden | Instance doesn't belong to account | Verify instance and subdomain |
| 404 Not Found | Endpoint or resource doesn't exist | Check URL and parameters |
| 429 Too Many Requests | Rate limit reached | Wait before retrying |
| 500 Internal Server Error | Error in Uazapi server | Try again or contact support |

### Error Handling Pattern (TypeScript)

```typescript
async function sendMediaWithErrorHandling(
  instanceToken: string,
  instanceSubdomain: string,
  phoneNumber: string,
  mediaUrl: string
) {
  try {
    const response = await axios.post(
      `https://${instanceSubdomain}.uazapi.com/messages/image`,
      {
        to: phoneNumber,
        media: mediaUrl,
        caption: 'Imagem anexada'
      },
      {
        headers: {
          'Authorization': `Bearer ${instanceToken}`
        },
        timeout: 30000 // 30 seconds timeout
      }
    );
    
    return { success: true, data: response.data };
  } catch (error: any) {
    if (error.response) {
      const status = error.response.status;
      const message = error.response.data?.message || 'Erro desconhecido';
      
      switch (status) {
        case 401:
          console.error('Token inválido ou expirado');
          break;
        case 403:
          console.error('Instância não autorizada');
          break;
        case 429:
          console.error('Rate limit atingido. Aguarde...');
          break;
        case 500:
          console.error('Erro no servidor Uazapi:', message);
          break;
        default:
          console.error(`Erro ${status}: ${message}`);
      }
      
      return { 
        success: false, 
        error: message,
        status 
      };
    } else if (error.code === 'ECONNABORTED') {
      return { 
        success: false, 
        error: 'Timeout da requisição' 
      };
    } else {
      return { 
        success: false, 
        error: error.message || 'Erro desconhecido' 
      };
    }
  }
}
```

## Best Practices

### 1. Choose the Right Method

| Method | Best For | Avoid For |
|--------|------------|-----------|
| **URL** | Hosted files (S3, Cloudinary) | Local temporary files |
| **Base64** | Small files (<10MB) or dynamic files | Large files |
| **Upload Local** | Large files or user uploads | Externally hosted files |
| **Media ID** | Same file to multiple recipients | First send to single recipient |

### 2. File Validation

Always validate files before sending:

```typescript
function validateFile(filePath: string, maxSizeMB: number = 10) {
  const fs = require('fs');
  const stats = fs.statSync(filePath);
  const fileSizeMB = stats.size / (1024 * 1024);
  
  if (fileSizeMB > maxSizeMB) {
    throw new Error(`Arquivo excede ${maxSizeMB}MB`);
  }
  
  return true;
}

// Usage
validateFile('./video.mp4', 100); // Vídeos até 100MB
validateFile('./documento.pdf', 2048); // Documentos até 2GB
```

### 3. Optional Caption

Always provide descriptive caption for media:

```typescript
const goodCaption = '📊 Gráfico de despesas mensais de janeiro 2026';
const badCaption = null; // No context

// Caption helps user understand the content
```

### 4. Phone Number Format

Use E.164 format with country code:

```typescript
// Bad
'5511999988777' // Ambiguous
'+55 1199988777' // Not standard

// Good
'5511999988777' // Complete Brazilian number
```

### 5. Timeout and Retry

Implement timeout and retry for reliability:

```typescript
async function sendWithRetry(
  fn: () => Promise<any>,
  maxRetries: number = 3,
  timeout: number = 30000
) {
  let lastError: any;
  
  for (let attempt = 0; attempt < maxRetries; attempt++) {
    try {
      const result = await fn();
      return result;
    } catch (error: any) {
      lastError = error;
      
      // Don't retry on auth errors
      if (error.response?.status === 401 || error.response?.status === 403) {
        throw error;
      }
      
      // Wait before retry (exponential backoff)
      const delay = Math.pow(2, attempt) * 1000;
      await new Promise(resolve => setTimeout(resolve, delay));
    }
  }
  
  throw lastError;
}
```

### 6. Logging and Monitoring

Log all media send operations:

```typescript
interface MediaSendLog {
  timestamp: string;
  phoneNumber: string;
  mediaType: 'image' | 'document' | 'audio' | 'video';
  method: 'url' | 'base64' | 'upload' | 'media_id';
  success: boolean;
  error?: string;
  responseTimeMs: number;
}

function logMediaSend(log: MediaSendLog) {
  console.log(`[${log.timestamp}] Media Send:`, log);
  
  // Send to monitoring system
  // analytics.track('media_send', log);
}
```

## Practical Examples for FinManage

### Case 1: Send Generated PDF Report

```typescript
async function sendFinancialReport(
  phoneNumber: string,
  pdfUrl: string,
  fileName: string
) {
  const instanceToken = getInstanceToken('finmanage-001');
  
  const result = await sendDocumentByUrl(
    instanceToken,
    'finmanage-001',
    phoneNumber,
    pdfUrl,
    fileName
  );
  
  if (result.success) {
    logMediaSend({
      timestamp: new Date().toISOString(),
      phoneNumber,
      mediaType: 'document',
      method: 'url',
      success: true,
      responseTimeMs: result.responseTime
    });
  }
  
  return result;
}

// Usage
await sendFinancialReport(
  '5511999988777',
  'https://storage.finmanage.com/reports/financeiro-jan-2026.pdf',
  'Relatorio_Financeiro_Janeiro_2026.pdf'
);
```

### Case 2: Send Generated Chart (Image)

```typescript
async function sendChart(
  phoneNumber: string,
  chartData: ChartData
) {
  const instanceToken = getInstanceToken('finmanage-001');
  
  // Generate chart URL (QuickChart.io or similar)
  const chartUrl = generateChartUrl(chartData);
  
  const result = await sendImageByUrl(
    instanceToken,
    'finmanage-001',
    phoneNumber,
    chartUrl,
    `📊 Gráfico de ${chartData.title}`
  );
  
  return result;
}

// Usage
await sendChart(
  '5511999988777',
  {
    title: 'Despesas por Categoria',
    type: 'bar',
    data: { ... }
  }
);
```

### Case 3: Send Multiple Media in Sequence

```typescript
async function sendMediaSequence(phoneNumber: string, medias: Media[]) {
  const instanceToken = getInstanceToken('finmanage-001');
  
  for (const [index, media] of medias.entries()) {
    const delay = index * 2000; // 2 seconds between messages
    
    await new Promise(resolve => setTimeout(resolve, delay));
    
    const result = await sendMediaByType(
      instanceToken,
      'finmanage-001',
      phoneNumber,
      media
    );
    
    if (!result.success) {
      console.error(`Erro ao enviar mídia ${index + 1}:`, result.error);
      break;
    }
  }
}

// Usage
await sendMediaSequence('5511999988777', [
  { type: 'text', content: '📊 Análise Financeira' },
  { type: 'image', url: 'https://...', caption: 'Gráfico de despesas' },
  { type: 'text', content: '📈 Tendências de receita' },
  { type: 'document', url: 'https://...', fileName: 'relatorio.pdf' }
]);
```

## Troubleshooting

### Issue: Media Not Sending (405 Method Not Allowed)

**Symptoms:** Calls to Uazapi return `405 Method Not Allowed`

**Possible Causes:**
1. Wrong endpoint URL (using v1 instead of v2)
2. Incorrect HTTP method (GET instead of POST)
3. Missing required headers
4. Server expecting different content-type

**Solutions:**
1. Verify endpoint URL: `https://{subdomain}.uazapi.com/messages/image`
2. Use POST method for sending messages
3. Include Authorization header with Bearer token
4. For multipart uploads, let FormData handle Content-Type

### Issue: File Too Large

**Symptoms:** WhatsApp rejects the message or file doesn't send

**Solution:** Resize or compress files before sending:
- Images: Use WebP or JPEG with compression
- Videos: Compress with H.264 codec
- Documents: Compress PDF if possible

### Issue: Media ID Expired

**Symptoms:** Error when trying to reuse `media_id`

**Solution:** Upload the file again to get a new `media_id`, as IDs expire after 30 days.

## References

- **Official Uazapi Documentation:** https://docs.uazapi.com
- **Uazapi Help Desk:** https://support.whapi.cloud
- **API Reference (Whapi - same infrastructure):** https://whapi.readme.io
- **WhatsApp Media Policies:** https://developers.facebook.com/documentation/business-messaging/whatsapp/business-phone-numbers/media

---

## Implementation Checklist

- [ ] Configure authentication with correct `instanceToken`
- [ ] Validate phone number format (E.164)
- [ ] Implement file size validation
- [ ] Add timeout and retry logic
- [ ] Implement comprehensive error handling
- [ ] Add logging for monitoring
- [ ] Test with different media types (image, document, audio, video)
- [ ] Handle media ID expiration (30 days)
- [ ] Use appropriate method (URL, Base64, upload, media ID)
- [ ] Add descriptive captions for better UX
- [ ] Test error scenarios (401, 403, 429, 500)

---

**End of Skill**
