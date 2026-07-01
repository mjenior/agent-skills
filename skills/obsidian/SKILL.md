---
name: obsidian
description: Create, update, audit, and parse Obsidian-compatible local knowledge bases from user-provided technical documents, non-technical documents, code collections, meeting summaries, and task-analysis outputs. Also captures user-expressed opinions and stances on in-scope topics as timestamped, append-only opinion notes. Covers Obsidian Flavored Markdown syntax (wikilinks, embeds, callouts, block references, properties, tags), Bases (.base) index views, JSON Canvas (.canvas), and the Obsidian CLI.
argument-hint: "What documents, code, or vault should be converted or maintained?"
---

The user has asked you to work with an Obsidian-compatible local knowledge base. Optimize only for Obsidian vaults: local folders of Markdown files, Obsidian WikiLinks, YAML frontmatter, attachments, and Obsidian indexing behavior. Do not generalize the workflow for other note platforms, static-site generators, or publishing systems unless the user explicitly asks in a separate task.

Use the workflow in [WORKFLOW.md](./WORKFLOW.md). It defines vault scoping, source inventory, extraction, note construction, subagent topology, model and thinking tiers, validation gates, and maintenance/audit procedures.

Syntax and artifact references, loaded on demand:

- [references/OBSIDIAN-SYNTAX.md](./references/OBSIDIAN-SYNTAX.md): Obsidian Flavored Markdown — wikilinks, heading/block references, embeds, callouts, tags, highlights, comments, footnotes, math, Mermaid. Consult when composing or repairing note bodies.
- [references/BASES.md](./references/BASES.md): Obsidian Bases (`.base`) live index views. Use to replace hand-maintained MOC/index notes and static source tables with auto-updating queries over note frontmatter.
- [references/CANVAS.md](./references/CANVAS.md): JSON Canvas (`.canvas`) visual artifacts — mind maps, architecture and concept diagrams that can embed vault notes.
- [references/OBSIDIAN-CLI.md](./references/OBSIDIAN-CLI.md): the `obsidian` CLI as an optional, more reliable backend for live-vault interaction and validation when Obsidian is running.

## Obsidian workspace

Treat the user-named vault directory as the workspace and write all generated or updated notes inside that vault. Do not leave note artifacts in the current working directory, a temporary folder, or chat output when the user has provided a target vault or knowledge base.

If the user has not named a vault and the current directory looks like an Obsidian vault, use it. A directory looks like a vault when it contains `.obsidian/`, a meaningful set of `.md` files, or an obvious vault structure such as `00_Meta/`, `10_Fleeting/`, `20_Permanent/`, or `30_Projects/`.

When creating a new vault, use this default structure unless the user provides an existing convention:

- `00_Meta/Templates/`: reusable note templates.
- `00_Meta/Attachments/`: images, PDFs, diagrams, and other binary files.
- `00_Meta/Sources/`: source registers, ingestion notes, and provenance ledgers.
- `10_Fleeting/`: temporary triage notes, rough imports, and scratch work.
- `20_Permanent/`: evergreen concept notes intended for long-term retrieval.
- `30_Projects/`: project-specific or time-bound notes.
- `40_Opinions/`: timestamped, append-only notes logging user-expressed opinions and stances on in-scope topics.

Create files lazily as they become useful, always under the vault root:

- `00_Meta/VAULT-MANIFEST.md`: vault conventions, folder roles, filename rules, link style, required frontmatter fields, and known exclusions. Use [VAULT-MANIFEST-FORMAT.md](./VAULT-MANIFEST-FORMAT.md).
- `00_Meta/Sources/SOURCE-REGISTER.md`: source inventory, provenance, processing status, and extraction coverage. Use [SOURCE-REGISTER-FORMAT.md](./SOURCE-REGISTER-FORMAT.md).
- `00_Meta/VAULT-AUDIT.md`: validation findings for filenames, links, frontmatter, tags, attachments, duplicates, and indexer risks. Use [VAULT-AUDIT-FORMAT.md](./VAULT-AUDIT-FORMAT.md).
- Permanent notes under `20_Permanent/`: evergreen, source-grounded Markdown notes. Use [NOTE-FORMAT.md](./NOTE-FORMAT.md).
- Opinion notes under `40_Opinions/`: timestamped, append-only records of user-expressed opinions on in-scope topics. Use [OPINION-FORMAT.md](./OPINION-FORMAT.md).
- Project notes, meeting notes, task analyses, or generated summary artifacts under the appropriate existing vault folder, or under `30_Projects/` when they are time-bound rather than evergreen.
- Task-analysis outputs from the `tasks` skill under `30_Projects/Task Reviews/` by default when no existing daily-note, review, or productivity folder convention exists.

