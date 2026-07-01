# Obsidian Knowledge Base Workflow

Use this workflow to create, update, parse, or audit an Obsidian-compatible local knowledge base from user-provided technical/scientific documents, non-technical documents, code collections, meeting summaries, and task-analysis outputs. The coordinator owns all phase gates. Subagents are bounded workers; their outputs are advisory until the coordinator verifies them against source material, vault conventions, and Obsidian compatibility rules.

## Objectives

- Convert varied source material into a coherent Obsidian vault with shallow folders, unique filenames, stable WikiLinks, strict YAML frontmatter, and centralized attachments.
- When the user provides a target vault or knowledge base, create or update the output notes inside that vault and connect them to the existing graph rather than returning detached markdown artifacts.
- Preserve provenance from source documents, code paths, pages, sections, line ranges, commits, task-note dates, analysis periods, or other locators.
- Split long or complex sources into durable evergreen notes rather than source-shaped summaries.
- Maintain a graph that is readable for humans and predictable for LLMs: clear note titles, explicit aliases, consistent tags, backlinks, and short concept notes.
- Audit and repair Obsidian-specific risks: duplicate filenames, invalid YAML, broken WikiLinks, orphaned attachments, inconsistent tags, deep nesting, and indexer-heavy folders.
- Capture user-expressed opinions on in-scope topics as attributed, timestamped, append-only opinion notes, kept distinct from source-grounded facts.

## Obsidian compatibility rules

1. Keep the vault as standard local Markdown files and attachments.
2. Prefer WikiLinks for internal links: `[[Note Title]]` or `[[Note Title|display text]]`.
3. Use unique note filenames across the entire vault. Avoid duplicate names such as `Index.md` in multiple folders.
4. Use cross-platform-safe filenames: letters, numbers, spaces, hyphens, and underscores. Avoid `/`, `:`, `\\`, control characters, and other OS-hostile punctuation.
5. Keep folder depth shallow. Default to top-level domain folders under `20_Permanent/` only when they improve human navigation.
6. Put attachments in `00_Meta/Attachments/` unless an existing vault convention says otherwise.
7. Put frontmatter first, bounded by `---` lines.
8. In frontmatter, use arrays for `aliases`, `tags`, `sources`, and `related`. Do not prefix frontmatter tags with `#`.
9. Quote YAML strings that contain colons, brackets, braces, hashes, dates that should remain strings, or other special characters.
10. Do not store large generated folders, dependency directories, datasets, or code repositories as indexed knowledge notes. If they must remain in the vault, mark them as Obsidian indexer exclusions in the manifest or audit.
11. Use the full Obsidian Flavored Markdown syntax in [references/OBSIDIAN-SYNTAX.md](./references/OBSIDIAN-SYNTAX.md) — heading and block references, embeds, and callouts — so notes are navigable, not flat. Use typed frontmatter properties so notes are queryable.
12. Prefer a generated `.base` view ([references/BASES.md](./references/BASES.md)) for note listings that should stay current, and reserve `.canvas` ([references/CANVAS.md](./references/CANVAS.md)) for visual artifacts. Both are standard local files and keep the vault portable.

## Model and thinking tiers

Use the smallest tier that can satisfy the phase gate.

| tier | use when | model | thinking | max task size |
| --- | --- | --- | --- | --- |
| quick | Mechanical vault checks, filename normalization proposals, duplicate detection, link checks, attachment inventory, simple non-technical extraction. | GPT-5.5 or Claude Haiku-4.5 | low | 10-40 files or less than 20 minutes |
| standard | Mixed document ingestion, ordinary technical notes, source registers, note merging, metadata normalization, moderate code walkthroughs. | GPT-5.5 or Claude Sonnet-4.6 | medium | One source cluster, 5-15 documents, or 3-8 related code files |
| complex | Dense scientific papers, specifications, mathematical material, large architecture documents, ambiguous terminology, cross-document synthesis, or codebases with many entry points. | GPT-5.5 or Claude Opus-4.8 | high | One complex source, one subsystem, or any high-risk synthesis task |
| verifier | Independent audit of note quality, link integrity, frontmatter validity, source-grounding, merge decisions, and Obsidian compatibility. | GPT-5.5 or Claude Sonnet-4.6; use Claude Opus-4.8 for complex source-grounding review | medium by default, high for complex tier changes | Bounded to changed notes, source cluster, or vault audit |

