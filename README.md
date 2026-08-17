# Enterprise Network Troubleshooting & Security Labs
Full Documentation attached as PDF

A comprehensive Cisco Packet Tracer troubleshooting portfolio documenting seven hands-on network troubleshooting scenarios involving IP configuration, DHCP, Access Control Lists (ACLs), Spanning Tree Protocol, BPDU Guard, trunking, NAT, Port Security, OSPF, SSH, AAA, and TACACS+.

The central focus of this project is not simply configuring Cisco devices, but developing a structured troubleshooting methodology based on evidence, verification, and the OSI model.

---

## Project Overview

This project contains a series of network troubleshooting labs completed in **Cisco Packet Tracer**.

Each lab presented a realistic networking problem where something in the environment was broken or incorrectly configured.

Rather than immediately changing configurations, I followed a structured process:

1. Confirm the reported problem.
2. Determine the scope of the problem.
3. Gather evidence.
4. Work through the OSI model.
5. Identify the root cause.
6. Apply a targeted fix.
7. Verify that the fix solved the problem.
8. Test the network again to make sure the solution did not introduce another issue.

The main philosophy behind the project was:

> **Never guess. Always verify.**

The labs helped me understand that network troubleshooting is not primarily about memorising commands.

It is about knowing:

- What question needs to be answered
- Which command can provide the evidence
- How to interpret the output
- How to narrow down the possible causes
- How to fix the actual root cause
- How to verify the result
---

# Technologies and Concepts Covered

This project covers:

- IPv4 addressing
- Static IP addressing
- DHCP
- APIPA
- Default gateways
- MAC addresses
- Switching
- VLANs
- Access ports
- Trunk ports
- 802.1Q
- Spanning Tree Protocol (STP)
- PortFast
- BPDU Guard
- Access Control Lists (ACLs)
- Implicit deny
- Router-on-a-Stick
- Inter-VLAN routing
- Network Address Translation (NAT)
- Port Security
- Sticky MAC learning
- OSPF
- OSPF neighbours
- OSPF areas
- Redundant routing
- Failover testing
- SSH
- AAA
- TACACS+
- Authentication
- Authorization
- Accounting
- Network logging
- OSI-model troubleshooting
- Evidence-based troubleshooting
- Change management

---

# Troubleshooting Methodology

The same general methodology was used throughout the labs.

## 1. Confirm the Problem

Before changing anything, I tested the reported issue myself.

For example:

```text
ping 1.1.1.2
````

If the ping failed, I now had a confirmed problem rather than an assumption.

---

## 2. Determine the Scope

The next question was:

> Is this affecting one device, several devices, or the entire network?

This was important because the scope immediately helped narrow down the possible causes.

For example:

* One device failing → investigate that device first.
* One VLAN failing → investigate VLAN, routing, or NAT configuration.
* Entire office failing → investigate shared infrastructure.
* One user unable to authenticate → investigate the user's account and authentication service.

---

## 3. Work Through the OSI Model

The OSI model became the troubleshooting roadmap.

I generally worked from the lower layers upward:

```text
Layer 1
Physical connectivity
        ↓
Layer 2
Switching, MAC addresses, VLANs
        ↓
Layer 3
IP addressing, routing, NAT
        ↓
