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
- [ ] Review every screenshot, including browser tabs, URLs and notifications (see `assets/README.md`)
- [ ] Strip image metadata and prefer cropped or redrawn diagrams over full-screen captures
- [ ] Remove customer identifiers
- [ ] Sanitise addresses and IPs
- [ ] Check config examples are generic and contain no real values
- [ ] Read the full `git diff --staged`
- [ ] Confirm no private keys or certificates are staged
- [ ] Confirm the content is suitable for a public repo

## Secret scanning

Scanning runs locally in a pre-commit hook using [gitleaks](https://github.com/gitleaks/gitleaks). There is no CI.

One-time setup per clone:

1. Install gitleaks <https://github.com/gitleaks/gitleaks#installing>.
2. Enable the tracked hook: `git config core.hooksPath .githooks`

The hook scans staged changes and refuses the commit if anything matches. It **fails closed**: if gitleaks is not installed, commits are refused. `.gitleaks.toml` keeps the default rules and adds rules for private/Tailscale IPv4 addresses and `.ts.net` hostnames.

Handling false positives: fix the content if you can. Otherwise add a narrow allowlist entry (specific path and pattern) to `.gitleaks.toml` with a comment saying why it is safe. Do not use `--no-verify`, and do not disable rules. No allowlist is currently needed, including for this guide.

Run a full scan of history at any time: `gitleaks git --redact --config .gitleaks.toml`

Also enable GitHub secret scanning and push protection on the remote (Settings → Code security).

If a secret is ever committed, rotate it first. Removing it from history is secondary, because it must be treated as compromised.

## Voice and plain language

The journal is read by technical people and by people who are not into computers. Write for both.

**Voice.** Like a tradie explaining the job to a curious customer: first person, confident, friendly and honest about what went wrong. Short sentences and Australian spelling.

| Do | Avoid |
| --- | --- |
| Say why it mattered: "I use this every night, so it had to work on the tablet." | Hype words: "amazing", "game-changer", "blazingly fast" |
| Name a feeling once in a while: "This one annoyed me for a day." | Jokes, memes or emoji that will date badly |
| Use an everyday comparison for a hard idea | Sarcasm, or blaming tools |
| Keep the honesty: "I do not know which fix worked." | Over-claiming: "I built" when Claude wrote the code |
| Open a problem with the symptom, then the cause | Long technical paragraphs before the reader knows why it matters |

Professional line: a hiring manager should be able to read any paragraph aloud in an interview without me wincing.

**Every entry has an "In short" box** straight after the "Back to journal index" link: three or four sentences with no jargon at all, saying what I did, why, and what is better now. Follow it with the glossary line:

```markdown
> [!NOTE]
> **In short:** Three or four plain sentences.

New to some of the terms? The [glossary](../../GLOSSARY.md) explains them in plain English.
```

**Explain a term the first time it appears** in an entry, in a few words inside the sentence: "17 pull requests (proposed changes I review and approve before they go in)". Later mentions stay short. Terms that keep coming up belong in [`GLOSSARY.md`](GLOSSARY.md), in plain words, one or two sentences each.

**Tell the best problem as a story.** For the one or two problems per entry worth it: the symptom as the heading, what it was like, the cause in plain words, then the fix in short labelled steps. Smaller problems stay as short bullets.

**Keep the tables and the detail.** The "In short" box is the way in for non-technical readers; the rest of the entry stays useful for technical ones.

The publication rules above still apply to every word, including jokes and asides: no hostnames, addresses or port numbers.

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
