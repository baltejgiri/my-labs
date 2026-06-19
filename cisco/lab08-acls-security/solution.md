# Solution — Lab 08: ACLs & Device Security

> Fill in this workbook as you complete each task.

---

## Task 1 — Baseline Connectivity

**PC1 → Server1 ping (before ACLs):**

```

```

---

## Task 2 — Standard ACL

**Commands on R1:**

```

```

**Telnet from PC1 (result):**

**Telnet from PC2 (result):**

**`show access-lists 10` match counts:**

```

```

---

## Task 3 — Extended ACL 110

**Commands on R1:**

```

```

**Test results:**

| Test | Expected | Actual |
|------|----------|--------|
| PC1 → Server1 ICMP | Permit | |
| PC1 → Server1 HTTP (80) | Permit | |
| PC1 → Server1 SSH (22) | Deny | |

**`show access-lists 110`:**

```

```

---

## Task 4 — Named ACL

**Commands (named ACL creation):**

```

```

**Adding HTTPS entry with sequence number:**

```

```

**`show ip access-lists INSIDE-TO-SERVER`:**

```

```

---

## Task 5 — Device Hardening & SSH

**Commands on R1:**

```

```

**`show ip ssh`:**

```

```

**SSH test from PC1 — result:**

**Telnet test — result (should be refused):**

---

## Lessons Learned

## Troubleshooting Notes
