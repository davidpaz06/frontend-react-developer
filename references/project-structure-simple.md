# Simple Project Structure

For frontend-only projects: landing pages, portfolios, informational sites. No backend, no authentication.

## Directory Tree

```
project-root/
├── public/
│   └── media/                        # Static assets served directly (favicons, og images)
│
├── src/
│   ├── assets/
│   │   ├── fonts/                    # .woff2 only
│   │   ├── icons/                    # SVG icons
│   │   ├── images/                   # .webp only
│   │   └── videos/                   # .webm only
│   │
│   ├── components/
│   │   ├── layout/                   # Structural components — one folder per component
│   │   │   └── Header/
│   │   │       ├── Header.tsx
│   │   │       └── HeaderProps.ts    # Props colocated; omit if no props
│   │   └── ui/                       # Reusable atomic components — one folder per component
│   │       └── Button/
│   │           ├── Button.tsx
│   │           └── ButtonProps.ts
│   │
│   ├── constants/                    # Static data objects (nav links, feature lists, etc.)
│   │   └── nav-links.catalog.ts
│   │
│   ├── hooks/                        # Custom hooks (useScrollPosition, useMediaQuery, etc.)
│   │   └── useScrollPosition.ts
│   │
│   ├── pages/                        # One folder per route/section
│   │   ├── HomePage/
│   │   │   └── HomePage.tsx
│   │   └── AboutPage/
│   │       └── AboutPage.tsx
│   │
│   ├── router/
│   │   └── router.tsx                # createBrowserRouter() with all routes
│   │
│   ├── utils/                        # Pure utility functions
│   │   └── format-date.ts
│   │
│   ├── App.tsx
│   ├── index.css
│   └── main.tsx
│
├── vite.config.ts
├── tsconfig.json
├── tsconfig.node.json
└── package.json
```

## Key Files

### src/router/router.tsx
```tsx
import { createBrowserRouter, RouterProvider } from 'react-router';
import { HomePage } from '@/pages/HomePage/HomePage';
import { AboutPage } from '@/pages/AboutPage/AboutPage';

const router = createBrowserRouter([
  { path: '/', element: <HomePage /> },
  { path: '/about', element: <AboutPage /> },
]);

export function AppRouter() {
  return <RouterProvider router={router} />;
}
```

### src/App.tsx
```tsx
import { AppRouter } from '@/router/router';

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

- No `src/api/`, `src/contexts/`, or `src/services/` folders — these are not needed for static sites.
- No `.env` file unless the project fetches from a third-party API.
- `components/layout/` and `components/ui/` use a **folder-per-component** structure. Props interfaces (`[ComponentName]Props.ts`) are colocated in the component folder. Omit the props file if the component accepts no props. There is no centralized `interfaces/` folder.
- Do not create files or folders not listed here unless the user explicitly requests additions.
