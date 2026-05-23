# Lab 01: Hostname Customization, Interface Activation, and Dual-Stack (IPv4/IPv6) 
# Addressing
## Objective
Establish point-to-point Layer 3 routing readiness between two back-to-back nodes 
utilizing a model-driven configuration data store. You must successfully navigate the CLI 
modes, enable physical and logical structures, allocate appropriate subnets, and prove 
bidirectional reachability.
## Topology Linkage
* `sw1` interface `ethernet-1/1` is wired back-to-back directly to `sw2` interface 
`ethernet-1/1`.
## Core Tasks
### Task 1: CLI Entry & State Assessment
1. Access the CLI of both `sw1` and `sw2` from your Linux console shell. 2. Determine the 
default node name and identify the active software release variant. 3. Check the current 
status of physical link `ethernet-1/1` on both boxes before modifying any state data.
### Task 2: Transactional Identity & State Preparation
1. Enter the isolated candidate configuration data store session on both devices. 2. 
Modify the system name properties:
   * Set node 1 to: `SROS-LAB-SW1` * Set node 2 to: `SROS-LAB-SW2` 3. Physically enable 
administrative operation on interface `ethernet-1/1` across both nodes.
### Task 3: Layer 3 Interface & Protocol Bindings
1. Within subinterface `0` of `ethernet-1/1`, enable both IPv4 and IPv6 families globally. 
2. Assign the following parameter blocks to subinterface `0`:
   * **SW1:** `10.10.12.1/24` and `2001:db8:12::1/64` * **SW2:** `10.10.12.2/24` and 
   `2001:db8:12::2/64`
3. Bind logical subinterface `ethernet-1/1.0` into the default global routing instance 
(network-instance) on both switches.
### Task 4: Transactional Validation
1. Perform a structural check on your pending changes to ensure no syntax parameters break 
system modeling rules.
2. Execute a permanent write transaction to migrate your changes from the private candidate data store to the running state engine.
