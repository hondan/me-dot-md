# START.md
name: START.md
description: Starting instructions for setting up a new project with AI agent context orchestration
version: 1.1
author: Dan Han

---

## Purpose

This file bootstraps portable, persistent memory for AI coding agents (Claude Code, OpenAI Codex, etc.) across sessions and projects. When placed in a folder and read by an agent, it ensures all context is consistently loaded and maintained, thus eliminating the need to rebuild context at the start of each new session.

---

## Instructions

Before writing any code or making any changes, set up the context files described below. These files serve as the agent's working memory across sessions.

**Always prompt the user for information to populate context files before starting work.** Files must be structured for easy agent parsing: use consistent headings, explicit labels, and machine-friendly formats.

---

## Bootstrap Condition

> **If any required context files are missing when a session begins, the agent MUST enter SETUP MODE.**
>
> In SETUP MODE:
> 1. Identify which required files are absent.
> 2. Prompt the user to provide the necessary information.
> 3. Create the missing files before proceeding with any repository changes.
>
> Do not attempt to infer or fabricate missing context.

---

## File Structure

### Files to Create

---

#### `CLAUDE.md`
- **Location:** Project root (`./`)
- **Purpose:** Orchestration instructions specifically for Claude Code. Defines load order, behavioral rules, and session lifecycle requirements.
- **Required:** Yes
- **Schema version field:** Include `CONTEXT_VERSION: x.x` at the top.

**Required contents:**
- Load order (see Execution Order below)
- Bootstrap condition handling (see above)
- Instruction to update `.CHANGELOG.md` after **any** change to the repository or context files
- Explicit session close instruction: *"Before ending a session, prepend a new entry to `.CHANGELOG.md` summarizing all changes made."*

---

#### `AGENTS.md`
- **Location:** Project root (`./`)
- **Purpose:** Orchestration instructions for all other AI agents (OpenAI Codex, Copilot, etc.). Mirrors CLAUDE.md in intent but may be tailored to the target agent's execution model.
- **Required:** Yes
- **Schema version field:** Include `CONTEXT_VERSION: x.x` at the top.

**Required contents:**
- Same load order and bootstrap condition as CLAUDE.md
- Instruction to update `.CHANGELOG.md` after **any** change to the repository or context files
- Explicit session close instruction: *"Before ending a session, prepend a new entry to `.CHANGELOG.md` summarizing all changes made."*

---

#### `.CHANGELOG.md`
- **Location:** Project root (`./`)
- **Purpose:** Persistent memory across sessions. Agents write to it after every change and read from it at the start of each session to understand prior work.
- **Required:** Yes

**Format rules:**
- Newer entries must appear at the **top** of the file (prepend, do not append).
- Each entry must follow this schema:

```markdown
## [YYYY-MM-DD HH:MM UTC] <Short Session Title>

- **Agent:** Claude Code | Codex | OpenCode | etc.
- **Changed:** <File or component modified>
- **Why:** <Reason, task reference, or ticket ID>
- **Notes:** <Optional: decisions made, blockers, follow-ups>
```

**Example:**

```markdown
## [2025-04-14 09:32 UTC] Auth Module Refactor

- **Agent:** Claude Code
- **Changed:** src/auth/login.ts, src/auth/token.ts
- **Why:** Replaced JWT library with jose for ESM compatibility
- **Notes:** Old refresh token logic removed; clients must re-authenticate
```

---

#### `DESIGN.md`
- **Location:** Project root (`./`)
- **Purpose:** Master design document with defined start and end for unit tasks. Agents use this to understand what is being built and to plan work across sessions.
- **Required:** Yes

**Required sections:**

```markdown
## Goal
<One paragraph describing what this project does and why.>

## Stack & Architecture
<Languages, frameworks, key dependencies, and high-level structure.>

## Features
<List of features in scope.>

## Tasks
| ID  | Description         | Status              | Owner / Agent  |
|-----|---------------------|---------------------|----------------|
| T01 | Example task        | todo / in-progress / done | -         |

## Out of Scope
<Explicit list of things this project will NOT do.>

## Open Questions
<Unresolved decisions that need user or agent input.>
```

> **Task status values:** `todo` | `in-progress` | `done` | `blocked`
> Agents must update task statuses in `DESIGN.md` as work progresses.

---

### Existing Files to Load (If Present)

#### `../contextcard/AGENTS.md`
- **Purpose:** Personal context loading instructions. Contains the load order and parameters for the user's personal context cards. Agents should follow the instructions here to learn about the user before starting work.
- **Required:** No - load if present, skip gracefully if absent.

> **Path fallback:** If `../contextcard/AGENTS.md` is not found, the agent must notify the user:
> *"Personal context file not found at `../contextcard/AGENTS.md`. Would you like to provide personal context manually, or specify an alternate path?"*
>
> Do not assume a default location beyond the path above.

---

## Execution Order

Agents must follow this order at the start of every session:

1. **Attempt to load personal context** from `../contextcard/AGENTS.md`. If not found, notify the user and proceed.
2. **Check for required context files** (`CLAUDE.md` / `AGENTS.md`, `.CHANGELOG.md`, `DESIGN.md`). If any are missing, enter SETUP MODE.
3. **Read `.CHANGELOG.md`** to understand what has already been done.
4. **Read `DESIGN.md`** to understand the project goals, architecture, and current task statuses.
5. **Prompt the user** for any missing or ambiguous information needed to proceed.
6. **Begin work**, updating task statuses in `DESIGN.md` as items move through the pipeline.
7. **On session close:** Prepend a new entry to `.CHANGELOG.md` before ending the session.

---

## Context Version

This document follows schema version `1.1`. When making structural changes to any context file, update the `CONTEXT_VERSION` field in `CLAUDE.md` and `AGENTS.md` accordingly and log the change in `.CHANGELOG.md`.
