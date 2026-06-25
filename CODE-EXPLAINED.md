# DoctoLebanon — Code Explained (for learning)

> **What this file is:** A plain-English tour of the code so you understand the *logic* and *architecture*, not just what it does.
> Read this with `index.html` open in a text editor and the app open in your browser.

_Last updated: 2026-06-05 · Covers: `index.html` (v0.1)_

---

## 1. The big picture: how ANY web page works

Every website is built from **three languages** that work together:

```
   HTML  →  the STRUCTURE   (what exists: text, buttons, boxes)
   CSS   →  the STYLE       (how it looks: colors, layout, spacing)
   JS    →  the BEHAVIOR    (what happens: clicks, search, booking)
```

Think of a house:
- **HTML** = the walls and rooms (structure).
- **CSS** = the paint, furniture, decoration (style).
- **JavaScript** = the electricity and plumbing — the things that *do stuff* when you flip a switch.

Right now, all three live inside **one file** (`index.html`) to keep things simple. Later, when the app grows, we'll split them into separate files and folders.

---

## 2. Folder structure (the file tree)

```
D:\doctolebanon\
│
├── index.html               ← THE WHOLE APP (HTML + CSS + JavaScript in one file)
├── README.md                 ← project overview + live link
├── PROJECT-PLAN.md           ← the living plan, roadmap & changelog
├── CODE-EXPLAINED.md         ← this file (how the code works)
├── ARCHITECTURE.md           ← how PC, GitHub, Netlify & Supabase connect
├── COLLABORATION.md          ← how to work together without conflicts
├── MY-NOTES.md               ← your learning notebook (lessons + your notes)
├── CONTEXT-FOR-CLAUDE.md     ← Claude's running memory of the project
└── .gitignore                ← files Git should ignore
```

> 🔌 **Want the big picture** of how your PC, GitHub, Netlify and Supabase connect and talk to each other? See **`ARCHITECTURE.md`**.

> As the project grows (Stage C in the plan), this tree will expand into many folders — e.g. `components/`, `pages/`, `lib/`. We'll update this tree each time.

---

## 3. Inside `index.html` — the logical map

The single file is organized top-to-bottom like this:

```
index.html
│
├── <head>
│   ├── meta tags          → page settings (mobile-friendly, title, encoding)
│   └── <style> ... </style>  → ALL the CSS (the looks)
│
└── <body>
    ├── <header>           → top bar: logo + "Find a doctor" + "My appointments"
    ├── <section .hero>    → green banner + the SEARCH box
    ├── .demo-flag         → the yellow "this is fake data" warning
    ├── <main>
    │   ├── #listView      → the grid of doctor cards (shown by default)
    │   └── #apptView      → the "My appointments" page (hidden until opened)
    ├── #overlay/#modal    → the pop-up window for booking (hidden until opened)
    ├── <footer>           → bottom line
    │
    └── <script> ... </script>  → ALL the JavaScript (the behavior)
```

Two ideas to notice:
1. **Views are swapped, not separate pages.** "My appointments" and the doctor list are both in the page; JavaScript just **hides one and shows the other**. (Real multi-page navigation comes later with Next.js.)
2. **The modal** (booking pop-up) is always in the page but invisible until you click a doctor.

---

## 4. The CSS part (the looks) — key concepts

At the top inside `<style>` you'll see:

```css
:root{
  --green:#0f8a7e;
  --ink:#13343b;
  ...
}
```

- `:root { --green: ... }` defines **reusable color variables**. Instead of typing the green color 50 times, we write `var(--green)`. **Change it once here → the whole site changes.** Try it!
- Class names like `.doc`, `.btn`, `.modal` are **styling labels**. In the HTML you'll see `class="doc"`; the CSS rule `.doc { ... }` styles every element with that label.
- `@media(max-width:560px){ ... }` = **responsive design**: special rules that kick in on small phone screens.

**Learning tip:** find `--green:#0f8a7e;`, change it to `#8a0f3a` (a pink-red), save, refresh. The whole app re-themes. That's the power of CSS variables.

---

## 5. The JavaScript part (the behavior) — walkthrough

This is the "brain." Here's each block, in order, in plain English.

### 5.1 The data (fake doctors)
```js
const CITIES = ["Beirut","Tripoli", ...];
const SPECIALTIES = ["General practitioner", ...];
const DOCTORS = [ {n:"Dr. Rana Khoury", s:"Dermatologist", c:"Beirut", ...}, ... ];
```
- `const` = "this is a value I'm naming and won't reassign."
- `[ ... ]` = an **array** (a list).
- `{ ... }` = an **object** (a single thing with labeled properties: `n`=name, `s`=specialty, `c`=city, `r`=rating).
- So `DOCTORS` is **a list of doctor objects**. This is our pretend "database" for now.

