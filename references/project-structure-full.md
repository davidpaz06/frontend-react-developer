# Full Project Structure

For web applications connected to a backend API, with authentication and potentially part of a micro-frontend architecture.

## Directory Tree

```
project-root/
├── public/
│   └── media/                        # Static assets served directly (favicons, og images, etc.)
│
├── src/
│   ├── entities/                     # Domain entity interfaces (mirror DB models)
│   │   └── User.interface.ts
│   │
│   ├── api/                          # API layer — HTTP clients and their interfaces
│   │   ├── index.ts                  # Exports url constant + re-exports all clients
│   │   ├── clients/                  # One file per backend controller
│   │   │   ├── auth.api.ts
│   │   │   └── user.api.ts
│   │   └── interfaces/
│   │       ├── ApiResponse.interface.ts        # Wraps ALL API responses
│   │       ├── requests/                       # DTOs matching backend expectations
│   │       │   └── CreateUserRequest.interface.ts
│   │       └── responses/                      # Composite/custom backend responses only
│   │           └── CreateUserResponse.interface.ts
│   │
│   ├── adapters/                     # Hooks and contexts connecting presentation to api/
│   │   ├── contexts/                 # React Context or Zustand stores
│   │   │   └── AuthContext.tsx
│   │   └── hooks/
│   │       ├── actions/              # Hooks for backend use cases (call api/clients/)
│   │       │   └── useCreateUser.ts
│   │       └── common/               # Shared utility hooks
│   │           └── useDebounce.ts
│   │
│   ├── infrastructure/               # Third-party SDKs and service integrations
│   │   └── firebase/
│   │
│   ├── presentation/                 # All visual and routing code
│   │   ├── assets/
│   │   │   ├── fonts/                # .woff2 only
│   │   │   ├── icons/                # SVG icons
│   │   │   ├── images/               # .webp only
│   │   │   └── videos/               # .webm only
│   │   ├── layout/                   # Structural components — one folder per component
│   │   │   └── AppLayout/
│   │   │       ├── AppLayout.tsx
│   │   │       └── AppLayoutProps.ts   # Props colocated; omit if no props
│   │   ├── ui/                       # Reusable atomic components — one folder per component
│   │   │   ├── Button/
│   │   │   │   ├── Button.tsx
│   │   │   │   └── ButtonProps.ts
│   │   │   └── UsersSkeleton/
│   │   │       └── UsersSkeleton.tsx  # No props file — skeletons rarely need props
│   │   ├── pages/
│   │   │   ├── public/               # Routes that do not require authentication
│   │   │   │   └── AuthPage/
│   │   │   │       ├── AuthPage.tsx
│   │   │   │       ├── AuthPage.handlers.ts
│   │   │   │       └── auth-page.schema.ts
│   │   │   └── private/              # Routes guarded by ProtectedLayout
│   │   │       └── UsersPage/
│   │   │           ├── UsersPage.tsx
│   │   │           └── UsersPage.handlers.ts
│   │   └── router/
│   │       ├── loaders/              # Data pre-fetching functions for route loader props
│   │       │   └── users-page.loader.ts
│   │       ├── private/
│   │       │   ├── ProtectedLayout.tsx   # Auth guard — redirects to login if not authenticated
│   │       │   └── routes.tsx            # RouteObject[] for private routes
│   │       ├── public/
│   │       │   └── routes.tsx            # RouteObject[] for public routes
│   │       └── router.tsx                # createBrowserRouter() combining all routes
│   │
│   └── shared/
│       ├── constants/                # Small catalog objects mirroring DB tables
│       │   └── identification-types.catalog.ts
│       └── utils/                    # Pure utility functions (formatters, parsers)
│           └── format-date.ts
│
├── .env                              # VITE_API_BASE_URL= must be the first entry
├── .env.example                      # Committed template with empty values
├── App.tsx                           # Renders <AppRouter />
├── index.css                         # Global styles and Tailwind CSS variables
├── main.tsx                          # Entry point — renders <App /> into #root
├── vite.config.ts
├── tsconfig.json
├── tsconfig.node.json
└── package.json
```

## Key Files

### src/api/index.ts
```ts
export const url = import.meta.env.VITE_API_BASE_URL as string;

export { authApi } from './clients/auth.api';
export { userApi } from './clients/user.api';
```

### .env
```
VITE_API_BASE_URL=http://localhost:3000
```

### src/App.tsx
```tsx
import { AppRouter } from '@/presentation/router/router';

export default function App() {
  return <AppRouter />;
}
```

### src/main.tsx
```tsx
import { StrictMode } from 'react';
import { createRoot } from 'react-dom/client';
import App from './App';
import './index.css';

createRoot(document.getElementById('root')!).render(
  <StrictMode>
    <App />
  </StrictMode>,
);
```

## Notes

- `entities/` holds plain TypeScript interfaces that mirror the database models. No business logic.
- `api/clients/` is exclusively for this project's own backend HTTP calls. Third-party SDK integrations go in `infrastructure/`.
- `api/interfaces/responses/` is only for composite or custom backend responses. If the response is a plain entity, reference `entities/` directly.
- `adapters/hooks/actions/` contains React hooks (useXxx) that handle backend use cases. They call functions from `api/clients/` and never import from `presentation/` directly. Stays flat while small; once it holds 8+ hooks or hooks for 3+ distinct entities, it subdivides into one subfolder per entity — see `references/actions.md`.
- `adapters/hooks/common/` contains utility hooks with no backend coupling (debounce, pagination, media query, etc.).
- `presentation/layout/` and `presentation/ui/` use a **folder-per-component** structure. Each component's props interface (`[ComponentName]Props.ts`) lives in the same folder. Omit the props file if the component accepts no props.
- Skeleton components live in `presentation/ui/[Section]Skeleton/` and are treated as regular UI components.
- `shared/constants/` catalogs must be kept in sync with the backend's equivalent lookup tables.
- Do not create files or folders not listed here unless the user explicitly requests additions.
