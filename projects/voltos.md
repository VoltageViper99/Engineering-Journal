---
title: VoltOS
date: 2026-10-03
category: Infrastructure
tags:
  - Linux
  - Fedora
  - Packaging
  - Testing
summary: An experimental Fedora-based distribution with a Windows 95/98-inspired desktop, built and tested in virtual machines.
public: true
featured: false
---

[Back to projects](README.md)

## Summary

VoltOS is an experimental Linux distribution built on Fedora. It reuses upstream infrastructure (kernel, Mesa, PipeWire, NetworkManager, systemd, dnf) and puts its effort into the desktop shell, theme and boot experience: a classic Start menu and taskbar on a tiling Wayland compositor.

## Problem

I wanted to learn how a distribution is assembled and packaged, and to have a desktop I enjoy. The project is as much a learning vehicle for packaging, image building and automated testing as a product.

## Architecture

- Fedora 44 base, installed from a kickstart into a VM image under QEMU/KVM.
- A pinned Hyprland RPM stack built from source.
- A `voltos-desktop` package set so a stock Fedora install becomes a VoltOS session with `dnf install` and a reboot.
- Work is split into milestones, each with notes in `docs/dev/` and decisions recorded as ADRs.

## Technologies

Fedora, RPM and dnf, QEMU/KVM, kickstart, systemd, greetd, Hyprland, Waybar, PipeWire, NetworkManager, pytest, make.

## My role

- Set the goals, the milestone order and the look and feel.
- Approved the architecture and the decisions recorded in the ADRs.
- Ran the builds and VM tests and decided what counted as done for each milestone.
- **AI-assisted development:** much of the code, packaging and test scaffolding was written with Claude. I directed the work and validated the results.

## Key decisions

- Reuse mature upstream components and spend effort only on the desktop experience.
- Pin the compositor version and rehearse upgrades before taking them.
- Prove each milestone with an automated smoke test in a VM, from a fresh install through to logout, reboot and shutdown.

## Security considerations

- Idle network traffic is captured and checked in the audio and network milestone.
- A branding asset ledger records where each image came from.
- Not yet reviewed as a hardened distribution.

## Testing and validation

- `make smoke` style targets reset a VM, boot it, wait for SSH, run pytest and power off. Host-only unit targets run in seconds.

## Problems encountered

- Pinned compositor builds mean minor upgrades need a rehearsal rather than a blind update.

## Current status

Experimental. Currently at milestone 6 (branding and power actions).

## Lessons learned

- Repeatable VM tests make a big build safe to change.
- Writing down decisions as they are made saves re-arguing them later.
