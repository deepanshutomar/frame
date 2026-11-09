# Fonts — always ask

**Rule: never pick the font for a video silently.** Typeface is a brand decision. Ask every time, unless the user
has a saved house font for this kind of video. If they do, still confirm it in one line ("Using <font> as usual?").

## How to ask (one message, with the style question)

1. Shortlist 3-4 options that suit the chosen style ([styles/styles.md](styles/styles.md)) and pass the checks below.
   Mix roles if the style needs it: one display face for hero words, one clean face for captions.
2. Render a **specimen still**: the user's real frame, with the real caption line in each font at the real size and
   position. A font name alone means little; a sample on their own face decides it fast.
3. Ask: "Which font? A / B / C / D, or name your own." Accept a font name, a file the user provides, or "same as last
   time".

## Checks before a font goes on the shortlist

| Check | How |
|---|---|
| Script and language coverage | Every character in the script renders: Latin diacritics, Devanagari or other scripts if used, ₹ and other symbols, emoji handling. Test with `fc-list ':lang=hi' family` (Linux/macOS with fontconfig) or render the full caption text once and look for empty boxes (tofu). |
| Weights and styles available | The style needs e.g. Regular + Bold + Black. Check the family has them; don't fake bold. |
| Readability at size | Captions at the real pixel size on a phone frame; check thin strokes and counters at 1080 px width. |
| Licence | OFL / Apache (Google Fonts, Fontsource) are safe for video, including commercial use. System and paid fonts: desktop licences usually allow burned-in video text, but check the EULA for broadcast or commercial clauses when unsure. Don't redistribute paid font files inside a project or repo. |
| Variable font support | If animating weight or width (catalog §25.7), confirm the renderer supports variable axes. |

## Where fonts come from

| Source | How | Licence |
|---|---|---|
| Already installed | `fc-list : family style` (Linux/macOS with fontconfig); macOS also has Font Book (`/Library/Fonts`, `~/Library/Fonts`); Windows: `C:\Windows\Fonts` | Varies; check |
| Fontsource API (no key) | `https://api.fontsource.org/v1/fonts` lists families with subsets and weights; filter by subset (e.g. `?subsets=devanagari`). Files are also on npm as `@fontsource/<family>` | Mostly OFL/Apache, listed per font |
| Google Fonts | Download from fonts.google.com or the `google/fonts` GitHub repo. The Developer API needs a key | OFL / Apache / UFL per family |
| User-supplied | A file they give you | Theirs to check; keep it out of public repos |

## Using the chosen font

- HTML renderers (HyperFrames, Remotion): load the font file locally with `@font-face` (no CDN at render time). Wait
  for `document.fonts.ready` before capturing frames, or the first frames render in a fallback font.
- FFmpeg captions: `subtitles=file.ass:fontsdir=./fonts`, or `drawtext=fontfile=...`. Point at the file directly.
- Write the choice into the project notes (family, weights, sizes, colours) so later passes and future videos stay
  consistent.
