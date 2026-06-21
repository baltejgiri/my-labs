# my-labs

A collection of hands-on network engineering labs across three vendors: **Arista**, **Cisco**, and **Nokia**.

---

## Arista & Nokia — Containerlab (Infrastructure as Code)

Arista and Nokia labs are built and managed using [Containerlab](https://containerlab.dev/), an infrastructure-as-code tool for container-based network topologies. Each lab is defined in a `.clab.yml` topology file that specifies nodes, images, links, and startup configuration. Labs are deployed and torn down with a single command, making them fully reproducible.

- **Arista** nodes run **cEOS** (containerized EOS)
- **Nokia** nodes run **SR Linux**

## Cisco — Cisco Modeling Labs (CML)

Cisco labs are built using **Cisco Modeling Labs (CML)**, a network simulation platform for running virtual Cisco IOS/IOS-XE devices. CML supports realistic Cisco hardware emulation and is well-suited for practising IOS configuration, troubleshooting, and exam-style scenarios.

---

## Lab Directories

| Directory | Vendor | Platform |
|-----------|--------|----------|
| `arista/` | Arista | Containerlab (cEOS) |
| `nokia/` | Nokia | Containerlab (SR Linux) |
| `cisco/` | Cisco | Cisco Modeling Labs (CML) |
