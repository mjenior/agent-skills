# Deep Research Workflow

Use this workflow to convert an open-ended topic into a verified, synthesized research brief. The coordinator owns all phase gates. Subagents are bounded workers; their outputs are advisory until the coordinator checks them against verified sources and the research goal.

## Objectives

- Define the user's target understanding before searching.
- Discover high-quality resources from distinct angles rather than repeating the same search path.
- Verify each source exists and is inspectable before extracting claims from it.
- Extract claims with citations, quotes or excerpts, uncertainty, and contradiction tracking.
- Use skeptic review to challenge scope drift, weak evidence, overconfident synthesis, and hallucinated conclusions.
- Produce a final synthesis that answers the research goal with traceable evidence.

## Model and thinking tiers

Use the smallest tier that can satisfy the phase gate.

| tier | use when | model | thinking | max task size |
| --- | --- | --- | --- | --- |
| quick | Link checks, metadata verification, duplicate detection, mechanical register updates. | GPT-5.5 or Claude Haiku-4.5 | low | 5-15 sources or less than 20 minutes |
| standard | Focused source discovery, source quality assessment, claim extraction from clear sources. | GPT-5.5 or Claude Sonnet-4.6 | medium | One research facet or 3-5 sources |
| complex | Ambiguous domains, specialized technical or academic material, conflicting evidence, legal, medical, financial, policy, security, or scientific claims. | GPT-5.5 or Claude Opus-4.8 | high | Any high-risk or expert-level facet |
| skeptic | Challenge the goal, source strategy, evidence quality, contradictions, and synthesis. | GPT-5.5 or Claude Opus-4.8 | high | Bounded to the current artifact set |
| synthesis | Integrate extracted evidence into a final answer with uncertainty and traceability. | GPT-5.5 or Claude Opus-4.8 | high | Full verified evidence set |

## Subagent roles

- **Coordinator**: Owns the research goal, phase gates, source register, claims matrix, synthesis, and final quality. Use GPT-5.5 or Claude Opus-4.8 with high thinking for complex topics; use GPT-5.5 or Claude Sonnet-4.6 with medium thinking otherwise.
- **Goal reviewer**: Optional verifier for unclear or high-stakes goals. Challenges whether the goal, audience, depth, exclusions, and stopping criteria are concrete enough. Use GPT-5.5 or Claude Sonnet-4.6 with medium thinking, escalating to GPT-5.5 or Claude Opus-4.8 with high thinking for high-stakes goals.
- **Resource scouts**: Find candidate sources from distinct facets, source types, regions, schools of thought, or time periods. Use GPT-5.5 or Claude Sonnet-4.6 with medium thinking; use GPT-5.5 or Claude Opus-4.8 with high thinking for specialized domains.
- **Existence verifiers**: Confirm each candidate source exists, is reachable or otherwise identifiable, and has enough metadata for citation. Use GPT-5.5 or Claude Haiku-4.5 with low thinking unless provenance is ambiguous.
- **Evidence extractors**: Read verified sources and extract only goal-relevant claims into the claims matrix. Use GPT-5.5 or Claude Sonnet-4.6 with medium thinking; use GPT-5.5 or Claude Opus-4.8 with high thinking for dense academic, legal, technical, or contradictory sources.
- **Skeptic**: Challenges current conclusions, source mix, search directions, evidence gaps, and hallucination risk. Use GPT-5.5 or Claude Opus-4.8 with high thinking. Deploy after goal setting, after source verification for complex topics, and after preliminary synthesis.
- **Synthesis writer**: Produces the final research brief from the claims matrix and source register. Use GPT-5.5 or Claude Opus-4.8 with high thinking.

## Parallelization defaults

