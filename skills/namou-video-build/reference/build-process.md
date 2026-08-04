# Build process — Namou video workflow

You are turning a raw project folder into a finished branded reel. Follow this exactly. Read [errors.md](./errors.md) FIRST — it prevents the black-render trap.

## Trigger

The user points you at a project folder under `reels/video-projects/<Subject>/<slug>/` and asks to stitch and overlay it. **Use the `namou-video-overlays` skill** for the overlay layer — this process is its on-ramp.

## Hard constraints

- **Never move or modify the raw inputs.** Read them. Write everything new under `edited-videos/`.
- **Clips are stitched in numeric filename order** (`Scene 1`, `Scene 2`, … or `1`, `2`, …). Confirm the order if filenames are ambiguous.
- **Overlay text comes from the script, docx, or report** in the project folder. Extract it, propose concise on-screen captions plus a CTA, and confirm the wording with the user before rendering.
- **Audio:** a file may be present, but do NOT include it unless the user explicitly asks. Default render is silent.
- **Arabic:** font is **Tajawal**. **Never** put `dir="rtl"` on `<html>` or `<body>` — RTL goes on each Arabic element only. See errors.md #1.

## Steps

1. **Read inputs.** List the project folder. Identify the clips (number order), the script or doc or report, and any audio.

   A `.docx` is a zip — extract the text however your platform allows. Any of these work:
   - macOS / Linux / Git Bash: `unzip -p file.docx word/document.xml | sed 's/<[^>]*>//g'`
   - PowerShell: `Expand-Archive` rejects a `.docx` extension — copy it to `.zip` first:
     ```powershell
     Copy-Item file.docx tmpdocx.zip
     Expand-Archive tmpdocx.zip -DestinationPath tmpdocx
     ```
     then read `tmpdocx/word/document.xml` and strip the tags
   - Python (anywhere): `python -c "import zipfile,re;print(re.sub(r'<[^>]+>',' ',zipfile.ZipFile('file.docx').read('word/document.xml').decode()))"`

   Use whichever fits the machine you are on. Do not assume a shell.

2. **Check the footage before writing captions.** Grab a mid-frame of each clip and look at it:
   ```bash
   ffmpeg -ss 4 -i "Scene 1.mp4" -frames:v 1 s1.jpg
   ```
   Some source clips already carry burned-in text or numbers. Do not caption over a scene that already says the thing. See errors.md #2.

3. **Propose overlay text.** From the script, draft one concise caption per scene plus the CTA. Get the user's OK. Ask which Arabic font only if they want something other than Tajawal.

4. **Scaffold the overlay project.** From `reels/edited-videos/<Subject>/`, run:
   ```bash
   npx hyperframes init <slug>-overlays --example blank --resolution portrait --non-interactive
   ```
   `init` creates the `<slug>-overlays/` folder for you — do not create it first. `--resolution portrait` sets the 1080×1920 canvas. Verify the flags against `npx hyperframes init --help` if the CLI has moved on.

   Then copy `design.md` and `namou-logo.png` from the `namou-video-overlays` skill's `assets/` folder into the project.

5. **Probe and stitch clips into one `base.mp4`.** A single video on track 0 is far more reliable than multiple clips.
   ```bash
   ffmpeg -i s1 -i s2 ... -filter_complex \
     "[0:v]scale=1080:1920:force_original_aspect_ratio=increase,crop=1080:1920,fps=30,setsar=1[v0]; ... ;[v0][v1]...concat=n=N:v=1:a=0[outv]" \
     -map "[outv]" -an -c:v libx264 -crf 18 -pix_fmt yuv420p -g 30 base.mp4
   ```
   For a **blurry CTA closer**, append a gaussian-blurred frozen-last-frame tail so `base.mp4` length equals the composition length.

6. **Build `index.html`.** Start from the skeleton in the overlay kit (`namou-video-overlays/assets/kit-readme.md`).
   - `<html lang="ar">` — **NO `dir` attribute**.
   - GSAP from the **CDN**: `<script src="https://cdn.jsdelivr.net/npm/gsap@3.14.2/dist/gsap.min.js">`.
   - One `<video id="base-video" class="scene">`. Its `data-duration` must equal the composition `data-duration` and the real `base.mp4` length.
   - Caption banners and the CTA as `class="clip"` overlays. `direction: rtl` on each Arabic element. Tajawal for Arabic. Brand colors only.
   - Logo: copy `namou-logo.png` from the overlay skill's assets into the project. White on dark via `filter: brightness(0) invert(1)`.

7. **QA loop:** `npx hyperframes lint` (0 errors), then `inspect` (0 layout issues), then `snapshot --at <key times>` and eyeball each beat. Also snapshot a few **off-target** timestamps to catch overlays bleeding into scenes they do not belong to (errors.md #5).

8. **Render** in a clean shell with nothing else using Chrome:
   ```bash
   npx hyperframes render --output <slug>-with-overlays.mp4
   ```
   ffmpeg must be on PATH in the same shell. If the machine cannot render locally, `npx hyperframes cloud render` renders on HeyGen's infrastructure instead — see context.md.

9. **VERIFY the render is not black** before claiming done:
   ```bash
   ffmpeg -ss <t> -i <slug>-with-overlays.mp4 -frames:v 1 -vf "signalstats,metadata=print" -f null -
   ```
   YAVG around 16 across all frames means black — go to errors.md. Expect tens of MB for a 40s reel.

10. **Centralize:** copy the output to `../<slug>.mp4`, beside the project in the Subject folder. Report the path.

## Keep these docs current

If a new pitfall, decision, or convention emerges, update [errors.md](./errors.md) or [context.md](./context.md) in the same turn, and the overlay skill's `assets/design.md` if it is a brand or render rule.
