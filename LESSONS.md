# LESSONS.md: what five real runs learned

Source: the `NOTES.md` that each run wrote itself, plus our measurements of the finished videos. Same prompt (`prompt.md`), same model (Claude Opus 5.5), five effort levels, each run in its own blind folder with no human input after the task arrived. Costs are estimates at API prices; the runs were made on a subscription.

| run | time | responses | cost (API prices) | video | similarity (0–100) |
|---|---|---|---|---|---|
| low | 6.6 min | 18 | ~$1.04 | 60.0 s, 7 scenes, 5 synthesized SFX | 69.7 |
| medium | 16.1 min | 52 | ~$3.68 | 61.9 s, 10 shots, 11 SFX | 84.1 |
| high | 19.8 min | 54 | ~$4.32 | 61.5 s, 10 scenes, 13 SFX | 83.6 |
| extra high | 44.6 min | 80 | ~$8.68 | 60.0 s, 12 shots, 21 SFX | 87.0 |
| max | 70.1 min | 146 | ~$20.77 | 60.0 s, 11 shots, 122 synthesized SFX cues | 86.9 |

Max cost more than the other four runs combined ($17.72) and scored a tenth of a point below extra high. The whole experiment: about $38.49.

For scale: each real Kurzgesagt clip, scored against the other seven with the same formula, lands between about 64 and 93 (typical ≈ 87). The score measures statistics (colour, motion, cuts), not craft.

## Story and script
- **They all wrote the same video.** All five told the same story: the Kola Superdeep Borehole, 180 °C at the bottom, rock that behaves like plastic, a core as hot as the Sun's surface. Three used the exact words "an ocean of liquid iron" (max wrote "an ocean of molten iron"), three scripts were exactly 149 words, and all five picked the same music track. If you want variety, ask for a specific angle, character or structure.
- **Length is set by words, not by speed.** With fixed voice settings the only lever was the text. About 145–150 words gave 57–62 s with this voice. High's first draft (184 words) ran 72 s and had to be cut twice; medium's first take ran 65 s; extra high trimmed 7 words from a 59.9 s take to leave room for an intro and outro; max cut 156 words (62.1 s) down to 142 (57.1 s).
- **Science:** zero factual errors in the five narrations (checked against USGS, the Kola borehole record and published core-temperature estimates). All avoided the classic traps ("sounds from hell", "the longest hole ever", "the mantle is lava", "hotter than the Sun"). Four of five used ≈ 5,200 °C for the inner core (a common textbook figure); max gave a range, ~5,000–6,000 °C, which matches newer estimates (a 2013 lab experiment puts it near 6,000 °C). Both are "about as hot as the Sun's surface".
- **Scale honesty:** at true scale the crust is thinner than a line. Low drew it thicker "without saying so"; extra high kept true scale except a thin visible skin. Say when a diagram is not to scale.

## Visual style (measured on the finished videos)
- **Too flat, everywhere:** 93–99% flat colour against the reference 81%, and about half the edge detail. Max came closest (93%) after it measured itself and added light, shadow and fine detail. Add soft gradient lighting, glows, inner shadows and fine detail. High wrote it itself: "The current look is very flat and a bit 'clip-art'."
- **Navy, not black:** all five got the dark backgrounds right; saturation was on target (0.68–0.78).
- **Motion:** low moved the camera in only 19% of frame pairs (reference 57%) and the picture changed in 30% of frame pairs (reference 79%). Max overshot the other way: its own (approximate) check said the camera barely moved, so it added pans everywhere, and the real metric then measured 84%. Measure with the same tool as the reference.
- **Busy vs calm:** high measured itself at about 40 events per minute against ~21 and wrote "It reads as busy in places." Extra high found its own activity too high and suggested "a few moments of true stillness". Hit the reference, don't exceed it.
- **Cuts:** 9–12.6 hard cuts per minute against 8.1. Max hit 9 and the closest event rate (22 per minute against 21). Prefer a couple of match cuts or continuous zooms over more hard cuts.
- **What the runs wanted to add next:** smoother transitions (match cuts, zoom-throughs), more depth on planets, a small original character for human scale, livelier secondary motion.

