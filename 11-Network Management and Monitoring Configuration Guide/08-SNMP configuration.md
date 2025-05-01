
# Configuring SNMP

## About SNMP

Simple Network Management Protocol (SNMP)
is used for a management station to access and operate the devices on a
network, regardless of their vendors, physical characteristics, and
interconnect technologies.

SNMP enables network administrators to read
and set the variables on managed devices for state monitoring, troubleshooting,
statistics collection, and other management purposes.

### SNMP framework

The SNMP framework contains the following
elements:

·     SNMP manager—Works on an NMS to monitor and manage the SNMP-capable devices in
the network. It can get and set values of MIB objects on an agent.

·     SNMP agent—Works on a managed device to receive and handle requests from the
NMS, and sends notifications to the NMS when events, such as an interface state
change, occur.

·     Management Information Base
(MIB)—Specifies the variables (for example,
interface status and CPU usage) maintained by the SNMP agent for the SNMP
manager to read and set.

Figure 1 Relationship
between NMS, agent, and MIB

![](https://resource.h3c.com/en/202407/12/20240712_11705739_x_Img_x_png_0_2216113_294551_0.png)

 

### MIB and view-based MIB access control

A MIB stores variables called
"nodes" or "objects" in a tree hierarchy and identifies
each node with a unique OID. An OID is a dotted numeric string that uniquely
identifies the path from the root node to a leaf node. For example, object B in
[Figure 2](#_Ref289503265) is
uniquely identified by the OID {1.2.1.1}.

Figure 2 MIB tree

![](https://resource.h3c.com/en/202407/12/20240712_11705740_x_Img_x_png_1_2216113_294551_0.png)

 

A MIB view represents a set of MIB objects
(or MIB object hierarchies) with certain access privileges and is identified by
a view name. The MIB objects included in the MIB view are accessible while
those excluded from the MIB view are inaccessible. 

A MIB view can have multiple view records
each identified by a view-name oid-tree pair.

You control access to the MIB by assigning
MIB views to SNMP groups or communities.

### SNMP operations

SNMP provides the following basic
operations:

·     Get—NMS retrieves the value of an object node in an agent MIB.

·     Set—NMS modifies the value of an object node in an agent MIB.

·     Notification—SNMP notifications include traps and informs. The SNMP agent sends
traps or informs to report events to the NMS. The difference between these two
types of notification is that informs require acknowledgment but traps do not. Informs
are more reliable but are also resource-consuming. Traps are available in
SNMPv1, SNMPv2c, and SNMPv3. Informs are available only in SNMPv2c and SNMPv3.

### Protocol versions

The device supports SNMPv1, SNMPv2c, and
SNMPv3 in non-FIPS mode and supports only SNMPv3 in FIPS mode. An NMS and an
SNMP agent must use the same SNMP version to communicate with each other.

·     SNMPv1—Uses community names for authentication. To access an SNMP agent,
an NMS must use the same community name as set on the SNMP agent. If the
community name used by the NMS differs from the community name set on the
agent, the NMS cannot establish an SNMP session to access the agent or receive
traps from the agent.

·     SNMPv2c—Uses community names for authentication. SNMPv2c is compatible with
SNMPv1, but supports more operation types, data types, and error codes.

·     SNMPv3—Uses a user-based security model (USM) to secure SNMP
communication. You can configure authentication and privacy mechanisms to
authenticate and encrypt SNMP packets for integrity, authenticity, and
confidentiality.

### Access control modes

SNMP uses the following modes to control
access to MIB objects:

·     View-based Access Control
Model—VACM mode controls access to MIB objects by
assigning MIB views to SNMP communities or users.

·     Role based access control—RBAC mode controls access to MIB objects by assigning user roles to
SNMP communities or users.

¡     SNMP
communities or users with predefined user role network-admin or level-15 have read
and write access to all MIB objects.

¡     SNMP
communities or users with predefined user role network-operator have read-only
access to all MIB objects.

¡     SNMP
communities or users with a user-defined user role have access rights to MIB
objects as specified by the rule command.

RBAC mode controls access on a per MIB
object basis, and VACM mode controls access on a MIB view basis. As a best
practice to enhance MIB security, use the RBAC mode.

If you create the same SNMP community or
user with both modes multiple times, the most recent configuration takes
effect. For more information about RBAC, see Fundamentals
Command Reference.

## FIPS compliance

The device supports the FIPS mode that
complies with NIST FIPS 140-2 requirements. Support for features, commands, and
parameters might differ in FIPS mode and non-FIPS mode. For more information
about FIPS mode, see Security Configuration Guide.

## SNMP tasks at a glance

To configure SNMP, perform the following
tasks:

**1\.**[Enabling the SNMP agent](#_Ref472522494)

**2\.**[Enabling SNMP versions](#_Ref472522515)

**3\.**Configuring SNMP basic parameters

¡     (Optional.)
[Configuring SNMP common parameters](#_Ref472522542)

¡     [Configuring an SNMPv1 or SNMPv2c community](#_Ref490832359)

¡     [Configuring an SNMPv3 group and user](#_Ref472522578)

**4\.**(Optional.) [Configuring SNMP notifications](#_Ref472522590)

**5\.**(Optional.)
[Examining the system configuration for
changes](#_Ref61289600)

**6\.**(Optional.) [Configuring SNMP logging](#_Ref472522606)

## Enabling the SNMP agent

#### Restrictions and guidelines

The SNMP agent is enabled when you use any
command that begins with snmp-agent except for
the snmp-agent
calculate-password command.

The SNMP agent will fail to be enabled when
the port that the agent will listen on is used by another service. You can use
the snmp-agent
port command to specify a listening port. To
view the UDP port use information, execute the display udp
verbose command. For more information about the display udp
verbose command, see IP performance optimization
commands in Layer 3—IP Services Configuration Guide.

If you disable the SNMP agent, the SNMP
settings do not take effect. The display current-configuration command does not display the SNMP settings and the SNMP settings
will not be saved in the configuration file. For the SNMP settings to take
effect, enable the SNMP agent.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enable the SNMP agent.

snmp-agent

By default, the SNMP agent is disabled.

## Enabling SNMP versions

#### Restrictions and guidelines

The device supports SNMPv1, SNMPv2c, and SNMPv3
in non-FIPS mode and supports only SNMPv3 in FIPS mode. An NMS and an SNMP
agent must use the same SNMP version to communicate with each other.

The community name and data carried in
SNMPv1 and SNMPv2c messages are in plaintext form, putting the SNMP
communication at risks. As a best practice, use SNMPv3.

To use SNMP notifications in IPv6, enable
SNMPv2c or SNMPv3.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enable SNMP versions.

In non-FIPS mode:

snmp-agent sys-info version { all \| { v1 \| v2c \| v3 } \* }

In FIPS mode:

snmp-agent sys-info version v3

By default, SNMPv3 is enabled.

If you execute the command multiple times
with different options, all the configurations take effect, but only one SNMP
version is used by the agent and NMS for communication.

## Configuring SNMP common parameters

#### Restrictions and guidelines

An SNMP engine ID uniquely identifies a
device in an SNMP managed network. Make sure the local SNMP engine ID is unique
within your SNMP managed network to avoid communication problems. By default,
the device is assigned a unique SNMP engine ID.

If you have configured SNMPv3 users, change
the local SNMP engine ID only when necessary. The change can void the SNMPv3
usernames and encrypted keys you have configured.

The SNMP agent will fail to be enabled when
the port that the agent will listen on is used by another service. You can use
the snmp-agent
port command to change the SNMP listening port.
As a best practice, execute the display udp verbose command to view the UDP port use information before specifying a new
SNMP listening port. For more information about the display udp
verbose command, see IP performance optimization
commands in Layer 3—IP Services Configuration Guide.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Specify an SNMP listening port.

snmp-agent port port-number

By default, the SNMP listening port is
UDP port 161\.

**3\.**Set a local SNMP engine ID.

snmp-agent local-engineid engineid

By default, the local SNMP engine ID is
the company ID plus the device ID.

**4\.**Set an engine ID for a remote SNMP entity.

snmp-agent remote { ipv4-address \| ipv6 ipv6-address } \[ vpn-instance
vpn-instance-name ] engineid engineid

By default, no remote entity engine IDs
exist.

This step is required for the device to
send SNMPv3 notifications to a host, typically NMS.

**5\.**Create or update a MIB view.

snmp-agent mib-view { excluded \| included } view\-name oid\-tree \[ mask mask-value ]

By default, the MIB view ViewDefault is
predefined. In this view, all the MIB objects in the iso subtree but
the snmpUsmMIB, snmpVacmMIB, and snmpModules.18 subtrees are accessible.

Each view-name
oid-tree pair represents a view record. If you specify the same record
with different MIB sub-tree masks multiple times, the most recent configuration
takes effect.

**6\.**Configure the system management information.

¡     Configure
the system contact.

snmp-agent sys-info contact sys-contact

By default, the system contact is New H3C Technologies Co., Ltd..

¡     Configure
the system location.

snmp-agent sys-info location sys-location

By default, the system location is Hangzhou, China.

**7\.**Create an SNMP context.

snmp-agent context context-name

By default, no SNMP contexts exist.

**8\.**Configure the maximum SNMP packet size (in
bytes) that the SNMP agent can handle.

snmp-agent packet max-size byte\-count

By default, an SNMP agent can process
SNMP packets with a maximum size of 1500 bytes.

**9\.**Set the DSCP value for SNMP responses.

snmp-agent packet response dscp dscp-value

By default, the DSCP value for SNMP
responses is 0\.

## Configuring an SNMPv1 or SNMPv2ccommunity

### About configuring an SNMPv1 or SNMPv2c community

You can create an SNMPv1 or SNMPv2c
community by using a community name or by creating an SNMPv1 or SNMPv2c user.
After you create an SNMPv1 or SNMPv2c user, the system automatically creates a
community by using the username as the community name. 

### Restrictions and guidelines for configuring an SNMPv1 or SNMPv2c community

SNMPv1 and SNMPv2c settings are not supported
in FIPS mode.

Make sure the NMS and agent use the same
SNMP community name.

 Only users with the network-admin or
level-15 user role can create SNMPv1 or SNMPv2c communities, users, or groups. Users
with other user roles cannot create SNMPv1 or SNMPv2c communities, users, or
groups even if these roles are granted access to related commands or commands
of the SNMPv1 or SNMPv2c feature.

### Configuring an SNMPv1/v2c community by a community name

**1\.**Enter system view.

system-view

**2\.**Create an SNMPv1/v2c community. Choose one
option as needed.

¡     In
VACM mode:

snmp-agent community { read \| write } \[ simple \| cipher ] community-name \[ mib-view view-name ] \[ acl { ipv4-acl-number \| name ipv4-acl-name } \| acl ipv6 { ipv6-acl-number \| name ipv6-acl-name
} ] \*

¡     In
RBAC mode:

snmp-agent community \[ simple \| cipher ] community-name user-role role-name \[ acl { ipv4-acl-number \| name ipv4-acl-name } \| acl ipv6 { ipv6-acl-number \| name ipv6-acl-name
} ] \*

**3\.**(Optional.) Map the SNMP community name to
an SNMP context.

snmp-agent community-map
community-name context context-name

### Configuring an SNMPv1/v2c community by creating an SNMPv1/v2c user

**1\.**Enter system view.

system-view

**2\.**Create an SNMPv1/v2c group.

snmp-agent group { v1 \| v2c } group-name \[ notify-view view-name \| read-view view-name \| write-view view-name ] \* \[ acl { ipv4-acl-number \| name ipv4-acl-name
} \| acl ipv6 { ipv6-acl-number
\| name ipv6-acl-name } ] \*

**3\.**Add an SNMPv1/v2c user to the group.

snmp-agent usm-user { v1 \| v2c } user-name group-name \[ acl { ipv4-acl-number \| name ipv4-acl-name
} \| acl ipv6 { ipv6-acl-number
\| name ipv6-acl-name } ] \*

The system automatically creates an SNMP
community by using the username as the community name.

**4\.**(Optional.) Map the SNMP community name to
an SNMP context.

snmp-agent community-map
community-name context context-name

## Configuring an SNMPv3 group and user

### Restrictions and guidelines for configuring an SNMPv3 group and user

Only users with the network-admin or
level-15 user role can create SNMPv3 users or groups.Users with other user
roles cannot create SNMPv3 users or groups even if these roles are granted
access to related commands or commands of the SNMPv3 feature.

SNMPv3 users are managed in groups. All
SNMPv3 users in a group share the same security model, but can use different
authentication and encryption algorithms and keys. [Table 1](#_Ref485041462)
describes the basic configuration requirements for different security models.

Table 1 Basic configuration requirements for
different security models

| Security model | Keyword for the group | Parameters for the user | Remarks |
| --- | --- | --- | --- |
| Authentication with privacy | privacy | Authentication and encryption algorithms and keys | For an NMS to access the agent, make sure the NMS and agent use the same authentication and encryption keys. || Authentication without privacy | authentication | Authentication algorithm and key | For an NMS to access the agent, make sure the NMS and agent use the same authentication key. || No authentication, no privacy | N/A | N/A | The authentication and encryption keys, if configured, do not take effect. |



‌

### Configuring an SNMPv3 group and user in non-FIPS mode

**1\.**Enter system view.

system-view

**2\.**Create an SNMPv3 group.

snmp-agent group v3 group-name \[ authentication \| privacy ] \[ notify-view view-name \| read-view view-name \| write-view view-name ] \*\[ acl { ipv4-acl-number \| name ipv4-acl-name
} \| acl ipv6 { ipv6-acl-number
\| name ipv6-acl-name } ] \*

**3\.**(Optional.) Calculate the encrypted form for
the key in plaintext form.

snmp-agent calculate-password plain-password mode { 3desmd5 \| 3dessha \| 3dessha224 \| 3dessha256 \| 3dessha384 \| 3dessha512 \| aes192md5 \| aes192sha \| aes192sha224
\| aes192sha256 \| aes192sha384 \| aes192sha512 \| aes256md5 \| aes256sha \| aes256sha224
\| aes256sha256 \| aes256sha384 \| aes256sha512 \| md5 \| sha \| sha224 \| sha256 \| sha384 \| sha512 } { local-engineid \| specified-engineid engineid }

**4\.**Create an SNMPv3 user. Choose one option as
needed.

¡     In
VACM mode:

snmp-agent usm-user v3 user-name group-name \[ remote { ipv4-address \| ipv6 ipv6-address } \[ vpn-instance vpn-instance-name ] ] \[ { cipher \| simple } authentication-mode { md5 \| sha \| sha224 \| sha256 \| sha384 \| sha512 } auth-password \[ privacy-mode
{ 3des \| aes128 \| aes192 \| aes256 \| des56 } priv-password ] ] \[ acl { ipv4-acl-number \| name ipv4-acl-name
} \| acl ipv6 { ipv6-acl-number
\| name ipv6-acl-name } ] \*

¡     In
RBAC mode:

snmp-agent usm-user v3 user-name user-role role-name \[ remote { ipv4-address \| ipv6 ipv6-address } \[ vpn-instance vpn-instance-name ] ] \[ { cipher \| simple } authentication-mode { md5 \| sha \| sha224 \| sha256 \| sha384 \| sha512 } auth-password \[ privacy-mode { 3des \| aes128 \| aes192 \| aes256 \| des56 } priv-password ] ] \[ acl { ipv4-acl-number \| name ipv4-acl-name
} \| acl ipv6 { ipv6-acl-number
\| name ipv6-acl-name } ] \*

To send notifications to an SNMPv3 NMS,
you must specify the remote keyword.

**5\.**(Optional.) Assign a user role to the SNMPv3
user created in RBAC mode.

snmp-agent usm-user v3 user-name user-role role-name

By default, an SNMPv3 user has the user
role assigned to it at its creation.

### Configuring an SNMPv3 group and user in FIPS mode

**1\.**Enter system view.

system-view

**2\.**Create an SNMPv3 group.

snmp-agent group v3 group-name { authentication \| privacy } \[ notify-view view-name \| read-view view-name \| write-view view-name ] \* \[ acl { ipv4-acl-number \| name ipv4-acl-name
} \| acl ipv6 { ipv6-acl-number
\| name ipv6-acl-name } ] \*

**3\.**(Optional.) Calculate the encrypted form for
the key in plaintext form.

snmp-agent calculate-password plain-password mode { aes192sha \| aes192sha224 \| aes192sha256 \| aes192sha384 \| aes192sha512 \| aes256sha \| aes256sha224 \| aes256sha256 \| aes256sha384 \| aes256sha512 \| sha \| sha224 \| sha256 \| sha384 \| sha512 } { local-engineid \| specified-engineid engineid }

**4\.**Create an SNMPv3 user. Choose one option as
needed.

¡     In
VACM mode:

snmp-agent usm-user v3 user-name group-name \[ remote { ipv4-address \| ipv6 ipv6-address } \[ vpn-instance vpn-instance-name ] ] { cipher \| simple } authentication-mode { sha \| sha224 \| sha256 \| sha384 \| sha512 } auth-password \[ privacy-mode
{ aes128 \| aes192 \| aes256 } priv-password ] \[ acl { ipv4-acl-number \| name ipv4-acl-name
} \| acl ipv6 { ipv6-acl-number
\| name ipv6-acl-name } ] \*

¡     In
RBAC mode:

snmp-agent usm-user v3 user-name user-role role-name \[ remote { ipv4-address \| ipv6 ipv6-address } \[ vpn-instance vpn-instance-name ] ] { cipher \| simple } authentication-mode { sha \| sha224 \| sha256 \| sha384 \| sha512 } auth-password \[ privacy-mode
{ aes128 \| aes192 \| aes256 } priv-password ] \[ acl { ipv4-acl-number \| name ipv4-acl-name
} \| acl ipv6 { ipv6-acl-number
\| name ipv6-acl-name } ] \*

To send notifications to an SNMPv3 NMS,
you must specify the remote keyword.

**5\.**(Optional.) Assign a user role to the SNMPv3
user created in RBAC mode.

snmp-agent usm-user v3 user-name user-role role-name

By default, an SNMPv3 user has the user
role assigned to it at its creation.

## Configuring SNMP notifications

### About SNMP notifications

The SNMP agent sends notifications (traps
and informs) to inform the NMS of significant events, such as link state
changes and user logins or logouts. After you enable notifications for a module,
the module sends the generated notifications to the SNMP agent. The SNMP agent
sends the received notifications as traps or informs based on the current
configuration. Unless otherwise stated, the trap keyword
in the command line includes both traps and informs. 

### Enabling SNMP notifications

#### Restrictions and guidelines

Enable an SNMP notification only if
necessary. SNMP notifications are memory-intensive and might affect device
performance.

To generate linkUp or linkDown
notifications when the link state of an interface changes, you must perform the
following tasks:

·     Enable linkUp or linkDown notification globally
by using the snmp-agent trap enable standard \[ linkdown \| linkup ] \* command.

·     Enable linkUp or linkDown notification on the
interface by using the enable snmp trap updown
command.

After you enable notifications for a
module, whether the module generates notifications also depends on the
configuration of the module. For more information, see the configuration guide
for each module.

To use SNMP notifications in IPv6, enable
SNMPv2c or SNMPv3.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enable SNMP notifications.

snmp-agent trap enable \[ configuration \| protocol \| standard \[ authentication \| coldstart \| linkdown \| linkup \| warmstart ] \* \| system ]

By default, configuration change
notifications, SNMP standard notifications, and system notifications are
enabled. For the SNMP notifications enabling status for other modules, see the
module guide.

For the device to send SNMP notifications
for a protocol, first enable the protocol.

**3\.**Enter interface view.

interface interface-type
interface-number

**4\.**Enable link state notifications.

enable snmp trap updown

By default, link state notifications are
enabled.

### Configuring parameters for sending SNMP notifications

#### About this task

You can configure the SNMP agent to send
notifications as traps or informs to a host, typically an NMS, for analysis and
management. Traps are less reliable and use fewer resources than informs,
because an NMS does not send an acknowledgment when it receives a trap.

When network congestion occurs or the
destination is not reachable, the SNMP agent buffers notifications in a queue.
You can set the queue size and the notification lifetime (the maximum time that
a notification can stay in the queue). When the queue size is reached, the
system discards the new notification it receives. If modification of the queue
size causes the number of notifications in the queue to exceed the queue size,
the oldest notifications are dropped for new notifications. A notification is
deleted when its lifetime expires.

You can extend standard linkUp/linkDown
notifications to include interface description and interface type, but must
make sure the NMS supports the extended SNMP messages.

#### Configuring the parameters for sending SNMP traps

**1\.**Enter system view.

system-view

**2\.**Configure a target host.

In non-FIPS mode:

snmp-agent target-host trap address udp-domain { ipv4-target-host \| ipv6 ipv6-target-host
} \[ udp-port port-number ] \[ dscp dscp-value ] \[ vpn-instance vpn-instance-name ] params { cipher-securityname cipher-security-string \[ v1 \| v2c ] \| securityname security-string \[ v1 \| v2c \| v3 \[ authentication \| privacy ] ] }

In FIPS mode:

snmp-agent target-host trap address udp-domain { ipv4-target-host \| ipv6 ipv6-target-host
} \[ udp-port port-number ] \[ dscp dscp-value ] \[ vpn-instance vpn-instance-name ] params securityname security-string v3 { authentication \| privacy }

By default, no target host is configured.

**3\.**(Optional.) Configure a source address for
sending traps.

snmp-agent trap source interface-type { interface-number \| interface-number.subnumber }

By default, SNMP uses the IP address of
the outgoing routed interface as the source IP address.

#### Configuring the parameters for sending SNMP informs

**1\.**Enter system view.

system-view

**2\.**Configure a target host.

In non-FIPS mode:

snmp-agent target-host inform address udp-domain { ipv4-target-host \| ipv6 ipv6-target-host
} \[ udp-port port-number ] \[ vpn-instance vpn-instance-name ] params { cipher-securityname
cipher-security-string v2c \| securityname security-string { v2c \| v3 \[ authentication \| privacy ] } }

In FIPS mode:

snmp-agent target-host inform address udp-domain { ipv4-target-host \| ipv6 ipv6-target-host
} \[ udp-port port-number ] \[ vpn-instance vpn-instance-name ] params securityname security-string v3 { authentication \| privacy }

By default, no target host is configured.

Only SNMPv2c and SNMPv3 support inform
packets.

**3\.**(Optional.) Configure a source address for
sending informs.

snmp-agent inform source interface-type { interface-number \| interface-number.subnumber }

By default, SNMP uses the IP address of
the outgoing routed interface as the source IP address.

#### Configuring common parameters for sending notifications

**1\.**Enter system view.

system-view

**2\.**(Optional.) Enable extended linkUp/linkDown
notifications.

snmp-agent trap if-mib link extended

By default, the SNMP agent sends standard
linkUp/linkDown notifications.

If the NMS does not support extended linkUp/linkDown
notifications, do not use this command.

**3\.**(Optional.) Set the notification queue size.

snmp-agent trap queue-size size

By default, the notification queue can
hold 100 notification messages.

**4\.**(Optional.) Set the notification lifetime.

snmp-agent trap life seconds

The default notification lifetime is 120
seconds.

**5\.**(Optional.) Specify the notification format
as CMCC.

snmp-agent trap format cmcc

By default, the SNMP notifications are in
general format.

**6\.**(Optional.) Configure the device to carry
its serial number in the notifications sent an NMS.

snmp-agent trap withsn

By default, the device does not carry its
serial number in the notifications sent an NMS.

## Examining the system configuration for changes

#### About this task

The SNMP module examines the system running
configuration, startup configuration, and next-startup configuration file for
changes periodically and generates a log if any change is found. If SNMP
notifications for configuration changes has been enabled, the system generates
also an SNMP notification.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Set the interval at which the SNMP module
examines the system configuration for changes.

snmp-agent configuration-examine interval interval

By default, the SNMP module examines the
system configuration for changes at intervals of 600 seconds.

**3\.**Enable SNMP notifications for system
configuration changes.

snmp-agent trap enable configuration

By default, SNMP notifications is enabled
for system configuration changes.

## Configuring SNMP logging

#### About this task

The SNMP agent logs Get requests, Set
requests, Set responses, SNMP notifications, and SNMP authentication failures,
but does not log Get responses. 

·     Get operation—The agent logs the IP address of the NMS, name of the accessed
node, and node OID.

·     Set operation—The agent logs the NMS' IP address, name of accessed node, node
OID, variable value, and error code and index for the Set operation.

·     Notification tracking—The agent logs the SNMP notifications after sending them to the
NMS.

·     SNMP authentication failure—The agent logs related information when an NMS fails to be
authenticated by the agent.

The SNMP module sends these logs to the
information center. You can configure the information center to output these
messages to certain destinations, such as the console and the log buffer. The
total output size for the node field (MIB node name) and the value field (value
of the MIB node) in each log entry is 1024 bytes. If this limit is exceeded,
the information center truncates the data in the fields. For more information
about the information center, see "Configuring the information
center."

#### Restrictions and guidelines

Enable SNMP logging only if necessary. SNMP
logging is memory-intensive and might impact device performance.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enable SNMP logging.

snmp-agent log { all \| authfail \| get-operation \| set-operation }

By default, SNMP logging is enabled for
set operations and disabled for SNMP authentication failures and get operations.

**3\.**Enable SNMP notification logging.

snmp-agent trap log

By default, SNMP notification logging is
disabled.

## Display and maintenance commands for SNMP

Execute display
commands in any view and reset commands in user
view.

 

| Task | Command |
| --- | --- |
| Display SNMPv1 or SNMPv2c community information. (This command is not supported in FIPS mode.) | display snmp-agent community \[ read \| write ] || Display SNMP contexts. | display snmp-agent context \[ context-name ] || Display SNMP group information. | display snmp-agent group \[ group-name ] || Display the local engine ID. | display snmp-agent local-engineid || Display SNMP MIB node information. | display snmp-agent mib-node \[ details \| index-node \| trap-node \| verbose ] || Display MIB view information. | display snmp-agent mib-view \[ exclude \| include \| viewname view-name ] || Display remote engine IDs. | display snmp-agent remote \[ { ipv4-address \| ipv6 ipv6-address } \[ vpn-instance vpn-instance-name ] ] || Display SNMP agent statistics. | display snmp-agent statistics || Display SNMP agent system information. | display snmp-agent sys-info \[ contact \| location \| version ] \* || Display basic information about the notification queue. | display snmp-agent trap queue || Display SNMP notifications drop records. | display snmp-agent trapbuffer drop || Display SNMP notifications sending records. | display snmp-agent trapbuffer send || Display the SNMP notifications enabling status for modules. | display snmp-agent trap-list || Display SNMPv3 user information. | display snmp-agent usm-user \[ engineid engineid \| username user-name \| group group-name ] \* || Clear all records from the SNMP trap buffer. | reset snmp-agent trapbuffer |















## SNMP configuration examples

### Example: Configuring SNMPv1/SNMPv2c

The device does not support this
configuration example in FIPS mode.

The configuration procedure is the same for
SNMPv1 and SNMPv2c. This example uses SNMPv1.

#### Network configuration

As shown in [Figure 3](#_Ref222195927), the
NMS (1.1.1.2/24) uses SNMPv1 to manage the SNMP agent (1.1.1.1/24), and the
agent automatically sends notifications to report events to the NMS.

Figure 3 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705741_x_Img_x_png_2_2216113_294551_0.png)

 

#### Procedure

**1\.**Configure the SNMP agent:

\# Assign IP address 1.1.1.1/24 to
the agent and make sure the agent and the NMS can reach each other. (Details
not shown.)

\# Specify SNMPv1, and create read-only
community public and read and write community private.

\<Agent\> system-view

\[Agent] snmp-agent sys-info
version v1

\[Agent] snmp-agent community
read public

\[Agent] snmp-agent community
write private

\# Configure contact and physical location
information for the agent.

\[Agent] snmp-agent sys-info
contact Mr.Wang-Tel:3306

\[Agent] snmp-agent sys-info
location telephone-closet,3rd-floor

\# Enable SNMP notifications, specify the
NMS at 1.1.1.2 as an SNMP trap destination, and use public as the
community name. (To make sure the NMS can receive traps, specify the same SNMP
version in the snmp-agent target-host
command as is configured on the NMS.)

\[Agent] snmp-agent trap enable

\[Agent] snmp-agent target-host
trap address udp-domain 1.1.1.2 params securityname public v1

**2\.**Configure the SNMP NMS:

¡     Specify
SNMPv1.

¡     Create
read-only community public, and create read and
write community private.

¡     Set the
timeout timer and maximum number of retries as needed.

For information about configuring the
NMS, see the NMS manual. 

 

|  | NOTE:  The SNMP settings on the agent and the NMS must match. |
| --- | --- |

 

#### Verifying the configuration

\# Try to get the MTU value of the NULL0
interface from the agent. The attempt succeeds.

Send request to 1.1.1.1/161 ...

Protocol version: SNMPv1

Operation: Get

Request binding: 

1: 1.3.6.1.2.1.2.2.1.4.135471

Response binding:

1: Oid\=ifMtu.135471 Syntax\=INT
Value\=1500

Get finished

\# Use a wrong community name to get the
value of a MIB node on the agent. You can see an authentication failure trap on
the NMS.

1.1.1.1/2934 V1 Trap \=
authenticationFailure

SNMP Version \= V1

Community \= public

Command \= Trap

Enterprise \=
1.3.6.1.4.1.43.1.16.4.3.50

GenericID \= 4

SpecificID \= 0

Time Stamp \= 8:35:25.68

### Example: Configuring SNMPv3

#### Network configuration

As shown in [Figure 4](#_Ref222195950), the
NMS (1.1.1.2/24) uses SNMPv3 to monitor and manage the agent (1.1.1.1/24). The
agent automatically sends notifications to report events to the NMS. The
default UDP port 162 is used for SNMP notifications. 

The NMS and the agent perform
authentication when they establish an SNMP session. The authentication
algorithm is SHA-1 and the authentication key is 123456TESTauth\&!. The NMS and the agent also encrypt
the SNMP packets between them by using the AES
algorithm and encryption key 123456TESTencr\&!.

Figure 4 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705742_x_Img_x_png_3_2216113_294551_0.png)

 

#### Configuring SNMPv3 in RBAC mode

**1\.**Configure the agent:

\# Assign IP address 1.1.1.1/24 to
the agent and make sure the agent and the NMS can reach each other. (Details
not shown.)

#Create user role test, and assign
test read-only access to the objects under the snmpMIB node
(OID:1.3.6.1.6.3.1), including the
linkUp and linkDown objects.

\<Agent\> system-view

\[Agent] role name test

\[Agent-role-test] rule 1
permit read oid 1.3.6.1.6.3.1

\# Assign user role test read-only
access to the system node (OID:1.3.6.1.2.1.1) and read-write access to the interfaces
node(OID:1.3.6.1.2.1.2).

\[Agent-role-test] rule 2
permit read oid 1.3.6.1.2.1.1

\[Agent-role-test] rule 3
permit read write oid 1.3.6.1.2.1.2

\[Agent-role-test] quit

\# Create SNMPv3 user RBACtest. Assign
user role test to RBACtest. Set the authentication algorithm to SHA-1, authentication key to 123456TESTauth\&!, encryption algorithm to AES, and encryption key to 123456TESTencr\&!.

\[Agent] snmp-agent usm-user v3
RBACtest user-role test simple authentication-mode sha 123456TESTauth\&!
privacy-mode aes128 123456TESTencr\&!

#Configure contact and physical location
information for the agent.

\[Agent] snmp-agent sys-info contact
Mr.Wang-Tel:3306

\[Agent] snmp-agent sys-info location
telephone-closet,3rd-floor

#Enable notifications on the agent. Specify
the NMS at 1.1.1.2 as the notification destination, and RBACtest as the
username.

\[Agent] snmp-agent trap enable

\[Agent] snmp-agent target-host
trap address udp-domain 1.1.1.2 params securitynameRBACtest v3 privacy

**2\.**Configure the NMS:

¡     Specify
SNMPv3.

¡     Create
SNMPv3 user RBACtest.

¡     Enable
authentication and encryption. Set the authentication algorithm to SHA-1, authentication key to 123456TESTauth\&!,
encryption algorithm to AES, and encryption key to 123456TESTencr\&!.

¡     Set
the timeout timer and maximum number of retries.

For information about configuring the
NMS, see the NMS manual.

 

|  | NOTE:  The SNMP settings on the agent and the NMS must match. |
| --- | --- |

 

#### Configuring SNMPv3 in VACM mode

**1\.**Configure the agent:

\# Assign IP address 1.1.1.1/24 to the
agent, and make sure the agent and the NMS can reach each other. (Details not
shown.)

\# Create SNMPv3 group managev3group
and assign managev3group read-only access to the objects under the snmpMIB node
(OID: 1.3.6.1.2.1.2.2) in the test view, including the linkUp and linkDown objects.

\<Agent\> system-view

\[Agent] undo snmp-agent
mib-view ViewDefault

\[Agent] snmp-agent mib-view
included test snmpMIB

\[Agent] snmp-agent group v3
managev3group privacy read-view test

#Assign SNMPv3 group managev3group
read-write access to the objects under the system node (OID: 1.3.6.1.2.1.1) and interfaces node
(OID:1.3.6.1.2.1.2) in the test view.

\[Agent] snmp-agent mib-view
included test 1.3.6.1.2.1.1

\[Agent] snmp-agent mib-view
included test 1.3.6.1.2.1.2

\[Agent] snmp-agent group v3
managev3group privacy read-view test write-view test

\# Add user VACMtest to SNMPv3 group managev3group,
and set the authentication algorithm to SHA-1, authentication key to 123456TESTauth\&!, encryption algorithm to AES, and encryption key to 123456TESTencr\&!.

\[Agent] snmp-agent usm-user v3
VACMtest managev3group simple authentication-mode sha 123456TESTauth\&!
privacy-mode aes128 123456TESTencr\&!

\# Configure contact and physical location
information for the agent.

\[Agent] snmp-agent sys-info
contact Mr.Wang-Tel:3306

\[Agent] snmp-agent sys-info
location telephone-closet,3rd-floor

\# Enable notifications on the agent.
Specify the NMS at 1.1.1.2 as the trap destination, and VACMtest as the
username.

\[Agent] snmp-agent trap enable

\[Agent] snmp-agent target-host
trap address udp-domain 1.1.1.2 params VACMtest v3 privacy

**2\.**Configure the SNMP NMS:

¡     Specify
SNMPv3.

¡     Create
SNMPv3 user VACMtest.

¡     Enable
authentication and encryption. Set the authentication algorithm to SHA-1, authentication key to 123456TESTauth\&!,
encryption algorithm to AES, and encryption key to 123456TESTencr\&!.

¡     Set
the timeout timer and maximum number of retries.

For information about configuring the NMS,
see the NMS manual.

 

|  | NOTE:  The SNMP settings on the agent and the NMS must match. |
| --- | --- |

 

#### Verifying the configuration

·     Use username RBACtest
to access the agent.

\# Retrieve the value of the sysName node.
The value Agent is returned.

\# Set the value for the sysName node to Sysname. The operation
fails because the NMS does not have write access to the node.

\# Shut down or bring up an interface on
the agent. The NMS receives linkUP (OID: 1.3.6.1.6.3.1.1.5.4) or linkDown (OID:
1.3.6.1.6.3.1.1.5.3) notifications.

·     Use username VACMtest
to access the agent.

\# Retrieve the value of the sysName node.
The value Agent is returned.

\# Set the value for the sysName node to Sysname. The operation
succeeds.

\# Shut down or bring up an interface on
the agent. The NMS receives linkUP (OID: 1.3.6.1.6.3.1.1.5.4) or linkDown (OID: 1.3.6.1.6.3.1.1.5.3)
notifications.

 

