---
title: Automated Torrent Seedbox with Safe Manual Import Gate
date: 2026-11-05
category: Infrastructure
tags:
  - Bash
  - Automation
  - qBittorrent
  - systemd
  - Linux
  - Seedbox
  - Scripting
summary: Built a split-script torrent discovery and import system that runs unattended fetches in the background while keeping the manual import step interactive, with systemd timer automation, ntfy notifications, and a structured safety model.
public: true
featured: false
---

[Back to journal index](../README.md)

> [!NOTE]
> **In short:** I built an automated torrent seedbox setup for the Voltage Linux Seeding Project. The system uses two scripts: one that runs unattended on a timer to discover and download `.torrent` files, and another that you run manually to import them into qBittorrent with a password prompt. This keeps background jobs safe while preserving the human gate you asked for. The scripts support optional ntfy notifications, systemd timer automation, and a structured file layout with active and archived torrents.

New to some of the terms? The [glossary](../../GLOSSARY.md) explains them in plain English.

## What I worked on

The seedbox needed automated discovery of new Linux torrent files, but you wanted a manual step before anything hit qBittorrent. I built it in two pieces, each with one job:

| Script | What it does |
| --- | --- |
| `seedbox-fetch.sh` | Discover and download new `.torrent` files from a source (default: Ubuntu releases). Runs unattended on a systemd timer every 2 hours. Sends ntfy notification only when new torrents arrive. Cannot log into qBittorrent. |
| `seedbox-import.sh` | Read downloaded torrents from the work folder, prompt for qBittorrent username/password, import each torrent into the configured category, then archive the `.torrent` files. Always requires your input. |

The split keeps your safety gate intact: fetch runs in the background with no secrets, but nothing gets into qBittorrent until you approve it.

## What changed

**The file layout**

- Active/new torrent files: `~/storage/torrents/active`
- Archived imported files: `~/storage/torrents/active/archive`

**Fetch automation**

- systemd user timer (`seedbox-fetch.timer`) runs every 2 hours.
- Service unit (`seedbox-fetch.service`) calls the fetch script.
- Environment file (`~/.config/seedbox.env`) holds optional settings:
  - `NTFY_SERVER` (example: `https://ntfy.example.com`)
  - `NTFY_TOPIC` (example: `seedbox-notifications`)
  - `NTFY_TOKEN` (bearer token if required)

**The fetch step**

1. Curl the source URL (Ubuntu releases by default).
2. Parse HTML with grep/sed to extract `.torrent` links.
3. De-duplicate against already-discovered URLs.
4. Check against existing files in both active and archive folders.
5. Download only new files.
6. Send ntfy notification only if something was downloaded.

**The import step**

1. Read `.torrent` files from the work folder.
2. Prompt for qBittorrent URL, username, password (stored only in memory during the session).
3. Authenticate with qBittorrent API `/api/v2/auth/login`.
4. Post each torrent with the configured category (default: `Voltage Linux Seeding Project`).
5. Move each torrent to the archive folder.
6. Send a summary notification with counts.

## Prerequisites and setup

**Required commands** (installed on most systems):
- bash, curl, grep, sed, sort, jq (for import)

On Debian/Ubuntu:
```bash
sudo apt update
sudo apt install -y curl jq grep sed coreutils
```

**Make scripts executable:**
```bash
chmod +x seedbox-fetch.sh seedbox-import.sh
```

**Manual test flow:**
```bash
# Discover new torrents
./seedbox-fetch.sh

# If new torrents were found, import them
./seedbox-import.sh
```

**Systemd timer setup** (recommended):
```bash
# Create user systemd directory if needed
mkdir -p ~/.config/systemd/user

# Copy units
cp seedbox-fetch.service ~/.config/systemd/user/
cp seedbox-fetch.timer ~/.config/systemd/user/

# Optional: create environment file
cat > ~/.config/seedbox.env << EOF
NTFY_SERVER=https://ntfy.example.com
NTFY_TOPIC=seedbox-notifications
# NTFY_TOKEN=...
EOF

# Enable and start
systemctl --user daemon-reload
systemctl --user enable --now seedbox-fetch.timer

# Verify
systemctl --user status seedbox-fetch.timer
systemctl --user list-timers | grep seedbox-fetch
```

