# Changelog

## 9.0.3 — 2026-09-19

- fix: a Steam Linux Runtime entry makes Protontricks stop with "Proton installation could not be found!", not a winetricks environment error
- fix: the Flatpak sandbox message is "does not appear to have access to the following directories"; "library folder not found" does not exist
- fix: the manual uninstall list gains the added `MinorCivSounds_VoxPopuli.xml`; Steam's verify restores the package but leaves added files
- fix: §1, Step 8 and the troubleshooting row say the wizard blocks any folder without `Assets\DLC` and all ten DLC folders
- fix: Step 8 names the second command, not the third, as the pass/fail test, and says what Community Patch only leaves in the game folder
- fix: `(3a) VP - EUI Compatibility Files` and `(3b) 43 Civs Community Patch` are written out in full in the §1 and §6 tables
- fix: the `S:` fallback also applies when the library root and `steamapps` sit on different filesystems
- fix: the Contents line no longer parses as a one-item ordered list
- fix: the 39-line command appendix may break across PDF pages instead of leaving the page before it two-fifths empty
- docs: the first "configuring game data" pass may crash once, per the opening post of thread 702075; troubleshooting row added
- docs: Info Addict carries the modpack maintainer's memory-crash warning
- docs: §10 names the open 5.4.x multiplayer desync report, Community-Patch-DLL #13349
- docs: Proton #8327 is closed as not planned; the troubleshooting row says so
- docs: the path reference lists the added city-state sound XML
- docs: thread 702075 page 2 now runs to 2026-09-16; release, MPPatch and thread checks re-dated 2026-09-19
- docs: Sources name Protontricks 1.14.1 `cli/command.py` and `gui.py` for the two corrected messages
- test: all 24 fish blocks re-parsed and 30 run checks passed under fish 3.7.0 against a stubbed Steam tree
- test: the 9.0.2 print HTML was reproduced byte for byte and its PDF text for text before 9.0.3 was built

## 9.0.2 — 2026-09-16

- fix: Step 6's GUI and Flatpak lines rendered as one run-on paragraph in Markdown; now two paragraphs
- fix: the print HTML no longer emits empty class attributes on untagged blocks
- docs: grammar in the Steam Deck note and in the §15 sentence about the forum
- test: final line-by-line pass over all four files; 24 fish blocks re-parsed, 30 run checks re-passed, HTML and PDF rebuilt

## 9.0.1 — 2026-09-16

- fix: §7 credits the early-crash fix correctly — taskset for the Windows build under Proton; the config.ini reports are mostly native
- fix: Step 1 says what the S: link should target, with Proton's fallback when the library root is not writable
- fix: the disk-write-error row states the issue's closure date and workaround instead of assuming a client fix
- fix: the NVIDIA row no longer says "at launch"; the reports do not
- fix: "Unknown arg" applies to verbs newer than the packaged Winetricks, not to vcrun2008
- fix: the fsync lever is attributed to the forum report that used it
- fix: §3's opening sentence no longer contradicts the CachyOS-only pacman line
- fix: Step 1 no longer calls every disk write error a client bug
- docs: the Debian block names nvidia-driver-libs:i386 for the proprietary driver
- docs: Steam Deck note on the read-only root and the grep substitution for rg
- docs: Steam directory found through ~/.steam/root, as the steam-installer package documents; Debian/Ubuntu row covers older installs
- docs: the Debian/Ubuntu Steam directory is sourced from the steam-installer package itself
- docs: Sources split into three tables; the Path reference page no longer ends half empty
- docs: revision line names the Proton 11.0 source; "app ID" and "Protontricks" spelled one way in prose
- docs: 9.0.0's test note overstated the PDF check — the rebuilt 8.0.0 PDF matched text for text, not byte for byte
- test: all 24 fish blocks re-parsed and the 30 run checks re-passed; HTML and PDF rebuilt

## 9.0.0 — 2026-09-16

- feat: new §3 installs Steam and Protontricks on Ubuntu 26.04 and 24.04, Debian 13, Fedora 44 and Steam Deck
- feat: §3 gives the default Steam library for native, Debian/Ubuntu, Flatpak and Snap Steam
- feat: install vcrun2008 and corefonts into the prefix; the VP DLL imports the VC++ 2008 runtime
- feat: Flatpak Protontricks aliases let every later command run unchanged
- feat: print the exact Z:\ wizard path from $CIV5_GAME, identical in bash
- feat: find the library that holds Civ V from libraryfolders.vdf, identical in bash
- feat: early random crashes and the MaxSimultaneousThreads fix from ProtonDB reports
- feat: troubleshooting splits into setup and in-game tables with ten new rows
- feat: civ5vp-installer 0.1.6 download with sha256 and the author's local-build warning
- feat: name civ5vp-installer as a modpack builder in §10
- fix: Proton 11.0 and Experimental create S: by default; the launch option is for 10.0 and older
- fix: the shell table promised three constructs, listed four and lacked the for loop
- fix: the pacman line gains --needed and the ripgrep, rsync and zip this guide calls
- fix: "Invalid file magic number" now names the Protontricks 1.12.0 floor
- fix: civ5vp-installer also writes the game cache, per its README
- fix: four table rows ran into their right border
- fix: the PDF no longer strands a lead-in line or heading at a page foot
- docs: the forum command's WINEPREFIX and WINE_DISABLE_NICE have no effect; say so
- docs: renumber sections for the new §3 and update every § reference
- docs: thread 702075 page 2 read in full; sources and limits updated
- docs: Proton references move from proton_9.0 to proton_11.0
- docs: split Sources into mod and Linux tables so each fits a printed page
- test: all 24 fish blocks parse; 30 run checks pass under fish 3.7.0 with stubbed tools
- test: print HTML and PDF rebuilt after reproducing the 8.0.0 build byte-identically

