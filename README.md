# Vox Populi on CachyOS

**Civilization V + Vox Populi via Steam and Proton**

Revision 8.0 · 2026-09-12 · Verified against Vox Populi 5.4.6 (stable), Protontricks 1.14.1-1, CachyOS with native Steam.

**Every command block below is fish**, which is the default login shell on CachyOS. Since the July 2025 snapshot the installer also offers zsh or bash; if you picked one of those, three fish constructs need translating:

```
┌──────────────────────────────┬──────────────────────────────────────────────────┐
│ fish                         │ bash / zsh                                       │
├──────────────────────────────┼──────────────────────────────────────────────────┤
│ set -g NAME "value"          │ NAME="value"                                     │
│ set NAME (command)           │ NAME="$(command)"                                │
│ cmd; and other               │ cmd && other                                     │
│ test -d x; and A; or B       │ if [ -d x ]; then A; else B; fi                  │
└──────────────────────────────┴──────────────────────────────────────────────────┘
```

Blocks tagged `fish` are commands to run; untagged blocks are reference tables and paths. Every step ends in a check you can run. `WARNING` marks a common failure; `CRITICAL` marks a step that decides whether this works at all.

The same guide is also here as `vox-populi-cachyos.pdf` (print-ready, US Letter) and `vox-populi-cachyos-print.html`.

---

## Contents

