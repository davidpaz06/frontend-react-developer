# Context Pattern

Use React Context for state that needs to be accessible across a subtree without prop drilling. Context methods never call API files directly — they go through action hooks, same as any other component.

> For complex or frequently-updated global state, prefer **Zustand** over Context. It avoids re-render propagation issues and has a simpler API for large state trees. Context is well-suited for low-frequency state (theme, locale, auth).

---

## Structure

```tsx
// src/adapters/contexts/ThemeContext.tsx
import { createContext, useContext, useState, type ReactNode } from 'react';

interface ThemeContextValue {
  theme: 'light' | 'dark';
  toggleTheme: () => void;
}

const ThemeContext = createContext<ThemeContextValue | null>(null);

export function ThemeProvider({ children }: { children: ReactNode }) {
  const [theme, setTheme] = useState<'light' | 'dark'>('light');

  const toggleTheme = () => setTheme((t) => (t === 'light' ? 'dark' : 'light'));

  return (
    <ThemeContext.Provider value={{ theme, toggleTheme }}>
      {children}
    </ThemeContext.Provider>
  );
}

export function useTheme() {
  const ctx = useContext(ThemeContext);
  if (!ctx) throw new Error('useTheme must be used inside ThemeProvider');
  return ctx;
}
```

Always export a typed accessor hook (`useTheme`, `useAuth`, etc.) — never expose the raw context object. The guard throw ensures misuse surfaces immediately.

---

## Placement

Context files live in `src/adapters/contexts/`. Plain `.tsx`, PascalCase, no type token.

```
src/adapters/contexts/
├── AuthContext.tsx
└── ThemeContext.tsx
```

---

## When NOT to use Context

- When data is only needed 1–2 levels deep — pass props instead.
- When state updates frequently — React re-renders all consumers on every change; use Zustand or split into smaller contexts.
- When the state is local to a single page — keep it in the page component or its action hook.
