# Next.js App Router Skill Validation Report

Validated from repository root:

```text
/Users/pedromonteiro/Documents/github-repository/my-skills
```

Skill under validation:

```text
next/nextjs-app-router-patterns
```

All commands below were executed against the working tree. Exit codes and captured standard output/error are recorded. Empty output is stated explicitly. The Skill files were also read in full for the English-only and semantic reviews.

## Summary

| Check                                       | Result                | Evidence summary                                                                                                                                           |
| ------------------------------------------- | --------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1. Structural / exact tree                  | PASS                  | The filesystem contains exactly `SKILL.md` and the three planned references; the plan Step 3 assertion exited 0.                                           |
| 2. Frontmatter / YAML and required sections | PASS                  | Exactly two delimiters and exactly `name`/`description`; all constraints and required sections passed.                                                     |
| 3. Line limit                               | PASS                  | The `SKILL.md` body has 122 lines, below 500.                                                                                                              |
| 4. Internal links                           | PASS                  | All three local Markdown links resolve to existing reference files.                                                                                        |
| 5. Topic coverage                           | PASS                  | The plan Step 2 assertion found all required examples and artifacts.                                                                                       |
| 6. Source removal                           | PASS                  | All five superseded top-level source documents are absent.                                                                                                 |
| 7. Whitespace                               | PASS                  | `git diff --check -- next` exited 0 with no output.                                                                                                        |
| 8. Git scope                                | PASS                  | Stable final status contains only this untracked validation report under `next/`; all four Skill files are tracked and were included in filesystem checks. |
| 9. English-only                             | PASS                  | All four Skill Markdown files were manually reviewed and the Portuguese-marker scan found zero matches.                                                    |
| Unavailable infrastructure                  | NOT RUN / UNAVAILABLE | `eng/skill-validator`, both checked CODEOWNERS locations, and all `eval.yaml` files are absent.                                                            |

## 1. Structural / Exact Tree — PASS

### Filesystem enumeration

Command:

```sh
find next/nextjs-app-router-patterns -type f | sort
```

Captured output:

```text
next/nextjs-app-router-patterns/references/component-boundaries.md
next/nextjs-app-router-patterns/references/project-organization.md
next/nextjs-app-router-patterns/references/resilient-ui-states.md
next/nextjs-app-router-patterns/SKILL.md
```

Exit code: `0`

### Exact all-file assertion

Command:

```sh
python3 -c 'from pathlib import Path; root=Path("next/nextjs-app-router-patterns"); expected=["SKILL.md","references/component-boundaries.md","references/project-organization.md","references/resilient-ui-states.md"]; actual=sorted(str(p.relative_to(root)) for p in root.rglob("*") if p.is_file()); assert actual==expected, (actual,expected); print("exact tree:"); [print(path) for path in actual]'
```

Captured output:

```text
exact tree:
SKILL.md
references/component-boundaries.md
references/project-organization.md
references/resilient-ui-states.md
```

Exit code: `0`

### Plan Step 3 assertion (verbatim)

Command:

```sh
python3 -c 'from pathlib import Path; root=Path("next"); old=["server-components.md","client-components.md","project-folder-structure.md","route-groups.md","error-handling-and-loading-states.md"]; assert not any((root/p).exists() for p in old); skill=root/"nextjs-app-router-patterns"; assert (skill/"SKILL.md").is_file(); assert sorted(str(p.relative_to(skill)) for p in skill.rglob("*.md")) == ["SKILL.md","references/component-boundaries.md","references/project-organization.md","references/resilient-ui-states.md"]'
```

Captured output: `(no output)`

Exit code: `0`

The enumeration and both assertions operate on filesystem files, so they include files regardless of whether Git classifies them as tracked, staged, or untracked.

## 2. Frontmatter / YAML and Required Sections — PASS

### Plan Step 1 assertion (verbatim)

Command:

```sh
python3 -c 'from pathlib import Path; p=Path("next/nextjs-app-router-patterns/SKILL.md"); s=p.read_text(); assert s.startswith("---\nname: nextjs-app-router-patterns\n"); assert s.count("\n---\n") >= 1; assert len(s.splitlines()) < 500; assert all(h in s for h in ["## Purpose", "## When to Use", "## When Not to Use", "## Inputs", "## Workflow", "## Validation", "## Common Pitfalls"])'
```

Captured output: `(no output)`

Exit code: `0`

### Complete frontmatter and section assertion

Command:

```sh
python3 -c 'from pathlib import Path; import re; p=Path("next/nextjs-app-router-patterns/SKILL.md"); lines=p.read_text().splitlines(); delimiters=[i for i,line in enumerate(lines) if line=="---"]; assert len(delimiters)==2 and delimiters[0]==0; fm=lines[1:delimiters[1]]; fields=dict(line.split(":",1) for line in fm); fields={k.strip():v.strip() for k,v in fields.items()}; assert set(fields)=={"name","description"}; name=fields["name"]; description=fields["description"]; quote=chr(39); description=description[1:-1].replace(quote+quote,quote) if len(description)>=2 and description[0]==description[-1]==quote else description; assert name==p.parent.name; assert 1<=len(name)<=64 and re.fullmatch(r"[a-z0-9]+(?:-[a-z0-9]+)*",name); assert 1<=len(description)<=1024; assert not re.search(r"<[^>]+>",description); required=["## Purpose","## When to Use","## When Not to Use","## Inputs","## Workflow","## Validation","## Common Pitfalls"]; assert all(h in lines[delimiters[1]+1:] for h in required); print(f"delimiters: {len(delimiters)}"); print(f"fields: {sorted(fields)}"); print(f"name: {name}"); print(f"description length: {len(description)}"); print("XML-like tags: none"); print("required sections: " + ", ".join(h[3:] for h in required))'
```

Captured output:

```text
delimiters: 2
fields: ['description', 'name']
name: nextjs-app-router-patterns
description length: 448
XML-like tags: none
required sections: Purpose, When to Use, When Not to Use, Inputs, Workflow, Validation, Common Pitfalls
```

Exit code: `0`

This verifies that the name equals the directory name, matches the lowercase-letter/number/hyphen rule, has no leading/trailing or consecutive hyphen, and is 1–64 characters. It also verifies that the non-empty description is 1–1,024 characters and contains no XML-like tags.

## 3. Line Limit — PASS

Command:

```sh
python3 -c 'from pathlib import Path; p=Path("next/nextjs-app-router-patterns/SKILL.md"); lines=p.read_text().splitlines(); delimiters=[i for i,line in enumerate(lines) if line=="---"]; assert len(delimiters)==2; body=lines[delimiters[1]+1:]; assert len(body)<500; print(f"SKILL.md body lines: {len(body)}"); print("limit: under 500")'
```

Captured output:

```text
SKILL.md body lines: 122
limit: under 500
```

Exit code: `0`

## 4. Internal Links — PASS

Command:

```sh
python3 -c 'from pathlib import Path; import re, urllib.parse; root=Path("next/nextjs-app-router-patterns"); files=sorted(root.rglob("*.md")); links=[]; pattern=re.compile(r"(?<!!)\[[^]]+\]\(([^)]+)\)");
for source in files:
 text=source.read_text()
 for raw in pattern.findall(text):
  target=raw.strip().split()[0].strip("<>")
  if re.match(r"^[a-z][a-z0-9+.-]*:",target,re.I) or target.startswith(("#","mailto:")): continue
  pathpart=urllib.parse.unquote(target.split("#",1)[0])
  resolved=(source.parent/pathpart).resolve()
  assert resolved.exists(), f"missing: {source}: {raw} -> {resolved}"
  links.append((source.relative_to(root),raw,resolved.relative_to(root.resolve())))
assert links, "no local Markdown links found"
for source,raw,resolved in links: print(f"{source}: {raw} -> {resolved} [exists]")
print(f"local links checked: {len(links)}")'
```

Captured output:

```text
SKILL.md: references/component-boundaries.md -> references/component-boundaries.md [exists]
SKILL.md: references/project-organization.md -> references/project-organization.md [exists]
SKILL.md: references/resilient-ui-states.md -> references/resilient-ui-states.md [exists]
local links checked: 3
```

Exit code: `0`

Every Markdown file in the actual filesystem tree was scanned, including any file that could have been untracked. No local Markdown links occur in the three reference files; the three links above are the complete local-link set.

## 5. Topic Coverage — PASS

Plan Step 2 assertion (verbatim):

```sh
python3 -c 'from pathlib import Path; root=Path("next/nextjs-app-router-patterns"); expected={"references/component-boundaries.md","references/project-organization.md","references/resilient-ui-states.md"}; assert expected <= {str(p.relative_to(root)) for p in root.rglob("*.md")}; text="\n".join((root/p).read_text() for p in expected); required=["ProductPage","ProductDetails","AddToCart","useState","Counter","src/","features/","(marketing)","(auth)","(dashboard)","/settings","loading.tsx","error.tsx","No results","try again"]; assert all(term.lower() in text.lower() for term in required)'
```

Captured output: `(no output)`

Exit code: `0`

The assertion covers all required artifacts/examples: `ProductPage`, `ProductDetails`, `AddToCart`, `useState`, `Counter`, `src/`, `features/`, `(marketing)`, `(auth)`, `(dashboard)`, `/settings`, `loading.tsx`, `error.tsx`, `No results`, and `try again`.

## 6. Superseded Source Removal — PASS

Command:

```sh
python3 -c 'from pathlib import Path; root=Path("next"); old=["server-components.md","client-components.md","project-folder-structure.md","route-groups.md","error-handling-and-loading-states.md"];
for name in old:
 path=root/name
 assert not path.exists(), f"still present: {path}"
 print(f"{path}: absent")'
```

Captured output:

```text
next/server-components.md: absent
next/client-components.md: absent
next/project-folder-structure.md: absent
next/route-groups.md: absent
next/error-handling-and-loading-states.md: absent
```

