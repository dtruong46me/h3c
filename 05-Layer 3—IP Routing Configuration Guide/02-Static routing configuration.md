
# Configuring static routing

## About static routes

Static routes are manually configured. If a
network's topology is simple, you only need to configure static routes for the
network to work correctly. 

Static routes
cannot adapt to network topology changes. If a fault or a topological change
occurs in the network, the network administrator must modify the static routes
manually.

## Configuring a static route

**1\.**Enter system view.

system-view

**2\.**Configure a static route.

Public network:

ip route-static dest-address { mask-length \| mask } interface-type interface-number \[ next-hop-address \[ nexthop-index index-string ] ] \[ permanent \| track track-entry-number ] \[ preference preference ] \[ tag tag-value ] \[ recursive-lookup ] \[ description text ]

ip route-static dest-address { mask-length \| mask } { next-hop-address \[ nexthop-index index-string ] \[ recursive-lookup host-route ] \| vpn-instance d-vpn-instance-name next-hop-address \[ nexthop-index index-string ] \[ recursive-lookup host-route ] } \[ permanent \| track track-entry-number ] \[ preference preference ] \[ tag tag-value ] \[ recursive-lookup ] \[ description text ]

By default, no static route is
configured.

You can associate Track with a static
route to monitor the reachability of the next hops. For more information about Track,
see High Availability Configuration Guide.

The recursive-lookup
host-route and recursive-lookup keywords are mutually exclusive. You cannot specify both of the
keywords.

VPN:

ip route-static vpn-instance s-vpn-instance-name dest-address { mask-length \| mask } interface-type interface-number
\[ next-hop-address \[ nexthop-index index-string ] ] \[ permanent \| track track-entry-number ] \[ preference preference ] \[ tag tag-value ] \[ recursive-lookup ] \[ description text ]

ip route-static vpn-instance s-vpn-instance-name dest-address { mask-length \| mask } { next-hop-address \[ nexthop-index index-string ] \[ recursive-lookup host-route ] \[ public ] \| vpn-instance d-vpn-instance-name next-hop-address \[ nexthop-index index-string ] \[ recursive-lookup host-route ] } \[ permanent \| track track-entry-number ] \[ preference preference ] \[ tag tag-value ] \[ recursive-lookup ] \[ description text ]

By default, no static route is
configured.

You can associate Track with a static
route to monitor the reachability of the next hops. For more information about Track,
see High Availability Configuration Guide.

The recursive-lookup host-route and recursive-lookup
keywords are mutually exclusive. You cannot specify both of the keywords.

**3\.**(Optional.) Enable periodic sending of ARP
requests to the next hops of static routes.

ip route-static arp-request interval interval

By default, the device does not send ARP
requests to the next hops of static routes.

**4\.**(Optional.) Configure the default preference
for static routes.

ip route-static default-preference default-preference

The default setting is 60\.

## Configuring a static route group

#### About this task

This task allows you to batch create static
routes with different prefixes but the same output interface and next hop.

You can create a static route group, and specify
the static group in the ip route-static command.
All prefixes in the static route group will be assigned the next hop and output
interface specified in the ip route-static
command.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Create a static route group and enter its
view.

ip route-static-group group-name

By default, no static route group is
configured.

**3\.**Add a static route prefix to the static
route group.

prefix dest-address { mask-length \| mask }

By default, no static route prefix is
added to the static route group.

**4\.**Return to system view.

quit

**5\.**Configure a static route.

Public network:

ip route-static group group-name interface-type interface-number
\[ next-hop-address ] \[ permanent \| track track-entry-number ] \[ preference preference ] \[ tag tag-value ] \[ recursive-lookup ] \[ description text ]

ip route-static group group-name { next-hop-address \[ recursive-lookup host-route ] \| vpn-instance
d-vpn-instance-name next-hop-address \[ recursive-lookup host-route ] } \[ permanent \| track track-entry-number
] \[ preference preference ] \[ tag tag-value ] \[ recursive-lookup ] \[ description text ]

The recursive-lookup host-route and recursive-lookup
keywords are mutually exclusive. You cannot specify both of the keywords.

VPN:

ip route-static vpn-instance s-vpn-instance-name group group-name interface-type interface-number \[ next-hop-address ] \[ permanent \| track track-entry-number ] \[ preference preference ] \[ tag tag-value ] \[ recursive-lookup ] \[ description text ]

