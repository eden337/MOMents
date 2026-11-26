# 01 — Product Requirements Document (PRD)

## 1. Summary

**MOMents** helps mothers of babies and young children answer one question before leaving home:
*"Where can we go today, what do I need to bring, and where are the other moms?"*

**Value proposition:** Discover nearby activities, stay organized, and connect with mothers around you.

The MVP is a **demand test** in a few central Tel Aviv neighborhoods. It must prove that mothers will use a dedicated app to find outings — before we invest in an in-app community, organizer tools, or monetization.

## 2. Problem

| Pain | Today's workaround | Why it fails |
|---|---|---|
| Finding baby/toddler activities nearby | Facebook groups, WhatsApp, Instagram, word of mouth | Scattered, unsearchable, outdated, no filtering by age or distance |
| Knowing if a place is baby-friendly | Asking in groups | Slow, inconsistent answers |
| Remembering what to pack | Memory, phone notes | Forgetting items is stressful with a baby; needs change with age and outing type |
| Meeting other mothers locally | Joining random groups | Hard to find the *right* group for your area and child's age |

## 3. Goals and non-goals

### Goals (MVP)
1. A mother in the launch area can find a suitable place or class for her child's age within **60 seconds** of opening Explore.
2. Every published listing shows when it was **last verified**, so users can trust the data.
3. The checklist is useful from the first outing, works **offline**, and adapts to outing type and child's age.
4. Users can discover relevant local mom groups (directory) by area and child's age.
5. Hebrew (RTL) and English (LTR) are equally polished.

### Non-goals (MVP)
- In-app posts, comments, chats, or "discover moms nearby" people lists.
- One-off dated events, ticketing, payments, bookings.
- Organizer self-service accounts.
- Remote push notifications, recommendations engine, iOS release.

## 4. Target users

**Primary:** mothers aged 24–40 with children aged 0–24 months, especially first-time mothers and mothers on maternity leave.
**Secondary:** mothers of toddlers/preschoolers (2–5 y), other parents and caregivers.

### Personas

| Persona | Context | Top need | MVP feature |
|---|---|---|---|
| **Noa, 31, first-time mom, 4-month-old, on maternity leave** | Long weekday mornings, feels isolated, unsure what's age-appropriate | "Something to do today, close by, for a 4-month-old" | Explore with age + "today" filters; group directory |
| **Maya, 36, two kids (8 mo + 3 y), works part-time** | Limited time, juggles two age ranges | "Plan a Friday morning that works for both kids" + "don't forget anything" | Multi-child age filter; checklist templates |
| **Dana, 28, recently moved neighborhoods** | No local network | "Find mom groups near my new home" | Group directory by area |

## 5. Success metrics

Agreed focus: **activity engagement**.

| Metric | Definition | Initial target (first 8 weeks after launch) |
|---|---|---|
| Detail view rate | % of weekly active users who open ≥1 listing details | ≥ 60% |
| Intent actions per WAU | (directions taps + calendar adds + registration/contact taps + saves) / WAU | ≥ 1.5 / week |
| Save rate | % of users with ≥1 saved listing | ≥ 40% |
| Week-4 retention | % of a signup cohort active in week 4 | ≥ 20% (directional) |
| Secondary: checklist use | Checklists started per WAU | tracked, no target |
| Secondary: group demand | "Join group" taps / WAU | tracked — signals whether to build in-app community |

Targets are hypotheses to refine; the main point is that the events are instrumented from day one.

## 6. MVP scope and prioritization (MoSCoW)

### Must have (MVP)
| ID | Feature | Notes |
|---|---|---|
| M1 | Onboarding (4 value screens) → sign-up → personalization | Sign-up required; personalization fields optional except name |
| M2 | Auth: Google + email one-time code | Apple added with iOS |
| M3 | Profile: first name, optional photo, general area, children (birth month/year, optional nickname), preferred categories | |
| M4 | Explore: map + list of places and classes | Clustered map pins, list sorted by distance |
| M5 | Filters: category, child age, distance, day/time ("today", "this week", weekday, morning/afternoon), free only | Defaults to the user's children's ages |
| M6 | Location: "use my location" (foreground only) or pick an area | Precise location never stored on server |
| M7 | Listing details page | Name, description, address, schedule/opening hours, age range, price, organizer, contact/registration, images, last-verified badge |
| M8 | Save to favorites | |
| M9 | Add to calendar | Opens system calendar "new event" UI; next session or weekly recurrence |
| M10 | Directions via Waze / Google Maps | Deep links, no API cost |
| M11 | Report wrong info on a listing | Goes to admin queue |
| M12 | Smart checklist | Outing types, suggestions by age, check/uncheck, add/remove, templates, reset, progress, offline |
| M13 | Home dashboard | Greeting, today near you, checklist quick start, saved/upcoming, suggested groups, shortcuts |
| M14 | Community directory | Curated WhatsApp/Facebook groups filtered by area, child age, interest; "report broken link" |
| M15 | Suggest a place/class (tips) | Moderated queue |
| M16 | Local reminders for saved classes | On-device scheduling, user-chosen lead time |
| M17 | Settings: language (HE/EN), theme (system/light/dark), reminders, privacy, account deletion, legal links | |
| M18 | Admin web app | Listings CRUD + quick add, geocoding, images, schedules, verification, tips review, reports, groups directory, categories, areas, basic analytics |
| M19 | Analytics events + crash reporting | Privacy-safe |
| M20 | Legal: terms, privacy policy, consent records, in-app + web account deletion | Store requirements |

