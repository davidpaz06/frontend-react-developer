# Naming Rules

All naming conventions used across frontend projects.

---

## File Naming Pattern

```
<name>.<type>.<ext>
```

- `<name>`: **kebab-case** for multi-word names (e.g., `user-profile`, `auth-page`)
- `<type>`: describes the file's role (see table below)
- `<ext>`: `.ts` or `.tsx`

Single-word names omit the kebab (e.g., `auth.api.ts`, not `auth-.api.ts`).

| Type token | Used for | Example |
|---|---|---|
| *(none)* | React components | `UserCard.tsx`, `AppLayout.tsx` |
| `.api` | API service objects | `user.api.ts` |
| `.actions` | Plain action modules used outside of hooks (deprecated in favor of action hooks) | `user.actions.ts` |
| `.loader` | Query server actions (GET) | `dashboard.loader.ts` |
| `.interface` | TypeScript interfaces (API layer only) | `ApiResponse.interface.ts` |
| `.schema` | Zod validation schemas | `auth-page.schema.ts` |
| `.catalog` | Constant catalog objects | `identification-types.catalog.ts` |
| `.handlers` | Handler functions for a page component | `users-page.handlers.ts` |
| `.config` | Configuration files | `axios.config.ts` *(if ever needed)* |
| `.context` | *(not used — context files are plain .tsx)* | — |

---

## Components

PascalCase, no type token, `.tsx` extension.

```
UserCard.tsx
AppLayout.tsx
SearchInput.tsx
```

---

## Page Folders and Components

Page folders are named `[Name]Page` in PascalCase. The main component inside has the same name.

```
src/presentation/pages/public/AuthPage/
├── AuthPage.tsx
├── AuthPage.handlers.ts
└── auth-page.schema.ts

src/presentation/pages/private/UsersPage/
├── UsersPage.tsx
└── UsersPage.handlers.ts
```

---

## Interfaces

Named after what they describe. API-layer interfaces use `.interface.ts`. Component props do **not** use that suffix — see below.

| Context | Name pattern | Example |
|---|---|---|
| API wrapper | `ApiResponse.interface.ts` | `ApiResponse<T>` |
| Request DTO | `Create[Entity]Request.interface.ts` | `CreateUserRequest.interface.ts` |
| Response DTO | `Create[Entity]Response.interface.ts` | `CreateUserResponse.interface.ts` |
| Domain model | `[Entity].interface.ts` | `User.interface.ts` |
| Component props | `[ComponentName]Props.ts` | `ButtonProps.ts` |

### Component props — colocation rule

Every component's props interface lives **in the same folder as the component**, as a plain `[ComponentName]Props.ts` file (no `.interface` suffix). If a component accepts no props, omit the file entirely.

```
src/presentation/ui/Button/
├── Button.tsx
└── ButtonProps.ts

src/presentation/layout/AppLayout/
├── AppLayout.tsx
└── AppLayoutProps.ts

src/presentation/pages/private/HomePage/
├── HomePage.tsx
└── HomePageProps.ts
```

The same rule applies to simple projects — `src/components/ui/` and `src/components/layout/` also follow the folder-per-component, colocated-props pattern.

There is no centralized `src/interfaces/components/` or `src/presentation/interfaces/` folder.

---

## Skeleton Components

Named `[Section]Skeleton.tsx`, colocated as any other UI component (own folder if it has props, single file if it doesn't).

```
src/presentation/ui/UsersSkeleton/
└── UsersSkeleton.tsx
```

---

## Constants (Catalogs)

File: `kebab-case.catalog.ts`  
Exported identifier: `SCREAMING_SNAKE_CASE`

```ts
// src/constants/identification-types.catalog.ts
export const IDENTIFICATION_TYPES = [
  { value: 'DNI', label: 'DNI' },
  { value: 'PASSPORT', label: 'Passport' },
] as const;
```

---

## Contexts

Plain `.tsx`, PascalCase, no type token.

```
src/adapters/contexts/AuthContext.tsx
src/adapters/contexts/ThemeContext.tsx
```

Exported hook: `use[Name]` (e.g., `useAuth`, `useTheme`).

---

## Custom Hooks

`use[Name].ts` — no type token.

Action hooks (`src/adapters/hooks/actions/`): Hooks that handle backend use cases. Named after the use case.
```
src/adapters/hooks/actions/useCreateUser.ts
src/adapters/hooks/actions/useAuthActions.ts
```

Once `actions/` grows past the split trigger (8+ files, or 3+ distinct entities — see `actions.md`), it subdivides into one subfolder per entity, matching the entity's `api/clients/` name:
```
src/adapters/hooks/actions/user/useCreateUser.ts
src/adapters/hooks/actions/auth/useAuthActions.ts
```

Common hooks (`src/adapters/hooks/common/`): Reusable utility hooks with no backend coupling.
```
src/adapters/hooks/common/useDebounce.ts
src/adapters/hooks/common/usePagination.ts
src/adapters/hooks/common/useSearchQuery.ts
```

---

## Router Files

```
src/presentation/router/router.tsx
src/presentation/router/public/routes.tsx
src/presentation/router/private/routes.tsx
src/presentation/router/private/ProtectedLayout.tsx
src/presentation/router/loaders/dashboard.loader.ts
```

---

## API Files

```
src/api/index.ts                                              ← url constant + re-exports all clients
src/api/clients/user.api.ts
src/api/clients/auth.api.ts
src/api/clients/products.api.ts
src/api/interfaces/ApiResponse.interface.ts
src/api/interfaces/requests/CreateUserRequest.interface.ts
src/api/interfaces/responses/CreateUserResponse.interface.ts
```

---

## Summary Cheat Sheet

```
src/presentation/pages/public/AuthPage/AuthPage.tsx
src/presentation/pages/private/UsersPage/UsersPage.tsx
src/presentation/pages/private/UsersPage/UsersPage.handlers.ts
src/presentation/pages/private/UsersPage/users-page.schema.ts

src/presentation/ui/Button/Button.tsx
src/presentation/ui/Button/ButtonProps.ts
src/presentation/ui/UsersSkeleton/UsersSkeleton.tsx
src/presentation/layout/AppLayout/AppLayout.tsx
src/presentation/layout/AppLayout/AppLayoutProps.ts

src/adapters/contexts/AuthContext.tsx
src/adapters/hooks/common/useSearchQuery.ts
src/adapters/hooks/actions/useCreateUser.ts

src/api/clients/user.api.ts
src/api/interfaces/ApiResponse.interface.ts
src/api/interfaces/requests/CreateUserRequest.interface.ts
src/api/interfaces/responses/CreateUserResponse.interface.ts
src/entities/User.interface.ts

src/presentation/router/loaders/users-page.loader.ts
src/shared/constants/identification-types.catalog.ts
src/shared/utils/format-date.ts
```
