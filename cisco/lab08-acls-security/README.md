# Lab 08: IPv4 ACLs (standard, extended and named)

**CCNA Domain:** 5.0 Security Fundamentals (5.6 Configure and verify access control lists)  
**Platforms:** Cisco Packet Tracer 9.0, Cisco Modeling Labs (CML) 2.x, or EVE-NG  
**Based on:** CCNA 200-301 Official Cert Guide, Volume 2, Chapter 2 (Basic IPv4 Access Control Lists) and  
Chapter 3 (Advanced IPv4 Access Control Lists). The topology and host names follow the book's Figure 3-8.  
**Estimated time:** 15 minutes to build (CML: import only), 20 minutes for the ACL tasks  
**Tested:** CML 2.x with IOSv and Alpine, baseline and work order (2026-11-15)

---

## Objectives

1. Choose between a standard and an extended ACL for a requirement
2. Place each ACL on the right router, interface and direction
3. Configure numbered and named ACLs
4. Work with first-match logic and the implicit deny at the end of every ACL
5. Verify with tests from the hosts and with `show ip access-lists` match counts
6. Edit an ACL with sequence numbers

---

## Topology

```
                                      Gi0/1   Gi0/1
 Server1 .1.100 --+                 +-------- R2 Gi0/0 --- SW12 --- Larry .2.10
                  SW1 --- Gi0/0 R1 -+          | Gi0/2
 Server2 .1.102 --+                 |          |
                                    |          | Gi0/2
                                    +-------- R3 Gi0/0 --- SW13 --+- Bob   .3.10
                                      Gi0/2   Gi0/1               +- Jerry .3.9
```

| Site | Hosts | Subnet |
|---|---|---|
| Main site (R1) | Server1 172.16.1.100, Server2 172.16.1.102 | 172.16.1.0/24 |
| Branch (R2) | Larry 172.16.2.10 | 172.16.2.0/24 |
| Branch (R3) | Bob 172.16.3.10, Jerry 172.16.3.9 | 172.16.3.0/24 |
| R1-R2 link | | 172.16.12.0/30 |
| R1-R3 link | | 172.16.13.0/30 |
| R2-R3 link | | 172.16.23.0/30 |

---

## Part 1: Build the network

Pick one platform. The router configs and the work order are the same on all three.

| | Packet Tracer | CML | EVE-NG |
|---|---|---|---|
| Routers | 2911 | IOSv | vIOS (or IOL, see note) |
| Switches | 2960 | Unmanaged switch | Network (bridge) object or vIOS-L2 |
| Servers and PCs | Server, PC | Alpine | Any Linux node |
| Setup | Build it yourself (below) | Import `topology.cml.yml`: everything is pre-configured | Build it yourself (below) |

### Option A: CML

1. In CML, **Import** `topology.cml.yml`, then **Start** the lab.
2. Routing, addresses and the server services are already configured. Hosts log in with `cisco` / `cisco`.
3. Skip to the **baseline check**.

### Option B: EVE-NG

1. Add the 11 nodes from the table above and cable them as in the **Cables** table below (on vIOS the port names
   match). With an unmanaged Network object as the switch, connect the router and the hosts to it.
2. Paste the **router configs** below into R1, R2 and R3.
3. On each Linux host, run the commands from **Linux host setup** below.

**IOL note:** IOL names its ports Ethernet0/0, 0/1, 0/2. Replace `GigabitEthernet0/x` with `Ethernet0/x` in the
router configs and in your ACL commands.

### Option C: Packet Tracer

Build it with the tables below: devices, cables, end-device addressing, router configs.

| Device | Packet Tracer model |
|---|---|
| R1, R2, R3 | Network Devices > Routers > **2911** |
| SW1, SW12, SW13 | Network Devices > Switches > **2960** |
| Server1, Server2 | End Devices > **Server** |
| Larry, Bob, Jerry | End Devices > **PC** |

### Cables

| From | To | Cable |
|---|---|---|
| R1 Gi0/1 | R2 Gi0/1 | Copper Cross-Over |
| R1 Gi0/2 | R3 Gi0/1 | Copper Cross-Over |
| R2 Gi0/2 | R3 Gi0/2 | Copper Cross-Over |
| R1 Gi0/0 | SW1 Gi0/1 | Copper Straight-Through |
| R2 Gi0/0 | SW12 Gi0/1 | Copper Straight-Through |
| R3 Gi0/0 | SW13 Gi0/1 | Copper Straight-Through |
| Server1, Server2 | SW1 Fa0/1, Fa0/2 | Copper Straight-Through |
| Larry | SW12 Fa0/1 | Copper Straight-Through |
| Bob, Jerry | SW13 Fa0/1, Fa0/2 | Copper Straight-Through |

The switches need no configuration. (In CML and EVE-NG with Linux hosts, the host port is `eth0`.)

### End-device addressing

Packet Tracer: Desktop > IP Configuration > Static. All masks are 255.255.255.0.

