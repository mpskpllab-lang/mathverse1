# MathVerse implementation plan

## Product approach

Build MathVerse as an interactive mathematics-learning application, not a static marketing page. Keep the existing React 19, TypeScript, Vite and Wouter frontend. Replace the application data/auth/AI backend in source with Supabase Auth, PostgreSQL, Row Level Security (RLS) and Edge Functions; use Anthropic Claude for MathMentor. Do not introduce another frontend framework or change the agreed MathVerse visual direction.

### Scope and boundaries

Exclude the Parent/Teacher portal, parent/teacher mentoring, family links, teacher-class assignments and parent/teacher reports. Keep the student experience, student-facing MathMentor AI and administrator content workspace. Preserve dashboard, Learn, Practice, Quizzes, Apply sandbox, Boss Challenge, Formula Vault, Progress, student settings, admin, landing/auth, privacy and terms pages. Curriculum/class selectors cover Kerala State and CBSE/NCERT, Classes 1–10. Until source material has been reviewed, official chapter content remains clearly unverified and sample lessons remain labeled as demonstrations.

## Replacement backend and code-only boundary

Use Supabase Auth with verified email/password sign-up/sign-in, password reset and authenticated sessions. Do not create an open admin signup path. Store admin privileges separately from user-editable metadata and check them in Edge Functions; create no parent/teacher roles or family records. Minimize child data, keep progress private, and use configurable consent/privacy copy without claiming jurisdiction-specific legal compliance.

Use Supabase PostgreSQL for normalized application data, with versioned SQL migrations under `supabase/migrations/`. Enforce least-privilege grants and RLS policies for every exposed table. Students may read/change only their own profile and progress. Signed-out readers receive only reviewed, published curriculum/formula data. Quiz answer keys are not exposed to browser clients. Service-role-only Postgres RPCs commit each quiz attempt with derived rewards/mastery/progress, record minute sessions with the consecutive-day streak check, and commit admin content materialization/review/audit/publication transitions in single transactions. Question XP is once per user/question; quiz attempts carry a run ID, and Perfect Quiz requires every eligible question in the topic set to be correct in that run with no wrong attempt. Topic mastery stores the floored ratio of distinct correct eligible questions to the actual eligible question count, so an incomplete set cannot round to 100%; chapter completion and Chapter Master require every topic at 100%. The RPCs are idempotent where appropriate and recheck server-side authorization; user-scoped Edge Functions still verify sessions and validate inputs.

Use Supabase Edge Functions (Deno TypeScript) for MathMentor requests, answer submission/reward validation, minute-session recording and admin content review/publishing. User-facing functions validate Supabase sessions and use RLS-scoped access for student requests. Privileged access stays server-side and follows explicit role checks. The browser receives only the project URL and publishable key; it never receives a service-role/secret key or Anthropic API key.

Use Anthropic's Messages API for MathMentor, defaulting to the documented `claude-sonnet-5-5` model while keeping the model ID configurable. The Edge Function applies age-appropriate instructions, selected class/curriculum/topic context, bounded input/output and rate limits; it must not send student identity or unnecessary profile data. Prompt/answer retention is disabled by default. If `ANTHROPIC_API_KEY` is absent, report demo mode; provider failures return retryable errors rather than silently substituting a demo answer. Image questions remain disabled until configured and validated.

The active MathVerse client uses a Supabase browser client/data adapter and displays demo or setup-required states when project settings are absent. Express serves only the website, safe runtime config and unauthenticated health endpoint; it does not mount application tRPC or OAuth routes. Retire unused Manus OAuth, tRPC, Drizzle and MySQL application modules, their dependencies and migration commands from the website code. This code cleanup does not drop, alter or migrate data from the already-provisioned shared MySQL database; its platform capability may remain enabled but is no longer used by the app. No personal identity or progress migration is authorized; existing users create new Supabase accounts unless the owner separately approves migration.

The user approved code-only preparation and explicitly did not authorize remote project connection, credential use, migrations against a remote service, deployment or publication. Therefore the Supabase project is not connected. Do not claim a live replacement: account/data/AI functions require a separately authorized Supabase project, public URL/publishable key and Anthropic Edge Function secret before they can work outside demo/setup mode. Never request credentials in chat or store them in source.

## Architecture and project structure

