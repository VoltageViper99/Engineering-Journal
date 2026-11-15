---
title: An empty icon list, and rebuilding a Fedora package properly
date: 2026-11-15
category: Linux
tags:
  - Linux
  - Fedora
  - LXQt
  - Qt
  - RPM
  - Debugging
summary: The icon theme picker in LXQt was empty after the Qt 6.11 update, traced it to an upstream fix that only exists if the package is rebuilt against the new Qt, rebuilt lxqt-config locally, and set up the window themes on the way through.
public: true
featured: false
---

[Back to journal index](../README.md)

> [!NOTE]
> **In short:** The icon theme picker in the LXQt settings tool showed nothing at all, even though the
> themes were installed and working. The cause was a fix upstream had already written, but it only takes
> effect if the program is built against the newer version of the toolkit, and Fedora's package was built
> before that version arrived. I rebuilt the package myself against the current version, confirmed the
> list came back, and set up the window theme at the same time.

## What I worked on

Theming the new LXQt desktop, which turned out to be four separate systems rather than one setting:

| What it changes | Where it lives |
| --- | --- |
| Icon themes | `/usr/share/icons` and `~/.local/share/icons` |
| LXQt theme (panel and its apps) | `~/.config/lxqt/lxqt.conf` |
| Qt widget style | the Qt style plugin |
| Window decorations | the compositor, labwc |

The first three are in the LXQt appearance tool. The fourth is not, because on Wayland the window borders
are drawn by the compositor, and here that is labwc reading Openbox themes. That split is why "change the
theme" has no single answer on this desktop.

## What was actually wrong

The Icons Theme page in `lxqt-config-appearance` was completely empty. No rows, no preview icons, just the
"Colorize icons based on widget style" checkbox at the bottom of the page.

The themes were installed and fine. About 40 of them in `/usr/share/icons` with a valid `index.theme`,
Papirus, the Mint sets, Breeze, plus a few I had downloaded. The desktop also kept working, it just had no
way to change icon set, so the failure looked cosmetic and was easy to ignore.

The cause is in `iconthemeinfo.cpp`. It does a `QSettings` status check on each `index.theme` and returns
early on any error. Qt 6.11 made the INI parser stricter, so it now reports `FormatError` for basically
every real world theme file even though all the values still parse. Every theme gets thrown away.

The confirmation was counting them both ways with the same validation the tool uses:

| Validation | Themes found |
| --- | --- |
| skip the status check (what upstream does now) | 55 |
| keep the status check (what the package does) | 0 |

Upstream already fixed this in `90edc2b5`, with a guard that skips the check on Qt 6.11 and newer. That
commit is in the 2.4.0 source. The problem is the guard is a **compile-time** check, so it only makes it
into the binary if the package was built against Qt 6.11 headers. Fedora built lxqt-config on 27 April
2026 against Qt 6.10, Qt 6.11.1 arrived on 12 May, and the guard compiled straight back out.

You can see which headers a binary was built against:

```bash
objdump -p /usr/bin/lxqt-config-appearance | grep -oE 'Qt_6\.[0-9]+' | sort -u
```

The shipped one said `Qt_6.10`. The source had the fix. The binary did not.

## Rebuilding the package

The fix is a rebuild, not a patch, so I did it locally rather than waiting:

```bash
sudo dnf install -y rpmdevtools
rpmdev-setuptree
sudo dnf config-manager setopt fedora-source.enabled=1 updates-source.enabled=1
dnf download --source lxqt-config --destdir ~/rpmbuild/SRPMS
sudo dnf builddep -y ~/rpmbuild/SRPMS/lxqt-config-2.4.0-1.fc44.src.rpm
rpmbuild --rebuild --define 'dist .fc44.local1' ~/rpmbuild/SRPMS/lxqt-config-2.4.0-1.fc44.src.rpm
sudo dnf install -y ~/rpmbuild/RPMS/x86_64/lxqt-config-2.4.0-1.fc44.local1.x86_64.rpm
sudo dnf versionlock add lxqt-config lxqt-config-l10n
```

The `.fc44.local1` release tag matters. Plain `.fc44` is the same version as the installed one, so dnf
would not see it as an upgrade, and the version lock stops a later Fedora update quietly replacing the
build with the old one.

The build failed the first time, and not because of anything I was chasing:

```
lxqt-config-monitor/kscreenutils.cpp:29:25: error: invalid use of incomplete type 'class KScreen::Screen'
```

Same rebuild, different problem. libkscreen 6.8 moved `KScreen::Screen` out of `types.h`, which now only
forward-declares it, so two files need `#include <KScreen/Screen>`. Upstream `8efdae2` is exactly that,
two lines. I kept it as a patch file and declared it as `Patch100` in the spec, so `%autosetup` applies it
on every build.

After that the build went through, and the rebuilt binary reported `Qt_6.11` and listed 55 themes.

## The window theme

Window decorations are labwc, so the setting is `<theme><name>` in `~/.config/lxqt/labwc/rc.xml`. Not
`~/.config/labwc`, because the LXQt session starts labwc with `-C` pointing at the LXQt config directory,
which is the kind of detail that costs an hour if you miss it.

There is a GUI: `labwc-tweaks`, packaged in Fedora, and it covers the theme, icon theme, shadows, corner
radius and titlebar settings. It reads the same `-C` directory through `LABWC_CONFIG_DIR`, so without that
variable it edits a config file that nothing reads. Set once in `~/.config/lxqt/labwc/environment` and the
menu entry works from then on.

Reload without logging out:

```bash
labwc -r
```

## What I learned

- **Compile-time version guards do nothing for packages built before the version.** A fix can be in the
  tarball, in the upstream changelog, in the release notes, and still be absent from the binary on disk.
  `objdump` on the binary settles it faster than any amount of reading.
- **Count things both ways.** "0 themes" against "55 themes" is a far better diagnosis than "the list is
  empty", because it isolates the check that throws them away instead of describing the symptom again.
- **A rebuild can fail for unrelated reasons.** The first `FAILED` line is the one to read, not the last,
  and it is worth checking whether the file is even in the area you care about.
- **Check which config file the program actually reads.** `-C`, `LABWC_CONFIG_DIR` and friends mean the
  path in the documentation is not always the path in use.
- **A bug report with separate fields writes itself** once the diagnosis is done. Details, steps, actual,
  expected and additional information, with the diagnosis and the version evidence kept out of the
  user-facing boxes.

## Loose ends

The report is written up for Fedora's Bugzilla, with the two upstream commits, the build date and the
symbol version evidence, so a maintainer can rebuild rather than re-diagnose. When Fedora eventually
rebuilds lxqt-config against Qt 6.11, the version lock has to come off or the local build will sit there
forever.
