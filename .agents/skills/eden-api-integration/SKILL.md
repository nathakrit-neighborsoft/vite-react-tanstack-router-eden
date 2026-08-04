---
name: eden-api-integration
description: Use when adding or changing Eden Treaty requests, Better Auth client configuration, VITE_API_URL, API response handling, generated server types, or authenticated backend integration in this Vite + React starter.
---

# Eden API Integration

Use a typed boundary: backend contract → generated `App` type → shared `api` client → feature hook or mutation → feature-owned query keys.

## Apply by branch

- API client, auth client, environment, proxy, or backend contract → use this skill.
- Route loaders, router context, query-key shape, or cache ownership → also use `tanstack-router-query`.
- Boilerplate boundaries and replaceable sample features → use `frontend-boilerplate-development`.

## Integration steps

1. **Inspect the boundary.** Read `src/lib/env.ts`, `src/lib/api/client.ts`, the owning feature's `api/keys.ts`, and its hooks. Identify whether `src/lib/api/server.d.ts` is stale. Continue when the endpoint, owner, and generated-file boundary are known.

2. **Sync the contract.** After a backend contract change, run `bun run gen:types`. Treat `src/lib/api/server.d.ts` as generated output: the command updates it, and the backend must be reachable for new routes to appear. Continue when the generated `App` type contains the endpoint and inputs you need.

3. **Use the shared client.** Feature code imports `api` from `@/lib/api/client`, `handleEdenResponse` from `@/lib/api/eden-helpers`, and `useEdenQuery` from `@/lib/api/use-eden-query`. Keep endpoint calls and response unwrapping in feature hooks/API modules so routes and components stay thin.

4. **Preserve cookie auth.** `VITE_API_URL` is an origin only (`http://localhost:3050`), never an `/api` path. An empty value uses the same-origin Vite/nginx proxy. Eden appends the server route path, and the shared client sends `credentials: 'include'`. Keep Better Auth's client in `src/features/auth/auth-client.ts` with `baseURL: env.VITE_API_URL || undefined`.

5. **Close the cache loop.** Put keys in the owning feature's `api/keys.ts`. Every successful create, update, or delete mutation invalidates that feature's root key, such as `featureKeys.all`, so lists and details cannot remain stale.

## Current proxy map

| Browser path | Dev target                         |
| ------------ | ---------------------------------- |
| `/api/*`     | `http://localhost:3050/*`          |
| `/auth/*`    | `http://localhost:3050/api/auth/*` |

The map lives in `vite.config.ts`; production proxy behavior is configured separately in `nginx.conf`.

## Verification

- Contract or API TypeScript change: `bun run gen:types && bun run typecheck`
- Feature behavior or mutation change: `bun run test`
- Lint-sensitive change: `bun run lint`
- Routing, proxy, or production configuration change: `bun run build`

The integration is complete when the generated contract is current, the feature uses the shared client/helpers, cookie and URL behavior match the proxy, every mutation invalidates its owning keys, and the checks relevant to the changed branch pass.
