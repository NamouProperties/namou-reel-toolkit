# Errors & Gotchas — Namou Video Workflow

Hard-won lessons. Read before building. Add to this file whenever a new pitfall costs time.

---

## 🔴 #1 — `dir="rtl"` on `<html>` makes HyperFrames render ALL-BLACK

**Symptom:** preview / `snapshot` look perfect, but `npx hyperframes render` outputs a tiny all-black MP4 (every frame pure black, video-range luma ≈ 16).

**Cause:** `dir="rtl"` (or `direction: rtl`) on the `<html>` (or `<body>`) tag. The render compositor chokes on it; preview/snapshot tolerate it, so it fools you.

**Fix:**
- Keep `<html>` **neutral / LTR** — `<html lang="ar">` with **no `dir`** attribute.
- Apply RTL **only on the individual Arabic text elements** — `direction: rtl;` (and `text-align`) on each caption / CTA block that holds Arabic.
- Never set RTL on `<html>` or `<body>`.

This cost a very long debug on `landowner-reel-1`. It is now also documented in the overlay kit's `design.md`.

---

## How to debug a black / wrong render (cheap → expensive)

1. **Verify a real frame, never trust the MP4 blind:**
   ```bash
   ffmpeg -ss <t> -i out.mp4 -frames:v 1 -vf "signalstats,metadata=print" -f null -   # YAVG≈16 = black
   ffmpeg -ss <t> -i out.mp4 -frames:v 1 frame.png                                     # then look at it
   ```
   A 40s 1080p reel should be tens of MB. A few hundred KB = black.
2. **Render a video-only test** (base.mp4, no overlays/GSAP/fonts). If it works → the bug is in the overlay layer, not the video.
3. **Re-render a known-good project** (e.g. Fujairah `labor-camp-fujairah-overlays`) to rule out a HyperFrames version regression.
4. **Bisect** the composition — add pieces back to the working video-only test until it breaks.

---

## 🟡 #2 — Footage may ALREADY have burned-in graphics/text — check frames first

**Symptom:** the script's "Text on Screen" gets added as a banner, but the clip already shows that exact text/number (duplicated, cluttered).

**Cause:** some source reels are partly-finished motion graphics, not clean b-roll. (Nakheel `nakheel-20m-reel-1` Scene 3 already had `FAR 18`, `G+M+28`, a height ruler, and a NAMOU sign burned in.)

**Fix:** before drafting captions, grab a mid-frame of each clip (`ffmpeg -ss 4 -i "Scene N.mp4" -frames:v 1 s.jpg`) and look. Only caption the CLEAN scenes; skip any scene that already carries its message. Numbers/FAR live in native graphics — don't repeat them in a banner.

---

## 🟡 #3 — Localize fonts to woff2 (CDN Google Fonts link now lint-errors)

**Symptom:** `npx hyperframes lint` throws 2 errors — `google_fonts_import` + `font_family_without_font_face` — and warns text will fall back to a generic font in render.

**Fix:** download the **latin** woff2 subsets, drop them in `fonts/`, and inline `@font-face` (no `<link>`). The compiler then embeds each as a data URI (`[Compiler] Embedded local font file: ...`) — fully offline, correct typography, 0 lint errors. Pull the css2 URL with a Chrome UA, keep only the `/* latin */` blocks. (DM Sans 400/500/700 + Instrument Serif italic for a Latin/English reel.) The old Tajawal CDN `<link>` still renders with `--use-system-ca`, but localizing removes the risk + the lint errors.

**English/LTR reels:** font mix is **DM Sans** (700 caps headline) + **Instrument Serif** (italic accent line). No `dir`/RTL concerns — that's the Arabic path only.

---

## 🟡 #4 — `hyperframes render` fails with "Low disk space" (needs several GB temp)

**Symptom:** render aborts early with `✗ Low disk space / 0.9 GB free`; no output MP4. Not a composition bug — lint/validate/inspect all pass.

**Cause:** render dumps the full PNG frame sequence to temp before encoding (a 35s @ 30fps 1080p reel ≈ 1050 frames, ~2 GB temp). If the drive is near-full it can't stage them.

**Fix (safe, regenerable first):** `npm cache clean --force` — the npm cache commonly runs 2–3 GB and is fully regenerable. Then re-render. Do NOT delete user project files to make room; if npm cache + temp aren't enough, stop and ask. Check free space with `df -h .` (macOS/Linux) or `Get-PSDrive C` (Windows). Alternatively skip local rendering entirely with `npx hyperframes cloud render`.

## 🟡 #5 — `class="clip"` + `data-start`/`data-duration` alone does NOT hide an element outside its window

**Symptom:** an SVG/overlay meant only for one scene (e.g. a plot-outline reveal) shows up bleeding into an earlier or later scene's snapshot/render frame, even though it has `data-start`/`data-duration` set to the later window.

