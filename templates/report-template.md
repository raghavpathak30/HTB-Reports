# [Machine / Challenge Name] – HackTheBox Writeup

| Field        | Details                        |
|--------------|--------------------------------|
| **Name**     | Machine / Challenge Name       |
| **OS**       | Linux / Windows / N/A          |
| **Difficulty** | Easy / Medium / Hard / Insane |
| **Category** | Machine / Web Challenge / Crypto / Misc |
| **Points**   | N/A                            |
| **Status**   | ✅ Pwned / 🔄 In Progress       |
| **Date**     | YYYY-MM-DD                     |

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

> Brief 2–3 sentence description of the machine/challenge, the attack path taken, and the main vulnerability exploited.

---

## Reconnaissance

### Port Scan

```bash
nmap -sC -sV -oN nmap/initial.txt <TARGET_IP>
```

**Open Ports:**

| Port | Service | Version |
|------|---------|---------|
| 22   | SSH     | OpenSSH x.x |
| 80   | HTTP    | Apache x.x  |

### Directory / Subdomain Enumeration

```bash
gobuster dir -u http://<TARGET_IP> -w /usr/share/wordlists/dirb/common.txt
ffuf -u http://<TARGET_IP>/FUZZ -w /path/to/wordlist.txt
```

---

## Enumeration

<!-- Describe what you found during enumeration — interesting files, services, 
     credentials, misconfigurations, etc. Include screenshots where relevant. -->

### Web Application

- [ ] robots.txt
- [ ] Source code review
- [ ] Cookie / session analysis
- [ ] Input fields tested for injection

### Service-Specific

<!-- Add sections for each relevant service (FTP, SMB, LDAP, etc.) -->

---

## Exploitation

### Vulnerability

**CVE / OWASP Category:** <!-- e.g., CVE-2021-XXXXX or OWASP A03:2021 – Injection -->

**Description:**  
<!-- Explain the vulnerability and why it exists. -->

### Proof of Concept

```bash
# Commands / scripts used to exploit the target
```

<!-- Insert screenshot of initial shell / flag capture here -->
![Exploit Screenshot](../assets/screenshots/exploit.png)

---

## Post-Exploitation / Privilege Escalation

### Local Enumeration

```bash
# Example: linpeas, sudo -l, SUID binaries, cron jobs, etc.
sudo -l
find / -perm -4000 2>/dev/null
```

### Privilege Escalation Path

<!-- Step-by-step explanation of how root/SYSTEM was obtained -->

```bash
# Commands used for privilege escalation
```

<!-- Screenshot of root/SYSTEM shell -->
![Root Shell](../assets/screenshots/root.png)

---

## Flags

| Flag       | Hash                                      |
|------------|-------------------------------------------|
| User flag  | `[REDACTED – submitted on platform]`      |
| Root flag  | `[REDACTED – submitted on platform]`      |

---

## Key Takeaways

- **What I learned:** <!-- Main technical lesson from this box -->
- **Techniques used:** <!-- e.g., SQL Injection, LFI, Buffer Overflow -->
- **Tools used:** <!-- e.g., Burp Suite, SQLMap, Metasploit -->
- **Mitigations:** <!-- How the vulnerability could have been prevented -->

---

## References

- [HackTheBox – Machine Name](https://app.hackthebox.com/machines/XXXX)
- [Relevant CVE](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-XXXX-XXXXX)
- [OWASP Top 10](https://owasp.org/www-project-top-ten/)

---

*Report by [Raghav Pathak](https://github.com/raghavpathak30) | B.Tech CSE, LNMIIT | Google Cybersecurity Certified*
