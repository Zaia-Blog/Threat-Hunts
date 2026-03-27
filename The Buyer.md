<p align="center">
  <svg xmlns="http://www.w3.org/2000/svg" width="1200" height="300" viewBox="0 0 1200 300">
    <defs>
      <linearGradient id="bg" x1="0%" y1="0%" x2="100%" y2="100%">
        <stop offset="0%" style="stop-color:#1a0033;stop-opacity:1" />
        <stop offset="50%" style="stop-color:#3d0066;stop-opacity:1" />
        <stop offset="100%" style="stop-color:#1a0040;stop-opacity:1" />
      </linearGradient>
      <linearGradient id="glow" x1="0%" y1="0%" x2="100%" y2="0%">
        <stop offset="0%" style="stop-color:#ff69b4;stop-opacity:0" />
        <stop offset="50%" style="stop-color:#ff69b4;stop-opacity:0.15" />
        <stop offset="100%" style="stop-color:#ff69b4;stop-opacity:0" />
      </linearGradient>
    </defs>
    <rect width="1200" height="300" fill="url(#bg)" rx="12"/>
    <rect width="1200" height="300" fill="url(#glow)" rx="12"/>
    <line x1="0" y1="60" x2="1200" y2="60" stroke="#ff69b4" stroke-opacity="0.08" stroke-width="1"/>
    <line x1="0" y1="120" x2="1200" y2="120" stroke="#ff69b4" stroke-opacity="0.08" stroke-width="1"/>
    <line x1="0" y1="180" x2="1200" y2="180" stroke="#ff69b4" stroke-opacity="0.08" stroke-width="1"/>
    <line x1="0" y1="240" x2="1200" y2="240" stroke="#ff69b4" stroke-opacity="0.08" stroke-width="1"/>
    <line x1="200" y1="0" x2="200" y2="300" stroke="#b44fff" stroke-opacity="0.08" stroke-width="1"/>
    <line x1="400" y1="0" x2="400" y2="300" stroke="#b44fff" stroke-opacity="0.08" stroke-width="1"/>
    <line x1="600" y1="0" x2="600" y2="300" stroke="#b44fff" stroke-opacity="0.08" stroke-width="1"/>
    <line x1="800" y1="0" x2="800" y2="300" stroke="#b44fff" stroke-opacity="0.08" stroke-width="1"/>
    <line x1="1000" y1="0" x2="1000" y2="300" stroke="#b44fff" stroke-opacity="0.08" stroke-width="1"/>
    <circle cx="80" cy="40" r="1.5" fill="#ff69b4" opacity="0.7"/>
    <circle cx="200" cy="25" r="1" fill="#da70d6" opacity="0.6"/>
    <circle cx="350" cy="55" r="2" fill="#ff69b4" opacity="0.5"/>
    <circle cx="520" cy="20" r="1.5" fill="#b44fff" opacity="0.7"/>
    <circle cx="700" cy="45" r="1" fill="#ff69b4" opacity="0.6"/>
    <circle cx="900" cy="30" r="2" fill="#da70d6" opacity="0.5"/>
    <circle cx="1050" cy="50" r="1.5" fill="#ff69b4" opacity="0.7"/>
    <circle cx="1150" cy="22" r="1" fill="#b44fff" opacity="0.6"/>
    <circle cx="140" cy="260" r="1.5" fill="#da70d6" opacity="0.5"/>
    <circle cx="460" cy="275" r="1" fill="#ff69b4" opacity="0.6"/>
    <circle cx="760" cy="265" r="2" fill="#b44fff" opacity="0.5"/>
    <circle cx="980" cy="280" r="1.5" fill="#ff69b4" opacity="0.6"/>
    <rect x="0" y="0" width="1200" height="3" fill="#ff69b4" opacity="0.6" rx="2"/>
    <rect x="0" y="297" width="1200" height="3" fill="#b44fff" opacity="0.6" rx="2"/>
    <rect x="60" y="60" width="3" height="180" fill="#ff69b4" opacity="0.4" rx="2"/>
    <rect x="1137" y="60" width="3" height="180" fill="#b44fff" opacity="0.4" rx="2"/>
    <text x="600" y="130" font-family="'Courier New', monospace" font-size="48" font-weight="bold" fill="#ffffff" text-anchor="middle" opacity="0.95">✦ THREAT HUNT REPORT ✦</text>
    <text x="600" y="178" font-family="'Courier New', monospace" font-size="22" fill="#ff69b4" text-anchor="middle" opacity="0.9">The Buyer — Akira Ransomware Incident Response</text>
    <text x="600" y="230" font-family="'Courier New', monospace" font-size="14" fill="#da70d6" text-anchor="middle" opacity="0.75">SancLogic Cyber Range  ·  Advanced  ·  40/40 ✓  ·  Destiny Furlong</text>
    <text x="80" y="100" font-family="serif" font-size="20" fill="#ff69b4" opacity="0.3">✦</text>
    <text x="1100" y="100" font-family="serif" font-size="20" fill="#b44fff" opacity="0.3">✦</text>
    <text x="80" y="220" font-family="serif" font-size="20" fill="#b44fff" opacity="0.3">✦</text>
    <text x="1100" y="220" font-family="serif" font-size="20" fill="#ff69b4" opacity="0.3">✦</text>
  </svg>
