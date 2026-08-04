# Context — Namou video workflow

## What this is

A repeatable pipeline: **raw numbered clips plus a script → one stitched base video → branded HyperFrames overlays → final vertical MP4.** It wraps the `namou-video-overlays` skill so every reel comes out on-brand and in the right place.

## The two locations (keep them separate)

Both live under a single working root, relative to wherever the agent was started. Never hardcode an absolute path.

- **Raw inputs (read-only source):** `reels/video-projects/<Subject>/<slug>/`
  Numbered clips, the script or doc or report, optional audio, and the source brochure. Produced by `namou-property-reel` or dropped in by the user. **Never modified, never moved.**
- **Edited output:** `reels/edited-videos/<Subject>/`
  Holds the HyperFrames project `<slug>-overlays/` and the finished `<slug>.mp4` beside it.

If the user already has a folder convention, ask once and use theirs.

## Tools

- **`namou-video-overlays` skill** — the brand spec, the 13 overlay patterns, and the overlay build workflow. Its kit ships inside that skill at `assets/` (`design.md`, `components.md`, `kit-readme.md`, `namou-logo.png`, `examples/`). Nothing external to fetch.
- **HyperFrames CLI** — `npx hyperframes` (init, lint, inspect, snapshot, preview, render). Needs **Node 22 or newer**.
- **ffmpeg** — must be on PATH. Used to stitch clips, build the blurred CTA tail, probe durations, and verify rendered frames. Check with `ffmpeg -version`.
- **Higgsfield MCP** — used by `namou-property-reel` for generation. Not needed by this skill.

## Rendering: local vs cloud

- **Local:** `npx hyperframes render --output <slug>-with-overlays.mp4`. Needs ffmpeg on PATH, Chrome, and several GB of free temp space (a 35s 1080p reel stages roughly 1050 PNG frames).
- **Cloud:** `npx hyperframes cloud render` renders on HeyGen's infrastructure with no local Chrome, ffmpeg, or AWS. Use this when the machine is short on disk or the local render keeps failing.
  - **Needs a HeyGen account first.** Run `npx hyperframes auth login`, or set `HEYGEN_API_KEY` in the environment. Check the exact flags with `npx hyperframes cloud --help` — this CLI changes.
  - **Cloud renders consume HeyGen credits.** Local rendering does not. Ask before switching a routine job to cloud.
  - The project uploads to HeyGen, with a 200 MB limit. A long reel with big source video can exceed it.
- **Note:** the HyperFrames **MCP** `render_video` tool is disabled for command-line agents (Claude Code, Codex, Cursor). Render through the CLI, not the MCP.

**ffmpeg is still required either way** — the stitch, the duration probe, and the black-frame verification all run locally and are outside HyperFrames.

## Environment notes

- **TLS-intercepting corporate proxy:** if `npx hyperframes` fails to fetch the GSAP CDN or Google Fonts with a certificate error, set `NODE_OPTIONS=--use-system-ca` for every hyperframes command. On a normal network this is unnecessary.
- **ffmpeg on PATH:** set it in the same shell as the render. PATH updates made in another shell do not propagate.
- Render works fine with CDN GSAP. A **local** `gsap.min.js` can 404 during render (the asset pipeline serves media and fonts, not loose `.js`) — use the CDN `<script src>`.

## Brand defaults (full spec in `namou-video-overlays/assets/design.md`)

- Colors: forest `#003D2E`, accent `#00B073`, mint `#3FE6A8`, ink `#121212`, cream `#FDFDFD`.
- Latin fonts: DM Sans plus Instrument Serif.
- **Arabic font: Tajawal.** DM Sans and Instrument Serif have no Arabic glyphs. Do not substitute Segoe UI or Tahoma.
- Aspect: 1080×1920 (9:16). Motion: vertical IN, horizontal OUT.

## Reference build

`landowner-reel-1` — 5 scenes, Arabic Tajawal captions, logo intro, blurry forest CTA closer, no voiceover. The first build that produced this workflow, and the one that surfaced the `dir="rtl"` black-render bug in [errors.md](./errors.md).