```js
DOCTORS.forEach((d,i)=>{ d.id=i; d.color=...; d.init=... });
```
- `forEach` = "do this for every item in the list." Here it gives each doctor an `id` number, a color, and initials (e.g. "RK") for their avatar.

### 5.2 Helper functions (small reusable tools)
```js
const $ = id => document.getElementById(id);
```
- A **shortcut**: `$("grid")` finds the HTML element whose `id="grid"`. (`document` = the page itself.)

```js
function loadAppts(){ return JSON.parse(localStorage.getItem("dl_appts")||"[]"); }
function saveAppts(a){ localStorage.setItem("dl_appts", JSON.stringify(a)); }
```
- `localStorage` = a small **storage box inside your browser** that survives refreshes. This is *why* your bookings stay after you close the tab.
- `JSON.stringify` / `JSON.parse` = convert our list to text to store it, and back again to use it. (Storage can only hold text.)

```js
function slotsFor(doc){ ... }
```
- Builds the fake available time slots for a doctor over the next few days. (Later, real availability comes from the database.)

### 5.3 Showing the doctor list — `render()`
```js
function render(){
  const spec=$("fSpec").value, city=$("fCity").value, name=$("fName").value...;
  const list = DOCTORS.filter(d => (!spec||d.s===spec) && (!city||d.c===city) && ...);
  ...
  list.forEach(d => { /* build a card and add it to the page */ });
}
```
- Reads what you typed/selected in the search box.
- `.filter(...)` = keep only the doctors that **match** your search.
- Then loops over the matches and **builds an HTML card** for each, inserting it into `#grid`.
- `render()` runs at startup and every time you change the search — that's why results update live.

### 5.4 Booking flow — three functions chained together
```
openDoctor(id)   → shows the doctor's available slots in the pop-up
     ↓ (you click a slot)
pickSlot(...)     → shows the confirm form (name + phone)
     ↓ (you click Confirm)
confirmBooking()  → saves the appointment + shows the ✅ success screen
```
- Notice how the same pop-up (`#modal`) gets its **contents rewritten** at each step. That's done with `$("modal").innerHTML = ` ... — JavaScript replacing what's inside the box.

### 5.5 The appointments page
```js
function showAppointments(){ ... }   // hides the list, shows saved bookings
function cancelAppt(i){ ... }        // removes booking #i and re-draws
```

### 5.6 Navigation & startup
```js
function goHome(){ /* show list, hide appointments */ }
render(); updateBadge();   // ← the last two lines: run once when the page loads
```
- Those last two lines are the **"on" switch** — they draw the initial screen.

---

## 6. How a click flows through the app (data flow)

Example: you book an appointment. Follow the arrows:

```
You click "See availability & book"
        │
        ▼
  openDoctor(id) ───────► fills #modal with time slots ──► pop-up appears
        │
You click a time slot
        ▼
  pickSlot(id, day, time) ─► fills #modal with name/phone form
        │
You click "Confirm booking"
        ▼
  confirmBooking()
        ├─ reads name + phone
        ├─ loadAppts()  → get current list from browser storage
        ├─ adds your new appointment
        ├─ saveAppts()  → write list back to browser storage
        └─ shows ✅ success screen
```

This "event → function → update the page → save data" cycle is the **heartbeat of every interactive app**. Learn this pattern and you understand most front-end code.

---

## 7. The 8 concepts to learn first (in order)

If you want to *understand* the code, learn these one at a time. Ask me to teach any of them with examples from this exact file:

1. **Variables** (`const`, `let`) — naming values.
2. **Arrays** (`[ ]`) — lists.
3. **Objects** (`{ }`) — a thing with labeled properties.
4. **Functions** — reusable blocks of actions.
5. **The DOM** (`document.getElementById`, `innerHTML`) — how JS reads/changes the page.
6. **Events** (`onclick`, `oninput`) — running code when the user does something.
7. **Array methods** (`.filter`, `.forEach`, `.map`) — looping and transforming lists.
8. **localStorage + JSON** — saving data in the browser.

> Everything in `index.html` is just these 8 ideas combined. Once they click, you'll be able to read almost any front-end code.

---

## 8. What this version does NOT have yet (and why it matters)

| Missing | What it means | Comes in (plan stage) |
|---|---|---|
| Shared database | Bookings only live in YOUR browser, not shared | Stage C |
| Real logins | No real patient/doctor accounts | Stage C |
| Being online | Only opens from your D drive | Stage B |
| Security | Fine for a demo; not for real medical data | Stage E |

These aren't bugs — they're the next chapters of the plan.

---

## 9. New in v0.2 — the Doctor sign-up feature

We added a **"Join as a doctor"** page. Here's the logic, and the new concepts it teaches.

