# TalentFlow — Handoff Notes

_Personal note to hand the project off. Not in the repo / not on GitHub — share directly._
_Prepared 28 May 2026._

## What this project is
**TalentFlow** — Atomberg's internal AI recruiting tool. Next.js 16 + TypeScript + Tailwind + Supabase (Postgres/Auth) + the Anthropic (Claude) API. Repo: `https://github.com/Richa1616/talentflow` (branch `main`). Deploys to Vercel and **auto-deploys on every push to `main`**.

## What shipped most recently (so you have context)
The back half of the hiring funnel was added — four new modules on the candidate detail page (**Interviews → Offer → Onboarding → Background verification**):
1. **Interview scorecards** + reschedule form + .ics calendar invites
2. **Offer management** — draft → approval → send → accept/decline, with a printable offer letter
3. **Onboarding checklist** — marks the candidate "hired" on completion
4. **Background verification** — 5-check tracker (employment / education / identity / address / criminal)

Plus: the psychometric flow hardening was restored (idempotent link open, retry, resume), all pre-existing lint was fixed (repo is lint-clean), and Vercel **Analytics + Speed Insights** were added.

Latest commit as of this note: **`ab6ad5a`** ("feat: add Vercel Speed Insights"). All four DB migrations (`src/db/migrations/20260528_*.sql`) are **already applied** to the shared Supabase project.

---

## Getting started tomorrow (do this in order)

You already have `.env.local`, the same Supabase, and GitHub access — so it's just three steps:

```bash
# 1. Sync with GitHub (you're behind)
git fetch origin && git pull
git log --oneline -1        # should show ab6ad5a or newer

# 2. Install deps  ⚠️ DON'T SKIP — new packages were added (@vercel/analytics,
#    @vercel/speed-insights). A git pull never touches node_modules, so without
#    this the build fails with "module not found".
npm install

# 3. Verify it's green in your environment
npm run build               # should pass clean
npm run dev                 # localhost:3000 — log in, open a candidate,
                            # confirm the Offer / Onboarding / BGV sections render
```

### If `git pull` complains
- **Uncommitted local edits:** `git stash` → `git pull` → `git stash pop`
- **Local commits not pushed:** `git pull --rebase`

### Things you do NOT need to do
- **No migrations to run** — the Supabase DB is shared and all tables already exist.
- **No `.env.local` changes** — yours is already set up (same Supabase keys).
  - Note: AI features (boolean query, CV scoring, question generation) need a real `ANTHROPIC_API_KEY` in `.env.local`. The new four modules are plain CRUD and don't need Claude.

---

## Working with Claude Code on this repo

The repo is built to onboard a new person/AI fast. Point Claude at these first:
- **`CLAUDE.md`** (repo root) — loads automatically; the house rules and conventions.
- **`prompt.md`** — the task workflow. It currently says *"(no active task)"*. Write your task under the `# Task` heading, then tell Claude **"go"** and it executes it.
- **`docs/`**:
  - `prd.md` — what we're building and why
  - `techspec.md` — how it's built (**§12** documents the four new modules)
  - `decision.md` — the architecture decision log (ADRs 1–13; the constraints)
  - `build-plan.md` — the original one-day plan (historical)

**The golden rule of this repo:** the repo is the memory, chat is ephemeral. Non-trivial work gets written into `prompt.md` first, then run.

---

## Good next tasks (from the gap analysis)
When you're ready to pick something up, the highest-value gaps remaining are:
- 🔴 **Governance layer** — audit log, DPDP compliance (consent / retention / erasure), and RBAC (the `users` table has no role column today; anyone with an `@atomberg.com` login sees everything).
- 🟠 **Real analytics dashboard** — the dashboard stat cards are still hardcoded; wire them to real data (now that Vercel Analytics is in, and you have the candidate/role/status data in Supabase).
- 🟡 **Requisition intake, talent pool / candidate de-dup, automated sourcing** — longer-term, toward a true end-to-end ATS.

There's also a fuller stakeholder write-up of the gaps in `TalentFlow-ATS-Analysis.html` (Richa has it).

---

## Quick reference
- **Run locally:** `npm run dev`
- **Check it compiles:** `npm run build` (or `npx tsc --noEmit` for a fast type-check)
- **Lint:** `npm run lint` (should be 0 problems — keep it that way)
- **Commit style:** imperative, reference the ADR/feature, e.g. `feat: add audit log (ADR-014)`
- **Deploy:** just push to `main` — Vercel auto-deploys. (Env keys live in the Vercel project settings, not in git.)

Good luck — ping Richa if anything's unclear. 🙂
