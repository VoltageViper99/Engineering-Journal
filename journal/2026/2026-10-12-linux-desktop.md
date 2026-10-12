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
summary: Why my daily-driver machine has moved from Manjaro with Hyprland, to Fedora with GNOME, then KDE Plasma and now Xfce, and what each move taught me about working in a way that suits me, including why I like simple, classic interfaces.
public: true
featured: false
---

[Back to journal index](../README.md)

## What I worked on

Writing down the desktop changes on my daily-driver machine. It has gone through four setups in under a year, and I am not embarrassed about that. Choosing tools by trying them is how I learn, and each change taught me something about how I want to work. This covers the major moves, not every setting.

## What changed

| Setup | How it went |
| --- | --- |
| Manjaro (Arch-based) with the Hyprland tiling window manager | A fully keyboard-driven setup that I enjoyed. It ended because the system kept breaking, not because of Hyprland. |
| Fedora with GNOME | The first stop after leaving Arch. It fixed the stability problem but did not suit me. |
| Fedora with KDE Plasma | Fedora's stability underneath, Plasma's flexibility on top. Too heavy for me, and too much effort to theme. |
| Fedora with Xfce | The current setup: a lightweight, traditional desktop with a classic start menu, styled as classic Windows 98. |

### Away from Arch

Arch-based systems follow the newest software closely. That is great for learning and for getting new features, but updates occasionally broke the system, and I had a business to run from this machine. Fedora is current enough for development work without that habit.

### Away from Hyprland

I loved Hyprland. Everything was a keybinding and the setup was code I could read and version. But it turned into a second job: I spent more hours tweaking the window manager than working in it. It also had no system tray, so I added a separate bar just to get one. KDE Plasma now provides the panel, tray, notifications and compositor in one maintained package, so there is much less of my own configuration to look after.

### Away from GNOME

GNOME was tidy but too opinionated. Making the desktop my own meant fighting extensions and its own ideas of how I should work. Plasma lets me change nearly everything through its own settings.

### Away from KDE, to Xfce

KDE Plasma was the setup I settled on in September, and it is a good desktop. But it was too heavy for me, and getting it to look and feel like Windows 98 took more effort than it should have. The settings are a good example. KDE gives you a huge number of options, which is great if you want them, but I do not. Xfce has a classic start menu, so finding an application is simple, and its settings are plain and easy to find. It is lightweight and it just works.

The move was to Xfce on the same Fedora base, so nothing changes underneath: the same Fedora and dnf knowledge applies.

### The look, and why

I follow a classic Windows 98 look and feel, flat and square with no blur or animation, and I carry it into my applications as well as the desktop, so everything looks and behaves the same. I prefer it because it is simple and easy to use. I am not a fan of fancy, cluttered interfaces. Controls are where you expect them, nothing moves for the sake of it, and a screen shows what you need and no more.

### Why this matters beyond my desktop

My philosophy is that software should be easy for people who are not into computers as much as I am. The clearest example is [Supporter Invoicing](../../projects/supporter-invoicing.md). I designed it for my mum, who is not a technical user. She has had no problems using it, loves it and, in her words, could not go back to doing invoices manually. I use the same test for everything I build or set up: if a non-technical person cannot use it without help, the design is wrong.

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
- **Pick the desktop that already looks and works the way I want**, instead of bending a powerful one into shape. Theming KDE to look like Windows 98 cost more effort than choosing Xfce.
- **Fewer pieces to maintain.** One integrated desktop beats a hand-assembled stack when the stack's upkeep competes with real work.
- **Simple over fancy, in my desktop and in my software.** I choose plain, consistent interfaces, and I build for the least technical person who will use the tool.
- **Keyboard-first where it helps, mouse where it is easier.** I no longer try to do everything from the keyboard.
- **Try things, but write down the result.** I keep a private system manual and keep it honest by checking it against the live machine.

## What I learned

- Simple is a feature. The best test of an interface is whether someone who does not enjoy computers can use it unaided.
- Customisation has a running cost. A setup that needs constant tweaking is a hobby, not a work tool, and I should be honest about which one I want.
- Admin skills carry over. Moving from pacman to dnf, and from a hand-built desktop to Plasma, was easy because the underlying Linux knowledge (systemd, packages, config files, Wayland basics) is the same.
- Notes that are not checked against the machine drift, and a stale note is worse than none.
- Switching away from something I liked, because it was not working for me, was the right call. The point is finding what works, not defending a past choice.

## Next steps

- Keep the system manual in step with the machine after each change.
- Stay on Fedora with Xfce for now, and revisit only if something stops working.
