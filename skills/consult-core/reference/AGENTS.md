<!-- Managed by consult. Edit directly or use /consult:add-to to add new knowledge. -->
# Project Instructions

> This file is the single source of truth for AI agents working in this project.
> Human-readable documentation lives in `docs/` and is referenced below.

## Architecture
→ See [docs/architecture.md](docs/architecture.md) for tech stack, server ports, layers, and dependency rules.

## Coding Conventions
→ See [docs/coding_conventions.md](docs/coding_conventions.md) for global coding patterns and style rules.

## Features
→ See [docs/features/auth.md](docs/features/auth.md) for authentication-specific rules.

## Rules
- Never expose raw database errors to the client — always map to typed API error responses.
- Use `/consult:add-to` to record new decisions; do not silently remember project-specific knowledge.
- When adding a new API route, check `docs/architecture.md` for the layer it belongs to before writing code.
