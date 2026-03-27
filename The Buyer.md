<p align="center">
  <img
    src="https://github.com/user-attachments/assets/337bb215-8833-4653-b570-93c443bd9c11"
    width="1200"
    alt="Threat Hunt Cover Image"
  />
</p>

# 🛡️ Threat Hunt Report — Akira Ransomware: Ashford Sterling Recruitment

**Analyst:** Destiny Furlong
**Platform:** Microsoft Sentinel + Azure Log Analytics
**Incident Date:** January 27, 2026
**Investigation Date:** March 2026
**Severity:** Critical

---

## 📌 Executive Summary

On January 27, 2026, a ransomware affiliate deployed Akira ransomware across the network of Ashford Sterling Recruitment. The attacker used pre-staged access from a prior intrusion, requiring no new initial compromise. Two hosts were confirmed encrypted: `as-pc2` and `as-srv`.

The attacker disabled Windows Defender, stole credentials from LSASS, moved laterally to a domain server, exfiltrated data, and encrypted files — all within approximately three hours. The ransomware binary was deleted after execution to slow forensic recovery.

This investigation was completed 47 days after the incident. Microsoft Defender for Endpoint logs had already expired. All findings were derived from Azure Log Analytics using KQL against retained MDE telemetry.

---

## 🧭 Scope & Environment

| Field | Detail |
|-------|--------|
| Organization | Ashford Sterling Recruitment |
| SIEM | Microsoft Sentinel |
| Query Platform | Azure Log Analytics (LAW-Cyber-Range workspace) |
| Data Sources | DeviceProcessEvents, DeviceFileEvents, DeviceNetworkEvents, DeviceRegistryEvents, DeviceEvents, DeviceLogonEvents |
| Incident Date | January 27, 2026 |
| Hosts Compromised | as-pc2, as-srv |
| Compromised Account | david.mitchell |
| Ransomware Family | Akira |

---

## 📚 Table of Contents

