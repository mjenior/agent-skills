# abstract

Analyze the target file or module in focus for structural and maintainability problems. 

Do not assume problems exist or that any specific restructuring is the answer; let the evidence decide.

## Investigate

1. **Dead or unreachable code.** Identify functions, classes, branches, and imports with no reachable callers. Before classifying anything as dead, trace its call sites across the repo and confirm it is not reached through public exports, dynamic dispatch, registration/plugin patterns, tests, or external entry points. If reachability is uncertain, keep the code and note exactly what must be confirmed before removal.
2. **Redundancy and conflicts.** Find duplicated logic, overlapping responsibilities, and implementations that contradict each other or the module's stated contract.
3. **Broken code.** Find logic that cannot work as written, including incorrect control flow, unhandled error paths, and violated invariants.
4. **Cohesion and boundaries.** Assess whether the current grouping of responsibilities is clear. Consider the full range of options: leave structure as-is, rename, inline, merge units, or extract into separate files/packages. Recommend extraction only when it measurably improves cohesion or reduces coupling, not merely because a file is long.

## Report

Output an analysis only; do not apply changes unless explicitly asked. List findings ordered by implementation priority, where priority weighs correctness risk first, then maintainability impact, then effort. For each finding include:

- The concrete location (file and symbol or line range).
- The evidence (e.g., call sites traced, contract referenced, failing path).
- The recommended action and a brief rationale.
- Risk and blast radius of acting on it.

If you find no significant issues, say so plainly rather than manufacturing findings.
