---
title: Supporter Invoicing
date: 2026-10-03
category: Automation
tags:
  - Invoicing
  - Cloudflare
  - Packaging
  - CI
summary: A desktop invoicing app for Australian sole-trader support workers, plus the Cloudflare, container and release tooling I run around it.
public: true
featured: false
---

[Back to projects](README.md)

## Summary

Supporter Invoicing is a proprietary desktop invoicing and expense app for Australian sole-trader support workers, covering GST handling and an end-of-financial-year summary. This page is about the product and the infrastructure around it. The source is in private repositories.

## Problem

Support workers lose time preparing invoices by hand, exporting PDFs and tracking income. Around the app there is also release, update and licensing tooling that has to run reliably.

## Architecture

- A desktop app packaged for Windows, macOS and Debian, with installers built in CI.
- An update check that reads a hosted manifest and verifies a SHA-256 digest before opening an installer.
- A Cloudflare Worker that issues signed licence tokens after a purchase webhook, and containerised services on the homelab for agreement signing, licence data and the purchase automation.
- The [Control Centre](control-centre.md) shows the pipeline's health.

## Technologies

Python, Cloudflare Workers and KV, Docker Compose, Inno Setup, code signing, GitHub Actions.

## My role

- Defined the product requirements and how it should be sold and licensed.
- Chose how updates, signing and licence records fit together, and deployed the services.
- Tested releases, debugged failures and documented the setup.
- **AI-assisted development:** the application code was written mostly with Claude. I specified, tested and shipped it.

## Key decisions

- Licensing gates are risky. An earlier licence-key gate was reverted after it stopped the packaged app launching, and the licensing model is still being finalised.
- Move Windows packaging from a store package to a signed installer to avoid store review for each release.
- Keep secrets out of version control after the [signing key incident](../security/incident-response.md).

## Security considerations

- Update downloads are verified by digest. Signing keys and licence data are kept out of git, and containers refuse to start if a secret is missing.

## Testing and validation

- The app has an automated test suite and a CI workflow that builds the installers. Release builds are tested on the target platforms before use.

## Problems encountered

- The licence gate regression above, and the signing key incident.

## Current status

Active development, pre-release. Some release infrastructure (code-signing certificate, download host) is still waiting on external setup.

## Lessons learned

- Ship a revert plan with every risky change.
- Test the packaged app, not just the source tree.
