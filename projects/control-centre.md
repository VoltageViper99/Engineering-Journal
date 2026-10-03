---
title: Control Centre
date: 2026-10-03
category: Infrastructure
tags:
  - Homelab
  - Dashboard
summary: A central internal dashboard for seeing and reaching the services in my lab from one place.
public: true
featured: false
---

## Purpose

A central internal interface for homelab and server visibility and navigation.

## Problems it solves

- Reduces the need to open individual admin or service interfaces directly.
- Replaces redundant dashboards with a single place to look.

## Current role

Acts as the entry point for internal services. Its links were updated as services moved behind the internal reverse proxy (see [Reverse Proxy](../infrastructure/reverse-proxy.md)). It has taken over from Homarr, which has been removed.

## Design direction

Gradually replace other dashboards and reduce direct access to individual service interfaces. Implementation details are not documented here yet.

## Security considerations

Intended for internal use over LAN and Tailscale. Further notes to be added once documented.

## Current status

In use and evolving. Detailed documentation pending.
