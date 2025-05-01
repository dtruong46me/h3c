
# Configuring Smart Link

## About Smart Link

### Application scenario

Smart Link is an H3C-proprietary protocol
thatprovides link redundancy and subsecond
convergence time in a dual uplink network. As shown in [Figure 1](#_Ref226536640), Smart
Link is configured on Device C and Device D. The secondary link takes over
quickly when the primary link fails.

Figure 1 Dual uplink network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705595_x_Img_x_png_0_2216099_294551_0.png)

 

A Smart Link network has the following devices:

·     Smart Link devices—A Smart Link device has two uplinks. A Smart Link device must be
configured with a smart link group and a transmit control VLAN to transmit flush
messages. Device C and Device D in [Figure 1](#_Ref226536640) are
Smart Link devices.

·     Associated devices—An associated device is an uplink device to which Smart Link devices
are connected. An associated device supports Smart Link, and receives flush
messages sent from the specified control VLAN. When a primary/secondary link
switchover occurs, the associated device updates the MAC address entries and
ARP/ND entries according to received flush messages. Device A, Device B, and
Device E in [Figure 1](#_Ref226536640) are associated devices.

### Terminology

#### Smart link group

A smart link group consists of only two
member ports: the primary and the secondary ports. Only one port is active for
forwarding at a time, and the other port is blocked and in standby state. When
link failure occurs on the active port due to port shutdown or the presence of
unidirectional link, the standby port becomes active and takes over. The
original active port transits to the blocked state.

As shown in [Figure 1](#_Ref226536640), Port C1 and Port C2 of Device C form a smart link group. Port C1 is
active, and Port C2 is standby. Port D1 and Port D2 of Device D form another
smart link group. Port D1 is active, and Port D2 is standby.

#### Primary/secondary port

Primary port and secondary port are two port
types in a smart link group. When both ports in a smart link group are up, the primary
port preferentially transits to the forwarding state. The secondary port stays
in standby state. When the primary port fails, the secondary port takes over to
forward traffic.

As shown in [Figure 1](#_Ref226536640), Port C1 of Device C and Port D1 of Device D are primary ports. Port
C2 of Device C and Port D2 of Device D are secondary ports.

#### Primary/secondary link

The link that
connects the primary port in a smart link group is the primary link. The link
that connects the secondary port is the secondary link.

#### Flush message

When link switchover occurs, the smart link
group uses flush messages to notify other devices to refresh their MAC address
entries and ARP/ND entries. Flush messages are common multicast data packets,
and will be dropped by a blocked receiving port.

#### Protected VLAN

A smart link group controls the forwarding
state of protected VLANs. Each smart link group on a port controls a different
protected VLAN. The state of the port in a protected VLAN is determined by the
state of the port in the smart link group.

#### Transmit control VLAN

The transmit control VLAN is used for
transmitting flush messages. When link switchover occurs, the devices (such as
Device C and Device D in [Figure 1](#_Ref226536640)) send flush messages within the transmit control VLAN.

#### Receive control VLAN

The receive control VLAN is used for
receiving and processing flush messages. When link switchover occurs, the
devices (such as Device A, Device B, and Device E in [Figure 1](#_Ref226536640)) receive and process flush messages in the receive control VLAN. In
addition, they refresh their MAC address entries and ARP/ND entries.

### How Smart Link works

#### Link backup

As shown in [Figure 1](#_Ref226536640), the link on Port C1 of Device C is the primary link. The link on Port
C2 of Device C is the secondary link. Port C1 is in forwarding state, and Port
C2 is in standby state. When the primary link fails, Port C2 takes over to
forward traffic and Port C1 is blocked and placed in standby state.

When a port switches to the forwarding
state, the system outputs log information to notify the user of the port state
change.

#### Topology change

Link switchover might outdate the MAC address
entries and ARP/ND entries on all devices. A flush update mechanism is provided
to ensure correct packet transmission. With this mechanism, a Smart Link-enabled
device updates its information by transmitting flush messages over the backup
link to its upstream devices. This mechanism requires the upstream devices to
be capable of recognizing Smart Link flush messages to update their MAC address
forwarding entries and ARP/ND entries.

#### Preemption mode

As shown in [Figure 1](#_Ref226536640),
the link on Port C1 of Device C is the primary link. The link on Port C2 of
Device C is the secondary link. When the primary link fails, Port C1 is
automatically blocked and placed in standby state, and Port C2 takes over to
forward traffic. When the primary link recovers, one of the following actions occurs:

·     If the smart link group is not configured with a
preemption mode, Port C1 stays blocked to keep traffic forwarding stable. Port
C1 does not take over to forward traffic until the next link switchover.

·     If the smart link group is configured with a preemption
mode and the preemption conditions are met, Port C1 takes over to forward
traffic as soon as its link recovers. Port C2 is automatically blocked and
placed in standby state.

#### Load sharing

A ring network might carry traffic of
multiple VLANs. Smart Link can forward traffic from different VLANs in
different smart link groups for load sharing.

To implement load sharing, you can assign a
port to multiple smart link groups. Configure each group with a different
protected VLAN. Make sure the state of the port is different in these smart
link groups, so traffic from different VLANs can be forwarded along different
paths.

You can configure protected VLANs for a
smart link group by referencing Multiple Spanning Tree Instances (MSTIs). For
more information about MSTIs, see Layer 2—LAN Switching Configuration
Guide.

### Collaboration between Smart Link and Monitor Link for port status detection

Smart Link cannot detect when faults occur
on the uplink of the upstream devices or when faults are cleared. You can
configure the Monitor Link function to monitor the status of the uplink ports
of the upstream devices. Monitor Link adapts the up/down state of downlink
ports to uplink ports, and triggers Smart Link to perform link switchover on
the downstream device. For more information about Monitor Link, see "Configuring
Monitor Link."

### Collaboration between Smart Link and Track for link status detection

Smart Link cannot detect unidirectional
links, misconnected fibers, or packet loss on intermediate devices or network
paths of the uplink. It cannot detect when faults are cleared either. To detect
link status, smart link group member ports must use link detection protocols.
When a fault is detected or cleared, the link detection protocols inform Smart
Link to switch over the links.

Smart Link collaborates with link detection
protocols through track entries. It supports only the Continuity Check (CC)
function of Connectivity Fault Detection (CFD) to implement link detection. CFD
notifies the smart link group member ports of fault detection events by using detection
VLANs and detection ports. A port responds to a continuity check event only
when the control VLAN of the smart link group to which it belongs matches the
detection VLAN. For more information about track entries and the CC function of
CFD, see "Configuring Track" and "Configuring CFD."

## Restrictions and guidelines: Smart Link configuration

When you configure Smart Link, follow these
restrictions and guidelines:

·     If you configure a port as both an aggregation
group member and a smart link group member, only the aggregation group
configuration takes effect. The port is not shown in the output from the display
smart-link group command. The smart link group
configuration takes effect after the port leaves the aggregation group.

·     In a M-LAG network:

¡     The
two M-LAG member devices must have the same Smart Link settings.

¡     For
a smart link group to work with an M-LAG system, assign only M-LAG interfaces
to the smart link group.

¡     Do
not assign a peer-link interface to a smart link group.

## Smart Link tasks at a glance

To configure Smart Link, perform the
following tasks:

**1\.**[Configuring a Smart Link device](#_Ref326591234)

**a.**[Configuring protected VLANs for a smart
link group](#_Ref326591280)

**b.**[Configuring member ports for a smart
link group](#_Ref326591285)

**c.**(Optional.) [Configuring a preemption mode for a
smart link group](#_Ref326591291)

**d.**(Optional.) [Enabling the sending of flush messages](#_Ref326591296)

**e.**(Optional.) [Enabling the sending of dummy messages](#_Ref114239102)

**f.**(Optional.) [Enabling the sending of dummy messages](#_Ref326591299)

[**g.**Restrictions and
guidelines](#_Ref326591299)

[After you enable
this feature, the device can send the MAC addresses learned from downlink ports
to the peer through dummy messages. The device performs such actions when the
state of ports in the smart link group changes from standby to active.](#_Ref326591299) 

#### [Restrictions and guidelines](#_Ref326591299)

[Sending of flush
messages and sending of dummy messages are mutually exclusive.](#_Ref326591299)

#### [Procedure](#_Ref326591299)

[**1\.**Enter system view.](#_Ref326591299)

[system-view](#_Ref326591299)

[**2\.**Enter smart link group view.](#_Ref326591299)

[smart-link group group-id](#_Ref326591299)

[**3\.**Enable sending of dummy messages.](#_Ref326591299)

[dummy enable \[ max-number number
] \[ times times ]](#_Ref326591299)

[By default, sending
of dummy messages is enabled.](#_Ref326591299)

[**a.**Configuring collaboration between Smart Link
and Track](#_Ref326591299)

**4\.**[Enabling an associated device to receive
flush messages](#_Ref475603268)

## Configuring a Smart Link device

### Prerequisites for Smart Link device configuration

Before configuring a Smart Link device,
complete the following tasks:

·     To prevent loops, shut down a port before configuring
it as a smart link group member. You can bring up the port only after completing
the smart link group configuration.

·     Disable the spanning tree feature, RRPP, and
ERPS on the ports you want to add to the smart link group. 

### Configuring protected VLANs for a smart link group

#### Prerequisites

Before you configure protected VLANs, you
must configure an MST region and the VLAN-to-instance mapping table. For more
information about MST regions, see spanning tree configuration in Layer 2—LAN Switching Configration Guide.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Create a smart link group and enter smart
link group view.

smart-link group group-id

**3\.**Configure protected VLANs for the smart link
group.

protected-vlan reference-instance instance-id-list

### Configuring member ports for a smart link group

#### Restrictions and guidelines

You can configure member ports for a smart
link group either in smart link group view or in interface view. The
configurations made in these two views have the same effect.

#### In smart link group view

**1\.**Enter system view.

system-view

**2\.**Create a smart link group and enter smart
link group view.

smart-link group group-id

**3\.**Configure member ports for a smart link
group.

port interface-type
interface-number { primary \| secondary }

By default, no member port is configured
for a smart link group.

#### In interface view

**1\.**Enter system view.

system-view

**2\.**Enter Layer 2 Ethernet interface view or
Layer 2 aggregate interface view.

interface interface-type
interface-number

**3\.**Configure member ports for a smart link
group.

port smart-link group group-id { primary \| secondary }

By default, an interface is not a smart
link group member.

### Configuring a preemption mode for a smart link group

**1\.**Enter system view.

system-view

**2\.**Enter smart link group view.

smart-link group group-id

**3\.**Configure a preemption mode for the smart
link group.

preemption mode { role \| speed \[ threshold threshold-value ] }

By default, preemption is disabled.

**4\.**Configure the preemption delay.

preemption delay delay

By default, the preemption delay is 1
second.

The preemption delay configuration takes
effect only after a preemption mode is configured.

### Enabling the sending of flush messages

#### Restrictions and guidelines

·     The control VLAN configured for a smart link
group must be different from the control VLAN configured for any other smart
link group.

·     Make sure the configured control VLAN already
exists, and assign the smart link group member ports to the control VLAN.

·     The control VLAN of a smart link group must also
be one of its protected VLANs. Do not remove the control VLAN. Otherwise, flush
messages cannot be sent correctly.

#### Restrictions and guidelines

Sending of flush messages and sending of
dummy messages are mutually exclusive.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter smart link group view.

smart-link group group-id

**3\.**Enable flush update.

flush enable \[ control-vlan vlan-id ]

By default, flush update is enabled, and
VLAN 1 is the control VLAN.

### Enabling the sending of dummy messages

#### Restrictions and guidelines

After you enable this feature, the device
can send the MAC addresses learned from downlink ports to the peer through dummy
messages. The device performs such actions when the state of ports in the smart
link group changes from standby to active. 

#### Restrictions and guidelines

Sending of flush messages and sending of
dummy messages are mutually exclusive.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter smart link group view.

smart-link group group-id

**3\.**Enable sending of dummy messages.

dummy enable \[ max-number number ] \[ times times ]

By default, sending of dummy messages is
enabled.

### Configuring collaboration between Smart Link and Track

#### About this task

Smart Link collaborates with the CC function
of CFD through track entries to implement link detection. 

#### Prerequisites

Before you configure collaboration between
Smart Link and Track on a port, you must assign the port to the smart link
group.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter Layer 2 Ethernet interface view or
Layer 2 aggregate interface view.

interface interface-type
interface-number

**3\.**Configure collaboration between Smart Link
and Track on the port.

port smart-link group group-id track track-entry-number

By default, smart link group member ports
do not collaborate with track entries.

## Enabling an associated device to receive flush messages

#### Restrictions and guidelines

·     You do not need to enable all ports on the
associated devices to receive flush messages. Enable the feature only on all control
VLANs of ports on the primary and secondary links between the Smart Link device
and the destination device.

·     If no control VLAN is specified for processing
flush messages, the device forwards the received flush messages without any processing.

·     Make sure the receive control VLAN is the same
as the transmit control VLAN configured on the Smart Link device. If they are
not the same, the associated device will forward the received flush messages
directly without any processing.

·     Do not remove the control VLANs. Otherwise,
flush messages cannot be sent correctly.

·     Make sure the control VLANs are existing VLANs,
and assign the ports capable of receiving flush messages to the control VLANs.

#### Prerequisites

Disable the spanning tree feature on the
associated device's ports that connect to the member ports of the smart link
group. Otherwise, the ports will discard flush messages when they are not in
forwarding state if a topology change occurs.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter Layer 2 Ethernet interface view or
Layer 2 aggregate interface view.

interface interface-type
interface-number

**3\.**Configure the control VLANs for receiving
flush messages.

smart-link flush enable
\[ control-vlan vlan-id-list ]

By default, no control VLAN receives
flush messages.

## Display and maintenance commands for Smart Link

Execute display
commands in any view and the reset
command in user view:

 

| Task | Command |
| --- | --- |
| Display information about the received flush messages. | display smart-link flush || Display smart link group information. | display smart-link group { group-id \| all } || Clear the statistics about flush messages. | reset smart-link statistics |



 

## Smart Link configuration examples

### Example: Configuring a single smart link group

#### Network configuration

As shown in [Figure 2](#_Ref206497211):

·     Device C and Device D are Smart Link devices. Device
A, Device B, and Device E are associated devices. Traffic of VLANs 1 through 30
on Device C and Device D is dually uplinked to Device A.

·     Configure Smart Link on Device C and Device D
for dual uplink backup.

Figure 2 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705596_x_Img_x_png_1_2216099_294551_0.png)

‌

#### Procedure

**1\.**Configure Device C:

\# Create VLANs 1 through 30\.

\<DeviceC\> system-view

\[DeviceC] vlan 1 to 30

\# Map these VLANs to MSTI 1\.

\[DeviceC] stp region-configuration

\[DeviceC-mst-region] instance 1
vlan 1 to 30

\# Activate the MST region configuration.

\[DeviceC-mst-region] active
region-configuration

\[DeviceC-mst-region] quit

\# Shut down Ten-GigabitEthernet 1/0/1.

\[DeviceC] interface ten-gigabitethernet 1/0/1

\[DeviceC-Ten-GigabitEthernet1/0/1] shutdown

\# Disable the spanning tree feature on
the port.

\[DeviceC-Ten-GigabitEthernet1/0/1] undo stp enable

\# Configure the port as a trunk port.

\[DeviceC-Ten-GigabitEthernet1/0/1] port link-type
trunk

\# Assign the port to VLANs 1 through 30\.

\[DeviceC-Ten-GigabitEthernet1/0/1] port trunk permit
vlan 1 to 30

\[DeviceC-Ten-GigabitEthernet1/0/1] quit

\# Configure Ten-GigabitEthernet 1/0/2 in the same way Ten-GigabitEthernet 1/0/1 is configured.

\[DeviceC] interface ten-gigabitethernet 1/0/2

\[DeviceC-Ten-GigabitEthernet1/0/2] shutdown

\[DeviceC-Ten-GigabitEthernet1/0/2] undo stp enable

\[DeviceC-Ten-GigabitEthernet1/0/2] port link-type
trunk

\[DeviceC-Ten-GigabitEthernet1/0/2] port trunk permit
vlan 1 to 30

\[DeviceC-Ten-GigabitEthernet1/0/2] quit

\# Create smart link group 1, and configure
all VLANs mapped to MSTI 1 as the protected VLANs.

\[DeviceC] smart-link group 1

\[DeviceC-smlk-group1]
protected-vlan reference-instance 1

\# Configure Ten-GigabitEthernet 1/0/1 as the primary
port and Ten-GigabitEthernet 1/0/2 as the secondary port for smart link group 1\.

\[DeviceC-smlk-group1] port ten-gigabitethernet 1/0/1 primary

\[DeviceC-smlk-group1] port ten-gigabitethernet 1/0/2 secondary

\# Enable flush message sending in smart
link group 1, and configure VLAN 10 as the transmit control VLAN.

\[DeviceC-smlk-group1] flush
enable control-vlan 10

\[DeviceC-smlk-group1] quit

\# Bring up Ten-GigabitEthernet 1/0/1 and Ten-GigabitEthernet 1/0/2 again.

\[DeviceC] interface ten-gigabitethernet 1/0/1

\[DeviceC-Ten-GigabitEthernet1/0/1] undo shutdown

\[DeviceC-Ten-GigabitEthernet1/0/1] quit

\[DeviceC] interface ten-gigabitethernet 1/0/2

\[DeviceC-Ten-GigabitEthernet1/0/2] undo shutdown

\[DeviceC-Ten-GigabitEthernet1/0/2] quit

**2\.**Configure Device D:

\# Create VLANs 1 through 30\.

\<DeviceD\> system-view

\[DeviceD] vlan 1 to 30

\# Map these VLANs to MSTI 1\.

\[DeviceD] stp region-configuration

\[DeviceD-mst-region] instance 1
vlan 1 to 30

\# Activate the MST region configuration.

\[DeviceD-mst-region] active
region-configuration

\[DeviceD-mst-region] quit

\# Shut down Ten-GigabitEthernet 1/0/1.

\[DeviceD] interface ten-gigabitethernet 1/0/1

\[DeviceD-Ten-GigabitEthernet1/0/1] shutdown

\# Disable the spanning tree feature on
the port.

\[DeviceD-Ten-GigabitEthernet1/0/1] undo stp enable

\# Configure the port as a trunk port.

\[DeviceD-Ten-GigabitEthernet1/0/1] port link-type
trunk

\# Assign the port to VLANs 1 through 30\.

\[DeviceD-Ten-GigabitEthernet1/0/1] port trunk permit
vlan 1 to 30

\[DeviceD-Ten-GigabitEthernet1/0/1] quit

\# Configure Ten-GigabitEthernet 1/0/2 in the same way Ten-GigabitEthernet 1/0/1 is configured.

\[DeviceD] interface ten-gigabitethernet 1/0/2

\[DeviceD-Ten-GigabitEthernet1/0/2] shutdown

\[DeviceD-Ten-GigabitEthernet1/0/2] undo stp enable

\[DeviceD-Ten-GigabitEthernet1/0/2] port link-type
trunk

\[DeviceD-Ten-GigabitEthernet1/0/2] port trunk permit
vlan 1 to 30

\[DeviceD-Ten-GigabitEthernet1/0/2] quit

\# Create smart link group 1, and configure
all VLANs mapped to MSTI 1 as the protected VLANs.

\[DeviceD] smart-link group 1

\[DeviceD-smlk-group1]
protected-vlan reference-instance 1

\# Configure Ten-GigabitEthernet 1/0/1 as the primary
port and Ten-GigabitEthernet 1/0/2 as the secondary port for smart link group 1\.

\[DeviceD-smlk-group1] port ten-gigabitethernet 1/0/1 primary

\[DeviceD-smlk-group1] port ten-gigabitethernet 1/0/2 secondary

\# Enable flush message sending in smart
link group 1, and configure VLAN 20 as the transmit control VLAN.

\[DeviceD-smlk-group1] flush
enable control-vlan 20

\[DeviceD-smlk-group1] quit

\# Bring up Ten-GigabitEthernet 1/0/1 and Ten-GigabitEthernet 1/0/2 again.

\[DeviceD] interface ten-gigabitethernet 1/0/1

\[DeviceD-Ten-GigabitEthernet1/0/1] undo shutdown

\[DeviceD-Ten-GigabitEthernet1/0/1] quit

\[DeviceD] interface ten-gigabitethernet 1/0/2

\[DeviceD-Ten-GigabitEthernet1/0/2] undo shutdown

\[DeviceD-Ten-GigabitEthernet1/0/2] quit

**3\.**Configure Device B:

\# Create VLANs 1 through 30\.

\<DeviceB\> system-view

\[DeviceB] vlan 1 to 30

\# Configure Ten-GigabitEthernet 1/0/1 as a trunk port.

\[DeviceB] interface ten-gigabitethernet 1/0/1

\[DeviceB-Ten-GigabitEthernet1/0/1] port link-type
trunk

\# Assign the port to VLANs 1 through 30\.

\[DeviceB-Ten-GigabitEthernet1/0/1] port trunk permit
vlan 1 to 30

\# Enable flush message receiving and
configure VLAN 10 and VLAN 20 as the receive control VLANs on the port.

\[DeviceB-Ten-GigabitEthernet1/0/1] smart-link flush
enable control-vlan 10 20

\[DeviceB-Ten-GigabitEthernet1/0/1] quit

\# Configure Ten-GigabitEthernet 1/0/2 as a trunk port.

\[DeviceB] interface ten-gigabitethernet 1/0/2

\[DeviceB-Ten-GigabitEthernet1/0/2] port link-type
trunk

\# Assign the port to VLANs 1 through 30\.

\[DeviceB-Ten-GigabitEthernet1/0/2] port trunk permit
vlan 1 to 30

\# Disable the spanning tree feature on
the port.

\[DeviceB-Ten-GigabitEthernet1/0/2] undo stp enable

\# Enable flush message receiving and
configure VLAN 20 as the receive control VLAN on the port.

\[DeviceB-Ten-GigabitEthernet1/0/2] smart-link flush
enable control-vlan 20

\[DeviceB-Ten-GigabitEthernet1/0/2] quit

\# Configure Ten-GigabitEthernet 1/0/3 as a trunk port.

\[DeviceB] interface ten-gigabitethernet 1/0/3

\[DeviceB-Ten-GigabitEthernet1/0/3] port link-type
trunk

\# Assign the port to VLANs 1 through 30\.

\[DeviceB-Ten-GigabitEthernet1/0/3] port trunk permit
vlan 1 to 30

\# Disable the spanning tree feature on
the port.

\[DeviceB-Ten-GigabitEthernet1/0/3] undo stp enable

\# Enable flush message receiving and
configure VLAN 10 as the receive control VLAN on the port.

\[DeviceB-Ten-GigabitEthernet1/0/3] smart-link flush
enable control-vlan 10

\[DeviceB-Ten-GigabitEthernet1/0/3] quit

**4\.**Configure Device E:

\# Create VLANs 1 through 30\.

\<DeviceE\> system-view

\[DeviceE] vlan 1 to 30

\# Configure Ten-GigabitEthernet 1/0/1 as a trunk port.

\[DeviceE] interface ten-gigabitethernet 1/0/1

\[DeviceE-Ten-GigabitEthernet1/0/1] port link-type
trunk

\# Assign the port to VLANs 1 through 30\.

\[DeviceE-Ten-GigabitEthernet1/0/1] port trunk permit
vlan 1 to 30

\# Enable flush message receiving and
configure VLAN 10 and VLAN 20 as the receive control VLANs on the port.

\[DeviceE-Ten-GigabitEthernet1/0/1] smart-link flush
enable control-vlan 10 20

\[DeviceE-Ten-GigabitEthernet1/0/1] quit

\# Configure Ten-GigabitEthernet 1/0/2 as a trunk port.

\[DeviceE] interface ten-gigabitethernet 1/0/2

\[DeviceE-Ten-GigabitEthernet1/0/2] port link-type
trunk

\# Assign the port to VLANs 1 through 30\.

\[DeviceE-Ten-GigabitEthernet1/0/2] port trunk permit
vlan 1 to 30

\# Disable the spanning tree feature on
the port.

\[DeviceE-Ten-GigabitEthernet1/0/2] undo stp enable

\# Enable flush message receiving and
configure VLAN 10 as the receive control VLAN on the port.

\[DeviceE-Ten-GigabitEthernet1/0/2] smart-link flush
enable control-vlan 10

\[DeviceE-Ten-GigabitEthernet1/0/2] quit

\# Configure Ten-GigabitEthernet 1/0/3 as a trunk port.

\[DeviceE] interface ten-gigabitethernet 1/0/3

\[DeviceE-Ten-GigabitEthernet1/0/3] port link-type
trunk

\# Assign the port to VLANs 1 through 30\.

\[DeviceE-Ten-GigabitEthernet1/0/3] port trunk permit
vlan 1 to 30

\# Disable the spanning tree feature on
the port.

\[DeviceE-Ten-GigabitEthernet1/0/3] undo stp enable

\# Enable flush message receiving and
configure VLAN 20 as the receive control VLAN on the port.

\[DeviceE-Ten-GigabitEthernet1/0/3] smart-link flush
enable control-vlan 20

\[DeviceE-Ten-GigabitEthernet1/0/3] quit

**5\.**Configure Device A:

\# Create VLANs 1 through 30\.

\<DeviceA\> system-view

\[DeviceA] vlan 1 to 30

\# Configure Ten-GigabitEthernet 1/0/1 as a trunk port.

\[DeviceA] interface ten-gigabitethernet 1/0/1

\[DeviceA-Ten-GigabitEthernet1/0/1] port link-type
trunk

\# Assign the port to VLANs 1 through 30\.

\[DeviceA-Ten-GigabitEthernet1/0/1] port trunk permit
vlan 1 to 30

\# Enable flush message receiving and configure
VLAN 10 and VLAN 20 as the receive control VLANs on the port.

\[DeviceA-Ten-GigabitEthernet1/0/1] smart-link flush
enable control-vlan 10 20

\[DeviceA-Ten-GigabitEthernet1/0/1] quit

\# Configure Ten-GigabitEthernet 1/0/2 in the same way Ten-GigabitEthernet 1/0/1 is configured.

\[DeviceA] interface ten-gigabitethernet 1/0/2

\[DeviceA-Ten-GigabitEthernet1/0/2] port link-type
trunk

\[DeviceA-Ten-GigabitEthernet1/0/2] port trunk permit
vlan 1 to 30

\[DeviceA-Ten-GigabitEthernet1/0/2] smart-link flush
enable control-vlan 10 20

\[DeviceA-Ten-GigabitEthernet1/0/2] quit

#### Verifying the configuration

\# Display the smart link group
configuration on Device C.

\[DeviceC] display smart-link group 1

Smart link group 1 information:

  Device ID       : 000f-e23d-5af0

  Preemption mode : None

  Preemption delay: 1(s)

  Control VLAN    : 10

  Protected VLAN  : Reference
Instance 1

 

  Member                  Role     
State   Flush-count     Last-flush-time

 
\-----------------------------------------------------------------------------

  XGE1/0/1                 PRIMARY   ACTIVE  5          
    16:45:20 2012/04/21

  XGE1/0/2                 SECONDARY STANDBY
1               16:37:20 2012/04/21

\# Display the flush messages received on
Device B.

\[DeviceB] display smart-link flush

 Received flush packets              
              : 5

 Receiving interface of the last
flush packet       : Ten-GigabitEthernet1/0/3

 Receiving time of the last flush
packet            : 16:50:21 2012/04/21

 Device ID of the last flush
packet                 : 000f-e23d-5af0

 Control VLAN of the last flush
packet              : 10

### Example: Configuring multiple smart link groups load sharing

#### Network configuration

As shown in [Figure 3](#_Ref307333346):

·     Device C is a Smart Link device. Device A, Device
B, and Device D are associated devices. Traffic of VLANs 1 through 200 on Device
C is dually uplinked to Device A by Device B and Device D.

·     Implement dual uplink backup and load sharing on
Device C. Traffic of VLANs 1 through 100 is uplinked to Device A by Device B. Traffic
of VLANs 101 through 200 is uplinked to Device A by Device D.

Figure 3 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705597_x_Img_x_png_2_2216099_294551_0.png)

‌

#### Procedure

**1\.**Configure Device C:

\# Create VLAN 1 through VLAN 200\.

\<DeviceC\> system-view

\[DeviceC] vlan 1 to 200

\# Map VLANs 1 through 100 to MSTI 1, and VLANs
101 through 200 to MSTI 2\.

\[DeviceC] stp
region-configuration

\[DeviceC-mst-region] instance 1
vlan 1 to 100

\[DeviceC-mst-region] instance
2 vlan 101 to 200

\# Activate the MST region configuration.

\[DeviceC-mst-region] active
region-configuration

\[DeviceC-mst-region] quit

\# Shut down Ten-GigabitEthernet 1/0/1.

\[DeviceC] interface ten-gigabitethernet 1/0/1

\[DeviceC-Ten-GigabitEthernet1/0/1] shutdown

\# Disable the spanning tree feature on
the port.

\[DeviceC-Ten-GigabitEthernet1/0/1] undo stp enable

\# Configure the port as a trunk port.

\[DeviceC-Ten-GigabitEthernet1/0/1] port link-type
trunk

\# Assign the port to VLAN 1 through VLAN
200\.

\[DeviceC-Ten-GigabitEthernet1/0/1] port trunk permit
vlan 1 to 200

\[DeviceC-Ten-GigabitEthernet1/0/1] quit

\# Configure Ten-GigabitEthernet 1/0/2 in the same way Ten-GigabitEthernet 1/0/1 is configured.

\[DeviceC] interface ten-gigabitethernet 1/0/2

\[DeviceC-Ten-GigabitEthernet1/0/2] shutdown

\[DeviceC-Ten-GigabitEthernet1/0/2] undo stp enable

\[DeviceC-Ten-GigabitEthernet1/0/2] port link-type
trunk

\[DeviceC-Ten-GigabitEthernet1/0/2] port trunk permit
vlan 1 to 200

\[DeviceC-Ten-GigabitEthernet1/0/2] quit

\# Create smart link group 1, and configure
all VLANs mapped to MSTI 1 as the protected VLANs for smart link group 1\.

\[DeviceC] smart-link group 1

\[DeviceC-smlk-group1]
protected-vlan reference-instance 1

\# Configure Ten-GigabitEthernet 1/0/1 as the primary
port and Ten-GigabitEthernet 1/0/2 as the secondary port for smart link group 1\.

\[DeviceC-smlk-group1] port ten-gigabitethernet 1/0/1 primary

\[DeviceC-smlk-group1] port ten-gigabitethernet 1/0/2 secondary

\# Enable role preemption in smart link
group 1, enable flush message sending, and configure VLAN 10 as the transmit
control VLAN.

\[DeviceC-smlk-group1] preemption
mode role

\[DeviceC-smlk-group1] flush
enable control-vlan 10

\[DeviceC-smlk-group1] quit

\# Create smart link group 2, and configure
all VLANs mapped to MSTI 2 as the protected VLANs for smart link group 2\.

\[DeviceC] smart-link group 2

\[DeviceC-smlk-group2]
protected-vlan reference-instance 2

\# Configure Ten-GigabitEthernet 1/0/1 as the secondary
port and Ten-GigabitEthernet 1/0/2 as the primary port for smart link group 2\.

\[DeviceC-smlk-group2] port ten-gigabitethernet 1/0/2 primary

\[DeviceC-smlk-group2] port ten-gigabitethernet 1/0/1 secondary

\# Enable role preemption in smart link
group 2, enable flush message sending, and configure VLAN 110 as the transmit
control VLAN.

\[DeviceC-smlk-group2]
preemption mode role

\[DeviceC-smlk-group2] flush
enable control-vlan 110

\[DeviceC-smlk-group2] quit

\# Bring up Ten-GigabitEthernet 1/0/1 and Ten-GigabitEthernet 1/0/2.

\[DeviceC] interface ten-gigabitethernet 1/0/1

\[DeviceC-Ten-GigabitEthernet1/0/1] undo shutdown

\[DeviceC-Ten-GigabitEthernet1/0/1] quit

\[DeviceC] interface ten-gigabitethernet 1/0/2

\[DeviceC-Ten-GigabitEthernet1/0/2] undo shutdown

\[DeviceC-Ten-GigabitEthernet1/0/2] quit

**2\.**Configure Device B:

\# Create VLAN 1 through VLAN 200\.

\<DeviceB\> system-view

\[DeviceB] vlan 1 to 200

\# Configure Ten-GigabitEthernet 1/0/1 as a trunk port.

\[DeviceB] interface ten-gigabitethernet 1/0/1

\[DeviceB-Ten-GigabitEthernet1/0/1] port link-type
trunk

\# Assign the port to VLANs 1 through 200\.

\[DeviceB-Ten-GigabitEthernet1/0/1] port trunk permit
vlan 1 to 200

\# Enable flush message receiving and configure
VLAN 10 and VLAN 110 as the receive control VLANs on the port.

\[DeviceB-Ten-GigabitEthernet1/0/1] smart-link flush
enable control-vlan 10 110

\[DeviceB-Ten-GigabitEthernet1/0/1] quit

\# Configure Ten-GigabitEthernet 1/0/2 as a trunk port.

\[DeviceB] interface ten-gigabitethernet 1/0/2

\[DeviceB-Ten-GigabitEthernet1/0/2] port link-type
trunk

\# Assign the port to VLANs 1 through 200\.

\[DeviceB-Ten-GigabitEthernet1/0/2] port trunk permit
vlan 1 to 200

\# Disable the spanning tree feature on
the port.

\[DeviceB-Ten-GigabitEthernet1/0/2] undo stp enable

\# Enable flush message receiving and configure
VLAN 10 and VLAN 110 as the receive control VLANs on the port.

\[DeviceB-Ten-GigabitEthernet1/0/2] smart-link flush
enable control-vlan 10 110

\[DeviceB-Ten-GigabitEthernet1/0/2] quit

**3\.**Configure Device D:

\# Create VLAN 1 through VLAN 200\.

\<DeviceD\> system-view

\[DeviceD] vlan 1 to 200

\# Configure Ten-GigabitEthernet 1/0/1 as a trunk port.

\[DeviceD] interface ten-gigabitethernet 1/0/1

\[DeviceD-Ten-GigabitEthernet1/0/1] port link-type
trunk

\# Assign the port to VLANs 1 through 200\.

\[DeviceD-Ten-GigabitEthernet1/0/1] port trunk permit
vlan 1 to 200

\# Enable flush message receiving and
configure VLAN 10 and VLAN 110 as the receive control VLANs on the port.

\[DeviceD-Ten-GigabitEthernet1/0/1] smart-link flush
enable control-vlan 10 110

\[DeviceD-Ten-GigabitEthernet1/0/1] quit

\# Configure Ten-GigabitEthernet 1/0/2 as a trunk port.

\[DeviceD] interface ten-gigabitethernet 1/0/2

\[DeviceD-Ten-GigabitEthernet1/0/2] port link-type
trunk

\# Assign the port to VLANs 1 through 200\.

\[DeviceD-Ten-GigabitEthernet1/0/2] port trunk permit
vlan 1 to 200

\# Disable the spanning tree feature on
the port.

\[DeviceD-Ten-GigabitEthernet1/0/2] undo stp enable

\# Enable flush message receiving and configure
VLAN 10 and VLAN 110 as the receive control VLANs on the port.

\[DeviceD-Ten-GigabitEthernet1/0/2] smart-link flush
enable control-vlan 10 110

\[DeviceD-Ten-GigabitEthernet1/0/2] quit

**4\.**Configure Device A:

\# Create VLAN 1 through VLAN 200\.

\<DeviceA\> system-view

\[DeviceA] vlan 1 to 200

\# Configure Ten-GigabitEthernet 1/0/1 as a trunk port.

\[DeviceA] interface ten-gigabitethernet 1/0/1

\[DeviceA-Ten-GigabitEthernet1/0/1] port link-type
trunk

\# Assign the port to VLANs 1 through 200\.

\[DeviceA-Ten-GigabitEthernet1/0/1] port trunk permit
vlan 1 to 200

\# Enable flush message receiving and configure
VLAN 10 and VLAN 110 as the receive control VLANs on the port.

\[DeviceA-Ten-GigabitEthernet1/0/1] smart-link flush
enable control-vlan 10 110

\[DeviceA-Ten-GigabitEthernet1/0/1] quit

\# Configure Ten-GigabitEthernet 1/0/2 in the same way Ten-GigabitEthernet 1/0/1 is configured.

\[DeviceA] interface ten-gigabitethernet 1/0/2

\[DeviceA-Ten-GigabitEthernet1/0/2] port link-type
trunk

\[DeviceA-Ten-GigabitEthernet1/0/2] port trunk permit
vlan 1 to 200

\[DeviceA-Ten-GigabitEthernet1/0/2] smart-link flush
enable control-vlan 10 110

\[DeviceA-Ten-GigabitEthernet1/0/2] quit

#### Verifying the configuration

\# Display the smart link group
configuration on Device C.

\[DeviceC] display smart-link group
all

Smart link group 1 information:

  Device ID       : 000f-e23d-5af0

  Preemption mode : Role

  Preemption delay: 1(s)

  Control VLAN    : 10

  Protected VLAN  : Reference
Instance 1

 

  Member                  Role     
State   Flush-count     Last-flush-time

 
\-----------------------------------------------------------------------------

  XGE1/0/1                 PRIMARY   ACTIVE  5          
    16:45:20 2012/04/21

  XGE1/0/2                 SECONDARY STANDBY
1               16:37:20 2012/04/21

 

Smart link group 2 information:

  Device ID       : 000f-e23d-5af0

  Preemption mode : Role

  Preemption delay: 1(s)

  Control VLAN    : 110

  Protected VLAN  : Reference
Instance 2

 

  Member                  Role     
State   Flush-count     Last-flush-time

 
\-----------------------------------------------------------------------------

  XGE1/0/2                 PRIMARY   ACTIVE  5          
    16:45:20 2012/04/21

  XGE1/0/1                 SECONDARY STANDBY
1               16:37:20 2012/04/21

\# Display the flush messages received on Device
B.

\[DeviceB] display smart-link flush

 Received flush packets              
              : 5

 Receiving interface of the last
flush packet       : Ten-GigabitEthernet1/0/2

 Receiving time of the last flush
packet            : 16:25:21 2012/04/21

 Device ID of the last flush
packet                 : 000f-e23d-5af0

 Control VLAN of the last flush
packet              : 10

### Example: Configuring Smart Link and Track collaboration

#### Network configuration

As shown in [Figure 4](#_Ref268075831):

·     Device A, Device B, Device C, and Device D form
maintenance domain (MD) MD\_A of level 5\. Device
C is a Smart Link device, and Device A, Device B, and Device D are associated
devices. Traffic of VLANs 1 through 200 on Device C is dually uplinked to
Device A by Device B and Device D.

·     Configure collaboration between Smart Link and the
CC function of CFD through track entries to meet the following requirements:

¡     Traffic
of VLANs 1 through 100 is uplinked to Device A by Device C through Ten-GigabitEthernet 1/0/1 (primary port of
smart link group 1).

¡     Traffic
of VLANs 101 through 200 is uplinked to Device A by Device C through Ten-GigabitEthernet 1/0/2 (primary port of
smart link group 2).

¡     When
the link between Device C and Device A fails, traffic is quickly switched to
the secondary port of each smart link group. After the fault is cleared,
traffic is switched back to the primary ports.

For more information about CFD, see "Configuring
CFD."

Figure 4 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705598_x_Img_x_png_3_2216099_294551_0.png)

‌

#### Procedure

**1\.**Configure Device A:

\# Create VLAN 1 through VLAN 200\.

\<DeviceA\> system-view

\[DeviceA] vlan 1 to 200

\# Configure Ten-GigabitEthernet 1/0/1 as a trunk port.

\[DeviceA] interface ten-gigabitethernet 1/0/1

\[DeviceA-Ten-GigabitEthernet1/0/1] port link-type
trunk

\# Assign the port to VLANs 1 through 200\.

\[DeviceA-Ten-GigabitEthernet1/0/1] port trunk permit
vlan 1 to 200

\# Enable flush message receiving and
configure VLAN 10 and VLAN 110 as the receive control VLANs on the port.

\[DeviceA-Ten-GigabitEthernet1/0/1] smart-link flush
enable control-vlan 10 110

\[DeviceA-Ten-GigabitEthernet1/0/1] quit

\# Configure Ten-GigabitEthernet 1/0/2 in the same way Ten-GigabitEthernet 1/0/1 is configured.

\[DeviceA] interface ten-gigabitethernet 1/0/2

\[DeviceA-Ten-GigabitEthernet1/0/2] port link-type
trunk

\[DeviceA-Ten-GigabitEthernet1/0/2] port trunk permit
vlan 1 to 200

\[DeviceA-Ten-GigabitEthernet1/0/2] smart-link flush
enable control-vlan 10 110

\[DeviceA-Ten-GigabitEthernet1/0/2] quit

\# Enable CFD and create MD MD\_A of level 5\.

\[DeviceA] cfd enable

\[DeviceA] cfd md MD\_A level 5

\# Create service instance 1 in which the
MA name is based on the VLAN ID in MD\_A and configure
the MA to serve VLAN 10\.

\[DeviceA] cfd service-instance
1 ma-id vlan-based md MD\_A vlan 10

\# Create a MEP list in service instance 1,
create outward-facing MEP 1002, and enable CCM sending in service instance 1 on
Ten-GigabitEthernet 1/0/1.

\[DeviceA] cfd meplist 1001 1002
service-instance 1

\[DeviceA] interface ten-gigabitethernet 1/0/1

\[DeviceA-Ten-GigabitEthernet1/0/1] cfd mep 1002
service-instance 1 outbound

\[DeviceA-Ten-GigabitEthernet1/0/1] cfd cc service-instance
1 mep 1002 enable

\[DeviceA-Ten-GigabitEthernet1/0/1] quit

\# Create service instance 2 in which the
MA name is based on the VLAN ID in MD\_A and
configure the MA to serve VLAN 110\.

\[DeviceA] cfd service-instance
2 ma-id vlan-based md MD\_A vlan 110

\# Create a MEP list in service instance 2,
create outward-facing MEP 1002, and enable CCM sending in service instance 2 on
Ten-GigabitEthernet 1/0/2.

\[DeviceA] cfd meplist 2001 2002
service-instance 2

\[DeviceA] interface ten-gigabitethernet 1/0/2

\[DeviceA-Ten-GigabitEthernet1/0/2] cfd mep 2002
service-instance 2 outbound

\[DeviceA-Ten-GigabitEthernet1/0/2] cfd cc service-instance
2 mep 2002 enable

\[DeviceA-Ten-GigabitEthernet1/0/2] quit

**2\.**Configure Device B:

\# Create VLAN 1 through VLAN 200\.

\<DeviceB\> system-view

\[DeviceB] vlan 1 to 200

\# Configure Ten-GigabitEthernet 1/0/1 as a trunk port.

\[DeviceB] interface ten-gigabitethernet 1/0/1

\[DeviceB-Ten-GigabitEthernet1/0/1] port link-type
trunk

\# Assign the port to VLANs 1 through 200\.

\[DeviceB-Ten-GigabitEthernet1/0/1] port trunk permit
vlan 1 to 200

\# Enable flush message receiving and
configure VLAN 10 and VLAN 110 as the receive control VLANs on the port.

\[DeviceB-Ten-GigabitEthernet1/0/1] smart-link flush
enable control-vlan 10 110

\[DeviceB-Ten-GigabitEthernet1/0/1] quit

\# Configure Ten-GigabitEthernet 1/0/2 as a trunk port.

\[DeviceB] interface ten-gigabitethernet 1/0/2

\[DeviceB-Ten-GigabitEthernet1/0/2] port link-type
trunk

\# Assign the port to VLANs 1 through 200\.

\[DeviceB-Ten-GigabitEthernet1/0/2] port trunk permit
vlan 1 to 200

\# Disable the spanning tree feature on
the port.

\[DeviceB-Ten-GigabitEthernet1/0/2] undo stp enable

\# Enable flush message receiving and
configure VLAN 10 and VLAN 110 as the receive control VLANs on the port.

\[DeviceB-Ten-GigabitEthernet1/0/2] smart-link flush
enable control-vlan 10 110

\[DeviceB-Ten-GigabitEthernet1/0/2] quit

**3\.**Configure Device C:

\# Create VLAN 1 through VLAN 200\.

\<DeviceC\> system-view

\[DeviceC] vlan 1 to 200

\# Map VLANs 1 through 100 to MSTI 1 and
VLANs 101 through 200 to MSTI 2\.

\[DeviceC] stp region-configuration

\[DeviceC-mst-region] instance 1
vlan 1 to 100

\[DeviceC-mst-region] instance 2
vlan 101 to 200

\# Activate the MST region configuration.

\[DeviceC-mst-region] active
region-configuration

\[DeviceC-mst-region] quit

\# Shut down Ten-GigabitEthernet 1/0/1.

\[DeviceC] interface ten-gigabitethernet 1/0/1

\[DeviceC-Ten-GigabitEthernet1/0/1] shutdown

\# Disable the spanning tree feature on
the port.

\[DeviceC-Ten-GigabitEthernet1/0/1] undo stp enable

\# Configure the port as a trunk port.

\[DeviceC-Ten-GigabitEthernet1/0/1] port link-type
trunk

\# Assign the port to VLANs 1 through 200\.

\[DeviceC-Ten-GigabitEthernet1/0/1] port trunk permit
vlan 1 to 200

\[DeviceC-Ten-GigabitEthernet1/0/1] quit

\# Configure Ten-GigabitEthernet 1/0/2 in the same way Ten-GigabitEthernet 1/0/1 is configured.

\[DeviceC] interface ten-gigabitethernet 1/0/2

\[DeviceC-Ten-GigabitEthernet1/0/2] shutdown

\[DeviceC-Ten-GigabitEthernet1/0/2] undo stp enable

\[DeviceC-Ten-GigabitEthernet1/0/2] port link-type
trunk

\[DeviceC-Ten-GigabitEthernet1/0/2] port trunk permit vlan
1 to 200

\[DeviceC-Ten-GigabitEthernet1/0/2] quit

\# Create smart link group 1, and configure
all VLANs mapped to MSTI 1 as the protected VLANs for smart link group 1\.

\[DeviceC] smart-link group 1

\[DeviceC-smlk-group1]
protected-vlan reference-instance 1

\# Configure Ten-GigabitEthernet 1/0/1 as the primary
port and Ten-GigabitEthernet 1/0/2 as the secondary port for smart link group 1\.

\[DeviceC-smlk-group1] port ten-gigabitethernet 1/0/1 primary

\[DeviceC-smlk-group1] port ten-gigabitethernet 1/0/2 secondary

\# Enable role preemption in smart link
group 1, enable flush message sending, and configure VLAN 10 as the transmit
control VLAN.

\[DeviceC-smlk-group1] preemption
mode role

\[DeviceC-smlk-group1] flush
enable control-vlan 10

\[DeviceC-smlk-group1] quit

\# Create smart link group 2, and configure
all VLANs mapped to MSTI 2 as the protected VLANs for smart link group 2\.

\[DeviceC] smart-link group 2

\[DeviceC-smlk-group2]
protected-vlan reference-instance 2

\# Configure Ten-GigabitEthernet 1/0/1 as the secondary
port and Ten-GigabitEthernet 1/0/2 as the primary port for smart link group 2\.

\[DeviceC-smlk-group2] port ten-gigabitethernet 1/0/2 primary

\[DeviceC-smlk-group2] port ten-gigabitethernet 1/0/1 secondary

\# Enable role preemption in smart link
group 2, enable flush message sending, and configure VLAN 110 as the transmit
control VLAN.

\[DeviceC-smlk-group2] preemption
mode role

\[DeviceC-smlk-group2] flush
enable control-vlan 110

\[DeviceC-smlk-group2] quit

\# Enable CFD and create MD MD\_A of level 5\.

\[DeviceC] cfd enable

\[DeviceC] cfd md MD\_A level 5

\# Create service instance 1 in which the
MA name is based on the VLAN ID in MD\_A and
configure the MA to serve VLAN 10\.

\[DeviceC] cfd service-instance
1 ma-id vlan-based md MD\_A vlan 10

\# Create a MEP list in service instance 1\.
Create outward-facing MEP 1001, and enable CCM sending in service instance 1 on
Ten-GigabitEthernet 1/0/1.

\[DeviceC] cfd meplist 1001 1002
service-instance 1

\[DeviceC] interface ten-gigabitethernet 1/0/1

\[DeviceC-Ten-GigabitEthernet1/0/1] cfd mep 1001
service-instance 1 outbound

\[DeviceC-Ten-GigabitEthernet1/0/1] cfd cc service-instance
1 mep 1001 enable

\[DeviceC-Ten-GigabitEthernet1/0/1] quit

\# Create service instance 2 in which the
MA name is based on the VLAN ID in MD\_A and
configure the MA to serve VLAN 110\.

\[DeviceC] cfd service-instance
2 ma-id vlan-based md MD\_A vlan 110

\# Create a MEP list in service instance 2\.
Create outward-facing MEP 2001\. Enable CCM sending in service instance 2 on Ten-GigabitEthernet 1/0/2.

\[DeviceC] cfd meplist 2001 2002
service-instance 2

\[DeviceC] interface ten-gigabitethernet 1/0/2

\[DeviceC-Ten-GigabitEthernet1/0/2] cfd mep 2001
service-instance 2 outbound

\[DeviceC-Ten-GigabitEthernet1/0/2] cfd cc service-instance
2 mep 2001 enable

\[DeviceC-Ten-GigabitEthernet1/0/2] quit

\# Create track entry 1 that is associated
with the CFD CC function of MEP 1001 in service instance 1\.

\[DeviceC] track 1 cfd cc
service-instance 1 mep 1001

\# Configure collaboration between the primary
port Ten-GigabitEthernet 1/0/1
of smart link group 1 and the CC function of CFD through track entry 1, and
bring up the port.

\[DeviceC] interface ten-gigabitethernet 1/0/1

\[DeviceC-Ten-GigabitEthernet1/0/1] port smart-link
group 1 track 1

\[DeviceC-Ten-GigabitEthernet1/0/1] undo shutdown

\[DeviceC-Ten-GigabitEthernet1/0/1] quit

\# Create track entry 1 that is associated
with the CFD CC function of MEP 1001 in service instance 1\.

\[DeviceC] track 2 cfd cc
service-instance 2 mep 2001

\# Configure collaboration between the primary
port Ten-GigabitEthernet 1/0/2
of smart link group 2 and the CC function of CFD through track entry 2, and
bring up the port.

\[DeviceC] interface ten-gigabitethernet 1/0/2

\[DeviceC-Ten-GigabitEthernet1/0/2] port smart-link
group 2 track 2

\[DeviceC-Ten-GigabitEthernet1/0/2] undo shutdown

\[DeviceC-Ten-GigabitEthernet1/0/2] quit

**4\.**Configure Device D:

\# Create VLAN 1 through VLAN 200\.

\<DeviceD\> system-view

\[DeviceD] vlan 1 to 200

\# Configure Ten-GigabitEthernet 1/0/1 as a trunk port.

\[DeviceD] interface ten-gigabitethernet 1/0/1

\[DeviceD-Ten-GigabitEthernet1/0/1] port link-type
trunk

\# Assign the port to VLANs 1 through 200\.

\[DeviceD-Ten-GigabitEthernet1/0/1] port trunk permit
vlan 1 to 200

\# Enable flush message receiving and
configure VLAN 10 and VLAN 110 as the receive control VLANs on the port.

\[DeviceD-Ten-GigabitEthernet1/0/1] smart-link flush
enable control-vlan 10 110

\[DeviceD-Ten-GigabitEthernet1/0/1] quit

\# Configure Ten-GigabitEthernet 1/0/2 as a trunk port.

\[DeviceD] interface ten-gigabitethernet 1/0/2

\[DeviceD-Ten-GigabitEthernet1/0/2] port link-type
trunk

\# Assign the port to VLANs 1 through 200\.

\[DeviceD-Ten-GigabitEthernet1/0/2] port trunk permit
vlan 1 to 200

\# Disable the spanning tree feature on
the port.

\[DeviceD-Ten-GigabitEthernet1/0/2] undo stp enable

\# Enable flush message receiving and
configure VLAN 10 and VLAN 110 as the receive control VLANs on the port.

\[DeviceD-Ten-GigabitEthernet1/0/2] smart-link flush
enable control-vlan 10 110

\[DeviceD-Ten-GigabitEthernet1/0/2] quit

#### Verifying the configuration

\# When the optical fiber between Device A
and Device B fails, display the smart link group configuration on Device C.

\[DeviceC] display smart-link group
all

Smart link group 1 information:

  Device ID       : 000f-e23d-5af0

  Preemption mode : Role

  Preemption delay: 1(s)

  Control VLAN    : 10

  Protected VLAN  : Reference
Instance 1

 

  Member                  Role     
State   Flush-count     Last-flush-time

 
\-----------------------------------------------------------------------------

  XGE1/0/1                 PRIMARY   DOWN    5               16:45:20 2012/04/21

  XGE1/0/2                 SECONDARY ACTIVE  1               16:37:20 2012/04/21

 

Smart link group 2 information:

  Device ID       : 000f-e23d-5af0

  Preemption mode : Role

  Preemption delay: 1(s)

  Control VLAN    : 110

  Protected VLAN  : Reference
Instance 2

 

  Member                  Role     
State   Flush-count     Last-flush-time

 
\-----------------------------------------------------------------------------

  XGE1/0/2                 PRIMARY   ACTIVE  5               16:45:20 2012/04/21

  XGE1/0/1                 SECONDARY STANDBY 1               16:37:20 2012/04/21

The output shows that primary port Ten-GigabitEthernet 1/0/1 of smart link group
1 fails, and secondary port Ten-GigabitEthernet 1/0/2 is in forwarding
state.

 

