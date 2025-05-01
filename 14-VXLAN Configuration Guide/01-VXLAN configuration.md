
# VXLAN overview

Virtual eXtensible LAN (VXLAN) is a MAC-in-UDP
technology that provides Layer 2 connectivity between distant network sites across
an IP network. VXLAN is typically used in data centers and the access layer of
campus networks for multitenant services.

## VXLAN benefits

VXLAN provides the following benefits:

·     Support for more virtual switched
domains than VLANs—Each VXLAN is uniquely
identified by a 24-bit VXLAN ID. The total number of VXLANs can reach 16777216
(224). This specification makes VXLAN a better
choice than 802.1Q VLAN to isolate traffic for user terminals.

·     Easy deployment and maintenance—VXLAN requires deployment only on the edge devices of the transport
network. Devices in the transport network perform typical Layer 3 forwarding.

## VXLAN network model

As shown in [Figure 1](#_Ref374540414), a
VXLAN is a virtual Layer 2 network (known as the overlay network) built on top
of an existing physical Layer 3 network (known as the underlay network). The
overlay network encapsulates inter-site Layer 2 frames into VXLAN packets and
forwards the packets to the destination along the Layer 3 forwarding paths
provided by the underlay network. The underlay network is transparent to
tenants, and geographically dispersed sites of a tenant are merged into a Layer
2 network.

The site networks and the undelay network
can be IPv4 or IPv6 networks. 

The transport edge devices assign user
terminals to different VXLANs, and then forward traffic between sites for user
terminals by using VXLAN tunnels. Supported user terminals include PCs,
wireless terminals, and VMs on servers.

 

|  | NOTE:  This document uses VMs as examples to describe the mechanisms of VXLAN. The mechanisms do not differ between different kinds of user terminals. |
| --- | --- |

 

The transport edge devices are VXLAN tunnel
endpoints (VTEP). The VTEP implementation of the device uses ACs, VSIs, and
VXLAN tunnels to provide VXLAN services.

·     VSI—A virtual switch instance is a virtual Layer 2 switched domain. Each
VSI provides switching services only for one VXLAN. VSIs learn MAC addresses
and forward frames independently of one another. VMs in different sites have
Layer 2 connectivity if they are in the same VXLAN.

·     Attachment circuit (AC)—An AC is a physical or virtual link that connects a VTEP to a local
site. Typically, ACs are Ethernet service instances that are associated with
the VSI of a VXLAN. Traffic received from an AC is assigned to the VSI
associated with the AC. Ethernet service instances are created on site-facing
Layer 2 interfaces. An Ethernet service instance matches a list of custom VLANs
by using a frame match criterion.

·     VXLAN tunnel—Logical point-to-point tunnels between VTEPs over the transport
network. Each VXLAN tunnel can trunk multiple VXLANs.

VTEPs encapsulate VXLAN traffic in the
VXLAN, outer UDP, and outer IP headers. The devices in the transport network
forward VXLAN traffic only based on the outer IP header.

Figure 1 VXLAN network model

![](https://resource.h3c.com/en/202407/12/20240712_11705925_x_Img_x_png_0_2216143_294551_0.png)

 

## VXLAN packet format

As shown in [Figure 2](#_Ref374629374), a
VTEP encapsulates a frame in the following headers:

·     8-byte VXLAN header—VXLAN information for the frame.

¡     Flags—If the I bit is 1, the VXLAN ID
is valid. If the I bit is 0, the VXLAN ID is invalid. All other bits are
reserved and set to 0\.

¡     24-bit VXLAN ID—Identifies the VXLAN
of the frame. It is also called the virtual network identifier (VNI). 

·     8-byte outer UDP header for
VXLAN—The default VXLAN destination UDP port
number is 4789\.

·     20-byte outer IP header—Valid addresses of VTEPs or VXLAN multicast groups on the transport
network. Devices in the transport network forward VXLAN packets based on the
outer IP header.

Figure 2 VXLAN packet format

![](https://resource.h3c.com/en/202407/12/20240712_11705926_x_Img_x_png_1_2216143_294551_0.png)

 

## VXLAN working mechanisms

### Generic VXLAN network establishment and forwarding process

The VTEP uses the following process to establish
the VXLAN network and forward an inter-site frame:

**1\.**Discovers remote VTEPs, establishes VXLAN
tunnels, and assigns the VXLAN tunnels to VXLANs.

**2\.**Assigns the frame to its matching VXLAN if
the frame is sent between sites.

**3\.**Performs MAC learning on the VXLAN's VSI.

**4\.**Forwards the frame through VXLAN tunnels.

This section describes this process in
detail. For intra-site frames in a VSI, the system performs typical Layer 2
forwarding, and it processes 802.1Q VLAN tags as described in "[Access modes of VSIs](#_Ref382150933)."

### VXLAN tunnel establishment and assignment

To provide Layer 2 connectivity for a VXLAN
between two sites, you must create a VXLAN tunnel between the sites and assign
the tunnel to the VXLAN.

#### VXLAN tunnel establishment

VXLAN supports manual and automatic VXLAN
tunnel establishment.

·     Manual creation—Manually create a VXLAN tunnel interface, and specify the tunnel
source and destination IP addresses on the peer VTEPs.

·     Automatic creation—Configure Ethernet Virtual Private Network (EVPN) to automatically discover
VTEPs and set up VXLAN tunnels. For more information about EVPN, see EVPN Configuration Guide.

#### VXLAN tunnel assignment

VXLAN supports manual and automatic VXLAN
tunnel assignment.

·     Manual assignment—Manually assign VXLAN tunnels to VXLANs.

·     Automatic assignment—Run EVPN to automatically assign VXLAN tunnels to VXLANs. For more
information about EVPN, see EVPN Configuration Guide.

### Assignment of traffic to VXLANs

#### Traffic from the local site to a remote site

The VTEP uses the following methods to
assign customer frames to a VXLAN:

·     Ethernet service
instance-to-VSI mapping—This method uses the
frame match criterion of an Ethernet service instance to match a list of VLANs
on a site-facing Layer 2 interface. The frame match criterion specifies the
characteristics of traffic from the VLANs, such as tagging status and VLAN IDs.
The VTEP assigns customer traffic to a VXLAN by mapping the Ethernet service
instance to a VSI.

·     VLAN-based VXLAN assignment—This method maps a VLAN to a VXLAN. The VTEP assigns all frames of
the VLAN to the VXLAN.

As shown in [Figure 3](#_Ref376422245), Ethernet
service instance 1 matches VLAN 2 and is mapped to VSI A (VXLAN 10). When a
frame from VLAN 2 arrives, the VTEP assigns the frame to VXLAN 10, and looks up
VSI A's MAC address table for the outgoing interface.

Figure 3 Identifyingtraffic from the local site

![](https://resource.h3c.com/en/202407/12/20240712_11705937_x_Img_x_png_2_2216143_294551_0.png)

 

#### Traffic from a remote site to the local site

When a frame arrives at a VXLAN tunnel, the
VTEP uses the VXLAN ID in the frame to identify its VXLAN.

### MAC learning

The VTEP performs source MAC learning on
the VSI as a Layer 2 switch.

·     For traffic from the local site to the remote
site, the VTEP learns the source MAC address before VXLAN encapsulation.

·     For traffic from the remote site to the local
site, the VTEP learns the source MAC address after removing the VXLAN header. 

A VSI's MAC address table includes the
following types of MAC address entries:

·     Local MAC—MAC entries learned from the local site. The outgoing interfaces
for the MAC address entries are site-facing interfaces. 

¡     Static—Manually added MAC entries. 

¡     Dynamic—Dynamically learned MAC entries.

·     Remote MAC—MAC entries learned from a remote site, including static and
dynamic MAC entries. The outgoing interfaces for the MAC addresses are VXLAN
tunnel interfaces.

¡     Static—Manually added MAC entries.

¡     Dynamic—MAC entries learned in the
data plane from incoming traffic on VXLAN tunnels. The learned MAC addresses
are contained in the inner Ethernet header.

¡     BGP EVPN—MAC entries advertised
through BGP EVPN. For more information, see EVPN
Configuration Guide.

¡     OpenFlow—MAC entries issued by a
remote controller through OpenFlow. For more information, see OpenFlow Configuration Guide.

¡     OVSDB—MAC entries issued by a remote
controller through OVSDB.

The following shows the priority order of
different types of remote MAC address entries:

**a.**Static MAC address entries, and MAC address
entries issued by a remote controller through OpenFlow or OVSDB. These types of
entries have the same priority and overwrite each other.

**a.**MAC address entries advertised through BGP
EVPN. 

**b.**Dynamic MAC address entries.

### Unicast forwarding

#### Intra-site unicast forwarding

The VTEP uses the following process to
forward a known unicast frame within a site:

**1\.**Identifies the VSI of the frame. 

**2\.**Looks up the destination MAC address in the VSI's
MAC address table for the outgoing interface. 

**3\.**Sends the frame out of the matching outgoing
interface.

As shown in [Figure 4](#_Ref479094486), VTEP 1
forwards a frame from VM 1 to VM 4 within the local site in VLAN 10 as follows:

**4\.**Identifies that the frame belongs to VSI A when
the frame arrives at Interface A.

**5\.**Looks up the destination MAC address (MAC 4\)
in the MAC address table of VSI A for the outgoing interface.

**6\.**Sends the frame out of the matching outgoing
interface (Interface B) to VM 4 in VLAN 10\.

Figure 4 Intra-site unicast

![](https://resource.h3c.com/en/202407/12/20240712_11705948_x_Img_x_png_3_2216143_294551_0.png)

 

#### Inter-site unicast forwarding

The following process (see [Figure 5](#_Ref382215891)) applies
to a known unicast frame between sites: 

**1\.**The source VTEP encapsulates the Ethernet
frame in the VXLAN/UDP/IP header. 

In the outer IP header, the source IP
address is the source VTEP's VXLAN tunnel source IP address. The destination IP
address is the VXLAN tunnel destination IP address.

**2\.**The source VTEP forwards the encapsulated
packet out of the outgoing VXLAN tunnel interface found in the VSI's MAC
address table.

**3\.**The intermediate transport devices (P
devices) forward the frame to the destination VTEP by using the outer IP
header.

**4\.**The destination VTEP removes the headers on
top of the inner Ethernet frame. It then performs MAC address table lookup in
the VXLAN's VSI to forward the frame out of the matching outgoing interface.

Figure 5 Inter-site
unicast

![](https://resource.h3c.com/en/202407/12/20240712_11705950_x_Img_x_png_4_2216143_294551_0.png)

‌

### Flood

The source VTEP floods a broadcast,
multicast, or unknown unicast frame to all site-facing interfaces and VXLAN
tunnels in the VXLAN, except for the incoming interface. Each destination VTEP
floods the inner Ethernet frame to all site-facing interfaces in the VXLAN. To
avoid loops, the destination VTEPs do not flood the frame back to VXLAN
tunnels.

VXLAN supports unicast mode (also called
head-end replication).

#### Unicast mode (head-end replication)

As shown in [Figure 6](#_Ref393546634), the source
VTEP replicates the flood frame, and then sends one replica to the destination
IP address of each VXLAN tunnel in the VXLAN.

Figure 6 Unicast mode

![](https://resource.h3c.com/en/202407/12/20240712_11705951_x_Img_x_png_5_2216143_294551_0.png)

 

#### Multicast mode (tandem replication)

| IMPORTANT | IMPORTANT:  Multicast mode is not supported if the underlay network is an IPv6 network. |
| --- | --- |

‌

As shown in [Figure 7](#_Ref375732760), the
source VTEP sends the flood frame in a multicast VXLAN packet destined for a
multicast group address. Transport network devices replicate and forward the
packet to remote VTEPs based on their multicast forwarding entries. 

Figure 7 Multicast mode

![](https://resource.h3c.com/en/202407/12/20240712_11705953_x_Img_x_png_7_2216143_294551_0.png)

 

### Access modes of VSIs

The access mode of a VSI determines how the
VTEP processes the 802.1Q VLAN tags in the Ethernet frames.

#### VLAN access mode

In this mode, Ethernet frames received from
or sent to the local site must contain 802.1Q VLAN tags.

·     For an Ethernet frame received from the local
site, the VTEP removes all its 802.1Q VLAN tags before forwarding the frame.

·     For an Ethernet frame destined for the local
site, the VTEP adds 802.1Q VLAN tags to the frame before forwarding the frame.

In VLAN access mode, VXLAN packets sent
between sites do not contain 802.1Q VLAN tags. You can use different 802.1Q
VLANs to provide the same service in different sites.

#### Ethernet access mode

The VTEP does not process the 802.1Q VLAN
tags of Ethernet frames received from or sent to the local site.

·     For an Ethernet frame received from the local
site, the VTEP forwards the frame with the 802.1Q VLAN tags intact.

·     For an Ethernet frame destined for the local
site, the VTEP forwards the frame without adding 802.1Q VLAN tags.

In Ethernet access mode, VXLAN packets sent
between VXLAN sites contain 802.1Q VLAN tags. You must use the same VLAN to
provide the same service between sites.

## ARP and ND flood suppression

ARP or ND flood suppression reduces ARP request
broadcasts or ND request multicasts by enabling the VTEP to reply to ARP or ND
requests on behalf of VMs. 

As shown in [Figure 8](#_Ref393546811),
this feature works as follows:

**1\.**The VTEP snoops ARP or ND packets to populate
the ARP or ND flood suppression table with local and remote MAC addresses. 

**2\.**If an ARP or ND request from the local site
does not have a matching entry, the VTEP drops the request or floods it over
the transport network. If an ARP or ND request has a matching entry, the VTEP acts
as follows:

¡     In
proxy reply mode, the VTEP replies to the ARP or ND request on behalf of the
target IP address. In the ARP reply, the sender IP address is the target IP
address in the ARP request, and the sender and source MAC addresses are the MAC
address in the matching ARP flood suppression entry. In the ND reply, the
sender IP address is the target IP address in the ND request, and the source
MAC address is the MAC address in the matching ND flood suppression entry.

¡     In
unicast forwarding mode, the VTEP replaces the target MAC address and
destination MAC address in the ARP request with the MAC address in the entry
and unicasts the ARP request. For an ND request, the VTEP replaces the destination
MAC address with the MAC address in the entry and unicasts the ND request.

Proxy reply mode decreases system resource consumption.
Unicast forwarding mode reduces the ARP or ND entry learning errors caused by
the delay in refreshing ARP or ND flood suppression entries at the expense of
high system resource usage.

To avoid incorrect packet discards, do not
enable the VTEP to discard ARP or ND requests that do not match any flood suppression
entries before the VTEP creates ARP or ND flood suppression entries.

Figure 8 ARP flood suppression

![](https://resource.h3c.com/en/202407/12/20240712_11705954_x_Img_x_png_8_2216143_294551_0.png)

 

The following uses ARP flood suppression
that operates in proxy reply mode and floods ARP requests that do not match any
ARP flood suppression entries as an example to explain the flood suppression
workflow: 

**3\.**VM 1 sends an ARP request to obtain the MAC
address of VM 7\.

**4\.**VTEP 1 creates a suppression entry for VM 1,
and floods the ARP request in the VXLAN. 

**5\.**VTEP 2 and VTEP 3 de-encapsulate the ARP
request. The VTEPs create a suppression entry for VM 1, and broadcast the
request in the local site.

**6\.**VM 7 sends an ARP reply.

**7\.**VTEP 2 creates a suppression entry for VM 7
and forwards the ARP reply to VTEP 1\.

**8\.**VTEP 1 de-encapsulates the ARP reply,
creates a suppression entry for VM 7, and forwards the ARP reply to VM 1\.

**9\.**VM 4 sends an ARP request to obtain the MAC
address of VM 1 or VM 7\.

**10\.**VTEP 1 creates a suppression entry for VM 4
and replies to the ARP request.

**11\.**VM 10 sends an ARP request to obtain the MAC
address of VM 1\.

**12\.**VTEP 3 creates a suppression entry for VM 10
and replies to the ARP request.

## VXLAN IP gateways

A VXLAN IP gateway provides Layer 3
forwarding services for VMs in VXLANs. A VXLAN IP gateway can be an independent
device or be collocated with a VTEP. For more information about VXLAN IP
gateway placement, see "[Configuring VXLAN IP gateways](#_Ref408937293)."

## VXLANM-LAG

| IMPORTANT | IMPORTANT:  VXLAN M-LAG supports only IPv4 sites and IPv4 underlay networks. |
| --- | --- |

‌

#### About VXLAN M-LAG

As shown in [Figure 9](#_Ref33968582), VXLAN multichassis
link aggregation (M-LAG) virtualizes two VTEPs or VXLAN IP gateways into one M-LAG
system through M-LAG to avoid single points of failure. The VTEPs or VXLAN IP gateways
are called M-LAG member devices. For more information about M-LAG, see Layer 2—LAN Switching Configuration Guide.

Figure 9 VXLANM-LAG

![](https://resource.h3c.com/en/202407/12/20240712_11705927_x_Img_x_png_10_2216143_294551_0.png)

 

#### VM reachability information synchronization

To ensure VM reachability information
consistency in the M-LAG system, the M-LAG member devices synchronize MAC
address entries and ARP information with each other through a peer link. The
peer link is an Ethernet aggregate link, which is referred to as direct peer
link.

#### Virtual VTEP address

The M-LAG member devices use the same IP
address to set up VXLAN tunnels with remote VTEPs or VXLAN IP gateways. 

#### Dual-homed ACs

As shown in [Figure 9](#_Ref33968582), a VM is
dual-homed to the VTEPs in an M-LAG system through Ethernet links. The Ethernet
links connected to the same VM are aggregated into one Layer 2 aggregation
group across VTEPs. On the corresponding Layer 2 aggregate interface, Ethernet
service instances are configured as ACs of VXLANs to match customer traffic.
These ACs are dual-homed ACs.

When a site-facing AC is configured on a
VTEP, the VTEP automatically creates an AC on the peer link with the same
traffic match criterion as the site-facing AC. Then, it maps the automatically
created AC to the VSI of the site-facing AC. When the site-facing AC is down,
traffic sent to the AC is forwarded to the other VTEP through the peer link.
This mechanism ensures service continuity in case of AC failure.

#### Communication between single-homed ACs

An AC that is attached to only one of the
VTEPs in an M-LAG system is called a single-homed AC. Two single-homed ACs
attached to different VTEPs communicate through the peer link.

When a single-homed AC is configured on a
VTEP, the VTEP automatically creates an AC on the peer link with the same
traffic match criterion as the single-homed AC. Then, it maps the automatically
created AC to the VSI of the single-homed AC. When receiving traffic from the
single-homed AC, the VTEP sends the traffic to the other VTEP through the peer
link. Then, the other VTEP identifies the VSI of the traffic and forwards it.

## Protocols and standards

RFC 7348, Virtual
eXtensible Local Area Network (VXLAN): A Framework for Overlaying Virtualized
Layer 2 Networks over Layer 3 Networks

