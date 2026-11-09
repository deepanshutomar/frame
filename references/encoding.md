# Step 4 — Plan the encode chain

Goal: footage gets lossy-encoded **once**, at the end, with settings that keep it looking like the source.

## 1. Count the generations

Write the chain before rendering, for example:
`camera HEVC 10-bit -> [ProRes 422 HQ intermediate] -> composite with alpha graphics + captions -> [final x264 CRF 17]`.
Each lossy step is one generation. Aim for one lossy step after the camera.

| Situation | Chain |
|---|---|
| Cuts only, same source | Stream copy. Zero generations. |
| One pass of filters/overlays | Source → final encode. One generation. |
| Several passes (conform, then grade, then composite) | Source → intermediate → … → final encode. Intermediates are near lossless. |
| Graphics from a renderer | Renderer → alpha layer (ProRes 4444 / PNG). The original footage goes into the final composite directly. |

## 2. Intermediates (between passes)

| Intermediate | Use | ffmpeg |
|---|---|---|
| ProRes 422 HQ | Default 10-bit intermediate, NLE friendly | `-c:v prores_ks -profile:v 3 -pix_fmt yuv422p10le` (on a Mac you may use `prores_videotoolbox -profile:v hq`) |
| ProRes 4444 | Graphics with alpha | `-c:v prores_ks -profile:v 4 -pix_fmt yuva444p10le` |
| DNxHR HQX | Windows/Avid friendly, 10-bit | `-c:v dnxhd -profile:v dnxhr_hqx -pix_fmt yuv422p10le` |
| FFV1 | Mathematically lossless archive | `-c:v ffv1 -level 3 -g 1 -slices 16 -slicecrc 1` (MKV) |
| x264 lossless | Lossless when space is tight, 8-bit only | `-c:v libx264 -qp 0 -preset veryfast` |
| Audio intermediate | Always uncompressed | `-c:a pcm_s24le` (MOV/MKV) |

Short of disk space (machine.md)? Use `libx265 -crf 12 -preset fast -pix_fmt yuv420p10le` as a visually lossless
intermediate, and tell the user.

## 3. Final delivery encode

**Default master: H.264.** It plays everywhere and every platform accepts it.

| Source / target | Codec | Settings |
|---|---|---|
| 8-bit SDR source, SDR delivery (most common) | libx264 | `-c:v libx264 -crf 17 -preset slow -profile:v high -pix_fmt yuv420p` |
| 10-bit source, SDR delivery to social/web | libx264 8-bit, converted once at the end | as above. Add `-x264-params aq-mode=3` against banding in skies and gradients. |
| 10-bit source, keep 10-bit (archive, YouTube, HDR) | libx265 | `-c:v libx265 -crf 18 -preset slow -pix_fmt yuv420p10le -tag:v hvc1` |
| HDR delivery (HLG/PQ) | libx265 10-bit | colour.md for the tags and metadata |
| Size matters a lot and playback is modern (YouTube, archive) | libsvtav1 | `-c:v libsvtav1 -crf 28 -preset 6 -pix_fmt yuv420p10le`. Slow; check platform support first. |
| Hardware encode allowed | see machine.md | Raise the quality setting until the bitrate lands in the source's band |

Quality dial: CRF 17-18 (x264) and 18-20 (x265) look the same as the source to the eye. Lower numbers mean more bits.
Go to CRF 14-16 for grainy, noisy or very detailed footage, or when a platform will re-compress it hard. Never ship
CRF 23 (the default) as a final.

Preset dial: `slow` for finals. `medium` if time is short. `veryfast` / `ultrafast` only for drafts and proxies.
Preset changes speed and file size at the same quality, not the quality you get at a given CRF. So never trade CRF
for speed; trade preset.

Bitrate mode instead of CRF: only when a platform or file-size cap needs it. Then use two-pass:
`-b:v N -pass 1 … -f null -` then `-b:v N -pass 2 …`. Don't set a target below the source's bitrate band without telling
the user.

## 4. Always add

```
-colorspace bt709 -color_primaries bt709 -color_trc bt709 -color_range tv   # SDR; HDR tags in colour.md
-movflags +faststart                                                        # MP4/MOV for web upload
-map 0:v:0 -map 0:a:0? -map_metadata 0                                      # explicit streams; keep metadata
-c:a copy        (when the source audio is AAC/ALAC/PCM and the target accepts it)
-c:a aac -b:a 320k -ar 48000   (when audio must be encoded; once)
```

Keep the source frame rate (`-fps_mode passthrough` for straight re-encodes). Change frame rate only for the target,
and only with a proper filter (`fps=` to drop or duplicate frames; `minterpolate` only on request, since it smears).

Scaling: `scale=W:H:flags=lanczos`. Keep the aspect ratio. Never upscale a final unless asked. To scale with zscale
when converting colour too, use `zscale=w=W:h=H:filter=lanczos`.

## 5. Drafts and proxies

Proxies exist to save time during review, so they may be cheap:
`-vf scale=-2:540 -c:v libx264 -preset veryfast -crf 26` (or the hardware encoder) at 15-30 fps.
Mark every draft as a draft in its file name. The final always re-renders from the originals with full settings,
never from a proxy.

## Quality killers (check before every final)

- [ ] CRF 23 or another library default anywhere in the chain
- [ ] `veryfast` / `ultrafast` preset on a final
- [ ] An extra lossy pass (burning captions or overlays onto an already encoded copy)
- [ ] 10-bit to 8-bit too early, or 4:2:2 to 4:2:0 before the last step
- [ ] Missing colour tags, or a range mismatch
- [ ] Frame rate changed by accident (VFR conformed badly, a renderer at the wrong fps)
- [ ] Audio re-encoded more than once, or at 128k
- [ ] A scale to an odd size, or a stretch (use `-2` for auto even dimensions)
- [ ] A proxy left in the final timeline