| Device | IP address | Default gateway |
|---|---|---|
| Server1 | 172.16.1.100 | 172.16.1.1 |
| Server2 | 172.16.1.102 | 172.16.1.1 |
| Larry | 172.16.2.10 | 172.16.2.1 |
| Bob | 172.16.3.10 | 172.16.3.1 |
| Jerry | 172.16.3.9 | 172.16.3.1 |

Packet Tracer: on both servers, check that **Services > HTTP** and **Services > FTP** are on (they are by default).

### Linux host setup (EVE-NG; CML already has this)

Run as root on each host, with that host's own address and gateway from the table above:
```
ip address add 172.16.3.10/24 dev eth0
ip link set dev eth0 up
ip route add default via 172.16.3.1
```
On Server1 and Server2 only, also start a web page on TCP 80 and an FTP-style listener on TCP 21:
```
mkdir -p /tmp/www && echo "Server1 web page" > /tmp/www/index.html
httpd -p 80 -h /tmp/www    # Alpine; on other Linux use: python3 -m http.server 80 --directory /tmp/www &
(while true; do echo "220 Server1 FTP ready" | nc -l -p 21; done) &
```

### Router configuration

Answer `no` to the initial configuration dialog if a router asks, then paste each block into the router's CLI.

**R1**
```
enable
configure terminal
hostname R1
no ip domain-lookup
interface GigabitEthernet0/0
 description MAIN-SITE-LAN
 ip address 172.16.1.1 255.255.255.0
 no shutdown
interface GigabitEthernet0/1
 description to R2
 ip address 172.16.12.1 255.255.255.252
 no shutdown
interface GigabitEthernet0/2
 description to R3
 ip address 172.16.13.1 255.255.255.252
 no shutdown
ip route 172.16.2.0 255.255.255.0 172.16.12.2
ip route 172.16.3.0 255.255.255.0 172.16.13.2
end
copy running-config startup-config
```

**R2**
```
enable
configure terminal
hostname R2
no ip domain-lookup
interface GigabitEthernet0/0
 description LARRY-LAN
 ip address 172.16.2.1 255.255.255.0
 no shutdown
interface GigabitEthernet0/1
 description to R1
 ip address 172.16.12.2 255.255.255.252
 no shutdown
interface GigabitEthernet0/2
 description to R3
 ip address 172.16.23.1 255.255.255.252
 no shutdown
ip route 172.16.1.0 255.255.255.0 172.16.12.1
ip route 172.16.3.0 255.255.255.0 172.16.23.2
end
copy running-config startup-config
```

**R3**
```
enable
configure terminal
hostname R3
no ip domain-lookup
interface GigabitEthernet0/0
 description BOB-JERRY-LAN
 ip address 172.16.3.1 255.255.255.0
 no shutdown
interface GigabitEthernet0/1
 description to R1
 ip address 172.16.13.2 255.255.255.252
 no shutdown
interface GigabitEthernet0/2
 description to R2
 ip address 172.16.23.2 255.255.255.252
 no shutdown
ip route 172.16.1.0 255.255.255.0 172.16.13.1
ip route 172.16.2.0 255.255.255.0 172.16.23.1
end
copy running-config startup-config
```

### Baseline check (before any ACLs)

Everything should reach everything. Don't start Part 2 until all five pass.

| From | Check | Packet Tracer | CML / EVE-NG (Linux host) | Expect |
|---|---|---|---|---|
| Bob | FTP to Server1 | Command Prompt: `ftp 172.16.1.100` (user `cisco`, password `cisco`), then `quit` | `nc -w 3 172.16.1.100 21` | `ftp>` prompt, or the `220` banner |
| Bob | Web to Server1 | Web Browser: `http://172.16.1.100` | `wget -q -O - -T 3 http://172.16.1.100` | Server1's page |
| Larry | Web to Server1 | Web Browser: `http://172.16.1.100` | `wget -q -O - -T 3 http://172.16.1.100` | Server1's page |
| Larry | Ping Bob | `ping 172.16.3.10` | `ping -c 3 172.16.3.10` | Replies (the first may time out while ARP resolves) |
| Jerry | Ping Server2 | `ping 172.16.1.102` | `ping -c 3 172.16.1.102` | Replies |

Use the same commands to prove the work order in Part 2. Save your lab before you start Part 2.

---

## Part 2: Work order

1. Bob can't use FTP on any main-site server. Everything else Bob does keeps working.
2. Larry can't open Server1's website. Larry can still use Server2's website and FTP on both servers.
3. Jerry can't reach anything at the main site.
4. Everyone else keeps full access.

## Questions

1. Which ACL type did you use for each requirement, and why?
2. Which router, interface and direction did you pick for each one? Why there?
3. Prove each requirement with a test from the right host. What did `show ip access-lists` show after the tests?
4. What would happen to the other hosts if you left out the last line of each ACL?

Record your work in [`solution.md`](solution.md).
