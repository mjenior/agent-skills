# yeet

Run from the **git repository root**. Requires `task` (go-task) and `uv` on `PATH`. If either tool is not found, stop and report the missing tool. Do not attempt to install it. Request network access when running `task preflight`, `task preflight:no-lock`, `git fetch`, `git pull --rebase`, `git push`, `glab mr create`, or `gh pr create`.

Default posture: complete the full flow with minimal user interruption. If the repository is already on a non-`main`/`master` feature branch, use that branch. If the repository is still on `main` or `master`, do not push that branch; create an appropriately named feature branch before pushing and continue the normal push and merge request flow there. Prefer automatically resolving ordinary git, upstream, formatting, lint, type, test, hook, push, and merge request issues when the fix is local, bounded, and does not require destructive history edits or product judgment.

## Mandatory flow

Follow this order. Do not skip ahead after preflight passes:

1. Capture **baseline status**, confirm branch safety, and repair ordinary git metadata issues.
2. Run the selected preflight task and finish any bounded fix loop.
3. Capture **post-preflight status** and diff.
4. Determine eligible paths for "this change".
5. Classify every modified path and apply the triage gate.
6. Gather final inspection output.
7. Create a feature branch if still on `main`/`master`, then stage only triage-approved paths with explicit `git add <paths>`.
8. Commit, confirm **post-commit status**, then push.
9. Open a new merge request.
10. Report preflight, triage, branch/upstream, commits, push result, merge request URL, and exclusions.

## 1. Environment

If `.venv` is missing or `uv run` fails with environment errors, run `task install` once. If `task install` itself fails, stop and report the error. Do not proceed to preflight.

## 2. Preflight loop

Before running any mutating validation:

1. Run `git status --short` and save this as the **baseline status** / **baseline dirty state**.
2. Confirm the current branch. If it is `main` or `master`, record that a feature branch must be created before staging, committing, or pushing. Continue the flow, but never push directly to `main` or `master` unless the user explicitly requested it.
3. Treat the current non-`main`/`master` branch as the primary anchor for "this change". If the current branch is `main` or `master`, use the baseline dirty state and this session's edits as the anchor until the new feature branch exists. Baseline modified tracked files on the anchored branch are presumed in-scope branch work unless they are clearly local artifacts, secrets, or the user explicitly said to exclude them.
4. Repair routine git metadata problems automatically before deciding the branch is unsafe:
   - If upstream lookup fails, run `git fetch origin --prune`, then re-check the current branch and upstream.
   - If the remote default branch or merge-base cannot be resolved from local refs, run `git fetch origin --prune` before falling back.
   - If the branch still has no upstream after fetch, continue the workflow and plan to create the upstream during push with `git push -u origin HEAD`.
5. Only treat baseline paths as protected when there is concrete evidence they are unrelated to the current feature branch, such as local debug artifacts, secrets, or user-specified exclusions.
6. Do **not** interrupt the flow for ordinary feature-branch conditions such as missing local `origin/main` refs, absent upstream tracking, baseline dirty tracked files on the current branch, preflight auto-fixes, or standard non-fast-forward push rejections.

Run `task preflight:no-lock` by default and **wait for it to finish** (do not background it).

Use `task preflight` only when the user explicitly requested a dependency refresh, lock drift requires it, or you have a concrete reason to upgrade dependencies. `task preflight` is **lock-mutating**: it runs `uv lock --upgrade`, then `uv sync` from the updated lockfile, then Ruff format, Ruff lint auto-fixes, mypy, and pytest. Both preflight variants are **file-mutating** (format and lint-fix write changes to disk); the difference is solely whether the lockfile is upgraded.

When `uv.lock` changes:

1. List the changed package names before committing.
2. Stop and ask before committing unrelated major-version bumps, unexpected `pyproject.toml` changes, or dependency churn that is not needed for the anchored task.
3. Include lock changes only when they belong to this push.

If preflight fails:

1. Identify which step failed from the task output. go-task prints the subtask name in brackets (e.g. `task: [typecheck] ...` or `task: Failed to run task "typecheck"`). Match against: `install`, `lock`, `format`, `lint-fix`, `typecheck`, `test`.
2. Apply the smallest fix for **that** layer:
   - **install** — environment sync failure; if `--frozen` fails and the issue looks like stale lock metadata, automatically try `task preflight` once instead of `task preflight:no-lock`. Ask the user only if the lock update introduces unrelated dependency churn or still fails.
   - **lock** — dependency/version resolution; do not guess with unrelated code edits.
   - **format** / **lint-fix** — accept or apply the reported auto-fixes.
   - **typecheck** — fix types; avoid broad `Any` silencing.
   - **test** — fix behavior or tests; if schema snapshot tests fail, run `task export-schemas` and include updates under `protocol_scheduler_diagnostics/schemas/`.
3. Rerun the same preflight task you started with.

Do **not** stop just because a failing check touches an in-scope feature-branch file that was already dirty at baseline; treat that file as eligible branch work and fix it if the change is consistent with the current branch behavior.

**Stop and report** instead of guessing only when a fix would:

- Edit files outside the anchored change set (see §3), protected baseline paths, preflight auto-fixes on those files, or files required to make those pass.
- Change public API signatures.
- Require product judgment.
- Require destructive git operations or a conflicted history rewrite.

For read-only validation without lock upgrades or auto-fixes, use `task check` and `task test` instead of preflight.

## 3. Review, anchor, and triage

After preflight passes:

1. Run `git status --short` and `git diff` as the **post-preflight status** and diff. Distinguish **session/feature** edits from **tool** edits (format, lint-fix, lock), and compare against the baseline dirty state captured before preflight.
2. Identify untracked files created during validation. Exclude local debug output such as `artifacts/`, `htmlcov/`, `.scheduler_diagnostics/`, `tmp*/`, or cache directories unless the user explicitly requested them.
3. Default to finishing the current branch's tracked work. Use the triage gate to exclude things, not to disqualify the branch's own modified tracked files.

### Determine eligible paths

Anchor "this change" before staging anything. Files are eligible to commit in this priority order:

- Paths you edited in **this conversation** for the user’s stated task.
- Else tracked paths already modified on the current non-`main`/`master` feature branch at baseline, plus tracked paths mutated by preflight or the bounded fix loop to make that same branch pass.
- Else paths changed on the branch since merge-base with the default branch. Resolve the default branch and merge-base in this order:
  ```bash
  git symbolic-ref --short refs/remotes/origin/HEAD 2>/dev/null
  ```
  If that fails, inspect `git remote show origin` for the HEAD branch. If refs are missing or stale, run `git fetch origin --prune` and retry. Then resolve merge-base against the discovered default branch, falling back through `origin/main`, `origin/master`, `main`, and `master`.
  If merge-base still cannot be resolved after fetch, do **not** stop merely because the repository lacks local default-branch refs. Continue with the current feature branch as the anchor and use the baseline/post-preflight tracked diff to define eligible paths, excluding only secrets, local artifacts, and explicit user exclusions.
  When merge-base resolves, list changed paths with:
  ```bash
  git diff --name-only <merge-base>
  ```

### Split commits

When multiple groups are large, prefer **separate commits** (oldest dependency first):

1. `uv.lock` only — message notes dependency refresh from preflight.
2. Mechanical-only paths — e.g. `Apply ruff format and lint fixes`.
3. Feature paths — the actual task message.

## 4. Commit

For inspection only, gather these outputs before staging:

- `git status --short`
- `git diff` (staged and unstaged)
- `git log -5 --oneline` (match recent commit message style)

Before staging:

- Do **not** commit secrets (`.env`, `.env.*` except `.env.template`), local-only artifacts, or **protected** / **unknown** / unrelated user changes.
- Do **not** stage local debug output such as `artifacts/`, `htmlcov/`, `.scheduler_diagnostics/`, `tmp*/`, or cache directories.
- Include **lock** changes only when `task preflight` ran `task: lock` and they belong to this push.
- For ambiguous untracked files, prefer excluding them silently unless they look like intentional source, test, docs, schema, or config changes needed for this branch. Only ask when excluding them would likely omit real work.
- Prefer committing the current feature branch's tracked local changes when they pass the triage gate; do not exclude them solely because they existed in the baseline dirty state.

