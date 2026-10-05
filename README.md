# Vox Populi on CachyOS

**Civilization V + Vox Populi via Steam and Proton**

Revision 11.4.0 · 2026-10-04 · Vox Populi 5.4.6 (stable) · Protontricks 1.14.1-1 · Proton 11.0 · CachyOS with native Steam; other distributions in §3.

No terminal is needed: the work happens in Steam, your package manager (Shelly on CachyOS; Octopi on systems installed before the April 2026 ISO), the Protontricks and Winetricks windows, the installer's wizard and your file manager.

Work through §4–§6 in order: Steps 1–3 prepare the game, Steps 4–8 install Vox Populi and §6 is the first launch. Every step that writes to disk ends in a **Check**. **WARNING** marks a common failure, **CRITICAL** a step that decides whether this works at all.

Maintained at [github.com/ryanmusante/vox-populi-cachyos](https://github.com/ryanmusante/vox-populi-cachyos); `vox-populi-cachyos.pdf` carries the same text.

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
17. [Limits](#17-limits)

---

## 1. How it works

**Vox Populi (VP)** is the Community Patch Project's overhaul of Civilization V: Brave New World — a replacement game-core DLL with AI and bug fixes (the Community Patch) plus rebalanced rules and new systems.

**Proton is mandatory.** Vox Populi ships a Windows game-core DLL that the native Aspyr Linux build cannot load.

**The installer is Inno Setup 6**, a plain Win32 wizard that needs no .NET and renders correctly under Proton.

**It asks for two different paths**; the second one decides the install:

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
│ Select the Civilization │ Whatever you enter —       │ Assets\DLC\VPUI (VP setups)     │
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

**It cleans up after itself.** Before writing, it deletes `cache`, VP's previous files in both folders and every legacy VP folder name. Clear nothing by hand, and expect any edit inside a VP mod folder to be lost at the next install or update.

**There is no uninstaller entry** (`Uninstallable=no`). Removal is the **Uninstall all** setup type in the same `.exe`.

### The install at a glance

```
┌───┬────────────────────────────┬────────────────────────┬──────────────────────────────┐
│   │ Step                       │ Where                  │ Done when                    │
├───┼────────────────────────────┼────────────────────────┼──────────────────────────────┤
│ ☐ │ 1 Force Proton             │ Steam → Properties     │ CivilizationV_DX11.exe is    │
│   │                            │                        │ in the game folder           │
│ ☐ │ 2 Install every DLC        │ Steam → Properties     │ Assets/DLC holds all ten     │
│ ☐ │ 3 Launch once, then quit   │ Steam library window   │ the Documents folder exists  │
│ ☐ │ 4 vcrun2008 and corefonts  │ package manager,       │ both ticked when reopened    │
│   │                            │ Protontricks           │                              │
│ ☐ │ 5 Download and verify      │ browser, file manager  │ the sha256 matches           │
│ ☐ │ 6–7 Run the wizard         │ Protontricks Launcher  │ Ready page shows S:\ or Z:\  │
│ ☐ │ 8 Verify placement         │ file manager           │ Assets/DLC holds VPUI        │
│ ☐ │ First launch (§6)          │ Civ V → MODS           │ mods enabled, NEXT pressed   │
└───┴────────────────────────────┴────────────────────────┴──────────────────────────────┘
```

---

## 2. Requirements

```
┌──────────────┬─────────────────────────────────────────────────────────────────────────┐
│ Item         │ Requirement                                                             │
├──────────────┼─────────────────────────────────────────────────────────────────────────┤
│ Game         │ Civilization V 1.0.3.279, all expansions and all DLC installed          │
│ Steam        │ Native Steam (multilib steam); data directory ~/.local/share/Steam,     │
│              │ also reachable through ~/.steam/root. Other distributions: §3           │
│ Compat tool  │ Proton Experimental or newest numbered Proton (11.0 at this revision);  │
│              │ proton-cachyos-slr OK. NOT a "Steam Linux Runtime" entry (Step 1)       │
│ Language     │ Game language English (Steam → Properties → Language). VP ships its     │
│              │ text in English only; other languages show missing or stale entries     │
│ Tooling      │ extra/protontricks 1.14.1-1 with yad (its game list) and zenity (a      │
│              │ Winetricks dialog tool, already a steam dependency); it pulls           │
│              │ winetricks, which pulls wine and cabextract                             │
│ Disk         │ Full re-download of the Windows depots (the Steam store asks for 8 GB   │
│              │ free) plus the 110 MB installer                                         │
│ Conflicts    │ VP's modinfo files block More Luxuries, CSD for VP, Civ IV Diplomatic   │
│              │ Features (both), Artificial Unintelligence, Bridges and Canals, and     │
│              │ standalone Squads for VP; remove them, Workshop copies included. No     │
│              │ other mod may ship its own DLL                                          │
└──────────────┴─────────────────────────────────────────────────────────────────────────┘
```

The procedure works in three folders. Steam → right-click **Sid Meier's Civilization V** → **Properties → Installed Files → Browse** opens the game folder; the library is the folder above `steamapps`, and `compatdata/8930` always sits in the same library as the game. Show hidden files in your file manager — `.local` and `.steam` are hidden.

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

Bookmark the Documents folder in your file manager: §8, §9 and §10 send you back to it to delete `cache`.

---

## 3. Other distributions

Other distributions differ only in how Steam is installed, where its default library sits and whether their Protontricks is recent enough; from Step 1 on, the procedure is the same. **Protontricks 1.12.0 is the floor**: older releases cannot read the current Steam client's `appinfo.vdf` and stop with "Invalid file magic number".

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

**Debian and Ubuntu** need the i386 architecture enabled and the `multiverse` (Ubuntu) or `contrib` (Debian) component before `steam-installer` is offered. Debian also wants the 32-bit Mesa packages that the Debian wiki's Steam page lists, or NVIDIA's 32-bit driver libraries (`nvidia-driver-libs:i386`) with the proprietary driver. **Fedora** takes Steam from RPM Fusion Nonfree and Protontricks from Fedora itself.

Where the table says **Flatpak**, add Flathub, install `com.github.Matoking.protontricks` and restart; on the **Steam Deck**, use Desktop Mode's Discover — SteamOS's root is read-only. Flatpak Protontricks reaches Steam's directories and the standard home folders and ships the same shortcut and Launcher entries; with a library or installer elsewhere, it names the folders it cannot reach and how to grant access.

Then, on any of them: start Steam once and sign in so it creates its directories, confirm in your package manager that Protontricks is 1.12.0 or newer, and continue with Step 1.

---

## 4. Preparing the game

### Step 1 · Force Proton

Steam → right-click **Sid Meier's Civilization V** → **Properties → Compatibility** → tick **Force the use of a specific Steam Play compatibility tool** → Proton Experimental or the newest numbered Proton. Steam swaps the native build for the Windows depots; let it finish. If the download stops with a disk write error, see §13.

> **WARNING** — The same dropdown lists Steam Linux Runtime entries. They are not Proton: no Wine prefix is created, and Protontricks stops with "Proton installation could not be found!".

`proton-cachyos-slr` from the CachyOS repositories also works (install it, restart Steam); despite its name it is a Proton build.

Proton 11.0 and Experimental map `S:` to the game's Steam library, used in Step 7; on Proton 10.0 or older add the launch option `PROTON_SET_GAME_DRIVE=1 %command%`. If the library root is not writable, `S:` is `steamapps` itself (`S:\common\…`).

**Check:** the game folder holds `CivilizationV_DX11.exe` among other `.exe` files. No `.exe` at all, and a `Civ5XP` binary instead, means Steam is still serving the native build.

### Step 2 · Confirm every DLC

Steam → right-click **Sid Meier's Civilization V** → **Properties → DLC** → tick every entry.

**Check:** `Assets/DLC` in the game folder holds all ten — `DLC_01`–`DLC_07`, `DLC_Deluxe`, `Expansion` and `Expansion2`. A missing one stops the installer at Step 7.

### Step 3 · Launch once, then quit

Press **Play** in the Steam library window and pick **Play Sid Meier's Civilization V (DirectX 10/11)** from Steam's launch options. The first start takes longer while Proton builds the prefix; wait for the main menu, then quit. This creates the prefix, `compatdata/8930/pfx`, which Protontricks needs, and Civ V's Documents folder inside it.

**Check:** the Documents folder exists and holds `config.ini`.

---

## 5. Installing Vox Populi

### Step 4 · Install Protontricks and the runtime libraries

Install `protontricks` and `yad` from the CachyOS repositories; `zenity` is already there as a Steam dependency (other distributions: §3). Then:

1. Open **Protontricks** from the application menu and pick **Sid Meier's Civilization V** — it is listed only after Step 3.
2. In the Winetricks window that opens, choose **Select the default wineprefix**.
3. **Install a Windows DLL or component** → tick `vcrun2008` → **OK**.
4. **Install a font** → tick `corefonts` → **OK**, then close Winetricks.

Close the 64-bit WINEPREFIX warning: every Proton prefix is 64-bit. Both installs download; repeating the step is harmless.

`vcrun2008` is the Visual C++ 2008 runtime the VP game-core DLL imports; Wine's built-in copy usually suffices, but a report in [thread 702075](https://forums.civfanatics.com/threads/702075/) needed it.

**Check:** opened again, those two lists show `vcrun2008` and `corefonts` already ticked.

### Step 5 · Download and verify

Download `Vox.Populi.<version>.exe` from the release's assets at [github.com/LoneGazebo/Community-Patch-DLL/releases](https://github.com/LoneGazebo/Community-Patch-DLL/releases); the asset list shows each file's digest. `Release_Debug.zip` beside it is a debug DLL for crash reports, not needed for play. For 5.4.6:

```
Vox.Populi.5.4.6.exe — 110,481,637 bytes
sha256  31679423e55d7f64ba9693d8252d9e29f824fd9f8da043af547470068f98ba8a
```

**Check:** the digest your file manager computes matches (Dolphin: **Properties → Checksums**). On a mismatch, delete the file and download it again.

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
│ Community Patch only           │ AI and bug-fix base mod. NOT compatible with EUI      │
│ 43 Civ Community Patch only,   │ The same three with a DLL supporting 43 major civs    │
│   43 Civ Vox Populi (no EUI),  │                                                       │
│   43 Civ Vox Populi (with EUI) │                                                       │
│ Uninstall all                  │ Removal — §12                                         │
└────────────────────────────────┴───────────────────────────────────────────────────────┘
```

The `Z:\` form of the game folder in the default library:

```
Z:\home\<your user name>\.local\share\Steam\steamapps\common\Sid Meier's Civilization V
```

### Step 8 · Verify placement

**Check:** `Assets/DLC` in the game folder now holds `VPUI`, plus `UI_bc1` with EUI — the pass/fail test for Step 7. **Community Patch only** installs neither but still adds `Expansion2/Sounds/XML/MinorCivSounds_VoxPopuli.xml` there. The Documents folder's `MODS` holds the numbered mod folders of your variant.

If `<library>/steamapps/compatdata/8930/pfx/drive_c/Program Files (x86)/Steam/steamapps/common/Sid Meier's Civilization V` exists, the files went into the prefix: re-run with the correct path, or copy that tree's `Assets` over the game folder's.

---

## 6. First launch

1. Launch Civ V from the Steam library window, as in Step 3.
2. Main menu → **MODS**; accept the prompt if one appears.
3. The first entry into the MODS menu runs a "configuring game data" pass of 5–15 minutes — not a hang; if it crashes, relaunch (§13).
4. Enable the VP mods the installer placed in `MODS` — the table below.
5. Press **NEXT**, never **Back**.
6. **Single Player → Set Up Game**. In this menu *Single Player* looks like a heading but is a button.

```
┌────────────────────────────────┬───────────────────────────────────────────────────────┐
│ Mod                            │ When to enable it                                     │
├────────────────────────────────┼───────────────────────────────────────────────────────┤
│ (1) Community Patch            │ Always — every other VP mod requires it               │
│ (2) Vox Populi                 │ Requires (1) Community Patch                          │
│ (3a) VP - EUI Compatibility    │ MUST be on for any EUI install                        │
│   Files                        │                                                       │
│ (3b) 43 Civs Community Patch   │ Only for 43-civ variants                              │
│ (4a) Squads for VP             │ Optional: RTS-style control groups (quality of life)  │
│ (5) Modpack Maker for VP       │ Leave off for normal play; builds modpacks (§10)      │
└────────────────────────────────┴───────────────────────────────────────────────────────┘
```

> **CRITICAL** — Back returns to the main menu and silently deactivates the mod set — the most common "I installed VP and nothing changed" report.

**Check:** after **NEXT** the menu lists the enabled mods; missing VP units or wrong text: §13.

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

In multiplayer the modpack thread asks for 500 autosaves. The aim is a save from the turn *before* a problem.

### Crashes

**Late-game crashes are a memory problem, not a Vox Populi bug.** Civ V is 32-bit, and the project attributes most late-game CTDs (crashes to desktop) to address-space exhaustion. Mitigations, most important first:

1. Leader Scene Quality on Minimum.
2. Yield icons off from the Industrial era, or avoid zooming far out.
3. Standard or small maps.
4. A lower in-game resolution — ultrawide and 4K panels are the demanding case.

Proton already helps: `PROTON_FORCE_LARGE_ADDRESS_AWARE` is **on by default**, giving the 32-bit executable a 4 GB address space instead of 2 GB; leave it alone.

**Early, random crashes are a different problem.** ProtonDB reports tie them to thread count. Under Proton the reported fix is the launch option `taskset -c 0-7 %command%` (**Properties → General → Launch Options**), which pins the game to eight threads. One reporter instead raised `MaxSimultaneousThreads` in the Documents folder's `config.ini` (default 8) to the machine's thread count; another found that 24 stopped the game from starting. Reports that edit that key under `~/.local/share/Aspyr` concern the native build; Proton never reads that file.

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

The table pairs each Options → Video setting with its key in `GraphicsSettingsDX11.ini` and a performance-first value; forum benchmarks single out anti-aliasing and terrain tessellation.

```
┌──────────────────────────────┬──────────────────────────┬──────────────────────────────┐
│ Options → Video              │ Key                      │ Performance-first            │
├──────────────────────────────┼──────────────────────────┼──────────────────────────────┤
│ Anti-Aliasing                │ MSAASamples              │ Off (1). Costliest item;     │
│                              │                          │ DX11 AA can also black out   │
│                              │                          │ the screen                   │
│ VSync                        │ WaitForVSync             │ On (1). Caps at the refresh  │
│                              │                          │ rate, steadies frame times   │
│ Leader Scene Quality         │ —                        │ Minimum — saves memory       │
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

Two fixes exist only in the files. `MinimizeGrayTiles = 1` in the graphics file stops the gray tiles that appear while scrolling on weaker GPUs (the official Steam FAQ's fix). `FullScreen`, `WindowResX` and `WindowResY` under `[UserSettings]` in the graphics file recover a game that opens at a size the screen cannot show; the DX11 build needs at least 768 pixels of height. For wall-clock time rather than frame rate, turn on **Quick Combat** and **Quick Movement** under Options → Game.

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

Mods for VP are posted in the Community Patch Project's [**Mods Repository**](https://forums.civfanatics.com/forums/549/) subforum. Its sticky [*Mod Compatibility with Latest Version*](https://forums.civfanatics.com/threads/701787/) (January 2026) says every mod there is meant to work with the current VP; mods that stop working move to the **Mods Archive**. A thread's opening post holds the download and its last pages hold the reports for 5.4.6. Copies in the site's **Downloads** section and on the Steam Workshop can lag by years.

### Popular mods for VP

Eight of the most popular mods made for VP, by CivFanatics downloads, thread views and players' shared mod lists; all show 2026 activity and no reported breakage:

```
┌────────────────────────────────────┬────────────────────────┬──────────────────────────┐
│ Mod and author                     │ What it adds           │ Version, needs, status   │
├────────────────────────────────────┼────────────────────────┼──────────────────────────┤
│ More Wonders for VP                │ New world and natural  │ v24.11, VP 5.3.3+; an    │
│   by adan_eslavo                   │ wonders                │ effects folder goes in   │
│                                    │                        │ the game folder          │
│ Even More Resources for VP         │ New bonus, luxury and  │ also on GitHub; skip     │
│   by HungryForFood                 │ city-state resources   │ the Workshop copy        │
│ Unique City-States                 │ Unique traits for      │ v19.3, VP 5.4.x          │
│   by adan_eslavo                   │ city-states            │                          │
│ City-States Leaders for VP         │ Leaders for            │ v26, VP 5.3.x; made      │
│   by adan_eslavo                   │ city-states            │ for Unique City-States   │
│ Community Events                   │ More random events     │ thread active 2026       │
│   by Enginseer                     │                        │                          │
│ Wonder Planner for VP              │ Planning screen for    │ v20                      │
│   by adan_eslavo                   │ wonders                │                          │
│ Better Lakes for VP                │ Lake rework            │ thread active 2026       │
│   by InkAxis                       │                        │                          │
│ Unit Scaling and Formation for VP  │ Unit model size and    │ thread active 2026       │
│   by N.Core                        │ formations             │                          │
└────────────────────────────────────┴────────────────────────┴──────────────────────────┘
```

Versions are as the authors state them; "thread active 2026" means 2026 posts but no stated VP version. Do not install More Unique Components: it is part of VP 5.

### Installing a mod

1. Download from the opening post, not the Steam Workshop (see the rules above).
2. Extract the archive. A `.civ5mod` is a 7-Zip archive under another name; rename it to `.7z` if your archiver refuses it, and extract it yourself rather than leaving it in `MODS`.
3. Move the folder that contains the `.modinfo` file straight into the Documents folder's `MODS`, beside the numbered VP folders — not the archive, not a folder inside a folder.
4. Opening posts give Windows paths. One under `steamapps\common\Sid Meier's Civilization V` means the real game folder (§2), never a `C:\Program Files (x86)` tree inside the prefix (Step 8).
5. Delete `cache` and `ModUserData`.
6. In the MODS menu, enable the VP set first, then the new mod, then press **NEXT**. The mod lists what it requires: nearly all need (1) and (2).

Add one mod at a time and play a few turns before the next, so a crash points at the last one added (§13). For multiplayer the mod must be part of the modpack (§10); a mod enabled through the menu cannot join a modpack game.

---

## 10. Multiplayer

VP cannot be played in multiplayer through the MODS menu; it must be packaged as a modpack that loads automatically as a DLC.

**Preferred — a prebuilt modpack.** The CivFanatics [modpack thread 685164](https://forums.civfanatics.com/threads/685164/) tracks current releases (5.4.6), including one generated on Linux under Proton. Extract so its folders — `ZMP_MODPACK` and any `VPUI` or `UI_bc1` it carries — sit directly under the game folder's `Assets/DLC`, then delete the Documents folder's `cache`.

**Alternative — build your own** with the Modpack Maker:

1. Turn logging on (§15), then enable `(5) Modpack Maker for VP` plus every mod to include.
2. Start or load a game and press **Ctrl+Shift+M**.
3. Check `Logs/Lua.log` in the Documents folder for errors.
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

**Known issues.** Community-Patch-DLL #13349, open since 2026-09-05, reports a 5.4.x multiplayer desync that its reporter suspects lies in pathfinding; check its state before a long game.

---

## 11. Updating

Back up first: copy `Saves` and `MODS` from the Documents folder to somewhere outside the prefix. A prefix reset destroys the saves, and the installer rewrites the mod folders, losing any edit under `MODS`.

Then download the newer `Vox.Populi.<version>.exe`, check it against its own digest on the release page (Step 5) and run it exactly as in Steps 6–7. It deletes the old mod folders and `cache` before writing; no manual cleanup.

By the project's versioning rule, saves are compatible when only the third version component changes: 5.4.4 → 5.4.6 keeps them, 5.4.x → 5.5.0 does not.

> **WARNING** — Steam's *Verify integrity of game files* restores the stock `Expansion2.Civ5Pkg`, which VP replaces to fix city-state audio. Re-run the VP installer after any verify.

---

## 12. Uninstalling

**Preferred — Uninstall all.** Re-run the installer, choose **Uninstall all** and point the Civilization V folder page at the same real install; this also restores the stock `Expansion2.Civ5Pkg`.

**Fallback — manual removal.** Delete these, then verify game files in Steam to restore the stock `Expansion2.Civ5Pkg`:

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

### Setup and install

```
┌────────────────────────────────────┬───────────────────────────────────────────────────┐
│ Symptom                            │ Cause and fix                                     │
├────────────────────────────────────┼───────────────────────────────────────────────────┤
│ Steam: "disk write error" while it │ Steam bug with case-mismatched depot folders,     │
│   downloads the Windows depots     │ fixed in July 2026. Update Steam; else create the │
│   (Step 1)                         │ folder the error names, in that exact case, under │
│                                    │ steamapps/downloading/8930.                       │
│ "Invalid file magic number"        │ Protontricks older than 1.12.0. Upgrade, or use   │
│                                    │ the Flatpak (§3).                                 │
│ Flatpak: "does not appear to have  │ The file or library is outside the Flatpak's      │
│   access to the following          │ folders (§3): move it or grant access as the      │
│   directories"                     │ message says.                                     │
│ Protontricks does not list Civ V   │ Prefix absent. Launch once via Proton (Step 3).   │
│ "Proton installation could not be  │ A Steam Linux Runtime entry is selected, not      │
│   found!"                          │ Proton. Pick Proton (Step 1).                     │
│ "command cabextract … returned     │ cabextract trips over a symlink Proton created.   │
│   status 1"                        │ Delete the symlink the error names, then re-run.  │
│ Wizard never appears               │ Check other windows (Wayland focus), then try     │
│                                    │ Proton Experimental.                              │
│ "did not provide the correct path" │ The folder has no Assets\DLC child. Point at the  │
│                                    │ game root, not Assets or DLC.                     │
│ "You don't have all required DLCs" │ The message names the missing packs. Install them │
│                                    │ (Step 2).                                         │
└────────────────────────────────────┴───────────────────────────────────────────────────┘
```

### In the game

```
┌────────────────────────────────────┬───────────────────────────────────────────────────┐
│ Symptom                            │ Cause and fix                                     │
├────────────────────────────────────┼───────────────────────────────────────────────────┤
│ Crash during the first             │ Relaunch and enter MODS again (§6); if it         │
│   "configuring game data" pass     │ repeats, delete cache.                            │
│ VP absent from the in-game mod     │ The Documents page was changed; mods are outside  │
│   list                             │ the prefix. Re-run with the default path.         │
│ Missing textures, broken UI        │ The Civ V folder page pointed into the prefix.    │
│                                    │ Re-run, or copy across per Step 8.                │
│ EUI works, no other VP features    │ Back was pressed in MODS, or (3a) is off on an    │
│                                    │ EUI install. Enable all mods, press NEXT.         │
│ Tech or policy tree unchanged, or  │ Game language is not English. Steam → Properties  │
│   entries show wrong text          │ → Language → English, then delete cache (§2).     │
│ Mods stale, duplicated or missing  │ Delete cache and ModUserData (§9); look for       │
│                                    │ Workshop copies sharing mod IDs, or a mod with    │
│                                    │ its own DLL.                                      │
│ CustomModOption change has no      │ Cache not cleared, or the option needed its       │
│   effect                           │ defines (§8).                                     │
│ Random crashes early in a session  │ Thread count: pin eight threads with taskset, or  │
│                                    │ set MaxSimultaneousThreads (§7).                  │
│ Multiplayer crashes after turn 1   │ A player did not delete cache before launch       │
│                                    │ (§10).                                            │
│ Crash to desktop in the late game  │ The 32-bit address space ran out. Apply §7 in     │
│                                    │ order; keep crashlogs for a report (§15).         │
│ Still broken after all of the      │ Minimal install — VP without EUI, no other mods — │
│   above                            │ then add one layer at a time.                     │
└────────────────────────────────────┴───────────────────────────────────────────────────┘
```

---

## 14. Alternative installer

[github.com/Alpakinator/civ5vp-installer](https://github.com/Alpakinator/civ5vp-installer) is a single-file native Linux binary that installs VP without Protontricks — a fallback if the Inno wizard misbehaves, newer and less tested than the Protontricks route. It finds the Civ V folders itself, has a modpack mode, and its Uninstall button restores an unmodded game.

```
civ5vp-installer-linux-x86_64 (v0.1.6, 2026-09-15) — 44,735,720 bytes
sha256  a0b59eb445d23bc9a5206ec0f592a7f81c4cfd40a5f2e0d823737bacbd271baa
```

Verify the digest as in Step 5, mark the file executable (Dolphin: **Properties → Permissions → Is executable**) and open it.

> **WARNING** — Install release versions only. On 2026-08-26 the author advised against its "Compile the DLL myself" option, single player included: its compiler can produce a DLL that freezes the game.

---

## 15. Bug reporting

Vox Populi bugs go to the project's tracker, [github.com/LoneGazebo/Community-Patch-DLL/issues](https://github.com/LoneGazebo/Community-Patch-DLL/issues), not the forum; errors in this guide go to [github.com/ryanmusante/vox-populi-cachyos/issues](https://github.com/ryanmusante/vox-populi-cachyos/issues). The bug form requires the mod version, the installed components — the setup type from Step 7 — and a description, and asks for three attachments:

```
┌────────────────────────┬───────────────────────────────────────────────────────────────┐
│ Attachment             │ Where it is                                                   │
├────────────────────────┼───────────────────────────────────────────────────────────────┤
│ Save from one turn     │ Saves in the Documents folder — the reason for the autosave   │
│   before the problem   │ settings in §7                                                │
│ Logs                   │ Logs in the Documents folder, zipped — useless unless logging │
│                        │ was already on                                                │
│ Crash artifacts, when  │ crashlogs in the game folder (crashes.log, .dmp) per the      │
│   reporting a crash    │ issue form; the minidump guide says CvMiniDump_*.dmp beside   │
│                        │ the game executable — check both                              │
└────────────────────────┴───────────────────────────────────────────────────────────────┘
```

Logging is off by default and must be on *before* the problem occurs: in the Documents folder's `config.ini`, set `ValidateGameDatabase`, `LoggingEnabled`, `MessageLog`, `AILog`, `AIPerfLog`, `BuilderAILog` and `PlayerAndCityAILogSplit` to `1`. Collect logs *before* loading a game — most are erased on load — and turn logging off afterwards.

Dumps are not guaranteed under Proton; if none appears after a crash, say so in the report and attach the logs and save.

---

## 16. Path reference

`<library>` is the §2 Steam library; indented rows sit inside the row above.

```
┌────────────────────────────┬───────────────────────────────────────────────────────────┐
│ Location                   │ Path                                                      │
├────────────────────────────┼───────────────────────────────────────────────────────────┤
│ Steam library              │ ~/.local/share/Steam by default, also ~/.steam/root;      │
│                            │ other packagings in §3                                    │
│ Game folder                │ <library>/steamapps/common/Sid Meier's Civilization V     │
│   As the wizard sees it    │ S:\steamapps\common\Sid Meier's Civilization V,           │
│                            │ or its Z:\ form (Step 7)                                  │
│ Prefix                     │ <library>/steamapps/compatdata/8930/pfx                   │
│ Documents folder           │ pfx/drive_c/users/steamuser/Documents/My Games/           │
│                            │ Sid Meier's Civilization 5                                │
│   Mods                     │ MODS — the numbered VP folders and any added mod (§6, §9) │
│   Caches                   │ cache · ModUserData — delete after any mod change (§9)    │
│   Saves                    │ Saves · Saves/multi/auto for multiplayer autosaves (§10)  │
└────────────────────────────┴───────────────────────────────────────────────────────────┘
```

---

## 17. Limits

- **Written against 5.4.6**; the sha256 is for 5.4.6 only.
- **Read from source, not exercised:** the `S:` drive default, the Protontricks and Winetricks window routes and the §8 toggles; `ENABLE_ACHIEVEMENTS` is marked "FUNCTIONALITY NOT GUARANTEED" upstream.
- **Community reports, not upstream statements:** the English-language requirement, the runtime-library step in Step 4, the ProtonDB fixes in §7 and §13, and the 2010–2013 Windows benchmarks behind §7's performance values.
- **The mod list is a 2026-10-04 snapshot**; none was tested under Proton, and a mod's last forum page is the authority for 5.4.6.
- **Untested:** the §3 distributions (checked against package listings only), file managers other than KDE's Dolphin, Flatpak and Snap Steam, the 43-civ variants and civ5vp-installer's local DLL build.
