# Lab 01 Getting Started — Solution & Verification Workbook

Complete each section as you work through the lab. Paste exact CLI outputs and answer each question block based on what you observe on the device — not from memory or documentation.

---

## Section 1: CLI Entry, Datastore Orientation & State Assessment

### Question 1.1
What is the default node name displayed in the prompt when you first connect to `sw1` and `sw2`?

* **sw1 default name:** sw1
* **sw2 default name:** sw2

### Question 1.2
What command identifies the active SR Linux software release variant running on the node?

* **Command:** ```show version```
* **sw1 version output:**

```bash
A:sw1# show version
-----------------------------------------------------------------------------------------------------------------------------------
Hostname             : sw1
Chassis Type         : 7220 IXR-D2L
Part Number          : Sim Part No.
Serial Number        : Sim Serial No.
System HW MAC Address: 1A:6E:00:FF:00:00
OS                   : SR Linux
Software Version     : v24.10.5
Build Number         : 344-g1c1a8d80ff4
Architecture         : x86_64
Last Booted          : 2026-05-23T19:23:42.753Z
Total Memory         : 32095800 kB
Free Memory          : 26685679 kB
-----------------------------------------------------------------------------------------------------------------------------------
--{ running }--[  ]--
```

### Question 1.3
SR Linux has three primary datastores. Without making any changes, navigate into each one and describe what you observe.

| Datastore | Command used to enter | What the prompt looks like |
|-----------|----------------------|---------------------------|
| `running` | enter running | --{ + running }--[  ]--
|           |               | A:sw1#|
| `candidate` | enter candidate  | sw1# enter candidate
|           |               |   --{ + candidate shared default }--[  ]--
| `state`   | enter state   | A:sw1# enter state
|           |               | --{ + state }--[  ]--|

### Question 1.4
While in the `state` datastore, inspect the operational status of `ethernet-1/1` on both nodes **before any configuration changes**. Paste the relevant output below.

**sw1 — ethernet-1/1 state (pre-config):**
```bash
A:sw1# show interface ethernet-1/1
===================================================================================================================================
ethernet-1/1 is up, speed 25G, type None
-----------------------------------------------------------------------------------------------------------------------------------
===================================================================================================================================

--{ state }--[  ]--
```

**sw2 — ethernet-1/1 state (pre-config):**
```bash
A:sw2# show interface ethernet-1/1
===================================================================================================================================
ethernet-1/1 is up, speed 25G, type None
-----------------------------------------------------------------------------------------------------------------------------------
===================================================================================================================================

--{ state }--[  ]--
```

### Question 1.5
Based on the state datastore output above, is `ethernet-1/1` administratively up or down? Is it operationally up or down? What is the distinction between the two?

* **Admin state:** up
* **Oper state:** N/A
* **Distinction:** Admin state is what is defined manually it's either disabled or enabled however Oper state is what data plant observes, data plane determines the hardware link detection and other vaiables like protocol or peer state on the link.

## Section 2: Transactional Identity & State Preparation

### Question 2.1
What command is used to enter the candidate datastore for an isolated configuration session?

* **Command:** enter candidate private

### Question 2.2
Paste the exact prompt string displayed after entering the candidate datastore but before committing any changes. What does the `+` symbol indicate when it appears?

* **Prompt string:** 
```bash
A:sw1# enter candidate private

--{ + candidate private private-admin }--[  ]--
```
* **Meaning of `+`:** Symbol + means there are uncommited changes in the candiadate tha differ from running datastore.

### Question 2.3
What commands did you use to set the system hostname on each node?

* **sw1 command:** set / system name host-name SROS-LAB-SW1
* **sw2 command:** set / system name host-name SROS-LAB-SW2

### Question 2.4
What command enables the administrative state of `ethernet-1/1` on both nodes?

* **Command:** set interface ethernet-1/1 admin-state enable

### Question 2.5
What command shows a line-by-line diff of your pending candidate changes before committing?

