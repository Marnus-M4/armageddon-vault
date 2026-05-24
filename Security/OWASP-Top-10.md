# OWASP Top 10 — 2021

> The OWASP Top 10 is the standard awareness document for web application security risks.

---

## A01 — Broken Access Control
**What it is:** Users can act outside their intended permissions.

**Examples:**
- Accessing another user's account by modifying the URL (`/user/123` → `/user/124`)
- Privilege escalation (regular user accessing admin endpoints)
- CORS misconfiguration allowing unauthorised API access

**How to test:**
```bash
# Test IDOR (Insecure Direct Object Reference)
curl -H "Authorization: Bearer <user_token>" https://target.com/api/users/1
curl -H "Authorization: Bearer <user_token>" https://target.com/api/users/2
```

**Mitigation:** Enforce server-side access controls, deny by default, log access failures.

---

## A02 — Cryptographic Failures
**What it is:** Sensitive data exposed due to weak or missing encryption.

**Examples:**
- Passwords stored in plain text or MD5/SHA1
- Sensitive data transmitted over HTTP
- Weak TLS configuration

**How to test:**
```bash
# Check SSL/TLS config
nmap --script ssl-enum-ciphers -p 443 target.com
testssl.sh target.com
```

**Mitigation:** Use bcrypt/Argon2 for passwords, enforce TLS 1.2+, encrypt sensitive data at rest.

---

## A03 — Injection
**What it is:** Untrusted data sent to an interpreter as part of a command or query.

**Types:** SQL, NoSQL, OS command, LDAP, XPath injection.

**SQL Injection examples:**
```sql
-- Basic auth bypass
' OR '1'='1
' OR 1=1--
admin'--

-- UNION-based data extraction
' UNION SELECT username, password FROM users--

-- Time-based blind
'; IF (1=1) WAITFOR DELAY '0:0:5'--
```

**Tools:**
```bash
sqlmap -u "https://target.com/page?id=1" --dbs
sqlmap -u "https://target.com/page?id=1" -D dbname --tables
```

**Mitigation:** Parameterised queries, prepared statements, input validation.

---

## A04 — Insecure Design
**What it is:** Missing or ineffective security controls at the design level.

**Examples:**
- No rate limiting on login → brute force possible
- Password reset via secret questions
- No multi-factor authentication for sensitive operations

**Mitigation:** Threat modelling during design phase, security design patterns.

---

## A05 — Security Misconfiguration
**What it is:** Improperly configured security settings.

**Common issues:**
- Default credentials unchanged (`admin/admin`)
- Directory listing enabled
- Verbose error messages exposing stack traces
- Unnecessary ports/services open
- Cloud storage buckets publicly accessible

**How to test:**
```bash
# Check for default creds
hydra -L users.txt -P passwords.txt target.com http-post-form "/login:user=^USER^&pass=^PASS^:F=incorrect"

# Directory enumeration
gobuster dir -u https://target.com -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
```

---

## A06 — Vulnerable and Outdated Components
**What it is:** Using components with known vulnerabilities.

**Tools:**
```bash
# Check for known CVEs
npm audit
pip-audit
trivy image myapp:latest

# Search CVEs
searchsploit apache 2.4.49
```

**Mitigation:** Regularly update dependencies, subscribe to security advisories.

---

## A07 — Identification and Authentication Failures
**What it is:** Weaknesses in authentication and session management.

**Examples:**
- Weak passwords allowed
- No account lockout → brute force
- Session tokens exposed in URL
- Insecure "remember me" functionality

**How to test:**
```bash
# Brute force login
hydra -l admin -P /usr/share/wordlists/rockyou.txt target.com http-post-form "/login:username=^USER^&password=^PASS^:Invalid"
```

---

## A08 — Software and Data Integrity Failures
**What it is:** Code and infrastructure not protected against integrity violations.

**Examples:**
- Insecure CI/CD pipeline
- Auto-updating software without integrity verification
- Deserialisation of untrusted data

---

## A09 — Security Logging and Monitoring Failures
**What it is:** Insufficient logging allows attackers to go undetected.

**What to log:**
- Failed login attempts
- Access control failures
- Input validation failures
- High-value transactions

**Mitigation:** Centralised logging (ELK stack), alerting on suspicious patterns, audit trails.

---

## A10 — Server-Side Request Forgery (SSRF)
**What it is:** Server makes requests to unintended locations based on user input.

**Examples:**
```bash
# Basic SSRF - access internal services
https://target.com/fetch?url=http://169.254.169.254/latest/meta-data/
https://target.com/fetch?url=http://localhost:8080/admin
https://target.com/fetch?url=file:///etc/passwd
```

**Mitigation:** Whitelist allowed domains, block internal IP ranges, disable unnecessary URL schemes.

---

## Quick Reference Checklist

- [ ] A01: Test all object references with different user tokens
- [ ] A02: Check encryption in transit and at rest
- [ ] A03: Test all inputs for injection
- [ ] A04: Review authentication flows for logic flaws
- [ ] A05: Check for default configs, exposed admin panels
- [ ] A06: Run dependency vulnerability scanner
- [ ] A07: Test brute force protection and session management
- [ ] A08: Review update mechanisms and deserialisation
- [ ] A09: Verify logging is capturing security events
- [ ] A10: Test URL parameters that fetch remote resources
