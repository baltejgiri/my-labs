# Lab 04 — BGP Basics

**Author:** Baltej Giri  
**Repo:** [https://github.com/baltejgiri/my-labs](https://github.com/baltejgiri/my-labs)  
**Reference:** [Arista EOS BGP Documentation](https://www.arista.com/en/um-eos/eos-border-gateway-protocol)

---

## Topology

```
[Lo0: 192.168.1.1]                    [Lo0: 192.168.2.2]
       R1 (.1)------10.12.1.0/24------(.2) R2
      AS 65100                            AS 65200
```

---

## Links and Addressing

| Link  | Subnet       | Endpoints                   |
|-------|--------------|-----------------------------|
| R1—R2 | 10.12.1.0/24 | R1=.1 (eth1), R2=.2 (eth1) |

| Router | Loopback       | ASN   |
|--------|----------------|-------|
| R1     | 192.168.1.1/32 | 65100 |
| R2     | 192.168.2.2/32 | 65200 |

---

## BGP Session

| Type | R1 | R2 |
|------|----|----|
| eBGP | AS 65100 | AS 65200 |
| Neighbour IP | 10.12.1.2 | 10.12.1.1 |
| Advertises | 192.168.1.1/32 | 192.168.2.2/32 |

---

## Cisco vs Arista

| Cisco IOS | Arista EOS |
|-----------|------------|
| `network X.X.X.X mask Y.Y.Y.Y` | `network X.X.X.X/prefix` |
| `bgp router-id X.X.X.X` | `router-id X.X.X.X` |
| `show bgp ipv4 unicast summary` | `show bgp summary` |
| `show bgp ipv4 unicast neighbors X` | `show bgp neighbors X` |
| Not required | `service routing protocols model multi-agent` |

> **Important:** `service routing protocols model multi-agent` is required on Arista EOS before configuring BGP. Without it BGP will not function correctly. This must be the first line in the configuration.

---

## Learning Objectives

1. Understand eBGP session establishment between two different ASNs
2. Verify BGP neighbour states progress to Established
3. Advertise loopback prefixes into BGP
4. Verify routes are received and installed in the routing table
5. Understand BGP router-id selection and best practice

---

## Verification Commands

```bash
# BGP session summary — confirm Established and prefixes received
show bgp summary

# Detailed neighbour information
show bgp neighbors 10.12.1.2

# Routes advertised to the peer
show bgp neighbors 10.12.1.2 advertised-routes

# Routes received from the peer
show bgp neighbors 10.12.1.2 received-routes

# Full BGP table
show ip bgp

# BGP routes in the routing table
show ip route bgp
```

---

## Expected Output

```
R1# show bgp summary

Neighbor    AS      MsgRcvd  MsgSent  Up/Down    State/PfxRcd
10.12.1.2   65200   5        5        00:01:23   1
```

`1` in State/PfxRcd confirms R1 received one prefix from R2 — the 192.168.2.2/32 loopback.

```
R1# show ip route bgp
B E   192.168.2.2/32 [200/0] via 10.12.1.2, Ethernet1
```

`B E` = BGP eBGP route installed in the routing table.

---

## Break/Fix Scenarios

| Scenario | Break | Expected Symptom | Fix |
|----------|-------|------------------|-----|
| A | Wrong ASN on neighbour statement | BGP OPEN error — session drops | Correct ASN to 65200 |
| B | Wrong neighbour IP | BGP stuck in Active state | Correct IP to 10.12.1.2 |
| C | Authentication mismatch | Session drops — MD5 failure | Match password on both sides |
| D | Remove network advertisement | Peer loses the prefix | Re-add network statement |

---

## BGP Neighbour States — Reference

| State | Meaning |
|-------|---------|
| Idle | BGP not started or TCP failed |
| Connect | TCP SYN sent, waiting for response |
| Active | TCP failed, retrying — most common stuck state |
| OpenSent | BGP OPEN message sent, waiting for peer OPEN |
| OpenConfirm | OPEN received, waiting for KEEPALIVE |
| Established | ✅ Session up — UPDATE messages flowing |

> **Stuck in Active** is the most common BGP issue — always check: wrong neighbour IP, wrong ASN, ACL blocking TCP port 179, or no route to peer.