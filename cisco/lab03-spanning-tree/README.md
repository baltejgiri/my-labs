# Lab 03 — Spanning Tree Protocol

**CCNA Domain:** 2.0 Network Access  
**CML Topology:** `topology.cml.yml`  
**Estimated Time:** 75–90 minutes

---

## Objectives

1. Observe default STP behaviour and root bridge election
2. Manipulate root bridge placement using bridge priority
3. Control port roles by adjusting port cost
4. Compare PVST+ and Rapid PVST+ convergence
5. Configure PortFast and BPDU Guard on access ports
6. Simulate a link failure and observe reconvergence

---

## Topology

```
         [SW1]
        /  |  \
      GE0/0 GE0/1 GE0/2 GE0/3
      (dual links to SW2 and SW3)

  [SW2] -------- [SW3]
    |                |
   PC2              PC3
```

The topology forms a triangle with redundant uplinks between SW1–SW2 and SW1–SW3, creating multiple loops that STP must resolve.

| Device | Interface | Connects To |
|--------|-----------|-------------|
| SW1 | GigabitEthernet0/0 | SW2 GE0/0 |
| SW1 | GigabitEthernet0/1 | SW2 GE0/1 |
| SW1 | GigabitEthernet0/2 | SW3 GE0/0 |
| SW1 | GigabitEthernet0/3 | SW3 GE0/1 |
| SW1 | GigabitEthernet1/0 | PC1 eth0 |
| SW2 | GigabitEthernet0/2 | SW3 GE0/2 |
| SW2 | GigabitEthernet0/3 | PC2 eth0 |
| SW3 | GigabitEthernet0/3 | PC3 eth0 |

---

## Tasks

### Task 1 — Default STP Observation

1.1 Without making any configuration changes, run `show spanning-tree` on all three switches. Record:
   - The Root Bridge ID (priority + MAC)
   - Each switch's Bridge ID
   - Port roles (Root, Designated, Alternate) on every inter-switch link
   - Which ports are in Forwarding vs Blocking state

1.2 Draw the active logical topology showing which links STP has blocked.

1.3 Identify which switch became root bridge and explain **why** based on the election rules.

### Task 2 — Root Bridge Manipulation

2.1 Make SW1 the root bridge for VLAN 1 by setting its bridge priority to the lowest possible value using the `spanning-tree vlan 1 root primary` command.

2.2 Verify the change. Confirm SW1 is now root and all of its ports are Designated.

2.3 Now set SW2 as the secondary root using `spanning-tree vlan 1 root secondary`. Verify its priority value.

2.4 Simulate root bridge failure by shutting down all inter-switch links on SW1. Observe which switch becomes root. Restore the links when done.

### Task 3 — Port Cost Manipulation

3.1 On SW2, identify which port is the Root Port (the best path to root).

3.2 Increase the STP cost on SW2's current Root Port to 100. Observe which port becomes the new Root Port and why.

3.3 Restore the original port cost.

### Task 4 — Rapid PVST+

4.1 Change all switches from PVST+ to Rapid PVST+ (`spanning-tree mode rapid-pvst`).

4.2 Compare the time to reach a Forwarding state after a link event versus PVST+. What specific mechanism in Rapid PVST+ enables faster convergence?

4.3 Identify the port types (Edge, Point-to-Point, Shared) on each switch.

### Task 5 — PortFast & BPDU Guard

5.1 Configure PortFast on all access ports connecting to PCs (SW1 GE1/0, SW2 GE0/3, SW3 GE0/3).

5.2 Configure BPDU Guard on the same ports.

5.3 Verify PortFast and BPDU Guard are active using `show spanning-tree interface <int> detail`.

5.4 Simulate a rogue switch: connect a loopback cable to one of the PortFast ports (or configure a trunk on the PC port). Observe what happens to the port and how to recover it.

---

## Verification Checklist

- [ ] Default root bridge identified and election logic explained
- [ ] SW1 is root bridge after priority manipulation
- [ ] SW2 is secondary root with correct priority
- [ ] Rapid PVST+ running on all switches
- [ ] PortFast and BPDU Guard enabled on all PC-facing ports
- [ ] PC1 can ping PC2 and PC3
