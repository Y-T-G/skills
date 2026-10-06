---
name: learn
description: Extract lessons, corrections, and workflows from recent conversation turns and persist them as rules or skills. Use when the user types /learn or asks to remember, save, or learn from a correction, preference, or procedure.
argument-hint: "[optional: specific topic or rule to learn]"
---

# Learn

Persist lessons, corrections, and workflows from recent conversation turns into agent configuration, so future sessions remember them.

## When to learn

- The user corrected a mistake or preference (such as tool flags, formatting, or forbidden commands).
- A non-obvious debugging session succeeded after earlier attempts failed.
- The user established a reusable procedure or workflow.
- If the recent conversation has no new reusable behaviors or constraints, state that clearly and exit without proposing changes.

## Workflow

### 1. Identify what to learn

1. **Find the trigger:** Look at recent user messages for explicit corrections, constraints, or preferences (such as "no", "do not", "use X instead", "always do Y").
2. **Isolate the root fix:** Compare failed attempts against the final working solution to pinpoint the critical change. Focus on the underlying cause, not surface symptoms.
3. **Choose the scope:**
   - **Project:** Specific to this codebase, architecture, or repository.
   - **Global:** General user preferences, device quirks, or personal workflows across all projects.

### 2. Classify: rule vs skill

- **Rule:** Universal constraints, behavioral boundaries, coding styles, environment quirks, or formatting rules.
  - Destination: rule files such as `AGENTS.md`, `CLAUDE.md`, or `GEMINI.md`.
  - Pick the file matching the project or agent conventions. If a rule file already exists, prefer that file.
- **Skill:** Actionable multi-step procedures, tool chains, complex flag combinations, or runbooks that the agent executes on demand.
  - Destination: a directory with `SKILL.md` under the project or personal skills directory.

### 3. Update vs create

- **Update existing (preferred):** If an existing rule or skill covers the domain, refine it. Incorporate the new edge case, correction, or constraint into the existing text. Keep rules consolidated rather than duplicating instructions.
- **Create new:** Only create a new rule file or skill when the workflow covers an entirely new domain.

### 4. Mandatory proposal workflow

Never edit configuration files or create skills silently.

1. **Formulate the proposal:**
   - Classification: rule or skill
   - Target file path and scope (project or global)
   - Rationale: what triggered the lesson and why it matters
   - Exact text: show the exact markdown diff or snippet to insert
2. **Present to user:** Show the proposal concisely and ask for confirmation before writing.
3. **Apply:** Once approved, write or edit the target file. Keep edits clean, minimal, and focused.
