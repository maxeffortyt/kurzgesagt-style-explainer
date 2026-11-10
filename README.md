# kurzgesagt-style-explainer

A free Claude Code skill for making short flat-vector science explainers in the **measured** visual style of Kurzgesagt – In a Nutshell, and for checking the result with numbers instead of vibes.

It comes from an experiment: one prompt, five Claude Code runs at different effort levels (low, medium, high, extra high, max), each making the same 60-second explainer, *"Why you can't dig through the Earth"*. Every run was measured against the real style. Everything that worked, and everything that didn't, is in here.

> Style only. This skill contains **no** Kurzgesagt frames, footage, characters, logos or audio, only numbers measured from 8 one-minute clips. Not affiliated with or endorsed by Kurzgesagt. Go watch the real thing: https://www.youtube.com/@kurzgesagt

## What's inside
| file | what it is |
|---|---|
| `SKILL.md` | the workflow Claude follows: style target → prompt → Remotion build → voice with timestamps → render → measure and fix |
| `STYLE.md` | the measured style: 13 metrics (mean, spread, range), 8 palettes, observations, and where Claude's versions missed |
| `prompt.md` | the exact prompt used in the experiment and a reusable template |
| `LESSONS.md` | lessons from the real runs: time, cost, scores, what broke, what each run would improve |
| `reference/ref_profile.json` | reference statistics for the similarity score (numbers only) |
| `tools/style_metrics.py` | measures a video: palette, near-black, brightness, contrast, saturation, flatness, edge density, camera motion (phase correlation), activity, events/min, hard cuts/min |
| `tools/similarity.py` | builds a reference profile and scores a clip 0–100 against it (weights fixed before the experiment) |
| `tools/render_glitches.py` | finds single corrupted frames in a render |
| `tools/tts.sh`, `tools/alignment_to_words.py` | ElevenLabs voice-over with character timestamps (curl) → word timings for sync |
| `templates/remotion.config.ts` | render settings that worked (system Chrome on macOS 26, concurrency 1) |

## Install
```bash
git clone {GITHUB_URL} ~/.claude/skills/kurzgesagt-style-explainer
```
Then ask Claude Code for a Kurzgesagt-style explainer on any topic, or point it at `SKILL.md`.

**Requirements:** Node 20+ and Remotion 4, Python 3 with `opencv-python` and `numpy` (`pip install opencv-python numpy`), Google Chrome (used by the render config), an ElevenLabs API key for the voice. Put your keys in a `.env` in your project (`ELEVENLABS_API_KEY`, `ELEVENLABS_VOICE_ID`, `ELEVENLABS_MODEL_ID`) and never commit it.

## Measure anything
```bash
python3 tools/style_metrics.py out/final.mp4 out/metrics.json --fps 10
python3 tools/similarity.py score reference/ref_profile.json out/metrics.json
python3 tools/render_glitches.py out/final.mp4
```
To target another style, measure your own references with `style_metrics.py` and build a profile with `similarity.py build`.

## Results from the experiment (same prompt, Claude Opus 5.5)
| effort | time | cost at API prices | similarity |
|---|---|---|---|
| low | 6.6 min | ~$1.04 | 69.7 |
| medium | 16.1 min | ~$3.68 | 84.1 |
| high | 19.8 min | ~$4.32 | 83.6 |
| extra high | 44.6 min | ~$8.68 | 87.0 |
| max | 70.1 min | ~$20.77 | 86.9 |

Real Kurzgesagt clips, each scored against the other seven, land between about 64 and 93 (typical ≈ 87). So the score tells you when the statistics are off, not whether the video is good. The biggest gap in every run: **too flat** (93–99% flat colour against 81%). Max cost more than the other four combined and scored about the same as extra high (86.9 vs 87.0). See `LESSONS.md`.

## Credits
- Style reference: Kurzgesagt – In a Nutshell. The style belongs to the artists who built it; this project only measures it.
- Rendering: [Remotion](https://www.remotion.dev). Voice: ElevenLabs.
- Made by the maxeffort channel.

## License
MIT, see `LICENSE`. The license covers the code and text in this folder, not Kurzgesagt's work or trademarks.
