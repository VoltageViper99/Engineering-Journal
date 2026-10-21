# Engineering Journal

Engineering journal documenting hands-on Linux systems administration, homelab infrastructure, Docker, networking, security, monitoring, automation and AI-assisted tooling.

## About

I'm TJ, a self-employed IT technician running [Voltage PC Repairs](https://voltagepcrepairs.com.au) in Australia. My interest is Linux, infrastructure and DevOps, and I learn by running real systems: a Docker-based homelab that supports my business tooling and my family's everyday services.

This repository is where I write that work down: what I built or changed, why, what broke and what I learned. Sensitive details (addresses, credentials, customer information, exact firewall and port layouts) are deliberately left out. See the [publication guide](PUBLICATION-GUIDE.md).

**How I work.** Some project code and tooling were developed with AI-assisted coding tools (Claude). I'm responsible for requirements, architecture, deployment, testing, validation, security decisions and ongoing operation. I'm not presenting myself as a software developer, though one small project, the [CD backup script](projects/cd-backup.md), is mostly my own Python. I prefer plain, simple interfaces, and I build and set things up so that people who are not into computers can use them. The strength of this repo is the systems work around the code.

**Not a tech person?** Every journal entry opens with an "In short" box in plain English, and the [glossary](GLOSSARY.md) explains the technical words.

## If you only have 5 minutes, start here

1. [Control Centre](projects/control-centre.md): the dashboard I run the whole lab from, and the project I am proudest of.
2. [Homelab overview](infrastructure/homelab.md): what runs, how it's laid out, and the principles behind it.
3. [Internal reverse proxy](infrastructure/reverse-proxy.md): reaching services by name instead of by address and port.
4. [Retiring Portainer and hardening network exposure](journal/2026/2026-10-04.md): a day of audit-then-harden work (SSH, firewall, logins, closing ports).
5. [Signing key committed to version control](security/incident-response.md): an incident case study, from detection to rotation to prevention.
6. Latest entry: see the [journal index](journal/README.md).

## What this repo contains

| Section | What's in it |
| --- | --- |
| [`journal/`](journal/README.md) | Dated engineering logs: changes, debugging, decisions, lessons |
| [`projects/`](projects/README.md) | Project write-ups, each with an explicit "my role" section |
| [`infrastructure/`](infrastructure/README.md) | Architecture and platform notes: homelab, Docker, reverse proxy, monitoring |
| [`security/`](security/README.md) | Hardening notes and incident write-ups |
| [`repairs/`](repairs/README.md) | Hardware repair and diagnostic case studies (planned) |
| [`GLOSSARY.md`](GLOSSARY.md) | Plain-English explanations of the technical terms |

## Featured work

**Control Centre.** The tool that makes a large Docker and server stack manageable. One dashboard shows every stack, host health, security and update status, and the invoicing pipeline, and lets me restart or upgrade a single container safely with protection levels, health checks and a one-time undo. It is the project I use most and have put the most thought into. The code was largely written with AI assistance. *What I did:* defined the problem, designed what it shows and what it may do, tested every change against the real lab, and run it daily. [Control Centre](projects/control-centre.md), [how it grew](journal/2026/2026-10-09.md).

![Control Centre overview showing weather, server health, containers, music and the invoicing pipeline](assets/screenshots/control-centre-overview.png)

**Homelab and reverse proxy.** One Docker host running Compose stacks defined in version control, with internal services reached by name through a private Caddy proxy over LAN and Tailscale. A few services are published through Cloudflare Tunnel instead of port forwards. *What I did:* designed the layout, deployed and migrated the services, and wrote the migration order and rollback steps. [Homelab](infrastructure/homelab.md), [Reverse proxy](infrastructure/reverse-proxy.md).

**Security hardening and exposure audit.** Audited what the server really listens on, then moved SSH to key-only with fail2ban, narrowed firewall rules, put logins and TLS on admin tools, and closed direct ports. *What I did:* chose the method (repoint first, close second), tested every change from a fresh connection, and verified the apps still worked. [Journal, 04/10/2026](journal/2026/2026-10-04.md), [Hardening](security/hardening.md).

