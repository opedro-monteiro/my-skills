# React patterns — before/after examples

Read only the section you need.

## Contents

1. Early returns
2. State colocation
3. Derived state
4. Custom hooks
5. Splitting big components
6. Composition over prop explosion
7. Type boundaries

---

## 1. Early returns

### Before

```tsx
function UserProfile({ user, isLoading, error }: UserProfileProps) {
  return (
    <div>
      {isLoading ? (
        <p>Carregando...</p>
      ) : error ? (
        <p>Erro!</p>
      ) : user ? (
        <div>
          {user.isAdmin ? (
            <div>
              <h1>{user.name}</h1>
              <AdminPanel />
            </div>
          ) : (
            <h1>{user.name}</h1>
          )}
        </div>
      ) : (
        <p>Nenhum usuário encontrado.</p>
      )}
    </div>
  );
}
```

### After

```tsx
function UserProfile({ user, isLoading, error }: UserProfileProps) {
  if (isLoading) return <p>Carregando...</p>;
  if (error) return <p>Erro!</p>;
  if (!user) return <p>Nenhum usuário encontrado.</p>;

  return (
    <div>
      <h1>{user.name}</h1>
      {user.isAdmin && <AdminPanel />}
    </div>
  );
}
```

Same logic, half the lines, one concern per line.

---

## 2. State colocation

### Before — state lifted with no reason

```tsx
function App() {
  const [isMenuOpen, setIsMenuOpen] = useState(false);
  const [searchQuery, setSearchQuery] = useState("");

  return (
    <>
      <Navbar isMenuOpen={isMenuOpen} setIsMenuOpen={setIsMenuOpen} />
      <SearchBar searchQuery={searchQuery} setSearchQuery={setSearchQuery} />
    </>
  );
}
```

Only `Navbar` uses `isMenuOpen`, only `SearchBar` uses `searchQuery`, yet every keystroke re-renders `App` and `Navbar`.

### After

```tsx
function App() {
  return (
    <>
      <Navbar />
      <SearchBar />
    </>
  );
}

function Navbar() {
  const [isMenuOpen, setIsMenuOpen] = useState(false);
  // ...
}

function SearchBar() {
  const [searchQuery, setSearchQuery] = useState("");
  // ...
}
```

Lift again only if, say, `Navbar` later needs to read `searchQuery`.

---

## 3. Derived state

### Before — two sources of truth

```tsx
const [items, setItems] = useState<CartItem[]>(initialItems);
const [selectedCount, setSelectedCount] = useState(0);

function handleSelect(id: string) {
  const updated = items.map((item) =>
    item.id === id ? { ...item, selected: !item.selected } : item,
  );
  setItems(updated);
  setSelectedCount(updated.filter((item) => item.selected).length);
}
```

Any future code that updates `items` and forgets `setSelectedCount` makes the UI wrong.

### After — one source of truth

```tsx
const [items, setItems] = useState<CartItem[]>(initialItems);

const selectedItems = items.filter((item) => item.selected);
const selectedCount = selectedItems.length;

function handleSelect(id: string) {
  setItems((current) =>
    current.map((item) =>
      item.id === id ? { ...item, selected: !item.selected } : item,
    ),
  );
}
```

The same applies to `useEffect(() => setX(f(y)), [y])` — replace it with `const x = f(y)`.

---

## 4. Custom hooks

### Before — fetching logic inline, copied between components

```tsx
function ProductPage({ productId }: ProductPageProps) {
  const [product, setProduct] = useState<ProductDTO | null>(null);
  const [isLoading, setIsLoading] = useState(true);
  const [error, setError] = useState<Error | null>(null);

  useEffect(() => {
    fetch(`/api/products/${productId}`)
      .then((res) => res.json())
      .then((data) => {
        setProduct(data);
        setIsLoading(false);
      })
      .catch((err) => {
        setError(err);
        setIsLoading(false);
      });
  }, [productId]);

  if (isLoading) return <ProductSkeleton />;
  if (error || !product) return <ErrorState />;

  return <ProductDetails product={product} />;
}
```

### After — logic in a hook, server state via React Query

```ts
// src/features/product/hooks/use-product.ts
export function useProduct(productId: string) {
  return useQuery({
    queryKey: ["product", productId],
    queryFn: () => fetchProduct(productId),
  });
}
```

```tsx
function ProductPage({ productId }: ProductPageProps) {
  const { data: product, isLoading, error } = useProduct(productId);

  if (isLoading) return <ProductSkeleton />;
  if (error || !product) return <ErrorState />;

  return <ProductDetails product={product} />;
}
```

