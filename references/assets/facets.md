# Facets — what decides whether an asset can be used, and how to search for it

Two kinds of facet: **rights facets** (may we use it, and how) and **search facets** (does it fit the edit). Set both
before searching, and pass them to the API as filters.

## A. Rights facets (check every asset)

| Facet | Values | What it means for a video |
|---|---|---|
| Licence family | CC0, Public Domain Mark | Free for any use, no attribution required (crediting is still good practice) |
| | CC BY | Any use, including commercial; **attribution required** (creator, title, licence, link) |
| | CC BY-SA | Attribution, plus **share-alike**: putting it in a video can arguably put the video under BY-SA. Avoid unless the user accepts that. |
| | CC BY-NC (-SA/-ND) | **Non-commercial only.** Not for monetised channels, sponsored posts, client work or ads. A creator's monetised account counts as commercial. |
| | CC BY-ND / any -ND | **No derivatives.** Cutting, looping, re-timing or overlaying it counts as modifying, so practically unusable in an edit. |
| | Platform licences (Pexels, Pixabay, Unsplash) | Free use, including commercial, without attribution, but with restrictions: don't sell the file unaltered, no trademark or endorsement use, no misleading portrayal of people. Read the current terms. |
| | Unknown / "free download" | Not usable. |
| Commercial use | yes / no | The user's channel, brand deals and client work are commercial. Default: require commercial = yes. |
| Modification | yes / no | Editing needs modification = yes. Default: require it. |
| Attribution | required / not | If required, write the exact attribution text into the ledger now. |
| Releases | model / property release | Needed for identifiable people or private property in commercial or sensitive contexts. Stock sites state it; CC photos often have none. |
| Trademarks and logos | present / absent | Even inside a free photo, a brand logo can't be used to imply endorsement. Crop or blur it, or choose another asset. |
| Content ID risk (music) | registered / not | Some CC or "free" tracks are registered with content-ID systems and get claims on YouTube or Instagram. Prefer CC0 and sources that state there are no claims. Keep the licence proof in the ledger so a claim can be disputed. |
| Editorial-only | yes / no | News or archival items marked editorial can't be used in promos or ads. |

**Openverse filter values:** `license=` (e.g. `cc0,pdm,by`) and `license_type=commercial,modification`.
`license_type=commercial,modification` gives the safe default for edits. Add `license=cc0,pdm` for "no attribution
needed".

**Freesound filter:** `filter=license:"Creative Commons 0"` (also "Attribution"; avoid "Attribution NonCommercial"
for commercial work).

## B. Search facets (fit to the edit)

| Need | Facet | API parameter examples |
|---|---|---|
| Kind of asset | music vs sound effect; photo vs illustration vs vector | Openverse audio `category=music` / `sound_effect`; Openverse images `category=photograph` / `illustration` / `digitized_artwork`; Pixabay `image_type=photo` / `illustration` / `vector`, videos `video_type=film` / `animation` |
| Length | short hit vs bed | Openverse audio `length=` buckets; Freesound `filter=duration:[0 TO 2]`; Pexels videos `min_duration` / `max_duration` |
| Frame shape | vertical for 9:16, wide for 16:9 | Openverse images `aspect_ratio=tall` / `wide` / `square`; Pexels `orientation=portrait` / `landscape` / `square`; Pixabay `orientation=vertical` / `horizontal` |
| Resolution | at least the delivery size | Openverse `size=large`; Pixabay `min_width` / `min_height`; Pexels `size=large`; choose the largest video file in the result |
| Format | lossless audio, transparent PNG, SVG | Openverse `extension=wav,flac` / `png` / `svg`; Freesound `filter=type:wav` |
| Source collection | Freesound vs Jamendo vs Wikimedia | Openverse `source=freesound` / `jamendo` / `wikimedia_audio` (`/v1/audio/stats/` lists the names) |
| Colour / mood | matches the palette | Pixabay `colors=`; otherwise query words |
| Safety | no mature content | Openverse `mature=false` (default); Pixabay `safesearch=true` |
| Working links | skip dead items | Openverse `filter_dead=true` |

Parameter names and allowed values change. Check the API's own docs page once per session before relying on an
exact value. If a filter isn't supported, filter the results yourself and say so.

## C. Decision per asset (write in the ledger)

```
asset: <file>  need: <what it's for>
rights: <licence> · commercial ✓/✗ · modify ✓/✗ · attribution <text or none> · releases <y/n/na> · logos <none/blurred>
fit: <duration/size/orientation/format>  verdict: use / reject (<reason>)
```
