# Tswalu Steel Fabrication – Network Infrastructure

## Project Overview

This project presents the design, implementation, configuration, and testing of a Cisco Packet Tracer network for Tswalu Steel Fabrication (Rustenburg).

The network was designed to provide segmented connectivity for different organisational departments while implementing IPv4 and IPv6 dual-stack networking.

## Client

**Organisation:** Tswalu Steel Fabrication (Rustenburg)

**Industry:** Manufacturing

**Project:** CMPG325 Computer Networks

**Technical Challenge:** IPv6 Dual-Stack Addressing and Routing

**Change Request:** Guest Wi-Fi for visitors, isolated from internal resources.

## Network Design

The network uses a router-on-a-stick architecture with a single Cisco switch connected to the router using an IEEE 802.1Q trunk.

Five VLANs are implemented:

| VLAN | Name                        | IPv4 Network     | IPv6 Prefix           |
| ---- | --------------------------- | ---------------- | --------------------- |
| 10   | Management & Administration | 172.30.12.64/27  | 2001:DB8:ACAD:10::/64 |
| 20   | Production                  | 172.30.12.0/26   | 2001:DB8:ACAD:20::/64 |
| 30   | Finance & HR                | 172.30.12.128/28 | 2001:DB8:ACAD:30::/64 |
| 40   | IT & Services               | 172.30.12.144/28 | 2001:DB8:ACAD:40::/64 |
| 50   | Guest Wi-Fi                 | 172.30.12.96/27  | 2001:DB8:ACAD:50::/64 |

## Default Gateways

| VLAN | IPv4 Gateway  | IPv6 Gateway        |
| ---- | ------------- | ------------------- |
| 10   | 172.30.12.65  | 2001:DB8:ACAD:10::1 |
| 20   | 172.30.12.1   | 2001:DB8:ACAD:20::1 |
| 30   | 172.30.12.129 | 2001:DB8:ACAD:30::1 |
| 40   | 172.30.12.145 | 2001:DB8:ACAD:40::1 |
| 50   | 172.30.12.97  | 2001:DB8:ACAD:50::1 |

## Physical Topology

The topology consists of:

* Cisco 1941 router (R1)
* Cisco switch (SW1)
* PC-Admin
* PC-Office
* PC-Production
* PC-Finance
* PC-IT
* SRV1
* AP-Guest
* Laptop-Guest

## Configuration

Router-on-a-stick inter-VLAN routing is implemented on R1 using GigabitEthernet0/0 subinterfaces.

The switch trunk connects SW1 GigabitEthernet0/1 to the router.

Access ports are assigned to the appropriate VLANs according to the network design.

## IPv6

IPv6 routing is enabled on R1 using:

`ipv6 unicast-routing`

Each VLAN has a dedicated IPv6 /64 prefix and default gateway.

IPv6 connectivity was verified using ICMPv6 ping tests between gateways, same-VLAN hosts, and hosts across different VLANs.

## Guest Network

VLAN 50 is dedicated to guest wireless access.

The guest network provides connectivity to the guest gateway while preventing guest devices from accessing internal network resources.

Guest isolation is implemented using IPv4 and IPv6 ACLs on the Guest VLAN subinterface on R1.

## Testing and Verification

Network operation was verified using:

* `show vlan brief`
* `show interfaces trunk`
* `show ip interface brief`
* `show ipv6 interface brief`
* IPv4 ping tests
* IPv6 ping tests
* Inter-VLAN connectivity tests
* SSH authentication tests
* Guest isolation tests
* HTTP access tests

The following connectivity was verified:

* IPv4 gateway, same-VLAN, and inter-VLAN connectivity
* IPv6 gateway, same-VLAN, and inter-VLAN connectivity
* SSH access to R1 and SW1 using valid credentials
* Rejection of invalid SSH credentials
* Telnet access blocked because remote administration is SSH-only
* Guest access to the Guest VLAN gateway
* Guest access to internal VLANs blocked over IPv4 and IPv6
* PC-Admin access to SRV1 over HTTP
* Laptop-Guest access to SRV1 blocked over HTTP

Successful connectivity tests produced 0% packet loss where connectivity was expected.

## Security

Device administration on R1 and SW1 was secured using:

* Enable secret
* Local user authentication
* `login local`
* SSH-only remote access using `transport input ssh`
* SSH version 2
* Password encryption
* Executive session timeout

Telnet access is disabled.

Configuration changes were saved to startup configuration.

## Evidence

Supporting evidence is organised in the following folders:

* `configurations/` – router and switch configuration exports
* `diagrams/` – network topology diagrams
* `docs/` – project documentation and reflection
* `ip-addressing/` – IPv4 and IPv6 addressing plan
* `packet-tracker/` – Cisco Packet Tracer project
* `screenshots/` – configuration, connectivity, security, and guest-isolation evidence
* `troubleshooting/` – troubleshooting notes and diagnostic evidence

## Packet Tracer

The completed Cisco Packet Tracer implementation is available in:

`packet-tracker/Tswalu_Network.pkt`

## Project Outcome

The completed network provides segmented departmental connectivity, IPv4 and IPv6 dual-stack routing, secured network-device administration, and a dedicated guest wireless network isolated from internal resources.

The implementation was tested in Cisco Packet Tracer and the required connectivity, IPv6 functionality, security controls, and guest-isolation requirements were verified.
