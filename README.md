# mh_PLAYer v2.8.21

**High-Performance CGI/VFX Image Sequence & Video Player for Windows**  
Martin P. Heigan · [anti-matter-3d.com](https://anti-matter-3d.com/mhplayer)

> **Proprietary software — free to download and use. Some production features require a license.**  
> See [Licensing](#licensing) below. Full terms in `License_Agreement.pdf` (included in the ZIP).

---

## Download

**[Download mh_PLAYer v2.8.21 (Windows x64)](https://github.com/MHeigan/mh_player/releases/tag/mh_PLAYer_v2_8_21)**

Or visit [anti-matter-3d.com/mhplayer](https://anti-matter-3d.com/mhplayer) for release notes and licensing.

---

## What is mh_PLAYer?

mh_PLAYer is a professional image sequence and video player built for CGI and VFX production pipelines. It handles multi-layer OpenEXR sequences, video dailies, HDR files, and everything in between — with full colour management, A/B compare, stereo anaglyph compositing, annotation tools, a pipeline CLI, and a three-level frame cache for sustained playback of large sequences.

No Python. No dependencies. No installer. Extract the ZIP and run.

---

## Features

**Playback & Formats**
- **OpenEXR multi-layer** — full AOV tree, all channel depths, float16/float32
- **HDR / Radiance RGBE** — `.hdr` support with Reinhard tone-map
- **Video** — MP4, MOV, MKV, AVI, WebM, WMV via bundled FFmpeg
- **R/G/B/A channel isolation** — on all source types including video
- **Frame stride** — play every 2nd–5th frame at real-time speed
- **Three-level cache** — L1/L2 RAM + L3 SSD for large-sequence playback

**Colour Management**
- sRGB, ACES Filmic, Power Gamma, Linear display modes
- LUT support — `.cube` and `.3dl`
- **OpenColorIO bundled** — no separate install; auto-loads `$OCIO`
- Auto colour space detection from EXR header on load
- EV (±8 stops) and gamma — spinbox controls, live labels

**Stereo / Anaglyph**
- **Two-sequence mode** — load left and right eye sequences independently; any supported format
- **Single stereo EXR mode** — auto-detects left/right layer pairs in multi-camera EXRs (`left.R/G/B + right.R/G/B` and 10 other naming conventions)
- **Colour modes** — Red-Cyan Half-Color · Red-Cyan Greyscale · Amber-Blue · Green-Magenta
- Eye swap and gamma-correct options
- **CLI anaglyph convert** — headless `--anaglyph` flag for batch pipeline use; supports both two-sequence and stereo-EXR input modes

**Review & QC**
- A/B Wipe compare — draggable divider or independent B sequence *(license)*
- Diff mode — `|A−B| × amplify` (1–32×) full-frame difference *(license)*
- Annotation tools — pen, line, arrow, rectangle, text + voice notes *(license)*
- HUD overlay — frame number, SMPTE TC, shot name (file / folder / custom)
- Aspect ratio correction — 13 presets from 1:1 to 4K Scope, saved per workspace
- Waveform / Parade scope — vertical RGB stack with solo buttons
- Pixel inspector — linear float readout, hex sRGB, clipboard copy
- Histogram — live RGB overlay

**Interface**
- **Quick-access icon toolbar** — 14 icon buttons across display, HUD, guides, A/B compare, scopes, pixel inspector, remote review, and more — collapsible with persistent state
- **Sidebar anchors** — toolbar icons scroll the sidebar directly to the relevant section; auto-expands if collapsed
- Collapsible sidebar — Tab or click the ‹ strip for a clean full-canvas view

**Pipeline**
- CLI — viewer / convert (headless) / check (QC) modes
- **CLI PATH Setup** — Help → CLI PATH Setup… adds the app to user PATH, no admin rights required
- Nuke flipbook integration — `mh_player_nuke.py` included; `$OCIO` passed through automatically
- Remote review — built-in HTTP server, any browser on the network; antenna icon in toolbar
- Workspace save/load — complete session state in a `.mhplay` file
- Sequence gap detection — warns on open with exact missing frame ranges
- SMPTE timecode — configurable start, click frame counter to toggle

**Export** *(license required)*
- Frame export — PNG, TIFF, EXR; current / range / full sequence
- Video export — H.264, H.265, ProRes via bundled FFmpeg
- Slate & burn-in — shot, scene, take, artist, company, logo, watermark
- Quick Save — current frame to PNG/TIFF/JPEG, one keystroke

---

## Installation

1. Download the ZIP from the [Releases](https://github.com/MHeigan/mh_player/releases) page.
2. Extract to any folder — no installer or admin rights required.
3. Run `mh_PLAYer_Win_x64_v2_8_21.exe`.
4. Use **Help → Manage Shortcuts…** to create Desktop and Start Menu shortcuts.
5. Use **Help → CLI PATH Setup…** to add mh_PLAYer to your user PATH for terminal access.

```
mh_PLAYer_Win_x64_v2_8_21.exe   Main application (digitally signed)
ffmpeg\                           Bundled FFmpeg
_internal\                        Application runtime files
mh_PLAYer_v2_8_User_Manual.pdf   Full user manual
mh_player_nuke.py                 Nuke flipbook integration script
License_Agreement.pdf             End User License Agreement
README.txt                        Plain-text quick reference
```

---

## Quick Start

1. Drop an EXR frame, image file, or video onto the window — or use **File → Open Sequence**.
2. Press **Space** to play. Use **J / K / L** for shuttle control.
3. Press **Tab** to collapse the sidebar for a clean full-canvas view.

---

## Stereo / Anaglyph

mh_PLAYer composites stereo image pairs into anaglyph output for review with colour-filtered glasses.

**Two-sequence workflow:**
1. Open the left eye sequence via File → Open Sequence.
2. In the sidebar STEREO / ANAGLYPH section, click **Load R Eye…** to load the right eye.
3. Check **Enable Anaglyph**.

**Single stereo EXR workflow:**
1. Click **Load Stereo EXR…** — mh_PLAYer auto-detects the layer pairs and enables anaglyph immediately.

**CLI headless anaglyph convert:**

```bash
# Two separate sequences → anaglyph MP4
mh_PLAYer --convert --anaglyph left.####.exr --right-eye right.####.exr anaglyph.mp4

# Single stereo EXR sequence → anaglyph MP4 (auto-detects left.R/G/B + right.R/G/B)
mh_PLAYer --convert --anaglyph --stereo-exr camera_stereo.####.exr anaglyph.mp4

# ACES display + gamma correction for linear EXRs
mh_PLAYer --convert --anaglyph --display aces --anaglyph-gamma \
    left.####.exr --right-eye right.####.exr review.mp4

# Greyscale mode — least ghosting, no colour
mh_PLAYer --convert --anaglyph --anaglyph-mode rc_grey \
    left.####.exr --right-eye right.####.exr out.mp4

# Anamorphic source — bake 2.39:1 display geometry into output
mh_PLAYer --convert --ar 2.39 beauty.####.exr review.mp4

# Combine AR correction with anaglyph
mh_PLAYer --convert --anaglyph --ar 2.39 left.####.exr --right-eye right.####.exr out.mp4
```

---

## Frame Stride

Click the **×N** label in the transport bar to cycle 1→2→3→4→5→1 — play every Nth frame at real-time speed. Indispensable for a rapid first pass on a long overnight render.

| Stride | Effect |
|---|---|
| ×1 | Every frame — normal playback |
| ×2 | Every 2nd frame — half decode cost |
| ×4 | 75% fewer frames decoded |
| ×5 | Rapid overview of long sequences |

Also available in CLI: `-n 2`.

---

## A/B Compare — Wipe and Diff

Press **[** to freeze the current frame as B and enter wipe mode.  
**View → Compare: Diff Mode** for full-frame `|A−B|` difference display.  
**Amplify** slider 1–32× makes single-pixel differences visible against solid black.  
*(Requires license)*

---

## Pipeline CLI

```bash
# Viewer
mh_PLAYer beauty.####.exr
mh_PLAYer -s 1001 -e 1250 --display aces -n 2 beauty.####.exr

# Convert (headless, farm-safe)
mh_PLAYer --convert beauty.####.exr  review/beauty.####.png
mh_PLAYer --convert --display aces beauty.####.exr  out.mp4

# Anaglyph convert
mh_PLAYer --convert --anaglyph left.####.exr --right-eye right.####.exr anaglyph.mp4
mh_PLAYer --convert --anaglyph --stereo-exr camera_stereo.####.exr anaglyph.mp4

# Aspect ratio correction (bake display geometry into output)
mh_PLAYer --convert --ar 2.39 beauty.####.exr review.mp4

# Check / QC (exit codes: 0=complete, 1=gaps, 2=error)
mh_PLAYer --check beauty.####.exr
mh_PLAYer --help
```

`--ar RATIO` bakes the correct display geometry into the output pixels (e.g. `--ar 2.39` for Scope, `--ar 1.85` for Flat, `--ar 1.778` for 16:9). Works with all output formats and with `--anaglyph`.

---

## Nuke Flipbook Integration

```python
# %USERPROFILE%\.nuke\menu.py
import mh_player_nuke
mh_player_nuke.register()
```
```batch
set MH_PLAYER_PATH=C:\App\mh_PLAYer\mh_PLAYer_Win_x64_v2_8_21.exe
```

---

## Verifying Your Download

Every release ships with verification artefacts. Before running a build you received
from a third party, confirm the checksums match the published release.

### Files included in every release

| File | Purpose |
|---|---|
| `SHA256SUMS.txt` | Plain-text SHA-256 hashes for all release files |
| `SHA256SUMS.yaml` | Machine-readable hashes for pipeline scripts |
| `release_manifest.cat` | Signed Windows catalogue — cryptographic proof of file integrity |
| `manifest.yaml` | Full release manifest (version, date, file list) |
| `release_info.txt` | Human-readable release summary |
| `release_info.yaml` | Machine-readable release metadata |

The distribution is also wrapped in a **signed self-extracting archive (SFX)** — verify
the signature on the outer archive before extraction.

### Verify the SHA-256 hash

**PowerShell:**
```powershell
Get-FileHash mh_PLAYer_Win_x64_v2_8_21.exe -Algorithm SHA256
```

**Command Prompt:**
```cmd
certutil -hashfile mh_PLAYer_Win_x64_v2_8_21.exe SHA256
```

**Python (pipeline-friendly):**
```python
import hashlib, pathlib
exe = pathlib.Path("mh_PLAYer_Win_x64_v2_8_21.exe")
print(hashlib.sha256(exe.read_bytes()).hexdigest())
```

Compare the output against `SHA256SUMS.txt`. If they match, the file is intact and
unmodified since signing.

### Verify the signed catalogue

```cmd
signtool verify /pa /v release_manifest.cat
```

Or right-click the `.cat` file in Explorer → Properties → Digital Signatures.

> The exe is also independently code-signed. The distribution is submitted to
> Microsoft WDSI and VirusTotal before every public release.

---

## System Requirements

| | |
|---|---|
| OS | Windows 10 / 11 (64-bit) |
| RAM | 8 GB min; 16 GB+ recommended for large EXR sequences |
| Storage | SSD recommended for L3 disk cache |
| Display | 1920×1080 min; 4K supported |

**Image formats:** EXR, PNG, TIFF, JPEG, DPX, HDR  
**Video formats:** MP4, MOV, MKV, AVI, WebM, WMV  
**Audio formats:** WAV, MP3, FLAC, AIFF

---

## Licensing

mh_PLAYer is **free to download and use**. The core viewer, all display and colour management tools, the QC toolkit, and the pipeline CLI work without a license — with no time limit and no reduced quality.

A license unlocks the production workflow features:

| Feature | Description |
|---|---|
| Annotations | Per-frame drawing (pen, line, arrow, rectangle, text) + voice notes |
| A/B Compare | Wipe and `\|A−B\|` × amplify diff compare modes |
| Playlist | Multi-clip queue with per-clip In/Out and SSD pre-caching |
| Audio | Import external WAV/MP3/FLAC/AIFF for image sequence sync |
| Export Frames | PNG / TIFF / EXR export with burn-in and annotation bake |
| Export Video | H.264 / H.265 / ProRes encode via bundled FFmpeg |
| Slate & Burn-in | Title card + frame number, timecode, and watermark bake |

> Stereo anaglyph (viewer + CLI convert) and aspect ratio correction are available without a license.  
> Video file audio plays without a license — only imported audio for image sequences requires one.

| License | Machine binding | Expiry |
|---|---|---|
| Individual | Single machine (MAC-bound) | None — perpetual |
| Studio | Any machine in organisation | None — perpetual |
| Trial | Any machine | 10 days — all features |

**10-day full-feature trial available on request — no payment required.**

Purchase and trial requests: [anti-matter-3d.com/mhplayer](https://anti-matter-3d.com/mhplayer)  
Or use the [contact form](https://anti-matter-3d.com/contact/)

> This software is proprietary. Use is governed by the End User License Agreement included in the distribution (`License_Agreement.pdf`). By downloading or using mh_PLAYer you agree to its terms.

---

## Keyboard Shortcuts

| Key | Action |
|---|---|
| Space | Play / Pause |
| J / K / L | Shuttle reverse / stop / forward |
| ← / → | Step one frame |
| Home / End | First / Last frame |
| R / G / B / A | Channel isolation |
| C | Full colour (RGB) |
| V | Invert display |
| P | Cycle proxy (Full → ½ → ¼) |
| F / 1 | Fit to window / 1:1 pixel |
| Tab | Toggle sidebar |
| N | Annotation mode *(license)* |
| [ | A/B compare *(license)* |
| Ctrl+G | Go to frame |
| Ctrl+S | Save workspace |
| Ctrl+E | Export frames *(license)* |
| Ctrl+Shift+E | Export video *(license)* |
| Ctrl+F | Fullscreen |
| Shift+R | Reset EV and gamma |

---

Digitally signed · Signed release catalogue (`.cat`) + signed SFX · WDSI + VirusTotal submitted before every release  
[anti-matter-3d.com/mhplayer](https://anti-matter-3d.com/mhplayer) · [Contact form](https://anti-matter-3d.com/contact/)

*Copyright © 2026 Martin P. Heigan. All Rights Reserved.*
