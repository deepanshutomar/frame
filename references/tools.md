# Step 3 — Pick the tool

Pick by the job, not by habit. One edit often uses two or three tools. That's fine, as long as the footage itself is
encoded once (encoding.md).

| Job | First choice | Also good | Avoid / note |
|---|---|---|---|
| Trim, cut, join, change container | FFmpeg with stream copy (`-c copy`) | — | Stream-copy cuts snap to keyframes. For frame-accurate cuts, re-encode at master settings (encoding.md) or cut on keyframes. Join with the concat demuxer only when all clips share codec settings. |
| Remove silences, jump cuts | auto-editor | FFmpeg `silencedetect` plus your own cut list | Export a cut list or timeline (auto-editor can write FCPXML / Premiere XML / Resolve) when the user finishes in an NLE |
| Convert, re-encode, scale, change frame rate | FFmpeg | HandBrake (GUI users) | MoviePy and similar libraries: pass explicit codec and quality settings, or they encode at weak defaults |
| Captions (burned in) | Subtitles or an overlay drawn in the final composite pass | ASS subtitles with libass (`subtitles=` filter) for styled text | Burning captions into an already lossy copy adds a generation. Burn them in the final pass. |
| Transcript / SRT | Whisper (whisper.cpp / MLX on Mac, faster-whisper elsewhere) | NVIDIA Parakeet, cloud speech APIs | Check proper nouns and code-mixed words by ear |
| Motion graphics, kinetic text, HTML/CSS/JS animation | HyperFrames or Remotion | Lottie for vector loops | Render graphics as an alpha layer (ProRes 4444, PNG sequence, or VP9 alpha WebM), then composite onto the original in FFmpeg. Check the Remotion licence for company use. |
| 3D | Blender (headless: `blender -b file.blend -a`) | three.js inside HyperFrames/Remotion for light 3D | Render to an EXR/PNG sequence or ProRes 4444, then composite |
| Colour grading | FFmpeg `lut3d` plus filters for LUT-based work | DaVinci Resolve (free) for real grading by a human | Grading is gated: colour.md |
| Audio clean-up, loudness | FFmpeg (`loudnorm`, `afftdn`, `highpass`, `alimiter`) | Audacity / iZotope by a human | Measure first (audio.md) |
| Listening: sync, SFX choice, sound critique | earshot | — | audio.md |
| Hands-on human editing | Final Cut Pro / Premiere / Resolve | — | Hand over an interchange file (FCPXML, XML, EDL) plus the media, not a baked render, when the user wants to keep editing |
| Finding music, SFX, B-roll, images | Openverse API (audio + images), then Pexels / Pixabay / Freesound / Wikimedia | earshot's sound bank, the user's own library | assets/README.md: licence facets first, credits ledger always |
| Font | Ask the user (fonts.md) | Fontsource / Google Fonts (OFL) | Never choose silently |
| Image sequence to video | FFmpeg (`-framerate N -i f_%05d.png`) | — | Set the colour tags and the pixel format yourself |

## Rules

- **Prefer the tool already installed** when it can do the job at full quality. Offer to install a better one in one
  line, but don't install large tools (Blender, Resolve, model weights) without the user's yes.
- **Renderers are not encoders.** HTML and 3D renderers often encode their own MP4 at medium quality. Ask them for
  the highest quality, or better, for frames or ProRes with alpha. Then run the final encode yourself in FFmpeg, where
  you control the settings.
- **One composite pass at the end:** original footage + graphics layer(s) + captions + final audio go in, and one
  delivery file comes out.
- **When a tool fails twice,** stop, say what failed, and switch tools or ask. No third blind retry.
- **Check tool versions** before relying on a flag (`ffmpeg -h encoder=libx265`, `<tool> --help`). Flags change
  between versions.
