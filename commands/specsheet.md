# handoff

# Role & Objective
Act as a principal software engineer. Generate a concise, structured handoff document summarizing the state of the current conversation so a fresh AI agent (or team of agents) can seamlessly resume the workflow in a new session. This skill handles two distinct scenarios:

1. **Single-Agent Handoff:** Simple, straightforward tasks to be completed by a single agent in a focused session.
2. **Long-Running Agent Team Handoff:** Complex, multi-faceted projects requiring sustained effort from multiple agents with clear goals, objectives, and specifications. 

# Target Destination
*   **File Location:** Save the final document to the host operating system's standard temporary directory (e.g., using system-agnostic methods like Python's `tempfile` or referencing `$TMPDIR` / `%TEMP%`). 
*   **Constraint:** Do NOT write or save this file anywhere within the current active workspace or project root directory.

# Content & Architecture
The handoff document must include the following distinct sections:

1.  **Current State Summary:** A high-level overview of what has been accomplished in this session and where the execution left off.
2.  **Context & Artifact References:** 
    *   Do not duplicate content already captured in external project artifacts (such as PRDs, architecture plans, ADRs, active issues, or git commits/diffs).
    *   Instead, explicitly reference these existing items by their relative file paths or URLs.
3.  **Suggested Skills:** A dedicated section listing specific tools, functions, or capabilities the incoming agent should prioritize or invoke next.
4.  **Immediate Next Steps:** A tactical list of actions for the next session.

## For Long-Running Agent Teams
When the task involves complex, sustained work requiring multiple agents or extended execution, enhance the handoff document with these additional sections:

5.  **Project Objectives & Success Criteria:**
    *   Clearly define the overarching goals and what "done" looks like.
    *   Include measurable success criteria or acceptance criteria.
    *   Specify any business requirements, user stories, or outcomes to achieve.

6.  **Required Software Specifications:**
    *   Technical requirements (languages, frameworks, platforms, versions).
    *   Architecture patterns or constraints (microservices, monolith, event-driven, etc.).
    *   Performance requirements (latency, throughput, scalability targets).
    *   Security and compliance requirements.
    *   Integration points with existing systems.

7.  **Agent Team Composition & Responsibilities:**
    *   Recommended agent roles (e.g., architect, implementer, tester, reviewer).
    *   Specific responsibilities and ownership areas for each agent type.
    *   Coordination strategy and handoff points between agents.

8.  **Constraints & Assumptions:**
    *   Time constraints or milestones.
    *   Resource limitations (budget, API rate limits, compute constraints).
    *   Assumptions made about the environment, dependencies, or user needs.
    *   Known risks or blockers.

9.  **Quality & Testing Requirements:**
    *   Testing strategy (unit, integration, e2e, performance).
    *   Code quality standards (linting, formatting, review process).
    *   Documentation requirements.
    *   Deployment and rollback procedures.

# Decision Logic: Single-Agent vs. Team Handoff

Before creating the handoff document, evaluate the task complexity:

**Create a Single-Agent Handoff when:**
*   The task is well-defined and scoped to a single deliverable.
*   Completion can reasonably happen in one session (< 2 hours of work).
*   Dependencies are minimal and clearly identified.
*   No significant architectural decisions are required.

**Create a Long-Running Team Handoff when:**
*   The task involves multiple subsystems, components, or phases.
*   Work will span multiple sessions or require sustained effort.
*   Significant design, architecture, or technology choices need to be made.
*   Multiple specialized skills are needed (design, implementation, testing, deployment).
*   The project requires coordination across different workstreams.
*   Success criteria are complex or involve multiple stakeholders.

# Security & Privacy Constraints
*   **Strict Redaction:** Scan the summary and redact all sensitive information before saving. This includes, but is not limited to: API keys, authentication tokens, passwords, secrets, and personally identifiable information (PII).

# Conditional Execution (User Arguments)
*   If the user has provided specific arguments or runtime flags, treat them as the explicit scope and focus for the upcoming session. Tailor the "Current State Summary" and "Immediate Next Steps" sections to align directly with the intent of those arguments.
*   When arguments suggest a complex or long-running project, automatically include the enhanced sections for long-running agent teams (objectives, specs, team composition, etc.).
*   If the user explicitly mentions "agent team," "long-running," "multi-phase," or similar terms, default to the comprehensive team handoff format.
