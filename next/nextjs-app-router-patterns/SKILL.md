---
name: nextjs-app-router-patterns
description: 'Applies Next.js App Router architecture patterns for Server and Client Component boundaries, "use client", project folders, route groups, loading.tsx, error.tsx, and empty states. USE FOR: creating, refactoring, organizing, or reviewing Next.js App Router pages, layouts, components, and route trees. DO NOT USE FOR: Pages Router migrations, generic React component design without Next.js routing or rendering concerns, or TypeScript-only guidance.'
---

# Next.js App Router Patterns

## Purpose

Apply intentional App Router boundaries, a responsibility-based project structure, URL-neutral route groups, and distinct loading, success, empty, and error experiences while keeping browser JavaScript and organizational complexity proportional to actual requirements.

## When to Use

- Create or review App Router pages, layouts, components, or route trees.
- Decide whether code belongs in a Server Component or Client Component.
- Introduce `"use client"` without moving an entire page into the client bundle.
- Organize a growing Next.js project by responsibility or feature.
- Group routes for ownership or layouts without changing public URLs.
- Design `loading.tsx`, `error.tsx`, empty states, and recovery actions.

## When Not to Use

- Do not use for Pages Router migrations or `pages/` architecture.
- Do not use for generic React component design with no Next.js rendering or routing decision.
- Do not use for TypeScript-only modeling or typing guidance.
- Do not reorganize a small, understandable application or add route groups without a concrete ownership or layout need.

## Inputs

| Input                                   | Required    | Description                                                                                                          |
| --------------------------------------- | ----------- | -------------------------------------------------------------------------------------------------------------------- |
| App Router files or intended route tree | Yes         | Existing files, a requested feature, or a proposed `app/` structure. Discover these in the repository when possible. |
| Interaction requirements                | Recommended | State, event handlers, browser APIs, and other behavior that requires the browser.                                   |
| Project conventions                     | Recommended | Existing locations for shared UI, features, hooks, services, types, and utilities.                                   |
| Async outcomes                          | Recommended | Loading, success, no-results, failure, and recoverability requirements.                                              |

## Decision Guide

| Decision             | Default                                                             | Change the default only when                                                              |
| -------------------- | ------------------------------------------------------------------- | ----------------------------------------------------------------------------------------- |
| Server or client     | Keep the page and data-rendering components on the server.          | A component needs state, event handlers, browser APIs, or browser-only interactivity.     |
| Client boundary size | Isolate the smallest interactive leaf.                              | Several interactive elements genuinely share client state or browser behavior.            |
| Folder placement     | Follow the project's existing responsibility or feature convention. | Growth has made ownership or file discovery unclear.                                      |
| Route groups         | Use ordinary route segments.                                        | Related routes need organization or distinct layouts without adding a URL segment.        |
| Async UI             | Represent loading, success, empty, and error separately.            | Never collapse empty data into failure or hide a recoverable failure behind generic copy. |

## Workflow

### Step 1: Inspect the App Router context

1. Confirm that the work targets the `app/` router.
2. Inspect nearby pages, layouts, components, and established folder conventions before moving files or adding boundaries.
3. Identify data dependencies, browser-only behavior, route URLs, layout boundaries, and all expected async outcomes.

**Checkpoint:** State the rendering, organization, routing, and resilience decisions that the change actually requires. Do not redesign unaffected areas.

### Step 2: Set the server/client boundary

1. Start with Server Components for data fetching and non-interactive rendering.
2. Ask: **Which part actually needs the browser?**
3. Add `"use client"` only to the smallest component subtree that needs state, event handlers, browser APIs, or interaction.
4. Pass serializable data from server-rendered parents to interactive children; do not mark a whole page as client-side for one interactive leaf.
5. Use [Component Boundaries](references/component-boundaries.md) for the `ProductPage`, `ProductDetails`, `AddToCart`, and `Counter` patterns.

**Checkpoint:** Every Client Component has a concrete browser requirement, and non-interactive page content remains server-rendered where practical.

### Step 3: Place files by clear responsibility

1. Preserve a consistent home for routes, reusable UI, feature code, hooks, infrastructure, services, shared types, and utilities.
2. Prefer existing repository conventions over imposing a universal tree.
3. Introduce a new directory only when its responsibility and ownership are distinct.
4. Use [Project Organization](references/project-organization.md) for the `src/` responsibility map and route-group examples.

**Checkpoint:** A contributor can answer where new code belongs without choosing among overlapping directories.

### Step 4: Add route groups only for a real boundary

1. Use parenthesized folders such as `(marketing)`, `(auth)`, or `(dashboard)` to organize related routes or provide distinct layouts.
2. Verify that the group name does not appear in the public URL; for example, `app/(dashboard)/settings/page.tsx` maps to `/settings`.
3. Avoid route groups in small applications when ordinary route segments are already clear.

**Checkpoint:** Each group expresses an ownership or layout boundary, and every affected public URL remains intentional.

### Step 5: Model resilient UI states

1. Add `loading.tsx` where route-level loading feedback is needed.
2. Add `error.tsx` where the route segment needs a failure boundary and recovery experience.
3. Distinguish loading, success with data, success with no results, and failure.
4. Write contextual error copy and provide a retry action only when retrying can recover.
5. Use [Resilient UI States](references/resilient-ui-states.md) for concrete state flows and examples.

**Checkpoint:** Empty and failed outcomes cannot be mistaken for one another, and recoverable failures expose a clear next action.

### Step 6: Validate in the target repository

1. Run the repository's relevant build, lint, type-check, and targeted tests.
2. Inspect client boundaries, route URLs, layouts, and each async state in the resulting UI or route tree.
3. Report unavailable commands and failures truthfully; never claim successful validation after a command fails.

**Checkpoint:** Report what changed, the checks run, their results, and any unverified behavior.

## Validation

- [ ] The work targets the App Router rather than the Pages Router.
- [ ] Each `"use client"` boundary has a state, event, browser API, or interaction requirement.
- [ ] Non-interactive data fetching and rendering remain on the server where practical.
- [ ] Folder names have distinct responsibilities and follow local conventions.
- [ ] Parenthesized route groups do not alter intended URLs and serve a real organization or layout need.
- [ ] Loading, success, empty, and error outcomes are visibly distinct.
- [ ] Error copy is contextual, and retry is offered only when recovery is possible.
- [ ] Relevant build, lint, type-check, and tests pass, or failures and unavailable checks are reported.

If a referenced bundled file cannot be read, list `references/` once to confirm its expected name, continue with the guidance available here, and explicitly report that detailed example coverage was incomplete.

## Common Pitfalls

| Pitfall                                                  | Corrective action                                                                       |
| -------------------------------------------------------- | --------------------------------------------------------------------------------------- |
| Adding `"use client"` to a page for one button           | Extract the button or smallest interactive subtree into a Client Component.             |
| Treating Server Components as a ban on Client Components | Keep browser-dependent interaction on the client while making the boundary intentional. |
| Applying one "perfect" folder structure everywhere       | Preserve local conventions and separate responsibilities only where ownership is clear. |
| Creating route groups for every category                 | Add them only for meaningful organization or distinct layouts.                          |
| Assuming route-group names become URL segments           | Verify the URL from non-parenthesized segments; parentheses are omitted.                |
| Rendering empty data as an error                         | Render a dedicated no-results state after a successful request.                         |
| Showing only "Something went wrong"                      | Name the failed context and provide a valid recovery action when available.             |
| Claiming completion despite failed checks                | Record the failed command and unresolved behavior accurately.                           |
