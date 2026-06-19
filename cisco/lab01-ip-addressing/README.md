# Lab 01 — IP Addressing & Network Fundamentals

**CCNA Domain:** 1.0 Network Fundamentals  
**CML Topology:** `topology.cml.yml`  
**Estimated Time:** 60–90 minutes

---

## Objectives

1. Design a VLSM addressing scheme from a given address block
2. Configure IPv4 addresses on router interfaces and loopback interfaces
3. Verify end-to-end connectivity using `ping` and `traceroute`
4. Use CDP and LLDP to discover and map the physical topology
5. Analyse ARP and MAC address table behaviour

---

## Topology

```
  PC1          PC2
   |            |
 [SW1-GE0/1] [SW1-GE0/2]
        |
    [SW1-GE0/0]
        |
    [R1-GE0/1]

[R1-GE0/0]---[R2-GE0/0]  [R2-GE0/1]---[R3-GE0/0]
```

| Device | Interface | Connects To |
|--------|-----------|-------------|
| R1 | GigabitEthernet0/0 | R2 GE0/0 |
| R1 | GigabitEthernet0/1 | SW1 GE0/0 |
| R2 | GigabitEthernet0/0 | R1 GE0/0 |
| R2 | GigabitEthernet0/1 | R3 GE0/0 |
| R3 | GigabitEthernet0/0 | R2 GE0/1 |
| SW1 | GigabitEthernet0/0 | R1 GE0/1 |
| SW1 | GigabitEthernet0/1 | PC1 eth0 |
| SW1 | GigabitEthernet0/2 | PC2 eth0 |

---

## Address Space

You are allocated: **10.0.0.0/24**

Design a VLSM scheme to satisfy all segments below. Document your plan **before** configuring anything.

| Segment | Connected Devices | Hosts Required |
|---------|-------------------|----------------|
| R1 — R2 point-to-point | R1, R2 | 2 |
| R2 — R3 point-to-point | R2, R3 | 2 |
| LAN (SW1) | R1, PC1, PC2 | 3 |
| R1 Loopback (Lo0) | R1 | 1 |
| R2 Loopback (Lo0) | R2 | 1 |
| R3 Loopback (Lo0) | R3 | 1 |

---

## Tasks

### Task 1 — VLSM Planning

1.1 Using VLSM, divide **10.0.0.0/24** into subnets that satisfy the table above. Allocate the smallest valid subnet to each segment.

1.2 Record the network address, prefix length, usable IP range, and broadcast address for every subnet.

1.3 Assign a specific IP address from each subnet to every interface in the topology.

### Task 2 — Interface Configuration

2.1 Configure IPv4 addresses and subnet masks on all routed interfaces on R1, R2, and R3 per your plan.

2.2 Configure loopback interfaces (Lo0) on each router.

2.3 Assign static IPv4 addresses, masks, and default gateways to PC1 and PC2.

2.4 Verify that all interfaces show `up/up` in `show ip interface brief`.

### Task 3 — Connectivity Verification

3.1 From R1, ping every interface on R2 and R3.

3.2 From PC1, attempt to ping PC2. Record whether it succeeds and explain why or why not given the current configuration.

3.3 From PC1, attempt to ping R3's loopback. Explain the result.

3.4 Run `traceroute` from R1 to R3's loopback and record the path.

### Task 4 — CDP & LLDP

4.1 Run `show cdp neighbors` on R1. Identify every field in the output.

4.2 Run `show cdp neighbors detail` on R2 and record the IP addresses of its neighbours.

4.3 Enable LLDP globally on all three routers.

4.4 Compare `show lldp neighbors` with `show cdp neighbors` on R2. Note any differences.

### Task 5 — ARP & MAC Address Analysis

5.1 Ping PC2 from PC1. Immediately run `show ip arp` on R1 and `show mac address-table` on SW1.

5.2 Identify which MAC address corresponds to PC1 and which to PC2 in the MAC table.

5.3 Clear the ARP cache on R1 (`clear ip arp`). Ping PC2 from PC1 again and observe what changes.

---

## Verification Checklist

- [ ] All interfaces are Up/Up
- [ ] VLSM plan documented before configuration
- [ ] PC1 can ping PC2
- [ ] R1 can ping R3 loopback
- [ ] CDP neighbours correct on all routers
- [ ] LLDP enabled and neighbours visible
- [ ] ARP table entries match expected MACs
