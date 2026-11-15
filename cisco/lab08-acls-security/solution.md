# Solution: Lab 08, IPv4 ACLs

> Fill in this workbook as you complete each requirement.

---

## Baseline (before ACLs)

**Results of the five baseline tests:**

```

```

---

## Requirement 1: Bob and FTP

**ACL type, router, interface and direction (and why):**

**Commands:**

```

```

**Tests (Bob FTP to both servers, Bob web to Server1):**

```

```

---

## Requirement 2: Larry and Server1's website

**ACL type, router, interface and direction (and why):**

**Commands:**

```

```

**Tests (Larry web to Server1 and Server2, Larry FTP to Server1):**

```

```

---

## Requirement 3: Jerry and the main site

**ACL type, router, interface and direction (and why):**

**Commands:**

```

```

**Tests (Jerry to the main site, Jerry to Larry):**

```

```

---

## Verification

**`show ip access-lists` on R1, R2 and R3 (match counts):**

```

```

---

## Questions

1. **Which ACL type for each requirement, and why?**

2. **Which router, interface and direction for each, and why there?**

3. **What did the match counts prove?**

4. **What happens to the other hosts without the last line of each ACL?**
