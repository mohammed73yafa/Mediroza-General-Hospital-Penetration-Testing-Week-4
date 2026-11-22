# Mediroza-General-Hospital-Penetration-Testing-Week-4


<p align="center">
  <b>Black-Box Web Application Penetration Testing</b><br>
  NetworkWalks | B083 | Week 01
</p>

<p align="center">
  <img src="https://img.shields.io/badge/Assessment-Black--Box-blue">
  <img src="https://img.shields.io/badge/Platform-Web%20Application-orange">
  <img src="https://img.shields.io/badge/Week-01-green">
  <img src="https://img.shields.io/badge/Status-In%20Progress-yellow">
</p>

---

## 📌 Project Overview

This repository documents the practical penetration testing activities performed against **Mediroza General Hospital** as part of the NetworkWalks B083 training program.

The assessment follows a Black-Box Penetration Testing methodology, focusing on identifying security vulnerabilities, evaluating authentication mechanisms, and assessing the exposure of sensitive information.

| Information | Details |
|---|---|
| Target | `medirozahospital.com` |
| Organization | Mediroza General Hospital |
| Assessment Type | Black-Box Penetration Testing |
| Training Program | NetworkWalks |
| Cohort | B083 |
| Assessment Period | 5 Days |
| Documentation | Week 01 |

> **Disclaimer:** All testing activities described in this repository are intended to be conducted within the explicitly authorized assessment scope. Sensitive patient information, credentials, and confidential organizational records are intentionally excluded from this public documentation.

---

## 📑 Table of Contents

