
# Configuring port mirroring

## About port mirroring

Port mirroring copies the packets passing through a port to a port that
connects to a data monitoring device for packet analysis.

### Terminology

The following terms are used in port
mirroring configuration.

#### Mirroring source

The mirroring sources can be one or more
monitored ports called source ports. 

Packets passing through mirroring sources
are copied to a port connecting to a data monitoring device for packet
analysis. The copies are called mirrored packets.

#### Source device

The device where the mirroring sources reside
is called a source device.

#### Mirroring destination

The mirroring destination connects to a
data monitoring device and is the destination port (also known as the monitor
port) of mirrored packets. Mirrored packets are sent out of the monitor port to
the data monitoring device.

A monitor port might receive multiple copies
of a packet when it monitors multiple mirroring sources. For example, two
copies of a packet are received on Port A when the following conditions exist:

·     Port A is monitoring bidirectional traffic of Port
B and Port C on the same device.

·     The packet travels from Port B to Port C.

#### Destination device

The device where the monitor port resides
is called the destination device.

#### Mirroring direction

The mirroring direction specifies the direction
of the traffic that is copied on a mirroring source.

·     Inbound—Copies packets received.

·     Outbound—Copies packets sent.

·     Bidirectional—Copies packets received and sent.

#### Mirroring group

Port mirroring is implemented through
mirroring groups. Mirroring groups can be classified into local mirroring
groups, remote source groups, and remote destination groups.

#### Reflector port, egress port, and remote probe VLAN

Reflector ports, remote probe VLANs, and
egress ports are used for Layer 2 remote port mirroring. The remote probe VLAN
is a dedicated VLAN for transmitting mirrored packets to the destination device.
Both the reflector port and egress port reside on a source device and send
mirrored packets to the remote probe VLAN.

On port mirroring devices, all ports except
source, destination, reflector, and egress ports are called common ports. 

### Port mirroring classification

Port mirroring includes the following types:

·     Local port mirroring—Also known as Switch Port Analyzer (SPAN).
In local port mirroring, the source device is directly connected to a data
monitoring device. The source device acts as the destination device to forward
mirrored packets to the data monitoring device.

·     Remote port mirroring—In remote port mirroring, the source device is not directly
connected to a data monitoring device. The source device copies mirrored
packets to the destination device, which forwards them to the data monitoring
device. Depending on how the source device and the destination device are
connected, remote port mirroring includes the following types:

¡     Layer 2 remote port mirroring—Also
known as Remote SPAN (RSPAN). The source device and destination device are
connected through a Layer 2 network.

¡     Layer 3 remote port mirroring—Also
known as Encapsulated Remote SPAN (ERSPAN). The source device and destination
device are connected through a Layer 3 network. 

### Local port mirroring (SPAN)

Figure 1 Local port mirroring implementation

