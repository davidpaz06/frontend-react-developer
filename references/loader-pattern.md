# Loader Pattern

Loaders pre-fetch data for a route before the page component renders. The default is **deferred loading**: return raw unresolved promises from the loader and resolve them in the page with `<Suspense>` + `<Await>`.

This keeps navigation instant, limits error scope to individual sections, and avoids full-page loading spinners.

---

## When to defer vs when to block

| Situation | Approach |
|---|---|
| Lists, counters, secondary content | Defer — return raw promise |
| Primary record a detail page cannot render without | Block — `await` inside the loader |
| Mix of critical and secondary data | `await` only the critical part; defer the rest |

---

## Deferred loader (default)

```ts
// src/presentation/router/loaders/users-page.loader.ts
import { userApi } from '@/api';

export const getUsersPageData = () => {
  const usersPromise = userApi.getAll();
  return { users: usersPromise };
};
```

## Page component — resolving deferred data

```tsx
// UsersPage.tsx
import { useLoaderData, Await } from 'react-router';
import { Suspense } from 'react';
import { UsersSkeleton } from '@/presentation/ui/UsersSkeleton/UsersSkeleton';
import type { User } from '@/entities/User.interface';

export function UsersPage() {
  const { users } = useLoaderData() as { users: Promise<User[]> };

  return (
    <Suspense fallback={<UsersSkeleton />}>
      <Await resolve={users} errorElement={<p>Failed to load users</p>}>
        {(resolvedUsers) => <UsersTable data={resolvedUsers} />}
      </Await>
    </Suspense>
  );
}
```

`errorElement` on `<Await>` scopes the error to that section — it does not crash the whole route.

## Mixing critical and deferred data

```ts
// src/presentation/router/loaders/user-detail-page.loader.ts
import { userApi } from '@/api';
import type { LoaderFunctionArgs } from 'react-router';

export const getUserDetailPageData = async ({ params }: LoaderFunctionArgs) => {
  // Block only for the primary record
  const user = await userApi.getById(params.id!);
  // Defer everything else
  return {
    user,
    activity: userApi.getActivity(params.id!),
    stats: userApi.getStats(params.id!),
  };
};
```
