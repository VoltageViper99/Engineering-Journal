---
title: Windows XP and 7 Themes, and a Start Menu Built from Scratch
date: 2026-10-17
category: Linux
tags:
  - Linux
  - Xfce
  - Theming
  - Python
  - GTK
summary: Adding switchable Windows XP and Windows 7 looks next to my Windows 98 setup, keeping the themes in one repo so they can be reapplied to a fresh machine, and writing a Windows 7 style Start menu in Python because the existing one could not be restyled far enough.
public: true
featured: false
---

[Back to journal index](../README.md)

## What I worked on

Extending the classic Windows look on my Xfce desktop (see [Finding a Linux desktop that works for me](2026-10-12-linux-desktop.md)). The Windows 98 setup stays, and I added Windows XP (Luna) and Windows 7 (Aero) looks that I can switch between with one command. The goal is that the machine looks and feels like Windows, so it is familiar and plain to use.

## What changed

| Piece | What I did |
| --- | --- |
| Theme repo | Kept every theme (icons, cursors, sounds, GTK and window-border themes) in a single `desktop-themes` repo, with a README per theme, so a look can be reapplied to a fresh install instead of rebuilt from memory. Added the Windows 7 GTK theme, Windows 7 icons and sounds. |
| Theme switcher | A small shell script sets the GTK theme, window borders, icons, cursor and fonts through Xfce's settings tool for `xp`, `win7` or `classic`, and can print the current values. It only changes settings and deletes nothing, so `classic` puts everything back. |
| Panel styling | A stylesheet for each look restyles the taskbar, the window buttons, the clock and tray area, and the old Start menu. The script copies the right one into place and adds a single import line to my GTK stylesheet, so the rest of that file is never edited. |
| Start menu | A new Windows 7 style Start menu written in Python with GTK 3. It has pinned and most-used programs, an All Programs list, search, a places column and a Shut down button with restart, sleep, log off and lock. |

### Python app, not a panel plugin

The question was whether the Start menu should be an Xfce panel plugin or a separate program. A plugin has to be written in C and compiled against the panel, and it still only provides a button in the panel; the menu itself is still a window I would have to draw. A Python GTK window uses the same toolkit as the rest of the desktop, needs no compiling and can be restyled with plain CSS. The panel just runs a command (`--toggle`) from a launcher button or the Super key, and the program shows or hides its window. It reads the system's application entries, so new programs appear without any list to maintain.

### Why not keep restyling the old menu

The existing menu could be recoloured but not rearranged. In particular the search box and the two-column layout are fixed, and those are the parts that make a Windows 7 menu look like one.

### Checking it

I ran it on the real desktop and took screenshots. That showed two faults that reading the code would not have: the blue glass background was not being painted (the right-hand column was see-through and unreadable), and the places labels were staggered instead of left-aligned. Both are fixed. I have not yet tested launching programs from it, search, or the Super key.

## Problems / debugging

- **Transparent background.** The blue panel was set on the window, and the theme overrode it. Painting it on an inner box fixed that.
- **Start menu closed straight away.** My first launch also toggled the window, so my second command closed it again. A separate `--daemon` start (hidden) and `--toggle` (show or hide) removed the ambiguity.
- **A theme folder that is really another repo.** The Windows 7 theme was cloned into the repo with its own git history, so my repo records it only as a pointer to someone else's repository and a fresh clone would get an empty folder. Still to fix.
- **Process cleanup.** `pkill -f` against a name that appears in my own command line can match itself. A bracket in the pattern (`[w]in7...`) avoids that.

## Decisions

- **Add looks beside the old one, never replace it.** Everything is reversible with one command.
- **Plain Python and CSS over a compiled plugin.** Easier for me to read, change and rebuild from the README.
- **A crashing Start menu must not affect the panel.** It runs as its own process and talks to nothing else.
- **Document the manual steps instead of scripting them.** The README lists the launcher, autostart and Super key setup so I can do and understand each step.

## What I learned

- Look at a screenshot before declaring a visual change done. The code looked right and the result was not.
- A theme that is "mostly CSS" is quicker to iterate on than any compiled component, which matters when the work is trial and error.
- Embedded repos are easy to create by accident and quietly break reproducibility.

## Next steps

- Draw the orb (Start button) artwork and add it to the repo.
- Wire the launcher, autostart and Super key, then test launching apps, search and Shut down.
- Replace the pointer to the Windows 7 theme with real files so a fresh clone works.
- Bring the XP and Windows 7 switcher scripts into the theme repo with the rest.
