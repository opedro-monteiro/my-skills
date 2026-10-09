---
name: react-patterns
description: React design patterns for this codebase — early returns, state colocation, derived state, custom hooks, splitting oversized components, composition over prop explosion, and type boundaries (mapper → DTO). Use this skill whenever writing, refactoring, or reviewing any .tsx component, custom hook, or client-side logic in this project, even if the user does not mention "patterns" — e.g. "cria um componente", "refatora esse componente", "esse arquivo está gigante", "adiciona um filtro/modal/tabela", "extrai um hook", "revisa esse componente", or any new screen under features/*/components.
---

# React patterns

Apply these patterns whenever you write or touch TSX and component logic. They share one idea: **do the least complex thing that still does the job** — don't store what you can calculate, don't lift what you can colocate, don't nest what you can flatten, don't configure what you can compose.

| Pattern | One rule |
|---|---|
| 1. Early returns | One level of concern at a time |
| 2. State colocation | One owner per piece of state |
| 3. Derived state | One source of truth |
| 4. Custom hooks | One home for logic |
| 5. Split big components | One responsibility per component |
| 6. Composition | Structure in JSX, not in props |
| 7. Type boundaries | UI never touches raw backend shapes |

Full before/after examples for each pattern live in `references/examples.md` — read the relevant section when you need a concrete model to follow or when explaining a refactor to the user.

---

## 1. Early returns — flatten the JSX

Nested ternaries for loading / error / empty / permission states turn the render into a pyramid that's hard to read and easy to break. Handle each edge case at the top and return immediately, so the main render only runs when data is ready.

```tsx
export function OrderSummary({ order, isLoading, error }: OrderSummaryProps) {
  if (isLoading) return <OrderSummarySkeleton />;
  if (error) return <ErrorState message={error.message} />;
  if (!order) return <EmptyState />;

  return (
    <section>
      <h2>{order.customerName}</h2>
      {order.isPaid && <PaidBadge />}
    </section>
  );
}
```

Think of it as a bouncer at the door: each guard clause rejects one case, and whoever reaches the bottom is good to go. A single `&&` or one ternary inside the final JSX is fine; a ternary nested inside another ternary is the signal to refactor.

## 2. State colocation — keep state where it's used

Lifting every `useState` to the top component re-renders the whole tree on each change and forces props through layers that don't care about them. Put state in the lowest component that uses it. Lift only when two or more siblings genuinely need to share it — and then lift only to their closest common parent.

```tsx
function Navbar() {
  const [isMenuOpen, setIsMenuOpen] = useState(false);
  // ...
}

function SearchBar() {
  const [query, setQuery] = useState("");
  // ...
}
```

Related: in this project, state that should survive a reload or be shareable (filters, pagination, active tab) belongs in the URL via `nuqs` (see `features/sales/hooks/use-sales-filters.ts`), and server data belongs in React Query or Server Components — not in `useState`.

## 3. Derived state — don't store what you can calculate

Two pieces of state that must stay in sync will eventually drift, and the UI starts lying. If a value can be computed from props or existing state, compute it during render.

```tsx
const [items, setItems] = useState<CartItem[]>([]);

const selectedItems = items.filter((item) => item.selected);
const selectedCount = selectedItems.length;
const total = selectedItems.reduce((sum, item) => sum + item.price, 0);
```

Red flags: a `useState` whose setter is only called right after another setter, or a `useEffect` whose only job is `setX(f(y))`. Both mean `x` should be derived. Calculations like filter/count/sum are cheap; reach for `useMemo` only when profiling shows a real cost.

## 4. Custom hooks — give logic a home

When a component mixes several `useState`/`useEffect` blocks for one behavior, or the same stateful logic appears in two components, extract a hook. The component then describes *what the user sees*; the hook describes *how the behavior works*.

```tsx
export function OrdersTable({ orders }: OrdersTableProps) {
  const table = useOrdersTable(orders);

  return (
    <>
      <SearchInput value={table.search} onChange={table.setSearch} />
      <Table rows={table.visibleOrders} />
      <Pagination page={table.page} onChange={table.setPage} />
    </>
  );
}
```

Project conventions:
- File name in kebab-case with the `use-` prefix: `use-orders-table.ts`, `use-dashboard-filters.ts`.
- Location: `src/features/<feature>/hooks/` when feature-specific; `src/shared/hooks/` only when it has no business logic and is reused across features.
- Split hooks by concern rather than one mega-hook: `use-dashboard-data.ts`, `use-dashboard-filters.ts`, `use-dashboard-export.ts`.
- For server data, wrap React Query (`useQuery`/`useMutation`) inside the hook instead of hand-rolling `useEffect` + `fetch` + loading/error state.

Don't extract `useSomething()` for three lines of logic. Extract when the behavior has become a meaningful concept of its own — something you can name and reason about separately.

## 5. Split components that do too many jobs

A component that grows to own fetching, filters, pagination, a modal, validation, permissions, sorting and export becomes a 1,000-line file nobody wants to touch. When a component holds several unrelated responsibilities, break it into named pieces plus a hook:

```
Dashboard
├── DashboardHeader
├── DashboardFilters
├── StatsCards
├── OrdersTable
├── OrderDetailsModal
└── useDashboardData
```

```tsx
export function Dashboard() {
  const { orders } = useDashboardData();

  return (
    <DashboardLayout>
      <DashboardHeader />
      <StatsCards orders={orders} />
      <DashboardFilters />
      <OrdersTable orders={orders} />
    </DashboardLayout>
  );
}
```

Good signals to split: the file passes ~200 lines, you need to scroll to see the JSX, or you can describe the component only with "and … and … and". Place the pieces under the feature's `components/` folder, grouped by screen (`admin/list/`, `admin/create/`).

## 6. Composition over prop explosion

Reusable components that grow a boolean/config prop for every new design (`showIcon`, `iconType`, `showCancelButton`, `confirmButtonDanger`, ...) turn into configuration files. Instead, expose small parts and let the caller assemble the structure with `children`.

```tsx
<Dialog open={open} onOpenChange={setOpen}>
  <DialogContent>
    <DialogHeader>
      <DialogTitle>Excluir pedido</DialogTitle>
      <DialogDescription>Essa ação não pode ser desfeita.</DialogDescription>
    </DialogHeader>
    <DialogFooter>
      <Button variant="outline" onClick={() => setOpen(false)}>Cancelar</Button>
      <Button variant="destructive" onClick={handleDelete}>Excluir</Button>
    </DialogFooter>
  </DialogContent>
</Dialog>
```

In this project, the shadcn primitives in `src/shared/components/ui/` are already built this way — compose them rather than wrapping them in a new component with a dozen props. They're based on `@base-ui/react`, not Radix, so there's no `asChild`; follow base-ui's composition API. When you build a new reusable component, prefer compound parts (`StatPanel`, `StatPanelHeader`, `StatPanelBody`) over flags; a few genuinely variant props (`variant`, `size`) are fine.

## 7. Type boundaries — keep backend shapes out of the UI

If components consume raw API/DB rows directly, every backend change ripples through the whole UI. Put a mapper between the data source and the UI so components depend on a stable domain type:

```
DB row / API response → mapper → DTO / domain type → UI
```

This project already has the layers: `data/*.dal.ts` returns Prisma rows, `data/*.mapper.ts` converts them into DTOs, and components receive DTOs typed from `features/<feature>/types` or `@/entities`. Don't import Prisma types (`@/generated/prisma`) into components, and don't pass a raw row "just this once". Types here are architecture, not just typo prevention.

---

## Applying the patterns

When writing new code, apply all of them from the start. When refactoring or reviewing existing code:

1. Read the component and list which patterns it violates, most impactful first (usually 5 → 4 → 3 → 1).
2. Refactor one pattern at a time so each step stays easy to verify.
3. Keep the behavior identical; these are structural changes, not feature changes.
4. Respect the project style: no braces around single-statement `if`, and prefer semantic names over explanatory comments.
5. Run `pnpm lint` and `npx tsc --noEmit -p .` after the change.

When reviewing, report findings as `pattern → location → why it hurts → suggested change`, and skip patterns that don't apply instead of forcing them.
