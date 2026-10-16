---
title: VoltOps
date: 2026-10-03
category: Automation
tags:
  - Operations
  - Python
  - Business tooling
  - Packaging
summary: "A desktop app that runs my repair business end to end: jobs, invoices, payments, expenses and reporting, installed as a normal RPM."
public: true
featured: false
---

[Back to projects](README.md)

## Summary

VoltOps (the repair-tracker app) tracks repair jobs through to pickup, generates invoices and receipts as PDFs, records payments and business expenses and produces reports for my accountant. It is the system I use to run Voltage PC Repairs. The source is in a private repository.

## Problem

I was tracking jobs and finances with a mix of tools. I wanted one local app that matched how a small repair shop works. It has since replaced my bookkeeping software.

## Architecture

- A native window (pywebview) around a small HTML, CSS and JavaScript frontend, backed by Python and SQLite.
- Online bookings from a scheduling service arrive through a Cloudflare Worker that queues events for the app to pull, so the app needs no public address.
- Automatic daily database backups, mirrored to a synced folder so they do not live only on the same disk.
- Packaged as an RPM with a normal launcher entry, installed next to a separate development checkout.
- A second, keyboard-driven terminal interface runs over SSH on the same code and data, for when I am not at the desktop.
- Receipts and invoices can be texted to customers. The PDF is uploaded to the business website and served at an unguessable link that expires after 30 days and can be revoked, and every text is logged.
- A receipt watcher reads photos of receipts with a local vision model and offers the result as a suggestion that I confirm before it becomes an expense. It is local-only on purpose, for privacy and cost.

## Technologies

Python, pywebview, Textual, SQLite, Cloudflare Workers and Pages, an SMS service, RPM packaging.

## My role

- Wrote the requirements from running the business: job states, invoicing rules, GST and expense categories, what the accountant needs.
- Chose the architecture and the choice to keep it local-first.
- Package, install and operate it daily, and test it against real work.
- **AI-assisted development:** the code was written with Claude. I reviewed behaviour, caught the bugs below and made the product decisions.

## Key decisions

- **Money as integer cents** everywhere, with one helper doing the only dollars conversion.
- **Additive, guarded schema changes** so re-running the app upgrades an existing database safely.
- **Slow or flaky integrations run isolated** from the UI, so a dead external API cannot hang the app.
- **Hand PDFs to the system viewer.** An in-app viewer was built and then removed after it caused a memory leak (see below). Setting a reliable system default fixed the original problem more simply.

## Security considerations

- Customer data stays local. Secrets live in `.env` files kept out of version control, are masked in the interface and the files are owner-only.
- Texted links are long and unguessable, expire after 30 days and can be revoked early.
- The app refuses overpayments, voids issued invoices and receipts instead of deleting them, and never reuses a document number.
- The window has a content security policy, and only one copy can run against a data folder.
- A browser version served behind my private proxy was built and then retired on 15/10, so the app runs on the desktop only.
- Limitation: there is one user and no network login, which is acceptable for a single-machine app.

## Testing and validation

- An automated test suite was added in the 15/10 audit pass: about a hundred backend checks, plus interface checks in a headless browser and against the real window. A run of the suite, and a rehearsed restore of a backup, were part of that pass.
- I still exercise changes against real jobs and invoices and read the generated PDFs, because that is where the earlier bugs showed up.

## Problems encountered

- A "blurry invoice" bug turned out to be a browser's built-in PDF renderer dropping letters. I built an in-app viewer to avoid it, but a second app window leaked memory without limit (a platform bug, confirmed with a minimal reproduction). I removed the viewer and fixed the system default PDF handler instead.
- A scheduling-service API once accepted connections and then never answered. A shared lock let that hang the whole app. Isolating the integration fixed it.

## Current status

In daily use, with the most active development in October 2026. See [VoltOps, texted receipts and an audit pass](../journal/2026/2026-10-15.md). The long-term goal is for it to replace my remaining business tools. Accounting software has already been replaced.

## Lessons learned

- Anything that sends a message to a customer needs a way to take it back, and a record of exactly what was sent.
- Do not trust a system component for something that must be right every time, and be ready to back out a fix that causes a worse problem.
- A slow dependency needs its own timeout and its own failure state.
