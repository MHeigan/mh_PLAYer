# mh_PLAYer v2.12.4 — Changelog

**Stencil overlay  ·  Find Feature  ·  Display grade in exports  ·  Fullscreen A/B  ·  File associations  ·  Drag-and-drop A/B  ·  Command line  ·  Remote review & control  ·  Fixes  —  DPX / Cineon · timecode at fractional rates · Quick Save · B source grading · Multi-View zoom/pan · video in stack cells and buffers · fullscreen video · video read-ahead · shutdown**

### New — stencil overlay
- **An image with transparency laid over playback** — a station ident, a scoreboard or a stats banner — so the action can be kept clear of it while animating or reviewing. Drawn A over B by the image's own alpha, over both sides of an A/B wipe, under the guides, annotations and HUD, and in fullscreen. The frames themselves are not changed.
- **A new icon in the icon toolbar** (a frame with a banner and a corner ident, next to the HUD and Guides icons): Show Stencil, Load Stencil…, Clear Stencil; lit while a stencil is showing; opens the new **STENCIL OVERLAY** sidebar section. The same three are in **View → Stencil Overlay**. **S** shows and hides it (with none loaded, S asks for one). The sidebar section takes a dropped PNG.
- **Placement:** Fit to frame (default — scaled to fit, aspect kept, so a PNG made at the delivery size lands pixel for pixel) or Original size (one stencil pixel per source pixel, so at half or quarter proxy it still covers the same part of the picture); nine positions; margin in source pixels; opacity. Remembered between sessions and saved with workspaces.
- **Baked in on request:** Export Video and Export Frames have an "Include stencil overlay" option, ticked while the stencil is showing; Quick Save includes it whenever it is showing. OpenEXR output with linear data gets it in linear light, so it reads correctly through an sRGB view. Showing it is free; baking it in follows the Export Frames / Export Video licence.
- **Command line:** `--stencil FILE` with `--stencil-size fit|original`, `--stencil-pos tl|t|tr|l|c|r|bl|b|br`, `--stencil-margin PX` and `--stencil-opacity PCT`. In the viewer it shows the stencil (free); in `--convert` it is baked into every frame, after the aspect ratio and under the burn-in (licence).
- Remote Review streams the clean picture, without the stencil (like the HUD and annotations).

### New — Find Feature (Help → Find Feature…, F1)
- **"Where is it?" for the whole program.** Type a word and the list shows every place that feature lives — the menu path, the toolbar icon, the sidebar section and the key — e.g. *diff* → View → Compare → Diff Mode. Everyday words work too: *wipe* finds A/B compare, *scoreboard* the stencil overlay, *mirror* the flips, *stream* Remote Review.
- **Enter or a double-click opens it** — runs the menu command, clicks the icon or opens the sidebar section — and the status bar shows where it lives. Commands that quit, close or clear something are listed but not run from there; greyed-out items say what they need first.
- **Built from the live menus, icons and sidebar** each time it opens, so it always matches the program. Help → Keyboard Shortcuts and Find Feature now share one shortcut table, so they always agree.

### New — the display grade in exports
- **Export Frames and Export Video have "Apply the display grade"**: the CDL grade, invert (V) and flip (H / Shift+H) baked in exactly as the viewer shows them. The label names what is active; it is ticked when any of them is active and greyed when none is — untick it to export the frames as stored. OpenEXR with linear data gets the CDL and the flip (invert is a display check and is not applied to linear data).

### Changed — Export Frames
- **The AUDIO section has been removed.** Image sequences carry no sound, so the option had no effect on frame exports.

### Fixed — Nuke integration (`mh_player_nuke.py`)
- **mh_PLAYer is listed in Nuke's Flipbook dialog.** The script now registers mh_PLAYer through Nuke's flipbook API, as a flipbook application in the Flipbook dialog's Flipbook menu, rather than replacing a Nuke function.
- **Render → Open in mh_PLAYer uses the node's own frame range.** A Read opens with its full range and a Write with its "limit to range" frames; otherwise the script range is used, as before. With nothing selected, a short message says so.
- **Finding the program is more reliable:** versions are compared by number, so v2.12 is recognised as newer than v2.9, and the script finds mh_PLAYer from its Nuke_Integration folder and wherever the Setup installed it.
- mh_PLAYer starts with its own Python and Qt settings, independent of Nuke's; calling register() more than once adds the menu item only once.
- The Nuke Quick Start has been rewritten to match, and manual section A.8 now shows both lines to add to menu.py.

