# Step 8 — Draft, final, verify

## Draft

- Render a cheap preview from proxies (encoding.md, section 5) for the user to judge the edit itself: timing, framing,
  text, sound.
- Look at the draft through a few stills or a contact sheet, not by watching it frame by frame. Don't re-render the
  final for every small change. Batch the fixes.
- Two fix rounds at most. If it's still wrong after the second, change approach or ask the user.

## Final

- Re-render from the originals with the final settings. Never from the proxy or the draft.
- Run the "quality killers" checklist (encoding.md) once before starting.
- Use the render time to plan the hand-over, not to start other heavy jobs on a light machine.

## Verify (always, before handing over)

### 1. Same specs as intended

Probe the output with the same `ffprobe` command as step 2 and compare it with the source card:

| Check | Pass |
|---|---|
| Resolution, aspect | as planned |
| Frame rate | as planned (CFR at the right rate) |
| Bit depth / pix_fmt | as planned (not dropped to 8-bit by accident) |
| Colour tags and range | present and correct for the target |
| Duration | matches the edit plan (within one frame) |
| Audio | codec, 48 kHz, channels, loudness target met |
| Video bitrate | inside the band below |

**Bitrate band (H.264-equivalent, SDR, after an edit with no intended loss):**
- At least about 60% of the source's bitrate when the source is a high-quality camera file (≥20 Mbps at 1080p).
  Inter-frame camera codecs carry headroom, so a well-made CRF 17 master is often smaller than the camera file.
  That's fine.
- At least about 90% when the source is already a delivery-style file (≤15 Mbps at 1080p). Below that, generation
  loss is likely.
- Below the band: lower the CRF by 2 and re-render, or report it if a size cap forced it.
- HEVC can sit at about 60-70% of the H.264 numbers at the same quality; AV1 lower still.

### 2. Same picture (when the edit didn't change the pixels on purpose)

For straight re-encodes or conversions, measure against the source:
```
ffmpeg -i OUT -i SRC -lavfi "[0:v][1:v]ssim" -f null -           # "All" ≥ 0.98 ≈ same; < 0.95 investigate
ffmpeg -i OUT -i SRC -lavfi "[0:v][1:v]libvmaf" -f null -        # VMAF ≥ 95 if the build has libvmaf
```
Both inputs must be the same size and frame rate. Scale or trim the source first if needed. For creative edits, these
numbers don't apply. Compare 3-5 stills of the untouched stretches (face, shadows, a gradient) side by side at 100%,
looking for blocking, banding or colour shift.

### 3. Sound

- Sync: pull 2-second clips at the start, the middle, the end and around hard cuts. Check that lips and hits line up
  (earshot `timeline` shows it when available).
- Loudness and true peak are in target (audio.md).
- No clipped peaks, pops at cuts or missing tracks.

### 4. Plays where it's going

- MP4 has `faststart`. The file opens in QuickTime/VLC without warnings (`ffprobe` shows no errors).
- Vertical video isn't marked as rotated by accident.
- For social, safe zones are clear of text and faces.

## Report to the user (two lines)

```
final: <path> · <codec> <w>x<h> <fps> <bit depth> · <N> Mbps (source <M> Mbps) · <colour> · audio <codec> <LUFS>
check: specs ✓ · SSIM <x> / stills ✓ · sync ✓ · <anything intentionally lost, or "no intended loss">
```
