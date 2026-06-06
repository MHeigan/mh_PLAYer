# mh_PLAYer — Changelog
**Martin P. Heigan · anti-matter-3d.com**

---

## v2.12.0 — 2026
**Release  —  v2.11 cycle promoted to a clean minor version**

The feature work accumulated across the v2.11.13–v2.11.37 cycle is promoted to
v2.12.0 for a clean public release. Beyond that consolidation and the
documentation refresh, this release makes the False Colour exposure display
fully functional and clears a set of latent issues caught in the final
pre-release test pass (see Fixed, below).

- `VERSION` → `2.12.0`.
- Help → User Manual… and the row-2 manual icon now open `mh_PLAYer_v2_12_User_Manual.pdf` from the distribution root (via `_open_pdf`).
- New v2.12 User Manual: adds §25 EDL / Multi-Clip Timeline, §26 Plugins, Appendix B — Plugin API, the Studio Pro tier in §2, and CLI Appendix A.12 (`.mhedl` / `.m3u8` inputs + headless timeline render).
- Build set: `mh_PLAYer_v2_12_0.py` + avloose / pyinstaller bats + version_info.
- **False Colour (exposure heat map)** is now a working display transform — sidebar radio, View → False Colour, and Ctrl+Shift+F, plus a headless `--display falsecolor` mode. Green = 18% middle grey, blue/purple = under, yellow/red = over, magenta/white = clipping. Manual §6.7.

### Highlights since v2.11.12
Drop-frame timecode · LAN session auto-discovery · the full EDL / Multi-Clip
Timeline subsystem (loop, preload, grade persistence, drag-reorder, trim
handles, non-modal editor + live playhead, CMX 3600 round-trip, headless
render) · M3U/M3U8 playlist interchange · playlist reorder/trim and
Send-Playlist-to-Timeline · plugin API expansion · CLI EDL/playlist open · False Colour exposure map.

### Fixed (final pre-release pass)
Latent issues found while testing the consolidated build — all in code paths not exercised earlier in the v2.11 cycle:

- **False Colour** did nothing: the transform was never wired into the EXR / raster / CLI display paths, and the Ctrl+Shift+F / View-menu toggle called an undefined `_set_dmode`. Now fully wired; the also-undefined `_set_gamma` was restored in the same pass, which additionally repaired Input-Buffer state restore.
- **Video Export** would not open — `VideoExportDialog._build` referenced an undefined `_sep` separator helper.
- **Channel-isolated export** (R / G / B / A) crashed: `_channel_to_greyscale` was never defined; the call sites now use the existing `_extract_channel`.
- **Rubber-band zoom**, **region / ROI sampling**, and the **timeline bookmark markers** crashed — `QRect` and `QPolygon` were not imported (only the `F` float variants were), and a stray `@property` on `_commit_zoom_region` broke the zoom-region commit.
- **Bookmark previous / next** crashed: `_go_to_frame_idx` was undefined; navigation now uses the standard `_frame_idx` + `_after_step` jump.
- **Audio persisted across loads**: opening a new source (e.g. an EXR sequence after an MP4 with audio) left the previous clip's audio track and waveform loaded. A new `_reset_audio_state()` now clears embedded + imported audio, hides the waveform, and discards any in-flight async extraction at every source load.
- **Plugin Scripting is now correctly enforced as a Studio Pro feature.** The licence gate previously admitted any valid tier — `plugins` was unintentionally covered by the `FEATURES=ALL` shorthand. It now lives in `PRO_FEATURES`, so Individual / Studio licences no longer unlock it; Studio Pro keys must enumerate it (`FEATURES=ALL,remote_review_sync,edl_timeline,plugins`). This matches the manual, website, and invoices, which already list Plugin Scripting as Studio Pro.

---

## v2.11.37 — 2026
**Feature  —  Headless EDL render (`--convert cut.mhedl out.mp4`)**

A saved timeline can be rendered with no GUI. mh_PLAYer concatenates the clips
in order, applies each trim, and runs the unified frame list through the
standard convert pipeline, so `--display`, `--ev`, `--gamma`, `--burnin`,
`--codec`, and AR all apply to the cut.

- New module-level `_cli_convert_edl`, `_cli_extract_clip_frames` (video clips → temp PNGs via cv2), `_cli_render_pairs_to_images`.
- Image-sequence source clips read directly; video clips frame-extracted, so mixed-source timelines render. Licence-gated (`edl`). Missing clips warned + skipped → partial-success exit code 1.

---

## v2.11.36 — 2026
**API + CLI  —  EDL/playlist API queries, `.mhedl`/`.m3u8` viewer inputs, drop-frame burn-in**

- Plugin API gains `get_playlist()`, `get_edl()`, `is_edl_active()`; `API_VERSION` → (1, 1).
- CLI viewer mode opens `.mhedl` (load + activate, licence-gated) and `.m3u8` / `.m3u` (import); `_playlist_import` refactored to share `_playlist_load_m3u(path)`.
- CLI `--convert` burn-in timecode is now drop-frame-correct at 29.97 / 59.94, sharing the `EditDecisionList` SMPTE math.

---

## v2.11.35 — 2026
**Feature  —  Send Playlist to Timeline (EDL)**

Playlist → Send Playlist to Timeline (EDL)… builds an EDL from the current
playlist, carrying order and per-shot trims; untrimmed shots are probed for
length. Guards a dirty EDL and opens the non-modal editor. Studio Pro.

---

## v2.11.34 — 2026
**Feature  —  Playlist reorder + per-shot trim**

PlaylistPanel right-click menu (Move Up/Down, Set In/Out to current frame,
Clear Trim, Remove); identity-based reorder (fixes a latent duplicate-path
bug in `_on_rows_moved`); trim badge `[in–out]` in labels.

---

## v2.11.33 — 2026
**Feature  —  Non-modal EDL editor + live playhead**

The EDL Editor now `show()`s as a single non-modal instance and stays open
during playback. A cyan playhead (`PLAYHEAD_COLOR #3FC1E0`) tracks the current
position on the track view, pushed from `_update_frame_display`.

---

## v2.11.32 — 2026
**Feature  —  EDL drag-to-reorder + edge-trim handles**

`EDLTrackView` gains drag-to-reorder (amber drop-line) and left/right edge
trim handles (resize cursor, live in≤out clamp), via new `reorder_requested`
/ `trim_requested` signals.

---

## v2.11.31 — 2026
**Fix  —  Licence-message consistency**

The Plugins-menu "Buy / Manage Licence" path called a non-existent helper and
fell back to a hardcoded string; it now routes to the canonical
`_show_license_info()` (Help → License Information).

---

## v2.11.30 — 2026
**Feature  —  EDL playback: loop, next-clip preload, grade persistence**

- Loop EDL Playback wraps last clip → first.
- Next-clip preload warms the upcoming source into the OS cache on a background thread ~12 frames before the cut.
- EV / gamma / CDL grade persist across cuts rather than resetting per clip.

---

## v2.11.29 — 2026
**Feature  —  LAN session auto-discovery (stdlib UDP beacon)**

Synced Remote Review hosts broadcast a small JSON beacon on UDP 8768
(`SyncDiscoveryBeacon`); the Join dialog shows discovered sessions live
(`SyncDiscoveryListener`). Pure stdlib — consistent with the stdlib Sync
transport, no zeroconf/native dependency.

---

## v2.11.28 — 2026
**Feature  —  Exact drop-frame timecode (29.97 / 59.94)**

`EditDecisionList` gains true SMPTE drop-frame math (`_df_params`,
`_tc_to_frames`, `_frames_to_tc`). DF only at 29.97→30/2 and 59.94→60/4;
integer 30/60 and 23.976 stay non-drop. CMX import/export wires the FCM flag
and `;` separator. Verified against a full 0–220000 frame bijection.

---

## v2.11.27 — 2026
**Feature  —  M3U / M3U8 playlist import/export**

VLC-compatible playlist interchange. `#EXTINF` titles honoured; per-clip trim
preserved in a custom `#MHPLAYER-TRIM:<in>,<out>` comment that other players
ignore.

---

## v2.11.26 — 2026
**UI  —  Row-2 quick-access icon toolbar**

Five row-2 IconButtons (playlist, EDL, onion, export, sync menu) with five new
geometric glyphs in `IconButton._draw_icon`.

---

## v2.11.25 — 2026
**Fix  —  L3 video cache stores display-ready frames**

L3 disk cache for video now stores display-ready frames (matching L2), fixing
a transform mismatch on cache hits.

---

## v2.11.24 — 2026
**Feature  —  Bi-directional Sync (host-authoritative clock)**

Synced Remote Review becomes bi-directional with a host-authoritative clock,
so any participant can drive transport while the host remains timing master.

---

## v2.11.23 — 2026
**Change  —  Single plugin directory**

Plugins load from one location only: `<exe-dir>/plugins/` (created on first
run; the bundled `example_plugin.py` lives there).

---

## v2.11.22 — 2026
**Change  —  Registration mailto lists all four tiers**

The Register / Activate mailto template now lists Individual, Studio, Studio
Pro, and Trial.

---

## v2.11.21 — 2026
**Licensing  —  Unified feature-locked / unlicensed message**

`LicenseManager.locked_message_html()` + `require()` unify every feature gate
and licence dialog onto one consistent message.

---

## v2.11.20 — 2026
**Fix  —  Plugin menu items now respond; richer example plugin**

A Qt `triggered(bool)` argument was clobbering the plugin callback; menu items
registered by plugins now fire correctly. Example plugin expanded.

---

## v2.11.19 — 2026
**Feature  —  Sync Review v1.1: auth token + reconnect-on-drop**

Synced Remote Review adds a shared auth token and automatic reconnect after a
dropped connection. Studio Pro, stdlib-only transport.

---

## v2.11.18 — 2026
**Perf  —  L2 backward-window for short loops; frozen-import bootstrap**

L2 cache keeps a backward window so short loops replay from RAM; a frozen-build
import bootstrap shortens cold start.

---

## v2.11.17 — 2026
**Perf  —  CDL via precomputed per-channel LUT; example plugin bundled**

CDL slope/offset/power is applied through a precomputed 256-entry per-channel
LUT (~4–5× faster per frame, with a skip-when-identity fast path). The example
plugin is bundled in `plugins/`.

---

## v2.11.16 — 2026
**Build  —  avloose Nuitka pipeline + `--include-package=av`**

New avloose Nuitka onedir build (the release pipeline) plus a PyInstaller
interim build. Adds `--include-package=av` so PyAV is bundled and `HAS_AV` is
true in frozen builds.

---

## v2.11.15 — 2026
**Perf  —  Early waveform paint**

Audio waveform peaks are computed on the worker and delivered/painted early,
improving perceived audio-load responsiveness.

---

## v2.11.14 — 2026
**Feature  —  PyAV audio extraction (faster embedded-audio load)**

Embedded-audio extraction adds a PyAV (direct libav) fast path as Attempt 2 in
`_audio_extract_worker`, guarded by `HAS_AV`, with the ffmpeg path retained as
fallback.

---

## v2.11.13 — 2026
**Feature  —  Play-start warm gate**

`_set_playing` becomes a thin orchestrator that holds playback start until the
first frames are warm (image-seq L2 poll + video decoder pre-roll), eliminating
the cold-start stutter. A pending warm gate never fires into a newly opened
source.

---

## v2.11.12 — 2026
**Hotfix  —  Nuitka build (real fix — listcomp inside `with` inside `finally`)**

v2.11.11's worker refactor was looking in the wrong place. Nuitka's
own optimization warnings in this build pinpointed the exact location:

```
Problem at 'mh_PLAYer_v2_11_11.py:5862' with Try(...)
Problem at 'mh_PLAYer_v2_11_11.py:5859' with FunctionDef _client_recv_loop
Problem at 'mh_PLAYer_v2_11_11.py:5748' with ClassDef SyncReviewServer
```

### The actual trigger

`SyncReviewServer._client_recv_loop` (added v2.11.1 for Synced Remote
Review) had this structure:

```python
def _client_recv_loop(self, sock, addr_str):
    buf = b""
    try:
        while self._running:
            chunk = sock.recv(4096)
            ...
    except Exception:
        pass
    finally:
        try: sock.close()
        except Exception: pass
        with self._clients_lock:
            self._clients = [(s, a) for s, a in self._clients   ← TOXIC
                             if s is not sock]
```

The combination is:
- A **list comprehension** —
- Inside a **`with` statement** —
- Inside the **`finally` block** of a try-finally —
- Inside a **class method**

Nuitka's tree-cloner transforms try-finally internally and clones the
finalbody for each exit path. When the cloner descends into the
`with` and tries to clone the listcomp, it re-allocates the same
internal temp variable name (`listcomp_1__.0_clone`) and hits the
assertion in `allocateTempVariable`.

The v2.11.11 worker-refactor was a real Nuitka-safety improvement
but it was treating the wrong root cause. **Reading Nuitka's actual
warnings** would have pinpointed this in minutes — that's the
process lesson.

### Fix

Both listcomps in `SyncReviewServer` rewritten as explicit for-loops:

```python
finally:
    try: sock.close()
    except Exception: pass
    with self._clients_lock:
        kept = []
        for pair in self._clients:
            if pair[0] is not sock:
                kept.append(pair)
        self._clients = kept
```

Same semantics — remove the entry whose first element is `sock` from
`self._clients`. Just no listcomp.

Also converted the listcomp in `_broadcast` (line 5916) as a
precaution — same class, similar `with` + iteration pattern. Not
necessarily Nuitka-toxic on its own (not inside try-finally), but
defensive conversion in case its proximity to the toxic pattern was
contributing.

### After v2.11.12 the SyncReviewServer class contains ZERO comprehensions

Verified by AST scan. Other comprehensions elsewhere in the file
inside `with` statements (`EditDecisionList.import_cmx3600` at line
822, `DiskCache.invalidate_sequence` at line 1662) have been in the
codebase across multiple successful builds — those are NOT triggers.

### Diagnostic note for future sessions

**When Nuitka build fails, READ THE WARNINGS BEFORE THE TRACEBACK.**
Nuitka's optimization warnings include the exact AST node and line
number of the offending pattern. The traceback only shows the cloner
recursion — the warnings show the code. I ignored the warnings on
v2.11.11 and refactored the wrong code (closure-in-method). The fix
above is what should have shipped first.

**Nuitka-toxic Python patterns to avoid in this codebase:**
1. List/set/dict/generator comprehension inside `with` inside `finally`
2. Closure-defined-inside-method containing try-except blocks (v2.11.11 lesson — still good to avoid)

When in doubt, use explicit `for` loops. They build everywhere.

### Carried forward from v2.11.10/v2.11.11 unchanged

- Loop seam fix (`AudioEngine.restart_at_frame()` — no PortAudio
  stream teardown on loop wrap)
- MP4 audio ffmpeg pipe (no temp WAV — 2-3× faster)
- Worker is a module-level function (v2.11.11 — also Nuitka-safer)

The Nuitka warnings I see in this build also have unrelated
"Nuitka-Metadata:WARNING: Error, failure to detect name of
distribution" messages for numpy / openexr / pillow / pyinstaller-
hooks-contrib / pynvml. Those are environment-side dist-info
parsing warnings, not blockers — they don't stop the build.

### Validation

- `ast.parse` ✓
- `py_compile.compile(doraise=True)` ✓
- AST audit: SyncReviewServer contains 0 comprehensions ✓
- 23,838 lines (+20 from v2.11.11)
- bat CRLF / ASCII verified
- host_version string in sync hello bumped to "2.11.12"

### What to expect

`build_mh_PLAYer_v2_11_12.bat` should complete through all 6 steps
cleanly. If it still fails with `listcomp_n__.0_clone` for a
different `n`, run the same scan I did and we'll have the next
target.

---

## v2.11.11 — 2026
**Hotfix  —  Nuitka build failure (closure-in-method tree clone bug)**

Martin reported v2.11.10 source runs fine but Nuitka 1-click build
crashes with:

```
AssertionError: listcomp_1__.0_clone
File ".../nuitka/nodes/NodeBases.py", line 642, in allocateTempVariable
    assert full_name not in self.temp_variables, full_name
```

### Root cause

Nuitka's tree cloner ran into a duplicate temp-variable allocation
while expanding a try-except block. The recursion in the traceback
showed the failure happens during `makeTryFinallyStatement →
getFinal → makeClone` — Nuitka transforms try-except into try-finally
internally, then clones the finally subtree for each path. If the
tried section contains an Outline node (list comprehension is one),
the clone process re-allocates the same internal name and the
assertion fires.

The triggering pattern in v2.11.10 was new: the `_worker` closure
defined **inside** `PlayerApp._extract_video_audio` (a class method)
containing multiple try-except blocks. The combination —
class-method → nested-def → try-except → optimizable expression —
hits the cloner edge case.

### Fix

Lifted `_worker` out of the method to module level. New
`_audio_extract_worker(loader, video_path)` lives near
`_AudioLoadSignals`. Takes the loader and path as parameters instead
of capturing them via closure. Same code logic, identical behavior;
just no longer a closure-inside-a-method.

`PlayerApp._extract_video_audio` now spawns the thread with:

```python
threading.Thread(
    target=_audio_extract_worker,
    args=(loader, video_path),
    daemon=True,
    name=f"mh_audio_extract_{gen}",
).start()
```

Cleaner pattern anyway — testable in isolation, no implicit closure
state.

### Validation

- `ast.parse` ✓
- `py_compile.compile(doraise=True)` ✓
- AST audit for the toxic pattern (try-block containing nested def
  with list comprehension) re-run after refactor:
  - **Before (v2.11.10)**: 1 hit in the new `_extract_video_audio`
    closure (the one I introduced)
  - **After (v2.11.11)**: only the pre-existing `_cli_burnin._tc`
    pattern remains — that one's been in the codebase across
    multiple successful builds and is not the trigger
- Cross-thread signal test re-run: module-level worker delivers
  `failed` signal correctly via Qt's queued connection ✓
- 23,818 lines (+11 from v2.11.10 net — added module function,
  removed closure body, replaced spawn call)
- bat CRLF / ASCII verified

### Why this slipped past my v2.11.10 validation

I tested AST cleanliness and runtime behavior (the source runs
correctly), but I didn't compile-test with Nuitka. Nuitka's
optimization passes hit code patterns that valid Python tolerates.
The closure-inside-method pattern is perfectly legal Python and
runs fine; it's specifically Nuitka's tree-cloner that chokes on it
in certain combinations.

**Lesson:** when introducing closures inside class methods in this
codebase, default to module-level functions or static methods
instead. The closure pattern is sometimes elegant in source but
risks Nuitka edge cases — and module-level functions are easier to
test in isolation anyway.

### What this release contains (carryover from v2.11.10)

The actual feature changes from v2.11.10 are unchanged:
- **Loop seam fix**: `AudioEngine.restart_at_frame()` — no PortAudio
  stream teardown on loop wrap, so the 1-second freeze at end-of-
  sequence is gone
- **MP4 audio: ffmpeg pipe**: PCM streamed via stdout pipe, no temp
  WAV file, no disk round-trip — typically 2-3× faster
- Both fixes from v2.11.10 carry through; this release is the
  same functionality, just refactored so Nuitka can compile it

### Companion files unchanged

`mh_license_generator_v1_1_3.py` is unchanged from v2.11.10 — it
built fine and works for Martin already.

---

## v2.11.10 — 2026
**Performance  —  seamless loop · MP4 audio pipe (no temp WAV)**

Two real performance fixes following Martin's testing of v2.11.9 with
a 60-frame looping PNG sequence + manual audio.

### Fix 1 — Loop seam (1-second freeze at end of loop)

**Symptom:** "At the end of the sequence, the clip freezes for at
least a second before it loops from the beginning again."

**Root cause:** `_play_tick`'s loop-wrap branch did
`self._audio.stop()` followed by `self._audio.seek_to_frame(nxt)` and
`self._audio.play()`. The `stop()` call tears down the PortAudio
stream: `stream.stop()` waits for the current buffer to drain (~20-50ms),
then `stream.close()` releases the device handle, then `play()`
opens a brand-new `OutputStream(...)` and `.start()`s it (~150-300ms
of PortAudio init on Windows). **Total ~200-400ms of blocking I/O
on the GUI thread inside `_play_tick`.** Plus PortAudio's worker may
hold extra buffers in flight. That's the visible freeze.

**Fix:** New `AudioEngine.restart_at_frame(idx)` method. Since the
PortAudio callback simply reads from `self._pos`, there's no need to
tear down the stream at all. The method just:

1. Calls `seek_to_frame(idx)` (updates `_pos`)
2. Sets `_playing = True` (re-engages the callback if it auto-paused
   at end-of-data — the callback flips this off when it runs out of
   samples, which happens right before a loop)

The next callback iteration (10-20ms later) picks up at the new
position. No stream rebuild, no PortAudio init, no GUI freeze.

`_play_tick` now calls `self._audio.restart_at_frame(nxt)` instead
of the stop/seek/play triplet. **One line in `_play_tick`; ~30 lines
of new method.** Loop wrap should now be seamless — the rotating-
globe-360-degree test case Martin described.

### Fix 2 — MP4 audio: pipe PCM from ffmpeg, no temp WAV

**Symptom:** "If anything the audio clip now takes longer to load (a
few seconds)" on MP4 sources.

**Root cause:** When `soundfile.read()` fails on MP4 (libsndfile on
Windows usually lacks MP4/AAC support), the fallback ffmpeg path did:

