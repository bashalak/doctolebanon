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

## Current state of the build (v0.7)
- **v0.7 (2026-06-06): booking reason + document attach.** pickSlot form now has a radio group `name="bReason"` (new/follow/urgent, default new) and a `<input type="file" id="bDoc">`. confirmBooking reads the checked radio + `bDoc.files[0]?.name` and stores `reason` + `docName` on the appt (only the NAME is stored — real upload deferred to Stage C for privacy/Law 81). reasonText(key) translates the reason; shown in success screen + My appointments. New CSS `.radios`/`.radio`. New i18n keys reasonLabel/reasonNew/reasonFollow/reasonUrgent/attachLabel/attachHint/reasonWord/attachedWord. Footer v0.7. CODE-EXPLAINED §14.
- Idea origin: user wanted a richer confirm step (radios / attach test documents) like Doctolib.
- (history) v0.6 phone+maps; v0.5 polish; v0.4 profiles; v0.3 trilingual+RTL; v0.2 sign-up; v0.1 base.

## (previous) Current state of the build (v0.6)
- **v0.6 (2026-06-06): profile phone + Maps link.** Added genTel(d) (uses d.tel from sign-up, else stable fake Lebanese number from id) → tappable `tel:` link; mapsURL(d) (encodeURIComponent of AREAS+city+Lebanon) → address is a Google Maps link (target _blank). Added a Phone field to the sign-up form (dPhone) stored as `tel`. Profile grid now: Address(map link), Phone(tel link), Languages, Fee, Experience. New CSS `.plink`. Footer v0.6. Reused existing i18n key `phone`/`phPhone` (no new keys). CODE-EXPLAINED §13.
- Idea origin: user asked for phone (was missing) + interactive address→maps. Deeper version (verified numbers, embedded GPS map) deferred to Stage C+.
- (history) v0.5 polish/mobile; v0.4 profiles; v0.3 trilingual+RTL; v0.2 sign-up; v0.1 base.

## (previous) Current state of the build (v0.5)
- **v0.5 (2026-06-06): polish & mobile.** CSS-only refinements: input :focus glow, .btn:active press, hero radial overlay + card base shadow, responsive grid `minmax(min(100%,300px),1fr)`, expanded `@media(max-width:560px)` (full-width search button, tighter nav/main/modal padding, RTL border fix). Footer bumped to v0.5. No new translatable strings. CODE-EXPLAINED §12.
- **STAGE A COMPLETE.** All Stage A items done.
- (history) v0.4 profiles; v0.3 trilingual+RTL; v0.2 sign-up; v0.1 base.

## (previous) Current state of the build (v0.4)
- **v0.4 (2026-06-06): doctor profile pages.** openDoctor() now renders a profile (address, fee, languages, experience in a `.profile-grid`), an "About" bio, THEN availability slots. Card button label changed from book→`viewProfile`. New data is GENERATED not stored: `AREAS` list + `AREAS[d.id % len]` for address; `yearsExp(6 + d.id%18)`; custom doctors show `newOnPlatform`. New i18n keys: viewProfile/about/address/experience/availability/languagesWord/newOnPlatform (+ yearsExp()/bioText() build localized strings). Footer bumped to v0.4. CODE-EXPLAINED §11 documents it.
- (history) v0.3 trilingual+RTL; v0.2 sign-up; v0.1 base.

## (previous) Current state of the build (v0.3)
- **v0.3 (2026-06-06): trilingual UI (EN/FR/AR) + RTL.** Added `I18N` object (en/fr/ar) + `t(key)` helper; `lang` state persisted in localStorage (`dl_lang`). Header language switcher EN/FR/ع → setLang()/applyLang(). applyLang sets `<html dir=rtl>` for Arabic, updates all static element texts (ids: navFind/navJoin/navApptLabel/heroTitle/heroSub/lblSpecialty/lblCity/lblDocName/btnSearch/demoFlag/listTitle/apptTitle/footerText), repopulates filters, re-renders. Specialty/city/spoken-language DISPLAY translated via SPEC_T/CITY_T/LANG_T while the stored VALUE stays English (so filtering still works). All dynamic views use t()/specLabel()/cityLabel()/langLabel(). CODE-EXPLAINED §10 documents it.
- (history) earlier: v0.2 doctor sign-up; v0.1 base prototype.