## Subagent roles

- **Coordinator**: Owns vault scoping, conventions, phase gates, note taxonomy, source register, integration, and final validation. Use GPT-5.5 or Claude Opus-4.8 with high thinking for large or highly technical vault builds; use GPT-5.5 or Claude Sonnet-4.6 with medium thinking otherwise.
- **Vault cartographer**: Inspects existing folder structure, `.obsidian/` settings when present, filename conventions, templates, frontmatter patterns, tags, attachments, and link style. Use GPT-5.5 or Claude Sonnet-4.6 with medium thinking; use GPT-5.5 or Claude Haiku-4.5 with low thinking for small vault inventories.
- **Source inventory scouts**: Catalog user-provided documents and code by type, size, complexity, provenance, and processing priority. Use GPT-5.5 or Claude Haiku-4.5 with low thinking for simple inventories; use GPT-5.5 or Claude Sonnet-4.6 with medium thinking for mixed or messy collections.
- **Document extractors**: Extract concepts, claims, definitions, procedures, entities, limitations, and relationships from documents. Use GPT-5.5 or Claude Sonnet-4.6 with medium thinking; use GPT-5.5 or Claude Opus-4.8 with high thinking for scientific, mathematical, legal, medical, or high-density technical material.
- **Code extractors**: Trace entry points, architecture, APIs, data models, configuration, tests, and operational workflows from code. Use GPT-5.5 or Claude Sonnet-4.6 with medium thinking; use GPT-5.5 or Claude Opus-4.8 with high thinking for large subsystems, dynamic dispatch, generated code, or cross-language projects.
- **Note composers**: Convert verified extractions into Obsidian notes following [NOTE-FORMAT.md](./NOTE-FORMAT.md). Use GPT-5.5 or Claude Sonnet-4.6 with medium thinking; use GPT-5.5 or Claude Haiku-4.5 with low thinking only for mechanical formatting from already verified extracts.
- **Vault verifier**: Checks final notes for broken links, duplicate filenames, malformed YAML, inconsistent tags, missing provenance, attachment issues, and excessive note length. Use GPT-5.5 or Claude Sonnet-4.6 with medium thinking; escalate to GPT-5.5 or Claude Opus-4.8 with high thinking when verifying dense source-grounding.

## Parallelization defaults

- Do not use subagents for fewer than 10 short files or a small single-topic update where coordination costs exceed direct work.
- Use 2-4 source inventory scouts for 25-100 mixed files. Use up to 6 only when files split cleanly by document type, domain, project, or source folder.
- For one very large file, split by sections, chapters, headings, page ranges, or code regions. Use 2-4 extractors for complex documents; use 1 extractor when the document is short or highly sequential.
- For many smaller files, batch by source type and domain: scientific papers, manuals, meeting notes, task analyses, policy documents, README/docs, source code, tests, configuration, and data schemas.
- Assign code extractors by subsystem or entry-point boundary, not by arbitrary file count.
- Keep note taxonomy, filename selection, merge decisions, and final link integration coordinator-owned to prevent duplicate concepts and inconsistent aliases.
- Run verifier subagents after composition, not in parallel with note writers that are still changing the same files.
- Never let parallel subagents write to the same note or source register section. They return bounded extraction output; the coordinator integrates.

## Phase gates

