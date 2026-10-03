---
title: Internal Reverse Proxy
date: 2026-10-03
category: Infrastructure
tags:
  - Linux
  - Docker
  - Caddy
  - Networking
summary: How internal services are reached by name through a private reverse proxy instead of raw addresses and ports.
public: true
featured: false
---

This page describes the pattern, not the configuration.

## Pattern

```text
Client
→ internal DNS
→ Caddy
→ internal service
```

## How it works

- The internal domain is `voltinfra.net`, with wildcard DNS for `*.voltinfra.net`.
- Caddy handles internal reverse proxying.
- Services are intended to be reachable over LAN and Tailscale.
- Public exposure through Cloudflare Tunnel is a separate path and is not part of this proxy.
- Unknown hostnames should not automatically expose services.

## Why this design

- It reduces direct use of host ports and raw IP addresses.
- Names are easier to remember, document and move than address and port pairs.
- A single entry point is easier to reason about than many exposed ports.

## Status

Media services are already being moved to this model. Further services will follow.

## Deliberately not published

Internal addresses, routing rules, credentials, firewall configuration and port lists.