- `client/src/App.tsx`: explicit user-facing routes; preserve the `/404` dashboard alias and synchronized route manifest.
- `client/src/pages/`: student dashboard, curriculum, lesson, practice, quiz, sandbox, challenge, tutor, formulas, progress, settings, admin, landing/auth, privacy and terms views.
- `client/src/components/`: shared shell, navigation, accessible controls, tutor panel and activity widgets.
- `client/src/lib/supabase.ts`: browser-safe Supabase client initialized from public runtime settings.
- `client/src/lib/mathverse-api.ts`: typed adapters for profile/progress, curriculum, quiz answers, formulas, admin review and tutor functions; keep presentation separate from persistence.
- `server/_core/index.ts` and `server/_core/vite.ts`: website hosting, Preview handling, safe runtime config and `/api/health` only.
- `supabase/config.toml`, `supabase/migrations/`: project/function settings, normalized schema, explicit grants/RLS, indexes, safe triggers and demo-only seed data.
- `supabase/functions/mathmentor-ask/`, `submit-answer/`, `record-session/`, `admin-content/`: authenticated tutor, answer/reward, session and admin workflows.
- `supabase/tests/`: SQL/RLS cases for anonymous, student and service-role access expectations.
- `client/public/manus-routes.json`: complete manifest of real browser routes.

## Design system

- **Movement:** restrained Swiss information design with a modern learning-workbench feel: clear hierarchy, generous whitespace and precise mathematical controls.
- **Core principles:** make each next step obvious; teach in small, confidence-building increments; give immediate, kind feedback; make progress and content provenance trustworthy.
- **Color philosophy:** white and black carry the lesson content; neutral grays organize secondary information; blue `#2563EB` is reserved for actions, selection and progress. Dark mode uses true black, white text and contrasting borders.
- **Layout paradigm:** a collapsible desktop sidebar and compact mobile navigation frame spacious, task-specific work surfaces. Lesson stages and progress stay visible without crowding practice controls.
- **Signature elements:** the MathVerse orbital-point mark; a four-stage Learn/Practice/Apply/Boss tracker; interactive number lines, fraction bars and geometric controls instead of decorative textbook art.
- **Interaction and animation:** keyboard-friendly controls with clear focus; feedback appears promptly and remains encouraging. Use subtle fast transitions for state changes, avoid motion that delays learning, and honor reduced-motion preferences.
- **Typography:** modern sans-serif such as Inter with a system fallback; compact hierarchy for labels, comfortable line length for explanations, and clear accessible math notation.
- **Brand essence:** a calm, step-by-step mathematics studio for Classes 1–10, distinguished by interactive practice and visible mastery; personality: clear, encouraging, dependable.
- **Brand voice:** concise and kind, never shaming. Examples: “Let’s make the pieces the same size first.” “You’re close—try one smaller step.”
- **Wordmark/logo:** preserve the existing MathVerse geometry-orbit symbol and wordmark, not platform branding. Signature brand color: MathVerse blue `#2563EB`.

## Implementation and validation workflow

Maintain registered TypeScript diagnostics and use the pinned pnpm policy. Remove only disconnected legacy application source/dependencies; do not issue schema drops or modify the managed MySQL data. The SQL migration/RLS and Edge Functions are source scaffolding only: the approved code-only scope has no Supabase project, CLI, Deno, Docker, psql or credentials, so functions and RLS tests have not been compiled or executed. Validate the frontend/website code with available TypeScript, unit-test, build and Preview HTTP checks; describe external Supabase behavior as unverified until a separately approved project is configured. Keep Preview available and do not publish the website unless explicitly asked.

## Provider contract references

- [Anthropic Messages API](https://platform.claude.com/docs/en/api/messages) and [Models API](https://platform.claude.com/docs/en/api/models/list): model selection and message contract.
- [Supabase React Auth quickstart](https://supabase.com/docs/guides/auth/quickstarts/react): browser client and session setup.
- [Supabase Edge Functions](https://supabase.com/docs/guides/functions), [function authentication](https://supabase.com/docs/guides/functions/auth), and [function secrets](https://supabase.com/docs/guides/functions/secrets): Deno runtime, JWT-scoped access and server-side secrets.
- [Supabase RLS guide](https://supabase.com/docs/guides/database/postgres/row-level-security): grants, policies and isolation.
- [Supabase database migrations](https://supabase.com/docs/guides/deployment/database-migrations): versioned schema workflow.
