# Week 01 — Advanced Recon & Attack Surface Mapping

<p align="center">
  <img src="https://img.shields.io/badge/Phase-Reconnaissance-purple?style=for-the-badge">
  <img src="https://img.shields.io/badge/Focus-Passive%20OSINT-blue?style=for-the-badge">
  <img src="https://img.shields.io/badge/Week-01-orange?style=for-the-badge">
</p>

> *"Given eleven minutes to assess a target, spend ten on recon."*

Before a single payload drops, there's reconnaissance. No scanning, no noise — just passive intelligence that leaves zero traces in any log. This week is about building a complete picture of the target before touching anything.

---

## What We're Actually Doing

Most people jump straight to scanning. That's a mistake. Real recon means understanding the target's infrastructure, their tech choices, their dev habits, and their blind spots — all without sending a single suspicious packet.

By the end of this week you'll have:

- A full subdomain map including forgotten staging servers and legacy endpoints
- Infrastructure details: IP ranges, ASNs, hosting providers, name servers
- A technology fingerprint — frameworks, libraries, server versions
- Any credentials or keys accidentally pushed to public repos
- Screenshots of every live asset worth looking at

---

## The Recon Workflow

### Task 01 — Subdomain Discovery

No single tool gives the full picture. Run several and cross-reference.

| Tool | What it does |
|------|--------------|
| `Subfinder` | Passive scraping across 30+ data sources |
| `Assetfinder` | Lightweight, good at catching what others miss |
| `Amass` | Correlates across 50+ sources, builds relationship graphs |
| `crt.sh` | Pulls subdomains from SSL certificate transparency logs |
| `DNSDumpster` | Maps DNS relationships visually |

SSL certificates are particularly useful — they log every domain a cert was issued for, including internal ones companies never meant to expose.

---

### Task 02 — Infrastructure Profiling

Once you know what exists, figure out how it's built.

- **WhatWeb** fingerprints HTTP responses — headers, cookies, framework signatures
- **WHOIS** reveals registrant info, name servers, and IP ownership history
- **NSLookup** maps the full DNS architecture (A, MX, NS, TXT records)
- **Google Dorks** surface files and directories that were never meant to be indexed

A misconfigured S3 bucket or an exposed `.env` file can hand over everything. These get found through dorks, not scanners.

---

### Task 03 — GitHub Recon

Developers push sensitive data to public repos more often than anyone admits. Git history is permanent — a credential committed three years ago and deleted the next day is still sitting in the commit log.

Hunting for:
- `API_KEY`, `SECRET`, `PASSWORD` strings in repos and commit history
- `.env` files that got pushed by mistake
- Database connection strings buried in old config files
- Internal hostnames and IP addresses referenced in source code

`GitHacker` can reconstruct entire repositories from misconfigured `.git` directories exposed on web servers — full commit history included.

---

### Task 04 — Visual Recon

A list of subdomains is only useful if you know which ones are alive. Run everything through:

- **Httpx** to filter live assets from dead ones
- **Gowitness** to screenshot every live page automatically

The output is a visual map of the entire web-facing attack surface — login panels, admin dashboards, dev environments, forgotten portals. Reviewing screenshots takes ten minutes and saves hours of blind probing.

---

## Deliverables

| Output | Description |
|--------|-------------|
| Executive Summary | What's out there, what matters, what looks vulnerable |
| Asset List | CSV of every subdomain, IP, netblock, and ASN discovered |
| Technology Map | Full stack fingerprint — servers, frameworks, libraries |
| Findings | Exposed `.git` directories, open buckets, leaked credentials |

---

## Tools Reference

| Tool | Strength |
|------|----------|
| `Amass` | Best for correlation and graph-based discovery |
| `crt.sh` | Finds subdomains that never appear in DNS |
| `WhatWeb` | Identifies 1800+ technologies from a single request |
| `GitHacker` | Reconstructs repos from exposed `.git` folders |

```
crt.sh + DNSDumpster     →   historical subdomains
Subfinder + Amass        →   current subdomains
Httpx                    →   filter live assets
WhatWeb + Gowitness      →   tech fingerprint + screenshots
GitHub dorks             →   leaked credentials
```

---

## How to Know You've Done It Right

- Found subdomains not listed in any public DNS record
- Mapped IP ranges back to specific teams or locations
- Identified exact version numbers of frameworks running in production
- Found at least one exposed credential or misconfiguration
- Could draw a full network diagram without ever touching the target

---

## Moving Into Week 02

Everything found this week feeds directly into next week. The subdomains become target lists. The tech stack becomes a CVE research checklist. The leaked credentials become initial access attempts.

Recon quality determines exploitation quality. Skip this and you're just guessing.

---

<p align="center">
  <img src="https://img.shields.io/badge/Status-Learning%20in%20Progress-yellow?style=flat-square">
  <img src="https://img.shields.io/badge/Phase-Passive%20Recon-blue?style=flat-square">
  <img src="https://img.shields.io/badge/Next-Weaponization-red?style=flat-square">
</p>
