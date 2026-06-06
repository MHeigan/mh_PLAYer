# mh_PLAYer v2.12.0

**High-Performance CGI/VFX Image Sequence & Video Player for Windows**  
Martin P. Heigan · [anti-matter-3d.com](https://anti-matter-3d.com/mhplayer)

> **Proprietary software — free to download and use. Some production features require a license.**  
> See [Licensing](#licensing) below. Full terms in `License_Agreement.pdf` (included in the ZIP).

---

## Download

**[⬇ Download mh_PLAYer v2.12.0 (Windows x64)](https://anti-matter-3d.com/mh_player/mh_PLAYer_v2_12_0.zip)**

Or visit [anti-matter-3d.com/mhplayer](https://anti-matter-3d.com/mhplayer) for release notes and licensing. GitHub mirror: [Releases]([https://github.com/MHeigan/mh_player/releases/latest](https://github.com/MHeigan/mh_PLAYer/releases/tag/mh_PLAYer_v2_12_0)).

---

## What is mh_PLAYer?

mh_PLAYer is a professional image sequence and video player built for CGI and VFX production pipelines. It handles multi-layer OpenEXR sequences, video dailies, HDR files, and everything in between — with full colour management, A/B compare, stereo anaglyph compositing, annotation tools, a pipeline CLI, and a three-level frame cache for sustained playback of large sequences.

Version 2.12 adds a complete **EDL / Multi-Clip Timeline**, frame-accurate **Synced Remote Review** across machines, and **Python plugin scripting** — turning the player into a review and conform hub for the whole team.

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
- **Onion-skinning** — overlay adjacent frames for animation timing

**Colour Management**
- sRGB, ACES Filmic, Power Gamma, Linear display modes
- **CDL grade** — slope / offset / power, applied through a fast precomputed LUT
- LUT support — `.cube` and `.3dl`
- **OpenColorIO bundled** — no separate install; auto-loads `$OCIO`
- Auto colour space detection from EXR header on load
- EV (±8 stops) and gamma — spinbox controls, live labels
- **Aspect ratio correction** — 13 presets from 1:1 to 4K Scope; transport bar combo + sidebar; saved per workspace

**Stereo / Anaglyph** *(free)*
- **Two-sequence mode** — load left and right eye sequences independently; any supported format
- **Single stereo EXR mode** — auto-detects left/right layer pairs in multi-camera EXRs (`left.R/G/B + right.R/G/B` and 10 other naming conventions)
- **Colour modes** — Red-Cyan Half-Color · Red-Cyan Greyscale · Amber-Blue · Green-Magenta
- Eye swap and gamma-correct options
- **CLI anaglyph convert** — headless `--anaglyph` flag for batch pipeline use

**Review & QC**
- A/B Wipe compare — draggable divider or independent B sequence *(license)*
- Diff mode — `|A−B| × amplify` (1–32×) full-frame difference *(license)*
- **Playlist** — multi-clip queue with per-clip In/Out trim, drag-reorder, and **M3U / M3U8 import/export**; SSD pre-caching between clips *(license)*
- Annotation tools — pen, line, arrow, rectangle, text + voice notes *(license)*
- HUD overlay — frame number, SMPTE TC (drop-frame-correct at 29.97/59.94), shot name (file / folder / custom)
- **Remote Review (browser streaming)** — built-in HTTP server; open `http://LAN-IP:8765` on any tablet, phone, or second PC; ~15–20 fps on a wired LAN; no software install on the remote device *(free)*
- Waveform / Parade scope — vertical RGB stack with solo buttons
- Pixel inspector — linear float readout, hex sRGB, clipboard copy
- Histogram — live RGB overlay

**EDL / Multi-Clip Timeline** *(Studio Pro)*
- Assemble multiple sequences and clips into a single ordered cut for back-to-back review
- Per-clip trims, **drag-to-reorder**, **edge trim handles**, and a **non-modal editor with a live playhead**
- **Loop playback**, seamless next-clip pre-loading, and grade persistence across cuts
- **CMX 3600 EDL import/export** for round-trips with Premiere, Avid, Resolve, and Final Cut — drop-frame-aware at NTSC rates
- Build a timeline straight from the Playlist (**Send Playlist to Timeline**)
- **Headless render** — `mh_player --convert cut.mhedl review.mp4`

**Synced Remote Review** *(Studio Pro)*
- Frame-accurate playback **synchronisation across multiple mh_PLAYer instances** on a LAN
- **Automatic session discovery** — followers see hosted sessions and join with one click
- Host-authoritative timing with bi-directional transport control, auth token, and reconnect-on-drop
- Distinct from the free browser Remote Review above — this syncs full-quality playback between running players

**Plugins** *(Studio Pro)*
- Load small **Python plugins** that add menu commands and react to playback/export events
- Stable, versioned `mh_player_api` — state queries, playback control, status/log, and event hooks
- Drop a `.py` file in the `plugins/` folder; a bundled `example_plugin.py` shows the structure
- Full API reference in Appendix B of the user manual

**Interface**
- **Quick-access icon toolbar** — collapsible rows of icon buttons for display, HUD, guides, A/B compare, scopes, remote review, plus a second row for Playlist, EDL, Onion-skin, Export, and Sync
- **Sidebar anchors** — toolbar icons scroll the sidebar directly to the relevant section; auto-expands if collapsed
- Collapsible sidebar — Tab or click the ‹ strip for a clean full-canvas view

**Pipeline**
- CLI — viewer / convert (headless) / check (QC) modes
- **Timeline & playlist inputs** — open `.mhedl` and `.m3u8` from the command line; render a timeline headlessly
- **CLI PATH Setup** — Help → CLI PATH Setup… adds the app to user PATH, no admin rights required
- Nuke flipbook integration — `mh_player_nuke.py` + `mh_player_nuke_Quickstart.pdf` included
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

1. Download the ZIP from [anti-matter-3d.com/mhplayer](https://anti-matter-3d.com/mhplayer) or the [Releases](https://github.com/MHeigan/mh_player/releases) page.
2. Extract to any folder — no installer or admin rights required.
3. Run `mh_PLAYer_Win_x64_v2_12_0.exe`.
4. Use **Help → Manage Shortcuts…** to create Desktop and Start Menu shortcuts.
5. Use **Help → CLI PATH Setup…** to add mh_PLAYer to your user PATH for terminal access.

```
mh_PLAYer_Win_x64_v2_12_0.exe       Main application (digitally signed)
ffmpeg\                              Bundled FFmpeg
_internal\                           Application runtime files
plugins\                             Plugin folder (bundled example_plugin.py)
mh_PLAYer_v2_12_User_Manual.pdf      Full user manual
Nuke_Integration\                    Nuke flipbook integration:
                                       mh_player_nuke.py, mh_player_nuke_Quickstart.pdf
License_Agreement.pdf                End User License Agreement
README.txt                           Plain-text quick reference
```

---

## Quick Start

1. Drop an EXR frame, image file, or video onto the window — or use **File → Open Sequence**.
2. Press **Space** to play. Use **J / K / L** for shuttle control.
3. Press **Tab** to collapse the sidebar for a clean full-canvas view.

---

## EDL / Multi-Clip Timeline *(Studio Pro)*

Assemble shots into a single reviewable cut:

1. **EDL → New EDL**, or build one from the Playlist with **Playlist → Send Playlist to Timeline (EDL)…**
2. **EDL → EDL Editor…** opens a non-modal editor — drag clips to reorder, drag a clip edge to trim, watch the cyan playhead track playback live.
3. **EDL → Activate EDL Playback** to run the clips back-to-back; **Loop EDL Playback** to loop.
4. **EDL → Export CMX3600 .edl…** to round-trip with an NLE, or render headlessly:

```bash
mh_player --convert cut.mhedl review.mp4
mh_player --convert cut.mhedl review.mov --display aces --burnin --codec prores
```

---

## Synced Remote Review *(Studio Pro)*

Keep several machines on the same frame, in sync, at full quality:

1. On the host, start a Sync session. Followers on the same LAN see it appear automatically and join with one click (or enter the host address).
2. The host drives transport; playback stays frame-accurate across all participants, with automatic reconnect if a connection drops.

> For lightweight viewing on a tablet or phone, the free **browser Remote Review** (below) is still available and needs no second mh_PLAYer.

---

## Remote Review (browser streaming) *(free)*

Start the server via **Help → Remote Review** or the antenna icon in the toolbar. A dialog shows the URL — open it on any device on the same network:

```
http://192.168.x.x:8765
```

The browser page updates automatically during playback at ~15–20 fps on a wired LAN. No software installation required on the remote device. Works on iPad, phone, laptop, or a second PC with any modern browser.

---

## Plugins *(Studio Pro)*

Drop a Python file in the `plugins/` folder next to the exe. Plugins load on startup and can add menu commands and react to events:

```python
import mh_player_api as mh

def _note():
    mh.log(f"reviewing {mh.get_current_path()} @ {mh.get_current_frame_number()}")
    mh.notify("Review note logged")

mh.register_menu_item("Log Review Note", _note)
mh.on_export_complete(lambda out: mh.log(f"export done: {out}"))
```

See the bundled `example_plugin.py` and Appendix B of the user manual for the full API.

---

## Stereo / Anaglyph *(free)*

**Two-sequence workflow:**
1. Open the left eye sequence via File → Open Sequence.
2. In the sidebar STEREO / ANAGLYPH section, click **Load R Eye…** to load the right eye.
3. Check **Enable Anaglyph**.

**Single stereo EXR workflow:**
1. Click **Load Stereo EXR…** — mh_PLAYer auto-detects the layer pairs and enables anaglyph immediately.

**CLI headless anaglyph convert:**

```bash
# Two separate sequences → anaglyph MP4
mh_player --convert --anaglyph left.####.exr --right-eye right.####.exr anaglyph.mp4

# Single stereo EXR sequence (auto-detects left.R/G/B + right.R/G/B)
mh_player --convert --anaglyph --stereo-exr camera_stereo.####.exr anaglyph.mp4

# ACES display + gamma correction for linear EXRs
mh_player --convert --anaglyph --display aces --anaglyph-gamma \
    left.####.exr --right-eye right.####.exr review.mp4
```

---

## Pipeline CLI

```bash
# Viewer
mh_player beauty.####.exr
mh_player -s 1001 -e 1250 --display aces -n 2 beauty.####.exr
mh_player cut.mhedl --play             # open a saved timeline
mh_player review.m3u8 --play           # open a playlist

# Convert (headless, farm-safe)
mh_player --convert beauty.####.exr  review/beauty.####.png
mh_player --convert --display aces beauty.####.exr  out.mp4
mh_player --convert --ar 2.39 beauty.####.exr review.mp4
mh_player --convert cut.mhedl review.mp4          # render an EDL/timeline

# Check / QC (exit codes: 0=complete, 1=gaps/partial, 2=error)
mh_player --check beauty.####.exr
mh_player --help
```

---

## Nuke Flipbook Integration

```python
# %USERPROFILE%\.nuke\menu.py
import mh_player_nuke
mh_player_nuke.register()
```
```batch
set MH_PLAYER_PATH=C:\App\mh_PLAYer\mh_PLAYer_Win_x64_v2_12_0.exe
```

See `Nuke_Integration/mh_player_nuke_Quickstart.pdf` for full setup including OCIO passthrough and exe discovery.

---

## Verifying Your Download

Every release ships with verification artefacts.

| File | Purpose |
|---|---|
| `SHA256SUMS.txt` | Plain-text SHA-256 hashes for all release files |
| `SHA256SUMS.yaml` | Machine-readable hashes for pipeline scripts |
| `release_manifest.cat` | Signed Windows catalogue — cryptographic proof of file integrity |
| `manifest.yaml` | Full release manifest (version, date, file list) |

**PowerShell:**
```powershell
Get-FileHash mh_PLAYer_Win_x64_v2_12_0.exe -Algorithm SHA256
```

**Command Prompt:**
```cmd
certutil -hashfile mh_PLAYer_Win_x64_v2_12_0.exe SHA256
```

Compare the output against `SHA256SUMS.txt`. The exe is digitally signed and submitted to Microsoft WDSI and VirusTotal before every public release.

---

## System Requirements

| | |
|---|---|
| OS | Windows 10 / 11 (64-bit) |
| RAM | 16 GB min; 32 GB+ recommended for large EXR sequences |
| Storage | SSD recommended for L3 disk cache |
| Display | 1920×1080 min; 4K supported |

**Image formats:** EXR, PNG, TIFF, JPEG, DPX, HDR  
**Video formats:** MP4, MOV, MKV, AVI, WebM, WMV  
**Audio formats:** WAV, MP3, FLAC, AIFF

---

## Licensing

mh_PLAYer is **free to download and use**. The core viewer, all display and colour management tools, stereo anaglyph compositing, aspect ratio correction, browser remote review, the QC toolkit, and the pipeline CLI work without a license — with no time limit and no reduced quality.

Paid licenses add the production workflow features. **Individual** and **Studio** licenses unlock the same feature set and differ only in machine binding (single machine vs organisation-wide); **Studio Pro** adds the advanced collaboration and automation features on top.

| Capability | Free | Individual | Studio | Studio Pro |
|---|:--:|:--:|:--:|:--:|
| Core viewer · colour management · aspect ratio · stereo anaglyph | ✓ | ✓ | ✓ | ✓ |
| Browser remote review · QC toolkit (scopes, inspector, histogram) · CLI | ✓ | ✓ | ✓ | ✓ |
| Annotations · A/B Compare · Playlist · Audio · Export · Slate & Burn-in | — | ✓ | ✓ | ✓ |
| EDL / Multi-Clip Timeline · Synced Remote Review · Plugin Scripting | — | — | — | ✓ |
| Licensed use across machines | — | Single (MAC-bound) | Organisation-wide | Organisation-wide |

All paid licenses are **perpetual** — no subscription, no renewal fees. A **Trial** unlocks every feature, including Studio Pro, for 10 days on any machine.

| License | Use across machines | Feature set | Expiry |
|---|---|---|---|
| Free (unlicensed) | Any machine | Core viewer, colour, AR, stereo, browser remote review, QC, CLI | None |
| Individual | Single machine (MAC-bound) | Free + production features | None — perpetual |
| Studio | Organisation-wide (floating) | Free + production features | None — perpetual |
| Studio Pro | Organisation-wide (floating) | Studio + EDL / Timeline, Synced Remote Review, Plugins | None — perpetual |
| Trial | Any machine | All features (including Studio Pro) | 10 days |

> Video file audio plays without a license — only imported audio for image sequences requires one.

**10-day full-feature trial available on request — no payment required.**

Purchase and trial requests: [anti-matter-3d.com/mhplayer](https://anti-matter-3d.com/mhplayer)  
Or contact me at: [anti-matter-3d.com/contact](https://anti-matter-3d.com/contact)

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
[anti-matter-3d.com/mhplayer](https://anti-matter-3d.com/mhplayer) · [anti-matter-3d.com/contact](https://anti-matter-3d.com/contact)

*Copyright © 2026 Martin P. Heigan. All Rights Reserved.*
