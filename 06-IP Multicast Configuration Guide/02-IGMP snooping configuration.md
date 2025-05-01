
# Configuring IGMP snooping

## About IGMP snooping

IGMP snooping runs on a Layer 2 device as a multicast constraining
mechanism to improve multicast forwarding efficiency. It creates Layer 2
multicast forwarding entries from IGMP packets that are exchanged between the
hosts and the Layer 3 device.

### Fundamentals of IGMP snooping

As shown in [Figure 1](#_Ref370998285), when
IGMP snooping is not enabled, the Layer 2 switch floods multicast packets to
all hosts in a VLAN or VSI. When IGMP snooping is enabled, the Layer 2 switch
forwards multicast packets of known multicast groups to only the receivers.

Figure 1 Multicast packet transmission
without and with IGMP snooping

![](https://resource.h3c.com/en/202407/12/20240712_11704937_x_Img_x_png_0_2216031_294551_0.png)

 

### IGMP snooping ports

As shown in [Figure 2](#_Ref313026055),
IGMP snooping runs on Switch A and Switch B, and Host A and Host C are receivers
in a multicast group. IGMP snooping ports are divided into member ports and
router ports.

Figure 2 IGMP snooping ports

![](https://resource.h3c.com/en/202407/12/20240712_11704938_x_Img_x_png_1_2216031_294551_0.png)

 

#### Router ports

On an IGMP snooping Layer 2 device, the
ports toward Layer 3 multicast devices are called router ports. In [Figure 2](#_Ref313026055), Port
A1 of Switch A and Port B1 of Switch B are router ports.

Router ports contain the following types:

·     Dynamic router port—When a port receives an IGMP general query whose source address is
not 0.0.0.0 or receives a PIM hello message, the port is added into the dynamic
router port list. At the same time, an aging timer is started for the port. If
the port receives either of the messages before the timer expires, the timer is
reset. If the port does not receive either of the messages when the timer
expires, the port is removed from the dynamic router port list.

·     Static router port—When a port is statically configured as a router port, it is added
into the static router port list. The static router port does not age out, and it
can be deleted only manually.

Do not confuse the "router port"
in IGMP snooping with the "routed interface" commonly known as the
"Layer 3 interface." The router port in IGMP snooping is a Layer 2
interface.

#### Member ports

On an IGMP snooping Layer 2 device, the
ports toward receiver hosts are called member ports. In [Figure 2](#_Ref313026055), Port
A2 and Port A3 of Switch A and Port B2 of Switch B are member ports.

Member ports contain the following types:

·     Dynamic member port—When a port receives an IGMP report, it is added to the associated
dynamic IGMP snooping forwarding entry as an outgoing interface. At the same
time, an aging timer is started for the port. If the port receives an IGMP
report before the timer expires, the timer is reset. If the port does not
receive an IGMP report when the timer expires, the port is removed from the
associated dynamic forwarding entry.

·     Static member port—When a port is statically configured as a member port, it is added
to the associated static IGMP snooping forwarding entry as an outgoing
interface. The static member port does not age out, and it can be deleted only manually.

Unless otherwise specified, router ports
and member ports in this document include both static and dynamic router ports
and member ports.

### How IGMP snooping works

The ports in this section are dynamic
ports. For information about how to configure and remove static ports, see "[Configuring a static member port](#_Ref311648022)" and "[Configuring a static router port](#_Ref454267586)."

IGMP messages types include general query, IGMP
report, and leave message. An IGMP snooping-enabled Layer 2 device performs
differently depending on the message types.

#### General query

The IGMP querier periodically sends IGMP
general queries to all hosts and devices on the local subnet to check for the existence
of multicast group members.

After receiving an IGMP general query, the Layer
2 device forwards the query to all ports in the VLAN or VSI except the receiving
port. The Layer 2 device also performs one of the following actions:

·     If the receiving port is a dynamic router port in
the dynamic router port list, the Layer 2 device restarts the aging timer for
the port.

·     If the receiving port does not exist in the dynamic
router port list, the Layer 2 device adds the port to the dynamic router port
list. It also starts an aging timer for the port.

#### IGMP report

A host sends an IGMP report to the IGMP
querier for the following purposes:

·     Responds to queries if the host is a multicast
group member.

·     Applies for a multicast group membership.

After receiving an IGMP report from a host,
the Layer 2 device forwards the report through all the router ports in the VLAN
or VSI. It also resolves the address of the reported multicast group, and looks
up the forwarding table for a matching entry as follows:

·     If no match is found, the Layer 2 device creates
a forwarding entry with the receiving port as an outgoing interface. It also
marks the receiving port as a dynamic member port and starts an aging timer for
the port.

·     If a match is found but the matching forwarding
entry does not contain the receiving port, the Layer 2 device adds the receiving
port to the outgoing interface list. It also marks the receiving port as a dynamic
member port and starts an aging timer for the port.

·     If a match is found and the matching forwarding
entry contains the receiving port, the Layer 2 device restarts the aging timer
for the port.

 

|  | NOTE:  A Layer 2 device does not forward an IGMP report through a non-router port because of the host IGMP report suppression mechanism. If a non-router port has member host attached, the member hosts suppress their IGMP reports upon receiving IGMP reports forwarded by the non-router port. The Layer 2 device cannot know the existence of the member hosts attached to the non-router port. For more information about the IGMP report suppression mechanism, see "Configuring IGMP." |
| --- | --- |

 

#### Leave message

An IGMPv1 receiver host does not send any
leave messages when it leaves a multicast group. The Layer 2 device cannot
immediately update the status of the port that connects to the receiver host. The
Layer 2 device does not remove the port from the outgoing interface list in the
associated forwarding entry until the aging time for the group expires. 

An IGMPv2 or IGMPv3 host sends an IGMP
leave message when it leaves a multicast group. 

When the Layer 2 device receives an IGMP
leave message on a dynamic member port, the Layer 2 device first examines
whether a forwarding entry matches the group address in the message.

·     If no match is found, the Layer 2 device
discards the IGMP leave message.

·     If a match is found but the receiving port is
not an outgoing interface in the forwarding entry, the Layer 2 device discards
the IGMP leave message.

·     If a match is found and the receiving port is
not the only outgoing interface in the forwarding entry, the Layer 2 device
performs the following actions:

¡     Discards
the IGMP leave message.

¡     Sends
an IGMP group-specific query to identify whether the group has active receivers
attached to the receiving port.

¡     Sets
the aging timer for the receiving port to twice the IGMP last member query
interval.

·     If a match is found and the receiving port is
the only outgoing interface in the forwarding entry, the Layer 2 device performs
the following actions:

¡     Forwards
the IGMP leave message to all router ports in the VLAN or VSI.

¡     Sends
an IGMP group-specific query to identify whether the group has active receivers
attached to the receiving port.

¡     Sets
the aging timer for the receiving port to twice the IGMP last member query
interval.

After receiving the IGMP leave message on a
port, the IGMP querier resolves the multicast group address in the message.
Then, it sends an IGMP group-specific query to the multicast group through the receiving
port.

After receiving the IGMP group-specific
query, the Layer 2 device forwards the query through all router ports and
member ports of the group in the VLAN or VSI. Then, it waits for the responding
IGMP report from the directly connected hosts. For the dynamic member port that
received the leave message, the Layer 2 device also performs one of the
following actions:

·     If the port receives an IGMP report before the aging
timer expires, the Layer 2 device resets the aging timer.

·     If the port does not receive an IGMP report when
the aging timer expires, the Layer 2 device removes the port from the
forwarding entry for the multicast group.

### IGMP snooping proxying

As shown in [Figure 3](#_Ref447267411), to reduce the number
of IGMP report and leave messages received by the upstream device, you can enable
IGMP snooping proxying on the edge device. With IGMP snooping proxying enabled,
the edge device acts as a host for the upstream IGMP snooping querier to send
IGMP report and leave messages to Device A. The host IGMP report suppression
mechanism on the edge device does not take effect. For more information about the
IGMP report suppression mechanism, see "Configuring IGMP."

Figure 3 IGMP snooping proxying

![](https://resource.h3c.com/en/202407/12/20240712_11704939_x_Img_x_png_2_2216031_294551_0.png)

 

The IGMP snooping proxy device processes different
IGMP messages as follows:

·     General query.

After receiving an IGMP general query,
the device forwards the query to all ports in the VLAN or VSI except the receiving
port. The device also generates an IGMP report based on the local membership
information and sends the report to all router ports.

·     Group-specific query or group-and-source-specific
query.

After receiving an IGMP group-specific
query or group-and-source-specific query, the device forwards the query to all
ports in the VLAN or VSI except the receiving port. If the forwarding entry has
a member port, the device sends a report to all router ports in the VLAN or VSI.

·     Report.

After receiving an IGMP report from a
host, the device looks up the forwarding table for a matching entry as follows:

¡     If a
match is found and the matching forwarding entry contains the receiving port,
the device resets the aging timer for the port.

¡     If a
match is found but the matching forwarding entry does not contain the receiving
port, the device adds the receiving port to the outgoing interface list. It
also marks the receiving port as a dynamic member port and starts an aging
timer for the port.

¡     If
no match is found, the device creates a forwarding entry with the receiving port
as an outgoing interface. It also marks the receiving port as a dynamic member
port and starts an aging timer for the port. Then it sends the report to all
router ports.

·     Leave message.

After receiving the IGMP leave message on
a port, the device sends an IGMP group-specific query through the receiving port.
The device sends the IGMP leave message to all router ports only when the last
member port is removed from the forwarding entry.

### Protocols and standards

RFC 4541, Considerations
for Internet Group Management Protocol (IGMP) and Multicast Listener Discovery
(MLD) Snooping Switches

## Restrictions and guidelines: IGMP snooping configuration

For IGMP reports received from secondary
VLANs, the associated IGMP snooping forwarding entries are maintained by the primary
VLAN. Therefore, you need to enable IGMP snooping only for the primary VLAN.
The IGMP snooping configuration made in secondary VLANs does not take effect.
For more information about primary VLANs and secondary VLANs, see Layer 2—LAN Switching Configuration Guide.

The IGMP snooping configurations made on
Layer 2 aggregate interfaces do not interfere with the configurations made on
member ports. In addition, the configurations made on Layer 2 aggregate
interfaces do not take part in aggregation calculations. The configuration made
on a member port of the aggregate group takes effect after the port leaves the
aggregate group.

Some features can be configured for a VLAN
in VLAN view or for multiple VLANs in IGMP-snooping view. The configuration
made in VLAN view and the configuration made in IGMP-snooping view have the
same priority, and the most recent configuration takes effect.

Some features can be configured for a VLAN
in VLAN view or globally for all VLANs in IGMP-snooping view. The VLAN-specific
configuration takes priority over the global configuration.

Some features can be configured for a VSI
in VSI view or globally for all VSIs in IGMP-snooping view. The VSI-specific
configuration takes priority over the global configuration.

Some features can be configured for an
interface in interface view or for all interfaces of the specified VLANs in IGMP-snooping
view. The interface-specific configuration takes priority over the configuration
made in IGMP-snooping view.

## VLAN-based IGMP snooping tasks at a glance

To configure IGMP snooping for VLANs,
perform the following tasks:

**1\.**[Enabling the IGMP snooping feature](#_Ref480274866)

**2\.**[Enabling IGMP snooping](#_Ref480274873)

Choose the following tasks as needed:

¡     [Enabling IGMP snooping globally](#_Ref480274895)

¡     [Enabling IGMP snooping for VLANs](#_Ref480274901)

**3\.**(Optional.) [Configuring basic IGMP snooping features](#_Ref474337131)

[¡     Specifying an IGMP snooping version](#_Ref360082967)

¡     [Setting the maximum number of IGMP
snooping forwarding entries](#_Ref205091898)

¡     [Configuring static multicast MAC address
entries](#_Ref445913104)

¡     [Setting the IGMP last member query
interval](#_Ref445913153)

**4\.**(Optional.) [Configuring IGMP snooping port features](#_Ref206564202)

¡     [Setting aging timers for dynamic ports](#_Ref386204268)

¡     [Configuring a static member port](#_Ref311648052)

¡     [Configuring a static router port](#_Ref454267586)

¡     [Configuring a port as a simulated member
host](#_Ref474337160)

¡     [Enabling fast-leave processing](#_Ref365014500)

¡     [Disabling a port from becoming a dynamic
router port](#_Ref474337171)

¡     [Preventing the device from forwarding
multicast data to router ports in a VLAN](#_Ref79574335)

**5\.**(Optional.) [Specifying the Layer 2 multicast
forwarding mode](#_Ref529811828)

**6\.**(Optional.) [Configuring the IGMP snooping querier](#_Ref165195457)

¡     [Enabling the IGMP snooping querier](#_Ref386128228)

¡     [Enabling IGMP snooping querier election](#_Ref490244939)

¡     [Configuring parameters for IGMP general queries
and responses](#_Ref386128236)

¡     [Enabling the device to send IGMP general
queries upon a path change](#_Ref508871222)

**7\.**(Optional.) [Enabling IGMP snooping proxying](#_Ref444519467)

**8\.**(Optional.) [Configuring parameters for IGMP messages](#_Ref365014581)

¡     [Configuring source IP addresses for IGMP
messages](#_Ref365014597)

¡     [Setting the 802.1p priority for IGMP
messages](#_Ref474337209)

**9\.**(Optional.) [Configuring IGMP snooping policies](#_Ref365014618)

¡     [Configuring a multicast group policy](#_Ref392495583)

¡     [Enabling multicast source port filtering](#_Ref332718041)

¡     [Enabling dropping unknown multicast data](#_Ref332967146)

¡     [Enabling IGMP report suppression](#_Ref474337237)

¡     [Setting the maximum number of multicast
groups on a port](#_Ref357765191)

¡     [Enabling multicast group replacement](#_Ref392495606)

¡     [Enabling host tracking](#_Ref427074201)

**10\.**(Optional.) [Setting the DSCP value for outgoing IGMP
protocol packets](#_Ref491337104)

## VSI-based IGMP snooping tasks at a glance

To configure IGMP snooping for VSIs,
perform the following tasks:

**1\.**[Enabling the IGMP snooping feature](#_Ref480274801)

**2\.**[Enabling IGMP snooping](#_Ref480274873)

Choose the following tasks as needed:

¡     [Enabling IGMP snooping globally](#_Ref480275411)

¡     [Enabling IGMP snooping for a VSI](#_Ref480275424)

**3\.**(Optional.) [Configuring basic IGMP snooping features](#_Ref474337131)

[¡     Specifying an IGMP snooping version](#_Ref360082967)

¡     [Setting the maximum number of IGMP
snooping forwarding entries](#_Ref205091898)

¡     [Setting the IGMP last member query
interval](#_Ref445913153)

**4\.**(Optional.) [Setting aging timers for dynamic ports](#_Ref386204268)

**5\.**(Optional.) [Configuring the IGMP snooping querier](#_Ref165195457)

¡     [Enabling the IGMP snooping querier](#_Ref386128228)

¡     [Enabling IGMP snooping querier election](#_Ref490244940)

¡     [Configuring parameters for IGMP general queries
and responses](#_Ref386128236)

¡     [Enabling the device to send IGMP general queries
upon a port state change](#_Ref75534979)

**6\.**(Optional.) [Enabling IGMP snooping proxying](#_Ref444519467)

**7\.**(Optional.) [Configuring parameters for IGMP messages](#_Ref365014581)

¡     [Configuring source IP addresses for IGMP
messages](#_Ref365014597)

¡     [Setting the 802.1p priority for IGMP
messages](#_Ref474337209)

**8\.**(Optional.) [Configuring IGMP snooping policies](#_Ref365014618)

¡     [Enabling dropping unknown multicast data](#_Ref332967146)

¡     [Enabling IGMP report suppression](#_Ref474337237)

**9\.**(Optional.) [Setting the DSCP value for outgoing IGMP
protocol packets](#_Ref491337104)

## Enabling the IGMP snooping feature

#### About this task

You must enable the IGMP snooping feature
before you configure other IGMP snooping features.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enable the IGMP snooping feature and enter
IGMP-snooping view.

igmp-snooping

By default, the IGMP snooping feature is
disabled.

## Enabling IGMP snooping

### Enabling IGMP snooping globally

#### About this task

After you enable IGMP snooping globally,
IGMP snooping is enabled for all VLANs or VSIs. You can disable IGMP snooping for
a VLAN or VSI when IGMP snooping is globally enabled.

#### Restrictions and guidelines

To configure other IGMP snooping features
for VLANs or VSIs, you must enable IGMP snooping for the specific VLANs or VSIs
even though IGMP snooping is enabled globally.

The VLAN-specific or VSI-specific IGMP
snooping configuration takes priority over the global IGMP snooping configuration.
For example, if you enable IGMP snooping globally and then use the igmp-snooping
disable command to disable IGMP snooping for a
VLAN, IGMP snooping is disabled in the VLAN. 

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter IGMP-snooping view.

igmp-snooping

**3\.**Enable IGMP snooping globally.

global-enable

By default, IGMP snooping is disabled
globally.

**4\.**(Optional.) Disable IGMP snooping for a VLAN
or VSI.

**a.**Return to system view.

quit

**b.**Enter VLAN or VSI view.

vlan vlan-id

vsi vsi-name

**c.**Disable IGMP snooping for the VLAN or VSI.

igmp-snooping disable

By default, the IGMP snooping status in a
VLAN or VSI is consistent with the global IGMP snooping status.

### Enabling IGMP snooping for VLANs

#### Restrictions and guidelines

You can enable IGMP snooping for multiple
VLANs by using the enable vlan command in
IGMP-snooping view or for a VLAN by using the igmp-snooping
enable command in VLAN view. The configuration
in VLAN view has the same priority as the configuration in IGMP-snooping view.

IGMP snooping configuration in a VLAN takes
effect only on the member ports in the VLAN.

#### Enabling IGMP snooping for multiple VLANs

**1\.**Enter system view.

system-view

**2\.**Enter IGMP-snooping view.

igmp-snooping

**3\.**Enable IGMP snooping for the specified
VLANs.

enable vlan vlan-list

By default, the IGMP snooping status in a
VLAN is consistent with the global IGMP snooping status.

#### Enabling IGMP snooping for a VLAN

**1\.**Enter system view.

system-view

**2\.**Enter VLAN view.

vlan vlan-id

**3\.**Enable IGMP snooping for the VLAN.

igmp-snooping enable

By default, the IGMP snooping status in a
VLAN is consistent with the global IGMP snooping status.

### Enabling IGMP snooping for a VSI

#### Restrictions and guidelines

For VSIs, you can only enable IGMP snooping
for a VSI by using the igmp-snooping enable
command in VSI view.

IGMP snooping configuration in a VSI takes
effect only on the member ports in the VSI.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter VSI view.

vsi vsi-name

**3\.**Enable IGMP snooping for the VSI.

igmp-snooping enable

By default, the IGMP snooping status in a
VSI is consistent with the global IGMP snooping status.

## Configuring basic IGMP snooping features

### Specifying an IGMP snooping version

#### About this task

Different IGMP snooping versions process
different versions of IGMP messages.

·     IGMPv2 snooping processes IGMPv1 messages, IGMPv2
messages, and IGMPv3 queries, but it floods IGMPv3 reports in the VLAN or VSI instead
of processing them.

·     IGMPv3 snooping processes IGMPv1, IGMPv2, and
IGMPv3 messages.

#### Restrictions and guidelines

If you change the IGMP snooping version
from 2 to 3, the device performs the following actions:

·     Clears all IGMP snooping forwarding entries that
are dynamically created.

·     Keeps static IGMPv3 snooping forwarding entries
(\*, G).

·     Clears static IGMPv3 snooping forwarding entries
(S, G), which will be restored when the IGMP snooping version is switched back
to 3\.

For more information about static IGMP
snooping forwarding entries, see "[Configuring a static member port](#_Ref311648052)."

#### Specifying an IGMP snooping version for multiple VLANs

**1\.**Enter system view.

system-view

**2\.**Enter IGMP-snooping view.

igmp-snooping

**3\.**Specify an IGMP snooping version for multiple
VLANs.

version version-number vlan vlan-list

By default, the IGMP snooping version for
a VLAN is 2\.

#### Specifying an IGMP snooping version for a VLAN

**1\.**Enter system view.

system-view

**2\.**Enter VLAN view.

vlan vlan-id

**3\.**Specify an IGMP snooping version for the
VLAN.

igmp-snooping version version-number

By default, the IGMP snooping version for
a VLAN is 2\.

#### Specifying an IGMP snooping version for a VSI

**1\.**Enter system view.

system-view

**2\.**Enter VSI view.

vsi vsi-name

**3\.**Specify an IGMP snooping version for the
VSI.

igmp-snooping version version-number

By default, the IGMP snooping version for
a VSI is 2\.

### Setting the maximum number of IGMP snooping forwarding entries

#### About this task

You can modify the maximum number of IGMP
snooping forwarding entries, including dynamic entries and static entries. When
the number of forwarding entries on the device reaches the upper limit, the
device does not automatically remove any existing entries. To allow new entries
to be created, remove some entries manually. 

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter IGMP-snooping view.

igmp-snooping

**3\.**Set the maximum number of IGMP snooping
forwarding entries.

entry-limit limit

By default, the maximum number of IGMP
snooping forwarding entries is 4294967295.

### Configuring static multicast MAC address entries

#### About this task

In Layer 2 multicast, multicast MAC address
entries can be dynamically created through Layer 2 multicast protocols (such as
IGMP snooping). You can also manually configure static multicast MAC address
entries by binding multicast MAC addresses and ports to control the destination
ports of the multicast data.

#### Restrictions and guidelines

You must specify an unused multicast MAC
address when configuring a static multicast MAC address entry. A multicast MAC
address is the MAC address in which the least significant bit of the most
significant octet is 1\.

#### Configuring a static multicast MAC address entry in system view

**1\.**Enter system view.

system-view

**2\.**Configure a static multicast MAC address
entry.

mac-address multicast mac-address interface interface-list vlan vlan-id

#### Configuring a static multicast MAC address entry in interface view

**1\.**Enter system view.

system-view

**2\.**Enter Layer 2 interface view.

¡     Enter
Layer 2 Ethernet interface view.

interface interface-type interface-number

¡     Enter
Layer 2 aggregate interface view.

interface bridge-aggregation interface-number

**3\.**Configure a static multicast MAC address
entry.

mac-address multicast mac-address vlan vlan-id

### Configuring static multicast MAC address entries for VXLAN

#### About this task

Use this feature to configure local and
remote static multicast MAC address entries for a VXLAN network. For more
information about VXLAN, see VXLAN Configuration Guide.

#### Restrictions and guidelines

Do not specify the tunnel interfaces
automatically created by using EVPN as outgoing interfaces for a remote static
multicast MAC address entry. If you do so, the numbers of these tunnel
interfaces might change during tunnel re-establishment, and the related entries
cannot be restored as a result. For more information about EVPN, see EVPN Configuration Guide.

In an EVPN network, you cannot configure
the same multicast MAC address entry on multiple leaf nodes or VTEPs. When
configured with EVPN multihoming, a VTEP does not support synchronization of
multicast MAC address entries.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Configure local static multicast MAC address
entries.

mac-address multicast mac-address { interface { interface-type interface-number service-instance
instance-id }\&\<1-4\> } vsi vsi-name

By default, no local static multicast MAC address entries exist.

For successful configuration, make sure
the specified Ethernet service instances have been mapped to the specified VSI.

**3\.**Configure remote static multicast MAC
address entries.

mac-address multicast mac-address { interface { tunnel tunnel-number1
\[ to tunnel tunnel-number2 ] }\&\<1-4\> } vsi vsi-name

By default, no remote static multicast
MAC address entries exist.

For successful configuration, make sure
the specified VXLAN tunnel interfaces have been associated with the specified
VSI.

### Setting the IGMP last member query interval

#### About this task

A receiver host starts a report delay timer
for a multicast group when it receives an IGMP group-specific query for the
group. This timer is set to a random value in the range of 0 to the maximum
response time advertised in the query. When the timer value decreases to 0, the
host sends an IGMP report to the group.

The IGMP last member query interval defines
the maximum response time advertised in IGMP group-specific queries. Set an
appropriate value for the IGMP last member query interval to speed up hosts'
responses to IGMP group-specific queries and avoid IGMP report traffic bursts.

#### Setting the IGMP last member query interval globally

**1\.**Enter system view.

system-view

**2\.**Enter IGMP-snooping view.

igmp-snooping

**3\.**Set the IGMP last member query interval
globally.

last-member-query-interval interval

By default, the global IGMP last member
query interval is 1 second.

#### Setting the IGMP last member query interval for a VLAN

**1\.**Enter system view.

system-view

**2\.**Enter VLAN view.

vlan vlan-id

**3\.**Set the IGMP last member query interval for
the VLAN.

igmp-snooping last-member-query-interval interval

By default, the IGMP last member query
interval is 1 second for a VLAN.

#### Setting the IGMP last member query interval for a VSI

**1\.**Enter system view.

system-view

**2\.**Enter VSI view.

vsi vsi-name

**3\.**Set the IGMP last member query interval for
the VSI.

igmp-snooping last-member-query-interval interval

By default, the IGMP last member query
interval is 1 second for a VSI.

## Configuring IGMP snooping port features

### Setting aging timers for dynamic ports

#### About this task

A dynamic router port is removed from the
dynamic router port list if it does not receive an IGMP general query or PIM
hello message when its aging timer expires.

A dynamic member port is removed from the
dynamic member port if it does not receive an IGMP report when its aging timer
expires.

#### Restrictions and guidelines

Set an appropriate value for the aging
timers of dynamic ports. For example, if the memberships of multicast groups frequently
change, set a relatively small value for the aging timer of the dynamic member
ports. If the memberships of multicast groups rarely change, set a relatively
large value.

If a dynamic router port receives a PIMv2
hello message, the aging timer for the port is specified by the hello message.
In this case, the router-aging-time or igmp-snooping
router-aging-time command does not take effect
on the port.

IGMP group-specific queries originated by
the Layer 2 device trigger the adjustment of aging timers for dynamic member
ports. If a dynamic member port receives such a query, its aging timer is set
to twice the IGMP last member query interval. For more information about
setting the IGMP last member query interval on the Layer 2 device, see "[Setting the IGMP last member query
interval](#_Ref445913153)."

#### Setting the aging timers for dynamic ports globally

**1\.**Enter system view.

system-view

**2\.**Enter IGMP-snooping view.

igmp-snooping

**3\.**Set the aging timer for dynamic router ports
globally.

router-aging-time seconds

By default, the aging timer for dynamic
router ports is 260 seconds.

**4\.**Set the global aging timer for dynamic
member ports globally.

host-aging-time seconds

By default, the aging timer for dynamic
member ports is 260 seconds.

#### Setting the aging timers for dynamic ports in a VLAN

**1\.**Enter system view.

system-view

**2\.**Enter VLAN view.

vlan vlan-id

**3\.**Set the aging timer for dynamic router ports
in the VLAN.

igmp-snooping router-aging-time seconds

By default, the aging timer for dynamic
router ports is 260 seconds.

**4\.**Set the aging timer for dynamic member ports
in the VLAN.

igmp-snooping host-aging-time seconds

By default, the aging timer for dynamic
member ports is 260 seconds.

#### Setting the aging timers for dynamic ports in a VSI

**1\.**Enter system view.

system-view

**2\.**Enter VSI view.

vsi vsi-name

**3\.**Set the aging timer for dynamic router ports
in the VSI.

igmp-snooping router-aging-time seconds

By default, the aging timer for dynamic
router ports is 260 seconds.

**4\.**Set the aging timer for dynamic member ports
in the VSI.

igmp-snooping host-aging-time seconds

By default, the aging timer for dynamic
member ports is 260 seconds.

### Configuring a static member port

#### About this task

You can configure a port as a static member
port for a multicast group so that hosts attached to the port can always
receive multicast for the group. The static member port does not respond to IGMP
queries. When you complete or cancel this configuration on a port, the port
does not send an unsolicited IGMP report or leave message.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter Layer 2 interface view.

¡     Enter
Layer 2 Ethernet interface view.

interface interface-type interface-number

¡     Enter
Layer 2 aggregate interface view.

interface bridge-aggregation interface-number

**3\.**Configure the port as a static member port.

igmp-snooping static-group group-address \[ source-ip source-address ] vlan vlan-id

By default, a port is not a static member
port.

### Configuring a static router port

#### About this task

You can configure a port as a static router
port for a multicast group so that all multicast data for the group received on
the port will be forwarded.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter Layer 2 interface view.

¡     Enter
Layer 2 Ethernet interface view.

interface interface-type interface-number

¡     Enter Layer 2 aggregate interface
view.

interface bridge-aggregation interface-number

**3\.**Configure the port as a static router port.

igmp-snooping static-router-port vlan vlan-id

By default, a port is not a static router
port.

### Configuring a port as a simulated member host

#### About this task

When a port is configured as a simulated
member host, it is equivalent to an independent host in the following ways:

·     It sends an unsolicited IGMP report when you
complete the configuration.

·     It responds to IGMP general queries with IGMP
reports.

·     It sends an IGMP leave message when you cancel the
configuration.

The version of IGMP running on the
simulated member host is the same as the version of IGMP snooping running on
the port. The port ages out in the same way as a dynamic member port.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter Layer 2 interface view.

¡     Enter
Layer 2 Ethernet interface view.

interface interface-type interface-number

¡     Enter Layer 2 aggregate
interface view.

interface bridge-aggregation interface-number

**3\.**Configure the port as a simulated member
host.

igmp-snooping host-join
group-address \[ source-ip source-address ] vlan vlan-id

By default, the port is not a simulated
member host.

### Enabling fast-leave processing

#### About this task

This feature enables the Layer 2 device to
immediately remove a port from the forwarding entry for a multicast group when
the port receives a leave massage. The Layer 2 device no longer sends or
forwards IGMP group-specific queries for the group to the port.

#### Restrictions and guidelines

Do not enable fast-leave processing on a
port that has multiple receiver hosts in a VLAN. If you do so, the remaining
receivers cannot receive multicast data for a group after a receiver leaves the
group.

#### Enabling fast-leave processing globally

**1\.**Enter system view.

system-view

**2\.**Enter IGMP-snooping view.

igmp-snooping

**3\.**Enable fast-leave processing globally.

fast-leave \[ vlan vlan-list ]

By default, fast-leave processing is
disabled globally.

#### Enabling fast-leave processing on a port

**1\.**Enter system view.

system-view

**2\.**Enter Layer 2 interface view.

¡     Enter
Layer 2 Ethernet interface view.

interface interface-type interface-number

¡     Enter Layer 2 aggregate
interface view.

interface bridge-aggregation interface-number

**3\.**Enable fast-leave processing on the port.

igmp-snooping fast-leave \[ vlan vlan-list ]

By default, fast-leave processing is
disabled on a port.

### Disabling a port from becoming a dynamic router port

#### About this task

A receiver host might send IGMP general queries
or PIM hello messages for testing purposes. On the Layer 2 device, the port
that receives either of the messages becomes a dynamic router port. Before the
aging timer for the port expires, the following problems might occur:

·     All multicast data for the VLAN to which the
port belongs flows to the port. Then, the port forwards the data to attached receiver
hosts. The receiver hosts will receive multicast data that it does not want to
receive.

·     The port forwards the IGMP general queries or
PIM hello messages to its upstream Layer 3 devices. These messages might affect
the multicast routing protocol state (such as the IGMP querier or M-LAG election)
on the Layer 3 devices. This might further cause network interruption.

To solve these problems, you can disable a port
from becoming a dynamic router port. This also improves network security and
the control over receiver hosts.

#### Restrictions and guidelines

This configuration and the static router
port configuration do not interfere with each other.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter Layer 2 interface view.

¡     Enter
Layer 2 Ethernet interface view.

interface interface-type interface-number

¡     Enter Layer 2 aggregate
interface view.

interface bridge-aggregation interface-number

**3\.**Disable the port from becoming a dynamic
router port.

igmp-snooping router-port-deny \[ vlan vlan-list ]

By default, a port is allowed to become a
dynamic router port.

### Preventing the device from forwarding multicast data to router ports in a VLAN

#### About this task

If all ports on an IGMP snooping Layer 2
device are statically configured as router ports, the Layer 2 device will forwards
multicast data from Layer 3 multicast devices to all its router ports. This
wastes bandwidth resources. Perform this task to not forward multicast data to
router ports in a VLAN if they are not interested in receiving the data.

#### Prerequisites

Before performing this task, you must
enable IGMP snooping in the VLAN.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter VLAN view.

vlan vlan-id

**3\.**Prevent the device from forwarding multicast
data to static router ports in the VLAN.

igmp-snooping router-port-discard

By default, the device forwards multicast
data to static router ports in a VLAN.

## Specifying the Layer 2 multicast forwarding mode

### About Layer 2 multicast forwarding modes

The device supports the following Layer 2
multicast forwarding modes:

·     MAC-based mode—The device forwards Layer 2 multicast packets based on the multicast
MAC addresses in the packets.

·     IP-based mode—The device forwards Layer 2 multicast packets based on the IP
addresses (multicast source addresses and multicast group addresses) in the
packets.

To display Layer 2 multicast IP forwarding
entries, use the display l2-multicast ip forwarding command.

To display Layer 2 multicast MAC forwarding
entries, use the display l2-multicast mac forwarding command.

### Restrictions and guidelines for specifying the Layer 2 multicast forwarding mode

Multiple multicast IP addresses might be
mapped to the same multicast MAC address. This causes that non-members of a
multicast group receive the multicast data of this group if the device uses the
MAC-based forwarding mode. As a best practice, specify the IP-based Layer 2
multicast forwarding mode on Layer 3 multicast devices.

As a best practice, specify the IP-based Layer
2 multicast forwarding mode on the device if a device running IGMPv3 exists in
the network.

### Specifying the Layer 2 multicast forwarding mode for a VLAN

**1\.**Enter system view.

system-view

**2\.**Enter VLAN view.

vlan vlan-id

**3\.**Specify the Layer 2 multicast forwarding
mode.

igmp-snooping forwarding-mode { ip \| mac }

By default, the device forwards Layer 2
multicast packets based on the multicast MAC addresses in the packets.

## Configuringthe IGMP snooping querier

### Enabling the IGMP snooping querier

#### About this task

This feature enables the Layer 2 device to periodically
send IGMP general queries to establish and maintain multicast forwarding
entries at the data link Layer. You can configure an IGMP snooping querier on a
network without Layer 3 multicast devices.

#### Restrictions and guidelines

Do not enable the IGMP snooping querier on
a multicast network that runs IGMP. An IGMP snooping querier does not take part
in IGMP querier elections. However, it might affect IGMP querier elections if
it sends IGMP general queries with a low source IP address. For more information
about IGMP querier, see "Configuring IGMP."

On a VXLAN network, the IGMP snooping
querier in a VSI does not include VLAN tags in IGMP general queries. As a best
practice, do not enable the IGMP snooping querier in a VSI if the VSI uses the
Ethernet access mode. For more information about the Ethernet access mode, see VXLAN Configuration Guide.

#### Enabling the IGMP snooping querier for a VLAN

**1\.**Enter system view.

system-view

**2\.**Enter VLAN view.

vlan vlan-id

**3\.**Enable the IGMP snooping querier for the
VLAN.

igmp-snooping querier

By default, the IGMP snooping querier is
disabled for a VLAN.

#### Enabling the IGMP snooping querier for a VSI

**1\.**Enter system view.

system-view

**2\.**Enter VSI view.

vsi vsi-name

**3\.**Enable the IGMP snooping querier for the
VSI.

igmp-snooping querier

By default, the IGMP snooping querier is
disabled for a VSI.

### Enabling IGMP snooping querier election

#### About this task

To avoid traffic interruption caused by the
failure of a single querier in a VLAN or VSI, configure multiple queriers in
the VLAN or VSI and enable querier election. When the elected querier fails,
the device starts a new querier election to ensure multicast forwarding. The mechanism
for IGMP snooping querier election is the same as that for IGMP querier
election. For more information about IGMP querier election, see
"Configuring IGMP."

#### Prerequisites for enabling IGMP snooping querier election

Before you enable IGMP snooping querier
election, you must complete the following tasks:

·     Enable the IGMP snooping querier for a VLAN or
VSI. For more information about enabling the IGMP snooping querier, see "[Enabling the IGMP snooping querier](#_Ref490244641)."

·     Configure the source IP address for IGMP general
queries as an IP address different from 0.0.0.0 and the local querier IP
address. An IGMP snooping querier performs querier election only if the source
IP address of a received IGMP general query is not 0.0.0.0 or its own IP
address.

·     Make sure the candidate IGMP snooping queriers
run the same IGMP snooping version. To specify the IGMP snooping version, use
the igmp-snooping version command.

#### Enabling IGMP snooping querier election for a VLAN

**1\.**Enter system view.

system-view

**2\.**Enter VLAN view.

vlan vlan-id

**3\.**Enable IGMP snooping querier election for
the VLAN.

igmp-snooping querier-election

By default, IGMP snooping querier election
is disabled for a VLAN.

#### Enabling IGMP snooping querier election for a VSI

**1\.**Enter system view.

system-view

**2\.**Enter VSI view.

vsi vsi-name

**3\.**Enable IGMP snooping querier election for
the VSI.

igmp-snooping querier-election

By default, IGMP snooping querier election
is disabled for a VSI.

### Configuring parameters for IGMP general queries and responses

#### About this task

You can modify the IGMP general query
interval based on the actual condition of the network.

A receiver host starts a report delay timer
for each multicast group that it has joined when it receives an IGMP general
query. This timer is set to a random value in the range of 0 to the maximum
response time advertised in the query. When the timer value decreases to 0, the
host sends an IGMP report to the corresponding multicast group.

Set an appropriate value for the maximum
response time for IGMP general queries to speed up hosts' responses to IGMP
general queries and avoid IGMP report traffic bursts.

#### Restrictions and guidelines

To avoid mistakenly deleting multicast
group members, make sure the IGMP general query interval is greater than the
maximum response time for IGMP general queries.

#### Configuring parameters for IGMP general queries and responses globally

**1\.**Enter system view.

system-view

**2\.**Enter IGMP-snooping view.

igmp-snooping

**3\.**Set the maximum response time for IGMP
general queries.

max-response-time seconds

By default, the maximum response time for
IGMP general queries is 10 seconds.

#### Configuring parameters for IGMP general queries and responses in a VLAN

**1\.**Enter system view.

system-view

**2\.**Enter VLAN view.

vlan vlan-id

**3\.**Set the IGMP general query interval in the
VLAN.

igmp-snooping query-interval interval

By default, the IGMP general query
interval is 125 seconds for a VLAN.

**4\.**Set the maximum response time for IGMP
general queries in the VLAN.

igmp-snooping max-response-time seconds

By default, the maximum response time for
IGMP general queries is 10 seconds for a VLAN.

#### Configuring parameters for IGMP general queries and responses in a VSI

**1\.**Enter system view.

system-view

**2\.**Enter VSI view.

vsi vsi-name

**3\.**Set the IGMP general query interval in the
VSI.

igmp-snooping query-interval interval

By default, the IGMP general query
interval is 125 seconds for a VSI.

**4\.**Set the maximum response time for IGMP
general queries in the VSI.

igmp-snooping max-response-time seconds

By default, the maximum response time for
IGMP general queries is 10 seconds for a VSI.

### Enabling the device to send IGMP general queries upon a path change

#### About this task

Perform this task to enable the device to immediately
send an IGMP general query to all ports in the affected VLAN or VSI when a path
changes. This feature ensures that the device can update the member port
information in multicast forwarding entries and switch multicast traffic to the
new path promptly.

#### Restrictions and guidelines

Perform this task on the IGMP snooping
querier in a network of which the topology frequently changes.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter IGMP-snooping view.

igmp-snooping

**3\.**Enable the device to send IGMP general queries
upon a path change.

send-query enable

By default, the device does not send IGMP
general queries upon a path change.

### Enabling the device to send IGMP general queries upon a port state change

#### About this task

As shown in [Figure 4](#_Ref75534796), the
receiver side's devices connect to the Layer 2 multicast device through a Reth
interface for high availability. Interface A and interface B are member
interfaces of the Reth interface, and interface A is active. Multicast data is
sent to the receivers through Device A. When interface A fails, Device C does
not send an IGMP general query immediately and cannot update interface B to multicast
forwarding entries. For information about Reth interfaces, see High Availability Configuration Guide.

Perform this task to enable the device to immediately
send an IGMP general query to all ports in the affected VLAN when the state of
a port changes. This feature ensures that the device can update the member port
information in multicast forwarding entries and switch multicast traffic to the
new link promptly. 

Figure 4 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11704940_x_Img_x_png_3_2216031_294551_0.png)

 

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter VLAN view.

vlan vlan-id

**3\.**Enable the device to send IGMP general queries
upon a port state change.

igmp-snooping triggered-query enable { port-down \| port-up } \*

By default, the device does not send IGMP
general queries upon a port state change.

## Enabling IGMP snooping proxying

### About enabling IGMP snooping proxying

The device enabled with IGMP snooping
proxying is called an IGMP snooping proxy. The IGMP snooping proxy acts as a
host to the upstream device. Enabled with IGMP snooping querier, the IGMP
snooping proxy acts as the router to downstream devices and receives report and
leave messages on behalf of the upstream device. As a best practice, enable
IGMP snooping proxy on the edge device to alleviate the effect caused by
excessive packets.

### Restrictions and guidelines for enabling IGMP snooping proxying

Before you enable IGMP snooping proxying
for a VLAN or VSI, you must first enable IGMP snooping globally and enable IGMP
snooping for the VLAN or VSI. IGMP snooping proxying does not take effect on
sub VLANs of a multicast VLAN.

Use this feature with IGMP snooping querier.
For more information about enabling IGMP snooping querier, see "[Enabling the IGMP snooping querier](#_Ref529812123)."

### Enabling IGMP snooping proxying for a VLAN

**1\.**Enter system view.

system-view

**2\.**Enter VLAN view.

vlan vlan-id

**3\.**Enable IGMP snooping proxying for the VLAN.

igmp-snooping proxy enable

By default, IGMP snooping proxying is
disabled for a VLAN.

### Enabling IGMP snooping proxying for a VSI

**1\.**Enter system view.

system-view

**2\.**Enter VSI view.

vsi vsi-name

**3\.**Enable IGMP snooping proxying for the VSI.

igmp-snooping proxy enable

By default, IGMP snooping proxying is
disabled for a VSI.

## Configuring parameters for IGMP messages

### Configuring source IP addresses for IGMPmessages

#### About this task

The IGMP snooping querier might send IGMP
general queries with the source IP address 0.0.0.0. The port that receives such
queries will not be maintained as a dynamic router port. This might prevent the
associated dynamic IGMP snooping forwarding entry from being correctly created
at the data link layer and eventually cause multicast traffic forwarding failures.
To avoid this problem, you can configure a non-all-zero IP address as the source
IP address of the IGMP queries on the IGMP snooping querier. This configuration
might affect the IGMP querier election within the subnet.

You can also change the source IP address
of IGMP reports or leave messages sent by a simulated member host or an IGMP
snooping proxy.

#### Configuring the source IP addresses for IGMP messages in a VLAN

**1\.**Enter system view.

system-view

**2\.**Enter VLAN view.

vlan vlan-id

**3\.**Configure the source IP address for IGMP
general queries.

igmp-snooping general-query source-ip ip-address

By default, the source IP address of IGMP
general queries is the IP address of the current VLAN interface. If the current
VLAN interface does not have an IP address, the source IP address is 0.0.0.0.

**4\.**Configure the source IP address for IGMP
group-specific queries.

igmp-snooping special-query source-ip ip-address

By default, the source IP address of IGMP
group-specific queries is one of the following:

¡     The
source address of IGMP group-specific queries if the IGMP snooping querier of
the VLAN has received IGMP general queries.

¡     The
IP address of the current VLAN interface if the IGMP snooping querier does not
receive an IGMP general query.

¡     0.0.0.0
if the IGMP snooping querier does not receive an IGMP general query and the
current VLAN interface does not have an IP address.

**5\.**Configure the source IP address for IGMP
reports.

igmp-snooping report source-ip ip-address

By default, the source IP address of IGMP
reports is the IP address of the current VLAN interface. If the current VLAN interface
does not have an IP address, the source IP address is 0.0.0.0.

**6\.**Configure the source IP address for IGMP
leave messages.

igmp-snooping leave source-ip ip-address

By default, the source IP address of IGMP
leave messages is the IP address of the current VLAN interface. If the current
VLAN interface does not have an IP address, the source IP address is 0.0.0.0.

#### Configuring the source IP addresses for IGMP messages in a VSI

**1\.**Enter system view.

system-view

**2\.**Enter VSI view.

vsi vsi-name

**3\.**Configure the source IP address for IGMP
general queries.

igmp-snooping general-query source-ip ip-address

By default, the source IP address of IGMP
general queries is the IP address of the gateway interface for a VSI. If the
gateway interface does not have an IP address, the source IP address is
0.0.0.0.

**4\.**Configure the source IP address for IGMP
group-specific queries.

igmp-snooping special-query source-ip ip-address

By default, the source IP address of IGMP
group-specific queries is one of the following:

¡     The source
IP address of IGMP general queries if the IGMP snooping querier of a VSI has
received IGMP general queries.

¡     The
IP address of the gateway interface for the VSI if the IGMP snooping querier
does not receive an IGMP general query.

¡     0.0.0.0
if the gateway interface of the VSI does not have an IP address.

**5\.**Configure the source IP address for IGMP
reports.

igmp-snooping report source-ip ip-address

By default, the source IP address of IGMP
reports is the IP address of the gateway interface for a VSI. If the gateway
interface does not have an IP address, the source IP address is 0.0.0.0.

**6\.**Configure the source IP address for IGMP
leave messages.

igmp-snooping leave source-ip ip-address

By default, the source IP address of IGMP
leave messages is the IP address of the gateway interface for a VSI. If the
gateway interface does not have an IP address, the source IP address is
0.0.0.0.

### Setting the 802.1p priority for IGMP messages

#### About this task

When congestion occurs on outgoing ports of
the Layer 2 device, it forwards IGMP messages in their 802.1p priority order, from
highest to lowest. You can assign a higher 802.1p priority to IGMP messages that
are created or forwarded by the device.

#### Restrictions and guidelines

The configuration in IGMP-snooping view
takes effect only for all VLANs. For VSIs, you can set the 802.1p priority for
IGMP messages only in VSI view.

#### Setting the 802.1p priority for IGMP messages globally

**1\.**Enter system view.

system-view

**2\.**Enter IGMP-snooping view.

igmp-snooping

**3\.**Set the 802.1p priority for IGMP messages.

dot1p-priority priority

By default, the global 802.1p priority is
6 for IGMP messages.

#### Setting the 802.1p priority for IGMP messages in a VLAN

**1\.**Enter system view.

system-view

**2\.**Enter VLAN view.

vlan vlan-id

**3\.**Set the 802.1p priority for IGMP messages in
the VLAN.

igmp-snooping dot1p-priority priority

By default, the 802.1p priority is 6 for
IGMP messages in a VLAN.

## Configuring IGMP snooping policies

### Configuring a multicast group policy

#### About this task

This feature enables the Layer 2 device to filter
IGMP reports by using an ACL that specifies the multicast groups and the optional
sources. It is used to control the multicast groups that hosts can join. This
configuration takes effect only on the multicast groups that ports join dynamically.

In a multicast application, a host sends an
unsolicited IGMP report when a user requests a multicast program. The Layer 2
device uses the multicast group policy to filter the IGMP report. The host can
join the multicast group only if the IGMP report is permitted by the multicast
group policy. 

#### Configuring a multicast group policy globally

**1\.**Enter system view.

system-view

**2\.**Enter IGMP-snooping view.

igmp-snooping

**3\.**Configure a multicast group policy globally.

group-policy ipv4-acl-number \[ vlan vlan-list ]

By default, no multicast group policies are
configured, and hosts can join any multicast groups.

#### Configuring a multicast group policy on a port

**1\.**Enter system view.

system-view

2\.     Enter Layer 2
interface view.

¡     Enter
Layer 2 Ethernet interface view.

interface interface-type interface-number

¡     Enter
Layer 2 aggregate interface view.

interface bridge-aggregation interface-number

**3\.**Configure a multicast group policy on the
port.

igmp-snooping group-policy ipv4-acl-number \[ vlan vlan-list ]

By default, no multicast group policies are
configured on a port, and hosts attached to the port can join any multicast
groups.

### Enabling multicast source port filtering

#### About this task

This feature enables the Layer 2 device to
discard all multicast data packets and to accept multicast protocol packets. You
can enable this feature on ports that connect only to multicast receivers.

#### Restrictions and guidelines

When IPv4 multicast source port filtering is
enabled, the system automatically enables IPv6 multicast source port filtering.

The configuration made for multiple
interfaces in IGMP-snooping view has the same priority as the
interface-specific configuration, and the most recent configuration takes
effect.

#### Enabling multicast source port filtering in IGMP-snooping view

**1\.**Enter system view.

system-view

**2\.**Enter IGMP-snooping view.

igmp-snooping

**3\.**Enable multicast source port filtering.

source-deny port
interface-list

By default, multicast source port
filtering is disabled.

#### Enabling multicast source port filtering in interface view

**1\.**Enter system view.

system-view

**2\.**Enter Layer 2 Ethernet interface view.

interface interface-type interface-number

**3\.**Enable multicast source port filtering.

igmp-snooping source-deny

By default, multicast source port
filtering is disabled.

### Enabling dropping unknown multicast data

#### About this task

Unknown multicast data refers to multicast
data for which no forwarding entries exist in the IGMP snooping forwarding
table. This feature enables the device to forward unknown multicast data only to
the router port. If the device does not have a router port, unknown multicast
data will be dropped.

If you do not enable this feature, the
unknown multicast data is flooded in the VLAN or VSI to which the data belongs.

#### Restrictions and guidelines

When dropping unknown IPv4 multicast data
is enabled, the device also drops unknown IPv6 multicast data.

When this feature is enabled for a VLAN or
VSI, the device still forwards unknown multicast data out of router ports except
the receiving router port in this VLAN or VSI.

#### Enabling dropping unknown multicast data for a VLAN

**1\.**Enter system view.

system-view

**2\.**Enter VLAN view.

vlan vlan-id

**3\.**Enable dropping unknown multicast data for
the VLAN.

igmp-snooping drop-unknown

By default, dropping unknown multicast
data is disabled for a VLAN. Unknown multicast data is flooded in the VLAN.

#### Enabling dropping unknown multicast data for a VSI

**1\.**Enter system view.

system-view

**2\.**Enter VSI view.

vsi vsi-name

**3\.**Enable dropping unknown multicast data for
the VSI.

igmp-snooping drop-unknown

By default, dropping unknown multicast
data is disabled for a VSI. Unknown multicast data is flooded in the VSI.

### Enabling IGMP report suppression

#### About this task

This feature enables the Layer 2 device to
forward only the first IGMP report for a multicast group to its directly
connected Layer 3 device. Other reports for the same group in the same query
interval are discarded. Use this feature to reduce multicast traffic.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter IGMP-snooping view.

igmp-snooping

**3\.**Enable IGMP report suppression.

report-aggregation

By default, IGMP report suppression is
enabled.

### Setting the maximum number of multicast groups on a port

#### About this task

You can set the maximum number of multicast
groups on a port to regulate the port traffic. This feature takes effect only
on the multicast groups that a port joins dynamically.

If the number of multicast groups on a port
exceeds the limit, the system removes all the forwarding entries associated
with the port. The receiver hosts attached to that port can join multicast
groups again before the number of multicast groups on the port reaches the limit.
When the number of multicast groups on the port reaches the limit, the port
automatically drops IGMP reports for new groups.

#### Procedure

**1\.**Enter system view.

system-view

2\.     Enter Layer 2
interface view.

¡     Enter
Layer 2 Ethernet interface view.

interface interface-type interface-number

¡     Enter
Layer 2 aggregate interface view.

interface bridge-aggregation interface-number

**3\.**Set the maximum number of multicast groups
on the port.

igmp-snooping group-limit limit \[ vlan vlan-list ]

By default, no limit is placed on the
maximum number of multicast groups on a port.

### Enabling multicast group replacement

#### About this task

When multicast group replacement is enabled,
the port does not drop IGMP reports for new groups if the number of multicast
groups on the port reaches the upper limit. Instead, the port leaves the
multicast group that has the lowest IP address and joins the new group
contained in the IGMP report. The multicast group replacement feature is
typically used in the channel switching application.

#### Restrictions and guidelines

This feature takes effect only on the
multicast groups that a port joins dynamically.

This feature does not take effect if the
following conditions exist:

·     The number of the IGMP snooping forwarding
entries on the device reaches the upper limit.

·     The multicast group that the port newly joins is
not included in the multicast group list maintained by the device.

#### Enabling multicast group replacement globally

**1\.**Enter system view.

system-view

**2\.**Enter IGMP-snooping view.

igmp-snooping

**3\.**Enable multicast group replacement globally.

overflow-replace \[ vlan vlan-list ]

By default, multicast group replacement
is disabled globally.

#### Enabling multicast group replacement on a port

**1\.**Enter system view.

system-view

2\.     Enter Layer 2 interface view.

¡     Enter
Layer 2 Ethernet interface view.

interface interface-type interface-number

¡     Enter
Layer 2 aggregate interface view.

interface bridge-aggregation interface-number

**3\.**Enable multicast group replacement on the
port.

igmp-snooping overflow-replace \[ vlan vlan-list ]

By default, multicast group replacement
is disabled on a port.

### Enabling host tracking

#### About this task

This feature enables the Layer 2 device to
record information about member hosts that are receiving multicast data. The
information includes IP addresses of the hosts, length of time elapsed since
the hosts joined multicast groups, and remaining timeout time for the hosts. This
feature facilitates monitoring and managing member hosts.

#### Restrictions and guidelines

The global configuration does not take
effect on VSIs.

#### Enabling host tracking globally

**1\.**Enter system view.

system-view

**2\.**Enter IGMP-snooping view.

igmp-snooping

**3\.**Enable host tracking globally.

host-tracking

By default, host tracking is disabled
globally.

#### Enabling host tracking for a VLAN

**1\.**Enter system view.

system-view

**2\.**Enter VLAN view.

vlan vlan-id

**3\.**Enable host tracking for the VLAN.

igmp-snooping host-tracking

By default, host tracking is disabled for
a VLAN.

## Setting the DSCP value for outgoing IGMP protocol packets

#### About this task

The DSCP value determines the packet transmission
priority. A greater DSCP value represents a higher priority.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter IGMP snooping view.

igmp-snooping

**3\.**Set the DSCP value for outgoing IGMP
protocol packets.

dscp dscp-value

By default, the DSCP value is 48 for
outgoing IGMP protocol packets.

## Display and maintenance commands for IGMP snooping

Executedisplay commands in any view and reset
commands in user view.

 

| Task | Command |
| --- | --- |
| Display IGMP snooping status. | display igmp-snooping \[ global \| vlan vlan-id \| vsi vsi-name ] || Display dynamic IGMP snooping group entries. | display igmp-snooping group \[ group-address \| source-address ] \* \[ vlan vlan-id \| vsi vsi-name ] \[ interface interface-type interface-number \| \[ verbose ] \[ slot slot-number  ] ] || Display host tracking information. | display igmp-snooping host-tracking vlan vlan-id group group-address \[ source source-address ] \[ slot slot-number ] || Display dynamic router port information. | display igmp-snooping router-port \[ vlan vlan-id \| vsi vsi-name ] \[ verbose ] \[ slot slot-number ] || Display static IGMP snooping group entries. | display igmp-snooping static\-group \[ group-address \| source-address ] \* \[ vlan vlan-id ] \[ verbose ] \[ slot slot-number ] || Display static router port information. | display igmp-snooping static-router-port \[ vlan vlan-id ] \[ verbose ] \[ slot slot-number ] || Display statistics for the IGMP messages and PIMv2 hello messages learned by IGMP snooping. | display igmp-snooping statistics || Display Layer 2 multicast fast forwarding entries. | display l2-multicast fast-forwarding cache \[ vlan vlan-id ] \[ source-address \| group-address ] \* \[ slot slot-number ] || Display information about Layer 2 IP multicast groups. | display l2-multicast ip \[ group group-address \| source source-address ] \* \[ vlan vlan-id \| vsi vsi-name ] \[ slot slot-number ] || Display Layer 2 IP multicast group entries. | display l2-multicast ip forwarding \[ group group-address \| source source-address ] \* \[ vlan vlan-id ] \[ slot slot-number ] || Display information about Layer 2 MAC multicast groups. | display l2-multicast mac \[ mac-address ] \[ vlan vlan-id \| vsi vsi-name ] \[ slot slot-number ] || Display Layer 2 MAC multicast group entries. | display l2-multicast mac forwarding \[ mac-address ] \[ vlan vlan-id ] \[ slot slot-number ] || Display static multicast MAC address entries. | display mac-address \[ mac-address \[ vlan vlan-id ] \| \[ multicast ] \[ vlan vlan-id ] \[ count ] ] || Display multicast group information that IGMP snooping learns from EVPN. | display igmp-snooping evpn-group \[ group-address \| source-address ] \* \[vsi vsi-name ] \[ slot slot-number   ] || Clear dynamic IGMP snooping group entries. | reset igmp-snooping group { group-address \[ source-address ] \| all } \[ vlan vlan-id \| vsi vsi-name ] || Clear dynamic router port information. | reset igmp-snooping router-port { all \| vlan vlan-id \| vsi vsi-name } || Clear statistics for IGMP messages and PIMv2 hello messages learned through IGMP snooping. | reset igmp-snooping statistics || Clear Layer 2 multicast fast forwarding entries. | reset l2-multicast fast-forwarding cache \[ vlan vlan-id ] { { source-address \| group-address } \* \| all } \[ slot slot-number ] |


















 

## IGMP snooping configuration examples

### Example: Configuring VLAN-based IGMP snooping group polices and simulated joining

#### Network configuration

As shown in [Figure 5](#_Ref370889522),
Router A runs IGMPv2 and acts as the IGMP querier. Switch A runs IGMPv2
snooping.

Configure a multicast group policy and
simulated joining to meet the following requirements:

·     Host A and Host B receive only the multicast data
addressed to multicast group 224.1.1.1. Multicast data can be forwarded through
Ten-GigabitEthernet 1/0/3 and Ten-GigabitEthernet 1/0/4 of Switch A
uninterruptedly, even though Host A and Host B fail to receive the multicast
data.

·     Switch A will drop unknown multicast data
instead of flooding it in VLAN 100.

Figure 5 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11704941_x_Img_x_png_4_2216031_294551_0.png)

‌

#### Prerequisites

Assign an IP address and subnet mask
to each interface, as shown in [Figure 5](#_Ref370889522).

#### Procedure

**1\.**Configure Router A:

\# Enable IP multicast routing.

\<RouterA\> system-view

\[RouterA] multicast routing

\[RouterA-mrib] quit

\# Enable IGMP on Ten-GigabitEthernet 1/0/1.

\[RouterA] interface ten-gigabitethernet 1/0/1

\[RouterA-Ten-GigabitEthernet1/0/1] igmp enable

\[RouterA-Ten-GigabitEthernet1/0/1] quit

\# Enable PIM-DM on Ten-GigabitEthernet 1/0/2.

\[RouterA] interface ten-gigabitethernet 1/0/2

\[RouterA-Ten-GigabitEthernet1/0/2] pim dm

\[RouterA-Ten-GigabitEthernet1/0/2] quit

**2\.**Configure Switch A:

\# Enable the IGMP snooping feature.

\<SwitchA\> system-view

\[SwitchA] igmp-snooping

\[SwitchA-igmp-snooping] quit

\# Create VLAN 100, and assign Ten-GigabitEthernet 1/0/1 through Ten-GigabitEthernet 1/0/4 to the VLAN.

\[SwitchA] vlan 100

\[SwitchA-vlan100] port ten-gigabitethernet 1/0/1 to ten-gigabitethernet 1/0/4

\# Enable IGMP snooping, and enable
dropping unknown multicast data for VLAN 100\.

\[SwitchA-vlan100]
igmp-snooping enable

\[SwitchA-vlan100] igmp-snooping
drop-unknown

\[SwitchA-vlan100] quit

\# Configure a multicast group policy so
that hosts in VLAN 100 can join only multicast group 224.1.1.1.

\[SwitchA] acl basic 2001

\[SwitchA-acl-ipv4-basic-2001]
rule permit source 224.1.1.1 0

\[SwitchA-acl-ipv4-basic-2001]
quit

\[SwitchA] igmp-snooping

\[SwitchA-igmp-snooping]
group-policy 2001 vlan 100

\[SwitchA-igmp-snooping] quit

\# Configure Ten-GigabitEthernet 1/0/3 and Ten-GigabitEthernet 1/0/4 as simulated member
hosts of multicast group 224.1.1.1.

\[SwitchA] interface ten-gigabitethernet 1/0/3

\[SwitchA-Ten-GigabitEthernet1/0/3] igmp-snooping
host-join 224.1.1.1 vlan 100

\[SwitchA-Ten-GigabitEthernet1/0/3] quit

\[SwitchA] interface ten-gigabitethernet 1/0/4

\[SwitchA-Ten-GigabitEthernet1/0/4] igmp-snooping
host-join 224.1.1.1 vlan 100

\[SwitchA-Ten-GigabitEthernet1/0/4] quit

#### Verifying the configuration

\# Send IGMP reports from Host A and Host B to
join multicast groups 224.1.1.1 and 224.2.2.2. (Details not shown.)

\# Display dynamic IGMP snooping group
entries for VLAN 100 on Switch A.

\[SwitchA] display igmp-snooping group
vlan 100

Total 1 entries.

 

VLAN 100: Total 1 entries.

  (0.0.0.0, 224.1.1.1)

    Host ports (2 in total):

      XGE1/0/3                              (00:03:23)

      XGE1/0/4                              (00:04:10)

The output shows the following information:

·     Host A and Host B have joined multicast group 224.1.1.1
through the member ports Ten-GigabitEthernet 1/0/4 and Ten-GigabitEthernet 1/0/3 on Switch A,
respectively. 

·     Host A and Host B have failed to join multicast
group 224.2.2.2.

### Example: Configuring VLAN-based IGMP snooping static ports

#### Network configuration

As shown in [Figure 6](#_Ref365889583):

·     Router A runs IGMPv2 and acts as the IGMP querier.
Switch A, Switch B, and Switch C run IGMPv2 snooping.

·     Host A and host C are permanent receivers of
multicast group 224.1.1.1. 

Configure static ports to meet the
following requirements:

·     To enhance the reliability of multicast traffic
transmission, configure Ten-GigabitEthernet 1/0/3 and Ten-GigabitEthernet 1/0/5 on Switch C as
static member ports for multicast group 224.1.1.1.

·     Suppose the STP runs on the network. To avoid
data loops, the forwarding path from Switch A to Switch C is blocked. Multicast
data flows to the receivers attached to Switch C only along the path of Switch
A—Switch B—Switch C. When this path is blocked, a minimum of one IGMP query-response
cycle must be completed before multicast data flows to the receivers along the path
of Switch A—Switch C. In this case, the multicast delivery is interrupted
during the process. For more information about the STP, see Layer 2—LAN Switching Configuration Guide.

Configure Ten-GigabitEthernet 1/0/3 on Switch A as a
static router port. Then, multicast data can flow to the receivers nearly
uninterruptedly along the path of Switch A—Switch C when the path of Switch A—Switch
B—Switch C is blocked.

Figure 6 Network
diagram

![](https://resource.h3c.com/en/202407/12/20240712_11704942_x_Img_x_png_5_2216031_294551_0.png)

‌

#### Prerequisites

Assign an IP address and subnet mask to each
interface, as shown in [Figure 6](#_Ref365889583).

#### Procedure

**1\.**Configure Router A:

\# Enable IP multicast routing.

\<RouterA\> system-view

\[RouterA] multicast routing

\[RouterA-mrib] quit

\# Enable IGMP on Ten-GigabitEthernet 1/0/1.

\[RouterA] interface ten-gigabitethernet 1/0/1

\[RouterA-Ten-GigabitEthernet1/0/1] igmp enable

\[RouterA-Ten-GigabitEthernet1/0/1] quit

\# Enable PIM-DM on Ten-GigabitEthernet 1/0/2.

\[RouterA] interface ten-gigabitethernet 1/0/2

\[RouterA-Ten-GigabitEthernet1/0/2] pim dm

\[RouterA-Ten-GigabitEthernet1/0/2] quit

**2\.**Configure Switch A:

\# Enable the IGMP snooping feature.

\<SwitchA\> system-view

\[SwitchA] igmp-snooping

\[SwitchA-igmp-snooping] quit

\# Create VLAN 100, and assign Ten-GigabitEthernet 1/0/1 through Ten-GigabitEthernet 1/0/3 to the VLAN.

\[SwitchA] vlan 100

\[SwitchA-vlan100] port ten-gigabitethernet 1/0/1 to ten-gigabitethernet 1/0/3

\# Enable IGMP snooping for VLAN 100\.

\[SwitchA-vlan100]
igmp-snooping enable

\[SwitchA-vlan100] quit

\# Configure Ten-GigabitEthernet 1/0/3 as a static router
port.

\[SwitchA] interface ten-gigabitethernet 1/0/3

\[SwitchA-Ten-GigabitEthernet1/0/3] igmp-snooping static-router-port
vlan 100

\[SwitchA-Ten-GigabitEthernet1/0/3] quit

**3\.**Configure Switch B:

\# Enable the IGMP snooping feature.

\<SwitchB\> system-view

\[SwitchB] igmp-snooping

\[SwitchB-igmp-snooping] quit

\# Create VLAN 100, and assign Ten-GigabitEthernet 1/0/1 and Ten-GigabitEthernet 1/0/2 to the VLAN.

\[SwitchB] vlan 100

\[SwitchB-vlan100] port ten-gigabitethernet 1/0/1 ten-gigabitethernet 1/0/2

\# Enable IGMP snooping for VLAN 100\.

\[SwitchB-vlan100]
igmp-snooping enable

\[SwitchB-vlan100] quit

**4\.**Configure Switch C:

\# Enable the IGMP snooping feature.

\<SwitchC\> system-view

\[SwitchC] igmp-snooping

\[SwitchC-igmp-snooping] quit

\# Create VLAN 100, and assign Ten-GigabitEthernet 1/0/1 through Ten-GigabitEthernet 1/0/5 to the VLAN.

\[SwitchC] vlan 100

\[SwitchC-vlan100] port ten-gigabitethernet 1/0/1 to ten-gigabitethernet 1/0/5

\# Enable IGMP snooping for VLAN 100\.

\[SwitchC-vlan100]
igmp-snooping enable

\[SwitchC-vlan100] quit

\# Configure Ten-GigabitEthernet 1/0/3 and Ten-GigabitEthernet 1/0/5 as static member
ports for multicast group 224.1.1.1.

\[SwitchC] interface ten-gigabitethernet 1/0/3

\[SwitchC-Ten-GigabitEthernet1/0/3] igmp-snooping
static-group 224.1.1.1 vlan 100

\[SwitchC-Ten-GigabitEthernet1/0/3] quit

\[SwitchC] interface ten-gigabitethernet 1/0/5

\[SwitchC-Ten-GigabitEthernet1/0/5] igmp-snooping static-group
224.1.1.1 vlan 100

\[SwitchC-Ten-GigabitEthernet1/0/5] quit

#### Verifying the configuration

\# Display static router port information for
VLAN 100 on Switch A.

\[SwitchA] display igmp-snooping static-router-port
vlan 100

VLAN 100:

  Router ports (1 in total):

    XGE1/0/3

The output shows that Ten-GigabitEthernet 1/0/3 on Switch A has
become a static router port.

\# Display static IGMP snooping group
entries for VLAN 100 on Switch C.

\[SwitchC] display igmp-snooping static-group
vlan 100

Total 1 entries.

 

VLAN 100: Total 1 entries.

  (0.0.0.0, 224.1.1.1)

    Host ports (2 in total):

      XGE1/0/3

      XGE1/0/5

The output shows that Ten-GigabitEthernet 1/0/3 and Ten-GigabitEthernet 1/0/5 on Switch C have
become static member ports of multicast group 224.1.1.1.

### Example: Configuring the VLAN-based IGMP snooping querier

#### Network configuration

As shown in [Figure 7](#_Ref365446388):

·     The network is a Layer 2-only network.

·     Source 1 and Source 2 send multicast data to
multicast groups 224.1.1.1 and 225.1.1.1, respectively.

·     Host A and Host C are receivers of multicast
group 224.1.1.1, and Host B and Host D are receivers of multicast group
225.1.1.1.

·     All host receivers run IGMPv2, and all switches
run IGMPv2 snooping. Switch A (which is close to the multicast sources) acts as
the IGMP snooping querier.

Configure the switches to meet the
following requirements:

·     To prevent the switches from flooding unknown data
in the VLAN, enable all the switches to drop unknown multicast data.

·     A switch does not mark a port that receives an
IGMP query with source IP address 0.0.0.0 as a dynamic router port. This adversely
affects the establishment of Layer 2 forwarding entries and multicast traffic
forwarding. To avoid this, configure the source IP address of IGMP queries as a
non-zero IP address.

Figure 7 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11704943_x_Img_x_png_6_2216031_294551_0.png)

‌

#### Procedure

**1\.**Configure Switch A:

\# Enable the IGMP snooping feature.

\<SwitchA\> system-view

\[SwitchA] igmp-snooping

\[SwitchA-igmp-snooping] quit

\# Create VLAN 100, and assign Ten-GigabitEthernet 1/0/1 through Ten-GigabitEthernet 1/0/3 to the VLAN.

\[SwitchA] vlan 100

\[SwitchA-vlan100] port ten-gigabitethernet 1/0/1 to ten-gigabitethernet 1/0/3

\# Enable IGMP snooping, and enable dropping
unknown multicast data for VLAN 100\.

\[SwitchA-vlan100]
igmp-snooping enable

\[SwitchA-vlan100]
igmp-snooping drop-unknown

\# Configure Switch A as the IGMP snooping
querier.

\[SwitchA-vlan100]
igmp-snooping querier

\[SwitchA-vlan100] quit

\# In VLAN 100, specify 192.168.1.1 as the
source IP address of IGMP general queries.

\[SwitchA-vlan100]
igmp-snooping general-query source-ip 192.168.1.1

\# In VLAN 100, specify 192.168.1.1 as the
source IP address of IGMP group-specific queries.

\[SwitchA-vlan100]
igmp-snooping special-query source-ip 192.168.1.1

\[SwitchA-vlan100] quit

**2\.**Configure Switch B:

\# Enable the IGMP snooping feature.

\<SwitchB\> system-view

\[SwitchB] igmp-snooping

\[SwitchB-igmp-snooping] quit

\# Create VLAN 100, and assign Ten-GigabitEthernet 1/0/1 through Ten-GigabitEthernet 1/0/4 to the VLAN.

\[SwitchB] vlan 100

\[SwitchB-vlan100] port ten-gigabitethernet 1/0/1 to ten-gigabitethernet 1/0/4

\# Enable IGMP snooping, and enable dropping
unknown multicast data for VLAN 100\.

\[SwitchB-vlan100] igmp-snooping
enable

\[SwitchB-vlan100]
igmp-snooping drop-unknown

\[SwitchB-vlan100] quit

**3\.**Configure Switch C:

\# Enable the IGMP snooping feature.

\<SwitchC\> system-view

\[SwitchC] igmp-snooping

\[SwitchC-igmp-snooping] quit

\# Create VLAN 100, and assign Ten-GigabitEthernet 1/0/1 through Ten-GigabitEthernet 1/0/3 to the VLAN.

\[SwitchC] vlan 100

\[SwitchC-vlan100] port ten-gigabitethernet 1/0/1 to ten-gigabitethernet 1/0/3

\# Enable IGMP snooping, and enable dropping
unknown multicast data for VLAN 100\.

\[SwitchC-vlan100]
igmp-snooping enable

\[SwitchC-vlan100] igmp-snooping
drop-unknown

\[SwitchC-vlan100] quit

**4\.**Configure Switch D:

\# Enable the IGMP snooping feature.

\<SwitchD\> system-view

\[SwitchD] igmp-snooping

\[SwitchD-igmp-snooping] quit

\# Create VLAN 100, and assign Ten-GigabitEthernet 1/0/1 and Ten-GigabitEthernet 1/0/2 to the VLAN.

\[SwitchD] vlan 100

\[SwitchD-vlan100] port ten-gigabitethernet 1/0/1 to ten-gigabitethernet 1/0/2

\# Enable IGMP snooping, and enable dropping
unknown multicast data for VLAN 100\.

\[SwitchD-vlan100]
igmp-snooping enable

\[SwitchD-vlan100]
igmp-snooping drop-unknown

\[SwitchD-vlan100] quit

#### Verifying the configuration

\# Display statistics for IGMP messages and
PIMv2 hello messages learned through IGMP snooping on Switch B.

\[SwitchB] display igmp-snooping
statistics

Received IGMP messages:

  IGMP general queries: 19

  IGMPv2 specific queries: 10 

  IGMPv3 specific queries: 0

  IGMPv3 specific sg queries: 0

  IGMPv1 reports: 0

  IGMPv2 reports: 19

  IGMPv3 reports: 0

  IGMPv3 reports with right and wrong
records: 0

  IGMP leaves: 5

  Error IGMP messages: 1

Sent IGMP messages:

  IGMPv2 specific queries: 0

  IGMPv3 specific queries: 0

  IGMPv3 specific sg queries: 0

Received PIMv2 hello : 0

The output shows that all switches except Switch
A can receive the IGMP general queries after Switch A acts as the IGMP snooping
querier. 

### Example: Configuring VLAN-based IGMP snooping proxying

#### Network configuration

As shown in [Figure 8](#_Ref445123365), Router A runs IGMPv2
and acts as the IGMP querier. Switch A runs IGMPv2 snooping. Configure IGMP
snooping proxying so that Switch A can perform the following actions:

·     Forward IGMP report and leave messages to Router
A.

·     Respond to IGMP queries sent by Router A and
forward the queries to downstream hosts.

Figure 8 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11704944_x_Img_x_png_7_2216031_294551_0.png) 

‌

#### Prerequisites

Assign an IP address and subnet mask to
each interface, as shown in [Figure 8](#_Ref445123365). 

#### Procedure

**1\.**Configure Router A:

\# Enable IP multicast routing.

\<RouterA\> system-view

\[RouterA] multicast routing

\[RouterA-mrib] quit

\# Enable IGMP and PIM-DM on Ten-GigabitEthernet 1/0/1.

\[RouterA] interface ten-gigabitethernet 1/0/1

\[RouterA-Ten-GigabitEthernet1/0/1] igmp enable

\[RouterA-Ten-GigabitEthernet1/0/1] pim dm

\[RouterA-Ten-GigabitEthernet1/0/1] quit

\# Enable PIM-DM on Ten-GigabitEthernet 1/0/2.

\[RouterA] interface ten-gigabitethernet 1/0/2

\[RouterA-Ten-GigabitEthernet1/0/2] pim dm

\[RouterA-Ten-GigabitEthernet1/0/2] quit

**2\.**Configure Switch A:

\# Enable the IGMP snooping feature.

\<SwitchA\> system-view

\[SwitchA] igmp-snooping

\[SwitchA-igmp-snooping] quit

\# Create VLAN 100, and assign Ten-GigabitEthernet 1/0/1 through Ten-GigabitEthernet 1/0/4 to the VLAN.

\[SwitchA] vlan 100

\[SwitchA-vlan100] port ten-gigabitethernet 1/0/1 to ten-gigabitethernet 1/0/4

\# Enable IGMP snooping and IGMP snooping
proxying for the VLAN.

\[SwitchA-vlan100]
igmp-snooping enable

\[SwitchA-vlan100]
igmp-snooping proxy enable

\[SwitchA-vlan100] quit

#### Verifying the configuration

\# Send IGMP reports from Host A and Host B
to join multicast groups 224.1.1.1 and 224.1.1.1. (Details not shown.)

\# Display brief information about IGMP
snooping group entries on Switch A.

\[SwitchA] display igmp-snooping group

 Total 1 entries.

 

VLAN 100: Total 1 entries.

  (0.0.0.0, 224.1.1.1)

    Host ports (2 in total):

      XGE1/0/3                                     (00:04:00)

      XGE1/0/4                                    
(00:04:04)

The output shows that Ten-GigabitEthernet 1/0/3 and Ten-GigabitEthernet 1/0/4 are member ports of
multicast group 224.1.1.1. Host A and Host B become receivers of the group.

\# Display IGMP group membership information
on Router A.

\[RouterA] display igmp group

IGMP groups in total: 1

 Ten-GigabitEthernet1/0/1(10.1.1.1):

  IGMP groups reported in total: 1

   Group address   Last reporter  
Uptime      Expires

   224.1.1.1       0.0.0.0         00:00:31   
00:02:03

\# Send an IGMP leave message from Host A to
leave multicast group 224.1.1.1. (Details not shown.)

\# Display brief information about IGMP
snooping group entries on Switch A.

\[SwitchA] display igmp-snooping group

Total 1 entries.

 

VLAN 100: Total 1 entries.

  (0.0.0.0, 224.1.1.1)

    Host ports (1 in total):

      XGE1/0/3                                    
( 00:01:23 )

The output shows that Ten-GigabitEthernet 1/0/3 is the only member
port of multicast group 224.1.1.1. Only Host B remains as the receiver of the
group.

### Example: Configuring VSI-based IGMP snooping

#### Network configuration

As shown in [Figure 9](#_Ref385527372):

·     There are three sites in VPN a. They are connected through the VPLS network.

·     In VLAN 100, Source in Site 1, Host A in Site 2,
and Host B in Site 3 communicate with CE 1, CE 2, and CE 3, respectively.

Configure the devices so that Host A and
Host B can receive multicast data from Source.

Figure 9 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11704945_x_Img_x_png_8_2216031_294551_0.png)

‌

Table 1 Interface and IP address assignment

| Device | Interface | IP address | Device | Interface | IP address |
| --- | --- | --- | --- | --- | --- |
| PE 1 | Vlan-int101 | 10.110.1.1/24 | PE 2 | Loop0 | 2.2.2.2/32 || PE 1 | Vlan-int102 | 10.110.2.1/24 | PE 3 | Loop0 | 10.110.2.2/24 || PE 1 | Loop0 | 1.1.1.1/32 | PE 3 | Loop0 | 10.110.3.2/24 || PE 2 | Vlan-int101 | 10.110.1.2/24 | PE 3 | Loop0 | 3.3.3.3/32 || PE 2 | Vlan-int103 | 10.110.3.1/24 |  |  |  |





 

#### Prerequisites

On the VPLS network, assign an IP address
and subnet mask to each interface as shown in [Table 1](#_Ref385524623), and configure OSPF on the
switches. 

#### Procedure

**1\.**Configure CE 1:

\# Enable the IGMP snooping feature.

\<CE1\> system-view

\[CE1] igmp-snooping

\[CE1-igmp-snooping] quit

\# Create VLAN 100, and assign Ten-GigabitEthernet 1/0/2 to VLAN 100\.

\[CE1] vlan 100

\[CE1-vlan100] port ten-gigabitethernet 1/0/2

\# In VLAN 100, enable IGMP snooping, and
enable dropping unknown multicast data.

\[CE1-vlan100] igmp-snooping
enable

\[CE1-vlan100] igmp-snooping
drop-unknown

\# Configure Ten-GigabitEthernet 1/0/1 as a trunk port,
and assign it to VLAN 100\.

\[CE1] interface ten-gigabitethernet 1/0/1

\[CE1-Ten-GigabitEthernet1/0/1] port link-type
trunk

\[CE1-Ten-GigabitEthernet1/0/1] port trunk permit
vlan 100

\[CE1-Ten-GigabitEthernet1/0/1] quit

\# In VLAN 100, enable the IGMP snooping querier.

\[CE1-vlan100] igmp-snooping
querier

\# In VLAN 100, specify 192.168.1.100 as
the source IP address of IGMP general queries and IGMP group-specific queries.

\[CE1-vlan100] igmp-snooping
general-query source-ip 192.168.1.100

\[CE1-vlan100] igmp-snooping
special-query source-ip 192.168.1.100

**2\.**Configure CE 2:

\# Enable the IGMP snooping feature.

\<CE2\> system-view

\[CE2] igmp-snooping

\[CE2-igmp-snooping] quit

\# Create VLAN 100, and assign Ten-GigabitEthernet 1/0/2 to VLAN100.

\[CE2] vlan 100

\[CE2-vlan100] port ten-gigabitethernet 1/0/2

\# In VLAN 100, enable IGMP snooping, and enable
dropping unknown multicast data.

\[CE2-vlan100] igmp-snooping
enable

\[CE2-vlan100] igmp-snooping
drop-unknown

\# Configure Ten-GigabitEthernet 1/0/1 as a trunk port,
and assign it to VLAN 100\.

\[CE2] interface ten-gigabitethernet 1/0/1

\[CE2-Ten-GigabitEthernet1/0/1] port link-type
trunk

\[CE2-Ten-GigabitEthernet1/0/1] port trunk permit
vlan 100

\[CE2-Ten-GigabitEthernet1/0/1] quit

**3\.**Configure CE 3:

\# Enable the IGMP snooping feature.

\<CE3\> system-view

\[CE3] igmp-snooping

\[CE3-igmp-snooping] quit

\# Create VLAN 100, and assign Ten-GigabitEthernet 1/0/2 to VLAN100.

\[CE3] vlan 100

\[CE3-vlan100] port ten-gigabitethernet 1/0/2

\# In VLAN 100, enable IGMP snooping, and
dropping unknown multicast data.

\[CE3-vlan100] igmp-snooping
enable

\[CE3-vlan100] igmp-snooping
drop-unknown

\# Configure Ten-GigabitEthernet 1/0/1 as a trunk port,
and assign it to VLAN 100\.

\[CE3] interface ten-gigabitethernet 1/0/1

\[CE3-Ten-GigabitEthernet1/0/1] port link-type
trunk

\[CE3-Ten-GigabitEthernet1/0/1] port trunk permit
vlan 100

\[CE3-Ten-GigabitEthernet1/0/1] quit

**4\.**Configure PE 1:

\# Configure the LSR ID as 1.1.1.1 for the
local node, and enable L2VPN and LDP.

\<PE1\> system-view

\[PE1] mpls lsr-id 1.1.1.1

\[PE1] l2vpn enable

\[PE1] mpls ldp

\[PE1-ldp] quit

\# Enable MPLS and LDP on VLAN-interface 101\.

\[PE1] interface vlan-interface
101

\[PE1-Vlan-interface101] mpls
enable

\[PE1-Vlan-interface101] mpls
ldp enable

\[PE1-Vlan-interface101] quit

\# Enable MPLS and LDP on VLAN-interface
102\.

\[PE1] interface vlan-interface
102

\[PE1-Vlan-interface102] mpls
enable

\[PE1-Vlan-interface102] mpls
ldp enable

\[PE1-Vlan-interface102] quit

\# Create a VSI named aaa, and specify
the VSI to establish PWs statically.

\[PE1] vsi aaa

\[PE1-vsi-aaa] pwsignaling
static

\# Configure PWs for VSI aaa. 

\[PE1-vsi-aaa-static] peer
2.2.2.2 pw-id 3 in-label 100 out-label 100

\[PE1-vsi-aaa-static-2.2.2.2-3]
quit

\[PE1-vsi-aaa-static] peer
3.3.3.3 pw-id 3 in-label 200 out-label 200

\[PE1-vsi-aaa-static-3.3.3.3-3]
quit

\[PE1-vsi-aaa-static] quit

\[PE1-vsi-aaa] quit

\# Bind Ten-GigabitEthernet 1/0/1 to VSI aaa.

\[PE1] interface ten-gigabitethernet 1/0/1

\[PE1-Ten-GigabitEthernet1/0/1] service-instance 1

\[PE1-Ten-GigabitEthernet1/0/1-srv1] encapsulation
s-vid 5

\[PE1-Ten-GigabitEthernet1/0/1-srv1] xconnect vsi
aaa

\[PE1-Ten-GigabitEthernet1/0/1-srv1] quit

\[PE1-Ten-GigabitEthernet1/0/1] quit

\# Enable the IGMP snooping feature.

\[PE1] igmp-snooping

\[PE1-igmp-snooping] quit

\# Enable IGMP snooping, and enable
dropping unknown multicast data for VSI aaa.

\[PE1] vsi aaa

\[PE1-vsi-aaa] igmp-snooping
enable

\[PE1-vsi-aaa] igmp-snooping
drop-unknown

\[PE1-vsi-aaa] quit

**5\.**Configure PE 2:

\# Configure the LSR ID as 2.2.2.2 for the
local node, and enable L2VPN and LDP.

\<PE2\> system-view

\[PE2] mpls lsr-id 2.2.2.2

\[PE2] l2vpn enable

\[PE2] mpls ldp

\[PE2-ldp] quit

\# Enable MPLS and LDP on VLAN-interface 101\.

\[PE2] interface vlan-interface
101

\[PE2-Vlan-interface101] mpls
enable

\[PE2-Vlan-interface101] mpls
ldp enable

\[PE2-Vlan-interface101] quit

\# Enable MPLS and LDP on VLAN-interface
103\.

\[PE2] interface vlan-interface
103

\[PE2-Vlan-interface103] mpls
enable

\[PE2-Vlan-interface103] mpls
ldp enable

\[PE2-Vlan-interface103] quit

\# Create a VSI named aaa, and specify
the VSI to establish PWs statically.

\[PE2] vsi aaa

\[PE2-vsi-aaa] pwsignaling
static

\# Configure PWs for VSI aaa.

\[PE2-vsi-aaa-static] peer 1.1.1.1
pw-id 3 in-label 100 out-label 100

\[PE2-vsi-aaa-static-1.1.1.1-3]
quit

\[PE2-vsi-aaa-static] peer
3.3.3.3 pw-id 3 in-label 300 out-label 300

\[PE2-vsi-aaa-static-3.3.3.3-3]
quit

\[PE2-vsi-aaa-static] quit

\[PE2-vsi-aaa] quit

\# Bind Ten-GigabitEthernet 1/0/1 to VSI aaa.

\[PE2] interface ten-gigabitethernet 1/0/1

\[PE2-Ten-GigabitEthernet1/0/1] service-instance 1

\[PE2-Ten-GigabitEthernet1/0/1-srv1] encapsulation
s-vid 5

\[PE2-Ten-GigabitEthernet1/0/1-srv1] xconnect vsi
aaa

\[PE2-Ten-GigabitEthernet1/0/1-srv1] quit

\[PE2-Ten-GigabitEthernet1/0/1] quit

\# Enable the IGMP snooping feature.

\[PE2] igmp-snooping

\[PE2-igmp-snooping] quit

\# In VSI aaa, enable IGMP snooping, and enable
dropping unknown multicast data.

\[PE2] vsi aaa

\[PE2-vsi-aaa] igmp-snooping
enable

\[PE2-vsi-aaa] igmp-snooping
drop-unknown

\[PE2-vsi-aaa] quit

**6\.**Configure PE 3:

\# Configure the LSR ID as 3.3.3.3 for the
local node, and enable L2VPN and LDP.

\<PE3\> system-view

\[PE3] mpls lsr-id 3.3.3.3

\[PE3] l2vpn enable

\[PE3] mpls ldp

\[PE3-ldp] quit

\# Enable MPLS and LDP on VLAN-interface 102\.

\[PE3] interface vlan-interface
102

\[PE3-Vlan-interface102] mpls
enable

\[PE3-Vlan-interface102] mpls
ldp enable

\[PE3-Vlan-interface102] quit

\# Enable MPLS and LDP on VLAN-interface
103\.

\[PE3] interface vlan-interface
103

\[PE3-Vlan-interface103] mpls
enable

\[PE3-Vlan-interface103] mpls
ldp enable

\[PE3-Vlan-interface103] quit

\# Create a VSI named aaa, and specify
the VSI to establish PWs statically.

\[PE3] vsi aaa

\[PE3-vsi-aaa] pwsignaling
static

\# Configure PWs for VSI aaa.

\[PE3-vsi-aaa-static] peer 1.1.1.1
pw-id 3 in-label 200 out-label 200

\[PE3-vsi-aaa-static-1.1.1.1-3]
quit

\[PE3-vsi-aaa-static] peer 2.2.2.2
pw-id 3 in-label 300 out-label 300

\[PE3-vsi-aaa-static-2.2.2.2-3]
quit

\[PE3-vsi-aaa-static] quit

\[PE3-vsi-aaa] quit

\# Bind Ten-GigabitEthernet 1/0/1 to VSI aaa.

\[PE3] interface ten-gigabitethernet 1/0/1

\[PE3-Ten-GigabitEthernet1/0/1] service-instance 1

\[PE3-Ten-GigabitEthernet1/0/1-srv1] encapsulation
s-vid 5

\[PE3-Ten-GigabitEthernet1/0/1-srv1] xconnect vsi
aaa

\[PE3-Ten-GigabitEthernet1/0/1-srv1] quit

\[PE3-Ten-GigabitEthernet1/0/1] quit

\# Enable the IGMP snooping feature.

\[PE3] igmp-snooping

\[PE3-igmp-snooping] quit

\# In VSI aaa, enable IGMP snooping, and enable
dropping unknown multicast data.

\[PE3] vsi aaa

\[PE3-vsi-aaa] igmp-snooping
enable

\[PE3-vsi-aaa] igmp-snooping
drop-unknown

\[PE3-vsi-aaa] quit

#### Verifying the configuration

\# Display detailed information about
dynamic IGMP snooping group entries for VSI aaa on
PE 1\. 

\[PE1] display igmp-snooping group vsi
aaa verbose

Total 1 entries.

 

VSI aaa: Total 1 entries.

  (0.0.0.0, 225.0.0.1)

    Attribute: global port

    FSM information: dummy

    Host slots (0 in total):

    Host ports (1 in total):

      NPW (Link ID 9\)                         (00:02:24)

        VLAN pairs (1 in total):

          Outer VLAN 100     Inner
VLAN 0     (00:02:24)

\# Display detailed information about
dynamic router ports for VSI aaa on PE 1\.

\[PE1] display igmp-snooping
router-port vsi aaa verbose

VSI aaa:

  Router slots (0 in total):

  Router ports (1 in total):

    XGE1/0/1 (Link ID 0\)                       (00:01:46)

      VLAN pairs (1 in total):

        Outer VLAN 100     Inner VLAN
0       (00:01:46)

\# Display detailed information about
dynamic IGMP snooping group entries for VSI aaa on PE
2\.

\[PE2] display igmp-snooping group vsi
aaa verbose

Total 1 entries.

 

VSI aaa: Total 1 entries.

  (0.0.0.0, 225.0.0.1)

    Attribute: global port

    FSM information: dummy

    Host slots (0 in total):

    Host ports (1 in total):

      NPW (Link ID 8\)                         (00:02:07)

        VLAN pairs (1 in total):

          Outer VLAN 100     Inner
VLAN 0     (00:02:07)

\# Display detailed information about
dynamic router ports for VSI aaa on PE 2\.

\[PE2] display igmp-snooping
router-port vsi aaa verbose

VSI aaa:

  Router slots (0 in total):

  Router ports (1 in total):

    NPW (Link ID 9\)                           (00:01:26)

      VLAN pairs (1 in total):

        Outer VLAN 100     Inner VLAN
0       (00:01:26)

\# Display detailed information about
dynamic IGMP snooping group entries for VSI aaa on PE
3\.

\[PE3]display
igmp-snooping group vsi aaa verbose

Total 1 entries.

 

VSI aaa: Total 1 entries.

  (0.0.0.0, 225.0.0.1)

    Attribute: global port

    FSM information: dummy

    Host slots (0 in total):

    Host ports (1 in total):

      XGE1/0/1 (Link ID 0\)                   (00:02:04)

        VLAN pairs (1 in total):

          Outer VLAN 100     Inner
VLAN 0     (00:02:04)

\# Display detailed information about
dynamic router ports for VSI aaa on PE 3\.

\[PE3] display igmp-snooping
router-port vsi aaa verbose

VSI aaa:

  Router slots (0 in total):

  Router ports (1 in total):

    NPW (Link ID 9\)                           (00:01:24)

      VLAN pairs (1 in total):

        Outer VLAN 100     Inner VLAN
0       (00:01:24)

### Example: Configuring VXLAN-based IGMP snooping

#### Network configuration

As shown in [Figure 10](#_Ref435176429):

·     VXLAN 10 provides Layer 2 connectivity for VM 1,
VM 2, and VM 3 across the network sites.

·     VXLAN 10 uses the unicast mode for flood
traffic.

Configure IGMP snooping on the switches to
implement Layer 2 multicast forwarding and reduce the burden of replicating
known multicast traffic for VTEPs in VXLAN 10\.

Figure 10 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11704946_x_Img_x_png_9_2216031_294551_0.png)

‌

Table 2 Interface and IP address assignment

| Device | Interface | IP address | Device | Interface | IP address |
| --- | --- | --- | --- | --- | --- |
| Switch A | Vlan-int11 | 11.1.1.1/24 | Switch C | Vlan-int13 | 13.1.1.3/24 || Switch A | Loop0 | 1.1.1.1/32 | Switch C | Loop0 | 3.3.3.3/32 || Switch B | Vlan-int12 | 12.1.1.2/24 | Switch D | Vlan-int11 | 11.1.1.4/24 || Switch B | Loop0 | 2.2.2.2/32 | Switch D | Vlan-int12 | 12.1.1.4/24 ||  |  |  | Switch D | Vlan-int13 | 13.1.1.4/24 |





 

#### Prerequisites

On the transport network, assign an IP
address and subnet mask to each interface as shown in [Table 2](#_Ref435170465), and configure
OSPF on all the switches.

#### Procedure

**1\.**Configure Switch A:

\# Enable L2VPN.

\<SwitchA\> system-view

\[SwitchA] l2vpn enable

\# Enable Layer 2 forwarding for VXLANs.

\[SwitchA] undo vxlan
ip-forwarding

\# Create a VSI named vpna, and create
VXLAN 10\.

\[SwitchA] vsi vpna

\[SwitchA-vsi-vpna] vxlan 10

\[SwitchA-vsi-vpna-vxlan-10]
quit

\[SwitchA-vsi-vpna] quit

\# Enable the IGMP snooping feature.

\[SwitchA] igmp-snooping

\[SwitchA -igmp-snooping] quit

\# Enable IGMP snooping and dropping
unknown multicast data packets for VSI vpna.

\[SwitchA] vsi vpna

\[SwitchA-vsi-vpna]
igmp-snooping enable

\[SwitchA-vsi-vpna]
igmp-snooping drop-unknown

\[SwitchA-vsi-vpna] quit

\# Assign an IP address to Loopback 0\.
This IP address will be used as the source address of the VXLAN tunnels to
Switch B and Switch C.

\[SwitchA] interface loopback 0

\[SwitchA-Loopback0] ip address
1.1.1.1 255.255.255.255

\[SwitchA-Loopback0] quit

\# Create a VXLAN tunnel to Switch B.

¡     The
tunnel interface name is Tunnel 1.

¡     The
source address and destination address of the tunnel interface are 1.1.1.1 and
2.2.2.2, respectively.

\[SwitchA] interface tunnel 1
mode vxlan

\[SwitchA-Tunnel1] source
1.1.1.1

\[SwitchA-Tunnel1] destination
2.2.2.2

\[SwitchA-Tunnel1] quit

\# Create a VXLAN tunnel to Switch C.

¡     The
tunnel interface name is Tunnel 2.

¡     The
source address and destination address of the tunnel interface are 1.1.1.1 and
3.3.3.3, respectively.

\[SwitchA] interface tunnel 2
mode vxlan

\[SwitchA-Tunnel2] source
1.1.1.1

\[SwitchA-Tunnel2] destination
3.3.3.3

\[SwitchA-Tunnel2] quit

\# Assign Tunnel 1 and Tunnel 2 to VXLAN
10\.

\[SwitchA] vsi vpna

\[SwitchA-vsi-vpna] vxlan 10

\[SwitchA-vsi-vpna-vxlan-10]
tunnel 1

\[SwitchA-vsi-vpna-vxlan-10]
tunnel 2

\[SwitchA-vsi-vpna-vxlan-10]
quit

\[SwitchA-vsi-vpna] quit

\# On Ten-GigabitEthernet 1/0/1, create Ethernet
service instance 1000 to match VLAN 2\.

\[SwitchA] interface ten-gigabitethernet 1/0/1

\[SwitchA-Ten-GigabitEthernet1/0/1] service-instance
1000

\[SwitchA-Ten-GigabitEthernet1/0/1-srv1000]
encapsulation s-vid 2

\# Map Ethernet service instance 1000 to
VSI vpna.

\[SwitchA-Ten-GigabitEthernet1/0/1-srv1000] xconnect
vsi vpna

\[SwitchA-Ten-GigabitEthernet1/0/1-srv1000] quit

\[SwitchA-Ten-GigabitEthernet1/0/1] quit

**2\.**Configure Switch B:

\# Enable L2VPN.

\<SwitchB\> system-view

\[SwitchB] l2vpn enable

\# Enable Layer 2 forwarding for VXLANs.

\[SwitchB] undo vxlan
ip-forwarding

\# Create a VSI named vpna, and create
VXLAN 10\.

\[SwitchB] vsi vpna

\[SwitchB-vsi-vpna] vxlan 10

\[SwitchB-vsi-vpna-vxlan-10]
quit

\[SwitchB-vsi-vpna] quit

\# Enable the IGMP snooping feature.

\[SwitchB] igmp-snooping

\[SwitchB -igmp-snooping] quit

\# Enable IGMP snooping and dropping
unknown multicast data packets for VSI vpna.

\[SwitchB] vsi vpna

\[SwitchB-vsi-vpna]
igmp-snooping enable

\[SwitchB-vsi-vpna]
igmp-snooping drop-unknown

\[SwitchB-vsi-vpna] quit

\# Assign an IP address to Loopback 0\.
This IP address will be used as the source address of the VXLAN tunnels to
Switch A and Switch C.

\[SwitchB] interface loopback 0

\[SwitchB-Loopback0] ip address
2.2.2.2 255.255.255.255

\[SwitchB-Loopback0] quit

\# Create a VXLAN tunnel to Switch A.

¡     The
tunnel interface name is Tunnel 2.

¡     The
source address and destination address of the tunnel interface are 2.2.2.2 and
1.1.1.1, respectively.

\[SwitchB] interface tunnel 2
mode vxlan

\[SwitchB-Tunnel2] source
2.2.2.2

\[SwitchB-Tunnel2] destination
1.1.1.1

\[SwitchB-Tunnel2] quit

\# Create a VXLAN tunnel to Switch C.

¡     The
tunnel interface name is Tunnel 3.

¡     The
source address and destination address of the tunnel interface are 2.2.2.2 and
3.3.3.3, respectively.

\[SwitchB] interface tunnel 3
mode vxlan

\[SwitchB-Tunnel3] source
2.2.2.2

\[SwitchB-Tunnel3] destination
3.3.3.3

\[SwitchB-Tunnel3] quit

\# Assign Tunnel 2 and Tunnel 3 to VXLAN
10\.

\[SwitchB] vsi vpna

\[SwitchB-vsi-vpna] vxlan 10

\[SwitchB-vsi-vpna-vxlan-10]
tunnel 2

\[SwitchB-vsi-vpna-vxlan-10]
tunnel 3

\[SwitchB-vsi-vpna-vxlan-10]
quit

\[SwitchB-vsi-vpna] quit

\# On Ten-GigabitEthernet 1/0/1, create Ethernet
service instance 1000 to match VLAN 2\.

\[SwitchB] interface ten-gigabitethernet 1/0/1

\[SwitchB-Ten-GigabitEthernet1/0/1] service-instance
1000

\[SwitchB-Ten-GigabitEthernet1/0/1-srv1000]
encapsulation s-vid 2

\# Map Ethernet service instance 1000 to
VSI vpna.

\[SwitchB-Ten-GigabitEthernet1/0/1-srv1000] xconnect
vsi vpna

\[SwitchB-Ten-GigabitEthernet1/0/1-srv1000] quit

\[SwitchB-Ten-GigabitEthernet1/0/1] quit

**3\.**Configure Switch C:

\# Enable L2VPN.

\<SwitchC\> system-view

\[SwitchC] l2vpn enable

\# Enable Layer 2 forwarding for VXLANs.

\[SwitchC] undo vxlan
ip-forwarding

\# Create a VSI named vpna, and create
VXLAN 10\.

\[SwitchC] vsi vpna

\[SwitchC-vsi-vpna] vxlan 10

\[SwitchC-vsi-vpna-vxlan-10]
quit

\[SwitchC-vsi-vpna] quit

\# Enable the IGMP snooping feature.

\[SwitchB] igmp-snooping

\[SwitchB -igmp-snooping] quit

\# Enable IGMP snooping for VSI vpna.

\[SwitchC] vsi vpna

\[SwitchC-vsi-vpna]
igmp-snooping enable

\# Enable dropping unknown multicast data
packets for VSI vpna.

\[SwitchC-vsi-vpna]
igmp-snooping drop-unknown

\# Specify 3.3.3.3 as the source IP
address of IGMP general queries in VSI vpna.

\[SwitchC-vsi-vpna]
igmp-snooping general-query source-ip 3.3.3.3

\# Enable the IGMP snooping querier.

\[SwitchC-vsi-vpna]
igmp-snooping querier

\[SwitchC-vsi-vpna] quit

\# Assign an IP address to Loopback 0\.
This address will be used as the source address of the VXLAN tunnels to Switch
A and Switch B.

\[SwitchC] interface loopback 0

\[SwitchC-Loopback0] ip address
3.3.3.3 255.255.255.255

\[SwitchC-Loopback0] quit

\# Create a VXLAN tunnel to Switch A.

¡     The
tunnel interface name is Tunnel 1.

¡     The
source address and destination address of the tunnel interface are 3.3.3.3 and
1.1.1.1, respectively.

\[SwitchC] interface tunnel 1
mode vxlan

\[SwitchC-Tunnel1] source
3.3.3.3

\[SwitchC-Tunnel1] destination
1.1.1.1

\[SwitchC-Tunnel1] quit

\# Create a VXLAN tunnel to Switch B.

¡     The
tunnel interface name is Tunnel 3.

¡     The
source address and destination address of the tunnel interface are 3.3.3.3 and
2.2.2.2, respectively.

\[SwitchC] interface tunnel 3
mode vxlan

\[SwitchC-Tunnel3] source
3.3.3.3

\[SwitchC-Tunnel3] destination
2.2.2.2

\[SwitchC-Tunnel3] quit

\# Assign Tunnel 1 and Tunnel 3 to VSI vpna.

\[SwitchC] vsi vpna

\[SwitchC-vsi-vpna] vxlan 10

\[SwitchC-vsi-vpna-vxlan-10]
tunnel 1

\[SwitchC-vsi-vpna-vxlan-10]
tunnel 3

\[SwitchC-vsi-vpna-vxlan-10]
quit

\[SwitchC-vsi-vpna] quit

\# On Ten-GigabitEthernet 1/0/1, create Ethernet
service instance 1000 to match VLAN 2\.

\[SwitchC] interface ten-gigabitethernet 1/0/1

\[SwitchC-Ten-GigabitEthernet1/0/1] service-instance
1000

\[SwitchC-Ten-GigabitEthernet1/0/1-srv1000]
encapsulation s-vid 2

\# Map Ethernet service instance 1000 to
VSI vpna.

\[SwitchC-Ten-GigabitEthernet1/0/1-srv1000] xconnect
vsi vpna

\[SwitchC-Ten-GigabitEthernet1/0/1-srv1000] quit

\[SwitchC-Ten-GigabitEthernet1/0/1] quit

#### Verifying the configuration

\# Display brief information about dynamic
router ports for VSI vpna on Switch A.

\[SwitchA] display igmp-snooping
router-port vsi vpna 

VSI vpna:

  Router ports (1 in total):

    Tun2 (VXLAN ID 10\)                        (00:04:17)

\# Display brief information about dynamic
router ports for VSI vpna on Switch B.

\[SwitchB] display igmp-snooping
router-port vsi vpna 

VSI vpna:

  Router ports (1 in total):

    Tun3 (VXLAN ID 10\)                        (00:04:07)

## Troubleshooting IGMP snooping

### Layer 2 multicast forwarding cannot function

#### Symptom

Layer 2 multicast forwarding cannot
function on the Layer 2 device.

#### Solution

To resolve the problem:

**1\.**Use the display igmp-snooping command to display IGMP snooping status.

**2\.**If IGMP snooping is not enabled, use the igmp-snooping command in system view to enable the IGMP snooping feature. Then,
use the igmp-snooping enable command in VLAN
view or VSI view to enable IGMP snooping for the VLAN or VSI.

**3\.**If IGMP snooping is enabled globally but not
enabled for the VLAN or VSI, use the igmp-snooping enable command in VLAN view or VSI view to enable IGMP snooping for the
VLAN or VSI.

**4\.**If the problem persists, contact H3C
Support.

### Multicast group policy does not work

#### Symptom

Hosts can receive multicast data for multicast
groups that are not permitted by the multicast group policy.

#### Solution

To resolve the problem:

**1\.**Use the display acl command
to verify that the configured ACL meets the multicast group policy requirements.

**2\.**Use the display this command in IGMP-snooping view or in a corresponding interface view
to verify that the correct multicast group policy has been applied. If the
applied policy is not correct, use the group-policy
or
igmp-snooping group-policy command to apply the
correct multicast group policy.

**3\.**Use the display igmp-snooping command to verify that dropping unknown multicast data is enabled.
If it is not, use the igmp-snooping drop-unknown command to enable dropping unknown multicast data.

**4\.**If the problem persists, contact H3C
Support. 

