# simplification-audit-to-pr workflow

Run this workflow from the repository root when the goal is to convert a broad simplification audit into a reviewed, implemented, tested, committed, and PR-ready change.

Default posture: automate the full loop, but keep each phase evidence-gated. Do not modify files during audit or planning. Do not commit, push, or open a PR unless the user explicitly requested the full repository workflow or confirms the final plan includes those actions.

This file is the complete workflow definition. Do not delegate decisions outside this file. Subagents may be used only as bounded workers following these instructions, and their output remains advisory until the coordinator verifies it against repository evidence.

## objectives

- Find confirmed opportunities to simplify, deduplicate, correct, or delete code without losing core functionality.
- Convert only evidence-backed findings into a minimal implementation plan that fits the current codebase.
- Review and refine the plan against existing ownership, contracts, call sites, tests, conventions, and user preferences before editing.
- Implement the accepted plan with minimal diffs, direct contract updates, and no unnecessary compatibility layers.
- Confirm behavior with targeted tests, type checks, lint checks, and a final change-set audit.
- Commit only triage-approved paths and open a PR or MR with an accurate description.

## definitions

Core functionality is behavior reachable from real entry points, including public APIs, CLI commands, HTTP or RPC handlers, scheduled jobs, event listeners, exported library surfaces, framework or plugin discovery, serialization hooks, persisted schemas or data formats, and tests that protect real workflows. Preserve all core functionality unless the user explicitly approves a breaking change.

Finding categories:

- `duplication`: exact or near-duplicate functions, classes, modules, blocks, literals, rule tables, utilities, or parallel implementations of the same concept.
- `conflict`: code paths that implement the same rule differently or contradict documented contracts, schemas, persisted data, caller expectations, or tests.
- `bug`: incorrect conditions, swallowed errors, unreachable branches, broken invariants, race conditions, leaks, or other behavior defects proven by tracing.
- `dead-code`: functions, classes, files, exports, dependencies, feature-flag branches, or tests proven unreachable after checking entry points and dynamic-use patterns.
- `useless-active`: reachable code that runs but adds latency, cost, risk, redundant recomputation, ineffective retries or caching, or no-op transformation without contributing to an outcome.
- `simplification`: structures, layers, indirection, or abstractions that can be collapsed without changing behavior.

Confidence levels:

- `confirmed`: traced from an entry point, exported surface, framework convention, realistic test path, or complete caller set with enough evidence to act.
- `likely`: strong static evidence exists, but one reachability or contract detail still needs confirmation before implementation.
- `uncertain`: reachability, intent, or external use is unclear. Do not implement. Record the exact verification step needed.

## non-goals

- Do not preserve obsolete APIs with shims, aliases, adapters, facades, wrappers, bridges, or dual paths when all in-repo callers can be updated together.
- Do not introduce abstractions for hypothetical future use.
- Do not delete convention-reachable, dynamically-discovered, serialized, migration, fixture, generated-boundary, or exported code without proof.
- Do not expand implementation beyond confirmed findings and explicitly accepted plan items.
- Do not perform unrelated cleanup, formatting-only churn, dependency upgrades, file moves, or style corrections unless required by the accepted plan or repository tooling.

## model and thinking tiers

Use the smallest model tier that can reliably complete the assigned phase. Escalate when evidence shows the current tier is missing cross-file relationships, boundary contracts, dynamic reachability, or failure causes.

| tier | use when | model | thinking | max task size |
| --- | --- | --- | --- | --- |
| quick | Single-file or narrow local inspection, no public contracts, no data shape changes, no ambiguous reachability. | GPT-5.5 or Claude Haiku-4.5 | low | 1-3 files or less than 30 minutes expected work |
| standard | Multiple related files, shared helpers, tests, or caller updates with clear ownership. | GPT-5.5 or Claude Sonnet-4.6 | medium | 4-12 files or 30-120 minutes expected work |
| complex | Boundary contracts, public APIs, schemas, migrations, security, concurrency, generated code, framework discovery, or unclear reachability. | GPT-5.5 or Claude Opus-4.8 | high | More than 12 files, more than 2 hours expected work, or any high-risk boundary |
| verifier | Independent review, plan critique, audit confirmation, or failure diagnosis. | GPT-5.5 or Claude Opus-4.8 for complex-tier review | medium by default, high for complex tier changes | Bounded to the reviewed artifact or diff |

