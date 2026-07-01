# Source Register Format

Use this format for `00_Meta/Sources/SOURCE-REGISTER.md`. The source register is the provenance ledger for documents, code, task-analysis outputs, and other materials used to build the Obsidian vault.

```md
---
title: "Source Register"
tags: [meta, obsidian, sources]
status: active
created: YYYY-MM-DD
updated: YYYY-MM-DD
---

# Source Register

## Processing summary

- Total sources: {number}
- Processed: {number}
- Partially processed: {number}
- Skipped: {number}
- Last updated: YYYY-MM-DD

## Source table

| source id | title or path | type | complexity | status | produced or updated notes |
| --- | --- | --- | --- | --- | --- |
| `SRC-0001` | `{title or relative path}` | `{paper | spec | docs | code | meeting-notes | task-notes | task-analysis | book | dataset | image | conversation | other}` | `{simple | standard | complex | huge}` | `{queued | processing | processed | partial | skipped}` | `[[Note Title]]`, `[[Another Note]]` |

## Source records

### SRC-0001: {source title or path}

- Type: {paper | spec | docs | code | meeting-notes | task-notes | task-analysis | book | dataset | image | conversation | other}
- Location: `{path, URL, DOI, repository path, commit, or stable locator}`
- Origin: {author, organization, project, repository, user-provided, unknown}
- Date or version: {date, version, commit, release, unknown}
- Language: {language or mixed}
- Size: {pages, words, lines, files, or rough estimate}
- Complexity: {simple | standard | complex | huge}
- Processing status: {queued | processing | processed | partial | skipped}
- Access limits: {none, binary unreadable, OCR needed, truncated, generated, proprietary, unknown}
- Obsidian handling: {source kept outside vault | copied to attachments | summarized only | task analysis captured in vault | code excluded from indexing | other}

#### Extraction scope

- Included sections, files, or ranges:
  - {section, page range, file path, line range, module, chapter}
- Excluded sections, files, or ranges:
  - {reason: irrelevant, generated, binary, dependency, duplicate, inaccessible}

#### Produced or updated notes

- `[[Concept Note]]`: {created | updated}; `{vault-relative path}`; {one-sentence contribution from this source}
- `[[Another Concept]]`: {created | updated}; `{vault-relative path}`; {one-sentence contribution from this source}

#### Claims or facts needing careful provenance

| locator | claim or fact | note |
| --- | --- | --- |
| `{page/section/line}` | {concise claim} | `[[Note Title]]` |

#### Open issues

- {missing pages, parse failure, ambiguous terminology, contradiction, needed user decision}
```

## Rules

- Assign source IDs before extraction begins.
- Keep skipped and rejected sources in the register when listing them prevents duplicate future work.
- Use precise locators wherever possible. For code, prefer file path plus symbol or line range.
- Do not mark a source `processed` until every note created or updated from it is inside the target vault, has source IDs in frontmatter, appears in the produced or updated notes list, and includes provenance in the body where needed.
- For `task-analysis` sources, the produced or updated notes list must include the captured daily, weekly, monthly, or annual analysis file and any durable permanent notes extracted from it.
- Use the `conversation` type for opinions the user expresses in chat that are logged as opinion notes ([OPINION-FORMAT.md](./OPINION-FORMAT.md)). Set `Origin: user-provided`, use the statement date/time as the locator, and list the produced `40_Opinions/` notes. A `conversation` source records subjective, user-attributed stances, not source-grounded facts, so it needs no `Claims or facts needing careful provenance` rows.
