
# BGP overview

Border Gateway
Protocol (BGP) is an exterior gateway protocol (EGP). It is called internal BGP
(IBGP) when it runs within an AS and called external BGP (EBGP) when it runs
between ASs. The current version in use is BGP-4 (RFC 4271).

## BGP characteristics

BGP has the following characteristics:

·     Focuses on route control and selection rather
than route discovery and calculation.

·     Uses TCP to enhance reliability.

·     Measures the distance of a route by using a list
of ASs that the route must travel through to reach the destination. BGP is also
called a path-vector protocol.

·     Supports CIDR.

·     Reduces bandwidth consumption by advertising
only incremental updates. BGP is very suitable to advertise large numbers of
routes on the Internet.

·     Eliminates routing loops by adding AS path
information to BGP route updates.

·     Uses policies to implement flexible route
filtering and selection.

·     Has good scalability.

## BGP speaker and BGP peer

A router running BGP is a BGP speaker. A
BGP speaker establishes peer relationships with other BGP speakers to exchange
routing information over TCP connections.

Based on the AS, a BGP peer can be either
of the following types:

·     IBGP peer—Resides in the same AS as the local router.

·     EBGP peer—Resides in different ASs from the local router.

Based on the IP version, a BGP peer can be
either of the following types:

·     IPv4 peer—Uses an IPv4 address to establish a peer relationship with the
local router.

·     IPv6 peer—Uses an IPv6 address to establish a peer relationship with the
local router.

## BGP message types

BGP uses the following message types:

·     Open—After establishing a TCP connection, BGP sends an OPEN message to
establish a session to the peer.

·     Update—BGP sends UPDATE messages to exchange routing information between
peers. Each UPDATE message can advertise a group of feasible routes with
identical attributes and multiple withdrawn routes.

·     Keepalive—BGP sends KEEPALIVE messages between peers to maintain
connectivity.

·     Route-refresh—BGP sends a ROUTE-REFRESH message to request the routing information
for a specific address family from a peer.

·     Notification—BGP sends a NOTIFICATION message upon detecting an error and immediately
closes the connection.

## BGP finite state machine

