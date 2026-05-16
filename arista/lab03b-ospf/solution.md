# Lab 03b — Packet flow in OSPF Multiarea

## Topology

![Lab 03b - OSPF Topology](/arista/lab03b-ospf/lab03b-ospf.clab.png)

---

# Overview

This lab demonstrate the OSPF protocol in multiarea setup. This lab is created using Arista cEOS image on containerlab. Yaml file [lab03b-ospf](/arista/lab03b-ospf/lab03b-ospf.clab.yml) contains the template for the network topology definding nodes, 

Directory [Configs](/arista/lab03b-ospf/configs) contains the two states of configuration **baseconfig** and **ospf configured**. Baseconfig.cfg for R1-R4 snapshots were taken before configuring the ospf configuration and ospfconfigured.cfg snapshots were taken after configuring ospf.

## Problem
Which path R3 takes to route traffic to destination address 11.1.1.1?

## Solution

In order to understand which path R3 takes to route traffic to network address in the area 2. I used ```show ip route``` and ```show lldp neighbors``` commands starting from R3 since the question described packet is initiating from R3.

### Step 1.

"On R3, the ```show ip route``` command shows that the destination network 11.1.1.0/30 is learned via OSPF Inter-area (O IA). It is accessible through the next-hop IP address 13.1.1.1 on interface Ethernet1.

```bash
R3#show ip route

VRF: default
Source Codes:
       C - connected, S - static, K - kernel,
       O - OSPF, IA - OSPF inter area, E1 - OSPF external type 1,
       E2 - OSPF external type 2, N1 - OSPF NSSA external type 1,
       N2 - OSPF NSSA external type2, B - Other BGP Routes,
       B I - iBGP, B E - eBGP, R - RIP, I L1 - IS-IS level 1,
       I L2 - IS-IS level 2, O3 - OSPFv3, A B - BGP Aggregate,
       A O - OSPF Summary, NG - Nexthop Group Static Route,
       V - VXLAN Control Service, M - Martian,
       DH - DHCP client installed default route,
       DP - Dynamic Policy Route, L - VRF Leaked,
       G  - gRIBI, RC - Route Cache Route,
       CL - CBF Leaked Route

Gateway of last resort is not set

 O IA     1.1.1.1/32 [110/20]
           via 13.1.1.1, Ethernet1
 O IA     2.2.2.2/32 [110/30]
           via 13.1.1.1, Ethernet1
 C        3.3.3.3/32
           directly connected, Loopback0
 O        4.4.4.4/32 [110/20]
           via 12.1.1.1, Ethernet2
 O IA     10.1.1.0/30 [110/20]
           via 13.1.1.1, Ethernet1
 O IA     11.1.1.0/30 [110/30]
           via 13.1.1.1, Ethernet1
 C        12.1.1.0/30
           directly connected, Ethernet2
 C        13.1.1.0/30
           directly connected, Ethernet1
 C        172.20.20.0/24
           directly connected, Management0
```
To determine the exact neighbor device for this path, the ```show lldp neighbors eth1 detail``` command was executed. The output verifies that the next-hop router connected to Ethernet1 is R1 (system name is R1), which uses the Router ID 1.1.1.1 and connects via its local interface Ethernet3."

```bash
R3#show lldp neighbors eth1 detail 
Interface Ethernet1 detected 1 LLDP neighbors:

  Neighbor 001c.733b.aeee/"Ethernet3", age 27 seconds
  Discovered 11:34:35 ago; Last changed 11:34:31 ago
  - Chassis ID type: MAC address (4)
    Chassis ID     : 001c.733b.aeee
  - Port ID type: Interface name(5)
    Port ID     : "Ethernet3"
  - Time To Live: 120 seconds
  - Port Description: "R1-to-R3-Area1"
  - System Name: "R1"
  - System Description: "Arista Networks EOS version 4.32.0F-36401836.4320F (engineering build) running on an Arista cEOSLab"
  - System Capabilities : Bridge, Router
    Enabled Capabilities: Bridge, Router
  - Management Address Subtype: IPv4 
    Management Address        : 1.1.1.1
    Interface Number Subtype  : ifIndex (2)
    Interface Number          : 5000000
    OID String                : 
  - IEEE802.1 Port VLAN ID: 0
  - IEEE802.3 Link Aggregation
    Link Aggregation Status: Capable, Disabled (0x01)
    Port ID                : 0
  - IEEE802.3 Maximum Frame Size: 9236 bytes
```
### Step 2.

