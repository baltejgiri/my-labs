# my-labs

A hands-on lab portfolio built with **Containerlab** and vendor-native NOS images, structured around the **CCNP Enterprise ENCOR (350-401)** exam blueprint. Labs cover every major ENCOR domain — switching, routing, wireless, SD-WAN/SD-Access architecture, security, QoS, and automation — using Arista cEOS and Nokia SR Linux as the practice platforms.

---

## Cisco IOS Labs — CCNA Coverage

Ten labs built on **Cisco Modeling Labs (CML)** using IOSv and IOSv L2 images. Each lab maps directly to a CCNA 200-301 exam domain and is structured as an exam paper (objectives + tasks, no hints) with a separate solution workbook.

| Lab | Topic | CCNA Domain |
|-----|-------|-------------|
| 01 | IP Addressing & Network Fundamentals | 1.0 — 20% |
| 02 | VLANs & Inter-VLAN Routing | 2.0 — 20% |
| 03 | Spanning Tree Protocol | 2.0 — 20% |
| 04 | EtherChannel | 2.0 — 20% |
| 05 | Static Routing & Default Routes | 3.0 — 25% |
| 06 | OSPFv2 — Single-Area & Multi-Area | 3.0 — 25% |
| 07 | DHCP & NAT | 4.0 — 10% |
| 08 | ACLs & Device Security | 5.0 — 15% |
| 09 | IPv6 — SLAAC, DHCPv6, OSPFv3, Dual-Stack | 1.0 + 3.0 |
| 10 | IP Services — NTP, SNMP, Syslog, HSRP | 4.0 — 10% |

> **Platform:** Load `topology.cml.yml` from each lab directory into CML. All labs use IOSv (routers) and IOSv L2 (switches) node definitions. End hosts use Alpine Linux.

---

## Primary Focus — CCNP Enterprise (ENCOR 350-401)

The core driver for this repository is passing the **Cisco 350-401 ENCOR** exam (target: November 2026). Every lab is intentionally tied to at least one ENCOR blueprint domain:

| ENCOR Domain | Weight | Coverage in This Repo |
|---|---|---|
| 1.0 Architecture | 15% | SD-Access / SD-WAN design labs, campus/DC topology |
| 2.0 Virtualization | 10% | VRF, hypervisor/container concepts |
| 3.0 Infrastructure | 30% | OSPF, BGP, EIGRP, VLANs, STP, FHRP, QoS, MPLS |
| 4.0 Network Assurance | 10% | SNMP, syslog, IP SLAs, telemetry |
| 5.0 Security | 20% | AAA, ACLs, 802.1X, Zone-Based Firewall |
| 6.0 Automation | 15% | Python, REST APIs, NETCONF/YANG, Ansible |

> Labs are sequenced to build exam-relevant depth first. Vendor choice (Arista vs Nokia) is secondary — the protocol behaviour and ENCOR blueprint alignment are what matter.

---

## Repository Layout

