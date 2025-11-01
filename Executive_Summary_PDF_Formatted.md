# 🧠 Incident Response Report – Windows Host Compromise (win2.mcp.lab)

**Prepared for:** Capstone Evaluation Committee  
**Prepared by:** David Bustos – MCP Medic Triage Assistant  
**Report Generation Assisted by:** ChatGPT & Claude (AI-Assisted Workflow)  
**Date:** October 31, 2025  
**Classification:** Educational Use Only – Simulated Report

> **Note for GitHub viewers:** This file is the **PDF-formatted** version. It renders on GitHub, but is optimized for **Export to PDF**. A standard GitHub version is available here: [Executive_Summary_Light_Unredacted.md](./Executive_Summary_Light_Unredacted.md).

---

<a id="title-page"></a>
## 🔒 Title Page
**Incident Response Report**  
**Host:** win2.mcp.lab  
**Classification:** Educational Use Only – Simulated Report  

**Prepared for:** Capstone Evaluation Committee  
**Prepared by:** David Bustos – MCP Medic Triage Assistant  
**Assisted by:** ChatGPT & Claude (AI-Assisted Workflow)  
**Date:** October 31, 2025  

<div style="page-break-after: always;"></div>

---

<a id="toc"></a>
## 📑 Table of Contents
1. [Executive Summary](#1-executive-summary)  
2. [Incident Overview](#2-incident-overview)  
3. [Attack Timeline](#3-attack-timeline)  
4. [Technical Findings](#4-technical-findings)  
5. [Indicators of Compromise (IOCs)](#5-indicators-of-compromise-iocs)  
6. [Impact Assessment](#6-impact-assessment)  
7. [Containment & Recovery Actions](#7-containment--recovery-actions)  
8. [Evidence Summary](#8-evidence-summary)  
9. [Confidence Assessment](#9-confidence-assessment)  
10. [Analyst Notes & Capstone Context](#10-analyst-notes--capstone-context)

<div style="page-break-after: always;"></div>

---

<a id="1-executive-summary"></a>
## 1. Executive Summary
A multi-stage intrusion was simulated against **win2.mcp.lab** between **17:30–18:26 UTC** on **October 30, 2025**.  
The attacker disabled Windows Defender, performed system reconnaissance, deployed an XOR-encrypted .NET payload, and installed **AnyDesk** for persistent remote access.  
AI-assisted tooling (*Medic MCP*, *ChatGPT*, and *Claude*) supported triage, documentation, and report generation.

---

<a id="2-incident-overview"></a>
## 2. Incident Overview
- **Target Host:** win2.mcp.lab  
- **Attack Duration:** ~1 hour  
- **Attack Vector:** PowerShell & batch script execution  
- **Persistence Mechanisms:** Scheduled Task, Windows Service, Startup Shortcut  
- **C2 Channel:** AnyDesk (relay-6a630189.net.anydesk.com)  

---

<a id="3-attack-timeline"></a>
## 3. Attack Timeline

| Time (UTC) | Stage | Action | Artifact |
|-------------|--------|---------|-----------|
| 17:30 | Defense Evasion | Disabled Defender | anz.ps1 |
| 17:33 | Reconnaissance | Enumerated users, network | kor.bat |
| 18:17 | Payload Execution | XOR .NET loader executed | tyvm87.dat |
| 18:26 | Backdoor Install | AnyDesk persistent service created | AnyDesk.exe |

<div style="page-break-after: always;"></div>

---

<a id="4-technical-findings"></a>
## 4. Technical Findings

### 4.1 Defense Evasion
PowerShell script `anz.ps1` disabled multiple security controls and modified registry settings to suppress Defender alerts.
```powershell
Set-MpPreference -DisableRealtimeMonitoring $true
Set-MpPreference -DisableBehaviorMonitoring $true
```
- Registry: `DisableAntiSpyware = 1`
- Exclusions set on target directories

### 4.2 Reconnaissance
Batch file `kor.bat` enumerated system accounts, network interfaces, and service lists to establish situational awareness.
```
net user
net localgroup administrators
ipconfig /all
tasklist
```

### 4.3 Payload Deployment (Fileless)
File `tyvm87.dat` was an XOR-encrypted .NET assembly executed reflectively, resulting in a **fileless** payload.
- Size: 34 KB  
- XOR Key: `0xAA`  
- Executed in-memory via PowerShell

### 4.4 Persistence and Remote Access
`AnyDesk.exe` installed as a Windows Service and Startup link, maintaining an active outbound connection.
- SHA-256: `D2A7B55FD4A1114751F1FCD2B55366D37E5DAB9621F2C5428EC95997E57878A1`

---

<a id="5-indicators-of-compromise-iocs"></a>
## 5. Indicators of Compromise (IOCs)

**Files**
```
C:\Users\Administrator\AppData\Local\anz.ps1
C:\Users\Administrator\AppData\Local\kor.bat
C:\Users\Administrator\AppData\Roaming\tyvm87.dat
C:\ProgramData\AnyDesk\AnyDesk.exe
C:\ProgramData\Microsoft\Windows\Start Menu\Programs\Startup\AnyDesk.lnk
```

**Network Connections**
```
64.31.23.26:443 (AnyDesk Relay)
10.1.145.123:443 (Internal Lateral Movement)
```

**Hashes**
```
SHA256: D2A7B55FD4A1114751F1FCD2B55366D37E5DAB9621F2C5428EC95997E57878A1
```

---

<a id="6-impact-assessment"></a>
## 6. Impact Assessment

| Category | Impact | Description |
|-----------|---------|-------------|
| Security Controls | 🔴 Critical | Defender and UAC disabled |
| Confidentiality | 🔴 Critical | Potential data exfiltration |
| Integrity | 🔴 Critical | System and registry modified |
| Availability | 🟡 Medium | Host operational but compromised |
| Credentials | 🔴 Critical | Likely harvested |

---

<a id="7-containment--recovery-actions"></a>
## 7. Containment & Recovery Actions
1. Isolate the affected host from the network.  
2. Terminate AnyDesk processes and remove persistence entries.  
3. Quarantine malware files and export event logs.  
4. Decrypt payload (`tyvm87.dat`) using XOR key 0xAA for sandbox analysis.  
5. Reset all domain and local credentials.  
6. Rebuild system from known-good image.  
7. Perform enterprise-wide IOC sweep.

---

<a id="8-evidence-summary"></a>
## 8. Evidence Summary

| Artifact | Description | Source |
|-----------|--------------|--------|
| tyvm87.dat | Encrypted .NET payload | Memory dump |
| anz.ps1 | PowerShell Defender disabler | Disk |
| kor.bat | Reconnaissance script | Disk |
| AnyDesk.exe | Remote access backdoor | Service directory |

---

<a id="9-confidence-assessment"></a>
## 9. Confidence Assessment

| Finding | Confidence |
|----------|-------------|
| Host Compromise | 99% |
| Multi-stage Intrusion | 99% |
| Defender Disabled | 97% |
| Active C2 Channel | 90% |
| Fileless Payload | 98% |
| Persistence Confirmed | 95% |

**Overall Confidence:** 99% – Confirmed Simulated Compromise.

---

<a id="10-analyst-notes--capstone-context"></a>
## 10. Analyst Notes & Capstone Context
This capstone exercise demonstrates the integration of **AI-assisted analysis** in cybersecurity workflows.  
LLMs (*ChatGPT* and *Claude*) supported data correlation, summary generation, and report formatting.  
Medic MCP triaged host artifacts and guided investigation steps.  
The analyst successfully identified persistence, malicious process activity, and network IOCs in alignment with the course success criteria.

---

**Footer (for PDF export):**  
*Classification: Educational Use Only – Simulated Report*  
*Report Generation Assisted by ChatGPT & Claude*