**Cause:** `data-start`/`data-duration` on a `.clip` element schedule when the *GSAP timeline* is allowed to touch it, but they do not implicitly set the element's default visual state. If the GSAP timeline only ever animates the element **to** visible (e.g. `tl.set(el, {opacity:1}, laterTime)`) and never explicitly sets it hidden at `t=0`, the element renders at its CSS default (visible) for the entire composition up until the "show" tween fires.

**Fix:** always add an explicit `tl.set(el, {opacity:0}, 0)` (or equivalent hidden state) at the start of the timeline for any element that shouldn't be visible for the whole composition, then tween it to visible at its real start time. Reel #1's plot-outline used `stroke-dasharray`/`stroke-dashoffset` for a related reason (zero-length stroke by default) plus an explicit `tl.set(svg, {opacity:0}, 0)` — copy both patterns, don't drop the initial hide. Caught on `almarjan-rewind` 2026-07-01 via a snapshot at an off-scene timestamp before render; always snapshot a few off-target timestamps (not just each scene's own window) to catch bleed-through like this.

## 🟡 #6 — `higgsfield` CLI auth is a dead end — use the Higgsfield MCP instead

**Short version:** do not try to generate through the local `higgsfield` CLI. Its login needs a browser device-approval flow an agent cannot complete, and the npm shim is often missing from PATH. Use the Higgsfield **MCP tools** — they work without any of this. The full history is kept below only so nobody re-derives it.

<details>
<summary>Original CLI investigation (historical)</summary>

### `higgsfield` CLI: no shim on PATH, and device-login auth can't be completed in a non-interactive session

**Symptom:** `higgsfield` is not found on PATH in Git Bash or PowerShell even though `@higgsfield/cli` is installed globally (`npm ls -g` shows it). Running the package's `bin/higgsfield.js` (or `bin/run.js`) directly produces no output and no error.

**Cause:** the npm-installed shim (`higgsfield`/`higgs` commands) wasn't linked into `AppData\Roaming\npm` on this machine, and `bin/run.js` only exports a function (`module.exports = function run() {...}`) — running it directly with `node run.js` does nothing because nothing calls the export. The actual binary is vendored at `AppData\Roaming\npm\node_modules\@higgsfield\cli\vendor\hf.exe`; call that directly. Separately, this account had no saved credentials (`~/.config/higgsfield/credentials.json.lock` was an empty lockfile, not real creds) — `hf auth login` requires a browser-based device-approval flow (prints a `https://higgsfield.ai/device?code=...` URL) that cannot be completed by an agent in a non-interactive session, and there is no `HIGGSFIELD_*` API-key env var fallback.

**Fix:** if `higgsfield` isn't found, try invoking the vendored binary directly: `& "$env:APPDATA\npm\node_modules\@higgsfield\cli\vendor\hf.exe" <args>`. If `hf account status` / `hf auth status` shows not authenticated, this is a hard stop for any Kling/Higgsfield generation step — surface it to the user immediately (they need to run `hf auth login` and approve in a browser themselves) rather than attempting workarounds. Everything else in the pipeline (ffmpeg stitching, overlay build, render) can proceed independently and be validated with placeholder clips (e.g. ffmpeg `zoompan` Ken Burns from the same ref stills) so only the blocked generation step remains once the user unblocks auth.

**Addendum (2026-07-01):** the CLI auth blocker didn't need to be resolved at all — the `almarjan-rewind` S1/S2/S6 Kling clips generated successfully via the Higgsfield **MCP session connection** instead of `hf.exe`, so a stuck local CLI login doesn't have to block generation if the MCP tools are reachable.

</details>

## Other known pitfalls

- **Multiple video clips on one track** render slowly (fell to 1 worker, ~12 min) and are fragile. **Stitch the scenes into ONE `base.mp4`** with ffmpeg (`libx264`, `-pix_fmt yuv420p`) and use a single `<video>` element.
- **Duration mismatch blacks the render.** Composition `data-duration` must equal the base video `data-duration`. A gap on track 0 (video shorter than the composition) → black. For a CTA "blurry overlay" closer, append a gaussian-blurred frozen-last-frame tail to `base.mp4` so the durations still match.
- **Local `gsap.min.js` can 404 in render.** Use the CDN `<script src="https://cdn.jsdelivr.net/npm/gsap@3.14.2/dist/gsap.min.js">` — it loads fine in render when `NODE_OPTIONS=--use-system-ca` is set.
- **Not a GPU issue.** `--no-browser-gpu` did NOT fix the black render — don't chase it.
- **Not a codec issue** if base is already `h264 / yuv420p` — don't chase pixel format if ffprobe confirms it.
- **Fonts:** DM Sans / Instrument Serif have **no Arabic glyphs** — Arabic won't render in them at all. Use **Tajawal** for Arabic.
- **Always run the QA loop:** `npx hyperframes lint` (0 errors) → `inspect` (0 layout issues) → `snapshot --at <times>` (eyeball) → `render` → verify frames with ffmpeg.
- **Don't leave the preview server or extra `snapshot` jobs running during a render** — they starve the render of Chrome workers.
