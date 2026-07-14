# Form Validation

Forms use **Zod** for schema definition and **react-hook-form** with `@hookform/resolvers/zod` for form state management.

---

## File placement

The Zod schema lives in a `.schema.ts` file colocated inside the page folder alongside the form component.

```
src/presentation/pages/public/AuthPage/
├── AuthPage.tsx
├── AuthPage.handlers.ts
└── auth-page.schema.ts   ← schema here, not in a global schemas/ folder
```

---

## Schema definition

```ts
// auth-page.schema.ts
import { z } from 'zod';

export const loginSchema = z.object({
  email: z.string().email('Invalid email'),
  password: z.string().min(8, 'Password must be at least 8 characters'),
});

export type LoginFormValues = z.infer<typeof loginSchema>;
```

Always export the inferred type alongside the schema — this is the type used by the form and the handler.

---

## Form component

```tsx
// AuthPage.tsx
import { useForm } from 'react-hook-form';
import { zodResolver } from '@hookform/resolvers/zod';
import { loginSchema, type LoginFormValues } from './auth-page.schema';

export function AuthPage() {
  const {
    register,
    handleSubmit,
    formState: { errors },
  } = useForm<LoginFormValues>({
    resolver: zodResolver(loginSchema),
  });

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <input {...register('email')} />
      {errors.email && <span>{errors.email.message}</span>}
      <input type="password" {...register('password')} />
      {errors.password && <span>{errors.password.message}</span>}
      <button type="submit">Login</button>
    </form>
  );
}
```

The `onSubmit` handler lives in `AuthPage.handlers.ts`, not inline in the component.
