# Changelog

## 2026.10.22

### Power menu key calls kiro-powermenu

**What Changed.** Super+Shift+X now runs `kiro-powermenu` instead of the old name `edu-powermenu`, and the keybindings cheatsheet says so. The polybar variant's `config-polybar` still called `arcolinux-powermenu`, which no package provides any more, so Super+Shift+X did nothing there; it now runs `kiro-powermenu` too. The package now depends on `kiro-powermenu`, so the menu is always installed with this desktop.

**Technical Details.** Plain rename of the command. `kiro-powermenu` still ships `edu-powermenu` as a second command, so configs users already have in `~/.config` keep working.

**Files Modified.** `etc/skel/.config/i3/keybindings.txt`, `etc/skel/.config/i3/config-polybar`, `etc/skel/.config/i3/config`

## 2026.10.19

### What Changed
- Added the new ohmychadwm keybindings (added to ohmychadwm today) so they work the same here:
  - Super+Ctrl+L locks the screen
  - Super+Ctrl+A opens pavucontrol
  - Super+Ctrl+Shift+A opens an AI agent (`alacritty -e claude`); Claude Code is not part of Kiro, so without it the terminal closes straight away
  - Super+Shift+F opens thunar
  - Super+Shift+B opens vivaldi
  - Super+Shift+N opens code
  - Super+Shift+M opens spotify
  - XF86AudioMicMute mutes the microphone
  - Alt+volume keys change the volume by 1%
- Left out because the key already does something else here: Super+K (focus down (`$down` is k)).

### Technical Details
- Propagated with `kiro-update-keybindings.py`, then checked by hand: the engine does not expand sxhkd `{h,j,k,l}` braces, i3 `$down`-style variables, or the WM-native configs, so it proposed some keys that were already taken.
- Super+W (close) and Super+Up/Down (focus) were not ported; they are window-manager actions, native per WM.
- `ctrl+alt+x` → `oma` was not ported: oma is ohmychadwm Appearance and only works there.
- The engine wanted to overwrite Super+Shift+B (`border toggle`) with vivaldi; kept border toggle.
- Added the new keys to the cheatsheet and bumped its Generated date.

### Files Modified
- `etc/skel/.config/i3/config`
- `etc/skel/.config/i3/keybindings.txt`

## 2026.10.18

### What Changed
- Hid the "i3 (with debug log)" entry on the SDDM login screen. i3-wm ships `i3-with-shmlog.desktop` next to `i3.desktop`; it runs the same i3 plus an in-memory debug log that only matters when filing an i3 bug report, so it looked like a duplicate session.

### Technical Details
- New `/usr/bin/kiro-i3-hide-sessions` appends `NoDisplay=true` (idempotent) — hidden, not removed, same as kiro-qtile and kiro-xfce. A pacman hook re-runs it whenever i3-wm installs or upgrades the file; `post_install`/`post_upgrade` run it too, because the hook does not fire when kiro-i3 is installed on a box that already has i3-wm.
- The PKGBUILD now also copies `usr/` into the package.

### Files Modified
- `usr/bin/kiro-i3-hide-sessions` (new)
- `usr/share/libalpm/hooks/kiro-i3-hide-sessions.hook` (new)
- `KIRO-PKG-BUILD-APPS/kiro-i3/PKGBUILD`, `readme.install`

## 2026.09.27

### What Changed
- Reworded the `keybindings.txt` header: dropped "DO NOT EDIT BY HAND" and the generator name, added a line telling users it lists the default bindings and they can edit it to match their own. A user changed a binding, expected the file to update itself, and went looking for a generator that isn't part of Kiro.

### Technical Details
- `Generated:` now carries only the date, so the kiro-keybindings HTML/PDF footer (which prints everything after `Generated:`) shows a clean date. The new line is a `#` comment, which both kiro-keybindings parsers skip. Bindings unchanged.

### Files Modified
- `etc/skel/.config/i3/keybindings.txt`

## 2026.09.11

### What Changed
- Fixed the i3 startup error banner "You have an error in your i3 config file! / Duplicate keybinding". Three keys were bound twice: `Super + e`, `Super + t` and `Super + v` each carried both an old i3 tiling command and the app-launcher propagated from ohmychadwm (June 2026). i3 silently let the last binding win, so the launchers worked and the tiling commands were dead — but i3 raised the error nag on every login.
- The launchers keep the plain keys (master wins for apps); the three tiling commands moved to Shift variants so the functionality is back instead of being dropped.

### Technical Details
- `Super + v` (split v) → `Super + Shift + v`, `Super + e` (layout toggle split) → `Super + Shift + s`, `Super + t` (border normal) → `Super + Shift + t`. All three targets were unbound; a full duplicate sweep over `bindsym` lines now comes back clean.
- `keybindings.txt` gained the three entries in section 4 (Layout & Tiling) — they had been dropped from the cheatsheet when the launchers took over the keys.

### Files Modified
- `etc/skel/.config/i3/config`
- `etc/skel/.config/i3/keybindings.txt`

## 2026.06.30

