# Linux SSH Security Assessment

## Project Overview

This project documents a hands-on cybersecurity assessment conducted in a virtualized Linux environment using Kali Linux and Ubuntu.

The objective was to assess SSH accessibility, investigate authentication activity, configure firewall access restrictions, and validate defensive security controls through network testing.

The exercise combined **Red Team reconnaissance** with **Blue Team security monitoring and hardening**.

## Technologies Used

- Oracle VirtualBox
- Kali Linux
- Ubuntu Linux
- Nmap
- OpenSSH
- UFW (Uncomplicated Firewall)
- Linux systemd journal / journalctl

## Network Architecture

The homelab contained two virtual machines connected through an internal network and a host-only network.

| Machine | Internal Network | Host-Only Network |
|---------|------------------|-------------------|
| Kali Linux | 192.168.56.10 | 192.168.57.10 |
| Ubuntu | 192.168.56.20 | 192.168.57.20 |

Kali served as the assessment workstation, while Ubuntu was the system being defended.

## Assessment Methodology

### 1. Network Reconnaissance

Used Nmap to discover and examine services exposed by the Ubuntu system.

Commands used:
nmap -sT -sV 192.168.56.20
nmap -Pn -p 22 192.168.56.20

### 2. SSH Authentication Analysis

Generated controlled failed and successful SSH login events and analyzed authentication records using:

Commands used:
sudo journalctl -u ssh --since "15 minutes ago"

Observed two failed authentication attempts followed by a successful login.

### 3. Firewall Hardening

Configured UFW to restrict incoming connections and tested a temporary rule permitting SSH from an authorized lab address.

### 4. Security Control Verification

Performed Nmap scans before and after changing the firewall rules.

Observed that SSH was initially filtered, became open when the appropriate allow rule was applied, and was reported closed after the rule was removed during the final test.

## Key Findings

- SSH was accessible when an appropriate firewall rule permitted access.
- Authentication logs recorded successful and failed SSH login events.
- Firewall changes affected SSH accessibility as observed through Nmap.
- Multiple network interfaces required careful consideration when configuring access restrictions.

## Lessons Learned

This assessment improved my understanding of TCP ports, network reconnaissance, SSH administration, Linux authentication logs, least-privilege access control, and firewall verification.

A major takeaway was that security configurations must be tested rather than assumed to work as intended.

## Future Improvements

- Implement SSH key-based authentication
- Configure centralized logging with Wazuh
- Develop authentication-failure alerting
- Expand testing to Windows endpoints
- Practice incident response and defensive monitoring

**Disclaimer:** All testing was performed in a personally owned, authorized virtual cybersecurity lab.