| phase | gate |
| --- | --- |
| vault scope | Vault root, creation/update mode, source locations, and modification permissions are known or explicitly assumed. |
| vault conventions | `00_Meta/VAULT-MANIFEST.md` exists or current conventions are recorded before new notes are written. |
| source inventory | `00_Meta/Sources/SOURCE-REGISTER.md` lists source IDs, types, locations, sizes, complexity, and processing status. |
| extraction | Extracted claims, concepts, code facts, and relationships cite source IDs and precise locators where available. |
| note planning | Proposed notes have unique filenames, target folders inside the vault, aliases, tags, source IDs, graph entry points, and merge/update decisions. |
| composition | Notes follow [NOTE-FORMAT.md](./NOTE-FORMAT.md), use Obsidian WikiLinks, include provenance, and are written to vault-relative paths. |
| integration | New and updated notes are referenced from relevant index, project, source, or related concept notes, and `SOURCE-REGISTER.md` lists the produced notes. |
| opinion capture | Any user opinion on an in-scope topic is logged under `40_Opinions/` with a resolved `subject` link, an `expressed` timestamp, `origin`, and `status`, append-only and outside any `Source grounding`. |
| vault validation | Links, filenames, frontmatter, tags, attachments, nesting depth, and indexer risks have been checked and recorded in `00_Meta/VAULT-AUDIT.md` when useful. |

## Workflow loop

### 0. Scope the vault and source collection

1. Identify whether the user wants to create a new vault, update an existing vault, parse an existing vault, or audit/repair Obsidian compatibility.
2. Identify the vault root and source locations. Sources may be PDFs, Markdown, text files, office documents converted to text, code repositories, API docs, notebooks, diagrams, meeting notes, task-analysis outputs from the `tasks` skill, or mixed folders.
3. If the user provides a target vault or knowledge base, use that directory as the write target for every output note. Do not create parallel notes outside the vault unless the user explicitly requests a separate export.
4. Inspect current vault conventions before writing. Check folder names, note naming, templates, frontmatter fields, aliases, tags, link style, attachment location, and `.obsidian/` settings when available.
5. Ask one focused question only if the wrong assumption could cause overwritten notes, misplaced sources, incompatible link style, or a substantially different note granularity.
6. Write or update `00_Meta/VAULT-MANIFEST.md` using [VAULT-MANIFEST-FORMAT.md](./VAULT-MANIFEST-FORMAT.md).

### 1. Inventory sources

1. Assign each source a stable source ID.
2. Record path, type, size, language, domain, author or origin when known, date/version when known, and access limitations.
3. Classify processing complexity:
   - `simple`: short non-technical or single-topic files;
   - `standard`: moderate technical or mixed-domain files;
   - `complex`: dense scientific, mathematical, legal, medical, code-heavy, or cross-referenced files;
   - `huge`: files too large for one reliable pass and requiring sectioned extraction.
4. Identify source clusters that should be processed together because they share terminology, provenance, or code boundaries.
5. Update `00_Meta/Sources/SOURCE-REGISTER.md` using [SOURCE-REGISTER-FORMAT.md](./SOURCE-REGISTER-FORMAT.md).

### 2. Plan the note graph

1. Choose note granularity by concept, not by source file. A single source can yield many notes; several sources can update one concept note.
2. Prefer permanent notes for durable concepts, workflows, APIs, entities, algorithms, experimental findings, architecture decisions, and recurring terminology.
3. Keep project-specific execution notes in `30_Projects/` and temporary import notes in `10_Fleeting/`.
4. Define canonical note titles and vault-relative output paths before writing. Titles should be unique, descriptive, and stable.
5. For task-analysis outputs, preserve the period in the filename and place files under the existing vault convention for daily notes, reviews, or productivity logs. If no convention exists, use `30_Projects/Task Reviews/daily/`, `30_Projects/Task Reviews/weekly/`, `30_Projects/Task Reviews/monthly/`, or `30_Projects/Task Reviews/annual/`.
6. Define graph entry points for each note: related concept links, source links, project links, index/MOC links, task review indexes, or backlinks from existing notes. When a domain or source cluster needs a note listing, plan a generated `.base` index ([references/BASES.md](./references/BASES.md)) driven by frontmatter rather than a hand-maintained static index, so it stays current; embed it in a human-facing MOC with `![[Index.base]]`.
7. Define aliases for acronyms, synonyms, old names, paper-specific terms, and code symbols that users or agents may search for later.
8. Define tags sparingly. Use tags for broad retrieval facets, not as a replacement for links.
9. Decide merge vs create:
   - merge when an existing note covers the same concept and the new source adds evidence, nuance, or updated behavior;
   - create when the concept is distinct, the existing title would become overloaded, or source terminology needs a separate bridge note.

