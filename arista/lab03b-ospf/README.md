# Lab 03b — OSPF Multi-Area

**Topics:** Multi-area OSPF, ABR roles, inter-area routing
**Author:** Baltej Giri
**Repo:** [https://github.com/baltejgiri/my-labs](https://github.com/baltejgiri/my-labs)  
**Reference:** [Arista EOS OSPF Documentation](https://www.arista.com/en/um-eos/eos-open-shortest-path-first-version-2)

---

## Topology

![Lab 03b - OSPF Topology](/arista/lab03b-ospf/lab03b-ospf.clab.png)

## Links and Subnets

| Link  | Subnet        | Endpoints                    | Area   | Speed |
|-------|---------------|------------------------------|--------|-------|
| R1—R2 | 10.1.1.0/30   | R1=.1 (eth1), R2=.2 (eth1)  | Area 0 | 1G   |
| R2—R4 | 11.1.1.0/30   | R2=.2 (eth2), R4=.1 (eth1)  | Area 2 | 1G   |
| R1—R3 | 13.1.1.0/30   | R1=.1 (eth3), R3=.2 (eth1)  | Area 1 | 1G   |
| R3—R4 | 12.1.1.0/30   | R3=.2 (eth2), R4=.1 (eth2)  | Area 1 | 1G   |

## Loopbacks

| Router | Loopback    | Area   |
|--------|-------------|--------|
| R1     | 1.1.1.1/32  | Area 0 |
| R2     | 2.2.2.2/32  | Area 0 |
| R3     | 3.3.3.3/32  | Area 1 |
| R4     | 4.4.4.4/32  | Area 1 |

## Question

> **Q1: What path does R3 reach the 11.1.1.0/30 network in Area 2?**

> **Q2: Why can't R3 directly send traffic to R4?**

> **Q3: What are the roles of each router in this topology?**
