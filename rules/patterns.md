# Common Patterns

## API Response Format

```typescript
interface ApiResponse<T> {
  success: boolean;
  data?: T;
  error?: string;
  meta?: {
    total: number;
    page: number;
    limit: number;
  };
}
```

## Custom Hooks Pattern

```typescript
export function useDebounce<T>(value: T, delay: number): T {
  const [debouncedValue, setDebouncedValue] = useState<T>(value);

  useEffect(() => {
    const handler = setTimeout(() => setDebouncedValue(value), delay);
    return () => clearTimeout(handler);
  }, [value, delay]);

  return debouncedValue;
}
```

## Repository Pattern

```typescript
interface Repository<T> {
  findAll(filters?: Filters): Promise<T[]>;
  findById(id: string): Promise<T | null>;
  create(data: CreateDto): Promise<T>;
  update(id: string, data: UpdateDto): Promise<T>;
  delete(id: string): Promise<void>;
}
```

## HTTP Requests

- **Default**: Use `ky` (client components, Server Components)
- **Exception 1**: Native `fetch` for Next.js caching options
- **Exception 2**: Native `fetch` for API Routes/Route Handlers/server utilities (ky is ESM-only, incompatible with Vercel serverless bundling)

```typescript
// Default: ky (client components, Server Components)
const data = await ky.get("api/users").json<User[]>();

// Exception 1: Next.js caching
fetch(url, { next: { revalidate: 3600 }, cache: "force-cache" });

// Exception 2: API Routes / Route Handlers (serverless functions)
// app/api/webhook/route.ts - use native fetch, NOT ky
const res = await fetch("https://slack.com/api/chat.postMessage", {
  method: "POST",
  headers: { "Content-Type": "application/json" },
  body: JSON.stringify(payload),
});
if (!res.ok) throw new Error(`Slack API error: ${res.status}`);
```

Why `ky` (client/Server Components):

- Auto-throws on non-2xx responses
- Clean JSON parsing (`.json<T>()`)
- Built-in hooks, retry, timeout

Why NOT `ky` (serverless functions):

- ESM-only package causes FUNCTION_INVOCATION_FAILED on Vercel
- Applies to: `app/api/**`, server utilities imported by API routes

## Data Fetching

- **Required**: `@tanstack/react-query` for all API calls in React components
- **Forbidden**: `useState` + `fetch` combination

| Operation       | Hook          |
| --------------- | ------------- |
| GET             | `useQuery`    |
| POST/PUT/DELETE | `useMutation` |

```typescript
// GET
const { data, isLoading } = useQuery({
  queryKey: ["users", id],
  queryFn: () => api.getUser(id),
});

// Mutation with feedback
const mutation = useMutation({
  mutationFn: api.createUser,
  onSuccess: () => toast.success("Created"),
  onError: (e) => toast.error(e.message),
});
```
