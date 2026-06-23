# plan

Review all findings from the previous investigation and any referenced files, diffs, diagnostics, or errors. Create an implementation plan that fully addresses every highlighted issue, requested update, and unresolved hypothesis.

## Method

1. Restate the goal, success criteria, and explicit non-goals in 1-3 sentences. If the scope is ambiguous, ask the smallest clarifying question before planning.
2. Perform only the supplementary investigation needed to confirm facts that affect the plan. Prefer direct evidence from code, tests, logs, diagnostics, and current repo state over assumptions.
3. Enumerate the issues or requested updates the plan must cover. For each one, note the evidence that it is in scope and whether it is confirmed, likely, or still uncertain.
4. When multiple implementation options exist, select the option most likely to produce a correct, maintainable result in this codebase. Justify the selection in one sentence, including the key trade-off.
5. Produce a sequenced implementation plan with concrete files, modules, tests, and verification commands where they are known. Include migration, compatibility, documentation, or observability work only when the requested behavior or affected contracts require it.
6. Identify risks, assumptions, and open questions that could change the plan. Do not resolve material risks inside this command; defer risk resolution to `risks.md` or the risk-resolution stage. For each open question, state the fastest evidence needed to resolve it.

## Output

Return:

1. **Plan**: ordered implementation steps, each with the intended outcome and relevant files or symbols.
2. **Coverage Check**: a concise mapping from every highlighted issue or suggestion to the plan step that addresses it.
3. **Verification**: the tests, type checks, lint checks, manual checks, or diagnostic reruns needed, plus any checks that are intentionally out of scope.
4. **Open Questions**: only unresolved decisions or missing evidence that could materially change implementation.