1. [Scope and Rules](#-scope-and-rules)
2. [Testing Environment](#-testing-environment)
3. [Reconnaissance](#-1-reconnaissance)
4. [Information Disclosure](#-2-information-disclosure)
5. [Authentication Testing](#-3-authentication-testing)
6. [SQL Injection](#-4-sql-injection)
7. [Unauthorized Access to Confidential Reports](#-5-unauthorized-access-to-confidential-reports)
8. [PDF Password Recovery](#-6-pdf-password-recovery)
9. [Findings Summary](#-findings-summary)
10. [Recommendations](#-recommendations)
11. [Conclusion](#-conclusion)

---

# 🎯 Scope and Rules

The assessment was restricted to the following target:

- `medirozahospital.com`

### Assessment Restrictions

| Activity | Status |
|---|---|
| Reconnaissance | Allowed within scope |
| Directory Enumeration | Allowed within scope |
| Web Application Testing | Allowed within scope |
| Social Engineering | Prohibited |
| Denial of Service (DoS) | Prohibited |
| Testing Outside Scope | Prohibited |
| Sharing Findings Before Reveal Session | Prohibited |

---

# 🛠️ Testing Environment

The following tools were used during the assessment:

| Tool | Purpose |
|---|---|
| FFUF | Directory and file enumeration |
| PDFCrack | Offline PDF password recovery |
| RockYou | Password wordlist |
| Web Browser | Application testing and verification |
| Bash | Command-line operations and documentation |

---

# 🔎 1. Reconnaissance

## 1.1 Directory Enumeration Using FFUF

The assessment started with reconnaissance and directory enumeration to identify publicly accessible resources and hidden application paths.

**Tool:** FFUF

**Objective:**
- Discover hidden directories.
- Identify publicly accessible files.
- Locate potential backup resources.
- Identify additional application entry points.

### Command

```bash
ffuf -u https://medirozahospital.com/FUZZ \
-w /path/to/wordlist.txt
```

### Results

During the enumeration process, several interesting resources were identified.

The most notable discoveries included:

- `robots.txt`
- An accessible directory named `old`

### Evidence

![FFUF Enumeration](screenshots/01-ffuf-enumeration.png)

*Figure 1: Directory enumeration results using FFUF.*

---

## 1.2 Robots.txt Analysis

The discovered `robots.txt` file was inspected to identify additional paths referenced by the application.

### Command

```bash
curl -s https://medirozahospital.com/robots.txt
```

### Observation

The file was reviewed as part of the reconnaissance process to identify potentially interesting application paths.

### Evidence

![Robots TXT](screenshots/02-robots-txt.png)

*Figure 2: Robots.txt inspection.*

---

# 📂 2. Information Disclosure

## 2.1 Discovery of the Old Directory

During the enumeration phase, a directory named `old` was discovered.

Further inspection revealed that the directory contained an old database backup.

This discovery was particularly important because the backup contained sensitive organizational information.

### Evidence

![Old Directory](screenshots/03-old-directory.png)

*Figure 3: Discovery of the old directory.*

## 2.2 Database Backup Discovery

The backup file was found to contain records related to hospital employees and shareholders.

This represents a significant information disclosure risk because database backups should never be publicly accessible.

### Information Identified

| Data Category | Observation |
|---|---|
| Employee Records | Present |
| Shareholder Information | Present |
| Database Backup | Discovered |
| Confidential Information | Exposed |

### Evidence

![Database Backup](screenshots/04-database-backup.png)

*Figure 4: Discovery of the database backup. Sensitive contents are intentionally redacted.*

### Security Impact

An unauthorized user could potentially retrieve confidential organizational records without using the application's intended authentication mechanisms.

---

# 🔐 3. Authentication Testing

## 3.1 Login Page Analysis

After reconnaissance, the authentication mechanism was examined.

The objective was to understand how the application handled invalid usernames and passwords.

### Testing Observations

Two different responses were observed:

| Test Case | Application Response |
|---|---|
| Invalid Username | Username does not exist |
| Existing Username + Invalid Password | Incorrect password |

### Analysis

The different responses revealed that the application disclosed whether a username existed.

This behavior is commonly known as **Username Enumeration**.

### Evidence

![User Enumeration](screenshots/05-user-enumeration.png)

*Figure 5: Different authentication responses revealing username validity.*

---

# 💉 4. SQL Injection

## 4.1 Identifying the Database Technology

During authentication testing, a specially crafted input caused the application to return a database-related error.

The error revealed that the backend database was using **MySQL**.

### Observation

The application appeared to process user input in an unsafe manner, resulting in an SQL-related error.

### Evidence

![MySQL Error](screenshots/06-mysql-error.png)

*Figure 6: Database error observed during authentication testing.*

## 4.2 Authentication Bypass

Further authorized testing demonstrated that the authentication mechanism could be bypassed.

This resulted in access to a restricted application page without successfully completing the normal login process.

### Vulnerability Classification

**SQL Injection – Authentication Bypass**

### Security Impact

- Unauthorized access to restricted functionality.
- Potential exposure of confidential information.
- Compromise of authentication controls.

### Evidence

![Authentication Bypass](screenshots/07-authentication-bypass.png)

*Figure 7: Evidence of authentication bypass. Session information is redacted.*

---

# 📄 5. Unauthorized Access to Confidential Reports

Following the authentication bypass, a restricted page containing confidential laboratory reports was accessed.

Three PDF documents were discovered.

### Observations

| Item | Result |
|---|---|
| Restricted Page | Accessible |
| Confidential PDF Reports | 3 |
| PDF Protection | Password Protected |
| Unauthorized Access | Confirmed |

### Evidence

![Confidential Reports](screenshots/08-confidential-reports.png)

*Figure 8: Discovery of three confidential laboratory reports.*

### Security Impact

The exposure of medical laboratory reports represents a serious confidentiality risk.

Unauthorized access to such documents may expose sensitive patient information.

---

# 🔓 6. PDF Password Recovery

## 6.1 Password Protection Analysis

The three PDF documents were individually examined to determine their password protection mechanisms.

**Tool:** PDFCrack

**Wordlist:** RockYou

Password recovery was performed offline against the authorized assessment files.

### Command

```bash
pdfcrack -f report.pdf -w rockyou.txt
```

> The command above illustrates the offline recovery workflow. Original reports and recovered passwords must remain private and must not be uploaded to this repository.

## 6.2 Recovery Results

The password recovery process successfully recovered the passwords protecting all three PDF documents.

Each document was tested individually to verify successful access.

### Results

| Document | Password Recovery | Verification |
|---|---|---|
| Report 01 | Successful | Verified |
| Report 02 | Successful | Verified |
| Report 03 | Successful | Verified |

### Evidence

![PDFCrack Results](screenshots/09-pdfcrack-results.png)

*Figure 9: PDF password recovery results. Actual passwords are redacted.*

## 6.3 Document Access Verification

After recovering the passwords, the PDF documents were opened to verify that the protection could be bypassed through offline password recovery.

### Evidence

![PDF Access](screenshots/10-pdf-access.png)

*Figure 10: Successful document access. Patient information is completely redacted.*

### Security Impact

The use of weak passwords to protect confidential documents can expose sensitive information when attackers obtain copies of those documents.

---

# 📊 Findings Summary

The following table summarizes the vulnerabilities identified during Week 01.

| ID | Finding | Category | Preliminary Severity |
|---|---|---|---|
| NW-01 | Exposed Database Backup | Information Disclosure | Critical |
| NW-02 | Username Enumeration | Authentication Weakness | Medium |
| NW-03 | SQL Injection | Injection | Critical |
| NW-04 | Authentication Bypass | Broken Authentication | Critical |
| NW-05 | Unauthorized Report Access | Broken Access Control | Critical |
| NW-06 | Weak PDF Password Protection | Weak Cryptography / Passwords | High |

*Severity ratings are preliminary and should be validated against the complete impact and assessment evidence.*

---

# 🛡️ Recommendations

Based on the findings, the following security improvements are recommended:

### 1. Database Backup Protection
- Remove backup files from publicly accessible directories.
- Store backups outside the web root.
- Restrict access to backup resources.
- Encrypt sensitive backups.

### 2. Authentication Security
- Use generic login error messages.
- Implement rate limiting.
- Apply multi-factor authentication where appropriate.
- Monitor suspicious login activity.

### 3. SQL Injection Prevention
- Use prepared statements.
- Implement parameterized SQL queries.
- Avoid dynamic SQL query construction.
- Apply least-privilege database permissions.
- Disable detailed database errors in production.

### 4. Access Control
- Enforce server-side authorization checks.
- Restrict access to confidential documents.
- Validate permissions for every file request.
- Implement secure session management.

### 5. PDF Security
- Use strong and unique passwords.
- Apply modern encryption standards.
- Avoid predictable passwords.
- Do not rely exclusively on PDF password protection.

---

# 📝 Conclusion

The first week of the assessment revealed several significant security weaknesses in the Mediroza General Hospital web application.

The most notable discoveries involved:

- Public exposure of a database backup.
- Username enumeration.
- SQL Injection affecting the authentication mechanism.
- Authentication bypass.
- Unauthorized access to confidential laboratory reports.
- Successful offline recovery of PDF passwords.

These findings demonstrate how multiple security weaknesses can be combined to compromise the confidentiality of sensitive information.

Further analysis of the exposed database and the remaining assessment objectives will be documented in subsequent updates.

---

# 📚 References

- [FFUF – GitHub Repository](https://github.com/ffuf/ffuf)
- [PDFCrack – GitHub Repository](https://github.com/robins/pdfcrack)
- [OWASP Web Security Testing Guide](https://owasp.org/www-project-web-security-testing-guide/)
- [OWASP SQL Injection Prevention Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/SQL_Injection_Prevention_Cheat_Sheet.html)
- [CWE-89 – SQL Injection](https://cwe.mitre.org/data/definitions/89.html)
- [CWE-200 – Exposure of Sensitive Information](https://cwe.mitre.org/data/definitions/200.html)

---

<p align="center">
  <b>NetworkWalks | B083</b><br>
  <i>Week 01 – Practical Penetration Testing</i>
</p>
