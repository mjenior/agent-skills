# audit

Audit all code changes associated with the provided plan or chat history. 

If no such plan is identifiable, ask which change set to audit (e.g., `git diff`, a specific PR, or a named feature) before proceeding — do not guess.

## Scope

1. Establish the change set explicitly. Prefer `git status` + `git diff` against the merge base, plus the files named in the originating plan. State the scope in one sentence before auditing.
2. For each changed symbol (function, class, type, constant, config key, route, schema field), enumerate:
   - its **definition site** (is it complete and consistent with how it is used?),
   - its **direct call sites and importers** (one hop), and
   - any **boundary contracts** it crosses (public API, serialized payload, persisted schema, env/config, CLI/MCP surface).
3. Stop at one hop unless a contract change at the boundary forces deeper tracing. Note any deeper traces and why.

## What to look for

Classify each finding into exactly one bucket:

- **Missing/insufficient**: a symbol, branch, error path, or migration is referenced but not defined, or is defined but does not satisfy its documented/observed contract.
- **Interface drift**: a signature, return shape, exception type, schema, or config key changed but at least one caller/consumer/test/doc was not updated.
- **Behavioral regression**: a code path that previously produced behavior X now produces behavior Y, where Y is not explicitly intended by the plan. Include test failures, type/lint errors, and removed validations.

For each finding, record: file:line, bucket, one-sentence evidence, and proposed action.

## Action policy

- **Fix directly** only when the correct change is mechanical and unambiguous (e.g., update a caller to a renamed argument, restore a removed import, re-add a deleted test that still passes). Preserve existing behavior when intent is unclear.
- **Flag and do not fix** when the resolution requires a product or design decision, when the original change itself appears to be the defect, or when fixing would expand scope beyond the audited change set. Note the open question.
- Never invent an implementation for a missing component whose intended behavior is not specified by the plan, existing code, or tests.

## Verification

After applying fixes, run the project's test, type, and lint commands relevant to the touched files and report results. If any command is unavailable or out of scope, say so explicitly rather than skipping silently.

Use at most 1 review-focused subagent when the implementation changed boundary contracts, shared behavior, data shape, security-sensitive code, or workflow publication logic. Verify any subagent finding against the diff, relevant callers, diagnostics, or command results before fixing or reporting it.

## Output

Produce a todo list with one item per finding, grouped by bucket and ordered by severity (broken contracts > regressions > missing pieces > cleanup). Mark each item as `fixed`, `needs-decision`, or `flagged`. End with the verification results and any unresolved questions.
