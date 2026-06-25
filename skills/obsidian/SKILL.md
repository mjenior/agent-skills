---
name: obsidian
description: Create, update, audit, and parse Obsidian-compatible local knowledge bases from user-provided technical documents, non-technical documents, and code collections.
argument-hint: "What documents, code, or vault should be converted or maintained?"
---

The user has asked you to work with an Obsidian-compatible local knowledge base. Optimize only for Obsidian vaults: local folders of Markdown files, Obsidian WikiLinks, YAML frontmatter, attachments, and Obsidian indexing behavior. Do not generalize the workflow for other note platforms, static-site generators, or publishing systems unless the user explicitly asks in a separate task.

Use the workflow in [WORKFLOW.md](./WORKFLOW.md). It defines vault scoping, source inventory, extraction, note construction, subagent topology, model and thinking tiers, validation gates, and maintenance/audit procedures.

## Obsidian workspace

Treat the user-named vault directory as the workspace. If the user has not named a vault and the current directory looks like an Obsidian vault, use it. A directory looks like a vault when it contains `.obsidian/`, a meaningful set of `.md` files, or an obvious vault structure such as `00_Meta/`, `10_Fleeting/`, `20_Permanent/`, or `30_Projects/`.

When creating a new vault, use this default structure unless the user provides an existing convention:

- `00_Meta/Templates/`: reusable note templates.
- `00_Meta/Attachments/`: images, PDFs, diagrams, and other binary files.
- `00_Meta/Sources/`: source registers, ingestion notes, and provenance ledgers.
- `10_Fleeting/`: temporary triage notes, rough imports, and scratch work.
- `20_Permanent/`: evergreen concept notes intended for long-term retrieval.
- `30_Projects/`: project-specific or time-bound notes.

Create files lazily as they become useful:

- `00_Meta/VAULT-MANIFEST.md`: vault conventions, folder roles, filename rules, link style, required frontmatter fields, and known exclusions. Use [VAULT-MANIFEST-FORMAT.md](./VAULT-MANIFEST-FORMAT.md).
- `00_Meta/Sources/SOURCE-REGISTER.md`: source inventory, provenance, processing status, and extraction coverage. Use [SOURCE-REGISTER-FORMAT.md](./SOURCE-REGISTER-FORMAT.md).
- `00_Meta/VAULT-AUDIT.md`: validation findings for filenames, links, frontmatter, tags, attachments, duplicates, and indexer risks. Use [VAULT-AUDIT-FORMAT.md](./VAULT-AUDIT-FORMAT.md).
- Permanent notes under `20_Permanent/`: evergreen, source-grounded Markdown notes. Use [NOTE-FORMAT.md](./NOTE-FORMAT.md).

## Default posture

- Preserve Obsidian compatibility over general Markdown portability.
- Prefer WikiLinks in body text unless the existing vault manifest requires another Obsidian-supported convention.
- Keep filenames unique across the vault so `[[Note Title]]` links do not require brittle folder paths.
- Keep folder nesting shallow, ideally no more than 2-3 levels below the vault root.
- Put YAML frontmatter as the first content in each permanent note.
- Use strict YAML: quote strings containing colons or special characters, omit `#` from frontmatter tags, and use arrays for multi-value fields.
- Centralize attachments under `00_Meta/Attachments/` unless the vault already has a configured attachment folder.
- Do not dump entire long documents or source files into permanent notes. Extract stable concepts, claims, APIs, workflows, definitions, and decision-relevant relationships.
- Keep source provenance explicit. Every important note should identify the source document, code path, section, page, line range, commit, or other locator that supports it.
- For code collections, create human-readable concept, architecture, API, and workflow notes. Avoid copying large code blocks; use short snippets only when they clarify a stable interface or non-obvious mechanism.

## When to ask before proceeding

Ask one focused question when the vault location, desired note granularity, treatment of source files, or permission to modify an existing vault would materially change the result. Otherwise state the assumption in `00_Meta/VAULT-MANIFEST.md` or `00_Meta/Sources/SOURCE-REGISTER.md` and proceed.

## Safety rules

- Before editing an existing vault, inspect its current folder conventions, frontmatter fields, link style, templates, and attachment settings.
- Do not overwrite existing notes. If a generated note would collide with an existing filename, merge only when the note covers the same concept and provenance supports the update; otherwise choose a unique, descriptive filename.
- Do not reorganize large parts of an existing vault unless the user explicitly asks for restructuring.
- Do not move or delete source documents, attachments, code repositories, or existing notes without explicit user approval.
- If large code repositories, generated output, datasets, dependency folders, or binary collections are inside the vault, record an Obsidian indexer risk and recommend exclusion rather than ingesting them directly into permanent notes.
