# Step 7 — Delivery target

Platforms re-compress every upload. The better the file you give them, the better what viewers see. Make a **master**
first, then an **upload copy** only if the platform or a size limit needs something different.

> Platform specs change. These are working values as of **November 2026**. Check the platform's own help page when a
> number matters, and update this file.

| Target | Size / aspect | Codec | Frame rate | Video bitrate guide | Audio | Notes |
|---|---|---|---|---|---|---|
| Instagram Reels, TikTok, YouTube Shorts | 1080×1920, 9:16 | H.264 High, MP4 | same as the source (30 typical; 24-60 accepted) | CRF 16-18, which usually lands around 10-20 Mbps; don't go below ~8 Mbps | AAC 48 kHz, 256-320k | Keep text and faces inside the safe zone (about the top 220 px and bottom 400 px get covered by app UI). Meta publishes no official encode spec; these are working values. |
| Instagram feed | 1080×1350 (4:5) or 1080×1080 | H.264, MP4 | as source | as above | AAC 48 kHz | |
| YouTube long-form | 1920×1080 / 2560×1440 / 3840×2160 | H.264 High (SDR); HEVC 10-bit or AV1 accepted | the same as recorded (YouTube says so) | official minimums at 1080p: 8 Mbps @30fps, 12 Mbps @60fps; higher is fine (CRF 17 is fine) | AAC-LC 48 kHz, 384k stereo | MP4, `+faststart`, 4:2:0, BT.709 tags. Uploading 1440p or 4K gets the better VP9/AV1 playback stream even for 1080p viewers. |
| YouTube HDR | as above | HEVC/AV1 10-bit, HLG or PQ with metadata | as recorded | 1080p about 10-15 Mbps; 4K 44-56 Mbps (30fps) | as above | colour.md, section A |
| WhatsApp / messaging | ≤ the app's size limit | H.264, MP4 | 30 | sized to fit (two-pass bitrate) | AAC 128-192k | This is where quality loss is acceptable. Tell the user. |
| Editor hand-over (FCP, Premiere, Resolve) | source size | ProRes 422 HQ, or the original media plus FCPXML/XML/EDL | source | — | PCM 24-bit | Never hand an NLE a heavily compressed MP4 to keep editing |
| Archive master | source size | ProRes 422 HQ, or x265 10-bit CRF 14-16, or FFV1 | source | — | PCM or AAC 320k | |

## Rules

- **Ask the target only if it's unknown and it changes the file:** aspect ratio, HDR or SDR, a size cap. A vertical
  1080×1920 source going to "Instagram" needs no question.
- **Never upscale to hit a spec.** If the source is 720p, deliver 720p and say why.
- **Aspect changes (16:9 → 9:16)** are an edit decision (crop around the subject, or blur-fill). Show one still before
  rendering.
- **Size caps:** compute the bitrate from the cap (`bitrate ≈ cap_in_bits / duration`, minus the audio, minus about 3%
  for the container). Use two-pass encoding, and tell the user the quality cost.
- **Name outputs clearly:** `<name>_master_prores.mov`, `<name>_reels_1080x1920_h264.mp4`, `<name>_draft_540p.mp4`.