### 3. Extract source facts

1. Read source material directly. Do not infer technical claims from filenames, abstracts, tables of contents, or code names alone.
2. For scientific and technical documents, extract:
   - problem statement and motivation;
   - definitions and notation;
   - method, architecture, algorithm, or protocol;
   - assumptions and constraints;
   - empirical setup, datasets, metrics, and results;
   - limitations, failure modes, and open questions;
   - relationships to existing notes.
3. For non-technical documents, extract:
   - people, organizations, goals, decisions, timelines, obligations, definitions, and action-relevant context;
   - claims that affect interpretation of technical sources;
   - contradictions or unresolved ambiguity.
4. For task-analysis outputs, preserve the generated analysis as a period-specific artifact and extract only:
   - recurring execution patterns;
   - durable planning rules or constraints;
   - project-relevant commitments, blockers, and follow-up themes;
   - links to related project notes, daily notes, or review indexes.
   Do not promote every daily task into a permanent note.
5. For code, extract:
   - entry points and exported surfaces;
   - module responsibilities;
   - data models, schemas, configuration, environment variables, and persisted formats;
   - runtime flows, state transitions, and integration boundaries;
   - tests and examples that reveal intended behavior;
   - stable symbols worth adding as aliases or backlinks.
6. Record precise locators: section, heading, page, paragraph, line range, file path, function/class, commit, test name, task-note date, or analysis period.
7. Mark uncertain or conflicting facts explicitly. Do not smooth contradictions into a false synthesis.

### 4. Compose and integrate Obsidian notes

1. Use [NOTE-FORMAT.md](./NOTE-FORMAT.md) for permanent notes and [references/OBSIDIAN-SYNTAX.md](./references/OBSIDIAN-SYNTAX.md) for Obsidian Flavored Markdown syntax (embeds, callouts, heading/block references, math, Mermaid).
2. Write notes to their planned vault-relative paths. If a target vault was provided, all output notes must be created or updated inside it.
3. Put frontmatter first and keep it valid YAML.
4. Use a concise opening definition or claim so the note is useful in preview and graph contexts.
5. Link related concepts with WikiLinks on first meaningful mention.
6. Add aliases for acronyms, alternate names, code symbols, and source-specific terminology.
7. Keep notes focused. If a note grows beyond one concept or becomes difficult to scan, split it and link the parts.
8. Prefer prose and compact tables over long pasted excerpts.
9. Include short code snippets only when they explain an API, schema, invariant, or non-obvious mechanism. Cite the source path and line range.
10. Add a `Source grounding` section for claims that may need verification later.
11. Add `Open questions` only for material ambiguity that affects retrieval, interpretation, or future updates.
12. For task-analysis outputs, add frontmatter fields that support retrieval, such as `analysis_type`, `period_start`, `period_end`, `source_task_notes`, `projects`, `tags`, and `sources`, while preserving the analysis headings produced by the `tasks` skill.
13. Integrate each note into the vault graph by adding or updating relevant links in existing index, project, source, task review, or related concept notes when such notes exist. If no natural entry point exists, create the smallest useful index or source note rather than leaving the note orphaned.
14. Update `00_Meta/Sources/SOURCE-REGISTER.md` so each processed source lists the notes it produced or updated.

### 5. Update and maintain existing notes

1. Before creating a note, search for existing titles, aliases, and backlinks that cover the concept.
2. Preserve existing user-written content unless it is clearly obsolete, contradicted by newer source evidence, or duplicated by a cleaner integrated note.
3. When updating, add source-backed changes in place rather than appending disconnected summaries.
4. Update aliases, tags, backlinks, source lists, task review indexes, and index/project references with the same change.
5. If two notes overlap, recommend a merge only when both note purposes are redundant. Otherwise add bridge links explaining the distinction.
6. Record major convention changes in `00_Meta/VAULT-MANIFEST.md`.