Subagent defaults:

- Audit scouts: GPT-5.5 or Claude Sonnet-4.6 with medium thinking. Use GPT-5.5 or Claude Opus-4.8 with high thinking for large module maps, dynamic dispatch, framework conventions, public APIs, schemas, persistence, or security-sensitive areas.
- Plan writer: GPT-5.5 or Claude Opus-4.8 with high thinking for complex findings; GPT-5.5 or Claude Sonnet-4.6 with medium thinking otherwise.
- Plan reviewer: GPT-5.5 or Claude Sonnet-4.6 with medium thinking. Use at most one reviewer unless the repository is so large that independent domain review is needed.
- Implementation workers: GPT-5.5 or Claude Sonnet-4.6 with medium thinking for isolated work packages. Use GPT-5.5 or Claude Opus-4.8 with high thinking only for boundary contracts or cross-cutting refactors.
- Test and confirmation reviewers: GPT-5.5 or Claude Sonnet-4.6 with medium thinking. Escalate to GPT-5.5 or Claude Opus-4.8 with high thinking when failures contradict the plan or expose hidden contracts.
- Commit and PR helper: GPT-5.5 or Claude Haiku-4.5 with low thinking when the diff and validation are already complete.

Parallelization defaults:

- Do not use subagents for small, single-file, or clearly sequential work where coordination would cost more than direct execution.
- Parallelize read-only audit work by independent entry-point domain, package, top-level module, or ownership boundary. Start with 2-4 audit scouts for medium or large repositories; use up to 6 only when the repository has enough independent domains and the coordinator can synthesize all outputs.
- Keep planning and plan review mostly serial. Use one plan reviewer by default; use two only when separate high-risk domains need independent review.
- Parallelize implementation only when work packages have non-overlapping files or each worker runs in an isolated git worktree. Use at most 2 concurrent implementation workers in one repository checkout; use up to 4 only with isolated worktrees and independent verification commands.
- Run test, confirmation, and final-audit reviewers serially by default. Use at most 2 concurrent reviewers when they inspect disjoint changed areas or independent failing checks.
- Never parallelize commit, push, PR, staging, or branch-management steps.
- Prefer fewer subagents when contracts, schemas, generated artifacts, or shared utilities are involved, because those changes need one coordinator-owned integration path.

## phase gates

A phase may advance only when its gate is satisfied.

| phase | gate |
| --- | --- |
| audit | Findings cite files, symbols, line ranges, reachability evidence, confidence, impact, risk, action, callers, and verification steps. |
| planning | Every accepted finding maps to a plan step, verification command, and owner file or symbol. |
| plan review | The plan has been checked against existing code ownership, contracts, conventions, and user preferences. Speculative abstractions and compatibility layers have been removed. |
| implementation | The worktree diff matches the reviewed plan or explicitly records why the plan changed. |
| confirmation | Targeted verification passes or every failure is explained with a bounded next action. |
| final audit | Changed definitions, one-hop callers, boundary contracts, tests, and docs are consistent. |
| commit and PR | Only approved paths are staged, validation is recorded, commit messages match the diff, push succeeds, and the PR or MR URL is captured. |

## workflow loop

### 0. preflight context

1. Capture `git status --short` before any edits.
2. Identify repository type, package manager, build system, test commands, lint commands, type-check commands, formatter commands, and PR hosting CLI.
3. Identify real source, test, generated, fixture, docs, config, schema, migration, and local-artifact directories.
4. If the user did not explicitly authorize committing and PR creation, run through final confirmation and stop before staging.
5. If the repository is not clean, classify existing modifications before editing. Do not overwrite unrelated user work.

