# Step 1 — Read the machine

Goal: know what this machine can encode and how hard you can push it, before choosing anything. Run the checks that
fit the OS, write a short machine profile in your notes, and reuse it for the rest of the session.

## Checks

| What | macOS | Linux | Windows (PowerShell) |
|---|---|---|---|
| OS / chip | `sw_vers; uname -m; sysctl -n machdep.cpu.brand_string` | `uname -a; lscpu \| head -20` | `Get-ComputerInfo OsName,CsProcessors` |
| RAM | `sysctl -n hw.memsize` | `free -h` | `(Get-CimInstance Win32_ComputerSystem).TotalPhysicalMemory` |
| GPU | `system_profiler SPDisplaysDataType` | `lspci \| grep -iE 'vga\|3d'; nvidia-smi -L` | `Get-CimInstance Win32_VideoController` |
| Free disk | `df -h .` | `df -h .` | `Get-PSDrive -PSProvider FileSystem` |
| CPU cores | `sysctl -n hw.ncpu` | `nproc` | `$env:NUMBER_OF_PROCESSORS` |

All platforms:
- `ffmpeg -hide_banner -version` gives the build and its enabled libraries (`--enable-libx265`, `--enable-libsvtav1`,
  `--enable-libzimg`, `--enable-libvmaf` and so on).
- `ffmpeg -hide_banner -encoders` lists what can actually encode here. Look for `libx264`, `libx265`, `libsvtav1`,
  `prores_ks`, `dnxhd`, and the hardware encoders below.
- `ffmpeg -hide_banner -hwaccels` lists the hardware decoders.
- `ffmpeg -hide_banner -filters | grep -E 'zscale|tonemap|lut3d|libvmaf|ssim|loudnorm'` lists the filters later
  steps need.
- Other tools: `which ffprobe mediainfo exiftool auto-editor whisper earshot node npx blender python3` (on Windows,
  use `where`).

If ffmpeg is missing or too old (lacking an encoder or filter you need), say so and offer to install it: `brew install
ffmpeg` (macOS), the distro package or a static build (Linux), `winget install Gyan.FFmpeg` (Windows). Don't work
around a missing encoder by silently picking a worse one.

## Hardware encoders, by platform

| Platform | Encoders (ffmpeg names) | Quality control |
|---|---|---|
| Apple Silicon / Intel Mac | `h264_videotoolbox`, `hevc_videotoolbox`, `prores_videotoolbox` | `-q:v 1-100` (constant quality on Apple Silicon, higher = better; about 65-75 for masters) or `-b:v` |
| NVIDIA | `h264_nvenc`, `hevc_nvenc`, `av1_nvenc` (RTX 40 and newer) | `-rc vbr -cq 16-21 -preset p6/p7 -tune hq` |
| Intel iGPU/Arc | `h264_qsv`, `hevc_qsv`, `av1_qsv` | `-global_quality 18-23` |
| AMD | `h264_amf`, `hevc_amf`, `av1_amf` | `-rc cqp -qp_i/-qp_p/-qp_b 18-22` or `-quality quality` |
| Linux generic | `*_vaapi` | `-qp` or `-b:v`; needs `-vaapi_device` and `hwupload` |

When to use them:
- **Drafts, proxies, previews, long batch jobs:** hardware first. They're fast and fine for review.
- **Final masters:** software (`libx264` / `libx265` with CRF) by default. Software encoders give better quality per
  bit. Use hardware for a final only when software would be too slow for the deadline, or the user asks. Then raise
  the quality setting and check the bitrate against the source (verify.md).
- **ProRes on a Mac:** `prores_videotoolbox` is fast and good. Elsewhere use `prores_ks`.

## Machine tiers (decide load, not quality)

Quality settings for the final never change with the machine. Only speed, parallel work and proxy use change.

| Tier | Rule of thumb | What changes |
|---|---|---|
| Light | ≤8 GB RAM, or a fanless laptop, or no hardware encoder | One render at a time. Use proxies for every review pass. Don't run heavy model tools (earshot, Whisper large, SAM) while rendering. Cap threads if the machine gets unresponsive (`-threads` about cores−1). Never open the browser or the NLE during a render. |
| Mid | 16 GB, or a decent GPU | Proxies for review, one heavy job plus light jobs side by side |
| Heavy | ≥24 GB with a strong GPU, or a workstation | Parallel draft renders, full-resolution previews are fine, models can stay loaded |

Disk: a ProRes 422 HQ intermediate is roughly 1 GB per minute at 1080p30 and about 4 GB per minute at 4K. Check free
space before choosing an intermediate. If space is tight, use a lossless or visually lossless HEVC intermediate
instead, and say so.

## Machine profile (write this once per session)

```
machine: <OS> <chip>, <RAM> GB, GPU <name>, tier <light/mid/heavy>, free disk <N> GB
ffmpeg: <version>; software encoders: <list>; hardware: <list>; filters: zscale <y/n>, tonemap <y/n>, libvmaf <y/n>
tools: <list>
plan: drafts on <encoder>, finals on <encoder>, parallel <y/n>
```
