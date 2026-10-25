# Mise

## What it is

Mise is an AI cinematography tool for BookTok and YouTube creators. Speak or type a book review (up to **50 books per video**), and Mise directs the video inside a persistent 3D library: it glides the camera across your bookshelves, pulls each named book out into a format-aware **Hero Zone** facing the viewer at a clean 3D angle, holds dedicated **Negative Space** for your talking-head overlay (`9:16` BookTok or `16:9` YouTube), opens the 3D book while you talk to flip through your **Star Rating**, **Personal Review**, and **Highlighted Quote** before closing back to the front cover, and surrounds the book with high-DPI 3D effects and living pop-up dioramas that frame the outer border without ever covering the title or author's name. Then it watches its own rendered WebGL frames, critiques them against the plan, and automatically patches and re-renders any shot that missed.

Built at the Gemini 4 Argon SF Hackathon (Cerebral Valley x Google DeepMind), October 25, 2026, in the **More Dimensions** track. Built with Google Antigravity and Gemini 4 Argon (`2,097,152`-token context window).

## The problem

BookTok and YouTube creators who read on e-readers have no physical shelf to film in front of. Creators with shelves get a visual identity for free. E-reader readers do not. The audience already exists; the demand is latent. They do not know they can have a library that performs while they just read and talk.

## Who it is for

BookTok and BookTube creators making review videos, especially e-reader readers with no physical shelf. The library in the video persists across videos like a real room, so viewers can audit the shelf. That continuity is the trust mechanism.

## What was built today versus what was pre-existing

- **Pre-existing (not in this repo)**: The 3D library room itself, built earlier with Claude Code. It is the stage, not the demo: simple, persistent, already made.
- **Built today**: Everything the room does under direction:
  - **2M-Token Context Window & Up to 50 Books Per Video**: Configured for Gemini 4 Argon's full `2,097,152`-token input context window (`65,536` max output tokens), matching up to **50 spoken books per video** (`[Title + "by" + Author]`) against the full 863-book 3D shelf catalog with voiceover-synchronized beat durations (`~2.2 words/sec` or proportional to recorded mic audio duration).
  - **Format-Aware Negative Space & Hero Zone Framing**:
    - **BookTok `9:16` Vertical**: Reserves a 100% transparent **Bottom-Left Creator Cutout Zone** for the creator's talking head while pulling the 3D book into the **Upper-Right Hero Zone**, dynamically scaled with top/bottom headroom so top-mounted 3D effects never clip.
    - **YouTube `16:9` Widescreen**: Reserves a 100% transparent **Left-Side Creator Cutout Zone** while pulling the 3D book into the **Right-Side Full-Height Hero Zone**.
  - **Interactive 3-Page Open-Book Sequence (`Stars` $\rightarrow$ `Review` $\rightarrow$ `Highlighted Quote` $\rightarrow$ `Close`)**:
    - While the creator talks about a book, the 3D cover swings open (`setOpen(0.76)`) and flips through up to three archival interior pages:
      1. **Page 1 — Star Rating**: Displays the exact `# of stars` (`1..5` 3D-shaded gold stars) the user gave the book in the library dataset.
      2. **Page 2 — Personal Review**: Typesets the user's review from the library dataset (automatically skipped if the book has no review).
      3. **Page 3 — Highlighted Quote**: Displays one of the user's highlighted e-reader quotes with a warm yellow highlight marker (automatically skipped if the book has no highlights).
    - As soon as the available pages finish, the book smoothly closes (`setOpen(0)`) to showcase the front cover again before returning to the shelf.
  - **Phased Directing Styles & Non-Occluding 3D Effects**:
    - **Phase 1 — Simple Cinema Pans, No Frills (`simple_cinema_pans`)**: Pure steady tripod camera glides across wall bookcases, fixed-second pull-out transitions, and voiceover-synced holds.
    - **Phase 2 — Polished, Serious, Subtle (`polished_serious_subtle`)**: Preserves natural room and shelf lighting while adding smooth vertical floating motion and **FOV-compensated focal breathing**.
    - **Phase 3 — Over the Top, Visually Catchy (`over_the_top_catchy`)**: High-energy `0.52s` whip-pans with Dutch-angle Z-roll, elastic `-7.5°` crash-zooms, 3-page open-book sequence, **Argon 4 Living Pop-Up Book Diorama**, and **10 High-DPI Sculpted 3D Cutout Effects** (*5-Pointed Starburst*, *Glossy Floating Hearts*, *Cleaved Broken Heart*, *Jeweled Royal Crown*, *Electric Lightning Bolts*, *Rising Tapered Flames*, *Teardrop Crystal Rain*, *Crossed Broadswords*, *Runic Portal Vortex*, and *Cylindrical Trash-Can Toss*). All 3D effects use hardware depth testing and outer-border rectangular projection so they **never cover the book title or author's name**.
  - **"More Dimensions" Argon Verification Loop**: Argon inspects the captured WebGL renders and scene telemetry after the sequence completes, verifies that every named book filled the Hero Zone clear of the creator's Negative Space Zone and that camera movement and 3D stunts executed accurately, and automatically patches and re-renders any miss.

## Why Argon

The verification loop is the Argon-native core: Argon watches rendered shots, critiques them against the goal, and fixes them. That **render $\rightarrow$ watch $\rightarrow$ critique $\rightarrow$ patch $\rightarrow$ re-render** loop is the More Dimensions statement. The creator's talking head sits in reserved negative space, never as an avatar inside the 3D scene.

## Architecture

- **Capture**: Microphone recording + live Web Speech transcript (or pasted script) with voice `"Cut"` trigger.
- **Parse (`/api/argon/parse-beats`)**: Gemini 4 Argon (`2,097,152`-token context window) parses up to 50 books per video into structured beats with exact shelf indices, voiceover-synced durations, format-aware framing offsets, and 3D stunt profiles.
- **Direct (`StageController`)**: Drives the 3D camera along wall bookcases, spawns and dynamically scales the 3D `BookObject` inside the Hero Zone, animates the 3-Page Open-Book Sequence (`Stars` $\rightarrow$ `Review` $\rightarrow$ `Quote` $\rightarrow$ `Close`), and renders non-occluding Pop-Up Dioramas and 3D effects around the outer border of the book.
- **Verify (`/api/argon/verify-render`)**: Audits captured WebGL frames + telemetry against the beat plan, surfaces specific camera/framing/stunt fixes, and triggers an automatic Pass #2 re-render when a miss is detected.

## How to run it

Prerequisites: Node.js (v22.6+) and an AI Studio API key for Gemini 4 Argon, stored in a local `.env` file (`cp .env.example .env`). API keys are never committed to this repo.

```bash
npm run dev
# Opens the Mise Director Studio at http://localhost:3000
```

Note: the demo runs against a 3D library room. The room used in the hackathon demo is pre-existing and private, and is not included in this repo. The code here is the direction layer that operates on a compatible WebGL scene (with a built-in fallback 3D WebGL stage if the private library bundle is not present).

## Demo

Live demo: a 3-book review covering *The Brothers Karamazov* by Fyodor Dostoevsky, *Jane Eyre* by Charlotte Brontë, and *Meditations* by Marcus Aurelius. All 3 books pull forward into the Hero Zone synchronized to the length of each spoken review segment, bookshelf lighting and focal breathing shift to match each book's emotional tone, and the Argon Verification Loop audits and verifies the render on screen.