Exit code: `0`

The plan Step 3 assertion in Check 1 independently verifies the same removal.

## 7. Whitespace — PASS

Command:

```sh
git diff --check -- next
```

Captured output: `(no output)`

Exit code: `0`

No whitespace errors were reported in tracked/staged Next changes. The validation report is a new workflow artifact and is accounted for separately in the final status below.

## 8. Git Scope — PASS

### Final full status, including untracked files

Command:

```sh
git status --short --untracked-files=all
```

Captured output:

```text
?? next/.agents/tasks/validation-report.md
```

Exit code: `0`

A second sequential invocation returned the same output, confirming the final snapshot was stable. Every changed, added, or untracked path is under `next/`. This validation step created only this report and did not modify, stage, restore, rename, move, or delete any path outside `next/`.

### Skill tracking state

Command:

```sh
git ls-files --stage -- next/nextjs-app-router-patterns next/.agents/tasks
```

Captured output:

```text
100644 403cf0cf94d3b4b05c5f2de9d3954907e83e8cec 0	next/.agents/tasks/plan.md
100644 872d11238659819b0930491250a790ec6f4ba7cf 0	next/.agents/tasks/review.json
100644 6651f601d7faf2aa606338fc665f0a79a907bba2 0	next/.agents/tasks/review.md
100644 a41b619cb41f256f82291141f71d9184a9aaa7e5 0	next/nextjs-app-router-patterns/SKILL.md
100644 0a334571c4c73433f005f95aa8e52c75a593f647 0	next/nextjs-app-router-patterns/references/component-boundaries.md
100644 d52c5a66e7bb2d9f472f0599042a58eff775c5ed 0	next/nextjs-app-router-patterns/references/project-organization.md
100644 e2307bb81148bd95299370497cb9d5bcd429c2c0 0	next/nextjs-app-router-patterns/references/resilient-ui-states.md
```

Exit code: `0`

The prior review described the four Skill files as untracked/new. In the current checkout they are tracked, as the index entries above establish. They were nevertheless included directly by all filesystem-based tree, link, and coverage checks.

### Final untracked artifacts

Command:

```sh
git ls-files --others --exclude-standard
```

Captured output:

```text
next/.agents/tasks/validation-report.md
```

Exit code: `0`

The report is the only untracked artifact, and it is under `next/`. No commit was created by this validation step.

## 9. English-Only — PASS

Command:

```sh
python3 -c 'from pathlib import Path; import re; root=Path("next/nextjs-app-router-patterns"); files=sorted(root.rglob("*.md")); markers=re.compile(r"\b(não|você|vocês|arquivo|arquivos|pasta|carregamento|falha|resultado|resultados|componente|componentes|servidor|cliente|projeto|roteamento|recuperação|usuário|usuários)\b",re.I); matches=[];
for path in files:
 text=path.read_text()
 found=markers.findall(text)
 print(f"reviewed: {path}")
 matches.extend((path,word) for word in found)
assert not matches, matches
print("Portuguese-marker matches: 0")
print("English-only prose scan: PASS")'
```

Captured output:

```text
reviewed: next/nextjs-app-router-patterns/SKILL.md
reviewed: next/nextjs-app-router-patterns/references/component-boundaries.md
reviewed: next/nextjs-app-router-patterns/references/project-organization.md
reviewed: next/nextjs-app-router-patterns/references/resilient-ui-states.md
Portuguese-marker matches: 0
English-only prose scan: PASS
```

Exit code: `0`

A manual full-file review accompanied the marker scan. Frontmatter, headings, instructions, examples, tables, and all prose in the four Skill files are English.

## Unavailable / Out-of-Scope Infrastructure — NOT RUN

### dotnet Skill validator

Command:

```sh
ls eng/skill-validator
```

Captured error:

```text
ls: eng/skill-validator: No such file or directory
```

Exit code: `1`

The create-skill guide's dotnet validator was **not run** and is not claimed to pass because its project directory is absent.

### CODEOWNERS

Commands:

```sh
ls CODEOWNERS
ls .github/CODEOWNERS
```

Captured errors:

```text
ls: CODEOWNERS: No such file or directory
ls: .github/CODEOWNERS: No such file or directory
```

Each command exited with code `1`. No CODEOWNERS file exists at either checked location. Creating or changing one would also violate the explicit `next/`-only modification scope, so no CODEOWNERS update was attempted.

### Multi-stimulus eval

Command:

```sh
find . -name eval.yaml -print
```

Captured output: `(no output)`

Exit code: `0`

No `eval.yaml` exists in this repository, so the guide's multi-stimulus evaluation infrastructure is unavailable. No eval was run or claimed to pass; adding one outside the authorized final Skill tree was not attempted.

## Defects and Corrections

No Skill content, structure, link, coverage, source-removal, whitespace, or language defect was found, so no Skill file was changed. An initial draft of the comprehensive frontmatter command had a shell-quoting syntax error; the corrected command and successful output are the evidence recorded above. The stable final Git status contains no path outside the authorized `next/` scope.
