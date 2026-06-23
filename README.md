# agent-skills

This is my public prompt library for use across LLM harness platforms. Many are my own creations, while a few are adapted or refactore from versions in a variety of different places.

This repository collects reusable:

- slash-command prompts for focused workflows,
- skill files with trigger metadata and operating instructions,
- system prompt text for a direct, verification-first coding agent.

The content is oriented toward code review, debugging, planning, documentation sync, prompt auditing, agent-readiness analysis, deep research, benchmark generation, teaching workspaces, and repository workflow execution. Most files are written as operational instructions for an agent working in a real repository with access to code, tests, diffs, and shell tools.

## repository layout

### `commands/`

Plain Markdown prompts intended for explicit command-style invocation. These are the source prompts for task-specific workflows.

#### analysis, audit, and debugging

- `abstract.md` - targeted structural and maintainability review for a file or module
- `agentify.md` - multi-agent repository analysis for LLM navigability, static traceability, context locality, and autonomous change safety
- `audit.md` - change-set audit against callers, contracts, and regressions
- `cleanup.md` - read-only audit for behavioral defects and unreachable code
- `investigate.md` - root-cause investigation from workflow errors and intermediate output
- `prompts.md` - audit of prompts, agent instructions, and model-facing text against actual behavior
- `test_audit.md` - test-suite audit for behavior coverage, reachability, isolation, and maintainability
- `unvibe.md` - broad simplification audit for duplication, dead code, conflicting logic, and low-value complexity

#### planning and implementation workflow

- `apply.md` - update an implementation plan using findings and critique
- `fit.md` - check whether a plan fits the current codebase and existing abstractions
- `grill.md` - interactive pressure-test of a plan through focused questioning
- `implement.md` - execute a provided plan in order with ongoing verification
- `plan.md` - build an implementation plan from findings, diffs, diagnostics, or requests
- `risks.md` - resolve plan risks, assumptions, and open questions back into the plan

#### documentation and communication

- `annotate.md` - synchronize README files, docstrings, and code comments with implementation
- `handoff.md` - write a redacted temporary handoff document for a fresh agent, with artifact references and suggested skills
- `mr.md` - write a merge request title and description from actual branch history and diff

#### repository execution workflow

- `yeet.md` - end-to-end repository workflow for preflight, triage, commit, push, and merge request creation

### `skills/`

Skill files in the same general style as the existing `repo-explorer` skill: front matter plus operational instructions. These are meant for harnesses that support reusable skills selected from natural-language intent rather than explicit slash commands.

Current skills:

- `annotate.md` - documentation and annotation synchronization
- `audit.md` - change-set audit with caller and contract tracing
- `fit.md` - plan-fit review against the existing codebase
- `investigate.md` - workflow failure diagnosis from supplied evidence
- `plan.md` - implementation plan generation from confirmed findings
- `prompts.md` - prompt and instruction audit against repository behavior
- `repo-explorer.md` - external repository cloning and inspection using a reusable local cache
- `research/` - deep research workflow skill for goal-setting, parallel source discovery, source verification, claim extraction, skeptic review, and synthesis
- `teach/` - stateful teaching workspace skill for creating mission-grounded lessons, learning records, reference documents, resources, and reusable lesson assets
- `test_audit.md` - test-suite audit for coverage quality and isolation

### `agents/`

System prompt text that defines the baseline agent behavior.

- `pi.md` - a CLI-focused coding-agent policy emphasizing directness, minimal diffs, contract-first changes, deterministic verification, realistic testing, and cautious handling of destructive operations

- `judge.md` - an LLM response-evaluation policy that scores a `TEST_RESPONSE` against an `INITIAL_PROMPT` and `REFERENCE_RESPONSE`, then returns strict JSON with per-criterion scores, a rounded composite score, and a one-paragraph review summary

### `workflows/`

End-to-end operating procedures that combine commands, skills, subagents, and verification into one larger workflow/loop.

- `tester.md` - multi-agent benchmark generation workflow that analyzes a knowledge corpus, builds a distribution matrix, assigns parallel question-generation batches, and validates adversarial JSON evaluation items
- `unvibe.md` - standalone automated audit-to-PR loop for broad simplification review, planning, plan review, implementation, confirmation, final audit, commit, push, and PR creation

## what kind of content lives here

This is not a general prompt dump. The repository content is opinionated and procedural.

Common characteristics across the files:

- evidence-first reasoning over speculation
- preference for repository inspection before proposing abstractions
- explicit scope control
- strong verification requirements
- direct output formats designed for practical engineering work
- separation between read-only review workflows and mutating implementation workflows

A large portion of the library is aimed at software delivery tasks such as:

- investigating failures
- auditing changes and tests
- analyzing codebases for LLM navigability and agent modification safety
- refining or applying plans
- aligning docs with code
- generating reviewer-facing summaries
- handing off conversation state to a fresh agent without duplicating existing artifacts
- building source-verified research briefs from parallel discovery, evidence extraction, and synthesis
- generating corpus-grounded benchmark datasets with adversarial evaluation items
- building stateful teaching workspaces with lessons, references, resources, and learning records
- completing branch-to-MR workflows

## how to use this repo

### use a command prompt

Use files from `commands/` when your harness supports explicit user-invoked slash commands or named prompt templates.

Typical pattern:

1. choose a command file that matches the task,
2. register it in your harness as a slash command or reusable prompt,
3. invoke it with the relevant repo context, plan file, diff, or logs.

Examples:

- use `commands/investigate.md` for CI failures or broken workflow output
- use `commands/audit.md` to review a branch or diff for contract drift and regressions
- use `commands/agentify.md` to score a repository's agent readiness and identify structural friction for LLM coding agents
- use `commands/annotate.md` to update docs and comments without changing executable code
- use `commands/handoff.md` to save a redacted session summary outside the workspace for a fresh agent
- use `commands/yeet.md` only when you want the agent to drive the full commit and MR flow explicitly

### use a skill

Use files from `skills/` when your platform supports skill selection based on task intent.

Typical pattern:

1. place the skill file where your harness expects reusable skills,
2. keep the front matter intact so the harness can read the skill name, description, and allowed tools,
3. rely on the description text to route relevant natural-language requests to the right skill.

Skills in this repo are best suited to recurring, high-signal workflows such as investigation, audits, planning, documentation sync, repository exploration, deep research, and stateful teaching.

### use the system prompt

Use `system/pi.md` as a base policy for a coding agent when you want a direct, low-noise, verification-first operating style.

It is most useful when the agent has:

- shell access,
- repository read and edit tools,
- a need to run tests or validations,
- responsibility for making narrowly scoped engineering changes.

## choosing commands vs skills

Use a command when:

- the workflow is explicit and user-driven,
- the action is high-risk or highly stateful,
- you want exact invocation semantics.

Use a skill when:

- the task recurs across many conversations,
- the workflow benefits from automatic intent matching,
- the instructions are stable and reusable.

In this repo, `yeet.md` is a good example of something better kept as an explicit command, while `investigate.md`, `audit.md`, and `test_audit.md` translate well into skills.

## authoring conventions in this repo

Files here generally follow these conventions:

- tell the agent what evidence to gather before concluding
- define what is in scope and out of scope
- separate method from required output shape
- avoid broad speculative refactors
- prefer concrete verification over intuition

That makes the prompts easier to reuse across different harness platforms without changing their core operational behavior.