### Fixed — Export Frames
- **Export Frames now saves reliably.** A settings mismatch between the export window and the export engine prevented frames from being written; the two now agree, and the progress window closes when the export completes.
- **OpenEXR export from PNG, TIFF and DPX sources** now writes linear data (the image decoded from sRGB), matching the command line.
- **Clearer format labels.** EV, gamma and the display mode are applied when "Commit display transforms" is ticked (off by default, which exports the image as stored). The labels and the tooltip now describe this, and video sources follow the same setting.

### Fixed — EDL licence check
- **Studio Pro licences are recognised on every timeline route.** Rendering or opening a `.mhedl` on the command line and Playlist → Send Playlist to Timeline now accept a Studio Pro licence, as the timeline editor always did (they were checking an incorrect feature name).

### New — plugins can draw text (plugin API 1.2)
- **Three new plugin functions**, for plugin authors: `render_text(...)` returns the text as a transparent image (font, size, colour, optional outline, several lines, alignment) — the same kind of image a plugin gets from loading a PNG, so it can be used anywhere a logo is used; `find_font(name)` finds a font by name ("Arial", "Segoe UI") or returns the player's own default; `list_fonts()` lists the fonts available, for a font picker. Previously, plugins that needed text located font files themselves, and Studio Watermark worked with PNG logos only.
- The API version is now **1.2**. Existing plugins are unaffected — all seven bundled plugins load and work unchanged.
- **Fonts are found wherever Windows keeps them** — including a Windows installed on a drive other than C: and fonts installed for one user only. This also applies to the burn-in font.

### New — Studio Watermark: image, text, or both
- **The Studio Watermark plugin can stamp text as well as a logo** — or both together. Choose the Source in the dialog: **Image** (your logo, as before), **Text** (type it, choose the font, bold, fill and outline colours and outline width), or **Image + Text** (the logo with the text below, above, right or left of it, at a size you set relative to the logo). Handy for a studio logo with the shot name or version under it — change the text per delivery and keep the logo.
- Out of the box it now stamps "WORK IN PROGRESS" in white with a dark outline — the same look as the two sample logos — so there is nothing to supply before the first run.
- If you had already chosen a logo in an earlier version, it keeps using it.
- The preview fits the dialog correctly from the first time it opens.

### New — the sidebar follows what you are doing
- **Using a feature now opens the sidebar at its section.** Pressing R / G / B / A / C opens Channel View; P or View → Half / Quarter opens Proxy Resolution; Shift+R, or the gamma control in the transport, opens Exposure / Gamma; the colour-mode icon, the display-mode control and False Colour open Display Transform; the aspect-ratio control opens Aspect Ratio; `[`, anything under View → Compare, the B offset keys and loading a B source open Compare; N (turning annotation on) and loading annotations open Annotation; loading an audio file opens Audio; stepping through EXR layers opens Render Layers. If the sidebar is collapsed it opens; the section expands and comes to the top. It can be switched off in **Preferences → Display** ("Open the sidebar at a feature's section when you use it"). The toolbar icons that name a section always do it.
- **View → Stereo / Anaglyph** — a new menu (Enable Anaglyph, Load Right Eye…, Load Stereo EXR…) that also opens the Stereo / Anaglyph section.
- **The Compare section can load and clear B itself** — a field you can drop an image-sequence frame or a video on (like the LUT fields), which also shows the name of the loaded B source, plus **Load B…** and **Clear B** buttons.

### Fixed — interface refinements
- **View → Guides presets** — Title Safe, Rule of Thirds, Golden + Thirds and any guide presets you have saved yourself — now apply as expected.
- **The sidebar icons bring their section to the top.** Clicking Display, Compare, Scopes or Pixel Inspector in the second toolbar row now scrolls the section to the top of the sidebar, so its controls are in view whichever direction you come from (HUD and Annotation, at the end of the sidebar, come fully into view).
- **Help → Keyboard Shortcuts lists every shortcut**, including Ctrl+Shift+I (load still images), Ctrl+Alt+C (copy frame image), H / Shift+H (flip), Esc (clear region of interest) and Shift+O (onion skin), with the Load B Source menu path updated and notes on dropping a file on the right half of the viewer and on leaving fullscreen.
- Closing the program shortly after Help → Check for Latest Version now exits cleanly.

