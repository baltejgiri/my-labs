# my-labs

A hands-on lab portfolio built across two tracks: **CCNA topic refresher** labs on Cisco IOS/CML to keep certified fundamentals sharp, and **CCNP Enterprise** certification pursuit — ENCOR 350-401 followed by ENARSI 300-410 — practised on Arista cEOS and Nokia SR Linux via Containerlab.

---

## Two Tracks at a Glance

| Track | Platform | Focus |
|-------|----------|-------|
| CCNA — Cisco IOS Labs | Cisco Modeling Labs (CML) | 10 labs reinforcing core CCNA topics (already certified) |
| CCNP — ENCOR → ENARSI | Containerlab + Arista cEOS / Nokia SR Linux | ENCOR 350-401 followed by ENARSI 300-410 |

---

## Track 1 — CCNA Cisco IOS Labs

Ten labs on **Cisco Modeling Labs (CML)** using IOSv (routers) and IOSv L2 (switches). Purpose is to keep CCNA fundamentals sharp through hands-on practice — not exam prep. Already certified. Each lab follows the exam-paper format: objectives and tasks only, no hints.

| Lab | Topic | CCNA Domain | Weight |
|-----|-------|-------------|--------|
| 01 | IP Addressing & Network Fundamentals | 1.0 Network Fundamentals | 20% |
| 02 | VLANs & Inter-VLAN Routing | 2.0 Network Access | 20% |
| 03 | Spanning Tree Protocol | 2.0 Network Access | 20% |
| 04 | EtherChannel | 2.0 Network Access | 20% |
| 05 | Static Routing & Default Routes | 3.0 IP Connectivity | 25% |
| 06 | OSPFv2 — Single-Area & Multi-Area | 3.0 IP Connectivity | 25% |
| 07 | DHCP & NAT | 4.0 IP Services | 10% |
| 08 | ACLs & Device Security | 5.0 Security Fundamentals | 15% |
| 09 | IPv6 — SLAAC, DHCPv6, OSPFv3, Dual-Stack | 1.0 + 3.0 | — |
| 10 | IP Services — NTP, SNMP, Syslog, HSRP | 4.0 IP Services | 10% |
| — | Automation & Programmability | 6.0 Automation | 10% *(planned)* |

