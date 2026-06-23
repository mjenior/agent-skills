---
name: prompts
description: Audit prompts, system messages, agent instructions, and other model-facing text for mismatches with actual repository behavior. Use this skill when the user asks to review prompt quality, instruction drift, workflow guidance, or agent adherence risks.
allowed-tools: Bash(rg *) Bash(git diff *) Bash(git status *) Bash(pytest *) Bash(task *)
---

Use this skill to audit model-facing text against real repository behavior.

If the user names files, directories, commits, branches, PRs, or a workflow, use that as the audit scope. Otherwise infer a reasonable scope from the current diff, recently changed files, nearby prompt files, and the relevant implementation code.

## Method

1. Assess behavioral changes before auditing prompt text. Infer current behavior from source code, tests, schemas, configuration, docs, and diffs as needed.
2. Compare model-facing instructions against behavior that can actually occur through supported entry points and workflows.
3. Ask clarifying questions only when the available repository context is insufficient to identify the target workflow, expected behavior, or prompt surface with reasonable confidence.
4. Do not edit files unless the user explicitly asks to apply the changes.
5. Ground every finding in a specific mismatch between prompt text and observed repository behavior.
6. Recommend direct language changes, not broad rewrites, unless the surrounding prompt structure is itself the problem.
7. Avoid style-only suggestions unless they are likely to improve correctness, safety, routing, evidence handling, tool use, output quality, or model adherence.

## Output

Return findings ranked by expected impact on correctness and model adherence, highest first.

For each finding include:

1. Short title
2. Location
3. Observed behavior
4. Issue
5. Recommended change
6. Expected benefit

If no clear mismatches are found, say so explicitly and list any residual uncertainty from the audit scope.
