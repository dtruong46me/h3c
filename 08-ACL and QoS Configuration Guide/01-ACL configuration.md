
# Configuring ACLs

## About ACLs

An access control list (ACL) is a set of
rules for identifying traffic based on criteria such as source IP address,
destination IP address, and port number. The rules are also called permit or
deny statements.

ACLs are primarily used for packet
filtering. You can also use ACLs in QoS, security, routing, and other modules
for identifying traffic. The packet drop or forwarding decisions depend on the modules
that use ACLs.

### Numbering and naming ACLs

When creating an ACL, you must assign it a
number or name for identification. You can specify an existing ACL by its
number or name. Each ACL type has a unique range of ACL numbers.

For basic or advanced ACLs with the same
number, you must use the ipv6 keyword to distinguish
them. For ACLs with the same name, you must use the ipv6 and mac keywords to distinguish
them.

### ACLtypes

| Type | ACL number | IP version | Match criteria |
| --- | --- | --- | --- |
| Basic ACLs | 2000 to 2999 | IPv4 | Source IPv4 address. || IPv6 | Source IPv6 address. || Advanced ACLs | 3000 to 3999 | IPv4 | Source IPv4 address, destination IPv4 address, packet priority, protocol number, and other Layer 3 and Layer 4 header fields. || IPv6 | Source IPv6 address, destination IPv6 address, packet priority, protocol number, and other Layer 3 and Layer 4 header fields. || Layer 2 ACLs | 4000 to 4999 | IPv4 and IPv6 | Layer 2 header fields, such as source and destination MAC addresses, 802.1p priority, and link layer protocol type. |





‌

### Match order

The rules in an ACL are sorted in a
specific order. When a packet matches a rule, the device stops the match process
and performs the action defined in the rule. If an ACL contains overlapping or
conflicting rules, the matching result and action to take depend on the rule
order. 

The following ACL match orders are
available:

·     config—Sorts ACL rules in ascending order of rule ID. A rule with a lower
ID is matched before a rule with a higher ID. If you use this method, check the
rules and their order carefully.

·     auto—Sorts ACL rules in depth-first order. Depth-first ordering makes
sure any subset of a rule is always matched before the rule. The following
table lists the sequence of tie breakers that depth-first ordering uses to sort
rules for each type of ACL.

Table 1 Sort ACL rules in depth-first order

| ACL type | Sequence of tie breakers |
| --- | --- |
| IPv4 basic ACL | **1\.**VPN instance.  **2\.**More 0s in the source IPv4 address wildcard (more 0s means a narrower IPv4 address range).  **3\.**Rule configured earlier. || IPv4 advanced ACL | 1\.     VPN instance.  **2\.**Specific protocol number.  **3\.**More 0s in the source IPv4 address wildcard mask.  **4\.**More 0s in the destination IPv4 address wildcard.  **5\.**Narrower TCP/UDP service port number range.  **6\.**Rule configured earlier. || IPv6 basic ACL | 1\.     VPN instance.  **2\.**Longer prefix for the source IPv6 address (a longer prefix means a narrower IPv6 address range).  **3\.**Rule configured earlier. || IPv6 advanced ACL | 1\.     VPN instance.  **2\.**Specific protocol number.  **3\.**Longer prefix for the source IPv6 address.  **4\.**Longer prefix for the destination IPv6 address.  **5\.**Narrower TCP/UDP service port number range.  **6\.**Rule configured earlier. || Layer 2 ACL | 1\.     More 1s in the source MAC address mask (more 1s means a smaller MAC address).  **2\.**More 1s in the destination MAC address mask.  **3\.**Rule configured earlier. |





‌

A wildcard mask, also called an inverse
mask, is a 32-bit binary number represented in dotted decimal notation. In
contrast to a network mask, the 0 bits in a wildcard mask represent "do
care" bits, and the 1 bits represent "don't care" bits. If the
"do care" bits in an IP address are identical to the "do
care" bits in an IP address criterion, the IP address matches the
criterion. All "don't care" bits are ignored. The 0s and 1s in a
wildcard mask can be noncontiguous. For example, 0.255.0.255 is a valid
wildcard mask.

