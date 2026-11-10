# STYLE.md: the measured visual style of Kurzgesagt – In a Nutshell

Numbers only. No frames, characters or artwork from the channel are included here.

**What was measured:** 8 one-minute clips (seconds 120–180 of 8 Kurzgesagt videos), analysed at **10 frames per second** (4,800 frames in total), frames downscaled to 320 px wide, with `tools/style_metrics.py`. Every number below was measured, not estimated. The same statistics, in machine-readable form, are in `reference/ref_profile.json`.

## Metrics (mean ± standard deviation, [min–max] over the 8 clips)
| metric | value | meaning |
|---|---|---|
| near_black | 0.25 ± 0.22 [0.01–0.57] | share of pixels with luma < 20/255 |
| luma_mean | 0.37 ± 0.19 [0.13–0.71] | average brightness (0–1) |
| luma_std | 0.25 ± 0.04 [0.19–0.30] | contrast |
| sat_mean | 0.68 ± 0.16 [0.37–0.87] | average HSV saturation (0–1) |
| flatness | 0.81 ± 0.08 [0.72–0.98] | share of pixels inside flat colour areas (flat vector look) |
| edge_density | 0.066 ± 0.019 [0.040–0.104] | share of edge pixels (Canny) |
| cam_moving | 0.57 ± 0.15 [0.38–0.92] | share of frame pairs where the whole picture shifts (camera moves) |
| cam_speed | 1.1 ± 0.5 px/frame [0.6–2.0] | typical camera speed when moving (320 px wide, 10 fps) |
| activity | 0.79 ± 0.10 [0.66–1.00] | share of frame pairs where something visibly changes |
| change_area | 0.078 ± 0.028 [0.040–0.115] | average share of the picture that changes per frame pair |
| events_per_min | 21 ± 5 [13–28] | new elements / big motions per minute |
| cuts_per_min | 8.1 ± 2.6 [5–12] | hard cuts per minute (slow transitions not counted) |
| shot_median_s | 5.3 ± 2.0 s [3.0–10.0] | median shot length |

## Palettes (8 dominant colours per clip, share of pixels)
- clip 1: #0a0223 39%, #260c4d 24%, #61196f 13%, #1c1ea9 9%, #19b3cf 5%, #d6d2ee 4%, #7d5bd1 4%, #e17747 2%
- clip 2: #09011e 37%, #230a46 20%, #58ccd2 8%, #69196c 8%, #cc981b 8%, #2d4aa3 7%, #f6f7eb 7%, #cc8091 5%
- clip 3: #57c8f7 24%, #180e51 17%, #368a9b 15%, #e1a243 14%, #6b1489 13%, #c7384d 6%, #82260e 6%, #e8e2d8 5%
- clip 4: #d1f4e6 29%, #f2ef8b 21%, #eaca27 13%, #262d43 9%, #778a25 9%, #1c9b62 9%, #70c4c9 7%, #a6385d 4%
- clip 5: #020829 43%, #121e58 15%, #7362c0 9%, #6a0697 9%, #bb9edc 8%, #25428e 8%, #169f94 4%, #98ce1c 3%
- clip 6: #e2eab9 29%, #222c45 21%, #3396e6 14%, #40d0ed 13%, #cf539d 8%, #1392a9 8%, #eca64a 5%, #5d3ec8 3%
- clip 7: #350756 33%, #6ad6ee 16%, #c8f5eb 13%, #43c25f 11%, #8eeb27 9%, #92129f 7%, #306c83 6%, #e6835c 4%
- clip 8: #000102 35%, #091617 32%, #1f2c2f 12%, #36464d 9%, #5b6769 6%, #f0f1f2 2%, #909b98 2%, #13a3c2 2%

Pattern: a deep navy or purple background, a turquoise/cyan, and one warm accent (orange, yellow or pink).

## Dark pixels
In 7 of 8 clips, pure black (RGB < 12) is under 3% of pixels; 96–100% of the very dark pixels are saturated navy or purple, not black. (Clip 8, deep-space voids, is the exception: 34% pure black.)

## Observations
1. It almost never uses pure black: dark backgrounds are deep navy or purple.
2. Something changes in about 4 of every 5 frame pairs; the camera is drifting more than half of the time, slowly.
3. A new visual event roughly every 3 seconds (about 21 per minute).
4. A hard cut roughly every 7 seconds (about 8 per minute), plus transitions.
5. About 81% of pixels sit in flat areas of colour: flat vector shapes, no photographic textures.

## Where Claude's versions missed (measured on 5 real runs, low → max)
- **Too flat:** 93–99% flat colour against 81%, and about half the edge detail (0.027–0.037 against 0.066). The real thing has soft glows, gradients and fine detail. Flat design isn't actually flat.
- **Too many cuts:** 9–12.6 hard cuts per minute against 8.1.
- **Max overshot the camera:** moving in 84% of frame pairs against 57%, after its own rougher check said it barely moved.
- **Low effort barely moved the camera:** moving in 19% of frame pairs against 57%, and the picture changed in only 30% of frame pairs against 79%.
- **Got right everywhere:** dark navy/purple instead of black, and saturation in range (0.68–0.78 against 0.68 ± 0.16).
