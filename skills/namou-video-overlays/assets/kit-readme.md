# Namou video overlay kit

Reusable HyperFrames-based overlay system for Namou Properties ad videos. This kit is the source of truth for fonts, colors, motion, and the overlay patterns used to take a raw speaker video (HeyGen avatar, or a stitched land reel) and turn it into a branded ad with stats, image insets, and CTAs.

## Files in this folder

| File | Purpose |
|---|---|
| `design.md` | Canonical brand spec — palette, fonts, type scale, motion principles, voice and tone. Read this first. |
| `components.md` | The 13 reusable overlay patterns with HTML / CSS / GSAP snippets. |
| `namou-logo.png` | The logo. Copy into each project. |
| `examples/nadim-ad-1/index.html` | Code reference — the full Nadim ad composition. Media files not included, so it does not run as-is. |

## Folder conventions

Relative to the working root. Never hardcode an absolute path.

```
<workspace>/
└── reels/
    └── edited-videos/
        └── <Subject>/                      ← grouped by topic: Nadim, Fujairah, AlMarjan …
            ├── {slug}-overlays/            ← HyperFrames project dir per video
            │   ├── base.mp4 (or speaker.mp4)  ← copy of source video
            │   ├── {asset}.png/jpg            ← images for image-inset patterns
            │   ├── namou-logo.png             ← copied from this kit
            │   ├── design.md                  ← copied from this kit
            │   ├── index.html                 ← the composition
            │   └── {slug}-with-overlays.mp4   ← rendered output (heavy; stays inside)
            └── {slug}.mp4                  ← clean finished video, BESIDE the project
```

## Shell note

Commands below are written for a POSIX shell (macOS, Linux, Git Bash). On PowerShell, substitute the obvious equivalents: `cp` → `Copy-Item`, `mkdir -p` → `New-Item -ItemType Directory -Force`, `cd` works as-is. Every `npx hyperframes` and `ffmpeg`/`ffprobe` command is identical on both.

## Workflow — build a new branded ad

1. **Gather inputs.** A speaker or base MP4, the script text, any reference images (project renders, partner logos, location photos), and the key stats to surface.

2. **Scaffold a HyperFrames project.** From the Subject folder, let `init` create the project directory:
   ```bash
   cd reels/edited-videos/<Subject>
   npx hyperframes init {slug}-overlays --example blank --resolution portrait --non-interactive
   cd {slug}-overlays
   ```
   `--resolution portrait` gives the 1080×1920 canvas. Run `npx hyperframes init --help` if the flags have moved on — this CLI changes.

3. **Copy assets in.**
   - The source video as `speaker.mp4` (or `base.mp4`) — short name, no spaces
   - Any image assets with sanitized filenames
   - `design.md` and `namou-logo.png` from this kit

4. **Get the video's actual duration.**
   ```bash
   ffprobe -v error -show_entries format=duration speaker.mp4
   ```

5. **Estimate phrase timecodes.** If `whisper-cpp` is installed, run `npx hyperframes transcribe speaker.mp4` for word-level timestamps. Otherwise estimate phrase boundaries from the script and duration at roughly 150 wpm, then fine-tune in the preview server.

6. **Write `index.html`.** Use the skeleton below. Drop in overlay patterns from `components.md`, anchoring each `data-start` to the phrase it accompanies. Do not crowd — 3 overlay moments per 30s is the sweet spot.

7. **Quality checks.**
   ```bash
   npx hyperframes lint
   npx hyperframes validate
   npx hyperframes inspect
   ```
   Layout issues must be zero. Contrast warnings on out-of-window elements are known false positives — only worry about ones flagged at timestamps when the element is supposed to be visible.

8. **Visual preview.** Start the preview server in the background and screenshot at each overlay's hero frame.
   ```bash
   npx hyperframes preview --port 3003
   ```
   Then navigate to `http://127.0.0.1:3003/api/projects/{project-dir}/preview/comp/index.html`, call `window.__player.enableRenderMode()`, and use `window.__player.renderSeek(t)` to drive the timeline. Plain `seek(t)` shows stale overlays.

