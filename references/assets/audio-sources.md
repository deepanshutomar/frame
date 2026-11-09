# Music and sound effects — sources, in order

Set the facets first (facets.md). Default filters: commercial + modification allowed.

## 1. The user's own library and licensed packs

Check first: folders the user names, their SFX library, music they've licensed. earshot `find` / `rank` can search a
local library by description (audio.md).

## 2. Openverse — first stop for both SFX and music

One search across Freesound, Jamendo, Wikimedia Commons audio and more, with licence data on every result.

- **Endpoint:** `GET https://api.openverse.org/v1/audio/?q=<words>` (images: `/v1/images/`).
- **No key needed** for normal use. Anonymous use is rate-limited and caps page size and depth. For more, register
  once (`POST /v1/auth_tokens/register/` with name, description and email, then `POST /v1/auth_tokens/token/` with
  client credentials). Send `Authorization: Bearer <token>`. Store the credentials as env vars; they can't be shown
  again.
- **Useful filters:** `license_type=commercial,modification`, `license=cc0,pdm,by`, `category=sound_effect` or
  `music`, `length=`, `extension=wav,flac,mp3`, `source=freesound|jamendo|wikimedia_audio`, `filter_dead=true`,
  `page_size=`.
- **What comes back:** `title`, `creator`, `license`, `license_version`, `license_url`, `foreign_landing_url` (the
  source page), `url` (the file), `duration` (ms), `attribution` (ready-made text: copy it into the ledger),
  `waveform` / `thumbnail`.
- **Example:**
  ```
  curl -s "https://api.openverse.org/v1/audio/?q=paper%20tear&category=sound_effect&license_type=commercial,modification&page_size=10"
  ```
- **Caution:** much Openverse *music* comes from Jamendo, and many Jamendo tracks are **NC** (non-commercial). The
  `license_type=commercial` filter matters. Keep `foreign_landing_url` in the ledger; the landing page is the proof.
- Download the file at `url` (or the original from the source page if better quality), then check it with ffprobe.

## 3. Freesound — deepest SFX library (free key)

- Apply for a key at freesound.org/apiv2/apply.
- **Search:**
  `GET https://freesound.org/apiv2/search/text/?query=<words>&filter=license:"Creative Commons 0" duration:[0 TO 3]&fields=id,name,username,license,duration,previews,url&token=$FREESOUND_API_KEY`
- **Previews** (mp3/ogg, no OAuth) are fine for auditioning. **Original files** need OAuth2
  (`/apiv2/sounds/<id>/download/`), or the user downloads them from the sound's page. A high-quality preview is
  acceptable only for short hits buried in a mix, and only if the user agrees.
- The licence is per sound (CC0, Attribution, Attribution NonCommercial). Attribution format: "<sound name> by
  <username> (freesound.org/s/<id>/), licensed under <licence>".

## 4. earshot's code-made bank (no licence questions)

252 effects generated in code: `earshot sfx list`, then `earshot sfx render ID -o x.wav --apex T`. Good for whooshes,
pops, clicks, risers and hits, with nothing to attribute. See audio.md.

## 5. Other music routes (no public API; the user downloads)

| Source | Notes |
|---|---|
| YouTube Audio Library | Free for YouTube use; some tracks need attribution; it's in YouTube Studio |
| Pixabay Music / SFX | Pixabay Content License; the site only (the Pixabay API covers images and video, not music) |
| Free Music Archive, ccMixter, Incompetech | Licences vary per track (CC BY, BY-NC…); read each one |
| Platform libraries (Instagram, TikTok in-app sounds) | Licensed only inside that app's editor; never download and re-upload |

## Never

- Rip music from YouTube, Spotify, Instagram or films, or any "NCS"-style compilation without its licence page.
- Treat "no copyright music" in a title as a licence.
- Use NC-licensed music on monetised or sponsored videos.
