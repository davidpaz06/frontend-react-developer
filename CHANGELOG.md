<!-- markdownlint-disable MD024 -->

# Changelog

All notable changes to the `frontend-react-developer` skill are documented here.

This file follows [Keep a Changelog](https://keepachangelog.com/en/1.1.0/) and [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [2.0.2] - 2026-09-02

### Added

- `references/actions.md`, documenting when and how to subdivide `adapters/hooks/actions/` by domain.
- Anti-pattern #7, “Bloated Actions Folder”, with before-and-after layouts.
- An action-folder entry in `references/patterns.md`.
- Subfolder naming guidance in `references/naming-rules.md`.

### Changed

- Refactor and Implementation workflows now consult `references/actions.md` when the action-hook folder grows beyond its split trigger.
- `references/project-structure-full.md` now documents the flat-to-subdivided action-folder convention.
- `README.md` now describes the current conventions, installation options, requirements, and complete repository structure.

## [2.0.1] - 2026-07-14

Initial published release, previously versioned internally through 1.2.1.

### Added

- The `SKILL.md` senior frontend-engineer persona and task workflows.
- Layered architecture conventions for `entities/`, `api/`, `adapters/`, `infrastructure/`, `presentation/`, and `shared/`.
- Reference patterns for project structure, naming, anti-patterns, component flow, hooks, context, authentication, routing, loaders, forms, and Vite configuration.
- `README.md` with skill overview and installation instructions.

[2.0.2]: https://github.com/davidpaz06/frontend-react-developer/releases/tag/v2.0.2
[2.0.1]: https://github.com/davidpaz06/frontend-react-developer/releases/tag/v2.0.1
