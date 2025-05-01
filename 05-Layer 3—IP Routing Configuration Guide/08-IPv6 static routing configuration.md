
# Configuring IPv6 static routing

## About IPv6 static routing

Static routes are manually configured and
cannot adapt to network topology changes. If a fault or a topological change
occurs in the network, the network administrator must modify the static routes
manually. IPv6 static routing works well in a simple IPv6 network.

## Configuring an IPv6 static route

**1\.**Enter system view.

system-view

**2\.**Configure an IPv6 static route.

Public network:

ipv6 route-static ipv6-address
prefix-length interface-type interface-number
\[ next-hop-address ] \[ permanent \| track track-entry-number ] \[ preference preference ] \[ tag tag-value ] \[ recursive-lookup ] \[ description text ]

ipv6 route-static ipv6-address
prefix-length { next-hop-address
\[ recursive-lookup host-route ] \| vpn-instance d-vpn-instance-name next-hop-address \[ recursive-lookup host-route ] } \[ permanent \| track track-entry-number ] \[ preference preference ] \[ tag tag-value ] \[ recursive-lookup ] \[ description text ]

By default, no IPv6 static route is
configured.

The recursive-lookup host-route and recursive-lookup
keywords are mutually exclusive. You cannot specify both of the keywords.

VPN:

ipv6 route-static vpn-instance s-vpn-instance-name ipv6-address
prefix-length interface-type interface-number
\[ next-hop-address ] \[ permanent \| track track-entry-number ] \[ preference preference ] \[ tag tag-value ] \[ recursive-lookup ] \[ description text ]

ipv6 route-static vpn-instance s-vpn-instance-name ipv6-address prefix-length { next-hop-address \[ recursive-lookup host-route ] \[ public ] \| vpn-instance d-vpn-instance-name next-hop-address \[ recursive-lookup host-route ] } \[ permanent \| track track-entry-number ] \[ preference preference ] \[ tag tag-value ] \[ recursive-lookup ] \[ description text ]

By default, no IPv6 static route is
configured.

The recursive-lookup host-route and recursive-lookup
keywords are mutually exclusive. You cannot specify both of the keywords.

**3\.**(Optional.) Set the default preference for
IPv6 static routes.

ipv6 route-static default-preference default-preference

The default setting is 60\.

## Configuring IPv6 floating static routes

Perform this task to configure route backup
to improve network reliability.

When an IPv6 static or dynamic route to a
destination address already exists on the device, you configure another IPv6 static
route with a lower priority as the backup route to improve the network
reliability. This backup IPv6 static route is called an IPv6 floating static
route and is activated only when the primary route fails. After the primary
route recovers from failure, the IPv6 floating static route becomes inactive
and data forwarding switches back to the primary route.

An IPv6 floating static route can be
configured in either of the following ways

·     Configure different priorities for multiple IPv6
static routes to the same destination address. The route with the lower
priority automatically becomes the IPv6 floating static route.

·     When an IPv6 route to a destination address
already exists on the device, configure an IPv6 static route with a lower
priority to the same destination address.

When you configure an IPv6 floating static
route, the priority value of the route must be larger than then priority value
of the primary route. For more information, see "[Configuring an IPv6 static route](#_Ref102412925)."

## Deleting IPv6 static routes

#### About this task

To delete an IPv6 static route, use the undo ipv6
route-static command. To delete all IPv6 static
routes including the default route, use the delete ipv6 static-routes all command.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Delete all IPv6 static routes, including the
default route.

delete ipv6 \[ vpn-instance vpn-instance-name ] static-routes all

 

| CAUTION | CAUTION:  Deleting all IPv6 static routes with caution. This operation might cause network connectivity failure and packet forwarding failure. |
| --- | --- |

‌

## Configuring BFD for IPv6 static routes

### About BFD for IPv6 static routes

BFD provides a general purpose, standard, and
medium- and protocol-independent fast failure detection mechanism. It can
uniformly and quickly detect the failures of the bidirectional forwarding paths
between two routers for protocols, such as routing protocols and MPLS. BFD for
IPv6 static routes tests the reachability of the next hop for each IPv6 static
route. If a next hop is unreachable, BFD deletes the associated IPv6 static
route.

For more information about BFD, see High Availability Configuration Guide.

### Restrictions and guidelines for BFD

When you configure BFD for IPv6 static
routes, follow these restrictions and guidelines:

·     If you specify a source IPv6 address for BFD packets
on the local device, you must specify that IPv6 address as the next hop IPv6
address on the peer device.