Higher Layers
DHCP, OSPF, SSH, AAA and other services
```

This prevented me from jumping randomly between possible causes.

---

## 4. Gather Evidence

Instead of assuming what was wrong, I used Cisco commands and endpoint tools to collect information.

Examples included:

```text
ping
ipconfig
ipconfig /all
show ip interface brief
show mac address-table
show running-config
show ip dhcp binding
show ip dhcp pool
show access-lists
show ip route
show ip ospf neighbor
show port-security interface
show logging
show interfaces <interface> switchport
traceroute
```

The output from these commands helped identify the actual root cause.

---

## 5. Apply a Targeted Fix

Once the root cause was identified, I changed only the configuration required to resolve the problem.

The goal was not simply to make the error disappear.

The goal was to correct the underlying configuration while maintaining proper network functionality and security.

---

## 6. Verify the Fix

After making a change, I repeated the original test.

Examples included:

```text
ping 1.1.1.2
```

```text
ping 192.168.1.1
```

```text
show ip ospf neighbor
```

```text
show ip nat translations
```

```text
show logging
```

```text
ssh -l tony 192.168.1.1
```

A fix was not considered complete until the result was verified.

---

# Lab 1 — The Laptop That Could Not Reach the Internet

## Scenario

**Problem:** Incorrect static IP address.

Laptop Zero could not access the internet after being brought from another office to the head office.

The laptop needed to successfully reach:

```text
1.1.1.2
```

which represented the internet in the simulation.

---

## Network

| Item                | Details                        |
| ------------------- | ------------------------------ |
| Network             | `192.168.1.0/24`               |
| Default Gateway     | `192.168.1.1`                  |
| Internet Simulation | `1.1.1.2`                      |
| Problem Device      | Laptop Zero                    |
| Topology            | Router → Switch → PCs + Laptop |

---

## Step 1 — Confirm the Problem

I tested connectivity from Laptop Zero:

```text
ping 1.1.1.2
```

Every request timed out.

This confirmed that the reported problem was real.

---

## Step 2 — Determine the Scope

I tested another computer:

```text
ping 1.1.1.2
```

PC1 successfully received replies.

This immediately showed that:

* The router was working.
* The switch was working.
* The internet connection was working.
* The problem was isolated to Laptop Zero.

This prevented me from wasting time troubleshooting infrastructure that was already working.

---

## Step 3 — Layer 1 Investigation

I checked the switch interface:

```text
show ip interface brief
```

The port connected to Laptop Zero was:

```text
FastEthernet0/2
```

and it showed as up.

This indicated that:

* The cable was connected.
* The switch port was working.
* The laptop had a physical connection.

Layer 1 was therefore working correctly.

---

## Step 4 — Layer 2 Investigation

I checked the switch's MAC address table:

```text
show mac address-table
```

Initially, the laptop's MAC address was not immediately visible.

I generated traffic from the laptop and checked the table again.

The switch then learned the laptop's MAC address and associated it with the correct port.

This confirmed that Layer 2 communication was functioning.

---

## Step 5 — Layer 3 Investigation

On Laptop Zero, I entered:

```text
ipconfig
```

The laptop had an address beginning with:

```text
10.1.1.x
```

However, devices at the head office were using:

```text
192.168.1.x
```

The correct network was:

```text
192.168.1.0/24
```

The laptop was still using a static IP configuration from its previous office.

---

## Root Cause

The laptop had a static IP address belonging to a different network.

It expected to communicate through a `10.1.1.x` network while the head office used:

```text
192.168.1.0/24
```

Because of this mismatch, it could not properly communicate with the default gateway:

```text
192.168.1.1
```

---

## Fix

I changed Laptop Zero from static IP configuration to DHCP.

DHCP automatically provided the laptop with a valid configuration for the head office network.

The laptop received an address in:

```text
192.168.1.x
```

---

## Verification

I repeated the original connectivity test:

```text
ping 1.1.1.2
```

All four ping requests were successful.

Laptop Zero could now access the simulated internet.

---

## Lesson

This lab reinforced the importance of starting with the basics.

The network itself was working.

The physical connection was working.

The switch was working.

The actual problem was simply that the laptop had the wrong IP configuration.

---

# Lab 2 — The Entire Office Lost the Internet

## Scenario

**Problem:** Misconfigured Access Control List blocking network traffic.

Unlike Lab 1, this problem affected the entire office.

Users could not access websites, and computers could not even communicate with the default gateway.

---

## Initial Test

From PC0:

```text
ping 1.1.1.2
```

The ping failed.

I then tested the default gateway:

```text
ping 192.168.1.1
```

This also failed.

This indicated that the problem was inside the office network rather than somewhere on the internet.

---

## Check IP Configuration

I ran:

```text
ipconfig
```

The computer had an address beginning with:

```text
169.254.x.x
```

This is an APIPA address.

APIPA is automatically assigned when a device cannot obtain a valid address from DHCP.

This suggested that the PCs were not receiving addresses from the router.

---

## Check Router Internet Connectivity

On the router:

```text
ping 1.1.1.2
```

The ping was successful.

This confirmed:

* Internet connectivity was working.
* The ISP connection was working.
* The router could reach the internet.

---

## Check Router Interface

I ran:

```text
show ip interface brief
```

The LAN interface:

```text
GigabitEthernet0/0
```

was up.

I then checked:

```text
show interfaces gig0/0
```

There were:

* No errors
* No dropped packets
* No CRC errors

The physical connection appeared healthy.

---

## Check DHCP

I checked DHCP bindings:

```text
show ip dhcp binding
```

There were no DHCP bindings.

I then checked the DHCP pool:

```text
show ip dhcp pool
```

The pool existed and was configured correctly.

Finally:

```text
show running-config
```

The network address, subnet mask, and default gateway were correct.

DHCP itself was not the root cause.

---

## Investigating the ACL

I checked the configured Access Control Lists:

```text
show access-lists
```

Access List 2 allowed only:

```text
192.168.1.100
```

This was suspicious.

---

## Understanding the Implicit Deny

Cisco ACLs have an implicit deny at the end.

Conceptually:

```text
permit 192.168.1.100
deny everything else
```

Because only one address was explicitly permitted, all other office devices were blocked.

---

## Finding Where the ACL Was Applied

I checked:

```text
show running-config
```

The configuration showed:

```text
interface GigabitEthernet0/0
ip access-group 2 in
```

This meant Access List 2 was applied inbound on the office LAN interface.

All traffic entering that interface was therefore evaluated against the restrictive ACL.

---

## Root Cause

The ACL allowed only:

```text
192.168.1.100
```

and blocked all other traffic through the implicit deny.

This prevented computers from:

* Reaching the router
* Receiving DHCP communication
* Accessing the internet

---

## Fix

I removed the ACL from the interface:

```text
interface GigabitEthernet0/0
no ip access-group 2 in
```

I then removed the faulty ACL:

```text
no ip access-list standard 2
```

The computers were then able to renew their network settings.

They received valid IP addresses from DHCP.

---

## Verification

I tested internet access again:

```text
ping 1.1.1.2
```

All ping requests succeeded.

Internet access was restored for the office.

---

## Lesson

This lab demonstrated how a single incorrect security configuration can affect an entire network.

It also reinforced the importance of:

* Understanding implicit deny
* Reviewing where ACLs are applied
* Testing configuration changes
* Using change management
* Verifying changes before and after deployment

The lesson was not simply "remove the ACL."

The deeper lesson was to understand why the ACL caused the outage and how careless changes to production interfaces can have a large impact.

---

# Lab 3 — The New Switch That Wouldn't Come Online

## Scenario

**Problem:** BPDU Guard shutting down an inter-switch connection.

The company was adding a second switch.

The link between Switch 1 and Switch 2 would not come online, preventing computers connected to Switch 2 from accessing the network.

---

## Initial Investigation

PC5, PC6, and PC7 connected to Switch 2 could not obtain IP addresses.

PC0 connected to Switch 1 worked normally.

This isolated the issue to:

* Switch 2
* The connection between Switch 1 and Switch 2

---

## Check Interfaces

On Switch 2:

```text
show ip interface brief
```

The PC-facing ports were up.

However:

```text
GigabitEthernet0/1
```

which connected Switch 2 to Switch 1, was down.

---

## Check Logs

On Switch 1:

```text
show logging
```

The logs showed that the switch had received a BPDU and BPDU Guard had shut down the port.

---

## Understanding BPDU

BPDU stands for:

**Bridge Protocol Data Unit**

Switches use BPDUs to exchange information required by Spanning Tree Protocol.

STP helps prevent Layer 2 switching loops.

---

## Understanding BPDU Guard

BPDU Guard protects ports that are expected to connect to end devices.

If a PortFast-enabled port receives a BPDU, BPDU Guard assumes that an unexpected switch has been connected.

The switch can then place the port into an error-disabled state.

---

## Configuration Found

The switch contained:

```text
spanning-tree portfast bpduguard default
```

PortFast is intended for ports connected to end devices such as PCs.

It should not be used as the normal configuration for an inter-switch link.

---

## Root Cause

The inter-switch connection had been configured as an access port instead of a trunk.

Because PortFast/BPDU Guard was applied, the switch expected an end device.

When another switch was connected, it sent BPDUs.

BPDU Guard detected those BPDUs and shut the port down.

---

## Fix

Instead of disabling BPDU Guard, I corrected the port configuration.

On Switch 1:

```text
interface GigabitEthernet0/1
switchport mode trunk
switchport trunk native vlan 1
switchport trunk allowed vlan 1
shutdown
no shutdown
end
write
```

The same configuration was applied on Switch 2.

---

## Configuration Explanation

### Trunk Mode

```text
switchport mode trunk
```

Changes the interface into a trunk.

### Native VLAN

```text
switchport trunk native vlan 1
```

Sets VLAN 1 as the native VLAN.

### Allowed VLAN

```text
switchport trunk allowed vlan 1
```

Allows VLAN 1 across the trunk.

### Shutdown / No Shutdown

```text
shutdown
no shutdown
```

Resets the interface and clears the error-disabled condition caused by BPDU Guard.

---

## Verification

I checked:

```text
show ip interface brief
```

The inter-switch interface showed as up.

I then checked:

```text
show interfaces GigabitEthernet0/1 switchport
```

The interface was operating as a trunk and using 802.1Q encapsulation.

---

## End-to-End Testing

From PC5:

```text
ping 192.168.1.1
```

The gateway responded.

Then:

```text
ping 1.1.1.2
```

Internet connectivity was successful.

---

## Lesson

The important lesson was:

> Do not remove security controls simply because they are blocking something.

Instead, determine why the security control was triggered and configure the network correctly.

BPDU Guard was doing exactly what it was designed to do.

The actual problem was that an inter-switch link had been configured incorrectly.

---

# Lab 4 — The Server VLAN That Could Not Reach the Internet

## Scenario

**Problem:** NAT access list missing the new VLAN subnet.

A new application server had been placed in VLAN 2.

The existing workstations in VLAN 1 could access the internet, but the server in VLAN 2 could not.

The network used:

* Router-on-a-Stick
* Inter-VLAN routing
* NAT

---

## Network

| Item           | Details            |
| -------------- | ------------------ |
| VLAN 1         | Workstations       |
| VLAN 1 Network | `192.168.1.0/24`   |
| VLAN 2         | Application Server |
| VLAN 2 Network | `192.168.2.0/24`   |
| VLAN 2 Gateway | `192.168.2.1`      |
| Internet Test  | `1.1.1.2`          |
| Routing        | Router-on-a-Stick  |

---

## Step 1 — Test the Server Gateway

From the App Server:

```text
ping 192.168.2.1
```

The ping succeeded.

This showed that the server could communicate with its router sub-interface.

---

## Step 2 — Test the Internet

```text
ping 1.1.1.2
```

The ping failed.

A PC on VLAN 1 could access the internet successfully.

Therefore, the problem was isolated to VLAN 2.

---

## Step 3 — Check VLAN and Trunk Configuration

I confirmed that:

* The App Server was connected to VLAN 2.
* The switch-to-router link was a trunk.
* The trunk carried VLAN 1 and VLAN 2.

---

## Step 4 — Check Router Interfaces

I ran:

```text
show ip interface brief
```

The router sub-interfaces were up and had the correct IP addresses.

---

## Step 5 — Check Routing

I ran:

```text
show ip route
```

The router had a route to the internet.

Since VLAN 1 could reach the internet, routing was already functioning.

This shifted the investigation toward NAT.

---

## Step 6 — Check NAT

I ran:

```text
show ip nat translations
```

When I generated traffic from VLAN 1, NAT entries appeared.

When I generated traffic from the App Server in VLAN 2, no NAT entry appeared.

This indicated that traffic from VLAN 2 was not being translated.

---

## Root Cause

The router used:

```text
ip nat inside source list 1 interface <WAN_interface> overload
```

This meant that traffic had to match Access List 1 before being translated.

The ACL contained:

```text
access-list 1 permit 192.168.1.0 0.0.0.255
```

VLAN 1 was included.

VLAN 2 was missing.

Therefore, the router translated traffic from VLAN 1 but ignored traffic from:

```text
192.168.2.0/24
```

---

## Fix

I added VLAN 2 to the NAT access list:

```text
access-list 1 permit 192.168.2.0 0.0.0.255
```

---

## Verification

I checked:

```text
show access-lists
```

Both VLAN networks were now included.

I then tested:

```text
ping 1.1.1.2
```

The App Server successfully reached the simulated internet.

Finally:

```text
show ip nat translations
```

The server's private address appeared in the NAT table.

---

## Lesson

This lab demonstrated that routing and NAT are separate functions.

A router can know how to reach the internet but still fail to provide internet access to a subnet if that subnet is missing from the NAT configuration.

When a new VLAN or subnet is added, the following may all need to be considered:

* VLAN configuration
* Inter-VLAN routing
* Routing table
* NAT configuration
* NAT access lists

The command:

```text
show ip nat translations
```

proved particularly useful for determining whether traffic was actually being translated.

---

# Lab 5 — The New PC That Was Blocked at the Switch

## Scenario

**Problem:** Port Security violation caused by an incorrect stored MAC address.

A new computer, PC Zero, had been connected to the network.

The rest of the office worked normally, but PC Zero could not obtain an IP address or access the internet.

---

## Network

| Item             | Details         |
| ---------------- | --------------- |
| Network          | Office LAN      |
| Problem Device   | PC Zero         |
| Switch Port      | FastEthernet0/2 |
| IP Assignment    | DHCP            |
| Security Feature | Port Security   |

---

## Step 1 — Confirm the Problem

I tested another computer:

```text
ping 1.1.1.2
```

The ping succeeded.

This confirmed that the router, switch, and internet connection were working.

I then checked PC Zero:

```text
ipconfig
```

The PC did not have an IP address.

I also confirmed that DHCP was enabled.

---

## Step 2 — Check Switch Logs

On the switch:

```text
show logging
```

The logs showed a Port Security violation on:

```text
FastEthernet0/2
```

The switch had detected a device it did not recognize and disabled the port.

---

## Step 3 — Check Port Security

I ran:

```text
show port-security interface FastEthernet0/2
```

The output showed:

* Port Security enabled
* Violation mode set to shutdown
* Port placed into a secure shutdown state

---

## Step 4 — Check Configuration

I ran:

```text
show running-config
```

The configuration contained:

```text
switchport port-security mac-address <MAC Address>
```

This meant the switch was expecting one specific MAC address.

---

## Compare MAC Addresses

On PC Zero:

```text
ipconfig /all
```

The MAC address of PC Zero did not match the MAC address stored on the switch.

The old computer's MAC address was still configured.

When the new PC was connected, the switch interpreted it as an unauthorized device.

---

## Root Cause

The computer had been replaced, but the switch still expected the MAC address of the previous device.

Port Security detected the mismatch and shut down the port.

---

## Fix

Instead of manually entering the new MAC address, I used Sticky MAC learning.

The configuration was:

```text
configure terminal
interface FastEthernet0/2
no switchport port-security mac-address <old_MAC>
switchport port-security mac-address sticky
shutdown
no shutdown
end
copy running-config startup-config
```

---

## Understanding Sticky MAC

Sticky MAC allows the switch to automatically learn the MAC address of the connected device and save it as a secure MAC address.

This reduces the need to manually enter MAC addresses whenever devices are replaced.

---

## Verification

After the port was restored, PC Zero received an IP address from DHCP.

I then tested:

```text
ping 1.1.1.2
```

All four ping requests succeeded.

---

## Lesson

Port Security can protect a network from unauthorized devices, but it must be managed when legitimate hardware changes occur.

The security feature was not the problem.

The problem was that the switch had outdated security information.

This lab also demonstrated the value of:

```text
show logging
```

because the switch logs immediately pointed toward the Port Security violation.

---

# Lab 6 — The Redundant Path That Was Never There

## Scenario

**Problem:** OSPF Area Mismatch preventing routers from becoming neighbours.

The network contained a primary path and a backup path.

The primary connection was:

```text
West → South
```

The backup path was:

```text
West → North → East
```

The primary connection had previously failed, but the backup route did not take over.

By the time troubleshooting began, the primary link had already been repaired.

The objective was therefore to discover why the backup path had failed and verify that it would work during a future failure.

---

## Network

| Item               | Details                  |
| ------------------ | ------------------------ |
| Routers            | West, South, North, East |
| Primary Path       | West → South             |
| Backup Path        | West → North → East      |
| Test Device        | West PC1                 |
| Destination Server | `172.16.1.100`           |

---

## Step 1 — Confirm Current Connectivity

From West PC1:

```text
ping 172.16.1.100
```

The ping succeeded.

The primary path was currently working.

However, this did not prove that the backup path was functioning.

---

## Step 2 — Check OSPF Neighbours

On the West router:

```text
show ip ospf neighbor
```

Only the South router appeared.

The North router did not appear as an OSPF neighbour.

This was suspicious because a physical connection existed between West and North.

If the routers were not OSPF neighbours, they could not exchange routing information.

---

## Step 3 — Check Logs

On the North router:

```text
show logging
```

The logs repeatedly reported:

```text
OSPF Area Mismatch
```

This indicated that the routers were communicating but did not agree on the OSPF area associated with the link.

---

## Step 4 — Compare OSPF Configuration

On West:

```text
network 10.1.1.0 0.0.0.3 area 0
```

On North:

```text
network 10.1.1.0 0.0.0.3 area 100
```

The configurations did not match.

---

## Root Cause

The West router placed the link in:

```text
Area 0
```

while the North router placed the same link in:

```text
Area 100
```

Routers connected across the same OSPF link must use the same area.

Because of the mismatch, the routers could not form an OSPF neighbour relationship.

Therefore, they could not exchange routing information.

---

## Fix

On the North router:

```text
configure terminal
router ospf 1
no network 10.1.1.0 0.0.0.3 area 100
network 10.1.1.0 0.0.0.3 area 0
end
```

The incorrect Area 100 configuration was removed and the network was added to Area 0.

---

## Verify OSPF Neighbours

I ran:

```text
show ip ospf neighbor
```

West now showed both:

* South
* North

as OSPF neighbours.

The backup routing relationship was now established.

---

## Test the Backup Path

I started a continuous ping:

```text
ping 172.16.1.100 -t
```

While the ping was running, I disconnected the West-to-South link to simulate a failure.

Only one ping packet was lost.

Traffic then continued successfully through:

```text
West → North → East
```

OSPF automatically selected the backup route.

---

## Lesson

This lab demonstrated an important enterprise networking principle:

**Redundancy must be tested.**

A physical backup path does not automatically mean that the network is resilient.

The backup path in the lab existed physically, but the OSPF Area mismatch prevented the routers from becoming neighbours.

After correcting the OSPF configuration, the backup route was tested by deliberately disconnecting the primary path.

The network successfully redirected traffic through the backup path.

---

# Lab 7 — The New Engineer Who Could Not Log In

## Scenario

**Problem:** Missing user account on the TACACS+ authentication server.

A new network engineer named Tony was unable to log into the CE router remotely using SSH.

His username and password had been provided, but every login attempt failed.

---

# Understanding SSH

SSH stands for:

**Secure Shell**

SSH provides secure remote access to network devices.

Unlike Telnet, SSH encrypts:

* Usernames
* Passwords
* Communication

This makes SSH the preferred method for secure remote device management.

---

# Understanding AAA

AAA stands for:

**Authentication, Authorization, and Accounting**

### Authentication

Determines:

> Who are you?

### Authorization

Determines:

> What are you allowed to do?

### Accounting

Records:

> What did you do?

AAA therefore provides a structured way to control and monitor access to network devices.

---

# Understanding TACACS+

TACACS+ is an authentication service that can store user accounts and verify login requests from network devices.

Instead of configuring every user directly on every router, network devices can send authentication requests to a centralized TACACS+ server.

In this lab, the authentication server was:

```text
192.168.1.203
```

The CE router was:

```text
192.168.1.1
```

---

## Step 1 — Confirm Network Connectivity

From Tony's computer:

```text
ping 192.168.1.1
```

The ping succeeded.

This confirmed that the network connection between Tony's PC and the router was working.

---

## Step 2 — Test SSH

Tony attempted:

```text
ssh -l tony 192.168.1.1
```

After entering the password, the router returned:

```text
Login Invalid
```

The authentication failed.

---

## Step 3 — Test Another User

Another engineer, Alice, attempted to log in:

```text
ssh -l alice 192.168.1.1
```

Alice successfully logged in.

This immediately provided useful evidence.

It showed that:

* SSH was working.
* The router was reachable.
* AAA was functioning.
* The TACACS+ server was reachable.
* The problem was specific to Tony.

---

## Step 4 — Check Router AAA Configuration

I checked:

```text
show running-config
```

The router was configured to use AAA and a TACACS+ server for authentication.

A local backup account was also configured.

This provided fallback access if the TACACS+ server became unavailable.

---

## Step 5 — Investigate the Authentication Server

I logged into the TACACS+ server and checked the user accounts.

The server showed:

```text
Alice  ✔
Sam    ✔
Tony   ✘
```

Tony's account did not exist.

---

## Root Cause

Tony had been given credentials, but his account had never actually been created on the TACACS+ server.

The router sent Tony's authentication request to the TACACS+ server.

The server could not find the account.

Therefore, authentication failed.

---

## Fix

I created Tony's account on the TACACS+ server using the correct username and password.

After saving the account, Tony attempted the SSH connection again:

```text
ssh -l tony 192.168.1.1
```

This time, authentication succeeded.

Tony was able to log into the router.

---

## Lesson

This lab demonstrated that not every network problem originates from the router, switch, or physical network.

Network infrastructure often depends on supporting services such as:

* TACACS+
* RADIUS
* Syslog
* NTP
* SNMP
* DHCP
* DNS

A problem with one of these services can make a network device appear faulty even when the device itself is working correctly.

Testing another user was particularly useful because Alice could successfully authenticate.

That immediately narrowed the problem down to Tony's account rather than the router or SSH service.

---

# Troubleshooting Command Reference

## ping

```text
ping <IP address>
```

Tests whether one device can communicate with another.

---

## ipconfig

```text
ipconfig
```

Displays:

* IP address
* Subnet mask
* Default gateway

---

## ipconfig /all

```text
ipconfig /all
```

Provides detailed information including:

* MAC address
* DHCP information
* DNS information
* IP configuration

---

## show ip interface brief

```text
show ip interface brief
```

Provides a quick overview of router or Layer 3 switch interfaces.

---

## show mac address-table

```text
show mac address-table
```

Displays MAC addresses learned by the switch and the ports associated with them.

---

## show running-config

```text
show running-config
```

Displays the current configuration.

Used throughout the labs to verify:

* IP configuration
* ACLs
* NAT
* Port Security
* OSPF
* AAA
* Interface configuration

---

## show ip dhcp binding

```text
show ip dhcp binding
```

Displays DHCP address assignments.

---

## show ip dhcp pool

```text
show ip dhcp pool
```

Displays information about configured DHCP pools.

---

## show access-lists

```text
show access-lists
```

Displays configured ACLs and their rules.

---

## show ip route

```text
show ip route
```

Displays the routing table.

---

## show ip ospf neighbor

```text
show ip ospf neighbor
```

Displays OSPF neighbour relationships.

---

## show port-security interface

```text
show port-security interface <interface>
```

Displays Port Security configuration and violation information.

---

## show logging

```text
show logging
```

Displays system log messages.

---

## show interfaces switchport

```text
show interfaces <interface> switchport
```

Provides detailed switchport information including:

* Access/trunk mode
* VLAN configuration
* Allowed VLANs
* Encapsulation

---

## show ip nat translations

```text
show ip nat translations
```

Displays active NAT translations.

---

## traceroute

```text
traceroute <IP address>
```

Shows the path traffic takes toward a destination.

---

# Summary of Root Causes

| Lab | Problem                               | Root Cause                                                    | Resolution                     |
| --- | ------------------------------------- | ------------------------------------------------------------- | ------------------------------ |
| 1   | Laptop could not access internet      | Incorrect static IP from another network                      | Changed laptop to DHCP         |
| 2   | Entire office lost internet           | ACL blocked traffic through implicit deny                     | Removed faulty ACL             |
| 3   | New switch would not connect          | BPDU Guard shut down incorrectly configured inter-switch link | Configured the link as a trunk |
| 4   | Server VLAN could not access internet | VLAN 2 missing from NAT ACL                                   | Added VLAN 2 subnet to NAT ACL |
| 5   | New PC blocked at switch              | Stored MAC address did not match new PC                       | Configured Sticky MAC          |
| 6   | Backup routing path failed            | OSPF Area mismatch                                            | Corrected OSPF area            |
| 7   | Engineer could not SSH                | User account missing from TACACS+ server                      | Created the user account       |

---

# Key Troubleshooting Lessons

## Evidence Before Assumptions

Do not guess.

Use commands, tests, logs, and configuration output to determine what is actually happening.

## Determine Scope Early

Knowing whether one device or an entire network is affected can dramatically reduce the troubleshooting area.

## Use the OSI Model

Move logically from physical connectivity to switching, IP addressing, routing, and higher-level services.

## Understand Security Features

Security mechanisms such as:

* ACLs
* BPDU Guard
* Port Security
* AAA

are valuable, but they must be configured correctly.

## Test Redundancy

A backup path should not simply exist.

It should be tested.

## Understand Dependencies

Routers and switches depend on services such as:

* DHCP
* TACACS+
* SNMP
* Syslog
* NTP

A failure in a supporting service can look like a network-device problem.

## Verify the Fix

Never assume that a configuration change solved the problem.

Test it.

---

# Security Concepts Demonstrated

## Access Control Lists

ACLs can control which traffic is permitted or denied.

However, an incorrectly configured ACL can cause a major outage.

The project demonstrated the importance of understanding implicit deny.

---

## BPDU Guard

BPDU Guard protects PortFast-enabled ports from unexpected switches.

It helps reduce the risk of switching loops caused by incorrectly connected devices.

---

## Port Security

Port Security restricts which MAC addresses can use a switch port.

It can prevent unauthorized devices from connecting to protected ports.

---

## Sticky MAC

Sticky MAC allows a switch to dynamically learn and retain the MAC address of an approved device.

---

## AAA

AAA provides a structured approach to:

* Authentication
* Authorization
* Accounting

---

## TACACS+

TACACS+ provides centralized authentication for network devices.

This means administrators do not necessarily need separate local accounts on every router and switch.

---

## SSH

SSH provides encrypted remote access to network devices.

It is more secure than Telnet because the communication is encrypted.

---

# Network Resilience and Redundancy

Lab 6 demonstrated an important enterprise networking principle:

**Redundancy must be tested.**

A physical backup path does not automatically mean that the network is resilient.

The backup path in the lab existed physically, but the OSPF Area mismatch prevented the routers from becoming neighbours.

After correcting the OSPF configuration, the backup route was tested by deliberately disconnecting the primary path.

The network successfully redirected traffic through:

```text
West → North → East
```

This provided practical evidence that the redundant path was working.

---

# Change Management Lesson

Lab 2 also demonstrated why change management matters.

A junior administrator made an ACL change while preparing a new server.

The change was applied to a live network interface and unintentionally blocked the rest of the office.

Change management involves:

* Planning
* Reviewing
* Testing
* Approving
* Implementing
* Verifying changes

This reduces the likelihood of configuration errors causing network outages.

---

# Supporting Network Services

Lab 7 demonstrated that network devices depend on services outside the device itself.

Examples include:

| Service | Purpose                    |
| ------- | -------------------------- |
| TACACS+ | Centralized authentication |
| RADIUS  | Authentication             |
| Syslog  | Centralized logging        |
| NTP     | Time synchronization       |
| SNMP    | Network monitoring         |
| DHCP    | Automatic IP configuration |
| DNS     | Name resolution            |

Troubleshooting therefore requires looking beyond the immediate device.

---

# What I Learned

These labs significantly changed how I approach network troubleshooting.

The labs showed me that effective troubleshooting is much more structured than simply changing configurations and hoping the problem disappears.

I learned to:

* Confirm the problem before troubleshooting.
* Determine whether the issue affects one device or an entire network.
* Use the OSI model as a troubleshooting roadmap.
* Use Cisco diagnostic commands to gather evidence.
* Identify the root cause before making changes.
* Avoid disabling security controls without understanding why they triggered.
* Test redundant network paths rather than assuming they work.
* Consider supporting services when troubleshooting authentication, addressing, and connectivity problems.
* Verify every fix.

The most important habit I developed was:

> **Verify first, investigate step by step, and let the evidence lead to the solution.**

---

# Troubleshooting Philosophy

The methodology developed throughout this project can be summarized as:

```text
Confirm
   ↓