```
my-labs/
├── README.MD                  ← You are here
│
├── cisco/                     ← Cisco IOS labs (CML — Cisco Modeling Labs)
│   ├── lab01-ip-addressing/       CCNA 1.0 — VLSM, subnetting, CDP/LLDP
│   ├── lab02-vlans-intervlan/     CCNA 2.0 — VLANs, 802.1Q trunks, Router-on-a-Stick
│   ├── lab03-spanning-tree/       CCNA 2.0 — PVST+, Rapid PVST+, PortFast, BPDU Guard
│   ├── lab04-etherchannel/        CCNA 2.0 — LACP, PAgP, EtherChannel bundles
│   ├── lab05-static-routing/      CCNA 3.0 — Static routes, default routes, floating static
│   ├── lab06-ospf/                CCNA 3.0 — OSPFv2 single-area and multi-area
│   ├── lab07-dhcp-nat/            CCNA 4.0 — DHCP server/relay, static NAT, PAT
│   ├── lab08-acls-security/       CCNA 5.0 — Standard/extended/named ACLs, SSH hardening
│   ├── lab09-ipv6/                CCNA 1.0/3.0 — IPv6, SLAAC, DHCPv6, OSPFv3, dual-stack
│   └── lab10-ip-services/         CCNA 4.0 — NTP, SNMP, Syslog, HSRP/FHRP
│
├── arista/                    ← Arista cEOS labs (Containerlab) — ENCOR-aligned
│   ├── lab01-eos-basics/          ENCOR 3.0 — CLI, management plane, LLDP
│   ├── lab02-l2-switching/        ENCOR 3.0 — 802.1Q, VLANs, trunk negotiation
│   ├── lab03-ospf/                ENCOR 3.0 — OSPF single/multi-area, LSA types
│   ├── lab03b-ospf/               ENCOR 3.0 — OSPF variant / extended scenarios
│   ├── lab04a-bgp-basics/         ENCOR 3.0 — eBGP/iBGP, attributes, filtering
│   ├── l2-forwarding/             ENCOR 3.0 — L2 forwarding deep-dive
│   ├── trunk-lab/                 ENCOR 3.0 — 802.1Q trunking and DTP
│   ├── mlag-lab/                  ENCOR 3.0 — MLAG, LACP, high-availability
│   └── dc-starter/                ENCOR 1.0/3.0 — Data centre topology starter
│
├── nokia/                     ← Nokia SR Linux labs (Containerlab) — ENCOR-aligned
│   └── lab01-getting-started/     SR Linux basics, CLI, topology bring-up
│
└── ccnp-exam-prep-2026-2027/  ← Study notes and exam tracking (separate track)
```

Each lab folder follows the same structure:

```
lab-XX-topic/
├── <name>.clab.yml      ← Containerlab topology + inline startup configs
├── README.md            ← Exam-paper style: objectives and tasks only, no hints
└── solution.md          ← Answer sheet: exact CLI output, RCA, lessons learned
```

---

## Lab Philosophy

**README.md = the exam paper.** States the objectives, topology, and tasks. No CLI hints, no expected outputs, no spoilers — forces genuine recall.

**solution.md = the answer sheet.** Captures exact commands run, operational state observed, misconfigurations introduced, and root-cause analysis. Written after completing the lab.

This split mirrors the ENCOR exam's troubleshooting questions: you either know how to interpret the output or you don't.

---

## Vendor Platforms

### Arista cEOS

Used as the primary ENCOR practice platform. EOS command structure maps closely enough to IOS-XE that concepts transfer directly, while EOS's structured CLI output makes verifying protocol state faster.

### Nokia SR Linux

Used for service-provider adjacent topics (IS-IS, MPLS, L3VPN/VPRN) that appear in ENCOR's Infrastructure domain and give broader exposure to carrier-grade NOS design.

---

## Toolchain

| Tool | Purpose |
|------|---------|
| [Containerlab](https://containerlab.dev) | Topology orchestration |
| Arista cEOS | EOS node images |
| Nokia SR Linux | SR Linux node images |
| Docker | Container runtime |
| Git / GitHub | Version control and lab history |

---

## Getting Started

### Prerequisites

- Linux host (Ubuntu 22.04+) or WSL2
- Docker installed and running
- Containerlab installed:

```bash
bash -c "$(curl -sL https://get.containerlab.dev)"
```

- Arista cEOS image imported:

```bash
docker import cEOS-lab-x.x.x.tar.xz ceos:latest
```

### Deploy a lab

```bash
sudo clab deploy -t arista/lab01-eos-basics/<name>.clab.yml
```

### Connect to a node

```bash
# Arista cEOS
docker exec -it clab-<lab>-<node> Cli

# Nokia SR Linux
docker exec -it clab-<lab>-<node> sr_cli
```

### Destroy a lab

```bash
sudo clab destroy -t <path>.clab.yml --cleanup
```

---

## References

- [CCNP ENCOR Exam Blueprint (350-401)](https://learningnetwork.cisco.com/s/encor-exam-topics)
- [Containerlab Documentation](https://containerlab.dev/manual/topo-def-file/)
- [Arista EOS Central](https://eos.arista.com)
- [Nokia SR Linux Documentation](https://documentation.nokia.com/srlinux/)
- [RFC Index — IETF](https://www.rfc-editor.org)

---

## Author

**Baltej Giri** — Network Systems Analyst | CCNA | AZ-900 | Pursuing CCNP Enterprise (ENCOR Nov 2026)  
[GitHub](https://github.com/baltejgiri)
