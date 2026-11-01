---
title: Voltage Music
date: 2026-10-31
category: Development
tags:
  - Python
  - Flask
  - Audio
  - Linux
  - Bluetooth
summary: A self-hosted music player that plays from my server through a Bluetooth adapter, with gapless playback, loudness levelling, radio and smart playlists, split out of my dashboard into its own app.
public: true
featured: false
---

[Back to projects](README.md)

## Summary

Voltage Music is the music player for my home. It reads my library from a self-hosted music server, plays it from the server's own audio output, and is controlled from any browser on my network: phone, tablet or computer. It began as part of the [Control Centre](control-centre.md) and became its own app on 31/10/2026.

## Problem

A music player inside a server dashboard meant that a bug in one could get in the way of the other, and the player's look had grown one control at a time. Retiring the kiosk (the screen that used to play the music) also meant the server had no way to make sound of its own.

## Architecture

- A Flask web app with server-rendered pages and small amounts of JavaScript, no build step, and its own database, login, settings and service.
- Playback is **mpv**, a command-line player the app controls, sending sound to a Bluetooth adapter plugged into the server. Closing the browser does not stop the music.
- **Gapless:** the next track is lined up before the current one ends.
- **Loudness levelling:** the app measures each track once and stores a gain, then applies it. The player's own ReplayGain is switched off so the two cannot stack.
- Radio stations with a scheduler, smart playlists (one made with the help of a local AI model that can only suggest) and a station log.
- A one-way import tool brought the play history and settings across from the Control Centre's database.

## Technologies

Python, Flask, SQLite, mpv, PipeWire, systemd, a Subsonic-compatible music server.

## My role

- Decided to split the player out, and what should and should not move.
- Chose the design (violet on near-black, big text for reading from across a room) and sent back every change I wanted after living with it.
- Installed each step on the real server and tested it by listening, then wrote the update steps down.
- **AI-assisted development:** the code was written with Claude. I set the requirements, reviewed and tested the result, and I operate it.

## Key decisions

- **Own app, own login, own data** so a fault in one cannot reach the other.
- **Copy, do not share, the common code.** About a thousand lines of login and settings code were copied instead of turned into a shared library.
- **Leave out what I would not use.** Crossfade, an energy mix and similar artists were planned and then removed.
- **A one-command update** that refuses to run as root.

## Security considerations

- Internal only, over my LAN and private network.
- Sessions lock after a period without use. A background check that kept an idle session alive was found and fixed.
- The update script refuses to run as root.

## Testing and validation

- A test suite of over a thousand checks. One known test depends on random choices and fails now and then, which I have noted rather than hidden.
- The part a test cannot check, how it sounds, I check by listening: gaps between tracks, volume between tracks and what happens when the browser is closed.

## Problems encountered

- A once-a-second page check counted as use and kept sessions open forever.
- Fonts the design asked for were not included in the new app, so pages quietly used a fallback.
- A test I wrote assumed the wrong word for "off" from the player.

See [the journal entry](../journal/2026/2026-10-31.md) and the [Bluetooth fix](../journal/2026/2026-11-01.md).

## Current status

In daily use. The old interface was removed on 01/11/2026.

## Lessons learned

- Keep the old thing working until the new one is proven.
- Read what a program actually reports. "No" and false are not the same.
- Use a design for a day before you decide what is wrong with it.
