# frontend-react-developer

Claude Code skill for Vite + React + TypeScript + Tailwind CSS projects. Defines a layered project structure, naming conventions, routing and data-loading patterns (React Router v7, deferred loading), and a maintainability checklist to catch common AI-generated anti-patterns (bloated state, props explosion, misplaced error handling, etc.).

## Key points

- Layered architecture: `entities/`, `api/`, `adapters/`, `infrastructure/`, `presentation/`, `shared/`.
- Entity interfaces live in `entities/`, one file per model, no business logic.
- Component prop interfaces are colocated with their component (`ButtonProps.ts` next to `Button.tsx`), not centralized.
- Mutations go through action hooks (`adapters/hooks/actions/`), not plain `.actions.ts` files.
- Error handling is layer-specific: API client throws, action hook only tracks loading state, the component owns the try/catch and the UX response.
- Route loaders default to deferred loading (`Suspense` + `Await`) instead of blocking navigation; blocking is reserved for data a page cannot render without.
- Built for React Router v7 (`react-router` package, no `defer()`).
- Includes an anti-patterns checklist checked during code generation, not only on explicit refactor requests.
- All conventions are defaults — you can override any of them mid-conversation.

## Installation

### Personal (applies to all your projects)

```bash
git clone https://github.com/davidpaz06/frontend-react-developer.git ~/.claude/skills/frontend-react-developer
```

### Project-only (pinned to one repo)

```bash
git submodule add https://github.com/davidpaz06/frontend-react-developer.git .claude/skills/frontend-react-developer
```

### From a downloaded .zip or .skill file

```bash
# rename if needed: a .skill file is a zip with a different extension
mv frontend-react-developer.skill frontend-react-developer.zip
unzip frontend-react-developer.zip -d ~/.claude/skills/frontend-react-developer
```

PowerShell:

```powershell
Rename-Item -Path "frontend-react-developer.skill" -NewName "frontend-react-developer.zip"
Expand-Archive -Path "frontend-react-developer.zip" -DestinationPath "$HOME\.claude\skills\frontend-react-developer" -Force
```

No restart needed — Claude Code picks up skill changes live.

## Updating

```bash
cd ~/.claude/skills/frontend-react-developer
git pull
```

## Structure

```
frontend-react-developer/
├── SKILL.md
└── references/
    ├── naming-rules.md
    ├── patterns.md
    ├── project-structure-full.md
    ├── project-structure-simple.md
    └── anti-patterns.md
```

## Requirements

- Vite + React + TypeScript
- Tailwind CSS (via @tailwindcss/vite)
- React Router v7 (react-router, not react-router-dom)
