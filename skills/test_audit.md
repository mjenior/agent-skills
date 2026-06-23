---
name: test-audit
description: Audit a repository's test suite for behavior coverage, reachability, isolation, and maintainability. Use this skill when the user asks whether tests are valid, redundant, weak, brittle, or worth simplifying.
allowed-tools: Bash(rg *) Bash(git status *) Bash(git diff *) Bash(pytest *) Bash(task *)
---

Use this skill to critically review tests for signal and real behavior coverage.

Do not assume problems exist. Prove each finding from the test code and the production path it exercises.

## Method

For each test file in scope:

1. Trace each test through production entry points and in-repo callers before judging it.
2. Mark a test invalid only when the scenario cannot occur through supported inputs, persisted state, external responses, or real workflows, the behavior no longer exists, or the asserted failure mode cannot be raised by the code path.
3. Identify weak-value tests such as duplicate assertions, excessive mocking that bypasses the behavior under test, assertions of implementation details, brittle snapshots, or coverage that no longer protects a meaningful contract.
4. Identify consolidation opportunities where tests cover the same contract, setup, or failure mode and can be merged without losing distinct behavior coverage.
5. Flag isolation risks such as shared mutable state, leaked environment variables, filesystem or network side effects, time or randomness issues, global monkeypatches, or missing teardown.
6. Preserve tests that cover shipped behavior, public contracts, regression history, integration boundaries, or realistic edge cases even when they are verbose.

## Output

Return findings as a numbered list grouped by test file. For each finding include:

- the test name,
- the production path or caller evidence,
- the risk,
- the recommended action: keep, delete, consolidate, rewrite, or improve isolation.

Distinguish confirmed invalid tests from lower-confidence cleanup candidates, and give the exact verification step needed for any lower-confidence candidate.

If the suite is large, audit the highest-risk files first and state which files were not examined. If no actionable issues are found, say so directly.
