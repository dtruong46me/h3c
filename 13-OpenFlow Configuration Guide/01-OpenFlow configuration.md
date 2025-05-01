
# Configuring OpenFlow

## About OpenFlow

OpenFlow is the communications interface
defined between the control and forwarding layers of a Software-Defined
Networking architecture. With OpenFlow, you can perform centralized data
forwarding management for physical and virtual devices through controllers.

### OpenFlow network components

OpenFlow separates the data forwarding and routing
decision functions. It keeps the flow-based forwarding function and employs a separate
controller to make routing decisions. An OpenFlow switch communicates with the controller
through an OpenFlow channel. An OpenFlow channel can be encrypted by using TLS
or run directly over TCP. An OpenFlow switch exchanges control messages with the
controller through an OpenFlow channel to perform the following operations:

·     Receive flow table entries or data from the controller.

·     Report information to the controller.

Unless otherwise stated, a switch refers to
an OpenFlow switch throughout this document.

Figure 1 OpenFlow network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705898_x_Img_x_png_0_2216140_294551_0.png)

‌

### OpenFlow switch

OpenFlow switches include the following
types:

·     OpenFlow-only—Supports only OpenFlow operation.

·     OpenFlow-hybrid—Supports both OpenFlow operation and traditional Ethernet switching
operation.

### OpenFlow port

OpenFlow supports the following types of
ports:

·     Physical port—Corresponds to a hardware interface, such as an Ethernet interface.
A physical port can be either an ingress port or an output port.

·     Logical port—Does not correspond to a hardware interface and might be defined by
non-OpenFlow methods. For example, aggregate interfaces are logical ports. A logical
port can be either an ingress port or an output port.

·     Reserved port—Defined by OpenFlow to specify forwarding actions. Reserved ports include
the following types:

¡     Controller—OpenFlow controller.

¡     In port—Packet ingress port.

¡     Local—Local CPU.

¡     Normal—Normal forwarding process.

Only the Controller and Local types can
be used as ingress ports.

 

### OpenFlow instance

Unless otherwise stated, an OpenFlow switch
refers to an OpenFlow instance throughout this document.

You can configure one or more OpenFlow
instances on the same device. A controller considers each OpenFlow instance as
a separate OpenFlow switch and deploys forwarding instructions to it.

#### OpenFlow instance mode

An OpenFlow instance operates in one of the
following modes: 

·     Global mode—When the global mode is enabled for an OpenFlow instance, the flow
entries take effect on packets within the network. All service interfaces on
the device belong to the OpenFlow instance.

·     VLAN mode—When the VLAN mode is enabled for an OpenFlow instance, the flow
entries take effect only on packets within VLANs associated with the OpenFlow
instance. You must associate the OpenFlow instance with VLANs. All interfaces
in the associated VLANs belong to the OpenFlow instance.

#### Activation and reactivation

The configurations for an OpenFlow instance
take effect only after the OpenFlow instance is activated.

The controller can deploy flow entries to
an OpenFlow instance only after the OpenFlow instance reports the following
device information to the controller:

·     Capabilities supported by OpenFlow.

·     Information about ports that belong to the OpenFlow
instance.

An activated OpenFlow instance must be
reactivated when any of the OpenFlow instance configurations are changed.

After reactivation, the OpenFlow instance
is disconnected from all controllers and then reconnected to them.

#### OpenFlow instance port

An OpenFlow switch sends information about
the following ports to the controller:

·     Physical ports.

·     Logical ports.

·     Reserved ports of the Local
type.

In loosen mode, a port belongs to the
OpenFlow instance when VLANs associated with the OpenFlow instance overlap with
the port's allowed VLANs. Otherwise, a port belongs to an OpenFlow instance
only when VLANs associated with the OpenFlow instance are within the port's
allowed VLAN list.

### OpenFlow flow table

An OpenFlow switch matches packets with one
or more flow tables. A flow table contains flow entries, and packets are
matched based on the matching precedence of flow entries.

#### Flow table types

OpenFlow flow tables include the following
types:

·     MAC-IP—Combines the MAC address table and FIB table.

A MAC-IP flow table provides the
following match fields:

¡     Destination
MAC address.

¡     VLAN.

¡     Destination
IP address.

A MAC-IP flow table provides the
following actions:

¡     Modifying
the destination MAC address.

¡     Modifying
the source MAC address.

¡     Modifying
the VLAN.

¡     Modifying
the tunnel ID.

¡     Specifying
the output port.

For more information, see "[Appendix B MAC-IP flow table](#_Ref429475658)."

·     Extensibility—Uses ACLs to match packets. 

#### Flow entry

A flow entry contains the following fields:

·     Match fields—Matching rules of the flow entry. These contain the ingress port, packet
headers, and metadata specified by the previous table.

·     Priority—Matching precedence of the flow entry. When a packet is matched with
the flow table, only the highest priority flow entry that matches the packet is
selected.

·     Counters—Counts of the packets and bytes that match the flow entry.

·     Instructions—Used to modify the action set or pipeline processing. Instructions include
the following types:

¡     Meter—Directs the packets to the
specified meter to rate limit the packets. 

¡     Apply-Actions—Applies the specified actions
in the action list immediately.

¡     Clear-Actions—Clears all actions in
the action set immediately.

¡     Write-Actions—Modifies all actions in
the action set immediately.

Actions are executed in one of the
following ways:

¡     Action Set—When the instruction set
of a flow entry does not contain a Goto-Table
instruction, pipeline processing stops. Then, the actions in the action set are
executed in the order specified by the instruction list. An action set contains
a maximum of one action of each type.

¡     Action List—The actions in the action
list are executed immediately in the order specified by the action list. The effect
of those actions is cumulative.

Actions include the following types:

¡     (Required.) Output—The Output action
forwards a packet to the specified OpenFlow port. OpenFlow switches must
support forwarding packets to physical ports, logical ports, and reserved
ports.

¡     (Required.) Drop—No explicit action
exists to represent drops. Packets whose action sets have no output actions are
dropped. Typically, packets are dropped due to empty instruction sets, empty
action sets, or the executing a Clear-Actions instruction.

¡     (Required.) Group—Process the packet
through the specified group. The exact interpretation depends on group type.

¡     (Optional.) Set-Queue—The Set-Queue
action sets the queue ID for a packet. When the packet is forwarded to a port
by the output action, the packet is assigned to the queue attached to this port
for scheduling and forwarding. The forwarding behavior is dictated by the configuration
of the queue and provides basic QoS support.

¡     (Optional.) Set-Field—The Set-Field
actions are identified by their field type and modify the values of corresponding
header fields in the packet. Set-Field actions are always applied to the
outermost header. For example, a Set VLAN ID action always sets the ID of the outermost
VLAN tag.

·     Timeouts—Maximum amount of idle time or hard time for the flow entry. 

¡     idle time—The flow entry is removed
if it has matched no packets within the idle time. 

¡     hard time—The flow entry is removed when
the hard time timeout is exceeded, regardless of whether or not it has matched
packets.

·     Cookie—Custom data specified by the controller. The data is not used for
processing packets, and might be used by the controller for matching flow
entries. 

·     Flags—Flag for modifying the flow entry management method. For example,
the OFPFF\_SEND\_FLOW\_REM flag triggers the switch to send Flow-Removed messages
for the flow entry to the controller. 

Figure 2 Flow entry components

