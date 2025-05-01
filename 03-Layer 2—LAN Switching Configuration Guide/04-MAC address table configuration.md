
# Configuring the MAC address table

## About the MAC address table

An Ethernet device uses a MAC address table
to forward frames. A MAC address entry includes a destination MAC address, an
outgoing interface, and a VLAN ID. When the device receives a frame, it uses
the destination MAC address of the frame to look for a match in the MAC address
table.

·     The device forwards the frame out of the
outgoing interface in the matching entry if a match is found. 

·     The device floods the frame in the VLAN of the
frame if no match is found.

### How a MAC address entry is created

The entries in the MAC address table
include entries automatically learned by the device and entries manually added.

#### MAC address learning

The device can automatically populate its MAC
address table by learning the source MAC addresses of incoming frames on each interface.


The device performs the following operations
to learn the source MAC address of incoming packets:

**1\.**Checks the source MAC address (for example, MAC-SOURCE) of the frame. 

**2\.**Looks up the source MAC address in the MAC
address table. 

¡     The
device updates the entry if an entry is found.

¡     The
device adds an entry for MAC-SOURCE and the incoming port if no entry is found.

When the device receives a frame destined
for MAC-SOURCE after learning this source MAC address, the device performs the
following operations:

**1\.**Finds the MAC-SOURCE entry in the MAC address table. 

**2\.**Forwards the frame out of the port in the
entry.

The device performs the learning process for
each incoming frame with an unknown source MAC address until the table is fully
populated. 

#### Manually configuring MAC address entries

Dynamic MAC address learning does not
distinguish between illegitimate and legitimate frames, which can invite
security hazards. When Host A is connected to Port A, a MAC address entry will
be learned for the MAC address of Host A (for example, MAC A). When an illegal
user sends frames with MAC A as the source MAC address to Port B, the device
performs the following operations: 

**1\.**Learns a new MAC address entry with Port B
as the outgoing interface and overwrites the old entry for MAC A.

**2\.**Forwards frames destined for MAC A out of Port
B to the illegal user. 

As a result, the illegal user obtains the
data of Host A. To improve the security for Host A, manually configure a static
entry to bind Host A to Port A. Then, the frames destined for Host A are always
sent out of Port A. Other hosts using the forged MAC address of Host A cannot
obtain the frames destined for Host A.

### Types of MAC address entries

A MAC address table can contain the
following types of entries:

·     Static entries—A static entry is manually added to forward frames with a specific
destination MAC address out of the associated interface, and it never ages out.
A static entry has higher priority than a dynamically learned one.

·     Dynamic entries—A dynamic entry can be manually configured or dynamically learned
to forward frames with a specific destination MAC address out of the associated
interface. A dynamic entry might age out. A manually configured dynamic entry
has the same priority as a dynamically learned one.

·     Blackhole entries—A blackhole entry is manually configured and never ages out. A
blackhole entry is configured for filtering out frames with a specific source
or destination MAC address. For example, to block all frames destined for or
sourced from a user, you can configure the MAC address of the user as a
blackhole MAC address entry. A blackhole entry has higher priority than a
dynamically learned one.

·     Multiport unicast entries—A multiport unicast entry is manually added to send frames with a
specific unicast destination MAC address out of multiple ports, and it never
ages out. A multiport unicast entry has higher priority than a dynamically
learned one.

A static or blackhole MAC address entry can
overwrite a dynamic MAC address entry. A dynamic MAC address entry cannot
overwrite a static, blackhole, or multiport unicast MAC address entry. A static
entry, a blackhole entry, and a multiport unicast entry cannot overwrite one
another.

A multiport unicast MAC address entry does
not affect learning the corresponding dynamic MAC address entry. For the same
MAC address, a multiport unicast MAC address entry and a dynamic MAC address
entry can coexist, and the multiport unicast MAC address takes priority. 

This document does not cover the
configuration of static multicast MAC address entries and MAC address entries
in VPLS. For more information about configuring static multicast MAC address
entries, see IGMP snooping in IP Multicast Configuration
Guide. For more information about MAC address table configuration in
VPLS, see VPLS in MPLS Configuration Guide. 

