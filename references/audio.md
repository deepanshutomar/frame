# Step 6 — Audio

Picture quality is wasted if the sound is thin, clipped or out of sync. Audio follows the same "once, at the end"
rule as video.

## Handling

- Keep the source audio untouched until the final mix. Copy it (`-c:a copy`) if the edit doesn't change it.
- Edit or mix in PCM (`pcm_s24le`, 48 kHz). Encode to AAC once, at the end: `-c:a aac -b:a 320k -ar 48000` (stereo).
  For mono voice, 192k is plenty.
- Keep the sample rate at 48 kHz for video. Convert 44.1 kHz music once, with `aresample=48000:resampler=soxr` if the
  build has soxr.
- Map the right track (`-map 0:a:0`). Some cameras record a second, quieter safety track.
- After any cut or speed change, check sync at the start, middle and end (verify.md).

Finding music or sound effects: see [assets/audio-sources.md](assets/audio-sources.md) (Openverse first, licence
facets, credits ledger).

## Loudness (measure, then set)

Measure: `ffmpeg -i IN -af loudnorm=print_format=summary -f null -` or `ebur128=peak=true`.

| Target | Integrated loudness | True peak |
|---|---|---|
| Social short-form (Reels, Shorts, TikTok) | about −14 LUFS (−16 to −13 is fine) | ≤ −1 dBTP |
| YouTube long-form | about −14 LUFS | ≤ −1 dBTP |
| Podcast / voice | −16 LUFS (stereo) | ≤ −1 dBTP |
| Broadcast | follow the broadcaster's spec (EBU R128: −23 LUFS) | as specified |

Use two-pass `loudnorm`: measure, then apply with `measured_I/TP/LRA/thresh` and `linear=true`, so it doesn't pump.
Light clean-up only when it's needed and audible: `highpass=f=80` for rumble, `afftdn` for steady hiss (gentle),
`alimiter` as a safety net. Voice is the priority. Keep music 15-20 dB under speech while someone is talking.

## earshot — the agent's ears (on by default when the machine allows)

[earshot](https://github.com/Leonxlnx/earshot) (MIT, local, CPU) turns sound into evidence an agent can read: a
second-by-second listening timeline, whether each sound lands on its cut, a scorecard with timestamped fixes, search
through a sound library by description, and a bank of 252 code-made sound effects with no licence questions.

**When it's on:** use earshot by default on any edit with music, sound effects or cuts that need to land on sound,
when either:
- it's already installed (`earshot doctor` passes), or
- the user approves installing it. It needs uv, ffmpeg, PyTorch (CPU) and about 1.1 GB of models. Ask once with that
  size.

**Machine limits:** model calls take about 20-40 s each to load. On a light machine (machine.md), run earshot
before or after renders, never during one. For more than a few calls, use its MCP server so the models stay loaded.

**Install (with a yes):**
```
uv tool install "earshot[mcp] @ git+https://github.com/Leonxlnx/earshot"
earshot doctor --fetch                          # downloads the models (~1.1 GB)
claude mcp add earshot --scope user -- earshot mcp   # optional: keep the models loaded for Claude Code
```

**Use it at these points:**

| Moment | Command | What it gives you |
|---|---|---|
| Before planning | `earshot timeline IN --video IN` | events, sections, loudness, beat grid: where the energy and pauses are |
| Choosing sound effects or music | `earshot rank "a soft wooden knock" files…` / `earshot find long.wav "…"` | the best file or the best moment inside one |
| Needing an effect | `earshot sfx list`, then `earshot sfx render ID -o x.wav --apex T` | a licence-free effect aimed at a frame (no models needed) |
| Checking a draft | `earshot timeline DRAFT --video DRAFT` / `earshot sheet DRAFT -o sheet.png` | sync of each sound to its cut, plus an annotated picture |
| Final sound check | `earshot critique FINAL --video FINAL` | a scorecard with timestamped fixes |
| Loudness only | `earshot measure IN` | LUFS, true peak, LRA (no models needed) |

Commands and flags change between versions. Run `earshot --help` or `earshot <command> --help` once and follow that.

**How to use the results:** earshot's verdicts are evidence, not orders. Fix what it flags when you agree (a hit
landing 80 ms late is real). If it disagrees with the user's ear, the user wins.

Without earshot (not installed, no yes, or the machine is too small), measure loudness with ffmpeg and check sync by
pulling short clips around the cuts for the user to hear. Say in one line that earshot would do this check
automatically.
