# Obsidian Permanent Note Format

Use this format for evergreen notes under `20_Permanent/`. Adapt headings only when the existing vault manifest defines a different convention. For Obsidian Flavored Markdown syntax in the body — embeds, callouts, heading and block references, math, Mermaid — see [references/OBSIDIAN-SYNTAX.md](./references/OBSIDIAN-SYNTAX.md).

```md
---
id: YYYYMMDDHHMMSS-slug
title: "Note Title"
aliases: [Alternate Name, Acronym, CodeSymbol]
tags: [domain, concept-type]
sources: [SRC-0001]
status: seedling | evergreen | draft | archived
created: YYYY-MM-DD
updated: YYYY-MM-DD
---

# Note Title

One concise paragraph that defines the concept, claim, workflow, API, entity, or decision. Make the note useful when read alone, but link the first meaningful mention of related concepts with WikiLinks.

## Core idea

- {Important point, definition, relationship, or mechanism.}
- {Use bullets only when they improve scanning.}
- {Link related notes as `[[Related Concept]]`.}

## Details

{Short prose, compact tables, or small structured lists. Keep this note focused on one durable concept. Split overloaded notes into separate linked notes.}

## Source grounding

| source | locator | supports |
| --- | --- | --- |
| `SRC-0001` | `{page, section, heading, file path, symbol, or line range}` | {claim, definition, API behavior, result, limitation} |

## Related

- `[[Related Concept]]`: {relationship in one phrase}
- `[[Source or Project Note]]`: {relationship in one phrase}

## Open questions

- {Only include material ambiguity that affects retrieval, interpretation, implementation, or future updates.}
```

## Note construction rules

- The frontmatter must be the first content in the file.
- `title` must match the note's canonical title.
- `aliases` should include acronyms, source-specific names, old names, key code symbols, and common search terms.
- `tags` should be broad retrieval facets without `#` prefixes. Prefer links over tag proliferation.
- `sources` must list source IDs from `00_Meta/Sources/SOURCE-REGISTER.md`.
- Frontmatter properties are typed. Obsidian recognizes text, number, checkbox (`true`/`false`), date (`YYYY-MM-DD`), date-time (`YYYY-MM-DDTHH:mm:ss`), list (YAML array), and link (`"[[Note]]"`, quoted). Use the correct type for any field you add — for example a numeric rating or a real date — so the note is queryable by Bases and Dataview, not just human-readable.
- Use WikiLinks for internal links. Add a block ID (`^id`) to any claim a `Source grounding` row or another note will cite, then reference it with `[[Note#^id]]`.
- Do not paste large source excerpts. Quote only the minimum text needed for fidelity or later verification.
- For code notes, include short snippets only when they clarify a stable interface, schema, invariant, or non-obvious mechanism. Cite file path and line range.
- For scientific notes, separate method, evidence, limitations, and interpretation when combining them would hide uncertainty.
- For non-technical notes, distinguish facts, decisions, obligations, preferences, timelines, and interpretations.
- Do not record the user's subjective opinions or stances here. A permanent note holds source-grounded content; log user opinions as attributed, timestamped notes using [OPINION-FORMAT.md](./OPINION-FORMAT.md) and link them from this note's `Related` section instead of embedding them in `Source grounding`.
- Keep one note focused on one concept. If the title needs "and", "overview", or multiple unrelated aliases, consider splitting it.
