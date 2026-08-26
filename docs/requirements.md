# Client Requirements Analysis

## Client

**Organisation:** Tswalu Steel Fabrication (Rustenburg)
**Industry: ** Manufacturing
**Client ID: ** CLI-029

## Business context

Tswalu Steel Fabrication requires a network that supports its office
staff, manufacturing floor operations, and internal servers, while
also providing safe internet access for visiting guests. A previous
incident of unauthorised changes to network devices means secure
administration is a priority for this project.

## Requirements

1. **Addressing: ** the network must be built on the assigned block
   `172.30.12.0/23`, subnetted to serve distinct departments/functions.
2. **Connectivity: ** provide appropriate connectivity and network
   services across office, production, and server segments.
3. **Design constraint — secure administration: ** all device
   administration must be secured. This is addressed by placing
   router/switch management interfaces on a dedicated Management VLAN,
   using SSH (not Telnet) for remote access, and restricting management
   access via ACL.
4. **Change request CR3 — Guest Wi-Fi: ** a Guest Wi-Fi network must be
   added for visitors, fully isolated from internal resources. This is
   addressed with a dedicated Guest VLAN with no inter-VLAN routing to
   internal segments.
5. **Assigned technical challenge — IPv6 dual-stack: ** the network must
   run IPv4 and IPv6 side by side, with dual-stack addressing and
   routing configured, verified, and demonstrated.

## Departments / network segments identified

| Segment | Purpose |
|---|---|
| Management | Router/switch administration only |
| Office | Staff PCs, general administration |
| Production | Manufacturing floor devices |
| Servers | File and application servers |
| Guest | Visitor Wi-Fi, isolated from all internal segments |

## Design implication

These requirements translate into a five-VLAN network behind a
dual-stack edge router and core switch, detailed further in
`diagrams/` and `ip-addressing/`.
