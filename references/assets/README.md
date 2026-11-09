# Assets — where to find music, SFX, footage and images, and how to keep them legal

Open this folder whenever an edit needs anything that isn't the user's own footage: music, sound effects, B-roll,
photos, illustrations or textures.

| File | Open when |
|---|---|
| [facets.md](facets.md) | Always, first: the licence and search facets that decide whether an asset can be used |
| [audio-sources.md](audio-sources.md) | Music or sound effects (Openverse first) |
| [visual-sources.md](visual-sources.md) | B-roll video, photos, illustrations, archival images |
| [project-folder.md](project-folder.md) | Setting up where assets live in the project, plus the credits ledger |

## Order

1. **Check what the user already has:** their own SFX/music library, past projects, brand assets. Their own and
   licensed material comes first.
2. **Define the need in words:** "a soft paper tear, under 1 s", "warm lofi bed, 60-90 s, no vocals",
   "vertical B-roll of a crowded Delhi street". Then set the facets (facets.md) before searching.
3. **Search the APIs in the order the source files give.** Filter by licence in the query itself, not afterwards.
4. **Audition and choose:** look at thumbnails and frames, listen to previews (earshot `rank` / `find` when
   available, audio.md). Pick 2-3 options when the choice is creative and let the user decide.
5. **Download the original or best-quality file** into the project folder (project-folder.md). Never hotlink. Never
   use a preview or thumbnail as the final asset if a full-quality file exists.
6. **Log it in the credits ledger** at download time: source, ID, URL, creator, licence, attribution text.
7. **Hand over the credits** with the final video (CREDITS.md / attribution text for the post description).

## Rules

- **The licence travels with the file.** No ledger entry, no use.
- **API keys:** Pexels, Pixabay, Freesound and Unsplash need free keys. Ask the user once. Keep keys in environment
  variables (`PEXELS_API_KEY`, `PIXABAY_API_KEY`, `FREESOUND_API_KEY`, `UNSPLASH_ACCESS_KEY`) or a git-ignored
  `.env`. Never write a key into a project file, a log, a commit or this repo.
- **Respect rate limits and terms:** cache search results, don't mass-download, and follow each API's attribution and
  hotlinking rules.
- **Network:** if the machine's network blocks an API, say so and ask the user to download the file or allow the
  domain. Don't route around a block.
- **Real people and brands:** stock photos of identifiable people need a model release for commercial or sensitive
  use. Don't use photos of real public figures, or brand logos, as decoration. Never use an asset to imply someone
  said or endorsed something.
- **AI-generated visuals or music** are a separate route (the user's own tools). Label them as generated, and don't
  generate realistic footage of real people.
- **Never** claim an asset is "royalty-free" or "copyright-free" without its licence in the ledger. "Free to download"
  is not a licence.