</p>

# 🔍 The Buyer — Akira Ransomware Incident Response

**SancLogic Cyber Range &nbsp;✦&nbsp; Advanced Difficulty &nbsp;✦&nbsp; 40/40 Questions Solved**

> *"A ransomware affiliate returned to Ashford Sterling Recruitment using access pre-staged during a prior compromise. The threat actor deployed Akira ransomware across two hosts."*

---

## 💜 Overview

This repository documents a complete incident response investigation into an **Akira ransomware** attack on a fictional recruitment firm, **Ashford Sterling Recruitment**. This was completed as part of the **SancLogic Cyber Range** two-part series (*The Broker* → *The Buyer*).

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

The incident happened **47 days before** the investigation. Microsoft Defender for Endpoint only keeps Advanced Hunting logs for 30 days. By the time I started, MDE had nothing. **Azure Log Analytics was the only platform that still had the data** — and I did not know that until I was already mid-investigation.

Every query decision after that point was shaped by that constraint.

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

## ⚔️ Attack Chain

The attacker never had to break in again. Access was already sitting there from a prior compromise. The operation ran for about three hours and moved through six phases.

```
Phase 1 — Re-Entry
  └─ AnyDesk pre-staged in C:\Users\Public\ (no new exploit needed)
  └─ Attacker connects from 88.97.164.155 via relay-0b975d23.net.anydesk.com

Phase 2 — Tooling
  └─ scan.exe (AdvancedIPScanner) pulled via bitsadmin from sync.cloud-endpoint.net
  └─ wsync.exe C2 beacon dropped to C:\ProgramData\
  └─ Beacon v1 fails → swapped with beacon v2 (different SHA256, same filename)

Phase 3 — Defense Evasion
  └─ kill.bat downloaded and executed
  └─ DisableAntiSpyware set via reg.exe [21:03:42 UTC] — Defender is gone
  └─ Shadow copies deleted via vssadmin — no local recovery possible

Phase 4 — Credential Theft
  └─ tasklist | findstr lsass — attacker confirms LSASS is running
  └─ \Device\NamedPipe\lsass accessed [21:42 UTC] — credentials stolen

Phase 5 — Lateral Movement + Exfiltration
  └─ as.srv.administrator authenticates to AS-SRV using stolen creds
  └─ SMB enumeration of 10.1.0.154 and 10.1.0.183
  └─ st.exe creates exfil_data.zip in C:\Users\Public\

Phase 6 — Encryption + Cleanup
  └─ updater.exe staged on AS-SRV via powershell.exe
  └─ akira_readme.txt dropped [22:18:33 UTC] — encryption starts
  └─ clean.bat deletes updater.exe [~22:20 UTC] — evidence removed
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

## 🔬 How I Solved It

### Starting Point — Follow the Alert

The only entry point was a Defender alert about `DisableAntiSpyware` being modified. That immediately gave me the device (`as-pc2`) and the user (`david.mitchell`). I built everything outward from there.

---

### Mapping Infrastructure in One Pass (Q5–Q8)

One query against `DeviceNetworkEvents` gave me everything at once: both payload domains, both C2 IPs, and the AnyDesk relay. Four questions solved in a single run.

```kql
DeviceNetworkEvents
| where Timestamp between (datetime(2026-01-27T00:00:00Z) .. datetime(2026-01-27T23:59:00Z))
| where DeviceName == "as-pc2"
| project Timestamp, RemoteUrl, RemoteIP, InitiatingProcessFileName
| order by Timestamp asc
```

---

### Finding Every Dropped Tool at Once (Q9, Q19, Q23, Q30, Q33, Q39)

Filtering `DeviceFileEvents` for newly created executables in suspicious directories surfaced six tools in one query: `wsync.exe`, `scan.exe`, `st.exe`, `updater.exe`, `kill.bat`, and `clean.bat`. Six questions, one query.

```kql
DeviceFileEvents
| where Timestamp between (datetime(2026-01-27T00:00:00Z) .. datetime(2026-01-27T23:59:00Z))
| where FolderPath has "ProgramData" or FolderPath has "Public"
| where ActionType == "FileCreated"
| project Timestamp, DeviceName, FileName, FolderPath, SHA256
| order by Timestamp asc
```

---

### The Hardest Question — Credential Theft via Named Pipe (Q14)

This one stopped me for a while. Every search I ran looking for processes connecting TO lsass came back empty. Nothing. The breakthrough came from flipping the question entirely: instead of looking for something connecting to lsass, I searched for a pipe that was **named** lsass.

That single change in perspective solved it.

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

> **Lesson learned:** when you are stuck, question the framing, not just the query. The angle matters as much as the syntax.

---

### The Sneakiest Infrastructure Question (Q6)

`cdn.cloud-endpoint.net` returned zero results every time I searched `as-pc2`. I thought I had the wrong domain. I ran the same query five different ways and got nothing.

The fix was going completely broad — search every table, every device, all at once.

```kql
search in (DeviceProcessEvents, DeviceFileEvents, DeviceNetworkEvents,
           DeviceEvents, DeviceImageLoadEvents, DeviceRegistryEvents) 'cdn.cloud-endpoint'