- Keep goal decision, skeptic review, and final synthesis serial. These phases require one coherent interpretation of the research target.
- Run 2-4 resource scouts in parallel for most deep research. Use 1 scout for narrow topics and up to 6 only when the goal naturally splits into independent facets or source types.
- Run existence verification in parallel after candidate collection. Use 4-8 quick verifiers when there are many independent links or citations, but cap lower when tools have rate limits or when sources require judgment rather than mechanical checking.
- Run evidence extraction in parallel by source or source cluster. Use 3-6 extractors when sources are independent. Use fewer when sources cross-reference each other heavily or require shared domain context.
- Do not let parallel agents independently update the final synthesis. They write bounded findings; the coordinator integrates.
- Assign every parallel subagent explicit scope, source IDs or search facets, output format, and unassessed boundaries.
- Avoid duplicate work. Do not assign multiple agents to the same source unless independent review is intentionally needed for a high-stakes or contradictory source.

## Phase gates

| phase | gate |
| --- | --- |
| goal decision | `RESEARCH-GOAL.md` states the question, audience, desired level of understanding, scope, exclusions, source quality bar, and stopping criteria. |
| search plan | Research facets and source types cover the goal without obvious gaps or redundant scouts. |
| resource discovery | Candidate sources are listed with source type, why they may matter, expected contribution, and scout confidence. |
| existence verification | Every source used downstream has an ID, title or name, URL or stable locator, access status, citation metadata when available, and verification result. |
| extraction | Every material extracted claim has a verified source ID, quote or excerpt when possible, location within the source, confidence, and contradiction notes. |
| skeptic review | The skeptic has challenged goal fit, evidence quality, contradictions, missing perspectives, and overclaims; coordinator resolved or recorded each challenge. |
| synthesis | The final brief answers the goal, cites source IDs, separates evidence from interpretation, and states remaining uncertainty. |

## Workflow loop

### 0. Goal decision

1. Extract the topic or question from the user request.
2. Determine the user's intended use: decision support, learning, market scan, technical design, literature review, competitive analysis, historical understanding, or another purpose.
3. Determine desired level of understanding:
   - **orientation**: enough to understand the landscape and vocabulary;
   - **working knowledge**: enough to make practical choices or explain tradeoffs;
   - **decision-grade**: enough to support a consequential recommendation;
   - **expert map**: enough to identify schools of thought, unresolved debates, and research frontiers.
4. Write or update `RESEARCH-GOAL.md` using [GOAL-FORMAT.md](./GOAL-FORMAT.md).
5. Ask one question only if the missing answer changes the source quality bar, scope, or final deliverable.
6. For broad, high-stakes, or ambiguous research, run a goal reviewer or skeptic before search begins.

### 1. Search plan

1. Break the goal into 3-6 research facets. Each facet should answer a distinct part of the goal.
2. Define source types required for each facet, such as primary sources, peer-reviewed papers, standards, official docs, datasets, books, reputable journalism, expert commentary, or community evidence.
3. Assign resource scouts by facet or source type.
4. State explicit exclusions so scouts do not chase tangents.

Resource scout output:

```md
## Scout: {facet or source type}

### Search strategy
- Queries, databases, repositories, catalogs, or websites checked.

### Candidate sources
- `CAND-{n}`: {title/name} - {URL or locator}
  - Type: {primary source | paper | docs | dataset | book | article | expert commentary | community | other}
  - Expected contribution: {why this source may help the goal}
  - Quality signal: {authority, primary status, citations, recency, domain reputation}
  - Known limitation: {bias, age, access issue, narrow scope, unknown}

### Unassessed scope
- {what this scout did not check}
```

### 2. Resource discovery

1. Run scouts according to the parallelization defaults.
2. Coordinator merges candidates into `SOURCE-REGISTER.md`.
3. Deduplicate by canonical URL, DOI, title, author, publication date, and content identity.
4. Keep rejected candidates when the rejection prevents future duplicate search.
5. For decision-grade and expert-map goals, require source diversity across methods, perspectives, and incentives.

### 3. Existence verification

1. Assign verifiers candidate source IDs from `SOURCE-REGISTER.md`.
2. Confirm the source exists and can be cited. Acceptable verification includes fetching the page, opening the document, confirming DOI or ISBN records, checking official documentation, or locating stable archival/catalog metadata.
3. Record access limitations. A paywalled source can be verified but cannot support detailed claims unless the relevant content was inspected.
4. Mark sources as:
   - `verified`: source exists and content or metadata is inspectable;
   - `partially verified`: source exists but content access is limited;
   - `rejected`: source does not exist, is inaccessible, is duplicate, is low quality, or does not fit the goal.
