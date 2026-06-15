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

### Wireshark Filter Usedireshark network traffic analysis, port scan detection, brute force traffic, MITRE ATT&amp;CK T1595, NIST SP 800-61
