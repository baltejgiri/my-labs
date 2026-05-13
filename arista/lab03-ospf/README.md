# Lab 03 — OSPF on Arista cEOS
**Lab Objective:** Understand OSPF single-area, multi-area, DR/BDR, ABR, route 
summarization, and stub areas using Containerlab. ---
## Topology
- **R1** — Connected to PC1 and R2 - **R2** — Connected to R1 and R3 - **R3** — 
Connected to R2 and R4 (ABR) - **R4** — Connected to R3 and PC2 **Area Design:** - 
Area 0: R1, R2, R3 - Area 1: R3, R4 ---
## Tasks
### Task 1: Basic Configuration
- Configure hostnames on all routers. - Configure all physical interfaces and 
loopback interfaces with correct IP addresses. - Configure PC1 and PC2 with 
correct IP addresses and default gateways. - Verify IP reachability between 
directly connected devices.
### Task 2: Single Area OSPF (Area 0)
- Configure OSPF process 1 on all routers. - Place all interfaces (including R3-R4 
link) into Area 0. - Set router-ids using loopback addresses. - Verify OSPF 
neighbor relationships. - Verify that all routers have full OSPF routes. - Test 
end-to-end connectivity (PC1 to PC2).
### Task 3: DR/BDR Election
- Check the current DR/BDR status on the R1-R2 link. - Modify OSPF priority to 
influence DR/BDR election. - Verify the new DR/BDR after changes. - Observe 
behavior when priority is set to 0.
### Task 4: Multi-Area OSPF
- Reconfigure OSPF so that: - R1, R2, and R3's link toward R2 remain in **Area 
  0**. - R3's link toward R4 and R4 itself are in **Area 1**.
- Verify R3 is acting as an ABR. - Check routing tables on R1 and R4. - Confirm 
that routes from Area 0 appear as `O IA` on R4 and vice versa. - Verify end-to-end 
connectivity is maintained.
### Task 5: Inter-Area Route Summarization
- On the ABR (R3), configure inter-area route summarization for Area 1 networks. - 
Verify the summary route appears on routers in Area 0. - Check the effect on the 
routing table and OSPF database. - Remove the summary and observe the change. --- 
**End of Tasks**