If the data can be loaded on the server, prefer a Server Component that calls the DAL and skip the client hook entirely.

### Extracting behavior from a busy component

Before:

```tsx
function OrdersTable({ orders }: OrdersTableProps) {
  const [search, setSearch] = useState("");
  const [page, setPage] = useState(1);
  const [sortBy, setSortBy] = useState<OrderSortKey>("createdAt");
  const [selectedIds, setSelectedIds] = useState<string[]>([]);
  // filtering, sorting, pagination, selection...

  return (
    // 200 lines of JSX
  );
}
```

After:

```ts
// src/features/order/hooks/use-orders-table.ts
export function useOrdersTable(orders: OrderDTO[]) {
  const [search, setSearch] = useState("");
  const [page, setPage] = useState(1);
  const [sortBy, setSortBy] = useState<OrderSortKey>("createdAt");

  const filteredOrders = filterOrders(orders, search);
  const visibleOrders = paginate(sortOrders(filteredOrders, sortBy), page);

  return { search, setSearch, page, setPage, sortBy, setSortBy, visibleOrders };
}
```

```tsx
function OrdersTable({ orders }: OrdersTableProps) {
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

Note that `visibleOrders` is derived (pattern 3), not stored.

---

## 5. Splitting big components

How a component rots: `Dashboard.tsx` starts simple, then gains API fetching, filters, pagination, a modal, form validation, permissions, sorting, export… and ends at 1,200 lines.

Target structure:

```
features/dashboard/
  components/
    dashboard.tsx
    dashboard-header.tsx
    dashboard-filters.tsx
    stats-cards.tsx
    orders-table.tsx
    order-details-modal.tsx
  hooks/
    use-dashboard-data.ts
    use-dashboard-filters.ts
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

Each child owns its own local state (pattern 2) and receives only the data it renders.

---

## 6. Composition over prop explosion

### Before — the component becomes a config file

```tsx
<Modal
  open={open}
  title="Excluir pedido"
  showIcon
  iconType="warning"
  showDescription
  description="Essa ação não pode ser desfeita."
  showCancelButton
  cancelText="Cancelar"
  showConfirmButton
  confirmText="Excluir"
  confirmButtonDanger
  showFooterBorder
/>
```

Every new design needs another prop and another branch inside `Modal`.

### After — structure is explicit at the call site

```tsx
<Dialog open={open} onOpenChange={setOpen}>
  <DialogContent>
    <DialogHeader>
      <DialogTitle>Excluir pedido</DialogTitle>
    </DialogHeader>
    <div className="flex items-center gap-2">
      <TriangleAlert className="text-destructive" />
      <p>Essa ação não pode ser desfeita.</p>
    </div>
    <DialogFooter>
      <Button variant="outline" onClick={() => setOpen(false)}>
        Cancelar
      </Button>
      <Button variant="destructive" onClick={handleDelete}>
        Excluir
      </Button>
    </DialogFooter>
  </DialogContent>
</Dialog>
```

### Building your own compound component

```tsx
export function StatPanel({ children, className }: StatPanelProps) {
  return <div className={cn("rounded-lg border", className)}>{children}</div>;
}

export function StatPanelHeader({ children }: PropsWithChildren) {
  return <div className="border-b p-3">{children}</div>;
}

export function StatPanelBody({ children }: PropsWithChildren) {
  return <div className="p-3">{children}</div>;
}
```

---

## 7. Type boundaries

### Before — UI coupled to the DB shape

```tsx
import type { Product } from "@/generated/prisma";

function ProductCard({ product }: { product: Product }) {
  return <p>{(product.priceInCents / 100).toFixed(2)}</p>;
}
```

Renaming a column or changing how prices are stored breaks every component that touches `Product`.

### After — mapper produces a DTO the UI depends on

```ts
// src/features/product/data/products.mapper.ts
export function toProductDTO(row: ProductRow): ProductDTO {
  return {
    id: row.id,
    name: row.name,
    price: row.priceInCents / 100,
    imageUrl: row.image?.url ?? null,
  };
}
```

```tsx
function ProductCard({ product }: { product: ProductDTO }) {
  return <p>{formatCurrency(product.price)}</p>;
}
```

Flow: `products.dal.ts` (Prisma row) → `products.mapper.ts` → `ProductDTO` → components. A backend change is absorbed in the mapper.
