---
title: Monitoring Approach
date: 2026-10-03
category: Infrastructure
tags:
  - Monitoring
  - Docker
  - Linux
  - Reliability
summary: How I keep an eye on service health in my lab, and why checking monitoring is part of every infrastructure change.
public: true
featured: false
---

This page describes the approach, not the exact monitors, alerts or thresholds.

## Purpose

Know when a service is down or misbehaving before I rely on it, and confirm that a change did what I intended.

## What is monitored

- Service availability and health
- Container logs and Docker status
- Update activity for containers
- Power status of the host's UPS
- A single view of internal services through the Control Centre

## Tools and approach

- **Uptime Kuma** for service health checks.
- **Dozzle** for live container logs, which is the fastest way to see why a container is unhappy.
- **Portainer** for Docker status and management.
- **A UPS dashboard** for power status.
- **ntfy**, self-hosted, for push notifications. Watchtower reports container updates through it.
- **Control Centre** as the internal starting point for reaching services (see [Control Centre](../projects/control-centre.md)).
- **Log rotation** on chatty containers, so a runaway error loop cannot fill the disk. Watchtower's logs are capped for this reason.

## How monitoring fits change management

Monitoring checks the names and addresses services are reached by, so a migration can leave it pointing at something that no longer exists. The reverse proxy rollout showed this directly: the monitors must be repointed to the new service names, and user-facing links updated to match.

After a migration:

1. Update the links in the Control Centre.
2. Repoint monitors to the new names.
3. Check that each service responds through its new path.
4. Retire monitors and links for anything removed.

Update policy follows the same idea. Low-risk services update automatically and report through notifications. Services with databases, family data or business impact are updated by hand, and I check them afterwards.

## Security and reliability considerations

- Monitoring tools are administrative interfaces, so they are kept internal.
- The monitors run on the same host as the services they watch. A whole-host outage can silence them as well, and an independent check from outside is a gap to consider.
- Notification quality matters. A notification topic shared with unrelated projects once mixed update notices with unrelated noise, and the noise was mistaken for errors. Dedicated, clearly named topics fixed it.
- A monitor that nobody reads is no monitor, so noise should stay low.

## Current direction

- Repoint monitors after the reverse proxy migration.
- Keep monitors and links in step with each service move.
- Add visibility into security issues and vulnerabilities in running containers. This is a goal, not something in place yet.
