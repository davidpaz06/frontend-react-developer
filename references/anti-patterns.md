# Anti-Patterns

Common structural problems in React + TypeScript projects. Load this file during **Refactor** and **Debug** tasks when the issue is architectural rather than a specific runtime error.

Each entry: what it is, why it happens, what bad code looks like, and the fix aligned to this skill's patterns.

---

## 1. Bloated State

**What it is:** Variables in `useState` that are not real state — they can be derived from other state, belong in the URL, or are server data that should live in a loader. The result is a larger-than-necessary state tree with chained `useEffect`s keeping things in sync.

**Why it happens:** Developers (and AI agents) default to `useState` for anything that "changes over time" without asking whether the value is truly independent state or just a projection of something else.

```tsx
// ❌
const [users, setUsers] = useState<User[]>([]);
const [activeCount, setActiveCount] = useState(0);     // derived from users
const [isLoading, setIsLoading] = useState(false);     // process state — see #4
const [isError, setIsError] = useState(false);         // process state — see #4
const [page, setPage] = useState(1);                   // belongs in URL
const [filter, setFilter] = useState('active');        // belongs in URL

useEffect(() => {
  setActiveCount(users.filter(u => u.active).length);  // syncing state with state
}, [users]);
```

**Filter for every variable:**

| Question | Answer |
|---|---|
| Changes by user action and can't be computed? | `useState` |
| Computable from existing state? | Derived variable (or `useMemo` if expensive) |
| Describes a stage in a sequential process? | Discriminated union — see #4 |
| Should survive a page refresh or be shareable? | `useSearchParams` |
| Server data fetched on route load? | Loader + `useLoaderData` |

```tsx
// ✅ — references: loader-pattern.md, routing.md
const [users, setUsers] = useState<User[]>([]);

const activeCount = users.filter(u => u.active).length;   // derived

const [searchParams, setSearchParams] = useSearchParams();
const page   = Number(searchParams.get('page') ?? '1');   // URL
const filter = searchParams.get('filter') ?? 'active';    // URL
```

---

## 2. Prop Drilling

**What it is:** A value is passed as a prop through intermediate components that don't use it, only to reach a deeply nested consumer.

**Why it happens:** The instinct to "just pass it down" works for one or two levels but scales poorly. The intermediate components become coupled to data they don't own.

```tsx
// ❌ — Layout and Sidebar don't use `user`, they just forward it
<Page user={user}>
  <Layout user={user}>
    <Sidebar user={user}>
      <Avatar user={user} />
    </Sidebar>
  </Layout>
</Page>
```

**Fix** (`context-pattern.md`): If the value is low-frequency (auth user, theme, locale), put it in a Context. The consumer reads it directly — no intermediaries involved.

```tsx
// ✅
export function Avatar() {
  const { user } = useAuth(); // reads from AuthContext directly
  return <img src={user.avatar} />;
}
```

Use Context when: the value is needed in 3+ levels deep, or in components that are far apart in the tree. For 1–2 levels, passing props is still cleaner.

---

## 3. Prop Explosion

**What it is:** A component receives more than ~6 individual props that all come from the same hook, fragmenting a cohesive object into disconnected pieces.

**Why it happens:** The parent destructures the hook's return value and passes each piece individually, breaking the natural grouping that the hook already provides.

```tsx
// ❌ — 20 props, all from chatMessages and postPreview hooks
<ChatMessagesView
  messages={chatMessages.messages}
  loading={chatMessages.messagesLoading}
  selectedIds={chatMessages.selectedIds}
  onToggleSelect={chatMessages.toggleSelect}
  fetchingCardId={postPreview.fetchingCardId}
  onCardClick={postPreview.handleCardClick}
  // ... 14 more
/>
```

**Fix** (`custom-hooks.md`, `component-flow.md`): Pass the hook return object directly. The types `UseChatMessagesReturn` and `UsePostPreviewReturn` already exist — use them as the prop type.

```tsx
// ✅ — ChatMessagesViewProps.ts
import type { UseChatMessagesReturn } from '@/adapters/hooks/actions/useChatMessages';
import type { UsePostPreviewReturn } from '@/adapters/hooks/actions/usePostPreview';

export interface ChatMessagesViewProps {
  chatMessages: UseChatMessagesReturn;
  postPreview: UsePostPreviewReturn;
  onBack: () => void;
}

// Call site
<ChatMessagesView chatMessages={chatMessages} postPreview={postPreview} onBack={handleBack} />
```

The component destructures internally what it needs. The parent's call site drops from 20 props to 3.

---

## 4. Boolean Flag Proliferation

**What it is:** A sequential process (idle → loading → success → error) is modeled with multiple parallel boolean flags instead of a single state variable with a discriminated union type.

**Why it happens:** Each flag feels natural when added individually. The problem emerges when they coexist: `isLoading: true` and `isSuccess: true` simultaneously is a valid TypeScript state but an impossible runtime state.

```tsx
// ❌ — 16 possible combinations, only 4 are valid
const [isLoading, setIsLoading] = useState(false);
const [isError,   setIsError]   = useState(false);
const [isSuccess, setIsSuccess] = useState(false);
const [isEmpty,   setIsEmpty]   = useState(false);
```