### Comments and remarks for rules

An ACL can have multiple rules. To identify
rules, you can add a comment for each rule one by one, or add the same remark
for multiple rules.

·     For rules requiring different identification, you
can add a comment for a particular rule or add a different comment for each
rule.

·     For a range of rules requiring the same
identification, you can insert a remark before the start rule and a remark after
the end rule. This configuration mode eliminates the need for configuring a comment
for each rule one by one and improves configuration efficiency.

### Rule numbering

ACL rules can be manually numbered or
automatically numbered. This section describes how automatic ACL rule numbering
works.

#### Rule numbering step

If you do not assign an ID to the rule you
are creating, the system automatically assigns it a rule ID. The rule numbering
step sets the increment by which the system automatically numbers rules. For
example, the default ACL rule numbering step is 5\. If you do not assign IDs to
rules you are creating, they are automatically numbered 0, 5, 10, 15, and so
on. The wider the numbering step, the more rules you can insert between two
rules. 

By introducing a gap between rules rather
than contiguously numbering rules, you have the flexibility of inserting rules
in an ACL. This feature is important for a config-order ACL, where ACL rules
are matched in ascending order of rule ID. 

The rule numbering step sets the increment
by which the system numbers rules automatically. If you do not specify a rule
ID when creating an ACL rule, the system automatically assigns it a rule ID.
This rule ID is the nearest higher multiple of the numbering step to the
current highest rule ID, starting from the start rule ID. For example, if the
rule numbering step is 5 and the current highest rule ID is 12, the rule is
numbered 15\.

The wider the numbering step, the more
rules you can insert between two rules. Whenever the step or start rule ID
changes, the rules are renumbered, starting from the start rule ID. For
example, if there are five rules numbered 0, 5, 9, 10, and 15, changing the
step from 5 to 2 causes the rules to be renumbered 0, 2, 4, 6, and 8\.

#### Automatic rule numbering and renumbering

The ID automatically assigned to an ACL
rule takes the nearest higher multiple of the numbering step to the current
highest rule ID, starting with 0\.

For example, if the step is 5, and there
are five rules numbered 0, 5, 9, 10, and 12, the newly defined rule is numbered
15\. If the ACL does not contain a rule, the first rule is numbered 0\. 

Whenever the step changes, the rules are renumbered,
starting from 0\. For example, changing the step from 5 to 2 renumbers rules 5,
10, 13, and 15 as rules 0, 2, 4, and 6\. 

For an ACL of the match order auto, rules are sorted in depth-first order, and are
renumbered based on the match order. For example, rules are in the match order
of 0, 10, and 5\. Changing the numbering step to 2 renumbers rules 0, 10, and 5
(not 0, 5, and 10\) as rules 0, 2, 4\.

### Fragment filtering with ACLs

Traditional packet filtering matches only
first fragments of packets, and allows all subsequent non-first fragments to
pass through. Attackers can fabricate non-first fragments to attack networks. 

To avoid risks, the ACL feature is designed
as follows:

·     Filters all fragments by default, including
non-first fragments.

·     Allows for matching criteria modification for
efficiency. For example, you can configure the ACL to filter only non-first
fragments.

## Restrictions and guidelines: ACL configuration

·     If you create a numbered ACL, you can enter the
view of the ACL by using either of the following commands:

¡     acl \[ ipv6 ] number acl-number.

¡     acl { \[ ipv6 ] { advanced \| basic } \| mac } acl-number.

·     If you create a ACL by using the acl \[ ipv6 ] number acl-number name acl-name
command, you can enter the view of the ACL by using either of the following
commands:

¡     acl \[ ipv6 ] name acl-name (for
only basic ACLs and advanced ACLs). 

