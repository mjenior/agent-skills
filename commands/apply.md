# apply

Review the findings, recommendations, and unresolved items from the previous investigation, fit review, risk review, user feedback, or other scoped critique, then update the associated plan file so it reflects the best current evidence.

If no plan file or plan text is identifiable, ask which plan to update before proceeding. Do not infer a plan from unrelated chat history.

## Method

1. Establish the exact plan being updated and the findings in scope. Include findings from investigation, fit review, risk review, referenced files, diffs, diagnostics, errors, agent outputs, and user comments when they clearly apply to the same plan.
2. For each finding or recommendation, decide whether it is:
   - **Apply**: supported by evidence and actionable in the plan.
   - **Defer**: blocked on a product, owner, operational, or external decision.
   - **Reject**: contradicted by evidence, obsolete, duplicate, or outside the plan's scope.
3. When recommendations conflict, prefer the change that best satisfies the plan goal with the smallest correct scope. Use direct evidence from code, tests, logs, docs, diagnostics, and current repo state over assumptions.
4. Update the plan in place. Fold accepted recommendations into the implementation steps, verification steps, risks, assumptions, or open questions where they naturally belong. Remove stale or duplicate items instead of preserving parallel versions.
5. Do not implement code changes unless explicitly asked; this command updates the plan.

## Output

After updating the plan, report:

1. **Plan Updated**: the file or plan source that changed.
2. **Applied Changes**: the most important plan updates and which finding each one addresses.
3. **Deferred Or Rejected Findings**: any recommendation not applied, with the reason.
4. **Remaining Gaps**: only unresolved decisions or missing evidence that could materially change implementation.
