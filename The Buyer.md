<p align="center">
  <img
    src="https://github.com/user-attachments/assets/337bb215-8833-4653-b570-93c443bd9c11"
    width="1200"
    alt="Threat Hunt Cover Image"
  />
</p>

# 🔍 The Buyer — Akira Ransomware Incident Response

**SancLogic Cyber Range | Advanced Difficulty | 40/40 Questions Solved**

> *"A ransomware affiliate returned to Ashford Sterling Recruitment using access pre-staged during a prior compromise. The threat actor deployed Akira ransomware across two hosts."*

---

## 📋 Overview

This repository documents a full incident response investigation into an **Akira ransomware** attack on a fictional recruitment firm, **Ashford Sterling Recruitment**. This challenge is part of the **SancLogic Cyber Range** two-part series (*The Broker* → *The Buyer*).

| Detail | Value |
|--------|-------|
| Analyst | Destiny Furlong |
| Difficulty | Advanced |
| Platform | Microsoft Sentinel + Azure Log Analytics |
| Incident Date | January 27, 2026 |
| Investigation Date | March 2026 |
| Questions Solved | 40/40 across 12 sections |

---

## 🧠 The Core Challenge

The incident occurred **47 days before** the investigation began. Microsoft Defender for Endpoint only retains Advanced Hunting data for 30 days. By the time the investigation started, MDE had nothing. **Azure Log Analytics was the only viable platform** — and this was not known until mid-investigation.

This shaped every query decision made throughout the hunt.

---

## 🧬 MITRE ATT&CK Coverage

| Technique | MITRE ID | Phase |
|-----------|----------|-------|
| External Remote Services | T1133 | Initial Access |
| Valid Accounts | T1078 | Persistence |
| Impair Defenses | T1562 | Defense Evasion |
| Modify Registry | T1112 | Defense Evasion |
| Masquerading | T1036 | Defense Evasion |
| Indicator Removal | T1070 | Defense Evasion |
| OS Credential Dumping | T1003 | Credential Access |
| Network Share Discovery | T1135 | Discovery |
| Remote Services | T1021 | Lateral Movement |
| Ingress Tool Transfer | T1105 | Command & Control |
| Remote Access Tools | T1219 | Command & Control |
| Archive Collected Data | T1560 | Collection |
| Exfiltration Over C2 Channel | T1041 | Exfiltration |
| Data Encrypted for Impact | T1486 | Impact |
| Inhibit System Recovery | T1490 | Impact |

---

## ⚔️ Attack Chain Overview

The attacker did not need to re-compromise the environment. Access was already there from a prior intrusion. The operation moved through six phases over roughly three hours.

```
Phase 1 — Re-Entry
  └─ AnyDesk pre-staged backdoor activated (C:\Users\Public\)
  └─ Attacker connects from 88.97.164.155 via relay-0b975d23.net.anydesk.com

Phase 2 — Tooling
  └─ scan.exe (AdvancedIPScanner) downloaded via bitsadmin
  └─ wsync.exe C2 beacon deployed to C:\ProgramData\
  └─ Beacon v1 fails → replaced with beacon v2 (different SHA256, same filename)

Phase 3 — Defense Evasion
  └─ kill.bat downloaded and executed
  └─ DisableAntiSpyware set in registry via reg.exe [21:03:42 UTC]
  └─ Shadow copies deleted via vssadmin

Phase 4 — Credential Theft
  └─ tasklist | findstr lsass confirms LSASS running
  └─ \Device\NamedPipe\lsass accessed [21:42 UTC]

Phase 5 — Lateral Movement + Exfiltration
  └─ as.srv.administrator authenticates to AS-SRV from 10.0.8.9
  └─ SMB enumeration of 10.1.0.154 and 10.1.0.183
  └─ st.exe creates exfil_data.zip in C:\Users\Public\

Phase 6 — Encryption + Cleanup
  └─ updater.exe staged on AS-SRV via powershell.exe
  └─ akira_readme.txt dropped [22:18:33 UTC] — encryption begins
  └─ clean.bat deletes updater.exe [~22:20 UTC]
```

---

## 🗂️ Investigation Sections