![](https://resource.h3c.com/en/202407/12/20240712_11705810_x_Img_x_png_0_2216126_294551_0.png)

‌

As shown in[Figure 1](#_Ref206488062), the
source port (Port A) and the monitor port (Port B) reside on the same device. Packets
received on Port A are copied to Port B. Port B then forwards the packets to the
data monitoring device for analysis.

### Layer 2 remote port mirroring (RSPAN)

In Layer 2 remote port mirroring, the mirroring
sources and destination reside on different devices and are in different
mirroring groups. 

A remote source group is a mirroring group
that contains the mirroring sources. A remote destination group is a mirroring
group that contains the mirroring destination. Intermediate devices are the
devices between the source device and the destination device.

Layer 2 remote port mirroring can be
implemented through the reflector port method or the egress port method. 

#### Reflector port method

In Layer 2 remote port mirroring that uses the
reflector port method, packets are mirrored as follows:

**1\.**The source device copies packets received on
the mirroring sources to the reflector port.

**2\.**The reflector port broadcasts the mirrored packets
in the remote probe VLAN.

**3\.**The intermediate devices transmit the mirrored
packets to the destination device through the remote probe VLAN.

**4\.**Upon receiving the mirrored packets, the destination
device determines whether the ID of the mirrored packets is the same as the
remote probe VLAN ID. If the two VLAN IDs match, the destination device
forwards the mirrored packets to the data monitoring device through the monitor
port.

Figure 2 Layer 2
remoteport mirroring implementation through the
reflector port method

![](https://resource.h3c.com/en/202407/12/20240712_11705811_x_Img_x_png_1_2216126_294551_0.png)

‌

#### Egress port method

In Layer 2 remote port mirroring that uses the
egress port method, packets are mirrored as follows:

**1\.**The source device copies packets received on
the mirroring sources to the egress port.

**2\.**The egress port forwards the mirrored packets
to the intermediate devices.

**3\.**The intermediate devices flood the mirrored packets
in the remote probe VLAN and transmit the mirrored packets to the destination
device.

**4\.**Upon receiving the mirrored packets, the
destination device determines whether the ID of the mirrored packets is the
same as the remote probe VLAN ID. If the two VLAN IDs match, the destination device
forwards the mirrored packets to the data monitoring device through the monitor
port.

Figure 3 Layer 2 remote port mirroring
implementationthrough the egress port method

![](https://resource.h3c.com/en/202407/12/20240712_11705817_x_Img_x_png_2_2216126_294551_0.png)

‌

### Layer 3 remote port mirroring (ERSPAN)

Layer 3 remote port mirroring encapsulates
mirrored packets in GRE packets with a protocol number of 0x88BE and routes the
packets to the remote monitoring device.

The switch series supports implementing Layer
3 remote port mirroring in encapsulation parameter mode.

#### Layer 3 remote port mirroring in encapsulation parameter mode

To implement Layer 3 remote port mirroring
in encapsulation parameter mode, perform the following tasks:

**1\.**On the source device, create a local
mirroring group and configure the mirroring sources, the monitor port, and the
encapsulation parameters for mirrored packets.

**2\.**On all devices from source to destination,
configure a unicast routing protocol to ensure Layer 3 reachability between the
devices.

Create a local mirroring group on the
source device, and specify the source ports and monitor port for the local
mirroring group. When configuring the monitor port, specify the following
encapsulation parameters for mirrored packets: 

·     Monitoring device IP address as destination IP
address. 

·     Monitor port IP address as source IP address. 

As shown in [Figure 4](#_Ref38642913), Layer 3
remote port mirroring in encapsulation parameter mode works as follows:

**1\.**The source device copies a packet passing
through a source port.

**2\.**The source device encapsulates the copied
packet with the specified encapsulation parameters, monitoring device IP as
destination IP and monitor port IP as source IP. 

**3\.**The encapsulated packet is routed to the
monitoring device through the IP network. 

**4\.**The monitoring device decapsulates the
packet and analyzes the packet contents. 

The packet sent to the monitoring device
through Layer 3 remote port mirroring in encapsulation parameter mode is
encapsulated. In this mode, make sure the monitoring device supports
decapsulating packets. 

Figure 4 Layer 3 remote port mirroring in
encapsulation parameter mode

![](https://resource.h3c.com/en/202407/12/20240712_11705818_x_Img_x_png_3_2216126_294551_0.png)

‌

## Restrictions and guidelines: Port mirroring configuration

The reflector port method for Layer 2
remote port mirroring can be used to implement local port mirroring with
multiple data monitoring devices.

In the reflector port method, the reflector
port broadcasts mirrored packets in the remote probe VLAN. By assigning the
ports that connect to data monitoring devices to the remote probe VLAN, you can
implement local port mirroring to mirror packets to multiple data monitoring
devices. The egress port method cannot implement local port mirroring in this
way. 

For inbound traffic mirroring, the VLAN tag
in the original packet is copied to the mirrored packet.

For outbound traffic mirroring, the
mirrored packet carries the VLAN tag of the egress interface. 

Port mirroring and flow mirroring are
mutually exclusive on the same port. If you first configure port mirroring and
then configure flow mirroring on a port, only port mirroring takes effect. If
you first configure flow mirroring and then port mirroring on a port, flow
mirroring first takes effect, and port mirroring takes effect on packets that
do not match flow mirroring. 

## Configuring localport mirroring (SPAN)

### Restrictions and guidelines

A local mirroring group takes effect only after
it is configured with the monitor port and mirroring sources.

A Layer 2 or Layer 3 aggregate interface,
tunnel interface, or VLAN interface cannot be configured as a source port of a
local mirroring group. You can configure member ports of a Layer 2 or Layer 3
aggregate interface as mirroring source ports. 

A Layer 2 or Layer 3 aggregate interface
cannot be configured as the monitor port of a local mirroring group. 

The member port of an aggregate interface
cannot be configured as a monitor port.

### Local port mirroring tasks at a glance

To configure local port mirroring, perform
the following tasks:

**1\.**[Configuring mirroring sources](#_Ref476068564)

**2\.**[Configuring the monitor port](#_Ref476068592)

### Creating a local mirroring group

**1\.**Enter system view.

system-view

**2\.**Create a local mirroring group.

mirroring-group group-id local

### Configuring mirroring sources

#### Restrictions and guidelines

When you configure source ports for a local
mirroring group, follow these restrictions and guidelines:

·     A mirroring group can contain multiple source
ports.

·     A port can act as a source port for only one
mirroring group.

·     A source port cannot be configured as a
reflector port, egress port, or monitor port. 

·     A Layer 2 or Layer 3 aggregate interface, tunnel
interface, or VLAN interface cannot be configured as a source port for a local
mirroring group.

#### Configuring source ports

·     Configure source ports in system view:

**a.**Enter system view.

system-view

**b.**Configure source ports for a local mirroring
group.

mirroring-group group-id mirroring-port
interface-list { both \| inbound \| outbound }

By default, no source port is configured
for a local mirroring group.

·     Configure source ports in interface view:

**a.**Enter system view.

system-view

**b.**Enter interface view.

interface interface-type
interface-number

**c.**Configure the port as a source port for a
local mirroring group.

mirroring-group group-id mirroring-port { both \| inbound \| outbound }

By default, a port does not act as a
source port for any local mirroring groups.

### Configuring the monitor port

#### Restrictions and guidelines

Do not enable the spanning tree feature on
the monitor port.

Only one monitor port can be specified for
a local mirroring group.

Use a monitor port only for port mirroring,
so the data monitoring device receives only the mirrored traffic.

The destination port cannot be any member
port of an aggregate group. 

#### Procedure

·     Configure the monitor port in system view:

**a.**Enter system view.

system-view

**b.**Configure the monitor port for a local mirroring
group.

mirroring-group group-id monitor-port interface-type
interface-number

By default, no monitor port is configured
for a local mirroring group.

·     Configure the monitor port in interface view:

**a.**Enter system view.

system-view

**b.**Enter interface view.

interface interface-type
interface-number

**c.**Configure the port as the monitor port for a
mirroring group.

mirroring-group group-id monitor-port

By default, a port does not act as the
monitor port for any local mirroring groups.

## Configuring local port mirroring group with multiple monitoring devices

#### About local port mirroring with multiple monitoring devices

To monitor interesting traffic passing
through a device on multiple directly connected data monitoring devices,
configure local port mirroring with a remote probe VLAN as follows:

**1\.**Configure a remote source group on the
device.

**2\.**Configure mirroring sources and a reflector
port for the remote source group.

**3\.**Specify a VLAN as the remote probe VLAN and
assign the ports connecting to the data monitoring devices to the VLAN.

This configuration enables the device to
copy packets received on the mirroring sources to the reflector port, which
broadcasts the packets in the remote probe VLAN. The packets are then sent out
of the member ports of the remote probe VLAN to the data monitoring devices.

#### Restrictions and guidelines

The reflector port must be a port not in use.
Do not connect a network cable to the reflector port.

When a port is configured as a reflector
port, the port restores to the factory default settings. You cannot configure
other features on the reflector port.

Do not assign a source port of a mirroring
group to the remote probe VLAN of the mirroring group.

A VLAN can act as the remote probe VLAN for
only one remote source group. As a best practice, use the VLAN for port
mirroring exclusively. Do not create a VLAN interface for the VLAN or configure
other features for the VLAN.

The remote probe VLAN must be a static
VLAN. 

To delete a VLAN that has been configured
as the remote probe VLAN for a mirroring group, remove the remote probe VLAN
from the mirroring group first.

The device supports only one remote probe
VLAN.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Create a remote source group.

mirroring-group group-id remote-source

**3\.**Configure mirroring sources for the remote
source group. Choose one option as needed:

¡     Configure
mirroring ports in system view.

mirroring-group group-id mirroring-port
interface-list { both \| inbound \| outbound }

¡     Execute
the following commands in sequence to enter interface view and then configure
the interface as a source port.

interface interface-type interface-number

mirroring-group group-id mirroring-port { both \| inbound \| outbound }

quit

**4\.**Configure the reflector port for the remote
source group.

mirroring-group group-id reflector-port
reflector-port

By default, no reflector port is
configured for a remote source group.

**5\.**Create a VLAN and enter its view.

vlan vlan-id

**6\.**Assign the ports that connect to the data
monitoring devices to the VLAN.

port interface-list

By default, a VLAN does not contain any
ports. 

**7\.**Return to system view.

quit

**8\.**Specify the VLAN as the remote probe VLAN
for the remote source group.

mirroring-group group-id remote-probe vlan vlan-id

By default, no remote probe VLAN is
configured for a remote source group. 

## Configuring Layer 2 remote port mirroring (RSPAN)

### Restrictions and guidelines

To ensure successful traffic mirroring, configure
devices in the order of the destination device, the intermediate devices, and
the source device.

If intermediate devices exist, configure the
intermediate devices to allow the remote probe VLAN to pass through.

For a mirrored packet to successfully
arrive at the remote destination device, make sure its VLAN ID is not removed or
changed.

Do not configure both MVRP and Layer 2 remote
port mirroring. Otherwise, MVRP might register the remote probe VLAN with
incorrect ports, which would cause the monitor port to receive undesired copies.
For more information about MVRP, see Layer 2—LAN
Switching Configuration Guide.

To monitor the bidirectional traffic of a
source port, disable MAC address learning for the remote probe VLAN on the
source, intermediate, and destination devices. For more information about MAC
address learning, see Layer 2—LAN Switching
Configuration Guide.

The member port of a Layer 2 or Layer 3
aggregate interface cannot be configured as the monitor port for Layer 2 remote
port mirroring. 

### Layer 2 remote port mirroring with reflector port configuration task list

#### Configuring the destination device

**1\.**[Creating a remote destination group](#_Ref476120220)

**2\.**[Configuring the monitor port](#_Ref476120227)

**3\.**[Configuring the remote probe VLAN](#_Ref462823804)

**4\.**[Assigning the monitor port to the remote probe
VLAN](#_Ref343498989)

#### Configuring the source device

**1\.**[Creating a remote source group](#_Ref476120694)

**2\.**[Configuring mirroring sources](#_Ref476121240)

**3\.**[Configuring the reflector port](#_Ref476121518)

**4\.**[Configuring the remote probe VLAN](#_Ref462823804)

### Layer 2 remote port mirroring with egress port configuration task list

#### Configuring the destination device

**1\.**[Creating a remote destination group](#_Ref476120220)

**2\.**[Configuring the monitor port](#_Ref476120227)

**3\.**[Configuring the remote probe VLAN](#_Ref462823804)

**4\.**[Assigning the monitor port to the remote probe
VLAN](#_Ref343498989)

#### Configuring the source device

**1\.**[Creating a remote source group](#_Ref476120694)

**2\.**[Configuring mirroring sources](#_Ref476121240)

**3\.**[Configuring the egress port](#_Ref476121606)

**4\.**[Configuring the remote probe VLAN](#_Ref462823804)

### Creating a remote destination group

#### Restrictions and guidelines

Perform this task on the destination device
only.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Create a remote destination group.

mirroring-group group-id remote-destination

### Configuring the monitor port

#### Restrictions and guidelines

Perform this task on the destination device
only.

Do not enable the spanning tree feature on
the monitor port.

Use a monitor port only for port mirroring,
so the data monitoring device receives only the mirrored traffic.

A monitor port can belong to only one
mirroring group.

A Layer 2 or Layer 3 aggregate interface
cannot be configured as the monitor port for a mirroring group. 

The member port of an aggregate interface
cannot be configured as a monitor port.

#### Configuring the monitor port in system view

**1\.**Enter system view.

system-view

**2\.**Configure the monitor port for a remote
destination group.

mirroring-group group-id monitor-port interface-type
interface-number

By default, no monitor port is configured
for a remote destination group.

#### Configuring the monitor port in interface view

**1\.**Enter system view.

system-view

**2\.**Enter interface view.

interface interface-type
interface-number

**3\.**Configure the port as the monitor port for a
remote destination group.

mirroring-group group-id monitor-port

By default, a port does not act as the
monitor port for any remote destination groups. 

### Configuring the remote probe VLAN

#### Restrictions and guidelines

This task is required on the both the
source and destination devices.

Only an existing static VLAN can be
configured as a remote probe VLAN.

When a VLAN is configured as a remote probe
VLAN, use the remote probe VLAN for port mirroring exclusively.

Configure the same remote probe VLAN for
the remote source group and the remote destination group.

The device supports only one remote probe
VLAN. 

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Configure the remote probe VLAN for the
remote source or destination group.

mirroring-group group-id remote-probe
vlan vlan-id

By default, no remote probe VLAN is
configured for a remote source or destination group.

### Assigning the monitor port to the remote probe VLAN

#### Restrictions and guidelines

Perform this task on the destination device
only.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter the interface view of the monitor
port.

interface interface-type interface-number

**3\.**Assign the port to the remote probe VLAN.

¡     Assign
an access port to the remote probe VLAN.

port access vlan vlan-id

¡     Assign
a trunk port to the remote probe VLAN.

port trunk permit vlan vlan-id

¡     Assign
a hybrid port to the remote probe VLAN.

port hybrid vlan vlan-id { tagged \| untagged }

For more information about the port access
vlan, port trunk permit vlan, and port hybrid vlan
commands, see Layer 2—LAN Switching Command Reference.

### Creating a remote source group

#### Restrictions and guidelines

Perform this task on the source device
only.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Create a remote source group.

mirroring-group group-id remote-source

### Configuring mirroring sources

#### Restrictions and guidelines

Perform this task on the source device
only.

When you configure source ports for a remote
source group, follow these restrictions and guidelines:

·     Do not assign a source port of a mirroring group
to the remote probe VLAN of the mirroring group.

·     A mirroring group can contain multiple source
ports.

·     A port can act as a source port for only one
mirroring group.

·     A source port cannot be configured as a
reflector port, monitor port, or egress port.

#### Configuring source ports

·     Configure source ports in system view:

**a.**Enter system view.

system-view

**b.**Configure source ports for a remote source
group.

mirroring-group group-id mirroring-port
interface-list { both \| inbound \| outbound }

By default, no source port is configured
for a remote source group.

·     Configure source ports in interface view:

**a.**Enter system view.

system-view

**b.**Enter interface view.

interface interface-type
interface-number

**c.**Configure the port as a source port for a
remote source group.

mirroring-group group-id mirroring-port { both \| inbound \| outbound }

By default, a port does not act as a
source port for any remote source groups.

### Configuring the reflector port

#### Restrictions and guidelines

Perform this task on the source device
only.

A remote source group supports only one
reflector port.

#### Configuring the reflector port in system view

**1\.**Enter system view.

system-view

**2\.**Configure the reflector port for a remote
source group.

mirroring-group group-id reflector-port
interface-type interface-number

 

| CAUTION | CAUTION:  ·     The port to be configured as a reflector port must be a port not in use. Do not connect a network cable to a reflector port.  ·     When a port is configured as a reflector port, the default settings of the port are automatically restored. You cannot configure other features on the reflector port.  ·     If an IRF port is bound to only one physical interface, do not configure the physical interface as a reflector port. Otherwise, the IRF might split. |
| --- | --- |

 

By default, no reflector port is
configured for a remote source group.

#### Configuring the reflector port in interface view

**1\.**Enter system view.

system-view

**2\.**Enter interface view.

interface interface-type
interface-number

 

| CAUTION | CAUTION:  ·     The port to be configured as a reflector port must be a port not in use. Do not connect a network cable to a reflector port.  ·     When a port is configured as a reflector port, the default settings of the port are automatically restored. You cannot configure other features on the reflector port.  ·     If an IRF port is bound to only one physical interface, do not configure the physical interface as a reflector port. Otherwise, the IRF might split. |
| --- | --- |

 

**3\.**Configure the port as the reflector port for
a remote source group.

mirroring-group group-id reflector-port

By default, a port does not act as the
reflector port for any remote source groups. 

### Configuring the egress port

#### Restrictions and guidelines

Perform this task on the source device
only.

Disable the following features on the egress
port:

·     Spanning tree.

·     802.1X.

·     IGMP snooping.

·     Static ARP.

·     MAC address learning. 

A port of an existing mirroring group
cannot be configured as an egress port.

A mirroring group supports only one egress
port.

#### Configuring the egress port in system view

**1\.**Enter system view.

system-view

**2\.**Configure the egress port for a remote
source group.

mirroring-group group-id monitor-egress
interface-type interface-number

By default, no egress port is configured
for a remote source group.

**3\.**Enter the egress port view.

interface interface-type interface-number

**4\.**Assign the egress port to the remote probe
VLAN.

¡     Assign
a trunk port to the remote probe VLAN.

port trunk permit vlan vlan-id

¡     Assign
a hybrid port to the remote probe VLAN.

port hybrid vlan vlan-id { tagged \| untagged }

For more information about the port trunk
permit vlan and port hybrid
vlan commands, see Layer
2—LAN Switching Command Reference.

#### Configuring the egress port in interface view

**1\.**Enter system view.

system-view

**2\.**Enter interface view.

interface interface-type
interface-number

**3\.**Configure the port as the egress port for a
remote source group.

mirroring-group group-id monitor-egress

By default, a port does not act as the
egress port for any remote source groups. 

## Configuring Layer 3 remote port mirroring (ERSPAN in encapsulation parameter mode)

### Restrictions and guidelines

To implement ERSPAN in encapsulation
parameter mode, perform the following tasks:

**1\.**On the source device, create a local
mirroring group and configure the source ports, the monitor port, and the
encapsulation parameters for mirrored packets.

**2\.**On all devices from source to destination,
configure a unicast routing protocol to ensure Layer 3 reachability between the
devices.

For ERSPAN in encapsulation parameter mode,
the data monitoring device must be able to remove the outer headers to obtain
the original mirrored packets for analysis. 

### Layer 3 remote port mirroring tasksat a glance

To configure Layer 3 remote port mirroring
in ERSPAN mode, perform the following tasks:

**1\.**[Creating a local mirroring group on the
source device](#_Ref511748366)

**2\.**[Configuring mirroring sources](#_Ref511748383)

**3\.**[Configuring the monitor port](#_Ref510256603)

### Creating a local mirroring group on the source device

**1\.**Enter system view.

system-view

**2\.**Create a local mirroring group.

mirroring-group group-id local

By default, no local mirroring group exists. 

### Configuring mirroring sources

#### Restrictions and guidelines

When you configure source ports for the
local mirroring group, follow these restrictions and guidelines:

·     A port can act as a source port for only one
mirroring group either as a unidirectional port or bidirectional port. 

·     A source port cannot be configured as a
reflector port, egress port, or monitor port.

 

#### Configuring source ports

·     Configure source ports in system view:

**a.**Enter system view.

system-view

**b.**Configure source ports for a local mirroring
group.

mirroring-group group-id mirroring-port
interface-list inbound

By default, no source port is configured
for a local mirroring group.

·     Configure source ports in interface view:

**a.**Enter system view.

system-view

**b.**Enter interface view.

interface interface-type
interface-number

**c.**Configure the port as a source port for a
local mirroring group.

mirroring-group group-id mirroring-port  inbound

### Configuring the monitor port

#### Restrictions and guidelines

Do not enable the spanning tree feature on
the monitor port.

Only one monitor port can be specified for
a local mirroring group.

Use a monitor port only for port mirroring,
so the data monitoring device receives only the mirrored traffic.

An aggregation group member port cannot be
configured as the monitor port for a mirroring group. 

#### Procedure

·     Configure the monitor port in system view:

**a.**Enter system view.

system-view

**b.**Configure the monitor port in a local
mirroring group and specify the encapsulation parameters.

mirroring-group group-id monitor-port interface-list destination-ip destination-ip-address source-ip source-ip-address \[ dscp dscp-value \| vlan vlan-id \| vrf-instance vrf-name ] \*

By default, no monitor port is configured
for a local mirroring group.

·     Configure the monitor port in interface view:

**a.**Enter system view.

system-view

**b.**Enter interface view.

interface interface-type
interface-number

**c.**Specify the port as the monitor port in a
local mirroring group and configure the encapsulation parameters in a local
mirroring group.

mirroring-group group-id monitor-port destination-ip destination-ip-address source-ip source-ip-address \[ dscp dscp-value \| vlan vlan-id \| vrf-instance vrf-name ] \*

By default, a port does not act as the
monitor port for any local mirroring groups.

## Display and maintenance commands for port mirroring

Execute display commands
in any view.

 

| Task | Command |
| --- | --- |
| Display mirroring group information. | display mirroring-group { group-id \| all \| local \| remote-destination \| remote-source } |

‌

## Port mirroring configuration examples

### Example: Configuring local port mirroring(SPAN)

#### Network configuration

As shown in [Figure 5](#_Ref178759134),
configure local port mirroring in source port mode to enable the server to
monitor the bidirectional traffic of the two departments.

Figure 5 Network
diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705821_x_Img_x_png_6_2216126_294551_0.png)

‌

#### Procedure

\# Create local mirroring group 1\.

\<Device\> system-view

\[Device] mirroring-group 1 local

\# Configure Ten-GigabitEthernet 1/0/1 and Ten-GigabitEthernet 1/0/2 as source ports for
local mirroring group 1\.

\[Device] mirroring-group 1
mirroring-port ten-gigabitethernet 1/0/1 ten-gigabitethernet 1/0/2 both

\# Configure Ten-GigabitEthernet 1/0/3 as the monitor
port for local mirroring group 1\.

\[Device] mirroring-group 1
monitor-port ten-gigabitethernet 1/0/3

\# Disable the spanning tree feature on the
monitor port (Ten-GigabitEthernet 1/0/3).

\[Device] interface ten-gigabitethernet 1/0/3

\[Device-Ten-GigabitEthernet1/0/3] undo stp enable

\[Device-Ten-GigabitEthernet1/0/3] quit

#### Verifying the configuration

\# Verify the mirroring group configuration.

\[Device] display mirroring-group all

Mirroring group 1:

    Type: Local

    Status: Active

    Mirroring port:

        Ten-GigabitEthernet1/0/1  Both

        Ten-GigabitEthernet1/0/2  Both

    Monitor port: Ten-GigabitEthernet1/0/3

### Example: Configuring local port mirroring with multiple monitoring devices

#### Network configuration

As shown in [Figure 6](#_Ref453170009), Dept.
A, Dept. B, and Dept. C are connected to the device through Ten-GigabitEthernet
1/0/1, Ten-GigabitEthernet 1/0/2, and Ten-GigabitEthernet 1/0/3, respectively. 

Configure port mirroring to enable data
monitoring devices Server A and Server B to monitor both the incoming and
outgoing traffic of departments A, B, and C.

Figure 6 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705822_x_Img_x_png_7_2216126_294551_0.png)

‌

#### Procedure

\# Create remote source group 1\.

\<Device\> system-view

\[Device] mirroring-group 1
remote-source

\# Configure Ten-GigabitEthernet 1/0/1
through Ten-GigabitEthernet 1/0/3 as source ports of remote source group 1\.

\[Device] mirroring-group 1
mirroring-port Ten-GigabitEthernet1/0/1 to Ten-GigabitEthernet1/0/3 both

\# Configure an unused port (Ten-GigabitEthernet
1/0/6 in this example) as the reflector port of remote source group 1\.

\[Device] mirroring-group 1
reflector-port Ten-GigabitEthernet1/0/6

This operation may delete all
settings made on the interface. Continue? \[Y/N]:y

\# Create VLAN 10 and assign the ports
connecting the data monitoring devices to the VLAN.

\[Device] vlan 10

\[Device-vlan10] port
Ten-GigabitEthernet1/0/4 to Ten-GigabitEthernet1/0/5

\[Device-vlan10] quit

\# Configure VLAN 10 as the remote probe
VLAN of remote source group 1\.

\[Device] mirroring-group 1
remote-probe vlan 10 

### Example: Configuring Layer 2 remote port mirroring (RSPAN with reflector port)

#### Network configuration

As shown in [Figure 7](#_Ref179633329), configure
Layer 2 remote port mirroring to enable the server to monitor the bidirectional
traffic of the Marketing Department.

Figure 7 Network
diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705823_x_Img_x_png_8_2216126_294551_0.png)

‌

#### Procedure

**1\.**Configure Device C (the destination device):

\# Configure Ten-GigabitEthernet 1/0/1 as a trunk port,
and assign the port to VLAN 2\.

\<DeviceC\> system-view

\[DeviceC] interface ten-gigabitethernet 1/0/1

\[DeviceC-Ten-GigabitEthernet1/0/1] port link-type
trunk

\[DeviceC-Ten-GigabitEthernet1/0/1] port trunk permit
vlan 2

\[DeviceC-Ten-GigabitEthernet1/0/1] quit

\# Create a remote destination group.

\[DeviceC] mirroring-group 2
remote-destination

\# Create VLAN 2\.

\[DeviceC] vlan 2

\# Disable MAC address learning for VLAN
2\.

\[DeviceC-vlan2] undo
mac-address mac-learning enable

\[DeviceC-vlan2] quit

\# Configure VLAN 2 as the remote probe
VLAN for the mirroring group.

\[DeviceC] mirroring-group 2 remote-probe
vlan 2

\# Configure Ten-GigabitEthernet 1/0/2 as the monitor
port for the mirroring group.

\[DeviceC] interface ten-gigabitethernet 1/0/2

\[DeviceC-Ten-GigabitEthernet1/0/2] mirroring-group 2 monitor-port

\# Disable the spanning tree feature on Ten-GigabitEthernet 1/0/2.

\[DeviceC-Ten-GigabitEthernet1/0/2] undo stp enable

\# Assign Ten-GigabitEthernet 1/0/2 to VLAN 2\.

\[DeviceC-Ten-GigabitEthernet1/0/2] port access vlan 2

\[DeviceC-Ten-GigabitEthernet1/0/2] quit

**2\.**Configure Device B (the intermediate
device):

\# Create VLAN 2\.

\<DeviceB\> system-view

\[DeviceB] vlan 2

\# Disable MAC address learning for VLAN
2\.

\[DeviceB-vlan2] undo
mac-address mac-learning enable

\[DeviceB-vlan2] quit

\# Configure Ten-GigabitEthernet 1/0/1 as a trunk port,
and assign the port to VLAN 2\.

\[DeviceB] interface ten-gigabitethernet 1/0/1

\[DeviceB-Ten-GigabitEthernet1/0/1] port link-type
trunk

\[DeviceB-Ten-GigabitEthernet1/0/1] port trunk permit vlan
2

\[DeviceB-Ten-GigabitEthernet1/0/1] quit

\# Configure Ten-GigabitEthernet 1/0/2 as a trunk port,
and assign the port to VLAN 2\.

\[DeviceB] interface ten-gigabitethernet 1/0/2

\[DeviceB-Ten-GigabitEthernet1/0/2] port link-type
trunk

\[DeviceB-Ten-GigabitEthernet1/0/2] port trunk permit
vlan 2

\[DeviceB-Ten-GigabitEthernet1/0/2] quit

**3\.**Configure Device A (the source device):

\# Create a remote source group.

\<DeviceA\> system-view

\[DeviceA] mirroring-group 1
remote-source

\# Create VLAN 2\.

\[DeviceA] vlan 2

\# Disable MAC address learning for VLAN
2\.

\[DeviceA-vlan2] undo
mac-address mac-learning enable

\[DeviceA-vlan2] quit

\# Configure VLAN 2 as the remote probe
VLAN for the mirroring group.

\[DeviceA] mirroring-group 1 remote-probe
vlan 2

\# Configure Ten-GigabitEthernet 1/0/1 as a source port for
the mirroring group.

\[DeviceA] mirroring-group 1
mirroring-port ten-gigabitethernet 1/0/1 both

\# Configure Ten-GigabitEthernet 1/0/3 as the reflector
port for the mirroring group.

\[DeviceA] mirroring-group 1 reflector-port
ten-gigabitethernet 1/0/3

This operation may delete all
settings made on the interface. Continue? \[Y/N]: y

\# Configure Ten-GigabitEthernet 1/0/2 as a trunk port,
and assign the port to VLAN 2\.

\[DeviceA] interface ten-gigabitethernet 1/0/2

\[DeviceA-Ten-GigabitEthernet1/0/2] port link-type
trunk

\[DeviceA-Ten-GigabitEthernet1/0/2] port trunk permit
vlan 2

\[DeviceA-Ten-GigabitEthernet1/0/2] quit

#### Verifying the configuration

\# Verify the mirroring group configuration
on Device C.

\[DeviceC] display mirroring-group all

Mirroring group 2:

    Type: Remote destination

    Status: Active

    Monitor port: Ten-GigabitEthernet1/0/2

    Remote probe VLAN: 2

\# Verify the mirroring group configuration
on Device A.

\[DeviceA] display mirroring-group all

Mirroring group 1:

    Type: Remote source

    Status: Active

    Mirroring port:

        Ten-GigabitEthernet1/0/1  Both

    Reflector port: Ten-GigabitEthernet1/0/3

    Remote probe VLAN: 2

### Example: Configuring Layer 2 remote port mirroring (RSPAN with egress port)

#### Network configuration

On the Layer 2 network shown in [Figure 8](#_Ref178762945), configure
Layer 2 remote port mirroring to enable the server to monitor the bidirectional
traffic of the Marketing Department.

Figure 8 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705824_x_Img_x_png_9_2216126_294551_0.png)

‌

#### Procedure

**1\.**Configure Device C (the destination device):

\# Configure Ten-GigabitEthernet 1/0/1 as a trunk port,
and assign the port to VLAN 2\.

\<DeviceC\> system-view

\[DeviceC] interface ten-gigabitethernet 1/0/1

\[DeviceC-Ten-GigabitEthernet1/0/1] port link-type
trunk

\[DeviceC-Ten-GigabitEthernet1/0/1] port trunk permit
vlan 2

\[DeviceC-Ten-GigabitEthernet1/0/1] quit

\# Create a remote destination group.

\[DeviceC] mirroring-group 2
remote-destination

\# Create VLAN 2\.

\[DeviceC] vlan 2

\# Disable MAC address learning for VLAN
2\.

\[DeviceC-vlan2] undo
mac-address mac-learning enable

\[DeviceC-vlan2] quit

\# Configure VLAN 2 as the remote probe
VLAN for the mirroring group.

\[DeviceC] mirroring-group 2
remote-probe vlan 2

\# Configure Ten-GigabitEthernet 1/0/2 as the monitor
port for the mirroring group.

\[DeviceC] interface ten-gigabitethernet 1/0/2

\[DeviceC-Ten-GigabitEthernet1/0/2] mirroring-group 2
monitor-port

\# Disable the spanning tree feature on Ten-GigabitEthernet 1/0/2.

\[DeviceC-Ten-GigabitEthernet1/0/2] undo stp enable

\# Assign Ten-GigabitEthernet 1/0/2 to VLAN 2 as an
access port.

\[DeviceC-Ten-GigabitEthernet1/0/2] port access vlan 2

\[DeviceC-Ten-GigabitEthernet1/0/2] quit

**2\.**Configure Device B (the intermediate
device):

\# Create VLAN 2\.

\<DeviceB\> system-view

\[DeviceB] vlan 2

\# Disable MAC address learning for VLAN
2\.

\[DeviceB-vlan2] undo
mac-address mac-learning enable

\[DeviceB-vlan2] quit

\# Configure Ten-GigabitEthernet 1/0/1 as a trunk port,
and assign the port to VLAN 2\.

\[DeviceB] interface ten-gigabitethernet 1/0/1

\[DeviceB-Ten-GigabitEthernet1/0/1] port link-type
trunk

\[DeviceB-Ten-GigabitEthernet1/0/1] port trunk permit
vlan 2

\[DeviceB-Ten-GigabitEthernet1/0/1] quit

\# Configure Ten-GigabitEthernet 1/0/2 as a trunk port,
and assign the port to VLAN 2\.

\[DeviceB] interface ten-gigabitethernet 1/0/2

\[DeviceB-Ten-GigabitEthernet1/0/2] port link-type
trunk

\[DeviceB-Ten-GigabitEthernet1/0/2] port trunk permit
vlan 2

\[DeviceB-Ten-GigabitEthernet1/0/2] quit

**3\.**Configure Device A (the source device):

\# Create a remote source group.

\<DeviceA\> system-view

\[DeviceA] mirroring-group 1
remote-source

\# Create VLAN 2\.

\[DeviceA] vlan 2

\# Disable MAC address learning for VLAN
2\.

\[DeviceA-vlan2] undo
mac-address mac-learning enable

\[DeviceA-vlan2] quit

\# Configure VLAN 2 as the remote probe
VLAN of the mirroring group.

\[DeviceA] mirroring-group 1 remote-probe
vlan 2

\# Configure Ten-GigabitEthernet 1/0/1 as a source port for
the mirroring group.

\[DeviceA] mirroring-group 1
mirroring-port ten-gigabitethernet 1/0/1 both

\# Configure Ten-GigabitEthernet 1/0/2 as the egress port
for the mirroring group.

\[DeviceA] mirroring-group 1
monitor-egress ten-gigabitethernet 1/0/2

\# Configure Ten-GigabitEthernet 1/0/2 as a trunk port,
and assign the port to VLAN 2\.

\[DeviceA] interface ten-gigabitethernet 1/0/2

\[DeviceA-Ten-GigabitEthernet1/0/2] port link-type
trunk

\[DeviceA-Ten-GigabitEthernet1/0/2] port trunk permit
vlan 2

\# Disable the spanning tree feature on
the port.

\[DeviceA-Ten-GigabitEthernet1/0/2] undo stp enable

\[DeviceA-Ten-GigabitEthernet1/0/2] quit

#### Verifying the configuration

\# Verify the mirroring group configuration
on Device C.

\[DeviceC] display mirroring-group all

Mirroring group 2:

    Type: Remote destination

    Status: Active

    Monitor port: Ten-GigabitEthernet1/0/2

    Remote probe VLAN: 2

\# Verify the mirroring group configuration
on Device A.

\[DeviceA] display mirroring-group all

Mirroring group 1:

    Type: Remote source

    Status: Active

    Mirroring port:

        Ten-GigabitEthernet1/0/1  Both

    Monitor egress port: Ten-GigabitEthernet1/0/2

    Remote probe VLAN: 2

### Example: Configuring Layer 3 remote port mirroring (ERSPAN in encapsulation parameter mode)

#### Network configuration

On a Layer 3 network shown in [Figure 9](#_Ref510252805), configure
Layer 3 remote port mirroring in encapsulation parameter mode to enable the
server to monitor the incoming traffic of the Marketing Department.

Figure 9 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705812_x_Img_x_png_10_2216126_294551_0.png)

‌

#### Procedure

**1\.**Configure IP addresses for the interfaces as
shown in [Figure 9](#_Ref510252805).
(Details not shown.)

**2\.**Configure Device A (the source device):

\# Enable the OSPF protocol.

\[DeviceA] ospf 1

\[DeviceA-ospf-1] area 0

\[DeviceA-ospf-1-area-0.0.0.0]
network 10.1.1.0 0.0.0.255

\[DeviceA-ospf-1-area-0.0.0.0]
network 20.1.1.0 0.0.0.255

\[DeviceA-ospf-1-area-0.0.0.0]
quit

\[DeviceA-ospf-1] quit

\# Create local mirroring group 1\.

\[DeviceA] mirroring-group 1
local

\# Configure Ten-GigabitEthernet 1/0/1 as a source port.

\[DeviceA] mirroring-group 1
mirroring-port ten-gigabitethernet 1/0/1 inbound

\# Configure Ten-GigabitEthernet 1/0/2 as the monitor
port. Specify the destination and source IP addresses for mirrored packets as
40.1.1.2 and 20.1.1.1, respectively. 

\[DeviceA] mirroring-group 1
monitor-port ten-gigabitethernet 1/0/2 destination-ip
40.1.1.2 source-ip 20.1.1.1

**3\.**Enable the OSPF protocol on Device B.

\<DeviceB\> system-view

\[DeviceB] ospf 1

\[DeviceB-ospf-1] area 0

\[DeviceB-ospf-1-area-0.0.0.0]
network 20.1.1.0 0.0.0.255

\[DeviceB-ospf-1-area-0.0.0.0]
network 30.1.1.0 0.0.0.255

\[DeviceB-ospf-1-area-0.0.0.0]
quit

\[DeviceB-ospf-1] quit

**4\.**Enable the OSPF protocol on Device C.

\[DeviceC] ospf 1

\[DeviceC-ospf-1] area 0

\[DeviceC-ospf-1-area-0.0.0.0]
network 30.1.1.0 0.0.0.255

\[DeviceC-ospf-1-area-0.0.0.0]
network 40.1.1.0 0.0.0.255

\[DeviceC-ospf-1-area-0.0.0.0]
quit

\[DeviceC-ospf-1] quit

#### Verifying the configuration

\# Verify the mirroring group configuration
on Device A.

\[DeviceA] display mirroring-group all

Mirroring group 1:

    Type: Local

    Status: Active

    Mirroring port:

        Ten-GigabitEthernet1/0/1  Inbound

    Monitor port: Ten-GigabitEthernet1/0/2

                  Encapsulation:
Destination IP address 40.1.1.2

                                 Source
IP address 20.1.1.1

                                
Destination MAC address 000f-e241-5e5b

