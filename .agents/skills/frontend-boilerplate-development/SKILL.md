---
name: frontend-boilerplate-development
description: Use when changing the reusable Vite + React starter's routes, feature modules, shared UI, app setup, scripts, or replaceable sample code.
---

# Frontend Boilerplate Development

Treat this repository as a starter, not as the identity of its current sample domain. Shared code stays domain-neutral; product behavior lives in a feature module.

## Scope decision

1. Read `AGENTS.md`, the relevant `README.md` sections, and the target files. Record generated-file boundaries and existing tests before editing.
2. Classify each change:
   - `src/routes/` — thin route composition and route-specific loader wiring.
   - `src/features/<feature>/` — product UI, hooks, API modules, query keys, types, and tests.
   - `src/components/ui/`, `src/lib/`, and `src/app/` — reusable, domain-neutral building blocks.
   - `src/routeTree.gen.ts` and `src/lib/api/server.d.ts` — generated output.
3. Choose the smallest branch:
   - Starter maintenance → preserve replaceable sample modules unless removal is requested.
   - New product behavior → add or replace a complete feature unit and update its route, exports, tests, keys, and types together.

The scope is clear when every changed file has an owner, every sample-domain reference that must move is listed, and generated outputs have a regeneration command.

## Shared boundaries

- Keep routes as composition points; place feature logic under `src/features/<feature>/`.
- Reuse existing UI primitives, hooks, helpers, types, and tests before introducing new abstractions or dependencies.
- Use Bun for project commands and keep `bun.lock` authoritative.
- Keep product labels, API assumptions, and feature-specific state out of shared UI, `src/lib/`, and shared app registration.

**REQUIRED SUB-SKILL:** Use `eden-api-integration` when the change touches Eden Treaty, Better Auth, `VITE_API_URL`, API helpers, generated server types, or backend integration.

**REQUIRED SUB-SKILL:** Use `tanstack-router-query` when the change adds routes, loaders, router context, query keys, mutations, or server-state fetching.

## Generated boundaries

- Add, rename, or remove route files, then let the TanStack Router plugin regenerate `src/routeTree.gen.ts` through the normal Vite command (`bun run dev` or `bun run build`).
- Regenerate `src/lib/api/server.d.ts` with `bun run gen:types` after a backend contract change.
- Keep generated files as tool output; review their diff rather than hand-editing them.

## Verification

- TypeScript or feature code: `bun run typecheck` and the relevant `bun run test` scope.
- Route, shared configuration, or production behavior: `bun run build`.
- Lint-sensitive changes: `bun run lint`.
- Documentation/frontmatter changes: `bun run format:check` when formatting is affected.

Finish when the requested behavior is implemented in the correct boundary, related feature files agree, generated outputs are current, relevant checks pass, and unrelated work remains untouched.
