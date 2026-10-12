# Project Organization and Route Groups

## Contents

- Responsibility-based project structure
- Directory responsibilities
- Route groups and URLs
- Stop conditions

## Organize by Clear Responsibility

No single folder structure fits every project. Prefer an existing consistent convention; introduce separation when growth makes ownership or file discovery unclear. A responsibility-based `src/` layout can look like this:

```text
src/
├── app/
├── components/
├── features/
├── hooks/
├── lib/
├── services/
├── types/
└── utils/
```

Use each directory for a distinct concern:

| Directory | Responsibility |
|-----------|----------------|
| `app/` | App Router pages, layouts, route handlers, and route-specific files. |
| `components/` | Reusable UI shared across features. |
| `features/` | Feature-specific components, logic, and supporting code. |
| `hooks/` | Reusable custom React hooks. |
| `lib/` | Shared infrastructure, integrations, and configured library clients. |
| `services/` | API access and external service operations. |
| `types/` | Shared TypeScript types. |
| `utils/` | Small, reusable, generally stateless utilities. |

Treat these responsibilities as a decision aid, not a mandatory template. Merge or omit directories when the project does not need the distinction. Consistency and clear code ownership matter more than finding a universally perfect structure.

Before adding or moving a file, ask:

1. Is the code route-specific, feature-specific, or shared?
2. Which existing directory already owns that responsibility?
3. Would introducing another directory create overlap?
4. Can a new contributor predict where similar code belongs?

## Organize Routes Without Changing URLs

Route Groups organize related routes and can provide different layouts without adding segments to public URLs:

```text
app/
├── (marketing)/
│   ├── page.tsx
│   └── pricing/
│       └── page.tsx
├── (auth)/
│   ├── login/
│   │   └── page.tsx
│   └── register/
│       └── page.tsx
└── (dashboard)/
    ├── layout.tsx
    ├── page.tsx
    └── settings/
        └── page.tsx
```

The parentheses are significant: `(marketing)`, `(auth)`, and `(dashboard)` are organizational names and do not become URL segments. Therefore:

```text
app/(dashboard)/settings/page.tsx -> /settings
```

Use this pattern when, for example, marketing routes need a public-site layout while dashboard routes need different navigation. Verify all routes after grouping because two files in different groups can still resolve to the same public path.

## Stop Conditions

Do not add a Route Group merely to classify every folder. Stop when:

- The application is small and the ordinary `app/` tree is already clear.
- The group would express no distinct layout, ownership, or organization boundary.
- The proposed grouping creates ambiguous or conflicting public paths.
- A physical move would break imports or conventions without improving discoverability.

After reorganizing, update imports and internal documentation, then verify intended URLs and each affected layout boundary.
