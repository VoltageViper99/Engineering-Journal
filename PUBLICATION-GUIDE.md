# Publication Guide

This repository is public. Everything committed here is visible to anyone, forever, including history.

> **Warning:** `public: false` in a page's front matter does **not** protect it. It only marks a page as a draft that should not appear on a website feed. Anything committed is already public. If it is not safe to publish, do not commit it.

## Safe to publish

- High-level architecture and data-flow descriptions
- Sanitised diagrams
- Technology choices and the reasoning behind them
- Troubleshooting approaches and lessons learned
- Generic implementation patterns
- Screenshots that have been reviewed for sensitive data
- Lessons from incidents, once secrets are removed and rotated
- Hardware repair case studies with customer details removed

## Never publish

- Credentials, passwords, API keys, tokens
- Private keys, and certificates or keystores containing sensitive material
- Internal access details or access paths
- Customer information, names, invoice data
- Raw configs that contain secrets
- Private repository URLs
- Serial numbers and identifying customer device information
- Real internal IPs, Tailscale IPs, Cloudflare account or tunnel IDs
- Exact attack surface information (port lists, routing rules, firewall rules)
- Raw vulnerability or port scan output

## Sanitising

Describe roles, not addresses.

| Instead of | Write |
| --- | --- |
| an internal IP and port | Internal Docker Host |
| a specific hostname for an admin UI | the admin interface |
| a customer's name or device serial | a customer's laptop |

## Before commit checklist

- [ ] Search for secrets (see below)
- [ ] Review every screenshot, including browser tabs, URLs and notifications
- [ ] Remove customer identifiers
- [ ] Sanitise addresses and IPs
- [ ] Check config examples are generic and contain no real values
- [ ] Read the full `git diff --staged`
- [ ] Confirm no private keys or certificates are staged
- [ ] Confirm the content is suitable for a public repo

## Lightweight secret scanning

Run a quick check before each commit:

```sh
git diff --staged | grep -nEi 'password|passwd|secret|token|api[_-]?key|BEGIN [A-Z ]*PRIVATE KEY|[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}'
```

Recommended, no CI required:

- Run [gitleaks](https://github.com/gitleaks/gitleaks) locally (`gitleaks protect --staged`), optionally as a pre-commit hook.
- Enable GitHub secret scanning and push protection once the remote exists.

If a secret is ever committed, rotate it first. Removing it from history is secondary, because it must be treated as compromised.

## Front matter

```yaml
---
title: Page title
date: YYYY-MM-DD
category: Development | Infrastructure | Security | Repairs | Automation | Hardware
tags:
  - Example
summary: One or two plain sentences that work as a website card description.
public: false
featured: false
---
```

`summary` should be concise, light on jargon, and say what the work is worth.
