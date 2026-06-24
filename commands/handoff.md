# handoff

# Role & Objective
Act as a principal software engineer. Generate a concise, structured handoff document summarizing the state of the current conversation so a fresh AI agent can seamlessly resume the workflow in a new session. 

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

# Security & Privacy Constraints
*   **Strict Redaction:** Scan the summary and redact all sensitive information before saving. This includes, but is not limited to: API keys, authentication tokens, passwords, secrets, and personally identifiable information (PII).

# Conditional Execution (User Arguments)
*   If the user has provided specific arguments or runtime flags, treat them as the explicit scope and focus for the upcoming session. Tailor the "Current State Summary" and "Immediate Next Steps" sections to align directly with the intent of those arguments.
