---
title: Docker
date: 2026-10-03
category: Infrastructure
tags:
  - Docker
  - Compose
  - Automation
summary: How the lab's containers are defined, isolated, updated and cleaned up.
public: true
featured: false
---

[Back to infrastructure](README.md)

How I run containers on the lab host. Addresses, ports and the service inventory are deliberately left out.

## Approach

- **One Compose stack per group of related services.** Definitions live in version control. Secrets, certificates and runtime data do not, so a stack can be rebuilt from the repository plus separately held secrets.
- **Small private networks.** Each proxied service shares a network with the [reverse proxy](reverse-proxy.md) only, so services cannot reach each other by accident. A separate shared network lets monitoring reach other stacks for health checks.
- **Container names, not host ports.** Apps talk to each other by container name. Anything with no login of its own is kept off the network, and ports that must exist for the host are bound to loopback.
- **Fail closed on secrets.** Some stacks refuse to start when a required secret is missing, so they cannot fall back to a default or open login.
- **Capped logs.** Chatty containers have log rotation so an error loop cannot fill the disk.

## Updates

Automatic updates are opt-in per container through a label, using a maintained fork of the original update tool. Low-risk services (media tooling, DNS filtering, monitoring, notifications) update themselves. Anything with a database, family data or business impact is updated by hand and checked afterwards. See [scoped automatic updates](../journal/2026/2026-09-29.md).

## Removing a service properly

Deleting the compose block only removes the definition. A full removal also covers the container, its volume, its image, any firewall rules, the docs and the ignore entries. [Portainer's retirement](../journal/2026/2026-10-04.md) is the worked example.

## Things to remember

- Docker-published ports bypass the host firewall, so two separate policies decide who can reach a service.
- A container's published port can differ from the port the app listens on inside it. The logs say which.
- A plain restart does not reload environment values. Recreate the container.

See also: [Monitoring](monitoring.md), [Homelab overview](homelab.md).