¡     acl \[ ipv6 ] number acl-number \[ name acl-name ].

¡     acl { \[ ipv6 ] { advanced \| basic } \| mac } name acl-name.

·     If you create a named ACL by using the acl { \[ ipv6 ] { advanced \| basic } \| mac } name acl-name command, you
can enter the view of the ACL by using either of the following commands:

¡     acl \[ ipv6 ] name acl-name (for
only basic ACLs and advanced ACLs). 

¡     acl { \[ ipv6 ] { advanced \| basic } \| mac } name acl-name.

## ACL tasks at a glance

To configure an ACL, perform the following
tasks:

·     Configure ACLs according to the characteristics
of the packets to be matched

¡     [Configuring a basic ACL](#_Ref261447461)

¡     [Configuring an advanced ACL](#_Ref261447463)

¡     [Configuring a Layer 2 ACL](#_Ref157853900)

·     (Optional.) [Copying an ACL](#_Ref261447331)

·     (Optional.) [Configuring packet filtering with ACLs](#_Ref458786494)

## Configuring a basic ACL

### About basic ACLs

Basic ACLs match packets based only on
source IP addresses.

Basic ACLs can be used in the following
scenarios:

·     To improve security when the device acts as an
FTP or TFTP server, you can use basic ACLs to allow only matching clients to
access the server. For more information, see FTP and TFTP configuration in Fundamentals Configuration Guide.

·     In a multicast scenario, you can use basic ACLs
in a multicast source policy to receive or forward only matching multicast
packets. For more information about multicast source policy, see PIM
configuration and IPv6 PIM configuration in IP Multicast
Configuration Guide.

·     To filter routes in a routing policy, you can
use basic ACLs to receive or send matching routes. For more information about
routing policy, see Layer 3—IP Routing Configuration
Guide.

·     To take different QoS actions on different
traffic types, you can use basic ACLs to classify traffic. For more information
about traffic classes, see "Configuring QoS." 

### Configuring an IPv4 basic ACL

#### Restrictions and guidelines

If an IPv4 basic ACL is used for QoS
traffic classification or packet filtering in a VXLAN network, the ACL matches
packets as follows:

·     On a VTEP, the ACL can only match the incoming
packets of Ethernet service instances.

·     On an intermediate transport device, the ACL can
match both incoming packets and outgoing packets.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Create an IPv4 basic ACL and enter its view.
Choose one option as needed:

¡     Create an IPv4 basic ACL by specifying an ACL number.

acl number acl-number \[ name acl-name ] \[ match-order { auto \| config } ]

¡     Create an IPv4 basic ACL by specifying the basic keyword.

acl basic { acl-number \| name acl-name } \[ match-order { auto \| config } ]

**3\.**(Optional.) Configure a description for the
IPv4 basic ACL.

description text

By default, an IPv4 basic ACL does not
have a description.

**4\.**(Optional.) Set the rule numbering step.

step step-value \[ start start-value
]

By default, the rule numbering step is 5
and the start rule ID is 0\.

**5\.**Create or edit a rule.

rule \[ rule-id ] { deny \| permit } \[ counting \| fragment \| \[ flow-logging
\| logging ] \| source { source-address source-wildcard \| any } \| time-range time-range-name \| vpn-instance
vpn-instance-name ] \*

The flow-logging or logging keyword takes effect only when the module (for example, packet
filtering) that uses the ACL supports logging.

**6\.**(Optional.) Add or edit a rule comment.

rule rule-id comment text

By default, no rule comment is
configured.

**7\.**(Optional.) Add a rule remark.

rule \[ rule-id ] remark text

By default, no rule remark is configured.

### Configuring an IPv6 basic ACL

#### Restrictions and guidelines

If an IPv6 basic ACL is used for QoS traffic
classification or packet filtering:

·     Do not specify the fragment keyword.

·     Do not specify the routing keyword if the ACL is for outbound application.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Create an IPv6 basic ACL view and enter its
view. Choose one option as needed:

¡     Create an IPv6 basic ACL by specifying an ACL number.

acl ipv6 number acl-number \[ name acl-name ] \[ match-order { auto \| config } ]

¡     Create an IPv6 basic ACL by specifying
the basic keyword.

acl ipv6 basic { acl-number \| name acl-name } \[ match-order { auto \| config } ]

**3\.**(Optional.) Configure a description for the
IPv6 basic ACL.

description text

By default, an IPv6 basic ACL does not
have a description.

**4\.**(Optional.) Set the rule numbering step.

step step-value \[ start start-value
]

By default, the rule numbering step is 5
and the start rule ID is 0\.

**5\.**Create or edit a rule.

rule \[ rule-id ] { deny \| permit } \[ counting \| fragment \| \[ flow-logging
\| logging ] \| routing \[ type routing-type ] \| source { source-address source-prefix \| source-address/source-prefix \| any } \| time-range time-range-name \| vpn-instance
vpn-instance-name ] \*

The flow-logging or logging keyword takes effect only when the module (for example, packet
filtering) that uses the ACL supports logging.

**6\.**(Optional.) Add or edit a rule comment.

rule rule-id comment text

By default, no rule comment is
configured.

**7\.**(Optional.) Add a rule remark.

rule \[ rule-id ] remark text

By default, no rule remark is configured.

## Configuring an advanced ACL

### About advanced ACLs

Advanced ACLs can be used in the
following scenarios:

·     When the device acts as an FTP or TFTP server,
you can use advanced ACLs to allow only matching clients to access the server.
For more information, see FTP and TFTP configuration in Fundamentals
Configuration Guide.

·     In a multicast scenario, you can use advanced
ACLs in a multicast source policy to receive or forward only matching multicast
packets. For more information about multicast source policy, see PIM
configuration and IPv6 PIM configuration in IP Multicast
Configuration Guide.

·     To filter routes in a routing policy, you can
use advanced ACLs to receive or send matching routes. For more information
about routing policy, see Layer 3—IP Routing
Configuration Guide.

·     To take different QoS actions on different
traffic types, you can use advanced ACLs to classify traffic. For more
information about traffic classification see "Configuring QoS." 

Advanced ACLs match packets based on the
following criteria:

·     Source IP addresses.

·     Destination IP addresses.

·     Packet priorities.

·     Protocol types.

·     Other protocol header information, such as
TCP/UDP source and destination port numbers, TCP flags, ICMP message types, and
ICMP message codes.

Compared to basic ACLs, advanced ACLs allow
more flexible and accurate filtering. 

### Configuring an IPv4 advanced ACL

#### Restrictions and guidelines

If an IPv4 advanced ACL is used for QoS
traffic classification or packet filtering in a VXLAN network, the ACL matches
packets as follows:

·     On a VTEP, the ACL can only match the incoming
packets of Ethernet service instances.

·     On an intermediate transport device, the ACL can
match both incoming packets and outgoing packets.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Create an IPv4 advanced ACL and enter its
view. Choose one option as needed:

¡     Create a numbered IPv4 advanced ACL by specifying an ACL number.

acl number acl-number \[ name acl-name ] \[ match-order { auto \| config } ]

¡     Create an IPv4 advanced ACL by specifying
the advanced keyword.

acl advanced { acl-number \| name acl-name } \[ match-order { auto \| config } ]

**3\.**(Optional.) Configure a description for the
IPv4 advanced ACL.

description text

By default, an IPv4 advanced ACL does not
have a description.

**4\.**(Optional.) Set the rule numbering step.

step step-value \[ start start-value
]

By default, the rule numbering step is 5
and the start rule ID is 0\.

**5\.**Create or edit a rule.

rule \[ rule-id ] { deny \| permit } protocol \[ { { ack ack-value \| fin fin-value \| psh psh-value \| rst rst-value \| syn syn-value \| urg urg-value } \* \| established } \| counting \| destination { microsegment microsegment-id \[ mask-length mask-length ] \| dest-address
dest-wildcard \| any } \| destination-port operator port1 \[ port2 ] \| { dscp dscp \| { precedence precedence \| tos tos } \* } \| fragment \| icmp-type { icmp-type \[ icmp-code ] \| icmp-message } \| \[ flow-logging \| logging ] \| source { microsegment microsegment-id \[ mask-length mask-length ] \| source-address
source-wildcard \| any } \| source-port operator port1 \[ port2 ] \| time-range time-range-name \| vpn-instance
vpn-instance-name ] \*

Only the S5560X-HI and S6520X-HI switch
series support the microsegment microsegment-id option. 

The flow-logging or logging keyword takes effect only when the module (for example, packet
filtering) that uses the ACL supports logging. 

**6\.**(Optional.) Add or edit a rule comment.

rule rule-id comment text

By default, no rule comment is
configured.

**7\.**(Optional.) Add a rule remark.

rule \[ rule-id ] remark text

By default, no rule remark is configured.

### Configuring an IPv6 advanced ACL

#### Restrictions and guidelines

If an IPv6 advanced ACL is used for QoS traffic
classification or packet filtering:

·     Do not specify the fragment keyword.

·     Do not specify the routing, hop-by-hop, or flow-label keyword if the ACL is for outbound application.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Create an IPv6 advanced ACL and enter its
view. Choose one option as needed:

¡     Create a numbered IPv6 advanced ACL by specifying an ACL number.

acl ipv6 number acl-number \[ name acl-name ] \[ match-order { auto \| config } ]

¡     Create an IPv6 advanced ACL by specifying the advanced keyword.

acl ipv6 advanced { acl-number \| name acl-name } \[ match-order { auto \| config } ]

**3\.**(Optional.) Configure a description for the
IPv6 advanced ACL.

description text

By default, an IPv6 advanced ACL does not
have a description.

**4\.**(Optional.) Set the rule numbering step.

step step-value \[ start start-value
]

By default, the rule numbering step is 5
and the start rule ID is 0\.

**5\.**Create or edit a rule. 

rule \[ rule-id ] { deny \| permit } protocol \[ { { ack ack-value \| fin fin-value \| psh psh-value \| rst rst-value \| syn syn-value \| urg urg-value } \* \| established } \| counting \| destination { microsegment microsegment-id
\[ mask-length mask-length ] \| dest-address
dest-prefix \| dest-address/dest-prefix \| any } \| destination-port operator port \| \| dscp dscp \| flow-label flow-label-value \| fragment  \| icmp6-type { icmp6-type icmp6-code \| icmp6-message } \| \[ flow-logging \| logging ]\| routing \[ type routing-type ] \| hop-by-hop \[ type hop-type ] \| source { microsegment
microsegment-id
\[ mask-length mask-length ] \| source-address source-prefix \| source-address/source-prefix \| any } \| source-port operator port \| time-range time-range-name \| vpn-instance
vpn-instance-name ] \*

The flow-logging or logging keyword takes effect only when the module (for example, packet filtering)
that uses the ACL supports logging.

Only the S5560X-HI and S6520X-HI switch
series support the microsegment microsegment-id option. 

**6\.**(Optional.) Add or edit a rule comment.

rule rule-id comment text

By default, no rule comment is
configured.

**7\.**(Optional.) Add a rule remark.

rule \[ rule-id ] remark text

By default, no rule remark is configured.

## Configuring a Layer 2 ACL

#### About this task

Layer 2 ACLs can be used in the following
scenarios:

·     To improve security when the device acts as a
Telnet server, you can use Layer 2 ACLs to allow only matching clients to
access the server. For more information, see login management in Fundamentals Configuration Guide.

·     To take different QoS actions on different
traffic types, you can use Layer 2 ACLs to classify traffic. For more information
about traffic classes, see "Configuring QoS."

Layer 2 ACLs, also called Ethernet frame
header ACLs, match packets based on Layer 2 Ethernet header fields, such as:

·     Source MAC address.

·     Destination MAC address.

·     802.1p priority (VLAN priority).

·     Link layer protocol type. 

·     Encapsulation type.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Create a Layer 2 ACL and enter its view. Choose
one option as needed:

¡     Create a Layer 2 ACL by specifying an ACL number.

acl number acl-number \[ name acl-name ] \[ match-order { auto \| config } ]

¡     Create a Layer 2 ACL by specifying the mac keyword.

acl mac { acl-number \| name acl-name } \[ match-order { auto \| config } ]

**3\.**(Optional.) Configure a description for the
Layer 2 ACL.

description text

By default, a Layer 2 ACL does not have a
description.

**4\.**(Optional.) Set the rule numbering step.

step step-value \[ start start-value
]

By default, the rule numbering step is 5
and the start rule ID is 0\.

**5\.**Create or edit a rule.

rule \[ rule-id ] { deny \| permit } \[ cos dot1p \| counting \| dest-mac dest-address dest-mask \| { lsap lsap-type lsap-type-mask \| type protocol-type protocol-type-mask } \| source-mac source-address source-mask \| time-range time-range-name ] \*

**6\.**(Optional.) Add or edit a rule comment.

rule rule-id comment text

By default, no rule comment is
configured.

**7\.**(Optional.) Add a rule remark.

rule \[ rule-id ] remark text

By default, no rule remark is configured.

## Copying an ACL

#### About this task

You can create an ACL by copying an
existing ACL (source ACL). The new ACL (destination ACL) has the same
properties and content as the source ACL, but uses a different number or name
than the source ACL. 

#### Restrictions and guidelines

To successfully copy an ACL, make sure:

·     The destination ACL is the same type as the
source ACL. 

·     The source ACL already exists, but the destination
ACL does not. 

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Copy an existing ACL to create a new ACL.

acl \[ ipv6 \| mac ] copy { source-acl-number \| name source-acl-name } to { dest-acl-number \| name dest-acl-name }

## Configuring packet filtering with ACLs

### About packet filtering with ACLs

This section describes procedures for using
an ACL to filtering packets. For example, you can apply an ACL to an interface
to filter incoming or outgoing packets.

### Applying an ACL to filter packets globally

#### Restrictions and guidelines

You can apply a maximum of three ACLs to
the same direction globally: one IPv4 ACL, one IPv6 ACL, and one Layer 2 ACL.

For an ACL applied globally, if the vpn-instance keyword is specified for an ACL rule, the ACL does not take effect.
If the vpn-instance keyword is not specified,
the rule applies to both public network and private network packets. 

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Apply an ACL globally.

packet-filter \[ ipv6 \| mac ] { acl-number \| name acl-name } global { inbound \| outbound } \[ hardware-count ]

By default, global packet filtering is
not configured.

### Applying an ACL to an interface for packet filtering

#### Restrictions and guidelines

To the same direction of an interface, you
can apply a maximum of three ACLs: one IPv4 ACL, one IPv6 ACL, and one Layer 2
ACL. 

You can use the packet-filter command in VLAN interface view or use the packet-filter
vlan-interface command in system view to
configure packet filtering in one direction of a VLAN interface. You cannot
configure both of them in the same direction of a VLAN interface.

The term "interface" in this
section collectively refers to Layer 2 Ethernet interfaces, Layer 3 Ethernet
interfaces, Layer 3 Ethernet subinterfaces, VLAN interfaces, and VSI
interfaces. You can use the port link-mode command to configure an Ethernet port as a Layer 2 or Layer 3
interface (see Layer 2—LAN
Switching Configuration Guide). For information
about VSI interfaces, see VXLAN Configuration Guide.


For an ACL applied to an interface, if the vpn-instance keyword is specified for an ACL rule, the ACL does not take effect.
If the vpn-instance keyword is not specified,
the rule applies to both public network and private network packets. 

#### Procedure

**1\.**Enter system view. 

system-view

**2\.**Enter interface view.

interface interface-type interface-number

**3\.**Apply an ACL to the interface to filter
packets.

packet-filter \[ ipv6 \| mac ] { acl-number \| name acl-name } { inbound \| outbound } \[ hardware-count ]

By default, an interface does not filter
packets.

### Applying an ACL to a user profile for packet filtering

#### Restrictions and guidelines

You can apply only one ACL to the same
direction of a user profile.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Create a user profile and enter system view.

user-profile profile-name

**3\.**Apply an ACL to the user profile.

packet-filter \[ ipv6 ] { acl-number \| name acl-name } { inbound \| outbound }

By default, the system does not filter
packets for a user profile.

### Configuring the applicable scope of packet filtering on a VLAN interface

#### About this task

You can configure the packet filtering on a
VLAN interface to filter the following packets: 

·     Packets forwarded at Layer 3 by the VLAN
interface.

·     All packets, including packets forwarded at
Layer 3 by the VLAN interface and packets forwarded at Layer 2 by the physical
ports associated with the VLAN interface.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Create a VLAN interface and enter its view.

interface vlan-interface vlan-interface-id

If the VLAN interface already exists, you
directly enter its view.

By default, no VLAN interface exists.

**3\.**Specify the applicable scope of packet
filtering on the VLAN interface.

packet-filter filter  { route \| all }

By default, the packet filtering filters
packets forwarded at Layer 3\. 

### Configuring logging and SNMP notifications for packet filtering

#### About this task

You can configure the ACL module to generate
log entries or SNMP notifications for packet filtering and output them to the
information center or SNMP module at the output interval. The log entry or
notification records the number of matching packets and the matched ACL rules.
When the first packet of a flow matches an ACL rule, the output interval
starts, and the device immediately outputs a log entry or notification for this
packet. When the output interval ends, the device outputs a log entry or
notification for subsequent matching packets of the flow. 

For more information about the information
center and SNMP, see Network Management and Monitoring
Configuration Guide.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Set the interval for outputting packet
filtering logs or notifications.

acl { logging \| trap } interval interval

The default setting is 0 minutes. By
default, the device does not generate log entries or SNMP notifications for
packet filtering.

### Setting the packet filtering default action

**1\.**Enter system view.

system-view

**2\.**Set the packet filtering default action to
deny.

packet-filter default deny

By default, the packet filter permits
packets that do not match any ACL rule to pass.

## Display and maintenance commands for ACL

Execute display commands
in any view and reset commands in user
view.

 

| Task | Command |
| --- | --- |
| Display ACL configuration and match statistics. | display acl \[ ipv6 \| mac ] { acl-number \| all \| name acl-name } || Display ACL application information for packet filtering. | display packet-filter { { global \| interface \[ interface-type interface-number ] \| vlan-interface } \[ inbound \| outbound ] } \[ slot slot-number \[ cpu cpu-number ] ] || Display match statistics for packet filtering ACLs. | display packet-filter statistics { { global \| interface interface-type interface-number \| vlan-interface } { inbound \| outbound } \[ \[ ipv6 \| mac \| user-defined ] { acl-number \| name acl-name } ] \[ brief ] || Display the accumulated statistics for packet filtering ACLs. | display packet-filter statistics sum { inbound \| outbound } \[ ipv6 \| mac ] { acl-number \| name acl-name } \[ brief ] || Display detailed ACL packet filtering information. | display packet-filter verbose { global \| interface interface-type interface-number \| l2vpn-ac interface interface-type interface-number service-instance instance-id \| vlan-interface } { inbound \| outbound } \[ \[ ipv6 \| mac ] { acl-number \| name acl-name } ] \[ slot slot-number ] || Display QoS and ACL resource usage. | display qos-acl resource \[ slot slot-number ] || Clear ACL statistics. | reset acl \[ ipv6 \| mac ] counter { acl-number \| all \| name acl-name } || Clear match statistics for packet filtering ACLs. | reset packet-filter statistics { global \| interface \[ interface-type interface-number ] \| vlan-interface } { inbound \| outbound } \[ \[ ipv6 \| mac ] { acl-number \| name acl-name } ] |








‌

## ACL configuration examples

### Example: Configuring interface-based packet filter

#### Network configuration

A company interconnects its departments
through the device. Configure a packet filter to:

·     Permit access from the President's office at any
time to the financial database server.

·     Permit access from the Finance department to the
database server only during working hours (from 8:00 to 18:00) on working days.

·     Deny access from any other department to the database
server.

Figure 1 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705194_x_Img_x_png_0_2216056_294551_0.png)

‌

#### Procedure

\# Create a periodic time range from 8:00 to
18:00 on working days.

\<Device\> system-view

\[Device] time-range work 08:0 to 18:00
working-day

\# Create an IPv4 advanced ACL numbered 3000\.

\[Device] acl advanced 3000

\# Configure a rule to permit access from
the President's office to the financial database server.

\[Device-acl-ipv4-adv-3000] rule permit
ip source 192.168.1.0 0.0.0.255 destination 192.168.0.100 0

\# Configure a rule to permit access from the
Finance department to the database server during working hours.

\[Device-acl-ipv4-adv-3000] rule permit
ip source 192.168.2.0 0.0.0.255 destination 192.168.0.100 0 time-range work

\# Configure a rule to deny access to the financial
database server.

\[Device-acl-ipv4-adv-3000] rule deny
ip source any destination 192.168.0.100 0

\[Device-acl-ipv4-adv-3000] quit

\# Apply IPv4 advanced ACL 3000 to filter outgoing
packets on interface Ten-GigabitEthernet 1/0/1.

\[Device] interface ten-gigabitethernet 1/0/1

\[Device-Ten-GigabitEthernet1/0/1] packet-filter 3000
outbound

\[Device-Ten-GigabitEthernet1/0/1] quit

#### Verifying the configuration

\# Verify that a PC in the Finance
department can ping the database server during working hours. (All PCs in this
example use Windows XP).

C:\\\> ping 192.168.0.100

 

Pinging 192.168.0.100 with 32 bytes
of data:

 

Reply from 192.168.0.100: bytes\=32
time\=1ms TTL\=255

Reply from 192.168.0.100: bytes\=32
time\<1ms TTL\=255

Reply from 192.168.0.100: bytes\=32
time\<1ms TTL\=255

Reply from 192.168.0.100: bytes\=32
time\<1ms TTL\=255

 

Ping statistics for 192.168.0.100:

    Packets: Sent \= 4, Received \=
4, Lost \= 0 (0% loss),

Approximate round trip times in
milli-seconds:

    Minimum \= 0ms, Maximum \= 1ms,
Average \= 0ms

\# Verify that a PC in the Marketing
department cannot ping the database server during working hours.

C:\\\> ping 192.168.0.100

 

Pinging 192.168.0.100 with 32 bytes
of data:

 

Request timed out.

Request timed out.

Request timed out.

Request timed out.

 

Ping statistics for 192.168.0.100:

    Packets: Sent \= 4, Received \=
0, Lost \= 4 (100% loss),

\# Display configuration and match
statistics for IPv4 advanced ACL 3000 on the device during working hours.

\[Device] display acl 3000

Advanced IPv4 ACL 3000, 3 rules,

ACL's step is 5

 rule 0 permit ip source 192.168.1.0
0.0.0.255 destination 192.168.0.100 0

 rule 5 permit ip source 192.168.2.0
0.0.0.255 destination 192.168.0.100 0 time-range work (Active)

 rule 10 deny ip destination
192.168.0.100 0

The output shows that rule 5 is active. Rule
5 and rule 10 have been matched four times as the result of the ping operations.

 

