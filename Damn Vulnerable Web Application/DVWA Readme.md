# DVWA Walkthrough — Web Application Security Fundamentals
> Deliberately Vulnerable Web Application: Learning security by breaking it intentionally.

---
## What This Is

DVWA is a deliberately vulnerable web application designed for learning web security. Every vulnerability is intentional—put there so we can practice exploiting it, understanding it, and learning how to defend against it.
This walkthrough documents the journey through **5 critical web vulnerabilities** at **4 difficulty levels each** (Low → Medium → High → Impossible).

**Philosophy:** Break it to understand it. Understand it to defend it.

---
## Vulnerabilities Exploited

### 1. **Command Injection**
Injecting OS commands into application input fields. If an app doesn't sanitize user input before executing system commands, attackers can run arbitrary code.
**Levels:** Low | Medium | High | Impossible
**Real-world impact:** Remote code execution (RCE) — highest severity
___
### 2. **CSRF (Cross-Site Request Forgery)**
Tricking authenticated users into performing unwanted actions on a site where they're logged in. The user doesn't know they're making the request.
**Levels:** Low | Medium | High | Impossible
**Real-world impact:** Unauthorized actions (transfer money, change password, delete data)
___
### 3. **File Inclusion**
Exploiting the application to read/include files from the server that shouldn't be accessible. Can escalate to remote code execution.
**Levels:** Low | Medium | High | Impossible
**Real-world impact:** Data exposure, remote code execution
___
### 4. **SQL Injection**
Injecting SQL code into input fields to manipulate database queries. Bypass authentication, extract data, modify/delete records.
**Levels:** Low | Medium | High | Impossible
**Real-world impact:** Complete database compromise, authentication bypass
___
### 5. **SQL Blind Injection**
SQL injection when error messages aren't visible, but the application still responds differently based on query results. Requires logical inference.
**Levels:** Low | Medium | High | Impossible
**Real-world impact:** Slower but still devastating database extraction

---
## Difficulty Levels Explained

### **Low**
- Minimal/no input validation
- Obvious vulnerability
- Exploit works with simple payload
- Learning: Understand the basic concept

### **Medium**
- Some input filtering/sanitization
- Requires bypassing basic defenses
- Exploit needs adjustment
- Learning: Understand defenses and how to evade them

### **High**
- Strong input validation
- Multiple layers of protection
- Exploit requires creative thinking
- Learning: Understand sophisticated defenses

### **Impossible**
- Near-impossible to exploit through normal means
- May require logic/cryptographic breaks
- Shows "this should never be vulnerable"
- Learning: Best practices in action

---
## Learning Progression

Each vulnerability flows: **Understand → Exploit → Defend**

1. **Understand:** What's the vulnerability? How does it work?
2. **Exploit:** How do I take advantage of it? What's my payload?
3. **Defend:** How would a developer fix this? What's the correct code?

---

## File Structure
4-dvwa-walkthrough/
├── README.md (this file)
├── 01-COMMAND_INJECTION.md
│   ├── Level Low
│   ├── Level Medium
│   ├── Level High
│   └── Level Impossible
├── 02-CSRF.md
│   ├── Level Low
│   ├── Level Medium
│   ├── Level High
│   └── Level Impossible
├── 03-FILE_INCLUSION.md
│   ├── Level Low
│   ├── Level Medium
│   ├── Level High
│   └── Level Impossible
├── 04-SQL_INJECTION.md
│   ├── Level Low
│   ├── Level Medium
│   ├── Level High
│   └── Level Impossible
├── 05-SQL_BLIND_INJECTION.md
    ├── Level Low
    ├── Level Medium
    ├── Level High
    └── Level Impossible

---
## How to Read This

For each vulnerability:
1. **Read the overview** (what it is, how it works)
2. **Follow the Low level** (basic exploitation)
3. **Progress through Medium → High → Impossible** (increasing difficulty)
4. **Compare defenses** (see how each level mitigates the vulnerability)

---
## Key Takeaways

- **Low:** Exploit is straightforward
- **Medium:** Defenses exist, but bypassable
- **High:** Strong defenses, requires understanding nuances
- **Impossible:** What secure code looks like

---
## Real-World Significance

These 5 vulnerabilities account for **major portions of the OWASP Top 10**. Understanding them deeply means understanding web security fundamentals.

- Command Injection = Code Execution (Critical)
- CSRF = Authorization Bypass (High)
- File Inclusion = Data Exposure (High)
- SQL Injection = Database Compromise (Critical)
- SQL Blind = Stealth Database Extraction (High)

---
## Next Steps

1. Read each vulnerability deep-dive
2. Study the progression from Low → Impossible
3. Understand the defenses at each level
4. Ask: "Why does this level succeed where the next fails?"

---
## Philosophy

> The best defense is understanding the attack.
>
> You can't protect what you don't understand.
>
> DVWA forces understanding.

---
**Started:** [19th September, 2026]
**Status:** 5 Vulnerabilities × 4 Levels = 20 Exploitations Documented

Made with intention. Built for mastery.

---

## References & Resources

### For the making of this solution the following resource were used:

- https://github.com/digininja/DVWA
- https://github.com/LeonardoE95/DVWA/

### Tools Used
- **DVWA:** Deliberately Vulnerable Web Application (for intentional vulnerabilities)
- **Burp Suite Community:** Network traffic inspection and manipulation
- **Browser DevTools:** For CSRF and client-side analysis

## Disclaimer

> All exploitation is performed on **deliberately vulnerable applications (DVWA)** in a controlled lab environment.
>
> These techniques are for educational purposes only. Unauthorized access to systems is illegal.
>
> Always obtain proper authorization before testing.

---
