# Linux SSH Security Assessment Report

**Project:** Cybersecurity Homelab — Project 01  
**Assessment Date:** October 2026  
**Assessment Type:** Network Security and Linux Hardening  
**Environment:** Oracle VirtualBox  
**Tools:** Kali Linux, Ubuntu, Nmap, OpenSSH, UFW, journalctl

---

## 1. Executive Summary

This security assessment evaluated SSH accessibility, authentication logging, and firewall access controls on an Ubuntu Linux virtual machine.

The assessment combined offensive reconnaissance with defensive monitoring and security hardening.

Testing demonstrated that SSH accessibility changed when firewall permissions were modified. Additionally, authentication logs recorded two unsuccessful login attempts followed by a successful authentication.

**Overall Result:** The exercise demonstrated functional SSH logging and the ability to restrict access using firewall rules. No exploitation or unauthorized compromise was established.

## 2. Scope and Objectives

The assessment was performed within an authorized, isolated VirtualBox environment.

| Machine | Role | Internal IP | Host-Only IP |
|---|---|---|---|
| Kali Linux | Assessment workstation | 192.168.56.10 | 192.168.57.10 |
| Ubuntu | Defender | 192.168.56.20 | 192.168.57.20 |

**Assessment objectives:**
- Identify exposed network services.
- Analyze SSH authentication events.
- Configure least-privilege firewall rules.
- Verify the effect of firewall changes.
- Document findings and recommendations.

## 3. Network Architecture

The homelab used two virtual network segments.

**Internal Network — CyberLab:** Supported communication between Kali and Ubuntu.

**Host-Only Network:** Allowed administration from the Windows host without router port forwarding.

Add your network diagram here:

![Homelab Network Architecture](../screenshots/network-diagram.png)

## 4. Assessment Methodology

### 4.1 Network Reconnaissance

Nmap was used to identify network services exposed by Ubuntu.

```bash
nmap -sT -sV 192.168.56.20
```

**Purpose:** Identify accessible TCP ports and attempt service-version detection.

**Observation:** SSH was identified as an accessible service during the initial assessment.

![Initial Nmap Scan](../screenshots/nmap-initial-scan.png)

### 4.2 Authentication Log Analysis

Controlled SSH login attempts were generated from Kali Linux.

```bash
ssh analyst@192.168.57.20
```

Ubuntu's authentication records were inspected using:

```bash
sudo journalctl -u ssh --since "15 minutes ago"
```

**Observed results:**

| Event | Result |
|---|---|
| Source IP | 192.168.57.10 |
| Target account | analyst |
| Failed login events | 2 |
| Subsequent successful login | Yes |

The events were intentionally generated during the authorized exercise and did not establish malicious activity.

![SSH Authentication Logs](../screenshots/ssh-authentication-logs.png)

### 4.3 Firewall Hardening

UFW was used to restrict unnecessary inbound connections.

The firewall was inspected using:

```bash
sudo ufw status verbose
```

A rule permitting SSH from Kali's internal-network address was temporarily introduced for verification.

```bash
sudo ufw allow from 192.168.56.10 to any port 22 proto tcp
```

The temporary rule was subsequently removed after testing.

### 4.4 Security Control Verification

Nmap was used to observe SSH accessibility before and after firewall changes.

```bash
nmap -Pn -p 22 --reason 192.168.56.20
```

| Test | Observed SSH State |
|---|---|
| Initial restricted state | Filtered |
| SSH allowed from Kali | Open |
| Temporary rule removed | Closed (final observation) |

The final result was reported as `closed`, which differs from `filtered`. Additional verification would be required to establish the exact cause of that response.

![Firewall Rules](../screenshots/ufw-firewall-rules.png)

![SSH Allowed](../screenshots/nmap-ssh-allowed.png)

![SSH Restricted](../screenshots/nmap-ssh-blocked.png)

## 5. Security Findings

### Finding 01 — SSH Service Exposure

**Classification:** Informational

SSH was accessible when network access was permitted by applicable firewall rules.

**Risk:** Unnecessarily broad access to administrative services increases the potential attack surface.

**Recommendation:** Limit SSH access to designated management IP addresses and implement key-based authentication.

### Finding 02 — Authentication Logging

**Classification:** Informational

Ubuntu recorded controlled unsuccessful and successful SSH authentication events.

**Risk:** Without active monitoring, suspicious authentication patterns may go unnoticed.

**Recommendation:** Implement centralized log collection and alerting for abnormal authentication activity.

### Finding 03 — Firewall Rule Verification

**Classification:** Positive Security Observation

Firewall modifications produced observable changes in SSH accessibility.

**Recommendation:** Continue testing access restrictions from all relevant network interfaces, while preserving legitimate administrative access.

## 6. Remediation Recommendations

1. Implement SSH public-key authentication.
2. Restrict SSH access to approved administration addresses.
3. Enable centralized logging and security alerting.
4. Review firewall rules periodically.
5. Keep Linux packages and SSH services updated.
6. Verify required services after configuration changes.

## 7. Lessons Learned

This project improved my understanding of network reconnaissance, SSH authentication, Linux security logs, firewall access control, and defensive security verification.

A particularly important lesson was that security controls must be tested from the perspective of the accessing system rather than relying only on configuration settings.

The exercise also reinforced a principle applicable to Collegiate Cyber Defense Competitions: defenders must reduce unauthorized access while preserving the availability of legitimate services.

## 8. Conclusion

The project demonstrated foundational Red Team and Blue Team skills within a controlled Linux homelab.

The assessment established a baseline for future exercises involving centralized logging, incident response, vulnerability assessment, and server hardening.

**Future Development:** Expand the environment with a SIEM to investigate and correlate security events across multiple systems.

---

*All testing was performed within an authorized personal cybersecurity homelab.*
