---
title: VoltOps
date: 2026-10-03
category: Automation
tags:
  - Operations
  - Python
  - Business tooling
  - Packaging
summary: A desktop app that runs my repair business end to end: jobs, invoices, payments, expenses and reporting, installed as a normal RPM.
public: true
featured: false
---

[Back to projects](README.md)

## Summary

VoltOps (the repair-tracker app) tracks repair jobs through to pickup, generates invoices and receipts as PDFs, records payments and business expenses and produces reports for my accountant. It is the system I use to run Voltage PC Repairs. The source is in a private repository.

## Problem

I was tracking jobs and finances with a mix of tools and paper. I wanted one local app that matched how a small repair shop works and could eventually replace my bookkeeping software.

## Architecture

- A native window (pywebview) around a small HTML, CSS and JavaScript frontend, backed by Python and SQLite.
- Online bookings from a scheduling service arrive through a Cloudflare Worker that queues events for the app to pull, so the app needs no public address.
- Automatic daily database backups, mirrored to a synced folder so they do not live only on the same disk.
- Packaged as an RPM with a normal launcher entry, installed next to a separate development checkout.

## Technologies

Python, pywebview, SQLite, Cloudflare Workers, RPM packaging, a PDF rasteriser for the in-app viewer.

## My role

- Wrote the requirements from running the business: job states, invoicing rules, GST and expense categories, what the accountant needs.
- Chose the architecture and the choice to keep it local-first.
- Package, install and operate it daily, and test it against real work.
- **AI-assisted development:** the code was written with Claude. I reviewed behaviour, caught the bugs below and made the product decisions.

## Key decisions

- **Money as integer cents** everywhere, with one helper doing the only dollars conversion.
- **Additive, guarded schema changes** so re-running the app upgrades an existing database safely.
- **Slow or flaky integrations run isolated** from the UI, so a dead external API cannot hang the app.
- **Own PDF viewer.** The system and browser PDF renderers were not trustworthy for invoices.

## Security considerations

- Customer data stays local. Secrets live in `.env` files kept out of version control.
- Limitation: there is one user and no network login, which is acceptable for a single-machine app.

## Testing and validation

- Exercised against real jobs and invoices. I regenerate and read the PDFs after changes.

## Problems encountered

- A "blurry invoice" bug turned out to be a browser's built-in PDF renderer dropping letters, which led to the in-app viewer.
- A scheduling-service API once accepted connections and then never answered. A shared lock let that hang the whole app. Isolating the integration fixed it.

## Current status

In daily use; expense reporting is the next area.

## Lessons learned

- Do not trust a system component for something that must be right every time.
- A slow dependency needs its own timeout and its own failure state.