## 8.0.0 — 2026-09-12

- feat: document the options file's Class legend and that Class 3 costs overhead unused
- feat: cover both documented crash-dump locations; the two upstream sources disagree
- feat: warn that dump generation under Proton depends on Wine's `dbghelp.dll`
- docs: frame the four highlighted toggles as a subset of the mod's full option set
- docs: record the crash-dump path conflict as unresolved rather than picking one
- test: the two-location crash-archive command runs clean with and without a dump

## 7.1.0 — 2026-09-12

- docs: state that every command block is fish, CachyOS's default login shell
- docs: add a fish-to-bash/zsh translation table for the three constructs that differ
- docs: state the fence convention — `fish` blocks are commands, untagged are reference
- fix: the backup `rsync` could not create its destination and failed outright
- fix: an unmatched `*.dmp` glob aborted the crash-archive command in fish
- fix: `$EDITOR` aborts in fish when unset; give the options file path instead
- test: all 15 fish blocks parsed and the file-touching ones executed under fish 3.7.0
- fix: PDF build mangled the new intro table; intro now shares the body's block handling

## 7.0.0 — 2026-09-12

- feat: bug reporting rebuilt from the upstream issue form; blank issues are disabled
- feat: name the three attachments the form always wants, with their exact paths
- feat: crash artifacts live in `<game install>/crashlogs` as `crashes.log` plus a `.dmp`
- feat: point at the project wiki for report guidance, Lua API and group movement
- docs: explain why the autosave settings matter — the form wants the turn-before save
- docs: troubleshooting's late-game CTD row now points at the crash artifacts

## 6.2.0 — 2026-09-12

- docs: drop the separate README; the guide is now README.md, the landing page
- fix: blank line before `</details>` so GitHub renders the collapsible block
- fix: `proton-cachyos-slr` read as a contradiction of the "not Steam Linux Runtime" warning
- fix: the Flatpak section conflated Flatpak Protontricks with Flatpak Steam
- fix: replace hardware-specific resolution and refresh-rate advice with general guidance
- docs: list the paths with `ls -d` before running the manual `rm -rf` removal
- docs: state that guide errors go to this tracker and mod bugs to the project's
- docs: name the PDF and print HTML carried alongside this file

## 6.1.0 — 2026-09-12

- fix: separate the installer's DLC folder check from its information page list
- fix: a Steam Linux Runtime entry never creates a Wine prefix at all
- fix: drop the unsourced single-thread claim about late-game turn times
- fix: attribute the Leader Scene Quality advice rather than implying a published ranking
- fix: scope the MPPatch crash report to the single forum post it comes from
- fix: `set -l` in the backup snippet breaks when pasted at an interactive prompt
- fix: `cache` and `ModUserData` do not exist at first launch
- fix: the Start Menu wizard page may be skipped
- fix: logs are erased on load, not truncated
- fix: brace notation in the path table read as shell expansion that would not work
- docs: the modpack folder must sit directly under `Assets/DLC`
- docs: attribute the savegame compatibility rule to the project's versioning scheme
- docs: realign all box-drawn tables; seven had rows one character off

## 6.0.0 — 2026-09-12

- feat: new section on stacking other mods alongside Vox Populi
- docs: condense throughout and merge the performance notes into the stability section
- docs: record that CivFanatics thread 702075 is now stickied

## 5.0.0 — 2026-09-12

- feat: document the Community Patch options file and its four useful toggles
- feat: achievements with mods via the project's own option, not binary patching
- feat: backup procedure before updating, since the installer rewrites the mod folders
- docs: `(4a) Squads for VP` is optional, not a required component
- docs: MPPatch checked and deliberately not recommended
- docs: note that Vox Populi ships beta releases alongside stable ones

## 4.0.0 — 2026-09-12

- feat: late-game crashes are 32-bit address-space exhaustion, with the project's fixes
- feat: `PROTON_FORCE_LARGE_ADDRESS_AWARE` is on by default, giving 4 GB instead of 2 GB
- feat: optional `PROTON_SET_GAME_DRIVE=1` maps `S:` and shortens the wizard browse step
- docs: the issue template asks for version and installed components
- docs: the project's developer docs misspell the config path as "Civilization V"

## 3.0.0 — 2026-09-12

- feat: `(3a) VP - EUI Compatibility Files` is mandatory on an EUI install
- feat: Workshop mod-ID conflicts and third-party DLL mods cause silently missing mods
- feat: prebuilt modpacks as the recommended multiplayer route
- feat: logging configuration keys for bug reports
- fix: selecting a Steam Linux Runtime entry breaks `protontricks-launch`
- fix: Steam's verify-integrity reverts the `Expansion2.Civ5Pkg` that Vox Populi replaces
- docs: restructure to Arch Wiki conventions with a check after every step

## 2.0.0 — 2026-09-12

- feat: rebuild from the Vox Populi Inno Setup script rather than forum reconstruction
- feat: document the two-path wizard, the second page deciding whether copying is needed
- feat: the installer blocks unless every DLC folder is present
- feat: the installer is Inno Setup, not .NET, and clears the cache itself
- feat: "Uninstall all" is a setup type; there is no uninstaller entry
- feat: multiplayer modpack procedure from the mod's own documentation
- fix: current stable is 5.4.6, not 5.4.4
- fix: Brave New World alone is insufficient

## 1.0.0 — 2026-09-12

- feat: initial guide from CivFanatics thread 702075 and the TeaDrinkingProgrammer guide
