# Solution — Lab 07: DHCP & NAT

> Fill in this workbook as you complete each task.

---

## Task 1 — R1 Inside Interfaces

**Commands:**

```

```

**`show ip interface brief` on R1:**

```

```

---

## Task 2 — DHCP Server

**Commands on R1:**

```

```

**PC1 DHCP request output:**

```

```

**`show ip dhcp binding` on R1:**

```

```

**`show ip dhcp pool LAN1`:**

```

```

---

## Task 3 — DHCP Relay

**Commands on R1 (pool + helper-address):**

```

```

**PC3 DHCP address received:**

**`show ip dhcp binding` after relay (3 leases):**

```

```

---

## Task 4 — Static NAT

**Commands:**

```

```

**`show ip nat translations` (static entry):**

```

```

**Ping from R2-ISP to static NAT IP — result:**

---

## Task 5 — PAT

**ACL and NAT overload commands:**

```

```

**PC1 → 8.8.8.8 ping result:**

```

```

**`show ip nat translations` (PAT entries):**

```

```

**`show ip nat statistics`:**

```

```

---

## Lessons Learned

## Troubleshooting Notes
