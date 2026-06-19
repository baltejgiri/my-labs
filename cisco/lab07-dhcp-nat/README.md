# Lab 07 — DHCP & NAT

**CCNA Domain:** 4.0 IP Services  
**CML Topology:** `topology.cml.yml`  
**Estimated Time:** 75–90 minutes

---

## Objectives

1. Configure a Cisco router as a DHCP server for a local LAN
2. Verify DHCP lease allocation on end hosts
3. Configure a DHCP relay agent (`ip helper-address`) for a remote segment
4. Configure static NAT for a specific inside host
5. Configure PAT (NAT overload) so all inside hosts share a single public IP
6. Verify NAT translations in the NAT table

---

## Topology

```
                 [R2-ISP]
                 8.8.8.8
                    |
                 GE0/1 (203.0.113.x)
                [R1 — DHCP Server + NAT Gateway]
               GE0/0                GE0/2
       (192.168.1.0/24)       (192.168.2.0/24)
            [SW1]                  [SW2]
           /     \                /     \
         PC1     PC2           PC3    Server1
    (DHCP)   (DHCP)       (DHCP relay)  (static IP)
```

---

## Pre-Configured Addressing

| Device | Interface | IP | Description |
|--------|-----------|-----|-------------|
| R1 | GE0/1 | 203.0.113.1/30 | Outside — link to ISP |
| R2-ISP | GE0/0 | 203.0.113.2/30 | ISP side |
| R2-ISP | Lo0 | 8.8.8.8/32 | Simulated internet |

Configure the rest during the lab.

| Device | Expected IP | Method |
|--------|-------------|--------|
| R1 GE0/0 | 192.168.1.1/24 | Static (you configure) |
| R1 GE0/2 | 192.168.2.1/24 | Static (you configure) |
| PC1 | 192.168.1.x/24 | DHCP from R1 |
| PC2 | 192.168.1.x/24 | DHCP from R1 |
| PC3 | 192.168.2.x/24 | DHCP via relay |
| Server1 | 192.168.2.100/24 | Static (DHCP excluded) |

---

## Tasks

### Task 1 — Configure R1 Inside Interfaces

1.1 Configure R1 GE0/0 with IP 192.168.1.1/24 and bring it up. This is the gateway for LAN1 (SW1).

1.2 Configure R1 GE0/2 with IP 192.168.2.1/24 and bring it up. This is the gateway for LAN2 (SW2).

1.3 Verify both interfaces are Up/Up.

### Task 2 — DHCP Server on R1

2.1 Create a DHCP excluded-address range on R1 to reserve the first 10 addresses of 192.168.1.0/24 (so DHCP does not assign them to clients).

2.2 Create a DHCP pool named `LAN1` for the 192.168.1.0/24 network. Set:
   - Network and mask
   - Default gateway: 192.168.1.1
   - DNS server: 8.8.8.8
   - Lease time: 1 day

2.3 On PC1, request an IP via DHCP (`udhcpc -i eth0` on Alpine Linux). Verify PC1 received an address from the pool.

2.4 Repeat for PC2. Verify a different address was assigned.

2.5 On R1, run `show ip dhcp binding` and confirm both leases are listed.

2.6 Run `show ip dhcp pool LAN1` and `show ip dhcp conflict` on R1.

### Task 3 — DHCP Relay (ip helper-address)

3.1 Create a second DHCP pool named `LAN2` on R1 for the 192.168.2.0/24 network. Exclude the first 10 addresses and 192.168.2.100 (for Server1). Set gateway to 192.168.2.1.

3.2 On R1's GE0/2 interface, configure `ip helper-address` pointing to R1's own GE0/0 IP (192.168.1.1). This demonstrates relay — in a real network this would point to a centralised DHCP server.

   > Note: For this lab the DHCP server and relay are on the same router. The helper-address still demonstrates the relay mechanism.

3.3 On PC3, request a DHCP address. Verify it received an address from the LAN2 pool.

3.4 Manually assign Server1 a static IP of 192.168.2.100/24 with gateway 192.168.2.1.

### Task 4 — Static NAT

4.1 Configure a static NAT entry mapping Server1's private IP (192.168.2.100) to a fixed public IP. Use 203.0.113.1 as the inside global address.

   > Note: In a real environment you would use a separate public IP from a pool, but for this lab 203.0.113.1 demonstrates the concept.

4.2 Mark R1 GE0/0 and GE0/2 as `ip nat inside` and GE0/1 as `ip nat outside`.

4.3 From R2-ISP, ping Server1 using the public NAT IP. Run `show ip nat translations` on R1 and verify the static entry.

### Task 5 — PAT (NAT Overload)

5.1 Configure a standard ACL that matches the inside subnets (192.168.1.0/24 and 192.168.2.0/24).

5.2 Configure PAT using the ACL and R1's outside interface (`overload`).

5.3 From PC1 and PC2, ping 8.8.8.8 (R2-ISP loopback).

5.4 On R1, run `show ip nat translations` immediately after the pings. Observe the dynamic PAT entries (inside local, inside global, outside global).

5.5 Run `show ip nat statistics` and record the hit count.

---

## Verification Checklist

- [ ] PC1 and PC2 received DHCP addresses from the LAN1 pool
- [ ] PC3 received a DHCP address via relay (LAN2 pool)
- [ ] `show ip dhcp binding` shows 3 leases
- [ ] Static NAT entry for Server1 visible in `show ip nat translations`
- [ ] PC1 can ping 8.8.8.8 through PAT
- [ ] PAT translations visible in NAT table after ping
