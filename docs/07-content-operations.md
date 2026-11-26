# 07 — Content Operations: Data Collection & Verification

No public API lists "baby sensory class in Florentin on Tuesdays at 10:00." MOMents' content is built and maintained by hand, so this process matters as much as the code.

## 1. Content model recap
- **Places** (low upkeep): playgrounds, parks with shade, baby-friendly cafés, nursing/changing rooms, libraries with baby corners, mall play areas.
- **Recurring classes** (medium upkeep): weekly baby music, sensory, baby yoga/massage, development groups, community center (matnas) parent-and-baby programmes, toddler gyms.
- **Not in MVP:** one-off events (high upkeep, go stale in days).

## 2. Launch target

| Type | Target count (few central Tel Aviv neighborhoods) |
|---|---|
| Playgrounds & parks | 40–60 |
| Baby-friendly cafés / nursing & changing spots | 20–30 |
| Recurring classes | 40–60 |
| Group directory entries | 10–20 |
| **Total listings** | **~100–150** |

Rule of thumb: a user opening Explore with default filters (3 km, child's age, this week) should see **at least 15 results**.

## 3. Sources (where to find content)

| Source | Good for | Notes |
|---|---|---|
| Municipality website & open-data portals (Tel Aviv-Yafo, data.gov.il) | Playgrounds, parks, community centers, libraries | Check whether a dataset exists and **check its licence** before importing; may allow a bulk start for playgrounds |
| Community centers (matnasim) programme pages / brochures | Parent-and-baby classes | Programmes change by term; record term dates in `valid_from/valid_until` |
| Class providers' websites & Instagram | Music, sensory, yoga classes | Confirm schedule by message/phone; ask permission for photos |
| Malls' websites | Play areas, nursing rooms, kids' programmes | |
| Local mom WhatsApp/Facebook groups | Recommendations, hidden gems | Use as leads only; verify independently; ask group admins about being listed in the directory |
| Your own walks | Playgrounds, cafés, amenities, photos | Best photos and the most reliable amenity data |
| User tips (in-app) | Ongoing growth | Review queue in admin |

**Don't:** copy text or photos from websites or Google Maps without permission, or scrape platforms whose terms forbid it. Write your own short descriptions; use your own photos or images with written permission (record in `credit`).

## 4. Verification protocol

A listing is **published** only after at least one of:
1. Confirmed directly with the organizer/venue (phone, WhatsApp, email), **or**
2. Confirmed on the organizer's official website/social page, updated within the last 3 months, **or**
3. Visited in person (places).

Record how it was verified in `source_reference` (admin-only).

| Listing type | Re-verify every | Becomes `stale` after | Auto-archived after |
|---|---|---|---|
| Recurring class | 60 days, and at each term start (Sept, Jan, after Passover, summer) | 60 days | +30 days stale |
| Place | 180 days | 180 days | +30 days stale |
| Group directory entry | 90 days (link still works, still active) | flagged `needs_check` | admin decision |

User reports of "closed" or "wrong time" move a listing to the top of the verification queue.

## 5. Time budget (you as sole operator)

| Activity | Estimate |
|---|---|
| Initial entry | ~10 min per listing with quick add → 150 × 10 min ≈ **25 h** (spread over ~12 weeks during Phases 3–4) |
| Photos (own walks) | ~6–10 h total |
| Group outreach (consent from admins) | ~5 h |
| Ongoing maintenance | ~**2–3 h/week**: verification queue, tips, reports, link checks |

If ongoing maintenance regularly exceeds ~4 h/week, it's a signal to (a) shrink the area, (b) recruit a volunteer "neighborhood ambassador" with a `moderator` role, or (c) start the organizer portal (v1.3) so organizers maintain their own schedules.

## 6. Tips workflow
1. User submits a tip → `pending`.
2. Admin queue shows nearby existing listings (within 100 m) to catch duplicates.
3. Admin verifies using §4 → "Create listing from tip" (pre-filled) → publish → tip `accepted` (user sees "Added — thank you!").
4. Or `declined` / `duplicate` with an internal note.

Target: review tips within 7 days.

## 7. Data quality fields shown to users
- "Verified on 12 Nov 2026" on every listing.
- "May be outdated — last verified 3 months ago" badge on stale listings.
- "Report wrong info" on every listing.
- Organizer contact shown so users can confirm before going.

## 8. Path to scale (post-MVP)
1. **Organizer portal (v1.3):** organizers claim their listing (`organizers.owner_user_id`), submit schedule updates → admin approves → `source = organizer`.
2. **Partnerships:** community centers and malls send term schedules in a simple spreadsheet template → bulk import tool in admin.
3. **Ambassadors:** trusted local moms with the `moderator` role verify listings in their neighborhood.
4. Only then expand to new neighborhoods/cities, one at a time, using the same density target.
