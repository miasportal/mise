# Mise

## What it is

Mise is an AI cinematography tool for BookTok creators. Speak or type a book review, and Mise directs the video inside a persistent 3D library: it glides the camera across your bookshelves, pulls each named book out into a format-aware **Hero Zone** facing the viewer at a clean 3D angle, holds dedicated **Negative Space** for your talking-head overlay (`9:16` BookTok or `16:9` YouTube), shifts the bookshelf and room lighting with the emotional tone of what you say, and synchronizes every shot's hold duration to your voiceover. Then it watches its own rendered WebGL frames, critiques them against the plan, and automatically patches and re-renders any shot that missed.

Built at the Gemini 4 Argon SF Hackathon (Cerebral Valley x Google DeepMind), October 25, 2026, in the **More Dimensions** track. Built with Google Antigravity and Gemini 4 Argon.

## The problem

BookTok and YouTube creators who read on e-readers have no physical shelf to film in front of. Creators with shelves get a visual identity for free. E-reader readers do not. The audience already exists; the demand is latent. They do not know they can have a library that performs while they just read and talk.

## Who it is for

BookTok creators making review videos, especially e-reader readers with no physical shelf. The library in the video persists across videos like a real room, so viewers can audit the shelf. That continuity is the trust mechanism.

## What was built today versus what was pre-existing

- **Pre-existing (not in this repo)**: The 3D library room itself, built earlier with Claude Code. It is the stage, not the demo: simple, persistent, already made.
- **Built today (in this repo)**: Everything the room does under direction:
  - **Voiceover & Transcript Beat Parser**: Matches spoken `[Title + "by" + Author]` spans against the 3D shelf catalog and calculates voiceover-synchronized beat durations (`~2.2 words/sec` or proportional to recorded mic audio duration).
  - **Format-Aware Negative Space & Hero Zone Framing**:
    - **BookTok `9:16` Vertical**: Reserves a 100% transparent **Bottom-Left Creator Cutout Zone** for the creator's talking head while pulling the 3D book into the **Upper-Right Hero Zone**, dynamically scaled to fill the Hero Box at a horizontal Y-turn that shows a bit of the left spine with zero up/down tilt.
    - **YouTube `16:9` Widescreen**: Reserves a 100% transparent **Left-Side Creator Cutout Zone** while pulling the 3D book into the **Right-Side Full-Height Hero Zone**.
  - **Phased Directing Styles**:
    - **Phase 1 — Simple Cinema Pans, No Frills (`simple_cinema_pans`)**: Pure steady tripod camera glides across wall bookcases (100% bookshelf backdrop, no window or weather distractions), fixed-second pull-out transitions, and full-duration front-cover holds synced to each book's voiceover segment.
    - **Phase 2 — Polished, Serious, Subtle (`polished_serious_subtle`)**: Gentle, well-lit sentiment-driven bookshelf `RectAreaLight` washes and `HemisphereLight` mood crossfades (e.g., *Philosophical Candlelight*, *Gothic Melancholy*, *Stoic Golden Hour*) plus **FOV-compensated focal breathing** (compressing the background bookshelves while keeping the held book locked inside the Hero Zone).
    - **Phase 3 — Over the Top, Visually Catchy (`over_the_top_catchy`)**: High-energy BookTok `0.52s` whip-pans with a brief Dutch-angle camera Z-roll, elastic `-8.5°` FOV crash-zooms, a mid-hold **Cover-Open Peek** (`setOpen`) revealing the book's typeset interior, and sentiment-driven **3D BookTok Stunts & Props** (*Mind-Blown Golden Orbit & Spin-In*, *Heartbreak Shatter Shards*, *Pedestal Halo Ring + Double 360° Spin*, and *3D Trash-Can Toss*).
    - **Phase 4 — Spoken Quote $\rightarrow$ E-Reader Highlight Match**: Matches a spoken or paraphrased favorite quote against the book's real e-reader highlights and displays it on screen.
  - **"More Dimensions" Argon Verification Loop**: Argon inspects the captured WebGL renders and scene telemetry after Beat 3 returns to the shelf, verifies that every named book filled the Hero Zone clear of the creator's Negative Space Zone and that Phase 2/3 emotional lighting, FOV breathing/crash-zooms, and 3D stunts executed accurately, and automatically patches and re-renders any miss.

## Why Argon

The verification loop is the Argon-native core: Argon watches rendered shots, critiques them against the goal, and fixes them. That **render $\rightarrow$ watch $\rightarrow$ critique $\rightarrow$ patch $\rightarrow$ re-render** loop is the More Dimensions statement. The creator's talking head sits in reserved negative space, never as an avatar inside the 3D scene.

## Architecture

- **Capture**: Microphone recording + live Web Speech transcript (or pasted script) with voice `"Cut"` trigger.
- **Parse (`/api/argon/parse-beats`)**: Gemini 4 Argon parses the review into structured beats with exact shelf indices, voiceover-synced durations, format-aware framing offsets, Phase 2 emotional lighting profiles, and Phase 3 3D stunt profiles.
- **Direct (`StageController`)**: Drives the 3D camera along wall bookcases, spawns and dynamically scales the 3D `BookObject` to fill the Hero Zone at the exact camera frustum depth, and holds transparent negative space for the creator.
- **Mood & Stunts (`polished_serious_subtle` & `over_the_top_catchy`)**: Crossfades overhead bookcase `RectAreaLight` washes and ambient `HemisphereLight` while applying FOV-compensated focal breathing (Phase 2) or whip-pans, crash-zooms, cover-open peeks, and 3D orbiting/pedestal/trash-can props (Phase 3).
- **Verify (`/api/argon/verify-render`)**: Audits captured WebGL frames + telemetry against the beat plan, surfaces specific camera/lighting/stunt fixes, and triggers an automatic Pass #2 re-render when a miss is detected.

## How to run it

Prerequisites: Node.js (v22.6+) and an AI Studio API key for Gemini 4 Argon, stored in a local `.env` file (`cp .env.example .env`). API keys are never committed to this repo.

```bash
npm run dev
# Opens the Mise Director Studio at http://localhost:3000
```

Note: the demo runs against a 3D library room. The room used in the hackathon demo is pre-existing and private, and is not included in this repo. The code here is the direction layer that operates on a compatible WebGL scene (with a built-in fallback 3D WebGL stage if the private library bundle is not present).

## Demo

Live demo: a 3-book review covering *The Brothers Karamazov* by Fyodor Dostoevsky, *Jane Eyre* by Charlotte Brontë, and *Meditations* by Marcus Aurelius. All 3 books pull forward into the Hero Zone synchronized to the length of each spoken review segment, bookshelf lighting and focal breathing shift to match each book's emotional tone, and the Argon Verification Loop audits and verifies the render on screen.
