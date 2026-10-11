# AI Render-to-Video: Architectural House Tours

**Turn a folder of architectural renders into a premium, social-ready vertical house tour video, directed by an AI agent and rendered locally.**

Drop in still renders and a music track, describe the property, and the agent plans a cinematic walkthrough (camera moves, transitions, room labels, atmosphere, contact card), waits for your approval, then builds and exports a finished 1080×1920 video ready for Reels, TikTok and Shorts.

---

## Demo

> 28-second vertical house tour, generated from 10 still renders and one music track.

[▶ Watch the demo video](properties/01-house-tour/output/house_tour_vertical.mp4)

<!-- Tip: for an inline player, edit this README on GitHub and drag the .mp4 into the editor. GitHub will replace this line with an embedded video. -->

> **Note:** this repository shows **vertical (9:16) output only**, made for Reels, TikTok and Shorts. Landscape (16:9) output is not included in this example.

---

## What it does

| Input | → | Output |
|---|---|---|
| 10 static architectural renders (JPG/PNG, landscape) | | 1080×1920 vertical walkthrough video |
| 1 royalty-free music track (MP3) | | Music mixed with fades, trimmed to length |
| Property and agent details | | Branded end card with contact info |

**Features**
- 🎥 **Walkthrough camera motion**: slow pans and push-ins that follow the direction a visitor would walk.
- 📐 **Smart vertical reframing**: landscape renders are cropped and panned to fill 9:16, never stretched or letterboxed.
- 🚪 **Front door entry sequence**: a simulated door opening that leads from the exterior into the hallway.
- ✨ **Atmosphere effects**
- 🏷️ **Room labels and address lower third**
- 🎵 **Audio mixing**: background music with fades, trimmed to the video length.
- 📇 **Animated contact card** over a blurred final frame.
- ✅ **Automated QA**
- 🗜️ **Two outputs**: a high-quality master and a compressed, web-optimised delivery file.

---

## Who it's for

Small businesses in property and design that need marketing video but have no video editor on staff:

- **Small property dealers and real estate agents:** turn off-the-plan renders into listing videos for social media before the house is built.
- **Builders and home designers:** show a design to clients as a walkthrough instead of a PDF of stills.
- **Architectural visualisers and interior designers:** add a short-form video product to a render package.

---

## How it works

```
 ┌──────────────┐    ┌───────────────────┐    ┌─────────────────┐    ┌──────────────┐
 │  Renders +   │ →  │  Phase 1: PLAN    │ →  │  Human review   │ →  │ Phase 2:     │
 │  music +     │    │  AI agent inspects│    │  "approved"     │    │ BUILD        │
 │  brief       │    │  every image and  │    │  gate           │    │              │
 └──────────────┘    │  writes a shot-by-│    └─────────────────┘    └──────┬───────┘
                     │  shot plan        │                                  │
                     └───────────────────┘                                  ▼
 ┌──────────────┐    ┌───────────────────┐    ┌─────────────────────────────────────┐
 │ Final MP4    │ ←  │  QA + encode      │ ←  │ Composition & render engine         │
 │ (vertical)   │    │  (FFmpeg)         │    │ motion, transitions, text, effects  │
 └──────────────┘    └───────────────────┘    └─────────────────────────────────────┘
```

1. **Plan:** the agent looks at each render, decides framing and camera direction, and proposes timing, effects and the door-entry treatment. Nothing is rendered yet.
2. **Approve:** you review the plan and reply `approved`, or ask for changes.
3. **Build:** the agent writes a programmatic video composition and renders a master file.
4. **Deliver:** FFmpeg encodes a compressed H.264/AAC copy, and the agent runs quality checks and reports the duration and file sizes.

The plan-then-approve gate keeps a human in control of the creative direction and avoids wasted renders.

---

## v1 vs v2: what changed

Both versions use the same 10 renders and music track. Both are vertical 1080×1920.

| | v1 (first try) | v2 (current) |
|---|---|---|
| Video | [v1_house_tour_vertical.mp4](comparison/v1_house_tour_vertical.mp4) | [v2_house_tour_vertical.mp4](comparison/v2_house_tour_vertical.mp4) |
| Workflow | One pass: brief in, video out | Plan, human approval, then build |
| Built for | Landscape first, vertical as a second export | Vertical first, framed for 9:16 |
| Running time | 34 s | 28 s |
| Front door | Standard crossfade into the hallway | Front door opens into the hallway |
| Local context | Generic | Regional conventions for the target buyer |
| Delivery file | 27.8 MB (about 6.3 Mbps) | 9.5 MB (about 2.5 Mbps), compressed for web |
| Final report | Duration only | Duration and file sizes |