### Improved — Quick Save Frame saves what you see
- **Quick Save Frame (Ctrl+Shift+C) saves the frame exactly as displayed** — exposure, display mode and the display grade (CDL, invert, flip) included, for every kind of source. Image sequences now include the CDL, invert and flip too, as video already did.

### Improved — the B source follows exposure, gamma and display mode
- **Changing exposure, gamma, the display mode or the channel view (R / G / B / A) now changes A and B together**, for every kind of B source — EXR, PNG / TIFF / JPEG and video — so a wipe always compares like with like.

### Fixed — timecode at 29.97, 59.94 and 23.976 fps
- **The timecode on screen, in the HUD, in GUI video-export burn-ins and in command-line burn-ins now always agree.** At 29.97 and 59.94 fps the player shows true drop-frame timecode (with the `;` separator, e.g. 00:01:00;02 one minute in), exactly as the command line already did; at 23.976 fps the frame labels stay exact. Other frame rates are unchanged.

### Improved — fullscreen in A/B compare
- **Ctrl+F while comparing A and B now keeps the comparison.** In A/B wipe, difference or stack compare, Ctrl+F makes the main window fullscreen with just the viewer: both sources, the draggable divider and every compare key work exactly as in the window. Ctrl+F, Esc or F11 returns to the normal window with its panels as they were. With a single source, Ctrl+F opens the usual fullscreen player. (Previously, Ctrl+F in compare mode switched to the single-source fullscreen view.)
- **Multi-View Compare has its own fullscreen:** Ctrl+F or F11 in that window. Esc first leaves fullscreen, and closes the window only when pressed again.

### New — mh_PLAYer in Windows' "Open with" and Default apps
- **Windows now offers mh_PLAYer for its file types.** Right-click a video or image, choose **Open with**, and mh_PLAYer is in the list; it also appears in **Settings → Apps → Default apps** with the types it opens. Nothing is taken over: your existing defaults stay as they are, and you choose mh_PLAYer where you want it (Open with → Always, or Default apps).
- **Chosen in the installer, changeable any time.** Setup asks which groups to offer it for — video, VFX images (EXR, DPX, CIN, HDR) and common images (PNG, JPEG, TIFF) are ticked, playlists (M3U8, M3U) are optional. Change them later in **Help → File Associations…** or **Preferences → Windows integration**, which also serve the portable ZIP build, and whose **Open Default Apps** button goes straight to mh_PLAYer's page in Windows Settings. **Remove All** withdraws everything; uninstalling does too.
- **mh_PLAYer's own files open on a double-click** — workspaces (`.mhplay`) and edit decision lists (`.mhedl`).
- **"Open with" shows a short name** — "mh_PLAYer" instead of the full program description.
- **Help → File Associations… works in the ZIP build** as well as the installed one. If it is unavailable (for example when running from source), it explains why.
- Current user only; no administrator rights needed.

### Improved — opening files from Explorer or by drag and drop
- **Workspaces (`.mhplay`), EDLs (`.mhedl`) and playlists (`.m3u8`)** now open from a double-click or a drop exactly as they do from their menu commands; a dropped EDL starts playing the cut.

### Improved — video format support
- **MXF, MTS and M2TS** now appear in the Open dialog and are handled as video throughout — export, video export and Quick Save Frame. Every part of the player now shares one list of formats.
- **FLV and MPEG-TS (`.ts`) now open.** `.ts` is not offered in Open with, because on developers' machines it is also a source-code file type.

