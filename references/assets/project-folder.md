# Project folder and credits ledger

Use the user's own project layout if they have one. Otherwise create this structure once per project, next to the
footage or where the user says:

```
<project>/
├── source/              original footage + original audio (never modified)
├── proxies/             draft-quality copies (safe to delete)
├── assets/
│   ├── music/           <source>_<id>_<short-name>.<ext>   e.g. openverse_eab8a6e2_paper-swoosh.wav
│   ├── sfx/
│   ├── broll/           e.g. pexels_123456_delhi-street_2160x3840.mp4
│   ├── images/
│   ├── fonts/           font files used (keep paid fonts out of any public repo)
│   ├── luts/
│   └── generated/       anything made by AI or code (label it as generated)
├── graphics/            renders with alpha (ProRes 4444 / PNG sequences)
├── work/                intermediates (ProRes etc.)
├── exports/
│   ├── drafts/          <name>_draft_v01_540p.mp4
│   └── final/           <name>_final_v01_reels_1080x1920_h264.mp4
├── notes.md             machine profile, source cards, chosen style, font, settings, decisions
├── credits.json         the ledger (below)
└── CREDITS.md           human-readable credits / post-description text, made from the ledger
```

Rules:
- `source/` is read-only. Every change produces a new file elsewhere.
- One file name tells you source, ID and content. Never `final_final2.mp4`; use version numbers (`_v01`, `_v02`).
- Keep `.env` (API keys) outside the project or git-ignored.
- On a light machine (machine.md), put `proxies/` and `work/` on the disk with the most free space, and clean them
  up after the final passes verification.

## credits.json (the ledger)

One entry per asset, written **at download time**:

```json
[
  {
    "file": "assets/sfx/openverse_eab8a6e2_paper-swoosh.wav",
    "used_for": "transition at 0:07",
    "source": "Openverse → Freesound",
    "source_id": "eab8a6e2-0ac8-4615-9e0d-ebde30523783",
    "landing_url": "https://freesound.org/s/351256/",
    "creator": "<creator>",
    "license": "CC0 1.0",
    "license_url": "https://creativecommons.org/publicdomain/zero/1.0/",
    "commercial": true,
    "modification": true,
    "attribution_required": false,
    "attribution_text": "\"<title>\" by <creator>, CC0 1.0",
    "releases": "n/a",
    "downloaded": "2026-11-09"
  }
]
```

At hand-over:
- Write `CREDITS.md` with every attribution that's required, ready to paste into the post or video description.
  Include the optional ones as a courtesy.
- Flag any asset whose licence was unclear and was left out.
- Remove unused assets from the ledger and the folder, or mark them `"used_for": "unused"`.
