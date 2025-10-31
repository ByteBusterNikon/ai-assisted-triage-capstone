# 🧠 Executive Summary – Windows Host Compromise (win2.mcp.lab)

**Prepared for:** Capstone Evaluation Committee  
**Prepared by:** David Bustos – MCP Medic Triage Assistant  
**Report Generation Assisted by:** ChatGPT & Claude (AI-Assisted Workflow)  
**Date:** October 31, 2025  
**Classification:** Educational Use Only – Simulated Report  

---

## ⚠️ Disclaimer
This is a **fictional, educational simulation** of an incident response exercise.  
All systems, data, and indicators are fabricated for learning and demonstration purposes.  
No real malware, infrastructure, or personal data are present.

---

## 📑 Table of Contents
1. [Overview](#overview)  
2. [Attack Summary](#attack-summary)  
3. [Timeline of Events](#timeline-of-events)  
4. [Malware Components](#malware-components)  
5. [Indicators of Compromise (IOCs)](#indicators-of-compromise-iocs)  
6. [Impact Assessment](#impact-assessment)  
7. [Immediate Actions](#immediate-actions)  
8. [Evidence Collected](#evidence-collected)  
9. [Confidence Assessment](#confidence-assessment)  
10. [Analyst Notes & Capstone Context](#analyst-notes--capstone-context)

---

<a id="overview"></a>
## 🧭 Overview
A sophisticated four-stage compromise targeted **win2.mcp.lab** on **2025-10-30** (17:30–18:26 UTC).  
The attacker disabled Windows Defender, executed a fileless .NET payload, and established persistent remote access through **AnyDesk**.  
This report documents each stage, technical indicators, and recommended containment actions.

---

<a id="attack-summary"></a>
## 🔍 Attack Summary

| Stage | Objective | Component |
|--------|------------|------------|
| 1 | Defense Evasion | `anz.ps1` – Disabled Defender and UAC |
| 2 | Reconnaissance | `kor.bat` – Enumerated users, groups, and networks |
| 3 | Payload Deployment | `tyvm87.dat` – XOR-encrypted .NET loader |
| 4 | Backdoor Persistence | `AnyDesk.exe` – Installed and auto-started under SYSTEM |

**Active External C2:** `64.31.23.26` (relay-6a630189.net.anydesk.com)  
**Suspicious Internal Connection:** `10.1.145.123:443` (svchost.exe)

---

<a id="timeline-of-events"></a>
## ⏱️ Timeline of Events

| Time (UTC) | Stage | Action | Artifact |
|-------------|--------|--------|-----------|
| 17:30 | Defense Evasion | Disabled Defender via PowerShell | `anz.ps1` |
| 17:33 | Reconnaissance | Enumerated users, network, services | `kor.bat` |
| 18:17 | Payload Execution | Loaded XOR .NET assembly in-memory | `tyvm87.dat` |
| 18:26 | Backdoor Install | AnyDesk persistence under SYSTEM | `AnyDesk.exe` |

---

<a id="malware-components"></a>
## 🧩 Malware Components

### 1️⃣ anz.ps1 – Defense Evasion Script
```powershell
Set-MpPreference -DisableRealtimeMonitoring $true
Set-MpPreference -DisableBehaviorMonitoring $true
```
- Disabled Defender and UAC.  
- Added malware to exclusion paths.  
- Registry modification: `DisableAntiSpyware = 1`.

### 2️⃣ kor.bat – Recon Script
```
net user
net localgroup administrators
ipconfig /all
tasklist
```
- Collected host and network intel.  
- Enumerated logged-in users and system groups.

### 3️⃣ tyvm87.dat – Encrypted Payload
- 34 KB XOR-encrypted .NET loader.  
- Key: `0xAA`.  
- Executed reflectively via PowerShell; remained fileless.

### 4️⃣ AnyDesk.exe – Remote Access Backdoor
- SHA256: `D2A7B55FD4A1114751F1FCD2B55366D37E5DAB9621F2C5428EC95997E57878A1`  
- Installed service and startup shortcut.  
- Connected outbound to `64.31.23.26:443`.

---

<a id="indicators-of-compromise-iocs"></a>
## 🧾 Indicators of Compromise (IOCs)

**Files**
```
C:\Users\Administrator\AppData\Local\anz.ps1
C:\Users\Administrator\AppData\Local\kor.bat
C:\Users\Administrator\AppData\Roaming\tyvm87.dat
C:\ProgramData\AnyDesk\AnyDesk.exe
C:\ProgramData\Microsoft\Windows\Start Menu\Programs\Startup\AnyDesk.lnk
```

**Network**
```
64.31.23.26 (AnyDesk Relay)
10.1.145.123 (Internal Connection)
```

**Persistence**
- Scheduled Task: `\WinInputService` (SYSTEM)  
- Service: `AnyDesk` (Auto-Start)  
- Startup Shortcut: `AnyDesk.lnk`

---

<a id="impact-assessment"></a>
## ⚠️ Impact Assessment

| Category | Impact | Description |
|-----------|---------|-------------|
| Security Controls | 🟥 Critical | Defender and UAC disabled |
| Confidentiality | 🟥 Critical | Remote access and data exfil risk |
| Integrity | 🟥 Critical | System modifications confirmed |
| Availability | 🟡 Medium | No destruction observed |
| Credentials | 🟥 Critical | Assume credential compromise |

---

<a id="immediate-actions"></a>
## 🧰 Immediate Actions

### Containment
1. Isolate host `win2.mcp.lab`.  
2. Stop AnyDesk service and remove persistence.  
3. Delete scheduled task `WinInputService`.  
4. Quarantine artifacts.

### Forensics
1. Capture memory and event logs.  
2. Decrypt payload `tyvm87.dat` with XOR key `0xAA`.  
3. Analyze .NET loader in sandbox.  

### Recovery
1. Reset local and domain credentials.  
2. Rebuild from clean image.  
3. Re-enable Defender.  
4. Conduct enterprise-wide IOC hunt.

---

<a id="evidence-collected"></a>
## 🧾 Evidence Collected

| Filename | Size | URL |
|-----------|------|-----|
| 1761872376_tyvm87.dat | 34 KB | hxxp://medic.mcp.lab:8000/collections/1761872376_tyvm87.dat |
| 1761873478_anz.ps1 | 759 B | hxxp://medic.mcp.lab:8000/collections/1761873478_anz.ps1 |
| 1761873482_kor.bat | 670 B | hxxp://medic.mcp.lab:8000/collections/1761873482_kor.bat |
| 1761872389_init.vbs | 178 B | hxxp://medic.mcp.lab:8000/collections/1761872389_init.vbs |
| 1761872501_EnableClearType.ps1 | 656 B | hxxp://medic.mcp.lab:8000/collections/1761872501_EnableClearType.ps1 |

---

<a id="confidence-assessment"></a>
## 📊 Confidence Assessment

| Finding | Confidence |
|----------|-------------|
| Host compromised | 99% |
| Multi-stage attack | 99% |
| Defender disabled | 97% |
| Active malicious connection | 90% |
| Fileless payload | 98% |
| AnyDesk unauthorized | 90% |

**Overall Confidence:** 99% – Near Certainty

---

<a id="analyst-notes--capstone-context"></a>
## 🧩 Analyst Notes & Capstone Context
This exercise demonstrates AI-assisted triage workflows using *Medic MCP*.  
Key goals achieved:
- Identified persistence mechanisms and fileless execution.  
- Documented malicious process and network activity.  
- Produced executive-level summary with structured Markdown automation.  

---
_End of Light Unredacted Report_
