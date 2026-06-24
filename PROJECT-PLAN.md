# DoctoLebanon — Project Plan & Progress Log

> **What this file is:** Your living "map" of the whole project — what we decided, what we've built, and the full step-by-step plan to grow from a demo into a real web app.
> **Rule:** Every time we build or change something, this file gets updated (see the Changelog at the bottom).

_Last updated: 2026-06-05_

---

## 1. The idea

**DoctoLebanon** = a website (and later a mobile app) where people in Lebanon can find a doctor and book an appointment online — inspired by Doctolib, but built for Lebanon (local cities, Arabic/French/English, local prices and payments). Doctors get a free/paid account to manage their schedule; patients book for free.

---

## 2. Key decisions so far

| Topic | Decision | Why |
|---|---|---|
| Who builds it | **Claude (me) writes the real code; you own & run it** | You don't want to learn traditional coding, but you want a real product and to learn *while* watching. |
| Coding vs no-code | **Real code** (not FlutterFlow/Bubble) | I can write and maintain real code directly; it's free and more flexible. |
| Web or mobile first | **Web first**, mobile later | Easier to build and show; the website builds the "brain" the mobile app will reuse. |
| Ambition | "Real startup" is the **destination**; we build a learning MVP with fake data **first** | Avoids drowning in legal/payment/security before anything works. |
| Project location | `D:\doctolebanon\` | Your choice. |
| Time | ~5–10 hrs/week, solo | Roadmap is sized in small steps. |

---

## 3. The technology (plain-English glossary)

A website is made of layers. Here's the stack we're heading toward and what each part does:

| Layer | Tool we'll use | What it does (in plain words) |
|---|---|---|
| **Structure** | HTML | The skeleton — what's on the page (text, buttons, boxes). |
| **Style** | CSS | The looks — colors, spacing, fonts, layout. |
| **Behavior** | JavaScript | The actions — what happens when you click, search, book. |
| **Framework** (later) | React + Next.js | A smarter way to build big JavaScript apps without chaos. |
| **Backend / Database** (later) | Supabase (Postgres) | The shared "memory" of the app — stores doctors, patients, bookings for *everyone*, not just your browser. Also handles logins. |
| **Hosting** (later) | Netlify / Vercel | Puts the site on the internet at a real web address. |
| **Mobile** (much later) | React Native + Expo | Turns it into an iPhone/Android app, reusing the web code. |

> 💡 Right now (v0.1) we only use the **first 3 layers** (HTML + CSS + JavaScript) in a single file. That's enough for a clickable demo.

---

## 4. What we've built so far

### ✅ v0.1 — Clickable prototype (2026-06-05)
- One file: `index.html` — opens in any browser, **zero installation**.
- Features that work:
  - Search doctors by **specialty**, **city**, and **name**.
  - Doctor cards with rating, languages, price, next availability.
  - Click a doctor → see fake time slots → pick one → enter name/phone → **confirm booking**.
  - **"My appointments"** page; bookings are saved in your browser (`localStorage`) and survive a refresh; you can cancel them.
- Branding: green/cedar theme, Lebanese cities, fake Lebanese doctors.
- **Limitation:** front-end only. Data is fake; bookings live only in *your* browser; no real accounts, no SMS, not online yet.

---

## 5. The full road from "demo" to "real web app"

Each step is a milestone. We check them off as we go. `[ ]` = todo, `[x]` = done.

### Stage A — Polish the prototype (no new tools needed)
- [x] Build the basic clickable prototype (v0.1)
- [x] Add a **doctor sign-up** page — new doctors are saved and appear in search with a "New" badge (v0.2)
- [x] Add **language toggle** (English / French / Arabic + right-to-left layout for Arabic) — remembers your choice (v0.3)
- [x] Improve design details & mobile-phone layout (focus states, depth, tactile buttons, full-width mobile search, tighter modals) (v0.5)
- [x] Add a doctor **profile page** (address, languages, fee, experience, About bio — translated) before booking (v0.4)

### Stage B — Get it ONLINE (fast morale boost)
- [x] Create a free **GitHub** account (`bashalak`)
- [x] Push the code to GitHub → https://github.com/bashalak/doctolebanon (private)
- [x] Create a free **Netlify** account (signed in via Google; GitHub connected for deploys)
- [x] Deploy `index.html` → **LIVE at https://doctolebanon-app.netlify.app** (auto-redeploys on every git push)
- [x] Renamed site to `doctolebanon-app.netlify.app`
- [ ] Open it on your phone — share with a friend

### Stage C — Make it REAL (the big jump: from demo to app)
**Approach (refined 2026-06-06):** connect the CURRENT app to a real cloud database (Supabase) FIRST — gentle, no big installs, keep all existing work. Rebuild in React/Next.js only later, if/when the app grows enough to need it.
- [ ] **C1:** Create a free **Supabase** account + project (cloud database) ← *in progress*
- [x] **C2:** Created a `doctors` table; app now loads doctors from Supabase so they're **shared** across all users (v0.8) ✅
- [x] **C3:** Appointments saved to Supabase, **tied to the logged-in user and private** (each user sees only their own); book / view / cancel all run against the cloud (v0.11) ✅
- [x] **C4:** Real **accounts** — sign up / log in / log out with Supabase Auth; header shows the logged-in email; session persists (v0.10). In-app **Sign up now works** (we turned "Confirm email" OFF in Supabase for the demo). *(For a real launch: turn confirmation back ON with a real email service so users verify their address.)*
- [x] **C5:** Real **document upload** — files go to a private Supabase Storage bucket (each user can only reach their own), viewed via short-lived signed links (v0.13) ✅ **Stage C complete!**
- [ ] (later) Rebuild front-end with **React + Next.js** + split into multiple files — only when needed (needs Node.js then)

### Stage D — Make it trustworthy
- [ ] **Email/SMS reminders** for appointments
- [ ] Cancel / reschedule flow
- [ ] Basic **teleconsultation** (video link)
- [ ] Search improvements, reviews, doctor verification
- [ ] Full multi-language (AR/FR/EN) everywhere

### Stage E — Turn it into a real startup (only after it works)
- [ ] **Legal:** comply with Lebanon's data-protection **Law No. 81** (consent, encryption, user data rights) — likely talk to a lawyer
- [ ] **Payments:** research Lebanon options (cards are limited; wallets like Whish/OMT; or "pay at clinic")
- [ ] **Security hardening** (medical data is sensitive)
- [ ] Recruit **real Lebanese doctors** (the business challenge)
- [ ] Terms of service, privacy policy, hosting in a compliant region

### Stage F — Mobile app
- [ ] Rebuild UI in **React Native + Expo** (reuses the backend)
- [ ] Android first (works on Windows); iOS needs a Mac + Apple account ($99/yr)

---

## 6. How YOU learn fast alongside this (my tips)

1. **Read `CODE-EXPLAINED.md`** next to the running app. Match each section of the doc to what you see on screen.
2. **The "change one thing" loop:** open `index.html` in a text editor, change one value (e.g. a color or a doctor's name), save, refresh the browser. Seeing your change instantly is the fastest teacher.
3. **Open DevTools:** press **F12** in the browser → "Elements" shows the HTML, "Console" shows JavaScript. Poke around.
4. **Ask me to explain any line** — literally paste a line and ask "what does this do?".
5. **Keep a `MY-NOTES.md`** file with questions and "aha" moments. ✅ created — your personal notebook.
6. **Free resources** when you're curious: `developer.mozilla.org` (MDN — the dictionary of the web), `javascript.info` (clear JS lessons), `freecodecamp.org` (free interactive courses).

> You don't need to *write* code to learn — reading, tweaking, and asking "why" builds real understanding fast.

---

## 7. Changelog (newest first)

| Date | What changed | Files |
|---|---|---|
| 2026-06-06 | Migrated hosting from Netlify (ran out of free credits) to **GitHub Pages** — now live at https://bashalak.github.io/doctolebanon/ (repo made public; free, no credit system) | (hosting) |
| 2026-06-06 | **v0.14 — doctor dashboard (part 1):** doctor listings owned by accounts, appointments linked to a doctor, "Join as a doctor" now requires login | `index.html` |
| 2026-06-06 | **v0.13 — C5 (Stage C complete!):** real document upload to a private Storage bucket; view via short-lived signed links | `index.html` |
| 2026-06-06 | **v0.12:** safety net — press Escape to close any pop-up (after a stale-cache tab left an overlay stuck on the live site; hard-refresh resolves that) | `index.html` |
| 2026-06-06 | **v0.11 — C3:** appointments saved to Supabase, private per user (book/view/cancel against the cloud; login required to book) | `index.html` |
| 2026-06-06 | **v0.10 — C4:** real accounts via Supabase Auth (log in / log out, email shown in header, session persists) | `index.html` |
| 2026-06-06 | **v0.9:** "Join as a doctor" now saves to the shared Supabase database (real `insert`) — new doctors appear for everyone, not just one browser | `index.html` |
| 2026-06-06 | Added architecture guide (how PC/GitHub/Netlify/Supabase connect); refreshed README & CODE-EXPLAINED file tree to v0.8 | `ARCHITECTURE.md`, `README.md`, `CODE-EXPLAINED.md` |
| 2026-06-06 | **v0.8 — STAGE C BEGINS:** connected the app to a real **Supabase** cloud database; doctors now load from the cloud (shared across everyone), with a built-in fallback | `index.html` |
| 2026-06-06 | First Pull Request practice run (branch → PR → review → merge → pull); README updated to v0.7 via PR #1 | `README.md` |
| 2026-06-06 | Added a collaboration guide (inviting teammates, avoiding conflicts, branches & PRs) | `COLLABORATION.md` |
| 2026-06-06 | **v0.7:** booking now has a "Reason for visit" (radio buttons) and an optional document attachment; both shown in confirmation & My appointments | `index.html` |
| 2026-06-06 | **v0.6:** profile now shows a tappable phone (`tel:`) and a clickable address that opens Google Maps; phone added to doctor sign-up | `index.html` |
| 2026-06-06 | **v0.5:** polish & mobile — input focus states, hero/card depth, tactile buttons, full-width mobile search, tighter modals, responsive grid | `index.html` |
| 2026-06-06 | **v0.4:** doctor profile (address, languages, fee, experience, About bio) shown before booking; all translated | `index.html` |
| 2026-06-06 | **v0.3:** trilingual UI (EN/FR/AR) with a language switcher + right-to-left layout for Arabic; choice remembered | `index.html` |
| 2026-06-06 | **v0.2:** added "Join as a doctor" sign-up page; new doctors saved in browser and shown in search results | `index.html` |
| 2026-06-06 | 🚀 Deployed live via Netlify — https://tranquil-hummingbird-0732de.netlify.app (auto-deploys on push). **Stage B complete.** | (deploy) |
| 2026-06-06 | Set up Git, made first commit, pushed to GitHub (private repo `bashalak/doctolebanon`) | `README.md`, `.gitignore`, all |
| 2026-06-06 | Created learning notebook + Claude's context file; delivered Lesson 1 (Variables + Arrays) | `MY-NOTES.md`, `CONTEXT-FOR-CLAUDE.md` |
| 2026-06-05 | Created project plan & code-explained docs | `PROJECT-PLAN.md`, `CODE-EXPLAINED.md` |
| 2026-06-05 | Built v0.1 clickable prototype (search, booking, my-appointments, localStorage) | `index.html` |
| 2026-06-05 | Created project folder | `D:\doctolebanon\` |
