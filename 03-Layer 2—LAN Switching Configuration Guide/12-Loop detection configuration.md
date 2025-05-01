
# Configuring loop detection

## About loop detection

The loop detection
mechanism performs periodic checking for Layer 2 loops. The mechanism immediately
generates a log when a loop occurs so that you are promptly notified to adjust network
connections and configurations. You can configure loop detection to shut down
the looped interface. Logs are maintained in the information center.For more information, see Network Management and Monitoring Configuration Guide.

### Loop detection mechanism

The device detects loops by sending
detection frames and then checking whether these frames return to any interface on the device. If they do, the device considers that the
interface is on a looped link. 

Loop detection usually works within a VLAN.
If a detection frame is returned with a different VLAN tag than it was sent out
with, an inter-VLAN loop has occurred. To remove the loop, examine the QinQ or
VLAN mapping configuration for incorrect settings. For more information about
QinQ and VLAN mapping, see "Configuring QinQ" and "Configuring
VLAN mapping." 

Figure 1 Ethernet frame headerfor loop detection

![](https://resource.h3c.com/en/202407/12/20240712_11704409_x_Img_x_png_0_2215982_294551_0.png)

‌

The Ethernet frame header of a loop
detection packet contains the following fields:

·     DMAC—Destination MAC address of the frame, which
is the multicast MAC address 010f\-e200-0007. When a
loop detection-enabled device receives a frame with this destination MAC
address, it performs the following operations:

¡     Sends
the frame to the CPU. 

¡     Floods the frame in the VLAN from which the frame was originally received.

·     SMAC—Source MAC address of the frame, which is
the bridge MAC address of the sending device.

·     TPID—Type of the VLAN tag, with the value of
0x8100.

·     TCI—Information of the VLAN tag, including the
priority and VLAN ID. 

·     Type—Protocol type, with the value of 0x8918. 

Figure 2 Inner frame headerfor loop detection

![](https://resource.h3c.com/en/202407/12/20240712_11704410_x_Img_x_png_1_2215982_294551_0.png)

‌

The inner frame header of a loop detection packet
contains the following fields:

·     Code—Protocol sub-type, which
is 0x0001, indicating the loop detection protocol. 

·     Version—Protocol version, which
is always 0x0000. 

·     Length—Length of the frame.
The value includes the inner header, but excludes the Ethernet header. 

·     Reserved—This field is reserved.

Frames for loop
detection are encapsulated as TLV triplets.

Table 1 TLVs supported by loop detection

| TLV | Description | Remarks |
| --- | --- | --- |
| End of PDU | End of a PDU. | Optional. || Device ID | Bridge MAC address of the sending device. | Required. || Port ID | ID of the PDU sending interface. | Optional. || Port Name | Name of the PDU sending interface. | Optional. || System Name | Device name. | Optional. || Chassis ID | Chassis ID of the sending interface. | Optional. || Slot ID | Slot ID of the sending interface. | Optional. || Sub Slot ID | Sub-slot ID of the sending interface. | Optional. |








‌

### Loop detection interval

Loop detection is a
continuous process as the network changes. Loop detection frames are sent at the
loop detection interval to determine whether loops occur on interfaces and
whether loops are removed.

### Loop protection actions

When the device detects a loop on an
interface, it generates a log but performs no action on the interface by
default. You can configure the device to take one of the following actions:

·     Block—Disables the interface from learning MAC addresses and blocks the interface.

·     No-learning—Disables the interface from learning MAC addresses.

·     Shutdown—Shuts down the interface to disable it from receiving and sending any
frames.

### Loop protection delay

The loop protection delay enables the
device to wait for a period of time before taking loop protection actions on
looped interfaces. On a network protected by loop detection, the shutdown or
block action taken on a looped interface of an upstream device might cause traffic
interruption to its downstream devices. To minimize the impacts of loop
detection on downstream traffic, set the loop protection delay on the upstream
devices. This delay allows the downstream devices to remove loops before the
upstream devices take an action.

### Interface status auto recovery

When the device configured with the block or
no-learning loop action detects a loop on an interface, it performs the action
and waits three loop detection intervals. If the device does not receive a loop
detection frame within three loop detection intervals, it performs the
following operations:

·     Automatically sets the interface to the
forwarding state.

·     Notifies the user of the event.

When the device configured with the
shutdown action detects a loop on an interface, the following events occur:

**1\.**The device automatically shuts down the interface.


**2\.**The device automatically sets the interface
to the forwarding state after the detection timer set by using the shutdown-interval command expires. For more information about
the shutdown-interval command, see Fundamentals Command Reference. 

**3\.**The device shuts down the interface again if
a loop is still detected on the
interface when the detection timer expires. 

This process is repeated until the loop is
removed.

 

|  | NOTE:  Incorrect recovery can occur when loop detection frames are discarded to reduce the load. To avoid this, use the shutdown action, or manually remove the loop. |
| --- | --- |

‌

## Restriction and guidelines: M-LAG configuration

Member devices in an M-LAG system must have
the same loop detection configuration.

## Loop detection tasks at a glance

To configure loop detection, perform the following
tasks:

**1\.**[Enabling loop detection](#_Ref330482368)

¡     [Enabling loop detection globally](#_Ref474769543)

¡     [Enabling loop detection on an interface](#_Ref49171151)

**2\.**(Optional) [Setting the loop protection action](#_Ref330482208)

¡     [Setting the global loop protection action](#_Ref474769555)

¡     [Setting the loop protection action on an interface](#_Ref49171169)

**3\.**(Optional) [Setting the loop detection interval](#_Ref226193081)

**4\.**(Optional) [Setting the loop protection delay timer](#_Ref49171181)

¡     [Setting the global loop protection delay
timer](#_Ref49171208)

¡     [Setting the loop protection delay timer on an
interface](#_Ref49171214)

## Enabling loop detection

### Restrictions and guidelines for loop detection configuration

You can enable loop detection globally or
on a per-interface basis. When an interface receives a detection frame in any
VLAN, the loop protection action is triggered on that interface, regardless of
whether loop detection is enabled on it.

The loop detection feature is mutually
exclusive with Layer 2 loop prevention features, including:

·     The spanning tree feature.

·     RRPP.

·     ERPS. 

To avoid unexpected network issues, do not
use the loop detection feature and any of those Layer 2 loop prevention
features together. For more information about the spanning tree feature, see "Configuring
spanning tree protocols." For more information about RRPP and ERPS, see High Availability Configuration Guide.

### Enabling loop detection globally

**1\.**Enter system view.

system-view

**2\.**Globally enable loop detection.

loopback-detection global enable vlan { vlan-id--list \| all }

By default, loop detection is globally
disabled.

### Enabling loop detection onan interface

**1\.**Enter system view.

system-view

**2\.**Enter Layer 2 Ethernet interface view or
Layer 2 aggregate interface view. 

interface interface-type
interface-number

**3\.**Enable loop detection on the interface.

loopback-detection enable vlan { vlan-id--list \| all }

By default, loop detection is disabled on
interfaces.

## Setting the loop protection action

### Restrictions and guidelines for loop protection action configuration

You can set the loop protection action
globally or on a per-interface basis. The global action applies to all interfaces.
An interface-specific action applies to the interface on which it is configured.
On an interface, the interface-specific action takes precedence over the global
action.

### Setting the global loop protection action

**1\.**Enter system view.

system-view

**2\.**Set the global loop protection action.

loopback-detection global action shutdown

By default, the device generates a log
but performs no action on the interface on which a loop is detected. 

### Setting the loop protection action on an interface

**1\.**Enter system view.

system-view

**2\.**Enter interface view.

interface interface-type
interface-number

**3\.**Set the loop protection action on the
interface.

loopback-detection action { block \| no-learning \| shutdown }

By default, the device generates a log
but performs no action on the interface on which a loop is detected.

Support for the keywords of this command varies by interface type.
For more information, see Layer 2—LAN Switching Command Reference.

## Setting the loop detection interval

#### About this task

With loop detection enabled, the device sends
loop detection frames at the loop detection interval. A shorter interval offers
more sensitive detection but consumes more resources. Consider the system performance
and loop detection speed when you set the loop detection interval. 

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Set the loop detection interval.

loopback-detection interval-time interval

The default setting is 30 seconds.

## Setting the loop protection delay timer

### Restrictions and guidelines for loop protection delay timer configuration

The global delay timer applies to all interfaces
enabled with loop detection. An interface-specific delay timer applies to the interface
on which it is configured. On an interface, the interface-specific delay timer
takes precedence over the global delay timer.

To prevent interfaces from going down or
coming up concurrently, set the loop protection delay timer to different values
on different devices or on different interfaces of the same device.

### Setting the global loop protection delay timer

**1\.**Enter system view.

system-view

**2\.**Set the global loop protection delay timer.

loopback-detection global delay-timer time

By default, loop protection delay is
disabled globally.

### Setting the loop protection delay timer on an interface

**1\.**Enter system view.

system-view

**2\.**Enter interface view.

interface interface-type
interface-number

**3\.**Set the loop protection delay timer on the
interface.

loopback-detection global delay-timer time

By default, loop protection delay is
disabled on an interface.

## Display and maintenance commands for loop detection

Execute display commands
in any view.

 

| Task | Command |
| --- | --- |
| Display the loop detection configuration and status. | display loopback-detection |

‌

## Loop detection configuration examples

### Example: Configuring basic loop detection functions

#### Network configuration

As shown in [Figure 3](#_Ref257993452), configure
loop detection on Device A to meet the following requirements: 

·     Device A generates a log as a notification. 

·     Device A automatically shuts down the interface on
which a loop is detected. 

Figure 3 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11704411_x_Img_x_png_2_2215982_294551_0.png)

‌

#### Procedure

**1\.**Configure Device A:

\# Create VLAN 100, and globally enable
loop detection for the VLAN.

\<DeviceA\> system-view

\[DeviceA] vlan 100

\[DeviceA-vlan100] quit

\[DeviceA] loopback-detection
global enable vlan 100

\# Configure Ten-GigabitEthernet 1/0/1 and Ten-GigabitEthernet 1/0/2 as trunk ports, and
assign them to VLAN 100\.

\[DeviceA] interface Ten-GigabitEthernet 1/0/1

\[DeviceA-Ten-GigabitEthernet1/0/1] port link-type
trunk

\[DeviceA-Ten-GigabitEthernet1/0/1] port trunk permit
vlan 100

\[DeviceA-Ten-GigabitEthernet1/0/1] quit

\[DeviceA] interface ten-gigabitethernet 1/0/2

\[DeviceA-Ten-GigabitEthernet1/0/2] port link-type
trunk

\[DeviceA-Ten-GigabitEthernet1/0/2] port trunk permit
vlan 100

\[DeviceA-Ten-GigabitEthernet1/0/2] quit

\# Set the global loop protection action
to shutdown.

\[DeviceA] loopback-detection global
action shutdown

\# Set the loop detection interval to 35
seconds.

\[DeviceA] loopback-detection
interval-time 35

**2\.**Configure Device B:

\# Create VLAN 100\.

\<DeviceB\> system-view

\[DeviceB] vlan 100

\[DeviceB–vlan100] quit

\# Configure Ten-GigabitEthernet 1/0/1 and Ten-GigabitEthernet 1/0/2 as trunk ports, and
assign them to VLAN 100\.

\[DeviceB] interface ten-gigabitethernet 1/0/1

\[DeviceB-Ten-GigabitEthernet1/0/1] port link-type
trunk

\[DeviceB-Ten-GigabitEthernet1/0/1] port trunk permit
vlan 100

\[DeviceB-Ten-GigabitEthernet1/0/1] quit

\[DeviceB] interface ten-gigabitethernet 1/0/2

\[DeviceB-Ten-GigabitEthernet1/0/2] port link-type
trunk

\[DeviceB-Ten-GigabitEthernet1/0/2] port trunk permit
vlan 100

\[DeviceB-Ten-GigabitEthernet1/0/2] quit

**3\.**Configure Device C:

\# Create VLAN 100\.

\<DeviceC\> system-view

\[DeviceC] vlan 100

\[DeviceC–vlan100] quit

\# Configure Ten-GigabitEthernet 1/0/1 and Ten-GigabitEthernet 1/0/2 as trunk ports, and
assign them to VLAN 100\.

\[DeviceC] interface ten-gigabitethernet 1/0/1

\[DeviceC-Ten-GigabitEthernet1/0/1] port link-type
trunk

\[DeviceC-Ten-GigabitEthernet1/0/1] port trunk permit
vlan 100

\[DeviceC-Ten-GigabitEthernet1/0/1] quit

\[DeviceC] interface ten-gigabitethernet 1/0/2

\[DeviceC-Ten-GigabitEthernet1/0/2] port link-type
trunk

\[DeviceC-Ten-GigabitEthernet1/0/2] port trunk permit
vlan 100

\[DeviceC-Ten-GigabitEthernet1/0/2] quit

#### Verifying the configuration

\# View the system logs on devices, for
example, Device A. 

\[DeviceA]

%Feb 24 15:04:29:663 2019 DeviceA
LPDT/4/LPDT\_LOOPED:  A loop was detected on Ten-GigabitEthernet1/0/1.

%Feb 24 15:04:29:664 2019 DeviceA
LPDT/4/LPDT\_VLAN\_LOOPED: A loop was detected on Ten-GigabitEthernet1/0/1 in VLAN 100\.

%Feb 24 15:04:29:667 2019 DeviceA
LPDT/4/LPDT\_LOOPED: A loop was detected on Ten-GigabitEthernet1/0/2.

%Feb 24 15:04:29:668 2019 DeviceA
LPDT/4/LPDT\_VLAN\_LOOPED: A loop was detected on Ten-GigabitEthernet1/0/2 in VLAN 100\.

%Feb 24 15:04:44:243 2019 DeviceA
LPDT/5/LPDT\_VLAN\_RECOVERED: A loop was removed on Ten-GigabitEthernet1/0/1 in VLAN 100\.

%Feb 24 15:04:44:243 2019 DeviceA
LPDT/5/LPDT\_RECOVERED: All loops were removed on Ten-GigabitEthernet1/0/1.

%Feb 24 15:04:44:248 2019 DeviceA
LPDT/5/LPDT\_VLAN\_RECOVERED: A loop was removed on Ten-GigabitEthernet1/0/2 in VLAN 100\.

%Feb 24 15:04:44:248 2019 DeviceA
LPDT/5/LPDT\_RECOVERED: All loops were removed on Ten-GigabitEthernet1/0/2.

The output shows the following information:


·     Device A detected loops on Ten-GigabitEthernet 1/0/1 and Ten-GigabitEthernet 1/0/2 within a loop
detection interval. 

·     Loops on Ten-GigabitEthernet 1/0/1 and Ten-GigabitEthernet 1/0/2 were removed.

\# Use the display
loopback-detection command to display the loop
detection configuration and status on devices, for example, Device A.

\[DeviceA] display loopback-detection

Loop detection is enabled.

Global loop detection interval is 35
second(s).

Loop is detected on following
interfaces:

  Interface                      Action
mode     VLANs/VSI

 Ten-GigabitEthernet1/0/1       Shutdown       
100

 Ten-GigabitEthernet1/0/2       Shutdown       
100

The output shows that the device has
removed the loops from Ten-GigabitEthernet 1/0/1 and Ten-GigabitEthernet 1/0/2 according to the
shutdown action.

\# Display the status of Ten-GigabitEthernet 1/0/1 on devices, for
example, Device A.

\[DeviceA] display interface ten-gigabitethernet 1/0/1

Ten-GigabitEthernet1/0/1 current state: DOWN (Loop detection down)

...

The output shows that Ten-GigabitEthernet 1/0/1 is already shut
down by the loop detection module.

\# Display the status of Ten-GigabitEthernet 1/0/2 on devices, for
example, Device A.

\[DeviceA] display interface ten-gigabitethernet 1/0/2

Ten-GigabitEthernet1/0/2 current state: DOWN (Loop detection down)

...

The output shows that Ten-GigabitEthernet 1/0/2 is already shut
down by the loop detection module.

### Example: Configuring loop detection on an M-LAG system

#### Network configuration

As shown in [Figure 4](#_Ref25310261),
configure loop detection on the M-LAG system formed by Device A and Device B to
meet the following requirements:

·     Generates a log as a notification.

·     Automatically shuts down the interface on which
a loop is detected.

Figure 4 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11704412_x_Img_x_png_3_2215982_294551_0.png)

‌

#### Procedure

**1\.**Configure Device A:

\# Create VLAN 100\.

\<DeviceA\> system-view

\[DeviceA] vlan 100

\[DeviceA-vlan100] quit

\# Configure M-LAG system settings.

\[DeviceA] m-lag system-mac
1-1-1

\[DeviceA] m-lag system-number
1

\[DeviceA] m-lag
system-priority 123

\# Configure M-LAG keepalive packet
parameters.

\[DeviceA] m-lag keepalive ip
destination 1.1.1.1 source 1.1.1.2

\# Set the link mode of Ten-GigabitEthernet 1/0/6 to Layer 3, and
assign the interface an IP address. The IP address will be used as the source
IP address of keepalive packets.

\[DeviceA] interface ten-gigabitethernet 1/0/6

\[DeviceA-Ten-GigabitEthernet1/0/6] port link-mode
route

\[DeviceA-Ten-GigabitEthernet1/0/6] ip address 1.1.1.2
24

\[DeviceA-Ten-GigabitEthernet1/0/6] quit

\# Exclude the interface used for M-LAG
keepalive detection (Ten-GigabitEthernet 1/0/6) from the shutdown
action by M-LAG MAD.

\[DeviceA] drni m-lag exclude
interface ten-gigabitethernet 1/0/6

\# Create Layer 2 dynamic aggregate
interface Bridge-Aggregation 3, and specify it as the M-LAG peer-link interface.

\[DeviceA] interface
bridge-aggregation 3

\[DeviceA-Bridge-Aggregation3]
link-aggregation mode dynamic

\[DeviceA-Bridge-Aggregation3]
port m-lag peer-link 1

\[DeviceA-Bridge-Aggregation3]
quit

\# Assign Ten-GigabitEthernet 1/0/5 to aggregation
group 3\.

\[DeviceA] interface ten-gigabitethernet 1/0/5

\[DeviceA-Ten-GigabitEthernet1/0/5] port
link-aggregation group 3

\[DeviceA-Ten-GigabitEthernet1/0/5] quit

\# Set the link type of Bridge-Aggregation
3 to trunk, and assign it to VLAN 100\.

\[DeviceA] interface
bridge-aggregation 3

\[DeviceA-Bridge-Aggregation3]
port link-type trunk

\[DeviceA-Bridge-Aggregation3]
port trunk permit vlan 100

\[DeviceA-Bridge-Aggregation3]
quit

\# Create Layer 2 dynamic aggregate
interface Bridge-Aggregation 4, and assign it to M-LAG group 4\.

\[DeviceA] interface
bridge-aggregation 4

\[DeviceA-Bridge-Aggregation4]
link-aggregation mode dynamic

\[DeviceA-Bridge-Aggregation4]
port m-lag group 4

\[DeviceA-Bridge-Aggregation4]
quit

\# Assign Ten-GigabitEthernet 1/0/1 and Ten-GigabitEthernet 1/0/2 to aggregation
group 4\.

\[DeviceA] interface ten-gigabitethernet 1/0/1

\[DeviceA-Ten-GigabitEthernet1/0/1] port
link-aggregation group 4

\[DeviceA-Ten-GigabitEthernet1/0/1] quit

\[DeviceA] interface ten-gigabitethernet 1/0/2

\[DeviceA-Ten-GigabitEthernet1/0/2] port
link-aggregation group 4

\[DeviceA-Ten-GigabitEthernet1/0/2] quit

\# Set the link type of Bridge-Aggregation
4 to trunk, and assign it to VLAN 100\.

\[DeviceA] interface
bridge-aggregation 4

\[DeviceA-Bridge-Aggregation4]
port link-type trunk

\[DeviceA-Bridge-Aggregation4]
port trunk permit vlan 100

\[DeviceA-Bridge-Aggregation4]
quit

\# Create Layer 2 dynamic aggregate
interface Bridge-Aggregation 5, and assign it to M-LAG group 5\.

\[DeviceA] interface
bridge-aggregation 5

\[DeviceA-Bridge-Aggregation5]
link-aggregation mode dynamic

\[DeviceA-Bridge-Aggregation5]
port m-lag group 5

\[DeviceA-Bridge-Aggregation5]
quit

\# Assign Ten-GigabitEthernet 1/0/3 and Ten-GigabitEthernet 1/0/4 to aggregation
group 5\.

\[DeviceA] interface ten-gigabitethernet 1/0/3

\[DeviceA-Ten-GigabitEthernet1/0/3] port
link-aggregation group 5

\[DeviceA-Ten-GigabitEthernet1/0/3] quit

\[DeviceA] interface ten-gigabitethernet 1/0/4

\[DeviceA-Ten-GigabitEthernet1/0/4] port
link-aggregation group 5

\[DeviceA-Ten-GigabitEthernet1/0/4] quit

\# Set the link type of Bridge-Aggregation
5 to trunk, and assign it to VLAN 100\.

\[DeviceA] interface
bridge-aggregation 5

\[DeviceA-Bridge-Aggregation5]
port link-type trunk

\[DeviceA-Bridge-Aggregation5]
port trunk permit vlan 100

\[DeviceA-Bridge-Aggregation5]
quit

\# Disable the spanning tree feature.

\[DeviceA] undo stp global
enable

\# Enable loop detection for VLAN 100
globally, set the global loop protection action to shutdown, and set the loop
detection interval to 35 seconds.

\[DeviceA] loopback-detection
global enable vlan 100

\[DeviceA] loopback-detection
global action shutdown

\[DeviceA] loopback-detection
interval-time 35

**2\.**Configure Device B in the same way Device A
is configured. Set the M-LAG system number to 2, and set the source and
destination IP addresses of keepalive packets to 1.1.1.1 and 1.1.1.2,
respectively. (Details not shown.)

**3\.**Configure Device C:

\# Disable the spanning tree feature.

\<DeviceC\> system-view

\[DeviceC] undo stp global
enable

\# Create VLAN 100\.

\[DeviceC] vlan 100

\[DeviceC-vlan100] quit

\# Create Layer 2 dynamic aggregate
interface Bridge-Aggregation 4\.

\[DeviceC] interface
bridge-aggregation 4

\[DeviceC-Bridge-Aggregation4]
link-aggregation mode dynamic

\[DeviceC-Bridge-Aggregation4]
quit

\# Assign Ten-GigabitEthernet 1/0/1 through Ten-GigabitEthernet 1/0/4 to aggregation
group 4\.

\[DeviceC] interface range ten-gigabitethernet 1/0/1 to ten-gigabitethernet 1/0/4

\[DeviceC-if-range] port
link-aggregation group 4

\[DeviceC-if-range] quit

\# Set the link type of Bridge-Aggregation
4 to trunk, and assign it to VLAN 100\.

\[DeviceC] interface
bridge-aggregation 4

\[DeviceC-Bridge-Aggregation4] port
link-type trunk

\[DeviceC-Bridge-Aggregation4]
port trunk permit vlan 100

\[DeviceC-Bridge-Aggregation4]
quit

\# Set the link type of Ten-GigabitEthernet 1/0/5 to trunk, and
assign it to VLAN 100\.

\[DeviceC] interface ten-gigabitethernet 1/0/5

\[DeviceC-Ten-GigabitEthernet1/0/5] port link-type
trunk

\[DeviceC-Ten-GigabitEthernet1/0/5] port trunk permit
vlan 100

\[DeviceC-Ten-GigabitEthernet1/0/5] quit

**4\.**Configure Device D:

\# Disable the spanning tree feature.

\<DeviceD\> system-view

\[DeviceD] undo stp global
enable

\# Create VLAN 100\.

\[DeviceD] vlan 100

\[DeviceD-vlan100] quit

\# Create Layer 2 dynamic aggregate
interface Bridge-Aggregation 5\.

\[DeviceD] interface
bridge-aggregation 5

\[DeviceD-Bridge-Aggregation5]
link-aggregation mode dynamic

\[DeviceD-Bridge-Aggregation5]
quit

\# Assign Ten-GigabitEthernet 1/0/1 through Ten-GigabitEthernet 1/0/4 to aggregation
group 5\.

\[DeviceD] interface range ten-gigabitethernet 1/0/1 to ten-gigabitethernet 1/0/4

\[DeviceD-if-range] port
link-aggregation group 5

\[DeviceD-if-range] quit

\# Set the link type of Bridge-Aggregation
5 to trunk, and assign it to VLAN 100\.

\[DeviceD] interface
bridge-aggregation 5

\[DeviceD-Bridge-Aggregation5]
port link-type trunk

\[DeviceD-Bridge-Aggregation5]
port trunk permit vlan 100

\[DeviceD-Bridge-Aggregation5]
quit

\# Set the link type of Ten-GigabitEthernet 1/0/5 to trunk, and
assign it to VLAN 100\.

\[DeviceD] interface ten-gigabitethernet 1/0/5

\[DeviceD-Ten-GigabitEthernet1/0/5] port link-type
trunk

\[DeviceD-Ten-GigabitEthernet1/0/5] port trunk permit
vlan 100

\[DeviceD-Ten-GigabitEthernet1/0/5] quit

**5\.**Configure Device E:

\# Disable the spanning tree feature.

\<DeviceE\> system-view

\[DeviceE] undo stp global
enable

\# Create VLAN 100\.

\[DeviceE] vlan 100

\[DeviceE-vlan100] quit

\# Set the link type of Ten-GigabitEthernet 1/0/1 and Ten-GigabitEthernet 1/0/2 to trunk, and
assign them to VLAN 100\.

\[DeviceE] interface ten-gigabitethernet 1/0/1

\[DeviceE-Ten-GigabitEthernet1/0/1] port link-type
trunk

\[DeviceE-Ten-GigabitEthernet1/0/1] port trunk permit
vlan 100

\[DeviceE-Ten-GigabitEthernet1/0/1] quit

\[DeviceE] interface ten-gigabitethernet 1/0/2

\[DeviceE-Ten-GigabitEthernet1/0/2] port link-type
trunk

\[DeviceE-Ten-GigabitEthernet1/0/2] port trunk permit
vlan 100

\[DeviceE-Ten-GigabitEthernet1/0/2] quit

#### Verifying the configuration

\# View the system logs on Device A. 

\[DeviceA]

%Aug  1 03:28:48:110 2019 DeviceA
LPDT/4/LPDT\_LOOPED: A loop was detected on

 Bridge-Aggregation4.

%Aug  1 03:28:48:191 2019 DeviceA
LPDT/4/LPDT\_VLAN\_LOOPED: A loop was detect

ed on Bridge-Aggregation4 in VLAN
100\.

%Aug  1 03:28:48:194 2019 DeviceA
LPDT/4/LPDT\_LOOPED: A loop was detected on

 Bridge-Aggregation5. 

%Aug  1 03:28:48:288 2019 DeviceA
LPDT/4/LPDT\_VLAN\_LOOPED: A loop was detect

ed on Bridge-Aggregation5 in VLAN
100\. 

%Aug  1 03:28:48:290 2019 DeviceA
LPDT/5/LPDT\_VLAN\_RECOVERED: A loop was rem

oved on Bridge-Aggregation4 in VLAN
100\.

%Aug  1 03:28:48:291 2019 DeviceA
LPDT/5/LPDT\_RECOVERED: All loops were remo

ved on Bridge-Aggregation4. 

%Aug  1 03:28:48:302 2019 DeviceA
LPDT/5/LPDT\_VLAN\_RECOVERED: A loop was rem

oved on Bridge-Aggregation5 in VLAN
100\.

%Aug  1 03:28:48:304 2019 DeviceA
LPDT/5/LPDT\_RECOVERED: All loops were remo

ved on Bridge-Aggregation5.

The output shows the following information:


·     Device A detected loops on Bridge-Aggregation 4
and Bridge-Aggregation 5 within a loop detection interval. 

·     Loops on Bridge-Aggregation 4 and Bridge-Aggregation
5 were removed.

\# Use the display
loopback-detection command to display the loop
detection configuration and status on Device A.

\[DeviceA] display loopback-detection

Loop detection is enabled.

Global loop detection interval is 35
second(s).

Loop is detected on following
interfaces:

  Interface                      
Action mode     VLANs/VSI

  Bridge-Aggregation4            
Shutdown        100

  Bridge-Aggregation5            
Shutdown        100

The output shows that the device has
removed the loops from Bridge-Aggregation 4 and Bridge-Aggregation 5 according
to the shutdown action.

\# Verify that Bridge-Aggregation 4 has been
shut down by loop detection.

\[DeviceA] display interface
Bridge-Aggregation 4 

Bridge-Aggregation4

The interface has assigned a M-LAG
group.

Current state: DOWN (Loopback
detection down) 

…

\# Verify that Bridge-Aggregation 5 has been
shut down by loop detection.

\[DeviceA] display interface Bridge-Aggregation
5

Bridge-Aggregation5

The interface has assigned a M-LAG
group.

Current state: DOWN (Loopback
detection down)

…

\# Verify that loops have been removed on Device
B. (Details not shown.)

 