### Installer
- **Offers to uninstall the previous version first.** When an earlier mh_PLAYer is installed, Setup shows a page with that option ticked (recommended): the old version is removed completely before the new one is installed, so no files from older versions are left behind. Your settings are kept. Setup waits for the old uninstaller to finish completely before it continues.
- **The picture on Setup's final page now runs the full height of the window**, down past the line above the Finish button — with art cut to that taller shape, so the picture is not stretched, and one version per Windows display scaling (100–200%), so it stays sharp on high-DPI screens too.
- **The file-association options use a short video-formats label**; the full list of formats is in the program's File Associations dialog.
- **Upgrading removes the previous version's program file** from the install folder (the program file name carries the version number).
- **The `mh_player` command-line launcher is installed with the program**, so the "Add mh_PLAYer to my PATH" option gives a working `mh_player` command.
- Install, upgrade and uninstall were each verified with Inno Setup 6.7.3 for what they leave in the registry and the program folder.

### New — drag-and-drop A / B
- **Drop a file on the right of the wipe divider to load it as B**; drop on the left to replace A. While you drag, the half the file will fill is highlighted and labelled A or B, with the divider shown, so the drop always says what it will do. With nothing loaded yet, a drop anywhere opens the file as A, as before.
- **Drop two files at once to fill A and B in one go** — in name order, so `shot_v001` becomes A and `shot_v002` B, whichever you grabbed first. Frames multi-selected from one image sequence still load as that one sequence.
- Audio files load as audio wherever they land — on the viewer, the sidebar or the timeline — and project and playlist files are never taken as a B source.
- Without an A/B compare licence the split never appears, so a drop simply opens the file as A — no licence prompt from a drag.
- The one-click **Load B source** button in the second toolbar row works the same way as before; it and the dialog now share one loading path with the drop, and its tooltip mentions dragging a file onto the right half.

### Fixed — B source naming
- **Loading a numbered video as B shows and caches the right file.** Numbered video siblings (`shot_v001.mov`, `shot_v002.mov`) are no longer grouped as if they were frames of one sequence, so loading `v002` as B is labelled and cached as `v002`. The picture itself was always correct.

### Improved — Multi-View Compare keeps your view
- **Selecting images in the filmstrip keeps each pane's zoom and pan.** Panes whose image stays selected are reused, so they keep their own view with no flicker.
- **A newly shown pane opens at the view you last set** on any pane — zoom and pan together, because inspecting one region across several images needs both. Fit is still the default until you first zoom. Fit All returns to it; 1:1 All, wheel, drag and double-click all count as setting the view. Clear starts a fresh comparison at fit. Fit All and 1:1 All remain one-shot buttons, and Sync Zoom & Pan is still the only view toggle.
- **Images of a different resolution** take the same zoom, with the pan clamped to that image's valid range so it always stays on screen. Same resolution is applied exactly, so the same region stays under inspection.
- **Remove Selected rebuilds the grid once**, after the filmstrip and the image list agree, which is quicker with large images.

### Fixed — video sources in stack compare and input buffers
- **Stack cells show video sources.** Cells now use the same per-frame lookup as the rest of the player and decode through their own video decoder, kept open while the stack is up so stepping stays fast.
- **Recalling a buffer across source types shows the right picture.** Recalling a video buffer reopens the clip the normal way (decoder, audio, read-ahead, frame rate); recalling a sequence over a video releases the video and its audio first. Sequence-to-sequence recall is unchanged.

### Fixed — fullscreen video playback
- **The video read-ahead (new in v2.12.3) now starts reliably.** It begins filling the RAM cache as soon as a clip is opened, and a fill already under way is kept going rather than restarted, which avoids extra keyframe seeks.
- **Fullscreen video plays from any frame.** Fullscreen now decodes a frame that is not yet cached, just as the main window does, and keeps the read-ahead running ahead of it — playback runs in real time from any frame, including right after a pause or a jump, and Home / End land on an image. Fullscreen playback also uses the same per-frame cache keys as the main window.
- **Note — memory:** with the read-ahead running as designed, opening a video fills up to a quarter of the RAM cache with that clip in the background. Scrubbing and replay are smooth straight after opening as a result.

### Fixed — shutdown
- **Smoother exit while video read-ahead is running.** The background loaders (the video read-ahead and the image-sequence preloader) now wait briefly, with a time limit, for their threads to finish before the program closes, which prevents an error that could occur at exit.

