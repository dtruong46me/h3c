
# Configuring Ethernet interfaces

## About Ethernet interface

The Switch Series supports Ethernet
interfaces, management Ethernet interfaces, Console interfaces, and USB
interfaces. For the interface types and the number of interfaces supported by a
switch model, see the installation guide and hardware manuals.

This chapter describes how to configure
management Ethernet interfaces and Ethernet interfaces.

## Configuring a management Ethernet interface

#### About this task

You can connect a management Ethernet interface
to a PC for software loading and system debugging, or connect it to a remote
NMS for remote system management.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter management Ethernet interface view.

interface M-GigabitEthernet
interface-number

**3\.**(Optional.) Set the interface description.

description text

The default setting is M-GigabitEthernet0/0/0 Interface. 

**4\.**(Optional.) Set the duplex mode for the
management Ethernet interface.

duplex { auto \| full \| half }

By default, the duplex mode is auto for a management Ethernet interface.

**5\.**(Optional.)\_Set the speed for the management
Ethernet interface.

speed { 10 \| 100 \| 1000 \| auto }

By default, the speed is auto for a management Ethernet interface.

**6\.**(Optional.) Shut down the interface.

shutdown

By default, the management Ethernet
interface is up.

 

| CAUTION | CAUTION:  Executing the shutdown command on an interface will disconnect the link of the interface and interrupt communication. Use this command with caution. |
| --- | --- |

‌

## Ethernet interface naming conventions

The Ethernet interfaces are named in the
format of interface type A/B/C. The letters that
follow the interface type represent the following elements: 

·     A—IRF member ID. If the switch is not in an IRF fabric, A is 1 by
default.

·     B—Card slot number. 0 indicates the
interface is a fixed interface of the switch.

·     C—Port index. 

