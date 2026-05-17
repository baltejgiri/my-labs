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

## Arista EOS Lab Series

The Arista series follows a progressive lab curriculum covering every major protocol and feature area of Arista EOS, from foundational CLI skills through data centre fabric design.

| # | Lab | Protocols / Features | Status |
|---|-----|----------------------|--------|
| 01 | EOS Basics | CLI, Management, LLDP, ARP, ICMP | ✅ Complete |
| 02 | Layer 2 Switching | VLANs, STP, RSTP, MSTP | ✅ Complete |
| 03 | OSPF | Single-area, Multi-area, Troubleshooting | ✅ Complete |
| 03b | OSPF Multi-Area Packet Flow | ABR roles, LSA Types, Inter-area routing, Packet flow analysis | ✅ Complete |
| 04 | BGP | eBGP, iBGP, Attributes, Filtering, Communities | 🔲 Planned |
| 05 | VXLAN and EVPN | Spine-Leaf, MLAG, VXLAN, EVPN, BGP underlay | 🔲 Planned |

## Cisco IOS Lab Series

*Coming soon — Enterprise campus and WAN protocols.*

## Nokia SR Linux Lab Series

*Coming soon — Service provider routing and MPLS.*

---

*Maintained by [Baltej Giri](https://linkedin.com/in/baltej-giri/)*
