# Active Directory (AD) Homelab Portfolio
![Status](https://img.shields.io/badge/Status-Active-brightgreen)
![Platform](https://img.shields.io/badge/Platform-Kali%20Linux-557C94?logo=kalilinux&logoColor=white)
![Focus](https://img.shields.io/badge/Focus-Active%20Directory-0078D4?logo=windows&logoColor=white)
![Tools](https://img.shields.io/badge/Tools-Impacket%20%7C%20BloodHound%20%7C%20CrackMapExec-red)
![License](https://img.shields.io/badge/License-MIT-lightgrey)

A hands-on offensive security lab simulating a real enterprise Active Directory environment. Built to practice the full attack chain — from initial enumeration through privilege escalation — using industry-standard red team tooling.

> All activity is conducted in an isolated, self-contained virtual network. No external systems were targeted.

---

## 📖 Overview

This lab simulates a small corporate Active Directory environment with intentional misconfigurations mirroring common real-world vulnerabilities. The goal was to develop practical offensive security skills by attacking infrastructure I designed and built myself — not just following guided course exercises.

**What I built:**
- A fully functional Windows domain (`lab.local`) with a Domain Controller and domain-joined workstation
- Realistic user accounts and service accounts with deliberately weak configurations
- An isolated virtual network running on UTM (Apple Silicon)

**What I practiced:**
- AD enumeration and attack path mapping
- Kerberos-based attacks (AS-REP Roasting, Kerberoasting)
- Lateral movement and credential abuse
- Local privilege escalation

---

## 🏗️ Lab Architecture

### Network Topology

```
┌─────────────────────────────────────────────┐
│             Host-Only Network               │
│               192.168.10.0/24               │
│                                             │
│   ┌──────────┐        ┌──────────────┐     │
│   │  Kali    │        │    DC01      │     │
│   │ Attacker │───────▶│ Domain Ctrl  │     │
│   │          │        │ lab.local    │     │
│   └──────────┘        └──────────────┘     │
│        │                                    │
│        │              ┌──────────────┐     │
│        └─────────────▶│    WS01      │     │
│                       │  Workstation │     │
│                       │ (domain joined)│   │
│                       └──────────────┘     │
└─────────────────────────────────────────────┘
```

### Virtual Machines

| Hostname | OS | Role | Purpose |
|---|---|---|---|
| kali | Kali Linux | Attacker | Offensive tooling and attack execution |
| DC01 | Windows Server 2022 | Domain Controller | AD, DNS, Kerberos |
| WS01 | Windows 10/11 | Workstation | Lateral movement target |

### Design Decisions

- **Host-Only networking** isolates all lab traffic from the real network — nothing leaves the hypervisor
- **Kali dual-adapter** (Host-Only + NAT) allows tool updates without exposing lab VMs to the internet
- **Intentional misconfigurations** are modeled after findings commonly reported in real penetration test reports, not arbitrary CTF puzzles

---

## ⚔️ Techniques Demonstrated

| Technique | Category | What It Exploits |
|---|---|---|
| Network & AD Enumeration | Reconnaissance | Open ports, SMB shares, domain users, trust relationships |
| AS-REP Roasting | Credential Access | Accounts with Kerberos preauthentication disabled |
| Kerberoasting | Credential Access | Service accounts with SPNs set, weak service passwords |
| Password Spraying | Credential Access | Weak or default domain passwords |
| BloodHound Attack Path Mapping | Discovery | AD object relationships and privilege escalation paths |
| Pass-the-Hash | Lateral Movement | NTLM hash reuse without plaintext credential recovery |
| Token Impersonation | Privilege Escalation | Abusing access tokens of higher-privileged processes |
| Local PrivEsc Enumeration | Privilege Escalation | Misconfigured services, unquoted paths, weak permissions |

---

## 📂 Walkthroughs

Detailed methodology, commands, and output for each technique:

| Walkthrough | Technique | Difficulty |
|---|---|---|
| [AS-REP Roasting](./walkthroughs/asrep-roasting.md) | Credential Access | Beginner |
| [Kerberoasting](./walkthroughs/kerberoasting.md) | Credential Access | Beginner |
| [BloodHound Enumeration](./walkthroughs/bloodhound.md) | Discovery | Intermediate |
| [Pass-the-Hash](./walkthroughs/pass-the-hash.md) | Lateral Movement | Intermediate |
| [Local Privilege Escalation](./walkthroughs/privesc.md) | Privilege Escalation | Intermediate |

Each walkthrough follows the same structure:
- **Objective** — what the attack achieves and why it works
- **Prerequisites** — what access or information you need first
- **Steps** — exact commands with annotated output
- **Detection** — how a blue team would catch this (because understanding defense makes you a better attacker)
- **References** — tools and resources used

---

## 🧠 Key Takeaways

**Misconfigurations are the real attack surface.** Every technique I practiced required some form of misconfiguration to be exploitable — disabled preauthentication, weak service account passwords, SMB signing off. The tooling is almost secondary to understanding *why* those configurations are dangerous.

**Enumeration determines everything downstream.** The quality of your BloodHound graph and initial AD recon directly determines what attacks are available to you. Rushing past enumeration is the fastest way to miss the obvious path.

**Attack and defense are the same knowledge.** Writing the "detection" section for each walkthrough forced me to think about log sources, SIEM alerts, and EDR signatures — which made the offensive techniques themselves click more deeply.

**Building the lab teaches as much as attacking it.** Promoting a Domain Controller, configuring Kerberos, and setting SPNs manually gave me intuition for AD internals that purely attacking pre-built environments wouldn't have.

---

## 🧰 Tools Used

| Tool | Purpose |
|---|---|
| [Nmap](https://nmap.org) | Port scanning and service enumeration |
| [enum4linux-ng](https://github.com/cddmp/enum4linux-ng) | SMB and LDAP enumeration |
| [Kerbrute](https://github.com/ropnop/kerbrute) | Username enumeration and password spraying |
| [CrackMapExec](https://github.com/byt3bl33d3r/CrackMapExec) | Domain enumeration and lateral movement |
| [Impacket](https://github.com/fortra/impacket) | AS-REP Roasting, Kerberoasting, PTH, remote execution |
| [BloodHound](https://github.com/BloodHoundAD/BloodHound) + SharpHound | AD attack path visualization |
| [Hashcat](https://hashcat.net) | Offline hash cracking |
| [winPEAS](https://github.com/carlospolop/PEASS-ng) | Local privilege escalation enumeration |

---

## 📚 References & Learning Resources

- [TCM Security — Practical Ethical Hacking](https://academy.tcm-sec.com)
- [TryHackMe — Attacktive Directory](https://tryhackme.com/room/attacktivedirectory)
- [HackTricks — Active Directory Methodology](https://book.hacktricks.xyz/windows-hardening/active-directory-methodology)
- [PayloadsAllTheThings — AD Attacks](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Active%20Directory%20Attack.md)
- [BloodHound Docs](https://bloodhound.readthedocs.io)

---

## ⚖️ Legal & Ethical Notice

This lab runs entirely on locally hosted virtual machines in an isolated network. No external systems, networks, or services were targeted at any point. All techniques demonstrated here are conducted in a controlled environment for educational purposes.
