# Vault Audit Format

Use this format for `00_Meta/VAULT-AUDIT.md` when validating an Obsidian vault or a changed note set.

```md
---
title: "Vault Audit"
tags: [meta, obsidian, audit]
status: active
created: YYYY-MM-DD
updated: YYYY-MM-DD
---

# Vault Audit

## Scope

- Vault path: `{path}`
- Audit mode: `{changed notes | source cluster | full vault | compatibility review}`
- Files inspected: {number or paths}
- Source register inspected: `{yes | no | partial}`
- Manifest inspected: `{yes | no | partial}`

## Summary

- Notes created: {number}
- Notes updated: {number}
- Sources processed: {number}
- Broken links: {number}
- Duplicate filenames: {number}
- Frontmatter issues: {number}
- Attachment issues: {number}
- Indexer risks: {number}

## Findings

### Finding 1: {short title}

- Category: {broken-link | ambiguous-link | duplicate-filename | invalid-frontmatter | tag-drift | missing-provenance | orphan-note | attachment-location | deep-nesting | indexer-risk | merge-candidate | other}
- Severity: {high | medium | low}
- Location: `{note path, folder path, attachment path, or source path}`
- Evidence: {specific observed issue}
- Obsidian impact: {how this affects links, search, backlinks, graph view, metadata cache, or human navigation}
- Recommended action: {specific repair}
- Status: {fixed | flagged | needs-decision | deferred}

## Link validation

| link | source note | result | action |
| --- | --- | --- | --- |
| `[[Missing Note]]` | `20_Permanent/example.md` | broken | {create note, retarget link, remove link, ask user} |
| `[[Ambiguous Name]]` | `20_Permanent/example.md` | ambiguous | {rename duplicate, use unique title, merge notes} |

## Frontmatter validation

| note | issue | action |
| --- | --- | --- |
| `20_Permanent/example.md` | `{missing sources | invalid YAML | tag has # | title mismatch}` | {fix or flag} |

## Filename and folder validation

| path | issue | action |
| --- | --- | --- |
| `{path}` | `{duplicate filename | unsafe character | too deeply nested}` | {fix or flag} |

## Attachment validation

| attachment | issue | linked from | action |
| --- | --- | --- | --- |
| `{path}` | `{outside attachment folder | orphaned | too large}` | `{note or none}` | {move with approval, link, compress, flag} |

## Indexer risks

| path | risk | recommendation |
| --- | --- | --- |
| `{path}` | `{large binary folder | node_modules | generated files | code repo | dataset}` | {exclude from Obsidian indexing, move outside vault, leave as-is, ask user} |

## Residual risks

- {Unverified source grounding, unreadable binaries, unresolved duplicate titles, user decisions needed}
```
