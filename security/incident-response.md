---
title: Signing Key Committed to Version Control
date: 2026-10-03
category: Security
tags:
  - Incident Response
  - Secrets
  - Git
  - Docker
summary: A signing key ended up in a git repository by accident. How I contained it, replaced it, proved the replacement worked and stopped it happening again.
public: true
featured: true
---

A sanitised case study. It covers the approach and the lessons, not the operational details.

## Summary

Key material for a self-hosted e-signature service was committed to a version-controlled infrastructure repository. It arrived inside a manual backup folder, not as a deliberate file. I treated the key as compromised from the moment I found it, replaced it, and made the repository refuse that class of file in future. No customer data was involved.

## Timeline

- **2026-09-25:** the backup folder, containing a private key and certificate, was committed in a work-in-progress commit.
- **2026-10-03:** found during a cleanup of backup folders. The same day I removed it from the working tree, blocked the file types, rotated the key, and tested the signing flow end to end.

## Detection

I found it by reviewing what the repository actually tracked while removing old backup folders. It was not caught automatically. That is the main gap this incident exposed.

## Containment

- Treated the key as compromised once it had been committed, regardless of who could see the repository. Deleting a file does not remove it from history.
- Removed the backup folder so key material no longer sat in the working tree.
- Decided to rotate rather than try to prove nobody had read it.

## Rotation

- Generated a new signing key and certificate and installed them on the service.
- The first two attempts to use the new certificate failed, and neither was a cryptography problem:
  1. **File permissions.** The new file was created readable only by its owner, so the service's process could not read it. Signing stalled quietly instead of failing loudly. The logs showed the permission error immediately.
  2. **Special character in an environment variable.** The certificate password contained a character that Docker Compose interpolates, so the service received a different password than the one the certificate was made with. The service reported an integrity error on the certificate. Escaping the character and recreating the container fixed it. A plain restart does not reload environment values.
- Verified the new certificate independently, then ran the whole signing flow to the point of the payment step. Checking that one command worked was not enough.

## Cleanup

- Removed the backup folder from the tree and stopped keeping manual backups inside the repository.
- Moved another service's secrets out of a tracked config file and into a git-ignored environment file, after noticing the same pattern there.
- Still to do: remove the old key from git history and delete leftover copies on the host. The old key is no longer used, but it stays in history until that is done.

## Prevention

- `.gitignore` now blocks private keys, certificate bundles and backup files by extension and folder name.
- Secrets live in git-ignored environment files. Where possible, the stack refuses to start if a secret is missing, so it cannot fall back to a default login.
- This public journal has a local secret-scanning hook (gitleaks) that blocks commits containing likely secrets. See the [publication guide](../PUBLICATION-GUIDE.md).
- Next step: add the same hook to the infrastructure repository. It is not in place there yet.

## What I learned

- A `.gitignore` entry is a seatbelt, not a detector. It needs a scanner behind it.
- Backups kept inside a working tree get committed. Keep them elsewhere.
- Once a secret is committed, rotate first and clean history second.
- After rotating a credential, test the full workflow that depends on it. The failures here were in configuration around the key, not in the key.
- "Works when I run it by hand" and "works inside the container as the service user" are different tests.
