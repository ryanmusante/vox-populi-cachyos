# Changelog

## 9.0.6 — 2026-09-22

- fix: §14 uses `CIV5VP_SUM`, leaving Step 5's `CIV5_SUM` intact; §9.2 gains a check; §16 lists Step 3's `ls "$CIV5_DOCS"`
- docs: bash uses the Protontricks README's `alias` lines, not `alias --save`; §2 names the `rm -rf` blocks that print `STOP`; US spelling
- docs: backticks out of the §2 table; CTD defined at first use; `Ctrl` and `[Wizard]` labels consistent; the print HTML ends in a newline

## 9.0.5 — 2026-09-19

- docs: prose tightened throughout and this changelog condensed; no command, path or procedure changed
- docs: §10 no longer repeats §14's note on packs built before civ5vp-installer 0.1.6; "game-core" spelled one way

## 9.0.4 — 2026-09-19

- fix: downloads verified with `sha256sum -c` and fetched with `curl -fL`; §14 starts the installer in a second block, after the check
- fix: the §8 and §12 `rm -rf` blocks print `STOP` when the §2 variables are unset; §2 says they last one terminal session
- fix: Step 1 gains a check and Step 8's is labeled; the two wizard steps are checked by Step 8
- fix: `MinorCivSounds_VoxPopuli.xml` in full in §1; Flatpak override argument quoted; continuation lines use four spaces
- docs: ripgrep ships with CachyOS; Sources name Community-Patch-DLL #13364 and the CachyOS installer package list

## 9.0.3 — 2026-09-19

- fix: Protontricks messages quoted correctly: "Proton installation could not be found!" and the Flatpak directory-access text
- fix: the wizard blocks a folder without `Assets\DLC` and all ten DLC folders; Step 8's second command is the pass/fail test
- fix: manual uninstall list gains the added `MinorCivSounds_VoxPopuli.xml`; `(3a)` and `(3b)` mod names in full
- fix: `S:` fallback covers a library root on another filesystem; the PDF's command appendix may break across pages
- docs: first configure pass may crash once; Info Addict memory warning; open 5.4.x desync #13349; Proton #8327 closed

## 9.0.2 — 2026-09-16

- fix: Step 6's GUI and Flatpak lines render as two paragraphs; the print HTML drops empty class attributes

## 9.0.1 — 2026-09-16

- fix: early-crash fix credited to `taskset` under Proton; `S:` target stated; disk-write-error and NVIDIA rows match their sources
- docs: Debian NVIDIA 32-bit libraries; Steam Deck read-only root; Steam directory via `~/.steam/root`; Sources in three tables

## 9.0.0 — 2026-09-16

- feat: §3 covers Ubuntu 26.04 and 24.04, Debian 13, Fedora 44 and Steam Deck, with each packaging's default Steam library
- feat: Step 4 installs `vcrun2008` and `corefonts`; Flatpak aliases; commands print the `Z:\` wizard path and find the library
- feat: early random crashes and the thread-count fix; troubleshooting split in two tables; civ5vp-installer 0.1.6 with sha256
- fix: Proton 11.0 and Experimental create `S:` by default; "Invalid file magic number" names the Protontricks 1.12.0 floor

## 8.0.0 — 2026-09-12

- feat: options-file Class legend; both documented crash-dump locations; dumps under Proton depend on Wine's `dbghelp.dll`

## 7.1.0 — 2026-09-12

- docs: every command block is fish, with a fish-to-bash/zsh table and the fence convention
- fix: the backup `rsync`, the `*.dmp` glob and an unset `$EDITOR` no longer break their snippets under fish

## 7.0.0 — 2026-09-12

- feat: bug reporting rebuilt from the upstream issue form: three attachments, their paths, and why autosaves matter

## 6.2.0 — 2026-09-12

- docs: the guide becomes README.md, the landing page
- fix: `</details>` renders on GitHub; `proton-cachyos-slr` told apart from bare Steam Linux Runtime; hardware-specific advice made general

## 6.1.0 — 2026-09-12

- fix: DLC folder check separated from the information page list; Steam Linux Runtime creates no prefix; logs are erased on load
- docs: the modpack folder sits directly under `Assets/DLC`; box-drawn tables realigned

## 6.0.0 — 2026-09-12

- feat: section on adding other mods; performance notes merged into the stability section

## 5.0.0 — 2026-09-12

- feat: Community Patch options file and four toggles; achievements through the project's own option; backup before updating
- docs: `(4a) Squads for VP` is optional; MPPatch not recommended; VP ships betas alongside stable releases

## 4.0.0 — 2026-09-12

- feat: late-game crashes are 32-bit address-space exhaustion; `PROTON_FORCE_LARGE_ADDRESS_AWARE` is on by default; optional `S:` drive

## 3.0.0 — 2026-09-12

- feat: `(3a) VP - EUI Compatibility Files` mandatory with EUI; prebuilt modpacks for multiplayer; logging keys for bug reports
- fix: Steam Linux Runtime entries break `protontricks-launch`; Steam's verify reverts the replaced `Expansion2.Civ5Pkg`

## 2.0.0 — 2026-09-12

- feat: rebuilt from the Inno Setup script: two-path wizard, DLC gate, self-cleaning installer, "Uninstall all" setup type
- fix: current stable is 5.4.6, not 5.4.4; Brave New World alone is insufficient

## 1.0.0 — 2026-09-12

- feat: initial guide from CivFanatics thread 702075 and the TeaDrinkingProgrammer guide