> **Platform note:** Load `topology.cml.yml` from each lab directory into CML. End hosts use Alpine Linux. See [Getting Started — CML](#getting-started----cml-cisco-labs) below.

---

## Track 2 — CCNP Enterprise ENCOR Labs

The core driver for this repository is passing the **Cisco 350-401 ENCOR** exam, followed by ENARSI 300-410. Arista cEOS and Nokia SR Linux serve as the practice platforms — protocol behaviour is identical to IOS-XE, and the structured CLI output makes verification faster.

| ENCOR Domain | Weight | Current Lab Coverage |
|---|---|---|
| 1.0 Architecture | 15% | `dc-starter` — data centre topology (in progress) |
| 2.0 Virtualization | 10% | VRF-Lite concepts via BGP lab (partial) |
| 3.0 Infrastructure | 30% | OSPF, BGP, VLANs, STP, MLAG, LACP, L2 forwarding — strong coverage |
| 4.0 Network Assurance | 10% | Planned |
| 5.0 Security | 20% | Planned |
| 6.0 Automation | 15% | Planned |

> Labs are sequenced to build exam-relevant depth first. Vendor choice (Arista vs Nokia) is secondary — blueprint alignment is what matters.

---

## Repository Layout

```
my-labs/
├── README.MD                  ← You are here
│
├── cisco/                     ← Cisco IOS labs (CML) — CCNA track
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
├── arista/                    ← Arista cEOS labs (Containerlab) — ENCOR track
│   ├── lab01-eos-basics/          ENCOR 3.0 — CLI, management plane, LLDP
│   ├── lab02-l2-switching/        ENCOR 3.0 — 802.1Q, VLANs, trunk negotiation
│   ├── lab03-ospf/                ENCOR 3.0 — OSPF single/multi-area, LSA types
│   ├── lab03b-ospf/               ENCOR 3.0 — OSPF extended scenarios
│   ├── lab04a-bgp-basics/         ENCOR 3.0 — eBGP/iBGP, attributes, filtering
│   ├── l2-forwarding/             ENCOR 3.0 — L2 forwarding deep-dive
│   ├── trunk-lab/                 ENCOR 3.0 — 802.1Q trunking and DTP
│   ├── mlag-lab/                  ENCOR 3.0 — MLAG, LACP, high-availability
│   └── dc-starter/                ENCOR 1.0/3.0 — Data centre topology starter
│
└── nokia/                     ← Nokia SR Linux labs (Containerlab) — ENCOR track
    └── lab01-getting-started/     SR Linux basics, CLI, topology bring-up
```

Each lab folder follows the same three-file structure:

**Cisco labs (CML):**
```
lab-XX-topic/
├── topology.cml.yml   ← CML topology — import directly into CML
├── README.md          ← Exam-paper: objectives and tasks, no hints
└── solution.md        ← Answer sheet: exact CLI output, RCA, lessons learned
```

**Arista / Nokia labs (Containerlab):**
```
lab-XX-topic/
├── <name>.clab.yml    ← Containerlab topology + inline startup configs
├── README.md          ← Exam-paper: objectives and tasks, no hints
└── solution.md        ← Answer sheet: exact CLI output, RCA, lessons learned
```

---

## Lab Philosophy

**README.md = the exam paper.** States the objectives, topology, and tasks. No CLI hints, no expected outputs, no spoilers — forces genuine recall.

**solution.md = the answer sheet.** Captures exact commands run, operational state observed, misconfigurations introduced, and root-cause analysis. Written after completing the lab.

This split mirrors how real exams work: you either know how to read the output or you don't.

---

## Vendor Platforms

### Cisco IOS (CML)

Used for the CCNA track. IOSv and IOSv L2 images run directly in CML with no extra wrapping needed. Command syntax is the exam-standard baseline that everything else is measured against.

### Arista cEOS (Containerlab)

Used as the primary ENCOR practice platform. EOS command structure maps closely enough to IOS-XE that concepts transfer directly, while structured CLI output makes verifying protocol state faster.

### Nokia SR Linux (Containerlab)

Used for service-provider adjacent topics (IS-IS, MPLS, L3VPN/VPRN) that appear in ENCOR's Infrastructure domain and give broader exposure to carrier-grade NOS design.

---

## Toolchain

| Tool | Purpose |
|------|---------|
| [Cisco Modeling Labs (CML)](https://developer.cisco.com/modeling-labs/) | Cisco IOS lab orchestration |
| [Containerlab](https://containerlab.dev) | Arista and Nokia topology orchestration |
| Arista cEOS | EOS node images (manually imported) |
| Nokia SR Linux | SR Linux node images (pulled automatically) |
| Docker | Container runtime for Containerlab |
| Git / GitHub | Version control and lab history |

---

## Getting Started — CML (Cisco Labs)

### Prerequisites

- Cisco Modeling Labs personal licence with IOSv and IOSv L2 node definitions available
- Alpine Linux image available in CML (used for end hosts)

### Import and run a lab

1. Open CML and click **Import** (or **File → Import Lab**)
2. Select `topology.cml.yml` from the lab directory (e.g. `cisco/lab01-ip-addressing/topology.cml.yml`)
3. Click **Start Lab** to boot all nodes
4. Access each node via the built-in CML console

### Stop a lab

Click **Stop Lab** in CML. Use **Wipe** to reset node state between attempts.

---

## Getting Started — Containerlab (Arista / Nokia Labs)

### Prerequisites

- Linux host (Ubuntu 22.04+) or WSL2
- Docker installed and running
- Containerlab installed:

```bash
bash -c "$(curl -sL https://get.containerlab.dev)"
```

- Arista cEOS image imported into Docker:

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

- [CCNA 200-301 Exam Topics](https://learningnetwork.cisco.com/s/ccna-exam-topics)
- [CCNP ENCOR 350-401 Exam Topics](https://learningnetwork.cisco.com/s/encor-exam-topics)
- [Cisco Modeling Labs Documentation](https://developer.cisco.com/docs/modeling-labs/)
- [Containerlab Documentation](https://containerlab.dev/manual/topo-def-file/)
- [Arista EOS Central](https://eos.arista.com)
- [Nokia SR Linux Documentation](https://documentation.nokia.com/srlinux/)
- [RFC Index — IETF](https://www.rfc-editor.org)

---

## Author

**Baltej Giri** — Network Systems Analyst | CCNA | AZ-900 | Pursuing CCNP Enterprise  
[GitHub](https://github.com/baltejgiri)
