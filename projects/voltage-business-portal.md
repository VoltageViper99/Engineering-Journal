---
title: Voltage Business Portal
date: 2026-10-30
category: Development
tags:
  - Python
  - Flask
  - Business tooling
  - Security
summary: One front door for my repair business that brings the repair app, software licences and server status into a single login, with a second, limited login for a helper.
public: true
featured: false
---

[Back to projects](README.md)

## Summary

The Voltage Business Portal is a small website that sits in front of the tools I already run. It shows the day's business, a customer list and profile, software licences, every text message sent, a read-only view of the server's health and a settings area. It does not replace [VoltOps](volt-ops.md) or the [Control Centre](control-centre.md): it asks them questions and shows their answers. The source is in a private repository.

## Problem

My repair app was a window on my desktop, my server status was on another page and software licences were somewhere else again. I wanted to see them together, reach them from any of my devices, and let a helper do part of the work without handing over everything.

## Architecture

- A small server-rendered Flask app that owns the login, the page frame and the homepage.
- The portal talks to the other systems **server to server**. The browser only ever talks to the portal.
- VoltOps runs headless on the server and exposes a narrow API that only the portal uses. The existing VoltOps pages are relayed through the portal.
- The Control Centre offers a small read-only health feed, with a key that can open nothing else.
- Reached by name through the private reverse proxy, over the LAN and my private network only.

## Technologies

Python, Flask, Jinja, systemd, Caddy, GitHub Actions.

## My role

- Decided what the portal shows and what it may do, and what the helper account must not see.
- Wrote the specification for the second login before any code.
- Rehearsed the move of VoltOps to the server on spare ports, wrote the runbook and ran the real switch.
- **AI-assisted development:** the code was written with Claude. I made the design and security decisions, tested the result and operate it.

## Key decisions

- **Separate from VoltOps.** If the portal breaks, VoltOps still works.
- **Least access.** The helper can book, invoice, text and see the numbers, but not owner-only items such as bank details.
- **Server to server only,** so there are fewer things for a browser to reach directly.
- **Fail closed.** The portal refuses to start with unsafe settings, and its own error pages say only what they can know.

## Security considerations

- Internal only. Nothing is published to the internet.
- Login with rate limiting, CSRF checks, an audit trail and a password re-prompt before sensitive settings changes.
- The portal's key to the server's health feed can open nothing else, and a test proves it.
- Limitation: it depends on the server it reports on, so a whole-server outage takes it down too.

## Testing and validation

- About 680 automated checks run on every change, with pinned library versions. The fake VoltOps and Control Centre it tests against are real local web servers.
- A contract test checks that the portal still understands what VoltOps sends.

## Problems encountered

- The proxy could not reach a portal that listened only to its own machine.
- A port I planned to use was already taken.
- The server's health feed marked parts as "never checked" because background jobs were not stamping the time.

See [the journal entry](../journal/2026/2026-10-30.md).

## Current status

In use. Giving the VoltOps pages the portal's look is next, then a button to revoke and reissue a software licence.

## Lessons learned

- A rehearsal finds the problems before the real switch does.
- Work out what a second user must not see before building their login.
- Say only what you know: an error page that claims "everything else is fine" is wrong when everything is down.
