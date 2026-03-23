# SOC Attack Investigation — Splunk BOTS v1

![Splunk](https://img.shields.io/badge/SIEM-Splunk%20Enterprise-orange)
![Dataset](https://img.shields.io/badge/Dataset-BOTS%20v1-blue)
![MITRE](https://img.shields.io/badge/Framework-MITRE%20ATT%26CK-red)
![Status](https://img.shields.io/badge/Status-Complete-brightgreen)

---

## Overview

This project presents a full end-to-end SOC investigation using the Splunk BOTS v1 dataset. The investigation follows the complete attack lifecycle, from initial reconnaissance to data exfiltration.

---

## Investigation Summary

| Field | Detail |
|---|---|
| Dataset | BOTS v1 |
| Analyst | Rohit Aswal |
| Events Analysed | ~33 million |
| Attack Type | Web Application Attack (Joomla) |
| Outcome | Full compromise with C2 and data exfiltration |

---

## Key Findings

### 1. Attacker Identification
- External IP: **40.80.148.42**
- 17,547 HTTP requests
- Identified as primary attacker

### 2. Reconnaissance
- Joomla CMS targeted
- Heavy scanning on `/component/search/`
- Directory traversal attempts detected

### 3. Brute Force Attack
- Admin login targeted
- Multiple POST requests
- Successful authentication confirmed

### 4. Credential Compromise
- Username: **admin**
- Password: **batman**

### 5. Web Shell Deployment
- PHP web shell uploaded
- File: `/joomla/agent.php`

### 6. Command & Control (C2)
- C2 IP: **23.22.63.114**
- 194 callback requests
- Automated beaconing behaviour

### 7. Data Exfiltration
- ~19.8MB data transferred to attacker IP
- Confirms successful data theft

---

## Attack Timeline

- Reconnaissance → Vulnerability scanning
- Initial Access → Brute force login
- Execution → Web shell upload
- Persistence → C2 channel established
- Exfiltration → Data transferred externally

---

## MITRE ATT&CK Mapping

| Technique | Description |
|---|---|
| T1190 | Exploit Public-Facing Application |
| T1110 | Brute Force |
| T1078 | Valid Accounts |
| T1505 | Web Shell |
| T1071 | C2 over HTTP |
| T1041 | Data Exfiltration |

---

## Detection Queries

### Attacker Identification
```spl
index=botsv1 sourcetype=stream:http
| stats count by src_ip
| sort -count
