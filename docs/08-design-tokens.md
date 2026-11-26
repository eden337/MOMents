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
