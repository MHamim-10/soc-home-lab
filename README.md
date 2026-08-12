# SOC Home Lab: Active Directory + Splunk SIEM Detection Lab

## Overview

A self-built home lab simulating a small enterprise environment with Active Directory, a Splunk SIEM, and an attacker machine — built to practice log collection, detection engineering, and incident investigation from both the attacker and defender sides.

## Architecture

| Machine | Role | OS | IP |
|---|---|---|---|
| Splunk Server | SIEM / Log Indexer | Ubuntu Server | 192.168.10.10 |
| Win11 | Domain-joined workstation (attack target) | Windows 11 | 192.168.10.11 |
| Win22 | Domain Controller (`Myproject.local`) | Windows Server 2022 | 192.168.10.12 |
| Kali | Attacker machine | Kali Linux | 192.168.10.100 |

![Network Architecture](diagrams/network-architecture.png)

**Domain:** `Myproject.local`
**Organizational Units:** `IT` (user: `jsmith`), `HR` (user: `acooper`)

## Tools Used

- **Splunk Enterprise** (indexer/search head) + **Splunk Universal Forwarder**
- **Active Directory Domain Services** (Windows Server 2022)
- **Kali Linux**: Hydra, Nmap, xfreerdp, and other tooling per attack
- **VirtualBox** with a NAT Network for isolated lab networking

## Attacks Documented

| # | Attack | Technique | Link |
|---|---|---|---|
| 01 | RDP Brute Force | Credential Access (T1110) | [attacks/01-rdp-bruteforce](attacks/01-rdp-bruteforce/README.md) |
| 02 | *(coming soon)* | | |
| 03 | *(coming soon)* | | |
| 04 | *(coming soon)* | | |

## Setup Summary

1. Built a NAT Network in VirtualBox for `192.168.10.0/24`, assigned static IPs to each VM.
2. Installed and configured Splunk on Ubuntu Server; deployed Splunk Universal Forwarder to Win11 and Win22.
3. Promoted Win22 to a Domain Controller for `Myproject.local`; created `IT` and `HR` OUs.
4. Domain-joined Win11.
5. Configured Kali as the attacker box.

## Infrastructure Challenges & Troubleshooting

- **DNS/network loss after promoting Win22 to a Domain Controller** — DNS on the DC's own NIC needed to point to itself, not an external resolver — a classic AD DS gotcha after promotion.
- **Missing Splunk logs after the DC network issue** — traced through forwarder service status, firewall/network profile changes, and `inputs.conf` monitoring stanzas.
- **Events not appearing in "Last 24 hours" despite existing in "All time"** — root-caused to clock drift: Ubuntu's `systemd-timesyncd` was inactive, causing UTC drift relative to the domain. Fixed with `timedatectl set-ntp true` and `w32tm /resync /force` across the Windows machines.

## Skills Demonstrated

- SIEM deployment and log source onboarding
- Active Directory administration
- SPL (Search Processing Language) query writing
- Offensive tooling for controlled attack simulation
- Cross-platform troubleshooting: DNS, firewall, NTP/time sync, log forwarding
- End-to-end incident reconstruction from raw logs

## Repository Structure

```
soc-home-lab/
├── README.md
├── diagrams/
│   ├── network-architecture.drawio
│   └── network-architecture.png
└── attacks/
    ├── 01-rdp-bruteforce/
    │   ├── README.md
    │   └── screenshots/
    └── ... (one folder per attack)
```

## Notes

This lab was built entirely in an isolated NAT network with no internet-facing exposure — all attack activity was contained to the private lab subnet.
