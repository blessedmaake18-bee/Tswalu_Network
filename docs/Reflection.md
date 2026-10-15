# Reflection – Tswalu Steel Fabrication Network (CMPG325)

**Student:** Blessed Maake (41432363)
**Project ID:** CMPG325-2026-029
**Technical challenge:** IPv6 dual-stack addressing and routing
**Change request:** Guest Wi-Fi isolated from internal resources

---

## 1. What I set out to do

- Design a network for Tswalu Steel Fabrication (Rustenburg) from the client requirements.
- Build it in Cisco Packet Tracer.
- Prove each requirement works with a test.
- Four outcomes: connect the organisation, introduce IPv6, protect the infrastructure, support visitors safely.

## 2. What I built

- Five VLANs (10, 20, 30, 40, 50) routed by one router (R1) using router-on-a-stick.
- IPv4 from the assigned `172.30.12.0/23` block, split with VLSM.
- IPv6 from `2001:DB8:ACAD::/48`, one /64 per VLAN.
- SSH-only administration on R1 and SW1 with local login.
- Guest VLAN 50 isolated using IPv4 and IPv6 ACLs on R1.

## 3. Key design decisions and why

| Decision | Why I chose it |
|---|---|
| Router-on-a-stick | One router and one trunk is enough for five VLANs. Keeps the design simple. |
| VLSM for IPv4 | Each VLAN gets only the size it needs. No wasted addresses. |
| One /64 per VLAN for IPv6 | Standard practice. Easy to read and to route. |
| ACLs on R1 for guest isolation | All inter-VLAN traffic passes through R1, so it is the single place to enforce the rule. |
| Both an IPv4 ACL and an IPv6 ACL | On a dual-stack network, a guest could skip the IPv4 block by using IPv6. |
| SSH v2 only, Telnet off | Telnet sends passwords in clear text. |

## 4. Problems I faced and how I solved them

- **Forgotten device passwords (R1 and SW1).**
  - Carried over from an earlier session.
  - I replaced both devices with fresh ones and recabled, since nothing needed to be kept.
  - Lesson: this showed why the client's secured-administration requirement matters.
- **"% Login invalid" on SSH.**
  - The password I typed did not match the one stored on R1.
  - I reset the enable secret and netadmin password on the console, then tested SSH again.
  - Lesson: test with known credentials and change one thing at a time.
- **Open VTY lines on SW1.**
  - Only lines 0–4 were secured. Lines 5–15 had no login configured.
  - I locked them down with `login local` and `transport input ssh`.
  - Lesson: check the whole line range, not only the first part.
- **First ping showed 25% loss.**
  - This was the first packet waiting for ARP to resolve.
  - Later pings showed 0% loss.

## 5. What I learned

- **IPv6:**
  - Connected /64 networks appear in the routing table automatically once `ipv6 unicast-routing` is on.
  - No static routes were needed.
- **TTL as evidence:**
  - TTL 128 means the host was in the same VLAN (no router hop).
  - TTL 127 means the packet was routed through R1.
- **Security:**
  - VLAN separation alone is not isolation. A router will route between VLANs unless an ACL stops it.
  - ACL match counters prove the ACL is really filtering traffic.
- **Process:**
  - Build the IPv4 base first, then IPv6, then security, then guest isolation.
  - Each step was verified before the next one was added.
  - I saved changes with `copy running-config startup-config` after each change.

## 6. What I would do differently or improve

- Use DHCP (and DHCPv6 or SLAAC) so hosts do not need static addresses.
- Add a WAN or ISP link so guests get internet access only, as shown in the logical topology.
- Add a restriction so only the Management VLAN can reach the SSH service on R1 and SW1.
- Add a second switch and a redundant link for resilience.
- Add logging and NTP for better monitoring.
- Start the GitHub documentation earlier so it is written as I work.

## 7. Limitations

- Everything was tested in Packet Tracer (a simulation). Real hardware may behave differently.
- Only about the first half of the `/23` block is used. The rest is spare for growth.
- Hosts use static addressing.
- Guest isolation covers traffic through R1. Guest access to R1's own management services needs its own control.

## 8. Final thoughts

- The network meets the client requirements: segmented connectivity, working IPv4 and IPv6, secured administration, and isolated guest Wi-Fi.
- Each requirement has a test and a screenshot as proof.
- The problems I hit were useful. They made the security requirement feel real.
