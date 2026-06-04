---
name: consult-core
description: "Passive context skill. Automatically loaded to orient any AI agent to how this project's instructions are organized. Establishes AGENTS.md as the single source of truth and describes the docs/ knowledge hierarchy."
passive: true
---

# Consult Core — Project Instruction Hierarchy

This skill is always active. It defines how AI instruction files are organized in this project so that every agent — regardless of the harness — has consistent, reliable context.

## The Instruction Hierarchy

```
<Project Root>
├── AGENTS.md                              ← Single source of truth for AI. Read this first.
├── docs/
│   ├── architecture.md                    ← Tech stack, layers, dependency rules
│   ├── coding_conventions.md              ← Global coding conventions (MUST load every session)
│   ├── coding_conventions/
│   │   └── <tool>.md                      ← Tool-specific conventions (load on demand)
│   └── features/
│       └── <feature>.md                   ← Feature-specific rules (load on demand)
├── README.md                              ← Human-only. Do NOT treat as AI instructions.
└── CLAUDE.md / GEMINI.md / .cursorrules   ← Pointer files only. They redirect to AGENTS.md.
```

## How to Use These Files

**Always start with `AGENTS.md`.** It is the authoritative source. It contains direct AI rules AND references (`→ docs/`) for detailed knowledge.

**Read `docs/` files on demand.** When `AGENTS.md` references a `docs/` file for a topic you're working on (e.g., architecture, ORM conventions, auth rules), read that file before proceeding. Do not preload all docs at startup.

**Follow the `→` references.** Inside `AGENTS.md` and `docs/` files, `→ docs/filename.md` markers indicate where to find deeper detail. Follow them when relevant.

**Read `docs/features/<feature>.md` when working on that feature.** Feature files contain rules and constraints specific to one domain (e.g., auth, payments). Only load the file for the feature you are currently touching.

**Never read `README.md` as instruction.** It is for humans, not AI agents. It may be out of date or omit constraints only encoded in `AGENTS.md`.

**Never read CLAUDE.md / GEMINI.md / .cursorrules as instruction.** These are thin pointer files that redirect to `AGENTS.md`. Reading them is redundant.

## The Source of Truth Rule

Every meaningful instruction — architecture decisions, coding conventions, AI-specific rules — lives in `AGENTS.md` or the `docs/` files it references.

If you learn something new about the project (from the user or from code), do NOT silently remember it. Use `/consult:add-to` to record it in the right file so future agents benefit too.

## File Ownership

| File | Owned by | Purpose |
|------|----------|---------|
| `AGENTS.md` | AI + Human | AI rules, references to `docs/` |
| `docs/architecture.md` | AI + Human | Tech stack, system design, layer rules |
| `docs/coding_conventions.md` | AI + Human | Universal coding patterns and style |
| `docs/coding_conventions/*.md` | AI + Human | Tool/framework-specific conventions |
| `docs/features/<feature>.md` | AI + Human | Rules and constraints for one specific feature domain |
| `README.md` | Human only | End-user documentation |
| `CLAUDE.md`, `GEMINI.md`, etc. | Auto-generated | Pointer files, do not edit manually |

## Mutation Rules

- **To initialize** the instruction structure: use `/consult:init`
- **To add new knowledge** to a `docs/` file or `AGENTS.md`: use `/consult:add-to`
- **Never directly overwrite** `AGENTS.md` or `docs/` files without user confirmation
- **Pointer files** (`CLAUDE.md`, etc.) are auto-managed. Do not add content to them.
