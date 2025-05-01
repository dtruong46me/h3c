
# Configuring CFD

## About CFD

Connectivity Fault Detection (CFD), which conforms to IEEE 802.1ag
Connectivity Fault Management (CFM) and ITU-T Y.1731, is an end-to-end per-VLAN
link layer OAM mechanism. CFD is used for link connectivity detection, fault
verification, and fault location.

### Basic CFD concepts

#### Maintenance domain

A maintenance domain (MD) defines the
network or part of the network where CFD plays its role. An MD is identified by
its MD name.

#### Maintenance association

A maintenance association (MA) is a part of
an MD. You can configure multiple MAs in an MD as needed. An MA is identified
by the MD name \+ MA name. 

An MA serves the specified VLAN or no VLAN.
An MA that serves a VLAN is considered to be carrying VLAN attribute. An MA
that serves no VLAN is considered to be carrying no VLAN attribute. 

#### Maintenance point

An MP is configured on a port and belongs
to an MA. MPs include the following types: maintenance association end points
(MEPs) and maintenance association intermediate points (MIPs).

MEPs define the boundary of the MA. Each MEP is identified by a MEP
ID.

MEPs include inward-facing MEPs and
outward-facing MEPs: 

·     An outward-facing MEP sends packets to its host
port. 

·     An inward-facing MEP does not send packets to
its host port. Rather, it sends packets to other ports on the device. 

A MIP is internal to an MA. It cannot send
CFD packets actively, but it can handle and respond to CFD packets. MIPs are
automatically created by the device. By cooperating with MEPs, a MIP can
perform a function similar to ping and traceroute. 

#### MEP list

A MEP list is a collection of local MEPs
allowed to be configured and the remote MEPs to be monitored in the same MA. It
lists all the MEPs configured on different devices in the same MA. The MEPs all
have unique MEP IDs. When a MEP receives from a remote device a continuity
check message (CCM) carrying a MEP ID not in the MEP list of the MA, it drops
the message. 

The local device must send CCM messages
carrying the Remote Defect Indication (RDI) flag bits. Otherwise, the peer
device cannot sense certain failures. When a local MEP has not learned all
remote MEPs in the MEP list, the MEPs in the MA might not carry the RDI flag
bits in CCMs.

### CFD levels

#### MD levels

To accurately locate faults, CFD introduces
eight levels (from 0 to 7\) to MDs. The bigger the number, the higher the level
and the larger the area covered. Domains can touch or nest (if the outer domain
has a higher level than the nested one) but cannot intersect or overlap. 

