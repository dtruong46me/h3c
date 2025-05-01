
# EVPN VXLAN overview

EVPN VXLAN uses EVPN routes for automatic
VXLAN tunnel establishment and assignment and MAC reachability information advertisement
in the control plane and uses VXLAN for forwarding in the data plane.

## Network model

As shown in [Figure 1](#_Ref452370269), EVPN
uses the VXLAN technology for traffic forwarding in the data plane. The
transport edge devices assign user terminals to different VXLANs, and then
forward traffic between sites for user terminals by using VXLAN tunnels. The
transport edge devices are VXLAN tunnel endpoints (VTEPs). 

The EVPN network sites and transport
network can be IPv4 or IPv6 networks. Supported user terminals include PCs,
wireless terminals, and VMs on servers.

 

|  | NOTE:  This document uses VMs as examples to describe the mechanisms of EVPN. The mechanisms do not differ between different kinds of user terminals. |
| --- | --- |

 

A VTEP uses ESs, VSIs, and VXLAN tunnels to
provide VXLAN services:

·     Ethernet segment (ES)—An ES is a link that connects a site to a VTEP. Each ES is uniquely
identified by an Ethernet segment identifier (ESI).

·     VSI—A virtual switch instance is a virtual Layer 2 switched domain.
Each VSI provides switching services only for one VXLAN. VSIs learn MAC
addresses and forward frames independently of one another. User terminals in
different sites have Layer 2 connectivity if they are in the same VXLAN. A
VXLAN is identified by a 24-bit VXLAN ID which is also called the virtual
network identifier (VNI). A VXLAN corresponds to an EVPN instance. 

·     VXLAN tunnel—Logical point-to-point tunnels between VTEPs over the transport
network. Each VXLAN tunnel can trunk multiple VXLANs.

All VXLAN processing is performed on VTEPs.
The ingress VTEP encapsulates VXLAN traffic in the VXLAN, outer UDP, and outer
IP headers, and forwards the traffic through VXLAN tunnels. The egress VTEP
removes the VXLAN encapsulation and forwards the traffic to the destination. Transport
network devices (for example, the P device in [Figure 1](#_Ref452370269))
forward VXLAN traffic only based on the outer IP header of VXLAN packets. 

Figure 1 EVPN
network model

![](https://resource.h3c.com/en/202407/12/20240712_11705978_x_Img_x_png_0_2216147_294551_0.png)

‌

## Configuration automation

If EVPN is used for Layer 2 forwarding, VTEPs
use the following BGP EVPN routes to discover VTEP neighbors, establish VXLAN
tunnels, and assign the tunnels to VXLANs:

·     IMET route—VTEPs advertise the VXLAN IDs they have through IMET routes. If two
VTEPs have the same VXLAN ID, they automatically establish a VXLAN tunnel and
assign the tunnel to the VXLAN.

·     MAC/IP advertisement route—VTEPs advertise local MAC addresses and VXLAN IDs through MAC/IP
advertisement routes. If two VTEPs have the same VXLAN ID, they automatically
establish a VXLAN tunnel and assign the tunnel to the VXLAN.

If EVPN is used for Layer 3 forwarding,
VTEPs use the following BGP EVPN routes to discover VTEP neighbors, establish
VXLAN tunnels, and assign the tunnels to VXLANs:

·     IMET route—VTEPs advertise the VXLAN IDs they have through IMET routes. If two
VTEPs have the same VXLAN ID, they automatically establish a VXLAN tunnel and
assign the tunnel to the VXLAN.

·     MAC/IP advertisement route
and IP prefix advertisement route—In the EVPN
gateway deployment, VTEPs advertise MAC/IP advertisement routes or IP prefix
advertisement routes with the export targets. When a VTEP receives a route, it
compares the export targets of the route with the local import targets. If the
route targets match, the VTEP establishes a VXLAN tunnel with the remote VTEP
and associates the tunnel with the L3 VXLAN ID of the corresponding VPN
instance. For more information about the L3 VXLAN ID, see "[Distributed EVPN gateway deployment](#_Ref452468311)."

## Assignment of traffic to VXLANs

### Traffic from the local site to a remote site

The VTEP uses an Ethernet service instance
to match customer traffic on a site-facing interface. The VTEP assigns customer
traffic to a VXLAN by mapping the Ethernet service instance to a VSI. 

An Ethernet service instance is identical
to an attachment circuit (AC) in L2VPN. An Ethernet service instance matches a
list of VLANs on a Layer 2 Ethernet interface by using a frame match criterion.
The frame match criterion specifies the characteristics of traffic from the
VLANs, such as tagging status and VLAN IDs.

As shown in [Figure 2](#_Ref376422245), Ethernet
service instance 1 matches VLAN 2 and is mapped to VSI A (VXLAN 10). When a
frame from VLAN 2 arrives, the VTEP assigns the frame to VXLAN 10, and looks up
VSI A's MAC address table for the outgoing interface.

Figure 2 Identifyingtraffic from the local site

![](https://resource.h3c.com/en/202407/12/20240712_11705979_x_Img_x_png_1_2216147_294551_0.png)

 

### Traffic from a remote site to the local site

When a VXLAN packet arrives at a VXLAN
tunnel interface, the VTEP uses the VXLAN ID in the packet to identify its
VXLAN.

## Layer 2 forwarding

### MAC learning

The VTEP performs Layer 2 forwarding based
on a VSI's MAC address table. The VTEP learns MAC addresses by using the
following methods:

·     Local MAC learning—The VTEP automatically learns the source MAC addresses of frames
sent from the local site. The outgoing interfaces of local MAC address entries
are site-facing interfaces on which the MAC addresses are learned.

·     Remote MAC learning—The VTEP uses MP-BGP to advertise local MAC reachability
information to remote sites and learn MAC reachability information from remote
sites. The outgoing interfaces of MAC address entries advertised from a remote
site are VXLAN tunnel interfaces.

### Unicast

As shown in [Figure 3](#_Ref313265758), the
VTEP performs typical Layer 2 forwarding for known unicast traffic within the
local site.

Figure 3 Intra-site unicast

![](https://resource.h3c.com/en/202407/12/20240712_11705990_x_Img_x_png_2_2216147_294551_0.png)

 

As shown in [Figure 4](#_Ref449001496), the
following process applies to a known unicast frame between sites: 

**1\.**The source VTEP encapsulates the Ethernet frame
in the VXLAN/UDP/IP header. 

In the outer IP header, the source IP
address is the source VTEP's VXLAN tunnel source IP address. The destination IP
address is the VXLAN tunnel destination IP address.

**2\.**The source VTEP forwards the encapsulated
packet out of the outgoing VXLAN tunnel interface found in the VSI's MAC
address table.

**3\.**The intermediate transport devices (P
devices) forward the packet to the destination VTEP by using the outer IP
header.

**4\.**The destination VTEP removes the headers on
top of the inner Ethernet frame. It then performs MAC address table lookup in
the VXLAN's VSI to forward the frame out of the matching outgoing interface.

Figure 4 Inter-site unicast

![](https://resource.h3c.com/en/202407/12/20240712_11706001_x_Img_x_png_3_2216147_294551_0.png)

 

### Flood

As shown in [Figure 5](#_Ref375731306), a
VTEP floods a broadcast, multicast, or unknown unicast frame to all site-facing
interfaces and VXLAN tunnels in the VXLAN, except for the incoming interface.
The source VTEP replicates the flood frame, and then sends one replica to the
destination IP address of each VXLAN tunnel in the VXLAN. Each destination VTEP
floods the inner Ethernet frame to all the site-facing interfaces in the VXLAN.
To avoid loops, the destination VTEPs do not flood the frame to VXLAN tunnels.

Figure 5 Forwarding of flood traffic

![](https://resource.h3c.com/en/202407/12/20240712_11706006_x_Img_x_png_4_2216147_294551_0.png)

 

## Centralized EVPN gateway deployment

| IMPORTANT | IMPORTANT:  This section uses IPv4 sites as examples to describe the Layer 3 forwarding process of EVPN networks. The Layer 3 forwarding process does not differ between IPv4 and IPv6 sites. |
| --- | --- |

 

Centralized EVPN gateway deployment uses
one VTEP to provide Layer 3 forwarding for VXLANs. The VTEP uses virtual Layer
3 VSI interfaces as gateway interfaces for VXLANs. Typically, the
gateway-collocated VTEP connects to other VTEPs and the external network. To
use this design, make sure the gateway has sufficient bandwidth and processing
capability.

As shown in [Figure 6](#_Ref452469978), a
VTEP acts as a gateway for VMs in the VXLANs. The VTEP both terminates the
VXLANs and performs Layer 3 forwarding for the VMs. The network uses the
following process to forward Layer 3 traffic from a VM to the destination:

**1\.**The VM sends an ARP request to obtain the MAC
address of the VSI interface that acts as the gateway, and then sends the Layer
3 traffic to the centralized EVPN gateway.

**2\.**The local VTEP looks up the matching VSI's MAC
address table and forwards the traffic to the centralized EVPN gateway through
a VXLAN tunnel.

**3\.**The centralized EVPN gateway removes the
VXLAN encapsulation and forwards the traffic at Layer 3\.

**4\.**The centralized EVPN gateway forwards the
replies sent by the destination node to the VM based on the ARP entry for the
VM.

Figure 6 Example of centralized EVPN gateway deployment

![](https://resource.h3c.com/en/202407/12/20240712_11706008_x_Img_x_png_6_2216147_294551_0.png)

 

## Distributed EVPN gateway deployment

| IMPORTANT | IMPORTANT:  This section uses IPv4 sites as examples to describe the Layer 3 forwarding process of EVPN networks. The Layer 3 forwarding process does not differ between IPv4 and IPv6 sites. |
| --- | --- |

 

### About distributed EVPN gateway deployment

As shown in[Figure 7](#_Ref452380681),
each site's VTEP acts as a gateway to perform Layer 3 forwarding for the VXLANs
of the local site. A VTEP acts as a border gateway to the Layer 3 network for
the VXLANs.

Figure 7 Distributed EVPN gateway placement design

![](https://resource.h3c.com/en/202407/12/20240712_11706010_x_Img_x_png_8_2216147_294551_0.png)

‌

A distributed EVPN gateway supports the
following traffic forwarding modes:

·     Asymmetric IRB—The ingress gateway performs Layer 2 and Layer 3 lookups and the
egress gateway performs only Layer 2 forwarding.

·     Symmetric IRB—Both the ingress and egress gateways perform Layer 2 and Layer 3
lookups.

### Symmetric IRB

#### Basic concepts

Symmetric IRB introduces the following
concepts:

·     L3 VXLAN ID—Also called L3 VNI. An L3 VXLAN ID identifies the traffic of a
routing domain where devices have Layer 3 reachability. An L3 VXLAN ID is
associated with one VPN instance. Distributed EVPN gateways use VPN instances
to isolate traffic of different services on VXLAN tunnel interfaces.

·     Router MAC address—Each distributed EVPN gateway has a unique router MAC address used
for inter-gateway forwarding. The MAC addresses in the inner Ethernet header of
VXLAN packets are router MAC addresses of distributed EVPN gateways. 

#### VSI interfaces

As shown in [Figure 8](#_Ref452470327), each
distributed EVPN gateway has the following types of VSI interfaces:

·     VSI interface as a gateway
interface of a VXLAN—The VSI interface acts as
the gateway interface for VMs in a VXLAN. The VSI interface is associated with
a VSI and a VPN instance. On different distributed EVPN gateways, the VSI
interface of a VXLAN use the same IP address to provide services.

·     VSI interface associated
with an L3 VXLAN ID—The VSI interface is
associated with a VPN instance and assigned an L3 VXLAN ID. VSI interfaces
associated with the same VPN instance share an L3 VXLAN ID.

A border gateway only has VSI interfaces
that are associated with an L3 VXLAN ID.

Figure 8 Example of distributedEVPN gateway deployment

![](https://resource.h3c.com/en/202407/12/20240712_11706011_x_Img_x_png_9_2216147_294551_0.png)

‌

#### Layer 3 forwarding entry learning

A distributed EVPN gateway forwards Layer 3
traffic based on FIB entries generated from BGP EVPN routes and ARP
information.

A VTEP advertises an external route
imported in the EVPN address family through MP-BGP. A remote VTEP adds the
route to the FIB table of a VPN instance based on the L3 VXLAN ID carried in
the route. In the FIB entry, the outgoing interface is a VXLAN tunnel
interface, and the next hop is the peer VTEP address in the NEXT\_HOP attribute
of the route.

A VTEP has the following types of ARP
information:

·     Local ARP information—ARP information of VMs in the local site. The VTEP snoops GARP
packets, RARP packets, and ARP requests for the gateway MAC address to learn
the ARP information of the senders and generates ARP entries and FIB entries.
In an ARP or FIB entry, the outgoing interface is the site-facing interface
where the packet is received, and the VPN instance is the instance associated
with the corresponding VSI interface.

·     Remote ARP information—ARP information of VMs in remote sites. Each VTEP uses MP-BGP to
advertise its local ARP information with L3 VXLAN IDs in routes to remote
sites. A VTEP generates only FIB entries for the remote ARP information. A FIB
entry contains the following information:

¡     Outgoing
interface: VSI interface associated with the L3 VXLAN ID.

¡     Next
hop: Peer VTEP address in the NEXT\_HOP attribute of the route.

¡     VPN
instance: VPN instance associated with the L3 VXLAN ID.

The VTEP then creates an ARP entry for
the next hop in the FIB entry.

#### Traffic forwarding

A distributed EVPN gateway can work in one
of the following mode:

·     Switching and routing mode—Forwards Layer 2 traffic based on the MAC address table and
forwards Layer 3 traffic based on the FIB table. In this mode, you need to
enable ARP flood suppression on the distributed EVPN gateway to reduce
flooding. 

·     Routing mode— Forwards both Layer 2 and Layer 3 traffic based on the FIB table. In
this mode, you need to enable local proxy ARP on the distributed EVPN gateway.

For more information about MAC address
table-based Layer 2 forwarding, see "[Unicast](#_Ref452384896)."

[Figure 9](#_Ref435278517) shows
the intra-site Layer 3 forwarding process.

**1\.**The source VM sends an ARP request to obtain
the MAC address of the destination VM.

**2\.**The gateway replies to the source VM with
the MAC address of the VSI interface associated with the source VM's VSI.

**3\.**The source VM sends a Layer 3 packet to the
gateway.

**4\.**The gateway looks up the FIB table of the
VPN instance associated with the source VM's VSI and finds the matching
outgoing site-facing interface.

**5\.**The gateway processes the Ethernet header of
the Layer 3 packet as follows:

¡     Replaces
the destination MAC address with the destination VM's MAC address.

¡     Replaces
the source MAC address with the VSI interface's MAC address.

**6\.**The gateway forwards the Layer 3 packet to
the destination VM.

Figure 9 Intra-site Layer 3 forwarding

![](https://resource.h3c.com/en/202407/12/20240712_11705980_x_Img_x_png_10_2216147_294551_0.png)

 

[Figure 10](#_Ref452385120)
shows the inter-site Layer 3 forwarding process.

**1\.**The source VM sends an ARP request to obtain
the MAC address of the destination VM.

**2\.**The gateway replies to the source VM with
the MAC address of the VSI interface associated with the source VM's VSI.

**3\.**The source VM sends a Layer 3 packet to the
gateway.

**4\.**The gateway looks up the FIB table of the
VPN instance associated with the source VM's VSI and finds the matching outgoing
VSI interface.

**5\.**The gateway processes the Ethernet header of
the Layer 3 packet as follows:

¡     Replaces
the destination MAC address with the destination gateway's router MAC address.

¡     Replaces
the source MAC address with its own router MAC address.

**6\.**The gateway adds VXLAN encapsulation to the
Layer 3 packet and forwards the packet to the destination gateway. The
encapsulated VXLAN ID is the L3 VXLAN ID of the corresponding VPN instance.

**7\.**The destination gateway identifies
the VPN instance of the packet based on the L3 VXLAN ID and removes the VXLAN
encapsulation. Then the gateway forwards the packet based on the matching ARP
entry.

Figure 10 Inter-site Layer 3 forwarding

![](https://resource.h3c.com/en/202407/12/20240712_11705981_x_Img_x_png_11_2216147_294551_0.png)

 

#### Communication between private and public networks

A distributed EVPN gateway uses the public
instance to perform Layer 3 forwarding for the public network and to enable
communication between private and public networks. The public instance is
similar to a VPN instance. A distributed EVPN gateway processes traffic of the
public instance in the same way it does for a VPN instance. For the public
instance to work correctly, you must configure an RD, an L3 VXLAN ID, and route
targets for it. If a VSI interface is not associated with any VPN instance, the
VSI interface belongs to the public instance.

### Asymmetric IRB

#### VSI interfaces

Asymmetric IRB uses the same distributed
EVPN gateway deployment as symmetric IRB.

As shown in [Figure 8](#_Ref452470327), each
distributed EVPN gateway has the following types of VSI interfaces:

·     VSI interface as a gateway
interface of a VXLAN—The VSI interface is
associated with a VSI and a VPN instance. On different distributed EVPN
gateways, the VSI interface of a VXLAN must use different IP addresses to
provide services.

·     VSI interface associated
with an L3 VXLAN ID—The VSI interface acts as the
gateway for VMs in a VXLAN to communicate with the external network through the
border gateway. The VSI interface is associated with a VPN instance and
assigned an L3 VXLAN ID. VSI interfaces associated with the same VPN instance
share an L3 VXLAN ID.

A border gateway only has VSI interfaces
that are associated with an L3 VXLAN ID.

#### Layer 3 forwarding

Asymmetric IRB supports only Layer 3
forwarding in the same VXLAN on distributed EVPN gateways.

After a distributed EVPN gateway learns ARP
information about local VMs, it advertises the information to other distributed
EVPN gateways through MAC/IP advertisement routes. Other distributed EVPN
gateways generate FIB entries based on the advertised ARP information.

As shown in [Figure 11](#_Ref42706771), VM 1
and VM 2 belong to VXLAN 10 and they can reach each other at Layer 3 through
the distributed EVPN gateways. The distributed EVPN gateways use the following
process to perform Layer 3 forwarding in asymmetric IRB mode when VM 1 sends a
packet to VM 2:

**1\.**After GW 1 receives the packet from VM 1, it
finds that the destination MAC address is itself. Then, GW 1 removes the Layer
2 frame header and looks up the FIB table for the destination IP address.

**2\.**GW 1 matches the packet to the FIB entry
generated based on the ARP information of VM 2\.

**3\.**GW 1 encapsulates the packet source and
destination MAC addresses as the MAC addresses of GW 1 and VM 2, respectively.
Then, GW 1 adds VXLAN encapsulation to the packet and forwards the packet to GW
2 through a VXLAN tunnel.

**4\.**GW 2 removes the VXLAN encapsulation from
the packet, and performs Layer 2 forwarding in VXLAN 10 by looking up the MAC
address table for the destination MAC address.

**5\.**GW 2 forwards the packet to VM 2 based on
the MAC address table lookup result.

Figure 11 Layer 3 forwarding in the same VXLAN (asymmetric IRB)

![](https://resource.h3c.com/en/202407/12/20240712_11705982_x_Img_x_png_12_2216147_294551_0.png)

‌

## EVPN VXLAN multihoming

| IMPORTANT | IMPORTANT:  EVPN multihoming supports only IPv4 underlay networks. |
| --- | --- |

‌

### About EVPN multihoming

As shown in [Figure 12](#_Ref505864996), EVPN
supports deploying multiple VTEPs at a site for redundancy and high
availability. On the redundant VTEPs, Ethernet links connected to the site form
an Ethernet segment (ES) that is uniquely identified by an Ethernet segment
identifier (ESI). 

Figure 12 EVPN multihoming

![](https://resource.h3c.com/en/202407/12/20240712_11705984_x_Img_x_png_14_2216147_294551_0.png)

‌

### DF election

To prevent redundant VTEPs from sending
duplicate flood traffic to a multihomed site, a designated forwarder (DF) is
elected from the VTEPs for each AC to forward flood traffic to the AC. VTEPs
that fail the election are assigned the backup designated forwarder (BDF) role.
BDFs of an AC do not forward flood traffic to the AC.

A remote VTEP takes part in the DF election
of a multihomed site. Redundant VTEPs of the site send Ethernet segment routes
to the remote VTEP to advertise ES and VTEP IP mappings. Then, the VTEPs select
a DF for each AC based on the ES and VTEP IP mappings by using the following
procedure:

**1\.**Arrange source IP addresses in Ethernet segment routes with the same
ESI in ascending order and assign a sequence number to each IP address,
starting from 0\.

**2\.**Divide the lowest VLAN ID permitted on an AC
by the number of the redundant VTEPs, and match the reminder to the sequence
numbers of IP addresses.

**3\.**Assign the DF role to the VTEP that uses the
IP address with the matching sequence number.

The following uses AC 1 in [Figure 13](#_Ref505868856) as
an example to explain the DF election procedure:

**1\.**VTEP 1 and VTEP 2 send Ethernet segment
routes to VTEP 3\.

**2\.**Sequence numbers 0 and 1 are assigned to IP
addresses 1.1.1.1 and 2.2.2.2 in the Ethernet segment routes, respectively.

**3\.**The VTEPs divide 4 (the lowest VLAN ID
permitted by AC 1\) by 2 (the number of redundant VTEPs), and match the reminder
0 to the sequence numbers of the IP addresses.

**4\.**The DF role is assigned to VTEP 1 at
1.1.1.1.

Figure 13 DF election

![](https://resource.h3c.com/en/202407/12/20240712_11705985_x_Img_x_png_15_2216147_294551_0.png)

‌

### Split horizon

In a multihomed site, a VTEP forwards multicast,
broadcast, and unknown unicast frames received from ACs out of all site-facing interfaces
and VXLAN tunnels in the corresponding VXLAN, except for the incoming
interface. As a result, the other VTEPs at the site receive these flood frames
and forward them to site-facing interfaces, which causes duplicate floods and
loops. EVPN introduces split horizon to resolve this issue. Split horizon
disables a VTEP from forwarding flood traffic received from another local VTEP
to site-facing interfaces if an ES on that local VTEP has the same ESI as these
interfaces. As shown in [Figure 14](#_Ref505876429), both
VTEP 1 and VTEP 2 have ES 1\. When receiving flood traffic from VTEP 1, VTEP 2
does not forward the traffic to interfaces with ESI 1\.

Figure 14 Split horizon

![](https://resource.h3c.com/en/202407/12/20240712_11705986_x_Img_x_png_16_2216147_294551_0.png)

‌

### Redundancy mode

The device supports the all-active
redundancy mode of EVPN multihoming. This mode allows all redundant VTEPs at a
multihomed site to forward broadcast, multicast, and unknown unicast traffic. 

·     For flood frames received from remotes sites, a
VTEP forwards them to the ACs of which it is the DF.

·     For flood frames received from the local site, a
VTEP forwards them out of all site-facing interfaces and VXLAN tunnels in the
corresponding VXLAN, except for the incoming interfaces. For flood frames to be
sent out of a VXLAN tunnel interface, a VTEP replicates each flood frame and
sends one replica to all the other VTEPs in the corresponding VXLAN.

### IP aliasing

In all-active redundancy mode, all
redundant VTEPs of an ES advertise the ES to remote VTEPs through MP-BGP. IP
aliasing allows a remote VTEP to add the IP addresses of all the redundant
VTEPs as the next hops for the MAC or ARP information received from one of these
VTEPs. This mechanism creates ECMP routes between the remote VTEP and the redundant
VTEPs.

## EVPN VXLAN multicast

| IMPORTANT | IMPORTANT:  EVPN multicast supports only IPv4 underlay networks. |
| --- | --- |

‌

EVPN supports multicast forwarding. In an
EVPN network, VTEPs create and maintain multicast forwarding entries based on
received IGMP membership reports and leave group messages to reduce IGMP
floods.

### Multicast in single-homed sites

As shown in [Figure 15](#_Ref505879552), VTEPs
at single-homed sites create multicast forwarding entries by using the
following procedure:

**1\.**VTEP 1 receives the IGMP membership report
sent by Server 1\.

**2\.**VTEP 1 creates a multicast forwarding entry
and advertises information about the multicast group to VTEP 2 and VTEP 3 through
an SMET route.

**3\.**VTEP 2 and VTEP 3 create multicast
forwarding entries based on the SMET route. The next hop in the entries is VTEP
1\.

Figure 15 Multicast in single-homed sites

![](https://resource.h3c.com/en/202407/12/20240712_11705988_x_Img_x_png_18_2216147_294551_0.png)

‌

### Multicast in multihomed sites

The IGMP membership reports and leave group
messages sent from a multihomed site are received by multiple VTEPs. To ensure
consistency of multicast forwarding entries, redundant VTEPs advertise IGMP
join synch and leave synch routes to synchronize multicast information for each
ES.

As shown in [Figure 16](#_Ref505935050), if
the DF receives the first membership report for an IGMP multicast group, the following
route advertisement and withdrawal process takes place:

**1\.**VTEP 1 (DF) receives an IGMP membership
report.

**2\.**VTEP 1 sends an SMET route to VTEP 2 and
VTEP 3, and sends an IGMP join synch route to VTEP 2\.

**3\.**An IGMP leave group message is sent from
Site 1, and one of the following processes occurs:

¡     If
VTEP 1 (DF) receives the message, it sends an IGMP leave synch route to VTEP 2
and withdraws the SMET route and IGMP join synch route that it has advertised.

¡     If
VTEP 2 (BDF) receives the message, it sends an IGMP leave synch route to VTEP
1\. Then VTEP 1 withdraws the SMET route and IGMP join synch route that it has
advertised.

As shown in [Figure 16](#_Ref505935050), if
the BDF receives the first membership report for an IGMP multicast group, the
following route advertisement and withdrawal process takes place:

**1\.**VTEP 2 (BDF) receives an IGMP membership
report.

**2\.**VTEP 2 sends an IGMP join synch route to
VTEP 1 (DF).

**3\.**VTEP 1 sends an SMET route to VTEP 2 and
VTEP 3\.

**4\.**An IGMP leave group message is sent from
Site 1, and one of the following processes occurs:

¡     If
VTEP 1 (DF) receives the message, it sends an IGMP leave synch route to VTEP 2,
and VTEP 2 withdraws the IGMP join synch route that it has advertised. Then,
VTEP 1 withdraws the SMET route that it has advertised.

¡     If
VTEP 2 (BDF) receives the message, it sends an IGMP leave synch route to VTEP 1
and withdraws the IGMP join synch route that it has advertised. Then, VTEP 1
withdraws the SMET route that it has advertised.

Figure 16 Multicast in multihomed sites

![](https://resource.h3c.com/en/202407/12/20240712_11705989_x_Img_x_png_19_2216147_294551_0.png)

‌

## ARP and ND flood suppression

ARP or ND flood suppression reduces ARP
request broadcasts or ND request multicasts by enabling the VTEP to reply to
ARP or ND requests on behalf of VMs. 

As shown in [Figure 17](#_Ref393546811), this
feature snoops ARP or ND requests, ARP or ND responses, and BGP EVPN routes to populate
the ARP or ND flood suppression table with local and remote MAC addresses. If
an ARP or ND request has a matching entry, the VTEP replies to the request on
behalf of the VM. If no match is found, the VTEP floods the request to both
local and remote sites.

Figure 17 ARP and ND flood suppression

![](https://resource.h3c.com/en/202407/12/20240712_11705991_x_Img_x_png_20_2216147_294551_0.png)

 

The following uses ARP flood suppression as
an example to explain the flood suppression workflow:

**1\.**VM 1 sends an ARP request to obtain the MAC
address of VM 7\.

**2\.**VTEP 1 creates a suppression entry for VM 1,
floods the ARP request in the VXLAN, and sends the suppression entry to VTEP 2
and VTEP 3 through BGP EVPN. 

**3\.**VTEP 2 and VTEP 3 de-encapsulate the ARP
request and broadcast the request in the local site.

**4\.**VM 7 sends an ARP reply.

**5\.**VTEP 2 creates a suppression entry for VM 7,
forwards the ARP reply to VTEP 1, and sends the suppression entry to VTEP 1 and
VTEP 3 through BGP EVPN.

**6\.**VTEP 1 de-encapsulates the ARP reply and
forwards the ARP reply to VM 1\.

**7\.**VM 4 sends an ARP request to obtain the MAC
address of VM 1\.

**8\.**VTEP 1 creates a suppression entry for VM 4
and replies to the ARP request.

**9\.**VM 10 sends an ARP request to obtain the MAC
address of VM 1\.

**10\.**VTEP 3 creates a suppression entry for VM 10
and replies to the ARP request.

## MAC mobility

MAC mobility refers to that a VM or host
moves from one ES to another. The source VTEP is unaware of the MAC move event.
To notify other VTEPs of the change, the destination VTEP advertises a MAC/IP
advertisement route for the MAC address. The source VTEP withdraws the old
route for the MAC address after receiving the new route. The MAC/IP
advertisement route has a sequence number that increases when the MAC address
moves. The sequence number identifies the most recent move if the MAC address
moves multiple times.

## EVPNM-LAG

‌

| IMPORTANT | IMPORTANT:  You can use EVPN M-LAG on IPv4 sites extended by IPv4 underlay networks or on IPv6 sites extended by IPv6 underlay networks. |
| --- | --- |

‌

### About EVPN M-LAG

As shown in [Figure 18](#_Ref33968582), EVPN multichassis
link aggregation (M-LAG) virtualizes two VTEPs or EVPN gateways into one M-LAG
system through M-LAG to avoid single points of failure. The VTEPs or EVPN
gateways are called M-LAG member devices. For more information about M-LAG, see
Layer 2—LAN Switching Configuration Guide.

Figure 18 EVPNM-LAG

![](https://resource.h3c.com/en/202407/12/20240712_11705993_x_Img_x_png_22_2216147_294551_0.png)

 

### VM reachability information synchronization

To ensure VM reachability information
consistency in the M-LAG system, the M-LAG member devices synchronize MAC
address entries and ARP or ND information with each other through a peer link. 

### Virtual VTEP address

The M-LAG member devices use a virtual VTEP
address to set up VXLAN tunnels with remote VTEPs or EVPN gateways. 

### Independent BGP neighbor relationship establishment

The M-LAG member devices use different BGP
peer addresses to establish neighbor relationships with remote devices. For
load sharing and link redundancy, a neighbor sends traffic destined for the
virtual VTEP address to both of the M-LAG member devices through ECMP routes of
the underlay network.

### Site-facing link redundancy

| IMPORTANT | IMPORTANT:  This mechanism ensures service continuity in case of site-facing AC failure. |
| --- | --- |

 

As shown in [Figure 18](#_Ref33968582), a VM
accesses the EVPN network through multiple Ethernet links that connect to the
VTEPs. On each VTEP, all site-facing Ethernet links are assigned to a Layer 2
aggregation group for high availability. On the corresponding Layer 2 aggregate
interfaces, Ethernet service instances are configured as ACs of VXLANs to match
customer traffic.

#### Link redundancy mechanism for a direct peer link

If the peer link is an Ethernet aggregate
link, VTEPs in the M-LAG system transmit data traffic between them over the peer
link or a VXLAN tunnel when a site-facing AC fails.

·     Data traffic transmission
over a VXLAN tunnel—The VTEPs automatically set
up a VXLAN tunnel between them and assign it to all VXLANs. When a site-facing
AC on one M-LAG member device fails, the device forwards the remote packets
destined for the AC to the other M-LAG member device over the VXLAN tunnel. The
remote packets are encapsulated with the VXLAN ID of the failed site-facing AC.
When the other M-LAG member device receives the packets, it decapsulates them
and forwards them in the VXLAN where they belong.

·     Data traffic transmission
over the peer link—Each VTEP in the M-LAG system
creates dynamic ACs on the peer-link interface by using one of the following
methods: 

¡     Creation based on site-facing ACs—When
a site-facing AC is created, a VTEP automatically creates an AC on the peer-link
interface. The automatically created AC uses the same traffic match criterion as
the site-facing AC and is mapped to the same VSI as the site-facing AC.

¡     Creation based on VXLAN IDs—When a
VXLAN is created, a VTEP automatically creates an AC on the peer-link interface.
The automatically created AC uses a frame match criterion generated based on
the VXLAN ID and is mapped to the VSI of the VXLAN.

When a site-facing AC goes down, traffic
that a remote device sends to the AC is forwarded to the other M-LAG member
device through the peer link. The other M-LAG member device identifies the VSI
of the traffic and forwards the traffic to the destination. 

