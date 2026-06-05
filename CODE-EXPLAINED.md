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
├── PROJECT-PLAN.md           ← the living plan & progress log
├── CODE-EXPLAINED.md         ← this file (how the code works)
├── MY-NOTES.md               ← your learning notebook (lessons + your notes)
└── CONTEXT-FOR-CLAUDE.md     ← Claude's running memory of the project
```

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
