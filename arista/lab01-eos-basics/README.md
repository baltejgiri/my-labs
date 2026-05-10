# Lab 01 — Arista EOS Basics
**Topics:** CLI Navigation, Management,  LLDP, ARP, ICMP  
**Platform:** ContainerLab and Arista cEOS  
**Repo:** https://github.com/baltejgiri/my-labs/arista/lab01-eos-basics  
**Reference:** [Arista EOS User Manual](https://www.arista.com/assets/data/pdf/user-manual/EOS-User-Manual.pdf)

---

## Topology

```
[PC1]──eth1──Ethernet1──[SW1]──Ethernet2──Ethernet1──[SW2]──Ethernet2──eth1──[PC2]
10.0.0.10/24            10.0.0.1/24  10.1.0.1/30  10.1.0.2/30  10.0.0.1/24  10.0.0.20/24
```

| Node | Role         | Data IP       | Mgmt IP     |
|------|--------------|---------------|-------------|
| SW1  | Arista cEOS  | Eth1: 10.0.0.1/24 / Eth2: 10.1.0.1/30 | 192.168.1.1 |
| SW2  | Arista cEOS  | Eth1: 10.1.0.2/30 / Eth2: 10.0.0.1/24 | 192.168.1.2 |
| PC1  | Linux (Alpine) | 10.0.0.10/24 | 192.168.1.10 |
| PC2  | Linux (Alpine) | 10.0.0.20/24 | 192.168.1.11 |


## Prerequisites

1. ContainerLab installed on Linux host
2. Arista cEOS image imported into Docker:
   ```bash
   docker import cEOS-lab-4.32.0F.tar ceos:latest
   docker image ls | grep ceos   # verify
   ```


## Deploy & Destroy

```bash
# Deploy
sudo containerlab deploy -t lab01-eos-basics.yml

# Verify nodes are running
sudo containerlab inspect -t lab01-eos-basics.clab.yml

# Access SW1 CLI
docker exec -it clab-lab01-eos-basics-sw1 Cli

# Access SW2 CLI
docker exec -it clab-lab01-eos-basics-sw2 Cli

# Access PC1 shell
docker exec -it clab-lab01-eos-basics-pc1 sh

# Destroy (keep configs)
sudo containerlab destroy -t lab01-eos-basics.yml

# Destroy + wipe configs
sudo containerlab destroy -t lab01-eos-basics.yml --cleanup
```

## Learning Tasks

### Task 1 — EOS CLI Navigation

Connect to SW1 and practice moving between CLI modes.

```
# You start in User EXEC mode
SW1>

# Enter Privileged EXEC (enable) mode
SW1> enable
SW1#

# Enter Global Configuration mode
SW1# configure terminal
SW1(config)#

# Enter Interface config mode
SW1(config)# interface Ethernet1
SW1(config-if-Et1)#

# Go back one level
SW1(config-if-Et1)# exit

# Go all the way back to Privileged EXEC
SW1(config)# end
SW1#
```

**Key EOS CLI differences from Cisco IOS to note:**
- `show` commands work in config mode (no need to exit first)
- Tab completion and `?` work the same way
- `no` negates any command
- `write memory` or `copy running-config startup-config` to save

### Task 2 — Verify System Information

```bash
# Show EOS version, model, serial, uptime
SW1# show version

# Show running configuration
SW1# show running-config

# Show all interfaces and their status
SW1# show interfaces status

# Show specific interface detail
SW1# show interfaces Ethernet1

# Show IP interfaces
SW1# show ip interface brief

# Show management interface
SW1# show interfaces Management0
```

**Expected output for `show ip interface brief`:**
```
                                                                        Address
Interface       IP Address          Status      Protocol        MTU     Mask    
Ethernet1       10.0.0.1/24         up          up              1500            
Ethernet2       10.1.0.1/30         up          up              1500            
Management0     172.20.20.5/24      up          up              1500            
```

### Task 3 — LLDP Verification

LLDP (Link Layer Discovery Protocol) allows adjacent devices to advertise their identity and capabilities. SW1 and SW2 are connected via Ethernet2/Ethernet1.

```bash
# Show all LLDP neighbors
SW1# show lldp neighbors

# Show detailed LLDP neighbor info (includes capabilities, mgmt IP)
SW1# show lldp neighbors detail

# Show LLDP info for a specific interface
SW1# show lldp neighbors Ethernet2

# Show local LLDP information this device is advertising
SW1# show lldp local-info
```

**Expected `show lldp neighbors` output on SW1:**
```
Last table change time   : 0:00:15 ago
Number of table inserts  : 1
Number of table deletes  : 0
Number of table drops    : 0
Number of table age-outs : 0

Port       Neighbor Device ID      Neighbor Port ID    TTL
Et2        SW2                     Ethernet1           40
Ma0        SW2                     Management0         40
```

**Questions to answer:**
- What is SW2's system capability as reported by LLDP?
- What management IP does SW2 advertise?
- What happens to LLDP neighbors if you shut Ethernet2 on SW1?

### Task 4 — ARP Table

ARP (Address Resolution Protocol) maps IP addresses to MAC addresses at Layer 2.

```bash
# On SW1 — view ARP table (initially empty for data interfaces)
SW1# show arp

# Trigger ARP by pinging PC1's gateway from SW1
SW1# ping 10.0.0.10

# Now check the ARP table again
SW1# show arp

# Show ARP for a specific interface
SW1# show arp interface Ethernet1

# Clear ARP table (use carefully in production!)
SW1# clear arp-cache
```

**Expected ARP output after pinging PC1:**
```
Address         Age (min)  Hardware Addr   Interface
10.0.0.10       0:00:05    aa:bb:cc:dd:ee:ff  Ethernet1
```
Note: PC1's MAC address shown above is generic for demonstration purpose. The actual address MAC address will be different.

**On PC1 — view ARP from the host side:**
```bash
# Enter PC1 shell
docker exec -it clab-lab01-eos-basics-pc1 sh

# Show ARP table on PC1
arp -n

# Expected: SW1's Ethernet1 MAC mapped to 10.0.0.1
```

### Task 5 — ICMP Reachability Testing

Test end-to-end connectivity using ping and traceroute.

```bash
# --- On SW1 ---

# Ping SW2's Ethernet1 (directly connected)
SW1# ping 10.1.0.2

# Ping PC2 (requires SW1 to have route to 10.0.0.0/24 via SW2)
SW1# ping 10.0.0.20

# Ping with specific source interface
SW1# ping 10.0.0.20 source Ethernet2

# Ping with repeat count and size
SW1# ping 10.0.0.20 repeat 10 size 1400

# Traceroute to PC2
SW1# traceroute 10.0.0.20
```

**Note:** PC1→PC2 ICMP will work because SW1 has a directly connected route to 10.0.0.0/24 and SW2 has a static route back. Confirm with:

```bash
SW1# show ip route
SW2# show ip route
```

**On PC1 — ping across to PC2:**
```bash
docker exec -it clab-lab01-eos-basics-pc1 sh
ping 10.0.0.20 -c 5
traceroute 10.0.0.20
```

### Task 6 — Useful Show Commands Reference

| Command | What it shows |
|---|---|
| `show version` | EOS version, uptime, model, flash |
| `show running-config` | Active configuration in memory |
| `show startup-config` | Config saved to flash |
| `show interfaces status` | Port status table (up/down/err-disabled) |
| `show interfaces Ethernet1` | Full stats for one interface |
| `show ip interface brief` | IP addresses and status summary |
| `show lldp neighbors` | Directly connected LLDP peers |
| `show lldp neighbors detail` | Full LLDP detail per neighbor |
| `show arp` | ARP table (IP to MAC mappings) |
| `show ip route` | Routing table |
| `show mac address-table` | L2 MAC table (if using switchport mode) |
| `show logging` | System log buffer |
| `show clock` | System time |
| `show environment` | Hardware health (fans, temp, power) |


### Task 7 — Configuration Practice

Try making these changes live on SW1 to build EOS config muscle memory:

```bash
# 1. Change the hostname
SW1(config)# hostname SW1-LAB01

# 2. Add a description to an interface
SW1(config)# interface Ethernet1
SW1(config-if-Et1)# description CONNECTED-TO-PC1

# 3. Set a DNS name server
SW1(config)# ip name-server vrf MGMT 8.8.8.8

# 4. Set system clock timezone
SW1(config)# clock timezone Canada/Pacific

# 5. Save configuration
SW1# write memory
```

## Troubleshooting Scenarios

Try breaking and fixing these — great practice for the TSE interview Round 3.

**Scenario A — Interface Down**
```bash
# Shut Ethernet2 on SW1
SW1(config)# interface Ethernet2
SW1(config-if-Et2)# shutdown

# Observe: LLDP neighbor disappears, ping to SW2 fails
SW1# show lldp neighbors
SW1# ping 10.1.0.2

# Fix it
SW1(config-if-Et2)# no shutdown
```

**Scenario B — Wrong IP / Subnet Mismatch**
```bash
# Misconfigure SW1 Ethernet2 with wrong IP
SW1(config)# interface Ethernet2
SW1(config-if-Et2)# ip address 10.1.0.5/30   ! wrong — not same subnet as SW2's 10.1.0.2/30

# Ping will fail — diagnose using:
SW1# show ip interface brief
SW1# show arp
SW1# ping 10.1.0.2

# Fix:
SW1(config-if-Et2)# ip address 10.1.0.1/30
```

**Scenario C — LLDP Disabled**
```bash
# Disable LLDP globally
SW1(config)# no lldp run

# Observe neighbours disappear after holdtime expires (40 sec)
SW1# show lldp neighbors

# Re-enable
SW1(config)# lldp run
```

## Key Takeaways

- EOS CLI is very similar to Cisco IOS but with some important differences (show in config mode, `write memory`, interface naming `Ethernet1` not `Gi0/0/1`)
- LLDP is enabled by default on EOS — it's your first visibility tool for physical topology
- ARP table + `show ip interface brief` + `show interfaces status` are your Layer 2/3 baseline commands
- The management VRF (`MGMT`) isolates management traffic — always verify you're in the right VRF context when pinging from management
- `ping` on EOS sources from the lowest-numbered interface by default — use `source` to control this

---

## Next Lab

**Lab 02 — VLANs, STP/RSTP/MSTP, and Layer 2 Switching**  
Builds on this foundation by adding Layer 2 switching, trunk ports, and spanning tree.