# Component → Action → API Flow

The core architectural pattern connecting the presentation layer to the API layer. Every data mutation and fetch in the app follows this vertical flow — no exceptions.

```
Component (.tsx)
    └── calls hook from adapters/hooks/actions/
            └── hook calls method on api/clients/*.api.ts
                    └── calls fetch(), parses ApiResponse<T>, returns T
```

**Why this exists:** Components should not know how to format request bodies or handle HTTP errors. The API layer should not contain business logic — only fetch and parse. Each layer has one responsibility.

---

## Error Handling Responsibility

Each layer handles a specific type of concern. Do not blur these boundaries.

| Layer | Responsibility |
|---|---|
| Component | `try/catch` around the action hook call — handles UX (toast, revert optimistic state) |
| Action hook | `try/finally` for loading state only — does not catch errors, lets them propagate |
| API client | throws on non-OK responses — never catches |

---

## Component

```tsx
import { useCreateUser } from '@/adapters/hooks/actions/useCreateUser';
import type { CreateUserRequest } from '@/api/interfaces/requests/CreateUserRequest.interface';

const handleCreateUser = async (data: CreateUserRequest) => {
  const tempId = crypto.randomUUID();
  const tempUser = { ...data, user_id: tempId };

  setUsers((prev) => [...prev, tempUser]);
  setTotal((prev) => prev + 1);

  try {
    const result = await createUser(data);
    setUsers((prev) =>
      prev.map((u) => (u.user_id === tempId ? { ...tempUser, user_id: result.user_id } : u)),
    );
  } catch (error) {
    setUsers((prev) => prev.filter((u) => u.user_id !== tempId));
    setTotal((prev) => prev - 1);
    toast.error('Failed to create user');
  }
};
```

## Action Hook

```ts
// src/adapters/hooks/actions/useCreateUser.ts
import { useState } from 'react';
import { userApi } from '@/api';
import type { CreateUserRequest } from '@/api/interfaces/requests/CreateUserRequest.interface';
import type { CreateUserResponse } from '@/api/interfaces/responses/CreateUserResponse.interface';

export function useCreateUser() {
  const [isLoading, setIsLoading] = useState(false);

  const createUser = async (data: CreateUserRequest): Promise<CreateUserResponse> => {
    setIsLoading(true);
    try {
      return await userApi.create(data);
    } finally {
      setIsLoading(false);
    }
  };

  return { createUser, isLoading };
}
```

## API Client

```ts
// src/api/clients/user.api.ts
import { url } from '@/api';
import type { ApiResponse } from '@/api/interfaces/ApiResponse.interface';
import type { CreateUserRequest } from '@/api/interfaces/requests/CreateUserRequest.interface';
import type { CreateUserResponse } from '@/api/interfaces/responses/CreateUserResponse.interface';

export const userApi = {
  async create(data: CreateUserRequest): Promise<CreateUserResponse> {
    const response = await fetch(`${url}/user`, {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      credentials: 'include',
      body: JSON.stringify(data),
    });
    const json = await response.json();
    if (!response.ok) {
      const message = json?.message ?? json?.error ?? 'Unexpected error';
      throw new Error(Array.isArray(message) ? message.join(', ') : message);
    }
    return (json as ApiResponse<CreateUserResponse>).data;
  },
};
```

## API Index

```ts
// src/api/index.ts
export const url = import.meta.env.VITE_API_BASE_URL as string;

export { userApi } from './clients/user.api';
export { authApi } from './clients/auth.api';
```

## ApiResponse Interface

```ts
// src/api/interfaces/ApiResponse.interface.ts
export interface ApiResponse<T> {
  success: boolean;
  statusCode: number;
  message: string;
  data: T;
  timestamp: string;
}
```
