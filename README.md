# Vox Populi on CachyOS

**Civilization V + Vox Populi via Steam and Proton**

Revision 10.4.0 · 2026-09-24 · Vox Populi 5.4.6 (stable) · Protontricks 1.14.1-1 · Proton 11.0 · CachyOS with native Steam; other distributions in §3.

No terminal is needed: the work happens in Steam, the Protontricks and Winetricks windows, the installer's wizard and your file manager. Every step that writes to disk ends in a check; the two wizard steps, 6 and 7, are checked by Step 8. `WARNING` marks a common failure, `CRITICAL` a step that decides whether this works at all.

The same guide is also here as `vox-populi-cachyos.pdf` (print-ready, US Letter), built from this file by `tools/build_print.py`; `tools/check_guide.py` verifies both.

---

## Contents

1\. [How it works](#1-how-it-works) · 2. [Requirements](#2-requirements) · 3. [Other distributions](#3-other-distributions) · 4. [Stage A — game under Proton](#4-stage-a--game-under-proton) · 5. [Stage B — install Vox Populi](#5-stage-b--install-vox-populi) · 6. [Stage C — first run](#6-stage-c--first-run) · 7. [Settings and stability](#7-settings-and-stability) · 8. [Adding other mods](#8-adding-other-mods) · 9. [Community Patch options](#9-community-patch-options) · 10. [Multiplayer](#10-multiplayer) · 11. [Updating](#11-updating) · 12. [Uninstalling](#12-uninstalling) · 13. [Troubleshooting](#13-troubleshooting) · 14. [Alternative installer](#14-alternative-installer) · 15. [Bug reporting](#15-bug-reporting) · 16. [Path reference](#16-path-reference) · 17. [Sources](#17-sources) · 18. [Limits](#18-limits)

---

## 1. How it works

**Proton is mandatory.** Vox Populi ships a compiled Windows game-core DLL that the native Aspyr Linux build cannot load; the Windows build under Proton is the only working configuration.

**The installer is Inno Setup 6** (Installer Version 1.2), a plain Win32 wizard that renders correctly under Proton; no .NET is involved.

**It asks for two different paths.**

```
┌────────────────────────┬──────────────────────────────┬─────────────────────────────────┐
│ Wizard page            │ Resolves to                  │ Receives                        │
├────────────────────────┼──────────────────────────────┼─────────────────────────────────┤
│ Select Destination     │ {userdocs}\My Games\         │ MODS\(1) Community Patch        │
│ Location               │ Sid Meier's Civilization 5   │ MODS\(2) Vox Populi             │
│ — the Documents path   │                              │ MODS\(3a) VP - EUI              │
│                        │ Under Proton:                │   Compatibility Files           │
│ LEAVE THIS ALONE       │ C:\users\steamuser\          │ MODS\(3b) 43 Civs Community     │
│                        │ Documents\… (in the prefix)  │   Patch                         │
│                        │                              │ MODS\(4a) Squads for VP         │
│                        │                              │ MODS\(5) Modpack Maker for VP   │
│                        │                              │ Text\VPUI_tips_en_us.xml        │
├────────────────────────┼──────────────────────────────┼─────────────────────────────────┤
│ Select the             │ Whatever you browse to. Must │ Assets\DLC\VPUI                 │
│ Civilization V folder  │ be the REAL game install,    │ Assets\DLC\UI_bc1  (EUI only)   │
│ — custom page, right   │ reached through S:\ or Z:\   │ Assets\DLC\Expansion2\          │
│ after the setup-type   │ (Wine maps Z: to /)          │   Expansion2.Civ5Pkg (replaced) │
│ page                   │                              │ Assets\DLC\Expansion2\Sounds\   │
│ THIS IS THE ONE THAT   │                              │   XML\                          │
│ GOES WRONG             │                              │   MinorCivSounds_VoxPopuli.xml  │
└────────────────────────┴──────────────────────────────┴─────────────────────────────────┘
```

The Documents default is already correct: it resolves inside the prefix, where the Windows build reads mods. The Civilization V folder page arrives blank; pressing Next with it empty fills it from the registry or `C:\Program Files (x86)\Steam\…` only if such a tree exists inside the prefix, which a clean prefix has not.

> **CRITICAL** — Aim that page at the real Linux install and nothing needs copying. The wizard refuses a folder without `Assets\DLC` and all ten DLC folders, so on a clean prefix a wrong path is blocked, not installed. A game tree already inside the prefix — a copied install, say — passes that test, and the UI assets land where the game never reads them; that is why some guides need a copy step.

**Every DLC must be installed, not merely owned.** The wizard blocks unless `DLC_01`–`DLC_07`, `DLC_Deluxe` (Babylon), `Expansion` (Gods & Kings) and `Expansion2` (Brave New World) all exist — in practice the Complete Edition on Civ V 1.0.3.279.

**It cleans up after itself.** Before writing it deletes the prefix `cache` and `Text\VPUI_tips_en_us.xml`, the game folder's `VPUI`, `UI_bc1`, `Expansion2.Civ5Pkg` and the sound XML, the current VP mod folders, and every legacy folder name back to the CBP/CBO/CSD era — including `(4) Civ IV Diplomatic Features`, `(5) More Luxuries`, the `(6x)` compatibility files and `(7a) Promotion Icons for VP` / `(7b) UI - Promotion Tree for VP`. Clear nothing by hand, and expect any edit inside a VP mod folder to be lost at the next install or update.

**There is no uninstaller entry** (`Uninstallable=no`). Removal is the **Uninstall all** setup type in the same `.exe`.

**Stable and beta releases both exist.** Each GitHub release links a release-notes thread titled *New STABLE Version* or *New BETA Version*. 5.4.6 (2026-08-31) is stable.

---

## 2. Requirements

```
┌──────────────┬────────────────────────────────────────────────────────────────────────┐
│ Game         │ Civilization V 1.0.3.279, all expansions and all DLC installed         │
│ Steam        │ Native Steam (multilib steam); data dir ~/.local/share/Steam,          │
│              │ also reachable through ~/.steam/root. Other distributions: §3          │
│ Compat tool  │ Proton Experimental or newest numbered Proton (11.0 at this revision); │
│              │ proton-cachyos-slr OK. NOT "Steam Linux Runtime" — that is not Proton  │
│ Language     │ Game language English (Steam → Properties → Language). VP's text       │
│              │ exists in English only; other languages show missing or stale entries  │
│ Tooling      │ extra/protontricks 1.14.1-1 with yad (its game list) and zenity (the   │
│              │ Winetricks windows); it pulls winetricks, wine and cabextract          │
│ Disk         │ Full re-download of the Windows depots, plus ~105 MB installer         │
│ Conflicts    │ The mods block, by modinfo: More Luxuries, CSD for VP, Civ IV          │
│              │ Diplomatic Features (both), Artificial Unintelligence, Bridges and     │
│              │ Canals, standalone Squads for VP — no Workshop copies of them; and     │
│              │ no other mod that ships its own DLL                                    │
└──────────────┴────────────────────────────────────────────────────────────────────────┘
```

Every path below is inside one of three folders. Steam → right-click **Sid Meier's Civilization V** → **Properties → Installed Files → Browse** opens the game folder; the library is the folder above `steamapps`, and `compatdata/8930` always sits in the same library as the game. Show hidden files in your file manager — `.local` and `.steam` are hidden.

```
┌──────────────────┬──────────────────────────────────────────────────────────────────┐
│ Steam library    │ ~/.local/share/Steam — native Steam's default on CachyOS; a game │
│                  │ in a secondary library uses that library. Other packagings: §3   │
│ Game folder      │ <library>/steamapps/common/Sid Meier's Civilization V            │
│ Documents folder │ <library>/steamapps/compatdata/8930/pfx/drive_c/users/steamuser/ │
│                  │ Documents/My Games/Sid Meier's Civilization 5 — mods, saves,     │
│                  │ logs, cache and config.ini of the Windows build                  │
└──────────────────┴──────────────────────────────────────────────────────────────────┘
```

---

## 3. Other distributions

From Step 1 on, other distributions differ in three things — how Steam is installed, where its default library sits, and whether their own Protontricks is recent enough. **Protontricks 1.12.0 is the floor**: older releases cannot read the current Steam client's `appinfo.vdf` and stop with "Invalid file magic number".

```
┌──────────────────────┬──────────────────────────────┬──────────────────────────────────┐
│ Distribution         │ Steam                        │ Protontricks                     │
├──────────────────────┼──────────────────────────────┼──────────────────────────────────┤
│ CachyOS, Arch        │ steam (multilib)             │ extra, 1.14.1 — Step 4           │
│ Ubuntu 26.04 LTS     │ steam-installer (multiverse) │ multiverse, 1.13.1 — use it      │
│ Ubuntu 24.04 LTS     │ steam-installer (multiverse) │ multiverse, 1.10.5 — too old;    │
│                      │                              │ use the Flatpak                  │
│ Debian 13            │ steam-installer (contrib)    │ none in trixie; use the Flatpak  │
│ Fedora 44            │ steam (RPM Fusion Nonfree)   │ fedora, 1.13.1 — use it          │
│ Steam Deck           │ preinstalled                 │ Flatpak, from Discover           │
└──────────────────────┴──────────────────────────────┴──────────────────────────────────┘
```

Where the default library sits — the §2 library unless Civ V lives in a secondary one:

```
┌────────────────────────────────┬────────────────────────────────────────────────────────┐
│ Steam packaging                │ Default library                                        │
├────────────────────────────────┼────────────────────────────────────────────────────────┤
│ Native package, any distro     │ what ~/.steam/root links to                            │
│   Arch, CachyOS                │   ~/.local/share/Steam                                 │
│   Debian, Ubuntu               │   ~/.steam/debian-installation for a new install;      │
│                                │   older installs may sit in ~/.local/share/Steam       │
│ Flatpak Steam                  │ ~/.var/app/com.valvesoftware.Steam/.local/share/Steam  │
│ Snap Steam                     │ ~/snap/steam/common/.local/share/Steam                 │
└────────────────────────────────┴────────────────────────────────────────────────────────┘
```

**Debian and Ubuntu** need the i386 architecture enabled and the `multiverse` (Ubuntu) or `contrib` (Debian) component before `steam-installer` is offered; Debian also wants the 32-bit Mesa packages that the Debian wiki's Steam page lists, or NVIDIA's 32-bit driver libraries (`nvidia-driver-libs:i386`) with the proprietary driver. **Fedora** takes Steam from RPM Fusion Nonfree and Protontricks from Fedora itself. Where the table says **Flatpak**, add Flathub, install `com.github.Matoking.protontricks` and restart; on the **Steam Deck**, Desktop Mode's Discover does this, and SteamOS's read-only root rules out anything else. Flatpak Protontricks reaches Steam's own directories plus the standard Downloads, Documents and Desktop folders, and ships the same app shortcut and Launcher entries; a library or installer elsewhere makes it report the folders it cannot reach and how to grant them.

Then, on any of them: start Steam once and sign in so it creates its directories, confirm in your package manager that Protontricks is 1.12.0 or newer, and continue with Step 1.

---

## 4. Stage A — game under Proton

### Step 1 · Force Proton

Steam → right-click **Sid Meier's Civilization V** → **Properties → Compatibility** → tick **Force the use of a specific Steam Play compatibility tool** → Proton Experimental or the newest numbered Proton. Steam swaps the native build for the Windows depots; let it finish. If the download stops with a disk write error, see §13.

> **WARNING** — The same dropdown lists Steam Linux Runtime entries. They are not Proton: no Wine prefix is created, and Protontricks stops with "Proton installation could not be found!".

CachyOS alternatives (install the package, restart Steam): `proton-cachyos-slr`, `proton-cachyos-native`, or `protonup-qt` / `protonplus` for Proton-GE. Prefer `-slr` — a Proton build that runs inside the Steam Linux Runtime container, not one of the bare *Steam Linux Runtime* entries above.

`S:` drive: Proton 11.0 and Experimental map `S:` to the game's Steam library for every game, which shortens the path typed in Step 7; on Proton 10.0 or older the launch option `PROTON_SET_GAME_DRIVE=1 %command%` (**Properties → General → Launch Options**) does the same. If the library root is not writable or is on another filesystem, `S:` points at `steamapps` itself and the wizard path becomes `S:\common\…`; the `Z:\` path always works.

**Check:** the game folder holds `CivilizationV_DX11.exe` among other `.exe` files. No `.exe` at all, and a `Civ5XP` binary instead, means Steam is still serving the native build.

### Step 2 · Confirm every DLC

Steam → Properties → **DLC** → tick everything.

**Check:** `Assets/DLC` in the game folder holds all ten — `DLC_01`–`DLC_07`, `DLC_Deluxe`, `Expansion` and `Expansion2`. A missing one stops the installer at Step 7.

### Step 3 · Launch once, reach the main menu, quit

This creates the prefix, `compatdata/8930/pfx`, without which Protontricks cannot see the game, and builds Civ V's Documents tree inside it. Launch from the Steam library window: the modpack thread reports that a desktop shortcut or the tray icon starts the DirectX 9 build without the launcher. When the Firaxis launcher appears, choose DirectX 10/11; the DX9 executable may start regardless (Proton #8327), which does not affect Vox Populi.

**Check:** the Documents folder exists and holds `Logs`, `Saves` and `config.ini` among others.

---

## 5. Stage B — install Vox Populi

### Step 4 · Protontricks and runtime libraries

Install `protontricks`, `yad` and `zenity` from the CachyOS repositories (other distributions: §3). Open **Protontricks** from the application menu and pick **Sid Meier's Civilization V** — it is listed only after Step 3. Winetricks opens for the game's prefix: choose **Select the default wineprefix**, then **Install a Windows DLL or component** → `vcrun2008`, and **Install a font** → `corefonts`. Both download, so this needs network access; Winetricks skips what is already installed, so repeating it is harmless.

**Check:** opened again, those two lists show `vcrun2008` and `corefonts` already ticked.

`CvGameCore_Expansion2.dll` (both 5.4.6 variants) imports `MSVCR90.dll` and `MSVCP90.dll`, the Visual C++ 2008 runtime that `vcrun2008` installs; Wine's built-in copies usually suffice, but a report in thread 702075 had VP working only after this step. `corefonts` adds Microsoft's ten core web fonts and needs `cabextract`.

### Step 5 · Download and verify

Releases are GitHub assets on `LoneGazebo/Community-Patch-DLL`, named `Vox.Populi.<version>.exe`; each asset's digest is shown in the release's asset list, and the release-notes thread title says STABLE or BETA. For 5.4.6:

```
Vox.Populi.5.4.6.exe — 110,481,637 bytes
sha256  31679423e55d7f64ba9693d8252d9e29f824fd9f8da043af547470068f98ba8a
```

**Check:** the digest your file manager computes matches (Dolphin: **Properties → Checksums**, paste the digest and it confirms the match). A mismatch means delete the file and download it again; do not run it. `Release_Debug.zip` on the same page is a debug DLL for crash reports, not needed for play.

### Step 6 · Run it inside the prefix

Right-click `Vox.Populi.5.4.6.exe` → **Open With → Protontricks Launcher** → **Sid Meier's Civilization V**. The wizard can take a minute to appear and may open behind other windows under Wayland. A warning about a 64-bit WINEPREFIX, if one appears, is expected: Civ V is 32-bit inside a 64-bit prefix.

### Step 7 · Work the wizard

```
┌───┬──────────────────────────┬──────────────────────────────────────────────────────┐
│   │ Page                     │ Action                                               │
├───┼──────────────────────────┼──────────────────────────────────────────────────────┤
│   │ License · Information    │ Next                                                 │
│ ★ │ Select Destination       │ LEAVE UNCHANGED. Must read C:\users\steamuser\       │
│   │ Location                 │ Documents\My Games\Sid Meier's Civilization 5        │
│ ★ │ Setup Type / Components  │ Choose one variant — table below                     │
│ ★ │ Select the Civilization  │ Type S:\steamapps\common\Sid Meier's Civilization V  │
│   │ V folder                 │ or the Z:\ form of the game folder, given below      │
│   │ Start Menu folder        │ Irrelevant under Proton — Next                       │
│   │ Ready to Install         │ CHECK "Civilization V path" shows the S:\ or Z:\     │
│   │                          │ path, not C:\Program Files (x86)\…                   │
│   │ Installing · Finished    │ Wait, Finish                                         │
└───┴──────────────────────────┴──────────────────────────────────────────────────────┘
```

```
┌───────────────────────────────┬──────────────────────────────────────────────────────┐
│ Vox Populi (with EUI)         │ Full VP + Enhanced User Interface — the usual choice │
│ Vox Populi (no EUI)           │ Full VP, stock interface                             │
│ Community Patch only          │ AI and bugfix base mod. NOT compatible with EUI      │
│ 43 Civ … (three variants)     │ Same options with a DLL supporting 43 major civs     │
│ Uninstall all                 │ Removal path — §12                                   │
└───────────────────────────────┴──────────────────────────────────────────────────────┘
```

The `Z:\` form is the game folder with `Z:` in front and backslashes instead of slashes; for the default library:

```
Z:\home\<your user name>\.local\share\Steam\steamapps\common\Sid Meier's Civilization V
```

If the Ready page shows a `C:\Program Files (x86)` path, go back and fix it rather than copy files afterwards. The installer also fails if MODS has been moved out of the Documents tree, which under Proton only happens if you change the Documents page.

### Step 8 · Verify placement

**Check:** `Assets/DLC` in the game folder now holds `VPUI`, plus `UI_bc1` with EUI — the pass/fail test for Step 7. Community Patch only installs neither, but adds `Expansion2/Sounds/XML/MinorCivSounds_VoxPopuli.xml` there. The Documents folder's `MODS` holds the numbered mod folders of your variant.

If `<library>/steamapps/compatdata/8930/pfx/drive_c/Program Files (x86)/Steam/steamapps/common/Sid Meier's Civilization V` exists, a game tree inside the prefix took the files: re-run the installer with the correct path, or copy that tree's `Assets` folder over the real game folder's. There is no `Assets/DLC/Vox Populi` folder; the installer creates `VPUI` and, with EUI, `UI_bc1`.

---

## 6. Stage C — first run

1. Launch Civ V from Steam.
2. Main menu → **MODS**; accept the prompt about DLC being disabled and the game restarting.
3. The first entry into the mods menu runs a "configuring game data" pass of 5–15 minutes — not a hang. Thread 702075's opening post reports it may crash once and work after a relaunch.
4. Enable the VP mods the installer placed in `MODS`.
5. Press **NEXT**, never **Back**.
6. **Single Player → Set Up Game.**

> **CRITICAL** — Back returns to the main menu and silently deactivates the mod set — the most common "I installed VP and nothing changed" report. The installer's finish screen says the same: enable all mods, click Next, not Back.

```
┌──────────────────────────────┬─────────────────────────────────────────────────────┐
│ (2) Vox Populi               │ Requires (1) Community Patch                        │
│ (3a) VP - EUI Compatibility  │ MUST be enabled on any EUI install, or VP will not  │
│   Files                      │ function                                            │
│ (3b) 43 Civs Community Patch │ Only for 43-civ variants                            │
│ (4a) Squads for VP           │ Optional QoL — RTS-style control groups and group   │
│                              │ movement                                            │
│ (5) Modpack Maker for VP     │ Leave off for normal play; builds modpacks (§10)    │
└──────────────────────────────┴─────────────────────────────────────────────────────┘
```

**Check:** the main menu lists active mods in the lower right. EUI working but no new units, luxuries, or advanced-setup options means the base game with EUI only — redo from the MODS menu without pressing Back. Trees that look unchanged, or entries with wrong text, mean the game language is not English (§2).

---

## 7. Settings and stability

```
┌────────────────────────┬──────────────────┬────────────────────────────────────────┐
│ Setting                │ Single player    │ Multiplayer                            │
├────────────────────────┼──────────────────┼────────────────────────────────────────┤
│ Autosave frequency     │ 1 turn           │ 1 turn                                 │
│ Max autosaves          │ 0 (unlimited)    │ 500                                    │
│ Leader Scene Quality   │ Minimum          │ Minimum                                │
│ Yield icons            │ Off from the     │ Off from the Industrial era — every    │
│                        │ Industrial era   │ player must do it                      │
└────────────────────────┴──────────────────┴────────────────────────────────────────┘
```

The single-player figures are what the install thread and the bug-report form ask for; the modpack thread asks for 500 in multiplayer. Either way the aim is a save from the turn *before* a problem, which needs frequent autosaves that are not rotated away.

**Late-game crashes are a memory problem, not a Vox Populi bug.** Civ V is 32-bit, and the project attributes most late-game CTDs (crashes to desktop) to address-space exhaustion. Mitigations, most important first: Leader Scene Quality on Minimum; yield icons off from the Industrial era (or avoid zooming far out); standard or small maps; a lower in-game resolution — ultrawide and 4K panels are the demanding case. Proton helps: `PROTON_FORCE_LARGE_ADDRESS_AWARE` is **on by default**, giving the 32-bit executable a 4 GB address space instead of 2 GB. Leave it alone. Upstream's open September 2026 reports fit the same picture — #13344 (crash, likely out of memory) and the draft fix #13372 for intermittent crashes when loading huge maps.

**Early, random crashes are a different problem.** ProtonDB reports tie them to thread count. Under Proton the reported fix is the launch option `taskset -c 0-7 %command%` (**Properties → General → Launch Options**), which pins the game to eight threads; one reporter instead raised `MaxSimultaneousThreads` in the Documents folder's `config.ini` (default 8) to the machine's thread count, and another found that 24 stopped the game starting. Reports that edit that key under `~/.local/share/Aspyr` concern the native build; Proton never reads that file.

Late-game turn times are AI-bound, not GPU-bound. Treat graphics settings as a memory lever rather than a frame-rate one, and cap the frame rate at the panel's refresh rate with VSync.

### Configuration files and performance-first settings

The game keeps its settings as plain-text `.ini` files in the Documents folder (§2) — under Proton, inside the prefix. Edit them with the game closed; the in-game menus write to the same files, and deleting them resets every setting to default at the next start.

```
┌──────────────────────────┬────────────────────────────────────────────────────────────┐
│ config.ini               │ Debugging, logging (§15), audio switches and startup       │
│                          │ parameters — MaxSimultaneousThreads (above) lives here     │
│ UserSettings.ini         │ User options with no menu entry: SkipIntroVideo,           │
│                          │ NoBasicHelp, DisableAdvisorSpeech, AutoWorkersDontReplace  │
│ GraphicsSettingsDX11.ini │ Everything from Options → Video for the DX11 build:        │
│                          │ resolution, fullscreen, VSync, MSAA, the detail levels     │
│ GraphicsSettingsDX9.ini  │ The same for the DX9 build — each build keeps its own      │
└──────────────────────────┴────────────────────────────────────────────────────────────┘
```

Performance-first values for Options → Video, with the key behind each option in `GraphicsSettingsDX11.ini`. Anti-aliasing and terrain tessellation are the items forum benchmarks single out; the rest are ordinary detail levels. Nothing here shortens late-game AI turns — that is CPU time, see above.

```
┌──────────────────────────────┬──────────────────────────┬─────────────────────────────┐
│ Options → Video              │ Key                      │ Performance-first           │
├──────────────────────────────┼──────────────────────────┼─────────────────────────────┤
│ Anti-Aliasing                │ MSAASamples              │ Off (1). Costliest item;    │
│                              │                          │ DX11 AA can also black out  │
│                              │                          │ the screen                  │
│ VSync                        │ WaitForVSync             │ On (1). Caps at the refresh │
│                              │                          │ rate, steadies frame times  │
│ Leader Scene Quality         │ —                        │ Minimum — memory, above     │
│ Terrain Tessellation Level   │ TerrainTessLevel,        │ Low. On a weak GPU also set │
│                              │ BicubicTerrainTessSubdiv │ BicubicTerrainTessSubdiv 0  │
│ Shadow Detail · Terrain      │ ShadowLevel,             │ Low                         │
│ Shadow Quality               │ TerrainShadowQuality     │                             │
│ Water Quality · reflections  │ TerrainWaterQuality,     │ Low · off                   │
│                              │ ReflectionLevel          │                             │
│ High Detail Strategic View   │ HDStrategicView          │ Off (0)                     │
│ Overlay Detail · Fog of War  │ OverlayLevel, FOWLevel,  │ Low                         │
│ · Terrain Detail Level       │ TerrainDetailLevel       │                             │
│ Texture Quality              │ TextureQuality           │ High, unless late-game      │
│                              │                          │ crashes point at memory     │
└──────────────────────────────┴──────────────────────────┴─────────────────────────────┘
```

Three things only the files can do: `MinimizeGrayTiles = 1` in the graphics file stops the gray tiles that appear while scrolling on weaker GPUs (the official Steam FAQ's fix); `SkipIntroVideo = 1` in `UserSettings.ini` skips the opening movie, as the in-game *Skip Intro* option does; and `FullScreen`, `WindowResX` and `WindowResY` under `[UserSettings]` in the graphics file recover a game that opens at a size the screen cannot show — the DX11 build needs at least 768 pixels of height. For wall-clock time rather than frame rate, Options → Game: **Quick Combat** and **Quick Movement** on.

---

## 8. Adding other mods

```
┌────────────────────────────────────────────────────────────────────────────────────┐
│ Any mod that ships its own DLL is incompatible. VP replaces the Civ V DLL          │
│   entirely, and the Community Patch cannot coexist with another DLL mod.           │
│ Steam Workshop subscriptions do not reliably land in the right place under Proton. │
│   Download from CivFanatics or GitHub and extract manually.                        │
│ Mods go in the Documents folder's MODS — inside the prefix, beside the VP folders. │
│ After ANY mod change, delete cache and ModUserData there before launching. Stale   │
│   cache is the single most common reason mods look broken after a change.          │
│ Filenames do not need lowercasing under Proton; that applies to the native build.  │
└────────────────────────────────────────────────────────────────────────────────────┘
```

Check the CivFanatics "MODS compatible with Vox Populi (VP)" thread first: VP reworks most systems, so mods that touch game components are often only partly compatible. Reported working under Proton: Community Events, Improved City View (Gazebo's version), most of WHoward's Pick'N'Mix, and Info Addict (extra patch needed with EUI) — though the modpack maintainer warns that Info Addict is known to crash from memory overflow (§7).

### Popular mods from CivFanatics

Where they live: the Community Patch Project's **Mods Repository** subforum (Civ5 → Creation & Customization → Project & Mod Development → Community Patch Project). Its sticky says every mod there is meant to work with the current VP; ones that stop working move to the **Mods Archive**. The opening post of each thread holds the download — an attachment, a file-host link or a GitHub repository — and its last page holds the compatibility reports for 5.4.6. A mod's page under the site's **Downloads** section, where one exists, can lag the thread by years (Even More Resources: v8 from 2021 there). The most-viewed threads, in that order:

```
┌─────────────────────────────────────────┬───────────────┬────────────────────────────────┐
│ Mod                                     │ Author        │ What it is                     │
├─────────────────────────────────────────┼───────────────┼────────────────────────────────┤
│ More Wonders for VP                     │ adan_eslavo   │ Extra wonders; continues       │
│                                         │               │ Infixo's VP Wonders Expanded   │
│ Improved City View — VP with EUI        │ Infixo        │ City screen rework; EUI        │
│                                         │               │ installs only                  │
│ Even More Resources for VP              │ HungryForFood │ 21 resources incl. city-state  │
│                                         │               │ luxuries; also on GitHub       │
│ Hokath's Proposals                      │ hokath        │ Bundle of balance proposals    │
│ Unique City-States, adan_eslavo         │ adan_eslavo   │ Unique traits for city-states  │
│   expansion                             │               │                                │
│ Dolen2's Ethnic Diversity               │ Dolen2        │ Culture-specific unit art      │
│ Cultural Components, 5/6 UC             │ hokath        │ Fifth and sixth unique         │
│                                         │               │ components per civilization    │
│ Enlightenment Era for VP                │ hokath        │ An era between Renaissance     │
│                                         │               │ and Industrial                 │
│ Various Gameplay Tweaks                 │ balparmak     │ Naval supply, veterancy, less  │
│                                         │               │ micromanagement                │
│ Semper Fidelis                          │ hokath        │ Ideologies expansion pack      │
└─────────────────────────────────────────┴───────────────┴────────────────────────────────┘
```

Custom civilizations fill the rest of the list (The Goths, Duchy of Brittany, Kingdom of Scotland, Louisiana and more); the sticky *Map of Compatible Civilizations for VP* charts which ones work.

Installing one:

1. Download from the opening post. Skip the Steam Workshop copy where one exists — Workshop subscriptions are unreliable under Proton.
2. Extract the archive with your archiver. A `.civ5mod` is a 7-Zip archive under another name; rename it to `.7z` if the archiver refuses it. The game can unpack a `.civ5mod` left in `MODS` when the MODS menu opens, but the forum reports that as hit-or-miss, so extract it yourself.
3. Move the folder that contains the `.modinfo` file straight into the Documents folder's `MODS`, beside the numbered VP folders — not the archive, not a folder inside a folder. Many mods carry a prefix such as `(7a)` so they sort after VP's own entries.
4. Delete `cache` and `ModUserData`.
5. In the MODS menu, enable the VP set first, then the new mod; it lists what it requires — nearly all need (1) and (2), Improved City View needs (3a) — then NEXT.

For multiplayer the mod must be part of the modpack (§10); a mod enabled through the menu cannot join a modpack game.

---

## 9. Community Patch options

The options file is `MODS/(1) Community Patch/Database Changes/NewCustomModOptions.xml` in the Documents folder. Rules from its own header: an option with a **"See also:"** comment must be enabled through its mod, not here; one listing **"Defines:"** or **"PostDefines:"** needs those defines verified first; anything else is enabled by changing `Value` from `0` to `1`. Each row's `Class` (0 Data … 6 Major) says what kind of option it is, not how safe it is; leave Class 3, Events, alone unless you need it — those run game-core triggers whether or not anything consumes them. Of the full option set — 29 API-class rows alone — these four are the ones most people want:

```
┌──────────────────────────┬─────────────────────────────────────────────────────────┐
│ ENABLE_ACHIEVEMENTS      │ Steam achievements in modded single-player. Marked      │
│                          │ "FUNCTIONALITY NOT GUARANTEED" and it changes the       │
│                          │ savegame format — set it before a campaign, not during  │
│ DIPLO_DEBUG_MODE         │ Reveals the AI's true opinion, approach and Congress    │
│ (+ …_SETTING)            │ voting; at setting 2 the AI accepts every Discuss       │
│                          │ request. The best window into how VP scores you         │
│ SQLITE_LOGGING           │ Writes gameplay statistics to a queryable stats.db      │
│ CORE_DEBUGGING           │ Extra game-core debugging; slows the game, leave off    │
└──────────────────────────┴─────────────────────────────────────────────────────────┘
```

Delete `cache` after editing, and keep a copy of the file — the installer rewrites these folders on every update. Use `ENABLE_ACHIEVEMENTS` rather than the executable-patching method used for other modded Civ V setups; map-type achievements are broken on the native Linux build regardless of mods, but work under Proton.

---

## 10. Multiplayer

VP cannot be played in multiplayer through the MODS menu; it must be packaged as a modpack that loads automatically as a DLC.

**Preferred — a prebuilt modpack.** The CivFanatics modpack thread tracks current releases (5.4.6), including one generated on Linux under Proton. Extract so the modpack folder sits directly under the game folder's `Assets/DLC`, then delete the Documents folder's `cache`. Modpacks also work in single player and are easier to update than a mods-menu install.

**Or build your own:** enable `(5) Modpack Maker for VP` plus every mod to include → start or load a game → **Ctrl-Shift-M** → check `Logs/Lua.log` in the Documents folder for errors → exit → delete `cache` → relaunch and start from Single Player or Multiplayer, never the MODS menu.

**Or let civ5vp-installer build one** (§14).

```
┌────────────────────────────────────────────────────────────────────────────────────┐
│ Modpacks cannot be combined with mods activated through the MODS menu              │
│ Every player must use byte-identical modpacks                                      │
│ Every player must delete cache BEFORE EVERY LAUNCH, or the game will most likely   │
│   crash after the first turn                                                       │
│ Do not hand-edit Assets/DLC/VP_MODPACK — rebuild instead                           │
│ Saves do not record which modpack was used; a mismatch crashes                     │
│ Multiplayer autosaves live in Saves/multi/auto — collect them for desync reports   │
└────────────────────────────────────────────────────────────────────────────────────┘
```

Remove a modpack by deleting `Assets/DLC/VP_MODPACK` and `cache`.

An open upstream report, Community-Patch-DLL #13349 (2026-09-05), describes a multiplayer desync on 5.4.x that its reporter suspects lies in pathfinding; check its state before starting a long multiplayer game. MPPatch, which would allow modded multiplayer without modpacks, has had no release since December 2023, and a forum report describes crashes when combined with VP plus EUI. Modpacks remain the supported route.

---

## 11. Updating

Back up first: copy `Saves` and `MODS` from the Documents folder to somewhere outside the prefix. Saves live inside the Wine prefix, so a prefix reset destroys them too, and the installer rewrites the mod folders, so any edit under `MODS` is lost.

Then run the newer `Vox.Populi.<version>.exe` exactly as in Steps 6–7. It deletes the old mod folders and cache before writing; no manual cleanup.

By the project's versioning rule, saves are compatible when only the third version component changes: 5.4.4 → 5.4.6 keeps them, 5.4.x → 5.5.0 does not.

> **WARNING** — Steam's *Verify integrity of game files* restores the stock `Expansion2.Civ5Pkg`, which VP replaces to fix city-state audio (including the BNW bug where Cape Town uses Almaty's clip). Re-run the VP installer after any verify.

---

## 12. Uninstalling

**Preferred**, because it restores the stock `Expansion2.Civ5Pkg`: re-run the installer, choose **Uninstall all**, and point the Civilization V folder page at the same real install.

**Manual fallback.** Delete these, then verify game files in Steam to restore the stock `Expansion2.Civ5Pkg` — verification leaves added files alone, hence the sound XML in the list:

```
┌──────────────────┬───────────────────────────────────────────────────────────────┐
│ Documents folder │ MODS/(1) Community Patch · MODS/(2) Vox Populi                │
│                  │ MODS/(3a) VP - EUI Compatibility Files                        │
│                  │ MODS/(3b) 43 Civs Community Patch · MODS/(4a) Squads for VP   │
│                  │ MODS/(5) Modpack Maker for VP                                 │
│                  │ cache · ModUserData · Text/VPUI_tips_en_us.xml                │
│ Game folder      │ Assets/DLC/VPUI · Assets/DLC/UI_bc1 · Assets/DLC/VP_MODPACK   │
│                  │ Assets/DLC/Expansion2/Sounds/XML/MinorCivSounds_VoxPopuli.xml │
└──────────────────┴───────────────────────────────────────────────────────────────┘
```

**Full prefix reset** (destroys in-prefix saves — back up first): delete `<library>/steamapps/compatdata/8930`.

---

## 13. Troubleshooting

Ordered by when the failure appears. **Setup and install:**

```
┌────────────────────────────────────────┬──────────────────────────────────────────────────────────────┐
│ Symptom                                │ Cause and fix                                                │
├────────────────────────────────────────┼──────────────────────────────────────────────────────────────┤
│ Steam: "disk write error" while it     │ Steam client bug with case-mismatched depot folders          │
│ downloads the Windows depots (Step 1)  │ (steam-for-linux #13406; the Civ V report is #13436),        │
│                                        │ closed as completed 2026-07-27. Update Steam and retry;      │
│                                        │ reporters created the folder the error names, in that        │
│                                        │ exact case, under steamapps/downloading/8930.                │
│ Crashes on NVIDIA                      │ Two May 2026 ProtonDB reports crash on drivers 580.142       │
│                                        │ and 595.71.05; one reverted the driver. A July report        │
│                                        │ on 580.105.08 runs. Not a VP fault.                          │
│ Protontricks does not list Civ V       │ Prefix absent. Launch once via Proton (Step 3).              │
│ "Proton installation could not be      │ Compat tool is a Steam Linux Runtime entry, not Proton.      │
│ found!"                                │ Re-select a real Proton build (Step 1).                      │
│ Flatpak: "does not appear to have      │ The installer or a library sits outside the folders the      │
│ access to the following directories"   │ Flatpak can reach (§3). Move it, or grant the folder as the  │
│                                        │ message says, then restart Protontricks.                     │
│ "Invalid file magic number"            │ Protontricks older than 1.12.0 cannot read the current       │
│                                        │ appinfo.vdf. Upgrade, or use the Flatpak (§3).               │
│ "command cabextract … returned         │ cabextract trips over a symlink Proton created. Delete       │
│ status 1"                              │ the symlink the error names, then re-run.                    │
│ "Cannot find cabextract"               │ Install the cabextract package.                              │
│ "warning: 64-bit WINEPREFIX"           │ Expected. Civ V is 32-bit. Ignore.                           │
│ Wizard never appears                   │ Wayland focus or a broken Proton build. Check other windows, │
│                                        │ then try Proton Experimental or proton-cachyos-slr.          │
│ "You don't have all required DLCs"     │ The message names the missing packs. Enable every DLC in     │
│                                        │ Steam and repeat the Step 2 check.                           │
│ "did not provide the correct path"     │ Chosen folder has no Assets\DLC child. Point at the game     │
│                                        │ root, not Assets or DLC.                                     │
└────────────────────────────────────────┴──────────────────────────────────────────────────────────────┘
```

**In the game:**

```
┌────────────────────────────────────────┬──────────────────────────────────────────────────────────────┐
│ Symptom                                │ Cause and fix                                                │
├────────────────────────────────────────┼──────────────────────────────────────────────────────────────┤
│ Crash during the first configuring     │ The thread-702075 opening post saw this once; relaunch and   │
│ game data pass                         │ enter MODS again (§6).                                       │
│ VP absent from the in-game mod list    │ Documents page was changed; mods are outside the prefix.     │
│                                        │ Re-run with the default Documents path.                      │
│ Missing textures, broken UI            │ Civ V folder page pointed at a game tree inside the prefix.  │
│                                        │ Re-run, or copy across per Step 8.                           │
│ EUI works, no other VP features        │ Back was pressed in MODS, or (3a) is not enabled on an EUI   │
│                                        │ install. Re-enable all mods, press NEXT.                     │
│ Tech or policy tree unchanged, or      │ Game language is not English. Steam → Properties →           │
│ entries show wrong text                │ Language → English, then delete cache (§2).                  │
│ Mods stale, duplicated or missing      │ Delete cache and ModUserData (§8). Then look for Workshop    │
│                                        │ subscriptions sharing mod IDs, or a mod with its own DLL.    │
│ CustomModOption change has no effect   │ Cache not cleared, or the option needed its defines. §9.     │
│ Still broken after all of the above    │ Minimal install — VP without EUI, no other mods — then add   │
│                                        │ EUI, then other mods, one layer at a time.                   │
│ Random crashes early in a session      │ Thread count (ProtonDB reports). Pin to eight threads        │
│                                        │ with taskset, or align MaxSimultaneousThreads (§7).          │
│ Crash to desktop in the late game      │ 32-bit address-space exhaustion. Apply §7 in order, and      │
│                                        │ keep the game folder's crashlogs for a report (§15).         │
│ Crackling audio                        │ One ProtonDB report: launch option PULSE_LATENCY_MSEC=60     │
│                                        │ %command%.                                                   │
│ Multiplayer crashes after turn 1       │ Cache not cleared before launch by every player (§10).       │
│ DX9 starts despite choosing DX11       │ Proton report #8327, closed as not planned. Launch from the  │
│                                        │ library window, not a shortcut or the tray icon (Step 3).    │
│ DX11 hangs after a few minutes         │ A January 2026 ProtonDB report ran stable with the launch    │
│                                        │ option -dx9; another saw gray screen areas under DX9.        │
└────────────────────────────────────────┴──────────────────────────────────────────────────────────────┘
```

---

## 14. Alternative installer

`github.com/Alpakinator/civ5vp-installer` — a single-file native Linux binary (Apache-2.0) that installs VP without Protontricks. It finds the Civ V folders itself (you can correct them), writes only to the game's MODS, DLC and Text folders and its cache, has a modpack mode, and its Uninstall button restores an unmodded game. Proton-only, like §1. Newer and less tested than the Protontricks route; a fallback if the Inno wizard misbehaves.

```
civ5vp-installer-linux-x86_64 — v0.1.6 (2026-09-15)
sha256  a0b59eb445d23bc9a5206ec0f592a7f81c4cfd40a5f2e0d823737bacbd271baa
```

Verify the digest as in Step 5, mark the file executable (Dolphin: **Properties → Permissions → Is executable**) and open it. Unofficial or in-development versions are compiled locally — about 1.1 GB of build tools once, roughly 5 GB in `~/.local/share/civ5vp-installer`.

> **WARNING** — Install release versions only. On 2026-08-26 the author advised against its "Compile the DLL myself" option, single player included: the compiler it currently uses probably drops loop checks from the C++ code, which can freeze the game.

Packs built by versions before 0.1.6 can show raw text keys for loading tips and EUI options (upstream issue #13364); rebuild and redistribute them with 0.1.6.

---

## 15. Bug reporting

Vox Populi bugs go to the project's GitHub issue tracker, not the forum; errors in this guide go to this repository's tracker. Upstream's bug form is the only route (blank issues are disabled). It requires the mod version, the installed components — the setup type from Step 7 — and a description, and asks for three attachments:

```
┌────────────────────────┬─────────────────────────────────────────────────────────┐
│ Save from one turn     │ Saves in the Documents folder — the reason for the      │
│ before the problem     │ autosave settings in §7                                 │
│ Logs                   │ Logs in the Documents folder, zipped — useless unless   │
│                        │ logging was already on                                  │
│ Crash artifacts, when  │ crashlogs in the game folder, for crashes.log and the   │
│ reporting a crash      │ .dmp, per the issue form. The minidump guide instead    │
│                        │ says dumps land beside the game executable as           │
│                        │ CvMiniDump_*.dmp — check both places.                   │
└────────────────────────┴─────────────────────────────────────────────────────────┘
```

Logging is off by default and must be on *before* the problem occurs. Enable it once per installation in the Documents folder's `config.ini`: set `ValidateGameDatabase`, `LoggingEnabled`, `MessageLog`, `AILog`, `AIPerfLog`, `BuilderAILog` and `PlayerAndCityAILogSplit` to `1`. (Upstream's docs name the folder "Civilization V"; it is "Civilization 5".) Collect logs *before* loading a game — most are erased on load. Turn logging back off when you are not chasing a bug; it rewrites a large directory continuously.

Dumps are not guaranteed under Proton: the DLL loads `dbghelp.dll` from the prefix's `System32`, so it depends on Wine's implementation. If none appears after a crash, say so in the report and attach the logs and save.

The project wiki adds guidance on writing a report, a full changelog, a Lua API reference, and a page on the group movement that `(4a) Squads for VP` builds on.

---

## 16. Path reference

Paths not spelled out elsewhere; `<library>` is the §2 Steam library, `<prefix root>` the first row.

```
┌────────────────────────────┬────────────────────────────────────────────────────────┐
│ Wine prefix root           │ <library>/steamapps/compatdata/8930/pfx/drive_c        │
│ Drive mappings             │ <library>/steamapps/compatdata/8930/pfx/dosdevices     │
│ CP options file            │ Documents folder/MODS/(1) Community Patch/             │
│                            │ Database Changes/NewCustomModOptions.xml               │
│ Crash artifacts            │ Game folder/crashlogs, and CvMiniDump_*.dmp beside the │
│                            │ game executable in the game folder                     │
│ civ5vp-installer data      │ ~/.local/share/civ5vp-installer                        │
│ Phantom tree (stays empty) │ <prefix root>/Program Files (x86)/Steam/steamapps/     │
│                            │ common/Sid Meier's Civilization V                      │
└────────────────────────────┴────────────────────────────────────────────────────────┘
```

---

## 17. Sources

Every row was verified on 2026-09-24 unless it says otherwise.

```
┌──────────────────────────────────────────────┬──────────────────────────────────────────────┐
│ Installer behavior: two-path wizard, setup   │ LoneGazebo/Community-Patch-DLL —             │
│ types, DLC gate, cleanup, Uninstallable=no,  │ VPSetupData.iss, scripts/release.py,         │
│ Expansion2.Civ5Pkg swap, savegame rule       │ Opener.rtf                                   │
│ Mod dependencies and blocked mods, EUI       │ the six modinfo files; (1)/(2)/(3a)          │
│ rules, VPUI/UI_bc1; Squads is QoL; modpack   │ INSTRUCTIONS.txt and MANUAL INSTALL.txt;     │
│ rules and removal; cleanup list              │ ModpackMaker.lua; VPSetupData.iss            │
│ CustomModOptions rules, Class legend, Events │ (1) Community Patch/Database Changes/        │
│ overhead, the four toggles                   │ NewCustomModOptions.xml                      │
│ 1.0.3.279 + all DLC; logging keys; minidump  │ VP README.md, DEVELOPMENT.md,                │
│ location and dbghelp.dll dependency          │ docs/minidumps.md                            │
│ Bug form: required fields, three attachments,│ .github/ISSUE_TEMPLATE/bug_report_v5.yml     │
│ crashlogs path, autosave rationale; wiki     │ and config.yml; Community-Patch-DLL wiki     │
│ 5.4.6 stable, asset name, size, sha256;      │ GitHub releases + release feed (released     │
│ still the newest release                     │ 2026-08-31)                                  │
│ Game language must be English                │ CivFanatics thread 528034 FAQ; modpack       │
│                                              │ thread 685164 OP; (1a) Community Patch –     │
│                                              │ German Workshop page                         │
│ Game-core DLL imports MSVCR90 and MSVCP90    │ CvGameCore_Expansion2.dll, both 5.4.6        │
│                                              │ variants, PE import table                    │
│ Prebuilt modpacks incl. Linux/Proton build;  │ CivFanatics modpack thread 685164 (OP, posts │
│ MP autosaves; library-window launch tip;     │ #721 and #732); Community-Patch-DLL #13349,  │
│ open 5.4.x desync; Info Addict; 2026 crash   │ open; #13344 open; draft PR #13372           │
│ reports                                      │                                              │
│ Late-game CTD from 32-bit memory; the        │ CivFanatics "Start Here" thread 701813       │
│ mitigation order; beta vs stable naming      │                                              │
│ Autosaves; Workshop and DLL conflicts;       │ CivFanatics threads 528034 ("How To          │
│ minimal-install triage; compatibility list   │ Install") and 542679 ("MODS compatible")     │
│ Popular mods: views, authors, prefixes;      │ CivFanatics Mods Repository subforum (549),  │
│ repository and archive rule; Downloads lag   │ pages 1–2; stickies 701787 and               │
│                                              │ 689101; Even More Resources page 28019       │
│ .civ5mod is 7-Zip; the .modinfo folder goes  │ CivFanatics threads 451941, 549219 and       │
│ into MODS; in-game unpacking unreliable      │ 477763; the (7a) Events Overhaul title       │
│ Linux mod handling, cache + ModUserData,     │ CivFanatics thread 702075 (schubman), now    │
│ known-good mods, first pass may crash once;  │ stickied; page 2 posts #21–#23 for the       │
│ runtime-library report; Debian Steam path    │ last two                                     │
│ Achievements via VP's own option; native     │ bmaupin/civ5-cheevos-with-mods README,       │
│ Linux map-achievement breakage               │ citing Community-Patch-DLL #12965            │
│ Steam Linux Runtime breaks Protontricks;     │ TeaDrinkingProgrammer guide and its issue    │
│ original copy-the-Assets workaround          │ #3                                           │
│ Alternative installer: 0.1.6, asset, sha256, │ Alpakinator/civ5vp-installer README,         │
│ data dir, local-build warning, text-key bug  │ CHANGELOG, v0.1.6 assets; CivFanatics        │
│                                              │ thread 704249; Community-Patch-DLL #13364    │
│ MPPatch last release Dec 2023                │ Lymia/MPPatch release feed                   │
│ LARGE_ADDRESS_AWARE default; GAME_DRIVE and  │ ValveSoftware/Proton README and proton       │
│ its 11.0 default; DX9/DX11 launch report     │ script, 9.0 to 11.0; Proton issue #8327      │
│ Protontricks app shortcut and Launcher,      │ Matoking/protontricks README, setup.cfg,     │
│ prefix warning, Flatpak access message,      │ TROUBLESHOOTING.md, 1.14.1 CHANGELOG.md and  │
│ 1.12.0 appinfo.vdf floor, not-Proton message │ source; yad/zenity roles from the Arch       │
│ Flatpak folders and desktop entries          │ optdepends; Flathub manifest (finish-args)   │
│ Winetricks menus, vcrun2008 and corefonts    │ Winetricks/winetricks src/winetricks         │
│ contents, installed items pre-ticked         │ (20260125)                                   │
│ Checksums and Is-executable in Dolphin       │ KDE Dolphin file properties dialog           │
│ Package versions and repositories            │ archlinux.org package DB; mirror.cachyos.org │
│ Ubuntu, Debian and Fedora packages, Steam    │ packages.ubuntu.com, Launchpad;              │
│ setup and default directory; NVIDIA 32-bit   │ packages.debian.org, sources.debian.org;     │
│ libraries on Debian                          │ Bodhi (protontricks-1.13.1-3.fc44); Debian   │
│                                              │ wiki: Steam; RPM Fusion; Flathub;            │
│                                              │ steam-installer 1.0.0.85 source              │
│ Steam default dir and ~/.steam/root          │ Arch Wiki: Steam                             │
│ Steam disk write error on case-mismatched    │ ValveSoftware/steam-for-linux #13406,        │
│ depot folders                                │ #13436                                       │
│ Early-crash thread fix; NVIDIA, DX9/DX11     │ ProtonDB public data export, 2026-09-01:     │
│ and audio reports                            │ 196 reports for app 8930                     │
│ INI files, sections and keys; gray-tile fix; │ PCGamingWiki "Sid Meier's Civilization V";   │
│ resolution recovery; tessellation cost;      │ Steam FAQ thread for app 8930; CivFanatics   │
│ SkipIntroVideo; INI reset                    │ 384886, 382624, 381059, 386447, 644343,      │
│                                              │ 505112, 472949; Steam discussion threads     │
└──────────────────────────────────────────────┴──────────────────────────────────────────────┘
```

---

## 18. Limits

- **Thread 702075 page 1 cannot be fetched verbatim** (metadata only), so its opening post is known from indexed excerpts; page 2 was read in full, last on 2026-09-19. Structural facts come from the Vox Populi source tree, hence the different `Assets/DLC` folder name and the avoidable copy step.
- **The `S:` default was read from Proton's source, not observed**; the wizard refuses a wrong path, and `Z:` always works.
- **The runtime-library step rests on one forum report and the DLL's import table**; it was not reproduced on a clean prefix. It is cheap, and Winetricks skips what is installed.
- **The window routes were read from Protontricks' and Winetricks' sources**, and the file-manager steps describe KDE's Dolphin; other file managers put checksums and the executable bit elsewhere.
- **The performance-first values order options by the cost forum benchmarks reported** (2010–2013 threads, Windows); none were measured under Proton, and the numeric levels behind most detail keys are not documented beyond the samples those threads posted.
- **The popular-mod roster is thread views on the two most recently active pages of the Mods Repository** at verification, and each mod's one-line description comes from its title, opening lines or download page, not from testing under Proton.
- **The English-language requirement is a community FAQ item** (thread 528034, the modpack thread, the German language pack), not an upstream statement; the library-window launch tip is a 2023 modpack-thread report.
- **§3 was checked against package indexes and upstream documentation, not run** on any of those systems; the Protontricks 1.12.0 floor is what matters. Fedora's figure is the newest Bodhi update found for Fedora 44 (January 2026); a later one may exist.
- **ProtonDB's page needs JavaScript**, so its public data export (2026-09-01) was read instead; the §7 and §13 items from it are community reports, not tested fixes.
- **§9 toggles were read from the source file, not exercised.** `ENABLE_ACHIEVEMENTS` is marked "functionality not guaranteed" upstream and changes the savegame format.
- **Upstream disagrees with itself on where crash dumps land** — the issue form says `crashlogs`, the minidump guide says beside the game executable. Both are covered.
- **Written against 5.4.6.** Wizard page order and setup-type names have been stable but are not guaranteed; the sha256 is for 5.4.6 only.
- **Untested paths:** Flatpak and Snap Steam (procedure holds, paths move), the 43-civ variants, MPPatch, and civ5vp-installer's local DLL build.
