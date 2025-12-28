# State Persistence - Persistência de Estado

Este workflow documenta como implementar persistência de estado entre navegações de página usando React Context API.

## Quando Usar
- Dados de busca que devem persistir ao navegar para outra página
- Filtros de interface que não devem resetar
- Estado de formulários multi-step
- Configurações de visualização (zoom, seleção, etc)

---

## 1. Criar o Context

Criar arquivo em `src/contexts/[Nome]Context.tsx`:

```typescript
import React, { createContext, useContext, useState, useCallback } from 'react';

interface StateType {
  // Definir campos do estado
  campo1: string;
  campo2: any[];
}

interface ContextType {
  state: StateType;
  setCampo1: (value: string) => void;
  setCampo2: (value: any[]) => void;
  clearState: () => void;
}

const initialState: StateType = {
  campo1: '',
  campo2: [],
};

const MyContext = createContext<ContextType>({
  state: initialState,
  setCampo1: () => {},
  setCampo2: () => {},
  clearState: () => {},
});

export const MyProvider: React.FC<{ children: React.ReactNode }> = ({ children }) => {
  const [state, setState] = useState<StateType>(initialState);

  const setCampo1 = useCallback((campo1: string) => {
    setState(prev => ({ ...prev, campo1 }));
  }, []);

  const setCampo2 = useCallback((campo2: any[]) => {
    setState(prev => ({ ...prev, campo2 }));
  }, []);

  const clearState = useCallback(() => {
    setState({ ...initialState });
  }, []);

  return (
    <MyContext.Provider value={{ state, setCampo1, setCampo2, clearState }}>
      {children}
    </MyContext.Provider>
  );
};

export const useMyContext = () => useContext(MyContext);
```

---

## 2. Integrar Provider no App.tsx

Envolver as rotas com o Provider:

```tsx
import { MyProvider } from './contexts/MyContext';

const App = () => (
  <AuthProvider>
    <MyProvider>
      <Router>
        {/* Routes */}
      </Router>
    </MyProvider>
  </AuthProvider>
);
```

> [!IMPORTANT]
> O Provider deve estar **dentro** do AuthProvider mas **fora** do Router.

---

## 3. Usar o Context na Página

Substituir `useState` local pelo hook do contexto:

```tsx
// ANTES (estado local - não persiste)
const [query, setQuery] = useState('');
const [results, setResults] = useState([]);

// DEPOIS (estado global - persiste)
const { state, setQuery, setResults } = useMyContext();
// Acessar: state.query, state.results
```

---

## 4. Boas Práticas

1. **useCallback** em todos os setters para evitar re-renders
2. **Não persistir** estados de loading/error (devem ser locais)
3. **clearState** para limpar quando necessário (logout, reset)
4. **Separar contextos** por domínio (SearchContext, FilterContext, etc)

---

## 5. Checklist

- [ ] Contexto criado em `src/contexts/`
- [ ] Provider adicionado no App.tsx
- [ ] Página atualizada para usar o hook
- [ ] Estados de loading/error mantidos locais
- [ ] Testado: navegar e voltar mantém estado
