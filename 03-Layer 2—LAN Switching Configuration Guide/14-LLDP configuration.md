
# Configuring LLDP

## About LLDP

The Link Layer Discovery Protocol (LLDP) is
a standard link layer protocol that allows network devices from different
vendors to discover neighbors and exchange system and configuration information.

In an LLDP-enabled network, a device advertises
local device information in LLDP Data Units (LLDPDUs) to the directly connected
devices. The information distributed through LLDP is stored by its recipients
in standard MIBs, making it possible for the information to be accessed by a
Network Management System (NMS) through SNMP.

Information that can be distributed through
LLDP includes (but is not limited to):

·     Major capabilities of the system.

·     Management IP address of the system.

·     Device ID.

·     Port ID.

### LLDP agents and bridge modes

An LLDP agent is a mapping of a protocol entity
that implements LLDP. Multiple LLDP agents can run on the same interface. 

LLDP agents are classified into the
following types: 

·     Nearest bridge agent.

·     Nearest customer bridge agent.

·     Nearest non-TPMR bridge agent.

A Two-port MAC Relay (TPMR) is a type of
bridge that has only two externally-accessible bridge ports. It supports a
subset of the features of a MAC bridge. A TPMR is transparent to all
frame-based media-independent protocols except for the following protocols:

¡     Protocols
destined for the TPMR.

¡     Protocols
destined for reserved MAC addresses that the relay feature of the TPMR is configured
not to forward.

