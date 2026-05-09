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

## BGP Configuration (eBGP Underlay)
- **Spines:** AS 65001
- **Leaf1:** AS 65101 (Network: 10.10.10.0/24)
- **Leaf2:** AS 65102 (Network: 10.10.20.0/24)

## Advanced Verification
- **Check BGP Neighbors:** `show ip bgp summary` (Should be 'Established')
- **Verify ECMP:** `show ip route 10.10.20.0` (Should show two paths)
- **Check BGP Table:** `show ip bgp`
