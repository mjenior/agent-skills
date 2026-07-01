# Obsidian Opinion Note Format

Use this format to log a **user-expressed opinion, preference, judgment, or stance** on a topic the vault already covers, whether the user states it directly ("I think X is the wrong approach") or indirectly (a preference, complaint, ranking, or value judgment that bears on an in-scope topic). Opinions are subjective and user-owned. They are **not** source-grounded facts and must never be recorded in a `Source grounding` table or presented as vault fact — see [NOTE-FORMAT.md](./NOTE-FORMAT.md).

Store opinions as append-only, individually timestamped notes under `40_Opinions/`. One note per opinion entry. When the user's view on a topic changes, write a **new** note that supersedes the old one; do not overwrite or delete the prior entry, so the temporal record of how a view evolved stays intact.

```md
---
id: YYYYMMDDHHMMSS-slug
title: "Opinion: {short paraphrase of the stance}"
type: opinion
subject: "[[Topic Note]]"
topics: [topic-a, topic-b]
stance: "{one-line paraphrase of the position}"
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

# Opinion: {short paraphrase of the stance}

> [!quote] Expressed {YYYY-MM-DD}
> {The user's opinion, paraphrased faithfully, or a short verbatim quote when the exact wording matters.}

## Position

{One or two sentences stating the view in the user's terms. Keep it faithful; do not argue with it, resolve it, or convert a preference into a claim of fact.}

## Context

- Prompted by: {what in the conversation or vault triggered the statement}
- Relates to: `[[Topic Note]]` — {how this opinion bears on the topic}
- Directness: {direct statement | inferred from an indirect remark}

## Rationale

- {Any reasoning the user gave. Omit this section if none was offered rather than inventing one.}

## Related

- `[[Topic Note]]`: {the in-scope topic this opinion is about}
- `[[Opinion: earlier stance]]`: {only when this note supersedes or refines a prior opinion}
```

## Construction rules

- **Faithful, not editorialized.** Record the user's stance in their terms. Do not rebut it, resolve a contradiction, or launder a preference into a factual claim. If an opinion contradicts a source-grounded note, note the tension in `Context`; do not silently reconcile them.
- **Attribution and timing are mandatory.** Every opinion note must carry `origin`, `expressed` (a full `date-time`, the timestamp for temporal reference), and a resolved `subject` link. The `expressed` value is when the user stated it, not when a later edit occurred.
- **Append-only supersession.** A changed view is a new note with `status: current` and a `supersedes` link to the prior note; set the prior note's `status: superseded`. Use `withdrawn` when the user retracts a view without replacing it. Never edit the substance of a past opinion in place.
- **One current opinion per subject.** At most one note per `subject` should be `status: current`. Earlier entries remain in the vault as `superseded`/`withdrawn` history.
- **Subject resolution.** `subject` must point to an existing topic note whenever one exists (resolve via titles, aliases, and tags). If the opinion clearly concerns an implied-but-absent topic, create a minimal stub note or hold the opinion in `10_Fleeting/` until a topic note exists — do not force an unrelated link.
- **Typed frontmatter.** Keep `expressed` a `date-time`, `subject`/`supersedes` links (quoted), and `topics`/`tags` arrays, so opinions are queryable by Bases — see [references/BASES.md](./references/BASES.md) for an opinion-timeline view.
- **Scope.** Log opinions that bear on topics the vault covers. Do not log passing remarks, task instructions, or opinions unrelated to the knowledge base.
