
# Configuring RRPP

## About RRPP

The Rapid Ring Protection
Protocol (RRPP) is an H3C-proprietary link layer protocol designed for Ethernet
rings. RRPP can prevent broadcast storms caused by data loops when an Ethernet
ring is healthy. RRPP can also rapidly restore the communication paths between
the nodes when a link is disconnected on the ring. Compared with the spanning
tree protocol, the convergence time of RRPP is fast and independent of the
number of nodes in the Ethernet ring. RRPP is applicable to large-diameter
networks.

### RRPP networking

[Figure 1](#_Ref374363204) shows
a typical RRPP network with two Ethernet rings and multiple nodes. RRPP detects
ring status and sends topology change information by exchanging Rapid Ring
Protection Protocol Data Units (RRPPDUs) among the nodes.

Figure
1 RRPP networking diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705567_x_Img_x_png_0_2216097_294551_0.png)

 

#### RRPP domain

An RRPP domain is uniquely identified by a
domain ID. The interconnected devices with the same domain ID and control VLANs
constitute an RRPP domain. An RRPP domain contains the following elements: 

·     Primary ring and subring. 

·     Control VLAN.

·     Master node, transit node, edge node, and assistant
edge node.

·     Primary port, secondary port, common port, and edge
port. 

As shown in [Figure 1](#_Ref374363204),
Domain 1 is an RRPP domain, containing two RRPP rings: Ring 1 and Ring 2\. All
the nodes on the two RRPP rings belong to the RRPP domain. 

#### RRPP ring

A ring-shaped Ethernet topology is called an
RRPP ring. RRPP rings include primary rings and subrings. You can configure a
ring as either the primary ring or a subring by specifying its ring level. The
primary ring is of level 0, and a subring is of level 1\. An RRPP domain
contains one or multiple RRPP rings, one serving as the primary ring and the others
serving as subrings. A ring can be in one of the following states:

·     Health state—All physical links on the
Ethernet ring are connected.

·     Disconnect state—Some physical links on
the Ethernet ring are not connected. 

As shown in [Figure 1](#_Ref374363204),
Domain 1 contains two RRPP rings: Ring 1 and Ring 2\. The level is set to 0 for Ring
1 and 1 for Ring 2\. Ring 1 is configured as the primary ring, and Ring 2 is configured
as a subring. 

#### Control VLAN and protected VLAN

**1\.**Control VLAN

In an RRPP domain, a control VLAN is dedicated
to transferring RRPPDUs. On a device, the ports accessing an RRPP ring belong
to the control VLANs of the ring, and only these ports can join the control
VLANs. 

An RRPP domain is configured with the
following control VLANs: 

¡     One primary
control VLAN, which is the control VLAN for the primary ring.

¡     One
secondary control VLAN, which is the control VLAN for subrings. 

After you specify a VLAN as the primary
control VLAN, the system automatically configures the secondary control VLAN.
The VLAN ID is the primary control VLAN ID plus one. All subrings in the same
RRPP domain share the same secondary control VLAN. IP address configuration is
prohibited on the control VLAN interfaces. 

**2\.**Protected VLAN

A protected VLAN is dedicated to transferring
data packets. Both RRPP ports and non-RRPP ports can be assigned to a protected
VLAN. 

#### Node role

Each device on an RRPP ring is a node. The role
of a node is configurable. RRPP has the following node roles:

·     Master node—Each ring has only one master node. The master node initiates the
polling mechanism and determines the operations to be performed after a topology
change. 

·     Transit node—On the primary ring, transit nodes refer to all nodes except the
master node. On the subring, transit nodes refer to all nodes except the master
node and the nodes where the primary ring intersects with the subring. A
transit node monitors the state of its directly connected RRPP links and
notifies the master node of the link state changes, if any. Based on the link
state changes, the master node determines the operations to be performed.

·     Edge node—A special node residing on both the primary ring and a subring at
the same time. An edge node acts as a master node or transit node on the primary
ring and as an edge node on the subring. 

·     Assistant edge node—A special node residing on both the primary ring and a subring at
the same time. An assistant edge node acts as a master node or transit node on
the primary ring and as an assistant edge node on the subring. This node works
in conjunction with the edge node to detect the integrity of the primary ring
and to perform loop guard. 

As shown in [Figure 1](#_Ref374363204), Ring
1 is the primary ring and Ring 2 is a subring. Device A is the master node of
Ring 1\. Device B, Device C, and Device D are the transit nodes of Ring 1\.
Device E is the master node of Ring 2, Device B is the edge node of Ring 2, and
Device C is the assistant edge node of Ring 2\.

#### Port role

**1\.**Primary port and secondary port

Each master node or transit node has two
ports connected to an RRPP ring, a primary port and a secondary port. You can
determine the role of a port. 

In terms of functionality, the primary
port and the secondary port of a master node have the following differences:

¡     The
primary port and the secondary port are designed to play the role of sending
and receiving Hello packets, respectively.

¡     When
an RRPP ring is in Health state, the secondary port logically denies protected VLANs
and permits only the packets from the control VLANs. 

¡     When
an RRPP ring is in Disconnect state, the secondary port forwards packets from protected
VLANs.

In terms of functionality, the primary
port and the secondary port of a transit node are the same. Both are designed
for transferring protocol packets and data packets over an RRPP ring. 

As shown in [Figure 1](#_Ref374363204), Device
A is the master node of Ring 1\. Port 1 and Port 2 are the primary port and the
secondary port of the master node on Ring 1, respectively. Device B, Device C,
and Device D are the transit nodes of Ring 1\. Their Port 1 and Port 2 are the
primary port and the secondary port on Ring 1, respectively. 

**2\.**Common port and edge port

The ports connecting the edge node and assistant
edge node to the primary ring are common ports. The ports connecting the edge
node and assistant edge node only to the subrings are edge ports. You can
determine the role of a port. 

As shown in [Figure 1](#_Ref374363204), Device
B and Device C reside on Ring 1 and Ring 2\. Device B's Port 1 and Port 2 and Device
C's Port 1 and Port 2 access the primary ring, so they are common ports. Device
B's Port 3 and Device C's Port 3 access only the subring, so they are edge
ports. 

#### RRPP ring group

To reduce Edge-Hello traffic, you can
configure a group of subrings on the edge node or assistant edge node. You must
configure a device as the edge node of these subrings, and another device as
the assistant edge node of these subrings. Additionally, the subrings of the
edge node and assistant edge node must connect to the same subring packet tunnels
in major ring (SRPTs). Edge-Hello packets of the edge node of these subrings travel
to the assistant edge node of these subrings over the same link. 

An RRPP ring group configured on the edge
node is an edge node RRPP ring group. An RRPP ring group configured on an assistant
edge node is an assistant edge node RRPP ring group. Only one subring in an
edge node RRPP ring group is allowed to send Edge-Hello packets. 

### RRPPDUs

RRPPDUs of subrings
are transmitted as data packets in the primary ring, and RRPPDUs of the primary
ring can only be transmitted within the primary ring. In the primary ring, Common-Flush-FDB
packets and Complete-Flush-FDB packets of subrings are sent to the CPU on each
node for processing.

Table 1 RRPPDU
types and their functions

| Type | Description || Hello | The master node sends Hello packets (also known as Health packets) to detect the integrity of a ring in a network. || Fast-Hello | The master node sends Fast-Hello packets to detect the integrity of a ring in a network. Fast-Hello packets are used for the fast detection mechanism. || Link-Down | When a port on the transit node, edge node, or assistant edge node fails, the node initiates Link-Down packets to notify the master node of the disconnection of the ring. || Common-Flush-FDB | When an RRPP ring transits to Disconnect state, the master node initiates Common-Flush-FDB (FDB stands for Forwarding Database) packets. It uses the packets to instruct the transit nodes, edge nodes, and assistant edge nodes to update their own MAC address entries and ARP/ND entries. || Complete-Flush-FDB | When an RRPP ring transits to Health state, the master node sends Complete-Flush-FDB packets for the following purposes:  ·     Instruct the transit nodes, edge nodes, and assistant edge nodes to update their MAC address entries and ARP/ND entries.  ·     Instruct the transit nodes to unblock temporarily blocked ports. || Edge-Hello | The edge node sends Edge-Hello packets to examine the SRPTs between the edge node and the assistant edge node. || Fast-Edge-Hello | The edge node sends Fast-Edge-Hello packets to examine the SRPTs between it and the assistant edge node. Fast-Edge-Hello packets are used for the fast detection mechanism. || Major-Fault | The assistant edge node sends Major-Fault packets to notify the edge node of SRPT failure when an SRPT between assistant edge node and edge node is disconnected. |








| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |

 

### RRPP timers

When RRPP determines the link state of an
Ethernet ring, it uses the following timers: 

#### Hello timer

The Hello timer specifies the interval at
which the master node sends Hello packets out of the primary port. 

#### Fail timer

The Fail timer specifies the maximum delay of
Hello packets sent from the primary port to the secondary port of the master
node. If the secondary port receives the Hello packets sent by the local master
node before the Fail timer expires, the ring is in Health state. If the
secondary port does not receive the Hello packets before the Fail timer expires,
the ring transits to Disconnect state. 

In an RRPP domain, a transit node learns
the Fail timer value on the master node through the received Hello packets.
This ensures that all nodes in the ring network have consistent Fail timer
settings.

#### Link-up delay timer

This feature prevents frequent switchover
of RRPP traffic forwarding paths caused by unstable RRPP port states. This
feature behaves differently depending on whether you specify the distribute keyword in the linkup-delay-timer
command.

·     If you do not specify the distribute keyword, the master node starts the link-up delay timer when a faulty
port comes up and the master node receives Hello packets from the secondary
port.

¡     If
the master node can still receive Hello packets from the secondary port after
the link-up delay timer expires, the master node performs the following
operations:

\-     Changes the RRPP ring state from Disconnect to Health.

\-     Switches the traffic from the secondary port to the primary port.

¡     If
the master node cannot receive Hello packets from the secondary port after the
Fail timer expires and before the link-up delay timer expires, the master node
performs the following operations:

\-     Stops the link-up delay timer.

\-     Keeps the RRPP ring in Disconnect state.

·     If you specify the distribute keyword, all nodes in the RRPP domain can learn the value of the link-up
delay timer through Hello packets. When the faulty port comes up, the master
node performs the following operations:

¡     The
hosting RRPP node blocks the faulty port (the faulty port cannot send or
receive any packets).

¡     Starts
the link-up delay timer.

If the port does not become faulty after
the link-up delay timer expires, the hosting RRPP node sets the port state to
up. The master node can receive Hello packets from its secondary port again.
Then, the master node changes the RRPP ring state from Disconnect to Health and
switches the traffic from the secondary port to the primary port.

If the port becomes faulty again before
the link-up delay timer expires, the hosting RRPP node blocks the port and
stops the link-up delay timer.

#### Fast-Hello timer

The Fast-Hello timer specifies the interval
at which the master node sends Fast-Hello packets out of the primary port. 

#### Fast-Fail timer

The Fast-Fail timer specifies the maximum
delay of Fast-Hello packets sent from the primary port to the secondary port of
the master node. If the secondary port receives the Fast-Hello packets before
the Fast-Fail timer expires, the ring is in Health state. Otherwise, the ring
transits to Disconnect state. 

A transit node cannot learn the Fast-Fail
timer value on the master node through received Fast-Hello packets.

#### Fast-Edge-Hello timer

The Fast-Edge-Hello timer specifies the
interval at which the edge node sends Fast-Edge-Hello packets out of the common
port. 

#### Fast-Edge-Fail timer

The Fast-Edge-Fail timer specifies the
maximum delay of Fast-Edge-Hello packets from the common port of the edge node
to the common port of the assistant edge node. If the assistant edge node receives
the Fast-Edge-Hello packets from the common port before the Fast-Edge-Fail
timer expires, the ring is in Health state. Otherwise, the ring transits to
Disconnect state. 

### How RRPP works

#### Polling mechanism

The polling mechanism is used by the master
node of an RRPP ring to examine the Health state of the ring network.

The master node sends Hello packets out of its
primary port at the Hello interval. These Hello packets travel through each
transit node on the ring in turn. 

·     If the ring is complete, the secondary port of
the master node receives Hello packets before the Fail timer expires. The
master node keeps the secondary port blocked. 

·     If the ring is disconnected, the secondary port
of the master node fails to receive Hello packets before the Fail timer
expires. The master node releases the secondary port from blocking protected VLANs.
It sends Common-Flush-FDB packets to instruct all transit nodes to update their
own MAC address entries and ARP/ND entries. 

#### Load balancing

In a ring network, traffic from multiple
VLANs might be transmitted at the same time. RRPP can implement load balancing
by transmitting traffic from different VLANs along different paths.

You can configure multiple RRPP domains for
a ring network. Each RRPP domain transmits the traffic from the specified VLANs
(protected VLANs). Traffic from different VLANs can be transmitted according to
different topologies in the ring network for load balancing. 

As shown in [Figure 6](#_Ref212280450), Ring 1 is configured as the primary ring of Domain 1 and Domain 2,
which are configured with different protected VLANs. Device A is the master
node of Ring 1 in Domain 1\. Device B is the master node of Ring 1 in Domain 2\.
With such configurations, traffic from different VLANs can be transmitted on
different links for load balancing in the single-ring network. 

#### Link down alarm mechanism

In an RRPP domain, when the transit node,
edge node, or assistant edge node finds that any of its ports is down, it
immediately sends Link-Down packets to the master node. When the master node receives
a Link-Down packet, it takes the following actions: 

·     Releases the secondary port from blocking protected
VLANs. 

·     Sends Common-Flush-FDB packets to instruct all
the transit nodes, edge nodes, and assistant edge nodes to update their MAC
address entries and ARP/ND entries. 

After each node updates its own entries, traffic
is switched to the normal link. 

#### Ring recovery

When the ports in an RRPP domain on the
transit nodes, edge nodes, or assistant edge nodes come up again, the ring is
recovered. However, the master node might detect the ring recovery after a
period of time. A temporary loop might arise in the protected VLAN during this
period. As a result, a broadcast storm occurs.

To prevent such cases, non-master nodes
block the ports immediately when they find the ports accessing the ring are
brought up again. The nodes block only the packets from the protected VLAN, and
they permit only the packets from the control VLAN to pass through. The blocked
ports are activated only when the nodes determine that no loop will be
generated by these ports. 

#### Broadcast storm suppression mechanism in case of SRPT failure in a multi-homed subring

As shown in [Figure 5](#_Ref267556394), Ring 1 is the primary ring, and Ring 2 and Ring 3 are subrings.
When the two SRPTs between the edge node and the assistant edge node are down,
the master nodes of Ring 2 and Ring 3 will open their secondary ports. A loop is
generated among Device B, Device C, Device E, and Device F, causing a broadcast
storm. 

To avoid generating a loop, the edge node
will temporarily block the edge port. The blocked edge port is activated only
when the edge node determines that no loop will be generated when the edge port
is activated. 

#### RRPP ring group

In an edge node RRPP ring group, only the activated
subring with the smallest domain ID and ring ID can send Edge-Hello packets. In
an assistant edge node RRPP ring group, any activated subring that has received
Edge-Hello packets will forward these packets to the other activated subrings. When
an edge node RRPP ring group and an assistant edge node RRPP ring group are configured,
the CPU workload is reduced because of the following reasons: 

·     Only one subring sends Edge-Hello packets on the
edge node.

·     Only one subring receives Edge-Hello packets on
the assistant edge node. 

As shown in [Figure 5](#_Ref212278384), Device B is the edge node of Ring 2 and Ring 3\. Device C is the assistant
edge node of Ring 2 and Ring 3\. Device B and Device C need to send or receive
Edge-Hello packets frequently. If more subrings are configured or more domains
are configured for load balancing, Device B and Device C will send or receive a
large number of Edge-Hello packets. 

To reduce Edge-Hello traffic, perform the
following tasks:

·     Assign Ring 2 and Ring 3 to an RRPP ring group
configured on the edge node Device B.

·     Assign Ring 2 and Ring 3 to an RRPP ring group
configured on the assistant edge node Device C. 

If all rings are activated, only Ring 2 on
Device B sends Edge-Hello packets. 

#### Fast detection mechanism

Ideally, an RRPP ring can fast converge
because its transit nodes can detect link failures quickly and send out
notifications immediately. In practice, some devices on an RRPP ring might not
support RRPP. RRPP can detect link failures between these devices only through
the timeout mechanism. This results in long-time traffic interruption and failure
to implement millisecond-level convergence. 

To address this problem, a fast detection
mechanism was introduced. The mechanism works as follows:

·     The master node sends Fast-Hello packets out of its
primary port at the interval specified by the Fast-Hello timer. If the
secondary port receives the Fast-Hello packets sent by the local master node
before the Fast-Fail timer expires, the entire ring is in Health state. Otherwise,
the ring transits to Disconnect state. 

·     The edge node sends Fast-Edge-Hello packets out of
its common ports at the interval specified by the timer resolution. If the assistant
edge node fails to receive the Fast-Edge-Hello packets within three times the timer
resolution, the SRPTs transit to Disconnect state. 

As shown in [Figure 2](#_Ref215992975), when
fast detection is enabled for RRPP domain 1, Device A (master node of Ring 1\)
sends Fast-Hello packets periodically. It determines the ring status according
to whether Fast-Hello packets are received before the Fast-Fail timer expires,
implementing link status fast detection. 

To implement fast detection on an RRPP
ring, enable fast detection on the master node, edge node, and assistant edge
node of the RRPP ring. 

 

### Typical RRPP networking

#### Single ring

As shown in [Figure 2](#_Ref212278244), only
a single ring exists in the network topology. You need only define an RRPP
domain. 

Figure 2 Schematic
diagram for a single-ring network

![](https://resource.h3c.com/en/202407/12/20240712_11705568_x_Img_x_png_1_2216097_294551_0.png)

 

#### Tangent rings

As shown in [Figure 3](#_Ref212278289), two
or more rings exist in the network topology and only one common node exists between
rings. You must define an RRPP domain for each ring. 

Figure 3 Schematic
diagram for a tangent-ring network

![](https://resource.h3c.com/en/202407/12/20240712_11705571_x_Img_x_png_2_2216097_294551_0.png)

 

#### Intersecting rings

As shown in [Figure 4](#_Ref212278321), two
or more rings exist in the network topology and two common nodes exist between
rings. You need only define an RRPP domain and configure one ring as the
primary ring and the other rings as subrings.

Figure
4 Schematic diagram for an intersecting-ring
network

![](https://resource.h3c.com/en/202407/12/20240712_11705572_x_Img_x_png_3_2216097_294551_0.png)

 

#### Dual-homed rings

As shown in[Figure 5](#_Ref212278384), two
or more rings exist in the network topology and two similar common nodes exist between
rings. You need only define an RRPP domain and configure one ring as the
primary ring and the other rings as subrings.

Figure 5 Schematic
diagram for a dual-homed-ring network

![](https://resource.h3c.com/en/202407/12/20240712_11705573_x_Img_x_png_4_2216097_294551_0.png)

 

#### Single-ring load balancing

In a single-ring
network, you can achieve load balancing by configuring multiple domains.

As shown in [Figure 6](#_Ref212280450):

·     Ring 1 is configured as the primary ring of both
Domain 1 and Domain 2\. 

·     Domain 1 and Domain 2 are configured with
different protected VLANs. 

·     In Domain 1, Device A is configured as the
master node of Ring 1\. 

·     In Domain 2, Device B is configured as the
master node of Ring 1\. 

Such configurations enable the ring to
block different links based on VLANs and achieve single-ring load balancing. 

Figure
6 Schematic diagram for a single-ring load balancing
network

![](https://resource.h3c.com/en/202407/12/20240712_11705574_x_Img_x_png_5_2216097_294551_0.png)

 

#### Intersecting-ring load balancing

In an
intersecting-ring network, you can also achieve load balancing by configuring
multiple domains.

As shown in [Figure 7](#_Ref212280467): 

·     Ring 1 is the primary ring and Ring 2 is the
subring in both Domain 1 and Domain 2\. 

·     Domain 1 and Domain 2 are configured with
different protected VLANs. 

·     Device A is configured as the master node of
Ring 1 in Domain 1\. 

·     Device D is configured as the master node of
Ring 1 in Domain 2\. 

·     Device E is configured as the master node of
Ring 2 in both Domain 1 and Domain 2\. However, different ports on Device E are blocked
in Domain 1 and Domain 2\. 

Traffic from different VLANs can travel over
different paths in the subring and primary ring.

Figure
7 Schematic diagram for an intersecting-ring load balancing
network

![](https://resource.h3c.com/en/202407/12/20240712_11705575_x_Img_x_png_6_2216097_294551_0.png)

 

#### RRPP with M-LAG

M-LAG allows you to virtualize the devices in
an RRPP ring into one system through multichassis link, thereby achieving device-level
redundancy and load balancing. 

As shown in [Figure 8](#_Ref54016441), the
M-LAG system composed of Device A and Device E is the master node of Ring 1,
and the M-LAG system composed of Device B and Device F is the transit node of
Ring 1\. For more information about M-LAG, see Layer
2—LAN Switching Configuration Guide.

Figure 8 RRPP withM-LAG

![](https://resource.h3c.com/en/202407/12/20240712_11705576_x_Img_x_png_7_2216097_294551_0.png)

‌

### Protocols and standards

RFC 3619, Extreme
Networks' Ethernet Automatic Protection Switching (EAPS) Version 1 

## Restrictions and guidelines: RRPP configuration

·     RRPP does not have an auto election mechanism. You
must configure each node in the ring network correctly for RRPP to monitor and
protect the ring network. 

·     You can configure RRPP in the following order:

¡     Create
RRPP domains based on service planning.

¡     Specify
control VLANs and protected VLANs for each RRPP domain. 

¡     Determine
the ring roles and node roles based on the traffic paths in each RRPP domain. 

## RRPP tasks at a glance

To configure RRPP, perform the following tasks:

**1\.**[Creating an RRPP
domain](#_Ref326592922)

Perform this task on devices you want to
configure as nodes in the RRPP domain.

**2\.**[Configuring control
VLANs](#_Ref326592945)

Perform this task on all nodes in the
RRPP domain.

**3\.**[Configuring
protected VLANs](#_Ref326592927)

Perform this task on all nodes in the
RRPP domain.

**4\.**[Configuring RRPP
rings](#_Ref326593033)

**a.**[Configuring RRPP ports](#_Ref326593035)

Perform this task on each node's ports
intended for accessing RRPP rings.

**b.**[Configuring RRPP
nodes](#_Ref326593038)

Perform this task on all nodes in the
RRPP domain.

**5\.**[Activating an RRPP
domain](#_Ref326593076)

Perform this task on all nodes in the
RRPP domain.

**6\.**(Optional.) [Configuring RRPP timers](#_Ref326593081)

Perform this task on the master node in
the RRPP domain.

¡     [Configuring the Hello timer and Fail timer](#_Ref515447698)

¡     [Configuring the link-up delay timer](#_Ref515447710)

**7\.**(Optional.) [Configuring RRPP fast detection](#_Ref326593084)

**a.**[Enabling fast
detection](#_Ref326593088)

Perform this task on the master node,
edge node, and assistant edge node in the RRPP domain.

**b.**[Configuring fast
detection timers](#_Ref326593092)

Perform this task on the master node in
the RRPP domain.

**8\.**(Optional.) [Configuring an RRPP ring group](#_Ref326593095)

Perform this task on the edge node and
assistant edge node in the RRPP domain.

**9\.**(Optional.) [Enabling SNMP notifications for RRPP](#_Ref447266694)

## Prerequisites for RRPP

Before you configure RRPP, you must
physically construct a ring-shaped Ethernet topology. 

## Creating an RRPP domain

#### About this task

When you create an RRPP domain, specify a
domain ID to uniquely identify the RRPP domain. All devices in the same RRPP
domain must be configured with the same domain ID. 

#### Restrictions and guidelines

Perform this task on devices you want to
configure as nodes in the RRPP domain.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Create an RRPP domain and enter RRPP domain
view.

rrpp domain domain-id

## Configuring control VLANs

#### Restrictions and guidelines

·     Perform this task on all nodes in the RRPP
domain.

·     Before you configure RRPP rings in an RRPP
domain, configure the same control VLANs for all nodes in the RRPP domain first.
You need only configure the primary control VLAN for an RRPP domain. The system
automatically configures the secondary control VLAN. It uses the primary
control VLAN ID plus 1 as the secondary control VLAN ID. For the control VLAN
configuration to succeed, make sure the IDs of the two control VLANs are
consecutive and have not been previously assigned.

·     Do not configure the default VLAN of a port
accessing an RRPP ring as the control VLAN. 

·     The primary and secondary control VLAN IDs must
be different from the Layer 3 Ethernet subinterface IDs of the master ring and
subrings.

·     For RRPPDUs to be correctly forwarded, do not
enable QinQ or VLAN mapping on control VLANs. 

·     After you configure RRPP rings for an RRPP
domain, you cannot delete or modify the primary control VLAN of the domain. You
can only use the undo control-vlan command
to delete a primary control VLAN.

·     To transparently transmit RRPPDUs on a device
not configured with RRPP, make sure only the two ports accessing the RRPP ring
permit packets from the control VLANs. Otherwise, the packets from other VLANs might
enter the control VLANs in transparent transmission mode and strike the RRPP
ring.

·     When configuring RRPP with M-LAG, make sure the peer-link interface is a trunk port. The peer-link interface
will automatically join or leave a control VLAN as the control
VLAN is created or deleted.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter RRPP domain view.

rrpp domain domain-id

**3\.**Configure the primary control VLAN for the
RRPP domain.

control-vlan vlan-id

## Configuring protected VLANs

#### Restrictions and guidelines

·     Perform this task on all nodes in the RRPP
domain.

·     Before you configure RRPP rings in an RRPP
domain, configure the same protected VLANs for all nodes in the RRPP domain. All
VLANs to which the RRPP ports are assigned must be protected by the RRPP
domains. 

·     When you configure load balancing, you must
configure different protected VLANs for different RRPP domains.

#### Prerequisites

Before you configure protected VLANs, you
must configure an MST region and the VLAN-to-instance mapping table. For more
information about MST regions, see spanning tree configuration in Layer 2—LAN Switching Configuration Guide.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter RRPP domain view.

rrpp domain domain-id

**3\.**Configure protected VLANs for the RRPP
domain.

protected-vlan reference-instance
instance-id-list

## Configuring RRPP rings

When you configure an RRPP ring, you must configure
the ports connecting each node to the RRPP ring before configuring the nodes. 

### Prerequisites

Before you configure an RRPP ring, you must
configure control VLANs and protected VLANs. 

### Configuring RRPP ports

#### Restrictions and guidelines

·     Perform this task on each node's ports intended
for accessing RRPP rings.

·     Do not enable the OAM remote loopback function
on an RRPP port. Otherwise, temporary broadcast storms might occur.

·     To accelerate topology convergence, use the link-delay command to enable link status rapid report function on an RRPP port.
Use this command to set the physical state change suppression interval to 0
seconds. For more information about the link-delay
command, see Layer 2—LAN Switching Command Reference.


·     Do not assign a port to both an aggregation
group and an RRPP ring. If you do so, the port does not take effect on the RRPP
ring.

·     Do not assign a peer-link interface to an RRPP ring. A peer-link interface does not work for an RRPP
ring.

·     Do not assign both an M-LAG interface and a non-M-LAG
interface to an RRPP ring. If you do so, the M-LAG interface does not take effect on the RRPP
ring.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter Layer 2 Ethernet interface view or Layer
2 aggregate interface view.

interface interface-type interface-number

**3\.**Configure the link type of the interface as
trunk.

port link-type trunk

By default, the link type of an interface
is access.

For more information about the command,
see Layer 2—LAN Switching Command Reference.

**4\.**Assign the trunk port to the protected VLANs
of the RRPP domain.

port trunk permit vlan { vlan-id-list \| all }

By default, a trunk port allows only
packets from VLAN 1 to pass through.

RRPP ports always allow packets from the
control VLANs to pass through.

For more information about the command,
see Layer 2—LAN Switching Command Reference.

**5\.**Disable the spanning tree feature.

undo stp enable

By default, the spanning tree feature is
enabled.

For more information about the command,
see Layer 2—LAN Switching Command Reference.

### Configuring RRPP nodes

#### Restrictions and guidelines

·     Perform this task on all nodes in the RRPP
domain.

·     If a device carries multiple RRPP rings in an
RRPP domain, it can only be an edge node or an assistant edge node on a subring.


·     When you configure an edge node or an assistant
edge node, you must configure the primary ring before configuring the subrings.

#### Specifying a master node

**1\.**Enter system view.

system-view

**2\.**Enter RRPP domain view.

rrpp domain domain-id

**3\.**Specify the current device as the master
node of the ring, and specify the primary port and the secondary port.

ring ring-id node-mode master \[ primary-port
interface-type
interface-number ] \[ secondary-port interface-type
interface-number ] level level-value

#### Specifying a transit node

**1\.**Enter system view.

system-view

**2\.**Enter RRPP domain view.

rrpp domain domain-id

**3\.**Specify the current device as a transit node
of the ring, and specify the primary port and the secondary port.

ring ring-id node-mode transit \[ primary-port
interface-type
interface-number ] \[ secondary-port interface-type
interface-number ] level level-value

#### Specifying an edge node

**1\.**Enter system view.

system-view

**2\.**Enter RRPP domain view.

rrpp domain domain-id

**3\.**Specify the current device as a master node
or transit node of the primary ring, and specify the primary port and the
secondary port.

ring ring-id node-mode { master \| transit } \[ primary-port
interface-type
interface-number ] \[ secondary-port interface-type
interface-number ] level level-value

**4\.**Specify the current device as the edge node
of a subring, and specify the edge port.

ring ring-id node-mode edge \[ edge-port interface-type
interface-number ]

#### Specifying an assistant edge node

**1\.**Enter system view.

system-view

**2\.**Enter RRPP domain view.

rrpp domain domain-id

**3\.**Specify the current device as a master node
or transit node of the primary ring, and specify the primary port and the
secondary port.

ring ring-id node-mode { master \| transit } \[ primary-port
interface-type
interface-number ] \[ secondary-port interface-type
interface-number ] level level-value

By default, a device is not a node of the
RRPP ring.

**4\.**Specify the current device as the assistant
edge node of the subring, and specify an edge port.

ring ring-id node-mode assistant-edge \[ edge-port interface-type
interface-number ]

## Activating an RRPP domain

#### Restrictions and guidelines

·     Perform this task on all nodes in the RRPP
domain.

·     Before you activate an RRPP domain on the
current device, enable the RRPP protocol and RRPP rings for the RRPP domain on
the current device. 

·     Before you enable subrings on a device, you must
enable the primary ring. Before you disable the primary ring on the device, you
must disable all subrings. Otherwise, the system displays error prompts.

·     To prevent Hello packets of subrings from being
looped on the primary ring, enable the primary ring on its master node first. Then
enable the subrings on their respective master nodes. 

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enable RRPP.

rrpp enable

By default, RRPP is disabled.

**3\.**Enter RRPP domain view.

rrpp domain domain-id

**4\.**Enable the specified RRPP ring.

ring ring-id enable

By default, an RRPP ring is disabled.

## Configuring RRPP timers

### Restrictions and guidelines for RRPP timer configuration

Perform this task on the master node of an
RRPP domain.

### Configuring the Hello timer and Fail timer

#### Restrictions and guidelines

·     The Fail timer must be greater than or equal to
three times the Hello timer.

·     In a dual-homed-ring network, to avoid temporary
loops when the primary ring fails, make sure the value of A is greater than the value of B,
where:

¡     A is the difference
between the Fail timer values on the master node of the subring and the master
node of the primary ring.

¡     B is twice the
Hello timer value on the master node of the subring. 

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter RRPP domain view.

rrpp domain domain-id

**3\.**Set the Hello timer and Fail timer for the
RRPP domain.

timer hello-timer hello-value fail-timer fail-value

By default, the Hello timer value is 1
second and the Fail timer value is 3 seconds.

### Configuring the link-up delay timer

#### Restrictions and guidelines

If the distribute
keyword is not specified, the link-up delay timer value cannot be greater than
the Fail timer value minus twice the Hello timer value.

If you specify the distribute keyword in an RRPP network implementing load balancing, you must
configure the link-up delay timer for each RRPP domain for the timer to take
effect. If you set different timer values for different RRPP domains, the
smallest timer value takes effect.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter RRPP domain view.

rrpp domain domain-id

**3\.**Set the link-up delay timer for the RRPP
domain.

linkup-delay-timer delay-time \[ distribute ]

By default, the link-up delay timer value
is 0 seconds, and the distribute keyword is
not specified.

## Configuring RRPP fast detection

### Enabling fast detection

#### Restrictions and guidelines

·     Perform this task on the master node, edge node,
and assistant edge node in the RRPP domain.

·     As a best practice, configure fast detection on
the master node of a subring only when the edge node and assistant edge node of
the subring support fast detection. 

·     When you configure fast detection in an RRPP
domain, enable fast detection first on the edge node, and then on the assistant
edge node. Otherwise, the assistant edge node might fail to receive Fast-Edge-Hello
packets and consider the master node faulty even if it is not.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter RRPP domain view.

rrpp domain domain-id

**3\.**Enable fast detection.

fast-detection enable

By default, fast detection is disabled.

### Configuring fast detection timers

#### Restrictions and guidelines

·     Perform this task on the master node in the RRPP
domain.

·     The Fast-Fail timer must be equal to or greater
than three times the Fast-Hello timer. 

·     When you configure the Fast-Hello timer, take
the device capability into consideration. A small Fast-Hello timer value might
cause the link to be unstable. 

·     In a dual-homed-ring network, to avoid temporary
loops when the primary ring fails, make sure the following requirements are
met: 

¡     The Fast-Fail
timer value on the master node of the subring is equal to or greater than six
times the timer resolution. 

¡     The
difference between the Fast-Fail timer values on the master node of the subring
and the master node of the primary ring is greater than twice the Fast-Hello
timer value on the master node of the subring. 

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter RRPP domain view.

rrpp domain domain-id

**3\.**Set the Fast-Hello timer and Fast-Fail
timer.

fast-timer hello-timer hello-value fail-timer fail-value

By default, the Fast-Hello timer is 20
milliseconds, and the Fast-Fail timer is 60 milliseconds.

**4\.**Set the Fast-Edge-Hello timer and
Fast-Edge-Fail timer.

fast-edge-timer hello-timer hello-value fail-timer fail-value

By default, the Fast-Edge-Hello timer is
10 milliseconds, and the Fast-Edge-Fail timer is 30 milliseconds.

## Configuring an RRPP ring group

#### About this task

To reduce Edge-Hello traffic, assign
subrings with the same edge node and assistant edge node to an RRPP ring group.
An RRPP ring group must be configured on both the edge node and the assistant
edge node. It can only be configured on these two types of nodes. 

#### Restrictions and guidelines

·     Perform this task on the edge node and assistant
edge node in the RRPP domain.

·     You can assign a subring to only one RRPP ring
group. The RRPP ring groups configured on the edge node and the assistant edge
node must contain the same subrings. Otherwise, the RRPP ring group cannot
operate correctly. 

·     The subrings in an RRPP ring group must share
the same edge node and assistant edge node. The edge node and the assistant
edge node must have the same SRPTs. 

·     Make sure a device is either the edge node or
the assistant edge node on the subrings in an RRPP ring group. 

·     Make sure the RRPP ring groups on the edge node
and the assistant edge node have the same configurations and activation status.


·     Make sure all subrings in an RRPP ring group
have the same SRPTs. If the SRPTs of these subrings are different, the RRPP
ring group cannot operate correctly. 

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Create an RRPP ring group and enter RRPP
ring group view.

rrpp ring-group ring-group-id

By default, no RRPP ring groups exist.

**3\.**Assign the specified subrings to the RRPP
ring group.

domain domain-id ring ring-id-list

By default, no subrings are assigned to
an RRPP ring group.

## Enabling SNMP notifications for RRPP

#### About this task

To report critical RRPP events to an NMS,
enable SNMP notifications for RRPP. For RRPP event notifications to be sent
correctly, you must also configure SNMP on the device. For more information
about SNMP configuration, see the network management and monitoring
configuration guide for the device.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enable SNMP notifications for RRPP.

snmp-agent trap enable rrpp \[ major-fault \| multi-master \| ring-fail \| ring-recover ] \*

By default, SNMP notifications for RRPP
are disabled.

## Display and maintenance commands for RRPP

Execute display
commands in any view and reset commands in user
view.

 

| Task | Command || Display brief RRPP information. | display rrpp brief || Display RRPP group configuration information. | display rrpp ring-group \[ ring-group-id ] || Display RRPPDU statistics. | display rrpp statistics domain domain-id \[ ring ring-id ] || Display detailed RRPP information. | display rrpp verbose domain domain-id \[ ring ring-id ] || Clear RRPPDU statistics. | reset rrpp statistics domain domain-id \[ ring ring-id ] |





| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |

 

## RRPP configuration examples

### Example: Configuring a single ring

#### Network configuration

As shown in [Figure 9](#_Ref266189578): 

·     Device A, Device B, Device C, and Device D form RRPP
domain 1\. Specify the primary control VLAN of RRPP domain 1 as VLAN 4092\. Specify
the protected VLANs of RRPP domain 1 as VLANs 1 through 30\. 

·     Device A, Device B, Device C, and Device D form primary
ring 1\.

·     Specify Device A as the master node of primary
ring 1, Ten-GigabitEthernet 1/0/1 as the primary port, and Ten-GigabitEthernet 1/0/2 as the secondary
port.

·     Specify Device B, Device C, and Device D as the
transit nodes of primary ring 1\. Specify Ten-GigabitEthernet 1/0/1 as the primary
port and Ten-GigabitEthernet 1/0/2 as the secondary port on Device B, Device C, and Device D.

Figure
9 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705577_x_Img_x_png_8_2216097_294551_0.png)

‌

#### Procedure

**1\.**Configure Device A:

\# Create VLANs 1 through 30\. 

\<DeviceA\> system-view

\[DeviceA] vlan 1 to 30

\# Map these VLANs to MSTI 1\.

\[DeviceA] stp
region-configuration

\[DeviceA-mst-region] instance
1 vlan 1 to 30

\# Activate the MST region configuration.

\[DeviceA-mst-region] active
region-configuration

\[DeviceA-mst-region] quit

\# Set the physical state change
suppression interval to 0 seconds on Ten-GigabitEthernet 1/0/1. 

\[DeviceA] interface ten-gigabitethernet 1/0/1

\[DeviceA-Ten-GigabitEthernet1/0/1] link-delay up 0

\[DeviceA-Ten-GigabitEthernet1/0/1] link-delay down 0

\# Disable the spanning tree feature on
the port. 

\[DeviceA-Ten-GigabitEthernet1/0/1] undo stp enable

\# Configure the port as a trunk port. 

\[DeviceA-Ten-GigabitEthernet1/0/1] port link-type
trunk

\# Assign the port to VLANs 1 through 30\.

\[DeviceA-Ten-GigabitEthernet1/0/1] port trunk permit
vlan 1 to 30

\[DeviceA-Ten-GigabitEthernet1/0/1] quit

\# Configure Ten-GigabitEthernet 1/0/2 in the same way Ten-GigabitEthernet 1/0/1 is configured.

\[DeviceA] interface ten-gigabitethernet 1/0/2

\[DeviceA-Ten-GigabitEthernet1/0/2] link-delay up 0

\[DeviceA-Ten-GigabitEthernet1/0/2] link-delay down 0

\[DeviceA-Ten-GigabitEthernet1/0/2] undo stp enable

\[DeviceA-Ten-GigabitEthernet1/0/2] port link-type
trunk

\[DeviceA-Ten-GigabitEthernet1/0/2] port trunk permit
vlan 1 to 30

\[DeviceA-Ten-GigabitEthernet1/0/2] quit

\# Create RRPP domain 1\.

\[DeviceA] rrpp domain 1

\# Configure VLAN 4092 as the primary
control VLAN of RRPP domain 1\. 

\[DeviceA-rrpp-domain1]
control-vlan 4092

\# Configure the VLANs mapped to MSTI 1 as
the protected VLANs of RRPP domain 1\. 

\[DeviceA-rrpp-domain1]
protected-vlan reference-instance 1

\# Configure Device A as the master node
of primary ring 1, with Ten-GigabitEthernet 1/0/1 as the primary
port and Ten-GigabitEthernet 1/0/2 as the secondary port. Enable ring 1\. 

\[DeviceA-rrpp-domain1] ring 1
node-mode master primary-port ten-gigabitethernet 1/0/1 secondary-port ten-gigabitethernet 1/0/2 level 0

\[DeviceA-rrpp-domain1] ring 1
enable

\[DeviceA-rrpp-domain1] quit

\# Enable RRPP. 

\[DeviceA] rrpp enable

**2\.**Configure Device B:

\# Create VLANs 1 through 30\.

\<DeviceB\> system-view

\[DeviceB] vlan 1 to 30

\# Map these VLANs to MSTI 1\.

\[DeviceB] stp
region-configuration

\[DeviceB-mst-region] instance
1 vlan 1 to 30

\# Activate the MST region configuration.

\[DeviceB-mst-region] active region-configuration

\[DeviceB-mst-region] quit

\# Set the physical state change
suppression interval to 0 seconds on Ten-GigabitEthernet 1/0/1. 

\[DeviceB] interface ten-gigabitethernet 1/0/1

\[DeviceB-Ten-GigabitEthernet1/0/1] link-delay up 0

\[DeviceB-Ten-GigabitEthernet1/0/1] link-delay down 0

\# Disable the spanning tree feature on
the port. 

\[DeviceB-Ten-GigabitEthernet1/0/1] undo stp enable

\# Configure the port as a trunk port. 

\[DeviceB-Ten-GigabitEthernet1/0/1] port link-type
trunk

\# Assign the port to VLANs 1 through 30\.

\[DeviceB-Ten-GigabitEthernet1/0/1] port trunk permit
vlan 1 to 30

\[DeviceB-Ten-GigabitEthernet1/0/1] quit

\# Configure Ten-GigabitEthernet 1/0/2 in the same way Ten-GigabitEthernet 1/0/1 is configured.

\[DeviceB] interface ten-gigabitethernet 1/0/2

\[DeviceB-Ten-GigabitEthernet1/0/2] link-delay up 0

\[DeviceB-Ten-GigabitEthernet1/0/2] link-delay down 0

\[DeviceB-Ten-GigabitEthernet1/0/2] undo stp enable

\[DeviceB-Ten-GigabitEthernet1/0/2] port link-type
trunk

\[DeviceB-Ten-GigabitEthernet1/0/2] port trunk permit
vlan 1 to 30

\[DeviceB-Ten-GigabitEthernet1/0/2] quit

\# Create RRPP domain 1\. 

\[DeviceB] rrpp domain 1

\# Configure VLAN 4092 as the primary
control VLAN of RRPP domain 1\. 

\[DeviceB-rrpp-domain1]
control-vlan 4092

\# Configure the VLANs mapped to MSTI 1 as
the protected VLANs of RRPP domain 1\. 

\[DeviceB-rrpp-domain1]
protected-vlan reference-instance 1

\# Configure Device B as the transit node
of primary ring 1, with Ten-GigabitEthernet 1/0/1 as the primary
port and Ten-GigabitEthernet 1/0/2 as the secondary port. Enable ring 1\. 

\[DeviceB-rrpp-domain1] ring 1
node-mode transit primary-port ten-gigabitethernet 1/0/1 secondary-port ten-gigabitethernet 1/0/2 level 0

\[DeviceB-rrpp-domain1] ring 1
enable

\[DeviceB-rrpp-domain1] quit

\# Enable RRPP. 

\[DeviceB] rrpp enable

**3\.**Configure Device C:

Configure Device C in the same way Device
B is configured. 

**4\.**Configure Device D:

Configure Device D in the same way Device
B is configured. 

#### Verifying the configuration

\# Use the display commands to view RRPP configuration and operational information on
each device.

### Example: Configuring intersecting rings

#### Network configuration

As shown in [Figure 10](#_Ref212285565): 

·     Device A, Device B, Device C, Device D, and
Device E form RRPP domain 1\. VLAN 4092 is the primary control VLAN of RRPP
domain 1\. RRPP domain 1 protects VLANs 1 through 30\.

·     Device A, Device B, Device C, and Device D form primary
ring 1\. Device B, Device C, and Device E form subring 2\.

·     Device A is the master node of primary ring 1, with
Ten-GigabitEthernet 1/0/1 as the
primary port and Ten-GigabitEthernet 1/0/2 the secondary port.

·     Device E is the master node of subring 2, with Ten-GigabitEthernet 1/0/1 as the primary port
and Ten-GigabitEthernet 1/0/2
the secondary port.

·     Device B is the transit node of primary ring 1
and the edge node of subring 2, with Ten-GigabitEthernet 1/0/3 as the edge port. 

·     Device C is the transit node of primary ring 1
and the assistant edge node of subring 1, with Ten-GigabitEthernet 1/0/3 as the edge port.

·     Device D is the transit node of primary ring 1, with
Ten-GigabitEthernet 1/0/1 as the
primary port and Ten-GigabitEthernet 1/0/2 the secondary port.


Figure
10 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705578_x_Img_x_png_9_2216097_294551_0.png)

‌

#### Procedure

**1\.**Configure Device A:

\# Create VLANs 1 through 30\.

\<DeviceA\> system-view

\[DeviceA] vlan 1 to 30

\# Map these VLANs to MSTI 1\.

\[DeviceA] stp
region-configuration

\[DeviceA-mst-region] instance
1 vlan 1 to 30

\# Activate the MST region configuration.

\[DeviceA-mst-region] active
region-configuration

\[DeviceA-mst-region] quit

\# Set the physical state change suppression
interval to 0 seconds on Ten-GigabitEthernet 1/0/1.

\[DeviceA] interface ten-gigabitethernet 1/0/1

\[DeviceA-Ten-GigabitEthernet1/0/1] link-delay up 0

\[DeviceA-Ten-GigabitEthernet1/0/1] link-delay down 0

\# Disable the spanning tree feature on
the port. 

\[DeviceA-Ten-GigabitEthernet1/0/1] undo stp enable

\# Configure the port as a trunk port. 

\[DeviceA-Ten-GigabitEthernet1/0/1] port link-type
trunk

\# Assign the port to VLANs 1 through 30\.

\[DeviceA-Ten-GigabitEthernet1/0/1] port trunk permit
vlan 1 to 30

\[DeviceA-Ten-GigabitEthernet1/0/1] quit

\# Configure Ten-GigabitEthernet 1/0/2 in the same way Ten-GigabitEthernet 1/0/1 is configured.

\[DeviceA] interface ten-gigabitethernet 1/0/2

\[DeviceA-Ten-GigabitEthernet1/0/2] link-delay up 0

\[DeviceA-Ten-GigabitEthernet1/0/2] link-delay down 0

\[DeviceA-Ten-GigabitEthernet1/0/2] undo stp enable

\[DeviceA-Ten-GigabitEthernet1/0/2] port link-type
trunk

\[DeviceA-Ten-GigabitEthernet1/0/2] port trunk permit
vlan 1 to 30

\[DeviceA-Ten-GigabitEthernet1/0/2] quit

\# Create RRPP domain 1\. 

\[DeviceA] rrpp domain 1 

\# Configure VLAN 4092 as the primary
control VLAN of RRPP domain 1\. 

\[DeviceA-rrpp-domain1]
control-vlan 4092

\# Configure the VLANs mapped to MSTI 1 as
the protected VLANs of RRPP domain 1\. 

\[DeviceA-rrpp-domain1]
protected-vlan reference-instance 1

\# Configure Device A as the master node
of primary ring 1, with Ten-GigabitEthernet 1/0/1 as the primary
port and Ten-GigabitEthernet 1/0/2 as the secondary port. Enable ring 1\. 

\[DeviceA-rrpp-domain1] ring 1
node-mode master primary-port ten-gigabitethernet 1/0/1 secondary-port ten-gigabitethernet 1/0/2 level 0

\[DeviceA-rrpp-domain1] ring 1
enable

\[DeviceA-rrpp-domain1] quit

\# Enable RRPP. 

\[DeviceA] rrpp enable

**2\.**Configure Device B:

\# Create VLANs 1 through 30\.

\<DeviceB\> system-view

\[DeviceB] vlan 1 to 30

\# Map these VLANs to MSTI 1\.

\[DeviceB] stp
region-configuration

\[DeviceB-mst-region] instance
1 vlan 1 to 30

\# Activate the MST region configuration.

\[DeviceB-mst-region] active
region-configuration

\[DeviceB-mst-region] quit

\# Set the physical state change suppression
interval to 0 seconds on Ten-GigabitEthernet 1/0/1.

\[DeviceB] interface ten-gigabitethernet 1/0/1

\[DeviceB-Ten-GigabitEthernet1/0/1] link-delay up 0

\[DeviceB-Ten-GigabitEthernet1/0/1] link-delay down 0

\# Disable the spanning tree feature on
the port. 

\[DeviceB-Ten-GigabitEthernet1/0/1] undo stp enable

\# Configure the port as a trunk port. 

\[DeviceB-Ten-GigabitEthernet1/0/1] port link-type
trunk

\# Assign the port to VLANs 1 through 30\.

\[DeviceB-Ten-GigabitEthernet1/0/1] port trunk permit
vlan 1 to 30

\[DeviceB-Ten-GigabitEthernet1/0/1] quit

\# Configure Ten-GigabitEthernet 1/0/2 in the same way Ten-GigabitEthernet 1/0/1 is configured.

\[DeviceB] interface ten-gigabitethernet 1/0/2

\[DeviceB-Ten-GigabitEthernet1/0/2] link-delay up 0

\[DeviceB-Ten-GigabitEthernet1/0/2] link-delay down 0

\[DeviceB-Ten-GigabitEthernet1/0/2] undo stp enable

\[DeviceB-Ten-GigabitEthernet1/0/2] port link-type
trunk

\[DeviceB-Ten-GigabitEthernet1/0/2] port trunk permit
vlan 1 to 30

\[DeviceB-Ten-GigabitEthernet1/0/2] quit

\# Configure Ten-GigabitEthernet 1/0/3 in the same way Ten-GigabitEthernet 1/0/1 is configured.

\[DeviceB] interface ten-gigabitethernet 1/0/3

\[DeviceB-Ten-GigabitEthernet1/0/3] link-delay up 0

\[DeviceB-Ten-GigabitEthernet1/0/3] undo stp enable

\[DeviceB-Ten-GigabitEthernet1/0/3] port link-type
trunk

\[DeviceB-Ten-GigabitEthernet1/0/3] port trunk permit
vlan 1 to 30

\[DeviceB-Ten-GigabitEthernet1/0/3] quit

\# Create RRPP domain 1\. 

\[DeviceB] rrpp domain 1

\# Configure VLAN 4092 as the primary
control VLAN of RRPP domain 1\. 

\[DeviceB-rrpp-domain1]
control-vlan 4092

\# Configure the VLANs mapped to MSTI 1 as
the protected VLANs of RRPP domain 1\. 

\[DeviceB-rrpp-domain1]
protected-vlan reference-instance 1

\# Configure Device B as a transit node of
primary ring 1, with Ten-GigabitEthernet 1/0/1 as the primary
port and Ten-GigabitEthernet 1/0/2 as the secondary port. Enable ring 1\. 

\[DeviceB-rrpp-domain1] ring 1
node-mode transit primary-port ten-gigabitethernet 1/0/1 secondary-port ten-gigabitethernet 1/0/2 level 0

\[DeviceB-rrpp-domain1] ring 1
enable

\# Configure Device B as the edge node of subring
2, with Ten-GigabitEthernet 1/0/3 as the edge port. Enable ring 2\. 

\[DeviceB-rrpp-domain1] ring 2
node-mode edge edge-port ten-gigabitethernet 1/0/3

\[DeviceB-rrpp-domain1] ring 2
enable

\[DeviceB-rrpp-domain1] quit

\# Enable RRPP. 

\[DeviceB] rrpp enable

**3\.**Configure Device C:

\# Create VLANs 1 through 30\.

\<DeviceC\> system-view

\[DeviceC] vlan 1 to 30

\# Map these VLANs to MSTI 1\.

\[DeviceC] stp
region-configuration

\[DeviceC-mst-region] instance
1 vlan 1 to 30

\# Activate the MST region configuration.

\[DeviceC-mst-region] active
region-configuration

\[DeviceC-mst-region] quit

\# Set the physical state change suppression
interval to 0 seconds on Ten-GigabitEthernet 1/0/1.

\[DeviceC] interface ten-gigabitethernet 1/0/1

\[DeviceC-Ten-GigabitEthernet1/0/1] link-delay up 0

\[DeviceC-Ten-GigabitEthernet1/0/1] link-delay down 0

\# Disable the spanning tree feature on
the port. 

\[DeviceC-Ten-GigabitEthernet1/0/1] undo stp enable

\# Configure the port as a trunk port. 

\[DeviceC-Ten-GigabitEthernet1/0/1] port link-type
trunk

\# Assign the port to VLANs 1 through 30\.

\[DeviceC-Ten-GigabitEthernet1/0/1] port trunk permit
vlan 1 to 30

\[DeviceC-Ten-GigabitEthernet1/0/1] quit

\# Configure Ten-GigabitEthernet 1/0/2 in the same way Ten-GigabitEthernet 1/0/1 is configured.

\[DeviceC] interface ten-gigabitethernet 1/0/2

\[DeviceC-Ten-GigabitEthernet1/0/2] link-delay up 0

\[DeviceC-Ten-GigabitEthernet1/0/2] link-delay down 0

\[DeviceC-Ten-GigabitEthernet1/0/2] undo stp enable

\[DeviceC-Ten-GigabitEthernet1/0/2] port link-type
trunk

\[DeviceC-Ten-GigabitEthernet1/0/2] port trunk permit
vlan 1 to 30

\[DeviceC-Ten-GigabitEthernet1/0/2] quit

\# Configure Ten-GigabitEthernet 1/0/3 in the same way Ten-GigabitEthernet 1/0/1 is configured.

\[DeviceC] interface ten-gigabitethernet 1/0/3

\[DeviceC-Ten-GigabitEthernet1/0/3] link-delay up 0

\[DeviceC-Ten-GigabitEthernet1/0/3] link-delay down 0

\[DeviceC-Ten-GigabitEthernet1/0/3] undo stp enable

\[DeviceC-Ten-GigabitEthernet1/0/3] port link-type
trunk

\[DeviceC-Ten-GigabitEthernet1/0/3] port trunk permit
vlan 1 to 30

\[DeviceC-Ten-GigabitEthernet1/0/3] quit

\# Create RRPP domain 1\. 

\[DeviceC] rrpp domain 1

\# Configure VLAN 4092 as the primary
control VLAN of RRPP domain 1\. 

\[DeviceC-rrpp-domain1]
control-vlan 4092

\# Configure the VLANs mapped to MSTI 1 as
the protected VLANs of RRPP domain 1\. 

\[DeviceC-rrpp-domain1]
protected-vlan reference-instance 1

\# Configure Device C as a transit node of
primary ring 1, with Ten-GigabitEthernet 1/0/1 as the primary
port and Ten-GigabitEthernet 1/0/2 as the secondary port. Enable ring 1\. 

\[DeviceC-rrpp-domain1] ring 1
node-mode transit primary-port ten-gigabitethernet 1/0/1 secondary-port ten-gigabitethernet 1/0/2 level 0

\[DeviceC-rrpp-domain1] ring 1
enable

\# Configure Device C as the assistant
edge node of subring 2, with Ten-GigabitEthernet 1/0/3 as the edge port.
Enable ring 2\. 

\[DeviceC-rrpp-domain1] ring 2
node-mode assistant-edge edge-port ten-gigabitethernet 1/0/3

\[DeviceC-rrpp-domain1] ring 2
enable

\[DeviceC-rrpp-domain1] quit

\# Enable RRPP. 

\[DeviceC] rrpp enable

**4\.**Configure Device D:

\# Create VLANs 1 through 30\.

\<DeviceD\> system-view

\[DeviceD] vlan 1 to 30

\# Map these VLANs to MSTI 1\.

\[DeviceD] stp
region-configuration

\[DeviceD-mst-region] instance
1 vlan 1 to 30

\# Activate the MST region configuration.

\[DeviceD-mst-region] active
region-configuration

\[DeviceD-mst-region] quit

\# Set the physical state change suppression
interval to 0 seconds on Ten-GigabitEthernet 1/0/1.

\[DeviceD] interface ten-gigabitethernet 1/0/1

\[DeviceD-Ten-GigabitEthernet1/0/1] link-delay up 0

\[DeviceD-Ten-GigabitEthernet1/0/1] link-delay down 0

\# Disable the spanning tree feature on
the port. 

\[DeviceD-Ten-GigabitEthernet1/0/1] undo stp enable

\# Configure the port as a trunk port. 

\[DeviceD-Ten-GigabitEthernet1/0/1] port link-type
trunk

\# Assign the port to VLANs 1 through 30\.

\[DeviceD-Ten-GigabitEthernet1/0/1] port trunk permit
vlan 1 to 30

\[DeviceD-Ten-GigabitEthernet1/0/1] quit

\# Configure Ten-GigabitEthernet 1/0/2 in the same way Ten-GigabitEthernet 1/0/1 is configured.

\[DeviceD] interface ten-gigabitethernet 1/0/2

\[DeviceD-Ten-GigabitEthernet1/0/2] link-delay up 0

\[DeviceD-Ten-GigabitEthernet1/0/2] link-delay down 0

\[DeviceD-Ten-GigabitEthernet1/0/2] undo stp enable

\[DeviceD-Ten-GigabitEthernet1/0/2] port link-type
trunk

\[DeviceD-Ten-GigabitEthernet1/0/2] port trunk permit
vlan 1 to 30

\[DeviceD-Ten-GigabitEthernet1/0/2] quit

\# Create RRPP domain 1\. 

\[DeviceD] rrpp domain 1

\# Configure VLAN 4092 as the primary
control VLAN of RRPP domain 1\. 

\[DeviceD-rrpp-domain1]
control-vlan 4092

\# Configure the VLANs mapped to MSTI 1 as
the protected VLANs of RRPP domain 1\. 

\[DeviceD-rrpp-domain1]
protected-vlan reference-instance 1

\# Configure Device D as the transit node
of primary ring 1, with Ten-GigabitEthernet 1/0/1 as the primary
port and Ten-GigabitEthernet 1/0/2 as the secondary port. Enable ring 1\. 

\[DeviceD-rrpp-domain1] ring 1
node-mode transit primary-port ten-gigabitethernet 1/0/1 secondary-port ten-gigabitethernet 1/0/2 level 0

\[DeviceD-rrpp-domain1] ring 1
enable

\[DeviceD-rrpp-domain1] quit

\# Enable RRPP. 

\[DeviceD] rrpp enable

**5\.**Configure Device E:

\# Create VLANs 1 through 30\.

\<DeviceE\> system-view

\[DeviceE] vlan 1 to 30

\# Map these VLANs to MSTI 1\.

\[DeviceE] stp
region-configuration

\[DeviceE-mst-region] instance
1 vlan 1 to 30

\# Activate the MST region configuration.

\[DeviceE-mst-region] active
region-configuration

\[DeviceE-mst-region] quit

\# Set the physical state change suppression
interval to 0 seconds on Ten-GigabitEthernet 1/0/1.

\[DeviceE] interface ten-gigabitethernet 1/0/1

\[DeviceE-Ten-GigabitEthernet1/0/1] link-delay up 0

\[DeviceE-Ten-GigabitEthernet1/0/1] link-delay down 0

\# Disable the spanning tree feature on
the port. 

\[DeviceE-Ten-GigabitEthernet1/0/1] undo stp enable

\# Configure the port as a trunk port. 

\[DeviceE-Ten-GigabitEthernet1/0/1] port link-type
trunk

\# Assign the port to VLANs 1 through 30\.

\[DeviceE-Ten-GigabitEthernet1/0/1] port trunk permit
vlan 1 to 30

\[DeviceE-Ten-GigabitEthernet1/0/1] quit

\# Configure Ten-GigabitEthernet 1/0/2 in the same way Ten-GigabitEthernet 1/0/1 is configured.

\[DeviceE] interface ten-gigabitethernet 1/0/2

\[DeviceE-Ten-GigabitEthernet1/0/2] link-delay up 0

\[DeviceE-Ten-GigabitEthernet1/0/2] link-delay down 0

\[DeviceE-Ten-GigabitEthernet1/0/2] undo stp enable

\[DeviceE-Ten-GigabitEthernet1/0/2] port link-type
trunk

\[DeviceE-Ten-GigabitEthernet1/0/2] port trunk permit
vlan 1 to 30

\[DeviceE-Ten-GigabitEthernet1/0/2] quit

\# Create RRPP domain 1\. 

\[DeviceE] rrpp domain 1

\# Configure VLAN 4092 as the primary
control VLAN of RRPP domain 1\. 

\[DeviceE-rrpp-domain1]
control-vlan 4092

\# Configure the VLANs mapped to MSTI 1 as
the protected VLANs of RRPP domain 1\. 

\[DeviceE-rrpp-domain1]
protected-vlan reference-instance 1

\# Configure Device E as the master node
of subring 2, with Ten-GigabitEthernet 1/0/1 as the primary
port and Ten-GigabitEthernet 1/0/2 as the secondary port. Enable ring 2\. 

\[DeviceE-rrpp-domain1] ring 2
node-mode master primary-port ten-gigabitethernet 1/0/1 secondary-port ten-gigabitethernet 1/0/2 level 1

\[DeviceE-rrpp-domain1] ring 2
enable

\[DeviceE-rrpp-domain1] quit

\# Enable RRPP. 

\[DeviceE] rrpp enable

#### Verifying the configuration

\# Use the display commands to view RRPP configuration and operational information on
each device.

### Example: Configuring load-balanced intersecting rings

#### Network configuration

As shown in [Figure 11](#_Ref266264581):

·     Device A, Device B, Device C, Device D, and
Device F form RRPP domain 1\. VLAN 100 is the primary control VLAN of the RRPP
domain. Device A is the master node of the primary ring, Ring 1\. Device D is
the transit node of Ring 1\. Device F is the master node of the subring Ring 3\. Device
C is the edge node of the subring Ring 3\. Device B is the assistant edge node
of the subring Ring 3\. 

·     Device A, Device B, Device C, Device D, and
Device E form RRPP domain 2\. VLAN 105 is the primary control VLAN of the RRPP
domain. Device A is the master node of the primary ring, Ring 1\. Device D is
the transit node of Ring 1\. Device E is the master node of the subring Ring 2\. Device
C is the edge node of the subring Ring 2\. Device B is the assistant edge node
of the subring Ring 2\. 

·     Specify VLAN 11 as the protected VLAN of domain
1, and VLAN 12 the protected VLAN of domain 2\. You can implement VLAN-based
load balancing on Ring 1\. 

·     Ring 2 and Ring 3 have the same edge node and assistant
edge node, and the two subrings have the same SRPTs. You can add Ring 2 and
Ring 3 to an RRPP ring group to reduce Edge-Hello traffic. 

Figure 11 Network
diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705569_x_Img_x_png_10_2216097_294551_0.png)

‌

#### Procedure

**1\.**Configure Device A:

\# Create VLANs 11 and 12\. 

\<DeviceA\> system-view

\[DeviceA] vlan 11 to 12

\# Map VLAN 11 to MSTI 1 and VLAN 12 to
MSTI 2\. 

\[DeviceA] stp
region-configuration

\[DeviceA-mst-region] instance
1 vlan 11

\[DeviceA-mst-region] instance 2
vlan 12

\# Activate the MST region configuration.

\[DeviceA-mst-region] active
region-configuration

\[DeviceA-mst-region] quit

\# Set the physical state change suppression
interval to 0 seconds on Ten-GigabitEthernet 1/0/1. 

\[DeviceA] interface ten-gigabitethernet 1/0/1

\[DeviceA-Ten-GigabitEthernet1/0/1] link-delay up 0

\[DeviceA-Ten-GigabitEthernet1/0/1] link-delay down 0

\# Disable the spanning tree feature on
the port. 

\[DeviceA-Ten-GigabitEthernet1/0/1] undo stp enable

\# Configure the port as a trunk port. 

\[DeviceA-Ten-GigabitEthernet1/0/1] port link-type
trunk

\# Remove the port from VLAN 1, and assign
it to VLANs 11 and 12\. 

\[DeviceA-Ten-GigabitEthernet1/0/1] undo port trunk
permit vlan 1

\[DeviceA-Ten-GigabitEthernet1/0/1] port trunk permit
vlan 11 12

\# Configure VLAN 11 as the default VLAN. 

\[DeviceA-Ten-GigabitEthernet1/0/1] port trunk pvid
vlan 11

\[DeviceA-Ten-GigabitEthernet1/0/1] quit

\# Configure Ten-GigabitEthernet 1/0/2 in the same way Ten-GigabitEthernet 1/0/1 is configured.

\[DeviceA] interface ten-gigabitethernet 1/0/2

\[DeviceA-Ten-GigabitEthernet1/0/2] link-delay up 0

\[DeviceA-Ten-GigabitEthernet1/0/2] link-delay down 0

\[DeviceA-Ten-GigabitEthernet1/0/2] undo stp enable

\[DeviceA-Ten-GigabitEthernet1/0/2] port link-type
trunk

\[DeviceA-Ten-GigabitEthernet1/0/2] undo port trunk
permit vlan 1

\[DeviceA-Ten-GigabitEthernet1/0/2] port trunk permit
vlan 11 12

\[DeviceA-Ten-GigabitEthernet1/0/2] port trunk pvid
vlan 11

\[DeviceA-Ten-GigabitEthernet1/0/2] quit

\# Create RRPP domain 1\. 

\[DeviceA] rrpp domain 1

\# Configure VLAN 100 as the primary
control VLAN of RRPP domain 1\. 

\[DeviceA-rrpp-domain1]
control-vlan 100

\# Configure the VLAN mapped to MSTI 1 as
the protected VLAN of RRPP domain 1\. 

\[DeviceA-rrpp-domain1]
protected-vlan reference-instance 1

\# Configure Device A as the master node
of primary ring 1, with Ten-GigabitEthernet 1/0/1 as the primary
port and Ten-GigabitEthernet 1/0/2 as the secondary port. Enable ring 1\. 

\[DeviceA-rrpp-domain1] ring 1
node-mode master primary-port ten-gigabitethernet 1/0/1 secondary-port ten-gigabitethernet 1/0/2 level 0

\[DeviceA-rrpp-domain1] ring 1
enable

\[DeviceA-rrpp-domain1] quit

\# Create RRPP domain 2\. 

\[DeviceA] rrpp domain 2

\# Configure VLAN 105 as the primary
control VLAN of RRPP domain 2\. 

\[DeviceA-rrpp-domain2]
control-vlan 105

\# Configure the VLAN mapped to MSTI 2 as
the protected VLAN of RRPP domain 2\. 

\[DeviceA-rrpp-domain2]
protected-vlan reference-instance 2

\# Configure Device A as the master node
of primary ring 1, with Ten-GigabitEthernet 1/0/2 as the master port
and Ten-GigabitEthernet 1/0/1
as the secondary port. Enable ring 1\. 

\[DeviceA-rrpp-domain2] ring 1
node-mode master primary-port ten-gigabitethernet 1/0/2 secondary-port ten-gigabitethernet 1/0/1 level 0

\[DeviceA-rrpp-domain2] ring 1
enable

\[DeviceA-rrpp-domain2] quit

\# Enable RRPP. 

\[DeviceA] rrpp enable

**2\.**Configure Device B:

\# Create VLANs 11 and 12\. 

\<DeviceB\> system-view

\[DeviceB] vlan 11 to 12

\# Map VLAN 11 to MSTI 1 and VLAN 12 to
MSTI 2\. 

\[DeviceB] stp
region-configuration

\[DeviceB-mst-region] instance
1 vlan 11

\[DeviceB-mst-region] instance 2
vlan 12

\# Activate the MST region configuration.

\[DeviceB-mst-region] active
region-configuration

\[DeviceB-mst-region] quit

\# Set the physical state change suppression
interval to 0 seconds on Ten-GigabitEthernet 1/0/1. 

\[DeviceB] interface ten-gigabitethernet 1/0/1

\[DeviceB-Ten-GigabitEthernet1/0/1] link-delay up 0

\[DeviceB-Ten-GigabitEthernet1/0/1] link-delay down 0

\# Disable the spanning tree feature on
the port. 

\[DeviceB-Ten-GigabitEthernet1/0/1] undo stp enable

\# Configure the port as a trunk port. 

\[DeviceB-Ten-GigabitEthernet1/0/1] port link-type
trunk

\# Remove the port from VLAN 1, and assign
it to VLANs 11 and 12\. 

\[DeviceB-Ten-GigabitEthernet1/0/1] undo port trunk
permit vlan 1

\[DeviceB-Ten-GigabitEthernet1/0/1] port trunk permit
vlan 11 12

\# Configure VLAN 11 as the default VLAN. 

\[DeviceB-Ten-GigabitEthernet1/0/1] port trunk pvid
vlan 11

\[DeviceB-Ten-GigabitEthernet1/0/1] quit

\# Configure Ten-GigabitEthernet 1/0/2 in the same way Ten-GigabitEthernet 1/0/1 is configured.

\[DeviceB] interface ten-gigabitethernet 1/0/2

\[DeviceB-Ten-GigabitEthernet1/0/2] link-delay up 0

\[DeviceB-Ten-GigabitEthernet1/0/2] link-delay down 0

\[DeviceB-Ten-GigabitEthernet1/0/2] undo stp enable

\[DeviceB-Ten-GigabitEthernet1/0/2] port link-type
trunk

\[DeviceB-Ten-GigabitEthernet1/0/2] undo port trunk
permit vlan 1

\[DeviceB-Ten-GigabitEthernet1/0/2] port trunk permit
vlan 11 12

\[DeviceB-Ten-GigabitEthernet1/0/2] port trunk pvid
vlan 11

\[DeviceB-Ten-GigabitEthernet1/0/2] quit

\# Set the physical state change suppression
interval to 0 seconds on Ten-GigabitEthernet 1/0/3.

\[DeviceB] interface ten-gigabitethernet 1/0/3

\[DeviceB-Ten-GigabitEthernet1/0/3] link-delay up 0

\[DeviceB-Ten-GigabitEthernet1/0/3] link-delay down 0

\# Disable the spanning tree feature on
the port. 

\[DeviceB-Ten-GigabitEthernet1/0/3] undo stp enable

\# Configure the port as a trunk port. 

\[DeviceB-Ten-GigabitEthernet1/0/3] port link-type
trunk

\# Remove the port from VLAN 1, and assign
it to VLAN 12\. 

\[DeviceB-Ten-GigabitEthernet1/0/3] undo port trunk
permit vlan 1

\[DeviceB-Ten-GigabitEthernet1/0/3] port trunk permit
vlan 12

\# Configure VLAN 12 as the default VLAN. 

\[DeviceB-Ten-GigabitEthernet1/0/3] port trunk pvid
vlan 12

\[DeviceB-Ten-GigabitEthernet1/0/3] quit

\# Set the physical state change suppression
interval to 0 seconds on Ten-GigabitEthernet 1/0/4.

\[DeviceB] interface ten-gigabitethernet 1/0/4

\[DeviceB-Ten-GigabitEthernet1/0/4] link-delay up 0

\[DeviceB-Ten-GigabitEthernet1/0/4] link-delay down 0

\# Disable the spanning tree feature on
the port. 

\[DeviceB-Ten-GigabitEthernet1/0/4] undo stp enable

\# Configure the port as a trunk port. 

\[DeviceB-Ten-GigabitEthernet1/0/4] port link-type
trunk

\# Remove the port from VLAN 1, and assign
it to VLAN 11\. 

\[DeviceB-Ten-GigabitEthernet1/0/4] undo port trunk
permit vlan 1

\[DeviceB-Ten-GigabitEthernet1/0/4] port trunk permit
vlan 11

\# Configure VLAN 11 as the default VLAN. 

\[DeviceB-Ten-GigabitEthernet1/0/4] port trunk pvid
vlan 11

\[DeviceB-Ten-GigabitEthernet1/0/4] quit

\# Create RRPP domain 1\. 

\[DeviceB] rrpp domain 1

\# Configure VLAN 100 as the primary
control VLAN of RRPP domain 1\. 

\[DeviceB-rrpp-domain1]
control-vlan 100

\# Configure the VLAN mapped to MSTI 1 as
the protected VLAN of RRPP domain 1\. 

\[DeviceB-rrpp-domain1]
protected-vlan reference-instance 1

\# Configure Device B as a transit node of
primary ring 1 in RRPP domain 1, with Ten-GigabitEthernet 1/0/1 as the primary
port and Ten-GigabitEthernet 1/0/2 as the secondary port. Enable ring 1\. 

\[DeviceB-rrpp-domain1] ring 1
node-mode transit primary-port ten-gigabitethernet 1/0/1 secondary-port ten-gigabitethernet 1/0/2 level 0

\[DeviceB-rrpp-domain1] ring 1
enable

\# Configure Device B as the assistant
edge node of subring 3 in RRPP domain 1, with Ten-GigabitEthernet 1/0/4 as the edge port.
Enable subring 3\. 

\[DeviceB-rrpp-domain1] ring 3
node-mode assistant-edge edge-port ten-gigabitethernet 1/0/4 

\[DeviceB-rrpp-domain1] ring 3
enable

\[DeviceB-rrpp-domain1] quit

\# Create RRPP domain 2\. 

\[DeviceB] rrpp domain 2

\# Configure VLAN 105 as the primary
control VLAN of RRPP domain 2\. 

\[DeviceB-rrpp-domain2]
control-vlan 105

\# Configure the VLAN mapped to MSTI 2 as
the protected VLAN of RRPP domain 2\. 

\[DeviceB-rrpp-domain2]
protected-vlan reference-instance 2

\# Configure Device B as the transit node
of primary ring 1, with Ten-GigabitEthernet 1/0/1 as the primary
port and Ten-GigabitEthernet 1/0/2 as the secondary port. Enable ring 1\. 

\[DeviceB-rrpp-domain2] ring 1
node-mode transit primary-port ten-gigabitethernet 1/0/1 secondary-port ten-gigabitethernet 1/0/2 level 0

\[DeviceB-rrpp-domain2] ring 1
enable

\# Configure Device B as the assistant
edge node of subring 2 in RRPP domain 2, with Ten-GigabitEthernet 1/0/3 as the edge port.
Enable subring 2\. 

\[DeviceB-rrpp-domain2] ring 2
node-mode assistant-edge edge-port ten-gigabitethernet 1/0/3

\[DeviceB-rrpp-domain2] ring 2
enable

\[DeviceC-rrpp-domain2] quit

\# Enable RRPP. 

\[DeviceB] rrpp enable

**3\.**Configure Device C:

\# Create VLANs 11 and 12\. 

\<DeviceC\> system-view

\[DeviceC] vlan 11 to 12

\# Map VLAN 11 to MSTI 1 and VLAN 12 to
MSTI 2\. 

\[DeviceC] stp
region-configuration

\[DeviceC-mst-region] instance
1 vlan 11

\[DeviceC-mst-region] instance 2
vlan 12

\# Activate the MST region configuration.

\[DeviceC-mst-region] active
region-configuration

\[DeviceC-mst-region] quit

\# Set the physical state change suppression
interval to 0 seconds on Ten-GigabitEthernet 1/0/1.

\[DeviceC] interface ten-gigabitethernet 1/0/1

\[DeviceC-Ten-GigabitEthernet1/0/1] link-delay up 0

\[DeviceC-Ten-GigabitEthernet1/0/1] link-delay down 0

\# Disable the spanning tree feature on
the port. 

\[DeviceC-Ten-GigabitEthernet1/0/1] undo stp enable

\# Configure the port as a trunk port. 

\[DeviceC-Ten-GigabitEthernet1/0/1] port link-type
trunk

\# Remove the port from VLAN 1, and assign
it to VLANs 11 and 12\. 

\[DeviceC-Ten-GigabitEthernet1/0/1] undo port trunk
permit vlan 1

\[DeviceC-Ten-GigabitEthernet1/0/1] port trunk permit
vlan 11 12

\# Configure VLAN 11 as the default VLAN. 

\[DeviceC-Ten-GigabitEthernet1/0/1] port trunk pvid
vlan 11

\[DeviceC-Ten-GigabitEthernet1/0/1] quit

\# Configure Ten-GigabitEthernet 1/0/2 in the same way Ten-GigabitEthernet 1/0/1 is configured.

\[DeviceC] interface ten-gigabitethernet 1/0/2

\[DeviceC-Ten-GigabitEthernet1/0/2] link-delay up 0

\[DeviceC-Ten-GigabitEthernet1/0/2] link-delay down 0

\[DeviceC-Ten-GigabitEthernet1/0/2] undo stp enable

\[DeviceC-Ten-GigabitEthernet1/0/2] port link-type
trunk

\[DeviceC-Ten-GigabitEthernet1/0/2] undo port trunk
permit vlan 1

\[DeviceC-Ten-GigabitEthernet1/0/2] port trunk permit
vlan 11 12

\[DeviceC-Ten-GigabitEthernet1/0/2] port trunk pvid
vlan 11

\[DeviceC-Ten-GigabitEthernet1/0/2] quit

\# Set the physical state change suppression
interval to 0 seconds on Ten-GigabitEthernet 1/0/3.

\[DeviceC] interface ten-gigabitethernet 1/0/3

\[DeviceC-Ten-GigabitEthernet1/0/3] link-delay up 0

\[DeviceC-Ten-GigabitEthernet1/0/3] link-delay down 0

\# Disable the spanning tree feature on
the port. 

\[DeviceC-Ten-GigabitEthernet1/0/3] undo stp enable

\# Configure the port as a trunk port. 

\[DeviceC-Ten-GigabitEthernet1/0/3] port link-type
trunk

\# Remove the port from VLAN 1, and assign
it to VLAN 12\. 

\[DeviceC-Ten-GigabitEthernet1/0/3] undo port trunk permit
vlan 1

\[DeviceC-Ten-GigabitEthernet1/0/3] port trunk permit
vlan 12

\# Configure VLAN 12 as the default VLAN. 

\[DeviceC-Ten-GigabitEthernet1/0/3] port trunk pvid
vlan 12

\[DeviceC-Ten-GigabitEthernet1/0/3] quit

\# Set the physical state change suppression
interval to 0 seconds on Ten-GigabitEthernet 1/0/4.

\[DeviceC] interface ten-gigabitethernet 1/0/4

\[DeviceC-Ten-GigabitEthernet1/0/4] link-delay up 0

\[DeviceC-Ten-GigabitEthernet1/0/4] link-delay down 0

\# Disable the spanning tree feature on
the port. 

\[DeviceC-Ten-GigabitEthernet1/0/4] undo stp enable

\# Configure the port as a trunk port. 

\[DeviceC-Ten-GigabitEthernet1/0/4] port link-type
trunk

\# Remove the port from VLAN 1, and assign
it to VLAN 11\. 

\[DeviceC-Ten-GigabitEthernet1/0/4] undo port trunk
permit vlan 1

\[DeviceC-Ten-GigabitEthernet1/0/4] port trunk permit
vlan 11

\# Configure VLAN 11 as the default VLAN. 

\[DeviceC-Ten-GigabitEthernet1/0/4] port trunk pvid
vlan 11

\[DeviceC-Ten-GigabitEthernet1/0/4] quit

\# Create RRPP domain 1\. 

\[DeviceC] rrpp domain 1

\# Configure VLAN 100 as the primary
control VLAN of RRPP domain 1\. 

\[DeviceC-rrpp-domain1]
control-vlan 100

\# Configure the VLAN mapped to MSTI 1 as
the protected VLAN of RRPP domain 1\. 

\[DeviceC-rrpp-domain1]
protected-vlan reference-instance 1

\# Configure Device C as the transit node
of primary ring 1 in RRPP domain 1, with Ten-GigabitEthernet 1/0/1 as the primary
port and Ten-GigabitEthernet 1/0/2 as the secondary port. Enable ring 1\. 

\[DeviceC-rrpp-domain1] ring 1
node-mode transit primary-port ten-gigabitethernet 1/0/1 secondary-port ten-gigabitethernet 1/0/2 level 0

\[DeviceC-rrpp-domain1] ring 1
enable

\# Configure Device C as the edge node of subring
3 in RRPP domain 1, with Ten-GigabitEthernet 1/0/4 as the edge port.
Enable subring 3\. 

\[DeviceC-rrpp-domain1] ring 3
node-mode edge edge-port ten-gigabitethernet 1/0/4

\[DeviceC-rrpp-domain1] ring 3
enable

\[DeviceC-rrpp-domain1] quit

\# Create RRPP domain 2\. 

\[DeviceC] rrpp domain 2

\# Configure VLAN 105 as the primary
control VLAN of RRPP domain 2\. 

\[DeviceC-rrpp-domain2]
control-vlan 105

\# Configure the VLAN mapped to MSTI 2 as
the protected VLAN of RRPP domain 2\. 

\[DeviceC-rrpp-domain2]
protected-vlan reference-instance 2

\# Configure Device C as the transit node
of primary ring 1 in RRPP domain 2, with Ten-GigabitEthernet 1/0/1 as the primary
port and Ten-GigabitEthernet 1/0/2 as the secondary port. Enable ring 1\. 

\[DeviceC-rrpp-domain2] ring 1
node-mode transit primary-port ten-gigabitethernet 1/0/1 secondary-port ten-gigabitethernet 1/0/2 level 0

\[DeviceC-rrpp-domain2] ring 1
enable

\# Configure Device C as the edge node of
subring 2 in RRPP domain 2, with Ten-GigabitEthernet 1/0/3 as the edge port.
Enable subring 2\. 

\[DeviceC-rrpp-domain2] ring 2
node-mode edge edge-port ten-gigabitethernet 1/0/3

\[DeviceC-rrpp-domain2] ring 2
enable

\[DeviceC-rrpp-domain2] quit

\# Enable RRPP. 

\[DeviceC] rrpp enable

**4\.**Configure Device D:

\# Create VLANs 11 and 12\. 

\<DeviceD\> system-view

\[DeviceD] vlan 11 to 12

\# Map VLAN 11 to MSTI 1 and VLAN 12 to
MSTI 2\. 

\[DeviceD] stp
region-configuration

\[DeviceD-mst-region] instance
1 vlan 11

\[DeviceD-mst-region] instance 2
vlan 12

\# Activate the MST region configuration.

\[DeviceD-mst-region] active
region-configuration

\[DeviceD-mst-region] quit

\# Set the physical state change suppression
interval to 0 seconds on Ten-GigabitEthernet 1/0/1.

\[DeviceD] interface ten-gigabitethernet 1/0/1

\[DeviceD-Ten-GigabitEthernet1/0/1] link-delay up 0

\[DeviceD-Ten-GigabitEthernet1/0/1] link-delay down 0

\# Disable the spanning tree feature on
the port. 

\[DeviceD-Ten-GigabitEthernet1/0/1] undo stp enable

\# Configure the port as a trunk port. 

\[DeviceD-Ten-GigabitEthernet1/0/1] port link-type
trunk

\# Remove the port from VLAN 1, and assign
it to VLANs 11 and 12\. 

\[DeviceD-Ten-GigabitEthernet1/0/1] undo port trunk
permit vlan 1

\[DeviceD-Ten-GigabitEthernet1/0/1] port trunk permit
vlan 11 12

\# Configure VLAN 11 as the default VLAN. 

\[DeviceD-Ten-GigabitEthernet1/0/1] port trunk pvid
vlan 11

\[DeviceD-Ten-GigabitEthernet1/0/1] quit

\# Configure Ten-GigabitEthernet 1/0/2 in the same way Ten-GigabitEthernet 1/0/1 is configured.

\[DeviceD] interface ten-gigabitethernet 1/0/2

\[DeviceD-Ten-GigabitEthernet1/0/2] link-delay up 0

\[DeviceD-Ten-GigabitEthernet1/0/2] link-delay down 0

\[DeviceD-Ten-GigabitEthernet1/0/2] undo stp enable

\[DeviceD-Ten-GigabitEthernet1/0/2] port link-type
trunk

\[DeviceD-Ten-GigabitEthernet1/0/2] undo port trunk
permit vlan 1

\[DeviceD-Ten-GigabitEthernet1/0/2] port trunk permit
vlan 11 12

\[DeviceD-Ten-GigabitEthernet1/0/2] port trunk pvid
vlan 11

\[DeviceD-Ten-GigabitEthernet1/0/2] quit

\# Create RRPP domain 1\. 

\[DeviceD] rrpp domain 1

\# Configure VLAN 100 as the primary
control VLAN of RRPP domain 1\. 

\[DeviceD-rrpp-domain1]
control-vlan 100

\# Configure the VLAN mapped to MSTI 1 as
the protected VLAN of RRPP domain 1\. 

\[DeviceD-rrpp-domain1]
protected-vlan reference-instance 1

\# Configure Device D as the transit node
of primary ring 1 in RRPP domain 1, with Ten-GigabitEthernet 1/0/1 as the primary
port and Ten-GigabitEthernet 1/0/2 as the secondary port. Enable ring 1\. 

\[DeviceD-rrpp-domain1] ring 1
node-mode transit primary-port ten-gigabitethernet 1/0/1 secondary-port ten-gigabitethernet 1/0/2 level 0

\[DeviceD-rrpp-domain1] ring 1
enable

\[DeviceD-rrpp-domain1] quit

\# Create RRPP domain 2\. 

\[DeviceD] rrpp domain 2

\# Configure VLAN 105 as the primary
control VLAN of RRPP domain 2\. 

\[DeviceD-rrpp-domain2]
control-vlan 105

\# Configure the VLAN mapped to MSTI 2 as
the protected VLAN of RRPP domain 2\. 

\[DeviceD-rrpp-domain2]
protected-vlan reference-instance 2

\# Configure Device D as the transit node
of primary ring 1 in RRPP domain 2, with Ten-GigabitEthernet 1/0/1 as the primary
port and Ten-GigabitEthernet 1/0/2 as the secondary port. Enable ring 1\. 

\[DeviceD-rrpp-domain2] ring 1
node-mode transit primary-port ten-gigabitethernet 1/0/1 secondary-port ten-gigabitethernet 1/0/2 level 0

\[DeviceD-rrpp-domain2] ring 1
enable

\[DeviceD-rrpp-domain2] quit

\# Enable RRPP. 

\[DeviceD] rrpp enable

**5\.**Configure Device E:

\# Create VLAN 12\. 

\<DeviceE\> system-view

\[DeviceE] vlan 12

\# Map VLAN 12 to MSTI 2\. 

\[DeviceE-vlan12] quit

\[DeviceE] stp
region-configuration

\[DeviceE-mst-region] instance
2 vlan 12

\# Activate the MST region configuration.

\[DeviceE-mst-region] active
region-configuration

\[DeviceE-mst-region] quit

\# Set the physical state change suppression
interval to 0 seconds on Ten-GigabitEthernet 1/0/1.

\[DeviceE] interface ten-gigabitethernet 1/0/1

\[DeviceE-Ten-GigabitEthernet1/0/1] link-delay up 0

\[DeviceE-Ten-GigabitEthernet1/0/1] link-delay down 0

\# Disable the spanning tree feature on
the port. 

\[DeviceE-Ten-GigabitEthernet1/0/1] undo stp enable

\# Configure the port as a trunk port. 

\[DeviceE-Ten-GigabitEthernet1/0/1] port link-type
trunk

\# Remove the port from VLAN 1, and assign
it to VLAN 12\. 

\[DeviceE-Ten-GigabitEthernet1/0/1] undo port trunk
permit vlan 1

\[DeviceE-Ten-GigabitEthernet1/0/1] port trunk permit
vlan 12

\# Configure VLAN 12 as the default VLAN. 

\[DeviceE-Ten-GigabitEthernet1/0/1] port trunk pvid
vlan 12

\[DeviceE-Ten-GigabitEthernet1/0/1] quit

\# Configure Ten-GigabitEthernet 1/0/2 in the same way Ten-GigabitEthernet 1/0/1 is configured.

\[DeviceE] interface ten-gigabitethernet 1/0/2

\[DeviceE-Ten-GigabitEthernet1/0/2] link-delay up 0

\[DeviceE-Ten-GigabitEthernet1/0/2] link-delay down 0

\[DeviceE-Ten-GigabitEthernet1/0/2] undo stp enable

\[DeviceE-Ten-GigabitEthernet1/0/2] port link-type
trunk

\[DeviceE-Ten-GigabitEthernet1/0/2] undo port trunk
permit vlan 1

\[DeviceE-Ten-GigabitEthernet1/0/2] port trunk permit
vlan 12

\[DeviceE-Ten-GigabitEthernet1/0/2] port trunk pvid
vlan 12

\[DeviceE-Ten-GigabitEthernet1/0/2] quit

\# Create RRPP domain 2\. 

\[DeviceE] rrpp domain 2

\# Configure VLAN 105 as the primary
control VLAN of RRPP domain 2\. 

\[DeviceE-rrpp-domain2]
control-vlan 105

\# Configure the VLAN mapped to MSTI 2 as
the protected VLAN of RRPP domain 2\. 

\[DeviceE-rrpp-domain2]
protected-vlan reference-instance 2

\# Configure Device E as the master mode
of subring 2 in RRPP domain 2, with Ten-GigabitEthernet 1/0/2 as the primary
port and Ten-GigabitEthernet 1/0/1 as the secondary port. Enable ring 2\. 

\[DeviceE-rrpp-domain2] ring 2
node-mode master primary-port ten-gigabitethernet 1/0/2 secondary-port ten-gigabitethernet 1/0/1 level 1

\[DeviceE-rrpp-domain2] ring 2
enable

\[DeviceE-rrpp-domain2] quit

\# Enable RRPP. 

\[DeviceE] rrpp enable

**6\.**Configure Device F:

\# Create VLAN 11\. 

\<DeviceF\> system-view

\[DeviceF] vlan 11

\[DeviceF-vlan11] quit

\# Map VLAN 11 to MSTI 1\. 

\[DeviceF] stp
region-configuration

\[DeviceF-mst-region] instance
1 vlan 11

\# Activate the MST region configuration.

\[DeviceF-mst-region] active
region-configuration

\[DeviceF-mst-region] quit

\# Set the physical state change suppression
interval to 0 seconds on Ten-GigabitEthernet 1/0/1.

\[DeviceF] interface ten-gigabitethernet 1/0/1

\[DeviceF-Ten-GigabitEthernet1/0/1] link-delay up 0

\[DeviceF-Ten-GigabitEthernet1/0/1] link-delay down 0

\# Disable the spanning tree feature on
the port. 

\[DeviceF-Ten-GigabitEthernet1/0/1] undo stp enable

\# Configure the port as a trunk port. 

\[DeviceF-Ten-GigabitEthernet1/0/1] port link-type
trunk

\# Remove the port from VLAN 1, and assign
it to VLAN 11\. 

\[DeviceF-Ten-GigabitEthernet1/0/1] undo port trunk
permit vlan 1

\[DeviceF-Ten-GigabitEthernet1/0/1] port trunk permit
vlan 11

\# Configure VLAN 11 as the default VLAN. 

\[DeviceF-Ten-GigabitEthernet1/0/1] port trunk pvid
vlan 11

\[DeviceF-Ten-GigabitEthernet1/0/1] quit

\# Configure Ten-GigabitEthernet 1/0/2 in the same way Ten-GigabitEthernet 1/0/1 is configured.

\[DeviceF] interface ten-gigabitethernet 1/0/2

\[DeviceF-Ten-GigabitEthernet1/0/2] link-delay up 0

\[DeviceF-Ten-GigabitEthernet1/0/2] link-delay down 0

\[DeviceF-Ten-GigabitEthernet1/0/2] undo stp enable

\[DeviceF-Ten-GigabitEthernet1/0/2] port link-type
trunk

\[DeviceF-Ten-GigabitEthernet1/0/2] undo port trunk
permit vlan 1

\[DeviceF-Ten-GigabitEthernet1/0/2] port trunk permit
vlan 11

\[DeviceF-Ten-GigabitEthernet1/0/2] port trunk pvid
vlan 11

\[DeviceF-Ten-GigabitEthernet1/0/2] quit

\# Create RRPP domain 1\. 

\[DeviceF] rrpp domain 1

\# Configure VLAN 100 as the primary
control VLAN of RRPP domain 1\. 

\[DeviceF-rrpp-domain1]
control-vlan 100

\# Configure the VLAN mapped to MSTI 1 as
the protected VLAN of RRPP domain 1\. 

\[DeviceF-rrpp-domain1]
protected-vlan reference-instance 1

\# Configure Device F as the master node
of subring 3 in RRPP domain 1, with Ten-GigabitEthernet 1/0/1 as the primary
port and Ten-GigabitEthernet 1/0/2 as the secondary port. Enable subring 3\. 

\[DeviceF-rrpp-domain1] ring 3
node-mode master primary-port ten-gigabitethernet 1/0/1 secondary-port ten-gigabitethernet 1/0/2 level 1

\[DeviceF-rrpp-domain1] ring 3
enable

\[DeviceF-rrpp-domain1] quit

\# Enable RRPP. 

\[DeviceF] rrpp enable

**7\.**Configure RRPP ring group settings on Device
B and Device C:

\# Create RRPP ring group 1 on Device B,
and add subrings 2 and 3 to the RRPP ring group. 

\[DeviceB] rrpp ring-group 1

\[DeviceB-rrpp-ring-group1]
domain 2 ring 2

\[DeviceB-rrpp-ring-group1]
domain 1 ring 3

\# Create RRPP ring group 1 on Device C,
and add subrings 2 and 3 to the RRPP ring group. 

\[DeviceC] rrpp ring-group 1

\[DeviceC-rrpp-ring-group1]
domain 2 ring 2

\[DeviceC-rrpp-ring-group1]
domain 1 ring 3

#### Verifying the configuration

\# Use the display commands to view RRPP configuration and operational information on
each device.

### Example: Configuring fast detection

#### Network configuration

As shown in [Figure 12](#_Ref215995928):

·     Device A, Device B, Device C, and Device D form RRPP
domain 1\. VLAN 4092 is the primary control VLAN of RRPP domain 1\. RRPP domain 1
protects VLANs 1 through 30\. 

·     Device A is the master node and supports RRPP
fast detection, providing a timer resolution of 10 milliseconds. The default Fast-Fail
and Fast-Hello timers are 60 milliseconds and 20 milliseconds, respectively. Device
D is a transit node. Device B and Device C do not support RRPP. 

Neither Device B nor Device C supports RRPP.
When the link between them fails, the link failure cannot be detected by the
master node in a timely manner. Configure RRPP fast detection to implement fast
link switchover even when the link between Device B and Device C fails. 

Figure
12 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705570_x_Img_x_png_11_2216097_294551_0.png)

‌

#### Procedure

**1\.**Configure Device A:

\# Create VLANs 1 through 30\. 

\<DeviceA\> system-view

\[DeviceA] vlan 1 to 30

\# Map these VLANs to MSTI 1\.

\[DeviceA] stp
region-configuration

\[DeviceA-mst-region] instance
1 vlan 1 to 30

\# Activate the MST region configuration.

\[DeviceA-mst-region] active
region-configuration

\[DeviceA-mst-region] quit

\# Set the physical state change suppression
interval to 0 seconds on Ten-GigabitEthernet 1/0/1. 

\[DeviceA] interface ten-gigabitethernet 1/0/1

\[DeviceA-Ten-GigabitEthernet1/0/1] link-delay up 0

\[DeviceA-Ten-GigabitEthernet1/0/1] link-delay down 0

\# Disable the spanning tree feature on
the port. 

\[DeviceA-Ten-GigabitEthernet1/0/1] undo stp enable

\# Configure the port as a trunk port. 

\[DeviceA-Ten-GigabitEthernet1/0/1] port link-type
trunk

\# Assign the port to VLANs 1 through 30\.

\[DeviceA-Ten-GigabitEthernet1/0/1] port trunk permit
vlan 1 to 30

\[DeviceA-Ten-GigabitEthernet1/0/1] quit

\# Configure Ten-GigabitEthernet 1/0/2 in the same way Ten-GigabitEthernet 1/0/1 is configured.

\[DeviceA] interface ten-gigabitethernet 1/0/2

\[DeviceA-Ten-GigabitEthernet1/0/2] link-delay up 0

\[DeviceA-Ten-GigabitEthernet1/0/2] link-delay down 0

\[DeviceA-Ten-GigabitEthernet1/0/2] undo stp enable

\[DeviceA-Ten-GigabitEthernet1/0/2] port link-type
trunk

\[DeviceA-Ten-GigabitEthernet1/0/2] port trunk permit
vlan 1 to 30

\[DeviceA-Ten-GigabitEthernet1/0/2] quit

\# Create RRPP domain 1\. 

\[DeviceA] rrpp domain 1

\# Configure VLAN 4092 as the primary
control VLAN of RRPP domain 1\. 

\[DeviceA-rrpp-domain1]
control-vlan 4092

\# Configure the VLANs mapped to MSTI 1 as
the protected VLANs of RRPP domain 1\. 

\[DeviceA-rrpp-domain1] protected-vlan
reference-instance 1

\# Configure Device A as the master node
of the primary ring Ring 1, with Ten-GigabitEthernet 1/0/1 as the primary
port and Ten-GigabitEthernet 1/0/2 as the secondary port. Enable Ring 1\. 

\[DeviceA-rrpp-domain1] ring 1 node-mode
master primary-port ten-gigabitethernet 1/0/1 secondary-port ten-gigabitethernet 1/0/2 level 0

\[DeviceA-rrpp-domain1] ring 1
enable

\# Enable fast detection. Set the Fast-Hello
timer and Fast-Fail timer to 100 milliseconds and 300 milliseconds,
respectively. 

\[DeviceA-rrpp-domain1]
fast-detection enable

\[DeviceA-rrpp-domain1]
fast-timer hello-timer 100 fail-timer 300

\[DeviceA-rrpp-domain1] quit

\# Enable the RRPP protocol. 

\[DeviceA] rrpp enable

**2\.**Configure Device B:

\# Create VLAN 4092 and VLAN 4093\. 

\<DeviceB\> system-view

\[DeviceB] vlan 4092 to 4093

\# Set the physical state change suppression
interval to 0 seconds on Ten-GigabitEthernet 1/0/1. 

\[DeviceB] interface ten-gigabitethernet 1/0/1

\[DeviceB-Ten-GigabitEthernet1/0/1] link-delay up 0

\[DeviceB-Ten-GigabitEthernet1/0/1] link-delay down 0

\# Disable the spanning tree feature on
the port. 

\[DeviceB-Ten-GigabitEthernet1/0/1] undo stp enable

\# Configure the port as a trunk port. 

\[DeviceB-Ten-GigabitEthernet1/0/1] port link-type
trunk

\# Assign the port to VLANs 1 through 30, 4092,
and 4093\.

\[DeviceB-Ten-GigabitEthernet1/0/1] port trunk permit
vlan 1 to 30 4092 4093

\[DeviceB-Ten-GigabitEthernet1/0/1] quit

\# Configure Ten-GigabitEthernet 1/0/2 in the same way Ten-GigabitEthernet 1/0/1 is configured.

\[DeviceB] interface ten-gigabitethernet 1/0/2

\[DeviceB-Ten-GigabitEthernet1/0/2] link-delay up 0

\[DeviceB-Ten-GigabitEthernet1/0/2] link-delay down 0

\[DeviceB-Ten-GigabitEthernet1/0/2] undo stp enable

\[DeviceB-Ten-GigabitEthernet1/0/2] port link-type
trunk

\[DeviceB-Ten-GigabitEthernet1/0/2] port trunk permit
vlan 1 to 30 4092 4093

**3\.**Configure Device C:

\# Create VLAN 4092 and VLAN 4093\. 

\<DeviceC\> system-view

\[DeviceC] vlan 4092 to 4093

\# Set the physical state change suppression
interval to 0 seconds on Ten-GigabitEthernet 1/0/1. 

\[DeviceC] interface ten-gigabitethernet 1/0/1

\[DeviceC-Ten-GigabitEthernet1/0/1] link-delay up 0

\[DeviceC-Ten-GigabitEthernet1/0/1] link-delay down 0

\# Disable the spanning tree feature on
the port. 

\[DeviceC-Ten-GigabitEthernet1/0/1] undo stp enable

\# Configure the port as a trunk port. 

\[DeviceC-Ten-GigabitEthernet1/0/1] port link-type
trunk

\# Assign the port to VLANs 1 through 30, 4092,
and 4093\.

\[DeviceC-Ten-GigabitEthernet1/0/1] port trunk permit vlan
1 to 30 4092 4093

\[DeviceC-Ten-GigabitEthernet1/0/1] quit

\# Configure Ten-GigabitEthernet 1/0/2 in the same way Ten-GigabitEthernet 1/0/1 is configured.

\[DeviceC] interface ten-gigabitethernet 1/0/2

\[DeviceC-Ten-GigabitEthernet1/0/2] link-delay up 0

\[DeviceC-Ten-GigabitEthernet1/0/2] link-delay down 0

\[DeviceC-Ten-GigabitEthernet1/0/2] undo stp enable

\[DeviceC-Ten-GigabitEthernet1/0/2] port link-type
trunk

\[DeviceC-Ten-GigabitEthernet1/0/2] port trunk permit
vlan 1 to 30 4092 4093

**4\.**Configure Device D:

\# Create VLANs 1 through 30\. 

\<DeviceD\> system-view

\[DeviceD] vlan 1 to 30

\# Map these VLANs to MSTI 1\.

\[DeviceD] stp
region-configuration

\[DeviceD-mst-region] instance
1 vlan 1 to 30

\# Activate the MST region configuration.

\[DeviceD-mst-region] active
region-configuration

\[DeviceD-mst-region] quit

\# Set the physical state change suppression
interval to 0 seconds on Ten-GigabitEthernet 1/0/1. 

\[DeviceD] interface ten-gigabitethernet 1/0/1

\[DeviceD-Ten-GigabitEthernet1/0/1] link-delay up 0

\[DeviceD-Ten-GigabitEthernet1/0/1] link-delay down 0

\# Disable the spanning tree feature on
the port. 

\[DeviceD-Ten-GigabitEthernet1/0/1] undo stp enable

\# Configure the port as a trunk port. 

\[DeviceD-Ten-GigabitEthernet1/0/1] port link-type
trunk

\# Assign the port to VLANs 1 through 30\.

\[DeviceD-Ten-GigabitEthernet1/0/1] port trunk permit
vlan 1 to 30

\[DeviceD-Ten-GigabitEthernet1/0/1] quit

\# Configure Ten-GigabitEthernet 1/0/2 in the same way Ten-GigabitEthernet 1/0/1 is configured.

\[DeviceD] interface ten-gigabitethernet 1/0/2

\[DeviceD-Ten-GigabitEthernet1/0/2] link-delay up 0

\[DeviceD-Ten-GigabitEthernet1/0/2] link-delay down 0

\[DeviceD-Ten-GigabitEthernet1/0/2] undo stp enable

\[DeviceD-Ten-GigabitEthernet1/0/2] port link-type
trunk

\[DeviceD-Ten-GigabitEthernet1/0/2] port trunk permit
vlan 1 to 30

\[DeviceD-Ten-GigabitEthernet1/0/2] quit

\# Create RRPP domain 1\. 

\[DeviceD] rrpp domain 1

\# Configure VLAN 4092 as the primary
control VLAN of RRPP domain 1\. 

\[DeviceD-rrpp-domain1]
control-vlan 4092

\# Configure the VLANs mapped to MSTI 1 as
the protected VLANs of RRPP domain 1\. 

\[DeviceD-rrpp-domain1] protected-vlan
reference-instance 1

\# Configure Device D as a transit node of
the primary ring Ring 1, with Ten-GigabitEthernet 1/0/1 as the primary
port and Ten-GigabitEthernet 1/0/2 as the secondary port. Enable Ring 1\. 

\[DeviceD-rrpp-domain1] ring 1 node-mode
transit primary-port ten-gigabitethernet 1/0/1 secondary-port ten-gigabitethernet 1/0/2 level 0

\[DeviceD-rrpp-domain1] ring 1
enable

\[DeviceD-rrpp-domain1] quit

\# Enable the RRPP protocol. 

\[DeviceD] rrpp enable

#### Verifying the configuration

\# Use the display commands to view RRPP configuration and operational information on
Device A and Device D. 

## Troubleshooting RRPP

### The primary node cannot receive Hello packets when the link state is normal

#### Symptom

When the link state is normal, the master
node cannot receive Hello packets, and it unblocks the secondary port. 

#### Analysis

The following are possible causes:

·     One or more nodes on the RRPP ring are not
enabled with RRPP.

·     The domain IDs or control VLANs are not the same
for all nodes on the RRPP ring.

·     The port on the RRPP ring does not work
correctly.

#### Solution

To resolve the problem:

**1\.**Use the display rrpp brief command to determine whether RRPP is enabled for all nodes. If it
is not, use the rrpp enable command and
the ring
enable command to enable RRPP and RRPP rings for
all nodes. 

**2\.**Use the display rrpp brief command to determine whether the domain ID and primary control VLAN
ID are the same for all nodes. If they are not, set the same domain ID and primary
control VLAN ID for the nodes. 

**3\.**Use the display rrpp verbose command to examine the link state of each port in each ring. 

**4\.**Use the debugging rrpp command on each node to determine whether a port receives or
transmits Hello packets. If it does not, Hello packets are lost.

**5\.**If the problem persists, contact H3C
Support.

