
# Configuring Ethernet OAM

## About Ethernet OAM

Ethernet Operation, Administration, and
Maintenance (OAM) is a tool that monitors Layer 2 link status and addresses
common link-related issues on the "last mile." Ethernet OAM improves
Ethernet management and maintainability. You can use it to monitor the status
of the point-to-point link between two directly connected devices. 

### Major functions of Ethernet OAM

Ethernet OAM provides the following
functions:

·     Link performance monitoring—Monitors the performance indices of a link, including packet loss,
delay, and jitter, and collects traffic statistics of various types.

·     Fault detection and alarm—Checks the connectivity of a link by sending OAM protocol data
units (OAMPDUs) and reports to the network administrators when a link error
occurs.

·     Remote loopback—Checks link quality and locates link errors by looping back OAMPDUs.

### Ethernet OAMPDUs

Ethernet OAM operates on the data link
layer. Ethernet OAM reports the link status by periodically exchanging OAMPDUs
between devices, so that the administrator can effectively manage the network. 

Ethernet OAMPDUs include the following
types shown in [Table 1](#_Ref324594725).

Table 1 Functions of different types of
OAMPDUs

| OAMPDU type | Function |
| --- | --- |
| Information OAMPDU | Used for transmitting state information of an Ethernet OAM entity, including the information about the local device and remote devices, and customized information, to the remote Ethernet OAM entity, and maintaining OAM connections. || Event Notification OAMPDU | Used by link monitoring to notify the remote OAM entity when it detects problems on the link in between. || Loopback Control OAMPDU | Used for remote loopback control. By inserting the information used to enable/disable loopback to a loopback control OAMPDU, you can enable/disable loopback on a remote OAM entity. |



 

|  | NOTE:  Throughout this document, an Ethernet OAM-enabled port is called an Ethernet OAM entity or an OAM entity. |
| --- | --- |

 

### How Ethernet OAM works

This section describes the working
procedures of Ethernet OAM. 

#### Ethernet OAM connection establishment

OAM connection establishment is also known
as the Discovery phase, where an Ethernet OAM entity discovers the remote OAM entity
to establish a session. 

In this phase, two connected OAM entities exchange
Information OAMPDUs to advertise their OAM configuration and capabilities to
each other for a comparison. If their Loopback, link detection, and link event settings
match, the OAM entities establish an OAM connection. 

An OAM entity operates in active mode or passive mode. OAM entities
in active mode initiate OAM connections, and OAM entities in passive mode wait and
respond to the OAM connection requests. To set up an OAM connection between two
OAM entities, you must set at least one entity to operate in active mode.

[Table 2](#_Ref145130463) shows the actions that a device can perform in different modes. 

Table 2 Active Ethernet OAM mode and passive
Ethernet OAM mode

| Item | Active Ethernet OAM mode | Passive Ethernet OAM mode |
| --- | --- | --- |
| Initiating OAM Discovery | Available | Unavailable || Responding to OAM Discovery | Available | Available || Transmitting Information OAMPDUs | Available | Available || Transmitting Event Notification OAMPDUs | Available | Available || Transmitting Information OAMPDUs without any TLV | Available | Available || Transmitting Loopback Control OAMPDUs | Available | Unavailable || Responding to Loopback Control OAMPDUs | Available | Available |







 

After an Ethernet OAM connection is
established, the Ethernet OAM entities exchange Information OAMPDUs at the handshake
packet transmission interval to detect the availability of the Ethernet OAM
connection. If an Ethernet OAM entity receives no Information OAMPDU within the
Ethernet OAM connection timeout time, the Ethernet OAM connection is considered
disconnected. 

#### Link monitoring

Error detection in an Ethernet is
difficult, especially when the physical connection in the network is not disconnected,
but network performance is degrading gradually. 

Link monitoring detects link faults in
various environments. Ethernet OAM entities monitor link status by exchanging
Event Notification OAMPDUs. When detecting one of the link error events listed
in [Table 3](#_Ref164679354), an OAM entity sends an Event Notification OAMPDU to its peer OAM
entity. The network administrator can keep track of
network status changes by retrieving the log. 

Table 3 Ethernet OAM link error events

| Ethernet OAM link events | Description |
| --- | --- |
| Errored symbol event | An errored symbol event occurs when the number of detected symbol errors in the detection window (specified number of received symbols) exceeds the predefined threshold. || Errored frame event | An errored frame event occurs when the number of detected error frames in the detection window (specified detection interval) exceeds the predefined threshold. || Errored frame period event | An errored frame period event occurs when the number of frame errors in the detection window (specified number of received frames) exceeds the predefined threshold. || Errored frame seconds event | An errored frame seconds event occurs when the number of errored frame seconds (the second in which an errored frame appears is called an errored frame second) detected on a port in the detection window (specified detection interval) reaches the predefined threshold. |




 

#### Remote fault detection

Information OAMPDUs are exchanged
periodically among Ethernet OAM entities across established OAM connections. When
traffic is interrupted due to device failure or unavailability, the Ethernet
OAM entity at the faulty end sends error information to its peer. The Ethernet
OAM entity uses the flag field in Information OAMPDUs to indicate the error
information (any critical link event type as shown in [Table 4](#_Ref200252285)). You can
use the log information to track ongoing link status and troubleshoot problems
promptly. 

Table 4 Critical link events

| Type | Description | OAMPDU transmission frequencies |
| --- | --- | --- |
| Link Fault | Peer link signal is lost. | Once per second. || Dying Gasp | An unexpected fault, such as power failure, occurred. | Non-stop. || Critical Event | An undetermined critical event happened. | Non-stop. |



 

#### Remote loopback

Remote loopback is available only after the
Ethernet OAM connection is established. With remote loopback enabled, the Ethernet
OAM entity in active mode sends non-OAMPDUs to its peer. After receiving these frames,
the peer does not forward them according to their destination addresses. Instead,
it returns them to the sender along the original path.

Remote loopback enables you to check the
link status and locate link failures. Performing remote loopback periodically
helps to detect network faults promptly. Furthermore, performing remote loopback
by network segments helps to locate network faults.

### Protocols and standards

IEEE 802.3ah, Carrier
Sense Multiple Access with Collision Detection (CSMA/CD) Access Method and
Physical Layer Specifications

## Restrictions and guidelines: Ethernet OAM configuration

The device's support for sending and receiving
Information OAMPDUs carrying critical link events is as follows:

·     Can receive Information OAMPDUs carrying the
critical link events listed in [Table 4](#_Ref200252285).

·     Can send Information OAMPDUs carrying Link Fault
events. 

·     Can send Information OAMPDUs carrying Dying Gasp
events when the device is rebooted or relevant ports are manually shut down. Physical
IRF ports, however, are unable to send this type of OAMPDUs.

·     Cannot send Information OAMPDUs carrying
Critical Events. 

## Ethernet OAM tasks at a glance

To configure Ethernet OAM, perform the
following tasks:

**1\.**[Configuring basic Ethernet OAM functions](#_Ref145149510)

**2\.**(Optional.) [Configuring the Ethernet OAM connection
detection timers](#_Ref235328008)

**3\.**(Optional.) Configuring link event detection

¡     [Configuring errored symbol event detection](#_Ref198350878)

¡     [Configuring errored frame event detection](#_Ref198350881)

¡     [Configuring errored frame period event
detection](#_Ref198350882)

¡     [Configuring errored frame seconds event
detection](#_Ref198350883)

**4\.**(Optional.) [Configuring the action a port takes after
it receives an Ethernet OAM event from the remote end](#_Ref312337527)

**5\.**(Optional.) Configuring Ethernet OAM remote loopback

¡     [Enabling Ethernet OAM remote loopback for a
port](#_Ref474507792)

¡     [Rejecting the Ethernet OAM remote loopback
request from a remote port](#_Ref278978888)

## Configuring basic Ethernet OAM functions

#### About this task

To set up an Ethernet OAM connection between
two Ethernet OAM entities, you must set at least one entity to operate in
active mode. An Ethernet OAM entity can initiate OAM connection only in active
mode.

#### Restrictions and guidelines

To change the Ethernet OAM mode on an
Ethernet OAM-enabled port, first disable Ethernet OAM on the port. 

#### Procedure

**1\.**Enter system view.

System-view

**2\.**Enter Layer 2/Layer 3 Ethernet port view.

interface interface-type
interface-number

**3\.**Set the Ethernet OAM mode.

oam mode { active \| passive }

The default is active Ethernet OAM mode.

**4\.**Enable Ethernet OAM.

oam enable

Ethernet OAM is disabled by default.

## Configuring the Ethernet OAM connection detection timers

### About the Ethernet OAM connection detection timers

After an Ethernet OAM connection is established,
the Ethernet OAM entities exchange Information OAMPDUs at the handshake packet
transmission interval to detect the availability of the Ethernet OAM
connection. If an Ethernet OAM entity receives no Information OAMPDU within the
Ethernet OAM connection timeout time, the Ethernet OAM connection is considered
disconnected.

By adjusting the handshake packet
transmission interval and the connection timeout timer, you can change the
detection time resolution for Ethernet OAM connections. 

### Restrictions and guidelines for configuring Ethernet OAM connection detection timers

When you configure Ethernet OAM, follow
these restrictions and guidelines:

·     You can configure this command in system view or
port view. The configuration in system view takes effect on all ports, and the
configuration in port view takes effect on the specified port. For a port, the
configuration in port view takes precedence.

·     After the timeout timer of an Ethernet OAM
connection expires, the local OAM entity ages out and terminates its connection
with the peer OAM entity. To keep the Ethernet OAM connections stable, set the
connection timeout timer to be at least five times the handshake packet
transmission interval. 

### Configuring the Ethernet OAM connection detection timers globally

**1\.**Enter system view.

System-view

**2\.**Configure the Ethernet OAM handshake packet
transmission interval.

oam global timer hello interval

The default is 1000 milliseconds.

**3\.**Configure the Ethernet OAM connection
timeout timer.

oam global timer keepalive interval

The default is 5000 milliseconds.

### Configuring the Ethernet OAM connection detection timers on a port

**1\.**Enter system view.

System-view

**2\.**Enter Layer 2/Layer 3 Ethernet port view.

interface interface-type
interface-number

**3\.**Configure the Ethernet OAM handshake packet
transmission interval.

oam timer hello interval

By default, an interface uses the value
configured globally.

**4\.**Configure the Ethernet OAM connection
timeout timer.

oam timer keepalive interval

By default, an interface uses the value
configured globally.

## Configuring errored symbol event detection

### Restrictions and guidelines for configuring errored symbol event detection

You can configure this function in system
view or port view. The configuration in system view takes effect on all ports,
and the configuration in port view takes effect on the specified port. For a
port, the configuration in port view takes precedence.

### Configuring errored symbol event detection globally

**1\.**Enter system view.

system-view

**2\.**Configure the errored symbol event detection
window.

oam global errored-symbol-period window window-value

By default, the errored symbol event
detection window is 100000000\.

**3\.**Configure the errored symbol event
triggering threshold.

oam global errored-symbol-period threshold threshold-value

By default, the errored symbol event
triggering threshold is 1\.

### Configuring errored symbol event detection on a port

**1\.**Enter system view.

system-view

**2\.**Enter Layer 2/Layer 3 Ethernet port view.

interface interface-type
interface-number

**3\.**Configure the errored symbol event detection
window.

oam errored-symbol-period window window-value

By default, an interface uses the value
configured globally.

**4\.**Configure the errored symbol event
triggering threshold.

oam errored-symbol-period threshold threshold-value

By default, an interface uses the value
configured globally.

## Configuring errored frame event detection

### Restrictions and guidelines for configuring errored frame event detection

You can configure this function in system
view or port view. The configuration in system view takes effect on all ports,
and the configuration in port view takes effect on the specified port. For a
port, the configuration in port view takes precedence.

### Configuring errored frame event detection globally

**1\.**Enter system view.

system-view

**2\.**Configure the errored frame event detection
window.

oam global errored-frame window window-value

By default, the errored frame event
detection window is 1000 milliseconds.

**3\.**Configure the errored frame event triggering
threshold.

oam global errored-frame threshold threshold-value

By default, the errored frame event
triggering threshold is 1\.

### Configuring errored frame event detection on a port

**1\.**Enter system view.

system-view

**2\.**Enter Layer 2/Layer 3 Ethernet port view.

interface interface-type
interface-number

**3\.**Configure the errored frame event detection
window.

oam errored-frame window window-value

By default, an interface uses the value
configured globally.

**4\.**Configure the errored frame event triggering
threshold.

oam errored-frame threshold threshold-value

By default, an interface uses the value
configured globally.

## Configuring errored frame period event detection

### Restrictions and guidelines for configuring errored frame period event detection

You can configure this function in system
view or port view. The configuration in system view takes effect on all ports,
and the configuration in port view takes effect on the specified port. For a
port, the configuration in port view takes precedence.

### Configuring errored frame period event detection globally

**1\.**Enter system view.

system-view

**2\.**Configure the errored frame period event
detection window.

oam global errored-frame-period window window-value

By default, the errored frame period
event detection window is 10000000\.

**3\.**Configure the errored frame period event
triggering threshold.

oam global errored-frame-period threshold threshold-value

By default, the errored frame period
event triggering threshold is 1\.

### Configuring errored frame period event detection on a port

**1\.**Enter system view.

system-view

**2\.**Enter Layer 2/Layer 3 Ethernet port view.

interface interface-type
interface-number

**3\.**Configure the errored frame period event
detection window.

oam errored-frame-period window window-value

By default, an interface uses the value
configured globally.

**4\.**Configure the errored frame period event
triggering threshold.

oam errored-frame-period threshold threshold-value

By default, an interface uses the value
configured globally.

## Configuring errored frame seconds event detection

### Restrictions and guidelines for configuring errored frame seconds event detection

·     You can configure this function in system view
or port view. The configuration in system view takes effect on all ports, and
the configuration in port view takes effect on the specified port. For a port,
the configuration in port view takes precedence.

·     Make sure the errored frame seconds triggering
threshold is less than the errored frame seconds detection window. Otherwise,
no errored frame seconds event can be generated.

### Configuring errored frame seconds event detection globally

**1\.**Enter system view.

system-view

**2\.**Configure the errored frame seconds event
detection window.

oam global errored-frame-seconds window window-value

By default, the errored frame seconds
event detection window is 60000 milliseconds.

**3\.**Configure the errored frame seconds event
triggering threshold.

oam global errored-frame-seconds threshold threshold-value

By default, the errored frame seconds
event triggering threshold is 1\.

### Configuring errored frame seconds event detection on a port

**1\.**Enter system view.

system-view

**2\.**Enter Layer 2/Layer 3 Ethernet port view.

interface interface-type
interface-number

**3\.**Configure the errored frame seconds event
detection window.

oam errored-frame-seconds window window-value

By default, an interface uses the value
configured globally.

**4\.**Configure the errored frame seconds event
triggering threshold.

oam errored-frame-seconds threshold threshold-value

By default, an interface uses the value
configured globally.

## Configuring the action a port takes after it receives an Ethernet OAM event from the remote end

#### About this task

This feature enables a port to log events and
automatically terminate the OAM connection and set the link state to down.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter Layer 2/Layer 3 Ethernet port view.

interface interface-type
interface-number

**3\.**Configure the action the port takes after it
receives an Ethernet OAM event from the remote end.

oam remote-failure { connection-expired \| critical-event \| dying-gasp \| link-fault } action error-link-down

By default, the port only logs the
Ethernet OAM event it receives from the remote end.

## Enabling Ethernet OAM remote loopback for a port

| CAUTION | CAUTION:  Use this feature with caution, because enabling Ethernet OAM remote loopback impacts other services. |
| --- | --- |

 

### About Ethernet OAM remote loopback

When you enable Ethernet OAM remote loopback
on a port, the port sends Loopback Control OAMPDUs to a remote port. After
receiving the Loopback Control OAMPDUs, the remote port enters the loopback
state. The remote port then returns any packets sent from the local port except
OAMPDUs. By observing how many of these packets return, you can calculate the
packet loss ratio on the link and evaluate the link performance. 

### Restrictions and guidelines for enabling Ethernet OAM remote loopback

·     Ethernet OAM remote loopback is available only
after the Ethernet OAM connection is established. It can be performed only by Ethernet
OAM entities operating in active Ethernet OAM mode.

·     Remote loopback is available only on full-duplex
links that support remote loopback at both ends. 

·     Ethernet OAM remote loopback must be supported
by both the remote port and the sending port.

·     Enabling Ethernet OAM remote loopback interrupts
data communications. After Ethernet OAM remote loopback is disabled, all the
ports involved will go down and then come up. Ethernet OAM remote loopback can
be disabled by any of the following events: 

¡     Disabling
Ethernet OAM. 

¡     Disabling
Ethernet OAM remote loopback. 

¡     Timeout
of the Ethernet OAM connection. 

·     Enabling internal loopback test on a port in
remote loopback test can terminate the remote loopback test. For more
information about loopback test, see Layer 2—LAN
Switching Configuration Guide.

·     You can enable Ethernet OAM remote loopback on a
specific port in user view, system view, or Ethernet port view. The
configuration effects are the same.

### Enabling Ethernet OAM remote loopbackfor a port in system view

**1\.**(Optional.) Enter system view.

system-view

You can also perform this task in user
view.

**2\.**Enable Ethernet OAM remote loopback for a
port.

oam remote-loopback start interface interface-type
interface-number

By default, Ethernet OAM remote loopback
is disabled.

### Enabling Ethernet OAM remote loopback for a port in interface view

**1\.**Enter system view.

system-view

**2\.**Enter Layer 2/Layer 3 Ethernet port view.

interface interface-type interface-number

**3\.**Enable Ethernet OAM remote loopback on the
port.

oam remote-loopback start

By default, Ethernet OAM remote loopback
is disabled.

## Rejecting the Ethernet OAM remote loopback request from a remote port

#### About this task

The Ethernet OAM remote loopback feature impacts
other services. To solve this problem, you can disable a port from being controlled
by the Loopback Control OAMPDUs sent by a remote port. The local port then rejects
the Ethernet OAM remote loopback request from the remote port. 

#### Restrictions and guidelines

This feature does not affect the ongoing
remote loopback test on the port. It takes effect when the next remote loopback
starts on the port.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter Layer 2/Layer 3 Ethernet port view.

interface interface-type interface-number

**3\.**Reject the Ethernet OAM remote loopback
request from a remote port.

oam remote-loopback reject-request

By default, a port does not reject the
Ethernet OAM remote loopback request from a remote port. 

## Display and maintenance commands for Ethernet OAM

Execute display
commands in any view and reset commands in user
view:

 

| Task | Command |
| --- | --- |
| Display information about an Ethernet OAM connection. | display oam { local \| remote } \[ interface interface-type interface-number ] || Display Ethernet OAM configuration. | display oam configuration \[ interface interface-type interface-number ] || Display the statistics on critical events after an Ethernet OAM connection is established. | display oam critical-event \[ interface interface-type interface-number ] || Display the statistics on Ethernet OAM link error events after an Ethernet OAM connection is established. | display oam link-event { local \| remote } \[ interface interface-type interface-number ] || Clear statistics on Ethernet OAM packets and Ethernet OAM link error events. | reset oam \[ interface interface-type interface-number ] |





 

## Ethernet OAM configuration examples

### Example: Configuring Ethernet OAM

#### Network configuration

On the network shown in [Figure 1](#_Ref234725630),
perform the following operations:

·     Enable Ethernet OAM on Device A and Device B to auto-detect
link errors between the two devices

·     Determine the performance of the link between
Device A and Device B by collecting statistics about the error frames received
by Device A

Figure 1 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705552_x_Img_x_png_1_2216094_294551_0.png)

‌

#### Procedure

**1\.**Configure Device A:

\# Configure Ten-GigabitEthernet 1/0/1 to operate in active
Ethernet OAM mode, and enable Ethernet OAM for it.

 

\<DeviceA\> system-view

\[DeviceA] interface ten-gigabitethernet 1/0/1

\[DeviceA-Ten-GigabitEthernet1/0/1] oam mode active

\[DeviceA-Ten-GigabitEthernet1/0/1] oam enable

\# Set the errored frame event detection window
to 20000 milliseconds, and set the errored frame event triggering threshold to
10\. 

\[DeviceA-Ten-GigabitEthernet1/0/1] oam errored-frame
window 200

\[DeviceA-Ten-GigabitEthernet1/0/1] oam errored-frame threshold
10

\[DeviceA-Ten-GigabitEthernet1/0/1] quit

**2\.**Configure Device B:

\# Configure Ten-GigabitEthernet 1/0/1 to operate in passive
Ethernet OAM mode (the default), and enable Ethernet OAM for it. 

\<DeviceB\> system-view

\[DeviceB] interface ten-gigabitethernet 1/0/1

\[DeviceB-Ten-GigabitEthernet1/0/1] oam mode passive

\[DeviceB-Ten-GigabitEthernet1/0/1] oam enable

\[DeviceB-Ten-GigabitEthernet1/0/1] quit

#### Verifying the configuration

Use the display oam
critical-event command to display the statistics
of Ethernet OAM critical link events. For example:

\# Display the statistics of Ethernet OAM
critical link events on all the ports of Device A.

\[DeviceA] display oam
critical-event

-----------\[Ten-GigabitEthernet1/0/1] \-----------

 Local link status   : UP

 Event statistics

   Link fault        :
Not occurred

   Dying gasp        :
Not occurred

   Critical event    :
Not occurred

The output shows that no critical link
event occurred on the link between Device A and Device B. 

Use the display oam
link-event command to display the statistics of
Ethernet OAM link events. For example:

\# Display Ethernet OAM link event
statistics of the local end of Device A.

\[DeviceA] display oam
link-event local

\------------ \[Ten-GigabitEthernet1/0/1] \-----------

 Link status: UP

 OAM local errored frame event

   Event time stamp        : 5789
x 100 milliseconds

   Errored frame window    : 200
x 100 milliseconds

   Errored frame threshold : 10
error frames

   Errored frame           : 13
error frames

   Error running total     : 350 error frames

   Event running
total     : 17 events

The output shows the following:

¡     350
errors occurred after Ethernet OAM is enabled on Device A.

¡     17
errors were caused by error frames. 

¡     The
link is unstable. 

 

