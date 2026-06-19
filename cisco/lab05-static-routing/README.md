# Lab 05 — Static Routing & Default Routes

**CCNA Domain:** 3.0 IP Connectivity  
**CML Topology:** `topology.cml.yml`  
**Estimated Time:** 60–75 minutes

---

## Objectives

1. Configure IPv4 static routes to reach remote networks
2. Configure a default route and verify it is used for unknown destinations
3. Configure floating static routes as backup paths
4. Verify routing tables and trace packet paths
5. Understand administrative distance and its effect on route selection

---

## Topology

```
[PC1]---[R1]---[R2]---[R3]---[PC2]
                |
             [R4-ISP]
             (8.8.8.8)
```

---

## Pre-Configured Addressing

Interface IP addresses are already configured. Do **not** modify them.

| Device | Interface | IP Address | Description |
|--------|-----------|------------|-------------|
| R1 | GE0/0 | 10.0.12.1/30 | Link to R2 |
| R1 | GE0/1 | 192.168.1.1/24 | LAN1 (PC1) |
| R1 | Lo0 | 1.1.1.1/32 | Loopback |
| R2 | GE0/0 | 10.0.12.2/30 | Link to R1 |
| R2 | GE0/1 | 10.0.23.1/30 | Link to R3 |
| R2 | GE0/2 | 203.0.113.1/30 | Link to ISP |
| R2 | Lo0 | 2.2.2.2/32 | Loopback |
| R3 | GE0/0 | 10.0.23.2/30 | Link to R2 |
| R3 | GE0/1 | 192.168.3.1/24 | LAN3 (PC2) |
| R3 | Lo0 | 3.3.3.3/32 | Loopback |
| R4-ISP | GE0/0 | 203.0.113.2/30 | Link to R2 |
| R4-ISP | Lo0 | 8.8.8.8/32 | Simulated internet host |
| PC1 | eth0 | 192.168.1.10/24 | GW: 192.168.1.1 |
| PC2 | eth0 | 192.168.3.10/24 | GW: 192.168.3.1 |

---

## Tasks

### Task 1 — Examine the Starting State

1.1 Run `show ip route` on R1, R2, and R3. Record which networks each router knows about and which it does not.

1.2 From R1, attempt to ping 192.168.3.1 (R3's LAN interface). Explain the result.

### Task 2 — Static Route Configuration

2.1 On R1, configure a static route so it can reach the 192.168.3.0/24 network via R2.

2.2 On R3, configure a static route so it can reach the 192.168.1.0/24 network via R2.

2.3 On R2, configure static routes to reach both 192.168.1.0/24 (via R1) and 192.168.3.0/24 (via R3).

2.4 Verify `show ip route` on all three routers shows the new static entries (`S`).

2.5 Verify PC1 can ping PC2.

### Task 3 — Loopback Reachability

3.1 Ensure all three loopbacks (1.1.1.1, 2.2.2.2, 3.3.3.3) are reachable from every router. Add any missing static routes.

3.2 From R1, run `traceroute 3.3.3.3` and verify the path goes R1 → R2 → R3.

### Task 4 — Default Route

4.1 On R2, configure a default route pointing to R4-ISP (203.0.113.2).

4.2 On R1, configure a default route pointing to R2 (10.0.12.2).

4.3 On R3, configure a default route pointing to R2 (10.0.23.1).

4.4 From PC1, ping 8.8.8.8 (R4-ISP loopback). Verify it succeeds and trace the full path.

4.5 Run `show ip route` on R1. Identify how the default route appears in the table.

### Task 5 — Floating Static Routes

5.1 Assume R2's link to R3 (GE0/1) is the primary path. Configure a floating static route on R2 to reach 192.168.3.0/24 via R4-ISP as a backup (administrative distance of 5).

5.2 Verify the floating route does **not** appear in the routing table while the primary route is active.

5.3 Shut down R2's GE0/1 interface to simulate a failure. Verify the floating route now appears and that R2 can still reach 192.168.3.0/24 (assuming R4 has a path back).

5.4 Restore GE0/1 and confirm the primary route returns and the floating route disappears.

---

## Verification Checklist

- [ ] `show ip route` on R1 shows static routes to all remote networks
- [ ] PC1 can ping PC2
- [ ] All loopbacks reachable from all routers
- [ ] Default route in place on R1, R2, R3 — PC1 can reach 8.8.8.8
- [ ] Floating static route only appears when primary path is down
