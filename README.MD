# my-labs

A structured, hands-on network lab repository built with **Containerlab** and vendor-native NOS images to validate protocol behaviour, sharpen troubleshooting instincts, and document findings in reproducible workbooks.

---

## Purpose

This repository serves three goals:

1. **Skill validation** — Build and break real protocol stacks in isolated topologies rather than reading about them.
2. **Documented evidence** — Every lab produces a `SOLUTION.md` workbook capturing exact CLI inputs, operational state, and root-cause analysis — a living record of what was learned.
3. **Comprehensive coverage** — Labs span every major layer of network engineering, from switching fundamentals and routing protocols through MPLS, multicast, and full data centre design, with a growing service provider track covering Nokia SR Linux and SROS for L3VPN, IS-IS, and MPLS/RSVP use cases common in carrier environments.

## Repository Layout

```
my-labs/
├── README.md                  ← You are here
│
├── arista/                    ← Arista EOS labs (Containerlab + cEOS)
│   ├── lab-01-eos-basics/
│   ├── lab-02-vlans-stp/
│   ├── lab-03-ospf/
│   ├── lab-04-bgp/
│   ├── lab-05-rip-redistribution/
│   ├── lab-06-vrf/
│   ├── lab-07-mpls/
│   ├── lab-08-dhcp-nat-acl/
│   ├── lab-09-aaa-snmp-sflow/
│   ├── lab-10-qos-varp-lacp/
│   ├── lab-11-multicast/
│   └── lab-12-spine-leaf-dc/
│
├── nokia/                     ← Nokia SR Linux / SROS labs (in progress)
│   └── ...
│
└── shared/
    └── clab-tips.md           ← Containerlab operational notes
```

Each lab folder follows the same structure:

```
lab-XX-topic/
├── topology.clab.yml    ← Containerlab topology definition
├── configs/             ← Startup configs per node (optional)
├── README.md            ← Lab guide — objectives, topology diagram, tasks. Zero CLI hints.
└── SOLUTION.md          ← Validation workbook — full commands, outputs, RCA notes
```

## Lab Tracks

### Arista EOS — Foundation to Data Centre

| # | Lab | Key Protocols / Features |
|---|-----|--------------------------|
| 01 | EOS Basics | CLI navigation, management plane, LLDP, ARP, ICMP |
| 02 | VLANs & Spanning Tree | 802.1Q, STP / RSTP / MSTP, port roles, topology changes |
| 03 | OSPF | Single-area, multi-area, LSA types, DR/BDR, troubleshooting |
| 04 | BGP | eBGP / iBGP, attributes, route reflectors, filtering, communities |
| 05 | RIP & Redistribution | RIPv2, redistribution between OSPF / BGP / RIP, metric manipulation |
| 06 | VRF | VRF-Lite, inter-VRF route leaking |
| 07 | MPLS | LDP, LFIB, L3VPN basics |
| 08 | DHCP, NAT & ACLs | DHCP server/relay, static/dynamic NAT, standard/extended ACLs |
| 09 | Management Plane | AAA / TACACS+, SNMP v2c/v3, sFlow |
| 10 | QoS & HA | DSCP/CoS marking, LACP / MLAG, VARP, VRRP |
| 11 | Multicast | IGMP, PIM sparse-mode, PIM dense-mode, RP configuration |
| 12 | Spine-Leaf DC | BGP underlay, VXLAN/EVPN overlay, MLAG, BFD — full data centre build |

### Nokia

Labs for Nokia SR Linux and SROS will be added here as they are built. Initial focus areas include IS-IS, MPLS/RSVP, L3VPN (VPRN), and SROS service model troubleshooting.

## Lab Philosophy

**README.md = the exam paper.** It states the objectives, topology, and tasks. No configuration hints, no expected outputs, no CLI spoilers.

**SOLUTION.md = the answer sheet.** It captures the exact commands run, the operational state observed, any misconfigurations introduced, and the root-cause analysis. Written after the lab is completed.

This split forces genuine recall during the lab and produces a searchable troubleshooting record afterward.

## Toolchain

| Tool | Purpose |
|------|---------|
| [Containerlab](https://containerlab.dev) | Topology orchestration |
| Arista cEOS | EOS node images |
| Nokia SR Linux / SROS | Nokia node images |
| Docker | Container runtime |
| Git / GitHub | Version control and lab history |
| VS Code | Lab authoring and config editing |

## Getting Started

### Prerequisites

- Linux host (Ubuntu 22.04+ recommended) or WSL2
- Docker installed and running
- Containerlab installed:

```bash
bash -c "$(curl -sL https://get.containerlab.dev)"
```

- Arista cEOS image imported into Docker:

```bash
docker import cEOS-lab-x.x.x.tar.xz ceos:x.x.x
```

### Deploy a lab

```bash
cd arista/lab-01-eos-basics
sudo clab deploy -t topology.clab.yml
```

### Connect to a node

```bash
ssh admin@clab-<lab-name>-<node-name>
# or
docker exec -it clab-<lab-name>-<node-name> Cli
```

### Destroy a lab

```bash
sudo clab destroy -t topology.clab.yml
```

## References

- [Arista EOS Central](https://eos.arista.com)
- [Arista Documentation](https://arista.com/docs)
- [Containerlab Documentation](https://containerlab.dev/manual/topo-def-file/)
- [Nokia SR Linux Documentation](https://documentation.nokia.com/srlinux/)
- [Nokia SROS Documentation](https://documentation.nokia.com/cgi-bin/dbaccessfilename.cgi)
- [RFC Index — IETF](https://www.rfc-editor.org)

---

## Author

**Baltej Giri** — Network Systems Analyst | CCNA | AZ-900  
[Blogpost](https://baltejgiri.com) · [GitHub](https://github.com/baltejgiri) · [YouTube](https://www.youtube.com/@baltegiri)