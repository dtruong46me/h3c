
# Configuring OSPF

## About OSPF

Open
Shortest Path First (OSPF) is a link-state IGP developed by the OSPF working
group of the IETF. OSPF version 2 is used for IPv4. OSPF refers to OSPFv2 throughout
this chapter.

### OSPF features

OSPF has the following features:

·     Wide scope—Supports multiple network sizes and several hundred routers in an
OSPF routing domain.

·     Fast convergence—Advertises routing updates instantly upon network topology changes.

·     Loop free—Computes routes with the SPF algorithm to avoid routing loops.

·     Area-based network
partition—Splits an AS into multiple areas to facilitate
management. This feature reduces the LSDB size on routers to save memory and CPU
resources, and reduces route updates transmitted between areas to save bandwidth.

·     ECMP routing—Supports multiple equal-cost routes to a destination.

·     Routing hierarchy—Supports a 4-level routing hierarchy that prioritizes routes into
intra-area, inter-area, external Type-1, and external Type-2 routes.

·     Authentication—Supports area- and interface-based packet authentication to ensure secure
packet exchange.

·     Support for multicasting—Multicasts protocol packets on some types of links to avoid
impacting other devices.

### OSPF packets

OSPF messages are carried directly over IP.
The protocol number is 89\.

OSPF uses the following packet types:

·     Hello—Periodically sent to find and maintain neighbors, containing timer values,
information about the DR, BDR, and known neighbors.

·     Database description (DD)—Describes the digest of each LSA in the LSDB, exchanged between two
routers for data synchronization.

·     Link state request (LSR)—Requests needed LSAs from a neighbor. After exchanging the DD
packets, the two routers know which LSAs of the neighbor are missing from their
LSDBs. They then exchange LSR packets requesting the missing LSAs. LSR packets
contain the digest of the missing LSAs.

·     Link state update (LSU)—Transmits the requested LSAs to the neighbor.

·     Link state acknowledgment
(LSAck)—Acknowledges received LSU packets. It
contains the headers of received LSAs (an LSAck packet can acknowledge multiple
LSAs).

### LSA types

OSPF advertises routing information in Link
State Advertisements (LSAs). The following LSAs are commonly used:

·     Router LSA—Type-1 LSA, originated by all routers and flooded throughout a
single area only. This LSA describes the collected states of the router's
interfaces to an area.

·     Network LSA—Type-2 LSA, originated for broadcast and NBMA networks by the designated
router, and flooded throughout a single area only. This LSA contains the list
of routers connected to the network.

·     Network Summary LSA—Type-3 LSA, originated by Area Border Routers (ABRs), and flooded
throughout the LSA's associated area. Each summary-LSA describes a route to a
destination outside the area, yet still inside the AS (an inter-area route).

·     ASBR Summary LSA—Type-4 LSA, originated by ABRs and flooded throughout the LSA's
associated area. Type 4 summary-LSAs describe routes to Autonomous System Boundary
Router (ASBR).

·     AS External LSA—Type-5 LSA, originated by ASBRs, and flooded throughout the AS (except
stub and NSSA areas). Each AS-external-LSA describes a route to another AS.

·     NSSA LSA—Type-7 LSA, as defined in RFC 1587, originated by ASBRs in NSSAs
and flooded throughout a single NSSA. NSSA LSAs describe routes to other ASs.

·     Opaque LSA—LSA for OSPF extensions. Its format consists of a standard LSA
header and application specific information. The opaque LSA includes Type 9,
Type 10, and Type 11\. The Type 9 opaque LSA is flooded into the local subnet.
Grace LSA, used by graceful restart, is Type 9 LSA. The Type 10 is flooded into
the local area. The Type 11 is flooded throughout the AS.

### OSPF areas

#### Area-based OSPF network partition

In large OSPF routing domains, SPF route computations
consume too many storage and CPU resources, and enormous OSPF packets generated
for route synchronization occupy excessive bandwidth.