A 10-GE breakout interface split from a
40-GE interface is named in the format of interface type
A/B/C:D. A/B/C is the interface number of
the 40-GE interface. D is the number of the 10-GE
interface, which is in the range of 1 to 4\. For information about splitting a
40-GE interface into 10-GE breakout interfaces, see "[Splitting a 40-GE interface and combining
10-GE breakout interfaces](#_Ref479952899)."

A 25-GE breakout interface split from a 100-GE
interface is named in the format of interface type A/B/C:D.
A/B/C is the interface number of the 100-GE
interface. D is the number of the 25-GE interface,
which is in the range of 1 to 4\. For information about splitting a 100-GE
interface into 25-GE breakout interfaces, see "[Splitting a 100-GE interface and combining 25-GE
breakout interfaces](#_Ref514317502)." 

## Restrictions and guidelines: Ethernet interface configuration

When you configure the Ethernet interface
speed, follow these restrictions and guidelines:

·     For an Ethernet copper port, use the speed command to set its speed to match the speed of the peer interface.
Support of copper ports for keywords of this command varies by copper port
type. For more information, use the speed ?
command in interface view. If the system does not prompt that operation failed
when you configure a speed for a copper port, the copper port supports this
speed. Otherwise, the copper port does not support this speed.

·     For a fiber port, use the speed command to set its speed to match the rate of a transceiver module.
Support of fiber ports for keywords of this command varies by fiber port type.
For more information, use the speed ?
command in interface view. If the system does not prompt that operation failed
when you configure a speed for a fiber port, the fiber port supports this
speed. Otherwise, the fiber port does not support this speed. Additionally, you
must select a speed for a fiber port according to the transceiver module
installed to ensure that the transceiver module can be used properly. If the
transceiver module installed in a fiber port does not support the speed for the
fiber port, the transceiver module cannot be used. For example, the transceiver
module cannot be used in either of the following conditions:. 

¡     The
transceiver module installed in an SFP\+ fiber port is an SFP GE transceiver
module and the speed 10000 command is
executed on the fiber port. 

¡     The
transceiver module installed in an SFP\+ fiber port is a 10-GE transceiver
module and the speed 1000 command is
executed on the fiber port.

Interfaces on an LSWM2SP2PM or LSWM2XGT2PM
interface card installed in the S6520X-SI switch series can operate only at 10
Gbps. 

On an S6520X-54HC-EI or S6520X-54HC-HI
switch, interfaces numbered 1 through 8 do not support 1000-Mbps SFP
transceiver modules or cables, and these interfaces can operate only at 10
Gbps. 

When you configure 25-GE interfaces on the LSWM2ZSP8P
interface module, follow these restrictions and guidelines:

·     Interfaces 1 through 4 are in the same group,
and interfaces 5 through 8 are in the same group. Interfaces in the same group
must be configured with the same speed and all have the 10G or 25G transceiver
modules or cables installed. When interfaces have 25G transceiver modules or
cables installed, the interfaces can operate only at 25 Gbps. 

·     These interfaces do not support speed or duplex
mode autonegotiation. You must execute the speed and duplex commands to manually configure the same speed and duplex mode for
interfaces at both ends of a link. For an interface to come up, make sure the
speed configured for the interface matches the speed of the transceiver module
or cable installed. 

·     When the speed of an interface is different from
the speed of the transceiver module or cable installed, perform the following
tasks:

**a.**Execute the shutdown command to shut down the interface. 

**b.**Execute the speed command to configure the speed of the interface as the speed of the
transceiver module or cable installed. 

**c.**Execute the undo shutdown command to
bring up the interface. 

The Ethernet interface configuration
restrictions might be updated because more switch models and compatible
interface card models will be added. For exact information, see the
installation guide or hardware description for the product. 

## Configuring common Ethernet interface settings

This section describes the settings common
to Layer 2 Ethernet interfaces, Layer 3 Ethernet interfaces, and Layer 3
Ethernet subinterfaces. For more information about the settings specific to
Layer 2 Ethernet interfaces, see "[Configuring a Layer 2 Ethernet interface](#_Ref433700339)." For more information about the settings specific to Layer 3
Ethernet interfaces or subinterfaces, see "[Configuring a Layer 3 Ethernet interface
or subinterface](#_Ref433700355)."

### Configuring the physical type for a combo interface (single combo interface)

#### About this task

A combo interface is a logical interface
that physically comprises one fiber combo port and one copper combo port. The
two ports share one forwarding channel and one interface view. As a result,
they cannot work simultaneously. When you activate one port, the other port is automatically
disabled. If you execute the combo enable
auto command on a combo interface, the interface automatically
identifies the media inserted and activates the corresponding combo port. In
the interface view, you can activate the fiber or copper combo port, and configure
other port attributes such as the interface rate and duplex mode.

#### Prerequisites

Before you configure combo interfaces,
complete the following tasks:

·     Determine the combo interfaces on your device. Identify
the two physical interfaces that belong to each combo interface according to
the marks on the device panel.

·     Use the display interface command to determine which port (fiber or copper) of each combo interface
is active:

¡     If
the copper port is active, the output includes "Media type is twisted pair."

¡     If the
fiber port is active, the output does not include this information.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter Ethernet interface view.

interface interface-type interface-number

**3\.**Activate the copper combo port or fiber
combo port.

combo enable { auto \| copper \| fiber }

The default is auto.

### Splitting a 40-GE interface and combining 10-GE breakout interfaces

#### About this task

You can use a 40-GE interface as a single
interface. To improve port density, reduce costs, and improve network
flexibility, you can also split a 40-GE interface into four 10-GE breakout interfaces.
The 10-GE breakout interfaces support the same configuration and attributes as
common 10-GE interfaces, except that they are numbered differently.

For example, you can split 40-GE interface
FortyGigE 1/0/1 into four 10-GE breakout interfaces Ten-GigabitEthernet 1/0/1:1
through Ten-GigabitEthernet 1/0/1:4.

If you need higher bandwidth on a single
interface, you can combine the four 10-GE breakout interfaces into a 40-GE
interface.

#### Restrictions and guidelines for 40-GE interface splitting and 10-GE breakout interface combining

·     A 40-GE interface split into four 10-GE breakout
interfaces must use a dedicated 1-to-4 cable. After you combine the four 10-GE breakout
interfaces, replace the dedicated 1-to-4 cable with a dedicated 1-to-1 cable or
a 40-GE transceiver module. For more information about the cable or transceiver
module, see the installation guides.

·     Device reboot is not required for this feature
to take effect. To view information about the breakout or combined interfaces,
execute the display interface brief command. The 10-GE breakout interfaces cannot act as IRF physical
interfaces. 

#### Splitting a 40-GE interface into four 10-GE breakout interfaces

**1\.**Enter system view.

system-view

**2\.**Enter 40-GE interface view.

interface fortygige
interface-number

**3\.**Split the 40-GE interface into four 10-GE
breakout interfaces.

using tengige

By default, a 40-GE interface is not
split and operates as a single interface.

#### Combining four 10-GE breakout interfaces into a 40-GE interface

**1\.**Enter system view.

system-view

**2\.**Enter the view of any 10-GE breakout
interface.

interface ten-gigabitethernet
interface-number

**3\.**Combine the four 10-GE breakout interfaces
into a 40-GE interface.

using fortygige

By default, a 10-GE breakout interface operates
as a single interface.

### Splitting a 100-GE interface and combining 25-GE breakout interfaces

#### About this task

You can use a 100-GE interface as a single
interface. To improve port density, reduce costs, and improve network
flexibility, you can also split a 100-GE interface into four 25-GE breakout
interfaces. The 25-GE breakout interfaces support the same configuration and
attributes as common 25-GE interfaces, except that they are numbered differently.

For example, you can split 100-GE interface
HundredGigE 1/0/1 into four 25-GE breakout interfaces Twenty-FiveGigE 1/0/1:1
through Twenty-FiveGigE 1/0/1:4. 

If you need higher bandwidth on a single
interface, you can combine the four 25-GE breakout interfaces into a 100-GE
interface.

 

#### Restrictions and guidelines for 100-GE interface splitting and 25-GE breakout interface combining

·     A 100-GE interface split into four 25-GE
breakout interfaces must use a dedicated 1-to-4 cable. After you combine the four
25-GE breakout interfaces, replace the dedicated 1-to-4 cable with a dedicated
1-to-1 cable or a 100-GE transceiver module. For more information about the
cable or transceiver module, see the installation guides.

·     This feature is available only on devices that
support 100-GE interfaces. 

·     Device reboot is not required for this feature
to take effect. To view information about the breakout or combined interfaces,
execute the display interface brief
command. The 25-GE breakout interfaces cannot act as IRF physical interfaces. 

#### Splitting a 100-GE interface into four 25-GE breakout interfaces

**1\.**Enter system view.

system-view

**2\.**Enter 100-GE interface view.

interface hundredgige
interface-number

**3\.**Split the 100-GE interface into four 25-GE
breakout interfaces.

using twenty-fivegige

By default, a 100-GE interface is not
split and operates as a single interface.

#### Combining four 25-GE breakout interfaces into a 100-GE interface

**1\.**Enter system view.

system-view

**2\.**Enter the view of any 25-GE breakout
interface.

interface twenty-fivegige
interface-number

**3\.**Combine the four 25-GE breakout interfaces
into a 100-GE interface.

using hundredgige

By default, a 25-GE breakout interface operates
as a single interface.

### Configuring basic settings of an Ethernet interface

#### About this task

You can configure an Ethernet interface to
operate in one of the following duplex modes:

·     Full-duplex mode—The interface can send and receive packets simultaneously.

·     Autonegotiation mode—The interface negotiates a duplex mode with its peer.

You can set the speed of an Ethernet
interface or enable it to automatically negotiate a speed with its peer. 

#### Restrictions and guidelines

The shutdown and
port up-mode commands are
mutually exclusive. 

The shutdown
command cannot be configured on an Ethernet interface in a loopback test. 

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter Ethernet interface view.

interface interface-type interface-number

**3\.**Set the description for the Ethernet
interface.

description text

The default setting is interface-name Interface. For example, Ten-GigabitEthernet1/0/1 Interface.

**4\.**Set the duplex mode for the Ethernet
interface.

duplex { auto \| full }

By default, the duplex mode is auto for Ethernet interfaces.

Ethernet copper ports that operate in
1000 Mbps or 10000 Mbps and fiber ports do not support the half keyword. 

**5\.**Set the speed for the Ethernet interface.

speed { 10 \| 100 \| 1000 \| 2500 \| 5000 \| 10000 \| 25000 \| 40000 \| 100000 \| auto }

By default, an Ethernet interface
negotiates a speed with its peer. 

**6\.**Set the expected bandwidth for the Ethernet
interface.

bandwidth bandwidth-value

By default, the expected bandwidth (in
kbps) is the interface baud rate divided by 1000\.

**7\.**Bring up the Ethernet interface.

undo shutdown

By default, Ethernet interfaces are in up
state. 

### Configuring basic settings of an Ethernet subinterface

#### About this task

By default, a Layer 3 Ethernet subinterface
processes packets for only the VLAN whose ID is the same as the subinterface
number.

#### Restrictions and guidelines for Ethernet subinterface basic settings

·     The shutdown, port up-mode, and loopback commands are mutually exclusive. 

·     The shutdown
command cannot be configured on an Ethernet interface in a loopback test. 

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Create an Ethernet subinterface.

interface interface-type interface-number.subnumber

**3\.**Set the description for the Ethernet
subinterface.

description text

The default setting is interface-name Interface. For example, Ten-GigabitEthernet1/0/1.1 Interface.

**4\.**Set the expected bandwidth for the Ethernet
subinterface. 

bandwidth bandwidth-value

By default, the expected bandwidth (in
kbps) is the interface baud rate divided by 1000\.

**5\.**Bring up the Ethernet subinterface.

undo shutdown

By default, Ethernet subinterfaces are in
up state.

### Configuring the link mode of an Ethernet interface

#### About this task

Interfaces on the device can operate either
as Layer 2 or Layer 3 Ethernet interfaces. You can use commands to set the link
mode to bridge or route. 

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter Ethernet interface view.

interface interface-type interface-number

**3\.**Configure the link mode of the Ethernet
interface.

port link-mode { bridge \| route }

By default, all Ethernet interfaces on
the device operate in bridge mode.

 

| CAUTION | CAUTION:  After you change the link mode of an Ethernet interface, all commands (except the description, duplex, jumboframe enable, speed, shutdown, and combo enable commands) on the Ethernet interface are restored to their defaults in the new link mode. |
| --- | --- |

‌

### Configuring jumbo frame support

#### About this task

Jumbo frames are frames larger than 1536
bytes and are typically received by an Ethernet interface during high-throughput
data exchanges, such as file transfers. 

The Ethernet interface processes jumbo
frames in the following ways:

·     When the Ethernet interface is configured to
deny jumbo frames (by using the undo jumboframe enable command), the Ethernet interface discards jumbo frames.

·     When the Ethernet interface is configured with jumbo
frame support, the Ethernet interface performs the following operations:

¡     Processes
jumbo frames within the specified length. 

¡     Discards
jumbo frames that exceed the specified length. 

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter Ethernet interface view.

interface interface-type interface-number

**3\.**Configure jumbo frame support.

jumboframe enable \[ size ]

By default, the device allows jumbo
frames within 10000 bytes to pass through.

If you set the size argument multiple times, the most recent configuration takes
effect.

### Configuring physical state change suppression on an Ethernet interface

#### About this task

The physical link state of an Ethernet
interface is either up or down. Each time the physical link of an interface comes
up or goes down, the interface immediately reports the change to the CPU. The
CPU then performs the following operations:

·     Notifies the upper-layer protocol modules (such
as routing and forwarding modules) of the change for guiding packet forwarding.

·     Automatically generates traps and logs to inform
users to take the correct actions.

To prevent frequent physical link flapping
from affecting system performance, configure physical state change suppression.
You can configure this feature to suppress only link-down events, only link-up
events, or both. If an event of the specified type still exists when the suppression
interval expires, the system reports the event to the CPU.

#### Restrictions and guidelines

Do not enable this feature on an interface
that has RRPP, spanning tree protocols, or Smart Link enabled. 

You can configure different suppression
intervals for link-up and link-down events.

If you execute the link-delay command multiple times on an interface, the following rules apply:

·     You can configure the suppression intervals for
link-up and link-down events separately.

·     If you configure the suppression interval
multiple times for link-up or link-down events, the most recent configuration
takes effect. 

The link-delay, dampening, and port link-flap protect enable commands are mutually exclusive on an Ethernet interface. 

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter Ethernet interface view.

interface interface-type interface-number

**3\.**Configure physical state change suppression.

link-delay { down \| up } \[ msec ] delay-time

By default, each time the physical link
of an interface goes up or comes down, the interface immediately reports the
change to the CPU. 

### Configuring dampening on an Ethernet interface

#### About this task

The interface dampening feature uses an
exponential decay mechanism to prevent excessive interface flapping events from
adversely affecting routing protocols and routing tables in the network. Suppressing
interface state change events protects the system resources.

If an interface is not dampened, its state
changes are reported. For each state change, the system also generates an SNMP trap
and log message.

After a flapping interface is dampened, it
does not report its state changes to the CPU. For state change events, the
interface only generates SNMP trap and log messages.

#### Parameters

·     Penalty—The interface has an initial penalty of 0\. When the interface
flaps, the penalty increases by 1000 for each down event until the ceiling is
reached. It does not increase for up events. When the interface stops flapping,
the penalty decreases by half each time the half-life timer expires until the
penalty drops to the reuse threshold.

·     Ceiling—The penalty stops increasing when it reaches the ceiling.

·     Suppress-limit—The accumulated penalty that triggers the device to dampen the
interface. In dampened state, the interface does not report its state changes
to the CPU. For state change events, the interface only generates SNMP traps and
log messages.

·     Reuse-limit—When the accumulated penalty decreases to this reuse threshold, the
interface is not dampened. Interface state changes are reported to the upper
layers. For each state change, the system also generates an SNMP trap and log
message.

·     Decay—The amount of time (in seconds) after which a penalty is decreased.

·     Max-suppress-time—The maximum amount of time the interface can be dampened. If the
penalty is still higher than the reuse threshold when this timer expires, the
penalty stops increasing for down events. The penalty starts to decrease until
it drops below the reuse threshold.

When configuring thedampening command, follow these rules to set the values mentioned above:

·     The ceiling is equal to 2(Max-suppress-time/Decay)  × reuse-limit. It is not
user configurable.

·     The configured suppress limit is lower than or
equal to the ceiling.

·     The ceiling is lower than or equal to the
maximum suppress limit supported.

[Figure 1](#_Ref372020119)
shows the change rule of the penalty value. The lines t0
and t2 indicate the start time and end time of the
suppression, respectively. The period from t0 to t2 indicates the suppression period, t0 to t1 indicates the
max-suppress-time, and t1 to t2 indicates the complete decay period.

Figure 1 Change rule of the penalty value

![](https://resource.h3c.com/en/202407/12/20240712_11704313_x_Img_x_png_2_2215971_294551_0.png)

‌

#### Restrictions and guidelines

·     The dampening, link-delay, and port link-flap protect enable commands are mutually exclusive on an interface. 

·     The dampening command
does not take effect on the administratively down events. When you execute the shutdown command, the penalty restores to 0, and the interface reports the
down event to the upper-layer protocols.

·     Do not enable the dampening feature on an
interface with RRPP, MSTP, or Smart Link enabled. 

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter Ethernet interface view.

interface interface-type
interface-number

**3\.**Enable dampening on the interface.

dampening \[ half-life reuse
suppress max-suppress-time ]

By default, interface dampening is
disabled on Ethernet interfaces.

### Enabling link flapping protection on an interface

#### About this task

Link flapping on an interface changes
network topology and increases the system overhead. For example, in an active/standby
link scenario, when interface status on the active link changes between UP and DOWN, traffic switches
between active and standby links. To solve this problem, configure this feature
on the interface.

With this feature enabled on an interface,
when the interface goes down, the system enables link flapping detection. During
the link flapping detection interval, if the number of detected flaps reaches
or exceeds the link flapping detection threshold, the system shuts down the
interface.

#### Restrictions and guidelines

This feature takes effect only if it is
configured in both the system view and interface view.

When you configure this feature on an
interface, how link flapping protection is triggered depends on the parameters
specified:

·     If you specify level-1 link flapping protection
parameters but do not specify level-2 parameters, link flapping protection will
be triggered and the interface will be shut down when the level-1 link flapping
protection conditions are met. 

·     If you specify both level-1 and level-2 link
flapping protection parameters, link flapping protection will be triggered and
the interface will be shut down when either of level-1 and level-2 link
flapping protection conditions are met.

The dampening, link-delay, and port link-flap protect enable commands are mutually exclusive on an Ethernet interface.

To bring up an interface that has been shut
down by link flapping protection, execute the undo shutdown command.

In the display interface command output, the Link-Flap DOWN value
of the Current state field indicates that the
interface has been shut down by link flapping protection.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enable link flapping protection globally.

link-flap protect enable

By default, link flapping protection is disabled
globally.

**3\.**Enter Ethernet interface view.

interface interface-type interface-number

**4\.**Enable link flapping protection on the
Ethernet interface.

port link-flap protect enable \[ interval interval \| threshold threshold ] \* \[
second-interval second-interval
second-threshold second-threshold ]

By default, link flapping protection is disabled
on an Ethernet interface.

### Configuring storm suppression

#### About this task

The storm suppression feature ensures that the
size of a particular type of traffic (broadcast, multicast, or unknown unicast
traffic) does not exceed the threshold on an interface. When the broadcast,
multicast, or unknown unicast traffic on the interface exceeds this threshold,
the system discards packets until the traffic drops below this threshold.

Both storm suppression and storm control
can suppress storms on an interface. Storm suppression uses the chip to suppress
traffic. Storm suppression has less impact on the device performance than storm
control, which uses software to suppress traffic.

#### Restrictions and guidelines

·     For the traffic suppression result to be determined,
do not configure storm control together with storm suppression for the same
type of traffic. For more information about storm control, see "[Configuring storm control on an Ethernet
interface](#_Ref482742207)."

·     When you configure the suppression threshold in
kbps, the actual suppression threshold might be different from the configured
one as follows:

¡     If
the configured value is smaller than 64, the value of 64 takes effect.

¡     If
the configured value is greater than 64 but not an integer multiple of 64, the
integer multiple of 64 that is greater than and closest to the configured value
takes effect.

For the suppression threshold that takes
effect, see the prompt on the device. 

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter Ethernet interface view.

interface interface-type interface-number

**3\.**Enable broadcast suppression and set the
broadcast suppression threshold.

broadcast-suppression { ratio \| pps max-pps \| kbps max-kbps }

By default, broadcast suppression is
disabled.

**4\.**Enable multicast suppression and set the
multicast suppression threshold.

multicast-suppression { ratio \| pps max-pps \| kbps max-kbps } 

By default, multicast suppression is
disabled.

**5\.**Enable unknown unicast suppression and set
the unknown unicast suppression threshold.

unicast-suppression { ratio \| pps max-pps \| kbps max-kbps }

By default, unknown unicast suppression
is disabled.

### Configuring generic flow control on an Ethernet interface

#### About this task

To avoid dropping packets on a link, you
can enable generic flow control at both ends of the link. When traffic
congestion occurs at the receiving end, the receiving end sends a flow control
(Pause) frame to ask the sending end to suspend sending packets. Generic flow
control includes the following types:

·     TxRx-mode generic flow
control—Enabled by using the flow-control command. With TxRx-mode generic flow control enabled, an interface
can both send and receive flow control frames:

¡     When
congestion occurs, the interface sends a flow control frame to its peer.

¡     When
the interface receives a flow control frame from its peer, it suspends sending
packets to its peer.

·     Rx-mode generic flow
control—Enabled by using the flow-control
receive enable command. With Rx-mode generic flow
control enabled, an interface can receive flow control frames, but it cannot
send flow control frames:

¡     When
congestion occurs, the interface cannot send flow control frames to its peer.

¡     When
the interface receives a flow control frame from its peer, it suspends sending
packets to its peer.

To handle unidirectional traffic congestion
on a link, configure the flow-control receive enable command at one end and the flow-control
command at the other end. To enable both ends of a link to handle traffic
congestion, configure the flow-control command at
both ends.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter Ethernet interface view.

interface interface-type interface-number

**3\.**Enable generic flow control.

¡     Enable TxRx-mode generic flow control.

flow-control

¡     Enable Rx-mode generic flow control.

flow-control receive enable

By default, generic flow control is
disabled on an Ethernet interface.

### Setting the statistics polling interval

#### About this task

By setting the statistics polling interval,
you can collect statistics of packets and analyze packets at the specified
interval. Based on the interface traffic statistics, you can take traffic
control measures promptly to avoid network congestion and service interruption.


·     When network congestion is detected, you can set
the statistics polling interval to be smaller than 300 seconds (30 seconds when
congestion deteriorates). Then, check traffic distribution on interfaces within
a short period of time. For data packets that cause congestion, take traffic
control measures. 

·     When the network bandwidth is sufficient and
services are operating normally, you can set the statistics polling interval to
be greater than 300 seconds. Once traffic parameter anomalies occur, modify the
statistics polling interval promptly so that you can observe the traffic
parameter trend in real time. 

To display the interface statistics
collected in the last statistics polling interval, use the display
interface command. To clear the interface
statistics, use the reset counters interface command.

#### Setting the statistics polling interval in Ethernet interface view

**1\.**Enter system view.

system-view

**2\.**Enter Ethernet interface view.

interface interface-type interface-number

**3\.**Set the statistics polling interval for the
Ethernet interface.

flow-interval interval

By default, the statistics polling
interval is 300 seconds. 

### Enabling loopback testing on an Ethernet interface

#### About this task

Perform this task to determine whether an
Ethernet link works correctly.

Loopback testing includes the following
types:

·     Internal loopback testing—Tests the device where the Ethernet interface resides. The Ethernet
interface sends outgoing packets back to the local device. If the device fails
to receive the packets, the device fails.

·     External loopback testing—Tests the inter-device link. The Ethernet interface sends incoming packets
back to the remote device. If the remote device fails to receive the packets, the
inter-device link fails. 

#### Restrictions and guidelines

·     After you enable this feature on an Ethernet
interface, the interface does not forward data traffic.

·     You cannot perform a loopback test on the
following Ethernet interfaces:

¡     Ethernet
interfaces manually brought down (displayed as in ADM
or Administratively DOWN state).

¡     Ethernet
interfaces with the port up-mode command
executed.

·     The speed, duplex, and shutdown commands
cannot be executed on an Ethernet interface in a loopback test. 

·     After you enable this feature on an Ethernet
interface, the Ethernet interface switches to full duplex mode. After you
disable this feature, the Ethernet interface restores to its duplex setting.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter Ethernet interface view.

interface interface-type interface-number

**3\.**Configure loopback testing on the Ethernet
interface.

¡     Enable
loopback testing on the Ethernet interface.

loopback{ external \| internal }

By default, loopback testing is disabled
on an Ethernet interface.

¡     Perform
a loopback test.

loopback-test{ external \| internal }

### Forcibly bringing up a fiber port

#### About this task

As shown in [Figure 2](#_Ref326828413), a
fiber port uses separate fibers for transmitting and receiving packets. The
physical state of the fiber port is up only when both transmit and receive
fibers are physically connected. If one of the fibers is disconnected, the fiber
port does not work.

To enable a fiber port to forward traffic
over a single link, you can use the port up-mode
command. This command forcibly brings up a fiber port, even when no fiber links
or transceiver modules are present for the fiber port. When one fiber link is present
and up, the fiber port can forward packets over the link unidirectionally.

Figure 2 Forcibly bring up a fiber port

![](https://resource.h3c.com/en/202407/12/20240712_11704314_x_Img_x_png_3_2215971_294551_0.png)

‌

#### Restrictions and guidelines

·     Copper ports do not support this feature. 

·     The port up-mode and shutdown commands are
mutually exclusive. 

·     A fiber port does not support this feature if
the port is shut down by a protocol or by using the shutdown command.

·     A fiber port does not support this feature if
the port joins an aggregation group.

·     A fiber port forcibly brought up stays
physically up whether or not a transceiver module or a fiber link is present
for the port. 

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter Ethernet interface view.

interface interface-type interface-number

**3\.**Forcibly bring up the fiber port.

port up-mode

By default, a fiber port is not forcibly
brought up, and the physical state of a fiber port depends on the physical
state of the fibers.

### Configuring interface alarm functions

#### About this task

With the interface alarm functions enabled,
when the number of error packets on an interface in normal state within the
specified interval exceeds the upper threshold, the interface generates an
upper threshold exceeding alarm and enters the alarm state. When the number of error
packets on an interface in the alarm state within the specified interval drops
below the lower threshold, the interface generates a recovery alarm and
restores to the normal state. 

#### Restrictions and guidelines

An interface that is shut down because of
error packet alarms cannot automatically recover. To bring up the interface,
execute the undo shutdown command
on the interface. 

#### Enabling interface alarm functions

**1\.**Enter system view.

system-view

**2\.**Enable alarm functions for the interface
monitoring module. 

snmp-agent trap enable ifmonitor \[ crc-error \| input-error \| input-usage \| output-error \| output-usage \| rx-pause \| sdh-b1-error \| sdh-b2-error \| sdh-error \| tx-pause ] \*

By default, all alarm functions are
enabled for interfaces. 

#### Configuring CRC error packet parameters

**1\.**Enter system view. 

system-view

**2\.**Configure global CRC error packet alarm parameters.


ifmonitor crc-error slot slot-number high-threshold high-value low-threshold low-value interval interval \[ shutdown ]

By default, the upper threshold is 1000,
the lower threshold is 100, and the statistics collection and comparison
interval is 10 seconds for CRC error packets.

**3\.**Enter Ethernet interface view. 

interface interface-type interface-number

**4\.**Configure CRC error packet alarm parameters
for the interface. 

port ifmonitor crc-error \[ ratio ] high-threshold high-value low-threshold low-value interval interval \[ shutdown ]

By default, an interface uses the global
CRC error packet alarm parameters. 

#### Configuring input error packet alarm parameters

**1\.**Enter system view.

system-view

**2\.**Configure global input error packet alarm
parameters.

ifmonitor input-error slot slot-number high-threshold high-value low-threshold low-value interval interval \[ shutdown ]

By default, the upper threshold is 1000,
the lower threshold is 100, and the statistics collection and comparison
interval is 10 seconds for input error packets.

**3\.**Enter Ethernet interface view. 

interface interface-type interface-number

**4\.**Configure input error packet alarm
parameters for the interface. 

port ifmonitor input-error high-threshold high-value low-threshold low-value interval interval \[ shutdown ]

By default, an interface uses the global
input error packet alarm parameters. 

#### Configuring output error packet alarm parameters

**1\.**Enter system view.

system-view

**2\.**Configure global output error packet alarm
parameters.

ifmonitor output-error slot slot-number high-threshold high-value low-threshold low-value interval interval \[ shutdown ]

By default, the upper threshold is 1000,
the lower threshold is 100, and the statistics collection and comparison
interval is 10 seconds for output error packets. 

**3\.**Enter Ethernet interface view. 

interface interface-type interface-number

**4\.**Configure output error packet alarm
parameters. 

port ifmonitor output-error high-threshold high-value low-threshold low-value interval interval \[ shutdown ]

By default, an interface uses the global
output error packet alarm parameters. 

#### Configuring input bandwidth usage alarm parameters

**1\.**Enter system view. 

system-view

**2\.**Configure global input bandwidth usage alarm
parameters.

ifmonitor input-usage slot slot-number high-threshold high-value low-threshold low-value

By default, the upper threshold is 90 and
the lower threshold is 80 for input bandwidth usage alarms.

#### Configuring output bandwidth usage alarm parameters

**1\.**Enter system view. 

system-view

**2\.**Configure global output bandwidth usage
alarm parameters.

ifmonitor output-usage slot slot-number high-threshold high-value low-threshold low-value

By default, the upper threshold is 90 and
the lower threshold is 80 for output bandwidth usage alarms.

#### Configuring received pause frame alarm parameters

**1\.**Enter system view. 

system-view

**2\.**Configure global received pause frame alarm
parameters.

ifmonitor rx-pause slot slot-number high-threshold high-value low-threshold low-value interval interval

By default, the upper threshold is 500,
the lower threshold is 100, and the statistics collection and comparison
interval is 10 seconds for received pause frames.

**3\.**Enter Ethernet interface view.

interface interface-type interface-number

**4\.**Configure received pause frame alarm
parameters. 

port ifmonitor rx-pause high-threshold high-value low-threshold low-value interval interval

By default, an interface uses the global received
pause frame alarm parameters.

#### Configuring sent pause frame alarm parameters

**1\.**Enter system view. 

system-view

**2\.**Configure global sent pause frame alarm
parameters.

ifmonitor tx-pause slot slot-number high-threshold high-value low-threshold low-value interval interval \[ shutdown ]

By default, the upper threshold is 500,
the lower threshold is 100, and the statistics collection and comparison
interval is 10 seconds for sent pause frames.

**3\.**Enter Ethernet interface view.

interface interface-type interface-number

**4\.**Configure sent pause frame alarm parameters.


port ifmonitor tx-pause high-threshold high-value low-threshold low-value interval interval \[ shutdown ]

By default, an interface uses the global
sent pause frame alarm parameters.

#### Configuring SDH error packet alarm parameters

**1\.**Enter system view. 

system-view

**2\.**Configure global SDH error packet alarm parameters.

ifmonitor sdh-error slot slot-number high-threshold high-value low-threshold low-value interval interval \[ shutdown ]

By default, the upper threshold is 1000,
the lower threshold is 100, and the statistics collection and comparison
interval is 10 seconds for SDH error packets.

**3\.**Enter Ethernet interface view.

interface interface-type interface-number

**4\.**Configure SDH error packet alarm parameters
for the interface.

port ifmonitor sdh-error high-threshold high-value low-threshold low-value interval interval \[ shutdown ]

By default, an interface uses the global
SDH error packet alarm parameters.

#### Configuring SDH-B1 error packet alarm parameters

**1\.**Enter system view. 

system-view

**2\.**Configure global SDH-B1 error packet alarm parameters.

ifmonitor sdh-b1-error slot slot-number high-threshold high-value low-threshold low-value interval interval \[ shutdown ]

By default, the upper threshold is 1000,
the lower threshold is 100, and the statistics collection and comparison
interval is 10 seconds for SDH-B1 error packets.

**3\.**Enter Ethernet interface view.

interface interface-type interface-number

**4\.**Configure SDH-B1 error packet alarm
parameters for the interface.

port ifmonitor sdh-b1-error high-threshold high-value low-threshold low-value interval interval \[ shutdown ]

By default, an interface uses the global
SDH-B1 error packet alarm parameters.

#### Configuring SDH-B2 error packet alarm parameters

**1\.**Enter system view. 

system-view

**2\.**Configure global SDH-B2 error packet alarm parameters.

ifmonitor sdh-b2-error slot slot-number high-threshold high-value low-threshold low-value interval interval \[ shutdown ]

By default, the upper threshold is 1000,
the lower threshold is 100, and the statistics collection and comparison
interval is 10 seconds for SDH-B2 error packets.

**3\.**Enter Ethernet interface view.

interface interface-type interface-number

**4\.**Configure SDH-B2 error packet alarm
parameters for the interface.

port ifmonitor sdh-b2-error high-threshold high-value low-threshold low-value interval interval \[ shutdown ]

By default, an interface uses the global
SDH-B2 error packet alarm parameters.

### Shutting down all physical interfaces

#### About this task

This feature shuts down all physical
interfaces except the management Ethernet interfaces on the device. Physical
interfaces shut down by using this command are in ADM state. 

#### Restrictions and guidelines

To shut down all physical interfaces or the
specified interface, execute the shutdown all-physical-interfaces command in system view or execute the shutdown command in interface view.

To bring up interfaces shut down by using
the shutdown
all-physical-interfaces command, execute its undo form in system view. To bring up an interface shut down by using
the shutdown command, execute its undo form in
interface view.

If you execute the shutdown
all-physical-interfaces command with the include
irf-physical-interface keyword multiple times,
this command shuts down all physical interfaces except the management Ethernet
interfaces on the device.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Shut down all physical interfaces except
management Ethernet interfaces.

shutdown all-physical-interfaces \[ include irf-physical-interface ]

By default, physical interfaces are up. 

### Restoring the default settings for an interface

#### Restrictions and guidelines

| CAUTION | CAUTION:  This feature might interrupt ongoing network services. Make sure you are fully aware of the impacts of this feature when you use it in a live network. |
| --- | --- |

‌

This feature might fail to restore the
default settings for some commands because of command dependencies or system
restrictions. You can use the display this command in interface view to check for these commands and perform
their undo forms or follow the command
reference to restore their default settings. If your restoration attempt still
fails, follow the error message to resolve the problem.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter Ethernet interface viewor Ethernet subinterface view.

interface interface-type { interface-number \|
interface-number.subnumber }

**3\.**Restore the default settings for the interface.

default

## Configuring a Layer 2 Ethernet interface

### Setting the MDIX mode of an Ethernet interface

| IMPORTANT | IMPORTANT:  Fiber ports do not support the MDIX mode setting. |
| --- | --- |

‌

#### About this task

A physical Ethernet interface has eight
pins, each of which plays a dedicated role. For example, pins 1 and 2 transmit
signals, and pins 3 and 6 receive signals. You can use both crossover and
straight-through Ethernet cables to connect copper Ethernet interfaces. To
accommodate these types of cables, a copper Ethernet interface can operate in
one of the following Medium Dependent Interface-Crossover (MDIX) modes:

·     MDIX mode—Pins 1 and 2 are receive pins and pins 3 and 6 are transmit pins.

·     MDI mode—Pins 1 and 2 are transmit pins and pins 3 and 6 are receive pins.

·     AutoMDIX mode—The interface negotiates pin roles with its peer.

 

|  | NOTE:  This feature does not take effect on pins 4, 5, 7, and 8 of physical Ethernet interfaces.  ·     Pins 4, 5, 7, and 8 of interfaces operating at 10 Mbps or 100 Mbps do not receive or transmit signals.  ·     Pins 4, 5, 7, and 8 of interfaces operating at 1000 Mbps or higher rates receive and transmit signals. |
| --- | --- |

‌

#### Restrictions and guidelines

To enable a copper Ethernet interface to
communicate with its peer, set the MDIX mode of the interface by following
these guidelines:

·     Typically, set the MDIX mode of the interface to
AutoMDIX. Set the MDIX mode of the interface to MDI or MDIX only when the
device cannot determine the cable type.

·     When a straight-through cable is used, configure
the interface to operate in an MDIX mode different than its peer.

·     When a crossover cable is used, perform one of
the following tasks:

¡     Configure
the interface to operate in the same MDIX mode as its peer.

¡     Configure
either end to operate in AutoMDIX mode.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter Ethernet interface view.

interface interface-type interface-number

**3\.**Set the MDIX mode of the Ethernet interface.

mdix-mode { automdix \| mdi \| mdix }

By default, a copper Ethernet interface
operates in auto mode to negotiate pin roles with its peer.

### Configuring storm control on an Ethernet interface

#### About this task

Storm control compares broadcast, multicast
and unknown unicast traffic regularly with their respective traffic thresholds
on an Ethernet interface. For each type of traffic, storm control provides a
lower threshold and an upper threshold.

Depending on your configuration, when a
particular type of traffic exceeds its upper threshold, the interface performs either
of the following operations:

·     Blocks this type of traffic
and forwards other types of traffic—Even though
the interface does not forward the blocked traffic, it still counts the
traffic. When the blocked traffic drops below the lower threshold, the interface
begins to forward the traffic.

·     Goes down automatically—The interface goes down automatically and stops forwarding any
traffic. When the blocked traffic drops below the lower threshold, the interface
does not automatically come up. To bring up the interface, use the undo shutdown command or disable the storm control feature.

You can configure an Ethernet interface to
output threshold event traps and log messages when monitored traffic meets one
of the following conditions:

·     Exceeds the upper threshold.

·     Drops below the lower threshold.

Both storm suppression and storm control
can suppress storms on an interface. Storm suppression uses the chip to suppress
traffic. Storm suppression has less impact on the device performance than storm
control, which uses software to suppress traffic. For more information about storm
suppression, see "[Configuring storm suppression](#_Ref480271731)."

Storm control uses a complete polling cycle
to collect traffic data, and analyzes the data in the next cycle. An interface
takes one to two polling intervals to take a storm control action.

#### Restrictions and guidelines

For the traffic suppression result to be determined,
do not configure storm control together with storm suppression for the same
type of traffic. 

#### Procedure

**1\.**Enter system view.

system-view

**2\.**(Optional.) Set the statistics polling
interval of the storm control module.

storm-constrain interval interval

The default setting is 10 seconds.

For network stability, use the default or
set a longer statistics polling interval.

**3\.**Enter Ethernet interface view.

interface interface-type interface-number

**4\.**Enable storm control, and set the lower and
upper thresholds for broadcast, multicast, or unknown unicast traffic.

storm-constrain { broadcast \| multicast \| unicast } { pps \| kbps \| ratio } upperlimit lowerlimit

By default, storm control is disabled.

**5\.**Set the control action to take when
monitored traffic exceeds the upper threshold.

storm-constrain control { block \| shutdown }

By default, storm control is disabled.

**6\.**Enable the Ethernet interface to output log
messages when it detects storm control threshold events.

storm-constrain enable log

By default, the Ethernet interface
outputs log messages when monitored traffic exceeds the upper threshold or
drops below the lower threshold from a value above the upper threshold.

**7\.**Enable the Ethernet interface to send storm
control threshold event traps.

storm-constrain enable trap

By default, the Ethernet interface sends
traps when monitored traffic exceeds the upper threshold or drops below the
lower threshold from the upper threshold from a value above the upper threshold.

### Testing the cable connection of an Ethernet interface

| IMPORTANT | IMPORTANT:  If the link of an Ethernet interface is up, testing its cable connection will cause the link to go down and then come up. |
| --- | --- |

‌

#### About this task

The virtual cable test (VCT) technology
uses time domain reflectometry (TDR) to detect the cable status. When pulse
signals are transmitted in a cable, some energy of the signals is reflected at
the end or a failure point on the cable. This phenomenon is called TDR. The VCT
algorithm measures the time spent in transmitting pulse signals over a cable,
reaching the failure point, and returning the pulse signals. The measured time
is converted into the distance.

As shown in [Figure 3](#_Ref101109009), GE
1/0/1 on Device A is connected to GE 1/0/1 on Device B through a cable. A
failure point exists on the cable. After VCT is configured on GE 1/0/1 on
Device A, the system will generate pulse signals. Some energy of the signals is
reflected at the failure point on the cable. Suppose the distance from Device A
to the failure point is L, the interval between sending signals and receiving
reflected signals is T, and the signal transmission speed on the cable is V.
Then, the distance between GE 1/0/1 on Device A and the failure point can be
calculated as follows: L\=(V×T)/2.

Figure 3 Schematic diagram for VCT

![](https://resource.h3c.com/en/202407/12/20240712_11704318_x_Img_x_png_7_2215971_294551_0.png)

‌

VCT detects the network cable failure type
and identifies the failure point, facilitating failure location on network
cables.

#### Restrictions and guidelines

Fiber ports do not support this feature.

#### Procedure

**1\.**Enter system view.

system-view 

**2\.**Enter Ethernet interface view.

interface interface-type interface-number 

**3\.**Perform a test for the cable connected to
the Ethernet interface.

virtual-cable-test

### Enabling bridging on an Ethernet interface

#### About this task

By default, the device drops packets whose outgoing
interface and incoming interface are the same.

To enable the device to forward such
packets rather than drop them, enable the bridging feature in Ethernet
interface view.

 

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter Ethernet interface view.

interface interface-type interface-number

**3\.**Enable bridging on the Ethernet interface.

port bridge enable

By default, bridging is disabled on an
Ethernet interface.

### Configuring Layer 3 forwarding on a Layer 2 Ethernet interface

#### About this task

By default, when a Layer 2 Ethernet
interface receives a Layer 3 unicast protocol packet, it delivers the packet to
the CPU. With this feature configured, a Layer 2 Ethernet interface floods such
a packet in the VLAN to which the packet belongs instead of delivering the
packet to the CPU.

On a network where a firewall is attached
to the device for transparent packet inspection, configure this feature on
Layer 2 Ethernet interfaces on the device. In this way, the device will forward
Layer 3 unicast protocol packets to the firewall for packet inspection and the
firewall forwards only packets that pass packet inspection back to the device
for further forwarding.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter Layer 2 Ethernet interface view.

interface interface-type interface-number

**3\.**Enable Layer 3 forwarding on the interface.

port ip-forwarding enable

By default, Layer 3 forwarding is enabled
on an interface.

## Configuring a Layer 3 Ethernet interface or subinterface

### Setting the MTU for an Ethernet interface or subinterface

#### About this task

The maximum transmission unit (MTU)
determines the maximum number of bytes in a single IP packet that can be sent.
The length of an IP packet refers to the number of bytes starting from the IP
header to the payload.  

When the IP layer receives an IP data
packet to be sent, the IP layer determines the local destination interface of
the packet and obtains the MTU of the interface. The IP layer compares the MTU
with the length of the data packet to be sent. If the length is greater than
the MTU, the IP layer fragments the packet. The length of a fragment can be
smaller than or equal to the MTU to ensure that big packets are not lost on the
network.  

As a best practice, use the default MTU.
When the packet length or the packet receiver changes, you can adjust the MTU
as needed. When configuring the MTU, follow these restrictions and guidelines:

·     If the configured MTU is small but the packet
length is large, the following events might occur:

¡     Packets
will be dropped when they are forwarded by hardware. 

¡     Packets
will be fragmented into too many fragments when packets are forwarded through
the CPUs, which affects normal data transmission. 

·     If the configured MTU is too large, the MTU will
exceed the receiving capabilities of the receiver or a device along the
transmission path. As a result, packets will be fragmented or even dropped,
which increases the network transmission load and affects data transmission. 

#### Restrictions and guidelines

The MTU of an Ethernet interface affects
the fragmentation and reassembly of IP packets on the interface. Typically, you
do not need to modify the MTU of an interface. 

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter interface view.

interface interface-type { interface-number \|
interface-number.subnumber }

**3\.**Set the MTU for the interface.

mtu size

The default setting is 1500 bytes.

## Display and maintenance commands for Ethernet interfaces

For more information about the display
diagnostic-information command, see device management
in Fundamentals Command Reference.

Execute display
commands in any view and reset commands in user
view. 

 

| Task | Command |
| --- | --- |
| Display interface traffic statistics. | display counters { inbound \| outbound } interface \[ interface-type \[ interface-number ] ] || Display traffic rate statistics of interfaces in up state over the last statistics polling interval. | display counters rate { inbound \| outbound } interface \[ interface-type \[ interface-number ] ] || Display the operating information for the IFMGR module. | display diagnostic-information ifmgr \[ key-info ] \[ filename ] || Display the Ethernet module statistics. | display ethernet statistics slot slot-number || Display the operational and status information of the specified interfaces. | display interface \[ interface-type \[ interface-number \| interface-number.subnumber ] ] \[ brief \[ description \| down ] ] || Display the status and packet statistics of interfaces. | display interface link-info \[ main ] || Display the operational and status information of interfaces except subinterfaces. | display interface \[ interface-type ] \[ brief \[ description \| down ] ] main || Display information about link flapping protection on interfaces. | display link-flap protection \[ interface interface-type \[ interface-number ] ] || Display the physical link state change statistics of interfaces. | display link-state-change statistics interface \[ interface-type \[ interface-number ] ] || Display information about storm control on the specified interfaces. | display storm-constrain \[ broadcast \| multicast \| unicast ] \[ interface interface-type interface-number ] || Clear interface statistics. | reset counters interface \[ interface-type \[ interface-number \| interface-number.subnumber ] ] || Clear the Ethernet module statistics. | reset ethernet statistics \[ slot slot-number ] || Clear the physical link state change statistics of interfaces. | reset link-state-change statistics interface \[ interface-type \[ interface-number ] ] |













‌

 