### What I learned
1. **Plan before you render.** v1 went straight from brief to finished video, so every mistake meant a full re-render. In v2 the agent proposes a shot-by-shot plan first and builds only after approval, so problems get fixed on paper.
2. **Design for vertical from the start.** In v1, vertical was a second export of a landscape edit. In v2 every shot is framed for 9:16 from the beginning, with the camera travelling across each wide render so key features stay in view.
3. **Shorter holds attention better.** v1 ran 34 seconds at 3.5 seconds per room. v2 runs 28 seconds at about 3 seconds per room, which suits how fast people scroll on Reels and TikTok.
4. **The entry is the hook.** v1 crossfaded from the exterior to the hallway. v2 opens the front door into the house, so the viewer feels they're walking in.
5. **Know your buyer.** v2 follows local conventions for the target market: room names, spelling, address and phone formats, and inward-opening doors. A generic video looks less credible to local buyers.
6. **Delivery size matters.** The v1 file was 27.8 MB. The v2 file is 9.5 MB after a separate compression step, with no visible loss of quality. Smaller files upload faster and load faster on phones.

---

## Tech stack

| Layer | Tool |
|---|---|
| Editor | **Visual Studio Code** |
| AI agent | **Claude Code** (Anthropic), running in VS Code / the Claude desktop app |
| Agent skills | Video-editing skill (FFmpeg), motion-graphics composition and rendering skill, media and audio skill |
| MCP servers | Browser automation (preview and frame inspection), PDF viewer (reading plans and briefs) |
| Encoding | **FFmpeg** (H.264, AAC, `+faststart`) |
| Rendering | Local rendering, no cloud and no API keys needed for rendering |

> Claude Code **skills** are packaged instruction sets that teach the agent a specialised workflow, for example how to edit and encode video with FFmpeg.
> **MCP** (Model Context Protocol) servers give the agent extra tools, such as driving a browser to preview and inspect frames.

---

## Repository structure

```
.
├── README.md
├── LICENSE
├── prompts/
│   └── prompt.example.md              # Redacted prompt structure (IP protected)
├── comparison/
│   ├── v1_house_tour_vertical.mp4     # first version
│   └── v2_house_tour_vertical.mp4     # current version
└── properties/
    ├── 01-house-tour/
    │   ├── input/                     # renders (in walkthrough order) + music
    │   └── output/
    │       └── house_tour_vertical.mp4
    └── 02-[next-property]/            # coming soon
        ├── input/
        └── output/
```

### Adding another property
1. Create `properties/02-[short-name]/input/` and add the renders (numbered in walkthrough order) and a music track.
2. Run the workflow and save the final video to `properties/02-[short-name]/output/`.
3. Add a row to the gallery below.

Use a short descriptive folder name such as `02-two-storey-family-home`. Don't use a real street address.

## Gallery

| # | Property | Video |
|---|---|---|
| 01 | House tour, 10 renders | [house_tour_vertical.mp4](properties/01-house-tour/output/house_tour_vertical.mp4) |
| 02 | `[coming soon]` | |

---

## Usage (overview)

1. Put your numbered renders (in walkthrough order) and a music track in `properties/<name>/input/`.
2. Open the folder in VS Code with Claude Code.
3. Fill in the prompt template (`prompts/prompt.example.md`) with your property details.
4. Review the agent's shot plan and reply `approved`.
5. Collect the finished video from `properties/<name>/output/`.

---

## About the prompt (IP protected)

The production prompt behind this project is **proprietary**. `prompts/prompt.example.md` shows its **structure only**: phases, sections and placeholders. About 60% of the content, including the camera logic, transition and door-entry techniques, effect rules, timing values and QA checklist, has been redacted.

---

## Credits

- **Music:** "White Clouds – Calm Piano" by **leberch**, via [Pixabay](https://pixabay.com/) (Pixabay Content License).
- **Renders:** "Credit to SADDG Team"

## License

© 2026 Aish Dwivedi. **All rights reserved.** Published for showcase purposes only. See (LICENSE).
