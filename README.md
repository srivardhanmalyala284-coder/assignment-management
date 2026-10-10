# assignment-management
# SUBMITRA

SUBMITRA is a calm, accountable assignment workspace for students and teachers. It keeps the assignment brief, task plan, submission pipeline, server receipt, and published feedback together.

## Run locally

```bash
pnpm install
cp .env.example .env
pnpm dev
```

Open `http://localhost:3000`. The project builds the React UI and serves it with the Node API from one stable process; the preview intentionally does not depend on a Vite HMR WebSocket. `pnpm start` runs the production-shaped server against `dist/` after a build.

The managed preview health endpoint is `/api/health`. The route declaration is `/manus-routes.json`.

## Demo access

Demo access is controlled by `DEMO_MODE=true` and is visible in the UI. The seeded records carry `isDemo=true` and the UI labels them **Demo data**.

| Role | Email | Password |
|---|---|---|
| Student | `student@submitra.demo` | `student123` |
| Teacher | `teacher@submitra.demo` | `teacher123` |

Use `pnpm seed` to reset the local JSON store to the documented demo data. That command is intentionally a destructive local reset; never run it against a shared production store.

## Environment

See `.env.example` for `PORT`, `DEMO_MODE`, `DEFAULT_TIMEZONE`, upload limits, receipt HMAC secret, storage driver, managed database placeholder, AI placeholder, privacy threshold, and virus scan flag. The current empty-project fallback uses local JSON persistence and local private upload files; this limitation is documented in `docs/LIMITATIONS.md` rather than being presented as a production database or object store.

## Architecture

- `src/main.tsx` contains the responsive React workspace, accessible UI primitives, role-aware navigation, student assignment flow, receipt, teacher table/review, feedback, settings, and development design-system page.
- `src/styles.css` contains semantic CSS-variable tokens, separate light/dark themes, responsive breakpoints, reduced-motion rules, focus treatment, and print layout.
- `server/index.ts` contains server-side auth checks, typed JSON endpoints, course/assignment mutations, task progress, pre-checks, submission state transitions, checksum persistence, receipt HMAC, teacher grading, and published-feedback visibility.
- `server/store.ts` defines the typed data model, durable JSON adapter, password hashing helper, and labeled seed records.
- `server/security.ts` defines HTTP-only session cookies, resource-role checks, HMAC, filename sanitization, and typed error responses.
- `lib/metrics/registry.ts` is the single source of truth for displayed Priority 1 metrics.
- `docs/` records the design system, metric definitions, security controls, AI disclosure, limitations, and the test report.

## Submission trust model

A student confirmation creates an idempotent attempt. The server checks enrollment, assignment state, file count, allowed type, size, duplicate names, and PDF magic bytes; then writes the file, computes SHA-256, persists the submission/files/receipt, and only then returns `submitted`. The UI never shows **Submitted** from local state alone. Failed attempts are kept as failed attempts and do not receive a receipt. Receipt verification is available at `/verify/<reference>` for the owner or owning teacher.

## Priority status

Priority 1 is the implemented demo-ready core: controlled demo auth, courses and assignments, student workspace and tasks, server-confirmed submission/receipt, teacher review/grading, published feedback, responsive themes, documented authorization, private student-teacher doubt threads, and server-side Study Assist with an honest fallback. Deeper text extraction, deadline-risk inputs, improvement recommendations, and Confusion Radar remain labeled as not yet implemented. See `docs/LIMITATIONS.md`.
