# cleanup

Audit this repository for behavioral defects and unreachable code that limit production quality or extensibility. This is a read-only review: do not modify any files.

## What to look for

1. Behavioral anti-patterns — defects that affect correctness, reliability, or safety, not style. Examples:
   - Exception handling that catches the wrong type, swallows errors, masks the original cause, or wraps code that cannot raise the caught type.
   - Validation gaps at external boundaries (deserialization, network input, persisted data).
   - Concurrency, ordering, or state-mutation bugs.
   - Incorrect resource lifecycle (files, sessions, sockets, locks).
   - Logic that silently diverges from documented contracts.
2. Dead, unreachable, orphaned, or unwired code — symbols, branches, modules, config keys, registered handlers, or entry points with no live caller.

## What to exclude

- Stylistic preferences already enforced by the project's linter, formatter, or type checker. Inspect the project's lint/format/type-check configuration before deciding what is in scope.
- Vendored code, generated files, migrations, fixtures, and third-party stubs.
- Speculative architecture critiques, naming preferences, or "this could be more extensible" suggestions without a concrete defect.

Exception handling defects are always in scope, even when the project's style rules touch error handling, because mishandled exceptions are behavioral.

## Verification requirements

- Before reporting code as dead, confirm there is no caller via repo-wide search and check for indirect references: dynamic imports, plugin/entry-point registries, MCP tool or resource descriptors, Pydantic model references, framework decorators, test discovery, and public API exports declared in `__init__.py`, `pyproject.toml`, or similar.
- If you cannot confidently rule out indirect use, report the finding with confidence "low" rather than omitting it or overstating it.

## Output format

Return a numbered list ranked by severity (highest first). Cap the list at the top 20 findings; collapse repeated occurrences of the same pattern into a single finding that lists each location.

For each finding, use this structure:

1. **Title** — short description of the defect.
   - **Severity**: critical | high | medium | low
     - critical: data loss, security exposure, or crashes on common paths
     - high: incorrect behavior on realistic paths, or dead code in a public interface
     - medium: incorrect behavior on edge paths, or dead code in internal modules
     - low: minor correctness risk or clearly orphaned helpers
   - **Confidence**: high | medium | low
   - **Location(s)**: `path/to/file.py:line-range` (list every occurrence)
   - **Evidence**: the specific pattern, with a short quoted snippet or reference if useful.
   - **Why it is a defect**: one or two sentences explaining the behavioral impact, not a style judgment.
   - **Recommendation**: one of `remove`, `refine`, or `rewire`, followed by 1–3 sentences describing the change and any caller-visible risk.
     - `remove`: delete the code; nothing depends on it.
     - `refine`: keep the code's role but fix the defect in place.
     - `rewire`: the code should exist but is reachable from the wrong place or not reachable at all; describe where it should be wired in.

If no findings meet the severity bar, say so explicitly rather than padding the list.