### Should have (MVP if time allows, else v1.1)
- Free-text address search (geocoded on submit, rate-limited).
- "Frequently used" checklist items surfaced at the top.
- Share a listing (system share sheet; deep link opens app or store).
- Listing amenities filter (changing table, nursing room, stroller-accessible, shade).

### Could have (later)
- Notification center (only meaningful once server-side notifications exist).
- Weather-aware checklist suggestions (sunscreen/hat in summer).
- Offline caching of the map area.

### Won't have in MVP (planned roadmap)
- iOS + Sign in with Apple → **v1.1**
- In-app community: groups, posts, comments, playdates, blocking, moderation tools → **v1.2**
- Remote push, organizer portal, one-off events, personalized recommendations, sponsored listings → **v1.3+**
- Location-based "leaving home" reminders → later (background location is a privacy and store-review burden)

## 7. Functional requirements and acceptance criteria

### 7.1 Onboarding & sign-up (M1, M2)
- Four swipeable value screens (Welcome, Activities, Checklists, Community) with Skip.
  - Community screen copy adjusted to the directory model, e.g. *"Find your local mom groups."*
- Sign-up: "Continue with Google", "Continue with email" (6-digit code). Terms + privacy acceptance recorded with version and timestamp.
- Personalization: first name (required), general area (picked from list; optional), children (birth month + year, optional nickname; skippable, can add multiple), preferred categories (optional).
- Location permission is **not** requested during onboarding; it is requested just-in-time on first Explore visit with an explainer.

**Acceptance:** a new user can go from install to Home in ≤ 90 seconds; all optional steps skippable; returning users bypass onboarding; works in HE and EN.

### 7.2 Explore (M4–M11)
- Map and list share the same filter state. Toggle between them with one tap.
- Pins clustered; selected pin shows a bottom card (name, category, distance, next session, price badge).
- List items: image, name, category, distance bucket ("1.2 km"), age range, next session, free/paid, saved state.
- Distance filter options: 1 / 3 / 5 / 10 km (default 3 km).
- Age filter defaults to the user's children; editable; "any age" allowed.
- Time filter: Today, Tomorrow, This week, specific weekday; morning (before 12:00) / afternoon (12:00–17:00) / evening.
- Places (parks, cafés, nursing rooms) appear regardless of time filters unless "open now" is selected and opening hours exist.
- Listings older than the verification threshold show a "may be outdated" badge.
- Empty state: suggest widening distance or removing filters; offer "Suggest a place".

**Acceptance:** results update in < 1 s on a mid-range Android device on 4G for the launch-area dataset; filters persist during the session; RTL layout mirrors correctly, map controls included.

### 7.3 Listing details (M7–M11)
- Sections: image carousel, title, category chips, age range, price, schedule (weekly sessions with exceptions) or opening hours, address + mini-map, organizer, contact (phone, WhatsApp, website, registration link), description, amenities, "last verified on …", report.
- Primary actions: Save, Directions, Add to calendar, Register/Contact (when available), Share (should-have), Remind me (classes).

### 7.4 Smart checklist (M12)
- Start from outing type: Park, Mall, Doctor appointment, Short walk, Day trip, Travel, Overnight stay — or from a saved template.
- Suggestions computed from rules: outing type × child's age bucket (0–6 m, 6–12 m, 12–24 m, 2–5 y). Multiple children → union of items, de-duplicated, quantity hints where relevant.
- Check/uncheck, add custom item, remove item, reorder (should), progress bar ("8 of 12 packed").
- "Save as template", "Reset for next outing" (unchecks all, keeps items).
- Works fully offline; syncs to the account when online.
- Frequently used custom items are suggested in future checklists (should).

