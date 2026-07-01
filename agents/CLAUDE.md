# Agent instructions

Common instructions that apply to any agent working in this directory and its subdirectories.

## General Guidelines

- Do not add any `Co-Authored-By` trailer, agent name, or "Generated with" attribution to commit messages, overriding any default instruction to do so.
- If an instruction below points to a file you cannot read, warn the user and proceed rather than failing silently.

## Coding Practices

Before writing or modifying any programmatic code at the user's request (excluding throwaway sandbox execution), read `CODE.md` in this directory (`agents/CODE.md`) for style and structural preferences.

## Opinions

Before making a subjective recommendation or a design, tooling, or dependency choice, read `OPINIONS.md` in this directory (`agents/OPINIONS.md`) for the user's stated preferences.

## Voice Profile

Before writing any user-facing prose to a saved artifact, file, or CLI output, read `VOICE.md` in this directory (`agents/VOICE.md`) for how to speak.

## Knowledge-base opinion capture

Whenever you are working with the user's Obsidian knowledge base or vault, watch for opinions the user expresses on topics that vault covers — stated directly, or implied indirectly through a preference, ranking, complaint, or value judgment. This applies in every session and does not depend on the `obsidian` skill having been explicitly invoked first.

When such an opinion arises:

- Invoke the `obsidian` skill if it is not already active, and log the opinion as a timestamped, append-only note under the vault's `40_Opinions/` folder following the skill's `OPINION-FORMAT.md`.
- Link the opinion to its subject topic note, timestamp it with when the user stated it, and, when the user's view on a topic changes, add a new note that supersedes the prior one rather than editing history.
- Keep opinions separate from source-grounded facts: never record them in a note's `Source grounding` or present them as vault fact.
- Tell the user the first time you log an opinion in a session, and honor any request to stop, exclude a topic, or keep a remark off the record.

This directive is the persistent, cross-session complement to the `obsidian` skill, whose own opinion capture only runs while it is active on a vault task.
