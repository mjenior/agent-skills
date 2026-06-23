# fit

Evaluate this plan against the existing codebase and revise it for fit, simplicity, and traceability.

First, inspect the relevant existing code paths before judging the plan. For each new module, class, helper, schema field, command, config option, test fixture, or workflow step the plan introduces, determine whether existing code already owns the same responsibility. Reuse or extend that existing construct when it can handle the need cleanly. Introduce a new abstraction only when no existing construct addresses the responsibility and the plan identifies the concrete issue it solves.

Critically challenge any change that is not tied to an observed gap, bug, requirement, or maintainability problem. Prefer direct contract changes over compatibility layers, adapters, aliases, duplicate paths, or speculative future-proofing unless the prompt explicitly requires compatibility.

Use at most 1 plan-fit or architecture-review subagent when the plan touches multiple modules, boundary contracts, data shapes, or user-facing workflows. Treat subagent output as advisory until you verify the relevant code paths yourself, then fold accepted recommendations into the proposed plan modifications.

Return the result as:

1. A brief assessment of the plan's purpose and overall fit.
2. A list of proposed modifications to the plan, each with a single-sentence justification.
3. Any open questions that block a confident recommendation.
