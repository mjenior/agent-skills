# Vault Manifest Format

Use this format for `00_Meta/VAULT-MANIFEST.md`. The manifest records Obsidian-specific conventions so future agents can create, update, and parse the vault consistently.

````md
---
title: "Vault Manifest"
tags: [meta, obsidian, vault-conventions]
status: active
created: YYYY-MM-DD
updated: YYYY-MM-DD
---

# Vault Manifest

## Purpose

{One paragraph describing what this vault is for, who uses it, and what kinds of sources it contains.}

## Vault root

- Path: `{absolute or user-provided path}`
- Mode: `{new vault | existing vault | imported collection | audit-only}`
- Obsidian settings inspected: `{yes | no | partial}`

## Folder conventions

| folder | role | notes |
| --- | --- | --- |
| `00_Meta/` | Templates, attachments, manifests, source registers, and vault maintenance files. | {existing or default convention} |
| `10_Fleeting/` | Temporary notes, scratch imports, and triage. | {retention or cleanup rule} |
| `20_Permanent/` | Evergreen concept notes. | {domain subfolders if used} |
| `30_Projects/` | Time-bound or project-specific notes. | {project naming rule} |
| `40_Opinions/` | Timestamped, append-only notes logging user-expressed opinions on in-scope topics. | {opinion-capture convention, or `not used`} |

## Naming rules

- Filename style: `{descriptive title | kebab-case | existing convention}`
- Uniqueness rule: filenames must be unique across the vault.
- Disallowed characters: `/`, `:`, `\\`, control characters, and OS-hostile punctuation.
- Collision handling: `{merge same concept | create unique title | ask user}`

## Link conventions

- Internal links: WikiLinks, using `[[Note Title]]` or `[[Note Title|display text]]`.
- Path style: shortest unique link when possible.
- Required backlinks: {rules for source notes, concept notes, project notes, code notes, or index notes}.
- Graph entry points: {index/MOC notes, project notes, source notes, or hub notes that new notes should be linked from}.
- Index style: {generated `.base` views for note listings that should stay current | static MOC notes | both}. Record `.base` index file paths and what each lists.

## Frontmatter schema

Required fields for permanent notes:

```yaml
---
id: YYYYMMDDHHMMSS-slug
title: "Note Title"
aliases: []
tags: []
sources: []
status: seedling | evergreen | draft | archived
created: YYYY-MM-DD
updated: YYYY-MM-DD
---
```

Field rules:

- `id`: stable unique identifier. Prefer timestamp plus slug.
- `title`: quoted when it contains punctuation or special characters.
- `aliases`: array of alternate names, acronyms, source terms, and code symbols.
- `tags`: array without `#` prefixes.
- `sources`: array of source IDs from `00_Meta/Sources/SOURCE-REGISTER.md`.
- `status`: controlled vocabulary for note maturity.
- Property typing: keep properties typed (text, number, checkbox, date `YYYY-MM-DD`, date-time, list/array, link `"[[Note]]"`) so notes are queryable by Bases and Dataview. Record any vault-specific custom properties and their types here.

## Opinion-capture conventions

- Opinion folder: `40_Opinions/` unless overridden here.
- Capture posture: `{log automatically while the skill is active | confirm before logging | disabled}`.
- Cross-session capture: `{CLAUDE.md/memory directive present | skill-scoped only}`. Ambient capture across all sessions requires a `CLAUDE.md` or memory directive; the skill alone only captures while active on a vault task.
- Opinion frontmatter schema ([OPINION-FORMAT.md](./OPINION-FORMAT.md)):

```yaml
---
id: YYYYMMDDHHMMSS-slug
title: "Opinion: {short paraphrase}"
type: opinion
subject: "[[Topic Note]]"
topics: []
stance: "{one-line paraphrase}"
polarity: favorable | critical | mixed | neutral
confidence: tentative | held | strong
origin: conversation
expressed: YYYY-MM-DDTHH:mm:ss
status: current | superseded | withdrawn
supersedes: "[[Opinion: earlier stance]]"
tags: [opinion]
created: YYYY-MM-DD
updated: YYYY-MM-DD
---
```

- Rules: append-only (a changed view is a new note that supersedes the prior one); at most one `current` note per `subject`; `expressed` is a typed `date-time`; opinions are never recorded in a permanent note's `Source grounding` or presented as fact.

## Attachment conventions

- Attachment folder: `00_Meta/Attachments/` unless overridden here.
- Allowed attachment types: {images, PDFs, diagrams, audio, other}.
- Naming rule: {source-id or note-title prefix if useful}.

## Source handling conventions

- Source register path: `00_Meta/Sources/SOURCE-REGISTER.md`.
- Generated note placement: `{20_Permanent/ for evergreen notes | 30_Projects/ for time-bound notes | 30_Projects/Task Reviews/ for task-analysis outputs | existing convention}`.
- Source files copied into vault: `{yes | no | only selected}`.
- Large source files or code repositories: `{keep outside vault | keep but exclude from Obsidian indexing | user decision needed}`.
- Provenance requirement: every factual, scientific, technical, code behavior, or productivity-pattern claim must cite a source ID and locator.

## Tags and aliases

- Tag vocabulary: {short list of approved broad tags or link to a tag index note}.
- Alias rules: {when to add acronyms, old names, code symbols, paper titles, author names}.
- Deprecated tags or aliases: {if any}.

## Indexer risks and exclusions

| path | risk | action |
| --- | --- | --- |
| `{path}` | `{large binaries | node_modules | generated data | code repo | duplicate tree}` | `{exclude | move outside vault | leave as-is | ask user}` |

## Maintenance notes

- Last validation: YYYY-MM-DD
- Validation scope: {changed notes, full vault, source cluster}
- Known issues: {broken links, duplicate filenames, malformed YAML, orphan notes, missing provenance, task analyses outside review indexes}
````