### 1. complete audit

Perform a read-only simplification audit. For larger repositories, split the audit with subagents by entry-point domain or top-level module, then synthesize one findings register.

System map requirements:

1. Identify dependency and build configuration.
2. Identify real entry points and exported surfaces.
3. Build a high-level module map showing each top-level submodule's responsibility and how data or control moves between them.
4. List convention-based reachability rules before judging code as unused. Include plugin registries, dynamic dispatch, reflection, framework discovery, serialization hooks, migrations, fixtures, generated-code boundaries, and naming conventions.

Audit checks:

1. Detect duplication:
   - exact or near-duplicate functions, classes, modules, and copy-pasted blocks;
   - parallel implementations of the same concept that have drifted;
   - repeated literals, constants, config, or rule tables that should share one source of truth;
   - bespoke implementations that duplicate existing project utilities, standard library behavior, or installed dependencies.
2. Detect conflicting or incorrect logic:
   - submodules implementing the same rule differently;
   - logic contradicting documented contracts, schemas, persisted data, or caller expectations;
   - incorrect conditions, swallowed errors, unreachable branches, broken invariants, race conditions, resource leaks, and other defects found while tracing.
3. Detect dead or low-value code:
   - unreferenced functions, classes, files, exports, dependencies, and feature-flag branches after checking entry points and dynamic-use patterns;
   - abstractions with a single implementation or caller that add indirection without protecting a real extension point;
   - tests that assert trivial, unreachable, or non-production behavior instead of real workflows.
4. Detect useless-active code:
   - paths that run but add latency, cost, risk, redundant recomputation, ineffective retries or caching, or no-op transformations without contributing to an outcome.
5. Identify simplifications:
   - structures that can collapse without behavior change;
   - layers or indirection that can be flattened;
   - code that can be replaced by existing project utilities or standard library behavior.

Audit evidence rules:

1. Ground every claim in files, symbols, line ranges, and traced paths or complete static caller evidence.
2. Treat convention-based code as live unless proven otherwise.
3. Separate zero-risk simplifications from breaking changes.
4. Do not call code unused, duplicate, dead, or low-value from naming or intuition alone.
5. Recommend implementation only for confirmed findings unless a likely finding has a cheap verification step completed before planning.
6. For duplication, name the designated source of truth and all callers that must move to it.
7. For dead code, prove non-reachability after checking dynamic and convention-based use.
8. For useless-active code, prove the path runs and adds cost, risk, or no-op work without affecting the outcome.

Audit output format:

1. Executive summary:
   - overall assessment of reducible surface area;
   - biggest themes;
   - top 5-10 recommended actions ranked by impact, confidence, and effort;
   - explicit not-assessed scope.
2. System map:
   - entry points and exported surfaces;
   - dependency and build configuration;
   - top-level module responsibilities;
   - convention-based live-code patterns that affected the audit.
3. Findings register grouped by submodule and category. Each finding must include:
   - `id`;
   - `category`;
   - `confidence`;
   - `locations`;
   - `evidence`;
   - `impact and risk-if-changed`;
   - `recommended action`;
   - `callers to update together`, when applicable;
   - `verification step`, required for likely and uncertain findings.

Audit subagent pattern:

- Coordinator assigns bounded modules with explicit entry points and known conventions.
- Use 2-4 scouts for most parallel audits. Use 1 scout for a narrow audit and up to 6 only when domains are independent enough to keep findings bounded.
- Each scout gets a non-overlapping scope and explicit unassessed boundaries. Do not assign multiple scouts to the same files unless independent review is intentionally requested.
- Each audit scout returns only evidence-backed findings plus unassessed scope using the audit output format.
- Coordinator deduplicates findings, rejects unsupported claims, ranks by impact, confidence, and effort, and produces the final audit report.

