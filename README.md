# Namou Reel Toolkit

Three agent skills that turn a Namou land brochure into a finished, branded vertical reel.

You give the agent a brochure link. It reads the land facts, downloads the photos, pitches reel angles, generates the scenes in Higgsfield, then stitches and brands them into a 1080×1920 MP4 with Namou overlays.

## The three skills

| Skill | What it does |
|---|---|
| **`namou-property-reel`** | Reads a brochure (link, PDF, or doc + photos), pitches 3–4 reel angles, generates scene keyframes and motion clips in Higgsfield, and writes a project folder of numbered clips plus a script. Stops there. |
| **`namou-video-build`** | Takes that folder, stitches the clips into one base video, builds the branded overlay composition, renders the final MP4, and verifies it. |
| **`namou-video-overlays`** | The brand kit — palette, fonts, 13 overlay patterns, logo, and a worked example composition. `namou-video-build` reads it. You rarely invoke it directly. |

Everything they need ships inside the skill folders. There is nothing else to download.

## Install

Clone and copy the three skill folders into your agent's skills directory.

```bash
git clone https://github.com/NamouProperties/namou-reel-toolkit.git
cp -r namou-reel-toolkit/skills/* ~/.agents/skills/
```

Skills directory by agent:

| Agent | Path |
|---|---|
| Codex / OpenAI agents | `~/.agents/skills/` |
| Claude Code | `~/.claude/skills/` |
| Cursor | `~/.cursor/skills/` |

On Windows PowerShell:

```powershell
git clone https://github.com/NamouProperties/namou-reel-toolkit.git
Copy-Item -Recurse .\namou-reel-toolkit\skills\* "$env:USERPROFILE\.agents\skills\"
```

Restart the agent session so it picks the skills up.

## Prerequisites

| Requirement | Why | Check |
|---|---|---|
| **Node 22 or newer** | Runs the HyperFrames CLI | `node -v` |
| **ffmpeg** on PATH | Stitches clips, probes durations, verifies frames | `ffmpeg -version` |
| **Higgsfield MCP** connected | Generates the scene images and clips | ask the agent to list its tools |
| **HyperFrames** | Builds and renders the overlay composition — installed on demand via `npx hyperframes` | — |

Roughly 400 MB of installs (Node about 100 MB, ffmpeg about 300 MB). If either is missing, ask the agent to install it.

**ffmpeg is required even if you render in the cloud.** HyperFrames can render on HeyGen's infrastructure with `npx hyperframes cloud render`, but the stitching, duration probe, and black-frame check all run locally through ffmpeg.

Cloud rendering needs a HeyGen login (`npx hyperframes auth login`, or a `HEYGEN_API_KEY` in the environment) and **consumes HeyGen credits**. Local rendering does not. Treat cloud as the fallback, not the default.

Note: the HyperFrames **MCP** `render_video` tool is disabled for command-line agents like Codex and Claude Code. Rendering goes through the CLI.

## How to use it

Start the agent in whatever folder you want the work to live in, then:

**1. Make the scenes**

> Use `namou-property-reel` on https://namou-brochures.vercel.app/marjan-residential-129287-sqft-html/

The agent reads the page, downloads the drone photos, and pitches 3–4 reel angles. **You pick one.** It plans the scenes, generates the keyframes, and shows them to you. **It then stops and asks before spending video credits** — video generation bills to William's Higgsfield account, so confirm with him before approving.

Output lands in `reels/video-projects/<Subject>/<slug>/`.

**2. Build the reel**

> Use `namou-video-build` on `reels/video-projects/AlMarjan/marjan-beachfront-129k`

It stitches the clips, drafts the on-screen captions from the script, **confirms the wording with you**, renders, and verifies the output is not black.

Final reel lands at `reels/edited-videos/<Subject>/<slug>.mp4`.

## Where files go

All paths are relative to wherever you started the agent. Nothing is hardcoded.

```
<your working folder>/
└── reels/
    ├── video-projects/<Subject>/<slug>/    ← raw scenes, script, source images
    └── edited-videos/<Subject>/
        ├── <slug>-overlays/                ← the editable HyperFrames project
        └── <slug>.mp4                      ← the finished reel
```

`<Subject>` groups by land family (`AlMarjan`, `Maireed`, `Fujairah`). `<slug>` is the specific plot.

If you already have a folder convention, tell the agent once and it will use yours.

## The two approval stops

The pipeline deliberately pauses twice:

1. **After the angles are pitched** — you choose the direction.
2. **Before video generation** — this spends Higgsfield credits on William's account. Confirm with him.

Both are intentional. Do not remove them.

## Notes

- Brochure pages give you the facts every time and real drone photos most of the time. If a brochure only has site plans and location maps, the reel gets built from generated scenes instead.
- Satellite map tiles and the 360° tour on a brochure page come from outside services. The skill skips them.
- Arabic captions render in Tajawal. Never put `dir="rtl"` on `<html>` — it makes the render come out all black. The skills already handle this; it is documented in `namou-video-build/reference/errors.md`.
- HeyGen is not used in this pipeline. That is only for talking-head videos.

## Repo layout

```
skills/
├── namou-property-reel/
│   ├── SKILL.md
│   └── reference/script-template.md
├── namou-video-build/
│   ├── SKILL.md
│   └── reference/{build-process,errors,context}.md
└── namou-video-overlays/
    ├── SKILL.md
    └── assets/{design,components,kit-readme}.md, namou-logo.png, examples/
```
