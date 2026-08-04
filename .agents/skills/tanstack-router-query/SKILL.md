---
name: tanstack-router-query
description: Use when adding or changing TanStack Router routes, route loaders, router context, TanStack Query hooks, query keys, mutations, or cache invalidation in this Vite + React starter.
---

# TanStack Router + Query

Treat routing and server state as one boundary: routes compose and prefetch; feature modules own request functions, query keys, hooks, and mutations.

## Server-state flow

1. **Define ownership.** Put keys and reusable query options/functions in the owning feature's `api/` module. Use one stable root key for the feature:

   ```typescript
   export const projectsKeys = {
     all: ['projects'] as const,
     lists: () => [...projectsKeys.all, 'list'] as const,
     details: () => [...projectsKeys.all, 'detail'] as const,
     detail: (id: string | number) => [...projectsKeys.details(), id] as const,
   }
   ```

   Replace `projects` with the feature name. The ownership is complete when list, detail, and mutation consumers use the same key factory rather than inline strings.

2. **Prefetch at the route boundary.** A file route uses `createFileRoute`. When navigation needs server data, its loader calls `context.queryClient.ensureQueryData(featureQueryOptions())`; the query options/query function stay in the feature API module. The route component composes the feature page instead of fetching in render.

3. **Read in feature hooks.** API-backed component queries use `useEdenQuery` from `@/lib/api/use-eden-query` with the feature key factory. Keep request and response handling in the feature hook/API module. Use raw `useQuery` only for a non-Eden data source or an explicit infrastructure case.

4. **Invalidate after writes.** Each successful create, update, or delete mutation calls `queryClient.invalidateQueries({ queryKey: featureKeys.all })`. Include narrower detail keys when the mutation has a separate detail cache. The mutation is complete when every affected list/detail view refetches from the owning key family.

## Router typing and generated routes

- Pass `QueryClient` through `context` in `src/app/router.ts`; type the root context with `createRootRouteWithContext` in `src/routes/__root.tsx`.
- Keep the registration in `src/app/router.ts` so `Link`, `useNavigate`, and `useParams` remain type-safe:

  ```typescript
  declare module '@tanstack/react-router' {
    interface Register {
      router: typeof router
    }
  }
  ```

- Add or rename files under `src/routes/`, then let the TanStack Router plugin regenerate `src/routeTree.gen.ts` through `bun run dev` or `bun run build`. Review generated output; the route tree is not a hand-maintained source file.

**REQUIRED SUB-SKILL:** Use `eden-api-integration` when the query or mutation touches Eden Treaty, Better Auth, `VITE_API_URL`, response helpers, or generated backend types.

## Verification

- Route or router-context change: `bun run typecheck && bun run build`
- Query, mutation, or feature behavior: `bun run test`
- Lint-sensitive change: `bun run lint`

Finish when the route is thin, prefetching uses typed router context, feature hooks and keys own server state, every successful write invalidates its key family, generated routes are current, and relevant checks pass.