* **Command:** diff
* **Paste diff output (sw1):**
```text
A:sw1# diff
      system {
          name {
+             host-name SROS-LAB-SW1
          }
      }

--{ * candidate private private-admin }--[  ]--
```

## Section 3: Layer 3 Interface & Protocol Bindings

### Question 3.1
What commands were used to enable IPv4 and IPv6 address families within subinterface `0` of `ethernet-1/1`?

* **IPv4 enable command:** ```set interface ethernet-1/1 subinterface 0 ipv4 admin-state enable```
* **IPv6 enable command:** ```set interface ethernet-1/1 subinterface 0 ipv6 admin-state enable```

### Question 3.2
What commands assigned the IP addresses to subinterface `0` on each node?

* **sw1 IPv4 command:** ```set interface ethernet-1/1 subinterface 0 ipv4 address 10.10.12.1/24```
* **sw1 IPv6 command:** ```set interface ethernet-1/1 subinterface 0 ipv6 address 2001:db8:12::1/64```
* **sw2 IPv4 command:** ```set interface ethernet-1/1 subinterface 0 ipv4 address 10.10.12.2/24```
* **sw2 IPv6 command:** ```set interface ethernet-1/1 subinterface 0 ipv6 address 2001:db8:12::2/64```

### Question 3.3
What command bound subinterface `ethernet-1/1.0` into the default network-instance on both nodes?

* **Command:** ```set /network-instance default interface ethernet-1/1.0```

### Question 3.4
Why is binding the subinterface into a network-instance required before the interface can participate in routing?

* **Answer:** SR Linux keeps each interface in a seperate VRF as completely isolated virtual router. Thus I used the **default** network instance to bind the sub-interaface ```ethernet-1/1.0```.

## Section 4: Transactional Validation & Commit

### Question 4.1
What command performs a structural validation of your candidate changes without committing them?

* **Command:** commit validate
* **Validation output:** I used diff command while in the ```private candidate``` datastore.
```text
A:sw1# commit validate
All changes are valid.

--{ +* candidate private private-admin }--[  ]--
```

```text
A:sw1# commit validate
All changes are valid.

--{ +* candidate private private-admin }--[  ]--
```

### Question 4.2
What command commits the candidate changes to the running datastore?

* **Command:**commit stay

```text
A:sw1# commit stay
All changes have been committed. Starting new transaction.

--{ + candidate private private-admin }--[  ]--
A:sw1#

--{ + candidate private private-admin }--[  ]--
A:SROS-LAB-SW1#
```
```text
A:sw2# commit stay
All changes have been committed. Starting new transaction.

--{ + candidate private private-admin }--[  ]--
A:sw2#

--{ + candidate private private-admin }--[  ]--
A:SROS-LAB-SW2#
```
### Question 4.3
After committing, re-enter the `state` datastore and verify that `ethernet-1/1` is now operationally up on both nodes. Paste the updated output below and compare it to your pre-config baseline from Section 1.

**sw1 — ethernet-1/1 state (post-commit):**
```text
A:SROS-LAB-SW1# show interface ethernet-1/1
===================================================================================================================================
ethernet-1/1 is up, speed 25G, type None
  ethernet-1/1.0 is up
    Network-instances:
      * Name: defaul (default)
    Encapsulation   : null
    Type            : routed
    IPv4 addr    : 10.10.12.1/24 (static, preferred, primary)
    IPv6 addr    : 2001:db8:12::1/64 (static, preferred, primary)
    IPv6 addr    : fe80::186e:ff:feff:1/64 (link-layer, preferred)
-----------------------------------------------------------------------------------------------------------------------------------
===================================================================================================================================

--{ + state }--[  ]--
```