### 6. Validate the vault

Run the narrowest available checks for the touched scope. When Obsidian is running, the `obsidian` CLI ([references/OBSIDIAN-CLI.md](./references/OBSIDIAN-CLI.md)) is an optional, more reliable backend for link, backlink, tag, and render checks; otherwise perform the same checks via direct filesystem reads. Do not block the workflow on the CLI.

1. Duplicate filenames across the vault.
2. Invalid or missing frontmatter in permanent notes.
3. Frontmatter tags containing `#`.
4. Broken WikiLinks or links that resolve ambiguously.
5. Missing aliases for common acronyms and source names.
6. Orphan notes created by the workflow that should link into the graph.
7. Processed sources missing produced-note entries in `SOURCE-REGISTER.md`.
8. Task-analysis outputs missing expected period frontmatter, task-review index links, or source task-note provenance.
9. Generated notes written outside the target vault when a vault was provided.
10. Attachments outside the configured attachments folder.
11. Deeply nested folders beyond the vault convention.
12. Large non-Markdown folders inside the vault that may slow the Obsidian indexer.
13. Notes without source grounding when they contain technical, scientific, code, medical, legal, productivity-pattern, or factual claims.
14. `.base` files: valid YAML, every referenced property/formula defined, Duration math accessing a numeric field before rounding, and null-guarded formulas (see [references/BASES.md](./references/BASES.md)).
15. `.canvas` files: valid JSON, unique node and edge IDs, and every edge `fromNode`/`toNode` resolving to an existing node (see [references/CANVAS.md](./references/CANVAS.md)).
16. Opinion notes under `40_Opinions/`: each has `origin`, an `expressed` `date-time`, and a resolved `subject` link; at most one `current` note per `subject`; any superseded/withdrawn note retains a valid `supersedes` chain.
17. No user opinion recorded in a permanent note's `Source grounding` table or presented as source-grounded fact.

Record results in `00_Meta/VAULT-AUDIT.md` using [VAULT-AUDIT-FORMAT.md](./VAULT-AUDIT-FORMAT.md) when the task is more than a trivial edit.

### Ongoing: capture user opinions

This step is reactive rather than sequential: it runs whenever, during any phase, the user expresses an opinion, preference, judgment, or stance on a topic the vault covers — directly or indirectly.

1. Detect the opinion. Direct: the user states a position on a named in-scope concept. Indirect: a preference, ranking, complaint, or value judgment that bears on an in-scope topic without naming it. Ignore passing remarks, task instructions, and opinions unrelated to the knowledge base.
2. Resolve the `subject`. Match the opinion to an existing topic note via titles, aliases, and tags. If the topic is clearly implied but has no note, create a minimal stub under `20_Permanent/` or hold the opinion in `10_Fleeting/` until a topic note exists. Do not attach the opinion to an unrelated note.
3. Compose an opinion note under `40_Opinions/` using [OPINION-FORMAT.md](./OPINION-FORMAT.md): faithful paraphrase, `subject` link, `origin: conversation`, an `expressed` `date-time`, `polarity`, `confidence`, and `status: current`. Timestamp with the moment the user stated it.
4. Handle change over time append-only. If the user's view on a subject already has a `current` opinion note, write a new note with a `supersedes` link and set the prior note's `status` to `superseded` (or `withdrawn` if retracted without replacement). Never edit the substance of a past opinion in place.
5. Keep opinions distinct from facts. Never record an opinion in a permanent note's `Source grounding` or convert it into a factual claim. If an opinion conflicts with a source-grounded note, note the tension in the opinion's `Context`; do not reconcile it silently.
6. Tell the user the first time you log an opinion in a session, and honor any request to stop, exclude a topic, or keep a remark off the record.

## Output to the user

Return a concise summary with:

- vault path;
- source count processed;
- notes created, updated, and skipped;
- opinions logged, with subject and supersession changes, when any were captured;
- validation performed and results;
- unresolved source gaps, broken links, merge candidates, or indexer risks;
- paths to the manifest, source register, audit, and notable notes.
