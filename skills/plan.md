---
name: plan
description: Create an implementation plan from findings, diffs, diagnostics, errors, or explicit requested changes. Use this skill when the user asks for a concrete execution plan that covers all confirmed issues and relevant constraints.
allowed-tools: Bash(rg *) Bash(git diff *) Bash(git status *) Bash(pytest *) Bash(task *)
---

Use this skill to turn confirmed findings and requested changes into a concrete implementation plan.

If scope is ambiguous, ask the smallest clarifying question before planning.

## Method

1. Restate the goal, success criteria, and explicit non-goals in one to three sentences.
2. Perform only the supplementary investigation needed to confirm facts that affect the plan.
3. Enumerate the issues or requested updates the plan must cover. For each one, note the evidence that it is in scope and whether it is confirmed, likely, or still uncertain.
4. When multiple implementation options exist, select the option most likely to produce a correct, maintainable result in this codebase. State the key trade-off in one sentence.
5. Produce a sequenced implementation plan with concrete files, modules, tests, and verification commands where they are known.
6. Include migration, compatibility, documentation, or observability work only when the requested behavior or affected contracts require it.
7. Identify risks, assumptions, and open questions that could materially change the plan. State the fastest evidence needed to resolve each one.

## Output

Return:

1. Plan: ordered implementation steps, each with the intended outcome and relevant files or symbols.
2. Coverage check: a concise mapping from every highlighted issue or suggestion to the plan step that addresses it.
3. Verification: the tests, type checks, lint checks, manual checks, or diagnostic reruns needed, plus any checks intentionally out of scope.
4. Open questions: only unresolved decisions or missing evidence that could materially change implementation.
