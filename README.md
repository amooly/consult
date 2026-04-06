# Consult

A plugin for AI coding assistants that creates and maintains a clean, consistent AI instruction structure for your project.

## The Design

All AI instruction lives in one hierarchy:

```
AGENTS.md                         ← Single source of truth for AI agents
├── docs/architecture.md          ← Tech stack, layers, dependency rules
├── docs/coding_conventions.md    ← Global coding conventions (always loaded)
│   └── docs/coding_conventions/  ← Tool-specific conventions (e.g. orm.md)
└── README.md                     ← Human-only. Not AI instructions.

CLAUDE.md / GEMINI.md / .cursorrules / etc.  ← Thin pointer files → AGENTS.md
```

- **`AGENTS.md`** is the only file AI agents should trust as ground truth.
- **`docs/`** holds structured knowledge readable by both humans and AI.
- **`README.md`** is for humans only — AI agents do not read it as instruction.
- Provider files (`CLAUDE.md`, `GEMINI.md`, etc.) are auto-generated pointers. Do not add content to them.

## Commands

### `/consult:init`

Initialize the project's AI instruction structure.

- Scans for existing instruction files (`CLAUDE.md`, `.cursorrules`, etc.)
- Merges all discovered content into `AGENTS.md`, routing each piece to the right place
- Scaffolds `docs/architecture.md`, `docs/coding_conventions.md`, and `docs/coding_conventions/`
- Writes pointer files for all known AI harnesses
- Always asks for confirmation before writing

### `/consult:add-to [knowledge]`

Add a new piece of knowledge to the right file — without hunting for where it belongs.

```
/consult:add-to Always use the repository pattern for database access
/consult:add-to The API layer must never import from the data layer directly
/consult:add-to   ← (no argument: analyzes the current conversation for learnings)
```

- Routes the knowledge to the correct file and section automatically
- Shows a diff-style preview before writing
- Always asks for confirmation

### `/consult:fact-check` *(coming soon)*

Validate that the project's code actually follows the rules in `AGENTS.md` and `docs/`.

## Passive Skill: `consult-core`

The `consult-core` skill is loaded automatically and orients every AI agent to the instruction hierarchy described above. It tells the AI where to look, what to trust, and how to contribute back.

## Installation

This plugin is designed for AI harnesses that support the Claude plugin format. Place the plugin in your AI harness's plugin directory to activate it.