"On R1, the ```show ip route``` command reveals a routing table entry for network 11.1.1.0/30. This network is learned via OSPF Inter-area (O IA) and is reachable through the next-hop IP address 10.1.1.2 on interface Ethernet1.

```bash
R1#show ip route

VRF: default
Source Codes:
       C - connected, S - static, K - kernel,
       O - OSPF, IA - OSPF inter area, E1 - OSPF external type 1,
       E2 - OSPF external type 2, N1 - OSPF NSSA external type 1,
       N2 - OSPF NSSA external type2, B - Other BGP Routes,
       B I - iBGP, B E - eBGP, R - RIP, I L1 - IS-IS level 1,
       I L2 - IS-IS level 2, O3 - OSPFv3, A B - BGP Aggregate,
       A O - OSPF Summary, NG - Nexthop Group Static Route,
       V - VXLAN Control Service, M - Martian,
       DH - DHCP client installed default route,
       DP - Dynamic Policy Route, L - VRF Leaked,
       G  - gRIBI, RC - Route Cache Route,
       CL - CBF Leaked Route

Gateway of last resort is not set

 C        1.1.1.1/32
           directly connected, Loopback0
 O        2.2.2.2/32 [110/20]
           via 10.1.1.2, Ethernet1
 O        3.3.3.3/32 [110/20]
           via 13.1.1.2, Ethernet3
 O        4.4.4.4/32 [110/30]
           via 13.1.1.2, Ethernet3
 C        10.1.1.0/30
           directly connected, Ethernet1
 O IA     11.1.1.0/30 [110/20]
           via 10.1.1.2, Ethernet1
 O        12.1.1.0/30 [110/20]
           via 13.1.1.2, Ethernet3
 C        13.1.1.0/30
           directly connected, Ethernet3
 C        172.20.20.0/24
           directly connected, Management0
```

To verify the identity of the next-hop device, the ```show lldp neighbors eth1 detail``` command was executed. The output confirms that the neighbor attached to Ethernet1 is router R2, System name and router id of R2 matching the expected next-hop path.

```bash
R1#show lldp neighbors eth1 detail 
Interface Ethernet1 detected 1 LLDP neighbors:

  Neighbor 001c.731d.c35a/"Ethernet1", age 21 seconds
  Discovered 11:44:01 ago; Last changed 11:43:53 ago
  - Chassis ID type: MAC address (4)
    Chassis ID     : 001c.731d.c35a
  - Port ID type: Interface name(5)
    Port ID     : "Ethernet1"
  - Time To Live: 120 seconds
  - Port Description: "R2-to-R1-Area0"
  - System Name: "R2"
  - System Description: "Arista Networks EOS version 4.32.0F-36401836.4320F (engineering build) running on an Arista cEOSLab"
  - System Capabilities : Bridge, Router
    Enabled Capabilities: Bridge, Router
  - Management Address Subtype: IPv4 
    Management Address        : 2.2.2.2
    Interface Number Subtype  : ifIndex (2)
    Interface Number          : 5000000
    OID String                : 
  - IEEE802.1 Port VLAN ID: 0
  - IEEE802.3 Link Aggregation
    Link Aggregation Status: Capable, Disabled (0x01)
    Port ID                : 0
  - IEEE802.3 Maximum Frame Size: 9236 bytes
```
### Step 3.

On R2, the ```show ip route``` command shows that network 11.1.1.0/30 is a directly connected route accessible via Ethernet2. This confirms that R2 is directly connected to the 11.1.1.0/30 network. Because it is directly connected, the global routing table prioritizes the connected status (C) over OSPF, though R2 still advertises this network to other routers via OSPF.