ip route-static vpn-instance s-vpn-instance-name group group-name { next-hop-address \[ recursive-lookup host-route ] \[ public ] \| vpn-instance
d-vpn-instance-name next-hop-address \[ recursive-lookup host-route ] } \[ permanent \| track track-entry-number
] \[ preference preference ] \[ tag tag-value ] \[ recursive-lookup ] \[ description text ]

The recursive-lookup host-route and recursive-lookup
keywords are mutually exclusive. You cannot specify both of the keywords.

By default, no static route is
configured.

## Configuring floating static routes

Perform this task to configure route backup
to improve network reliability.

When a static or dynamic route to a
destination address already exists on the device, you configure another static
route with a lower priority as the backup route to improve the network
reliability. This backup static route is called a floating static route and is
activated only when the primary route fails. After the primary route recovers
from failure, the floating static route becomes inactive and data forwarding
switches back to the primary route.

A floating static route can be configured
in either of the following ways

·     Configure different priorities for multiple
static routes to the same destination address. The route with the lower
priority automatically becomes the floating static route.

·     When a route to a destination address already
exists on the device, configure a static route with a lower priority to the
same destination address.

When you configure a floating static route,
the priority value of the route must be larger than then priority value of the
primary route. For more information, see "[Configuring a static route](#_Ref324758868)."

## Deleting static routes

#### About this task

To delete a static route, use the undo ip route-static command. To delete all static routes including the default route,
use the delete static-routes all command.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Delete all static routes.

Public network:

delete static-routes all

VPN:

delete vpn-instance vpn-instance-name static-routes all

 

| CAUTION | CAUTION:  Deleting all static routes with caution. This operation might cause network connectivity failure and packet forwarding failure. |
| --- | --- |

‌

## Configuring BFD for static routes

| IMPORTANT | IMPORTANT:  Enabling BFD for a flapping route could worsen the situation. |
| --- | --- |

‌

### About BFD

BFD provides a general-purpose, standard,
medium-, and protocol-independent fast failure detection mechanism. It can
uniformly and quickly detect the failures of the bidirectional forwarding paths
between two routers for protocols, such as routing protocols and MPLS. 

For more information about BFD, see High Availability Configuration Guide.

### Configuring BFD control packet mode

#### About this task

This mode uses BFD control packets to
detect the status of a link bidirectionally at a millisecond level. 

BFD control packet mode can be applied to
static routes with a direct next hop or with an indirect next hop.

#### Restrictions and guidelines for BFD control packet mode

If you use BFD control packet mode at the
local end, you must use this mode also at the peer end.

#### Configuring BFD control packet mode for a static route (direct next hop)

**1\.**Enter system view.

system-view

**2\.**Configure BFD control packet mode for a
static route.

Public network:

ip route-static dest-address { mask-length \| mask } interface-type interface-number next-hop-address bfd control-packet \[ preference preference ] \[ tag tag-value ] \[ description text ]

VPN:

ip route-static vpn-instance s-vpn-instance-name
dest-address { mask-length \| mask } interface-type
interface-number next-hop-address bfd control-packet \[ preference preference ] \[ tag tag-value ] \[ description text ]

By default, BFD control packet mode for a
static route is not configured.

#### Configuring BFD control packet mode for a static route (indirect next hop)

**1\.**Enter system view.

system-view

**2\.**Configure BFD control packet mode for a
static route.

Public network:

ip route-static dest-address { mask-length \| mask } { next-hop-address bfd control-packet bfd-source ip-address \| vpn-instance d-vpn-instance-name next-hop-address bfd control-packet bfd-source ip-address } \[ preference preference ] \[ tag tag-value ] \[ description text ]

VPN:

ip route-static vpn-instance s-vpn-instance-name
dest-address { mask-length \| mask } { next-hop-address bfd control-packet bfd-source ip-address \| vpn-instance
d-vpn-instance-name next-hop-address bfd control-packet bfd-source ip-address } \[ preference preference ] \[ tag tag-value ] \[ description text ]

By default, BFD control packet mode for a
static route is not configured.

### Configuring BFD echo packet mode

#### About this task

With BFD echo packet mode enabled for a
static route, the output interface sends BFD echo packets to the destination device,
which loops the packets back to test the link reachability.

#### Restrictions and guidelines

You do not need to configure BFD echo
packet mode at the peer end.

Do not use BFD for a static route with the output
interface in spoofing state.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Configure the source address of echo
packets.

