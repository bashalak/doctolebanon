# CONTEXT FOR CLAUDE — read me first when resuming

> **Purpose:** This is Claude's own running memory of the DoctoLebanon project. If the conversation gets long or context is lost, RE-READ THIS FILE FIRST to resume seamlessly. Keep it updated after every working session.
> **Audience:** Claude (not the user). Be blunt and complete here.

_Last updated: 2026-06-06_

---

## Who the user is
- Name context: Bakr (Windows 10 PC, working dir was C:\Users\Bakr; project lives on **D:\doctolebanon\**).
- **Complete beginner** to programming. Does NOT want to learn traditional hard coding, BUT wants to learn fast by reading/tweaking and watching me build.
- Wants ME (Claude) to build the app; he owns & runs it.
- Time: ~5–10 hrs/week, solo. Budget near-zero (~$0–100/mo).
- Communication: explain in plain English, no jargon without defining it. Encourage. Be honest about scope/risks.

## The project
- **DoctoLebanon**: a Doctolib-style doctor-appointment booking web app for Lebanon (web first, mobile much later). Lebanese cities, Arabic/French/English, local prices/payments. Patients book free; doctors get accounts.
- "Real startup" is the DESTINATION; we build a learning MVP with FAKE data first, then layer legal/payments/security.

## Decisions locked in
- Real code (NOT no-code; FlutterFlow/Bubble were considered then dropped because Claude builds real code directly).
- Web first → mobile later (React Native/Expo).
- Tech path: now = single HTML file (vanilla HTML/CSS/JS). Later = React + Next.js + Supabase (DB/auth) + Netlify/Vercel hosting.
- iOS later needs a Mac + Apple acct ($99/yr); user has no Mac. Android works on Windows.
- Lebanon legal: Law No. 81 (2018) on Electronic Transactions & Personal Data governs medical/personal data — handle in Stage E.

## Current state of the build (v0.1)
- `index.html` = self-contained clickable prototype. Vanilla JS, no dependencies, no installs, opens by double-click.
  - Works: search by specialty/city/name; doctor cards; time-slot booking flow (openDoctor → pickSlot → confirmBooking); "My appointments" persisted in **localStorage**; cancel.
  - Fake Lebanese doctors in a `DOCTORS` array. Green/cedar theme. Yellow "fake data" demo banner.
  - NOT yet: shared database, real logins, online hosting, security, SMS. Bookings live only in the user's browser.

## Git / GitHub
- Git installed (C:\Program Files\Git). GitHub CLI NOT installed.
- Repo initialized in D:\doctolebanon\, branch `main`. First commit pushed 2026-06-06.
- GitHub: user `bashalak`, email `bakerchlak1991@gmail.com`, repo **https://github.com/bashalak/doctolebanon (PRIVATE)**.
- AUTH GOTCHA: this tool env is non-interactive; first `git push` failed once ("User cancelled dialog" / no tty) because the Git Credential Manager popup appeared BEHIND other windows. Retrying the push and having the user complete the GCM browser sign-in worked. For future pushes, credentials are now stored, should be silent. If auth breaks again, consider a PAT.
- Added `README.md` and `.gitignore` for the repo.
- New files in git: README.md, .gitignore (+ the 5 prior files).

## Deployment (Netlify) — STAGE B COMPLETE
- LIVE at **https://tranquil-hummingbird-0732de.netlify.app** (verified working 2026-06-06).
- Netlify account: signed in via Google, but connected to GitHub repo `bashalak/doctolebanon` for continuous deploy.
- **Every `git push` auto-redeploys the live site** (~seconds). No build step (static index.html).
- User may rename site to doctolebanon.netlify.app (Site configuration → Site details → Change site name) — update docs if they do.
- Note: JS-rendered content (doctor cards) appears empty to non-JS fetchers/bots but renders fine in real browsers.

## Files in D:\doctolebanon\
- `index.html` — the app (v0.1).
- `PROJECT-PLAN.md` — LIVING: decisions, glossary, progress, roadmap Stages A–F, changelog. UPDATE on every build/change.
- `CODE-EXPLAINED.md` — LIVING: architecture, file tree, per-block code walkthrough, data-flow diagram, 8 concepts list. UPDATE when code changes.
- `MY-NOTES.md` — the user's learning notebook; I append each lesson + exercise here. (Lesson 1 = Variables + Arrays, done 2026-06-06.)
- `CONTEXT-FOR-CLAUDE.md` — this file.

## Roadmap (short form; full in PROJECT-PLAN.md)
- Stage A: polish prototype (doctor sign-up page, AR/FR/EN + RTL toggle, profiles, mobile layout).
- Stage B: get ONLINE free (GitHub + Netlify → real link).
- Stage C: make REAL (install Node.js; rebuild in React/Next; Supabase DB + real logins; shared bookings).
- Stage D: trust (reminders, reschedule, teleconsult, reviews, full i18n).
- Stage E: startup (Law 81 legal, Lebanon payments, security hardening, recruit real doctors).
- Stage F: mobile (React Native/Expo; Android first).

## Teaching track (the user learns alongside)
- Method: read CODE-EXPLAINED + "change one thing" loop + DevTools (F12) + ask-me-any-line + MY-NOTES.
- 8 core concepts order: 1 variables, 2 arrays, 3 objects, 4 functions, 5 the DOM, 6 events, 7 array methods (filter/forEach/map), 8 localStorage+JSON.
- Lessons delivered: **#1 Variables + Arrays (2026-06-06).** Next up: #2 Objects, then #3 Functions.

## Next step (user is choosing among)
- Continue lessons (next: Objects), OR build Stage A item (doctor sign-up / language toggle), OR Stage B (deploy online).

## Notes / gotchas for me
- Bash tool in this env was broken (`mkdir: command not found`); use **PowerShell** for shell ops on this Windows machine.
- Keep the 3 living docs (PLAN, CODE-EXPLAINED, this file) in sync after every change — the user explicitly asked for this.
- Don't over-engineer; user is a beginner. Small steps, celebrate wins.
