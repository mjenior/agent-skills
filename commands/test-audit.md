# test audit

Critically audit this repository's test suite for behavior coverage, reachability, isolation, and maintainability. Optimize for a smaller, higher-signal suite, not for deleting tests by count alone. Do not assume problems exist; prove each finding from the test code and the production path it exercises, and cite the test name and file for every finding.

For each test file:

1. Trace each test through production entry points and in-repo callers before judging it. Mark a test invalid only when one of these holds: the scenario cannot occur through supported inputs, persisted state, external responses, or real workflows; the behavior it claims to test no longer exists; or the asserted failure mode cannot be raised by the code path.
2. Identify weak-value tests: duplicate assertions, excessive mocking that bypasses the behavior under test, assertions that only restate implementation details, brittle snapshots, or coverage that no longer protects a meaningful contract.
3. Identify consolidation opportunities where tests cover the same contract, setup, or failure mode and can be merged without losing distinct behavior coverage.
4. Flag isolation risks: shared mutable state, test-order dependence, leaked environment variables, filesystem or network side effects, time or randomness, global monkeypatches, or missing setup and teardown.
5. Preserve tests that cover shipped behavior, public contracts, regression history, integration boundaries, or realistic edge cases even if they look verbose.

Return findings as a numbered list grouped by test file. For each finding, include the test name, the production path or caller evidence, the risk, and the recommended action: keep, delete, consolidate, rewrite, or improve isolation. Distinguish confirmed invalid tests from lower-confidence cleanup candidates, and give the exact verification step needed for any lower-confidence candidate. Do not edit tests unless explicitly asked; this audit should produce actionable recommendations.

If the suite is large, audit the highest-risk files first and state which files you did not examine. If you find no actionable issues, say so directly and name the strongest reason the suite should remain unchanged.