MD levels facilitate fault location and
make fault location more accurate. As shown in [Figure 1](#_Ref454702808), MD\_A
in light blue nests MD\_B in dark blue. If a connectivity fault is detected at
the boundary of MD\_A, any of the devices in MD\_A, including Device A through
Device E, might fail. If a connectivity fault is also detected at the boundary
of MD\_B, the failure points can be any of Device B through Device D. If the
devices in MD\_B can operate correctly, at least Device C is operational. 

Figure 1 Two nested MDs

![](https://resource.h3c.com/en/202407/12/20240712_11705554_x_Img_x_png_0_2216095_294551_0.png)

 

CFD exchanges messages and performs
operations on a per-domain basis. By planning MDs correctly in a network, you
can use CFD to rapidly locate failure points. 

#### MA and MP levels

The level of an MA equals the level of the
MD to which the MA belongs.

The level of a MEP equals the level of the
MD to which the MEP belongs.

The level of a MIP is defined by its
generation rule and the MD to which the MIP belongs. MIPs are generated on each
port automatically according to the following MIP generation rules:

·     Default rule—If no lower-level MIP exists on an interface, a MIP is created on
the current level. A MIP can be created even if no MEP is configured on the
interface.

·     Explicit rule—If no lower-level MIP exists and a lower-level MEP exists on an
interface, a MIP is created on the current level. A MIP can be created only
when a lower-level MEP is created on the interface.

If a port has no MIP, the system will check
the MAs in each MD (from low to high levels), and follow the procedure as
described in [Figure 2](#_Ref454703041) to
create or not to create MIPs at the current level. 

Figure 2 Procedure of creating MIPs

![](https://resource.h3c.com/en/202407/12/20240712_11705555_x_Img_x_png_1_2216095_294551_0.png)

 

#### CFD grading example

[Figure 3](#_Ref454703113)
demonstrates a grading example of the CFD module. Four levels of MDs (0, 2, 3,
and 5\) are designed. The bigger the number, the higher the level and the larger
the area covered. MPs are configured on the ports of Device A through Device F.
Port A of Device B is configured with the following MPs: 

·     A level 5 MIP. 

·     A level 3 inward-facing MEP. 

·     A level 2 inward-facing MEP. 

·     A level 0 outward-facing MEP. 

Figure 3 CFD grading example

![](https://resource.h3c.com/en/202407/12/20240712_11705556_x_Img_x_png_2_2216095_294551_0.png)

 

### Packet processing of MPs

For an MA carrying VLAN attribute, MPs of
the MA send packets only in the VLAN that the MA serves. The level of packets
sent by an MP equals the level of the MD to which the MP belongs.

For an MA not carrying VLAN attribute, MPs
of the MA can only be outward-facing MEPs. The level of packets sent by an
outward-facing MEP equals the level of the MD to which the MEP belongs.

A MEP forwards packets at a higher level
without any processing and only processes packets of its level or lower.

A MIP forwards packets of a different level
without any processing and only processes packets of its level.

### CFD functions

CFD functions, which are implemented
through the MPs, include:

·     Continuity check (CC).

·     Loopback (LB).

·     Linktrace (LT).

·     Alarm indication signal (AIS).

·     Loss measurement (LM).

·     Delay measurement (DM).

·     Test (TST).

#### Continuity check

Connectivity faults are usually caused by
device faults or configuration errors. Continuity check examines the
connectivity between MEPs. This function is implemented through periodic
sending of CCMs by the MEPs. A CCM sent by one MEP is intended to be received
by all the other MEPs in the same MA. If a MEP fails to receive the CCMs within
3.5 times the sending interval, the link is considered as faulty and a log is
generated. When multiple MEPs send CCMs at the same time, the
multipoint-to-multipoint link check is achieved. CCM frames are multicast
frames. 

#### Loopback

Similar to ping at the IP layer, loopback
verifies the connectivity between a source device and a target device. To
implement this function, the source MEP sends loopback messages (LBMs) to the
target MEP. Depending on whether the source MEP can receive a loopback reply
message (LBR) from the target MEP, the link state between the two can be
verified. 

LBM frames are multicast and unicast
frames. The device can send and receive unicast LBM frames, and can receive
multicast LBM frames but cannot send multicast LBM frames. LBR frames are
unicast frames. 

#### Linktrace

Linktrace is similar to traceroute. It
identifies the path between the source MEP and the target MP. The source MEP
sends the linktrace messages (LTMs) to the target MP. After receiving the
messages, the target MP and the MIPs that the LTM frames pass send back
linktrace reply messages (LTRs) to the source MEP. Based on the reply messages,
the source MEP can identify the path to the target MP. LTM frames are multicast
frames and LTRs are unicast frames. 

#### AIS

The AIS function suppresses the number of
error alarms reported by MEPs. If a local MEP does not receive any CCM frames
from its peer MEP within 3.5 times the CCM transmission interval, it
immediately starts sending AIS frames. The AIS frames are sent periodically in
the opposite direction of CCM frames. When the peer MEP receives the AIS
frames, it suppresses the error alarms locally, and continues to send the AIS
frames. If the local MEP receives CCM frames within 3.5 times the CCM
transmission interval, it stops sending AIS frames and restores the error alarm
function. AIS frames are multicast frames. 

#### LM

The LM function measures the frame loss in
a certain direction between a pair of MEPs. The source MEP sends loss
measurement messages (LMMs) to the target MEP. The target MEP responds with
loss measurement replies (LMRs). The source MEP calculates the number of lost
frames according to the counter values of the two consecutive LMRs (the current
LMR and the previous LMR). LMMs and LMRs are unicast frames. 

#### DM

The DM function measures frame delays
between two MEPs, including the following types: 

·     One-way frame delay measurement

The source MEP sends a one-way delay
measurement (1DM) frame, which carries the transmission time, to the target
MEP. When the target MEP receives the 1DM frame, it does the following: 

¡     Records
the reception time. 

¡     Calculates
and records the link transmission delay and jitter (delay variation) according
to the transmission time and reception time. 

1DM frames are unicast frames. 

·     Two-way frame delay measurement

The source MEP sends a delay measurement
message (DMM), which carries the transmission time, to the target MEP. When the
target MEP receives the DMM, it responds with a delay measurement reply (DMR).
The DMR carries the reception time and transmission time of the DMM and the
transmission time of the DMR. When the source MEP receives the DMR, it does the
following:

¡     Records
the DMR reception time. 

¡     Calculates
the link transmission delay and jitter according to the DMR reception time and
DMM transmission time. 

DMM frames and DMR frames are unicast
frames. 

#### TST

The TST function tests the bit errors
between two MEPs. The source MEP sends a TST frame, which carries the test
pattern, such as pseudo random bit sequence (PRBS) or all-zero, to the target
MEP. When the target MEP receives the TST frame, it determines the bit errors
by calculating and comparing the content of the TST frame. TST frames are
unicast frames. 

### EAIS

Ethernet Alarm Indication Signal (EAIS) enables
collaboration between the Ethernet port status and the AIS function. When a
port on the device (not necessarily an MP) goes down, it immediately starts to
send EAIS frames periodically to suppress the error alarms. When the port goes
up again, it immediately stops sending EAIS frames. When the MEP receives the
EAIS frames, it suppresses the error alarms locally, and continues to send the
EAIS frames. If a MEP receives no EAIS frames within 3.5 times the EAIS frame
transmission interval, the fault is considered cleared. The port stops sending
EAIS frames and restores the error alarm function. EAIS frames are multicast
frames.

### Collaboration between CFD and Track

CFD can collaborate with Track as follows:

·     If CFD detects that the peer is reachable, it
notifies the Track module. The Track module sets the state of the track entry
to Positive. 

·     If CFD detects that the peer is unreachable, it
notifies the Track module. The Track module sets the state of the track entry
to Negative. 

For more information about Track, see
"Configuring Track."

### Protocols and standards

·     IEEE 802.1ag, Virtual
Bridged Local Area Networks Amendment 5: Connectivity Fault Management

·     ITU-T Y.1731, OAM
functions and mechanisms for Ethernet based networks

## Restrictions and guidelines: CFD configuration

When you configure CFD, follow these restrictions
and guidelines:

·     Configure CC before you use the MEP ID of the
remote MEP to configure other CFD functions. This restriction does not apply
when you use the MAC address of the remote MEP to configure other CFD
functions.

·     Do not configure a Layer 2 aggregate interface
as an peer-link interface if the following conditions exist:

¡     The
Layer 2 aggregate interface exists between an inward-facing MEP and a remote
MEP.

¡     The
MAC address of the remote MEP is used for CFD functions.

For information about peer-link interfaces,
see M-LAG configuration in Layer 2—LAN Switching
Configuration Guide.

·     Typically, a port blocked by the spanning tree
feature cannot receive or send CFD messages except in the following cases:

¡     The
port is configured as an outward-facing MEP. 

¡     The
port is configured as a MIP or inward-facing MEP, which can still receive and
send CFD messages except CCM messages. 

For more information about the spanning
tree feature, see Layer 2—LAN Switching Configuration
Guide.

## CFD tasks at a glance

To configure CFD, perform the following
tasks:

**1\.**[Configuring basic
CFD settings](#_Ref231803863)

**a.**[Enabling CFD](#_Ref231803869)

**b.**[Configuring service instances](#_Ref502771002)

**c.**[Configuring MEPs](#_Ref231805283)

**d.**[Configuring MIP
auto-generation rules](#_Ref164676626)

**2\.**[Configuring CFD
functions](#_Ref231803881)

**a.**[Configuring CC](#_Ref231803892)

**b.**(Optional.) [Configuring LB](#_Ref372114855)

**c.**(Optional.) [Configuring LT](#_Ref164676810)

**d.**(Optional.) [Configuring AIS](#_Ref371502068)

**e.**(Optional.) [Configuring LM](#_Ref371502073)

**f.**(Optional.) [Configuring one-way DM](#_Ref371502076)

**g.**(Optional.) [Configuring two-way DM](#_Ref371502079)

**h.**(Optional.) [Configuring TST](#_Ref371502090)

**3\.**(Optional.) [Configuring EAIS](#_Ref260240087)

## Prerequisites for CFD

For CFD to work correctly, design the
network by performing the following tasks:

·     Grade the MDs in the entire network, and define
the boundary of each MD.

·     Assign a name for each MD. Make sure the devices
in the same MD use the same MD name. 

·     Define the MA in each MD according to the VLAN
you want to monitor.

·     Assign a name for each MA. Make sure that the
devices in the same MA in the same MD use the same MA name.

·     Determine the MEP list of each MA in each MD.
Make sure devices in the same MA maintain the same MEP list. 

·     At the edges of MD and MA, MEPs must be designed
at the device port. MIPs can be designed on devices or ports that are not at
the edges.

## Configuring basic CFD settings

### Enabling CFD

**1\.**Enter system view.

system-view

**2\.**Enable CFD.

cfd enable

By default, CFD is disabled.

### Configuringservice instances

#### About this task

Before configuring the MEPs and MIPs, you
must first configure service instances. A service instance is a set of service
access points (SAPs), and belongs to an MA in an MD. 

The MD and MA define the level attribute
and VLAN attribute of the messages handled by the MPs in a service instance.
The MPs of the MA that carries no VLAN attribute do not belong to any VLAN. 

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Create an MD.

cfd md md-name \[ index index-value ] level level-value \[ md-id { dns dns-name \| mac mac-address subnumber \| none } ]

**3\.**Create a service instance.

cfd service-instance instance-id ma-id { icc-based ma-name \| integer ma-num \| string ma-name \| vlan-based \[ vlan-id ] } \[ ma-index index-value ] md md-name \[ vlan vlan-id ]

### Configuring MEPs

#### About this task

CFD is implemented through various
operations on MEPs. As a MEP is configured on a service instance, the MD level
and VLAN attribute of the service instance become the attribute of the MEP.

#### Restrictions and guidelines

·     You can specify an interface as the MEP for only
one of the non-VLAN-specific MAs at the same level. In addition, the MEP must
be outward facing.

·     If a MEP in a non-VLAN-specific MA does not
receive a CCM message within 3.5 CCM intervals from a remote MEP, the local MEP
sets its interface to link down state. This behavior of the local MEP
facilitates fast switchover for RRPP or Smart Link.

#### Prerequisites

Before you configure MEPs, you must configure
service instances.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Configure a MEP list.

cfd meplist mep-list
service-instance instance-id

The created MEP must be included in the
configured MEP list.

**3\.**Enter Layer 2 Ethernet interface view or
Layer 2 aggregate interface view.

interface interface-type
interface-number

**4\.**Create a MEP.

cfd mep mep-id service-instance
instance-id { inbound \| outbound }

### Configuring MIP auto-generation rules

#### About this task

As functional entities in a service
instance, MIPs respond to various CFD frames, such as LTM and LBM frames. You
can configure MIP auto-generation rules for the system to automatically create
MIPs.

Any of the following events can cause MIPs
to be created or deleted after you have configured the cfd mip-rule command:

·     Enabling or disabling CFD.

·     Creating or deleting MEPs on a port.

·     Changes occur to the VLAN attribute of a port.

·     The rule specified in the cfd mip-rule command changes.

#### Restrictions and guidelines

·     An MA carrying no VLAN attribute is typically used
to detect direct link status. The system cannot generate MIPs for such MAs. 

·     For an MA carrying VLAN attribute, the system
does not generate MIPs if the same or a higher level MEP exists on the
interface. 

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Configure MIP auto-generation rules.

cfd mip-rule { default \| explicit }
service-instance instance-id

By default, no rules for generating MIPs
are configured, and the system does not automatically create any MIP.

## Configuring CFD functions

### ConfiguringCC

#### About this task

After the CC function is configured, MEPs
in an MA can periodically send CCM frames to maintain connectivity. 

You must configure CC before you use the
MEP ID of the remote MEP to configure other CFD functions. This restriction
does not apply when you use the MAC address of the remote MEP to configure other
CFD functions.

When the lifetime of a CCM frame expires,
the link to the sending MEP is considered disconnected. When setting the CCM
interval, use the settings described in [Table 1](#_Ref341370384).

Table 1 CCM interval field encoding

| CCM interval field | Transmission interval | Maximum CCM lifetime |
| --- | --- | --- |
| 1 | 10/3 milliseconds | 35/3 milliseconds || 2 | 10 milliseconds | 35 milliseconds || 3 | 100 milliseconds | 350 milliseconds || 4 | 1 second | 3.5 seconds || 5 | 10 seconds | 35 seconds || 6 | 60 seconds | 210 seconds || 7 | 600 seconds | 2100 seconds |







 

|  | NOTE:  ·     The value range for the interval field is 1 to 7\. If you set the value to 1 or 2, the continuity check might work incorrectly due to hardware restrictions.  ·     The CCM messages with an interval field value of 1 to 3 are short-interval CCM messages. The CCM messages with an interval field value of 4 to 7 are long-interval CCM messages. |
| --- | --- |

 

#### Restrictions and guidelines

When you configure the CCM interval, follow
these restrictions and guidelines: 

·     Configure the same CCM interval field value for
all MEPs in the same MA. 

·     After the CCM interval field is modified, the
MEP must wait for another CCM interval before sending CCMs. 

·     If the device cannot process short-interval CCM
messages, setting the CCM interval field value to smaller than 4 might cause the
CC function to operate unsteadily.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**(Optional.) Set the CCM interval field.

cfd cc interval interval-value
service-instance instance-id

By default, the interval field value is
4\.

**3\.**Enter Layer 2 Ethernet interface view or
Layer 2 aggregate interface view.

interface interface-type
interface-number

**4\.**Enable CCM sending on a MEP.

cfd cc service-instance instance-id mep mep-id enable

By default, CCM sending is disabled on a
MEP.

### Configuring LB

To verify the link state between the local
MEP and the remote MEP, execute the following command in any view:

cfd loopback service-instance instance-id mep mep-id { target-mac mac-address \| target-mep target-mep-id } \[ number number ]

### Configuring LT

#### About this task

LT can trace the path between source and
target MEPs, and can locate link faults by automatically sending LT messages.
The two functions are implemented in the following way:

·     Tracing path—The source MEP first sends LTM messages to the target MEP. Based on
the LTR messages in response to the LTM messages, the path between the two MEPs
is identified. 

·     LT messages automatic sending—If the source MEP fails to receive CCM frames from the target MEP
within 3.5 times the transmission interval, it considers the link faulty. The
source MEP then sends LTM frames, with the TTL field set to the maximum value
255, to the target MEP. Based on the returned LTRs, the fault source is
located.

#### Prerequisites

Before you configure LT on a MEP in an MA carrying
VLAN attribute, create the VLAN to which the MA belongs.

#### Procedure

**1\.**Identify the path between a source MEP and a
target MEP.

cfd linktrace service-instance instance-id mep mep-id { target-mac mac-address \| target-mep target-mep-id } \[ ttl ttl-value ] \[ hw-only ]

This command is available in any view.

**2\.**Enter system view.

system-view

**3\.**Enable LT messages automatic sending.

cfd linktrace auto-detection \[ size size-value ]

By default, LT messages automatic sending
is disabled.

### Configuring AIS

#### About this task

The AIS function suppresses the number of
error alarms reported by MEPs. 

#### Restrictions and guidelines

If you enable AIS, the MEPs in a service
instance cannot send AIS frames in the following conditions:

·     No AIS frame transmission level is configured.

·     The AIS frame transmission level is lower than
the MD level of the service instance.

If you enable AIS and configure an AIS
frame transmission level equal to the MD level of a service instance, the MEPs
in the service instance:

·     Can suppress error alarms.

·     Cannot send AIS frames to MDs with higher level.


#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enable AIS.

cfd ais enable

By default, AIS is disabled.

**3\.**Configure the AIS frame transmission level.

cfd ais level level-value
service-instance instance-id

By default, the AIS frame transmission
level is not configured.

The AIS frame transmission level must be
higher than the MD level of the service instance.

**4\.**Configure the AIS frame transmission
interval.

cfd ais period period-value
service-instance instance-id

By default, the AIS frame transmission
interval is 1 second.

### Configuring LM

#### About this task

The LM function measures frame loss between
MEPs. Frame loss statistics include the number of lost frames, the frame loss
ratio, and the average number of lost frames for the source and target MEPs. 

#### Procedure

To configure LM, execute the following
command in any view:

cfd slm service-instance instance-id mep mep-id { target-mac mac-address \| target-mep target-mep-id } \[ dot1p dot1p-value ] \[ number number ] \[ interval interval ]

### Configuring one-way DM

#### About this task

The one-way DM function measures the
one-way frame delay between two MEPs, and monitors and manages the link
transmission performance. 

#### Restrictions and guidelines

Follow these guidelines when you configure one-way
DM on a MEP:

·     One-way DM requires that the time setting at the
transmitting MEP and the receiving MEP be the same. For the purpose of frame
delay variation measurement, the requirement can be relaxed. 

·     To view the test result, use the display cfd dm
one-way history command on the target MEP.

#### Procedure

To configure one-way DM, execute the
following command in any view:

cfd dm one-way service-instance instance-id mep mep-id { target-mac mac-address \| target-mep target-mep-id } \[ number number ]

### Configuring two-way DM

#### About this task

The two-way DM function measures the
two-way frame delay, average two-way frame delay, and two-way frame delay
variation between two MEPs. It also monitors and manages the link transmission
performance. 

#### Procedure

To configure two-way DM, execute the
following command in any view:

cfd dm two-way service-instance instance-id mep mep-id { target-mac mac-address \| target-mep target-mep-id } dot1p dot1p-value ] \[ number number ] \[ interval interval ]

### Configuring TST

#### About this task

The TST function detects bit errors on a
link, and monitors and manages the link transmission performance. 

#### Restrictions and guidelines

To view the test result, use the display cfd tst command on the target MEP. 

#### Procedure

To configure TST, execute the following
command in any view:

cfd tst service-instance instance-id mep mep-id { target-mac mac-address \| target-mep target-mep-id } \[ number number ] \[ length-of-test length ] \[ pattern-of-test
{ all-zero \| prbs } \[ with-crc ] ]

## Configuring EAIS

#### Restrictions and guidelines

Follow these guidelines when you configure EAIS
on a MEP:

·     You can configure EAIS on a device that does not
support or is not configured with CFD. However, EAIS must collaborate with the
CFD function in the network, so you must configure CFD in the network. 

·     You can configure EAIS on the member port of an
aggregation group, but the configuration does not take effect. If you configure
EAIS on the port and then add it to an aggregation group, the EAIS
configuration immediately fails to take effect. After the port leaves the
aggregation group, the EAIS configuration takes effect. 

·     If the intersection of the configured VLANs
where the EAIS frames can be transmitted and the VLANs to which the port
belongs is empty, no EAIS frame is sent. If the intersection contains more than
70 VLANs and the EAIS frame transmission interval is 1 second, the CPU usage
will be too high. As a best practice, set the EAIS frame transmission interval
to 60 seconds in this case. 

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enable port status-AIS collaboration.

cfd ais-track link-status global

By default, port status-AIS collaboration
is disabled.

**3\.**Enter Layer 2 Ethernet interface view or
Layer 2 aggregate interface view.

interface interface-type
interface-number

**4\.**Configure the EAIS frame transmission level.

cfd ais-track link-status level level-value

By default, the EAIS frame transmission
level is not configured.

**5\.**Configure the EAIS frame transmission
interval.

cfd ais-track link-status period period-value

By default, the EAIS frame transmission
interval is not configured.

**6\.**Specify the VLANs where the EAIS frames can
be transmitted.

cfd ais-track link-status vlan vlan-list

By default, the EAIS frames can only be
transmitted within the default VLAN of the port.

The EAIS frames are transmitted within
the intersection of the VLANs specified with this command and the existing VLANs
on the device.

## Display and maintenance commands forCFD

Execute display
commands in any view and reset commands in user
view.

 

| Task | Command |
| --- | --- |
| Display the AIS configuration and information on the specified MEP. | display cfd ais \[ service-instance instance-id \[ mep mep-id ] ] || Display the AIS configuration and information associated with the status of the specified port. | display cfd ais-track link-status \[ interface interface-type interface-number ] || Display the one-way DM result on the specified MEP. | display cfd dm one-way history \[ service-instance instance-id \[ mep mep-id ] ] || Display LTR information received by a MEP. | display cfd linktrace-reply \[ service-instance instance-id \[ mep mep-id ] ] || Display the content of the LTR messages received as responses to the automatically sent LTMs. | display cfd linktrace-reply auto-detection \[ size size-value ] || Display MD configuration information. | display cfd md || Display the attribute and running information of the MEPs. | display cfd mep mep-id service-instance instance-id || Display MEP lists in a service instance. | display cfd meplist \[ service-instance instance-id ] || Display MP information. | display cfd mp \[ interface interface-type interface-number ] || Display information about a remote MEP. | display cfd remote-mep service-instance instance-id mep mep-id || Display service instance configuration. | display cfd service-instance \[ instance-id ] || Display CFD status. | display cfd status || Display the TST result on the specified MEP. | display cfd tst \[ service-instance instance-id \[ mep mep-id ] ] || Clear the one-way DM result on the specified MEP. | reset cfd dm one-way history \[ service-instance instance-id \[ mep mep-id ] ] || Clear the TST result on the specified MEP. | reset cfd tst \[ service-instance instance-id \[ mep mep-id ] ] |















 

## CFD configuration examples

### Example: Configuring CFD

#### Network configuration

As shown in [Figure 4](#_Ref145911060):

·     The network comprises five devices and is
divided into two MDs: MD\_A (level 5\) and MD\_B (level 3). All ports belong to
VLAN 100, and the MAs in the two MDs all serve VLAN 100\. Assume that the MAC
addresses of Device A through Device E are 0010-FC01-6511, 0010-FC02-6512,
0010-FC03-6513, 0010-FC04-6514, and 0010-FC05-6515, respectively. 

·     MD\_A has three edge ports: Ten-GigabitEthernet 1/0/1 on Device A, Ten-GigabitEthernet 1/0/3 on Device D, and Ten-GigabitEthernet 1/0/4 on Device E. They
are all inward-facing MEPs. MD\_B has two edge ports: Ten-GigabitEthernet 1/0/3 on Device B and Ten-GigabitEthernet 1/0/1 on Device D. They
are both outward-facing MEPs.

·     In MD\_A, Device B is designed to have MIPs when
its port is configured with low level MEPs. Port Ten-GigabitEthernet 1/0/3 is configured with
MEPs of MD\_B, and the MIPs of MD\_A can be configured on this port. You must
configure the MIP generation rule of MD\_A as explicit.

·     The MIPs of MD\_B are designed on Device C, and
are configured on all ports. You must configure the MIP generation rule as
default.

·     Configure CC to monitor the connectivity among
all the MEPs in MD\_A and MD\_B. Configure LB to locate link faults, and use the
AIS and EAIS functions to suppress the error alarms that are reported. 

·     After the status information of the entire
network is obtained, use LT, LM, one-way DM, two-way DM, and TST to detect link
faults.

Figure 4 Network
diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705557_x_Img_x_png_3_2216095_294551_0.png)

‌

#### Procedure

**1\.**Configure a VLAN and assign ports to it:

On each device shown in [Figure 4](#_Ref145911060), create
VLAN 100 and assign ports Ten-GigabitEthernet 1/0/1 through Ten-GigabitEthernet 1/0/4 to VLAN 100\.

**2\.**Enable CFD:

\# Enable CFD on Device A.

\<DeviceA\> system-view

\[DeviceA] cfd enable

\# Configure Device B through Device E in
the same way Device A is configured. (Details not shown.)

**3\.**Configure service instances:

\# Create MD\_A (level 5\) on Device A, and
create service instance 1 (in which the MA is identified by a VLAN and serves
VLAN 100).

\[DeviceA] cfd md MD\_A level 5

\[DeviceA] cfd service-instance
1 ma-id vlan-based md MD\_A vlan 100

\# Configure Device E in the same way Device
A is configured. (Details not shown.) 

\# Create MD\_A (level 5\) on Device B, and
create service instance 1 (in which the MA is identified by a VLAN and serves
VLAN 100). 

\[DeviceB] cfd md MD\_A level 5

\[DeviceB] cfd service-instance
1 ma-id vlan-based md MD\_A vlan 100

\# Create MD\_B (level 3), and create service
instance 2 (in which the MA is identified by a VLAN and serves VLAN 100).

\[DeviceB] cfd md MD\_B level 3

\[DeviceB] cfd service-instance
2 ma-id vlan-based md MD\_B vlan 100

\# Configure Device D in the same way Device
B is configured. (Details not shown.) 

\# Create MD\_B (level 3\) on Device C, and
create service instance 2 (in which the MA is identified by a VLAN and serves
VLAN 100).

\[DeviceC] cfd md MD\_B level 3

\[DeviceC] cfd service-instance
2 ma-id vlan-based md MD\_B vlan 100

**4\.**Configure MEPs:

\# On Device A, configure a MEP list in service
instance 1, and create inward-facing MEP 1001 in service instance 1 on Ten-GigabitEthernet 1/0/1. 

\[DeviceA] cfd meplist 1001
4002 5001 service-instance 1

\[DeviceA] interface ten-gigabitethernet 1/0/1

\[DeviceA-Ten-GigabitEthernet1/0/1] cfd mep 1001
service-instance 1 inbound

\[DeviceA-Ten-GigabitEthernet1/0/1] quit

\# On Device B, configure a MEP list in service
instances 1 and 2\.

\[DeviceB] cfd meplist 1001
4002 5001 service-instance 1

\[DeviceB] cfd meplist 2001
4001 service-instance 2

\# Create outward-facing MEP 2001 in service
instance 2 on Ten-GigabitEthernet 1/0/3. 

\[DeviceB] interface ten-gigabitethernet 1/0/3

\[DeviceB-Ten-GigabitEthernet1/0/3] cfd mep 2001
service-instance 2 outbound

\[DeviceB-Ten-GigabitEthernet1/0/3] quit

\# On Device D, configure a MEP list in service
instances 1 and 2\.

\[DeviceD] cfd meplist 1001
4002 5001 service-instance 1

\[DeviceD] cfd meplist 2001
4001 service-instance 2

\# Create outward-facing MEP 4001 in service
instance 2 on Ten-GigabitEthernet 1/0/1.

\[DeviceD] interface ten-gigabitethernet 1/0/1

\[DeviceD-Ten-GigabitEthernet1/0/1] cfd mep 4001
service-instance 2 outbound

\[DeviceD-Ten-GigabitEthernet1/0/1] quit

\# Create inward-facing MEP 4002 in service
instance 1 on Ten-GigabitEthernet 1/0/3.

\[DeviceD] interface ten-gigabitethernet 1/0/3

\[DeviceD-Ten-GigabitEthernet1/0/3] cfd mep 4002
service-instance 1 inbound

\[DeviceD-Ten-GigabitEthernet1/0/3] quit

\# On Device E, configure a MEP list in service
instance 1\.

\[DeviceE] cfd meplist 1001
4002 5001 service-instance 1

\# Create inward-facing MEP 5001 in service
instance 1 on Ten-GigabitEthernet 1/0/4.

\[DeviceE] interface ten-gigabitethernet 1/0/4

\[DeviceE-Ten-GigabitEthernet1/0/4] cfd mep 5001
service-instance 1 inbound

\[DeviceE-Ten-GigabitEthernet1/0/4] quit

**5\.**Configure MIPs:

\# Configure the MIP generation rule in service
instance 1 on Device B as explicit. 

\[DeviceB] cfd mip-rule
explicit service-instance 1

\# Configure the MIP generation rule in service
instance 2 on Device C as default.

\[DeviceC] cfd mip-rule default
service-instance 2

**6\.**Configure CC:

\# On Device A, enable the sending of CCM
frames for MEP 1001 in service instance 1 on Ten-GigabitEthernet 1/0/1.

\[DeviceA] interface ten-gigabitethernet 1/0/1

\[DeviceA-Ten-GigabitEthernet1/0/1] cfd cc
service-instance 1 mep 1001 enable

\[DeviceA-Ten-GigabitEthernet1/0/1] quit

\# On Device B, enable the sending of CCM
frames for MEP 2001 in service instance 2 on Ten-GigabitEthernet 1/0/3.

\[DeviceB] interface ten-gigabitethernet 1/0/3

\[DeviceB-Ten-GigabitEthernet1/0/3] cfd cc
service-instance 2 mep 2001 enable

\[DeviceB-Ten-GigabitEthernet1/0/3] quit

\# On Device D, enable the sending of CCM
frames for MEP 4001 in service instance 2 on Ten-GigabitEthernet 1/0/1.

\[DeviceD] interface ten-gigabitethernet 1/0/1

\[DeviceD-Ten-GigabitEthernet1/0/1] cfd cc
service-instance 2 mep 4001 enable

\[DeviceD-Ten-GigabitEthernet1/0/1] quit

\# Enable the sending of CCM frames for MEP
4002 in service instance 1 on Ten-GigabitEthernet 1/0/3. 

\[DeviceD] interface ten-gigabitethernet 1/0/3

\[DeviceD-Ten-GigabitEthernet1/0/3] cfd cc
service-instance 1 mep 4002 enable

\[DeviceD-Ten-GigabitEthernet1/0/3] quit

\# On Device E, enable the sending of CCM
frames for MEP 5001 in service instance 1 on Ten-GigabitEthernet 1/0/4.

\[DeviceE] interface ten-gigabitethernet 1/0/4

\[DeviceE-Ten-GigabitEthernet1/0/4] cfd cc service-instance
1 mep 5001 enable

\[DeviceE-Ten-GigabitEthernet1/0/4] quit

**7\.**Configure AIS:

\# Enable AIS on Device B. Configure the
AIS frame transmission level as 5 and AIS frame transmission interval as 1
second in service instance 2\. 

\[DeviceB] cfd ais enable

\[DeviceB] cfd ais level 5
service-instance 2

\[DeviceB] cfd ais period 1
service-instance 2

**8\.**Configure EAIS:

\# Enable port status-AIS collaboration on
Device B. 

\[DeviceB] cfd ais-track
link-status global

\# On Ten-GigabitEthernet 1/0/3 of Device B,
configure the EAIS frame transmission level as 5 and the EAIS frame
transmission interval as 60 seconds. Specify the VLANs where the EAIS frames
can be transmitted as VLAN 100\. 

\[DeviceB] interface ten-gigabitethernet 1/0/3

\[DeviceB-Ten-GigabitEthernet1/0/3] cfd ais-track link-status
level 5

\[DeviceB-Ten-GigabitEthernet1/0/3] cfd ais-track
link-status period 60

\[DeviceB-Ten-GigabitEthernet1/0/3] cfd ais-track
link-status vlan 100

\[DeviceB-Ten-GigabitEthernet1/0/3] quit

#### Verifying the configuration

**1\.**Verify the LB function when the CC function
detects a link fault:

\# Enable LB on Device A to check the
status of the link between MEP 1001 and MEP 5001 in service instance 1\. 

\[DeviceA] cfd loopback
service-instance 1 mep 1001 target-mep 5001

Loopback to MEP 5001 with the
sequence number start from 1001-43404:

Reply from 0010-fc05-6515:
sequence number\=1001-43404 time\=5ms

Reply from 0010-fc05-6515:
sequence number\=1001-43405 time\=5ms

Reply from 0010-fc05-6515:
sequence number\=1001-43406 time\=5ms

Reply from 0010-fc05-6515:
sequence number\=1001-43407 time\=5ms

Reply from 0010-fc05-6515:
sequence number\=1001-43408 time\=5ms

Sent: 5        Received:
5        Lost: 0

**2\.**Verify the LT function after the CC function
obtains the status information of the entire network:

\# Identify the path between MEP 1001 and
MEP 5001 in service instance 1 on Device A.

\[DeviceA] cfd linktrace
service-instance 1 mep 1001 target-mep 5001

Linktrace to MEP 5001 with the
sequence number 1001-43462:

MAC address              
TTL     Last MAC         Relay action

0010-fc05-6515           
63      0010-fc02-6512   Hit

**3\.**Verify the LM function after the CC function
obtains the status information of the entire network:

\# Test the frame loss from MEP 1001 to
MEP 4002 in service instance 1 on Device A. 

\[DeviceA] cfd slm
service-instance 1 mep 1001 target-mep 4002

Reply from 0010-fc04-6514

Far-end frame loss: 10    Near-end
frame loss: 20

Reply from 0010-fc04-6514

Far-end frame loss: 40    Near-end
frame loss: 40

Reply from 0010-fc04-6514

Far-end frame loss: 0     Near-end
frame loss: 10

Reply from 0010-fc04-6514

Far-end frame loss: 30    Near-end
frame loss: 30

 

Average

Far-end frame loss: 20   
Near-end frame loss: 25

Far-end frame loss rate:
25.00%    Near-end frame loss rate: 32.00%

Send LMMs: 5        Received:
5        Lost: 0

**4\.**Verify the one-way DM function after the CC
function obtains the status information of the entire network:

\# Test the one-way frame delay from MEP
1001 to MEP 4002 in service instance 1 on Device A. 

\[DeviceA] cfd dm one-way
service-instance 1 mep 1001 target-mep 4002

5 1DMs have been sent. Please
check the result on the remote device.

\# Display the one-way DM result on MEP
4002 in service instance 1 on Device D. 

\[DeviceD] display cfd dm
one-way history service-instance 1 mep 4002

Service instance: 1

MEP ID: 4002

Sent 1DM total number: 0

Received 1DM total number: 5

Frame delay: 10ms 9ms 11ms 5ms
5ms

Delay average: 8ms

Delay variation: 5ms 4ms 6ms
0ms 0ms

Variation average: 3ms

**5\.**Verify the two-way DM function after the CC
function obtains the status information of the entire network:

\# Test the two-way frame delay from MEP
1001 to MEP 4002 in service instance 1 on Device A. 

\[DeviceA] cfd dm two-way
service-instance 1 mep 1001 target-mep 4002

Frame delay:

Reply from 0010-fc04-6514: 2406us

Reply from 0010-fc04-6514: 2215us

Reply from 0010-fc04-6514: 2112us

Reply from 0010-fc04-6514: 1812us

Reply from 0010-fc04-6514: 2249us

Average: 2158us 

Sent DMMs: 5        Received:
5        Lost: 0

 

Frame delay variation: 191us 103us
300us 437us

Average: 257us

**6\.**Verify the TST function after the CC
function obtains the status information of the entire network:

\# Test the bit errors on the link from
MEP 1001 to MEP 4002 in service instance 1 on Device A. 

\[DeviceA] cfd tst
service-instance 1 mep 1001 target-mep 4002

5 TSTs have been sent. Please
check the result on the remote device.

\# Display the TST result on MEP 4002 in service
instance 1 on Device D. 

\[DeviceD] display cfd tst
service-instance 1 mep 4002

Service instance: 1

MEP ID: 4002

Sent TST total number: 0

Received TST total number: 5

Received from 0010-fc01-6511,
Bit True,  sequence number 0

Received from 0010-fc01-6511,
Bit True,  sequence number 1

Received from 0010-fc01-6511,
Bit True,  sequence number 2

Received from 0010-fc01-6511,
Bit True,  sequence number 3

Received from 0010-fc01-6511,
Bit True,  sequence number 4

 

 