**sw2 — ethernet-1/1 state (post-commit):**
```text
A:SROS-LAB-SW2# show interface ethernet-1/1
===================================================================================================================================
ethernet-1/1 is up, speed 25G, type None
  ethernet-1/1.0 is up
    Network-instances:
      * Name: default (default)
    Encapsulation   : null
    Type            : routed
    IPv4 addr    : 10.10.12.2/24 (static, preferred, primary)
    IPv6 addr    : 200:db8:12::2/64 (static, preferred, primary)
    IPv6 addr    : fe80::18de:1ff:feff:1/64 (link-layer, preferred)
-----------------------------------------------------------------------------------------------------------------------------------
===================================================================================================================================

--{ + state }--[  ]--
```

### Question 4.4
Verify the IPv4 and IPv6 addresses are visible in the routing table of the default network-instance on both nodes.

**sw1 — route table output:**
```text
A:SROS-LAB-SW1# show network-instance defaul route-table
----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
IPv4 unicast route table of network instance defaul
----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
+---------------------------------------+-------+------------+----------------------+----------+----------+---------+------------+------------------------+------------------------+------------------------+--------------------------------------------------+
|                Prefix                 |  ID   | Route Type |     Route Owner      |  Active  |  Origin  | Metric  |    Pref    |    Next-hop (Type)     |   Next-hop Interface   | Backup Next-hop (Type) |            Backup Next-hop Interface             |
|                                       |       |            |                      |          | Network  |         |            |                        |                        |                        |                                                  |
|                                       |       |            |                      |          | Instance |         |            |                        |                        |                        |                                                  |
+=======================================+=======+============+======================+==========+==========+=========+============+========================+========================+========================+==================================================+
| 10.10.12.0/24                         | 2     | local      | net_inst_mgr         | True     | defaul   | 0       | 0          | 10.10.12.1 (direct)    | ethernet-1/1.0         |                        |                                                  |
| 10.10.12.1/32                         | 2     | host       | net_inst_mgr         | True     | defaul   | 0       | 0          | None (extract)         | None                   |                        |                                                  |
| 10.10.12.255/32                       | 2     | host       | net_inst_mgr         | True     | defaul   | 0       | 0          | None (broadcast)       |                        |                        |                                                  |
+---------------------------------------+-------+------------+----------------------+----------+----------+---------+------------+------------------------+------------------------+------------------------+--------------------------------------------------+
----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
IPv4 routes total                    : 3
IPv4 prefixes with active routes     : 3
IPv4 prefixes with active ECMP routes: 0
----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
IPv6 unicast route table of network instance defaul
----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
+---------------------------------------+-------+------------+----------------------+----------+----------+---------+------------+------------------------+------------------------+------------------------+--------------------------------------------------+
|                Prefix                 |  ID   | Route Type |     Route Owner      |  Active  |  Origin  | Metric  |    Pref    |    Next-hop (Type)     |   Next-hop Interface   | Backup Next-hop (Type) |            Backup Next-hop Interface             |
|                                       |       |            |                      |          | Network  |         |            |                        |                        |                        |                                                  |
|                                       |       |            |                      |          | Instance |         |            |                        |                        |                        |                                                  |
+=======================================+=======+============+======================+==========+==========+=========+============+========================+========================+========================+==================================================+
| 2001:db8:12::/64                      | 2     | local      | net_inst_mgr         | True     | defaul   | 0       | 0          | 2001:db8:12::1         | ethernet-1/1.0         |                        |                                                  |
|                                       |       |            |                      |          |          |         |            | (direct)               |                        |                        |                                                  |
| 2001:db8:12::1/128                    | 2     | host       | net_inst_mgr         | True     | defaul   | 0       | 0          | None (extract)         | None                   |                        |                                                  |
+---------------------------------------+-------+------------+----------------------+----------+----------+---------+------------+------------------------+------------------------+------------------------+--------------------------------------------------+
----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
IPv6 routes total                    : 2
IPv6 prefixes with active routes     : 2
IPv6 prefixes with active ECMP routes: 0
----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

--{ + state }--[  ]--
```