If the audit finds no confirmed high-value work, stop and report the audit. Do not manufacture an implementation task.

### 2. plan

Create a plan only from confirmed findings and any likely findings whose verification step has been completed.

Plan requirements:

1. State goal, success criteria, and explicit non-goals in 1-3 sentences.
2. Enumerate every accepted finding, its evidence, and whether it is confirmed or resolved from likely to confirmed.
3. Select the smallest maintainable implementation option. If multiple options exist, choose one and justify it in one sentence.
4. Map every accepted finding to a concrete implementation step with intended outcome, files, symbols, contracts, tests, and verification commands when known.
5. Prefer deletion, direct consolidation, and direct contract updates over compatibility paths.
6. Include migration, documentation, generated artifact, or observability work only when required by behavior, contracts, or operator diagnosis.
7. Split unrelated findings into separate implementation groups or defer them.
8. Record open questions only when missing evidence could materially change implementation. State the fastest evidence needed to resolve each one.

Plan output format:

1. `Plan`: ordered implementation steps.
2. `Coverage check`: mapping from each accepted finding to the plan step that addresses it.
3. `Verification`: commands and manual checks required, with intentionally out-of-scope checks noted.
4. `Open questions`: only unresolved decisions or missing evidence that could materially change implementation.

### 3. plan review and refinement

Review the plan against the current codebase before editing. Use one reviewer subagent when the plan touches multiple modules, boundary contracts, data shapes, security-sensitive code, or user-facing workflows.

Review criteria:

1. Inspect relevant existing code paths before judging fit.
2. For every new module, class, helper, schema field, command, config option, fixture, or workflow step, identify whether existing code already owns the same responsibility.
3. Reuse or extend existing constructs when they can handle the need cleanly.
4. Challenge any change not tied to an observed gap, bug, requirement, or maintainability problem.
5. Prefer direct contract changes over compatibility layers, adapters, aliases, duplicate paths, or speculative future-proofing unless the accepted plan explicitly requires compatibility.
6. Confirm all affected callers, contracts, schemas, tests, docs, generated artifacts, and operational diagnostics are represented in the plan.
7. Confirm the plan follows user preferences for minimal diffs, contract-first changes, deletion over deprecation, deterministic verification, and realistic tests.

Plan review output format:

1. Brief assessment of the plan's purpose and overall fit.
2. Proposed modifications, each with a one-sentence justification.
3. Open questions that block confident implementation.

Refinement loop:

1. If the reviewer finds a blocking codebase-fit issue, revise the plan and rerun a focused review of the changed plan section.
2. If open questions remain, resolve them with the fastest direct evidence from code, tests, logs, docs, or maintainers.
3. Advance only when each plan step has evidence, a minimal implementation path, and a verification method.

### 4. implementation

Implement the reviewed plan in order.

Implementation rules:

1. Edit the smallest surface that satisfies the plan.
2. Update contracts, type definitions, schemas, docs, tests, generated artifacts, and all in-repo callers in the same change when a contract changes.
3. Delete obsolete internal code when reachability has been proven false.
4. Do not add compatibility layers or transitional APIs unless the accepted plan explicitly requires them.
5. Follow existing naming, formatting, imports, error handling, logging, and test conventions.
6. Keep commits unmade until verification and final audit complete.
7. If implementation evidence invalidates a plan step, pause that step, update the plan, and rerun focused plan review before continuing.

Subagent use during implementation:

- Use implementation subagents only for independent work packages with non-overlapping files or isolated git worktrees.
- Run sequentially when packages touch shared contracts, schemas, generated artifacts, public APIs, or the same caller graph.
- Limit implementation to 1-2 concurrent workers in the same checkout. Use up to 4 only with isolated worktrees, disjoint file ownership, and separate verification commands.
- Give each subagent the accepted plan section, relevant audit evidence, expected files, non-goals, and verification command.
- Require each subagent to report changed files, commands run, failures, and residual risks.
- The coordinator reviews each diff before integrating it. Subagent output is not accepted without local verification.

