# Tswalu Steel Fabrication – Dual-Stack Network (IPv4/IPv6)

**CMPG325 Computer Networks – Individual Project**
**Student:** Blessed Maake (41432363) | **Project ID:** CMPG325-2026-029 | **Client ID:** CLI-029

A segmented, dual-stack (IPv4 and IPv6) network for Tswalu Steel Fabrication, designed, built and tested in Cisco Packet Tracer 9.0.1. It includes secured device administration and an isolated Guest Wi-Fi network.

| Item | Detail |
|---|---|
| Client | Tswalu Steel Fabrication (Rustenburg) – Manufacturing |
| Technical challenge | IPv6 dual-stack addressing and routing |
| Change request | Guest Wi-Fi for visitors, isolated from internal resources |
| Security requirement | Protected device administration |
| Assigned IPv4 block | `172.30.12.0/23` |
| IPv6 design prefix | `2001:DB8:ACAD::/48` |
| Tool | Cisco Packet Tracer 9.0.1 |
| Demo video | Uploaded separately on the school portal |
| Reports | Milestone 1 design document and Milestone 2 implementation report in `docs/` |

## Contents

1. [Quick start for markers](#quick-start-for-markers)
2. [Requirements and where they are proven](#requirements-and-where-they-are-proven)
3. [Network design](#network-design)
4. [IPv6 dual-stack (technical challenge)](#ipv6-dual-stack-technical-challenge)
5. [Secure administration](#secure-administration)
6. [Guest Wi-Fi isolation](#guest-wi-fi-isolation)
7. [Testing and verification](#testing-and-verification)
8. [Troubleshooting summary](#troubleshooting-summary)
9. [Limitations and future improvements](#limitations-and-future-improvements)
10. [Repository structure](#repository-structure)
11. [Project outcome](#project-outcome)

---

## Quick start for markers

1. Open `packet-tracker/Tswalu_Network.pkt` in Cisco Packet Tracer 9.0.1.
2. Open the CLI tab of R1 or SW1 and run the commands in [Testing](#testing-and-verification).
3. Administrator login for R1 and SW1: username `netadmin`. The password is given in the submission document.
4. Remote administration is SSH only. Example from PC-Admin: `ssh -l netadmin 172.30.12.65` (R1) or `ssh -l netadmin 172.30.12.66` (SW1).
5. Suggested demo order: VLANs and trunk, IPv6 routing, pings, SSH, guest isolation.

---

## Requirements and where they are proven

| Requirement | How it is met | Evidence |
|---|---|---|
| Connectivity between departments | Five VLANs, inter-VLAN routing on R1 | Ping screenshots in `screenshots/`, implementation report |
| Use `172.30.12.0/23` | VLSM subnets for every VLAN | `ip-addressing/`, tables below |
| IPv4 and IPv6 together | Dual-stack sub-interfaces on R1 | `show ip interface brief`, `show ipv6 interface brief` |
| IPv6 routing between segments | `ipv6 unicast-routing`, connected /64 routes | `show ipv6 route`, implementation report |
| Secure device administration | Local login, SSH only, hashed secrets, timeouts | Implementation report, `configurations/` |
| Separate guest network | VLAN 50 with its own subnet and prefix | Addressing tables |
| Guests cannot reach internal resources | IPv4 and IPv6 ACLs on `Gi0/0.50` | ACL match counters, implementation report |
| Working Packet Tracer implementation | `Tswalu_Network.pkt` | `packet-tracker/` |

---

## Network design

- **Router-on-a-stick:** one router (R1, Cisco 1941) and one switch (SW1) joined by an IEEE 802.1Q trunk.
- R1 uses sub-interfaces `Gi0/0.10` to `Gi0/0.50`, one per VLAN.
- Each sub-interface holds the IPv4 and IPv6 gateway for its VLAN.

**Why these choices**
- Router-on-a-stick: one router and one trunk is enough for five VLANs and keeps the design simple.
- VLSM: each VLAN gets only the address space it needs.
- One /64 per VLAN: standard IPv6 practice, easy to read and route.
- ACLs on R1: all inter-VLAN traffic passes through R1, so it is the one place to enforce guest isolation.

| VLAN | Name | IPv4 network | IPv4 gateway | IPv6 prefix | IPv6 gateway |
|---|---|---|---|---|---|
| 10 | Management & Administration | 172.30.12.64/27 | 172.30.12.65 | 2001:DB8:ACAD:10::/64 | 2001:DB8:ACAD:10::1 |
| 20 | Production | 172.30.12.0/26 | 172.30.12.1 | 2001:DB8:ACAD:20::/64 | 2001:DB8:ACAD:20::1 |
| 30 | Finance & HR | 172.30.12.128/28 | 172.30.12.129 | 2001:DB8:ACAD:30::/64 | 2001:DB8:ACAD:30::1 |
| 40 | IT & Services | 172.30.12.144/28 | 172.30.12.145 | 2001:DB8:ACAD:40::/64 | 2001:DB8:ACAD:40::1 |
| 50 | Guest Wi-Fi | 172.30.12.96/27 | 172.30.12.97 | 2001:DB8:ACAD:50::/64 | 2001:DB8:ACAD:50::1 |

**Why only part of the /23 is used:** the plan needs about `172.30.12.0` – `.158`. The rest is kept as spare room for growth.

### Devices and addresses

| Device | IPv4 | IPv6 |
|---|---|---|
| R1 (Cisco 1941) | gateways above | gateways above |
| SW1 (management, VLAN 10) | 172.30.12.66 /27 | – |
| PC-Admin | 172.30.12.67 | 2001:DB8:ACAD:10::10 |
| PC-Office | 172.30.12.68 | 2001:DB8:ACAD:10::11 |
| PC-Production | 172.30.12.2 | 2001:DB8:ACAD:20::10 |
| PC-Finance | 172.30.12.130 | 2001:DB8:ACAD:30::10 |
| PC-IT | 172.30.12.146 | 2001:DB8:ACAD:40::10 |
| SRV1 | 172.30.12.147 | 2001:DB8:ACAD:40::11 |
| Laptop-Guest (via AP-Guest) | 172.30.12.99 | 2001:DB8:ACAD:50::10 |

### Switch ports

- A trunk port on SW1 connects to R1 `GigabitEthernet0/0` and carries VLANs 10, 20, 30, 40 and 50.
- Access ports are assigned to the VLANs per host. The exact port numbers are in `configurations/Switch_SW1_Config.txt`.

### Diagrams

The physical and logical topology diagrams are in `diagrams/`.

---

## IPv6 dual-stack (technical challenge)

- `ipv6 unicast-routing` is enabled on R1.
- Each sub-interface has a /64 gateway address from `2001:DB8:ACAD::/48`.
- All five /64 networks appear as connected routes, so no static routes are needed.
- Verified with `show ipv6 interface brief`, `show ipv6 route` and ping tests between gateways, same-VLAN hosts and hosts in other VLANs.
- TTL shows the path: **128** = same VLAN (no router hop), **127** = routed through R1.

---

## Secure administration

| Control | Purpose |
|---|---|
| `enable secret` | Hashed privileged-mode password |
| `username netadmin secret ...` | Local admin account |
| `service password-encryption` | Hides remaining plain-text passwords |
| `ip domain-name` + RSA 2048-bit key | Required for SSH |
| `ip ssh version 2` | Secure SSH version |
| `login local` on console and VTY | Login required on every line |
| `transport input ssh` | Telnet blocked |
| `exec-timeout 5 0` | Idle sessions end after 5 minutes |

Applied to both R1 and SW1. All VTY lines (0–4 and 5–15) are secured.

---

## Guest Wi-Fi isolation

- VLAN 50 is the guest network, served by AP-Guest.
- Two ACLs are applied inbound on R1 `Gi0/0.50`. Both deny guest traffic to VLANs 10, 20, 30 and 40.
- The IPv6 ACL is needed so guests cannot bypass the IPv4 block by using IPv6.
- Match counters in `show access-lists` and `show ipv6 access-list` show the ACLs are actively filtering.

```text
ip access-list extended GUEST-ISOLATION
 deny ip 172.30.12.96 0.0.0.31 172.30.12.64 0.0.0.31
 deny ip 172.30.12.96 0.0.0.31 172.30.12.0 0.0.0.63
 deny ip 172.30.12.96 0.0.0.31 172.30.12.128 0.0.0.15
 deny ip 172.30.12.96 0.0.0.31 172.30.12.144 0.0.0.15
 permit ip any any
!
ipv6 access-list GUEST-ISOLATION-V6
 deny ipv6 2001:DB8:ACAD:50::/64 2001:DB8:ACAD:10::/64
 deny ipv6 2001:DB8:ACAD:50::/64 2001:DB8:ACAD:20::/64
 deny ipv6 2001:DB8:ACAD:50::/64 2001:DB8:ACAD:30::/64
 deny ipv6 2001:DB8:ACAD:50::/64 2001:DB8:ACAD:40::/64
 permit ipv6 any any
!
interface GigabitEthernet0/0.50
 ip access-group GUEST-ISOLATION in
 ipv6 traffic-filter GUEST-ISOLATION-V6 in
```

---

## Testing and verification

### Commands used

```text
show vlan brief
show interfaces trunk
show ip interface brief
show ipv6 interface brief
show ipv6 route
show access-lists
show ipv6 access-list
show running-config | section line
show ip ssh
```

### Results

| Test | Expected | Result |
|---|---|---|
| IPv4 ping: gateway, same-VLAN, inter-VLAN | Replies | PASS |
| IPv6 ping: gateway, same-VLAN, inter-VLAN | Replies | PASS |
| SSH to R1 and SW1, correct login | Reaches device | PASS |
| SSH to R1 and SW1, wrong password | `% Login invalid` | PASS |
| Telnet to R1 and SW1 | Refused | PASS |
| Guest to guest gateway (IPv4 and IPv6) | Replies | PASS |
| Guest to internal VLANs (IPv4 and IPv6) | Blocked | PASS |
| PC-Admin to SRV1 over HTTP | Page loads | PASS |
| Laptop-Guest to SRV1 over HTTP | Times out | PASS |

**Note on packet loss:** replies show 0% loss once ARP has resolved. The first ping to a new host can show one lost packet while ARP completes.

---

## Troubleshooting summary

| Problem | Fix |
|---|---|
| Forgotten device passwords on R1 and SW1 | Both devices replaced with fresh units and recabled |
| `% Login invalid` on SSH | Password did not match. Credentials reset on the console and retested |
| SW1 VTY lines 5–15 left open | Locked down with `login local` and `transport input ssh` |

Details are in `troubleshooting/` and the implementation report.

---

## Limitations and future improvements

- Everything was tested in Packet Tracer, which is a simulation.
- Hosts use static addresses. DHCP (and DHCPv6 or SLAAC) would be the next step.
- There is no WAN or ISP link, so internet access is not part of this lab.
- SSH access to R1 and SW1 should be allowed only from the Management VLAN.
- A second switch with redundant links would add resilience.
- Logging and NTP would improve monitoring.

A full reflection is in `docs/Reflection.md`.

---

## Repository structure

```text
Tswalu_Network/
├── configurations/     Router_R1_Config.txt, Switch_SW1_Config.txt
├── diagrams/           Physical and logical topology diagrams
├── docs/               Milestone 1, implementation report, submission document, Reflection.md
├── ip-addressing/      IPv4 and IPv6 addressing plan
├── packet-tracker/     Tswalu_Network.pkt
├── screenshots/        Configuration, connectivity, security and guest-isolation proof
├── troubleshooting/    Problems faced and how they were fixed
└── README.md
```

---

## Project outcome

The network gives segmented departmental connectivity, working IPv4 and IPv6 dual-stack routing, SSH-only secured administration, and a guest wireless network isolated from internal resources. Each requirement was tested in Cisco Packet Tracer and supported with screenshots.
