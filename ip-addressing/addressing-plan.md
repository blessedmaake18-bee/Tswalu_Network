# IP Addressing Plan

## Assigned block

`172.30.12.0/23` (172.30.12.0 – 172.30.13.255, 512 addresses)

Subnetted into five `/26` blocks (62 usable hosts each), leaving spare
capacity for future growth.

## IPv4 addressing table

| VLAN | Name | Subnet | Usable range | Gateway | Purpose |
|---|---|---|---|---|---|
| 10 | Management | 172.30.12.0/26 | .1 – .62 | 172.30.12.1 | Switch/router admin interfaces only |
| 20 | Office | 172.30.12.64/26 | .65 – .126 | 172.30.12.65 | Staff PCs, administration |
| 30 | Production | 172.30.12.128/26 | .129 – .190 | 172.30.12.129 | Manufacturing floor devices |
| 40 | Servers | 172.30.12.192/26 | .193 – .254 | 172.30.12.193 | File and application servers |
| 99 | Guest | 172.30.13.0/26 | .1 – .62 | 172.30.13.1 | Visitor Wi-Fi, isolated |

**Spare capacity: ** 172.30.13.64/26 – 172.30.13.192/26 (unused, reserved
for future growth).

## IPv6 addressing table (dual stack)

Using the reserved documentation prefix `2001:db8:acad::/48`, one `/64`
per VLAN:

| VLAN | Name | IPv6 prefix |
|---|---|---|
| 10 | Management | 2001:db8:acad:10::/64 |
| 20 | Office | 2001:db8:acad:20::/64 |
| 30 | Production | 2001:db8:acad:30::/64 |
| 40 | Servers | 2001:db8:acad:40::/64 |
| 99 | Guest | 2001:db8:acad:99::/64 |

## Notes

- Each router sub-interface (or router-on-a-stick VLAN interface) is
  configured with both an IPv4 address from its VLAN's `/26` and an
  IPv6 address from its matching `/64` — this is the dual-stack
  implementation for the assigned technical challenge.
- The Guest VLAN (99) has no inter-VLAN routing permitted to VLANs 10,
  20, 30, or 40, satisfying change request CR3.
- Management VLAN (10) access is restricted via ACL to satisfy the
  secure-administration design constraint.
