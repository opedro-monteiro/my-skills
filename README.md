# Agent Skills

This repository is a personal collection of reusable Skills for AI agents. Each Skill captures focused engineering guidance, decision rules, workflows, and validation criteria that an agent can apply while creating, reviewing, or refactoring code. The collection currently focuses on React, Next.js, and TypeScript practices.

## Available Skills

| Category | Skill                                                                    | Coverage                                                                                                                                                                             |
| -------- | ------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Next.js  | [`nextjs-app-router-patterns`](next/nextjs-app-router-patterns/SKILL.md) | Next.js App Router architecture: Server and Client Component boundaries, `"use client"`, project organization, route groups, and distinct loading, empty, success, and error states. |
| React    | [`react-patterns`](react/react-patterns/SKILL.md)                        | React component design: early returns, state colocation, derived state, custom hooks, component splitting, composition, and type boundaries between backend data and UI models.      |

The repository also contains [TypeScript guidance for discriminated unions](typescript/skill.md). It covers explicit modeling of mutually exclusive and asynchronous states, but it has not yet been moved into the canonical `<category>/<skill-name>/SKILL.md` layout.

## Repository Layout

Canonical Skills use the following convention:

```text
<category>/
└── <skill-name>/
    ├── SKILL.md
    └── references/        # Optional supporting material
```

`SKILL.md` is the entry point. Its YAML frontmatter identifies the Skill and describes when an agent should use it. Detailed examples or guidance that should be loaded only when needed can live in `references/`; other resources should be added only when the Skill requires them and the authoring guide supports them.

The current canonical Skill directories are:

```text
next/nextjs-app-router-patterns/
├── SKILL.md
└── references/
    ├── component-boundaries.md
    ├── project-organization.md
    └── resilient-ui-states.md

react/react-patterns/
├── SKILL.md
└── references/
    └── examples.md
```

## Using a Skill

Choose the `SKILL.md` whose description matches the task, then make that file available to the agent or read and apply its workflow directly. Follow its boundaries, checkpoints, and validation instructions. When a Skill links to bundled material such as `references/examples.md`, resolve that path relative to the directory containing its `SKILL.md` and load only the relevant resource.

Agents with native Skill support can discover these manifests through their own supported configuration. This repository does not define a package installation or execution command; consult the documentation for the agent environment being used rather than assuming a repository-specific setup.

## Adding or Maintaining a Skill

Use the local [`create-skill` authoring guide](.agents/skills/create-skill/SKILL.md) when creating a Skill. Apply its naming, frontmatter, description, structure, workflow, and validation guidance while following this repository's category-based destination convention:

```text
<category>/<skill-name>/SKILL.md
```

For a new Skill:

1. Choose a lowercase, hyphenated name and create its category directory if needed.
2. Write valid YAML frontmatter whose `name` matches the directory and whose `description` states both the capability and its activation boundaries.
3. Keep the main workflow focused and actionable; move detailed supporting content into relative `references/` files when useful.
4. Verify every bundled-file link, confirm the documented behavior against the Skill contents, and review the Markdown for formatting issues.

For an existing Skill, edit it in place and preserve its established scope unless the capability itself is intentionally changing. The local authoring guide originates from a broader upstream Skill collection, so repository-specific paths or commands in its examples should not be copied unless they exist here.

## License

This repository is available under the [MIT License](LICENSE).