5. Do not extract claims from unverified or rejected sources.

Existence verifier output:

```md
## Verification batch: {source IDs}

- `{source-id}`: {verified | partially verified | rejected}
  - Stable locator: {URL, DOI, ISBN, archive URL, catalog record}
  - Metadata: {title, author/org, publication date, publisher, version when available}
  - Access checked: {what was opened or confirmed}
  - Notes: {access limits, duplicates, rejection reason}
```

### 4. Evidence extraction

1. Assign verified sources to extractors by source or source cluster.
2. Extract only claims that help answer `RESEARCH-GOAL.md`.
3. Preserve direct quotes or short excerpts for important claims when legally and practically possible.
4. Record source location: section, page, timestamp, table, figure, line, or heading.
5. Classify each claim as:
   - `fact`: directly stated or measured;
   - `interpretation`: an author's analysis or argument;
   - `estimate`: model, forecast, or extrapolation;
   - `normative`: recommendation or value judgment;
   - `context`: background needed to understand other claims.
6. Note contradictions, source incentives, methods, sample sizes, and uncertainty.
7. Update `CLAIMS-MATRIX.md` using [CLAIMS-MATRIX-FORMAT.md](./CLAIMS-MATRIX-FORMAT.md).

### 5. Skeptic review

Deploy the skeptic at minimum after preliminary extraction and before final synthesis. Also deploy after goal decision or source verification when the topic is broad, high-stakes, or prone to misinformation.

Skeptic review questions:

1. Does the current source set answer the goal, or is it drifting toward what was easiest to find?
2. Are any source types overrepresented because they are easy to search?
3. Which claims rely on weak, circular, outdated, promotional, or non-primary evidence?
4. What plausible counterargument or missing perspective would change the synthesis?
5. Are there contradictions that the synthesis might flatten incorrectly?
6. Are any conclusions stronger than the evidence supports?
7. What must be researched next to satisfy the stopping criteria?

Skeptic output:

```md
## Skeptic review

### Blocking issues
- {issue, evidence, required resolution}

### Non-blocking concerns
- {concern, why it matters, optional improvement}

### Scope drift or hallucination risks
- {risk and corrective action}

### Recommended next searches or checks
- {bounded action tied to the goal}
```

Coordinator response:

- Resolve blocking issues before final synthesis.
- Record accepted challenges in `CLAIMS-MATRIX.md` or `SOURCE-REGISTER.md`.
- Reject skeptic suggestions that do not improve the stated goal, and record why in `NOTES.md` if non-obvious.

### 6. Information synthesis

1. Read `RESEARCH-GOAL.md`, `SOURCE-REGISTER.md`, `CLAIMS-MATRIX.md`, and resolved skeptic notes.
2. Group claims by the user's research facets, not by source order.
3. Separate what is known, what is disputed, what is uncertain, and what remains unresearched.
4. Prefer concise synthesis over source-by-source summary. Source-by-source notes belong in the claims matrix.
5. Cite source IDs for material claims.
6. Produce `SYNTHESIS.md` using [SYNTHESIS-FORMAT.md](./SYNTHESIS-FORMAT.md).

### 7. Stop or iterate

Stop only when the stopping criteria in `RESEARCH-GOAL.md` are satisfied or when further research has diminishing returns relative to the desired level of understanding.

Iterate when:

- a required facet lacks verified sources;
- a key claim is unsupported or contradicted;
- the skeptic identifies a blocking issue;
- the synthesis cannot answer the goal without speculation;
- the user changes the goal.

## Final response to the user

Return a concise note pointing to `SYNTHESIS.md` when a file was created. Include only:

- the research goal;
- verified source count;
- whether stopping criteria were met;
- key remaining uncertainty;
- path to the synthesis.
