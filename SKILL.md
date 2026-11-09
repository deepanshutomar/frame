---
name: frame
description: FRAME (Footage, Render, Audio, Media, Export), all you need for editing videos. Directive guide for any video edit or render. Read the machine and the source first, ask for style and font, source licensed music/SFX/footage (Openverse first), encode once at source quality, grade log footage only when asked, listen with earshot, then verify. Use whenever video is cut, captioned, animated, converted, graded or exported.
---

# FRAME — Footage · Render · Audio · Media · Export

All you need for editing videos.

> By **Deepanshu Tomar** · IG [@deepanshutomarg](https://instagram.com/deepanshutomarg) & [@dsedaddy](https://instagram.com/dsedaddy)

This skill writes no edits of its own and ships no scripts. It covers the backend calls that agents usually get wrong
without saying so: codec, quality, colour, frame rate, audio, which tool to use, and how to prove the output is as good
as the input. Any editing skill or plain request that touches video follows it.

## The one rule

**The output should look and sound the same as the source, except where the edit changes it on purpose.**
A smaller file at the same resolution is not proof of quality. Bitrate, bit depth, colour and frame rate count as much
as pixel size. If an edit has to lose quality (a size limit, a slow machine, a platform cap), say so in one line before
rendering, not after.

## Order for every job (no step skipped)

| # | Step | Open |
|---|---|---|
| 1 | Read the machine: OS, CPU/GPU, RAM, free disk, ffmpeg build and its encoders, other tools | [references/machine.md](references/machine.md) |
| 2 | Read every source file: codec, bitrate, bit depth, colour tags, HDR/log, frame rate (VFR?), audio | [references/source.md](references/source.md) |
| 3 | Style and font: offer 2-3 directions from the catalog and **ask for the font** (with a sample still) | [references/styles/styles.md](references/styles/styles.md), [references/fonts.md](references/fonts.md) |
| 4 | Pick the tool for each part of the edit | [references/tools.md](references/tools.md) |
| 5 | Assets: music, SFX, B-roll, images from licensed sources (Openverse first for audio), logged in the credits ledger | [references/assets/README.md](references/assets/README.md) |
| 6 | Plan the encode chain: how many times the footage gets encoded (aim for once), intermediates, final settings | [references/encoding.md](references/encoding.md) |
| 7 | Colour: log or HDR handling, grading only under the rules | [references/colour.md](references/colour.md) |
| 8 | Audio: keep it clean, loudness, and listen with earshot when available | [references/audio.md](references/audio.md) |
| 9 | Delivery target: platform specs, master vs upload copy | [references/delivery.md](references/delivery.md) |
| 10 | Draft, then final render, then verify against the source | [references/verify.md](references/verify.md) |

Open a reference only at its step. Steps 1 and 2 are a few commands each and cost almost nothing. Skipping them is
how quality drops without anyone noticing. Steps 3 and 5 apply only when the job involves styling or outside assets;
a plain convert or cut skips them. Ask the step 3 questions in one message, together with any other questions.

The big references are searched, never loaded whole: `styles/editing-catalog.md` with grep, section by section;
`styles/skills.json` with `jq` or Python to find a skill for a job (styles.md shows how).

## Hard rules

1. **Never trust defaults.** FFmpeg, MoviePy, HTML renderers and most libraries fall back to "good enough" settings
   (x264 CRF 23, 8-bit, untagged colour, 128k audio). Set every quality setting yourself.
2. **Encode the footage as few times as possible, ideally once.** Cuts that need no re-encode use stream copy. When
   several passes are unavoidable, work in a near-lossless intermediate and run the lossy delivery encode once, at
   the end.
3. **Graphics go on top of the original, not on a re-encoded copy.** Render overlays with alpha and composite them
   onto the original footage in the final pass.
4. **Keep the source's frame rate, resolution, bit depth and colour space** unless the delivery target needs a change.
   If it does, make the change once, on purpose, with the right filter (tone mapping for HDR to SDR, proper scaling,
   proper frame-rate conversion).
5. **Tag colour on every output.** Untagged files shift colour or gamma in players and on platforms.
6. **Audio is copied when possible.** When it must be encoded, do it once at a high bitrate. Never re-encode audio
   that is already a compressed format just because the video changed.
7. **Drafts are allowed to be cheap; finals are not.** Low-resolution, fast-preset proxies for review are fine. The
   final render always comes from the originals at full settings.
8. **Grade only log footage, only when nobody has graded it, and only with the user's yes.** Footage that is just flat
   or dark is not log footage. See colour.md.
9. **Verify before you hand over.** Compare the output to the source (step 10) and report the numbers in one or two
   lines.
10. **Always ask for the font.** Never pick a typeface silently. Show options on the user's own frame.
11. **No asset without a licence.** Use only outside music, SFX, footage or images whose licence allows commercial
    use and modification (unless the user says otherwise), and log each one in `credits.json` at download time.
    Keep API keys in env vars, never in files.
12. **Ask only when the answer changes the output:** style, font, the delivery platform, HDR vs SDR, whether to
    grade, or whether a size limit allows quality loss. Everything else follows from the machine and the source.

## What to tell the user (short)

Before the final render, one line: tool, codec, quality setting, and any intended loss.
After: output path, codec, resolution, frame rate, bit depth, bitrate vs source, verification result.
Use plain words; explain a term only if they ask.
