
# Configuring ARP

## About ARP

ARP resolves IP addresses into MAC addresses
on Ethernet networks.

### ARP message format

ARP uses two types of messages: ARP request
and ARP reply. [Figure 1](#_Ref145911779) shows
the format of ARP request/reply messages. Numbers in the figure refer to field
lengths.

Figure 1 ARP message format

![](https://resource.h3c.com/en/202407/12/20240712_11704488_x_Img_x_png_0_2215991_294551_0.png)

‌

·     Hardware type—Hardware address type. The value 1 represents Ethernet.

·     Protocol type—Type of the protocol address to be
mapped. The hexadecimal value 0x0800 represents IP.

·     Hardware address length
and protocol address length—Length, in
bytes, of a hardware address and a protocol address. For an Ethernet address,
the value of the hardware address length field is 6\. For an IPv4 address, the
value of the protocol address length field is 4\.

·     OP—Operation code, which describes the type of ARP message. The value 1
represents an ARP request, and the value 2 represents an ARP reply.

·     Sender hardware address—Hardware
address of the device sending the message.

·     Sender protocol address—Protocol
address of the device sending the message.

·     Target hardware address—Hardware
address of the device to which the message is being sent.

·     Target protocol address—Protocol
address of the device to which the message is being sent.

### ARP operating mechanism

As shown in [Figure 2](#_Ref165260434), Host
A and Host B are on the same subnet. Host A sends a packet to Host B as follows:

**1\.**Host A looks through the ARP table for an
ARP entry for Host B. If one entry is found, Host A uses the MAC address in the
entry to encapsulate the IP packet into a data link layer frame. Then Host A
sends the frame to Host B.

**2\.**If Host A finds no entry for Host B, Host A buffers
the packet and broadcasts an ARP request. The payload of the ARP request contains
the following information:

¡     Sender IP address and sender MAC address—Host A's IP address and MAC address.

¡     Target IP address—Host B's IP address.

¡     Target MAC address—An all-zero MAC
address.

All hosts on this subnet can receive the broadcast
request, but only the requested host (Host B) processes the request.

**3\.**Host B compares its own IP address with the target
IP address in the ARP request. If they are the same, Host B operates as follows:

**a.**Adds the sender IP address and sender MAC
address into its ARP table.

**b.**Encapsulates its MAC address into an ARP reply.

**c.**Unicasts the ARP reply to Host A.

**4\.**After receiving the ARP reply, Host A
operates as follows:

**a.**Adds the MAC address of Host B into its ARP
table.

**b.**Encapsulates the MAC address into the packet
and sends the packet to Host B.

Figure 2 ARP address resolution process

![](https://resource.h3c.com/en/202407/12/20240712_11704489_x_Img_x_png_1_2215991_294551_0.png)

‌

If Host A and Host B are on different subnets,
Host A sends a packet to Host B as follows:

**1\.**Host A broadcasts an ARP request where the target
IP address is the IP address of the gateway. 

**2\.**The gateway responds with its MAC address in
an ARP reply to Host A. 

**3\.**Host A uses the gateway's MAC address to
encapsulate the packet, and then sends the packet to the gateway. 

**4\.**If the gateway has an ARP entry for Host B,
it forwards the packet to Host B directly. If not, the gateway broadcasts an ARP
request, in which the target IP address is the IP address of Host B. 

**5\.**After the gateway gets the MAC address of Host
B, it sends the packet to Host B.

### ARPentry types

An ARP table stores dynamic ARP entries, OpenFlow
ARP entries, Rule ARP entries, and static ARP entries.

#### Dynamic ARP entry

ARP automatically creates and updates dynamic
entries. A dynamic ARP entry is removed when its aging timer expires or the
output interface goes down. In addition, a dynamic ARP entry can be overwritten
by a static ARP entry.

#### Static ARP entry

A static ARP entry is manually configured
and maintained. It does not age out and cannot be overwritten by any dynamic
ARP entry. 

Static ARP entries protect communication between
devices because attack packets cannot modify the IP-to-MAC mapping in a static
ARP entry.

The device supports the following types of
static ARP entries:

·     Long static ARP entry—It is directly used for forwarding packets. A long static ARP entry
contains the IP address, MAC address, VLAN, and output interface.

·     Short static ARP entry—It contains only the IP address and MAC address.

If the output interface is a Layer 3
Ethernet interface, the short ARP entry can be directly used to forward packets.


If the output interface is a VLAN
interface, the device sends an ARP request whose target IP address is the IP
address in the short entry. If the sender IP and MAC addresses in the received
ARP reply match the short static ARP entry, the device performs the following
operations:

¡     Adds
the interface that received the ARP reply to the short static ARP entry.

¡     Uses
the resolved short static ARP entry to forward IP packets.

·     Multiport ARP entry—It contains the IP address, MAC address, and VLAN/VSI information.

The device can use a multiport ARP entry
that has the same MAC address and VLAN/VSI as a multicast or multiport unicast
MAC address entry for packet forwarding. A multiport ARP entry is manually
configured. It does not age out and cannot be overwritten by any dynamic ARP
entry.

To communicate with a host by using a fixed
IP-to-MAC mapping, configure a short static ARP entry on the device. To
communicate with a host by using a fixed IP-to-MAC mapping through an interface
in a VLAN, configure a long static ARP entry on the device.

#### OpenFlow ARP entry

ARP creates OpenFlow ARP entries by
learning from the OpenFlow module. An OpenFlow ARP entry does not age out, and it
cannot be updated. An OpenFlow ARP entry can be used directly to forward packets.
For more information about OpenFlow, see OpenFlow
Configuration Guide.

#### Rule ARP entry

Rule ARP entries can be directly used for packet
forwarding. A Rule ARP entry does not age out, and it cannot be updated. It can
be overwritten by a static ARP entry.

ARP creates Rule ARP entries by learning
from the following modules: 

·     Portal. For more information about portal, see Security Configuration Guide.

·     VXLAN. For more information about VXLAN, see
VXLAN overview in VXLAN Configuration Guide.

·     OVSDB. For more information about OVSDB, see OVSDB-VTEP
configuration in VXLAN Configuration Guide.

## ARP tasks at a glance

All ARP tasks are optional.

·     [Configuring a static ARP entry](#_Ref97575444)

¡     [Configuring a short static ARP entry](#_Ref470009954)

¡     [Configuring a long static ARP entry](#_Ref470009955)

¡     [Configuring a multiport ARP entry](#_Ref470009956)

·     [Configuring features for dynamic ARP
entries](#_Ref470009957)

¡     [Setting the dynamic ARP learning limit
for a device](#_Ref470009958)

¡     [Setting the dynamic ARP learning limit
for an interface](#_Ref470009959)

¡     [Setting the aging timer for dynamic ARP
entries](#_Ref470009960)

¡     [Setting the maximum number of probes for
dynamic ARP entries](#_Ref508780894)

¡     [Setting the interval for probing dynamic
ARP entries](#_Ref522268470)

¡     [Enabling dynamic ARP entry check](#_Ref470009961)

·     [Synchronizing ARP entries across all
member devices](#_Ref499049615)

·     [Configuring a customer-side or
network-side port](#_Ref470009963)

·     Enabling user information checking for ARP entries:

¡     [Enabling recording user IP address
conflicts](#_Ref513723674)

¡     [Enabling interface consistency check
between ARP and MAC address entries](#_Ref519520815)

¡     [Enabling unknown source MAC-triggered ARP
probing](#_Ref77582973)

¡     [Enabling recording user port migrations](#_Ref529946634)

·     [Enabling ARP logging](#_Ref470009972)

·     [Enabling error logging for ARP entry
deployment to hardware](#_Ref106203916)

·     [Enabling SNMP notifications for ARP](#_Ref108792669)

## Configuring a static ARP entry

Static ARP entries are effective when the
device functions correctly.

### Configuring a short static ARP entry

#### Restrictions and guidelines

A resolved short static ARP entry becomes
unresolved upon certain events, for example, when the resolved output interface
goes down, or the corresponding VLAN or VLAN interface is deleted. 

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Configure a short static ARP entry.

arp static ip-address mac-address \[ vpn-instance
vpn-instance-name
] \[ description text ]

### Configuring a long static ARP entry

#### About this task

Long static ARP entries can be effective or
ineffective. Ineffective long static ARP entries cannot be used for packet
forwarding. A long static ARP entry is ineffective when any of the following
conditions exists:

·     The IP address in the entry conflicts with a
local IP address.

·     No local interface has an IP address in the same
subnet as the IP address in the ARP entry.

A long static ARP entry for a VLAN is
deleted if the VLAN or VLAN interface is deleted.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Configure a long static ARP entry.

arp static ip-address
mac-address \[ vlan-id interface-type interface-number \| vsi-interface
vsi-interface-id tunnel number vsi vsi-name \| vsi-interface
vsi-interface-id interface-type interface-number
service-instance instance-id vsi vsi-name ] \[ vpn-instance
vpn-instance-name ] \[ description text ]

### Configuring a multiport ARP entry

#### About this task

A multiport ARP entry contains an IP
address, MAC address, output interface, and VLAN ID/VSI information. The VLAN
and output interfaces are specified by a multiport unicast MAC address entry or
a multicast MAC address entry. For more information about multiport unicast MAC
address entries, see Layer—2 LAN Switching Configuration Guide. For more information about multicast
MAC address entries, see IGMP snooping configuration in IP
Multicast Configuration Guide. 

A multiport ARP entry can overwrite a
dynamic, short static or long static ARP entry. Conversely, a short static or
long static ARP entry can overwrite a multiport ARP entry.

#### Restrictions and guidelines

For a multiport ARP entry to be effective
for packet forwarding, make sure the following conditions are met:

·     A multiport unicast MAC address entry or a multicast
MAC address entry exists. 

·     The multiport ARP entry must have the same MAC
address and VLAN/VSI setting as the multiport unicast MAC address entry.

·     The IP address in the multiport
ARP entry must reside on the same subnet as the VLAN interface of the specified
VLAN.

If an aggregate interface is the output
interface of a multiport ARP entry and its member ports reside on multiple IRF
member devices, the device cannot use the entry for packet forwarding. To
resolve this issue, use the link-aggregation global load-sharing mode command to set a global link-aggregation load sharing mode other
than source or destination MAC address-based. For more information about this
command, see Ethernet link aggregation in Layer 2—LAN
Switching Command Reference.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Configure a multiport unicast MAC address
entry or a multicast MAC address entry.

¡     Configure
a multiport unicast MAC address entry in a common network.

mac-address multiport mac-address interface interface-list vlan vlan-id

¡     Configure
a local multiport unicast MAC address entry in a VXLAN network.

mac-address multiport mac-address { interface { interface-type interface-number service-instance instance-id }\&\<1-4\> } vsi vsi-name

¡     Configure
a remote multiport unicast MAC address entry in a VXLAN network.

mac-address multiport mac-address { interface { tunnel tunnel-number1 \[ to tunnel tunnel-number2 ] }\&\<1-4\> } vsi vsi-name

¡     Configure a multicast MAC address entry in a common network.

mac-address multicast mac-address interface interface-list vlan vlan-id

¡     Configure
a local static multicast MAC address entry in a VXLAN network.

mac-address multicast mac-address { interface { interface-type interface-number service-instance instance-id }\&\<1-4\> } vsi vsi-name

¡     Configure
a remote static multicast MAC address entry in a VXLAN network.

mac-address multicast mac-address { interface { tunnel tunnel-number1 \[ to tunnel tunnel-number2 ] }\&\<1-4\> } vsi vsi-name

**3\.**Configure a multiport ARP entry.

arp multiport ip-address mac-address { vlan-id \| vsi-interface vsi-interface-id vsi vsi-name } \[ vpn-instance vpn-instance-name ] \[ description text ]

## Configuring features for dynamic ARP entries

### Setting the dynamic ARP learning limit for a device

#### About this task

A device can dynamically learn ARP entries.
To prevent a device from holding too many ARP entries, you can set the maximum
number of dynamic ARP entries that the device can learn. When the limit is
reached, the device stops ARP learning.

If you set a value lower than the number of
existing dynamic ARP entries, the device does not delete the existing entries unless
they age out. You can use the reset arp dynamic command to clear dynamic ARP entries.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Set the dynamic ARP learning limit for the
device.

arp max-learning-number max-number slot slot-number

By default, the device dynamic ARP
learning limit is the ARP table capacity, which varies by remaining resources
on the device and the arp mode uni command
configuration. 

To disable the device from dynamic ARP learning,
set the value to 0\.

### Setting the dynamic ARP learning limitfor an interface

#### About this task

An interface can dynamically learn ARP
entries. To prevent an interface from holding too many ARP entries, you can set
the maximum number of dynamic ARP entries that the interface can learn. When
the limit is reached, the interface stops ARP learning.

You can set limits for both a Layer 2
interface and the VLAN interface for a permitted VLAN on the Layer 2 interface.
The Layer 2 interface learns an ARP entry only when neither limit is reached.

The total dynamic ARP learning limit for all
interfaces will not be higher than the dynamic ARP learning limit for the device.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter interface view.

interface interface-type interface-number

**3\.**Set the dynamic ARP learning limit for the
interface.

arp max-learning-num max-number \[ alarm alarm-threshold ]

By default, the dynamic ARP learning
limit for an interface is the ARP table capacity, which varies by remaining
resources on the device and the arp mode uni
command configuration. 

To disable the interface from dynamic ARP
learning, set the value to 0\.

### Setting the aging timer for dynamic ARP entries

#### About this task

Each dynamic ARP entry in the ARP table has
a limited lifetime, called an aging timer. The aging timer of a dynamic ARP
entry is reset each time the dynamic ARP entry is updated. A dynamic ARP entry that
is not updated before its aging timer expires is deleted from the ARP table.

You can set the aging timer for dynamic ARP
entries in system view or in interface view. The aging timer set in interface
view takes precedence over the aging timer set in system view.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Set the aging timer for dynamic ARP entries.

¡     Set
the aging timer for dynamic ARP entries in system view.

arp timer aging { aging-minutes \| second aging-seconds }

By default, the aging timer for dynamic
ARP entries in system view is 20 minutes.

¡     Execute
the following commands in sequence to set the aging timer for dynamic ARP
entries in interface view:

interface interface-type interface-number

arp timer aging { aging-minutes \| second aging-seconds }

By default, the aging timer for dynamic
ARP entries in interface view is the aging timer set in system view.

### Setting the maximum number of probes for dynamic ARP entries

#### About this task

This probe mechanism keeps legal dynamic
ARP entries valid and avoids unnecessary ARP resolution during later traffic
forwarding. It sends ARP requests for the IP address in a dynamic ARP entry. 

·     If the device receives an ARP reply before the
entry aging timer expires, the device resets the aging timer. 

·     If the device does not receive any ARP reply
after the maximum number of probes is made, the device deletes the entry when
the entry aging timer expires. 

You can set the maximum number of probes in
system view or in interface view. The setting in interface view takes
precedence over that in system view.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Set the maximum number of probes for dynamic
ARP entries.

¡     Set
the maximum number of probes for dynamic ARP entries in system view.

arp timer aging probe-count count

By default, the maximum number of probes for
dynamic ARP entries is 3 in system view.

¡     Execute
the following commands in sequence to set the maximum number of probes for
dynamic ARP entries on an interface:

interface interface-type interface-number

arp timer aging probe-count count

By default, the maximum number of probes for
dynamic ARP entries set on an interface is that set in system view.

### Setting the interval for probing dynamic ARP entries

#### About this task

The probing feature keeps legal dynamic ARP
entries valid and avoids unnecessary ARP resolution during later traffic
forwarding. 

Before a dynamic ARP entry is aged out, the
device sends ARP requests for the IP address in the ARP entry. 

·     If the device receives an ARP reply during the
probe interval, the device resets the aging timer. 

·     If the device does not receive any ARP reply
during the probe interval, the device starts a new probe.

·     If the maximum number probes are made, and still
no ARP reply is received, the device deletes the entry. 

You can set the probe interval in system
view and in interface view. The probe interval in interface view takes
precedence over the probe interval in system view.

#### Restrictions and guidelines

·     If massive traffic exists in the network, set a
long interval.

·     During the dynamic ARP entry probing process, a
dynamic ARP entry will not be deleted when its aging time expires. If a reply
is received during the probe, the aging timer of the ARP entry is reset.

·     To allow the device to perform the specified
number of probes, make sure the following requirement is met:

Aging time of the dynamic ARP entries \>
the maximum number of probes × probe interval

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Set the interval for probing dynamic ARP
entries.

¡     Set
the interval for probing dynamic ARP entries in system view.

arp timer aging probe-interval interval

By default, the probe interval is 5
seconds.

¡     Execute
the following commands in sequence to set the interval for probing dynamic ARP
entries on an interface:

interface interface-type interface-number

arp timer aging probe-interval interval

By default, the probe interval equals the
setting in system view.

### Enabling dynamic ARP entry check

#### About this task

The dynamic ARP entry check feature disables
the device from supporting dynamic ARP entries that contain multicast MAC
addresses. The device cannot learn dynamic ARP entries containing multicast MAC
addresses. You cannot manually add static ARP entries containing multicast MAC
addresses.

When dynamic ARP entry check is disabled, ARP
entries containing multicast MAC addresses are supported. The device can learn
dynamic ARP entries containing multicast MAC addresses obtained from the ARP
packets sourced from a unicast MAC address. You can also manually add static
ARP entries containing multicast MAC addresses.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enable dynamic ARP entry check.

arp check enable

By default, dynamic ARP entry check is enabled.

## Synchronizing ARP entries across all member devices

#### About this task

This task ensures that all IRF member
devices in an IRF fabric have the same ARP entries.

#### Restrictions and guidelines

To synchronize ARP entries across all
member devices in a timely manner, you can schedule the device to automatically
execute the arp smooth command. For
information about scheduling a task, see the device management configuration in
Fundamentals Configuration Guide.

#### Procedure

To synchronize ARP entries from the master
device to all subordinate devices, execute the following command in user view:

arp smooth

## Configuring a customer-side or network-side port

#### About this task

By default, a port operates as a
network-side port. The device associates an ARP entry with routing information
when the interface learns an ARP entry. The ARP entry provides the next hop
information for routing. 

When the port operates as the customer-side
port, the device will not associate the routing information with the learned
ARP entries, thus saving hardware resources.

Restricted to the hardware next hop
resources on the device, the maximum number of dynamic ARP entries that a
network-side port can learn might be smaller than the value you set, and it
might be different from that on a network-side port. Select an appropriate
operating mode for a port according to the port location in your live network. 

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter interface view.

interface interface-type interface-number

**3\.**Configure the interface as a customer-side
port or a network-side port.

¡     Configure
the interface as a customer-side port.

arp mode uni

¡     Configure
the interface as a network-side port.

undo arp mode

By default, a port operates as a
network-side port.

## Enabling recording user IP address conflicts

#### About this task

This feature enables the device to detect
and record user IP address conflicts. The device determines that a conflict
occurs if an incoming ARP packet has the same sender IP address as an existing
ARP entry but a different sender MAC address. The device generates a user IP
address conflict record, logs the conflict, and sends the log to the
information center. For information about the log destination and output rule
configuration in the information center, see the information center in Network Management and Monitoring Configuration Guide.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enable recording user IP address conflicts.

arp user-ip-conflict record enable

By default, recording user IP address
conflicts is disabled.

## Enabling interface consistency check between ARP and MAC address entries

#### About this task

In an unstable network, the receiving
interface for packets from a user might change. The interface in the MAC
address entry can be updated immediately while the interface in the ARP entry
cannot. In this case, the packets matching the ARP entry will be sent out of an
incorrect interface. To solve this problem, you can use this feature to
periodically check the interface consistency between the ARP and MAC address
entry for a user. If the interfaces are not the same, ARP sends ARP requests in
the VLAN of the ARP entry and updates the entry with the ARP reply receiving
interface.

Use display mac-address to display MAC address entries. For more information about this
command, see MAC address table in Layer 2—LAN Switching
Command Reference.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enable interface consistency check between
ARP and MAC address entries.

arp mac-interface-consistency check enable

By default, interface consistency check
between ARP and MAC address entries is disabled.

## Enabling recording user port migrations

#### About this task

This feature enables the device to detect
and record user port migration events. A user port migrates if an incoming ARP
packet has the same sender IP address and sender MAC address as an existing ARP
entry but a different ingress port. The device generates a user port migration record,
logs the migration event, sends the log to the information center, and updates
the interface for the ARP entry. For information about the log destination and
output rule configuration in the information center, see the information center
in Network Management and Monitoring Configuration Guide.

#### Restrictions and guidelines

To avoid device performance degradation,
disable recording user port migrations if too many user port migration logs are
generated.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enable recording user port migrations.

arp user-move record enable

By default, recording user port
migrations is disabled.

## Enabling unknown source MAC-triggered ARP probing

#### About this task

By default, upon receiving a data packet
with an unknown source MAC address from a user, the device learns a MAC address
entry. It does not learn an ARP entry so the device cannot send packets
destined for the user. To resolve this issue, you can enable unknown source
MAC-triggered ARP probing.

This feature enables the device to perform
ARP probing upon receiving a data packet with an unknown source MAC address.
During ARP probing, the device sends an ARP request out of the receiving
interface with the source IP address of the data packet as the target MAC
address. If the device receives an ARP reply, the device creates an ARP entry.

In inter-AC roaming, a client roams among
APs that are managed by different ACs with its IP address unchanged. For the
client to receive packets after it roams to a new AC, enable this feature on
the ACs.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter interface view.

interface interface-type
interface-number

**3\.**Enable unknown source MAC-triggered ARP
probing.

arp unknown-source-mac-probing enable

By default, unknown
source MAC-triggered ARP probing is disabled.

## Enabling ARP logging

#### About this task

This feature enables a device to log ARP events
when ARP cannot resolve IP addresses correctly. The log information helps
administrators locate and solve problems. The device can log the following ARP events:

·     On a proxy ARP-disabled interface, the target IP
address of a received ARP packet is not one of the following IP addresses:

¡     The
IP address of the receiving interface.

¡     The
virtual IP address of the VRRP group.

·     The sender IP address of a received ARP reply
conflicts with one of the following IP addresses:

¡     The
IP address of the receiving interface.

¡     The
virtual IP address of the VRRP group.

The device sends ARP log messages to the
information center. You can use the info-center source command to specify the log output rules for the information center.
For more information about information center, see Network
Management and Monitoring Configuration Guide. 

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enable ARP logging.

arp check log
enable

By default, ARP logging is disabled.

## Enablingerror logging for ARP entry deployment to hardware

#### About this task

After the device learns ARP entries, it
deploys the ARP entries to hardware for packet forwarding. After you enable
this feature, the device logs the errors that occur during the deployment. The
logs are sent to the information center of the device. For the logs to be
output correctly, you must also configure the information center on the device.
For more information about information center configuration, see Network Management and Monitoring Configuration Guide.

#### Restrictions and guidelines

Log generation occupies memory. As a best
practice, enable this feature only when traffic forwarding is abnormal for
troubleshooting.

If the maximum number of logs that can be
output per second is reached, no new error logs will be output.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enable error logging for ARP entry
deployment to hardware.

arp hardware log enable
\[ count-limit count-limit-value ]

By default, error logging for ARP entry
deployment to hardware is disabled.

## Enabling SNMP notifications for ARP

#### About this task

Enable SNMP notifications for ARP as
required.

·     If you enable SNMP notifications for ARP packet
rate limit, the device sends the peak rate as a notification to the SNMP module
when the ARP packet rate on an interface exceeds the specified rate limit.

·     If you enable user IP address conflict SNMP
notifications for ARP, the device sends a notification to the SNMP module when
a user IP address conflict occurs. The notification includes the sender IP and
MAC addresses in the conflicted ARP packet, and MAC address in the
corresponding local ARP entry.

For ARP event notifications to be sent
correctly, you must also configure SNMP on the device. For more information
about SNMP configuration, see Network Management and
Monitoring Configuration Guide.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enable SNMP notifications for ARP.

snmp-agent trap enable arp \[ rate-limit \| user-ip-conflict ] \*

By default, SNMP notifications for ARP
are disabled.

If you do not specify any keywords, this
command enables all types of SNMP notifications for ARP.

## Display and maintenance commands for ARP

| CAUTION | CAUTION:  Clearing ARP entries from the ARP table might cause communication failures. Make sure the entries to be cleared do not affect current communications. |
| --- | --- |

‌

Execute display commands
in any view and reset commands in user
view. 

 

| Task | Command |
| --- | --- |
| Display ARP entries. | display arp \[ \[ all \| dynamic \| multiport \| static ] \[ slot slot-number ] \| vlan vlan-id \| interface interface-type interface-number ] \[ count \| verbose ] || Display the maximum number of ARP entries that a device supports. | display arp entry-limit || Display the ARP entry for an IP address. | display arp ip-address \[ slot slot-number ] \[ verbose ] || Display the number of OpenFlow ARP entries. | display arp openflow count \[ slot slot-number ] || Display the aging timer of dynamic ARP entries. | display arp timer aging || Display the ARP table usage. | display arp usage || Display user IP address conflicts. | display arp user-ip-conflict record \[ slot slot-number ] || Display user port migrations. | display arp user-move record \[ slot slot-number ] || Display the ARP entries for a VPN instance. | display arp vpn-instance vpn-instance-name \[ count \| verbose ] || Clear ARP entries from the ARP table. | reset arp { all \| dynamic \| interface interface-type interface-number \| multiport \| slot slot-number \| static } |










‌

## ARP configuration examples

### Example: Configuring a long static ARP entry

#### Network configuration

As shown in [Figure 3](#_Ref216002388), hosts
are connected to Device B. Device B is connected to Device A through interface Ten-GigabitEthernet 1/0/1 in VLAN 10\. 

To ensure secure communications between Device
A and Device B, configure a long static ARP entry for Device A on Device B.

Figure 3 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11704491_x_Img_x_png_3_2215991_294551_0.png)

‌

#### Procedure

\# Create VLAN 10\.

\<DeviceB\> system-view

\[DeviceB] vlan 10

\[DeviceB-vlan10] quit

\# Add interface Ten-GigabitEthernet 1/0/1 to VLAN 10\.

\[DeviceB] interface ten-gigabitethernet 1/0/1

\[DeviceB-Ten-GigabitEthernet1/0/1] port access vlan
10

\[DeviceB-Ten-GigabitEthernet1/0/1] quit

\# Create VLAN-interface 10 and configure its
IP address.

\[DeviceB] interface vlan-interface 10

\[DeviceB-vlan-interface10] ip address
192.168.1.2 8

\[DeviceB-vlan-interface10] quit

\# Configure a long static ARP entry that
has IP address 192.168.1.1, MAC address 00e0-fc01-0000, and output interface Ten-GigabitEthernet 1/0/1 in VLAN 10\.

\[DeviceB] arp static 192.168.1.1 00e0-fc01-0000
10 ten-gigabitethernet 1/0/1

#### Verifying the configuration

\# Verify that Device B has a long static
ARP entry for Device A.

\[DeviceB] display arp static

  Type: S-Static   D-Dynamic  
O-Openflow   R-Rule   M-Multiport  I-Invalid

IP address      MAC address    VLAN/VSI
name Interface                Aging Type

192.168.1.1     00e0-fc01-0000 10            XGE1/0/1 
               \--    S

### Example: Configuring a short static ARP entry

#### Network configuration

As shown in [Figure 4](#_Ref388969383), hosts
are connected to Device B. Device B is connected to Device A through interface Ten-GigabitEthernet 1/0/2.

To ensure secure communications between Device
A and Device B, configure a short static ARP entry for Device A on Device B.

Figure 4 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11704492_x_Img_x_png_4_2215991_294551_0.png)

‌

#### Procedure

\# Configure an IP address for Ten-GigabitEthernet 1/0/2.

\<DeviceB\> system-view

\[DeviceB] interface ten-gigabitethernet 1/0/2

\[DeviceB-Ten-GigabitEthernet1/0/2] ip address
192.168.1.2 24

\[DeviceB-Ten-GigabitEthernet1/0/2] quit

\# Configure a short static ARP entry that
has IP address 192.168.1.1 and MAC address 00e0-fc01-001f.

\[DeviceB] arp static 192.168.1.1
00e0-fc01-001f

#### Verifying the configuration

\# Verify that Device B has a short static
ARP entry for Device A

\[DeviceB] display arp static

  Type: S-Static   D-Dynamic  
O-Openflow   R-Rule   M-Multiport  I-Invalid

IP address      MAC address    VLAN/VSI
name Interface                Aging Type

192.168.1.1     00e0-fc01-001f \--            \--      
                \--    S

### Example: Configuring a multiport ARP entry

#### Network configuration

As shown in [Figure 5](#_Ref319324231), a device
connects to three servers through interfaces Ten-GigabitEthernet 1/0/1, Ten-GigabitEthernet 1/0/2, and Ten-GigabitEthernet 1/0/3 in VLAN 10\. The
servers share the IP address 192.168.1.1/24 and MAC address 00e0-fc01-0000.

Configure a multiport ARP entry so that the
device sends IP packets with the destination IP address 192.168.1.1 to the
three servers.

Figure 5 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11704493_x_Img_x_png_5_2215991_294551_0.png)

‌

#### Procedure

\# Create VLAN 10\.

\<Device\> system-view

\[Device] vlan 10

\[Device-vlan10] quit

\# Add Ten-GigabitEthernet 1/0/1, Ten-GigabitEthernet 1/0/2, and Ten-GigabitEthernet 1/0/3 to VLAN 10\.

\[Device] interface ten-gigabitethernet 1/0/1

\[Device-Ten-GigabitEthernet1/0/1] port access vlan
10

\[Device-Ten-GigabitEthernet1/0/1] quit

\[Device] interface ten-gigabitethernet 1/0/2

\[Device-Ten-GigabitEthernet1/0/2] port access vlan
10

\[Device-Ten-GigabitEthernet1/0/2] quit

\[Device] interface ten-gigabitethernet 1/0/3

\[Device-Ten-GigabitEthernet1/0/3] port access vlan
10

\[Device-Ten-GigabitEthernet1/0/3] quit

\# Create VLAN-interface 10 and specify its
IP address.

\[Device] interface vlan-interface 10

\[Device-vlan-interface10] ip address
192.168.1.2 24

\[Device-vlan-interface10] quit

\# Configure a multiport unicast MAC address
entry that has MAC address 00e0-fc01-0000, and output interfaces Ten-GigabitEthernet 1/0/1, Ten-GigabitEthernet 1/0/2, and Ten-GigabitEthernet 1/0/3 in VLAN 10\.

\[Device] mac-address multiport
00e0-fc01-0000 interface ten-gigabitethernet 1/0/1 to ten-gigabitethernet 1/0/3 vlan 10

\# Configure a multiport ARP entry with IP
address 192.168.1.1 and MAC address 00e0-fc01-0000.

\[Device] arp multiport 192.168.1.1
00e0-fc01-0000 10

#### Verifying the configuration

\# Verify that the device has a multiport
ARP entry with IP address 192.168.1.1 and MAC address 00e0-fc01-0000.

\[Device] display arp

  Type: S-Static   D-Dynamic  
O-Openflow   R-Rule   M-Multiport  I-Invalid

IP address      MAC address    VLAN/VSI
name Interface                Aging Type

192.168.1.1     00e0-fc01-0000 10            \--       
               \--    M

 

