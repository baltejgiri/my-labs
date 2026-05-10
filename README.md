# Multi-Vendor Network Engineering Labs

## Purpose
This repository serves as a technical sandbox for mastering Cloud-Scale, Enterprise, and Service Provider networking. The goal is to bridge the gap between traditional networking and **NetOps** (Network Operations) by using Infrastructure as Code (IaC) to build repeatable, documented, and scalable lab environments.

## Repository Structure
The lab is organized by vendor, reflecting the specific industry sectors where each platform excels:

- **/arista** | **Data Center & Cloud**
  - Focus: Spine-Leaf architectures, BGP Underlays, and MLAG redundancy.
  - Objective: Mastering the high-performance fabrics used in modern Hyperscale environments.

- **/cisco** | **Enterprise & Campus**
  - Focus: Hands-on practice for Enterprise environments.
  - Objective: Simulating campus networks, including first-hop redundancy, OSPF, and office-scale switching.

- **/nokia** | **Service Provider (ISP)**
  - Focus: ISP-grade routing and Service Provider architectures.
  - Objective: Exploring SR Linux for high-scale peering, MPLS, and carrier-grade connectivity.

## Featured Lab: Arista MLAG (Multi-Chassis Link Aggregation) Lab

My current primary focus is on Arista EOS for Data Center environments with Layer 2 High Availability.
- **Architecture:** 1-Spine / 2-Leaf MLAG setup with dual-homed hosts.
- **Control Plane:** MLAG Peer-Link and Control Plane for redundancy without STP.
- **Features:** Dual-homing for Linux hosts using LACP Port-Channels, MLAG Peer-Link using Port-Channel.
- **Verification:** Utilizing `show mlag`, `show mlag config-sanity`, `show port-channel summary`, and `show lldp neighbors` to validate redundancy and connectivity.

## Toolchain
- **[Containerlab](https://containerlab.dev):** Orchestration for containerized network topologies.
- **[Docker](https://docker.com):** Container runtime for NOS images (cEOS, SR Linux, IOL).
- **[Git](https://git-scm.com):** Version control for persistent configuration management.

---
*Maintained by Baltej Giri*
