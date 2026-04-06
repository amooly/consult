---
name: init
description: "Initialize a project's AI instruction structure. Discovers existing instruction files, merges them into AGENTS.md, scaffolds docs/ with starter files, and writes pointer files for all known AI harnesses."
user-invocable: true
allowed-tools: Glob, Read, Write, Edit, AskUserQuestion
---

# Init — Set Up Project AI Instructions

You are setting up (or consolidating) the AI instruction structure for this project. Your job is to create a clean, consistent knowledge hierarchy: `AGENTS.md` as the single AI truth, `docs/` for shared knowledge, and thin pointer files for every AI harness.

## Step 1: Scan for Existing Instruction Files

Search the project root for ALL known AI instruction files:

- `AGENTS.md`
- `CLAUDE.md`
- `GEMINI.md`
- `COPILOT.md`
- `CODING_GUIDELINES.md`
- `CONVENTIONS.md`
- `.cursorrules`
- `.windsurfrules`
- `.clinerules`
- `.github/copilot-instructions.md`
- `.cursor/rules/*.md` or `.cursor/rules/*.mdc`
- `.ai/**/*`
- `README.md` *(scan only — look for AI-relevant headings: "AI", "LLM", "Agent", "Coding Guidelines", "Architecture", "Rules")*
- `docs/` *(list all existing files)*

Report what you found. Note empty files.

## Step 2: Branch on Results

### 2a. No instruction files found → Fresh Scaffold

Tell the user: "No AI instruction files found. I'll create the full instruction structure now."

Then proceed directly to **Step 3** (scaffold) with empty starter content.

### 2b. Instruction files found → Merge

1. **List** all discovered files with a 2–3 sentence summary of each.
2. **Detect conflicts** across files. Flag contradictions clearly:
   ```
   CONFLICT: Indentation style
   - .cursorrules says: "Use tabs"
   - CLAUDE.md says: "Use 2-space indentation"
   ```
3. **Ask the user** to confirm the merge plan and resolve conflicts. Wait for explicit confirmation.
4. **Extract and classify** all content into one of:
   - AI-specific rules / constraints → `AGENTS.md` (Rules section)
   - Architecture / tech stack / layers → `docs/architecture.md`
   - Global coding conventions → `docs/coding_conventions.md`
   - Tool-specific conventions (ORM, testing, etc.) → `docs/coding_conventions/<tool>.md`
   - Human-readable narrative → `README.md` (leave untouched, flag for user)

## Step 3: Write the File Structure

### 3a. Create or update `AGENTS.md`

Write `AGENTS.md` at the project root. If it already exists with content, merge into it:

```markdown
<!-- Managed by consult. Edit directly or use /consult:add-to to add new knowledge. -->
# Project Instructions

> This file is the single source of truth for AI agents working in this project.
> Human-readable documentation lives in `docs/` and is referenced below.

## Architecture
→ See [docs/architecture.md](docs/architecture.md) for tech stack, layers, and dependency rules.

<!-- Add any AI-specific architecture constraints here that are NOT in docs/architecture.md -->

## Coding Conventions
→ See [docs/coding_conventions.md](docs/coding_conventions.md) for global coding patterns and style rules.

<!-- Add any AI-specific coding constraints here that are NOT in docs/coding_conventions.md -->

## Rules
<!-- Explicit rules AI agents must follow. Keep these short and unambiguous. -->
<!-- Use /consult:add-to to append new rules from conversations. -->
```

**Fill in sections** with merged content from Step 2. Do not leave discovered instructions behind.

### 3b. Scaffold `docs/`

Create these files if they do not exist (do not overwrite non-empty files without confirmation):

**`docs/architecture.md`**
```markdown
# Architecture

## Tech Stack
<!-- List the primary languages, frameworks, runtimes, and infrastructure here. -->

## System Layers
<!-- Describe the major layers (e.g., API, service, data) and their responsibilities. -->

## Dependency Rules
<!-- Which layers can depend on which? What is forbidden? -->

## Key Design Decisions
<!-- Record significant architectural choices and the reasoning behind them. -->
```

**`docs/coding_conventions.md`**
```markdown
# Coding Conventions

> These conventions apply to ALL code in this project. Tool-specific conventions are in `docs/coding_conventions/`.

## Naming
<!-- Variables, functions, files, classes — naming patterns and case conventions. -->

## Formatting
<!-- Indentation, line length, file structure expectations. -->

## Patterns
<!-- Preferred design patterns, what to avoid, and why. -->

## Error Handling
<!-- How errors should be caught, logged, and surfaced. -->

## Testing
<!-- What to test, naming conventions for tests, where test files live. -->

## Tool-Specific Conventions
<!-- Links to sub-files for specific tools/libraries: -->
<!-- - [ORM](coding_conventions/orm.md) -->
<!-- - [API client](coding_conventions/api.md) -->
```

**`docs/coding_conventions/` directory**  
Create a `.gitkeep` placeholder so the directory is tracked in git. Do not create individual tool files unless you extracted tool-specific content during Step 2.

### 3c. Write Pointer Files

Write the following files. If they already exist, **overwrite** them with a single pointer line. Do NOT merge content — content goes in `AGENTS.md`:

| File | Content |
|------|---------|
| `CLAUDE.md` | `See [AGENTS.md](AGENTS.md) for all project instructions.` |
| `GEMINI.md` | `See [AGENTS.md](AGENTS.md) for all project instructions.` |
| `.cursorrules` | `See AGENTS.md for all project instructions.` |
| `.windsurfrules` | `See AGENTS.md for all project instructions.` |
| `.clinerules` | `See AGENTS.md for all project instructions.` |
| `.github/copilot-instructions.md` | `See [AGENTS.md](../../AGENTS.md) for all project instructions.` |

Only create pointer files that either (a) already existed in the project, or (b) the user confirms they want.  
**Do NOT modify `README.md`.**

## Step 4: Summary Report

After writing all files, print a summary:

```
✅ Created/updated AGENTS.md
✅ Created docs/architecture.md
✅ Created docs/coding_conventions.md
✅ Created docs/coding_conventions/ (empty)
✅ Wrote pointer: CLAUDE.md
✅ Wrote pointer: GEMINI.md
✅ Wrote pointer: .cursorrules
⏭  Skipped: .windsurfrules (not found in project, not created)
```

Then remind the user:
> "Use `/consult:add-to <knowledge>` to add new architectural decisions, coding conventions, or AI rules to the right file without hunting for where it belongs."

## Important Constraints

- Always ask for confirmation before writing or overwriting any file.
- Preserve all meaningful instructions from existing files — never silently drop content.
- If `AGENTS.md` already exists and is non-empty, treat it as a source to merge (not as a target to blindly overwrite).
- Never put content into pointer files (`CLAUDE.md`, etc.) — all knowledge goes into `AGENTS.md` or `docs/`.
