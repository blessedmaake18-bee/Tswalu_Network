# Tswalu_Steel_Fabrication_Network

Client: Tswalu Steel Fabrication (Rustenburg) | Industry: Manufacturing
Project ID: CMPG325-2026-029

## Overview

This repository documents the design, implementation, and testing of a
network solution for Tswalu Steel Fabrication, built in Cisco Packet
Tracer.

The network provides connectivity for office, production, server, and
management segments, with a dedicated isolated Guest Wi-Fi network,
and implements IPv6 dual-stack addressing and routing alongside IPv4.

## Client requirements

- Assigned addressing block: `172.30.12.0/23`
- Design constraint: all device administration must be secured
  (previous incident of unauthorized changes)
- Change request (CR3): Guest Wi-Fi added for visitors, isolated from
  internal resources
- Assigned technical challenge: IPv6 (dual-stack addressing & routing)

## Repository structure

| Folder | Contents |
|---|---|
| `docs/` | Requirements analysis, design decisions, reflection |
| `diagrams/` | Physical and logical topology diagrams |
| `ip-addressing/` | IPv4/IPv6 addressing plan and subnet table |
| `packet-tracer/` | The `.pkt` project file |
| `screenshots/` | Configuration and testing evidence |
| `troubleshooting/` | Issues encountered and how they were resolved |

## Network design summary

- Edge router and core switch, dual-stack (IPv4 + IPv6)
- Five VLANs: Management, Office, Production, Servers, Guest (isolated)
- IPv4 addressing from `172.30.12.0/23`, subnetted per VLAN
- IPv6 addressing using the `2001:db8:acad::/48` documentation prefix,
  one `/64` per VLAN
- Guest VLAN isolated from internal VLANs via ACL

## Status

🚧 In progress — Milestone 1 (28 August 2026)

## Author

Blessed Maake 
