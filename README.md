# Colonial Pipeline Ransomware Attack : Simulation & Incident Response Case Study

A simulated ransomware attack based on the real-world 2021 Colonial Pipeline breach, covering the 
full attack chain, detection, forensic analysis, and incident response planning ; mapped to 
MITRE ATT&CK and NIST frameworks.

## Overview

This project replicates the credential-based intrusion and ransomware deployment used by the 
DarkSide ransomware group in the 2021 Colonial Pipeline attack, simulated in a controlled lab 
environment (Kali Linux attacker, Windows Server 2008 victim). The goal was to understand 
attacker TTPs first-hand and build a matching detection, forensics, and response capability.

## Threat Background

The real Colonial Pipeline attack stemmed from a legacy VPN account without MFA, allowing DarkSide 
(a Russia-linked Ransomware-as-a-Service group) to gain remote access with stolen credentials. 
IT systems (billing, scheduling) were encrypted, forcing a full pipeline shutdown that disrupted 
~45% of the East Coast's fuel supply,  despite no direct compromise of operational technology (OT).

## Attack Simulation

Simulated using the MITRE ATT&CK framework as a reference, covering:

1. **Reconnaissance** — Nmap TCP/service scan to identify open ports and services on the target
2. **Credential Theft** — simulated phishing/login page (DVWA) with Burp Suite intercepting and 
   capturing login credentials (T1078, T1566.001)
3. **Lateral Movement** — RDP access to the victim machine using stolen credentials (T1021.001)
4. **Ransomware Deployment** — custom batch script simulating file encryption (renaming files, 
   dropping a ransom note) (T1486)
5. **Persistence** — registry Run key added to auto-execute the payload on reboot (T1547.001)
6. **Data Exfiltration** — sensitive file copied from victim to attacker machine via a shared 
   folder (T1567.002)

## Detection

- **Windows Event Viewer** — identified Event ID 4624 (Logon Type 10), confirming unauthorized 
  RDP access from the attacker's IP
- **Registry Inspection** — found the persistence entry under `HKCU\Software\Microsoft\Windows\CurrentVersion\Run`
- **Wireshark** — captured and filtered RDP session traffic (`tcp.port == 3389`), confirming the 
  full handshake and session between attacker and victim IPs

## Forensic Analysis

Post-event packet analysis using Wireshark to reconstruct the attack timeline:
- Identified the RDP session start time and correlated it with the ransom note's file timestamp 
  (ransomware executed ~3 minutes after RDP login)
- Found the `mstshash=Administrator` cookie in the TCP stream, confirming credential-based login (T1078)

## Incident Response Plan

Structured around the NIST IR lifecycle:

- **Preparation** — MFA enforcement, credential hygiene, account lifecycle audits
- **Detection & Analysis** — monitoring for abnormal RDP logins, registry changes, and lateral movement
- **Containment, Eradication & Recovery** — isolating infected systems, removing persistence 
  mechanisms, restoring from offline backups
- **Post-Incident Activity** — after-action review, policy updates, stakeholder reporting

Also includes a role-based **Cyber Playbook** (IR Lead, Security Analyst, Forensic Investigator, 
Network Engineer, Legal/Compliance, PR, Backup/Recovery, Executive Management) mapping 
responsibilities to each IR phase.

## Prevention Recommendations

- Enforce MFA on all remote access and privileged accounts
- Disable or tightly restrict RDP; use VPN tunneling with geo-IP/time restrictions
- Deploy EDR and SIEM for behavioral detection and centralized log correlation
- Apply Principle of Least Privilege and regularly audit/deactivate stale accounts
- Maintain offline, regularly tested backups
- Align with NIST CSF, ISO/IEC 27001, and MITRE ATT&CK for structured security posture

## Tools Used

Kali Linux, Windows Server 2008, Nmap, Burp Suite, xfreerdp, Wireshark, Windows Event Viewer, 
Registry Editor

## Screenshots

*(Add key screenshots — Nmap scan, Burp Suite credential capture, RDP session, ransomware 
encryption evidence, Event Viewer log, Wireshark analysis)*

---
