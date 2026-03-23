# SOC Attack Investigation — Splunk BOTS v1

![Splunk](https://img.shields.io/badge/SIEM-Splunk%20Enterprise-orange)
![Dataset](https://img.shields.io/badge/Dataset-BOTS%20v1-blue)
![MITRE](https://img.shields.io/badge/Framework-MITRE%20ATT%26CK-red)
![Status](https://img.shields.io/badge/Status-Complete-brightgreen)
![Events](https://img.shields.io/badge/Events%20Analysed-33M+-blueviolet)

---

## 📌 Overview

This project presents a full end-to-end SOC (Security Operations Center) investigation using the Splunk BOTS v1 dataset. The investigation traces a simulated cyber attack from initial reconnaissance through exploitation, persistence, command-and-control (C2), and data exfiltration.

The objective of this project is to demonstrate practical SOC analyst skills including threat hunting, SPL query development, attack chain analysis, and structured incident documentation.

---

## 📊 Investigation Summary

| Field | Detail |
|---|---|
| Dataset | BOTS v1 |
| Analyst | Rohit Aswal |
| Investigation Type | Full Attack Investigation |
| Events Analysed | ~33 million |
| Target System | Joomla Web Server |
| Outcome | Full compromise with C2 and data exfiltration |

---

## 🧠 Skills Demonstrated

- Threat hunting using Splunk (SPL)
- Web attack detection and analysis
- Brute force detection and credential compromise investigation
- Web shell identification and analysis
- Command & Control (C2) detection
- Data exfiltration detection using network telemetry
- Attack timeline reconstruction
- MITRE ATT&CK mapping
- SOC-style reporting and documentation

---

## 🛠️ Tools & Technologies

| Tool | Purpose |
|---|---|
| Splunk Enterprise | SIEM platform for log analysis |
| BOTS v1 Dataset | Simulated enterprise attack dataset |
| MITRE ATT&CK | Threat classification framework |

---

## 🚨 Key Findings

### 🔍 1. Attacker Identification
- External IP **40.80.148.42**
- Generated **17,547 HTTP requests**
- Identified as primary attacker

### 🔍 2. Reconnaissance Activity
- Target: Joomla CMS (`/joomla/`)
- Heavy scanning on:
  - `/component/search/`
  - `/administrator/index.php`
- Directory traversal attempts detected:
  - `/windows/win.ini`
  - `/boot.ini`

### 🔍 3. Brute Force Attack
- Multiple POST requests to admin login page  
- Successful authentication observed (HTTP 200 responses)  
- Confirms brute force attack success  

### 🔍 4. Credential Compromise
- Username: **admin**  
- Password: **batman**  
- Extracted from HTTP POST form data  

### 🔍 5. Web Shell Deployment
- PHP web shell uploaded via Joomla installer  
- File: `/joomla/agent.php`  
- Obfuscated payload detected (`eval`, `base64_decode`, `gzuncompress`)  

### 🔍 6. Command & Control (C2)
- Secondary IP: **23.22.63.114**  
- ~194 requests to `/joomla/agent.php`  
- Rapid beaconing behaviour (~100–400ms intervals)  

### 🔍 7. Data Exfiltration
- ~**19.8 MB** data transferred to attacker IP **40.80.148.42**  
- Confirms successful data theft  

---

## 🧩 Attack Timeline

Reconnaissance → Joomla vulnerability scanning  
Initial Access → Brute force login (admin/batman)  
Execution → Web shell upload (agent.php)  
Persistence → C2 communication established  
Post-Exploitation → File system enumeration  
Exfiltration → Data transferred (~19.8MB)

---

## 🧬 MITRE ATT&CK Mapping

| Technique ID | Description |
|---|---|
| T1190 | Exploit Public-Facing Application |
| T1110 | Brute Force |
| T1078 | Valid Accounts |
| T1505 | Web Shell |
| T1071 | Application Layer Protocol (HTTP) |
| T1041 | Exfiltration Over C2 Channel |

---

## 🔎 SPL Detection Queries

```spl
# Attacker Identification
index=botsv1 sourcetype=stream:http
| stats count by src_ip
| sort -count

# Brute Force Detection
index=botsv1 uri_path="/joomla/administrator/index.php"
| stats count by src_ip, http_method, status

# Credential Extraction
index=botsv1 http_method=POST
| table _time, src_ip, form_data

# C2 Detection
index=botsv1 uri_path="/joomla/agent.php"
| stats count by src_ip

# Data Exfiltration Detection
index=botsv1 dest_ip=192.168.250.70
| stats sum(bytes_out) by src_ip
| sort -sum(bytes_out)
```

---

## 📁 Repository Structure

```
SOC-BOTS-v1-Attack-Investigation/
│
├── README.md
│
├── report/
│   ├── BOTS_Attack_Report.docx
│   └── BOTS_Attack_Report.pdf
│
├── screenshots/
│   ├── SS1_attacker_ip.png
│   ├── SS2_uri_scan.png
│   ├── SS3_bruteforce.png
│   ├── SS4_credentials.png
│   ├── SS5_webshell_upload.png
│   ├── SS6_c2.png
│   ├── SS7_beaconing.png
│   └── SS8_exfiltration.png
│
└── queries/
    └── detection_queries.spl
```

---

## 🧾 Report

A detailed SOC-style investigation report is available in the `/report` directory.

---

## 👨‍💻 Author

**Rohit Aswal**  
MSc Cybersecurity, Threat Intelligence & Digital Forensics — University of Salford  
SOC Analyst | Threat Hunting | Digital Forensics  

🔗 LinkedIn: https://www.linkedin.com/in/rohit-aswal08  
🔗 GitHub: https://github.com/rohitaswal2108-Roh  

---

⚠️ This project is based on simulated attack data from the Splunk BOTS v1 dataset and is intended for educational purposes only.
