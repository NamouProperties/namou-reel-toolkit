---
name: namou-property-reel
description: Use when someone gives a Namou land brochure — a live brochure URL, a brochure or land-evaluation PDF, or a doc plus photos — and wants reel angle ideas or the scenes generated for a Namou property or land video. Triggers, "give me reel angles for this land", "turn this brochure into a reel", "make a reel from this brochure link", "generate the scenes for this land", "make the clips for a Namou land reel". NOT for stitching or branding an existing project folder (use namou-video-build), one-off image or video generation (higgsfield-generate), or overlay-only edits (namou-video-overlays).
---

# Namou Property Reel — scene generation

The **front half** of the Namou reel pipeline. Turn a land brochure into a project folder of numbered scene clips plus a script. You pitch angles, generate scene keyframes and motion clips, and stop at the folder. You do **NOT** stitch, brand, or render — that is `namou-video-build`.

**Generation engine:** every image/video call goes through Higgsfield (the `higgsfield-generate` skill if installed, otherwise the Higgsfield MCP tools directly).
**Downstream:** `namou-video-build` consumes your output folder. Match the contract below exactly.

## Where things go

This skill is **location-agnostic**. All work happens under a single working root:

```
<workspace>/                          ← the folder the agent was started in
└── reels/
    ├── video-projects/<Subject>/<slug>/   ← YOUR output (raw scenes + script + source)
    └── edited-videos/<Subject>/           ← namou-video-build's output. Do not touch.
```

- `<workspace>` is the current working directory. Never hardcode an absolute path.
- Create `reels/video-projects/<Subject>/<slug>/` if it does not exist.
- `<Subject>` groups by topic or land family (`AlMarjan`, `Maireed`, `Fujairah`). `<slug>` is the specific land or ad (`marjan-beachfront-129k`).
- If the user has an existing folder convention, ask once and use theirs instead.

## Step 1 — get the land facts

Three possible inputs. Handle whichever you are given.

### A. A brochure link (preferred)

The user pastes a Namou brochure URL, e.g.
`https://namou-brochures.vercel.app/<plot-slug>/`

Do this:

1. **Fetch the page** and read the text. A Namou brochure carries everything you need: plot area, asking price, district, ownership and height allowance, FAR, max GFA, travel times, investment angles with indicative returns, and the payment plan.
2. **List the images on the page**, then download every one served from the brochure's own domain into the project folder under `source-images/`. Typical names: `hero.jpg`, `land.jpg`, `image-2.jpg` … `image-7.jpg`, `tour-cover.jpg`, `master-plan.jpg`, `site-plan.jpg`, `namou-logo.png`. They are usually large drone photos (5472×3648).
3. **Skip anything served from another domain.** Satellite map tiles and the 360° tour are external services and are useless as reel stills.
4. **Skip the logo** as a scene source — the overlay build adds branding later.

If the page has no ground photos (only site plans and location maps), say so and plan the reel around generated scenes plus the site plan.

A GitHub link to the brochure repo works the same way — read `index.html` for the facts, take the images from the same folder.

### B. A brochure or land-evaluation PDF

Read it, extract the same facts, and pull the embedded images. Copy the PDF into the project folder.

### C. A doc plus loose photos

Read the doc, copy the photos into `source-images/`.

## Step 2 — pitch the angle

Pitch **3–4 reel angles in chat** — hook plus arc, one line each. Wait for a pick. Record the options and the chosen one in the script file.

Do not generate anything before an angle is chosen.

## Step 3 — plan the scenes

Plan 5–7 scenes for the chosen angle. Per scene decide:

- **Source** — a real photo from the brochure, or generated from scratch.
- **Image model** — GPT Image 2 for graphic, text-bearing, or from-scratch shots. Nano Banana 2 (Pro on hard cases) when building FROM a supplied photo or reference.
- **Motion** — what the camera or subject does.

Real drone photos are the strongest material. Use them wherever the scene allows and generate only what the brochure cannot supply.

## Step 4 — generate keyframes

Generate stills via Higgsfield, uploading the brochure photos as references for any scene based on them. Eyeball every still before animating. Regenerate rather than animating a weak keyframe.

## Step 5 — animate

**Stop here and get approval before spending video credits.** Video generation bills to William's Higgsfield account. State the scene count and the estimated cost, and wait.

> If you are not William, confirm with him before this step, not just with whoever is running the session.

Then animate each keyframe to a clip:

- Kling 3.0 `pro`, **sound OFF**, 9:16, roughly 5–8 seconds.
- Use start-frame plus end-frame for transitions and reveals.
- **Verify each clip by extracting a frame** — you cannot watch video. `ffmpeg -ss 2 -i "Scene 1.mp4" -frames:v 1 check.jpg`

## Step 6 — assemble the folder

In `reels/video-projects/<Subject>/<slug>/`:

- **`Scene 1.mp4` … `Scene N.mp4`** — numbered, no gaps. Numeric order **is** the stitch order. 1080×1920.
- **`<slug>-script.md`** — use `reference/script-template.md` in this skill's folder. It records the angle options, the pick, the final script, one block per scene (clip, visual, on-screen text, the image model and prompt and motion used), and the CTA.
- **`source-images/`** — the downloaded brochure photos.
- **The source** — the brochure PDF if you were given one, or the brochure URL written at the top of the script.
- Audio only if the user provides or asks for it.

The land name lives in the folder slug and the script title, never in the clip filenames.

## Step 7 — hand off

Tell the user to run **`namou-video-build`** on the folder. It stitches, brands, and renders the final reel.

## Hard rules

- Kling 3.0 `pro`, sound off, always. **Confirm before any video generation.**
- Clips are `Scene N.mp4`, sequential, no gaps.
- **Stop at the folder.** Do not stitch, overlay, or render.
- Never write into `edited-videos/` — that belongs to `namou-video-build`.
- Arabic captions go into the script in Arabic. Downstream renders them in Tajawal. Do no RTL work here.

## Common mistakes

- Generating video before the angle and keyframes are approved. Wasted credits.
- Downloading map tiles or the 360° tour cover from a brochure page. They are external and useless.
- Clips out of order or with gaps. The build then stitches wrong.
- Drifting into the overlay or stitch build. Hand off instead.
