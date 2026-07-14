# Routing

Uses **React Router v7** (`createBrowserRouter`). Routes are split into public/private arrays and composed in `router.tsx`.

> All imports come from `react-router` — not `react-router-dom`. If working on a legacy project running v6.x, verify with `npm ls react-router` before applying these examples.

---

## Folder Structure

```
src/presentation/router/
├── router.tsx              ← createBrowserRouter() — combines all routes
├── loaders/                ← data pre-fetching functions (see loader-pattern.md)
├── public/
│   └── routes.tsx          ← RouteObject[] for unauthenticated routes
└── private/
    ├── routes.tsx          ← RouteObject[] wrapped by ProtectedLayout
    └── ProtectedLayout.tsx ← auth guard
```

---

## router.tsx

```tsx
import { createBrowserRouter, Navigate, Outlet, useNavigation } from 'react-router';
import { AuthProvider } from '@/adapters/contexts/AuthContext';
import { publicRoutes } from './public/routes';
import { privateRoutes } from './private/routes';

function RootLayout() {
  const navigation = useNavigation();
  return (
    <AuthProvider>
      {navigation.state === 'loading' && (
        <div className="fixed top-0 inset-x-0 h-0.5 bg-blue-500 z-50 animate-pulse" />
      )}
      <Outlet />
    </AuthProvider>
  );
}

export const router = createBrowserRouter([
  {
    element: <RootLayout />,
    children: [
      ...publicRoutes,
      ...privateRoutes,
      { path: '*', element: <Navigate to="/auth/login" replace /> },
    ],
  },
]);
```

## AppRouter export

```tsx
// src/presentation/router/router.tsx
import { RouterProvider } from 'react-router';
import { router } from './router';

export function AppRouter() {
  return <RouterProvider router={router} />;
}
```

## Private routes with lazy loading

```tsx
// src/presentation/router/private/routes.tsx
import type { RouteObject } from 'react-router';
import { ProtectedLayout } from './ProtectedLayout';

export const privateRoutes: RouteObject[] = [
  {
    path: '/app',
    element: <ProtectedLayout />,
    children: [
      {
        path: 'users',
        loader: getUsersPageData,
        lazy: async () => {
          const { UsersPage } = await import('@/presentation/pages/private/UsersPage/UsersPage');
          return { Component: UsersPage };
        },
      },
    ],
  },
];
```

Always use `lazy` for route-level components — this enables code splitting per route.
