Run a full, read-only security audit of this project and tell me how many security vulnerabilities it has, rated by severity.

PROJECT (detect automatically, do not ask me)
- Code location: the current working directory. If it contains several apps (e.g. frontend/ and backend/ folders, or a monorepo), audit all of them. If a sibling repo is clearly part of the same system (referenced in config, README or API URLs), mention it but audit only what is inside this folder.
- Output folder: <your home folder>/security-audit-reports/<this project's folder name>/run-<N>, outside the project so the repo is never modified. N is one more than the highest existing run folder there (start at 1). Create the folders if missing. Never write reports inside a git repository.
- What the app handles: work it out from the README, data models, database migrations and integrations (e.g. health records, payments, personal data, authentication only, internal tool). State your conclusion at the top of REPORT.md and use it to rate impact.

HARD RULES
1. READ-ONLY: never modify, delete or commit anything in the project.
2. Never touch production: no requests to deployed URLs, live databases, real user data or real third-party accounts.
3. Never print secret values (at most the first 4 characters). Never use any credential you find.
4. Only execute code (tests, builds, proof-of-concept) inside an isolated, offline sandbox (e.g. Docker with no network and fake data). If none is available, do not execute; mark those leads "needs validation".
5. No network calls, except reading public vulnerability advisories for dependency versions.

STEP 1: UNDERSTAND THE PROJECT
Detect the languages, frameworks, entry points, how authentication works, data stores, outside services, deployment config and CI. Write attack-surface.md with:
- every external entry point (HTTP routes, GraphQL, WebSocket, queues, CLI, scheduled jobs, webhooks), with the access rule for each;
- where sensitive data is stored and where it is sent.

STEP 2: HUNT. Check every class below that applies to this stack; skip classes that cannot apply and say why:
- Authentication: login, password reset, OTP/MFA, social sign-in and account linking, session and token handling, logout and revocation
- Authorization: missing role checks, IDOR (accessing another user's object by changing an id), privilege escalation, multi-tenant leaks
- Injection: SQL/NoSQL/ORM, command, template, path traversal, SSRF, XXE, deserialization
- Client side: XSS (including unsafe HTML rendering and sanitizer bypasses), CSRF, open redirects, clickjacking, CORS misconfiguration
- Sensitive data exposure: API responses, logs, error messages, caches, exports, emails
- Secrets: hard-coded keys, committed .env files, keys in git history, secrets in build artifacts or images
- Cryptography: weak algorithms or modes, hard-coded keys or IVs, weak password hashing, missing TLS assumptions
- Business logic: price or quantity tampering, payment verification, race conditions, double spending, state-machine skips
- Abuse and availability: missing rate limits, unbounded queries or uploads, resource exhaustion
- Files: upload validation, content-type trust, storage permissions, download authorization
- AI/LLM features (if any): prompt injection, tools that can act without confirmation, data sent to model providers, cost or quota abuse
- Dependencies: versions with known vulnerabilities in every manifest (e.g. pom.xml, package.json, requirements.txt, go.mod)
- Configuration: security headers, debug or admin endpoints exposed, default credentials, permissive cloud or container settings, CI secrets
- Data retention and privacy: deletion, retention, consent, audit-trail gaps (if the app handles personal data)

STEP 3: VERIFY. A finding counts only if you have:
- traced it from entry point to impact;
- checked that no guard elsewhere already blocks it;
- where possible, proved it with a minimal test in the offline sandbox.

Anything plausible but unproven goes to "Needs validation", not to the confirmed list. Do not pad the report.

STEP 4: RATE. Rate each confirmed finding Critical / High / Medium / Low by:
- the real impact (data exposed, money, account takeover, availability);
- how easy it is to exploit (unauthenticated vs. authenticated, user interaction, preconditions).

STEP 5: REGRESSION. If a previous run exists in the output folder, re-check each of its confirmed findings and report whether it is fixed, still open, or back again.

OUTPUT (in the run folder)
REPORT.md, containing:
1. A summary table: count of confirmed findings by severity, plus new / still open / fixed versus the last run.
2. Urgent actions for the owner, things code cannot fix (e.g. rotate a leaked key).
3. Each confirmed finding, with:
   - ID, severity and title;
   - file:line;
   - root cause;
   - step-by-step exploit scenario;
   - impact;
   - recommended fix;
   - how it was verified.
4. "Needs validation": each unproven lead, and what would confirm it.
5. The regression results (if there was a previous run).

Plus attack-surface.md from step 1.

Finish by telling me the total number of vulnerabilities by severity, the top 3 to fix first, and the path to REPORT.md.
