# Lab 03b — OSPF Multi-Area

**Topics:** Multi-area OSPF, ABR roles, Type 3 LSAs, inter-area routing  
**Author:** Baltej Giri  
**Repo:** [https://github.com/baltejgiri/my-labs](https://github.com/baltejgiri/my-labs)  
**Reference:** [Arista EOS OSPF Documentation](https://www.arista.com/en/um-eos/eos-open-shortest-path-first-version-2)

---

## Topology

```
R1 (eth1)------10.1.1.0/30 10G------(eth1) R2
|                  Area 0                    |
eth3                                        eth2
|                  Area 1                 Area 2
eth1                                        eth1
R3 (eth2)------12.1.1.0/30 10G------(eth2) R4
```

## Links and Subnets

| Link  | Subnet        | Endpoints                    | Area   | Speed |
|-------|---------------|------------------------------|--------|-------|
| R1—R2 | 10.1.1.0/30   | R1=.1 (eth1), R2=.2 (eth1)  | Area 0 | 10G   |
| R2—R4 | 11.1.1.0/30   | R2=.2 (eth2), R4=.1 (eth1)  | Area 2 | 10G   |
| R1—R3 | 13.1.1.0/30   | R1=.1 (eth3), R3=.2 (eth1)  | Area 1 | 10G   |
| R3—R4 | 12.1.1.0/30   | R3=.2 (eth2), R4=.1 (eth2)  | Area 1 | 10G   |

## Router Roles

| Router | Role            | Interfaces                                  |
|--------|-----------------|---------------------------------------------|
| R1     | ABR             | Area 0 (eth1 → R2) + Area 1 (eth3 → R3)   |
| R2     | ABR             | Area 0 (eth1 → R1) + Area 2 (eth2 → R4)   |
| R3     | Internal Router | Area 1 only                                 |
| R4     | ABR             | Area 1 (eth2 → R3) + Area 2 (eth1 → R2)   |

## Loopbacks

| Router | Loopback    | Area   |
|--------|-------------|--------|
| R1     | 1.1.1.1/32  | Area 0 |
| R2     | 2.2.2.2/32  | Area 0 |
| R3     | 3.3.3.3/32  | Area 1 |
| R4     | 4.4.4.4/32  | Area 1 |

## Question

> **Q: How does R3 reach the 11.1.1.0/30 network in Area 2?**
