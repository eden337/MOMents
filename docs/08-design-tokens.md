# 08 — Design Tokens

- **Source of truth:** [`design/tokens.json`](../design/tokens.json). The app theme (Phase 0) will be generated from this file; Figma variables mirror it.
- **Figma file:** [MOMents — Design Foundations](https://www.figma.com/design/DhZRSQa8oVDK0Q5nlyp5lF) (page "Foundations" has light/dark swatch boards and the type ramp).

## What exists in Figma

| Collection / style | Contents | Scopes |
|---|---|---|
| `Primitives` (1 mode) | 36 raw colors (cream, peach, blue, coral, mint, amber, neutral, night) | Hidden from pickers — use semantic tokens |
| `Color` (Light) | 29 semantic colors: `bg/*`, `action/*`, `text/*`, `icon/*`, `border/*`, `status/*` aliased to primitives | Per group (fills / text / strokes) |
| `Color (Dark)` | Same 29 names, dark values | Same |
| `Dimension` | `spacing/0…16` (Tailwind-style scale), `radius/none…full`, `size/touch-target`, `size/icon-*` | Gap / corner radius / width-height |
| Text styles | `display`, `title-1…3`, `body-lg`, `body`, `body-strong`, `body-sm`, `label`, `caption` — Rubik (Hebrew + Latin) | — |
| Effect styles | `shadow/card`, `shadow/sheet` | — |

Every variable has WEB code syntax (e.g. `var(--color-text-primary)`, `var(--spacing-4)`), so Dev Mode shows the token name the code will use.

## Limitation: one mode per collection (Figma Starter plan)

Figma Starter allows only **1 mode per variable collection**, so Light and Dark are two collections with identical variable names instead of two modes. To preview dark mode, designs use the `Color (Dark)` variables (or duplicate a frame and swap the library variables).

**When you upgrade to Figma Professional:** merge `Color (Dark)` into `Color` as a second mode. Then any frame can switch Light/Dark with one click. Names stay the same, so code is unaffected.

## Accessibility decisions (vs. the original palette)

| Original | Problem | Token decision |
|---|---|---|
| Coral `#FF6B6B` | 2.6:1 on cream — fails for text; white text on it is 2.8:1 | Kept as `coral/400` (accent fills, dark-mode pressed). Primary button = `coral/600 #C93C3C` with white text (5.0:1). Coral text = `coral/700 #B83232` (5.6:1) |
| Cool Gray `#9CA3AF` | 2.4:1 on cream — fails for text | Kept as `neutral/400` for **disabled** only. Secondary text = `neutral/600 #5F6672` (≥ 4.5:1 on canvas, surface and subtle) |
| — | Input borders need ≥ 3:1 | `border/strong` = `neutral/500 #8A919C` (3.0:1) |
| Peach / Powder Blue / Mint | Fine as surfaces | `bg/brand-*`; use `text/on-brand` (charcoal) on them — never `text/secondary` |

All text/UI pairs listed in `tokens.json` were checked against WCAG 2.1 AA in both themes (script result: 0 failures). Re-run the check whenever a color changes.

## Rules for designing with tokens
1. Use only **semantic** variables (`Color` / `Color (Dark)`) in screens, never primitives.
2. Text colors: `text/*` only. On brand surfaces (peach/blue/mint), use `text/on-brand`.
3. Spacing/padding from `spacing/*`; radii from `radius/*` (cards = `radius/lg` 16, sheets = `radius/xl` 24, chips = `radius/full`).
4. Touch targets ≥ `size/touch-target` (48).
5. Use text styles for all text; don't override size or weight locally.
6. Need a new color? Add it to `tokens.json` first (primitive + semantic for **both** themes), then Figma.

## Core components (Figma page "Components")

All fills, strokes, padding, gaps and radii are bound to the variables above.

| Component | Variants / properties | Notes |
|---|---|---|
| `Button` | Variant = Primary / Secondary / Ghost × State = Default / Pressed / Disabled; text prop `Label` | Min height 48 (touch target), full radius |
| `Chip` | Selected = true / false; text prop `Label` | Visual height ~36 — code adds hitSlop to reach 48 |
| `Badge` | Type = Free / Verified / Outdated | Label is fixed per type |
| `ChecklistItem` | Checked = true / false; text prop `Label` | Whole row is the tap target; checked = strikethrough + secondary text |
| `TabItem` | State = Active / Inactive; text prop `Label`; instance-swap `Icon` | Active = coral pill (`bg/accent-subtle`) + Fill icon + medium coral label; min height 48 |
| `TabBar` | Language = English (LTR) / Hebrew (RTL) | 360 wide, 5 tabs (Home, Explore, Checklist, Community, Profile / בית, גילוי, רשימה, קהילה, פרופיל); Hebrew is mirrored |
| `BottomSheet` | text prop `Title`; booleans `Show header`, `Show footer`; instance-swap `Content` (`SheetContent/Placeholder`, `SheetContent/Filters`) | 360 wide, top radius 24, drag handle, 48×48 close button, footer = Reset (secondary) + Apply (primary); used for filters, reminders, report, directions chooser |
| `TextField` | State = Default / Focused / Filled / Error / Disabled; text props `Label`, `Text`, `Helper`; booleans `Show label`, `Show helper`, `Show icon`; instance-swap `Icon` | Label always above (no floating labels), min height 48, radius 12; Focused = 2px `border/focus`, Error = 2px `status/error` + message in Helper |
| `EmptyState` | Tone = Peach / Blue / Mint / Coral; text props `Title`, `Body`; instance-swap `Icon`; booleans `Show primary`, `Show secondary` (nested buttons exposed for label edits) | Peach = default, Blue = saved/personal, Mint = success/community, Coral = error/offline. Always offer a next step |
| `ListingCard` | text props `Title`, `Meta`, `Session`; instance-swap `Save icon` (heart / heart-fill); nested Badge instances | Image area uses `bg/brand-peach` as placeholder; save button top-end (top-left in RTL) |

## Icons — Phosphor (MIT)

- **Figma:** frame "Icons (Phosphor)" on the Components page — 40 components named `Icon/<name>`, 24×24, filled with `icon/default` (override with any `icon/*` token).
- **Weight:** *Regular* everywhere; *Fill* only for the active bottom-tab state and the saved heart.
- **Set:** house, map-trifold, check-square, users-three, user-circle (tabs, + fill versions) · heart (+ fill) · navigation-arrow, calendar-plus, bell, share-network, flag, phone, whatsapp-logo, facebook-logo, globe (listing actions) · magnifying-glass, sliders-horizontal, crosshair, map-pin, list, x, plus, trash, check, clock (UI) · caret-left/right, arrow-left (navigation) · baby, tree, music-notes, storefront, stethoscope, sun (categories / outing types).
- **RTL:** directional icons (`caret-*`, `arrow-left`, `navigation-arrow`) must be mirrored in Hebrew; all others stay as-is.
- **Code:** `phosphor-react-native` (same names, `weight="regular" | "fill"`), colored from theme tokens. Need a new icon? Pick it from phosphoricons.com and add it to both Figma and this list.

The core component set from doc 02 §5 is now complete in Figma. Still to design per screen: Skeleton loaders, Toast/Banner, Avatar, SegmentedControl, map pins/clusters.

Example frame **"Example / Empty states"** shows Explore with no results, Saved with nothing saved, and Offline.

Example frame **"Example / Text fields"** shows sign-up email (focused), wrong code (error), area search (icon, no label), a Hebrew field (right-aligned), and a disabled field.

Example frame **"Example / Filters sheet"** shows the sheet over a 40% black scrim. The scrim is a raw color for now — add an `overlay/scrim` token when the token format supports alpha.

> Hebrew tab labels are placeholders for the designer to confirm (e.g. "גילוי" vs "מפה" for Explore). A dark preview needs either the `Color (Dark)` variables swapped in or a Professional plan (modes).

## Screens in Figma

| Page | Frames | Notes |
|---|---|---|
| Screens (formerly Explore) | `Explore / Map` (EX-01), `Explore / List` (EX-02) — 360×800 Android | First layout draft built from library instances. Local components on the page: `MapPin` (Selected, swappable category icon), `MapCluster` (count), `ListingCard/Compact` (selected-pin card), `FloatingPill` (Map ⇄ List toggle), `IconButton` (Outlined = filters, Floating = locate me) |

| Screens | `Listing details / English (LTR)` and `Listing details / Hebrew (RTL)` (EX-06), full-length scroll frames | Photo carousel with floating back/share/save; title block + Verified badge; `FactTile` ×3 (age, price, distance); `AmenityTag`s; Next sessions (`SessionRow` with reminder bell); About; Location mini-map; Contact (`ContactRow`); verification date + Report link; sticky action bar = add-to-calendar + **Directions** (primary). Tab bar hidden on details |

| Screens | `Home / English (LTR)` and `Home / Hebrew (RTL)` (HO-01), full-length frames | Peach greeting header (name + child age + area); **Happening today near you** carousel (ListingCard 264 wide, next card peeks); **Packing for an outing?** = `ResumeCard` (progress bar) + `OutingTile`s; **Your saved classes this week** (`SessionRow` with reminder bell); **Groups for you** (`GroupRow`); TabBar with Home active. New local components: `SectionHeader`, `OutingTile`, `ResumeCard`, `GroupRow` (LTR/RTL); `ListingCard (RTL)` on the Components page |

**Known issue (fix by hand, see below):** on the English Home carousel the save heart is hidden — the card is narrowed to 264 but its save button is positioned from the left. Fix: in the `ListingCard` main component select *Save button* → Constraints: **Right**; in `ListingCard (RTL)` → **Left**. Then nudge each carousel card's width (e.g. 265 → 264) so the instances re-layout.

Decisions applied: list-card photo height 120; filter button shows an active-filter count badge (`IconButton` → `Show count`); Hebrew versions are built for key screens only (Explore, Details, Home).

> Figma Starter plan allows 3 pages: Foundations, Components, Screens. All screens live on the "Screens" page.

### RTL rule: number ranges
In Hebrew, an en dash between numbers is reordered by the bidi algorithm ("0–12" renders as "12–0", "10:00–10:45" as "10:45–10:00"). In Figma, wrap the dash with left-to-right marks (U+200E): `0\u200E–\u200E12`. **In code**, format ranges with a helper that inserts LRM (or wraps numbers in U+2066…U+2069 isolates) and test every range string in Hebrew. Prices in Hebrew read naturally as "מ־60 ₪".
