---
title: Network-Wide Encrypted DNS with AdGuard Home and Let's Encrypt
date: 2026-11-06
category: Infrastructure
tags:
  - DNS
  - Encryption
  - AdGuard Home
  - Docker
  - Let's Encrypt
  - DoT
  - DoH
  - Security
  - Home Network
summary: Configured AdGuard Home with Let's Encrypt certificates to provide encrypted DNS (DoT/DoH) across all network devices, replacing unencrypted ISP DNS with privacy-focused, ad-filtered DNS resolution.
public: true
featured: false
---

[Back to journal index](../README.md)

> [!NOTE]
> **In short:** I set up network-wide encrypted DNS using AdGuard Home running in Docker. The service uses Let's Encrypt wildcard certificates for DNS-over-TLS and DNS-over-HTTPS, with AdGuard DNS as the upstream resolver. All devices on the network now receive encrypted, filtered DNS without ads or trackers. Verified with dnsleaktest.com showing privacy-focused upstream DNS and 100% ad blocker effectiveness.

New to some of the terms? The [glossary](../../GLOSSARY.md) explains them in plain English.

## What I worked on

Set up a complete encrypted DNS infrastructure for the home network to replace unencrypted ISP DNS. The goal was to:

1. Encrypt all DNS traffic (privacy from ISP)
2. Block ads and trackers network-wide
3. Support multiple device types (iOS, Android, Linux, Google TV)
4. Use existing Caddy reverse proxy infrastructure and Let's Encrypt certificates

## What changed

**DNS resolution path**

- **Before:** Devices → Router → ISP DNS (unencrypted, no filtering)
- **After:** Devices → AdGuard Home (local filtering) → AdGuard DNS (encrypted upstream)

**Encryption configuration**

- DNS-over-TLS (DoT)
- DNS-over-HTTPS (DoH)
- Certificates: Let's Encrypt wildcard certificate

**Device configuration**

| Device | Method | Status |
| --- | --- | --- |
| iPhone/iPad | Wi-Fi DNS settings → encrypted DNS hostname | ✅ Working |
| Android phones | Private DNS → server IP address | ✅ Working |
| Linux machines | systemd-resolved with DoT upstream | ✅ Working |
| Google TVs | Network DNS via DHCP | ✅ Filtering (unencrypted) |

**Upstream DNS**

Changed from Cloudflare public DNS to AdGuard DNS over HTTPS for privacy-focused upstream resolution.

## Prerequisites and setup

**Required infrastructure**

- Docker with docker-compose
- Caddy reverse proxy with Let's Encrypt certificates
- AdGuard Home container
- Network with configurable DHCP/DNS

**Discovery: Finding Let's Encrypt certificates**

Used systemd and file search commands to locate existing Let's Encrypt certificates managed by Caddy reverse proxy.

**Docker configuration**

Updated docker-compose to mount certificate directory into AdGuard Home container with read-only access.

**Certificate chain validation**

AdGuard Home requires the complete certificate chain (server cert + intermediates + root). Initial configuration failed due to incomplete chain. Downloaded Let's Encrypt root certificate and combined with existing certificates to create a complete chain file.

**AdGuard Home encryption setup**

In AdGuard Home admin panel (Settings → Encryption):

1. Enable HTTPS/TLS encryption
2. Set certificate path to complete chain file
3. Set private key path
4. Configure DoT port 853 and DoH port 8443
5. Add DNS rewrite for local hostname resolution

**Upstream DNS configuration**

In AdGuard Home (Settings → DNS settings → Upstream servers):

Changed to privacy-focused DNS over HTTPS upstream provider.

**Device configuration**

*iPhone/iPad:*
- Settings → Wi-Fi → Configure DNS → Manual
- Add local hostname with encrypted DNS option
- Encryption mode: Automatic

*Android:*
- Settings → Network & internet → Private DNS
- Enter server address

*Linux machines:*
- Configure systemd-resolved to use local DNS server
- Restart DNS service

**Browser configuration**

Switched web browsers from uBlock Origin to AdGuard DNS-based filtering:

- Browser ad blockers disabled (redundant with DNS filtering)
- Rely on network-wide DNS filtering instead
- Reduces browser resource consumption

## Verification and testing

**DNS leak test:**

- Verified all DNS queries resolve through privacy-focused upstream provider
- No leaks to public or ISP DNS providers

**Ad blocker effectiveness:**

- All devices showed 100% blocking rate
- Effective tracking and ad domain blocking across network

## Common issues and solutions

**"Certificate chain is invalid" error**

Problem: Certificate file only contained server cert, not full chain.
Solution: Create complete chain file with intermediates and root certificate.

**"No such file or directory" in AdGuard Home**

Problem: Certificate paths didn't exist in container.
Solution: Verify Docker volume mounts and use container-relative paths (`/certs/...`).

**Android Private DNS "couldn't connect"**

Problem: Hostname-based Private DNS had validation issues despite working via IP.
Solution: Use server IP address directly in Private DNS instead of hostname.

**Port 853 unreachable**

Problem: Initially appeared to be firewall issue.
Solution: Verified port was listening (`sudo ss -tuln | grep 853`) and connection worked with proper certificate chain.

## Safety and privacy considerations

- **Encryption:** All DNS queries to upstream are encrypted (DoH)
- **No logs shared:** AdGuard DNS provides privacy-focused upstream
- **Local filtering:** Ad/tracker blocking happens on local network
- **Certificate validation:** Proper certificate chain prevents man-in-the-middle attacks
- **No IP leakage:** DNS leak tests confirm queries don't leak to alternative DNS providers

## Decisions made

- **Docker-based AdGuard Home** for easy management and isolation
- **Let's Encrypt certificates** to avoid device warnings and ensure trust
- **Privacy-focused DNS upstream** instead of commercial providers
- **Private DNS on Android via IP** to avoid hostname resolution issues
- **Network-wide DHCP DNS** for automatic device coverage
- **DNS filtering over browser extensions** to reduce resource usage and centralize control

## What I learned

- Complete certificate chains are critical: intermediate + root certificates must be included
- Docker volume mounts require container-relative paths, not host paths
- Android's Private DNS is very strict about certificate validation; hostname resolution can be finicky
- DNS-over-TLS vs DNS-over-HTTPS have different use cases; DoT for strict privacy, DoH for compatibility
- Network-wide DNS filtering is more efficient than per-device browser extensions
- A single encrypted upstream DNS provider is better than multiple public DNS options for privacy
