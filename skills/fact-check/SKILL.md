---
name: fact-check
description: "Validate that the project's code actually follows the rules defined in AGENTS.md and docs/. Extracts verifiable rules from all instruction files, searches the codebase for violations, and reports findings with file and line references."
user-invocable: true
allowed-tools: Glob, Grep, Read, AskUserQuestion
---

# Fact-Check — Validate Code Against AI Instructions

You are a compliance checker. Your job is to extract every verifiable rule from the project's instruction files and check whether the actual codebase follows them. Report violations with precise file and line references.

## Step 1: Load All Instruction Files

Read the following files (skip any that don't exist):
- `AGENTS.md`
- `docs/architecture.md`
- `docs/coding_conventions.md`
- All `docs/coding_conventions/*.md` files
- All `docs/features/*.md` files

If `AGENTS.md` does not exist, tell the user: "No instruction structure found. Run `/consult:init` first."

## Step 2: Extract Verifiable Rules

From all instruction files, extract rules that are **mechanically checkable** by reading code. A rule is verifiable if it can be confirmed or falsified with a file glob, grep, or naming pattern check.

Examples of verifiable rules:
- Naming conventions: "Use camelCase for all function names"
- Import restrictions: "Never import across module boundaries with `../`"
- File placement: "All API handlers must live in `src/handlers/`"
- Forbidden patterns: "Never use `console.log` in production code"
- Required patterns: "All async functions must handle errors with try/catch"

Examples of **non-verifiable** rules (skip these):
- "Keep functions short and focused"
- "Write clear commit messages"
- Subjective quality judgments

Present the extracted rules list to the user:
```
I found N verifiable rules across your instruction files. Checking all of them.
Skipping N rules that require human judgment (listed at the end).
```

Do not wait for confirmation — proceed immediately to Step 3.

## Step 3: Check Each Rule

For each verifiable rule:
1. Translate it into a concrete search: a file glob, a grep pattern, or a naming check
2. Determine the scope: which file extensions and directories to search (exclude `node_modules/`, `.git/`, build output dirs like `dist/`, `build/`, `.next/`)
3. Run the search
4. Record: rule text, files checked, violations found (file path + line number)

**Translation examples:**
- "Use camelCase for function names" → grep for `function [a-z_]+_[a-z]` in `.ts`, `.js` files
- "Never import from `../` across module boundaries" → grep for `from '\.\./\.\./` in source files
- "All handlers in `src/handlers/`" → glob for handler-named files outside that path
- "Never use `console.log`" → grep for `console\.log` in source files (excluding test files if specified)

## Step 4: Report Findings

Print a structured report:

```
## Fact-Check Report

### ✅ Rules Followed (N)
- **[rule text]** — checked N files, no violations found

### ❌ Violations Found (N rules, M total violations)

**[rule text]**
  - `src/foo.ts:42` — [one-line description of the violation]
  - `src/bar.ts:17` — [one-line description of the violation]

### ⚠️ Rules Not Mechanically Verifiable (N)
- [rule text] — requires human review
```

If no violations are found across all rules, say so clearly and congratulate the user.

After the report, if any violations were found, ask:
> "Would you like me to fix any of these violations? If so, which ones?"

Do not attempt fixes without explicit confirmation.

## Important Constraints

- **Read-only** — do not modify any file during fact-check.
- **No false positives** — if you are not confident a match is a genuine violation, put it under a "Possible violations (needs review)" subsection rather than reporting it as confirmed.
- **Scope to source code** — exclude `docs/`, `node_modules/`, `.git/`, and build artifacts from all searches.
- **One rule, one check** — do not merge multiple rules into one search. Each rule produces its own result row.
