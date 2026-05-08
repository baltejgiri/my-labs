# Arista L3 Leaf-Spine Starter Lab

## Topology Diagram
```text
        [Spine1]      [Spine2]
          /  \          /  \
         /    \        /    \
      [Leaf1]      [Leaf2]
```

## IP Plan (L3 Point-to-Point)
- Spine1 Et1 <-> Leaf1 Et1: 10.0.0.0/31
- Spine1 Et2 <-> Leaf2 Et1: 10.0.0.2/31
- Spine2 Et1 <-> Leaf1 Et2: 10.0.0.4/31
- Spine2 Et2 <-> Leaf2 Et2: 10.0.0.6/31

## Verification Commands
- `show lldp neighbors`
- `show ip interface brief`
- `ping 10.0.0.x`