## Default posture

- Preserve Obsidian compatibility over general Markdown portability.
- When the user provides a target knowledge base, integrate generated notes into that vault's graph: choose vault-relative paths, add frontmatter, use vault link conventions, update source provenance, and add backlinks or index links so the notes are discoverable.
- Prefer WikiLinks in body text unless the existing vault manifest requires another Obsidian-supported convention. Use the full WikiLink, embed, callout, and block-reference syntax in [references/OBSIDIAN-SYNTAX.md](./references/OBSIDIAN-SYNTAX.md) to make notes navigable rather than flat.
- Prefer a generated `.base` index ([references/BASES.md](./references/BASES.md)) over a hand-maintained static index/MOC when a domain or source cluster needs a note listing, so the index stays current automatically.
- Use typed frontmatter properties (text, number, checkbox, date, date-time, list, link) so notes are queryable by Bases and Dataview, not just human-readable.
- Keep filenames unique across the vault so `[[Note Title]]` links do not require brittle folder paths.
- Keep folder nesting shallow, ideally no more than 2-3 levels below the vault root.
- Put YAML frontmatter as the first content in each permanent note.
- Use strict YAML: quote strings containing colons or special characters, omit `#` from frontmatter tags, and use arrays for multi-value fields.
- Centralize attachments under `00_Meta/Attachments/` unless the vault already has a configured attachment folder.
- Do not dump entire long documents or source files into permanent notes. Extract stable concepts, claims, APIs, workflows, definitions, and decision-relevant relationships.
- Keep source provenance explicit. Every important note should identify the source document, code path, section, page, line range, commit, or other locator that supports it.
- Keep the user's subjective opinions separate from source-grounded facts. Record opinions only as attributed, timestamped opinion notes ([OPINION-FORMAT.md](./OPINION-FORMAT.md)); never merge them into a permanent note's `Source grounding` or present them as vault fact.
- For code collections, create human-readable concept, architecture, API, and workflow notes. Avoid copying large code blocks; use short snippets only when they clarify a stable interface or non-obvious mechanism.
- For task-analysis outputs, preserve the generated daily, weekly, monthly, or annual analysis as a time-bound artifact; extract only durable planning principles, recurring patterns, or project-relevant commitments into permanent notes when they are useful beyond the reviewed period.

## Opinion capture

While working with the user's vault, watch for opinions the user expresses on topics the knowledge base covers — directly ("I think X is the wrong tool") or indirectly (a preference, ranking, complaint, or value judgment that bears on an in-scope topic). When one arises, log it as a timestamped opinion note under `40_Opinions/` using [OPINION-FORMAT.md](./OPINION-FORMAT.md), link it to the subject topic note, and record it append-only so the temporal record of the user's evolving views is preserved.

- Resolve the opinion's `subject` to an existing topic note via titles, aliases, and tags. If the topic is clearly implied but has no note, create a minimal stub or hold the opinion in `10_Fleeting/`; do not force an unrelated link.
- When the user's view on a topic changes, write a new note that supersedes the prior one rather than editing history. Never fold an opinion into a source-grounded note or a `Source grounding` table.
- Log only opinions that bear on in-scope topics. Skip passing remarks, task instructions, and opinions unrelated to the knowledge base.
- Because logging a personal opinion is sensitive, tell the user the first time you record one in a session and honor any request to stop, exclude a topic, or keep a remark off the record.

Activation limit: this behavior applies while this skill is active on a vault task. It does not make opinion capture ambient across every conversation or session. For always-on capture regardless of whether the skill is invoked, the user must also add a directive to their `CLAUDE.md` or persistent memory (a shell hook cannot detect opinions semantically). Recommend this when the user wants capture to persist beyond skill-scoped work.

## When to ask before proceeding

Ask one focused question when the vault location, desired note granularity, treatment of source files, or permission to modify an existing vault would materially change the result. Otherwise state the assumption in `00_Meta/VAULT-MANIFEST.md` or `00_Meta/Sources/SOURCE-REGISTER.md` and proceed.

## Safety rules

- Before editing an existing vault, inspect its current folder conventions, frontmatter fields, link style, templates, and attachment settings.
- Do not overwrite existing notes. If a generated note would collide with an existing filename, merge only when the note covers the same concept and provenance supports the update; otherwise choose a unique, descriptive filename inside the target vault.
- Do not reorganize large parts of an existing vault unless the user explicitly asks for restructuring.
- Do not move or delete source documents, attachments, code repositories, or existing notes without explicit user approval.
- If large code repositories, generated output, datasets, dependency folders, or binary collections are inside the vault, record an Obsidian indexer risk and recommend exclusion rather than ingesting them directly into permanent notes.
