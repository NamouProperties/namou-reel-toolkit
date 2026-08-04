---
name: namou-video-overlays
description: Build branded video overlays for Namou Properties ads using HyperFrames and the Namou overlay kit (forest green / mint / DM Sans / Instrument Serif). Use when the user asks to "add overlays to this video", "edit this video", "brand this video", "add stats/CTAs to this video", "make this HeyGen video on-brand", "render this with Namou overlays", or any request involving editing or branding a speaker or avatar video for Namou Properties. The kit ships inside this skill at assets/.
---

# Namou video overlay builds

You are building a branded video ad for Namou Properties. This skill carries its own kit: a brand spec, 13 reusable overlay patterns, the logo, and a worked example composition to read. Use them — do not reinvent.

## Read these first, in order, from this skill's own folder

1. **`assets/design.md`** — the Namou brand spec. Source of truth for colors (forest `#003D2E`, accent `#00B073`, mint `#3FE6A8`, ink `#121212`, cream `#FDFDFD`), fonts (DM Sans plus Instrument Serif; Arabic uses **Tajawal** — `"Tajawal", "Segoe UI", "Tahoma", sans-serif` — with `direction: rtl; text-align: right;` on the element, never `dir="rtl"` on `<html>`), motion principles, type scale, voice and tone. **Never invent hex codes or substitute fonts.**

2. **`assets/components.md`** — the 13 reusable overlay patterns with full HTML, CSS, and GSAP code. The core four:
   - **Pattern 1:** Image inset card (top-right corner, 320×426, forest gradient plus Instrument Serif italic label in mint) — for project renders, partner logos, location photos
   - **Pattern 2:** Big-stat lower-third on dark forest — one big number under the speaker
   - **Pattern 3:** Big-stat card on white (editorial) — headline stats with a sign like "+30%"
   - **Pattern 4:** Forest-gradient CTA closer — italic Instrument Serif pull-quote plus an accent-green pill, runs the last 4–5s

   Patterns 5–13 are in the same file: arrow callout, live-stat hero, JV chips, audience badge, feature cascade, outcome ticker, rank badge, ranked area card, and **Pattern 13 — green pin drop plus plot polygon** (the map call-out for showing where a plot sits). Pattern 13 is the one to reach for on land reels.

3. **`assets/kit-readme.md`** — the full step-by-step workflow (scaffold, assets, timing, write composition, lint/validate/inspect, preview verify, render, centralize output) plus the skeleton `index.html`.

4. **`assets/examples/nadim-ad-1/index.html`** — a full worked composition to read as a **code reference**. Its media files (`nadim.mp4`, `wynn-casino.png`) are NOT included, so it will not run as-is. Read it to see how patterns are wired together and timed.

5. **`assets/namou-logo.png`** — the logo. Copy it into each project. White on dark via `filter: brightness(0) invert(1)`.

## Where things go

Location-agnostic. Everything sits under the working root, relative to where the agent was started. Never hardcode an absolute path.

```
<workspace>/
└── reels/
    └── edited-videos/<Subject>/
        ├── <slug>-overlays/       ← the HyperFrames project
        │   ├── speaker.mp4        (or base.mp4) — copy of the source video
        │   ├── namou-logo.png     — copied from this skill's assets/
        │   ├── design.md          — copied from this skill's assets/
        │   ├── index.html         — the composition
        │   └── <slug>-with-overlays.mp4  — rendered output, stays inside
        └── <slug>.mp4             ← clean finished video, BESIDE the project
```

`<Subject>` groups by topic (`Nadim`, `Fujairah`, `AlMarjan`). Ask or infer which, and create it if missing.

## Workflow (concise — see `assets/kit-readme.md` for full detail)

1. **Scaffold:** from `reels/edited-videos/<Subject>/`, run `npx hyperframes init <slug>-overlays --example blank --resolution portrait --non-interactive`. It creates the folder — do not create it first. Check `npx hyperframes init --help` if the flags have changed.
2. **Assets:** copy the source video in as `speaker.mp4` (short name, no spaces). Copy any images with sanitized filenames. Copy `design.md` and `namou-logo.png` from this skill's `assets/`.
3. **Duration:** `ffprobe -v error -show_entries format=duration speaker.mp4`. Use the real length for `data-duration` on the root, the video, and the audio.
4. **Timing:** transcribe if `whisper-cpp` is available, otherwise estimate phrase boundaries from the script and duration at roughly 150 wpm. Refine in preview.
5. **Build `index.html`:** start from the skeleton in `kit-readme.md`. Drop in patterns from `components.md`, anchored to spoken phrases. Do not crowd — 3 overlay moments per 30s.
6. **Quality checks:** `npx hyperframes lint && npx hyperframes validate && npx hyperframes inspect`. Layout issues must be zero. Contrast warnings on out-of-window elements are known false positives — only fix ones flagged at timestamps where the element is actually meant to be visible.
7. **Visual preview:** `npx hyperframes preview --port 3003`, then drive the timeline and screenshot each hero frame. Confirm overlays do not cover the speaker's face and the brand is consistent.
8. **Render:** ensure ffmpeg is on PATH, then `npx hyperframes render --output <slug>-with-overlays.mp4`. Roughly 50s for a 30s composition. If the machine cannot render locally, use `npx hyperframes cloud render`.
9. **Centralize:** copy the output to `../<slug>.mp4` — the clean copy sits in the Subject folder, outside the project folder.