Stage only paths approved by triage (§3):

```bash
git add <paths>
```

Do **not** use `git add .` or `git commit -a`.

Confirm the current branch and upstream. If the current branch is `main` or `master`, create and switch to a new branch before staging or committing:

1. Derive a concise branch name from the intended commit or merge request title.
2. Use the repository's existing branch naming style when it is obvious from recent local or remote branch names. Otherwise use lowercase kebab-case with a meaningful prefix such as `fix/`, `docs/`, `test/`, `chore/`, or `feature/`.
3. Run `git switch -c <branch-name>`.
4. Re-check `git status` and the current branch before staging.

Do **not** push directly to `main`/`master` unless explicitly requested.

Commit with a concise message that reflects the actual diff for that commit. Use a HEREDOC:

```bash
git commit -m "$(cat <<'EOF'
Your message here.

EOF
)"
```

Repeat per split-commit group from §3.

If `git commit` is rejected before creating a commit, fix the issue, rerun the relevant validation, restage only approved paths, and run `git commit` again. Do **not** amend. If a commit succeeds but a hook auto-modified files afterward, follow the active amend safety rules before deciding whether amend is allowed.

Prefer exhausting bounded local retries before surfacing a commit failure. Ordinary examples include formatting/lint hook rewrites, a fixable test assertion, or a type error in an in-scope branch file.

Run `git status` as the **post-commit status** after each commit to confirm success and see remaining unstaged paths.

## 5. Push

Immediately before pushing, re-check the current branch. If it is still `main` or `master`, create and switch to a new branch using the naming rules from §4, then re-check `git status` and the current branch before pushing.

Push the current branch to its upstream, or `git push -u origin HEAD` if there is no upstream.

If push is rejected due to ordinary feature-branch divergence, handle it automatically before reporting failure:

1. Run `git fetch origin --prune`.
2. Re-check `git status` and branch/upstream.
3. If the current branch is a non-`main`/`master` feature branch, the worktree is clean aside from the new commit(s), and the rejection is a standard non-fast-forward caused by upstream changes, run `git pull --rebase --autostash` on the current branch and then retry `git push`.
4. If rebase or push hits conflicts, hook failures, protected-branch constraints, auth failures, or would require force-push, stop and report the exact blocker. Do **not** force-push or rewrite `main`/`master`.

Do not surface routine successful recoveries as blockers. If fetch, upstream setup, rebase, or retry-push succeeds, continue and report only the final successful state plus a brief note that recovery was automatic.

## 6. Merge request

After the push succeeds, open a new merge request for the pushed branch.

1. Review the full commit message history of the current branch:
   ```bash
   git log --oneline
   ```
2. If this command reveals a significant number of apparently diverse commits, also investigate the actual code changes associated with the messages in question.
3. Based on this collective context, write a descriptive merge request description in active voice, in past tense, and using varied sentence structure. Avoid common LLM-associated characters or phrasing. Include a descriptive title.
4. Use the repository's hosting CLI for creation. For GitLab remotes, use `glab mr create --title "<title>" --description "$(cat <<'EOF'
<description>

EOF
)"`. For GitHub remotes, use `gh pr create --title "<title>" --body "$(cat <<'EOF'
<description>

EOF
)"`. Request network access for the command when needed.
5. If the hosting CLI is unavailable or authentication fails, stop and report the exact blocker plus the title and description you prepared. Do not claim the merge request was opened.

## 7. Report

Return a short summary:

- Preflight: pass (after N attempts) or fail (step + excerpt); which task (`preflight` or `preflight:no-lock`)
- Triage: whether the gate triggered; groups committed vs deferred; whether baseline dirty paths were protected
- Branch and upstream
- Commit SHAs and messages (all commits if split)
- Push: success or rejected (with message)
- Merge request: URL, or blocker if creation failed
- **Excluded untracked paths** (if any)
- **Excluded modified but unstaged paths** (unknown, deferred, or intentionally left out)
