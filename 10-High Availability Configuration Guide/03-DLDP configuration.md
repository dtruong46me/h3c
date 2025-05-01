
# Configuring DLDP

## About DLDP

Physical layer
detection mechanisms, such as auto-negotiation, can detect physical signals and
faults. However, they cannot detect communication failures for unidirectional
links where the physical layer is in connected state. 

As a data link layer protocol, the H3C-proprietary
Device Link Detection Protocol (DLDP) can do the following:

·     Monitor status of the fiber link or twisted-pair
link at the link layer.

·     Cooperate with physical layer protocols to detect
whether the link is correctly connected and whether the two ends of the link can
exchange packets correctly. 

When DLDP detects unidirectional links, it
can automatically shut down the faulty port to avoid network problems.
Alternatively, a user can manually shut down the faulty port. 

### Application scenario

A link becomes unidirectional when only one
end of the link can receive packets from the other end. 

Unidirectional fiber links occur in the
following cases:

·     Fibers are cross-connected.

·     A fiber is not connected at one end or one fiber
of a fiber pair is broken.

[Figure 1](#_Ref331690573) shows a correct fiber connection and two
types of unidirectional fiber connections.

When DLDP detects unidirectional links, it
can automatically shut down the faulty port to avoid network problems.
Alternatively, a user can manually shut down the faulty port. 

Figure 1 Correct and incorrect fiber
connections

![](https://resource.h3c.com/en/202407/12/20240712_11705559_x_Img_x_png_0_2216096_294551_0.png)

 

### Basic concepts

#### DLDP neighbor states

If port A can receive link-layer packets
from port B on the same link, port B is a DLDP neighbor of port A. Two ports
that can exchange packets are neighbors. 

Table 1 DLDP neighbor states

| DLDP timer | Description |
| --- | --- |
| Confirmed | The link to a DLDP neighbor is bidirectional. || Unconfirmed | The state of the link to a newly discovered neighbor is not determined. |


 

#### DLDP port states

A DLDP-enabled port is called a DLDP port. A
DLDP port can have multiple neighbors, and its state varies by the DLDP
neighbor state. 

Table 2 DLDP port states

| State | Description |
| --- | --- |
| Initial | DLDP is enabled on the port, but is disabled globally. || Inactive | DLDP is enabled on the port and globally, and the link is physically down. || Bidirectional | DLDP is enabled on the port and globally, and at least one neighbor in Confirmed state exists. || Unidirectional | DLDP is enabled on the port and globally, and no neighbor in Confirmed state exists. In this state, a port does not send or receive packets other than DLDP packets any more. |




 

#### DLDP timers

Table 3 DLDP timers

| DLDP timer | Description |
| --- | --- |
| Advertisement timer | Advertisement packet sending interval (the default is 5 seconds and is configurable). || Probe timer | Probe packet sending interval. This timer is set to 1 second. || Echo timer | The Echo timer is triggered when a probe is launched for a new neighbor. The neighbor information is deleted when the timer expires. This timer is set to 10 seconds. || Entry timer | When a new neighbor joins, a neighbor entry is created and the corresponding Entry timer is triggered if the neighbor is in Confirmed state. When an Advertisement is received, the device updates the corresponding neighbor entry and the Entry timer. When the Entry timer expires, the Enhanced timer and Echo timer are triggered for the neighbor.  The value of an Entry timer is three times that of the Advertisement timer. || Enhanced timer | Probe packet sending interval. This timer is set to 1 second.  When the Entry timer expires, the Enhanced timer is triggered and probe packets are sent. At the same time, the Echo timer is triggered. || DelayDown timer | If a port is physically down, the device triggers the DelayDown timer, rather than removing the corresponding neighbor entry. The default DelayDown timer is 1 second and is configurable.  When the DelayDown timer expires, the device removes the corresponding DLDP neighbor information if the port is down, and does not perform any operation if the port is up. || RecoverProbe timer | This timer is set to 2 seconds. A port in unidirectional state regularly sends RecoverProbe packets to detect whether a unidirectional link has been restored to bidirectional. |







 

#### DLDP authentication mode

You can use DLDP authentication to prevent
network attacks and illegal detecting. 

Table 4 DLDP authentication mode

| Authentication mode | Processing at the DLDP packet sending side | Processing at the DLDP packet receiving side |
| --- | --- | --- |
| Non-authentication | The sending side sets the Authentication field of DLDP packets to 0\. | The receiving side examines the authentication information of received DLDP packets and drops packets where the authentication information conflicts with the local configuration. || Plaintext authentication | The sending side sets the Authentication field to the password configured in plain text. || MD5 authentication | The sending side encrypts the user configured password by using MD5 algorithm, and assigns the digest to the Authentication field. |



 

### How DLDP works

#### Detecting one neighbor

When two devices are connected through an
optical fiber or a network cable, enable DLDP to detect unidirectional links to
the neighbor. The following illustrates the unidirectional link detection
process in two cases:

·     Unidirectional links occur before you enable
DLDP. 

Figure 2 Cross-connected
fibers

![](https://resource.h3c.com/en/202407/12/20240712_11705560_x_Img_x_png_1_2216096_294551_0.png)

 

As shown in [Figure 2](#_Ref326855259),
before you enable DLDP, the optical fibers between Device A and Device B are
cross-connected. After you enable DLDP, the four ports are all up and in unidirectional
state, and they send RecoverProbe packets. Take Port A1 as an example to
illustrate the unidirectional link detection process. 

**a.**Port A1 receives the RecoverProbe packet
from Port B2, and returns a RecoverEcho packet. 

**b.**Port B2 cannot receive any RecoverEcho
packet from Port A1, so Port B2 cannot become the neighbor of Port A1. 

**c.**Port B1 can receive the RecoverEcho packet
from Port A1, but Port B1 is not the intended destination, so Port B1 cannot
become the neighbor of Port A1. 

The same process occurs on the other
three ports. The four ports are all in unidirectional state. 

·     Unidirectional links occur after you enable
DLDP. 

Figure 3 Broken fiber

![](https://resource.h3c.com/en/202407/12/20240712_11705561_x_Img_x_png_2_2216096_294551_0.png)

 

As shown in [Figure 3](#_Ref326858491), Device
A and Device B are connected through an optical fiber. After you enable DLDP, Port
A1 and Port B1 establish the bidirectional neighborship in the following way: 

**a.**Port A1 that is physically up enters the unidirectional
state and sends a RecoverProbe packet. 

**b.**After receiving the RecoverProbe packet, Port
B1 returns a RecoverEcho packet. 

**c.**After Port A1 receives the RecoverEcho
packet, it examines the neighbor information in the packet. If the neighbor
information matches the local information, Port A1 establishes the neighborship
with Port B1 and transits to bidirectional state. Port A1 then starts the Entry
timer and periodically sends Advertisement packets. 

**d.**After Port B1 receives the Advertisement
packet, it establishes the Unconfirmed neighborship with Port A1. Port B1 then
starts the Echo timer and Probe timer, and periodically sends Probe packets. 

**e.**After receiving the Probe packet, Port A1
returns an Echo packet. 

**f.**After Port B1 receives the Echo packet, it examines
the neighbor information in the packet. If the neighbor information matches the
local information, the neighbor state of Port A1 becomes Confirmed. Port B1
then transits to bidirectional state, starts the Entry timer, and periodically
sends Advertisement packets. 

The bidirectional neighborship between Port
A1 and Port B1 is now established. 

After that, when Port B1's Rx end fails
to receive signals, Port B1 is physically down and enters the Inactive state.
Because Port B1's Tx end can still send signals to Port A1, Port A1 stays up.
After the Entry timer for Port B1 expires, Port A1 starts the Enhanced timer
and Echo timer, and sends a probe packet to Port B1. Because Port A1's Tx line
is broken, Port A1 cannot receive the Echo packet from Port B1 after the Echo
timer expires. Port A1 then enters the unidirectional state, and sends a Disable
packet to Port B1. At the same time, Port A1 deletes the neighborship with Port
B1, and starts the RecoverProbe timer. Port B1 stays in Inactive state during
this process. 

When an interface is physically down, but
the Tx end of the interface is still operating, DLDP sends a LinkDown packet to
inform the peer to delete the relevant neighbor entry. 

#### Detecting multiple neighbors

When multiple devices are connected through
a hub, enable DLDP on all interfaces connected to the hub to detect
unidirectional links among the neighbors. When no Confirmed neighbor exists, an
interface enters the unidirectional state. 

Figure 4 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705562_x_Img_x_png_3_2216096_294551_0.png)

 

As shown in [Figure 4](#_Ref326918027),
Device A through Device D are connected through a hub, and enabled with DLDP.
When Port A1, Port B1, and Port C1 detect that the link to Port D1 fails, they
delete the neighborship with Port D1, but stay in bidirectional state. 

## Restrictions and guidelines: DLDP configuration

When you configure DLDP, follow these
configuration restrictions and guidelines:

·     For DLDP to operate correctly, enable DLDP on
both sides and make sure the following settings are consistent: 

¡     Interval
to send Advertisement packets.

¡     DLDP
authentication mode.

¡     Password.


·     For DLDP to operate correctly, configure the
full duplex mode for the ports at the two ends of the link, and configure the
same speed for the two ports. 

## DLDP tasks at a glance

To configure DLDP, perform the following
tasks:

**1\.**[Enabling DLDP](#_Ref279391056)

**2\.**(Optional.) [Setting the interval to send
advertisement packets](#_Ref167075303)

**3\.**(Optional.) [Setting the DelayDown timer](#_Ref167075314)

**4\.**(Optional.) [Setting the port shutdown mode](#_Ref151196286)

**5\.**(Optional.) [Configuring DLDP authentication](#_Ref167075334)

## Enabling DLDP

#### About this task

After a port is enabled with DLDP, the port
enters Initial state and then transitions to Unidirectional state. By default, DLDP
blocks a port immediately when the port transitions to Unidirectional state. This
behavior causes a traffic disruption that lasts until the port enters Bidirectional
state when establishing a Confirmed neighbor.

You can set the delay time for DLDP to
block a port upon an Initial-to-Unidirectional state transition. DLDP does not
block the port until the delay time expires.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enable DLDP globally.

dldp global enable

By default, DLDP is globally disabled.

**3\.**Enter Ethernet interface view.

interface interface-type interface-number

**4\.**Enable DLDP and set the delay time for DLDP
to block the port upon an Initial-to-Unidirectional state transition.

dldp enable \[ initial-unidirectional-delay time ]

By default, DLDP is disabled on a port,
and when DLDP is enabled, a port is blocked immediately upon an Initial-to-Unidirectional
state transition.

## Setting the interval to send advertisement packets

#### About this task

To make sure DLDP can detect unidirectional
links before network performance deteriorates, set the advertisement interval
appropriate for your network environment. As a best practice, use the default
interval. 

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Set the interval to send Advertisement
packets.

dldp interval interval

By default, the interval is 5 seconds.

## Setting the DelayDown timer

#### About this task

When the Tx line fails, some ports might go
down and then come up again, causing optical signal jitters on the Rx line. To
prevent the device from removing neighbor entries in such cases, set the
DelayDown timer for the device. The device starts the DelayDown timer when a
port goes down due to a Tx line failure. If the port remains down when the
timer expires, the device removes the DLDP neighbor information. If the port
comes up, the device takes no action. 

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Set the DelayDown timer.

dldp delaydown-timer time

The default is 1 second.

The DelayDown timer setting applies to
all DLDP-enabled ports.

## Setting the port shutdown mode

#### About this task

On detecting a unidirectional link, DLDP
shuts down the ports in one of the following modes:

·     Auto mode—When DLDP detects a unidirectional link, it shuts down the
unidirectional port. When the link becomes bidirectional, DLDP brings up the
port that was shut down.

·     Manual mode—When DLDP detects a unidirectional link, it does not shut down the
port. You need to manually shut it down. To verify the link status, use the undo shutdown command to
bring up the port. If the link becomes bidirectional, the port becomes bidirectional.
Use this mode to prevent normal links from being shut down because of false
unidirectional link reports in the following cases:

¡     The
network performance is low.

¡     The
device is busy.

¡     The
CPU usage is high.

·     Hybrid mode—When DLDP detects a unidirectional link, it shuts down the
unidirectional port and stops link detection. To verify the link status, use
the undo shutdown command to bring up the port. If
the link becomes bidirectional, the port becomes bidirectional. 

#### Restrictions and guidelines

You can set the port shutdown mode for all
interfaces in system view or for a single interface in interface view. The
setting in interface view takes precedence over the setting in system view.

To enable remote OAM loopback on a DLDP
port, set the port shutdown mode to manual.
Otherwise, DLDP automatically shuts down the port when it receives a packet
sent by itself. This causes remote OAM loopback failure. For more information
about Ethernet OAM, see "Configuring Ethernet OAM."

#### Setting the global port shutdown mode

**1\.**Enter system view.

system-view

**2\.**Set the global port shutdown mode.

dldp unidirectional-shutdown { auto \| hybrid \| manual }

The default mode is auto.

#### Setting the port shutdown mode for an interface

**1\.**Enter system view.

system-view

**2\.**Enter Ethernet interface view.

interface interface-type interface-number

**3\.**Set the the port shutdown mode for the interface.

dldp port unidirectional-shutdown { auto \| hybrid \| manual }

By default, the global setting is used.

## ConfiguringDLDP authentication

#### About this task

You can guard your network against attacks
and malicious probes by configuring an appropriate DLDP authentication mode,
which can be plain text authentication or MD5 authentication. If your network
is safe, you can choose not to authenticate. 

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Configure a DLDP authentication mode.

dldp authentication-mode { md5 \| none \| simple }

The default authentication mode is none.

**3\.**Configure the password for DLDP
authentication.

dldp authentication-password { cipher \| simple } string

By default, no password is configured for
DLDP authentication.

If you do not configure the
authentication password after you configure the authentication mode, the
authentication mode is none no matter which authentication
mode you configure.

## Display and maintenance commands forDLDP

Execute display
commands in any view and the reset
command in user view.

 

| Task | Command |
| --- | --- |
| Display the DLDP configuration globally and of a port. | display dldp \[ interface interface-type interface-number ] || Display the statistics on DLDP packets passing through a port. | display dldp statistics \[ interface interface-type interface-number ] || Clear the statistics on DLDP packets passing through a port. | reset dldp statistics \[ interface interface-type interface-number ] |



 

## DLDP configuration examples

### Example: Configuring the auto port shutdown mode

#### Network configuration

As shown in [Figure 5](#_Ref97266835), Device
A and Device B are connected through two fiber pairs.

Configure DLDP to automatically shut down
the faulty port upon detecting a unidirectional link, and automatically bring
up the port after you clear the fault. 

Figure 5 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705563_x_Img_x_png_4_2216096_294551_0.png)

‌

#### Procedure

**1\.**Configure Device A:

\# Enable DLDP globally.

\<DeviceA\> system-view

\[DeviceA] dldp global enable

\# Configure Ten-GigabitEthernet 1/0/1 to operate in full
duplex mode and at 10000 Mbps, and enable DLDP on the port.

\[DeviceA] interface ten-gigabitethernet 1/0/1

\[DeviceA-Ten-GigabitEthernet1/0/1] duplex full

\[DeviceA-Ten-GigabitEthernet1/0/1] speed 10000

\[DeviceA-Ten-GigabitEthernet1/0/1] dldp enable

\[DeviceA-Ten-GigabitEthernet1/0/1] quit

\# Configure Ten-GigabitEthernet 1/0/2 to operate in full
duplex mode and at 10000 Mbps, and enable DLDP on the port.

\[DeviceA] interface ten-gigabitethernet 1/0/2

\[DeviceA-Ten-GigabitEthernet1/0/2] duplex full

\[DeviceA-Ten-GigabitEthernet1/0/2] speed 10000

\[DeviceA-Ten-GigabitEthernet1/0/2] dldp enable

\[DeviceA-Ten-GigabitEthernet1/0/2] quit

\# Set the global port shutdown mode to auto.

\[DeviceA] dldp unidirectional-shutdown
auto

**2\.**Configure Device B:

\# Enable DLDP globally.

\<DeviceB\> system-view

\[DeviceB] dldp global enable

\# Configure Ten-GigabitEthernet 1/0/1 to operate in full
duplex mode and at 10000 Mbps, and enable DLDP on it.

\[DeviceB] interface ten-gigabitethernet 1/0/1

\[DeviceB-Ten-GigabitEthernet1/0/1] duplex full

\[DeviceB-Ten-GigabitEthernet1/0/1] speed 10000

\[DeviceB-Ten-GigabitEthernet1/0/1] dldp enable

\[DeviceB-Ten-GigabitEthernet1/0/1] quit

\# Configure Ten-GigabitEthernet 1/0/2 to operate in full
duplex mode and at 10000 Mbps, and enable DLDP on it.

\[DeviceB] interface ten-gigabitethernet 1/0/2

\[DeviceB-Ten-GigabitEthernet1/0/2] duplex full

\[DeviceB-Ten-GigabitEthernet1/0/2] speed 10000

\[DeviceB-Ten-GigabitEthernet1/0/2] dldp enable

\[DeviceB-Ten-GigabitEthernet1/0/2] quit

\# Set the global port shutdown mode to
auto.

\[DeviceB] dldp unidirectional-shutdown
auto

#### Verifying the configuration

\# Display the DLDP configuration globally and
on all the DLDP-enabled ports of Device A.

\[DeviceA] display dldp

 DLDP global status: Enabled

 DLDP advertisement interval: 5s

 DLDP authentication-mode: None

 DLDP unidirectional-shutdown mode:
Auto

 DLDP delaydown-timer value: 1s

 Number of enabled ports: 2

 

Interface Ten-GigabitEthernet1/0/1

 DLDP port state: Bidirectional

 DLDP port unidirectional-shutdown
mode: None

 DLDP initial-unidirectional-delay:
0s

 Number of the port’s neighbors: 1

  Neighbor MAC address: 0023-8956-3600

  Neighbor port index: 1

  Neighbor state: Confirmed

  Neighbor aged time: 11s

  Neighbor echo time: \-

 

Interface Ten-GigabitEthernet1/0/2

 DLDP port state: Bidirectional

 DLDP port unidirectional-shutdown
mode: None

 DLDP initial-unidirectional-delay:
0s

 Number of the port’s neighbors: 1

  Neighbor MAC address: 0023-8956-3600

  Neighbor port index: 2

  Neighbor state: Confirmed

  Neighbor aged time: 12s

  Neighbor echo time: \-

The output shows that both Ten-GigabitEthernet 1/0/1 and Ten-GigabitEthernet 1/0/2 are bidirectional.

\# Enable the monitoring of logs on the
current terminal on Device A. Set the lowest level of the logs that can be
output to the current terminal to 6\.

\[DeviceA] quit

\<DeviceA\> terminal monitor

The current terminal is enabled to
display logs.

\<DeviceA\> terminal logging
level 6

The following log information is displayed on
Device A:

\<DeviceA\>%Jul 11 17:40:31:089
2012 DeviceA IFNET/3/PHY\_UPDOWN: Physical state on the interface Physical state
on the interface Ten-GigabitEthernet1/0/1 changed to down.

%Jul 11 17:40:31:091 2012 DeviceA
IFNET/5/LINK\_UPDOWN: Line protocol state on the interface Ten-GigabitEthernet1/0/1 changed to down. 

%Jul 11 17:40:31:677 2012 DeviceA
IFNET/3/PHY\_UPDOWN: Physical state on the interface Physical state on the
interface Ten-GigabitEthernet1/0/2 changed to down.

%Jul 11 17:40:31:678 2012 DeviceA
IFNET/5/LINK\_UPDOWN: Line protocol state on the interface Ten-GigabitEthernet1/0/2 changed to down.

%Jul 11 17:40:38:544 2012 DeviceA
IFNET/3/PHY\_UPDOWN: Physical state on the interface Physical state on the
interface Ten-GigabitEthernet1/0/1 l changed to up.

%Jul 11 17:40:38:836 2012 DeviceA
IFNET/3/PHY\_UPDOWN: Physical state on the interface Physical state on the
interface Ten-GigabitEthernet1/0/2 changed to up.

The output shows the following: 

·     The port status of both Ten-GigabitEthernet 1/0/1 and Ten-GigabitEthernet 1/0/2 is down and then
up. 

·     The link status of both Ten-GigabitEthernet 1/0/1 and Ten-GigabitEthernet 1/0/2 is always down. 

\# Display the DLDP configuration globally
and of all the DLDP-enabled ports.

\<DeviceA\> display dldp

 DLDP global status: Enabled

 DLDP advertisement interval: 5s

 DLDP authentication-mode: None

 DLDP unidirectional-shutdown mode:
Auto

 DLDP delaydown-timer value: 1s

 Number of enabled ports: 2

 

Interface Ten-GigabitEthernet1/0/1

 DLDP port state: Unidirectional

 DLDP port unidirectional-shutdown
mode: None

 DLDP initial-unidirectional-delay:
0s

 Number of the port’s neighbors: 0
(Maximum number ever detected: 1\)

 

Interface Ten-GigabitEthernet1/0/2

 DLDP port state: Unidirectional

 DLDP port unidirectional-shutdown
mode: None

 DLDP initial-unidirectional-delay:
0s

 Number of the port’s neighbors: 0
(Maximum number ever detected: 1\)

The output shows that the DLDP port status
of both Ten-GigabitEthernet 1/0/1 and Ten-GigabitEthernet 1/0/2 is unidirectional.
DLDP detects unidirectional links on them and automatically shuts down the two
ports.

The unidirectional links are caused by
cross-connected fibers. Correct the fiber connections. As a result, the ports
shut down by DLDP automatically recover, and Device A displays the following
log information:

\<DeviceA\>%Jul 11 17:42:57:709
2012 DeviceA IFNET/3/PHY\_UPDOWN: Physical state on the interface Ten-GigabitEthernet1/0/1 changed to down.

%Jul 11 17:42:58:603 2012 DeviceA
IFNET/3/PHY\_UPDOWN: Physical state on the interface Ten-GigabitEthernet1/0/2 changed to down.

%Jul 11 17:43:02:342 2012 DeviceA
IFNET/3/PHY\_UPDOWN: Physical state on the interface Ten-GigabitEthernet1/0/1 changed to up.

%Jul 11 17:43:02:343 2012 DeviceA
DLDP/6/DLDP\_NEIGHBOR\_CONFIRMED: A neighbor was confirmed on interface Ten-GigabitEthernet1/0/1. The neighbor's
system MAC is 0023-8956-3600, and the port index is 1\.

%Jul 11 17:43:02:344 2012 DeviceA
DLDP/6/DLDP\_LINK\_BIDIRECTIONAL: DLDP detected a bidirectional link on interface
Ten-GigabitEthernet1/0/1.

%Jul 11 17:43:02:353 2012 DeviceA
IFNET/5/LINK\_UPDOWN: Line protocol state on the interface Ten-GigabitEthernet1/0/1 changed to up.

%Jul 11 17:43:02:357 2012 DeviceA
IFNET/3/PHY\_UPDOWN: Physical state on the interface Ten-GigabitEthernet1/0/2 changed to up.

%Jul 11 17:43:02:362 2012 DeviceA
DLDP/6/DLDP\_NEIGHBOR\_CONFIRMED: A neighbor was confirmed on interface Ten-GigabitEthernet1/0/2. The neighbor's
system MAC is 0023-8956-3600, and the port index is 2\.

%Jul 11 17:43:02:362 2012 DeviceA
DLDP/6/DLDP\_LINK\_BIDIRECTIONAL: DLDP detected a bidirectional link on interface
Ten-GigabitEthernet1/0/2.

%Jul 11 17:43:02:368 2012 DeviceA
IFNET/5/LINK\_UPDOWN: Line protocol state on the interface Ten-GigabitEthernet1/0/2 changed
to up.

The output shows that the port status and
link status of both Ten-GigabitEthernet 1/0/1 and Ten-GigabitEthernet 1/0/2 are now up and
their DLDP neighbors are determined.

### Example: Configuring the manual port shutdown mode

#### Network configuration

As shown in [Figure 6](#_Ref256062673),
Device A and Device B are connected through two fiber pairs.

Configure DLDP to detect unidirectional
links. When a unidirectional link is detected, the administrator must manually
shut down the port.

Figure 6 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705564_x_Img_x_png_5_2216096_294551_0.png)

‌

#### Procedure

**1\.**Configure Device A:

\# Enable DLDP globally.

\<DeviceA\> system-view

\[DeviceA] dldp enable

\# Configure Ten-GigabitEthernet 1/0/1 to operate in full
duplex mode and at 10000 Mbps, and enable DLDP on the port.

\[DeviceA] interface ten-gigabitethernet 1/0/1

\[DeviceA-Ten-GigabitEthernet1/0/1] duplex full

\[DeviceA-Ten-GigabitEthernet1/0/1] speed 10000

\[DeviceA-Ten-GigabitEthernet1/0/1] dldp enable

\[DeviceA-Ten-GigabitEthernet1/0/1] quit

\# Configure Ten-GigabitEthernet 1/0/2 to operate in full
duplex mode and at 10000 Mbps, and enable DLDP on the port.

\[DeviceA] interface ten-gigabitethernet 1/0/2

\[DeviceA-Ten-GigabitEthernet1/0/2] duplex full

\[DeviceA-Ten-GigabitEthernet1/0/2] speed 10000

\[DeviceA-Ten-GigabitEthernet1/0/2] dldp enable

\[DeviceA-Ten-GigabitEthernet1/0/2] quit

\# Set the global port shutdown mode to
manual.

\[DeviceA] dldp unidirectional-shutdown
manual

**2\.**Configure Device B:

\# Enable DLDP globally.

\<DeviceB\> system-view

\[DeviceB] dldp global enable

\# Configure Ten-GigabitEthernet 1/0/1 to operate in full
duplex mode and at 10000 Mbps, and enable DLDP on it.

\[DeviceB] interface ten-gigabitethernet 1/0/1

\[DeviceB-Ten-GigabitEthernet1/0/1] duplex full

\[DeviceB-Ten-GigabitEthernet1/0/1] speed 10000

\[DeviceB-Ten-GigabitEthernet1/0/1] dldp enable

\[DeviceB-Ten-GigabitEthernet1/0/1] quit

\# Configure Ten-GigabitEthernet 1/0/2 to operate in full
duplex mode and at 10000 Mbps, and enable DLDP on it.

\[DeviceB] interface ten-gigabitethernet 1/0/2

\[DeviceB-Ten-GigabitEthernet1/0/2] duplex full

\[DeviceB-Ten-GigabitEthernet1/0/2] speed 10000

\[DeviceB-Ten-GigabitEthernet1/0/2] dldp enable

\[DeviceB-Ten-GigabitEthernet1/0/2] quit

\# Set the global port shutdown mode to
manual.

\[DeviceB] dldp unidirectional-shutdown
manual

#### Verifying the configuration

\# Display the DLDP configuration globally
and on all the DLDP-enabled ports of Device A.

\[DeviceA] display dldp

 DLDP global status: Enabled

 DLDP advertisement interval: 5s

 DLDP authentication-mode: None

 DLDP unidirectional-shutdown mode:
Manual

 DLDP delaydown-timer value: 1s

 Number of enabled ports: 2

 

Interface Ten-GigabitEthernet1/0/1

 DLDP port state: Bidirectional

 DLDP port unidirectional-shutdown
mode: None

 DLDP initial-unidirectional-delay:
0s

 Number of the port’s neighbors: 1

  Neighbor MAC address: 0023-8956-3600

  Neighbor port index: 1

  Neighbor state: Confirmed

  Neighbor aged time: 11s

  Neighbor echo time: \-

 

Interface Ten-GigabitEthernet1/0/2

 DLDP port state: Bidirectional

 DLDP port unidirectional-shutdown
mode: None

 DLDP initial-unidirectional-delay:
0s

 Number of the port’s neighbors: 1

  Neighbor MAC address: 0023-8956-3600

  Neighbor port index: 2

  Neighbor state: Confirmed

  Neighbor aged time: 12s

  Neighbor echo time: \-

The output shows that both Ten-GigabitEthernet 1/0/1 and Ten-GigabitEthernet 1/0/2 are in bidirectional
state, which means both links are bidirectional.

\# Enable the monitoring of logs on the current
terminal on Device A. Set the lowest level of the logs that can be output to
the current terminal to 6\.

\[DeviceA] quit

\<DeviceA\> terminal monitor

The current terminal is enabled to
display logs.

\<DeviceA\> terminal logging
level 6

The following log information is displayed
on Device A:

\<DeviceA\>%Jul 12 08:29:17:786
2012 DeviceA IFNET/3/PHY\_UPDOWN: Physical state on the interface Ten-GigabitEthernet1/0/1 changed to down.

%Jul 12 08:29:17:787 2012 DeviceA
IFNET/5/LINK\_UPDOWN: Line protocol state on the interface Ten-GigabitEthernet1/0/1 changed to down.

%Jul 12 08:29:17:800 2012 DeviceA
IFNET/3/PHY\_UPDOWN: Physical state on the interface Ten-GigabitEthernet1/0/2 changed to down.

%Jul 12 08:29:17:800 2012 DeviceA
IFNET/5/LINK\_UPDOWN: Line protocol state on the interface Ten-GigabitEthernet1/0/2 changed to down.

%Jul 12 08:29:25:004 2012 DeviceA
IFNET/3/PHY\_UPDOWN: Physical state on the interface Ten-GigabitEthernet1/0/1 changed to up.

%Jul 12 08:29:25:005 2012 DeviceA IFNET/5/LINK\_UPDOWN:
Line protocol state on the interface Ten-GigabitEthernet1/0/1 changed to up.

%Jul 12 08:29:25:893 2012 DeviceA
IFNET/3/PHY\_UPDOWN: Physical state on the interface Ten-GigabitEthernet1/0/2 changed to up.

%Jul 12 08:29:25:894 2012 DeviceA
IFNET/5/LINK\_UPDOWN: Line protocol state on the interface Ten-GigabitEthernet1/0/2 changed to up.

The output shows that the port status and
link status of both Ten-GigabitEthernet 1/0/1 and Ten-GigabitEthernet 1/0/2 are down and then
up. 

\# Display the DLDP configuration globally
and of all the DLDP-enabled ports.

\<DeviceA\> display dldp

 DLDP global status: Enabled

 DLDP advertisement interval: 5s

 DLDP authentication-mode: None

 DLDP unidirectional-shutdown mode:
Manual

 DLDP delaydown-timer value: 1s

 Number of enabled ports: 2

 

Interface Ten-GigabitEthernet1/0/1

 DLDP port state: Unidirectional

 DLDP port unidirectional-shutdown
mode: None

 DLDP initial-unidirectional-delay:
0s

 Number of the port’s neighbors: 0 (Maximum
number ever detected: 1\)

 

Interface Ten-GigabitEthernet1/0/2

 DLDP port state: Unidirectional

 DLDP port unidirectional-shutdown
mode: None

 DLDP initial-unidirectional-delay:
0s

 Number of the port’s neighbors: 0
(Maximum number ever detected: 1\)

The output shows that the DLDP port status
of both Ten-GigabitEthernet 1/0/1 and Ten-GigabitEthernet 1/0/2 is unidirectional.
DLDP detects unidirectional links on the two ports but does not shut them down.

The unidirectional links are caused by
cross-connected fibers. Manually shut down the two ports:

\# Shut down Ten-GigabitEthernet 1/0/1.

\<DeviceA\> system-view

\[DeviceA] interface ten-gigabitethernet 1/0/1

\[DeviceA-Ten-GigabitEthernet1/0/1] shutdown

The following log information is displayed
on Device A:

\[DeviceA-Ten-GigabitEthernet1/0/1]%Jul 12
08:34:23:717 2012 DeviceA IFNET/3/PHY\_UPDOWN: Physical state on the interface Ten-GigabitEthernet1/0/1 changed to down.

%Jul 12 08:34:23:718 2012 DeviceA
IFNET/5/LINK\_UPDOWN: Line protocol state on the interface Ten-GigabitEthernet1/0/1 changed to down.

%Jul 12 08:34:23:778 2012 DeviceA
IFNET/3/PHY\_UPDOWN: Physical state on the interface Ten-GigabitEthernet1/0/2 changed to down.

%Jul 12 08:34:23:779 2012 DeviceA
IFNET/5/LINK\_UPDOWN: Line protocol state on the interface Ten-GigabitEthernet1/0/2 changed to down.

The output shows that the port status and link
status of both Ten-GigabitEthernet 1/0/1 and Ten-GigabitEthernet 1/0/2 are now down.

\# Shut down Ten-GigabitEthernet 1/0/2.

\[DeviceA-Ten-GigabitEthernet1/0/1] quit

\[DeviceA] interface ten-gigabitethernet 1/0/2

\[DeviceA-Ten-GigabitEthernet1/0/2] shutdown

Correct the fiber connections and bring up
the two ports:

\# Bring up Ten-GigabitEthernet 1/0/2.

\[DeviceA-Ten-GigabitEthernet1/0/2] undo shutdown

The following log information is displayed
on Device A:

\[DeviceA-Ten-GigabitEthernet1/0/2]%Jul 12
08:46:17:677 2012 DeviceA IFNET/3/PHY\_UPDOWN: Physical state on the interface Ten-GigabitEthernet1/0/2 changed to up.

%Jul 12 08:46:17:678 2012 DeviceA
IFNET/5/LINK\_UPDOWN: Line protocol state on the interface Ten-GigabitEthernet1/0/2 changed to up.

%Jul 12 08:46:17:959 2012 DeviceA
DLDP/6/DLDP\_NEIGHBOR\_CONFIRMED: A neighbor was confirmed on interface Ten-GigabitEthernet1/0/2. The neighbor's
system MAC is 0023-8956-3600, and the port index is 2\.

%Jul 12 08:46:17:959 2012 DeviceA
DLDP/6/DLDP\_LINK\_BIDIRECTIONAL: DLDP detected a bidirectional link on interface
Ten-GigabitEthernet1/0/2.

The output shows that the port status and
link status of Ten-GigabitEthernet 1/0/2 are now up and its
DLDP neighbors are determined.

\# Bring up Ten-GigabitEthernet 1/0/1.

\[DeviceA-Ten-GigabitEthernet1/0/2] quit

\[DeviceA] interface ten-gigabitethernet 1/0/1

\[DeviceA-Ten-GigabitEthernet1/0/1] undo shutdown

The following log information is displayed
on Device A:

\[DeviceA-Ten-GigabitEthernet1/0/1]%Jul 12
08:48:25:952 2012 DeviceA IFNET/3/PHY\_UPDOWN: Physical state on the interface Ten-GigabitEthernet1/0/1 changed to up.

%Jul 12 08:48:25:952 2012 DeviceA
DLDP/6/DLDP\_NEIGHBOR\_CONFIRMED: A neighbor was confirmed on interface Ten-GigabitEthernet1/0/1. The neighbor's
system MAC is 0023-8956-3600, and the port index is 1\.

%Jul 12 08:48:25:953 2012 DeviceA
IFNET/5/LINK\_UPDOWN: Line protocol state on the interface Ten-GigabitEthernet1/0/1 changed to up.

%Jul 12 08:48:25:953 2012 DeviceA
DLDP/6/DLDP\_LINK\_BIDIRECTIONAL: DLDP detected a bidirectional link on interface
Ten-GigabitEthernet1/0/1.

The output shows that the port status and
link status of Ten-GigabitEthernet 1/0/1 are now up and its
DLDP neighbors are determined. 

### Example: Configuring the hybrid port shutdown mode

#### Network configuration

As shown in [Figure 7](#_Ref456944530)[Figure 6](#_Ref256062673),
Device A and Device B are connected through two fiber pairs.

Configure DLDP to detect unidirectional
links. When a unidirectional link is detected, DLDP automatically shuts down
the unidirectional port. The administrator needs to bring up the port after
clearing the fault.

Figure 7 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705565_x_Img_x_png_6_2216096_294551_0.png)

‌

#### Procedure

**1\.**Configure Device A:

\# Enable DLDP globally.

\<DeviceA\> system-view

\[DeviceA] dldp enable

\# Configure Ten-GigabitEthernet 1/0/1 to operate in full
duplex mode and at 10000 Mbps, and enable DLDP on the port.

\[DeviceA] interface ten-gigabitethernet 1/0/1

\[DeviceA-Ten-GigabitEthernet1/0/1] duplex full

\[DeviceA-Ten-GigabitEthernet1/0/1] speed 10000

\[DeviceA-Ten-GigabitEthernet1/0/1] dldp enable

\[DeviceA-Ten-GigabitEthernet1/0/1] quit

\# Configure Ten-GigabitEthernet 1/0/2 to operate in full
duplex mode and at 10000 Mbps, and enable DLDP on the port.

\[DeviceA] interface ten-gigabitethernet 1/0/2

\[DeviceA-Ten-GigabitEthernet1/0/2] duplex full

\[DeviceA-Ten-GigabitEthernet1/0/2] speed 10000

\[DeviceA-Ten-GigabitEthernet1/0/2] dldp enable

\[DeviceA-Ten-GigabitEthernet1/0/2] quit

\# Set the global port shutdown mode to
hybrid.

\[DeviceA] dldp unidirectional-shutdown
hybrid

**2\.**Configure Device B:

\# Enable DLDP globally.

\<DeviceB\> system-view

\[DeviceB] dldp global enable

\# Configure Ten-GigabitEthernet 1/0/1 to operate in full
duplex mode and at 10000 Mbps, and enable DLDP on it.

\[DeviceB] interface ten-gigabitethernet 1/0/1

\[DeviceB-Ten-GigabitEthernet1/0/1] duplex full

\[DeviceB-Ten-GigabitEthernet1/0/1] speed 10000

\[DeviceB-Ten-GigabitEthernet1/0/1] dldp enable

\[DeviceB-Ten-GigabitEthernet1/0/1] quit

\# Configure Ten-GigabitEthernet 1/0/2 to operate in full
duplex mode and at 10000 Mbps, and enable DLDP on it.

\[DeviceB] interface ten-gigabitethernet 1/0/2

\[DeviceB-Ten-GigabitEthernet1/0/2] duplex full

\[DeviceB-Ten-GigabitEthernet1/0/2] speed 10000

\[DeviceB-Ten-GigabitEthernet1/0/2] dldp enable

\[DeviceB-Ten-GigabitEthernet1/0/2] quit

\# Set the global port shutdown mode to hybrid.

\[DeviceB] dldp unidirectional-shutdown
hybrid

#### Verifying the configuration

\# Display the DLDP configuration globally
and on all the DLDP-enabled ports of Device A.

\[DeviceA] display dldp

 DLDP global status: Enabled

 DLDP advertisement interval: 5s

 DLDP authentication-mode: None

 DLDP unidirectional-shutdown mode: Hybrid

 DLDP delaydown-timer value: 1s

 Number of enabled ports: 2

 

Interface Ten-GigabitEthernet1/0/1

 DLDP port state: Bidirectional

 DLDP port unidirectional-shutdown
mode: None

 DLDP initial-unidirectional-delay:
0s

 Number of the port’s neighbors: 1

  Neighbor MAC address: 0023-8956-3600

  Neighbor port index: 1

  Neighbor state: Confirmed

  Neighbor aged time: 11s

  Neighbor echo time: \-

 

Interface Ten-GigabitEthernet1/0/2

 DLDP port state: Bidirectional

 DLDP port unidirectional-shutdown
mode: None

 DLDP initial-unidirectional-delay:
0s

 Number of the port’s neighbors: 1

  Neighbor MAC address: 0023-8956-3600

  Neighbor port index: 2

  Neighbor state: Confirmed

  Neighbor aged time: 12s

  Neighbor echo time: \-

The output shows that both Ten-GigabitEthernet 1/0/1 and Ten-GigabitEthernet 1/0/2 are in bidirectional
state, which means both links are bidirectional.

\# Enable the monitoring of logs on the current
terminal on Device A. Set the lowest level of the logs that can be output to
the current terminal to 6\.

\[DeviceA] quit

\<DeviceA\> terminal monitor

The current terminal is enabled to
display logs.

\<DeviceA\> terminal logging
level 6

The following log information is displayed
on Device A:

\<DeviceA\>%Jan  4 07:16:06:556
2011 DeviceA DLDP/5/DLDP\_NEIGHBOR\_AGED: A neighbor on interface

Ten-GigabitEthernet1/0/1 was deleted because
the neighbor was aged. The neighbor's system MAC is 0023-8956-3600, and the
port index is 162\.

%Jan  4 07:16:06:560 2011 DeviceA
DLDP/5/DLDP\_NEIGHBOR\_AGED: A neighbor on interface

Ten-GigabitEthernet1/0/2 was deleted because
the neighbor was aged. The neighbor's system MAC is 0023-8956-3600, and the
port index is 165\.

%Jan  4 07:16:06:724 2011 DeviceA
IFNET/3/PHY\_UPDOWN: Physical state on the interface Ten-GigabitEthernet1/0/1 changed to down.

%Jan  4 07:16:06:730 2011 DeviceA
IFNET/3/PHY\_UPDOWN: Physical state on the interface Ten-GigabitEthernet1/0/2 changed to down.

%Jan  4 07:16:06:736 2011 DeviceA
IFNET/5/LINK\_UPDOWN: Line protocol state on the interface Ten-GigabitEthernet1/0/1 changed to down.

%Jan  4 07:16:06:738 2011 DeviceA
IFNET/5/LINK\_UPDOWN: Line protocol state on the interface Ten-GigabitEthernet1/0/2 changed to down.

%Jan  4 07:16:07:152 2011 DeviceA
DLDP/3/DLDP\_LINK\_UNIDIRECTIONAL: DLDP detected a unidirectional link on
interface Ten-GigabitEthernet1/0/1. DLDP automatically
shut down the interface. Please manually bring up the interface.

%Jan  4 07:16:07:156 2011 DeviceA
DLDP/3/DLDP\_LINK\_UNIDIRECTIONAL: DLDP detected a unidirectional link on
interface Ten-GigabitEthernet1/0/2. DLDP automatically
shut down the interface. Please manually bring up the interface.

The output shows that the port status and
link status of both Ten-GigabitEthernet 1/0/1 and Ten-GigabitEthernet 1/0/2 are down. 

\# Display the DLDP configuration globally
and of all the DLDP-enabled ports.

\<DeviceA\> display dldp

 DLDP global status: Enabled

 DLDP advertisement interval: 5s

 DLDP authentication-mode: None

 DLDP unidirectional-shutdown mode:
Hybrid

 DLDP delaydown-timer value: 1s

 Number of enabled ports: 2

 

Interface Ten-GigabitEthernet1/0/1

 DLDP port state: Inactive

 DLDP port unidirectional-shutdown
mode: None

 DLDP initial-unidirectional-delay:
0s

 Number of the port's neighbors: 0
(Maximum number ever detected: 1\)

Interface Ten-GigabitEthernet1/0/2

 DLDP port state: Inactive

 DLDP port unidirectional-shutdown
mode: None

 DLDP initial-unidirectional-delay:
0s

 Number of the port's neighbors: 0
(Maximum number ever detected: 1\)

The output shows that DLDP detects a
unidirectional link and shuts down Ten-GigabitEthernet 1/0/1 and Ten-GigabitEthernet 1/0/2.

The unidirectional links are caused by
cross-connected fibers. Bring up the two ports after correcting the fiber
connection:

\# Bring up Ten-GigabitEthernet 1/0/1.

\<DeviceA\> system-view

\[DeviceA] interface ten-gigabitethernet 1/0/1

\[DeviceA-Ten-GigabitEthernet1/0/1] undo shutdown

The following log information is displayed
on Device A:

\[DeviceA-Ten-GigabitEthernet1/0/1]%Jan  4
07:33:26:574 2011 DeviceA IFNET/3/PHY\_UPDOWN: Physical state on the interface Ten-GigabitEthernet1/0/1 changed to up.

%Jan  4 07:33:57:562 2011 DeviceA
DLDP/6/DLDP\_NEIGHBOR\_CONFIRMED: A neighbor was confirmed on interface Ten-GigabitEthernet1/0/1. The neighbor's
system MAC is 0023-8956-3600, and the port index is 162\.

%Jan  4 07:33:57:563 2011 DeviceA
DLDP/6/DLDP\_LINK\_BIDIRECTIONAL: DLDP detected a bidirectional link on interface
Ten-GigabitEthernet1/0/1.

%Jan  4 07:33:57:590 2011 DeviceA
IFNET/5/LINK\_UPDOWN: Line protocol state on the interface Ten-GigabitEthernet1/0/1 changed to up.

%Jan  4 07:33:57:609 2011 DeviceA
STP/6/STP\_DETECTED\_TC: Instance 0's port Ten-GigabitEthernet1/0/1 detected a topology
change.

The output shows that the port status and
link status of Ten-GigabitEthernet 1/0/1 are now up and its
DLDP neighbors are determined.

\# Bring up Ten-GigabitEthernet 1/0/2.

\[DeviceA-Ten-GigabitEthernet1/0/1] quit

\[DeviceA] interface ten-gigabitethernet 1/0/2

\[DeviceA-Ten-GigabitEthernet1/0/2] undo shutdown

The following log information is displayed
on Device A:

\[DeviceA-Ten-GigabitEthernet1/0/2]%Jan  4 07:35:26:574
2011 DeviceA IFNET/3/PHY\_UPDOWN: Physical state on the interface Ten-GigabitEthernet1/0/2 changed to up.

%Jan  4 07:35:57:562 2011 DeviceA
DLDP/6/DLDP\_NEIGHBOR\_CONFIRMED: A neighbor was confirmed on interface Ten-GigabitEthernet1/0/2. The neighbor's
system MAC is 0023-8956-3600, and the port index is 162\.

%Jan  4 07:35:57:563 2011 DeviceA
DLDP/6/DLDP\_LINK\_BIDIRECTIONAL: DLDP detected a bidirectional link on interface
Ten-GigabitEthernet1/0/2.

%Jan  4 07:35:57:590 2011 DeviceA
IFNET/5/LINK\_UPDOWN: Line protocol state on the interface Ten-GigabitEthernet1/0/2 changed to up.

%Jan  4 07:35:57:609 2011 DeviceA
STP/6/STP\_DETECTED\_TC: Instance 0's port Ten-GigabitEthernet1/0/2 detected a topology
change.

The output shows that the port status and
link status of Ten-GigabitEthernet 1/0/2 are now up and its
DLDP neighbors are determined.

 

