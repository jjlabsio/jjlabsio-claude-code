---
paths:
  - "**/*.tsx"
  - "**/*.jsx"
---
# TypeScript/JavaScript Frontend

## Directory Structure: Colocation by Domain

Group files that change together in the same directory. Organize by domain/feature, not by type.

```text
// BAD: by type — hides dependencies, orphans unused code
src/components/  src/hooks/  src/utils/  src/constants/

// GOOD: shared + domain-scoped
src/
├── components/   # shared across entire project
├── hooks/
├── utils/
└── domains/
    ├── Checkout/
    │   ├── components/
    │   ├── hooks/
    │   └── utils/
    └── Profile/
        ├── components/
        ├── hooks/
        └── utils/
```

- **Shared code** (`src/components/`, `src/hooks/`, etc.): used across multiple domains
- **Domain code** (`src/domains/<Name>/`): only used within that domain
- Cross-domain imports (e.g. `../../OtherDomain/hooks/useFoo`) are a code smell — extract to shared or reconsider boundaries
- Deleting a feature = deleting its domain directory. No orphaned files

## API Handling

- **Data fetching**: Use TanStack Query (`@tanstack/react-query`) — no raw `fetch` or custom `useQuery` hooks
- **HTTP client (CSR)**: Use `ky` with TanStack Query for client-side API calls
- **HTTP client (Server)**: Use native `fetch` — Next.js extends it with caching/revalidation
- Query keys: array form `['resource', id]` for granular cache invalidation
- Mutations: use `useMutation` + `queryClient.invalidateQueries` on success
