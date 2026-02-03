# Component Colocation in Next.js App Router

**Rule:** Place page-specific components in `_components/` directory local to the
page. Only move to global `src/components/` when reused across multiple pages.

**Reasoning:**

- Maximizes cohesion by keeping page-specific code together.
- Avoids premature abstraction - components are promoted to global only when
  proven to be reusable.
- `_` prefix ensures the folder is excluded from Next.js routing (private
  folder).

#### Directory Structure:

```
app/
├── (app)/
│   ├── dashboard/
│   │   ├── _components/          # Page-local components
│   │   │   ├── stats-card.tsx
│   │   │   ├── activity-feed.tsx
│   │   │   └── quick-actions.tsx
│   │   └── page.tsx
│   ├── settings/
│   │   ├── _components/
│   │   │   ├── settings-form.tsx
│   │   │   └── preference-toggle.tsx
│   │   └── page.tsx
│   └── layout.tsx
├── layout.tsx
src/
├── components/                   # Global/shared components ONLY
│   ├── ui/                       # UI library components (button, input, etc.)
│   ├── layout/                   # Layout components (sidebar, nav, header)
│   └── shared/                   # Multi-page shared components
```

#### When to Use Page-Local (`_components/`):

- Component is only used by this page
- Component is newly created (start local, promote later)
- Component contains page-specific business logic

#### When to Use Global (`src/components/`):

- Component is used by 2+ pages
- Layout components (sidebar, navigation, header, footer)
- UI library components (button, input, modal, etc.)
- Generic utilities (loading spinner, error boundary)

#### Import Path Rule:

- **Absolute path (`@/`)** - All imports use absolute path by default
- **Exception: page-local `_components/`** - Use relative path (`"./_components/..."`)

```tsx
// app/(app)/products/page.tsx
import { Button } from "@/components/ui/button";       // absolute
import { useAuth } from "@/hooks/use-auth";             // absolute
import { ProductList } from "./_components/product-list";    // relative (page-local)
import { ProductFilters } from "./_components/product-filters"; // relative (page-local)
```

#### Recommended Pattern:

```tsx
// app/(app)/products/page.tsx
import { ProductList } from "./_components/product-list";
import { ProductFilters } from "./_components/product-filters";

export default function ProductsPage() {
  return (
    <div>
      <ProductFilters />
      <ProductList />
    </div>
  );
}
```

```tsx
// app/(app)/products/_components/product-list.tsx
import { Card } from "@/components/ui/card";  // absolute for global
export function ProductList() {
  return <Card>{/* ... */}</Card>;
}
```

#### Promotion Workflow:

1. **Create locally first** - New component goes in `_components/`
2. **Identify reuse need** - Second page needs similar component
3. **Evaluate similarity** - Is it truly the same, or just similar?
4. **Promote if identical** - Move to `src/components/` only if logic is shared
5. **Keep separate if divergent** - Similar but different needs stay local

**Guidance:** Resist the urge to prematurely abstract. Three similar components
in three pages is acceptable if they might diverge. Premature abstraction
creates coupling that hinders independent evolution.
