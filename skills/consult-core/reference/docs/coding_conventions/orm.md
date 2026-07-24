# Prisma ORM Conventions

> Applies when writing or reviewing any code that touches the Prisma client.

## Query Placement
- All Prisma calls must be in `lib/repositories/`. Never call `prisma.*` from services, API handlers, or components.
- Each repository file covers one Prisma model: `user-repository.ts` → `prisma.user.*`.

## Query Style
- Use `select` to explicitly list returned fields — never return full models unless all fields are needed.
- Use `include` only for relations you will actually use in the calling service.
- For batch operations, prefer `createMany` / `updateMany` over looping single operations.

## Migrations
- Run `prisma migrate dev` locally; CI runs `prisma migrate deploy`.
- Never edit migration files after they have been committed — create a new migration instead.
- Name migrations descriptively: `add_refresh_token_to_users`, not `update1`.

## Transactions
- Use `prisma.$transaction([...])` for multi-step writes that must be atomic.
- Keep transactions short — no external API calls inside a transaction block.