LLDP exchanges packets between neighbor
agents and creates and maintains neighbor information for them. [Figure 1](#_Ref336509596)
shows the neighbor relationships for these LLDP agents. 

Figure 1 LLDP neighbor relationships

![](https://resource.h3c.com/en/202407/12/20240712_11704448_x_Img_x_png_0_2215984_294551_0.png)

 

The types of supported LLDP agents vary
with the bridge mode in which LLDP operates. LLDP supports the following bridge
modes: customer bridge (CB) and service bridge (SB).

·     Customer bridge mode—LLDP supports nearest bridge agent, nearest non-TPMR bridge agent,
and nearest customer bridge agent. LLDP processes the LLDP frames with
destination MAC addresses for these agents and transparently transmits the LLDP
frames with other destination MAC addresses in VLANs.

·     Service bridge mode—LLDP supports nearest bridge agent and nearest non-TPMR bridge
agent. LLDP processes the LLDP frames with destination MAC addresses for these
agents and transparently transmits the LLDP frames with other destination MAC
addresses in VLANs.

### LLDP frame formats

LLDP sends device information in LLDP
frames. LLDP frames are encapsulated in Ethernet II or Subnetwork Access
Protocol (SNAP) format.

#### LLDP frame encapsulated in Ethernet II

Figure 2 Ethernet II-encapsulated LLDP frame

![](https://resource.h3c.com/en/202407/12/20240712_11704449_x_Img_x_png_1_2215984_294551_0.png)

 

Table 1 Fields in an Ethernet II-encapsulatedLLDP frame

| Field | Description |
| --- | --- |
| Destination MAC address | MAC address to which the LLDP frame is advertised. LLDP specifies different multicast MAC addresses as destination MAC addresses for LLDP frames destined for agents of different types. This helps distinguish between LLDP frames sent and received by different agent types on the same interface. The destination MAC address is fixed to one of the following multicast MAC addresses:  ·     0x0180-c200-000E for LLDP frames destined for nearest bridge agents.  ·     0x0180-c200-0000 for LLDP frames destined for nearest customer bridge agents.  ·     0x0180-c200-0003 for LLDP frames destined for nearest non-TPMR bridge agents. || Source MAC address | MAC address of the sending port. || Type | Ethernet type for the upper-layer protocol. This field is 0x88CC for LLDP. || Data | LLDPDU. || FCS | Frame check sequence, a 32-bit CRC value used to determine the validity of the received Ethernet frame. |





 

#### LLDP frame encapsulated in SNAP

Figure 3 SNAP-encapsulated LLDP frame

![](https://resource.h3c.com/en/202407/12/20240712_11704450_x_Img_x_png_2_2215984_294551_0.png)

 

Table 2 Fields in a SNAP-encapsulatedLLDP frame

| Field | Description |
| --- | --- |
| Destination MAC address | MAC address to which the LLDP frame is advertised. It is the same as that for Ethernet II-encapsulated LLDP frames. || Source MAC address | MAC address of the sending port. || Type | SNAP type for the upper-layer protocol. This field is 0xAAAA-0300-0000-88CC for LLDP. || Data | LLDPDU. || FCS | Frame check sequence, a 32-bit CRC value used to determine the validity of the received Ethernet frame. |





 

### LLDPDUs

Each LLDP frame contains one LLDPDU. Each
LLDPDU is a sequence of type-length-value (TLV) structures.

Figure 4 LLDPDU encapsulation
format

![](https://resource.h3c.com/en/202407/12/20240712_11704451_x_Img_x_png_3_2215984_294551_0.png)

 

As shown in [Figure 4](#_Ref224965243),
each LLDPDU starts with the following mandatory TLVs: Chassis ID TLV, Port ID
TLV, and Time to Live TLV. The mandatory TLVs are followed by a maximum of 29 optional
TLVs.

### TLVs

A TLV is an information element that contains
the type, length, and value fields.

LLDPDU TLVs include the following categories:


·     Basic management TLVs.

·     Organizationally (IEEE 802.1 and IEEE 802.3)
specific TLVs.

·     LLDP-MED (media endpoint discovery) TLVs.

Basic management TLVs are essential to
device management.

Organizationally specific TLVs and LLDP-MED
TLVs are used for enhanced device management. They are defined by standardization
or other organizations and are optional for LLDPDUs.

#### Basic management TLVs

[Table 3](#_Ref277335545) lists
the basic management TLV types. Some of them are mandatory for LLDPDUs.

Table 3 Basic management TLVs

| Type | Description | Remarks |
| --- | --- | --- |
| Chassis ID | Specifies the bridge MAC address of the sending device. | Mandatory. || Port ID | Specifies the ID of the sending port:  ·     If the LLDPDU carries LLDP-MED TLVs, the port ID TLV carries the MAC address of the sending port.  ·     Otherwise, the port ID TLV carries the port name. || Time to Live | Specifies the life of the transmitted information on the receiving device. || End of LLDPDU | Marks the end of the TLV sequence in the LLDPDU. || Port Description | Specifies the description for the sending port. | Optional. || System Name | Specifies the assigned name of the sending device. || System Description | Specifies the description for the sending device. || System Capabilities | Identifies the primary features of the sending device and the enabled primary features. || Management Address | Specifies the following elements:  ·     The management address of the local device.  ·     The interface number and object identifier (OID) associated with the address. |









 

#### IEEE 802.1 organizationally specific TLVs

[Table 4](#_Ref482026788)
lists the IEEE 802.1 organizationally specific TLVs. 

The device can receive protocol identity
TLVs and VID usage digest TLVs, but it cannot send these TLVs.

Layer 3 Ethernet ports support only link
aggregation TLVs.

Table 4 IEEE 802.1 organizationally specific
TLVs

| Type | Description |
| --- | --- |
| Port VLAN ID (PVID) | Specifies the port PVID. || Port And Protocol VLAN ID (PPVID) | Indicates whether the device supports protocol VLANs and, if so, what VLAN IDs these protocols will be associated with. || VLAN Name | Specifies the textual name of any VLAN to which the port belongs. || Protocol Identity | Indicates protocols supported on the port. || DCBX | Data center bridging exchange protocol.  DCBX TLVs are not supported in the current software version. || EVB module | Edge Virtual Bridging module, including EVB TLV and CDCP TLV.  EVB module TLVs are not supported in the current software version. || Link Aggregation | Indicates whether the port supports link aggregation, and if yes, whether link aggregation is enabled. || Management VID | Management VLAN ID. || VID Usage Digest | VLAN ID usage digest. || ETS Configuration | Enhanced Transmission Selection configuration.  ETS configuration TLVs are not supported in the current software version. || ETS Recommendation | ETS recommendation.  ETS recommendation TLVs are not supported in the current software version. || PFC | Priority-based Flow Control.  PFC TLVs are not supported in the current software version. || APP | Application protocol.  APP TLVs are not supported in the current software version. || QCN | Quantized Congestion Notification.  QCN TLVs are not supported in the current software version. |














‌

#### IEEE 802.3 organizationally specific TLVs

[Table 5](#_Ref482027037)
shows the IEEE 802.3 organizationally specific TLVs.

The Power Stateful Control TLV is defined
in IEEE P802.3at D1.0 and is not supported in later versions. The device sends
this type of TLVs only after receiving them.

Table 5 IEEE 802.3 organizationally specific
TLVs

| Type | Description |
| --- | --- |
| MAC/PHY Configuration/Status | Contains the bit-rate and duplex capabilities of the port, support for autonegotiation, enabling status of autonegotiation, and the current rate and duplex mode. || Link Aggregation | Indicates whether the port supports link aggregation, and if yes, whether link aggregation is enabled. || Power Via MDI | Contains the power supply capabilities of the port:  ·     Port class (PSE or PD).  ·     Power supply mode.  ·     Whether PSE power supply is supported.  ·     Whether PSE power supply is enabled.  ·     Whether pair selection can be controlled.  ·     Power supply type.  ·     Power source.  ·     Power priority.  ·     PD requested power.  ·     PSE allocated power. || Maximum Frame Size | Indicates the supported maximum frame size. || Power Stateful Control | Indicates the power state control configured on the sending port, including the following:  ·     Power supply mode of the PSE/PD.  ·     PSE/PD priority.  ·     PSE/PD power. || Energy-Efficient Ethernet | Indicates Energy Efficient Ethernet (EEE).  Energy-Efficient Ethernet TLVs are not supported in the current software version. |






 

#### LLDP-MED TLVs

LLDP-MED TLVs provide multiple advanced
applications for voice over IP (VoIP), such as basic configuration, network
policy configuration, and address and directory management. LLDP-MED TLVs provide
a cost-effective and easy-to-use solution for deploying voice devices in Ethernet.
LLDP-MED TLVs are shown in [Table 6](#_Ref226534647).

If the MAC/PHY configuration/status TLV is
not advertisable, none of the LLDP-MED TLVs will be advertised even if they are
advertisable. 

If the LLDP-MED capabilities TLV is not
advertisable, the other LLDP-MED TLVs will not be advertised even if they are
advertisable.

Table 6 LLDP-MED TLVs

| Type | Description |
| --- | --- |
| LLDP-MED Capabilities | Allows a network device to advertise the LLDP-MED TLVs that it supports. || Network Policy | Allows a network device or terminal device to advertise the VLAN ID of a port, the VLAN type, and the Layer 2 and Layer 3 priorities for specific applications. || Extended Power-via-MDI | Allows a network device or terminal device to advertise power supply capability. This TLV is an extension of the Power Via MDI TLV. || Hardware Revision | Allows a terminal device to advertise its hardware version. || Firmware Revision | Allows a terminal device to advertise its firmware version. || Software Revision | Allows a terminal device to advertise its software version. || Serial Number | Allows a terminal device to advertise its serial number. || Manufacturer Name | Allows a terminal device to advertise its vendor name. || Model Name | Allows a terminal device to advertise its model name. || Asset ID | Allows a terminal device to advertise its asset ID. The typical case is that the user specifies the asset ID for the endpoint to facilitate directory management and asset tracking. || Location Identification | Allows a network device to advertise the appropriate location identifier information for a terminal device to use in the context of location-based applications. |











‌

#### H3C-proprietary TLVs

H3C-proprietary TLVs are used to meet
specific transmission requirements on network management.

Only actual power TLVs are supported in the
current software version. This type of TLVs provides PoE power information
about an interface. 

### Management address

The network management system uses the
management address of a device to identify and manage the device for topology
maintenance and network management. The management address is encapsulated in
the management address TLV.

### LLDP operating modes

An LLDP agent can operate in one of the
following modes:

·     TxRx mode—An LLDP agent in this mode can send and receive LLDP frames.

·     Tx mode—An LLDP agent in this mode can only send LLDP frames. 

·     Rx mode—An LLDP agent in this mode can only receive LLDP frames.

·     Disable mode—An LLDP agent in this mode cannot send or receive LLDP frames.

Each time the operating mode of an LLDP
agent changes, its LLDP protocol state machine reinitializes. A configurable
reinitialization delay prevents frequent initializations caused by frequent
changes to the operating mode. If you configure the reinitialization delay, an
LLDP agent must wait the specified amount of time to initialize LLDP after the
LLDP operating mode changes. 

### Transmitting and receiving LLDP frames

#### Transmitting LLDP frames

An LLDP agent operating in TxRx mode or Tx
mode sends LLDP frames to its directly connected devices both periodically and
when the local configuration changes. To prevent LLDP frames from overwhelming the
network during times of frequent changes to local device information, LLDP uses
the token bucket mechanism to rate limit LLDP frames. For more information
about the token bucket mechanism, see ACL and QoS
Configuration Guide.

LLDP automatically enables the fast LLDP
frame transmission mechanism in either of the following cases: 

·     A new LLDP frame is received and carries device
information new to the local device. 

·     The LLDP operating mode of the LLDP agent
changes from Disable or Rx to TxRx or Tx. 

The fast LLDP frame transmission mechanism
successively sends the specified number of LLDP frames at a configurable fast LLDP
frame transmission interval. The mechanism helps LLDP neighbors discover the
local device as soon as possible. Then, the normal LLDP frame transmission
interval resumes.

#### Receiving LLDP frames

An LLDP agent operating in TxRx mode or Rx
mode confirms the validity of TLVs carried in every received LLDP frame. If the
TLVs are valid, the LLDP agent saves the information and starts an aging timer.
The initial value of the aging timer is equal to the TTL value in the Time To
Live TLV carried in the LLDP frame. When the LLDP agent receives a new LLDP
frame, the aging timer restarts. When the aging timer decreases to zero, all
saved information ages out.

### Collaboration with Track

You can configure a track entry and
associate it with an LLDP interface. The LLDP module checks the neighbor
availability of the LLDP interface and reports the check result to the Track
module. The Track module changes the track entry status accordingly so the
associated application module can take correct actions.

The Track module changes the track entry
status based on the neighbor availability of a monitored LLDP interface as
follows:

·     If the neighbor of the LLDP interface is
available, the Track module sets the track entry to Positive state.

·     If the neighbor of the LLDP interface is
unavailable, the Track module sets the track entry to Negative state.

For more information about collaboration
between Track and LLDP, see the track configuration in High
Availability Configuration Guide.

### AP reboot through LLDP

As a power sourcing equipment (PSE), the
device can detect the state of a powered AP through the neighbor aging
mechanism. If an AP does not send LLDP frames because of failure, the device
restarts the AP by calling the PoE module to restart the power interface (PI)
attached to the AP.

The AP reboot mechanism is as follows:

**1\.**The device receives LLDP frames that carry
the WlanAccessPoint field in the system capabilities TLV and identifies the
frame source as an AP neighbor.

**2\.**If the device has not received any LLDP
frames from the AP when the AP ages out and the PI attached to the AP is
physically up, the device reboots the PI. If the PI is down, the device deletes
the neighbor relationship with the AP.

The AP reboot mechanism takes effect only
on the PIs that have only one AP neighbor.

For more information about PSEs, powered
devices (PDs), and PIs, see PoE configuration in Network
Management and Monitoring Configuration Guide.

### Protocols and standards

·     IEEE 802.1AB-2005, Station
and Media Access Control Connectivity Discovery

·     IEEE 802.1AB-2009, Station
and Media Access Control Connectivity Discovery

·     ANSI/TIA-1057, Link Layer
Discovery Protocol for Media Endpoint Devices

·     IEEE Std 802.1Qaz-2011, Media Access Control (MAC) Bridges and Virtual Bridged Local
Area Networks-Amendment 18: Enhanced Transmission Selection for Bandwidth Sharing
Between Traffic Classes

## Restrictions and guidelines: LLDP configuration

When you configure LLDP, follow these
restrictions and guidelines:

·     Some of the LLDP configuration tasks are
available in different interface views (see [Table 7](#_Ref462157131)).

Table 7 Support of LLDP configuration tasks
in different views

| Tasks | Supported views |
| --- | --- |
| [Enabling LLDP](#_Ref475962539) | Layer 2 Ethernet interface view  Layer 3 Ethernet interface view  Management Ethernet interface view  Layer 2 aggregate interface view  Layer 3 aggregate interface view  IRF physical interface view || [Setting the LLDP operating mode](#_Ref163622805) || [Configuring the advertisable TLVs](#_Ref462241009) || [Configuring advertisement of the management address TLV](#_Ref214794644) || [Setting the encapsulation format for LLDP frames](#_Ref462241110) || [Enabling LLDP polling](#_Ref475962571) || Configuring LLDP trapping || [Configuring port ID subtype of port ID TLVs advertised by LLDP](#_Ref98435720) (not supported in Layer 2 aggregate interface view and Layer 3 aggregate interface view) |








 

·     To use LLDP together with OpenFlow, you must
enable LLDP globally on OpenFlow switches. To prevent LLDP from affecting
topology discovery of OpenFlow controllers, disable LLDP on ports of OpenFlow
instances. For more information about OpenFlow, see OpenFlow
Configuration Guide.

·     You can configure LLDP on an IRF physical
interface to monitor the connection and link status of the IRF physical link.
An LLDP-enabled IRF physical interface supports only the nearest bridge agent. 

## LLDP tasksat a glance

To configure LLDP, perform the following
tasks:

**1\.**[Enabling LLDP](#_Ref475962539)

**2\.**[Setting the LLDP bridge mode](#_Ref349303942)

**3\.**[Setting the LLDP operating mode](#_Ref153340346)

**4\.**(Optional.) [Setting the LLDP reinitialization delay](#_Ref214794532)

**5\.**(Optional.) Configuring LLDP packet-related
settings

¡     [Configuring the advertisable TLVs](#_Ref462241009)

¡     [Configuring advertisement of the
management address TLV](#_Ref214794644)

¡     [Setting the encapsulation format for
LLDP frames](#_Ref462241110)

¡     [Setting LLDP frame transmission parameters](#_Ref214794648)

¡     [Setting the timeout for receiving LLDP
frames](#_Ref7347940)

¡     [Configuring port ID subtype of port ID
TLVs advertised by LLDP](#_Ref98435685)

**6\.**(Optional.) [Enabling LLDP polling](#_Ref475962571)

**7\.**(Optional.) [Disabling LLDP PVID inconsistency check](#_Ref435522766)

**8\.**(Optional.) [Configuring CDP compatibility](#_Ref189446679)

**9\.**(Optional.) [Configuring LLDP trapping and LLDP-MED
trapping](#_Ref312952129)

**10\.**(Optional.) [Configuring MAC address learning for DCN](#_Ref75164860)

¡     [Setting the source MAC address of LLDP
frames](#_Ref75164870)

¡     [Enabling generation of ARP or ND entries
for received management address TLVs](#_Ref75164875)

## Enabling LLDP

#### Restrictions and guidelines

For LLDP to take effect on specific ports,
you must enable LLDP both globally and on these ports. 

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enable LLDP globally.

lldp global enable

For the S5000-EI switch series, LLDP is
disabled globally by default.

For other switch series, the default LLDP
global state varies by switch configuration.

¡     If
the switch is started with the software default settings, LLDP is disabled
globally.

¡     If
the switch is started with the factory default settings, LLDP is enabled
globally.

For more information about device startup
with software or factory default settings, see Fundamentals
Configuration Guide.

**3\.**Enter interface view.

interface interface-type
interface-number

**4\.**Enable LLDP.

lldp enable

By default, LLDP is enabled on a port.

## Setting the LLDP bridge mode

**1\.**Enter system view.

system-view

**2\.**Set the LLDP bridge
mode.

¡     Set
the LLDP bridge mode to service bridge.

lldp mode service-bridge

By default, LLDP operates in customer
bridge mode.

¡     Set
the LLDP bridge mode to customer bridge.

undo lldp mode

By default, LLDP operates in customer
bridge mode.

## Setting the LLDP operating mode

**1\.**Enter system view.

system-view

**2\.**Enter interface view.

interface interface-type
interface-number

**3\.**Set the LLDP operating mode.

¡     In
Layer 2/Layer 3 Ethernet interface view or management Ethernet interface view:

lldp \[ agent { nearest-customer \| nearest-nontpmr } ] admin-status { disable \| rx \| tx \| txrx }

In Ethernet interface view, if you do not
specify an agent type, the command sets the operating mode for the nearest
bridge agent.

¡     In
Layer 2/Layer 3 aggregate interface view:

lldp agent {
nearest-customer \|
nearest-nontpmr } admin-status { disable \| rx \| tx \| txrx }

In aggregate interface view, you can set
the operating mode only for the nearest customer bridge agent and nearest
non-TPMR bridge agent.

¡     In
IRF physical interface view:

lldp admin-status { disable \| rx \| tx \| txrx }

In IRF physical interface view, you can
set the operating mode only for the nearest bridge agent.

By default: 

¡     The
nearest bridge agent operates in TxRx
mode.

¡     The
nearest customer bridge agent and nearest non-TPMR bridge agent operate in Disable mode.

## Setting the LLDP reinitialization delay

#### About this task

When the LLDP operating mode changes on a
port, the port initializes the protocol state machines after an LLDP
reinitialization delay. By adjusting the delay, you can avoid frequent initializations
caused by frequent changes to the LLDP operating mode on a port. 

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Set the LLDP reinitialization delay.

lldp timer reinit-delay
delay

The default LLDP reinitialization delay
is 2 seconds.

## Configuring the advertisable TLVs

**1\.**Enter system view.

system-view

**2\.**Enter interface view.

interface interface-type
interface-number

**3\.**Configure the advertisable TLVs.

¡     In Layer
2 Ethernet interface view:

lldp tlv-enable { basic-tlv { all \| port-description \| system-capability \| system-description \| system-name \| management-address-tlv \[ ipv6 ] \[ ip-address \| interface loopback interface-number ] } \| dot1-tlv { all \| port-vlan-id \| link-aggregation \| protocol-vlan-id \[ vlan-id ] \| vlan-name \[ vlan-id ] \| management-vid \[ mvlan-id ] } \| dot3-tlv { all \| link-aggregation \| mac-physic \| max-frame-size \| power } \| med-tlv { all \| capability \| inventory \| network-policy \[ vlan-id ] \| power-over-ethernet \| location-id { civic-address device-type
country-code { ca-type
ca-value }\&\<1-10\> \| elin-address tel-number } } }

lldp tlv-enable private-tlv actual-power

By default, the nearest bridge agent
advertises all supported TLVs except the following TLVs:

\-     Location identification TLVs.

\-     Port and protocol VLAN ID TLVs.

\-     VLAN name TLVs.

\-     Management VLAN ID TLVs.

lldp agent nearest-nontpmr tlv-enable { basic-tlv { all \| port-description \| system-capability \| system-description \| system-name \| management-address-tlv \[ ipv6 ] \[ ip-address ] } \| dot1-tlv { all \| port-vlan-id \| link-aggregation } \| dot3-tlv { all \| link-aggregation } }

lldp tlv-enable dot1-tlv { protocol-vlan-id \[ vlan-id ] \| vlan-name \[ vlan-id ] \| management-vid \[ mvlan-id ] }

lldp agent nearest-nontpmr tlv-enable private-tlv
actual-power

By default, the nearest non-TPMR bridge
agent does not advertise any TLVs. 

lldp agent nearest-customer tlv-enable { basic-tlv { all \| port-description \|
system-capability \| system-description \| system-name \| management-address-tlv \[ ipv6 ] \[ ip-address ] } \| dot1-tlv { all \| port-vlan-id \| link-aggregation } \| dot3-tlv { all \| link-aggregation } }

lldp tlv-enable dot1-tlv { protocol-vlan-id \[ vlan-id ] \| vlan-name \[ vlan-id ] \| management-vid \[ mvlan-id ] }

lldp agent nearest-customer tlv-enable private-tlv
actual-power

By default, the nearest customer bridge agent
advertises all the supported basic management TLVs and Port VLAN ID TLVs and
link aggregation TLVs in the IEEE 802.1 organizationally specific TLV set.

¡     In Layer
3 Ethernet interface view:

lldp tlv-enable { basic-tlv { all \| port-description \| system-capability \| system-description \| system-name \| management-address-tlv \[ ipv6 ] \[ ip-address \| interface loopback interface-number ] } \| dot1-tlv { all \| link-aggregation } \| dot3-tlv { all \| link-aggregation \| mac-physic \| max-frame-size \| power } \| med-tlv { all \| capability \| inventory \| power-over-ethernet \| location-id { civic-address device-type
country-code { ca-type
ca-value }\&\<1-10\> \| elin-address tel-number } } }

lldp tlv-enable private-tlv actual-power

By default, the nearest bridge agent advertises
the following TLVs:

\-     All supported basic management TLVs.

\-     Link aggregation TLVs in the 802.1 organizationally specific TLV
set.

\-     All supported 802.3 organizationally specific TLVs.

\-     All supported LLDP-MED TLVs except the network policy TLVs.

lldp agent { nearest-nontpmr \| nearest-customer } tlv-enable { basic-tlv { all \| port-description \| system-capability \| system-description \| system-name \| management-address-tlv \[ ipv6 ] \[ ip-address ] } \| dot1-tlv { all \| link-aggregation } \| dot3-tlv { all \| link-aggregation } }

lldp agent { nearest-customer \| nearest-nontpmr
} tlv-enable
private-tlv actual-power

By default:

\-     The nearest non-TPMR bridge agent does not advertise any TLVs.

\-     The nearest customer bridge agent advertises all supported basic
management TLVs and link aggregation TLVs in the IEEE 802.1 organizationally
specific TLV set.

¡     In
management Ethernet interface view:

lldp tlv-enable { basic-tlv { all \| port-description \| system-capability \| system-description \| system-name \| management-address-tlv \[ ipv6 ] \[ ip-address ] } \| dot1-tlv { all \| link-aggregation } \| dot3-tlv { all \| link-aggregation \| mac-physic \| max-frame-size \| power } \| med-tlv { all \| capability \| inventory \| power-over-ethernet \| location-id { civic-address device-type
country-code { ca-type
ca-value }\&\<1-10\> \| elin-address tel-number } } }

By default, the nearest bridge agent
advertises the following TLVs:

\-     All supported basic management TLVs.

\-     Link aggregation TLVs in the 802.1 organizationally specific TLV set.

\-     All supported 802.3 organizationally specific TLVs.

\-     All supported LLDP-MED TLVs except the network policy TLVs.

lldp agent { nearest-nontpmr \| nearest-customer } tlv-enable { basic-tlv { all \| port-description \| system-capability \| system-description \| system-name \| management-address-tlv \[ ipv6 ] \[ ip-address ] } \| dot1-tlv { all \| link-aggregation } \| dot3-tlv { all \| link-aggregation } }

By default:

\-     The nearest non-TPMR bridge agent does not advertise any TLVs.

\-     The nearest customer bridge agent advertises all supported basic
management TLVs and link aggregation TLVs in the IEEE 802.1 organizationally
specific TLV set.

¡     In Layer
2 aggregate interface view:

lldp tlv-enable dot1-tlv { protocol-vlan-id \[ vlan-id ] \| vlan-name \[ vlan-id ] \| management-vid \[ mvlan-id ]

lldp agent nearest-nontpmr tlv-enable { basic-tlv { all \| management-address-tlv \[ ipv6 ] \[ ip-address \| interface loopback interface-number ] \| port-description \| system-capability \| system-description \| system-name } \| dot1-tlv { all \| port-vlan-id } }

By default, the nearest non-TPMR bridge
agent does not advertise any TLVs. 

lldp agent nearest-customer tlv-enable { basic-tlv { all \| management-address-tlv \[ ipv6 ] \[ ip-address ] \| port-description \| system-capability \| system-description \| system-name } \| dot1-tlv { all \| port-vlan-id } }

By default, the nearest customer bridge
agent advertises all supported basic management TLVs and Port VLAN ID TLVs in
the IEEE 802.1 organizationally specific TLV set.

The nearest bridge agent is not
supported.

¡     In Layer
3 aggregate interface view:

lldp agent { nearest-customer \| nearest-nontpmr } tlv-enable basic-tlv { all \| management-address-tlv \[ ipv6 ] \[ ip-address ] \| port-description \| system-capability \| system-description \| system-name }

By default:

\-     The nearest non-TPMR bridge agent does not advertise any TLVs.

\-     The nearest customer bridge agent advertises all supported basic
management TLVs.

The nearest bridge agent is not
supported.

¡     In
IRF physical interface view:

lldp tlv-enable basic-tlv {
port-description \| system-capability \|
system-description \| system-name }

By default, the nearest bridge agent
advertises all supported basic management TLVs.

Only the nearest bridge agent is
supported.

## Configuring advertisement of the management address TLV

#### About this task

LLDP encodes management addresses in numeric
or string format in management address TLVs. 

If a neighbor encodes its management
address in string format, set the encoding format of the management address to string on the connecting port. This guarantees normal communication with
the neighbor.

You can configure advertisement of the
management address TLV globally or on a per-interface basis. The device selects
the management address TLV advertisement setting for an interface in the
following order:

**1\.**Interface-based setting, configured by using
the lldp
tlv-enable command with the management-address-tlv keyword.

**2\.**Global setting, configured by using the lldp global
tlv-enable basic-tlv management-address-tlv
command.

**3\.**Default setting for the interface.

By default:

¡     The
nearest bridge agent and nearest customer bridge agent advertise the management
address TLV.

¡     The
nearest non-TPMR bridge agent does not advertise the management address TLV.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enable advertisement of the management
address TLV globally and set the management address to be advertised.

lldp \[ agent { nearest-customer \| nearest-nontpmr } ] global tlv-enable basic-tlv management-address-tlv \[ ipv6 ] { ip-address \| interface loopback interface-number \| interface m-gigabitethernet
interface-number \| interface vlan-interface interface-number }

By default, advertisement of the
management address TLV is disabled globally.

**3\.**Enter interface view.

interface interface-type
interface-number

**4\.**Enable advertisement of the management
address TLV on the interface and set the management address to be advertised.

¡     In
Layer 2 Ethernet interface view:

lldp tlv-enable basic-tlv management-address-tlv \[ ipv6 ] \[ ip-address \| interface loopback interface-number ]

lldp agent { nearest-customer \|
nearest-nontpmr } tlv-enable basic-tlv management-address-tlv \[ ipv6 ] \[ ip-address ]

¡     In management
interface view:

lldp \[ agent { nearest-customer \| nearest-nontpmr } ] tlv-enable basic-tlv management-address-tlv \[ ipv6 ] \[ ip-address ]

¡     In
Layer 3 Ethernet interface view:

lldp tlv-enable basic-tlv management-address-tlv \[ ipv6 ] \[ ip-address \| interface loopback interface-number ]

lldp agent { nearest-customer \|
nearest-nontpmr } tlv-enable basic-tlv management-address-tlv \[ ipv6 ] \[ ip-address ]

¡     In
Layer 2/Layer 3 aggregate interface view:

lldp agent { nearest-customer \|
nearest-nontpmr } tlv-enable basic-tlv
management-address-tlv \[ ipv6 ] \[ ip-address ]

By default:

¡     The
nearest bridge agent and nearest customer bridge agent advertise the management
address TLVs.

¡     The
nearest non-TPMR bridge agent does not advertise the management address TLV.

**5\.**Set the encoding format of the management
address to string.

¡     In
Layer 2/Layer 3 Ethernet interface view or management Ethernet interface view:

lldp \[ agent {
nearest-customer \| nearest-nontpmr } ]
management-address-format string

¡     In
Layer 2/Layer 3 aggregate interface view:

lldp agent { nearest-customer \| nearest-nontpmr } management-address-format string

The default management address encoding
format is numeric.

The device supports only the numeric
encoding format for IPv6 management addresses.

## Setting the encapsulation format for LLDP frames

#### About this task

Earlier versions of LLDP require the same encapsulation
format on both ends to process LLDP frames. To successfully communicate with a
neighboring device running an earlier version of LLDP, the local device must be
set with the same encapsulation format.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter interface view.

interface interface-type
interface-number

**3\.**Set the encapsulation format for LLDP frames
to SNAP.

¡     In
Layer 2/Layer 3 Ethernet interface view or management Ethernet interface view:

lldp \[ agent {
nearest-customer \| nearest-nontpmr } ] encapsulation
snap

¡     In
Layer 2/Layer 3 aggregate interface view:

lldp agent { nearest-customer \|
nearest-nontpmr } encapsulation snap

¡     In
IRF physical interface view:

lldp encapsulation snap

By default, the Ethernet II encapsulation
format is used.

## Setting LLDP frame transmission parameters

#### About this task

The Time to Live TLV carried in an LLDPDU
determines how long the device information carried in the LLDPDU can be saved
on a recipient device. 

By setting the TTL multiplier, you can
configure the TTL of locally sent LLDPDUs. The TTL is expressed by using the
following formula:

TTL \= Min (65535, (TTL multiplier × LLDP
frame transmission interval \+ 1))

As the expression shows, the TTL can be up
to 65535 seconds. TTLs greater than 65535 will be rounded down to 65535
seconds.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Set the TTL multiplier.

lldp hold-multiplier value

The default setting is 4\.

**3\.**Set the LLDP frame transmission interval.

lldp timer tx-interval interval

The default setting is 30 seconds.

**4\.**Set the token bucket size for sending LLDP
frames.

lldp max-credit credit-value

The default setting is 5\.

**5\.**Set the number of LLDP frames sent each time
fast LLDP frame transmission is triggered.

lldp fast-count count

The default setting is 4\.

**6\.**Set the fast LLDP frame transmission
interval.

lldp timer fast-interval interval

The default setting is 1 second.

## Setting the timeout for receiving LLDP frames

#### About this task

This feature allows the device to detect
the presence of directly connected neighbors by setting the timeout timer for receiving
LLDP frames. If an interface has not received any frames when the timeout timer
expires, the device reports a no LLDP neighbor event to the NETCONF module.

#### Restrictions and guidelines

To avoid misdetection, make sure the timeout
for receiving LLDP frames is greater than the LLDP frame transmission interval.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Set the timeout for receiving LLDP frames.

lldp timer rx-timeout timeout

By default, no timeout is set for receiving
LLDP frames, and the device does not report no LLDP neighbor events.

## Configuring port ID subtype of port ID TLVs advertised by LLDP

#### About this task

This task enables an H3C device to
advertise only port ID TLVs that contain interface names. By default, an H3C
device advertises port ID TLVs that contain interface MAC addresses or
interface names. The media devices from some vendors can obtain interface
information from H3C devices only through LLDP. For the media devices to obtain
interface names, you must configure H3C devices to generate port ID TLVs that
use interface names as the port ID subtype.

#### Restrictions and guidelines

Perform this task only when LLDP neighbors
must obtain interface names from LLDPDUs. Do not perform this task in any other
scenarios.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter Layer 2 Ethernet interface view/Layer
3 Ethernet interface view/management Ethernet interface view/IRF physical
interface view.

interface interface-type interface-number

**3\.**Configure the port ID subtype of port ID
TLVs advertised by LLDP.

lldp tlv-config basic-tlv port-id type-id

By default, an interface advertises port
ID TLVs that contain interface MAC addresses if it receives LLDP-MED TLVs, and
advertises port ID TLVs that contain interface names if no LLDP-MED TLVs are
received. 

## Enabling LLDP polling

#### About this task

With LLDP polling enabled, a device
periodically searches for local configuration changes. When the device detects
a configuration change, it sends LLDP frames to inform neighboring devices of
the change.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter interface view.

interface interface-type
interface-number

**3\.**Enable LLDP polling and set the polling
interval.

¡     In
Layer 2/Layer 3 Ethernet interface view or management Ethernet interface view:

lldp \[ agent { nearest-customer \| nearest-nontpmr } ] check-change-interval interval

¡     In
Layer 2/Layer 3 aggregate interface view:

lldp agent { nearest-customer \| nearest-nontpmr } check-change-interval interval

¡     In
IRF physical interface view:

lldp check-change-interval interval

By default, LLDP polling is disabled.

## Disabling LLDP PVID inconsistency check

#### About this task

By default, when the system receives an
LLDP packet, it compares the PVID value contained in the packet with the PVID
configured on the receiving interface. If the two PVIDs do not match, a log
message will be printed to notify the user.

You can disable PVID inconsistency check if
different PVIDs are required on a link.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Disable LLDP PVID inconsistency check.

lldp ignore-pvid-inconsistency

By default, LLDP PVID inconsistency check
is enabled.

## Configuring CDP compatibility

#### About this task

To enable your device to exchange
information with a directly connected Cisco device that supports only CDP, you
must enable CDP compatibility.

CDP compatibility enables your device to
receive and recognize CDP packets from the neighboring CDP device and send CDP
packets to the neighboring device. The CDP packets sent to the neighboring CDP device
carry the following information:

·     Device ID.

·     ID of the port connecting to the neighboring device.

·     Port IP address.

·     TTL. 

The port IP address is the primary IP
address of a VLAN interface in up state. The VLAN ID of the VLAN interface must
be the lowest among the VLANs permitted on the port. If no VLAN interfaces of
the permitted VLANs are assigned an IP address or all VLAN interfaces are down,
no port IP address will be advertised. 

You can view the neighboring CDP device
information that can be recognized by the device in the output of the display lldp
neighbor-information command. For more
information about the display lldp neighbor-information command, see LLDP commands in Layer 2—LAN
Switching Command Reference.

To make your device work with Cisco IP
phones, you must enable CDP compatibility.

If your LLDP-enabled device cannot
recognize CDP packets, it does not respond to the requests of Cisco IP phones
for the voice VLAN ID configured on the device. As a result, a requesting Cisco
IP phone sends voice traffic without any tag to your device. Your device cannot
differentiate the voice traffic from other types of traffic.

CDP compatibility
enables your device to receive and recognize CDP packets from a Cisco IP phone
and respond with CDP packets carrying TLVs with the configured voice VLAN. If
no voice VLAN is configured for CDP packets, CDP packets carry the voice VLAN
of the port or the voice VLAN assigned by the RADIUS server. The assigned voice
VLAN has a higher priority. According to TLVs with the voice VLAN
configuration, the IP phone automatically configures the voice VLAN. As a
result, the voice traffic is confined in the configured voice VLAN and is differentiated
from other types of traffic.

For more information about voice VLANs, see
"Configuring voice VLANs."

When the device is connected to a Cisco IP
phone that has a host attached to its data port, the host must access the
network through the Cisco IP phone. If the data port goes down, the IP phone
will send a CDP packet to the device so the device can log out the user.

CDP-compatible LLDP operates in one of the
following modes:

·     TxRx—CDP packets can be transmitted and received. 

·     Rx—CDP packets can be received but cannot be transmitted.

·     Disable—CDP packets cannot be transmitted or received. 

#### Restrictions and guidelines

When you configure CDP compatibility for
LLDP, follow these restrictions and guidelines:

·     To make CDP-compatible LLDP take effect on a port,
follow these steps:

**a.**Enable CDP-compatible LLDP globally. 

**b.**Configure CDP-compatible LLDP to operate in
TxRx mode on the port. 

·     The maximum TTL value that CDP allows is 255
seconds. To make CDP-compatible LLDP work correctly with Cisco IP phones,
configure the LLDP frame transmission interval to be no more than 1/3 of the
TTL value.

#### Prerequisites

Before you configure CDP compatibility, complete
the following tasks:

·     Globally enable LLDP. 

·     Enable LLDP on the port connecting to a CDP
device. 

·     Configure LLDP to operate in TxRx mode on the
port. 

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enable CDP compatibility globally.

lldp compliance cdp

By default, CDP compatibility is disabled
globally. 

**3\.**Enter Layer 2/Layer 3 Ethernet interface
view or management Ethernet interface view. 

interface interface-type
interface-number

**4\.**Configure CDP-compatible LLDP to operate in
TxRx mode.

lldp compliance admin-status cdp txrx

By default, CDP-compatible LLDP operates
in disable mode.

**5\.**Set the voice VLAN ID carried in CDP
packets.

cdp voice-vlan vlan-id

By default, no voice VLAN ID is
configured to be carried in CDP packets.

## Configuring LLDP trapping and LLDP-MED trapping

#### About this task

LLDP trapping or LLDP-MED trapping notifies
the network management system of events such as newly detected neighboring
devices and link failures.

Before you set a trap transmission interval
for LLDP, make sure you understand the following influences of the LLDP trap
and LLDP-MED trap transmission interval: 

·     If the interval value is large, the trap sending
frequency is low, which occupies fewer system resources. This is suitable for
scenarios where the network topology on the device changes frequently. However,
do not set the interval to a too large value. If the interval value is too
large, the network management system cannot identify neighbor changes in time,
which will cause the network topology on the device not to update in time.

·     If the interval value is small, the trap sending
frequency is high. This is suitable for topology-sensitive scenarios. However,
do not set the interval to a too small value. If the interval value is too
small, the network management system will frequently refresh the neighbor
status information. This will not only occupies too many system resources but
also cause network topology vibration on the device.

#### Restrictions and guidelines

In the early stage of networking, the
network topology changes frequently. If LLDP trapping or LLDP-MED trapping is
enabled, the device will frequently send trap information to the network
management system, which increases the system burden and wastes resources. As a
best practice, do not enable LLDP trapping or LLDP-MED trapping in the early
stage of networking.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter interface view.

interface interface-type
interface-number

**3\.**Enable LLDP trapping.

¡     In
Layer 2/Layer 3 Ethernet interface view or management Ethernet interface view:

lldp \[ agent {
nearest-customer \| nearest-nontpmr } ] notification remote-change enable

¡     In
Layer 2/Layer 3 aggregate interface view:

lldp agent { nearest-customer \| nearest-nontpmr } notification remote-change enable

¡     In
IRF physical interface view:

lldp notification remote-change enable

By default, LLDP trapping is disabled.

**4\.**(In Layer 2/Layer 3 Ethernet interface view
or management Ethernet interface view.) Enable LLDP-MED trapping.

lldp notification med-topology-change enable

By default, LLDP-MED trapping is disabled.

**5\.**Return to system view.

quit

**6\.**(Optional.) Set the LLDP trap transmission
interval.

lldp timer notification-interval interval

The default setting is 30 seconds.

## Configuring MAC address learning for DCN

### Setting the source MAC address of LLDP frames

#### About this task

This feature must be configured with generation
of ARP or ND entries for received management address TLVs to meet the following
requirements:

·     The source MAC address of outgoing LLDP frames
is the MAC address of a VLAN interface instead of the MAC address of the egress
interface.

·     The neighbor device can generate correct ARP or
ND entries for the local device. 

In Layer 2 Ethernet interface view or Layer
2 aggregate interface view, this feature sets the source MAC address of outgoing
LLDP frames to the MAC address of a VLAN interface to which the specified VLAN
ID belongs. The source MAC address of outgoing LLDP frames is the MAC address
of the Layer 2 interface in the following situations:

·     The specified VLAN or the corresponding VLAN
interface does not exist.

·     The VLAN interface to which the VLAN ID belongs
is physically down.

In Layer 3 Ethernet interface view, the MAC
address of the Layer 3 Ethernet interface is always used as the source MAC
address of outgoing LLDP frames, regardless of which VLAN ID you specify.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter Layer 2 Ethernet interface view, Layer
2 aggregate interface view, or Layer 3 Ethernet interface view.

interface interface-type interface-number

**3\.**Set the source MAC address of LLDP frames to
the MAC address of a VLAN interface or the Layer 3 Ethernet interface.

lldp source-mac vlan vlan-id

By default, the source MAC address of
LLDP frames is the MAC address of the egress interface.

### Enabling generation of ARP or ND entries for received management address TLVs

#### About this task

This feature enables the device to generate
an ARP or ND entry after receiving an LLDP frame containing a management
address TLV on an interface. The ARP or ND entry maps the advertised management
address to the source MAC address of the frame.

You can enable generation of both ARP and
ND entries on an interface. If the management address TLV contains an IPv4 address,
the device generates an ARP entry. If the management address TLV contains an IPv6
address, the device generates an ND entry.

In Layer 2 Ethernet interface view or Layer
2 aggregate interface view, this feature sets the Layer 2 interface to the
output interface in the generated entries. The VLAN to which the entries belong
is the VLAN specified by this feature. The device cannot generate ARP or ND
entries in one of the following situations:

·     The specified VLAN or the corresponding VLAN
interface does not exist.

·     The VLAN interface to which the VLAN ID belongs
is physically down.

In Layer 3 Ethernet interface view, the
Layer 3 Ethernet interface is always recorded as the output interface, no
matter whether you specify a VLAN ID. 

#### Restrictions and guidelines

This feature must be configured with the
feature of setting the source MAC address of LLDP frames to meet the following
requirements: 

·     The device uses the MAC address of a VLAN
interface or a Layer 3 Ethernet subinterface instead of the egress interface's
MAC address as the source MAC address of LLDP frames.

·     The neighbor device can generate correct ARP or
ND entries.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter Layer 2 Ethernet interface view, Layer
2 aggregate interface view, or Layer 3 Ethernet interface view.

interface interface-type interface-number

**3\.**Enable generation of ARP or ND entries for
management address TLVs received on the interface.

¡     In
Layer 2 Ethernet interface view or Layer 2 aggregate interface view:

lldp management-address
{ arp-learning \| nd-learning } vlan vlan-id

¡     In
Layer 3 Ethernet interface view:

lldp management-address
{ arp-learning \| nd-learning } \[ vlan vlan-id ] 

By default, generation of ARP or ND
entries for received management address TLVs is disabled on an interface.

In Layer 2 Ethernet interface view, the vlan vlan-id option specifies the ID of the VLAN to which the generated ARP or
ND entry belongs. To prevent the ARP or ND entries from overwriting each other,
do not specify the same VLAN ID for different Layer 2 Ethernet interfaces.

You can enable generation of both ARP and
ND entries on an interface. 

## Display and maintenance commands for LLDP

Execute display
commands in any view. 

 

| Task | Command |
| --- | --- |
| Display local LLDP information. | display lldp local-information \[ global \| interface interface-type interface-number ] || Display the information contained in the LLDP TLVs sent from neighboring devices. | display lldp neighbor-information \[ \[ \[ interface interface-type interface-number ] \[ agent { nearest-bridge \| nearest-customer \| nearest-nontpmr } ] \[ verbose ] ] \| list \[ system-name system-name ] ] || Display LLDP statistics. | display lldp statistics \[ global \| \[ interface interface-type interface-number ] \[ agent { nearest-bridge \| nearest-customer \| nearest-nontpmr } ] ] || Display LLDP status of a port. | display lldp status \[ interface interface-type interface-number ] \[ agent { nearest-bridge \| nearest-customer \| nearest-nontpmr } ] || Display types of advertisable optional LLDP TLVs. | display lldp tlv-config \[ interface interface-type interface-number ] \[ agent { nearest-bridge \| nearest-customer \| nearest-nontpmr } ] || Clear LLDP statistics on ports. | reset lldp statistics \[ interface interface-type interface number ] \[ agent { nearest-bridge \| nearest-customer \| nearest-nontpmr } ] |






## LLDP configuration examples

### Example: Configuring basic LLDP functions

#### Network configuration

As shown in [Figure 5](#_Ref214856223), enable
LLDP globally on Switch A and Switch B to perform the following tasks: 

·     Monitor the link between Switch A and Switch B
on the NMS. 

·     Monitor the link between Switch A and the MED
device on the NMS.

Figure 5 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11704452_x_Img_x_png_4_2215984_294551_0.png)

‌

#### Procedure

**1\.**Configure Switch A:

\# Enable LLDP globally.

\<SwitchA\> system-view

\[SwitchA] lldp global enable

\# Enable LLDP on Ten-GigabitEthernet 1/0/1. By default, LLDP
is enabled on ports. 

\[SwitchA] interface ten-gigabitethernet 1/0/1

\[SwitchA-Ten-GigabitEthernet1/0/1] lldp enable

\# Set the LLDP operating mode to Rx on Ten-GigabitEthernet 1/0/1. 

\[SwitchA-Ten-GigabitEthernet1/0/1] lldp admin-status
rx

\[SwitchA-Ten-GigabitEthernet1/0/1] quit

\# Enable LLDP on Ten-GigabitEthernet 1/0/2. By default, LLDP
is enabled on ports. 

\[SwitchA] interface gigabitethernet1/2

\[SwitchA-Ten-GigabitEthernet1/0/2] lldp enable

\# Set the LLDP operating mode to Rx on Ten-GigabitEthernet 1/0/2. 

\[SwitchA-Ten-GigabitEthernet1/0/2] lldp admin-status
rx

\[SwitchA-Ten-GigabitEthernet1/0/2] quit

**2\.**Configure Switch B:

\# Enable LLDP globally.

\<SwitchB\> system-view

\[SwitchB] lldp global enable

\# Enable LLDP on Ten-GigabitEthernet 1/0/1. By default, LLDP
is enabled on ports. 

\[SwitchB] interface ten-gigabitethernet 1/0/1

\[SwitchB-Ten-GigabitEthernet1/0/1] lldp enable

\# Set the LLDP operating mode to Tx on Ten-GigabitEthernet 1/0/1. 

\[SwitchB-Ten-GigabitEthernet1/0/1] lldp admin-status
tx

\[SwitchB-Ten-GigabitEthernet1/0/1] quit

#### Verifying the configuration

\# Verify the following items: 

·     Ten-GigabitEthernet 1/0/1 of Switch A connects to a MED device. 

·     Ten-GigabitEthernet 1/0/2 of Switch A connects to a non-MED device. 

·     Both ports operate in Rx mode, and they can
receive LLDP frames but cannot send LLDP frames.

\[SwitchA] display lldp status

Global status of LLDP: Enable

Bridge mode of LLDP: customer-bridge

The current number of LLDP neighbors:
2

The current number of CDP neighbors: 0

LLDP neighbor information last changed
time: 0 days, 0 hours, 4 minutes, 40 seconds

Transmit interval              : 30s

Fast transmit interval         : 1s

Transmit credit max            : 5

Hold multiplier                : 4

Reinit delay                   : 2s

Trap interval                  : 30s

Fast start times               : 4

 

LLDP status information of port 1 \[Ten-GigabitEthernet1/0/1]:

LLDP agent nearest-bridge:

Port status of LLDP            :
Enable

Admin status                   : Rx\_Only

Trap flag                      : No

MED trap flag                  : No

Polling interval               : 0s

Number of LLDP neighbors       : 1

Number of MED neighbors        : 1

Number of CDP neighbors        : 0

Number of sent optional TLV    : 21

Number of received unknown TLV : 0

 

LLDP agent nearest-customer:

Port status of LLDP            :
Enable

Admin status                   :
Disable

Trap flag                      : No

MED trap flag                  : No

Polling interval               : 0s

Number of LLDP neighbors       : 0

Number of MED neighbors        : 0

Number of CDP neighbors        : 0

Number of sent optional TLV    : 16

Number of received unknown TLV : 0

 

LLDP status information of port 2 \[Ten-GigabitEthernet1/0/2]:

LLDP agent nearest-bridge:

Port status of LLDP            :
Enable

Admin status                   : Rx\_Only

Trap flag                      : No

MED trap flag                  : No

Polling interval               : 0s

Number of LLDP neighbors       : 1

Number of MED neighbors        : 0

Number of CDP neighbors        : 0

Number of sent optional TLV    : 21

Number of received unknown TLV : 3

 

LLDP agent nearest-nontpmr:

Port status of LLDP            :
Enable

Admin status                   :
Disable

Trap flag                      : No

MED trap flag                  : No

Polling interval               : 0s

Number of LLDP neighbors       : 0

Number of MED neighbors        : 0

Number of CDP neighbors        : 0

Number of sent optional TLV    : 1

Number of received unknown TLV : 0

 

LLDP agent nearest-customer:

Port status of LLDP            :
Enable

Admin status                   :
Disable

Trap flag                      : No

MED trap flag                  : No

Polling interval               : 0s

Number of LLDP neighbors       : 0

Number of MED neighbors        : 0

Number of CDP neighbors        : 0

Number of sent optional TLV    : 16

Number of received unknown TLV : 0

\# Remove the link between Switch A and
Switch B.

\# Verify that Ten-GigabitEthernet 1/0/2 of Switch A does
not connect to any neighboring devices.

\[SwitchA] display lldp status

Global status of LLDP: Enable

The current number of LLDP neighbors:
1

The current number of CDP neighbors: 0

LLDP neighbor information last changed
time: 0 days, 0 hours, 5 minutes, 20 seconds

Transmit interval              : 30s

Fast transmit interval         : 1s

Transmit credit max            : 5

Hold multiplier                : 4

Reinit delay                   : 2s

Trap interval                  : 30s

Fast start times               : 4

 

LLDP status information of port 1 \[Ten-GigabitEthernet1/0/1]:

LLDP agent nearest-bridge:

Port status of LLDP            :
Enable

Admin status                   : Rx\_Only

Trap flag                      : No

MED trap flag                  : No

Polling interval               : 0s

Number of LLDP neighbors       : 1

Number of MED neighbors        : 1

Number of CDP neighbors        : 0

Number of sent optional TLV    : 0

Number of received unknown TLV : 5

 

LLDP agent nearest-nontpmr:

Port status of LLDP            :
Enable

Admin status                   : Disable

Trap flag                      : No

MED trap flag                  : No

Polling interval               : 0s

Number of LLDP neighbors       : 0

Number of MED neighbors        : 0

Number of CDP neighbors        : 0

Number of sent optional TLV    : 1

Number of received unknown TLV : 0

 

LLDP status information of port 2 \[Ten-GigabitEthernet1/0/2]:

LLDP agent nearest-bridge:

Port status of LLDP            :
Enable

Admin status                   : Rx\_Only

Trap flag                      : No

MED trap flag                  : No

Polling interval               : 0s

Number of LLDP neighbors       : 0

Number of MED neighbors        : 0

Number of CDP neighbors        : 0

Number of sent optional TLV    : 0

Number of received unknown TLV : 0

 

LLDP agent nearest-nontpmr:

Port status of LLDP            :
Enable

Admin status                   :
Disable

Trap flag                      : No

MED trap flag                  : No

Polling interval               : 0s

Number of LLDP neighbors       : 0

Number of MED neighbors        : 0

Number of CDP neighbors        : 0

Number of sent optional TLV    : 1

Number of received unknown TLV : 0

 

LLDP agent nearest-customer:

Port status of LLDP            :
Enable

Admin status                   :
Disable

Trap flag                      : No

MED trap flag                  : No

Polling interval               : 0s

Number of LLDP neighbors       : 0

Number of MED neighbors        : 0

Number of CDP neighbors        : 0

Number of sent optional TLV    : 16

Number of received unknown TLV : 0

### Example: Configuring CDP-compatible LLDP

#### Network configuration

As shown in [Figure 6](#_Ref214856444), Ten-GigabitEthernet 1/0/1 and Ten-GigabitEthernet 1/0/2 of Switch A are
each connected to a Cisco IP phone, which sends tagged voice traffic. 

Configure voice VLAN 2 on Switch A. Enable
CDP compatibility of LLDP on Switch A to allow the Cisco IP phones to
automatically configure the voice VLAN. The voice VLAN feature performs the
following operations: 

·     Confines the voice traffic to the voice VLAN. 

·     Isolates the voice traffic from other types of
traffic. 

Figure 6 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11704453_x_Img_x_png_5_2215984_294551_0.png)

‌

#### Procedure

**1\.**Configure a voice VLAN on Switch A:

\# Create VLAN 2\. 

\<SwitchA\> system-view

\[SwitchA] vlan 2

\[SwitchA-vlan2] quit

\# Set the link type of Ten-GigabitEthernet 1/0/1 and Ten-GigabitEthernet 1/0/2 to trunk, and
enable voice VLAN on them. 

\[SwitchA] interface ten-gigabitethernet 1/0/1

\[SwitchA-Ten-GigabitEthernet1/0/1] port link-type
trunk

\[SwitchA-Ten-GigabitEthernet1/0/1] voice-vlan 2 enable

\[SwitchA-Ten-GigabitEthernet1/0/1] quit

\[SwitchA] interface ten-gigabitethernet 1/0/2

\[SwitchA-Ten-GigabitEthernet1/0/2] port link-type
trunk

\[SwitchA-Ten-GigabitEthernet1/0/2] voice-vlan 2 enable

\[SwitchA-Ten-GigabitEthernet1/0/2] quit

**2\.**Configure CDP-compatible LLDP on Switch A:

\# Enable LLDP globally, and enable CDP
compatibility globally. 

\[SwitchA] lldp global enable 

\[SwitchA] lldp compliance cdp

\# Enable LLDP on Ten-GigabitEthernet 1/0/1. By default, LLDP
is enabled on ports. 

\[SwitchA] interface ten-gigabitethernet 1/0/1

\[SwitchA-Ten-GigabitEthernet1/0/1] lldp enable

\# Configure LLDP to operate in TxRx mode
on Ten-GigabitEthernet 1/0/1. 

\[SwitchA-Ten-GigabitEthernet1/0/1] lldp admin-status
txrx

\# Configure CDP-compatible LLDP to
operate in TxRx mode on Ten-GigabitEthernet 1/0/1.

\[SwitchA-Ten-GigabitEthernet1/0/1] lldp compliance
admin-status cdp txrx

\[SwitchA-Ten-GigabitEthernet1/0/1] quit

\# Enable LLDP on Ten-GigabitEthernet 1/0/2. By default, LLDP
is enabled on ports. 

\[SwitchA] interface ten-gigabitethernet 1/0/2

\[SwitchA-Ten-GigabitEthernet1/0/2] lldp enable

\# Configure LLDP to operate in TxRx mode
on Ten-GigabitEthernet 1/0/2. 

\[SwitchA-Ten-GigabitEthernet1/0/2] lldp admin-status
txrx

\# Configure CDP-compatible LLDP to
operate in TxRx mode on Ten-GigabitEthernet 1/0/2. 

\[SwitchA-Ten-GigabitEthernet1/0/2] lldp compliance
admin-status cdp txrx

\[SwitchA-Ten-GigabitEthernet1/0/2] quit

#### Verifying the configuration

\# Verify that Switch A has completed the
following operations:

·     Discovering the IP phones connected to Ten-GigabitEthernet 1/0/1 and Ten-GigabitEthernet 1/0/2. 

·     Obtaining IP phone information. 

\[SwitchA] display lldp
neighbor-information

 

CDP neighbor-information of port 1\[Ten-GigabitEthernet1/0/1]:

LLDP agent nearest-bridge:

 CDP neighbor index  : 1

 Chassis ID          :
SEP00141CBCDBFE 

 Port ID             : Port 1

 

CDP neighbor-information of port 2\[Ten-GigabitEthernet1/0/2]:

LLDP agent nearest-bridge:

 CDP neighbor index  : 2

 Chassis ID          : SEP00141CBCDBFF

 Port ID             : Port 1

 

 

