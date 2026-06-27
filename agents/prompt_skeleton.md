# Agent Prompt Skeleton

This is a reference guide for designing effective prompts for LLM-based agents. Use it to structure prompts consistently, avoid common pitfalls, and choose the right hybrid style for each task type.

---

## 1. Mental Model (why prompting works)

LLMs don't "think"; they predict the next token to fit the scene you set.

**Prompting = scene-setting for a robotic improv partner.**

Good prompts constrain the prediction space: role, goal, format, rules.

---

## 2. Core Skeleton (the must-haves)

Use (at least) these blocks — front-loaded, in this order:

| Block | Purpose |
|-------|---------|
| **ROLE** | Who the model is (expert persona, tone, values) |
| **GOAL** | One clear outcome; define success |
| **RULES** | Positive/negative constraints, ranked by priority |
| **THINK** | Your desired process (steps, trade-offs, verification) |
| **CONTEXT** | Facts the model won't infer (tools, audience, limits) |
| **EXAMPLES** | Small, high-signal "good answer" patterns |
| **AUDIENCE** | Reading level, vibe, domain familiarity |
| **FORMAT** | Exact structure (sections/tables/length/markdown) |

```xml
<role> You are a [specific expert]. </role>
<goal> [1 sentence outcome]. </goal>
<rules priority="high">
- Always: [rule]
- Never: [rule]
</rules>
<think> Step-by-step: [3–5 steps incl. verify]. </think>
<context> [facts, constraints]. </context>
<format> [bullets / table / sections / word limits]. </format>
```

---

## 3. Drift Control (long chats)

Models drift as early tokens fall out of the context window. Build durability in with a reinforcement block:

```xml
<reinforce_in_long_chats>
  <reset_command>Re-read Role, Goal, Rules before each section.</reset_command>
  <check_in>Every 3–4 turns, confirm adherence & format.</check_in>
  <self_correction enabled="true">
    If style or claims drift, re-ground and revise before output.
  </self_correction>
</reinforce_in_long_chats>
```

Paste a compact reminder every 3–5 messages (role/goal/rules/format).

---

## 4. Hybrid Prompts (house style)

Decide first whether to use a hybrid pair or the full hybrid:

- **Functional + Meta** → "Do the task, then self-improve it."
- **Meta + Exploratory** → "Refine the brainstorm, widen/sharpen ideas."
- **Exploratory + Role** → "Creative ideation with expert guardrails."
- **Functional + Role** → "Precise task, expert tone/standards."
- **Full hybrid** (Functional + Meta + Exploratory + Role) → Complex, end-to-end outputs with self-checks and creativity.

---

## 5. Model Guide Alignment (what to toggle)

- **`reasoning_effort`**: `minimal` (speed) ↔ `high` (complex, multi-step)
- **`verbosity`**: Keep final answers concise; raise only for code/docs
- **Responses API**: Reuse `previous_response_id` to preserve reasoning across turns
- **Tool preambles**: plan → act → narrate → summarize

**Agentic eagerness knobs:**
- Less eagerness: set search/tool budgets; early-stop criteria
- More eagerness: `<persistence>` — keep going until fully solved

---

## 6. Clarity-First Rule

- Define any unfamiliar term in plain English on first use.
- If the user seems new to a concept, add a 1-sentence explainer.
- Ask for missing inputs only if essential; otherwise proceed with stated assumptions and list them.

---

## 7. Add-On Blocks

**Transcript-following rule** (for courses/videos):

```xml
<source_adherence>
  Treat the provided transcript as the source of truth.
  Cite timestamps; flag any inference as "beyond transcript."
</source_adherence>
```

**Beginner-mode explainer** (SQL, coffee, etc.):

```xml
<beginner_mode>
  Define terms, give analogies, show tiny examples, list pitfalls.
</beginner_mode>
```

---

## 8. Trade-offs & Pitfalls

| Pitfall | How to avoid |
|---------|-------------|
| **Identity collisions** | Don't mix conflicting personas near code/logic; specify tone separately |
| **Contradictions** | Use ranked rules to prevent silent conflicts |
| **Overlong examples** | Keep them small — large examples eat context |
| **CoT overhead** | Step-by-step helps quality but costs tokens — use only for hard tasks |

---

## 9. Quick Chooser

| Need | Use |
|------|-----|
| Crisp deliverable (specs, plan, email, listing) | Functional + Role |
| Ideas and synthesis | Exploratory + Role or Meta + Exploratory |
| Model critiques/refines its own work | Functional + Meta |
| Big, multi-stage, end-to-end artifact | Full hybrid |

---

## 10. Ready-to-Use Prompt Templates

### A) Short Skeleton (everyday)

```xml
<role>You are a [expert] for [audience]. Tone: [style].</role>
<goal>[One clear outcome]. Success = [criteria].</goal>
<rules priority="high">Always [rule]; Never [rule].</rules>
<think>Steps: clarify → plan → do → verify → refine.</think>
<context>[facts, constraints, sources].</context>
<format>[sections/tables/word limits].</format>
<reinforce_in_long_chats>
  <reset_command>Re-read Role/Goal/Rules before answering.</reset_command>
</reinforce_in_long_chats>
```

### B) Full Hybrid (complex)

```xml
<role>[Expert persona]</role>
<goal>[Outcome]</goal>
<rules priority="high">[…ranked…]</rules>
<think>[step-by-step incl. trade-offs & verification]</think>
<context>[inputs/sources/constraints]</context>
<examples>[1 small good sample]</examples>
<audience>[reader profile]</audience>
<format>[explicit sections + limits]</format>
<clarity_first enabled="true"/>
<source_adherence enabled="true"/>
<reinforce_in_long_chats>
  <reset_command/> <check_in/> <self_correction enabled="true"/>
</reinforce_in_long_chats>
<persistence>Finish all sections before handing back.</persistence>
<tool_preambles>plan → act → narrate → summarize.</tool_preambles>
```