**sw2 — route table output:**
```text
A:SROS-LAB-SW2# show network-instance default route-table
----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
IPv4 unicast route table of network instance default
----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
+---------------------------------------+-------+------------+----------------------+----------+----------+---------+------------+------------------------+------------------------+------------------------+--------------------------------------------------+
|                Prefix                 |  ID   | Route Type |     Route Owner      |  Active  |  Origin  | Metric  |    Pref    |    Next-hop (Type)     |   Next-hop Interface   | Backup Next-hop (Type) |            Backup Next-hop Interface             |
|                                       |       |            |                      |          | Network  |         |            |                        |                        |                        |                                                  |
|                                       |       |            |                      |          | Instance |         |            |                        |                        |                        |                                                  |
+=======================================+=======+============+======================+==========+==========+=========+============+========================+========================+========================+==================================================+
| 10.10.12.0/24                         | 2     | local      | net_inst_mgr         | True     | default  | 0       | 0          | 10.10.12.2 (direct)    | ethernet-1/1.0         |                        |                                                  |
| 10.10.12.2/32                         | 2     | host       | net_inst_mgr         | True     | default  | 0       | 0          | None (extract)         | None                   |                        |                                                  |
| 10.10.12.255/32                       | 2     | host       | net_inst_mgr         | True     | default  | 0       | 0          | None (broadcast)       |                        |                        |                                                  |
+---------------------------------------+-------+------------+----------------------+----------+----------+---------+------------+------------------------+------------------------+------------------------+--------------------------------------------------+
----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
IPv4 routes total                    : 3
IPv4 prefixes with active routes     : 3
IPv4 prefixes with active ECMP routes: 0
----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
IPv6 unicast route table of network instance default
----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
+---------------------------------------+-------+------------+----------------------+----------+----------+---------+------------+------------------------+------------------------+------------------------+--------------------------------------------------+
|                Prefix                 |  ID   | Route Type |     Route Owner      |  Active  |  Origin  | Metric  |    Pref    |    Next-hop (Type)     |   Next-hop Interface   | Backup Next-hop (Type) |            Backup Next-hop Interface             |
|                                       |       |            |                      |          | Network  |         |            |                        |                        |                        |                                                  |
|                                       |       |            |                      |          | Instance |         |            |                        |                        |                        |                                                  |
+=======================================+=======+============+======================+==========+==========+=========+============+========================+========================+========================+==================================================+
| 2001:db8:12::/64                      | 2     | local      | net_inst_mgr         | True     | default  | 0       | 0          | 2001:db8:12::2         | ethernet-1/1.0         |                        |                                                  |
|                                       |       |            |                      |          |          |         |            | (direct)               |                        |                        |                                                  |
| 2001:db8:12::2/128                    | 2     | host       | net_inst_mgr         | True     | default  | 0       | 0          | None (extract)         | None                   |                        |                                                  |
+---------------------------------------+-------+------------+----------------------+----------+----------+---------+------------+------------------------+------------------------+------------------------+--------------------------------------------------+
----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
IPv6 routes total                    : 2
IPv6 prefixes with active routes     : 2
IPv6 prefixes with active ECMP routes: 0
----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

--{ + state }--[  ]--
```

### Question 4.5
Confirm bidirectional reachability. Paste the ping results for both IPv4 and IPv6 from `sw1` to `sw2`.

**IPv4 ping (sw1 → sw2):**
```text
A:SROS-LAB-SW1# ping network-instance defaul 10.10.12.2 -c 5
Using network instance defaul
PING 10.10.12.2 (10.10.12.2) 56(84) bytes of data.
64 bytes from 10.10.12.2: icmp_seq=1 ttl=64 time=3.26 ms
64 bytes from 10.10.12.2: icmp_seq=2 ttl=64 time=4.44 ms
64 bytes from 10.10.12.2: icmp_seq=3 ttl=64 time=4.80 ms
64 bytes from 10.10.12.2: icmp_seq=4 ttl=64 time=4.42 ms
64 bytes from 10.10.12.2: icmp_seq=5 ttl=64 time=4.22 ms

--- 10.10.12.2 ping statistics ---
5 packets transmitted, 5 received, 0% packet loss, time 4005ms
rtt min/avg/max/mdev = 3.260/4.229/4.802/0.519 ms

--{ + state }--[  ]--
```