·     If you specify a non-P2P output interface and a
direct next hop, specify the bfd-source ipv6-address option as a best practice. Make sure the source IPv6 address of BFD
packets meets the following requirements:

¡     The
address is the same as the IPv6 address of the output interface.

¡     The
address is on the same network segment as the next hop IPv6 address of the same
type. 

For example, if the next hop IPv6 address
is a link-local address, the source IPv6 address of BFD packets must also be a
link-local address.

·     Enabling BFD for a flapping route could worsen
the situation.

### Configuring BFD control packet mode

#### About this task

This mode uses BFD control packets to
detect the status of a link bidirectionally at a millisecond level.

BFD control packet mode can be applied to
IPv6 static routes with a direct next hop or with an indirect next hop.

#### Restrictions and guidelines for BFD control packet mode

If you configure BFD control packet mode at
the local end, you must also configure this mode at the peer end.

#### Configuring BFD control packet mode for an IPv6 static route (direct next hop)

**1\.**Enter system view.

system-view

**2\.**Configure BFD control packet mode for an
IPv6 static route.

ipv6 route-static \[ vpn-instance s-vpn-instance-name ] ipv6-address prefix-length interface-type
interface-number next-hop-address bfd
control-packet \[ bfd-source ipv6-address ] \[ preference preference ] \[ tag tag-value ] \[ description text ]

By default, BFD control packet mode for
an IPv6 static route is not configured.

#### Configuring BFD control packet mode for an IPv6 static route (indirect next hop)

**1\.**Enter system view.

system-view

**2\.**Configure BFD control packet mode for an
IPv6 static route.

ipv6 route-static \[ vpn-instance s-vpn-instance-name ] ipv6-address prefix-length \[ vpn-instance d-vpn-instance-name ] { next-hop-address \[ recursive-lookup host-route ] bfd control-packet bfd-source ipv6-address } \[ preference preference ] \[ tag tag-value ] \[ description text ]

By default, BFD control packet mode for
an IPv6 static route is not configured.

### Configuring BFD echo packet mode

#### About this task

With BFD echo packet mode enabled for a
static route, the output interface sends BFD echo packets to the destination
device, which loops the packets back to test the link reachability.

#### Restrictions and guidelines

You do not need to configure BFD echo
packet mode at the peer end.

Do not use BFD for a static route with the
output interface in spoofing state.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Configure the source address of echo
packets.

bfd echo-source-ipv6 ipv6-address

By default, the source address of echo packets
is not configured.

The source address of echo packets must
be a global unicast address.

For more information about this command,
see High Availability Command Reference. 

**3\.**Configure BFD echo packet mode for an IPv6
static route.

ipv6 route-static \[ vpn-instance s-vpn-instance-name ] ipv6-address prefix-length interface-type
interface-number next-hop-address bfd echo-packet
\[ bfd-source ipv6-address ] \[ preference preference ] \[ tag tag-value ] \[ description text ]

By default, BFD echo packet mode for an
IPv6 static route is not configured.

The next hop IPv6 address must be a
global unicast address.

## Enabling periodic sending of ND requests to the next hops of IPv6 static routes

#### About this task

When the following conditions exist, a
recursive IPv6 static route becomes inactive:

·     The IPv6 static route uses a host route to reach
its direct next hop.

·     The host route is obtained either by specifying
the recursive-lookup
host-route keyword in the ipv6
route-static command or by routing policy-based
recursive lookup.

·     The host route is unavailable because no ND
entry exists for that host route on the device.

To resolve this issue, you can perform this
task to enable the device to periodically send ND requests to the direct next
hop. When the device receives an ND response from the direct next hop, it stops
sending ND requests and activates the recursive IPv6 static route.

This task applies only to a recursive IPv6 static
route that meets the following requirements:

·     The IPv6 static route has no output interface
specified.

·     The IPv6 static route fails the next-hop
recursion.

For more information about ND, see IPv6
neighbor discovery in Layer 3—IP Services Configuration
Guide.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enable periodic sending of ND requests to
the next hops of IPv6 static routes.

ipv6 route-static nd-request \[ interval interval ]

By default, periodic sending of ND
requests to the next hops of IPv6 static routes is disabled.

## Display and maintenance commands for IPv6 static routing

Execute display
commands in any view.

 