**View service logs:**
```bash
journalctl --user -u seedbox-fetch.service -n 100 --no-pager
```

**Cron alternative** (if you prefer not to use systemd):
```cron
0 */2 * * * /path/to/seedbox-fetch.sh >> /path/to/seedbox-fetch.log 2>&1
```

## Runtime configuration

**Environment variables for fetch:**
- `NTFY_SERVER` (optional): ntfy server URL
- `NTFY_TOPIC` (optional): notification topic
- `NTFY_TOKEN` (optional): bearer token for auth

**Environment variables for import:**
- `QBIT_URL` (default: `https://qbittorrent.internal.local`)
- `CATEGORY` (default: `Linux Seedbox Project`)

**Example temporary override:**
```bash
CATEGORY="Custom Category" ./seedbox-import.sh
```

## Safety model

- **Fetch runs unattended.** No network logins, no secrets, safe to run on a timer or from cron.
- **Import always prompts.** You type the password every time. This is your gate.
- **Secrets never in config files.** Password is typed at the prompt, held in memory only during the session, then forgotten.
- **De-duplication and checks.** The fetch script checks for existing files by name and avoids duplicate URLs, so re-running it is safe.
- **Graceful failures.** If fetch finds nothing, the script exits cleanly with no error. If import fails partway, torrents stay in the work folder for the next attempt.

## Common troubleshooting

**Fetch finds no torrents:**
- Source website changed HTML layout
- Source page uses JavaScript or an API instead of HTML
- Network/CDN issue

Check manually:
```bash
curl -fsSL "https://releases.ubuntu.com/" | grep -i torrent
```

**qBittorrent login fails:**
- `QBIT_URL` is incorrect or unreachable
- Username/password incorrect
- Reverse proxy/certificate/SSO interfering with `/api/v2/auth/login`

Test manually:
```bash
curl -I https://qbittorrent.internal.local
```

**systemd timer does not run:**
```bash
systemctl --user daemon-reload
systemctl --user enable --now seedbox-fetch.timer
journalctl --user -u seedbox-fetch.service -n 100 --no-pager
```

**ntfy notifications not arriving:**
Check environment variables and test manually:
```bash
curl -H "Title: test" -d "hello from seedbox" "https://ntfy.example.com/seedbox-notifications"
```

**Duplicate torrents or filename collisions:**
The fetch script de-duplicates by URL and checks existing files in both active and archive folders. If two different releases share the same filename, only the first download is kept by the filename check.

**Permission errors writing to storage path:**
```bash
ls -ld "$HOME/storage/torrents/active"
```
Ensure the running user can create files and directories there.

## Decisions

- **Split into two scripts.** Unattended tasks cannot prompt for passwords, so the gate lives in the import script. The fetch script has nothing secret to handle.
- **Prefer systemd timer over cron.** Cleaner logs, better integration with the user session, easier to restart and debug.
- **Environment file for secrets, not defaults in script.** Keeps configuration out of the repository.
- **De-duplicate by URL and by filename.** Catches both the same release downloaded twice and different releases with identical names.
- **Archive, do not delete.** Keeps a record and avoids re-importing if the import fails partway.
- **ntfy notifications only on events.** Fetch only notifies when something arrived; import sends a summary. Reduces noise if you only care about successes.

## What I learned

- Split responsibilities make scripts easier to reason about and safer to automate.
- A password prompt is the simplest and clearest gate when you want manual control.
- De-duplication needs to happen at two levels: the URL level (was this source URL already fetched?) and the file level (does a torrent with this name already exist?).
- systemd timer is more powerful than cron for this use case: cleaner logging, easier to debug, and plays well with user environment variables.
- Graceful degradation is important: if the source changes format, the script should exit cleanly, not error, so it does not break cron or systemd notifications.