**Incident response.** A private key reached a git repository through a backup folder. I contained it, rotated it, debugged two configuration failures and added a fail-closed secret-scanning hook. [Case study](security/incident-response.md).

**Monitoring.** Uptime Kuma, Dozzle, a UPS dashboard and self-hosted ntfy notifications, kept in step with every service move. [Monitoring](infrastructure/monitoring.md).

**Supporter Invoicing.** A live product with a public website and a purchase pipeline I built and run: signed agreement (self-hosted Documenso), Stripe payment, Cloudflare Worker licence issuing and CI-built signed installers. The code was largely AI-assisted; the pipeline design, deployment and testing are mine. [Supporter Invoicing](projects/supporter-invoicing.md).

**CD Backup Script.** A Python tool that copies old data discs to server storage, with dry runs, clean failure handling and an optional full-screen interface. This is the one project here where I wrote most of the core code myself, as a way to learn Python. [CD Backup Script](projects/cd-backup.md).

**Business websites.** Three small business sites (my repair business, a photography business and the invoicing product site) deployed from Git to Cloudflare Pages, with a contact form hardened by Turnstile, rate limiting and a honeypot. I built the first version of the photography site myself; the later redesign and the other sites' code were largely AI-assisted. The setup, security and operation are mine. [Business websites](projects/business-websites.md), [the deploy that failed quietly](journal/2026/2026-10-12.md).

**Other projects.** [VoltOS](projects/voltos.md), [VoltOps](projects/volt-ops.md), [Holocron Kiosk](projects/holocron-kiosk.md).

## Skills demonstrated

Only things evidenced in this repo.

- **Linux / systems:** administration, systemd, SSH hardening, fail2ban, firewall rule review and cleanup, host and UPS monitoring.
- **Linux desktop:** Fedora (dnf) with Xfce, earlier Arch-based systems, KDE Plasma, GNOME and Hyprland, a documented rebuild order and a package list kept in step with the machine.
- **Docker / containers:** Compose stacks as code, container networks for isolation, log rotation, scoped automatic updates with Watchtower, image and volume cleanup.
- **Networking:** reverse proxying with Caddy, wildcard internal DNS, Tailscale, Cloudflare Tunnel, how Docker-published ports interact with the host firewall.
- **Security:** attack-surface audit, authentication on admin tools, TLS, least-privilege accounts, secrets handling, incident response and key rotation.
- **Monitoring:** Uptime Kuma, Dozzle, ntfy, noise reduction, monitoring as part of change management.
- **Python scripting:** a command-line tool with batch handling, error clean-up and a tested permission rule, written mostly by me as a learning project.
- **Web hosting and deployment:** static sites on Cloudflare Pages deployed from Git, Pages Functions, Turnstile, KV rate limiting, DNS and email-domain verification.
- **Automation:** opt-in update policy, pre-commit secret scanning, README-driven rebuildable setups.
- **Git and documentation:** version-controlled infrastructure, sanitised public write-ups, structured journal entries.
- **AI-assisted workflow:** specifying requirements, reviewing and testing AI-generated changes, and keeping responsibility for what runs.

## Journal

Latest entries (full list in the [journal index](journal/README.md)):

- 21/10/2026: [Turning my music app into a Windows Media Player](journal/2026/2026-10-21.md)
- 20/10/2026: [Dashboard themes and a local AI for playlists](journal/2026/2026-10-20.md)
- 17/10/2026: [Windows XP and 7 themes, and a Start menu built from scratch](journal/2026/2026-10-17-windows-themes.md)
- 15/10/2026: [VoltOps, texted receipts and an audit pass](journal/2026/2026-10-15.md)
- 12/10/2026: [Finding a Linux desktop that works for me](journal/2026/2026-10-12-linux-desktop.md)
- 12/10/2026: [The homelab so far, and a repeatable security audit](journal/2026/2026-10-12-homelab.md)
- 12/10/2026: [Business websites, and a security fix that was not live](journal/2026/2026-10-12.md)
- 10/10/2026: [Building a CD backup script in Python](journal/2026/2026-10-10.md)
- 09/10/2026: [Control Centre, from dashboard to operations hub](journal/2026/2026-10-09.md)
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
