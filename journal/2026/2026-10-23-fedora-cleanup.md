---
title: Cleaning Up a Fedora Xfce Workstation Without Breaking the Themes
date: 2026-10-23
category: Linux
tags:
  - Linux
  - Fedora
  - Xfce
  - Maintenance
  - Packages
summary: A read-only audit of my daily-driver Fedora machine, then a cleanup in small reversible batches. Leftover KDE and virtual machine pieces were switched off or removed, a file indexer was stopped from ever starting again, and my Windows-style theme switcher kept working throughout.
public: true
featured: false
---

[Back to journal index](../README.md)

## What I worked on

Cleaning up my main Fedora machine after moving through several desktops (see [Finding a Linux desktop that works for me](2026-10-12-linux-desktop.md)). I had removed the KDE Plasma desktop earlier the same day, but the machine still carried leftover apps, background services and start-up entries from KDE, GNOME and a virtual machine installer. A file indexer had also been seen using about a fifth of a CPU core while the machine was idle.

The goal was a lean Xfce development machine, not the smallest possible package list. The rule I set was that the audit comes first, nothing changes until I approve a written plan, and my Windows 98, XP and 7 theme switcher must keep working.

I used Claude to run the read-only audit and draft the plan. I approved the plan, ran every command that needed administrator rights myself, and checked the results.

## What changed

| Area | What I did |
| --- | --- |
| File indexer | Stopped LocalSearch from ever starting again. A masked service was not enough on its own, because it can also be started by the desktop's start-up list and by on-demand activation. I added an override for each, and deleted its 8 GB cache. |
| Start-up list | Hid seven start-up entries (the indexer, KDE's file indexer, a second KDE login agent, and four virtual machine or installer helpers). I used per-user override files rather than deleting package files, so each can be undone by removing one file. |
| Services | Disabled six services that only make sense inside a virtual machine or a live installer. The machine is real hardware. |
| Packages | Removed 44 packages that the package manager called unneeded (KDE games, a remote desktop client and server, a file indexer, KDE Connect and unused libraries). Marked the ones I wanted first, so they were kept. Then removed 12 duplicate apps in a second step. |
| Third-party sources | Disabled three COPR repositories that had nothing installed from them. Kept the one that provides my file manager. |
| Flatpak | Removed a GNOME extensions manager, and with it a 1 GB GNOME runtime that nothing else used. |

### Why the order mattered

1. **Audit with read-only commands only.** Everything in the plan had a measured number or a command behind it.
2. **A recovery point before any change.** Two read-only Btrfs snapshots (system and home) and the package manager's history ID.
3. **Reversible changes first.** Start-up overrides, then services, then packages.
4. **The theme switcher after every batch.** I ran all three looks and the advanced side each time.

### Marking what to keep

`dnf autoremove` listed 79 packages. Several were things I use but had never installed on purpose, such as the archive tool, sensor tools, encrypted-folder tools and the video driver. Running it blindly would have taken them. The package manager can mark a package as installed by choice, which removes it from the list. I marked about 30, re-ran the dry run, and the list dropped to 44, all of which I was happy to lose.

## Problems / debugging

- **My own mistake about the theme tools.** The plan said a certain KDE command was needed by the theme switcher. It was not installed at all, and it is only used by the KDE side of the switcher. I found this by checking after the removal rather than before. The two tools Xfce actually uses were present.
- **A check that ran too early.** My first verification showed none of my override files. I had run the commands but not yet finished, so the check proved nothing. Re-running it after the logout showed everything in place.
- **A command that killed itself.** `pkill -f` matched its own command line and ended the shell. The same lesson as the last entry, and I did not apply it.
- **An app I assumed was installed.** The music player I asked to remove was not installed anywhere. Only its 4 GB of data remained.
- **Two checks that disagreed.** A first look at default apps suggested one set of programs, but my recently-used list showed a different one. I trusted the usage evidence over the defaults, which is why Spectacle stayed.

## Decisions

- **Overrides over deletion.** A start-up entry is hidden by adding a user file, not by editing the package.
- **Ask about each app, don't guess.** Removing a duplicate only made sense once I had decided which one I actually use. I gave a yes or no for each, using the history and recently-used evidence.
- **Don't claim the cleanup fixed the idle restarts.** The machine sometimes restarts while idle. That looks like a hardware or firmware issue, so I treated it as separate and changed no power, kernel or BIOS settings.
- **Keep the snapshots for now.** They hold the deleted data until I delete them, so disk usage has not dropped yet.

## What I learned

- A fair before-and-after needs the same apps open. My baseline had a chat app open that I later closed, and that alone changed the temperature and CPU figures. After reopening it, idle CPU and load were still lower, but memory was higher, and I would not credit the cleanup with the full difference. The things I can prove are that the indexer cannot start, the cache is gone and the package count fell.
- "Unneeded" in a package manager means "nothing I installed on purpose needs this". It does not mean "I don't use it".
- Disabling a service has a quieter twin: the other ways it can start. Check the start-up list and on-demand activation as well as the service itself.

## Next steps

- Delete the two snapshots once the system has run for a few days without trouble.
- Review the unused container images, the three installed kernels and a handful of remaining services (crash reporting, modem manager, network discovery).
- Check whether the indexer stays quiet over a few days of normal use. A single quiet hour proves little.
- Investigate the idle restarts separately, starting with the logs rather than guessing.