```bash
R2#show ip route 

VRF: default
Source Codes:
       C - connected, S - static, K - kernel,
       O - OSPF, IA - OSPF inter area, E1 - OSPF external type 1,
       E2 - OSPF external type 2, N1 - OSPF NSSA external type 1,
       N2 - OSPF NSSA external type2, B - Other BGP Routes,
       B I - iBGP, B E - eBGP, R - RIP, I L1 - IS-IS level 1,
       I L2 - IS-IS level 2, O3 - OSPFv3, A B - BGP Aggregate,
       A O - OSPF Summary, NG - Nexthop Group Static Route,
       V - VXLAN Control Service, M - Martian,
       DH - DHCP client installed default route,
       DP - Dynamic Policy Route, L - VRF Leaked,
       G  - gRIBI, RC - Route Cache Route,
       CL - CBF Leaked Route

Gateway of last resort is not set

 O        1.1.1.1/32 [110/20]
           via 10.1.1.1, Ethernet1
 C        2.2.2.2/32
           directly connected, Loopback0
 O IA     3.3.3.3/32 [110/30]
           via 10.1.1.1, Ethernet1
 O IA     4.4.4.4/32 [110/40]
           via 10.1.1.1, Ethernet1
 C        10.1.1.0/30
           directly connected, Ethernet1
 C        11.1.1.0/30
           directly connected, Ethernet2
 O IA     12.1.1.0/30 [110/30]
           via 10.1.1.1, Ethernet1
 O IA     13.1.1.0/30 [110/20]
           via 10.1.1.1, Ethernet1
 C        172.20.20.0/24
           directly connected, Management0
```

We do not needs to use ```show lldp neighbor``` command since packet won't be going to next hop.

## Validation

Traceroute to the IP address 11.1.1.1 from R3, sourcing from both interfaces of R3 results the same output - matches to the explaination I wrote in three steps above.

```bash
R3#traceroute 11.1.1.1
traceroute to 11.1.1.1 (11.1.1.1), 30 hops max, 60 byte packets
 1  13.1.1.1 (13.1.1.1)  0.126 ms  0.057 ms  0.051 ms
 2  10.1.1.2 (10.1.1.2)  0.853 ms  1.238 ms  1.910 ms
 3  11.1.1.1 (11.1.1.1)  2.714 ms  2.964 ms  3.182 ms
!
R3#traceroute ip 11.1.1.1 source 13.1.1.2
traceroute to 11.1.1.1 (11.1.1.1), 30 hops max, 60 byte packets
 1  13.1.1.1 (13.1.1.1)  0.124 ms  0.072 ms  0.029 ms
 2  10.1.1.2 (10.1.1.2)  1.455 ms  1.877 ms  2.242 ms
 3  11.1.1.1 (11.1.1.1)  2.658 ms  2.881 ms  3.081 ms
!
R3#traceroute ip 11.1.1.1 source 12.1.1.2
traceroute to 11.1.1.1 (11.1.1.1), 30 hops max, 60 byte packets
 1  13.1.1.1 (13.1.1.1)  0.138 ms  0.033 ms  0.032 ms
 2  10.1.1.2 (10.1.1.2)  1.780 ms  2.744 ms  3.205 ms
 3  11.1.1.1 (11.1.1.1)  4.247 ms  4.556 ms  5.948 ms
R3#
```

## Why R3 can't route R4 directly?

R3 cannot route traffic to R4 directly because OSPF fundamentally requires all inter-area transit traffic to traverse the backbone area (Area 0).

Without this rule, a direct link between Area 1 and Area 2 could cause packets to trap themselves in an endless loop whenever a network change occurs.

## Router Roles I noticed
R1 and R2 are Area Border Routers (ABRs), both R1 and R2 have one of their interface in backbone area and other interface in the non-backbone area.

I used ```show ip ospf``` command on all 4 routers to understand their roles in the OSPF topology.

