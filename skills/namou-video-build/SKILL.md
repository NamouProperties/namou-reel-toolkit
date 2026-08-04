---
name: namou-video-build
description: Use when someone points at a Namou video project folder (numbered clips plus a script, sometimes audio) and wants it turned into a finished branded vertical reel. Triggers — "build the reel from this folder", "stitch this project into a video", "make the reel for this land", "turn this folder into a reel", "run the video workflow on this folder". NOT for small tweaks to an existing reel (edit it directly) or one-off overlay jobs (use namou-video-overlays).
---

# Namou Video Build

Turn a raw project folder — numbered clips plus a script, optional audio — into a finished branded reel: stitch, then branded HyperFrames overlays, then a final vertical MP4.

## Read these first, in this skill's own folder

1. `reference/build-process.md` — the exact step-by-step build
2. `reference/errors.md` — hard-won pitfalls. The `dir="rtl"` black-render trap is #1
3. `reference/context.md` — tools, environment, brand defaults

This skill is the on-ramp. **`namou-video-overlays`** holds the brand spec and overlay patterns and does the overlay build itself.

## Folders

Everything is relative to the working root. Never hardcode an absolute path.

```
<workspace>/
└── reels/
    ├── video-projects/<Subject>/<slug>/   ← INPUT, read-only
    └── edited-videos/<Subject>/
        ├── <slug>-overlays/              ← the HyperFrames project
        └── <slug>.mp4                    ← the finished reel, BESIDE the project
```

`<workspace>` is the current working directory. If the user has their own folder layout, ask once and use theirs.

## Guardrails (do not violate)

- Stitch clips in **numeric filename order**. Overlay text comes from the script or doc. Use **audio only if the user asks**.
- **Never move or edit** the raw inputs. Read them, write everything new under `edited-videos/`.
- Arabic is **Tajawal**. **NEVER** put `dir="rtl"` on `<html>` or `<body>` — it renders BLACK. Set RTL on each Arabic element only.
- Stitch to **one** `base.mp4` (libx264, yuv420p). The composition `data-duration` must equal the base video duration.
- **Confirm caption wording with the user before the final render.**
- **Verify the rendered frame is not black** with ffmpeg before claiming done.

## Done means

The final `<slug>.mp4` sits beside the project in `edited-videos/<Subject>/`, verified non-black, and you have reported the path.