Determine Scope
   ↓
Gather Evidence
   ↓
Follow the OSI Model
   ↓
Identify Root Cause
   ↓
Apply Targeted Fix
   ↓
Verify
   ↓
Test Again
```

This process is more reliable than randomly changing configurations.

---

# Project Structure

```text
enterprise-network-security-and-routing-lab/
│
├── README.md
│
└── IP Configuration · ACLs · Spanning Tree · Inter-VLAN Routing · NAT · Port Security · OSPF · AAA.pdf
```

The PDF contains the full laboratory documentation and troubleshooting walkthroughs.

---

# Project Status

**Completed**

The project documents seven Cisco Packet Tracer troubleshooting scenarios covering:

* IP configuration
* DHCP
* ACLs
* STP
* PortFast
* BPDU Guard
* Trunking
* 802.1Q
* NAT
* Router-on-a-Stick
* Port Security
* Sticky MAC
* OSPF
* OSPF areas
* Redundant routing
* Failover testing
* SSH
* AAA
* TACACS+

---

# Final Reflection

Completing these troubleshooting labs taught me that network troubleshooting is not about memorising commands.

It is about asking the right questions, collecting evidence, thinking logically, and testing every solution before declaring the problem solved.

Each lab demonstrated a different failure:

* An incorrect IP address
* A restrictive ACL
* A BPDU Guard shutdown
* A missing NAT rule
* A Port Security violation
* An OSPF Area mismatch
* A missing TACACS+ user account

Although the problems were different, the troubleshooting methodology remained consistent.

I learned to:

**Confirm the problem.**

**Determine its scope.**

**Gather evidence.**

**Follow the OSI model.**

**Find the root cause.**

**Fix the actual problem.**

**Verify the result.**

The most valuable skill I gained from this project was not a particular Cisco command.

It was developing the mindset to approach a network problem systematically instead of guessing.

That mindset is something I intend to carry into every network and infrastructure environment I work in.

```
```
