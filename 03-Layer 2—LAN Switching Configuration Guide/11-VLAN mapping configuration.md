
# Configuring VLAN mapping

## About VLAN mapping

VLAN mapping re-marks VLAN traffic with new
VLAN IDs.

### VLAN mapping types

H3C provides the following types of VLAN
mapping:

·     One-to-one VLAN mapping—Replaces one VLAN tag with another.

·     Many-to-one VLAN mapping—Replaces multiple VLAN tags with the same VLAN tag.

·     One-to-two VLAN mapping—Tags single-tagged packets with an outer VLAN tag.

·     Two-to-two VLAN mapping—Replaces the outer and inner VLAN IDs of double tagged traffic with
a new pair of VLAN IDs.

### VLAN mapping application scenarios

#### One-to-one and many-to-one VLAN mapping

One-to-one and many-to-one VLAN mapping
are typically used by a community for broadband Internet access, as shown in[Figure 1](#_Ref332719373).

Figure 1 Application scenario of one-to-one
and many-to-one VLAN mapping

![](https://resource.h3c.com/en/202407/12/20240712_11704396_x_Img_x_png_0_2215981_294551_0.png)

‌

As shown in[Figure 1](#_Ref332719373),
the network is implemented as follows:

·     Each home gateway uses different VLANs to
transmit the PC, VoD, and VoIP services.

·     To further subclassify each type of traffic by
customer, configure one-to-one VLAN mapping on the wiring-closet switches. This
feature assigns a separate VLAN to each type of traffic from each customer. The
required total number of VLANs in the network can be very large.

·     To prevent the maximum number of VLANs from
being exceeded on the distribution layer device, configure many-to-one VLAN
mapping on the campus switch. This feature assigns the same VLAN to the same
type of traffic from different customers.

#### One-to-two and two-to-two VLAN mapping

One-to-two and
two-to-two VLAN mapping are typically used to implement communication across different
SP networks, as shown in[Figure 2](#_Ref332810683).

Figure 2 Application scenario of one-to-two
and two-to-two VLAN mapping

![](https://resource.h3c.com/en/202407/12/20240712_11704397_x_Img_x_png_1_2215981_294551_0.png)

‌

As shown in [Figure 2](#_Ref332810683), Site
1 and Site 2 of VPN A are in VLAN 2 and VLAN 3, respectively. The SP 1 network assigns
SVLAN 10 to Site 1\. The SP 2 network assigns SVLAN 20 to Site 2\. When the
packet from Site 1 arrives at PE 1, PE 1 tags the packet with SVLAN 10 by using
one-to-two VLAN mapping.

When the double-tagged packet from the SP 1 network arrives at the
SP 2 network interface, PE 3 processes the packet as follows:

·     Replaces SVLAN tag 10 with SVLAN tag 20\.

·     Replaces CVLAN tag 2 with CVLAN tag 3\.

One-to-two VLAN mapping provides the
following benefits:

·     Enables a customer network to plan its CVLAN
assignment without conflicting with SVLANs.

·     Adds a VLAN tag to a tagged packet and expands
the number of available VLANs to 4094 × 4094\.

·     Reduces the stress on the SVLAN resources, which
were 4094 VLANs in the SP network before the mapping process was initiated.

### VLAN mapping implementations

[Figure 3](#_Ref346897236)
shows a simplified network that illustrates basic VLAN mapping terms.

Basic VLAN mapping terms include the
following:

·     Uplink traffic—Traffic transmitted from the customer network to the service
provider network.

·     Downlink traffic—Traffic transmitted from the service provider network to the
customer network.

·     Network-side port—A port connected to or closer to the service provider network.

·     Customer-side port—A port connected to or closer to the customer network.

Figure 3 Basic VLAN mappingterms

![](https://resource.h3c.com/en/202407/12/20240712_11704400_x_Img_x_png_2_2215981_294551_0.png)

‌

#### One-to-one VLAN mapping

As shown in [Figure 4](#_Ref332810702),
one-to-one VLAN mapping is implemented on the customer-side port and replaces VLAN
tags as follows:

·     Replaces the CVLAN with the SVLAN for the uplink
traffic.

·     Replaces the SVLAN with the CVLAN for the
downlink traffic.

Figure 4 One-to-one VLAN mapping
implementation

![](https://resource.h3c.com/en/202407/12/20240712_11704401_x_Img_x_png_3_2215981_294551_0.png)

‌

#### Many-to-one VLAN mapping

As shown in [Figure 5](#_Ref35847353), many-to-one
VLAN mapping is implemented on both the customer-side and network-side ports as
follows:

·     For the uplink traffic, the customer-side many-to-one
VLAN mapping replaces multiple CVLANs with the same SVLAN.

·     For the downlink traffic, the device performs
the following operations:

**a.**Searches the MAC address table for an entry
that matches the destination MAC address of the downlink packets.

**b.**Replaces the SVLAN tag with a CVLAN tag
based on the matching many-to-one mapping entry.

For more information about the MAC
address table, see "Configuring the MAC address table." 

Figure 5 Many-to-one VLAN mapping
implementation 

![](https://resource.h3c.com/en/202407/12/20240712_11704402_x_Img_x_png_4_2215981_294551_0.png)

‌

Figure 6 Many-to-one VLAN mapping
implementation in common mode (IPv6)

![](https://resource.h3c.com/en/202407/12/20240712_11704403_x_Img_x_png_5_2215981_294551_0.png)

‌

Figure 7 Many-to-one
VLAN mapping in enhanced mode for Layer 3 downlink IPv6 traffic

![](https://resource.h3c.com/en/202407/12/20240712_11704404_x_Img_x_png_6_2215981_294551_0.png)

‌

#### One-to-two VLAN mapping

As shown in [Figure 8](#_Ref332810706),
one-to-two VLAN mapping is implemented on the customer-side port to add the SVLAN
tag for the uplink traffic.

For the downlink traffic to be correctly
sent to the customer network, make sure the SVLAN tag is removed on the
customer-side port before transmission. Use one of the following methods to remove
the SVLAN tag from the downlink traffic:

·     Configure the customer-side port as a hybrid
port and assign the port to the SVLAN as an untagged member.

·     Configure the customer-side port as a trunk port
and set the port PVID to the SVLAN.

Figure 8 One-to-two VLAN mapping
implementation

![](https://resource.h3c.com/en/202407/12/20240712_11704405_x_Img_x_png_7_2215981_294551_0.png)

‌

#### Two-to-two VLAN mapping

As
shown in[Figure 9](#_Ref332810707),
two-to-two VLAN mapping is implemented on the customer-side port and replaces
VLAN tags as follows:

·     Replaces the CVLAN and the SVLAN with the CVLAN'
and the SVLAN' for the uplink traffic.

·     Replaces the SVLAN' and CVLAN' with the SVLAN
and the CVLAN for the downlink traffic.

Figure 9 Two-to-two VLAN mapping
implementation

![](https://resource.h3c.com/en/202407/12/20240712_11704406_x_Img_x_png_8_2215981_294551_0.png)

‌

## Restrictions and guidelines: VLAN mapping configuration

To add VLAN tags to packets, you can configure
both VLAN mapping and QinQ. VLAN mapping takes effect if a configuration
conflict occurs. For more information about QinQ, see "Configuring
QinQ." 

To add or replace VLAN tags for packets, you
can configure both VLAN mapping and a QoS policy. The QoS policy takes effect if
a configuration conflict occurs. For information about QoS policies, see ACL and QoS Configuration Guide. 

Do not configure VLAN mapping and Ethernet
service instance-to-VSI binding on the same Layer 2 Ethernet interface or Layer
2 aggregate interface. Do not configure VLAN mapping on a Layer 2 Ethernet
interface or Layer 2 aggregate interface that acts as the source interface of a
VXLAN tunnel. Otherwise, these features might not take effect. For more
information about VXLAN and VSI, see VXLAN Configuration
Guide. 

## VLAN mapping tasks at a glance

Use the appropriate VLAN mapping methods
for the devices in the network.

To configure VLAN mapping, perform the
following tasks:

·     [Configuring one-to-one VLAN mapping](#_Ref332811207)

Configure one-to-one VLAN mapping on the
wiring-closet switch, as shown in [Figure 1](#_Ref332719373).

·     [Configuring many-to-one VLAN mapping](#_Ref121987973)

Configure many-to-one VLAN mapping on the
campus switch, as shown in [Figure 1](#_Ref332719373).

·     [Configuring one-to-two VLAN mapping](#_Ref408596004)

Configure one-to-two VLAN mapping on PE 1
and PE 4, as shown in [Figure 2](#_Ref332810683), through
which traffic from customer networks enters the service provider networks.

·     [Configuring two-to-two VLAN mapping](#_Ref332811236)

Configure two-to-two VLAN mapping on PE 3,
as shown in [Figure 2](#_Ref332810683), which
is an edge device of the SP 2 network. 

## Prerequisites

Before you configure VLAN mapping, create original
and translated VLANs.

## Configuring one-to-one VLAN mapping

#### About this task

Configure one-to-one VLAN mapping on the
customer-side ports of wiring-closet switches (see [Figure 1](#_Ref332719373)) to
isolate traffic of the same service type from different homes.

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

**3\.**Set the link type of the port. 

port link-type { hybrid \| trunk }

By default, the link type of a port is access. 

**4\.**Assign the port to the original VLAN and the
translated VLAN. 

¡     Assign
the trunk port to the original VLAN and the translated VLAN.

port trunk permit vlan vlan-id-list

By
default, a trunk port is assigned to VLAN 1\.

¡     Assign
the hybrid port to the original VLAN and the translated VLAN as a tagged member.

port hybrid vlan vlan-id-list tagged

By
default, a hybrid port is an untagged member of the VLAN to which the port
belongs when its link type is access.

**5\.**Configure a one-to-one VLAN mapping.

vlan mapping vlan-id translated-vlan vlan-id

By default, no VLAN mapping is configured
on an interface. 

## Configuring many-to-one VLAN mapping

### About many-to-one VLAN mapping

Configure many-to-one VLAN mapping on
campus switches (see [Figure 1](#_Ref332719373)) to
transmit the same type of traffic from different users in one VLAN.

#### Restrictions and guidelines for many-to-one VLAN mapping

·     To ensure correct traffic forwarding from the
service provider network to the customer network, do not configure many-to-one
VLAN mapping together with the following reatures:

¡     uRPF.

¡     Disabling
MAC address learning.

¡     Setting
the MAC learning limit.

For more information about uRPF, see Security Configuration Guide. For more information
about MAC address learning, see "Configuring the MAC address table."

·     To avoid network connection failure in a
many-to-one VLAN mapping environment, make sure that an ARP request has been
sent from the customer-side port for the connection to the network-side port.

·     You can configure only one pairs of many-to-one
VLAN mapping on the same interface. To modify the many-to-one VLAN mapping
settings, perform the following tasks:

**a.**Excecute the undo vlan
mapping command to cancel the current
many-to-one VLAN mapping settings.

**b.**Reconfigure the many-to-one VLAN mapping
settings.

#### Many-to-one VLAN mapping tasks at a glance

**1\.**[Configuring the
customer-side port](#_Ref121988001)

**2\.**[Configuring the
network-side port](#_Ref121988007)

#### Configuring the customer-side port

**1\.**Enter system view. 

system-view

**2\.**Enter interface view.

¡     Enter
Layer 2 Ethernet interface view.

interface interface-type interface-number

¡     Enter
Layer 2 aggregate interface view.

interface bridge-aggregation interface-number

**3\.**Set the link type of the port. 

port link-type { hybrid \| trunk }

By default, the link type of a port is access. 

**4\.**Assign the port to the original VLANs.

¡     Assign
the trunk port to the original VLANs 

port trunk permit vlan vlan-id-list

By default, a trunk port is assigned to
VLAN 1\.

¡     Assign
the hybrid port to the original VLANs as a tagged member.

port hybrid vlan vlan-id-list tagged

By default, a hybrid port is an untagged
member of the VLAN to which the port belongs when its link type is access.

**5\.**Configure a many-to-one VLAN mapping.

vlan mapping uni { range vlan-range-list \| single vlan-id-list }
translated-vlan vlan-id

By default, no VLAN mapping is configured
on an interface.

#### Configuring the network-side port

**1\.**Enter system view. 

system-view

**2\.**Enter interface view.

¡     Enter
Layer 2 Ethernet interface view.

interface interface-type interface-number

¡     Enter
Layer 2 aggregate interface view.

interface bridge-aggregation interface-number

**3\.**Set the link type of the port. 

port link-type { hybrid \| trunk }

By default, the link type of a port is access.

**4\.**Assign the port to the translated VLAN.

¡     Assign
the trunk port to the translated VLAN.

port trunk permit vlan vlan-id-list

By default, a trunk port is assigned to
VLAN 1\.

¡     Assign
the hybrid port to the translated VLAN as a tagged member.

port hybrid vlan vlan-id-list tagged

By default, a hybrid port is an untagged
member of the VLAN to which the port belongs when its link type is access.

## Configuring one-to-two VLAN mapping

#### About this task

Configure one-to-two VLAN mapping on the customer-side
ports of edge devices from which customer traffic enters SP networks, for
example, on PEs 1 and 4 in [Figure 2](#_Ref332810683).
One-to-two VLAN mapping enables the edge devices to add an SVLAN tag to each
incoming packet.

#### Restrictions and guidelines

Only one SVLAN tag can be added to packets
from the same CVLAN. To add different SVLAN tags to different CVLAN packets on
a port, set the port link type to hybrid and configure multiple one-to-two VLAN
mappings. 

The MTU of an interface is 1500 bytes by default.
After a VLAN tag is added to a packet, the packet length is added by 4 bytes. As
a best practice, set the MTU to a minimum of 1504 bytes for ports on the forwarding
path of the packet in the service provider network.

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

**3\.**Set the link type of the port.

port link-type { hybrid \| trunk }

By default, the link type of a port is access. 

**4\.**Assign the port to the CVLANs. 

¡     Assign the trunk port to the CVLANs.

port trunk permit vlan vlan-id-list

By
default, a trunk port is assigned to VLAN 1\.

¡     Assign
the hybrid port to the CVLANs.

port hybrid vlan vlan-id-list { tagged \| untagged }

By
default, a hybrid port is an untagged member of the VLAN to which the port
belongs when its link type is access.

**5\.**Configure the port to allow packets from the
SVLAN to pass through untagged.

¡     Configure the SVLAN as the PVID of the trunk port and assign the
trunk port to the SVLAN.

port trunk pvid vlan vlan-id

port trunk permit vlan
{ vlan-id-list \| all }

¡     Assign
the hybrid port to the SVLAN as an untagged member.

port hybrid vlan vlan-id-list untagged

**6\.**Configure a one-to-two VLAN mapping.

vlan mapping nest { range
vlan-range-list \| single vlan-id-list } nested-vlan vlan-id

By default, no VLAN mapping is configured
on an interface.

## Configuring two-to-two VLAN mapping

#### About this task

Configure two-to-two VLAN mapping on the
customer-side port of an edge device that connects two SP networks, for
example, on PE 3 in [Figure 2](#_Ref332810683).
Two-to-two VLAN mapping enables two sites in different VLANs to communicate at
Layer 2 across two service provider networks that use different VLAN assignment
schemes.

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

**3\.**Set the link type of the port. 

port link-type { hybrid \| trunk }

By default, the link type of a port is access. 

**4\.**Assign the port to the original VLANs and
the translated VLANs. 

¡     Assign the trunk port to the original VLANs and the translated VLANs.

port trunk permit vlan vlan-id-list

By default, a trunk port is assigned to
VLAN 1\.

¡     Assign
the hybrid port to the original VLANs and the translated VLANs as a tagged
member.

port hybrid vlan vlan-id-list tagged

By default, a hybrid port is an untagged
member of the VLAN to which the port belongs when its link type is access.

**5\.**Configure a two-to-two VLAN mapping.

vlan mapping tunnel outer-vlan-id
inner-vlan-id translated-vlan outer-vlan-id
inner-vlan-id

By default, no VLAN mapping is configured
on an interface. 

## Display and maintenance commands for VLAN mapping

Execute display
commands in any view.

 

| Task | Command |
| --- | --- |
| Display VLAN mapping information. | display vlan mapping \[ interface interface-type interface-number ] |

‌

## VLAN mapping configuration examples

### Example: Configuring one-to-one VLAN mapping

#### Network configuration

As shown in [Figure 10](#_Ref332811003):

·     Each household subscribes to PC, VoD, and VoIP services.

·     On the home gateways, VLANs 1, 2, and 3 are
assigned to PC, VoD, and VoIP traffic, respectively.

To isolate traffic of the same service type
from different households, configure one-to-one VLAN mappings on the
wiring-closet switches. This feature assigns one VLAN to each type of traffic
from each household.

Figure 10 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11704407_x_Img_x_png_9_2215981_294551_0.png)

‌

#### Procedure

**1\.**Configure Switch A:

\# Create the original VLANs.

\<SwitchA\> system-view

\[SwitchA] vlan 2 to 3

\# Create the translated VLANs.

\[SwitchA] vlan 101 to 102

\[SwitchA] vlan 201 to 202

\[SwitchA] vlan 301 to 302

\# Configure customer-side port Ten-GigabitEthernet 1/0/1 as a trunk port.

\<SwitchA\> system-view

\[SwitchA] interface ten-gigabitethernet 1/0/1

\[SwitchA-Ten-GigabitEthernet1/0/1] port link-type
trunk

\# Assign Ten-GigabitEthernet 1/0/1 to all original
VLANs and translated VLANs.

\[SwitchA-Ten-GigabitEthernet1/0/1] port trunk permit
vlan 1 2 3 101 201 301

\# Configure one-to-one VLAN mappings on Ten-GigabitEthernet 1/0/1 to map VLANs 1, 2,
and 3 to VLANs 101, 201, and 301, respectively.

\[SwitchA-Ten-GigabitEthernet1/0/1] vlan mapping 1
translated-vlan 101

\[SwitchA-Ten-GigabitEthernet1/0/1] vlan mapping 2
translated-vlan 201

\[SwitchA-Ten-GigabitEthernet1/0/1] vlan mapping 3
translated-vlan 301

\[SwitchA-Ten-GigabitEthernet1/0/1] quit

\# Configure customer-side port Ten-GigabitEthernet 1/0/2 as a trunk port.

\[SwitchA] interface ten-gigabitethernet 1/0/2

\[SwitchA-Ten-GigabitEthernet1/0/2] port link-type
trunk

\# Assign Ten-GigabitEthernet 1/0/2 to all original
VLANs and translated VLANs.

\[SwitchA-Ten-GigabitEthernet1/0/2] port trunk permit
vlan 1 2 3 102 202 302

\# Configure one-to-one VLAN mappings on Ten-GigabitEthernet 1/0/2 to map VLANs 1, 2,
and 3 to VLANs 102, 202, and 302, respectively.

\[SwitchA-Ten-GigabitEthernet1/0/2] vlan mapping 1
translated-vlan 102

\[SwitchA-Ten-GigabitEthernet1/0/2] vlan mapping 2
translated-vlan 202

\[SwitchA-Ten-GigabitEthernet1/0/2] vlan mapping 3
translated-vlan 302

\[SwitchA-Ten-GigabitEthernet1/0/2] quit

\# Configure the network-side port (Ten-GigabitEthernet 1/0/3) as a trunk port.

\[SwitchA] interface ten-gigabitethernet 1/0/3

\[SwitchA-Ten-GigabitEthernet1/0/3] port link-type
trunk

\# Assign Ten-GigabitEthernet 1/0/3 to the translated
VLANs.

\[SwitchA-Ten-GigabitEthernet1/0/3] port trunk permit
vlan 101 201 301 102 202 302

\[SwitchA-Ten-GigabitEthernet1/0/3] quit

**2\.**Configure Switch B in the same way Switch A
is configured. (Details not shown.)

#### Verifying the configuration

\# Verify VLAN mapping information on the
wiring-closet switches, for example, Switch A.

\[SwitchA] display vlan mapping

Interface Ten-GigabitEthernet1/0/1:

  Outer VLAN    Inner VLAN    Translated
Outer VLAN    Translated Inner VLAN

  1             N/A           101                     
N/A

  2             N/A           201                     
N/A

  3             N/A           301                      N/A

Interface Ten-GigabitEthernet1/0/2:

  Outer VLAN    Inner VLAN    Translated
Outer VLAN    Translated Inner VLAN

  1             N/A           102                     
N/A

  2             N/A           202                     
N/A

  3             N/A          
302                      N/A

### Example: Configuring many-to-one VLAN mapping

#### Network configuration

As shown in [Figure 11](#_Ref81850866):

·     Create VLAN 2, VLAN 3, and VLAN 4 on the
wiring-closet switches to isolate traffic of the same service type from
different households. 

·     Configure many-to-one VLAN mappings on the
campus switch. This feature assigns one VLAN to each type of traffic from
different households.

Figure 11 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11704398_x_Img_x_png_10_2215981_294551_0.png)

 

#### Procedure

**1\.**Configure Switch A:

\# Create VLAN 2 as an original VLAN.

\<SwitchA\> system-view

\[SwitchA] vlan 2

\[SwitchA-vlan2] quit

\# Assign ports Ten-GigabitEthernet 1/0/1
and Ten-GigabitEthernet 1/0/2 to VLAN 2\.

\[SwitchA] interface range ten-gigabitethernet
1/0/1 to ten-gigabitethernet 1/0/2

\[SwitchA-if-range] port access
vlan 2

\[SwitchA-if-range] quit

**2\.**Configure Switch B and Switch C in the same
way Switch A is configured. (Details not shown.)

**3\.**Configure Switch D:

\# Create VLANs 2, 3, and 4 as original VLANs.

\<SwitchD\> system-view

\[SwitchD] vlan 2 to 4

\# Assign ports Ten-GigabitEthernet 1/0/1
to VLAN 2, Ten-GigabitEthernet 1/0/2 to VLAN 3, and Ten-GigabitEthernet 1/0/3
to VLAN 4\.

\[SwitchD] interface
ten-gigabitethernet 1/0/1

\[SwitchD-Ten-GigabitEthernet1/0/1]
port access vlan 2

\[SwitchD-Ten-GigabitEthernet1/0/1]
quit

\[SwitchD] interface
ten-gigabitethernet 1/0/2

\[SwitchD-Ten-GigabitEthernet1/0/2]
port access vlan 3

\[SwitchD-Ten-GigabitEthernet1/0/2]
quit

\[SwitchD] interface
ten-gigabitethernet 1/0/3

\[SwitchD-Ten-GigabitEthernet1/0/3]
port access vlan 4

\[SwitchD-Ten-GigabitEthernet1/0/3]
quit

\# Configure port Ten-GigabitEthernet
1/0/4 as a trunk port.

\[SwitchD] interface
ten-gigabitethernet 1/0/4

\[SwitchD-Ten-GigabitEthernet1/0/4]
port link-type trunk

\# Assign Ten-GigabitEthernet 1/0/4 to the
original VLANs.

\[SwitchD-Ten-GigabitEthernet1/0/4]
port trunk permit vlan 2 to 4

\[SwitchD-Ten-GigabitEthernet1/0/4]
quit

**4\.**Configure Switch E:

\# Create VLANs 2, 3, and 4 as original
VLANs.

\<SwitchD\> system-view

\[SwitchD] vlan 2 to 4

\# Configure the customer-side port (Ten-GigabitEthernet
1/0/1) as a trunk port.

\<SwitchE\> system-view

\[SwitchE] interface
ten-gigabitethernet 1/0/1

\[SwitchE-Ten-GigabitEthernet1/0/1]
port link-type trunk

\# Assign Ten-GigabitEthernet 1/0/1 to the
original VLANs.

\[SwitchE-Ten-GigabitEthernet1/0/1]
port trunk permit vlan 2 to 4

\# Configure many-to-one VLAN mapping on Ten-GigabitEthernet
1/0/1, which replaces VLAN tag 2 through VLAN tag 4 with VLAN tag 10\.

\[SwitchE-Ten-GigabitEthernet1/0/1]
vlan mapping uni range 2 to 4 translated-vlan 10

\[SwitchE-Ten-GigabitEthernet1/0/1]
quit

#### Verifying the configuration

\# Verify VLAN mapping information on Switch
E.

\[SwitchE] display vlan mapping

Interface Ten-GigabitEthernet1/0/1: 

  Outer VLAN    Inner VLAN   
Translated Outer VLAN    Translated Inner VLAN 

  2-4           N/A          
10                       N/A

### Example: Configuring one-to-two and two-to-two VLAN mapping

#### Network configuration

As shown in [Figure 12](#_Ref332811020):

·     Two VPN A branches, Site 1 and Site 2, are in
VLAN 5 and VLAN 6, respectively.

·     The two sites use different VPN access services from
different service providers, SP 1 and SP 2\.

·     SP 1 assigns VLAN 100 to Site 1 and Site 2\. SP 2
assigns VLAN 200 to Site 1 and Site 2\.

Configure one-to-two VLAN mappings and
two-to-two VLAN mappings to enable the two branches to communicate across
networks SP 1 and SP 2\.

Figure 12 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11704399_x_Img_x_png_11_2215981_294551_0.png)

‌

#### Procedure

**1\.**Configure PE 1:

\# Create VLANs 5 and 100\.

\<PE1\> system-view

\[PE1] vlan 5

\[PE1-vlan5] quit

\[PE1] vlan 100

\[PE1-vlan100] quit

\# Configure a one-to-two VLAN mapping on the
customer-side port (Ten-GigabitEthernet 1/0/1) to add SVLAN tag
100 to packets from VLAN 5\.

\[PE1] interface ten-gigabitethernet 1/0/1

\[PE1-Ten-GigabitEthernet1/0/1] vlan mapping nest
single 5 nested-vlan 100

\# Configure Ten-GigabitEthernet 1/0/1 as a hybrid port.

\[PE1-Ten-GigabitEthernet1/0/1] port link-type
hybrid

\# Assign Ten-GigabitEthernet 1/0/1 to VLAN 5 as a
tagged member.

\[PE1-Ten-GigabitEthernet1/0/1] port hybrid vlan 5
tagged

\# Assign Ten-GigabitEthernet 1/0/1 to VLAN 100 as an
untagged member.

\[PE1-Ten-GigabitEthernet1/0/1] port hybrid vlan
100 untagged

\[PE1-Ten-GigabitEthernet1/0/1] quit

\# Configure the network-side port (Ten-GigabitEthernet 1/0/2) as a trunk port.

\[PE1] interface ten-gigabitethernet 1/0/2

\[PE1-Ten-GigabitEthernet1/0/2] port link-type
trunk

\# Assign Ten-GigabitEthernet 1/0/2 to VLAN 100\.

\[PE1-Ten-GigabitEthernet1/0/2] port trunk permit
vlan 100

\[PE1-Ten-GigabitEthernet1/0/2] quit

**2\.**Configure PE 2:

\# Create VLAN 100\.

\<PE2\> system-view

\[PE2] vlan 100

\[PE2-vlan100] quit

\# Configure Ten-GigabitEthernet 1/0/1 as a trunk port.

\[PE2] interface ten-gigabitethernet 1/0/1

\[PE2-Ten-GigabitEthernet1/0/1] port link-type
trunk

\# Assign Ten-GigabitEthernet 1/0/1 to VLAN 100\.

\[PE2-Ten-GigabitEthernet1/0/1] port trunk permit
vlan 100

\[PE2-Ten-GigabitEthernet1/0/1] quit

\# Configure Ten-GigabitEthernet 1/0/2 as a trunk port.

\[PE2] interface ten-gigabitethernet 1/0/2

\[PE2-Ten-GigabitEthernet1/0/2] port link-type
trunk

\# Assign Ten-GigabitEthernet 1/0/2 to VLAN 100\.

\[PE2-Ten-GigabitEthernet1/0/2] port trunk permit
vlan 100

\[PE2-Ten-GigabitEthernet1/0/2] quit

**3\.**Configure PE 3:

\# Create VLANs 5, 6, 100, and 200\.

\<PE3\> system-view

\[PE3] vlan 5 to 6

\[PE3] vlan 100

\[PE3-vlan100] quit

\[PE3] vlan 200

\[PE3-vlan200] quit

\# Configure Ten-GigabitEthernet 1/0/1 as a trunk port.

\[PE3] interface ten-gigabitethernet 1/0/1

\[PE3-Ten-GigabitEthernet1/0/1] port link-type
trunk

\# Assign Ten-GigabitEthernet 1/0/1 to VLANs 100 and
200\.

\[PE3-Ten-GigabitEthernet1/0/1] port trunk permit
vlan 100 200

\# Configure a two-to-two VLAN mapping on Ten-GigabitEthernet 1/0/1 to map SVLAN 100
and CVLAN 5 to SVLAN 200 and CVLAN 6\.

\[PE3-Ten-GigabitEthernet1/0/1] vlan mapping
tunnel 100 5 translated-vlan 200 6

\[PE3-Ten-GigabitEthernet1/0/1] quit

\# Configure Ten-GigabitEthernet 1/0/2 as a trunk port.

\[PE3] interface ten-gigabitethernet 1/0/2

\[PE3-Ten-GigabitEthernet1/0/2] port link-type
trunk

\# Assign Ten-GigabitEthernet 1/0/2 to VLAN 200\.

\[PE3-Ten-GigabitEthernet1/0/2] port trunk permit
vlan 200

\[PE3-Ten-GigabitEthernet1/0/2] quit

**4\.**Configure PE 4:

\# Create VLANs 6 and 200\.

\<PE4\> system-view

\[PE4] vlan 6

\[PE4-vlan6] quit

\[PE4] vlan 200

\[PE4-vlan200] quit

\# Configure the network-side port (Ten-GigabitEthernet 1/0/1) as a trunk port.

\[PE4] interface ten-gigabitethernet 1/0/1

\[PE4-Ten-GigabitEthernet1/0/1] port link-type
trunk

\# Assign Ten-GigabitEthernet 1/0/1 to VLAN 200\.

\[PE4-Ten-GigabitEthernet1/0/1] port trunk permit
vlan 200

\[PE4-Ten-GigabitEthernet1/0/1] quit

\# Configure the customer-side port (Ten-GigabitEthernet 1/0/2) as a hybrid port.

\[PE4] interface ten-gigabitethernet 1/0/2

\[PE4-Ten-GigabitEthernet1/0/2] port link-type
hybrid

\# Assign Ten-GigabitEthernet 1/0/2 to VLAN 6 as a
tagged member.

\[PE4-Ten-GigabitEthernet1/0/2] port hybrid vlan 6
tagged

\# Assign Ten-GigabitEthernet 1/0/2 to VLAN 200 as an
untagged member.

\[PE4-Ten-GigabitEthernet1/0/2] port hybrid vlan
200 untagged

\# Configure a one-to-two VLAN mapping on Ten-GigabitEthernet 1/0/2 to add SVLAN tag
200 to packets from VLAN 6\.

\[PE4-Ten-GigabitEthernet1/0/2] vlan mapping nest
single 6 nested-vlan 200

\[PE4-Ten-GigabitEthernet1/0/2] quit

#### Verifying the configuration

\# Verify VLAN mapping information on PE 1\.

\[PE1] display vlan mapping

Interface Ten-GigabitEthernet1/0/1:

  Outer VLAN    Inner VLAN    Translated
Outer VLAN    Translated Inner VLAN

  5             N/A           100                     
5

\# Verify VLAN mapping information on PE 3\.

\[PE3] display vlan mapping

Interface Ten-GigabitEthernet1/0/1:

  Outer VLAN    Inner VLAN    Translated
Outer VLAN    Translated Inner VLAN

  100           5             200                     
6

\# Verify VLAN mapping information on PE 4\.

\[PE4] display vlan mapping

Interface Ten-GigabitEthernet1/0/2:

  Outer VLAN    Inner VLAN    Translated
Outer VLAN    Translated Inner VLAN

  6             N/A           200                     
6

