# agent-skills

This is my public prompt library for use across LLM harness platforms. 

The content is oriented toward code review, debugging, planning, documentation sync, response style control, prompt auditing, agent-readiness analysis, deep research, benchmark generation, teaching workspaces, repository workflow execution, and copy-paste prompts for web tools. Most files are written as operational instructions for an agent working in a real repository with access to code, tests, diffs, and shell tools.

Many are my own creations, but a few are adapted or refactored from a couple different sources:

- [teach & /handoff](https://github.com/mattpocock)
- repo-explorer](https://github.com/t3dotgg)

## repository layout

### `commands/`

Plain Markdown prompts intended for explicit slash command-style invocation. These are the source prompts for task-specific workflows.

#### analysis, audit, and debugging

- `/abstract` - targeted structural and maintainability review for a file or module
- `/agentify` - multi-agent repository analysis for LLM navigability, static traceability, context locality, and autonomous change safety
- `/audit` - change-set audit against callers, contracts, and regressions
- `/cleanup` - read-only audit for behavioral defects and unreachable code
- `/investigate` - root-cause investigation from workflow errors and intermediate output
- `/prompts` - audit of prompts, agent instructions, and model-facing text against actual behavior
- `/test_audit` - test-suite audit for behavior coverage, reachability, isolation, and maintainability

#### planning and implementation workflow

- `/apply` - update an implementation plan using findings and critique
- `/fit` - check whether a plan fits the current codebase and existing abstractions
- `/grill` - interactive pressure-test of a plan through focused questioning
- `/implement` - execute a provided plan in order with ongoing verification
- `/plan` - build an implementation plan from findings, diffs, diagnostics, or requests
- `/risks` - resolve plan risks, assumptions, and open questions back into the plan

#### documentation and communication

- `/annotate` - synchronize README files, docstrings, and code comments with implementation
- `/handoff` - write a redacted temporary handoff document for a fresh agent, with artifact references and suggested skills
- `/humanize` - rewrite target text into natural, concise, human-sounding prose while preserving meaning
- `/mr` - write a merge request title and description from actual branch history and diff

#### repository execution workflow

- `/yeet` - end-to-end repository workflow for preflight, triage, commit, push, and merge request creation

### `prompts/`

Copy-paste prompts intended for associated web page tools rather than slash-command or skill invocation.

- `notebook_lm` - NotebookLM audio overview prompt for neutral, structured, technically precise summaries of machine learning, AI architecture, and computational research papers

### `skills/`

These are meant for harnesses that support reusable skills selected from natural-language intent rather than explicit slash commands.

Current skills:

- `annotate` - documentation and annotation synchronization
- `audit` - change-set audit with caller and contract tracing
- `fit` - plan-fit review against the existing codebase
- `humanize` - natural, concise, human-sounding user-facing prose for non-trivial responses
- `investigate` - workflow failure diagnosis from supplied evidence
- `repo-explorer` - external repository cloning and inspection using a reusable local cache
- `research` - deep research workflow skill for goal-setting, parallel source discovery, source verification, claim extraction, skeptic review, and synthesis
- `teach` - stateful teaching workspace skill for creating mission-grounded lessons, learning records, reference documents, resources, and reusable lesson assets

### `agents/`

System prompt text that defines the baseline agent behavior.

- `pi.md` - a CLI-focused coding-agent policy emphasizing directness, minimal diffs, contract-first changes, deterministic verification, realistic testing, and cautious handling of destructive operations

- `judge.md` - an LLM response-evaluation policy that scores a `TEST_RESPONSE` against an `INITIAL_PROMPT` and `REFERENCE_RESPONSE`, then returns strict JSON with per-criterion scores, a rounded composite score, and a one-paragraph review summary

### `workflows/`

End-to-end operating procedures for tasks that need more than a single command invocation. A workflow defines the objective, phase gates, subagent topology, model and thinking tiers, verification requirements, and stopping conditions for a long-running loop.

Unlike commands, workflows do not just tell one agent how to perform a bounded action. They describe how a coordinator should split work across subagents, keep subagent outputs advisory until verified, sequence dependent phases, and decide when the overall objective is complete.

Current workflows:

- `/kaplan`
  - Objective: generate a corpus-grounded benchmark dataset with adversarial JSON evaluation items.
  - Subagent use: an orchestrator analyzes the source corpus, builds the distribution matrix, assigns parallel item-generation batches to worker subagents, then aggregates and validates the final dataset.
- `/unvibe`
  - Objective: convert a broad simplification audit into a reviewed, implemented, tested, committed, and PR-ready change.
  - Subagent use: a coordinator may use bounded audit scouts, plan reviewers, implementation workers, and confirmation reviewers, while retaining responsibility for evidence checks, integration decisions, verification, and branch operations.

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
- writing concise, natural user-facing responses without generic assistant artifacts
- handing off conversation state to a fresh agent without duplicating existing artifacts
- building source-verified research briefs from parallel discovery, evidence extraction, and synthesis
- generating corpus-grounded benchmark datasets with adversarial evaluation items
- building stateful teaching workspaces with lessons, references, resources, and learning records
- completing branch-to-MR workflows
- adapting web-page tools with reusable copy-paste prompts

## how to use this repo

### use a command prompt

Use files from `commands/` when your harness supports explicit user-invoked slash commands or named prompt templates.

Typical pattern:

1. choose a command file that matches the task,
2. register it in your harness as a slash command or reusable prompt,
3. invoke it with the relevant repo context, plan file, diff, or logs.

Examples:

- use `/investigate` for CI failures or broken workflow output
- use `/audit` to review a branch or diff for contract drift and regressions
- use `/agentify` to score a repository's agent readiness and identify structural friction for LLM coding agents
- use `/annotate` to update docs and comments without changing executable code
- use `/handoff` to save a redacted session summary outside the workspace for a fresh agent
- use `/humanize` to rewrite text so it sounds natural, concise, and less generated
- use `/yeet` only when you want the agent to drive the full commit and MR flow explicitly

### use a web tool prompt

Use files from `prompts/` when a web page expects pasted instructions rather than a registered command or skill.

Typical pattern:

1. choose the prompt file for the target web tool,
2. paste it into the tool's instruction or customization field,
3. attach or provide the source material the prompt expects.

Example:

- use `prompts/podcast.md` with NotebookLM to generate a measured technical audio overview of an attached AI or machine learning paper

### use a skill

Use files from `skills/` when your platform supports skill selection based on task intent.

Typical pattern:

1. place the skill file where your harness expects reusable skills,
2. keep the front matter intact so the harness can read the skill name, description, and allowed tools,
3. rely on the description text to route relevant natural-language requests to the right skill.

Skills in this repo are best suited to recurring, high-signal workflows such as investigation, audits, planning, documentation sync, response style control, repository exploration, deep research, and stateful teaching.

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

In this repo, `yeet.md` is a good example of something better kept as an explicit command, while `investigate.md`, `audit.md`, and `repo-explorer.md` translate well into skills.

## authoring conventions in this repo

Files here generally follow these conventions:

- tell the agent what evidence to gather before concluding
- define what is in scope and out of scope
- separate method from required output shape
- avoid broad speculative refactors
- prefer concrete verification over intuition

That makes the prompts easier to reuse across different harness platforms without changing their core operational behavior.