## MAC address table tasks at a glance

All MAC address table configuration tasks
are optional.

To configure the MAC address table, perform
the following tasks:

·     [Configuring MAC address entries](#_Ref156981119)

¡     [Adding or modifying a static or dynamic MAC
address entry](#_Ref472686317)

¡     [Adding or modifying a blackhole MAC address
entry](#_Ref386615913)

¡     [Adding or modifying a multiport unicast MAC
address entry](#_Ref386615918)

¡     [Adding or modifying a multiport unicast MAC
address entry for VXLAN](#_Ref529192454)

·     [Setting the aging timer for dynamic MAC
address entries](#_Ref472686331)

·     Configuring MAC address learning

¡     [Disabling MAC address learning](#_Ref156981124)

¡     [Setting the MAC learning limit](#_Ref156981142)

¡     [Configuring the unknown frame forwarding
rule after the MAC learning limit is reached](#_Ref386615245)

¡     [Assigning MAC learning priority to
interfaces](#_Ref208398963)

·     [Enabling MAC address synchronization](#_Ref386615258)

·     [Configuring MAC address move notifications
and suppression](#_Ref386615266)

·     [Enabling ARP fast update for MAC address
moves](#_Ref387062343)

·     [Disabling static source check](#_Ref397496798)

¡     [Disabling static source check globally](#_Ref33633410)

¡     [Disabling static source check on an interface](#_Ref33633415)

·     [Enabling SNMP notifications for the MAC
address table](#_Ref522538949)

## Configuring MAC address entries

### About MAC address entry-based frame forwarding

A frame whose source MAC address matches
different types of MAC address entries is processed differently.

 

| Type | Description |
| --- | --- |
| Static MAC address entry | Forwards the frame according to the destination MAC address regardless of whether the frame's ingress interface is the same as that in the entry. || Multiport unicast MAC address entry | Learns the source MAC address of the frame, generates a dynamic MAC address entry for that MAC address, and forwards the frame. The multiport unicast MAC address entry has higher priority than the dynamic MAC address entry in traffic forwarding. || Blackhole MAC address entry | Drops the frame. || Dynamic MAC address entry | ·     Learns the MAC address of the frames received on a different interface from that in the entry and overwrites the original entry.  ·     Forwards the frame received on the same interface as that in the entry and updates the aging timer for the entry. |




 

### Restrictions and guidelines for MAC address entry configuration

A manually configured dynamic MAC address entry
will overwrite a learned entry that already exists with a different outgoing
interface for the MAC address. 

The manually configured static, blackhole,
and multiport unicast MAC address entries cannot survive a reboot if you do not
save the configuration. The manually configured dynamic MAC address entries are
lost upon reboot whether or not you save the configuration.

Do not configure the reserved MAC addresses
of the device as static, dynamic, blackhole, or multiport unicast MAC
addresses. The reserved MAC addresses of a device are MAC addresses from the
bridge MAC address of the device to the bridge MAC address plus 127\. For
information about bridge MAC addresses, see IRF in Virtual
Technologies Configuration Guide. 

### Prerequisites for MAC address entry configuration

Before manually configuring a MAC address
entry for an interface, make sure the VLAN in the entry has been created.

### Adding or modifying a static or dynamic MAC address entry

#### Adding or modifying a static or dynamic MAC address entry globally

**1\.**Enter system view.

system-view 

**2\.**Add or modify a static or dynamic MAC address
entry.

mac-address { dynamic \| static } mac\-address interface interface\-type interface\-number vlan vlan\-id 

By default, no MAC address entry is
configured globally.

Make sure you have assigned the interface
to the VLAN.

#### Adding or modifying a static or dynamic MAC address entry on an interface

**1\.**Enter system view.

system-view 

**2\.**Enter interface view.

¡     Enter
Layer 2 Ethernet interface view.

interface interface-type interface-number

¡     Enter
Layer 2 aggregate interface view.

interface bridge-aggregation interface-number

**3\.**Add or modify a static or dynamic MAC
address entry.

mac\-address { dynamic \| static } mac\-address vlan vlan\-id 

By default, no MAC address entry is
configured on an interface.

Make sure you have assigned the interface
to the VLAN.

### Adding or modifying a blackhole MAC address entry

**1\.**Enter system view.

system-view 

**2\.**Add or modify a blackhole MAC address entry.

mac\-address blackhole
mac\-address vlan vlan\-id

By default, no blackhole MAC address
entry is configured.

### Adding or modifying a multiport unicast MAC address entry

#### About this task

You can configure a multiport unicast MAC
address entry to associate a unicast destination MAC address with multiple
ports. The frame with a destination MAC address matching the entry is sent out
of multiple ports. 

For example, in NLB unicast mode (see [Figure 1](#_Ref405917583)):

·     All servers within a cluster uses the cluster's
MAC address as their own address.

·     Frames destined for the cluster are forwarded to
every server in the group.

In this case, you can configure a multiport
unicast MAC address entry on the device connected to the server group. Then, the
device forwards the frame destined for the server group to every server through
all ports connected to the servers within the cluster.

Figure 1 NLB cluster

![](https://resource.h3c.com/en/202407/12/20240712_11704322_x_Img_x_png_0_2215974_294551_0.png)

 

You can configure a multiport unicast MAC
address entry globally or on an interface.

#### Configuring a multiport unicast MAC address entry globally

**1\.**Enter system view.

system-view 

**2\.**Add or modify a multiport unicast MAC
address entry.

mac\-address
multiport mac\-address interface interface-list vlan vlan\-id

By default, no multiport unicast MAC
address entry is configured globally.

Make sure you have assigned the interface
to the VLAN.

#### Configuring a multiport unicast MAC address entry on an interface

**1\.**Enter system view.

system-view 

**2\.**Enter interface view.

¡     Enter
Layer 2 Ethernet interface view.

interface interface-type
interface-number

¡     Enter
Layer 2 aggregate interface view.

interface bridge-aggregation interface-number

**3\.**Add the interface to a multiport unicast MAC
address entry.

mac\-address multiport
mac\-address vlan vlan\-id

By default, no multiport unicast MAC
address entry is configured on an interface.

Make sure you have assigned the interface
to the VLAN.

### Adding or modifying a multiport unicast MAC address entry for VXLAN

#### About this task

VXLAN has local and remote MAC addresses. A
local MAC address is the MAC address of a VM in the local site. A remote MAC
address is the MAC address of a VM in a remote site.

To send frames destined for a local or remote
MAC address out of multiple ports, configure a multiport unicast MAC address
entry. For more information about VXLAN, see VXLAN
Configuration Guide.

#### Restrictions and guidelines

Do not specify the tunnel interfaces
automatically created by using EVPN as outgoing interfaces for a remote
multiport unicast MAC address entry. If you do so, the numbers of these tunnel
interfaces might change during tunnel re-establishment, and the related entries
cannot be restored as a result. For more information about EVPN, see EVPN Configuration Guide.

In an EVPN network, you cannot configure
the same multiport unicast MAC address entry on multiple leaf nodes or VTEPs.
When configured with EVPN multihoming or EVPN M-LAG, a VTEP does not support
synchronization of multiport unicast MAC address entries. For more information
about EVPN, see EVPN Configuration Guide.

#### Procedure

**1\.**Enter system view.

system-view 

**2\.**Add or modify a multiport unicast MAC
address entry for VXLAN.

¡     Add or
modify a local multiport unicast MAC address entry.

mac-address multiport mac-address { interface { interface-type interface-number service-instance
instance-id }\&\<1-4\> } vsi vsi-name

For successful configuration, make sure the specified Ethernet
service instances have been mapped to the specified VSI.

¡     Add
or modify a remote multiport unicast MAC address entry.

mac-address multiport mac-address { interface { tunnel tunnel-number1
\[ to tunnel tunnel-number2 ] }\&\<1-4\> } vsi vsi-name

For successful configuration, make sure
the specified VXLAN tunnel interfaces have been associated with the specified
VSI. 

## Setting the aging timer for dynamic MAC address entries

#### About this task

For security and efficient use of table
space, the MAC address table uses an aging timer for each dynamic MAC address
entry. If a dynamic MAC address entry is not updated before the aging timer
expires, the device deletes the entry. This aging mechanism ensures that the
MAC address table can promptly update to accommodate latest network topology changes.


A stable network requires a longer aging
interval, and an unstable network requires a shorter aging interval. 

An aging interval that is too long might
cause the MAC address table to retain outdated entries. As a result, the MAC
address table resources might be exhausted, and the MAC address table might
fail to update its entries to accommodate the latest network changes. 

An interval that is too short might result
in removal of valid entries, which would cause unnecessary floods and possibly
affect the device performance.

To reduce floods on a stable network, set a
long aging timer or disable the timer to prevent dynamic entries from
unnecessarily aging out. Reducing floods improves the network performance.
Reducing flooding also improves the security because it reduces the chances for
a data frame to reach unintended destinations. 

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Set the aging timer for dynamic MAC address
entries.

mac-address timer { aging seconds \| no-aging }

By default, the aging timer is 300 seconds
for dynamic MAC address entries.

## Disabling MAC address learning

### About disabling MAC address learning

MAC address learning is enabled by default.
To prevent the MAC address table from being saturated when the device is
experiencing attacks, disable MAC address learning. For example, you can
disable MAC address learning to prevent the device from being attacked by a
large amount of frames with different source MAC addresses.

After MAC address learning is disabled, the
device immediately deletes existing dynamic MAC address entries. 

### Disabling global MAC address learning

#### Restrictions and guidelines

After you disable global MAC address
learning, the device cannot learn MAC addresses on any interfaces.

Global MAC address learning does not take
effect on a VPLS VSI or VXLAN VSI. For information about VPLS VSIs, see MPLS Configuration Guide. For information about VXLAN
VSIs, see VXLAN Configuration Guide. 

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Disable global MAC address learning.

undo mac-address mac-learning enable

By default, global MAC address learning
is enabled.

### Disabling MAC address learning on an interface

#### About this task

When global MAC address learning is enabled,
you can disable MAC address learning on a single interface.

#### Restrictions and guidelines

The mac-address mac-learning enable command and its undo form do
not take effect on a Layer 2 aggregate interface acting as a peer-link
interface, because MAC address learning is always disabled on that interface. For
more information about peer-link interfaces, see "Configuring M-LAG."

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter interface view.

¡     Enter
Layer 2 Ethernet interface view.

interface interface-type interface-number

¡     Enter
Layer 2 aggregate interface view.

interface bridge-aggregation interface-number

**3\.**Disable MAC address learning on the
interface.

undo mac-address mac-learning enable

By default, MAC address learning is
enabled on an interface.

### Disabling MAC address learning on a VLAN

#### About this task

When global MAC address learning is enabled,
you can disable MAC address learning on a per-VLAN basis.

As shown in [Figure 2](#_Ref101708706), VLAN
2, VLAN 3, and VLAN 10 are configured on the network to isolate users from
different departments. To save MAC address entry resources on the core device
and deny external access to these VLANs, you can disable MAC address learning
for the VLANs other than VLAN 2, VLAN 3, and VLAN 10 on Device A and Device B. 

Figure 2 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11704323_x_Img_x_png_1_2215974_294551_0.png)

 

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter VLAN view.

vlan vlan-id

**3\.**Disable MAC address learning on the VLAN.

undo mac-address mac-learning enable

By default, MAC address learning on the
VLAN is enabled.

## Setting the MAC learning limit

#### About this task

This feature limits the MAC address table
size. A large MAC address table will degrade forwarding performance.

#### Restrictions and guidelines

The MAC learning limit does not control the
number of MAC addresses learned in voice VLANs. For more information, see "Configuring voice VLANs." 

The MAC learning limit does not take effect
on a Layer 2 aggregate interface acting as an M-LAG peer-link interface,
because MAC address learning is always disabled on that interface. For more
information about peer-link interfaces, see "Configuring M-LAG."

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter interface view.

¡     Enter
Layer 2 Ethernet interface view.

interface interface-type interface-number

¡     Enter
Layer 2 aggregate interface view.

interface bridge-aggregation interface-number

**3\.**Set the MAC learning limit on the interface.

mac-address max-mac-count count

By default, no MAC learning limit is
configured on an interface. 

## Configuring the unknown frame forwarding rule after the MAC learning limit is reached

#### About this task

In this document, unknown frames refer to
frames whose source MAC addresses are not in the MAC address table.

You can enable or disable forwarding of unknown
frames after the MAC learning limit is reached.

#### Procedure

**1\.**Enter system view.

system-view 

**2\.**Enter interface view.

¡     Enter
Layer 2 Ethernet interface view.

interface interface-type
interface-number

¡     Enter
Layer 2 aggregate interface view.

interface bridge-aggregation interface-number

**3\.**Configure the device to forward unknown
frames received on the interface after the MAC learning limit on the interface
is reached.

mac-address max-mac-count enable-forwarding

By default, the device can forward
unknown frames received on an interface after the MAC learning limit on the
interface is reached.

## Assigning MAC learning priority to interfaces

#### About this task

The MAC learning priority mechanism assigns
either low priority or high priority to an interface. An interface with high
priority can learn MAC addresses as usual. However, an interface with low
priority is not allowed to learn MAC addresses already learned on a
high-priority interface.

The MAC learning priority mechanism can
help defend your network against MAC address spoofing attacks. In a network
that performs MAC-based forwarding, an upper layer device MAC address might be
learned by a downlink interface because of a loop or attack to the downlink
interface. To avoid this issue, perform the following tasks:

·     Assign high MAC learning priority to an uplink
interface.

·     Assign low MAC learning priority to a downlink
interface.

#### Restrictions and guidelines

In an IRF fabric, this feature takes effect
only on interfaces on one IRF member device. An interface with low MAC learning
priority can still learn MAC addresses already learned on a high-priority
interface of a different IRF member device.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter interface view.

¡     Enter
Layer 2 Ethernet interface view.

interface interface-type interface-number

¡     Enter
Layer 2 aggregate interface view.

interface bridge-aggregation interface-number

**3\.**Assign MAC learning priority to the
interface.

mac-address mac-learning priority { high \| low }

By default, low MAC learning priority is used.

## Enabling MAC address synchronization

#### About this task

To avoid unnecessary floods and improve
forwarding speed, make sure all member devices have the same MAC address table.
After you enable MAC address synchronization, each member device advertises
learned MAC address entries to other member devices.

As shown in [Figure 3](#_Ref289347142):

·     Device A and Device B form an IRF fabric enabled
with MAC address synchronization. 

·     Device A and Device B connect to AP C and AP D,
respectively. 

When Client A associates with AP C, Device
A learns a MAC address entry for Client A and advertises it to Device B. 

Figure 3 MAC address tables of devices when
Client A accesses AP C

![](https://resource.h3c.com/en/202407/12/20240712_11704324_x_Img_x_png_2_2215974_294551_0.png)

 

When Client A roams to AP D, Device B
learns a MAC address entry for Client A. Device B advertises it to Device A to
ensure service continuity for Client A, as shown in [Figure 4](#_Ref331495471).

Figure 4 MAC
address tables of devices when Client A roams to APD

![](https://resource.h3c.com/en/202407/12/20240712_11704325_x_Img_x_png_3_2215974_294551_0.png)

 

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enable MAC address synchronization.

mac-address mac-roaming enable

By default, MAC address synchronization
is disabled.

## Configuring MAC address move notifications and suppression

#### About this task

The outgoing interface for a MAC address
entry learned on interface A is changed to interface B when the following
conditions exist:

·     Interface B receives a packet with the MAC
address as the source MAC address.

·     Interface B belongs to the same VLAN as
interface A. 

In this case, the MAC address is moved from
interface A to interface B, and a MAC address move occurs. 

The MAC address move notifications feature
enables the device to output MAC address move logs when MAC address moves are
detected.

If a MAC address is continuously moved between
the two interfaces, Layer 2 loops might occur. To detect and locate loops, you
can view the MAC address move information. To display the MAC address move
records after the device is started, use the display
mac-address mac-move command. 

If the system detects that MAC address moves
occur frequently on an interface, you can configure MAC address move
suppression to shut the interface down. The interface automatically goes up
after a suppression interval. Or, you can manually bring up the interface.

#### Restrictions and guidelines

After you configure MAC address move
notifications, the system sends only log messages to the information center
module. If the device is also configured with the snmp-agent trap
enable mac-address command, the system also
sends SNMP notifications to the SNMP module.

#### Procedure

**1\.**Enter system view. 

system-view

**2\.**Enable MAC address move notifications and
optionally specify a MAC move detection interval. 

mac-address notification mac-move \[ interval interval ]

By default, MAC address move
notifications are disabled.

**3\.**(Optional.) Set MAC address move suppression
parameters. 

mac-address notification mac-move suppression { interval interval \| threshold threshold }

By default, the suppression interval is
30 seconds, and the suppression threshold is 3\.

For the MAC address move suppression
parameters to take effect, enable the MAC address move suppression on a port.

**4\.**Enter interface view.

¡     Enter
Layer 2 Ethernet interface view.

interface interface-type interface-number

¡     Enter
Layer 2 aggregate interface view.

interface bridge-aggregation interface-number

**5\.**Enable MAC address move suppression. 

mac-address notification mac-move suppression

By default, MAC address move suppression
is disabled.

## Enabling ARP fast update for MAC address moves

#### About this task

ARP fast update for MAC address moves
allows the device to update an ARP entry immediately after the outgoing
interface for a MAC address changes. This feature ensures data connection
without interruption.

Typically, the MAC address entry and ARP
entry created for an endpoint contain the same MAC address and outgoing
interface. In some scenarios, these entries might contain inconsistent
information if the endpoint fast moves between interfaces. As shown in [Figure 5](#_Ref254770196), a mobile user laptop accesses the network by connecting to
AP 1 or AP 2\. When the AP to which the user connects changes, the device updates
the ARP entry for the user immediately after it detects a MAC address move.

Figure 5 ARP fast update application
scenario

![](https://resource.h3c.com/en/202407/12/20240712_11704326_x_Img_x_png_4_2215974_294551_0.png)

‌

#### Procedure

**1\.**Enter system view. 

system-view

**2\.**Enable ARP fast update for MAC address
moves. 

mac-address mac-move fast-update

By default, ARP fast update for MAC
address moves is disabled.

## Disabling static source check

### About this task

By default, the static source check feature
is enabled on an interface. The check identifies whether a received frame meets
the following conditions:

·     The source MAC address of the frame matches a
static MAC address entry.

·     The incoming interface of the frame is different
from the outgoing interface in the entry.

If the frame meets both conditions, the device
drops the frame.

When this feature is disabled, the device
does not perform the check for a received frame. It can forward the frame
whether or not the frame meets the conditions.

### Restrictions and guidelines

To correctly forward traffic sourced from
the MAC address of a VLAN interface, you must disable the static source check
feature on the Layer 2 interfaces in the VLAN.

When static source check is disabled on
interfaces, the device does not perform the check for a frame received from any
interface. If the static source check feature is disabled on a member port in
an aggregation group, the configuration does not take effect until the member
port is removed from the aggregation group. 

### Disabling static source check globally

**1\.**Enter system view. 

system-view

**2\.**Disable the static source check feature
globally.

undo mac-address static source-check enable

By default, the static source check
feature is enabled globally.

### Disabling static source check on an interface

**1\.**Enter system view. 

system-view

**2\.**Enter interface view.

¡     Enter
Layer 2 Ethernet interface view.

interface interface-type interface-number

¡     Enter
Layer 2 aggregate interface view.

interface bridge-aggregation interface-number

¡     Enter
Layer 3 Ethernet interface/subinterface view.

interface interface-type { interface-number \| interface-number.subnumber }

¡     Enter
Layer 3 aggregate interface/subinterface view.

interface route-aggregation { interface-number \| interface-number.subnumber }

¡     Enter
IRF physical interface view.

interface interface-type interface-number

**3\.**Disable the static source check feature on
the interface.

undo mac-address static source-check enable

By default, the static source check
feature is enabled on an interface.

## Enabling SNMP notifications for the MAC address table

#### About this task

To report critical MAC address move events
to an NMS, enable SNMP notifications for the MAC address table. For MAC address
move event notifications to be sent correctly, you must also configure SNMP on
the device.

When SNMP notifications are disabled for
the MAC address table, the device sends the generated logs to the information
center. To display the logs, configure the log destination and output rule
configuration in the information center.

For more information about SNMP and
information center configuration, see the network management and monitoring configuration
guide for the device. 

#### Procedure

**1\.**Enter system view. 

system-view

**2\.**Enable SNMP notifications for the MAC
address table. 

snmp-agent trap enable mac-address \[ mac-move ]

By default, SNMP notifications are enabled
for the MAC address table.

When SNMP notifications are disabled for
the MAC address table, syslog messages are sent to notify important events on
the MAC address table module.

## Display and maintenance commands for MAC address table

Execute display
commands in any view. 

 

| Task | Command |
| --- | --- |
| Display MAC address table information. | display mac-address \[ mac\-address \[ vlan vlan-id ] \| \[ \[ dynamic \| static ] \[ interface interface-type interface-number ] \| blackhole \| multiport ] \[ vlan vlan-id ] \[ count ] ] || Display the aging timer for dynamic MAC address entries. | display mac-address aging-time || Display the system or interface MAC address learning state. | display mac-address mac-learning \[ interface interface\-type interface\-number ] || Display the MAC address move records. | display mac-address mac-move \[ slot slot-number] || Display MAC address statistics. | display mac-address statistics |





 

## MAC address table configuration examples

### Example: Configuring the MAC address table

#### Network configuration

As shown in [Figure 6](#_Ref402798912):

·     Host A at MAC address 000f-e235-dc71 is
connected to Ten-GigabitEthernet 1/0/1 of Device and
belongs to VLAN 1\.

·     Host B at MAC address 000f-e235-abcd, which
behaved suspiciously on the network, also belongs to VLAN 1\.

Configure the MAC address table as follows:

·     To prevent MAC address spoofing, add a static
entry for Host A in the MAC address table of Device.

·     To drop all frames destined for Host B, add a
blackhole MAC address entry for Host B.

·     Set the aging timer to 500 seconds for dynamic
MAC address entries. 

Figure 6 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11704327_x_Img_x_png_5_2215974_294551_0.png)

‌

#### Procedure

\# Add a static MAC address entry for MAC
address 000f-e235-dc71 on Ten-GigabitEthernet 1/0/1 that belongs to
VLAN 1\.

\<Device\> system-view

\[Device] mac-address static
000f-e235-dc71 interface ten-gigabitethernet 1/0/1 vlan 1

\# Add a blackhole MAC address entry for MAC
address 000f-e235-abcd that belongs to VLAN 1\. 

\[Device] mac-address blackhole 000f-e235-abcd
vlan 1

\# Set the aging timer to 500 seconds for
dynamic MAC address entries.

\[Device] mac-address timer aging 500

#### Verifying the configuration

\# Display the static MAC address entries
for Ten-GigabitEthernet 1/0/1.

\[Device] display mac-address static interface
ten-gigabitethernet 1/0/1

MAC Address      VLAN ID   
State            Port/Nickname            Aging

000f-e235-dc71   1          Static           XGE1/0/1     
           N

\# Display the blackhole MAC address
entries. 

\[Device] display mac-address
blackhole

MAC Address      VLAN ID   
State            Port/Nickname            Aging

000f-e235-abcd   1         
Blackhole        N/A                      N

\# Display the aging time of dynamic MAC
address entries. 

\[Device] display mac-address
aging-time

MAC address aging time: 500s.

 

