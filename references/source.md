# Step 2 — Read every source file

Goal: know exactly what you're starting from, so the output can match it. Do this for every input clip, not one
sample.

## The probe

```
ffprobe -v error -show_entries format=duration,bit_rate,format_name:format_tags \
  -show_entries stream=index,codec_type,codec_name,profile,width,height,pix_fmt,bits_per_raw_sample,\
color_range,color_space,color_transfer,color_primaries,r_frame_rate,avg_frame_rate,bit_rate,\
sample_rate,channels,channel_layout:stream_tags:stream_side_data \
  -of json INPUT
```

If `mediainfo` or `exiftool` is installed, run it too on camera files. Camera log profiles, recording gamma and HDR
formats often show up only there (`mediainfo INPUT`, `exiftool -G -a INPUT | grep -iE 'gamma|log|profile|color|hdr|
dolby|model|make'`). Sony also writes an XML sidecar (`*M01.XML`) next to the clip that names the capture gamma and
gamut.

## Write a source card per clip

```
clip: <name>  <container>  <duration>
video: <codec> <profile>  <w>x<h>  <pix_fmt> (<8/10/12>-bit)  <fps> (CFR/VFR)  ~<N> Mbps
colour: range <tv/pc>  primaries <..>  transfer <..>  matrix <..>  -> <SDR Rec.709 / HDR HLG / HDR PQ / LOG (which) / unknown>
audio: <codec> <rate> Hz <channels>  ~<N> kbps
camera: <make/model if known>
notes: <rotation, edit lists, timecode track, multiple audio tracks, alpha>
```

## How to read the tricky fields

**Bit depth.** `yuv420p` is 8-bit. `yuv420p10le` and `yuv422p10le` are 10-bit. 10-bit sources keep 10-bit through
every intermediate. Drop to 8-bit only for an 8-bit delivery, and do it once, at the end.

**Colour state.** Decide on one of these:
- **SDR Rec.709:** `bt709` tags, or untagged HD consumer footage. This is the normal case.
- **HDR HLG:** transfer `arib-std-b67`, primaries `bt2020`. This is the iPhone default for video, and many phones and
  cameras offer it.
- **HDR PQ / HDR10 / Dolby Vision:** transfer `smpte2084`. Dolby Vision also shows a DOVI configuration record in the
  side data.
- **LOG:** a camera log profile such as Sony S-Log3, DJI D-Log / D-Log M, Apple Log / Apple Log 2, Canon C-Log, Panasonic
  V-Log, Fujifilm F-Log, Nikon N-Log, or Blackmagic film. Log files are often tagged as plain `bt709` or left untagged,
  so the tags alone don't prove it. Confirm with the camera metadata or sidecar, or with the user.
- **Unknown:** say so. Never guess HDR or log from how a frame looks alone.

Visual hint only, never proof: log footage looks grey, low in contrast and low in saturation, and its histogram sits
in the middle. Flat Rec.709 footage can look the same. Treat a hint as a reason to check the metadata or ask, not as a
conclusion.

**Range.** `tv` (limited, 16-235) is normal for video. `pc` (full) is common from screen recorders, some phones and
renders made from images. A range mismatch crushes or lifts the blacks. Carry the range through, or convert it once on
purpose.

**Frame rate.** Compare `r_frame_rate` with `avg_frame_rate`. If they differ, or the source is a phone or screen
recording, assume variable frame rate (VFR). VFR breaks audio sync in editors and frame-accurate cuts. Convert to
constant frame rate (CFR) at the nearest standard rate in the intermediate (`-fps_mode cfr -r 30000/1001` or the
matching rate). For a straight re-encode with no editing, keep it as it is (`-fps_mode passthrough`).

**Bitrate.** This is the quality budget the source carries. Note it. The final should land in the same range for the
same resolution and codec family (verify.md has the bands).

**Rotation, edit lists, extra tracks.** Phone clips carry a rotation flag. Some files carry edit lists, timecode or
data tracks, or several audio tracks. Map streams explicitly (`-map 0:v:0 -map 0:a:0`) so nothing gets dropped or
picked at random.

## Mixed sources

If the clips differ (different cameras, frame rates, log profiles), choose the project's frame rate, resolution and
colour target first, based on the main camera or the delivery target. Then conform every clip to it once, in the
intermediate step. Write the decision down.
