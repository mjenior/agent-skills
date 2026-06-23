---
name: audit
description: Audit a defined change set against its plan, callers, and boundary contracts. Use this skill when the user asks for a code audit, review of branch changes, PR review, or contract-drift check.
allowed-tools: Bash(git status *) Bash(git diff *) Bash(git log *) Bash(rg *) Bash(pytest *) Bash(task *)
---

Use this skill to audit code changes associated with a provided plan, diff, branch, or other explicitly named change set.

If no change set is identifiable, ask which change set to audit instead of guessing.

## Scope

1. Establish the change set explicitly. Prefer `git status` plus `git diff` against the merge base, along with files named in the originating plan.
2. For each changed symbol, enumerate:
   - its definition site,
   - its direct call sites and importers, one hop only,
   - any boundary contracts it crosses, such as public APIs, serialized payloads, persisted schemas, config keys, env vars, CLI surfaces, or protocol boundaries.
3. Trace deeper only when a contract change at the boundary forces it.

## What to look for

Classify each finding into exactly one bucket:

- Missing or insufficient
- Interface drift
- Behavioral regression

For each finding, record the file and line, the bucket, one-sentence evidence, and the proposed action.

## Action policy

- Fix directly only when the correct change is mechanical and unambiguous.
- Flag and do not fix when the resolution requires product or design judgment, when the original change itself appears defective, or when fixing would expand scope beyond the audited change set.
- Never invent behavior for a missing component unless the plan, existing code, or tests specify it.

## Verification

After applying any mechanical fixes, run the relevant tests, type checks, and lint commands for the touched files and report the results. If a command is unavailable or out of scope, say so explicitly.

## Output

Produce a todo list with one item per finding, grouped by bucket and ordered by severity. Mark each item as `fixed`, `needs-decision`, or `flagged`. End with verification results and unresolved questions.