![](https://resource.h3c.com/en/202407/12/20240712_11705899_x_Img_x_png_1_2216140_294551_0.png)

‌

#### OpenFlow pipeline

The OpenFlow pipeline processing defines
how packets interact with flow tables contained by a switch. 

The flow tables of an OpenFlow switch are
sequentially numbered, starting at 0\. The packet is first matched with flow
entries of the first flow table, which is flow table 0\. A flow entry can only
direct a packet to a flow table number that is greater than its own flow table
number. 

When a packet matches a flow entry, the OpenFlow
switch updates the action set for the packet and passes the packet to the next
flow table. In the last flow table, the OpenFlow switch executes all actions to
modify packet contents and specify the output port for packet forwarding. If the
instruction set of a flow table contains an action list, the OpenFlow switch
immediately executes the actions for a copy of the packet in this table.

Figure 3 OpenFlow forwarding workflow

![](https://resource.h3c.com/en/202407/12/20240712_11705900_x_Img_x_png_2_2216140_294551_0.png)

‌

#### Table-miss flow entry

Every flow table must support a table-miss flow
entry to process table misses. The table-miss flow entry specifies how to
process packets that were not matched by other flow entries in the flow table. 

The table-miss flow entry wildcards all
match fields (all fields omitted) and has the lowest priority 0\.

The table-miss flow entry behaves in most
ways like any other flow entry.

### Group table

The ability for a flow entry to point to a
group enables OpenFlow to represent additional methods of forwarding. A group
table contains group entries. 

Figure 4 Group entry components

![](https://resource.h3c.com/en/202407/12/20240712_11705901_x_Img_x_png_3_2216140_294551_0.png)

‌

A group entry contains the following fields:

·     Group Identifier—A 32 bit unsigned integer uniquely identifying the group.

·     Group Type—Type of the group. All means executing
all buckets in the group. This group is used for multicast or broadcast
forwarding.

·     Counters—Updated when packets are processed by a group.

·     Action Buckets—An ordered list of action buckets, where each action bucket
contains a set of actions to execute and associated parameters. [Table 1](#_Ref15561752) shows
the actions supported by action buckets.

Table 1 Actions supported by action buckets

| Action | Type | Description |
| --- | --- | --- |
| Output | Required | Forwards a packet to a specific OpenFlow port, for example, physical port, logical port, and reserved port. |

 

### Meter table

Meters enable OpenFlow to implement various
simple QoS operations, such as rate-limiting. A meter table contains meter
entries.

A meter entry contains the following fields:

·     Meter Identifier—A 32 bit unsigned integer uniquely identifying the meter.

·     Meter Bands—Each meter can have one meter band, which specifies the rate at
which the band applies and the way packets should be processed. If the current
rate of packets exceeds the rate of the band, packets are processed according
to the method defined in the band.

·     Counters—Updated when packets are processed by a meter.

Figure 5 Meter entry components

![](https://resource.h3c.com/en/202407/12/20240712_11705902_x_Img_x_png_4_2216140_294551_0.png)

‌

A meter band contains the following fields:

·     Band Type—(Optional.) Packet processing methods. Options are:

¡     Drop—Discards the packet when the
rate of the packet exceeds the band rate. 

·     Rate—Defines the lowest rate at which the band can apply.

·     Counters—Updated when packets are processed by a band.

·     Type Specific Arguments—Some band types have specific arguments.

Figure 6 Band components

![](https://resource.h3c.com/en/202407/12/20240712_11705903_x_Img_x_png_5_2216140_294551_0.png)

‌

### OpenFlow channel

The OpenFlow channel is the interface that
connects each OpenFlow switch to a controller. The controller uses the OpenFlow
channel to exchange control messages with the switch to perform the following
operations: 

·     Configure and manage the switch. 

·     Receive events from the switch. 

·     Send packets out the switch. 

The OpenFlow channel is usually encrypted by
using TLS. Also, an OpenFlow channel can be run directly over TCP.

The OpenFlow protocol supports the
following message types: controller-to-switch, asynchronous, and symmetric. Each
message type has its own subtypes.

#### Controller-to-switch messages

Controller-to-switch messages are initiated
by the controller and used to directly manage or inspect the state of the
switch. Controller-to-switch messages might or might not require a response
from the switch.

The controller-to-switch messages include
the following subtypes:

·     Features—The controller requests the basic capabilities of a switch by
sending a features request. The switch must respond with a features reply that
specifies the basic capabilities of the switch.

·     Configuration—The controller sets and queries configuration parameters in the
switch. The switch only responds to a query from the controller.

·     Modify-State—The controller sends Modify-State messages to manage state on the
switches. Their primary purpose is to add, delete, and modify flow or group
entries in the OpenFlow tables and to set switch port properties.

·     Read-State—The controller sends Read-State messages to collect various
information from the switch, such as current configuration and statistics.

·     Packet-out—These are used by the controller to send packets out of the
specified port on the switch, or to forward packets received through packet-in
messages. Packet-out messages must contain a full packet or a buffer ID representing
a packet stored in the switch. The message must also contain a list of actions to
be applied in the order they are specified. An empty action list drops the
packet.

·     Barrier—Barrier messages are used to confirm the completion of the previous
operations. The controller send s Barrier request. The switch must send a Barrier
reply when all the previous operations are complete. 

·     Role-Request—Role-Request messages are used by the controller to set the role of
its OpenFlow channel, or query that role. It is typically used when the switch
connects to multiple controllers.

·     Asynchronous-Configuration—These are used by the controller to set an additional filter on the
asynchronous messages that it wants to receive, or to query that filter. It is
typically used when the switch connects to multiple controllers.

#### Asynchronous messages

Switches send asynchronous messages to
controllers to inform a packet arrival or switch state change. For example,
when a flow entry is removed due to timeout, the switch sends a flow-removed
message to inform the controller.

The asynchronous messages include the
following subtypes:

·     Packet-In—Transfer the control of a packet to the controller. For all packets
forwarded to the Controller reserved port using a flow entry or the table-miss flow
entry, a packet-in event is always sent to controllers. Other processing, such
as TTL checking, can also generate packet-in events to send packets to the
controller. The packet-in events can include the full packet or can be configured
to buffer packets in the switch. If the packet-in event is configured to buffer
packets, the packet-in events contain only some fraction of the packet header
and a buffer ID. The controller processes the full packet or the combination of
the packet header and the buffer ID. Then, the controller sends a packet-out message
to direct the switch to process the packet.

·     Flow-Removed—Inform the controller about the removal of a flow entry from a flow
table. These are generated due to a controller flow delete request or the
switch flow expiry process when one of the flow timeouts is exceeded.

·     Port-status—Inform the controller of a state or setting change on a port.

·     Error—Inform the controller of a problem or error.

#### Symmetric messages

Symmetric messages are sent without solicitation,
in either direction.

The symmetric messages contain the
following subtypes:

·     Hello—Hello messages are exchanged between the switch and controller upon
connection startup.

·     Echo—Echo request or reply messages can be sent from either the switch or
the controller, and must return an echo reply. They are mainly used to verify
the liveness of a controller-switch connection, and might also be used to
measure its latency or bandwidth.

#### OpenFlow timers

An OpenFlow switch supports the following
timers:

·     Connection detection
interval—Interval at which the OpenFlow switch
sends an Echo Request message to a controller. When the OpenFlow switch
receives no Echo Reply message within three intervals, the OpenFlow switch is
disconnected from the controller.

·     Reconnection interval—Interval for the OpenFlow switch to wait before it attempts to
reconnect to a controller.

### OpenFlow controller

#### Controller roles

A switch can establish connections with multiple
controllers. When OpenFlow operation is initiated, a switch is simultaneously
connected to multiple controllers in Equal state. A controller can request its
role to be changed at any time. The controller role contains the following
types:

·     Equal—In this role, the controller has full access to the switch and is
equal to other controllers in the same role. By default, the controller
receives all switch asynchronous messages such as packet-in and flow-removed
messages. The controller can send controller-to-switch messages to modify the
state of the switch.

·     Master—This role is similar to the Equal role and has full access to the switch.
The difference is that up to one controller in this role is allowed for a
switch.

·     Slave—In this role, the controller has read-only access to the switch. 

The controller cannot send controller-to-switch
messages to perform the following operations:

¡     Deploy
flow entries, group entries, and meter entries.

¡     Modify
the port and switch configurations.

¡     Send
packet-out messages.

By default, the controller does not
receive switch asynchronous messages except Port-status messages. The
controller can send Asynchronous-Configuration messages to set the asynchronous
message types it wants to receive.

#### Controller connection modes

An OpenFlow instance can connect to one or
more controllers, depending on the controller connection mode the OpenFlow
instance uses:

·     Single—The OpenFlow instance connects to only one controller at a time.
When communication with the current controller fails, the OpenFlow instance uses
another controller.

·     Multiple—The OpenFlow instance can simultaneously connect to multiple
controllers. When communication with any controller fails, the OpenFlow
instance attempts to reconnect to the controller after a reconnection interval.

#### Main and auxiliary connections

The OpenFlow channel can have one main
connection and multiple auxiliary connections.

·     Main connection—Processes control messages to complete operations such as deploying
entries, obtaining data, and sending information. The main connection must be a
reliable TCP or SSL connection.

·     Auxiliary connection—Improves the communication performance between the controller and
OpenFlow switches. An auxiliary connection can have the different destination IP
address and port number than the main connection.

#### Connection interruption mode

When an OpenFlow switch is disconnected
from all controllers, the OpenFlow switch is set to either of the following
modes:

·     Secure—The OpenFlow switch forwards traffic based on flow tables and does
not remove unexpired flow entries. If the output action in a matching flow
entry is to forward traffic to a controller, the traffic is discarded. This is
the default forwarding mode. 

·     Smart—The OpenFlow switch forwards traffic based on flow tables and does
not remove unexpired flow entries. If the output action in a matching flow
entry is to forward traffic to a controller, the traffic is forwarded in normal
process.

·     Standalone—The OpenFlow switch uses the normal forwarding process.

The OpenFlow switch forwards traffic based
on flow tables when it reconnects to a controller successfully.

### Protocols and standards

OpenFlow Switch
Specification Version 1.3.3

## OpenFlow tasks at a glance

To configure OpenFlow, perform the
following tasks:

**1\.**[Configuring OpenFlow instances](#_Ref474500695)

**a.**[Creating an OpenFlow instance](#_Ref350941419)

**b.**[Configuring the OpenFlow instance mode](#_Ref474500802)

**c.**(Optional.) [Configuring inband management VLANs](#_Ref517682696)

**d.**(Optional.) [Configuring flow tables and flow entries for
an OpenFlow instance](#_Ref350941423)

**e.**(Optional.) [Setting the controller connection mode](#_Ref474500816)

**f.**(Optional.) [Preventing an OpenFlow instance from reporting
the specified types of ports to controllers](#_Ref402876341)

**g.**[Activating or reactivating an OpenFlow
instance](#_Ref402972279)

**h.**(Optional.) [Configuring OpenFlow instance attributes](#_Ref474500890)

**2\.**[Configuring controllers for an OpenFlow
switch](#_Ref399687260)

**3\.**(Optional.) [Configuring an OpenFlow instance to act as
an SSL server to listen to controllers](#_Ref474500731)

**4\.**(Optional.) [Refreshing all Layer 3 flow entries in the
MAC-IP flow tables for an OpenFlow instance](#_Ref474500741)

**5\.**(Optional.) [Shutting down an interface by OpenFlow](#_Ref454471676)

**6\.**(Optional.) [Enabling SNMP notifications for OpenFlow](#_Ref18665904)

## Configuring OpenFlow instances

### Creating an OpenFlow instance

**1\.**Enter system view.

system-view

**2\.**Create an OpenFlow instance and enter its
view.

openflow instance instance-id

**3\.**(Optional) Set the datapath ID.

datapath-id id

By default, the datapath ID of an
OpenFlow instance contains the instance ID and the bridge MAC address of the
device. The upper 16 bits are the instance ID and the lower 48 bits are the
bridge MAC address of the device.

The datapath ID uniquely identifies an
OpenFlow switch (OpenFlow instance). Do not set the same datapath ID for
different OpenFlow switches.

**4\.**Set a DSCP value for OpenFlow packets.

tcp dscp dscp-value

By default, no DSCP value is set for OpenFlow
packets.

### Configuring the OpenFlow instance mode

#### Restrictions and guidelines

When you associate an OpenFlow instance
with VLANs, follow these guidelines:

·     For VLAN traffic to be processed correctly, associate
different OpenFlow instances with different VLANs.

·     When you activate an OpenFlow instance that is
associated with nonexistent VLANs, the system automatically creates the VLANs.

·     Do not associate VLANs permitted on a port with different
OpenFlow instances. Otherwise, port modification messages of different OpenFlow
instances deployed from different controllers overwrite each other.

·     Do not configure BFD MAD on the VLAN interface
for a VLAN that is associated with an OpenFlow instance. For more information
about BFD MAD, see Virtual Technologies Configuration
Guide.

#### Enabling the global mode for an OpenFlow instance

**1\.**Enter system view.

system-view

**2\.**Enter OpenFlow instance view.

openflow instance instance-id

**3\.**Enable the global mode for the OpenFlow
instance.

Classification global

By default, the OpenFlow instance mode is
not configured.

#### Enabling the VLAN mode for an OpenFlow instance

**1\.**Enter system view.

system-view

**2\.**Enter OpenFlow instance view.

openflow instance instance-id

**3\.**Configure the OpenFlow instance mode.

classification vlan vlan-id \[ mask vlan-mask ] \[ loosen ]

By default, the OpenFlow instance mode is
not configured. 

### Configuring inband management VLANs

#### About this task

Traffic in the inband management VLANs are
forwarded in the normal forwarding process instead of the OpenFlow forwarding process.
Inband management VLANs are used by an OpenFlow instance to establish OpenFlow
channels to controllers.

#### Restrictions and guidelines

The ports that are assigned only to inband
management VLANs are not OpenFlow ports.

Inband management VLANs configured for an
OpenFlow instance in VLAN mode must be within the list of the VLANs associated
with the OpenFlow instance.

If an OpenFlow instance in global or VLAN
mode connects to a controller through a non-management Ethernet interface,
configure the VLAN to which the interface belongs as an inband management VLAN.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter OpenFlow instance view.

openflow instance instance-id

**3\.**Configure inband management VLANs for the
OpenFlow instance.

in-band management vlan
vlan-id-list

By default, no inband management VLANs
are configured for an OpenFlow instance. 

### Configuring flow tables and flow entries for an OpenFlow instance

#### Restrictions and guidelines

The extensibility flow table ID must be
greater than the MAC-IP flow table ID. 

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter OpenFlow instance view.

openflow instance instance-id

**3\.**Configure flow tables for the OpenFlow
instance.

flow-table { mac-ip mac-ip-table-id \| extensibility extensibility-table-id }\*

By default, an OpenFlow instance contains
one extensibility flow table with an ID of 0\.

**4\.**Set the maximum number of flow entries that
each extensibility flow table supports.

flow-entry max-limit limit-value

The default setting is 65535\. 

When the maximum number is reached, the
OpenFlow instance does not accept new flow entries for that table and sends a
deployment failure notification to the controller.

**5\.**Configure the OpenFlow instance to allow
dynamic ARP entries to overwrite OpenFlow ARP entries.

precedence dynamic arp

By default, an OpenFlow instance does not
allow dynamic ARP entries to overwrite OpenFlow ARP entries.

Only MAC-IP flow tables support this
feature.

**6\.**Allow the deployed flow tables to include
link aggregation member ports.

permit-port-type member-port

By default, the deployed flow tables
cannot include link aggregation member ports.

**7\.**Configure the OpenFlow instance to drop slow
protocol packets.

protocol-packet filter slow

By default, an OpenFlow instance does not
drop slow protocol packets.

The slow protocols include LACP and OAM.

**8\.**Configure the default action of table-miss
flow entries to forward packets to the normal pipeline.

default table-miss permit

By default, the default action of
table-miss flow entries is to drop packets.

### Setting thecontroller connection mode

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter OpenFlow instance view.

openflow instance instance-id

**3\.**Set the controller connection mode.

controller mode { multiple \| single }

By default, the multiple mode is used.

### Preventing an OpenFlow instance from reporting the specified types of ports to controllers

#### About this task

Perform this task to prevent an OpenFlow
instance from reporting controllers information about the following types of
interfaces that belong to the OpenFlow instance:

·     Layer 3 Ethernet interface.

·     Layer 3 aggregate interface.

·     VLAN interface.

·     Virtual switch instance (VSI) interface.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter OpenFlow instance view 

openflow instance instance-id

**3\.**Prevent an OpenFlow instance from reporting
the specified types of ports to controllers.

forbidden port { l3-physical-interface \| vlan-interface \| vsi-interface } \*

By default, no port types are prevented
from being reported to the controllers. All ports that belong to an OpenFlow
instance are reported to the controllers.

### Activating or reactivating an OpenFlow instance

**1\.**Enter system view.

system-view

**2\.**Enter OpenFlow instance view.

openflow instance instance-id

**3\.**Activate or reactivate the OpenFlow
instance.

active instance

By default, an OpenFlow instance is not
activated.

### Configuring OpenFlow instance attributes

#### Restrictions and guidelines

The OpenFlow instance attribute
configurations of an OpenFlow instance can take effect without activation for
the OpenFlow instance.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter OpenFlow instance view.

openflow instance instance-id

**3\.**Configure a description for the OpenFlow
instance.

description text

By default, an OpenFlow instance does not
have a description.

**4\.**Set OpenFlow timers.

¡     Set
the connection detection interval.

controller echo-request
interval interval

The default setting is 5 seconds.

¡     Set
the reconnection interval.

controller connect interval interval

The default setting is 60 seconds.

**5\.**Configure MAC address-related features.

¡     Forbid
MAC address learning for VLANs associated with the OpenFlow instance.

mac-learning forbidden

By default, MAC address learning is
allowed for VLANs associated with an OpenFlow instance.

The configuration does not take effect on
inband management VLANs.

¡     Configure
the OpenFlow instance to support matching the dynamic MAC addresses in the
query and deletion flow entry instructions sent from controllers.

mac-ip dynamic-mac aware

By default, an OpenFlow instance ignores
dynamic MAC addresses in the query and deletion flow entry instructions sent
from controllers.

Only MAC-IP flow tables support this
feature.

**6\.**Prevent the OpenFlow instance from reporting
ARP packets to the specified controllers.

forbidden packet-in arp controller controller-id-list

By default, no controllers to which ARP
packets are forbidden to be reported are configured.

This feature forbids an OpenFlow instance
to report ARP packets to the specified controllers to prevent the controllers
from being affected by a large number of packets.

**7\.**Disable logging for successful flow table modifications.

flow-log disable

By default, logging for successful flow table
modifications is enabled.

**8\.**Enable loop guard for the OpenFlow instance.

loop-protection enable

By default, loop guard is disabled for an
OpenFlow instance.

After an OpenFlow instance is deactivated,
you can enable loop guard for the OpenFlow instance to avoid loops. This
feature enables the deactivated OpenFlow instance to create a flow entry for
dropping all traffic in these VLANs.

## Configuring controllers for an OpenFlow switch

#### Restrictions and guidelines

Make sure the configuration of an auxiliary
connection does not conflict with the configuration of the main connection.
Otherwise, the auxiliary connection cannot be established.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter OpenFlow instance view.

openflow instance instance-id

**3\.**Specify a controller and configure the main
connection to the controller.

controller controller-id address { ip ipv4-address \| ipv6 ipv6-address } \[ port port-number ] \[ local address { ip local-ipv4-address \| ipv6 local-ipv6-address } \[ port local-port- number ] ] \[ ssl ssl-policy-name
\[ access-control-policy acp-policy-name ] ] \[ vrf vrf-name ] 

By default, an OpenFlow instance does not
have a main connection to a controller.

The source IP address must be the IP
address of a port belonging to an OpenFlow instance. Otherwise, the OpenFlow
switch might fail to establish a connection with the controller.

**4\.**(Optional) Specify a
controller and configure an auxiliary connection to the controller.

controller id auxiliary auxiliary-id transport { tcp \| udp \| ssl ssl-policy-name } \[ address { ip ipv4-address \| ipv6 ipv6-address } ] \[ port port-number ]

By default, an OpenFlow instance does not
have auxiliary connections to a controller.

If no destination IP address and port
number are specified, the auxiliary connection uses the destination IP address
and port number configured for the main connection.

**5\.**(Optional) Set the connection interruption
mode.

fail-open mode { secure \| smart\|  standalone }

By default, the secure mode is used.

**6\.**(Optional) Enable OpenFlow connection
backup.

tcp-connection backup

By default, OpenFlow connection backup is
enabled.

This feature prevents connection
interruption when an active/standby switchover occurs.

This feature is available only on an IRF
fabric with two member devices. 

This feature is available for only
OpenFlow connections established over TCP.

## Configuring an OpenFlow instance to act as an SSL server to listen to controllers

#### About this task

Typically, an OpenFlow instance actively
connects to the controller acting as a TCP/SSL client. After the SSL server is
enabled for an OpenFlow instance, the controller acts as the SSL client and
actively connects to the OpenFlow instance. For more information about SSL, see
Security Configuration Guide.

#### Restrictions and guidelines

This feature can take effect without
activation for an OpenFlow instance.

To re-configure the SSL server, first
execute the undo form of the command to delete the existing SSL server configuration.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter OpenFlow instance view.

openflow instance instance-id

**3\.**Configure an OpenFlow instance to act as an
SSL server to listen to controllers.

listening port port-number ssl ssl-policy-name

By default, an OpenFlow instance is not
configured to acts as an SSL server listen to controllers.

## Refreshing all Layer 3 flow entries in the MAC-IP flow tables for an OpenFlow instance

#### About this task

Perform this task to obtain all Layer 3
flow entries in the MAC-IP flow tables from the controller again if the Layer 3
flow entries have been overwritten.

#### Restrictions and guidelines

**1\.**Enter system view.

system-view

**2\.**Enter OpenFlow instance view.

openflow instance instance-id

**3\.**Refresh all Layer 3 flow entries in the
MAC-IP flow tables.

refresh ip-flow

## Shutting down an interface by OpenFlow

#### About this task

After an interface is shut down by
OpenFlow, the Current state field displays OFP DOWN in the display interface command output. 

You can use the undo openflow
shutdown command to bring up an interface shut
down by OpenFlow. The interface can also be brought up by port modification
messages from controllers.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter interface view.

interface interface-type interface-number

**3\.**Shut down an interface by OpenFlow.

openflow shutdown

By default, an interface is not shut down
by OpenFlow.

## Enabling SNMP notifications for OpenFlow

#### About this task

This feature enables OpenFlow to generate
SNMP notifications for important OpenFlow events. For the notifications to be
sent correctly, you must also configure SNMP on the device. For more
information about SNMP configuration, see SNMP configuration in Network Management and Monitoring Configuration Guide.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enable SNMP notifications for OpenFlow.

snmp-agent trap enable openflow \[ connect-state ]

By default, SNMP notifications are
enabled for OpenFlow.

## Display and maintenance commands for OpenFlow

Execute display
commands in any view.

 

| Task | Command |
| --- | --- |
| Display the detailed information for an OpenFlow instance. | display openflow instance \[ instance-id ] || Display controller information for an OpenFlow instance. | display openflow instance instance-id { controller \[ controller-id ] \| listened } || Display auxiliary connection information. | display openflow instance instance-id auxiliary \[ controller-id \[ auxiliary auxiliary-id ] ] || Display flow table entries for an OpenFlow instance. | display openflow instance instance-id flow-table \[ table-id ] || Display group table information for an OpenFlow instance. | display openflow instance instance-id group \[ group-id ] || Display meter table information for an OpenFlow instance. | display openflow instance instance-id meter \[ meter-id ] || Display summary OpenFlow instance information. | display openflow summary || Clear statistics on packets that a controller sends and receives for an OpenFlow instance. | reset openflow instance instance-id { controller \[ controller-id ] \| listened } statistics |








‌

## OpenFlow configuration examples

### Example: Configuring OpenFlow in VLAN mode

#### Network configuration

As shown in [Figure 7](#_Ref350844579), an
OpenFlow switch communicates with the controller. Perform the following tasks on
the OpenFlow switch:

·     Create OpenFlow instance 1, associate VLANs 4092
and 4094 with the OpenFlow instance, and activate the OpenFlow instance.

·     Configure the IP address for controller 1 to
have the controller manage the OpenFlow switch.

Figure 7 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705904_x_Img_x_png_6_2216140_294551_0.png)

‌

#### Procedure

\# Create VLANs 4092 and 4094\.

\<Switch\> system-view

\[Switch] vlan 4092

\[Switch-vlan4092] quit

\[Switch] vlan 4094

\[Switch-vlan4094] quit

\# Create OpenFlow instance 1 and associate
VLANs with it.

\[Switch] openflow instance 1

\[Switch-of-inst-1] classification vlan
4092 mask 4093

\# Specify controller 1 for OpenFlow
instance 1 and activate the instance.

\[Switch-of-inst-1] controller 1 address
ip 192.168.49.49

\[Switch-of-inst-1] active instance

\[Switch-of-inst-1] quit

#### Verifying the configuration

\# View detailed information about the
OpenFlow instance.

\[Switch] display openflow instance 1

Instance 1 information:

 

Configuration information:

 Description   : \--

 Active status : Active

 Inactive configuration:

  None

 Active configuration:

  Classification VLAN, total
VLANs(2)

   4092, 4094

...

Port information:

 Ten-GigabitEthernet1/0/3

Active channel information:

 Controller 1 IP address:
192.168.49.49 port: 6633

 

## Appendix A Application restrictions

### Flow entry restrictions

#### Matching restrictions

##### VLAN matching

[Table 2](#_Ref387311239) describes
the VLAN matching restrictions when an OpenFlow instance is associated with
VLANs.

Table 2 VLAN matching

| VLAN | Mask | Matching packets |
| --- | --- | --- |
| \- | \- | All packets in the VLANs that are associated with the OpenFlow instance. || 0 | \- | Packets without a VLAN tag. The PVID of the ingress port must be associated with the OpenFlow instance. || 0 | Value | Unsupported. || Valid VLAN | -/value | Unsupported. || 0x1000 | -/value (except 0x1000) | Unsupported. || 0x1000 | 0x1000 | Packets with a VLAN tag. The VLAN ID of the VLAN tag must be associated with the OpenFlow instance. || Valid VLAN \| 0x1000 | -/value | Matching packets by the combination of the VLAN ID and VLAN mask. The VLANs obtained through the combination of the VLAN ID and VLAN mask must be associated with the OpenFlow instance. || Other | Other | Unsupported. |








‌

##### Protocol packet matching

If protocols are enabled, protocol packets
(except LLDP frames) are processed by the corresponding protocols instead of
the OpenFlow protocol.

For more information about LLDP frame
matching, see "[LLDP frame matching](#_Ref387666497)."

##### Metadata matching

Metadata passes matching information
between flow tables. The controller deploys metadata matching entries only to
non-first flow tables. If the controller deploys a metadata matching entry to
the first flow table, the switch returns an unsupported flow error.

#### Instruction restrictions

Table 3 Instruction restrictions

| Instruction type | Restrictions |
| --- | --- |
| Clear-Actions | The Clear-Actions instruction has the following restrictions:  ·     For the single flow table, the flow entries of the table cannot include this instruction and other instructions at the same time.  ·     For multiple flow tables of the pipeline, only the flow entries of the first flow table can include this instruction and other instructions at the same time. || Apply-Actions | The action list of the Apply-Actions instruction cannot include multiple Output actions.  When the action list includes only one Output action, the switch processes the action list as described in "[Restrictions for merging the action list into the action set](#_Ref387406557)." || Write-Metadata/mask | The flow entries of the last table of the pipeline cannot include this instruction. Otherwise, the switch returns an unsupported flow error. || Goto-Table |




‌

### Restrictions for merging the action list into the action set

The switch follows the following restrictions
to merge the action list into the action set.

#### Non-output actions

When the action set and the action list do not
contain the Output or Group action, the following rules apply:

·     If actions in the action set do not conflict with
actions in the action list, the switch merges the action list into the action set.

·     If actions in the action set conflict with
actions in the action list, actions in the action list are replaced with actions
in the action set.

#### Output actions

When the action set and the action list contain
the Output action or the Group action, the following rules apply:

·     If both the action list and the action set
contain an Output action, the Output action in the action list takes
precedence. The Output action in the action list does not modify the packet. The
Output action in the action set is executed at the last step of the pipeline
processing to modify the packet.

·     If either the action list or the action set
contains an Output action, the port specified by the Output action is treated
as the output port. The actions are executed in the order defined by the action
set rules.

·     If the action list contains an Output action and
the action set contains a Group action, the following rules apply:

¡     The Output
action does not modify the packet. 

¡     The Group
action is executed.

### Packet-out messages restrictions

#### Ingress port

The ingress port must be a physical or
logical port when one of the following reserved ports is the output port in a packet-out
message:

·     Normal. 

·     Local. 

·     In Port. 

·     Controller.

#### Buffer ID co-existing with packet

If a packet-out message contains both the packet
and the buffer ID representing the packet stored in the switch, the switch processes
only the buffered packet. The switch ignores the packet in the message.

#### Packets without a VLAN tag

If the packet contained in a packet-out message
has no VLAN tag, the switch performs the following operations:

·     Tags the packet with the PVID of the ingress
port. 

·     Forwards the packet within the VLAN. 

The switch processes the packet as follows when
the ingress port is a reserved port:

·     If the output port is a physical or logical
port, the switch tags the packet with the PVID of the output port and forwards
the packet within the VLAN.

·     If the output port is the Flood or All reserved
port, the switch processes the packet as described in "[Output port](#_Ref387321795)."

#### Output port

If the output port in a packet-out message is
the Flood or All reserved port, the switch processes the packet contained in
the packet-out message as follows:

·     When the output port is the Flood reserved port:

¡     If
the packet has a VLAN tag, the switch broadcasts the packet within the VLAN.

¡     If
the packet has no VLAN tag and the ingress port is a physical or logical port,
the switch tags the packet with the PVID of the ingress port. The switch then forwards
the packet within the VLAN.

¡     If
the packet has no VLAN tag and the ingress port is the Controller reserved
port, the switch forwards the packet out all OpenFlow ports.

·     When the output port is the All reserved port:

¡     If
the packet has a VLAN tag, the switch broadcasts the packet within the VLAN.

¡     If
the packet has no VLAN tag, the switch forwards the packet out of all OpenFlow
ports regardless of the ingress port type.

### Packet-in messages restrictions

#### Processing VLAN tags

When sending a packet-in message to the
controller, the switch processes the VLAN tag of the packet contained in the packet-out
message as follows:

·     If the VLAN tag of the packet is the same as the
PVID of the ingress port, the switch removes the VLAN tag.

·     If the VLAN tag of the packet is different from
the PVID of the ingress port, the switch does not remove the VLAN tag.

#### Packet buffer

If a packet-in message is sent to
controller due to no matching flow entry, the switch supports buffering the
packet contained in the packet-in message. The buffer size is 1K packets.

If a packet-in message is sent to
controller for other reasons, the switch does not support buffering the packet contained
in the packet-in message. The switch must send the full packet to the
controller, and the cookie field of the packet is set to 0xFFFFFFFFFFFFFFFF.

### LLDP frame matching

LLDP is used to perform topology discovery
in an OpenFlow network. LLDP must be enabled globally on a device. A switch
sends a LLDP frame to the controller through the packet-in message when the
following conditions exist:

·     The port that receives the LLDP frame from the
controller belongs to OpenFlow instances.

·     The flow tables in the OpenFlow instance have a
flow entry that matches the LLDP frame (the output port is the Controller
reserved port).

### Flow table modification messages restrictions

The flow table modification messages have
the following restrictions for the table-miss flow entry and common flow
entries.

#### Table-miss flow entry

The controller deploys the table-miss flow
entry (the action is Drop) to an OpenFlow instance after the OpenFlow instance
is activated.

The controller cannot query the table-miss
flow entry through Multipart messages.

The controller cannot modify the table-miss
flow entry through the Modify request. The controller can only modify the
table-miss flow entry through the Add request.

The controller can modify or delete the
table-miss flow entry only through the strict version of the Modify or Delete
request. The controller cannot modify or remove the table-miss flow entry through
the non-strict version of the Modify or Delete request despite that the match
fields are wildcarded.

The controller deploys a table-miss flow
entry (the action is Drop) to an OpenFlow instance after the current table-miss
flow entry is deleted.

#### Common flow entries

The controller cannot modify or remove all common
flow entries through the non-strict version of the Modify or Delete request despite
that the match fields are wildcarded.

## Appendix BMAC-IP flow table

### Capabilities supported by the MAC-IP flow table

The controller must include the required
match fields and actions and can include the optional match fields and actions in
the flow entries deployed to the MAC-IP flow table. If the controller does not
include the optional match fields and actions in the flow entries, the switch
adds them to the flow entries by default.

The Layer 2 flow entries are implemented by
using MAC address entries. [Table 4](#_Ref387328418)
describes the capabilities supported by Layer 2 flow entries.

Table 4 Capabilities supported by Layer 2
flow entries

| Item | Capabilities |
| --- | --- |
| Required match fields | The MAC-IP flow table must support the following match fields:  ·     VLAN ID.  ·     Unicast destination MAC address. || Optional match fields | N/A || Required actions | Specifying the output port. |



‌

The Layer 3 flow entries are implemented by
using routing entries. [Table 5](#_Ref401590227)
describes the capabilities supported by Layer 3 flow entries.

Table 5 Capabilities supported by Layer 3
flow entries

| Item | Capabilities |
| --- | --- |
| Required match fields | The MAC-IP flow table must support the following match fields:  ·     VLAN ID.  ·     Unicast destination IP address.  ·     Unicast destination MAC address, which must be the MAC address of the VLAN interface for the VLAN that is matched. || Optional match fields | N/A || Required actions | Specifying the output port. |



‌

The VXLAN Layer 2 flow entries are
implemented by using MAC address entries. [Table 6](#_Ref402875822)
describes the capabilities supported by VXLAN Layer 2 flow entries.

Table 6 Capabilities supported by VXLAN Layer
2 flow entries

| Item | Capabilities |
| --- | --- |
| Required match fields | Unicast destination MAC address. || Optional match fields | N/A || Required actions | The MAC-IP flow table must support the following actions:  ·     Specifying a tunnel interface as the output port.  ·     Specifying the tunnel ID. || Optional actions | The MAC-IP flow table can optionally support the following instructions:  ·     Goto-Table—When the switch has multiple tables, the switch adds this instruction by default if the controller does not deploy it.  ·     Write-Metadata—When the switch has multiple tables, the switch adds this instruction by default if the controller does not deploy it. |




‌

The VXLAN Layer 3 flow entries are
implemented by using routing entries. [Table 7](#_Ref402875829)
describes the capabilities supported by VXLAN Layer 3 flow entries.

Table 7 Capabilities supported by VXLAN Layer
3 flow entries

| Item | Capabilities |
| --- | --- |
| Required match fields | The MAC-IP flow table must support the following match fields:  ·     Ethernet type.  ·     Unicast destination IP address. || Optional match fields | N/A || Required actions | The MAC-IP flow table must support the following actions:  ·     Specifying a tunnel interface as the output port.  ·     Specifying the tunnel ID.  ·     Modifying the destination MAC address. || Optional actions | The MAC-IP flow table can optionally support the following actions:  ·     Goto-Table—When the switch has multiple tables, the switch adds this instruction by default if the controller does not deploy it.  ·     Write-Metadata—When the switch has multiple tables, the switch adds this instruction by default if the controller does not deploy it. |




‌

### MAC-IP flow table restrictions

Controller must follow the restrictions in
the following tables to deploy flow entries for MAC-IP flow table. Otherwise,
forwarding failure might occur.

Table 8 Restrictions for deploying Layer 2
flow entries for the MAC-IP flow table

| Item | Restrictions |
| --- | --- |
| Match fields | The destination MAC address cannot be the MAC address of the switch to which the flow entry is deployed. || Actions | The output port must belong to the VLAN that is matched. |


‌

Table 9 Restrictions for deploying Layer 3
flow entries for the MAC-IP flow table

| Item | Restrictions |
| --- | --- |
| Match fields | The VLAN interface of the VLAN that is matched is in up state.  The destination MAC address is the MAC address of the VLAN interface for the VLAN that is matched.  The destination IP address cannot be the IP address of the switch to which the flow entry is deployed. || Actions | The specified output port must belong to the destination VLAN.  The destination MAC address cannot be the MAC address of the switch to which the flow entry is deployed.  If the switch modifies the source MAC address, the source MAC address must be the MAC address of the VLAN interface for the VLAN to which the output port belongs. |


‌

To deploy a Layer 3 flow entry, make sure the
following requirements are met:

·     The VLAN interface of the matched VLAN is in up
state.

·     The switch sends the controller a packet that
indicates the VLAN interface acts as an OpenFlow port. The link state and the
MAC address of the VLAN interface are also included in the packet.

The switch reports the VLAN interface
deletion to the controller and the controller removes the corresponding Layer 3
flow entry.

The controller ensures the correctness of
Layer 3 flow entries. The switch does not check for the restrictions for Lay 3
flow entries.

Table 10 Restrictions for deploying VXLAN Layer
2 flow entries for the MAC-IP flow table

| Item | Restrictions |
| --- | --- |
| Match fields | The destination MAC address cannot be MAC address of the switch to which the flow entry is deployed. || Actions | The output port must be an existing tunnel interface.  The VXLAN that corresponds to the specified tunnel ID (VNI) must exist. |


‌

Table 11 Restrictions for deploying VXLAN Layer
3 flow entries for the MAC-IP flow table

| Item | Restrictions |
| --- | --- |
| Match fields | The destination IP address cannot be the IP address of the switch to which the flow entry is deployed. || Actions | The output port must be an existing tunnel interface.  The VXLAN and the VSI interface that correspond to the specified tunnel ID (VNI) must exist.  The destination MAC address cannot be MAC address of the switch to which the flow entry is deployed. |


‌

### Table-miss flow entry of MAC-IP flow tables

The table-miss flow entry of a MAC-IP flow
table supports the following output actions:

·     Goto-Table—Direct the packet to the next table.

·     Drop—Drop the packet.

·     Controller—Send the packet to the controller.

·     Normal—Forward the packet to the normal pipeline.

### Dynamic aware

On an OpenFlow switch that supports MAC-IP
flow tables, you can configure OpenFlow to support querying and deleting
dynamic MAC address flow entries.

The controller can query and delete dynamic
MAC address flow entries by specifying a VLAN, a MAC address, or the
combination of a MAC address and a VLAN.

### MAC-IP flow table cooperating with extensibility flow table

#### Metadata/mask

The MAC-IP flow table supports the Write
Metadata/mask instruction and the extensibility flow table supports metadata/mask
matching. The MAC-IP flow table can cooperate with an extensibility flow table to
perform the pipeline process of multiple tables by using metadata/mask.

Each metadata mask bit has a different meaning.
The corresponding metadata bit being set indicates that the metadata mask bit
is matched. When the corresponding metadata bit is not set, the metadata mask
bit is wildcarded.

Table 12 Metadata mask meanings

| Metadata mask bit | Meaning | Metadata |
| --- | --- | --- |
| Bit 0 | Destination MAC address | ·     1—Set. Matches the destination MAC address.  ·     0—Not set. Does not match the destination MAC address. || Bit 1 | Source MAC address | ·     1—Set. Matches the source MAC address.  ·     0—Not set. Does not match the source MAC address. || Bit 2 | Destination IP address | ·     1—Set. Matches the destination IP address.  ·     0—Not set. Does not match the destination IP address. || Others | Reserved | Reserved. |




‌

#### Matching restrictions

When the output action in an extensibility
flow table is not Normal, the following rules apply: 

·     The MAC-IP flow table does not take effect. 

·     All actions are executed according to the extensibility
flow table.

When the output action in an extensibility flow
table is Normal, the following rules apply: 

·     The output action is executed according to the
MAC-IP flow table. 

·     The other actions are executed according to the extensibility
flow table.

### Capabilities supported by the VLAN tagging flow table

The controller must include the required
match fields and actions and can include the optional match fields and actions
in the flow entries deployed to the VLAN tagging flow table. If the controller
does not include the optional match fields and actions in the flow entries, the
switch adds them to the flow entries by default.

[Table 13](#_Ref439856014) describes
the capabilities supported by the flow entries in the VLAN tagging flow table.

Table 13 Capabilities supported by flow
entries in the VLAN tagging flow table

| Item | Capabilities |
| --- | --- |
| Required match fields | The VLAN tagging flow table must support the following match fields:  ·     input-port.  ·     vlan. || Optional match fields | N/A || Required actions | The following actions in the action list of the Apply-Actions instruction must be applied immediately:  ·     Push-Tag.  ·     Set-Field (VLAN). || Optional actions | The VLAN tagging flow table can optionally support the following actions:  ·     Output (normal).  ·     Goto-Table. |




‌

The Push-Tag and Set-Field (VLAN) actions must
be in the action list of the Apply-Actions instruction. The Push-Tag and Set-Field
(VLAN) actions can be used as follows:

·     Push-Tag \+ Set-Field (value1)—Adds a VLAN tag value1.

·     Set-Field (value1) \+ Push-Tag
\+ Set-Field (value2)—Modifies the VLAN tag of
the packet to value1 and adds a VLAN tag value2.

·     Push-Tag \+ Set-Field (value1)
\+ Push-Tag \+ Set-Field (value2)—Adds inner VLAN
tag value2 and outer VLAN tag value1.

The Goto-Table
instruction is optional and does not take effect. The flow table specified by
this instruction can only be the next of the VLAN tagging flow table.

### Capabilities supported by the VLAN untagging flow table

Table 14 Capabilities supported by flow entries in the VLAN untagging flow
table

| Item | Capabilities |
| --- | --- |
| Required match fields | The VLAN untagging flow table must support the following match fields:  ·     egress port—Matches the egress port of packets.  ·     vlan—Matches the outer VLAN tag of packets. || Optional match fields | The VLAN untagging flow table can optionally support the inner vlan match field that matches the inner VLAN tag of double-tagged packets. || Required actions | The following actions in the action list of the Apply-Actions instruction must be applied immediately:  ·     Pop-Tag.  ·     Set-Field (VLAN). || Optional actions | The VLAN untagging flow table can optionally support the Output (normal) action. |




‌

The VLAN untagging flow table applies only
to double-tagged packets.

The egress port
and inner vlan are extended match fields that use
the Experimenter ID H3C (0xFE2) and take the private match field values 47 and
48, respectively. To deploy flow entries that contain the extended match fields,
make sure the controllers are developed to be compatible with the Experimenter
ID and the extended match fields.

The Pop-Tag and Set-Field (VLAN) actions
must be in the action list of the Apply-Actions instruction. The Pop-Tag and Set-Field
(VLAN) actions can be used as follows:

·     Pop-Tag—Removes the outer VLAN tag.

·     Pop-Tag \+ Pop-Tag—Removes the inner and outer VLAN tags.

·     Pop-Tag \+ Set-Field (value)—Removes the outer VLAN tag and modifies the inner VLAN tag to value.

### Multi-level table-based TTP pipeline

A TTP pipeline can include multiple types
of flow tables and the tables can be cascaded. [Figure 8](#_Ref39127805) shows
the forwarding workflow of a TTP pipeline.

Figure 8 Multi-level table-based TTP
pipeline

![](https://resource.h3c.com/en/202407/12/20240712_11705905_x_Img_x_png_7_2216140_294551_0.png)

 

### Table-miss flow entry of TTP flow tables

The table-miss flow entry of a TTP flow
table supports the following output actions:

·     Normal—Forward the packet to the normal pipeline. 

·     Goto-table-50—Direct the packet to table 50 (the TTP-Bridging flow table).

·     Controller—Send the packet to the controller.

[Table 15](#_Ref39074483)
describes the instruction supported by the table-miss flow entry of different
types in TTP flow tables. The instruction in the table-miss entry of TTP flow
tables 0 through 50 cannot be modified.

Table 15 Instruction supported by the
table-miss flow entry of TTP flow tables

| Table ID | TTP flow table | Instruction in the table-miss entry || 0 | TTP-ingress-port | Normal || 10 | TTP-VLAN | Normal || 20 | TTP-termination-MAC | Goto-table-50 || 30 | TTP-unicast-routing | Normal || 50 | TTP-bridging | Controller || 60 | TTP-ingress-ACL | N/A || 61 | TTP-egress-ACL | N/A |







| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |

 

### Capabilities supported by the TTP-ingress-port flow table

A TTP-ingress-port flow entry is used to
identify the type of the input port for a packet and direct the packet to
another table. 

·     Packets arriving at a physical interface are
directed to the TTP-VLAN flow table. 

·     Packets arriving at a VXLAN tunnel interface are
directed to a TTP-Bridging flow table and added the VXLAN ID information in metadata.

Table 16 Capabilities supported by TTP-ingress-port
flow entries (for packets arriving at physical
interfaces)

| Item | Capabilities | || Required match fields | N/A |  || Optional match fields | In-Port—Type of the input port (physical interface). |  || Required actions | Goto-table-10—Directs the packet to table 10 (TTP-VLAN flow table). |  || Optional actions | N/A |  |




| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |

 

Table 17 Capabilities supported by TTP-ingress-port
flow entries (for packets arriving at VXLAN tunnel interfaces)

| Item | Capabilities || Required match fields | N/A || Optional match fields | ·     In-Port—Type of the input port (VXLAN tunnel interface).  ·     Tunnel ID—VNI. || Required actions | ·     Write-metadata—Writes VNI and VRF information.  ·     Goto-table-50—Directs the packet to table 50 (TTP-bridging flow table). || Optional actions | N/A |




| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |

 

·     In-Port—Matches the type of the input port. It contains 4 bytes, where the
former 2 bytes indicates the interface type. The following interface types are
supported:

¡     0x0000—Physical interface at the user
side.

¡     0x0001—VLAN tunnel interface on an
overlay network.

·     Metadata—Transmits the VNI and VRF index information in a pipeline. This
field contains 64 bytes, where the lower-order 32 bytes indicate a VNI and the
higher-order 32 bytes indicate a VRF index.

### Capabilities supported by the TTP-VLAN flow table

A TTP-VLAN flow entry is used to match
untagged or tagged packets arriving at a physical interface, direct the packets
to the TTP-termination-MAC flow table, and transmit VXLAN ID information.

Table 18 Capabilities supported by TTP-VLAN flow
entries (for untagged packets)

| Item | Capabilities || Required match fields | N/A || Optional match fields | In-Port—Type of the input port (physical interface). || Required actions | ·     Write-metadata—Writes the VNI information.  ·     Goto-table-20—Directs the packet to table 20 (TTP-termination-MAC flow table). || Optional actions | N/A |




| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |

 

Table 19 Capabilities supported by TTP-VLAN flow entries (for tagged packets)

| Item | Capabilities || Required match fields | N/A || Optional match fields | ·     In-Port—Type of the input port (physical interface).  ·     VLAN ID—VLAN ID. || Required actions | ·     Write-metadata—Writes the VNI information.  ·     Goto-table-20—Directs the packet to table 20 (TTP-termination-MAC flow table). || Optional actions | N/A |




| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |

 

### Capabilities supported by the TTP-termination-MAC flow table

A TTP-termination-MAC flow entry is used to
select Layer 2 or 3 forwarding based on the destination MAC address of a packet,
direct the packet to a flow table, and transmit the VRF information.

Table 20 Capabilities supported by TTP-termination-MAC flow entries

| Item | Capabilities || Required match fields | N/A || Optional match fields | ·     ETH\_TYPE—Ethernet type. Value 0x800 indicates Ethernet.  ·     Metadata—VNI.  ·     ETH\_DST—Destination MAC address (the MAC address of the local gateway). || Required actions | ·     Write-metadata—Writes VRF index information. Value 0 indicates the public network.  ·     Goto-table—Directs the packet to a table.  ¡     If the packet matches the value of the ETH\_DST field, the switch directs the packet to table 30 (TTP-unicast-routing flow table) for Layer 3 forwarding.  ¡     If the packet does not match the value of the ETH\_DST field, the switch directs the packet to table 50 (TTP-bridging flow table) for Layer 2 forwarding. || Optional actions | N/A |




| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |

 

### Capabilities supported by the TTP-unicast-routing flow table

A TTP-unicast-routing flow entry is used to
forward Layer 3 packets.

Table 21 Capabilities supported by TTP-unicast-routing flow entries (for normal
IPv4 unicast packets)

| Item | Capabilities || Required match fields | N/A || Optional match fields | ·     ETH\_TYPE—Ethernet type. Value 0x800 indicates Ethernet.  ·     Metadata—VNI.  ·     IP\_DST—Destination IP address. || Required actions | The TTP-unicast-routing flow table must support the following actions:  ·     Write-actions—Writes actions in to the action set.  ¡     Set L3\_Unicast\_Group\_ID—Specifies actions in a Layer 3 unicast group entry ID. For more information about Layer 3 unicast group entries, see "[Capabilities supported by the TTP-L3-unicast-group flow table](#_Ref39080601)."  ¡     Set L3\_ECMP\_Group\_ID—Specifies actions in a Layer 3 ECMP group entry ID. For more information about Layer 3 ECMP group entries, see "[Capabilities supported by the TTP-L3-ECMP-group table](#_Ref39080610)."  ·     Set TTL—Decreases the TTL by 1\.  ·     Goto-table-60—Directs the packet to table 60 (TTP-ingress-ACL flow table). || Optional actions | N/A |




| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |

 

Table 22 Capabilities supported by TTP-unicast-routing flow entries (for IPv4
unicast packets with special destination IP addresses)

| Item | Capabilities || Required match fields | N/A || Optional match fields | ·     ETH\_TYPE—Ethernet type. Value 0x800 indicates Ethernet.  ·     Metadata—VNI.  ·     IP\_DST—Destination IP address (a subnet or an IP address of the switch). || Required actions | Controller—Sends the packet to the controller. || Optional actions | N/A |




| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |

 

### Capabilities supported by the TTP-bridging flow table

A TTP-bridging flow entry is used to forward
a Layer 2 packet, determine the type of the output port based on the
destination MAC address of the packet, and perform one of the following
actions:

·     If the output port is a local physical
interface, the switch tags or untags the packet before forwarding.

·     If the output port is a VXLAN tunnel interface,
the switch sets the VNI for the packet and untags the packet before forwarding.

Table 23 Capabilities supported by TTP-bridging flow entries

| Item | Capabilities || Required match fields | N/A || Optional match fields | ·     ETH\_DST—Destination MAC address.  ·     Metadata—VNI. || Required actions | ·     Write-actions—Writes actions in to the action set by using the Set L2\_Interface\_Group\_ID field. For more information about the action set, see "[Capabilities supported by the TTP-L2-interface-group flow table](#_Ref39080704)."  ·     Goto-table-60—Directs the packet to table 60 (TTP-ingress-ACL flow table). || Optional actions | N/A |




| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |

 

### Capabilities supported by the TTP-ingress-ACL flow table

The TTP-ingress-ACL flow entry is used to
rate limit, redirect, mirror, or discard matching packets in the inbound
direction or send the packets to the controller.

The TTP-ingress-ACL flow table does not
contain a table-miss entry and executes all actions in the action set on a packet
that does not match any flow entry.

Table 24 Capabilities supported by TTP-ingress-ACL flow entries (for LLDP and
ARP packets)

| Item | Capabilities || Required match fields | N/A || Optional match fields | ETH\_TYPE—Ethernet type. || Required actions | Controller—Sends the packet to the controller. || Optional actions | N/A |




| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |

 

Table 25 Capabilities supported by TTP-ingress-ACL flow entries (for DHCP
packets)

| Item | Capabilities || Required match fields | N/A || Optional match fields | ·     ETH\_TYPE—Ethernet type.  ·     IP\_PROTO—IP protocol type.  ·     UDP\_SRC—Source UDP port number.  ·     UDP\_DST—Destination UDP port number. || Required actions | Controller—Sends the packet to the controller. || Optional actions | N/A |




| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |

 

Table 26 Capabilities supported by TTP-ingress-ACL flow entries (for all
packets)

| Item | Capabilities || Required match fields | N/A || Optional match fields | ·     In-Port—Input port.  ·     Metadata—VNI or VRF.  ·     IPv4SRC—Source IPv4 address.  ·     IPv4DST—Destination IPv4 address.  ·     IP\_PROTO—IP protocol type.  ·     TCP\_SRC/UDP\_SRC—Source TCP or UDP port number.  ·     TCP\_DST/UDP\_DST—Destination TCP or UDP port number. || Required actions | A TTP-ingress-ACL flow entry must contain one of the following actions:  ·     Write meter-ID—Sets a meter entry for rate limit.  ·     Clear-actions \+ Output port number—Clears the action set and specifies an output port for packet redirection.  ·     Output port number—Specifies an output port to mirror the packet.  ·     Clear-actions—Clears the action set to discard the packet. || Optional actions | N/A |




| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |

 

### Capabilities supported by the TTP-egress-ACL flow table

A TTP-egress-ACL flow entry is used to rate
limit, mirror, or discard matching packets in the outbound direction.

The TTP-egress-ACL flow table does not
contain a table-miss entry and uses the normal forwarding process to forward a
packet that does not match any flow entry.

All packets processed by the
TTP-ingress-ACL flow table will be directed to the TTP-egress-ACL flow table
for processing by default.

Table 27 Capabilities supported by TTP-egress-ACL flow entries

| Item | Capabilities || Required match fields | N/A || Optional match fields | ·     Metadata—VNI.  ·     IPv4SRC—Source IPv4 address.  ·     IPv4DST—Destination IPv4 address.  ·     IP\_PROTO—IP protocol type.  ·     TCP\_SRC/UDP\_SRC—Source TCP or UDP port number.  ·     TCP\_DST/UDP\_DST—Destination TCP or UDP port number. || Required actions | A TTP-egress-ACL flow entry must contain one of the following actions:  ·     Write meter-ID—Sets a meter table for rate limit.  ·     Output port number—Specifies an output port to mirror the packet.  ·     Clear-actions—Clears the action set to discard the packet. || Optional actions | N/A |




| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |

 

### Capabilities supported by the TTP-L2-interface-group flow table

A TTP-L2-interface-group flow entry is used
to tag or untag a packet and set the VXLAN ID of the packet.

For a TTP-L2-interface-group flow entry,
the group type is indirect and the group entry contains only one action bucket.

Table 28 Capabilities supported by TTP-L2-interface-group flow entries (for VXLAN
interfaces)

| Item | Capabilities || Required actions | ·     Set Tunnel\_ID—Sets the VNI.  ·     POP VLAN—Untags the packet. Do not execute this action on an untagged packet.  ·     Output-port-number—Sets the output port to the VXLAN tunnel interface. || Optional actions | N/A |


| --- | --- | --- | --- | --- | --- |

 

Table 29 Capabilities supported by TTP-L2-interface-group flow entries (for
untagging physical interfaces)

| Item | Capabilities || Required actions | ·     POP VLAN—Untags the packet. Do not execute this action on an untagged packet.  ·     Output-port-number—Sets the output port. || Optional actions | N/A |


| --- | --- | --- | --- | --- | --- |

 

Table 30 Capabilities supported by TTP-L2-interface-group flow entries (for
tagging physical interfaces)

| Item | Capabilities || Required actions | ·     PUSH VLAN—Tags the packet with a VLAN ID.  ·     POP VLAN—Untags the packet. Do not execute this action on an untagged packet.  ·     Output-port-number—Sets the output port. || Optional actions | N/A |


| --- | --- | --- | --- | --- | --- |

 

### Capabilities supported by the TTP-L3-unicast-group flow table

A TTP-L3-unicast-group flow table is used
to perform the following actions on a packet:

·     Set the destination MAC address and source MAC
address.

·     Specify a TTP-L2-interface-group entry.

In a TTP-L3-unicast-group flow entry, the group
type is indirect and the group entry contains only one action bucket.

Table 31 Capabilities supported by TTP-L3-unicast-group flow entries

| Item | Capabilities || Required actions | ·     Set ETH\_DST—Sets the destination MAC address.  ·     Set ETH\_SRC—Sets the source MAC address.  ·     Set L2\_Interface\_Group\_ID—Specifies a TTP-L2-interface-group flow table. || Optional actions | N/A |


| --- | --- | --- | --- | --- | --- |

 

### Capabilities supported by the TTP-L3-ECMP-group table

A TTP-L3-ECMP-group table is used to direct
a packet to multiple TTP-L3-unicast-group entries for processing.

For a TTP-L3-ECMP-group entry, the group
type is select, and the group entry contains multiple action buckets.

Table 32 Capabilities supported by TTP-L3-ECMP-group flow entries

| Item | Capabilities || Required actions | ·     Bucket 1:{Set L3\_Unicast\_Group\_ID}  ·     Bucket 2:{Set L3\_Unicast\_Group\_ID}  ·     …  ·     Bucket n:{Set L3\_Unicast\_Group\_ID} || Optional actions | N/A |


| --- | --- | --- | --- | --- | --- |

 

 

