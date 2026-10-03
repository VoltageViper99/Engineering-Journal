---
title: Homelab Overview
date: 2026-10-03
category: Infrastructure
tags:
  - Linux
  - Docker
  - Homelab
  - Security
summary: A self-hosted Linux and Docker lab that runs my everyday services and doubles as a place to learn, test and practise safe operations.
public: true
featured: false
---

This is a high-level overview. It deliberately leaves out addresses, network layout, firewall details and a service-by-service inventory.

## Purpose

The homelab is where I run my own services and where I learn by operating them. It supports day-to-day tooling, development and testing for Voltage PC Repairs, and it is a safe place to practise infrastructure, networking and security work on something real.

## High-level architecture

- One Linux host running Docker.
- Each group of related services is its own Docker Compose stack, with the definitions kept in version control. Application data and media volumes are not.
- Secrets, certificates and runtime data are git-ignored, so a stack can be rebuilt from the repository plus separately held secrets.
- Internal services are reached by name through a private reverse proxy (see [Internal Reverse Proxy](reverse-proxy.md)) over LAN and Tailscale.
- Each proxied service shares a small private network with the proxy only, so services stay isolated from each other.
- A small number of services are published to the internet through Cloudflare Tunnels. That is a separate path from the internal proxy.
- A shared network lets the monitoring stack reach other stacks directly for health checks.

## What it supports

- Media and music management
- Network-wide DNS and ad blocking
- File storage and sync for family use
- Photo and video backup
- Home automation
- Monitoring, log viewing and push notifications (see [Monitoring](monitoring.md))
- Business tooling for Supporter Invoicing, such as agreement signing, licence records and automation
- A central internal dashboard (see [Control Centre](../projects/control-centre.md))

## Design principles

- **Infrastructure as code.** A stack can be rebuilt from its definition.
- **Internal by default.** Public exposure is the exception and needs a reason.
- **Names, not ports.** Reach services by name instead of remembered host ports and addresses.
- **Fewer moving parts.** Retire a tool when another covers it. Homarr gave way to the Control Centre, and a self-hosted Git service was removed when hosting moved back to GitHub.
- **Keep secrets and data out of version control.**
- **Match update risk to the service.** Low-risk services update themselves. Anything with a database, family data or business impact is updated by hand.

## Security approach

- Public services use outbound tunnels, not inbound port forwards.
- Internal services are limited to LAN and Tailscale. Unknown hostnames do not expose anything.
- Secrets live in git-ignored environment files. Some stacks refuse to start if a secret is missing, so they cannot fall back to defaults.
- Updates are opt-in per container through labels. A self-hosted notification service reports update activity.
- Obsolete rules and services are removed rather than left behind. Recent examples are retired firewall rules, a redundant dashboard and a backup folder that contained key material. The last one is written up in [Signing Key Committed to Version Control](../security/incident-response.md).
- Hardening is an ongoing learning plan for the host, not a finished state.

## Current direction

- Finish moving services behind the internal reverse proxy, starting with the media stack.
- Narrow host-port exposure where it is safe to do so.
- Add the remaining media tooling.
- Keep monitoring aligned with the new service names.
- Add secret scanning to the infrastructure repository.
- Continue cleanup and hardening.

See the [journal](../journal/2026/) for dated changes.
