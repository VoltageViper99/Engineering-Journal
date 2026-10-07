---
title: Holocron Kiosk
date: 2026-10-03
category: Infrastructure
tags:
  - Kiosk
  - Qt
  - Homelab
summary: A native display client that shows the Control Centre on a dedicated screen and plays music for its remote control.
public: true
featured: false
---

[Back to projects](README.md)

## Summary

The kiosk is the on-screen half of the [Control Centre](control-centre.md): a small native PySide6 (Qt) application on a dedicated display box. It shows the dashboard pages and plays the audio when I use the music page as a remote.

## Problem

The first version used a Chromium-based kiosk. It kept hitting browser behaviour that does not belong on a wall display: reserved keyboard shortcuts, context menus and odd input from cheap remotes.

## Architecture

- The Control Centre server does the data collection. The kiosk only renders pages and plays audio.
- The client talks to the server over the network and starts at boot through a systemd service on the display box.
- The earlier single-screen app and terminal client are kept in the repo, unmaintained, for reference.

## Technologies

Python, PySide6 (Qt), systemd.

## My role

- Set the requirement: a reliable, always-on display that behaves like an appliance.
- Decided to drop the browser kiosk for a native client after the input problems.
- Set up the display box, boot service and network access, and test it day to day.
- **AI-assisted development:** the client code was written with Claude. I specified, tested and operate it.

## Key decisions

- A native window removes a whole class of browser problems rather than working around each one.
- The display holds no data collection logic, so replacing the box does not mean reconfiguring integrations.

## Security considerations

- It is an internal-only client of an internal-only service. Nothing on the display box is reachable from the internet.

## Testing and validation

- Checked by running it on the real display box with the real remote. There is no formal test suite for the client.

## Problems encountered

- The browser-kiosk input problems above are what drove the rewrite.

## Current status

In use.

## Lessons learned

- If a tool fights its environment, change the tool before piling on workarounds.
