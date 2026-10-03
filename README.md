# Vox Populi on CachyOS

**Civilization V + Vox Populi via Steam and Proton**

Revision 10.7.2 · 2026-10-02 · Vox Populi 5.4.6 (stable) · Protontricks 1.14.1-1 · Proton 11.0 · CachyOS with native Steam; other distributions in §3.

No terminal is needed: the work happens in Steam, your package manager (Shelly on CachyOS; Octopi on systems installed before CachyOS 26.04), the Protontricks and Winetricks windows, the installer's wizard and your file manager.

Work through §4–§6 in order: Steps 1–3 prepare the game, Steps 4–8 install Vox Populi and §6 is the first launch. Every step that writes to disk ends in a **Check**; Step 8 checks the two wizard steps, 6 and 7. **WARNING** marks a common failure, **CRITICAL** a step that decides whether this works at all.

Maintained at [github.com/ryanmusante/vox-populi-cachyos](https://github.com/ryanmusante/vox-populi-cachyos); this README and `vox-populi-cachyos.pdf` (print-ready, US Letter) carry the same text.

---

## Contents

**Before you start**

1. [How it works](#1-how-it-works)
2. [Requirements](#2-requirements)
3. [Other distributions](#3-other-distributions)

**Install**

4. [Preparing the game](#4-preparing-the-game)
5. [Installing Vox Populi](#5-installing-vox-populi)
6. [First launch](#6-first-launch)

**Configure and play**

7. [Settings and stability](#7-settings-and-stability)
8. [Community Patch options](#8-community-patch-options)
9. [Adding other mods](#9-adding-other-mods)
10. [Multiplayer](#10-multiplayer)

**Maintain**

11. [Updating](#11-updating)
12. [Uninstalling](#12-uninstalling)

**When something fails**

13. [Troubleshooting](#13-troubleshooting)
14. [Alternative installer](#14-alternative-installer)
15. [Bug reporting](#15-bug-reporting)

**Reference**

16. [Path reference](#16-path-reference)
17. [Sources](#17-sources)
18. [Limits](#18-limits)

---

## 1. How it works

**Proton is mandatory.** Vox Populi ships a Windows game-core DLL that the native Aspyr Linux build cannot load.

**The installer is Inno Setup 6** (Installer Version 1.2), a plain Win32 wizard that needs no .NET and renders correctly under Proton.

**It asks for two different paths.**

```
┌─────────────────────────┬────────────────────────────┬─────────────────────────────────┐
│ Wizard page             │ Resolves to                │ Receives                        │
├─────────────────────────┼────────────────────────────┼─────────────────────────────────┤
│ Select Destination      │ {userdocs}\My Games\       │ MODS\(1) Community Patch        │
│   Location              │ Sid Meier's Civilization 5 │ MODS\(2) Vox Populi             │
│ — the Documents path    │                            │ MODS\(3a) VP - EUI              │
│                         │ Under Proton:              │   Compatibility Files           │
│ LEAVE AS IS             │ C:\users\steamuser\        │ MODS\(3b) 43 Civs Community     │
│                         │ Documents\… in the prefix  │   Patch                         │
│                         │                            │ MODS\(4a) Squads for VP         │
│                         │                            │ MODS\(5) Modpack Maker for VP   │
│                         │                            │ Text\VPUI_tips_en_us.xml        │
├─────────────────────────┼────────────────────────────┼─────────────────────────────────┤
│ Select the Civilization │ Whatever you browse to —   │ Assets\DLC\VPUI (VP setups)     │
│   V folder              │ the REAL game install,     │ Assets\DLC\UI_bc1 (EUI setups)  │
│ — custom page, after    │ reached through S:\ or Z:\ │ Assets\DLC\Expansion2\          │
│   Setup Type            │ (Wine maps Z: to /)        │   Expansion2.Civ5Pkg (replaced) │
│                         │                            │ Assets\DLC\Expansion2\Sounds\   │
│ SET THIS ONE            │                            │   XML\                          │
│                         │                            │   MinorCivSounds_VoxPopuli.xml  │
└─────────────────────────┴────────────────────────────┴─────────────────────────────────┘
```

The Documents default is already correct: it resolves inside the prefix, where the Windows build reads mods. On a clean prefix the Civilization V folder page arrives blank; the wizard fills it in from the registry or `C:\Program Files (x86)\Steam\…` only when such a game tree exists inside the prefix.

> **CRITICAL** — Aim that page at the real Linux install and nothing needs copying. The wizard refuses a folder without `Assets\DLC` and all ten DLC folders, so on a clean prefix a wrong path is blocked, not installed. A game tree already inside the prefix — a copied install, say — passes that test, and the UI assets then land where the game never reads them.

**Every DLC must be installed, not merely owned.** The wizard blocks unless `DLC_01`–`DLC_07`, `DLC_Deluxe` (Babylon), `Expansion` (Gods & Kings) and `Expansion2` (Brave New World) all exist — in practice the Complete Edition on Civ V 1.0.3.279.

**It cleans up after itself.** Before writing, it deletes the Documents folder's `cache` and `Text\VPUI_tips_en_us.xml`, the game folder's `VPUI`, `UI_bc1`, `Expansion2.Civ5Pkg` and sound XML, and the current VP mod folders. It also removes every legacy folder name back to the CBP/CBO/CSD era, including `(4) Civ IV Diplomatic Features`, `(5) More Luxuries`, the `(6x)` compatibility files, `(7a) Promotion Icons for VP` and `(7b) UI - Promotion Tree for VP`. Clear nothing by hand, and expect any edit inside a VP mod folder to be lost at the next install or update.

**There is no uninstaller entry** (`Uninstallable=no`). Removal is the **Uninstall all** setup type in the same `.exe`.

**Releases are stable or beta.** Each links a release-notes thread titled *New STABLE Version* or *New BETA Version*; 5.4.6 (2026-08-31) is stable.

---

## 2. Requirements

```
┌──────────────┬─────────────────────────────────────────────────────────────────────────┐
│ Item         │ Requirement                                                             │
├──────────────┼─────────────────────────────────────────────────────────────────────────┤
│ Game         │ Civilization V 1.0.3.279, all expansions and all DLC installed          │
│ Steam        │ Native Steam (multilib steam); data dir ~/.local/share/Steam, also      │
│              │ reachable through ~/.steam/root. Other distributions: §3                │
│ Compat tool  │ Proton Experimental or newest numbered Proton (11.0 at this revision);  │
│              │ proton-cachyos-slr OK. NOT a "Steam Linux Runtime" entry (Step 1)       │
│ Language     │ Game language English (Steam → Properties → Language). VP ships its     │
│              │ text in English only; other languages show missing or stale entries     │
│ Tooling      │ extra/protontricks 1.14.1-1 with yad (its game list) and zenity (a      │
│              │ Winetricks dialog tool, already a steam dependency); it pulls           │
│              │ winetricks, which pulls wine and cabextract                             │
│ Disk         │ Full re-download of the Windows depots, plus the 110 MB installer       │
│ Conflicts    │ VP's modinfo files block More Luxuries, CSD for VP, Civ IV Diplomatic   │
│              │ Features (both), Artificial Unintelligence, Bridges and Canals, and     │
│              │ standalone Squads for VP; remove them, Workshop copies included. No     │
│              │ other mod may ship its own DLL                                          │
└──────────────┴─────────────────────────────────────────────────────────────────────────┘
```

Every path in this guide sits in one of three folders. Steam → right-click **Sid Meier's Civilization V** → **Properties → Installed Files → Browse** opens the game folder; the library is the folder above `steamapps`, and `compatdata/8930` always sits in the same library as the game. Show hidden files in your file manager — `.local` and `.steam` are hidden.

```
┌──────────────────┬─────────────────────────────────────────────────────────────────────┐
│ Folder           │ Location and contents                                               │
├──────────────────┼─────────────────────────────────────────────────────────────────────┤
│ Steam library    │ ~/.local/share/Steam — native Steam's default on CachyOS; a game in │
│                  │ a secondary library uses that library. Other packagings: §3         │
│ Game folder      │ <library>/steamapps/common/Sid Meier's Civilization V               │
│ Documents folder │ <library>/steamapps/compatdata/8930/pfx/drive_c/users/steamuser/    │
│                  │ Documents/My Games/Sid Meier's Civilization 5 — mods, saves, logs,  │
│                  │ cache and config.ini of the Windows build                           │
└──────────────────┴─────────────────────────────────────────────────────────────────────┘
```

---

## 3. Other distributions

Other distributions differ from CachyOS in three things — how Steam is installed, where its default library sits and whether their own Protontricks is recent enough; from Step 1 on, the procedure is the same. **Protontricks 1.12.0 is the floor**: older releases cannot read the current Steam client's `appinfo.vdf` and stop with "Invalid file magic number".

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
┌────────────────────────────────┬───────────────────────────────────────────────────────┐
│ Steam packaging                │ Default library                                       │
├────────────────────────────────┼───────────────────────────────────────────────────────┤
│ Native package, any distro     │ what ~/.steam/root links to                           │
│   Arch, CachyOS                │   ~/.local/share/Steam                                │
│   Debian, Ubuntu               │   ~/.steam/debian-installation for a new install;     │
│                                │   older installs may sit in ~/.local/share/Steam      │
│ Flatpak Steam                  │ ~/.var/app/com.valvesoftware.Steam/.local/share/Steam │
│ Snap Steam                     │ ~/snap/steam/common/.local/share/Steam                │
└────────────────────────────────┴───────────────────────────────────────────────────────┘
```

**Debian and Ubuntu** need the i386 architecture enabled and the `multiverse` (Ubuntu) or `contrib` (Debian) component before `steam-installer` is offered; Debian also wants the 32-bit Mesa packages that the Debian wiki's Steam page lists, or NVIDIA's 32-bit driver libraries (`nvidia-driver-libs:i386`) with the proprietary driver. **Fedora** takes Steam from RPM Fusion Nonfree and Protontricks from Fedora itself.

Where the table says **Flatpak**, add Flathub, install `com.github.Matoking.protontricks` and restart; on the **Steam Deck**, use Desktop Mode's Discover — SteamOS's root is read-only. Flatpak Protontricks reaches Steam's own directories plus the standard Desktop, Documents, Downloads, Music, Pictures and Videos folders, and ships the same app shortcut and Launcher entries; a library or installer elsewhere makes it report the folders it cannot reach and how to grant them.

Then, on any of them: start Steam once and sign in so it creates its directories, confirm in your package manager that Protontricks is 1.12.0 or newer, and continue with Step 1.

---

## 4. Preparing the game

Steps 1–3 switch Civ V to its Windows build under Proton, make sure every DLC is installed and create the Wine prefix.

### Step 1 · Force Proton

Steam → right-click **Sid Meier's Civilization V** → **Properties → Compatibility** → tick **Force the use of a specific Steam Play compatibility tool** → Proton Experimental or the newest numbered Proton. Steam swaps the native build for the Windows depots; let it finish. If the download stops with a disk write error, see §13.

> **WARNING** — The same dropdown lists Steam Linux Runtime entries. They are not Proton: no Wine prefix is created, and Protontricks stops with "Proton installation could not be found!".

CachyOS alternatives (install the package, restart Steam): `proton-cachyos-slr`, `proton-cachyos-native`, or `protonup-qt` / `protonplus` for Proton-GE. Prefer `-slr` — a Proton build that runs inside the Steam Linux Runtime container, not one of the bare *Steam Linux Runtime* entries above.

Proton 11.0 and Experimental map an `S:` drive to the game's Steam library, which shortens the path typed in Step 7; on Proton 10.0 or older the launch option `PROTON_SET_GAME_DRIVE=1 %command%` (**Properties → General → Launch Options**) does the same. If the library root is not writable or is on another filesystem, `S:` points at `steamapps` itself and the wizard path becomes `S:\common\…`; the `Z:\` path always works.

**Check:** the game folder holds `CivilizationV_DX11.exe` among other `.exe` files. No `.exe` at all, and a `Civ5XP` binary instead, means Steam is still serving the native build.

### Step 2 · Confirm every DLC

Steam → right-click **Sid Meier's Civilization V** → **Properties → DLC** → tick every entry.

**Check:** `Assets/DLC` in the game folder holds all ten — `DLC_01`–`DLC_07`, `DLC_Deluxe`, `Expansion` and `Expansion2`. A missing one stops the installer at Step 7.

### Step 3 · Launch once, then quit

Press **Play** in the Steam library window and pick **Play Sid Meier's Civilization V (DirectX 10/11)**, the DX11 build, from Steam's launch options; the game's own launcher was retired in November 2024. Wait for the main menu, then quit. This first launch creates the prefix, `compatdata/8930/pfx`, without which Protontricks cannot see the game, and builds Civ V's Documents folder inside it. A desktop shortcut or the tray icon starts the default DX9 build without asking, and the DX9 executable may start regardless (Proton #8327); neither affects Vox Populi.

**Check:** the Documents folder exists and holds `Logs`, `Saves` and `config.ini` among others.

---

## 5. Installing Vox Populi

Steps 4–8 add the runtime libraries, verify the installer, run it inside the prefix and check where it put the files.

### Step 4 · Install Protontricks and the runtime libraries

Install `protontricks` and `yad` from the CachyOS repositories; `zenity` is already there as a Steam dependency (other distributions: §3). Then:

1. Open **Protontricks** from the application menu and pick **Sid Meier's Civilization V** — it is listed only after Step 3.
2. In the Winetricks window that opens, choose **Select the default wineprefix**.
3. **Install a Windows DLL or component** → tick `vcrun2008` → **OK**.
4. **Install a font** → tick `corefonts` → **OK**.

A warning that you are using a 64-bit WINEPREFIX is expected — Winetricks shows it for every 64-bit prefix, and every Proton prefix is one — so close it. Both installs download, so this needs network access; Winetricks skips what is already installed, so repeating the step is harmless.

`CvGameCore_Expansion2.dll` (both 5.4.6 variants) imports `MSVCR90.dll` and `MSVCP90.dll`, the Visual C++ 2008 runtime that `vcrun2008` installs; Wine's built-in copies usually suffice, but a report in [thread 702075](https://forums.civfanatics.com/threads/702075/) had VP working only after this step. `corefonts` adds the eleven Microsoft Core fonts for the Web and needs `cabextract`.

**Check:** opened again, those two lists show `vcrun2008` and `corefonts` already ticked.

### Step 5 · Download and verify

Download `Vox.Populi.<version>.exe` from the release's assets at [github.com/LoneGazebo/Community-Patch-DLL/releases](https://github.com/LoneGazebo/Community-Patch-DLL/releases); the asset list shows each file's digest. `Release_Debug.zip` beside it is a debug DLL for crash reports, not needed for play. For 5.4.6:

```
Vox.Populi.5.4.6.exe — 110,481,637 bytes
sha256  31679423e55d7f64ba9693d8252d9e29f824fd9f8da043af547470068f98ba8a
```

**Check:** the digest your file manager computes matches (Dolphin: **Properties → Checksums**, paste the digest and it confirms the match). A mismatch means delete the file and download it again; do not run it.

### Step 6 · Run the installer inside the prefix

Right-click `Vox.Populi.5.4.6.exe` → **Open With → Protontricks Launcher** → **Sid Meier's Civilization V**. The wizard can take a minute to appear and may open behind other windows under Wayland.

### Step 7 · Complete the wizard

The actions in capitals decide the result:

```
┌──────────────────────────────┬─────────────────────────────────────────────────────────┐
│ Wizard page                  │ Action                                                  │
├──────────────────────────────┼─────────────────────────────────────────────────────────┤
│ License · Information        │ Accept the agreement, then Next on both                 │
│ Select Destination Location  │ LEAVE UNCHANGED. It must read C:\users\steamuser\       │
│                              │ Documents\My Games\Sid Meier's Civilization 5           │
│ Setup Type / Components      │ CHOOSE one setup type — table below                     │
│ Select the Civilization V    │ TYPE S:\steamapps\common\Sid Meier's Civilization V,    │
│   folder                     │ or the Z:\ form of the game folder given below          │
│ Ready to Install             │ CHECK that "Civilization V path" shows the S:\ or Z:\   │
│                              │ path, not C:\Program Files (x86)\…                      │
│ Installing · Finished        │ Wait, then Finish                                       │
└──────────────────────────────┴─────────────────────────────────────────────────────────┘
```

The setup types, in the installer's order:

```
┌────────────────────────────────┬───────────────────────────────────────────────────────┐
│ Setup type                     │ What it installs                                      │
├────────────────────────────────┼───────────────────────────────────────────────────────┤
│ Vox Populi (with EUI)          │ Full VP + Enhanced User Interface — the usual choice  │
│ Vox Populi (no EUI)            │ Full VP, stock interface                              │
│ Community Patch only           │ AI and bugfix base mod. NOT compatible with EUI       │
│ 43 Civ Community Patch only,   │ The same three with a DLL supporting 43 major civs    │
│   43 Civ Vox Populi (no EUI),  │                                                       │
│   43 Civ Vox Populi (with EUI) │                                                       │
│ Uninstall all                  │ Removal — §12                                         │
└────────────────────────────────┴───────────────────────────────────────────────────────┘
```

The `Z:\` form is the game folder with `Z:` in front and backslashes instead of slashes; for the default library:

```
Z:\home\<your user name>\.local\share\Steam\steamapps\common\Sid Meier's Civilization V
```

If the Ready page shows a `C:\Program Files (x86)` path, go back and fix it rather than copy files afterwards. The information page warns that the installer fails if `MODS` has been moved out of the Documents folder; under Proton that only happens if you change the Documents page.

### Step 8 · Verify placement

**Check:** `Assets/DLC` in the game folder now holds `VPUI`, plus `UI_bc1` with EUI — the pass/fail test for Step 7. Community Patch only installs neither, but adds `Expansion2/Sounds/XML/MinorCivSounds_VoxPopuli.xml` there. The Documents folder's `MODS` holds the numbered mod folders of your variant.

If `<library>/steamapps/compatdata/8930/pfx/drive_c/Program Files (x86)/Steam/steamapps/common/Sid Meier's Civilization V` exists, a game tree inside the prefix took the files: re-run the installer with the correct path, or copy that tree's `Assets` folder over the real game folder's.

---

## 6. First launch

1. Launch Civ V from the Steam library window, as in Step 3.
2. Main menu → **MODS**; accept the prompt about DLC being disabled and the game restarting.
3. The first entry into the MODS menu runs a "configuring game data" pass of 5–15 minutes — not a hang. Thread 702075's opening post reports it may crash once and work after a relaunch.
4. Enable the VP mods the installer placed in `MODS` — the table below.
5. Press **NEXT**, never **Back**.
6. **Single Player → Set Up Game**.

```
┌────────────────────────────────┬───────────────────────────────────────────────────────┐
│ Mod                            │ When to enable it                                     │
├────────────────────────────────┼───────────────────────────────────────────────────────┤
│ (1) Community Patch            │ Always — every other VP mod requires it               │
│ (2) Vox Populi                 │ Requires (1) Community Patch                          │
│ (3a) VP - EUI Compatibility    │ MUST be enabled on any EUI install, or VP will not    │
│   Files                        │ function                                              │
│ (3b) 43 Civs Community Patch   │ Only for 43-civ variants                              │
│ (4a) Squads for VP             │ Optional QoL — RTS-style control groups and group     │
│                                │ movement                                              │
│ (5) Modpack Maker for VP       │ Leave off for normal play; builds modpacks (§10)      │
└────────────────────────────────┴───────────────────────────────────────────────────────┘
```

> **CRITICAL** — Back returns to the main menu and silently deactivates the mod set — the most common "I installed VP and nothing changed" report.

**Check:** the main menu lists active mods in the lower right. EUI working but no new units, luxuries or advanced-setup options means the base game with EUI only — redo from the MODS menu without pressing Back. Trees that look unchanged, or entries with wrong text, mean the game language is not English (§2).

---

## 7. Settings and stability

### Recommended settings

```
┌────────────────────────┬────────────────────┬──────────────────────────────────────────┐
│ Setting                │ Single player      │ Multiplayer                              │
├────────────────────────┼────────────────────┼──────────────────────────────────────────┤
│ Autosave frequency     │ 1 turn             │ 1 turn                                   │
│ Max autosaves          │ 0 (unlimited)      │ 500                                      │
│ Leader Scene Quality   │ Minimum            │ Minimum                                  │
│ Yield icons            │ Off from the       │ Off from the Industrial era — every      │
│                        │ Industrial era     │ player must do it                        │
└────────────────────────┴────────────────────┴──────────────────────────────────────────┘
```

Single-player values follow the install thread and the bug-report form; the modpack thread asks for 500 autosaves in multiplayer. The aim is a save from the turn *before* a problem, which needs frequent autosaves that are never rotated away.

### Crashes

**Late-game crashes are a memory problem, not a Vox Populi bug.** Civ V is 32-bit, and the project attributes most late-game CTDs (crashes to desktop) to address-space exhaustion. Mitigations, most important first:

1. Leader Scene Quality on Minimum.
2. Yield icons off from the Industrial era, or avoid zooming far out.
3. Standard or small maps.
4. A lower in-game resolution — ultrawide and 4K panels are the demanding case.

Proton already helps: `PROTON_FORCE_LARGE_ADDRESS_AWARE` is **on by default**, giving the 32-bit executable a 4 GB address space instead of 2 GB; leave it alone. Upstream tracks the same picture in #13344 (crash, likely out of memory) and the draft fix #13372 (intermittent crashes loading huge maps).

**Early, random crashes are a different problem.** ProtonDB reports tie them to thread count. Under Proton the reported fix is the launch option `taskset -c 0-7 %command%` (**Properties → General → Launch Options**), which pins the game to eight threads. One reporter instead raised `MaxSimultaneousThreads` in the Documents folder's `config.ini` (default 8) to the machine's thread count; another found that 24 stopped the game starting. Reports that edit that key under `~/.local/share/Aspyr` concern the native build; Proton never reads that file.

### Performance and configuration files

Late-game turn times are AI-bound, not GPU-bound. Treat graphics settings as a memory lever rather than a frame-rate one, and cap the frame rate at the panel's refresh rate with VSync.

The game keeps its settings as plain-text `.ini` files in the Documents folder (§2) — under Proton, inside the prefix. Edit them with the game closed; the in-game menus write to the same files, and deleting them resets every setting to default at the next start.

```
┌──────────────────────────┬─────────────────────────────────────────────────────────────┐
│ File                     │ What it holds                                               │
├──────────────────────────┼─────────────────────────────────────────────────────────────┤
│ config.ini               │ Debugging, logging (§15), audio switches and startup        │
│                          │ parameters — MaxSimultaneousThreads (above) lives here      │
│ UserSettings.ini         │ User options — SkipIntroVideo, NoBasicHelp,                 │
│                          │ DisableAdvisorSpeech and AutoWorkersDontReplace among them  │
│ GraphicsSettingsDX11.ini │ Everything from Options → Video for the DX11 build:         │
│                          │ resolution, fullscreen, VSync, MSAA, the detail levels      │
│ GraphicsSettingsDX9.ini  │ The same for the DX9 build — each build keeps its own       │
└──────────────────────────┴─────────────────────────────────────────────────────────────┘
```

Performance-first values for Options → Video, with the key behind each option in `GraphicsSettingsDX11.ini`. Forum benchmarks single out anti-aliasing and terrain tessellation; the rest are ordinary detail levels.

```
┌──────────────────────────────┬──────────────────────────┬──────────────────────────────┐
│ Options → Video              │ Key                      │ Performance-first            │
├──────────────────────────────┼──────────────────────────┼──────────────────────────────┤
│ Anti-Aliasing                │ MSAASamples              │ Off (1). Costliest item;     │
│                              │                          │ DX11 AA can also black out   │
│                              │                          │ the screen                   │
│ VSync                        │ WaitForVSync             │ On (1). Caps at the refresh  │
│                              │                          │ rate, steadies frame times   │
│ Leader Scene Quality         │ —                        │ Minimum — memory, above      │
│ Terrain Tessellation Level   │ TerrainTessLevel,        │ Low. On a weak GPU also set  │
│                              │ BicubicTerrainTessSubdiv │ BicubicTerrainTessSubdiv 0   │
│ Shadow Detail · Terrain      │ ShadowLevel,             │ Low                          │
│   Shadow Quality             │ TerrainShadowQuality     │                              │
│ Water Quality · reflections  │ TerrainWaterQuality,     │ Low · off                    │
│                              │ ReflectionLevel          │                              │
│ High Detail Strategic View   │ HDStrategicView          │ Off (0)                      │
│ Overlay Detail · Fog of War  │ OverlayLevel, FOWLevel,  │ Low                          │
│   · Terrain Detail Level     │ TerrainDetailLevel       │                              │
│ Texture Quality              │ TextureQuality           │ High, unless late-game       │
│                              │                          │ crashes point at memory      │
└──────────────────────────────┴──────────────────────────┴──────────────────────────────┘
```

Two things only the files can do. `MinimizeGrayTiles = 1` in the graphics file stops the gray tiles that appear while scrolling on weaker GPUs (the official Steam FAQ's fix). `FullScreen`, `WindowResX` and `WindowResY` under `[UserSettings]` in the graphics file recover a game that opens at a size the screen cannot show; the DX11 build needs at least 768 pixels of height. `SkipIntroVideo = 1` in `UserSettings.ini` is the file form of the in-game *Skip Intro* option; the movie plays while the game loads, so skipping it leaves a black screen rather than saving time. For wall-clock time rather than frame rate, turn on **Quick Combat** and **Quick Movement** under Options → Game.

---

## 8. Community Patch options

The options file is `MODS/(1) Community Patch/Database Changes/NewCustomModOptions.xml` in the Documents folder. Its header sets three rules:

- An option with a **"See also:"** comment is enabled through its mod, not here.
- One listing **"Defines:"** or **"PostDefines:"** needs those defines verified first.
- Anything else is enabled by changing `Value` from `0` to `1`.

A row's `Class` (0 Data … 6 Major) describes what kind of option it is, not how safe it is. Leave Class 3, Events, alone unless you need it: those options run game-core triggers whether or not anything consumes them. The four options most people want:

```
┌──────────────────────────┬─────────────────────────────────────────────────────────────┐
│ Option                   │ Effect                                                      │
├──────────────────────────┼─────────────────────────────────────────────────────────────┤
│ ENABLE_ACHIEVEMENTS      │ Steam achievements in modded single player. Marked          │
│                          │ "FUNCTIONALITY NOT GUARANTEED" and it changes the savegame  │
│                          │ format — set it before a campaign, not during               │
│ DIPLO_DEBUG_MODE         │ Reveals the AI's true opinion, approach and Congress        │
│   (+ …_SETTING)          │ voting; at setting 2 the AI accepts every Discuss request   │
│ SQLITE_LOGGING           │ Writes gameplay statistics to a queryable stats.db          │
│ CORE_DEBUGGING           │ Extra game-core debugging; slows the game, leave off        │
└──────────────────────────┴─────────────────────────────────────────────────────────────┘
```

Delete `cache` after editing, and keep a copy of the file — the installer rewrites these folders on every update. Use `ENABLE_ACHIEVEMENTS` rather than the executable-patching method used for other modded Civ V setups; map-type achievements are broken on the native Linux build regardless of mods, but work under Proton.

---

## 9. Adding other mods

```
┌────────────────────────────────────────────────────────────────────────────────────────┐
│ Rules for every added mod                                                              │
├────────────────────────────────────────────────────────────────────────────────────────┤
│ Any mod that ships its own DLL is incompatible. VP replaces the Civ V DLL entirely,    │
│   and the Community Patch cannot coexist with another DLL mod.                         │
│ VP reworks most systems: anything beyond maps, civilizations, art or interface needs   │
│   a version made for VP.                                                               │
│ Steam Workshop subscriptions do not reliably land in the right place under Proton.     │
│   Download from CivFanatics or GitHub and extract manually.                            │
│ Mods go in the Documents folder's MODS — inside the prefix, beside the VP folders.     │
│ After ANY mod change, delete cache and ModUserData there before launching. Stale cache │
│   is the most common reason mods look broken.                                          │
│ Filenames do not need lowercasing under Proton; that applies to the native build.      │
└────────────────────────────────────────────────────────────────────────────────────────┘
```

### Where compatible mods live

Mods for VP are posted in the Community Patch Project's [**Mods Repository**](https://forums.civfanatics.com/forums/549/) subforum (Civ5 → Creation & Customization → Project & Mod Development → Community Patch Project). Its sticky [*Mod Compatibility with Latest Version*](https://forums.civfanatics.com/threads/701787/) (January 2026) says every mod there is meant to work with the current VP and asks for problem reports, with logs, in each mod's thread; mods that stop working move to the **Mods Archive**. A thread's opening post holds the download — an attachment, a file-host link or a GitHub repository — and its last pages hold the reports for 5.4.6. Copies in the site's **Downloads** section and on the Steam Workshop can lag by years: Even More Resources' Workshop page carries reports that it fails on VP 3.1.1.

The older list [MODS compatible with Vox Populi (VP)](https://forums.civfanatics.com/threads/542679/) was last edited in March 2022, before VP 5, and now sits in the forum's archive. Thread 702075 reports Community Events, Improved City View, most of WHoward's Pick'N'Mix and Info Addict (an extra patch with EUI) working under Proton, though the modpack maintainer warns that Info Addict is known to crash from memory overflow (§7).

### Most popular compatible mods

The tables list the most viewed mod threads across all six pages of the Mods Repository that show 2026 activity and no reported breakage, most viewed first. Versions are as the authors state them; "thread active 2026" means 2026 posts but no stated VP version. Players' shared mod lists on the forum and on Reddit name the same core: More Wonders, Even More Resources and Unique City-States.

Gameplay and content:

```
┌────────────────────────────────────┬────────────────────────┬──────────────────────────┐
│ Mod and author                     │ What it adds           │ Version, needs, status   │
├────────────────────────────────────┼────────────────────────┼──────────────────────────┤
│ More Wonders for VP                │ New world and natural  │ v24.11, VP 5.3.3+; an    │
│   by adan_eslavo                   │ wonders                │ effects folder goes in   │
│                                    │                        │ the game folder (below)  │
│ Community Events                   │ More random events     │ see also msw1's (7a)     │
│   by Enginseer                     │                        │ VP Events Overhaul,      │
│                                    │                        │ titled for 5.4.6         │
│ Even More Resources for VP         │ New bonus, luxury and  │ also on GitHub; skip     │
│   by HungryForFood                 │ city-state resources   │ the Workshop copy        │
│ New Beliefs                        │ New religious beliefs  │ thread active 2026       │
│   by pineappledan,                 │                        │                          │
│   HungryForFood, Recursive         │                        │                          │
│ Hokath's Proposals                 │ Bundle of balance      │ thread active 2026       │
│   by hokath                        │ proposals              │                          │
│ Unique City-States                 │ Unique traits for      │ v19.3, VP 5.4.x          │
│   by adan_eslavo                   │ city-states            │                          │
│ Cultural Components (5/6 UC)       │ 5th and 6th unique     │ v9, VP 5.4; needs JFD's  │
│   by hokath, gwennog, jarcast2     │ components per         │ Cultural Diversity (1)   │
│                                    │ cultural group         │ (Core) Utilities         │
│ Enlightenment Era for VP           │ An era between         │ thread titled 5.3;       │
│   by hokath                        │ Renaissance and        │ active 2026              │
│                                    │ Industrial             │                          │
│ Various Gameplay Tweaks            │ Separate naval supply, │ thread active 2026       │
│   by balparmak                     │ veterancy, less micro  │                          │
│ JFD's Sovereignty for VP           │ Governments and        │ v15 (2024): fixes only;  │
│   by Troll Warlord                 │ reforms                │ thread active 2026       │
│ Better Lakes for VP                │ Lake rework            │ thread active 2026       │
│   by InkAxis                       │                        │                          │
│ Semper Fidelis                     │ Ideologies expansion   │ thread active 2026       │
│   by hokath                        │ pack                   │                          │
└────────────────────────────────────┴────────────────────────┴──────────────────────────┘
```

Interface, art and tools:

```
┌────────────────────────────────────┬────────────────────────┬──────────────────────────┐
│ Mod and author                     │ What it changes        │ Version, needs, status   │
├────────────────────────────────────┼────────────────────────┼──────────────────────────┤
│ Improved City View                 │ City screen rework     │ EUI installs only;       │
│   by Infixo                        │                        │ needs (3a)               │
│ Dolen2's Ethnic Diversity          │ Culture-specific unit  │ thread active 2026       │
│   by Dolen2                        │ art                    │                          │
│ City-States Leaders for VP         │ Leaders for            │ v26, VP 5.3.x; made      │
│   by adan_eslavo                   │ city-states            │ for Unique City-States   │
│ Trade Opportunities for VP         │ Trade screen rework    │ v26, VP 5.3.x            │
│   by adan_eslavo                   │                        │                          │
│ InGame Editor+ for VP              │ In-game editor for     │ thread active 2026       │
│   by N.Core                        │ map, cities and units  │                          │
│ Wonder Planner for VP              │ Planning screen for    │ v20; More Wonders'       │
│   by adan_eslavo                   │ wonders                │ opening post             │
│                                    │                        │ recommends it            │
│ Unit Scaling and Formation         │ Unit model size and    │ thread active 2026       │
│   for VP, by N.Core                │ formations             │                          │
└────────────────────────────────────┴────────────────────────┴──────────────────────────┘
```

**Custom civilizations** are the repository's largest group. The most viewed are Colonialist Legacies' Inuit, Cambodia, The Goths, G&H's Kingdom of Scotland and MC and LITE's Nubia; the sticky *Map of Compatible Civilizations for VP* charts the rest. Most support JFD's Cultural Diversity, which is how Cultural Components reaches them.

**Map scripts** are the safest additions by the rules above; the most viewed active ones are jarcast2's Bigger Huge Maps (for Communitu_79a and Continental Drift) and axatin's Continental Drift Map Script.

Left out despite their popularity:

```
┌────────────────────────────────┬───────────────────────────────────────────────────────┐
│ Mod                            │ Why it is left out                                    │
├────────────────────────────────┼───────────────────────────────────────────────────────┤
│ More Unique Components         │ Integrated into VP 5; do not install it               │
│   (3/4 UC)                     │                                                       │
│ Civics and Reforms             │ Reported not working in a shared VP mod list          │
│                                │ (August 2025); no thread post since October 2024      │
│ Pineappledan Tweaks for VP     │ No thread post since April 2024                       │
│ Promotion Overhaul for VP      │ Current version built for VP 5.2.x, per its author    │
│ Alternative Component Names    │ Marked outdated by its author                         │
│ Historical Religions Complete  │ Reported in December 2025 to no longer work with VP   │
│   (Steam Workshop)             │                                                       │
│ Maritime Weather+,             │ Crash reports in August 2025; a Maritime Battles      │
│   Maritime Battles+            │ rebuild was pending                                   │
└────────────────────────────────┴───────────────────────────────────────────────────────┘
```

### Installing a mod

1. Download from the opening post, not the Steam Workshop (see the rules above).
2. Extract the archive with your archiver. A `.civ5mod` is a 7-Zip archive under another name; rename it to `.7z` if the archiver refuses it. The game can unpack a `.civ5mod` left in `MODS` when the MODS menu opens, but the forum reports that as hit-or-miss, so extract it yourself.
3. Move the folder that contains the `.modinfo` file straight into the Documents folder's `MODS`, beside the numbered VP folders — not the archive, not a folder inside a folder. Many mods carry a prefix such as `(7a)` so they sort after VP's own entries.
4. Opening posts give Windows paths. One under `steamapps\common\Sid Meier's Civilization V` means the real game folder (§2), never a `C:\Program Files (x86)` tree inside the prefix (Step 8); More Wonders' effects folder, for one, goes into the game folder's `Assets/DLC/Expansion2/DLC`.
5. Delete `cache` and `ModUserData`.
6. In the MODS menu, enable the VP set first, then the new mod; it lists what it requires — nearly all need (1) and (2), Improved City View needs (3a), Cultural Components needs JFD's Cultural Diversity (1) (Core) Utilities — then **NEXT**.

Add one mod at a time and play a few turns before the next, so a crash points at the last one added (§13). For multiplayer the mod must be part of the modpack (§10); a mod enabled through the menu cannot join a modpack game.

---

## 10. Multiplayer

VP cannot be played in multiplayer through the MODS menu; it must be packaged as a modpack that loads automatically as a DLC.

**Preferred — a prebuilt modpack.** The CivFanatics [modpack thread 685164](https://forums.civfanatics.com/threads/685164/) tracks current releases (5.4.6), including one generated on Linux under Proton. Extract so its folders — `ZMP_MODPACK` and any `VPUI` or `UI_bc1` it carries — sit directly under the game folder's `Assets/DLC`, then delete the Documents folder's `cache`. Modpacks also work in single player and are easier to update than a MODS-menu install.

**Alternative — build your own.**

1. Enable `(5) Modpack Maker for VP` plus every mod to include.
2. Start or load a game and press **Ctrl+Shift+M**.
3. Check `Logs/Lua.log` in the Documents folder for errors (logging on, §15).
4. Exit and delete `cache`.
5. Relaunch and start from Single Player or Multiplayer, never the MODS menu.

**Alternative — civ5vp-installer.** It builds modpacks too (§14).

```
┌────────────────────────────────────────────────────────────────────────────────────────┐
│ Modpack rules                                                                          │
├────────────────────────────────────────────────────────────────────────────────────────┤
│ Modpacks cannot be combined with mods activated through the MODS menu                  │
│ Every player must use byte-identical modpacks                                          │
│ Every player must delete cache BEFORE EVERY LAUNCH, or the game will most likely crash │
│   after the first turn                                                                 │
│ Do not hand-edit a modpack folder — rebuild or re-download it instead                  │
│ Saves do not record which modpack was used; a mismatch crashes                         │
│ Multiplayer autosaves live in Saves/multi/auto — collect them for desync reports       │
└────────────────────────────────────────────────────────────────────────────────────────┘
```

Remove a modpack by deleting its folders under `Assets/DLC` and the Documents folder's `cache`: `VP_MODPACK` for one you built; `ZMP_MODPACK`, `VPUI` and `UI_bc1` for the thread's packs, per its opening post.

**Known issues.** Community-Patch-DLL #13349, open since 2026-09-05, reports a 5.4.x multiplayer desync that its reporter suspects lies in pathfinding; check its state before a long game. MPPatch, which would allow modded multiplayer without modpacks, has had no release since December 2023, and a forum report describes crashes with VP plus EUI.

---

## 11. Updating

Back up first: copy `Saves` and `MODS` from the Documents folder to somewhere outside the prefix. A prefix reset destroys the saves, and the installer rewrites the mod folders, losing any edit under `MODS`.

Then download the newer `Vox.Populi.<version>.exe`, check it against its own digest on the release page (Step 5) and run it exactly as in Steps 6–7. It deletes the old mod folders and `cache` before writing; no manual cleanup.

By the project's versioning rule, saves are compatible when only the third version component changes: 5.4.4 → 5.4.6 keeps them, 5.4.x → 5.5.0 does not.

> **WARNING** — Steam's *Verify integrity of game files* restores the stock `Expansion2.Civ5Pkg`, which VP replaces to fix city-state audio. Re-run the VP installer after any verify.

---

## 12. Uninstalling

**Preferred — Uninstall all.** Re-run the installer, choose **Uninstall all** and point the Civilization V folder page at the same real install; this also restores the stock `Expansion2.Civ5Pkg`.

**Fallback — manual removal.** Delete these, then verify game files in Steam to restore the stock `Expansion2.Civ5Pkg` — verification leaves added files alone, hence the sound XML in the list:

```
┌──────────────────┬─────────────────────────────────────────────────────────────────────┐
│ Folder           │ Delete                                                              │
├──────────────────┼─────────────────────────────────────────────────────────────────────┤
│ Documents folder │ MODS/(1) Community Patch · MODS/(2) Vox Populi                      │
│                  │ MODS/(3a) VP - EUI Compatibility Files                              │
│                  │ MODS/(3b) 43 Civs Community Patch · MODS/(4a) Squads for VP         │
│                  │ MODS/(5) Modpack Maker for VP                                       │
│                  │ cache · ModUserData · Text/VPUI_tips_en_us.xml                      │
│ Game folder      │ Assets/DLC/VPUI · Assets/DLC/UI_bc1                                 │
│                  │ Assets/DLC/VP_MODPACK or Assets/DLC/ZMP_MODPACK (modpacks, §10)     │
│                  │ Assets/DLC/Expansion2/Sounds/XML/MinorCivSounds_VoxPopuli.xml       │
└──────────────────┴─────────────────────────────────────────────────────────────────────┘
```

**Last resort — prefix reset.** Delete `<library>/steamapps/compatdata/8930`; this also destroys the saves inside the prefix, so back them up first (§11).

---

## 13. Troubleshooting

Ordered by when the failure appears.

### Setup and install

```
┌────────────────────────────────────┬───────────────────────────────────────────────────┐
│ Symptom                            │ Cause and fix                                     │
├────────────────────────────────────┼───────────────────────────────────────────────────┤
│ Steam: "disk write error" while it │ Steam client bug with case-mismatched depot       │
│   downloads the Windows depots     │ folders (steam-for-linux #13406, closed as        │
│   (Step 1)                         │ completed 2026-07-27). Update Steam and retry;    │
│                                    │ reporters created the folder the error names, in  │
│                                    │ that exact case, under                            │
│                                    │ steamapps/downloading/8930.                       │
│ "Invalid file magic number"        │ Protontricks older than 1.12.0 cannot read the    │
│                                    │ current appinfo.vdf. Upgrade, or use the Flatpak  │
│                                    │ (§3).                                             │
│ Flatpak: "does not appear to have  │ The installer or a library sits outside the       │
│   access to the following          │ folders the Flatpak can reach (§3). Move it, or   │
│   directories"                     │ grant the folder as the message says, then        │
│                                    │ restart Protontricks.                             │
│ Protontricks does not list Civ V   │ Prefix absent. Launch once via Proton (Step 3).   │
│ "Proton installation could not be  │ Compat tool is a Steam Linux Runtime entry, not   │
│   found!"                          │ Proton. Re-select a real Proton build (Step 1).   │
│ "You are using a 64-bit            │ Expected (Winetricks, Step 4): every Proton       │
│   WINEPREFIX"                      │ prefix is 64-bit. Close it.                       │
│ "Cannot find cabextract"           │ Install the cabextract package.                   │
│ "command cabextract … returned     │ cabextract trips over a symlink Proton created.   │
│   status 1"                        │ Delete the symlink the error names, then re-run.  │
│ Wizard never appears               │ Wayland focus or a broken Proton build. Check     │
│                                    │ other windows, then try Proton Experimental or    │
│                                    │ proton-cachyos-slr.                               │
│ "did not provide the correct path" │ Chosen folder has no Assets\DLC child. Point at   │
│                                    │ the game root, not Assets or DLC.                 │
│ "You don't have all required DLCs" │ The message names the missing packs. Enable every │
│                                    │ DLC in Steam and repeat the Step 2 check.         │
└────────────────────────────────────┴───────────────────────────────────────────────────┘
```

### In the game

```
┌────────────────────────────────────┬───────────────────────────────────────────────────┐
│ Symptom                            │ Cause and fix                                     │
├────────────────────────────────────┼───────────────────────────────────────────────────┤
│ DX9 starts despite choosing DX11   │ Proton report #8327, closed as not planned.       │
│                                    │ Launch from the library window, not a shortcut or │
│                                    │ the tray icon (Step 3).                           │
│ Crash during the first configuring │ The thread-702075 opening post saw this once;     │
│   game data pass                   │ relaunch and enter MODS again (§6); if it keeps   │
│                                    │ crashing, delete cache.                           │
│ VP absent from the in-game mod     │ Documents page was changed; mods are outside the  │
│   list                             │ prefix. Re-run with the default Documents path.   │
│ Missing textures, broken UI        │ Civ V folder page pointed at a game tree inside   │
│                                    │ the prefix. Re-run, or copy across per Step 8.    │
│ EUI works, no other VP features    │ Back was pressed in MODS, or (3a) is not enabled  │
│                                    │ on an EUI install. Re-enable all mods, press      │
│                                    │ NEXT.                                             │
│ Tech or policy tree unchanged, or  │ Game language is not English. Steam → Properties  │
│   entries show wrong text          │ → Language → English, then delete cache (§2).     │
│ Mods stale, duplicated or missing  │ Delete cache and ModUserData (§9). Then look for  │
│                                    │ Workshop subscriptions sharing mod IDs, or a mod  │
│                                    │ with its own DLL.                                 │
│ CustomModOption change has no      │ Cache not cleared, or the option needed its       │
│   effect                           │ defines (§8).                                     │
│ Crackling audio                    │ One ProtonDB report: launch option                │
│                                    │ PULSE_LATENCY_MSEC=60 %command%.                  │
│ DX11 hangs after a few minutes     │ A January 2026 ProtonDB report ran stable with    │
│                                    │ the launch option -dx9; another saw gray screen   │
│                                    │ areas under DX9.                                  │
│ Random crashes early in a session  │ Thread count (ProtonDB reports). Pin to eight     │
│                                    │ threads with taskset, or align                    │
│                                    │ MaxSimultaneousThreads (§7).                      │
│ Crashes on NVIDIA                  │ Two May 2026 ProtonDB reports crash on drivers    │
│                                    │ 580.142 and 595.71.05; one reverted the driver. A │
│                                    │ July report on 580.105.08 runs. Not a VP fault.   │
│ Multiplayer crashes after turn 1   │ Cache not cleared before launch by every player   │
│                                    │ (§10).                                            │
│ Crash to desktop in the late game  │ 32-bit address-space exhaustion. Apply §7 in      │
│                                    │ order, and keep the game folder's crashlogs for a │
│                                    │ report (§15).                                     │
│ Still broken after all of the      │ Minimal install — VP without EUI, no other mods — │
│   above                            │ then add EUI, then other mods, one layer at a     │
│                                    │ time.                                             │
└────────────────────────────────────┴───────────────────────────────────────────────────┘
```

---

## 14. Alternative installer

[github.com/Alpakinator/civ5vp-installer](https://github.com/Alpakinator/civ5vp-installer) is a single-file native Linux binary (Apache-2.0) that installs VP without Protontricks — a fallback if the Inno wizard misbehaves, newer and less tested than the Protontricks route. It finds the Civ V folders itself (you can correct them), writes only to the game's MODS, DLC and Text folders and its cache, has a modpack mode, and its Uninstall button restores an unmodded game. The game itself still runs under Proton (§1).

```
civ5vp-installer-linux-x86_64 (v0.1.6, 2026-09-15) — 44,735,720 bytes
sha256  a0b59eb445d23bc9a5206ec0f592a7f81c4cfd40a5f2e0d823737bacbd271baa
```

Verify the digest as in Step 5, mark the file executable (Dolphin: **Properties → Permissions → Is executable**) and open it. Unofficial or in-development versions are compiled locally — about 1.1 GB of build tools once, roughly 5 GB in `~/.local/share/civ5vp-installer`.

> **WARNING** — Install release versions only. On 2026-08-26 the author advised against its "Compile the DLL myself" option, single player included: the compiler it currently uses probably drops loop checks from the C++ code, which can freeze the game.

Packs built by versions before 0.1.6 can show raw text keys for loading tips and EUI options (upstream issue #13364); rebuild and redistribute them with 0.1.6.

---

## 15. Bug reporting

Vox Populi bugs go to the project's tracker, [github.com/LoneGazebo/Community-Patch-DLL/issues](https://github.com/LoneGazebo/Community-Patch-DLL/issues), not the forum; errors in this guide go to [github.com/ryanmusante/vox-populi-cachyos/issues](https://github.com/ryanmusante/vox-populi-cachyos/issues). Upstream's bug form is the only route (blank issues are disabled). It requires the mod version, the installed components — the setup type from Step 7 — and a description, and asks for three attachments:

```
┌────────────────────────┬───────────────────────────────────────────────────────────────┐
│ Attachment             │ Where it is                                                   │
├────────────────────────┼───────────────────────────────────────────────────────────────┤
│ Save from one turn     │ Saves in the Documents folder — the reason for the autosave   │
│   before the problem   │ settings in §7                                                │
│ Logs                   │ Logs in the Documents folder, zipped — useless unless logging │
│                        │ was already on                                                │
│ Crash artifacts, when  │ crashlogs in the game folder, for crashes.log and the .dmp,   │
│   reporting a crash    │ per the issue form. The minidump guide instead says dumps     │
│                        │ land beside the game executable as CvMiniDump_*.dmp — check   │
│                        │ both places.                                                  │
└────────────────────────┴───────────────────────────────────────────────────────────────┘
```

Logging is off by default and must be on *before* the problem occurs. Enable it in the Documents folder's `config.ini`: set `ValidateGameDatabase`, `LoggingEnabled`, `MessageLog`, `AILog`, `AIPerfLog`, `BuilderAILog` and `PlayerAndCityAILogSplit` to `1`. (Upstream's docs name the folder "Civilization V"; it is "Civilization 5".) Collect logs *before* loading a game — most are erased on load. Turn logging back off when you are not chasing a bug; it rewrites a large directory continuously.

Dumps are not guaranteed under Proton: the DLL loads `dbghelp.dll` from the prefix's `System32`, so it depends on Wine's implementation. If none appears after a crash, say so in the report and attach the logs and save.

The project wiki, [github.com/LoneGazebo/Community-Patch-DLL/wiki](https://github.com/LoneGazebo/Community-Patch-DLL/wiki), has guidance on writing a good report.

---

## 16. Path reference

Every location the guide uses, by folder. `<library>` is the §2 Steam library; indented rows sit inside the row above.

```
┌────────────────────────────┬───────────────────────────────────────────────────────────┐
│ Location                   │ Path                                                      │
├────────────────────────────┼───────────────────────────────────────────────────────────┤
│ Steam library              │ ~/.local/share/Steam by default, also ~/.steam/root;      │
│                            │ other packagings in §3                                    │
│   Depot downloads          │ steamapps/downloading/8930 — disk write error (§13)       │
│ Game folder                │ <library>/steamapps/common/Sid Meier's Civilization V     │
│   As the wizard sees it    │ S:\steamapps\common\Sid Meier's Civilization V,           │
│                            │ or its Z:\ form (Step 7)                                  │
│   DLC folders              │ Assets/DLC/DLC_01–DLC_07, DLC_Deluxe, Expansion,          │
│                            │ Expansion2 — all ten required (Step 2)                    │
│   VP assets                │ Assets/DLC/VPUI · Assets/DLC/UI_bc1 (EUI setups)          │
│                            │ Assets/DLC/Expansion2/Expansion2.Civ5Pkg                  │
│                            │ Assets/DLC/Expansion2/Sounds/XML/                         │
│                            │   MinorCivSounds_VoxPopuli.xml                            │
│   Mod extras               │ Assets/DLC/Expansion2/DLC — More Wonders' effects (§9)    │
│   Modpack                  │ Assets/DLC/VP_MODPACK if built;                           │
│                            │ Assets/DLC/ZMP_MODPACK if prebuilt (§10)                  │
│   Crash artifacts          │ crashlogs, and CvMiniDump_*.dmp beside the game           │
│                            │ executable (§15)                                          │
│ Prefix                     │ <library>/steamapps/compatdata/8930/pfx                   │
│   C: drive                 │ pfx/drive_c                                               │
│   Drive mappings (S:, Z:)  │ pfx/dosdevices                                            │
│   Phantom tree (Step 8)    │ drive_c/Program Files (x86)/Steam/steamapps/common/       │
│                            │ Sid Meier's Civilization V — must not exist               │
│ Documents folder           │ drive_c/users/steamuser/Documents/My Games/               │
│                            │ Sid Meier's Civilization 5                                │
│   Mods                     │ MODS — the numbered VP folders and any added mod (§6, §9) │
│   Options file             │ MODS/(1) Community Patch/Database Changes/                │
│                            │ NewCustomModOptions.xml (§8)                              │
│   Caches                   │ cache · ModUserData — delete after any mod change (§9)    │
│   Saves                    │ Saves · Saves/multi/auto for multiplayer autosaves (§10)  │
│   Logs                     │ Logs — most files only with logging on (§15)              │
│   Settings                 │ config.ini · UserSettings.ini ·                           │
│                            │ GraphicsSettingsDX11.ini · GraphicsSettingsDX9.ini (§7)   │
│   Loading tips             │ Text/VPUI_tips_en_us.xml                                  │
│ civ5vp-installer data      │ ~/.local/share/civ5vp-installer (§14)                     │
│ Native build settings      │ ~/.local/share/Aspyr — unused under Proton (§7)           │
└────────────────────────────┴───────────────────────────────────────────────────────────┘
```

---

## 17. Sources

Every row was verified on 2026-09-24; versions, releases, issue states and sources were re-checked on 2026-09-27 and 2026-10-02. Rows follow the guide's section order.

```
┌──────┬──────────────────────────────────────┬──────────────────────────────────────────┐
│ §    │ Fact                                 │ Source                                   │
├──────┼──────────────────────────────────────┼──────────────────────────────────────────┤
│ §1   │ Installer behavior: two-path wizard, │ LoneGazebo/Community-Patch-DLL —         │
│      │   page order, setup types, DLC gate, │ VPSetupData.iss, scripts/release.py,     │
│      │   cleanup, Uninstallable=no,         │ Opener.rtf; Inno Setup 6.1.0 source      │
│      │   Expansion2.Civ5Pkg swap, savegame  │                                          │
│      │   rule                               │                                          │
│ §1   │ 1.0.3.279 + all DLC; logging keys;   │ VP README.md, DEVELOPMENT.md,            │
│      │   minidump location and dbghelp.dll  │ docs/minidumps.md                        │
│      │   dependency                         │                                          │
│ §2   │ Mod dependencies and blocked mods,   │ the six modinfo files; (1)/(2)/(3a)      │
│      │   EUI rules, VPUI/UI_bc1; Squads is  │ INSTRUCTIONS.txt and MANUAL INSTALL.txt; │
│      │   QoL; modpack rules and removal;    │ ModpackMaker.lua; VPSetupData.iss        │
│      │   cleanup list                       │                                          │
│ §2   │ Game language must be English        │ CivFanatics thread 528034 FAQ; modpack   │
│      │                                      │ thread 685164 OP; (1a) Community Patch – │
│      │                                      │ German Workshop page                     │
│ §2   │ Package versions, dependencies and   │ archlinux.org package DB;                │
│      │   repositories; Shelly replacing     │ mirror.cachyos.org; CachyOS wiki: GUI    │
│      │   Octopi                             │ Installer changelog, 26.04               │
│ §2   │ Steam default dir and ~/.steam/root  │ Arch Wiki: Steam                         │
│ §3   │ Protontricks app shortcut and        │ Matoking/protontricks README, setup.cfg, │
│      │   Launcher, Flatpak access message,  │ TROUBLESHOOTING.md, 1.14.1 CHANGELOG.md  │
│      │   1.12.0 appinfo.vdf floor,          │ and source; yad/zenity roles from the    │
│      │   not-Proton message; Flatpak        │ Arch optdepends; Flathub manifest        │
│      │   folders and desktop entries        │ (finish-args)                            │
│ §3   │ Ubuntu, Debian and Fedora packages,  │ packages.ubuntu.com, Launchpad;          │
│      │   Steam setup and default directory; │ packages.debian.org, sources.debian.org; │
│      │   NVIDIA 32-bit libraries on Debian  │ Bodhi (protontricks-1.13.1-3.fc44);      │
│      │                                      │ Debian wiki: Steam; RPM Fusion; Flathub; │
│      │                                      │ steam-installer 1.0.0.85 source          │
│ §4   │ Steam Linux Runtime breaks           │ TeaDrinkingProgrammer guide and its      │
│      │   Protontricks; original             │ issue #3                                 │
│      │   copy-the-Assets workaround         │                                          │
│ §4   │ LARGE_ADDRESS_AWARE default;         │ ValveSoftware/Proton README and proton   │
│      │   GAME_DRIVE and its 11.0 default;   │ script, 9.0 to 11.0; Proton issue #8327  │
│      │   DX9/DX11 launch report             │                                          │
│ §4   │ DX11 from Steam's launch options;    │ 2K Support, "Civilization V: Launcher    │
│      │   shortcuts start DX9                │ Removal" (2024-11-18)                    │
│ §5   │ Winetricks menus, vcrun2008 and      │ Winetricks/winetricks src/winetricks     │
│      │   corefonts contents, installed      │ (20260125)                               │
│      │   items pre-ticked, the 64-bit       │                                          │
│      │   prefix warning                     │                                          │
│ §5   │ Game-core DLL imports MSVCR90 and    │ CvGameCore_Expansion2.dll, both 5.4.6    │
│      │   MSVCP90                            │ variants, PE import table                │
│ §5   │ 5.4.6 stable, asset name, size,      │ GitHub releases + release feed (released │
│      │   sha256; still the newest release   │ 2026-08-31)                              │
│ §5   │ Checksums and Is-executable in       │ KDE Dolphin file properties dialog       │
│      │   Dolphin                            │                                          │
│ §6   │ Linux mod handling, cache +          │ CivFanatics thread 702075 (schubman),    │
│      │   ModUserData, known-good mods,      │ now stickied; page 2 posts #21–#23 for   │
│      │   first pass may crash once;         │ the last two                             │
│      │   runtime-library report; Debian     │                                          │
│      │   Steam path                         │                                          │
│ §7   │ Autosaves; Workshop and DLL          │ CivFanatics threads 528034 ("How To      │
│      │   conflicts; minimal-install triage; │ Install") and 542679 ("MODS compatible", │
│      │   compatibility list and its 2022    │ in the archive)                          │
│      │   last edit                          │                                          │
│ §7   │ Late-game CTD from 32-bit memory;    │ CivFanatics "Start Here" thread 701813   │
│      │   the mitigation order; beta vs      │                                          │
│      │   stable naming                      │                                          │
│ §7   │ Early-crash thread fix; NVIDIA,      │ ProtonDB public data export, 2026-09-01: │
│      │   DX9/DX11 and audio reports         │ 196 reports for app 8930                 │
│ §7   │ INI files, sections and keys;        │ PCGamingWiki "Sid Meier's Civilization   │
│      │   gray-tile fix; resolution          │ V"; Steam FAQ thread for app 8930;       │
│      │   recovery; tessellation cost; Skip  │ CivFanatics 384886, 382624, 381059,      │
│      │   Intro option and the loading       │ 386447, 644343, 505112, 472949, 400990   │
│      │   behind the movie; INI reset        │ and 405977; Steam discussion threads     │
│ §8   │ CustomModOptions rules, Class        │ (1) Community Patch/Database             │
│      │   legend, Events overhead, the four  │ Changes/NewCustomModOptions.xml          │
│      │   toggles                            │                                          │
│ §8   │ Achievements via VP's own option;    │ bmaupin/civ5-cheevos-with-mods README,   │
│      │   native Linux map-achievement       │ citing Community-Patch-DLL #12965        │
│      │   breakage                           │                                          │
│ §9   │ Popular mods: ranking, authors,      │ CivFanatics Mods Repository (549), all   │
│      │   stated versions and requirements;  │ six pages; stickies 701787 and 689101;   │
│      │   prefixes; compatibility sticky,    │ opening posts of 653498 (More Wonders),  │
│      │   archive rule, Downloads and        │ 686269 (Unique City-States) and 701034   │
│      │   Workshop lag; 3/4 UC built in;     │ (Cultural Components); threads 699519    │
│      │   left-out mods                      │ and 685164; Even More Resources page     │
│      │                                      │ 28019 and its Workshop comments          │
│ §9   │ Community advice on what VP          │ Reddit r/civ5 and r/civvoxpopuli threads │
│      │   tolerates; recurring mod sets      │                                          │
│ §9   │ Community Events lineage and VP fork │ TechpriestEnginseer/                     │
│      │                                      │ Community-Patch-Events-Development;      │
│      │                                      │ n-core/VP-Community-Events               │
│ §9   │ .civ5mod is 7-Zip; the .modinfo      │ CivFanatics threads 451941, 549219 and   │
│      │   folder goes into MODS; in-game     │ 477763; the (7a) Events Overhaul title   │
│      │   unpacking unreliable               │                                          │
│ §10  │ Prebuilt modpacks incl. Linux/Proton │ CivFanatics modpack thread 685164 (OP,   │
│      │   build, their folder names; MP      │ posts #721 and #732);                    │
│      │   autosaves; library-window launch   │ Community-Patch-DLL #13349, open; #13344 │
│      │   tip; open 5.4.x desync; Info       │ open; draft PR #13372                    │
│      │   Addict; 2026 crash reports         │                                          │
│ §10  │ MPPatch last release Dec 2023        │ Lymia/MPPatch release feed               │
│ §13  │ Steam disk write error on            │ ValveSoftware/steam-for-linux #13406,    │
│      │   case-mismatched depot folders      │ #13433, #13436                           │
│ §14  │ Alternative installer: 0.1.6, asset, │ Alpakinator/civ5vp-installer README,     │
│      │   sha256, data dir, local-build      │ CHANGELOG, v0.1.6 assets; CivFanatics    │
│      │   warning, text-key bug              │ thread 704249; Community-Patch-DLL       │
│      │                                      │ #13364                                   │
│ §15  │ Bug form: required fields, three     │ .github/ISSUE_TEMPLATE/bug_report_v5.yml │
│      │   attachments, crashlogs path,       │ and config.yml; Community-Patch-DLL wiki │
│      │   autosave rationale; wiki           │                                          │
└──────┴──────────────────────────────────────┴──────────────────────────────────────────┘
```

---

## 18. Limits

- **Written against 5.4.6.** Wizard page order and setup-type names have been stable but are not guaranteed; the sha256 is for 5.4.6 only.
- **Read from source, not exercised:** the `S:` drive default (Proton's script), the Protontricks and Winetricks window routes, and the §8 toggles. The wizard refuses a wrong path and `Z:` always works; `ENABLE_ACHIEVEMENTS` is marked "FUNCTIONALITY NOT GUARANTEED" upstream and changes the savegame format.
- **Two opening posts are known only from indexed excerpts** — thread 702075's and the modpack thread's, including the prebuilt packs' folder names; both pages return metadata only. Page 2 of thread 702075 was read in full on 2026-09-19, and structural facts come from the Vox Populi source tree.
- **Community reports, not upstream statements:** the English-language requirement (thread 528034, the modpack thread, the German language pack); the tray icon starting DX9 (a 2023 modpack-thread report — the DX11 choice itself follows 2K's launcher-removal notice); the runtime-library step (one forum report and the DLL's import table, not reproduced on a clean prefix, harmless to apply); and the §7 and §13 items from ProtonDB's public data export (2026-09-01), read because its page needs JavaScript.
- **§3 was checked against package indexes and upstream documentation, not run** on those systems. Fedora's figure is the newest Bodhi update found for Fedora 44 (January 2026); a later one may exist.
- **The performance-first values order options by the cost forum benchmarks reported** (2010–2013 threads, Windows); none were measured under Proton, and the numeric levels behind most detail keys are not documented beyond the samples those threads posted.
- **The popular-mod roster ranks thread views across all six pages of the Mods Repository** on 2026-10-02, keeping mods with 2026 activity and no reported breakage; versions are the authors' statements, descriptions come from titles and opening posts. None was tested under Proton; a mod's last page is the authority for 5.4.6.
- **Upstream disagrees with itself on where crash dumps land** — the issue form says `crashlogs`, the minidump guide says beside the game executable. Both are covered.
- **File-manager steps describe KDE's Dolphin**; other file managers put checksums and the executable bit elsewhere.
- **Untested paths:** Flatpak and Snap Steam (procedure holds, paths move), the 43-civ variants, MPPatch and civ5vp-installer's local DLL build.