| where Timestamp between (datetime(2026-01-27T00:00:00Z) .. datetime(2026-01-27T23:59:00Z))
| project Timestamp, $table, DeviceName, FileName, ProcessCommandLine, RemoteUrl
| order by Timestamp asc
```

The domain only existed in `DeviceNetworkEvents` on `as-srv`. It was never on `as-pc2` at all. Never assume the first device tells the whole story.

---

### Lateral Movement (Q27)

Once I had the stolen credentials, I switched my query scope to `as-srv` and ran `DeviceLogonEvents`. The account `as.srv.administrator` showed up immediately, logging in from `10.0.8.9` using the credentials pulled from the LSASS dump.

```kql
DeviceLogonEvents
| where Timestamp between (datetime(2026-01-27T20:00:00Z) .. datetime(2026-01-27T23:59:00Z))
| where DeviceName == "as-srv"
| project Timestamp, AccountName, RemoteIP, LogonType
| order by Timestamp asc
```

---

### Confirming Scope the Right Way (Q40)

The ransomware binary was deleted. Searching for it would have given me the wrong answer. Instead I searched for the ransom note drop across all devices. Only `as-pc2` and `as-srv` had it. Those are the confirmed compromised hosts.

```kql
DeviceFileEvents
| where Timestamp between (datetime(2026-01-27T00:00:00Z) .. datetime(2026-01-27T23:59:00Z))
| where FileName has "akira_readme"
| summarize count() by DeviceName
| order by count_ desc
```

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

## 💡 What I Learned

**1. Check your log retention before you write a single query.**
MDE had nothing. Azure Log Analytics had everything. Knowing your platform saves hours.

**2. When a targeted search fails, go broad.**
Cross-table searches across all devices cracked Q6, Q8, and Q14 after targeted queries kept returning empty.

**3. Never lock yourself to one device.**
Q6 was on `as-srv`. Q14 was on `as-pc2`. The story is always spread across hosts.

**4. Change your angle, not just your syntax.**
Q14 failed until I stopped asking "what connected to lsass" and started asking "what pipe is named lsass." The framing of the question is everything.

**5. Same filename, different hash means a tool was replaced.**
Two `wsync.exe` files with different SHA256 values confirmed the attacker swapped their C2 beacon mid-operation.

**6. Scope with the ransom note, not the binary.**
The ransomware binary was deleted. The ransom note was not. Always search for the artifact that cannot be cleaned up.

**7. The real detection window was 21:03:42.**
That is when `DisableAntiSpyware` was set. Seventy-five minutes before encryption. The ransom note is already too late.

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

<p align="center">
  <em>40 questions &nbsp;✦&nbsp; 12 sections &nbsp;✦&nbsp; Complete Akira ransomware incident response</em><br/>
  <strong>Destiny Furlong &nbsp;✦&nbsp; March 2026</strong>
</p>
