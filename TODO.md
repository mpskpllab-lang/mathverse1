# MathVerse delivery outcomes

The Parent/Teacher portal, parent/teacher mentoring, family links, teacher-class assignments and parent/teacher reports were explicitly removed by the user. They are excluded from these outcomes. Student-facing MathMentor AI and administrator content management remain in scope.

## 1. Responsive application shell, navigation, and themes — [x]
- Create a professional application shell with MathVerse logo and home navigation; Learn; Practice; Quizzes; Formula Vault; My Progress; MathMentor AI; student profile; and light/dark theme toggle.
- Use a collapsible sidebar on desktop and a compact navigation system on mobile. Every navigation item must lead to a functional page or working feature.
- Use pure white (#FFFFFF) primary background, pure black (#000000) primary text, light gray (#E5E7EB) borders, neutral slate secondary text and blue (#2563EB) sparingly for buttons, selected states, progress indicators and important interactions. Use readable modern sans-serif typography, Lucide icons, clean white card surfaces with subtle borders and restrained shadows, consistent moderately rounded corners, generous whitespace and subtle fast purposeful transitions.
- Avoid unnecessary gradients, excessive colors, distracting backgrounds, oversized illustrations and cluttered dashboards. Make the application responsive across mobile phones, tablets, laptops and desktop monitors.
- Implement optional dark mode using true black (#000000) background, white text and appropriately contrasting borders.

## 2. Student dashboard and curriculum/class selection — [~]
- Create a personalized student dashboard containing welcoming greeting; current class and selected curriculum; Continue Learning; daily learning goal; current learning streak; total XP; overall mathematics mastery; recent chapters; recommended practice activities; achievements and badges; weekly learning activity; and quick access to MathMentor AI.
- Provide curriculum selector for Kerala State Syllabus and CBSE / NCERT. Display ten selectable cards, Class 1 through Class 10, each with class number, brief description and progress when available.
- Changing the curriculum or class must update relevant chapters, topics, activities and progress views.
- Include sensible empty, loading, error and retry states; do not fabricate an authenticated student when signed out.

## 3. Curriculum directory, verification, and modular content — [~]
- Implement Curriculum → Class → Subject → Chapter → Topic → Learning Activities. For each class, display relevant mathematics chapters and topics for the selected curriculum only when verified; otherwise show a clear honest placeholder and administrator import/review path.
- Chapter cards contain chapter number and name, short description, number of topics, progress percentage, status (Not Started, In Progress or Completed) and Continue Learning button. Provide search and filtering by chapter name, topic and progress status.
- Official SCERT Kerala and NCERT/CBSE textbooks and curriculum documents are the source of truth. Do not invent official chapter titles, textbook editions, syllabus alignments or curriculum claims.
- Keep curriculum content in modular structured data/database so administrators can update it without rebuilding the application. Clearly distinguish verified curriculum from sample/demonstration content. Where verified content is unavailable, show an honest placeholder and an administrator workflow for importing and reviewing textbook material. Support textbook editions and academic years.
- Do not hardcode chapter lists or question banks directly into UI components. Use stable IDs and appropriate data relationships.

## 4. Four-stage lesson experience — [~]
- Build an immersive topic-based learning interface with visible four-stage path: LEARN, PRACTICE, APPLY and BOSS CHALLENGE.
- LEARN concept bites explain one mathematical concept at a time with short student-friendly explanations, everyday examples, interactive diagrams where appropriate, step-by-step worked examples, formula cards, small knowledge checks, Previous and Next controls, “Explain More Simply” and “Ask MathMentor AI”. Adapt explanations to selected class/curriculum and avoid long textbook-style paragraphs.
- Support topic-appropriate activities and progress, with clear retry/feedback, accessible notation and appropriate mobile layouts.

## 5. Interactive quiz and practice engine — [~]
- Provide multiple-choice, fill-in-the-blank, true/false, matching, number-based answers and drag-and-drop ordering where suitable.
- After each answer immediately indicate correctness, explain reasoning, offer a hint after an incorrect attempt, allow retry, award XP according to activity rules and update student progress.
- Include question progress indicator, score, streak counter and completion screen. Do not penalize students excessively for mistakes; goal is mastery rather than memorization.

## 6. Interactive Apply sandbox and Boss Challenge — [~]
- Create practical math sandbox activities with real interactive browser controls, SVG/canvas or equivalent—not static images. Support suitable explorations such as geometric shapes and properties, rectangle dimensions/area/perimeter, integers on a number line, fraction comparison bars, balance equations, angles, multiplication/division/ratios/percentages and algebraic steps. Choose activities by current topic and class level; explain what is being explored and provide feedback as values change.
- At chapter end provide a comprehensive Boss Challenge with mixed chapter questions, gradually increasing difficulty, clear score/mastery summary, explanations for incorrect answers, weak-topic review, retry and achievement/XP reward.
- Unlock subsequent chapters according to configurable mastery requirements while always allowing earlier chapters to be revisited. Use encouraging language rather than shame-based messages.

## 7. MathMentor AI tutor — [~]
- Provide MathMentor AI from a non-obstructive floating button and dedicated study panel. Required capabilities: explain a concept simply or with different examples; solve typed mathematics problems step by step; generate practice questions for the current topic; identify common mistakes; give progressive hints before full solutions; explain incorrect answers; adapt to Classes 1–10 and selected curriculum; accept image uploads only when configured AI/provider support permits.
- Tutor is patient, encouraging, clear, age-appropriate and uses correct notation. For problem solving use: understand question; identify relevant concept; show every mathematical step; explain why each step works; present final answer; offer similar practice. Do not immediately reveal solutions when a hint would help.
- Call Anthropic Claude through a JWT-authenticated Supabase Edge Function; use the configurable `ANTHROPIC_MODEL` (default `claude-sonnet-5-5`) and store `ANTHROPIC_API_KEY` only in Supabase Function secrets. Never expose provider keys in browser code. Validate prompt/context size, use rate limits, and send selected class, curriculum, chapter and topic without unnecessary personal data. Provide loading, error, retry and empty states.
- If the Anthropic key is absent, provide clearly labelled demonstration mode with sample responses; do not claim a live AI connection when it is not connected. On provider failures, return a retryable error rather than silently substituting a demo answer. Clearly report live versus demo mode.

## 8. Student profile, progress persistence, and gamification — [~]
- Student profiles include display name, selected class, selected curriculum, learning preferences, current learning goal, total XP, current streak, earned badges, completed chapters, topic mastery, quiz history, learning time and recent activity.
- Record meaningful learning activity and calculate progress using completion/mastery rules, not page visits. Persist across sessions using Supabase Auth accounts and PostgreSQL protected by Row Level Security. Clearly distinguish any local development/demo fallback from production persistence. Do not migrate existing student identities or personal progress by default.
- Achievements include First Lesson Completed, Five-Day Learning Streak, Fraction Explorer, Algebra Beginner, Chapter Master and Perfect Quiz. Prevent duplicate completion rewards unintentionally; track learning dates consistently and handle timezone boundaries.

## 9. Formula Vault, search, and recommendations — [~]
- Build searchable Formula Vault organized by curriculum, class, chapter, topic and mathematical category. Include appropriate verified formulas for arithmetic, fractions, percentages, geometry, mensuration, algebra, statistics, probability and other relevant topics; each entry includes formula name, expression, symbol meanings, when to use it, worked example and relevant class/curriculum. Use accessible mathematical notation. Do not claim unverified formula or syllabus mappings are official textbook content.
- Add global search across chapters, topics, formulas and practice activities.
- Provide explainable contextual recommendations: continue previous lesson; revise a topic with low mastery; try a recommended practice set; review a recently completed chapter.

## 10. Administrator content management — [~]
- Authorized administrators can create/edit classes, chapters and topics; associate content with curriculum and textbook edition; add explanations and worked examples; create quizzes and answer explanations; configure sandbox activities and chapter mastery requirements; publish, unpublish and revise content; track content verification; import structured curriculum data; review AI-generated content before publishing.
- Use role-based access control enforced server-side, not just hidden frontend controls. Record important administrative changes. Students must never access administrator functions by modifying frontend URLs.

## 11. Data model, authorization, and backend integration — [~]
- Use modular application architecture for authentication/profiles, curriculum management, learning engine, quiz engine, sandbox activities, AI tutor, progress tracking, formula library, administration, theme and accessibility.
- Replace the application backend in source with Supabase Auth, normalized Supabase PostgreSQL, explicit table grants and Row Level Security, and Supabase Edge Functions. Use Anthropic Claude for MathMentor through a JWT-authenticated Edge Function. Keep Express only for website/Preview hosting, safe public configuration and health. Remove the disconnected Manus OAuth, application tRPC, MySQL/Drizzle source modules, dependencies and migration commands without dropping, altering or migrating the already-provisioned shared MySQL database.
- Validate all input and protect endpoints. Student profile/progress writes must be scoped to the authenticated user. Admin reads/writes, answer-key access, quiz correctness, reward calculation and curriculum publishing must be enforced in Edge Functions; never trust client-supplied XP or self-editable admin metadata. Keep Anthropic and service-role keys server-only, with secure error handling and abuse prevention.
- Design normalized stable-ID relations for users/roles, student profiles, curriculum definitions, textbook editions, classes, chapters, topics, lessons, questions/answer choices and private answer keys, quiz attempts, topic mastery, chapter progress, learning sessions, XP transactions, achievements, formula entries, AI conversation records, and content review/publication status. Include RLS tests for anonymous/student/admin access. Parent-student and teacher-class records remain out of scope. No remote Supabase project, credential, migration, deployment or website publication is authorized in this code-only task; show setup-required/demo states and do not claim live authentication, persistence or AI. Do not migrate personal identity or progress data without separate explicit approval.

## 12. Accessibility, child privacy, and safety — [~]
- Support keyboard navigation, clear focus indicators, screen-reader-friendly controls, sufficient text contrast, readable small-screen text, reduced-motion preferences and clear mathematical notation.
- Ensure age-appropriate AI responses, minimal personal information, appropriate account protections for children, no public display of children's personal learning data and no public identity-exposing student leaderboards by default.
- Include privacy-conscious retention settings and suitable consent workflows for intended age groups and deployment jurisdiction; do not claim legal compliance without review.

## 13. Required application pages and usable states — [x]
- Provide Home/Landing; Sign Up and Sign In; Student Onboarding; Student Dashboard; Curriculum and Class Selection; Chapter Directory; Lesson Learning Interface; Interactive Quiz; Math Sandbox; Boss Challenge; MathMentor AI; Formula Vault; Progress and Achievements; Student Settings; Administrator Dashboard; Privacy Policy and Terms pages.
- Use sensible empty states, loading states and error/retry states. The Parent/Teacher Dashboard was explicitly removed by the user and must not be built.
