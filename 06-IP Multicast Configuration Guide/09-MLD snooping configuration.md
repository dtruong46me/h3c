
# Configuring MLD snooping

## About MLD snooping

MLD snooping runs
on a Layer 2 device as an IPv6 multicast constraining mechanism to improve
multicast forwarding efficiency. It creates Layer 2 multicast forwarding entries
from MLD messages that are exchanged between the hosts and the Layer 3 device.

### Fundamentals of MLD snooping

As shown in [Figure 1](#_Ref313026047), when MLD
snooping is not enabled, the Layer 2 switch floods IPv6 multicast packets all hosts
in a VLAN or VSI. When MLD snooping is enabled, the Layer 2 switch forwards multicast
packets of known IPv6 multicast groups to only the receivers.

Figure 1 Multicast packet transmission
processes without and with MLD snooping

![](https://resource.h3c.com/en/202407/12/20240712_11705010_x_Img_x_png_0_2216038_294551_0.png)

 

### MLD snooping ports

As shown in [Figure 2](#_Ref206485088), MLD
snooping runs on Switch A and Switch B, and Host A and Host C are receiver
hosts in an IPv6 multicast group. MLD snooping ports are divided into member
ports and router ports.

Figure 2 MLD snooping ports

![](https://resource.h3c.com/en/202407/12/20240712_11705011_x_Img_x_png_1_2216038_294551_0.png)

 

#### Router ports

On an MLD snooping Layer 2 device, the ports
toward Layer 3 multicast devices are called router ports. In [Figure 2](#_Ref206485088), Port
A1 of Switch A and Port B1 of Switch B are router ports.

Router ports contain the following types:

·     Dynamic router port—When a port receives an MLD general query whose source address is
not 0::0 or receives an IPv6 PIM hello message, the port is added into the
dynamic router port list. At the same time, an aging timer is started for the
port. If the port receives either of the messages before the timer expires, the
timer is reset. If the port does not receive either of the messages when the
timer expires, the port is removed from the dynamic router port list.

·     Static router port—When a port is statically configured as a router port, it is added
into the static router port list. The static router port does not age out, and
it can be deleted only manually.

Do not confuse the "router port"
in MLD snooping with the "routed interface" commonly known as the
"Layer 3 interface." The router port in MLD snooping is a Layer 2
interface.

#### Member ports

On an MLD snooping Layer 2 device, the
ports toward receiver hosts are called member ports. In [Figure 2](#_Ref206485088), Port
A2 and Port A3 of Switch A and Port B2 of Switch B are member ports.

Member ports contain the following types:

·     Dynamic member port—When a port receives an MLD report, it is added to the associated
dynamic MLD snooping forwarding entry as an outgoing interface. At the same
time, an aging timer is started for the port. If the port receives an MLD
report before the timer expires, the timer is reset. If the port does not
receive an MLD report when the timer expires, the port is removed from the
associated dynamic forwarding entry.

·     Static member port—When a port is statically configured as a member port, it is added
to the associated static MLD snooping forwarding entry as an outgoing
interface. The static member port does not age out, and it can be deleted only
manually.

Unless otherwise specified, router ports
and member ports in this document include both static and dynamic router ports
and member ports.

### How MLD snooping works

The ports in this section are dynamic
ports. For information about how to configure and remove static ports, see
"[Configuring a static member
port](#_Ref313473570)" and "[Configuring a static router port](#_Ref454547205)."

MLD messages include
general query, MLD report, and done message. An MLD snooping-enabled Layer 2
device performs differently depending on the MLD message types.

#### General query

The MLD querier periodically sends MLD
general queries to all hosts and devices on the local subnet to check for the
existence of IPv6 multicast group members.

After receiving an MLD general query, the Layer
2 device forwards the query to all ports in the VLAN or VSI except the receiving
port. The Layer 2 device also performs one of the following actions:

·     If the receiving port is a dynamic router port in
the dynamic router port list, the Layer 2 device restarts the aging timer for
the router port.

·     If the receiving port does not exist in the dynamic
router port list, the Layer 2 device adds the port to the dynamic router port
list. It also starts an aging timer for the port.

#### MLD report

A host sends an MLD report to the MLD
querier for the following purposes:

·     Responds to queries if the host is an IPv6
multicast group member.

·     Applies for an IPv6 multicast group membership.

After receiving an MLD report from a host,
the Layer 2 device forwards the report through all the router ports in the VLAN
or VSI. It also resolves the IPv6 address of the reported IPv6 multicast group,
and looks up the forwarding table for a matching entry as follows:

·     If no match is found, the Layer 2 device creates
a forwarding entry for the group with the receiving port an outgoing interface.
It also marks the receiving port as a dynamic member port and starts an aging
timer for the port.

·     If a match is found but the matching forwarding
entry does not contain the receiving port, the Layer 2 device adds the receiving
port to the outgoing interface list. It also marks the port as a dynamic member
port to the forwarding entry and starts an aging timer for the port.

·     If a match is found and the matching forwarding
entry contains the receiving port, the Layer 2 device restarts the aging timer
for the port.

 

|  | NOTE:  A Layer 2 device does not forward an MLD report through a non-router port because of the host MLD report suppression mechanism. If a non-router port has member host attached, the member hosts suppress their MLD reports upon receiving MLD reports forwarded by the non-router port. The Layer 2 device cannot know the existence of the member hosts attached to the non-router port. For more information about the MLD report suppression mechanism, see "Configuring MLD." |
| --- | --- |

 

#### Done message

When a host leaves an IPv6 multicast group,
the host sends an MLD done message to the Layer 3 devices. When the Layer 2
device receives the MLD done message on a dynamic member port, the Layer 2
device first examines whether a forwarding entry matches the IPv6 multicast
group address in the message.

·     If no match is found, the Layer 2 device
discards the MLD done message.

·     If a match is found but the receiving port is
not an outgoing interface in the forwarding entry, the Layer 2 device discards
the MLD done message.

·     If a match is found and the receiving port is
not the only outgoing interface in the forwarding entry, the Layer 2 device
performs the following actions:

¡     Discards
the MLD done message.

¡     Sends
an MLD multicast-address-specific query to identify whether the group has
active listeners attached to the receiving port.

¡     Sets
the aging timer for the receiving port to twice the MLD last listener query
interval.

·     If a match is found and the receiving port is
the only outgoing interface in the forwarding entry, the Layer 2 device performs
the following actions:

¡     Forwards
the MLD done message to all router ports in the VLAN or VSI.

¡     Sends
an MLD multicast-address-specific query to identify whether the group has
active listeners attached to the receiving port.

¡     Sets
the aging timer for the receiving port to twice the MLD last listener query
interval.

After receiving the MLD done message on a
port, the MLD querier resolves the IPv6 multicast group address in the message.
Then, it sends an MLD multicast-address-specific query to the IPv6 multicast
group through the receiving port.

After receiving the MLD multicast-address-specific
query, the Layer 2 device forwards the query through all router ports and
member ports of the group in the VLAN or VSI. Then, it waits for the responding
MLD report from the directly connected hosts. For the dynamic member port that received
the done message, the Layer 2 device also performs one of the following actions:

·     If the port receives an MLD report before the aging
timer expires, the Layer 2 device resets the aging timer for the port.

·     If the port does not receive any MLD report
messages when the aging timer expires, the Layer 2 device removes the port from
the forwarding entry for the IPv6 multicast group.

### MLD snooping proxying

As shown in [Figure 3](#_Ref369504933), to
reduce the number of MLD report and done messages received by the upstream
device, you can enable MLD snooping proxying on the edge device. With MLD
snooping proxying enabled, the edge device acts as a host for the upstream MLD
snooping querier to send MLD report and done messages to Device A. The host MLD
report suppression mechanism on the edge device does not take effect. For more
information about the MLD report suppression mechanism, see "Configuring
MLD."

Figure 3 MLD snooping proxying

![](https://resource.h3c.com/en/202407/12/20240712_11705012_x_Img_x_png_2_2216038_294551_0.png)

 

The MLD snooping proxy device processes
different MLD messages as follows:

·     General query.

After receiving an MLD general query, the
device forwards the query to all ports in the VLAN or VSI except the receiving port.
The device also generates an MLD report based on the local membership
information and sends the report to all router ports.

·     Multicast-address-specific query or
multicast-address-and-source-specific query.

After receiving an MLD multicast-address-specific
query or multicast-address-and-source-specific query, the device forwards the
query to all ports in the VLAN or VSI except the receiving port. If the
forwarding entry has a member port, the device sends a response to all router
ports in the VLAN or VSI.

·     Report.

After receiving an MLD report from a host,
the device looks up the forwarding table for a matching entry as follows:

¡     If a
match is found and the matching forwarding entry contains the receiving port,
the device resets the aging timer for the port.

¡     If a
match is found but the matching forwarding entry does not contain the receiving
port, the device adds the receiving port to the outgoing interface list. It
also marks the receiving port as a dynamic member port and starts an aging
timer for the port.

¡     If no
match is found, the device creates a forwarding entry with the receiving port as
an outgoing interface. It also marks the receiving port as a dynamic member
port and starts an aging timer for the port. Then it sends the report to all
router ports.

·     Done message.

After receiving the MLD done message on a
port, the device sends an MLD multicast-address-specific query through the receiving
port. The device sends the MLD done message to all router ports only when the
last member port is removed from the forwarding entry.

### Protocols and standards

RFC 4541, Considerations
for Internet Group Management Protocol (IGMP) and Multicast Listener Discovery
(MLD) Snooping Switches

## Restrictions and guidelines: MLD snooping configuration

For MLD reports received from secondary VLANs,
the relevant MLD snooping forwarding entries are maintained by the primary
VLAN. Therefore, you need to enable MLD snooping only for the primary VLAN. The
configuration made in secondary VLANs will not take effect. For more
information about primary VLANs and secondary VLANs, see Layer 2—LAN Switching Configuration Guide.

The MLD snooping configurations made on
Layer 2 aggregate interfaces do not interfere with the configurations made on
member ports. In addition, the configurations made on Layer 2 aggregate
interfaces do not take part in aggregation calculations. The configuration made
on a member port of the aggregate group takes effect after the port leaves the
aggregate group.

Some features can be configured for a VLAN
in VLAN view or for multiple VLANs in MLD-snooping view. The VLAN-specific
configuration and the configuration made in MLD-snooping view have the same
priority, and the most recent configuration takes effect.

Some features can be configured for a VLAN
in VLAN view or globally for all VLANs in MLD-snooping view. The VLAN-specific configuration
takes priority over the global configuration.

Some features can be configured for a VSI
in VSI view or globally for all VSIs in MLD-snooping view. The VSI-specific
configuration takes priority over the global configuration.

Some features can be configured for an
interface in interface view or for all interfaces of the specified VLANs in
MLD-snooping view. The interface-specific configuration takes priority over the
configuration made in MLD-snooping view.

## VLAN-based MLD snooping tasks at a glance

To configure MLD snooping for VLANs,
perform the following tasks:

**1\.**[Enabling the MLD snooping feature](#_Ref480301175)

**2\.**[Enabling MLD snooping](#_Ref480301183)

Choose the following tasks as needed:

¡     [Enabling MLD snooping globally](#_Ref480301190)

¡     [Enabling MLD snooping for VLANs](#_Ref480274901)

**3\.**(Optional.) [Configuring basic MLD snooping features](#_Ref474348582)

¡     [Specifying an MLD snooping version](#_Ref474348629)

¡     [Setting the maximum number of MLD snooping
forwarding entries](#_Ref386204066)

¡     [Configuring static IPv6 multicast MAC
address entries](#_Ref447267859)

¡     [Setting the MLD last listener query
interval](#_Ref445913696)

**4\.**(Optional.) [Configuring MLD snooping port features](#_Ref474348656)

¡     [Setting aging timers for dynamic ports](#_Ref386204144)

¡     [Configuring a static member port](#_Ref474348669)

¡     [Configuring a static router port](#_Ref454547205)

¡     [Configuring a port as a simulated member
host](#_Ref474348692)

¡     [Enabling fast-leave processing](#_Ref474348696)

¡     [Disabling a port from becoming a dynamic
router port](#_Ref474348701)

¡     [Preventing the device from forwarding
multicast data to router ports in a VLAN](#_Ref81836979)

**5\.**(Optional.) [Configuring the MLD snooping querier](#_Ref365103349)

¡     [Enabling the MLD snooping querier](#_Ref474348940)

¡     [Enabling MLD snooping querier election](#_Ref490468580)

¡     [Configuring parameters for MLD general queries
and responses](#_Ref474348945)

¡     [Enabling the device to send MLD general queries
upon a path change](#_Ref508874058)

**6\.**(Optional.) [Enabling MLD snooping proxying](#_Ref444519467)

**7\.**(Optional.) [Configuring parameters for MLD messages](#_Ref365103384)

¡     [Configuring source IPv6 addresses for MLD messages](#_Ref365103396)

¡     [Setting the 802.1p priority for MLD
messages](#_Ref474348988)

**8\.**(Optional.) [Configuring MLD snooping policies](#_Ref474349003)

¡     [Configuring an IPv6 multicast group policy](#_Ref392504856)

¡     [Enabling IPv6 multicast source port
filtering](#_Ref474349012)

¡     [Enabling dropping unknown IPv6 multicast
data](#_Ref332975250)

¡     [Enabling MLD report suppression](#_Ref474349022)

¡     [Setting the maximum number of IPv6
multicast groups on a port](#_Ref357782929)

¡     [Enabling IPv6 multicast group replacement](#_Ref392504847)

¡     [Enabling host tracking](#_Ref474349036)

**9\.**(Optional.) [Setting the DSCP value for outgoing MLD
protocol packets](#_Ref491337104)

## VSI-based MLD snooping tasks at a glance

To configure MLD snooping for VSIs, perform
the following tasks:

**1\.**[Enabling the MLD snooping feature](#_Ref480301545)

**2\.**[Enabling MLD snooping](#_Ref480301553)

Choose the following tasks as needed:

¡     [Enabling MLD snooping globally](#_Ref480301561)

¡     [Enabling MLD snooping for a VSI](#_Ref480301573)

**3\.**(Optional.) [Configuring basic MLD snooping features](#_Ref474348582)

¡     [Specifying an MLD snooping version](#_Ref474348629)

¡     [Setting the maximum number of MLD
snooping forwarding entries](#_Ref386204066)

¡     [Setting the MLD last listener query
interval](#_Ref445913696)

**4\.**(Optional.) [Setting aging timers for dynamic ports](#_Ref386204144)

**5\.**(Optional.) [Configuring the MLD snooping querier](#_Ref365103349)

¡     [Enabling the MLD snooping querier](#_Ref474348940)

¡     [Enabling MLD snooping querier election](#_Ref490468594)

¡     [Configuring parameters for MLD general queries
and responses](#_Ref474348945)

**6\.**(Optional.) [Enabling MLD snooping proxying](#_Ref503857335)

**7\.**(Optional.) [Configuring parameters for MLD messages](#_Ref365103384)

¡     [Configuring source IPv6 addresses for
MLD messages](#_Ref365103396)

¡     [Setting the 802.1p priority for MLD
messages](#_Ref474348988)

**8\.**(Optional.) [Configuring MLD snooping policies](#_Ref474349003)

¡     [Enabling dropping unknown IPv6 multicast
data](#_Ref332975250)

¡     [Enabling MLD report suppression](#_Ref474349022)

**9\.**(Optional.) [Setting the DSCP value for outgoing MLD
protocol packets](#_Ref491337104)

## Enabling the MLD snooping feature

#### About this task

You must enable the MLD snooping feature
before you configure other MLD snooping features.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enable the MLD snooping feature and enter
MLD-snooping view.

mld-snooping

By default, the MLD snooping feature is
disabled.

## Enabling MLD snooping

### Enabling MLD snooping globally

#### About this task

After you enable MLD snooping globally, MLD
snooping is enabled for all VLANs or VSIs. You can disable MLD snooping for a
VLAN or VSI when MLD snooping is globally enabled.

#### Restrictions and guidelines

To configure other MLD snooping features
for VLANs or VSIs, you must enable MLD snooping for the specific VLANs or VSIs
even though MLD snooping is enabled globally.

The VLAN-specific or VSI-specific MLD
snooping configuration takes priority over the global MLD snooping
configuration. For example, if you enable MLD snooping globally and then use
the mld-snooping
disable command to disable MLD snooping for a
VLAN, MLD snooping is disabled in the VLAN.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter MLD-snooping view.

mld-snooping

**3\.**Enable MLD snooping globally.

global-enable

By default, MLD snooping is disabled
globally.

**4\.**(Optional.) Disable MLD snooping for a VLAN
or VSI.

**a.**Return to system view.

quit

**b.**Enter VLAN or VSI view.

vlan vlan-id

vsi vsi-name

**c.**Disable MLD snooping for the VLAN or VSI.

mld-snooping disable

By default, the MLD snooping status in a
VLAN or VSI is consistent with the global MLD snooping status.

### Enabling MLD snooping for VLANs

#### Restrictions and guidelines

You can enable MLD snooping for multiple
VLANs by using the enable vlan command in MLD-snooping
view or for a VLAN by using the mld-snooping enable command in VLAN view. The configuration in VLAN view has the same
priority as the configuration in MLD-snooping view.

MLD snooping configuration in a VLAN takes
effect only on the member ports in the VLAN.

#### Enabling MLD snooping for multiple VLANs

**1\.**Enter system view.

system-view

**2\.**Enter MLD-snooping view.

mld-snooping

**3\.**Enable MLD snooping for multiple VLANs.

enable vlan vlan-list

By default, the MLD snooping status in a
VLAN is consistent with the global MLD snooping status.

#### Enabling MLD snooping for a VLAN

**1\.**Enter system view.

system-view

**2\.**Enter VLAN view.

vlan vlan-id

**3\.**Enable MLD snooping for the VLAN.

mld-snooping enable

By default, the MLD snooping status in a
VLAN is consistent with the global MLD snooping status.

### Enabling MLD snooping for a VSI

#### Restrictions and guidelines

For VSIs, you can only enable MLD snooping
for a VSI by using the mld-snooping enable
command in VSI view.

MLD snooping configuration in a VSI takes
effect only on the member ports in the VSI.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter VSI view.

vsi vsi-name

**3\.**Enable MLD snooping for the VSI.

mld-snooping enable

By default, the MLD snooping status in a
VSI is consistent with the global MLD snooping status.

## Configuring basic MLD snooping features

### Specifying an MLD snooping version

#### About this task

Different MLD snooping versions can process
different versions of MLD messages:

·     MLDv1 snooping can process MLDv1 messages and
MLDv2 queries, but it floods MLDv2 reports in the VLAN or VSI instead of
processing them.

·     MLDv2 snooping can process MLDv1 and MLDv2
messages.

#### Restrictions and guidelines

If you change the version of MLD snooping
from 2 to 1, the system performs the following actions:

·     Clears all MLD snooping forwarding entries that
are dynamically created.

·     Keeps static MLDv2 snooping forwarding entries
(\*, G).

·     Clears static MLDv2 snooping forwarding entries
(S, G), which will be restored when MLD snooping is switched back to MLDv2
snooping.

For more information about static MLD
snooping forwarding entries, see "[Configuring a static member port](#_Ref313473570)."

#### Specifying an MLD snooping version for multiple VLANs

**1\.**Enter system view.

system-view

**2\.**Enter MLD-snooping view.

mld-snooping

**3\.**Specify an MLD snooping version for multiple
VLANs.

version version-number vlan vlan-list

By default, the MLD snooping version for
a VLAN is 1\.

#### Specifying an MLD snooping version for a VLAN

**1\.**Enter system view.

system-view

**2\.**Enter VLAN view.

vlan vlan-id

**3\.**Specify an MLD snooping version for the
VLAN.

mld-snooping version version-number

By default, the MLD snooping version for
a VLAN is 1\.

#### Specifying an MLD snooping version for a VSI

**1\.**Enter system view.

system-view

**2\.**Enter VSI view.

vsi vsi-name

**3\.**Specify an MLD snooping version for the VSI.

mld-snooping version version-number

By default, the MLD snooping version for
a VSI is 1\.

### Setting the maximum number of MLD snooping forwarding entries

#### About this task

You can modify the maximum number of MLD
snooping forwarding entries, including dynamic entries and static entries. When
the number of forwarding entries on the device reaches the upper limit, the
device does not automatically remove any existing entries. To allow new entries
to be created, remove some entries manually.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter MLD-snooping view.

mld-snooping

**3\.**Set the maximum number of MLD snooping
forwarding entries.

entry-limit limit

By default, the maximum number of MLD
snooping forwarding entries is 4294967295.

### Configuring static IPv6 multicast MAC addressentries

#### About this task

In Layer 2 IPv6 multicast, IPv6 multicast
MAC address entries can be dynamically created through Layer 2 multicast
protocols (such as MLD snooping). You can also manually configure static IPv6 multicast
MAC address entries by binding IPv6 multicast MAC addresses and ports to
control the destination ports of the IPv6 multicast data.

#### Restrictions and guidelines

You must specify an unused multicast MAC
address when configuring a static IPv6 multicast MAC address entry. A multicast
MAC address is the MAC address in which the least significant bit of the most
significant octet is 1\.

#### Configuring a static IPv6 multicast MAC address entry in system view

**1\.**Enter system view.

system-view

**2\.**Configure a static IPv6 multicast MAC
address entry.

mac-address multicast mac-address interface interface-list vlan vlan-id

#### Configuring a static IPv6 multicast MAC address entry in interface view

**1\.**Enter system view.

system-view

**2\.**Enter Layer 2 interface view.

¡     Enter
Layer 2 Ethernet interface view.

interface interface-type interface-number

¡     Enter Layer 2 aggregate
interface view.

interface bridge-aggregation interface-number

**3\.**Configure a static IPv6 multicast MAC
address entry.

mac-address multicast mac-address vlan vlan-id

### Setting theMLD last listener query interval

#### About this task

A receiver host starts a report delay timer
for an IPv6 multicast group when it receives an MLD multicast-address-specific
query for the group. This timer is set to a random value in the range of 0 to
the maximum response time advertised in the query. When the timer value
decreases to 0, the host sends an MLD report to the group.

The MLD last listener query interval
defines the maximum response time advertised in MLD multicast-address-specific queries.
Set an appropriate value for the MLD last listener query interval to speed up hosts'
responses to MLD multicast-address-specific queries and avoid MLD report
traffic bursts.

#### Setting the MLD last listener query interval globally

**1\.**Enter system view.

system-view

**2\.**Enter MLD-snooping view.

mld-snooping

**3\.**Set the MLD last listener query interval
globally.

last-listener-query-interval interval

By default, the MLD last listener query
interval is 1 second.

#### Setting the MLD last listener query interval for a VLAN

**1\.**Enter system view.

system-view

**2\.**Enter VLAN view.

vlan vlan-id

**3\.**Set the MLD last listener query interval for
the VLAN

mld-snooping last-listener-query-interval interval

By default, the MLD last listener query
interval is 1 second for a VLAN.

#### Setting the MLD last listener query interval for a VSI

**1\.**Enter system view.

system-view

**2\.**Enter VSI view.

vsi vsi-name

**3\.**Set the MLD last listener query interval for
the VSI.

mld-snooping last-listener-query-interval interval

By default, the MLD last listener query
interval is 1 second for a VSI.

## Configuring MLD snooping port features

### Setting aging timers for dynamic ports

#### About this task

A dynamic router port is removed from the
dynamic router port list if it does not receive an MLD general query or IPv6 PIM
hello message when its aging timer expires.

A dynamic member port is removed from the
dynamic member port if it does not receive an MLD report when its aging timer
expires.

#### Restrictions and guidelines

Set an appropriate value for the aging
timers of dynamic ports based on the actual network requirement. For example,
if the memberships of IPv6 multicast groups frequently change, set a relatively
small value for the aging timer of the dynamic member ports. 

If a dynamic router port receives an IPv6
PIMv2 hello message, the aging timer for the port is specified by the hello
message. In this case, the mld-snooping router-aging-time command does not take effect on the port.

MLD multicast-address-specific queries
originated by the Layer 2 device trigger the adjustment of aging timers of
dynamic member ports. If a dynamic member port receives such a query, its aging
timer is set to twice the MLD last listener query interval. For more
information about setting the MLD last listener query interval on the Layer 2
device, see "[Setting the MLD last listener query interval](#_Ref445913696)."

#### Setting the aging timers for dynamic ports globally

**1\.**Enter system view.

system-view

**2\.**Enter MLD-snooping view.

mld-snooping

**3\.**Set the aging timer for dynamic router ports
globally.

router-aging-time seconds

By default, the aging timer for dynamic
router ports is 260 seconds.

**4\.**Set the aging timer for dynamic member ports
globally.

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

mld-snooping router-aging-time seconds

By default, the aging timer for dynamic
router ports is 260 seconds for a VLAN.

**4\.**Set the aging timer for dynamic member ports
in the VLAN.

mld-snooping host-aging-time seconds

By default, the aging timer for dynamic
member ports is 260 seconds for a VLAN.

#### Setting the aging timers for dynamic ports in a VSI

**1\.**Enter system view.

system-view

**2\.**Enter VSI view.

vsi vsi-name

**3\.**Set the aging timer for dynamic router ports
in the VSI.

mld-snooping router-aging-time seconds

By default, the aging timer for dynamic
router ports is 260 seconds for a VSI.

**4\.**Set the aging timer for dynamic member ports
in the VSI.

mld-snooping host-aging-time seconds

By default, the aging timer for dynamic
member ports is 260 seconds for a VSI.

### Configuring a static member port

#### About this task

You can configure a port as a static member
port for an IPv6 multicast group so that all hosts attached to the port can
always receive IPv6 multicast data for the group. The static member port does
not respond to MLD queries. When you complete or cancel this configuration, the
port does not send an unsolicited report or done message. 

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

**3\.**Configure the port as a static member port.

mld-snooping static-group ipv6-group-address \[ source-ip ipv6-source-address ] vlan vlan-id

By default, a port is not a static member
port.

### Configuring a static router port

#### About this task

You can configure a port as a static router
port for an IPv6 multicast group so that all IPv6 multicast data for the group
received on the port will be forwarded.

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

**3\.**Configure the port as a static router port.

mld-snooping static-router-port vlan vlan-id

By default, a port is not a static router
port.

### Configuring a port as a simulated member host

#### About this task

When a port is configured as a simulated
member host, it is equivalent to an independent host in the following ways:

·     It sends an unsolicited MLD report when you
complete the configuration.

·     It responds to MLD general queries with MLD
reports.

·     It sends an MLD done message when you remove the
configuration.

The version of MLD running on the simulated
member host is the same as the version of MLD snooping running on the port. The
port ages out in the same ways as a dynamic member port.

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

mld-snooping host-join ipv6-group-address \[ source-ip ipv6-source-address ] vlan vlan-id

By default, the port is not a simulated
member host.

### Enabling fast-leave processing

#### About this task

This feature enables the Layer 2 device to
immediately remove a port from the forwarding entry for an IPv6 multicast group
when the port receives a done message. The device no longer sends or forwards
MLD multicast-address-specific queries for the group to the port.

#### Restrictions and guidelines

Do not enable fast-leave processing on a
port that has multiple receiver hosts attached in a VLAN. If you do so, the
remaining receivers cannot receive IPv6 multicast data for a group after a
receiver leaves the group.

#### Enabling fast-leave processing globally

**1\.**Enter system view.

system-view

**2\.**Enter MLD-snooping view.

mld-snooping

**3\.**Enable fast-leave processing globally.

fast-leave \[ vlan vlan-list ]

By default, fast-leave processing is disabled
globally.

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

mld-snooping fast-leave \[ vlan vlan-list ]

By default, fast-leave processing is
disabled on a port.

### Disabling a port from becoming a dynamic router port

#### About this task

A receiver host might send MLD general queries
or IPv6 PIM hello messages for testing purposes. On the Layer 2 device, the
port that receives either of the messages becomes a dynamic router port. Before
the aging timer for the port expires, the following problems might occur:

·     All IPv6 multicast data for the VLAN to which
the port belongs flows to the port. Then, the port forwards the data to
attached receiver hosts. The receiver hosts will receive IPv6 multicast data
that it does not expect.

·     The port forwards the MLD general queries or IPv6
PIM hello messages to its upstream Layer 3 devices. These messages might affect
the multicast routing protocol state (such as the MLD querier or DR election)
on the Layer 3 devices. This might further cause network interruption.

To solve these problems, you can disable the
port from becoming a dynamic router port when receiving either of the messages.
This also improves network security and the control over receiver hosts.

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

mld-snooping router-port-deny \[ vlan vlan-list ]

By default, a port is allowed to become a
dynamic router port.

### Preventing the device from forwarding multicast data to router ports in a VLAN

#### About this task

If all ports on an MLD snooping Layer 2
device are statically configured as router ports, the Layer 2 device will forwards
multicast data from Layer 3 multicast devices to all its router ports. This
wastes bandwidth resources. Perform this task to not forward multicast data to
router ports in a VLAN if they are not interested in receiving the data.

#### Prerequisites

Before performing this task, you must
enable MLD snooping in the VLAN.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter VLAN view.

vlan vlan-id

**3\.**Prevent the device from forwarding multicast
data to static router ports in the VLAN.

mld-snooping router-port-discard

By default, the device forwards multicast
data to static router ports in a VLAN.

## Configuring the MLD snooping querier

### Enabling the MLD snooping querier

#### About this task

This feature enables the Layer 2 device to periodically
send MLD general queries to establish and maintain multicast forwarding entries
at the data link Layer. You can configure an MLD snooping querier on a network
without Layer 3 multicast devices.

#### Restrictions and guidelines

Do not enable the MLD snooping querier on
an IPv6 multicast network that runs MLD. An MLD snooping querier does not participate
in MLD querier elections. However, it might affect MLD querier elections if it
sends MLD general queries with a low source IPv6 address. For more information
about MLD queries, see "Configuring MLD."

On a VXLAN network, the MLD snooping
querier in a VSI does not include VLAN tags in MLD general queries. As a best
practice, do not enable the MLD snooping querier in a VSI if the VSI uses the
Ethernet access mode. For more information about the Ethernet access mode, see VXLAN Configuration Guide.

#### Enabling the MLD snooping querier for a VLAN

**1\.**Enter system view.

system-view

**2\.**Enter VLAN view.

vlan vlan-id

**3\.**Enable the MLD snooping querier for the
VLAN.

mld-snooping querier

By default, the MLD snooping querier is
disabled for a VLAN.

#### Enabling the MLD snooping querier for a VSI

**1\.**Enter system view.

system-view

**2\.**Enter VSI view.

vsi vsi-name

**3\.**Enable the MLD snooping querier for the VSI.

mld-snooping querier

By default, the MLD snooping querier is
disabled for a VSI.

### Enabling MLD snooping querier election

#### About this task

To avoid traffic interruption caused by the
failure of a single querier in a VLAN or VSI, configure multiple queriers in
the VLAN or VSI and enable querier election. When the elected querier fails,
the device starts a new querier election to ensure multicast forwarding. The mechanism
for MLD snooping querier election is the same as that for MLD querier election.
For more information about MLD querier election, see "Configuring MLD."

#### Prerequisites

Before you enable MLD snooping querier
election, you must complete the following tasks:

·     Enable the MLD snooping querier for a VLAN or
VSI. For more information about enabling the MLD snooping querier, see "[Enabling the MLD snooping querier](#_Ref490465679)."

·     Configure the source IPv6 address for MLD
general queries as an IPv6 address different from :: and the local querier IPv6
address. An MLD snooping querier performs querier election only if the source
IPv6 address of a received MLD general query is not :: or its own IPv6 address.

·     Make sure the candidate MLD snooping queriers
run the same MLD snooping version. To specify the MLD snooping version, use the
mld-snooping
version command.

#### Enabling MLD snooping querier election for a VLAN

**1\.**Enter system view.

system-view

**2\.**Enter VLAN view.

vlan vlan-id

**3\.**Enable MLD snooping querier election for the
VLAN.

mld-snooping querier-election

By default, MLD snooping querier election
is disabled for a VLAN.

#### Enabling MLD snooping querier election for a VSI

**1\.**Enter system view.

system-view

**2\.**Enter VSI view.

vsi vsi-name

**3\.**Enable MLD snooping querier election for the
VSI.

mld-snooping querier-election

By default, MLD snooping querier election
is disabled for a VSI.

### Configuring parameters for MLD general queries and responses

#### About this task

You can modify the MLD general query
interval based on the actual network conditions.

A receiver host starts a report delay timer
for each IPv6 multicast group that it has joined when it receives an MLD
general query. This timer is set to a random value in the range of 0 to the maximum
response time advertised in the query. When the timer value decreases to 0, the
host sends an MLD report to the corresponding IPv6 multicast group.

Set an appropriate value for the maximum response
time for MLD general queries to speed up hosts' responses to MLD general
queries and avoid MLD report traffic bursts.

#### Restrictions and guidelines

To avoid mistakenly deleting IPv6 multicast
group members, make sure the MLD general query interval is greater than the
maximum response time for MLD general queries.

#### Configuring parameters for MLD general queries and responses globally

**1\.**Enter system view.

system-view

**2\.**Enter MLD-snooping view.

mld-snooping

**3\.**Set the maximum response time for MLD
general queries.

max-response-time seconds

By default, the maximum response time for
MLD general queries is 10 seconds.

#### Configuring parameters for MLD general queries and responses for a VLAN

**1\.**Enter system view.

system-view

**2\.**Enter VLAN view.

vlan vlan-id

**3\.**Set the MLD general query interval in the
VLAN.

mld-snooping query-interval interval

By default, the MLD general query interval
for a VLAN is 125 seconds.

**4\.**Set the maximum response time for MLD
general queries in the VLAN.

mld-snooping max-response-time seconds

By default, the maximum response time for
MLD general queries for a VLAN is 10 seconds.

#### Configuring parameters for MLD general queries and responses for a VSI

**1\.**Enter system view.

system-view

**2\.**Enter VSI view.

vsi vsi-name

**3\.**Set the MLD general query interval in the
VSI.

mld-snooping query-interval interval

By default, the MLD general query interval
for a VSI is 125 seconds.

**4\.**Set the maximum response time for MLD
general queries in the VSI.

mld-snooping max-response-time seconds

By default, the maximum response time for
MLD general queries for a VSI is 10 seconds.

### Enabling the device to send MLD general queries upon a path change

#### About this task

Perform this task to enable the device to immediately
send an MLD general query to all ports in the affected VLAN when the path
changes. This feature ensures that the device can update the member port
information in IPv6 multicast forwarding entries and switch IPv6 multicast
traffic to the new path promptly.

#### Restrictions and guidelines

Perform this task on the MLD snooping
querier in a network of which the topology frequently changes.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter MLD-snooping view.

mld-snooping

**3\.**Enable the device to send MLD general queries
upon a path change.

send-query enable

By default, the device does not send MLD
general queries upon a path change.

## Enabling MLD snooping proxying

### About MLD snooping proxying

The device enabled with MLD snooping
proxying is called an MLD snooping proxy. The MLD snooping proxy acts as a host
to the upstream device. Enabled with MLD snooping querier, the MLD snooping
proxy acts as the router to downstream devices and receives report and done
messages on behalf of the upstream device. As a best practice, enable MLD
snooping proxy on the edge device to alleviate the effect caused by excessive
packets.

### Restrictions and guidelines for enabling MLD snooping proxying

Before you enable MLD snooping proxying for
a VLAN or VSI, you must first enable MLD snooping globally and enable MLD
snooping for the VLAN or VSI. MLD snooping proxying does not take effect on sub
VLANs of a multicast VLAN.

Use this feature with MLD snooping querier.
For more information about enabling MLD snooping querier, see "[Enabling the MLD snooping querier](#_Ref529804281)."

### Enabling MLD snooping proxying for a VLAN

**1\.**Enter system view.

system-view

**2\.**Enter VLAN view.

vlan vlan-id

**3\.**Enable MLD snooping proxying for the VLAN.

mld-snooping proxy enable

By default, MLD snooping proxying is
disabled for a VLAN.

### Enabling MLD snooping proxying for a VSI

**1\.**Enter system view.

system-view

**2\.**Enter VSI view.

vsi vsi-name

**3\.**Enable MLD snooping proxying for the VSI.

mld-snooping proxy enable

By default, MLD snooping proxying is
disabled for a VSI.

## Configuring parameters for MLD messages

### Configuring source IPv6 addresses for MLDmessages

#### About this task

You can change the source IPv6 address of the
MLD queries sent by an MLD snooping querier. This configuration might affect
MLD querier election within the subnet.

You can also change the source IPv6 address
of MLD reports or done messages sent by a simulated member host or an MLD snooping
proxy.

#### Configuring the source IPv6 addresses for MLD messages for a VLAN

**1\.**Enter system view.

system-view

**2\.**Enter VLAN view.

vlan vlan-id

**3\.**Configure the source IPv6 address for MLD
general queries.

mld-snooping general-query source-ip ipv6-address

By default, the source IPv6 address of
MLD general queries is the IPv6 link-local address of the current VLAN
interface. If the current VLAN interface does not have an IPv6 link-local
address, the source IPv6 address is FE80::02FF:FFFF:FE00:0001.

**4\.**Configure the source IPv6 address for MLD
multicast-address-specific queries.

mld-snooping special-query source-ip ipv6-address

By default, the source IPv6 address of
MLD multicast-address-specific queries is one of the following:

¡     The
source address of MLD general queries if the MLD snooping querier of the VLAN
has received MLD general queries.

¡     The
IPv6 link-local address of the current VLAN interface if the MLD snooping
querier does not receive an MLD general query.

¡     FE80::02FF:FFFF:FE00:0001
if the MLD snooping querier does not receive an MLD general query and the
current VLAN interface does not have an IPv6 link-local address.

**5\.**Configure the source IPv6 address for MLD
reports.

mld-snooping report source-ip ipv6-address

By default, the source IPv6 address of
MLD reports is the IPv6 link-local address of the current VLAN interface. If
the current VLAN interface does not have an IPv6 link-local address, the source
IPv6 address is FE80::02FF:FFFF:FE00:0001.

**6\.**Configure the source IPv6 address for MLD
done messages.

mld-snooping done source-ip ipv6-address

By default, the source IPv6 address of
MLD done messages is the IPv6 link-local address of the current VLAN interface.
If the current VLAN interface does not have an IPv6 link-local address, the
source IPv6 address is FE80::02FF:FFFF:FE00:0001.

#### Configuring the source IPv6 addresses for MLD messages for a VSI

**1\.**Enter system view.

system-view

**2\.**Enter VLAN view.

vsi vsi-name

**3\.**Configure the source IPv6 address for MLD
general queries.

mld-snooping general-query source-ip ipv6-address

By default, the source IPv6 address of
MLD general queries is the IPv6 link-local address of the gateway interface for
a VSI. If the gateway interface does not have an IPv6 link-local address, the
source IPv6 address is FE80::02FF:FFFF:FE00:0001.

**4\.**Configure the source IPv6 address for MLD
multicast-address-specific queries.

mld-snooping special-query source-ip ipv6-address

By default, the source IPv6 address of
MLD multicast-address-specific queries is one of the following:

¡     The
source address of MLD general queries if the MLD snooping querier of the VSI
has received MLD general queries.

¡     IPv6
link-local address of the gateway interface for the VSI if the MLD snooping querier
does not receive an MLD general query.

¡     FE80::02FF:FFFF:FE00:0001
if the gateway interface does not have an IPv6 link-local address.

**5\.**Configure the source IPv6 address for MLD
reports.

mld-snooping report source-ip ipv6-address

By default, the source IPv6 address of
MLD reports is the IPv6 link-local address of the gateway interface for a VSI.
If the gateway interface does not have an IPv6 link-local address, the source
IPv6 address is FE80::02FF:FFFF:FE00:0001.

**6\.**Configure the source IPv6 address for MLD done
messages.

mld-snooping done source-ip ipv6-address

By default, the source IPv6 address of
MLD done messages is the IPv6 link-local address of the gateway interface for a
VSI. If the gateway interface does not have an IPv6 link-local address, the
source IPv6 address is FE80::02FF:FFFF:FE00:0001.

### Setting the 802.1p priority for MLD messages

#### About this task

When congestion occurs on outgoing ports of
the Layer 2 device, it forwards MLD messages in their 802.1p priority order,
from highest to lowest. You can assign a higher 802.1p priority to MLD messages
that are created or forwarded by the device.

#### Restrictions and guidelines

The configuration in MLD-snooping view
takes effect only for VLANs. For VSIs, you can set the 802.1p priority for MLD
messages only in VSI view.

#### Setting the 802.1p priority for MLD messages globally

**1\.**Enter system view.

system-view

**2\.**Enter MLD-snooping view.

mld-snooping

**3\.**Set the 802.1p priority for MLD messages
globally.

dot1p-priority priority

By default, the global 802.1p priority is
6 for MLD messages.

#### Setting the 802.1p priority for MLD messages for a VLAN

**1\.**Enter system view.

system-view

**2\.**Enter VLAN view.

vlan vlan-id

**3\.**Set the 802.1p priority for MLD messages in
the VLAN.

mld-snooping dot1p-priority priority

By default, the 802.1p priority is 6 for
MLD messages in a VLAN.

## Configuring MLD snooping policies

### Configuring an IPv6 multicast group policy

#### About this task

This feature enables the Layer 2 device to
filter MLD reports by using an ACL that specifies the IPv6 multicast groups and
the optional sources. It is used to control the IPv6 multicast groups that receiver
hosts can join. This configuration takes effect on the IPv6 multicast groups
that ports join dynamically. 

In an IPv6 multicast application, a host
sends an unsolicited MLD report when a user requests an IPv6 multicast program.
The Layer 2 device uses the IPv6 multicast group policy to filter the MLD
report. The host can join the IPv6 multicast group only if the MLD report is
permitted by the IPv6 multicast group policy. 

#### Configuring an IPv6 multicast group policy globally

**1\.**Enter system view.

system-view

**2\.**Enter MLD-snooping view.

mld-snooping

**3\.**Configure an IPv6 multicast group policy
globally.

group-policy ipv6-acl-number \[ vlan vlan-list ]

By default, no IPv6 multicast group policies
exist. Hosts can join any IPv6 multicast groups.

#### Configuring an IPv6 multicast group policy on a port

**1\.**Enter system view.

system-view

**2\.**Enter Layer 2 interface view.

¡     Enter
Layer 2 Ethernet interface view.

interface interface-type interface-number

¡     Enter Layer 2 aggregate
interface view.

interface bridge-aggregation interface-number

**3\.**Configure an IPv6 multicast group policy on
the port.

mld-snooping group-policy ipv6-acl-number \[ vlan vlan-list ]

By default, no IPv6 multicast group
policies exist on a port. Hosts attached to the port can join any IPv6 multicast
groups.

### Enabling IPv6 multicast source port filtering

#### About this task

This feature enables the Layer 2 device to
discard all IPv6 multicast data packets and to accept IPv6 multicast protocol
packets. You can enable this feature on ports that connect to only IPv6
multicast receivers.

The configuration made in MLD-snooping view
has the same priority over the interface-specific configuration.

#### Restrictions and guidelines

When IPv6 multicast source port filtering
is enabled, the device automatically enables IPv4 multicast source port
filtering.

The configuration made in MLD-snooping view
has the same priority as the interface-specific configuration, and the most
recent configuration takes effect.

#### Enabling IPv6 multicast source port filtering in MLD-snooping view

**1\.**Enter system view.

system-view

**2\.**Enter MLD-snooping view.

mld-snooping

**3\.**Enable IPv6 multicast source port filtering
globally.

source-deny port
interface-list

By default, IPv6 multicast source port
filtering is disabled globally.

#### Enabling IPv6 multicast source port filtering in interface view

**1\.**Enter system view.

system-view

**2\.**Enter Layer 2 Ethernet interface view.

interface interface-type interface-number

**3\.**Enable IPv6 multicast source port filtering
on the port.

mld-snooping source-deny

By default, IPv6 multicast source port
filtering is disabled on a port.

### Enabling dropping unknown IPv6 multicast data

#### About this task

Unknown IPv6 multicast data refers to IPv6
multicast data for which no forwarding entries exist in the MLD snooping
forwarding table. This feature enables the device to forward unknown IPv6
multicast data only to the router port. If the device does not have a router
port, unknown IPv6 multicast data will be dropped.

If you do not enable this feature, the
unknown IPv6 multicast data is flooded in the VLAN or VSI to which the data
belongs.

#### Restrictions and guidelines

When dropping unknown IPv6 multicast data
is enabled, the device also drops unknown IPv4 multicast data.

When this feature is enabled for a VLAN or
VSI, the device still forwards unknown IPv6 multicast data out of router ports
(except the receiving router port) in this VLAN or VSI.

If the outgoing ports of the IPv6 multicast
forwarding entry for a Layer 3 IPv6 multicast packet are in the same VLAN,
enable dropping unknown IPv6 multicast data for the VLAN as a best practice.
This restriction prevents the device from flooding the Layer 3 IPv6 multicast
packet in the VLAN.

#### Enabling dropping unknown IPv6 multicast data for a VLAN

**1\.**Enter system view.

system-view

**2\.**Enter VLAN view.

vlan vlan-id

**3\.**Enable dropping unknown IPv6 multicast data
for the VLAN.

mld-snooping drop-unknown

By default, dropping unknown IPv6
multicast data is disabled. Unknown IPv6 multicast data is flooded.

#### Enabling dropping unknown IPv6 multicast data for a VSI

**1\.**Enter system view.

system-view

**2\.**Enter VSI view.

vsi vsi-name

**3\.**Enable dropping unknown IPv6 multicast data
for the VSI.

mld-snooping drop-unknown

By default, dropping unknown IPv6 multicast
data is disabled. Unknown IPv6 multicast data is flooded.

### Enabling MLD report suppression

#### About this task

This feature enables the Layer 2 device to
forward only the first MLD report for an IPv6 multicast group to its directly
connected Layer 3 device. Other reports for the same group in the same query
interval are discarded. Use this feature to reduce the multicast traffic.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter MLD-snooping view.

mld-snooping

**3\.**Enable MLD report suppression.

report-aggregation

By default, MLD report suppression is enabled.

### Setting the maximum number of IPv6 multicast groupson a port

#### About this task

You can set the maximum number of IPv6
multicast groups on a port to regulate the port traffic. This feature takes
effect only on the IPv6 multicast groups that the port joins dynamically.

If the number of IPv6 multicast groups on a
port exceeds the limit, the system removes all the forwarding entries related
to that port. In this case, the receiver hosts attached to that port can join
IPv6 multicast groups again before the number of IPv6 multicast groups on the
port reaches the limit. When the number of IPv6 multicast groups on the port reaches
the limit, the port automatically drops MLD reports for new IPv6 multicast
groups.

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

**3\.**Set the maximum number of IPv6 multicast
groups on the port.

mld-snooping group-limit limit \[ vlan vlan-list ]

By default, no limit is placed on the
maximum number of IPv6 multicast groups on a port.

### Enabling IPv6 multicast group replacement

#### About this task

When IPv6 multicast group replacement is
enabled, the port does not drop MLD reports for new groups if the number of
multicast groups on the port reaches the upper limit. Instead, the port leaves
an IPv6 multicast group that has the lowest IPv6 address and joins the new
group contained in the MLD report. The IPv6 multicast group replacement feature
is typically used in the channel switching application.

#### Restrictions and guidelines

This feature takes effect only on the
multicast groups that the port joins dynamically. 

This feature does not take effect if the
following conditions exist:

·     The number of the MLD snooping forwarding
entries on the device reaches the upper limit.

·     The IPv6 multicast group that the port newly
joins is not included in the multicast group list maintained by the device.

#### Enabling IPv6 multicast group replacement globally

**1\.**Enter system view.

system-view

**2\.**Enter MLD-snooping view.

mld-snooping

**3\.**Enable IPv6 multicast group replacement
globally.

overflow-replace \[ vlan vlan-list ]

By default, IPv6 multicast group
replacement is disabled globally.

#### Enabling IPv6 multicast group replacement on a port

**1\.**Enter system view.

system-view

**2\.**Enter Layer 2 interface view.

¡     Enter
Layer 2 Ethernet interface view.

interface interface-type interface-number

¡     Enter
Layer 2 aggregate interface view.

interface bridge-aggregation interface-number

**3\.**Enable IPv6 multicast group replacement on
the port.

mld-snooping overflow-replace \[ vlan vlan-list ]

By default, IPv6 multicast group
replacement is disabled on a port.

### Enabling host tracking

#### About this task

This feature enables the Layer 2 device to
record information about member hosts that are receiving IPv6 multicast data. The
information includes IPv6 addresses of the hosts, length of time elapsed since
the hosts joined IPv6 multicast groups, and remaining timeout time for the
hosts. This feature facilitates monitoring and managing member hosts.

#### Restrictions and guidelines

The configuration made in MLD-snooping view
does not take effect on VSIs.

#### Enabling host tracking globally

**1\.**Enter system view.

system-view

**2\.**Enter MLD-snooping view.

mld-snooping

**3\.**Enable host tracking globally.

host-tracking

By default, host tracking is disabled globally.

#### Enabling host tracking in a VLAN

**1\.**Enter system view.

system-view

**2\.**Enter VLAN view.

vlan vlan-id

**3\.**Enable host tracking for the VLAN.

mld-snooping host-tracking

By default, host tracking is disabled in
a VLAN.

## Setting the DSCP value for outgoing MLD protocol packets

#### About this task

The DSCP value determines the packet transmission
priority. A greater DSCP value represents a higher priority.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter MLD snooping view.

mld-snooping

**3\.**Set the DSCP value for outgoing MLD protocol
packets.

dscp dscp-value

By default, the DSCP value is 48 for
outgoing MLD protocol packets.

## Display and maintenance commands for MLD snooping

Execute display commands
in any view and reset commands in user
view.

 

| Task | Command |
| --- | --- |
| Display Layer 2 IPv6 multicast fast forwarding entries. | display ipv6 l2-multicast fast-forwarding cache \[ vlan vlan-id ] \[ ipv6\-source-address \| ipv6-group-address ] \* \[ slot slot-number ] || Display information about Layer 2 IPv6 multicast groups. | display ipv6 l2-multicast ip \[ group ipv6-group-address \| source ipv6-source-address ] \* \[ vlan vlan-id \| vsi vsi-name ] \[ slot slot-number ] || Display Layer 2 IPv6 multicast group entries. | display ipv6 l2-multicast ip forwarding \[ group ipv6-group-address \| source ipv6-source-address ] \* \[ vlan vlan-id ] \[ slot slot-number ] || Display information about Layer 2 IPv6 MAC multicast groups. | display ipv6 l2-multicast mac \[ mac-address ] \[ vlan vlan-id \| vsi vsi-name ] \[ slot slot-number ] || Display Layer 2 IPv6 MAC multicast group entries. | display ipv6 l2-multicast mac forwarding \[ mac-address ] \[ vlan vlan-id ] \[ slot slot-number ] || Display static IPv6 multicast MAC address entries. | display mac-address \[ mac-address \[ vlan vlan-id ] \| \[ multicast ] \[ vlan vlan-id ] \[ count ] ] || Display MLD snooping status. | display mld-snooping \[ global \| vlan vlan-id \| vsi vsi-name ] || Display dynamic MLD snooping group entries. | display mld-snoopinggroup \[ ipv6-group-address \| ipv6-source-address ] \* \[ vlan vlan-id \| vsi vsi-name ] \[ interface interface-type interface-number \| \[ verbose ] \[ slot slot-number ] ] || Display host tracking information. | display mld-snooping host-tracking vlan vlan-id group ipv6-group-address \[ source ipv6-source-address ] \[ slot slot-number ] || Display dynamic router port information. | display mld-snooping router-port \[ vlan vlan-id \| vsi vsi-name ] \[ verbose ] \[ slot slot-number ] || Display static MLD snooping group entries. | display mld-snooping static\-group \[ ipv6-group-address \| ipv6-source-address ] \* \[ vlan vlan-id ] \[ verbose ] \[ slot slot-number ] || Display static router port information. | display mld-snooping static-router-port \[ vlan vlan-id ] \[ verbose ] \[ slot slot-number ] || Display statistics for the MLD messages and IPv6 PIM hello messages learned through MLD snooping. | display mld-snooping statistics || Clear Layer 2 IPv6 multicast fast forwarding entries. | reset ipv6 l2-multicast fast-forwarding cache \[ vlan vlan-id ] { { ipv6-source-address \| ipv6-group-address } \* \| all } \[ slot slot-number ] || Clear dynamic MLD snooping group entries. | reset mld-snooping group { ipv6-group-address \[ ipv6-source-address ] \| all } \[ vlan vlan-id \| vsi vsi-name ] || Clear dynamic router port information. | reset mld-snooping router-port { all \| vlan vlan-id \| vsi vsi-name } || Clear statistics for MLD messages and IPv6 PIM hello messages learned through MLD snooping. | reset mld-snooping statistics |

















 

## MLD snooping configuration examples

### Example: Configuring VLAN-based IPv6group policy and simulated joining

#### Network configuration

As shown in [Figure 4](#_Ref147392491),
Router A runs MLDv1 and acts as the MLD querier, and Switch A runs MLDv1
snooping.

Configure the group policy and simulate
joining to meet the following requirements:

·     Host A and Host B receive only the IPv6
multicast data addressed to the IPv6 multicast group FF1E::101. IPv6 multicast
data can be forwarded through Ten-GigabitEthernet 1/0/3 and Ten-GigabitEthernet 1/0/4 of Switch A
uninterruptedly, even though Host A and Host B fail to receive the multicast
data.

·     Switch A will drop unknown IPv6 multicast data
instead of flooding it in VLAN 100\.

Figure 4 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705013_x_Img_x_png_3_2216038_294551_0.png)

‌

#### Prerequisites

Assign an IPv6 address and prefix length to
each interface, as shown in [Figure 4](#_Ref147392491). 

#### Procedure

**1\.**Configure Router A:

\# Enable IPv6 multicast routing.

\<RouterA\> system-view

\[RouterA] ipv6 multicast routing

\[RouterA-mrib6] quit

\# Enable MLD on Ten-GigabitEthernet 1/0/1.

\[RouterA] interface ten-gigabitethernet 1/0/1

\[RouterA-Ten-GigabitEthernet1/0/1] mld enable

\[RouterA-Ten-GigabitEthernet1/0/1] quit

\# Enable IPv6 PIM-DM on Ten-GigabitEthernet 1/0/2.

\[RouterA] interface ten-gigabitethernet 1/0/2

\[RouterA-Ten-GigabitEthernet1/0/2] ipv6 pim dm

\[RouterA-Ten-GigabitEthernet1/0/2] quit

**2\.**Configure Switch A:

\# Enable the MLD snooping feature.

\<SwitchA\> system-view

\[SwitchA] mld-snooping

\[SwitchA-mld-snooping] quit

\# Create VLAN 100, and assign Ten-GigabitEthernet 1/0/1 through Ten-GigabitEthernet 1/0/4 to the VLAN.

\[SwitchA] vlan 100

\[SwitchA-vlan100] port ten-gigabitethernet 1/0/1 to ten-gigabitethernet 1/0/4

\# Enable MLD snooping, and enable dropping
IPv6 unknown multicast data for VLAN 100\. 

\[SwitchA-vlan100] mld-snooping
enable

\[SwitchA-vlan100] mld-snooping
drop-unknown

\[SwitchA-vlan100] quit

\# Configure an IPv6 multicast group policy
so that hosts in VLAN 100 can join only IPv6 multicast group FF1E::101.

\[SwitchA] acl ipv6 basic 2001

\[SwitchA-acl-ipv6-basic-2001] rule
permit source ff1e::101 128

\[SwitchA-acl-ipv6-basic-2001]
quit

\[SwitchA] mld-snooping

\[SwitchA–mld-snooping]
group-policy 2001 vlan 100

\[SwitchA–mld-snooping] quit

\# Configure Ten-GigabitEthernet 1/0/3 and Ten-GigabitEthernet 1/0/4 as simulated member
hosts to join IPv6 multicast group FF1E::101.

\[SwitchA] interface ten-gigabitethernet 1/0/3

\[SwitchA-Ten-GigabitEthernet1/0/3] mld-snooping
host-join ff1e::101 vlan 100

\[SwitchA-Ten-GigabitEthernet1/0/3] quit

\[SwitchA] interface ten-gigabitethernet 1/0/4

\[SwitchA-Ten-GigabitEthernet1/0/4] mld-snooping
host-join ff1e::101 vlan 100

\[SwitchA-Ten-GigabitEthernet1/0/4] quit

#### Verifying the configuration

\# Send MLD reports from Host A and Host B
to join IPv6 multicast groups FF1E::101 and FF1E::202. (Details not shown.)

\# Display dynamic MLD snooping group entries
for VLAN 100 on Switch A.

\[SwitchA] display mld-snooping group
vlan 100

Total 1 entries.

 

VLAN 100: Total 1 entries.

  (::, FF1E::101)

    Host ports (2 in total):

      XGE1/0/3                              (00:03:23)

      XGE1/0/4                              (00:04:10)

The output shows the following information:

·     Host A and Host B have joined IPv6 multicast
group FF1E::101 through the member ports Ten-GigabitEthernet 1/0/4 and Ten-GigabitEthernet 1/0/3 on Switch A,
respectively.

·     Host A and Host B have failed to join the
multicast group FF1E::202.

### Example: Configuring VLAN-based static ports

#### Network configuration

As shown in [Figure 5](#_Ref365893835):

·     Router A runs MLDv1 and acts as the MLD querier.
Switch A, Switch B, and Switch C run MLDv1 snooping.

·     Host A and Host C are permanent receivers of IPv6
multicast group FF1E::101.

Configure static ports to meet the
following requirements:

·     To enhance the reliability of IPv6 multicast
traffic transmission, configure Ten-GigabitEthernet 1/0/3 and Ten-GigabitEthernet 1/0/5 on Switch C as
static member ports for IPv6 multicast group FF1E::101.

·     Suppose the STP runs on the network. To avoid
data loops, the forwarding path from Switch A to Switch C is blocked. IPv6 multicast
data flows to the receivers attached to Switch C only along the path of Switch
A—Switch B—Switch C. When this path is blocked, a minimum of one MLD query-response
cycle must be completed before IPv6 multicast data flows to the receivers along
the path of Switch A—Switch C. In this case, the multicast delivery is interrupted
during the process. For more information about the STP, see Layer 2—LAN Switching Configuration Guide. 

Configure Ten-GigabitEthernet 1/0/3 on Switch A as a
static router port. Then, IPv6 multicast data can flow to the receivers nearly
uninterrupted along the path of Switch A—Switch C when the path of Switch
A—Switch B—Switch C is blocked.

Figure 5 Network
diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705014_x_Img_x_png_4_2216038_294551_0.png)

‌

#### Prerequisites

Assign an IPv6 address and prefix length to
each interface, as shown in [Figure 5](#_Ref365893835).

#### Procedure

**1\.**Configure Router A:

\# Enable IPv6 multicast routing.

\<RouterA\> system-view

\[RouterA] ipv6 multicast routing

\[RouterA-mrib6] quit

\# Enable MLD on Ten-GigabitEthernet 1/0/1.

\[RouterA] interface ten-gigabitethernet 1/0/1

\[RouterA-Ten-GigabitEthernet1/0/1] mld enable

\[RouterA-Ten-GigabitEthernet1/0/1] quit

\# Enable IPv6 PIM-DM on Ten-GigabitEthernet 1/0/2.

\[RouterA] interface ten-gigabitethernet 1/0/2

\[RouterA-Ten-GigabitEthernet1/0/2] ipv6 pim dm

\[RouterA-Ten-GigabitEthernet1/0/2] quit

**2\.**Configure Switch A:

\# Enable the MLD snooping feature.

\<SwitchA\> system-view

\[SwitchA] mld-snooping

\[SwitchA-mld-snooping] quit

\# Create VLAN 100, and assign Ten-GigabitEthernet 1/0/1 through Ten-GigabitEthernet 1/0/3 to the VLAN.

\[SwitchA] vlan 100

\[SwitchA-vlan100] port ten-gigabitethernet 1/0/1 to ten-gigabitethernet 1/0/3

\# Enable MLD snooping for VLAN 100\.

\[SwitchA-vlan100] mld-snooping
enable

\[SwitchA-vlan100] quit

\# Configure Ten-GigabitEthernet 1/0/3 as a static router
port.

\[SwitchA] interface ten-gigabitethernet 1/0/3

\[SwitchA-Ten-GigabitEthernet1/0/3] mld-snooping static-router-port
vlan 100

\[SwitchA-Ten-GigabitEthernet1/0/3] quit

**3\.**Configure Switch B:

\# Enable the MLD snooping feature.

\<SwitchB\> system-view

\[SwitchB] mld-snooping

\[SwitchB-mld-snooping] quit

\# Create VLAN 100, and assign Ten-GigabitEthernet 1/0/1 and Ten-GigabitEthernet 1/0/2 to the VLAN.

\[SwitchB] vlan 100

\[SwitchB-vlan100] port ten-gigabitethernet 1/0/1 ten-gigabitethernet 1/0/2

\# Enable MLD snooping for VLAN 100\.

\[SwitchB-vlan100] mld-snooping
enable

\[SwitchB-vlan100] quit

**4\.**Configure Switch C:

\# Enable the MLD snooping feature.

\<SwitchC\> system-view

\[SwitchC] mld-snooping

\[SwitchC-mld-snooping] quit

\# Create VLAN 100, and assign Ten-GigabitEthernet 1/0/1 through Ten-GigabitEthernet 1/0/5 to the VLAN.

\[SwitchC] vlan 100

\[SwitchC-vlan100] port ten-gigabitethernet 1/0/1 to ten-gigabitethernet 1/0/5

\# Enable MLD snooping for VLAN 100\.

\[SwitchC-vlan100] mld-snooping
enable

\[SwitchC-vlan100] quit

\# Configure Ten-GigabitEthernet 1/0/3 and Ten-GigabitEthernet 1/0/5 as static member
ports for IPv6 multicast group FF1E::101.

\[SwitchC] interface ten-gigabitethernet 1/0/3

\[SwitchC-Ten-GigabitEthernet1/0/3] mld-snooping
static-group ff1e::101 vlan 100

\[SwitchC-Ten-GigabitEthernet1/0/3] quit

\[SwitchC] interface ten-gigabitethernet 1/0/5

\[SwitchC-Ten-GigabitEthernet1/0/5] mld-snooping
static-group ff1e::101 vlan 100

\[SwitchC-Ten-GigabitEthernet1/0/5] quit

#### Verifying the configuration

\# Display static router port information
for VLAN 100 on Switch A.

\[SwitchA] display mld-snooping static-router-port
vlan 100

VLAN 100:

  Router ports (1 in total):

    XGE1/0/3

The output shows thatTen-GigabitEthernet 1/0/3 on Switch A has
become a static router port.

\# Display static MLD snooping group entries
in VLAN 100 on Switch C.

\[SwitchC] display mld-snooping static-group
vlan 100

Total 1 entries).

 

VLAN 100: Total 1 entries).

  (::, FF1E::101)

    Host ports (2 in total):

      XGE1/0/3

      XGE1/0/5

The output shows that Ten-GigabitEthernet 1/0/3 and Ten-GigabitEthernet 1/0/5 on Switch C have
become static member ports of IPv6 multicast group FF1E::101.

### Example: Configuring the VLAN-based MLD snooping querier

#### Network configuration

As shown in [Figure 6](#_Ref365106354):

·     The network is a Layer 2-only network.

·     Source 1 and Source 2 send multicast data to IPv6
multicast groups FF1E::101 and FF1E::102, respectively.

·     Host A and Host C are receivers of IPv6 multicast
group FF1E::101, and Host B and Host D are receivers of IPv6 multicast group FF1E::102.

·     All host receivers run MLDv1 and all switches
run MLDv1 snooping. Switch A (which is close to the multicast sources) acts as
the MLD snooping querier.

To prevent the switches from flooding
unknown IPv6 packets in the VLAN, enable all the switches to drop unknown IPv6 multicast
packets.

Figure 6 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705015_x_Img_x_png_5_2216038_294551_0.png)

‌

#### Procedure

**1\.**Configure Switch A:

\# Enable the MLD snooping feature.

\<SwitchA\> system-view

\[SwitchA] mld-snooping

\[SwitchA-mld-snooping] quit

\# Create VLAN 100, and assign Ten-GigabitEthernet 1/0/1 through Ten-GigabitEthernet 1/0/3 to the VLAN.

\[SwitchA] vlan 100

\[SwitchA-vlan100] port ten-gigabitethernet 1/0/1 to ten-gigabitethernet 1/0/3

\# Enable MLD snooping, and enable dropping
unknown IPv6 multicast data for VLAN 100\.

\[SwitchA-vlan100] mld-snooping
enable

\[SwitchA-vlan100] mld-snooping
drop-unknown

\# Configure Switch A as the MLD snooping querier.

\[SwitchA-vlan100] MLD-snooping
querier

\[SwitchA-vlan100] quit

**2\.**Configure Switch B:

\# Enable the MLD snooping feature.

\<SwitchB\> system-view

\[SwitchB] mld-snooping

\[SwitchB-mld-snooping] quit

\# Create VLAN 100, and assign Ten-GigabitEthernet 1/0/1 through Ten-GigabitEthernet 1/0/4 to the VLAN.

\[SwitchB] vlan 100

\[SwitchB-vlan100] port ten-gigabitethernet 1/0/1 to ten-gigabitethernet 1/0/4

\# Enable MLD snooping, and enable dropping
unknown IPv6 multicast data for VLAN 100\.

\[SwitchB-vlan100] mld-snooping
enable

\[SwitchB-vlan100] mld-snooping
drop-unknown

\[SwitchB-vlan100] quit

**3\.**Configure Switch C:

\# Enable the MLD snooping feature.

\<SwitchC\> system-view

\[SwitchC] mld-snooping

\[SwitchC-mld-snooping] quit

\# Create VLAN 100, and assign Ten-GigabitEthernet 1/0/1 through Ten-GigabitEthernet 1/0/3 to the VLAN.

\[SwitchC] vlan 100

\[SwitchC-vlan100] port ten-gigabitethernet 1/0/1 to ten-gigabitethernet 1/0/3

\# Enable MLD snooping, and enable dropping
unknown IPv6 multicast data for VLAN 100\.

\[SwitchC-vlan100] mld-snooping
enable

\[SwitchC-vlan100] mld-snooping
drop-unknown

\[SwitchC-vlan100] quit

**4\.**Configure Switch D:

\# Enable the MLD snooping feature.

\<SwitchD\> system-view

\[SwitchD] mld-snooping

\[SwitchD-mld-snooping] quit

\# Create VLAN 100, and assign Ten-GigabitEthernet 1/0/1 and Ten-GigabitEthernet 1/0/2 to the VLAN.

\[SwitchD] vlan 100

\[SwitchD-vlan100] port ten-gigabitethernet 1/0/1 to ten-gigabitethernet 1/0/2

\# Enable MLD snooping, and enable dropping
unknown IPv6 multicast data for VLAN 100\.

\[SwitchD-vlan100] mld-snooping
enable

\[SwitchD-vlan100] mld-snooping
drop-unknown

\[SwitchD-vlan100] quit

#### Verifying the configuration

\# Display statistics for MLD messages and
IPv6 PIM hello messages learned through MLD snooping on Switch B.

\[SwitchB] display mld-snooping
statistics

Received MLD messages:

  MLD general queries: 19

  MLDv1 specific queries: 0

  MLDv2 specific queries: 0

  MLDv2 specific sg queries: 0

  MLDv1 reports: 19

  MLDv2 reports: 0

  MLDv2 reports with right and wrong records: 0

  MLD dones: 5

  Error MLD messages: 1

Sent MLD messages:

  MLDv1 specific
queries: 0

  MLDv2
specific queries: 0

  MLDv2
specific sg queries: 0

Received IPv6 PIM hello: 0

The output shows that all switches except Switch
A can receive the MLD general queries after Switch A acts as the MLD snooping
querier.

### Example: Configuring VLAN-based MLD snooping proxying

#### Network configuration

As shown in [Figure 7](#_Ref206992127), Router
A runs MLDv1 and acts as the MLD querier. Switch A runs MLDv1 snooping. Configure
MLD snooping proxying so that Switch A can perform the following actions:

·     Forward MLD report and done messages to Router
A.

·     Respond to MLD queries sent by Router A and
forward the queries to downstream hosts.

Figure 7 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705016_x_Img_x_png_6_2216038_294551_0.png)

‌

#### Prerequisites

Assign an IPv6 address and subnet mask to
each interface, as shown in [Figure 7](#_Ref206992127).

#### Procedure

**1\.**Configure Router A:

\# Enable IPv6 multicast routing.

\<RouterA\> system-view

\[RouterA] ipv6 multicast
routing

\[RouterA-mrib6] quit

\# Enable MLD and IPv6 PIM-DM on Ten-GigabitEthernet 1/0/1.

\[RouterA] interface ten-gigabitethernet 1/0/1

\[RouterA-Ten-GigabitEthernet1/0/1] mld enable

\[RouterA-Ten-GigabitEthernet1/0/1] ipv6 pim dm

\[RouterA-Ten-GigabitEthernet1/0/1] quit

\# Enable IPv6 PIM-DM on Ten-GigabitEthernet 1/0/2.

\[RouterA] interface ten-gigabitethernet 1/0/2

\[RouterA-Ten-GigabitEthernet1/0/2] ipv6 pim dm

\[RouterA-Ten-GigabitEthernet1/0/2] quit

**2\.**Configure Switch A:

\# Enable the MLD snooping feature.

\<SwitchA\> system-view

\[SwitchA] mld-snooping

\[SwitchA-mld-snooping] quit

\# Create VLAN 100, and assign Ten-GigabitEthernet 1/0/1 through Ten-GigabitEthernet 1/0/4 to the VLAN.

\[SwitchA] vlan 100

\[SwitchA-vlan100] port ten-gigabitethernet 1/0/1 to ten-gigabitethernet 1/0/4

\# Enable MLD snooping and MLD snooping
proxying for the VLAN.

\[SwitchA-vlan100] mld-snooping
enable

\[SwitchA-vlan100] mld-snooping
proxy enable

\[SwitchA-vlan100] quit

#### Verifying the configuration

\# Send MLD reports from Host A and Host B
to join IPv6 multicast group FF1E::101. (Details not shown.)

\# Display MLD snooping group entries on
Switch A.

\[SwitchA] display mld-snooping group

 Total 1 entries.

 

VLAN 100: Total 1 entries.

  (::, FF1E::101)

    Host ports (2 in total):

      XGE1/0/3                                    
(00:04:09)

      XGE1/0/4                                     (00:03:06)

The output shows that Ten-GigabitEthernet 1/0/3 and Ten-GigabitEthernet 1/0/4 are member ports of
IPv6 multicast group FF1E::101. Host A and Host B will receive IPv6 multicast
data for the group.

\# Display MLD group membership information
on Router A.

\[RouterA] display mld group

MLD groups in total: 1

 Ten-GigabitEthernet1/0/1(2001::1):

  MLD groups reported in total: 1

   Group address: FF1E::101

    Last reporter:
FE80::2FF:FFFF:FE00:1

    Uptime: 00:00:31

    Expires: 00:03:48

\# Send an MLD done message from Host A to
leave IPv6 multicast group FF1E::101. (Details not shown.)

\# Display MLD snooping group entries on
Switch A.

\[SwitchA] display mld-snooping group

Total 1 entries.

 

VLAN 100: Total 1 entries.

  (::, FF1E::101)

    Host ports (1 in total):

      XGE1/0/3                                    
( 00:01:23 )

The output shows that Ten-GigabitEthernet 1/0/3 is the only member
port of IPv6 multicast group FF1E::101. Only Host B will receive IPv6 multicast
data for the group.

### Example: Configuring VSI-based MLD snooping

#### Network configuration

As shown in [Figure 8](#_Ref385527372):

·     There are three sites in VPN a. They are connected through the VPLS network.

·     In VLAN 100, Source in Site 1, Host A in Site 2,
and Host B in Site 3 communicate with CE 1, CE 2, and CE 3, respectively.

Configure the devices so that Host A and
Host B can receive IPv6 multicast data from Source. 

Figure 8 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705017_x_Img_x_png_7_2216038_294551_0.png)

‌

Table 1 Interface and IP address assignment

| Device | Interface | IP address | Device | Interface | IP address |
| --- | --- | --- | --- | --- | --- |
| PE 1 | Vlan-int101 | 10.110.1.1/24 | PE 2 | Loop0 | 2.2.2.2/32 || PE 1 | Vlan-int102 | 10.110.2.1/24 | PE 3 | Vlan-int102 | 10.110.2.2/24 || PE 1 | Loop0 | 1.1.1.1/32 | PE 3 | Vlan-int103 | 10.110.3.2/24 || PE 2 | Vlan-int101 | 10.110.1.2/24 | PE 3 | Loop0 | 3.3.3.3/32 || PE 2 | Vlan-int103 | 10.110.3.1/24 |  |  |  |





 

#### Prerequisites

Assign an IP address and subnet mask to
each interface on the VPLS network as shown in [Table 1](#_Ref385524623), and then configure OSPF
on the switches on the VPLS network.

#### Procedure

**1\.**Configure CE 1:

\# Enable the MLD snooping feature.

\<CE1\> system-view

\[CE1] mld-snooping

\[CE1-mld-snooping] quit

\# Create VLAN 100, and assign Ten-GigabitEthernet 1/0/2 to the VLAN.

\[CE1] vlan 100

\[CE1-vlan100] port ten-gigabitethernet 1/0/2

\# In VLAN 100, enable MLD snooping, and
enable dropping unknown IPv6 multicast data.

\[CE1-vlan100] mld-snooping
enable

\[CE1-vlan100] mld-snooping
drop-unknown

\# Configure Ten-GigabitEthernet 1/0/1 as a trunk port,
and assign it to VLAN 100\.

\[CE1] interface ten-gigabitethernet 1/0/1

\[CE1-Ten-GigabitEthernet1/0/1] port link-type
trunk

\[CE1-Ten-GigabitEthernet1/0/1] port trunk permit
vlan 100

\[CE1-Ten-GigabitEthernet1/0/1] quit

\# Enable the MLD snooping querier in VLAN
100\.

\[CE1-vlan100] mld-snooping
querier

**2\.**Configure CE 2:

\# Enable the MLD snooping feature.

\<CE2\> system-view

\[CE2] mld-snooping

\[CE2-mld-snooping] quit

\# Create VLAN 100, and assign Ten-GigabitEthernet 1/0/2 to VLAN 100\.

\[CE2] vlan 100

\[CE2-vlan100] port ten-gigabitethernet 1/0/2

\# In VLAN 100, enable MLD snooping, and
enable dropping unknown IPv6 multicast data.

\[CE2-vlan100] mld-snooping
enable

\[CE2-vlan100] mld-snooping
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

\# Enable the MLD snooping feature.

\<CE3\> system-view

\[CE3] mld-snooping

\[CE3-mld-snooping] quit

\# Create VLAN 100, and assign Ten-GigabitEthernet 1/0/2 to VLAN 100\.

\[CE3] vlan 100

\[CE3-vlan100] port ten-gigabitethernet 1/0/2

\# In VLAN 100, enable MLD snooping, and enable
dropping unknown IPv6 multicast data.

\[CE3-vlan100] mld-snooping
enable

\[CE3-vlan100] mld-snooping
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

\# Enable MPLS and LDP on VLAN-interface 101
and VLAN-interface 102\.

\[PE1] interface vlan-interface
101

\[PE1-Vlan-interface101] mpls
enable

\[PE1-Vlan-interface101] mpls
ldp enable

\[PE1-Vlan-interface101] quit

\[PE1] interface vlan-interface
102

\[PE1-Vlan-interface102] mpls
enable

\[PE1-Vlan-interface102] mpls
ldp enable

\[PE1-Vlan-interface102] quit

\# Create a VSI named aaa, specify the
VSI to establish PWs statically, and configure PWs for it.

\[PE1] vsi aaa

\[PE1-vsi-aaa] pwsignaling
static

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

\# Enable the MLD snooping feature.

\[PE1] mld-snooping

\[PE1-mld-snooping] quit

\# Enable MLD snooping, and enable
dropping unknown IPv6 multicast data for VSI aaa.

\[PE1] vsi aaa

\[PE1-vsi-aaa] mld-snooping
enable

\[PE1-vsi-aaa] mld-snooping
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

\# Enable MPLS and LDP on VLAN-interface 101
and VLAN-interface 103\.

\[PE2] interface vlan-interface
101

\[PE2-Vlan-interface101] mpls
enable

\[PE2-Vlan-interface101] mpls
ldp enable

\[PE2-Vlan-interface101] quit

\[PE2] interface vlan-interface
103

\[PE2-Vlan-interface103] mpls
enable

\[PE2-Vlan-interface103] mpls
ldp enable

\[PE2-Vlan-interface103] quit

\# Create a VSI named aaa, specify the
VSI to establish PWs statically, and configure PWs for it.

\[PE2] vsi aaa

\[PE2-vsi-aaa] pwsignaling
static

\[PE2-vsi-aaa-static] peer
1.1.1.1 pw-id 3 in-label 100 out-label 100

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

\# Enable the MLD snooping feature.

\[PE2] mld-snooping

\[PE2-mld-snooping] quit

\# Enable MLD snooping, and enable
dropping unknown IPv6 multicast data for VSI aaa.

\[PE2] vsi aaa

\[PE2-vsi-aaa] mld-snooping
enable

\[PE2-vsi-aaa] mld-snooping
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

\# Enable MPLS and LDP on VLAN-interface 102
and VLAN-interface 103\.

\[PE3] interface vlan-interface
102

\[PE3-Vlan-interface102] mpls
enable

\[PE3-Vlan-interface102] mpls
ldp enable

\[PE3-Vlan-interface102] quit

\[PE3] interface vlan-interface
103

\[PE3-Vlan-interface103] mpls
enable

\[PE3-Vlan-interface103] mpls
ldp enable

\[PE3-Vlan-interface103] quit

\# Create a VSI named aaa, specify the
VSI to establish PWs statically, and configure PWs for it.

\[PE3] vsi aaa

\[PE3-vsi-aaa] pwsignaling
static

\[PE3-vsi-aaa-static] peer
1.1.1.1 pw-id 3 in-label 200 out-label 200

\[PE3-vsi-aaa-static-1.1.1.1-3]
quit

\[PE3-vsi-aaa-static] peer
2.2.2.2 pw-id 3 in-label 300 out-label 300

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

\# Enable the MLD snooping feature.

\[PE3] mld-snooping

\[PE3-mld-snooping] quit

\# Enable MLD snooping, and enable
dropping unknown IPv6 multicast data for VSI aaa.

\[PE3] vsi aaa

\[PE3-vsi-aaa] mld-snooping
enable

\[PE3-vsi-aaa] mld-snooping
drop-unknown

\[PE3-vsi-aaa] quit

#### Verifying the configuration

\# Display detailed information about
dynamic MLD snooping group entries for VSI aaa on
PE 1\. 

\[PE1] display mld-snooping group vsi
aaa verbose

Total 1 entries.

 

VSI aaa: Total 1 entries.

  (::, FF1E::101)

    Attribute: global port

    FSM information: dummy

    Host slots (0 in total):

    Host ports (1 in total):

      NPW (Link ID 9\)                         (00:02:24)

        VLAN pairs (1 in total):

          Outer VLAN 5     Inner VLAN
0       (00:02:24)

\# Display detailed information about
dynamic router ports for VSI aaa on PE 1\.

\[PE1] display mld-snooping
router-port vsi aaa verbose

VSI aaa:

  Router slots (0 in total):

  Router ports (1 in total):

    XGE1/0/1 ( Link ID 0\)                      (00:01:46)

      VLAN pairs (1 in total):

        Outer VLAN 100     Inner VLAN
0       (00:01:46)

\# Display detailed information about
dynamic MLD snooping group entries for VSI aaa on
PE 2\.

\[PE2] display mld-snooping group vsi
aaa verbose

Total 1 entries.

 

VSI aaa: Total 1 entries.

  (::, FF1E::101)

    Attribute: global port

    FSM information: dummy

    Host slots (0 in total):

    Host ports (1 in total):

      NPW ( Link ID 8\)                        (00:02:07)

        VLAN pairs (1 in total):

          Outer VLAN 100     Inner
VLAN 0          (00:02:07)

\# Display detailed information about
dynamic router ports for VSI aaa on PE 2\.

\[PE2] display mld-snooping
router-port vsi aaa verbose

VSI aaa:

  Router slots (0 in total):

  Router ports (1 in total):

    NPW ( Link ID 8\)                          (00:01:26)

      VLAN pairs (1 in total):

        Outer VLAN 100     Inner VLAN
0       (00:01:26)

\# Display detailed information about
dynamic MLD snooping group entries for VSI aaa on
PE 3\.

\[PE3] display
mld-snooping group vsi aaa verbose

Total 1 entries.

 

VSI aaa: Total 1 entries.

  (::, FF1E::101)

    Attribute: global port

    FSM information: dummy

    Host slots (0 in total):

    Host ports (1 in total):

      XGE1/0/1 (Link ID 0\)                     (00:02:04)

        VLAN pairs (1 in total):

          Outer VLAN 100     Inner
VLAN 0     (00:02:04)

\# Display detailed information about
dynamic router ports for VSI aaa on PE 3\.

\[PE3] display mld-snooping
router-port vsi aaa verbose

VSI aaa:

  Router slots (0 in total):

  Router ports (1 in total):

    NPW (Link ID 9\)                           (00:01:24)

      VLAN pairs (1 in total):

        Outer VLAN 100     Inner VLAN
0       (00:01:24)

### Example: Configuring VXLAN-based MLD snooping

#### Network configuration

As shown in [Figure 9](#_Ref435434447):

·     VXLAN 10 provides Layer 2 connectivity for VM 1,
VM 2, and VM 3 across the network sites.

·     VXLAN 10 uses the unicast mode for flood
traffic.

Configure MLD snooping on the switches to
implement IPv6 Layer 2 multicast forwarding and reduce the burden of
replicating known multicast traffic for VTEPs in VXLAN 10\.

Figure 9 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705018_x_Img_x_png_8_2216038_294551_0.png)

‌

Table 2 Interface and IP address assignment

| Device | Interface | IP address | Device | Interface | IP address |
| --- | --- | --- | --- | --- | --- |
| Switch A | Vlan-int11 | 11.1.1.1/24 | Switch C | Vlan-int13 | 13.1.1.3/24 || Switch A | Loop0 | 1.1.1.1/32 | Switch C | Loop0 | 3.3.3.3/32 || Switch B | Vlan-int12 | 12.1.1.2/24 | Switch D | Vlan-int11 | 11.1.1.4/24 || Switch B | Loop0 | 2.2.2.2/32 | Switch D | Vlan-int12 | 12.1.1.4/24 ||  |  |  | Switch D | Vlan-int13 | 13.1.1.4/24 |





 

#### Prerequisites

Assign an IP address and subnet mask to
each interface on the transport network as shown in [Table 2](#_Ref435432300), and configure
OSPF on all the switches in the transport network.

#### Procedure

**1\.**Configure Switch A:

\# Enable L2VPN.

\<SwitchA\> system-view

\[SwitchA] l2vpn enable

\# Enable Layer 2 forwarding for VXLANs.

\[SwitchA] undo vxlan
ip-forwarding

\# Create a VSI named vpna, and create VXLAN 10\.

\[SwitchA] vsi vpna

\[SwitchA-vsi-vpna] vxlan 10

\[SwitchA-vsi-vpna-vxlan-10]
quit

\[SwitchA-vsi-vpna] quit

\# Enable the MLD snooping feature.

\[SwitchA] mld-snooping

\[SwitchA -mld-snooping] quit

\# Enable MLD snooping and dropping unknown
IPv6 multicast data packets for VSI vpna.

\[SwitchA] vsi vpna

\[SwitchA-vsi-vpna]
mld-snooping enable

\[SwitchA-vsi-vpna]
mld-snooping drop-unknown

\[SwitchA-vsi-vpna] quit

\# Assign an IP address to Loopback 0\.
This address will be used as the source address of the VXLAN tunnels to Switch
B and Switch C.

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

\[SwitchA-Ten-GigabitEthernet1/0/1-srv1000] encapsulation
s-vid 2

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

\# Create a VSI named vpna, and create VXLAN 10\.

\[SwitchB] vsi vpna

\[SwitchB-vsi-vpna] vxlan 10

\[SwitchB-vsi-vpna-vxlan-10]
quit

\[SwitchB-vsi-vpna] quit

\# Enable the MLD snooping feature.

\[SwitchB] mld-snooping

\[SwitchB -mld-snooping] quit

\# Enable MLD snooping and dropping unknown
IPv6 multicast data packets for VSI vpna.

\[SwitchB] vsi vpna

\[SwitchB-vsi-vpna]
mld-snooping enable

\[SwitchB-vsi-vpna]
mld-snooping drop-unknown

\[SwitchB-vsi-vpna] quit

\# Assign an IP address to Loopback 0\.
This address will be used as the source address of the VXLAN tunnels to Switch
A and Switch C.

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

\[SwitchB-vsi-vpna-vxlan-10] quit

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

\# Create a VSI named vpna, and create VXLAN 10\.

\[SwitchC] vsi vpna

\[SwitchC-vsi-vpna] vxlan 10

\[SwitchC-vsi-vpna-vxlan-10]
quit

\[SwitchC-vsi-vpna] quit

\# Enable the MLD snooping feature.

\[SwitchB] mld-snooping

\[SwitchB -mld-snooping] quit

\# Enable MLD snooping for VSI vpna.

\[SwitchC] vsi vpna

\[SwitchC-vsi-vpna]
mld-snooping enable

\# Enable dropping unknown IPv6 multicast
data packets for VSI vpna.

\[SwitchC-vsi-vpna]
mld-snooping drop-unknown

\# Specify FE80::3 as the source IPv6
address of MLD general queries in VSI vpna.

\[SwitchC-vsi-vpna] mld-snooping
general-query source-ip fe80::3

\# Enable the MLD snooping querier for VSI
vpna.

\[SwitchC-vsi-vpna]
mld-snooping querier

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

\# Assign Tunnel 1 and Tunnel 3 to VXLAN
10\.

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
service instance 1000 to map VLAN 2\.

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

\# Display dynamic router port information
for VSI vpna on Switch A.

\[SwitchA] display mld-snooping
router-port vsi vpna

VSI vpna:

  Router ports (1 in total):

    Tun2 (VXLAN ID 10\)                        (00:04:17)

\# Display dynamic router port information
for VSI vpna on Switch B.

\[SwitchB] display mld-snooping
router-port vsi vpna

VSI vpna:

  Router ports (1 in total):

   Tun3 (VXLAN ID 10\)                        (00:04:07)

## Troubleshooting MLD snooping

### Layer 2 multicast forwarding cannot function

#### Symptom

Layer 2 multicast forwarding cannot
function through MLD snooping.

#### Solution

To resolve the problem:

**1\.**Use the display mld-snooping command to display MLD snooping status.

**2\.**If MLD snooping is not enabled, use the mld-snooping command in system view to enable the MLD snooping feature. Then,
use the mld-snooping enable command in VLAN view
or VSI view to enable MLD snooping for the VLAN or VSI.

**3\.**If MLD snooping is enabled globally but not enabled
for the VLAN or VSI, use the mld-snooping enable command in VLAN view or VSI view to enable MLD snooping for the
VLAN or VSI.

**4\.**If the problem persists, contact H3C Support.

### IPv6multicast group policy does not work

#### Symptom

Hosts can receive IPv6 multicast data for IPv6
multicast groups that are not permitted by the IPv6 multicast group policy.

#### Solution

To resolve the problem:

**1\.**Use the display acl ipv6 command to verify that the configured IPv6 ACL meets the IPv6 multicast
group policy requirements.

**2\.**Use the display this command in MLD-snooping view or in interface view to verify that the
correct IPv6 multicast group policy has been applied. If the applied policy is
not correct, use the group-policy or mld-snooping
group-policy command to apply the correct IPv6
multicast group policy.

**3\.**Use the display mld-snooping command to verify that dropping unknown IPv6 multicast data is
enabled. If it is not, use the mld-snooping drop-unknown command to enable dropping unknown IPv6 multicast data.

**4\.**If the problem persists, contact H3C Support.