| # | Section | Difficulty | Questions |
|---|---------|------------|-----------|
| 1 | Ransom Note Analysis | Moderate | Q1–Q4 |
| 2 | Infrastructure | Moderate | Q5–Q8 |
| 3 | Defense Evasion | Hard | Q9–Q12 |
| 4 | Credential Access | Advanced | Q13–Q14 |
| 5 | Initial Access | Hard | Q15–Q18 |
| 6 | Command & Control | Hard | Q19–Q22 |
| 7 | Reconnaissance | Moderate | Q23–Q26 |
| 8 | Lateral Movement | Hard | Q27 |
| 9 | Tool Transfer | Moderate | Q28–Q29 |
| 10 | Exfiltration | Hard | Q30–Q32 |
| 11 | Ransomware Deployment | Advanced | Q33–Q38 |
| 12 | Anti-Forensics & Scope | Hard | Q39–Q40 |

---

## 🏁 All 40 Answers

<details>
<summary><strong>Section 1 — Ransom Note Analysis</strong></summary>

| Q | Question | Answer |
|---|----------|--------|
| Q1 | What ransomware group is responsible? | `Akira` |
| Q2 | What is the TOR negotiation address? | `akiral2iz6a7qgd3ayp3l6yub7xx2uep76idk3u2kollpj5z3z636bad.onion` |
| Q3 | What is the company unique ID? | `813R-QWJM-XKIJ` |
| Q4 | What file extension is added to encrypted files? | `.akira` |

</details>

<details>
<summary><strong>Section 2 — Infrastructure</strong></summary>

| Q | Question | Answer |
|---|----------|--------|
| Q5 | What domain hosted the payloads? | `sync.cloud-endpoint.net` |
| Q6 | What domain staged the ransomware? | `cdn.cloud-endpoint.net` |
| Q7 | What are the two C2 IP addresses? | `104.21.30.237, 172.67.174.46` |
| Q8 | What is the remote tool relay domain? | `relay-0b975d23.net.anydesk.com` |

</details>

<details>
<summary><strong>Section 3 — Defense Evasion</strong></summary>

| Q | Question | Answer |
|---|----------|--------|
| Q9 | What script disabled security? | `kill.bat` |
| Q10 | SHA256 of the evasion script? | `0e7da57d92eaa6bda9d0bbc24b5f0827250aa42f295fd056ded50c6e3c3fb96c` |
| Q11 | What registry value disabled Windows Defender? | `DisableAntiSpyware` |
| Q12 | What time was the registry modified? (UTC) | `21:03:42` |

</details>

<details>
<summary><strong>Section 4 — Credential Access</strong></summary>

| Q | Question | Answer |
|---|----------|--------|
| Q13 | What command enumerated processes? | `tasklist \| findstr lsass` |
| Q14 | What named pipe was accessed? | `\Device\NamedPipe\lsass` |

</details>

<details>
<summary><strong>Section 5 — Initial Access</strong></summary>

