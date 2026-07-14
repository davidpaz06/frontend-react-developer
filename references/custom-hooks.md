# Custom Hook Pattern

Custom hooks encapsulate reusable logic that doesn't belong in a single component. Each hook has one clear responsibility — if it's doing two unrelated things, split it.

---

## Two categories of hooks

| Category | Location | Purpose |
|---|---|---|
| Action hooks | `src/adapters/hooks/actions/` | Backend use cases — call `api/clients/` |
| Common hooks | `src/adapters/hooks/common/` | Utility logic with no backend coupling |

Action hooks are named after the use case (`useCreateUser`, `useAuthActions`). Common hooks are named after the behavior (`useDebounce`, `useSearchQuery`, `usePagination`).

---

## Common hook example

```ts
// src/adapters/hooks/common/useSearchQuery.ts
import { useState, useEffect } from 'react';
import { userApi } from '@/api';
import type { User } from '@/entities/User.interface';

interface UseSearchQueryResult {
  results: User[];
  isLoading: boolean;
  query: string;
  setQuery: (q: string) => void;
}

export function useSearchQuery(debounceMs = 300): UseSearchQueryResult {
  const [query, setQuery] = useState('');
  const [results, setResults] = useState<User[]>([]);
  const [isLoading, setIsLoading] = useState(false);

  useEffect(() => {
    if (!query.trim()) {
      setResults([]);
      return;
    }
    const timer = setTimeout(async () => {
      setIsLoading(true);
      try {
        const data = await userApi.search(query);
        setResults(data);
      } finally {
        setIsLoading(false);
      }
    }, debounceMs);

    return () => clearTimeout(timer);
  }, [query, debounceMs]);

  return { results, isLoading, query, setQuery };
}
```

---

## Return type convention

Always define and export the return type interface explicitly. This is what the component receives when the hook is passed as a prop object.

```ts
export interface UseSearchQueryResult {
  results: User[];
  isLoading: boolean;
  query: string;
  setQuery: (q: string) => void;
}
```

Naming: `Use[HookName]Result` or `Use[HookName]Return` — be consistent across the project.

---

## When a hook is too large

If a hook exceeds ~80 lines or manages state for two unrelated concerns, split it. Signs of a fat hook: multiple unrelated `useState` groups, effects that don't share state, or more than one API client import for unrelated resources.
