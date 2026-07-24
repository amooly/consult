# Architecture

## Tech Stack
- **Runtime:** Node.js 20 (LTS)
- **Framework:** Next.js 14 (App Router)
- **Database:** PostgreSQL 15 via Prisma ORM
- **Auth:** NextAuth.js v5
- **Styling:** Tailwind CSS
- **Deployment:** Vercel (frontend + API routes), Supabase (managed Postgres)

## Server Environment and Ports

The application communicates across the following servers and ports in local and production environments:

### Local Development Flow
- **Next.js Dev Server:** Runs on port `3000` (`http://localhost:3000`)
- **PostgreSQL Database:** Runs on port `5432` (`postgresql://localhost:5432`)

### Production Flow
- **Next.js (Vercel):** Serves clients over standard HTTPS port `443`
- **PostgreSQL (Supabase):** Accessible on port `5432` (direct) or port `6543` (connection pooling)

```
  Client (Browser)
       |
       |-- HTTP (:3000) -----------------> +---------------------------+
       |                                   |   Local Dev Environment   |
       |                                   |                           |
       |                                   |  Next.js Server           |
       |                                   |  (port 3000)              |
       |                                   |       |                   |
       |                                   |       | Prisma (:5432)    |
       |                                   |       v                   |
       |                                   |  Postgres DB              |
       |                                   |  (port 5432)              |
       |                                   +---------------------------+
       |
       |-- HTTPS (:443) ----------------> +---------------------------+
                                          |  Production Environment   |
                                          |                           |
                                          |  Vercel / Next.js         |
                                          |  (port 443, HTTPS)        |
                                          |       |                   |
                                          |       | Prisma            |
                                          |       | (:5432 / :6543)   |
                                          |       v                   |
                                          |  Supabase Postgres        |
                                          |  (port 5432 direct,       |
                                          |   port 6543 pooler)       |
                                          +---------------------------+
```

## System Layers
- **`app/`** — Next.js App Router pages, layouts, and loading states. No business logic.
- **`app/api/`** — API route handlers. Input validation only; delegate to service layer.
- **`lib/services/`** — Business logic. Calls repository layer; never calls Prisma directly.
- **`lib/repositories/`** — Database access. All Prisma calls go here.
- **`lib/types/`** — Shared TypeScript types and Zod schemas.

## Dependency Rules
- Pages → Services (via server actions or API routes)
- API handlers → Services
- Services → Repositories
- Repositories → Prisma client
- **Forbidden:** Pages or API handlers calling Prisma directly.
- **Forbidden:** Repositories containing business logic.

## Key Design Decisions
- App Router chosen over Pages Router for native streaming and server component support.
- Prisma as ORM for type-safe queries and migration tooling; raw SQL only for complex reporting queries.
- All external API calls go through `lib/integrations/` — never inline in components or services.