| Q | Question | Answer |
|---|----------|--------|
| Q15 | What remote access tool was pre-staged? | `AnyDesk` |
| Q16 | What directory was it executed from? | `C:\Users\Public\` |
| Q17 | What is the attacker's external IP? | `88.97.164.155` |
| Q18 | What user was compromised on AS-PC2? | `david.mitchell` |

</details>

<details>
<summary><strong>Section 6 — Command & Control</strong></summary>

| Q | Question | Answer |
|---|----------|--------|
| Q19 | What new C2 beacon was deployed? | `wsync.exe` |
| Q20 | What directory was it deployed to? | `C:\ProgramData\` |
| Q21 | SHA256 of the original beacon? | `66b876c52946f4aed47dd696d790972ff265b6f4451dab54245bc4ef1206d90b` |
| Q22 | SHA256 of the replacement beacon? | `0072ca0d0adc9a1b2e1625db4409f57fc32b5a09c414786bf08c4d8e6a073654` |

</details>

<details>
<summary><strong>Section 7 — Reconnaissance</strong></summary>

| Q | Question | Answer |
|---|----------|--------|
| Q23 | What scanner tool was used? | `scan.exe` |
| Q24 | SHA256 of the scanner? | `26d5748ffe6bd95e3fee6ce184d388a1a681006dc23a0f08d53c083c593c193b` |
| Q25 | What arguments were passed to the scanner? | `/portable "C:/Users/david.mitchell/Downloads/" /lng en_us` |
| Q26 | What two internal IPs were enumerated? | `10.1.0.154, 10.1.0.183` |

</details>

<details>
<summary><strong>Section 8 — Lateral Movement</strong></summary>

| Q | Question | Answer |
|---|----------|--------|
| Q27 | What account authenticated to AS-SRV? | `as.srv.administrator` |

</details>

<details>
<summary><strong>Section 9 — Tool Transfer</strong></summary>

| Q | Question | Answer |
|---|----------|--------|
| Q28 | What LOLBIN was first used to download tools? | `bitsadmin.exe` |
| Q29 | What PowerShell cmdlet was used as fallback? | `Invoke-WebRequest` |

</details>

<details>
<summary><strong>Section 10 — Exfiltration</strong></summary>

| Q | Question | Answer |
|---|----------|--------|
| Q30 | What staging tool compressed the data? | `st.exe` |
| Q31 | SHA256 of the staging tool? | `512a1f4ed9f512572608c729a2b89f44ea66a40433073aedcd914bd2d33b7015` |
| Q32 | What archive was created? | `exfil_data.zip` |

</details>

<details>
<summary><strong>Section 11 — Ransomware Deployment</strong></summary>

| Q | Question | Answer |
|---|----------|--------|
| Q33 | What is the ransomware filename? | `updater.exe` |
| Q34 | SHA256 of the ransomware? | `e609d070ee9f76934d73353be4ef7ff34b3ecc3a2d1e5d052140ed4cb9e4752b` |
| Q35 | What process staged the ransomware on AS-SRV? | `powershell.exe` |
| Q36 | What command deleted backup copies? | `vssadmin delete shadows /all /quiet` |
| Q37 | What process dropped the ransom note? | `updater.exe` |
| Q38 | What time was the ransom note dropped? (UTC) | `22:18:33` |

</details>

<details>
<summary><strong>Section 12 — Anti-Forensics & Scope</strong></summary>

| Q | Question | Answer |
|---|----------|--------|
| Q39 | What script deleted the ransomware? | `clean.bat` |
| Q40 | What hosts were compromised? | `as-pc2, as-srv` |

</details>

---

## 🔬 Investigation Walkthrough

### Starting Point — Find the First Alert

The only starting point was a Defender alert about `DisableAntiSpyware` being modified. That immediately pointed to device `as-pc2` and user `david.mitchell`. Everything else was built from there.

---

### Mapping the Infrastructure (Q5–Q8)

One query against `DeviceNetworkEvents` surfaced everything at once: payload domains, C2 IPs, and the AnyDesk relay. This solved four questions in a single pass.

```kql
DeviceNetworkEvents
| where Timestamp between (datetime(2026-01-27T00:00:00Z) .. datetime(2026-01-27T23:59:00Z))
| where DeviceName == "as-pc2"
| project Timestamp, RemoteUrl, RemoteIP, InitiatingProcessFileName
| order by Timestamp asc
```

---

### Finding All Dropped Tools (Q9, Q19, Q23, Q30, Q33, Q39)

Filtering `DeviceFileEvents` for new executables in suspicious directories surfaced six tools in one query: `wsync.exe`, `scan.exe`, `st.exe`, `updater.exe`, `kill.bat`, and `clean.bat`.

```kql
DeviceFileEvents
| where Timestamp between (datetime(2026-01-27T00:00:00Z) .. datetime(2026-01-27T23:59:00Z))
| where FolderPath has "ProgramData" or FolderPath has "Public"
| where ActionType == "FileCreated"
| project Timestamp, DeviceName, FileName, FolderPath, SHA256
| order by Timestamp asc
```

---

### Tracing Credential Theft (Q13–Q14)

Process events showed `tasklist | findstr lsass` at 21:11 UTC. The harder part was Q14.

Every search for processes connecting TO lsass came back empty. The breakthrough was changing the angle entirely: search for a named pipe **called** lsass instead.

```kql
DeviceEvents
| where Timestamp between (datetime(2026-01-27T20:00:00Z) .. datetime(2026-01-27T23:59:00Z))
| where DeviceName == "as-pc2"
| where ActionType == "NamedPipeEvent"
| extend PipeName = tostring(AdditionalFields.PipeName)
| where PipeName has "lsass"
| project Timestamp, InitiatingProcessFileName, PipeName
| order by Timestamp asc
```

> **Q14 was the hardest question in the investigation.** The framing of the problem was wrong. Searching for connections TO lsass never worked. The pipe itself had to be the target.

---

### Following Lateral Movement (Q27)

`DeviceLogonEvents` on `as-srv` (not as-pc2) showed `as.srv.administrator` logging in from `10.0.8.9` using credentials pulled from the LSASS dump.

```kql
DeviceLogonEvents
| where Timestamp between (datetime(2026-01-27T20:00:00Z) .. datetime(2026-01-27T23:59:00Z))
| where DeviceName == "as-srv"
| project Timestamp, AccountName, RemoteIP, LogonType
| order by Timestamp asc
```

---

### Finding the Ransomware Staging Domain (Q6)

`cdn.cloud-endpoint.net` was the hardest infrastructure question. It returned zero results on every query scoped to `as-pc2`. The fix was a broad cross-table search across all devices simultaneously.

```kql
search in (DeviceProcessEvents, DeviceFileEvents, DeviceNetworkEvents,
           DeviceEvents, DeviceImageLoadEvents, DeviceRegistryEvents) 'cdn.cloud-endpoint'
