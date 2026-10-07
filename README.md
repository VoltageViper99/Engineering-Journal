# Engineering Journal

Engineering journal documenting hands-on Linux systems administration, homelab infrastructure, Docker, networking, security, monitoring, automation and AI-assisted tooling.

## About

I'm TJ, a self-employed IT technician running [Voltage PC Repairs](https://voltagepcrepairs.com.au) in Australia. My interest is Linux, infrastructure and DevOps, and I learn by running real systems: a Docker-based homelab that supports my business tooling and my family's everyday services.

This repository is where I write that work down: what I built or changed, why, what broke and what I learned. Sensitive details (addresses, credentials, customer information, exact firewall and port layouts) are deliberately left out. See the [publication guide](PUBLICATION-GUIDE.md).

**How I work.** Some project code and tooling were developed with AI-assisted coding tools (Claude). I'm responsible for requirements, architecture, deployment, testing, validation, security decisions and ongoing operation. I'm not presenting myself as a software developer. The strength of this repo is the systems work around the code.

## If you only have 5 minutes, start here

1. [Homelab overview](infrastructure/homelab.md): what runs, how it's laid out, and the principles behind it.
2. [Internal reverse proxy](infrastructure/reverse-proxy.md): reaching services by name instead of by address and port.
3. [Retiring Portainer and hardening network exposure](journal/2026/2026-10-04.md): a day of audit-then-harden work (SSH, firewall, logins, closing ports).
4. [Signing key committed to version control](security/incident-response.md): an incident case study, from detection to rotation to prevention.
5. [Control Centre](projects/control-centre.md): the internal dashboard that ties the lab together.
6. Latest entry: see the [journal index](journal/README.md).

## What this repo contains

| Section | What's in it |
| --- | --- |
| [`journal/`](journal/README.md) | Dated engineering logs: changes, debugging, decisions, lessons |
| [`projects/`](projects/README.md) | Project write-ups, each with an explicit "my role" section |
| [`infrastructure/`](infrastructure/README.md) | Architecture and platform notes: homelab, Docker, reverse proxy, monitoring |
| [`security/`](security/README.md) | Hardening notes and incident write-ups |
| [`repairs/`](repairs/README.md) | Hardware repair and diagnostic case studies (planned) |

## Featured work

**Homelab and reverse proxy.** One Docker host running Compose stacks defined in version control, with internal services reached by name through a private Caddy proxy over LAN and Tailscale. A few services are published through Cloudflare Tunnel instead of port forwards. *What I did:* designed the layout, deployed and migrated the services, and wrote the migration order and rollback steps. [Homelab](infrastructure/homelab.md), [Reverse proxy](infrastructure/reverse-proxy.md).

**Security hardening and exposure audit.** Audited what the server really listens on, then moved SSH to key-only with fail2ban, narrowed firewall rules, put logins and TLS on admin tools, and closed direct ports. *What I did:* chose the method (repoint first, close second), tested every change from a fresh connection, and verified the apps still worked. [Journal, 04/10/2026](journal/2026/2026-10-04.md), [Hardening](security/hardening.md).

**Incident response.** A private key reached a git repository through a backup folder. I contained it, rotated it, debugged two configuration failures and added a fail-closed secret-scanning hook. [Case study](security/incident-response.md).

**Monitoring.** Uptime Kuma, Dozzle, a UPS dashboard and self-hosted ntfy notifications, kept in step with every service move. [Monitoring](infrastructure/monitoring.md).

**Control Centre.** An internal dashboard for Docker stacks, host health and a music remote. The code was largely written with AI assistance; I wrote the requirements, chose the architecture and run it. [Control Centre](projects/control-centre.md).

**Other projects.** [VoltOS](projects/voltos.md), [VoltOps](projects/volt-ops.md), [Holocron Kiosk](projects/holocron-kiosk.md), [Supporter Invoicing](projects/supporter-invoicing.md).

## Skills demonstrated

Only things evidenced in this repo.

- **Linux / systems:** administration, systemd, SSH hardening, fail2ban, firewall rule review and cleanup, host and UPS monitoring.
- **Docker / containers:** Compose stacks as code, container networks for isolation, log rotation, scoped automatic updates with Watchtower, image and volume cleanup.
- **Networking:** reverse proxying with Caddy, wildcard internal DNS, Tailscale, Cloudflare Tunnel, how Docker-published ports interact with the host firewall.
- **Security:** attack-surface audit, authentication on admin tools, TLS, least-privilege accounts, secrets handling, incident response and key rotation.
- **Monitoring:** Uptime Kuma, Dozzle, ntfy, noise reduction, monitoring as part of change management.
- **Automation:** opt-in update policy, pre-commit secret scanning, README-driven rebuildable setups.
- **Git and documentation:** version-controlled infrastructure, sanitised public write-ups, structured journal entries.
- **AI-assisted workflow:** specifying requirements, reviewing and testing AI-generated changes, and keeping responsibility for what runs.

## Journal

Latest entries (full list in the [journal index](journal/README.md)):

- 04/10/2026: [Retiring Portainer and hardening network exposure](journal/2026/2026-10-04.md)
- 03/10/2026: [Internal reverse proxy rollout](journal/2026/2026-10-03.md)
- 29/09/2026: [Scoped automatic container updates](journal/2026/2026-09-29.md)
- 26/09/2026: [Container update tooling and service cleanup](journal/2026/2026-09-26.md)

## Current learning

- DevOps practice: CI/CD, infrastructure automation
- Linux certification preparation
- Kubernetes, later, once the Docker fundamentals feel solid

## Links

- GitHub: [VoltageViper99](https://github.com/VoltageViper99)
- Business: [Voltage PC Repairs](https://voltagepcrepairs.com.au)

## Licence

Text is licensed under [CC BY 4.0](LICENSE). Pages carry front matter (`title`, `date`, `category`, `tags`, `summary`, `public`, `featured`) so a feed can be built from them later.
