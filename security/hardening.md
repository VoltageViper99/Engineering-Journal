---
title: Hardening
date: 2026-10-03
category: Security
tags:
  - Hardening
  - SSH
  - Firewall
  - Docker
summary: The method I use to harden the lab host and its services, and the controls currently in place.
public: true
featured: true
---

[Back to security](README.md)

Approach and controls only. Exact rules, addresses and port lists are not published.

## Method

1. **Audit before hardening.** List what really listens, then read the firewall rules, the Docker firewall policy and the SSH settings. Reading the repo alone missed pages with no login and services bound to every address.
2. **Repoint first, close second.** Move each dependent app to the new path while the old one still works, test the real connections, then close the old port. The change stays easy to undo until the last step.
3. **Login, then TLS, then close the port.** Verify each step before the next.
4. **Test every change from a fresh connection.** For SSH, prove key login works and keep a second session open. Add a narrow rule before deleting a wide one.

## Controls in place

- SSH is key-only, root login is refused, attempts per connection are limited and fail2ban watches it. It is reachable from the home network and over Tailscale only.
- Firewall rules were narrowed to the home network and the containers that need them. Obsolete rules were removed.
- Admin tools have their own logins, sit behind the private proxy for TLS and have their direct ports closed. Services with no login are bound to loopback or kept off the network.
- Notification access uses limited accounts with deny-by-default.
- Secrets live in git-ignored files, and a fail-closed pre-commit scanner blocks likely secrets.
- Public services use outbound Cloudflare Tunnels rather than port forwards.

## Known gaps

- Monitoring runs on the host it watches.
- Vulnerability visibility for running containers is a goal, not in place yet.
- Hardening is ongoing, not a finished state.

## Evidence

- [Journal, 2026-10-04](../journal/2026/2026-10-04.md): the audit and the changes in order.
- [Incident case study](incident-response.md): what happens when a secret slips through.