- [🧬 MITRE ATT&CK Summary](#-mitre-attck-summary)
- [⚔️ Attack Chain Overview](#%EF%B8%8F-attack-chain-overview)
- [🔍 Phase Analysis](#-phase-analysis)
  - [Phase 1 — Re-Entry via Pre-Staged Backdoor](#phase-1--re-entry-via-pre-staged-backdoor)
  - [Phase 2 — Tooling and C2 Establishment](#phase-2--tooling-and-c2-establishment)
  - [Phase 3 — Defense Evasion](#phase-3--defense-evasion)
  - [Phase 4 — Credential Theft](#phase-4--credential-theft)
  - [Phase 5 — Lateral Movement and Exfiltration](#phase-5--lateral-movement-and-exfiltration)
  - [Phase 6 — Ransomware Deployment and Cleanup](#phase-6--ransomware-deployment-and-cleanup)
- [🌐 Indicators of Compromise](#-indicators-of-compromise)
- [🚨 Detection Gaps & Recommendations](#-detection-gaps--recommendations)
- [🧾 Final Assessment](#-final-assessment)
- [📎 Analyst Notes](#-analyst-notes)

---

## 🧬 MITRE ATT&CK Summary

| Technique | MITRE ID | Phase | Priority |
|-----------|----------|-------|----------|
| External Remote Services | T1133 | Initial Access | High |
| Valid Accounts | T1078 | Persistence | High |
| Impair Defenses | T1562 | Defense Evasion | Critical |
| Modify Registry | T1112 | Defense Evasion | Critical |
| Masquerading | T1036 | Defense Evasion | Medium |
| Indicator Removal | T1070 | Defense Evasion | High |
| OS Credential Dumping | T1003 | Credential Access | Critical |
| Network Share Discovery | T1135 | Discovery | Medium |
| Remote Services | T1021 | Lateral Movement | High |
| Ingress Tool Transfer | T1105 | Command & Control | High |
| Remote Access Tools | T1219 | Command & Control | High |
| Archive Collected Data | T1560 | Collection | High |
| Exfiltration Over C2 Channel | T1041 | Exfiltration | Critical |
| Data Encrypted for Impact | T1486 | Impact | Critical |
| Inhibit System Recovery | T1490 | Impact | Critical |

---

## ⚔️ Attack Chain Overview

```
Phase 1 — Re-Entry
  └─ Pre-staged AnyDesk backdoor activated from C:\Users\Public\
  └─ Attacker connects from 88.97.164.155 via AnyDesk relay

Phase 2 — Tooling & C2
  └─ scan.exe (AdvancedIPScanner) downloaded via bitsadmin
  └─ wsync.exe C2 beacon dropped to C:\ProgramData\
  └─ Beacon v1 fails — replaced with beacon v2 (different hash, same filename)

Phase 3 — Defense Evasion
  └─ kill.bat downloaded and executed to impair Defender
  └─ DisableAntiSpyware set via reg.exe [21:03:42 UTC]
  └─ Shadow copies deleted via vssadmin

Phase 4 — Credential Theft
  └─ tasklist | findstr lsass confirms LSASS is running
  └─ \Device\NamedPipe\lsass accessed [21:42 UTC]

Phase 5 — Lateral Movement & Exfiltration
  └─ as.srv.administrator authenticates to AS-SRV using stolen credentials
  └─ SMB enumeration of internal targets
  └─ st.exe archives data into exfil_data.zip for staging

Phase 6 — Encryption & Cleanup
  └─ updater.exe staged on AS-SRV via powershell.exe
  └─ akira_readme.txt dropped [22:18:33 UTC] — encryption begins
  └─ clean.bat deletes updater.exe to hinder forensics
```

---

## 🔍 Phase Analysis

---

### Phase 1 — Re-Entry via Pre-Staged Backdoor

The attacker did not need to re-compromise the environment. AnyDesk had been pre-staged in `C:\Users\Public\` during a prior intrusion. It was activated at 7:15 PM and used to connect from external IP `88.97.164.155` at 7:21 PM through the relay domain `relay-0b975d23.net.anydesk.com`.

The compromised account was `david.mitchell` on `as-pc2`. Initial access for this intrusion traces back to Guacamole RDP sessions originating from `10.0.8.5` and `10.0.8.8` earlier in the day.

**Why it matters:** Pre-staged remote access tools persist across incident response efforts if not explicitly hunted and removed. A prior compromise that is closed without auditing all dropped tools leaves a direct path back in.

```kql
DeviceNetworkEvents
| where Timestamp between (datetime(2026-01-27T00:00:00Z) .. datetime(2026-01-27T23:59:00Z))
| where RemoteUrl has "anydesk.com"
| project Timestamp, DeviceName, RemoteIP, RemoteUrl, InitiatingProcessFileName
| order by Timestamp asc
```

---

### Phase 2 — Tooling and C2 Establishment

Two tools were deployed shortly after re-entry.

`scan.exe`, a renamed copy of AdvancedIPScanner, was downloaded via `bitsadmin.exe` from `sync.cloud-endpoint.net`. It was executed with the `/portable` flag to write results locally and avoid leaving registry traces. It identified two internal targets at `10.1.0.154` and `10.1.0.183`.

`wsync.exe` was deployed to `C:\ProgramData\` as the primary C2 beacon. The first version failed to establish a reliable connection. The attacker replaced it with a second binary sharing the same filename but a different SHA256 hash. Both communicated with C2 infrastructure proxied through Cloudflare.

| Artifact | SHA256 |
|----------|--------|
| scan.exe | `26d5748ffe6bd95e3fee6ce184d388a1a681006dc23a0f08d53c083c593c193b` |
| wsync.exe (v1) | `66b876c52946f4aed47dd696d790972ff265b6f4451dab54245bc4ef1206d90b` |
| wsync.exe (v2) | `0072ca0d0adc9a1b2e1625db4409f57fc32b5a09c414786bf08c4d8e6a073654` |

**Why it matters:** Two binaries with the same name and different hashes in a short window confirm tool replacement. This is detectable when file creation events are combined with hash-based alerting on sensitive directories.

```kql
DeviceFileEvents
| where Timestamp between (datetime(2026-01-27T00:00:00Z) .. datetime(2026-01-27T23:59:00Z))
| where FolderPath has "ProgramData" or FolderPath has "Public"
| where ActionType == "FileCreated"
| project Timestamp, DeviceName, FileName, FolderPath, SHA256
| order by Timestamp asc
```

---

### Phase 3 — Defense Evasion

Three actions were taken in sequence to remove defensive controls before encryption.

First, `kill.bat` was downloaded via `bitsadmin.exe` from `sync.cloud-endpoint.net` and executed to impair Windows Defender. Its SHA256 is `0e7da57d92eaa6bda9d0bbc24b5f0827250aa42f295fd056ded50c6e3c3fb96c`.

Second, `reg.exe` set `DisableAntiSpyware` to `1` under `HKLM\SOFTWARE\Policies\Microsoft\Windows Defender` at **21:03:42 UTC**. This is the earliest high-confidence detection opportunity in the entire incident. Defenders had approximately 75 minutes from this moment before encryption began.

Third, `vssadmin delete shadows /all /quiet` was executed to remove all Volume Shadow Copies, preventing file recovery through local backup.

**Why it matters:** All three actions occurred before any ransomware activity. Detection at the registry modification stage alone would have provided a meaningful response window.

```kql
DeviceRegistryEvents
| where Timestamp between (datetime(2026-01-27T20:00:00Z) .. datetime(2026-01-27T23:59:00Z))
| where RegistryValueName == "DisableAntiSpyware"
| project Timestamp, DeviceName, RegistryKey, RegistryValueName, RegistryValueData
```

```kql
DeviceProcessEvents
| where ProcessCommandLine has "vssadmin" and ProcessCommandLine has "delete shadows"
| project Timestamp, DeviceName, ProcessCommandLine
```

---

### Phase 4 — Credential Theft

The attacker confirmed LSASS was running at 21:11 UTC using `tasklist | findstr lsass`. At 21:42 UTC, the named pipe `\Device\NamedPipe\lsass` was accessed, indicating credential extraction from the LSASS process.

The credentials stolen here were used minutes later to authenticate to `as-srv` as `as.srv.administrator`.

**Why it matters:** This step was the bridge between the workstation compromise and the server compromise. Without the stolen credentials, lateral movement to a privileged system would not have been possible.

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

> **Investigative note:** Queries targeting processes connecting TO lsass returned no results. The correct approach was searching for a named pipe called lsass rather than connections directed at the process itself.

---

### Phase 5 — Lateral Movement and Exfiltration

Using credentials stolen from the LSASS dump, the account `as.srv.administrator` authenticated to `as-srv` from `10.0.8.9` via network and remote interactive logon. This pivoted the attacker from a workstation to a domain server.

SMB enumeration of `10.1.0.154` and `10.1.0.183` followed.

`st.exe` was then used to compress sensitive data into `exfil_data.zip`, staged in `C:\Users\Public\` for exfiltration over the active C2 channel.

| Artifact | SHA256 |
|----------|--------|
| st.exe | `512a1f4ed9f512572608c729a2b89f44ea66a40433073aedcd914bd2d33b7015` |

**Why it matters:** Exfiltration before encryption is standard Akira tradecraft. The group uses stolen data as leverage in double extortion: pay the ransom, or the data gets published. The encryption itself is only one half of the impact.

```kql
DeviceLogonEvents
| where Timestamp between (datetime(2026-01-27T20:00:00Z) .. datetime(2026-01-27T23:59:00Z))
| where DeviceName == "as-srv"
| project Timestamp, AccountName, RemoteIP, LogonType
| order by Timestamp asc
```

---

### Phase 6 — Ransomware Deployment and Cleanup

`updater.exe` was staged on `as-srv` by `powershell.exe` at 22:15 UTC, downloaded from `cdn.cloud-endpoint.net`. The binary was disguised as a Windows Update process.

At **22:18:33 UTC**, `updater.exe` dropped `akira_readme.txt`, marking the start of file encryption. All encrypted files received the `.akira` extension. The ransom note directed the victim to a TOR negotiation address and included a unique company ID.

Two minutes after encryption began, `clean.bat` executed and deleted `updater.exe` from the host.

| Artifact | SHA256 |
|----------|--------|
| updater.exe | `e609d070ee9f76934d73353be4ef7ff34b3ecc3a2d1e5d052140ed4cb9e4752b` |

| Ransom Detail | Value |
|---------------|-------|
| Group | Akira |
| File Extension | `.akira` |
| TOR Address | `akiral2iz6a7qgd3ayp3l6yub7xx2uep76idk3u2kollpj5z3z636bad.onion` |
| Victim ID | `813R-QWJM-XKIJ` |

**Why it matters:** Deleting the binary after execution removes the primary forensic artifact. Without it, defenders lose the ability to analyze the encryption implementation or identify decryption paths without paying the ransom. Scope confirmation required searching for ransom note drops rather than the binary itself, as the binary was gone.

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
| `104.21.30.237` | IP Address | C2 server (Cloudflare proxied) |
| `172.67.174.46` | IP Address | C2 server (Cloudflare proxied) |
| `relay-0b975d23.net.anydesk.com` | Domain | AnyDesk backdoor relay |
| `88.97.164.155` | IP Address | Attacker external IP |
| `10.1.0.154` | Internal IP | SMB enumeration target |
| `10.1.0.183` | Internal IP | SMB enumeration target |

| File | SHA256 |
|------|--------|
| kill.bat | `0e7da57d92eaa6bda9d0bbc24b5f0827250aa42f295fd056ded50c6e3c3fb96c` |
| wsync.exe (v1) | `66b876c52946f4aed47dd696d790972ff265b6f4451dab54245bc4ef1206d90b` |
| wsync.exe (v2) | `0072ca0d0adc9a1b2e1625db4409f57fc32b5a09c414786bf08c4d8e6a073654` |
| scan.exe | `26d5748ffe6bd95e3fee6ce184d388a1a681006dc23a0f08d53c083c593c193b` |
| st.exe | `512a1f4ed9f512572608c729a2b89f44ea66a40433073aedcd914bd2d33b7015` |
| updater.exe | `e609d070ee9f76934d73353be4ef7ff34b3ecc3a2d1e5d052140ed4cb9e4752b` |

---

## 🚨 Detection Gaps & Recommendations

### Observed Gaps

- Log retention was insufficient. MDE Advanced Hunting retains 30 days of data. This incident was 47 days old. Azure Log Analytics retained the telemetry, but the gap was not known at investigation start.
- AnyDesk was not removed after the prior compromise. It remained active and provided immediate re-entry with no new exploit required.
- No alerts fired on `bitsadmin.exe` downloading tools from external domains.
- The `DisableAntiSpyware` registry modification at 21:03:42 UTC generated no real-time alert despite being the clearest pre-encryption signal in the timeline.
- Shadow copy deletion went undetected.

### Recommendations

- Extend endpoint telemetry retention to a minimum of 90 days. Log Analytics should be configured as the primary retention platform.
- After any incident, audit all user-writable directories for unapproved remote access tools and staged executables before closing the case.
- Create detection rules for `bitsadmin.exe` and `Invoke-WebRequest` downloading from external domains to user-writable paths.
- Alert on any modification to Windows Defender registry keys. Treat `DisableAntiSpyware` being set to `1` as a critical-priority indicator.
- Alert on `vssadmin delete shadows` without exception. There is no legitimate use for this command in most environments.
- Restrict privileged account logons to approved Privileged Access Workstations to limit the impact of credential theft.

---

## 🧾 Final Assessment

This was a disciplined, well-structured double extortion ransomware operation. The attacker used pre-staged access to avoid re-compromise, replaced a failing C2 tool mid-operation, exfiltrated data before encrypting, and cleaned up the ransomware binary after execution. Every phase showed preparation.

The earliest viable detection point was **21:03:42 UTC** when `DisableAntiSpyware` was set in the registry. At that moment, defenders had approximately 75 minutes before encryption began. No alert fired.

All attacker tooling used native Windows utilities or renamed legitimate software. `bitsadmin`, `reg.exe`, `vssadmin`, and `AnyDesk` are not inherently malicious. Detection in this environment required behavioral context — not just file or process names — and that context was not being monitored.

The full attack chain was reconstructed from initial access through anti-forensics using only Azure Log Analytics telemetry. Both compromised hosts were confirmed. All attacker infrastructure was identified.

---

## 📎 Analyst Notes

- All findings derived from Azure Log Analytics. MDE Advanced Hunting had no data due to 30-day retention limit.
- Scope was confirmed using ransom note drops, not ransomware binary presence. The binary was deleted. The note was not.
- The named pipe access (LSASS credential theft) required searching for a pipe named lsass rather than processes connecting to the lsass process. Standard approaches returned no results.
- `cdn.cloud-endpoint.net` only appeared in `DeviceNetworkEvents` on `as-srv`. Cross-table, cross-device search was required to surface it.
- Two `wsync.exe` files with different SHA256 hashes confirmed C2 beacon replacement mid-operation.
- Techniques mapped to MITRE ATT&CK throughout. All KQL queries are documented inline and reproducible against the original dataset.
- Platform: SancLogic Cyber Range | [sanclogic.com](https://sanclogic.com)

---
