# Arista MLAG (Multi-Chassis Link Aggregation) Lab

This lab demonstrates Layer 2 High Availability using Arista MLAG. It allows two physical Leaf switches to appear as a single logical switch to end hosts, providing redundant paths without relying on Spanning Tree (STP) to block links.

## Lab Objectives
- Configure an MLAG Peer-Link using a Port-Channel.
- Establish the MLAG Control Plane (Domain, Peer-Address, and Heartbeat).
- Implement Dual-Homing for Linux hosts using LACP Port-Channels.
- Verify traffic flow and redundancy during a simulated switch failure.

## Topology
- Spine1: Acts as the Layer 3 core.
- Leaf1 and Leaf2: The MLAG Peer Group.
- Host1 and Host2: Multi-homed servers connected to both Leafs via Port-Channels.

## Interface Mapping

| Local Port | Connection | Purpose |
| :--- | :--- | :--- |
| Et1, Et2 | Leaf1 <-> Leaf2 | MLAG Peer-Link (Trunk) |
| Et3 | Leaf <-> Spine | Uplink |
| Et4 | Leaf <-> Host1 | MLAG Port-Channel (LACP) |
| Et5 | Leaf <-> Host2 | MLAG Port-Channel (LACP) |

## Verification Checklist (TSE Essentials)
- show mlag: Check MLAG domain status and peer connectivity.
- show mlag config-sanity: Verify configuration consistency between peers.
- show port-channel summary: Confirm LACP status for hosts and peer-link.
- show lldp neighbors: Validate physical wiring.

## How to Deploy
1. Ensure Arista cEOS image is available as ceos:latest.
2. Run: sudo containerlab deploy -t mlag.clab.yml
3. Follow the MLAG configuration guide to establish the peering.