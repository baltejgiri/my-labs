# Network Engineering Labs
### Hands-on lab environments for mastering enterprise, data centre, and service provider networking using Infrastructure as Code.

---

## About This Repository

This repository documents a structured, protocol-by-protocol approach to network engineering using containerized lab environments. Every lab is built with real network operating systems — Arista EOS, Cisco IOS, and Nokia SR Linux — orchestrated through ContainerLab and version-controlled with Git.

The focus is on **depth over breadth**: each lab isolates a specific technology, builds it from scratch, breaks it deliberately, and troubleshoots it systematically. Configurations, topology files, and observations are all committed alongside each lab so the learning is reproducible and shareable.

## Lab Environment

| Tool | Purpose |
|---|---|
| [ContainerLab](https://containerlab.dev) | Topology orchestration for containerized network nodes |
| [Docker](https://docker.com) | Container runtime for NOS images (cEOS, SR Linux, IOL) |
| [Git](https://git-scm.com) | Version control for topology files and configurations |
| Arista cEOS | Containerized Arista EOS — full-featured, lightweight |
| Alpine Linux | Lightweight host containers for end-to-end traffic testing |

## Repository Structure

```
my-labs/
├── arista/          # Data Centre & Cloud networking (Arista EOS)
├── cisco/           # Enterprise & Campus networking (Cisco IOS)
└── nokia/           # Service Provider networking (Nokia SR Linux)
```

## Arista EOS Lab Series

The Arista series follows a progressive 12-lab curriculum covering every major protocol and feature area of Arista EOS, from foundational CLI skills through full data centre fabric design.

| # | Lab | Protocols / Features | Status |
|---|-----|----------------------|--------|
| 01 | EOS Basics | CLI, Management, LLDP, ARP, ICMP | ✅ Complete |
| 02 | Layer 2 Switching | VLANs, STP, RSTP, MSTP | 🔲 Upcoming |
| 03 | OSPF | Single-area, Multi-area, Troubleshooting | 🔲 Upcoming |
| 04 | BGP | eBGP, iBGP, Attributes, Filtering, Communities | 🔲 Upcoming |
| 05 | Redistribution | RIP, OSPF↔BGP↔RIP redistribution | 🔲 Upcoming |
| 06 | VRF | VRF-Lite, Route Leaking | 🔲 Upcoming |
| 07 | MPLS | LDP, L3VPN Basics | 🔲 Upcoming |
| 08 | Services | DHCP, NAT, ACLs | 🔲 Upcoming |
| 09 | Management Plane | AAA/TACACS+, SNMP, sFlow | 🔲 Upcoming |
| 10 | High Availability | QoS, Flow Control, VARP/VRRP, LACP | 🔲 Upcoming |
| 11 | Multicast | IGMP, PIM Sparse & Dense Mode | 🔲 Upcoming |
| 12 | Data Centre Fabric | Spine-Leaf, MLAG, VXLAN/EVPN, BGP Underlay | 🔲 Upcoming |

## Arista EOS — Lab 01: EOS Basics

**Path:** `arista/lab01-eos-basics/`

The first lab establishes foundational familiarity with Arista EOS before moving into protocol-specific work.

**Topology:**
```
[PC1] ── Ethernet1 ──[SW1]── Ethernet2 ── Ethernet1 ──[SW2]── Ethernet2 ──[PC2]
10.0.0.10/24         10.0.0.1/24  10.1.0.1/30   10.1.0.2/30  10.0.0.1/24  10.0.0.20/24
```

**Covers:**
- EOS CLI modes and navigation (User EXEC, Privileged EXEC, Global Config)
- Interface configuration and verification
- LLDP neighbour discovery between SW1 and SW2
- ARP table population and inspection
- End-to-end ICMP reachability testing (PC1 → SW1 → SW2 → PC2)
- Core show commands: `show version`, `show interfaces`, `show lldp neighbors`, `show arp`, `show ip route`
- Deliberate fault injection and troubleshooting scenarios

## Cisco IOS Lab Series

*Coming soon — Enterprise campus and WAN protocols.*

## Nokia SR Linux Lab Series

*Coming soon — Service provider routing and MPLS.*

---

*Maintained by [Baltej Giri](https://linkedin.com/in/baltej-giri/)*
