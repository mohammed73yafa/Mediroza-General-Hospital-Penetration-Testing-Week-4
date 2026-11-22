# Mediroza General Hospital – Web Application Penetration Testing (Week 4)

<p align="center">
  <b>Black-Box Web Application Penetration Testing</b><br>
  NetworkWalks | Batch B083 | Week 04
</p>

<p align="center">
  <img src="https://img.shields.io/badge/Assessment-Black--Box-blue" alt="Black-Box">
  <img src="https://img.shields.io/badge/Target-Web%20Application-orange" alt="Web Application">
  <img src="https://img.shields.io/badge/Batch-B083-informational" alt="Batch B083">
  <img src="https://img.shields.io/badge/Week-04-green" alt="Week 04">
</p>

---

## Project Overview

This repository documents the black-box penetration testing engagement conducted against **Mediroza General Hospital** (`https://medirozahospital.com`) as part of the NetworkWalks Cybersecurity Program (**Batch B083 – Week 04**).

The objective of this engagement was to identify security weaknesses across the hospital's web infrastructure, evaluate authentication controls, gain unauthorized access to restricted patient laboratory reports, recover protected PDF contents, and assess the impact of exposed backend data.

| Parameter | Details |
| :--- | :--- |
| **Client** | Mediroza General Hospital |
| **Target** | `https://medirozahospital.com` |
| **Assessment Type** | Black-Box Penetration Testing |
| **Program / Cohort** | NetworkWalks – Batch B083 |
| **Module** | Week 04 |

> **Disclaimer:** All testing activities documented here were conducted strictly within an authorized educational lab environment. Sensitive patient records, employee personally identifiable information (PII), and recovered passwords have been redacted from this public documentation.

---

## Table of Contents

1. [Scope & Engagement Rules](#1-scope--engagement-rules)
2. [Testing Environment & Tools](#2-testing-environment--tools)
3. [Reconnaissance & Enumeration](#3-reconnaissance--enumeration)
4. [Sensitive Information Disclosure (Database Backup)](#4-sensitive-information-disclosure-database-backup)
5. [Authentication & SQL Injection Exploitation](#5-authentication--sql-injection-exploitation)
6. [Unauthorized Access to Patient Lab Reports](#6-unauthorized-access-to-patient-lab-reports)
7. [Offline PDF Password Recovery](#7-offline-pdf-password-recovery)
8. [Findings & Risk Summary](#8-findings--risk-summary)
9. [Remediation Recommendations](#9-remediation-recommendations)
10. [References](#10-references)

---

## 1. Scope & Engagement Rules

- **In-Scope Target:** `https://medirozahospital.com`
- **Out-of-Scope / Restrictions:**
  - No testing outside the target domain.
  - No Denial of Service (DoS) or resource-exhaustion attacks.
  - No social engineering attacks.

---

## 2. Testing Environment & Tools

Testing was performed from **Kali Linux** using the following toolset:

| Tool | Role in Engagement |
| :--- | :--- |
| **FFUF** | Directory and file fuzzing |
| **cURL / Browser DevTools** | HTTP request inspection and manual verification |
| **Burp Suite** | Intercepting and analyzing authentication requests |
| **SQLMap / Manual Payloads** | Testing and exploiting SQL Injection in the Patient Portal |
| **pdf2john / pdfcrack / Python** | Extracting PDF encryption hashes and offline password recovery |
| **RockYou Wordlist** | Dictionary-based password cracking |

---

## 3. Reconnaissance & Enumeration

### 3.1 Directory & File Fuzzing (`FFUF`)

Active directory enumeration was run against the target to uncover hidden directories, backup files, and administrative entry points while filtering out standard `404` and `403` responses:

```bash
ffuf -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -u [https://medirozahospital.com/FUZZ](https://medirozahospital.com/FUZZ) -c -fc 404,403 -t 40
