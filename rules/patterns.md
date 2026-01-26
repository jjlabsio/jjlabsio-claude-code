# Common Patterns

## API Response Format

```typescript
interface ApiResponse<T> {
  success: boolean
  data?: T
  error?: string
  meta?: {
    total: number
    page: number
    limit: number
  }
}
```

## Custom Hooks Pattern

```typescript
export function useDebounce<T>(value: T, delay: number): T {
  const [debouncedValue, setDebouncedValue] = useState<T>(value)

  useEffect(() => {
    const handler = setTimeout(() => setDebouncedValue(value), delay)
    return () => clearTimeout(handler)
  }, [value, delay])

  return debouncedValue
}
```

## Repository Pattern

```typescript
interface Repository<T> {
  findAll(filters?: Filters): Promise<T[]>
  findById(id: string): Promise<T | null>
  create(data: CreateDto): Promise<T>
  update(id: string, data: UpdateDto): Promise<T>
  delete(id: string): Promise<void>
}
```

## Skeleton Projects

When implementing new functionality:
1. Search for battle-tested skeleton projects
2. Use parallel agents to evaluate options:
   - Security assessment
   - Extensibility analysis
   - Relevance scoring
   - Implementation planning
3. Clone best match as foundation
4. Iterate within proven structure

## HTTP Requests

- **Default**: Use `ky` for all HTTP requests
- **Exception**: Native `fetch` only for Next.js caching options

```typescript
// Default: ky
const data = await ky.get('api/users').json<User[]>()

// Exception: Next.js Server Components with caching
fetch(url, { next: { revalidate: 3600 }, cache: 'force-cache' })
```

Why `ky`:
- Auto-throws on non-2xx responses
- Clean JSON parsing (`.json<T>()`)
- Built-in hooks, retry, timeout

## Data Fetching

- **Required**: `@tanstack/react-query` for all API calls in React components
- **Forbidden**: `useState` + `fetch` combination

| Operation | Hook |
|-----------|------|
| GET | `useQuery` |
| POST/PUT/DELETE | `useMutation` |

```typescript
// GET
const { data, isLoading } = useQuery({
  queryKey: ['users', id],
  queryFn: () => api.getUser(id),
})

// Mutation with feedback
const mutation = useMutation({
  mutationFn: api.createUser,
  onSuccess: () => toast.success('Created'),
  onError: (e) => toast.error(e.message),
})
```