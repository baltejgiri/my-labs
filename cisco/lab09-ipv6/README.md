# Lab 09 — IPv6 Fundamentals & Routing

**CCNA Domains:** 1.0 Network Fundamentals · 3.0 IP Connectivity  
**CML Topology:** `topology.cml.yml`  
**Estimated Time:** 90–120 minutes

---

## Objectives

1. Configure IPv6 global unicast addresses manually and using EUI-64
2. Verify automatically generated link-local and solicited-node multicast addresses
3. Enable SLAAC on a LAN and verify end hosts auto-configure from Router Advertisements
4. Configure stateful DHCPv6 for hosts that need server-assigned addresses
5. Configure IPv6 static routes
6. Configure OSPFv3 for IPv6 routing
7. Implement dual-stack (IPv4 and IPv6) on all routers

---

## Topology

```
[PC1]---[R1 GE0/1]   [R1 GE0/0]---[R2 GE0/0]   [R2 GE0/1]---[R3 GE0/0]   [R3 GE0/1]---[PC2]
       LAN1: 2001:db8:1::/64    Link: 2001:db8:12::/64    Link: 2001:db8:23::/64    LAN3: 2001:db8:3::/64
```

---

## IPv6 Address Space

You are allocated: **2001:db8::/32**

Plan and assign addresses from this block.

| Segment | Subnet | Purpose |
|---------|--------|---------|
| R1 LAN (GE0/1) | 2001:db8:1::/64 | PC1 connects here |
| R1 — R2 link (GE0/0 / GE0/0) | 2001:db8:12::/64 | Point-to-point |
| R2 — R3 link (GE0/1 / GE0/0) | 2001:db8:23::/64 | Point-to-point |
| R3 LAN (GE0/1) | 2001:db8:3::/64 | PC2 connects here |
| R1 Loopback | 2001:db8:1::1/128 | Router ID |
| R2 Loopback | 2001:db8:2::2/128 | Router ID |
| R3 Loopback | 2001:db8:3::3/128 | Router ID |

---

## Tasks

### Task 1 — Enable IPv6 Routing & Configure Addresses

1.1 Note that `ipv6 unicast-routing` is already enabled on all routers. Verify with `show running-config | include unicast`.

1.2 On R1, configure the following IPv6 addresses:
   - GE0/0: `2001:db8:12::1/64`
   - GE0/1: `2001:db8:1::1/64`
   - Lo0: `2001:db8:1::1/128`

1.3 On R2, configure:
   - GE0/0: `2001:db8:12::2/64`
   - GE0/1: `2001:db8:23::1/64`
   - Lo0: `2001:db8:2::2/128`

1.4 On R3, configure:
   - GE0/0: `2001:db8:23::2/64`
   - GE0/1: `2001:db8:3::1/64`
   - Lo0: `2001:db8:3::3/128`

1.5 Run `show ipv6 interface brief` on each router. Confirm all interfaces show a global unicast address and a link-local address.

### Task 2 — Address Types & EUI-64

2.1 On R2 GE0/0, remove the manually configured address and instead use the **EUI-64** format: `2001:db8:12::/64 eui-64`.

2.2 Run `show ipv6 interface GigabitEthernet0/0` on R2. Record the full EUI-64 generated address and explain how IOS derived it from the MAC address.

2.3 Identify the link-local address on R1 GE0/0. Explain how it is formed.

2.4 Run `show ipv6 interface GigabitEthernet0/0` on R1 and identify the solicited-node multicast address.

### Task 3 — SLAAC

3.1 On R1's GE0/1 (LAN1 interface), ensure the IPv6 address is configured and `no ipv6 nd suppress-ra` is set (RA should be sent by default).

3.2 On PC1, enable IPv6 autoconfiguration (`ip -6 addr add` or SLAAC via `sysctl`). On Alpine Linux, run `ip link set eth0 up && sysctl -w net.ipv6.conf.eth0.accept_ra=1`.

3.3 Verify PC1 auto-configured an IPv6 address in the 2001:db8:1::/64 prefix. Record the full address and identify the interface identifier portion.

3.4 Verify PC1's default IPv6 gateway matches R1's GE0/1 link-local address.

### Task 4 — Stateful DHCPv6

4.1 On R3, configure a stateful DHCPv6 pool named `LAN3-POOL` for PC2:
   - Address prefix: 2001:db8:3::/64
   - DNS server: 2001:4860:4860::8888

4.2 Configure R3's GE0/1 to use the DHCPv6 pool and set the RA M-flag (managed) so clients know to use DHCPv6.

4.3 On PC2, configure the interface to request an address via DHCPv6.

4.4 Verify PC2 received an address. Run `show ipv6 dhcp binding` on R3.

### Task 5 — IPv6 Static Routes

5.1 Without any routing protocol, configure static IPv6 routes on R1 to reach:
   - 2001:db8:23::/64 (R2-R3 link) via R2's link-local on GE0/0
   - 2001:db8:3::/64 (R3 LAN) via R2's link-local on GE0/0
   - 2001:db8:2::2/128 (R2 loopback) via R2's link-local on GE0/0

5.2 Configure matching static routes on R2 and R3 to achieve full reachability.

5.3 Ping R3's loopback from R1: `ping 2001:db8:3::3`.

### Task 6 — OSPFv3

6.1 Remove all static IPv6 routes added in Task 5.

6.2 Configure OSPFv3 process 1 on all three routers. Place all interfaces in **Area 0**.

6.3 Verify OSPFv3 neighbours: `show ipv6 ospf neighbor`.

6.4 Verify full routing table: `show ipv6 route ospf` on each router.

6.5 Ping PC2 from PC1 using IPv6.

### Task 7 — Dual-Stack

7.1 Configure IPv4 addresses on all router interfaces (use the 10.x.x.x/30 space for links and 192.168.x.1/24 for LANs).

7.2 Configure OSPFv2 for IPv4 routing.

7.3 Verify both `show ip route ospf` and `show ipv6 route ospf` show complete tables.

7.4 From R1, ping R3 using both IPv4 and IPv6. Record both results.

---

## Verification Checklist

- [ ] All interfaces have global unicast and link-local IPv6 addresses
- [ ] EUI-64 address generated correctly on R2 GE0/0 (and derivation explained)
- [ ] PC1 auto-configured via SLAAC in 2001:db8:1::/64
- [ ] PC2 received DHCPv6 address in 2001:db8:3::/64
- [ ] OSPFv3 adjacencies in FULL state on all links
- [ ] PC1 can ping PC2 using IPv6
- [ ] Dual-stack working — both IPv4 and IPv6 routing operational
