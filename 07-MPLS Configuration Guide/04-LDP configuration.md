
# Configuring LDP

## About LDP

The Label Distribution Protocol (LDP)
dynamically distributes FEC-label mapping information between LSRs to establish
LSPs.

### Terminology

#### LDP session

Two LSRs establish a TCP-based LDP session
to exchange FEC-label mappings.

#### LDP peer

Two LSRs that use LDP to exchange FEC-label
mappings are LSR peers.

#### Label spaces and LDP identifiers

Label spaces include the following types:

·     Per-interface label space—Each interface uses a single, independent label space. Different
interfaces can use the same label values.

·     Per-platform label space—Each LSR uses a single label space. The device only supports the
per-platform label space.

A six-byte LDP Identifier (LDP ID) identifies
a label space on an LSR. It is in the format of \<LSR ID\>:\<label space number\>, where:

·     The LSR ID takes four bytes to identity the LSR.


·     The label space number takes two bytes to
identify a label space within the LSR. 

A label space number of 0 indicates that the
label space is a per-platform label space. A label space number other than 0
indicates a per-interface label space.

#### FECs and FEC-label mappings

MPLS groups packets with the same characteristics
(such as the same destination or service class) into a class, called an FEC. The
packets of the same FEC are handled in the same way on an MPLS network. 

LDP can classify FECs by destination IP
address and by PW. This document describes FEC classification by destination IP
address. For information about FEC classification by PW, see "Configuring
MPLS L2VPN" and "Configuring VPLS." 

An LSR assigns a label for an FEC and advertises
the FEC-label mapping, or FEC-label binding, to its peers in a Label Mapping
message.

### LDP messages

LDP mainly uses the following types of
messages:

·     Discovery messages—Declare and maintain the presence of LSRs, such as Hello messages.

·     Session messages—Establish, maintain, and terminate sessions between LDP peers, such
as Initialization messages used for parameter negotiation and Keepalive messages
used to maintain sessions.

·     Advertisement messages—Create, alter, and remove FEC-label mappings, such as Label Mapping
messages used to advertise FEC-label mappings.

·     Notification messages—Provide advisory information and notify errors, such as
Notification messages.

LDP uses UDP to transport discovery
messages for efficiency, and uses TCP to transport session, advertisement, and
notification messages for reliability.

### LDP operation

LDP operates in the following phases: discovering and maintaining LDP
peers, establishing and maintaining LDP sessions, and establishing LSPs.

#### Discovering and maintaining LDP peers

LDP discovers peers in the following ways:

·     Basic Discovery—Discovers directly connected LSRs. On an IPv4 network, an LSR sends
IPv4 Link Hello messages to multicast address 224.0.0.2. All directly connected
LSRs can discover the LSR and establish an IPv4 Link Hello adjacency.

·     Extended Discovery—Sends LDP IPv4 Targeted Hello messages to an IPv4 address. The destination
LSR can discover the LSR and establish a hello adjacency. This mechanism is typically
used in LDP session protection, MPLS L2VPN, and VPLS. For more information
about MPLS L2VPN and VPLS, see "Configuring MPLS L2VPN," and "Configuring
VPLS."

LDP can establish two hello adjacencies
with a directly connected neighbor through both discovery mechanisms. It sends Hello
messages at the hello interval to maintain a hello adjacency. If LDP receives
no Hello message from a hello adjacency before the hello hold timer expires, it
removes the hello adjacency. 

#### Establishing and maintaining LDP sessions

LDP establishes a session to a peer in the
following steps:

**1\.**Establishes a TCP connection to the neighbor.

**2\.**Negotiates session parameters such as LDP version,
label distribution method, and Keepalive timer, and establishes an LDP session to
the neighbor if the negotiation succeeds.

After a session is established, LDP sends LDP
PDUs (an LDP PDU carries one or more LDP messages) to maintain the session. If
no information is exchanged between the LDP peers within the Keepalive
interval, LDP sends Keepalive messages at the Keepalive interval to maintain
the session. If LDP receives no LDP PDU from a neighbor before the keepalive
hold timer expires, or the last hello adjacency with the neighbor is removed,
LDP terminates the session.

LDP can also send a Shutdown message to a neighbor
to terminate the LDP session.

An LSR can establish only one LDP session
to a neighbor.

#### Establishing LSPs

LDP classifies FECs according to
destination IP addresses in IP routing entries, creates FEC-label mappings, and
advertises the mappings to LDP peers through LDP sessions. After an LDP peer
receives an FEC-label mapping, it uses the received label and the label locally
assigned to that FEC to create an LFIB entry for that FEC. When all LSRs (from
the Ingress to the Egress) establish an LFIB entry for the FEC, an LSP is established
exclusively for the FEC.

Figure 1 Dynamically establishing an LSP

