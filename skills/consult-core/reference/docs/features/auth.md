# Feature: Authentication

→ Implementation uses NextAuth.js v5 (`lib/auth.ts`).

## Session Handling
- Sessions are stored in the database (Prisma adapter), not JWTs, so they can be revoked server-side.
- Access `session.user` via `auth()` in server components; use the `useSession()` hook in client components.
- Never pass the full session object as a prop — pass only the fields the component needs.

## Route Protection
- Protect routes via `middleware.ts` using NextAuth's `auth` middleware helper.
- API routes that require authentication must call `auth()` at the top and return 401 if no session.
- Public routes must be explicitly listed in `middleware.ts`'s `publicRoutes` array.

## Roles and Permissions
- User roles are stored in `users.role` (enum: `USER`, `ADMIN`).
- Check roles in middleware for route-level protection; check again in services for data-level protection.
- Never rely on client-side role checks alone — always enforce server-side.

## Rules
- Never store tokens or credentials in `localStorage` — session cookies only.
- Never skip the server-side auth check on the assumption that middleware already blocked access.
- Password resets expire after 1 hour; enforce this in `auth-service.ts`, not just in the email template.
