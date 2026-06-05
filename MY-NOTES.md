# My Notes — learning DoctoLebanon

> This is YOUR notebook. Jot questions, "aha" moments, and things to try.
> Claude adds each lesson here; you add your own notes and answers below them.

---

## 📒 How I use this file
- After each lesson, write **one sentence in my own words** about what I learned.
- When something confuses me, write it under **❓ My questions** and ask Claude.
- When I want to try something, add it under **🧪 To try**.

---

# Lessons

## Lesson 1 — Variables + Arrays (2026-06-06)

**The 3 things to remember:**
1. A **variable** is a name for a value. We make one with `const`:
   ```js
   const city = "Beirut";   // "city" now means "Beirut"
   ```
   `const` = the name won't be reassigned to something else. (There's also `let` for values that change.)

2. An **array** is a **list**, written with square brackets `[ ]`:
   ```js
   const CITIES = ["Beirut", "Tripoli", "Sidon (Saida)"];
   ```
   - Count items with `.length` → `CITIES.length` is `3`.
   - Get one item by its **position number (starting at 0!)** → `CITIES[0]` is `"Beirut"`, `CITIES[1]` is `"Tripoli"`.

3. Arrays can hold **objects** (things with labels). In our app, `DOCTORS` is a list of doctor objects:
   ```js
   const DOCTORS = [
     { n:"Dr. Rana Khoury", s:"Dermatologist", c:"Beirut" },
     { n:"Dr. Karim Haddad", s:"Cardiologist", c:"Beirut" }
   ];
   ```
   - `DOCTORS[0]` → the first doctor object.
   - `DOCTORS[0].n` → `"Dr. Rana Khoury"` (the `.n` reaches inside for the "name" label).

**🧪 Exercise (do this in the browser):**
1. Open `index.html`, press **F12**, click the **Console** tab.
2. Type each line, press Enter, see the result:
   - `CITIES` → shows the whole city list
   - `CITIES.length` → how many cities?
   - `DOCTORS.length` → how many doctors?
   - `DOCTORS[2].n` → which doctor's name? (remember: 2 = the *third* one)
3. **Change-one-thing:** in `index.html`, find the `CITIES` line, add `"Aley"` to the list, save, refresh. Look at the City dropdown in the search box. 🎉

**✍️ What I learned (write in my own words):**
> _(your turn — write one sentence here)_

**❓ My questions:**
> _(write anything confusing here)_

---

# 🧪 To try (my ideas)
- _(add things you'd like to change or build)_

# ❓ My open questions
- _(anything you're wondering about)_
