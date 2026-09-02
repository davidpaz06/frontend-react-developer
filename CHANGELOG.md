<!-- markdownlint-disable MD024 -->

# Changelog

All notable changes to the `frontend-react-developer` skill are documented here.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

### Added

- `references/actions.md` — rules for when and how to subdivide `adapters/hooks/actions/`
  into per-domain subfolders (split trigger: 8+ hook files, or hooks for 3+ distinct entities).
- Anti-pattern #7 "Bloated Actions Folder" in `references/anti-patterns.md`, with
  before/after folder layouts.
- `references/patterns.md`: new "Action Folder Subdivision" row pointing at `actions.md`.
- `references/naming-rules.md`: subfolder naming guidance for a subdivided `actions/`
  (one folder per entity, matching the `api/clients/<entity>` base name).

### Changed

- `SKILL.md` Refactor and Feature workflows now reference `actions.md` when the target
  is a bloated `adapters/hooks/actions/` folder.
- `SKILL.md` reference table lists `references/actions.md`.
- `references/project-structure-full.md`: `adapters/hooks/actions/` description notes it
  stays flat while small and subdivides past the split trigger.

## [2.0.1] - 2026-07-14

Initial published release. Previously versioned internally through 1.2.1.

### Added

- `SKILL.md` — Vite + React + TypeScript + Tailwind senior-engineer persona with
  task-type workflows (Scaffolding, Refactor, Feature, Debug, Review, Q&A).
- Layered architecture convention: `entities/`, `api/`, `adapters/`, `infrastructure/`,
  `presentation/`, `shared/`.
- `references/project-structure-full.md` — directory tree for full projects (backend + auth).
- `references/project-structure-simple.md` — directory tree for simple projects (no backend).
- `references/naming-rules.md` — file naming, colocated prop interfaces, skeleton naming,
  cheat sheet.
- `references/anti-patterns.md` — checklist of AI-generated anti-patterns (bloated state,
  prop drilling, prop explosion, boolean flags, fat hooks, fetch in component).
- `references/component-flow.md` — data flow across layers.
- `references/custom-hooks.md` — action-hook conventions and the ~80-line fat-hook rule.
- `references/context-pattern.md` — React Context usage.
- `references/auth-pattern.md` — login, logout, protected routes, token refresh, AuthContext.
- `references/routing.md` — React Router v7 (`react-router` package, no `defer()`).
- `references/loader-pattern.md` — deferred route loading with `Suspense` + `Await`.
- `references/form-validation.md` — form validation pattern.
- `references/patterns.md` — index of pattern reference files.
- `references/vite-config.md` — `@` alias, tsconfig paths, Tailwind plugin setup.
- `README.md` — skill overview and installation (personal skill or project submodule).

[Unreleased]: https://github.com/davidpaz06/frontend-react-developer/compare/v2.0.1...HEAD
[2.0.1]: https://github.com/davidpaz06/frontend-react-developer/releases/tag/v2.0.1
