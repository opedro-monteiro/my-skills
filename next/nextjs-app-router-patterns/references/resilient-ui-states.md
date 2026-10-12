# Loading, Error, and Empty States

## Contents

- Model distinct outcomes
- App Router conventions
- Contextual errors and recovery
- Validation matrix

## Model Every Observable Outcome

Do not assume that a page always loads successfully. Networks, APIs, databases, and client connections can fail. Design these outcomes separately:

```text
Loading -> Success with data
Loading -> Success with no results
Loading -> Error
```

`No results` means the request completed successfully but returned no items. It is not equivalent to a failed request. Give each state distinct content and behavior so users can tell whether to wait, change their input, or retry.

## Use App Router State Conventions

Add `loading.tsx` to provide immediate route-segment feedback while content is being fetched:

```tsx
export default function Loading() {
  return <p>Loading orders...</p>;
}
```

Add `error.tsx` when a segment needs a failure boundary. Because the error UI uses interaction and receives a reset function, it is a Client Component:

```tsx
"use client";

export default function Error({ reset }: { reset: () => void }) {
  return (
    <section>
      <h2>We could not load your orders.</h2>
      <p>Please try again.</p>
      <button onClick={() => reset()}>Try again</button>
    </section>
  );
}
```

Use contextual copy such as “We could not load your orders” instead of only “Something went wrong.” Offer a retry action when the operation can recover; otherwise explain the next valid action rather than presenting a button that cannot help.

Render an empty state from the successful data path:

```tsx
export function OrderList({ orders }: { orders: Order[] }) {
  if (orders.length === 0) {
    return <p>No results. You have no orders yet.</p>;
  }

  return (
    <ul>
      {orders.map((order) => (
        <li key={order.id}>{order.number}</li>
      ))}
    </ul>
  );
}
```

## Validation Matrix

Exercise every state deliberately rather than verifying only the happy path:

| State | Trigger | Expected experience |
|-------|---------|---------------------|
| Loading | Delay the data source or navigation. | Immediate feedback identifies what is loading. |
| Success with data | Return one or more records. | Content renders without loading or error UI. |
| Success with no results | Return a successful empty collection. | A dedicated empty message appears, not an error. |
| Recoverable error | Fail once, then allow the request to succeed. | Contextual error copy and a working retry action appear. |
| Non-recoverable error | Return a persistent failure. | The UI explains a valid next step without a misleading retry loop. |

Confirm that loading feedback does not persist after completion, empty and failure states do not look equivalent, and retry returns the user to the normal rendering flow when recovery succeeds.
