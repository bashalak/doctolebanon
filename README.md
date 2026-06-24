# 🌿 DoctoLebanon

Book a doctor anywhere in Lebanon — a Doctolib-inspired appointment-booking web app, built for Lebanon (local cities, Arabic/French/English, local prices).

**🔗 Live demo:** https://bashalak.github.io/doctolebanon/

> **Status:** early prototype (v0.8). Demo data is fake — not for real medical use yet.

## What it does (so far)
- Search doctors by specialty, city, and name
- Doctors load from a real **Supabase cloud database** (shared across everyone)
- Doctor profiles with tappable phone and a Maps link
- Book an appointment with a reason for visit and optional document
- "My appointments" page (saved in your browser)
- "Join as a doctor" sign-up — new doctors appear in search
- Trilingual interface: English / French / Arabic (with right-to-left)

## How to run it
No installation needed — just open **`index.html`** in any web browser.

## Project documents
- **`PROJECT-PLAN.md`** — the roadmap and progress log
- **`CODE-EXPLAINED.md`** — how the code works (for learning)
- **`ARCHITECTURE.md`** — how the PC, GitHub, Netlify & Supabase fit together
- **`MY-NOTES.md`** — learning notebook
- **`COLLABORATION.md`** — how to work together without conflicts

## Tech
- **Front-end:** plain HTML + CSS + JavaScript (one file, `index.html`)
- **Database:** Supabase (cloud Postgres) — the doctor directory lives here
- **Hosting:** GitHub Pages (free, auto-deploys from GitHub on every push)
- **Planned:** real accounts (Supabase Auth), document upload (Supabase Storage), and a React rebuild later if the app grows enough to need it

---
Made with the help of Claude Code.
