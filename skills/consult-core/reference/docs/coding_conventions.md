# Coding Conventions

> These conventions apply to ALL code in this project. Tool-specific conventions are in `docs/coding_conventions/`.

## Naming
- Files and directories: `kebab-case` (e.g., `user-profile.ts`, `auth-service.ts`)
- React components: `PascalCase` (file and export name must match)
- Functions and variables: `camelCase`
- Types and interfaces: `PascalCase`, no `I` prefix on interfaces
- Constants: `SCREAMING_SNAKE_CASE` for module-level constants; `camelCase` for local constants

## Formatting
- 2-space indentation, no tabs
- Single quotes for strings; template literals for interpolation
- Max line length: 100 characters
- Trailing commas in multi-line arrays and objects

## Patterns
- Prefer `async/await` over promise chains.
- Prefer named exports over default exports (exception: Next.js page and layout files require default exports).
- Use Zod for all external input validation; derive TypeScript types from Zod schemas with `z.infer<>`.
- Do not use `any`. Use `unknown` and narrow explicitly.

## Error Handling
- Services throw typed errors (extend a base `AppError` class in `lib/errors.ts`).
- API handlers catch typed errors and map them to HTTP status codes.
- Never `console.log` in production code; use the structured logger at `lib/logger.ts`.

## Testing
- Test files live next to source: `user-service.test.ts` alongside `user-service.ts`.
- Unit tests for services and repositories; integration tests for API routes.
- Use `vitest` for all tests; `@testing-library/react` for component tests.
- Prefix test descriptions with the function name: `describe('createUser', () => { ... })`.

## Tool-Specific Conventions
- [Prisma ORM](coding_conventions/orm.md)