During session establishment, the devices
at the two ends transition between different BGP states, as shown in [Figure 1](#_Ref111747919).

Figure 1 BGP finite state machine

![](https://resource.h3c.com/en/202407/12/20240712_11704809_x_Img_x_png_0_2216020_294551_0.png)

‌

#### Idle

The idle state is the initial BGP state. In
this state, BGP rejects any connection requests. Only upon receiving a Start
event, the device assigns resources to BGP, and attempts to establish a TCP
connection and transition to Connect state. 

BGP transitions to idle state from the
remaining states upon a TCP disconnection error, packet error, or connection
closed due to configuration issues, and reception of a NOTIFICATION message.

#### Connect

In this state, BGP starts a Connect Retry
timer and waits for TCP connection establishment to be completed.

·     If TCP connection establishment succeeds before
the Connect Retry timer expires, BGP closes the Connect Retry timer, sends an OPEN
message to the peer, and transitions to OpenSent state.

·     If TCP connection establishment fails before the
Connect Retry timer expires, BGP transitions to Active state.

·     If the Connect Retry timer expires, and no
connection response is received, BGP resets the Connect Retry timer, tries to
establish a TCP connection with the peer again, and stays in Connect state.

#### Active

In this state, BGP continuously attempts to
establish a TCP connection. 

·     If TCP connection establishment succeeds, BGP
closes the Connect Retry timer, sends an OPEN message to the peer, and
transitions to OpenSent state.

·     If TCP connection establishment fails, BGP
resets the Connect Retry timer and stays in Active state.

·     If the Connect Retry timer expires, and no
connection response is received, BGP resets the Connect Retry timer and
transitions to Connect state.

#### OpenSent

In this state, BGP waits for an OPEN
message from the peer, and checks the BGP version number and AS number in the OPEN
message.

·     If the OPEN message is correct, BGP sends a KEEPALIVE
message to the peer, and transitions to OpenConfirm state.

·     If the OPEN message is incorrect, BGP sends a NOTIFICATION
message to the peer, and transitions to Idle state.

If the TCP connection is disconnected in
this state, BGP resets the Connect Retry timer, transitions to Active state,
and tries to establish a TCP connection again.

#### OpenConfirm

In this state, BGP waits for a KEEPALIVE or
NOTIFICATION message. 

·     Upon receiving a KEEPALIVE message, BGP
transitions to Established state. 

·     Upon receiving a NOTIFICATION message, BGP
transitions to Idle state.

#### Established

In this state, BGP can exchange UPDATE, KEEPALIVE,
ROUTE-REFRESH, and NOTIFICATION messages with the peer.

·     Upon receiving a correct UPDATE or KEEPALIVE
message, BGP stays in Established state.

·     Upon receiving an incorrect UPDATE or KEEPALIVE
message, BGP sends a NOTIFICATION message to the peer and transitions to Idle
state.

·     Upon receiving a ROUTE-REFRESH message, the BGP
state does not change.

·     Upon receiving a NOTIFICATION message, BGP
transitions to Idle state.

## BGP path attributes

BGP uses the following path attributes in UPDATE
messages for route filtering and selection:

#### ORIGIN

The ORIGIN attribute specifies the origin
of BGP routes. This attribute has the following types:

·     IGP—Has the highest priority. Routes generated in the local AS have the
IGP attribute.

·     EGP—Has the second highest priority. Routes obtained through EGP have
the EGP attribute.

·     INCOMPLETE—Has the lowest priority. The source of routes with this attribute
is unknown. Routes redistributed from other routing protocols have the
INCOMPLETE attribute.

#### AS\_PATH

The AS\_PATH attribute identifies the ASs
through which a route has passed. Before advertising a route to another AS, BGP
adds the local AS number into the AS\_PATH attribute, so the receiver can determine
ASs to route the message back. 

The AS\_PATH attribute has the following
types:

·     AS\_SEQUENCE—Arranges AS numbers in sequence. As shown in [Figure 2](#_Ref279760501), the number
of the AS closest to the receiver's AS is leftmost.

·     AS\_SET—Arranges AS numbers randomly. 

Figure 2 AS\_PATH attribute

![](https://resource.h3c.com/en/202407/12/20240712_11704810_x_Img_x_png_1_2216020_294551_0.png)

‌

BGP uses the AS\_PATH attribute to implement
the following functions: 

·     Avoid routing loops—A BGP router does not receive routes containing the local AS number
to avoid routing loops. 

·     Affect route selection—BGP gives priority to the route with the shortest AS\_PATH length if
other factors are the same. As shown in [Figure 2](#_Ref279760501), the
BGP router in AS 50 gives priority to the route passing AS 40 for sending data
to the destination 8.0.0.0. In some applications, you can apply a routing
policy to control BGP route selection by modifying the AS\_PATH length. For more
information about routing policy, see "Configuring routing policies."

·     Filter routes—By using an AS path list, you can filter routes based on AS numbers
contained in the AS\_PATH attribute. For more information about AS path list,
see "Configuring routing policies." 

#### NEXT\_HOP

The NEXT\_HOP attribute may not be the IP
address of a directly connected router. Its value is determined as follows:

·     When a BGP speaker advertises a self-originated
route to a BGP peer, it sets the address of the sending interface as the
NEXT\_HOP.

·     When a BGP speaker sends a received route to an
EBGP peer, it sets the address of the sending interface as the NEXT\_HOP.

·     When a BGP speaker sends a route received from
an EBGP peer to an IBGP peer, it does not modify the NEXT\_HOP attribute. If
load balancing is configured, BGP modifies the NEXT\_HOP attribute for the
equal-cost routes. For load balancing information, see "[BGP load balancing](#_Ref303340691)." 

Figure 3 NEXT\_HOP attribute

![](https://resource.h3c.com/en/202407/12/20240712_11704821_x_Img_x_png_2_2216020_294551_0.png)

‌

#### MED (MULTI\_EXIT\_DISC)

BGP advertises the MED attribute between
two neighboring ASs, each of which does not advertise the attribute to any
other AS.

Similar to metrics used by IGPs, MED is
used to determine the optimal route for traffic going into an AS. When a BGP
router obtains multiple routes to the same destination but with different next
hops, it selects the route with the smallest MED value as the optimal route. As
shown in [Figure 4](#_Ref279760680), traffic from AS 10 to AS 20 travels through Router B that is
selected according to MED.

Figure 4 MED attribute

![](https://resource.h3c.com/en/202407/12/20240712_11704832_x_Img_x_png_3_2216020_294551_0.png)

‌

Generally BGP only compares MEDs of routes received
from the same AS. You can also use the compare-different-as-med command to force BGP to compare MED values of routes received from
different ASs.

#### LOCAL\_PREF

The LOCAL\_PREF attribute is exchanged
between IBGP peers only, and is not advertised to any other AS. It indicates the
priority of a BGP router.

BGP uses LOCAL\_PREF to determine the optimal
route for traffic leaving the local AS. When a BGP router obtains multiple
routes to the same destination but with different next hops, it selects the
route with the highest LOCAL\_PREF value as the optimal route. As shown in [Figure 5](#_Ref279760715),
traffic from AS 20 to AS 10 travels through Router C that is selected according
to LOCAL\_PREF.

Figure 5 LOCAL\_PREF attribute

![](https://resource.h3c.com/en/202407/12/20240712_11704843_x_Img_x_png_4_2216020_294551_0.png)

‌

#### COMMUNITY

The COMMUNITY attribute identifies the
community of BGP routes. A BGP community is a group of routes with the same
characteristics. It has no geographical boundaries. Routes of different ASs can
belong to the same community.

A route can carry one or more COMMUNITY attribute
values (each of which is represented by a 4-byte integer). A router uses the
COMMUNITY attribute to determine whether to advertise the route and the advertising
scope without using complex filters such as ACLs. This mechanism simplifies routing
policy configuration, management, and maintenance.

Well-known COMMUNITY attributes involve the
following:

·     INTERNET—By default, all routes belong to the Internet community. Routes
with this attribute can be advertised to all BGP peers.

·     NO\_EXPORT—Routes with this attribute cannot be advertised out of the local AS
or out of the local confederation, but can be advertised to other sub-ASs in
the confederation. For confederation information, see "[Settlements for problems in large-scale BGP
networks](#_Ref163372066)." 

·     No\_ADVERTISE—Routes with this attribute cannot be advertised to other BGP peers.

·     No\_EXPORT\_SUBCONFED—Routes with this attribute cannot be advertised out of the local AS
or other sub-ASs in the local confederation.

You
can configure BGP community lists to filter BGP routes based on the BGP COMMUNITY
attribute. 

#### Extended community attribute

To meet new demands, BGP defines the extended
community attribute. The extended community attribute has the following
advantages over the COMMUNITY attribute: 

·     Provides more attribute values by extending the
attribute length to eight bytes. 

·     Allows for using different types of extended
community attributes in different scenarios to enhance route filtering and
control and simplify configuration and management. 

The device supports the route target attribute
and Site of Origin (SoO) extended community attribute. For information about
route target, see MCE Configuration Guide.

The SoO attribute specifies the site where
the route originated. It prevents advertising a route back to the originating
site. If the AS-path attribute is lost, the router can use the SoO attribute to
avoid routing loops. 

The SoO attribute has the following
formats:

·     16-bit AS number:32-bit
user-defined number. For example, 100:3.

·     32-bit IP address:16-bit
user-defined number. For example, 192.168.122.15:1.

·     32-bit AS number:16-bit
user-defined number, where the minimum value of
the AS number is 65536\. For example, 65536:1.

## BGP route selection

BGP discards routes with unreachable
NEXT\_HOPs. If multiple routes to the same destination are available, BGP selects
the optimal route in the following sequence:

**1\.**The route with the highest Preferred\_value.

**2\.**The route with the highest LOCAL\_PREF.

**3\.**The route generated by the network command, the route redistributed by the import-route command, or the summary route in turn.

**4\.**The route with the shortest AS\_PATH.

**5\.**The IGP, EGP, or INCOMPLETE route in turn.

**6\.**The route with the lowest MED value.

**7\.**The route learned from EBGP, confederation
EBGP, confederation IBGP, or IBGP in turn.

**8\.**The route with the smallest IGP metric to
the next hop.

**9\.**The route with the smallest recursion depth.

**10\.**If a route received from an EBGP peer is the
current optimal route, BGP does not change the optimal route when it receives
routes from other EBGP peers.

**11\.**The route advertised by the router with the
smallest router ID.

If one of the routes is advertised by a
route reflector, BGP compares the ORIGINATOR\_ID of the route with the router
IDs of other routers. Then, BGP selects the route with the smallest ID as the
optimal route.

**12\.**The route with an IPv4 next hop. 

**13\.**The route with the shortest CLUSTER\_LIST.

**14\.**The route advertised by the peer with the
lowest IP address.

The CLUSTER\_IDs of route reflectors form a
CLUSTER\_LIST. If a route reflector receives a route that contains its own
CLUSTER ID in the CLUSTER\_LIST, the router discards the route to avoid routing
loops.

If load balancing is configured, the system
selects available routes to implement load balancing.

## BGP route advertisement rules

BGP follows these rules for route
advertisement:

·     When multiple feasible routes to a destination
exist, BGP advertises only the optimal route to its peers. If the advertise-rib-active command is
configured, BGP advertises the optimal route in the IP routing table. If not,
BGP advertises the optimal route in the BGP routing table.

·     BGP advertises only routes that it uses. 

·     BGP advertises routes learned from an EBGP peer to
all BGP peers, including both EBGP and IBGP peers.

·     BGP advertises routes learned from an IBGP peer
to EBGP peers, rather than other IBGP peers.

·     After establishing a session to a new BGP peer, BGP
advertises all the routes matching the above rules to the peer. After that, BGP
advertises only incremental updates to the peer. 

## BGP load balancing

BGP load balancing is applicable between
EBGP peers, between IBGP peers, and between confederations.

BGP implements load balancing through route
recursion and route selection.

#### BGP load balancing through route recursion

The next hop of a BGP route might not be
directly connected. One of the reasons is that the next hop information exchanged
between IBGP peers is not modified. The BGP router must find the directly connected
next hop through IGP. The matching route with the direct next hop is called the
recursive route. The process of finding a recursive route is route recursion.

If multiple recursive routes to the same
destination are load balanced, BGP generates the same number of next hops to
forward packets. 

BGP load balancing based on route recursion
is always enabled in the system. 

#### BGP load balancing through route selection

IGP routing protocols, such as RIP and OSPF,
can use route metrics as criteria to load balance between routes that have the
same metric. BGP cannot load balance between routes by route metrics as an IGP
protocol does, because BGP does not have a route computation algorithm.

BGP uses the following load balancing criteria
to determine load balanced routes:

·     The routes have the same ORIGIN, LOCAL\_PREF, and
MED attributes. 

·     The routes meet the following requirements on
the AS\_PATH attribute:

¡     If
the balance
as-path-neglect command is configured, the
routes can have different AS\_PATH attributes.

¡     If
only the balance as-path-relax command is
configured, the routes can have different AS\_PATH attributes, but the length of
the AS\_PATH attributes must be the same.

¡     If
neither the balance as-path-neglect
nor the balance as-path-relax command is
configured, the routes must have the same AS\_PATH attribute.

·     The next hops of the routes meet the following
requirements on IGP metrics:

¡     If neither
the bestroute
igp-metric-ignore command nor the balance
igp-metric-ignore command is configured, the
next hops of the routes must have the same IGP metric value.

¡     If
the bestroute
igp-metric-ignore command or the balance
igp-metric-ignore command is configured, the
next hops of the routes can have different IGP metric values.

·     The routes have the same MPLS label assignment
status (labeled or not labeled).

BGP does not use the route selection rules
described in "[BGP route selection](#_Ref303261021)" for load balancing.

As shown in [Figure 6](#_Ref303261435),
Router A and Router B are IBGP peers of Router C. Router C allows a maximum
number of two ECMP routes for load balancing. 

Router D and Router E both advertise a
route 9.0.0.0 to Router C. Router C installs the two routes to its routing
table for load balancing if the routes meet the BGP load balancing criteria. After
that, Router C forwards to Router A and Router B a single route whose
attributes are changed as follows:

·     AS\_PATH attribute:

¡     If
the balance
as-path-neglect and balance as-path-relax
commands are not configured, the AS\_PATH
attribute does not change.

¡     If
the balance
as-path-neglect or balance as-path-relax command is configured, the AS\_PATH attribute is changed to the
attribute of the optimal route.

·     The NEXT\_HOP attribute is changed to the IP
address of Router C.

·     Other attributes are changed to be the same as
the optimal route. 

Figure 6 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11704854_x_Img_x_png_5_2216020_294551_0.png)

‌

## Settlements for problems in large-scale BGP networks

You can use the following methods to facilitate
management and improve route distribution efficiency on a large-scale BGP
network.

#### Route summarization

Route summarization can reduce the BGP routing
table size by advertising summary routes rather than more specific routes.

The system supports both manual and automatic
route summarization. Manual route summarization allows you to determine the
attribute of a summary route and whether to advertise more specific routes.

#### Route dampening

Route flapping (a route comes up and
disappears in the routing table frequently) causes BGP to send many routing
updates. It can consume too many resources and affect other operations.

In most cases, BGP runs in complex networks
where route changes are more frequent. To solve the problem caused by route
flapping, you can use BGP route dampening to suppress unstable routes. 

BGP route dampening uses a penalty value to
judge the stability of a route. The bigger the value, the less stable the
route. Each time a route state changes from reachable to unreachable, or a
reachable route's attribute changes, BGP adds a penalty value of 1000 to the
route. When the penalty value of the route exceeds the suppress value, the
route is suppressed and cannot become the optimal route. When the penalty value
reaches the upper limit, no penalty value is added. 

If the suppressed route does not flap, its penalty
value gradually decreases to half of the suppress value after a period of time.
This period is called "Half-life." When the value decreases to the
reusable threshold value, the route is usable again.

Figure 7 BGP
route dampening

![](https://resource.h3c.com/en/202407/12/20240712_11704860_x_Img_x_png_6_2216020_294551_0.png)

‌

#### Peer group

You can organize BGP peers with the same attributes
into a group to simplify their configurations.

When a peer joins the peer group, the peer
obtains the same configuration as the peer group. If the configuration of the
peer group is changed, the configuration of group members is changed.

#### Community

You can apply a community list or an
extended community list to a routing policy for route control. For more
information, see "[BGP finite
state machine](#_Ref303342544)

[During session establishment, the devices at the two ends
transition between different BGP states, as shown in Figure 1\.](#_Ref303342544)

[**Figure 8** BGP finite
state machine](#_Ref303342544)

[![](https://resource.h3c.com/en/202407/12/20240712_11704861_x_Img_x_png_7_2216020_294551_0.png)](#_Ref303342544)

[‌](#_Ref303342544)

#### [Idle](#_Ref303342544)

[The idle state is the initial BGP state. In this state, BGP
rejects any connection requests. Only upon receiving a Start event, the device
assigns resources to BGP, and attempts to establish a TCP connection and
transition to Connect state.](#_Ref303342544) 

[BGP transitions to idle state from the remaining states
upon a TCP disconnection error, packet error, or connection closed due to configuration
issues, and reception of a NOTIFICATION message.](#_Ref303342544)

#### [Connect](#_Ref303342544)

[In this state, BGP starts a Connect Retry timer and waits
for TCP connection establishment to be completed.](#_Ref303342544)

[·     If TCP connection establishment succeeds before the Connect Retry
timer expires, BGP closes the Connect Retry timer, sends an OPEN message to the
peer, and transitions to OpenSent state.](#_Ref303342544)

[·     If TCP connection establishment fails before the Connect Retry
timer expires, BGP transitions to Active state.](#_Ref303342544)

[·     If the Connect Retry timer expires, and no connection response is
received, BGP resets the Connect Retry timer, tries to establish a TCP
connection with the peer again, and stays in Connect state.](#_Ref303342544)

#### [Active](#_Ref303342544)

[In this state, BGP continuously attempts to establish a TCP
connection.](#_Ref303342544) 

[·     If TCP connection establishment succeeds, BGP closes the Connect
Retry timer, sends an OPEN message to the peer, and transitions to OpenSent
state.](#_Ref303342544)

[·     If TCP connection establishment fails, BGP resets the Connect
Retry timer and stays in Active state.](#_Ref303342544)

[·     If the Connect Retry timer expires, and no connection response is
received, BGP resets the Connect Retry timer and transitions to Connect state.](#_Ref303342544)

#### [OpenSent](#_Ref303342544)

[In this state, BGP waits for an OPEN message from the
peer, and checks the BGP version number and AS number in the OPEN message.](#_Ref303342544)

[·     If the OPEN message is correct, BGP sends a KEEPALIVE message to
the peer, and transitions to OpenConfirm state.](#_Ref303342544)

[·     If the OPEN message is incorrect, BGP sends a NOTIFICATION
message to the peer, and transitions to Idle state.](#_Ref303342544)

[If the TCP connection is disconnected in this state, BGP
resets the Connect Retry timer, transitions to Active state, and tries to
establish a TCP connection again.](#_Ref303342544)

#### [OpenConfirm](#_Ref303342544)

[In this state, BGP waits for a KEEPALIVE or NOTIFICATION
message.](#_Ref303342544) 

[·     Upon receiving a KEEPALIVE message, BGP transitions to Established
state.](#_Ref303342544) 

[·     Upon receiving a NOTIFICATION message, BGP transitions to Idle
state.](#_Ref303342544)

#### [Established](#_Ref303342544)

[In this state, BGP can exchange UPDATE, KEEPALIVE, ROUTE-REFRESH,
and NOTIFICATION messages with the peer.](#_Ref303342544)

[·     Upon receiving a correct UPDATE or KEEPALIVE message, BGP stays
in Established state.](#_Ref303342544)

[·     Upon receiving an incorrect UPDATE or KEEPALIVE message, BGP
sends a NOTIFICATION message to the peer and transitions to Idle state.](#_Ref303342544)

[·     Upon receiving a ROUTE-REFRESH message, the BGP state does not
change.](#_Ref303342544)

[·     Upon receiving a NOTIFICATION message, BGP transitions to Idle
state.](#_Ref303342544)

[BGP path attributes](#_Ref303342544)."

#### Route reflector

IBGP peers must be fully meshed to maintain
connectivity. If n routers exist in an AS, the number of IBGP connections is n(n-1)/2.
If a large number of IBGP peers exist, large amounts of network and CPU
resources are consumed to maintain sessions.

Using route reflectors can solve this
issue. In an AS, a router acts as a route reflector, and other routers act as
clients connecting to the route reflector. The route reflector forwards routing
information received from a client to other clients. In this way, all clients
can receive routing information from one another without establishing BGP sessions.

A router that is neither a route reflector
nor a client is a non-client, which, as shown in [Figure 9](#_Ref279763136), must establish
BGP sessions to the route reflector and other non-clients.

Figure 9 Network diagram for a route
reflector

![](https://resource.h3c.com/en/202407/12/20240712_11704862_x_Img_x_png_8_2216020_294551_0.png)

‌

The route reflector and clients form a
cluster. Typically a cluster has one route reflector. The ID of the route
reflector is the Cluster\_ID. You can configure more than one route reflector in
a cluster to improve availability, as shown in [Figure 10](#_Ref291493061). The
configured route reflectors must have the same Cluster\_ID to avoid routing loops.

Figure 10 Network diagram for route
reflectors

![](https://resource.h3c.com/en/202407/12/20240712_11704863_x_Img_x_png_9_2216020_294551_0.png)

‌

When the BGP routers in an AS are fully
meshed, route reflection is unnecessary because it consumes more bandwidth resources.
You can use commands to disable route reflection instead of modifying network
configuration or changing network topology.

After route reflection is disabled between
clients, routes can still be reflected between a client and a non-client.

#### Confederation

Confederation is another method to manage
growing IBGP connections in an AS. It splits an AS into multiple sub-ASs. In
each sub-AS, IBGP peers are fully meshed. As shown in [Figure 11](#_Ref279763209), intra-confederation
EBGP connections are established between sub-ASs in AS 200\.

Figure 11 Confederation network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11704811_x_Img_x_png_10_2216020_294551_0.png)

‌

A non-confederation BGP speaker does not
need to know sub-ASs in the confederation. To the BGP speaker, the confederation
is one AS and the confederation ID is the AS number. In the above figure, AS 200
is the confederation ID.

Confederation has a deficiency. When you
change an AS into a confederation, you must reconfigure the routers, and the
topology will be changed.

In large-scale BGP networks, you can use both
route reflector and confederation.

## MP-BGP

#### Supported address families

BGP-4 can only advertise IPv4 unicast
routing information. Multiprotocol Extensions for BGP-4 (MP-BGP) can advertise routing
information for the following address families:

·     IPv6 unicast address family.

·     IPv4 multicast address family and IPv6 multicast
address family.

PIM uses static and dynamic unicast routes
to perform RPF check before creating multicast routing entries. When the
multicast and unicast topologies are different, you can use MP-BGP to advertise
the routes for RPF check. MP-BGP stores the routes in the BGP multicast routing
table. For more information about PIM and RPF check, see IP Multicast Configuration Guide. 

·     VPNv4 address family.

For more information about this address
family, see MPLS Configuration Guide.

·     Labeled IPv4 unicast address family and IPv6
unicast address family.

MP-BGP advertises IPv4 unicast/IPv6
unicast routes and MPLS labels assigned for the routes. Labeled IPv4 unicast
routes apply to inter-AS Option C for MPLS L3VPN. For more information about
inter-AS Option C, see MPLS Configuration Guide.

·     L2VPN address family.

L2VPN information includes label block
information and remote peer information. For more information about L2VPN and
VPLS, see MPLS Configuration Guide.

·     EVPN address family.

MP-BGP advertises EVPN routes to implement
automatic VTEP discovery, VXLAN tunnel establishment and assignment, and MAC
and ARP information advertisement. For more information about EVPN, see EVPN Configuration Guide.

#### MP-BGP extended attributes

Prefixes and next hops are key routing information.
BGP-4 uses UPDATE messages to carry the following information:

·     Feasible route prefixes in the Network Layer Reachability
Information (NLRI) field.

·     Unfeasible route prefixes in the withdrawn routes
field.

·     Next hops in the NEXT\_HOP attribute.

BGP-4 cannot carry routing information for
multiple network layer protocols.

To support multiple network layer
protocols, MP-BGP defines the following path attributes:

·     MP\_REACH\_NLRI—Carries feasible route prefixes and next hops for multiple network
layer protocols. 

·     MP\_UNREACH\_NLRI—Carries unfeasible route prefixes for multiple network layer
protocols. 

MP-BGP uses these two attributes to
advertise feasible and unfeasible routes for different network layer protocols.
BGP speakers not supporting MP-BGP ignore updates containing these attributes
and do not forward them to its peers.

#### Address family

MP-BGP uses address families and subsequent
address families to identify different network layer protocols for routes
contained in the MP\_REACH\_NLRI and MP\_UNREACH\_NLRI attributes. For example, an
Address Family Identifier (AFI) of 2 and a Subsequent Address Family Identifier
(SAFI) of 1 identify IPv6 unicast routing information carried in the
MP\_REACH\_NLRI attribute. For address family values, see RFC 1700\.

#### Exchanging IPv4 and IPv6 routes in both IPv4 and IPv6 address families

MP-BGP supports IPv4 route exchange between
IPv6 peers and IPv6 route exchange between IPv4 peers as follows:

·     When the next hop of an IPv6 route is an IPv4
address, MP-BGP maps the IPv4 address to an IPv6 address encapsulated in the
NEXT\_HOP attribute of update messages. In this scenario, you must specify a routing policy to change
the next hop of the IPv6 route to the IPv6 address of the peer.

·     When the next hop of an IPv4 route is an IPv6
address, BGP negotiates the extended next hop encoding capability with its
peer. Then, BGP encapsulates the IPv4 NLRI in the MP\_REACH\_NLRI attribute of
update messages. In this scenario, you must specify a routing policy to change
the next hop of the IPv4 route to the IPv4 address of the peer.

Figure 12 Exchanging IPv4 and IPv6 routes in
both IPv4 and IPv6 address families

![](https://resource.h3c.com/en/202407/12/20240712_11704812_x_Img_x_png_11_2216020_294551_0.png)

 

As shown in [Figure 12](#_Ref17310763), an
IPv6 BGP peer relationship is established between Device A and Device B,
between Device B and Device C, and between Device C and Device D. An IPv4 BGP
peer relationship is established between Device A and Device B and between
Device C and Device D. Device A and Device D can learn both IPv4 and IPv6
routes from each other and traffic is forwarded correctly in both IPv4 and IPv6
address families. For Device C to correctly receive IPv4 routes using the IPv6
address of Device B as the next hop, configure a routing policy on Device C.
Use the routing policy to change the next hop of these routes to the IPv4
address of Device B.

## BGP multi-instance

A BGP router can run multiple BGP
processes. Each BGP process corresponds to a BGP instance. BGP maintains an
independent routing table for each BGP instance.

When you create multiple BGP instances,
follow these guidelines:

·     You can create multiple public address families
for a BGP instance. However, each public address family (except for IPv4
unicast address family, IPv6 unicast address family, VPNv4 address family, VPNv6 address family, and IPv4 RT-filter
address family) can belong to only one BGP instance.

·     You can create multiple VPN instances for a BGP
instance, and each VPN instance can have multiple address families. A VPN
instance can belong to only one BGP instance.

·     The IPv4 and IPv6 multicast address families
must belong to the same BGP instance.

·     Different BGP instances can have the same AS
number but cannot have the same name. 

## BGP configuration views

BGP uses different views to manage routing
information for different BGP instances, VPN instances, and address families. Most
BGP commands are available in all BGP views. BGP supports multiple VPN
instances by establishing a separate routing table for each VPN instance. 

[Table 1](#_Ref324853348) describes different BGP configuration views.

Table 1 BGP configuration views

| View names | Ways to enter the views | Remarks |
| --- | --- | --- |
| BGP instance view | \<Sysname\> system-view  \[Sysname] bgp 100 instance abc  \[Sysname-bgp-abc] | You can create a BGP instance and enter its view by specifying the instance keyword in the bgp command.  Configurations in this view apply to all public address families for the specified BGP instance. Some configurations (such as confederation, GR, and logging configurations) also apply to the address families of VPN instances. || BGP IPv4 unicast address family view | \<Sysname\> system-view  \[Sysname] bgp 100 instance abc  \[Sysname-bgp-abc] address-family ipv4 unicast  \[Sysname-bgp-abc-ipv4] | Configurations in this view apply to public IPv4 unicast routes and peers of the specified BGP instance. || BGP IPv6 unicast address family view | \<Sysname\> system-view  \[Sysname] bgp 100 instance abc  \[Sysname-bgp-abc] address-family ipv6 unicast  \[Sysname-bgp-abc-ipv6] | Configurations in this view apply to public IPv6 unicast routes and peers of the specified BGP instance. || BGP IPv4 multicast address family view | \<Sysname\> system-view  \[Sysname] bgp 100 instance abc  \[Sysname-bgp-abc] address-family ipv4 multicast  \[Sysname-bgp-abc-mul-ipv4] | Configurations in this view apply to IPv4 multicast routes and peers of the specified BGP instance. || BGP IPv6 multicast address family view | \<Sysname\> system-view  \[Sysname] bgp 100 instance abc  \[Sysname-bgp-abc] address-family ipv6 multicast  \[Sysname-bgp-abc-mul-ipv6] | Configurations in this view apply to IPv6 multicast routes and peers of the specified BGP instance. || BGP VPNv4 address family view | \<Sysname\> system-view  \[Sysname] bgp 100 instance abc  \[Sysname-bgp-abc] address-family vpnv4  \[Sysname-bgp-abc-vpnv4] | Configurations in this view apply to VPNv4 routes and peers of the specified BGP instance.  For more information about BGP VPNv4 address family view, see MPLS Configuration Guide. || BGP L2VPN address family view | \<Sysname\> system-view  \[Sysname] bgp 100 instance abc  \[Sysname-bgp-abc] address-family l2vpn  \[Sysname-bgp-abc-l2vpn] | Configurations in this view apply to L2VPN information and L2VPN peers of the specified BGP instance.  For more information about BGP L2VPN address family view, see MPLS Configuration Guide. || BGP EVPN address family view | \<Sysname\> system-view  \[Sysname] bgp 100 instance abc  \[Sysname-bgp-abc] address-family l2vpn evpn  \[Sysname-bgp-abc-evpn] | Configurations in this view apply to EVPN routes and peers of the specified BGP instance.  For more information about BGP EVPN address family view, see EVPN Configuration Guide. || BGP-VPN instance view | \<Sysname\> system-view  \[Sysname] bgp 100 instance abc  \[Sysname-bgp-abc] ip vpn-instance vpn1  \[Sysname-bgp-abc-vpn1] | Configurations in this view apply to all address families in the specified VPN instance of the specified BGP instance.  For more information about VPN instances, see MPLS L3VPN configuration in MPLS Configuration Guide. || BGP-VPN IPv4 unicast address family view | \<Sysname\> system-view  \[Sysname] bgp 100 instance abc  \[Sysname-bgp-abc] ip vpn-instance vpn1  \[Sysname-bgp-abc-vpn1] address-family ipv4 unicast  \[Sysname-bgp-abc-ipv4-vpn1] | Configurations in this view apply to IPv4 unicast routes and peers in the specified VPN instance of the specified BGP instance.  For more information about VPN instances, see MPLS L3VPN configuration in MPLS Configuration Guide. || BGP-VPN IPv6 unicast address family view | \<Sysname\> system-view  \[Sysname] bgp 100 instance abc  \[Sysname-bgp-abc] ip vpn-instance vpn1  \[Sysname-bgp-abc-vpn1] address-family ipv6 unicast  \[Sysname-bgp-abc-ipv6-vpn1] | Configurations in this view apply to IPv6 unicast routes and peers in the specified VPN instance of the specified BGP instance.  For more information about VPN instances, see MCE Configuration Guide. || BGP-VPN VPNv4 address family view | \<Sysname\> system-view  \[Sysname] bgp 100 instance abc  \[Sysname-bgp-abc] ip vpn-instance vpn1  \[Sysname-bgp-abc-vpn1] address-family vpnv4  \[Sysname-bgp-abc-vpnv4-vpn1] | Configurations in this view apply to VPNv4 routes and peers in the specified VPN instance of the specified BGP instance.  For more information about BGP-VPN VPNv4 address family view, see MPLS Configuration Guide. || BGP LS address family view | \<Sysname\> system-view  \[Sysname] bgp 100 instance abc  \[Sysname-bgp-abc] address-family link-state  \[Sysname-bgp-abc-ls] | Configurations in this view apply to LS messages and peers of the specified BGP instance. || BGP IPv4 RT filter address family view | \<Sysname\> system-view  \[Sysname] bgp 100 instance abc  \[Sysname-bgp-abc] address-family ipv4 rtfilter  \[Sysname-bgp-abc-rtf-ipv4] | Configurations in this view apply to IPv4 RT filter routes and peers of the specified BGP instance.  For more information about BGP IPv4 RT filter address family view, see MPLS L3VPN Configuration Guide. |














‌

## Protocols and standards

·     RFC 1700, ASSIGNED
NUMBERS

·     RFC 1997, BGP Communities
Attribute

·     RFC 2439, BGP Route Flap
Damping

·     RFC 2545, Use of BGP-4
Multiprotocol Extensions for IPv6 Inter-Domain Routing

·     RFC 2918, Route Refresh
Capability for BGP-4

·     RFC 4271, A Border
Gateway Protocol 4 (BGP-4)

·     RFC 4275, BGP-4 MIB
Implementation Survey

·     RFC 4277, Experience with
the BGP-4 Protocol

·     RFC 4360, BGP Extended
Communities Attribute

·     RFC 4451, BGP
MULTI\_EXIT\_DISC (MED) Consideration

·     RFC 4456, BGP Route
Reflection: An Alternative to Full Mesh Internal BGP

·     RFC 4486, Subcodes for
BGP Cease Notification Message

·     RFC 4724, Graceful
Restart Mechanism for BGP

·     RFC 4760, Multiprotocol
Extensions for BGP-4

·     RFC 5004, Avoid BGP Best
Path Transitions from One External to Another

·     RFC 5065, Autonomous
System Confederations for BGP

·     RFC 5082, The Generalized
TTL Security Mechanism (GTSM)

·     RFC 5291, Outbound Route
Filtering Capability for BGP-4

·     RFC 5292, Address-Prefix-Based
Outbound Route Filter for BGP-4

·     RFC 5549, Advertising
IPv4 Network Layer Reachability Information with an IPv6 Next Hop

·     RFC 5492, Capabilities
Advertisement with BGP-4

·     RFC 5668, 4-Octet AS
Specific BGP Extended Community

·     RFC 6198, Requirements for
the Graceful Shutdown of BGP Sessions

·     RFC 6793, BGP Support for
Four-Octet Autonomous System (AS) Number Space

·     RFC 7752, North-Bound
Distribution of Link-State and Traffic Engineering (TE) Information Using BGP

·     RFC 7854, BGP Monitoring
Protocol (BMP)

·     RFC 7911, Advertisement
of Multiple Paths in BGP

·     RFC 7947, Internet
Exchange BGP Route Server

 