9. **Render.**
   ```bash
   npx hyperframes render --output {slug}-with-overlays.mp4
   ```
   ffmpeg must be on PATH in the same shell. First render takes roughly 50s for a 30s composition. If the machine cannot render locally (low disk, no Chrome), use `npx hyperframes cloud render` instead.

10. **Centralize the output.**
    ```bash
    cp ./{slug}-with-overlays.mp4 ../{slug}.mp4   # beside the project, in the Subject folder
    ```

## Skeleton `index.html`

```html
<!doctype html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=1080, height=1920" />
    <link
      href="https://fonts.googleapis.com/css2?family=DM+Sans:wght@400;500;600;700&family=Instrument+Serif:ital@0;1&display=swap"
      rel="stylesheet"
    />
    <script src="https://cdn.jsdelivr.net/npm/gsap@3.14.2/dist/gsap.min.js"></script>
    <style>
      * { margin: 0; padding: 0; box-sizing: border-box; }
      html, body {
        width: 1080px; height: 1920px;
        overflow: hidden; background: #000;
        font-family: "DM Sans", sans-serif;
        color: #FDFDFD;
      }
      #speaker-video {
        position: absolute; inset: 0;
        width: 1080px; height: 1920px;
        object-fit: cover; z-index: 0;
      }
      /* Pattern CSS goes here — copy from components.md */
    </style>
  </head>
  <body>
    <div
      id="root"
      data-composition-id="main"
      data-start="0"
      data-duration="REAL_DURATION_FROM_FFPROBE"
      data-width="1080"
      data-height="1920"
    >
      <video
        id="speaker-video"
        data-start="0"
        data-duration="REAL_DURATION"
        data-track-index="0"
        src="speaker.mp4"
        muted
        playsinline
      ></video>
      <audio
        id="speaker-audio"
        data-start="0"
        data-duration="REAL_DURATION"
        data-track-index="1"
        src="speaker.mp4"
        data-volume="1"
      ></audio>

      <!-- Overlays from components.md go here -->
    </div>

    <script>
      window.__timelines = window.__timelines || {};
      const tl = gsap.timeline({ paused: true });
      // GSAP tweens from each pattern's snippet go here
      window.__timelines["main"] = tl;
    </script>
  </body>
</html>
```

**Arabic reels:** use `<html lang="ar">` with **no `dir` attribute**, load Tajawal, and set `direction: rtl; text-align: right;` on each Arabic text element only. Putting `dir="rtl"` on `<html>` or `<body>` makes the render come out all black.

## Adding new components

When a new pattern emerges (before/after sliders, plot map insets, etc.), document it in `components.md` following the same structure: use case, HTML, CSS, GSAP entry tweens, notes. Remove the entry from the "Future components" list at the bottom as it gets built.

## Known gotchas

- **Source video keyframes.** HeyGen outputs use sparse keyframes (10s interval) which causes brief frame stutter during seeks. If the render shows visual hiccups, re-encode the source first:
  ```bash
  ffmpeg -i source.mp4 -c:v libx264 -r 30 -g 30 -keyint_min 30 -movflags +faststart -c:a copy source-rekeyed.mp4
  ```
- **Filenames with spaces** break HyperFrames asset resolution. Rename before copying into the project.
- **ffmpeg PATH.** The render step needs ffmpeg on PATH in the same shell. PATH updates made elsewhere do not always propagate.
- **`whisper-cpp` not installed** means transcription falls back to manual phrase estimation. That is accurate to about ±0.3s, usually fine. Word-perfect caption sync needs whisper-cpp installed separately.
- **TLS-intercepting corporate proxy.** If hyperframes cannot fetch the GSAP CDN or Google Fonts with a certificate error, set `NODE_OPTIONS=--use-system-ca`. Unnecessary on a normal network.
