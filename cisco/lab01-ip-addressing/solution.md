# Solution — Lab 01: IP Addressing & Network Fundamentals

> Fill in this workbook as you complete each task. Include exact commands and relevant output.

---

## Pre-Lab: VLSM Plan

_Record your subnet calculations here before touching any device._

| Segment | Network | Prefix | Usable Range | Broadcast |
|---------|---------|--------|--------------|-----------|
| R1 — R2 | | | | |
| R2 — R3 | | | | |
| LAN (SW1) | | | | |
| R1 Lo0 | | | | |
| R2 Lo0 | | | | |
| R3 Lo0 | | | | |

**Interface assignment table:**

| Device | Interface | IP Address | Mask |
|--------|-----------|------------|------|
| R1 | GE0/0 | | |
| R1 | GE0/1 | | |
| R1 | Lo0 | | |
| R2 | GE0/0 | | |
| R2 | GE0/1 | | |
| R2 | Lo0 | | |
| R3 | GE0/0 | | |
| R3 | Lo0 | | |
| PC1 | eth0 | | |
| PC2 | eth0 | | |

---

## Task 1 — VLSM Planning

_Notes on subnetting decisions:_

---

## Task 2 — Interface Configuration

**Commands used on R1:**

```

```

**Commands used on R2:**

```

```

**Commands used on R3:**

```

```

**`show ip interface brief` output (R1):**

```

```

---

## Task 3 — Connectivity Verification

**Ping R2 from R1:**

```

```

**Ping PC2 from PC1 — result and explanation:**

```

```

**Traceroute R1 → R3 Lo0:**

```

```

---

## Task 4 — CDP & LLDP

**`show cdp neighbors` on R1:**

```

```

**`show cdp neighbors detail` on R2:**

```

```

**`show lldp neighbors` vs CDP comparison notes:**

---

## Task 5 — ARP & MAC Analysis

**`show ip arp` on R1 after ping:**

```

```

**`show mac address-table` on SW1:**

```

```

**Observations after clearing ARP cache:**

---

## Lessons Learned

_What was new, surprising, or reinforced?_

## Troubleshooting Notes

_Any issues encountered and how they were resolved._
