
# Configuring MFF

## About MFF

MAC-forced forwarding (MFF) implements
Layer 2 isolation and Layer 3 communication between hosts in the same broadcast
domain. 

An MFF-enabled device intercepts ARP
requests and returns the MAC address of a gateway (or server) to the senders. In
this way, the senders are forced to send packets to the gateway for traffic
monitoring and attack prevention.

### MFF network model

As shown in [Figure 1](#_Ref177962368),
hosts are connected to Switch C through Switch A and Switch B, which are called
Ethernet access nodes (EANs). The MFF-enabled EANs forward packets from hosts
to the gateway for further forwarding. The hosts are isolated at Layer 2, but
they can communicate at Layer 3\.

Figure 1 Network diagram for MFF

![](https://resource.h3c.com/en/202407/12/20240712_11705503_x_Img_x_png_0_2216085_294551_0.png)

‌

MFF works with any of the following
features to implement traffic filtering and Layer 2 isolation on the EANs:

·     ARP snooping (see Layer
3—IP Services Configuration Guide).

·     IP source guard (see "Configuring IP source
guard").

·     ARP detection (see "Configuring ARP attack
protection").

·     VLAN mapping (see Layer
2—LAN Switching Configuration Guide).

### Port roles

Two types of ports, user port and network
port, exist in an MFF-enabled VLAN.

#### User port

An MFF user port is directly connected to a
host and processes the following packets differently:

·     Allows multicast packets to pass.

·     Delivers ARP packets to the CPU. 

·     Processes unicast packets as follows:

¡     If gateways'
MAC addresses have been learned, the user port allows only the unicast packets
with the gateways' MAC addresses as the destination MAC addresses to pass.

¡     If
no gateways' MAC addresses have been learned, the user port discards all received
unicast packets.

#### Network port

An MFF network port is connected to any of
the following networking devices:

·     An access switch.

·     A distribution switch.

·     A gateway.

·     A server.

A network port processes the following packets
differently:

·     Allows multicast packets to pass.

·     Delivers ARP packets to the CPU. 

·     Denies broadcast packets other than DHCP and ARP
packets.

### Processing of ARP packets in MFF

An MFF-enabled device implements Layer 3
communication between hosts by intercepting ARP requests from the hosts and
replies with the MAC address of a gateway. This mechanism helps reduce the
number of broadcast messages.

The MFF device processes ARP packets as
follows:

·     After receiving an ARP request from a host, the
MFF device sends the MAC address of the corresponding gateway to the host. In
this way, hosts in the network have to communicate at Layer 3 through a gateway.

·     After receiving an ARP request from a gateway,
the MFF device sends the requested host's MAC address to the gateway if the
corresponding entry is available. If the entry is not available, the MFF device
forwards the ARP request.

·     The MFF device forwards ARP replies between
hosts and gateways.

·     If the source MAC addresses of ARP requests from
gateways are different from those recorded, the MFF device updates and
broadcasts the IP and MAC addresses of the gateways.

### MFF default gateway

MFF applies to only networks where the
hosts' IP addresses are manually configured. Because the hosts cannot obtain the
gateway information through DHCP, the default gateway must be specified by the mac-forced-forwarding
default-gateway command. MFF maintains only one default
gateway for each VLAN. MFF updates the MAC address of the default gateway upon receiving
an ARP packet with a different sender MAC address from the default gateway.

### Protocols and standards

RFC 4562, MAC-Forced
Forwarding

## MFF tasks at a glance

To configure MFF, perform the following
tasks:

**1\.**[Enabling MFF](#_Ref478476661)

**2\.**[Configuring a network port](#_Ref478476673)

**3\.**(Optional.) [Enabling periodic gateway probe](#_Ref478476686)

**4\.**[Specifying the IP addresses of servers](#_Ref478476701)

If servers exist in the network, you must
perform this task to ensure communication between the servers and hosts.

## Enabling MFF

#### Restrictions and guidelines

·     An MFF-enabled device and a host cannot ping
each other.

·     When MFF works with static IP source guard
bindings, you must configure VLAN IDs in the static bindings. Otherwise, IP
packets allowed by IP source guard are permitted even if their destination MAC
addresses are not the MAC address of the gateway.

·     MFF is not supported in a network where VRRP load
balancing mode is configured.

#### Prerequisites

For MFF to take effect, make sure ARP snooping
is enabled on the VLAN where MFF is enabled.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter VLAN view.

vlan vlan-id

**3\.**Enable MFF.

mac-forced-forwarding default-gateway gateway-ip

By default, MFF is disabled.

## Configuring a network port

#### Restrictions and guidelines

In a VLAN with MFF enabled, you need to
configure the following ports as network ports:

·     Upstream ports connected to the gateway.

·     Ports connected to other MFF devices.

Link aggregation is supported by network
ports in an MFF-enabled VLAN, but it is not supported by user ports in the
VLAN. You can add the network ports to link aggregation groups, but cannot add
the user ports to link aggregation groups. For more information about link
aggregation, see Layer 2—LAN Switching Configuration
Guide.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter interface view.

interface interface-type interface-number

**3\.**Configure the port as a network port.

mac-forced-forwarding network-port

By default, the port is a user port.

## Enabling periodic gateway probe

#### About this task

You can configure the MFF device to detect
gateways every 30 seconds for the change of MAC addresses by sending forged ARP
packets. The ARP packets use 0.0.0.0 as the sender IP address and bridge MAC
address as the sender MAC address.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter VLAN view.

vlan vlan-id

**3\.**Enable periodic gateway probe.

mac-forced-forwarding gateway probe

By default, this feature is disabled.

## Specifying the IP addresses of servers

#### About this task

Server IP addresses can be those of the interfaces
on a router in a VRRP group and those of the servers collaborating with MFF,
such as a RADIUS server.

When the MFF device receives an ARP request
from a server, the device searches IP-to-MAC address entries it has stored.
Then the device replies with the requested MAC address to the server.

As a result, packets from a host to a
server are forwarded by the gateway. However, packets from a server to a host
are not forwarded by the gateway.

MFF does not check whether the IP address
of a server is on the same network segment as that of a gateway. Instead, it
checks whether the IP address of a server is all-zero or all-one. An all-zero
or all-one server IP address is invalid.

#### Restrictions and guidelines

If the server's interface connecting to the
MFF device uses secondary IP addresses to send ARP packets, include all these
IP addresses in the server IP address list.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter VLAN view.

vlan vlan-id

**3\.**Specify the IP addresses of servers.

mac-forced-forwarding server server-ip\&\<1-10\>

By default, no server IP address is specified.

## Display and maintenance commands for MFF

Execute display
commands in any view.

 

| Task | Command |
| --- | --- |
| Display MFF port configuration. | display mac-forced-forwarding interface || Display the MFF configuration for a VLAN. | display mac-forced-forwarding vlan vlan-id |


‌

## MFF configuration examples

### Example: Configuring MFF in a tree network

#### Network configuration

As shown in [Figure 2](#_Ref209416674), all
the devices are in VLAN 100\. Hosts A, B, and C are assigned IP addresses
manually. 

Configure MFF to isolate the hosts at Layer
2 and allow them to communicate with each other through the gateway at Layer 3\.

Figure 2 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705504_x_Img_x_png_1_2216085_294551_0.png)

‌

#### Procedure

**1\.**Configure the IP addresses of the hosts and
the gateway, as shown in [Figure 2](#_Ref209416674).

**2\.**Configure Switch A:

\# Configure MFF on VLAN 100\.

\[SwitchA] vlan 100

\[SwitchA-vlan100]
mac-forced-forwarding default-gateway 10.1.1.100

\# Specify the IP address of the server.

\[SwitchA-vlan100] mac-forced-forwarding
server 10.1.1.200

\# Enable ARP snooping on VLAN 100\.

\[SwitchA-vlan100] arp snooping
enable

\[SwitchA-vlan100] quit

\# Configure Ten-GigabitEthernet 1/0/1 as a network port.

\[SwitchA] interface ten-gigabitethernet 1/0/1

\[SwitchA-Ten-GigabitEthernet1/0/1] mac-forced-forwarding
network-port

**3\.**Configure Switch B:

\# Configure MFF on VLAN 100\.

\[SwitchB] vlan 100

\[SwitchB-vlan100]
mac-forced-forwarding default-gateway 10.1.1.100

\# Specify the IP address of the server.

\[SwitchB-vlan100]
mac-forced-forwarding server 10.1.1.200

\# Enable ARP snooping on VLAN 100\.

\[SwitchB-vlan100] arp snooping
enable

\[SwitchB-vlan100] quit

\# Configure Ten-GigabitEthernet 1/0/2 as a network port.

\[SwitchB] interface ten-gigabitethernet 1/0/2 1/0/6

\[SwitchB-Ten-GigabitEthernet1/0/2] mac-forced-forwarding
network-port

### Example: Configuring MFF in a ring network

#### Network configuration

As shown in [Figure 3](#_Ref209416685), all
the devices are in VLAN 100, and the switches form a ring. Hosts A, B, and C
are assigned IP addresses manually. 

Configure MFF to isolate the hosts at Layer
2 and allow them to communicate with each other through the gateway at Layer 3\.


Figure 3 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705505_x_Img_x_png_2_2216085_294551_0.png)

‌

#### Procedure

**1\.**Configure the IP addresses of the hosts and
the gateway, as in shown in [Figure 3](#_Ref209416685).

**2\.**Configure Switch A:

\# Enable STP globally to make sure STP is
enabled on interfaces.

\[SwitchA] stp global enable

\# Configure MFF on VLAN 100\.

\[SwitchA] vlan 100

\[SwitchA-vlan100] mac-forced-forwarding
default-gateway 10.1.1.100

\# Specify the IP address of the server.

\[SwitchA-vlan100] mac-forced-forwarding
server 10.1.1.200

\# Enable ARP snooping on VLAN 100\.

\[SwitchA-vlan100] arp snooping
enable

\[SwitchA-vlan100] quit

\# Configure Ten-GigabitEthernet 1/0/2 and Ten-GigabitEthernet 1/0/3 as network ports.

\[SwitchA] interface ten-gigabitethernet 1/0/2

\[SwitchA-Ten-GigabitEthernet1/0/2] mac-forced-forwarding
network-port

\[SwitchA-Ten-GigabitEthernet1/0/2] quit

\[SwitchA] interface ten-gigabitethernet 1/0/3

\[SwitchA-Ten-GigabitEthernet1/0/3]
mac-forced-forwarding network-port

**3\.**Configure Switch B:

\# Enable STP globally to make sure STP is
enabled on interfaces.

\[SwitchB] stp global enable

\# Configure MFF on VLAN 100\.

\[SwitchB] vlan 100

\[SwitchB-vlan100]
mac-forced-forwarding default-gateway 10.1.1.100

\# Specify the IP address of the server.

\[SwitchB-vlan100] mac-forced-forwarding
server 10.1.1.200

\# Enable ARP snooping on VLAN 100\.

\[SwitchB-vlan100] arp snooping
enable

\[SwitchB-vlan100] quit

\# Configure Ten-GigabitEthernet 1/0/1 and Ten-GigabitEthernet 1/0/3 as network ports.

\[SwitchB] interface ten-gigabitethernet 1/0/1

\[SwitchB-Ten-GigabitEthernet1/0/1] mac-forced-forwarding
network-port

\[SwitchB-Ten-GigabitEthernet1/0/1] quit

\[SwitchB] interface ten-gigabitethernet 1/0/3

\[SwitchB-Ten-GigabitEthernet1/0/3]
mac-forced-forwarding network-port

**4\.**Enable STP on Switch C globally to make sure
STP is enabled on interfaces.

\<SwitchC\> system-view

\[SwitchC] stp global enable

