# Consult

A plugin for AI coding assistants that creates and maintains a clean, consistent AI instruction structure for your project.
This plugin can initialize AI instruction files **from scratch**, or **migrate existing ones**.

## The Structure

All AI instruction lives in one hierarchy, defined in `consult-core` to orient all skills:

```
<Project Path>
├── README.md                           ← Human-only: project overview, setup instructions
├── AGENTS.md                           ← Single source of truth for AI agents
├── docs/
│   ├── architecture.md                 ← Tech stack, layers, dependency rules
│   ├── coding_conventions.md           ← Global coding conventions (always loaded)
│   ├── coding_conventions/             ← Optional: tool-specific conventions (on demand)
│   │   ├── orm.md
│   │   └── ...
│   └── features/                       ← Optional: VIP feature rules (on demand)
│       ├── auth.md
│       └── ...
└── <Thin pointer files>                ← CLAUDE.md / GEMINI.md / .cursorrules / etc.
```

> **`docs/` collision note:** If your project already has a `docs/` folder with human documentation, `/consult:init` will detect this and ask before adding AI files alongside them. Existing files are never touched.


## Commands

### `/consult:init`

Initialize the project's AI instruction structure.
- Scans for existing instruction files (`CLAUDE.md`, `.cursorrules`, `.cursor/rules/`, etc.)
- Detects conflicts between existing files and asks for resolution
- Merges all discovered content into `AGENTS.md`, routing each piece to the right file (`docs/architecture.md`, `docs/coding_conventions.md`, `docs/features/<feature>.md`, etc.)
- Scaffolds empty starter files for any missing structure
- Writes pointer files for all known AI harnesses

### `/consult:add-to [knowledge]`

- Analyzes the content to classify it: architecture decision, coding convention, tool-specific convention, feature rule, or AI agent rule
- Shows a preview of exactly where it would land (e.g., `docs/features/auth.md → ## Session Handling`)
- Asks for confirmation before writing, creating new files if necessary

### `/consult:fact-check`

Validates that the project's code actually follows the rules defined in `AGENTS.md` and `docs/`.
- Extracts all mechanically verifiable rules from every instruction file
- Searches the codebase for violations using greps and file pattern checks
- Reports: rules followed, violations with file+line references, and rules that require human review

## Passive Skill: `consult-core`

The `consult-core` defines the core structure and rules for how AI instructions are organized in this project. It will only be referenced by other skills, never directly by the user.


## Installation

This plugin is designed for AI harnesses that support the Claude plugin format. Place the plugin in your AI harness's plugin directory to activate it.

## Development

To develop and test the plugin against another project without running through the whole installation process, symlink this repo into that project's plugin directory:

```bash
# Inside the target project
## make the symlink
mkdir -p .claude/plugins
ln -s /path/to/consult .claude/plugins/consult

## activate claude code specificatintelg the plugin in the session
claude claude --plugin-dir .claude/plugins/consult
```

## TODO
[] build a sample to show case the usage of this plugin. and add sample as reference in the consult-core spec.