**Fix** (`custom-hooks.md`): A discriminated union in a single `useState`. TypeScript enforces that only one state is active at a time.

```tsx
// ✅
type Status = 'idle' | 'loading' | 'success' | 'error';
const [status, setStatus] = useState<Status>('idle');

// In the action hook (component-flow.md):
const fetchUsers = async () => {
  setStatus('loading');
  try {
    const data = await userApi.getAll();
    setUsers(data);
    setStatus('success');
  } catch {
    setStatus('error');
  }
};
```

If the error state needs a message, extend with a discriminated union on the type rather than adding another boolean:

```ts
type State =
  | { status: 'idle' | 'loading' | 'success' }
  | { status: 'error'; message: string };
```

---

## 5. Fat Hooks

**What it is:** A hook that handles multiple unrelated responsibilities because they share a domain name. The hook grows past ~80 lines, becomes hard to test, and can't be reused partially.

**Why it happens:** The first few additions feel cohesive. By the time the hook is too large, refactoring feels risky.

```tsx
// ❌ — useUserPage handles listing, selection, deletion, search and pagination
export function useUserPage() {
  const [users, setUsers] = useState<User[]>([]);
  const [selected, setSelected] = useState<string[]>([]);
  const [query, setQuery] = useState('');
  const [page, setPage] = useState(1);
  const [deletingId, setDeletingId] = useState<string | null>(null);
  // 150 lines...
}
```

**Fix** (`custom-hooks.md`): Split by single responsibility. Each hook under ~80 lines. Signs to split: multiple unrelated `useState` groups, effects with no shared state, or imports from more than one unrelated API client.

```tsx
// ✅
const userList  = useUserList();     // fetch + pagination (action hook)
const selection = useSelection();    // multi-select + batch ops (common hook)
const search    = useSearchQuery();  // debounced search (common hook)
const deletion  = useDeleteUser();   // single + batch delete (action hook)
```

Each hook is independently testable and reusable outside this page.

---

## 6. Fetch in Component via useEffect

**What it is:** HTTP requests are made directly inside a component with `useEffect` + `fetch` or `useEffect` + an API call, bypassing the layered architecture defined in `component-flow.md`.

**Why it happens:** It's the most visible React pattern on the internet. AI agents reproduce it by default unless explicitly instructed otherwise.

```tsx
// ❌ — fetch inside the component, skipping the action hook and loader layers
export function UsersPage() {
  const [users, setUsers] = useState<User[]>([]);

  useEffect(() => {
    fetch('/api/users', { credentials: 'include' })
      .then(r => r.json())
      .then(d => setUsers(d.data));
  }, []);

  return <Table data={users} />;
}
```

Problems this causes: no loading state management, no error handling, race conditions on fast navigation, business logic leaking into the component.

**Fix** — two cases:

**Route-level data** (`loader-pattern.md`): Move the fetch to a loader. The component receives already-initiated promises via `useLoaderData`.

```tsx
// ✅ — loader
export const getUsersPageData = () => ({ users: userApi.getAll() });

// ✅ — component
export function UsersPage() {
  const { users } = useLoaderData() as { users: Promise<User[]> };
  return (
    <Suspense fallback={<UsersSkeleton />}>
      <Await resolve={users}>{(data) => <Table data={data} />}</Await>
    </Suspense>
  );
}
```

**User-triggered fetch** (`component-flow.md`): Move the fetch to an action hook. The component calls the hook and handles UX in `try/catch`.

```tsx
// ✅ — action hook handles fetch; component handles UX
const { createUser, isLoading } = useCreateUser();

const handleSubmit = async (data: CreateUserRequest) => {
  try {
    await createUser(data);
  } catch {
    toast.error('Failed to create user');
  }
};
```

---

## 7. Bloated Actions Folder

**What it is:** `adapters/hooks/actions/` stays flat as hooks for many unrelated entities pile in — user hooks, auth hooks, product hooks, order hooks, all in one directory with no organizing structure.

**Why it happens:** Each new hook feels like a small addition. Nobody revisits the folder structure until it's already 15+ files deep and hard to scan.

```
❌ src/adapters/hooks/actions/
   ├── useCreateUser.ts
   ├── useUpdateUser.ts
   ├── useDeleteUser.ts
   ├── useAuthActions.ts
   ├── useCreateProduct.ts
   ├── useUpdateProduct.ts
   ├── useCreateOrder.ts
   └── ... 10 more, no grouping
```

**Fix** (`actions.md`): Once the folder holds 8+ hook files, or hooks for 3+ distinct entities coexist, subdivide into one subfolder per entity — named after the matching `api/clients/<entity>.api.ts`.

```
✅ src/adapters/hooks/actions/
   ├── user/
   │   ├── useCreateUser.ts
   │   ├── useUpdateUser.ts
   │   └── useDeleteUser.ts
   ├── auth/
   │   └── useAuthActions.ts
   └── product/
       ├── useCreateProduct.ts
       └── useUpdateProduct.ts
```

Don't pre-create these subfolders during Scaffolding — start flat, subdivide only once the trigger in `actions.md` is actually met.
