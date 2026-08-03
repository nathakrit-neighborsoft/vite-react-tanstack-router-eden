---
name: frontend-boilerplate-development
description: Use when working in this Vite + React frontend boilerplate, especially before changing routes, feature modules, API/auth integration, project structure, scripts, or starter UI.
---

# Frontend Boilerplate Development

## Overview

This repository is a reusable frontend starter for bootstrapping new projects. Treat domain-specific modules as replaceable examples and preserve the shared architecture while the new project establishes its own domain.

## Before editing

1. Read `AGENTS.md` and `README.md` for project rules, commands, and current starter integrations.
2. Read the files related to the request and identify generated files, shared modules, and feature-local code.
3. Decide whether the change belongs in reusable boilerplate or in a replaceable sample feature.

Continue when the target files, affected conventions, and generated-file boundaries are clear.

## Conventions

- Use Bun for all scripts (`bun`, not `npm`, `yarn`, or `pnpm`).
- Keep TanStack Router route files thin; put feature logic in `src/features/<feature>/`.
- Keep shared components and utilities domain-neutral.
- Reuse existing UI primitives, hooks, API helpers, types, and tests before adding abstractions.
- Keep changes minimal and focused.
- Inspect `git status` before broad edits and preserve pre-existing worktree changes.
- Do not edit `src/routeTree.gen.ts` or `src/lib/api/server.d.ts`; regenerate them with the project tooling.

## Starter boundary

- Do not treat the current sample feature names as the identity of the repository.
- Put new product behavior under a feature module and keep routes as composition points.
- When replacing sample code, update its route, exports, tests, query keys, and types together.
- Avoid putting product-specific assumptions into `src/components/ui/`, `src/lib/`, or shared app setup.

## API and auth integration

- Keep `VITE_API_URL` origin-only; an empty value uses the same-origin proxy.
- Use `credentials: 'include'` for cookie-based calls.
- Use `handleEdenResponse` or `useEdenQuery` from `@/lib/api/` for typed API responses.
- Keep query keys in the owning feature and invalidate the relevant keys after mutations.
- Regenerate `src/lib/api/server.d.ts` with `bun run gen:types` when the backend contract changes.

## Verification

Run the smallest check that covers the change:

- Markdown/frontmatter-only → inspect the changed files and run `bun run format:check` when applicable.
- TypeScript → `bun run typecheck`.
- Feature behavior → `bun run test`.
- Linting → `bun run lint`.
- Routing, shared configuration, or production behavior → `bun run build`.

## Guardrails

- No new dependencies without asking.
- No commits or pushes without asking.
- Edit only files required by the request; do not modify, delete, or stage unrelated files.
- No generated-file edits, alternate package-manager lockfiles, or unrelated refactors.
