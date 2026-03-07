# Red Team: Operation Digital Shadow

<p align="center">
  <img src="https://readme-typing-svg.demolab.com?font=Fira+Code&weight=600&size=28&duration=3000&pause=1000&color=FF0000&center=true&vCenter=true&width=600&lines=RED+TEAM+OPERATIVE;ZERO+TRACE.+MAXIMUM+INTEL.;YOUR+FOOTPRINT+IS+OUR+FOOTHOLD" alt="Typing SVG" />
</p>

<p align="center">
  <img src="https://img.shields.io/badge/PHASE-RECONNAISSANCE-ff0000?style=for-the-badge&logo=eye&logoColor=white">
  <img src="https://img.shields.io/badge/MODE-PASSIVE-800080?style=for-the-badge&logoColor=white">
  <img src="https://img.shields.io/badge/STATUS-OPERATIONAL-00ff00?style=for-the-badge&logoColor=white">
</p>

> *"Given eleven minutes to assess a target, spend ten on reconnaissance."*

---

## Why This Repo Exists

Most people think hacking looks like the movies — hooded figure, rapid keystrokes, firewall exploding on screen.

The reality is quieter. And far more dangerous.

The real damage happens before a single exploit runs. It happens when someone finds the `.git` folder a developer forgot to restrict. When a three-year-old commit reveals a password that was "temporary." When a staging server, invisible to the company's own security team, is running a framework with a CVE from 2021.

Nobody broke in. The door was already open. They just found it first.

This repo is a full three-month red team operation — documented, structured, and built for people who want to understand how professional adversaries actually operate. Not CTF tricks. Not script-kiddie one-liners. The real methodology, from first OSINT query to domain admin.

---

## The Three Months

### Month 01 — Reconnaissance: Learning to See

This is the part most people skip. It's also the part that determines everything else.

Before touching the target, you map it. Not with scanners — with patience. DNS records, SSL certificate logs, GitHub commit history, job postings that accidentally reveal internal tech stacks. By the time month one is over, you'll know the target's infrastructure better than most of their own engineers do.

What gets covered:
- **Passive OSINT** — building a full asset map without generating a single log entry
- **Infrastructure Profiling** — identifying hosting providers, IP ranges, framework versions
- **GitHub Dorking** — hunting API keys, hardcoded secrets, and the commits developers wish they could delete
- **Visual Recon** — screenshotting every live subdomain to find the login panels nobody locked down

The goal isn't just a list of subdomains. It's understanding the shape of the target — where the edges are, where the gaps are, where no one is watching.

---

### Month 02 — Enumeration & Initial Access: From Observer to Operator

Month two is where passive becomes active, and observation becomes interaction.

Every open port is a question. Every service banner is an answer. The version number on that web server maps to a CVE. That CVE has a working exploit. Suddenly a recon finding becomes a shell.

What gets covered:
- **Stealth Scanning** — the difference between getting blocked and getting in is noise level
- **Service Fingerprinting** — version numbers are confessions
- **Initial Access** — from the first whoami to a stable foothold
- **Privilege Escalation** — the climb from low-privilege user to full system control

One machine owned is not the objective. It's the starting point.

---

### Month 03 — Active Directory: Taking the Kingdom

Corporate networks run on Windows domains. Windows domains run on Active Directory. Active Directory, when you know what to look for, is a map to everything.

BloodHound draws the path from a regular user account to Domain Admin. Kerberos hands out tickets that can be cracked offline. A Golden Ticket gives you access that survives password resets. DCSync pulls every credential hash in the domain in seconds.

What gets covered:
- **AD Enumeration** — understanding the structure before attacking it
- **Kerberos Attacks** — AS-REP Roasting, Kerberoasting, ticket forgery
- **Golden Tickets** — persistence that doesn't care about remediation
- **DCSync** — when you want all the hashes, not just one

By the end of month three, the network isn't the target's anymore.

---

## The Arsenal

| Tool | What It Does |
|------|-------------|
| `Amass` | Maps subdomains by correlating 50+ passive sources |
| `crt.sh` | SSL certificate logs — finds domains that DNS doesn't show |
| `WhatWeb` | Fingerprints 1800+ technologies from a single HTTP request |
| `BloodHound` | Visualizes Active Directory attack paths |
| `Mimikatz` | Extracts credentials from Windows memory |
| C2 Frameworks | Manages shells and post-exploitation operations |

---

## What Gets Produced

Every week ends with documented intelligence — not just tool output dumped into a folder, but structured findings that tell a story.

```
Reconnaissance Reports/
├── Asset_List.csv          # Every subdomain, IP, and netblock
├── Technology_Map.md       # The full stack, version by version
├── Security_Gaps.md        # Misconfigs, exposed files, leaked credentials
└── Executive_Summary.pdf   # The findings that matter, clearly explained
```

---

## The Part That Doesn't Get Talked About Enough

Tools are easy. Anyone can run Amass and get a subdomain list.

The actual skill is in the analysis. Knowing that the subdomain pointing to an old S3 bucket might have public read access. Recognizing that the TLS certificate on an internal staging server just revealed three hostnames that aren't in any DNS record. Understanding that the job posting asking for "experience with CyberArk" tells you exactly what PAM solution they're running.

This is what separates a scanner from an operator. The methodology here is built around that gap.

---

## Getting Started

```bash
git clone https://github.com/yourusername/red-team-recon.git
cd red-team-recon
cat mindset.txt
```

---

<p align="center">
  <img src="https://img.shields.io/badge/Always_Learning-000000?style=flat-square&logoColor=00FF00">
  <img src="https://img.shields.io/badge/Never_Detected-FF0000?style=flat-square&logoColor=white">
  <img src="https://img.shields.io/badge/RED_TEAM_FOR_LIFE-800080?style=flat-square&logoColor=white">
</p>

<p align="center">
  <b>Your target is out there. Their secrets are too.</b><br>
  <sub>No traces. No alerts. Just intel.</sub>
</p>
