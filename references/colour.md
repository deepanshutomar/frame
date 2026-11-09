# Step 5 — Colour

Two separate jobs: **handling** colour correctly (always) and **grading** it (only when allowed).

## A. Handling (every job)

- Carry the source's colour state (source.md) through every intermediate unchanged.
- Tag every output (encoding.md, step 4).
- Do conversions in high precision: `zscale` (zimg) where available, with a float or 16-bit stage in between:
  `format=gbrpf32le` or `format=rgb48le`, then back to the delivery pixel format at the very end.

### HDR source → SDR delivery (social platforms, most players)

A plain re-encode looks washed out and grey. Tone map it once:

```
-vf "zscale=t=linear:npl=100,format=gbrpf32le,zscale=p=bt709,tonemap=tonemap=hable:desat=0,\
zscale=t=bt709:m=bt709:r=tv,format=yuv420p"
```

Check frames before the full render (section C). Without `zscale`/`tonemap` in the build, say so; don't ship an
un-tonemapped file.

### HDR source → HDR delivery (YouTube HDR, Apple devices)

Keep 10-bit HEVC and the original transfer:
- HLG: `-c:v libx265 -pix_fmt yuv420p10le -x265-params "colorprim=bt2020:transfer=arib-std-b67:colormatrix=bt2020nc"`
  with `-color_primaries bt2020 -color_trc arib-std-b67 -colorspace bt2020nc`.
- PQ/HDR10: transfer `smpte2084`. Carry the mastering-display and content-light metadata from the source
  (`master-display=…:max-cll=…` in `-x265-params`).
- Dolby Vision metadata usually doesn't survive an ffmpeg re-encode. Say so, and fall back to HLG/HDR10.

Ask the user (one question) whether they want HDR or SDR if the target accepts both.

## B. Grading (gated)

Grade **only if all three** are true:
1. The source is log (confirmed by metadata, sidecar or the user, not guessed from looks), **or** the user explicitly
   asks for a grade or correction.
2. Nobody has graded it already (the user didn't say they grade elsewhere, and there's no look baked in).
3. The user said yes to grading in this job.

If the user grades in their own NLE, never grade. Hand over the footage untouched, or with only a clearly labelled
optional preview LUT.

### Order for log footage

1. **Technical transform** (log to Rec.709) with the camera maker's own conversion LUT or transform for that exact
   profile and gamut. Examples: Sony S-Log3/S-Gamut3.Cine → 709, DJI D-Log M → Rec.709, Apple Log / Apple Log 2 →
   Rec.709. Download the LUT from the maker. Don't invent a curve when an official transform exists. If the maker's
   LUT isn't available, ask the user for it.
2. **Balance:** exposure and white balance to match across clips (`eq`, `colorbalance`, `colortemperature`, `curves`),
   in small steps.
3. **Look (optional, only if asked):** gentle, with skin tones protected. "Cinematic" saturation means richer and
   denser colour (darker, more saturated mid-tones), not brighter or neon colour. Keep it subtle unless asked.
4. **Match cameras** when several are cut together: compare stills side by side and adjust until skin tone and
   neutrals match.

Apply LUTs with tetrahedral interpolation in a high-precision stage:
```
-vf "format=gbrpf32le,lut3d=file=CameraToRec709.cube:interp=tetrahedral,format=yuv420p10le"
```
Then encode as in encoding.md, adding 709 tags.

Delivering LUTs instead of a render (for the user's own NLE): export `.cube` files (33 or 65 points) for the
technical transform, the look, and the combined result, each named by camera profile and look.

### Flat but not log

Offer a correction (exposure, white balance, contrast) in one line. Apply it only on a yes. Never push a log LUT onto
Rec.709 footage.

## C. Check with stills before rendering video

- Pull 16-bit PNG or TIFF frames, not JPEG (JPEG bands and shifts colour):
  `ffmpeg -ss T -i IN -frames:v 1 -vf "<the same filter chain>,format=rgb48be" -c:v png still_T.png`.
  Pick 3-5 moments: the darkest, the brightest, a face, a saturated object, and a cut between cameras.
- Put source and graded side by side on one contact sheet and look at it once.
- Measure if needed: `signalstats` (YMIN/YMAX for clipping and crushed blacks), or `waveform` and `vectorscope`
  filters drawn to a PNG.
- Render the video only after the stills pass.
