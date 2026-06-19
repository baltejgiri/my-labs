# Lab 06 — OSPFv2: Single-Area & Multi-Area

**CCNA Domain:** 3.0 IP Connectivity  
**CML Topology:** `topology.cml.yml`  
**Estimated Time:** 90–120 minutes

---

## Objectives

1. Configure OSPFv2 in a single area (Area 0)
2. Verify OSPF neighbour adjacencies and the link-state database
3. Extend the topology to multi-area OSPF (Area 0, Area 1, Area 2)
4. Identify ABR routers and the LSA types present in each area
5. Configure passive interfaces to prevent OSPF hellos on LAN-facing interfaces
6. Manipulate OSPF metric (cost) to influence path selection
7. Redistribute a default route into OSPF

---

## Topology

```
         Area 1          Area 0                Area 2
  [R4] ─────────── [R1-ABR] ─── [R2] ─── [R3-ABR] ─────────── [R5]
  4.4.4.4            1.1.1.1     2.2.2.2    3.3.3.3              5.5.5.5
                        \                  /
                         └─── [R3-ABR] ──┘
                              (also direct R1-R3 link)
```

**OSPF Areas:**
- **Area 0** (Backbone): R1-ABR, R2, R3-ABR — links 10.0.12.x, 10.0.13.x, 10.0.23.x
- **Area 1**: R1-ABR, R4 — link 10.0.14.x
- **Area 2**: R3-ABR, R5 — link 10.0.35.x

---

## Pre-Configured Addressing

Interface IPs are already configured. Do **not** modify them.

| Device | Interface | IP Address | OSPF Area |
|--------|-----------|------------|-----------|
| R1-ABR | GE0/0 | 10.0.12.1/30 | Area 0 |
| R1-ABR | GE0/1 | 10.0.13.1/30 | Area 0 |
| R1-ABR | GE0/2 | 10.0.14.1/30 | Area 1 |
| R1-ABR | Lo0 | 1.1.1.1/32 | Area 0 |
| R2 | GE0/0 | 10.0.12.2/30 | Area 0 |
| R2 | GE0/1 | 10.0.23.1/30 | Area 0 |
| R2 | Lo0 | 2.2.2.2/32 | Area 0 |
| R3-ABR | GE0/0 | 10.0.13.2/30 | Area 0 |
| R3-ABR | GE0/1 | 10.0.23.2/30 | Area 0 |
| R3-ABR | GE0/2 | 10.0.35.1/30 | Area 2 |
| R3-ABR | Lo0 | 3.3.3.3/32 | Area 0 |
| R4 | GE0/0 | 10.0.14.2/30 | Area 1 |
| R4 | Lo0 | 4.4.4.4/32 | Area 1 |
| R5 | GE0/0 | 10.0.35.2/30 | Area 2 |
| R5 | Lo0 | 5.5.5.5/32 | Area 2 |

---

## Tasks

### Task 1 — Single-Area OSPF (Area 0 Only)

1.1 Configure OSPF process 1 on R1-ABR, R2, and R3-ABR. Place all interfaces (including loopbacks) into **Area 0** only. Do not configure R4 or R5 yet.

1.2 Verify neighbour adjacencies: `show ip ospf neighbor`. Confirm all expected adjacencies are in FULL state.

1.3 Examine the OSPF database: `show ip ospf database`. Identify the Router LSAs (Type 1) and Network LSAs (Type 2). Record which router generated each LSA.

1.4 Verify routing tables on R1-ABR, R2, and R3-ABR. Confirm all loopbacks and links are present.

1.5 Identify the DR and BDR on any multi-access segment if one exists.

### Task 2 — Extend to Multi-Area OSPF

2.1 Configure R4 with OSPF process 1. Place its GE0/0 interface into **Area 1** and its loopback into **Area 1**.

2.2 On R1-ABR, move its GE0/2 interface (link to R4) into **Area 1**. Keep its other interfaces in Area 0.

2.3 Configure R5 with OSPF process 1. Place its GE0/0 into **Area 2** and loopback into **Area 2**.

2.4 On R3-ABR, move its GE0/2 (link to R5) into **Area 2**. Keep its other interfaces in Area 0.

2.5 Verify `show ip ospf neighbor` on all routers shows the expected adjacencies.

### Task 3 — Verify Multi-Area Routing

3.1 On R4, run `show ip route ospf`. Identify which routes are inter-area (`O IA`) vs intra-area (`O`).

3.2 On R1-ABR, run `show ip ospf database`. Identify the Summary LSAs (Type 3). Which router generated them and why?

3.3 Ping from R4's loopback (4.4.4.4) to R5's loopback (5.5.5.5). Trace the path.

### Task 4 — Passive Interfaces

4.1 On R2, configure all loopback interfaces as OSPF passive (no OSPF hellos transmitted on loopbacks).

4.2 Verify the passive interface is still advertised in OSPF but no adjacency can form on it.

### Task 5 — OSPF Cost Manipulation

5.1 On R1-ABR, there are two paths to R3-ABR: direct (GE0/1) and via R2. Determine which path OSPF currently prefers by examining the routing table.

5.2 Increase the OSPF cost on R1-ABR's direct link to R3-ABR (GE0/1) so that traffic prefers the path via R2.

5.3 Verify the routing table on R1-ABR reflects the new preferred path.

5.4 Restore the original cost.

### Task 6 — Default Route Redistribution

6.1 On R2, configure a static default route to a simulated ISP (use 0.0.0.0/0 via a null interface or any available next-hop).

6.2 Redistribute the default route into OSPF using `default-information originate`.

6.3 Verify all other routers receive the default route (`O*E2`) in their routing tables.

---

## Verification Checklist

- [ ] All OSPF adjacencies in FULL state
- [ ] R4 has `O IA` routes to Area 0 and Area 2 prefixes
- [ ] R5 has `O IA` routes to Area 0 and Area 1 prefixes
- [ ] R4 loopback reachable from R5 (ping successful)
- [ ] Loopbacks configured as passive where appropriate
- [ ] All routers have `O*E2 0.0.0.0/0` in routing table