### The idea
- The page is an empty box: `<div id="signupView">`. JavaScript fills it with a form when you click "Join as a doctor".
- When you submit, we save the new doctor into the browser (`localStorage`, key `dl_doctors`).
- The search list now shows **built-in doctors + your saved doctors** combined.

### The key new functions
```js
function loadDocs(){ ... }   // read saved doctors from the browser
function saveDocs(a){ ... }  // write saved doctors to the browser
```
Same pattern as `loadAppts`/`saveAppts` from v0.1 — you've seen it before! (Reusing a pattern = you're learning.)

```js
function allDoctors(){
  const custom = loadDocs().map((d,i)=>({ ...d, id:1000+i, color:..., init:..., custom:true }));
  return DOCTORS.concat(custom);
}
```
- `.map(...)` = **transform every item in a list** into a new shape. Here it takes each saved doctor and adds an `id`, a `color`, initials, and a `custom:true` flag.
- `{ ...d, id:1000+i }` — the `...d` is the **"spread"**: "copy all of d's properties, then add/override these." We give custom doctors ids starting at `1000` so they never clash with the built-in ones (0–11).
- `.concat(custom)` = **glue two lists together**. So `allDoctors()` = the 12 fake ones + yours.
- `render()` now loops over `allDoctors()` instead of `DOCTORS`. That one change is why your new doctor appears in search. ✨

```js
function submitDoctor(){
  const langs=[...document.querySelectorAll(".dLang:checked")].map(c=>c.value);
  if(!/^dr\.?\s/i.test(name)) name = "Dr. " + name;
  ...
}
```
- `document.querySelectorAll(".dLang:checked")` = **find all the ticked checkboxes**.
- `[... ]` around it turns that result into a real array so we can `.map` it into a list of languages.
- `/^dr\.?\s/i.test(name)` is a **regular expression** ("regex") — a pattern-matcher. This one asks "does the name already start with 'Dr.'?" If not, we add it. (Regex is advanced — don't worry about the symbols yet; just know it's pattern-matching on text.)

