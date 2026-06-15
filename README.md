# Network-Traffic-Analysis-Lab
W# 🔍 Network Traffic Analysis Lab
### SOC Analyst Portfolio | George Eze | TS Academy | 2026

---

## 🎯 What This Lab Is About

This lab captures and analyses real network traffic 
during simulated attacks using Wireshark.

The goal is to apply my mentor Chrissie's advice:

> *"Investigate what the device was doing BEFORE 
> the attack — look for suspicious traffic in the 
> surrounding activity."*

This means detecting attackers at **Cyber Kill Chain 
Stage 1 (Reconnaissance)** — not just Stage 4 when 
the main attack fires.

**Framework:** NIST SP 800-61 Rev.2  
**Mentor:** Chrissie — TS Academy  
**Analyst:** George Eze  

---

## 🏗️ Lab Setup

| Machine | IP Address | Role |
|---------|-----------|------|
| Kali Linux | 192.168.0.239 | Attacker + Wireshark |
| Ubuntu ICONIC | 192.168.0.138 | Victim endpoint |
| Wazuh SIEM | Docker/Kali | Alert detection |
| Suricata IDS | Ubuntu | Network monitoring |

---

## ✅ Investigation 1 — Port Scan Detection

**Case ID:** NTA-2026-001  
**Date:** June 4, 2026  
**Tool:** Wireshark  
**Attack Simulated:** Nmap -sS SYN Port Scan  

---

### What I Did

1. Started Wireshark on Kali — capturing eth0
2. Launched Nmap SYN scan from Kali against Ubuntu
3. Applied Wireshark filter to isolate scan traffic
4. Analysed the conversations window
5. Identified port scan signature
6. Compared what Wireshark found vs what Wazuh detected
7. Wrote new Suricata rule to detect it earlier

---

### Wireshark Filter Usedireshark networtcp.flags.syn == 1 and tcp.flags.ack == 0

This filter isolates SYN-only packets — the exact 
signature of a port scan. SYN packets with no ACK 
means the connection was never completed — the 
attacker is just knocking on doors.

---

### What I Found

| Finding | Value | Meaning |
|---------|-------|---------|
| Total packets captured | 14,376 | Full attack traffic recorded |
| Packets matching filter | 1,006 | Port scan packets isolated |
| TCP Conversations | 1,006 | One IP hitting 1,006 ports |
| Attacker IP | 192.168.0.239 | Kali Linux |
| Target IP | 192.168.0.138 | Ubuntu ICONIC |
| Ports discovered | 22, 80, 443, 445, 3389, 5900 | Services mapped by attacker |
| Packet size | 58 bytes | Consistent SYN signature |

---Normal traffic = Two IPs talking on ONE port
Port scan traffic = ONE IP hitting MANY ports
192.168.0.239 → 192.168.0.138:22 SYN
192.168.0.239 → 192.168.0.138:80 SYN
192.168.0.239 → 192.168.0.138:443 SYN
192.168.0.239 → 192.168.0.138:445 SYN
192.168.0.239 → 192.168.0.138:3389 SYN
...1,006 more ports...
This is the attacker mapping which doors are open
BEFORE launching the main attack.


---

### Pre-Attack Timeline — Chrissie's Method

| Time | What Happened | Kill Chain Stage |
|------|--------------|-----------------|
| T+0:00 | Nmap port scan launched | Stage 1 — Reconnaissance |
| T+0:01 | 1,006 SYN packets sent | Stage 1 — Reconnaissance |
| T+0:30 | Port 22 SSH confirmed open | Stage 2 — Weaponisation |
| T+1:00 | Hydra brute force launched | Stage 3 — Delivery |
| T+3:00 | Wazuh detects 609 alerts | Stage 4 — Exploitation |

**Key insight:** Wazuh only detected at Stage 4.  
Wireshark caught the attack at Stage 1.  
That is the difference Chrissie's method makes.

---

### MITRE ATT&CK Mapping

| Technique ID | Technique Name | Tactic |
|-------------|---------------|--------|
| T1595 | Active Scanning | Reconnaissance |
| T1046 | Network Service Discovery | Discovery |
| T1110 | Brute Force (follow-on attack) | Credential Access |
| T1018 | Remote System Discovery | Discovery |

---

### What Wazuh Detected vs What Wireshark Found

| Tool | Detected Port Scan? | Detected Brute Force? |
|------|--------------------|-----------------------|
| Wazuh SIEM | ❌ MISSED | ✅ 609 alerts |
| Wireshark | ✅ 1,006 conversations | ✅ Rapid SSH SYN packets |

**This proves SIEM alone is not enough.**  
Packet capture reveals what SIEM cannot see.

---

### New Suricata Rule — Early Detection

Based on this investigation I wrote this rule to 
detect port scanning at Kill Chain Stage 1:
alert tcp any any -> 192.168.0.138 any (
msg:"PORT SCAN DETECTED - Pre-Attack Recon";
flags:S;
threshold:type both, track by_src,
count 20, seconds 10;
sid:9000001;
rev:1;
)

**Result:** Detection moved from Stage 4 to Stage 1.

---

### IOCs — Indicators of Compromise

| Type | Value | Action |
|------|-------|--------|
| Attacker IP | 192.168.0.239 | Block at firewall |
| Target IP | 192.168.0.138 | Monitor closely |
| Protocol | TCP SYN scan | Alert on pattern |
| Open Port | 22 SSH | Harden immediately |
| Open Port | 445 SMB | Restrict access |
| PCAP File | wireshark_eth0Y96PQ3 | Preserve as evidence |

---

### Response Actions Taken

- [x] Identified attacker IP from PCAP evidence
- [x] Saved PCAP file for chain of custody
- [x] Wrote Suricata rule for early detection
- [x] Blocked attacker IP via iptables
- [ ] Restrict unnecessary open ports
- [ ] Add port scan alert to Wazuh dashboard

---

### Lessons Learned

**1. SIEM alone is not enough**  
Wazuh completely missed the Nmap scan. Without 
Wireshark, the SOC would have had no visibility 
into the reconnaissance phase of this attack.

**2. Port scan is always pre-attack**  
1,006 connections from one IP in seconds is 
unmistakable. Learning to recognise this pattern 
in Wireshark is a fundamental SOC skill.

**3. Moving detection left saves businesses**  
Catching an attacker at Stage 1 means they never 
reach Stage 4. That is what Chrissie's methodology 
teaches — and this lab proved it works.

---

## 🔜 Coming Next

- Investigation 2 — Brute Force Traffic Analysis
- Investigation 3 — Real Malware PCAP Analysis

---

## 📂 Files in This Repo

### The Port Scan Pattern Explainedk traffic analysis, port scan detection, brute force traffic, MITRE ATT&amp;CK T1595, NIST SP 800-61
