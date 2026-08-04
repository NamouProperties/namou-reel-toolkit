# Namou Properties — Design Spec

Brand cues pulled from https://namou-main.vercel.app/ (CSS custom properties + linked Google Fonts).

## Palette

**Light surfaces (default for overlays on light video moments):**
- `--bg`: `#FDFDFD` — primary surface
- `--bg-alt`: `#F5F5F5` — secondary surface
- `--ink`: `#121212` — primary text
- `--ink-muted`: `#555555` — secondary text
- `--ink-soft`: `#737373` — tertiary text
- `--rule`: `#E2E2E2` — hairline divider

**Dark surfaces (for stat cards and editorial moments):**
- `--bg-deep`: `#003D2E` — deep forest green (hero dark background)

**Accents:**
- `--accent`: `#00B073` — primary brand green (CTA, callouts, numbers)
- `--accent-deep`: `#003D2E` — same as bg-deep, for solid badges
- `--accent-hover`: `#009963` — accent darker
- `--mint`: `#3FE6A8` — bright mint (highlight, emphasis on dark bg)
- `--sage`: `#6FB89C` — sage accent (secondary highlight)
- `--accent-subtle`: `rgba(0, 176, 115, 0.08)` — accent tint background

## Typography

- **Headings + display numbers**: `DM Sans` weights 600 / 700 — geometric, modern, authoritative
- **Editorial accent / italic**: `Instrument Serif` (italic) — for serif quotes, the word *now*, framing language
- **Body**: `DM Sans` weights 400 / 500

Google Fonts URL (embedded by HyperFrames compiler):
`https://fonts.googleapis.com/css2?family=DM+Sans:wght@400;500;600;700&family=Instrument+Serif:ital@0;1&display=swap`

For rendered video, use `font-variant-numeric: tabular-nums` on all stat numbers.

**Arabic text:** DM Sans and Instrument Serif have NO Arabic glyphs — they will not render Arabic at all. Use **Tajawal** (Google font, geometric, pairs with the kit's DM Sans look) — `font-family: "Tajawal", "Segoe UI", "Tahoma", sans-serif`. (Segoe UI / Tahoma is an acceptable no-webfont fallback — both ship Arabic glyphs on Windows.) Apply `direction: rtl; text-align: right;` on each Arabic ELEMENT.

**⚠️ NEVER put `dir="rtl"` (or `direction: rtl`) on the `<html>` or `<body>` tag.** It makes `hyperframes render` output an ALL-BLACK video (snapshot/preview tolerate it, the render compositor does not — this cost a long debug on the Landowners reel). Keep `<html lang="ar">` with no `dir`, and set `direction: rtl` only on the individual caption / CTA elements that hold Arabic text.

## Type scale (1080×1920 vertical)

- Hero stat number: 220–280px, DM Sans 700, tabular-nums
- Stat label: 36–44px, DM Sans 500, uppercase, letter-spacing 0.04em
- Editorial pull-quote / italic: 64–80px, Instrument Serif italic
- CTA label: 40–48px, DM Sans 600
- Body / caption: 28–32px, DM Sans 400

## Motion

- **Ease**: `cubic-bezier(0.22, 1, 0.36, 1)` (site standard — `--ease`). For GSAP use `power3.out` on entrances, `power2.in` on exits (final scene only — per HyperFrames rule, scenes use entrances only, transitions handle exits).
- Stat number reveal: count-up from 0 to target with slight scale (1.02 → 1.0) over 0.8s, ease `expo.out`.
- Pill/badge entrance: y +30, opacity 0 → 0, opacity 1 over 0.6s, ease `power3.out`.
- Image cutaway entrance: scale 1.08 → 1.0, opacity 0 → 1 over 0.7s with subtle x drift.

## Voice / tone

- **Strategic, data-driven, editorial luxury.** Numbers do the heavy lifting; copy is sparse and confident.
- **Avoid:** exclamation marks, sales clichés, neon/aggressive colors, drop shadows, glassmorphism, photoshop bevels.
- **Lean into:** breathing room, tabular numbers, serif italic for emphasis, single accent color per scene, deep forest green for authority.

## Layout — 9:16 (1080×1920)

- Safe area: 80px from all edges
- Stat cards: full-width, 1080px wide, anchored to bottom-third (y ≈ 1080–1480) so they don't cover Nadim's face
- Top callouts: 80px from top, max 720px wide (so they don't crowd the brand area)
- Logo lockup (if used): bottom-right, 200px wide, opacity 0.85

## Don'ts

- No `Math.random()`, `Date.now()`, or non-deterministic logic
- No full-screen linear gradients on dark backgrounds (use radial or solid + localized glow)
- No invented hex codes — every color in the composition must appear in this file
- No font substitutions for Latin text — DM Sans + Instrument Serif only (Arabic text is the one exception: use **Tajawal**, with `Segoe UI / Tahoma` only as the no-webfont fallback — see the Typography section)
- No exit animations on non-final **scenes** — that is a HyperFrames multi-scene rule, where the transition between scenes handles the exit. **It does NOT apply to timed overlay clips** on top of a continuous base video. Those MUST have a horizontal exit tween. See the motion rule in the `namou-video-overlays` skill.