### What Changed
- Removed the `update-system` keybinding (`Ctrl + Alt + Shift + F1` and `Super + Ctrl + Shift + F1`). `update-system` is a personal maintenance script that had slipped into ohmychadwm and propagated across every Kiro environment; it is not part of the shipped desktop, so the binding is gone. The cheatsheet `keybindings.txt` was updated to match.
- Deleted the generated `keybindings.html` / `keybindings.pdf` cheatsheets — these will be regenerated by a different process.
- **Grouped the `super + F1`–`F12` app-launchers into one contiguous ascending block** in the cheatsheet (they were scattered among the other launchers), per the new ordering rule in `/kiro-create-keybindings`.
- **Guardrail against shipping generated cheatsheets:** added a `.gitignore` rule for `keybindings.html`/`keybindings.pdf` plus an `up.sh` commit-guard that aborts the push if either is ever tracked — they are generated on demand by the kiro-keybindings app, never committed.

### Technical Details
- Removed the two `# update-system` comment + `bindsym` pairs from `i3/config`.

### Files Modified
- `etc/skel/.config/i3/config`
- `etc/skel/.config/i3/keybindings.txt`
- `.gitignore`
- `up.sh`

### Files Removed
- `etc/skel/.config/i3/keybindings.html`
- `etc/skel/.config/i3/keybindings.pdf`

## 2026.06.08

### What Changed
- Rebound `Super + F9` from `lollypop` to `virt-manager`, matching the distro-wide change applied across all Kiro environments.

### Technical Details
- Edited the `bindsym $mod+F9` line in `i3/config` (comment + command) and regenerated the cheatsheet (`keybindings.txt` → `keybindings.html` + `keybindings.pdf` via `kiro-keybindings-html.py`).

### Files Modified
- `etc/skel/.config/i3/config`
- `etc/skel/.config/i3/keybindings.txt`
- `etc/skel/.config/i3/keybindings.html`
- `etc/skel/.config/i3/keybindings.pdf`

## 2026.06.02

### What Changed
- Cleaned the autostart/monitor regions of `etc/skel/.config/i3/config` per the canonical [TWM autostart standard](/home/erik/Insync/Kiro/Kiro-HQ/AUTOSTART_TEMPLATE.md) (native WMs get the light touch — structure left intact). No app behavior changed.
- Removed the dead `# exec --no-startup-id xrandr …` hardware preset wall under the monitor section (per-machine xrandr lives in `~/.screenlayout/<username>.sh`, which the canonical block already sources).
- Fixed a redundant double-`feh`: the config set `archlinux/arch-wallpaper.jpg` and then immediately overrode it with `kiro/kiro-wallpaper.jpg` — dropped the dead first line.
- Removed the broken cross-distro fallback `# feh … /usr/share/archlinux-tweak-tool/data/wallpaper/wallpaper.png` — that file does not exist (the real ATT file is `wallpaper.jpg`), so the hint was wrong as written.

### Files Modified
- etc/skel/.config/i3/config

## 2026.06.01

### What Changed
- Added a native i3 keybinding `super + ctrl + s` that launches **kiro-keybindings**, the new searchable Qt6/PySide6 keybindings cheatsheet (a cross-desktop Kiro feature). `super + ctrl + s` is the universal cheatsheet hotkey across all Kiro tiling window managers ("S" = Shortcuts; AZERTY-safe).

### Technical Details
- `config`: added `bindsym $mod+Ctrl+s exec --no-startup-id kiro-keybindings` near the rofi launcher binding. i3 has no sxhkd, so the binding is native.
- `keybindings.txt` fully regenerated by `/kiro-keybindings-all-twms`; the cheatsheet opener now appears under Applications & Launchers.

### Files Modified
- etc/skel/.config/i3/config
- etc/skel/.config/i3/keybindings.txt

## 2026.05.26

### What Changed
- Replaced picom with **fastcompmgr** as the compositor. Boot launch and the toggle now use fastcompmgr, and the compositor-toggle keybind moved to the unified `super + g` (was `ctrl + alt + o`).

### Technical Details
- `config` and `config-polybar`: `exec_always … picom --config ~/.config/i3/picom.conf` → `exec_always … fastcompmgr -c`; toggle `bindsym` changed from `control+mod1+o` to `$mod+g`.
- `scripts/picom-toggle.sh` renamed to `fastcompmgr-toggle.sh` (simple on/off toggle — fastcompmgr takes no config file).
- Deleted the now-unused `picom.conf`.

### Files Modified
- etc/skel/.config/i3/config
- etc/skel/.config/i3/config-polybar
- etc/skel/.config/i3/scripts/fastcompmgr-toggle.sh (created, replaces picom-toggle.sh)
- etc/skel/.config/i3/scripts/picom-toggle.sh (deleted)
- etc/skel/.config/i3/picom.conf (deleted)

## 2026.05.21

### What Changed
- Initial markdown scaffold added per the ecosystem MD-scaffold rule ([HQ/CLAUDE.md](/home/erik/Insync/Kiro/Kiro-HQ/CLAUDE.md#required-markdown-scaffold-every-repo)).
- Stubs created for `CHANGELOG.md`, `CLAUDE.md`, `IDEAS.md`, `TODO.md` (whichever were missing).
- README rewritten with real install/usage content (replaced earlier one-line stub) where applicable.

### Files Modified
- CHANGELOG.md (created)
- CLAUDE.md (created where missing)
- IDEAS.md (created where missing)
- TODO.md (created where missing)
- README.md (rewritten where it was a stub)
