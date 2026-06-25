---
name: notes
description: Synthesize a meeting audio recording and personal .txt notes into a polished markdown artifact with accurate discussion summaries, decisions, action items, risks, open questions, and follow-up requirements. Use this skill when the user provides meeting notes plus an audio recording or transcript and asks for meeting content analysis, minutes, action extraction, or a summary document.
---

Use this skill to create a high-quality meeting synthesis from a recording and accompanying personal notes.

The final deliverable is a markdown file artifact, not a chat-only response, unless the user asks otherwise.

## Source policy

1. Treat the meeting recording or transcript as the primary evidence source.
2. Use the personal notes as secondary guidance for emphasis, terminology, acronyms, names, topic anchors, and the note-taker's priorities.
3. Do not let the notes determine, constrain, or filter the final content. Include important meeting content even when the notes omit it.
4. Treat note-only claims as lower confidence unless the recording supports them. Include them only when useful, and label them as note-derived or requiring clarification.
5. Never invent participants, deadlines, decisions, owners, technical details, deliverables, or project names.

## Method

1. Review the full recording or transcript before writing. For long meetings, segment the content and keep a running evidence map so early topics are not lost.
2. Review the notes and identify emphasized topics, terminology, possible timestamps, follow-up cues, and discrepancies with the recording.
3. Build a concise meeting model:
   - purpose or objective;
   - participants and roles, when known;
   - major discussion threads;
   - decisions and non-decisions;
   - action items and owners;
   - risks, blockers, dependencies, and open questions.
4. Group related discussion by topic, even if it occurred in multiple parts of the meeting.
5. Separate facts from interpretation. Mark missing owners, dates, or status as `Not specified` rather than guessing.
6. Attribute points to speakers when speaker identity is clear. Use `Unidentified speaker` when identity is uncertain.
7. Before finalizing, verify that every listed decision and action item is supported by the recording or clearly labeled as note-derived.

## Output structure

Create a markdown file with this structure, adapting section depth to the meeting's complexity. Omit sections that have no substantive content, except for action items and open questions.

```markdown
# Meeting summary

## Meeting overview

| Field | Details |
| --- | --- |
| Purpose | ... |
| Participants | ... |
| Duration | ... |
| Overall outcome | ... |

## Executive summary

...

## Major discussion topics

### <topic title>

**Summary:** ...

**Key points**

- ...

**Decisions**

- ...

**Risks or concerns**

- ...

**Open questions**

- ...

**Related action items**

- ...

## Decisions register

| Decision | Context | Decision maker(s) | Evidence source |
| --- | --- | --- | --- |
| ... | ... | ... | Recording |

## Action items

| Owner | Action item | Deliverable | Due date | Dependencies | Status |
| --- | --- | --- | --- | --- | --- |
| ... | ... | ... | Not specified | ... | ... |

## Risks and blockers

| Risk or blocker | Impact | Owner | Mitigation or next step |
| --- | --- | --- | --- |
| ... | ... | Not specified | ... |

## Open questions

- ...

## Follow-up requirements

- ...

## Important context and insights

- ...

## Information requiring clarification

- ...

## Notes and recording alignment

### Supported by both sources

- ...

### Present primarily in recording

- ...

### Present primarily in notes

- ...
```

## Quality bar

- Make the artifact complete enough to support follow-up work without re-listening to the meeting.
- Keep prose concise and professional. Prefer dense, specific bullets over generic summaries.
- Do not produce a transcript unless requested.
- Do not include a notes-to-recording alignment section if it would only restate the summary or expose irrelevant note-taking artifacts.
- If the recording is unavailable or unusable, state that the output is based only on notes and lower the confidence accordingly.