```
1. ffmpeg -i in.mp4 ... -acodec pcm_s16le out.wav    (disk write)
2. soundfile.read(out.wav, dtype='float32')           (disk read)
3. tmp_wav.unlink()                                   (disk delete)
```

Two disk round-trips for data that only needs to live in memory.
Plus WAV header parsing overhead on the read side.

**Fix:** Stream raw PCM directly from ffmpeg's stdout into a numpy
buffer. The ffmpeg invocation becomes:

```
ffmpeg -i in.mp4 -vn -f s16le -acodec pcm_s16le \
       -ar 48000 -ac 2 -loglevel error pipe:1
```

`-f s16le` selects raw little-endian s16 PCM (no container format
headers). `pipe:1` writes to stdout. The Python side does:

```python
proc = subprocess.Popen([...], stdout=subprocess.PIPE)
raw, err = proc.communicate(timeout=30)
samples = np.frombuffer(raw, dtype=np.int16)
n_frames = samples.size // target_ch
data = samples[:n_frames * target_ch].reshape(n_frames, target_ch)
data = data.astype(np.float32) / 32768.0   # matches sf.read shape exactly
loader.loaded.emit(data, target_sr)
```

**Same total decode work** (ffmpeg still has to decode AAC → PCM —
that's the answer to Martin's "can't it pass-through?" question:
sounddevice needs PCM samples, AAC frames don't play directly), but
**no temp file, no WAV header round-trip, no disk I/O**. Typical
2-3× speedup for the same clip on the same hardware.

**Stderr capture** for error messages: if ffmpeg fails, the captured
stderr is truncated to 200 chars and shown in the audio status
panel — much more diagnostic than the old "Audio extraction failed."

**Output shape compatibility:** the (n_frames, n_channels) shape and
float32 dtype match `soundfile.read(..., dtype='float32', always_2d=True)`
exactly, so the downstream `_on_audio_loaded` path needs no changes.

### Validation gates

- `ast.parse` ✓
- `py_compile.compile(doraise=True)` ✓
- **PCM byte-parsing test**: synthesized known stereo s16 buffer,
  verified shape (48000, 2), dtype float32, channel values converted
  correctly to [-1, 1] range, odd-byte truncation handled, empty
  buffer detected ✓
- 23,807 lines (+61 from v2.11.9)
- No stale `2.11.9` strings
- bat CRLF / ASCII verified

### What Martin should see now

**Loop test:** load a short looping sequence (60-frame rotation, etc.).
Hit play. The wrap from end-of-sequence back to start should be
seamless — no freeze, no audio glitch. The rotating-globe-360 test
case should play smoothly indefinitely.

**MP4 audio load:** open an MP4. The "Loading audio…" status should
clear noticeably faster than v2.11.9 for the same file. Exact
speedup depends on hardware (SSD vs HDD makes a big difference for
the disk-I/O elimination); typical 2-3× faster on a modern SSD,
larger on slow storage.

### About the "High Performance Player" gap

Martin's broader point — "We describe mh_PLAYer as a High Performance
Player, currently it is not" — deserves real engagement. After
v2.11.10 the remaining performance investigations:

1. **CDL color-grade per-pixel cost.** `_apply_display_post` runs the
   CDL formula on every pixel of the display image every `_show_frame`.
   For 1920×1080 that's 2M pixel-ops in numpy per frame. Possible:
   skip CDL when at identity; use a precomputed LUT; SIMD via cython
   or numba.

2. **L2 preloader backward-window for short-loop playback.** When
   sequence length ≤ ~120 frames (fits in L2 entirely), the
   preloader could keep the WHOLE sequence hot rather than a forward
   window. Eliminates any L2 miss at the loop point.

3. **PyAV instead of ffmpeg subprocess for audio.** Direct libav
   bindings would eliminate even the subprocess fork overhead.
   Typical 5-10× speedup.

4. **Play-start warm gate.** Hold "Play" off until N frames are
   L2-cached, so playback starts smoothly rather than skipping
   through cold-cache frames.

Any of these would be a focused next-session item. They compound —
no single fix produces "buttery smooth," but together they should.

---

## Companion release — mh_license_generator v1.1.3

**Studio Pro license type added.**

- New "STUDIO PRO" option in the type dropdown (alongside INDIVIDUAL,
  STUDIO, TRIAL). Selecting it defaults the features field to
  `ALL,remote_review_sync,edl_timeline` — the explicit enumeration
  required for the v2.11 Pro features (Synced Remote Review and EDL
  Timeline). No MAC lock by default, no expiry, floating like STUDIO.
- **TRIAL** now defaults to the full Studio Pro feature set
  (`ALL,remote_review_sync,edl_timeline`), so evaluators get to test
  every feature including Pro ones during the 10-day trial. The
  features field stays editable if a more restricted trial is wanted.
- Display label "STUDIO PRO" (with space) translates to file format
  `STUDIO_PRO` (with underscore) at write time, matching
  `LicenseType.STUDIO_PRO` in the player's licence loader.
- Features dropdown gains 4 presets:
  - `ALL,remote_review_sync,edl_timeline` (Studio Pro full)
  - `ALL` (standard full)
  - `ALL,remote_review_sync` (Studio + remote review only)
  - `ALL,edl_timeline` (Studio + EDL only)
- Tooltip updated to explain that Pro features are opt-in even with
  `ALL`, with the two Pro feature key names spelled out.

Why explicit enumeration is needed: in v2.11.0 the licence model was
deliberately designed so that existing INDIVIDUAL/STUDIO licences
with `FEATURES=ALL` would NOT auto-grant the new Pro features when
upgrading the player. This kept the previous customer base on their
original purchase tier. Studio Pro requires the explicit Pro keys.

---

## v2.11.9 — 2026
**UX polish  —  histogram collapsed by default · 200ms audio pre-delay removed**

Two targeted fixes following Martin's v2.11.8 testing feedback:

> "It is better, if anything the audio clip now takes longer to load
> (a few seconds). Once loaded there is less lag on the scrubbing
> audio update (but there is still some). Set the default state of
> the Histogram to collapsed, as it definitely impacts the scrubbing
> speed."

### Fix 1 — Histogram section collapsed by default

`CollapsibleSection("HISTOGRAM", collapsed=False)` → `collapsed=True`.

The per-frame `_update_histogram(arr8)` numpy computation fires on
every `_show_frame` call. For a 1920×1080 image that's 2M pixels of
histogram math per scrub-debounced decode. Plus QLabel rendering of
the histogram pixmap. With the section default-collapsed, the panel
is hidden and several Qt branches in `_update_histogram` skip work
(it already checks `if self._info_bar.expanded` and `if self._fps <=
12` to avoid the cost during playback — collapsed sidebar adds
another natural gate).

The section still exists; users who want the histogram click the
section header to expand. One-click opt-in is the right default for
a feature that meaningfully impacts scrub responsiveness.

### Fix 2 — Remove the 200ms pre-delay before audio extraction

Previously `_open_video_file` wrapped the extraction call in
`QTimer.singleShot(200, ...)`. That delay made sense when the
decode was synchronous and blocked the UI — it gave the first frame
time to render before the freeze. With v2.11.8's threaded
extraction, the decode runs on a background thread regardless, so
the first frame already renders in parallel.

Removing the wrapper shaves 200ms off the perceived audio-load
time. The "few seconds" Martin observed on MP4 sources is the
ffmpeg subprocess fallback (libsndfile on Windows usually doesn't
support MP4/AAC, so `soundfile.read()` raises and we fall through
to ffmpeg). The 200ms wasn't the dominant cost but it was a free
win to drop.

### About the residual scrub-update lag

Martin noted "still some lag" on audio update during scrub even
after v2.11.8. With the histogram collapsed by default in v2.11.9
the per-decode work drops noticeably, which should help. If lag
remains visible after this release, the remaining likely culprits
in order:

1. **Display-transform pipeline cost** — `_apply_display_post` runs
   on every `_show_frame` (invert → CDL → flip H → flip V). The
   CDL color-grade in particular touches every pixel.
2. **Preloader scheduling overhead** — fires after each
   `_scrub_decode_now`; cheap but non-zero.
3. **Update overlay / HUD recalculation** — cheap individually but
   adds up when chained.

Each of those would be a focused investigation if needed. Worth
testing v2.11.9 first; the histogram collapse may be enough.

### Faster audio load options (deferred, if needed)

If MP4 audio extraction stays "a few seconds" and that bothers you
after using v2.11.9 for real, options:

1. **PyAV** library — direct libav bindings, much faster than
   subprocess ffmpeg (~5-10× speedup typical)
2. **Streaming RMS computation** — don't decode the full audio for
   the waveform; just sample chunks for the RMS bucket display.
   Full decode happens lazily on Play
3. **Bundled libsndfile with MP4/AAC support** — replaces the
   ffmpeg fallback with a fast direct read

PyAV (#1) is the most realistic — small dependency, big win, no
code restructure. Worth queuing for a future session.

### Validation gates

- `ast.parse` ✓
- `py_compile.compile(doraise=True)` ✓
- Targeted greps confirmed both changes landed
- 23,746 lines (+15 from v2.11.8)
- No stale `2.11.8` strings
- bat CRLF / ASCII verified

### To-do for next session (Martin requested in v2.11.8)

- **Working plugin example shipping with the release** — pre-loaded
  in the Plugins menu as a reference for third-party authors

### Other deferred items

- Play-start lag (preloader warm-up gate before Play allowed)
- PyAV-based audio extraction (fast MP4 audio)
- Drag-to-reorder blocks in EDLTrackView
- Edge trim handles on EDLTrackView
- Playhead vertical line on EDLTrackView during playback
- Bottom icon row enhancements
- Sync v1.1 (bi-directional, mDNS, auth, reconnect-on-drop)
- Exact 29.97 drop-frame timecode math
- EDL playback polish (next-clip preload, CDL persistence, loop-EDL)

---

## v2.11.8 — 2026
**Hotfix  —  scrub debounce + async audio load (the real fixes)**

Martin called out that v2.11.6 and v2.11.7 didn't fix the symptom on
MP4 sources. He was right. Both previous attempts treated symptoms,
not the root cause. This release fixes the actual problem.

### What was actually wrong (full diagnosis)

**Two distinct issues conflated under one "audio lag" symptom:**

**Issue 1: Audio loading is synchronous on the main thread.**

`_extract_video_audio` ran `soundfile.read(video_path)` directly on
the GUI thread. For an MP4, that decodes the entire AAC track in one
blocking call — 200ms to several seconds depending on duration and
codec. The fallback ffmpeg path is even worse (subprocess spawn +
PCM transcode). The UI was frozen for the duration; the waveform
"appeared late" because the entire app was hung during extraction.

**Issue 2: `_on_timeline_scrub` synchronously decodes the image.**

The scrub handler called `_show_frame` directly. For MP4 sources,
`_video_decoder.read_frame(idx)` on a non-keyframe takes 50-200ms+
(keyframe seek + P/B-frame chain decode). Every scrub event blocks
the event loop for that duration. The waveform's paint event,
audio seek, and any other UI work all queue behind it.

The v2.11.6 reorder (waveform call before _show_frame) and v2.11.7
repaint() switch made the waveform paint promptly **within each
scrub call** — but couldn't help when each scrub call itself is
50-200ms long. With user mouseMoves arriving faster than the decode,
the perceived update rate of EVERYTHING (waveform, audio, image) was
capped at ~5-20fps regardless of what the waveform call did.

You cannot fix a 50-200ms blocking call by tweaking when a 1ms paint
happens inside it.

### Real fix 1 — scrub debounce (standard pro-tool pattern)

`_on_timeline_scrub` no longer calls `_show_frame` directly. Instead:

```
_on_timeline_scrub (called on every mouseMove during drag):
    self._frame_idx = idx                  # state update (instant)
    self._update_frame_display()           # spinbox refresh
    self._canvas.set_annotation_frame(idx) # annotation sync
    self._update_hud_values()              # HUD numerics
    self._update_voice_note_ui(idx)        # note marker
    self._waveform.set_position(           # waveform repaint (sync, ~µs)
        idx/total, immediate=True)
    self._audio.seek_to_frame(idx)         # numpy index (µs)
    self._scrub_decode_timer.start()       # 30ms debounce, restarts each call
```

A `QTimer(singleShot, 30ms)` is reset on every scrub event. When the
user pauses for >30ms, the timer fires `_scrub_decode_now` which
calls `_show_frame(immediate=True)` at the latest frame index.

**During continuous drag:**
- Timeline cursor: smooth (always was — Qt's own paintEvent)
- Waveform playhead: **smooth** (cheap repaint, never blocked)
- Audio scrub: smooth (numpy index, microseconds)
- Image: stays at last decoded frame, catches up when drag pauses

**After drag stops:** image decodes once at the final position.

This is exactly how RV, DaVinci Resolve, and Premiere behave. The
image "settling" after a fast drag is the **expected professional
behavior**, not a bug. What was unprofessional was everything
following the image decode at decode-rate. That's gone now.

Image-sequence scrub (where `_show_frame` is a fast L1/L2 hit, not a
heavy MP4 decode) also goes through the debounce — but at 30ms with
fast cache hits, the user perceives no change from before. The
debounce only matters when the underlying decode is slow.

### Real fix 2 — async audio extraction

`_extract_video_audio` now spawns a background `threading.Thread`
that runs the heavy decode (soundfile or ffmpeg) off the GUI thread.
A new `_AudioLoadSignals(QObject)` carrier emits `loaded(data, sr)`
or `failed(msg)` from the worker; Qt's automatic queued-connection
routing delivers the slot call on the main thread where it's safe
to touch widgets and the audio engine.

**During the background decode:**
- Status panel shows "Loading audio…  (filename.mp4)"
- UI is fully interactive — scrub, CDL, pan/zoom, even open another
  file (see generation counter below)
- Waveform strip hidden until data arrives
- When decode completes: `_on_audio_loaded` installs the buffer,
  updates the status to "✓ Embedded audio …", refreshes the
  waveform strip

**Stale-completion guard via generation counter.** If the user opens
file A, then quickly opens file B before file A's extraction
finishes, file A's eventual completion would corrupt file B's
audio state. The `_audio_extract_gen` counter is bumped on every
`_extract_video_audio` call; completion handlers check that the
generation matches before applying — stale results are silently
dropped.

### Validation gates

- `ast.parse` ✓
- `py_compile.compile(doraise=True)` ✓
- **Live QTimer-based scrub debounce test** — 4 timing scenarios:
  - Burst (50 events, no pause): 50 cheap calls, 0 decodes during,
    1 decode at the LATEST position after pause ✓
  - Stop-and-go (2 bursts separated by pause): 2 decodes at the
    burst-end positions ✓
  - Single isolated scrub: 1 decode after 30ms ✓
  - State integrity: decode always uses latest `_frame_idx`, not a
    captured intermediate value ✓
- **Cross-thread signal test** — verified that:
  - Worker-thread `loader.loaded.emit(data, sr)` delivers slot on
    the MAIN thread (Qt queued-connection auto-routing) ✓
  - numpy array data transmits intact (no slicing/serialization) ✓
  - Failed-path message delivery ✓
  - Generation counter rejects stale file_a completion when current
    gen is file_b ✓
- 23,731 lines (+99 from v2.11.7)
- No stale `2.11.7` strings
- bat CRLF / ASCII verified

### Honest accounting of the previous attempts

- **v2.11.6**: Moved `_waveform.set_position` BEFORE `_show_frame`.
  Did literally nothing for the symptom — both call orders use
  `update()` which schedules a paint that sits queued behind the
  blocking decode either way.
- **v2.11.7**: Changed waveform's `update()` to `repaint()` with
  `immediate=True`. Made the waveform paint synchronously **within
  each scrub call** — but couldn't help when each call is 50-200ms
  long. Confirmed by Martin's screen-capture test on MP4.
- **v2.11.8 (this release)**: Removes the blocking call from the
  scrub path entirely. The scrub handler is now ~100µs of work.

The diagnostic lesson: when a fix doesn't move the needle, the model
of the problem is wrong. Don't ship a second guess based on the same
flawed model. v2.11.6 and v2.11.7 were both built on "the waveform
paint is the lag" — but the actual lag was "the scrub call itself is
slow because it decodes." Different problem, different fix.

### How Martin should verify

1. Run `python mh_PLAYer_v2_11_8.py` from cmd
2. Open the same MP4 from the previous screen capture
3. **Audio loading**: should see "Loading audio…  (filename)" status
   briefly, with the rest of the UI fully responsive during. Then
   the waveform appears. No frozen UI.
4. **Scrub**: drag the timeline rapidly back and forth. The waveform
   playhead and timeline cursor should move in lockstep, smoothly,
   no lag, no snap-after-release. The image will appear to "stick"
   at one frame until the drag pauses ~30ms, then catch up — this
   is intentional (and matches every professional player).
5. If anything still feels off, another screen capture would be
   gold for further diagnosis.

### Play-start lag (separate issue, not addressed yet)

Martin also mentioned: "There is even a bit of lag before any video
or sequence plays". This is a separate issue from the scrub lag —
it's the preloader needing time to warm the L2 cache after file
open. Possible fixes: warm-up gate (block Play until N frames
cached), eager-cache on file open, or simply tighter pre-cache
window. Worth its own focused fix but not in this release.

### To-do for next session (per Martin)

- **Working plugin example shipping with the release** — should be
  pre-installed in the Plugins menu as a reference. Pattern for
  third-party plugin authors.

---

## v2.11.7 — 2026
**Hotfix  —  audio waveform lag (real fix this time)**

The v2.11.6 "fix" only addressed call ordering. Martin tested with an
MP4 source and the lag was still there: "If I drag the time slider,
it often takes a second for the audio timeline mark to update and
snap to the correct position." The earlier diagnosis missed the actual
mechanism.

### The real mechanism

`WaveformStrip.set_position` was calling `self.update()`. Qt's `update()`
**schedules** a paint event in the queue — it doesn't paint immediately.
For an MP4 source the scrub call sequence is:

```
mouseMove
  → Timeline.frame_changed (direct connection, synchronous)
  → _on_timeline_scrub
    → _waveform.set_position(idx/total)   ← schedules paint, returns
    → _show_frame(immediate=True)
      → _video_decoder.read_frame(idx)    ← 50-200ms MP4 seek + decode
    → _audio.seek_to_frame(idx)
  → return
Qt processes next event...
```

While `_show_frame` is running, the event loop is **blocked**. The
waveform's queued paint sits in the queue. Meanwhile the user is
still dragging — more mouseMove events arrive, get compressed by Qt
(on Windows mouseMove compression is on by default), and when the
event loop next runs the queued mouseMove fires another scrub →
another `set_position` → another scheduled paint. Qt **coalesces**
multiple pending paints on the same widget into ONE paint at the
LATEST position.

Net result: the waveform paint event keeps getting deferred. It
only actually paints when there's a gap in mouse activity — which
visually manifests as the playhead "snapping" to the correct position
~1 second after the user stops scrubbing.

The image and timeline both stay smooth because:
- **Timeline** updates synchronously inside its OWN `mouseMoveEvent`
  via `self.update()` — Qt processes its paint between mouse events
- **Image canvas** repaints from `_show_frame` which is gated by the
  decode cost itself (so user sees ~5fps image updates during drag)

Only the waveform suffered because it was downstream of the blocking
work but not painted from inside its own event handler.

### Fix — synchronous repaint

`WaveformStrip.set_position` now takes an optional `immediate=True`
parameter that calls `self.repaint()` instead of `self.update()`.
`repaint()` paints **synchronously, in-line** — bypasses the queue
entirely. Every scrub event leaves a freshly-painted waveform behind
it, regardless of how slow the downstream `_show_frame` call is.

The strip is 28px tall with a trivial paint routine (filled rect, RMS
bars, single playhead line, border). Synchronous paint cost is
microseconds — well below any threshold where coalescing would matter.

Default remains `update()` so playback's per-frame `_play_tick` calls
keep the cheaper async path (24fps coalescing is fine when paints
fire at regular cadence with no event-loop saturation).

**Call sites updated:**
- `_on_timeline_scrub` → `immediate=True`
- `_play_tick` → default (update)
- `_after_step` (arrow keys) → default
- `_update_waveform` (audio device change) → default

### Diagnostic lesson

When a Qt widget appears to "lag" by a noticeable delay (hundreds of
ms to seconds) but eventually shows the correct state, suspect
**queued paints coalescing behind blocking work** before throttling/
debouncing/timer issues. The diagnostic question: "Is the paint
being SCHEDULED inside a synchronous code path that does heavy work
afterward?" If yes, `repaint()` solves it.

v2.11.6's "fix" of moving the call BEFORE `_show_frame` actually did
nothing useful — both call orders schedule a paint, both paints sit
in the queue equally. The order only matters if you also force the
paint to actually happen (which is what repaint does).

### Validation

- `ast.parse` ✓
- `py_compile.compile(doraise=True)` ✓
- **Headless behavior test**: subclassed `WaveformStrip` with tracking
  overrides of `update()` and `repaint()`; verified that
  `set_position(0.5)` calls `update()` (and not `repaint()`),
  `set_position(0.75, immediate=True)` calls `repaint()` (and not
  `update()`), and the `_pos` value is set correctly in both paths
  including clamp behavior.
- 23,632 lines (+29 from v2.11.6)
- bat CRLF / ASCII verified

### What Martin should see now

Drag the timeline on an MP4 source. The waveform playhead should track
the timeline playhead **continuously during the drag**, not snap to
position after release. The image canvas will still update at ~5fps
during fast scrub (gated by MP4 decode cost) — that's a separate issue
that would need debouncing of `_show_frame` itself to address, and is
a bigger UX change worth a separate discussion.

If the waveform STILL lags after this fix, the next steps would be:
1. Capture a screen recording so we can see the exact behavior
2. Add timing instrumentation to confirm where the time actually goes
3. Consider debouncing `_show_frame` during drag (image updates only
   on user pause; waveform stays smooth)

---

## v2.11.6 — 2026
**Backlog batch  —  canvas scrub + audio waveform lag fix**

Two deferred items knocked off after the v2.11 cycle wrapped.

### Fix — audio waveform lagging behind timeline during scrub

**Symptom:** During rapid timeline scrub, the audio waveform's playhead
visibly lags behind the main timeline playhead. Martin flagged this
with a screenshot showing waveform vs frame number 081 misaligned.

**Root cause:** In `_on_timeline_scrub`, the call order was:
```
1. self._update_frame_display()   # spinbox updates (instant)
2. self._show_frame()             # ← 10-30 ms decode + display
3. self._waveform.set_position()  # ← only fires AFTER decode
```

The timeline updates instantly because the user drags it directly
(its `set_frame` is called by mouseMoveEvent inside the widget itself).
The waveform's `set_position` was queued behind the heavy `_show_frame`
work, so it lagged by exactly the per-frame decode time. During rapid
scrubs this stacks up visibly.

**Fix:** Move the waveform `set_position` call BEFORE `_show_frame`.
Both playheads now repaint in the same paint cycle, in lockstep.
Two-line move; no other behavior changes.

### New — canvas timeline scrub (Alt+Shift+LMB drag)

Long-deferred. Drag horizontally on the canvas (image area) to scrub
the timeline directly. Useful when the timeline strip is too narrow
or you want to keep eyes on the image while seeking.

**Modifier choice — Alt+Shift+LMB.** The full modifier-conflict map
ruled out simpler bindings:

| Combo | Existing use |
|---|---|
| Plain LMB drag | Pan |
| Shift+LMB | Zoom to region |
| Ctrl+LMB | Region sample |
| Ctrl+Shift+LMB | ROI (v2.10.4) |
| Alt+LMB | Pan (alternative) |
| Middle | Pan |
| Right | A/B wipe scrub / stack context |
| **Alt+Shift+LMB** | **NEW — canvas scrub** |

Alt+Shift was the only available combo. Memorable: "Alt" = alternate
behavior, "Shift" = range-related action.

**Frame mapping.** Drag X delta maps proportionally to sequence length:

```
delta_frames = round((dx_px / canvas_width_px) * (total_frames - 1))
```

Dragging the full canvas left-to-right traverses the full sequence.
Short sequences scrub smoothly (sub-pixel-per-frame, integer-clamped);
long sequences scrub fast (many frames per pixel). Out-of-range drags
clamp to [0, n-1].

**Behaviour.** Routes through the existing `_on_timeline_scrub` handler
so audio bursts, waveform updates, preload scheduling, and the
scrub-stop debounce timer all work identically to dragging the
timeline strip. No new audio plumbing needed.

**Three new signals on `ImageCanvas`:**

```python
scrub_drag_started = Signal()
scrub_drag_delta   = Signal(int, int)   # dx_px, canvas_width_px
scrub_drag_ended   = Signal()
```

The "started" signal lets PlayerApp capture `_frame_idx` at drag start
so each delta is relative to that anchor (avoids accumulating rounding
error across many move events).

**Cursor feedback.** While scrubbing: `SizeHorCursor` (horizontal
double-arrow). Released: cursor unset, returns to default.

### Validation gates (all clean)

- `ast.parse` ✓
- `py_compile.compile(doraise=True)` ✓
- **Static signal/handler audit** — 12/12 pieces wired:
  - 3 ImageCanvas signals defined
  - 3 PlayerApp connections
  - 3 PlayerApp handler methods
  - 3 mouse-event-handler integrations (press/move/release)
- **Frame-mapping logic test** — 9/10 cases verified correct on the
  production math (the 10th "fail" was an off-by-one in my expected
  value; production behavior is correct: `round(12/1200 * 4999) = 50`,
  not 49)
- 23,603 lines (+91 from v2.11.5)
- No stale `2.11.5` strings
- bat CRLF / ASCII verified

### How to test

**Audio lag fix:** load a sequence with audio. Click on the timeline
strip and drag back-and-forth rapidly. The audio waveform playhead
should now stay locked to the timeline playhead — no visible lag.

**Canvas scrub:** load a sequence. Hold **Alt+Shift** and drag
horizontally on the image area. Cursor changes to the horizontal-resize
shape; timeline scrubs in real time. Dragging from the left edge to
the right edge of the canvas should traverse the full sequence.

### Deferred items still on the backlog

- Drag-to-reorder blocks in EDLTrackView
- Edge trim handles on EDLTrackView
- Playhead vertical line on EDLTrackView during playback
  (needs non-modal dialog redesign)
- Bottom icon row enhancements
- Sync v1.1 (bi-directional, mDNS, auth, reconnect-on-drop)
- Exact 29.97 drop-frame timecode math
- EDL playback polish (next-clip preload, CDL persistence, loop-EDL)

---

## v2.11.5 — 2026
**Studio Pro feature  —  EDL playback integration · closes v2.11 cycle**

The EDL feature is now functional end-to-end: build (v2.11.2) → import
from CMX3600 (v2.11.3) → visualise (v2.11.4) → **play through clips
sequentially (v2.11.5)**. Activate EDL Playback and the player
auto-advances through the clip list during normal Play, loading each
clip's source as it becomes current.

### New — EDL playback engine

When **EDL → Activate EDL Playback** is toggled on:

1. Loads the first clip's source via existing `_open_file` path
2. Seeks to `clip.seq_in` and sets timeline IN/OUT to `clip.seq_in..seq_out`
3. Adds an EDL position marker to the status bar (`EDL: 1/N · label`)

During Play, when `_frame_idx` exceeds the current clip's `out_point`,
`_play_tick` detects the boundary and intercepts the normal end-of-range
behavior (loop/playlist/stop) — invoking `_edl_advance_to_next_clip`
instead.

`_edl_advance_to_next_clip`:
- Loads the next clip's source
- Seeks to its `seq_in`
- Updates timeline IN/OUT
- Continues playback seamlessly

At end of EDL: clean stop (no loop in v1) with status bar
"EDL playback finished".

### Critical guard — `_edl_advancing` flag

`_open_file` is heavyweight: it triggers `_show_frame` internally, which
could itself check for EDL advance. Without a guard, you get re-entry
during the clip load. The `_edl_advancing` flag (managed via try/finally
in `_edl_load_clip`) prevents this. The `_play_tick` advance hook also
checks the flag so a mid-load tick doesn't fire a second advance.

Verified by an isolated state-machine smoke test (Test 5) — load_clip
properly sets the flag on entry and clears on exit even if `_open_file`
raises.

### Stale-EDL safety — out-of-range clamping

If a `.mhedl` file points at a source that's been shortened (or replaced
with fewer frames), the saved `seq_in/seq_out` would exceed the actual
sequence range. `_edl_load_clip` clamps both to the actual `len(self._sequence)`
so playback continues with a truncated clip rather than seeking off the
end. Status bar shows the load message; the editor will reflect the
mismatch on next open.

### Status bar — persistent EDL indicator

`QLabel` added via `statusBar().addPermanentWidget()`. Shows
`EDL: N/M · label` in amber while EDL playback is active. Hidden when
inactive. Pinned to the right side of the status bar so transient
`showMessage` calls don't overwrite it.

### EDL menu — Activate EDL Playback toggle

```
EDL
├─ ...
├─ Export CMX3600 .edl…
├─ ─────
├─ Activate EDL Playback       ← v2.11.5 (toggleable, checkable)
├─ ─────
└─ Close EDL
```

Standard mh_tools licence gate via `_edl_require`. If toggled on without
an EDL loaded, shows a guidance dialog and reverts the check. Closing
the EDL while playback is active auto-deactivates and unchecks the menu
item.

### Validation — 5 state-machine tests + standard gates

Used a `MockApp` with stubbed heavy methods (`_open_file`,
`_show_frame`, `_set_playing`) to test the EDL state machine in isolation:

1. **Load first clip** — frame/in/out set correctly, load_count=1
2. **Advance to clip 1** — next source loaded, in/out updated
3. **Advance to clip 2 (last)** — third source loaded
4. **Advance past last clip** — stops playback, no extra load, status
   shows "EDL playback finished"
5. **`_edl_advancing` guard** — flag properly toggled via try/finally
   even though we forced it True before the call

Standard gates:
- `ast.parse` ✓
- `py_compile.compile(doraise=True)` ✓
- Static attribute audit on EDL playback surface — 9/9 symbols resolve
  (3 methods, 6 attributes/widgets) ✓
- 23,512 lines (+152 from v2.11.4)
- bat CRLF / ASCII verified
- No stale `2.11.4` strings

### v2.11 cycle complete — Studio Pro feature roster

The v2.11 cycle delivered the Studio Pro tier:

- **v2.11.0** — Tier infrastructure (`LicenseType.STUDIO_PRO`, `PRO_FEATURES`,
  tier badge, About Pro dialog)
- **v2.11.1** — Synced Remote Review (TCP/JSON sync, host/follower)
- **v2.11.2** — EDL data model + editor dialog
- **v2.11.3** — CMX3600 EDL import/export
- **v2.11.4** — EDL visual timeline track widget (view + select)
- **v2.11.5** — EDL playback integration (auto-advance through clips)

Both Pro features (`remote_review_sync`, `edl_timeline`) now have full
implementations. A Studio Pro licence (`FEATURES=ALL,remote_review_sync,edl_timeline`)
unlocks the complete capability set.

### Deferred to post-v2.11 backlog

These were planned during the EDL cycle but moved out to keep each
release focused. None block the Studio Pro feature from being functional:

- **Drag-to-reorder blocks in EDLTrackView** — Move Up/Down buttons
  still work; drag is a UX convenience improvement
- **Edge trim handles on EDLTrackView** — seq_in/seq_out are editable
  via the per-clip Edit… dialog (double-click a block or list row)
- **Playhead vertical line on EDLTrackView** — track view is in a modal
  dialog; while playing back, the dialog isn't open. To make playhead
  worthwhile, the dialog needs to be non-modal — a larger UX change.
- **Click-and-drag scrub global from canvas** (original deferred item)
- **Audio timeline scrub interactivity** (original deferred item)
- **Audio timeline lagging behind main timeline when scrubbing**
- **Bottom icon row enhancements**
- **Sync v1.1** — bi-directional control, mDNS discovery, auth token,
  reconnect-on-drop
- **Exact 29.97 drop-frame timecode math** (v2.11.3 import uses naive math)
- **EDL playback polish** — preload of next-clip's first frame to
  reduce stutter at clip transitions; CDL/grade persistence across
  clip advances (currently each `_open_file` resets); loop-EDL mode

### Session capacity status

After v2.11.5 the context window is ~85% used. **This is the natural
end of this session** — a fresh conversation with the compaction
summary as a starting point would be the right call for any further
work. Recommend a checkpoint here.

---

## v2.11.4 — 2026
**Studio Pro feature  —  EDL visual timeline track widget (view + select)**

The EDL Editor dialog now shows clips as a horizontal proportional
strip in addition to the list. Lets the user see at a glance which
clips dominate the EDL length, which are short, and how they
sequence — much faster comprehension than a list.

This release is **view + select only**. Drag-to-reorder, edge trim
handles, and playback-position indicator are deferred to v2.11.5
along with playback integration (the final EDL release).

### New — `EDLTrackView(QWidget)`

Horizontal strip rendered with QPainter. Each clip is a block whose
width is proportional to its frame duration relative to the EDL total.

- **Layout**: 4px side padding, 1px gap between adjacent clips
- **Min-width clamp**: 36px floor so very-short clips remain clickable
  (a 3-frame clip next to a 1000-frame clip won't render at 0px)
- **Rescale**: if aggregate width exceeds available, all blocks
  proportionally shrink (with a hard floor of 18px) so the layout
  always fits without horizontal scrolling
- **Block labels**: clip label centered upper, truncated to fit width
- **Duration label**: frame count centered lower, dimmed
- **Empty state**: "No clips in EDL" placeholder centered

### Palette — neutral, grading-room-correct

Per Martin's principle that the player GUI should not bias colour
judgment:

- Background: `#1E1E22` (slightly darker than dialog)
- Clip fill: `#3F3F46` neutral slate (same for all clips)
- Clip border (default): `#5C5C66` lighter slate
- Hover fill: `#4C4C54` (slight lighten — still neutral)
- **Selected border**: `#D4870A` amber, 2px (the ONLY tinted element,
  and only on the selected clip)
- Label text: `#D0D0D0`
- Duration text: `#888888`

The accent colour appears only on the selected clip's border — a single
small chromatic element that's clearly an interactive state marker,
not a competing surface colour. Editor dialog is also modal and over
the player, so when it's open the user is in management mode, not
grading mode — but the constraint stands as a discipline.

### Bidirectional selection sync

- **Track → list**: click a block; `selection_changed(clip_id)` signal
  fires; dialog finds the matching list row and selects it. List then
  scrolls to show the selected row.
- **List → track**: row selection change emits
  `currentItemChanged(current, prev)`; dialog reads the new
  `clip_id` and calls `track.set_selected(cid)`.
- **EDL mutation**: any add/remove/move call ends with `_refresh_list()`
  which now also calls `track.set_edl(edl)` so the track repaints
  from the same EDL state.

### Hover + tooltip

mouseMove caches the hovered clip_id; the block lights up in `CLIP_HOVER`
fill (only if not currently selected — selection visual wins). Tooltip
shows full clip info on hover:

```
alpha
Source: C:/shots/sh010.mov
Seq: 1–47
Duration: 47 frames
```

### Layout — dialog dimensions

EDL Editor minimum size bumped from 680×480 to 720×560 to accommodate
the new track view above the list. Track widget itself is 58px fixed
height, fits cleanly between the name/fps row and the list.

### Validation — 6 headless widget smoke tests pass

Used Qt's `offscreen` platform plugin to run a real `QApplication`
without a display, instantiate the actual production `EDLTrackView`,
force paints, and exercise paint output + hit-testing + signal
emission:

1. Empty EDL renders + `_block_bounds == []`
2. Three-clip EDL — 3 non-overlapping blocks with sensible widths
3. Hit-test mid-block returns correct clip_id (both clips 0 and 1)
4. Hit-test past last block returns `None`
5. `mousePressEvent` emits `selection_changed` with correct clip_id
6. **Min-width clamp**: 1000-frame + 3-frame EDL renders both clickable
   (big=756px, tiny=34px — well above the 18px hard floor)

Plus the standard gates:

- `ast.parse` ✓
- `py_compile.compile(doraise=True)` ✓
- Static attribute audit — 3/3 dialog→track method references resolve ✓
- 23,360 lines (+238 from v2.11.3)
- bat CRLF / ASCII verified
- No stale `2.11.3` strings

### Deferred to v2.11.5 (the final EDL release)

- **Drag-to-reorder blocks** — visual feedback while dragging, drop at
  insertion gap. Modifies the EDL clip order via `move_clip`.
- **Edge trim handles** — small handles on left and right of each block;
  drag horizontally to adjust seq_in or seq_out.
- **Playback integration** — when an EDL is loaded and the user hits
  Play, auto-advance through clips: when crossing into a clip's range,
  open that clip's source and seek to its seq_in. Status bar shows
  "EDL: 3/8 clips · 142/512 frames".
- **Playhead indicator** — vertical line on the track view showing
  current EDL playback position.

### Session memory note

After v2.11.4: 23,360 lines, 9 releases shipped this session
(v2.10.6 → v2.11.4). Context window approaching 80%. **One more
substantial release should fit comfortably** (v2.11.5 with the EDL
interactivity + playback integration). After that, a fresh session
or compaction would be the right call.

---

## v2.11.3 — 2026
**Studio Pro feature  —  CMX3600 EDL interchange**

The EDL feature gains industry-standard interchange. CMX3600 is the
dominant editorial format — produced by Premiere, Avid, Resolve, FCP,
and most pipeline tools. v2.11.3 lets mh_PLAYer round-trip EDLs to
and from any tool that speaks the format.

### Reorder of v2.11.x roadmap

Martin's testing this with his other tool's CMX3600 output → bumped
this ahead of the visual timeline track widget. Remaining EDL work
now splits as:

- v2.11.4 — Visual timeline track widget (clip blocks, drag-to-reorder,
  trim handles, splice points). The crux UI work.
- v2.11.5 — Playback integration (auto-play-through clips). Touches
  `_show_frame` / `_play_tick` so wants careful integration testing.

### New — `EditDecisionList.import_cmx3600(path, fps)`

Liberal parser tolerates variable whitespace, lowercase keywords
(`title:`, `fcm:`, `from clip name:`), and missing optional columns
(transition duration token between edit type and timecodes).

- Skips `BL`/`BLACK`/`BLK` reel events (filler)
- Skips audio-only events (A/A1/A2 tracks); video and Both pass through
- Handles `C` (cut), `D` (dissolve), `W` (wipe) — D and W treated as
  cuts on import (standard simplification; dissolve target replaces
  dissolve source on the timeline)
- Comment lines `* FROM CLIP NAME:` / `* FROM CLIP:` / `* SOURCE FILE:` /
  `* CLIP NAME:` all recognised, associated with the preceding event
- CMX OUT is exclusive; mh_PLAYer `seq_out` is inclusive — auto-converted
  (`seq_out = src_out_frame - 1`)
- Drop-frame timecodes (`HH:MM:SS;FF`) parsed but produce a warning on
  the resulting EDL — math is naive (assumes wall-clock = frame count),
  exact 29.97 DF math is a v2.11.x improvement
- Warnings collected and shown in a single QMessageBox after import
  rather than spammed during parse

FPS is the caller's argument (defaults to 24.0). CMX3600 does NOT
encode rate explicitly; the player passes its current `_fps` so import
is rate-aware.

### New — `EditDecisionList.export_cmx3600(path)`

Strict canonical writer. Always NDF on export — mh_PLAYer EDLs are
internally frame-indexed so there's no source DF to preserve.

- Record timecodes computed sequentially (each clip's `record_in` =
  previous clip's `record_out`)
- Reel name = clip label truncated to 8 chars uppercase (CMX convention)
- Full source path goes in the `* FROM CLIP NAME:` comment — no info lost
- Atomic temp-file + rename (same pattern as `.mhedl` save)

### New — EDL menu items

Inserted in the EDL menu between Save As… and Close EDL:

```
EDL
├─ ...
├─ Save EDL As…
├─ ─────
├─ Import CMX3600 .edl…
├─ Export CMX3600 .edl…
├─ ─────
└─ Close EDL
```

Both gated under `edl_timeline` Pro feature.

### New — `_tc_to_frames` / `_frames_to_tc` static helpers

Module-level pair on `EditDecisionList`. NDF math: `frames =
((h * 3600 + m * 60 + s) * round(fps)) + f`. Round-tripping verified
across 7 sample timecodes — all clean. DF separator (`;` or `.` as
last separator) accepted, parsed with same naive math.

### Cosmetic — tier badge text colour matches border (Martin's feedback)

Per Martin's colour-theory note: the player GUI is intentionally
neutral grey so it doesn't bias chromaticity judgment during grading
sessions (telecine principle — surround colour affects perceived
appearance of the image being evaluated). The tier-accent colour now
only appears on the dark background fill of the pill; both the text
and the border are `C_BADGE_BG` neutral grey. Visible chrome stays
neutral; the accent is a subtle fill rather than a vibrant text colour.

### Validation gates (all clean)

- `ast.parse` ✓
- `py_compile.compile(doraise=True)` ✓
- **5 CMX3600 test categories pass** on the actual production classes:
  - Timecode helpers: 7 NDF cases + DF parsing
  - Synthetic CMX import: 5-event fixture parsed correctly (BL skipped,
    A1 audio-only skipped, dissolve treated as cut)
  - Export round-trip: re-imported file matches original EDL clip count,
    sources, seq_in, seq_out, duration
  - Liberal parsing: lowercase keywords + variable whitespace tolerated
  - Cross-format: CMX → .mhedl → .mhedl preserves all clip data
- 23,122 lines (+333 from v2.11.2)
- bat CRLF / ASCII verified
- No stale `2.11.2` strings

### Testing notes for Martin

Tomorrow's plan: export EDL from your other tool as CMX3600 .edl → in
mh_PLAYer, EDL → Import CMX3600 .edl… → check the clip list in EDL
Editor… If a clip's seq_in/seq_out look off by exactly 1, it'd suggest
the source tool also writes inclusive OUT (unusual but possible) — let
me know and we'll add a "OUT semantics" toggle. If clip sources come
through as the 8-char reel ("AX", "BL") rather than filenames, the
source tool isn't emitting `* FROM CLIP NAME:` comments — we can add
parsing of alternative comment formats if needed.

---

## v2.11.2 — 2026
**Studio Pro feature  —  EDL foundation (Part 1)**

The EDL / Multi-Clip Timeline feature lands in stages. v2.11.2 ships the
data layer and editor dialog; v2.11.3 will add CMX3600 import/export,
the visual timeline track widget, and playback-engine integration
(auto-play-through clips). All three are substantial enough to warrant
separate releases.

### New — EDL data model

```python
@dataclass
class EDLClip:
    source:  str        # path to image sequence or video file
    seq_in:  int = 0    # inclusive first frame from source
    seq_out: int = 0    # inclusive last frame from source
    label:   str = ""   # display name (auto-derived from source stem)
    clip_id: str = ""   # stable random id (auto-assigned)

@dataclass
class EditDecisionList:
    name:      str
    fps:       float
    clips:     List[EDLClip]
    file_path: Optional[str]   # path on disk (None = unsaved)
    dirty:     bool            # in-memory state diverges from disk
```

Operations: `add_clip`, `remove_clip(id)`, `move_clip(id, new_idx)`,
`total_duration`, `to_dict` / `from_dict`, `save_to_file` /
`load_from_file`. Save uses temp-file + atomic rename so a crash
mid-write can't corrupt an existing file.

### New — `.mhedl` save format (mh_PLAYer native JSON)

```json
{
  "format_version": 1,
  "name": "Reel_05",
  "fps": 24.0,
  "clips": [
    {"source": "C:/shots/sh010.mov",
     "seq_in": 1, "seq_out": 47,
     "label": "sh010", "id": "ee456d37dfbc"}
  ]
}
```

`format_version` validated at load: rejects 0/missing (corrupt or
non-mhedl) and rejects values > `FORMAT_VERSION` (newer build's format
not yet known how to parse). Smoke test caught a bug in my first draft
where future versions silently loaded as valid — fixed before shipping.

### New — EDL menu (top-level)

Sits between Playlist and Annotate menus:

```
EDL
├─ New EDL
├─ Open EDL…
├─ ─────
├─ EDL Editor…
├─ ─────
├─ Save EDL          (disabled when no unsaved changes)
├─ Save EDL As…
├─ ─────
└─ Close EDL
```

All entries gated under `edl_timeline` Pro feature via `_edl_require`
helper. Unlicenced users see the standard mh_tools licence dialog on
click.

### New — EDL Editor dialog

Modal QDialog for managing the active EDL:

- **Top:** name LineEdit + FPS DoubleSpinBox (both live-bound to the EDL)
- **Middle:** QListWidget showing clips with format
  `N.  label   [seq_in–seq_out]   duration fr   ·   filename`
- **Side buttons:** Add Current, Add File…, Remove, Edit…, Move Up, Move Down
- **Summary line:** `N clips · total F frames · X.XX fps  *` (* = dirty)
- **Bottom:** Save · Save As… · Close

Double-click on a clip opens the per-clip edit sub-dialog
(`EDLClipEditDialog`) for label / seq_in / seq_out editing. Seq_out
< seq_in is rejected with a warning.

**Add Current** pulls the player's currently-open source and the IN/OUT
range from the FrameRangeBar — fast workflow for building EDLs from
review sessions where Martin already has clips loaded one at a time.

**Add File…** drops a placeholder 0–0 range that the user can edit;
avoids scanning files at click-time (sequence detection / video probing
can be slow).

### New — unsaved-changes guard

`New EDL`, `Open EDL…`, and `Close EDL` all check the dirty flag and
offer Save / Discard / Cancel before discarding the current EDL.
Standard QMessageBox pattern.

### Cosmetic — Studio badge tweak (Martin's feedback)

Tier pill outline: 1px → 2px (the 1px line was breaking up on Windows
subpixel rendering). Outline colour: tier accent → `C_BADGE_BG` so the
two pills (tier + version) share a consistent neutral frame. Padding
adjusted by 1px to keep overall pill size unchanged.

### About Studio Pro dialog — feature status updated

`remote_review_sync` no longer shows "coming soon" tag (shipped v2.11.1).
`edl_timeline` now shows "foundation in v2.11.2 — playback integration
in v2.11.3" so the dialog stays honest as the cycle progresses.

### Explicit deferrals to v2.11.3

- **CMX3600 .edl import/export** — industry-standard format, gnarly
  timecode parsing, deserves its own release with proper test fixtures.
- **Visual timeline track widget** — clip blocks on a scrub-bar with
  drag-to-reorder, splice points, trim handles. Major UI design work.
- **Playback integration** — when EDL is active, auto-load next clip
  when crossing into its range during playback; status bar shows
  EDL position. Requires careful integration with `_show_frame`,
  `_play_tick`, and the L1/L2/L3 cache invalidation logic.

### Validation gates (all clean)

- `ast.parse` ✓
- `py_compile.compile(doraise=True)` ✓
- Static EDL-attribute audit on `self._edl*` — 12/12 references resolve ✓
- Menu action handlers verified to exist on PlayerApp — 6/6 ✓
- Class definitions vs reference counts balanced ✓
- **Live data-model round-trip** on actual production class definitions
  (not stubs) — 8 tests pass: clip creation, EDL composition, dict
  round-trip, file save/load, move_clip, remove_clip,
  format_version rejection (both negative and future), dirty tracking ✓
- `dataclasses.dataclass` / `field` imports added (caught by an import
  audit — would have NameError'd at runtime, py_compile only catches
  syntax not import resolution)
- `QGridLayout` import added (used in EDLClipEditDialog sub-dialog)

### Build

- 22,789 lines (+657 from v2.11.1)
- No stale `2.11.1` strings
- bat CRLF / ASCII verified

---

## v2.11.1 — 2026
**Studio Pro feature  —  Synced Remote Review**

The first actual Studio Pro feature lands. Two or more mh_PLAYer instances
on a LAN share a playhead in real time: the host machine drives, followers
follow. Designed for remote VFX review sessions where reviewer and artist
need to look at the same frame at the same moment without leaning over
a screen-share.

### How it works

**Host side:** Playback → Sync (Host)… starts a TCP listener on port 8767
and shows the host's LAN IP. Every frame change, play, and pause is
broadcast to all connected followers.

**Follower side:** Playback → Sync (Join)… prompts for the host's IP
(default port 8767, override with `ip:port`). Once connected, incoming
frame/play/pause messages drive the local playhead directly.

**Disconnect:** Playback → Sync (Disconnect) tears down whichever role
this instance is in. Idempotent — safe to invoke when not connected.

### Protocol — JSON-line over TCP

```
Host -> Follower:
  {"type":"frame","idx":1234,"playing":false}
  {"type":"play"}
  {"type":"pause"}
  {"type":"hello","host_version":"2.11.1","msg":"Connected to mh_PLAYer sync host"}

Follower -> Host:
  {"type":"bye"}                    (on graceful disconnect)
```

One message per line, UTF-8, terminated by `\n`. Stdlib `socket` + `json`
only — no `websockets` dependency. ~50ms LAN latency is well below the
threshold for shared review.

### Implementation notes

**`SyncReviewServer`** — listening socket + accept loop in a daemon
thread, one daemon thread per connected client (mostly idle, watches for
`bye`). Broadcast is non-blocking: dead sockets are dropped silently
rather than waiting for timeouts. Lock-protected client list. Idempotent
start/stop from any thread.

**`SyncReviewClient` (QObject)** — single receive thread emits Qt
signals (`frame_received`, `play_received`, `pause_received`,
`connected`, `disconnected`). Signals auto-marshal to the main thread
via Qt's default queued connection, so PlayerApp handlers run safely on
the GUI thread without locks.

**Echo-loop guard** — when a follower applies an inbound message
(e.g., setting `_frame_idx` and calling `_show_frame`), the broadcast
hook in `_show_frame` would normally re-emit the same message back. A
`_sync_applying_remote` flag short-circuits the broadcast during inbound
application. Without this guard, two connected instances would create
an immediate feedback storm.

**Same-frame dedup** — paused-state redraws (CDL/EV/gamma adjustments)
re-enter `_show_frame` without changing `_frame_idx`. The
`_sync_last_broadcast_idx` tracker stops these from spamming followers
with identical messages.

**Mutual exclusion** — host and follower roles are mutually exclusive
per instance. Joining a host when already hosting prompts confirmation
and stops hosting first.

### Constraints — what v2.11.1 explicitly does NOT cover

- **No bi-directional control.** Followers receive only. Future v2.11.x
  may add follower-can-scrub mode.
- **No state beyond frame + play/pause.** Each viewer keeps their own
  EV/gamma/grade — matches review-session practice where artists may
  evaluate the same frame at different exposures.
- **No discovery.** Host IP entered manually. mDNS or UDP broadcast
  could come later.
- **No authentication.** Trusts the LAN. Anyone reachable on port 8767
  can connect and follow. For untrusted networks, use a VPN or wait
  for a future auth pass.
- **No sequence-mismatch checking.** If host displays shot_A and
  follower has shot_B loaded, frame N still gets set on follower —
  whatever that means for their loaded sequence. User responsibility.
- **No reconnect.** A dropped connection requires re-running Join.

### Gating — `remote_review_sync` Pro feature

Both Host and Join check `_LIC.require('remote_review_sync', self)` —
the standard mh_tools modal licence dialog shows for unlicensed users.
Disconnect remains available regardless (so users who lose their licence
mid-session can still tear down cleanly).

### Validation gates

- `ast.parse` clean
- `py_compile.compile(doraise=True)` clean
- Static attribute audit — all `self._sync_*.X` calls resolve to a real
  method/property/signal on Server or Client (14/14 covered)
- **Live protocol round-trip test** — server + client instantiated on
  localhost; 3 frame broadcasts + play + pause + clean shutdown all
  verified working. The test exercises the actual production classes
  (not stubs of them), so a real socket session is proven before
  shipping.
- `socket` import added to module top (the gates would have missed this
  at runtime — caught by the explicit import audit)

### Build

- 21,962 lines (+344 from v2.11.0)
- No stale `2.11.0` strings
- bat CRLF / ASCII verified

### Looking forward — v2.11.2

EDL / Multi-Clip Timeline. The Pro feature key `edl_timeline` is already
in place. Major UI design pass needed for the timeline data model and
edit operations — this will be a larger release than v2.11.1.

---

## v2.11.0 — 2026
**Studio Pro licence tier  —  infrastructure release**

Opens the v2.11 big-lift cycle. This release lays the licensing plumbing
for two upcoming Pro-tier features: **Synced Remote Review** (v2.11.1)
and **EDL / Multi-Clip Timeline** (v2.11.2). No new user-facing features
in v2.11.0 itself — but the new tier badge will appear in the header and
title bar for users running test Pro licences.

### New — `LicenseType.STUDIO_PRO`

Joins TRIAL / INDIVIDUAL / STUDIO / UNLICENSED as a fifth tier. Licence
files with `TYPE=STUDIO_PRO` and the matching FEATURES enumeration
unlock the new Pro feature keys.

### New — feature keys (placeholders for v2.11.x)

```python
LICENSE_FEATURES = {
    # ... existing 8 standard features unchanged ...
    "remote_review_sync": "Synced Remote Review",
    "edl_timeline":       "EDL / Multi-Clip Timeline",
}
PRO_FEATURES = frozenset({"remote_review_sync", "edl_timeline"})
```

Neither feature is implemented yet — the keys are in place so v2.11.1
and v2.11.2 can wire gating with `_LIC.feature('remote_review_sync')`
or `_LIC.require('edl_timeline', self)` without further licence work.

### Critical — backward-compatible `FEATURES=ALL` semantics

Existing INDIVIDUAL / STUDIO licences with `FEATURES=ALL` would have
silently gained the new Pro features if the parser simply expanded
"ALL" to every key in LICENSE_FEATURES. Fixed by changing `has_feature`
parse logic:

```
FEATURES=ALL                            → standard features only (Pro excluded)
FEATURES=ALL,remote_review_sync         → standard + that one Pro feature
FEATURES=ALL,remote_review_sync,edl_timeline → full Studio Pro
FEATURES=annotations,slates             → just those two (explicit list, unchanged)
```

The `ALL` token now expands to `set(LICENSE_FEATURES.keys()) - PRO_FEATURES`.
Studio Pro licences must enumerate the Pro features explicitly. Existing
INDIVIDUAL/STUDIO licences shipped before v2.11.0 keep their exact
prior capability set — zero surprise upgrades.

5 isolated semantics tests pass (full + Pro hybrid + custom + edge cases).

### New — `LicenseInfo.tier_name` + `is_studio_pro`

```python
info.tier_name      # 'Studio Pro' / 'Studio' / 'Individual' / 'Trial' / 'Unlicenced'
info.is_studio_pro  # bool
```

Use these for UI display and Pro-only feature gates. (Note: actual
runtime feature gates still use `_LIC.feature(key)` — `is_studio_pro`
is for UI cosmetics like tier badges.)

### UI — tier badge in HeaderStrip

A second pill next to the version badge shows the current tier:
- **Studio Pro** → amber (premium)
- **Studio** → cyan
- **Individual** → green
- **Trial** → orange
- **Unlicenced** → grey

`HeaderStrip.__init__` accepts a new `tier_text` parameter; PlayerApp
passes `_LIC.info.tier_name` at construction time. Visible immediately
at startup; updates when a new licence is loaded require a restart
(same as today's licence install pattern).

### UI — window title shows tier

```
mh_PLAYer  v2.11.0  ·  Studio Pro  ·  Martin P. Heigan
mh_PLAYer  v2.11.0  ·  Individual  ·  ACME VFX  ·  John Smith
mh_PLAYer  v2.11.0  ·  UNLICENSED
```

### UI — Help → About Studio Pro

New help dialog lists the Pro feature roster with per-feature unlock
state derived from the current licence. Features not yet implemented
are flagged `(coming v2.11.x)` so the dialog stays honest as the
cycle progresses. Replaces guesswork about whether Pro is active.

### UI — License Information dialog updates

`Help → License Information…` now shows the friendly tier name (with
Studio Pro highlighted in amber) and the actual unlocked feature set
derived from `has_feature` rather than the raw FEATURES string. Easier
to read.

### Licence generator side (separate tool — not in this release)

The `mh_license_generator` tool will need a `STUDIO_PRO` tier preset
that writes:

```
TYPE=STUDIO_PRO
FEATURES=ALL,remote_review_sync,edl_timeline
```

Producing a Pro key for beta testing — to be done in a separate
licence-generator update.

### Validation gates (all clean)

- `ast.parse` clean
- `py_compile.compile(doraise=True)` clean
- 5 isolated semantics tests pass:
  - `FEATURES=ALL` → 8 standard, 0 Pro (backward compat)
  - `FEATURES=ALL,remote_review_sync,edl_timeline` → all 10 features
  - `FEATURES=ALL,remote_review_sync` → 9 features (8 std + 1 Pro)
  - `FEATURES=annotations,slates` → 2 features (explicit list)
  - Unknown / empty tokens → silently dropped (forward-compat)
- No stale `2.10.8` version strings

### Looking forward

- **v2.11.1**: Synced Remote Review (the first actual Pro feature).
  Builds on existing `RemoteControlServer` (7979) + `RemoteReviewServer`
  (8765) infrastructure. WebSocket protocol on a new port for bi-directional
  playhead sync between mh_PLAYer instances.
- **v2.11.2**: EDL / Multi-Clip Timeline. Non-destructive editorial
  sequencing. Major UI work — needs a real design pass.

---

## v2.10.8 — 2026
**Hotfix  —  FrameRangeBar.set_bookmarks proxy missing**

Onion-peeling continued from v2.10.7. With the `zoom` TypeError fixed,
`_open_video_file` ran further and hit the next latent bug.

### Bug — `FrameRangeBar.set_bookmarks` AttributeError

**Symptom:** Console traceback when opening any file:
```
File "..." line 15536, in _open_video_file
    self._timeline.set_bookmarks({})
AttributeError: 'FrameRangeBar' object has no attribute 'set_bookmarks'
```

**Root cause:** PlayerApp's `self._timeline` is a `FrameRangeBar`. The
FrameRangeBar wraps an inner `Timeline` (line 7373) that DOES have
`set_bookmarks`. The wrapper proxies `set_frame`, `set_in`, `set_out`,
`set_sequence`, `in_point`, `out_point`, and re-emits drag signals —
but `set_bookmarks` was never added to the proxy set. Five PlayerApp
call sites would all fail; only the first to be reached actually
crashed (the others would have if execution had got that far).

Why this didn't surface before v2.10.8: the v2.10.6 `zoom` TypeError
in `_update_overlay` killed `_show_frame` mid-execution before the
`set_bookmarks` line could run. Now that v2.10.7 fixed zoom, the next
bug downstream surfaced.

**Fix:** Added a `set_bookmarks(bm)` proxy method on FrameRangeBar
(line 7561) that forwards to `self._timeline.set_bookmarks(bm)`. One
method, all five PlayerApp call sites unblocked simultaneously.

### Static-scan validation

Audited every `self._timeline.X` access in PlayerApp scope against
FrameRangeBar's published attribute surface. Found 17 distinct method
/ property / signal accesses, all but `set_bookmarks` already covered.
After this fix: complete coverage. No other latent proxy gaps lurking
on this path.

### Validation gates

- `ast.parse` clean
- `py_compile.compile(doraise=True)` clean
- Isolated smoke test: `FrameRangeBar.set_bookmarks({})` proxies correctly
- Static attribute-surface scan: 100% PlayerApp `self._timeline.X` calls
  resolve to a real FrameRangeBar attribute

---

## v2.10.7 — 2026
**Hotfix  —  two latent bugs revealed by .py-from-cmd testing**

Martin ran `python mh_PLAYer_v2_10_6.py` directly from cmd (rather than the
built exe, which had been swallowing stderr) and surfaced two real bugs.

### Bug 1 — `_LIC.has_feature()` doesn't exist (AttributeError)

**Symptom:** Console traceback on first show:
```
AttributeError: 'LicenseManager' object has no attribute 'has_feature'
```

**Root cause:** I wrote `_LIC.has_feature().get('plugins', False)` in two
places (in `_load_plugins` and `_refresh_plugins_menu`) when the actual
API is `_LIC.feature(key) -> bool`. The confusion: `LicenseManager._info`
has a `.has_feature` *attribute* (a dict) but the manager itself only
exposes `.feature(key)` (a method). I imagined a method that doesn't exist.

**Fix:** Both sites now call `_LIC.feature('plugins')`. Confirmed by direct
inspection of the LicenseManager class at line 220 in the source.

This is why Martin's Plugins menu showed `(loading…)` in the v2.10.6 build
and "does nothing when I click in it now" in the .py run — the refresh
threw AttributeError silently on the GUI path; on the .py path it printed
to console. The v2.10.6 `QTimer.singleShot(0, ...)` defensive refresh fix
DID fire, but the refresh method itself raised. Now it works.

### Bug 2 — `_canvas.zoom` was a method, not a property (TypeError)

**Symptom:** Console traceback on every video/sequence frame display:
```
File "..." line 16597, in _update_overlay
    zp = self._canvas.zoom * 100
TypeError: unsupported operand type(s) for *: 'method' and 'int'
```

**Root cause:** `def zoom(self) -> float: return self._zoom` was a plain
method on the canvas. Two call sites accessed it as if it were a property:
`self._canvas.zoom` (returning the bound method) and tried to multiply
it by an int. TypeError every time.

**Critically:** this was the ACTUAL root cause of the
"scopes-don't-update-with-video" bug reported during v2.10.5 testing.
The exception propagated up through `_update_overlay()` inside
`_show_frame()`, so the *next* line — `_update_histogram(arr8)` — was
never reached. The scope widgets never got their data; the placeholder
lingered forever. My v2.10.6 `update_frame()` paint-robustness fix was
treating the symptom; v2.10.7 fixes the cause.

The bug existed in v2.10.5 too — but Martin was testing the built exe
where stderr was redirected away from the console he was watching, so
the exception storm was invisible. Running the .py directly today made
the trace explode loudly.

**Fix:** Added `@property` decorator on `Canvas.zoom`. Cleanest possible
fix — converts both call sites from a buggy-method-reference into a
correct attribute access without changing any caller. Verified no other
code calls `zoom()` explicitly (only 2 call sites total, both bugs).

### Pre-delivery validation

- `ast.parse` clean
- `py_compile.compile(..., doraise=True)` clean
- Isolated smoke test: `c.zoom * 100` now returns the float, not
  TypeError, after `@property` annotation
- Two `_LIC.has_feature` occurrences confirmed replaced with `_LIC.feature(...)`
- No stale `2.10.6` version strings

### Validation lesson — testing from .py vs built exe

Built Nuitka exes can redirect or buffer stderr in ways the launching
cmd doesn't see immediately. Running `python source.py` from the .venv
gives the full traceback stream. For future patch cycles, ALWAYS run the
.py directly first, watching stderr, before building. This conversation
just paid for that lesson twice (v2.10.4 import error, v2.10.7 dual
AttributeError + TypeError).

### What's still in v2.10.6 (carried into v2.10.7)

All v2.10.6 changes remain:
- Scope `update_frame` paint robustness (`self.update()` + conditional repaint)
- Plugins menu `QTimer.singleShot(0, ...)` defensive refresh
- View menu restructure (Compare submenu + B Source sub-submenu)
- "image & video player" file description

With v2.10.7's two fixes, the scopes-with-video path should now work
end-to-end (paint trigger AND data delivery), and the Plugins menu
should populate correctly for both licensed and unlicensed users.

---

## v2.10.6 — 2026
**Patch release  —  bug fixes from v2.10.5 user testing**

Three fixes from the v2.10.5 testing session. No new features; all changes
target real bugs / UX issues observed on a freshly compiled Windows build.

### Fix 1 — Scopes silent on video load (bug)

**Symptom:** Open any video file (.mp4 etc) and the sidebar Histogram +
Waveform/Parade/Overlay/Luma scopes show the "Load a sequence to see…"
placeholder forever. Works correctly for image sequences. Reported by
Martin from v2.10.5 build testing.

**Root cause:** During `_open_video_file`, `populate_aov_tree()` runs
right before `_show_frame(immediate=True)`. The AOV-tree reconfiguration
puts the parent panel into a brief layout-flux state in which
`isVisible()` returns False on the scope widgets. The existing
`update_frame` code only called `repaint()` when visible, so the first
frame's histogram/waveform data was computed and stored but never
painted. The placeholder lingered indefinitely.

**Fix:** `HistogramView.update_frame` and `WaveformScope.update_frame`
now call `self.update()` unconditionally (queues a deferred paint that
fires on the next event-loop tick regardless of current visibility) AND
`self.repaint()` only when currently visible (preserves the QScrollArea
viewport behaviour that motivated repaint in the first place). Two
widgets, four lines changed.

### Fix 2 — Plugins menu stuck on "(loading…)"

**Symptom:** Plugins menu shows only `(loading…)` placeholder forever,
even when unlicensed (where it should read "Plugins require a licence").
Reported by Martin from v2.10.5 build testing.

**Root cause:** The placeholder is set in `_build_menus`. It should be
replaced by `_refresh_plugins_menu()` which fires from `showEvent` via
`_load_plugins()`. But if any condition causes the call chain not to
fire — `plugins_enabled=False` in prefs, a path where `showEvent` runs
before `_plugin_host` is set up, or an early-return — the placeholder
lingers.

**Fix:** Added a `QTimer.singleShot(0, self._refresh_plugins_menu)` at
the end of the Plugins-menu construction in `_build_menus`. This queues
a refresh for the next event-loop tick after init completes, regardless
of any later `_load_plugins` path. Belt-and-suspenders — `_load_plugins`
still calls `_refresh_plugins_menu` on first show; the QTimer is a
guaranteed earlier refresh path. The placeholder cannot now linger.

### Fix 3 — View menu cropping on HD-laptop screens

**Symptom:** View menu grown to ~30 entries spanning multiple groupings;
overflows ≤768-pixel-tall laptop screens. Reported by Martin from
v2.10.5 build testing.

**Fix:** Restructured the View menu with two new submenus, dropping
~8 top-level entries:

- **Compare →** new submenu containing:
  - Compare A/B (set B frame)  `[`
  - Diff Mode
  - B Source → (sub-submenu)
    - Load B Source…
    - Clear B Source
    - B Offset +1 frame
    - B Offset −1 frame
    - Reset B Offset
  - Stack / Grid → (existing submenu, moved under Compare)

- **ROI hint line removed.** The disabled `"ROI: Ctrl+Shift+drag canvas
  to set crop region"` row was hogging vertical space for no interactive
  value. Hint moved into the `Clear ROI` action's tooltip — discoverable
  on hover, doesn't consume a menu row.

### Other

- **Version description updated** to "mh_PLAYer - High-performance VFX
  image & video player" (was "image sequence player") in
  `version_mh_PLAYer_v2_10_6.txt` and the Nuitka build command line.
  Reflects that the player handles both image sequences and standalone
  video files.

### Validation

Pre-delivery gates:
- AST clean (21,459 lines, +26 from v2.10.5)
- `py_compile.compile(doraise=True)` clean (per v2.10.4 hotfix lesson)
- No stale `2.10.5` version strings
- Sanity check: 12 references to `compare_menu` / `bsrc_menu` confirm
  the View menu restructure landed

---

## v2.10.5 — 2026
**Retime per source  —  persistent playback-speed multiplier**

Final feature of the v2.10 cycle. Adds a per-source playback-speed multiplier
that composes with the existing shuttle (J/L) and FPS override. Persists in
workspace prefs and per-input-buffer state.

### New — `_retime_speed` on PlayerApp

Range 0.1× – 4.0×, default 1.0×. Stored per workspace AND per input buffer
(Ctrl+1–9), so different shots can have different retime values. Recalling
a buffer restores its retime alongside the existing EV / gamma / dmode /
FPS state.

### New — composition in `_play_tick`

The effective playback rate is `fps × shuttle × retime`. Single integration
point in `_play_tick`:

```python
speed = max(0.0625, self._shuttle_speed * self._retime_speed)
```

Existing shuttle math (≥1× = skip frames, <1× = stretch interval) handles
the composed value naturally. No other code paths touched.

### New — TransportBar UI

Compact spinbox sits between the FPS spinbox and the stride label:

- `RT [1.00×]` — QDoubleSpinBox, range 0.1–4.0, step 0.05, 2 decimals
- Right-click → reset to 1.00× (mirrors the FPS spinbox idiom)
- Amber + bold styling when value ≠ 1.00× (mirrors FPS override tint)
- Tooltip explains the composition with shuttle and FPS

Two new TransportBar signals: `retime_changed(float)` and `retime_reset()`.
Two new helpers: `set_retime(value)` for programmatic updates (used on file
open, buffer recall, workspace restore) and `_sync_retime_tint(value)` for
the amber styling, called on both user and programmatic value changes.

### New — PlayerApp handlers

- `_set_retime(speed)` — slot for `retime_changed`. Clamps to (0.1, 4.0),
  updates internal state, persists to prefs, stores into the active buffer.
- `_reset_retime()` — slot for `retime_reset` (right-click). Restores 1.00×.

### Buffer integration

`_store_buffer` writes `'retime_speed': self._retime_speed` into the buffer
dict. `_recall_buffer` reads `buf.get('retime_speed', 1.0)` and pushes via
`self._transport.set_retime(...)`. Older saved buffers default to 1.0
silently — no migration step needed.

### Prefs persistence

- Initial startup: `self._prefs.get('retime_speed', 1.0)` clamped + applied
- Workspace save: `'retime_speed': self._retime_speed` in state dict
- Workspace restore: `state.get('retime_speed', 1.0)` clamped + applied
  via `self._transport.set_retime`

### Architectural notes

**No licence gate.** Simple playback-speed control is core viewer
functionality (shuttle is already free). The "per-source" aspect — storing
retime with a buffer — is just data persistence, not a licensed capability.

**Composes, doesn't replace.** Retime is multiplicative with FPS and
shuttle. A user can have:
- File native: 24 fps
- FPS override: 12 fps (wall-clock half-speed)
- Retime: 0.5× (slow-mo on top)
- Shuttle: 4× (J/L fast-forward)
- Effective rate: 12 × 0.5 × 4 = 24 fps (back to native!)

This is by design — each control is independent. The user can isolate the
effect they want.

**Frame holds deferred.** The original roadmap mentioned per-frame holds
(`_hold_frames: Dict[int, int]` mapping real_idx → held sequence_idx).
That adds significant complexity to playhead/timeline/cache interaction
for ambiguous review-workflow value. Deferred to v2.11+ if real demand
materialises.

### Test plan

1. Load any sequence, click RT spinbox in transport, set to 0.50×
2. Confirm: amber + bold styling on RT spinbox
3. Press space → playback at half real-time speed
4. Adjust to 2.00× → playback at 2× speed (frames may skip per shuttle math)
5. Right-click RT spinbox → resets to 1.00×, amber styling clears
6. Set 0.5×, press J to shuttle → composes (J shuttle = 2× → effective 1×)
7. Ctrl+1 to store current state as buffer 1
8. Load a different sequence, set retime 1.5×, Ctrl+2 → buffer 2 stored
9. Press `1` (recall buffer 1) → retime returns to 0.5×; transport reflects
10. Press `2` (recall buffer 2) → retime updates to 1.5×
11. Close + reopen player → retime restored from prefs

### Forward-looking — v2.10 cycle closes here

After v2.10.5 the v2.10 medium-feature cycle is complete:

| Version | Feature |
|---|---|
| v2.10.0 | Per-source LUT |
| v2.10.1 | Onion-skinning + badge fix |
| v2.10.2 | Stack / grid compare |
| v2.10.3 | Plugin scripting |
| v2.10.4 | ROI (Region of Interest) |
| v2.10.5 | Retime per source |

v2.11 opens the big-lift cycle: synced remote review, EDL / multi-clip
timeline, and the Studio Pro licence tier. These three deserve dedicated
design passes before any code is written.

---

## v2.10.4 — 2026
**ROI  —  Region of Interest (decode-time windowed display)**

Fifth feature of the v2.10 cycle and first of Tier 3. Lets you drag a region
on the canvas; only that crop is decoded and displayed thereafter. The
cropped extent becomes "the image" for zoom, pan, A/B compare, and pixel
inspector — at proportionally lower RAM and downstream compute cost.

### New — decoder ROI parameters

`load_exr_raw(...)` and `load_raster_raw(...)` both gained an optional
`roi: Tuple[int, int, int, int]` kwarg representing `(x0, y0, x1, y1)`
in image-space coordinates. Crop is applied BEFORE proxy downsample, so
the proxy ratio operates on the cropped extent (matches what's on screen).
A new internal `_finalize(arr)` helper in `load_raster_raw` replaces three
identical `if proxy > 0` blocks across the DPX / HDR / PIL paths.

For `load_exr_raw`, the ROI is a post-decode numpy slice rather than a
true OpenEXR windowed read — the latter requires per-channel awkward
scanline-range I/O. Practical benefit is still significant: RAM and all
downstream display / cache / compare work drop proportional to crop area.
True windowed I/O is a future optimisation when the gain matters.

### New — canvas rubber band + active indicator

- `Ctrl+Shift+drag` on canvas → image-space ROI rubber band (amber, dashed)
- On release → emits new `roi_changed(x0, y0, x1, y1)` signal
- Canvas state: `_roi_start`, `_roi_end`, `_roi_active`
- `set_roi_active(True)` paints a 2-pixel amber border around the canvas
- `roi_cleared` signal fires on canvas double-click when `_roi_active`
- Modifier ordering: ROI branch comes BEFORE Shift+drag and Ctrl+drag in
  `mousePressEvent` (more specific modifier check wins)

### New — `PlayerApp._set_roi()` / `_clear_roi()`

Single canonical helper for ROI state transitions. On change:
1. Auto-exit stack mode (per-cell ROI deferred to v2.11+)
2. Flush A and B preloaders, push new ROI to each via `set_roi()`
3. Wipe L1, L2, B-L2 caches (decoded extent differs after crop)
4. Update canvas active indicator
5. Refresh current frame via `_show_frame(immediate=True)`
6. Status bar message with crop dimensions + clear hint
7. Toggle View → Clear ROI menu action enabled state

### Preloader ROI integration

`SequentialPreloader._roi` field + `set_roi(roi)` setter. Both internal
decode paths (`load_exr_raw` and `load_raster_raw`) pass `roi=self._roi`,
so background preload work also produces cropped frames consistent with
the foreground display.

### Decoder pass-through map

| Site | ROI applied? |
|---|---|
| Main `_show_frame` EXR/raster decode | ✓ |
| Save Frame helper | ✓ (saves what you see) |
| Contact-sheet helper | ✓ |
| B-source raster decode | ✓ (compare matches) |
| Preloader (both classes) | ✓ |
| Frame / Video export workers | ✗ (deliverables = full frame) |
| Stack-mode cell render | ✗ (stack auto-exits on ROI set) |
| Sequence probe | ✗ |

### File-open behaviour

`_open_file` and `_open_video_file` both silently clear ROI at the top of
the method — the new file may have different dimensions, and stale image-
space coordinates would be invalid.

### View menu

- `View → Clear ROI` with `Escape` shortcut (WindowShortcut context)
- Disabled until ROI is set; enabled / disabled by `_set_roi`
- Disabled hint "ROI: Ctrl+Shift+drag canvas to set crop region" above it

### Canvas double-click behaviour

`mouseDoubleClickEvent` now branches:
- When `_roi_active` is True → emit `roi_cleared`
- Otherwise → existing `fit()` behaviour (unchanged)

### CLI

```
--roi X,Y,W,H        Region of Interest: decode only this rectangle in
                     image-space pixels. Useful for headless transcoding
                     of a window from a large EXR.
```

Applied via a 50 ms `QTimer.singleShot` so the window is fully constructed
before the ROI refresh runs.

### Test plan

1. Load a 4K EXR sequence → press `Ctrl+Shift+drag` on a region → release
2. Confirm: amber border appears, cropped extent fills the canvas, status
   bar reads `ROI: WxH @ (X, Y) — Esc / double-click to clear`
3. Confirm: A/B compare still works (both show cropped extent)
4. Confirm: pixel inspector reads correctly within the cropped extent
5. Press `Escape` → ROI clears, full frame returns
6. Set ROI again → double-click canvas → ROI clears
7. Open a different sequence with different dimensions → ROI silently
   cleared, no error
8. Enter stack mode while ROI is active → ROI clears, stack works normally
9. Run `mh_PLAYer.exe --roi 100,100,512,512 shot.####.exr` → starts with
   ROI applied
10. Run `mh_PLAYer.exe --roi bogus.input` → warning logged, player runs
    without ROI

### Architectural notes

- **No new licence key.** ROI is a free core feature — it doesn't add
  capability so much as constrain what the existing pipeline operates on.
- **Compare-cache coherency.** Both A and B preloaders receive the same
  ROI via independent `set_roi()` calls. Their L1/L2 caches are wiped
  together in `_set_roi`, so A/B can never go out of sync.
- **No new rule.** The pattern "flush preloader + wipe caches + push new
  parameter via setter" is the same one used for source LUT (v2.10.0)
  and applies cleanly here.

---

## v2.10.3 — 2026
**Plugin scripting  —  third-party Python extensions**

Fourth feature of the v2.10 cycle. Opens the player to studio pipelines via
a `plugins/` folder + a clean public API. Plugins are gated by a new
`plugins` licence-feature key.

### New — `PluginHost` class + `mh_player_api` injected module

`PluginHost` discovers `.py` files in two directories and imports each one
once at startup. Before any plugin runs, `mh_player_api` is injected into
`sys.modules` so plugins can `import mh_player_api` and call its functions.

**Discovery order:**
1. `~/.mh_player/plugins/` — user-level (recommended, persists across reinstall)
2. `<exe_dir>/plugins/` — app-level (optional, for shipped example plugins)

**Public API (`mh_player_api`, v1.0):**

*Queries:*
- `get_current_frame()` `get_current_frame_number()` `get_sequence()`
  `get_current_path()` `get_video_path()` `get_fps()` `get_in_out()`
  `get_ev()` `get_gamma()` `get_display_mode()` `get_proxy()`

*Actions:*
- `goto_frame(n)` `play()` `pause()` `stop()` `open_file(path)`

*Utilities:*
- `log(msg, level='info')` `notify(msg, timeout_ms=3000)`
- `api_version` `player_version`

*Registration:*
- `register_menu_item(label, callback)`
- `on_frame_change(callback)` — `(frame_idx, path)`
- `on_open_file(callback)` — `(path)`
- `on_export_complete(callback)` — `(out_path)`

### New — frame-loop and event hooks

| Event | Fire point |
|---|---|
| `frame_change` | `_update_histogram` (every successful frame display) |
| `open_file`    | end of `_open_file` and `_open_video_file` |
| `export_complete` | after `ExportDialog` and `VideoExportDialog` accept |

All callbacks are individually try/except wrapped. A buggy plugin can
log errors but cannot crash the player.

### New — Plugins menu

A `Plugins` menu is added to the menubar (before Help). Contents are
built dynamically:
- Plugin-registered menu items, if any
- `Open User Plugins Folder…` — opens the dir in Explorer
- `Reload Plugins` — useful during plugin development
- `About Plugin API…` — quick-reference dialog with usage example

### New — licence gate

New `plugins` feature key added to `LICENSE_FEATURES`. Without a valid
licence:
- Plugin directories are not scanned (no code is executed)
- The Plugins menu shows a single disabled "Plugins require a licence"
  item plus a "Buy / Manage Licence…" link
- `Reload Plugins` shows the standard licence dialog

### New — CLI safety flag

```
--no-plugins   Disable plugin loading for this session.
```

Useful when a misbehaving plugin would otherwise prevent the player from
starting. Sets the in-memory prefs flag without altering the saved file.

### New — shipped example plugin

`example_logger.py` demonstrates the full API surface:
- Throttled `on_frame_change` (1 Hz cap)
- `on_open_file` + `on_export_complete` notifications
- Two menu items (`Show Player State`, `Jump to Middle Frame`)
- `api.log` / `api.notify` usage
- Plugin registration block at module bottom

Ships alongside the executable; users can drop it in their user plugins
folder as a starting template.

### Architectural notes

**Nuitka onedir compatibility.** Per the build skill, the `plugins/` folder
lives next to the exe (NOT inside `_internal/`) so users can drop files
in without invalidating signatures or breaking the bundle. User-level
`~/.mh_player/plugins/` is unaffected by Nuitka entirely.

**Plugin reload model.** Reload imports plugin files fresh via
`importlib.util.spec_from_file_location` — does NOT use `importlib.reload`
because plugin globals may have registered callbacks that need to be
forgotten and re-registered cleanly. Plugins should be reload-tolerant
(no side effects in module init beyond registration).

**Performance budget.** `frame_change` fires up to 60+ Hz during playback.
Plugins SHOULD throttle (the example uses 1 Hz). The plugin host adds
~1 µs per registered callback per frame (negligible) but callback bodies
themselves are user code and can be arbitrarily slow.

### Test plan

1. With no licence: open the player → Plugins menu shows disabled "Plugins
   require a licence" → no `~/.mh_player/plugins/` activity
2. With a valid licence: drop `example_logger.py` into the user plugins
   folder → restart → status bar reports "Plugins: loaded 1 — example_logger"
3. Open a sequence → `example_logger` notifies and logs
4. Plugins → Example: Show Player State → state appears in console
5. Plugins → Example: Jump to Middle Frame → playhead jumps
6. Edit the plugin, save, Plugins → Reload Plugins → new behaviour active
7. Plugins → Open User Plugins Folder → Explorer opens at the right path
8. Run `mh_PLAYer.exe --no-plugins` → plugins not loaded this session
9. Drop a deliberately-broken `.py` (e.g. with a syntax error) →
   error logged to console, player still launches normally

---

## v2.10.2 — 2026
**Stack / grid compare  —  N-way comparison extending Input Buffers**

Third feature of the v2.10 cycle. Extends the A/B compare primitive to 2 or 4
cells in a grid, using the existing Input Buffers (1–9) as natural cell sources.

### New — three stack layouts

| Mode | Layout         | Cell count |
|---|---|---|
| `2x1` | side-by-side  | 2 |
| `1x2` | top / bottom  | 2 |
| `2x2` | four-up grid  | 4 |

Cells are letterboxed inside their region, preserving each source's aspect ratio.
A 2-pixel black separator divides cells.

### New — buffer-driven cell sources

When stack mode is entered, cells auto-populate from the first N filled
buffers (1, 2, 3, 4 in order). Each cell carries a top-left badge showing its
buffer id (e.g., `B1`, `B3`).

Right-clicking a cell shows a context menu listing all currently-stored buffers.
Selecting one reassigns the cell. The menu also offers `Clear Cell` and
`Exit Stack Mode`.

### New — per-cell rendering with buffer's own display state

Each cell renders using **its buffer's** stored EV, gamma, display mode,
layer, AOV, and channel — independent of the main view. This makes the
feature genuinely useful for cross-version review where buffers were captured
with different settings.

L2 cache is keyed by `(path, layer, aov, ch, proxy, ev, gamma, dmode)`, so
cells benefit from cache hits when settings match. On L2 miss the cell
decodes on demand via a new `_render_cell_arr8()` helper that mirrors
`raw_to_display` / `raster_to_display` with the buffer's parameters.

To keep playback fluid, cell decode-on-demand is skipped during fast playback
(L2 hits still render fine); the cell rejoins on the next L2 hit or pause.

### New — frame-loop integration

`_update_stack_cells()` is called from `_update_histogram()` right after the
onion-skin refresh, so cells track the playhead during playback. Helper
early-returns when stack mode is off, so cost when disabled is ~zero.

### New — cell-aware pixel inspector

In stack mode, hovering over a cell:
- Highlights that cell with an accent-coloured border
- Maps the cursor to image-space within that cell's letterboxed image
- Emits `pixel_hovered` for the cell's underlying arr8 → the pixel inspector
  sidebar reads from the *hovered* cell, not the main frame

### Architectural — mutual exclusion with A/B compare

Entering stack mode auto-disables A/B compare (and vice-versa). The two
features answer the same question (compare N images) and combining them
would be visually incoherent.

Annotations and HUD overlays are skipped in stack mode — they're per-shot
artefacts that don't apply across multiple cells. They return as soon as
stack mode is turned off.

### Licence gate

Stack mode is gated behind the existing `ab_compare` feature key. The
licence-system mapping is now:

| Key | Unlocks |
|---|---|
| `ab_compare` | A/B compare, diff mode, anaglyph, **stack / grid (v2.10.2)** |

No new licence key was needed; this is a natural extension of N-way compare.

### View menu

- View → Stack / Grid Compare → Off
- View → Stack / Grid Compare → 2×1 (side by side)
- View → Stack / Grid Compare → 1×2 (top / bottom)
- View → Stack / Grid Compare → 2×2 (four-up)

The submenu items behave as a radio group via `_refresh_stack_menu_checks()`.

### Test plan

1. Load shot A → `Ctrl+1` to store buffer 1
2. Load shot B → `Ctrl+2` to store buffer 2
3. View → Stack / Grid Compare → 2×1 → see both side-by-side, badges B1 / B2
4. Right-click left cell → menu lists B1, B2; select B2 → cell switches
5. Hover any cell → pixel inspector reads from that cell
6. Press play → cells advance together with playhead
7. Switch to 2×2 → first 2 cells filled, last 2 empty (hint text)
8. Store buffers 3 and 4, switch back to 2×2 → all four filled
9. View → Stack / Grid Compare → Off → returns to normal single-frame view

---

## v2.10.1 — 2026
**Onion-skinning  +  header badge auto-tracks VERSION**

Second feature of the v2.10 cycle. Canvas-only change, no cache impact, no
licence gating — straightforward animation-review utility.

### New — onion-skin / ghost overlay

Past and future frames drawn as semi-transparent overlays on the current frame.
Pulled from the **L2 RAM cache only** — frames not currently resident are
silently skipped, so the feature never blocks on decode. Cheap enough to run
unconditionally on every successful frame display.

**Controls:**

- `_onion_enabled` — master on/off
- `_onion_back` — 0–5 past frames to ghost (default 2)
- `_onion_fwd`  — 0–3 future frames to ghost (default 0)
- `_onion_alpha` — base opacity (0.15 – 0.65; default 0.30)
- `_onion_tint`  — red-past / blue-future colour cue (default ON)

**Canvas integration (`ImageCanvas.set_onion_frames()`):**

Accepts a list of `(QPixmap, alpha, tint)` tuples. Painted in `paintEvent`
AFTER the current pixmap in the normal single-frame path. Skipped when A/B
compare, anaglyph, or diff modes are active to avoid visual confusion.

Tint, when used, is applied via an offscreen pixmap with
`CompositionMode_SourceIn` so the tint only affects pixels where the ghost has
coverage — transparent / black areas stay untinted.

**Ghost-fetch helper (`_fetch_ghost_arr8`):**

Works transparently for both image sequences and video files. For video,
uses `_vcache_path(idx)` to compose the L2 key. For sequences, uses the
indexed path. Bounds-checked; returns `None` for out-of-range or cache miss.

**Frame-update integration:**

`_update_onion_frames()` is called from `_update_histogram()` BEFORE the
high-FPS early-return so ghosts shift with the playhead during playback.
Helper early-returns when disabled, so the cost when off is ~zero.

**Cache-aware behaviour:**

Because ghosts come from L2 only, any cache invalidation (EV change, source
LUT change, etc.) automatically reflects in the ghosts on the next frame
display. No special bookkeeping needed.

### New — View → Onion Skin submenu

- Enable Onion Skin (`Shift+O`)
- Red/Blue Tint toggle
- Past Frames: Off / 1 / 2 / 3 / 4 / 5
- Future Frames: Off / 1 / 2 / 3
- Opacity: 15% / 25% / 35% / 50% / 65%

All states are persisted in workspace prefs and restored on launch.

### Fixed — stale `v2.9.1` version badge

The `HeaderStrip` widget hardcoded its version label, which had drifted across
multiple version bumps (still read `v2.9.1` deep into the 2.9.x cycle).
Constructor now takes a `version: str` parameter and the call site passes
`self.VERSION`, so the badge auto-tracks the class constant on every release.
This eliminates a recurring class of release-hygiene bug.

### Architecture rule (informal)

The "L1 preserved, L2 cleared, ghost from L2" pattern from v2.10.0 source-LUT
work continues to pay off: the onion-skin ghost-fetch is a pure L2 lookup
with no decode, so it can run on every frame without performance impact.

---

## v2.10.0 — 2026
**Per-source LUT  —  scene-linear look transform**

First feature of the v2.10 cycle. Opens the medium-feature roadmap with the
highest value-per-effort item: a creative/look LUT applied in scene-linear,
independent of the display transform.

### New — `_SOURCE_LUT_ENGINE` (second instance of `LUTEngine`)

A second module-level `LUTEngine` instance is introduced alongside the existing
display `_LUT_ENGINE`. The two engines play distinct roles:

| Engine               | Role            | Position in pipeline                          | Triggered by             |
|---|---|---|---|
| `_LUT_ENGINE`        | Display LUT     | replaces display transform                    | `dmode == DM_LUT`        |
| `_SOURCE_LUT_ENGINE` | Source/Look LUT | scene-linear, **before** the display transform| any time `.loaded` is True |

Both can be active at the same time. Same `.cube` / `.3dl` / `.look` parser is
shared. No new file-format work was needed.

### New — pipeline integration in all three display paths

Source LUT is applied immediately after the EV multiply and clip, before the
display-mode branch. Insertion points:

- `raw_to_display` (EXR / multi-channel float source)
- `raster_to_display` (PNG / JPG / TIFF / video frame — noted as a non-linear
  source; the LUT is still applied uniformly so behaviour is predictable)
- `_cli_apply_display` (headless `--convert` mode)

### New — CLI flag `--source-lut <path>`

```
mh_PLAYer --source-lut LMT_acescg_warm.cube  beauty.####.exr
mh_PLAYer --convert --source-lut LMT.cube --display srgb  ... -o out.mov
```

The flag works in viewer, convert, and check modes. Loaded once at startup via
`window._load_source_lut()` so the sidebar UI reflects the loaded state.

### New — SOURCE LUT sidebar section

A new `CollapsibleSection("SOURCE LUT", collapsed=True)` is placed between
PIXEL INSPECTOR and CDL / GRADE in the sidebar — mirroring data flow (source
LUT is upstream of CDL). Three controls:

- Path field (read-only, drag-and-drop accepts `.cube` / `.3dl` / `.look`)
- Browse LUT… + Clear buttons
- Status label showing dimensions, domain, title (via `LUTEngine.info`)

Three new signals on `AOVPanel`: `source_lut_browse_requested`,
`source_lut_clear_requested`, `source_lut_dropped`. Wired into PlayerApp the
same way as the display LUT.

### New — cache invalidation strategy (`_invalidate_for_source_lut`)

Load or clear of the source LUT invalidates **L2** (uint8 display-ready frames)
and the **B-source L2** (compare buffer). **L1** (raw float arrays) is
**preserved** — the LUT operates downstream of L1, so the expensive EXR decode
work is not redone. Preloaders are flushed and the current frame is refreshed
via `_show_frame(immediate=True)`.

### New — drag-and-drop handler unified

PlayerApp's `eventFilter` now handles drag-drop on both `_lut_path` and
`_src_lut_path` widgets via a shared branch. The drop target is identified by
identity comparison; the file-type filter and visual highlight are shared.

### New — prefs persistence

`source_lut_path` is saved to workspace state alongside `lut_path`. On
workspace restore, if the file still exists, the engine reloads and the
sidebar populates without a cache invalidation pass (caches were already
cleared earlier in the load path).

### Cleanup — Cineon decode comment

Comment for the DPX/Cineon decode now accurately describes a linear stretch
between Cineon reference points rather than claiming a log-to-linear decode.
A proper log curve is documented inline as planned future work.

### Architecture rule

No new architecture rule was needed for v2.10.0 — the source LUT follows the
existing `LUTEngine` + cache-invalidation patterns. The "L1 preserved on
display-only changes" principle is now used by both CDL (display-space, no
cache touched) and source LUT (scene-linear, L2 cleared only).

### Forward-looking

`LicenseType.STUDIO_PRO` and the proposed `remote_review_sync` / `edl_timeline`
feature keys are intentionally **not** added in this release — they would be
dormant constants. They will land alongside the actual feature implementations
in v2.11+.

---

## v2.9.10 (patch) — Post-v2.9.9 code cleanup
**Verification-pass cleanup — no new features, no behavioural changes for users**

Source verified by post-test inspection of v2.9.9. Four small issues addressed before
v2.10 feature work begins.

### Cleanup — dead `_maybe_invert` method removed
- v2.9.7 introduced `_apply_display_post` and sed-replaced all 9 call sites of
  `_maybe_invert`, but the method definition itself was left in place as dead code.
  Removed the orphan method body — 6 lines.

### Cleanup — broken `/info` HTTP endpoint removed
- The `/info` endpoint queued an unhandled `info_request` command and returned a
  static `{"status":"see_player"}` placeholder, which did not match the documented
  `{frame, path, playing}` contract. Removed from the handler and from the server
  docstring. Endpoint now falls through to a clean 404. Proper implementation will
  require a back-channel from the main Qt thread to the HTTP handler and is planned
  for a future release. The four working endpoints (`/ping`, `/open`, `/goto`,
  `/play`, `/stop`) are unchanged.

### Cleanup — DPX/Cineon decode comment corrected
- The decode in `load_raster_raw` for `.dpx` / `.cin` does a linear stretch between
  Cineon reference black (95/1023) and reference white (685/1023), not a true
  log-to-linear decode. The previous comment claimed "Lift out of Cineon log domain"
  which overstated the math. Comment now accurately describes the linear stretch and
  documents the proper log curve as planned future work. No runtime change.

### Cleanup — Rule #32 propagated to `CLAUDE.md`
- The QMenu chaining rule established in the v2.9.9 patch lived only in the session
  notes. Added to the Qt-Specific Rules section of the pipeline bible so it is
  available to future tool development. Includes the related `_make_action` slot guard.

### Version bump
- `VERSION` constant and `RemoteControlServer._version` both bumped to `"2.9.10"`.

---

## v2.9.9 (patch) — Three launch crash fixes
**Post-test bug fixes — no new features**

### Bug fix — `AttributeError: 'PlayerApp' has no attribute '_prefs'`
- `RemoteControlServer` auto-start check was inserted before `self._prefs = load_prefs()`.
  Reordered so `_prefs` is always loaded before any `_prefs.get()` call.

### Bug fix — `TypeError: Expected signal or callable, got "NoneType"`
- `_make_action(label, None, None)` calls `act.triggered.connect(None)` — invalid in PySide6.
  Added `if slot is not None:` guard in `_make_action`. Two call sites affected: the
  "── Presets ──" header in View → Guides and "Store Current" header in View → Input Buffers.

### Bug fix — `AttributeError: 'NoneType' has no attribute 'setEnabled'`
- `QMenu.addAction(QAction)` returns `None` in PySide6 (void return). Chaining
  `.setEnabled(False)` on the return value crashes. Fixed by storing the action first,
  then adding it, then calling `.setEnabled(False)` on the stored reference.
- **Rule #32 established:** `QMenu.addAction(QAction)` → `None` in PySide6. Never chain.
  The string overload `addAction(str)` is exempt — it returns the created `QAction`.

---

## v2.9.9 — 2026
**Remote-control protocol (HTTP)**

### New — Remote Control Server  (Help → Remote Control)
- `RemoteControlServer` — minimal `http.server.HTTPServer` on `127.0.0.1:7979` (default),
  running in a daemon thread. Commands posted to a `queue.SimpleQueue` and drained every
  50 ms on the main thread via a `QTimer` poll — no Qt objects accessed from the thread.
- Endpoints: `GET /ping` (version JSON), `/open?path=...`, `/goto?frame=N`,
  `/play`, `/stop`, `/info` (placeholder).
- `_poll_remote_control()` dispatches: `open` → `_open_file(path)`; `goto` → finds nearest
  frame index in `_frame_numbers`, clamps to in/out, calls `_show_frame(immediate=True)`;
  `play` / `stop` → `_set_playing`.
- Wired: Help menu "Remote Control (start server)" checkable action → `_toggle_remote_control`.
  Auto-start on launch if `prefs['remote_control_enabled']`. Stopped in `closeEvent`.
- Port 7979 chosen to not conflict with Remote Review Server (8765).
- Nuke integration: `import urllib.request; urllib.request.urlopen("http://localhost:7979/goto?frame=1024")`
- Shell: `curl "http://localhost:7979/open?path=/shots/sh010/beauty.%04d.exr"`

---

## v2.9.8 — 2026
**Burn-in template wired · DPX / Cineon source decode**

### New — Burn-in template now applied in VideoExportWorker
- `_apply_burnin_template(arr8, template, position, font_size, font_path, ...)` —
  new module-level function. Expands tokens via `str.format_map`: `{frame}`, `{tc}`,
  `{shot}`, `{fps}`, `{res}`, `{date}`, `{project}`. Falls back to the raw string on
  expansion failure. Draws at any of 6 positions (Bottom/Top × Left/Centre/Right) using
  PIL `ImageDraw.text` with a 1 px drop shadow.
- Wired in `VideoExportWorker._write_frames`: applied on top of the checkbox burn-in
  when `bi_template` is non-empty and the template checkbox is checked.
- The dialog UI (`_bi_tpl_cb`, `_bi_tpl_edit`, `_bi_tpl_pos`, `_bi_tpl_font`) already
  existed — this release makes it functional.

### New — DPX / Cineon source decode
- `load_raster_raw` gains a `.dpx` / `.cin` branch. Uses `cv2.imread` with
  `IMREAD_ANYDEPTH | IMREAD_COLOR` (BGR→RGB), handles uint16 data by normalising
  against Cineon reference black (95/1023) and reference white (685/1023) to produce
  a display-normalised uint8 array. Proxy sub-sampling, channel extraction, and
  per-channel solo views all apply identically to other raster formats.
- `.dpx` and `.cin` were already present in all file-dialog filter strings — the decode
  path was the only missing piece.

---

## v2.9.7 — 2026
**CDL grade node**

### New — CDL / Grade sidebar section
- `CDLGrade` class (`__slots__`, `is_identity`, `to_cc_xml`, `from_cc_xml`) — holds
  slope/offset/power per R/G/B channel and saturation. All values default to identity.
- `_apply_cdl_display(arr8, cdl) → np.ndarray` — SOP in 0-1 float (clip after each
  channel's slope+offset, power applied only if ≠ 1.0), BT.709 saturation, clip + uint8.
  Short-circuits immediately via `cdl.is_identity()`.
- `_apply_display_post(arr8)` replaces `_maybe_invert` at all call sites — applies
  invert → CDL → flip H → flip V in one pass. Fixes a latent bug where flip H/V was not
  re-applied after `_maybe_invert` returned early on non-invert frames.
- **Sidebar CDL/GRADE section** (collapsed by default, between PIXEL INSPECTOR and
  COMPARE): 3×3 grid of `QDoubleSpinBox` (Slope/Offset/Power × R/G/B) + saturation
  spinbox. Import .cc…, Export .cc…, Reset buttons.
- ASC-CDL I/O: `to_cc_xml(shot_id)` exports `<ColorCorrection>` XML; `from_cc_xml(text)`
  parses both bare and wrapped formats via `xml.etree.ElementTree`.
- `PlayerApp._cdl: CDLGrade` reset to identity on new sequence/video load and via
  CDL Reset button. Import pushes values to spinboxes without triggering re-parse.
- CDL is **display-space only** (applied to the uint8 arr8 after the full colour pipeline).
  Intentional: review-tool approximation without cache invalidation.

---

## v2.9.6 — 2026
**Ping-pong loop**

### New — Ping-Pong Loop  (Playback → Ping-Pong Loop)
- `_ping_pong: bool = False` on `PlayerApp`. Checkable action in Playback menu;
  toggled by `_toggle_ping_pong()`.
- In `_play_tick`: ping-pong check runs before the normal loop/stop logic. When going
  forward and `nxt > _out_point` → calls `_set_reverse(True)`, advances from
  `_out_point - effective_stride`. When going reverse and `nxt < _in_point` →
  `_set_reverse(False)`, advances from `_in_point + effective_stride`. Audio not
  restarted on bounce (unlike normal loop-wrap).

## v2.9.5 — 2026
**Contact Sheet export · Persistent guide presets**

### New — Export Contact Sheet  (File → Export Contact Sheet…)
- `ContactSheetDialog(QDialog)` — composites evenly-sampled frames into a single PNG grid.
- Inputs: frame range (In/Out spinboxes), max frames (default 36), columns (default 6),
  thumbnail width (default 320 px), frame-number overlay checkbox, filename header checkbox,
  output path with file picker.
- Frame fetch uses `_fetch(idx)` closure: L2 RAM cache → EXR decode → raster decode →
  canvas fallback. Same chain as Quick Save.
- Compositing runs via PIL in the dialog thread (L2 hits are fast; no worker thread needed).
  Progress bar updates per frame with `processEvents()`.
- Licensed behind `export_frames` feature gate.
- `_open_contact_sheet()` on `PlayerApp`; wired to File menu (no shortcut — dialog provides one).

### New — Persistent Guide Presets  (View → Guides → Presets)
- Three built-in presets: **Title Safe** (`safe`), **Rule of Thirds** (`thirds`),
  **Golden + Thirds** (`golden`, `thirds`). Applied via `_apply_guide_preset(keys)`.
- **Save Current as Preset…** — `QInputDialog` for name; stored in `prefs['guide_presets']`
  as `{name: [key_list]}`. Saved immediately with `save_prefs`.
- **Delete a Saved Preset…** — `QInputDialog.getItem` picks from saved names; removes entry.
- **Saved Presets** sub-menu rebuilt by `_refresh_guide_preset_menu()` on every save/delete
  and on startup.

---

## v2.9.4 — 2026
**Input Buffers (1–9 slots)**

### New — Input Buffers  (`Ctrl+1–9` store · `Alt+1–9` recall)
- `_buffers: Dict[int, Optional[dict]]` and `_active_buffer: int` on `PlayerApp`.
- Each slot stores: sequence paths, frame numbers, pad, frame index, in/out points,
  channel map, layer, AOV, channel, FPS, EV, gamma, dmode, and a short label.
- `_store_buffer(n)` (`Ctrl+N`): snapshots the current state into slot N.
- `_recall_buffer(n)` (`Alt+N`): restores state from slot N — stops playback, re-points
  all state variables, calls `populate_aov_tree`, `timeline.set_sequence`, `_show_frame(immediate=True)`.
  L2 cache misses are handled gracefully by the existing decode fallback chain.
- View → **Input Buffers** sub-menu shows each slot's label + frame count; active slot bold.
  Refreshed by `_update_buffer_menu()` after every store/recall.
- `_clear_all_buffers()` wipes all slots.

---

## v2.9.3 — 2026
**J-K-L shuttle · FPS override with reset · Every-Frame mode**

### New — J-K-L Variable-Speed Shuttle
- Speed tiers: `[1×, 2×, 4×, 8×]`. J/L while already playing in that direction cycles
  to the next faster tier. J while playing forward (or L while playing reverse) switches
  direction at 1×. K stops and resets speed to 1×.
- `_shuttle_press_j()`, `_shuttle_press_l()`, `_shuttle_stop()` replace the former
  `_play_reverse()` / `_set_playing_forward()` (retained as thin legacy delegates).
- `_play_tick` adapts: speeds ≥ 1× advance `round(stride × speed)` frames per tick at
  normal timer cadence; speeds < 1× stretch the timer interval for genuine slow-motion.
- `_shuttle_lbl` label in transport bar shows speed multiplier + direction arrow in amber
  when speed ≠ 1× (`"4× ▶▶"`); hidden at 1×.

### New — FPS Override  (FPS spinbox in transport bar)
- FPS spinbox goes amber + bold when its value differs from the file's native rate
  (`_fps_native`, stored on file open). Tooltip updates to show both values.
- Right-click on FPS spinbox → `fps_override_reset` signal → `_reset_fps_to_native()`.
  Restores `_fps`, `_native_fps`, and audio engine fps; clears amber tint via `set_fps()`.
- `TransportBar._sync_fps_tint(fps)` connected to `fps_changed` to tint on every spinbox change.

### New — Every-Frame mode  ("EF" button in transport bar)
- `_every_frame: bool` on `PlayerApp`. "EF" toggle button (green when active).
- When on: `_play_tick` skips the monotonic clock check entirely; `_next_frame_time` is
  not updated. Plays as fast as the L2 cache can deliver frames — guaranteed no skipped frames.
  Useful for slow EXR sequences where every frame must be seen; audio sync is disabled in this mode.

---

## v2.9.2 — 2026
**Region Colour Sampling · EXR Header Inspector enhancements · Zoom-to-Region bug fix**

### Bug fix — Zoom to Region (`Shift+drag`) was silently broken
- `_zr_start` was declared in `ImageCanvas.__init__` but the `Shift+LeftButton` trigger
  in `mousePressEvent` was missing — the rubber band could never start.
  Added before the pan handler check (consistent with the session notes spec).

### New — Region / Marquee Colour Sampling  (`Ctrl+drag`)
- `_rs_start` / `_rs_end: Optional[QPoint]` on `ImageCanvas`. `region_sampled = Signal(int,int,int,int)`.
- `Ctrl+LeftButton+drag` draws a **cyan dashed rubber band** (distinct from amber zoom-to-region).
  On release, `_commit_region_sample()` emits image-space coords.
- `_on_region_sampled(ix1,iy1,ix2,iy2)` on `PlayerApp`: crops the linear float array
  (EXR `_float_data`) or uint8 display array, runs numpy `min/max/mean` per channel +
  BT.709 luma. Results passed to `RegionSampler.set_region()`.
- **`RegionSampler(QWidget)`** — new class below `PixelInspector` in PIXEL INSPECTOR sidebar
  section. Hidden until first sample. Shows `R / G / B / Luma` with `min / max / mean` columns,
  region coordinates and size. **⧉ Copy** (tab-separated plain text) · **✕ Clear**.
- Sidebar auto-expands + scrolls to PIXEL section on sample. Status bar shows one-line mean summary.
- Cleared on new sequence/video load.

### Enhanced — EXR Header Inspector  (`Ctrl+I`)
- **Data window** and **display window** shown separately. Offset from origin is flagged;
  match with display window is noted.
- **Pixel aspect ratio** shown with `(square)` annotation when exactly 1.0.
- **Compression** decoded from int → name (NO_COMPRESSION / RLE / ZIPS / ZIP / PIZ /
  PXR24 / B44 / B44A / DWAA / DWAB).
- **Per-channel pixel type**: each channel row shows HALF / FLOAT / UINT and subsampling
  if non-1×1. Channel count shown as a separate row.
- **Video files** now handled: resolution, fps, duration, frame count, video codec,
  pixel format, audio codec + sample rate via `probe_video`.
- **📋 Copy All** button: copies all metadata rows as `Key:\tValue` plain text.
- Dialog resized 560×380 → 640×480.

### Fix — `_flip_h`, `_flip_v`, `_bookmarks` explicit init in `PlayerApp.__init__`
- Were previously lazy-initialised (set on first toggle). Added explicit declarations
  to prevent AttributeError on cold paths before first interaction.

---

## v2.9.1 — 2026
**Flip H/V · Copy frame to clipboard · Master audio volume · Channel/Matte export in both dialogs**

### New — Flip Horizontal / Flip Vertical  (`H` / `Shift+H`)
- `_flip_h` / `_flip_v` state on `PlayerApp`. `_maybe_invert()` extended to also apply
  `np.ascontiguousarray(arr8[:, ::-1])` (H) and `arr8[::-1]` (V) after inversion.
  Display-only — no cache impact, zero decode overhead.
- Both added to View menu as checkable `ApplicationShortcut` actions alongside Invert Display.
- Listed in the two-column keyboard shortcuts dialog.

### New — Copy Frame Image to Clipboard  (`Ctrl+Alt+C`)
- `_copy_frame_image()` reads `self._canvas._arr8` (colour transform, invert, and flips
  already applied), converts via `ndarray_to_qimage()`, calls `QApplication.clipboard().setImage()`.
- Added to View menu as "Copy Frame Image". `Ctrl+C` continues to copy the file path.
- Paste directly into Slack, email, Photoshop, Nuke viewer, etc. — no temp file required.

### New — Master Audio Volume  (sidebar AUDIO section)
- `AudioEngine._master_volume: float = 1.0`. `set_master_volume(vol)` method.
- Applied in the `_callback` inner function: `chunk * engine._master_volume` — live effect,
  no stream restart needed.
- `_audio_volume` QSpinBox (0–100 %, default 100) added to sidebar AUDIO section above
  "Scrub audio". Wired: `valueChanged → set_master_volume(v / 100.0)`.

### New — Channel / Matte Export  (Export Frames + Export Video dialogs)
New **CHANNEL / MATTE** section added to both `ExportDialog` and `VideoExportDialog`:

- **AOV combo** — "Current" or any AOV from the channel map. Hidden for raster/video
  sources. Lets you export the normals pass, depth, cryptomatte etc. in one step.
- **Channel combo** — Current (mirrors live viewer) / Full Colour (RGB) / R / G / B /
  A (greyscale) / Luma (Y′ BT.709). Single-channel outputs are written as greyscale
  (R=G=B=channel value) so they open correctly everywhere.
- **Commit display transforms** checkbox — Off (default): raw float data as stored in
  the file. On: bakes the active EV, gamma, and display mode into the exported values —
  exports exactly what is on screen. Useful for extracting an inverted alpha, a graded
  beauty, or a tone-mapped HDR frame without opening Nuke or After Effects.

`ExportWorker`:
- New params `commit_transforms: bool` and `export_channel: str`.
- `_channel_to_greyscale(arr8, ch)` static method: R/G/B/A or Luma → `np.stack([g,g,g])`.
- EXR + commit=False path unchanged (raw float). EXR + commit=True: display transform baked.
- Greyscale applied after arr8 is ready, before `_write_raster`.

`VideoExportWorker._write_frames`:
- Reads `commit_transforms` and `export_channel` from cfg dict.
- Effective EV/gamma/dmode resolved from commit flag before `_fetch`.
- Greyscale via shared `ExportWorker._channel_to_greyscale()`.


### New — False Colour display mode  (`Ctrl+Shift+F`)
- `DM_FALSECOLOR = 'falsecolor'` constant alongside other DM constants.
- `_FC_STOPS` — 12-point ramp: black → blue → cyan → **green (18% middle grey)**
  → yellow → orange → red → magenta → white (hard clip at 4.0 linear).
- `_FC_LUT` — 4096-entry uint8 lookup table built once at import. Zero runtime cost.
- `_apply_falsecolor(rgb)` — BT.709 luminance Y → LUT index → HxWx3 uint8.
- Plugged into `raw_to_display()` (early return) and `raster_to_display()` (de-gammas
  uint8 with 2.2 power first). EV shifts the ramp relative to 18% middle grey.
- Sidebar radio, transport bar combo entry, View menu checkable `_fc_action`
  (`ApplicationShortcut`). `_toggle_falsecolor()` toggles DM_FALSECOLOR ↔ default.

### New — Zoom to Region  (`Shift+drag`)
- `_zr_start` / `_zr_end: Optional[QPoint]` on `ImageCanvas`.
- `Shift+LeftButton` (not annotation mode) starts rubber band; move updates end
  point; release calls `_commit_zoom_region()`.
- `_commit_zoom_region(p1, p2)`: screen rect → image coords →
  `new_zoom = min(cw/img_w, ch/img_h) * 0.95`, centred pan. Ignores rects < 5 px.
- `paintEvent`: amber dashed rubber band drawn before `p.end()`.

### New — Frame Bookmarks / Markers  (`M` · `Ctrl+M` · `Ctrl+,` / `Ctrl+.`)
- `self._bookmarks: Dict[int, str]` on `PlayerApp` — frame index → label.
- `Timeline.set_bookmarks(bm)`: repaints amber diamond ticks (⬧) at bookmarked frames
  using `QPolygon`. Above/below the track bar, on top of In/Out fill.
- `_toggle_bookmark()` (`M`): adds unlabelled bookmark or removes existing.
- `_set_bookmark_label()` (`Ctrl+M`): `QInputDialog` pre-filled with existing label.
- `_jump_bookmark(dir)` (`Ctrl+,` / `Ctrl+.`): sorted index list, wraps.
- `_clear_all_bookmarks()`: View menu only.
- Workspace: `{'bookmarks': {'1024': 'Hero pose', ...}}` — string keys, restored as
  `int`. Cleared on new file open.

### Bug fix — `_audio_volume` AttributeError on launch
- Volume spinbox patch silently failed (tooltip text mismatch in anchor string).
  Applied correctly to the `# Scrub audio toggle` block in `AOVPanel`.

### Build fix — bat exits before Nuitka if injector script missing
- Injector check moved from pre-Nuitka to step 4/5, downgraded from hard exit to
  `[WARN]` with `goto skip_inject`. Build always runs. Added `--include-package=pynvml`.

## v2.9.0 — 2026
**Multi-threaded caching & decode rewrite · single-pass EXR/AOV decode · scope render rewrite · pixel inspector & workspace fixes**

### Performance — parallel preloader + single-pass EXR decode
- **SequentialPreloader rewritten as a thread pool** — centre-out priority
  queue, auto-sized to `min(cpu_count - 2, 8)`. EXR / raster frames decode in
  parallel; video stays serialized behind a shared decoder + lock. Public API
  (`configure` / `schedule` / `schedule_all` / `flush` / `stop`) unchanged.
- **`load_exr_raw`** now reads every channel in a single `InputFile.channels()`
  call (one decompression pass) instead of channel-by-channel.
- **`load_exr_all_aovs()`** — decodes every AOV of the target layers from a
  single file open. The preloader optionally preloads sibling AOVs in the
  background so AOV switches are instant.
- **AOV / channel switches no longer clear L1/L2** (`_switch_aov_keep_cache`) —
  cache keys already encode layer + aov + channel, so a previously-decoded or
  sibling-preloaded pass is an instant hit instead of a full disk re-read.
- **New PERFORMANCE preferences** — decode thread count (0 = auto), preload
  sibling AOVs (default on), preload all layers (default off). Applied on next
  launch. B-source preloader now stopped cleanly on close.

### Open Sequence — EXR proxy caveat note
- Opening an EXR sequence with a proxy level selected now shows a note that
  proxy lowers RAM and display cost **but not load time** — OpenEXR has no
  subsampled read, so the full file is still decompressed from disk. Shown only
  for EXR sources while a proxy level is selected.

### Pixel Inspector — swatch now matches the image
- The colour swatch + hex previously applied a fixed `1/2.2` gamma to the
  linear value, ignoring the active display transform (sRGB / ACES / OCIO /
  LUT), the gamma slider and EV — and double-gamma'd already-encoded video — so
  the swatch read much lighter than the image.
- Swatch + hex now sample the **actual on-screen display pixel**, matching the
  image exactly under any display mode or exposure. The numeric R/G/B readout
  still reports scene-linear values.

### Scopes — vectorized parade / overlay / luma render
- Replaced the per-point `drawPoint` renderer with a single **vectorized RGBA
  buffer blitted once**. Fixes the progressive R→G→B parade reflow (now drawn
  correctly from scratch), the overlay / luma right-edge clip and axis
  transpose, and thin single-level traces that could drop out.
- Trace brightness lifted via a tunable gamma + per-mode gain
  (`_TRACE_GAMMA` / `_GAIN_OVERLAY` / `_GAIN_PARADE` / `_GAIN_LUMA`) — Overlay
  raised most (it was the most knocked-back), parade and luma a little.

### Workspace — reload caching fix
- `_load_workspace` opened the sequence (configuring the preloader for the
  default frame + default display settings) and only then restored frame / EV /
  gamma / dmode / channel — without re-syncing. The background cache filled
  with the wrong params on the wrong frame and a manual purge didn't help.
  Reload now does a full cache rebuild around the restored frame with the
  restored display settings.

### Preferences — mouse-wheel scroll guard on combos
- The display-mode and audio input / output device combo boxes now use
  `ScrollGuardCombo`, so scrolling the preferences page with the wheel no longer
  cycles the selected device (matches the sidebar fix).

## v2.8.21 — 2026
**Stereo / Anaglyph · Aspect Ratio · Remote Review rewrite · Shortcut manager rewrite · GUI polish**

### Manage Shortcuts — complete rewrite (VBScript via cscript)
The previous implementation went through six failed approaches across two sessions
before root-cause analysis (via Opus) identified two independent bugs:

- **Bug 1:** `SHGetKnownFolderPath` ctypes wrapper swallowed all exceptions in
  the Nuitka frozen runtime, silently falling back to `expanduser("~") / "Desktop"` —
  a path that does not exist on OneDrive-redirected machines. `mkdir(parents=True)`
  created it silently, writing the file to the wrong location.
- **Bug 2:** The MS-SHLLINK binary `.lnk` writer declared `VolumeIDSize = 16`,
  violating §2.3.1 ("MUST be greater than 0x10"). Windows Explorer silently
  rejected the resulting files.

**Replacement — VBScript via `cscript`:**
- `_sc_vbs_escape(s)` — doubles `"` for VBScript string literal safety
- `_sc_run_vbs(body, timeout)` — writes UTF-16-LE/BOM `.vbs` to `%TEMP%`,
  runs `cscript //Nologo //B`, parses `KEY=VALUE` lines from a result file;
  `DEVNULL` stdio + `CREATE_NO_WINDOW` (Rule 21)
- `_sc_resolve_paths()` — resolves Desktop and Programs paths inside VBScript
  via `WshShell.SpecialFolders` — the authoritative OneDrive-aware source
- `_sc_write_lnk(...)` — `CreateShortcut`/`Save` with `On Error Resume Next`
  capturing every error stage; **verifies `.lnk` exists on disk before
  reporting success** — false positives now impossible
- `_sc_write_lnk_ps(...)` — PowerShell fallback if VBS path fails
- Desktop and Programs paths resolved once on dialog open and cached as
  `self._desktop_dir` / `self._programs_dir` — create and remove use the same source
- `.vbs` written as UTF-16-LE with `\xff\xfe` BOM — only encoding `cscript`
  reliably handles across all Windows versions

**Legacy helpers removed:** `_sc_get_known_folder`, `_sc_get_desktop`,
`_sc_get_start_menu`, `_sc_create_lnk`, `_sc_release_com`, `_sc_create_lnk_ps`

**Note on VBScript deprecation:** Phase 1 (enabled by default) runs through
~2026/2027. When Phase 2 hits (disabled by default), swap primary/fallback:
make PowerShell primary and VBScript the fallback.

### Other post-release fixes
- **Waveform/timeline playhead alignment** — `WaveformStrip.paintEvent` playhead
  X corrected to use `MX=10` margin matching `Timeline.paintEvent`
- **License popup** — "or visit" → "or contact me at" for the
  `anti-matter-3d.com/contact` link in both `require()` and `_show_license_info()`
- **Build bat** — `xcopy /E /I /Y _internal` step added between FFmpeg copy and
  VERSIONINFO injection; eliminates need to copy `_internal` manually after build

### Stereo / Anaglyph — CLI headless convert

### CLI — Aspect ratio correction (`--ar`)
- `--ar RATIO` — resize output frames to the correct display geometry before saving
  (e.g. `--ar 2.39` for 2.39:1 Scope, `--ar 1.85` for Flat, `--ar 1.778` for 16:9 HD)
- Works with all output formats (PNG, TIFF, EXR, MP4, MOV) and with `--anaglyph`
- `_cli_ar_resize(rgb, ar)` helper — pure PIL LANCZOS resize on float32 arrays
- Applied in: `_cli_convert` image loop · `_cli_encode_video` frame prep ·
  `_cli_convert_anaglyph` both branches

### License gates — Anaglyph is a free feature
- `_load_b_source(free=False)` — new `free` param; when `True` skips `ab_compare`
  license check. `_ag_load_r_btn` now passes `free=True`.
- `_load_stereo_exr()` — license check removed entirely
- Both anaglyph load paths are now available without a license

### CLI — Anaglyph headless convert (`--anaglyph`)
New headless pipeline for compositing stereo sequences into anaglyph output directly from the terminal — no GUI required.

**New arguments:**
- `--anaglyph` — enable anaglyph compositing in `--convert` mode
- `--anaglyph-mode MODE` — `rc_half` (Red-Cyan half-color, default) · `rc_grey` (Red-Cyan greyscale) · `ab_half` (Amber-Blue) · `gm_half` (Green-Magenta)
- `--right-eye FILE` — right-eye sequence or video (two-sequence mode; left eye = `INPUT`)
- `--stereo-exr` — `INPUT` is a single multi-camera EXR containing both L+R layers; auto-detected via `_detect_stereo_exr_layers()`
- `--eye-swap` — swap L/R eye assignment (invert depth)
- `--anaglyph-gamma` — linearise before channel mixing, re-gamma after (reduces colour fringing on linear-light EXRs)

**New helper functions:**
- `_cli_anaglyph_composite(left, right, mode_key, swap, gamma)` — pure numpy float32 compositor; identical logic to `ImageCanvas._compute_anaglyph_pixmap()` for consistency
- `_cli_convert_anaglyph(...)` — full headless pipeline: frame loading, compositing, image-sequence or video output via existing FFmpeg path

**Example usage:**
```
mh_PLAYer --convert --anaglyph left.####.exr --right-eye right.####.exr anaglyph.mp4
mh_PLAYer --convert --anaglyph --stereo-exr camera_stereo.####.exr out.mp4
mh_PLAYer --convert --anaglyph --anaglyph-mode rc_grey --eye-swap left.####.exr --right-eye right.####.exr review.####.png
```

### Export — Video trim fast path + image-sequence In/Out fix

**Fix 1 — Video → video export (silent failure)**
The PNG round-trip pipeline (OpenCV decode → temp PNGs → ffmpeg re-encode) failed
silently for video sources. Root cause unconfirmed inside the slow path; fixed by
bypassing it entirely for the common case via an ffmpeg direct-trim fast path.

New `VideoExportWorker._encode_video_trim()` method: invokes ffmpeg with
`-ss`/`-to` input-side seek, matching all existing codec/CRF/ProRes profile
options. Fast path taken when **all** hold: source is a video file, no
slate/burn-in/HUD/annotations, no audio buffer, no display transform to bake
(EV=0, gamma≈2.2, dmode=sRGB). Any deviation falls through to the original
slow path so overlays, audio re-mux, and display-transform baking still work.

**Fix 2 — Image-sequence export ignored In/Out trim**
`ExportDialog` (Ctrl+E) always defaulted to Full sequence regardless of whether
the user had set an In/Out trim. Default radio is now range-aware:
`_rb_inout` selected when `in_point > 0` or `out_point < last`; `_rb_all`
otherwise.

**Fix 3 — Prepend slate defaulted to ON**
`VideoExportDialog`: `_cb_slate.setChecked(True)` → `setChecked(False)`.
Slate is now opt-in.

**Diagnostic improvements**
- `VideoExportWorker._fetch()`: `VideoFileDecoder` open failure now emits
  to `log_line` (not just `print`) — visible in the dialog log in Nuitka builds
- `VideoFileDecoder.read_frame()`: added reopen-tier recovery — if seek+retry
  fails, the `cv2.VideoCapture` is released and reopened before a final
  attempt; fixes decoder-state loss on some H.264 streams

**cfg key corrections** (prior handover had wrong names):
`in_point`/`out_point` → `in_idx`/`out_idx`; `annotation` → `annotation_layer`

### Export — Audio checkbox crash + slot misalignment on arrow-key step

**`_cb_include_audio` AttributeError on every video export click**
`VideoExportDialog._start_export` referenced `self._cb_include_audio`, but the AUDIO
section that creates it had been lost from `_build()` (the sibling `ExportDialog`
still had it). Every Export Video click raised `AttributeError` before the cfg dict
was even assembled — silent on the Nuitka build, traceback in console runs.

- **AUDIO section added** to `VideoExportDialog._build()` between SLATE and OUTPUT.
  Mirrors the sibling pattern: when `self._audio_data is not None`, shows
  `"Audio loaded: NN Hz · Nch · X.Xs"` label plus an "Include audio track in export"
  checkbox (default checked). Otherwise shows "No audio loaded — video only" and
  sets `_cb_include_audio = None`.
- **Defensive `getattr`** in `_start_export`: `getattr(self, '_cb_include_audio',
  None) is not None` instead of bare attribute access. If the section ever gets
  dropped again, audio is silently disabled instead of crashing the export.

**Audio playhead drift on arrow-key stepping**
The `WaveformStrip` playhead used the correct formula (`idx / (total − 1)`, same
denominator as `Timeline`), but `_after_step()` — called by Left/Right arrows,
Home, End — updated `_timeline.set_frame` without touching the waveform or the
audio engine. Mouse-scrub, the playback loop, and the audio-reload path all do
both; arrow-key stepping was the missing entry point. Each press accumulated
offset until the cursors were visibly misaligned.

Added the same two lines `_on_timeline_scrub` already has to the bottom of
`_after_step`:
```python
self._audio.seek_to_frame(self._frame_idx)
if self._sequence:
    total = max(1, len(self._sequence) - 1)
    self._waveform.set_position(self._frame_idx / total)
```
Deliberately did not add `play_scrub_burst` — mouse-scrub keeps its burst (gated
by the scrub checkbox), arrow stepping is silent but visually correct. Also
covers Home / End since those route through `_after_step`.

### Frame Range Bar — DJV-style flanking spinboxes

New `FrameRangeBar(QWidget)` class wraps the existing `Timeline` widget with
flanking absolute-frame spinboxes and mark/clear buttons. Layout
(left → right):

```
[CURRENT] [START] [IN] [Mark-In `[`] [Clear-In `✕`] ── Timeline ── [Clear-Out `✕`] [Mark-Out `]`] [OUT] [END]
```

| Control | Behaviour |
|---|---|
| **CURRENT** | Editable. Jumps to that frame. Clamps to active In/Out range. |
| **START** | Read-only, dimmed. `frame_numbers[0]`. |
| **IN** | Editable. Drives Timeline's in-bracket. Clamps to ≤ Out. |
| **`[`** | Mark-In at current frame (same code path as the `I` shortcut). |
| **`✕`** (left) | Reset In to first frame. Granular `_clear_in_only` method on player. |
| **`✕`** (right) | Reset Out to last frame. Granular `_clear_out_only` method on player. |
| **`]`** | Mark-Out at current frame (same code path as the `O` shortcut). |
| **OUT** | Editable. Drives Timeline's out-bracket. Clamps to ≥ In. |
| **END** | Read-only, dimmed. `frame_numbers[-1]`. |

**API-compatible drop-in.** `FrameRangeBar` exposes the same public surface as
`Timeline` — `set_sequence` / `set_frame` / `set_in` / `set_out` / `in_point` /
`out_point` properties, plus the three `frame_changed` / `in_changed` /
`out_changed` signals. The 20+ existing `self._timeline.*` call sites in the
player work unchanged; the only player-side construction edit was `Timeline()` →
`FrameRangeBar(top_widget=self._waveform)`.

**Mark/Clear → player delegation.** Four new signals
(`mark_in_requested`, `mark_out_requested`, `clear_in_requested`,
`clear_out_requested`) route the bracket buttons through the player's existing
`_mark_in` / `_mark_out` methods (same handlers as the `I` / `O` shortcuts) plus
new `_clear_in_only` / `_clear_out_only` methods. Behaviour, clamping, and
status-bar feedback stay in one place.

**Spinbox UX details:**
- All values shown are absolute frame numbers (read from `_frame_numbers[idx]`),
  never sequence indices.
- `setKeyboardTracking(False)` — commits on Enter/focus-out only; typing `1234`
  doesn't fire four seek events.
- `wheelEvent` neutered — accidental scroll near the strip won't change values.
- `setAlignment(AlignRight)` + monospaced font — frame numbers right-aligned
  for column-style readability.
- In/Out cross-clamp — editing IN above OUT (or vice versa) snaps to the limit.
- `QAbstractSpinBox` added to `QtWidgets` imports.

### WaveformStrip relocation — fixes audio playhead drift past timeline edge

When the FrameRangeBar replaced the bare Timeline, `WaveformStrip` was still
sitting in the player's main vertical layout, full-width — while `Timeline` was
now flanked by the in/out spinbox groups. Two widgets with different horizontal
extents → audio playhead at the same fractional position landed at a different
X coordinate than the Timeline frame marker (most visible at the end of the
sequence, where the audio cursor projected past the timeline's right edge).

- `FrameRangeBar.__init__` accepts a `top_widget` kwarg. The central column is
  now a `QVBoxLayout` that stacks `top_widget` (if provided) above `Timeline`.
- Player constructs `WaveformStrip` first, passes it to
  `FrameRangeBar(top_widget=self._waveform)`. The standalone
  `rl.insertWidget(rl.count() - 1, self._waveform)` line removed.
- Waveform and Timeline now share identical bounds; the playhead and frame
  marker align at every frame index, including the last.

### IndexError storm — defensive bounds in `_idx_to_fn` / `_fn_to_idx`

`_idx_to_fn` used `self._total` as the bound check but accessed
`self._frame_numbers[idx]`. When the user clicks/drags Timeline before a
sequence is loaded (or in any state where `len(_frame_numbers) != _total`), the
bound check passes but the indexed access raises `IndexError` — Timeline emits
`frame_changed(0)` even with an empty sequence.

Both helpers now use `len(self._frame_numbers)` directly and clamp to a valid
index, returning a sane fallback (`self._first_fn` for empty lists). Strictly
additive; can't regress correct cases.

### Keyboard shortcuts — `I` / `O` / `V` / `[` regression after FrameRangeBar

Introducing the FrameRangeBar spinboxes exposed a pre-existing inconsistency in
how shortcuts were registered:

- `_setup_shortcuts()` registers single-key shortcuts (`R`/`G`/`B`/`J`/`K`/`L`
  etc.) with `Qt.ShortcutContext.ApplicationShortcut` — they fire regardless of
  which widget has focus.
- `_add_action()` (used for the menu-driven shortcuts `I`/`O`/`V`/`[`) used
  Qt's default `WindowShortcut` context.

Before the FrameRangeBar, no focusable widget sat in the bottom strip, so this
inconsistency was invisible. Once the spinboxes existed, a focused
`QSpinBox`'s internal `QLineEdit` consumed letter keystrokes before
`WindowShortcut` actions could see them.

**Fix:** appended `setShortcutContext(Qt.ShortcutContext.ApplicationShortcut)`
to the four affected menu actions:

| Shortcut | Action | Location |
|---|---|---|
| `I` | Mark In | playback menu, line 11986 |
| `O` | Mark Out | playback menu, line 11988 |
| `V` | Invert Display | view menu, line 11935 |
| `[` | Compare A/B (set B frame) | view menu, line 11941 |

No `_add_action` signature change. All existing menu entries, slot connections,
checkable states, Help/Manual references unchanged. The four shortcuts now fire
even when any focusable child widget has focus, matching the existing
`_setup_shortcuts` convention.

**Audited but untouched:**
- `]` — never was a shortcut; used only as the Mark-Out button label
- `Space` / `Home` / `End` — special keys, not intercepted by `QSpinBox`
- `F` / `1` — already in `_setup_shortcuts` with `ApplicationShortcut`; the
  View-menu duplicates resolve via Qt and stay as-is
- All `Ctrl+X` / `Shift+X` shortcuts — modified-key combos aren't intercepted

### Remote Review — HTTP chain-load streaming (rewritten)
- Architecture changed from 500 ms polling to **chain-load** — browser requests next
  frame the moment the current one loads; maximum unique-frame rate, zero idle time
- **960 px width cap** — frames downscaled before JPEG encode (~4× faster than full-res);
  quality 75%; typical LAN frame rate ~15–20 fps
- **Background encoder thread** (`_encoder_worker`) — JPEG encoding off the Qt main
  thread; `queue.Queue(maxsize=1)` drops stale frames automatically
- **LAN IP auto-detection** — UDP routing trick (`SOCK_DGRAM.connect("8.8.8.8")`) picks
  the correct outbound interface; ignores VirtualBox, WSL, and VPN adapters
- `update_frame` now called in `_play_tick` outside the histogram fps throttle guard —
  remote server updates at full playback speed regardless of sequence fps
- Initial frame pushed immediately on server start — browser never blank on connect
- Antenna icon (`_r2_remote`) now synced on both start AND stop paths via
  `set_remote_btn()` — icon state always matches server state
- `update_frame` also updates for video files (not just image sequences)
- `arr8[..., :3]` slice — handles RGBA sources without PIL errors
- numpy `ValueError` fix: `_canvas._arr8 or arr8` → explicit `None` check

### GUI — Icon toolbar polish
- **Info bar icon dedup** — row 1 `_btn_eye` changed from `'eye'` → `'infobar'` icon;
  corrected tooltip (removed invalid `(I)` shortcut hint); `eye`/`eye_off` dynamic swap
  removed from `_on_eye_toggle` and `set_overlay_btn`
- **Row 2 duplicate removed** — `_r2_infobar` button, `infobar_toggled` Signal,
  `set_infobar_row2_btn()` method, and both connect/sync call sites removed
- **`loop_sel` icon** — new visually distinct icon: smaller arc shifted up + horizontal
  selection bar with filled centre region and In/Out tick marks; clearly distinguishable
  from the plain `loop` icon at small sizes

### Bug fixes
- **`_open_pdf` missing def** — method header was dropped by an earlier patch; body
  was present but uncallable; header restored; `sys.frozen` path bug also fixed
- **`_show_path_manager` `sys.frozen` bug** — same `sys.executable` pattern applied;
  Nuitka does not set `sys.frozen` (PyInstaller convention only)
- All `getattr(sys, "frozen", False)` occurrences replaced with
  `Path(sys.executable).suffix.lower() == ".exe"` check

### Build
- Build bat: `xcopy /E /I /Y _internal` step added between FFmpeg and VERSIONINFO;
  eliminates the need to copy `_internal` manually after every Nuitka build

---

## v2.8.20 — 2026
**Stereo / Anaglyph — GUI viewer feature**

### Stereo / Anaglyph viewer
New `STEREO / ANAGLYPH` sidebar section (collapsed by default, between COMPARE and AUDIO).

### Module-level additions
- `ANAGLYPH_MODES` — four presets: `rc_half` · `rc_grey` · `ab_half` · `gm_half`
- `_detect_stereo_exr_layers(raw_channels)` — detects 11 common stereo naming conventions (`left/right`, `L/R`, `Camera_L/Camera_R`, `eye_left/eye_right`, `leftEye/rightEye`, etc); returns `(left_prefix, right_prefix)` or `None`

### `ImageCanvas` changes
- `_anaglyph_mode`, `_anaglyph_mode_key`, `_anaglyph_swap`, `_anaglyph_gamma` state vars
- `set_anaglyph_mode(active)` and `set_anaglyph_params(mode_key, swap, gamma)` public setters
- `_compute_anaglyph_pixmap()` — per-frame compositor with optional gamma correction; all four mode keys implemented
- `paintEvent` — anaglyph branch fires first (before diff/wipe), takes priority when `_anaglyph_mode` is True
- `clear()` resets `_anaglyph_mode`

### Sidebar STEREO / ANAGLYPH section
- Enable Anaglyph checkbox — guards against missing B source, shows informative status
- Mode combo (4 presets)
- Swap eyes + Gamma correct checkboxes
- **Load R Eye…** — calls existing `_load_b_source()` for two-sequence workflow
- **Load Stereo EXR…** — new path for single multi-camera EXR
- Status label (live feedback on what's loaded / active mode)
- `'anaglyph': ag_sec` added to `_section_anchors`

### `PlayerWindow` additions
- `_on_anaglyph_toggled()` — guards require B source before enabling
- `_on_anaglyph_params_changed()` — pushes mode/swap/gamma to canvas
- `_load_stereo_exr()` — opens EXR, auto-detects layer pair via `_detect_stereo_exr_layers`, reads L+R float16 channels, converts to display-referred uint8, auto-enables anaglyph

---

## v2.8.19 — 2026
**UX polish — fit/zoom, HUD shot name, aspect ratio, email/URL cleanup**

### Smart zoom on open
- Images smaller than the canvas now open at 100% (pixel-perfect); larger images still fit to canvas
- `ImageCanvas.fit_or_100()` — new method replacing `fit()` at all three open sites (sequence, still, video)
- `PlayerWindow._set_ev(ev)` — new helper: sets EV, syncs sidebar spinbox, does NOT write to prefs

### EV reset on new file open
- Opening a new sequence or video now resets EV to 0.0
- Only workspace restore preserves a modified EV value — prefs no longer carry EV across opens
- Prevents stale exposure from a previous session silently affecting a new clip

### HUD Shot Name — source selector
- Replaced single text field with three-way radio: **File name** (default) · **Folder** · **Custom**
- File name: strips trailing frame number pattern (`beauty_v003.1001.exr` → `beauty_v003`)
- Folder: parent directory of the sequence (previous default behaviour)
- Custom: free-text field, enabled only when Custom radio is selected
- `PlayerWindow._derive_shot_name()` — centralises resolution logic
- Custom field disabled (greyed) unless Custom radio is active

### HUD toolbar icon → sidebar anchor
- Clicking the HUD icon in the row-2 toolbar now opens the sidebar and scrolls to the
  HUD OVERLAY section (via `hud_menu.aboutToShow` → `sidebar_section_requested('hud')`)
- `'hud'` added to `AOVPanel._section_anchors`

### Aspect Ratio
- `AR_PRESETS` — 13-entry list of `(label, float)` pairs covering:
  Pixel (default) · 1:1 · 4:3 PAL/NTSC · 16:9 HD · 1.37:1 Academy · 1.43:1 IMAX 70mm ·
  1.85:1 Flat · 2:1 Univisium · 2.39:1 Scope · 2K Flat · 2K Scope · 4K Flat · 4K Scope
- `ImageCanvas._ar_ratio` — new state; `set_ar_ratio(ratio)` public setter
- `ImageCanvas._ar_display_size(pw, ph)` — computes letterboxed/pillarboxed draw dimensions
- `_do_fit()`, `fit_or_100()`, `_image_rect()`, `_canvas_to_image()` all updated to use AR-corrected display size
- `paintEvent` uses AR-corrected `dw, dh` for all draw calls
- AR combo in transport bar row 1 (`AR:` label + `ScrollGuardCombo`, 106 px wide)
- AR section in sidebar DISPLAY (collapsed by default), synced bidirectionally with transport combo
- `TransportBar.ar_changed = Signal(float)` — new signal
- `PlayerWindow._on_ar_changed(ratio, sync_transport)` — applies to canvas, syncs both combos, re-fits
- AR saved to prefs (`ar_ratio`) and workspace (`ar_ratio`); restored on startup and workspace load

### Email / URL cleanup
- License popup (feature-requires): `mailto:` → `anti-matter-3d.com/contact`
- License info / unlicensed popup: same
- About dialog, all site references: `anti-matter-3d.com/tools` → `anti-matter-3d.com/mhplayer`
- `_open_registration` mailto kept — composing the actual registration email still requires it

---

## v2.8.18 — 2026
**GUI cosmetic pass — icon toolbar, sidebar anchors, CLI PATH Setup**

### Row-2 Transport Bar — Icon Toolbar
- Replaced all text-based row-2 controls (γ label, "HUD ▾" QToolButton, "Guides…" combo,
  "Ann" QPushButton) with a fully QPainter-drawn icon toolbar
- 14 new icons added to `_draw_icon` (now `@staticmethod`): `sun`, `palette`, `hud_icon`,
  `guides`, `pencil`, `split`, `waveform`, `eyedropper`, `infobar`, `fullscreen`,
  `snapshot`, `load_b`, `antenna`, `manual`
- New `IconMenuButton(QToolButton)` class — looks identical to `IconButton` but supports
  attached `QMenu` (used for HUD and Guides popup menus)
- `snapshot` icon: lightweight frame-corner brackets + down-arrow (line art, no fill)
- `load_b` icon: two offset outlined film frames + B label (outline-only, no solid fills)
- Load B icon placed immediately before Compare icon — logical A/B workflow pairing

### Row-2 Collapse Strip
- Left-side 14 px vertical strip matching the sidebar toggle strip style
- `▼` expanded / `▲` collapsed; `row2_visible` pref saved and restored on startup

### Sidebar Anchors
- `AOVPanel.scroll_to_section(key)` — auto-expands section if collapsed, then scrolls
  `QScrollArea` to the widget via `ensureWidgetVisible` (deferred with `QTimer.singleShot`)
- Anchor keys: `'display'` · `'annotation'` · `'compare'` · `'scopes'` · `'pixel'`
- Clicking an anchor icon auto-expands the sidebar if it was collapsed

### New Toolbar Actions (row-2 icons → existing methods)
- Info bar toggle (checkable, synced) · Fullscreen (Ctrl+F) · Quick save frame (Ctrl+Shift+C)
- Load B source · Remote review server (checkable, lit when active) · User manual PDF

### Help → CLI PATH Setup…
- `_show_path_manager()`: reads/writes `HKCU\Environment\Path` via `winreg`
- User-level PATH only — no admin rights, no UAC prompt
- `WM_SETTINGCHANGE` broadcast via `ctypes` so new terminals see change immediately
- Status indicator (green ✔ / amber ✘) · Add / Remove buttons · restart-terminal warning strip
- Graceful non-Windows fallback

### Crash Fix — White Screen on Launch
- `_draw_icon` converted to `@staticmethod` — fixes silent C-level segfault caused by
  `QWidget.__new__(IconButton)` in `IconMenuButton.paintEvent` (uninitialised C++ object)
- `palette` icon: replaced `QPainterPath` (not imported) + inline `from PySide6.QtCore import`
  with `p.drawPie()` — fixes `NameError` in `paintEvent` causing blank window

### Manual — Updated Sections
- Section 3.2: CLI PATH Setup (new)
- Section 5: Interface Overview transport bar table updated
- Section 5.2: Quick-Access Icon Toolbar reference table (new)
- Section 5.3: Collapsing the Icon Toolbar (new)
- Section 13: A/B Compare toolbar icon pairing note added
- Section 19: Remote Review antenna icon note added
- Appendix A.10: Adding mh_PLAYer to PATH (already present, wired in)
- TOC updated: entries for 3.2, 5.2, 5.3, A.10

### Post-Build Fixes (this session)

- **GPU not detected on newer NVIDIA cards (RTX 4070 Ti SUPER etc.)**
  - `pynvml` missing from Nuitka `--include-package` list → `ImportError` at startup
    fell through to nvidia-smi subprocess fallback, which also failed (see below)
  - Fix: added `--include-package=pynvml` to bat; `pip install pynvml` required in venv
  - nvidia-smi fallback used `capture_output=True` (Rule 21 violation) → silent WinError 6
    at module init → `HAS_NVIDIA = False` on all frozen builds lacking pynvml
  - Fix: replaced with `stdout=PIPE, stderr=DEVNULL, stdin=DEVNULL` + `CREATE_NO_WINDOW`
    (`stdout=PIPE` intentionally retained — output is needed for GPU name extraction)
- **Cache label (L1/L2/L3) cropping in transport bar**
  - `setFixedWidth(240)` too narrow for large cache values (e.g. 49 GB L1 → ~330 px text)
  - Fix: `setFixedWidth(240)` → `setMinimumWidth(200)` — expands freely as last widget in row
- **Build failure: `FATAL: failed to locate package 'pynvml'`**
  - Cause: new `--include-package=pynvml` flag without prior `pip install pynvml` in venv
  - Fix: `.venv\Scripts\python.exe -m pip install pynvml` before rebuilding

---

---

## v2.8.17 — 2026 (continued)
**Post-build testing fixes — UI, shortcuts, Help menu**

### Fixes — Manage Shortcuts (WinError 6)
- `_sc_create_lnk`: replaced `capture_output=True` with explicit `stdin/stdout/stderr=DEVNULL`
  and `creationflags=CREATE_NO_WINDOW` — fixes WinError 6 ("The handle is invalid") when
  creating/updating shortcuts from a Nuitka frozen exe. Root cause: frozen subprocess inherits
  invalid console handles; `CREATE_NO_WINDOW` prevents handle inheritance entirely.

### New Features (this session)
- **Keyboard Shortcuts dialog**: replaced `QMessageBox` with two-column `QDialog` — wider
  (920 px), capped at 680 px height (fits 1080p), left column Playback/In-Out/Display,
  right column Annotation/Compare/File
- **Help → User Manual…**: opens `mh_PLAYer_v2_8_User_Manual.pdf` from the exe root
- **Help → License Agreement…**: opens `License_Agreement.pdf` from the exe root
- **Help → Manage Shortcuts…**: inline `_ShortcutDialog` — Create / Update / Remove
  Desktop and Start Menu shortcuts using PowerShell `-EncodedCommand` (no quoting issues)

---

## v2.8.17 — 2026
**Systematic testing round — bug fixes, UI polish, new features**

### Fixes — Export
- Custom range not applied: `src_indices` now passed to `ExportWorker` for correct video frame seek
- Video source export black frames: `VideoExportWorker._fetch` lazy `VideoFileDecoder` path
- ExportWorker bare `ch` NameError fixed
- Custom range radio auto-selected when Start/End spinboxes edited
- Slate checkbox defaults to checked; `[SLATE]` diagnostic log lines
- Quick Save opens file dialog (PNG/TIFF/JPEG); video uses `_canvas._arr8`

### Fixes — Colour Management
- PyOpenColorIO v2: `getColorSpaces()` returns objects → `.getName()`, double try/except fallback
- OCIO errors shown in status bar + console
- COLOUR MANAGEMENT Browse buttons on separate full-width rows (were cropping)
- Auto colour space detection on EXR load (`acesImageContainerFlag`, chromaticities, attrs)
- Default colour mode pref (`default_dmode`) in Playback Prefs
- EV/Gamma applied to raster/video via `raster_to_display()`

### Fixes — Annotations
- **Root cause found**: `_play_tick` fires ~30fps constantly, called `set_annotation_frame()` clearing `_ann_drawing` between press and release. Fix: `set_annotation_frame` skips if `_ann_drawing is True`
- Annotation frame sync in `_on_dmode_changed`, `_invalidate_all`, `_invalidate_display_only`
- Voice note Play button now enabled after annotation load
- Annotation buttons split into two rows — no more cropping

### Fixes — Workspace / Prefs
- Workspace save: `VERSION` → `self.VERSION` (class attr); audio path from sidebar widget text
- Playback Prefs dialog scrollable; label "most EXR files"

### Fixes — UI / Canvas
- `ScrollGuardCombo`: wheel events ignored unless focused — stops scroll from entering sidebar dropdowns
- Radio/checkbox 2px border globally visible on dark screens
- Sidebar scrollbar always visible, styled
- Guide visibility increased (1.5px lines, higher opacity)

### New Features
- HDR / Radiance RGBE (.hdr) support via OpenCV + Reinhard tone-map
- Channel view (R/G/B/A) on video files
- Transport bar second row: γ · color mode · HUD▾ · Guides · Ann (bidirectional sync)
- HUD dropdown (QToolButton + menu) synced with sidebar checkboxes
- COMPARE sidebar: Wipe mode shows Position slider; Diff mode shows Amplify slider
- EV/Gamma spinbox-only (sliders removed), live value labels
- Audio input device selection for voice note recording (sidebar + Playback Prefs)
- AOV tree: expands only for multi-layer EXRs, stays collapsed for single-layer
- Waveform Parade: vertical stack, solo buttons

---

## v2.8.16 — 2026
**B-source sync · Compare controls · EV/gamma raster · Export fixes · Waveform**

- B-source video cache key `f"{video_path}|{b_idx}"` (was filename-only → static B)
- EXR non-blocking via `_b_last_arr8`; preloader every tick, window=40
- B Offset spinbox + right-click drag + Shift+Left/Right nudge
- Waveform Parade rewritten as vertical stack; solo buttons
- Export Video dialog scrollable; slate overhaul; burn-in options
- FFmpeg `shutil.which` fallback; `find_ffmpeg_bins` Nuitka path fix

---

## v2.8.15 — 2026
**FFmpeg detection fix · ExportDialog crash · Zoom combo · Quick save**

---

## v2.8.14x — 2026
**Load Still Images · Image Info rewrite · Load stride · Collapsible sidebar · Window positioning**

---

## v2.8.13 — 2026
**Launch crash fix + Frame stride + AOVPanel signals**

---

## v2.8.12 — 2026
**Diff compare + Ctrl+G**

## v2.8.11 — 2026
**CLI pipeline + Nuke integration**

## v2.8.10 — 2026
Recent files; gap detection; Quick save

## v2.8.9 — 2026
Pixel inspector; burnin; TC; drag-drop; Shot Notes

## v2.8.8 — 2026
RemoteReviewServer

## v2.8.7 — 2026
Voice annotations; OCIO engine; WaveformScope

## v2.8.0–v2.8.6 — 2026
Playlist; SSD cache; SMPTE TC; In/Out; B-source; Workspace

---

*mh_PLAYer · anti-matter-3d.com · 2026*
