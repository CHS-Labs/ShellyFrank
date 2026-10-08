# Shelly Frank

**AI Systems Architect · Co-founder, [Creative House Studios](https://creativehouse.app)**

[![Location](https://img.shields.io/badge/Saskatoon,%20SK-Canada-blue?style=flat-square)](https://shellyfrank.com)
[![LinkedIn](https://img.shields.io/badge/LinkedIn-shellyfrank-0A66C2?style=flat-square&logo=linkedin)](https://www.linkedin.com/in/shellyfrank)
[![Website](https://img.shields.io/badge/Web-shellyfrank.com-black?style=flat-square&logo=safari)](https://shellyfrank.com)

I design and run AI-native operating systems: the agents, guardrails, data spines and automations that let a very small team run a media company, ship client products, and keep humans in charge of every decision that matters.

I work as a **conductor**. I set the architecture, the contracts and the approval gates. Parallel Claude Code agents do the build work inside those boundaries. Most of my repositories are private client or company systems, so this page describes the work rather than linking to it.

---

## How I build

- **Agents carry role, projects carry truth.** Every project has a `CLAUDE.md` / `CURRENT-STATE.md` contract that agents read first and update last. Memory is a file, not a hope.
- **Humans approve, structurally.** Publishing, paid spend, deploys, schema changes and destructive actions sit behind human gates enforced in code and in the database, not in a policy doc.
- **Receipts or it didn't happen.** Every agent action leaves a record. If there's no check available, the honest status is "built, not verified."
- **One spine, many tenants.** Shared infrastructure, hard walls between clients: `client_id` on every row, RLS everywhere, secrets only from a vault.
- **Ship v1.** Small, live, tested beats large and theoretical.

---

## Selected work

### The Simple Plan: recovery-support app for a nonprofit · [thesimpleplan.app](https://thesimpleplan.app)
A free, installable recovery companion built around a 30-second practice people can open in a hard moment (Resentment → Forgiveness, Fear → Courage). It's deliberately non-coercive: no streaks, no dopamine loops.
**My part:** backend systems and automation, plus product direction.
- **Reflection-recap pipeline.** A Postgres `pg_cron` job fans out one `pg_net` call per opted-in user. A Supabase Edge Function mints a short-lived user JWT, gathers that person's activity, writes an email in the app's voice, and sends it through Resend. Unsubscribe uses HMAC-signed, version-revocable tokens with RFC 8058 one-click headers.
- **AI-generated guided meditations.** Scripts with timed pauses → OpenAI TTS → ffmpeg silence and a music bed. That produced a 2×2 voice picker with fallback, plus a HeyGen-cloned voice of the program's founding teacher.
- **Team metrics without exposing keys.** One edge function merges Supabase counts and Plausible traffic for the team dashboard, with no keys in the browser.
- React · TypeScript · Supabase (Postgres, Auth, RLS, 17 Edge Functions) · Vercel · Resend · Cloudflare R2

### Multi-tenant Credibility Spine: one platform, many brands · spine.creativehouse.app
An editorial platform where stories move intake → AI parsing → human review → approved "canon" → distribution, for several brands on one codebase and one database.
- Claude extracts category, tags, summary and location from an editor's free-form notes, and the record is geocoded onto a story map. Editor corrections are logged for tuning.
- Distribution to social is hard-gated: the server refuses unless the record is human-approved and the tenant ID matches twice.
- Tenant isolation by design, with six named cross-tenant "poisoning" failure modes (data, content, branding, voice, channel, credential) made structurally impossible.
- React · TypeScript · Supabase (49 migrations, ~14 Edge Functions) · Anthropic SDK · Vercel · Blotato

### I Drink Living Water: water, health and environment movement · [idrinklivingwater.com](https://idrinklivingwater.com)
The public home of a global water-integrity movement, and the first brand running on the Credibility Spine.
**My part:** systems architect. I designed the platform, the data flow from the Spine, and the automation behind it.
- **Live story map** fed by human-approved Spine records, with a built-in fallback so the site never goes blank if the API does.
- **"Doomscroll 12-Day Reset" email programme.** Sign-up goes into a Resend audience. Delivery events come back through a webhook with an HMAC signature check (timing-safe comparison) and land in a nurture-events ledger.
- **One hardened intake endpoint for every form,** with honeypot and minimum-time bot protection and an operations ledger.
- Topic sections (WaterX, health, environment, pets, research), a Stripe store, and a health-report edge function.
- Next.js 15 · Supabase · Stripe · Resend · PostHog · Vercel

### CHS agent runtime: guardrails for an AI workforce
The core that decides how a request from any channel becomes a task, which agent may run it, under what permissions, and what receipt proves it was done.
- Model-neutral agent contracts: Claude, Codex and others are workers with scoped roles.
- Database-enforced invariants: idempotency keys, one receipt per run, and monotonic fence tokens so a stale worker can't commit.
- **"A worker may never approve" is structural.** Approval authority lives in tables that workers have no rows in. Approvals are signed and bound to the request, destination and spend cap.
- Private MCP servers that fail closed, a read-only default-deny system doctor, and a fleet of systemd timers for release, distribution, metrics and watchdogs.
- Python · TypeScript · Postgres · MCP · Doppler · 350+ tests on the dispatcher. Proven end to end on staging.

### Parallel-lane conductor skill
A Claude skill that turns one authorized work packet into 3–5 parallel, bounded agent lanes, each with typed contracts, receipts and exception schemas.
- Model routing: Opus for governance, Sonnet for production, Codex for code, scripts for checks.
- Any single lane can be cancelled mid-run without touching the others. Exceptions collapse into one human ruling.
- Paired with a **multi-model red-team loop**: Claude reconciles, a second model challenges from an auto-generated, deny-scanned state snapshot, and I rule. Every ruling is logged, and settled ground stays settled.

### First Seven: your first week with AI · [first-seven-welcome-room.vercel.app](https://first-seven-welcome-room.vercel.app)
A seven-day onboarding where a small-business owner builds real AI-drafted customer replies and a personalised week plan, by typing or by talking to a voice guide.
- Claude streams a structured plan from a five-question interview. The JSON is validated and allowed to fail safely.
- A realtime voice agent with short-lived client tokens hands interview answers back to the page through a tool call, and the owner can correct them.
- Usage limits are reserved atomically on the server and fail closed. Drafts don't count until the owner reviews them.
- Write-up: [How I built First Seven: patterns you can borrow](https://first-seven-welcome-room.vercel.app/how-i-built-first-seven.md)

### AI documentary pipeline: Voices for Good · [creativehouse.app/voices-for-good](https://creativehouse.app/voices-for-good)
Short documentaries on historical moral courage, produced by an agent pipeline with human gates.
- HeyGen avatar host → ffmpeg build (b-roll motion, grade, burned-in captions, an AI-transparency watermark) → sha256-hashed canonical films → R2 → YouTube.
- A human-in-the-loop map with six named decision points. Agents advance work to the next gate and stop. Script approval, paid renders, posting and deploys are hard limits.
- The regen harness turns reviewer feedback into a script rewrite and fails closed on drift. It clears the old approval so the changed script goes back for human sign-off.

### Phone-first production loop
My own video series is built as code: episode cut lists, captions and b-roll moves live in Python files.
- Builds run on a VPS and check themselves (speech recognition confirms the closing line survives the mix; loudness and duration are checked), then send a preview to my phone.
- When I reply with a note, a headless Claude picks it up, rebuilds, reports back and commits. Nothing publishes without my approval, and passing QA never counts as approval.

### Security-first backends
- **Funding/lead intake engine.** Default-deny RLS, salted IP hashing, per-campaign flood ceilings and identical neutral responses so callers can't probe which campaigns exist. Hardened after three independent adversarial reviews (14 findings triaged and fixed).
- **shellyfrank.com.** Contact writes go only through a hardened `SECURITY DEFINER` function, and anonymous inserts were verified to return 401 over real HTTP.

### Operations surfaces
- **ops.creativehouse.app.** A 72-route Next.js command centre with Claude agent modules (transcript parsing, SOW updates, meeting prep) and a tiered, tenant-aware role contract.
- **Client tracker.** A printable team dashboard. Its crons sync Google Sheets into Postgres, and non-developers edit email copy in a sheet that publishes to live Resend templates.
- **27 n8n workflows** covering OAuth flows, connection-health and token-expiry monitoring, meeting intelligence and app-lifecycle messaging.

---

## Stack

- **AI:** Claude Code · Anthropic API · MCP · multi-agent orchestration · HeyGen · ElevenLabs · OpenAI TTS · realtime voice
- **Backend:** Supabase (Postgres, RLS, Edge Functions, pg_cron) · Python · TypeScript · Node
- **Infra:** Vercel · Cloudflare (DNS, R2) · VPS + systemd · Doppler · GitHub
- **Automation:** n8n · Resend · Blotato · Google Workspace APIs · Retool · Telegram

---

## Now

- Building **Creative House Studios** into a sponsorship-funded media network, run lean on the systems above.
- Teaching founders to build their own agentic operations: **"agency-in-a-box."**
- Running my company from my phone, and helping my co-founder do the same.

📫 [shellyfrank.com](https://shellyfrank.com) · [LinkedIn](https://www.linkedin.com/in/shellyfrank)