## (previous) Current state of the build (v0.2)
- `index.html` = self-contained clickable prototype. Vanilla JS, no dependencies, no installs, opens by double-click.
  - Works: search by specialty/city/name; doctor cards; time-slot booking flow (openDoctor → pickSlot → confirmBooking); "My appointments" persisted in **localStorage**; cancel.
  - **v0.2 (2026-06-06): "Join as a doctor" sign-up page.** New helpers loadDocs/saveDocs (localStorage key `dl_doctors`), `allDoctors()` merges built-in DOCTORS + custom (custom ids start at 1000, flagged `custom:true`, shown with "★ New" badge). render()/openDoctor() now use allDoctors()/getDoctor(id). showSignup() builds the form; submitDoctor() validates (auto-prefixes "Dr."), saves, shows success → returns to listings filtered to the new doctor's city.
  - Fake Lebanese doctors in a `DOCTORS` array. Green/cedar theme. Yellow "fake data" demo banner. Footer says v0.2.
  - NOT yet: shared database, real logins, security, SMS. Custom doctors + bookings live only in the user's browser.

## Git / GitHub
- Git installed (C:\Program Files\Git). GitHub CLI NOT installed.
- Repo initialized in D:\doctolebanon\, branch `main`. First commit pushed 2026-06-06.
- GitHub: user `bashalak`, email `bakerchlak1991@gmail.com`, repo **https://github.com/bashalak/doctolebanon (PRIVATE)**.
- AUTH GOTCHA: this tool env is non-interactive; first `git push` failed once ("User cancelled dialog" / no tty) because the Git Credential Manager popup appeared BEHIND other windows. Retrying the push and having the user complete the GCM browser sign-in worked. For future pushes, credentials are now stored, should be silent. If auth breaks again, consider a PAT.
- Added `README.md` and `.gitignore` for the repo.
- New files in git: README.md, .gitignore (+ the 5 prior files).

## Deployment (Netlify) — STAGE B COMPLETE
- LIVE at **https://doctolebanon-app.netlify.app** (renamed from tranquil-hummingbird-0732de; verified working 2026-06-06).
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
- `COLLABORATION.md` — guide for inviting teammates + avoiding git conflicts (added 2026-06-06; user wants to invite a collaborator). Note: all code still in one file (index.html) = high conflict surface; splitting into files happens in Stage C.
- `ARCHITECTURE.md` — plain-English explanation of how PC/GitHub/Netlify/Supabase connect (code path vs data path; why Supabase data changes show without a push; Supabase NOT connected to GitHub). Added 2026-06-06 after user asked how it all communicates. README + CODE-EXPLAINED file tree refreshed to v0.8 at the same time.

## Current state of the build (v0.11) — REAL PRIVATE APPOINTMENTS (C3 done)
- **v0.11 (2026-06-06): appointments in the cloud, private per user.** New `appointments` table (cols: id, user_id uuid default auth.uid() refs auth.users, doctor_name, specialty, city, slot_day, slot_time, fee, patient_name, phone, reason, doc_name, created_at). RLS: select/insert/delete each `user_id = auth.uid()`. confirmBooking() now async → requires currentUser (else alert loginToBook + showAuth), inserts to appointments (user_id auto). showAppointments() async fetches from cloud (RLS returns only own) + shapeAppt() mapping; shows login prompt if logged out. cancelAppt(id) async delete .eq('id',id). updateBadge() async count (head:true). onAuthStateChange refreshes badge + appt view. Removed loadAppts/saveAppts (localStorage). New i18n key loginToBook. Footer v0.11. CODE-EXPLAINED §18. Verified: booked as baker@test.com, row in Table Editor, shows in My appointments.
- (history) v0.10 auth; v0.9 doctor sign-up→cloud; v0.8 doctors read; v0.1–v0.7 prior.

