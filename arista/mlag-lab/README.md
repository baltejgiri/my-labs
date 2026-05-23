# Arista MLAG (Multi-Chassis Link Aggregation) Lab

This lab demonstrates Layer 2 High Availability using Arista MLAG. It allows two physical Leaf switches to appear as a single logical switch to end hosts, providing redundant paths without relying on Spanning Tree (STP) to block links.

## Lab Objectives
- Configure an MLAG Peer-Link using a Port-Channel.
- Establish the MLAG Control Plane (Domain, Peer-Address, and Heartbeat).
- Implement Dual-Homing for Linux hosts using LACP Port-Channels.
- Verify traffic flow and redundancy during a simulated switch failure.

### The Five-Step Configuration Process:
1.  Verify Control Plane ACL: Ensure the MLAG heartbeat port (port 11,111) is allowed.

2.  Configure MLAG Peer Link: Create a Port Channel (e.g., Port Channel 10) using two physical links, ideally across separate line cards for redundancy.

3.  Configure MLAG VLAN: Set up VLAN 4094, assign a `trunk group` to isolate it from other traffic, disable Spanning Tree for this VLAN, and configure the SVI (Switched Virtual Interface) with an IP address.

4.  Define MLAG Domain: Use the `mlag` configuration mode to set the local interface, peer IP address, peer link, and a matching domain ID.

5.  Configure MLAG Member Ports: Assign downstream interfaces (e.g., Ethernet 3-4) to an MLAG-enabled Port Channel.

## Topology
- Spine1: Acts as the Layer 3 core.
- Leaf1 and Leaf2: The MLAG Peer Group.
- Host1 and Host2: Multi-homed servers connected to both Leafs via Port-Channels.

![MLAG LAB Diagram](/arista/mlag-lab/mlag-lab-diagram.png)

## Interface Mapping

| Local Port | Connection | Purpose |
| :--- | :--- | :--- |
| Et1, Et2 | Leaf1 <-> Leaf2 | MLAG Peer-Link (Trunk) |
| Et3 | Leaf <-> Spine | Uplink |
| Et4 | Leaf <-> Host1 | MLAG Port-Channel (LACP) |
| Et5 | Leaf <-> Host2 | MLAG Port-Channel (LACP) |

## Verification Checklist
- show mlag: Check MLAG domain status and peer connectivity.
- show mlag config-sanity: Verify configuration consistency between peers.
- show port-channel summary: Confirm LACP status for hosts and peer-link.
- show lldp neighbors: Validate physical wiring.

Verify the setup using commands like `show mlag`, `show mlag detail`, and `show mlag interfaces` to confirm the active-active status and bandwidth aggregation.

## How to Deploy
1. Ensure Arista cEOS image is available as ceos:latest.
2. Run: sudo containerlab deploy -t mlag.clab.yml
3. Follow the MLAG configuration guide to establish the peering.