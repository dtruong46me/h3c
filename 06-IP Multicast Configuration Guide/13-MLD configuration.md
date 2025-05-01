
# Configuring MLD

## About MLD

Multicast Listener Discovery (MLD) establishes
and maintains IPv6 multicast group memberships between a Layer 3 multicast
device and the hosts on the directly connected subnet.

### MLD versions

MLD has the following versions:

·     MLDv1 (defined by RFC 2710), which is derived
from IGMPv2.

·     MLDv2 (defined by RFC 3810), which is derived
from IGMPv3.

MLDv1 and MLDv2 support the ASM model.
MLDv2 can directly implement the SSM model, but MLDv1 must work with the MLD
SSM mapping feature to implement the SSM model. For more information about the
ASM and SSM models, see "Multicast overview."

### How MLDv1 works

MLDv1 implements IPv6 multicast listener
management based on the query and response mechanism.

#### Electing the MLD querier

All IPv6 multicast devices that run MLD on
the same subnet can monitor MLD listener report messages (often called reports)
from hosts. However, only one device can act as the MLD querier to send MLD
query messages (often called queries). A querier election mechanism determines
which device acts as the MLD querier on the subnet.

**1\.**Initially, every MLD device assumes itself
as the querier. Each device sends MLD general query messages (often called
general queries) to all hosts and devices on the local subnet. The destination
address of the general queries is FF02::1.

**2\.**After receiving a general query, every MLD device
compares the source IPv6 address of the query with its own link-local interface
address. The device with the lowest IPv6 address wins the querier election and becomes
the querier. All the other devices become non-queriers.

**3\.**All the non-queriers start a timer called the
"other querier present timer." If a device receives an MLD query from
the querier before the timer expires, it resets this timer. Otherwise, it considers
that the querier has timed out. In this case, the device initiates a new
querier election process. 

#### Joining an IPv6 multicast group

Figure 1 MLD queries and reports

