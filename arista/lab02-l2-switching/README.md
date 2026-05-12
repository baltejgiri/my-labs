# Lab 02 — VLANs, STP, RSTP, and MSTP
**Topics:** Layer 2 Switching, VLAN Segmentation, STP, RSTP, MSTP  
**Platform:** ContainerLab + Arista cEOS  
**Repo:** https://github.com/baltejgiri/my-labs  
**Reference:** [Arista EOS Spanning Tree Protocol](https://www.arista.com/en/um-eos/eos-spanning-tree-protocol)

---

## Topology

```bash

      [PC-10A]        [PC-20A]
         |                |
       Et1(VLAN10)    Et2(VLAN20)
          \               \
          [SW1]---Et5---[SW2]
          /   \          /   \
        Et3   Et4      Et3   Et4
          \     \      /     /
           \     [SW3]      /    <-- Root Bridge (priority 4096)
            \   /     \   /
            Et1         Et2
             |           |
          Et5(V10)    Et6(V20)
         [PC-10B]    [PC-20B]
```

| Node   | Role         | VLAN 10 IP     | VLAN 20 IP     |
|--------|--------------|----------------|----------------|
| SW1    | Distribution | —              | —              |
| SW2    | Distribution | —              | —              |
| SW3    | Core / Root  | —              | —              |
| PC-10A | Host (SW1)   | 10.10.10.10/24 | —              |
| PC-20A | Host (SW1)   | —              | 10.20.20.10/24 |
| PC-10B | Host (SW3)   | 10.10.10.20/24 | —              |
| PC-20B | Host (SW3)   | —              | 10.20.20.20/24 |

**Loop design:** SW1↔SW3 (2 links) + SW2↔SW3 (2 links) + SW1↔SW2 (1 link) creates a triangle with redundant paths — STP must block ports to prevent broadcast storms.

## Prerequisites

- Lab 01 completed (EOS CLI basics)
- ContainerLab running with cEOS image available

## Deploy & Destroy

# Deploy

```bash
sudo containerlab deploy -t ~/my-labs/arista/lab02-l2-switching/lab02-l2-switching.clab.yml
```

# Inspect
```bash
sudo containerlab inspect -t ~/my-labs/arista/lab02-l2-switching/lab02-l2-switching.clab.yml
```

# Access nodes
```bash
docker exec -it clab-lab02-l2-switching-sw1 Cli
docker exec -it clab-lab02-l2-switching-sw2 Cli
docker exec -it clab-lab02-l2-switching-sw3 Cli
docker exec -it clab-lab02-l2-switching-pc10a sh
docker exec -it clab-lab02-l2-switching-pc20b sh
```

# Destroy
```bash
sudo containerlab destroy -t ~/my-labs/arista/lab02-l2-switching/lab02-l2-switching.clab.yml --cleanup
```

## Background — STP Protocol Comparison

| Feature          | STP (802.1D)    | RSTP (802.1w)           | MSTP (802.1s)               |
|------------------|-----------------|-------------------------|-----------------------------|
| Convergence time | 30–50 seconds   | 1–2 seconds             | 1–2 seconds                 |
| Instances        | 1 (all VLANs)   | 1 per VLAN (Rapid-PVST) | Multiple VLANs per instance |
| Port states      | 5 states        | 3 states                | 3 states                    |
| Default on EOS   | No              | No                      | **Yes**                     |
| Load balancing   | No              | Per-VLAN                | Per-instance                |
| Standard         | IEEE 802.1D     | IEEE 802.1w             | IEEE 802.1s                 |

**Key EOS fact:** MSTP is the default. All switches in an MST region must share the same region name, revision number, and VLAN-to-instance mapping.

## Task 1 — VLAN Configuration (All Switches)

Configure VLANs and switchport modes on all three switches before touching STP.

- Create VLAN 10 (name: OPERATIONS) and VLAN 20 (name: ENGINEERING) on SW1, SW2, and SW3
- On SW1: configure Ethernet1 as an access port for VLAN 10 with PortFast enabled
- On SW1: configure Ethernet2 as an access port for VLAN 20 with PortFast enabled
- On SW1: configure Ethernet3 and Ethernet4 as trunk ports to SW3 allowing VLANs 10 and 20
- On SW1: configure Ethernet5 as a trunk port to SW2 allowing VLANs 10 and 20
- On SW2: configure Ethernet3 and Ethernet4 as trunk ports to SW3 allowing VLANs 10 and 20
- On SW2: configure Ethernet5 as a trunk port to SW1 allowing VLANs 10 and 20
- On SW3: configure Ethernet1 and Ethernet2 as trunk ports to SW1 allowing VLANs 10 and 20
- On SW3: configure Ethernet3 and Ethernet4 as trunk ports to SW2 allowing VLANs 10 and 20
- On SW3: configure Ethernet5 as an access port for VLAN 10 with PortFast enabled
- On SW3: configure Ethernet6 as an access port for VLAN 20 with PortFast enabled

**Verify before moving on:**
- `show vlan brief` shows VLAN 10 and 20 active on all switches
- `show interfaces trunk` shows trunk ports with correct allowed VLANs
- `show interfaces status` shows all ports as connected

## Task 2 — Part A: MSTP (Default on Arista EOS)

MSTP is already running by default. Map VLANs to MST instances and control which switch becomes root per instance.

- Confirm the current STP mode on all switches using `show spanning-tree`
- On SW1, SW2, and SW3: enter MST configuration mode and configure region name `LAB02-REGION`, revision `1`, VLAN 10 → instance 1, VLAN 20 → instance 2
- Set SW3 as root bridge for both MST instance 1 and MST instance 2 using bridge priority
- Set SW1 as secondary root for MST instance 1
- Set SW2 as secondary root for MST instance 2

**Verify:**
- `show spanning-tree mst 1` — confirm SW3 is root, identify which ports are blocking on SW1 and SW2
- `show spanning-tree mst 2` — confirm SW3 is root, check if VLAN 20 takes a different path than VLAN 10
- `show spanning-tree mst configuration` — confirm region name and instance map matches on all three switches

**Questions to answer in your own notes:**
- Which ports ended up in blocking state and why?
- Are VLAN 10 and VLAN 20 using different forwarding paths?
- What happens if you change the region name on SW1 only?

## Task 2 — Part B: MSTP Fault Injection

- Change the MST region name on SW1 only to something different — observe what happens to the topology
- Use `show spanning-tree mst configuration` to diagnose the mismatch
- Restore the correct region name and verify the topology recovers

## Task 3 — Part A: RSTP (Rapid-PVST)

Switch all three nodes from MSTP to Rapid-PVST mode and reconfigure root bridge per VLAN.

- Change the STP mode to `rapid-pvst` on SW1, SW2, and SW3
- Set SW3 as root bridge for VLAN 10 using bridge priority
- Set SW3 as root bridge for VLAN 20 using bridge priority
- Set SW1 as secondary root for VLAN 10
- Set SW2 as secondary root for VLAN 20

**Verify:**
- `show spanning-tree vlan 10` — confirm SW3 is root, check port roles on SW1 and SW2
- `show spanning-tree vlan 20` — confirm SW3 is root, check if path differs from VLAN 10
- `show spanning-tree detail` — review port states (forwarding / blocking / discarding)

## Task 3 — Part B: RSTP Convergence Test

Measure how fast RSTP recovers from a link failure.

- Open a shell on PC-10A and start a continuous ping to PC-10B at 200ms intervals
- In a separate terminal on SW1: shut down Ethernet3 (one trunk link to SW3)
- Count the number of dropped pings — RSTP should recover in approximately 1–2 seconds
- Re-enable Ethernet3 and observe reconvergence
- Record your observation: how many pings were lost?

## Task 4 — Part A: Classic STP Timers (802.1D Behaviour)

Simulate classic STP convergence by adjusting timers on the root bridge to match 802.1D defaults, then repeat the convergence test to compare.

> **EOS Note:** Arista EOS does not have a standalone `stp` mode keyword. Classic 802.1D behaviour is simulated by setting STP timers to their 802.1D defaults. Look up the EOS commands for `spanning-tree hello-time`, `forward-time`, and `max-age` on the Arista documentation.

- On SW3 (root bridge): configure hello-time, forward-time, and max-age to their classic 802.1D default values
- Confirm SW3 is still root for both VLANs
- Repeat the convergence test from Task 3B — shut a trunk port on SW1 while pinging PC-10B from PC-10A
- Record how many pings were lost and how long recovery took compared to RSTP

**Questions to answer in your own notes:**
- How many seconds did convergence take vs RSTP?
- Which specific timers are responsible for the slow convergence?
- Would you ever use classic STP timers in a production network?

## Task 4 — Part B: Restore to MSTP

After completing the STP comparison, restore all switches back to MSTP (the EOS default).

- Re-apply MSTP mode on all three switches
- Re-apply MST region name, revision, and instance mapping
- Re-apply root bridge priorities on SW3
- Verify with `show spanning-tree` that the topology matches Task 2

## Task 5 — MAC Address Table

- Generate traffic by pinging between PC-10A and PC-10B
- On SW1: view the full MAC address table and identify which MACs were learned on which ports
- On SW1: view the MAC table filtered by VLAN 10
- On SW1: view the MAC table filtered by a specific interface
- Clear the MAC address table dynamically and observe it repopulate after a ping
- Check the MAC aging timer — what is the default value in EOS?

## Task 6 — Trunk Verification and Fault Injection

- On SW1: display all trunk ports and confirm which VLANs are allowed vs active
- Remove VLAN 10 from the allowed list on SW1's Ethernet3 trunk port
- Attempt to ping from PC-10A to PC-10B — does it still work? Why or why not?
- Use `show interfaces trunk` and `show spanning-tree vlan 10` to diagnose
- Add VLAN 10 back to the trunk and confirm connectivity restores

## Troubleshooting Scenarios

Work through these without looking up the fix first — document your methodology.

**Scenario A — STP Region Mismatch**
- SW1 has been misconfigured with a different MST region name
- Symptom: unexpected topology changes, some VLANs stop forwarding
- Diagnose using: `show spanning-tree mst configuration`, `show spanning-tree`

**Scenario B — VLAN Missing on Trunk**
- VLAN 20 has been removed from the trunk between SW2 and SW3
- Symptom: PC-20B cannot ping PC-20A
- Diagnose using: `show interfaces trunk`, `show vlan`, `show mac address-table`

**Scenario C — PortFast on a Trunk Port**
- PortFast has been enabled on SW1's Ethernet3 (a trunk to SW3)
- Symptom: EOS issues a warning — observe it
- Task: identify the risk, remove PortFast from the trunk port

## Key Show Commands Reference

| Command | What it shows |
|---|---|
| `show spanning-tree` | Full STP state for all instances |
| `show spanning-tree mst <n>` | Specific MST instance detail |
| `show spanning-tree vlan <n>` | Per-VLAN STP state (Rapid-PVST) |
| `show spanning-tree detail` | Port roles, states, costs, timers |
| `show spanning-tree mst configuration` | MST region name, revision, instance map |
| `show vlan` | All VLANs and their port memberships |
| `show vlan brief` | Summary VLAN table |
| `show interfaces trunk` | Trunk ports and allowed VLANs |
| `show interfaces status` | Port status table |
| `show mac address-table` | L2 forwarding table |
| `show mac address-table vlan <n>` | MAC table filtered by VLAN |

## Key Takeaways

- MSTP is the default on Arista EOS — all switches in a region must share the same name, revision, and VLAN-to-instance map
- MSTP instances allow VLAN load balancing across redundant links — VLAN 10 can use one path while VLAN 20 uses another
- RSTP converges in ~1–2 seconds vs 30–50 seconds for classic STP timers
- `spanning-tree portfast` should only ever be used on access ports connected to end hosts — never on trunk ports
- Always manually set the root bridge with `priority` — never leave it to election in a real network

---

## Next Lab

**Lab 03 — OSPF: Single-Area, Multi-Area, and Troubleshooting**  
Moves from Layer 2 into Layer 3 routing with Arista EOS.