| Task | Command |
| --- | --- |
| Display IPv6 static route next hop information. | display ipv6 route-static nib \[ nib-id ] \[ verbose ] || Display IPv6 static routing table information. | display ipv6 route-static routing-table \[ vpn-instance vpn-instance-name ] \[ ipv6-address prefix-length ] || Display IPv6 static route information. | display ipv6 routing-table protocol static \[ inactive \| verbose ] |



‌

## IPv6 static routing configuration examples

### Example: Configuring basic IPv6 static route

#### Network configuration

As shown in [Figure 1](#_Ref279844271),
configure IPv6 static routes so that hosts can reach one another.

Figure 1 Network
diagram

![](https://resource.h3c.com/en/202407/12/20240712_11704871_x_Img_x_png_1_2216022_294551_0.png)

‌

#### Procedure

**1\.**Configure the IPv6 addresses for all VLAN
interfaces. (Details not shown.)

**2\.**Configure IPv6 static routes:

\# Configure a default IPv6 static route
on Switch A.

\<SwitchA\> system-view

\[SwitchA] ipv6 route-static ::
0 4::2

\# Configure two IPv6 static routes on
Switch B.

\<SwitchB\> system-view

\[SwitchB] ipv6 route-static
1:: 64 4::1

\[SwitchB] ipv6 route-static 3::
64 5::1

\# Configure a default IPv6 static route
on Switch C.

\<SwitchC\> system-view

\[SwitchC] ipv6 route-static ::
0 5::2

**3\.**Configure the IPv6 addresses for all the
hosts and configure the default gateway of Host A, Host B, and Host C as 1::1,
2::1, and 3::1.

#### Verifying the configuration

\# Display the IPv6 static route information
on Switch A.

\[SwitchA] display ipv6 routing-table
protocol static

 

Summary count : 1

 

Static Routing table status :
\<Active\>

Summary count : 1

 

Destination:
::/0                                        Protocol  : Static

NextHop    :
4::2                                        Preference: 60

Interface  :
Vlan200                                     Cost      : 0

 

Static Routing table status :
\<Inactive\>

Summary count : 0

\# Display the IPv6 static route information
on Switch B.

\[SwitchB] display ipv6 routing-table
protocol static

 

Summary count : 2

 

Static Routing table status :
\<Active\>

Summary count : 2

 

Destination:
1::/64                                      Protocol  : Static

NextHop    :
4::1                                        Preference: 60

Interface  :
Vlan200                                     Cost      : 0

 

Destination: 3::/64                                      Protocol 
: Static

NextHop    :
5::1                                        Preference: 60

Interface  :
Vlan300                                     Cost      : 0

 

Static Routing table status :
\<Inactive\>

Summary count : 0

\# Use the ping command to test
the reachability.

\[SwitchA] ping ipv6 3::1

Ping6(56 data bytes) 4::1 --\> 3::1,
press CTRL\+C to break

56 bytes from 3::1, icmp\_seq\=0 hlim\=62
time\=0.700 ms

56 bytes from 3::1, icmp\_seq\=1 hlim\=62
time\=0.351 ms

56 bytes from 3::1, icmp\_seq\=2 hlim\=62
time\=0.338 ms

56 bytes from 3::1, icmp\_seq\=3 hlim\=62
time\=0.373 ms

56 bytes from 3::1, icmp\_seq\=4 hlim\=62
time\=0.316 ms

 

\--- Ping6 statistics for 3::1 \---

5 packet(s) transmitted, 5 packet(s)
received, 0.0% packet loss

round-trip min/avg/max/std-dev \=
0.316/0.416/0.700/0.143 ms

### Example: Configuring BFD for IPv6 static routes (direct next hop)

#### Network configuration

As shown in [Figure 2](#_Ref311450821):

·     Configure an IPv6 static route to subnet 120::/64
on Switch A.

·     Configure an IPv6 static route to subnet 121::/64
on Switch B.

·     Enable BFD for both routes.

·     Configure an IPv6 static route to subnet 120::/64
and an IPv6 static route to subnet 121::/64 on Switch C.

When the link between Switch A and Switch B
through the Layer 2 switch fails, BFD can detect the failure immediately, and
Switch A and Switch B can communicate through Switch C.

Figure 2 Networkdiagram

![](https://resource.h3c.com/en/202407/12/20240712_11704872_x_Img_x_png_2_2216022_294551_0.png)

‌

Table 1 Interface and IP address assignment

| Device | Interface | IPv6 address |
| --- | --- | --- |
| Switch A | Vlan-int10 | 12::1/64 || Switch A | Vlan-int11 | 10::102/64 || Switch B | Vlan-int10 | 12::2/64 || Switch B | Vlan-int13 | 13::1/64 || Switch C | Vlan-int11 | 10::100/64 || Switch C | Vlan-int13 | 13::2/64 |






‌

#### Procedure

**1\.**Configure IPv6 addresses for interfaces. (Details
not shown.)

**2\.**Configure IPv6 static routes and BFD:

\# Configure IPv6 static routes on Switch
A and enable BFD control packet mode for the static route that traverses the
Layer 2 switch.

\<SwitchA\> system-view

\[SwitchA] interface vlan-interface
10

\[SwitchA-vlan-interface10] bfd
min-transmit-interval 500

\[SwitchA-vlan-interface10] bfd
min-receive-interval 500

\[SwitchA-vlan-interface10] bfd
detect-multiplier 9

\[SwitchA-vlan-interface10]
quit

\[SwitchA] ipv6 route-static
120:: 64 vlan-interface 10 12::2 bfd control-packet

\[SwitchA] ipv6 route-static
120:: 64 10::100 preference 65

\[SwitchA] quit

\# Configure IPv6 static routes on Switch
B and enable BFD control packet mode for the static route that traverses the
Layer 2 switch.

\<SwitchB\> system-view

\[SwitchB] interface vlan-interface
10

\[SwitchB-vlan-interface10] bfd
min-transmit-interval 500

\[SwitchB-vlan-interface10] bfd
min-receive-interval 500

\[SwitchB-vlan-interface10] bfd
detect-multiplier 9

\[SwitchB-vlan-interface10]
quit

\[SwitchB] ipv6 route-static
121:: 64 vlan-interface 10 12::1 bfd control-packet

\[SwitchB] ipv6 route-static
121:: 64 vlan-interface 13 13::2 preference 65

\[SwitchB] quit

\# Configure IPv6 static routes on Switch
C.

\<SwitchC\> system-view

\[SwitchC] ipv6 route-static
120:: 64 13::1

\[SwitchC] ipv6 route-static
121:: 64 10::102

#### Verifying the configuration

\# Display the BFD sessions on Switch A.

\<SwitchA\> display bfd session

 Total sessions: 1        Up
sessions: 1        Init mode: Active

 

 IPv6 session working in control
packet mode:

 

       Local Discr: 513                 Remote Discr: 33

         Source IP: 12::1

    Destination IP: 12::2

     Session State: Up                      Interface: Vlan10

         Hold Time: 2012ms

The output shows that the BFD session has
been created.

\# Display IPv6 static routes on Switch A.

\<SwitchA\> display ipv6
routing-table protocol static

 

Summary count : 1

 

Static Routing table status :
\<Active\>

Summary count : 1

 

Destination: 120::/64                                  
 Protocol  : Static

NextHop    : 12::2                               
       Preference: 60

Interface  : Vlan10                                      Cost      : 0

 

Direct Routing table status :
\<Inactive\>

Summary count : 0

The output shows that Switch A communicates
with Switch B through VLAN-interface 10\. The link over VLAN-interface 10 fails.

\# Display IPv6 static routes on Switch A
again.

\<SwitchA\> display ipv6
routing-table protocol static

 

Summary count : 1

 

Static Routing table status :
\<Active\>

Summary count : 1

 

Destination:
120::/64                                    Protocol  : Static

NextHop    :
10::100                                     Preference: 65

Interface  : Vlan11                                      Cost      : 0

 

Static Routing table status : \<
Inactive\>

Summary count : 0

The output shows that Switch A communicates
with Switch B through VLAN-interface 11\.

### Example: Configuring BFD for IPv6 static routes (indirect next hop)

#### Network configuration

As shown in [Figure 3](#_Ref311450851):

·     Switch A has a route to interface Loopback 1 (2::9/128)
on Switch B, and the output interface is VLAN-interface 10\.

·     Switch B has a route to interface Loopback 1 (1::9/128)
on Switch A, and the output interface is VLAN-interface 12\.

·     Switch D has a route to 1::9/128, and the output
interface is VLAN-interface 10\. It also has a route to 2::9/128, and the output
interface is VLAN-interface 12\.

Configure the following:

·     Configure an IPv6 static route to subnet 120::/64
on Switch A.

·     Configure an IPv6 static route to subnet 121::/64
on Switch B.

·     Enable BFD for both routes.

·     Configure an IPv6 static route to subnet 120::/64
and an IPv6 static route to subnet 121::/64 on both Switch C and Switch D.

When the link between Switch A and Switch B
through Switch D fails, BFD can detect the failure immediately and Switch A and
Switch B can communicate through Switch C.

Figure 3 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11704873_x_Img_x_png_3_2216022_294551_0.png)

‌

Table 2 Interface and IP address assignment

| Device | Interface | IPv6 address |
| --- | --- | --- |
| Switch A | Vlan-int10 | 12::1/64 || Switch A | Vlan-int11 | 10::102/64 || Switch A | Loop1 | 1::9/128 || Switch B | Vlan-int12 | 11::2/64 || Switch B | Vlan-int13 | 13::1/64 || Switch B | Loop1 | 2::9/128 || Switch C | Vlan-int11 | 10::100/64 || Switch C | Vlan-int13 | 13::2/64 || Switch D | Vlan-int10 | 12::2/64 || Switch D | Vlan-int12 | 11::1/64 |










‌

#### Procedure

**1\.**Configure IPv6 addresses for interfaces. (Details
not shown.)

**2\.**Configure IPv6 static routes and BFD:

\# Configure IPv6 static routes on Switch
A and enable BFD control packet mode for the IPv6 static route that traverses Switch
D.

\<SwitchA\> system-view

\[SwitchA] bfd multi-hop
min-transmit-interval 500

\[SwitchA] bfd multi-hop
min-receive-interval 500

\[SwitchA] bfd multi-hop
detect-multiplier 9

\[SwitchA] ipv6 route-static
120:: 64 2::9 bfd control-packet bfd-source 1::9

\[SwitchA] ipv6 route-static
120:: 64 10::100 preference 65

\[SwitchA] ipv6 route-static
2::9 128 12::2

\[SwitchA] quit

\# Configure IPv6 static routes on Switch B
and enable BFD control packet mode for the static route that traverses Switch D.

\<SwitchB\> system-view

\[SwitchB] bfd multi-hop
min-transmit-interval 500

\[SwitchB] bfd multi-hop
min-receive-interval 500

\[SwitchB] bfd multi-hop
detect-multiplier 9

\[SwitchB] ipv6 route-static
121:: 64 1::9 bfd control-packet bfd-source 2::9

\[SwitchB] ipv6 route-static
121:: 64 13::2 preference 65

\[SwitchB] ipv6 route-static
1::9 128 11::1

\[SwitchB] quit

\# Configure IPv6 static routes on Switch
C.

\<SwitchC\> system-view

\[SwitchC] ipv6 route-static
120:: 64 13::1

\[SwitchC] ipv6 route-static
121:: 64 10::102

\# Configure IPv6 static routes on Switch
D.

\<SwitchD\> system-view

\[SwitchD] ipv6 route-static
120:: 64 11::2

\[SwitchD] ipv6 route-static
121:: 64 12::1

\[SwitchD] ipv6 route-static
2::9 128 11::2

\[SwitchD] ipv6 route-static
1::9 128 12::1

#### Verifying the configuration

\# Display the BFD sessions on Switch A.

\<SwitchA\> display bfd session

 Total sessions: 1        Up
sessions: 1        Init mode: Active

 

 IPv6 session working in control
packet mode:

 

       Local Discr: 513                 Remote Discr: 33

         Source IP: 1::9

    Destination IP: 2::9

     Session State: Up                      Interface: N/A

         Hold Time: 2012ms

The output shows that the BFD session has
been created.

\# Display the IPv6 static routes on Switch
A.

\<SwitchA\> display ipv6
routing-table protocol static

 

Summary count : 1

 

Static Routing table status :
\<Active\>

Summary count : 1

 

Destination: 120::/64                                  
 Protocol  : Static

NextHop    : 2::9                                        Preference: 60

Interface  : Vlan10                                      Cost      : 0

 

Static Routing table status :
\<Inactive\>

Summary count : 0

The output shows that Switch A communicates
Switch B through VLAN-interface 10\. The link over VLAN-interface 10 fails.

\# Display IPv6 static routes on Switch A again.

\<SwitchA\> display ipv6
routing-table protocol static

 

Summary count : 1

 

Static Routing table status :
\<Active\>

Summary count : 1

 

Destination: 120::/64                                
   Protocol  : Static

NextHop    : 10::100                             
       Preference: 65

Interface  : Vlan11                                      Cost      : 0

 

Static Routing table status :
\<Inactive\>

Summary count : 0

The output shows that Switch A communicates
with Switch B through VLAN-interface 11\.

