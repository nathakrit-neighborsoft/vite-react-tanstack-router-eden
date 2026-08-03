# Project Agent Instructions

## Project

Reusable frontend boilerplate for bootstrapping new web and native projects. It provides a Vite + React starter with routing, server state, optional API/auth integration, reusable UI primitives, testing, linting, formatting, and Tauri targets. Any domain-specific feature currently in the source is starter/example code, not the product domain.

`README.md` describes the current starter setup and commands. Treat product-specific examples there as replaceable implementation details, not as project identity or a domain contract.

## Stack

- Bun 1.3 (package manager + TypeScript runner)
- Vite 8 + React 19
- TypeScript 5.9
- TanStack Router 1.95 (file-based, auto-code-splitting)
- TanStack Query 5 (server state, cache, mutations)
- `@elysiajs/eden` (typed RPC client for the starter API integration)
- `better-auth/react` (optional auth client state)
- Tailwind CSS v4 + Radix UI primitives (shadcn-style)
- Vitest + Testing Library (tests)
- oxlint (linter) + Prettier (formatter)
- Tauri 2 (optional desktop and Android targets)

## Source layout

```
src/
  app/                 Router setup + shared app registration
  components/ui/       Reusable UI primitives
  features/<feature>/   Feature-specific UI, hooks, API code, and tests
  lib/api/              API client, helpers, and generated server types
  lib/env.ts            Environment defaults and validation
  routes/               Thin TanStack Router route files
  routeTree.gen.ts      Generated route tree
scripts/                Project scripts such as API type generation
src-tauri/              Optional Tauri desktop and mobile wrapper
```

## Working rules

- Use Bun for all scripts (`bun run dev`, `bun run build`, `bun run test`, etc.).
- Keep changes minimal and focused; do not refactor beyond the task.
- Keep route files (`src/routes/`) thin. Put feature logic in `src/features/<feature>/`.
- Keep shared UI and utilities domain-neutral. Put product-specific behavior in a feature module.
- Treat existing domain-specific sample modules as replaceable when starting a new project. Rename or remove them only when the task requires it.
- Reuse existing components, utilities, hooks, types, and project APIs before adding abstractions or dependencies.
- Inspect `git status` before broad edits. Preserve pre-existing worktree changes and do not modify, delete, or stage unrelated files.
- Do not commit or push unless explicitly requested.
- Never edit generated files directly:
  - `src/routeTree.gen.ts` — regenerate through the TanStack Router plugin.
  - `src/lib/api/server.d.ts` — regenerate with `bun run gen:types`.

## Starter API and auth integration

- `VITE_API_URL` is origin-only (for example, `http://localhost:3050`). Do not append `/api`; Eden Treaty adds route paths itself.
- An empty `VITE_API_URL` uses the same-origin Vite/nginx proxy.
- The current dev proxy maps `/api/*` to the configured backend API and `/auth/*` to the backend auth endpoints.
- Use `credentials: 'include'` for cookie-based API/auth calls.
- Use `handleEdenResponse` or `useEdenQuery` from `@/lib/api/` for typed API responses.
- Keep query keys in each feature's `api/keys.ts` and invalidate the relevant feature keys after mutations.

## Routing and data rules

- Register the router type in `src/app/router.ts` so `Link`, `useNavigate`, and `useParams` remain type-safe.
- Use `context.queryClient.ensureQueryData` in route loaders when a route needs prefetched server data.
- Keep server-state fetching and mutations in feature hooks or API modules rather than route components.

## Verification

- Documentation-only changes → check Markdown/frontmatter and run `bun run format:check` when formatting is affected.
- TypeScript changes → `bun run typecheck`.
- Feature changes → `bun run test`.
- Linting changes or before finishing a broader change → `bun run lint`.
- Routing, shared configuration, or production behavior changes → `bun run build`.
- Backend contract changes → `bun run gen:types` before typechecking.
- Do not add npm, Yarn, or pnpm lockfiles; keep the Bun lockfile authoritative.