### 5. confirmation and testing

Run the narrowest deterministic checks that cover the changed behavior, then expand if shared contracts or broad modules changed.

Confirmation order:

1. Inspect `git diff` and confirm it matches the accepted plan.
2. Run targeted tests for changed behavior.
3. Run type checks for touched typed code.
4. Run lint and formatting checks required by the repository.
5. Run broader test suites when shared contracts, public APIs, schemas, framework wiring, or cross-cutting behavior changed.
6. If a check fails, fix only in-scope failures. If the failure reveals a plan defect, return to plan refinement.

Final change-set audit:

1. Establish scope with `git status` and `git diff` against the relevant base.
2. For each changed symbol, inspect definition site, direct callers/importers, and boundary contracts.
3. Classify findings as `missing/insufficient`, `interface drift`, or `behavioral regression`.
4. Fix mechanical and unambiguous issues directly.
5. Flag product or design decisions without inventing behavior.
6. Rerun relevant verification after fixes.

Completion criteria before staging:

- All accepted plan items are implemented or explicitly deferred.
- Every changed contract has updated callers and tests where applicable.
- Verification commands have passed, or remaining failures are unrelated and documented with evidence.
- No unrelated files, local artifacts, secrets, cache output, or protected baseline work are included.

### 6. commit and PR

Run commit and PR steps only after final confirmation and only when authorized.

Branch and staging rules:

1. Re-check `git status --short`, `git diff`, and `git log -5 --oneline`.
2. If on `main` or `master`, create a feature branch before staging. Use the repository's branch naming style when obvious; otherwise use lowercase kebab-case with a meaningful prefix such as `fix/`, `docs/`, `test/`, `chore/`, or `feature/`.
3. Stage only approved paths with explicit `git add <paths>`. Do not use `git add .` or `git commit -a`.
4. Do not stage secrets, `.env` files except templates, local artifacts, caches, debug output, unrelated baseline work, or ambiguous untracked files.
5. Split commits when the diff contains separable dependency, mechanical, and feature groups.
6. Commit with concise messages that describe the actual diff.
7. Confirm `git status --short` after each commit.

Push rules:

1. Re-check the current branch before pushing. Never push directly to `main` or `master` unless explicitly requested.
2. Push the current branch to its upstream, or use `git push -u origin HEAD` if no upstream exists.
3. If push is rejected due to standard feature-branch non-fast-forward divergence, run `git fetch origin --prune`, then `git pull --rebase --autostash` only when the current branch is not `main` or `master` and the worktree is clean aside from intended branch work.
4. If rebase or push hits conflicts, hook failures, protected-branch constraints, authentication failures, or would require force-push, stop and report the exact blocker. Do not force-push without explicit confirmation.

PR or MR rules:

1. Inspect branch commits and diff before writing the PR or MR description.
2. Use the repository hosting CLI when available: `gh pr create` for GitHub, `glab mr create` for GitLab.
3. Write a title and description in active voice, past tense, based only on actual commits and diff.
4. If hosting CLI is unavailable or unauthenticated, stop and report the blocker plus the prepared title and description.

Final report:

- Audit scope and top accepted findings.
- Plan review outcome and any refined decisions.
- Implemented changes and deferred items.
- Verification commands and results.
- Final audit result.
- Branch, commit SHA, push result, PR or MR URL.
- Excluded modified or untracked paths, if any.

## stop conditions

Stop and ask one focused question when proceeding could cause data loss, security issues, public API breakage not covered by the plan, contract violations, destructive git operations, or significant rework.

Stop and report instead of guessing when:

- The audit cannot prove a high-impact finding.
- A likely or uncertain finding would require speculative implementation.
- A fix requires product judgment.
- Verification is unavailable and no meaningful substitute exists.
- Required push or PR tooling is unavailable or unauthenticated.
- The worktree contains unrelated user changes that cannot be safely separated.
