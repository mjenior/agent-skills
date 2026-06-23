# implement

Review the provided plan file, understand its purpose and fit within the existing codebase, then implement the plan's to-do items in order.

## Method

1. Identify the exact plan file or plan text to implement. If no plan is provided or the plan source is ambiguous, ask for the plan before making changes.
2. Read the relevant code, tests, configuration, and documentation needed to understand how the plan fits the current repository. Prefer existing patterns and direct contract changes over new abstractions or compatibility layers unless the plan explicitly requires them.
3. Work through the plan's to-do items in sequence. Keep each edit scoped to the current item, but update later items if earlier implementation evidence makes the original sequence stale or incorrect.
4. When a to-do is unclear, inspect the code first and choose the smallest implementation that satisfies the plan. Ask a focused question only when the ambiguity could materially change behavior, data contracts, or user-facing results.
5. After each meaningful change, run the most relevant available verification for the touched area, such as tests, type checks, lint checks, or focused manual checks. Fix issues you introduced before moving on.
6. Do not stop with partially completed to-dos unless blocked by missing information, failing external dependencies, or an explicit user instruction. If blocked, explain the blocker, the evidence gathered, and the smallest next action needed to continue.

## Output

When implementation is complete, report:

1. **Implemented**: the plan items completed and the main behavior changed.
2. **Verification**: the checks run and their results.
3. **Blocked Or Deferred**: any plan items not completed, with the reason and next action.