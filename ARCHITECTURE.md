# How DoctoLebanon fits together (architecture)

> Plain-English explanation of how the 4 pieces — your PC, GitHub, Netlify, and Supabase — connect and communicate.
> The big idea: they split into **two separate jobs** — moving your **CODE**, and moving your **DATA**.

_Last updated: 2026-06-06 (v0.8)_

---

## The 4 players

| Place | What it holds | Think of it as… |
|---|---|---|
| **Your PC** (`D:\doctolebanon`) | The code you edit | Your **workshop** |
| **GitHub** | A cloud copy + full history of the **code** | The **filing cabinet** |
| **Netlify** | Turns the code into the live website | The **printing press / publisher** |
| **Supabase** | The **data** (doctors; later bookings, accounts) | The **warehouse** |

---

## Flow #1 — the CODE path (how your app gets published)

```
   YOUR PC  ── git push ──▶  GITHUB  ── auto-deploy ──▶  NETLIFY ──▶  live website
 (edit code)              (stores code)              (publishes)   doctolebanon-app.netlify.app
```

- **PC → GitHub:** `git push` uploads your code to GitHub (over the internet, using your saved GitHub login). GitHub keeps every version.
- **GitHub → Netlify:** Netlify is *connected* to your GitHub repo. The moment GitHub receives a push to `main`, it notifies Netlify, which automatically grabs the new code and republishes the site. That's why the site updates by itself.

➡️ This whole path is only about **code**, and it runs **when you push**.

---

## Flow #2 — the DATA path (how the running app gets/saves info)

```
   The app running in a browser          ──▶   SUPABASE
   (your local file OR the live site)     ◀──   (cloud database)
        "give me the doctors"        →   Supabase checks its rules, returns the data as JSON
```

- When `index.html` runs in **anyone's browser**, that browser talks **directly to Supabase** over the internet — using the **Project URL** (the address) and the **publishable key** (the ID badge), both written in the code.
- Supabase checks its security rules (RLS), then returns the data (or saves what you send).
- **GitHub and Netlify are NOT part of this conversation.**

---

## Why a change in Supabase shows up without a `git push`

Your code does **not store** the doctors — it **asks for them every time the page loads**:

```
1. Browser opens index.html        → loads the CODE (the instructions)
2. Code runs loadDoctorsFromCloud()
3. Sends a request to Supabase:  "give me the current doctors"   ───▶
4. Supabase replies with whatever the data is RIGHT NOW          ◀───
5. The app draws the cards using that fresh answer
```

So the data is fetched **live** at refresh time. Change it in Supabase → refresh → step 3 asks again → step 4 returns the new value → it appears. No push needed, because **data ≠ code**.

- Change **code** → you must `push` (then Netlify republishes).
- Change **data** → instant everywhere, no push.

---

## Important: Supabase and GitHub are NOT connected

During Supabase project setup there was an optional *"Connect GitHub"* — we **skipped** it. So:
- **GitHub** only ever sees your **code**; it has no idea Supabase exists.
- The only link between the app and Supabase is these lines **inside the code**:
  ```js
  const SUPABASE_URL = "https://qujudggwzlhdbptohwbp.supabase.co";  // the address
  const SUPABASE_KEY = "sb_publishable_...";                        // the ID badge (safe in a browser)
  const sb = supabase.createClient(SUPABASE_URL, SUPABASE_KEY);     // open a phone line to Supabase
  ```
- GitHub *stores* those lines (they're part of the code), but the actual **conversation with Supabase happens live, from the browser, every time the app runs**.

---

## The whole picture

```
        ┌─────────────────────── CODE ───────────────────────┐
        YOUR PC ──push──▶ GITHUB ──auto-deploy──▶ NETLIFY ──▶ LIVE SITE
                                                                 │
                                                                 │ (and your local file too)
                                                                 ▼
                                                       the running app in a browser
                                                                 │
                                                                 │  DATA (URL + publishable key)
                                                                 ▼
                                                            SUPABASE  🗄️
```

**One sentence:** GitHub + Netlify handle the **code**; Supabase handles the **data**; they never talk to each other — your browser is the customer that visits the warehouse directly, live, every time the app runs.

---

## A note on keys (security)
- **Publishable key** — lives in the code, safe to share. It can only do what the database's RLS rules allow.
- **Secret key** — master key that bypasses all rules. **Never** put it in code or share it; it's only for backend servers (which we don't have). (We rotated/deleted ours after it was accidentally shared — the right move.)
