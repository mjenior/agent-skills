# risks

Resolve the "Open Questions", "Risks And Assumptions", or similarly named unresolved-risk sections in the provided plan, then update that plan so it reflects the current evidence.

If no plan file or plan text is identifiable, ask which plan to evaluate before proceeding. Do not infer a plan from unrelated chat history.

## Method

1. Establish the exact plan being evaluated and enumerate the unresolved questions, risks, and assumptions currently in scope.
2. For each item, gather only the evidence needed to decide whether it is resolved, still material, or blocked on an external decision. Prefer direct evidence from code, tests, docs, configs, diagnostics, and current repo state over assumptions.
3. Classify each item as exactly one of:
   - **Resolved**: evidence answers the question or invalidates the risk.
   - **Requires mitigation**: the issue is real enough to change implementation, sequencing, tests, rollout, or observability.
   - **Accepted risk**: the risk remains but is intentionally tolerated; record the rationale and trigger for revisiting it.
   - **Needs decision**: progress depends on product, owner, operational, or external evidence that is not available in the repo.
4. For every item that requires mitigation, choose the most robust codebase-aligned approach that addresses the underlying issue rather than only the visible symptom. Include the concrete files, modules, tests, or verification commands affected when they are known.
5. Update the plan in place: remove stale questions, move actionable mitigations into the implementation or verification steps, revise sequencing when risk changes priority, and keep only unresolved decisions or evidence gaps that could materially change implementation.
6. Do not implement code changes unless explicitly asked; this command updates the plan.

## Output

After updating the plan, report:

1. **Plan Updated**: the file or plan source that was changed.
2. **Risk Resolution Summary**: each item grouped as resolved, requires mitigation, accepted risk, or needs decision.
3. **Plan Changes**: the most important implementation, verification, sequencing, or scope changes made.
4. **Remaining Decisions**: any unresolved decision or missing evidence, with the fastest way to resolve it.
