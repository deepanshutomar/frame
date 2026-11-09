# Editing styles — choose, then ask

This folder holds two big references. Don't read either of them whole.

| File | What it is | How to read it |
|---|---|---|
| [editing-catalog.md](editing-catalog.md) | About 77 sections covering effects, techniques, creator frameworks, transitions, sound, colour, typography and genre grammars | Find the section you need with `grep -n "^#\{1,2\} [0-9]" editing-catalog.md`, then read only that section by its line range |
| [repos.md](repos.md) | 427 public video-skill repos (Nov 2026 snapshot) by kind, with licence and security grade | Search it with `grep -i "<keyword>" repos.md` |

## 1. Pick a style for the content (before building anything)

1. Read what the video is: talking head, explainer, promo, story, podcast clip, tutorial, music video. Note the
   platform (short vertical or long horizontal) and the emotional register (urgent, calm, reflective, playful).
2. Pick 2-3 fitting directions from the catalog. Start with these sections:
   - §1 The five dials (pace, typography, motion density, sound, colour). Describe each option as five dial settings.
   - §12 Creator frameworks (retention short-form, explainer, corporate thriller, clean productivity, cinematic travel).
   - §27 Genre grammars (psychological essays, education, vlog, product, comedy, horror, news, fitness).
   - §20 Emotion and intimacy (pauses, holds, restraint). For personal or reflective talking heads, read this before
     any effect section.
   - §75 Effect combinations / recipe library, for ready-made bundles.
   - §33 Decision framework and §33.3 the restraint test: every effect must solve a problem.
3. **Ask the user to choose.** Show each direction as one line of dial settings plus one still or a quick sample
   frame on their own footage when possible. Ask the font question in the same message (see
   [../fonts.md](../fonts.md)).
4. If the user has their own editing skill or a saved house style, that wins. This catalog only fills the gaps.

## 2. Look up a technique

The user names an effect ("text behind me", "paper tear", "speed ramp") → find it in the catalog (§76 has synonyms
and the vocabulary for finding things) → read its execution notes → pick the tool with [../tools.md](../tools.md).
The catalog describes After Effects / Resolve / Premiere ways of working. Translate them to the tools available here
(FFmpeg, HyperFrames/Remotion, Blender), or hand over to the user's NLE.

Technical delivery advice in the catalog (§29 codecs, bitrates) is general. **This skill's encoding.md, colour.md and
delivery.md take priority** wherever they differ.

## 3. Borrowing from other repos

[repos.md](repos.md) is a list of ideas, not things to install.

- **Search by need:** `grep -i "talking-head\|caption\|kinetic" repos.md`, or check the kind headings (Editing, Shorts
  & social, Motion graphics, Explainers…).
- **Licence decides what you may take:**
  - MIT / Apache-2.0 / MIT-0: code may be reused. Keep their licence notice.
  - AGPL-3.0: take ideas only. Copying code would put the user's project under AGPL.
  - CC-BY-SA: share-alike applies.
  - `none` / NOASSERTION: no clear licence. Read for ideas and copy nothing.
- **Security:** a repo is strangers' code that runs on the user's machine. Read a script before running it. Grade
  `caution` means read everything first. Never paste in API keys a repo asks for without the user's yes.
- **Don't install whole skill packs.** Every installed skill costs context in every session, and packs overlap.
  Copy the one useful idea or script (licence allowing) into the user's own skill, with credit.
- **Stars are a snapshot,** not a quality guarantee. Check the last commit date and open issues before relying on one.

Starting points by job (all from repos.md; check them live first):

| Job | Look at |
|---|---|
| Render engine for HTML/GSAP motion | heygen-com/hyperframes (Apache-2.0), remotion-dev/skills |
| Talking-head reels: captions, punch-ins, cutouts | artemnovitckii/claude-editing-skills, adriiita/vertical-video-editing-skill (MIT), naive-kun/naive-video-skill (MIT), kurbaitaev/ghost-editor (MIT), aaksa/facecammaxxing (no clear licence: ideas only) |
| Editing judgment (pacing, cuts, captions, sound) | kmilo93sd/agent-video-editor (MIT, includes CC0 sounds and graphics), edotheDev/claude-video-kit (MIT) |
| Silence and filler removal | louisedesadeleer/cut-video (MIT), brookejlacey/boring-tools (MIT) |
| Long video to shorts | AgriciDaniel/claude-shorts (MIT), ghost1412/shorts-generator (MIT) |
| Style libraries / looks | lemomo-ai/lemo-opuscar (MIT, 66 styles), threeaus/creator-style-kit (MIT), Tejashmakwana/motionmaxxing (Apache-2.0, anti-slop) |
| Explainers, paper collage, maths | Alisa0808/vox-director (MIT), adithya-s-k/manim_skill (MIT), iart-ai/explainer-video-skills (MIT) |
| Motion design craft and critique loops | echris6/motion-video-kit (MIT), iart-ai/motion-design-skills (MIT) |
| Lottie / vector animation | diffusionstudio/lottie (MIT) |
| Talking head to YouTube long-form | hassancs91/claude-youtube-editor (MIT) |
| Premiere / After Effects control | hetpatel-11/Adobe_Premiere_Pro_MCP (MIT), JUNKDOGE-JOE/after-effects-mcp (MIT) |
