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
