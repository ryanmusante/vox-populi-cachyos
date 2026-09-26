# Changelog

## 10.4.1 — 2026-09-25

- fix: no wine package behind Protontricks, corefonts is eleven fonts, civ5vp-installer size stated, a repeating first-pass crash clears cache

## 10.4.0 — 2026-09-24

- docs: §16 cut to paths stated nowhere else, Sources carry one verification date, §8 loses its view counts, Fedora's Protontricks is 1.13.1 per Bodhi, the print HTML leaves the archive

## 10.3.3 — 2026-09-24

- fix: checked against the 5.4.6 source tree — the installer's full cleanup list, the blocked mods from the modinfo files, the DLC message naming the missing packs, the bug form's required fields; Squads' unverified Ctrl+number binding dropped

## 10.3.2 — 2026-09-24

- docs: wording and path-style consistency pass; the GPU Texture Decode row, which recommended nothing, is dropped

## 10.3.1 — 2026-09-24

- fix: build tool hardened — WeasyPrint API instead of a subprocess, atomic writes, input validation, documented functions; the guide is unchanged

## 10.3.0 — 2026-09-24

- feat: §7 gains "Configuration files and performance-first settings" — the four .ini files, the key behind each Video option with a performance-first value, and the file-only fixes

## 10.2.0 — 2026-09-24

- feat: §8 gains "Popular mods from CivFanatics" — where the Mods Repository is, the ten most-viewed mods, and the five-step install of a downloaded mod

## 10.1.0 — 2026-09-24

- feat: game language must be English; launch from the library window for the DirectX choice; zenity required beside yad; Flatpak folder scope and desktop entries corrected; #13344 and #13372 noted

## 10.0.0 — 2026-09-24

- feat: no terminal — Steam, the Protontricks and Winetricks windows, the wizard and a file manager do it all; three named folders replace the shell variables; 18 sections

## 9.0.6 — 2026-09-22

- fix: §14 gets its own digest variable; §9.2 check; US spelling; label and table cleanups

## 9.0.5 — 2026-09-19

- docs: prose tightened and the changelog condensed; no command, path or procedure changed

## 9.0.4 — 2026-09-19

- fix: downloads verified with `sha256sum -c` and fetched with `curl -fL`; `rm -rf` blocks print `STOP` when the variables are unset; Step 1 and Step 8 checks

## 9.0.3 — 2026-09-19

- fix: Protontricks messages quoted correctly; the wizard's DLC gate and Step 8 pass/fail test; `S:` fallback; first-pass crash, Info Addict and desync #13349 noted

## 9.0.2 — 2026-09-16

- fix: Step 6 renders as two paragraphs; the print HTML drops empty class attributes

## 9.0.1 — 2026-09-16

- fix: early-crash fix credited to `taskset`; `S:` target stated; Debian NVIDIA 32-bit libraries; Sources in three tables

## 9.0.0 — 2026-09-16

- feat: §3 Other distributions (Ubuntu 26.04/24.04, Debian 13, Fedora 44, Steam Deck); `vcrun2008` and `corefonts`; early random crashes and the thread-count fix; civ5vp-installer 0.1.6

## 8.0.0 — 2026-09-12

- feat: options-file Class legend; both crash-dump locations; dumps under Proton depend on Wine's `dbghelp.dll`

## 7.1.0 — 2026-09-12

- docs: every command block is fish, with a fish-to-bash/zsh table; the `rsync`, `*.dmp` and `$EDITOR` snippets fixed for fish

## 7.0.0 — 2026-09-12

- feat: bug reporting rebuilt from the upstream issue form: three attachments, their paths, why autosaves matter

## 6.2.0 — 2026-09-12

- docs: the guide becomes README.md; `proton-cachyos-slr` told apart from bare Steam Linux Runtime; hardware-specific advice made general

## 6.1.0 — 2026-09-12

- fix: DLC check separated from the information page; Steam Linux Runtime creates no prefix; logs are erased on load

## 6.0.0 — 2026-09-12

- feat: adding other mods; performance notes merged into the stability section

## 5.0.0 — 2026-09-12

- feat: Community Patch options file and four toggles; achievements through VP's own option; backup before updating

## 4.0.0 — 2026-09-12

- feat: late-game crashes are 32-bit address-space exhaustion; `PROTON_FORCE_LARGE_ADDRESS_AWARE` on by default; optional `S:` drive

## 3.0.0 — 2026-09-12

- feat: `(3a)` mandatory with EUI; prebuilt modpacks for multiplayer; logging keys; Steam's verify reverts `Expansion2.Civ5Pkg`

## 2.0.0 — 2026-09-12

- feat: rebuilt from the Inno Setup script: two-path wizard, DLC gate, self-cleaning installer, Uninstall all; stable is 5.4.6

## 1.0.0 — 2026-09-12

- feat: initial guide from CivFanatics thread 702075 and the TeaDrinkingProgrammer guide
