# Mise

## What it is

Mise is an AI cinematography tool for BookTok creators. Speak a book review out loud, and Mise directs the video: it pulls each named book forward in a 3D library, shifts the lighting with the mood of what you say, flashes a favorite highlight, and holds negative space for your talking-head overlay. Then it watches its own work, critiques it, and fixes what missed.

Built at the Gemini 4 Argon SF Hackathon (Cerebral Valley x Google DeepMind), October 25, 2026, in the More Dimensions track. Built with Google Antigravity and Gemini 4 Argon.

## The problem

BookTok and YouTube creators who read on e-readers have no physical shelf to film in front of. Creators with shelves get a visual identity for free. E-reader readers do not. The audience already exists; the demand is latent. They do not know they can have a library that performs while they just read and talk.

## Who it is for

BookTok creators making review videos, especially e-reader readers with no physical shelf. The library in the video persists across videos like a real room, so viewers can audit the shelf. That continuity is the trust mechanism.

## What was built today versus what was pre-existing

Pre-existing (not in this repo): the 3D library room itself, built earlier with Claude Code. It is the stage, not the demo: simple, persistent, already made.

Built today (in this repo): everything the room does under direction: microphone to transcript to structured beats (named books, emotion shifts); camera choreography (each named book pulls forward as named, negative space held for the talking-head overlay); mood lighting and weather; highlight flash; and the verification loop (Argon watches the rendered shots, checks every named book got a shot and every emotion shift got a lighting change, names misses, corrects, re-renders).

## Why Argon

The verification loop is the Argon-native core: Argon watches rendered video, critiques it against the goal, and fixes it. That watch, critique, fix loop is the More Dimensions statement. The talking head is an ordinary overlay in reserved negative space, never a person inside the 3D scene.

## Architecture

- Capture: microphone to transcript.
- Parse: Argon turns the transcript into beats: named books and emotion shifts.
- Direct: the camera director moves the camera, pulls named books forward, and holds negative space.
- Mood: lighting and weather follow the emotion of each beat.
- Highlight: a favorite quote or highlight flashes when its book is featured.
- Verify: Argon watches the rendered shots, checks them against the beats, and corrects misses.

## How to run it

Prerequisites: an AI Studio API key for Gemini 4 Argon, stored in a local .env file. API keys are never committed to this repo.

TODO: fill in the start command from your project before publishing.

Note: the demo runs against a 3D library room. The room used in the hackathon demo is pre-existing and private, and is not included in this repo. The code here is the direction layer that operates on a compatible WebGL scene.

## Demo

Live demo: a 40-second voiceover covering 3 books (one loved, one that wrecked the reviewer, one to press into a stranger's hands). All 3 books pull forward as named, lighting follows emotion, a highlight flashes, and the verification check passes on screen.
