# MOMents — Planning Docs

> Status: **Draft for approval** · Last updated: 2026-11-26
> No implementation code will be written until this plan is approved.

MOMents is a mobile companion for mothers of babies and young children: discover nearby baby-friendly places and classes, get ready for every outing with smart checklists, and find local mom groups.

## Documents

| # | Document | Covers (deliverable numbers from the brief) |
|---|---|---|
| 01 | [Product Requirements (PRD)](01-prd.md) | PRD (1), MVP scope & prioritization (2), assumptions, missing requirements |
| 02 | [UX: Flows, IA, Screens](02-ux-flows-ia-screens.md) | User flows (3), Information architecture (4), Screen inventory (5) |
| 03 | [Technical Architecture](03-technical-architecture.md) | Architecture (6), Tech comparison & recommendation (7), API structure (10), Auth & authorization (11) |
| 04 | [Data Model](04-data-model.md) | Database schema (8), ERD (9), Row Level Security |
| 05 | [Security & Privacy](05-security-privacy.md) | Security & privacy (12), legal/store checklist |
| 06 | [Roadmap & Risks](06-roadmap.md) | Roadmap (13), complexity estimates (14), risks (15) |
| 07 | [Content Operations](07-content-operations.md) | Realistic data collection & verification strategy for activities |

## Decision log (agreed in the planning session)

| Area | Decision |
|---|---|
| Product name | **MOMents** (bundle id e.g. `com.moments.app` — confirm availability) |
| Nature | Side project to test demand; lean and cheap to run, but structured to grow |
| Team | 1 UX/UI designer (also sole content operator) + 1 part-time React/TypeScript developer (~15–20 h/week) |
| Platform | **Android first**; iOS later from the same codebase |
| Launch area | A few central Tel Aviv neighborhoods |
| Content | **Permanent places + recurring weekly classes**; no one-off dated events in MVP |
| Content sources | Admin-entered + user "Suggest a place/class" tips (moderated). No organizer portal in MVP |
| Community (MVP) | **Bridge directory**: curated links to existing WhatsApp / Facebook mom groups. No in-app posts |
| Audience | Open to all, mom-first brand. Neutral wording ("parent") in forms |
| Sign-up | **Required** (Google + email OTP). Value screens shown before sign-up |
| Languages | Hebrew + English UI; content in Hebrew (English optional per item). RTL is a first-class layout |
| Child data | Birth month/year + optional private nickname. Never shown to others |
| Maps | Google Maps via `react-native-maps`, lightly styled. Geocoding happens once, in admin |
| Admin | Refine-based web admin with a "quick add" flow, built **before** the Explore screens |
| Design | Figma variables → tokens file → app theme. Light **and dark** mode in MVP |
| Extras in MVP | Local (on-device) reminders for saved classes; dark mode |
| Analytics | Activity engagement events only, stored privately in our own database |
| Monetization | None in MVP; schema reserves `is_sponsored` and organizer tables |
| Legal | Templates + requirements checklist; recommend a 1-hour privacy-lawyer review before launch |
| Plan format | Markdown in this repo |

## Pricing note

Third-party pricing referenced in these docs was checked on 2026-11-26 and **must be re-verified before launch** — vendors change it often (e.g. Google Maps replaced its $200 monthly credit with per-SKU free caps in March 2025). Items marked **(verify)** were not confirmed from an official source.