```bash
R1#show ip ospf 
OSPF instance 1 with ID 1.1.1.1 VRF default
 Supports opaque LSA
Maximum number of LSA allowed 12000
  Threshold for warning message 75%
  Ignore-time 5 minutes, reset-time 5 minutes
  Ignore-count allowed 5, current 0
 It is not an autonomous system boundary router and is an area border router <<<<<<<<<<<
 Initial SPF schedule delay 0 msecs
 Minimum hold time between two consecutive SPFs 5000 msecs
 Current hold time between two consecutive SPFs 5000 msecs
 Maximum wait time between two consecutive SPFs 5000 msecs
 Minimum LSA arrival 1000 msecs
 Initial LSA throttle delay 1000 msecs
 Minimum hold time for LSA throttle 5000 msecs
 Maximum wait time for LSA throttle 5000 msecs
 Number of external LSA 0, Checksum sum 0
 Number of opaque AS LSA 0, Checksum sum 0
 Number of areas in this router is 2. 2 normal, 0 stub, 0 nssa
 Number of LSA 17
 Time since last SPF 43997 secs
 No Scheduled SPF
 Adjacency exchange-start threshold is 20
 Maximum number of next-hops supported in ECMP is 128
 Retransmission threshold for LSA is 10
 Number of backbone neighbors is 1
 Routes over tunnel interfaces disabled
 Graceful-restart is not configured
 Graceful-restart-helper mode is enabled
 Area 0.0.0.0
 Number of interface in this area is 2
   It is a normal area
   Traffic engineering is disabled
   Area has None authentication 
   SPF algorithm executed 17 times
   Number of LSA 8. Checksum Sum 327585
   Number of opaque link LSA 0. Checksum Sum 0
   Number of opaque area LSA 0. Checksum Sum 0
   Number of indication LSA 0
   Number of DC-clear LSA 0
 Area 0.0.0.1
 Number of interface in this area is 1
   It is a normal area
   Traffic engineering is disabled
   Area has None authentication 
   SPF algorithm executed 8 times
   Number of LSA 9. Checksum Sum 312661
   Number of opaque link LSA 0. Checksum Sum 0
   Number of opaque area LSA 0. Checksum Sum 0
   Number of indication LSA 0
   Number of DC-clear LSA 0
```

```bash
R2#show ip ospf 
OSPF instance 1 with ID 2.2.2.2 VRF default
 Supports opaque LSA
Maximum number of LSA allowed 12000
  Threshold for warning message 75%
  Ignore-time 5 minutes, reset-time 5 minutes
  Ignore-count allowed 5, current 0
 It is not an autonomous system boundary router and is an area border router <<<<<<<<<<<
 Initial SPF schedule delay 0 msecs
 Minimum hold time between two consecutive SPFs 5000 msecs
 Current hold time between two consecutive SPFs 5000 msecs
 Maximum wait time between two consecutive SPFs 5000 msecs
 Minimum LSA arrival 1000 msecs
 Initial LSA throttle delay 1000 msecs
 Minimum hold time for LSA throttle 5000 msecs
 Maximum wait time for LSA throttle 5000 msecs
 Number of external LSA 0, Checksum sum 0
 Number of opaque AS LSA 0, Checksum sum 0
 Number of areas in this router is 2. 2 normal, 0 stub, 0 nssa
 Number of LSA 18
 Time since last SPF 44059 secs
 No Scheduled SPF
 Adjacency exchange-start threshold is 20
 Maximum number of next-hops supported in ECMP is 128
 Retransmission threshold for LSA is 10
 Number of backbone neighbors is 1
 Routes over tunnel interfaces disabled
 Graceful-restart is not configured
 Graceful-restart-helper mode is enabled
 Area 0.0.0.0
 Number of interface in this area is 2
   It is a normal area
   Traffic engineering is disabled
   Area has None authentication 
   SPF algorithm executed 15 times
   Number of LSA 8. Checksum Sum 327585
   Number of opaque link LSA 0. Checksum Sum 0
   Number of opaque area LSA 0. Checksum Sum 0
   Number of indication LSA 0
   Number of DC-clear LSA 0
 Area 0.0.0.2
 Number of interface in this area is 1
   It is a normal area
   Traffic engineering is disabled
   Area has None authentication 
   SPF algorithm executed 10 times
   Number of LSA 10. Checksum Sum 370664
   Number of opaque link LSA 0. Checksum Sum 0
   Number of opaque area LSA 0. Checksum Sum 0
   Number of indication LSA 0
   Number of DC-clear LSA 0
```

R3 and R4 are internal routers, R3 and R4 does not have any of their interface connecting to backbone area.