| where Timestamp between (datetime(2026-01-27T00:00:00Z) .. datetime(2026-01-27T23:59:00Z))
| project Timestamp, $table, DeviceName, FileName, ProcessCommandLine, RemoteUrl
| order by Timestamp asc
```

The domain only appeared in `DeviceNetworkEvents` on `as-srv`. Restricting to one device would have missed it entirely.

---

### Confirming Scope (Q40)

Scope was confirmed by searching for ransom note drops across all devices, not by looking for the ransomware binary. The binary was deleted. The note was not.

```kql
DeviceFileEvents
| where Timestamp between (datetime(2026-01-27T00:00:00Z) .. datetime(2026-01-27T23:59:00Z))
| where FileName has "akira_readme"
| summarize count() by DeviceName
| order by count_ desc
```

Only `as-pc2` and `as-srv` had ransom notes. Those are the confirmed compromised hosts.

---

## 🌐 Indicators of Compromise

| Indicator | Type | Role |
|-----------|------|------|
| `sync.cloud-endpoint.net` | Domain | Initial tool delivery |
| `cdn.cloud-endpoint.net` | Domain | Ransomware staging |
| `104.21.30.237` | IP | C2 server (Cloudflare proxied) |
| `172.67.174.46` | IP | C2 server (Cloudflare proxied) |
| `relay-0b975d23.net.anydesk.com` | Domain | AnyDesk backdoor relay |
| `88.97.164.155` | IP | Attacker external IP |
| `10.1.0.154` | Internal IP | SMB enumeration target |
| `10.1.0.183` | Internal IP | SMB enumeration target |

---

## 💡 Key Lessons

1. **Check your log retention before anything else.** MDE had nothing. Azure Log Analytics had everything. Knowing which platform to use early saves hours.

2. **When a targeted search fails, go broad.** Cross-table searches across all devices solved Q6, Q8, and Q14 after targeted queries returned nothing.

3. **Never restrict to one device.** Q6 lived on `as-srv`. Q14 lived on `as-pc2`. Assuming one host tells the whole story is the fastest way to miss critical evidence.

4. **Change your angle when stuck.** Q14 failed repeatedly when searching for processes connecting to lsass. The answer was a pipe named lsass. The framing of the question matters.

5. **Same filename, different hash means tool replacement.** Two `wsync.exe` files with different SHA256 values confirmed the C2 beacon was swapped mid-operation.

6. **Scope with the ransom note, not the binary.** The ransomware binary was deleted. The ransom note was not. Searching for `akira_readme` drops gave the correct host count.

7. **The real detection window was 21:03:42.** That is when `DisableAntiSpyware` was set. Defenders had 75 minutes before encryption began. The ransom note is already too late.

---

## 🛠️ Tools & Platform

- **Microsoft Sentinel** — SIEM platform
- **Azure Log Analytics** — Primary investigation platform (LAW-Cyber-Range workspace)
- **KQL (Kusto Query Language)** — All queries written and executed in KQL
- **MDE Telemetry** — DeviceProcessEvents, DeviceFileEvents, DeviceNetworkEvents, DeviceRegistryEvents, DeviceEvents, DeviceLogonEvents
- **MITRE ATT&CK** — Framework used for technique mapping throughout

---

## 🙏 Credits

- **SancLogic** — [sanclogic.com](https://sanclogic.com) — Cyber range platform and challenge design
- **The Buyer** — Part of the SancLogic Security Operations Centre Cyber Range series

---

*40 questions. 12 sections. Complete Akira ransomware incident response. — Destiny Furlong, March 2026*
