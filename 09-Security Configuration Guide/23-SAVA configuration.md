
# Configuring SAVA

## About SAVA

Source Address
Validation Architecture (SAVA) checks the validity of the source IPv6 address
of packets based on routing information to prevent source IPv6 address spoofing
attacks. 

A SAVA-enabled
interface creates SAVA entries based on routes that use the interface as the
outgoing interface. Upon receiving an IPv6 packet on the interface, the device
searches for a SAVA entry that contains the source IPv6 address and the receiving
interface. If a match is found, the device forwards the packet. If no match is
found, the device discards the packet.

SAVA is typically
deployed on the border devices of the backbone network connecting to an access
network.

### Benefits

As shown in[Figure 1](#_Ref55076655), asymmetric
routing might occur in an IPv6 multiple-access network. The outgoing interface
for a return packet might be different from the incoming interface of the
original packet. You can enable strict uRPF check on interface 1 of Device B to
prevent attacks with spoofed source IPv6 addresses. However, strict uRPF check
might determine that a valid packet from the LAN as an attack packet based on
local routing information, which causes unexpected packet drop. 

Compared with uRPF, SAVA
can not only prevent source IPv6 address spoofing attacks but also avoid
mistaken packet drop caused by asymmetric routing. When SAVA is enabled on
interface 1 of both Device A and Device B, the devices can create SAVA entries
based on local routes and remote routes synchronized from other routing protocols.
In this way, both the devices have consistent SAVA entries. This ensures that
packets of valid users will not be mistakenly dropped on either a asymmetric
network or a symmetric network with asymmetric routing.

Figure 1 SAVA-enabled
network

![](https://resource.h3c.com/en/202407/12/20240712_11705494_x_Img_x_png_0_2216084_294551_0.png)

‌

### Mechanism

The mechanism of
SAVA includes obtaining prefixes of valid users, creating SAVA entries based on
the prefixes, and using SAVA entries to filter IPv6 packets.

#### Obtaining prefixes of valid users

On an IPv6 multiple
access network, each border device on the backbone network obtains prefix
information of all users in the LAN based on the following types of routes:

Local routes—Each border device obtains
prefix information of users in the LAN from local routes, including direct
routes, static routes, and dynamic routes for the interfaces connecting to the
LAN.

Remote routes synchronized from other routing protocols—Each border device adds the same tag to the local routes for the LAN
and redistributes the routes to other border devices through a routing
protocol. All the border devices import the remote routes with the specified
tag and then obtain prefix information from these routes. In this way, all
border devices have consistent prefix information of valid users.

#### Creating SAVA entries

Each border device
creates SAVA entries for the SAVA-enabled interfaces based on prefix
information of valid users. A SAVA entry includes a prefix, prefix length, and
binding interface.

#### Filtering user packets based on SAVA entries

When a SAVA-enabled
interface receives an IPv6 packet from the LAN, SAVA validates the source IPv6
address of the packet. It searches for a SAVA entry that includes the interface
by the source IPv6 address. If a match is found, SAVA permits the packet. If no
match is found, SAVA drops the packet.

### Application scenarios

#### Deployed on intra-AS border devices directedly connected to the LAN

As shown in[Figure 2](#_Ref56763730),
users in the LAN connect to the backbone network through Device B and Device C.
Device B is configured with two gateways addresses (2000::1/64 and 2001::1/64).
Device C is configured with the gateway address (2000::2/64).

You can configure SAVA
on interface 1 of both Device B and Device C. When Device C receives an IPv6
route with prefix 2001::/64 from Device B, it creates an SAVA entry with this
prefix. Then, upon receiving an IPv6 packet from a user at 2001::/64, Device C
will permit the packet because a matching SAVA entry exists.

Figure 2 SAVA deployed on border devices
directly connected to the LAN

![](https://resource.h3c.com/en/202407/12/20240712_11705495_x_Img_x_png_1_2216084_294551_0.png)

‌

#### Deployed on intra-AS border devices indirectedly connected to the LAN

As shown in[Figure 3](#_Ref43657509), asymmetric
traffic exists between Device D and the LAN after route convergence. Traffic
from Device D to the LAN will traverse Device E, Device C, Device B, and Device
A. Traffic from the LAN to Device D will traverse only Device A.

You can configure SAVA
on interface 1 of both Device B and Device D. Device B and Device D synchronize
routes with each other, obtain prefix information of the LAN based on the synchronized
remote routes, and then create SAVA entries. Upon receiving a packet from a
user in the LAN, Device C will permit the packet because a matching SAVA entry
exists.

Figure 3 SAVA deployed on intra-AS border devices
indirectly connected to the LAN

![](https://resource.h3c.com/en/202407/12/20240712_11705496_x_Img_x_png_2_2216084_294551_0.png)

‌

#### Delpoyed on inter-AS border devices indirectedly connected to the LAN

As shown in[Figure 4](#_Ref58336413),
asymmetric traffic exists between Device D and the LAN after route convergence.
Traffic from Device D to the LAN will traverse Device E, Device C, Device B,
and Device A. Traffic from the LAN to Device D will traverse only Device A.

You can configure SAVA
on interface 1 of both Device B and Device D. Device B and Device C in AS 100
as well as Device D and Device E in AS 200 synchronize local routes with each
other through an IGP protocol (OSPFv3, for example). Then, Device C in AS 100
and Device E in AS 200 synchronize routes with each other through BGP. Upon
receiving a packet from a user in the LAN, Device C will permit the packet
because a matching SAVA entry exists. 

Figure 4 SAVA deployed on inter-AS border devices
indirectly connected to the LAN

![](https://resource.h3c.com/en/202407/12/20240712_11705497_x_Img_x_png_3_2216084_294551_0.png)

‌

## SAVA tasks at a glance

To configure SAVA,
perform the following tasks:

**1\.**[Enabling SAVA](#_Ref54270058)

**2\.**[Enabling SAVA entry creation based on
synchronized remote routes](#_Ref45629144)

Perform this task if the LAN connects to
the backbone network through multiple border devices and interfaces on the
border devices do not have prefix information of all user in the LAN.

**3\.**[Adding an interface to a SAVA access
group](#_Ref45628617)

Perform this task if a border device has
multiple interfaces connected to the same LAN.

**4\.**[Configuring SAVA logging](#_Ref55295176)

## Enabling SAVA

#### Restrictions and guidelines

SAVA is mutually exclusive with uPRF and
microsegmentation. Do not configure SAVA together with uRPF or
microsegmentation. 

If the device has a
large number of routing entries, it might take a long time for the device to
complete SAVA entry creation. Before SAVA entry creation completes, valid IPv6
packets might be dropped.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter interface view.

interface interface-type interface-number

**3\.**Enable SAVA on the interface.

ipv6 sava enable

By default, SAVA is disabled.

## Enabling SAVA entry creation based on synchronized remote routes

#### About this task

Perform this task to
ensure that the border devices through which users in a LAN connects to the backbone
network have the same SAVA entries to avoid mistaken packet drop.

Each border device
adds a route tag to local routes based on which SAVA entries are created and
then advertises the tagged local routes to the other border devices through a
routing protocol. Then, other border devices will create SAVA entries upon
receiving the tagged routes advertised by other border devices. In this way,
the border devices maintain consistent SAVA entries.

#### Prerequisites

Before you enable SAVA
entry creation based on synchronized remote routes, you must complete one of the
following tasks:

·     Configure OSPFv3 link tag inheritance

Perform this task if OSPFv3 runs on the
network. For more information about OSPFv3 link tag inheritance, see OSPFv3
configuration in Layer 3—IP Routing Configuration Guide.

·     Configure IPv6 IS-IS link tag inheritance

Perform this task if IPv6 IS-IS runs on the
network. For more information about IPv6 IS-IS link tag inheritance, see IS-IS
configuration in Layer 3—IP Routing Configuration Guide.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter interface view.

interface interface-type interface-number

**3\.**Enable the device to create SAVA entries
based on synchronized remote routes with the specified route tag.

ipv6 sava import remote-route-tag tag

By default, the device does not create
SAVA entries based on synchronized remote routes.

## Adding an interface to a SAVA access group

#### About this task

If the device has
multiple interfaces connected to the same LAN, each interface creates SAVA
entries only based on its local routes. When an interface receives a packet
from the LAN for which the interface has no matching SAVA entry, the packet
will be discarded. 

To resolve this
issue, you can add the interfaces to a SAVA access group. The interfaces in the
same SAVA access group will synchronize SAVA entries created based on local
routes with each other. This avoids unexpected packet drop caused by asymmetric
routing.

#### Restrictions and guidelines

All interfaces in a
SAVA access group must belong to the public network or the same VPN instance.

You can add a
maximum of eight interfaces to a SAVA access group.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter interface view.

interface interface-type interface-number

**3\.**Add the interface to a SAVA access group.

ipv6 sava access-group group-name

By default, an interface does not belong
to a SAVA access group.

## Configuring SAVA logging

#### About this task

To identify and
troubleshoot issues, enable SAVA logging.

This feature enables
the device to generate SAVA logs when SAVA detects spoofing packets.

With the information
center, you can configure log destinations and output rules. For more
information about the information center, see Network
Management and Monitoring Configuration Guide.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Configure SAVA logging.

ipv6 sava log enable spoofing-packet \[ interval interval \| number number ]\*

By default, SAVA logging is disabled.

## Display and maintenance commands for SAVA

Execute display commands in any view and
reset commands in user view.

 

| Task | Command |
| --- | --- |
| Display SAVA entries. | display ipv6 sava \[ interface interface-type interface-number \| slot slot-number ] || Display SAVA packet drop statistics. | display ipv6 sava packet-drop statistics \[ interface interface-type interface-number ] || Clear SAVA packet drop statistics. | reset ipv6 sava packet-drop statistics \[ interface interface-type interface-number ] |



‌

## SAVA configuration examples

### Example: Configuring SAVA on border devices directly connected the LAN

#### Network configuration

As shown in[Figure 5](#_Ref59375737), legal
users in the LAN use prefixes 2000::/64 and 2001::/64. Configure SAVA on
VLAN-interface 10 of Device B and Device C to meet the following requirements:

Device C creates a
SAVA entry upon receiving an IPv6 route with prefix 2001::/64.

The LAN-side
interface on Device C filters packets from users in the LAN based on SAVA
entries.

Figure 5 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705498_x_Img_x_png_4_2216084_294551_0.png)

‌

Table 1 Interface and IP address assignment

| Device | Interface | IP address | Device | Interface | IP address |
| --- | --- | --- | --- | --- | --- |
| Device A | Vlan-int20 | 192:168::12:1/120 | Device B | Vlan-int10 | 2001::1/64 || Device A | Vlan-int30 | 192:168::22:1/120 | Device B | Vlan-int20 | 192:168::22:2/120 || Device C | Vlan-int10 | 2000::2/64 | — | — | — || Device C | Vlan-int30 | 192:168::12:2/120 | — | — | — |




‌

#### Prerequisites

**1\.**Assign IPv6 addresses to interfaces on the
devices.

**2\.**Configure OSPFv3 on the backbone network.
For more information, see OSPFv3 configuration in Layer
3—IP Routing Configuration Guide.

#### Procedure

**1\.**Configure Device B:

\# Enable SAVA on VLAN-interface 10 (the
LAN-side interface).

\<DeviceB\> system-view

\[DeviceB] interface vlan-interface 10

\[DeviceB-Vlan-interface10] ipv6 sava enable

\# Configure routing policy named ttt, configure node
10 in permit mode to permit routes with the output interface VLAN-interface 10
and to set a tag of 100 for IGP routes.

\[DeviceB] route-policy ttt permit node 10

\[DeviceB-route-policy-ttt-10] if-match
interface vlan-interface 10

\[DeviceB-route-policy-ttt-10] apply tag 100

\[DeviceB-route-policy-ttt-10] quit

\# Configure OSPFv3 process 1 to
redistribute direct routes permitted by routing policy ttt.

\[DeviceB] ospfv3 100

\[DeviceB-ospfv3-100] import-route direct route-policy
ttt

\[DeviceB-ospfv3-100] quit

**2\.**Configure Device C:

\# Enable SAVA on VLAN-interface 10 (the
LAN-side interface).

\<DeviceC\> system-view

\[DeviceC] interface vlan-interface 10

\[DeviceC-Vlan-interface10] ipv6 sava enable

\# Configure VLAN-interface 10 to
redistribute remote routes with route tag 100\.

\[DeviceC-Vlan-interface10] ipv6 sava import
remote-route-tag 100

\[DeviceC-Vlan-interface10] quit

#### Verifying the configuration

\# Display SAVA
entries on Device C.

\[DeviceC] display ipv6 sava

IPv6 SAVA entry count: 2

Destination:
2000::                                        Prefix length: 64

Interface: Vlan-int10                                     
Flags: L

 

Destination:
2001::                                        Prefix length: 64

Interface: Vlan-int10                                     
Flags: R

The output shows
that Device C created a SAVA entry with prefix 2001::. When Device C receives
an IPv6 packet with prefix 2001:: on VLAN-interface 10, it will forward the
packet.

### Example: Configuring SAVA on border devices indirectly connected the LAN (OSPFv3)

#### Network configuration

As shown in[Figure 6](#_Ref59375854), OSPFv3
runs on the core network. Configure the devices to meet the following
requirements:

Enable SAVA on VLAN-interface
30 of Device B and VLAN-interface 40 on Device D to filter packets based on
SAVA entries.

On Device C,
configure a static route to the LAN with next-hop device Device A. Enable OSPFv3
link tag inheritance to statically tag routes to the LAN.

Configure OSPFv3 on Device
B and Device A to synchronize routes to the LAN. Enable OSPFv3 link tag inheritance
and set the OSPFv3 link tag to dynamically tag routes to the LAN.

Figure 6 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705499_x_Img_x_png_5_2216084_294551_0.png)

‌

Table 2 Interface and IP address assignment

| Device | Interface | IP address | Device | Interface | IP address |
| --- | --- | --- | --- | --- | --- |
| Device A | Vlan-int10 | 2001::2/64 | Device B | Vlan-int30 | 192:168::12:2/120 || Device A | Vlan-int20 | 2000::2/64 | Device B | Vlan-int20 | 192:168::34:3/120 || Device A | Vlan-int30 | 192:168::12:1/120 | — | — | — || Device A | Vlan-int40 | 192:168::22:1/120 | — | — | — || Device C | Vlan-int20 | 192:168::34:4/120 | Device D | Vlan-int40 | 192:168::22:2/120 || Device C | Vlan-int10 | 192:168::46:4/120 | Device D | Vlan-int20 | 192:168::56:5/120 || Device E | Vlan-int20 | 192:168::56:6/120 | — | — | — || Device E | Vlan-int10 | 192:168::46:6/120 | — | — | — |








‌

#### Prerequisites

Assign IPv6
addresses to interfaces on the devices.

#### Procedure

**1\.**Configure Device A:

\# Configure OSPFv3.

\<DeviceA\> system-view

\[DeviceA] ospfv3 1

\[DeviceA-ospfv3-1] router-id 1.1.1.1

\[DeviceA-ospfv3-1] quit

\[DeviceA] interface vlan-interface 10

\[DeviceA-Vlan-interface10] ospfv3 1 area
0.0.0.0

\[DeviceA-Vlan-interface10] quit

\[DeviceA] interface vlan-interface 30

\[DeviceA-Vlan-interface30] ospfv3 1 area
0.0.0.0

\[DeviceA-Vlan-interface130] quit

**2\.**Configure Device B:

\# Configure OSPFv3.

\<DeviceB\> system-view

\[DeviceB] ospfv3 1

\[DeviceB-ospfv3-1] router-id 2.2.2.2

\[DeviceB-ospfv3-1] quit

\[DeviceB] interface vlan-interface 30

\[DeviceB-Vlan-interface30] ospfv3 1 area 0.0.0.0

\[DeviceB-Vlan-interface30] quit

\[DeviceB] interface vlan-interface 20

\[DeviceB- Vlan-interface20] ospfv3 1 area
0.0.0.0

\[DeviceB- Vlan-interface20] quit

\# Enable OSPFv3 link tag inheritance.

\[DeviceB] ospfv3 1

\[DeviceB-ospfv3-1] link-tag inherit enable

\[DeviceB-ospfv3-1] quit

\# Set the OSPFv3 link tag for VLAN-interface
30 to 100\.

\[DeviceB] interface vlan-interface 30

\[DeviceB-Vlan-interface30] ospfv3 link-tag
100

\# Enable SAVA on VLAN-interface 30\.

\[DeviceB-Vlan-interface30] ipv6 sava enable

\# Configure VLAN-interface 30 to
redistribute remote routes with route tag 100\.

\[DeviceB-Vlan-interface30] ipv6 sava import remote-route-tag
100

\[DeviceB-Vlan-interface30] quit

**3\.**Configure Device D:

\# Configure a static route, whose
destination address is 2000:: /64, next hop address is FE80::1 (a link-local
IPv6 address on Device A), and tag value is 100\.

\<DeviceD\> system-view

\[DeviceD] ipv6 route-static 2000:: 64 vlan-interface
40 FE80::1 tag 100

\# Configure routing policy named sava, configure node
10 in permit mode to permit routes with the output interface VLAN-interface 40\.

\[DeviceD] route-policy sava permit node 10

\[DeviceD-route-policy-sava-10] if-match
interface vlan-interface 40

\[DeviceD-route-policy-sava-10] quit

\# Configure OSPFv3.

\[DeviceD] ospfv3 1

\[DeviceD-ospfv3-1] router-id 4.4.4.4

\[DeviceD-ospfv3-1] quit

\[DeviceD] interface vlan-interface 20

\[DeviceD-Vlan-interface20] ospfv3 1 area
0.0.0.0

\[DeviceD- Vlan-interface20] quit

\# Configure OSPFv3 process 1 to
redistribute direct routes permitted by routing policy sava.

\[DeviceD] ospfv3 1

\[DeviceD-ospfv3-1] import-route static route-policy
sava

\# Enable OSPFv3 link tag inheritance.

\[DeviceD-ospfv3-1] link-tag inherit enable

\[DeviceD-ospfv3-1] quit

\# Enable SAVA on VLAN-interface 40\.

\[DeviceD] interface vlan-interface 40

\[DeviceD-Vlan-interface40] ipv6 sava enable

\# Configure VLAN-interface 40 to
redistribute remote routes with route tag 100\.

\[DeviceD-Vlan-interface40] ipv6 sava import remote-route-tag
100

\[DeviceD-Vlan-interface40] quit

**4\.**Configure Device C:

\# Configure OSPFv3.

\<DeviceC\> system-view

\[DeviceC] ospfv3 1

\[DeviceC-ospfv3-1] router-id 3.3.3.3

\[DeviceC-ospfv3-1] quit

\[DeviceC] interface vlan-interface 40

\[DeviceC-Vlan-interface40] ospfv3 1 area
0.0.0.0

\[DeviceC-Vlan-interface40] quit

\[DeviceC] interface vlan-interface 10

\[DeviceC-Vlan-interface10] ospfv3 1 area
0.0.0.0

\[DeviceC-Vlan-interface10] quit

**5\.**Configure Device E:

\# Configure OSPFv3.

\<DeviceE\> system-view

\[DeviceE] ospfv3 1

\[DeviceE-ospfv3-1] router-id 5.5.5.5

\[DeviceE-ospfv3-1] quit

\[DeviceE] interface vlan-interface 20

\[DeviceE-Vlan-interface20] ospfv3 1 area
0.0.0.0

\[DeviceE-Vlan-interface20] quit

\[DeviceE] interface vlan-interface 10

\[DeviceE-Vlan-interface10] ospfv3 1 area
0.0.0.0

\[DeviceE-Vlan-interface10] quit

#### Verifying the configuration

\# Display SAVA
entries on Device D.

\[DeviceD] display ipv6 sava

IPv6 SAVA entry count: 4

Destination:192:168::12:0                                 
Prefix length: 120

Interface: Vlan-int40                                     
Flags: R

 

Destination:192:168::22:0                                 
Prefix length: 120

Interface: Vlan-int40                                     
Flags: L

 

Destination:
2000::                                        Prefix length: 64

Interface: Vlan-int40                                     
Flags: L

 

Destination:
2001::                                        Prefix length: 64

Interface: Vlan-int40                                     
Flags: R

The output shows
that Device D created SAVA entries with prefix 2000:: and 2001::. When Device D
receives an IPv6 packet with prefix 2000:: or 2001:: on VLAN-interface 40, it
will forward the packet.

\# Display SAVA
entries on Device B.

\[DeviceB] display ipv6 sava

IPv6 SAVA entry count: 3

Destination:192:168::12:0                                 
Prefix length: 120

Interface: Vlan-int30                                     
Flags: L

 

Destination:
2000::                                        Prefix length: 64

Interface: Vlan-int30                                     
Flags: R

 

Destination:
2001::                                        Prefix length: 64

Interface: Vlan-int30                                     
Flags: L

The output shows
that Device B created SAVA entries with prefix 2000:: and 2001::. When Device B
receives an IPv6 packet with prefix 2000:: or 2001:: on VLAN-interface 30, it
will forward the packet.

### Example: Configuring SAVA on border devices indirectly connected the LAN (IPv6 IS-IS)

#### Network configuration

As shown in[Figure 7](#_Ref59376180), IPv6
IS-IS runs on the core network. Configure the devices to meet the following
requirements:

Enable SAVA on VLAN-interface
30 of Device B and VLAN-interface 40 of Dervice D to filter packets based on
SAVA entries.

On Device C,
configure a static route to the LAN with next-hop device Device A. Enable IPv6
IS-IS link tag inheritance to statically tag routes to the LAN.

Configure IPv6 IS-IS
on Device B and Device A to synchronize routes to the LAN. Enable IPv6 IS-IS
link tag inheritance and set the IPv6 IS-IS link tag to dynamically tag routes
to the LAN.

Figure 7 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705500_x_Img_x_png_6_2216084_294551_0.png)

‌

Table 3 Interface and IP address assignment

| Device | Interface | IP address | Device | Interface | IP address |
| --- | --- | --- | --- | --- | --- |
| Device A | Vlan-int10 | 2001::2/64 | Device B | Vlan-int30 | 192:168::12:2/120 || Device A | Vlan-int20 | 2000::2/64 | Device B | Vlan-int20 | 192:168::34:3/120 || Device A | Vlan-int30 | 192:168::12:1/120 | — | — | — || Device A | Vlan-int40 | 192:168::22:1/120 | — | — | — || Device C | Vlan-int20 | 192:168::34:4/120 | Device D | Vlan-int40 | 192:168::22:2/120 || Device C | Vlan-int10 | 192:168::46:4/120 | Device D | Vlan-int20 | 192:168::56:5/120 || Device E | Vlan-int20 | 192:168::56:6/120 | — | — | — || Device E | Vlan-int10 | 192:168::46:6/120 | — | — | — |








‌

#### Prerequisites

Assign IPv6
addresses to interfaces on the devices.

#### Procedure

**1\.**Configure Device A:

\# Configure IPv6 IS-IS.

\<DeviceA\> system-view

\[DeviceA] isis 1

\[DeviceA-isis-1] is-level level-2

\[DeviceA-isis-1] network-entity
10.0000.0000.0001.00

\[DeviceA-isis-1] cost-style wide

\[DeviceA-isis-1] address-family ipv6

\[DeviceA-isis-1-ipv6] quit

\[DeviceA-isis-1] quit

\[DeviceA] interface vlan-interface 10

\[DeviceA-Vlan-interface10] isis ipv6 enable
1

\[DeviceA-Vlan-interface10] quit

\[DeviceA] interface vlan-interface 30

\[DeviceA-Vlan-interface30] isis ipv6 enable
1

\[DeviceA-Vlan-interface30] quit

**2\.**Configure Device B:

\# Configure IPv6 IS-IS.

\<DeviceB\> system-view

\[DeviceB] isis 1

\[DeviceB-isis-1] is-level level-2

\[DeviceB-isis-1] network-entity 10.0000.0000.0002.00

\[DeviceB-isis-1] cost-style wide

\[DeviceB-isis-1] address-family ipv6

\[DeviceB-isis-1-ipv6] quit

\[DeviceB-isis-1] quit

\[DeviceB] interface vlan-interface 30

\[DeviceB-Vlan-interface30] isis ipv6 enable
1

\[DeviceB-Vlan-interface30] quit

\[DeviceB] interface vlan-interface 20

\[DeviceB-Vlan-interface20] isis ipv6 enable
1

\[DeviceB-Vlan-interface20] quit

\# Enable IPv6 IS-IS link tag inheritance.

\[DeviceB] isis 1

\[DeviceB-isis-1] address-family ipv6

\[DeviceB-isis-1-ipv6] link-tag inherit
enable

\[DeviceB-isis-1-ipv6] quit

\[DeviceB-isis-1] quit

\# Set the IPv6 IS-IS link tag for VLAN-interface
30 to 100\.

\[DeviceB] interface vlan-interface 30

\[DeviceB-Vlan-interface30] isis ipv6
link-tag 100

\# Enable SAVA on VLAN-interface 30\.

\[DeviceB-Vlan-interface30] ipv6 sava enable

\# Configure VLAN-interface 30 to
redistribute remote routes with route tag 100\.

\[DeviceB-Vlan-interface30] ipv6 sava import remote-route-tag
100

\[DeviceB-Vlan-interface30] quit

**3\.**Configure Device D:

\# Configure a static route, whose
destination address is 2000:: /64, next hop address is FE80::1 (a link-local
IPv6 address on Device A), and tag value is 100\.

\<DeviceD\> system-view

\[DeviceD] ipv6 route-static 2000:: 64 vlan-interface
40 FE80::1 tag 100

\# Configure routing policy named sava, configure node
10 in permit mode to permit routes with the output interface VLAN-interface 40\.

\[DeviceD] route-policy sava permit node 10

\[DeviceD-route-policy-sava-10] if-match
interface vlan-interface 40

\[DeviceD-route-policy-sava-10] quit

\# Configure IPv6 IS-IS.

\[DeviceD] isis 1

\[DeviceD-isis-1] is-level level-2

\[DeviceD-isis-1] network-entity
10.0000.0000.0004.00

\[DeviceD-isis-1] cost-style wide

\[DeviceD-isis-1] address-family ipv6

\[DeviceD-isis-1-ipv6] quit

\[DeviceD-isis-1] quit

\[DeviceD] interface vlan-interface 20

\[DeviceD-Vlan-interface20] isis ipv6 enable
1

\[DeviceD-Vlan-interface20] quit

\# Configure IS-IS process 1 to
redistribute direct routes permitted by routing policy sava.

\[DeviceD] isis 1

\[DeviceD-isis-1] address-family ipv6

\[DeviceD-isis-1-ipv6] import-route static route-policy
sava level-2

\# Enable IPv6 IS-IS link tag inheritance.

\[DeviceD-isis-1-ipv6] link-tag inherit
enable

\[DeviceD-isis-1-ipv6] quit

\[DeviceD-isis-1] quit

\# Enable SAVA on VLAN-interface 40\.

\[DeviceD] interface vlan-interface 40

\[DeviceD-Vlan-interface40] ipv6 sava enable

\# Configure VLAN-interface 40 to
redistribute remote routes with route tag 100\.

\[DeviceD-Vlan-interface40] ipv6 sava import remote-route-tag
100

\[DeviceD-Vlan-interface40] quit

**4\.**Configure Device C:

\# Configure IPv6 IS-IS.

\<DeviceC\> system-view

\[DeviceC] isis 1

\[DeviceC-isis-1] is-level level-2

\[DeviceC-isis-1] network-entity
10.0000.0000.0003.00

\[DeviceC-isis-1] cost-style wide

\[DeviceC-isis-1] address-family ipv6

\[DeviceC-isis-1-ipv6] quit

\[DeviceC-isis-1] quit

\[DeviceC] interface vlan-interface 40

\[DeviceC-Vlan-interface40] isis ipv6 enable
1

\[DeviceC-Vlan-interface40] quit

\[DeviceC] interface vlan-interface 10

\[DeviceC-Vlan-interface10] isis ipv6 enable
1

\[DeviceC-Vlan-interface10] quit

**5\.**Configure Device E:

\# Configure IPv6 IS-IS.

\<DeviceE\> system-view

\[DeviceE] isis 1

\[DeviceE-isis-1] is-level level-2

\[DeviceE-isis-1] network-entity
10.0000.0000.0005.00

\[DeviceE-isis-1] cost-style wide

\[DeviceE-isis-1] address-family ipv6

\[DeviceE-isis-1-ipv6] quit

\[DeviceE-isis-1] quit

\[DeviceE] interface vlan-interface 20

\[DeviceE-Vlan-interface20] isis ipv6 enable
1

\[DeviceE-Vlan-interface20] quit

\[DeviceE] interface vlan-interface 10

\[DeviceE-Vlan-interface10] isis ipv6 enable
1

\[DeviceE-Vlan-interface10] quit

#### Verifying the configuration

\# Display SAVA
entries on Device D.

\[DeviceD] display ipv6 sava

IPv6 SAVA entry count: 3

Destination:192:168::22:0                                 
Prefix length: 120

Interface: Vlan-int40                                     
Flags: L

 

Destination: 2000::                                        Prefix
length: 64

Interface: Vlan-int40                                     
Flags: L

 

Destination:
2001::                                        Prefix length: 64

Interface: Vlan-int40                                  
   Flags: R

The output shows
that Device D created SAVA entries with prefix 2000:: and 2001::. When Device D
receives an IPv6 packet with prefix 2000:: or 2001:: on VLAN-interface 40, it
will forward the packet.

\# Display SAVA
entries on Device B.

\[DeviceB] display ipv6 sava

IPv6 SAVA entry count: 3

Destination:192:168::12:0                                 
Prefix length: 120

Interface: Vlan-int30                                     
Flags: L

 

Destination: 2000::                                       
Prefix length: 64

Interface: Vlan-int30                                     
Flags: R

 

Destination:
2001::                                        Prefix length: 64

Interface: Vlan-int30                          
           Flags: L

The output shows
that Device B created SAVA entries with prefix 2000:: and 2001::. When Device D
receives an IPv6 packet with prefix 2000:: or 2001:: on VLAN-interface 30, it
will forward the packet.

### Example: Configuring SAVA on inter-AS border devices indirectly connected the LAN

#### Network configuration

As shown in[Figure 8](#_Ref60312116),
configure the devices to meet the following requirements:

Configure SAVA VLAN-interface
30 of Device B and VLAN-interface 40 of Dervice D to filter packets based on
SAVA entries.

Configure remote
route tagging. Device B and Device C in AS 100 and Device D and Device E in AS
200 exchange routes through IGP (for example, OSPFv3). Device C and Device E in
different ASs exchange routes through BGP. The devices create SAVA entries
based on the synchronized remote routes.

Figure 8 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705501_x_Img_x_png_7_2216084_294551_0.png)

‌

Table 4 Interface and IP address assignment

| Device | Interface | IP address | Device | Interface | IP address |
| --- | --- | --- | --- | --- | --- |
| Device A | Vlan-int10 | 2001::2/64 | Device B | Vlan-int30 | 192:168::12:2/120 || Device A | Vlan-int20 | 2000::2/64 | Device B | Vlan-int20 | 192:168::23:2/120 || Device A | Vlan-int30 | 192:168::12:1/120 | — | — | — || Device A | Vlan-int40 | 192:168::22:1/120 | — | — | — || Device C | Vlan-int20 | 192:168::23:3/120 | Device D | Vlan-int40 | 192:168::22:2/120 || Device C | Vlan-int10 | 192:168::34:3/120 | Device D | Vlan-int20 | 192:168::45:4/120 || Device E | Vlan-int20 | 192:168::45:5/120 | — | — | — || Device E | Vlan-int10 | 192:168::34:4/120 | — | — | — |








‌

#### Prerequisites

Assign IPv6
addresses to interfaces on the devices.

#### Procedure

**1\.**Configure Device B:

\# Configure a static route, whose
destination address is 2001:: 64, next hop address is FE80::1 (a link-local
IPv6 address on Device A), and tag value is 100\.

\<DeviceB\> system-view

\[DeviceB] ipv6 route-static 2001:: 64 vlan-interface
30 FE80::1 tag 100

\# Configure routing policy sava and
configure node 10 in permit mode for the routing policy to permit routes with
the output interface VLAN-interface 30\.

\[DeviceB] route-policy sava permit node 10

\[DeviceB-route-policy-sava-10] if-match
interface vlan-interface 30

\[DeviceB-route-policy-sava-10] quit

\# Configure OSPFv3.

\[DeviceB] ospfv3 1

\[DeviceB-ospfv3-1] router-id 2.2.2.2

\[DeviceB-ospfv3-1] quit

\[DeviceB] interface vlan-interface 20

\[DeviceB-Vlan-interface20] ospfv3 1 area
0.0.0.0

\[DeviceB-Vlan-interface20] quit

\# Configure OSPFv3 to redistribute remote
routes with route tag 100\.

\[DeviceB] ospfv3 1

\[DeviceB-ospfv3-1] import-route static
route-policy sava

\[DeviceB-ospfv3-1] quit

\# Enable SAVA on VLAN-interface 30\.

\[DeviceB] interface vlan-interface 30

\[DeviceB-Vlan-interface30] ipv6 sava enable

\# Configure VLAN-interface 30 to
redistribute remote routes with route tag 100\.

\[DeviceB-Vlan-interface30] ipv6 sava import
remote-route-tag 100

\[DeviceB-Vlan-interface30] quit

**2\.**Configure Device D:

\# Configure a static route, whose destination
address is 2000:: 64, next hop address is FE80::1 (a link-local IPv6 address on
Device A), and tag value is 100\.

\<DeviceD\> system-view

\[DeviceD] ipv6 route-static 2000:: 64 vlan-interface
40 FE80::1 tag 100

\# Configure routing policy named sava, configure node
10 in permit mode to permit routes with the output interface VLAN-interface 40\.

\[DeviceD] route-policy sava permit node 10

\[DeviceD-route-policy-sava-10] if-match
interface vlan-interface 40

\[DeviceD-route-policy-sava-10] quit

\# Configure OSPFv3.

\[DeviceD] ospfv3 1

\[DeviceD-ospfv3-1] router-id 4.4.4.4

\[DeviceD-ospfv3-1] quit

\[DeviceD] interface vlan-interface 20

\[DeviceD-Vlan-interface20] ospfv3 1 area
0.0.0.0

\[DeviceD-Vlan-interface20] quit

\# Configure OSPFv3 process 1 to
redistribute remote routes permitted by routing policy sava.

\[DeviceD] ospfv3 1

\[DeviceD-ospfv3-1] import-route static
route-policy sava

\[DeviceD-ospfv3-1] quit

\# Enable SAVA on VLAN-interface 40\.

\[DeviceD] interface vlan-interface 40

\[DeviceD-Vlan-interface40] ipv6 sava enable

\# Configure VLAN-interface 40 to
redistribute remote routes with route tag 100\.

\[DeviceD-Vlan-interface40] ipv6 sava import
remote-route-tag 100

\[DeviceD-Vlan-interface40] quit

**3\.**Configure Device C:

\# Configure routing policy named sava-exp,
configure node 0 in permit mode to permit routes with tag 100 and to set the 10:10
community attribute for BGP.

\[DeviceC] route-policy sava-exp permit node
0

\[DeviceC-route-policy-sava-exp-0] if-match
tag 100

\[DeviceC-route-policy-sava-exp-0] apply
community 10:10

\[DeviceC-route-policy-sava-exp-0] quit

\# Enable BGP to exchange routing
information for IPv6 address family with peer 192:168::34:4 in AS 200\.

\[DeviceC] bgp 100

\[DeviceC-bgp-default] router-id 3.3.3.3

\[DeviceC-bgp-default] peer 192:168::34:4 as
200

\[DeviceC-bgp-default] address-family ipv6

\[DeviceC-bgp-default-ipv6] peer
192:168::34:4 enable

\[DeviceC-bgp-default-ipv6] peer
192:168::34:4 advertise-community

\# Configure BGP to redistribute routes
from OSPFv3 process 1, and apply routing policy sava-exp to routes outgoing to peer 192:168::34:4.


\[DeviceC-bgp-default-ipv6] import-route
ospfv3 1

\[DeviceC-bgp-default-ipv6] peer
192:168::34:4 route-policy sava-exp export

\[DeviceC-bgp-default-ipv6] quit

\[DeviceC-bgp-default] quit

\# Configure basic community list 1 to
permit routes with the 10:10 community attribute.

\[DeviceC] ip community-list 1 permit 10:10

\# Configure a routing policy named sava-imp, and configure
node 10 in permit mode for the routing policy to use community list 1 to match
BGP routes and to set the tag to 100\.

\[DeviceC] route-policy sava-imp permit node
0

\[DeviceC-route-policy-sava-imp-0] if-match
community 1

\[DeviceC-route-policy-sava-imp-0] apply tag
100

\[DeviceC-route-policy-sava-imp-0] quit

\# Apply routing policy sava-imp to
routes incoming from peer 192:168::34:4.

\[DeviceC] bgp 100

\[DeviceC-bgp-default] address-family ipv6

\[DeviceC-bgp-default-ipv6] peer
192:168::34:4 route-policy sava-imp import

\[DeviceC-bgp-default-ipv6] quit

\[DeviceC-bgp-default] quit

\# Configure OSPFv3.

\[DeviceC] ospfv3 1

\[DeviceC-ospfv3-1] router-id 3.3.3.3

\[DeviceC-ospfv3-1] quit

\[DeviceC] interface vlan-interface 20

\[DeviceC-Vlan-interface20] ospfv3 1 area
0.0.0.0

\[DeviceC-Vlan-interface20] quit

\# Configure OSPFv3 process 1 to
redistribute routes from IPv6 BGP.

\[DeviceC] ospfv3 1

\[DeviceC-ospfv3-1] import-route bgp4\+

\[DeviceC-ospfv3-1] quit

**4\.**Configure Device E:

\# Configure routing policy named sava-exp,
configure node 0 in permit mode to permit routes with tag 100 and to set the 10:10
community attribute for BGP.

\[DeviceE] route-policy sava-exp permit node
0

\[DeviceE-route-policy-sava-exp-0] if-match
tag 100

\[DeviceE-route-policy-sava-exp-0] apply
community 10:10

\[DeviceE-route-policy-sava-exp-0] quit

\# Enable BGP to exchange routing
information for IPv6 address family with peer 192:168::34:3 in AS 100\.

\[DeviceE] bgp 200

\[DeviceE-bgp-default] router-id 5.5.5.5

\[DeviceE-bgp-default] peer 192:168::34:3 as
100

\[DeviceE-bgp-default] address-family ipv6

\[DeviceE-bgp-default-ipv6] peer
192:168::34:3 enable

\[DeviceE-bgp-default-ipv6] peer
192:168::34:3 advertise-community

\# Configure BGP to redistribute routes
from OSPFv3 process 1, and apply routing policy sava-exp to routes outgoing to peer 192:168::34:3.


\[DeviceE-bgp-default-ipv6] import-route
ospfv3

\[DeviceE-bgp-default-ipv6] peer
192:168::34:3 route-policy sava-exp export

\[DeviceE-bgp-default-ipv6] quit

\[DeviceE-bgp-default] quit

\# Configure basic community list 1 to
permit routes with the 10:10 community attribute.

\[DeviceE] ip community-list 1 permit 10:10

\# Configure a routing policy named sava-imp, and configure
node 10 in permit mode for the routing policy to use community list 1 to match
BGP routes and to set the tag to 100\.

\[DeviceE] route-policy sava-imp permit node
0

\[DeviceE-route-policy-sava-imp-0] if-match
community 1

\[DeviceE-route-policy-sava-imp-0] apply tag
100

\[DeviceE-route-policy-sava-imp-0] quit

\# Apply routing policy sava-imp to
routes incoming from peer 192:168::34:4.

\[DeviceE] bgp 200

\[DeviceE-bgp-default] address-family ipv6

\[DeviceE-bgp-default-ipv6] peer
192:168::34:3 route-policy sava-imp import

\[DeviceE-bgp-default-ipv6] quit

\[DeviceE-bgp-default] quit

\# Configure OSPFv3.

\[DeviceE] ospfv3 1

\[DeviceE-ospfv3-1] router-id 5.5.5.5

\[DeviceE-ospfv3-1] quit

\[DeviceE] interface vlan-interface 20

\[DeviceE-Vlan-interface20] ospfv3 1 area
0.0.0.0

\[DeviceE- Vlan-interface20] quit

\# Configure OSPFv3 process 1 to
redistribute routes from IPv6 BGP.

\[DeviceE] ospfv3 1

\[DeviceE-ospfv3-1] import-route bgp4\+

\[DeviceE-ospfv3-1] quit

#### Verifying the configuration

\# Display SAVA
entries on Device D.

\[DeviceD] display ipv6 sava

IPv6 SAVA entry count: 3

Destination:192:168::22:0                                 
Prefix length: 120

Interface: Vlan-int40                                     
Flags: L

 

Destination:
2000::                                        Prefix length: 64

Interface: Vlan-int40                                     
Flags: L

 

Destination:
2001::                                        Prefix length: 64

Interface: Vlan-int40                                     
Flags: R

The output shows
that Device D created SAVA entries with prefix 2000:: and 2001::. When Device D
receives an IPv6 packet with prefix 2000:: or 2001:: on VLAN-interface 40, it
will forward the packet.

\# Display SAVA
entries on Device B.

\[DeviceB] display ipv6 sava

IPv6 SAVA entry count: 3

Destination:192:168::12:0                                 
Prefix length: 120

Interface: Vlan-int30                                     
Flags: L

 

Destination: 2000::                                        Prefix
length: 64

Interface: Vlan-int30                                     
Flags: R

 

Destination:
2001::                                        Prefix length: 64

Interface: Vlan-int30                                      
Flags: L

The output shows
that Device B created SAVA entries with prefix 2000:: and 2001::. When Device B
receives an IPv6 packet with prefix 2000:: or 2001:: on VLAN-interface 30, it
will forward the packet.