bfd echo-source-ip ip-address

By default, the source address of echo
packets is not configured.

For more information about this command,
see High Availability Command Reference.

**3\.**Configure BFD echo packet mode for a static
route.

Public network:

ip route-static dest-address { mask-length \| mask } interface-type interface-number next-hop-address bfd echo-packet \[ preference preference ] \[ tag tag-value ] \[ description text ]

VPN:

ip route-static vpn-instance s-vpn-instance-name dest-address { mask-length \| mask } interface-type interface-number next-hop-address bfd echo-packet \[ preference preference ] \[ tag tag-value ] \[ description text ]

By default, BFD echo packet mode for a
static route is not configured.

## Configuring static route FRR

### About static route FRR

A link or router failure on a path can
cause packet loss. Static route fast reroute (FRR) enables fast rerouting to
minimize the impact of link or node failures.

Figure 1 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11704735_x_Img_x_png_2_2216016_294551_0.png)

 

As shown in [Figure 1](#_Ref310863702),
upon a link failure, packets are directed to the backup next hop to avoid
traffic interruption. You can either specify a backup next hop for FRR or
enable FRR to automatically select a backup next hop (which must be configured
in advance). 

### Restrictions and guidelines for static route FRR

·     Do not use static route FRR and BFD (for a
static route) at the same time.

·     Equal-cost routes do not support static route FRR.

·     Besides the configured static route for FRR, the
device must have another route to reach the destination.

When the state of the primary link (with
Layer 3 interfaces staying up) changes from bidirectional to unidirectional or
down, static route FRR quickly redirects traffic to the backup next hop. When
the Layer 3 interfaces of the primary link are down, static route FRR temporarily
redirects traffic to the backup next hop. In addition, the device searches for
another route to reach the destination and redirects traffic to the new path if
a route is found. If no route is found, traffic interruption occurs.

### Configuring static route FRR by specifying a backup next hop

#### Restrictions and guidelines

A static route does not take effect when
the backup output interface is unavailable.

To change the backup output interface or
next hop, you must first remove the current setting. The backup output
interface and next hop must be different from the primary output interface and
next hop.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Configure static route FRR.

Public network:

ip route-static dest-address { mask-length \| mask } interface-type interface-number \[ next-hop-address \[ backup-interface interface-type interface-number \[ backup-nexthop backup-nexthop-address ] ] ] \[ permanent ] \[ preference preference ] \[ tag tag-value ] \[ description text ]

VPN:

ip route-static vpn-instance s-vpn-instance-name dest-address { mask-length \| mask } interface-type interface-number
\[ next-hop-address \[ backup-interface interface-type interface-number \[ backup-nexthop backup-nexthop-address ] ] ] \[ permanent ] \[ preference preference ] \[ tag tag-value ] \[ description text ]

By default, static route FRR is disabled.

### Configuring static route FRR to automatically select a backup next hop

**1\.**Enter system view.

system-view

**2\.**Configure static route FRR to automatically
select a backup next hop.

ip route-static fast-reroute auto

By default, static route FRR is disabled
from automatically selecting a backup next hop.

### Enabling BFD echo packet mode for static route FRR

#### About this task

By default, static route FRR uses ARP to
detect primary link failures. Perform this task to enable static route FRR to
use BFD echo packet mode for fast failure detection on the primary link.

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

**3\.**Enable BFD echo packet mode for static route
FRR.

ip route-static primary-path-detect bfd echo

By default, BFD echo packet mode for
static route FRR is disabled.

## Display and maintenance commands for static routing

Execute display
commands in any view.

 

| Task | Command |
| --- | --- |
| Display static route information. | display ip routing-table protocol static \[ inactive \| verbose ] || Display static route next hop information. | display route-static nib \[ nib-id ] \[ verbose ] || Display static routing table information. | display route-static routing-table \[ vpn-instance vpn-instance-name ] \[ ip-address { mask-length \| mask } ] |



‌

## Static route configuration examples

### Example: Configuring basic static routes

#### Network configuration

As shown in [Figure 2](#_Ref296884098), configure
static routes on the switches for interconnections between any two hosts. 

Figure 2 Network
diagram

![](https://resource.h3c.com/en/202407/12/20240712_11704736_x_Img_x_png_3_2216016_294551_0.png)

‌

#### Procedure

**1\.**Configure IP addresses for interfaces. (Details
not shown.)

**2\.**Configure static routes:

\# Configure a default route on Switch A.

\<SwitchA\> system-view

\[SwitchA] ip route-static
0.0.0.0 0.0.0.0 1.1.4.2

\# Configure two static routes on Switch B.

\<SwitchB\> system-view

\[SwitchB] ip route-static 1.1.2.0
255.255.255.0 1.1.4.1

\[SwitchB] ip route-static 1.1.3.0
255.255.255.0 1.1.5.6

\# Configure a default route on Switch C.

\<SwitchC\> system-view

\[SwitchC] ip route-static
0.0.0.0 0.0.0.0 1.1.5.5

**3\.**Configure the default gateways of Host A, Host B, and Host C as
1.1.2.3, 1.1.6.1, and 1.1.3.1. (Details not shown.)

#### Verifying the configuration

\# Display static routes on Switch A.

\[SwitchA] display ip routing-table
protocol static

 

Summary count : 1

 

Static Routing table status :
\<Active\>

Summary count : 1

 

Destination/Mask    Proto  Pre 
Cost         NextHop         Interface

0.0.0.0/0           Static 60  
0            1.1.4.2         Vlan500

 

Static Routing table status :
\<Inactive\>

Summary count : 0

\# Display static routes on Switch B.

\[SwitchB] display ip routing-table
protocol static

 

Summary count : 2

 

Static Routing table status :
\<Active\>

Summary count : 2

 

Destination/Mask    Proto  Pre 
Cost         NextHop         Interface

1.1.2.0/24          Static 60  
0            1.1.4.1         Vlan500

 

Static Routing table status :
\<Inactive\>

Summary count : 0

\# Use the ping command on Host B to test the reachability of Host A (Windows XP
runs on the two hosts).

C:\\Documents and
Settings\\Administrator\>ping 1.1.2.2

 

Pinging 1.1.2.2 with 32 bytes of
data:

 

Reply from 1.1.2.2: bytes\=32 time\=1ms
TTL\=126

Reply from 1.1.2.2: bytes\=32 time\=1ms
TTL\=126

Reply from 1.1.2.2: bytes\=32 time\=1ms
TTL\=126

Reply from 1.1.2.2: bytes\=32 time\=1ms
TTL\=126

 

Ping statistics for 1.1.2.2:

    Packets: Sent \= 4, Received \= 4,
Lost \= 0 (0% loss),

Approximate round trip times in
milli-seconds:

    Minimum \= 1ms, Maximum \= 1ms,
Average \= 1ms

\# Use the tracert command on Host B to test the reachability of Host A.

C:\\Documents and
Settings\\Administrator\>tracert 1.1.2.2

 

Tracing route to 1.1.2.2 over a
maximum of 30 hops

 

  1    \<1 ms    \<1 ms    \<1
ms  1.1.6.1

  2    \<1 ms    \<1 ms    \<1
ms  1.1.4.1

  3     1 ms    \<1 ms    \<1 ms 
1.1.2.2

 

Trace complete.

### Example: Configuring BFD for static routes (direct next hop)

#### Network configuration

Configure the following, as shown in [Figure 3](#_Ref311450821):

·     Configure a static route to subnet 120.1.1.0/24 on
Switch A.

·     Configure a static route to subnet 121.1.1.0/24
on Switch B.

·     Enable BFD for both routes.

·     Configure a static route to subnet 120.1.1.0/24
and a static route to subnet 121.1.1.0/24 on Switch C.

When the link between Switch A and Switch B
through the Layer 2 switch fails, BFD can detect the failure immediately. Switch
A then communicates with Switch B through Switch C.

Figure 3 Networkdiagram

![](https://resource.h3c.com/en/202407/12/20240712_11704737_x_Img_x_png_4_2216016_294551_0.png)

‌

Table 1 Interface and IP address assignment

| Device | Interface | IP address |
| --- | --- | --- |
| Switch A | VLAN-interface 10 | 12.1.1.1/24 || Switch A | VLAN-interface 11 | 10.1.1.102/24 || Switch B | VLAN-interface 10 | 12.1.1.2/24 || Switch B | VLAN-interface 13 | 13.1.1.1/24 || Switch C | VLAN-interface 11 | 10.1.1.100/24 || Switch C | VLAN-interface 13 | 13.1.1.2/24 |






‌

#### Procedure

**1\.**Configure IP addresses for the interfaces. (Details
not shown.) 

**2\.**Configure static routes and BFD:

\# Configure static routes on Switch A and
enable BFD control packet mode for the static route that traverses the Layer 2
switch.

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

\[SwitchA] ip route-static 120.1.1.0
24 vlan-interface 10 12.1.1.2 bfd control-packet

\[SwitchA] ip route-static
120.1.1.0 24 vlan-interface 11 10.1.1.100 preference 65

\[SwitchA] quit

\# Configure static routes on Switch B and
enable BFD control packet mode for the static route that traverses the Layer 2
switch.

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

\[SwitchB] ip route-static
121.1.1.0 24 vlan-interface 10 12.1.1.1 bfd control-packet

\[SwitchB] ip route-static
121.1.1.0 24 vlan-interface 13 13.1.1.2 preference 65

\[SwitchB] quit

\# Configure static routes on Switch C.

\<SwitchC\> system-view

\[SwitchC] ip route-static
120.1.1.0 24 13.1.1.1

\[SwitchC] ip route-static
121.1.1.0 24 10.1.1.102

#### Verifying the configuration

\# Display BFD sessions on Switch A.

\<SwitchA\> display bfd session

 Total sessions: 1        Up
sessions: 1        Init mode: Active

 

 IPv4 session working in control
packet mode:

 

LD/RD           SourceAddr     
DestAddr        State   Holdtime    Interface

 4/7             12.1.1.1       
12.1.1.2        Up      2000ms      Vlan10

The output shows that the BFD session has
been created.

\# Display the static routes on Switch A.

\<SwitchA\> display ip
routing-table protocol static

 

Summary count : 1

 

Static Routing table status :
\<Active\>

Summary count : 1

 

Destination/Mask    Proto  Pre 
Cost         NextHop         Interface

120.1.1.0/24        Static 60   0            12.1.1.2        Vlan10

 

Static Routing table status :
\<Inactive\>

Summary count : 0

The output shows that Switch A communicates
with Switch B through VLAN-interface 10\. Then the link over VLAN-interface 10
fails.

\# Display static routes on Switch A. 

\<SwitchA\> display ip
routing-table protocol static

 

Summary count : 1

 

Static Routing table status :
\<Active\>

Summary count : 1

 

Destination/Mask    Proto  Pre 
Cost         NextHop         Interface

120.1.1.0/24        Static 65   0            10.1.1.100      Vlan11

 

Static Routing table status :
\<Inactive\>

Summary count : 0

The output shows that Switch A communicates
with Switch B through VLAN-interface 11\.

### Example: Configuring BFD for static routes (indirect next hop)

#### Network configuration

[Figure 4](#_Ref311450851) shows the network topology as follows:

·     Switch A has a route to interface Loopback 1 (2.2.2.9/32)
on Switch B, with the output interface VLAN-interface 10\.

·     Switch B has a route to interface Loopback 1 (1.1.1.9/32)
on Switch A, with the output interface VLAN-interface 12\.

·     Switch D has a route to 1.1.1.9/32, with the output
interface VLAN-interface 10, and a route to 2.2.2.9/32, with the output
interface VLAN-interface 12\. 

Configure the following:

·     Configure a static route to subnet 120.1.1.0/24 on
Switch A.

·     Configure a static route to subnet 121.1.1.0/24
on Switch B.

·     Enable BFD for both routes.

·     Configure a static route to subnet 120.1.1.0/24
and a static route to subnet 121.1.1.0/24 on both Switch C and Switch D.

When the link between Switch A and Switch B
through Switch D fails, BFD can detect the failure immediately. Switch A then
communicates with Switch B through Switch C.

Figure 4 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11704738_x_Img_x_png_5_2216016_294551_0.png)

‌

Table 2 Interface and IP address assignment

| Device | Interface | IP address |
| --- | --- | --- |
| Switch A | VLAN-interface 10 | 12.1.1.1/24 || Switch A | VLAN-interface 11 | 10.1.1.102/24 || Switch A | Loopback 1 | 1.1.1.9/32 || Switch B | VLAN-interface 12 | 11.1.1.1/24 || Switch B | VLAN-interface 13 | 13.1.1.1/24 || Switch B | Loopback 1 | 2.2.2.9/32 || Switch C | VLAN-interface 11 | 10.1.1.100/24 || Switch C | VLAN-interface 13 | 13.1.1.2/24 || Switch D | VLAN-interface 10 | 12.1.1.2/24 || Switch D | VLAN-interface 12 | 11.1.1.2/24 |










‌

#### Procedure

**1\.**Configure IP addresses for interfaces. (Details
not shown.)

**2\.**Configure static routes and BFD:

\# Configure static routes on Switch A and
enable BFD control packet mode for the static route that traverses Switch D.

\<SwitchA\> system-view

\[SwitchA] bfd multi-hop min-transmit-interval
500

\[SwitchA] bfd multi-hop min-receive-interval
500

\[SwitchA] bfd multi-hop detect-multiplier
9

\[SwitchA] ip route-static
120.1.1.0 24 2.2.2.9 bfd control-packet bfd-source 1.1.1.9

\[SwitchA] ip route-static
120.1.1.0 24 vlan-interface 11 10.1.1.100 preference 65

\[SwitchA] quit

\# Configure static routes on Switch B and
enable BFD control packet mode for the static route that traverses Switch D.

\<SwitchB\> system-view

\[SwitchB] bfd multi-hop min-transmit-interval
500

\[SwitchB] bfd multi-hop min-receive-interval
500

\[SwitchB] bfd multi-hop detect-multiplier
9

\[SwitchB] ip route-static
121.1.1.0 24 1.1.1.9 bfd control-packet bfd-source 2.2.2.9

\[SwitchB] ip route-static
121.1.1.0 24 vlan-interface 13 13.1.1.2 preference 65

\[SwitchB] quit

\# Configure static routes on Switch C.

\<SwitchC\> system-view

\[SwitchC] ip route-static
120.1.1.0 24 13.1.1.1

\[SwitchC] ip route-static
121.1.1.0 24 10.1.1.102

\# Configure static routes on Switch D.

\<SwitchD\> system-view

\[SwitchD] ip route-static
120.1.1.0 24 11.1.1.1

\[SwitchD] ip route-static
121.1.1.0 24 12.1.1.1

#### Verifying the configuration

\# Display BFD sessions on Switch A.

\<SwitchA\> display bfd session

 Total sessions: 1        Up
sessions: 1        Init mode: Active

 

 IPv4 session working in control
packet mode:

 

LD/RD           SourceAddr     
DestAddr        State   Holdtime    Interface

 4/7             1.1.1.9        
2.2.2.9         Up      2000ms      N/A

The output shows that the BFD session has
been created.

\# Display the static routes on Switch A.

\<SwitchA\> display ip routing-table
protocol static

 

Summary count : 1

 

Static Routing table status :
\<Active\>

Summary count : 1

 

Destination/Mask    Proto  Pre 
Cost         NextHop         Interface

120.1.1.0/24        Static 60   0            12.1.1.2        Vlan10

 

Static Routing table status :
\<Inactive\>

Summary count : 0

The output shows that Switch A communicates
with Switch B through VLAN-interface 10\. Then the link over VLAN-interface 10
fails.

\# Display static routes on Switch A. 

\<SwitchA\> display ip routing-table
protocol static

 

Summary count : 1

 

Static Routing table status :
\<Active\>

Summary count : 1

 

Destination/Mask    Proto  Pre 
Cost         NextHop         Interface

120.1.1.0/24        Static 65   0            10.1.1.100      Vlan11

 

Static Routing table status :
\<Inactive\>

Summary count : 0

The output shows that Switch A communicates
with Switch B through VLAN-interface 11\.

### Example: Configuring static route FRR

#### Network configuration

As shown in [Figure 5](#_Ref311450875), configure
static routes on Switch A, Switch B, and Switch C, and configure static route
FRR. When Link A becomes unidirectional, traffic can be switched to Link B
immediately.

Figure 5 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11704739_x_Img_x_png_6_2216016_294551_0.png)

‌

Table 3 Interface and IP address assignment

| Device | Interface | IP address |
| --- | --- | --- |
| Switch A | VLAN-interface 100 | 12.12.12.1/24 || Switch A | VLAN-interface 200 | 13.13.13.1/24 || Switch A | Loopback 0 | 1.1.1.1/32 || Switch B | VLAN-interface 101 | 24.24.24.4/24 || Switch B | VLAN-interface 200 | 13.13.13.2/24 || Switch B | Loopback 0 | 4.4.4.4/32 || Switch C | VLAN-interface 100 | 12.12.12.2/24 || Switch C | VLAN-interface 101 | 24.24.24.2/24 |








‌

#### Procedure

**1\.**Configure IP addresses for interfaces. (Details
not shown.)

**2\.**Configure static route FRR on link A:

Use one of the following
methods:

¡     (Method
1.) Specify a backup next hop for static route FRR:

\# Configure a static route on Switch A,
and specify VLAN-interface 100 as the backup output interface and 12.12.12.2 as
the backup next hop.

\<SwitchA\> system-view

\[SwitchA] ip route-static
4.4.4.4 32 vlan-interface 200 13.13.13.2 backup-interface vlan-interface 100
backup-nexthop 12.12.12.2

\# Configure a static route on Switch B,
and specify VLAN-interface 101 as the backup output interface and 24.24.24.2 as
the backup next hop. 

\<SwitchB\> system-view

\[SwitchB] ip route-static
1.1.1.1 32 vlan-interface 200 13.13.13.1 backup-interface vlan-interface 101
backup-nexthop 24.24.24.2

¡     (Method
2.) Configure static route FRR to automatically select a backup next hop:

\# Configure static routes on Switch A,
and enable static route FRR.

\<SwitchA\> system-view

\[SwitchA] ip route-static
4.4.4.4 32 vlan-interface 200 13.13.13.2

\[SwitchA] ip route-static
4.4.4.4 32 vlan-interface 100 12.12.12.2 preference 70

\[SwitchA] ip route-static fast-reroute
auto

\# Configure static routes on Switch B,
and enable static route FRR.

\<SwitchB\> system-view

\[SwitchB] ip route-static
1.1.1.1 32 vlan-interface 200 13.13.13.1

\[SwitchB] ip route-static
1.1.1.1 32 vlan-interface 101 24.24.24.2 preference 70

\[SwitchB] ip route-static fast-reroute
auto

**3\.**Configure static routes on Switch C.

\<SwitchC\> system-view

\[SwitchC] ip route-static
4.4.4.4 32 vlan-interface 101 24.24.24.4

\[SwitchC] ip route-static
1.1.1.1 32 vlan-interface 100 12.12.12.1

#### Verifying the configuration

\# Display route 4.4.4.4/32 on Switch A to
view the backup next hop information. 

\[SwitchA] display ip routing-table
4.4.4.4 verbose

 

Summary count : 1

 

Destination: 4.4.4.4/32

   Protocol: Static          

 Process ID: 0

  SubProtID: 0x0                    Age:
04h20m37s

       Cost: 0               Preference:
60

      IpPre: N/A            
QosLocalID: N/A

        Tag: 0                    State:
Active Adv

  OrigTblID: 0x0                OrigVrf:
default-vrf

    TableID: 0x2                 OrigAs:
0

      NibID: 0x26000002          LastAs:
0

     AttrID: 0xffffffff        Neighbor:
0.0.0.0

      Flags: 0x1008c        OrigNextHop:
13.13.13.2

      Label: NULL           RealNextHop: 13.13.13.2

    BkLabel: NULL             BkNextHop: 12.12.12.2

    SRLabel: NULL            
BkSRLabel: NULL

   SIDIndex: NULL              
InLabel: NULL

  Tunnel ID: Invalid          Interface: Vlan-interface200

BkTunnel ID: Invalid        BkInterface: Vlan-interface100

   FtnIndex: 0x0          
TrafficIndex: N/A

  Connector: N/A                
PathID: 0x0

   LinkCost: 0              
MicroSegID: 0

RealFIRType: Normal          
RealThres: 0

\# Display route 1.1.1.1/32 on Switch B to
view the backup next hop information.

\[SwitchB] display ip routing-table 1.1.1.1
verbose

 

Summary count : 1

 

Destination: 1.1.1.1/32

   Protocol: Static          

 Process ID: 0

  SubProtID: 0x0                   
Age: 04h20m37s

       Cost: 0              
Preference: 60

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

      Flags: 0x1008c        OrigNextHop:
13.13.13.1

      Label: NULL           RealNextHop: 13.13.13.1

    BkLabel: NULL             BkNextHop: 24.24.24.2

    SRLabel: NULL            
BkSRLabel: NULL

   SIDIndex: NULL              
InLabel: NULL

  Tunnel ID: Invalid          Interface: Vlan-interface200

BkTunnel ID: Invalid        BkInterface: Vlan-interface101

   FtnIndex: 0x0           TrafficIndex:
N/A

  Connector: N/A                
PathID: 0x0

   LinkCost: 0              
MicroSegID: 0

RealFIRType: Normal          
RealThres: 0

