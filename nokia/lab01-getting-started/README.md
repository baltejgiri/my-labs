# Lab 01: Hostname Customization, Interface Activation, and Dual-Stack (IPv4/IPv6) Addressing

## Objective

Establish point-to-point Layer 3 routing readiness between two back-to-back nodes
utilizing a model-driven configuration data store. You must successfully navigate the CLI
modes, enable physical and logical structures, allocate appropriate subnets, and prove
bidirectional reachability.

---

## Topology Linkage

* `sw1` interface `ethernet-1/1` is wired back-to-back directly to `sw2` interface `ethernet-1/1`.

---

## Deploying the Lab

From inside the lab directory, spin up the topology:

```bash
sudo containerlab deploy -t topology.clab.yml
```

Containerlab will pull the SR Linux image if not already present locally and bring up both nodes. Startup typically takes under a minute.

---

## Deployment Verification

Once the deploy command completes, confirm both nodes are running:

```bash
sudo containerlab inspect -t topology.clab.yml
```

You should see both `sw1` and `sw2` listed with a `running` state and assigned management IP addresses. If a node is missing or shows a different state, check its container logs:

```bash
docker logs clab-<lab-name>-sw1
```

You can also verify at the Docker level:

```bash
docker ps
```

Both containers should appear. If a container has exited, it will not appear here — use `docker ps -a` to see it and inspect the logs for the cause.

---

## Connecting to Lab Nodes

Connect to either node directly by name from your Linux host:

```bash
ssh admin@clab-<lab-name>-sw1
```

The default credentials are:

* **Username:** `admin`
* **Password:** `NokiaSrl1!`

Alternatively, you can access the CLI through Docker without SSH:

```bash
docker exec -it clab-<lab-name>-sw1 sr_cli
```

Once connected, you will land at the SR Linux prompt:

```
--{ running }--[  ]--
A:admin@sw1#
```

---

## Core Tasks

### Task 1: CLI Entry, Datastore Orientation & State Assessment

SR Linux uses a model-driven architecture with distinct datastores, each serving a different purpose. Before making any changes, you must understand which context you are operating in and what each one exposes.

| Datastore | Purpose |
|-----------|---------|
| `running` | The active configuration currently applied to the system. This is where you land on login. |
| `candidate` | An isolated staging area where changes are prepared before being committed to running. Supports private sessions per user. |
| `state` | Read-only operational data — interface counters, link status, protocol state, and routing table entries as seen by the data plane. |

1. Access the CLI of both `sw1` and `sw2` from your Linux console shell.
2. Determine the default node name and identify the active software release variant.
3. Navigate into the `running` datastore and observe the current system configuration in its default factory state.
4. Navigate into the `state` datastore and inspect the operational status of interface `ethernet-1/1` on both nodes — note whether it is administratively and operationally up or down before any changes are made.
5. Return to the `running` datastore before proceeding to the next task.

### Task 2: Transactional Identity & State Preparation

1. Enter the isolated candidate configuration data store session on both devices.
2. Modify the system name properties:
   * Set node 1 to: `SROS-LAB-SW1`
   * Set node 2 to: `SROS-LAB-SW2`
3. Physically enable administrative operation on interface `ethernet-1/1` across both nodes.

### Task 3: Layer 3 Interface & Protocol Bindings

1. Within subinterface `0` of `ethernet-1/1`, enable both IPv4 and IPv6 families globally.
2. Assign the following parameter blocks to subinterface `0`:
   * **SW1:** `10.10.12.1/24` and `2001:db8:12::1/64`
   * **SW2:** `10.10.12.2/24` and `2001:db8:12::2/64`
3. Bind logical subinterface `ethernet-1/1.0` into the default global routing instance (network-instance) on both switches.

### Task 4: Transactional Validation

1. Perform a structural check on your pending changes to ensure no syntax parameters break system modeling rules.
2. Execute a permanent write transaction to migrate your changes from the private candidate data store to the running state engine.

---

## Lab Management

```bash
# List all running nodes and their management IPs
sudo containerlab inspect -t topology.clab.yml

# Destroy the lab and clean up all containers and links
sudo containerlab destroy -t topology.clab.yml

# Destroy and remove all generated lab files (full reset)
sudo containerlab destroy -t topology.clab.yml --cleanup

# Redeploy after destroy
sudo containerlab deploy -t topology.clab.yml
```