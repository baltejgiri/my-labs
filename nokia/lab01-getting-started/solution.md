# Lab 01 Getting Started — Solution & Verification Workbook

Complete each section as you work through the lab. Paste exact CLI outputs and answer each question block based on what you observe on the device — not from memory or documentation.

---

## Section 1: CLI Entry, Datastore Orientation & State Assessment

### Question 1.1
What is the default node name displayed in the prompt when you first connect to `sw1` and `sw2`?

* **sw1 default name:**
* **sw2 default name:**

### Question 1.2
What command identifies the active SR Linux software release variant running on the node?

* **Command:**
* **sw1 version output:**

### Question 1.3
SR Linux has three primary datastores. Without making any changes, navigate into each one and describe what you observe.

| Datastore | Command used to enter | What the prompt looks like |
|-----------|----------------------|---------------------------|
| `running` | | |
| `candidate` | | |
| `state` | | |

### Question 1.4
While in the `state` datastore, inspect the operational status of `ethernet-1/1` on both nodes **before any configuration changes**. Paste the relevant output below.

**sw1 — ethernet-1/1 state (pre-config):**
```text
[PASTE OUTPUT HERE]
```

**sw2 — ethernet-1/1 state (pre-config):**
```text
[PASTE OUTPUT HERE]
```

### Question 1.5
Based on the state datastore output above, is `ethernet-1/1` administratively up or down? Is it operationally up or down? What is the distinction between the two?

* **Admin state:**
* **Oper state:**
* **Distinction:**

---

## Section 2: Transactional Identity & State Preparation

### Question 2.1
What command is used to enter the candidate datastore for an isolated configuration session?

* **Command:**

### Question 2.2
Paste the exact prompt string displayed after entering the candidate datastore but before committing any changes. What does the `+` symbol indicate when it appears?

* **Prompt string:**
* **Meaning of `+`:**

### Question 2.3
What commands did you use to set the system hostname on each node?

* **sw1 command:**
* **sw2 command:**

### Question 2.4
What command enables the administrative state of `ethernet-1/1` on both nodes?

* **Command:**

### Question 2.5
What command shows a line-by-line diff of your pending candidate changes before committing?

* **Command:**
* **Paste diff output (sw1):**
```text
[PASTE OUTPUT HERE]
```

---

## Section 3: Layer 3 Interface & Protocol Bindings

### Question 3.1
What commands were used to enable IPv4 and IPv6 address families within subinterface `0` of `ethernet-1/1`?

* **IPv4 enable command:**
* **IPv6 enable command:**

### Question 3.2
What commands assigned the IP addresses to subinterface `0` on each node?

* **sw1 IPv4 command:**
* **sw1 IPv6 command:**
* **sw2 IPv4 command:**
* **sw2 IPv6 command:**

### Question 3.3
What command bound subinterface `ethernet-1/1.0` into the default network-instance on both nodes?

* **Command:**

### Question 3.4
Why is binding the subinterface into a network-instance required before the interface can participate in routing?

* **Answer:**

---

## Section 4: Transactional Validation & Commit

### Question 4.1
What command performs a structural validation of your candidate changes without committing them?

* **Command:**
* **Validation output:**
```text
[PASTE OUTPUT HERE]
```

### Question 4.2
What command commits the candidate changes to the running datastore?

* **Command:**

### Question 4.3
After committing, re-enter the `state` datastore and verify that `ethernet-1/1` is now operationally up on both nodes. Paste the updated output below and compare it to your pre-config baseline from Section 1.

**sw1 — ethernet-1/1 state (post-commit):**
```text
[PASTE OUTPUT HERE]
```

**sw2 — ethernet-1/1 state (post-commit):**
```text
[PASTE OUTPUT HERE]
```

### Question 4.4
Verify the IPv4 and IPv6 addresses are visible in the routing table of the default network-instance on both nodes.

**sw1 — route table output:**
```text
[PASTE OUTPUT HERE]
```

**sw2 — route table output:**
```text
[PASTE OUTPUT HERE]
```

### Question 4.5
Confirm bidirectional reachability. Paste the ping results for both IPv4 and IPv6 from `sw1` to `sw2`.

**IPv4 ping (sw1 → sw2):**
```text
[PASTE OUTPUT HERE]
```

**IPv6 ping (sw1 → sw2):**
```text
[PASTE OUTPUT HERE]
```

---

## RCA Notes

Use this section to document any misconfigurations encountered during the lab, the symptoms observed, and the fix applied.

| Step | Issue observed | Root cause | Fix applied |
|------|---------------|------------|-------------|
| | | | |
| | | | |