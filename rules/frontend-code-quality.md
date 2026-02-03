# Frontend Code Quality

## 1. Colocate Files That Change Together
Cohesion: Place files modified together in the same directory. Shared code at project root, domain-specific code under `domains/<Domain>/`.

```text
src/
  components/    # shared
  hooks/
  utils/
  domains/
    Domain1/
      components/
      hooks/
      utils/
    Domain2/
      components/
      hooks/
      utils/
```

## 2. Left-to-Right Comparisons
Readability: Write range conditions like mathematical inequalities, from lower bound to upper bound.

```typescript
if (minPrice <= price && price <= maxPrice) {
  console.log("Affordable price");
}
```

## 3. Name Complex Conditions
Readability: Extract complex boolean expressions into named variables to reduce cognitive load.

```typescript
const isSameCategory = category.id === targetCategory.id;
const isPriceInRange = product.prices.some(
  (price) => price >= minPrice && price <= maxPrice
);

return isSameCategory && isPriceInRange;
```

## 4. Form Cohesion
Cohesion: Choose field-level validation (inline `register` rules) for simple forms, or form-level validation (Zod schema + `zodResolver`) when fields have cross-dependencies.

```tsx
// Form-level: unified schema
const schema = z.object({
  name: z.string().min(1, "Please enter your name."),
  email: z.string().min(1, "Please enter your email.").email("Invalid email.")
});

const { register, formState: { errors }, handleSubmit } = useForm({
  resolver: zodResolver(schema)
});
```

## 5. Reveal Hidden Logic
Predictability: Keep only logic predictable from a function's name, parameters, and return type. Separate side effects like logging.

```typescript
async function fetchBalance(): Promise<number> {
  const balance = await http.get<number>("...");
  return balance;
}

// Logging separated to call site
const balance = await fetchBalance();
logging.log("balance_fetched");
await syncBalance(balance);
```

## 6. Distinguish Wrapper Functions from Library Originals
Predictability: When wrapping a library function, use a distinct name that communicates the added behavior.

```typescript
import { http as httpLibrary } from "@some-library/http";

export const httpService = {
  async getWithAuth(url: string) {
    const token = await fetchToken();
    return httpLibrary.get(url, {
      headers: { Authorization: `Bearer ${token}` }
    });
  }
};
```

## 7. Eliminate Props Drilling with Composition
Coupling: Use the Composition pattern instead of passing props through intermediate components.

```tsx
function ItemEditModal({ open, items, recommendedItems, onConfirm, onClose }) {
  const [keyword, setKeyword] = useState("");

  return (
    <Modal open={open} onClose={onClose}>
      <Input value={keyword} onChange={(e) => setKeyword(e.target.value)} />
      <Button onClick={onClose}>Close</Button>
      <ItemEditList
        keyword={keyword}
        items={items}
        recommendedItems={recommendedItems}
        onConfirm={onConfirm}
      />
    </Modal>
  );
}
```

## 8. Extract Cross-Cutting Concerns
Readability: Separate cross-cutting logic (auth guards, redirects) into wrapper components or HOCs.

```tsx
function AuthGuard({ children }) {
  const status = useCheckLoginStatus();

  useEffect(() => {
    if (status === "LOGGED_IN") {
      location.href = "/home";
    }
  }, [status]);

  return status !== "LOGGED_IN" ? children : null;
}

// Usage
<AuthGuard>
  <LoginStartPage />
</AuthGuard>
```

## 9. Name Magic Numbers
Readability, Cohesion: Declare magic numbers as named constants that convey context.

```typescript
const ANIMATION_DELAY_MS = 300;

async function onLikeClick() {
  await postLike(url);
  await delay(ANIMATION_DELAY_MS);
  await refetchPostLike();
}
```

## 10. Separate Code Paths That Don't Run Together
Readability: Split branching logic into distinct components, each handling a single case.

```tsx
function SubmitButton() {
  const isViewer = useRole() === "viewer";
  return isViewer ? <ViewerSubmitButton /> : <AdminSubmitButton />;
}

function ViewerSubmitButton() {
  return <TextButton disabled>Submit</TextButton>;
}

function AdminSubmitButton() {
  useEffect(() => { showButtonAnimation(); }, []);
  return <Button type="submit">Submit</Button>;
}
```

## 11. Replace Nested Ternaries with If Statements
Readability: Use if statements or IIFE instead of nested ternary operators.

```typescript
const status = (() => {
  if (ACondition && BCondition) return "BOTH";
  if (ACondition) return "A";
  if (BCondition) return "B";
  return "NONE";
})();
```

## 12. Allow Duplication When Behavior May Diverge
Coupling: Do not prematurely abstract shared code if behavior is likely to differ across contexts. Duplicate code is acceptable when coupling would be worse.

## 13. One Hook, One Responsibility
Coupling, Readability: Split combined hooks into single-responsibility hooks with clear names.

```typescript
export function useCardIdQueryParam() {
  const [cardId, _setCardId] = useQueryParam("cardId", NumberParam);

  const setCardId = useCallback((cardId: number) => {
    _setCardId({ cardId }, "replaceIn");
  }, []);

  return [cardId ?? undefined, setCardId] as const;
}
```

## 14. Unify Return Types for Similar Functions
Predictability: Functions with similar roles should return the same shape. Hooks calling server APIs return `Query` objects. Validation functions return `{ ok, reason? }`.

```typescript
function checkIsNameValid(name: string) {
  if (name.length === 0) return { ok: false, reason: "Name cannot be empty." };
  if (name.length >= 20) return { ok: false, reason: "Name too long." };
  return { ok: true };
}
```

## 15. Manage Permissions as a Policy Object
Readability: Consolidate permission logic into a single object to reduce scattered conditionals.

```tsx
function Page() {
  const user = useUser();
  const policy = {
    admin: { canInvite: true, canView: true },
    viewer: { canInvite: false, canView: true }
  }[user.role];

  return (
    <div>
      <Button disabled={!policy.canInvite}>Invite</Button>
      <Button disabled={!policy.canView}>View</Button>
    </div>
  );
}
```
