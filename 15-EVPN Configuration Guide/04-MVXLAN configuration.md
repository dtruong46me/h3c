
# MVXLAN overview

Multicast VXLAN (MVXLAN) transmits
multicast traffic from a multicast source to multicast receivers in a VXLAN or
EVPN VXLAN network.

## Restrictions and guidelines: MVXLAN configuration

Before you configure MVXLAN settings,
perform the following tasks:

**1\.**Execute the switch-mode 1 command in system view to set the system operating mode to VXLAN.

**2\.**Save the running configuration to the
next-startup configuration file.

**3\.**Reboot the device.

For more information about the system
operating mode, see device management in Fundamentals
Configuration Guide.

MVXLAN supports only IPv4 underlay networks
in the current software version.

## MVXLAN modes

MVXLAN supports the following traffic
transmission modes:

·     Ingress replication—Used for forwarding multicast traffic of multiple VPN instances in
a VXLAN network.

·     Multicast distribution tree
(MDT)—Used for forwarding multicast traffic in
an EVPN VXLAN network.

## Ingress replication MVXLAN

### Network model

[Figure 1](#_Ref24644124) shows a
typical MVXLAN network that uses ingress replication. In the network, the
border device is attached to multiple sources, and the VTEPs are attached to
multicast receivers. VXLAN tunnels are manually established between the border
device and the VTEPs and assigned to VXLANs. When receiving multicast packets,
the border device identifies the VPN instance to which the packets belong and
forwards them to the VTEPs configured with the VPN instance. Then, the VTEPs
forward the multicast packets to the attached multicast receivers in the VPN
instance.

Figure 1 Ingress replication MVXLAN network model

![](https://resource.h3c.com/en/202407/12/20240712_11706017_x_Img_x_png_0_2216149_294551_0.png)

‌

### Working mechanism

Ingress replication MVXLAN requires the
following configuration on the border device and VTEPs:

·     On the border device, create VSI interfaces,
associate them with VPN instances, and enable IGMP on the VSI interfaces.

·     On the border device, associate the multicast
source-facing interfaces with VPN instances.

·     On the border device and VTEPs, enable IGMP
snooping on VSIs.

The border device and VTEPs learn multicast
forwarding entries as follows:

**1\.**On the border device, VSI interfaces
broadcast IGMP queries in their respective VXLANs after they are enabled with
IGMP.

**2\.**The VTEPs mark the VXLAN tunnel interfaces
where the IGMP queries are received as IGMP snooping router ports, remove VXLAN
encapsulation from the IGMP queries, and forward them to local hosts.

**3\.**Multicast receivers reply with IGMP
membership reports.

**4\.**The VTEPs mark the ACs where the IGMP
membership reports are received as IGMP snooping member ports and forward the
reports to the border device through VXLAN tunnels.

**5\.**The border device receives the IGMP
membership reports on a VXLAN tunnel interface and marks the VXLAN tunnel
interface as an IGMP snooping member port.

Having learned multicast forwarding
entries, the border device and VTEPs forward multicast traffic as follows:

**1\.**When receiving multicast packets, the border
device identifies their VPN instance by the incoming interface and forwards
them based on the multicast forwarding table of the VPN instance.

**2\.**If the traffic outgoing interface is a VSI
interface, the border device forwards the multicast packets through the IGMP
snooping member ports in the VXLAN associated with the VSI interface. The IGMP
snooping member ports are VXLAN tunnel interfaces.

**3\.**The VTEPs decapsulate the multicast packets and
forward them out of member ports to multicast receivers.

## MDT MVXLAN

On the public network, MVXLAN multicast
traffic is forwarded along an MDT rooted at the multicast source-side VTEP to
leaf receiver-side VTEPs through unidirectional MVXLAN tunnels. MDT-based
transmission ensures that multicast traffic is forwarded along optimal paths.

### Benefits

MDT MVXLAN provides the following benefits:

·     On-demand multicast forwarding—Creates multicast distribution trees and manages multicast group members
by using BGP EVPN routes and PIM.

·     Inter-VXLAN multicast forwarding—Uses distributed EVPN gateways to forward Layer 3 multicast traffic
between VXLANs.

### Network model

As shown in [Figure 2](#_Ref157344211), distributed
EVPN gateways are collocated with the VTEPs, and MVXLANs are created on the VTEPs
to direct multicast traffic forwarding. When receiving multicast packets, a VTEP
forwards them through ACs and MVXLAN tunnels to multicast receivers.

For more information about VTEPs, VSIs, and
VXLANs, see VXLAN Configuration Guide. For more information
about EVPN configuration, see "Configuring EVPN."

Figure 2 MDT MVXLAN network model

![](https://resource.h3c.com/en/202407/12/20240712_11706018_x_Img_x_png_1_2216149_294551_0.png)

 

### Basic concepts

The following are the basic concepts in MVXLAN:

·     MDT—An MDT is a multicast distribution tree constructed by all VTEPs in
the same MVXLAN. MDTs include the default MDT and the data MDT.

·     Default group—A default group is a unique multicast address assigned to each MVXLAN
on the public network. It is the unique identifier of an MVXLAN on the public network
and helps build the default MDT for an MVXLAN on the public network. Packets of
the private multicast groups in an MVXLAN are encapsulated into packets of the default
group before they are transmitted on the public network.

·     Default MDT—A default MDT uses a default group address as its group address. The
default MDT of an MVXLAN is uniquely identified by the default group and transmits
all private multicast packets of the MVXLAN. A default MDT is automatically created
after the default group is specified and will always exist on the public network,
regardless of whether multicast services exist on the public network or MVXLAN.

·     Data group—An MVXLAN is assigned a unique data group for MDT switchover. If
you use an ACL to match the multicast traffic of an MVXLAN, the ingress VTEP selects
a least used address from the data group range to encapsulate the matching multicast
packets of the MVXLAN. Other VTEPs are notified to use the address to forward the
matching traffic of the MVXLAN. This initiates the switchover to the data MDT.

·     Data MDT—A data MDT is an MDT that uses a data group as it group address. At
MDT switchover, VTEPs with downstream receivers join a data group to build a data
MDT. The ingress VTEP forwards the encapsulated MVXLAN multicast traffic along the
data MDT over the public network.

### MP-BGP extension for MVXLAN

To support MVXLAN, MP-BGP introduces the following
routes for creating MDTs under the EVPN address family:

·     Supplementary broadcast domain
selective multicast Ethernet tag (SBD-SMET) route—Contains private multicast source address and private multicast group
address information. A receiver-side VTEP uses the SBD-SMET route to advertise its
interest in a specific (\*, G) or (S, G). An SBD-SMET route carries the RD configured
in VPN instance view and export targets configured in VPN instance IPv4 address
family view.

·     Selective provider multicast
service interface route—Also known as S-PMSI A-D
route. An S-PMSI A-D route contains the private multicast source address, private
multicast group address, default or data group address, and MVXLAN source interface
address. S-PMSI A-D routes are used by the multicast source-side VTEP and its BGP
peers to establish the default MDT and switch traffic from the default MDT to a
data MDT. An S-PMSI A-D route carries the RD configured in VPN instance view and
export targets configured in VPN instance IPv4 address family view.

### Automatic MVXLAN tunnel establishment and assignment

In an MVXLAN network, VTEPs automatically establish
MVXLAN tunnels and assign them to MVXLANs to forward Layer 3 multicast traffic.
The tunnel source is the MVXLAN source interface address, and the tunnel destination
is the default or data group address. An MVXLAN tunnel is a unidirectional tunnel
from the multicast source-side VTEP to a multicast receiver-side VTEP.

### Default MDT establishment

The multicast routing protocol running on the
public network can be PIM-SM or PIM-SSM. The process of creating a default MDT is
the same in these PIM modes. All VTEPs in an MVXLAN join the default MDT of the
MVXLAN. The private multicast packets of the MVXLAN are forwarded along the default
MDT to the VTEPs, no matter whether the site attached to a VTEP contains receivers.

Figure 3 Default MDT establishment in a PIM-SM
network

![](https://resource.h3c.com/en/202407/12/20240712_11706022_x_Img_x_png_2_2216149_294551_0.png)

 

As shown in [Figure 3](#_Ref207011040), PIM-SM
runs on the public network, and MVXLAN is configured on all VTEPs. The process for
establishing a default MDT is as follows:

**1\.**VTEP 1 sends an S-PMSI A-D route that contains
(\*, \*) to VTEP 2 and VTEP 3\.

**2\.**VTEP 2 and VTEP 3 receive the route and join
a multicast group according to the PMSI tunnel attribute of the route. The PMSI
tunnel attribute contains the following information:

¡     The multicast
source is the IP address of the MVXLAN source interface on VTEP 1\.

¡     The multicast
group is the default group configured on VTEP 1\.

**3\.**Multicast forwarding entries are created on each
device along the paths on the public network, and a shortest path tree (SPT) with
VTEP 1 as the root and VTEP 2 and VTEP 3 as leaves is created. The SPT is the default
MDT.

### Default MDT-based transmission

After the default MDT is established, the multicast
source sends the private multicast traffic to the receivers in each site along the
default MDT. The private multicast packets are encapsulated into public multicast
packets on the local VTEP and transmitted along the default MDT. Then, they are
decapsulated on the remote VTEPs and transmitted in remote VXLAN sites.

Figure 4 Multicast
data packettransmission

![](https://resource.h3c.com/en/202407/12/20240712_11706023_x_Img_x_png_3_2216149_294551_0.png)

 

As shown in [Figure 4](#_Ref7439423), PIM-SM runs
on the public network, the multicast source is attached to VTEP 1, and the multicast
receiver is attached to VTEP 2\. The multicast forwarding process is as follows:

**1\.**The multicast source sends private multicast
packets (192.1.1.1, 225.1.1.1) to VTEP 1\.

**2\.**VTEP 1 creates a multicast forwarding entry for
(192.1.1.1, 225.1.1.1).

**3\.**VTEP 1 processes the packets based on whether
the receiver has joined the private multicast group:

¡     If the
receiver has sent an IGMP join message to VTEP 2, VTEP 1 has an SBD-SMET route sent
by VTEP 1 that contains (\*, G). VTEP 1 adds VXLAN encapsulation to the packets according
to the route and forwards them to VTEP 2 and VTEP 3 along the default MDT. In the
outer IP header of the VXLAN packets, the source IP address is the IP address of
the MVXLAN source interface, and the destination IP address is the default group
address.

¡     If no
receiver exists, VTEP 1 drops the packets.

**4\.**VTEP 2 decapsulates the VXLAN packets and forwards
the private multicast packets to the receiver.

**5\.**VTEP 3 decapsulates the VXLAN packets and drops
the private multicast packets because no local receiver exists.

### MDT switchover

An MVXLAN can use the default MDT or a data
MDT for multicast traffic forwarding. The default MDT is uniquely identified by
the default group, and a data MDT is uniquely identified by a data group. Each default
group is associated with a data group range.

#### Switching from the default MDT to a data MDT

When a multicast packet of an MVXLAN is transmitted
through the default MDT on the public network, the packet is forwarded to all VTEPs
configured with the VPN instance of the MVXLAN. This occurs whether or not any active
receivers exist in the sites attached to the VTEPs. When the rate of the multicast
traffic of that MVXLAN is high, multicast traffic might be flooded on the public
network. This increases the bandwidth use and brings extra burden on the VTEPs.

To optimize multicast transmission, the MDT-based
MVXLAN solution introduces a dedicated data MDT. The data MDT is built between the
VTEPs that are attached to MVXLAN multicast receivers and multicast sources. When
specific network criteria are met, multicast traffic is switched from the default
MDT to the data MDT.

A switchover from the default MDT to the data
MDT is initiated as follows:

**1\.**Private multicast traffic passes the ACL rule
filtering for default MDT to data MDT switchover.

**2\.**The source-side VTEP selects a least-used address
from the data group range and sends an S-PMSI A-D route to all the other VTEPs down
the default MDT. This route contains the private multicast source address, private
multicast group address, IP address of the MVXLAN source interface, and data group
address.

**3\.**Each VTEP that receives the route examines whether
it has receivers of that private multicast stream.

If so, it joins the data MDT rooted at the
source-side VTEP. Otherwise, it caches the route and will join the data MDT when
it has attached receivers.

**4\.**After sending the S-PMSI A-D route, the source-side
VTEP starts the data-delay timer. When the timer expires, the source-side VTEP uses
the data group address to encapsulate the private multicast traffic. The multicast
traffic is then forwarded down the data MDT.

**5\.**After the multicast traffic is switched from
the default MDT to the data MDT, a downstream VTEP can leave the data MDT by sending
a PIM prune message if it no longer has active receivers attached to it.

#### Switching from the data MDT to the default MDT

After the MXVLAN multicast traffic is switched
to the data MDT, the multicast traffic conditions might change and no longer meet
the switchover criterion. In this case, the source-side VTEP initiates a backward
MDT switchover process when any of the following criteria are met: 

·     The associated data group range is changed, and the
data group address for encapsulating the MVXLAN multicast traffic is not in the
new address range.

·     The ACL rule for controlling the switchover from
the default MDT to the data MDT has changed, and the MVXLAN multicast traffic fails
to pass the new ACL rule.

### M-LAG in MVXLAN

| IMPORTANT | IMPORTANT:  To use this feature, make sure the site network and the underlay network are both IPv4 networks. |
| --- | --- |

 

#### Overview

As shown in [Figure 5](#_Ref75878557), you
can use multichassis link aggregation (M-LAG) to virtualize two VTEPs or border
devices into an M-LAG system to prevent single points of failure from
interrupting traffic. The VTEPs or border devices can have both multicast
sources and receivers attached. For more information about M-LAG, see Layer 2—LAN Switching Configuration Guide.

Figure 5 M-LAG in MVXLAN

![](https://resource.h3c.com/en/202407/12/20240712_11706025_x_Img_x_png_5_2216149_294551_0.png)

 

#### Mechanisms

In an M-LAG system, the M-LAG member
devices synchronize multicast traffic and multicast join requests (IGMP
membership reports or PIM join messages) over the peer link to maintain
consistency in multicast source and receiver information. When one M-LAG member
device fails or its uplink or downlink fails, the other M-LAG member device
forwards all multicast traffic to avoid traffic interruption.

As shown in [Figure 5](#_Ref75878557), the
M-LAG system formed by VTEP 1 and VTEP 2 operates as follows:

**1\.**VTEP 1 and VTEP 2 set up MVXLAN tunnels with
the other devices on the network. The MVXLAN tunnels use the virtual VTEP
address as the multicast source and the default group address as the
destination address.

**2\.**When receiving the multicast join requests
sent by the multicast receivers on aggregate interface 2, VTEP 1 sends the
requests over the peer link to VTEP 2\.

**3\.**Both VTEP 1 and VTEP 2 create multicast forwarding
entries for the multicast join requests and send SBD-SMET routes to the
multicast source-side VTEP.

**4\.**When receiving the multicast traffic sent by
the multicast source on aggregate interface 1, VTEP 1 sends the multicast
traffic over the peer link to VTEP 2\.

**5\.**VTEP 1 and VTEP 2 forward the multicast
traffic according to the following rules:

¡     The M-LAG
member device with an odd M-LAG system number forwards traffic destined for odd
multicast group addresses.

¡     The M-LAG
member device with an even M-LAG system number forwards traffic destined for
even multicast group addresses.

¡     When
one M-LAG member device fails, the other M-LAG member device forwards all
multicast traffic.

**6\.**If the requirements are met for switching
traffic from the default MDT to a data MDT, the primary M-LAG member device
selects a target data MDT and advertises the data MDT to the secondary M-LAG
member device through an SBD-SMET route.

**7\.**The secondary M-LAG member device acts as
follows:

¡     If
the data group exists on the device, the device uses that data group for
multicast forwarding.

¡     If
the data group does not exist or the device does not receive the SBD-SMET
route, the device selects a local data group.

### Layer 3 multicast in DCI multihoming scenarios

As shown in [Figure 6](#_Ref82080538), to
avoid single point of failure for Layer 3 multicast in DCI scenarios, you can
deploy multiple EDs at the edge of a data center. The EDs use the same virtual ED
address to form an ED group. The ED group uses the virtual ED address to
establish VXLAN tunnels with VTEPs and VXLAN-DCI tunnels with remote EDs for ED
redundancy and load balancing.

Figure 6 Layer 3 multicast in DCI multihoming
scenarios

![](https://resource.h3c.com/en/202407/12/20240712_11706026_x_Img_x_png_6_2216149_294551_0.png)

 

As shown in [Figure 7](#_Ref24653233), Layer
3 multicast in DCI multihoming scenarios uses the same BGP EVPN route
advertisement and VXLAN/MVXLAN tunnel establishment processes as Layer 3
multicast in DCI scenarios except for the following items:

·     Multiple EDs (ED 1 and ED 2 in this example) use
the virtual IP address of the ED group to establish VXLAN-DCI tunnels with
remote EDs (ED 3 in this example).

·     When ED 3 receives SMET or S-PMSI A-D routes
from DC 1, it replaces the next hop of the routes with the virtual IP address
of the ED group.

·     An ED in the ED group does not join the MVXLAN
tunnels of other EDs in the same ED group.

After BGP EVPN route advertisement and VXLAN/MVXLAN
tunnel establishment, DCI Layer 3 multicast traffic is forwarded as follows:

**1\.**When VTEP 1 receives multicast traffic from
a multicast source, it identifies the VPN instance of the traffic. Then, VTEP 1
forwards the multicast traffic to ED 1, ED 2, and the other VTEPs in the local
data center through multicast tunnels in the VPN instance.

**2\.**The other VTEPs in the local data center
forwards the multicast traffic to the multicast receiver attached to the VTEPs.
ED 1 and ED 2 performs forwarder election and the ED that wins the election
forwards the multicast traffic to ED 3 in DC 2 over a VXLAN-DCI tunnel.

**3\.**ED 3 forwards the multicast traffic to VTEP
2 through a multicast tunnel.

**4\.**VTEP 2 forwards the multicast traffic to
multicast receivers.

