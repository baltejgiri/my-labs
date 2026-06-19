# Lab 10 — IP Services: NTP, SNMP, Syslog & HSRP

**CCNA Domain:** 4.0 IP Services  
**CML Topology:** `topology.cml.yml`  
**Estimated Time:** 90–120 minutes

---

## Objectives

1. Configure an NTP master and synchronise client routers to it
2. Configure SNMPv2c and SNMPv3 community strings and users
3. Configure syslog to send messages to a centralised server
4. Implement HSRP on R1 and R2 for first-hop redundancy
5. Verify HSRP active/standby election and failover
6. Configure CDP/LLDP management features

---

## Topology

```
               [R3 — NTP Master / Syslog Server]
               Lo0: 3.3.3.3
              /                    \
       GE0/0 (10.0.13.x)   GE0/1 (10.0.23.x)
            |                       |
    [R1 — HSRP Active]     [R2 — HSRP Standby]
    192.168.1.2/24          192.168.1.3/24
    HSRP VIP: 192.168.1.1
            \                       /
          GE0/0 (SW1 GE0/0)  GE0/0 (SW1 GE0/1)
                     [SW1]
                    /     \
                  PC1      PC2
             192.168.1.x  192.168.1.x
```

---

## Pre-Configured Addressing

All interface IPs and default routes are pre-configured.

| Device | Interface | IP | Description |
|--------|-----------|-----|-------------|
| R1 | GE0/0 | 192.168.1.2/24 | HSRP LAN (active) |
| R1 | GE0/1 | 10.0.13.1/30 | Uplink to R3 |
| R2 | GE0/0 | 192.168.1.3/24 | HSRP LAN (standby) |
| R2 | GE0/1 | 10.0.23.1/30 | Uplink to R3 |
| R3 | GE0/0 | 10.0.13.2/30 | From R1 |
| R3 | GE0/1 | 10.0.23.2/30 | From R2 |
| R3 | Lo0 | 3.3.3.3/32 | NTP master / syslog |
| HSRP VIP | — | 192.168.1.1/24 | Virtual gateway for PCs |

---

## Tasks

### Task 1 — Configure Static IPs on PCs

1.1 Assign PC1 the IP 192.168.1.10/24 with default gateway **192.168.1.1** (the HSRP VIP).

1.2 Assign PC2 the IP 192.168.1.20/24 with default gateway **192.168.1.1**.

1.3 Note that 192.168.1.1 is not yet configured anywhere — pings will fail until HSRP is set up.

### Task 2 — NTP

2.1 On R3, configure it as an NTP master (stratum 2):
   ```
   ntp master 2
   ```

2.2 On R1 and R2, configure R3's loopback (3.3.3.3) as the NTP server. Verify R3 is reachable from R1 and R2 first.

2.3 On R1 and R2, run `show ntp status` and `show ntp associations`. Confirm synchronisation (status should reach `synchronized` — allow 1–2 minutes).

2.4 Set the timezone on all routers to your local timezone.

2.5 Verify that R1 and R2 have the same time: `show clock`.

### Task 3 — SNMP

3.1 On R1, configure SNMPv2c:
   - Read-only community string: `public-ro`
   - Read-write community string: `private-rw`
   - Contact: `netadmin@lab.local`
   - Location: `CML Lab`

3.2 Configure an SNMP trap receiver pointing to R3's loopback (3.3.3.3) using community string `public-ro`.

3.3 On R1, configure SNMPv3 with a user named `snmpv3user`:
   - Authentication: SHA, password `AuthPass123`
   - Privacy: AES 128, password `PrivPass123`
   - Security level: `authPriv`

3.4 Verify SNMP configuration: `show snmp` and `show snmp user`.

### Task 4 — Syslog

4.1 On R1 and R2, configure syslog to send all messages at severity **informational** (level 6) and below to R3's loopback (3.3.3.3).

4.2 Configure the syslog source interface to use each router's uplink interface (so syslog packets have a consistent source IP).

4.3 Set logging buffer size to 64000 bytes and severity to **debugging** for the local buffer.

4.4 Generate a syslog event by manually shutting and restoring an interface. Verify the log appeared locally: `show logging`.

4.5 Set the logging timestamp format to `datetime msec` so timestamps are human-readable.

### Task 5 — HSRP

5.1 On R1 GE0/0, configure HSRP group 1:
   - Virtual IP: 192.168.1.1
   - Priority: 110 (making R1 the preferred active router)
   - Preempt: enabled

5.2 On R2 GE0/0, configure HSRP group 1:
   - Virtual IP: 192.168.1.1 (same group)
   - Priority: 100 (default — makes R2 standby)
   - Preempt: enabled

5.3 Verify HSRP state: `show standby brief` on both routers. Confirm R1 is Active and R2 is Standby.

5.4 From PC1, ping the HSRP VIP (192.168.1.1) and 3.3.3.3 (R3 loopback). Both should succeed.

5.5 Test failover: shut down R1 GE0/0. Observe R2 taking over as Active. Verify PC1 can still reach 3.3.3.3.

5.6 Restore R1 GE0/0. Verify R1 preempts and returns to Active (due to the preempt setting and higher priority).

### Task 6 — HSRP Interface Tracking

6.1 Configure HSRP interface tracking on R1 so that if R1's uplink to R3 (GE0/1) goes down, R1's HSRP priority drops below R2's priority (triggering a failover).

6.2 Configure the decrement value so that a GE0/1 failure causes R1's priority to fall from 110 to 90 (decrement of 20).

6.3 Test: shut down R1 GE0/1. Verify R2 becomes Active. Restore and verify R1 reclaims Active.

---

## Verification Checklist

- [ ] R1 and R2 are synchronised to R3 via NTP (`show ntp status` = `synchronized`)
- [ ] SNMPv2c community strings configured on R1
- [ ] SNMPv3 user `snmpv3user` configured with authPriv
- [ ] Syslog messages being sent to 3.3.3.3
- [ ] `show standby brief` shows R1 as Active, R2 as Standby
- [ ] PC1 → 3.3.3.3 ping succeeds through HSRP VIP
- [ ] HSRP failover works when R1 GE0/0 is shut
- [ ] HSRP interface tracking causes failover when R1 GE0/1 is shut
