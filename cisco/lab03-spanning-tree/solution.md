# Solution — Lab 03: Spanning Tree Protocol

> Fill in this workbook as you complete each task.

---

## Task 1 — Default STP Observation

**Root Bridge ID (before any changes):**

**Bridge IDs of all switches:**

| Switch | Priority | MAC | Bridge ID |
|--------|----------|-----|-----------|
| SW1 | | | |
| SW2 | | | |
| SW3 | | | |

**Port roles (before changes):**

| Switch | Interface | Role | State |
|--------|-----------|------|-------|
| SW1 | GE0/0 | | |
| SW1 | GE0/1 | | |
| SW1 | GE0/2 | | |
| SW1 | GE0/3 | | |
| SW2 | GE0/0 | | |
| SW2 | GE0/1 | | |
| SW2 | GE0/2 | | |
| SW3 | GE0/0 | | |
| SW3 | GE0/1 | | |
| SW3 | GE0/2 | | |

**Active logical topology drawing (blocked ports marked):**

```

```

**Why this switch became root bridge:**

---

## Task 2 — Root Bridge Manipulation

**Commands on SW1:**

```

```

**Commands on SW2 (secondary):**

```

```

**`show spanning-tree` on SW1 after changes:**

```

```

**Failover observation (SW1 links shut):**

---

## Task 3 — Port Cost Manipulation

**Original Root Port on SW2:**

**Commands to change port cost:**

```

```

**New Root Port after cost change and reason:**

---

## Task 4 — Rapid PVST+

**Commands:**

```

```

**Convergence comparison notes:**

**Port type observations (Edge/P2P/Shared):**

---

## Task 5 — PortFast & BPDU Guard

**Commands:**

```

```

**`show spanning-tree interface <int> detail` excerpt:**

```

```

**BPDU Guard err-disable event observation:**

---

## Lessons Learned

## Troubleshooting Notes
