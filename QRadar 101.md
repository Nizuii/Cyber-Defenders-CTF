# QRadar 101 Challenge – Incident Investigation Report

## a) QRadar 101 Challenge

### i) Challenge Overview and Objectives

The **QRadar 101 Challenge** simulates a real-world security incident involving a compromised financial organization. Multiple systems were affected, alerts indicated the use of known malicious tools, and there was suspicion of insider involvement.

The objective of this challenge was to investigate the incident using **IBM QRadar SIEM**, analyze logs from multiple security sources, correlate events, identify attacker behavior, and reconstruct the complete attack timeline following standard SOC investigation practices.

**Key Objectives:**
- Understand QRadar SIEM architecture and workflows  
- Perform log analysis and event correlation  
- Identify attacker techniques using MITRE ATT&CK  
- Apply structured SOC investigation methodology  

---

### ii) Step-by-Step Methodology Followed

1. Reviewed the incident scenario and defined the investigation scope.  
2. Identified available log sources including Sysmon, PowerShell, Windows Event Logs, Suricata IDS, and Zeek.  
3. Applied filters based on event IDs, payload keywords, IP addresses, usernames, and timestamps.  
4. Correlated events across multiple systems to identify attack stages such as initial access, persistence, lateral movement, and data exfiltration.  
5. Mapped identified attacker behaviors to MITRE ATT&CK techniques (e.g., T1547.001 – Registry Run Keys).  
6. Validated findings by cross-referencing logs and constructing a timeline of events.

---

### iii) Screenshots and Evidence Documentation

Screenshots from the QRadar 101 walkthrough and reference materials were used to document:
- Log source configuration  
- Event filtering and analysis  
- Detection of malicious activity  
- MITRE ATT&CK technique mapping  

> **Note:** Due to the high resource requirements of QRadar Community Edition, a stable local deployment was not feasible. Walkthrough-based screenshots were used to demonstrate investigation logic and analysis steps.

---

### iv) Summary of Key Findings, Challenges, and Resolutions

**Key Findings:**
- Initial infection occurred through a malicious document opened by an internal user.  
- The attacker executed commands using PowerShell.  
- Persistence was established via Windows Registry Run Keys.  
- Lateral movement occurred across internal systems.  
- Sensitive data was exfiltrated using command-line tools.

**Challenges Faced:**
- High system resource requirements for QRadar deployment.  
- Difficulty maintaining a stable enterprise SIEM environment locally.

**Resolutions:**
- Conducted investigation using official walkthrough material.  
- Focused on SOC analysis methodology, correlation logic, and attacker behavior rather than tool deployment.

---

### v) Attack Walkthrough Summary (Investigation Narrative)

Based on log correlation and timeline analysis in IBM QRadar, the attacker carried out a multi-stage compromise. The attack began with a malicious document delivered to an internal employee, leading to PowerShell-based command execution. The attacker established persistence by modifying Windows Registry Run Keys and created additional access mechanisms to maintain control.

Following initial compromise, the attacker performed internal host discovery and moved laterally across systems using administrative-style tools. Logs revealed interaction with the domain controller and targeted searches for project-related data. The incident concluded with successful data exfiltration, confirming a full breach of the environment.

---

## b) Comparison of WebStrike and QRadar 101 Challenges

| Aspect | WebStrike | QRadar 101 |
|------|----------|------------|
| Focus | Network & Web Attacks | SIEM-Based Investigation |
| Data Sources | PCAP, Web Logs | Aggregated Security Logs |
| Tools Used | Wireshark | IBM QRadar |
| Learning Outcome | Attack Execution | Attack Detection & Correlation |

Together, both challenges provided end-to-end visibility from attack execution to detection and response.

---

## c) Conclusion: SOC Skill Enhancement

Completing the WebStrike and QRadar 101 challenges enhanced practical SOC skills including:
- Incident investigation workflows  
- Multi-source log correlation  
- Threat detection and MITRE ATT&CK mapping  
- Analytical decision-making in SOC environments  

Overall, these challenges provided strong exposure to real-world SOC operations and enterprise SIEM investigations.
