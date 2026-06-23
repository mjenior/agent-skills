---
name: annotate
description: Synchronize documentation, docstrings, and comments with the current implementation. Use this skill when the user asks to update docs, refresh annotations, or align README and code comments with actual behavior.
allowed-tools: Bash(rg *) Bash(git diff *) Bash(pytest *) Bash(task *)
---

Use this skill to update documentation and code annotations without changing executable behavior.

You may edit Markdown, docstrings, and code comments. Do not modify executable code.

## Scope

- Default to documentation covering the code touched in the current branch or change set.
- If no such scope is implied, ask before sweeping the whole repository.
- In scope: root and package README files, module docstrings, public function, class, and method docstrings, and inline comments around non-obvious behavior.

## Method

1. Ground every documentation change in the code. Read the relevant modules, follow call sites, and check tests before asserting behavior.
2. When documentation and code disagree, update the documentation. If the code itself appears wrong, leave the docs alone and flag the suspected bug.
3. Delete documentation for removed or unbuilt behavior unless a tracked TODO or issue justifies keeping it, and mark any retained future work clearly as planned.
4. If you cannot verify a statement from the code, leave it untouched and note the uncertainty instead of guessing.
5. Fix inaccuracies, fill genuine gaps, and remove stale explanations without rewriting passages that are already accurate.
6. Add docstrings or comments only where they clarify public behavior, parameters, return values, side effects, raised errors, intent, invariants, or surprising mechanics.
7. Do not add comments that merely restate the code.

## Output

After editing, summarize:

- which files changed,
- the substantive corrections made,
- anything intentionally left alone,
- any uncertainties or suspected code bugs surfaced during the work.
