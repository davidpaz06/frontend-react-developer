# Action Folder Subdivision

Action hooks start flat in `src/adapters/hooks/actions/` — one file per use case, per `custom-hooks.md`. This file owns *when and how* that flat folder subdivides into per-domain subfolders once it grows too large to scan.

---

## When to subdivide

Subdivide once either is true, whichever comes first:

| Trigger | Threshold |
|---|---|
| Total hook files in `actions/` | 8 or more |
| Distinct entities/domains represented | 3 or more |

This mirrors the ~80-line fat-hook rule in `custom-hooks.md` — a concrete, checkable number instead of a vague "when it feels big."

**Do not** pre-create empty or near-empty domain subfolders during Scaffolding "just in case." Start flat. Subdivision is a Refactor decision made once the trigger is actually met — not a Scaffolding decision — unless the user explicitly asks for domain folders upfront.

---

## Domain = entity, not feature

The subfolder name matches the corresponding entity across all three layers — same base name as `api/clients/<entity>.api.ts` and `entities/<Entity>.interface.ts`, kebab-case, singular:

```
src/entities/User.interface.ts
src/api/clients/user.api.ts
src/adapters/hooks/actions/user/useCreateUser.ts
```

Grouping by entity (not by feature or page) keeps the hook's domain traceable through every layer it touches.

---

## Structure once subdivided

One level deep only — no nested sub-domains. All-or-nothing: once the trigger is crossed, every hook in `actions/` moves into a domain folder. Never mix loose top-level files with subfolders — that's ambiguous and defeats the point.

```
src/adapters/hooks/actions/
├── user/
│   ├── useCreateUser.ts
│   ├── useUpdateUser.ts
│   └── useDeleteUser.ts
├── auth/
│   └── useAuthActions.ts
└── product/
    └── useCreateProduct.ts
```

---

## Cross-cutting hooks

A hook that calls more than one unrelated `api/clients/*` (e.g. a dashboard summary hook pulling from `user.api.ts` and `product.api.ts`) doesn't map cleanly to one entity. File it under the domain it's most conceptually owned by. Only introduce `actions/shared/` once 2 or more genuinely cross-entity hooks exist — don't create it preemptively for a single hook.

---

## No barrel file

Unlike `api/index.ts`, `actions/` does not get a barrel re-export. This stays consistent with `hooks/common/`, `adapters/contexts/`, and `presentation/ui/` — none of which use a barrel either. Imports stay direct and deep:

```ts
import { useCreateUser } from '@/adapters/hooks/actions/user/useCreateUser';
```

The tradeoff: subdividing later is a real refactor that touches every call site, since there's no indirection layer to absorb the move. That's accepted in exchange for staying consistent with how every other hook/context/component folder in this skill is imported.

---

## Doing the subdivision (Refactor task)

1. For each hook file, identify its entity by which `api/clients/*` it calls.
2. Create one subfolder per distinct entity.
3. Move each hook file into its entity subfolder. Cross-cutting hooks: see above.
4. Update every import site across the project — `@/adapters/hooks/actions/useCreateUser` becomes `@/adapters/hooks/actions/user/useCreateUser`.
5. Verify no file remains directly under `actions/` — every hook sits inside a domain subfolder.

Follow the Refactor workflow in `SKILL.md`: name this as the "Bloated Actions Folder" anti-pattern (`anti-patterns.md`) before proposing the change, and don't touch hook internals — this is a move, not a rewrite. See `component-flow.md` for the layering the hooks themselves must keep obeying.
