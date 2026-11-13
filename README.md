# MathVerse — Learn Mathematics. Master Every Step.

A responsive mathematics-learning web app for Classes 1–10. The interface keeps the existing React 19, TypeScript, Vite and Wouter stack. The Express process serves the site, runtime-safe public configuration and health endpoint; it is not the new student-data API.

## Commands

- `pnpm dev`: start the Preview development server on `PORT` (default `3000`).
- `pnpm build` and `pnpm start`: build and serve the website.
- `pnpm check`: run TypeScript checks.
- `pnpm test`: run available application tests.
- `pnpm dev:static` and `pnpm build:static`: use the Vite-only options.

## Supabase backend status

The Supabase project code is in `supabase/`, including fresh-project PostgreSQL migrations, Row Level Security policies, transaction-safe RPCs and Edge Functions. It is an offline scaffold only: no Supabase project is linked, and migrations/functions/RLS tests have not been run or deployed. See [`supabase/README.md`](supabase/README.md) for the setup boundary and security notes.

When an authorized dedicated Supabase project is connected, configure the browser-safe project URL and publishable key as runtime settings (`SUPABASE_URL` and `SUPABASE_PUBLISHABLE_KEY`; `SUPABASE_ANON_KEY` is accepted as a compatibility fallback). Never put a Supabase secret/service-role key or `ANTHROPIC_API_KEY` in browser code. Store the Anthropic key in Supabase Edge Function secrets. Administrator access must be assigned out-of-band in `public.user_roles`; there is no open admin signup.

Without Supabase configuration, visitors can use the clearly identified device-local demonstration workspace. Sign-in and account creation are intentionally unavailable until an approved project is configured. The live MathMentor mode also requires `ANTHROPIC_API_KEY`; without it, the authenticated tutor function returns labeled demonstration replies.

## Administrator content imports

Only source-reviewed, verified and explicitly published content is visible as official student curriculum. Do not invent chapter titles or claim alignment to SCERT Kerala or NCERT/CBSE. An authorized administrator can import a JSON object containing nonempty `chapters` and `topics`, with optional `lessons` and `questions`; each imported question needs an `id`, `prompt`, and `answer`, and optional `question_type`, `hint`, `explanation`, and `choices` (`label` and `text`). The admin flow requires an HTTPS source URL and reviewer note before verification and a separate explicit publish action. Answer keys remain server-only.

## Data and scope

No existing identities or student progress are migrated. The previously provisioned Manus-managed MySQL database and its records are left untouched; this code no longer relies on that database for student-facing routes. Parent/teacher mentoring, family links, class assignments and parent/teacher reports remain excluded per the product owner's direction. Child-safety and consent copy is configurable and does not claim jurisdiction-specific legal compliance.
