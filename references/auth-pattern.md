# Auth Pattern

Authentication flows through `AuthContext` → `useAuthActions` (action hook) → `auth.api.ts`. The context never imports from `auth.api.ts` directly.

```
AuthContext.tsx
    └── useAuthActions (adapters/hooks/actions/)
            └── authApi (api/clients/auth.api.ts)
```

All authenticated requests must pass `credentials: 'include'` — auth is cookie-based (httpOnly), never stored in localStorage or memory.

---

## AuthContext

```tsx
// src/adapters/contexts/AuthContext.tsx
import { createContext, useContext, useState, useEffect, type ReactNode } from 'react';
import { useAuthActions } from '@/adapters/hooks/actions/useAuthActions';
import type { User } from '@/entities/User.interface';

interface AuthContextValue {
  user: User | null;
  isAuthenticated: boolean;
  isLoading: boolean;
  login: (email: string, password: string) => Promise<void>;
  logout: () => Promise<void>;
}

const AuthContext = createContext<AuthContextValue | null>(null);

export function AuthProvider({ children }: { children: ReactNode }) {
  const [user, setUser] = useState<User | null>(null);
  const [isLoading, setIsLoading] = useState(true);
  const { loginAction, logoutAction, refreshTokenAction } = useAuthActions();

  useEffect(() => {
    refreshTokenAction()
      .then(setUser)
      .catch(() => setUser(null))
      .finally(() => setIsLoading(false));
  }, []);

  const login = async (email: string, password: string) => {
    const loggedUser = await loginAction({ email, password });
    setUser(loggedUser);
  };

  const logout = async () => {
    await logoutAction();
    setUser(null);
  };

  return (
    <AuthContext.Provider value={{ user, isAuthenticated: !!user, isLoading, login, logout }}>
      {children}
    </AuthContext.Provider>
  );
}

export const useAuth = () => {
  const ctx = useContext(AuthContext);
  if (!ctx) throw new Error('useAuth must be used inside AuthProvider');
  return ctx;
};
```

---

## ProtectedLayout

```tsx
// src/presentation/router/private/ProtectedLayout.tsx
import { Navigate, Outlet } from 'react-router';
import { useAuth } from '@/adapters/contexts/AuthContext';

export function ProtectedLayout() {
  const { isAuthenticated, isLoading } = useAuth();

  if (isLoading) {
    return (
      <div className="min-h-screen flex items-center justify-center">
        <div className="w-8 h-8 border-2 border-gray-700 border-t-transparent rounded-full animate-spin" />
      </div>
    );
  }

  return isAuthenticated ? <Outlet /> : <Navigate to="/auth/login" replace />;
}
```

---

## Required auth actions

`useAuthActions` must expose at minimum: `loginAction`, `logoutAction`, `refreshTokenAction`, `registerAction`.

The `registerAction` can optionally call `loginAction` automatically after success to improve onboarding UX — only implement this if explicitly requested.
