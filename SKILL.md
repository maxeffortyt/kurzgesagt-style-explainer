---
name: kurzgesagt-style-explainer
description: Make a short flat-vector science explainer (about 60 s) in the measured visual style of Kurzgesagt – In a Nutshell, style only (no characters, logos or footage), with Remotion, an ElevenLabs voice-over synced by word timestamps, and a measurement loop that scores the finished render against the style. Use when asked for a Kurzgesagt-style or "flat vector space-y" animated explainer, or when you need to measure a video style in numbers and hit those numbers.
---

# Kurzgesagt-style explainer

This skill turns one topic into a ~60-second animated explainer that matches a *measured* style, then checks the render against the same measurements. The numbers come from 8 one-minute clips of Kurzgesagt videos (analysed at 10 fps, 4,800 frames). Read `STYLE.md` before you design anything.

**Hard rules**
- Copy the style only. Never use the reference channel's characters, mascots (the birds), logo, footage or images. Every shape is your own SVG/React.
- Keep the science accurate. Fact-check every number in the script before you generate the voice.
- Do not download music or sound effects unless the user provides them. Synthesize sound effects yourself if the folder is empty.
- Never print or commit API keys. Read them from the environment or a `.env` the user provides.

## Workflow

### 1. Style target
- Default: use `STYLE.md` and `reference/ref_profile.json` as they are (numbers only, no frames).
- To target another style: pick 5–10 one-minute clips you are allowed to analyse, then
  ```bash
  python3 tools/style_metrics.py clip.mp4 reports/clip.json --fps 10     # one report per clip
  python3 tools/similarity.py build reports/ my_profile.json              # mean/std per metric + palette pool
  ```
  and write your own `STYLE.md` with the same table. Analyse at 10 fps and say so; don't claim more than you measured.

### 2. Brief
Use `prompt.md` (the exact prompt from the experiment, plus a template). One prompt, no back-and-forth: the run decides the story and the visuals on its own.

### 3. Project
- Blank Remotion 4 project, 1920×1080, 30 fps, 60 s ± 2 s, output `out/final.mp4`.
- Copy `templates/remotion.config.ts`: on macOS 26 Remotion's downloaded `chrome-headless-shell` crashed ("icudtl.dat not found" → "Target closed"), so the config points Remotion at the installed Google Chrome, and it renders with **concurrency 1** (2 tabs produced single tiled/flickering frames on an 8 GB M1).
- Draw everything as flat SVG in React. Hit the style numbers on purpose:
  - dark backgrounds are deep navy/purple, almost never pure black;
  - the camera drifts in most shots (moving ~57% of frame pairs), slowly;
  - something new appears about every 3 s (~21 events/min), a hard cut about every 7 s (~8/min);
  - **not too flat**: the reference is ~81% flat colour. Every run in the experiment came out 93–99% flat with half the edge detail. Add soft glows, gradients, inner shadows and fine detail.
  - measure camera motion with `tools/style_metrics.py`, not a home-made approximation: one run's own check said the camera barely moved, it added pans, and the real metric came out far too high.

### 4. Narration and voice
- Write `narration.txt`. With a voice at ~2.5 words/s, about 145–150 words fill 60 s. If the take is too long, cut words; don't rely on a `speed` setting (it was ignored by the model used here).
- Generate the voice with timestamps (curl, because some Python installs lack SSL certificates):
  ```bash
  set -a; source .env; set +a          # ELEVENLABS_API_KEY, ELEVENLABS_VOICE_ID, ELEVENLABS_MODEL_ID
  tools/tts.sh narration.txt public/voice      # → public/voice.mp3 + public/voice.alignment.json
  python3 tools/alignment_to_words.py public/voice.alignment.json src/words.json --offset 0.5
  ```
- Key every cut, label and sound effect to a word index (cut 2–3 frames before the cue word).

### 5. Sound
- Music under the voice (about −10 dB), fading in and out. Synthesize sound effects (whoosh, pop, tick, rumble…) if none are provided.
- You can't listen to the mix, so measure it: render the bed without the voice (an input prop) and check that the voice stays at least ~6 dB above it. For a WAV render use `--codec=wav --audio-codec=pcm-16`.

### 6. Render and QA
```bash
npx remotion render <CompositionId> out/final.mp4          # settings come from remotion.config.ts
python3 tools/render_glitches.py out/final.mp4              # expect: glitch frames: []
python3 tools/style_metrics.py out/final.mp4 out/metrics.json --fps 10
python3 tools/similarity.py score reference/ref_profile.json out/metrics.json
```
Read the per-metric `parts`, fix the two or three lowest, re-render. Note: real Kurzgesagt clips scored against the other seven land between about 64 and 93 (typical ≈ 87), so the score measures statistics, not craft; look at the frames too.

### 7. Notes
Write `NOTES.md`: what you made, the shot list with times, the science checks and simplifications, what didn't work, what you'd improve. `LESSONS.md` lists what five real runs learned.

## Files
- `STYLE.md`: the measured style (numbers and palettes only).
- `prompt.md`: the experiment prompt and a reusable template.
- `LESSONS.md`: lessons from the five real runs (low → max).
- `reference/ref_profile.json`: reference statistics for `similarity.py score`.
- `tools/`: `style_metrics.py`, `similarity.py`, `render_glitches.py`, `tts.sh`, `alignment_to_words.py`.
- `templates/remotion.config.ts`: render settings that worked (system Chrome, concurrency 1).
