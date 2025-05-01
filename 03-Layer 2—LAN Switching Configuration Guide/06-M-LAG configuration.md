
# ConfiguringM-LAG

## About M-LAG

Multichassis Link Aggregation (M-LAG) virtualizes
two physical devices into one system through multichassis link aggregation. 

### M-LAG network model

As shown in[Figure 1](#_Ref468888329), M-LAG
virtualizes two devices into an M-LAG system, which connects to the remote aggregation
system through a multichassis aggregate link. To the remote aggregation system,
the M-LAG system is one device.

Figure 1 M-LAG network model

![](https://resource.h3c.com/en/202407/12/20240712_11704348_x_Img_x_png_0_2215976_294551_0.png)

 

The M-LAG member devices are M-LAG peers to
each other. For features that require centralized traffic processing (for
example, spanning tree), an M-LAG member device is assigned the primary or secondary
role based on its M-LAG role priority. The secondary M-LAG member device passes
the traffic of those features to the primary M-LAG member device for
processing. If the M-LAG member devices in an M-LAG system have the same M-LAG role
priority, the device with the lower bridge MAC address is assigned the primary
role.

M-LAG defines the following interface roles
for each M-LAG member device:

·     M-LAG interface—Layer 2 aggregate interface connected to the remote aggregation
system. M-LAG interfaces connected to the same remote aggregation system belong
to one M-LAG group. In [Figure 1](#_Ref468888329),
Bridge-Aggregation 1 on Device A and Bridge-Aggregation 2 on Device B belong to
the same M-LAG group. M-LAG interfaces in an M-LAG group form a multichassis
aggregate link.

·     Peer-link interface—Interface connected to the M-LAG peer for internal control. Each M-LAG
member device has only one peer-link interface. The peer-link interfaces of the
M-LAG member devices transmit M-LAG protocol packets and data packets through
the peer link established between them. An M-LAG system has only one peer link.

M-LAG member devices use a keepalive link
to monitor each other's state. For more information about the keepalive
mechanism, see "[Keepalive and failover mechanism](#_Ref469494168)."

If a device is attached to only one of the M-LAG
member devices in an M-LAG system, that device is a single-homed device.

### DRCP

M-LAG uses H3C proprietary Distributed
Relay Control Protocol (DRCP) for multichassis link aggregation. DRCP runs on
the peer link and uses distributed relay control protocol data units (DRCPDUs)
to advertise the M-LAG configuration out of peer-link interfaces and M-LAG interfaces.


#### DRCP operating mechanism

M-LAG-enabled devices use DRCPDUs for the
following purposes:

·     Exchange DRCPDUs through M-LAG interfaces to
determine whether they can form an M-LAG system.

·     Exchange DRCPDUs through peer-link interfaces to
negotiate the peer link state.

#### DRCP timeout timers

DRCP uses a timeout mechanism to specify
the amount of time that a peer-link interface or M-LAG interface must wait to
receive DRCPDUs before it determines that the peer interface is down. This
timeout mechanism provides the following timer options: 

·     Short DRCP timeout timer, which is fixed at 3
seconds. If this timer is used, the peer interface sends one DRCPDU every
second.

·     Long DRCP timeout timer, which is fixed at 90
seconds. If this timer is used, the peer interface sends one DRCPDU every 30
seconds.

Short DRCP timeout timer enables the M-LAG member
devices to detect a peer interface down event more quickly than the long DRCP
timeout timer. However this benefit is at the expense of bandwidth and system
resources.

### Keepalive and failover mechanism

H3C provides proprietary keepalive
mechanism to detect the availability of the M-LAG member devices.

For the secondary M-LAG member device to monitor
the state of the primary device, you must establish a Layer 3 keepalive link
between the M-LAG member devices. 

The M-LAG member devices periodically send
keepalive packets over the keepalive link. If an M-LAG member device has not
received keepalive packets from the peer when the keepalive timeout timer
expires, it determines that the keepalive link is down. When both the keepalive
link and the peer link are down, an M-LAG member device acts depending on its
role.

·     If its role is primary, the device retains its
role as long as it has up M-LAG interfaces. If all its M-LAG interfaces are
down, its role becomes None.

·     If its role is secondary, the device takes over
the primary role and retains the role as long as it has up M-LAG interfaces. If
all its M-LAG interfaces are down, its role becomes None.

A device with the None role cannot send or
receive keepalive packets. Its keepalive link stays in the down state.

If the keepalive link is down while the peer link is up, the M-LAG member
devices prompt you to check for keepalive link issues.

If the keepalive link is up while the peer
link is down, the M-LAG member devices elect a primary device based on the
information in the keepalive packets.

### MAD mechanism

#### M-LAG MAD

A multi-active collision occurs if the peer
link goes down while the keepalive link is up. To avoid network issues, M-LAG
MAD shuts down all network interfaces on the secondary M-LAG member device except
those manually or automatically excluded.

When the peer link comes up, the secondary M-LAG
member device starts a delay timer and begins to restore table entries
(including MAC address entries and ARP entries) from the primary M-LAG member
device. When the delay timer expires, the secondary M-LAG member device brings
up all network interfaces placed in M-LAG MAD DOWN state.

#### M-LAG MAD DOWN state persistence

Both of the M-LAG member devices might take
the primary role if both of them have M-LAG interfaces in up state after the
following series of events occur: 

**1\.**The peer link goes down while the keepalive
link is up. Then, M-LAG MAD shuts down all network interfaces on the secondary M-LAG
member device except those excluded from the shutdown action by  M-LAG MAD. 

**2\.**The keepalive link also goes down. Then, the
secondary M-LAG member device brings up the network interfaces in M-LAG MAD
DOWN state and sets its role to primary.

M-LAG MAD DOWN state persistence helps
avoid the forwarding issues that might occur in the multi-active situation that
occurs because the keepalive link goes down while the peer link is down.

### Device role calculation

As shown in [Figure 2](#_Ref53569324), the
role of an M-LAG member device can be primary, secondary, or none after role
calculation.

Figure 2 M-LAG role calculation process

![](https://resource.h3c.com/en/202407/12/20240712_11704349_x_Img_x_png_1_2215976_294551_0.png)

 

#### Role calculation rules

M-LAG calculates the roles of the M-LAG member
devices according to the following rules:

·     The M-LAG roles are determined upon M-LAG system
initialization triggered by M-LAG system setup or reboot of an M-LAG member
device.

·     If the peer link is up, the M-LAG member devices
exchange DRCPDUs over the peer link to determine which of them to take the
primary role.

·     If the peer link is down while the keepalive
link is up, the M-LAG member devices exchange keepalive packets over the link
to determine their roles.

·     If both the peer link and the keepalive link are
down, an M-LAG member device takes the primary role if it has available M-LAG interfaces.

#### Factors in role calculation

When the peer link or keepalive link is up,
the M-LAG member devices exchange the following information to determine which
of them takes the primary role:

**1\.**Status of M-LAG interfaces. An M-LAG member
device takes the primary role if it has available M-LAG interfaces. This status
is skipped if role calculation is performed over the peer link.

**2\.**Device roles before calculation. If one
device already has the primary role, the primary device retains its role. 

**3\.**M-LAG MAD DOWN state. If one device has not
placed any network interfaces in M-LAG MAD DOWN state, it becomes the primary
device.

**4\.**Health state. The healthier device takes the
primary role.

**5\.**M-LAG role priority. The device with higher M-LAG
role priority takes the primary role.

**6\.**Bridge MAC address. The device with a lower
bridge MAC address takes the primary role.

The device that has failed the election takes
the secondary role.

### M-LAG system setup process

As shown in [Figure 3](#_Ref471219177), two
devices perform the following operations to form an M-LAG system:

**1\.**Send DRCPDUs over the peer link to each other and
compare the DRCPDUs to determine the M-LAG system stackability and device
roles:

**a.**Compare the M-LAG system settings. The devices can
form an M-LAG system if they have consistent M-LAG
system settings.

**b.**Determine the device
roles as described in "[Device role calculation](#_Ref42589863)."

**c.**Perform configuration consistency check. For
more information, see "[Configuration consistency check](#_Ref492314698)."

**2\.**Send keepalive packets over the keepalive
link after primary M-LAG member election to verify that the peer system is
operating correctly.

**3\.**Synchronize configuration data by sending
DRCPDUs over the peer link. The configuration data includes MAC address entries
and ARP entries.

Figure 3 M-LAG system setup process

![](https://resource.h3c.com/en/202407/12/20240712_11704356_x_Img_x_png_2_2215976_294551_0.png)

 

### M-LAG standalone mode

The M-LAG member devices might both operate
with the primary role to forward traffic if they have M-LAG interfaces in up
state after the M-LAG system splits. M-LAG standalone mode helps avoid traffic
forwarding issues in this multi-active situation by allowing only the member
ports in the M-LAG interfaces on one member device to forward traffic.

The following information describes the
operating mechanism of this feature.

The M-LAG member devices change to M-LAG
standalone mode when they detect that both the peer link and the keepalive link
are down. In addition, the secondary M-LAG member device changes its role to
primary.

In M-LAG standalone mode, the LACPDUs sent
out of an M-LAG interface by each M-LAG member device contain the
interface-specific LACP system MAC address and LACP system priority. 

The Selected state of the member ports in
the M-LAG interfaces in an M-LAG group depends on their LACP system MAC address
and LACP system priority. If an M-LAG interface has a lower LACP system priority
value or LACP system MAC address, the member ports in that M-LAG interface become
Selected to forward traffic. If those Selected ports fail, the member ports in
the M-LAG interface on the other M-LAG member device become Selected to forward
traffic.

 

|  | NOTE:  An M-LAG member device changes to M-LAG standalone mode only when it detects that both the peer link and the keepalive link are down. It does not change to M-LAG standalone mode when the peer M-LAG member device reboots. |
| --- | --- |

 

### Configuration consistency check

During M-LAG system
setup, M-LAG member devices exchange the configuration and perform
configuration consistency check to verify their consistency in the following
configurations: 

·     Type 1 configuration—Settings that affect traffic forwarding of the M-LAG system. If an
inconsistency in type 1 configuration is detected, the secondary M-LAG member
device shuts down its M-LAG interfaces.

·     Type 2 configuration—Settings that affect only service features. If an inconsistency in
type 2 configuration is detected, the secondary M-LAG member device disables
the affected service features, but it does not shut down its M-LAG interfaces.

To prevent interface flapping, the M-LAG system
performs configuration consistency check when half the data restoration interval
elapses.

 

|  | NOTE:  The data restoration interval specifies the maximum amount of time for the secondary M-LAG member device to synchronize data with the primary M-LAG member device during M-LAG system setup. For more information, see "[Setting the data restoration interval](#_Ref469067215)." |
| --- | --- |

 

#### Type 1 configuration

Type 1 configuration consistency check is performed
both globally and on M-LAG interfaces. [Table 1](#_Ref492315143) and [Table 2](#_Ref492315157) show
settings that type 1 configuration contains.

Table 1 Global type 1 configuration

| Setting | Details |
| --- | --- |
| Peer-link interface link type | Peer-link interface link type, including access, hybrid, and trunk. || PVID on the peer-link interface | PVID on the peer-link interface. || Spanning tree state | ·     Global spanning tree state.  ·     VLAN-specific spanning tree state. M-LAG checks the VLAN-specific spanning tree state only when PVST is enabled. || Spanning tree mode | Spanning tree mode, including STP, RSTP, PVST, and MSTP. || MST region settings | ·     MST region name.  ·     MST region revision level.  ·     VLAN-to-MSTI mappings. |





 

Table 2 M-LAG interface type 1 configuration

| Setting | Details |
| --- | --- |
| Aggregation mode | Aggregation mode, including static and dynamic. || Spanning tree state | Interface-specific spanning tree state. || Link type | Interface link type, including access, hybrid, and trunk. || PVID | Interface PVID. |




 

#### Type 2 configuration

Type 2 configuration consistency check is
performed both globally and on M-LAG interfaces. [Table 3](#_Ref492315190) and [Table 4](#_Ref492315195) show
settings that type 2 configuration contains.

Table 3 Global type 2 configuration

| Setting | Details |
| --- | --- |
| VLANs permitted by the peer-link interface | VLANs permitted by the peer-link interface.  The M-LAG system compares tagged VLANs prior to untagged VLANs. || VLAN interfaces | Up VLAN interfaces of which the VLANs contain the peer-link interface. || VLAN interface status | Whether a VLAN interface is in administratively down state. || IPv4 address of a VLAN interface | IPv4 address assigned to a VLAN interface. || IPv6 address of a VLAN interface | IPv6 address assigned to a VLAN interface. || Virtual IPv4 address of the VRRP group on a VLAN interface | Virtual IPv4 address of the VRRP group configured on a VLAN interface on the VRRP master. || Global BPDU guard | Global status of BPDU guard. || MAC aging timer | Aging timer for dynamic MAC address entries. || Global state of port security. | Whether port security is enabled globally. || Authentication load sharing mode for users on port security-enabled M-LAG interfaces | Authentication load sharing mode for users on port security-enabled M-LAG interfaces:  ·     Centralized—The primary M-LAG member device authenticates users.  ·     Local—Each M-LAG member device authenticates their local users.  ·     Odd-MAC—The local M-LAG member device authenticates odd-MAC users on all M-LAG interfaces of the M-LAG system.  ·     Even-MAC—The local M-LAG member device authenticates even-MAC users on all M-LAG interfaces of the M-LAG system. || MAC move | Status of the MAC move feature. || MAC move mode | Port security MAC move mode:  ·     Port—Allows an authenticated online user to move between ports on the device.  ·     VLAN—Allows an authenticated online user to move between VLANs on a trunk or hybrid port.  ·     All—Allows an authenticated online user to move between ports on the device or VLANs on a trunk or hybrid port. || State of traffic statistics for 802.1X and MAC authentication users | Whether traffic statistics collection is enabled globally for 802.1X and MAC authentication users. || Global state of 802.1X authentication | Whether 802.1X authentication is enabled globally. || 802.1X authentication method | 802.1X authentication method:  ·     Chap—Performs EAP termination and uses CHAP to communicate with the RADIUS server.  ·     Eap—Relays EAP packets and supports any of the EAP authentication methods to communicate with the RADIUS server.  ·     Pap—Performs EAP termination and uses PAP to communicate with the RADIUS server. || EAD rule timer of 802.1X authentication | EAD rule timer value for 802.1X authentication. || Handshake timer of 802.1X authentication | Handshake timer value for 802.1X authentication. || Offline detection timer of 802.1X authentication | Offline detection timer value for 802.1X authentication. || Quiet timer of 802.1X authentication | Quiet timer value for 802.1X authentication. || Periodic reauthentication timer of 802.1X authentication | Periodic reauthentication timer value for 802.1X authentication. || Server timeout timer of 802.1X authentication | Server timeout timer value for 802.1X authentication. || Client timeout timer of 802.1X authentication | Client timeout timer value for 802.1X authentication. || Username request timeout timer of 802.1X authentication | Username request timeout timer value for 802.1X authentication. || Aging timer for users in Auth-Fail VLANs of 802.1X authentication | Aging timer value for users in Auth-Fail VLANs of 802.1X authentication. || Aging timer for users in Auth-Fail VSIs of 802.1X authentication | Aging timer value for users in Auth-Fail VSIs of 802.1X authentication. || Aging timer for users in critical microsegments of 802.1X authentication | Aging timer value for users in critical microsegments of 802.1X authentication. || Aging timer for users in critical VLANs of 802.1X authentication | Aging timer value for users in critical VLANs of 802.1X authentication. || Aging timer for users in critical VSIs of 802.1X authentication | Aging timer value for users in critical VSIs of 802.1X authentication. || Aging timer for users in guest VLANs of 802.1X authentication | Aging timer value for users in guest VLANs of 802.1X authentication. || Aging timer for users in guest VSIs of 802.1X authentication | Aging timer value for users in guest VSIs of 802.1X authentication. || Global state of MAC authentication | Whether MAC authentication is enabled globally. || MAC authentication method | MAC authentication method:  ·     Chap—CHAP authentication.  ·     Pap—PAP authentication. || Offline detection timer of MAC authentication | Offline detection timer value for MAC authentication. || Quiet timer of MAC authentication | Quiet timer value for MAC authentication. || Periodic reauthentication timer of MAC authentication | Periodic reauthentication timer value for MAC authentication. || Server timeout timer of MAC authentication | Server timeout timer value for MAC authentication. || Aging timer for temporary MAC authentication users | Aging timer value for temporary MAC authentication users. || Aging timer for users in critical microsegments of MAC authentication | Aging timer value for users in critical microsegments of MAC authentication. || Aging timer for users in critical VLANs of MAC authentication | Aging timer value for users in critical VLANs of MAC authentication. || Aging timer for users in critical VSIs of MAC authentication | Aging timer value for users in critical VSIs of MAC authentication. || Aging timer for users in guest VLANs of MAC authentication | Aging timer value for users in guest VLANs of MAC authentication. || Aging timer for users in guest VSIs of MAC authentication | Aging timer value for users in guest VSIs of MAC authentication. || Aging timer for MAC address entries created for temporary MAC authentication users | Aging timer value for MAC address entries created for temporary MAC authentication users. || VSI name | Name of a VSI that has ACs on an M-LAG interface. || VXLAN ID | VXLAN ID of a VSI. || Gateway interface | VSI interface associated with a VSI. || VSI interface number | Number of a VSI interface. || MAC address of a VSI interface | MAC address assigned to a VSI interface. || IPv4 address of a VSI interface | IPv4 address assigned to a VSI interface. || IPv6 address of a VSI interface | IPv6 address assigned to a VSI interface. || Physical state of a VSI interface | Physical link state of a VSI interface. || Protocol state of a VSI interface | Data link layer state of a VSI interface. || IPCC-enabled queue | Queue with IPCC enabled. |





















































 

The device displays the following global
type 2 settings only when VLAN or VLAN interface configuration inconsistency
exists:

·     VLAN interface status.

·     IPv4 address of a VLAN interface.

·     IPv6 address of a VLAN interface.

·     Virtual IPv4 address of the VRRP group on a VLAN
interface.

Table 4 M-LAG interface type 2 configuration

| Setting | Details |
| --- | --- |
| VLANs permitted by an M-LAG interface | VLANs permitted by an M-LAG interface.  The M-LAG system compares tagged VLANs prior to untagged VLANs. || Using port speed as the prioritized criterion for reference port selection | Whether an M-LAG interface uses port speed as the prioritized criterion for reference port selection. || Ignoring port speed in setting the aggregation states of member ports | Whether an M-LAG interface ignores port speed in setting the aggregation states of member ports. || Root guard status | Status of root guard. || Port security mode | Port security mode:  ·     Autolearn.  ·     Mac-authentication.  ·     Mac-and-userlogin-secure-ext.  ·     Mac-else-userlogin-secure.  ·     Mac-else-userlogin-secure-ext.  ·     Secure.  ·     Userlogin.  ·     Userlogin-secure.  ·     Userlogin-secure-ext.  ·     Userlogin-secure-or-mac.  ·     Userlogin-secure-or-mac-ext.  ·     Userlogin-withoui. || Secure MAC address aging type | Secure MAC address aging type:  ·     Inactivity—Inactivity aging feature together with the aging timer.  ·     Periodical—Timer aging only. || State of 802.1X authentication on an interface | Whether 802.1X authentication is enabled on an interface. || 802.1X critical VSI name | Name of the 802.1X critical VSI. || 802.1X online user handshake | Status of the 802.1X online user handshake feature. || 802.1X multicast trigger | Status of the 802.1X multicast trigger feature. || 802.1X unicast trigger | Status of the 802.1X unicast trigger feature. || 802.1X authentication offline detection | State of 802.1X authentication offline detection on an M-LAG interface:  ·     Enabled.  ·     Disabled. || Periodic reauthentication timer of 802.1X authentication | Periodic reauthentication timer of 802.1X authentication. || 802.1X critical microsegment | 802.1X critical microsegment which an M-LAG interface is assigned to. || 802.1X authentication critical profile | 802.1X authentication critical profile. || 802.1X critical VLAN | 802.1X critical VLAN which an M-LAG interface is assigned to. || 802.1X packet detection | State of 802.1X packet detection. || 802.1X packet detection retries | Number of 802.1X packet detection retries. || State of MAC authentication on an interface | Whether MAC authentication is enabled on an interface. || MAC authentication critical microsegment ID | ID of the MAC authentication critical microsegment. This setting is supported only by the S5560X-HI and S6520X-HI switches. || MAC authentication critical VSI name | Name of the MAC authentication critical VSI. || MAC authentication URL user logoff | Whether to log off MAC authentication users that have been assigned authorization URLs and have not passed authentication on a port when the first user is assigned to the critical microsegment.  This setting is supported only by the S5560X-HI and S6520X-HI switches. || Parallel processing of MAC authentication and 802.1X authentication on a port | Status of parallel processing of MAC authentication and 802.1X authentication on a port. || MAC authentication offline detection | State of MAC authentication offline detection on an M-LAG interface:  ·     Enabled.  ·     Disabled. || MAC authentication VLAN mode for users moving from one VLAN to another | MAC authentication VLAN mode for users moving from one VLAN to another on an M-LAG interface:  ·     Single VLAN—Single-VLAN mode.  ·     Multiple VLAN—Multi-VLAN mode. || MAC authentication delay timer | MAC authentication delay timer value. || Periodic reauthentication timer of MAC authentication | Periodic reauthentication timer value for MAC authentication. || MAC authentication critical profile | MAC authentication critical profile name. || Critical VLAN of MAC authentication | Critical VLAN of MAC authentication. || MAC authentication packet detection | State of MAC authentication packet detection. || MAC authentication packet detection retries | Number of MAC authentication packet detection retries. || State of Web authentication on an interface | Whether Web authentication is enabled on an interface. || Web authentication Auth-Fail VLAN | Auth-Fail VLAN for Web authentication. || Primary Web server for Web authentication | Name of the primary Web server used for Web authentication. || Secondary Web server for Web authentication | Name of the secondary Web server used for Web authentication. || Online detection interval of Web authentication | Online detection interval of Web authentication. |




































 

### Dual-active gateways on an M-LAG system

When a Layer 3 network is dualhomed to an
M-LAG system, each M-LAG member device uses a logical interface as the gateway
interface for the Layer 3 network. Both logical interfaces are active to
perform Layer 3 forwarding, and they use the same IP address and MAC address.
When both links to the M-LAG system are operating correctly, the links load
share traffic to balance bandwidth usage. When one of the links fails, the M-LAG
system switches the traffic on the failed link to the other link to ensure high
availability.

Typically, dual-active gateways are
deployed if downstream devices run a dynamic routing protocol to communicate
with the M-LAG system. As shown in [Figure 4](#_Ref101344107),
IGP or BGP runs between Device C and the M-LAG system. To configure dual-active
VLAN interfaces, perform the following tasks on Device A and Device B:

**1\.**Create VLAN interfaces for the same VLAN,
VLAN-interface 100 for example, assign the same IPv4, IPv6, and MAC addresses
to the VLAN interfaces, and assign the M-LAG interfaces to that VLAN.

**2\.**Assign different M-LAG VIPs to the VLAN
interfaces.

Device A and Device B use their M-LAG VIPs
to set up IGP or BGP peer relationships with Device C and with each other.

Figure 4 Layer 3 access to an M-LAG system
through dynamic routing protocols

![](https://resource.h3c.com/en/202407/12/20240712_11704357_x_Img_x_png_3_2215976_294551_0.png)

 

### M-LAG sequence number check

M-LAG sequence number check protects M-LAG member
devices from replay attacks. 

With this feature enabled, the M-LAG member
devices insert a sequence number into each outgoing DRCPDU or keepalive packet
and the sequence number increases by 1 for each sent packet. When receiving a DRCPDU
or keepalive packet, the M-LAG member devices check its sequence number and
drop the packet if the check result is either of the following:

·     The sequence number of the packet is the same as
that of a previously received packet.

·     The sequence number of the packet is smaller
than that of the most recently received packet.

### M-LAG packet authentication

M-LAG packet authentication prevents DRCPDU
and keepalive packet tampering from causing link flapping. 

With this feature enabled, the M-LAG member
devices compute a message digest by using an authentication key for each
outgoing DRCPDU or keepalive packet and insert the message digest into the
packet. When receiving a DRCPDU or keepalive packet, an M-LAG member device
computes a message digest and compares it with the message digest in the
packet. If the message digests match, the packet passes authentication. If the
message digests do not match, the device drops the packet.

### M-LAGfailure handling mechanisms

#### M-LAG interface failure handling mechanism

When the M-LAG interface
of one M-LAG member device fails, the M-LAG system forwards traffic through the
other M-LAG member device.

As shown in [Figure 5](#_Ref475025734), Device
A and Device B form an M-LAG system, to which Device C is attached through a
multichassis aggregation. If traffic to Device C arrives at Device B after the M-LAG
interface connected Device B to Device C has failed, the M-LAG system forwards
the traffic as follows:

**1\.**Device B sends the traffic to Device A over
the peer link.

**2\.**Device A forwards the downlink traffic
received from the peer link to Device C.

After the faulty M-LAG interface comes up,
Device B forwards traffic to Device C through the M-LAG interface.

Figure 5 M-LAG interface failurehandling mechanism

![](https://resource.h3c.com/en/202407/12/20240712_11704358_x_Img_x_png_4_2215976_294551_0.png)

 

#### Peer link failure handling mechanism

As shown in[Figure 6](#_Ref121936325),
multi-active collision occurs if the IPL goes down while the keepalive link is
up. To avoid network issues, the secondary M-LAG device sets all network
interfaces to M-LAG MAD DOWN state, except for the interfaces excluded from the
shutdown action by M-LAG MAD.

In this situation, the primary M-LAG member
device forwards all traffic for the M-LAG system.

When the peer-link interface comes up, the
secondary M-LAG member device does not bring up the network interfaces
immediately. Instead, it starts a delay timer and begins to recover data from
the primary M-LAG member device. When the delay timer expires, the secondary M-LAG
member device brings up all network interfaces.

Figure 6 Peer link failure handling
mechanism

![](https://resource.h3c.com/en/202407/12/20240712_11704359_x_Img_x_png_5_2215976_294551_0.png)

 

#### Device failure handling mechanism

As shown in[Figure 7](#_Ref475025069), when
the primary M-LAG member device fails, the secondary M-LAG member device takes
over the primary role to forward all traffic for the M-LAG system. When the
faulty device recovers, it becomes the secondary M-LAG member device.

When the secondary M-LAG member device
fails, the primary M-LAG member device forwards all traffic for the M-LAG system.


Figure 7 Device failure handling mechanism

![](https://resource.h3c.com/en/202407/12/20240712_11704360_x_Img_x_png_6_2215976_294551_0.png)

 

#### Uplink failure handling mechanism

Uplink failure does not interrupt traffic
forwarding of the M-LAG system. As shown in [Figure 8](#_Ref518551592), when
the uplink of Device A fails, Device A passes traffic destined for the IP
network to Device B for forwarding. 

To enable faster traffic switchover in
response to an uplink failure and minimize traffic losses, configure Monitor
Link to associate the M-LAG interfaces with the uplink interfaces. When the
uplink interface of an M-LAG member device fails, that device shuts down its M-LAG
interface for the other M-LAG member device to forward all traffic of Device C.
For more information about Monitor Link, see High
Availability Configuration Guide.

Figure 8 Uplink failure handling mechanism

![](https://resource.h3c.com/en/202407/12/20240712_11704361_x_Img_x_png_7_2215976_294551_0.png)

 

### Mechanisms to handle concurrent peer link and keepalive link failures

When both the peer link and the keepalive
link are down, the M-LAG member devices handle this situation depending on your
configuration.

#### Default failure handling mechanism

[Figure 9](#_Ref37178473)
shows the default mechanism to handle peer link and keepalive link failures
when the M-LAG standalone mode and M-LAG MAD DOWN state persistency features
are not configured. 

·     If the peer link goes down while the keepalive
link is up, the M-LAG member devices negotiate their roles over the keepalive
link. M-LAG MAD shuts down all network interfaces on the secondary M-LAG member
device except those excluded from the shutdown action by M-LAG MAD. 

·     If the keepalive link goes down while the peer
link is down, the secondary M-LAG member device sets its role to primary and
brings up the network interfaces in M-LAG MAD DOWN state to forward traffic. In
this situation, both of the M-LAG member devices might operate with the primary
role to forward traffic. Forwarding errors might occur because the M-LAG member
devices cannot synchronize MAC address entries over the peer link. 

·     If the keepalive link is down before the peer
link goes down, M-LAG MAD will not place network interfaces in M-LAG MAD DOWN
state. Both M-LAG member devices can operate with the primary role to forward traffic.

Figure 9 Default failure handling mechanism

![](https://resource.h3c.com/en/202407/12/20240712_11704362_x_Img_x_png_8_2215976_294551_0.png)

 

#### Failure handling mechanism with M-LAG MAD DOWN state persistence

[Figure 10](#_Ref42593360)
shows the mechanism to handle peer link and keepalive link failures when the M-LAG
MAD DOWN state persistence feature is configured. 

·     If the peer link goes down while the keepalive
link is up, the M-LAG member devices negotiate their roles over the keepalive
link. M-LAG MAD shuts down all network interfaces on the secondary M-LAG member
device except those excluded from the shutdown action by M-LAG MAD. 

·     If the keepalive link goes down while the peer
link is down, the secondary M-LAG member device sets its role to primary, but
it does not bring up the network interfaces in M-LAG MAD DOWN state. Only the
original primary member device can forward traffic. 

·     If the keepalive link is down before the peer
link goes down, M-LAG MAD will not place network interfaces in M-LAG MAD DOWN
state. Both M-LAG member devices can operate with the primary role to forward traffic.

Figure 10 Failure handling mechanism with M-LAG
MAD DOWN state persistence

![](https://resource.h3c.com/en/202407/12/20240712_11704363_x_Img_x_png_9_2215976_294551_0.png)

 

As shown in [Figure 11](#_Ref37179551), you
can bring up the interfaces in M-LAG MAD DOWN state on the secondary M-LAG member
device for it to forward traffic if the following conditions exist:

·     Both the peer link and the keepalive link are
down.

·     The primary M-LAG member device fails or its M-LAG
interface fails.

Figure 11 Bringing up the interfaces in M-LAG
MAD DOWN state

![](https://resource.h3c.com/en/202407/12/20240712_11704350_x_Img_x_png_10_2215976_294551_0.png)

 

#### Failure handling mechanism with M-LAG standalone mode

[Figure 12](#_Ref42592146)
shows the mechanism to handle peer link and keepalive link failures when the M-LAG
standalone mode feature is configured. 

·     If the peer link goes down while the keepalive
link is up, the M-LAG member devices negotiate their roles over the keepalive
link. M-LAG MAD shuts down all network interfaces on the secondary M-LAG member
device except those excluded from the shutdown action by M-LAG MAD. 

·     If the keepalive link goes down while the peer
link is down, both M-LAG member devices change to M-LAG standalone mode. The
secondary M-LAG member device sets its role to primary and brings up its
network interfaces in M-LAG MAD DOWN state. In M-LAG standalone mode, only the
aggregation member ports on one M-LAG member device can become Selected to forward
traffic. For more information about how M-LAG standalone mode operates, see
"[M-LAG standalone mode](#_Ref114076219)."

·     If the keepalive link is down before the peer
link goes down, both M-LAG member devices change to M-LAG standalone mode. 

Figure 12 Failure handling mechanism with M-LAG
standalone mode

![](https://resource.h3c.com/en/202407/12/20240712_11704351_x_Img_x_png_11_2215976_294551_0.png)

 

### Protocols and standards

IEEE P802.1AX-REV™/D4.4c,
Draft Standard for Local and Metropolitan Area Networks

## Restrictions and guidelines: M-LAG configuration

### Software version requirements

The M-LAG member devices in an M-LAG system
must use the same software version.

During an upgrade, the CLI of the peer M-LAG
member device is locked after the local M-LAG member device is upgraded and
rebooted. You cannot operate the CLI of the peer M-LAG member device. To unlock
the CLI of the peer M-LAG member device, perform a primary/secondary role
switchover or restart the M-LAG process of the device.

### M-LAG configuration

For the M-LAG member devices to be
identified as one M-LAG system, you must configure the same M-LAG system MAC
address and M-LAG system priority on them. You must assign different M-LAG
system numbers to the M-LAG member devices.

The M-LAG interfaces in the same M-LAG
group must use different LACP system MAC addresses.

To ensure correct forwarding, delete M-LAG
configuration from an M-LAG member device if it leaves its M-LAG system.

As a best practice to reduce the impact of
interface flapping on upper-layer services, use the link-delay command to configure the same link delay settings on the peer-link
interfaces.

In an M-LAG system , two peer-link
interfaces must have the same configuration for the maximum jumbo frame length.

### Compatibility with other features

For correct traffic forwarding, make sure
the M-LAG member devices are consistent in service feature settings.

#### IRF

M-LAG cannot work correctly on an IRF
fabric. Do not configure M-LAG on an IRF fabric. For more information about
IRF, see Virtual Technologies Configuration Guide.

#### MAC address table

If the M-LAG system has a large number of
MAC address entries, set the MAC aging timer to a higher value than 20 minutes
as a best practice. To set the MAC aging timer, use the mac-address
timer command. 

To ensure correct Layer 3 unicast
forwarding, disable static source check on aggregate interfaces acting as
peer-link interfaces by using the undo mac-address static source-check enable command. 

The MAC address learning feature is
disabled on the peer-link interface.

For more information about the MAC address
table, see "Configuring the MAC address table."

#### Ethernet link aggregation

Do not configure automatic link aggregation
on an M-LAG system.

The aggregate interfaces in an S-MLAG group
cannot be used as M-LAG interfaces or peer-link interfaces.

You cannot configure link aggregation
management subnets on an M-LAG system.

When you configure an M-LAG interface,
follow these restrictions and guidelines:

·     The link-aggregation selected-port maximum and link-aggregation selected-port minimum commands do not take effect on an M-LAG interface.

·     If you execute the display
link-aggregation verbose command for an M-LAG
interface, the displayed system ID contains the M-LAG system MAC address and
the M-LAG system priority.

·     If the reference port is a member port of an
M-LAG interface, the display link-aggregation verbose command displays the reference port on both M-LAG member devices.

For more information about Ethernet link
aggregation, see "Configuring Ethernet link aggregation."

#### Port isolation

Do not assign M-LAG interfaces or peer-link
interfaces to a port isolation group. For more information about port
isolation, see "Configuring port isolation."

#### Loop detection

Member devices in an M-LAG system must have
the same loop detection configuration. For information about loop detection,
see "Configuring loop detection."

#### Spanning tree

When the spanning tree protocol is enabled
for an M-LAG system, follow these restrictions and guidelines:

·     Make sure the M-LAG member devices have the same
spanning tree configuration. Violation of this rule might cause network
flapping. The configuration includes:

¡     Global
spanning tree configuration.

¡     Spanning
tree configuration on the peer-link interface.

¡     Spanning
tree configuration on M-LAG interfaces.

·     Peer-link interfaces of the M-LAG system do not
participate in spanning tree calculation.

·     The M-LAG member devices still use the M-LAG system
MAC address after the M-LAG system splits, which will cause spanning tree
calculation issues. To avoid the issues, enable M-LAG standalone mode on the M-LAG
member devices before the M-LAG system splits.

For more information about spanning tree,
see "Configuring spanning tree."

#### ICMP

An M-LAG system does not support sending
ICMPv4 or ICMPv6 redirect messages. These features are configurable with the ip redirects
enable and ipv6 redirects enable commands.

#### DHCP snooping

Do not use DHCP snooping and QinQ together
on an M-LAG system. DHCP snooping cannot operate correctly when QinQ is
configured.

#### AAA

On an M-LAG system, you must specify a
virtual IP address as the source IP address of outgoing RADIUS packets. 

When RADIUS DAS is enabled, an M-LAG system
does not support shutting down or rebooting the access ports for 802.1X
authentication users or reauthenticating the users through CoA messages. 

For more information about AAA, see Security Configuration Guide.

#### Portal

Before you configure portal authentication
to support M-LAG, you must complete the following tasks on both the primary and
secondary M-LAG member devices:

·     Enable portal authentication.

·     Configure IPv4 portal authentication or IPv6
portal authentication.

·     Set the portal authentication mode.

·     Enable or disable MAC-based quick portal
authentication.

·     Set the free-traffic threshold.

·     Enable portal fail-permit for a portal
authentication server.

·     Configure the URL of the portal Web server.

·     Configure portal user roaming.

·     Enable or disable portal authentication to
support IPv4/IPv6 dual stack.

Make sure the above configuration on both
the primary and secondary M-LAG member devices is the same.

For information about portal configuration,
see Security Configuration Guide.

#### Port security

In an M-LAG system, the M-LAG member
devices exchange configuration information with each other to detect whether
configuration conflicts exist. The detection does not affect packet forwarding
on the M-LAG member devices. If configuration conflicts exist, the M-LAG member
devices do not allow new users to come online.

For information about port security
configuration, see Security Configuration Guide.

#### CFD

Do not use the MAC address of a remote MEP
for CFD tests on peer-link interfaces. These tests cannot work on peer-link
interfaces. For more information about CFD, see High Availability
Configuration Guide.

#### VRRP

If you use M-LAG and VRRP together, make
sure the keepalive hold timer is shorter than the interval at which the VRRP
master sends VRRP advertisements. Violation of this restriction might cause a
VRRP master/backup switchover to occur before peer link failure is confirmed.
To set the interval at which the VRRP master sends VRRP advertisements, use the
vrrp
vrid timer advertise or vrrp ipv6 vrid
timer advertise command. For more information
about the commands, see High Availability Command
Reference.

#### Mirroring

For a mirroring group, do not assign the
source port to an aggregation group other than the one that accommodates the
destination port, egress port, or reflector port. If the source port is in a
different aggregation group than the other ports, mirrored LACPDUs will be
transmitted between the aggregation groups and cause aggregate interface
flapping.

#### VXLAN and EVPN

For information about VXLAN and EVPN
restrictions, see VXLAN Configuration Guide and
EVPN VXLAN configuration in EVPN Configuration Guide.

### Support for DRNI commands

The M-LAG feature was named DRNI in earlier
software versions. For compatibility with earlier software versions, the device
supports both M-LAG and DRNI commands.

Table 5 Difference between M-LAG and DRNI commands

| Feature name | Keywords | Example || M-LAG | m-lag, mlag, peer-link | m-lag system-number system-number  As a best practice, use M-LAG commands. || DRNI | drni, drmac, ipp | drni system-number system-number  To use configuration files created from an earlier software version, use DRNI commands.  The system recognizes only the complete syntaxes of DRNI commands. It does not support displaying available keywords and arguments in response to a question mark (?) or automatically completing the last keyword or argument in response to the Tab key.  If you execute a DRNI command, the system converts it into the corresponding M-LAG command and saves the M-LAG command in the configuration file. |


| --- | --- | --- | --- | --- | --- | --- | --- | --- |

 

M-LAG and DRNI commands do not differ in
the configuration method or functionality. For more information about the
keyword differences, see "[Comparison between M-LAG and DRNI commands](#_Ref122427960)."

## M-LAG tasks at a glance

To configure M-LAG, perform the following
tasks:

**1\.**[Configuring M-LAG system settings](#_Ref475039399)

¡     [Configuring the M-LAG system MAC address](#_Ref469067135)

¡     [Setting the M-LAG system number](#_Ref469067141)

¡     [Setting the M-LAG system priority](#_Ref469067147)

**2\.**[Setting the M-LAG role priority of the
device](#_Ref480272925)

**3\.**(Optional.) [Enabling M-LAG standalone mode on an M-LAG
member device](#_Ref37184867)

**4\.**[Configuring M-LAG keepalive settings](#_Ref475036111)

¡     [Configuring M-LAG keepalive packet
parameters](#_Ref475039259)

¡     [Setting the M-LAG keepalive interval and
timeout timer](#_Ref469421999)

¡     [Associating the keepalive link with a track
entry](#_Ref119503994)

**5\.**[Configuring M-LAG MAD](#_Ref8629673)

¡     [Configuring the default M-LAG MAD action on
network interfaces](#_Ref37184886)

¡     [Excluding an interface from the shutdown
action by M-LAG MAD](#_Ref37184892)

¡     [Excluding all logical interfaces from the
shutdown action by M-LAG MAD](#_Ref37184897)

¡     [Specifying interfaces to be shut down by
M-LAG MAD when the M-LAG system splits](#_Ref37184903)

¡     [Enabling M-LAG MAD DOWN state persistence](#_Ref42593425)

**6\.**Configuring interfaces on the M-LAG system

¡     [Configuring an M-LAG interface](#_Ref469067154)

¡     [Specifying a Layer 2 aggregate interface or
VXLAN tunnel interface as the](#_Ref469067160) 

¡     (Optional.) [Enabling the short DRCP timeout timer on
the peer-link interface or an M-LAG interface](#_Ref469067171)

**7\.**(Optional.) [Enabling the peer-link interface to retain
MAC address entries for down single-homed devices](#_Ref37184934)

**8\.**(Optional.) [Assigning a M-LAG virtual IP address to an
interface](#_Ref61797076)

Configure M-LAG VIPs for devices to
communicate with the M-LAG system by using dynamic routing protocols.

**9\.**(Optional.) [Configuring M-LAG extra VLANs](#_Ref119504007)

**10\.**(Optional.) Configuring configuration
consistency check

¡     [Setting the mode of configuration consistency
check](#_Ref22403337)

¡     (Optional.)
[Disabling configuration
consistency check](#_Ref495327531)

Configuration consistency check might
fail when you upgrade the M-LAG member devices in an M-LAG system. To prevent
the M-LAG system from falsely shutting down M-LAG interfaces, temporarily
disable configuration consistency check.

**11\.**Configuring M-LAG timers

¡     (Optional.) [Setting the keepalive hold timer for
identifying the cause of peer link down events](#_Ref469411378)

¡     [Configuring M-LAG system auto-recovery](#_Ref479256688)

¡     (Optional.) [Setting the data restoration interval](#_Ref469067215)

**12\.**(Optional.) Configuring M-LAG security
features

¡     [Enabling M-LAG sequence number check](#_Ref22403350)

¡     [Enabling M-LAG packet authentication](#_Ref22403355)

## Configuring M-LAG system settings

### Configuring the M-LAG system MAC address

#### Restrictions and guidelines

On an M-LAG system, M-LAG interfaces in the
same M-LAG group must use the same LACP system MAC address. As a best practice,
use the bridge MAC address of one M-LAG member device as the M-LAG system MAC
address.

Changing the M-LAG system MAC address
causes M-LAG system split. When you perform this task on a live network, make
sure you are fully aware of its impact.

You can configure the M-LAG system MAC
address on an aggregate interface only after it is configured as an M-LAG interface.

You can configure the M-LAG system MAC
address globally and in aggregate interface view. The global M-LAG system MAC
address takes effect on all aggregation groups. On an aggregate interface, the interface-specific
M-LAG system MAC address takes precedence over the global M-LAG system MAC
address.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Configure the M-LAG system MAC address.

m-lag system-mac { bridge-address
\|
mac-address }

By default, the M-LAG system MAC address
is not configured.

**3\.**Enter Layer 2 aggregate interface view.

interface bridge-aggregation interface-number

**4\.**Set the M-LAG system MAC address on the
aggregate interface.

port m-lag system-mac mac-address

By default, the M-LAG system MAC address
is not configured.

### Setting the M-LAG system number

#### Restrictions and guidelines

Changing the M-LAG system number causes M-LAG
system split. When you perform this task on a live network, make sure you are
fully aware of its impact.

You must assign different M-LAG system
numbers to the M-LAG member devices in an M-LAG system.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Set the M-LAG system number.

m-lag system-number system-number

By default, the M-LAG system number is
not set. 

### Setting the M-LAG system priority

#### About this task

An M-LAG system uses its M-LAG system
priority as the system LACP priority to communicate with the remote aggregation
system. 

#### Restrictions and guidelines

Changing the M-LAG system priority in
system view causes M-LAG system split. When you perform this task on a live
network, make sure you are fully aware of its impact.

You must configure the same M-LAG system
priority for the M-LAG interfaces in the same M-LAG group.

You can configure the M-LAG system priority
on an aggregate interface only after it is configured as an M-LAG interface.

You can configure the M-LAG system priority
globally and in aggregate interface view. The global M-LAG system priority
takes effect on all aggregation groups. On an aggregate interface, the interface-specific
M-LAG system priority takes precedence over the global M-LAG system priority.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Set the M-LAG system priority.

m-lag system-priority system-priority

By default, the M-LAG system priority is
32768\.

**3\.**Enter Layer 2 aggregate interface view.

interface bridge-aggregation interface-number

**4\.**Set the M-LAG system priority on the
aggregate interface.

port m-lag system-priority priority

By default, the M-LAG system priority is
32768\.

## Setting the M-LAG role priority of the device

#### About this task

M-LAG assigns the primary or secondary role
to an M-LAG member device based on its M-LAG role priority. The smaller the
priority value, the higher the priority. If the M-LAG member devices in an
M-LAG system use the same M-LAG role priority, the device with the lower bridge
MAC address is assigned the primary role.

#### Restrictions and guidelines

To prevent a primary/secondary role
switchover from causing network flapping, avoid changing the M-LAG priority
assignment after the M-LAG system is established.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Set the M-LAG role priority of the device.

m-lag role priority priority-value

By default, the M-LAG role priority of
the device is 32768\.

## Enabling M-LAG standalone mode on an M-LAG member device

#### About this task

Perform this task to avoid forwarding
issues in the multi-active situation that might occur after both the peer link
and the keepalive link are down. 

M-LAG standalone mode helps avoid traffic
forwarding issues in this multi-active situation by allowing only the member
ports in the M-LAG interfaces on one member device to forward traffic. For more
information about this mode, see "[M-LAG standalone mode](#_Ref42593114)."

When you configure this feature, you can
configure a delay to prevent an unnecessary mode change because of transient
link down issues.

#### Restrictions and guidelines

An M-LAG member device does not enter M-LAG
standalone mode if the M-LAG peer reboots.

As a best practice, enable M-LAG standalone
mode on both M-LAG member devices.

Before you enable M-LAG standalone mode on an
M-LAG member device, make sure its LACP system priority is higher than that of
the remote aggregation system. This restriction ensures that the reference port
is on the remote aggregation system and prevents the interfaces attached to the
M-LAG system from flapping.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enable M-LAG standalone mode.

m-lag standalone enable \[ delay delay-time ]

By default, M-LAG standalone mode is
disabled.

## Configuring M-LAG keepalive settings

### Restrictions and guidelines for configuring M-LAG keepalive settings

As a best practice, estalibsh a direct physical
link between the M-LAG member devices as the keepalive link and use that link
only for keepalive detection purposes. Make sure the link has both Layer 2 and Layer
3 connectivity.

Make sure the two ends use the same
keepalive settings. M-LAG member devices check the peer keepalive settings for
consistency. If an inconsistency is found, the device will prompt for
configuration revision.

### Configuring M-LAG keepalive packet parameters

#### About this task

Perform this task to specify the parameters
for sending M-LAG keepalive packets, such as its source and destination IP
addresses.

The device accepts only keepalive packets that
are sourced from the specified destination IP address. The keepalive link goes
down if the device receives keepalive packets sourced from any other IP
address.

#### Restrictions and guidelines

Make sure the source and destination IP
addresses of M-LAG keepalive packets have Layer 3 connectivity.

Make sure the M-LAG member devices in an
M-LAG system use the same keepalive destination UDP port.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Configure M-LAG keepalive packet parameters.

m-lag keepalive { ip \| ipv6 } destination { ipv4-address \| ipv6-address } \[ source { ipv4-address \| ipv6-address } \| udp-port udp-number \| vpn-instance
vpn-instance-name ] \*

By default, the M-LAG keepalive packet
parameters are not configured. If you do not specify a source IP address or
destination UDP port when you execute this command, the IP address of the
outgoing interface and UDP port 6400 are used, respectively.

### Setting the M-LAG keepalive interval and timeout timer

#### About this task

The device sends keepalive packets at the
specified interval to its M-LAG peer. If the device has not received a
keepalive packet from the M-LAG peer before the keepalive timeout timer
expires, the device determines that the keepalive link is down.

#### Restrictions and guidelines

The local M-LAG keepalive timeout timer
must be two times the M-LAG keepalive interval of the peer at minimum.

Configure the same M-LAG keepalive interval
on the M-LAG member devices in the M-LAG system. 

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Set the M-LAG keepalive interval and timeout
timer.

m-lag keepalive interval interval \[ timeout timeout ]

By default, the M-LAG keepalive interval
is 1000 milliseconds, and the M-LAG keepalive timeout timer is 5 seconds.

### Associating the keepalive link with a track entry

#### About this task

Associate a track entry with the keepalive
link to fast detect state changes of the keepalive link. For more information
about track entries, see Track configuration in High Availability
Configuration Guide.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Associate the keepalive link with a track
entry.

m-lag keepalive track track-entry-number

By default, no track entry is associated
with the keepalive link.

## Configuring M-LAG MAD

### About this task

#### M-LAG MAD configuration methods

When you configure M-LAG MAD, use either of
the following methods:

·     To shut down all network interfaces on the
secondary M-LAG member device except a few special-purpose interfaces that must
be retained in up state:

¡     Set
the default M-LAG MAD action to M-LAG MAD DOWN. For more information, see
"[Configuring the default M-LAG MAD action on
network interfaces](#_Ref10635104)."

¡     Exclude
interfaces from being shut down by M-LAG MAD. For more information, see "[Excluding an interface from the shutdown
action by M-LAG MAD](#_Ref10623785)."

This method is applicable to most network
environments.

·     To have the secondary M-LAG member device retain
a large number of interfaces in up state and shut down the remaining interfaces:

¡     Set the
default M-LAG MAD action to NONE. For more information, see "[Configuring the default M-LAG MAD action on
network interfaces](#_Ref10635104)."

¡     Specify
network interfaces that must be shut down by M-LAG MAD. For more information,
see "[Specifying interfaces to be shut down by
M-LAG MAD when the M-LAG system splits](#_Ref10634814)."

Use this method if you must retain a
large number of logical interfaces (for example, VLAN, aggregate, loopback, and
tunnel interfaces) in up state.

#### List of automatically included interfaces

M-LAG MAD will always shut down the ports
in the system-configured included port list if the device acts as the secondary
M-LAG member device when the M-LAG system splits. 

This list contains aggregation member ports
of M-LAG interfaces. To identify system-configured included ports, execute the display m-lag
mad verbose command.

#### List of automatically excluded interfaces

M-LAG MAD will not shut down the ports in
the following list when the M-LAG system splits:

·     System-configured excluded port list in M-LAG
MAD:

¡     Peer-link
interface.

¡     Aggregation
member interfaces if a Layer 2 aggregate interface is used as the peer-link
interface.

¡     M-LAG
interfaces.

¡     Management
interfaces.

To identify these interfaces, execute the
display
m-lag mad verbose command.

·     Network interfaces used for special purposes,
including:

¡     Interfaces
placed in a loopback test by using the loopback
command. For more information about this command, see Ethernet interface
commands in Interface Command Reference. 

¡     Interfaces
in a mirroring group. 

¡     Interfaces
forced to stay up by using the port-up mode
command. For more information about this command, see Ethernet interface
commands in Interface Command Reference. 

### Restrictions and guidelines for M-LAG MAD

When the M-LAG system splits, M-LAG MAD takes
the same action on an aggregate interface and its member ports. For example, if
M-LAG MAD shuts down an aggregate interface, it also shuts down the member
ports of the aggregate interface.

If you specify an aggregation member port
in the m-lag mad include interface or m-lag mad
exclude interface command, the configuration
takes precedence over the M-LAG MAD action on the aggregate interface.

### Configuring the default M-LAG MAD action on network interfaces

#### About this task

You can configure M-LAG MAD to take either
of the following default actions on network interfaces if the device acts as the
secondary M-LAG member device when the M-LAG system splits:

·     M-LAG MAD DOWN—M-LAG MAD will shut down all network interfaces on the secondary M-LAG
member device when the M-LAG system splits, except the interfaces excluded
manually or by the system.

·     NONE—M-LAG MAD will not shut down any network interfaces when the M-LAG system
splits, except the interfaces configured manually or by the system to be shut
down by M-LAG MAD.

#### Restrictions and guidelines

The M-LAG MAD DOWN action will not take
effect on the interfaces listed in "[List of automatically excluded interfaces](#_Ref10638945)."

The M-LAG MAD DOWN action will always take
on the interfaces listed in "[List of automatically included interfaces](#_Ref10639022)," even if the default M-LAG MAD action is NONE.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Configure the default M-LAG MAD action to
take on network interfaces on the secondary M-LAG member device when the M-LAG system
splits.

m-lag mad default-action { down \| none }

By default, M-LAG MAD shuts down network
interfaces on the secondary M-LAG member device. 

### Excluding an interface from the shutdown action by M-LAG MAD

#### About this task

By default, M-LAG MAD automatically excludes
the interfaces listed in "[List of automatically excluded interfaces](#_Ref10638945)" when it shuts down network interfaces on the secondary M-LAG member
device.

To specify additional interfaces that
cannot be shut down, perform this task.

You typically perform this task when the
default M-LAG MAD action is set to M-LAG MAD DOWN. 

#### Restrictions and guidelines

You must always exclude the following interfaces
from being shut down by M-LAG MAD:

·     For correct keepalive detection, you must
exclude the interfaces used for keepalive detection.

·     For M-LAG member devices to synchronize ARP and
ND entries, you must exclude the VLAN interfaces of the VLANs to which the M-LAG
interfaces and peer-link interfaces belong.

The M-LAG MAD DOWN action is always taken
on interfaces listed in "[List of automatically included interfaces](#_Ref10639022)." You cannot disable the action by excluding those interfaces.


To view interfaces excluded from the MAD
shutdown action, see the Excluded ports (user-configured)
field in the output from the display m-lag mad verbose command.

If you exclude an interface that is already
in M-LAG MAD DOWN state from the MAD shutdown action, the interface stays in
that state. It will not come up automatically.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Exclude an interface from the shutdown
action by M-LAG MAD.

m-lag mad exclude interface interface-type interface-number

By default, M-LAG MAD shuts down all
network interfaces when detecting a multi-active collision, except for the
network interfaces set by the system to not shut down.

### Excluding all logical interfaces from the shutdown action by M-LAG MAD

#### About this task

To retain a large number of logical
interfaces (for example, VLAN, aggregate, loopback, and tunnel interfaces) in
up state, you can exclude all logical interfaces from the shutdown action by
M-LAG MAD.

#### Restrictions and guidelines

The m-lag mad exclude interface and m-lag mad include interface commands take precedence over the m-lag mad exclude
logical-interfaces command.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Exclude all logical interfaces from the
shutdown action by M-LAG MAD.

m-lag mad exclude logical-interfaces

By default, M-LAG MAD shuts down all
network interfaces when it detects a multi-active collision, except for the
network interfaces set by the system to not shut down.

### Specifying interfaces to be shut down by M-LAG MAD when the M-LAG system splits

#### About this task

By default, M-LAG MAD automatically shuts
down the interfaces listed in "[List of automatically included interfaces](#_Ref10639022)" if the device is the secondary M-LAG member device when the M-LAG
system splits.

To specify additional interfaces to be shut
down by M-LAG MAD, perform this task.

You typically perform this task when the
default M-LAG MAD action is set to NONE.

#### Restrictions and guidelines

The M-LAG MAD DOWN action will not take
effect on the interfaces listed in "[List of automatically excluded interfaces](#_Ref10638945)."

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Specify interfaces to be shut down by M-LAG
MAD when the M-LAG system splits.

m-lag mad include interface interface-type interface-number

By default, the user-configured included
port list does not contain any ports. 

### Enabling M-LAG MAD DOWN state persistence

#### About this task

M-LAG MAD DOWN state persistence helps
avoid the multi-active situation by preventing the secondary M-LAG member
device from bringing up the network interfaces in M-LAG MAD DOWN state. For
more information about this feature, see "[M-LAG MAD DOWN state persistence](#_Ref61858024)" and "[Failure handling mechanism with M-LAG MAD
DOWN state persistence](#_Ref42593163)."

You can bring up the interfaces in M-LAG
MAD DOWN state on the secondary M-LAG member device for it to forward traffic
if the following conditions exist:

·     The primary M-LAG member device fails while the peer
link is down.

·     The M-LAG MAD DOWN state persists on the
secondary M-LAG member device.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enable M-LAG MAD DOWN state persistence.

m-lag mad persistent

By default, the secondary M-LAG member
device brings up interfaces in M-LAG MAD DOWN state when its role changes to
primary.

**3\.**(Optional.) Bring up the interfaces in M-LAG
MAD DOWN state.

m-lag mad restore

Execute this command only when both the peer
link and the keepalive link are down.

## Configuring an M-LAG interface

#### About this task

If an M-LAG group contains only one M-LAG interface,
that interface is called a single-homed M-LAG interface. By default, M-LAG does
not allow access through single-homed M-LAG interfaces, which means M-LAG MAD
shuts down an M-LAG interface if it is the only member in its M-LAG group.

To ensure traffic forwarding for a device single-homed
to an M-LAG interface, allow the M-LAG interface to be the single member in its
M-LAG group. M-LAG MAD will not shut down the single-homed M-LAG interface, and
the device will not perform configuration consistency check on the interface.

#### Restrictions and guidelines

The device can have multiple M-LAG interfaces.
However, you can assign a Layer 2 aggregate interface to only one M-LAG group.

A Layer 2 aggregate interface cannot
operate as both peer-link interface and M-LAG interface.

To improve forwarding efficiency, exclude
the M-LAG interface on the secondary M-LAG member device from the shutdown
action by M-LAG MAD. This action enables the M-LAG interface to forward traffic
immediately after a multi-active collision is removed without having to wait
for the secondary M-LAG member device to complete entry restoration.

To change the allow-single-member setting for a single-homed M-LAG interface, first execute the undo port m-lag
group command to remove it from its M-LAG group.

To prevent loops when you assign a
single-homed aggregate interface to an M-LAG group, use the following
procedure:

**1\.**Assign the aggregate interface to the M-LAG group.

**2\.**Assign ports to the aggregation group of the
aggregate interface.

When you remove a single-homed M-LAG interface
from its M-LAG group, use the following procedure:

**3\.**Remove the member ports from the aggregation
group of the M-LAG interface.

**4\.**Remove the M-LAG interface from the M-LAG group.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter Layer 2 aggregate interface view.

interface bridge-aggregation interface-number

**3\.**Assign the aggregate interface to an M-LAG group.

port m-lag group group-id \[
allow-single-member ]

As a best practice, specify the allow-single-member keyword for a dynamic aggregate interface.

## Specifying a Layer 2 aggregate interface or VXLAN tunnel interface as the peer-link interface

#### Restrictions and guidelines

A Layer 2 aggregate interface cannot
operate as both peer-link interface and M-LAG interface. Make sure the
bandwidth of the IPP is higher than that of an M-LAG interface.

An M-LAG member device can have only one peer-link
interface. 

If you specify an aggregate interface as a
peer-link interface, the device automatically assigns the aggregate interface
as a trunk port to all VLANs when the interface uses the default VLAN settings.
If the VLAN settings are not the default, the device does not change the VLAN
settings of the interface.

The device does not change the VLAN
settings of an aggregate interface when you remove its peer-link interface role.

As a best practice to reduce the impact of
interface flapping on upper-layer services, execute the link-delay command on the peer-link interface. For more information about this
command, see Ethernet link aggregation commands in Layer
2—LAN Switching Command Reference.

By default, MAC address learning is enabled
on the peer-link interface. This feature is not configurable on the peer-link
interface. For more information about the MAC address learning feature, see
"Configuring the MAC address table."

To prevent data synchronization failure,
you must set the same maximum jumbo frame length on the peer-link interfaces of
the M-LAG member devices. For more information about jumbo frames, see
"Configuring Ethernet link aggregation."

Do not use the MAC address of a remote MEP
for CFD tests on peer-link interfaces. These tests cannot work on peer-link
interfaces. For more information about CFD, see High Availability
Configuration Guide.

You must use the shutdown command to shut down an interface before you remove it from the
peer-link interface's aggregation group.

For M-LAG to operate correctly, you must
execute the undo mac-address static source-check enable command after assigning the peer-link interface role to an
interface. For more information about this command, see MAC address table
commands in Layer 2—LAN Switching Command Reference.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter interface view.

¡     Enter
Layer 2 aggregate interface view.

interface bridge-aggregation interface-number

**3\.**Specify the interface as the peer-link
interface.

port m-lag peer-link port-number

## Enabling the short DRCP timeout timer on the peer-link interface or an M-LAG interface

#### About this task

By default, the peer-link interface or an
M-LAG interface uses the 90-second long DRCP timeout timer. To detect peer
interface down events more quickly, enable the 3-second short DRCP timeout
timer on the interface.

#### Restrictions and guidelines

To avoid traffic interruption during an
ISSU or M-LAG process restart, disable the short DRCP timeout timer before you perform
an ISSU or M-LAG process restart. For more information about ISSU, see Fundamentals Configuration Guide.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter interface view.

¡     Enter
Layer 2 aggregate interface view.

interface bridge-aggregation interface-number

**3\.**Enable the short DRCP timeout timer.

m-lag drcp period short

By default, an interface uses the long
DRCP timeout timer (90 seconds).

## Enabling the peer-link interface to retain MAC address entries for down single-homed devices

#### About this task

When an M-LAG member device detects that
the link to a single-homed device goes down, the peer-link interface takes the
following actions:

·     Deletes the MAC address entries for the
single-homed device.

·     Sends a message to the peer peer-link interface
for it to delete the affected MAC address entries.

If the link to a single-homed device flaps
constantly, the peer-link interface repeatedly deletes and adds MAC address
entries for the device. This situation increases floods of unicast traffic
destined for the single-homed device.

To reduce flood traffic, enable the peer-link
interface to retain MAC address entries for single-homed devices. After the
links to single-homed devices go down, the affected MAC address entries age out
on expiration of the MAC aging timer instead of being deleted immediately. The
timer is set by using the mac-address timer
command. For more information about this command, see MAC address table
commands in Layer 2—LAN Switching Command Reference.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enable the peer-link interface to retain MAC
address entries for single-homed devices.

m-lag peer-link mac-address hold

By default, the peer-link interface does
not retain MAC address entries for single-homed devices when the devices go
down.

## Assigning a M-LAG virtual IP address to an interface

#### About this task

M-LAG virtual IP addresses (VIPs) allow
devices to communicate with the M-LAG system by using dynamic routing
protocols.

To ensure correct traffic forwarding,
assign M-LAG VIPs to the following interfaces on the M-LAG system:

·     VLAN interfaces that act as dual-active gateways
for the same VLAN.

·     Loopback interfaces that offer AAA and 802.1X
authentication services. For more information, see AAA configuration in Security Configuration Guide.

·     VSI interfaces that act as distributed EVPN
gateways. For more information, see EVPN VXLAN configuration in EVPN Configuration Guide.

When both M-LAG member devices act as
gateways for dualhomed user-side devices, the gateway interfaces (VLAN or VSI
interfaces) on the M-LAG member devices use the same IP address and MAC
address. In this scenario, the M-LAG member devices cannot set up neighbor
relationships with the user-side devices. To resolve this issue, assign VIPs to
the gateway interfaces and configure routing protocols such as BGP, OSPF, and
OSPFv3 to use the VIPs for neighbor relationship setup.

When dual-active gateways exist on the M-LAG
system, you must assign unique VIPs to the gateway interfaces on the M-LAG member
devices and configure both VIPs to be active. When you assign a virtual MAC
address to a VLAN interface, make sure the virtual MAC address is identical to
the MAC address assigned to the VLAN interface by using the mac-address command.

#### Restrictions and guidelines

When you assign multiple M-LAG VIPs to an
interface, follow these restrictions and guidelines:

·     You can assign a maximum of two virtual IPv4 or
IPv6 addresses to an interface.

·     If you configure different virtual MAC addresses
for a virtual IPv4 or IPv6 address, the most recent configuration takes effect.

·     You cannot configure the same virtual MAC
address for multiple virtual IPv4 or IPv6 addresses.

·     When you assign a virtual IPv4 or IPv6 address
to VLAN interfaces, you must configure the same virtual MAC address for the
virtual IPv4 or IPv6 address on both M-LAG member devices.

If you assign both virtual IPv4 and IPv6
addresses to VLAN interfaces, make sure the virtual IPv4 and IPv6 addresses
that use the same virtual MAC address are in the same state on the M-LAG member
devices.

#### Assigning M-LAG VIPs to a VLAN interface

**1\.**Enter system view.

system-view 

**2\.**Enter VLAN interface view.

interface vlan-interface interface-number

**3\.**Assign a virtual IPv4 address to the VLAN
interface.

port m-lag virtual-ip ipv4-address { mask-length \| mask } \[ active \| standby ] \[ virtual-mac mac-address ]

By default, no virtual IPv4 addresses are
assigned to interfaces.

**4\.**Assign a virtual IPv6 address to the VLAN
interface.

port m-lag ipv6 virtual-ip ipv6-address { prefix-length \[ active \| standby ] \[ virtual-mac mac-address ] \| link-local }

By default, no virtual IPv6 addresses are
assigned to interfaces.

#### Assigning M-LAG VIPs to a loopback interface

**1\.**Enter system view.

system-view 

**2\.**Enter loopback interface view.

interface loopback interface-number

**3\.**Assign a virtual IPv4 address to the loopback
interface.

port m-lag virtual-ip ipv4-address { mask-length \| mask } \[ active \| standby ]

By default, no virtual IPv4 addresses are
assigned to interfaces.

**4\.**Assign a virtual IPv6 address to the loopback
interface.

port m-lag ipv6 virtual-ip ipv6-address { prefix-length \[ active \| standby ] \| link-local }

By default, no virtual IPv6 addresses are
assigned to interfaces.

#### Assigning M-LAG VIPs to a VSI interface

**1\.**Enter system view.

system-view 

**2\.**Enter VSI interface view.

interface vsi-interface interface-number

**3\.**Assign a virtual IPv4 address to the VSI
interface.

port m-lag virtual-ip ipv4-address { mask-length \| mask } \[ active \| standby ]

By default, no virtual IPv4 addresses are
assigned to interfaces.

**4\.**Assign a virtual IPv6 address to the VSI
interface.

port m-lag ipv6 virtual-ip ipv6-address { prefix-length \[ active \| standby ] \| link-local }

By default, no virtual IPv6 addresses are
assigned to interfaces.

## Configuring M-LAG extra VLANs

#### About this task

For VLANs permitted only on non-M-LAG
interfaces, the device processes traffic locally and do not synchronize
forwarding entries such as MAC and ARP entries with the M-LAG peer device. For
the device to synchronize forwarding entries for such VLANs, configure them as
extra VLANs. The device will synchronize forwarding entries for the extra VLANs
even though they are not permitted on M-LAG interfaces, as shown in [Figure 13](#_Ref111470839). When
the uplink of one M-LAG member device fails, traffic of the extra VLANs can
fail over to the other M-LAG member device through the peer link.

Figure 13 M-LAG extra VLANs

![](https://resource.h3c.com/en/202407/12/20240712_11704352_x_Img_x_png_12_2215976_294551_0.png)

 

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Configure M-LAG extra VLANs.

m-lag extra-vlan vlan-id-list

By default, no M-LAG extra VLANs are
configured.

## Setting the mode of configuration consistency check

#### About this task

The device handles configuration
inconsistency depending on the mode of configuration consistency check.

·     For type 1 configuration inconsistency:

¡     The
device generates log messages if loose mode is enabled.

¡     The
device shuts down M-LAG interfaces and generates log messages if strict mode is
enabled.

·     For type 2 configuration inconsistency, the
device only generates log messages, whether strict or loose mode is enabled.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Set the mode of configuration consistency
check.

m-lag consistency-check mode { loose \| strict }

By default, configuration consistency
check uses strict mode.

## Disabling configuration consistency check

#### About this task

To ensure that the M-LAG system can operate
correctly, M-LAG by default performs configuration consistency check when the M-LAG
system is set up. 

Configuration consistency check might fail
when you upgrade the M-LAG member devices in an M-LAG system. To prevent the M-LAG
system from falsely shutting down M-LAG interfaces, you can temporarily disable
configuration consistency check.

#### Restrictions and guidelines

Make sure the M-LAG member devices use the
same setting for configuration consistency check.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Disable configuration consistency check.

m-lag consistency-check disable

By default, configuration consistency
check is enabled.

## Setting the keepalive hold timer for identifying the cause of peer link down events

#### About this task

The keepalive hold timer starts when the peer
link goes down. The keepalive hold timer specifies the amount of time that the
device uses to identify the cause of a peer link down event. 

·     If the device receives keepalive packets from the
M-LAG peer before the timer expires, the peer link is down because the peer
link fails.

·     If the device does not receive keepalive packets
from the M-LAG peer before the timer expires, the peer link is down because the
peer M-LAG member device fails.

#### Restrictions and guidelines

For the M-LAG member device to correctly
determine the cause of a peer link down event, make sure the keepalive hold
timer is longer than the keepalive interval and is shorter than the keepalive
timeout timer.

If you use M-LAG and VRRP together, make
sure the keepalive hold timer is shorter than the interval at which the VRRP
master sends VRRP advertisements. Violation of this restriction might cause a
VRRP master/backup switchover to occur before peer link failure is confirmed.
To set the interval at which the VRRP master sends VRRP advertisements, use the
vrrp
vrid timer advertise command or the vrrp ipv6 vrid
timer advertise command. For more information about
this command, see High Availability Command Reference.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Set the keepalive hold timer.

m-lag keepalive hold-time value

By default, the keepalive hold timer is 3
seconds.

## Configuring M-LAG system auto-recovery

#### About this task

If only one M-LAG member device recovers
after the entire M-LAG system reboots, auto-recovery enables that member device
to remove its M-LAG interfaces from the M-LAG DOWN interface list.

·     If that member device has up M-LAG interfaces,
it takes over the primary role when the reload delay timer expires and forwards
traffic.

·     If that member device does not have up M-LAG interfaces,
it is stuck in the None role and does not forward traffic.

If auto-recovery is disabled, that M-LAG member
device will be stuck in the None role with all its M-LAG interfaces being M-LAG
DOWN after it recovers.

#### Restrictions and guidelines

If both M-LAG member devices recover and
have up M-LAG interfaces after the entire M-LAG system reboots, active-active
situation might occur if both the peer link and the keepalive link were down
when the reload delay timer expires. If this rare situation occurs, examine the
peer link and the keepalive link and restore them.

To avoid incorrect role preemption, make
sure the reload delay timer is longer than the amount of time required for the
device to restart.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Configure M-LAG system auto-recovery.

m-lag auto-recovery reload-delay delay-value

By default, M-LAG system auto-recovery is
not configured. The reload delay timer is not set.

## Setting the data restoration interval

#### About this task

The data restoration interval specifies the
maximum amount of time for the secondary M-LAG device to synchronize data with
the primary M-LAG device during M-LAG system setup. Within the data restoration
interval, the secondary M-LAG device sets all network interfaces to M-LAG MAD DOWN
state, except for the interfaces excluded from the shutdown action by M-LAG
MAD.

When the data restoration interval expires,
the secondary M-LAG member device brings up all network interfaces.

#### Restrictions and guidelines

Increase the data restoration interval as
needed for the following purposes:

·     Avoid packet loss and forwarding failure that
might occur when the amount of data is large or when you perform an ISSU
between the M-LAG member devices.

·     Avoid M-LAG interface flapping that might occur
if type 1 configuration consistency check fails after the M-LAG interfaces come
up upon expiration of the data restoration interval.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Set the data restoration interval.

m-lag restore-delay value

By default, the data restoration interval
is 30 seconds.

## Enabling M-LAG sequence number check

#### Restrictions and guidelines

As a best practice to improve security, use
M-LAG sequence number check together with M-LAG packet authentication.

After one M-LAG member device reboots, the
other M-LAG member device might receive and accept the packets that were intercepted
by an attacker before the reboot. As a best practice, change the  authentication
key after an M-LAG member device reboots.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enable M-LAG sequence number check.

m-lag sequence enable

By default, M-LAG sequence number check
is disabled.

## Enabling M-LAG packet authentication

#### Restrictions and guidelines

For successful authentication, configure
the same authentication key for the M-LAG member devices.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enable M-LAG packet authentication and
configure an authentication key.

m-lag authentication key { simple \| cipher } string

By default, M-LAG packet authentication
is disabled.

## Displaying and maintaining M-LAG

Execute display
commands in any view and reset commands in user
view.

 

| Task | Command |
| --- | --- |
| Display information about the configuration consistency check done by M-LAG. | display m-lag consistency { type1 \| type2 } { global \| interface interface-type interface-number } || Display the status of configuration consistency check. | display m-lag consistency-check status || Display DRCPDU statistics. | display m-lag drcp statistics \[ interface interface-type interface-number ] || Display M-LAG keepalive packet statistics. | display m-lag keepalive || Display detailed M-LAG MAD information. | display m-lag mad verbose || Display M-LAG role information. | display m-lag role || Display brief information about the peer-link interface and M-LAG interfaces. | display m-lag summary || Display the M-LAG system settings. | display m-lag system || Display detailed information about the peer-link interface and M-LAG interfaces. | display m-lag verbose \[ interface bridge-aggregation interface-number ] || Display M-LAG troubleshooting information. | display m-lag troubleshooting \[ m-lag-interface \| peer-link \| keepalive ] \[ history ] \[ count ] || Display M-LAG VIPs. | display m-lag virtual-ip \[ interface interface-type interface-number ] || Clear DRCPDU statistics. | reset m-lag drcp statistics \[ interface interface-list ] || Clear M-LAG troubleshooting records. | reset m-lag troubleshooting history |













 

## M-LAG configuration examples

### Example: Configuring basic M-LAG functions

#### Network configuration

As shown in [Figure 14](#_Ref469047589),
configure M-LAG on Device A and Device B to establish a multichassis aggregate
link with Device C.

Figure 14 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11704353_x_Img_x_png_13_2215976_294551_0.png)

‌

#### Procedure

 

**1\.**Configure Device A:

\# Configure M-LAG system settings.

\<DeviceA\> system-view

\[DeviceA] m-lag system-mac
1-1-1

\[DeviceA] m-lag system-number
1

\[DeviceA] m-lag
system-priority 123

\# Configure M-LAG keepalive packet
parameters.

\[DeviceA] m-lag keepalive ip destination
1.1.1.1 source 1.1.1.2

\# Set the link mode of Ten-GigabitEthernet 1/0/5 to Layer 3, and
assign the interface an IP address. The IP address will be used as the source
IP address of keepalive packets.

\[DeviceA] interface ten-gigabitethernet 1/0/5

\[DeviceA-Ten-GigabitEthernet1/0/5] port link-mode
route

\[DeviceA-Ten-GigabitEthernet1/0/5] ip address 1.1.1.2 24

\[DeviceA-Ten-GigabitEthernet1/0/5] quit

\# Exclude the interface used for M-LAG keepalive
detection (Ten-GigabitEthernet 1/0/5) from the shutdown
action by M-LAG MAD. 

\[DeviceA] m-lag mad exclude interface
ten-gigabitethernet 1/0/5

\# Create Layer 2 dynamic aggregate
interface Bridge-Aggregation 3\.

\[DeviceA] interface
bridge-aggregation 3

\[DeviceA-Bridge-Aggregation3]
link-aggregation mode dynamic

\[DeviceA-Bridge-Aggregation3]
quit

\# Assign Ten-GigabitEthernet 1/0/1 and Ten-GigabitEthernet 1/0/2 to aggregation
group 3\.

\[DeviceA] interface ten-gigabitethernet 1/0/1

\[DeviceA-Ten-GigabitEthernet1/0/1] port
link-aggregation group 3

\[DeviceA-Ten-GigabitEthernet1/0/1] quit

\[DeviceA] interface ten-gigabitethernet 1/0/2

\[DeviceA-Ten-GigabitEthernet1/0/2] port
link-aggregation group 3

\[DeviceA-Ten-GigabitEthernet1/0/2] quit

\# Specify Bridge-Aggregation 3 as the peer-link
interface.

\[DeviceA] interface
bridge-aggregation 3

\[DeviceA-Bridge-Aggregation3]
port m-lag peer-link 1

\[DeviceA-Bridge-Aggregation3]
undo mac-address static source-check enable

\[DeviceA-Bridge-Aggregation3]
quit

\# Create Layer 2 dynamic aggregate
interface Bridge-Aggregation 4\.

\[DeviceA] interface
bridge-aggregation 4

\[DeviceA-Bridge-Aggregation4]
link-aggregation mode dynamic

\[DeviceA-Bridge-Aggregation4]
quit

\# Assign Ten-GigabitEthernet 1/0/3 and Ten-GigabitEthernet 1/0/4 to aggregation
group 4\.

\[DeviceA] interface ten-gigabitethernet 1/0/3

\[DeviceA-Ten-GigabitEthernet1/0/3] port link-aggregation
group 4

\[DeviceA-Ten-GigabitEthernet1/0/3] quit

\[DeviceA] interface ten-gigabitethernet 1/0/4

\[DeviceA-Ten-GigabitEthernet1/0/4] port
link-aggregation group 4

\[DeviceA-Ten-GigabitEthernet1/0/4] quit

\# Assign Bridge-Aggregation 4 to M-LAG group
4\.

\[DeviceA] interface
bridge-aggregation 4

\[DeviceA-Bridge-Aggregation4]
port m-lag group 4

\[DeviceA-Bridge-Aggregation4]
quit

**2\.**Configure Device B:

\# Configure M-LAG system settings.

\<DeviceB\> system-view

\[DeviceB] m-lag system-mac
1-1-1

\[DeviceB] m-lag system-number 2

\[DeviceB] m-lag
system-priority 123

\# Configure M-LAG keepalive packet
parameters.

\[DeviceB] m-lag keepalive ip destination
1.1.1.2 source 1.1.1.1

\# Set the link mode of Ten-GigabitEthernet 1/0/5 to Layer 3, and
assign the interface an IP address. The IP address will be used as the source
IP address of keepalive packets.

\[DeviceB] interface ten-gigabitethernet 1/0/5

\[DeviceB-Ten-GigabitEthernet1/0/5] port link-mode
route

\[DeviceB-Ten-GigabitEthernet1/0/5] ip address 1.1.1.1 24

\[DeviceB-Ten-GigabitEthernet1/0/5] quit

\# Exclude the interface used for M-LAG keepalive
detection (Ten-GigabitEthernet 1/0/5) from the shutdown
action by M-LAG MAD.

\[DeviceB] m-lag mad exclude interface
ten-gigabitethernet 1/0/5

\# Create Layer 2 dynamic aggregate
interface Bridge-Aggregation 3\.

\[DeviceB] interface
bridge-aggregation 3

\[DeviceB-Bridge-Aggregation3]
link-aggregation mode dynamic

\[DeviceB-Bridge-Aggregation3]
quit

\# Assign Ten-GigabitEthernet 1/0/1 and Ten-GigabitEthernet 1/0/2 to aggregation
group 3\.

\[DeviceB] interface ten-gigabitethernet 1/0/1

\[DeviceB-Ten-GigabitEthernet1/0/1] port
link-aggregation group 3

\[DeviceB-Ten-GigabitEthernet1/0/1] quit

\[DeviceB] interface ten-gigabitethernet 1/0/2

\[DeviceB-Ten-GigabitEthernet1/0/2] port
link-aggregation group 3

\[DeviceB-Ten-GigabitEthernet1/0/2] quit

\# Specify Bridge-Aggregation 3 as the peer-link
interface.

\[DeviceB] interface
bridge-aggregation 3

\[DeviceB-Bridge-Aggregation3]
port m-lag peer-link 1

\[DeviceB-Bridge-Aggregation3]
undo mac-address static source-check enable

\[DeviceB-Bridge-Aggregation3]
quit

\# Create Layer 2 dynamic aggregate
interface Bridge-Aggregation 4\.

\[DeviceB] interface
bridge-aggregation 4

\[DeviceB-Bridge-Aggregation4]
link-aggregation mode dynamic

\[DeviceB-Bridge-Aggregation4]
quit

\# Assign Ten-GigabitEthernet 1/0/3 and Ten-GigabitEthernet 1/0/4 to aggregation
group 4\.

\[DeviceB] interface ten-gigabitethernet 1/0/3

\[DeviceB-Ten-GigabitEthernet1/0/3] port
link-aggregation group 4

\[DeviceB-Ten-GigabitEthernet1/0/3] quit

\[DeviceB] interface ten-gigabitethernet 1/0/4

\[DeviceB-Ten-GigabitEthernet1/0/4] port
link-aggregation group 4

\[DeviceB-Ten-GigabitEthernet1/0/4] quit

\# Assign Bridge-Aggregation 4 to M-LAG group
4\.

\[DeviceB] interface
bridge-aggregation 4

\[DeviceB-Bridge-Aggregation4]
port m-lag group 4

\[DeviceB-Bridge-Aggregation4]
quit

**3\.**Configure Device C:

\# Create Layer 2 dynamic aggregate
interface Bridge-Aggregation 4\.

\<DeviceC\> system-view

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

#### Verifying the configuration

\# Verify that the keepalive link is working
correctly on Device A.

\[DeviceA] display m-lag keepalive

Neighbor keepalive link status (cause):
Up

Neighbor is alive for: 104 s, 16 ms

Keepalive packet transmission status:

  Sent: Successful

  Received: Successful

Last received keepalive packet
information:

  Source IP address: 1.1.1.1

  Time: 2019/09/11 09:21:51

  Action: Accept

 

M-LAG keepalive parameters:

Destination IP address: 1.1.1.1

Source IP address: 1.1.1.2

Keepalive UDP port : 6400

Keepalive VPN name : N/A

Keepalive interval : 1000 ms

Keepalive timeout  : 5 sec

Keepalive hold time: 3 sec

\# Verify that the peer-link interface and
the M-LAG interface are working correctly on Device A.

\[DeviceA] display m-lag summary

Flags: A \-- Aggregate interface down,
B \-- No peer M-LAG interface configured

       C \-- Configuration consistency
check failed

 

Peer-link interface: BAGG3

Peer-link interface state (cause): UP

Keepalive link state (cause): UP

 

                     M-LAG interface
information

M-LAG IF      M-LAG group  Local
state (cause)  Peer state  Remaining down time(s)

BAGG4         4            UP                   UP        
 \-

\[DeviceA] display m-lag verbose

Flags: A \-- Home\_Gateway, B \--
Neighbor\_Gateway, C \-- Other\_Gateway,

       D \-- PeerLink\_Activity, E \--
DRCP\_Timeout, F \-- Gateway\_Sync,

       G \-- Port\_Sync, H \-- Expired

Peer-link interface/Peer-link
interface ID: BAGG3/1

State: UP

Cause: \-

Local DRCP flags/Peer DRCP flags:
ABDFG/ABDFG

Local Selected ports (index): XGE1/0/1 (260), XGE1/0/2 (261)

Peer Selected ports indexes: 260, 261

 

M-LAG interface/M-LAG group ID:
BAGG4/4

Local M-LAG interface state: UP

Peer M-LAG interface state: UP

M-LAG group state: UP

Local M-LAG interface down cause: \-

Remaining M-LAG DOWN time: \-

Local M-LAG interface LACP MAC: Config\=0001-0001-0001,
Effective\=0001-0001-0001

Peer M-LAG interface LACP MAC: Config\=0001-0001-0001,
Effective\=0001-0001-0001

Local M-LAG interface LACP priority: Config\=123,
Effective\=123

Peer M-LAG interface LACP priority: Config\=123,
Effective\=123

Local DRCP flags/Peer DRCP flags:
ABDFG/ABDFG

Local Selected ports (index): XGE1/0/3 (258), XGE1/0/4 (259)

Peer Selected ports indexes: 258, 259

\# Verify that all member ports of aggregation
group 4 are in Selected state on Device C, which indicates a successful link
aggregation between the M-LAG system and Device C.

\[DeviceC] display link-aggregation
verbose bridge-aggregation 4

Loadsharing Type: Shar \--
Loadsharing, NonS \-- Non-Loadsharing

Port Status: S \-- Selected, U \--
Unselected, I \-- Individual

Port: A \-- Auto port, M \-- Management
port, R \-- Reference port

Flags:  A \-- LACP\_Activity, B \--
LACP\_Timeout, C \-- Aggregation,

        D \-- Synchronization, E \--
Collecting, F \-- Distributing,

        G \-- Defaulted, H \-- Expired

Aggregate Interface:
Bridge-Aggregation4

Creation Mode: Manual

Aggregation Mode: Dynamic

Loadsharing Type: Shar

Management VLANs: None

System ID: 0x8000, 2e56-cbae-0600

Local:

  Port                Status   Priority
Index    Oper-Key               Flag

  XGE1/0/1(R)         S       
32768    1        1                      {ACDEF}

  XGE1/0/2            S        32768    2        1                     
{ACDEF}

  XGE1/0/3            S        32768    3        1                     
{ACDEF}

  XGE1/0/4            S        32768    4        1                     
{ACDEF}

Remote:

  Actor               Priority
Index    Oper-Key SystemID               Flag

  XGE1/0/1            32768    16387   
40004    0x7b  , 0001-0001-0001 {ACDEF}

  XGE1/0/2            32768    16388   
40004    0x7b  , 0001-0001-0001 {ACDEF}

  XGE1/0/3            32768    32771   
40004    0x7b  , 0001-0001-0001 {ACDEF}

  XGE1/0/4            32768    32772   
40004    0x7b  , 0001-0001-0001 {ACDEF}

### Example: Configuring Layer 3 gateways on an M-LAG system

#### Network configuration

As shown in [Figure 15](#_Ref485975013):

·     Configure Device A and Device B as an M-LAG system
to establish one multichassis aggregate link with Device C and one with Device
D.

·     Set up a keepalive link between Ten-GigabitEthernet 1/0/5 of Device A and Ten-GigabitEthernet 1/0/5 of Device B, and
exclude the interfaces from the shutdown action by M-LAG MAD.

·     Configure two VRRP groups on Device A and Device
B to provide gateway services for VLAN 100 and VLAN 200\. Configure Device A as
the master of the VRRP groups.

Figure 15 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11704354_x_Img_x_png_14_2215976_294551_0.png)

‌

#### Procedure

 

**1\.**Configure Device A:

\# Configure M-LAG system settings.

\<DeviceA\> system-view

\[DeviceA] m-lag system-mac
1-1-1

\[DeviceA] m-lag system-number
1

\[DeviceA] m-lag
system-priority 123

\# Configure M-LAG keepalive parameters.

\[DeviceA] m-lag keepalive ip destination
1.1.1.2 source 1.1.1.1

\# Set the link mode of Ten-GigabitEthernet 1/0/5 to Layer 3, and
assign the interface an IP address. The IP address will be used as the source
IP address of keepalive packets. 

\[DeviceA] interface ten-gigabitethernet 1/0/5

\[DeviceA-Ten-GigabitEthernet1/0/5] port link-mode
route

\[DeviceA-Ten-GigabitEthernet1/0/5] ip address 1.1.1.1 24

\[DeviceA-Ten-GigabitEthernet1/0/5] quit

\# Exclude the interface used for M-LAG keepalive
detection (Ten-GigabitEthernet 1/0/5) from the shutdown
action by M-LAG MAD.

\[DeviceA] m-lag mad exclude interface
ten-gigabitethernet 1/0/5

\# Create Layer 2 dynamic aggregate
interface Bridge-Aggregation 125\.

\[DeviceA] interface
bridge-aggregation 125

\[DeviceA-Bridge-Aggregation125]
link-aggregation mode dynamic

\[DeviceA-Bridge-Aggregation125]
quit

\# Assign Ten-GigabitEthernet 1/0/3 and Ten-GigabitEthernet 1/0/4 to aggregation
group 125\.

\[DeviceA] interface ten-gigabitethernet 1/0/3

\[DeviceA-Ten-GigabitEthernet1/0/3] port
link-aggregation group 125

\[DeviceA-Ten-GigabitEthernet1/0/3] quit

\[DeviceA] interface Ten-GigabitEthernet 1/0/4

\[DeviceA-Ten-GigabitEthernet1/0/4] port
link-aggregation group 125

\[DeviceA-Ten-GigabitEthernet1/0/4] quit

\# Specify Bridge-Aggregation 125 as the peer-link
interface.

\[DeviceA] interface
bridge-aggregation 125

\[DeviceA-Bridge-Aggregation125]
port m-lag peer-link 1

\[DeviceA-Bridge-Aggregation125]
undo mac-address static source-check enable

\[DeviceA-Bridge-Aggregation125]
quit

\# Create Layer 2 dynamic aggregate
interface Bridge-Aggregation 100, and assign it to M-LAG group 1\.

\[DeviceA] interface
bridge-aggregation 100

\[DeviceA-Bridge-Aggregation100]
link-aggregation mode dynamic

\[DeviceA-Bridge-Aggregation100]
port m-lag group 1

\[DeviceA-Bridge-Aggregation100]
quit

\# Assign Ten-GigabitEthernet 1/0/1 to aggregation
group 100\.

\[DeviceA] interface ten-gigabitethernet 1/0/1

\[DeviceA-Ten-GigabitEthernet1/0/1] port
link-aggregation group 100

\[DeviceA-Ten-GigabitEthernet1/0/1] quit

\# Create Layer 2 dynamic aggregate
interface Bridge-Aggregation 101, and assign it to M-LAG group 2\.

\[DeviceA] interface
bridge-aggregation 101

\[DeviceA-Bridge-Aggregation101]
link-aggregation mode dynamic

\[DeviceA-Bridge-Aggregation101]
port m-lag group 2

\[DeviceA-Bridge-Aggregation101]
quit

\# Assign Ten-GigabitEthernet 1/0/2 to aggregation
group 101\.

\[DeviceA] interface ten-gigabitethernet 1/0/2

\[DeviceA-Ten-GigabitEthernet1/0/2] port
link-aggregation group 101

\[DeviceA-Ten-GigabitEthernet1/0/2] quit

\# Create VLAN 100 and VLAN 101\.

\[DeviceA] vlan 100

\[DeviceA-vlan100] quit

\[DeviceA] vlan 101

\[DeviceA-vlan101] quit

\# Set the link type of Bridge-Aggregation
100 to trunk, and assign it to VLAN 100\.

\[DeviceA] interface
bridge-aggregation 100

\[DeviceA-Bridge-Aggregation100]
port link-type trunk

\[DeviceA-Bridge-Aggregation100]
port trunk permit vlan 100

\[DeviceA-Bridge-Aggregation100]
quit

\# Set the link type of Bridge-Aggregation
101 to trunk, and assign it to VLAN 101\.

\[DeviceA] interface
bridge-aggregation 101

\[DeviceA-Bridge-Aggregation101]
port link-type trunk

\[DeviceA-Bridge-Aggregation101]
port trunk permit vlan 101

\[DeviceA-Bridge-Aggregation101]
quit

\# Create VLAN-interface 100 and VLAN-interface
101, and assign IP addresses to them.

\[DeviceA] interface
vlan-interface 100

\[DeviceA-vlan-interface100] ip
address 10.1.1.1 24

\[DeviceA-vlan-interface100]
quit

\[DeviceA] interface
vlan-interface 101

\[DeviceA-vlan-interface101] ip
address 20.1.1.1 24

\[DeviceA-vlan-interface101]
quit

\# Exclude VLAN-interface 100 and VLAN-interface
101 from the shutdown action by M-LAG MAD.

\[DeviceA] m-lag mad exclude interface
vlan-interface 100

\[DeviceA] m-lag mad exclude interface
vlan-interface 101

\# Configure OSPF.

\[DeviceA] ospf

\[DeviceA-ospf-1] import-route
direct

\[DeviceA-ospf-1] area 0

\[DeviceA-ospf-1-area-0.0.0.0]
network 10.1.1.0 0.0.0.255

\[DeviceA-ospf-1-area-0.0.0.0]
network 20.1.1.0 0.0.0.255

\[DeviceA-ospf-1-area-0.0.0.0]
quit

\[DeviceA-ospf-1] quit

\# Create VRRP group 1 on VLAN-interface 100
and set its virtual IP address to 10.1.1.100.

\[DeviceA] interface
vlan-interface 100

\[DeviceA-Vlan-interface100]
vrrp vrid 1 virtual-ip 10.1.1.100

\# Set the priority of Device A (primary M-LAG
member device) to 200 for it to become the master in VRRP group 1\.

\[DeviceA-Vlan-interface100]
vrrp vrid 1 priority 200

\[DeviceA-Vlan-interface100]
quit

\# Create VRRP group 2 on VLAN-interface
101 and set its virtual IP address to 20.1.1.100.

\[DeviceA] interface
vlan-interface 101

\[DeviceA-Vlan-interface101]
vrrp vrid 2 virtual-ip 20.1.1.100

\# Set the priority of Device A (primary M-LAG
member device) to 200 for it to become the master in VRRP group 2\.

\[DeviceA-Vlan-interface101]
vrrp vrid 2 priority 200

\[DeviceA-Vlan-interface101]
quit

**2\.**Configure Device B:

\# Configure M-LAG system settings.

\<DeviceB\> system-view

\[DeviceB] m-lag system-mac
1-1-1

\[DeviceB] m-lag system-number 2

\[DeviceB] m-lag
system-priority 123

\# Configure M-LAG keepalive parameters.

\[DeviceB] m-lag keepalive ip destination
1.1.1.1 source 1.1.1.2

\# Set the link mode of Ten-GigabitEthernet 1/0/5 to Layer 3, and
assign the interface an IP address. The IP address will be used as the source
IP address of keepalive packets.

\[DeviceB] interface ten-gigabitethernet 1/0/5

\[DeviceB-Ten-GigabitEthernet1/0/5] port link-mode
route

\[DeviceB-Ten-GigabitEthernet1/0/5] ip address 1.1.1.2 24

\[DeviceB-Ten-GigabitEthernet1/0/5] quit

\# Exclude the interface used for M-LAG keepalive
detection (Ten-GigabitEthernet 1/0/5) from the shutdown
action by M-LAG MAD.

\[DeviceB] m-lag mad exclude interface
ten-gigabitethernet 1/0/5

\# Create Layer 2 dynamic aggregate
interface Bridge-Aggregation 125\.

\[DeviceB] interface
bridge-aggregation 125

\[DeviceB-Bridge-Aggregation125]
link-aggregation mode dynamic

\[DeviceB-Bridge-Aggregation125]
quit

\# Assign Ten-GigabitEthernet 1/0/3 and Ten-GigabitEthernet 1/0/4 to aggregation
group 125\.

\[DeviceB] interface ten-gigabitethernet 1/0/3

\[DeviceB-Ten-GigabitEthernet1/0/3] port
link-aggregation group 125

\[DeviceB-Ten-GigabitEthernet1/0/3] quit

\[DeviceB] interface ten-gigabitethernet 1/0/4

\[DeviceB-Ten-GigabitEthernet1/0/4] port
link-aggregation group 125

\[DeviceB-Ten-GigabitEthernet1/0/4] quit

\# Specify Bridge-Aggregation 125 as the peer-link
interface.

\[DeviceB] interface
bridge-aggregation 125

\[DeviceB-Bridge-Aggregation125]
port m-lag peer-link 1

\[DeviceB-Bridge-Aggregation125]
undo mac-address static source-check enable

\[DeviceB-Bridge-Aggregation125]
quit

\# Create Layer 2 dynamic aggregate
interface Bridge-Aggregation 100, and assign it to M-LAG group 1\.

\[DeviceB] interface
bridge-aggregation 100

\[DeviceB-Bridge-Aggregation100]
link-aggregation mode dynamic

\[DeviceB-Bridge-Aggregation100]
port m-lag group 1

\[DeviceB-Bridge-Aggregation100]
quit

\# Assign Ten-GigabitEthernet 1/0/1 to aggregation
group 100\.

\[DeviceB] interface ten-gigabitethernet 1/0/1

\[DeviceB-Ten-GigabitEthernet1/0/1] port
link-aggregation group 100

\[DeviceB-Ten-GigabitEthernet1/0/1] quit

\# Create Layer 2 dynamic aggregate
interface Bridge-Aggregation 101, and assign it to M-LAG group 2\.

\[DeviceB] interface
bridge-aggregation 101

\[DeviceB-Bridge-Aggregation101]
link-aggregation mode dynamic

\[DeviceB-Bridge-Aggregation101]
port m-lag group 2

\[DeviceB-Bridge-Aggregation101]
quit

\# Assign Ten-GigabitEthernet 1/0/2 to aggregation
group 101\.

\[DeviceB] interface ten-gigabitethernet 1/0/2

\[DeviceB-Ten-GigabitEthernet1/0/2] port
link-aggregation group 101

\[DeviceB-Ten-GigabitEthernet1/0/2] quit

\# Create VLAN 100 and VLAN 101\.

\[DeviceB] vlan 100

\[DeviceB-vlan100] quit

\[DeviceB] vlan 101

\[DeviceB-vlan101] quit

\# Set the link type of Bridge-Aggregation
100 to trunk, and assign it to VLAN 100\.

\[DeviceB] interface bridge-aggregation
100

\[DeviceB-Bridge-Aggregation100]
port link-type trunk

\[DeviceB-Bridge-Aggregation100]
port trunk permit vlan 100

\[DeviceB-Bridge-Aggregation100]
quit

\# Set the link type of Bridge-Aggregation
101 to trunk, and assign it to VLAN 101\.

\[DeviceB] interface
bridge-aggregation 101

\[DeviceB-Bridge-Aggregation101]
port link-type trunk

\[DeviceB-Bridge-Aggregation101]
port trunk permit vlan 101

\[DeviceB-Bridge-Aggregation101]
quit

\# Create VLAN-interface 100 and VLAN-interface
101, and assign IP addresses to them.

\[DeviceB] interface
vlan-interface 100

\[DeviceB-vlan-interface100] ip
address 10.1.1.2 24

\[DeviceB-vlan-interface100]
quit

\[DeviceB] interface
vlan-interface 101

\[DeviceB-vlan-interface101] ip
address 20.1.1.2 24

\[DeviceB-vlan-interface101]
quit

\# Exclude VLAN-interface 100 and VLAN-interface
101 from the shutdown action by M-LAG MAD.

\[DeviceB] m-lag mad exclude interface
vlan-interface 100

\[DeviceB] m-lag mad exclude interface
vlan-interface 101

\# Configure OSPF.

\[DeviceB] ospf

\[DeviceB-ospf-1] import-route
direct

\[DeviceB-ospf-1] area 0

\[DeviceB-ospf-1-area-0.0.0.0]
network 10.1.1.0 0.0.0.255

\[DeviceB-ospf-1-area-0.0.0.0]
network 20.1.1.0 0.0.0.255

\[DeviceB-ospf-1-area-0.0.0.0]
quit

\[DeviceB-ospf-1] quit

\# Create VRRP group 1 on VLAN-interface
100 and set its virtual IP address to 10.1.1.100.

\[DeviceB] interface
vlan-interface 100

\[DeviceB-Vlan-interface100]
vrrp vrid 1 virtual-ip 10.1.1.100

\[DeviceB-Vlan-interface100]
quit

\# Create VRRP group 2 on VLAN-interface
101 and set its virtual IP address to 20.1.1.100.

\[DeviceB] interface
vlan-interface 101

\[DeviceB-Vlan-interface101]
vrrp vrid 2 virtual-ip 20.1.1.100

\[DeviceB-Vlan-interface101]
quit

**3\.**Configure Device C:

\# Create Layer 2 dynamic aggregate
interface Bridge-Aggregation 100\.

\<DeviceC\> system-view

\[DeviceC] interface
bridge-aggregation 100

\[DeviceC-Bridge-Aggregation100]
link-aggregation mode dynamic

\[DeviceC-Bridge-Aggregation100]
quit

\# Assign Ten-GigabitEthernet 1/0/1 and Ten-GigabitEthernet 1/0/2 to aggregation
group 100\.

\[DeviceC] interface range ten-gigabitethernet 1/0/1 to ten-gigabitethernet 1/0/2

\[DeviceC-if-range] port
link-aggregation group 100

\[DeviceC-if-range] quit

\# Create VLAN 100\.

\[DeviceC] vlan 100

\[DeviceC-vlan100] quit

\# Set the link type of Bridge-Aggregation
100 to trunk, and assign it to VLAN 100\.

\[DeviceC] interface
bridge-aggregation 100

\[DeviceC-Bridge-Aggregation100]
port link-type trunk

\[DeviceC-Bridge-Aggregation100]
port trunk permit vlan 100

\[DeviceC-Bridge-Aggregation100]
quit

\# Set the link type of Ten-GigabitEthernet 1/0/3 to trunk, and
assign it to VLAN 100\.

\[DeviceC] interface ten-gigabitethernet 1/0/3

\[DeviceC-Ten-GigabitEthernet1/0/3] port link-type
trunk

\[DeviceC-Ten-GigabitEthernet1/0/3] port trunk permit
vlan 100

\[DeviceC-Ten-GigabitEthernet1/0/3] quit

\# Create VLAN-interface 100, and assign
it an IP address.

\[DeviceC] interface
vlan-interface 100

\[DeviceC-vlan-interface100] ip
address 10.1.1.3 24

\[DeviceC-vlan-interface100]
quit

\# Configure OSPF.

\[DeviceC] ospf

\[DeviceC-ospf-1] import-route
direct

\[DeviceC-ospf-1] area 0

\[DeviceC-ospf-1-area-0.0.0.0] network
10.1.1.0 0.0.0.255

\[DeviceC-ospf-1-area-0.0.0.0]
quit

\[DeviceC-ospf-1] quit

**4\.**Configure Device D:

\# Create Layer 2 dynamic aggregate
interface Bridge-Aggregation 101\.

\<DeviceD\> system-view

\[DeviceD] interface
bridge-aggregation 101

\[DeviceD-Bridge-Aggregation101]
link-aggregation mode dynamic

\[DeviceD-Bridge-Aggregation101]
quit

\# Assign Ten-GigabitEthernet 1/0/1 and Ten-GigabitEthernet 1/0/2 to aggregation
group 101\.

\[DeviceD] interface range ten-gigabitethernet 1/0/1 to ten-gigabitethernet 1/0/2

\[DeviceD-if-range] port
link-aggregation group 101

\[DeviceD-if-range] quit

\# Create VLAN 101\.

\[DeviceD] vlan 101

\[DeviceD-vlan101] quit

\# Set the link type of Bridge-Aggregation
101 to trunk, and assign it to VLAN 101\.

\[DeviceD] interface
bridge-aggregation 101

\[DeviceD-Bridge-Aggregation101]
port link-type trunk

\[DeviceD-Bridge-Aggregation101]
port trunk permit vlan 101

\[DeviceD-Bridge-Aggregation101]
quit

\# Set the link type of Ten-GigabitEthernet 1/0/3 to trunk, and
assign it to VLAN 101\.

\[DeviceD] interface ten-gigabitethernet 1/0/3

\[DeviceD-Ten-GigabitEthernet1/0/3] port link-type
trunk

\[DeviceD-Ten-GigabitEthernet1/0/3] port trunk permit
vlan 101

\[DeviceD-Ten-GigabitEthernet1/0/3] quit

\# Create VLAN-interface 101, and assign
it an IP address.

\[DeviceD] interface
vlan-interface 101

\[DeviceD-vlan-interface101] ip
address 20.1.1.3 24

\[DeviceD-vlan-interface101]
quit

\# Configure OSPF.

\[DeviceD] ospf

\[DeviceD-ospf-1] import-route
direct

\[DeviceD-ospf-1] area 0

\[DeviceD-ospf-1-area-0.0.0.0]
network 20.1.1.0 0.0.0.255

\[DeviceD-ospf-1-area-0.0.0.0]
quit

\[DeviceD-ospf-1] quit

#### Verifying the configuration

\# Verify that Device A has formed an M-LAG system
with Device B.

\[DeviceA] display m-lag summary

Flags: A \-- Aggregate interface down,
B \-- No peer M-LAG interface configured

       C \-- Configuration consistency
check failed

 

Peer-link interface: BAGG125

Peer-link interface state (cause): UP

Keepalive link state (cause): UP

 

                     M-LAG interface
information

M-LAG IF      M-LAG group  Local
state (cause)  Peer state  Remaining down time(s)

BAGG100       1            UP                  
UP          \-

BAGG101       2            UP                  
UP          \-

\[DeviceA] display m-lag verbose

Flags: A \-- Home\_Gateway, B \--
Neighbor\_Gateway, C \-- Other\_Gateway,

       D \-- PeerLink\_Activity, E \--
DRCP\_Timeout, F \-- Gateway\_Sync,

       G \-- Port\_Sync, H \-- Expired

 

Peer-link interface/Peer-link
interface ID: BAGG125/1

State: UP

Cause: \-

Local DRCP flags/Peer DRCP flags:
ABDFG/ABDFG

Local Selected ports (index): XGE1/0/3 (260), XGE1/0/4 (261)

Peer Selected ports indexes: 260, 261

 

M-LAG interface/M-LAG group ID:
BAGG100/1

Local M-LAG interface state: UP

Peer M-LAG interface state: UP

M-LAG group state: UP

Local M-LAG interface down cause: \-

Remaining M-LAG DOWN time: \-

Local M-LAG interface LACP MAC:
Config\=N/A, Effective\=0001-0001-0001

Peer M-LAG interface LACP MAC:
Config\=N/A, Effective\=0001-0001-0001

Local M-LAG interface LACP priority:
Config\=32768, Effective\=123

Peer M-LAG interface LACP priority:
Config\=32768, Effective\=123

Local DRCP flags/Peer DRCP flags:
ABDFG/ABDFG

Local Selected ports (index): XGE1/0/1 (258)

Peer Selected ports indexes: 258

 

M-LAG interface/M-LAG group ID:
BAGG101/2

Local M-LAG interface state: UP

Peer M-LAG interface state: UP

M-LAG group state: UP

Local M-LAG interface down cause: \-

Remaining M-LAG DOWN time: \-

Local M-LAG interface LACP MAC:
Config\=N/A, Effective\=0001-0001-0001

Peer M-LAG interface LACP MAC:
Config\=N/A, Effective\=0001-0001-0001

Local M-LAG interface LACP priority:
Config\=32768, Effective\=123

Peer M-LAG interface LACP priority:
Config\=32768, Effective\=123

Local DRCP flags/Peer DRCP flags:
ABDFG/ABDFG

Local Selected ports (index): XGE1/0/2 (259)

Peer Selected ports indexes: 259

\# Verify that Device C and Device D have
correctly set up aggregate links with the M-LAG system.

\[DeviceC] display link-aggregation
verbose

Loadsharing Type: Shar \--
Loadsharing, NonS \-- Non-Loadsharing

Port Status: S \-- Selected, U \--
Unselected, I \-- Individual

Port: A \-- Auto port, M \-- Management
port, R \-- Reference port

Flags:  A \-- LACP\_Activity, B \--
LACP\_Timeout, C \-- Aggregation,

        D \-- Synchronization, E \--
Collecting, F \-- Distributing,

        G \-- Defaulted, H \-- Expired

 

Aggregate Interface:
Bridge-Aggregation100

Creation Mode: Manual

Aggregation Mode: Dynamic

Loadsharing Type: Shar

Management VLANs: None

System ID: 0x8000, a03b-0694-0300

Local:

  Port                Status  
Priority Index    Oper-Key               Flag

  XGE1/0/1            S        32768   
1        1                      {ACDEF}

  XGE1/0/2            S        32768   
2        1                      {ACDEF}

Remote:

  Actor               Priority
Index    Oper-Key SystemID               Flag

  XGE1/0/1(R)         32768   
16386    40001    0x7b  , 0001-0001-0001 {ACDEF}

  XGE1/0/2            32768    32770   
40001    0x7b  , 0001-0001-0001 {ACDEF}

\[DeviceD] display link-aggregation
verbose

Loadsharing Type: Shar \--
Loadsharing, NonS \-- Non-Loadsharing

Port Status: S \-- Selected, U \--
Unselected, I \-- Individual

Port: A \-- Auto port, M \-- Management
port, R \-- Reference port

Flags:  A \-- LACP\_Activity, B \--
LACP\_Timeout, C \-- Aggregation,

        D \-- Synchronization, E \--
Collecting, F \-- Distributing,

        G \-- Defaulted, H \-- Expired

 

Aggregate Interface:
Bridge-Aggregation101

Creation Mode: Manual

Aggregation Mode: Dynamic

Loadsharing Type: Shar

Management VLANs: None

System ID: 0x8000, a03b-0d51-0400

Local:

  Port                Status  
Priority Index    Oper-Key               Flag

  XGE1/0/1            S        32768   
1        1                      {ACDEF}

  XGE1/0/2            S        32768    2       
1                      {ACDEF}

Remote:

  Actor               Priority
Index    Oper-Key SystemID               Flag

  XGE1/0/1(R)         32768   
16387    40002    0x7b  , 0001-0001-0001 {ACDEF}

  XGE1/0/2            32768    32771   
40002    0x7b  , 0001-0001-0001 {ACDEF}

\# Verify that Device A is the master in
VRRP group 1 and VRRP group 2\.

\[DeviceA] display vrrp

IPv4 Virtual Router Information:

 Running mode : Standard

 Total number of virtual routers : 2

 Interface          VRID 
State        Running Adver   Auth             Virtual

                                      
Pri     Timer   Type             IP

 \----------------------------------------------------------------------------

 Vlan100            1    
Master       200     100     None             10.1.1.100

 Vlan101            2    
Master       200     100     None             20.1.1.100

\[DeviceB] display vrrp

IPv4 Virtual Router Information:

 Running mode : Standard

 Total number of virtual routers : 2

 Interface          VRID 
State        Running Adver   Auth             Virtual

                                      
Pri     Timer   Type             IP

 \----------------------------------------------------------------------------

 Vlan100            1    
Backup       100     100     None             10.1.1.100

 Vlan101            2    
Backup       100     100     None             20.1.1.100

\# Verify that Device C and Device D have
established OSPF neighbor relationships with Device A and Device B.

\[DeviceC] display ospf peer

 

         OSPF Process 1 with Router
ID 10.1.1.3

               Neighbor Brief
Information

 

 Area: 0.0.0.0

 Router ID       Address         Pri
Dead-Time  State             Interface

 20.1.1.1        10.1.1.1        1  
37         Full/DR           Vlan100

 20.1.1.2        10.1.1.2        1  
32         Full/BDR          Vlan100

\[DeviceD] display ospf peer

 

         OSPF Process 1 with Router
ID 20.1.1.3

               Neighbor Brief
Information

 

 Area: 0.0.0.0

 Router ID       Address         Pri
Dead-Time  State             Interface

 20.1.1.1        20.1.1.1        1  
38         Full/DR           Vlan101

 20.1.1.2        20.1.1.2        1  
37         Full/BDR          Vlan101

\# Verify that Host A and Host B can ping
each other. (Details not shown.)

### Example: Configure dual-active gateways on an M-LAG system

#### Network configuration

As shown in [Figure 16](#_Ref81841851), set up
an M-LAG system with Device A and Device B and configure dual-active gateways
for the servers on Device A and Device B.

Figure 16 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11704355_x_Img_x_png_15_2215976_294551_0.png)

‌

#### Restrictions and guidelines

In this example, all devices use the
factory default settings. When you apply this configuration example to a live
environment, make sure the existing configurations do not conflict with the
configurations in this example.

You must configure the same M-LAG system
MAC address on the M-LAG member devices in the same M-LAG system. You must
assign unique M-LAG system MAC addresses to different M-LAG systems.

To avoid loops, do not configure local
proxy ARP or ND on the downstream VLAN interfaces of the M-LAG member devices.
For more information about local proxy ARP or ND, see proxy ARP configuration
and IPv6 neighbor discovery in Layer 3—IP Services
Configuration Guide.

#### Procedure

 

 

**1\.**Configure Device A:

\# Configure M-LAG system settings.

\<DeviceA\> system-view

\[DeviceA] m-lag system-mac 2-2-2

\[DeviceA] m-lag system-number
1

\[DeviceA] m-lag
system-priority 123

\# Configure M-LAG keepalive parameters.

\[DeviceA] m-lag keepalive ip
destination 21.1.1.2 source 21.1.1.1

\# Set the link mode of Ten-GigabitEthernet 1/0/5 to Layer 3, and
assign the interface IPv4 and IPv6 addresses. The IPv4 address will be used as
the source IP address of keepalive packets. 

\[DeviceA] interface ten-gigabitethernet 1/0/5

\[DeviceA-Ten-GigabitEthernet1/0/5] port link-mode
route

\[DeviceA-Ten-GigabitEthernet1/0/5] ip address 21.1.1.1
24

\[DeviceA-Ten-GigabitEthernet1/0/5] ipv6 address 21::1
64

\[DeviceA-Ten-GigabitEthernet1/0/5] quit

\# Exclude the interface used for M-LAG keepalive
detection (Ten-GigabitEthernet 1/0/5) from the shutdown
action by M-LAG MAD.

\[DeviceA] m-lag mad exclude
interface ten-gigabitethernet 1/0/5

\# Create Layer 2 dynamic aggregate
interface Bridge-Aggregation 1\.

\[DeviceA] interface bridge-aggregation
1

\[DeviceA-Bridge-Aggregation1]
link-aggregation mode dynamic

\[DeviceA-Bridge-Aggregation1]
quit

\# Assign Ten-GigabitEthernet 1/0/3 and Ten-GigabitEthernet 1/0/4 to aggregation
group 1\.

\[DeviceA] interface ten-gigabitethernet 1/0/3

\[DeviceA-Ten-GigabitEthernet1/0/3] port
link-aggregation group 1

\[DeviceA-Ten-GigabitEthernet1/0/3] quit

\[DeviceA] interface ten-gigabitethernet 1/0/4

\[DeviceA-Ten-GigabitEthernet1/0/4] port
link-aggregation group 1

\[DeviceA-Ten-GigabitEthernet1/0/4] quit

\# Configure Bridge-Aggregation 1 as the peer-link
interface.

\[DeviceA] interface bridge-aggregation
1

\[DeviceA-Bridge-Aggregation1]
port m-lag peer-link 1

\[DeviceA-Bridge-Aggregation1]
undo mac-address static source-check enable

\[DeviceA-Bridge-Aggregation1]
quit

\# Create Layer 2 dynamic aggregate
interface Bridge-Aggregation 3, and assign it to M-LAG group 1\.

\[DeviceA] interface bridge-aggregation
3

\[DeviceA-Bridge-Aggregation3]
link-aggregation mode dynamic

\[DeviceA-Bridge-Aggregation3]
port m-lag group 1

\[DeviceA-Bridge-Aggregation3]
quit

\# Assign Ten-GigabitEthernet 1/0/1 and Ten-GigabitEthernet 1/0/2 to aggregation
group 3\.

\[DeviceA] interface ten-gigabitethernet 1/0/1

\[DeviceA-Ten-GigabitEthernet1/0/1] port
link-aggregation group 3

\[DeviceA-Ten-GigabitEthernet1/0/1] quit

\[DeviceA] interface ten-gigabitethernet 1/0/2

\[DeviceA-Ten-GigabitEthernet1/0/2] port
link-aggregation group 3

\[DeviceA-Ten-GigabitEthernet1/0/2] quit

\# Create VLAN 102 and VLAN 103\.

\[DeviceA] vlan 102 103

\# Set the link type of Bridge-Aggregation
3 to trunk, and assign it to VLAN 102 and VLAN 103\.

\[DeviceA] interface bridge-aggregation
3

\[DeviceA-Bridge-Aggregation3]
port link-type trunk

\[DeviceA-Bridge-Aggregation3]
port trunk permit vlan 102 103

\[DeviceA-Bridge-Aggregation3]
quit

\# Create VLAN-interface 102 and assign it
an IPv4 address and an IPv6 address for the interface to act as an IPv4 and
IPv6 gateway.

\[DeviceA] interface
vlan-interface 102

\[DeviceA-Vlan-interface102] ip
address 10.102.0.1 255.255.255.0

\[DeviceA-Vlan-interface102]
ipv6 address 10:102::1 64

\# Assign M-LAG virtual IPv4 and IPv6
addresses to VLAN-interface 102 for Device A to establish OSPF and OSPFv3
neighbor relationships with Device D.

\[DeviceA-Vlan-interface102]
port m-lag virtual-ip 10.102.0.3 24 active

\[DeviceA-Vlan-interface102]
port m-lag ipv6 virtual-ip FE80::2 link-local

\# Assign a MAC address to VLAN-interface
102\.

\[DeviceA-Vlan-interface102]
mac-address 3-3-3

\[DeviceA-Vlan-interface102] quit

\# Exclude VLAN-interface 102 from the
shutdown action by M-LAG MAD.

\[DeviceA] m-lag mad exclude
interface vlan-interface 102

\# Create VLAN-interface 103 and assign it
an IPv4 address and an IPv6 address for the interface to act as an IPv4 and
IPv6 gateway.

\[DeviceA] interface vlan-interface
103

\[DeviceA-Vlan-interface103] ip
address 10.103.0.1 24

\[DeviceA-Vlan-interface103]
ipv6 address 10:103::1 64

\# Assign M-LAG virtual IPv4 and IPv6
addresses to VLAN-interface 103 for Device A to establish BGP and BGP4\+ peer
relationships with Device D.

\[DeviceA-Vlan-interface103]
port m-lag virtual-ip 10.103.0.3 24 active

\[DeviceA-Vlan-interface103]
port m-lag ipv6 virtual-ip 10:103::3 64 active

\# Assign a MAC address to VLAN-interface
103\.

\[DeviceA-Vlan-interface103]
mac-address 4-4-4

\[DeviceA-Vlan-interface103]
quit

\# Exclude VLAN-interface 103 from the
shutdown action by M-LAG MAD.

\[DeviceA] m-lag mad exclude
interface vlan-interface 103

\# Assign an IPv4 address and an IPv6
address to uplink interface Ten-GigabitEthernet 1/0/6.

\[DeviceA] interface ten-gigabitethernet 1/0/6

\[DeviceA-Ten-GigabitEthernet1/0/6] port link-mode
route

\[DeviceA-Ten-GigabitEthernet1/0/6] ip address 32.1.1.1
24

\[DeviceA-Ten-GigabitEthernet1/0/6] ipv6 address
32:1::1 64

\[DeviceA-Ten-GigabitEthernet1/0/6] quit

\# Configure OSPF.

\[DeviceA] ospf 1 router-id
11.1.1.1

\[DeviceA-ospf-1] area 0

\[DeviceA-ospf-1-area-0.0.0.0] quit

\[DeviceA-ospf-1] quit

\[DeviceA] interface vlan-interface
102

\[DeviceA-Vlan-interface102]
ospf 1 area 0

\[DeviceA-Vlan-interface102]
ospf peer sub-address enable 10.102.0.3

\[DeviceA-Vlan-interface102] quit

\[DeviceA] interface ten-gigabitethernet 1/0/6

\[DeviceA-Ten-GigabitEthernet1/0/6] ospf 1 area 0

\[DeviceA-Ten-GigabitEthernet1/0/6] quit

\# Configure OSPFv3.

\[DeviceA] ospfv3 1

\[DeviceA-ospfv3-1] router-id
10.1.1.1

\[DeviceA-ospfv3-1] area 0

\[DeviceA-ospfv3-1-area-0.0.0.0]
quit

\[DeviceA-ospfv3-1] quit

\[DeviceA] interface vlan-interface
102

\[DeviceA-Vlan-interface102]
ospfv3 1 area 0

\[DeviceA-Vlan-interface102] quit

\[DeviceA] interface ten-gigabitethernet 1/0/6

\[DeviceA-Ten-GigabitEthernet1/0/6] ospfv3 1 area 0

\[DeviceA-Ten-GigabitEthernet1/0/6] quit

\# Configure BGP.

\[DeviceA] bgp 100

\[DeviceA-bgp-default]
router-id 12.1.1.1

\[DeviceA-bgp-default] peer
32.1.1.2 as-number 100

\[DeviceA-bgp-default] peer
10.103.0.2 as-number 100

\[DeviceA-bgp-default] peer
10.103.0.2 source-address 10.103.0.3

\[DeviceA-bgp-default]
address-family ipv4

\[DeviceA-bgp-default-ipv4]
peer 10.103.0.2 enable

\[DeviceA-bgp-default-ipv4]
network 10.103.0.0  255.255.255.0

\[DeviceA-bgp-default-ipv4]
peer 32.1.1.2 enable

\[DeviceA-bgp-default-ipv4]
network 32.1.1.0 255.255.255.0

\[DeviceA-bgp-default-ipv4]
quit

\# Configure BGP4\+.

\[DeviceA-bgp-default] peer
32:1::2 as-number 100

\[DeviceA-bgp-default] peer
10:103::2 as-number 100

\[DeviceA-bgp-default] peer
10:103::2 source-address 10:103::3

\[DeviceA-bgp-default]
address-family ipv6

\[DeviceA-bgp-default-ipv6]
peer 10:103::2 enable

\[DeviceA-bgp-default-ipv6]
network 10:103::2  64

\[DeviceA-bgp-default-ipv6]
peer 32:1::2 enable

\[DeviceA-bgp-default-ipv6]
network  32:1::2 64

\[DeviceA-bgp-default-ipv6]
quit

\[DeviceA-bgp-default] quit

**2\.**Configure Device B:

\# Configure M-LAG system settings.

\<DeviceB\> system-view

\[DeviceB] m-lag system-mac 2-2-2

\[DeviceB] m-lag system-number 2

\[DeviceB] m-lag
system-priority 123

\# Configure M-LAG keepalive parameters.

\[DeviceB] m-lag keepalive ip
destination 21.1.1.1 source 21.1.1.2

\# Set the link mode of Ten-GigabitEthernet 1/0/5 to Layer 3, and
assign the interface IPv4 and IPv6 addresses. The IPv4 address will be used as
the source IP address of keepalive packets. 

\[DeviceB] interface ten-gigabitethernet 1/0/5

\[DeviceB-Ten-GigabitEthernet1/0/5] port link-mode
route

\[DeviceB-Ten-GigabitEthernet1/0/5] ip address 21.1.1.2
255.255.255.0

\[DeviceB-Ten-GigabitEthernet1/0/5] ipv6 address 21::2
64

\[DeviceB-Ten-GigabitEthernet1/0/5] quit

\# Exclude the interface used for M-LAG keepalive
detection (Ten-GigabitEthernet 1/0/5) from the shutdown
action by M-LAG MAD.

\[DeviceB] m-lag mad exclude
interface ten-gigabitethernet 1/0/5

\# Create Layer 2 dynamic aggregate
interface Bridge-Aggregation 1\.

\[DeviceB] interface
bridge-aggregation 1

\[DeviceB-Bridge-Aggregation1]
link-aggregation mode dynamic

\[DeviceB-Bridge-Aggregation1]
quit

\# Assign Ten-GigabitEthernet 1/0/3 and Ten-GigabitEthernet 1/0/4 to aggregation
group 1\.

\[DeviceB] interface ten-gigabitethernet 1/0/3

\[DeviceB-Ten-GigabitEthernet1/0/3] port
link-aggregation group 1

\[DeviceB-Ten-GigabitEthernet1/0/3] quit

\[DeviceB] interface ten-gigabitethernet 1/0/4

\[DeviceB-Ten-GigabitEthernet1/0/4] port
link-aggregation group 1

\[DeviceB-Ten-GigabitEthernet1/0/4] quit

\# Configure Bridge-Aggregation 1 as the peer-link
interface.

\[DeviceB] interface
bridge-aggregation 1

\[DeviceB-Bridge-Aggregation1]
port m-lag peer-link 1

\[DeviceB-Bridge-Aggregation1]
undo mac-address static source-check enable

\[DeviceB-Bridge-Aggregation1]
quit

\# Create Layer 2 dynamic aggregate
interface Bridge-Aggregation 3, and assign it to M-LAG group 1\.

\[DeviceB] interface
bridge-aggregation 3

\[DeviceB-Bridge-Aggregation3]
link-aggregation mode dynamic

\[DeviceB-Bridge-Aggregation3]
port m-lag group 1

\[DeviceB-Bridge-Aggregation3]
quit

\# Assign Ten-GigabitEthernet 1/0/1 and Ten-GigabitEthernet 1/0/2 to aggregation
group 3\.

\[DeviceB] interface ten-gigabitethernet 1/0/1

\[DeviceB-Ten-GigabitEthernet1/0/1] port
link-aggregation group 3

\[DeviceB-Ten-GigabitEthernet1/0/1] quit

\[DeviceB] interface ten-gigabitethernet 1/0/2

\[DeviceB-Ten-GigabitEthernet1/0/2] port
link-aggregation group 3

\[DeviceB-Ten-GigabitEthernet1/0/2] quit

\# Create VLAN 102 and VLAN 103\.

\[DeviceB] vlan 102 103

\# Set the link type of Bridge-Aggregation
3 to trunk, and assign it to VLAN 102 and VLAN 103\.

\[DeviceB] interface
bridge-aggregation 3

\[DeviceB-Bridge-Aggregation3]
port link-type trunk

\[DeviceB-Bridge-Aggregation3]
port trunk permit vlan 102 103

\[DeviceB-Bridge-Aggregation3]
quit

\# Create VLAN-interface 102 and assign it
an IPv4 address and an IPv6 address for the interface to act as an IPv4 and
IPv6 gateway.

\[DeviceB] interface
vlan-interface 102

\[DeviceB-Vlan-interface102] ip
address 10.102.0.1 24

\[DeviceB-Vlan-interface102]
ipv6 address 10:102::1 64

\# Assign M-LAG virtual IPv4 and IPv6
addresses to VLAN-interface 102 for Device A to establish OSPF and OSPFv3
neighbor relationships with Device D.

\[DeviceB-Vlan-interface102]
port m-lag virtual-ip 10.102.0.4 24 active

\[DeviceB-Vlan-interface102]
port m-lag ipv6 virtual-ip FE80::4 link-local

\# Assign a MAC address to VLAN-interface
102\.

\[DeviceB-Vlan-interface102]
mac-address 3-3-3

\[DeviceB-Vlan-interface102] quit

\# Exclude VLAN-interface 102 from the
shutdown action by M-LAG MAD.

\[DeviceB] m-lag mad exclude
interface vlan-interface 102

\# Create VLAN-interface 103 and assign it
an IPv4 address and an IPv6 address for the interface to act as an IPv4 and
IPv6 gateway.

\[DeviceB] interface vlan-interface
103

\[DeviceB-Vlan-interface103] ip
address 10.103.0.1 24

\[DeviceB-Vlan-interface103]
ipv6 address 10:103::1 64

\# Assign M-LAG virtual IPv4 and IPv6
addresses to VLAN-interface 103 for Device A to establish BGP and BGP4\+ peer
relationships with Device D.

\[DeviceB-Vlan-interface103]
port m-lag virtual-ip 10.103.0.4 24 active

\[DeviceB-Vlan-interface103]
port m-lag ipv6 virtual-ip 10:103::4 64 active

\# Assign a MAC address to VLAN-interface
103\.

\[DeviceB-Vlan-interface103]
mac-address 4-4-4

\[DeviceB-Vlan-interface103]
quit

\# Exclude VLAN-interface 103 from the
shutdown action by M-LAG MAD.

\[DeviceB] m-lag mad exclude
interface vlan-interface 103

\# Assign an IPv4 address and an IPv6
address to uplink interface Ten-GigabitEthernet 1/0/6.

\[DeviceB] interface ten-gigabitethernet 1/0/6

\[DeviceB-Ten-GigabitEthernet1/0/6] port link-mode
route

\[DeviceB-Ten-GigabitEthernet1/0/6] ip address 33.1.1.1
24

\[DeviceB-Ten-GigabitEthernet1/0/6] ipv6 address 33:1::1
64

\[DeviceB-Ten-GigabitEthernet1/0/6] quit

\# Configure OSPF.

\[DeviceB] ospf 1 router-id
11.1.1.2

\[DeviceB-ospf-1] area 0

\[DeviceB-ospf-1-area-0.0.0.0] quit

\[DeviceB-ospf-1] quit

\[DeviceB] interface vlan-interface
102

\[DeviceB-Vlan-interface102]
ospf 1 area 0

\[DeviceB-Vlan-interface102]
ospf peer sub-address enable 10.102.0.4

\[DeviceB-Vlan-interface102] quit

\[DeviceB] interface ten-gigabitethernet 1/0/6

\[DeviceB-Ten-GigabitEthernet1/0/6] ospf 1 area 0

\[DeviceB-Ten-GigabitEthernet1/0/6] quit

\# Configure OSPFv3.

\[DeviceB] ospfv3 1

\[DeviceB-ospfv3-1] router-id
10.1.1.2

\[DeviceB-ospfv3-1] area 0

\[DeviceB-ospfv3-1-area-0.0.0.0]
quit

\[DeviceB-ospfv3-1] quit

\[DeviceB] interface vlan-interface
102

\[DeviceB-Vlan-interface102]
ospfv3 1 area 0

\[DeviceB-Vlan-interface102]
quit

\[DeviceB] interface ten-gigabitethernet 1/0/6

\[DeviceB-Ten-GigabitEthernet1/0/6] ospfv3 1 area 0

\[DeviceB-Ten-GigabitEthernet1/0/6] quit

\# Configure BGP.

\[DeviceB] bgp 100

\[DeviceB-bgp-default]
router-id 12.1.1.2

\[DeviceB-bgp-default] peer
33.1.1.2 as-number 100

\[DeviceB-bgp-default] peer
10.103.0.2 as-number 100

\[DeviceB-bgp-default] peer
10.103.0.2 source-address 10.103.0.4

\[DeviceB-bgp-default]
address-family ipv4

\[DeviceB-bgp-default-ipv4]
peer 10.103.0.2 enable

\[DeviceB-bgp-default-ipv4]
network 10.103.0.0  255.255.255.0

\[DeviceB-bgp-default-ipv4]
peer 33.1.1.2 enable

\[DeviceB-bgp-default-ipv4]
network 33.1.1.0 255.255.255.0

\[DeviceB-bgp-default-ipv4]
quit

\# Configure BGP4\+.

\[DeviceB-bgp-default] peer
33:1::2 as-number 100

\[DeviceB-bgp-default] peer
10:103::2 as-number 100

\[DeviceB-bgp-default] peer
10:103::2 source-address 10:103::4

\[DeviceB-bgp-default]
address-family ipv6

\[DeviceB-bgp-default-ipv6]
peer 10:103::2 enable

\[DeviceB-bgp-default-ipv6]
network 10:103::2  64

\[DeviceB-bgp-default-ipv6]
peer 33:1::2 enable

\[DeviceB-bgp-default-ipv6]
network  33:1::2 64

\[DeviceB-bgp-default-ipv6]
quit

\[DeviceB-bgp-default] quit

**3\.**Configure Device C:

\# Assign IPv4 and IPv6 addresses to both Ten-GigabitEthernet 1/0/1 and Ten-GigabitEthernet 1/0/2.

\[DeviceC] interface ten-gigabitethernet 1/0/1

\[DeviceC-Ten-GigabitEthernet1/0/1] port link-mode
route

\[DeviceC-Ten-GigabitEthernet1/0/1] ip address 32.1.1.2
24

\[DeviceC-Ten-GigabitEthernet1/0/1] ipv6 address 32:1::2
64

\[DeviceC-Ten-GigabitEthernet1/0/1] quit

\[DeviceC] interface ten-gigabitethernet 1/0/2

\[DeviceC-Ten-GigabitEthernet1/0/2] port link-mode
route

\[DeviceC-Ten-GigabitEthernet1/0/2] ip address 33.1.1.2
24

\[DeviceC-Ten-GigabitEthernet1/0/2] ipv6 address 33:1::2
64

\[DeviceC-Ten-GigabitEthernet1/0/2] quit

\# Configure OSPF.

\[DeviceC] ospf 1 router-id
11.1.1.3

\[DeviceC-ospf-1] area 0

\[DeviceC-ospf-1-area-0.0.0.0] quit

\[DeviceC-ospf-1] quit

\[DeviceC] interface ten-gigabitethernet 1/0/1

\[DeviceC-Ten-GigabitEthernet1/0/1] ospf 1 area 0

\[DeviceC-Ten-GigabitEthernet1/0/1] quit

\[DeviceC] interface ten-gigabitethernet 1/0/2

\[DeviceC-Ten-GigabitEthernet1/0/2] ospf 1 area 0

\[DeviceC-Ten-GigabitEthernet1/0/2] quit

\# Configure OSPFv3.

\[DeviceC] ospfv3 1

\[DeviceC-ospfv3-1] router-id
10.1.1.3

\[DeviceC-ospfv3-1] area 0

\[DeviceC-ospfv3-1-area-0.0.0.0]
quit

\[DeviceC-ospfv3-1] quit

\[DeviceC] interface ten-gigabitethernet 1/0/1

\[DeviceC-Ten-GigabitEthernet1/0/1] ospfv3 1 area 0

\[DeviceC-Ten-GigabitEthernet1/0/1] quit

\[DeviceC] interface ten-gigabitethernet 1/0/2

\[DeviceC-Ten-GigabitEthernet1/0/2] ospfv3 1 area 0

\[DeviceC-Ten-GigabitEthernet1/0/2] quit

\# Configure BGP.

\[DeviceC] bgp 100

\[DeviceC-bgp-default]
router-id 12.1.1.3

\[DeviceC-bgp-default] peer
33.1.1.1 as-number 100

\[DeviceC-bgp-default] peer
32.1.1.1 as-number 100

\[DeviceC-bgp-default]
address-family ipv4

\[DeviceC-bgp-default-ipv4]
peer 33.1.1.1 enable

\[DeviceC-bgp-default-ipv4]
network 33.1.1.0  255.255.255.0

\[DeviceC-bgp-default-ipv4]
peer 32.1.1.1 enable

\[DeviceC-bgp-default-ipv4]
network 32.1.1.0 255.255.255.0

\[DeviceC-bgp-default-ipv4]
quit

\# Configure BGP4\+.

\[DeviceC-bgp-default] peer
33:1::1 as-number 100

\[DeviceC-bgp-default] peer
32:1::1 as-number 100 

\[DeviceC-bgp-default]
address-family ipv6

\[DeviceC-bgp-default-ipv6]
peer 32:1::1 enable

\[DeviceC-bgp-default-ipv6]
peer 33:1::1 enable

\[DeviceC-bgp-default-ipv6]
network 32:1:: 64

\[DeviceC-bgp-default-ipv6]
network 33:1:: 64

\[DeviceC-bgp-default-ipv6] quit

\[DeviceC-bgp-default] quit

**4\.**Configure Device D:

\# Create VLAN 102 and VLAN 103\.

\<DeviceD\> system-view

\[DeviceD] vlan 102 103

\# Create Layer 2 dynamic aggregate
interface Bridge-Aggregation 3\.

\[DeviceD] interface
bridge-aggregation 3

\[DeviceD-Bridge-Aggregation3]
link-aggregation mode dynamic

\[DeviceD-Bridge-Aggregation3]
quit

\# Assign Ten-GigabitEthernet 1/0/1 through Ten-GigabitEthernet 1/0/4 to aggregation
group 3\.

\[DeviceD] interface range ten-gigabitethernet 1/0/1 to ten-gigabitethernet 1/0/4

\[DeviceD-if-range] port
link-aggregation group 3

\[DeviceD-if-range] quit

\# Configure Bridge-Aggregation 3 as a
trunk port, and assign it to VLAN 102 and VLAN 103\.

\[DeviceD] interface bridge-aggregation
3

\[DeviceD-Bridge-Aggregation3]
port link-type trunk

\[DeviceD-Bridge-Aggregation3]
port trunk permit vlan 102 103

\[DeviceD-Bridge-Aggregation3]
quit

\# Create VLAN-interface102 and assign
IPv4 and IPv6 addresses to it.

\[DeviceD] interface vlan-interface
102

\[DeviceD-Vlan-interface102] ip
address 10.102.0.2 24

\[DeviceD-Vlan-interface102]
ipv6 address 10:102::2 64

\[DeviceD-Vlan-interface102] quit

\# Create VLAN-interface103 and assign
IPv4 and IPv6 addresses to it.

\[DeviceD] interface vlan-interface
103

\[DeviceD-Vlan-interface103] ip
address 10.103.0.2 24

\[DeviceD-Vlan-interface103]
ipv6 address 10:103::2 64

\[DeviceD-Vlan-interface103] quit

\# Configure OSPF.

\[DeviceD] ospf 1 router-id
11.1.1.4

\[DeviceD-ospf-1] area 0

\[DeviceD-ospf-1-area-0.0.0.0] quit

\[DeviceD-ospf-1] quit

\[DeviceD] interface vlan-interface
102

\[DeviceD-Vlan-interface102]
ospf 1 area 0

\[DeviceD-Vlan-interface102] quit

\[DeviceD] interface vlan-interface
103

\[DeviceD-Vlan-interface103]
ospf 1 area 0

\[DeviceD-Vlan-interface103] quit

\# Configure OSPFv3.

\[DeviceD] ospfv3 1

\[DeviceD-ospfv3-1] router-id
10.1.1.4

\[DeviceD-ospfv3-1] area 0

\[DeviceD-ospfv3-1-area-0.0.0.0]
quit

\[DeviceD-ospfv3-1] quit

\[DeviceD] interface vlan-interface
102

\[DeviceD-Vlan-interface102]
ospfv3 1 area 0

\[DeviceD-Vlan-interface102] quit

\[DeviceD] interface vlan-interface
103

\[DeviceD-Vlan-interface103]
ospfv3 1 area 0

\[DeviceD-Vlan-interface103] quit

\# Configure BGP.

\[DeviceD] bgp 100

\[DeviceD-bgp-default]
router-id 12.1.1.4

\[DeviceD-bgp-default] peer
10.103.0.3 as-number 100

\[DeviceD-bgp-default] peer
10.103.0.4 as-number 100

\[DeviceD-bgp-default]
address-family ipv4

\[DeviceD-bgp-default-ipv4]
peer 10.103.0.3 enable

\[DeviceD-bgp-default-ipv4]
peer 10.103.0.4 enable

\[DeviceD-bgp-default-ipv4]
network 10.103.0.3  255.255.255.0

\[DeviceD-bgp-default-ipv4] quit

\# Configure BGP4\+.

\[DeviceD-bgp-default] peer
10:103::3 as-number 100

\[DeviceD-bgp-default] peer
10:103::4 as-number 100 

\[DeviceD-bgp-default]
address-family ipv6

\[DeviceD-bgp-default-ipv6]
peer 10:103::3  enable

\[DeviceD-bgp-default-ipv6]
peer 10:103::4 enable

\[DeviceD-bgp-default-ipv6]
network 10:103::3 64

\[DeviceD-bgp-default-ipv6]
quit

\[DeviceD-bgp-default] quit

#### Verifying the configuration

The following tasks use Device A as an
example.

\# Verify that Device A has formed an M-LAG system
with Device B.

\[DeviceA] display m-lag summary

Flags: A \-- Aggregate interface down,
B \-- No peer M-LAG interface configured

       C \-- Configuration consistency
check failed

 

Peer-link interface: BAGG1

Peer-link interface state (cause): UP

Keepalive link state (cause): UP

 

                     M-LAG interface
information

M-LAG IF      M-LAG group  Local
state (cause)  Peer state  Remaining down time(s)

BAGG3         1            UP                  
UP          \-

\[DeviceA] display m-lag verbose

Flags: A \-- Home\_Gateway, B \--
Neighbor\_Gateway, C \-- Other\_Gateway,

       D \-- PeerLink\_Activity, E \--
DRCP\_Timeout, F \-- Gateway\_Sync,

       G \-- Port\_Sync, H \-- Expired

 

Peer-link interface/Peer-link
interface ID: BAGG1/1

State: UP

Cause: \-

Local DRCP flags/Peer DRCP flags:
ABDFG/ABDFG

Local Selected ports (index): XGE1/0/3 (260), XGE1/0/4 (261)

Peer Selected ports indexes: 260, 261

 

M-LAG interface/M-LAG group ID: BAGG3/1

Local M-LAG interface state: UP

Peer M-LAG interface state: UP

M-LAG group state: UP

Local M-LAG interface down cause: \-

Remaining M-LAG DOWN time: \-

Local M-LAG interface LACP MAC:
Config\=N/A, Effective\=0002-0002-0002

Peer M-LAG interface LACP MAC:
Config\=N/A, Effective\=0002-0002-0002

Local M-LAG interface LACP priority:
Config\=32768, Effective\=123

Peer M-LAG interface LACP priority:
Config\=32768, Effective\=123

Local DRCP flags/Peer DRCP flags:
ABDFG/ABDFG

Local Selected ports (index): XGE1/0/1 (258), XGE1/0/2 (259)

Peer Selected ports indexes: 258, 259

\# Verify that Device A have established
OSPF neighbor relationships.

\[DeviceA] display ospf peer

 

         OSPF Process 1 with Router
ID 11.1.1.1

               Neighbor Brief
Information

 

 Area: 0.0.0.0

 Router ID       Address         Pri
Dead-Time  State             Interface

 11.1.1.3        32.1.1.2        1  
39         Full/BDR          XGE1/0/6

 11.1.1.4        10.102.0.2      1  
31         Full/DROther      Vlan102

 11.1.1.2        10.102.0.4      1  
37         Full/DR           Vlan102

\# Verify that Device A have established
OSPFv3 neighbor relationships.

\[DeviceA] display ospfv3 peer

 

               OSPFv3 Process 1 with
Router ID 10.1.1.1

 

 Area: 0.0.0.0

\-------------------------------------------------------------------------

 Router ID       Pri
State             Dead-Time InstID Interface

 10.1.1.3        1  
Full/BDR          00:00:34  0      XGE1/0/6

 10.1.1.2        1  
Full/BDR          00:00:32  0      Vlan102

 10.1.1.4        1  
Full/DROther      00:00:39  0      Vlan102

\# Verify that Device A have established BGP
peer relationships.

\[DeviceA] display bgp peer ipv4

 

 BGP local router ID: 12.1.1.1

 Local AS number: 100

 Total number of peers:
2                 Peers in established state: 2

 

  \* \- Dynamically created peer

  ^ \- Peer created through link-local
address

  Peer                    AS 
MsgRcvd  MsgSent OutQ PrefRcv Up/Down  State

 

  10.103.0.2             100       
6        6    0       1 00:01:50 Established

  32.1.1.2               100       
5        5    0       2 00:00:01 Established

\# Verify that Device A have established
BGP4\+ peer relationships.

\[DeviceA] display bgp peer ipv6

 

 BGP local router ID: 12.1.1.1

 Local AS number: 100

 Total number of peers:
2                 Peers in established state: 2

 

  \* \- Dynamically created peer

  ^ \- Peer created through link-local
address

  Peer                    AS 
MsgRcvd  MsgSent OutQ PrefRcv Up/Down  State

 

  10:103::2              100       
9        9    0       1 00:04:26 Established

  32:1::2                100        6       
6    0       2 00:02:13 Established

 

 

