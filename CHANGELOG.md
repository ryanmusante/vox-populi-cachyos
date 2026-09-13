# Changelog

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
