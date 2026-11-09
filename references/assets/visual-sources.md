# B-roll, photos, illustrations — sources, in order

Set the facets first (facets.md): orientation and resolution to match delivery, commercial + modification allowed.
Openverse has images but **no video**; video comes from Pexels, Pixabay, Wikimedia Commons, NASA or archives.

## Video (B-roll)

| # | Source | Access | Licence notes |
|---|---|---|---|
| 1 | **Pexels** | Free key, header `Authorization: $PEXELS_API_KEY`. Video search: `GET https://api.pexels.com/v1/videos/search?query=<q>&orientation=portrait&size=large&per_page=15` (the older `https://api.pexels.com/videos/search` path is being retired). Default limit about 200 requests/hour, 20k/month. | Pexels License: free, commercial OK, no attribution required (appreciated). Each result lists several `video_files`; pick the highest resolution that matches the delivery. |
| 2 | **Pixabay** | Free key as `key=` param. `GET https://pixabay.com/api/videos/?key=$PIXABAY_API_KEY&q=<q>&video_type=film&per_page=20`. About 100 requests/minute; cache results 24 h. | Pixabay Content License. **No permanent hotlinking**: download to the project. Results give sizes `large` / `medium` / `small` / `tiny`. |
| 3 | **Wikimedia Commons** | No key. MediaWiki API: `https://commons.wikimedia.org/w/api.php?action=query&list=search&srnamespace=6&srsearch=<q> filetype:video&format=json`, then `prop=imageinfo&iiprop=url|extmetadata` for licence and author. | Per-file licence (PD, CC0, CC BY, CC BY-SA…). Read `extmetadata.LicenseShortName` and `Artist`. |
| 4 | **NASA Image and Video Library** | No key. `https://images-api.nasa.gov/search?q=<q>&media_type=video` | Generally not copyrighted. Don't imply NASA endorsement or use its logos; some items are third-party, so check the description. |
| 5 | **Internet Archive** | `https://archive.org/advancedsearch.php?q=<q>+AND+mediatype:movies&fl[]=identifier,licenseurl,title&output=json` | Mixed. Use only items with a `licenseurl` of PD or CC, or clearly public-domain films. "Old" does not mean public domain. |

## Photos, illustrations, textures

| # | Source | Access | Licence notes |
|---|---|---|---|
| 1 | **Openverse images** | No key. `GET https://api.openverse.org/v1/images/?q=<q>&license_type=commercial,modification&aspect_ratio=tall&size=large` | CC and PD from Flickr, Wikimedia, museums and more. Copy the `attribution` field into the ledger. |
| 2 | **Pexels photos** | Same key. `GET https://api.pexels.com/v1/search?query=<q>&orientation=portrait&size=large` | Pexels License |
| 3 | **Pixabay images** | Same key. `GET https://pixabay.com/api/?key=...&q=<q>&image_type=photo&orientation=vertical&min_width=1080` (also `illustration` / `vector`) | Pixabay Content License; no hotlinking |
| 4 | **Unsplash** | Free key, header `Authorization: Client-ID $UNSPLASH_ACCESS_KEY`. `GET https://api.unsplash.com/search/photos?query=<q>&orientation=portrait`. **Must** call the photo's `links.download_location` when downloading (API terms). Demo limit about 50 requests/hour. | Unsplash License: free, commercial OK. API terms ask for credit to the photographer and Unsplash. |
| 5 | **Wikimedia Commons** | As above, with `filetype:bitmap` or `filetype:drawing` | Per file |
| 6 | **Museums, open access (CC0)** | The Met (`collectionapi.metmuseum.org`, no key); Smithsonian Open Access (api.data.gov key); Rijksmuseum (key) | Use only items flagged public domain / CC0 |
| 7 | **NASA images** | `images-api.nasa.gov/search?q=<q>&media_type=image` | As above |

## Using visuals well

- **Match the delivery:** vertical sources for 9:16, or crop a wide source around its subject, which needs about 2×
  resolution. Never upscale a small image to fill the frame. Choose another one.
- **Match the look:** colour, grain and frame rate. Conform the frame rate once (encoding.md). Grade B-roll toward the
  main footage, never the other way round.
- **Check every frame** of a clip you use (pull a contact sheet). Watch for visible logos, faces, text or watermarks.
- **Evidence and claims:** a stock image is an illustration, not proof. When a video makes a factual claim, use
  sourced material (a news page screenshot, a document) and label any reconstruction (catalog §26.6).
- **Download originals** to `assets/` with the source and ID in the file name (project-folder.md), then add them to
  the ledger.
