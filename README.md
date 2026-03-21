<div align="center">

```
██╗  ██╗████████╗██████╗      ██████╗ ███████╗██████╗  ██████╗ ██████╗ ████████╗███████╗
██║  ██║╚══██╔══╝██╔══██╗     ██╔══██╗██╔════╝██╔══██╗██╔═══██╗██╔══██╗╚══██╔══╝██╔════╝
███████║   ██║   ██████╔╝     ██████╔╝█████╗  ██████╔╝██║   ██║██████╔╝   ██║   ███████╗
██╔══██║   ██║   ██╔══██╗     ██╔══██╗██╔══╝  ██╔═══╝ ██║   ██║██╔══██╗   ██║   ╚════██║
██║  ██║   ██║   ██████╔╝     ██║  ██║███████╗██║     ╚██████╔╝██║  ██║   ██║   ███████║
╚═╝  ╚═╝   ╚═╝   ╚═════╝      ╚═╝  ╚═╝╚══════╝╚═╝      ╚═════╝ ╚═╝  ╚═╝   ╚═╝   ╚══════╝
```

# 🔐 HackTheBox — Exploit Reports & Writeups

[![HTB Profile](https://img.shields.io/badge/HackTheBox-Profile-9FEF00?style=for-the-badge&logo=hackthebox&logoColor=black)](https://app.hackthebox.com)
[![Google Cybersecurity](https://img.shields.io/badge/Google-Cybersecurity%20Certified-4285F4?style=for-the-badge&logo=google&logoColor=white)](https://grow.google/certificates/cybersecurity/)
[![Web Pentesting Path](https://img.shields.io/badge/Web%20Pentesting%20Path-93%25%2B-9FEF00?style=for-the-badge&logo=hackthebox&logoColor=black)](https://app.hackthebox.com)
[![B.Tech CSE](https://img.shields.io/badge/LNMIIT-B.Tech%20CSE-blue?style=for-the-badge)](https://www.lnmiit.ac.in/)

*Professional penetration testing reports covering enumeration, exploitation, privilege escalation, and OWASP Top 10 vulnerabilities.*

</div>

---

## 📑 Table of Contents

1. [About](#-about)
2. [Repository Structure](#-repository-structure)
3. [Machines](#-machines)
4. [Web Challenges](#-web-challenges)
5. [Walkthroughs](#-walkthroughs)
6. [Tools & Skills](#-tools--skills)
7. [Report Template](#-report-template)
8. [Disclaimer](#-disclaimer)

---

## 👤 About

> **Raghav Pathak** — B.Tech CSE student at LNMIIT, Google Cybersecurity Certified.  
> Focused on **web application pentesting** and **cryptography research**.  
> Currently 93%+ through the HackTheBox Web Pentesting Path.

---

## 📁 Repository Structure

```
HTB-Reports/
├── machines/          # Linux & Windows machine writeups
├── web-challenges/    # Web application challenge reports
├── walkthroughs/      # Step-by-step guided walkthroughs
├── templates/         # Reusable report templates
│   └── report-template.md
└── README.md
```

---

## 🖥️ Machines

| Name | OS | Difficulty | Techniques Used | Status |
|------|----|-----------|----------------|--------|
| [Appointment](./machines/appointment.md) | Linux | Very Easy | SQL Injection, auth bypass | ✅ Completed |
| [Crocodile](./machines/crocodile.md) | Linux | Very Easy | Anonymous FTP, credential reuse | ✅ Completed |
| [Fawn](./machines/fawn.md) | Linux | Very Easy | Anonymous FTP misconfiguration | ✅ Completed |
| [Responder](./machines/responder.md) | Windows | Very Easy | LLMNR poisoning, NetNTLMv2 cracking, WinRM | ✅ Completed |

> Reports are organized with a consistent structure covering reconnaissance, enumeration, exploitation, and post-exploitation where applicable.

---

## 🌐 Web Challenges

| Name | Difficulty | Vulnerability | OWASP Category | Status |
|------|-----------|--------------|----------------|--------|
| — | — | — | — | 🔄 Coming Soon |

> Web challenge writeups focus on OWASP Top 10 vulnerabilities, authentication bypasses, injection flaws, and logic errors.

---

## 🗺️ Walkthroughs

| Name | Category | Topics Covered | Status |
|------|---------|----------------|--------|
| — | — | — | 🔄 Coming Soon |

---

## 🛠️ Tools & Skills

### Penetration Testing Tools

| Tool | Category | Use Case |
|------|---------|---------|
| **Burp Suite** | Web Proxy | HTTP interception, fuzzing, vulnerability scanning |
| **Nmap** | Network Scanner | Port scanning, service & OS detection |
| **Gobuster / ffuf** | Fuzzer | Directory, file, and subdomain brute-forcing |
| **SQLMap** | SQL Injection | Automated SQLi detection and exploitation |
| **Metasploit** | Exploitation Framework | Exploit modules, payload generation |
| **Wireshark** | Packet Analyser | Network traffic analysis and capture |
| **Hashcat / John** | Password Cracking | Hash cracking and password recovery |
| **LinPEAS / WinPEAS** | Privilege Escalation | Automated local enumeration |
| **CyberChef** | Data Analysis | Encoding, decoding, cryptographic operations |
| **Python / Pwntools** | Scripting | Custom exploits and automation |

### Core Skills

- 🔍 **Reconnaissance & Enumeration** — Active/passive information gathering
- 💉 **Web Application Testing** — SQLi, XSS, SSRF, IDOR, XXE, LFI/RFI, SSTI
- 🔑 **Authentication & Session Analysis** — JWT attacks, cookie manipulation, OAuth flaws
- 🐧 **Linux Privilege Escalation** — SUID/GUID, sudo misconfigs, cron jobs, kernel exploits
- 🪟 **Windows Privilege Escalation** — Token impersonation, service misconfigs, registry
- 🔐 **Cryptography Research** — Classical ciphers, hash analysis, public-key cryptosystems
- 📝 **Technical Report Writing** — Professional pentest-style documentation

---

## 📋 Report Template

A reusable writeup template is available for new reports:

📄 [`templates/report-template.md`](./templates/report-template.md)

The template includes sections for:
- Machine/Challenge metadata table
- Reconnaissance & port scanning
- Enumeration details
- Exploitation with PoC
- Post-exploitation / privilege escalation
- Flags (redacted)
- Key takeaways & references

---

## ⚠️ Disclaimer

> **For Educational Purposes Only**
>
> All content in this repository — including reports, writeups, scripts, and techniques — is intended **solely for educational purposes** and **authorized security research**.
>
> - All machines and challenges documented here are **retired or officially released** HackTheBox content.
> - Techniques described should **never** be applied to systems without **explicit written authorization** from the system owner.
> - The author does **not** endorse or encourage any illegal, unauthorized, or unethical activity.
> - The author assumes **no liability** for misuse of the information provided in this repository.
>
> Always follow responsible disclosure practices and comply with applicable laws and regulations in your jurisdiction.

---

<div align="center">

**Made with 💚 by [Raghav Pathak](https://github.com/raghavpathak30)**  
*B.Tech CSE | LNMIIT | Google Cybersecurity Certified*

[![GitHub](https://img.shields.io/badge/GitHub-raghavpathak30-181717?style=flat-square&logo=github)](https://github.com/raghavpathak30)

</div>