```bash
R3#show ip ospf 
OSPF instance 1 with ID 3.3.3.3 VRF default
 Supports opaque LSA
Maximum number of LSA allowed 12000
  Threshold for warning message 75%
  Ignore-time 5 minutes, reset-time 5 minutes
  Ignore-count allowed 5, current 0
 It is not an autonomous system boundary router and is not an area border router <<<<<<<<
 Initial SPF schedule delay 0 msecs
 Minimum hold time between two consecutive SPFs 5000 msecs
 Current hold time between two consecutive SPFs 5000 msecs
 Maximum wait time between two consecutive SPFs 5000 msecs
 Minimum LSA arrival 1000 msecs
 Initial LSA throttle delay 1000 msecs
 Minimum hold time for LSA throttle 5000 msecs
 Maximum wait time for LSA throttle 5000 msecs
 Number of external LSA 0, Checksum sum 0
 Number of opaque AS LSA 0, Checksum sum 0
 Number of areas in this router is 1. 1 normal, 0 stub, 0 nssa
 Number of LSA 9
 Time since last SPF 44086 secs
 No Scheduled SPF
 Adjacency exchange-start threshold is 20
 Maximum number of next-hops supported in ECMP is 128
 Retransmission threshold for LSA is 10
 Number of backbone neighbors is 0
 Routes over tunnel interfaces disabled
 Graceful-restart is not configured
 Graceful-restart-helper mode is enabled
 Area 0.0.0.1
 Number of interface in this area is 3
   It is a normal area
   Traffic engineering is disabled
   Area has None authentication 
   SPF algorithm executed 10 times
   Number of LSA 9. Checksum Sum 312661
   Number of opaque link LSA 0. Checksum Sum 0
   Number of opaque area LSA 0. Checksum Sum 0
   Number of indication LSA 0
   Number of DC-clear LSA 0
```

```bash
R4#show ip ospf 
OSPF instance 1 with ID 4.4.4.4 VRF default
 Supports opaque LSA
Maximum number of LSA allowed 12000
  Threshold for warning message 75%
  Ignore-time 5 minutes, reset-time 5 minutes
  Ignore-count allowed 5, current 0
 It is not an autonomous system boundary router and is not an area border router <<<<<<<<
 Initial SPF schedule delay 0 msecs
 Minimum hold time between two consecutive SPFs 5000 msecs
 Current hold time between two consecutive SPFs 5000 msecs
 Maximum wait time between two consecutive SPFs 5000 msecs
 Minimum LSA arrival 1000 msecs
 Initial LSA throttle delay 1000 msecs
 Minimum hold time for LSA throttle 5000 msecs
 Maximum wait time for LSA throttle 5000 msecs
 Number of external LSA 0, Checksum sum 0
 Number of opaque AS LSA 0, Checksum sum 0
 Number of areas in this router is 2. 2 normal, 0 stub, 0 nssa
 Number of LSA 19
 Time since last SPF 44096 secs
 No Scheduled SPF
 Adjacency exchange-start threshold is 20
 Maximum number of next-hops supported in ECMP is 128
 Retransmission threshold for LSA is 10
 Number of backbone neighbors is 0
 Routes over tunnel interfaces disabled
 Graceful-restart is not configured
 Graceful-restart-helper mode is enabled
 Area 0.0.0.1
 Number of interface in this area is 2
   It is a normal area
   Traffic engineering is disabled
   Area has None authentication 
   SPF algorithm executed 8 times
   Number of LSA 9. Checksum Sum 312661
   Number of opaque link LSA 0. Checksum Sum 0
   Number of opaque area LSA 0. Checksum Sum 0
   Number of indication LSA 0
   Number of DC-clear LSA 0
 Area 0.0.0.2
 Number of interface in this area is 1
   It is a normal area
   Traffic engineering is disabled
   Area has None authentication 
   SPF algorithm executed 7 times
   Number of LSA 10. Checksum Sum 370664
   Number of opaque link LSA 0. Checksum Sum 0
   Number of opaque area LSA 0. Checksum Sum 0
   Number of indication LSA 0
   Number of DC-clear LSA 0
```

# Summary

The only path for R3 to reach network 11.1.1.0/30 is R3 > R1 > R2 > R4. This is because OSPF requires all non-backbone areas to connect to the backbone (Area 0) to pass traffic between each other, preventing routing loops.