## Hard rules (HyperFrames plus Namou)

- **Aspect ratio:** 1080×1920 (9:16 vertical). Match the source video — do not crop.
- **Brand:** every color hex in the composition must appear in `assets/design.md`. No invented colors, no font substitutions for Latin text.
- **Arabic text:** DM Sans and Instrument Serif have no Arabic glyphs. Use **Tajawal** — `font-family: "Tajawal", "Segoe UI", "Tahoma", sans-serif`. Apply `direction: rtl; text-align: right;` on each Arabic ELEMENT. Do this automatically without asking. **⚠️ NEVER put `dir="rtl"` or `direction: rtl` on `<html>` or `<body>` — it makes `hyperframes render` output an ALL-BLACK video. Preview and snapshot look fine; the render does not. Keep `<html lang="ar">` with no `dir` and set RTL only on the text elements.**
- **Determinism:** no `Math.random()`, no `Date.now()`. Count-ups use a GSAP `textContent` tween with `snap: { textContent: 1 }`.
- **Visibility control:** every timed overlay needs `class="clip"`, `data-start`, `data-duration`, `data-track-index`. Same-track clips cannot overlap in time. Also add an explicit `tl.set(el, {opacity:0}, 0)` for anything that should not be visible from the start — `data-start` alone does not hide it.
- **Video/audio:** the base video is always `muted playsinline` on `data-track-index="0"`. Audio reads from the SAME source file on `data-track-index="1"` — no extraction needed.
- **Motion rule — vertical IN, horizontal OUT.** Every overlay except the CTA closer MUST have both an entrance and an exit. Entrance slides in **vertically** (`gsap.from({ y: ... })`, `power3.out`, about 0.65s). Exit slides out **horizontally** (`gsap.to({ x: ±1100, opacity: 0 })`, `power2.in`, 0.5s, timed to `data-start + data-duration - 0.6`). The CTA closer has no exit — it holds until the video ends. Letting a clip snap to hidden without an exit tween looks unprofessional and is forbidden.
- **Meta safe zones — placement.** Paid social overlays its own UI on the bottom third (caption, username, CTA button, action rail) and clips the top roughly 220px. NEVER place content boxes in the bottom third. Small badges go in the **top strip** (`top: ~230–250`, above the speaker's head, below Meta's top chrome). Bigger cards go in the **mid-zone** over the speaker's chest, anchored with `top:`, below the chin and above roughly y=1250, never covering the face. The CTA closer is the ONLY overlay allowed in the bottom zone. See the Meta safe zones table in `components.md`. Extract the speaker's chin Y from a frame grab (`ffmpeg -ss N -i speaker.mp4 -frames:v 1 frame.jpg`) before positioning cards.
- **Preview verification uses `renderSeek`, not `seek`.** Plain `window.__player.seek(t)` updates the video clock but does not reliably update overlay clip visibility — you will see stale overlays. Call `enableRenderMode()` first, then use `window.__player.renderSeek(t)` for accurate per-beat screenshots.
- **The HyperFrames "no exit" rule is for multi-scene compositions, not single-scene overlay clips.** These compositions are single-scene with overlay clips on top of a continuous base video. The `class="clip"` mechanism still snaps the element to hidden at the end of its window, but a `gsap.to()` exit tween in the last 0.5s pushes it off-screen first so the snap is invisible.
- **No `repeat: -1`** on any tween — it breaks the capture engine.

## When the user gives you a new request

- **"Make me a video like the Nadim ad but for X"** → scaffold a new project, copy assets, follow the workflow, reuse patterns from `components.md`.
- **"Add overlays to this video"** → ask for the script if not provided, the key stats to surface, and any image assets. Then proceed.
- **"Fix the timing on the [stat/CTA] in this video"** → edit the relevant `data-start` in `index.html` and re-render. Round trip is roughly 2 minutes.
- **"Change the CTA copy"** → edit the pill text in `index.html`. If the new copy is too long for one line at 40px, recommend a split (italic line plus pill) or shrink the pill font.
- **"Add a new pattern"** → build it in the new project's `index.html`, then back-port the working snippet into `assets/components.md` under "Future components → built", documented the same way the existing ones are.

## Naming conventions

- HyperFrames project dir: `reels/edited-videos/<Subject>/<slug>-overlays/`
- Final MP4: `reels/edited-videos/<Subject>/<slug>.mp4` — beside the project folder, not inside it
- Asset files inside a project: lowercase, hyphen-separated, no spaces (`wynn-casino.png`, not `wynn casino.png`)

## Don'ts

- Do not run a preview server in the foreground and block — run it in the background.
- Do not leave a preview server or extra snapshot jobs running during a render. They starve the render of Chrome workers.
- Do not trust contrast warnings without checking whether the element is actually meant to be visible at the flagged timestamp.
- Do not render without ffmpeg on PATH. The npx process will not pick up PATH updates made in another shell.
- Do not crowd a 30s video with more than 3 overlay moments.
- Do not write copy on the pill longer than roughly 24 characters at 40px — it wraps awkwardly. Split into an italic line plus a pill instead.
