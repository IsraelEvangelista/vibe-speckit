# Description
Evitar re-fetches desnecessários ao navegar entre telas ou interagir com componentes que não alteram os dados de fato.

# Workflow: Eficiência de Carregamento com Cache de Dados

---
## Problema
Hooks React que usam estados como dependência do `useCallback` ou `useEffect` causam re-fetch toda vez que o estado muda, mesmo quando a mudança não exige novos dados do servidor.
**Sintoma**: Interface lenta ao navegar, loading aparece repetidamente, múltiplas requisições no Network tab.
---
## Solução: Cache com useRef + useMemo
### 1. Identifique a Chave de Cache
Defina qual **subset dos parâmetros** realmente requer novo fetch:
```typescript
// ❌ RUIM: objeto completo como dependência
const fetchData = useCallback(async () => { ... }, [filterObject]);
// ✅ BOM: apenas os campos que exigem novo fetch
const cacheKey = useMemo(() => {
  return `${filterObject.year}-${filterObject.month}`;
}, [filterObject.year, filterObject.month]);
```
### 2. Implemente Cache com useRef
```typescript
interface CacheEntry<T> {
  key: string;
  data: T[];
}
const cacheRef = useRef<CacheEntry<T> | null>(null);
const fetchData = useCallback(async (forceRefetch = false) => {
  // Verificar cache antes de buscar
  if (!forceRefetch && cacheRef.current?.key === cacheKey) {
    return; // Dados já em cache, não buscar novamente
  }
  setIsLoading(true);
  try {
    const response = await api.get('/endpoint', { params });
    
    // Salvar no cache
    cacheRef.current = { key: cacheKey, data: response.data };
    setAllData(response.data);
  } finally {
    setIsLoading(false);
  }
}, [cacheKey]); // Só depende da chave, não do objeto completo
```
### 3. Filtragem no Frontend com useMemo
Aplique filtros locais (busca, seleção) sem refetch:
```typescript
// Estado bruto do servidor
const [allData, setAllData] = useState<T[]>([]);
// Filtragem local (instantânea, sem nova requisição)
const filteredData = useMemo(() => {
  let result = allData;
  
  // Filtro por busca
  if (searchTerm.trim()) {
    result = result.filter(item => 
      item.name.toLowerCase().includes(searchTerm.toLowerCase())
    );
  }
  
  // Filtro por seleção (não requer refetch)
  if (selectedItem) {
    result = result.filter(item => item.id === selectedItem.id);
  }
  
  return result;
}, [allData, searchTerm, selectedItem]);
```
### 4. Refetch Manual
Exponha função para forçar atualização após mutações:
```typescript
const refetch = useCallback(() => {
  fetchData(true); // forceRefetch = true
}, [fetchData]);
return { data: filteredData, isLoading, refetch };
```
---
## Checklist de Aplicação
- [ ] Identificar quais parâmetros realmente exigem novo fetch
- [ ] Criar `useMemo` para chave de cache estável
- [ ] Implementar `useRef` para armazenar cache
- [ ] Mover filtros para `useMemo` no frontend
- [ ] Expor função `refetch(true)` para uso manual
- [ ] Validar com TypeScript e Build
---
## Quando Usar
| Cenário | Usar Cache? |
|---------|-------------|
| Navegação entre itens de uma lista já carregada | ✅ Sim |
| Mudança de período principal (mês/ano) | ❌ Não (novo fetch) |
| Busca por termo em dados já carregados | ✅ Sim |
| Filtro local (status, tipo, categoria) | ✅ Sim |
| Após criar/editar/deletar registro | ❌ Não (chamar refetch) |
| Troca de página/aba com mesmos dados | ✅ Sim |
---
## Template de Hook Otimizado
```typescript
import { useState, useEffect, useCallback, useMemo, useRef } from 'react';
interface CacheEntry<T> {
  key: string;
  data: T[];
}
interface UseOptimizedDataReturn<T> {
  data: T[];
  isLoading: boolean;
  error: string | null;
  refetch: () => void;
}
export function useOptimizedData<T>(
  primaryFilter: { year: number; month: number },
  searchTerm: string = ''
): UseOptimizedDataReturn<T> {
  const cacheRef = useRef<CacheEntry<T> | null>(null);
  const [allData, setAllData] = useState<T[]>([]);
  const [isLoading, setIsLoading] = useState(true);
  const [error, setError] = useState<string | null>(null);
  // Chave de cache: apenas campos que exigem novo fetch
  const cacheKey = useMemo(() => {
    return `${primaryFilter.year}-${primaryFilter.month}`;
  }, [primaryFilter.year, primaryFilter.month]);
  const fetchData = useCallback(async (forceRefetch = false) => {
    if (!forceRefetch && cacheRef.current?.key === cacheKey) {
      return;
    }
    setIsLoading(true);
    setError(null);
    try {
      const response = await fetch(`/api/data?year=${primaryFilter.year}&month=${primaryFilter.month}`);
      const data = await response.json();
      
      cacheRef.current = { key: cacheKey, data };
      setAllData(data);
    } catch (err) {
      setError(err instanceof Error ? err.message : 'Erro ao carregar dados');
    } finally {
      setIsLoading(false);
    }
  }, [cacheKey, primaryFilter.year, primaryFilter.month]);
  useEffect(() => {
    fetchData();
  }, [fetchData]);
  // Filtragem local (sem refetch)
  const filteredData = useMemo(() => {
    if (!searchTerm.trim()) return allData;
    return allData.filter(item => 
      JSON.stringify(item).toLowerCase().includes(searchTerm.toLowerCase())
    );
  }, [allData, searchTerm]);
  const refetch = useCallback(() => fetchData(true), [fetchData]);
  return { data: filteredData, isLoading, error, refetch };
}
```
---
## Benefícios
- **Performance**: Elimina requisições redundantes
- **UX**: Navegação instantânea entre itens filtrados
- **Economia**: Reduz chamadas à API/banco de dados
- **Escalável**: Padrão aplicável a qualquer hook de dados
