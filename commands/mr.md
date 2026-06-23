# mr

Write a high-quality merge request title and description for the current branch. Ground the message in the branch's actual commits and code changes, not in assumptions or generic release-note phrasing.

## Guardrails

- This command is read-only. Inspect repository state, commit history, and diffs, but do not create, edit, format, move, or delete files.
- Do not stage, commit, push, amend, rebase, merge, tag, open a merge request, or perform any other git operation that changes local or remote state.
- Do not generate artifacts, patch files, temporary notes, copied MR descriptions, or any other files on disk.
- Do not run commands that install dependencies, start services, mutate databases, update generated outputs, or otherwise change the working tree or environment.
- The only objective is to return the MR title and message in chat.

## Method

1. Identify the branch base and review the full commit history for the branch, including commit subjects and bodies where useful.
2. Inspect the branch diff against the base branch. If the commit history is long, diverse, rewritten, or unclear, rely on the diff and relevant files as the source of truth.
3. Separate the change into user-facing behavior, internal implementation, tests, docs, migrations, configuration, and operational impact. Include only categories that are actually present.
4. Look for the "why" behind the work: the problem solved, workflow improved, bug removed, risk reduced, or capability added. Do not merely restate filenames or commit subjects.
5. Note important validation performed if it is visible from commits, test changes, or command output. If validation is unknown, do not invent it.

## Output

Return only the MR message:

1. **Title**: one concise sentence fragment in imperative or active style. Name the concrete outcome, not the implementation mechanism.
2. **Summary**: 2-4 bullets explaining the most important changes and why they matter.
3. **Details**: include only when the branch needs more context than the summary can carry. Use short outline bullets grouped by topic.
4. **Validation**: list tests, checks, or manual verification that were actually run or clearly evidenced. If none are known, write `Not documented in branch context.`
5. **Risks / Notes**: include only real migration concerns, rollout risks, follow-up work, or reviewer context.

## Style

- Use an outline format with tight headings and bullets. Keep the structure scannable for reviewers.
- Write in active voice and past tense for completed work. Use varied sentence structure without sounding literary.
- Be specific about behavior and impact. Avoid vague verbs such as "updated", "improved", or "handled" unless the object and result are clear.
- Avoid marketing language, inflated claims, filler transitions, emoji, and common LLM tells such as "This MR", "ensures", "seamlessly", "robust", "comprehensive", or "various".
- Do not mention internal investigation steps, uncertainty about the prompt, or the fact that git commands were run.
- Do not invent tests, benchmarks, migrations, incidents, tickets, or reviewer concerns.
