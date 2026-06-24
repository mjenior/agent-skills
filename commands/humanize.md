# humanize

Rewrite the target text so it sounds natural, human, and context-appropriate while preserving the original meaning.

Do not add new facts, claims, promises, caveats, citations, or arguments unless the user explicitly asks for them. If the target text is missing, ask for it.

## Rewrite goals

1. Make the text sound like a thoughtful person wrote it for the intended audience.
2. Remove robotic, formulaic, trite, or over-balanced assistant phrasing.
3. Prefer direct conclusions when the source supports them. Do not default to listing both sides of every point.
4. Use active voice where it fits naturally.
5. Vary sentence length and structure without making the prose literary, dramatic, or overwritten.
6. Keep the original level of formality unless the user asks for a specific tone.
7. Preserve technical terms, names, numbers, constraints, and intent.
8. Improve token economy by removing needless setup, repetition, hedging, and recap.

## Control verbosity

Make the rewrite as concise as the context allows. Preserve necessary nuance, but remove material that does not change the reader's understanding or next action.

Cut or compress:

- repeated ideas;
- obvious context the audience already has;
- long lead-ins before the main point;
- recap paragraphs that only restate prior content;
- caveats that do not affect the conclusion;
- examples that do not clarify a difficult point;
- stacked adjectives and intensifiers;
- meta-commentary about what the text is doing.

Do not shorten by removing important constraints, evidence, uncertainty, or action steps. If the source is intentionally detailed, preserve the detail but make each sentence earn its place.

## Remove assistant artifacts

Avoid patterns that make the text feel generated:

- throat-clearing phrases such as "It's important to note that", "It's worth mentioning that", "As an AI", and "Just to clarify";
- filler transitions such as "Furthermore", "Moreover", "Additionally", and "In addition";
- repetitive paragraph structure;
- generic disclaimers;
- unnecessary summaries of obvious information;
- forced symmetry, such as always presenting pros and cons;
- inflated words where simpler ones work;
- title-case headings unless the source or context requires them;
- emoji unless the source uses them or the user asks for them;
- exclamation points outside genuine warnings or errors;
- em dashes, en dashes, or ellipses used as stylistic punctuation.

## Keep the useful parts

- Keep the author's point of view, commitments, and uncertainty level.
- Keep bullets only when the items are genuinely parallel or easier to scan as a list.
- Keep headings when they improve navigation, but make them plain and natural.
- Keep domain-specific language when it is accurate and expected by the audience.
- Keep concise text concise. Do not expand short text just to sound conversational.

## Output

Return only the rewritten text unless the user asks for analysis or alternatives.

If meaning is ambiguous, make the smallest reasonable assumption and preserve the ambiguity rather than inventing details. If a rewrite would require choosing between materially different meanings, ask one focused question.