**IPv6 ping (sw1 → sw2):**
```text
A:SROS-LAB-SW1# ping network-instance defaul 2001:db8:12::2 -c 5
Using network instance defaul
PING 2001:db8:12::2(2001:db8:12::2) 56 data bytes
64 bytes from 2001:db8:12::2: icmp_seq=1 ttl=64 time=14.3 ms
64 bytes from 2001:db8:12::2: icmp_seq=2 ttl=64 time=3.16 ms
64 bytes from 2001:db8:12::2: icmp_seq=3 ttl=64 time=3.99 ms
64 bytes from 2001:db8:12::2: icmp_seq=4 ttl=64 time=4.18 ms
64 bytes from 2001:db8:12::2: icmp_seq=5 ttl=64 time=4.89 ms

--- 2001:db8:12::2 ping statistics ---
5 packets transmitted, 5 received, 0% packet loss, time 4004ms
rtt min/avg/max/mdev = 3.160/6.100/14.282/4.127 ms

--{ + state }--[  ]--
```

---

## RCA Notes

Use this section to document any misconfigurations encountered during the lab, the symptoms observed, and the fix applied.

| Step      | Issue observed                      | Root cause | Fix applied |
|------|---------------|------------|-------------|
| 1         | Subinterface 0 on Sw2 showing down  | Subinterface was not enabled | admin-state enable was applied on sw2's sub-interface to fix the issue. |
| 2         | Incorrect IPv6 address on Sw2       | A typo in entering the IPv6 address on Sw2's subinterface | deleted the incorrect ip address and added the correct |

---

## Grader Feedback — Round 2

**Grade: B+ — 87/100** *(revised from B / 78)*

Three of the five original issues were fixed. Two remain open. See status below.

---

### Issue 1 — Q1.5 Admin/Oper state distinction: FIXED (with one item still wrong)

The distinction explanation is now correct — well done. However, **Oper state is still listed as "N/A"**. The pre-config output in Q1.4 clearly reads `ethernet-1/1 is up` — that line reflects oper state. The correct answer is `up`, not N/A.

---

### Issue 2 — Q2.2 `+` symbol explanation: FIXED

Correct. (Minor typos — "uncommited", "candiadate", "tha" — worth cleaning up for readability.)

---

### Issue 3 — Q4.1 Validation command: FIXED

`commit validate` is correct and the output `All changes are valid.` confirms it ran. Two small cleanup items remain:

- The description text still reads *"I used diff command while in the private candidate datastore"* — this should be removed or replaced to match the updated command.
- Both validation outputs show `A:sw1#` — the second block should show `A:sw2#`. Looks like a copy-paste that wasn't updated.

---

### Issue 4 — RCA gap: `defaul` typo on sw1 — STILL OPEN

The `defaul` misconfiguration is visible in Q4.3, Q4.4, and Q4.5 outputs on sw1 and has not been added to the RCA table. The outputs themselves cannot be changed (they reflect what actually ran), but the RCA must document it. Add a row:

| Step | Issue observed | Root cause | Fix applied |
|------|----------------|------------|-------------|
| 3 | sw1 bound to network-instance `defaul` instead of `default` | Typo when entering `set /network-instance default` — trailing `t` dropped | `delete /network-instance defaul interface ethernet-1/1.0` then `set /network-instance default interface ethernet-1/1.0` and `commit stay` |

---

### Issue 5 — sw2 diff missing `admin-state enable`: CLOSED

RCA row 1 now states the fix clearly enough. Accepted.