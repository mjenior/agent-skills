# prompts

Audit the prompts, agent instructions, system messages, workflow guidance, and model-facing text in the current repository for mismatches with the repository's actual behavior.

Work from any relevant files in the current repository. If the user names files, directories, commits, PRs, branches, or a workflow/component, use that as the audit scope. If no scope is provided, infer a reasonable scope from the current diff, recently changed files, prompt/instruction files, and nearby implementation code.

Automatically assess behavioral changes before auditing prompt text. Do not require the user to provide a behavioral-change summary. Infer current behavior from source code, tests, schemas, configuration, docs, and diffs as needed. Compare model-facing instructions against the behavior that can actually occur through supported entry points and workflows.

Ask clarifying questions only when the available repository context is insufficient to identify the target workflow, expected behavior, or prompt surface with reasonable confidence. Ask the minimum number of questions needed, then proceed with the audit and suggested changes once satisfied.

Do not edit files unless the user explicitly asks you to apply the changes. Generate suggested changes that are specific enough to apply directly.

For every finding:
- Ground it in a specific mismatch between prompt text and observed repository behavior.
- Cite the exact file and line or smallest relevant location.
- Recommend direct language changes, not broad rewrites, unless the surrounding prompt structure is the problem.
- Include replacement text or a concise patch-style description when useful.
- Avoid style preferences and speculative improvements unless they are likely to improve correctness, safety, routing, evidence handling, tool use, output quality, or model adherence.

Return findings ranked by expected impact on correctness and model adherence, highest first.

Use this format for each finding:
1. <Short title>
   Location: <file:line or smallest relevant location>
   Observed behavior: <one sentence summarizing the behavior inferred from code/tests/docs/diffs>
   Issue: <one sentence describing the mismatch>
   Recommended change: <specific replacement text or direct edit description>
   Expected benefit: <one sentence explaining why this improves correctness or adherence>

If no clear mismatches are found, say so explicitly and list any residual uncertainty from the audit scope.