![](https://resource.h3c.com/en/202407/12/20240712_11705099_x_Img_x_png_0_2216048_294551_0.png)

 

### Label distribution and control

#### Label advertisement modes

LDP advertises label-FEC mappings in one of the following ways:

·     Downstream Unsolicited (DU)
mode—Distributes FEC-label mappings to the
upstream LSR, without waiting for label requests. The device supports only the
DU mode.

·     Downstream on Demand (DoD)
mode—Sends a label request for an FEC to the
downstream LSR. After receiving the label request, the downstream LSR
distributes the FEC-label mapping for that FEC to the upstream LSR.

Figure 2 Label advertisement modes

![](https://resource.h3c.com/en/202407/12/20240712_11705100_x_Img_x_png_1_2216048_294551_0.png)

 

|  | NOTE:  To successfully establish an LSP, a pair of upstream and downstream LSRs must use the same label advertisement mode. |
| --- | --- |

 

#### Label distribution control

LDP controls label distribution in one of
the following ways:

·     Independent label
distribution—Distributes an FEC-label mapping to
an upstream LSR at any time. An LSR might distribute a mapping for an FEC to
its upstream LSR before it receives a label mapping for that FEC from its
downstream LSR. As shown in [Figure 3](#_Ref317499424), in DU
mode, each LSR distributes a label mapping for an FEC to its upstream LSR
whenever it is ready to label-switch the FEC. The LSRs do not need to wait for
a label mapping for the FEC from its downstream LSR. In DoD mode, an LSR
distributes a label mapping for an FEC to its upstream LSR after it receives a
label request for the FEC. The LSR does not need to wait for a label mapping for
the FEC from its downstream LSR.

Figure 3 Independent label distribution control
mode

![](https://resource.h3c.com/en/202407/12/20240712_11705104_x_Img_x_png_2_2216048_294551_0.png)

 

·     Ordered label distribution—Distributes a label mapping for an FEC to its upstream LSR only
after it receives a label mapping for that FEC from its downstream LSR unless
the local node is the egress node of the FEC. As shown in [Figure 2](#_Ref421198903), in DU
mode, an LSR distributes a label mapping for an FEC to its upstream LSR only if
it receives a label mapping for the FEC from its downstream LSR. In DoD mode,
when an LSR (Transit) receives a label request for an FEC from its upstream LSR
(Ingress), it continues to send a label request for the FEC to its downstream
LSR (Egress). After the transit LSR receives a label mapping for the FEC from
the egress LSR, it distributes a label mapping for the FEC to the ingress LSR.

#### Label retention mode

The label retention mode specifies whether
an LSR maintains a label mapping for an FEC learned from a neighbor that is not
its next hop.

·     Liberal label retention—Retains a received label mapping for an FEC regardless of whether
the advertising LSR is the next hop of the FEC. This mechanism allows for
quicker adaptation to topology changes, but it wastes system resources because LDP
has to keep useless labels. The device only supports liberal label retention.

·     Conservative label
retention—Retains a received label mapping for an
FEC only when the advertising LSR is the next hop of the FEC. This mechanism saves
label resources, but it cannot quickly adapt to topology changes.

### LDP GR

LDP Graceful Restart (GR) preserves label
forwarding information when the signaling protocol or control plane fails, so
that LSRs can still forward packets according to forwarding entries.

As shown in[Figure 4](#_Ref421198494), GR
defines the following roles:

·     GR restarter—An LSR that performs GR. It must be GR-capable.

·     GR helper—A neighbor LSR that helps the GR restarter to complete GR.

The device can act as a GR restarter or a
GR helper.

Figure 4 LDP GR

![](https://resource.h3c.com/en/202407/12/20240712_11705105_x_Img_x_png_3_2216048_294551_0.png)

 

As shown in [Figure 5](#_Ref342479932), LDP GR operates as
follows:

**1\.**LSRs establish an LDP session. The L flag of
the Fault Tolerance TLV in their Initialization messages is set to 1 to indicate
that they support LDP GR.

**2\.**When LDP restarts, the GR restarter starts the
MPLS Forwarding State Holding timer, and marks the MPLS forwarding entries as
stale. When the GR helper detects that the LDP session to the GR restarter goes
down, it performs the following operations:

**a.**Marks the FEC-label mappings learned from
the session as stale.

**b.**Starts the Reconnect timer received from the
GR restarter.

**3\.**After LDP completes restart, the GR
restarter re-establishes an LDP session to the GR helper.

¡     If
the LDP session is not set up before the Reconnect timer expires, the GR helper
deletes the stale FEC-label mappings and the corresponding MPLS forwarding
entries. 

¡     If
the LDP session is successfully set up before the Reconnect timer expires, the
GR restarter sends the remaining time of the MPLS Forwarding State Holding
timer to the GR helper.

The remaining time is sent as the LDP Recovery
time.

**4\.**After the LDP session is re-established, the
GR helper starts the LDP Recovery timer.

**5\.**The GR restarter and the GR helper exchange
label mappings and update their MPLS forwarding tables.

The GR restarter compares each received
label mapping against stale MPLS forwarding entries. If a match is found, the
restarter deletes the stale mark for the matching entry. Otherwise, it adds a
new entry for the label mapping.

The GR helper compares each received
label mapping against stale FEC-label mappings. If a match is found, the helper
deletes the stale mark for the matching mapping. Otherwise, it adds the
received FEC-label mapping and a new MPLS forwarding entry for the mapping.

**6\.**When the MPLS Forwarding State Holding timer
expires, the GR restarter deletes all stale MPLS forwarding entries.

**7\.**When the LDP Recovery timer expires, the GR
helper deletes all stale FEC-label mappings.

Figure 5 LDP GR operation

![](https://resource.h3c.com/en/202407/12/20240712_11705106_x_Img_x_png_4_2216048_294551_0.png)

 

### LDP NSR

LDP NSR ensures nonstop services of LDP
when LDP has redundant processes on multiple MPUs. In contrast to Graceful
Restart, NSR does not require an LDP peer to recover MPLS forwarding
information.

LDP NSR backs up protocol states and data (including
LDP session and LSP information) from the active process to the standby process.
When the LDP active process fails, the standby process becomes active and takes
over processing seamlessly. The LDP peers are not notified of the LDP
interruption. The LDP session stays in Operational state, and the forwarding is
not interrupted.

The LDP active process fails when one of
the following situations occurs:

·     The active process restarts.

·     The LDP process is operating at a different
location than the one determined by the process placement feature.

### LDP-IGP synchronization

#### Basic operating mechanism

LDP establishes LSPs based on the IGP
optimal route. If LDP is not synchronized with IGP, MPLS traffic forwarding
might be interrupted.

LDP is not synchronized with IGP when one
of the following situations occurs:

·     A link is up, and IGP advertises and uses this
link. However, LDP LSPs on this link have not been established.

·     An LDP session on a link is down, and LDP LSPs
on the link have been removed. However, IGP still uses this link.

·     The Ordered label
distribution control mode is used. IGP used the link before the local device received
the label mappings from the downstream LSR to establish LDP LSPs.

After LDP-IGP synchronization is enabled, IGP
advertises the actual cost of a link only when LDP convergence on the link is
completed. Before LDP convergence is completed, IGP advertises the maximum cost
of the link. In this way, the link is visible on the IGP topology, but IGP does
not select this link as the optimal route when other links are available. Therefore,
the device can avoid discarding MPLS packets when there is not an LDP LSP established
on the optimal route.

LDP convergence on a link is completed when
both the following situations occur:

·     The local device establishes an LDP session to a
minimum of one peer, and the LDP session is already in Operational
state.

·     The local device has distributed the label
mappings to a minimum of one peer.

#### Notification delay for LDP convergence completion

By default, LDP immediately sends a
notification to IGP that LDP convergence has completed. However, immediate
notifications might cause MPLS traffic forwarding interruptions in one of the
following scenarios:

·     LDP peers use the Ordered
label distribution control mode. The device has not received a label mapping
from downstream at the time LDP notifies IGP that LDP convergence has
completed.

·     A large number of label mappings are distributed
from downstream. Label advertisement is not completed when LDP notifies IGP
that LDP convergence has completed.

To avoid traffic forwarding interruptions in
these scenarios, configure the notification delay. When LDP convergence on a
link is completed, LDP waits before notifying IGP.

#### Notification delay for LDP restart or active/standby switchover

When an LDP restart or an active/standby switchover
occurs, LDP takes time to converge, and LDP notifies IGP of the LDP-IGP synchronization
status as follows:

·     If a notification delay is not configured, LDP
immediately notifies IGP of the current synchronization states during
convergence, and then updates the states after LDP convergence. This could
impact IGP processing.

·     If a notification delay is configured, LDP
notifies IGP of the LDP-IGP synchronization states in bulk when one of the
following events occurs:

¡     LDP
recovers to the state before the restart or switchover.

¡     The
maximum delay timer expires.

### LDP FRR

#### LDP LFA FRR

A link or router failure on a path can
cause packet loss until LDP establishes a new LSP on the new path. LDP Loop
Free Alternate (LFA) FRR enables fast rerouting to minimize the failover time.
LDP FRR is based on IP FRR and is enabled automatically after IP FRR is
enabled.

You can use one of the following methods to
enable IP FRR:

·     Configure an IGP to automatically calculate a
backup next hop.

·     Configure an IGP to specify a backup next hop by
using a routing policy.

As shown in[Figure 6](#_Ref421198637),
configure IP FRR on LSR A. The IGP automatically calculates a backup next hop or
it specifies a backup next hop through a routing policy. LDP creates a primary
LSP and a backup LSP according to the primary route and the backup route
calculated by IGP. When the primary LSP operates correctly, it forwards the
MPLS packets. When the primary LSP fails, LDP directs packets to the backup
LSP.

When packets are forwarded through the
backup LSP, IGP calculates the optimal path based on the new network topology.
When IGP route convergence occurs, LDP establishes a new LSP according to the
optimal path. If a new LSP is not established after IGP route convergence,
traffic forwarding might be interrupted. As a best practice, enable LDP-IGP
synchronization to work with LDP LFA FRR to reduce traffic interruption.

Figure 6 Network diagram for LDP LFA FRR

![](https://resource.h3c.com/en/202407/12/20240712_11705107_x_Img_x_png_5_2216048_294551_0.png)

 

#### LDP remote LFA FRR

LDP LFA FRR might not be able to compute
backup paths for full backup coverage on a large-scale network. To address this
issue, you can deploy LDP remote LFA FRR.

As shown in [Figure 7](#_Ref531696829), P 1 is
the source node. P 2 is the destination node. The primary LDP LSP is P 1—P 2\.
LDP remote LFA FRR can establish a backup LDP LSP (P 1—P 4—P 2\) to protect the
primary LDP LSP, as follows:

**1\.**The IGP calculates the PQ node (P 4\) using
the remote LFA algorithm.

**2\.**LDP does the following:

**a.**Automatically creates the remote peer
according to the PQ node address.

**b.**Establishes the remote LDP session between
the source node and the PQ node.

**c.**Assigns a label to the destination address
on the session so as to establish the remote LFA FRR LSP (P 1—P 4—P 2).

When the primary LDP LSP fails, P 1
instantly switches traffic to the remote LFA FRR LSP to minimize traffic loss.
For more information about remote LFA, see IS-IS configuration in Layer 3—IP Routing Configuration Guide.

Figure 7 LDP remote LFA FRR

![](https://resource.h3c.com/en/202407/12/20240712_11705108_x_Img_x_png_6_2216048_294551_0.png)

 

### Protocols

·     RFC 5036, LDP
Specification

## Restrictions and guidelines: LDP configuration

Before configuring MPLS, perform the
following tasks:

·     In standalone mode, execute the switch-mode 3 command to switch the operating mode of the device to MPLS, and then
restart the device.

·     In IRF mode, execute the switch-mode 4 command to switch the operating mode of the device to MPLS-IRF, and
then restart the IRF fabric.

## LDPtasks at a glance

To configure LDP, perform the following
tasks:

**1\.**[Enabling LDP](#_Ref473980285)

**2\.**(Optional.) Tuning and optimizing LDP

¡     [Configuring Hello parameters](#_Ref473980286)

¡     [Configuring LDP session parameters](#_Ref473980287)

¡     [Configuring LDP backoff](#_Ref385939222)

¡     [Setting a DSCP value for outgoing LDP
packets](#_Ref473980288)

**3\.**(Optional.) Tuning and controlling LSP
generation

¡     [Configuring LDP to redistribute BGP unicast
routes](#_Ref473980289)

¡     [Configuring an LSP generation policy](#_Ref373770016)

¡     [Configuring a label mapping propagation
policy](#_Ref25241188)

**4\.**(Optional.) Distributing and managing LDP
labels

¡     [Configuring a label mapping propagation
policy](#_Ref35852885)

¡     [Configuring the LDP label distribution
control mode](#_Ref317499043)

¡     [Configuring a label advertisement policy](#_Ref315769110)

¡     [Configuring a label acceptance policy](#_Ref473980495)

**5\.**(Optional.) [Configuring LDP MD5 authentication](#_Ref473980501)

**6\.**(Optional.) [Configuring LDP loop detection parameters](#_Ref317499077)

This feature is applicable to an MPLS
network where most of the devices do not support the TTL mechanism, such as ATM
switches.

**7\.**(Optional.) Enhancing LDP availability

¡     [Configuring LDP session protection](#_Ref473980577)

¡     [Configuring LDP GR](#_Ref473980580)

¡     [Configuring LDP NSR](#_Ref473980585)

¡     [Configuring LDP-IGP synchronization](#_Ref365364875)

¡     [Configuring LDP FRR](#_Ref373770230)

**8\.**(Optional.) [Enabling SNMP notifications for LDP](#_Ref353278460)

## Enabling LDP

### Restrictions and guidelines for enabling LDP

To enable LDP, you must first enable LDP
globally and then enable LDP on relevant interfaces.

### Enabling LDP globally

**1\.**Enter system view.

system-view

**2\.**Enable LDP for the local node or for a VPN.

¡     Enable
LDP for the local node and enter LDP view.

mpls ldp

¡     Execute
the following commands in sequence to enable LDP for a VPN, and then enter
LDP-VPN instance view:

mpls ldp

vpn-instance vpn-instance-name

By default, LDP is globally disabled.

**3\.**Configure an LDP LSR ID.

lsr-id lsr-id

By default, the LDP LSR ID is the same as
the MPLS LSR ID.

### Enabling LDP on an interface

**1\.**Enter system view.

system-view

**2\.**Enter interface view.

interface interface-type interface-number

If the interface is bound to a VPN
instance, you must enable LDP for the VPN instance by using the vpn-instance command in LDP view.

**3\.**Enable IPv4 LDP on the interface.

mpls ldp enable

By default, IPv4 LDP is disabled on an
interface.

## Configuring Hello parameters

### About hello timers

LDP has the following hello timers:

·     Link Hello hold time and Link Hello interval.

·     Targeted Hello hold time and Targeted Hello interval
for a peer.

### Restrictions and guidelines for hello parameter configuration

Changes to hello parameters do not take
effect on established LDP sessions. To apply the changes to existing LDP
sessions on a network, you must use the reset mpls ldp command to reset all LDP sessions on that network.

### Setting Link Hello timers

**1\.**Enter system view.

system-view

**2\.**Enter the view of the interface where you
want to establish an LDP session.

interface interface-type interface-number

**3\.**Set the Link Hello hold time.

mpls ldp timer hello-hold timeout

By default, the Link Hello hold time is
15 seconds.

**4\.**Set the Link Hello interval.

mpls ldp timer hello-interval interval

By default, the Link Hello interval is 5
seconds.

### Setting Targeted Hello timers for an LDP peer

**1\.**Enter system view.

system-view

**2\.**Enter LDP view.

mpls ldp

**3\.**Specify an LDP peer and enter LDP peer view.
The device will send unsolicited Targeted Hellos to the peer and can respond to
the Targeted Hellos received from the peer.

targeted-peer ipv4-address \[ peer-name ]

By default, the device does not send
Targeted Hellos to a peer, or respond to Targeted Hellos received from a peer.

**4\.**Set the Targeted Hello hold time.

mpls ldp timer hello-hold timeout

By default, the Targeted Hello hold time
is 45 seconds.

**5\.**Set the Target Hello interval.

mpls ldp timer hello-interval interval

By default, the Targeted Hello interval
is 15 seconds.

## Configuring LDP session parameters

### About LDP session parameters

An LDP session has the following parameters:

·     Keepalive hold time and Keepalive interval.

·     LDP transport address—IP address for
establishing TCP connections.

LDP uses Basic Discovery and Extended Discovery
mechanisms to discovery LDP peers and establish LDP sessions with them.

### Restrictions and guidelines for configuring LDP session parameters

When you configure LDP session parameters,
follow these guidelines:

·     The configured LDP transport address must be the
IP address of an up interface on the device. Otherwise, no LDP session can be
established.

·     Make sure the LDP transport addresses of the
local and peer LSRs can reach each other. Otherwise, no TCP connection can be
established.

·     Changes to LDP session parameters do not take
effect on established LDP sessions. To apply the changes to existing LDP
sessions on a network, you must use the reset mpls ldp command to reset all LDP sessions on that network.

### Configuring LDP sessions parameters for Basic Discovery mechanism

**1\.**Enter system view.

system-view

**2\.**Enter the view of the LDP session interface (the
interface where you want to establish an LDP session).

interface interface-type interface-number

**3\.**Set the Keepalive hold time.

mpls ldp timer keepalive-hold timeout

By default, the Keepalive hold time is 45
seconds.

**4\.**Set the Keepalive interval.

mpls ldp timer keepalive-interval interval

By default, the Keepalive interval is 15
seconds.

**5\.**Configure the IPv4 LDP transport address.

mpls ldp transport-address { ipv4-address
\| interface }

The default LDP IPv4 transport address is
the local LSR ID if the interface where you want to establish an LDP session
belongs to the public network. If the interface belongs to a VPN, the default LDP
IPv4 transport address is the primary IP address of the interface.

If the LDP session interface belongs to a
VPN instance, the interface with the IP address specified by this command must
belong to the same VPN instance.

### Configuring LDP sessions parameters for Extended Discovery mechanism

**1\.**Enter system view.

system-view

**2\.**Enter LDP view.

mpls ldp

**3\.**Specify an IPv4 LDP peer and enter LDP peer
view. The device will send unsolicited IPv4 Targeted Hellos to the peer and can
respond to IPv4 Targeted Hellos received from the targeted peer.

targeted-peer ipv4-address \[ peer-name ]

By default, the device does not send IPv4
Targeted Hellos to a peer, or respond to IPv4 Targeted Hellos received from a
peer.

**4\.**Set the Keepalive hold time.

mpls ldp timer keepalive-hold timeout

By default, the Keepalive hold time is 45
seconds.

**5\.**Set the Keepalive interval.

mpls ldp timer keepalive-interval interval

By default, the Keepalive interval is 15
seconds.

**6\.**Configure the LDP transport address.

mpls ldp transport-address ipv4-address

By default, the LDP transport address is
the LSR ID of the local device.

## Configuring LDP backoff

#### About this task

If LDP session parameters (for example, the
label advertisement mode) are incompatible, two LDP peers cannot establish a
session, and they will keep negotiating with each other.

The LDP backoff mechanism can mitigate this
problem by using an initial delay timer and a maximum delay timer. After failing
to establish a session to a peer LSR for the first time, LDP does not start an
attempt until the initial delay timer expires. If the session setup fails
again, LDP waits for two times the initial delay before the next attempt, and
so forth until the maximum delay time is reached. After that, the maximum delay
time will always take effect.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter LDP view or enter LDP-VPN instance
view.

¡     Enter
LDP view:

mpls ldp

¡     Execute
the following commands in sequence to enter LDP-VPN instance view:

mpls ldp

vpn-instance vpn-instance-name

**3\.**Set the initial delay time and maximum delay
time.

backoff initial initial-time maximum maximum-time

By default, the initial delay time is 15
seconds, and the maximum delay time is 120 seconds.

## Setting a DSCP value for outgoing LDP packets

#### About this task

To control the transmission preference of
outgoing LDP packets, set a DSCP value for outgoing LDP packets.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter LDP view.

mpls ldp

**3\.**Set a DSCP value for outgoing LDP packets.

dscp dscp-value

By default, the DSCP value for outgoing
LDP packets is 48\.

## Configuring LDP to redistribute BGP unicast routes

#### About this task

By default, LDP automatically redistributes
IGP routes, including the BGP routes that have been redistributed into IGP. Then,
LDP assigns labels to the IGP routes and labeled BGP routes, if these routes
are permitted by an LSP generation policy. LDP does not automatically
redistribute BGP unicast routes if the routes are not redistributed into the
IGP.

For example, on a carrier's carrier network
where IGP is not configured between a PE of a Level 1 carrier and a CE of a
Level 2 carrier, LDP cannot redistribute BGP unicast routes to assign labels to
them. For this network to operate correctly, you can enable LDP to redistribute
BGP unicast routes. If the routes are permitted by an LSP generation policy,
LDP assigns labels to them to establish LSPs. For more information about
carrier's carrier, see "Configuring MPLS
L3VPN."

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter LDP view or enter LDP-VPN instance
view.

¡     Enter
LDP view.

mpls ldp

¡     Execute
the following commands in sequence to enter LDP-VPN instance view:

mpls ldp

vpn-instance vpn-instance-name

**3\.**Enable LDP to redistribute BGP IPv4 unicast
routes.

import bgp \[ as-number ]

By default, LDP does not redistribute BGP
IPv4 unicast routes.

## Configuring an LSP generation policy

#### About this task

LDP assigns labels to the routes that have
been redistributed into LDP to generate LSPs. An LSP generation policy specifies
which redistributed routes can be used by LDP to generate LSPs to control the
number of LSPs, as follows:

·     Use all routes to establish LSPs. 

On loopback interfaces, only routes with
a 32-bit mask can trigger LDP to establish LSPs.

·     Use the routes permitted by an IP prefix list to
establish LSPs. For information about IP prefix list configuration, see Layer 3—IP Routing Configuration Guide.

·     Use only IPv4 host routes with a 32-bit mask to
establish LSPs.

#### Restrictions and guidelines

By default, LDP uses only IPv4 host routes
with a 32-bit mask to establish LSPs. The other two methods can result in more
LSPs than the default policy. To change the policy, make sure the system
resources and bandwidth resources are sufficient.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter LDP view or enter LDP-VPN instance
view.

¡     Enter
LDP view.

mpls ldp

¡     Execute
the following commands in sequence to enter LDP-VPN instance view:

mpls ldp

vpn-instance vpn-instance-name

**3\.**Configure an IPv4 LSP generation policy.

lsp-trigger { all \| prefix-list prefix-list-name }

By default, LDP uses only the
redistributed IPv4 routes with a 32-bit mask to establish LSPs.

## Configuring a label mapping propagation policy

#### About this task

In an LDP network, LSRs establish LSPs by
advertising label mapping messages. When a PE acts as both an ingress LSR and a
transit LSR, there might be a large number of transit LSPs on the PE. You can
configure a label mapping propagation policy on the PE to reduce the number of transit
LSPs and thus save network resources. A label mapping propagation policy allows
the PE to advertise only permitted label mappings to the peers to establish
transit LSPs.

#### Restrictions and guidelines

If you execute both the lsp-trigger and propagate mapping
commands, establishment of transit LSPs is controlled by the propagate mapping command.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter LDP view or LDP-VPN instance view.

¡     Enter
LDP view.

mpls ldp

¡     Execute
the following commands in sequence to enter LDP-VPN instance view:

mpls ldp

vpn-instance vpn-instance-name

**3\.**Configure a label mapping propagation
policy.

propagate mapping { none \| prefix-list prefix-list-name }

By default, no label mapping propagation
policy is configured. The device advertises all IPv4 prefix label mappings
permitted by the LSP generation policy.

## Configuring the LDP label distribution control mode

**1\.**Enter system view.

system-view

**2\.**Enter LDP view or enter LDP-VPN instance
view.

¡     Enter
LDP view.

mpls ldp

¡     Execute
the following commands in sequence to enter LDP-VPN instance view:

mpls ldp

vpn-instance vpn-instance-name

**3\.**Configure the label distribution control
mode.

label-distribution { independent \| ordered }

By default, the Ordered label
distribution mode is used.

## Configuring a label advertisement policy

#### About this task

A label advertisement policy uses IP prefix
lists to control the FEC-label mappings advertised to peers. 

As shown in [Figure 8](#_Ref315769111), LSR A
advertises label mappings for FECs permitted by IP prefix list B to LSR B. It advertises
label mappings for FECs permitted by IP prefix list C to LSR C.

Figure 8 Label advertisement control diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705109_x_Img_x_png_7_2216048_294551_0.png)

 

#### Restrictions and guidelines

A label advertisement policy on an LSR and
a label acceptance policy on its upstream LSR can achieve the same purpose. As
a best practice, use label advertisement policies to reduce network load if
downstream LSRs support label advertisement control.

Before you configure an LDP label advertisement
policy, create an IP prefix list. For information about IP prefix list
configuration, see routing policy configuration in Layer
3—IP Routing Configuration Guide.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter LDP view or enter LDP-VPN instance
view.

¡     Enter
LDP view.

mpls ldp

¡     Execute
the following commands in sequence to enter LDP-VPN instance view:

mpls ldp

vpn-instance vpn-instance-name

**3\.**Configure an IPv4 label advertisement policy.

advertise-label prefix-list prefix-list-name \[ peer peer-prefix-list-name ]

By default, LDP advertises all IPv4
FEC-label mappings permitted by the LSP generation policy to all peers.

## Configuring a label acceptance policy

#### About this task

A label acceptance policy uses an IP prefix
list to control the label mappings received from a peer.

As shown in [Figure 9](#_Ref234642614), LSR A
uses an IP prefix list to filter label mappings from LSR B, and it does not
filter label mappings from LSR C.

Figure 9 Label acceptance control diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705110_x_Img_x_png_8_2216048_294551_0.png)

 

#### Restrictions and guidelines

A label advertisement policy on an LSR and
a label acceptance policy on its upstream LSR can achieve the same purpose. As
a best practice, use the label advertisement policy to reduce network load.

You must create an IP prefix list before
you configure a label acceptance policy. For information about IP prefix list
configuration, see routing policy configuration in Layer
3—IP Routing Configuration Guide.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter LDP view or enter LDP-VPN instance
view.

¡     Enter
LDP view.

mpls ldp

¡     Execute
the following commands in sequence to enter LDP-VPN instance view:

mpls ldp

vpn-instance vpn-instance-name

**3\.**Configure an IPv4 label acceptance policy.

accept-label peer peer-lsr-id prefix-list prefix-list-name

By default, LDP accepts all IPv4
FEC-label mappings.

## Configuring LDP MD5 authentication

#### About this task

To improve security for LDP sessions, you
can configure MD5 authentication for the underlying TCP connections to check
the integrity of LDP messages.

#### Restrictions and guidelines

For two LDP peers to establish an LDP
session successfully, make sure the LDP MD5 authentication configuration on the
LDP peers are consistent.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter LDP view or enter LDP-VPN instance
view.

¡     Enter
LDP view.

mpls ldp

¡     Execute
the following commands in sequence to enter LDP-VPN instance view:

mpls ldp

vpn-instance vpn-instance-name

**3\.**Enable LDP MD5 authentication.

md5-authentication peer-lsr-id { cipher \| plain } string

By default, LDP MD5 authentication is
disabled.

## Configuring LDP loop detection parameters

#### About this task

The device does not support LDP loop
detection. However, you must configure LDP loop detection parameters on the
device so the device can establish an LDP session with a neighbor in the
following situation:

·     The neighbor device supports LDP loop detection.

·     The neighbor device requires both devices of the
LDP session to advertise the same loop detection settings during LDP session
negotiation.

#### Restrictions and guidelines

Configuring LDP loop detection parameters
enables the device to have the loop detection negotiation capability during LDP
session establishment. The device still cannot perform LDP loop detection.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter LDP view or enter LDP-VPN instance
view.

¡     Enter
LDP view.

mpls ldp

¡     Execute
the following commands in sequence to enter LDP-VPN instance view:

mpls ldp

vpn-instance vpn-instance-name

**3\.**Enable loop detection.

loop-detect

By default, loop detection is disabled.

**4\.**(Optional.) Set the maximum hop count.

maxhops hop-number

By default, the maximum hop count is 32\.

**5\.**(Optional.) Set the path vector limit.

pv-limit pv-number

By default, the path vector limit is 32\.

## Configuring LDP session protection

#### About this task

If two LDP peers have both a direct link
and an indirect link in between, you can configure this feature to protect
their LDP session when the direct link fails. 

LDP establishes both a Link Hello adjacency
over the direct link and a Targeted Hello adjacency over the indirect link with
the peer. When the direct link fails, LDP deletes the Link Hello adjacency but still
maintains the Targeted Hello adjacency. In this way, the LDP session between
the two peers is kept available, and the FEC-label mappings based on this
session are not deleted. When the direct link recovers, the LDP peers do not
need to re-establish the LDP session or re-learn the FEC-label mappings.

When you enable the session protection feature,
you can also specify the session protection duration. If the Link Hello adjacency
does not recover within the duration, LDP deletes the Targeted Hello adjacency
and the LDP session. If you do not specify the session protection duration, the
two peers will always maintain the LDP session over the Targeted Hello adjacency.

#### Restrictions and guidelines

LDP session protection is applicable only to
IPv4 networks.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter LDP view.

mpls ldp

**3\.**Enable the session protection feature.

session protection \[ duration time ] \[ peer peer-prefix-list-name ]

By default, session protection is
disabled.

## Configuring LDP GR

#### Prerequisites

Before you configure LDP GR, enable LDP on
the GR restarter and GR helpers.

#### Restrictions and guidelines

The LDP GR configuration is required only
on a GR restarter. Because the role (GR restarter or helper) of a device in a
GR process is unpredictable, configure LDP GR on all involved devices as a best
practice.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter LDP view.

mpls ldp

**3\.**Enable LDP GR.

graceful-restart

By default, LDP GR is disabled.

**4\.**(Optional.) Set the Reconnect timer for LDP
GR.

graceful-restart timer reconnect reconnect-time

By default, the Reconnect time is 120
seconds.

**5\.**(Optional.) Set the MPLS Forwarding State
Holding timer for LDP GR.

graceful-restart timer forwarding-hold hold-time

By default, the MPLS Forwarding State
Holding time is 180 seconds.

## Configuring LDP NSR

**1\.**Enter system view.

system-view

**2\.**Enter LDP view.

mpls ldp

**3\.**Enable LDP NSR.

non-stop-routing

By default, LDP NSR is disabled.

## Configuring LDP-IGP synchronization

### Restrictions and guidelines for LDP-IGP synchronization

After you enable LDP-IGP synchronization
for an OSPF process, OSPF area, or an IS-IS process, LDP-IGP synchronization is
enabled on the OSPF process interfaces or the IS-IS process interfaces.

You can execute the mpls ldp igp
sync disable command to disable LDP-IGP synchronization
on interfaces where LDP-IGP synchronization is not required.

LDP-IGP synchronization protection is only applicable
to an IPv4 network.

LDP-IGP synchronization is not supported
for an OSPF process and its OSPF areas if the OSPF process belongs to a VPN
instance.

LDP-IGP synchronization is not supported
for an IS-IS process that belongs to a VPN instance.

### Configuring LDP-OSPF synchronization

#### Configuring LDP-OSPF synchronization for an OSPF process

**1\.**Enter system view.

system-view

**2\.**Enter OSPF view.

ospf \[ process-id \| router-id router-id ] \*

**3\.**Enable LDP-OSPF synchronization.

mpls ldp sync

By default, LDP-OSPF synchronization is
disabled.

**4\.**(Optional.) Disable LDP-OSPF synchronization
on an interface:

**a.**Return to system view.

quit

**b.**Enter interface view.

interface interface-type interface-number

**c.**Disable LDP-IGP synchronization on the
interface.

mpls ldp igp sync disable

By default, LDP-IGP synchronization is
enabled on an interface.

**5\.**(Optional.) Configure LDP-OSPF
synchronization parameters:

**a.**Return to system view.

quit

**b.**Enter LDP view.

mpls ldp

**c.**Set the delay for LDP to notify IGP of the
LDP convergence.

igp sync delay time

By default, LDP immediately notifies IGP
of the LDP convergence completion.

**d.**Set the maximum delay for LDP to notify IGP
of the LDP-IGP synchronization status after an LDP restart or active/standby
switchover.

igp sync delay on-restart time

By default, the maximum notification
delay is 90 seconds.

#### Configuring LDP-OSPF synchronization for an OSPF area

**1\.**Enter system view.

system-view

**2\.**Enter OSPF view.

ospf \[ process-id \| router-id router-id ] \*

**3\.**Enter area view.

area area-id

**4\.**Enable LDP-OSPF synchronization.

mpls ldp sync

By default, LDP-OSPF synchronization is
disabled.

**5\.**(Optional.) Disable LDP-OSPF synchronization
on an interface:

**a.**Return to system view.

quit

**b.**Enter interface view.

interface interface-type interface-number

**c.**Disable LDP-IGP synchronization on the
interface.

mpls ldp igp sync disable

By default, LDP-IGP synchronization is
enabled on an interface.

**6\.**(Optional.) Configure LDP-OSPF
synchronization parameters:

**a.**Return to system view.

quit

**b.**Enter LDP view.

mpls ldp

**c.**Set the delay for LDP to notify IGP of the
LDP convergence.

igp sync delay time

By default, LDP immediately notifies IGP
of the LDP convergence completion.

**d.**Set the maximum delay for LDP to notify IGP
of the LDP-IGP synchronization status after an LDP restart or active/standby
switchover.

igp sync delay on-restart time

By default, the maximum notification
delay is 90 seconds.

### Configuring LDP IS-IS synchronization

**1\.**Enter system view.

system-view

**2\.**Enter IS-IS view.

isis \[ process-id ]

**3\.**Enable LDP-ISIS synchronization.

mpls ldp sync \[ level-1 \| level-2 ]

By default, LDP-ISIS synchronization is
disabled.

**4\.**(Optional.) Disable LDP-ISIS synchronization
on an interface:

**a.**Return to system view.

quit

**b.**Enter interface view.

interface interface-type interface-number

**c.**Disable LDP-IGP synchronization on the
interface.

mpls ldp igp sync disable

By default, LDP-IGP synchronization is
enabled on an interface.

**5\.**(Optional.) Configure LDP-IGP
synchronization parameters:

**a.**Return to system view.

quit

**b.**Enter LDP view.

mpls ldp

**c.**Set the delay for LDP to notify IGP of the
LDP convergence completion.

igp sync delay time

By default, LDP immediately notifies IGP
of the LDP convergence completion.

**d.**Set the maximum delay for LDP to notify IGP
of the LDP-IGP synchronization status after an LDP restart or an active/standby
switchover occurs.

igp sync delay on-restart time

By default, the maximum notification
delay is 90 seconds.

## Configuring LDP FRR

### Configuring LDP LFA FRR

LDP FRR is based on IP FRR, and is enabled automatically
after IP FRR is enabled. For information about configuring IP FRR, see Layer 3—IP Routing Configuration Guide.

### Configuring LDP remote LFA FRR

#### Restrictions and guidelines

In a remote LFA network, configure this
feature on the PQ nodes.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter LDP view.

mpls ldp

**3\.**Enable the device to automatically set up
targeted LDP sessions on receiving Targeted Hellos.

accept target-hello { all \| prefix-list prefix-list-name }

By default, the device does not
automatically establish targeted LDP sessions when it receives Targeted Hellos.

## Enabling SNMP notifications for LDP

#### About this task

This command enables generating SNMP notifications
for LDP upon LDP session changes, as defined in RFC 3815\. For LDP event
notifications to be sent correctly, you must also configure SNMP on the device.
For more information about SNMP configuration, see the network management and monitoring
configuration guide for the device.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enable SNMP notifications for LDP.

snmp-agent trap enable ldp

By default, SNMP notifications for LDP
are enabled.

## Display and maintenance commands for LDP

Execute display commands
in any view and reset commands in user
view.

 

| Task | Command |
| --- | --- |
| Display LDP discovery information. | display mpls ldp discovery \[ vpn-instance vpn-instance-name ] \[ interface interface-type interface-number \| peer peer-lsr-id \| targeted-peer ipv4-address ] \[ verbose ] || Display error TCP packets received by the LDP session. | display mpls ldp error-packet || Display LDP FEC-label mapping information. | display mpls ldp fec \[ vpn-instance vpn-instance-name ] \[ ipv4-address mask-length \| summary ] || Display LDP-IGP synchronization information. | display mpls ldp igp sync \[ interface interface-type interface-number ] || Display LDP interface information. | display mpls ldp interface \[ vpn-instance vpn-instance-name ] \[ interface-type interface-number ] || Display LDP LSP information. | display mpls ldp lsp \[ vpn-instance vpn-instance-name ] \[ ipv4-address mask-length ] || Display LDP running parameters. | display mpls ldp parameter \[ vpn-instance vpn-instance-name ] || Display LDP peer and session information. | display mpls ldp peer \[ vpn-instance vpn-instance-name ] \[ peer-lsr-id ] \[ verbose ] || Display LDP summary information. | display mpls ldp summary \[ all \| vpn-instance vpn-instance-name ] || Display information about LDP session interruptions. | display mpls ldp troubleshooting || Reset LDP sessions. | reset mpls ldp \[ vpn-instance vpn-instance-name ] \[ peer peer-id ] |











 

## IPv4 LDP configuration examples

### Example: Configuring LDP LSPs

#### Network configuration

Switch A, Switch B, and Switch C all
support MPLS.

Configure LDP to establish LSPs between Switch
A and Switch C, so subnets 11.1.1.0/24 and 21.1.1.0/24 can reach each other
over MPLS.

Configure LDP to establish LSPs only for destinations
1.1.1.9/32, 2.2.2.9/32, 3.3.3.9/32, 11.1.1.0/24, and 21.1.1.0/24 on Switch A,
Switch B, and Switch C.

Figure 10 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705111_x_Img_x_png_9_2216048_294551_0.png)

 

#### Requirements analysis

·     To ensure that the LSRs establish IPv4 LSPs
automatically, enable IPv4 LDP on each LSR.

·     To establish IPv4 LDP LSPs, configure an IPv4
routing protocol to ensure IP connectivity between the LSRs. This example uses
OSPF.

·     To control the number of IPv4 LSPs, configure an
IPv4 LSP generation policy on each LSR.

#### Procedure

**1\.**Configure IP addresses and masks for
interfaces, including the loopback interfaces, as shown in [Figure 10](#_Ref324857874).
(Details not shown.)

**2\.**Configure OSPF on each switch to ensure IP
connectivity between them:

\# Configure Switch A.

\<SwitchA\> system-view

\[SwitchA] ospf

\[SwitchA-ospf-1] area 0

\[SwitchA-ospf-1-area-0.0.0.0]
network 1.1.1.9 0.0.0.0

\[SwitchA-ospf-1-area-0.0.0.0]
network 10.1.1.0 0.0.0.255

\[SwitchA-ospf-1-area-0.0.0.0]
network 11.1.1.0 0.0.0.255

\[SwitchA-ospf-1-area-0.0.0.0]
quit

\[SwitchA-ospf-1] quit

\# Configure Switch B.

\<SwitchB\> system-view

\[SwitchB] ospf

\[SwitchB-ospf-1] area 0

\[SwitchB-ospf-1-area-0.0.0.0]
network 2.2.2.9 0.0.0.0

\[SwitchB-ospf-1-area-0.0.0.0]
network 10.1.1.0 0.0.0.255

\[SwitchB-ospf-1-area-0.0.0.0]
network 20.1.1.0 0.0.0.255

\[SwitchB-ospf-1-area-0.0.0.0]
quit

\[SwitchB-ospf-1] quit

\# Configure Switch C.

\<SwitchC\> system-view

\[SwitchC] ospf

\[SwitchC-ospf-1] area 0

\[SwitchC-ospf-1-area-0.0.0.0]
network 3.3.3.9 0.0.0.0

\[SwitchC-ospf-1-area-0.0.0.0]
network 20.1.1.0 0.0.0.255

\[SwitchC-ospf-1-area-0.0.0.0]
network 21.1.1.0 0.0.0.255

\[SwitchC-ospf-1-area-0.0.0.0]
quit

\[SwitchC-ospf-1] quit

\# Verify that the switches have learned the
routes to each other. This example uses Switch A.

\[SwitchA] display ip routing-table

 

Destinations : 21       
Routes : 21

 

Destination/Mask   Proto   Pre
Cost        NextHop         Interface

0.0.0.0/32         Direct  0  
0           127.0.0.1       InLoop0

1.1.1.9/32         Direct  0  
0           127.0.0.1       InLoop0

2.2.2.9/32         O\_INTRA 10  1           10.1.1.2        Vlan2

3.3.3.9/32         O\_INTRA 10  2           10.1.1.2        Vlan2

10.1.1.0/24        Direct  0  
0           10.1.1.1        Vlan2

10.1.1.0/32        Direct  0  
0           10.1.1.1        Vlan2

10.1.1.1/32        Direct  0  
0           127.0.0.1       InLoop0

10.1.1.255/32      Direct  0  
0           10.1.1.1        Vlan2

11.1.1.0/24        Direct  0  
0           11.1.1.1        Vlan4

11.1.1.0/32        Direct  0  
0           11.1.1.1        Vlan4

11.1.1.1/32        Direct  0  
0           127.0.0.1       InLoop0

11.1.1.255/32      Direct  0  
0           11.1.1.1        Vlan4

20.1.1.0/24        O\_INTRA 10 
2           10.1.1.2        Vlan2

21.1.1.0/24        O\_INTRA 10 
3           10.1.1.2        Vlan2

127.0.0.0/8        Direct  0  
0           127.0.0.1       InLoop0

127.0.0.0/32       Direct  0  
0           127.0.0.1       InLoop0

127.0.0.1/32       Direct  0  
0           127.0.0.1       InLoop0

127.255.255.255/32 Direct  0  
0           127.0.0.1       InLoop0

224.0.0.0/4        Direct  0  
0           0.0.0.0         NULL0

224.0.0.0/24       Direct  0  
0           0.0.0.0         NULL0

255.255.255.255/32 Direct  0  
0           127.0.0.1       InLoop0

**3\.**Enable MPLS and IPv4 LDP:

\# Configure Switch A.

\[SwitchA] mpls lsr-id 1.1.1.9

\[SwitchA] mpls ldp

\[SwitchA-ldp] quit

\[SwitchA] interface
vlan-interface 2

\[SwitchA-Vlan-interface2] mpls
enable

\[SwitchA-Vlan-interface2] mpls
ldp enable

\[SwitchA-Vlan-interface2] quit

\# Configure Switch B.

\[SwitchB] mpls lsr-id 2.2.2.9

\[SwitchB] mpls ldp

\[SwitchB-ldp] quit

\[SwitchB] interface
vlan-interface 2

\[SwitchB-Vlan-interface2] mpls
enable

\[SwitchB-Vlan-interface2] mpls
ldp enable

\[SwitchB-Vlan-interface2] quit

\[SwitchB] interface
vlan-interface 3

\[SwitchB-Vlan-interface3] mpls
enable

\[SwitchB-Vlan-interface3] mpls
ldp enable

\[SwitchB-Vlan-interface3] quit

\# Configure Switch C.

\[SwitchC] mpls lsr-id 3.3.3.9

\[SwitchC] mpls ldp

\[SwitchC-ldp] quit

\[SwitchC] interface
vlan-interface 3

\[SwitchC-Vlan-interface3] mpls
enable

\[SwitchC-Vlan-interface3] mpls
ldp enable

\[SwitchC-Vlan-interface3] quit

**4\.**Configure IPv4 LSP generation policies:

\# On Switch A, create IP prefix list switcha, and
configure LDP to use only the routes permitted by the prefix list to establish
LSPs.

\[SwitchA] ip prefix-list switcha
index 10 permit 1.1.1.9 32

\[SwitchA] ip prefix-list switcha
index 20 permit 2.2.2.9 32

\[SwitchA] ip prefix-list switcha
index 30 permit 3.3.3.9 32

\[SwitchA] ip prefix-list switcha
index 40 permit 11.1.1.0 24

\[SwitchA] ip prefix-list switcha
index 50 permit 21.1.1.0 24

\[SwitchA] mpls ldp

\[SwitchA-ldp] lsp-trigger prefix-list
switcha

\[SwitchA-ldp] quit

\# On Switch B, create IP prefix list switchb, and
configure LDP to use only the routes permitted by the prefix list to establish
LSPs.

\[SwitchB] ip prefix-list switchb
index 10 permit 1.1.1.9 32

\[SwitchB] ip prefix-list switchb
index 20 permit 2.2.2.9 32

\[SwitchB] ip prefix-list switchb
index 30 permit 3.3.3.9 32

\[SwitchB] ip prefix-list switchb
index 40 permit 11.1.1.0 24

\[SwitchB] ip prefix-list switchb
index 50 permit 21.1.1.0 24

\[SwitchB] mpls ldp

\[SwitchB-ldp] lsp-trigger prefix-list
switchb

\[SwitchB-ldp] quit

\# On Switch C, create IP prefix list switchc, and
configure LDP to use only the routes permitted by the prefix list to establish
LSPs.

\[SwitchC] ip prefix-list switchc
index 10 permit 1.1.1.9 32

\[SwitchC] ip prefix-list switchc
index 20 permit 2.2.2.9 32

\[SwitchC] ip prefix-list switchc
index 30 permit 3.3.3.9 32

\[SwitchC] ip prefix-list switchc
index 40 permit 11.1.1.0 24

\[SwitchC] ip prefix-list switchc
index 50 permit 21.1.1.0 24

\[SwitchC] mpls ldp

\[SwitchC-ldp] lsp-trigger prefix-list
switchc

\[SwitchC-ldp] quit

#### Verifying the configuration

\# Display LDP LSP information on the
switches, for example, on Switch A.

\[SwitchA] display mpls ldp lsp

Status Flags: \* \- stale, L \- liberal,
B \- backup, N/A – unavailable

FECs: 5            Ingress: 3          Transit:
3      Egress: 2

 

FEC                In/Out Label        Nexthop        
OutInterface/LSINDEX

1.1.1.9/32         3/-

                   -/1279(L)

2.2.2.9/32         -/3                 10.1.1.2       
Vlan2

                   1279/3              10.1.1.2       
Vlan2

3.3.3.9/32         -/1278              10.1.1.2       
Vlan2

                   1278/1278           10.1.1.2       
Vlan2

11.1.1.0/24        1277/-

                   -/1277(L)

21.1.1.0/24        -/1276              10.1.1.2       
Vlan2

                   1276/1276           10.1.1.2       
Vlan2

\# Test the connectivity of the LDP LSP from
Switch A to Switch C.

\[SwitchA] ping mpls -a 11.1.1.1 ipv4
21.1.1.0 24

MPLS ping FEC 21.1.1.0/24 with 100 bytes
of data:

100 bytes from 20.1.1.2: Sequence\=1
time\=1 ms

100 bytes from 20.1.1.2: Sequence\=2
time\=1 ms

100 bytes from 20.1.1.2: Sequence\=3
time\=8 ms

100 bytes from 20.1.1.2: Sequence\=4
time\=2 ms

100 bytes from 20.1.1.2: Sequence\=5
time\=1 ms

 

\--- Ping statistics for FEC
21.1.1.0/24 \---

5 packets transmitted, 5 packets
received, 0.0% packet loss

Round-trip min/avg/max \= 1/2/8 ms

\# Test the connectivity of the LDP LSP from
Switch C to Switch A.

\[SwitchC] ping mpls -a 21.1.1.1 ipv4
11.1.1.0 24

MPLS ping FEC 11.1.1.0/24 with 100
bytes of data:

100 bytes from 10.1.1.1: Sequence\=1
time\=1 ms

100 bytes from 10.1.1.1: Sequence\=2
time\=1 ms

100 bytes from 10.1.1.1: Sequence\=3
time\=1 ms

100 bytes from 10.1.1.1: Sequence\=4
time\=1 ms

100 bytes from 10.1.1.1: Sequence\=5 time\=1
ms

 

\--- Ping statistics for FEC
11.1.1.0/24 \---

5 packets transmitted, 5 packets
received, 0.0% packet loss

Round-trip min/avg/max \= 1/1/1 ms

### Example: Configuring label acceptance control

#### Network configuration

Two links, Switch A—Switch B—Switch C and Switch
A—Switch D—Switch C, exist between subnets 11.1.1.0/24 and 21.1.1.0/24.

Configure LDP to establish LSPs only for
routes to subnets 11.1.1.0/24 and 21.1.1.0/24.

Configure LDP to establish LSPs only on the
link Switch A—Switch B—Switch C to forward traffic between subnets 11.1.1.0/24
and 21.1.1.0/24.

Figure 11 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705101_x_Img_x_png_10_2216048_294551_0.png)

 

#### Requirements analysis

·     To ensure that the LSRs establish IPv4 LSPs
automatically, enable IPv4 LDP on each LSR.

·     To establish IPv4 LDP LSPs, configure an IPv4
routing protocol to ensure IP connectivity between the LSRs. This example uses
OSPF.

·     To ensure that LDP establishes IPv4 LSPs only
for the routes 11.1.1.0/24 and 21.1.1.0/24, configure IPv4 LSP generation
policies on each LSR.

·     To ensure that LDP establishes IPv4 LSPs only over
the link Switch A—Switch B—Switch C, configure IPv4 label acceptance policies
as follows:

¡     Switch
A accepts only the label mapping for FEC 21.1.1.0/24 received from Switch B. Switch
A denies the label mapping for FEC 21.1.1.0/24 received from Switch D.

¡     Switch
C accepts only the label mapping for FEC 11.1.1.0/24 received from Switch B.
Switch C denies the label mapping for FEC 11.1.1.0/24 received from Switch D.

#### Prerequisites

Before configuration, disable the spanning
tree feature globally or map each VLAN to an MSTI. For more information, see Layer 2—LAN Switching Configuration Guide.

#### Procedure

**1\.**Configure IP addresses and masks for
interfaces, including the loopback interfaces, as shown in [Figure 11](#_Ref324857877).
(Details not shown.)

**2\.**Configure OSPF on each switch to ensure IP
connectivity between them. (Details not shown.)

**3\.**Enable MPLS and IPv4 LDP:

\# Configure Switch A.

\<SwitchA\> system-view

\[SwitchA] mpls lsr-id 1.1.1.9

\[SwitchA] mpls ldp

\[SwitchA-ldp] quit

\[SwitchA] interface
vlan-interface 2

\[SwitchA-Vlan-interface2] mpls
enable

\[SwitchA-Vlan-interface2] mpls
ldp enable

\[SwitchA-Vlan-interface2] quit

\[SwitchA] interface
vlan-interface 6

\[SwitchA-Vlan-interface6] mpls
enable

\[SwitchA-Vlan-interface6] mpls
ldp enable

\[SwitchA-Vlan-interface6] quit

\# Configure Switch B.

\<SwitchB\> system-view

\[SwitchB] mpls lsr-id 2.2.2.9

\[SwitchB] mpls ldp

\[SwitchB-ldp] quit

\[SwitchB] interface
vlan-interface 2

\[SwitchB-Vlan-interface2] mpls
enable

\[SwitchB-Vlan-interface2] mpls
ldp enable

\[SwitchB-Vlan-interface2] quit

\[SwitchB] interface
vlan-interface 3

\[SwitchB-Vlan-interface3] mpls
enable

\[SwitchB-Vlan-interface3] mpls
ldp enable

\[SwitchB-Vlan-interface3] quit

\# Configure Switch C.

\<SwitchC\> system-view

\[SwitchC] mpls lsr-id 3.3.3.9

\[SwitchC] mpls ldp

\[SwitchC-ldp] quit

\[SwitchC] interface
vlan-interface 3

\[SwitchC-Vlan-interface3] mpls
enable

\[SwitchC-Vlan-interface3] mpls
ldp enable

\[SwitchC-Vlan-interface3] quit

\[SwitchC] interface
vlan-interface 7

\[SwitchC-Vlan-interface7] mpls
enable

\[SwitchC-Vlan-interface7] mpls
ldp enable

\[SwitchC-Vlan-interface7] quit

\# Configure Switch D.

\<SwitchD\> system-view

\[SwitchD] mpls lsr-id 4.4.4.9

\[SwitchD] mpls ldp

\[SwitchD-ldp] quit

\[SwitchD] interface
vlan-interface 6

\[SwitchD-Vlan-interface6] mpls
enable

\[SwitchD-Vlan-interface6] mpls
ldp enable

\[SwitchD-Vlan-interface6] quit

\[SwitchD] interface
vlan-interface 7

\[SwitchD-Vlan-interface7] mpls
enable

\[SwitchD-Vlan-interface7] mpls
ldp enable

\[SwitchD-Vlan-interface7] quit

**4\.**Configure IPv4 LSP generation policies:

\# On Switch A, create IP prefix list switcha, and
configure LDP to use only the routes permitted by the prefix list to establish
LSPs.

\[SwitchA] ip prefix-list switcha
index 10 permit 11.1.1.0 24

\[SwitchA] ip prefix-list switcha
index 20 permit 21.1.1.0 24

\[SwitchA] mpls ldp

\[SwitchA-ldp] lsp-trigger prefix-list
switcha

\[SwitchA-ldp] quit

\# On Switch B, create IP prefix list switchb, and
configure LDP to use only the routes permitted by the prefix list to establish
LSPs.

\[SwitchB] ip prefix-list switchb
index 10 permit 11.1.1.0 24

\[SwitchB] ip prefix-list switchb
index 20 permit 21.1.1.0 24

\[SwitchB] mpls ldp

\[SwitchB-ldp] lsp-trigger prefix-list
switchb

\[SwitchB-ldp] quit

\# On Switch C, create IP prefix list switchc, and
configure LDP to use only the routes permitted by the prefix list to establish
LSPs.

\[SwitchC] ip prefix-list switchc
index 10 permit 11.1.1.0 24

\[SwitchC] ip prefix-list switchc
index 20 permit 21.1.1.0 24

\[SwitchC] mpls ldp

\[SwitchC-ldp] lsp-trigger prefix-list
switchc

\[SwitchC-ldp] quit

\# On Switch D, create IP prefix list switchd, and
configure LDP to use only the routes permitted by the prefix list to establish
LSPs.

\[SwitchD] ip prefix-list switchd
index 10 permit 11.1.1.0 24

\[SwitchD] ip prefix-list switchd
index 20 permit 21.1.1.0 24

\[SwitchD] mpls ldp

\[SwitchD-ldp] lsp-trigger prefix-list
switchd

\[SwitchD-ldp] quit

**5\.**Configure IPv4 label acceptance policies:

\# On Switch A, create IP prefix list prefix-from-b to
permit subnet 21.1.1.0/24. Switch A uses this list to filter FEC-label mappings
received from Switch B.

\[SwitchA] ip prefix-list prefix-from-b
index 10 permit 21.1.1.0 24

\# On Switch A, create IP prefix list prefix-from-d to
deny subnet 21.1.1.0/24. Switch A uses this list to filter FEC-label mappings
received from Switch D.

\[SwitchA] ip prefix-list prefix-from-d
index 10 deny 21.1.1.0 24

\# On Switch A, configure label acceptance
policies to filter FEC-label mappings received from Switch B and Switch D.

\[SwitchA] mpls ldp

\[SwitchA-ldp] accept-label
peer 2.2.2.9 prefix-list prefix-from-b

\[SwitchA-ldp] accept-label
peer 4.4.4.9 prefix-list prefix-from-d

\[SwitchA-ldp] quit

\# On Switch C, create IP prefix list prefix-from-b to
permit subnet 11.1.1.0/24. Switch C uses this list to filter FEC-label mappings
received from Switch B.

\[SwitchC] ip prefix-list prefix-from-b
index 10 permit 11.1.1.0 24

\# On Switch C, create IP prefix list prefix-from-d to
deny subnet 11.1.1.0/24. Switch A uses this list to filter FEC-label mappings
received from Switch D.

\[SwitchC] ip prefix-list prefix-from-d
index 10 deny 11.1.1.0 24

\# On Switch C, configure label acceptance
policies to filter FEC-label mappings received from Switch B and Switch D.

\[SwitchC] mpls ldp

\[SwitchC-ldp] accept-label
peer 2.2.2.9 prefix-list prefix-from-b

\[SwitchC-ldp] accept-label
peer 4.4.4.9 prefix-list prefix-from-d

\[SwitchC-ldp] quit

#### Verifying the configuration

\# Display LDP LSP information on the switches,
for example, on Switch A.

\[SwitchA] display mpls ldp lsp

Status Flags: \* \- stale, L \- liberal,
B \- backup, N/A – unavailable

FECs: 2            Ingress: 1          Transit:
1      Egress: 1

 

FEC                In/Out Label        Nexthop        
OutInterface/LSINDEX

11.1.1.0/24        1277/-

                   -/1148(L)

21.1.1.0/24        -/1149(L)

                   -/1276              10.1.1.2       
Vlan2

                   1276/1276           10.1.1.2       
Vlan2

The output shows that the next hop of the
LSP for FEC 21.1.1.0/24 is Switch B (10.1.1.2). The LSP has been established
over the link Switch A—Switch B—Switch C, not over the link Switch A—Switch
D—Switch C.

\# Test the connectivity of the LDP LSP from
Switch A to Switch C.

\[SwitchA] ping mpls -a 11.1.1.1 ipv4
21.1.1.0 24

MPLS ping FEC 21.1.1.0/24 with 100
bytes of data:

100 bytes from 20.1.1.2: Sequence\=1
time\=1 ms

100 bytes from 20.1.1.2: Sequence\=2
time\=1 ms

100 bytes from 20.1.1.2: Sequence\=3
time\=8 ms

100 bytes from 20.1.1.2: Sequence\=4
time\=2 ms

100 bytes from 20.1.1.2: Sequence\=5
time\=1 ms

 

\--- Ping statistics for FEC
21.1.1.0/24 \---

5 packets transmitted, 5 packets
received, 0.0% packet loss

Round-trip min/avg/max \= 1/2/8 ms

\# Test the connectivity of the LDP LSP from
Switch C to Switch A.

\[SwitchC] ping mpls -a 21.1.1.1 ipv4
11.1.1.0 24

MPLS ping FEC 11.1.1.0/24 with 100
bytes of data:

100 bytes from 10.1.1.1: Sequence\=1
time\=1 ms

100 bytes from 10.1.1.1: Sequence\=2
time\=1 ms

100 bytes from 10.1.1.1: Sequence\=3
time\=1 ms

100 bytes from 10.1.1.1: Sequence\=4
time\=1 ms

100 bytes from 10.1.1.1: Sequence\=5
time\=1 ms

 

\--- Ping statistics for FEC
11.1.1.0/24 \---

5 packets transmitted, 5 packets
received, 0.0% packet loss

Round-trip min/avg/max \= 1/1/1 ms

### Example: Configuring label advertisement control

#### Network configuration

Two links, Switch A—Switch B—Switch C and Switch
A—Switch D—Switch C, exist between subnets 11.1.1.0/24 and 21.1.1.0/24.

Configure LDP to establish LSPs only for
routes to subnets 11.1.1.0/24 and 21.1.1.0/24.

Configure LDP to establish LSPs only on the
link Switch A—Switch B—Switch C to forward traffic between subnets 11.1.1.0/24
and 21.1.1.0/24.

Figure 12 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705102_x_Img_x_png_11_2216048_294551_0.png)

 

#### Requirements analysis

·     To ensure that the LSRs establish IPv4 LSPs
automatically, enable IPv4 LDP on each LSR.

·     To establish IPv4 LDP LSPs, configure an IPv4
routing protocol to ensure IP connectivity between the LSRs. This example uses
OSPF.

·     To ensure that LDP establishes IPv4 LSPs only
for the routes 11.1.1.0/24 and 21.1.1.0/24, configure IPv4 LSP generation
policies on each LSR.

·     To ensure that LDP establishes IPv4 LSPs only over
the link Switch A—Switch B—Switch C, configure IPv4 label advertisement
policies as follows:

¡     Switch
A advertises only the label mapping for FEC 11.1.1.0/24 to Switch B.

¡     Switch
C advertises only the label mapping for FEC 21.1.1.0/24 to Switch B.

¡     Switch
D does not advertise label mapping for FEC 21.1.1.0/24 to Switch A. Switch D
does not advertise label mapping for FEC 11.1.1.0/24 to Switch C.

#### Prerequisites

Before configuration, disable the spanning
tree feature globally or map each VLAN to an MSTI. For more information, see Layer 2—LAN Switching Configuration Guide.

#### Procedure

**1\.**Configure IP addresses and masks for
interfaces, including the loopback interfaces, as shown in [Figure 12](#_Ref324857880).
(Details not shown.)

**2\.**Configure OSPF on each switch to ensure IP
connectivity between them. (Details not shown.)

**3\.**Enable MPLS and IPv4 LDP:

\# Configure Switch A.

\<SwitchA\> system-view

\[SwitchA] mpls lsr-id 1.1.1.9

\[SwitchA] mpls ldp

\[SwitchA-ldp] quit

\[SwitchA] interface
vlan-interface 2

\[SwitchA-Vlan-interface2] mpls
enable

\[SwitchA-Vlan-interface2] mpls
ldp enable

\[SwitchA-Vlan-interface2] quit

\[SwitchA] interface
vlan-interface 6

\[SwitchA-Vlan-interface6] mpls
enable

\[SwitchA-Vlan-interface6] mpls
ldp enable

\[SwitchA-Vlan-interface6] quit

\# Configure Switch B.

\<SwitchB\> system-view

\[SwitchB] mpls lsr-id 2.2.2.9

\[SwitchB] mpls ldp

\[SwitchB-ldp] quit

\[SwitchB] interface
vlan-interface 2

\[SwitchB-Vlan-interface2] mpls
enable

\[SwitchB-Vlan-interface2] mpls
ldp enable

\[SwitchB-Vlan-interface2] quit

\[SwitchB] interface
vlan-interface 3

\[SwitchB-Vlan-interface3] mpls
enable

\[SwitchB-Vlan-interface3] mpls
ldp enable

\[SwitchB-Vlan-interface3] quit

\# Configure Switch C.

\<SwitchC\> system-view

\[SwitchC] mpls lsr-id 3.3.3.9

\[SwitchC] mpls ldp

\[SwitchC-ldp] quit

\[SwitchC] interface
vlan-interface 3

\[SwitchC-Vlan-interface3] mpls
enable

\[SwitchC-Vlan-interface3] mpls
ldp enable

\[SwitchC-Vlan-interface3] quit

\[SwitchC] interface
vlan-interface 7

\[SwitchC-Vlan-interface7] mpls
enable

\[SwitchC-Vlan-interface7] mpls
ldp enable

\[SwitchC-Vlan-interface7] quit

\# Configure Switch D.

\<SwitchD\> system-view

\[SwitchD] mpls lsr-id 4.4.4.9

\[SwitchD] mpls ldp

\[SwitchD-ldp] quit

\[SwitchD] interface
vlan-interface 6

\[SwitchD-Vlan-interface6] mpls
enable

\[SwitchD-Vlan-interface6] mpls
ldp enable

\[SwitchD-Vlan-interface6] quit

\[SwitchD] interface
vlan-interface 7

\[SwitchD-Vlan-interface7] mpls
enable

\[SwitchD-Vlan-interface7] mpls
ldp enable

\[SwitchD-Vlan-interface7] quit

**4\.**Configure IPv4 LSP generation policies:

\# On Switch A, create IP prefix list switcha, and configure
LDP to use only the routes permitted by the prefix list to establish LSPs.

\[SwitchA] ip prefix-list switcha
index 10 permit 11.1.1.0 24

\[SwitchA] ip prefix-list switcha
index 20 permit 21.1.1.0 24

\[SwitchA] mpls ldp

\[SwitchA-ldp] lsp-trigger prefix-list
switcha

\[SwitchA-ldp] quit

\# On Switch B, create IP prefix list switchb, and configure
LDP to use only the routes permitted by the prefix list to establish LSPs.

\[SwitchB] ip prefix-list switchb
index 10 permit 11.1.1.0 24

\[SwitchB] ip prefix-list switchb
index 20 permit 21.1.1.0 24

\[SwitchB] mpls ldp

\[SwitchB-ldp] lsp-trigger prefix-list
switchb

\[SwitchB-ldp] quit

\# On Switch C, create IP prefix list switchc, and configure
LDP to use only the routes permitted by the prefix list to establish LSPs.

\[SwitchC] ip prefix-list switchc
index 10 permit 11.1.1.0 24

\[SwitchC] ip prefix-list switchc
index 20 permit 21.1.1.0 24

\[SwitchC] mpls ldp

\[SwitchC-ldp] lsp-trigger prefix-list
switchc

\[SwitchC-ldp] quit

\# On Switch D, create IP prefix list switchd, and configure
LDP to use only the routes permitted by the prefix list to establish LSPs.

\[SwitchD] ip prefix-list switchd
index 10 permit 11.1.1.0 24

\[SwitchD] ip prefix-list switchd
index 20 permit 21.1.1.0 24

\[SwitchD] mpls ldp

\[SwitchD-ldp] lsp-trigger prefix-list
switchd

\[SwitchD-ldp] quit

**5\.**Configure IPv4 label advertisement policies:

\# On Switch A, create IP prefix list prefix-to-b to
permit subnet 11.1.1.0/24. Switch A uses this list to filter FEC-label mappings
advertised to Switch B.

\[SwitchA] ip prefix-list
prefix-to-b index 10 permit 11.1.1.0 24

\# On Switch A, create IP prefix list peer-b to permit
2.2.2.9/32. Switch A uses this list to filter peers.

\[SwitchA] ip prefix-list peer-b
index 10 permit 2.2.2.9 32

\# On Switch A, configure a label
advertisement policy to advertise only the label mapping for FEC 11.1.1.0/24 to
Switch B.

\[SwitchA] mpls ldp

\[SwitchA-ldp] advertise-label prefix-list
prefix-to-b peer peer-b

\[SwitchA-ldp] quit

\# On Switch C, create IP prefix list prefix-to-b to
permit subnet 21.1.1.0/24. Switch C uses this list to filter FEC-label mappings
advertised to Switch B.

\[SwitchC] ip prefix-list prefix-to-b
index 10 permit 21.1.1.0 24

\# On Switch C, create IP prefix list peer-b to permit
2.2.2.9/32. Switch C uses this list to filter peers.

\[SwitchC] ip prefix-list peer-b
index 10 permit 2.2.2.9 32

\# On Switch C, configure a label
advertisement policy to advertise only the label mapping for FEC 21.1.1.0/24 to
Switch B.

\[SwitchC] mpls ldp

\[SwitchC-ldp] advertise-label prefix-list
prefix-to-b peer peer-b

\[SwitchC-ldp] quit

\# On Switch D, create IP prefix list prefix-to-a to deny
subnet 21.1.1.0/24. Switch D uses this list to filter FEC-label mappings to be
advertised to Switch A.

\[SwitchD] ip prefix-list prefix-to-a
index 10 deny 21.1.1.0 24

\[SwitchD] ip prefix-list prefix-to-a
index 20 permit 0.0.0.0 0 less-equal 32

\# On Switch D, create IP prefix list peer-a to permit
1.1.1.9/32. Switch D uses this list to filter peers.

\[SwitchD] ip prefix-list peer-a
index 10 permit 1.1.1.9 32

\# On Switch D, create IP prefix list prefix-to-c to deny
subnet 11.1.1.0/24. Switch D uses this list to filter FEC-label mappings to be
advertised to Switch C.

\[SwitchD] ip prefix-list
prefix-to-c index 10 deny 11.1.1.0 24

\[SwitchD] ip prefix-list
prefix-to-c index 20 permit 0.0.0.0 0 less-equal 32

\# On Switch D, create IP prefix list peer-c to permit
subnet 3.3.3.9/32. Switch D uses this list to filter peers.

\[SwitchD] ip prefix-list peer-c
index 10 permit 3.3.3.9 32

\# On Switch D, configure a label
advertisement policy. This policy ensures that Switch D does not advertise
label mappings for FEC 21.1.1.0/24 to Switch A, and does not advertise label
mappings for FEC 11.1.1.0/24 to Switch C.

\[SwitchD] mpls ldp

\[SwitchD-ldp] advertise-label
prefix-list prefix-to-a peer peer-a

\[SwitchD-ldp] advertise-label
prefix-list prefix-to-c peer peer-c

\[SwitchD-ldp] quit

#### Verifying the configuration

\# Display LDP LSP information on each
switch.

\[SwitchA] display mpls ldp lsp

Status Flags: \* \- stale, L \- liberal,
B \- backup, N/A – unavailable

FECs: 2            Ingress: 1          Transit:
1      Egress: 1

 

FEC                In/Out Label        Nexthop        
OutInterface/LSINDEX

11.1.1.0/24        1277/-

                   -/1151(L)

                   -/1277(L)

21.1.1.0/24        -/1276              10.1.1.2       
Vlan2

                   1276/1276           10.1.1.2       
Vlan2

\[SwitchB] display mpls ldp lsp

Status Flags: \* \- stale, L \- liberal,
B \- backup, N/A – unavailable

FECs: 2            Ingress: 2          Transit:
2      Egress: 0

 

FEC                In/Out Label        Nexthop        
OutInterface/LSINDEX

11.1.1.0/24        -/1277              10.1.1.1       
Vlan2

                   1277/1277           10.1.1.1       
Vlan2

21.1.1.0/24        -/1149              20.1.1.2       
Vlan3

                   1276/1149           20.1.1.2       
Vlan3

\[SwitchC] display mpls ldp lsp

Status Flags: \* \- stale, L \- liberal,
B \- backup, N/A – unavailable

FECs: 2            Ingress: 1          Transit:
1      Egress: 1

 

FEC                In/Out Label        Nexthop        
OutInterface/LSINDEX

11.1.1.0/24        -/1277              20.1.1.1       
Vlan3

                   1148/1277           20.1.1.1       
Vlan3

21.1.1.0/24        1149/-

                   -/1276(L)

                   -/1150(L)

\[SwitchD] display mpls ldp lsp

Status Flags: \* \- stale, L \- liberal,
B \- backup, N/A – unavailable

FECs: 2            Ingress: 0          Transit:
0      Egress: 2

 

FEC                In/Out Label        Nexthop        
OutInterface/LSINDEX

11.1.1.0/24        1151/-

                   -/1277(L)

21.1.1.0/24        1150/-

The output shows that Switch A and Switch C
have received FEC-label mappings only from Switch B. Switch B has received
FEC-label mappings from both Switch A and Switch C. Switch D does not receive
FEC-label mappings from Switch A or Switch C. LDP has established an LSP only
over the link Switch A—Switch B—Switch C.

\# Test the connectivity of the LDP LSP from
Switch A to Switch C.

\[SwitchA] ping mpls -a 11.1.1.1 ipv4
21.1.1.0 24

MPLS ping FEC 21.1.1.0/24 with 100
bytes of data:

100 bytes from 20.1.1.2: Sequence\=1
time\=1 ms

100 bytes from 20.1.1.2: Sequence\=2
time\=1 ms

100 bytes from 20.1.1.2: Sequence\=3
time\=8 ms

100 bytes from 20.1.1.2: Sequence\=4
time\=2 ms

100 bytes from 20.1.1.2: Sequence\=5
time\=1 ms

 

\--- Ping statistics for FEC
21.1.1.0/24 \---

5 packets transmitted, 5 packets
received, 0.0% packet loss

Round-trip min/avg/max \= 1/2/8 ms

\# Test the connectivity of the LDP LSP from
Switch C to Switch A.

\[SwitchC] ping mpls -a 21.1.1.1 ipv4
11.1.1.0 24

MPLS ping FEC 11.1.1.0/24 with 100
bytes of data:

100 bytes from 10.1.1.1: Sequence\=1
time\=1 ms

100 bytes from 10.1.1.1: Sequence\=2
time\=1 ms

100 bytes from 10.1.1.1: Sequence\=3
time\=1 ms

100 bytes from 10.1.1.1: Sequence\=4
time\=1 ms

100 bytes from 10.1.1.1: Sequence\=5
time\=1 ms

 

\--- Ping statistics for FEC
11.1.1.0/24 \---

5 packets transmitted, 5 packets received,
0.0% packet loss

Round-trip min/avg/max \= 1/1/1 ms

### Example: Configuring LDP FRR

#### Network configuration

Switch S, Switch A, and Switch D reside in
the same OSPF domain. Configure OSPF FRR so LDP can establish a primary LSP and
a backup LSP on the Switch S—Switch D and the Switch S—Switch A—Switch D links,
respectively. 

When the primary LSP operates correctly,
traffic between subnets 11.1.1.0/24 and 21.1.1.0/24 is forwarded through the
LSP.

When the primary LSP fails, traffic between
the two subnets is immediately switched to the backup LSP.

Figure 13 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705103_x_Img_x_png_12_2216048_294551_0.png)

 

#### Requirements analysis

·     To ensure that the LSRs establish IPv4 LSPs
automatically, enable IPv4 LDP on each LSR.

·     To establish IPv4 LDP LSPs, configure an IPv4
routing protocol to ensure IP connectivity between the LSRs. This example uses
OSPF.

·     To ensure that LDP establishes IPv4 LSPs only
for the routes 11.1.1.0/24 and 21.1.1.0/24, configure IPv4 LSP generation
policies on each LSR.

·     To allow LDP to establish backup LSRs, configure
OSPF FRR on Switch S and Switch D.

#### Prerequisites

Before configuration, disable the spanning
tree feature globally or map each VLAN to an MSTI. For more information, see Layer 2—LAN Switching Configuration Guide.

#### Procedure

**1\.**Configure IP addresses and masks for
interfaces, including the loopback interfaces, as shown in [Figure 13](#_Ref361649078).
(Details not shown.)

**2\.**Configure OSPF on each switch to ensure IP
connectivity between them. (Details not shown.)

**3\.**Configure OSPF FRR by using one of the following
methods:

¡     (Method
1.) Enable OSPF FRR to calculate a backup next hop by using the LFA algorithm:

\# Configure Switch S.

\<SwitchS\> system-view

\[SwitchS] bfd echo-source-ip
10.10.10.10

\[SwitchS] ospf 1

\[SwitchS-ospf-1] fast-reroute
lfa

\[SwitchS-ospf-1] quit

\# Configure Switch D.

\<SwitchD\> system-view

\[SwitchD] bfd echo-source-ip
11.11.11.11

\[SwitchD] ospf 1

\[SwitchD-ospf-1] fast-reroute lfa

\[SwitchD-ospf-1] quit

¡     (Method
2.) Enable OSPF FRR to specify a backup next hop by using a routing policy:

\# Configure Switch S.

\<SwitchS\> system-view

\[SwitchS] bfd echo-source-ip
10.10.10.10

\[SwitchS] ip prefix-list abc
index 10 permit 21.1.1.0 24

\[SwitchS] route-policy frr
permit node 10

\[SwitchS-route-policy-frr-10]
if-match ip address prefix-list abc

\[SwitchS-route-policy-frr-10]
apply fast-reroute backup-interface vlan-interface 12 backup-nexthop 12.12.12.2

\[SwitchS-route-policy-frr-10]
quit

\[SwitchS] ospf 1

\[SwitchS-ospf-1] fast-reroute
route-policy frr

\[SwitchS-ospf-1] quit

\# Configure Switch D.

\<SwitchD\> system-view

\[SwitchD] bfd echo-source-ip 10.10.10.10

\[SwitchD] ip prefix-list abc
index 10 permit 11.1.1.0 24

\[SwitchD] route-policy frr
permit node 10

\[SwitchD-route-policy-frr-10]
if-match ip address prefix-list abc

\[SwitchD-route-policy-frr-10]
apply fast-reroute backup-interface vlan-interface 24 backup-nexthop 24.24.24.2

\[SwitchD-route-policy-frr-10]
quit

\[SwitchD] ospf 1

\[SwitchD-ospf-1] fast-reroute
route-policy frr

\[SwitchD-ospf-1] quit

**4\.**Enable MPLS and IPv4 LDP:

\# Configure Switch S.

\[SwitchS] mpls lsr-id 1.1.1.1

\[SwitchS] mpls ldp

\[SwitchS-mpls-ldp] quit

\[SwitchS] interface
vlan-interface 12

\[SwitchS-Vlan-interface12]
mpls enable

\[SwitchS-Vlan-interface12]
mpls ldp enable

\[SwitchS-Vlan-interface12]
quit

\[SwitchS] interface
vlan-interface 13

\[SwitchS-Vlan-interface13]
mpls enable

\[SwitchS-Vlan-interface13]
mpls ldp enable

\[SwitchS-Vlan-interface13]
quit

\# Configure Switch D.

\[SwitchD] mpls lsr-id 3.3.3.3

\[SwitchD] mpls ldp

\[SwitchD-mpls-ldp] quit

\[SwitchD] interface
vlan-interface 13

\[SwitchD-Vlan-interface13]
mpls enable

\[SwitchD-Vlan-interface13]
mpls ldp enable

\[SwitchD-Vlan-interface13]
quit

\[SwitchD] interface
vlan-interface 24

\[SwitchD-Vlan-interface24]
mpls enable

\[SwitchD-Vlan-interface24]
mpls ldp enable

\[SwitchD-Vlan-interface24]
quit

\# Configure Switch A.

\[SwitchA] mpls lsr-id 2.2.2.2

\[SwitchA] mpls ldp

\[SwitchA-mpls-ldp] quit

\[SwitchA] interface
vlan-interface 12

\[SwitchA-Vlan-interface12]
mpls enable

\[SwitchA-Vlan-interface12]
mpls ldp enable

\[SwitchA-Vlan-interface12]
quit

\[SwitchA] interface
vlan-interface 24

\[SwitchA-Vlan-interface24]
mpls enable

\[SwitchA-Vlan-interface24]
mpls ldp enable

\[SwitchA-Vlan-interface24]
quit

**5\.**Configure IPv4 LSP generation policies so
LDP uses all static routes and IGP routes to establish LSPs:

\# Configure Switch S.

\[SwitchS] mpls ldp

\[SwitchS-ldp] lsp-trigger all

\[SwitchS-ldp] quit

\# Configure Switch D.

\[SwitchD] mpls ldp

\[SwitchD-ldp] lsp-trigger all

\[SwitchD-ldp] quit

\# Configure Switch A.

\[SwitchA] mpls ldp

\[SwitchA-ldp] lsp-trigger all

\[SwitchA-ldp] quit

#### Verifying the configuration

\# Verify that primary and backup LSPs have
been established.

\[SwitchS] display mpls ldp lsp
21.1.1.0 24

Status Flags: \* \- stale, L \- liberal,
B \- backup, N/A – unavailable

FECs: 1            Ingress: 2          Transit:
2      Egress: 0

 

FEC                In/Out
Label        Nexthop         OutInterface/LSINDEX

21.1.1.0/24        -/3                 13.13.13.2     
Vlan13

                   2174/3              13.13.13.2     
Vlan13

                   -/3(B)              12.12.12.2     
Vlan12

                   2174/3(B)          
12.12.12.2      Vlan12

 

