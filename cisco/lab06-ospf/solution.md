# Solution — Lab 06: OSPFv2 Single-Area & Multi-Area

> Fill in this workbook as you complete each task.

---

## Task 1 — Single-Area OSPF

**Commands on R1-ABR:**

```

```

**Commands on R2:**

```

```

**Commands on R3-ABR:**

```

```

**`show ip ospf neighbor` on R1-ABR:**

```

```

**`show ip ospf database` (Area 0 — key entries):**

```

```

**DR/BDR observations (if applicable):**

---

## Task 2 — Extend to Multi-Area

**Commands on R4 (Area 1):**

```

```

**Commands on R5 (Area 2):**

```

```

**Updated R1-ABR config (Area 1 interface):**

```

```

**Updated R3-ABR config (Area 2 interface):**

```

```

**`show ip ospf neighbor` on all routers (final state):**

```

```

---

## Task 3 — Verify Multi-Area Routing

**`show ip route ospf` on R4:**

```

```

**Inter-area (`O IA`) routes observed on R4:**

**Type 3 Summary LSAs on R1-ABR — source and reason:**

**R4 → R5 ping and traceroute:**

```

```

---

## Task 4 — Passive Interfaces

**Commands:**

```

```

**Verification that loopback is still advertised but no adjacency forms:**

---

## Task 5 — OSPF Cost Manipulation

**Current preferred path R1-ABR → R3-ABR:**

**Commands to increase cost on direct link:**

```

```

**Routing table after cost change:**

```

```

---

## Task 6 — Default Route Redistribution

**Commands on R2:**

```

```

**`show ip route` on R4 showing O*E2 entry:**

```

```

---

## Lessons Learned

## Troubleshooting Notes
