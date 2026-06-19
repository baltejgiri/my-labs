# Solution — Lab 10: IP Services — NTP, SNMP, Syslog & HSRP

> Fill in this workbook as you complete each task.

---

## Task 1 — PC Static IPs

**PC1 and PC2 configuration commands:**

```

```

---

## Task 2 — NTP

**Commands on R3 (NTP master):**

```

```

**Commands on R1 and R2 (NTP clients):**

```

```

**`show ntp status` on R1:**

```

```

**`show ntp associations` on R2:**

```

```

**`show clock` on R1 and R2:**

---

## Task 3 — SNMP

**SNMPv2c commands on R1:**

```

```

**SNMPv3 commands on R1:**

```

```

**`show snmp` output:**

```

```

**`show snmp user` output:**

```

```

---

## Task 4 — Syslog

**Commands on R1 and R2:**

```

```

**`show logging` after generating an event:**

```

```

---

## Task 5 — HSRP

**Commands on R1 GE0/0:**

```

```

**Commands on R2 GE0/0:**

```

```

**`show standby brief` on R1 (before failover):**

```

```

**`show standby brief` on R2 (after R1 GE0/0 shut):**

```

```

**PC1 → 3.3.3.3 ping during failover:**

```

```

**R1 preemption observation after restoring GE0/0:**

---

## Task 6 — HSRP Interface Tracking

**Commands on R1:**

```

```

**`show standby` on R1 showing track object:**

```

```

**Failover test (GE0/1 shut) — R2 becomes Active:**

---

## Lessons Learned

## Troubleshooting Notes
