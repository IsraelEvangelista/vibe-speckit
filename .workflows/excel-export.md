# Workflow: Exportação de Arquivos Excel no Browser

Este workflow documenta a implementação correta de exportação de arquivos Excel (.xlsx) em aplicações web React, resolvendo problemas comuns como nomes de arquivo UUID e downloads que não salvam corretamente.

## Problema Comum

Ao usar bibliotecas como `xlsx` (SheetJS) ou `exceljs` para gerar arquivos Excel no browser, os seguintes problemas podem ocorrer:

1. **Arquivos baixados com nomes UUID** (ex: `c7244942-10da-4806-aa3d-792255ea9548`)
2. **Arquivos que aparecem no histórico de downloads mas não salvam na pasta**
3. **Extensão `.xlsx` não é adicionada ao arquivo**

### Causa Raiz

- **Google Tag Manager (GTM)** intercepta elementos `<a>` criados dinamicamente e corrompe o atributo `download`
- Outros scripts de tracking podem ter comportamento similar
- Configurações de segurança do browser podem bloquear downloads programáticos

---

## Solução Recomendada: File System Access API

A solução mais robusta é usar a **File System Access API** (`showSaveFilePicker`) que abre um diálogo nativo do sistema operacional e não pode ser interceptado por scripts.

### Pré-requisitos

```bash
npm install xlsx
```

### Implementação

```typescript
import * as XLSX from 'xlsx';

// Tipos para File System Access API
declare global {
  interface Window {
    showSaveFilePicker?: (options?: {
      suggestedName?: string;
      types?: Array<{
        description: string;
        accept: Record<string, string[]>;
      }>;
    }) => Promise<FileSystemFileHandle>;
  }
}

interface FileSystemFileHandle {
  createWritable(): Promise<FileSystemWritableFileStream>;
  name: string;
}

interface FileSystemWritableFileStream extends WritableStream {
  write(data: BufferSource | Blob | string): Promise<void>;
  close(): Promise<void>;
}

/**
 * Exporta dados para Excel usando File System Access API
 */
export const exportToExcel = async (
  data: unknown[][],
  suggestedFileName: string
): Promise<void> => {
  // 1. Criar workbook e worksheet
  const workbook = XLSX.utils.book_new();
  const worksheet = XLSX.utils.aoa_to_sheet(data);
  
  // 2. Configurar largura das colunas (opcional)
  worksheet['!cols'] = [
    { wch: 30 }, // Coluna A
    { wch: 20 }, // Coluna B
    // ... adicionar mais conforme necessário
  ];
  
  // 3. Adicionar worksheet ao workbook
  XLSX.utils.book_append_sheet(workbook, worksheet, 'Dados');
  
  // 4. Verificar se File System Access API está disponível
  if (window.showSaveFilePicker) {
    try {
      // Mostrar diálogo nativo "Salvar como"
      const fileHandle = await window.showSaveFilePicker({
        suggestedName: suggestedFileName,
        types: [
          {
            description: 'Arquivo Excel (.xlsx)',
            accept: {
              'application/vnd.openxmlformats-officedocument.spreadsheetml.sheet': ['.xlsx']
            }
          }
        ]
      });
      
      // Criar stream de escrita
      const writableStream = await fileHandle.createWritable();
      
      // Gerar buffer do Excel
      const excelBuffer = XLSX.write(workbook, { 
        bookType: 'xlsx', 
        type: 'array'
      });
      
      // Escrever e salvar
      await writableStream.write(excelBuffer);
      await writableStream.close();
      
    } catch (error) {
      if ((error as Error).name === 'AbortError') {
        console.log('Usuário cancelou o diálogo');
        return;
      }
      throw error;
    }
  } else {
    // Fallback para browsers sem suporte
    XLSX.writeFile(workbook, suggestedFileName);
  }
};
```

---

## Fallback: Workaround para GTM

Se a File System Access API não estiver disponível (Safari, Firefox antigo), use o workaround oficial do SheetJS para GTM:

```typescript
// Salvar método original
const originalCreateElement = document.createElement.bind(document);

// Patch: garantir que elementos <a> tenham target="_blank"
document.createElement = function(tagName: string, options?: ElementCreationOptions) {
  const element = originalCreateElement(tagName, options);
  if (tagName.toLowerCase() === 'a') {
    (element as HTMLAnchorElement).target = '_blank';
  }
  return element;
};

// Exportar
XLSX.writeFile(workbook, fileName);

// Restaurar método original após delay
setTimeout(() => {
  document.createElement = originalCreateElement;
}, 1000);
```

---

## Estilização do Excel (Opcional)

Para adicionar estilos (cores, fontes, bordas), use a biblioteca `xlsx-style` ou aplique estilos diretamente:

```typescript
// Definir estilo de célula
worksheet['A1'].s = {
  font: { bold: true, color: { rgb: 'FFFFFF' } },
  fill: { fgColor: { rgb: '4F81BD' } },
  alignment: { horizontal: 'center' }
};
```

> **Nota:** O SheetJS Community Edition tem suporte limitado a estilos. Para estilos avançados, considere `xlsx-style` ou `exceljs`.

---

## Checklist de Implementação

- [ ] Instalar dependência `xlsx`
- [ ] Adicionar tipos para File System Access API
- [ ] Implementar função de exportação com `showSaveFilePicker`
- [ ] Adicionar fallback para browsers sem suporte
- [ ] Testar em diferentes browsers (Chrome, Edge, Firefox, Safari)
- [ ] Verificar se Google Tag Manager está presente e aplicar workaround se necessário

---

## Referências

- [SheetJS Documentation - Local File Access](https://docs.sheetjs.com/docs/demos/local/file/)
- [File System Access API (MDN)](https://developer.mozilla.org/en-US/docs/Web/API/File_System_Access_API)
- [SheetJS GTM Workaround](https://docs.sheetjs.com/docs/demos/local/file/#google-tag-manager)