![](https://resource.h3c.com/en/202407/12/20240712_11705035_x_Img_x_png_0_2216042_294551_0.png)

 

As shown in [Figure 1](#_Ref198628498), Host
B and Host C want to receive the IPv6 multicast data addressed to IPv6
multicast group G1. Host A wants to receive the IPv6 multicast data addressed
to G2. The following process describes how the hosts join the IPv6 multicast
groups and how the MLD querier (Device B in [Figure 1](#_Ref198628498))
maintains the IPv6 multicast group memberships:

**1\.**The hosts send unsolicited MLD reports to
the IPv6 multicast groups they want to join without having to wait for the MLD
queries.

**2\.**The MLD querier periodically multicasts MLD
queries (with the destination address FF02::1) to all hosts and devices on the
local subnet.

**3\.**After receiving a query, the host whose
report delay timer expires first sends an MLD report to the IPv6 multicast
group G1 to announce its membership for G1. In this example, Host B sends the
report. After hearing the report from Host B, Host C, which is on the same
subnet as Host B, suppresses its own report for G1.

Because the MLD devices already know that
G1 has a minimum of one member, other members do not need to report their
memberships. This mechanism, known as the host MLD report suppression, helps
reduce traffic on the local subnet.

**4\.**At the same time, because Host A is
interested in G2, it sends a report to the IPv6 multicast group G2.

**5\.**Through the query/report process, the MLD devices
determine that G1 and G2 have members on the local subnet. The IPv6 multicast
routing protocol (for example, IPv6 PIM) that is running on the devices
generates (\*, G1) and (\*, G2) multicast forwarding entries. These entries are the
basis for subsequent IPv6 multicast forwarding. The asterisk (\*) represents any
IPv6 multicast source.

**6\.**When the IPv6 multicast data addressed to G1
or G2 reaches an MLD device, the device looks up the IPv6 multicast forwarding
table. Based on the (\*, G1) and (\*, G2) entries, the device forwards the IPv6
multicast data to the local subnet. Then, the receivers on the subnet receive
the data.

#### Leaving an IPv6 multicast group

When a host is leaving an IPv6 multicast
group, the following process occurs:

**1\.**The host sends an MLD done message to all
IPv6 multicast devices on the local subnet. The destination address of done
messages is FF02::2.

**2\.**After receiving the MLD done message, the
querier sends a configurable number of multicast-address-specific queries to
the group that the host is leaving. The IPv6 multicast addresses queried
include both the destination address field and the group address field of the message.

**3\.**One of the remaining members (if any on the
subnet) in the group sends a report within the time of the maximum response
time advertised in the multicast-address-specific queries.

**4\.**If the querier receives a report for the
group within the maximum response time, it maintains the memberships of the
IPv6 multicast group. Otherwise, the querier assumes that no hosts on the
subnet are interested in IPv6 multicast traffic addressed to that group and
stops maintaining the memberships of the group.

### MLDv2 enhancements

MLDv2 is based on and backwards-compatible
with MLDv1. MLDv2 provides hosts with enhanced control capabilities and
enhances the MLD state.

#### Enhancements in control capability of hosts

MLDv2 has introduced IPv6 multicast source
filtering modes (Include and Exclude). These modes allow a host to receive or
reject multicast data from the specified IPv6 multicast sources. When a host
joins an IPv6 multicast group, one of the following occurs:

·     If the host expects IPv6 multicast data from
specific IPv6 multicast sources like S1, S2, …, it sends a report with
Filter-Mode denoted as "Include Sources (S1, S2, …)."

·     If the host does not expect IPv6 multicast data
from specific IPv6 multicast sources like S1, S2, …, it sends a report with
Filter-Mode denoted as "Exclude Sources (S1, S2, …)."

As shown in [Figure 2](#_Ref90440017), the
network has two IPv6 multicast sources, Source 1 (S1) and Source 2 (S2). Both
of the sources can send IPv6 multicast data to IPv6 multicast group G. Host B wants
to receive IPv6 multicast data addressed to G from Source 1 but not from Source
2\.

Figure 2 Flow
paths of multicast-address-and-source-specific multicast traffic

![](https://resource.h3c.com/en/202407/12/20240712_11705036_x_Img_x_png_1_2216042_294551_0.png)

 

In MLDv1, Host B cannot select IPv6
multicast sources when it joins IPv6 multicast group G. The IPv6 multicast
streams from both Source 1 and Source 2 flow to Host B whether it needs them or
not.

In MLDv2, Host B can explicitly express its
interest in IPv6 multicast data destined to G from Source 1 but not from Source
2\. Then, Host B receives only IPv6 multicast data from Source 1\.

#### Enhancement in MLD state

A multicast device that is running MLDv2
maintains the multicast address state for each multicast address on each attached
subnet. The multicast address state consists of the following information:

·     Filter mode—Device keeps tracing the Include or Exclude state.

·     List of sources—Device keeps tracing the newly added or deleted IPv6 multicast
source.

·     Timers—Filter timers, which include the time that the device waits before
switching to the Include mode after an IPv6 multicast address times out, and
source timers for source recording.

### MLD SSM mapping

An MLDv2 host can explicitly specify
multicast sources in its MLDv2 reports. From the reports, the MLD device can
obtain the multicast source addresses and directly provide the SSM service.
However, an MLDv1 host cannot specify multicast sources in its MLDv1 reports.

The MLD SSM mapping feature enables the MLD
device to provide SSM support for MLDv1 receiver host. The device translates
(\*, G) in MLDv1 reports into (G, INCLUDE, (S1, S2...)) based on the configured
MLD SSM mappings.

Figure 3 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705037_x_Img_x_png_2_2216042_294551_0.png)

 

As shown in [Figure 3](#_Ref326594130), Host
A and Host B on the IPv6 SSM network run MLDv1, and Host C runs MLDv2. To
provide the SSM service for Host A and Host B, you must configure the MLD SSM
mapping feature on Device A.

After MLD SSM mappings are configured, Device
A checks the IPv6 multicast group address G carried in the message, and performs
the following operations:

·     If G is not in the IPv6 SSM group range, Device
A provides the ASM service.

·     If G is in the IPv6 SSM group range but does not
match any MLD SSM mapping, Device A drops the report.

·     If G is in the IPv6 SSM group range and matches MLD
SSM mappings, Device A translates (\*, G) in the report to (G, INCLUDE, (S1,
S2...)) to provide SSM services.

 

|  | NOTE:  The MLD SSM mapping feature does not process MLDv2 reports. |
| --- | --- |

 

For more information about the IPv6 SSM
group ranges, see "IPv6 PIM overview" and "Configuring IPv6
PIM."

### MLD proxying

As shown in [Figure 4](#_Ref369504933), in a
simple tree-shaped topology, it is not necessary to configure IPv6 multicast
routing protocols, such as IPv6 PIM, on edge devices. Instead, you can
configure MLD proxying on these devices. With MLD proxying configured, the edge
device acts as an MLD proxy:

·     For the upstream MLD querier, the MLD proxy
device acts as a host.

·     For the downstream receiver hosts, the MLD proxy
device acts as an MLD querier.

Figure 4 Network diagram

 ![](https://resource.h3c.com/en/202407/12/20240712_11705038_x_Img_x_png_3_2216042_294551_0.png)

 

The following interfaces are defined in MLD
proxying:

·     Host interface—An interface that is in the direction toward the root of the multicast
forwarding tree. A host interface acts as a receiver host that is running MLD. MLD
proxying must be enabled on this interface. This interface is also called the "proxy
interface."

·     Router interface—An interface that is in the
direction toward the leaf of the multicast forwarding tree. A router interface
acts as a router that is running MLD. MLD must be configured on this interface.

An MLD proxy device maintains a group
membership database, which stores the group memberships on all the router
interfaces. The host interfaces and router interfaces perform actions based on
this membership database.

·     The host interfaces respond to queries according
to the membership database or sends join/done messages when the database
changes. 

·     The router interfaces participate in the querier
election, send queries, and maintain memberships based on received MLD reports.

### Protocols and standards

·     RFC 2710, Multicast
Listener Discovery (MLD) for IPv6

·     RFC 3810, Multicast
Listener Discovery Version 2 (MLDv2) for IPv6

## MLD tasks at a glance

To configure MLD, perform the following
tasks:

**1\.**[Enabling MLD](#_Ref160941876)

**2\.**(Optional.) [Configuring basic MLD features](#_Ref476245210)

¡     [Specifying an MLD version](#_Ref160941878)

¡     [Configuring a static group member](#_Ref393108221)

¡     [Configuring an IPv6 multicast group policy](#_Ref392862896)

**3\.**(Optional.) [Adjusting MLD performance](#_Ref198628350)

¡     [Configuring MLD query and response
parameters](#_Ref375209772)

¡     [Enabling fast-leave processing](#_Ref160941964)

**4\.**(Optional.) [Configuring MLD SSM mappings](#_Ref372040058)

**5\.**(Optional.) [Configuring MLD proxying](#_Ref369522413)

¡     [Enabling MLD proxying](#_Ref206816046)

¡     [Enabling IPv6 multicast forwarding on a non-querier
interface](#_Ref206821523)

¡     [Enabling IPv6 multicast load splitting for
MLD proxy interfaces](#_Ref476245845)

**6\.**(Optional.) [Enabling MLD NSR](#_Ref476245337)

**7\.**(Optional.) [Setting the DSCP value for outgoing MLD
protocol packets](#_Ref491337104)

## Prerequisites for MLD

Before you configure MLD, you must configure
an IPv6 unicast routing protocol so that all devices can interoperate at the
network layer.

## Enabling MLD

#### Restrictions and guidelines

Perform this task on interfaces where IPv6
multicast group memberships are created and maintained.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enable IPv6 multicast routing and enter IPv6
MRIB view.

ipv6 multicast routing

By default, IPv6 multicast routing is
disabled.

For more information about this command,
see IP Multicast Command Reference.

**3\.**Return to system view.

quit

**4\.**Enter interface view.

interface interface-type interface-number

**5\.**Enable MLD.

mld enable

By default, MLD is disabled.

## Configuring basic MLD features

### Specifying an MLD version

#### Restrictions and guidelines

For MLD to operate correctly, specify the
same MLD version for all devices on the same subnet.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter interface view.

interface interface-type
interface-number

**3\.**Specify an MLD version on the interface.

mld version version-number

By default, the MLD version on an
interface is MLDv1.

### Configuring a static group member

#### About this task

You can configure an interface as a static
member of an IPv6 multicast group. Then, the interface can always receive IPv6
multicast data for the group.

A static group member does not respond to
MLD queries. When you complete or cancel this configuration on an interface,
the interface does not send an unsolicited MLD report or done message. 

#### Restrictions and guidelines

The interface to be configured as a static member
of an IPv6 multicast group has the following restrictions:

·     If the interface is MLD and IPv6 PIM-SM enabled,
it must be an IPv6 PIM-SM DR.

·     If the interface is MLD enabled but not IPv6
PIM-SM enabled, it must be an MLD querier. 

For more information about IPv6 PIM-SM and
DR, see "IPv6 PIM overview."

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter interface view.

interface interface-type interface-number

**3\.**Configure a static group member.

mld static-group ipv6-group-address \[ source ipv6-source-address ]

### Configuring an IPv6 multicast group policy

#### About this task

This feature enables an interface to filter
MLD reports by using an ACL that specifies IPv6 multicast groups and the
optional sources. It is used to control the IPv6 multicast groups that the
hosts attached to an interface can join.

#### Restrictions and guidelines

This configuration does not take effect on
static group members, because static group members do not send MLD reports.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter interface view.

interface interface-type interface-number

**3\.**Configure an IPv6 multicast group policy on
the interface.

mld group-policy ipv6-acl-number
\[ version-number ]

## Adjusting MLD performance

### Configuring MLD query and response parameters

#### About this task

The following are MLD query and response
parameters:

·     MLD querier's robustness
variable—Number of times for retransmitting MLD
queries in case of packet loss. A higher robustness variable makes the MLD
querier more robust, but increases the timeout time for IPv6 multicast groups.

·     MLD startup query interval—Interval at which an MLD querier sends MLD general queries at startup.

·     MLD startup query count—Number of MLD general queries that an MLD querier sends at startup.

·     MLD general query interval—Interval at which an MLD querier sends MLD general queries to check
for IPv6 multicast group members on the network.

·     MLD last listener query
interval—In MLDv1, it sets the interval at which
a querier sends multicast-address-specific queries after receiving a done
message. In MLDv2, it sets the interval at which a querier sends
multicast-address-and-source-specific queries after receiving a report that
changes IPv6 multicast source and group mappings.

·     MLD last listener query
count—In MLDv1, it sets the number of
multicast-address-specific queries that the querier sends after receiving a
done message. In MLDv2, it sets the number of
multicast-address-and-source-specific queries that the querier sends after
receiving a report that changes IPv6 multicast group and source mappings.

·     MLD maximum response time—Maximum time before a receiver responds with a report to an MLD general
query. This per-group timer is initialized to a random value in the range of 0
to the maximum response time specified in the MLD query. When the timer value
decreases to 0, the receiver sends an MLD report to the group.

·     MLD other querier present
timer—Lifetime for an MLD querier after a
non-querier receives an MLD general query. If the non-querier does not receive
a new query when this timer expires, the non-querier considers that the querier
has failed and starts a new querier election.

#### Restrictions and guidelines

·     To avoid frequent MLD querier changes, set the MLD
other querier present timer greater than the MLD general query interval. In addition,
configure the same MLD other querier present timer for all MLD devices on the
same subnet.

·     To speed up the response to MLD queries and
avoid simultaneous timer expirations that cause MLD report traffic bursts, you
must set an appropriate maximum response time.

¡     For
MLD general queries, the maximum response time is set by the max-response-time command. 

¡     For
MLD multicast-address-specific queries or MLD
multicast-address-and-source-specific queries, the maximum response time equals
the MLD last listener query interval.

·     You can configure MLD query and response
parameters globally for all interfaces in MLD view or for an interface in
interface view. The interface-specific configuration takes priority over the
global configuration.

#### Configuring the MLD query and response parameters globally

**1\.**Enter system view.

system-view

**2\.**Enter MLD view.

mld

**3\.**Set the MLD querier's robustness variable.

robust-count count

By default, the MLD querier's robustness
variable is 2\.

**4\.**Set the MLD startup query interval.

startup-query-interval interval

By default, the MLD startup query
interval is equal to one quarter of the MLD general query interval.

**5\.**Set the MLD startup query count.

startup-query-count count

By default, the MLD startup query count is
equal to the MLD querier's robustness variable.

**6\.**Set the MLD general query interval.

query-interval interval

By default, the MLD general query
interval is 125 seconds.

**7\.**Set the MLD last listener query interval.

last-listener-query-interval interval

By default, the MLD last listener query
interval is 1 second.

**8\.**Set the MLD last listener query count.

last-listener-query-count count

By default, the MLD last listener query
count is equal to the MLD querier's robustness variable.

**9\.**Set the maximum response time for MLD
general queries.

max-response-time time

By default, the maximum response time for
MLD general queries is 10 seconds.

**10\.**Set the MLD other querier present timer.

other-querier-present-timeout time

By default, the MLD other querier present
timer is calculated by using the following formula:  

\[ MLD general query interval ] × \[ MLD robustness variable ] \+ \[ maximum
response time for MLD general queries ] / 2\.

#### Configuring the MLD query and response parameters on an interface

**1\.**Enter system view.

system-view

**2\.**Enter interface view.

interface interface-type
interface-number

**3\.**Set the MLD querier's robustness variable.

mld robust-count count

By default, the MLD querier's robustness
variable is 2\.

**4\.**Set the MLD startup query interval.

mld startup-query-interval interval

By default, the MLD startup query
interval is equal to one quarter of the MLD general query interval.

**5\.**Set the MLD startup query count.

mld startup-query-count count

By default, the MLD startup query count is
equal to the MLD querier's robustness variable.

**6\.**Set the MLD general query interval.

mld query-interval interval

By default, the MLD general query
interval is 125 seconds.

**7\.**Set the MLD last listener query interval.

mld last-listener-query-interval interval

By default, the MLD last listener query
interval is 1 second.

**8\.**Set the MLD last listener query count.

mld last-listener-query-count count

By default, the MLD last listener query
count is equal to the MLD querier's robustness variable.

**9\.**Set the maximum response time for MLD
general queries.

mld max-response-time time

By default, the maximum response time for
MLD general queries is 10 seconds.

**10\.**Set the MLD other querier present timer.

mld other-querier-present-timeout time

By default, the MLD other querier present
timer is calculated by using the following formula:  

\[ MLD general query interval ] × \[ MLD robustness variable ] \+ \[ maximum
response time for MLD general queries ] / 2\.

### Enabling fast-leave processing

#### About this task

This feature enables an MLD querier to send
leave notifications to the upstream without sending multicast-address-specific
or multicast-address-and-source-specific queries after receiving a done message.
Use this feature to reduce leave latency and to preserve the network bandwidth.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter interface view.

interface interface-type interface-number

**3\.**Enable fast-leave processing.

mld fast-leave \[ group-policy ipv6-acl-number ]

By default, fast-leave processing is disabled.

## Configuring MLD SSM mappings

#### About this task

This feature enables the device to provide
SSM services for MLDv1 hosts.

#### Restrictions and guidelines

This feature does not process MLDv2
messages. Enable MLDv2 on the receiver-side interface to ensure that MLDv2
reports from MLDv2 receiver hosts can be processed.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter MLD view.

mld

**3\.**Configure an MLD SSM mapping.

ssm-mapping ipv6-source-address
ipv6-acl-number

## Configuring MLD proxying

### Prerequisites for MLD proxying

Before you configure MLD proxying,
determine the router interfaces and host interface based on the network
topology. Then, enable MLD on the router interfaces.

### Enabling MLD proxying

#### Restrictions and guidelines

·     Enable MLD proxying on the receiver-side
interfaces.

·     On an interface enabled with MLD proxying, only
the mld
version command takes effect and other MLD
commands do not take effect.

·     If you enable both MLD proxying and an IPv6
multicast routing protocol on the same device, the IPv6 multicast routing
protocol does not take effect.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter interface view.

interface interface-type interface-number

**3\.**Enable MLD proxying.

mld proxy enable

By default, MLD proxying is disabled.

### Enabling IPv6 multicast forwarding on a non-querier interface

#### About this task

Typically, only MLD queriers can forward IPv6
multicast traffic and non-queriers cannot. This prevents IPv6 multicast data
from being repeatedly forwarded. If a router interface on the MLD proxy failed
the querier election, enable IPv6 multicast forwarding on the interface to
forward IPv6 multicast data to downstream receivers.

#### Restrictions and guidelines

A shared-media network might have multiple
MLD proxies, including one proxy acting as a querier. To avoid duplicate IPv6 multicast
traffic, do not enable IPv6 multicast forwarding on any of the non-querier MLD
proxies for the network. 

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter interface view.

interface interface-type
interface-number

**3\.**Enable IPv6 multicast forwarding on a
non-querier interface.

mld proxy forwarding

By default, IPv6 multicast forwarding is
disabled on a non-querier interface.

### Enabling IPv6 multicast load splitting for MLD proxy interfaces

#### About this task

If multiple MLD proxy interfaces exist on
the device, only the proxy interface with the highest IP address forwards IPv6
multicast traffic. You can enable IPv6 multicast load splitting on the device
so that all the proxy interfaces can share IPv6 multicast traffic.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter MLD view.

mld

**3\.**Enable IPv6 multicast load splitting for MLD
proxy interfaces.

proxy multipath

By default, IPv6 multicast load splitting
is disabled for MLD proxy interfaces.

## Enabling MLD NSR

#### About this task

This feature backs up information about MLD
interfaces and MLD multicast groups to the standby process. The device recovers
the information without cooperation of other devices when an active/standby switchover
occurs. Use this feature to prevent an active/standby switchover from affecting
the IPv6 multicast service. 

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enable MLD NSR.

mld non-stop-routing

By default, MLD NSR is disabled.

## Setting the DSCP value for outgoing MLD protocol packets

#### About this task

The DSCP value determines the packet transmission
priority. A greater DSCP value represents a higher priority.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter MLD view.

mld

**3\.**Set the DSCP value for outgoing MLD protocol
packets.

dscp dscp-value

By default, the DSCP value is 48 for
outgoing MLD protocol packets.

## Display and maintenance commands for MLD

| CAUTION | CAUTION:  The reset mld group command might cause IPv6 multicast data transmission failures. |
| --- | --- |

 

Execute display
commands in any view and reset commands in user
view.

 

| Task | Command |
| --- | --- |
| Display information about MLD multicast groups. | display mld group \[ ipv6-group-address \| interface interface-type interface-number ] \[ static \| verbose ] || Display MLD information for interfaces. | display mld interface \[ interface-type interface-number ] \[ proxy ] \[ verbose ] || Display IPv6 multicast routing entries maintained by the MLD proxy. | display mld proxy group \[ ipv6-group-address \| interface interface-type interface-number ] \[ verbose ] || Display information about the MLD proxy routing table. | display mld proxy routing-table \[ ipv6-source-address \[ prefix-length ] \| ipv6-group-address \[ prefix-length ] ] \* \[ verbose ] || Display MLD SSM mappings. | display mld ssm-mapping ipv6-group-address || Clear dynamic MLD multicast group entries. | reset mld group { all \| interface interface-type interface-number { all \| ipv6-group-address \[ prefix-length ] \[ ipv6-source-address \[ prefix-length ] ] } } |






 

## MLD configuration examples

### Example: Configuring basic MLD features

#### Network configuration

As shown in [Figure 5](#_Ref206487078):

·     OSPFv3 and IPv6 PIM-DM run on the network.

·     VOD streams are sent to receiver hosts in
multicast. Receiver hosts of different organizations form stub networks N1 and
N2. Host A and Host C are multicast receiver hosts in N1 and N2, respectively.

·     MLDv1 runs between Switch A and N1, and between
the other two switches (Switch B and Switch C) and N2. 

·     Switch A acts as the MLD querier in N1. Switch B
acts as the MLD querier in N2 because it has a lower IPv6 address.

Configure the switches to meet the following
requirements:

·     The hosts in N1 can join only IPv6 multicast
group FF1E::101.

·     The hosts in N2 can join any IPv6 multicast
groups. 

Figure 5 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705040_x_Img_x_png_5_2216042_294551_0.png)

#### Prerequisites

Assign an IPv6 address and prefix length to
each interface as shown in [Figure 5](#_Ref206487078), and configure
OSPFv3 on the switches in the IPv6 PIM-DM domain.

#### Procedure

**1\.**Enable the IPv6 multicast routing, MLD, and IPv6
PIM-DM:

\# On Switch A, enable IPv6 multicast
routing.

\<SwitchA\> system-view

\[SwitchA] ipv6 multicast
routing

\[SwitchA-mrib6] quit

\# Enable MLD on VLAN-interface 100\.

\[SwitchA] interface
vlan-interface 100

\[SwitchA-Vlan-interface100] mld
enable

\[SwitchA-Vlan-interface100]
quit

\# Enable IPv6 PIM-DM on VLAN-interface
101\.

\[SwitchA] interface vlan-interface
101

\[SwitchA-Vlan-interface101] ipv6
pim dm

\[SwitchA-Vlan-interface101] quit

\# On Switch B, enable IPv6 multicast
routing.

\<SwitchB\> system-view

\[SwitchB] ipv6 multicast
routing

\[SwitchB-mrib6] quit

\# Enable MLD on VLAN-interface 200\.

\[SwitchB] interface
vlan-interface 200

\[SwitchB-Vlan-interface200]
mld enable

\[SwitchB-Vlan-interface200]
quit

\# Enable IPv6 PIM-DM on VLAN-interface
201\.

\[SwitchB] interface vlan-interface
201

\[SwitchB-Vlan-interface201] ipv6
pim dm

\[SwitchB-Vlan-interface201] quit

\# On Switch C, enable IPv6 multicast
routing.

\<SwitchC\> system-view

\[SwitchC] ipv6 multicast
routing

\[SwitchC-mrib6] quit

\# Enable MLD on VLAN-interface 200\.

\[SwitchC] interface
vlan-interface 200

\[SwitchC-Vlan-interface200]
mld enable

\[SwitchC-Vlan-interface200]
quit

\# Enable IPv6 PIM-DM on VLAN-interface
202\.

\[SwitchC] interface vlan-interface
202

\[SwitchC-Vlan-interface202] ipv6
pim dm

\[SwitchC-Vlan-interface202] quit

**2\.**Configure an IPv6 multicast group policy on
Switch A so that the hosts connected to VLAN-interface 100 can join only IPv6
multicast group FF1E::101.

\[SwitchA] acl ipv6 basic 2001

\[SwitchA-acl-ipv6-basic-2001]
rule permit source ff1e::101 128

\[SwitchA-acl-ipv6-basic-2001]
quit

\[SwitchA] interface vlan-interface
100

\[SwitchA-Vlan-interface100] mld
group-policy 2001

\[SwitchA-Vlan-interface100]
quit

#### Verifying the configuration

\# Display MLD information for VLAN-interface
200 on Switch B.

\[SwitchB] display mld interface
vlan-interface 200

 Vlan-interface200(FE80::200:5EFF:FE66:5100):

   MLD is enabled.

   MLD version: 1

   Query interval for MLD: 125s

   Other querier present time for
MLD: 255s

   Maximum query response time for
MLD: 10s

   Querier for MLD:
FE80::200:5EFF:FE66:5100 (This router)

  MLD groups reported in total: 1

### Example: Configuring MLD SSM mapping

#### Network configuration

As shown in [Figure 6](#_Ref369513208):

·     OSPFv3 runs on the network.

·     The IPv6 PIM-SM domain uses the SSM model for
IPv6 multicast delivery. The SSM group range is FF3E::/64.

·     MLDv2 runs on VLAN-interface 400 of Switch D.
The receiver host runs MLDv1, and does not support MLDv2. The receiver host
cannot specify IPv6 multicast sources in its membership reports. 

·     Source 1, Source 2, and Source 3 send IPv6
multicast packets to multicast groups in IPv6 SSM group range FF3E::/64. 

Configure the MLD SSM mapping feature on Switch
D so that the receiver host will receive IPv6 multicast data only from Source 1
and Source 3\. 

Figure 6 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705041_x_Img_x_png_6_2216042_294551_0.png)

 

Table 1 Interface and IPv6 address assignment

| Device | Interface | IPv6 address | Device | Interface | IPv6 address |
| --- | --- | --- | --- | --- | --- |
| Source 1 | — | 1001::1/64 | Source 3 | — | 3001::1/64 || Source 2 | — | 2001::1/64 | Receiver | — | 4001::1/64 || Switch A | Vlan-int100 | 1001::2/64 | Switch C | Vlan-int300 | 3001::2/64 || Switch A | Vlan-int101 | 1002::1/64 | Switch C | Vlan-int103 | 3002::1/64 || Switch A | Vlan-int104 | 1003::1/64 | Switch C | Vlan-int102 | 2002::2/64 || Switch B | Vlan-int200 | 2001::2/64 | Switch D | Vlan-int400 | 4001::2/64 || Switch B | Vlan-int101 | 1002::2/64 | Switch D | Vlan-int103 | 3002::2/64 || Switch B | Vlan-int102 | 2002::1/64 | Switch D | Vlan-int104 | 1003::2/64 |








 

#### Prerequisites

Assign an IPv6 address and prefix length to
each interface as shown in [Table 1](#_Ref369513382), and configure
OSPFv3 on the switches in the IPv6 PIM-SM domain.

#### Procedure

**1\.**Enable IPv6 multicast routing, and enable
IPv6 PIM-SM and MLD:

\# On Switch D, enable IPv6 multicast
routing.

\<SwitchD\> system-view

\[SwitchD] ipv6 multicast
routing

\[SwitchD-mrib6] quit

\# Enable MLDv2 on the receiver-side
interface (VLAN-interface 400).

\[SwitchD] interface
vlan-interface 400

\[SwitchD-Vlan-interface400]
mld enable

\[SwitchD-Vlan-interface400]
mld version 2

\[SwitchD-Vlan-interface400]
quit

\# Enable IPv6 PIM-SM on the other
interfaces.

\[SwitchD] interface
vlan-interface 103

\[SwitchD-Vlan-interface103]
ipv6 pim sm

\[SwitchD-Vlan-interface103]
quit

\[SwitchD] interface
vlan-interface 104

\[SwitchD-Vlan-interface104]
ipv6 pim sm

\[SwitchD-Vlan-interface104]
quit

\# On Switch A, enable IPv6 multicast
routing.

\<SwitchA\> system-view

\[SwitchA] ipv6 multicast
routing

\[SwitchA-mrib6] quit

\# Enable IPv6 PIM-SM on each interface.

\[SwitchA] interface
vlan-interface 100

\[SwitchA-Vlan-interface100]
ipv6 pim sm

\[SwitchA-Vlan-interface100]
quit

\[SwitchA] interface
vlan-interface 101

\[SwitchA-Vlan-interface101]
ipv6 pim sm

\[SwitchA-Vlan-interface101]
quit

\[SwitchA] interface
vlan-interface 104

\[SwitchA-Vlan-interface104] ipv6
pim sm

\[SwitchA-Vlan-interface104]
quit

\# Configure Switch B and Switch C in the
same way Switch A is configured. (Details not shown.)

**2\.**Configure the IPv6 SSM group range:

\# On Switch D, specify FF3E::/64 as the IPv6 SSM
group range.

\[SwitchD] acl ipv6 basic 2000

\[SwitchD-acl-ipv6-basic-2000]
rule permit source ff3e:: 64

\[SwitchD-acl-ipv6-basic-2000]
quit

\[SwitchD] pim ipv6

\[SwitchD-pim6] ssm-policy 2000

\[SwitchD-pim6] quit

\# Configure Switch A, Switch B and Switch
C in the same way Switch D is configured. (Details not shown.)

**3\.**Configure MLD SSM mappings on Switch D.

\[SwitchD] mld

\[SwitchD-mld] ssm-mapping
1001::1 2000

\[SwitchD-mld] ssm-mapping
3001::1 2000

\[SwitchD-mld] quit

#### Verifying the configuration

\# Display MLD SSM mappings for IPv6 multicast
group FF3E::101 on Switch D.

\[SwitchD] display mld ssm-mapping ff3e::101

 Group: FF3E::101

 Source list:

        1001::1

        3001::1

\# On Switch D, display information about MLD
multicast groups that hosts have dynamically joined.

\[SwitchD] display mld group

MLD groups in total: 1

 Vlan-interface400(FE80::101):

  MLD groups reported in total: 1

   Group address: FF3E::101

    Last reporter: FE80::1

    Uptime: 00:02:04

    Expires: Off

\# Display IPv6 PIM routing entries on
Switch D.

\[SwitchD] display ipv6 pim routing-table

 Total 0 (\*, G) entry; 2 (S, G) entry

 

 (1001::1, FF3E::101)

     Protocol: pim-ssm, Flag:

     UpTime: 00:13:25

     Upstream interface:
Vlan-interface104

         Upstream neighbor: FE80::1

         RPF prime neighbor: FE80::1

     Downstream interface(s)
information:

     Total number of downstreams: 1

         1: Vlan-interface400

             Protocol: mld, UpTime:
00:13:25, Expires: \-

 

 (3001::1, FF3E::101)

     Protocol: pim-ssm, Flag:

     UpTime: 00:13:25

     Upstream interface:
Vlan-interface103

         Upstream neighbor: FE80::1

         RPF prime neighbor: FE80::1

     Downstream interface(s)
information:

     Total number of downstreams: 1

         1: Vlan-interface400

             Protocol: mld, UpTime:
00:13:25, Expires: \-

### Example: Configuring MLD proxying

#### Network configuration

As shown in [Figure 7](#_Ref206992127):

·     IPv6 PIM-DM runs on the core network. 

·     Host A and Host C on the stub network receive
VOD information destined to IPv6 multicast group FF3E::101. 

Configure the MLD proxying feature on
Switch B so that Switch B can maintain group memberships and forward IPv6
multicast traffic without running IPv6 PIM-DM. 

Figure 7 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705042_x_Img_x_png_7_2216042_294551_0.png)

 

#### Prerequisites

Assign an IPv6 address and prefix length to
each interface, as shown in [Figure 7](#_Ref206992127).

#### Procedure

**1\.**Enable IPv6 multicast routing, IPv6 PIM-DM, MLD,
and MLD proxying:

\# On Switch A, enable IPv6 multicast
routing.

\<SwitchA\> system-view

\[SwitchA] ipv6 multicast
routing

\[SwitchA-mrib6] quit

\# Enable IPv6 PIM-DM on VLAN-interface
101\.

\[SwitchA] interface
vlan-interface 101

\[SwitchA-Vlan-interface101] ipv6
pim dm

\[SwitchA-Vlan-interface101]
quit

\# Enable MLD on VLAN-interface 100\.

\[SwitchA] interface
vlan-interface 100

\[SwitchA-Vlan-interface100]
mld enable

\[SwitchA-Vlan-interface100]
quit

\# On Switch B, enable IPv6 multicast
routing.

\<SwitchB\> system-view

\[SwitchB] multicast ipv6
routing-enable

\[SwitchB-mrib6] quit

\# Enable MLD proxying on VLAN-interface
100\.

\[SwitchB] interface
vlan-interface 100

\[SwitchB-Vlan-interface100]
mld proxying enable

\[SwitchB-Vlan-interface100]
quit

\# Enable MLD on VLAN-interface 200\.

\[SwitchB] interface
vlan-interface 200

\[SwitchB-Vlan-interface200]
mld enable

\[SwitchB-Vlan-interface200]
quit

#### Verifying the configuration

\# Display IPv6 multicast group membership
information maintained by the MLD proxy on Switch B.

\[SwitchB] display mld proxy group

MLD proxy group records in total: 1

 Vlan-interface100(FE80::16:1):

  MLD proxy group records in total: 1

   Group address: FF1E::1

    Member state: Delay

    Expires: 00:00:02

## Troubleshooting MLD

### No member information exists on the receiver-side device

#### Symptom

When a host sends a message to announce
that it is joining IPv6 multicast group G, no member information of multicast
group G exists on the immediate device.

#### Solution

To resolve the problem:

**1\.**Use the display mld interface command to verify that the networking, interface connections, and
IP address configuration are correct.

**2\.**Use the display current-configuration command to verify that the IPv6 multicast routing is enabled. If it
is not enabled, use the ipv6 multicast routing
command in system view to enable IPv6 multicast routing. In addition, verify
that MLD is enabled on the associated interfaces.

**3\.**Use the display mld interface command to verify that the MLD version on the interface is lower
than that on the host.

**4\.**Use the display current-configuration interface command to verify that no IPv6 multicast group policies have been
configured to filter MLD reports for IPv6 multicast group G.

**5\.**If the problem persists, contact H3C Support.

### Inconsistent membership information on the devices on the same subnet

#### Symptom

Different memberships are maintained on
different MLD devices on the same subnet.

#### Solution

To resolve the problem:

**1\.**Use the display current-configuration command to verify the MLD information on the interface. Make sure the
devices on the subnet have the same MLD settings on their interfaces.

**2\.**Use the display mld interface command on all devices on the same subnet to check the MLD timers
for inconsistent configurations.

**3\.**Use the display mld interface command to verify that all devices are running the same MLD
version.

**4\.**If the problem persists, contact H3C Support.

