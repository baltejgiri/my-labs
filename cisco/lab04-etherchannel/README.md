# Lab 04 — EtherChannel

**CCNA Domain:** 2.0 Network Access  
**CML Topology:** `topology.cml.yml`  
**Estimated Time:** 60–75 minutes

---

## Objectives

1. Configure a Layer 2 EtherChannel using LACP (802.3ad)
2. Configure a Layer 2 EtherChannel using PAgP
3. Configure a static (mode-on) EtherChannel and identify its limitations
4. Verify bundle status, member ports, and load-balancing algorithm
5. Test link failure and recovery within a bundle

---

## Topology

```
[PC1]---[SW1 GE1/0]    [SW2 GE1/0]---[PC2]

         SW1                SW2
       GE0/0 ─────────── GE0/0
       GE0/1 ─────────── GE0/1    ← 4 parallel links
       GE0/2 ─────────── GE0/2      bundled into
       GE0/3 ─────────── GE0/3      Port-Channel 1
```

| Device | Interfaces | Role |
|--------|-----------|------|
| SW1 | GE0/0–GE0/3 | EtherChannel members |
| SW1 | GE1/0 | Access — PC1 |
| SW2 | GE0/0–GE0/3 | EtherChannel members |
| SW2 | GE1/0 | Access — PC2 |

---

## Tasks

### Task 1 — Observe Pre-Bundle Behaviour

1.1 Before configuring any EtherChannel, look at the STP state of all four links between SW1 and SW2. Record which ports are forwarding and which are blocking.

1.2 Run `show interfaces GigabitEthernet0/0 counters` on SW1. Note the current byte and packet counts.

### Task 2 — LACP EtherChannel

2.1 On both SW1 and SW2, bundle GE0/0 and GE0/1 into **Port-Channel 1** using LACP **active** mode on SW1 and LACP **passive** mode on SW2.

2.2 Verify the bundle formed: `show etherchannel summary`. Confirm the channel state shows `SU` (Layer 2, in use) and both member ports show `P`.

2.3 Verify LACP negotiation details: `show lacp neighbor`.

2.4 Run `show spanning-tree` and observe how STP now sees the Port-Channel as a single logical link.

### Task 3 — PAgP EtherChannel

3.1 On both switches, bundle GE0/2 and GE0/3 into **Port-Channel 2** using PAgP. Use **desirable** mode on SW1 and **auto** mode on SW2.

3.2 Verify the bundle formed using the same show commands as Task 2.

3.3 What would happen if both sides were set to `auto`? Test this and observe the result.

### Task 4 — Load Balancing

4.1 Check the current load-balancing algorithm: `show etherchannel load-balance`.

4.2 Change the algorithm to `src-dst-mac` on SW1. Verify the change.

4.3 Use `test etherchannel load-balance interface port-channel 1 mac <src-mac> <dst-mac>` to predict which physical link a specific flow would use.

### Task 5 — Link Failure & Recovery

5.1 Shut down GE0/0 on SW1. Observe the effect on Port-Channel 1 using `show etherchannel summary`. Does traffic continue to flow?

5.2 Restore GE0/0 and confirm it rejoins the bundle automatically.

5.3 Shut down all four member links simultaneously. Observe the Port-Channel state. What happens to PC1 → PC2 traffic?

---

## Verification Checklist

- [ ] Port-Channel 1 (LACP) is `SU` with 2 member ports in `P` state
- [ ] Port-Channel 2 (PAgP) is `SU` with 2 member ports in `P` state
- [ ] STP shows Port-Channel interfaces, not individual member ports
- [ ] Load-balancing algorithm confirmed or changed
- [ ] Single link failure does not drop PC1 → PC2 connectivity
