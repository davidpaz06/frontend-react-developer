# Vite Config & tsconfig Paths

Always configure the `@` alias pointing to `./src`. This eliminates brittle relative imports like `../../components` across the entire project.

---

## vite.config.ts

```ts
import { defineConfig } from 'vite';
import react from '@vitejs/plugin-react';
import tailwindcss from '@tailwindcss/vite';
import path from 'path';

export default defineConfig({
  plugins: [react(), tailwindcss()],
  resolve: {
    alias: {
      '@': path.resolve(__dirname, './src'),
    },
  },
});
```

Install `@types/node` as a dev dependency so `path` resolves correctly:

```bash
npm i -D @types/node
```

---

## tsconfig.json

```json
{
  "compilerOptions": {
    "baseUrl": ".",
    "paths": {
      "@/*": ["./src/*"]
    }
  }
}
```

Both files must be configured — Vite needs the alias for bundling, TypeScript needs it for type checking. Configuring only one will cause errors in the IDE or at build time.

---

## Tailwind

Use the `@tailwindcss/vite` plugin — not the PostCSS plugin. It's already included in the `vite.config.ts` above via `tailwindcss()`.

```bash
npm i @tailwindcss/vite
```

No `tailwind.config.js` or `postcss.config.js` needed with this approach.