## Current state of the build (v0.10) — REAL ACCOUNTS (C4 done)
- **v0.10 (2026-06-06): Supabase Auth.** Header has `#authArea` → renderAuthUI() shows "Log in" (logged out) or email + "Log out" (logged in). `currentUser` global; refreshAuth() via getSession(); `sb.auth.onAuthStateChange` keeps header synced. showAuth() modal with email/password + doLogin (signInWithPassword) / doSignup (signUp) / doLogout (signOut). applyLang() calls renderAuthUI() so the button translates. Init registers onAuthStateChange + refreshAuth(). New i18n keys logIn/logOut/signUp/email/password/loginTitle/loginIntro/needEmailPass/checkEmail. Footer v0.10. CODE-EXPLAINED §17.
- **EMAIL CONFIRMATION GOTCHA:** user hit "email rate limit exceeded" on in-app Sign up (Supabase free email sender limit; "Confirm email" toggle was still ON / didn't save). WORKAROUND used: created a user via Authentication→Users→Add user with "Auto Confirm", then logged in from the app — WORKS. So login verified. To make in-app Sign up work: turn OFF Authentication→Providers→Email→"Confirm email" and Save (rate limit also resets ~1h). For production: keep confirmation ON + real email service.
- No new tables for C4 (auth is built-in). Appointments still localStorage (C3 next, now can tie to currentUser).
- (history) v0.9 doctor sign-up→cloud; v0.8 doctors read from cloud; v0.1–v0.7 prior.

## Current state of the build (v0.9) — doctor sign-up writes to cloud
- **v0.9 (2026-06-06): "Join as a doctor" now inserts into the shared `doctors` table.** submitDoctor() is async → `await sb.from('doctors').insert({name, specialty, city, langs, fee, tel})`, checks error, then `await loadDoctorsFromCloud()`. Removed localStorage custom-doctors (loadDocs/saveDocs gone); `allDoctors()` now just returns cloud DOCTORS. "New" badge now keyed on `!d.r` (no rating) instead of the old `custom` flag. shapeDoctor maps `tel`. Footer v0.9. CODE-EXPLAINED §16.
- SQL run by user: `alter table doctors add column if not exists tel text;` + `create policy "Public can add doctors" on doctors for insert with check (true);`. So doctors table policies: public SELECT + public INSERT (demo-permissive; tighten with auth in C4).
- Verified locally: signed up a doctor → row appeared in Supabase Table Editor + in app. Pushed.
- (history) v0.8 doctors read from cloud; v0.1–v0.7 prior.

## Current state of the build (v0.8) — CLOUD-CONNECTED
- **v0.8 (2026-06-06): C2 DONE — doctors load from Supabase.** Added supabase-js CDN + `sb = supabase.createClient(URL, publishableKey)`. `loadDoctorsFromCloud()` does `await sb.from('doctors').select('*').order('id')`, maps rows via `shapeDoctor()` into app shape {n,s,c,r,langs,fee,id,color,init}. `let DOCTORS=[]` filled at startup; init now `applyLang(); loadDoctorsFromCloud().then(render)` with a ⏳ placeholder. `FALLBACK_DOCTORS` (the 12) used if fetch fails. Footer v0.8. Verified working locally (Table Editor edit appeared after refresh).
- Supabase project: ref `qujudggwzlhdbptohwbp`, URL https://qujudggwzlhdbptohwbp.supabase.co. Publishable key `sb_publishable_S1Q11ku0H659CiuoW4i1Mw_5OwY60-u` (in code, safe). OLD leaked secret key was ROTATED + deleted by user (security resolved). `doctors` table: cols id/name/specialty/city/rating/langs(text[])/fee/created_at; RLS on + public SELECT policy "Public can read doctors". 12 rows seeded.
- Still browser-only (localStorage): signed-up doctors (loadDocs/saveDocs) and appointments (loadAppts/saveAppts). Those move to cloud in C3+.

## STAGE C IN PROGRESS (started 2026-06-06)
- **Refined approach: GENTLE path.** Connect the CURRENT vanilla index.html to Supabase via the Supabase JS CDN (`<script src>`) — NO Node.js, NO React rewrite yet, keep single-file + Netlify deploy. Swap localStorage (loadDocs/saveDocs/loadAppts/saveAppts) for Supabase queries. React/Next rebuild deferred to "later, only if needed".
- Steps: C1 create Supabase account+project → C2 `doctors` table, load shared → C3 appointments table → C4 Supabase Auth (real logins) → C5 Supabase Storage (real file upload).
- Anon/public key is safe to put in client code (designed for it); the service_role key must NEVER be committed. Will need RLS (row-level security) policies; for first demo step use simple public read/insert, tighten later.
- Waiting on user: create Supabase project (sign in w/ GitHub), save DB password, pick region (EU/Frankfurt reasonable for Lebanon), then report back → next I guide getting Project URL + anon key + creating the doctors table.

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
- Lessons delivered: **#1 Variables + Arrays; #2 Objects (both 2026-06-06).** Lesson 2 covered: object literals, dot vs bracket access (tied to why t() uses I18N[lang]), array-of-objects (DOCTORS), nested I18N, adding/changing properties. Next up: #3 Functions, then #4 the DOM.

## Next step (user is choosing among)
- Continue lessons (next: Objects), OR build Stage A item (doctor sign-up / language toggle), OR Stage B (deploy online).

## Notes / gotchas for me
- Bash tool in this env was broken (`mkdir: command not found`); use **PowerShell** for shell ops on this Windows machine.
- Keep the 3 living docs (PLAN, CODE-EXPLAINED, this file) in sync after every change — the user explicitly asked for this.
- Don't over-engineer; user is a beginner. Small steps, celebrate wins.
