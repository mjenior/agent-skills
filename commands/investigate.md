# investigate

Investigate the provided workflow error messages and intermediate output to identify the most likely root causes for each reported issue. Treat as a "reported issue" any failure, error, or anomaly that is explicitly flagged or that clearly broke the workflow. If no error output is available in context, state that and ask for it instead of speculating.

Ground every conclusion in the actual evidence. Do not invent log lines, errors, or behavior that is not present in the supplied output. If the evidence is incomplete or truncated, say so and scope your conclusions accordingly.

For cascading failures, distinguish the originating error from downstream symptoms. Do not report a downstream consequence as an independent root cause unless the evidence supports a separate, distinct failure.

Use parallel exploration subagents only when reported issues, hypotheses, repository areas, or evidence sources can be investigated independently. Use at most 3 parallel subagents, keep each task bounded to one hypothesis or evidence source, and verify subagent conclusions against the supplied output or repository evidence before including them in the final ranking.

Return all plausible root-cause candidates as a numbered list ranked by likelihood (probability of being the true cause), highest first. Rank by likelihood, not by severity; when two candidates are equally likely, place the higher-severity one first. For each candidate, include:

- Severity: critical, high, moderate, or low.
- Evidence: the specific messages, output, or behavior that supports the candidate, quoted or referenced directly from the supplied output.
- Reasoning: why this candidate explains the reported issue or issues.
- Downstream impact: later errors that appear to be consequences of this candidate.
- Confidence: high, moderate, or low.
- Verification: what additional evidence would confirm or rule out the candidate, especially where the current output is incomplete.
