# Lab 08 — ACLs & Device Security

**CCNA Domain:** 5.0 Security Fundamentals  
**CML Topology:** `topology.cml.yml`  
**Estimated Time:** 90–105 minutes

---

## Objectives

1. Configure a numbered standard ACL to restrict management access
2. Configure a numbered extended ACL to filter traffic by protocol and port
3. Configure a named ACL and understand the advantages over numbered ACLs
4. Apply ACLs to the correct interface and direction
5. Harden device management: SSH only, disable Telnet, set banners
6. Configure local username and password-based authentication
7. Verify ACL match statistics

---

## Topology

```
[PC1] \              
       [SW1]---[R1 GE0/0]---[R1 GE0/1]---[R2 GE0/0]---[R2 GE0/1]---[Server1]
[PC2] /
      192.168.1.0/24         10.0.12.0/30              192.168.2.0/24
```

---

## Pre-Configured Addressing

All IPs, default gateways, and static routes are already configured. Do **not** change them.

| Device | Interface | IP | Description |
|--------|-----------|-----|-------------|
| R1 | GE0/0 | 192.168.1.1/24 | Inside LAN gateway |
| R1 | GE0/1 | 10.0.12.1/30 | Link to R2 |
| R2 | GE0/0 | 10.0.12.2/30 | Link to R1 |
| R2 | GE0/1 | 192.168.2.1/24 | Server LAN gateway |
| PC1 | eth0 | 192.168.1.10/24 | GW: 192.168.1.1 |
| PC2 | eth0 | 192.168.1.20/24 | GW: 192.168.1.1 |
| Server1 | eth0 | 192.168.2.100/24 | GW: 192.168.2.1 |

---

## Tasks

### Task 1 — Verify Baseline Connectivity

1.1 Assign static IPs to PC1, PC2, and Server1 per the addressing table above.

1.2 Verify that PC1 can ping Server1 (192.168.2.100). This is the unrestricted baseline.

1.3 Verify PC1 can ping PC2.

### Task 2 — Standard ACL (Restrict Management Access)

2.1 On R1, create a numbered standard ACL (ACL 10) that permits only PC1 (192.168.1.10) to access the router via VTY lines. All other sources should be denied.

2.2 Apply ACL 10 to R1's VTY lines (lines 0 through 4) in the **inbound** direction.

2.3 Attempt to Telnet to R1 from PC1 and from PC2. Record the results.

2.4 Run `show access-lists 10` on R1 and note the match counts.

### Task 3 — Extended ACL (Traffic Filtering)

3.1 Create numbered extended ACL 110 on R1 with the following policy:
   - **Permit** HTTP (TCP port 80) from the 192.168.1.0/24 network to Server1 (192.168.2.100)
   - **Permit** ICMP from 192.168.1.0/24 to any destination
   - **Deny** all other traffic from 192.168.1.0/24 to the 192.168.2.0/24 network
   - **Permit** any other traffic (implicit or explicit)

3.2 Apply ACL 110 inbound on R1 GE0/0 (the inside interface).

3.3 Test the ACL:
   - From PC1, ping Server1 — should succeed (ICMP permitted)
   - From PC1, attempt to reach Server1 on TCP port 80 (e.g., `wget http://192.168.2.100`) — should be permitted
   - From PC1, attempt SSH or any other TCP connection to Server1 — should be denied

3.4 Run `show access-lists 110` and confirm the correct rules are matching.

### Task 4 — Named ACL

4.1 Remove ACL 110 from R1 GE0/0.

4.2 Create a named extended ACL called `INSIDE-TO-SERVER` with the same policy as Task 3.

4.3 Apply it inbound on R1 GE0/0.

4.4 Verify the same behaviour as Task 3. Run `show ip access-lists INSIDE-TO-SERVER`.

4.5 Add a new entry to the named ACL permitting HTTPS (TCP 443) without rewriting the entire ACL. Verify the new entry is inserted correctly using sequence numbers.

### Task 5 — Device Hardening & SSH

5.1 On both R1 and R2, configure:
   - Local username `netadmin` with privilege 15 and a strong encrypted password
   - `service password-encryption`
   - A MOTD banner warning that unauthorised access is prohibited
   - Disable Telnet on VTY lines — allow SSH only

5.2 Configure the domain name `lab.local` and generate a 2048-bit RSA key pair on both routers.

5.3 Set `ip ssh version 2` and set the authentication timeout to 60 seconds.

5.4 Test SSH access from PC1 to R1 using the netadmin credentials. Verify Telnet is rejected.

5.5 Run `show ip ssh` and `show ssh` on R1 to verify the SSH session.

---

## Verification Checklist

- [ ] Standard ACL 10: PC1 VTY access permitted, PC2 denied
- [ ] Extended ACL (named): ICMP and HTTP permitted, other protocols denied
- [ ] Named ACL visible with `show ip access-lists`
- [ ] HTTPS entry added to named ACL using sequence number
- [ ] SSH working from PC1 to R1
- [ ] Telnet rejected on all VTY lines
- [ ] `show ip ssh` confirms version 2