To resolve these issues, OSPF splits an AS
into multiple areas. Each area is identified by an area ID. The boundaries
between areas are routers rather than links. A network segment (or a link) can
only reside in one area as shown in [Figure 1](#_Ref278554838).

You can configure route summarization on ABRs
to reduce the number of LSAs advertised to other areas and minimize the effect
of topology changes.

Figure 1 Area-based OSPF network partition

![](https://resource.h3c.com/en/202407/12/20240712_11704755_x_Img_x_png_0_2216018_294551_0.png)

‌

#### Backbone area

Each AS has a backbone area that distributes
routing information between non-backbone areas. Routing information between
non-backbone areas must be forwarded by the backbone area. OSPF has the
following requirements:

·     All non-backbone areas must maintain
connectivity to the backbone area.

·     The backbone area must maintain connectivity
within itself.

In practice, these requirements might not
be met due to lack of physical links. OSPF virtual links can solve this issue.

#### Virtual links

A virtual link is established between two ABRs
through a non-backbone area. It must be configured on both ABRs to take effect.
The non-backbone area is called a transit area.

As shown in [Figure 2](#_Ref302740985), Area
2 has no direct physical link to the backbone Area 0\. You can configure a
virtual link between the two ABRs to connect Area 2 to the backbone area.

Figure 2 Virtual link application 1

![](https://resource.h3c.com/en/202407/12/20240712_11704756_x_Img_x_png_1_2216018_294551_0.png)

‌

Virtual links can also be used as redundant
links. If a physical link failure breaks the internal connectivity of the
backbone area, you can configure a virtual link to replace the failed physical
link, as shown in [Figure 3](#_Ref278554900).

Figure 3 Virtual link application 2

![](https://resource.h3c.com/en/202407/12/20240712_11704767_x_Img_x_png_2_2216018_294551_0.png)

‌

The virtual link between the two ABRs acts
as a point-to-point connection. You can configure interface parameters, such as
hello interval, on the virtual link as they are configured on a physical
interface.

The two ABRs on the virtual link unicast OSPF
packets to each other, and the OSPF routers in between convey these OSPF
packets as normal IP packets.

#### Stub area and totally stub area

A stub area does not distribute Type-5 LSAs
to reduce the routing table size and LSAs advertised within the area. The ABR of
the stub area advertises a default route in a Type-3 LSA so that the routers in
the area can reach external networks through the default route.

To further reduce the routing table size
and advertised LSAs, you can configure the stub area as a totally stub area. The
ABR of a totally stub area does not advertise inter-area routes or external
routes. It advertises a default route in a Type-3 LSA so that the routers in
the area can reach external networks through the default route.

#### NSSA area and totally NSSA area

An NSSA area does not import AS external
LSAs (Type-5 LSAs) but can import Type-7 LSAs generated by the NSSA ASBR. The NSSA
ABR translates Type-7 LSAs into Type-5 LSAs and advertises the Type-5 LSAs to
other areas.

As shown in [Figure 4](#_Ref302742395), the OSPF
AS contains Area 1, Area 2, and Area 0\. The other two ASs run RIP. Area 1 is an
NSSA area where the ASBR redistributes RIP routes in Type-7 LSAs into Area 1\. Upon
receiving the Type-7 LSAs, the NSSA ABR translates them to Type-5 LSAs, and
advertises the Type-5 LSAs to Area 0\.

The ASBR of Area 2 redistributes RIP routes
in Type-5 LSAs into the OSPF routing domain. However, Area 1 does not receive Type-5
LSAs because it is an NSSA area.

Figure 4 NSSA area

![](https://resource.h3c.com/en/202407/12/20240712_11704771_x_Img_x_png_3_2216018_294551_0.png)

‌

### Router types

As shown in [Figure 5](#_Ref456794652), OSPF
routers are classified into different types, including internal routers, ABRs,
backbone routers, and ASBRs.

Figure 5 OSPF router types

![](https://resource.h3c.com/en/202407/12/20240712_11704772_x_Img_x_png_4_2216018_294551_0.png)

‌

#### Internal router

All interfaces on an internal router belong
to one OSPF area.

#### ABR

An ABR belongs to more than two areas, one
of which must be the backbone area. ABR connects the backbone area to a non-backbone
area. An ABR and the backbone area can be connected through a physical or
logical link.

#### Backbone router

No less than one interface of a backbone
router must reside in the backbone area. All ABRs and internal routers in Area
0 are backbone routers.

#### ASBR

An ASBR exchanges routing information with
another AS. An ASBR might not reside on the border of the AS. It can be an
internal router or an ABR.

### Route types

OSPF prioritizes routes into the following route
levels:

·     Intra-area route.

·     Inter-area route.

·     Type-1 external route.

·     Type-2 external route.

The intra-area and inter-area routes
describe the network topology of the AS. The external routes describe routes to
external ASs.

A Type-1 external route has high
credibility. The cost of a Type-1 external route \= the cost from the router to
the corresponding ASBR \+ the cost from the ASBR to the destination of the external
route.

A Type-2 external route has low credibility.
OSPF considers that the cost from the ASBR to the destination of a Type-2 external
route is much greater than the cost from the ASBR to an OSPF internal router. The
cost of a Type-2 external route \= the cost from the ASBR to the destination of
the Type-2 external route. If two Type-2 routes to the same destination have
the same cost, OSPF takes the cost from the router to the ASBR into
consideration to determine the best route.

### Router ID

A router ID uniquely identifies a router in
an AS. For a router to run OSPF, it must have a router ID. You can choose to
manually specify a router ID or use the global router ID for an OSPF process.

#### Manual configuration

When you create an OSPF process, you can manually
specify a router ID. To make sure the router ID is unique in the AS, you can
specify the IP address of an interface on the router as the router ID.

#### Using the global router ID

If you do not specify a router ID when
creating an OSPF process, the global router ID is used. As a best practice,
manually specify a router ID or enable the OSPF process to automatically obtain
a router ID when you create the OSPF process.

### Route calculation

OSPF computes routes in an area as follows:

·     Each router generates LSAs based on the network
topology around itself, and sends them to other routers in update packets.

·     Each OSPF router collects LSAs from other
routers to compose an LSDB. An LSA describes the network topology around a
router, and the LSDB describes the entire network topology of the area.

·     Each router transforms the LSDB to a weighted
directed graph that shows the topology of the area. All the routers within the
area have the same graph.

·     Each router uses the SPF algorithm to compute a shortest
path tree that shows the routes to the nodes in the area. The router itself is
the root of the tree.

### OSPF network types

OSPF classifies networks into the following
types, depending on different link layer protocols:

·     Broadcast—If the link layer protocol is Ethernet or FDDI, OSPF considers the
network type as broadcast by default. On a broadcast network, hello, LSU, and
LSAck packets are multicast to 224.0.0.5 that identifies all OSPF routers or to
224.0.0.6 that identifies the DR and BDR. DD packets and LSR packets are
unicast.

·     NBMA—If the link layer protocol is Frame Relay, ATM, or X.25, OSPF
considers the network type as NBMA by default. OSPF packets are unicast on an
NBMA network.

·     P2MP—No link is P2MP type by default. P2MP must be a conversion from
other network types such as NBMA. On a P2MP network, OSPF packets are multicast
to 224.0.0.5.

·     P2P—If the link layer protocol is PPP or HDLC, OSPF considers the
network type as P2P. On a P2P network, OSPF packets are multicast to 224.0.0.5.

The following are the differences between
NBMA and P2MP networks:

·     NBMA networks are fully meshed. P2MP networks
are not required to be fully meshed.

·     NBMA networks require DR and BDR election. P2MP
networks do not have DR or BDR.

·     On an NBMA network, OSPF packets are unicast,
and neighbors are manually configured. On a P2MP network, OSPF packets are
multicast by default, and you can configure OSPF to unicast protocol packets.

### DR andBDR

#### DR and BDR mechanism

On a broadcast or NBMA network, any two
routers must establish an adjacency to exchange routing information with each
other. If n routers are present on the network, n(n-1)/2 adjacencies are established.
Any topology change on the network results in an increase in traffic for route
synchronization, which consumes a large amount of system and bandwidth resources.

Using the DR and BDR mechanisms can solve this
problem.

·     DR—Elected to advertise routing information among other routers. If
the DR fails, routers on the network must elect another DR and synchronize
information with the new DR. Using this mechanism without BDR is time-consuming
and is prone to route calculation errors.

·     BDR—Elected along with the DR to establish adjacencies with all other
routers. If the DR fails, the BDR immediately becomes the new DR, and other routers
elect a new BDR.

Routers other than the DR and BDR are
called DR Others. They do not establish adjacencies with one another, so the number
of adjacencies is reduced.

The role of a router is subnet (or
interface) specific. It might be a DR on one interface and a BDR or DR Other on
another interface.

As shown in [Figure 6](#_Ref278555389), solid
lines are Ethernet physical links, and dashed lines represent OSPF adjacencies.
With the DR and BDR, only seven adjacencies are established.

Figure 6 DR and BDR in a network

![](https://resource.h3c.com/en/202407/12/20240712_11704773_x_Img_x_png_5_2216018_294551_0.png)

‌

|  | NOTE:  In OSPF, neighbor and adjacency are different concepts. After startup, OSPF sends a hello packet on each OSPF interface. A receiving router checks parameters in the packet. If the parameters match its own, the receiving router considers the sending router an OSPF neighbor. Two OSPF neighbors establish an adjacency relationship after they synchronize their LSDBs through exchange of DD packets and LSAs. |
| --- | --- |

‌

#### DR and BDR election

DR election is performed on broadcast or NBMA
networks but not on P2P and P2MP networks.

Routers in a broadcast or NBMA network
elect the DR and BDR by router priority and ID. Routers with a router priority
value higher than 0 are candidates for DR and BDR election.

The election votes are hello packets. Each
router sends the DR elected by itself in a hello packet to all the other
routers. If two routers on the network declare themselves as the DR, the router
with the higher router priority wins. If router priorities are the same, the
router with the higher router ID wins.

If a router with a higher router priority becomes
active after DR and BDR election, the router cannot replace the DR or BDR until
a new election is performed. Therefore, the DR of a network might not be the
router with the highest priority, and the BDR might not be the router with the
second highest priority.

### Protocols and standards

·     RFC 1245, OSPF protocol
analysis

·     RFC 1246, Experience with
the OSPF protocol

·     RFC 1370, Applicability
Statement for OSPF

·     RFC 1403, BGP OSPF
Interaction

·     RFC 1745, BGP4/IDRP for
IP---OSPF Interaction

·     RFC 1765, OSPF
Database Overflow

·     RFC 1793, Extending OSPF
to Support Demand Circuits

·     RFC 2154, OSPF
with Digital Signatures

·     RFC 2328, OSPF Version 2

·     RFC 3101, OSPF
Not-So-Stubby Area (NSSA) Option

·     RFC 3166, Request to Move
RFC 1403 to Historic Status

·     RFC 3509, Alternative
Implementations of OSPF Area Border Routers

·     RFC 4167, Graceful OSPF
Restart Implementation Report

·     RFC 4577, OSPF as the
Provider/Customer Edge Protocol for BGP/MPLS IP Virtual Private Networks (VPNs)

·     RFC 4750, OSPF Version 2
Management Information Base

·     RFC 4811, OSPF
Out-of-Band LSDB Resynchronization

·     RFC 4812, OSPF
Restart Signaling

·     RFC 5088, OSPF Protocol
Extensions for Path Computation Element (PCE) Discovery

·     RFC 5250, The OSPF Opaque
LSA Option

·     RFC 5613, OSPF Link-Local
Signaling

·     RFC 5642, Dynamic
Hostname Exchange Mechanism for OSPF

·     RFC 5709, OSPFv2 HMAC-SHA
Cryptographic Authentication

·     RFC 6571, Loop-Free
Alternate (LFA) Applicability in Service Provider (SP) Networks

·     RFC 6860, Hiding
Transit-Only Networks in OSPF

·     RFC 6987, OSPF
Stub Router Advertisement

## Restrictions and guidelines: OSPF configuration

To run OSPF, you must first enable OSPF on
the router. Make a proper configuration plan to avoid incorrect settings that
can result in route blocking and routing loops.

## OSPF tasks at a glance

To configure OSPF, perform the following
tasks:

**1\.**[Configuring basic OSPF functions](#_Ref479951669)

¡     [Enabling an OSPF process](#_Ref479951666)

¡     [Creating an OSPF area](#_Ref479951693)

¡     [Enabling OSPF](#_Ref479951701)

¡     (Optional.)[Enabling OSPF to establish neighbor
relationships through the secondary IP addresses of an interface](#_Ref82033419)

¡     (Optional.) [Establishing OSPF neighbor relationships on
an M-LAG network](#_Ref82033420)

**2\.**(Optional.) [Configuring OSPF stub and NSSA areas](#_Ref82033421)

¡     [Configuring a stub area](#_Ref189536324)

¡     [Configuring an NSSA area](#_Ref189536327)

¡     [Configuring a virtual link](#_Ref189536328)

**3\.**(Optional.) [Configuring OSPF network types](#_Ref157313018)

¡     [Configuring the broadcast network type for
an interface](#_Ref157323443)

¡     [Configuring the NBMA network type for an
interface](#_Ref189536419)

¡     [Configuring the P2MP network type for an
interface](#_Ref189536421)

¡     [Configuring the P2P network type for an
interface](#_Ref189536422)

**4\.**(Optional.) [Configuring OSPF route control](#_Ref158978651)

¡     [Configuring OSPF inter-area route
summarization](#_Ref157323454)

¡     [Configuring redistributed route
summarization](#_Ref475989732)

¡     [Configuring received OSPF route filtering](#_Ref157323456)

¡     [Configuring Type-3 LSA filtering](#_Ref157323458)

¡     [Setting an OSPF cost for an interface](#_Ref324404325)

¡     [Enabling OSPF to advertise the maximum link
cost to neighbors](#_Ref32742553)

¡     [Associating OSPF with Track](#_Ref78373751)

¡     [Setting the maximum number of ECMP routes](#_Ref157323462)

¡     [Setting OSPF preference](#_Ref157323464)

¡     [Configuring discard routes for summary
networks](#_Ref373258813)

¡     [Redistributing routes from another routing
protocol](#_Ref475989775)

¡     [Redistributing a default route](#_Ref303174785)

¡     [Advertising a host route](#_Ref475989791)

¡     [Advertising OSPF link state information to
BGP](#_Ref456863656)

**5\.**(Optional.) [Setting OSPF timers](#_Ref157323472)

¡     [Configuring OSPF packet timers](#_Ref475989801)

¡     [Setting LSA transmission delay](#_Ref157323474)

¡     [Setting SPF calculation interval](#_Ref157323476)

¡     [Setting the minimum LSA arrival interval](#_Ref157323479)

¡     [Setting the LSA update interval](#_Ref157323480)

¡     [Setting OSPF exit overflow interval](#_Ref475989844)

**6\.**(Optional.) [Configuring OSPF packet parameters](#_Ref475989877)

¡     [Disabling interfaces from receiving and sending
OSPF packets](#_Ref157323484)

¡     [Adding the interface MTU into DD packets](#_Ref475989888)

¡     [Setting the DSCP value for outgoing OSPF
packets](#_Ref475989903)

¡     [Setting the maximum length of OSPF packets
that can be sent by an interface](#_Ref475989906)

¡     [Setting the LSU transmit rate](#_Ref484700495)

**7\.**(Optional.) [Controlling LSA generation, advertisement,
and reception](#_Ref475989924)

¡     [Setting the maximum number of external LSAs
in LSDB](#_Ref475989928)

¡     [Filtering outbound LSAs on an interface](#_Ref475989931)

¡     [Filtering LSAs for the specified neighbor](#_Ref475989936)

**8\.**(Optional.) [Accelerating OSPF convergence speed](#_Ref476928796)

¡     [Enabling OSPF ISPF](#_Ref475989951)

¡     [Configuring prefix suppression](#_Ref475989961)

¡     [Configuring prefix prioritization](#_Ref475989967)

¡     [Configuring OSPF PIC](#_Ref475989973)

**9\.**(Optional.) [Configuring advanced OSPF features](#_Ref475989979)

¡     [Configuring stub routers](#_Ref157323487)

¡     [Configuring OSPF isolation](#_Ref58340909)

¡     [Configuring OSPF shutdown](#_Ref72329207)

¡     [Enabling compatibility with RFC 1583](#_Ref67670700)

**10\.** (Optional.) Enhancing OSPF availability

¡     [Configuring OSPF GR](#_Ref475990011)

¡     [Configuring OSPF NSR](#_Ref326590774)

¡     [Configuring BFD for OSPF](#_Ref354774888)

¡     [Configuring OSPF FRR](#_Ref203367652)

**11\.**(Optional.) Configuring OSPF security
features

¡     [Configuring OSPF authentication](#_Ref475990079)

¡     [Configuring GTSM for OSPF](#_Ref475990084)

**12\.**(Optional.) [Configuring OSPF logging, SNMP
notifications, and troubleshooting](#_Ref475990090)

¡     [Logging neighbor state changes](#_Ref475990099)

¡     [Configuring the OSPF logging feature](#_Ref475990102)

¡     [Configuring OSPF network management](#_Ref475990105)

¡     [Setting the maximum number of OSPF neighbor
relationship troubleshooting entries](#_Ref15920212)

## Configuring basic OSPF functions

### Enabling an OSPF process

**1\.**Enter system view.

system-view

**2\.**(Optional.) Configure a global router ID.

router id router-id

By default, no global router ID is
configured.

If no global router ID is configured, the
highest loopback interface IP address, if any, is used as the router ID. If no
loopback interface IP address is available, the highest physical interface IP
address is used, regardless of the interface status (up or down).

**3\.**Enter OSPF view.

ospf \[ process-id \| router-id router-id \| vpn-instance
vpn-instance-name
] \*

By default, OSPF is disabled.

**4\.**(Optional.) Configure a description for the
OSPF process.

description text

By default, no description is configured
for the OSPF process.

As a best practice, configure a
description for each OSPF process.

### Creating an OSPF area

**1\.**Enter system view.

system-view

**2\.**(Optional.) Configure a global router ID.

router id router-id

By default, no global router ID is
configured.

**3\.**Enter OSPF view.

ospf \[ process-id \| router-id router-id \| vpn-instance
vpn-instance-name
] \*

By default, OSPF is disabled.

**4\.**(Optional.) Configure a description for the
OSPF process.

description text

By default, no description is configured
for the OSPF process.

As a best practice, configure a
description for each OSPF process.

**5\.**Create an OSPF area, and enter OSPF area
view.

area area-id

**6\.**(Optional.) Configure a description for the
area.

description text

By default, no description is configured
for the area.

As a best practice, configure a
description for each OSPF area.

**7\.**(Optional.) Exclude interfaces in the OSPF
area from the base topology:

capability default-exclusion

By default, interfaces in an OSPF area
belong to the base topology.

For correct neighbor relationship
establishment, perform this task on both the local device and the neighbor device.

### Enabling OSPF

#### About this task

To enable OSPF on a router, you must
perform the following tasks:

**1\.**Create an OSPF process.

**2\.**Create an OSPF area for the process.

**3\.**Specify a network in the area.

The interface attached to the network will run
the OSPF process in the area. OSPF advertises direct routes of the interface.

OSPF supports multiple processes. To run
multiple OSPF processes, you must specify an ID for each process. The process
IDs take effect locally and has no influence on packet exchange between
routers. Two routers with different process IDs can exchange packets.

OSPF supports multiple VPNs. You can
configure an OSPF process to run in a VPN instance. An OSPF process with no VPN
instance specified runs on the public network. For more information about VPN,
see MPLS Configuration Guide. 

#### Restrictions and guidelines for enabling OSPF

When you configure OSPF on an interface,
follow these restrictions and guidelines:

·     You can enable OSPF on the network where the
interface resides or directly enable OSPF on that interface. If you configure
both, the latter takes precedence.

·     If the specified OSPF process and area do not
exist, the operation creates an OSPF process and area for the interface. Disabling
an OSPF process on an interface does not delete the OSPF process or the area.

#### Enabling OSPF on a network

**1\.**Enter system view.

system-view

**2\.**Enter OSPF view.

ospf \[ process-id \| router-id router-id \| vpn-instance
vpn-instance-name
] \*

**3\.**Enter OSPF area view.

area area-id

**4\.**Specify a network to enable the interface
attached to the network to run the OSPF process in the area.

network ip-address wildcard-mask

By default, no network is specified to
enable OSPF on the interface attached to the network.

A network can be added to only one area.

#### Enabling OSPF on an interface

**1\.**Enter system view.

system-view

**2\.**Enter interface view.

interface interface-type
interface-number

**3\.**Enable an OSPF process on the interface.

ospf process-id area area-id \[ exclude-subip ]

By default, OSPF is disabled on an
interface.

### Enabling OSPF to establish neighbor relationships through the secondary IP addresses of an interface

#### About this task

By default, OSPF uses only the primary IP
address of an interface to establish neighbor relationships. This feature enables
OSPF to establish neighbor relationships through the secondary IP addresses of
an interface.

#### Restrictions and guidelines

·     For an interface with both primary and secondary
addresses, if you have advertised only the primary address in an area of an
OSPF process, OSPF uses only the primary address to establish neighbor
relationships. For OSPF to establish neighbor relationships through the
secondary IP addresses, you must also advertise the secondary IP addresses in
the same area of the same OSPF process.

·     If an interface does not have a primary address
but has multiple secondary addresses, OSPF uses the lowest secondary address to
establish neighbor relationships. For OSPF to establish neighbor relationships
through the other secondary addresses, you must advertise these addresses in
the same area of the same OSPF process.

·     OSPF cannot use secondary addresses for neighbor
establishment on a P2P link if the local and remote addresses of the link
belong to different networks.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter OSPF view.

ospf \[ process-id \| router-id router-id \| vpn-instance
vpn-instance-name
] \*

**3\.**Enter interface view.

interface interface-type
interface-number

**4\.**Enable OSPF to establish neighbor
relationships through the secondary IP addresses of the interface.

ospf peer sub-address enable

By default, OSPF cannot establish
neighbor relationships through the secondary IP addresses of an interface.

### Establishing OSPF neighbor relationships onan M-LAG network

#### About this task

When a customer-side device accesses the
OSPF network through both M-LAG member devices in an M-LAG system, the M-LAG
member devices provide Layer 3 gateway services. The gateway interfaces (such
as VLAN or VSI interfaces) on different M-LAG member devices might have the
same IP address and the same MAC address. In this case, the M-LAG member
devices cannot establish OSPF neighbor relationships with the customer-side
device.

To resolve this issue, configure the
gateway interfaces to use M-LAG virtual IPv4 addresses for OSPF neighbor
relationship establishment. For more information about M-LAG, see M-LAG
configuration in Layer 2—LAN Switching Configuration
Guide.

As shown in [Figure 7](#_Ref82033559), Device
C accesses the OSPF network through the M-LAG system that consists of Device A
and Device B. Device A and Device B provide Layer 3 gateway services. The
gateway interface VLAN-interface 100 on Device A and the gateway interface
VLAN-interface 100 on Device B have the same IP address and the same MAC
address. To ensure that both Device A and Device B can establish an OSPF
neighbor relationship with Device C, perform the following tasks:

**1\.**Configure different M-LAG virtual IPv4
addresses for the VLAN interfaces on Device A and Device B.

**2\.**Configure the VLAN interface to use the M-LAG
virtual IPv4 address for OSPF neighbor relationship establishment on both
Device A and Device B.

Figure 7 OSPF neighbor relationship
establishment on an M-LAG network

![](https://resource.h3c.com/en/202407/12/20240712_11704774_x_Img_x_png_6_2216018_294551_0.png)  

‌

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter interface view.

interface interface-type
interface-number

**3\.**Configure an M-LAG virtual IPv4 address for
the interface.

port m-lag virtual-ip ipv4-address { mask-length \| mask }

By default, no M-LAG virtual IPv4 address
is configured for an interface.

An M-LAG virtual IPv4 address is similar
to a secondary IP address. For more information about this command, see M-LAG
commands in Layer 2—LAN Switching Command Reference.

**4\.**Configure the interface to use the M-LAG
virtual IPv4 address for OSPF neighbor relationship establishment.

ospf peer sub-address enable ip-address

By default, an interface uses the primary
IP address for OSPF neighbor relationship establishment.

The IP address specified in this command
must be the same as the IPv4 address specified in the port m-lag virtual-ip command.

## Configuring OSPF stub and NSSA areas

### About OSPF stub and NSSA area configuration

This task allows you to configure an OSPF
area as a stub area or NSSA area. It also allows you to create a virtual link
if no connectivity can be achieved between a non-backbone area and backbone
area, or in the backbone area.

### Configuring a stub area

#### About this task

You can configure a non-backbone area at an
AS edge as a stub area. To do so, execute the stub command on all
routers attached to the area. The routing table size is reduced because Type-5
LSAs will not be flooded within the stub area. The ABR generates a default
route into the stub area so all packets destined outside of the AS are sent
through the default route.

To further reduce the routing table size
and routing information exchanged in the stub area, configure a totally stub
area by using the stub no-summary command
on the ABR. AS external routes and inter-area routes will not be distributed
into the area. All the packets destined for outside of the AS or area will be
sent to the ABR for forwarding.

A stub or totally stub area cannot have an
ASBR because external routes cannot be distributed into the area.

#### Restrictions and guidelines

Do not configure the backbone area as a
stub area or totally stub area.

To configure an area as a stub area, execute
the stub command on all routers attached to the area.

To configure an area as a totally stub area,
execute the stub command on all
routers attached to the area, and execute the stub no-summary command on the ABR.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter OSPF view.

ospf \[ process-id \| router-id router-id \| vpn-instance
vpn-instance-name
] \*

**3\.**Enter area view.

area area-id

**4\.**Configure the area as a stub area.

stub \[ default-route-advertise-always \| no-summary ] \*

By default, no stub area is configured.

**5\.**(Optional.) Set a cost for the default route
advertised to the stub area.

default-cost cost-value

By default, the cost for the default
route advertised to the stub area is 1\.

This command takes effect only on the ABR
of a stub area or totally stub area.

### Configuring an NSSA area

#### About this task

A stub area cannot import external routes,
but an NSSA area can import external routes into the OSPF routing domain while retaining
other stub area characteristics.

To configure an area as a totally NSSA
area, use the nssa no-summary command.
The ABR of the area does not advertise inter-area routes into the area.

#### Restrictions and guidelines

Do not configure the backbone area as an
NSSA area or totally NSSA area.

To configure an NSSA area, configure the nssa command on all the routers attached to the area.

To configure a totally NSSA area, configure
the nssa command on all the routers attached to the area and configure the nssa no-summary command on the ABR.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter OSPF view.

ospf \[ process-id \| router-id router-id \| vpn-instance
vpn-instance-name
] \*

**3\.**Enter area view.

area area-id

**4\.**Configure the area as an NSSA area.

nssa \[ default-route-advertise \[ cost cost-value \| nssa-only \| route-policy route-policy-name \| type type ] \* \| no-import-route \| no-summary \| suppress-fa \| \[ \[ \[ translate-always ] \[ translate-ignore-checking-backbone ] ] \| translate-never ] \| translator-stability-interval value ] \*

By default, no area is configured as an
NSSA area.

**5\.**(Optional.) Set a cost for the default route
advertised to the NSSA area.

default-cost cost-value

By default, the cost for the default
route advertised to the NSSA area is 1\.

This command takes effect only on the
ABR/ASBR on an NSSA area or totally NSSA area.

### Configuring a virtual link

#### About this task

You can configure a virtual link to
maintain connectivity between a non-backbone area and the backbone, or in the
backbone itself.

#### Restrictions and guidelines

A virtual link cannot traverse a stub area,
totally stub area, NSSA area, or totally NSSA area.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter OSPF view.

ospf \[ process-id \| router-id router-id \| vpn-instance
vpn-instance-name
] \*

**3\.**Enter OSPF area view.

area area-id

**4\.**Configure a virtual link.

vlink-peer router-id \[ dead seconds \| hello seconds \| { authentication-none \| { hmac-md5 \| hmac-sha-256 \| hmac-sm3 \| md5 } \[ key-id { cipher \| plain } string ] \| keychain keychain-name \| simple \[ { cipher \| plain } string ] } \| retransmit seconds \| trans-delay seconds ] \*

Configure this command on both ends of a
virtual link. The hello and dead intervals must be identical on both ends of the virtual link.

## Configuring OSPF network types

Based on the link layer protocol, OSPF
classifies networks into different types, including broadcast, NBMA, P2MP, and
P2P.

### Restrictions and guidelines for configuring OSPF network types

If any routers in a broadcast network do
not support multicasting, change the network type to NBMA.

If only two routers running OSPF exist on a
network segment, you can change the network type to P2P to save costs.

Two
broadcast-, NBMA-, and P2MP-interfaces can establish a neighbor relationship only
when they are on the same network segment.

### Configuring the broadcast network type for an interface

**1\.**Enter system view.

system-view

**2\.**Enter interface view.

interface interface-type
interface-number

**3\.**Configure the OSPF network type for the
interface as broadcast.

ospf network-type broadcast

By default, the network type of an
interface depends on the encapsulated link layer protocol.

**4\.**(Optional.) Set a router priority for the
interface.

ospf dr-priority priority

The default router priority is 1\.

### Configuring the NBMA network type for an interface

#### Restrictions and guidelines

After you configure the network type as
NBMA, you must specify neighbors and their router priorities because NBMA
interfaces cannot find neighbors by broadcasting hello packets.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter interface view.

interface interface-type
interface-number

**3\.**Configure the OSPF network type for the
interface as NBMA.

ospf network-type nbma

By default, the network type of an
interface depends on the encapsulated link layer protocol. 

**4\.**(Optional.) Set a router priority for the
interface.

ospf dr-priority priority

The default router priority for an
interface is 1\.

The router priority configured with this
command is for DR election.

**5\.**Return to system view.

quit

**6\.**Enter OSPF view.

ospf \[ process-id \| router-id router-id \| vpn-instance
vpn-instance-name
] \*

**7\.**Specify an NBMA neighbor.

peer ip-address \[ dr-priority priority ]

By default, no neighbor is specified.

The priority configured with this command
indicates whether a neighbor has the election right or not. If you configure
the router priority for a neighbor as 0, the local router determines the
neighbor has no election right. It does not send hello packets to this
neighbor. However, if the local router is the DR or BDR, it still sends hello
packets to the neighbor for neighbor relationship establishment.

### Configuring the P2MP network type for an interface

**1\.**Enter system view.

system-view

**2\.**Enter interface view.

interface interface-type
interface-number

**3\.**Configure the OSPF network type for the
interface as P2MP.

ospf network-type p2mp \[ unicast ]

By default, the network type of an
interface depends on the encapsulated link layer protocol.

After you configure the OSPF network type
for an interface as P2MP unicast, all packets are unicast over the interface.
The interface cannot broadcast hello packets to discover neighbors, so you must
manually specify the neighbors. 

**4\.**Return to system view.

quit

**5\.**Enter OSPF view.

ospf \[ process-id \| router-id router-id \| vpn-instance
vpn-instance-name
] \*

**6\.**Specify a P2MP neighbor.

peer ip-address \[ cost cost-value ]

By default, no neighbor is specified

This step is required if the interface
network type is P2MP unicast.

### Configuring the P2P network type for an interface

**1\.**Enter system view.

system-view

**2\.**Enter interface view.

interface interface-type
interface-number

**3\.**Configure the OSPF network type for the
interface as P2P.

ospf network-type p2p \[ peer-address-check ]

By default, the network type of an
interface depends on the encapsulated link layer protocol.

## Configuring OSPF route control

This section describes how to control the
advertisement and reception of OSPF routing information, as well as route redistribution
from other protocols.

### Configuring OSPF inter-area route summarization

#### About this task

OSPF inter-area route summarization reduces
the routing information exchanged between areas and the size of routing tables,
and improves routing performance.

OSPF inter-area route summarization enables
an ABR to summarize contiguous networks into a single network and advertise the
network to other areas. For example, three internal networks 19.1.1.0/24, 19.1.2.0/24, and 19.1.3.0/24 are available within an area. You can configure the
ABR to summarize the three networks into network 19.1.0.0/16, and advertise the
summary network to other areas in a Type-3 LSA. This configuration reduces the
scale of LSDBs on routers in other areas and the influence of topology changes.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter OSPF view.

ospf \[ process-id \| router-id router-id \| vpn-instance
vpn-instance-name
] \*

**3\.**Enter OSPF area view.

area area-id

**4\.**Configure ABR route summarization.

abr-summary ip-address { mask-length \| mask } \[ advertise \| not-advertise ] \[ cost cost-value ]

By default, route summarization is not
configured on an ABR.

### Configuring redistributed route summarization

#### About this task

Perform this task to enable an ASBR to
summarize external routes within the specified address range into a single
route. The ASBR advertises only Type-5 LSAs to reduce the number of LSAs in the
LSDB.

An ASBR can summarize routes in the
following LSAs:

·     Type-5 LSAs.

·     Type-7 LSAs in an NSSA area.

#### Restrictions and guidelines

If an ASBR (also an ABR) is a translator in
an NSSA area, it summarizes routes in Type-5 LSAs translated from Type-7 LSAs.
If it is not a translator, it does not summarize routes in in Type-5 LSAs
translated from Type-7 LSAs.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter OSPF view.

ospf \[ process-id \| router-id router-id \| vpn-instance
vpn-instance-name
] \*

**3\.**Configure ASBR route summarization.

asbr-summary ip-address { mask-length \| mask } \[ cost cost-value \| not-advertise \| nssa-only \| tag tag ] \*

By default, route summarization is not configured
on an ASBR.

### Configuring received OSPF route filtering

#### About this task

Perform this task to filter routes
calculated using received LSAs.

The following filtering methods are
available:

·     Use an ACL or IP prefix list to filter routing
information by destination address.

·     Use the gateway prefix-list-name option to filter routing information by next hop.

·     Use an ACL or IP prefix list to filter routing
information by destination address. At the same time use the gateway prefix-list-name option
to filter routing information by next hop.

·     Use the route-policy
route-policy-name option to filter routing information.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter OSPF view.

ospf \[ process-id \| router-id router-id \| vpn-instance
vpn-instance-name
] \*

**3\.**Configure OSPF to filter routes calculated
using received LSAs.

filter-policy {
ipv4-acl-number \[ gateway prefix-list-name ] \| gateway prefix-list-name \| prefix-list prefix-list-name \[ gateway prefix-list-name ] \| route-policy route-policy-name } import

By default, OSPF accepts all routes
calculated by using received LSAs.

### Configuring Type-3 LSA filtering

#### About this task

Perform this task to filter Type-3 LSAs advertised
into the local area or other areas on an ABR.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter OSPF view.

ospf \[ process-id \| router-id router-id \| vpn-instance
vpn-instance-name
] \*

**3\.**Enter OSPF area view.

area area-id

**4\.**Configure Type-3 LSA filtering.

filter { ipv4-acl-number \| prefix-list prefix-list-name
\| route-policy route-policy-name
} { export \| import }

By default, the ABR does not filter
Type-3 LSAs.

### Setting an OSPF cost for an interface

#### About this task

Set an OSPF cost for an interface by using
either of the following methods:

·     Set the cost value in interface view.

·     Set a bandwidth reference value for the interface.
OSPF computes the cost with this formula: Interface OSPF cost \= Bandwidth
reference value (100 Mbps) / Expected interface bandwidth (Mbps). The expected bandwidth
of an interface is configured with the bandwidth
command (see Interface Command Reference).

¡     If
the calculated cost is greater than 65535, the value of 65535 is used. If the
calculated cost is less than 1, the value of 1 is used.

¡     If
no cost or bandwidth reference value is configured for an interface, OSPF
computes the interface cost based on the interface bandwidth and default
bandwidth reference value.

#### Setting an OSPF cost for an interface

**1\.**Enter system view.

system-view

**2\.**Enter interface view.

interface interface-type
interface-number

**3\.**Set an OSPF cost for the interface.

ospf cost cost\-value

By default, the OSPF cost is calculated
according to the interface bandwidth. For a loopback interface, the OSPF cost
is 0 by default.

#### Setting a bandwidth reference value

**1\.**Enter system view.

system-view

**2\.**Enter OSPF view.

ospf \[ process-id \| router-id router-id \| vpn-instance
vpn-instance-name
] \*

**3\.**Set a bandwidth reference value.

bandwidth-reference value

The default setting is 100 Mbps.

### Enabling OSPF to advertise the maximum link cost to neighbors

#### About this task

On an OSPF network, when a link recovers from failures or the state
of an interface changes, OSPF will re-establish neighbor relationships and perform
route convergence. During the route convergence process, routing loops and traffic
loss might occur because the convergence speeds of the nodes are different. To address
this issue, enable OSPF to advertise the maximum link cost to neighbors within the
specified period, so the traffic forwarding path remains unchanged.After the specified period, OSPF advertises the original link cost
to neighbors and performs optimal route selection again.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter interface view.

interface interface-type
interface-number

**3\.**Enable OSPF to advertise the maximum link
cost to neighbors within the specified period.

ospf peer hold-max-cost
duration
time

By default, OSPF advertises the original link
cost to neighbors during a route convergence.

### Associating OSPF with Track

#### About this task

Perform this task to associate an OSPF
interface with a track entry to adjust the cost of the interface as follows:

·     When the track entry state becomes negative,
OSPF increases the cost of the interface.

·     When the track entry state becomes positive,
OSPF restores the cost of the interface to the original value.

When a customer side device is dual-homed
to an OSPF network through M-LAG, you can configure this feature to prevent the
traffic from detouring through the M-LAG peer link. As shown in [Figure 8](#_Ref97048865),
customer-side device Device C is dual-homed to the OSPF network through an M-LAG
system formed by Device A and Device B. Device A and Device B share the traffic
load between Device D and Device C. When the DR interface that connects Device
B to Device C fails, the upstream traffic and downstream traffic are forwarded
as follows:

·     Device C detects the M-LAG interface failure,
and traffic from Device C to Device D is switched from path Device C-\>Device
B-\>Device D to path Device C-\>Device A-\>Device D.

·     Device D cannot detect the M-LAG interface
failure and still forwards traffic through Device B. Because the link between
Device B and Device C fails, traffic from Device D to Device C is switched from
path Device D-\>Device B-\>Device C to path Device D-\>Device
B-\>Device A-\>Device C.

Figure 8 Traffic forwarding path after M-LAG
interface failure

![](https://resource.h3c.com/en/202407/12/20240712_11704775_x_Img_x_png_7_2216018_294551_0.png)

‌

To prevent the downstream traffic from
detouring through the M-LAG peer link, perform the following tasks on Device B:

**1\.**Execute the track interface command to create a track entry and associate it with the link
state of the M-LAG interface. For more information about the track interface command, see track commands in High
Availability Command Reference.

**2\.**Execute the ospf track
adjust-cost command to associate the gateway
interface with the track entry.

Then, Device B adjusts the cost of the
gateway interface based on the state of the associated track entry:

·     When the M-LAG interface fails, the track entry
state becomes negative, and OSPF increases the cost of the gateway interface.
Then, traffic from Device D to Device C is switched to path Device D-\>Device
A-\>Device C.

·     When the M-LAG interface restores, the track
entry state becomes positive, and OSPF restores the cost of the gateway
interface to the original value. Then, traffic from Device D to Device C is
switched back to path Device D-\>Device B-\>Device C.

Figure 9 Traffic forwarding path after associating OSPF with Track

![](https://resource.h3c.com/en/202407/12/20240712_11704776_x_Img_x_png_8_2216018_294551_0.png)

‌

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter interface view.

interface interface-type
interface-number

**3\.**Associate the OSPF interface with a track
entry to adjust the cost of the interface based on the track entry state.

ospf track track-entry-number adjust-cost { cost-offset \| max }

By default, an OSPF interface is not
associated with a track entry.

### Setting the maximum number of ECMP routes

#### About this task

OSPF might find multiple optimal equal-cost
routes to the same destination, which can be used to share the traffic load.
This task allows you to set the maximum number of ECMP routes for OSPF.

As shown in [Figure 10](#_Ref101815533),
Device A and Device B run OSPF. There are three equal-cost routes between Device
A and Device B. You can use these routes to implement load sharing.

Figure 10 Equal-cost routes

![](https://resource.h3c.com/en/202407/12/20240712_11704777_x_Img_x_png_9_2216018_294551_0.png)

‌

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter OSPF view.

ospf \[ process-id \| router-id router-id \| vpn-instance
vpn-instance-name
] \*

**3\.**Set the maximum number of ECMP routes.

maximum load-balancing number

By default, the maximum number of ECMP
routes equals the maximum number of ECMP routes supported by the system.

### Setting OSPF preference

#### About this task

A router can run multiple routing protocols,
and each protocol is assigned a preference. If multiple routes are available to
the same destination, the one with the highest protocol preference is selected
as the best route.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter OSPF view.

ospf \[ process-id \| router-id router-id \| vpn-instance
vpn-instance-name
] \*

**3\.**Set a preference for OSPF.

preference \[ ase ] { preference \| route-policy route-policy-name } \*

By default, the preference of OSPF
internal routes is 10 and the preference of OSPF external routes is 150\.

### Configuring discard routes for summary networks

#### About this task

Perform this task on an ABR or ASBR to
specify whether to generate discard routes for summary networks. You can also
specify a preference for the discard routes.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter OSPF view.

ospf \[ process-id \| router-id router-id \| vpn-instance
vpn-instance-name
] \*

**3\.**Configure discard routes for summary
networks.

discard-route { external { preference \| suppression } \| internal { preference \| suppression } } \*

By default, the ABR or ASBR generates
discard routes for summary networks and the default preference of discard
routes is 255\.

### Redistributing routes from another routing protocol

#### About this task

When an OSPF device does not have routes
from a non-OSPF routing protocol, it cannot access any devices that run the
non-OSPF routing protocol. To resolve this issue, you can configure OSPF to
redistribute routes from other protocols, such as IS-IS and BGP. OSPF can then
advertise the routes in Type-5 LSAs or Type-7 LSAs.

In addition, you can configure OSPF to filter
redistributed routes so that OSPF advertises only permitted routes.

#### Restrictions and guidelines

Although OSPF is a dynamic routing protocol
that provides loop-free intra-area/inter-area routes, this protocol is not good
at guarding against the routing loops caused by redistributed external routes.
When you configure route redistribution for OSPF, make sure you have understood
the potential impact.

OSPF redistributes only active routes. To view
route status information, use the display ip routing-table protocol command.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter OSPF view.

ospf \[ process-id \| router-id router-id \| vpn-instance
vpn-instance-name
] \*

**3\.**Configure route redistribution.

import-route bgp \[ as-number ] \[ allow-ibgp ] \[ \[ cost cost-value \| inherit-cost
] \| nssa-only \| route-policy route-policy-name \| tag tag \| type type ] \*

import-route { direct \| static } \[ \[ cost cost-value \| inherit-cost
] \| nssa-only \| route-policy route-policy-name \| tag tag \| type type ] \*

import-route { isis \| ospf \| rip } \[ process-id \| all-processes ] \[ allow-direct
\| \[ cost cost-value \| inherit-cost
] \| nssa-only \| route-policy route-policy-name \| tag tag \| type type ] \*

By default, OSPF does not redistribute
routes.

The import-route bgp command redistributes only EBGP routes. The import-route
bgp allow-ibgp command redistributes both EBGP
and IBGP routes, which might cause routing loops. Therefore, use it with
caution.

**4\.**(Optional.) Configure OSPF to filter
redistributed routes.

filter-policy {
ipv4-acl-number \| prefix-list prefix-list-name } export \[ protocol \[ process-id ] ]

By default, OSPF accepts all
redistributed routes.

**5\.**Configure the default parameters for
redistributed routes (cost, tag, and type).

default { cost cost-value \| tag tag \| type type } \*

By default, the cost is 1, the tag is 1,
and the route type is 2

### Redistributing a default route

#### About this task

On a OSPF network, multiple ABRs and ASBRs
work together to implement multi-egress redundancy or load balancing. To ease
the service burden on these devices and save routing table capacity, you can
configure default routes.

OSPF default routes are mostly used in the
following scenarios:

·     After receiving a Type 3 LSA that describes a
default route from the ABR, the intra-area router can use the route for packet
forwarding to another area in the same AS.

·     After receiving a Type 5 or 7 LSA that describes
a default route from the ASBR, the intra-area router can use the route for
packet forwarding to another AS.

When a router cannot find a exactly
matching route, it can use the default route for packet forwarding. The default
route originated from a Type 3 LSA takes precedence over that originated from a
Type 5 LSA.

As shown in [Table 1](#_Ref101861445), the
advertisement method for a redistributed default route varies by its area type.

Table 1 Advertisement of redistributed
default routes

| Area type | Route redistribution condition | Advertiser | LSA type | LSA flooding scope || Common area | Use the default-route-advertise command. | ASBR | Type 5 LSA | Common area || Stub area | Automatic route redistribution. | ABR | Type 3 LSA | Stub area || NSSA area | Use the nssa \[ default-route-advertise ] command. | ASBR | Type 7 LSA | NSSA area || Totally NSSA area | Automatic route redistribution. | ABR | Type 3 LSA | NSSA area |




| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |

‌

The import-route
command cannot redistribute a default route from an external routing protocol. To
resolve this issue, perform this task on the ASBR to redistribute a default
route.

#### Restrictions and guidelines

After receiving a default route from the
ASBR, the device can add the default route to its routing table in one of the
following scenarios:

·     The device does not have a static route.

·     The device has a static route, but the priority
value for the default route is higher than that for the static route.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter OSPF view.

ospf \[ process-id \| router-id router-id \| vpn-instance
vpn-instance-name
] \*

**3\.**Redistribute a default route.

default-route-advertise
\[ \[ always \| permit-calculate-other ] \| cost cost-value \| route-policy route-policy-name \| type type ] \* 

default-route-advertise
\[ summary cost cost-value ]

By default, no default route is
redistributed.

This command is applicable only to VPNs.
The PE router advertises a default route in a Type-3 LSA to a CE router.

**4\.**Configure the default parameters for
redistributed routes (cost, tag, and type).

default { cost cost-value \| tag tag \| type type } \*

By default, the cost is 1, the tag is 1,
and the route type is 2

### Advertising a host route

**1\.**Enter system view.

system-view

**2\.**Enter OSPF view.

ospf \[ process-id \| router-id router-id \| vpn-instance
vpn-instance-name
] \*

**3\.**Enter area view.

area area-id

**4\.**Advertise a host route.

host-advertise ip-address
cost-value

By default, OSPF does not advertise host
routes that are not in the area.

### Advertising OSPF link state information to BGP

#### About this task

After the device advertises OSPF link state
information to BGP, BGP can advertise the information for intended
applications. For more information about BGP LS, see "Configuring
BGP."

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter OSPF view.

ospf \[ process-id \| router-id router-id \| vpn-instance
vpn-instance-name
] \*

**3\.**Advertise OSPF link state information to
BGP.

distribute bgp-ls \[ strict-link-checking ]

By default, the device does not advertise
OSPF link state information to BGP.

## Setting OSPF timers

### About setting OSPF timers

This task allows you to change OSPF packet
timers to adjust the convergence speed and network load and tune the delay time
for sending LSAs on low-speed links.

### Configuring OSPF packet timers

#### About this task

An OSPF interface includes the following
timers:

·     Hello timer—Interval for sending
hello packets. It must be identical on OSPF neighbors.

·     Poll timer—Interval for sending
hello packets to a neighbor that is down on the NBMA network.

·     Dead timer—Interval within
which if the interface does not receive any hello packet from the neighbor, it
declares the neighbor is down.

·     LSA retransmission timer—Interval within
which if the interface does not receive any acknowledgment packets after
sending an LSA to the neighbor, it retransmits the LSA.

#### Restrictions and guidelines

The default value for the hello interval
and neighbor dead interval depends on the network type. When the network type
for an interface is changed, the default hello interval and neighbor dead
interval are restored. Make sure two neighboring interfaces are configured with
the same hello interval and neighbor dead interval. Inconsistent settings will
affect the OSPF neighbor relationship establishment.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter interface view.

interface interface-type
interface-number

**3\.**Set the hello interval.

ospf timer hello seconds

The default hello interval on P2P and broadcast
interfaces is 10 seconds. The default hello interval on P2MP and NBMA
interfaces is 30 seconds.

**4\.**Set the poll interval.

ospf timer poll seconds

The default setting is 120 seconds.

The poll interval is a minimum of four
times the hello interval.

**5\.**Set the dead interval.

ospf timer dead seconds

The default dead interval on P2P and
broadcast interfaces is 40 seconds. The default dead interval on P2MP and NBMA
interfaces is 120 seconds.

The dead interval must be a minimum of
four times the hello interval on an interface.

**6\.**Set the retransmission interval.

ospf timer retransmit interval

The default retransmission interval is 5
seconds.

A retransmission interval setting that is
too small can cause unnecessary LSA retransmissions. Typically set a bigger interval
than the round-trip time of a packet between two neighbors.

### Setting LSA transmission delay

#### About this task

To avoid LSAs from aging out during
transmission, set an LSA retransmission delay especially for low speed links.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter interface view.

interface interface-type
interface-number

**3\.**Set the LSA transmission delay.

ospf trans-delay seconds

The default LSA transmission delay is 1
second.

### Setting SPF calculation interval

#### About this task

LSDB changes result in SPF calculations. When
the topology changes frequently, a large amount of network and router resources
are occupied by SPF calculation. You can adjust the SPF calculation interval to
reduce the impact.

For a stable network, the minimum interval
is used. If network changes become frequent, the SPF calculation interval increases
by the incremental interval × 2n-2 for each
calculation until the maximum interval is reached. The value n is the number of calculation times.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter OSPF view.

ospf \[ process-id \| router-id router-id \| vpn-instance
vpn-instance-name
] \*

**3\.**Set the SPF calculation interval.

spf-schedule-interval maximum-interval
\[ minimum-interval \[ incremental-interval ] ]

By default, the maximum interval is 5
seconds, the minimum interval is 50 milliseconds, and the incremental interval
is 200 milliseconds.

### Setting the minimum LSA arrival interval

#### About this task

OSPF drops any duplicate LSAs (with the
same LSA type, LS ID, and router ID) within the minimum LSA arrival interval. This
helps avoid overuse of bandwidth and router resources due to frequent network
changes.

#### Restrictions and guidelines

On a stable network, you can set the LSA
arrival interval to 0 to fasten route convergence.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter OSPF view.

ospf \[ process-id \| router-id router-id \| vpn-instance
vpn-instance-name
] \*

**3\.**Set the minimum LSA arrival interval.

lsa-arrival-interval interval

By default, the minimum LSA arrival
interval is 1000 milliseconds.

### Setting the LSA update interval

#### About this task

Adjust the LSA update interval to protect
network resources and routers from being overwhelmed by LSAs at the time of frequent
network changes.

By default, OSPF adjusts the LSA update interval
automatically. For a stable network, the minimum interval is used. If network
changes become frequent, the LSA update interval is incremented by the incremental
interval × 2n-2 for each update until the maximum
interval is reached. The value n is the number of
update times.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter OSPF view.

ospf \[ process-id \| router-id router-id \| vpn-instance
vpn-instance-name
] \*

**3\.**Set the LSA update interval.

lsa-generation-interval
maximum-interval \[ minimum-interval \[ incremental-interval ] ]

By default, the maximum interval is 5
seconds, the minimum interval is 50 milliseconds, and the incremental interval
is 200 milliseconds.

### Setting OSPF exit overflow interval

#### About this task

When the number of LSAs in the LSDB exceeds
the upper limit, the LSDB is in an overflow state. In this state, OSPF does not
receive any external LSAs and deletes the external LSAs generated by itself to
save system resources.

This task allows you to configure the
interval that OSPF exits overflow state.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter OSPF view.

ospf \[ process-id \| router-id router-id \| vpn-instance
vpn-instance-name
] \*

**3\.**Set the interval that OSPF exits overflow
state.

lsdb-overflow-interval interval

By default, the OSPF exit overflow
interval is 300 seconds. An interval of 0 means that OSPF does not exit
overflow state.

## Configuring OSPF packet parameters

### Disabling interfaces from receiving and sending OSPF packets

#### About this task

To enhance OSPF adaptability and reduce
resource consumption, you can set an OSPF interface to "silent." A silent
OSPF interface blocks OSPF packets and cannot establish any OSPF neighbor
relationship. However, other interfaces on the router can still advertise
direct routes of the interface in Router LSAs.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter OSPF view.

ospf \[ process-id \| router-id router-id \| vpn-instance
vpn-instance-name
] \*

**3\.**Disable interfaces from receiving and
sending OSPF packets.

silent-interface { interface-type interface-number \| all }

By default, an OSPF interface can receive
and send OSPF packets.

This command disables only the interfaces
associated with the current process rather than other processes. Multiple OSPF processes
can disable the same interface from receiving and sending OSPF packets.

### Adding the interface MTU into DD packets

#### About this task

By default, an OSPF interface adds a value
of 0 into the interface MTU field of a DD packet rather than the actual
interface MTU. On receipt of a DD packet, the interface does not check the interface
MTU field of that packet. Such a mechanism ensures that two interfaces in
different devices can establish a neighbor relationship regardless of the interface
MTU. However, the device cannot establish neighbor relationships with the
devices from another manufacturer that discard the DD packets whose interface
MTU value is 0\.

To resolve this issue, perform this task to
enable an OSPF interface to add its interface MTU into DD packets and check the
interface MTU in received DD packets. If the interface MTU in a received DD
packet is higher than the MTU of the local interface, the interface will
discard the DD packet. To ensure that two interfaces in different devices can
establish a neighbor relationship, set the same MTU for these interfaces.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter interface view.

interface interface-type
interface-number

**3\.**Enable the interface to add its MTU into DD
packets.

ospf mtu-enable

By default, the interface adds an MTU
value of 0 into DD packets. 

### Setting the DSCP value for outgoing OSPF packets

#### About this task

The DSCP value specifies the precedence of
outgoing packets.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter OSPF view.

ospf \[ process-id \| router-id router-id \| vpn-instance
vpn-instance-name
] \*

**3\.**Set the DSCP value for outgoing OSPF
packets.

dscp dscp-value

By default, the DSCP value for outgoing
OSPF packets is 48\.

### Setting the maximum length of OSPF packets that can be sent by an interface

#### About this task

This task allows you to limit the length of
OSPF packets sent over an interface. In some scenarios, for example, when you
establish OSPF neighbors over a tunnel, you can perform this task to prevent
OSPF packet fragmentation on the outgoing tunnel interface. Make sure the
maximum length of the OSPF packets plus the encapsulated header length is no
greater than the outgoing tunnel interface's MTU. For more information about
tunnels, see Layer 3—IP Services Configuration Guide.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter interface view.

interface interface-type
interface-number

**3\.**Set the maximum length of OSPF packets that
can be sent by an interface.

ospf packet-size value

By default, the maximum length of OSPF
packets that an interface can send equals the interface's MTU.

### Setting the LSU transmit rate

#### About this task

During LSDB synchronization, if the local
router has multiple neighbors, it must send many LSUs to each neighbor. When a
neighbor receives excessive LSUs within a short time period, the following
events might occur:

·     The neighbor experiences degraded performance
because it uses too many system resources to process the received LSU packets.

·     The neighbor drops hello packets used for
maintaining the neighbor relationship because it is busy dealing with the LSUs.
As a result, the neighbor relationship is torn down. To reestablish a
relationship to the neighbor, the local router must send more LSUs to the
neighbor. This exacerbates the performance degradation.

This task allows you to limit the LSU
transmit rate by setting the LSU transmit interval and the maximum number of
LSUs that can be sent at each interval.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enable OSPF to limit the LSU transmit rate.

ospf lsu-flood-control \[ interval count ]

By default, OSPF does not limit the LSU
transmit rate.

Inappropriate use of this command might
cause abnormal routing. As a best practice, execute this command with the default
values.

**3\.**Enter OSPF view.

ospf \[ process-id \| router-id router-id \| vpn-instance
vpn-instance-name
] \*

**4\.**(Optional.) Set the LSU transmit interval
and the maximum number of LSUs that can be sent at each interval.

transmit-pacing interval interval count count

By default, an OSPF interface sends a
maximum of three LSU packets every 20 milliseconds.

## Controlling LSA generation, advertisement, and reception

### Setting the maximum number of external LSAs in LSDB

**1\.**Enter system view.

system-view

**2\.**Enter OSPF view.

ospf \[ process-id \| router-id router-id \| vpn-instance
vpn-instance-name
] \*

**3\.**Set the maximum number of external LSAs in
the LSDB.

lsdb-overflow-limit number

By default, the maximum number of
external LSAs in the LSDB is not limited.

### Filtering outbound LSAs on an interface

#### About this task

To reduce the LSDB size for the neighbor
and save bandwidth, you can perform this task on an interface to filter LSAs to
be sent to the neighbor.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter interface view.

interface interface-type
interface-number

**3\.**Filter outbound LSAs on the interface.

ospf database-filter { all \| { ase \[ acl ipv4-acl-number
] \| nssa \[ acl ipv4-acl-number ] \| summary \[ acl ipv4-acl-number ] } \* }

By default, the outbound LSAs are not
filtered on the interface.

### Filtering LSAs for the specified neighbor

#### About this task

On a P2MP network, a router might have
multiple P2MP type OSPF neighbors. Perform this task to prevent the router from
sending LSAs to the specified P2MP neighbor.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter OSPF view.

ospf \[ process-id \| router-id router-id \| vpn-instance
vpn-instance-name
] \*

**3\.**Filter LSAs for the specified P2MP neighbor.

database-filter peer ip-address { all \| { ase \[ acl ipv4-acl-number ] \| nssa \[ acl ipv4-acl-number ] \| summary \[ acl ipv4-acl-number ] } \* }

By default, the LSAs for the specified
P2MP neighbor are not filtered.

## Accelerating OSPF convergence speed

### Enabling OSPF ISPF

#### About this task

When the topology changes, Incremental
Shortest Path First (ISPF) computes only the affected part of the SPT, instead
of the entire SPT.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter OSPF view.

ospf \[ process-id \| router-id router-id \| vpn-instance
vpn-instance-name
] \*

**3\.**Enable OSPF ISPF.

ispf enable

By default, OSPF ISPF is enabled.

### Configuring prefix suppression

#### About this task

By default, an OSPF interface advertises
all of its prefixes in LSAs. To speed up OSPF convergence, you can suppress
interfaces from advertising all of their prefixes. This feature helps improve
network security by preventing IP routing to the suppressed networks.

When prefix suppression is enabled:

·     On P2P and P2MP networks, OSPF does not
advertise Type-3 links in Type-1 LSAs. Other routing information can still be
advertised to ensure traffic forwarding.

·     On broadcast and NBMA networks, the DR generates
Type-2 LSAs with a mask length of 32 to suppress network routes. Other routing
information can still be advertised to ensure traffic forwarding. If no
neighbors exist, the DR does not advertise Type-3 links in Type-1 LSAs.

#### Restrictions and guidelines for prefix suppression

As a best practice, configure prefix
suppression on all OSPF routers if you want to use prefix suppression.

#### Configuring prefix suppression for an OSPF process

**1\.**Enter system view.

system-view

**2\.**Enter OSPF view.

ospf \[ process-id \| router-id router-id \| vpn-instance
vpn-instance-name
] \*

**3\.**Enable prefix suppression for the OSPF
process.

prefix-suppression

By default, prefix suppression is disabled
for an OSPF process.

This feature does not suppress the
prefixes of secondary IP addresses, loopback interfaces, and passive
interfaces.

#### Configuring prefix suppression for an interface

**1\.**Enter system view.

system-view

**2\.**Enter interface view.

interface interface-type
interface-number

**3\.**Enable prefix suppression for the interface.

ospf prefix-suppression
\[ disable ]

By default, prefix suppression is disabled
on an interface.

This feature does not suppress prefixes
of secondary IP addresses.

### Configuring prefix prioritization

#### About this task

OSPF calculates intra-area routes,
inter-area routes, and AS external routes in descending convergence priority
order: critical, high, medium, and low. When a route has multiple prefix
priorities, it uses the highest priority.

To prioritize the convergence of specific
routes, perform this task to set a higher convergence priority value for those
routes.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Configure an IPv4 prefix list.

ip prefix-list prefix-list-name \[ index index-number ] { deny \| permit } ip-address
mask-length \[ greater-equal min-mask-length ] \[ less-equal max-mask-length ]

**3\.**Configure a routing policy.

route-policy route-policy-name { deny \| permit } node node-number

**4\.**Use the IPv4 prefix list as an IPv4 route
filter.

if-match ip { address \| next-hop \| route-source } prefix-list prefix-list-name

By default, no IPv4 route filters are
configured.

**5\.**Set a prefix priority for matching routes.

apply prefix-priority { critical \| high \| medium }

By default, no prefix priority is set.

**6\.**Return to system view.

quit

**7\.**Enter OSPF view.

ospf \[ process-id \| router-id router-id \| vpn-instance
vpn-instance-name
] \*

**8\.**Enable prefix prioritization.

prefix-priority route-policy route-policy-name

By default, prefix prioritization is
disabled.

### Configuring OSPF PIC

#### About this task

Prefix Independent Convergence (PIC)
enables the device to speed up network convergence by ignoring the number of
prefixes.

#### Restrictions and guidelines for OSPF PIC

When both OSPF PIC and OSPF FRR are
configured, OSPF FRR takes effect.

OSPF PIC applies only to inter-area routes
and external routes.

#### Enabling OSPF PIC

**1\.**Enter system view.

system-view

**2\.**Enter OSPF view.

ospf \[ process-id \| router-id router-id \| vpn-instance
vpn-instance-name
] \*

**3\.**Enable PIC for OSPF.

pic \[ additional-path-always ]

By default, OSPF PIC is enabled.

#### Configuring BFD control packet mode for OSPF PIC

**1\.**Enter system view.

system-view

**2\.**Enter interface view.

interface interface-type
interface-number

**3\.**Enable BFD control packet mode for OSPF PIC.

ospf primary-path-detect bfd ctrl

By default, BFD control packet mode is
disabled for OSPF PIC.

This mode requires BFD configuration on
both OSPF routers on the link.

#### Configuring BFD echo packet mode for OSPF PIC

**1\.**Enter system view.

system-view

**2\.**Configure the source IP address of BFD echo
packets.

bfd echo-source-ip ip-address

By default, the source IP address of BFD echo
packets is not configured.

The source IP address cannot be on the
same network segment as any local interfaces.

For more information about this command,
see High Availability Command Reference.

**3\.**Enter interface view.

interface interface-type
interface-number

**4\.**Enable BFD echo packet mode for OSPF PIC.

ospf primary-path-detect bfd echo

By default, BFD echo packet mode is
disabled for OSPF PIC.

This mode requires BFD configuration on
one OSPF router on the link.

## Configuring advanced OSPF features

### Configuring stub routers

#### About this task

A stub router is used for traffic control. It
reports its status as a stub router to neighboring OSPF routers. The neighboring
routers can have a route to the stub router, but they do not use the stub
router to forward data.

Router LSAs from the stub router might
contain different link type values. A value of 3 means a link to a stub
network, and the cost of the link will not be changed by default. To set the
cost of the link to 65535, specify the include-stub
keyword in the stub-router command. A
value of 1, 2 or 4 means a point-to-point link, a link to a transit network, or
a virtual link. On such links, a maximum cost value of 65535 is used. Neighbors
do not send packets to the stub router as long as they have a route with a
smaller cost.

As shown in [Figure 11](#_Ref101874140), Device
A, Device B, Device C, and Device D run OSPF. Device C is the backup device for
Device B. Device A and Device B, Device A and Device C, Device B and Device D,
and Device C and Device D are IBGP peers.

When the network is stable, BGP and OSPF
can finish route convergence completely. Device A forwards traffic destined for
10.3.1.0/30 to Device B. If Device B fails, Device C will take over to forward
the traffic from Device A.

After Device B recovers, Device A immediately
forwards the traffic Device B. However, BGP might have not finished route
convergence when OSPF has finished route convergence, because OSPF has a higher
route convergence speed than BGP. In this situation, Device B does not have a
route to 10.3.1.0/30 and will discard the traffic destined for 10.3.1.0/30. To
resolve this issue, configure Device B to act as a stub router during BGP route
convergence. Device A will forward traffic to Device C instead of Device B until
Device B finishes BGP route convergence.

Figure 11 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11704757_x_Img_x_png_10_2216018_294551_0.png)

‌

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter OSPF view.

ospf \[ process-id \| router-id router-id \| vpn-instance
vpn-instance-name
] \*

**3\.**Configure the router as a stub router.

stub-router \[ external-lsa \[ max-metric-value ] \| include-stub \| on-startup { seconds \| wait-for-bgp
\[ seconds ] } \| summary-lsa \[
max-metric-value ] ] \*

By default, the router is not configured
as a stub router.

A stub router is not related to a stub
area.

### Configuring OSPF isolation

#### About this task

Isolation is a method used for network
device maintenance. It gracefully removes a device from the packet forwarding
path for maintenance and gracefully adds the device to the network after
maintenance.

To reduce impact on traffic forwarding, you
can isolate a device before upgrading it. OSPF isolation works as follows:

**1\.**After OSPF isolation is enabled for a
device, OSPF increases the link cost in LSAs advertised by the device based on
the following rules:

¡     The
link cost in Type-1 LSAs (Router LSAs) is increased to 65535\.

¡     The
link cost in the following LSAs is increased to 16711680:

\-     Type-3 LSAs (Network summary LSAs).

\-     Type-5 LSAs (AS external LSAs).

\-     Type-7 LSAs (NSSA external LSAs).

**2\.**Each neighbor of the device reselects an
optimal route based on the LSAs and stops forwarding traffic to the device. The
device is fully isolated from the network and you can upgrade the device.

**3\.**After the maintenance, disable OSPF
isolation on the device to restore its link cost and gracefully add it back to
the network.

#### Restrictions and guidelines

Both the isolate enable and stub-router external-lsa 16711680 summary-lsa 16711680
include-stub commands can isolate the device
from the network.

When you execute both the isolate enable and stub-router commands,
follow these restrictions and guidelines:

·     If you specify the external-lsa and summary-lsa keywords in
the stub-router
command, the higher one of the two link costs
provided by the isolation feature and the stub router feature takes effect.

·     If the on-startup
keyword is specified in the stub-router command,
traffic forwarding path selection is affected only by the isolation feature
when the stub router feature does not take effect.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter OSPF view.

ospf \[ process-id \| router-id router-id \| vpn-instance
vpn-instance-name
] \*

**3\.**Configure OSPF isolation.

¡     Enable
OSPF isolation to gracefully isolate
the device from the network.

isolate enable

¡     Disable OSPF isolation to gracefully add the device back to the
network.

undo isolate enable

By default, OSPF isolation is disabled.

### Configuring OSPF shutdown

#### About this task

For maintenance purposes, you can use this
feature to shut down OSPF processes on the device with small impact on the
network. If you shut down a process, it will perform the following operations:

·     Send 1-way hello packets to its neighbors. 

On receipt of the packets, the neighbors
disconnect from the OSPF process and use the backup path to forward traffic.

·     Stop receiving and sending OSPF packets.

·     Clear its neighbor, LSDB, and OSPF route
information.

After maintenance, you can use the undo shutdown
process command to restart the process for
neighbor relationship re-establishment.

This feature shuts down an OSPF process
while retaining the process-associated settings to facilitate your maintenance.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter OSPF view.

ospf \[ process-id \| router-id router-id \| vpn-instance
vpn-instance-name
] \*

**3\.**Configure OSPF shutdown.

¡     Shut
down an OSPF process to isolate it from the network.

shutdown process

¡     Restart
the OSPF process to add it back to the network.

undo shutdown process

By default, the OSPF process is not shut
down.

### Enabling compatibility with RFC 1583

#### About this task

RFC 1583 specifies a different method than RFC
2328 for selecting the optimal route to a destination in another AS. When
multiple routes are available to the ASBR, OSPF selects the optimal route by
using the following procedure:

**1\.**Selects the route with the highest
preference.

¡     If RFC
2328 is compatible with RFC 1583, all these routes have equal preference.

¡     If
RFC 2328 is not compatible with RFC 1583, the intra-area route in a
non-backbone area is preferred to reduce the burden of the backbone area. The
inter-area route and intra-area route in the backbone area have equal
preference.

**2\.**Selects the route with the lower cost if two
routes have equal preference.

**3\.**Selects the route with the larger originating
area ID if two routes have equal cost.

#### Restrictions and guidelines

To avoid routing loops, set identical RFC
1583-compatibility on all routers in a routing domain.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter OSPF view.

ospf \[ process-id \| router-id router-id \| vpn-instance
vpn-instance-name
] \*

**3\.**Enable compatibility with RFC 1583\.

rfc1583 compatible

By default, compatibility with RFC 1583
is enabled.

## Configuring OSPF GR

### About OSPF GR

GR ensures forwarding continuity when a
routing protocol restarts or an active/standby switchover occurs.

Two routers are required to complete a GR
process. The following are router roles in a GR process:

·     GR restarter—Graceful restarting router. It must have GR capability.

·     GR helper—A neighbor of the GR restarter. It helps the GR restarter to complete
the GR process.

OSPF GR has the following types:

·     IETF GR—Uses Opaque LSAs to implement GR.

·     Non-IETF GR—Uses link local signaling (LLS) to advertise GR capability and uses
out of band synchronization to synchronize the LSDB.

A device can act as a GR restarter and GR
helper at the same time.

### Restrictions and guidelines for OSPF GR

You cannot enable OSPF NSR on a device that
acts as GR restarter.

### Configuring OSPF GR restarter

#### Configuring the IETF OSPF GR restarter

**1\.**Enter system view.

system-view

**2\.**Enable OSPF and enter its view.

ospf \[ process-id \| router-id router-id \| vpn-instance
vpn-instance-name
] \*

**3\.**Enable opaque LSA reception and advertisement
capability.

opaque-capability enable

By default, opaque LSA reception and advertisement
capability is enabled.

**4\.**Enable the IETF GR.

graceful-restart ietf \[ global \| planned-only ] \*

By default, the IETF GR capability is
disabled.

**5\.**(Optional.) Set the GR interval.

graceful-restart interval interval

By default, the GR interval is 120
seconds.

#### Configuring the non-IETF OSPF GR restarter

**1\.**Enter system view.

system-view

**2\.**Enable OSPF and enter its view.

ospf \[ process-id \| router-id router-id \| vpn-instance
vpn-instance-name
] \*

**3\.**Enable the link-local signaling capability.

enable link-local-signaling

By default, the link-local signaling
capability is disabled.

**4\.**Enable the out-of-band re-synchronization
capability.

enable out-of-band-resynchronization

By default, the out-of-band re-synchronization
capability is disabled.

**5\.**Enable non-IETF GR.

graceful-restart \[ nonstandard ] \[ global \| planned-only ] \*

By default, non-IETF GR capability is
disabled.

**6\.**(Optional.) Set the GR interval.

graceful-restart interval interval

By default, the GR interval is 120
seconds.

### Configuring OSPF GR helper

#### Configuring the IETF OSPF GR helper

**1\.**Enter system view.

system-view

**2\.**Enable OSPF and enter its view.

ospf \[ process-id \| router-id router-id \| vpn-instance
vpn-instance-name
] \*

**3\.**Enable opaque LSA reception and
advertisement capability.

opaque\-capability enable

By default, opaque LSA reception and
advertisement capability is enabled.

**4\.**Enable GR helper capability.

graceful-restart helper enable \[ planned-only ]

By default, GR helper capability is
enabled.

**5\.**(Optional.) Enable strict LSA checking for
the GR helper.

graceful-restart helper strict-lsa-checking

By default, strict LSA checking for the
GR helper is disabled.

When an LSA change on the GR helper is
detected, the GR helper device exits the GR helper mode.

#### Configuring the non-IETF OSPF GR helper

**1\.**Enter system view.

system-view

**2\.**Enable OSPF and enter its view.

ospf \[ process-id \| router-id router-id \| vpn-instance
vpn-instance-name
] \*

**3\.**Enable the link-local signaling capability.

enable link-local-signaling

By default, the link-local signaling
capability is disabled.

**4\.**Enable the out-of-band re-synchronization capability.

enable out-of-band-resynchronization

By default, the out-of-band re-synchronization
capability is disabled.

**5\.**Enable GR helper.

graceful-restart helper enable

By default, GR helper is enabled.

**6\.**(Optional.) Enable strict LSA checking for
the GR helper.

graceful-restart helper strict-lsa-checking

By default, strict LSA checking for the
GR helper is disabled.

When an LSA change on the GR helper is
detected, the GR helper device exits the GR helper mode.

### Triggering OSPF GR

#### About this task

You can trigger OSPF GR by performing an
active/standby switchover or using the reset ospf process command.

#### Procedure

To trigger OSPF GR, execute the reset ospf \[ process\-id ] process graceful-restart command in user view.

## Configuring OSPF NSR

#### About this task

Nonstop routing (NSR) backs up OSPF link
state information from the active process to the standby process. After an
active/standby switchover, NSR can complete link state recovery and route
regeneration without tearing down adjacencies or impacting forwarding services.

NSR does not require the cooperation of
neighboring devices to recover routing information, and it is typically used
more often than GR.

#### Restrictions and guidelines

A device that has OSPF NSR enabled cannot
act as GR restarter.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter OSPF view.

ospf \[ process-id \| router-id router-id \| vpn-instance
vpn-instance-name
] \*

**3\.**Enable OSPF NSR.

non-stop-routing

By default, OSPF NSR is disabled.

This command takes effect only for the
current process. As a best practice, enable OSPF NSR for each process if
multiple OSPF processes exist.

## Configuring BFD for OSPF

### About BFD for OSPF

OSPF sends hello packets to neighbors at
specific intervals to detect their state. If OSPF does not receive a hello
packet from a neighbor within a specific period, OSPF considers the neighbor down.
Such a failure detection method cannot achieve high data reliability, because
it takes a long time and causes data loss.

To resolve this issue, configure Bidirectional
Forwarding Detection (BFD) for OSPF. This feature improves the route convergence
speed against link state changes through fast link state detection. For more
information about BFD, see High Availability
Configuration Guide.

OSPF supports the following BFD detection
modes:

·     Bidirectional control
detection—Requires BFD configuration to be made on
both OSPF routers on the link.

·     Single-hop echo detection—Requires BFD configuration to be made on one OSPF router on the
link. 

### Configuring bidirectional control detection

**1\.**Enter system view.

system-view

**2\.**Enter interface view.

interface interface-type
interface-number

**3\.**Enable BFD bidirectional control detection.

ospf bfd enable

By default, BFD bidirectional control
detection is disabled.

Both ends of a BFD session must be on the
same network segment and in the same area.

### Configuring single-hop echo detection

**1\.**Enter system view.

system-view

**2\.**Configure the source address of echo
packets.

bfd echo-source-ip ip-address

By default, the source address of echo
packets is not configured.

The source IP address cannot be on the
same network segment as any local interfaces.

For more information about this command,
see High Availability Command Reference.

**3\.**Enter interface view.

interface interface-type
interface-number

**4\.**Enable BFD single-hop echo detection.

ospf bfd enable echo

By default, BFD single-hop echo detection
is disabled.

## Configuring OSPF FRR

### About OSPF FRR

A link or router failure on a path can
cause packet loss until OSPF completes routing convergence based on the new
network topology. FRR enables fast rerouting to minimize the impact of link or
node failures.

Figure 12 Network diagram for OSPF FRR

![](https://resource.h3c.com/en/202407/12/20240712_11704758_x_Img_x_png_11_2216018_294551_0.png)

‌

As shown in [Figure 12](#_Ref203213258), configure
FRR on Router B by using a routing policy to specify a backup next hop. When the
primary link fails, OSPF directs packets to the backup next hop. At the same
time, OSPF calculates the shortest path based on the new network topology. It
forwards packets over the path after network convergence.

You can configure OSPF FRR to calculate a
backup next hop by using the loop free alternate (LFA) algorithm, or specify a
backup next hop by using a routing policy.

### Restrictions and guidelines for OSPF FRR

When both OSPF PIC and OSPF FRR are
configured, OSPF FRR takes effect.

### Configuring OSPF FRR to use the LFA algorithm to calculate a backup next hop

#### Restrictions and guidelines

Do not use the fast-reroute
lfa command together with the vlink-peer command.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter interface view.

interface interface-type interface-number

**3\.**(Optional.) Enable LFA on an interface.

ospf fast-reroute lfa-backup

By default, the interface is enabled with
LFA and it can be selected as a backup interface.

**4\.**Return to system view.

quit

**5\.**Enter OSPF view.

ospf \[ process-id \| router-id router-id \| vpn-instance
vpn-instance-name
] \*

**6\.**Enable OSPF FRR to use the LFA algorithm to calculate
a backup next hop.

fast-reroute lfa \[ abr-only ]

By default, OSPF FRR is disabled.

If abr-only is
specified, the route to the ABR is selected as the backup path.

### Configuring OSPF FRR to use a backup next hop specified in a routing policy

#### About this task

Before you perform this task, use the apply
fast-reroute backup-interface command to specify
a backup next hop in a routing policy for OSPF FRR. For more information about
the apply
fast-reroute backup-interface command and
routing policy configuration, see "Configuring routing policies."

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter OSPF view.

ospf \[ process-id \| router-id router-id \| vpn-instance
vpn-instance-name
] \*

**3\.**Enable OSPF FRR to use a backup next hop
specified in a routing policy.

fast-reroute route-policy route-policy-name

By default, OSPF FRR is disabled.

### Configuring BFD control packet mode for OSPF FRR

#### About this task

By default, OSPF FRR does not use BFD to
detect primary link failures. To speed up OSPF convergence, enable BFD control
packet mode for OSPF FRR to detect primary link failures. This mode requires BFD
configuration on both OSPF routers on the link.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter interface view.

interface interface-type
interface-number

**3\.**Enable BFD control packet mode for OSPF FRR.

ospf primary-path-detect bfd ctrl

By default, BFD control packet mode is
disabled for OSPF FRR.

### Configuring BFD echo packet mode for OSPF FRR

#### About this task

By default, OSPF FRR does not use BFD to
detect primary link failures. To speed up OSPF convergence, enable BFD echo
packet mode for OSPF FRR to detect primary link failures. This mode requires BFD
configuration on one OSPF router on the link.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Configure the source IP address of BFD echo
packets.

bfd echo-source-ip ip-address

By default, the source IP address of BFD echo
packets is not configured.

The source IP address cannot be on the
same network segment as any local interface's IP address.

For more information about this command,
see High Availability Command Reference.

**3\.**Enter interface view.

interface interface-type
interface-number

**4\.**Enable BFD echo packet mode for OSPF FRR.

ospf primary-path-detect bfd echo

By default, BFD echo packet mode is
disabled for OSPF FRR.

## ConfiguringOSPF authentication

### About OSPF area and interface authentication

Perform this task to configure OSPF area
and interface authentication.

OSPF adds the authentication information
into sent packets, and uses the key to authenticate received packets. Only
packets that pass the authentication can be received. If a packet fails the
authentication, the OSPF neighbor relationship cannot be established.

If you configure OSPF authentication for
both an area and an interface in that area, the interface uses the OSPF
authentication configured on it.

### Restrictions and guidelines for configuring OSPF authentication

OSPF supports the MD5, HMAC-MD5, HMAC-SHA-256,
and HMAC-SM3 authentication algorithms.

The ID of keys used for authentication can
only be in the range of 0 to 255\.

### Configuring OSPF area authentication

**1\.**Enter system view.

system-view

**2\.**Enter OSPF view.

ospf \[ process-id \| router-id router-id \| vpn-instance
vpn-instance-name
] \*

**3\.**Enter area view.

area area-id

**4\.**Configure area authentication mode.

¡     Configure
HMAC-MD5/HMAC-SHA-256/HMAC-SM3/MD5 authentication.

authentication-mode { hmac-md5 \| hmac-sha-256 \| hmac-sm3 \| md5 } \[ key-id { cipher \| plain } string ] 

¡     Configure
simple authentication.

authentication-mode simple \[ { cipher \| plain } string ]

¡     Configure
keychain authentication.

authentication-mode keychain keychain-name

For information about keychains, see Security Configuration Guide.

By default, no authentication is
configured.

All routers in an area must use the same authentication
mode. If a key is configured, all routers must be configured with the same key.

### Configuring OSPF interface authentication

#### About this task

To prevent an interface from authenticating
OSPF packets, configure the None authentication mode on the interface. Then,
the interface will not inherit the authentication configuration of the area.

#### Restrictions and guidelines

The local interface and its peer interface
must use the same authentication mode. If a key is configured, the local and
peer interfaces must be configured with the same key.

If you configure the None authentication
mode on an interface, make sure its peer interface uses the None authentication
mode or is not configured with any authentication modes.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter interface view.

interface interface-type
interface-number

**3\.**Configure interface authentication mode.

¡     Configure
simple authentication.

ospf authentication-mode simple \[ { cipher \| plain } string ]

¡     Configure the None authentication mode.

ospf authentication-mode none

¡     Configure
HMAC-MD5/HMAC-SHA-256/HMAC-SM3/MD5 authentication.

ospf authentication-mode { hmac-md5 \| hmac-sha-256 \| hmac-sm3 \| md5 } \[ key-id { cipher \| plain } string ] 

¡     Configure
keychain authentication.

ospf authentication-mode keychain keychain-name

For information about keychains, see Security Configuration Guide.

By default, no authentication is
configured.

## Configuring GTSM for OSPF

### About GTSM

The Generalized TTL Security Mechanism (GTSM)
protects the device by comparing the TTL value in the IP header of incoming
OSPF packets against a valid TTL range. If the TTL value is within the valid
TTL range, the packet is accepted. If not, the packet is discarded. 

The valid TTL range is from 255 – the
configured hop count \+ 1 to 255\. 

When GTSM is configured, the OSPF packets sent
by the device have a TTL of 255\. 

### Restrictions and guidelines for GTSM

To use GTSM, you must configure GTSM on
both the local and peer devices. You can specify different hop-count values for them.

The GTSM configuration in OSPF area view
applies to all OSPF interfaces in the area. The GTSM configuration in interface
view takes precedence over the configuration in OSPF area view.

GTSM checks OSPF packets from common
neighbors and virtual link neighbors. It does not check OSPF packets from sham
link neighbors. For information about GTSM for OSPF sham links, see MPLS Configuration Guide. 

### Configuring GTSM in OSPF area view

**1\.**Enter system view.

system-view

**2\.**Enter OSPF view. 

ospf \[ process-id \| router-id router-id \| vpn-instance
vpn-instance-name
] \*

**3\.**Enter OSPF area view. 

area area-id

**4\.**Enable GTSM for the OSPF area.

ttl-security \[ hops hop-count ]

By default, GTSM is disabled for the OSPF
area.

### Configuring GTSM in interface view

**1\.**Enter system view.

system-view

**2\.**Enter interface view. 

interface interface-type
interface-number

**3\.**Configure GTSM for the interface.

¡     Enable
GTSM for the interface.

ospf ttl-security \[ hops hop-count ]

¡     Disable
GTSM for the interface.

ospf ttl-security disable

Disable GTSM for an interface when the
following conditions exist:

\-     The area to which the interface belongs is enabled with GTSM.

\-     The neighbor of the interface does not
support GTSM.

By default, an interface uses the GTSM
configuration of the area to which the interface belongs.

## Configuring OSPF logging, SNMP notifications, and troubleshooting

### Logging neighbor state changes

#### About this task

Perform this task to enable output of
neighbor state change logs to the information center. The information center
processes the logs according to user-defined output rules (whether and where to
output logs). For more information about the information center, see Network Management and Monitoring Configuration Guide.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter OSPF view.

ospf \[ process-id \| router-id router-id \| vpn-instance
vpn-instance-name
] \*

**3\.**Enable the logging of neighbor state
changes.

log-peer-change

By default, this feature of logging
neighbor state changes is enabled.

### Configuring the OSPF logging feature

#### About this task

OSPF logs include LSA aging logs, route
calculation logs, neighbor logs, OSPF route logs, and self-originated and
received LSA logs.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter OSPF view.

ospf \[ process-id \| router-id router-id \| vpn-instance
vpn-instance-name
] \*

**3\.**Set the number of OSPF logs.

event-log { hello { received \[ abnormal \| dropped ] \| sent \[ abnormal \| failed ] } \| lsa-flush \| peer \| spf } size count

By default, the device can generate a
maximum of 100 OSPF logs for each type.

### Configuring OSPF network management

#### About this task

This task involves the following
configurations:

·     Bind an OSPF process to MIB so that you can use
network management software to manage the specified OSPF process.

·     Enable SNMP notifications for OSPF to report
important events.

·     Configure the SNMP notification output interval
and the maximum number of SNMP notifications that can be output at each
interval.

To report critical OSPF events to an NMS,
enable SNMP notifications for OSPF. For SNMP notifications to be sent
correctly, you must also configure SNMP on the device. For more information
about SNMP configuration, see the network management and monitoring
configuration guide for the device.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Bind MIB to an OSPF process.

ospf mib-binding process-id

By default, MIB is bound to the process with
the smallest process ID.

**3\.**Enable SNMP notifications for OSPF.

snmp-agent trap enable ospf \[ authentication-failure \| bad-packet \| config-error \| grhelper-status-change \| grrestarter-status-change \| if-state-change \| lsa-maxage \| lsa-originate \|
lsdb-approaching-overflow \| lsdb-overflow \| neighbor-state-change \| nssatranslator-status-change \| retransmit \| virt-authentication-failure \| virt-bad-packet \| virt-config-error \| virt-retransmit \| virtgrhelper-status-change \| virtif-state-change \| virtneighbor-state-change ] \*

By default, SNMP notifications for OSPF
are enabled.

**4\.**Enter OSPF view.

ospf \[ process-id \| router-id router-id \| vpn-instance
vpn-instance-name
] \*

**5\.**Configure the SNMP notification output
interval and the maximum number of SNMP notifications that can be output at
each interval.

snmp trap rate-limit interval trap-interval count trap-number

By default, OSPF outputs a maximum of
seven SNMP notifications within 10 seconds.

### Setting the maximum number of OSPF neighbor relationship troubleshooting entries

**1\.**Enter system view.

system-view

**2\.**Set the maximum number of OSPF neighbor
relationship troubleshooting entries.

ospf troubleshooting max-number number

By default, OSPF can record a maximum of
100 neighbor relationship troubleshooting entries.

## Display andmaintenance commands for OSPF

Execute display
commands in any view and reset commands in user
view.

 

| Task | Command |
| --- | --- |
| Display summary route information on the OSPF ABR. | display ospf \[ process-id ] \[ area area-id ] abr-summary \[ ip-address { mask-length \| mask } ] \[ verbose ] || Display OSPF FRR backup next hop information. | display ospf \[ process-id ] \[ area area-id ] fast-reroute lfa-candidate || Display OSPF topology information. | display ospf \[ process-id ] \[ area area-id ] spf-tree \[ verbose ] || Display OSPF process information. | display ospf \[ process-id ] \[ verbose ] || Display OSPF ABR and ASBR information. | display ospf \[ process-id ] abr-asbr \[ verbose ] || Display OSPF ASBR route summarization information. | display ospf \[ process-id ] asbr-summary \[ ip-address { mask-length \| mask } ] || Display OSPF log information. | display ospf \[ process-id ] event-log { lsa-flush \| peer \[ neighbor-id ] \[ slot slot-number ] \| spf } || Display OSPF log information about received or sent hello packets. | display ospf \[ process-id ] event-log hello { received \[ abnormal \| dropped ] \| sent } \[ neighbor-id ] \[ slot slot-number ]  display ospf \[ process-id ] event-log hello sent { abnormal \| failed } \[ neighbor-address ] \[ slot slot-number ] || Display OSPF GR information. | display ospf \[ process-id ] graceful-restart \[ verbose ] || Display OSPF interface information. | display ospf \[ process-id ] interface \[ interface-type interface-number \| verbose ] || Display information about hello packets sent by OSPF interfaces. | display ospf \[ process-id ] interface \[ interface-type interface-number ] hello || Display OSPF LSDB information. | display ospf \[ process-id ] \[ area area-id ] lsdb { asbr \| network \| nssa \| opaque-area \| opaque-link \| router \| summary } \[ link-state-id ] \[ originate-router advertising-router-id \| self-originate ]  display ospf \[ process-id ] lsdb \[ brief \| originate-router advertising-router-id \| self-originate ]  display ospf \[ process-id ] lsdb { ase \| opaque-as } \[ link-state-id ] \[ originate-router advertising-router-id \| self-originate ] || Display OSPF next hop information. | display ospf \[ process-id ] nexthop || Display OSPF NSR information. | display ospf \[ process-id ] non-stop-routing status || Display OSPF neighbor information. | display ospf \[ process-id ] peer \[ hello \| verbose ] \[ interface-type interface-number ] \[ neighbor-id ] || Display neighbor statistics for OSPF areas. | display ospf \[ process-id ] peer statistics || Display OSPF request queue information. | display ospf \[ process-id ] request-queue \[ interface-type interface-number ] \[ neighbor-id ] || Display OSPF retransmission queue information. | display ospf \[ process-id ] retrans-queue \[ interface-type interface-number ] \[ neighbor-id ] || Display OSPF routing table information. | display ospf \[ process-id ] routing \[ ip-address { mask-length \| mask } ] \[ interface interface-type interface-number ] \[ nexthop nexthop-address ] \[ verbose ] || Display OSPF statistics. | display ospf \[ process-id ] statistics \[ error \| packet \[ hello \| interface-type interface-number ] ] || Display OSPF virtual link information. | display ospf \[ process-id ] vlink || Display the global route ID. | display router id || Display OSPF neighbor relationship troubleshooting information. | display ospf troubleshooting || Clear OSPF log information. | reset ospf \[ process-id ] event-log \[ lsa-flush \| peer \| spf ] || Clear OSPF log information about received or sent hello packets. | reset ospf \[ process-id ] event-log hello { received \[ abnormal \| dropped ] \| sent \[ abnormal \| failed ] } \[ slot slot-number ] || Restart an OSPF process. | reset ospf \[ process-id ] process \[ graceful-restart ] || Re-enable OSPF route redistribution. | reset ospf \[ process-id ] redistribution || Clear OSPF statistics. | reset ospf \[ process-id ] statistics || Clear OSPF neighbor relationship troubleshooting information. | reset ospf troubleshooting |





























‌

## OSPF configuration examples

### Example: Configuring basic OSPF

#### Network configuration

As shown in [Figure 13](#_Ref279690776):

·     Enable OSPF on all switches, and split the AS
into three areas.

·     Configure Switch A and Switch B as ABRs.

Figure 13 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11704759_x_Img_x_png_12_2216018_294551_0.png)

‌

#### Procedure

**1\.**Configure IP addresses for interfaces. (Details
not shown.)

**2\.**Enable OSPF:

\# Configure Switch A.

\<SwitchA\> system-view

\[SwitchA] router id 10.2.1.1

\[SwitchA] ospf

\[SwitchA-ospf-1] area 0

\[SwitchA-ospf-1-area-0.0.0.0] network 10.1.1.0 0.0.0.255

\[SwitchA-ospf-1-area-0.0.0.0] quit

\[SwitchA-ospf-1] area 1

\[SwitchA-ospf-1-area-0.0.0.1] network 10.2.1.0 0.0.0.255

\[SwitchA-ospf-1-area-0.0.0.1] quit

\[SwitchA-ospf-1] quit

\# Configure Switch B.

\<SwitchB\> system-view

\[SwitchB] router id 10.3.1.1

\[SwitchB] ospf

\[SwitchB-ospf-1] area 0

\[SwitchB-ospf-1-area-0.0.0.0] network 10.1.1.0 0.0.0.255

\[SwitchB-ospf-1-area-0.0.0.0] quit

\[SwitchB-ospf-1] area 2

\[SwitchB-ospf-1-area-0.0.0.2] network 10.3.1.0 0.0.0.255

\[SwitchB-ospf-1-area-0.0.0.2] quit

\[SwitchB-ospf-1] quit

\# Configure Switch C.

\<SwitchC\> system-view

\[SwitchC] router id 10.4.1.1

\[SwitchC] ospf

\[SwitchC-ospf-1] area 1

\[SwitchC-ospf-1-area-0.0.0.1] network 10.2.1.0 0.0.0.255

\[SwitchC-ospf-1-area-0.0.0.1] network 10.4.1.0 0.0.0.255

\[SwitchC-ospf-1-area-0.0.0.1] quit

\[SwitchC-ospf-1] quit

\# Configure Switch D.

\<SwitchD\> system-view

\[SwitchD] router id 10.5.1.1

\[SwitchD] ospf

\[SwitchD-ospf-1] area 2

\[SwitchD-ospf-1-area-0.0.0.2] network 10.3.1.0 0.0.0.255

\[SwitchD-ospf-1-area-0.0.0.2] network 10.5.1.0 0.0.0.255

\[SwitchD-ospf-1-area-0.0.0.2] quit

\[SwitchD-ospf-1] quit

#### Verifying the configuration

\# Display information about neighbors on Switch
A.

\[SwitchA] display ospf peer verbose

 

          OSPF Process 1 with Router
ID 10.2.1.1

                  Neighbors

 

 Area 0.0.0.0 interface 10.1.1.1(Vlan-interface100)'s neighbors

 Router ID: 10.3.1.1         Address: 10.1.1.2         GR State: Normal

   State: Full  Mode: Nbr is master  Priority:
1

   DR: 10.1.1.1  BDR: 10.1.1.2  MTU: 0

   Options is 0x02 (-\|-\|-\|-\|-\|-\|E\|-)

   Dead timer due in 37  sec

   Neighbor is up for 06:03:59

   Authentication Sequence: \[ 0 ]

   Neighbor state change count: 5

   BFD status: Disabled

 

 Area 0.0.0.1 interface 10.2.1.1(Vlan-interface200)'s neighbors

 Router ID: 10.4.1.1         Address: 10.2.1.2         GR State: Normal

   State: Full  Mode: Nbr is master  Priority:
1

   DR: 10.2.1.1  BDR: 10.2.1.2  MTU: 0

   Options is 0x02 (-\|-\|-\|-\|-\|-\|E\|-)

   Dead timer due in 32  sec

   Neighbor is up for 06:03:12

   Authentication Sequence: \[ 0 ]

   Neighbor state change count: 5

   BFD status: Disabled

\# Display OSPF routing information on
Switch A.

\[SwitchA] display ospf routing

 

          OSPF Process 1 with Router
ID 10.2.1.1

                   Routing Table

 

                Topology base (MTID
0\)

 

 Routing for network

 Destination        Cost     Type   
NextHop         AdvRouter       Area

 10.2.1.0/24        1        Transit 10.2.1.1        10.2.1.1        0.0.0.1

 10.3.1.0/24        2        Inter   10.1.1.2        10.3.1.1        0.0.0.0

 10.4.1.0/24        2        Stub    10.2.1.2        10.4.1.1        0.0.0.1

 10.5.1.0/24        3        Inter   10.1.1.2        10.3.1.1        0.0.0.0

 10.1.1.0/24        1        Transit 10.1.1.1        10.2.1.1        0.0.0.0

 

 Total nets: 5

 Intra area: 3  Inter area: 2  ASE:
0  NSSA: 0

\# Display OSPF routing information on Switch
D.

\[SwitchD] display ospf routing

 

          OSPF Process 1 with Router
ID 10.5.1.1

                   Routing Table

 

                Topology base (MTID
0\)

 

 Routing for network

 Destination        Cost     Type   
NextHop         AdvRouter       Area

 10.2.1.0/24        3        Inter   10.3.1.1        10.3.1.1        0.0.0.2

 10.3.1.0/24        1        Transit 10.3.1.2        10.3.1.1        0.0.0.2

 10.4.1.0/24        4        Inter   10.3.1.1        10.3.1.1        0.0.0.2

 10.5.1.0/24        1        Stub    10.5.1.1        10.5.1.1        0.0.0.2

 10.1.1.0/24        2        Inter   10.3.1.1        10.3.1.1        0.0.0.2

 

 Total nets: 5

 Intra area: 2  Inter area: 3  ASE:
0  NSSA: 0

\# On Switch D, ping the IP address 10.4.1.1 to test reachability.

\[SwitchD] ping 10.4.1.1

Ping 10.4.1.1 (10.4.1.1): 56 data
bytes, press CTRL\+C to break

56 bytes from 10.4.1.1: icmp\_seq\=0
ttl\=253 time\=1.549 ms

56 bytes from 10.4.1.1: icmp\_seq\=1
ttl\=253 time\=1.539 ms

56 bytes from 10.4.1.1: icmp\_seq\=2
ttl\=253 time\=0.779 ms

56 bytes from 10.4.1.1: icmp\_seq\=3
ttl\=253 time\=1.702 ms

56 bytes from 10.4.1.1: icmp\_seq\=4
ttl\=253 time\=1.471 ms

 

\--- Ping statistics for 10.4.1.1 \---

5 packet(s) transmitted, 5 packet(s)
received, 0.0% packet loss

round-trip min/avg/max/std-dev \=
0.779/1.408/1.702/0.323 ms

### Example: Configuring OSPF route redistribution

#### Network configuration

As shown in [Figure 14](#_Ref279690873):

·     Enable OSPF on all the switches.

·     Split the AS into three areas.

·     Configure Switch A and Switch B as ABRs.

·     Configure Switch C as an ASBR to redistribute
external routes (static routes).

Figure 14 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11704760_x_Img_x_png_13_2216018_294551_0.png)

‌

#### Procedure

**1\.**Configure IP addresses for interfaces. (Details
not shown.)

**2\.**Enable OSPF (see "[Example: Configuring basic OSPF](#_Ref346981305)").

**3\.**Configure OSPF to redistribute routes:

\# On Switch C, configure a static route
destined for network 3.1.2.0/24.

\<SwitchC\> system-view

\[SwitchC] ip route-static 3.1.2.1 24 10.4.1.2

\# On Switch C, configure OSPF to
redistribute static routes.

\[SwitchC] ospf 1

\[SwitchC-ospf-1] import-route
static

#### Verifying the configuration

\# Display the ABR/ASBR information on Switch
D.

\<SwitchD\> display ospf abr-asbr

 

          OSPF Process 1 with Router
ID 10.5.1.1

                  Routing Table to
ABR and ASBR

 

 

                Topology base (MTID
0\)

 Type        Destination    
Area            Cost  Nexthop         RtType

 Intra       10.3.1.1        0.0.0.2         10    10.3.1.1        ABR

 Inter       10.4.1.1        0.0.0.2         22    10.3.1.1        ASBR

\# Display the OSPF routing table on Switch
D.

\<SwitchD\> display ospf routing

 

          OSPF Process 1 with Router
ID 10.5.1.1

                   Routing Table

 

                Topology base (MTID
0\)

 

 Routing for network

 Destination        Cost     Type   
NextHop         AdvRouter       Area

 10.2.1.0/24        22       Inter   10.3.1.1        10.3.1.1        0.0.0.2

 10.3.1.0/24        10       Transit 10.3.1.2        10.3.1.1        0.0.0.2

 10.4.1.0/24        25       Inter   10.3.1.1        10.3.1.1        0.0.0.2

 10.5.1.0/24        10       Stub    10.5.1.1        10.5.1.1        0.0.0.2

 10.1.1.0/24        12       Inter   10.3.1.1        10.3.1.1        0.0.0.2

 

 Routing for ASEs

 Destination        Cost     Type   
Tag         NextHop         AdvRouter

 3.1.2.0/24        
1        Type2   1           10.3.1.1        10.4.1.1

 

 Total nets: 6

 Intra area: 2  Inter area: 3  ASE:
1  NSSA: 0

### Example: Configuring OSPFroute summarization

#### Network configuration

As shown in [Figure 15](#_Ref279690902):

·     Configure OSPF on Switch A and Switch B in AS
200\.

·     Configure OSPF on Switch C, Switch D, and Switch
E in AS 100\.

·     Configure an EBGP connection between Switch B
and Switch C. Configure Switch B and Switch C to redistribute OSPF routes and
direct routes into BGP and BGP routes into OSPF.

·     Configure Switch B to advertise only summary
route 10.0.0.0/8 to Switch A.

Figure 15 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11704761_x_Img_x_png_14_2216018_294551_0.png)

‌

#### Procedure

**1\.**Configure IP addresses for interfaces. (Details
not shown.)

**2\.**Enable OSPF:

\# Configure Switch A.

\<SwitchA\> system-view

\[SwitchA] router id 11.2.1.2

\[SwitchA] ospf

\[SwitchA-ospf-1] area 0

\[SwitchA-ospf-1-area-0.0.0.0] network 11.2.1.0 0.0.0.255

\[SwitchA-ospf-1-area-0.0.0.0] quit

\[SwitchA-ospf-1] quit

\# Configure Switch B.

\<SwitchB\> system-view

\[SwitchB] router id 11.2.1.1

\[SwitchB] ospf

\[SwitchB-ospf-1] area 0

\[SwitchB-ospf-1-area-0.0.0.0] network 11.2.1.0 0.0.0.255

\[SwitchB-ospf-1-area-0.0.0.0] quit

\[SwitchB-ospf-1] quit

\# Configure Switch C.

\<SwitchC\> system-view

\[SwitchC] router id 11.1.1.2

\[SwitchC] ospf

\[SwitchC-ospf-1] area 0

\[SwitchC-ospf-1-area-0.0.0.0] network 10.1.1.0 0.0.0.255

\[SwitchC-ospf-1-area-0.0.0.0] network 10.2.1.0 0.0.0.255

\[SwitchC-ospf-1-area-0.0.0.0] quit

\[SwitchC-ospf-1] quit

\# Configure Switch D.

\<SwitchD\> system-view

\[SwitchD] router id 10.3.1.1

\[SwitchD] ospf

\[SwitchD-ospf-1] area 0

\[SwitchD-ospf-1-area-0.0.0.0] network 10.1.1.0 0.0.0.255

\[SwitchD-ospf-1-area-0.0.0.0] network 10.3.1.0 0.0.0.255

\[SwitchD-ospf-1-area-0.0.0.0] quit

\[SwitchD-ospf-1] quit

\# Configure Switch E.

\<SwitchE\> system-view

\[SwitchE] router id 10.4.1.1

\[SwitchE] ospf

\[SwitchE-ospf-1] area 0

\[SwitchE-ospf-1-area-0.0.0.0] network 10.2.1.0 0.0.0.255

\[SwitchE-ospf-1-area-0.0.0.0] network 10.4.1.0 0.0.0.255

\[SwitchE-ospf-1-area-0.0.0.0] quit

\[SwitchE-ospf-1] quit

**3\.**Configure BGP to redistribute OSPF routes
and direct routes:

\# Configure Switch B.

\[SwitchB] bgp 200

\[SwitchB-bgp-default] peer 11.1.1.2 as 100

\[SwitchB-bgp-default] address-family
ipv4 unicast

\[SwitchB-bgp-default-ipv4]
peer 11.1.1.2 enable

\[SwitchB-bgp-default-ipv4]
import-route ospf

\[SwitchB-bgp-default-ipv4] import-route
direct

\[SwitchB-bgp-default-ipv4]
quit

\[SwitchB-bgp-default] quit

\# Configure Switch C.

\[SwitchC] bgp 100

\[SwitchC-bgp-default] peer 11.1.1.1 as 200

\[SwitchC-bgp-default] address-family
ipv4 unicast

\[SwitchC-bgp-default] peer
11.1.1.1 enable

\[SwitchC-bgp-default-ipv4] import-route
ospf

\[SwitchC-bgp-default-ipv4]import-route
direct

\[SwitchC-bgp-default-ipv4] quit

\[SwitchC-bgp-default] quit

**4\.**Configure Switch B and Switch C to
redistribute BGP routes into OSPF:

\# Configure OSPF to redistribute routes
from BGP on Switch B.

\[SwitchB] ospf

\[SwitchB-ospf-1] import-route
bgp

\# Configure OSPF to redistribute routes
from BGP on Switch C.

\[SwitchC] ospf

\[SwitchC-ospf-1] import-route
bgp

\# Display the OSPF routing table on Switch
A.

\[SwitchA] display ip routing-table

 

         Destinations : 16       
Routes : 16

 

Destination/Mask    Proto 
Pre  Cost         NextHop         Interface

 

0.0.0.0/32          Direct
0    0            127.0.0.1       InLoop0

10.1.1.0/24        
O\_ASE2 150  1            11.2.1.1       
Vlan100

10.2.1.0/24        
O\_ASE2 150  1            11.2.1.1        Vlan100

10.3.1.0/24         O\_ASE2 150  1            11.2.1.1        Vlan100

10.4.1.0/24         O\_ASE2 150  1            11.2.1.1        Vlan100

11.2.1.0/24         Direct 0   
0            11.2.1.2        Vlan100

11.2.1.0/32         Direct
0    0            11.2.1.2        Vlan100

11.2.1.2/32         Direct 0   
0            127.0.0.1       InLoop0

11.2.1.255/32       Direct
0    0            11.2.1.2        Vlan100

127.0.0.0/8         Direct
0    0            127.0.0.1       InLoop0

127.0.0.0/32        Direct
0    0            127.0.0.1       InLoop0

127.0.0.1/32        Direct
0    0            127.0.0.1       InLoop0

127.255.255.255/32  Direct
0    0            127.0.0.1       InLoop0

224.0.0.0/4         Direct
0    0            0.0.0.0         NULL0

224.0.0.0/24        Direct
0    0            0.0.0.0         NULL0

255.255.255.255/32  Direct
0    0            127.0.0.1       InLoop0

**5\.**Configure route summarization:

\# Configure route summarization on Switch
B to advertise a summary route 10.0.0.0/8.

\[SwitchB-ospf-1] asbr-summary 10.0.0.0 8

\# Display the IP routing table on Switch
A.

\[SwitchA] display ip routing-table

 

         Destinations : 13       
Routes : 13

 

Destination/Mask    Proto 
Pre  Cost         NextHop         Interface

0.0.0.0/32          Direct
0    0            127.0.0.1       InLoop0

10.0.0.0/8         
O\_ASE2 150  1            11.2.1.1        Vlan100

11.2.1.0/24         Direct 0   
0            11.2.1.2        Vlan100

11.2.1.0/32         Direct
0    0            11.2.1.2        Vlan100

11.2.1.2/32         Direct 0   
0            127.0.0.1       InLoop0

11.2.1.255/32       Direct
0    0            11.2.1.2        Vlan100

127.0.0.0/8         Direct
0    0            127.0.0.1       InLoop0

127.0.0.0/32        Direct
0    0            127.0.0.1       InLoop0

127.0.0.1/32        Direct
0    0            127.0.0.1       InLoop0

127.255.255.255/32  Direct
0    0            127.0.0.1       InLoop0

224.0.0.0/4         Direct
0    0            0.0.0.0         NULL0

224.0.0.0/24        Direct
0    0            0.0.0.0         NULL0

255.255.255.255/32  Direct
0    0            127.0.0.1       InLoop0

The output shows that routes10.1.1.0/24, 10.2.1.0/24, 10.3.1.0/24 and 10.4.1.0/24
are summarized into a single route 10.0.0.0/8.

### Example: Configuring OSPF stub area

#### Network configuration

As shown in [Figure 16](#_Ref279690920):

·     Enable OSPF on all switches, and split the AS
into three areas.

·     Configure Switch A and Switch B as ABRs to
forward routing information between areas.

·     Configure Switch D as the ASBR to redistribute
static routes.

·     Configure Area 1 as a stub area to reduce
advertised LSAs without influencing reachability.

Figure 16 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11704762_x_Img_x_png_15_2216018_294551_0.png)

‌

#### Procedure

**1\.**Configure IP addresses for interfaces. (Details
not shown.)

**2\.**Enable OSPF (see "[Example: Configuring basic OSPF](#_Ref445194915)").

**3\.**Configure route redistribution:

\# Configure Switch D to redistribute
static routes.

\<SwitchD\> system-view

\[SwitchD] ip route-static 3.1.2.1 24 10.5.1.2

\[SwitchD] ospf

\[SwitchD-ospf-1] import-route
static

\[SwitchD-ospf-1] quit

\# Display ABR/ASBR information on Switch C.

\<SwitchC\> display ospf
abr-asbr

 

          OSPF Process 1 with
Router ID 10.4.1.1

                  Routing
Table to ABR and ASBR

 

                Topology base
(MTID 0\)

 Type        Destination    
Area            Cost  Nexthop         RtType

 Intra       10.2.1.1        0.0.0.1         3     10.2.1.1        ABR

 Inter       10.5.1.1        0.0.0.1         7     10.2.1.1        ASBR

\# Display OSPF routing table on Switch C.

\<SwitchC\> display ospf
routing

 

          OSPF Process 1 with
Router ID 10.4.1.1

                   Routing
Table

 

                Topology base
(MTID 0\)

 

 Routing for network

 Destination        Cost    
Type    NextHop         AdvRouter       Area

 10.2.1.0/24        3        Transit 0.0.0.0         10.2.1.1        0.0.0.1

 10.3.1.0/24        7        Inter   10.2.1.1        10.2.1.1        0.0.0.1

 10.4.1.0/24        3        Stub    10.4.1.1        10.4.1.1        0.0.0.1

 10.5.1.0/24        17       Inter   10.2.1.1        10.2.1.1        0.0.0.1

 10.1.1.0/24        5        Inter   10.2.1.1        10.2.1.1        0.0.0.1

 

 Routing for ASEs

 Destination        Cost    
Type    Tag         NextHop         AdvRouter

 3.1.2.0/24         1       
Type2   1           10.2.1.1        10.5.1.1

 

 Total nets: 6

 Intra area: 2  Inter area: 3 
ASE: 1  NSSA: 0

The output shows that Switch C's routing
table contains an AS external route.

**4\.**Configure Area 1 as a stub area:

\# Configure Switch A.

\<SwitchA\> system-view

\[SwitchA] ospf

\[SwitchA-ospf-1] area 1

\[SwitchA-ospf-1-area-0.0.0.1] stub

\[SwitchA-ospf-1-area-0.0.0.1] quit

\[SwitchA-ospf-1] quit

\# Configure Switch C.

\<SwitchC\> system-view

\[SwitchC] ospf

\[SwitchC-ospf-1] area 1

\[SwitchC-ospf-1-area-0.0.0.1] stub

\[SwitchC-ospf-1-area-0.0.0.1] quit

\[SwitchC-ospf-1] quit

\# Display OSPF routing information on Switch
C

\[SwitchC] display ospf routing

 

          OSPF Process 1 with
Router ID 10.4.1.1

                   Routing
Table

 

                Topology base
(MTID 0\)

 

 Routing for network

 Destination        Cost    
Type    NextHop         AdvRouter       Area

 0.0.0.0/0          4       
Inter   10.2.1.1        10.2.1.1        0.0.0.1

 10.2.1.0/24        3        Transit 0.0.0.0         10.2.1.1        0.0.0.1

 10.3.1.0/24        7        Inter   10.2.1.1        10.2.1.1        0.0.0.1

 10.4.1.0/24        3        Stub    10.4.1.1        10.4.1.1        0.0.0.1

 10.5.1.0/24        17       Inter   10.2.1.1        10.2.1.1        0.0.0.1

 10.1.1.0/24        5        Inter   10.2.1.1        10.2.1.1        0.0.0.1

 

 Total nets: 6

 Intra area: 2  Inter area: 4 
ASE: 0  NSSA: 0

The output shows that a default route replaces
the AS external route.

\# Configure Area 1 as a totally stub area.

\[SwitchA] ospf

\[SwitchA-ospf-1] area 1

\[SwitchA-ospf-1-area-0.0.0.1] stub no-summary

\[SwitchA-ospf-1-area-0.0.0.1] quit

\[SwitchA-ospf-1] quit

\# Display OSPF routing information on Switch
C.

\[SwitchC] display ospf routing

 

          OSPF Process 1 with
Router ID 10.4.1.1

                   Routing
Table

 

                Topology base
(MTID 0\)

 

 Routing for network

 Destination        Cost    
Type    NextHop         AdvRouter       Area

 0.0.0.0/0          4        Inter   10.2.1.1        10.2.1.1        0.0.0.1

 10.2.1.0/24        3        Transit 0.0.0.0         10.4.1.1        0.0.0.1

 10.4.1.0/24        3        Stub    10.4.1.1        10.4.1.1        0.0.0.1

 

 Total nets: 3

 Intra area: 2  Inter area: 1 
ASE: 0  NSSA: 0

The output shows that inter-area routes
are removed, and only one external route (a default route) exists on Switch C.

### Example: Configuring OSPF NSSA area

#### Network configuration

As shown in [Figure 17](#_Ref279690953):

·     Configure OSPF on all switches and split AS into
three areas.

·     Configure Switch A and Switch B as ABRs to
forward routing information between areas.

·     Configure Area 1 as an NSSA area and configure
Switch C as an ASBR to redistribute static routes into the AS.

Figure 17 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11704763_x_Img_x_png_16_2216018_294551_0.png)

‌

#### Procedure

**1\.**Configure IP addresses for interfaces.

**2\.**Enable OSPF (see "[Example: Configuring basic OSPF](#_Ref445194915)").

**3\.**Configure Area 1 as an NSSA area:

\# Configure Switch A.

\<SwitchA\> system-view

\[SwitchA] ospf

\[SwitchA-ospf-1] area 1

\[SwitchA-ospf-1-area-0.0.0.1] nssa

\[SwitchA-ospf-1-area-0.0.0.1] quit

\[SwitchA-ospf-1] quit

\# Configure Switch C.

\<SwitchC\> system-view

\[SwitchC] ospf

\[SwitchC-ospf-1] area 1

\[SwitchC-ospf-1-area-0.0.0.1] nssa

\[SwitchC-ospf-1-area-0.0.0.1] quit

\[SwitchC-ospf-1] quit

\# Display OSPF routing information on Switch
C.

\[SwitchC] display ospf routing

 

          OSPF Process 1 with
Router ID 10.4.1.1

                   Routing
Table

 

                Topology base
(MTID 0\)

 

 Routing for network

 Destination        Cost    
Type    NextHop         AdvRouter       Area

 10.2.1.0/24        3       
Transit 10.2.1.2        10.4.1.1        0.0.0.1

 10.3.1.0/24        7       
Inter   10.2.1.1        10.2.1.1        0.0.0.1

 10.4.1.0/24        3       
Stub    10.4.1.1        10.4.1.1        0.0.0.1

 10.5.1.0/24        17      
Inter   10.2.1.1        10.2.1.1        0.0.0.1

 10.1.1.0/24        5       
Inter   10.2.1.1        10.2.1.1        0.0.0.1

 

 Total nets: 5

 Intra area: 2  Inter area: 3 
ASE: 0  NSSA: 0

**4\.**Configure route redistribution:

\# Configure Switch C to redistribute
static routes.

\[SwitchC] ip route-static 3.1.3.1 24 10.4.1.2

\[SwitchC] ospf

\[SwitchC-ospf-1] import-route
static

\[SwitchC-ospf-1] quit

\# Display OSPF routing information on Switch
D.

\<SwitchD\> display ospf
routing

 

                Topology base
(MTID 0\)

 

          OSPF Process 1 with
Router ID 10.5.1.1

                   Routing
Table

 

 Routing for network

 Destination        Cost    
Type    NextHop         AdvRouter       Area

 10.2.1.0/24        22       Inter   10.3.1.1        10.3.1.1        0.0.0.2

 10.3.1.0/24        10       Transit 10.3.1.2        10.3.1.1        0.0.0.2

 10.4.1.0/24        25       Inter   10.3.1.1        10.3.1.1        0.0.0.2

 10.5.1.0/24        10       Stub    10.5.1.1        10.5.1.1        0.0.0.2

 10.1.1.0/24        12       Inter   10.3.1.1        10.3.1.1        0.0.0.2

 

 Routing for ASEs

 Destination        Cost    
Type    Tag         NextHop         AdvRouter

 3.1.3.0/24         1       
Type2   1           10.3.1.1        10.2.1.1

 

 Total nets: 6

 Intra area: 2  Inter area: 3 
ASE: 1  NSSA: 0

The output shows that an external route imported
from the NSSA area exists on Switch D.

### Example: Configuring OSPF DR election

#### Network configuration

As shown in [Figure 18](#_Ref279690988):

·     Enable OSPF on Switches A, B, C, and D on the
same network.

·     Configure Switch D as the DR, and configure
Switch C as the BDR.

·     Change the router priorities on the interfaces
to configure Switch A as the DR and Switch C as the BDR.

Figure 18 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11704764_x_Img_x_png_17_2216018_294551_0.png)

‌

#### Procedure

**1\.**Configure IP addresses for interfaces. (Details
not shown.)

**2\.**Configure basic OSPF settings on all
switches. (Details not shown.)

For more information, see "[Example: Configuring basic OSPF](#_Ref529261211)."

**3\.**Display OSPF neighbor information on Switch
A.

\[SwitchA] display ospf peer
verbose

 

          OSPF Process 1 with
Router ID 1.1.1.1

                  Neighbors

 

 Area 0.0.0.0 interface 192.168.1.1(Vlan-interface1)'s neighbors

 Router ID: 2.2.2.2          Address: 192.168.1.2      GR State: Normal

   State: 2-Way  Mode: None 
Priority: 1

   DR: 192.168.1.4 
BDR: 192.168.1.3  MTU: 0

   Options is 0x02
(-\|-\|-\|-\|-\|-\|E\|-)

   Dead timer due in 38  sec

   Neighbor is up for 00:01:31

   Authentication Sequence: \[
0 ]

   Neighbor state change
count: 6

   BFD status: Disabled

 

 Router ID: 3.3.3.3          Address: 192.168.1.3      GR State: Normal

   State: Full  Mode: Nbr is master 
Priority: 1

   DR: 192.168.1.4 
BDR: 192.168.1.3  MTU: 0

   Options is 0x02
(-\|-\|-\|-\|-\|-\|E\|-)

   Dead timer due in 31  sec

   Neighbor is up for 00:01:28

   Authentication Sequence: \[
0 ]

   Neighbor state change
count: 6

   BFD status: Disabled

 

 Router ID: 4.4.4.4          Address: 192.168.1.4      GR State: Normal

   State: Full  Mode: Nbr is master 
Priority: 1

   DR: 192\.168.1.4  BDR: 192.168.1.3  MTU: 0

   Options is 0x02
(-\|-\|-\|-\|-\|-\|E\|-)

   Dead timer due in 31  sec

   Neighbor is up for 00:01:28

   Authentication Sequence: \[
0 ]

   Neighbor state change
count: 6

   BFD status: Disabled

The output shows that Switch D is the DR and
Switch C is the BDR.

**4\.**Configure router priorities on interfaces:

\# Configure Switch A.

\[SwitchA] interface vlan-interface
1

\[SwitchA-Vlan-interface1] ospf
dr-priority 100

\[SwitchA-Vlan-interface1] quit

\# Configure Switch B.

\[SwitchB] interface vlan-interface
1

\[SwitchB-Vlan-interface1] ospf
dr-priority 0

\[SwitchB-Vlan-interface1] quit

\# Configure Switch C.

\[SwitchC] interface vlan-interface
1

\[SwitchC-Vlan-interface1] ospf
dr-priority 2

\[SwitchC-Vlan-interface1] quit

\# Display neighbor information on Switch
D.

\<SwitchD\> display ospf
peer verbose

 

          OSPF Process 1 with
Router ID 4.4.4.4

                  Neighbors

 

 Area 0.0.0.0 interface 192.168.1.4(Vlan-interface1)'s neighbors

 Router ID: 1.1.1.1      Address: 192.168.1.1      GR State: Normal

   State: Full  Mode:Nbr is  slave 
Priority: 100

   DR: 192.168.1.4 
BDR: 192.168.1.3  MTU: 0

   Options is 0x02
(-\|-\|-\|-\|-\|-\|E\|-)

   Dead timer due in 31  sec

   Neighbor is up for 00:11:17

   Authentication Sequence: \[
0 ]

   Neighbor state change
count: 6

   BFD status: Disabled

 

 Router ID: 2.2.2.2      Address: 192.168.1.2      GR State: Normal

   State: Full  Mode:Nbr is  slave 
Priority: 0

   DR: 192.168.1.4 
BDR: 192.168.1.3  MTU: 0

   Options is 0x02
(-\|-\|-\|-\|-\|-\|E\|-)

   Dead timer due in 35  sec

   Neighbor is up for 00:11:19

   Authentication Sequence: \[
0 ]

   Neighbor state change
count: 6

   BFD status: Disabled

 

 Router ID: 3.3.3.3      Address: 192.168.1.3      GR State: Normal

   State: Full  Mode:Nbr is  slave 
Priority: 2

   DR: 192.168.1.4 
BDR: 192.168.1.3  MTU: 0

   Options is 0x02
(-\|-\|-\|-\|-\|-\|E\|-)

   Dead timer due in 33  sec

   Neighbor is up for 00:11:15

   Authentication Sequence: \[
0 ]

   Neighbor state change count:
6

   BFD status: Disabled

The output shows that the DR and BDR are
not changed, because the priority settings do not take effect immediately.

**5\.**Restart OSPF processes:

\# Restart the OSPF process of Switch A.

\<SwitchA\> reset ospf 1
process

Reset OSPF process? \[Y/N]:y

\# Restart the OSPF process of Switch B.

\<SwitchB\> reset ospf 1
process

Reset OSPF process? \[Y/N]:y

\# Restart the OSPF process of Switch C.

\<SwitchC\> reset ospf 1
process

Reset OSPF process? \[Y/N]:y

\# Restart the OSPF process of Switch D.

\<SwitchD\> reset ospf 1
process

Reset OSPF process? \[Y/N]:y

\# Display neighbor information on Switch
D.

\<SwitchD\> display ospf
peer verbose

 

          OSPF Process 1 with
Router ID 4.4.4.4

                  Neighbors

 

 Area 0.0.0.0 interface 192.168.1.4(Vlan-interface1)'s neighbors

 Router ID: 1.1.1.1          Address: 192.168.1.1      GR State: Normal

   State: Full  Mode: Nbr is slave 
Priority: 100

   DR: 192.168.1.1 
BDR: 192.168.1.3  MTU: 0

   Options is 0x02
(-\|-\|-\|-\|-\|-\|E\|-)

   Dead timer due in 39  sec

   Neighbor is up for 00:01:40

   Authentication Sequence: \[
0 ]

   Neighbor state change
count: 6

   BFD status: Disabled

 

 Router ID: 2.2.2.2          Address: 192.168.1.2      GR State: Normal

   State: 2-Way  Mode: None 
Priority: 0

   DR: 192.168.1.1 
BDR: 192.168.1.3  MTU: 0

   Options is 0x02
(-\|-\|-\|-\|-\|-\|E\|-)

   Dead timer due in 35  sec

   Neighbor is up for 00:01:44

   Authentication Sequence: \[
0 ]

   Neighbor state change
count: 6

   BFD status: Disabled

 

 Router ID: 3.3.3.3          Address: 192.168.1.3      GR State: Normal

   State: Full  Mode: Nbr is slave 
Priority: 2

   DR: 192.168.1.1 
BDR: 192.168.1.3  MTU: 0

   Options is 0x02
(-\|-\|-\|-\|-\|-\|E\|-)

   Dead timer due in 39  sec

   Neighbor is up for 00:01:41

   Authentication Sequence: \[
0 ]

   Neighbor state change
count: 6

   BFD status: Disabled

The output shows that Switch A becomes
the DR and Switch C becomes the BDR.

If the neighbor state is full, Switch D has established an adjacency with the
neighbor. If the neighbor state is 2-way, the two
switches are not the DR or the BDR, and they do not exchange LSAs.

\# Display OSPF interface information.

\<SwitchA\> display ospf
interface

 

          OSPF Process 1 with
Router ID 1.1.1.1

                  Interfaces

 

 Area: 0.0.0.0

 IP Address      Type     
State   Cost  Pri   DR             BDR

 192.168.1.1     Broadcast DR      1     100   192.168.1.1    192.168.1.3

 

\<SwitchB\> display ospf
interface

 

          OSPF Process 1 with
Router ID 2.2.2.2

                  Interfaces

 

 Area: 0.0.0.0

 IP Address      Type     
State    Cost  Pri   DR            BDR

 192.168.1.2     Broadcast DROther  1     0     192.168.1.1   192.168.1.3

The interface state DROther means the interface is not the DR or BDR.

### Example: Configuring OSPF virtual link

#### Network configuration

As shown in [Figure 19](#_Ref279691026), configure a virtual
link between Switch B and Switch C to connect Area 2 to the backbone area. After
configuration, Switch B can learn routes to Area 2\.

Figure 19 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11704765_x_Img_x_png_18_2216018_294551_0.png)

‌

#### Procedure

**1\.**Configure IP addresses for interfaces. (Details
not shown.)

**2\.**Enable OSPF:

\# Configure Switch A.

\<SwitchA\> system-view

\[SwitchA] ospf 1 router-id 1.1.1.1

\[SwitchA-ospf-1] area 0

\[SwitchA-ospf-1-area-0.0.0.0] network 10.1.1.0 0.0.0.255

\[SwitchA-ospf-1-area-0.0.0.0] quit

\[SwitchA-ospf-1] quit

\# Configure Switch B.

\<SwitchB\> system-view

\[SwitchB] ospf 1 router-id 2.2.2.2

\[SwitchB-ospf-1] area 0

\[SwitchB-ospf-1-area-0.0.0.0] network 10.1.1.0 0.0.0.255

\[SwitchB-ospf-1-area-0.0.0.0] quit

\[SwitchB-ospf-1] area 1

\[SwitchB–ospf-1-area-0.0.0.1] network 10.2.1.0 0.0.0.255

\[SwitchB–ospf-1-area-0.0.0.1] quit

\[SwitchB-ospf-1] quit

\# Configure Switch C.

\<SwitchC\> system-view

\[SwitchC] ospf 1 router-id 3.3.3.3

\[SwitchC-ospf-1] area 1

\[SwitchC-ospf-1-area-0.0.0.1] network 10.2.1.0 0.0.0.255

\[SwitchC-ospf-1-area-0.0.0.1] quit

\[SwitchC-ospf-1] area 2

\[SwitchC–ospf-1-area-0.0.0.2] network 10.3.1.0 0.0.0.255

\[SwitchC–ospf-1-area-0.0.0.2] quit

\[SwitchC-ospf-1] quit

\# Configure Switch D.

\<SwitchD\> system-view

\[SwitchD] ospf 1 router-id 4.4.4.4

\[SwitchD-ospf-1] area 2

\[SwitchD-ospf-1-area-0.0.0.2] network 10.3.1.0 0.0.0.255

\[SwitchD-ospf-1-area-0.0.0.2] quit

\[SwitchD-ospf-1] quit

\# Display the OSPF routing table on Switch
B.

\[SwitchB] display ospf routing

 

          OSPF Process 1 with
Router ID 2.2.2.2

                   Routing
Table

 

                Topology base
(MTID 0\)

 

 Routing for network

 Destination        Cost    
Type    NextHop         AdvRouter       Area

 10.2.1.0/24        2        Transit 10.2.1.1        3.3.3.3         0.0.0.1

 10.1.1.0/24        2        Transit 10.1.1.2        2.2.2.2         0.0.0.0

 Total nets: 2

 Intra area: 2  Inter area: 0 
ASE: 0  NSSA: 0

The output shows that Switch B does not
have routes to Area 2 because Area 0 is not directly connected to Area 2\.

**3\.**Configure a virtual link:

\# Configure Switch B.

\[SwitchB] ospf

\[SwitchB-ospf-1] area 1

\[SwitchB-ospf-1-area-0.0.0.1] vlink-peer 3.3.3.3

\[SwitchB-ospf-1-area-0.0.0.1] quit

\[SwitchB-ospf-1] quit

\# Configure Switch C.

\[SwitchC] ospf 1

\[SwitchC-ospf-1] area 1

\[SwitchC-ospf-1-area-0.0.0.1] vlink-peer 2.2.2.2

\[SwitchC-ospf-1-area-0.0.0.1] quit

\[SwitchC-ospf-1] quit

\# Display the OSPF routing table on Switch
B.

\[SwitchB] display ospf routing

 

          OSPF Process 1 with
Router ID 2.2.2.2

                   Routing
Table

 

                Topology base
(MTID 0\)

 

 Routing for network

 Destination        Cost    
Type    NextHop         AdvRouter       Area

 10.2.1.0/24        2        Transit 10.2.1.1        3.3.3.3         0.0.0.1

 10.3.1.0/24        5        Inter   10.2.1.2        3.3.3.3         0.0.0.0

 10.1.1.0/24        2        Transit 10.1.1.2        2.2.2.2         0.0.0.0

 

 Total nets: 3

 Intra area: 2  Inter area: 1 
ASE: 0  NSSA: 0

The output shows that Switch B has
learned the route 10.3.1.0/24 to Area 2\.

### Example: Configuring OSPF GR

#### Network configuration

As shown in [Figure 20](#_Ref150053592):

·     Switch A, Switch B, and Switch C that belong to
the same AS and the same OSPF routing domain are GR capable.

·     Switch A acts as the non-IETF GR restarter. Switch
B and Switch C are the GR helpers, and synchronize their LSDBs with Switch A
through OOB communication of GR.

Figure 20 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11704766_x_Img_x_png_19_2216018_294551_0.png)

‌

#### Procedure

**1\.**Configure IP addresses for interfaces. (Details
not shown.)

**2\.**Enable OSPF:

\# Configure Switch A.

SwitchA\> system-view

\[SwitchA] router id 1.1.1.1

\[SwitchA] ospf 100

\[SwitchA-ospf-100] area 0

\[SwitchA-ospf-100-area-0.0.0.0]
network 192.1.1.0 0.0.0.255

\[SwitchA-ospf-100-area-0.0.0.0]
quit

\[SwitchA-ospf-1] quit

\# Configure Switch B.

\<SwitchB\> system-view

\[SwitchB] router id 2.2.2.2

\[SwitchB] ospf 100

\[SwitchB-ospf-100] area 0

\[SwitchB-ospf-100-area-0.0.0.0]
network 192.1.1.0 0.0.0.255

\[SwitchB-ospf-100-area-0.0.0.0]
quit

\[SwitchB-ospf-1] quit

\# Configure Switch C.

\<SwitchC\> system-view

\[SwitchC] router id 3.3.3.3

\[SwitchC] ospf 100

\[SwitchC-ospf-100] area 0

\[SwitchC-ospf-100-area-0.0.0.0]
network 192.1.1.0 0.0.0.255

\[SwitchC-ospf-100-area-0.0.0.0]
quit

\[SwitchC-ospf-1] quit

**3\.**Configure OSPF GR:

\# Configure Switch A as the non-IETF OSPF
GR restarter: enable the link-local signaling capability, the out-of-band re-synchronization
capability, and non-IETF GR capability for OSPF process 100\.

\[SwitchA-ospf-100] enable
link-local-signaling

\[SwitchA-ospf-100] enable
out-of-band-resynchronization

\[SwitchA-ospf-100]
graceful-restart

\[SwitchA-ospf-100] quit

\# Configure Switch B as the GR helper: enable
the link-local signaling capability and the out-of-band re-synchronization
capability for OSPF process 100\.

\[SwitchB-ospf-100] enable
link-local-signaling

\[SwitchB-ospf-100] enable
out-of-band-resynchronization

\# Configure Switch C as the GR helper: enable
the link-local signaling capability and the out-of-band re-synchronization
capability for OSPF process 100\.

\[SwitchC-ospf-100] enable
link-local-signaling

\[SwitchC-ospf-100] enable
out-of-band-resynchronization

#### Verifying the configuration

\# Enable OSPF GR event debugging and
restart the OSPF process by using GR on Switch A.

\<SwitchA\> debugging ospf event
graceful-restart

\<SwitchA\> terminal monitor

\<SwitchA\> terminal logging
level 7

\<SwitchA\> reset ospf 100 process
graceful-restart

Reset OSPF process? \[Y/N]:y

%Oct 21 15:29:28:727 2019 SwitchA
OSPF/5/OSPF\_NBR\_CHG: OSPF 100 Neighbor 192.1.1.2(Vlan-interface100) from Full
to Down.

%Oct 21 15:29:28:729 2019 SwitchA
OSPF/5/OSPF\_NBR\_CHG: OSPF 100 Neighbor 192.1.1.3(Vlan-interface100) from Full
to Down.

\*Oct 21 15:29:28:735 2019 SwitchA
OSPF/7/DEBUG:

OSPF 100 nonstandard GR Started for
OSPF Router

\*Oct 21 15:29:28:735 2019 SwitchA
OSPF/7/DEBUG:

OSPF 100 created GR wait
timer,timeout interval is 40(s).

\*Oct 21 15:29:28:735 2019 SwitchA
OSPF/7/DEBUG:

OSPF 100 created GR Interval
timer,timeout interval is 120(s).

\*Oct 21 15:29:28:758 2019 SwitchA
OSPF/7/DEBUG:

OSPF 100 created OOB Progress timer
for neighbor 192.1.1.3.

\*Oct 21 15:29:28:766 2019 SwitchA OSPF/7/DEBUG:

OSPF 100 created OOB Progress timer
for neighbor 192.1.1.2.

%Oct 21 15:29:29:902 2019 SwitchA
OSPF/5/OSPF\_NBR\_CHG: OSPF 100 Neighbor 192.1.1.2(Vlan-interface100) from
Loading to Full.

\*Oct 21 15:29:29:902 2019 SwitchA
OSPF/7/DEBUG:

OSPF 100 deleted OOB Progress timer
for neighbor 192.1.1.2.

%Oct 21 15:29:30:897 2019 SwitchA
OSPF/5/OSPF\_NBR\_CHG: OSPF 100 Neighbor 192.1.1.3(Vlan-interface100) from
Loading to Full.

\*Oct 21 15:29:30:897 2019 SwitchA
OSPF/7/DEBUG:

OSPF 100 deleted OOB Progress timer
for neighbor 192.1.1.3.

\*Oct 21 15:29:30:911 2019 SwitchA
OSPF/7/DEBUG:

OSPF GR: Process 100 Exit
Restart,Reason : DR or BDR change,for neighbor : 192.1.1.3.

\*Oct 21 15:29:30:911 2019 SwitchA
OSPF/7/DEBUG:

OSPF 100 deleted GR Interval timer.

\*Oct 21 15:29:30:912 2019 SwitchA
OSPF/7/DEBUG:

OSPF 100 deleted GR wait timer.

%Oct 21 15:29:30:920 2019 SwitchA
OSPF/5/OSPF\_NBR\_CHG: OSPF 100 Neighbor 192.1.1.2(Vlan-interface100) from Full
to Down.

%Oct 21 15:29:30:921 2019 SwitchA
OSPF/5/OSPF\_NBR\_CHG: OSPF 100 Neighbor 192.1.1.3(Vlan-interface100) from Full
to Down.

%Oct 21 15:29:33:815 2019 SwitchA
OSPF/5/OSPF\_NBR\_CHG: OSPF 100 Neighbor 192.1.1.3(Vlan-interface100) from
Loading to Full.

%Oct 21 15:29:35:578 2019 SwitchA
OSPF/5/OSPF\_NBR\_CHG: OSPF 100 Neighbor 192.1.1.2(Vlan-interface100) from
Loading to Full.

The output shows that Switch A completes
GR.

### Example: Configuring OSPFNSR

#### Network configuration

As shown in [Figure 21](#_Ref229478029), Switch
S, Switch A, and Switch B belong to the same OSPF routing domain. Enable OSPF
NSR on Switch S to ensure correct routing when an active/standby switchover
occurs on Switch S.

Figure 21 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11704768_x_Img_x_png_20_2216018_294551_0.png)

‌

#### Procedure

**1\.**Configure IP addresses and subnet masks for
interfaces on the switches. (Details not shown.)

**2\.**Configure OSPF on the switches to ensure the
following: (Details not shown.)

¡     Switch
S, Switch A, and Switch B can communicate with each other at Layer 3\.

¡     Dynamic
route update can be implemented among them with OSPF.

**3\.**Enable OSPF NSR on Switch S.

\<SwitchS\> system-view

\[SwitchS] ospf 100

\[SwitchS-ospf-100]
non-stop-routing

\[SwitchS-ospf-100] quit

#### Verifying the configuration

\# Perform an active/standby switchover on
Switch S.

\[SwitchS] placement reoptimize

Predicted changes to the placement

Service Group(instance
name)                      Cur location  New location

\----------------------------------------------------------------------------

rib                                               0/0          
0/0

staticroute                                       0/0          
0/0

ospf                                              0/0          
1/0

Continue? \[y/n]:y

Re-optimization of the placement
start. You will be notified on completion.

Re-optimization of the placement
complete. Use 'display placement' to view the new placement.

\# During the switchover period, display OSPF
neighbors on Switch A to verify the neighbor relationship between Switch A and
Switch S.

\<SwitchA\> display ospf peer

 

          OSPF Process 1 with Router
ID 2.2.2.1

               Neighbor Brief
Information

 

 Area: 0.0.0.0

 Router ID       Address         Pri
Dead-Time  State             Interface

 3.3.3.1         12.12.12.2      1   37        
Full/BDR          Vlan100

\# Display OSPF routes on Switch A to verify
if Switch A has a route to the loopback interface on Switch B.

\<SwitchA\> display ospf routing

 

          OSPF Process 1 with Router
ID 2.2.2.1

                   Routing Table

 

                Topology base (MTID
0\)

 

 Routing for network

 Destination        Cost     Type   
NextHop         AdvRouter       Area

 44.44.44.44/32     2       
Stub    12.12.12.2      4.4.4.1         0.0.0.0

 14.14.14.0/24      2        Transit
12.12.12.2      4.4.4.1         0.0.0.0

 22.22.22.22/32     0        Stub   
22.22.22.22     2.2.2.1         0.0.0.0

 12.12.12.0/24      1        Transit
12.12.12.1      2.2.2.1         0.0.0.0

 

 Total nets: 4

 Intra area: 4  Inter area: 0  ASE:
0  NSSA: 0

\# Display OSPF neighbors on Switch B to verify
the neighbor relationship between Switch B and Switch S.

\<SwitchB\> display ospf peer

 

          OSPF Process 1 with Router
ID 4.4.4.1

               Neighbor Brief Information

 

 Area: 0.0.0.0

 Router ID       Address         Pri
Dead-Time  State             Interface

 3.3.3.1         14.14.14.2      1  
39         Full/BDR          Vlan200

\# Display OSPF routes on Switch B to verify
if Switch B has a route to the loopback interface on Switch A.

\<SwitchB\> display ospf routing

 

          OSPF Process 1 with Router
ID 4.4.4.1

                   Routing Table

 

                Topology base (MTID
0\)

 

 Routing for network

 Destination        Cost     Type   
NextHop         AdvRouter       Area

 44.44.44.44/32     0        Stub   
44.44.44.44     4.4.4.1         0.0.0.0

 14.14.14.0/24      1        Transit
14.14.14.1      4.4.4.1         0.0.0.0

 22.22.22.22/32     2       
Stub    14.14.14.2      2.2.2.1         0.0.0.0

 12.12.12.0/24      2        Transit
14.14.14.2      2.2.2.1         0.0.0.0

 

 Total nets: 4

 Intra area: 4  Inter area: 0  ASE:
0  NSSA: 0

The output shows the following when an
active/standby switchover occurs on Switch S:

·     The neighbor relationships and routing
information on Switch A and Switch B have not changed.

·     The traffic from Switch A to Switch B has not
been impacted.

### Example: Configuring BFD forOSPF

#### Network configuration

As shown in [Figure 22](#_Ref240711119),
run OSPF on Switch A, Switch B, and Switch C so that they are reachable to each
other at the network layer.

·     When the link over which Switch A and Switch B
communicate through a Layer 2 switch fails, BFD can quickly detect the failure
and notify OSPF of the failure.

·     Switch A and Switch B then communicate through
Switch C.

Figure 22 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11704769_x_Img_x_png_21_2216018_294551_0.png)

‌

Table 2 Interface and IP address assignment

| Device | Interface | IP address |
| --- | --- | --- |
| Switch A | Vlan-int10 | 192.168.0.102/24 || Switch A | Vlan-int11 | 10.1.1.102/24 || Switch A | Loop0 | 121.1.1.1/32 || Switch B | Vlan-int10 | 192.168.0.100/24 || Switch B | Vlan-int13 | 13.1.1.1/24 || Switch B | Loop0 | 120.1.1.1/32 || Switch C | Vlan-int11 | 10.1.1.100/24 || Switch C | Vlan-int13 | 13.1.1.2/24 |








‌

#### Procedure

**1\.**Configure IP addresses for interfaces. (Details
not shown.)

**2\.**Enable OSPF:

\# Configure Switch A.

\<SwitchA\> system-view

\[SwitchA] ospf

\[SwitchA-ospf-1] area 0

\[SwitchA-ospf-1-area-0.0.0.0] network
192.168.0.0 0.0.0.255

\[SwitchA-ospf-1-area-0.0.0.0] network
10.1.1.0 0.0.0.255

\[SwitchA-ospf-1-area-0.0.0.0] network
121.1.1.1 0.0.0.0

\[SwitchA-ospf-1-area-0.0.0.0]
quit

\[SwitchA-ospf-1] quit

\# Configure Switch B.

\<SwitchB\> system-view

\[SwitchB] ospf

\[SwitchB-ospf-1] area 0

\[SwitchB-ospf-1-area-0.0.0.0] network
192.168.0.0 0.0.0.255

\[SwitchB-ospf-1-area-0.0.0.0] network
13.1.1.0 0.0.0.255

\[SwitchB-ospf-1-area-0.0.0.0] network
120.1.1.1 0.0.0.0

\[SwitchB-ospf-1-area-0.0.0.0]
quit

\[SwitchB-ospf-1] quit

\# Configure Switch C.

\<SwitchC\> system-view

\[SwitchC] ospf

\[SwitchC-ospf-1] area 0

\[SwitchC-ospf-1-area-0.0.0.0] network
10.1.1.0 0.0.0.255

\[SwitchC-ospf-1-area-0.0.0.0] network
13.1.1.0 0.0.0.255

\[SwitchC-ospf-1-area-0.0.0.0]
quit

\[SwitchC-ospf-1] quit

**3\.**Configure BFD:

\# Enable BFD on Switch A and configure
BFD parameters.

\[SwitchA] bfd session
init-mode active

\[SwitchA] interface
vlan-interface 10

\[SwitchA-Vlan-interface10] ospf
bfd enable

\[SwitchA-Vlan-interface10] bfd
min-transmit-interval 500

\[SwitchA-Vlan-interface10] bfd
min-receive-interval 500

\[SwitchA-Vlan-interface10] bfd
detect-multiplier 7

\[SwitchA-Vlan-interface10]
quit

\# Enable BFD on Switch B and configure
BFD parameters.

\[SwitchB] bfd session
init-mode active

\[SwitchB] interface
vlan-interface 10

\[SwitchB-Vlan-interface10] ospf
bfd enable

\[SwitchB-Vlan-interface10] bfd
min-transmit-interval 500

\[SwitchB-Vlan-interface10] bfd
min-receive-interval 500

\[SwitchB-Vlan-interface10] bfd
detect-multiplier 6

\[SwitchB-Vlan-interface10]
quit

#### Verifying the configuration

\# Display the BFD information on Switch A.

\<SwitchA\> display bfd session

 

 Total sessions: 1     Up sessions:
1     Init mode: Active

 

 IPv4 session working in control packet mode:

 

 LD/RD           SourceAddr     
DestAddr        State   Holdtime    Interface

 3/1             192.168.0.102  
192.168.0.100   Up      1700ms      Vlan10

\# Display routes destined for 120.1.1.1/32 on
Switch A.

\<SwitchA\> display ip
routing-table 120.1.1.1 verbose

 

Summary count : 1

 

Destination: 120.1.1.1/32

   Protocol: O\_INTRA            

 Process ID: 1

  SubProtID: 0x1                   
Age: 04h20m37s

       Cost: 1              
Preference: 10

      IpPre: N/A            
QosLocalID: N/A

        Tag: 0                   
State: Active Adv

  OrigTblID: 0x0               
OrigVrf: default-vrf

    TableID: 0x2                 OrigAs:
0

      NibID: 0x26000002         
LastAs: 0

     AttrID: 0xffffffff       
Neighbor: 0.0.0.0

      Flags: 0x1008c       
OrigNextHop: 192.168.0.100

      Label: NULL           RealNextHop: 192.168.0.100

    BkLabel: NULL            
BkNextHop: N/A

    SRLabel: NULL            
BkSRLabel: NULL

   SIDIndex: NULL              
InLabel: NULL

  Tunnel ID: Invalid          Interface: Vlan-interface10

BkTunnel ID: Invalid       
BkInterface: N/A

   FtnIndex: 0x0           TrafficIndex:
N/A

  Connector: N/A                
PathID: 0x0

   LinkCost: 0              
MicroSegID: 0 

RealFIRType: Normal          
RealThres: 0

The output shows that Switch A communicates
with Switch B through VLAN-interface 10\. Then the link over VLAN-interface 10
fails.

\# Display routes destined for 120.1.1.1/32 on
Switch A.

\<SwitchA\> display ip
routing-table 120.1.1.1 verbose

 

Summary count : 1

 

Destination: 120.1.1.1/32

   Protocol: O\_INTRA            

 Process ID: 1

  SubProtID: 0x1                    Age:
04h20m37s

       Cost: 2               Preference:
10

      IpPre: N/A            
QosLocalID: N/A

        Tag: 0                   
State: Active Adv

  OrigTblID: 0x0               
OrigVrf: default-vrf

    TableID: 0x2                
OrigAs: 0

      NibID: 0x26000002         
LastAs: 0

     AttrID: 0xffffffff       
Neighbor: 0.0.0.0

      Flags: 0x1008c        OrigNextHop:
10.1.1.100

      Label: NULL           RealNextHop: 10.1.1.100

    BkLabel: NULL            B
kNextHop: N/A

    SRLabel: NULL            
BkSRLabel: NULL

   SIDIndex: NULL              
InLabel: NULL

  Tunnel ID: Invalid          Interface: Vlan-interface11

BkTunnel ID: Invalid       
BkInterface: N/A

   FtnIndex: 0x0           TrafficIndex:
N/A

  Connector: N/A                
PathID: 0x0

   LinkCost: 0              
MicroSegID: 0 

RealFIRType: Normal          
RealThres: 0

The output shows that Switch A communicates
with Switch B through VLAN-interface 11\.

### Example: Configuring OSPFFRR

#### Network configuration

As shown in [Figure 23](#_Ref203379900),
Switch A, Switch B, and Switch C reside in the same OSPF domain. Configure OSPF
FRR so that when the link between Switch A and Switch B fails, traffic is
immediately switched to Link B.

Figure 23 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11704770_x_Img_x_png_22_2216018_294551_0.png)

‌

Table 3 Interface and IP address assignment

| Device | Interface | IP address |
| --- | --- | --- |
| Switch A | Vlan-int100 | 12.12.12.1/24 || Switch A | Vlan-int200 | 13.13.13.1/24 || Switch A | Loop0 | 1.1.1.1/32 || Switch B | Vlan-int101 | 24.24.24.4/24 || Switch B | Vlan-int200 | 13.13.13.2/24 || Switch B | Loop0 | 4.4.4.4/32 || Switch C | Vlan-int100 | 12.12.12.2/24 || Switch C | Vlan-int101 | 24.24.24.2/24 |








‌

#### Procedure

**1\.**Configure IP addresses and subnet masks for
interfaces on the switches. (Details not shown.)

**2\.**Configure OSPF on the switches to ensure
that Switch A, Switch B, and Switch C can communicate with each other at the
network layer. (Details not shown.)

**3\.**Configure OSPF FRR to automatically
calculate the backup next hop:

You can enable OSPF FRR to either
calculate a backup next hop by using the LFA algorithm, or specify a backup
next hop by using a routing policy.

¡     (Method
1.) Enable OSPF FRR to calculate the backup next hop by using the LFA
algorithm:

\# Configure Switch A.

\<SwitchA\> system-view

\[SwitchA] ospf 1

\[SwitchA-ospf-1] fast-reroute lfa

\[SwitchA-ospf-1] quit

\# Configure Switch B.

\<SwitchB\> system-view

\[SwitchB] ospf 1

\[SwitchB-ospf-1] fast-reroute lfa

\[SwitchB-ospf-1] quit

¡     (Method
2.) Enable OSPF FRR to designate a backup next hop by using a routing policy.

\# Configure Switch A.

\<SwitchA\> system-view

\[SwitchA] ip prefix-list abc
index 10 permit 4.4.4.4 32

\[SwitchA] route-policy frr
permit node 10

\[SwitchA-route-policy-frr-10]
if-match ip address prefix-list abc

\[SwitchA-route-policy-frr-10] apply
fast-reroute backup-interface vlan-interface 100 backup-nexthop 12.12.12.2

\[SwitchA-route-policy-frr-10]
quit

\[SwitchA] ospf 1

\[SwitchA-ospf-1] fast-reroute route-policy
frr

\[SwitchA-ospf-1] quit

\# Configure Switch B.

\<SwitchB\> system-view

\[SwitchB] ip prefix-list abc
index 10 permit 1.1.1.1 32

\[SwitchB] route-policy frr
permit node 10

\[SwitchB-route-policy-frr-10]
if-match ip address prefix-list abc

\[SwitchB-route-policy-frr-10] apply
fast-reroute backup-interface vlan-interface 101 backup-nexthop 24.24.24.2

\[SwitchB-route-policy-frr-10]
quit

\[SwitchB] ospf 1

\[SwitchB-ospf-1] fast-reroute route-policy
frr

\[SwitchB-ospf-1] quit

#### Verifying the configuration

\# Display route 4.4.4.4/32 on Switch A to view the backup next hop information.

\[SwitchA] display ip routing-table
4.4.4.4 verbose

 

Summary count : 1

 

Destination: 4.4.4.4/32

   Protocol: O\_INTRA            

 Process ID: 1

  SubProtID: 0x1                    Age:
04h20m37s

       Cost: 1               Preference:
10

      IpPre: N/A            
QosLocalID: N/A

        Tag: 0                    State:
Active Adv

  OrigTblID: 0x0                OrigVrf:
default-vrf

    TableID: 0x2                 OrigAs:
0

      NibID: 0x26000002          LastAs:
0

     AttrID: 0xffffffff        Neighbor:
0.0.0.0

      Flags: 0x1008c        OrigNextHop:
13.13.13.2

      Label: NULL           RealNextHop: 13.13.13.2

    BkLabel: NULL             BkNextHop: 12.12.12.2

    SRLabel: NULL            
BkSRLabel: NULL

   SIDIndex: NULL              
InLabel: NULL

  Tunnel ID: Invalid          Interface: Vlan-interface200

BkTunnel ID: Invalid        BkInterface: Vlan-interface100

   FtnIndex: 0x0           TrafficIndex:
N/A

  Connector: N/A                
PathID: 0x0

   LinkCost: 0              
MicroSegID: 0 

RealFIRType: Normal          
RealThres: 0

\# Display route 1.1.1.1/32 on Switch B to view the backup next hop information.

\[SwitchB] display ip routing-table
1.1.1.1 verbose

 

Summary count : 1

 

Destination: 1.1.1.1/32

   Protocol: O\_INTRA            

 Process ID: 1

  SubProtID: 0x1                    Age:
04h20m37s

       Cost: 1               Preference:
10

      IpPre: N/A            
QosLocalID: N/A

        Tag: 0                    State:
Active Adv

  OrigTblID: 0x0                OrigVrf:
default-vrf

    TableID: 0x2                 OrigAs:
0

      NibID: 0x26000002          LastAs:
0

     AttrID: 0xffffffff        Neighbor:
0.0.0.0

      Flags: 0x1008c        OrigNextHop:
13.13.13.1

      Label: NULL           RealNextHop: 13.13.13.1

    BkLabel: NULL             BkNextHop: 24.24.24.2

    SRLabel: NULL            
BkSRLabel: NULL

   SIDIndex: NULL              
InLabel: NULL

  Tunnel ID: Invalid          Interface: Vlan-interface200

BkTunnel ID: Invalid        BkInterface: Vlan-interface101

   FtnIndex: 0x0           TrafficIndex:
N/A

  Connector: N/A                
PathID: 0x0

   LinkCost: 0              
MicroSegID: 0 

RealFIRType: Normal          
RealThres: 0

## Troubleshooting OSPF configuration

### No OSPF neighbor relationship established

#### Symptom

No OSPF neighbor relationship can be
established.

#### Analysis

If the physical link and lower layer
protocols work correctly, verify OSPF parameters configured on interfaces. Two
neighbors must have the same parameters, such as the area ID, network segment,
and mask. (A P2P or virtual link can have different network segments and masks.)

#### Solution

To resolve the problem:

**1\.**Use the display ospf peer command to verify OSPF neighbor information.

**2\.**Use the display ospf interface command to verify OSPF interface information.

**3\.**Ping the neighbor router's IP address to verify
that the connectivity is normal.

**4\.**Verify OSPF timers. The dead interval on an interface
must be a minimum of four times the hello interval.

**5\.**On an NBMA network, use the peer ip-address command to manually specify the neighbor.

**6\.**A minimum of one interface must have a router
priority higher than 0 on an NBMA or a broadcast network.

**7\.**If the problem persists, contact H3C
Support.

### Incorrect routing information

#### Symptom

OSPF cannot find routes to other areas.

#### Analysis

The backbone area must maintain
connectivity to all other areas. If a router connects to more than one area, a
minimum of one area must be connected to the backbone. The backbone cannot be
configured as a stub area.

In a stub area, all routers cannot receive
external routes, and all interfaces connected to the stub area must belong to
the stub area.

#### Solution

To resolve the problem:

**1\.**Use the display ospf peer command to verify neighbor information.

**2\.**Use the display ospf interface command to verify OSPF interface information.

**3\.**Use the display ospf lsdb command to verify the LSDB.

**4\.**Use the display current-configuration configuration
ospf command to verify area configuration. If
more than two areas are configured, a minimum of one area is connected to the
backbone.

**5\.**In a stub area, all routers attached are
configured with the stub command. In an
NSSA area, all routers attached are configured with the nssa command.

**6\.**If a virtual link is configured, use the display ospf
vlink command to verify the state of the virtual
link.

**7\.**If the problem persists, contact H3C Support.