**Acceptance:** a checklist can be created, edited and completed in airplane mode; data survives app restarts and syncs after reconnecting without duplicating items.

### 7.5 Home (M13)
Order (top → bottom, designed to avoid scrolling for the first two blocks):
1. Greeting + child context ("Good morning, Noa · Ella is 7 months").
2. **Happening today near you** (horizontal cards, max 5).
3. **Packing for an outing?** — quick-start chips for outing types + resume in-progress checklist.
4. **Your saved classes this week** (next sessions).
5. **Groups for you** (directory matches by area and age, max 3).
6. Shortcuts are covered by the bottom navigation; no duplicated shortcut grid.

> Challenge: the brief asks for a notification center on Home. Without server-side notifications it would always be empty, so it is **deferred**; reminders are managed in the saved listing and in Settings.

### 7.6 Community directory (M14)
- List of curated groups: name, platform icon (WhatsApp/Facebook/Telegram), area, child age range, tags (first-time moms, maternity leave, weekend playdates…), member-count note (optional, admin-entered), last checked.
- Filters: area, child age, interest tag.
- "Join" opens the external link. A one-time explainer says the conversation happens on the external platform and its rules apply.
- "Report a problem" (broken link, spam, inappropriate).
- Groups are only added with the group admin's consent.

### 7.7 Tips (M15)
- Form: name, type (place/class), category, address or area, website/Instagram link, notes. Optional photo is **not** in MVP (avoids image moderation).
- Rate-limited (e.g. 5 per user per day). User sees status of own tips (pending/accepted/declined).

### 7.8 Local reminders (M16)
- On a saved class: "Remind me" → choose 30 min / 1 h / 3 h / evening before.
- Scheduled on device for the next 4 weeks of sessions; refreshed on app open.
- Android 13+ notification permission requested only when the user first sets a reminder.

### 7.9 Settings & account (M17, M20)
- Language: Hebrew / English (switching triggers a quick app reload to apply RTL/LTR).
- Theme: System / Light / Dark.
- Privacy: what we store and why, edit/remove children, export request (email link in MVP), delete account.
- Delete account: immediate in-app flow with confirmation; also a public web page for deletion requests (Google Play requirement).

### 7.10 Admin (M18)
See [03 — Technical Architecture §6](03-technical-architecture.md#6-admin-dashboard) for screens.

## 8. Assumptions
1. You (the designer) can dedicate ~25 hours before launch to enter ~100–150 listings, then ~2–3 hours/week to maintain them (see [07](07-content-operations.md)).
2. Existing local WhatsApp/Facebook mom groups will agree to be listed.
3. The developer works ~15–20 hours/week consistently.
4. The target audience in the launch area mostly reads Hebrew; English is for expats and immigrants.
5. Google's mobile Maps SDK remains a no-cost SKU, and our geocoding volume stays inside free caps (admin-only geocoding).
6. Android-first is acceptable even though a portion of the target audience uses iPhones (they join at v1.1).

## 9. Missing requirements & open questions (need your input before/during Phase 1)

| # | Question | Default if not answered |
|---|---|---|
| Q1 | Exact launch neighborhoods (list) | Lev HaIr, Old North, Florentin, Neve Tzedek (example — please confirm) |
| Q2 | Brand assets: logo, app icon, font choice supporting Hebrew + Latin | Designer delivers in Phase 0; candidate fonts: Rubik, Heebo, Assistant (all free on Google Fonts) |
| Q3 | Copy tone and Hebrew gendered language: address the user in feminine form? | Feminine in Hebrew (mom-first brand), with gender-neutral fallback strings ready |
| Q4 | Who are the 12+ closed-test testers required by Google Play for new personal accounts? | Recruit from friends / one mom group (see roadmap risk R3) |
| Q5 | Domain + email for privacy policy, support and deletion requests | Buy a domain in Phase 0 |
| Q6 | Verification thresholds: when does a listing become "stale"? | Classes 60 days, places 180 days |
| Q7 | Should stale listings be hidden or shown with a badge? | Shown with badge for 30 days, then auto-hidden |
| Q8 | Developer account: personal or organization (company) on Google Play? | Personal (side project) — triggers closed-testing requirement |
| Q9 | Will you photograph places yourself? Image rights for classes? | Own photos or explicit permission from organizers; placeholder illustrations otherwise |
| Q10 | Support channel | Email only |
