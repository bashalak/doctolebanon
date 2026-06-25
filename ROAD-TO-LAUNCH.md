# DoctoLebanon — Road to a real launch

> What's between the current demo (feature-complete, v0.37) and serving **real patients & doctors in Lebanon**.
> The product features are essentially done — this is the "make it safe, legal, and operational" layer.
> Legend: 🟩 code (we can do) · 🟦 needs a service/setup · ⚖️ needs a lawyer · 💵 needs money/business · 🧑‍⚕️ people/ops

_Last updated: 2026-06-06_

---

## 1. Security hardening 🔒 (before any real patient data)
- [ ] 🟩 **Lock the `verified` column** so a doctor can't self-approve (admin-only). *(in progress — trigger)*
- [ ] 🟩 **Reviews require a real visit** (only patients who had an appointment can review).
- [ ] 🟩 **Move slot booking server-side** (an Edge Function) so users can't flip arbitrary `is_booked` flags. *(currently a permissive demo policy)*
- [ ] 🟦 **Re-enable email confirmation** on signup (needs the email service below).
- [ ] 🟩 **Review every RLS policy** for medical-data privacy.

## 2. Server-side pieces ⚙️ (small backend / Supabase Edge Functions)
- [ ] 🟦 **Email**: booking confirmations + reminders (provider e.g. Resend / SMTP).
- [ ] 🟦💵 **SMS** reminders (paid provider; Lebanon gateway or Twilio).
- [ ] 🟦 **True account deletion** (erase the auth login record — service-role function).
- [ ] 🟦 Scheduled reminders (cron) + a cached average-rating column.

## 3. Legal & compliance ⚖️ (the biggest real blocker)
- [ ] ⚖️ Real **Terms of Service** + **Privacy Policy** (lawyer-reviewed).
- [ ] ⚖️ Comply with **Lebanon's Law No. 81** (electronic transactions & personal data: consent, encryption, user rights).
- [ ] ⚖️ **Medical regulations** + liability for listing doctors and storing medical documents.
- [ ] ⚖️ Data **hosting region** & cross-border data rules (where Supabase stores data).

## 4. Payments 💵 (only if you charge)
- [ ] 💵 Decide model (doctor subscription, like Doctolib, vs booking fee).
- [ ] 💵 Payment integration — **Lebanon is hard** (cards limited; OMT / Whish / cash). Needs business + banking.

## 5. Operations & launch readiness 🚀
- [ ] 🧑‍⚕️ **Recruit real, verified doctors** (the chicken-and-egg problem — as hard as the code).
- [ ] 🧑‍⚕️ A real **license-verification process** (check the Lebanese Order of Physicians).
- [ ] 🟩 Thorough **testing** (edge cases, multiple devices) + bug-fixing.
- [ ] 🟦 **Custom domain** (e.g. doctolebanon.com) + branding/logo.
- [ ] 🟦 **Error monitoring**, backups, data export.
- [ ] 🧑‍⚕️ Support channel + doctor onboarding materials.

---

## Honest magnitude
- **Technically "usable"** (security pass + email + domain): ~a few weeks part-time. **Close.**
- **Legally & operationally ready for real patients in Lebanon:** bigger — lawyer + payments + recruiting doctors. Needs **time, money, and people**, not just code.

**The hard part — building a working product — is done.** ✅
