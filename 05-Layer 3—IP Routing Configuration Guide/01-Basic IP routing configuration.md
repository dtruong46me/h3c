
# Configuring basic IP routing

This chapter
focuses on unicast routing protocols. For more information about multicast
routing protocols, see IP Multicast Configuration Guide.

## About IP routing

IP routing directs IP packet forwarding on
routers. Based on the destination IP address in the packet, a router looks up a
route for the packet in a routing table and forwards the packet to the next
hop. Routes are path information used to direct IP packets.

### Routing table

A RIB contains the global routing
information and related information, including route recursion, route
redistribution, and route extension information. The router selects optimal
routes from the routing table and puts them into the FIB table. It uses the FIB
table to forward packets. For more information about the FIB table, see Layer 3—IP Services Configuration Guide.

### Route categories

[Table 1](#_Ref308595736) categorizes routes by different criteria.

Table 1 Route categories

| Criterion | Categories |
| --- | --- |
| Origin | ·     Direct route—A direct route is discovered by the data link protocol on an interface, and is also called an interface route.  ·     Static route—A static route is manually configured by an administrator.  ·     Dynamic route—A dynamic route is dynamically discovered by a routing protocol. || Destination | ·     Network route—The destination is a network. The subnet mask is less than 32 bits.  ·     Host route—The destination is a host. The subnet mask is 32 bits. || Whether the destination is directly connected | ·     Direct route—The destination is directly connected.  ·     Indirect route—The destination is indirectly connected. |



 

### Dynamic routing protocols

Static routes work well in small, stable
networks. They are easy to configure and require fewer system resources.
However, in networks where topology changes occur frequently, a typical
practice is to configure a dynamic routing protocol. Compared with static
routing, a dynamic routing protocol is complicated to configure, requires more router
resources, and consumes more network resources.

Dynamic routing protocols dynamically
collect and report reachability information to adapt to topology changes. They
are suitable for large networks.

Dynamic routing protocols can be classified
by different criteria, as shown in [Table 2](#_Ref277578761).

Table 2 Categories of dynamic routing
protocols

| Criterion | Categories |
| --- | --- |
| Operation scope | ·     IGPs—Work within an AS. Examples include RIP, OSPF, and IS-IS.  ·     EGPs—Work between ASs. The most popular EGP is BGP. || Routing algorithm | ·     Distance-vector protocols—Examples include RIP and BGP. BGP is also considered a path-vector protocol.  ·     Link-state protocols—Examples include OSPF and IS-IS. || Destination address type | ·     Unicast routing protocols—Examples include RIP, OSPF, BGP, and IS-IS.  ·     Multicast routing protocols—Examples include PIM-SM and PIM-DM. || IP version | ·     IPv4 routing protocols—Examples include RIP, OSPF, BGP, and IS-IS.  ·     IPv6 routing protocols—Examples include RIPng, OSPFv3, IPv6 BGP, and IPv6 IS-IS. |




 

An AS refers to a group of routers that use
the same routing policy and work under the same administration.

### Route preference

Routing protocols, including static and
direct routing, each by default have a preference. If they find multiple routes
to the same destination, the router selects the route with the highest
preference as the optimal route.

The preference of a direct route is always
0 and cannot be changed. You can configure a preference for each static route
and each dynamic routing protocol. The following table lists the route types
and default preferences. The smaller the value, the higher the preference.

Table 3 Route types and default route preferences

| Route type | Preference |
| --- | --- |
| Direct route | 0 || Multicast static route | 1 || OSPF | 10 || IS-IS | 15 || Unicast static route | 60 || RIP | 100 || OSPF ASE | 150 || OSPF NSSA | 150 || IBGP | 255 || EBGP | 255 || Unknown (route from an untrusted source) | 256 |











 

### Load sharing

A routing protocol might find multiple optimal
equal-cost routes to the same destination. You can use these routes to
implement equal-cost multi-path (ECMP) load sharing.

Static routing, IPv6 static routing, RIP, RIPng,
OSPF, OSPFv3, BGP, IPv6 BGP, IS-IS, and IPv6 IS-IS support ECMP load sharing. 

### Route backup

Route backup can improve network availability.
Among multiple routes to the same destination, the route with the highest priority
is the primary route and others are secondary routes.

The router forwards matching packets through
the primary route. When the primary route fails, the route with the highest
preference among the secondary routes is selected to forward packets. When the primary
route recovers, the router uses it to forward packets.

### Route recursion

To use a static, BGP, or RIP route that has
an indirectly connected next hop, a router must perform route recursion to find
the output interface to reach the next hop.

Link-state routing protocols, such as OSPF
and IS-IS, do not need route recursion, because they obtain directly connected
next hops through route calculation.

The RIB records and saves route recursion
information, including brief information about related routes, recursive paths,
and recursion depth.

### Route redistribution

Route redistribution enablesrouting
protocols to learn routing information from each other. A dynamic routing protocol
can redistribute routes from other routing protocols, including direct and
static routing. For more information, see the respective chapters on those
routing protocols in this configuration guide.

The RIB records redistribution
relationships of routing protocols.

### Extension attribute redistribution

Extension attribute redistribution enables routing
protocols to learn route extension attributes from each other, including BGP
extended community attributes, OSPF area IDs, route types, and router IDs.

The RIB records extended attributes of each
routing protocol and redistribution relationships of different routing protocol
extended attributes.

## Setting the maximum lifetime for routes and labels in the RIB

#### About this task

Perform this task to prevent routes of a certain
protocol from being aged out due to slow protocol convergence resulting from a
large number of route entries or long GR period.

#### Restrictions and guidelines

The configuration takes effect at the next
protocol or RIB process switchover.

#### Procedure (IPv4)

**1\.**Enter system view.

system-view

**2\.**Enter RIB view.

rib

**3\.**Create the RIB IPv4 address family and enter
its view.

address-family ipv4

**4\.**Set the maximum lifetime for IPv4 routes and
labels in the RIB.

protocol protocol \[ instance instance-name ] lifetime seconds

By default, the maximum lifetime for
routes and labels in the RIB is 480 seconds.

#### Procedure (IPv6)

**1\.**Enter system view.

system-view 

**2\.**Enter RIB view.

rib 

**3\.**Create the RIB IPv6 address family and enter
its view.

address-family ipv6

**4\.**Set the maximum lifetime for IPv6 routes and
labels in the RIB.

protocol protocol \[ instance instance-name ] lifetime seconds

By default, the maximum lifetime for
routes and labels in the RIB is 480 seconds.

## Setting the maximum lifetime for routes in the FIB

#### About this task

When GR or NSR is disabled, FIB entries
must be retained for some time after a protocol process switchover or RIB
process switchover. When GR or NSR is enabled, FIB entries must be removed
immediately after a protocol or RIB process switchover to avoid routing issues.
Perform this task to meet such requirements.

#### Procedure (IPv4)

**1\.**Enter system view.

system-view 

**2\.**Enter RIB view.

rib 

**3\.**Create the RIB IPv4 address family and enter
its view.

address-family ipv4

**4\.**Set the maximum lifetime for IPv4 routes in
the FIB.

fib lifetime seconds 

By default, the maximum lifetime for
routes in the FIB is 600 seconds.

#### Procedure (IPv6)

**1\.**Enter system view.

system-view 

**2\.**Enter RIB view.

rib 

**3\.**Create the RIB IPv6 address family and enter
its view.

address-family ipv6

**4\.**Set the maximum lifetime for IPv6 routes in
the FIB.

fib lifetime seconds 

By default, the maximum lifetime for
routes in the FIB is 600 seconds.

## Enabling the RIB to flush route attribute information to the FIB

**1\.**Enter system view.

system-view

**2\.**Enter RIB view.

rib

**3\.**Create the RIB IPv4 address family and enter
its view.

address-family ipv4

**4\.**Enable the RIB to flush route attribute
information to the FIB.

flush route-attribute protocol

By default, the RIB does not flush route
attribute information to the FIB.

## Setting the maximum number of ECMP routes

#### Restrictions and guidelines

This configuration takes effect at reboot.
Make sure the reboot does not impact your network.

#### Procedure (IPv4)

**1\.**Enter system view.

system-view 

**2\.**Set the maximum number of ECMP routes.

max-ecmp-num number 

By default, the maximum number of ECMP
routes is 8\.

## Configuring the ECMP mode

#### About this task

An ECMP route group contains ECMP routes to
the same destination. The following ECMP modes are supported:

·     Normal mode—When one or multiple ECMP routes in an ECMP group fail, this mode
enables the device to reallocate all traffic to the remaining routes in the
ECMP group. 

·     Enhanced mode—When one or multiple ECMP routes in an ECMP group fail, this mode
enables the device to reallocate only the traffic of the failed routes to the
remaining routes.

#### Restrictions and guidelines

This configuration takes effect at reboot.
Make sure the reboot does not impact your network.

The task applies to both IPv4 and IPv6 ECMP
routes.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Configure the ECMP mode.

ecmp mode enhanced

By default, the normal
ECMP mode is used.

## Configuring RIB NSR

#### About this task

When an active/standby switchover occurs,
nonstop routing (NSR) backs up routing information from the active process to
the standby process to avoid routing flapping and ensure forwarding continuity.

RIB NSR provides faster route convergence
than protocol NSR during an active/standby switchover.

#### Restrictions and guidelines

Use this feature with protocol GR or NSR to
avoid route timeouts and traffic interruption.

#### Procedure (IPv4)

**1\.**Enter system view.

system-view 

**2\.**Enter RIB view.

rib 

**3\.**Create the RIB IPv4 address family and enter
its view.

address-family ipv4

**4\.**Enable IPv4 RIB NSR.

non-stop-routing 

By default, RIB NSR is disabled.

#### Procedure (IPv6)

**1\.**Enter system view.

system-view 

**2\.**Enter RIB view.

rib 

**3\.**Create the RIB IPv6 address family and enter
its view.

address-family ipv6

**4\.**Enable IPv6 RIB NSR.

non-stop-routing 

By default, RIB NSR is disabled.

## Configuring inter-protocol FRR

#### About this task

This feature is applicable to services that
are sensitive to packet loss and latency. Inter-protocol fast reroute (FRR)
enables fast rerouting between routes of different protocols. A backup next hop
is automatically selected to reduce the service interruption time caused by
unreachable next hops. When the next hop of the primary link fails, the traffic
is redirected to the backup next hop.

Among the routes to the same destination in
the RIB, a router adds the route with the highest preference to the FIB table. After
you configure this feature, the device will select a route of another routing
protocol as the backup route. When the next hop of the primary route is
unreachable, the device forwards packets through the backup route.

#### Restrictions and guidelines

This feature uses the next hop of a route
from a different protocol as the backup next hop, which might cause loops.

If you configure both inter-protocol FRR
and FRR for the specified routing protocol, FRR for the specified routing
protocol takes effect.

#### Procedure (IPv4)

**1\.**Enter system view.

system-view 

**2\.**Enter RIB view.

rib 

**3\.**Create the RIB IPv4 address family and enter
its view.

address-family ipv4

**4\.**Enable IPv4 RIB inter-protocol FRR.

inter-protocol fast-reroute \[ vpn-instance
vpn-instance-name ]

By default, inter-protocol FRR is
disabled.

If you do not specify a VPN instance,
inter-protocol FRR is enabled for the public network.

#### Procedure (IPv6)

**1\.**Enter system view.

system-view 

**2\.**Enter RIB view.

rib

**3\.**Create the RIB IPv6 address family and enter
its view.

address-family ipv6

**4\.**Enable IPv6 RIB inter-protocol FRR.

inter-protocol fast-reroute \[ vpn-instance
vpn-instance-name ]

By default, inter-protocol FRR is
disabled.

If you do not specify a VPN instance,
inter-protocol FRR is enabled for the public network.

## Enabling route fast switchover

#### About this task

This feature applies to a device that
provides the same physical output interface for large numbers of routes,
including ECMP routes and primary/secondary routes. When a link failure occurs
on the interface, the device must perform the following tasks before switching
the traffic to another route:

**1\.**Deletes all ARP or ND entries for the link.

**2\.**Instructs the FIB to delete the associated
FIB entries.

This procedure is time consuming and
interrupts traffic for a long time. To resolve this problem, you can enable
route fast switchover. This feature allows the device to instruct the FIB to
delete the invalid FIB entries for route switchover first.

#### Procedure (IPv4)

**1\.**Enter system view.

system-view

**2\.**Enable IPv4 route fast switchover.

ip route fast-switchover enable

By default, IPv4 route fast switchover is
disabled.

#### Procedure (IPv6)

**1\.**Enter system view.

system-view

**2\.**Enable IPv6 route fast switchover.

ipv6 route fast-switchover enable

By default, IPv6 route fast switchover is
disabled.

## Configuring routing policy-based recursive lookup

#### About this task

Configure routing policy-based recursive
lookup to control route recursion results. For example, when a route changes,
the routing protocol has to perform a route recursion if the next hop is indirectly
connected. The routing protocol might select an incorrect path, which can cause
traffic loss. To prevent this problem, you can use a routing policy to filter
out incorrect routes. The routes that pass the filtering of the routing policy
will be used for route recursion.

#### Restrictions and guidelines

The apply
clauses in the specified routing policy cannot take effect.

Make sure a minimum of one related route
can match the routing policy for correct traffic forwarding.

#### Procedure (IPv4)

**1\.**Enter system view.

system-view

**2\.**Enter RIB view.

rib

**3\.**Create the RIB IPv4 address family and enter
its view.

address-family ipv4

**4\.**Configure routing policy-based recursive
lookup.

protocol protocol nexthop recursive-lookup route-policy
route-policy-name

By default, routing policy-based
recursive lookup is not configured.

#### Procedure (IPv6)

**1\.**Enter system view.

system-view

**2\.**Enter RIB view.

rib

**3\.**Create the RIB IPv6 address family and enter
its view.

address-family ipv6

**4\.**Configure routing policy-based recursive
lookup.

protocol protocol nexthop recursive-lookup route-policy
route-policy-name

By default, routing policy-based
recursive lookup is not configured.

## Setting the maximum number of active routes supported by the device

#### About this task

To perform this task: 

·     You can use the routing-table
limit number
warn-threshold command to specify an alarm threshold. When the percentage of active routes
exceeds the alarm threshold, the system generates a system log message but
still accepts active routes. You can take relevant actions based on the message
to save system resources. If the number of active routes reaches the maximum
number, no more routes can be added and new routes are discarded.

·     You can use the routing-table
limit number simply-alert command to specify an alarm threshold. When the maximum number of
active IPv4/IPv6 routes is exceeded, the device still accepts new active routes
but generates a system log message. You can take relevant actions based on the
message to save system resources. 

#### Procedure (IPv4)

**1\.**Enter system view.

system-view

**2\.**Enter RIB view.

rib

**3\.**Create the RIB IPv4 address family and enter
its view.

address-family ipv4

**4\.**Set the maximum number of active IPv4 routes
supported by the device.

routing-table limit number {
warn-threshold \| simply-alert }

By default, the maximum number of active
IPv4 routes is not set for the device.

Configuration in RIB IPv4 address family
view limits the number of active IPv4 routes for the public network and all VPN
instances. 

#### Procedure (IPv6)

**1\.**Enter system view.

system-view

**2\.**Enter RIB view.

rib

**3\.**Create the RIB IPv6 address family and enter
its view.

address-family ipv6

**4\.**Set the maximum number of active IPv6 routes
supported by the device.

routing-table limit number {
warn-threshold \| simply-alert }

By default, the maximum number of active
IPv6 routes is not set for the device.

Configuration in RIB IPv6 address family
view limits the number of active IPv6 routes for the public network and all VPN
instances. 

## Enabling MTP

#### About this task

Use maintenance probe (MTP) to locate
faults for routing protocols depending on your network maintenance
requirements. MTP enables the device to automatically ping and tracert a
neighbor upon expiration of the neighbor hold timer and record the ping and
tracert results. To view detailed fault information, use the display commands of routing protocols, for example, the display bgp
troubleshooting command. To view detailed MTP information,
use the display logbuffer command.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enable MTP.

maintenance-probe enable

By default, MTP is disabled.

## Display and maintenance commands for basicIP routing

Execute display
commands in any view and reset commands in user
view.

 

| Task | Command |
| --- | --- |
| Display the ECMP mode. | display ecmp mode || Display routing table information. | display ip routing-table \[ all-vpn-instance \|  vpn-instance vpn-instance-name ] \[ verbose ]  display ip routing-table all-routes || Display information about routes permitted by an IPv4 basic ACL. | display ip routing-table \[ vpn-instance vpn-instance-name ] acl ipv4-acl-number \[ verbose ] || Display information about routes to a specific destination address. | display ip routing-table \[ vpn-instance vpn-instance-name ] ip-address \[ mask-length \| mask ] \[ longer-match ] \[ verbose ] || Display information about routes to a range of destination addresses. | display ip routing-table \[ vpn-instance vpn-instance-name ] ip-address1 to ip-address2 \[ verbose ] || Display information about routes permitted by an IP prefix list. | display ip routing-table \[ vpn-instance vpn-instance-name ] prefix-list prefix-list-name \[ verbose ] || Display information about routes installed by a protocol. | display ip routing-table \[ vpn-instance vpn-instance-name ] protocol protocol \[ inactive \| verbose ] || Display IPv4 route statistics. | display ip routing-table \[ all-routes \| all-vpn-instance \| vpn-instance vpn-instance-name ] statistics || Display brief IPv4 routing table information. | display ip routing-table \[ vpn-instance vpn-instance-name ] summary || Display route attribute information in the IPv6 RIB. | display ipv6 rib attribute \[ attribute-id ] || Display IPv6 RIB GR state information. | display ipv6 rib graceful-restart || Display next hop information in the IPv6 RIB. | display ipv6 rib nib \[ self-originated ] \[ nib-id ] \[ verbose ]  display ipv6 rib nib protocol protocol \[ verbose ] || Display next hop information for IPv6 direct routes. | display ipv6 route-direct nib \[ nib-id ] \[ verbose ] || Display IPv6 routing table information. | display ipv6 routing-table \[ all-vpn-instance \| vpn-instance vpn-instance-name ] \[ verbose ]  display ipv6 routing-table all-routes || Display information about routes permitted by an IPv6 basic ACL. | display ipv6 routing-table \[ vpn-instance vpn-instance-name ] acl ipv6-acl-number \[ verbose ] || Display information about routes to an IPv6 destination address. | display ipv6 routing-table \[ vpn-instance vpn-instance-name ] ipv6-address \[ prefix-length ] \[ longer-match ] \[ verbose ] || Display information about routes to a range of IPv6 destination addresses. | display ipv6 routing-table \[ vpn-instance vpn-instance-name ] ipv6-address1 to ipv6-address2 \[ verbose ] || Display information about routes permitted by an IPv6 prefix list. | display ipv6 routing-table \[ vpn-instance vpn-instance-name ] prefix-list prefix-list-name \[ verbose ] || Display information about routes installed by an IPv6 protocol. | display ipv6 routing-table \[ vpn-instance vpn-instance-name ] protocol protocol \[ inactive \| verbose ] || Display IPv6 route statistics. | display ipv6 routing-table \[ all-routes \| all-vpn-instance \| vpn-instance vpn-instance-name ] statistics || Display brief IPv6 routing table information. | display ipv6 routing-table \[ vpn-instance vpn-instance-name ] summary || Display the maximum number of IPv4 ECMP routes. | display max-ecmp-num || Display route attribute information in the RIB. | display rib attribute \[ attribute-id ] || Display RIB GR state information. | display rib graceful-restart || Display next hop information in the RIB. | display rib nib \[ self-originated ] \[ nib-id ] \[ verbose ]  display rib nib protocol protocol \[ verbose ] || Display next hop information for direct routes. | display route-direct nib \[ nib-id ] \[ verbose ] || Clear IPv4 route statistics. | reset ip routing-table statistics protocol \[ vpn-instance vpn-instance-name ] { protocol \| all }  reset ip routing-table \[ all-routes \| all-vpn-instance ] statistics protocol { protocol \| all } || Clear IPv6 route statistics. | reset ipv6 routing-table statistics protocol \[ vpn-instance vpn-instance-name ] { protocol \| all }  reset ipv6 routing-table \[ all-routes \| all-vpn-instance ] statistics protocol { protocol \| all } |




























 

 

