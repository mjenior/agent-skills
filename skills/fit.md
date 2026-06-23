---
name: fit
description: Review an implementation plan for fit with the existing codebase, simplicity, and traceability. Use this skill when the user asks whether a plan matches current code, introduces unnecessary abstractions, or needs revision before implementation.
allowed-tools: Bash(rg *) Bash(git diff *) Bash(git status *) Bash(pytest *) Bash(task *)
---

Use this skill to evaluate a plan against the current repository before implementation starts.

Inspect the relevant code paths before judging the plan.

## Method

1. Determine the concrete goal of the plan and the code paths it affects.
2. For each new module, class, helper, schema field, command, config option, test fixture, or workflow step the plan introduces, check whether existing code already owns that responsibility.
3. Reuse or extend existing constructs when they can handle the need cleanly.
4. Introduce a new abstraction only when no existing construct addresses the responsibility and the plan identifies the concrete issue it solves.
5. Critically challenge any change that is not tied to an observed gap, bug, requirement, or maintainability problem.
6. Prefer direct contract changes over compatibility layers, adapters, aliases, duplicate paths, or speculative future-proofing unless compatibility is explicitly required.
7. Treat any advisory review from another agent as provisional until you verify the relevant code paths yourself.

## Output

Return:

1. A brief assessment of the plan's purpose and overall fit.
2. A list of proposed plan modifications, each with a one-sentence justification.
3. Any open questions that block a confident recommendation.
