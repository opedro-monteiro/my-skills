# Next.js App Router agent Skill finalized with recorded validation evidence

The change consolidates five Next.js App Router documents into one English agent Skill and three focused references. It uses progressive disclosure for Server and Client Component boundaries, project organization, route groups, and resilient UI states while excluding Pages Router and generic TypeScript guidance. The durable validation report records commands, outputs, and exit codes for the final filesystem, links, content coverage, and Git scope. **Watch for:** no remaining blocking concerns; unavailable repository infrastructure is explicitly documented as not run rather than presented as passing.

**Verdict**: APPROVED

## High-level view

The Skill’s routing metadata identifies concrete App Router scenarios and exclusions. Its workflow retains all five original topic groups while moving detailed examples into three references.

The previous evidence gap is resolved by executed checks rather than proposed commands. The report resolves every local link, enumerates the exact four-file Skill tree, confirms each superseded source is absent, captures whitespace and full Git status results, and records topic coverage.

The report separates unavailable repository infrastructure from successful local checks. The dotnet validator, CODEOWNERS locations, and eval infrastructure are shown as absent and are explicitly not claimed as executed or passing.

<details>
<summary>Issues (0)</summary>

No actionable findings.

</details>

<details><summary>Details</summary>

## Executed evidence closes the prior delivery gate

The exact-tree assertion and filesystem enumeration exit `0` and yield only `SKILL.md`, `references/component-boundaries.md`, `references/project-organization.md`, and `references/resilient-ui-states.md`. A separate exit-`0` check names all five original documents and captures each as absent.

The link scan covers every Markdown file in that tree and prints all three source-to-target resolutions from `SKILL.md`, each marked as existing, before exiting `0`. It also establishes that the references contain no additional local links, so the output accounts for the complete local-link set.

`git status --short --untracked-files=all` contains only `next/.agents/tasks/validation-report.md`, and `git ls-files --others --exclude-standard` independently identifies the same sole untracked artifact. The tracked-file listing includes all four Skill files under `next/`, the stable status contains no path outside `next/`, and `git diff --check -- next` records empty output with exit code `0`.

## Routing metadata and retained App Router coverage

The frontmatter check parses exactly two delimiters and the `name` and `description` fields, verifies the directory-aligned name and naming constraints, and reports a 448-character description with no XML-like tags. The English description names App Router boundaries, route groups, loading/error/empty states, relevant authoring and review intents, and exclusions for Pages Router, generic React work, and TypeScript-only guidance.

The coverage assertion records `ProductPage`, `ProductDetails`, `AddToCart`, `useState`, `Counter`, the `src/` and `features/` examples, `(marketing)`, `(auth)`, `(dashboard)`, `/settings`, `loading.tsx`, `error.tsx`, no-results, and retry behavior. Full-file inspection confirms the Skill and all three references are entirely in English and preserve every planned source group.

## Infrastructure limitations are reported without false completion claims

The report captures the missing `eng/skill-validator` and both missing CODEOWNERS locations with exit code `1`, plus an empty repository-wide `eval.yaml` search with exit code `0`. It explicitly states that the dotnet validator and eval were not run, no CODEOWNERS update was attempted, and none of those unavailable checks is claimed to pass.

</details>

<details>
<summary>File map</summary>

- `next/nextjs-app-router-patterns/SKILL.md` — defines App Router routing metadata, decisions, workflow, validation, and pitfalls.
- `next/nextjs-app-router-patterns/references/component-boundaries.md` — preserves Server and Client Component boundary guidance.
- `next/nextjs-app-router-patterns/references/project-organization.md` — preserves folder organization and URL-neutral route groups.
- `next/nextjs-app-router-patterns/references/resilient-ui-states.md` — preserves loading, success, empty, error, and recovery behavior.
- `next/.agents/tasks/validation-report.md` — records executed evidence and unavailable infrastructure.

Full scope evidence: `next/.agents/tasks/validation-report.md`; tracked Next diff: `git diff -- next`.

</details>
