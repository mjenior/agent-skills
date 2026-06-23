---
name: investigate
description: Diagnose workflow failures from actual logs, error messages, and intermediate output. Use this skill when the user asks to investigate CI failures, workflow errors, anomalous runs, or root causes from supplied output.
allowed-tools: Bash(rg *) Bash(git status *) Bash(git diff *) Bash(pytest *) Bash(task *)
---

Use this skill to investigate reported workflow failures without guessing.

If no error output or intermediate diagnostics are available, say so and ask for them instead of speculating.

## Method

1. Treat as a reported issue any failure, error, or anomaly that is explicitly flagged or that clearly broke the workflow.
2. Ground every conclusion in the supplied output and any directly relevant repository evidence.
3. If the evidence is incomplete or truncated, say so and scope the conclusions accordingly.
4. For cascading failures, distinguish the originating error from downstream symptoms.
5. Do not report a downstream consequence as an independent root cause unless the evidence supports a separate failure.
6. Use parallel investigation only when hypotheses or evidence sources can be checked independently, then verify those results before reporting them.

## Output

Return all plausible root-cause candidates as a numbered list ranked by likelihood, highest first. Rank by probability of being the true cause, not by severity. When two candidates are equally likely, place the higher-severity one first.

For each candidate include:

- Severity: critical, high, moderate, or low
- Evidence: the specific messages, output, or behavior that supports the candidate
- Reasoning: why the candidate explains the reported issue or issues
- Downstream impact: later errors that appear to be consequences of this candidate
- Confidence: high, moderate, or low
- Verification: the fastest additional evidence that would confirm or rule out the candidate
