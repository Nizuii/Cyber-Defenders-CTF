# Security Incident Report – QRadar SIEM Investigation

## Incident Summary

This report documents the investigation of a simulated security incident involving unauthorized access, lateral movement, and potential data exfiltration within a financial organization. The investigation was performed using IBM QRadar SIEM by analyzing multiple log sources including Windows Event Logs, Sysmon logs, Suricata IDS alerts, Zeek network logs, and PowerShell logs.

The objective of this investigation was to identify the attack vector, compromised systems, attacker techniques, and the overall impact of the incident.

---

## Incident Details

| Field                  | Information                            |
| ---------------------- | -------------------------------------- |
| Incident ID            | SOC-IR-001                             |
| Investigation Platform | IBM QRadar SIEM                        |
| Incident Type          | Unauthorized Access / Lateral Movement |
| Severity Level         | High                                   |
| Status                 | Closed (Simulated Investigation)       |
| Analyst                | [Your Name]                            |
| Date                   | [Add Date]                             |

---

## Scope of Investigation

The investigation focused on identifying:

* Initial access vector
* Compromised endpoints
* Attacker IP address
* Persistence mechanisms
* Lateral movement techniques
* Evidence of data exfiltration
* Indicators of compromise (IOCs)

---

## Log Sources Analyzed

The following log sources were analyzed during the investigation:

* Windows Security Event Logs
* Sysmon Logs
* Suricata IDS Alerts
* Zeek Network Monitoring Logs
* PowerShell Activity Logs
* Authentication Logs

---

## Investigation Methodology

The investigation followed a standard SOC investigation workflow:

1. Alert validation
2. Log correlation
3. Timeline creation
4. Threat actor activity tracking
5. IOC identification
6. Attack pattern analysis
7. Documentation of findings

---

## Key Findings

### 1. Initial Access

The investigation revealed that the attack began when a malicious document was executed on an internal workstation. This likely indicates a phishing or social engineering attack vector.

### 2. Attacker Infrastructure

Analysis of network logs identified a suspicious external IP address communicating with internal systems. This IP is believed to be part of the attacker infrastructure.

### 3. Compromised Host

Timeline analysis identified the first compromised machine (Patient Zero) based on the earliest suspicious activity recorded in the SIEM.

### 4. Persistence Mechanism

The attacker created a new user account to maintain persistence within the environment.

Observed behavior included:

* Account creation events
* Privilege modification attempts
* Suspicious authentication patterns

### 5. Lateral Movement

Evidence shows the attacker used WMI-based remote execution tools such as Impacket wmiexec to move laterally across systems.

This indicates post-exploitation activity and internal reconnaissance.

### 6. Data Exfiltration Activity

Network logs revealed the use of curl for outbound file transfer. This suggests possible data exfiltration activity.

Indicators included:

* Outbound connections
* File transfer commands
* Suspicious network sessions

### 7. Defense Evasion Attempts

The attacker appeared to check logging configurations, suggesting awareness of monitoring mechanisms.

Possible objectives:

* Avoid detection
* Identify logging gaps
* Maintain stealth access

### 8. Possible Insider Threat Indicators

Some activity suggested knowledge of internal systems and access patterns, which could indicate either credential compromise or insider assistance.

Further investigation would be required in a real environment.

---

## Indicators of Compromise (IOCs)

### Network Indicators

* Suspicious external IP address (Attacker infrastructure)

### Host Indicators

* Suspicious user account creation
* Unauthorized remote execution activity
* Malicious document execution

### Behavioral Indicators

* Lateral movement behavior
* Unusual authentication activity
* Suspicious outbound traffic

---

## MITRE ATT&CK Mapping

| Tactic            | Technique                              |
| ----------------- | -------------------------------------- |
| Initial Access    | Phishing (T1566)                       |
| Execution         | Malicious File Execution (T1204)       |
| Persistence       | Create Account (T1136)                 |
| Lateral Movement  | WMI Execution (T1047)                  |
| Command & Control | Remote Services                        |
| Exfiltration      | Exfiltration Over Web Services (T1567) |
| Defense Evasion   | Indicator Removal (T1070)              |

---

## Attack Timeline (Simplified)

| Time | Activity                    |
| ---- | --------------------------- |
| T1   | Malicious document executed |
| T2   | Initial system compromise   |
| T3   | Suspicious account creation |
| T4   | Lateral movement detected   |
| T5   | File access activity        |
| T6   | Possible data exfiltration  |
| T7   | Logging checks performed    |

---

## Impact Assessment

Potential risks identified:

* Unauthorized system access
* Credential compromise
* Internal reconnaissance
* Possible sensitive data exposure
* Security monitoring awareness by attacker

In a real environment this would be classified as a **high severity security incident**.

---

## Recommendations

Based on investigation findings, the following security improvements are recommended:

### Immediate Actions

* Reset compromised accounts
* Block attacker IP addresses
* Isolate affected systems
* Review privileged accounts

### Security Improvements

* Implement EDR solutions
* Enable enhanced logging
* Apply least privilege principle
* Conduct phishing awareness training
* Implement network segmentation

### Monitoring Improvements

* Improve SIEM correlation rules
* Monitor lateral movement activity
* Alert on abnormal account creation
* Monitor outbound traffic anomalies

---

## Lessons Learned

This investigation demonstrates the importance of:

* Centralized log monitoring
* Early detection capabilities
* Proper log correlation
* Incident response procedures
* Threat hunting practices

---

## Conclusion

The investigation successfully identified the attack path, attacker techniques, and affected systems through SIEM log analysis. The attack followed a typical intrusion lifecycle including initial compromise, persistence, lateral movement, and possible data exfiltration.

This exercise highlights the importance of SIEM platforms in detecting and investigating modern cyber threats.

---

## Analyst Notes

This report was created as part of a cybersecurity learning exercise to develop SOC analysis and incident investigation skills.

---

## Author

**Name:** [Your Name]
**Role:** SOC Analyst (Student)
**Focus Areas:** Cybersecurity, Threat Detection, Incident Response
**GitHub:** [Your GitHub Profile Link]

---
