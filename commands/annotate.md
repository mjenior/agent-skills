# annotate

Update documentation and code annotation to match the current implementation. 

Do not modify executable code; you may only edit Markdown, docstrings, and code comments.

Scope
- Default to the documentation that covers the code touched in the current branch or changes. If no such scope is implied, ask before sweeping the whole repo.
- In scope: root README files, package and package-subdirectory README files, module docstrings, public function/class/method docstrings (skip private symbols prefixed with `_` unless their behavior is surprising), and inline comments around non-obvious behavior.

Method
- Ground every change in the code. Read the relevant modules, follow call sites, and check tests before asserting behavior. Do not rely on memory of how similar projects work.
- When documentation and code disagree, update the documentation. Do not change the code to match the docs. If the code itself looks wrong, leave the docs alone and flag it in your summary.
- For documentation that describes removed features, delete it. For documentation that describes unbuilt or aspirational behavior, delete it unless a tracked TODO or issue justifies keeping it, in which case mark it clearly as planned.
- If you cannot verify a statement from the code, leave it untouched and note the uncertainty in your summary rather than guessing.

What to change
- Fix inaccuracies, fill genuine gaps, and remove stale explanations.
- Do not rewrite passages that are already accurate, even if you would phrase them differently. Preserve the existing voice of each file.
- Identify constructs that lack sufficient docstrings and add or update docstrings where they clarify public behavior, parameters, return values, side effects, raised errors, or workflow responsibilities. Keep docstrings grounded in the implementation and omit boilerplate for self-evident private helpers.
- Identify complex code sections that lack useful comments and add or revise comments only to capture intent, trade-offs, invariants, or surprising mechanics. Do not add comments that restate what the code already shows.
- Ensure each README has a short opening section stating the repository or package purpose, the functionality it currently provides, and why it exists. If an equivalent section already exists under any heading, leave it; only add or edit when the information is missing or wrong.
- Ensure each package subdirectory has its own README that explains the utility, responsibilities, and workflow role of that package or subpackage. Include the key modules or entry points only when they can be verified from the code. The goal is documentation at every meaningful code-organization level so engineers new to the codebase can maintain, update, and understand the workflow without reverse-engineering the directory structure.

Style
- Concise, specific, active voice. No marketing language, no emoji, no filler transitions.
- Do not invent cross-references, file paths, or APIs. Every link, symbol, and path you write must exist.

Output
- After editing, summarize: which files changed, the substantive corrections made, anything you intentionally left alone, and any uncertainties or suspected code bugs surfaced along the way.
