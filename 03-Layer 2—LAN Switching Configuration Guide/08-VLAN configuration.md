
# Configuring VLANs

## About VLANs

The Virtual Local Area Network (VLAN)
technology divides a physical LAN into multiple logical LANs. It has the
following benefits:

·     Security—Hosts in the same VLAN can communicate with one another at Layer 2,
but they are isolated from hosts in other VLANs at Layer 2\.

·     Broadcast traffic isolation—Each VLAN is a broadcast domain that limits the transmission of
broadcast packets.

·     Flexibility—A VLAN can be logically divided on a workgroup basis. Hosts in the
same workgroup can be assigned to the same VLAN, regardless of their physical
locations.

### VLAN frame encapsulation

To identify Ethernet frames from different
VLANs, IEEE 802.1Q inserts a four-byte VLAN tag between the destination and
source MAC address (DA\&SA) field and the Type field.

Figure 1 VLAN tag placement and format

![](https://resource.h3c.com/en/202407/12/20240712_11704369_x_Img_x_png_0_2215978_294551_0.png)

‌

A VLAN tag includes the following fields:

·     TPID—16-bit tag protocol identifier that indicates whether a frame is
VLAN-tagged. By default, the hexadecimal TPID value 8100 identifies a
VLAN-tagged frame. A device vendor can set the TPID to a different value. For
compatibility with a neighbor device, set the TPID value on the device to be
the same as the neighbor device.For more information about setting the TPID
value, see QinQ commands in Layer 2—LAN Switching
Command Reference.#

·      

·     Priority—3-bit long, identifies the 802.1p priority of the frame. For more
information, see ACL and QoS Configuration Guide.

·     CFI—1-bit long canonical format indicator that indicates whether the
MAC addresses are encapsulated in the standard format when packets are
transmitted across different media. Available values include:

¡     0 (default)—The MAC addresses are
encapsulated in the standard format.

¡     1—The MAC addresses are encapsulated
in a non-standard format.

This field is always set to 0 for
Ethernet.

·     VLAN ID—12-bit long, identifies the VLAN to which the frame belongs. The
VLAN ID range is 0 to 4095\. VLAN IDs 0 and 4095 are reserved, and VLAN IDs 1 to
4094 are user configurable.

The way a network device handles an
incoming frame depends on whether the frame has a VLAN tag and the value of the
VLAN tag (if any).

Ethernet supports encapsulation formats
Ethernet II, 802.3/802.2 LLC, 802.3/802.2 SNAP, and 802.3 raw. The Ethernet II
encapsulation format is used here. For information about the VLAN tag fields in
other frame encapsulation formats, see related protocols and standards.

For a frame that has multiple VLAN tags,
the device handles it according to its outermost VLAN tag and transmits its
inner VLAN tags as the payload.

### VLAN types

The following VLAN types are available:

·     Port-based VLAN.

·     MAC-based VLAN.

·     IP subnet-based VLAN.

·     Protocol-based VLAN.

If all these types of VLANs are configured
on a port, the port processes packets in the following descending order of
priority by default:

·     MAC-based VLAN.

·     IP subnet-based VLAN.

·     Protocol-based VLAN.

·     Port-based VLAN.

### Port-based VLANs

Port-based VLANs group VLAN members by
port. A port forwards packets from a VLAN only after it is assigned to the
VLAN.

#### Port link type

You can set the link type of a port to
access, trunk, or hybrid. The port link type determines whether the port can be
assigned to multiple VLANs. The link types use the following VLAN tag handling
methods:

·     Access—An access port can forward packets only from one VLAN and send
these packets untagged. An access port is typically used in the following
conditions:

¡     Connecting
to a terminal device that does not support VLAN packets.

¡     In scenarios
that do not distinguish VLANs.

·     Trunk—A trunk port can forward packets from multiple VLANs. Except
packets from the port VLAN ID (PVID), packets sent out of a trunk port are
VLAN-tagged. Ports connecting network devices are typically configured as trunk
ports.

·     Hybrid—A hybrid port can forward packets from multiple VLANs. The tagging
status of the packets forwarded by a hybrid port depends on the port
configuration. In one-to-two VLAN mapping, hybrid ports are used to remove
SVLAN tags for downlink traffic. For more information about one-to-two VLAN
mapping, see "Configuring VLAN mapping."

#### PVID

The PVID identifies the default VLAN of a
port. Untagged packets received on a port are considered as the packets from
the port PVID.

An access port can join only one VLAN. The
VLAN to which the access port belongs is the PVID of the port. A trunk or
hybrid port supports multiple VLANs and the PVID configuration.

#### How ports of different link types handle frames

| Actions | Access | Trunk | Hybrid | |
| --- | --- | --- | --- | --- |
| In the inbound direction for an untagged frame | Tags the frame with the PVID tag. | ·     If the PVID is permitted on the port, tags the frame with the PVID tag.  ·     If not, drops the frame. | | || In the inbound direction for a tagged frame | ·     Receives the frame if its VLAN ID is the same as the PVID.  ·     Drops the frame if its VLAN ID is different from the PVID. | ·     Receives the frame if its VLAN is permitted on the port.  ·     Drops the frame if its VLAN is not permitted on the port. | | || In the outbound direction | Removes the VLAN tag and sends the frame. | ·     Removes the tag and sends the frame if the frame carries the PVID tag and the port belongs to the PVID.  ·     Sends the frame without removing the tag if its VLAN is carried on the port but is different from the PVID. | | Sends the frame if its VLAN is permitted on the port. The tagging status of the frame depends on the port hybrid vlan command configuration. ||  |  |  |  |  |




‌

### MAC-based VLANs

The MAC-based VLAN feature assigns hosts to
a VLAN based on their MAC addresses. This feature is also called user-based
VLAN because VLAN configuration remains the same regardless of a user's
physical location.

#### Static MAC-based VLAN assignment

Use static MAC-based VLAN assignment in networks
that have a small number of VLAN users. To configure static MAC-based VLAN
assignment on a port, perform the following tasks:

**1\.**Create MAC-to-VLAN entries.

**2\.**Enable the MAC-based VLAN feature on the
port.

**3\.**Assign the port to the MAC-based VLAN.

A port configured with static MAC-based
VLAN assignment processes a received frame as follows before sending the frame
out:

·     For an untagged frame, the port determines its
VLAN ID in the following workflow:

**a.**The port first performs a fuzzy match as
follows:

\-     Searches for the MAC-to-VLAN entries whose masks are not all Fs.

\-     Performs a logical AND operation on the source MAC address and each
of these masks.

If an AND operation result matches the
MAC address in a MAC-to-VLAN entry, the port tags the frame with the VLAN ID
specific to this entry.

**b.**If the fuzzy match fails, the port performs
an exact match. It searches for MAC-to-VLAN entries whose masks are all Fs. If
the source MAC address of the frame exactly matches the MAC address of a
MAC-to-VLAN entry, the port tags the frame with the VLAN ID specific to this
entry.

**c.**If no matching VLAN ID is found, the port
determines the VLAN for the packet by using the following matching order:

\-     IP subnet-based VLAN.

\-     Protocol-based VLAN.

\-     Port-based VLAN.

When a match is found, the port tags the
packet with the matching VLAN ID.

·     For a tagged frame, the port determines whether
the VLAN ID of the frame is permitted on the port.

¡     If
the VLAN ID of the frame is permitted on the port, the port forwards the frame.

¡     If
the VLAN ID of the frame is not permitted on the port, the port drops the
frame.

#### Dynamic MAC-based VLAN assignment

When you cannot determine the target
MAC-based VLANs of a port, use dynamic MAC-based VLAN assignment on the port.
To use dynamic MAC-based VLAN assignment, perform the following tasks:

**1\.**Create MAC-to-VLAN entries.

**2\.**Enable the MAC-based VLAN feature on the
port.

**3\.**Enable dynamic MAC-based VLAN assignment on
the port.

Dynamic MAC-based VLAN assignment uses the
following workflow, as shown in [Figure 2](#_Ref454964269):

**1\.**When a port receives a frame, it first
determines whether the frame is tagged.

¡     If the
frame is tagged, the port gets the source MAC address of the frame.

¡     If the
frame is untagged, the port selects a VLAN for the frame by using the following
matching order:

\-     MAC-based VLAN (fuzzy and exact MAC address match).

\-     IP subnet-based VLAN.

\-     Protocol-based VLAN.

\-     Port-based VLAN.

After tagging the frame with the selected
VLAN, the port gets the source MAC address of the frame.

**2\.**The port uses the source MAC address and
VLAN of the frame to match the MAC-to VLAN entries.

¡     If
the source MAC address of the frame exactly matches the MAC address in a MAC-to-VLAN
entry, the port checks whether the VLAN ID of the frame matches the VLAN in the
entry.

\-     If the two VLAN IDs match, the port joins the VLAN and forwards the
frame.

\-     If the two VLAN IDs do not match, the port drops the frame.

¡     If
the source MAC address of the frame does not exactly match any MAC addresses in
MAC-to-VLAN entries, the port checks whether the VLAN ID of the frame is its
PVID.

\-     If the VLAN ID of the frame is the PVID of the port, the port
determines whether it allows the PVID.

If the PVID is allowed, the port forwards
the frame within the PVID. If the PVID is not allowed, the port drops the
frame.

\-     If the VLAN ID of the frame is not the PVID of the port, the port
determines whether the VLAN ID is the primary VLAN ID and the port PVID is a
secondary VLAN ID. If yes, the port forwards the frame. Otherwise, the port
drops the frame. 

Figure 2 Flowchart for processing a frame in
dynamic MAC-based VLAN assignment

![](https://resource.h3c.com/en/202407/12/20240712_11704370_x_Img_x_png_1_2215978_294551_0.png)

‌

#### Server-assigned MAC-based VLAN

Use this feature with access authentication,
such as MAC-based 802.1X authentication, to implement secure and flexible
terminal access.

To implement server-assigned MAC-based VLAN,
perform the following tasks:

**1\.**Configure the server-assigned MAC-based VLAN
feature on the access device.

**2\.**Configure username-to-VLAN entries on the
access authentication server.

When a user passes authentication of the
access authentication server, the server assigns the authorization VLAN
information for the user to the device. The device then performs the following
operations:

**1\.**Generates a MAC-to-VLAN entry by using the
source MAC address of the user packet and the authorization VLAN information.
The authorization VLAN is a MAC-based VLAN.

The generated MAC-to-VLAN entry cannot
conflict with the existing static MAC-to-VLAN entries. If a confliction exists,
the dynamic MAC-to-VLAN entry cannot be generated.

**2\.**Assigns the port that connects the user to
the MAC-based VLAN.

When the user goes offline, the device
automatically deletes the MAC-to-VLAN entry and removes the port from the MAC-based
VLAN. For more information about 802.1X and MAC authentication, see Security Configuration Guide.

### IP subnet-based VLANs

The IP subnet-based VLAN feature assigns untagged
packets to VLANs based on their source IP addresses and subnet masks.

Use this feature when untagged packets from
an IP subnet or IP address must be transmitted in a VLAN.

### Protocol-based VLANs

The protocol-based VLAN feature assigns
inbound packets to different VLANs based on their protocol types and encapsulation
formats. The protocols available for VLAN assignment include IP, IPX, and AT.
The encapsulation formats include Ethernet II, 802.3 raw, 802.2 LLC, and 802.2
SNAP.

This feature associates the available
network service types with VLANs and facilitates network management and
maintenance.

### Layer 3 communication between VLANs

Hosts of different VLANs use VLAN
interfaces to communicate at Layer 3\. VLAN interfaces are virtual interfaces
that do not exist as physical entities on devices. For each VLAN, you can
create one VLAN interface and assign an IP address to it. The VLAN interface acts
as the gateway of the VLAN to forward packets at Layer 3\. When the IP addresses
of multiple VLAN interfaces are on different subnets, you must configure IP
routing protocols to enable Layer 3 communication between these IP addresses.
Then, the devices can forward packets at Layer 3 across subnets between
different VLANs. For more information about IP routing protocols, see Layer 3—IP Routing Configuration Guide.

### Protocols and standards

IEEE 802.1Q, IEEE
Standard for Local and Metropolitan Area Networks: Virtual Bridged Local Area
Networks

 

## Configuring a VLAN

### Restrictions and guidelines

·     As the system default VLAN, VLAN 1 cannot be
created or deleted.

·     Before you delete a dynamic VLAN or a VLAN
locked by an application, you must first remove the configuration from the
VLAN.

### Creating VLANs

**1\.**Enter system view.

system-view 

**2\.**Create one or multiple VLANs.

¡     Create
a VLAN and enter its view.

vlan vlan-id 

¡     Create
multiple VLANs and enter VLAN view.

Create VLANs.

vlan { vlan-id-list \| all }

Enter VLAN view.

vlan vlan-id 

By default, only the system default VLAN
(VLAN 1\) exists. 

**3\.**(Optional.) Set a name for the VLAN.

name text 

By default, the name of a VLAN is VLAN vlan-id. The vlan-id argument
specifies the VLAN ID in a four-digit format. If the VLAN ID has fewer than
four digits, leading zeros are added. For example, the name of VLAN 100 is VLAN 0100. 

**4\.**(Optional.) Configure the description for
the VLAN.

description text 

By default, the description of a VLAN is VLAN vlan-id. The vlan-id argument
specifies the VLAN ID in a four-digit format. If the VLAN ID has fewer than
four digits, leading zeros are added. For example, the default description of
VLAN 100 is VLAN 0100.

## Configuring port-based VLANs

### Restrictions and guidelines for port-based VLANs

·     When you use the undo vlan command to delete the PVID of a port, either of the following
events occurs depending on the port link type:

¡     For
an access port, the PVID of the port changes to VLAN 1\.

¡     For a
hybrid or trunk port, the PVID setting of the port does not change.

You can use a nonexistent VLAN as the
PVID for a hybrid or trunk port, but not for an access port.

·     As a best practice, set the same PVID for a local
port and its peer.

·     To prevent a port from dropping untagged packets
or PVID-tagged packets, assign the port to its PVID.

·     When a member port joins or leaves a Layer 2 aggregation
group, follow these restrictions and guidelines for VLAN configuration on the
member port:

¡     Only
a member port with the default VLAN configuration (access port assigned to VLAN
1\) can be assigned to a Layer 2 aggregation group. 

¡     After
a member port is assigned to a Layer 2 aggregation group, the VLAN
configuration on the Layer 2 aggregate interface will be synchronized to the
member port. 

¡     After
a member port leaves an aggregation group, the VLAN configuration will be
restored to the default on the member port. 

### Assigning an access port to a VLAN

#### About this task

You can assign an access port to a VLAN in
VLAN view or interface view.

#### Assigning one or multiple access ports to a VLAN in VLAN view

**1\.**Enter system view.

system-view 

**2\.**Enter VLAN view.

vlan vlan-id 

**3\.**Assign one or multiple access ports to the
VLAN.

port interface-list 

By default, all ports belong to VLAN 1\.

#### Assigning an access port to a VLAN in interface view

**1\.**Enter system view.

system-view 

**2\.**Enter interface view.

¡     Enter Layer 2 Ethernet interface view.

interface interface-type
interface-number 

¡     Enter Layer 2 aggregate interface view.

interface bridge-aggregation interface-number 

**3\.**Set the port link type to access.

port link-type access 

By default, all ports are access ports.

**4\.**Assign the access port to a VLAN.

port access vlan vlan-id 

By default, all access ports belong to
VLAN 1\.

### Assigning a trunk port to a VLAN

#### About this task

A trunk port supports multiple VLANs. You
can assign it to a VLAN in interface view.

#### Restrictions and guidelines

To change the link type of a port from
trunk to hybrid, set the link type to access first.

To enable a trunk port to transmit packets
from its PVID, you must assign the trunk port to the PVID by using the port trunk
permit vlan command.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter interface view.

¡     Enter Layer 2 Ethernet interface view.

interface interface-type
interface-number

¡     Enter Layer 2 aggregate interface view.

interface bridge-aggregation interface-number

**3\.**Set the port link type to trunk.

port link-type trunk

By default, all ports are access ports.

**4\.**Assign the trunk port to the specified
VLANs.

port trunk permit vlan { vlan-id-list \| all }

By default, a trunk port permits only VLAN
1\.

**5\.**(Optional.) Set the PVID for the trunk port.

port trunk pvid vlan vlan-id

The default setting is VLAN 1\.

### Assigning a hybrid port to a VLAN

#### About this task

A hybrid port supports multiple VLANs. You
can assign it to the specified VLANs in interface view. Make sure the VLANs have
been created.

#### Restrictions and guidelines

To change the link type of a port from trunk
to hybrid, set the link type to access first.

To enable a hybrid port to transmit packets
from its PVID, you must assign the hybrid port to the PVID by using the port hybrid
vlan command.

#### Procedure

**1\.**Enter system view.

system-view 

**2\.**Enter interface view.

¡     Enter Layer 2 Ethernet interface view.

interface interface-type
interface-number 

¡     Enter Layer 2 aggregate interface view.

interface bridge-aggregation interface-number 

**3\.**Set the port link type to hybrid.

port link-type hybrid 

By default, all ports are access ports.

**4\.**Assign the hybrid port to the specified
VLANs.

port hybrid vlan vlan-id-list { tagged \| untagged }

By default, the hybrid port is an
untagged member of the VLAN to which the port belongs when its link type is access. 

**5\.**(Optional.) Set the PVID for the hybrid
port.

port hybrid pvid vlan vlan-id 

By default, the PVID of a hybrid port is
the ID of the VLAN to which the port belongs when its link type is access.

## Configuring MAC-based VLANs

### Restrictions and guidelines for MAC-based VLANs

·     MAC-based VLANs are available only on hybrid ports.

·     Do not configure a VLAN as both a super VLAN and
a MAC-based VLAN.

·     Do not configure MAC-based VLANstogether with mapping an
Ethernet service instance to a VSI on a Layer 2 Ethernet interface or Layer
2 aggregate interface. Do not configure MAC-based VLANs on a Layer 2 Ethernet
interface or Layer 2 aggregate interface that acts as the source interface of a
VXLAN tunnel. For information about VXLAN and VSIs, see VXLAN
Configuration Guide. 

### Configuring static MAC-based VLAN assignment

**1\.**Enter system view.

system-view 

**2\.**Create a MAC-to-VLAN entry.

mac-vlan mac-address mac-address \[ mask mac-mask ] vlan vlan-id \[ dot1p priority ]

By default, no MAC-to-VLAN entries exist.

**3\.**Enter interface view.

¡     Enter Layer 2 Ethernet interface view.

interface interface-type
interface-number 

¡     Enter Layer 2 aggregate interface view.

interface bridge-aggregation interface-number

**4\.**Set the port link type to hybrid.

port link-type hybrid 

By default, all ports are access ports.

**5\.**Assign the hybrid port to the MAC-based
VLANs.

port hybrid vlan vlan-id-list { tagged \| untagged }

By default, a hybrid port is an untagged
member of the VLAN to which the port belongs when its link type is access. 

**6\.**Enable the MAC-based VLAN feature.

mac-vlan enable 

By default, this feature is disabled.

**7\.**(Optional.) Set the VLAN matching order. 

vlan precedence { mac-vlan \| ip-subnet-vlan }

By default, the system assigns VLANs
based on the MAC address preferentially when both the MAC-based VLAN and IP
subnet-based VLAN are configured on a port. 

### Configuring dynamic MAC-based VLAN assignment

#### About this task

For successful dynamic MAC-based VLAN
assignment, use static VLANs when you create MAC-to-VLAN entries.

When a port joins a VLAN specified in the
MAC-to-VLAN entry, one of the following events occurs depending on the port
configuration:

·     If the port has not been configured to allow
packets from the VLAN to pass through, the port joins the VLAN as an untagged
member.

·     If the port has been configured to allow packets
from the VLAN to pass through, the port configuration remains the same.

The 802.1p priority of the VLAN in a
MAC-to-VLAN entry determines the transmission priority of the matching packets.

#### Restrictions and guidelines

·     If you configure both static and dynamic
MAC-based VLAN assignments on a port, dynamic MAC-based VLAN assignment takes
effect.

·     As a best practice to ensure correct operation
of 802.1X and MAC authentication, do not use dynamic MAC-based VLAN assignment
with 802.1X or MAC authentication.

·     As a best practice, do not both configure dynamic
MAC-based VLAN assignment and disable MAC address learning on a port. If the
two features are configured together on a port, the port forwards only packets exactly
matching the MAC-to-VLAN entries and drops inexactly matching packets. 

·     As a best practice, do not configure both dynamic
MAC-based VLAN assignment and the MAC learning limit on a port. 

If the two features are configured
together on a port and the port learns the configured maximum number of MAC
address entries, the port processes packets as follows: 

¡     Forwards
only packets matching the MAC address entries learnt by the port.

¡     Drops
unmatching packets.

·     As a best practice, do not use dynamic MAC-based
VLAN assignment with MSTP. In MSTP mode, if a port is blocked in the MSTI of its
target VLAN, the port drops the received packets instead of delivering them to
the CPU. As a result, the port will not be dynamically assigned to the target VLAN.

·     As a best practice, do not use dynamic MAC-based
VLAN assignment with PVST. In PVST mode, if the target VLAN of a port is not permitted
on the port, the port is placed in blocked state. The port drops the received
packets instead of delivering them to the CPU. As a result, the port will not
be dynamically assigned to the target VLAN.

·     As a best practice, do not configure both
dynamic MAC-based VLAN assignment and automatic voice VLAN assignment mode on a
port. They can have a negative impact on each other.

#### Procedure

**1\.**Enter system view.

system-view 

**2\.**Create a MAC-to-VLAN entry.

mac-vlan mac-address mac-address vlan vlan-id \[ dot1p priority ]

By default, no MAC-to-VLAN entries exist.

**3\.**Enter interface view.

¡     Enter Layer 2 Ethernet interface view.

interface interface-type
interface-number 

**4\.**Set the port link type to hybrid.

port link-type hybrid 

By default, all ports are access ports.

**5\.**Enable the MAC-based VLAN feature.

mac-vlan enable 

By default, MAC-based VLAN is disabled.

**6\.**Enable dynamic MAC-based VLAN assignment.

mac-vlan trigger enable


By default, dynamic MAC-based VLAN
assignment is disabled.

The VLAN assignment for a port is
triggered only when the source MAC address of its receiving packet exactly
matches the MAC address in a MAC-to-VLAN entry.

**7\.**(Optional.) Configure the system to assign
VLANs based on the MAC address preferentially.

vlan precedence mac-vlan 

By default, the system assigns VLANs
based on the MAC address preferentially when both the MAC-based VLAN and IP
subnet-based VLAN are configured on a port. 

**8\.**(Optional.) Disable the port from forwarding
packets that fail the exact MAC address match in its PVID.

port pvid forbidden 

By default, when a port receives packets
whose source MAC addresses fail the exact match, the port forwards them in its PVID.


### Configuring server-assigned MAC-based VLAN

**1\.**Enter system view.

system-view 

**2\.**Enter interface view.

¡     Enter Layer 2 Ethernet interface view.

interface interface-type
interface-number 

¡     Enter Layer 2 aggregate interface view.

interface bridge-aggregation interface-number

**3\.**Set the port link type to hybrid.

port link-type hybrid 

By default, all ports are access ports.

**4\.**Assign the hybrid port to the MAC-based
VLANs.

port hybrid vlan vlan-id-list { tagged \| untagged }

By default, a hybrid port is an untagged
member of the VLAN to which the port belongs when its link type is access. 

**5\.**Enable the MAC-based VLAN feature.

mac-vlan enable 

By default, MAC-based VLAN is disabled.

**6\.**(Optional.) Set the VLAN matching order. 

vlan precedence { mac-vlan \| ip-subnet-vlan }

By default, the system assigns VLANs
based on the MAC address preferentially when both the MAC-based VLAN and IP
subnet-based VLAN are configured on a port. 

**7\.**Configure 802.1X or MAC authentication.

For more information, see Security Command Reference.

## Configuring IP subnet-based VLANs

#### Restrictions and guidelines

This feature is available only on hybrid
ports, and it processes only untagged packets.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter VLAN view.

vlan vlan-id

**3\.**Associate the VLAN with an IP subnet or IP
address.

ip-subnet-vlan \[ ip-subnet-index ] ip ip-address \[ mask ]

By default, a VLAN is not associated with
an IP subnet or IP address.

A multicast subnet or a multicast address
cannot be associated with a VLAN.

**4\.**Return to system view.

quit

**5\.**Enter interface view.

¡     Enter
Layer 2 Ethernet interface view.

interface interface-type interface-number

¡     Enter
Layer 2 aggregate interface view.

interface bridge-aggregation interface-number

**6\.**Set the port link type to hybrid.

port link-type hybrid

By default, all ports are access ports.

**7\.**Assign the hybrid port to the specified IP
subnet-based VLANs.

port hybrid vlan vlan-id-list { tagged \| untagged }

By default, a hybrid port is an untagged
member of the VLAN to which the port belongs when its link type is access.

**8\.**Associate the hybrid port with the specified
IP subnet-based VLAN.

port hybrid ip-subnet-vlan vlan vlan-id

By default, a hybrid port is not associated
with a subnet-based VLAN.

## Configuring protocol-based VLANs

#### About this task

A protocol-based VLAN has one or multiple protocol
templates. A protocol template defines a protocol type and an encapsulation
format as the match criteria to match inbound packets. Each protocol template
has a unique index in the protocol-based VLAN. All protocol templates in a
protocol-based VLAN have the same VLAN ID.

For a port to assign inbound packets to
protocol-based VLANs, perform the following tasks:

·     Assign the port to the protocol-based VLANs.

·     Associate the port with the protocol templates
of the protocol-based VLANs.

When an untagged packet arrives at the port,
the port processes the packet as follows:

·     If the protocol type and encapsulation format in
the packet match a protocol template, the port tags the packet with the VLAN
tag specific to the protocol template.

·     If no protocol templates are matched, the port tags
the packet with its PVID.

#### Restrictions and guidelines

The voice VLAN in automatic mode processes
only tagged voice traffic. Do not configure a VLAN as both a protocol-based
VLAN and a voice VLAN. 

#### Procedure

**1\.**Enter system view.

system-view 

**2\.**Enter VLAN view.

vlan vlan-id 

**3\.**Associate the VLAN with a protocol template.

protocol-vlan \[ protocol-index ] { at \| ipv4 \| ipv6 \| ipx { ethernetii \| llc \| raw \| snap } \| mode { ethernetii etype etype-id \| llc { dsap dsap-id \[ ssap ssap-id ] \| ssap ssap-id } \| snap etype etype-id } }

By default, a VLAN is not associated with
a protocol template.

**4\.**Exit VLAN view.

quit 

**5\.**Enter interface view.

¡     Enter
Layer 2 Ethernet interface view.

interface interface-type interface-number 

¡     Enter
Layer 2 aggregate interface view.

interface bridge-aggregation interface-number 

**6\.**Set the port link type to hybrid.

port link-type hybrid 

By default, all ports are access ports.

**7\.**Assign the hybrid port to the specified
protocol-based VLANs.

port hybrid vlan vlan-id-list { tagged \| untagged }

By default, a hybrid port is an untagged
member of the VLAN to which the port belongs when its link type is access.

**8\.**Associate the hybrid port with the specified
protocol-based VLAN.

port hybrid protocol-vlan vlan vlan-id { protocol-index \[ to protocol-end ] \| all }

By default, a hybrid port is not
associated with a protocol-based VLAN.

## Configuring a VLAN group

#### About this task

A VLAN group includes a set of VLANs. 

On an authentication server, a VLAN group
name represents a group of authorization VLANs. When an 802.1X or MAC
authentication user passes authentication, the authentication server assigns a
VLAN group name to the device. The device then uses the received VLAN group
name to match the locally configured VLAN group names. If a match is found, the
device selects a VLAN from the group and assigns the VLAN to the user. For more
information about 802.1X and MAC authentication, see Security
Configuration Guide.

#### Procedure

**1\.**Enter system view.

system-view 

**2\.**Create a VLAN group and enter its view.

vlan-group group-name 

**3\.**Add VLANs to the VLAN group.

vlan-list vlan-id-list


By default, no VLANs exist in a VLAN
group.

You can add multiple VLAN lists to a VLAN
group.

## Configuring VLAN interfaces

### Restrictions and guidelines

·     You cannot create VLAN interfaces for sub-VLANs.
For more information about sub-VLANs, see "[Configuring super VLANs](#_Ref365393688)."

·     You cannot create VLAN interfaces for secondary
VLANs that have the following characteristics:

¡     Associated
with the same primary VLAN.

¡     Enabled
with Layer 3 communication in VLAN interface view of the primary VLAN interface.

For more information about secondary
VLANs, see "[Configuring private VLAN](#_Ref365393735)." 

·     A VLAN interface can come up only when one or
more Ethernet interfaces in the corresponding VLAN are up. 

### VLAN interfaces configuration tasks at a glance

To configure VLAN interfaces, perform the
following tasks:

**1\.**[Creating a VLAN interface](#_Ref472015761)

**2\.**(Optional.) [Specifying a traffic processing slot for the
VLAN interface](#_Ref472015865)

**3\.**(Optional.) [Restoring the default settings for the VLAN
interface](#_Ref472015928)

### Prerequisites

Before you create a VLAN interface for a
VLAN, create the VLAN first.

### Creating a VLAN interface

**1\.**Enter system view.

system-view 

**2\.**Create a VLAN interface and enter its view.

interface vlan-interface interface-number 

**3\.**Assign an IP address to the VLAN interface.

ip address ip-address { mask \| mask-length } \[ sub ]

By default, no IP address is assigned to
a VLAN interface.

**4\.**(Optional.) Configure the description for
the VLAN interface.

description text 

The default setting is the VLAN interface
name. For example, Vlan-interface1
Interface.

**5\.**(Optional.) Set the MTU for the VLAN
interface. 

mtu size 

By default, the MTU of a VLAN interface is
1500 bytes. 

**6\.**(Optional.) Set a MAC address for the VLAN
interface.

mac-address mac-address


By default, no MAC address is set for a
VLAN interface. 

**7\.**(Optional.) Set the expected bandwidth for
the interface.

bandwidth bandwidth-value 

By default, the expected bandwidth (in
kbps) is the interface baud rate divided by 1000\.

**8\.**Bring up the VLAN interface.

undo shutdown 

By default, a VLAN interface is not
manually shut down. The status of the VLAN interface depends on the status of
member ports of the VLAN.

### Specifying a traffic processing slot for the VLAN interface

#### About this task

Specify a traffic processing slot for a
VLAN interface if all traffic on the VLAN interface must be processed on the
same slot.

#### Procedure

**1\.**Enter system view.

system-view 

**2\.**Enter a VLAN interface view.

interface vlan-interface interface-number 

**3\.**Specify a traffic processing slot for the
VLAN interface.

service slot slot-number

By default, no traffic processing slot is
specified for the VLAN interface. 

### Restoring the default settings for the VLAN interface

#### Restrictions and guidelines

This feature might fail to restore the
default settings for some commands for reasons such as command dependencies or
system restrictions. Use the display this
command in interface view to identify these commands, and then use their undo forms or follow the command reference to restore their default
settings. If your restoration attempt still fails, follow the error message
instructions to resolve the problem.

#### Procedure

**1\.**Enter system view.

system-view 

**2\.**Enter a VLAN interface view.

interface vlan-interface interface-number 

**3\.**Restore the default settings for the VLAN
interface. 

default

‌

| CAUTION | CAUTION:  This feature might interrupt ongoing network services. Make sure you are fully aware of the impact of this feature when you use it on a live network. |
| --- | --- |

‌

## Configuring the 802.1p priority for control packets sent by a device

#### About this task

By default, the 802.1p priority is 6 for
control packets sent by a device. However, some devices will drop or not
process packets with 802.1p priority 6, which affects the operation of
protocols in the network. To resolve this problem, configure the 802.1p
priority for control packets sent by a device.

#### Restrictions and guidelines

This feature configures the 802.1p priority
for packets of the following protocols: ARP, NTP, OSPF, BGP, PIM, SSH, Telnet,
and LDP. 

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Configure the 802.1p priority for control
packets sent by the device

control-packet dot1p priority

By default, the 802.1p priority is 6 for
control packets sent by a device.

## Display and maintenance commands for VLANs

Execute display
commands in any view and reset commands in user
view.

 

| Task | Command |
| --- | --- |
| Display VLAN interface information. | display interface \[ vlan-interface \[ interface-number ] ] \[ brief \[ description \| down ] ] || Display information about IP subnet-based VLANs that are associated with the specified ports. | display ip-subnet-vlan interface { interface-type interface-number1 \[ to interface-type interface-number2 ] \| all } || Display information about IP subnet-based VLANs. | display ip-subnet-vlan vlan { vlan\-id1 \[ to vlan\-id2 ] \| all } || Display hybrid ports or trunk ports on the device. | display port { hybrid \| trunk } || Display information about protocol-based VLANs that are associated with the specified ports. | display protocol-vlan interface { interface-type interface-number1 \[ to interface-type interface-number2 ] \| all } || Display information about protocol-based VLANs. | display protocol-vlan vlan { vlan\-id1 \[ to vlan\-id2 ] \| all } || Display VLAN information. | display vlan \[ vlan-id1 \[ to vlan-id2 ] \| all \| dynamic \| reserved \| static ] || Display brief VLAN information. | display vlan brief || Display VLAN group information. | display vlan-group \[ group-name ] || Clear statistics on a VLAN interface. | reset counters interface \[ vlan-interface \[ interface-number ] ] || Display MAC-to-VLAN entries. | display mac-vlan { all \| dynamic \| mac-address mac-address \[ mask mac-mask ] \| static \| vlan vlan-id } || Display all ports that are enabled with the MAC-based VLAN feature. | display mac-vlan interface |












‌

## VLAN configuration examples

### Example: Configuring port-based VLANs

#### Network configuration

As shown in [Figure 3](#_Ref257291223):

·     Host A and Host C belong to Department A. VLAN
100 is assigned to Department A.

·     Host B and Host D belong to Department B. VLAN
200 is assigned to Department B.

Configure port-based VLANs so that only hosts
in the same department can communicate with each other.

Figure 3 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11704380_x_Img_x_png_3_2215978_294551_0.png)

‌

#### Procedure

**1\.**Configure Device A:

\# Create VLAN 100, and assign Ten-GigabitEthernet 1/0/1 to VLAN 100\.

\<DeviceA\> system-view

\[DeviceA] vlan 100

\[DeviceA-vlan100] port ten-gigabitethernet 1/0/1

\[DeviceA-vlan100] quit

\# Create VLAN 200, and assign Ten-GigabitEthernet 1/0/2 to VLAN 200\.

\[DeviceA] vlan 200

\[DeviceA-vlan200] port ten-gigabitethernet 1/0/2

\[DeviceA-vlan200] quit

\# Configure Ten-GigabitEthernet 1/0/3 as a trunk port,
and assign the port to VLANs 100 and 200\.

\[DeviceA] interface ten-gigabitethernet 1/0/3

\[DeviceA-Ten-GigabitEthernet1/0/3] port link-type
trunk

\[DeviceA-Ten-GigabitEthernet1/0/3] port trunk permit
vlan 100 200

Please wait... Done.

**2\.**Configure Device B in the same way Device A
is configured. (Details not shown.)

**3\.**Configure hosts:

**d.**Configure Host A and Host C to be on the
same IP subnet. For example, 192.168.100.0/24.

**e.**Configure Host B and Host D to be on the
same IP subnet. For example, 192.168.200.0/24.

#### Verifying the configuration

\# Verify that Host A and Host C can ping
each other, but they both fail to ping Host B and Host D. (Details not shown.)

\# Verify that Host B and Host D can ping
each other, but they both fail to ping Host A and Host C. (Details not shown.)

\# Verify that VLANs 100 and 200 are
correctly configured on Device A.

\[DeviceA-Ten-GigabitEthernet1/0/3] display vlan 100

 VLAN ID: 100

 VLAN type: Static

 Route interface: Not configured

 Description: VLAN 0100

 Name: VLAN 0100

 Tagged ports:

    Ten-GigabitEthernet1/0/3(U)

 Untagged ports:

    Ten-GigabitEthernet1/0/1(U)

\[DeviceA-Ten-GigabitEthernet1/0/3] display vlan 200

 VLAN ID: 200

 VLAN type: Static

 Route interface: Not configured

 Description: VLAN 0200

 Name: VLAN 0200

 Tagged ports:

    Ten-GigabitEthernet1/0/3(U)

 Untagged ports:

    Ten-GigabitEthernet1/0/2(U)

### Example: Configuring MAC-based VLANs

#### Network configuration

As shown in [Figure 4](#_Ref387936888):

·     Ten-GigabitEthernet 1/0/1 of Device A and Device C are each connected to a meeting room.
Laptop 1 and Laptop 2 are used for meetings and might be used in either of the
two meeting rooms.

·     One department uses VLAN 100 and owns Laptop 1\.
The other department uses VLAN 200 and owns Laptop 2\.

Configure MAC-based VLANs, so that Laptop 1
and Laptop 2 can access Server 1 and Server 2, respectively, no matter which
meeting room they are used in.

Figure 4 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11704381_x_Img_x_png_4_2215978_294551_0.png)

‌

#### Procedure

**1\.**Configure Device A:

\# Create VLANs 100 and 200\.

\<DeviceA\> system-view

\[DeviceA] vlan 100

\[DeviceA-vlan100] quit

\[DeviceA] vlan 200

\[DeviceA-vlan200] quit

\# Associate the MAC addresses of Laptop 1
and Laptop 2 with VLANs 100 and 200, respectively.

\[DeviceA] mac-vlan mac-address
000d-88f8-4e71 vlan 100

\[DeviceA] mac-vlan mac-address
0014-222c-aa69 vlan 200

\# Configure Ten-GigabitEthernet 1/0/1 as a hybrid port,
and assign it to VLANs 100 and 200 as an untagged VLAN member.

\[DeviceA] interface ten-gigabitethernet 1/0/1

\[DeviceA-Ten-GigabitEthernet1/0/1] port link-type
hybrid

\[DeviceA-Ten-GigabitEthernet1/0/1] port hybrid vlan
100 200 untagged

\# Enable the MAC-based VLAN feature on Ten-GigabitEthernet 1/0/1.

\[DeviceA-Ten-GigabitEthernet1/0/1] mac-vlan enable

\[DeviceA-Ten-GigabitEthernet1/0/1] quit

\# Configure the uplink port (Ten-GigabitEthernet 1/0/2) as a trunk port,
and assign it to VLANs 100 and 200\.

\[DeviceA] interface ten-gigabitethernet 1/0/2

\[DeviceA-Ten-GigabitEthernet1/0/2] port link-type
trunk

\[DeviceA-Ten-GigabitEthernet1/0/2] port trunk permit
vlan 100 200

\[DeviceA-Ten-GigabitEthernet1/0/2] quit

**2\.**Configure Device B:

\# Create VLAN 100, and assign Ten-GigabitEthernet 1/0/3 to VLAN 100\.

\<DeviceB\> system-view

\[DeviceB] vlan 100

\[DeviceB-vlan100] port ten-gigabitethernet 1/0/3

\[DeviceB-vlan100] quit

\# Create VLAN 200 and assign Ten-GigabitEthernet 1/0/4 to VLAN 200\.

\[DeviceB] vlan 200

\[DeviceB-vlan200] port ten-gigabitethernet 1/0/4

\[DeviceB-vlan200] quit

\# Configure Ten-GigabitEthernet 1/0/1 as a trunk port,
and assign the port to VLANs 100 and 200\.

\[DeviceB] interface ten-gigabitethernet 1/0/1

\[DeviceB-Ten-GigabitEthernet1/0/1] port link-type
trunk

\[DeviceB-Ten-GigabitEthernet1/0/1] port trunk permit
vlan 100 200

\[DeviceB-Ten-GigabitEthernet1/0/1] quit

\# Configure Ten-GigabitEthernet 1/0/2 as a trunk port,
and assign the port to VLANs 100 and 200\.

\[DeviceB] interface ten-gigabitethernet 1/0/2

\[DeviceB-Ten-GigabitEthernet1/0/2] port link-type
trunk

\[DeviceB-Ten-GigabitEthernet1/0/2] port trunk permit
vlan 100 200

\[DeviceB-Ten-GigabitEthernet1/0/2] quit

**3\.**Configure Device C in the same way as the
Device A is configured. (Details not shown.)

#### Verifying the configuration

\# Verify that Laptop 1 can access only
Server 1, and Laptop 2 can access only Server 2\. (Details not shown.)

\# Verify the MAC-to-VLAN entries on Device
A and Device C, for example, on Device A.

\[DeviceA] display mac-vlan all

  The following MAC VLAN addresses
exist:

  S:Static  D:Dynamic

  MAC address      Mask            
VLAN ID   Dot1p      State

  000d-88f8-4e71  
ffff-ffff-ffff   100       0          S

  0014-222c-aa69  
ffff-ffff-ffff   200       0          S

 

  Total MAC VLAN address count: 2

### Example: Configuring IP subnet-based VLANs

#### Network configuration

As shown in[Figure 5](#_Ref370109410),
the hosts in the office belong to different IP subnets.

Configure Device C to transmit packets from
192.168.5.0/24 and 192.168.50.0/24 in VLANs 100 and 200, respectively.

Figure 5 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11704382_x_Img_x_png_5_2215978_294551_0.png)

‌

#### Procedure

**1\.**Configure Device C:

\# Associate IP subnet 192.168.5.0/24 with
VLAN 100\.

\<DeviceC\> system-view

\[DeviceC] vlan 100

\[DeviceC-vlan100]
ip-subnet-vlan ip 192.168.5.0 255.255.255.0

\[DeviceC-vlan100] quit

\# Associate IP subnet 192.168.50.0/24
with VLAN 200\.

\[DeviceC] vlan 200

\[DeviceC-vlan200]
ip-subnet-vlan ip 192.168.50.0 255.255.255.0

\[DeviceC-vlan200] quit

\# Configure Ten-GigabitEthernet 1/0/2 as a hybrid port,
and assign it to VLAN 100 as a tagged VLAN member.

\[DeviceC] interface ten-gigabitethernet 1/0/2

\[DeviceC-Ten-GigabitEthernet1/0/2] port link-type
hybrid

\[DeviceC-Ten-GigabitEthernet1/0/2] port hybrid vlan
100 tagged

\[DeviceC-Ten-GigabitEthernet1/0/2] quit

\# Configure Ten-GigabitEthernet 1/0/3 as a hybrid port,
and assign it to VLAN 200 as a tagged VLAN member.

\[DeviceC] interface ten-gigabitethernet 1/0/3

\[DeviceC-Ten-GigabitEthernet1/0/3] port link-type
hybrid

\[DeviceC-Ten-GigabitEthernet1/0/3] port hybrid vlan
200 tagged

\[DeviceC-Ten-GigabitEthernet1/0/3] quit

\# Configure Ten-GigabitEthernet 1/0/1 as a hybrid port,
and assign it to VLANs 100 and 200 as an untagged VLAN member.

\[DeviceC] interface ten-gigabitethernet 1/0/1

\[DeviceC-Ten-GigabitEthernet1/0/1] port link-type
hybrid

\[DeviceC-Ten-GigabitEthernet1/0/1] port hybrid vlan
100 200 untagged

\# Associate Ten-GigabitEthernet 1/0/1 with the IP
subnet-based VLANs 100 and 200\.

\[DeviceC-Ten-GigabitEthernet1/0/1] port hybrid ip-subnet-vlan
vlan 100

\[DeviceC-Ten-GigabitEthernet1/0/1] port hybrid ip-subnet-vlan
vlan 200

\[DeviceC-Ten-GigabitEthernet1/0/1] quit

**2\.**Configure Device A and Device B to forward
packets from VLANs 100 and 200, respectively. (Details not shown.)

#### Verifying the configuration

\# Verify the IP subnet-based VLAN configuration
on Device C.

\[DeviceC] display ip-subnet-vlan vlan
all

 VLAN ID: 100

  Subnet index      IP address     
Subnet mask

  0                 192.168.5.0     255.255.255.0

 

 VLAN ID: 200

  Subnet index      IP address     
Subnet mask

  0                 192.168.50.0   
255.255.255.0

\# Verify the IP subnet-based VLAN configuration
on Ten-GigabitEthernet 1/0/1 of
Device C.

\[DeviceC] display ip-subnet-vlan
interface ten-gigabitethernet 1/0/1

 Interface: Ten-GigabitEthernet1/0/1

  VLAN ID   Subnet index    IP address
      Subnet mask       Status

  100       0               192.168.5.0   
  255.255.255.0     Active

  200       0               192.168.50.0   
 255.255.255.0     Active

### Example: Configuring protocol-based VLANs

#### Network configuration

As shown in[Figure 6](#_Ref375830785):

·     The majority of hosts in a lab environment run
the IPv4 protocol.

·     The other hosts run the IPv6 protocol for
teaching purposes.

To isolate IPv4 and IPv6 traffic at Layer 2,
configure protocol-based VLANs to associate the IPv4 and ARP protocols with
VLAN 100, and associate the IPv6 protocol with VLAN 200\.

Figure 6 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11704383_x_Img_x_png_6_2215978_294551_0.png)

‌

#### Procedure

In this example, L2 Switch A and L2 Switch
B use the factory configuration.

**1\.**Configure Device:

\# Create VLAN 100, and configure the
description for VLAN 100 as protocol
VLAN for IPv4.

\<Device\> system-view

\[Device] vlan 100

\[Device-vlan100] description protocol
VLAN for IPv4

\# Assign Ten-GigabitEthernet 1/0/3 to VLAN 100\.

\[Device-vlan100] port ten-gigabitethernet 1/0/3

\[Device-vlan100] quit

\# Create VLAN 200, and configure the
description for VLAN 200 as protocol
VLAN for IPv6.

\[Device] vlan 200

\[Device-vlan200] description
protocol VLAN for IPv6

\# Assign Ten-GigabitEthernet 1/0/4 to VLAN 200\.

\[Device-vlan200] port ten-gigabitethernet 1/0/4

\# Configure VLAN 200 as a protocol-based
VLAN, and create an IPv6 protocol template with the index 1 for VLAN 200\.

\[Device-vlan200] protocol-vlan
1 ipv6

\[Device-vlan200] quit

\# Configure VLAN 100 as a protocol-based
VLAN. Create an IPv4 protocol template with the index 1, and create an ARP
protocol template with the index 2\. (In Ethernet II encapsulation, the protocol
type ID for ARP is 0806 in hexadecimal notation.)

\[Device] vlan 100

\[Device-vlan100] protocol-vlan
1 ipv4

\[Device-vlan100] protocol-vlan
2 mode ethernetii etype 0806

\[Device-vlan100] quit

\# Configure Ten-GigabitEthernet 1/0/1 as a hybrid port,
and assign it to VLANs 100 and 200 as an untagged VLAN member.

\[Device] interface ten-gigabitethernet 1/0/1

\[Device-Ten-GigabitEthernet1/0/1] port link-type
hybrid

\[Device-Ten-GigabitEthernet1/0/1] port hybrid vlan
100 200 untagged

\# Associate Ten-GigabitEthernet 1/0/1 with the IPv4 and
ARP protocol templates of VLAN 100 and the IPv6 protocol template of VLAN 200\.

\[Device-Ten-GigabitEthernet1/0/1] port hybrid protocol-vlan
vlan 100 1 to 2

\[Device-Ten-GigabitEthernet1/0/1] port hybrid protocol-vlan
vlan 200 1

\[Device-Ten-GigabitEthernet1/0/1] quit

\# Configure Ten-GigabitEthernet 1/0/2 as a hybrid port,
and assign it to VLANs 100 and 200 as an untagged VLAN member.

\[Device] interface ten-gigabitethernet 1/0/2

\[Device-Ten-GigabitEthernet1/0/2] port link-type
hybrid

\[Device-Ten-GigabitEthernet1/0/2] port hybrid vlan
100 200 untagged

\# Associate Ten-GigabitEthernet 1/0/2 with the IPv4 and
ARP protocol templates of VLAN 100 and the IPv6 protocol template of VLAN 200\.

\[Device-Ten-GigabitEthernet1/0/2] port hybrid protocol-vlan
vlan 100 1 to 2

\[Device-Ten-GigabitEthernet1/0/2] port hybrid protocol-vlan
vlan 200 1

\[Device-Ten-GigabitEthernet1/0/2] quit

**2\.**Configure hosts and servers:

**f.**Configure IPv4 Host A, IPv4 Host B, and IPv4
server to be on the same network segment (192.168.100.0/24, for example).
(Details not shown.)

**g.**Configure IPv6 Host A, IPv6 Host B, and IPv6
server to be on the same network segment (2001::1/64, for example). (Details
not shown.)

#### Verifying the configuration

**1\.**Verify the following:

¡     The hosts
and the server in VLAN 100 can successfully ping one another. (Details not
shown.)

¡     The
hosts and the server in VLAN 200 can successfully ping one another. (Details
not shown.)

¡     The hosts
or the server in VLAN 100 cannot ping the hosts or server in VLAN 200\. (Details
not shown.)

**2\.**Verify the protocol-based VLAN configuration:

\# Display protocol-based VLANs on Device.

\[Device] display protocol-vlan
vlan all

 VLAN ID: 100

  Protocol index  Protocol
type

  1               IPv4

  2               Ethernet II
Etype 0x0806

 

 VLAN ID: 200

  Protocol index  Protocol
type

  1               IPv6

\# Display protocol-based VLANs on the
ports of Device.

\[Device] display protocol-vlan
interface all

 Interface: Ten-GigabitEthernet1/0/1

  VLAN ID  Protocol index 
Protocol type             Status

  100      1               IPv4                     
Active

  100      2               Ethernet
II Etype 0x0806  Active

  200      1               IPv6                     
Active

 

 Interface: Ten-GigabitEthernet 1/0/2

  VLAN ID  Protocol index 
Protocol type             Status

  100      1               IPv4                     
Active

  100      2               Ethernet
II Etype 0x0806  Active

  200      1               IPv6                  
   Active