## Sync
- Use the ElevenLabs `/with-timestamps` endpoint and turn character timings into word timings. Key every cut, label, counter and sound effect to a word index; cuts landed 0.1 s (medium) or 3 frames (extra high) before the cue word.
- Start the voice ~0.4–0.5 s in and leave ~2 s of tail.
- Check the take: max ran the voice-over through ElevenLabs speech-to-text and got the script back word for word.
- One run's zoom-out lost the borehole off screen because pan and zoom were interpolated differently; a camera path that keeps the subject on a smooth screen path fixed it. Extra high moved the camera only along a log-scale zoom path so the focus stays put.

## Sound
- **Claude can't listen.** High picked the music "by its title and length without hearing it"; extra high picked it by its loudness curve. All five chose the same track. Max measured its tempo (79.8 bpm), found the bass drop and started the track so the drop lands on the big zoom-out, then spliced into the song's own outro at a phrase boundary. Choose music by mood on purpose, and have a human listen once.
- **Measure the mix instead:** extra high rendered the music+effects bed without the voice (an input prop) and checked the voice stays at least 6.2 dB above it. `remotion render --codec=wav` needs `--audio-codec=pcm-16`.
- **Synthesized effects work** (whoosh, pop, tick, rumble, sizzle, chime…), but drills and creaks can sound synthetic.
- **Loudness:** low to extra high peaked between −0.6 and −4 dBFS but sat around −18 to −25 dB (RMS / short-term windows). Max built its own mix in numpy: compressed voice at −16.5 LUFS, music about 14 LU under it with ducking, a look-ahead limiter, −16 LUFS integrated, true peak −1.6 dBTP. Its first pass had a sign error (−9 LUFS, clipping) that it caught by measuring.

## Tools and environment
- **Remotion's bundled ffmpeg is stripped down:** no `xstack`, `tile`, `drawtext`, `rawvideo`, `ebur128`, `volumedetect` or `fps` filter. Medium couldn't run its own style measurement because of it; high and extra high decoded frames to PNG/JPEG and audio to WAV and measured in Node or Python instead. Use `tools/style_metrics.py` with OpenCV.
- **macOS 26:** Remotion's downloaded `chrome-headless-shell` crashed ("icudtl.dat not found" → "Target closed"). Point Remotion at the installed Chrome (`templates/remotion.config.ts`).
- **Concurrency 1:** two render tabs produced single tiled/flickering frames. With one tab and `tools/render_glitches.py`, all four videos had 0 bad frames.
- **Pixel format:** one render came out tagged `yuvj420p` (full range) from the JPEG frame pipeline even with `yuv420p` set. It plays fine.
- **Giant shapes at extreme zoom:** a planet circle at 12 km zoom would be over a million pixels wide; max switched to exact polyline arcs above 30,000 px so the 12 km → 13,000 km zoom stays clean.
- **Fonts:** system fonts (for example Avenir Next on macOS) fall back to Helvetica on other machines.
- **Auto mode:** the safety classifier blocked low, medium and max at the start (README reads; for max, a command that printed `.env` even redacted). All carried on; max then sourced `.env` without echoing anything. Not a blocker.

## Process
- **Blind the test.** The first low run read a stale hand-over note from an earlier session ("render is broken in the sandbox"), believed it and switched off its sandbox 4 times for a problem that was already fixed. It was thrown away. The real runs got identical blank folders with neutral names, blocked access to everything else, and a dummy first message ("reply: ready") before the task.
- **Watch the settings file:** a value with parentheses broke `source .env`. Keep `.env` values simple.
- **Measure, fix, measure again:** high and extra high measured their render once and listed the gaps; only max fixed what it found and measured again (camera pans, contrast, detail, flashes at cuts). It still scored like extra high, at more than twice the cost.
