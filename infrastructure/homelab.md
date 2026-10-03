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

- A Linux host running Docker.
- Each service group is its own Docker Compose stack, with the definitions kept in version control. Application data and media volumes are not.
- Internal services are reached by name through a private reverse proxy (see [Internal Reverse Proxy](reverse-proxy.md)) over LAN and Tailscale.
- A small number of services are published to the internet through Cloudflare Tunnels. This is a separate path from the internal proxy.

## What it supports

- Media and music management
- Network-wide DNS and ad blocking
- File storage and sync
- Photo and video backup
- Monitoring, log viewing and push notifications (see [Monitoring](monitoring.md))
- Business tooling for Supporter Invoicing, such as agreement signing, licence records and automation
- A central internal dashboard (see [Control Centre](../projects/control-centre.md))

## Design principles

- Infrastructure as code: a stack can be rebuilt from its definition.
- Internal by default. Public exposure is the exception and needs a reason.
- Reach services by name, not by remembered host ports and addresses.
- Fewer dashboards and fewer moving parts where one tool can do the job.
- Keep secrets and data out of version control.

## Security approach

- Public services use outbound tunnels, not inbound port forwards.
- Internal services are limited to LAN and Tailscale.
- Secrets live in environment files that are git-ignored.
- Container updates are opt-in per service. Update activity is reported through self-hosted notifications.
- Obsolete rules and services are removed rather than left behind. Recent examples are retired firewall rules and a redundant dashboard.

## Current direction

- Finish moving services behind the internal reverse proxy, starting with the media stack.
- Narrow host-port exposure where it is safe to do so.
- Add the remaining media tooling.
- Keep monitoring aligned with the new service names.
- Continue cleanup and hardening.

See the [journal](../journal/2026/2026-10-03.md) for the latest changes.
