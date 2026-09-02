# frontend-react-developer

Skill for Vite + React + TypeScript + Tailwind CSS projects. It provides a senior-frontend-engineer workflow for research, debugging, scaffolding, refactoring, and feature implementation, with architectural conventions designed to keep codebases coherent and maintainable.

## What it covers

- Layered architecture: `entities/`, `api/`, `adapters/`, `infrastructure/`, `presentation/`, and `shared/`.
- Vite configuration with the `@` alias mapped to `src` and Tailwind through `@tailwindcss/vite`.
- React Router v7 routing, including deferred route loaders with `Suspense` and `Await`.
- API integration using native `fetch`, action hooks, optimistic mutations, and layer-specific error handling.
- Cookie-based authentication using `httpOnly` cookies and `credentials: 'include'`.
- Form validation with Zod and React Hook Form.
- State-management guidance for React Context and Zustand.
- Naming, component-prop colocation, custom-hook, and handler-file conventions.
- A proactive anti-pattern checklist for bloated state, prop drilling, prop explosion, boolean flags, fat hooks, fetching inside components, and oversized action-hook folders.

## Core conventions

- Use native `fetch` rather than axios.
- Keep entity interfaces in `entities/`, one file per model and without business logic.
- Colocate a component's props interface with the component itself (for example, `ButtonProps.ts` next to `Button.tsx`).
- Put mutations in action hooks under `adapters/hooks/actions/`; split them by entity once the folder has 8+ hooks or covers 3+ entities.
- Let API clients throw errors, action hooks manage loading state, and components handle the UX response.
- Defer loader data by default; only block navigation for data the page cannot render without.
- Use `.webp` images, `.webm` video, and `.woff2` fonts.

All conventions are defaults and can be overridden for a project or conversation.

## Installation

### Personal installation

```bash
git clone https://github.com/davidpaz06/frontend-react-developer.git ~/.claude/skills/frontend-react-developer
```

### Project-only installation

```bash
git submodule add https://github.com/davidpaz06/frontend-react-developer.git .claude/skills/frontend-react-developer
```

### From a downloaded `.zip` or `.skill` file

```bash
# A .skill file is a ZIP archive with a different extension.
mv frontend-react-developer.skill frontend-react-developer.zip
unzip frontend-react-developer.zip -d ~/.claude/skills/frontend-react-developer
```

PowerShell:

```powershell
Rename-Item -Path "frontend-react-developer.skill" -NewName "frontend-react-developer.zip"
Expand-Archive -Path "frontend-react-developer.zip" -DestinationPath "$HOME\.claude\skills\frontend-react-developer" -Force
```

Claude Code detects skill changes without a restart.

## Updating

```bash
cd ~/.claude/skills/frontend-react-developer
git pull
```

## Repository structure

```text
frontend-react-developer/
|-- SKILL.md
|-- README.md
|-- CHANGELOG.md
`-- references/
    |-- actions.md
    |-- anti-patterns.md
    |-- auth-pattern.md
    |-- component-flow.md
    |-- context-pattern.md
    |-- custom-hooks.md
    |-- form-validation.md
    |-- loader-pattern.md
    |-- naming-rules.md
    |-- patterns.md
    |-- project-structure-full.md
    |-- project-structure-simple.md
    |-- routing.md
    `-- vite-config.md
```

Start with [`SKILL.md`](SKILL.md) for the complete task workflows. [`references/patterns.md`](references/patterns.md) is the index for the implementation patterns.

## Requirements

- Vite + React + TypeScript
- Tailwind CSS through `@tailwindcss/vite`
- React Router v7 via `react-router`