1. [How it works](#1-how-it-works) · 2. [Requirements](#2-requirements) · 3. [Stage A — game under Proton](#3-stage-a--game-under-proton) · 4. [Stage B — install Vox Populi](#4-stage-b--install-vox-populi) · 5. [Stage C — first run](#5-stage-c--first-run) · 6. [Settings and stability](#6-settings-and-stability) · 7. [Adding other mods](#7-adding-other-mods) · 8. [Optional tuning](#8-optional-tuning) · 9. [Multiplayer](#9-multiplayer) · 10. [Updating](#10-updating) · 11. [Uninstalling](#11-uninstalling) · 12. [Troubleshooting](#12-troubleshooting) · 13. [Alternative installer](#13-alternative-installer) · 14. [Bug reporting](#14-bug-reporting) · 15. [Command appendix](#15-command-appendix) · 16. [Path reference](#16-path-reference) · 17. [Sources](#17-sources) · 18. [Limits](#18-limits)

---

## 1. How it works

**Proton is mandatory.** Vox Populi ships a compiled game-core DLL built against the Civ V SDK. The native Aspyr Linux build cannot load it, so the Windows build under Proton is the only working configuration.

**The installer is Inno Setup 6** (Installer Version 1.2), a plain Win32 wizard that renders correctly under Proton. Reports about .NET installers failing under Wine do not apply.

**It asks for two different paths.** This is the crux.

```
┌────────────────────────┬──────────────────────────────┬─────────────────────────────────┐
│ Wizard page            │ Resolves to                  │ Receives                        │
├────────────────────────┼──────────────────────────────┼─────────────────────────────────┤
│ Select Destination     │ {userdocs}\My Games\         │ MODS\(1) Community Patch        │
│ Location               │ Sid Meier's Civilization 5   │ MODS\(2) Vox Populi             │
│ — the Documents path   │                              │ MODS\(3a) VP - EUI Compat Files │
│                        │ Under Proton:                │ MODS\(3b) 43 Civs Community …   │
│ LEAVE THIS ALONE       │ C:\users\steamuser\          │ MODS\(4a) Squads for VP         │
│                        │ Documents\… (in the prefix)  │ MODS\(5) Modpack Maker for VP   │
│                        │                              │ Text\VPUI_tips_en_us.xml        │
├────────────────────────┼──────────────────────────────┼─────────────────────────────────┤
│ Select the             │ Whatever you browse to. Must │ Assets\DLC\VPUI                 │
│ Civilization V folder  │ be the REAL game install,    │ Assets\DLC\UI_bc1  (EUI only)   │
│ — custom page, right   │ reached through Z:\ (Wine    │ Assets\DLC\Expansion2\          │
│ after the setup-type   │ maps Z: to /)                │   Expansion2.Civ5Pkg (replaced) │
│ page                   │                              │ Assets\DLC\Expansion2\Sounds\   │
│ THIS IS THE ONE THAT   │                              │   XML\MinorCivSounds_VoxPopuli  │
│ GOES WRONG             │                              │                                 │
└────────────────────────┴──────────────────────────────┴─────────────────────────────────┘
```

The Documents default is already correct — it resolves inside the prefix, where the Windows build reads mods from. The Civilization V folder page auto-fills only if a Windows-style Steam path already exists inside the prefix, which on a clean prefix it does not; the field arrives blank.

> **CRITICAL** — Aim that page at the real Linux install and nothing ever needs copying. Aim it inside the prefix and the UI assets land where the game will never read them. This is why some guides require a copy step and others do not.

**Every DLC must be installed, not merely owned.** The wizard checks for `DLC_01`–`DLC_07`, `DLC_Deluxe`, `Expansion`, `Expansion2` and blocks installation if any is missing. In practice that is the Complete Edition set on Civ V 1.0.3.279; the installer's information page names the Mongols, Spain & Inca, Polynesia, Denmark and Korea leader packs, Wonders of the Ancient World, Gods & Kings and Brave New World. Of the folder names, `DLC_Deluxe` is the Babylon pack, `Expansion` is Gods & Kings and `Expansion2` is Brave New World.

**It cleans up after itself.** Before writing it deletes the prefix `cache`, the current VP mod folders, and legacy CBP/CBO/CSD folder names. Do not clear the cache or delete old versions by hand. The corollary: anything you edit inside a VP mod folder is destroyed by the next install or update.

**There is no uninstaller entry** (`Uninstallable=no`). Removal is the **Uninstall all** setup type in the same `.exe`.

**Stable and beta releases both exist.** Each GitHub release links a release-notes thread titled *New STABLE Version* or *New BETA Version*. 5.4.6 (2026-08-31) is stable.

---

## 2. Requirements

```
┌──────────────┬────────────────────────────────────────────────────────────────────────┐
│ Game         │ Civilization V 1.0.3.279, all expansions and all DLC installed         │
│ Steam        │ Native Steam (multilib `steam`); data dir ~/.local/share/Steam,        │
│              │ also reachable through ~/.steam/root                                   │
│ Compat tool  │ Proton Experimental or newest numbered Proton; proton-cachyos-slr OK.  │
│              │ NOT "Steam Linux Runtime" — that is not Proton                         │
│ Tooling      │ extra/protontricks 1.14.1-1 (pulls winetricks) + yad, zenity for GUIs  │
│ Disk         │ Full re-download of the Windows depots, plus ~105 MB installer         │
│ Conflicts    │ No Workshop subscriptions to Community Patch, More Luxuries, City-State│
│              │ Diplomacy, Civ IV Diplomatic Features; no mod shipping its own DLL     │
└──────────────┴────────────────────────────────────────────────────────────────────────┘
```

Set these once; every command below uses them.

```fish
set -g CIV5_LIB  "$HOME/.local/share/Steam"
set -g CIV5_GAME "$CIV5_LIB/steamapps/common/Sid Meier's Civilization V"
set -g CIV5_PFX  "$CIV5_LIB/steamapps/compatdata/8930/pfx/drive_c"
set -g CIV5_DOCS "$CIV5_PFX/users/steamuser/Documents/My Games/Sid Meier's Civilization 5"
```

If Civ V sits in a secondary Steam library, adjust `CIV5_LIB`; `compatdata/8930` always lives in the same library as the game. List the libraries with:

```fish
rg -o '"path"\s+"([^"]+)"' -r '$1' "$HOME/.steam/root/steamapps/libraryfolders.vdf"
```

---

## 3. Stage A — game under Proton

### Step 1 · Force Proton

Steam → right-click **Sid Meier's Civilization V** → **Properties → Compatibility** → tick **Force the use of a specific Steam Play compatibility tool** → Proton Experimental or the newest numbered Proton. Steam replaces the native build with the Windows depots; let it finish.

> **WARNING** — The same dropdown lists Steam Linux Runtime entries. They are not Proton: no Wine prefix is set up, and `protontricks-launch` fails with a winetricks environment error.

CachyOS alternatives (install, restart Steam): `proton-cachyos-slr`, `proton-cachyos-native`, or `protonup-qt` / `protonplus` for Proton-GE. Prefer `-slr`: it is a Proton build packaged to run inside the Steam Linux Runtime container, which is not the same thing as the bare *Steam Linux Runtime* entries warned about above.

Optional: set launch options to `PROTON_SET_GAME_DRIVE=1 %command%`. Proton then maps `S:` to the game's Steam library, shortening the browse in Step 7. Verify after Step 3 with `ls -l "$CIV5_LIB/steamapps/compatdata/8930/pfx/dosdevices"`; if no `s:` appears, use the `Z:` path instead.

### Step 2 · Confirm every DLC

Steam → Properties → **DLC** → tick everything, then:

```fish
for d in DLC_01 DLC_02 DLC_03 DLC_04 DLC_05 DLC_06 DLC_07 DLC_Deluxe Expansion Expansion2
    test -d "$CIV5_GAME/Assets/DLC/$d"; and echo "ok   $d"; or echo "MISS $d"
end
```

**Check:** ten `ok` lines. Any `MISS` stops the installer at Step 7.

### Step 3 · Launch once, reach the main menu, quit

This creates `compatdata/8930/pfx`, without which protontricks cannot see the game, and lets Civ V build its Documents tree inside the prefix. If the Firaxis launcher appears, choose DirectX 10/11 — an upstream report (Proton #8327) notes the DX9 executable may start regardless; this does not affect Vox Populi.

**Check:**

```fish
ls "$CIV5_PFX"    # Program Files, Program Files (x86), users, windows
ls "$CIV5_DOCS"   # expect Logs, Saves, config.ini among others
```

---

## 4. Stage B — install Vox Populi

### Step 4 · Protontricks

```fish
sudo pacman -S protontricks yad zenity
protontricks -s civilization
```

**Check:** prints Sid Meier's Civilization V with App ID **8930**. Nothing printed means Step 3 did not complete.

<details>
<summary>Flatpak Protontricks, or Flatpak Steam</summary>

```fish
flatpak install flathub com.github.Matoking.protontricks
flatpak override --user --filesystem=$HOME/Downloads com.github.Matoking.protontricks
# one --filesystem= override per Steam library outside the default location
```

Flatpak Protontricks is sandboxed to the Steam directory; without an override covering the `.exe` you are launching, it fails with "library folder not found". Separately, if *Steam itself* is the Flatpak, every path in §16 moves under `~/.var/app/com.valvesoftware.Steam/`.

</details>

### Step 5 · Download and verify

Releases are GitHub assets on `LoneGazebo/Community-Patch-DLL`, named `Vox.Populi.<version>.exe`.

```fish
curl -L -o "$HOME/Downloads/Vox.Populi.5.4.6.exe" \
  https://github.com/LoneGazebo/Community-Patch-DLL/releases/download/Release-5.4.6/Vox.Populi.5.4.6.exe
sha256sum "$HOME/Downloads/Vox.Populi.5.4.6.exe"
```

**Check:** `31679423e55d7f64ba9693d8252d9e29f824fd9f8da043af547470068f98ba8a`

For other versions, take the digest from that release's asset listing and confirm STABLE vs BETA in its release-notes thread title. `Release_Debug.zip` is a debug DLL for crash reports, not needed for play.

### Step 6 · Run it inside the prefix

```fish
protontricks-launch --appid 8930 "$HOME/Downloads/Vox.Populi.5.4.6.exe"
```

GUI: right-click the `.exe` → **Open With → Protontricks Launcher** → Sid Meier's Civilization V.
Flatpak: `flatpak run --command=protontricks-launch com.github.Matoking.protontricks --appid 8930 <exe>`

The wizard can take a minute to appear and may not be raised to the foreground under Wayland. A 64-bit WINEPREFIX warning is expected and documented — Civ V is 32-bit inside a 64-bit prefix.

### Step 7 · Work the wizard

```
┌───┬──────────────────────────┬──────────────────────────────────────────────────────┐
│   │ Page                     │ Action                                               │
├───┼──────────────────────────┼──────────────────────────────────────────────────────┤
│   │ License · Information    │ Next                                                 │
│ ★ │ Select Destination       │ LEAVE UNCHANGED. Must read C:\users\steamuser\       │
│   │ Location                 │ Documents\My Games\Sid Meier's Civilization 5        │
│ ★ │ Setup Type / Components  │ Choose one variant — table below                     │
│ ★ │ Select the Civilization  │ Browse to S:\steamapps\common\Sid Meier's            │
│   │ V folder                 │ Civilization V, or Z:\home\<you>\.local\share\Steam\ │
│   │                          │ steamapps\common\Sid Meier's Civilization V          │
│   │ Start Menu folder        │ May be skipped; irrelevant under Proton, Next        │
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
│ Uninstall all                 │ Removal path — §11                                   │
└───────────────────────────────┴──────────────────────────────────────────────────────┘
```

If the Ready page shows a `C:\Program Files (x86)` path, go back and fix it rather than copying files afterwards. The installer also fails if the MODS folder has been moved out of the Documents tree — under Proton that only happens if you change the Documents page yourself.

### Step 8 · Verify placement

```fish
ls "$CIV5_DOCS/MODS"                                    # (1)…(5) for your variant
ls "$CIV5_GAME/Assets/DLC" | rg -i 'VPUI|UI_bc1'        # VPUI, + UI_bc1 for EUI
ls "$CIV5_PFX/Program Files (x86)/Steam/steamapps/common/Sid Meier's Civilization V/Assets/DLC" 2>/dev/null
```

The third command must print nothing — that is the pass/fail test for Step 7. If it lists `VPUI` or `UI_bc1`, re-run the installer with the correct path. Fallback if you would rather not:

```fish
rsync -a --dry-run \
  "$CIV5_PFX/Program Files (x86)/Steam/steamapps/common/Sid Meier's Civilization V/Assets/" \
  "$CIV5_GAME/Assets/"
```

Inspect, then re-run without `--dry-run`. Note there is no `Assets/DLC/Vox Populi` folder — the installer creates `VPUI` and, for EUI variants, `UI_bc1`.

---

## 5. Stage C — first run

1. Launch Civ V from Steam.
2. Main menu → **MODS**; accept the prompt about DLC being disabled and the game restarting.
3. The first entry into the mods menu runs a long "configuring game data" pass — from under a minute to 5–15 minutes. Not a hang.
4. Enable the VP mods the installer placed in `MODS`.
5. Press **NEXT**, never **Back**.
6. **Single Player → Set Up Game.**

> **CRITICAL** — Back returns to the main menu and silently deactivates the mod set. The installer's own finish screen says to enable all installed mods and click Next, not Back. This is the most common "I installed VP and nothing changed" report.

```
┌──────────────────────────────┬─────────────────────────────────────────────────────┐
│ (2) Vox Populi               │ Requires (1) Community Patch                        │
│ (3a) VP - EUI Compat Files   │ MUST be enabled on any EUI install, or VP will not  │
│                              │ function                                            │
│ (3b) 43 Civs Community Patch │ Only for 43-civ variants                            │
│ (4a) Squads for VP           │ Optional QoL — RTS-style control groups, CTRL+number│
│ (5) Modpack Maker for VP     │ Leave off for normal play; builds modpacks (§9)     │
└──────────────────────────────┴─────────────────────────────────────────────────────┘
```

**Check:** the main menu lists active mods in the lower right. EUI working but no new units, luxuries, or advanced-setup options means the base game with EUI only — redo from the MODS menu without pressing Back.

---

## 6. Settings and stability

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

The single-player figures are what the install thread and the project's bug-report form both ask for; the modpack thread asks for 500 in multiplayer. The purpose is the same either way: the bug form wants a save from the turn *before* the problem, which only exists if autosaves are frequent and not being rotated away.

**Late-game crashes are a memory problem, not a Vox Populi bug.** Civ V is a 32-bit executable and the project attributes most late-game CTDs to address-space exhaustion. Mitigations, the first being the one the project calls most important for solo play: Leader Scene Quality on Minimum; yield icons off from the Industrial era (avoid zooming far out if you keep them); standard or small maps; lower in-game resolution. Ultrawide and 4K panels sit at the demanding end of that last point.

Proton helps here: `PROTON_FORCE_LARGE_ADDRESS_AWARE` is **on by default**, giving the 32-bit executable a 4 GB address space instead of 2 GB. Leave it alone.

Late-game turn times are AI-bound, not GPU-bound. Treat graphics settings as a memory lever rather than a frame-rate one, and cap the frame rate at the panel's refresh rate with V-Sync.

---

## 7. Adding other mods

```
┌────────────────────────────────────────────────────────────────────────────────────┐
│ Any mod that ships its own DLL is incompatible. VP replaces the Civ V DLL          │
│   entirely, and the Community Patch cannot coexist with another DLL mod.           │
│ Steam Workshop subscriptions do not reliably land in the right place under Proton. │
│   Download from CivFanatics or GitHub and extract manually.                        │
│ Mods go in $CIV5_DOCS/MODS — inside the prefix, beside the VP folders.             │
│ After ANY mod change, delete cache/ and ModUserData/ before launching. Stale cache │
│   is the single most common reason mods look broken after a change.                │
│ Filenames do not need lowercasing under Proton; that applies to the native build.  │
└────────────────────────────────────────────────────────────────────────────────────┘
```

```fish
rm -rf "$CIV5_DOCS/cache" "$CIV5_DOCS/ModUserData"
```

Check the CivFanatics "MODS compatible with Vox Populi (VP)" thread before adding anything; VP reworks most systems, so mods that touch game components are often only partly compatible. Reported working on Linux under Proton: Community Events, Improved City View (Gazebo's version), most of WHoward's Pick'N'Mix, and Info Addict (which needs an extra patch alongside EUI).

---

## 8. Optional tuning

### 8.1 Community Patch options file

Open this file in your editor:

```
$CIV5_DOCS/MODS/(1) Community Patch/Database Changes/NewCustomModOptions.xml
```

Rules from the file's own header: an option with a **"See also:"** comment must be enabled through its mod, not here; one listing **"Defines:"** or **"PostDefines:"** needs those defines verified first; anything else is enabled by changing `Value` from `0` to `1`.

Each row carries a `Class` saying what kind of option it is, not how safe it is:

```
┌───┬───────────┬────────────────────────────────────────────────────────────────┐
│ 0 │ Data      │ Values consumed by other options                               │
│ 1 │ Bugfix    │ Unused — bugfixes are always on in the Community Patch         │
│ 2 │ API       │ Extensions with no effect unless another mod uses them         │
│ 3 │ Events    │ Gamecore triggers — significant overhead even when unused      │
│ 4 │ Minor     │ Limited gameplay changes                                       │
│ 5 │ Moderate  │ Noticeable gameplay changes                                    │
│ 6 │ Major     │ Significant gameplay changes                                   │
└───┴───────────┴────────────────────────────────────────────────────────────────┘
```

Leave Class 3 alone unless you need it; the header warns that those run triggers from the gamecore whether or not anything consumes them. The file holds the mod's entire option set — 29 API-class rows alone — of which these four are the ones most people want:

```
┌──────────────────────────┬─────────────────────────────────────────────────────────┐
│ ENABLE_ACHIEVEMENTS      │ Steam achievements in modded single-player. Marked      │
│                          │ "FUNCTIONALITY NOT GUARANTEED" and it changes the       │
│                          │ savegame format — set it before a campaign, not during  │
│ DIPLO_DEBUG_MODE         │ Reveals the AI's true opinion, approach and Congress    │
│ (+ …_SETTING)            │ voting; at setting 2 the AI accepts every Discuss       │
│                          │ request. The best window into how VP scores you         │
│ SQLITE_LOGGING           │ Writes gameplay statistics to a queryable stats.db      │
│ CORE_DEBUGGING           │ Extra gamecore debugging; slows the game, leave off     │
└──────────────────────────┴─────────────────────────────────────────────────────────┘
```

Clear the cache after editing, and keep a copy — the installer rewrites these folders on every update.

Use `ENABLE_ACHIEVEMENTS` rather than the executable-patching method circulated for other modded Civ V setups; it is unnecessary here. Related: map-type achievements are broken on the native Linux build regardless of mods, but work under Proton.

### 8.2 Back up before updating

```fish
set stamp (date +%Y%m%d)
set dest "$HOME/civ5-backup-$stamp"
mkdir -p "$dest"
rsync -a "$CIV5_DOCS/Saves" "$CIV5_DOCS/MODS" "$dest"/
```

Saves live inside the Wine prefix, so a full prefix reset destroys them too.

---

## 9. Multiplayer

VP cannot be played in multiplayer through the MODS menu; it must be packaged as a modpack that loads automatically as a DLC.

**Preferred — a prebuilt modpack.** The CivFanatics modpack thread tracks current releases (5.4.6), including one generated on Linux under Proton. Extract so the modpack folder sits directly under `$CIV5_GAME/Assets/DLC/`, then `rm -rf "$CIV5_DOCS/cache"`. Modpacks also work in single player and are easier to update than a mods-menu install.

**Or build your own:** enable `(5) Modpack Maker for VP` plus every mod to include → start or load a game → **Ctrl-Shift-M** → check `$CIV5_DOCS/Logs/Lua.log` for errors → exit → `rm -rf "$CIV5_DOCS/cache"` → relaunch and start from Single Player or Multiplayer, never the MODS menu.

```
┌────────────────────────────────────────────────────────────────────────────────────┐
│ Modpacks cannot be combined with mods activated through the MODS menu              │
│ Every player must use byte-identical modpacks                                      │
│ Every player must delete cache/ BEFORE EVERY LAUNCH, or the game will most likely  │
│   crash after the first turn                                                       │
│ Do not hand-edit Assets\DLC\VP_MODPACK — rebuild instead                           │
│ Saves do not record which modpack was used; a mismatch crashes                     │
│ Multiplayer autosaves live in Saves\multi\auto — collect them for desync reports   │
└────────────────────────────────────────────────────────────────────────────────────┘
```

Remove with `rm -rf "$CIV5_GAME/Assets/DLC/VP_MODPACK" "$CIV5_DOCS/cache"`.

MPPatch, which would allow modded multiplayer without modpacks, has had no release since December 2023, and a forum report describes crashes when combined with VP plus EUI. Modpacks remain the supported route.

---

## 10. Updating

Run the newer `Vox.Populi.<version>.exe` exactly as in Steps 6–7. It deletes the old mod folders and cache before writing; no manual cleanup. Take the §8.2 backup first if you have edited anything under `MODS`.

By the project's versioning rule, saves are compatible when only the third version component changes: 5.4.4 → 5.4.6 keeps them, 5.4.x → 5.5.0 does not.

> **WARNING** — Steam's *Verify integrity of game files* restores the stock `Expansion2.Civ5Pkg`, which VP replaces to fix city-state audio (including the BNW bug where Cape Town uses Almaty's clip). Re-run the VP installer after any verify.

---

## 11. Uninstalling

**Preferred**, because it restores the stock `Expansion2.Civ5Pkg`: re-run the installer, choose **Uninstall all**, and point the Civilization V folder page at the same real install.

**Manual fallback.** Swap `rm -rf` for `ls -d` first and confirm the list resolves as expected.

```fish
rm -rf "$CIV5_DOCS/MODS/(1) Community Patch" \
       "$CIV5_DOCS/MODS/(2) Vox Populi" \
       "$CIV5_DOCS/MODS/(3a) VP - EUI Compatibility Files" \
       "$CIV5_DOCS/MODS/(3b) 43 Civs Community Patch" \
       "$CIV5_DOCS/MODS/(4a) Squads for VP" \
       "$CIV5_DOCS/MODS/(5) Modpack Maker for VP" \
       "$CIV5_GAME/Assets/DLC/VPUI" \
       "$CIV5_GAME/Assets/DLC/UI_bc1" \
       "$CIV5_GAME/Assets/DLC/VP_MODPACK" \
       "$CIV5_DOCS/cache" "$CIV5_DOCS/ModUserData" \
       "$CIV5_DOCS/Text/VPUI_tips_en_us.xml"
```

Then verify game files in Steam to restore the stock `Expansion2` package and sound XML.

**Full prefix reset** (destroys in-prefix saves — back up first): `rm -rf "$CIV5_LIB/steamapps/compatdata/8930"`

---

## 12. Troubleshooting

Ordered by when the failure appears.

```
┌────────────────────────────────────────┬──────────────────────────────────────────────────────────────┐
│ Symptom                                │ Cause and fix                                                │
├────────────────────────────────────────┼──────────────────────────────────────────────────────────────┤
│ protontricks does not list Civ V       │ Prefix absent. Launch once via Proton (Step 3).              │
│ protontricks-launch: winetricks        │ Compat tool is a Steam Linux Runtime entry, not Proton.      │
│ environment error                      │ Re-select a real Proton build (Step 1).                      │
│ Flatpak: "library folder not found"    │ flatpak override --user --filesystem=<path> …protontricks    │
│ "Invalid file magic number"            │ Protontricks too old for the Steam client; extra's 1.14.1-1  │
│                                        │ is current.                                                  │
│ "Unknown option --no-bwrap"            │ Protontricks options go BEFORE the app ID.                   │
│ "warning: 64-bit WINEPREFIX"           │ Expected. Civ V is 32-bit. Ignore.                           │
│ Wizard never appears                   │ Wayland focus or a broken Proton build. Check other windows, │
│                                        │ then try Proton Experimental or proton-cachyos-slr.          │
│ "You don't have all required DLCs"     │ Missing DLC under the chosen path. Enable every DLC in Steam │
│                                        │ and re-run the Step 2 loop.                                  │
│ "did not provide the correct path"     │ Chosen folder has no Assets\DLC child. Point at the game     │
│                                        │ root, not Assets or DLC.                                     │
│ VP absent from the in-game mod list    │ Documents page was changed; mods are outside the prefix.     │
│                                        │ Re-run with the default Documents path.                      │
│ Missing textures, broken UI            │ Civ V folder page pointed inside the prefix. Re-run, or copy │
│                                        │ across per Step 8.                                           │
│ EUI works, no other VP features        │ Back was pressed in MODS, or (3a) is not enabled on an EUI   │
│                                        │ install. Re-enable all mods, press NEXT.                     │
│ Mods stale, duplicated or missing      │ rm -rf cache/ and ModUserData/ (§7). Then check for Workshop │
│                                        │ subscriptions sharing mod IDs, or a mod shipping its own DLL.│
│ CustomModOption change has no effect   │ Cache not cleared, or the option needed its defines. §8.1.   │
│ Still broken after all of the above    │ Minimal install — VP without EUI, no other mods — then add   │
│                                        │ EUI, then other mods, one layer at a time.                   │
│ Crash to desktop in the late game      │ 32-bit address-space exhaustion. Apply §6 in order, and      │
│                                        │ keep $CIV5_GAME/crashlogs for a report (§14).                │
│ Multiplayer crashes after turn 1       │ Cache not cleared before launch by every player (§9).        │
│ DX9 starts despite choosing DX11       │ Known Proton report #8327. Not a VP fault.                   │
└────────────────────────────────────────┴──────────────────────────────────────────────────────────────┘
```

---

## 13. Alternative installer

`github.com/Alpakinator/civ5vp-installer` — a single-file native Linux binary (v0.1.5, Apache-2.0, asset `civ5vp-installer-linux-x86_64`) that installs VP without Protontricks and understands Proton prefixes directly. It writes only to the game's MODS, DLC and Text folders, offers modpack mode and one-click uninstall, and can install unofficial or in-development versions — those it compiles locally, which costs about 1.1 GB of toolchain once and roughly a 5 GB data dir. Its documentation states the same Proton-only constraint as §1. Newer and less battle-tested than the Protontricks route; a reasonable fallback if the Inno wizard misbehaves.

---

## 14. Bug reporting

Bugs in Vox Populi go to the project's GitHub issue tracker, not the forum, which is likely to be ignored; errors in this guide belong on this repository's tracker. Blank issues are disabled upstream, so the bug form is the only route. It requires the mod version and the components you installed — note the setup type you chose in Step 7 — and asks you to always attach three things where possible:

```
┌────────────────────────┬─────────────────────────────────────────────────────────┐
│ Save from one turn     │ $CIV5_DOCS/Saves — the reason for the autosave settings │
│ before the problem     │ in §6                                                   │
│ Logs                   │ $CIV5_DOCS/Logs — useless unless logging was already on │
│ Crash artifacts, when  │ $CIV5_GAME/crashlogs for crashes.log and the .dmp, per  │
│ reporting a crash      │ the issue form. The minidump guide instead says dumps   │
│                        │ land beside the game executable as CvMiniDump_*.dmp —   │
│                        │ check both; the command below covers them.              │
└────────────────────────┴─────────────────────────────────────────────────────────┘
```

Logging is off by default and must be on *before* the problem occurs. Enable it once per game installation in `$CIV5_DOCS/config.ini` by setting `ValidateGameDatabase`, `LoggingEnabled`, `MessageLog`, `AILog`, `AIPerfLog`, `BuilderAILog` and `PlayerAndCityAILogSplit` to `1`. (The project's developer docs write this path with "Civilization V"; the folder is "Civilization 5".) Collect the logs *before* loading a game — most are erased on load.

```fish
cd "$CIV5_DOCS"; and zip -r ~/civ5-vp-logs.zip Logs
cd "$CIV5_GAME"; and zip -r ~/civ5-vp-crash.zip crashlogs
cd "$CIV5_GAME"; and find . -maxdepth 1 -name 'CvMiniDump_*.dmp' -exec zip ~/civ5-vp-crash.zip {} +
```

Dump generation is not guaranteed under Proton: the DLL loads `dbghelp.dll` from the prefix's `System32`, so it depends on Wine's implementation rather than the Windows one the feature was written against. If no dump appears after a crash, say so in the report and attach the logs and save instead.

Turn logging back off when you are not chasing a bug; it rewrites a large directory continuously.

The project wiki carries its own guidance on writing a useful report, a full changelog, a Lua API reference for modders, and a page explaining the linked and group movement that `(4a) Squads for VP` builds on.

---

## 15. Command appendix

```fish
set -g CIV5_LIB  "$HOME/.local/share/Steam"
set -g CIV5_GAME "$CIV5_LIB/steamapps/common/Sid Meier's Civilization V"
set -g CIV5_PFX  "$CIV5_LIB/steamapps/compatdata/8930/pfx/drive_c"
set -g CIV5_DOCS "$CIV5_PFX/users/steamuser/Documents/My Games/Sid Meier's Civilization 5"

sudo pacman -S protontricks yad zenity

# [Steam] Compatibility > force a Proton build (not a Linux Runtime)
# [Steam] DLC > enable everything
# [Steam] optional launch option: PROTON_SET_GAME_DRIVE=1 %command%

for d in DLC_01 DLC_02 DLC_03 DLC_04 DLC_05 DLC_06 DLC_07 DLC_Deluxe Expansion Expansion2
    test -d "$CIV5_GAME/Assets/DLC/$d"; and echo "ok   $d"; or echo "MISS $d"
end

# [Steam] launch once, reach the main menu, quit
ls "$CIV5_PFX"
ls -l "$CIV5_LIB/steamapps/compatdata/8930/pfx/dosdevices"
protontricks -s civilization

curl -L -o "$HOME/Downloads/Vox.Populi.5.4.6.exe" \
  https://github.com/LoneGazebo/Community-Patch-DLL/releases/download/Release-5.4.6/Vox.Populi.5.4.6.exe
sha256sum "$HOME/Downloads/Vox.Populi.5.4.6.exe"
# 31679423e55d7f64ba9693d8252d9e29f824fd9f8da043af547470068f98ba8a

# Documents page unchanged; Civ V folder browsed to S:\ or the Z:\ real install
protontricks-launch --appid 8930 "$HOME/Downloads/Vox.Populi.5.4.6.exe"

ls "$CIV5_DOCS/MODS"
ls "$CIV5_GAME/Assets/DLC" | rg -i 'VPUI|UI_bc1'
ls "$CIV5_PFX/Program Files (x86)/Steam/steamapps/common/Sid Meier's Civilization V/Assets/DLC" 2>/dev/null

# [Game] MODS > enable the VP mods > NEXT (never Back) > Single Player
# [Game] Options > Leader Scene Quality: Minimum; autosave every 1 turn
```

---

## 16. Path reference

```
┌────────────────────────────┬────────────────────────────────────────────────────────┐
│ Wine prefix root           │ $CIV5_PFX                                              │
│ Drive mappings             │ $CIV5_LIB/steamapps/compatdata/8930/pfx/dosdevices     │
│ Mods                       │ $CIV5_DOCS/MODS                                        │
│ CP options file            │ $CIV5_DOCS/MODS/(1) Community Patch/Database Changes/  │
│                            │ NewCustomModOptions.xml                                │
│ Cache · per-mod user data  │ $CIV5_DOCS/cache · $CIV5_DOCS/ModUserData              │
│ Logs · logging switches    │ $CIV5_DOCS/Logs · $CIV5_DOCS/config.ini                │
│ Saves (single · multi)     │ $CIV5_DOCS/Saves · $CIV5_DOCS/Saves/multi/auto         │
│ EUI tooltip text           │ $CIV5_DOCS/Text/VPUI_tips_en_us.xml                    │
│ Real game install          │ $CIV5_GAME                                             │
│ Crash artifacts            │ $CIV5_GAME/crashlogs, and CvMiniDump_*.dmp beside the  │
│                            │ game executable in $CIV5_GAME                          │
│ VP UI · EUI · modpack      │ $CIV5_GAME/Assets/DLC/VPUI · UI_bc1 · VP_MODPACK       │
│ Replaced BNW package       │ $CIV5_GAME/Assets/DLC/Expansion2/Expansion2.Civ5Pkg    │
│ Phantom tree (stays empty) │ $CIV5_PFX/Program Files (x86)/Steam/steamapps/common/  │
│                            │ Sid Meier's Civilization V                             │
└────────────────────────────┴────────────────────────────────────────────────────────┘
```

---

## 17. Sources

```
┌──────────────────────────────────────────────┬──────────────────────────────────────────────┐
│ Installer behaviour: two-path wizard, setup  │ LoneGazebo/Community-Patch-DLL —             │
│ types, DLC gate, cache and legacy-folder     │ VPSetupData.iss, scripts/release.py,         │
│ deletion, mod folders rewritten on install,  │ Opener.rtf                                   │
│ Uninstallable=no, Expansion2.Civ5Pkg swap,   │                                              │
│ Inno Setup, savegame compatibility rule,     │                                              │
│ Installer v1.2 requirement list              │                                              │
│ Mod dependencies, EUI rules, VPUI/UI_bc1     │ (1)/(2)/(3a) INSTRUCTIONS.txt and MANUAL     │
│ destinations, audio rationale; Squads is QoL │ INSTALL.txt; (4a) Squads modinfo             │
│ CustomModOptions rules and toggles           │ (1) Community Patch/Database Changes/        │
│                                              │ NewCustomModOptions.xml                      │
│ 1.0.3.279 + all DLC; logging keys; minidumps │ VP README.md, DEVELOPMENT.md, docs/          │
│                                              │ minidumps.md                                 │
│ Option Class legend and the Events overhead  │ NewCustomModOptions.xml header               │
│ Minidump filename pattern, location beside   │ docs/minidumps.md                            │
│ the executable, dbghelp.dll dependency       │                                              │
│ Blank issues disabled; required fields; the  │ .github/ISSUE_TEMPLATE/bug_report_v5.yml     │
│ three attachments; crashlogs path; the 0/1   │ and config.yml                               │
│ autosave rationale                           │                                              │
│ Report-writing guidance, Lua API, group      │ Community-Patch-DLL wiki                     │
│ movement page                                │                                              │
│ 5.4.6 stable, asset name, sha256             │ GitHub releases + release feed, 2026-08-31   │
│ Modpack: Ctrl-Shift-M, cache rules, removal  │ (5) Modpack Maker modinfo, ModpackMaker.lua  │
│ Prebuilt modpacks incl. Linux/Proton build;  │ CivFanatics modpack thread 685164            │
│ MP autosave and log-collection guidance      │                                              │
│ Late-game CTD from 32-bit memory; Leader     │ CivFanatics "Welcome to the Community Patch  │
│ Scene Quality, yield icons, maps, resolution;│ sub — Start Here" thread 701813              │
│ beta vs stable naming                        │                                              │
│ Autosave recommendation; Workshop and DLL    │ CivFanatics "What is Vox Populi – How To     │
│ conflicts; don't delete after the installer; │ Install" thread 528034; "MODS compatible     │
│ minimal-install triage; compatibility list   │ with Vox Populi" thread 542679               │
│ Linux mod handling: DLL exclusivity,         │ CivFanatics thread 702075 (schubman,         │
│ Workshop unreliability under Proton, MODS    │ 2026-02-13 → 2026-03-19), now stickied       │
│ location, cache + ModUserData, known-good    │                                              │
│ mods; Documents-path warning; launch first   │                                              │
│ Achievements via VP's own option; native     │ bmaupin/civ5-cheevos-with-mods README,       │
│ Linux map-achievement breakage               │ citing Community-Patch-DLL issue #12965      │
│ LARGE_ADDRESS_AWARE default; GAME_DRIVE;     │ ValveSoftware/Proton README (proton_9.0);    │
│ DX9/DX11 launch report                       │ Proton issue #8327                           │
│ Protontricks CLI, option order, prefix       │ Matoking/protontricks README +               │
│ warning, Flatpak overrides                   │ TROUBLESHOOTING.md; flathub manifest docs    │
│ Package versions and repos                   │ archlinux.org package DB; mirror.cachyos.org │
│ Steam default dir and ~/.steam/root          │ Arch Wiki: Steam                             │
│ Steam Linux Runtime breaks protontricks      │ TeaDrinkingProgrammer guide issue #3         │
│ Alternative installer                        │ Alpakinator/civ5vp-installer README + feed   │
│ MPPatch last release Dec 2023                │ Lymia/MPPatch release feed                   │
│ Original copy-the-Assets workaround          │ TeaDrinkingProgrammer GitHub guide           │
└──────────────────────────────────────────────┴──────────────────────────────────────────────┘
```

---

## 18. Limits

- **Thread 702075 was never readable verbatim** — CivFanatics serves only page metadata to automated fetchers, so its content here comes from indexed excerpts. Everything structural is taken from the Vox Populi source tree instead, which is why this document contradicts the thread on the `Assets/DLC` folder name and treats the copy step as avoidable.
- **`PROTON_SET_GAME_DRIVE` is an untested convenience.** Documented by Proton, but whether the `S:` mapping persists into a later `protontricks-launch` session was not tested; the Step 1 check settles it, and `Z:` always works.
- **§8.1 toggles were read from the source file, not exercised.** `ENABLE_ACHIEVEMENTS` carries the project's own "functionality not guaranteed" marking and changes the savegame format.
- **The two upstream sources disagree on where crash dumps land** — the issue form says `crashlogs`, the minidump guide says beside the game executable. Both are covered rather than resolved.
- **Written against 5.4.6.** Wizard page order and setup-type names have been stable across recent releases but are not guaranteed; the sha256 is for 5.4.6 only, and VP ships betas alongside stable releases.
- **Untested paths:** Flatpak Steam (procedure holds, paths move), the 43-civ variants, and MPPatch.
