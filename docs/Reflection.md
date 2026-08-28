# Reflection

## Overview

This project involved designing and implementing a dual-stack IPv4/IPv6 network for Tswalu Steel Fabrication. The implementation was completed in Cisco Packet Tracer using one router, one switch, end devices, a server, an access point, and a wireless guest laptop.

## What I Implemented

I divided the network into five VLANs to separate different areas of the organisation:

- VLAN 10 – MANAGEMENT
- VLAN 20 – PRODUCTION
- VLAN 30 – FINANCE
- VLAN 40 – IT_SERVICES
- VLAN 50 – GUEST

The switch was configured with access ports for the end devices and GigabitEthernet0/1 as an 802.1Q trunk toward the router. The trunk was configured to carry VLANs 10, 20, 30, 40, and 50.

On the router, I implemented router-on-a-stick using subinterfaces on GigabitEthernet0/0. Each VLAN received its own IPv4 and IPv6 gateway address. IPv6 unicast routing was also enabled so that IPv6 traffic could be routed between networks.

## Addressing and Connectivity

The IPv4 networks used in the final router configuration were:

- VLAN 10: 172.30.12.64/27, gateway 172.30.12.65
- VLAN 20: 172.30.12.0/26, gateway 172.30.12.1
- VLAN 30: 172.30.12.128/28, gateway 172.30.12.129
- VLAN 40: 172.30.12.144/28, gateway 172.30.12.145
- VLAN 50: 172.30.12.96/27, gateway 172.30.12.97

The IPv6 networks used the 2001:DB8:ACAD prefix, with a separate /64 network for each VLAN.

## Testing and Troubleshooting

Testing was performed using ping commands from the end devices. Successful tests included connectivity to the IPv4 and IPv6 gateway addresses and connectivity between appropriate devices.

For example, the guest device successfully reached the VLAN 50 gateway at:

`172.30.12.97`

and:

`2001:DB8:ACAD:50::1`

The Production PC also successfully communicated using its configured IPv6 address:

`2001:DB8:ACAD:20::10`

During testing, some connectivity failures were observed. These were useful for troubleshooting because they helped identify whether the problem was related to addressing, VLAN membership, routing, or the test destination. I learned that a successful ping to a local gateway does not automatically prove that communication to every other VLAN is working.

## Challenges Encountered

One of the main challenges was configuring the wireless guest laptop. The laptop initially reported that a WPC300N or WPC3CON wireless interface was required. I had to install the compatible wireless module before the laptop could associate with the access point.

Another challenge was understanding the difference between Cisco IOS command modes. Commands such as `show running-config` must be entered from privileged EXEC mode (`Router#`), rather than user EXEC mode (`Router>`). I also learned to avoid entering the prompt itself as part of a command.

I also encountered connectivity issues while testing IPv6 between VLANs. This led to checking the router configuration and enabling:

`ipv6 unicast-routing`

After this was configured, the router showed the IPv6 subinterfaces as up/up.

## Security and Management

The router was configured with an enable secret and console and VTY passwords. VLAN segmentation was used to separate management, production, finance, IT services, and guest traffic.

The guest wireless network was placed in its own VLAN, which provides logical separation from the internal departments. Verification screenshots were captured for VLANs, trunking, IPv4, IPv6, inter-VLAN testing, and guest isolation.

## What I Learned

This project improved my understanding of practical network design and configuration. In particular, I learned how VLANs can be used to separate departments, how router-on-a-stick provides inter-VLAN routing, and how IPv4 and IPv6 can operate together in a dual-stack network.

I also learned that verification is an important part of network implementation. Commands such as `show vlan`, `show interfaces trunk`, `show ip interface brief`, and `show ipv6 interface brief`, together with end-device ping tests, provided evidence that the configuration was functioning.

## Final Reflection

The project gave me practical experience in moving from a network design to an implemented and tested topology. Troubleshooting was an important part of the process because not every configuration worked immediately. Resolving the wireless adapter issue, checking command modes, verifying VLAN assignments, and enabling IPv6 routing helped strengthen my confidence with Cisco Packet Tracer.

Overall, the completed topology demonstrates a segmented dual-stack network with departmental VLANs, router-based inter-VLAN routing, a separate guest network, and documented verification evidence.