### Improved — the command line
Every documented command-line option now works as described, and each is covered by an automated test that runs the real command on real files.
- **Viewer options are applied after the file opens.** `-m`, `-E`, `-G`, `--lut` and `--ocio-config` are applied after the file (and after a workspace given with `-w`), so an option on the command line always takes effect.
- **More options supported:** `-w` (workspace), `-L` / `-A` (EXR layer / AOV), `-c` (channel), `-z` (zoom), `--ram` (RAM cache for this session), `-P bounce`, and `-s` / `-e` on sequences numbered from 1001. `-B FILE` loads that file as the B source directly. `-a FILE` and every other way of loading audio use the audio licence check, like Browse.
- **`--convert` is one pipeline for every input.** Image sequences, video and `.mhedl` timelines get the same frame range, stride, display transform, exposure, LUT, grade, burn-in, aspect ratio and codec options. A video source keeps its sound in a movie output (at stride 1).
- **Accurate colour for 8-bit sources.** PNG, JPEG and video frames are now decoded from sRGB first (`-g auto`, the default), so converting a PNG to a PNG keeps its values, and exposure and display transforms behave as they do on an EXR; `-g off` copies values untouched.
- **Burn-in matches the GUI export** — the same renderer and layout (frame number bottom-left, `--burnin-text` bottom-centre, timecode bottom-right), with the timecode counted from the first frame written and drop-frame at 29.97 and 59.94.
- **New:** `--cdl FILE` (an ASC-CDL `.cc` / `.ccc` / `.cdl`, applied after the display transform, as in the viewer); `--check` on a video or on a `.mhedl` timeline (every clip's media present, no gaps in the part used); `--strict` also opens every frame and reports empty, truncated or unreadable files; alpha is kept for PNG, TIFF, WebP and EXR output; `-f`, `--threads`, `--roi`, `-c` and `-d 16` are supported in convert.
- **Movies:** an odd width or height is padded by one pixel for H.264 and H.265 (which need even sizes); NTSC rates are written exactly (30000/1001), not as 29.97.
- **The `mh_player` command.** The build includes an `mh_player.cmd` launcher beside the program, so after Help → CLI PATH Setup, `mh_player` works in any new terminal. CLI PATH Setup confirms this, and lets you know if the launcher is missing.

### Improved — Remote Review (browser)
- **Efficient frame delivery.** The page now waits for each new frame (long-poll), so there is no network traffic while the player is paused. Every new frame is announced once, and on a slow network frames are skipped rather than queued, so the page keeps pace with the player.
- **The page shows the playback rate, the timecode and the shot name.**
- **The LAN address is shown**, so other machines can connect even on a studio network without an internet route.
- **A second mh_PLAYer on the same machine reports the port as in use**, and stopping and starting the server again works straight away.

### Improved — Synced Remote Review (Studio Pro)
- **A station joining mid-session goes straight to the host's frame and play state.**
- **When the host ends the session, the other stations are told at once** ("Host ended the session").
- **Each station has its own send queue with a time-out**, so a station that stops responding (a machine going to sleep, for example) is dropped while the others carry on. Frames are coalesced, so a slow station catches up to the current frame.
- Choosing Disconnect while an automatic reconnect is in progress now always disconnects.

### Improved — Remote Control (127.0.0.1:7979)
- **`/ping` reports the current version.**
- **`/open` opens everything the player's Open does** — `####` and `%04d` sequences, video, `.mhedl` timelines, `.mhplay` workspaces and `.m3u8` playlists — and replies 404 for a path that does not exist.
- **Stays responsive when a client leaves a connection open.**
- **For local scripts and tools.** Requests from web pages are declined (403), so only scripts, curl, Nuke and other local tools can control the player.

### Fixed — formats, colour and export
- **DPX and Cineon open reliably.** They are now decoded directly: 8 / 10 / 12 / 16-bit DPX in either byte order, and 10-bit Cineon, at any width — including the 32-bit line padding of 12 and 16-bit files of odd width, read exactly as FFmpeg reads it. (Packed 10 and 12-bit DPX, which are rare, report a clear message.)
- **The film-range stretch applies to log scans only.** The stretch (code 95 black to 685 white) now applies to Cineon and to DPX marked printing density or logarithmic. Any other DPX — linear or video DPX, including anything FFmpeg writes — is shown as stored, with its full tonal range, in the viewer and on the command line alike.
- **24 and 30 fps video opens at exactly 24 and 30 fps** (previously detected as 23.976 and 29.97), with the matching non-drop timecode.
- **CDL import reads every value** — slope, offset, power and saturation — from `.cc`, `.ccc` and `.cdl` files as written by Resolve and Baselight (with or without the ASC namespace, and single-value entries).
- **The player starts normally when `$OCIO` is set** to a valid OCIO config, as is common in Nuke studios.
- **Export Video** of a source with an odd width or height works with H.264 and H.265, and NTSC rates are written exactly.
- **Cancel during encoding.** Cancel now stops Export Video within a fraction of a second, even while the movie is being encoded, and removes the unfinished movie (an earlier export of the same name that has not yet been overwritten is kept). **Closing the export window during an export cancels it** too.
- After a workspace restore, the transport's display dropdown and the sidebar show the same mode.

### Documentation
- **The user manual, Quick Start guide, CLI cheat-sheet, Nuke Quick Start, README, release notes, START HERE and QUICK START guides** are updated for v2.12.4 — new sections on Open with, the sidebar following what you do, video read-ahead, fullscreen A/B, and the plugin text functions.
- **Manual updates:** the sidebar is described as the left panel; the Playlist icon opens the Playlist panel; the full list of sidebar sections; a note that the SSD cache never shows frames made with earlier display settings, so it does not need purging after changing them; a registry path's formatting.
- **GitHub README updates:** the sidebar toggle is Ctrl+Tab, and the optional signed installer is listed alongside the ZIP.
- **The command-line reference (manual Appendix A and the CLI cheat-sheet) has been rebuilt from the program's own option list**, so it matches the program exactly — options, burn-in text position, exit codes and gamma handling. New manual section **19.9 Remote Control**; section 19 updated for the browser server.
- **The manual now states** that the CDL grade is applied after the display transform, and that import reads `.cc`, `.ccc` and `.cdl`.
- **New manual section 14.1 Stencil Overlay**; the stencil in the icon-toolbar table, the sidebar list, "the sidebar follows what you do", fullscreen, Export, Quick Save, the shortcut list and Appendix A; a stencil group in the CLI cheat-sheet (still two pages); README, release notes, Quick Start, site.
- **Export documentation updated:** annotation, burn-in, HUD and slate options are described under Export Video, where they belong (manual 18.1, 18.3, section 14's tip, the licensing table and README.txt); the row-2 export icon opens Export Video; the GUI burn-in reads frame number / shot name / timecode, left to right; the info-bar toggle is on row 1; the row-2 table follows the real left-to-right order. On the website, aspect-ratio baking is described as a command-line (`--ar`) feature.

### Build / verification
- **Behavioural test harness** (`mh_PLAYer_v2_12_4_tests.py`) with three test suites that each run in their own process: the command line (real commands on real files, including DPX, HDR, 16-bit TIFF, stereo EXR, truncated frames and timelines), the viewer options (through the real start-up path, including start-up with `$OCIO` set), and the network servers (with the browser page in headless Chromium when available). Fullscreen, drag-and-drop and keyboard tests use real input events, and the command-line suite runs with real signed Studio and Studio Pro licences.
- **`predelivery_check.py` source checks:** LAMBDACAP (a signal argument overwriting a lambda's captured value), KWARGS (a call passing a keyword its definition does not accept) and LICKEY (a licence gate naming an unknown feature); the cache-key check also follows keys passed through a local variable.
- **`predelivery_check.py` release checks:** batch and version files (plain ASCII, Windows line endings, BATPAREN for brackets inside `( … )` blocks), script syntax, the email and private-folder rules across documents, SitePad block constraints, README width, stale version numbers, each build script against its version file, documented command-line options (CLIFLAGS), installer coverage of every file the build adds (ISSFILES), and the per-release claims. Its self-test confirms that every check fires on a violation and stays silent on a clean set of real edge cases.
- **Build bat:** writes the `mh_player.cmd` launcher into the distribution folder; bundled-plugins wording updated. **Installer script:** installs `mh_player.cmd`; unused `jaraco` entries removed.

---

Earlier versions: the full release history is in [CHANGELOG.md](https://github.com/MHeigan/mh_player/blob/HEAD/CHANGELOG.md).
