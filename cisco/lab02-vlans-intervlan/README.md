# Lab 02 — VLANs & Inter-VLAN Routing

**CCNA Domains:** 2.0 Network Access · 3.0 IP Connectivity  
**CML Topology:** `topology.cml.yml`  
**Estimated Time:** 75–105 minutes

---

## Objectives

1. Create and name VLANs on Cisco switches
2. Configure 802.1Q trunk ports between switches and between a switch and a router
3. Configure access ports and assign them to VLANs
4. Implement Router-on-a-Stick (subinterfaces) on R1 for inter-VLAN routing
5. Verify intra-VLAN and inter-VLAN connectivity

---

## Topology

```
          [R1]
           |  GE0/0 (trunk)
      [SW1-GE0/0]
      /    |    \
GE0/1  GE0/2  GE0/3
  |      |      \
SW2    PC1      PC2
GE0/1
  |
 PC3
```

| Device | Interface | Connects To | Purpose |
|--------|-----------|-------------|---------|
| R1 | GigabitEthernet0/0 | SW1 GE0/0 | Router-on-a-Stick trunk |
| SW1 | GigabitEthernet0/0 | R1 GE0/0 | Uplink trunk |
| SW1 | GigabitEthernet0/1 | SW2 GE0/0 | Inter-switch trunk |
| SW1 | GigabitEthernet0/2 | PC1 eth0 | Access — VLAN 10 |
| SW1 | GigabitEthernet0/3 | PC2 eth0 | Access — VLAN 20 |
| SW2 | GigabitEthernet0/0 | SW1 GE0/1 | Uplink trunk |
| SW2 | GigabitEthernet0/1 | PC3 eth0 | Access — VLAN 10 |

---

## VLAN & Addressing Plan

| VLAN | Name | Subnet | Gateway |
|------|------|--------|---------|
| 10 | Sales | 192.168.10.0/24 | 192.168.10.1 |
| 20 | Engineering | 192.168.20.0/24 | 192.168.20.1 |
| 99 | Management | 192.168.99.0/24 | 192.168.99.1 |

| Host | VLAN | IP Address | Default Gateway |
|------|------|------------|-----------------|
| PC1 | 10 | 192.168.10.10/24 | 192.168.10.1 |
| PC2 | 20 | 192.168.20.10/24 | 192.168.20.1 |
| PC3 | 10 | 192.168.10.20/24 | 192.168.10.1 |

---

## Tasks

### Task 1 — VLAN Creation

1.1 Create VLANs 10, 20, and 99 on both SW1 and SW2. Assign the names from the table above.

1.2 Verify the VLAN database on both switches using the appropriate show command.

1.3 Confirm that no ports are yet assigned to any VLAN (other than the default VLAN 1).

### Task 2 — Trunk Configuration

2.1 Configure the link between SW1 and R1 (SW1 GE0/0) as an 802.1Q trunk. Explicitly set the encapsulation type. Allow only VLANs 10, 20, and 99 on this trunk.

2.2 Configure the link between SW1 and SW2 as a trunk. Allow the same VLANs.

2.3 Verify trunk status on SW1 using `show interfaces trunk`. Confirm the allowed, active, and forwarding VLAN lists.

### Task 3 — Access Port Configuration

3.1 Assign SW1 GE0/2 to VLAN 10 and SW1 GE0/3 to VLAN 20. Set them as access ports.

3.2 Assign SW2 GE0/1 to VLAN 10. Set it as an access port.

3.3 Verify each port's mode and VLAN assignment with `show interfaces switchport`.

### Task 4 — Router-on-a-Stick

4.1 On R1, create subinterfaces on GE0/0 for each VLAN: `GE0/0.10`, `GE0/0.20`, `GE0/0.99`. Bring the physical interface up with no IP address.

4.2 Configure 802.1Q encapsulation and an IP address matching the gateway from the table on each subinterface.

4.3 Enable the physical interface if it is not already up.

4.4 Verify subinterface state with `show ip interface brief`.

### Task 5 — Host Configuration & Verification

5.1 Configure PC1, PC2, and PC3 with the IP addresses and gateways from the addressing table.

5.2 Ping within VLAN 10: PC1 → PC3. Confirm success.

5.3 Ping across VLANs: PC1 → PC2. Confirm success and identify where the routing occurs.

5.4 Run `show ip route` on R1 and identify the connected routes for each subinterface.

5.5 On SW1, run `show mac address-table` and correlate MAC entries to VLAN membership.

---

## Verification Checklist

- [ ] VLANs 10, 20, 99 exist in the VLAN database on both switches
- [ ] Trunk ports show correct allowed VLAN list
- [ ] Access ports are in the correct VLAN
- [ ] R1 subinterfaces are Up/Up
- [ ] PC1 can ping PC3 (same VLAN, different switch)
- [ ] PC1 can ping PC2 (inter-VLAN, via R1)