### New concepts this feature introduced (added to your learning list)
- **`.map()`** — transform a list into a new list (concept #7 from §7).
- **Spread `...`** — copy an object/array and tweak it.
- **`.concat()`** — join lists.
- **Checkboxes + `querySelectorAll`** — reading multiple form inputs.
- **Regex (a peek)** — matching text patterns.

> Notice we did NOT invent new ideas from scratch — we reused `load/save` + `localStorage` + arrays + objects (Lessons 1–3) and just combined them. That's how real features get built: small known pieces, assembled.

---

## 10. New in v0.3 — Languages (English / French / Arabic + RTL)

We made the whole app **trilingual** with a switcher in the header (EN / FR / ع), and Arabic flips the layout to **right-to-left**.

### The core idea: never hard-code text — look it up
Instead of writing `"Search"` directly in the page, we store every phrase in a big **translations object** and look it up by a short key:
```js
const I18N = {
  en: { search:"Search", lblCity:"City", ... },
  fr: { search:"Rechercher", lblCity:"Ville", ... },
  ar: { search:"بحث", lblCity:"المدينة", ... }
};
let lang = localStorage.getItem("dl_lang") || "en";   // remembers your choice
function t(key){ return I18N[lang][key] || I18N.en[key] || key; }
```
- `t("search")` returns "Search", "Rechercher", or "بحث" depending on the current `lang`.
- This `t()` helper (short for "translate") is used **everywhere** text appears.
- `lang` is saved in `localStorage` so the app reopens in your chosen language.

### Switching language
```js
function setLang(l){ lang=l; localStorage.setItem("dl_lang", l); applyLang(); }
function applyLang(){
  document.documentElement.setAttribute("dir", lang==="ar" ? "rtl" : "ltr");  // ← right-to-left for Arabic
  $("heroTitle").textContent = t("heroTitle");   // update each fixed text on the page
  ...
  populateFilters();   // dropdowns get translated labels
  render();            // redraw the doctor list in the new language
}
```
- The single line `setAttribute("dir","rtl")` is what mirrors the entire layout for Arabic — the browser does the flipping for us.
- `applyLang()` updates the fixed bits (header, hero, footer) and re-runs `render()` so the dynamic bits (doctor cards) come back translated.

### Smart bit: keep DATA in English, translate only the LABEL
A doctor's specialty is stored as `"Cardiologist"` (English) so the **search filter keeps working**. We only translate it for *display*:
```js
function specLabel(v){ return (SPEC_T[lang] && SPEC_T[lang][v]) || v; }  // "Cardiologist" → "طبيب قلب"
```
So filtering compares the stable English value, while the user sees their language. (Same trick for cities and spoken languages.)

### New concepts this introduced
- **Lookup tables / dictionaries** — `I18N[lang][key]` to fetch the right text.
- **`dir="rtl"`** — one attribute flips the whole layout for Arabic.
- **Separating data from presentation** — store one canonical value, show a translated label.

---

## 11. New in v0.4 — Doctor profile pages

Clicking a doctor used to jump straight to time slots. Now `openDoctor()` shows a **profile first** — address, languages, fee, years of experience, an "About" paragraph — then the availability below.

### Where the profile details come from
We didn't add a bio/address to all 12 doctors by hand. Instead we **generate** them, which keeps the data clean and teaches a useful trick:
```js
const addr = `${AREAS[d.id % AREAS.length]} — ${cityLabel(d.c)}`;     // pick a Beirut-area neighbourhood by id
const exp  = d.custom ? t("newOnPlatform") : yearsExp(6 + (d.id % 18)); // 6–23 years, derived from the id
```
- `d.id % AREAS.length` = the **remainder trick** (modulo): it always lands on a valid spot in the `AREAS` list no matter how big the id is. (You saw `%` before in `slotsFor`.)
- `bioText(d)` builds a localized sentence from the doctor's specialty + city — so even the bio respects EN/FR/AR.
- New doctors (from sign-up) have no history, so they show "New on DoctoLebanon" instead of a year count.

### The layout
A small **CSS grid** (`.profile-grid`) lays the four facts out in two columns (one column on a phone, via a `@media` rule). Then headings (`.psec`) separate "About" and "Availability".

### Concept introduced
- **Generating display data from existing fields** instead of storing everything — less data to maintain, and it scales automatically to new doctors.

---

## 12. New in v0.5 — Polish & mobile (responsive design)

No new features here — just making it look and feel better, especially on phones. The key idea is **responsive design**: the same page rearranges itself for small screens.

### Media queries = "different rules for small screens"
```css
@media(max-width:560px){
  .search .go{flex:1 0 100%}      /* search button takes a full row */
  .search .go .btn{width:100%}    /* ...and stretches across */
  .modal-head,.modal-body{padding:16px}  /* tighter spacing on phones */
}
```
- `@media(max-width:560px){ ... }` means "**only apply these rules when the screen is 560px wide or less**" (i.e. a phone). On a laptop they're ignored.
- This is how one website serves both desktop and mobile without a separate app.

### Small touches that make it feel professional
- `:focus` styles — a green glow appears when you click into an input (helps people know where they're typing, and helps accessibility).
- `.btn:active{transform:translateY(1px)}` — buttons "press down" 1px when tapped (tactile feedback).
- Subtle shadows on the hero and cards add depth.
- `grid-template-columns: ... minmax(min(100%,300px),1fr)` — a responsive grid that never lets a card overflow a narrow phone.

### Concept introduced
- **Media queries** (`@media`) — the foundation of mobile-friendly ("responsive") websites.

---

## 13. New in v0.6 — Tappable phone + Maps link

Two small but useful profile upgrades, and they show how **links can trigger phone/map apps**, not just open web pages.

### A phone number that calls
```js
const TEL_PREFIX = ["03","70","71","76","78","81"];
function genTel(d){
  if(d.tel) return d.tel;                          // doctor's own number if they signed up with one
  const p = TEL_PREFIX[d.id % TEL_PREFIX.length];  // otherwise a stable fake Lebanese number
  const s = String(100000 + (d.id*73813) % 900000);
  return `${p} ${s.slice(0,3)} ${s.slice(3)}`;
}
```
Then in the profile: `<a href="tel:03123456">📞 ...</a>`. The special **`tel:` link** tells a phone "dial this number." (We strip spaces with `.replace(/\s/g,'')` so the dialer gets clean digits.)

### An address that opens Maps
```js
function mapsURL(d){
  const q = encodeURIComponent(`${AREAS[d.id % AREAS.length]}, ${d.c}, Lebanon`);
  return `https://www.google.com/maps/search/?api=1&query=${q}`;
}
```
- `encodeURIComponent(...)` makes the text **safe to put in a web address** (turns spaces and commas into codes like `%20`). Always do this when building a URL from text.
- `target="_blank" rel="noopener"` opens Maps in a new tab safely.

### Concepts introduced
- **Special link schemes** — `tel:` (call) and Maps URLs (navigate). Same `<a href>` you know, pointed at an app instead of a page.
- **`encodeURIComponent`** — safely putting text into a URL.

---

## 14. New in v0.7 — Reason for visit (radios) + attach a document

The booking step now asks **why** you're coming and lets you **attach a file** (like previous test results) — just like the real Doctolib.

### Radio buttons = "pick exactly one"
```html
<label class="radio"><input type="radio" name="bReason" value="new" checked> New consultation</label>
<label class="radio"><input type="radio" name="bReason" value="follow"> Follow-up</label>
<label class="radio"><input type="radio" name="bReason" value="urgent"> Urgent</label>
```
- The trick: **all three share the same `name="bReason"`**. That's what makes them a group where only one can be selected. (Checkboxes — which we used for languages — allow *many*; radios allow *one*.)
- Reading the choice in JavaScript:
```js
const reason = document.querySelector('input[name="bReason"]:checked').value;  // "new" | "follow" | "urgent"
```
- We store the short `value` (`"follow"`) and translate it for display with `reasonText()` — same "store the value, show a translated label" idea from the language feature.

### The file attachment (and an honest limit)
```js
const docFile = $("bDoc").files[0];          // the file the user picked
const docName = docFile ? docFile.name : ""; // we keep only its NAME for now
```
- A `<input type="file">` gives us a list of chosen files; `files[0]` is the first one.
- ⚠️ We deliberately store only the **file name**, not the file's contents. Real file *upload* (saving the actual document somewhere safe) needs a backend server — that's Stage C. For medical documents this also has privacy/security implications (Lebanon's Law No. 81), so it's correct to wait and do it properly.

### Concepts introduced
- **Radio groups** (shared `name`) vs checkboxes — one choice vs many.
- **File inputs** (`type="file"`, `.files`) — and *why* truly storing files needs a backend.

---

## 15. New in v0.8 — Connecting to a real database (Supabase) 🌍

The biggest upgrade so far: doctors no longer live hard-coded in the file — they come from a **shared cloud database**. Change the data once, and *everyone* sees it.

### 1. Load the Supabase library + connect
```html
<script src="https://cdn.jsdelivr.net/npm/@supabase/supabase-js@2"></script>
```
```js
const SUPABASE_URL = "https://qujudggwzlhdbptohwbp.supabase.co";  // WHERE the database is
const SUPABASE_KEY = "sb_publishable_...";                        // WHO we are (publishable = safe in browser)
const sb = supabase.createClient(SUPABASE_URL, SUPABASE_KEY);     // our connection to the cloud
```
- The URL is the *address*; the publishable key is the *ID badge*. (The **secret** key is never used here — it would bypass security; it's only for backend servers.)

### 2. Fetch the doctors — and the idea of "async"
```js
async function loadDoctorsFromCloud(){
  const { data, error } = await sb.from("doctors").select("*").order("id");
  DOCTORS = data.map(shapeDoctor);
}
```
- `sb.from("doctors").select("*")` = "from the doctors table, get all columns." Plain English, and it's a real database query.
- **`await`** is the key new idea: talking to a server **takes time** (a fraction of a second over the internet). `await` means "wait here for the answer before continuing." A function that waits like this is marked `async`.
- Because the data arrives *later*, we **render only after it comes back**:
  ```js
  loadDoctorsFromCloud().then(render);   // fetch first, THEN draw the list
  ```

### 3. A safety net (fallback)
If the internet/database is unreachable, we don't want a blank page — so we catch the error and use the built-in list instead:
```js
try { ...load from cloud... }
catch(e){ DOCTORS = FALLBACK_DOCTORS.map(...); }   // app still works offline
```

### 4. Matching shapes
The database columns are `name, specialty, city...`, but our app code expects `n, s, c...`. `shapeDoctor(row)` translates a database row into the shape the rest of the app already understands — so we didn't have to rewrite everything.

### Concepts introduced
- **Client + server / database** — your code (client) asks a cloud database (server) for data.
- **`async` / `await`** — handling things that take time (network requests). This is huge and you'll use it forever.
- **A query** — `select("*")` is real database language (SQL, dressed up in JavaScript).
- **Publishable vs secret keys** — public ID vs private master key.

> Still TODO in later steps: appointments saved to the cloud (C3), real logins (C4), real file upload (C5).

---

## 16. New in v0.9 — Saving to the database (`insert`)

In §15 you learned to **read** from the database (`select`). Now the doctor sign-up **writes** to it (`insert`), so a new doctor is shared with everyone.

```js
async function submitDoctor(){
  ...validate the form...
  const { error } = await sb.from("doctors").insert({ name, specialty:spec, city, langs, fee, tel });
  if(error){ alert("Sorry, could not save: " + error.message); return; }
  await loadDoctorsFromCloud();   // re-fetch so the new doctor shows in the shared list
}
```
- `sb.from("doctors").insert({...})` = "add this row to the doctors table." We pass an **object** whose keys match the table's columns.
- It's `async`/`await` again, because saving over the internet takes a moment.
- We check `error` — if the save failed (e.g. no permission), we tell the user instead of pretending it worked.
- After inserting, we call `loadDoctorsFromCloud()` again so the list refreshes with the new doctor included.

### Two database permissions we set (RLS policies)
Our `doctors` table now has two rules:
- **"Public can read doctors"** → anyone may `select` (it's a public directory).
- **"Public can add doctors"** → anyone may `insert` (for the demo).

> 🔒 For a real product, "anyone can add a doctor" is too open — we'd require the person to be a **logged-in, verified doctor**. That's what **accounts (C4)** unlock. For now it's fine because it's a demo with fake data.

### What changed in the app's structure
Signed-up doctors used to be saved in the browser (`localStorage`) and merged in. Now **all** doctors — built-in and newly added — come from the one cloud list, so `allDoctors()` simply returns the cloud `DOCTORS`. A doctor counts as "New" (badge) when it has **no rating yet** (`!d.r`), which is true for freshly signed-up doctors.

### Concept introduced
- **`insert`** — writing a new row to a database (the partner of `select`).
- **Checking for errors** after a save.
- **RLS policies** as the database's "who may do what" rules.

---

## 17. New in v0.10 — Real accounts (Supabase Auth) 🔑

People can now **log in**. Supabase has a whole authentication system built in, so we didn't build passwords/security ourselves (which is good — auth is easy to get dangerously wrong).

### The handful of commands
```js
await sb.auth.signUp({ email, password });              // create an account
await sb.auth.signInWithPassword({ email, password });  // log in
await sb.auth.signOut();                                 // log out
const { data } = await sb.auth.getSession();             // is someone logged in?
```
- All `async` (they talk to the server).
- Supabase securely stores passwords (hashed) and manages the login — we never see or store the raw password.

### Staying in sync with `onAuthStateChange`
Instead of manually updating the header everywhere, we **listen** for login/logout events:
```js
sb.auth.onAuthStateChange((_event, session) => {
  currentUser = session ? session.user : null;
  renderAuthUI();   // redraw the header: "Log in" button  ↔  email + "Log out"
});
```
- This fires whenever the user logs in, logs out, or the page loads with an existing session.
- `currentUser` is our single source of truth for "who is logged in." `renderAuthUI()` just reads it and shows the right thing.

### Why you stay logged in after refresh
Supabase saves your **session** in the browser automatically. On startup, `refreshAuth()` calls `getSession()` — if a saved session exists, you're still logged in. (This is a *secure token*, not your password.)

### A real-world note (email confirmation)
By default Supabase emails new users a confirmation link before they can log in. For our demo we either turn that **off** (so sign-up is instant) or create users in the dashboard with **"Auto Confirm."** For a real launch you'd keep confirmation **on** (with a proper email service) so people verify their address — important for a medical app.

### Concept introduced
- **Authentication** — sign up / log in / log out, handled by a trusted service.
- **Sessions & tokens** — how a site remembers you're logged in without re-asking for your password.
- **Event listeners** (`onAuthStateChange`) — reacting to things as they happen, instead of checking constantly.

> Next (C3): now that we know *who* the user is, we can save **appointments** that belong to them — real, private bookings.

---

## 18. New in v0.11 — Real, private appointments (C3) 📅

Bookings now live in the cloud, **owned by the user who made them**, and each person sees only their own.

### The table remembers the owner
```sql
user_id uuid not null default auth.uid() references auth.users(id)
```
- Every appointment row stores a `user_id`. The default `auth.uid()` means "automatically fill in the **currently logged-in user's id**" — so we don't even pass it from the app.

### Privacy comes from the RLS rules
```sql
create policy "see own appointments"   on appointments for select using (user_id = auth.uid());
create policy "create own appointments" on appointments for insert with check (user_id = auth.uid());
create policy "delete own appointments" on appointments for delete using (user_id = auth.uid());
```
- `auth.uid()` = the logged-in user's id. These rules say **"you may only read/add/delete rows that are yours."**
- So when the app runs `select("*")`, the database **only returns the current user's appointments** — privacy enforced by the database itself, not by our code. That's the safe way to do it.

### The three operations in the app
```js
// CREATE — book (user_id auto-filled by the table default)
await sb.from("appointments").insert({ doctor_name, specialty, ... });
// READ — only returns YOUR rows, thanks to RLS
const { data } = await sb.from("appointments").select("*").order("id");
// DELETE — cancel one by its id
await sb.from("appointments").delete().eq("id", id);
```
- `.eq("id", id)` = "where id equals this" — how you target a specific row.
- The badge count uses a neat trick: `select("id", { count:"exact", head:true })` returns *just the number* of your appointments, no data.

### Login is now required to book
`confirmBooking()` checks `if(!currentUser)` and asks the user to log in first — because an appointment must belong to someone.

### Concepts introduced
- **Ownership column + `auth.uid()`** — tying rows to a user.
- **Per-user RLS** — the database guarantees privacy, regardless of the app code.
- **`delete` / `.eq()` filters / counting rows** — more of the database toolkit.

> Appointments, doctors, and accounts are all real and in the cloud now.

---

## 20. New in v0.14–v0.17 — Roles: doctor, admin & verification 🩺🛡️

The app now behaves differently for **three kinds of user** — and the rules live in the **database**, not just the screen.

### A user's "role" is derived from data
- You're **a doctor** if you *own a doctor listing* (`doctors.owner = your id`).
- You're **an admin** if your id is in the `admins` table.
- Otherwise you're **a patient**.

`refreshRoles()` works this out after login, and the header shows the right links (`Dashboard` for doctors, `Admin` for admins).

### Verification: the database hides unverified doctors
New doctors start `verified = false`. The key is the **read rule**:
```sql
create policy "Read verified or own doctors" on doctors for select
  using ( verified = true or owner = auth.uid() );
```
So **patients only ever receive verified doctors** from the database — the hiding is enforced by Postgres itself, not by hoping the app filters correctly. A doctor still sees their own pending listing (the `owner = auth.uid()` part).

### Admin powers via RLS subqueries
Admins can see *all* doctors and approve them, granted by rules that check the `admins` table:
```sql
create policy "admins read all doctors" on doctors for select
  using ( exists (select 1 from admins a where a.user_id = auth.uid()) );
create policy "admins update doctors"   on doctors for update
  using ( exists (select 1 from admins a where a.user_id = auth.uid()) );
```
Approving is just an update: `sb.from("doctors").update({ verified:true }).eq("id", id)` — which only succeeds because that policy allows it.

### Why this is the "right" way
Security lives in the **database rules (RLS)**, so even if the front-end had a bug, a patient still couldn't read unverified doctors and a non-admin couldn't approve anyone. Defense at the data layer — exactly how a real medical app should be built.

### Concepts introduced
- **Roles derived from data** (own a listing → doctor; in `admins` → admin).
- **RLS with subqueries** (`exists (select 1 from admins …)`) for permission checks.
- **`update` to change a row** (approving = flipping `verified`).
- **Three interfaces, one app** — the screen branches on role, but the database enforces it.

---

## 21. Real availability & no double-booking (v0.18–v0.20) 🗓️

Before this, time slots were **fake** (made up by code). Now they're **real**: doctors create their slots, patients book them, and a slot can be taken only once.

### The `slots` table
Each row is one bookable time for a doctor:
```
slots: id · doctor_id · slot_date · slot_time · is_booked
```
- Doctors **add** slots (an `insert`) from their dashboard.
- The **recurring generator** builds many at once: a loop walks each day in a date range, and for each chosen weekday it adds the times — then one bulk `upsert` saves them all (`ignoreDuplicates` so re-running is safe).

### Booking without double-booking (the clever bit)
When a patient books, we don't just trust the screen — we ask the database to reserve it **only if it's still free**:
```js
sb.from("slots").update({ is_booked:true }).eq("id", slotId).eq("is_booked", false).select()
```
- `.eq("id", slotId).eq("is_booked", false)` = "this slot, **but only if it's still free**."
- If it returns **0 rows**, someone grabbed it a split second earlier → we tell the patient "just taken." This is **race-safe** — even two simultaneous clicks can't both win.
- Cancelling sets `is_booked` back to `false` so the slot reopens.

### Concepts
- **A status flag (`is_booked`) + a conditional update** = safe reservations.
- **Generating data in a loop** + bulk `upsert`.

---

## 22. The doctor dashboard: tabs & calendar (v0.21–v0.23) 📅

### Tabs from one variable
The dashboard shows **one section at a time** using a single state variable:
```js
let dashTab = "appts";              // "appts" | "cal" | "avail"
function setDashTab(t){ dashTab=t; showDashboard(); }
```
`showDashboard()` checks `dashTab` and renders only that section. Clicking a tab changes the variable and re-draws. (Same idea you'll see in every app: *state → re-render*.)

### The calendar
- Each appointment stores its date as **`appt_date`** (a real date), so we can place it on a grid.
- We **bucket** appointments by date: `byDate[appt_date] = [appointments…]`.
- **Date math** builds the views: start-of-week (to line up Mon–Sun), and a 42-cell month grid (6 weeks). `calNav()` moves the reference date by a day/week/month.

### Concept
- **State-driven views** (one variable decides what's shown) and **working with dates** (grouping, grids, navigation).

---

## 23. Cancelling & rescheduling (v0.24–v0.25) 🔁

### A `status` instead of deleting
When a doctor cancels, we **don't delete** the appointment — we mark it:
```
appointments.status = 'cancelled_by_doctor',  appointments.doctor_note = '...'
```
Why? So the **patient can still see** it happened (and the doctor's note). A deleted row would just vanish silently. Representing situations with a **status field** is a core data-modelling idea.

### Reschedule = move between slots
`doReschedule()` does three careful steps:
1. **Reserve** the new slot (the same race-safe conditional update).
2. **Free** the old slot.
3. **Update** the appointment to point at the new slot/date/time (and reset status to `booked`).
If step 3 fails, we roll back step 1 (free the new slot again) so nothing is left half-done.

### Concept
- **Status fields** to model real-world states; **multi-step updates** with a rollback if something fails.

---

## 25. Search at scale: server-side filtering + pagination (v0.27–v0.28) 🚀

The old way fetched **every** doctor into the browser and filtered here — fine for 12, terrible for 10,000. Now the **database does the work** and sends back only one page.

### Ask the database, not the browser
```js
let q = sb.from("doctors").select("*", { count:"exact" }).eq("verified", true);
if(spec)  q = q.eq("specialty", spec);     // filters run in the database…
if(city)  q = q.eq("city", city);
if(name)  q = q.ilike("name", `%${name}%`); // ilike = case-insensitive "contains"
if(langF) q = q.contains("langs", [langF]); // array column contains this value
q = q.range(page*12, page*12+11);           // …and only THIS page of 12 rows comes back
const { data, count } = await q;
```
- `count:"exact"` tells us the **total** matches (for "Page 1 / N") without downloading them.
- `range(0,11)` = rows 0–11; `range(12,23)` = the next page. The database skips the rest.
- So the browser only ever receives **12 doctors**, whether the total is 12 or 12,000. That's how it scales.

### State + re-render again
`page` is a state variable; **Prev/Next** change it and re-run the query. Changing a filter resets to page 0 (`search()`), so you don't get stranded on a page that no longer exists.

### Concept
- **Server-side querying** (`.eq/.ilike/.contains`) and **pagination** (`.range`, `count`) — the standard way to handle large lists without melting the browser.

---

## 24. Reviews & ratings (v0.26) ⭐

### Averages from rows
Each review is a row (`doctor_id`, `rating`, `comment`). We load them all and compute each doctor's **average**:
```js
reviewStats[doctor_id] = { sum, count }   // average = sum / count
```
`ratingTag()` then shows: the **review average** if any, else the seeded rating, else "New".

### One review per patient (editable)
`unique (doctor_id, user_id)` + `upsert` means a patient has exactly one review per doctor, and submitting again **updates** it.

### Safety: escaping user text
Reviews are free text from anyone, so before putting a comment on the page we **escape** it:
```js
function esc(s){ return s.replace(/[&<>"]/g, …) }   // turns < into &lt;, etc.
```
This prevents someone sneaking HTML/script into a comment (a class of bug called **XSS**). Always escape untrusted text you display.

### Concept
- **Aggregating data** (averages from many rows), **upsert** for one-per-user records, and **escaping user input** for safety.

---

## 19. New in v0.13 — Real document upload (C5, Supabase Storage) 📎

The booking's "attach a document" now **actually uploads the file** to the cloud — privately.

### Files live in "Storage" (separate from the database)
Databases store *text/numbers*; **files** (PDFs, images) go in **Storage**, organized into **buckets**. We made a **private** bucket called `documents`.

### Uploading
```js
const safe = docFile.name.replace(/[^\w.\-]/g,"_");      // make the filename URL-safe
const docPath = `${currentUser.id}/${Date.now()}_${safe}`; // store it in a folder named after the user
await sb.storage.from("documents").upload(docPath, docFile);
```
- The path starts with the **user's id as a folder** — that's the trick the security rules use to keep files private.
- We save `docPath` on the appointment row so we know where the file is.

### Privacy = folder-per-user + signed links
The storage rules (the SQL you ran) say: *"you may only upload to / read from a folder named with your own user id."* So nobody can reach anyone else's files.

To actually view a private file, we generate a **signed URL** — a temporary link that works for 60 seconds, then dies:
```js
const { data } = await sb.storage.from("documents").createSignedUrl(path, 60);
window.open(data.signedUrl, "_blank");
```
This is the proper way to share sensitive files: no permanent public link, and only the owner can mint one.

### Concepts introduced
- **Object storage / buckets** — where files live (vs the database for structured data).
- **Folder-per-user privacy** + storage RLS.
- **Signed URLs** — temporary, owner-only links to private files.

> 🏁 **Stage C is complete.** DoctoLebanon now has: a shared cloud database, real accounts, private appointments, and private file upload. That's a real full-stack app. Remaining polish (optional): turn email-confirmation back on for sign-up, and restrict "add a doctor" to logged-in users.
