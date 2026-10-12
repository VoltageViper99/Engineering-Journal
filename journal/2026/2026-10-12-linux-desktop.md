---
title: Finding a Linux Desktop That Works for Me
date: 2026-10-12
category: Linux
tags:
  - Linux
  - Fedora
  - KDE
  - Arch
  - Workflow
summary: Why my daily-driver machine has moved from Manjaro with Hyprland, to Fedora with GNOME, to Fedora with KDE Plasma in under a year, and what each move taught me about working in a way that suits me.
public: true
featured: false
---

[Back to journal index](../README.md)

## What I worked on

Writing down the desktop changes on my daily-driver machine. It has gone through three setups in under a year, and I am not embarrassed about that. Choosing tools by trying them is how I learn, and each change taught me something about how I want to work. This covers the major moves, not every setting.

## What changed

| Setup | How it went |
| --- | --- |
| Manjaro (Arch-based) with the Hyprland tiling window manager | A fully keyboard-driven setup that I enjoyed. It ended because the system kept breaking, not because of Hyprland. |
| Fedora with GNOME | The first stop after leaving Arch. It fixed the stability problem but did not suit me. |
| Fedora with KDE Plasma | Where I have settled for now: Fedora's stability underneath, Plasma's flexibility on top. |

### Away from Arch

Arch-based systems follow the newest software closely. That is great for learning and for getting new features, but updates occasionally broke the system, and I had a business to run from this machine. Fedora is current enough for development work without that habit.

### Away from Hyprland

I loved Hyprland. Everything was a keybinding and the setup was code I could read and version. But it turned into a second job: I spent more hours tweaking the window manager than working in it. It also had no system tray, so I added a separate bar just to get one. KDE Plasma now provides the panel, tray, notifications and compositor in one maintained package, so there is much less of my own configuration to look after.

### Away from GNOME

GNOME was tidy but too opinionated. Making the desktop my own meant fighting extensions and its own ideas of how I should work. Plasma lets me change nearly everything through its own settings.

### The look

I run a flat, square, Windows 98-style theme, with blur and animation turned off. There is no grand reason. I like things simple that just work, and a plain, predictable desktop gets out of the way.

### The tools around the desktop changed too

- **Browser:** I tried a keyboard-only browser. The deal-breaker was no extension support, so I had no ad blocker and no password-manager autofill. Firefox came back.
- **Mail:** I ran a terminal mail stack (a mail client, a sync tool, search and a calendar tool, with credentials in an encrypted store). I picked it for stability, fast search and a keyboard-only workflow. But it needed ongoing care, such as a sync timer that could not unlock credentials until I logged in. Thunderbird replaced it, with its own calendar.
- **Other apps:** a PDF viewer and file manager were swapped for ones that fit better. The terminal (kitty) survived every move, because it does not depend on the desktop.

## Problems / debugging

- **Documentation went stale.** After the move to Fedora, my notes still described the old setup, and the package list was still a list from Arch's package manager, useless on a dnf system. I audited every page against the live machine, rewrote them to match, regenerated the package list and added a rebuild order.
- **A silent default.** After removing an app, the system's default PDF viewer still pointed at it, and nothing complained. The rebuild notes now say to set the default first.
- **Theme-hopping leftovers.** Trying many themes left behind caches and unused packages that I had to tidy up.

## Decisions

- **Stability over the newest software** for the machine I earn a living on.
- **Fewer pieces to maintain.** One integrated desktop beats a hand-assembled stack when the stack's upkeep competes with real work.
- **Keyboard-first where it helps, mouse where it is easier.** I no longer try to do everything from the keyboard.
- **Try things, but write down the result.** I keep a private system manual and keep it honest by checking it against the live machine.

## What I learned

- Customisation has a running cost. A setup that needs constant tweaking is a hobby, not a work tool, and I should be honest about which one I want.
- Admin skills carry over. Moving from pacman to dnf, and from a hand-built desktop to Plasma, was easy because the underlying Linux knowledge (systemd, packages, config files, Wayland basics) is the same.
- Notes that are not checked against the machine drift, and a stale note is worse than none.
- Switching away from something I liked, because it was not working for me, was the right call. The point is finding what works, not defending a past choice.

## Next steps

- Keep the system manual in step with the machine after each change.
- Stay on Fedora and KDE for now, and revisit only if something stops working.
