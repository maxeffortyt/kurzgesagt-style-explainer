# Prompt

## The exact prompt from the experiment
This went, word for word, to five separate Claude Code sessions (effort low, medium, high, extra high and max). Each session had a blank Remotion 4 project with `STYLE.md` in it, a shared `.env` two folders up, a music folder with three tracks and an empty sound-effects folder.

```
Make a 60-second educational explainer video: "Why you can't dig through the Earth". Use the visual style described in STYLE.md (measurements of the Kurzgesagt – In a Nutshell style).

Rules:
- Copy the style only. Do not use Kurzgesagt's characters, logo, mascots or any of their footage or images. Everything must be original.
- You decide what the story is and what to show. Keep the science accurate.
- This folder is a blank Remotion 4 project. Build the whole video here: write the narration script, design every visual as vector graphics (SVG / React), animate it, and add sound.
- Output: 1920x1080, 30 fps, 60 seconds (±2 s), rendered to out/final.mp4. Keep the render settings in remotion.config.ts.
- Narration: write it to narration.txt (English) and generate the voice-over with the ElevenLabs API, using ELEVENLABS_API_KEY, ELEVENLABS_VOICE_ID and ELEVENLABS_MODEL_ID from ../../.env, voice settings {"stability": 0.5}, output format mp3_44100_192. Use exactly this voice and these settings; don't change them. Sync the animation to the voice-over. Use curl for HTTP calls (Python on this machine has no SSL certificates).
- Music and sound effects: only files from ../../assets/music and ../../assets/sfx, or sounds you synthesize yourself. Nothing downloaded from the internet.
- Work on your own from start to finish. Don't ask me anything. When you're done, write NOTES.md: what you made, what you'd improve, and anything that didn't work.
```

Why some lines are there:
- "Use curl…": a fact about that machine, so the runs didn't waste time on SSL errors.
- "Keep the render settings in remotion.config.ts": the config holds the system-Chrome path and concurrency 1 (see `templates/remotion.config.ts`).
- Fixed voice and settings: the five runs had to be comparable. A side effect: the only way to fit 60 s was to cut words.

## Reusable template
```
Make a {LENGTH}-second educational explainer video: "{TOPIC}". Use the visual style described in STYLE.md (numbers measured from the reference style).

Rules:
- Copy the style only. Do not use the reference channel's characters, logo, mascots or any of their footage or images. Everything must be original.
- You decide what the story is and what to show. Keep the science accurate; list every number you use and where it comes from in NOTES.md.
- This folder is a blank Remotion 4 project. Write the narration, design every visual as vector graphics (SVG / React), animate it, and add sound.
- Output: 1920x1080, 30 fps, {LENGTH} seconds (±2 s), rendered to out/final.mp4. Keep the render settings in remotion.config.ts.
- Narration: narration.txt (English). Voice-over with the ElevenLabs API (/with-timestamps), using ELEVENLABS_API_KEY, ELEVENLABS_VOICE_ID and ELEVENLABS_MODEL_ID from .env. Sync every cut and label to the word timings.
- Music and sound effects: only the files in assets/, or sounds you synthesize yourself.
- Before you finish, measure the render with tools/style_metrics.py and tools/similarity.py against reference/ref_profile.json, fix the weakest metrics (watch flatness: aim near 0.81, not 0.98), and check frames with tools/render_glitches.py.
- Work on your own from start to finish. When you're done, write NOTES.md.
```
