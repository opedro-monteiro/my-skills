# Server and Client Component Boundaries

## Contents

- Server-first decision
- Isolate interactive leaves
- Client Component triggers
- Boundary review checklist

## Start with a Server Component

App Router components are Server Components by default. Keep data fetching and non-interactive rendering on the server:

```tsx
export default async function ProductPage() {
  const product = await getProduct();

  return <ProductDetails product={product} />;
}
```

`ProductPage` fetches data and renders `ProductDetails`; it does not need state, browser APIs, or event handlers. Keeping it on the server avoids unnecessary client-side JavaScript and keeps the architecture simpler.

Do not interpret server-first as server-only. Interactive UI still requires Client Components. Make that boundary intentional by asking: **Which part of this page actually needs to run in the browser?**

## Isolate the Interactive Leaf

Keep the product details on the server and move only the interaction to the client:

```tsx
export default async function ProductPage() {
  const product = await getProduct();

  return (
    <>
      <ProductDetails product={product} />
      <AddToCart productId={product.id} />
    </>
  );
}
```

```tsx
"use client";

type Props = {
  productId: string;
};

export function AddToCart({ productId }: Props) {
  return (
    <button onClick={() => addProductToCart(productId)}>
      Add to cart
    </button>
  );
}
```

The useful boundary in this page tree is the interactive leaf:

```text
Product Page                     Server
├── Product Details             Server
├── Product Images              Server unless interactive
├── Reviews                     Server unless interactive
└── Add to Cart                 Client: handles interaction
```

Do not mark the entire product page as a Client Component merely because `AddToCart` needs the browser.

## Use a Client Component for Browser Behavior

Add `"use client"` when a component needs state, event handlers, browser APIs, or other browser-side interaction:

```tsx
"use client";

import { useState } from "react";

export function Counter() {
  const [count, setCount] = useState(0);

  return (
    <button onClick={() => setCount(count + 1)}>
      {count}
    </button>
  );
}
```

`Counter` needs client-side JavaScript because it owns state and handles a click. Use Client Components for interaction, not by default.

## Boundary Review Checklist

- Begin at the page with a Server Component.
- Mark every state hook, event handler, browser API, and browser-only dependency.
- Place `"use client"` at the smallest shared boundary that contains those requirements.
- Keep unrelated data fetching and static rendering outside that boundary.
- Confirm that values crossing from server to client are serializable.
- Recheck the shipped client subtree after moving the directive; a directive applies to the imported subtree, not only the file containing it.
