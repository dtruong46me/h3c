
# Configuring port security

## About port security

Port security not only provides port
security modes to enhance network access security, but also provides MAC-based
network access control for 802.1X, MAC authentication, Web authentication, and
static users.

### Major functions

Port security provides the following
functions:

·     Prevents unauthorized access to a network by
checking the source MAC address of inbound traffic.

·     Prevents access to unauthorized devices or hosts
by checking the destination MAC address of outbound traffic.

·     Controls MAC address learning and authentication
on a port to make sure the port learns only source trusted MAC addresses.

### Port security features

#### NTK

The need to know (NTK) feature prevents
traffic interception by checking the destination MAC address in the outbound
frames. The feature ensures that frames are sent only to the following hosts:

·     Hosts that have passed authentication.

·     Hosts whose MAC addresses have been learned or
configured on the access device.

#### Intrusion protection

The intrusion protection feature checks the
source MAC address in inbound frames for illegal frames, and takes a predefined
action on each detected illegal frame. The action can be disabling the port
temporarily, disabling the port permanently, or blocking frames from the
illegal MAC address for a period set by the MAC block timer.

A frame is illegal if its source MAC
address cannot be learned in a port security mode or it is from a client that
has failed 802.1X or MAC authentication.

### Port security modes

Port security supports the following
categories of security modes:

·     MAC learning control—Includes two modes: autoLearn and secure. MAC address learning is
permitted on a port in autoLearn mode and disabled in secure mode. 

·     Authentication—Security modes in this category implement MAC authentication,
802.1X authentication, or a combination of these two authentication methods.

Upon receiving a frame, the port in a
security mode searches the MAC address table for the source MAC address. If a
match is found, the port forwards the frame. If no match is found, the port
learns the MAC address or performs authentication, depending on the security
mode. If the frame is illegal, the port takes the predefined NTK or intrusion
protection action, or sends SNMP notifications. Outgoing frames are not
restricted by port security's NTK action unless they trigger the NTK feature.

[Table 1](#_Ref246155685)
describes the port security modes and the security
features. 

Table 1 Port security modes

| Purpose | Security mode | | Features that can be triggered |
| --- | --- | --- | --- |
| Turning off the port security feature | noRestrictions (the default mode)  In this mode, port security is disabled on the port and access to the port is not restricted. | | N/A || [Controlling MAC address learning](#_Ref244502546) | autoLearn | | NTK/intrusion protection || secure | || [Performing 802.1X authentication](#_Ref244502548) | userLogin | | NTK (ntkauto mode) || userLoginSecure | | NTK/intrusion protection || userLoginSecureExt | || userLoginWithOUI | || [Performing MAC authentication](#_Ref244502550) | macAddressWithRadius | | NTK/intrusion protection || [Performing a combination of MAC authentication and 802.1X authentication](#_Ref244502552) | Or | macAddressOrUserLoginSecure | NTK/intrusion protection || macAddressOrUserLoginSecureExt || Else | macAddressElseUserLoginSecure || macAddressElseUserLoginSecureExt || And | macAddressAndUserLoginSecureExt |













 

The mode names are illustrated as follows:

·     userLogin specifies 802.1X authentication and port-based access control. userLogin with Secure
specifies 802.1X authentication and MAC-based access control. Ext indicates allowing multiple 802.1X users to be
authenticated and serviced at the same time. A security mode without Ext allows only one user to pass 802.1X authentication.

·     macAddress specifies MAC authentication.

·     Else specifies that the authentication method before Else is applied first. If the authentication fails,
whether to turn to the authentication method following Else
depends on the protocol type of the authentication request.

·     Or specifies that the authentication method following Or is applied first. If the authentication fails, the authentication
method before Or is applied.

·     And indicates that the authentication method before And is applied first. After the authentication succeeds,
the system turns to the authentication method that follows And upon receiving the request of the authentication
method.

#### Controlling MAC address learning

·     autoLearn.

A port in this mode can learn MAC
addresses. The automatically learned MAC addresses are not added to the MAC
address table as dynamic MAC address. Instead, these MAC addresses are added to
the secure MAC address table as secure MAC addresses. You can also configure
secure MAC addresses by using the port-security mac-address security command.

A port in autoLearn mode allows frames sourced
from the following MAC addresses to pass:

¡     Secure
MAC addresses.

¡     MAC
addresses configured by using the mac-address dynamic and mac-address static commands.

When the number of secure MAC addresses
reaches the upper limit, the port transitions to secure mode.

·     secure.

MAC address learning is disabled on a
port in secure mode. You configure MAC addresses by using the mac-address
static and mac-address dynamic commands. For more information about configuring MAC address table
entries, see Layer 2—LAN Switching Configuration Guide.

A port in secure mode allows only frames
sourced from the following MAC addresses to pass:

¡     Secure
MAC addresses.

¡     MAC
addresses configured by using the mac-address dynamic and mac-address static commands.

#### Performing 802.1X authentication

·     userLogin.

A port in this mode performs 802.1X
authentication and implements port-based access control. The port can service
multiple 802.1X users. Once an 802.1X user passes authentication on the port,
any subsequent 802.1X users can access the network through the port without
authentication.

·     userLoginSecure.

A port in this mode performs 802.1X
authentication and implements MAC-based access control. The port services only
one user passing 802.1X authentication.

·     userLoginSecureExt.

This mode is similar to the userLoginSecure
mode except that this mode supports multiple online 802.1X users.

·     userLoginWithOUI.

This mode is similar to the
userLoginSecure mode. The difference is that a port in this mode also permits
frames from one user whose MAC address contains a specific OUI.

In this mode, the port performs OUI check
at first. If the OUI check fails, the port performs 802.1X authentication. The
port permits frames that pass OUI check or 802.1X authentication.

 

|  | NOTE:  An OUI is a 24-bit number that uniquely identifies a vendor, manufacturer, or organization. In MAC addresses, the first three octets are the OUI. |
| --- | --- |

 

#### Performing MAC authentication

macAddressWithRadius: A port in this mode
performs MAC authentication, and services multiple users.

#### Performing a combination of MAC authentication and 802.1X authentication

·     macAddressOrUserLoginSecure.

This mode is the combination of the macAddressWithRadius
and userLoginSecure modes. The mode allows one 802.1X authentication user and
multiple MAC authentication users to log in.

In this mode, the port performs 802.1X
authentication first. By default, if 802.1X authentication fails, MAC
authentication is performed.

However, the port in this mode processes
authentication differently when the following conditions exist:

¡     The
port is enabled with parallel processing of MAC authentication and 802.1X
authentication.

¡     The
port is enabled with the 802.1X unicast trigger.

¡     The
port receives a packet from an unknown MAC address.

Under such conditions, the port sends a
unicast EAP-Request/Identity packet to the MAC address to initiate 802.1X
authentication. After that, the port immediately processes MAC authentication
without waiting for the 802.1X authentication result.

·     macAddressOrUserLoginSecureExt.

This mode is similar to the
macAddressOrUserLoginSecure mode, except that this mode supports multiple 802.1X
and MAC authentication users.

·     macAddressElseUserLoginSecure.

This mode is the combination of the
macAddressWithRadius and userLoginSecure modes, with MAC authentication having
a higher priority as the Else keyword implies. The mode allows one 802.1X authentication user and
multiple MAC authentication users to log in.

In this mode, the port performs MAC
authentication upon receiving non-802.1X frames. Upon receiving 802.1X frames, the
port performs MAC authentication and then, if the authentication fails, 802.1X
authentication.

·     macAddressElseUserLoginSecureExt.

This mode is similar to the
macAddressElseUserLoginSecure mode except that this mode supports multiple
802.1X and MAC authentication users as the Ext keyword implies.

·     macAddressAndUserLoginSecureExt.

In this mode, a user must pass both MAC
authentication and 802.1X authentication to access the authorized network
resources.

The device uses the following process to
handle an access user on a port operating in this mode:

**a.**Performs MAC authentication for the user.

**b.**Marks the user as a temporary MAC
authentication user when the user passes MAC authentication. A temporary MAC
authentication user can access only resources in the 802.1X guest VLAN or VSI.

**c.**After receiving 802.1X protocol packets from
the user on the port, the device performs 802.1X authentication for the user.

**d.**After the user passes 802.1X authentication
on the port, the device removes the temporary MAC authentication user entry.
Then, the user comes online as an 802.1X user. 

### Authentication load sharing modes in M-LAG environments

As shown in [Figure 1](#_Ref82023368), use
M-LAG to configure Device A and Device B into an M-LAG system for load sharing
and node redundancy. When one of the devices fails, traffic is switched to the
other device to ensure service continuity.

For users attached to the M-LAG interfaces
on an M-LAG system, port security provides the following authentication load
sharing modes:

·     Centralized mode—In this mode, the primary M-LAG member device processes authentication
services for all users attached to any M-LAG interfaces in the system.

·     Distributed modes—In a distributed mode, both M-LAG member devices provide authentication
services for users attached to the M-LAG interfaces. Port security provides the
following distributed authentication processing modes:

¡     Distributed local mode—Each M-LAG
member device processes authentication for users attached to their local M-LAG
interfaces.

¡     Distributed even-/odd-MAC mode—In
this mode, one M-LAG member device processes authentication for packets with an
even source MAC address and the other M-LAG member device processes
authentication for packets with an odd source MAC address. In this mode, you
must configure one M-LAG member device to process authentication for even
source MAC addresses and configure the other M-LAG member device to process
authentication for odd source MAC addresses.

One M-LAG member device automatically
synchronizes user data to the other M-LAG member device upon each successful
user authentication. This ensures that when one M-LAG member device fails, the other
member device can take over to process authentication services for all users.

Figure 1 M-LAG network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705397_x_Img_x_png_0_2216068_294551_0.png)

 

## Restrictions and guidelines: Port security configuration

This feature applies to networks, such as a
WLAN, that require different authentication methods for different users on a
port.

As a best practice, use the 802.1X
authentication or MAC authentication feature rather than port security for
scenarios that require only 802.1X authentication or MAC authentication. For
more information about 802.1X and MAC authentication, see "Configuring
802.1X" and "Configuring MAC authentication."

The secure and userlogin-withoui port
security modes are not supported on Layer 2 aggregate interfaces. Other port
security settings are supported on both Layer 2 Ethernet interfaces and Layer 2
aggregate interfaces.

After a Layer 2 Ethernet interface is added
to an aggregation group, port security settings on the interface do not take
effect.

Do not delete a Layer 2 aggregate interface
if the interface has online 802.1X or MAC authentication users.

When RADIUS DAS is enabled, an M-LAG system
does not support the following operations:

·     Shuts down or reboots the access ports for
802.1X, Web authentication, MAC authentication users by sending CoA messages.

·     Reauthenticates these users by sending CoA
messages.

For more information about RADIUS DAS, see
"Configuring AAA."

To ensure a successful HTTPS redirect for
users in either of the following situations, make sure VLAN interfaces or VSI
interfaces exist for the VLANs or VSIs that transport their packets,
respectively:

·     The users are assigned a redirect URL.

·     EAD assistant is enabled.

## Port security tasks at a glance

To configure port security, perform the
following tasks:

**1\.**Configuring basic features of port security

¡     [Enabling port security](#_Ref478404721)

¡     [Setting the port security mode](#_Ref478404726)

¡     (Optional.) [Setting port security's limit on the number
of secure MAC addresses on a port](#_Ref478404731)

¡     (Optional.) [Configuring secure MAC addresses](#_Ref478404739)

¡     (Optional.)
[Configuring NTK](#_Ref324753996)

¡     (Optional.) [Configuring intrusion protection](#_Ref478404747)

¡     [Configuring an authentication load sharing
mode for users attached to M-LAG interfaces](#_Ref82023391)

Perform this task on an M-LAG system.

**2\.**(Optional.) Configuring extended features of
port security

¡     [Ignoring authorization information from the
server](#_Ref478404777)

¡     [Configuring MAC move](#_Ref340243862)

¡     [Enabling the authorization-fail-offline
feature](#_Ref389052838)

¡     [Setting port security's limit on the number
of MAC addresses for specific VLANs on a port](#_Ref480204069)

¡     [Enabling open authentication mode](#_Ref462406885)

¡     [Configuring free VLANs for port security](#_Ref462406882)

¡     [Applying a NAS-ID profile to port security](#_Ref480204104)

¡     [Enabling the escape critical VSI feature
for 802.1X and MAC authentication users](#_Ref527124714)

¡     [Configuring the trigger order for authentication
methods on a port](#_Ref92889492)

¡     [Specifying a preauthentication domain for
port security users on a port](#_Ref101263806)

¡     [Specifying a domain for port security users
redirected to an unavailable URL](#_Ref95910829)

¡     [Configuring static users for port access
authentication](#_Ref101263814)

\-     [Configuring a static user range for port
access authentication](#_Ref101263828)

\-     [Enabling static user IP update](#_Ref101263834)

\-     [Setting the maximum number of concurrent
static users allowed on a port](#_Ref101263841)

\-     [Specifying an ACL to match the MAC
addresses of static users](#_Ref106109741)

¡     [Specifying an IP address and mask for calculating
the source IP of ARP detection packets](#_Ref115897097)

¡     [Setting port security timers](#_Ref97559738)

The extended port security features can
also take effect when port security is disabled but 802.1X or MAC
authentication is enabled.

**3\.**(Optional.) [Enabling SNMP notifications for port
security](#_Ref484188398)

**4\.**(Optional.) [Enabling port security user logging](#_Ref476302379)

## Enabling port security

#### Restrictions and guidelines

When you configure port security, follow these
restrictions and guidelines:

·     When port security is enabled, you cannot enable
802.1X or MAC authentication, or change the access control mode or port
authorization state. Port security automatically modifies these settings in
different security modes.

·     You can use the undo
port-security enable command to disable port
security. Because the command logs off online users, make sure no online users
are present.

·     Enabling or disabling port security resets the
following security settings to the default: 

¡     802.1X
access control mode, which is MAC-based.

¡     Port
authorization state, which is auto.

For more information about 802.1X
authentication and MAC authentication configuration, see "Configuring
802.1X" and "Configuring MAC authentication."

#### Prerequisites

Before you enable port security, disable
802.1X and MAC authentication globally.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enable port security.

port-security enable

By default, port security is disabled.

## Setting the port security mode

#### Restrictions and guidelines

You can specify a port security mode when
port security is disabled, but your configuration cannot take effect.

Changing the port security mode of a port
logs off the online users of the port.

Do not enable 802.1X authentication or MAC
authentication on a port where port security is enabled.

After enabling port security, you can
change the port security mode of a port only when the port is operating in
noRestrictions (the default) mode. To change the port security mode for a port in
any other mode, first use the undo port-security port-mode command to restore the default port security mode.

The device supports the URL attribute
assigned by a RADIUS server in the following port security modes:

·     mac-authentication.

·     mac-else-userlogin-secure.

·     mac-else-userlogin-secure-ext.

·     userlogin-secure.

·     userlogin-secure-ext.

·     userlogin-secure-or-mac.

·     userlogin-secure-or-mac-ext.

·     userlogin-withoui.

During authentication, the HTTP or HTTPS
requests of a user are redirected to the Web interface specified by the
server-assigned URL attribute. After the user passes the Web authentication,
the RADIUS server records the MAC address of the user and uses a DM (Disconnect
Message) to log off the user. When the user initiates 802.1X or MAC authentication
again, it will pass the authentication and come online successfully.

When the port security mode is macAddressAndUserLoginSecureExt on a port, follow these
restrictions and guidelines:

·     To make sure the 802.1X clients attached to the
port can initiate authentication, enable unicast trigger on the port by using
the dot1x
unicast-trigger command.

·     The MAC authentication guest VLAN or VSI on the
port does not take effect. For the temporary MAC authentication users to access
a limited set of resources, configure an 802.1X guest VLAN or VSI on the port.

·     If accounting is not required for the temporary
MAC authentication users, configure different ISP domains for MAC authentication
users and 802.1X users. In the ISP domain for MAC authentication users, set the
accounting method to none.

To redirect the HTTPS requests of port
security users, specify the HTTPS redirect listening port on the device. For
more information, see HTTP redirect in Layer 3—IP
Services Configuration Guide. 

#### Prerequisites

Before you set a port security mode for a
port, complete the following tasks:

·     Disable 802.1X and MAC authentication. 

·     If you are configuring the autoLearn mode, set port
security's limit on the number of secure MAC addresses. You cannot change the
setting when the port is operating in autoLearn mode. 

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Set an OUI value for user authentication.

port-security oui index index-value mac-address oui-value

By default, no OUI values are configured
for user authentication.

This command is required only for the userlogin-withoui mode.

You can set multiple OUIs, but when the
port security mode is userlogin-withoui, the port allows one 802.1X user and only one user that matches one
of the specified OUIs.

**3\.**Enter interface view.

interface interface-type
interface-number

**4\.**Set the port security mode.

port-security port-mode { autolearn \| mac-and-userlogin-secure-ext \| mac-authentication \| mac-else-userlogin-secure \| mac-else-userlogin-secure-ext \| secure \| userlogin \| userlogin-secure \| userlogin-secure-ext \| userlogin-secure-or-mac \| userlogin-secure-or-mac-ext \|
userlogin-withoui }

By default, a port operates in
noRestrictions mode.

## Setting port security's limit on the number of secure MAC addresses on a port

#### About this task

You can set the maximum number of secure
MAC addresses that port security allows on a port for the following purposes:

·     Controlling the number of concurrent users on
the port.

For a port operating in a security mode (except
for autoLearn and secure), the upper limit equals the smaller of the following
values:

¡     The limit
of the secure MAC addresses that port security allows.

¡     The limit
of concurrent users allowed by the authentication mode in use.

·     Controlling the number of secure MAC addresses
on the port in autoLearn mode.

You can also set the maximum number of
secure MAC addresses that port security allows for specific VLANs or each VLAN
on a port.

Port security's limit on the number of
secure MAC addresses on a port is independent of the MAC learning limit
described in MAC address table configuration. For more information about MAC
address table configuration, see Layer 2—LAN Switching
Configuration Guide.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter interface view.

interface interface-type interface-number

**3\.**Set the maximum number of secure MAC
addresses allowed on a port.

port-security max-mac-count max-count\[ vlan \[ vlan-id-list
] ]

By default, port security does not limit
the number of secure MAC addresses on a port.

## Configuring secure MAC addresses

### About secure MAC addresses

Secure MAC addresses are configured or
learned in autoLearn mode. If the secure MAC addresses are saved, they can survive
a device reboot. You can bind a secure MAC address only to one port in a VLAN.

Secure MAC addresses include static, sticky,
and dynamic secure MAC addresses.

Table 2 Comparison of static, sticky, and dynamic secure MAC addresses

| Type | Address sources | Aging mechanism | Can be saved and survive a device reboot? |
| --- | --- | --- | --- |
| Static | Manually added (by using the port-security mac-address security command without the sticky keyword). | Not available.  The static secure MAC addresses never age out unless you perform any of the following tasks:  ·     Manually remove these MAC addresses.  ·     Change the port security mode.  ·     Disable the port security feature. | Yes. || Sticky | ·     Manually added (by using the port-security mac-address security command with the sticky keyword).  ·     Converted from dynamic secure MAC addresses.  ·     Automatically learned when the dynamic secure MAC feature is disabled. | By default, sticky MAC addresses do not age out. However, you can configure an aging timer or use the aging timer together with the inactivity aging feature to remove old sticky MAC addresses.  ·     If only the aging timer is configured, the aging timer counts up regardless of whether traffic data has been sent from the sticky MAC addresses.  ·     If both the aging timer and the inactivity aging feature are configured, the aging timer restarts once traffic data is detected from the sticky MAC addresses. | Yes.  The secure MAC aging timer restarts at a reboot. || Dynamic | ·     Converted from sticky MAC addresses.  ·     Automatically learned after the dynamic secure MAC feature is enabled. | Same as sticky MAC addresses. | No.  All dynamic secure MAC addresses are lost at reboot. |



 

When the maximum number of secure MAC
address entries is reached, the port changes to secure mode. In secure mode,
the port cannot add or learn any more secure MAC addresses. The port allows
only frames sourced from secure MAC addresses or MAC addresses configured by
using the mac-address dynamic or mac-address
static command to pass through.

### Prerequisites

Before you configure secure MAC addresses,
complete the following tasks:

·     Set port security's limit on the number of MAC
addresses on the port. Perform this task before you enable autoLearn mode.

·     Set the port security mode to autoLearn.

·     Configure the port to permit packets of the
specified VLAN to pass or add the port to the VLAN. Make sure the VLAN already
exists.

### Adding secure MAC addresses

**1\.**Enter system view.

system-view

**2\.**Set the secure MAC aging timer.

port-security timer autolearn aging \[ second ] time-value

By default, secure MAC addresses do not
age out.

**3\.**Configure a secure MAC address.

¡     Configure
a secure MAC address in system view.

port-security mac-address security \[ sticky ] mac-address interface interface-type interface-number vlan vlan-id

¡     Execute
the following commands in sequence to configure a secure MAC address in
interface view:

interface interface-type
interface-number

port-security mac-address security \[ sticky ] mac-address vlan vlan-id

By default, no manually configured secure
MAC addresses exist.

In a VLAN, a MAC address cannot be
specified as both a static secure MAC address and a sticky MAC address.

### Enabling inactivity aging for secure MAC addresses

**1\.**Enter system view.

system-view

**2\.**Enter interface view.

interface interface-type interface-number

**3\.**Enable inactivity aging for secure MAC
addresses.

port-security mac-address aging-type inactivity

By default, the inactivity aging feature
is disabled for secure MAC addresses.

### Enabling the dynamic secure MAC feature

**1\.**Enter system view.

system-view

**2\.**Enter interface view.

interface interface-type interface-number

**3\.**Enable the dynamic secure MAC feature.

port-security mac-address dynamic

By default, the dynamic secure MAC feature
is disabled. Sticky MAC addresses can be saved to the configuration file. Once
saved, they can survive a device reboot.

## Configuring NTK

#### About this task

The NTK feature checks the destination MAC
address in outbound frames to make sure frames are forwarded only to trustworthy
devices.

The NTK feature supports the following
modes:

·     ntkonly—Forwards only unicast frames with a known destination MAC address.

·     ntk-withbroadcasts—Forwards only broadcast and unicast frames with a known destination
MAC address.

·     ntk-withmulticasts—Forwards only broadcast, multicast, and unicast frames with a known
destination MAC address.

·     ntkauto—Forwards only broadcast, multicast, and unicast frames with a known
destination MAC address, and only when the port has online users.

#### Restrictions and guidelines

The NTK feature drops any unicast frame
with an unknown destination MAC address.

Not all port security modes support triggering
the NTK feature. For more information, see [Table 1](#_Ref246155685).

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter interface view.

interface interface-type
interface-number

**3\.**Configure the NTK feature.

port-security ntk-mode { ntk-withbroadcasts \| ntk-withmulticasts \| ntkauto \| ntkonly }

By default, NTK is disabled on a port and
all frames are allowed to be sent.

## Configuring intrusion protection

#### About this task

Intrusion protection takes one of the
following actions on a port in response to illegal frames:

·     blockmac—Adds the source MAC addresses of illegal frames to the blocked MAC
address list, and then discards frames sourced from blocked MAC addresses for a
period set by the block timer. A blocked MAC address will be unblocked when the
block timer expires. 

·     disableport—Disables the port until you bring it up manually.

·     disableport-temporarily—Disables the port for a period of time. The period can be
configured with the port-security timer disableport command.

#### Restrictions and guidelines

On a port operating in either
macAddressElseUserLoginSecure mode or macAddressElseUserLoginSecureExt mode,
intrusion protection is triggered only after both MAC authentication and 802.1X
authentication fail for the same frame.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter interface view.

interface interface-type
interface-number

**3\.**Configure the intrusion protection feature.

port-security intrusion-mode { blockmac \| disableport \| disableport-temporarily
}

By default, intrusion protection is
disabled.

**4\.**Return to system view.

quit

**5\.**(Optional.) Set the silence timeout period
during which a port remains disabled.

port-security timer disableport time-value

By default, the port silence timeout period
is 20 seconds.

**6\.**(Optional.) Set the block timer for blocked
MAC addresses.

port-security timer blockmac time-value

By default, the block timer is 180
seconds.

## Configuring an authentication load sharing mode for users attached to M-LAG interfaces

#### About this task

This feature takes effect only on 802.1X, Web
authentication, and MAC authentication users attached to M-LAG interfaces in an
M-LAG environment. 

In an M-LAG system, the M-LAG member
devices exchange configuration information with each other to check for configuration
conflicts. If a configuration conflict exists, the M-LAG member devices do not
allow new users to come online.

For more information about M-LAG, see Layer 2—LAN Switching Configuration Guide.

#### Restrictions and guidelines

| CAUTION | CAUTION:  To avoid user logoffs caused by configuration conflicts, do not change the authentication load sharing mode for users on M-LAG interfaces when port security, 802.1X, Web authentication, or MAC authentication is enabled. |
| --- | --- |

 

Make sure the M-LAG member devices are
consistent in authentication load sharing settings for users attached to M-LAG
interfaces. 

·     Centralized mode—Configure both devices to operate in centralized mode for user
authentication.

·     Distributed local mode—Configure both M-LAG member devices to operate in distributed local
mode for user authentication.

·     Distributed even-/odd-MAC
mode—Configure one M-LAG member device in
distributed even-MAC mode and the other to operate in distributed odd-MAC mode
for user authentication.

To ensure correct user data processing,
follow these guidelines when you configure the peer aggregate interfaces on
each remote access device connected to the M-LAG interfaces:

·     If the M-LAG system uses distributed local mode
for user authentication, link-aggregation load sharing on the access device can
only be based on one of the following criteria:

¡     Destination
IP address.

¡     Destination
MAC address.

¡     Source
IP address.

¡     Source
MAC address.

·     In an 802.1X authentication scenario, you must
configure the access device to ignore all packet fields except the source MAC if
it uses the default link-aggregation load sharing mode.

In centralized mode, if all member ports of
an M-LAG interface belong only to one M-LAG member device and the M-LAG
interface forwards authentication traffic, users attached to the M-LAG
interface cannot come online. To ensure that users attached to such M-LAG
interfaces can come online, do not set the authentication load sharing mode to
centralized mode.

For more information about M-LAG, see Layer 2—LAN Switching Configuration Guide. For more
information about link aggregation load sharing, see Ethernet link aggregation
in Layer 2—LAN Switching Configuration Guide.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Set the authentication load sharing mode for
users attached to M-LAG interfaces.

port-security m-lag load-sharing-mode { centralized \| distributed { even-mac \| local \| odd-mac } }

By default, local mode applies. Each M-LAG member device authenticates
users on its local M-LAG interfaces.

## Ignoring authorization information from the server

#### About this task

You can configure a port to ignore the
authorization information received from the server (local or remote) after an
802.1X or MAC authentication user passes authentication.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter interface view.

interface interface-type
interface-number

**3\.**Ignore the authorization information
received from the authentication server.

port-security authorization ignore

By default, a port uses the authorization
information received from the authentication server.

## Configuring MAC move

#### About this task

Port security MAC move takes effect on
online users authenticated through 802.1X authentication, MAC authentication,
or Web authentication in the following scenarios:

·     Inter-port move on a device—An authenticated online user moves between ports on the device. The
user VLANor authentication
method might change or stay unchanged after the move.

·     Inter-VLAN move on a port—An authenticated online user moves between VLANs on a trunk or
hybrid port. This mode takes effect only when the packets that trigger
authentication are VLAN tagged.

Port security MAC move allows an
authenticated online user on one port or VLAN to be reauthenticated and come
online on another port or VLAN without going offline first. After the user
passes authentication on the new port or VLAN, the system removes the authentication
session of the user on the original port or VLAN. This action ensures that the
user stays online on only one port in one VLAN.

‌

|  | NOTE:  For MAC authentication, the MAC move feature applies only when MAC authentication single-VLAN mode is used. The MAC move feature does not apply to MAC authentication users that move between VLANs on a port with MAC authentication multi-VLAN mode enabled. |
| --- | --- |

‌

If this feature is disabled, authenticated
users must go offline first before they can be reauthenticated successfully on
a new port or VLAN to come online. 

For a user moving between ports, the port
from which the user moves is called the source port and the port to which the
user moves is called the destination port.

On the destination port, an authenticated user
will reauthenticate in the VLAN authorized on the source port if the source
port is enabled with MAC-based VLAN. If that VLAN is not permitted to pass
through on the destination port, reauthentication will fail. To avoid this
situation, enable VLAN check bypass on the destination port.

#### Restrictions and guidelines

As a best practice to minimize security
risks, enable MAC move only if user roaming between ports is required.

Authenticated users cannot move between
ports on a device or between VLANs on a port if the maximum number of online
users on the authentication server has been reached.

MAC authentication multi-VLAN mode has
higher priority than MAC move for users moving between VLANs on a port. If MAC
authentication multi-VLAN mode is enabled, these users can come online in the
new VLAN without being reauthenticated. To enable MAC authentication multi-VLAN
mode, use the mac-authentication host-mode multi-vlan command. For more information about MAC authentication multi-VLAN
mode, see "Configuring MAC authentication."

When you configure VLAN check bypass for users
moving between ports, follow these guidelines:

·     To ensure a successful reauthentication, enable
VLAN check bypass on a destination port if the source port is enabled with
MAC-based VLAN. 

·     If the destination port is an 802.1X-enabled
trunk port, you must configure it to send 802.1X protocol packets without VLAN
tags. For more information, see "Configuring 802.1X."

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enable MAC move.

port-security mac-move permit \[ port \| vlan ]

By default, MAC move is disabled.

**3\.**(Optional.) Enable VLAN check bypass for
users moving between ports.

**a.**Enter interface view.

interface interface-type
interface-number

**b.**Enable VLAN check bypass for users moving to
the port from other ports.

port-security mac-move bypass-vlan-check

By default, the VLAN check bypass feature
is disabled.

## Enabling the authorization-fail-offline feature

#### About the authorization-fail-offline feature

| IMPORTANT | IMPORTANT:  The authorization-fail-offline feature takes effect only on port security users that have failed ACL, CAR, or user profile authorization. |
| --- | --- |

 

The authorization-fail-offline feature logs
off port security users that have failed authorization.

A user fails authorization in the following
situations:

·     The device or server fails to assign the
specified authorization attribute to the user.

·     The device or server assigns authorization
information that does not exist on the device to the user.

This feature does not apply to users that have
failed VLAN authorization. The device logs off these users directly.

You can also enable the quiet timer feature
for 802.1X or MAC authentication users that are logged off by the
authorization-fail-offline feature. The device adds these users to the 802.1X
or MAC authentication quiet queue. Within the quiet timer, the device does not
process packets from these users or authenticate them. If you do not enable the
quiet timer feature, the device immediately authenticates these users upon
receiving packets from them.

#### Prerequisites

For the quiet timer feature to take effect,
complete the following tasks:

·     For 802.1X users, use the dot1x
quiet-period command to enable the quiet timer
and use the dot1x timer quiet-period command to set the timer.

·     For MAC authentication users, use the mac-authentication
timer quiet command to set the quiet timer for
MAC authentication.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enable the authorization-fail-offline
feature.

port-security authorization-fail offline \[ quiet-period ]

By default, this feature is disabled, and
the device does not log off users that have failed authorization.

## Setting port security's limit on the number of MAC addresses for specific VLANs on a port

#### About this task

Typically, port security allows the access
of the following types of MAC addresses on a port:

·     MAC addresses that pass 802.1X authentication or MAC authentication.

·     MAC addresses in the MAC authentication guest or
critical VLAN, MAC addresses in the MAC authentication guest or critical VSI,
and MAC addresses in the MAC authentication critical microsegment.

·     MAC addresses in the 802.1X guest, Auth-Fail, or
critical VLAN and MAC addresses in the 802.1X guest, Auth-Fail, or critical VSI.

·     MAC addresses that pass Web authentication and MAC
addresses in the Web authentication Auth-Fail VLAN.

This feature limits the number of MAC
addresses that port security allows to access a port through specific VLANs.
Use this feature to prevent resource contentions among MAC addresses and ensure
reliable performance for each access user on the port. When the number of MAC
addresses in a VLAN on the port reaches the upper limit, the device denies any
subsequent MAC addresses in the VLAN on the port.

#### Restrictions and guidelines

On a port, the maximum number of MAC
addresses in a VLAN cannot be smaller than the number of existing MAC addresses
in the VLAN. If the specified maximum number is smaller, the setting does not
take effect.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter interface view.

interface interface-type interface-number

**3\.**Set port security's limit on the number of
MAC addresses for specific VLANs on the port.

port-security mac-limit
max-number per-vlan vlan-id-list

The default setting is 2147483647. 

## Enabling open authentication mode

#### About this task

This feature enables access users (802.1X
or MAC authentication users) of a port to come online and access the network
even if they use nonexistent usernames or incorrect passwords.

Access users that come online in open
authentication mode are called open users. Authorization and accounting are not
available for open users. To display open user information, use the following
commands:

·     display dot1x connection open.

·     display mac-authentication connection open.

This feature does not affect the access of
users that use correct user information.

#### Restrictions and guidelines

When you configure open authentication
mode, follow these restrictions and guidelines:

·     If global open authentication mode is enabled,
all ports are enabled with open authentication mode regardless of the
port-specific open authentication mode setting. If global open authentication
mode is disabled, whether a port is enabled with open authentication mode
depends on the port-specific open authentication mode setting.

·     The open authentication mode setting has lower
priority than the 802.1X Auth-Fail VLAN and the MAC authentication guest VLAN.
Open authentication mode does not take effect on a port if the port is also
configured with the 802.1X Auth-Fail VLAN or the MAC authentication guest VLAN.
For information about 802.1X authentication and MAC authentication, see
"802.1X overview," "Configuring 802.1X," and
"Configuring MAC authentication." 

·     The open authentication mode setting has lower
priority than the 802.1X Auth-Fail VSI and the MAC authentication guest VSI.
Open authentication mode does not take effect on a port if the port is also
configured with the 802.1X Auth-Fail VSI or the MAC authentication guest VSI. For
information about 802.1X authentication and MAC authentication, see
"802.1X overview," "Configuring 802.1X," and
"Configuring MAC authentication."

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enable global open authentication mode.

port-security authentication open global

By default, global open authentication
mode is disabled.

**3\.**Enter interface view.

interface interface-type interface-number

**4\.**Enable open authentication mode on the port.

port-security authentication open

By default, open authentication mode is
disabled on a port.

## Configuring free VLANs for port security

#### About this task

This feature allows packets from the
specified VLANs to not trigger 802.1X or MAC authentication on a port
configured with any of the following features:

·     802.1X authentication.

·     MAC authentication.

·     One of the following port security modes:

¡     userLogin.

¡     userLoginSecure.

¡     userLoginWithOUI.

¡     userLoginSecureExt.

¡     macAddressWithRadius.

¡     macAddressOrUserLoginSecure.

¡     macAddressElseUserLoginSecure.

¡     macAddressOrUserLoginSecureExt.

¡     macAddressElseUserLoginSecureExt.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter interface view.

interface interface-type interface-number

**3\.**Configure free VLANs for port security.

port-security free-vlan
vlan-id-list

By default, no free VLANs for port
security exist on a port.

## Applying a NAS-ID profile to port security

#### About this task

By default, the device sends its device
name in the NAS-Identifier attribute of all RADIUS requests.

A NAS-ID profile enables you to send
different NAS-Identifier attribute strings in RADIUS requests from different
VLANs. The strings can be organization names, service names, or any user
categorization criteria, depending on the administrative requirements.

For example, map the NAS-ID companyA to all VLANs of company A. The device will send companyA in the NAS-Identifier attribute for the RADIUS
server to identify requests from any Company A users.

#### Restrictions and guidelines

You can apply a NAS-ID profile to port
security globally or on a port. On a port, the device selects a NAS-ID profile
in the following order:

**1\.**The port-specific NAS-ID profile.

**2\.**The NAS-ID profile applied globally.

If no NAS-ID profile is applied or no
matching binding is found in the selected profile, the device uses the device
name as the NAS-ID.

For more information about the NAS-ID
profile configuration, see "Configuring AAA."

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Apply a NAS-ID profile.

¡     Apply
a NAS-ID profile globally.

port-security nas-id-profile profile-name

¡     Execute
the following commands in sequence to apply a NAS-ID profile to an interface:

interface interface-type
interface-number

port-security nas-id-profile profile-name

By default, no NAS-ID profile is applied in
system view or in interface view.

## Enabling the escapecritical VSI feature for 802.1X and MAC authentication users

#### About this task

The escape critical VSI feature operates on
VXLAN networks. It enables 802.1X and MAC authentication users to escape the
authentication failure that occurs because the RADIUS server is malfunctioning.

You can enable this feature temporarily to
prevent 802.1X and MAC authentication service interruption while you are
troubleshooting a malfunctioning RADIUS server. 

After the escape critical VSI feature is
enabled on a port, the device performs the following operations when 802.1X or
MAC authentication is triggered for a user:

**1\.**Dynamically creates an Ethernet service
instance that matches the user's access VLAN and MAC address on the access
port.

**2\.**Maps the Ethernet service instance to the
802.1X or MAC authentication critical VSI on the port. 

The user can then come online without
authentication and access resources in the VXLAN associated with the critical
VSI.

The escape critical VSI feature does not
affect 802.1X or MAC authentication users that have been online before this
feature is enabled.

#### Restrictions and guidelines

For the escape critical VSI feature to work
correctly on a port, make sure the port does not have the following settings:

·     Web authentication.

·     Guest, Auth-Fail, or critical VLAN for 802.1X
authentication.

·     Guest or critical VLAN for MAC authentication.

You can enable or disable this feature
globally on all ports or on a per-port basis. 

If the mac-authentication critical vsi critical-vsi-name
url-user-logoff command is used in
conjunction with this feature, MAC authentication users that have been assigned
authorization URLs on the port will be logged off. For more information, see
"Configuring MAC authentication."

When you disable the escape critical VSI
both globally and on a port, the device logs off the users in the critical VSIs
for 802.1X and MAC authentication on that port. The users must perform
authentication to come online again on that port.

The escape critical VSI feature does not
take effect on a new 802.1X or MAC authentication user if any of the following
conditions exists:

·     The 802.1X client and the device use different
EAP message handling methods.

·     802.1X MAC address binding is enabled on the
user's access port, but the MAC address of the 802.1X user is not bound to that
port.

·     The user's MAC address is an all-zero, all-F, or
multicast MAC address.

#### Prerequisites

Before you enable the escape critical VSI feature on a port, configure an 802.1X
critical VSI and a MAC authentication critical VSI on that port. For more
information about critical VSI configuration, see "Configuring
802.1X" and "Configuring MAC authentication."

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enable the escape critical VSI feature.

¡     Enable
the global escape critical VSI feature.

port-security global escape critical-vsi

¡     Execute
the following commands in sequence to enable the escape critical VSI feature on
a port:

interface interface-type
interface-number

port-security escape critical-vsi

By default, the escape critical VSI
feature is disabled.

## Configuring the trigger order for authentication methods on a port

#### About this task

This feature takes effect only on ports
with triple authentication enabled. Triple authentication allows Web
authentication, MAC authentication, and 802.1X authentication to be enabled
concurrently on a Layer 2 port for user access. Different types of endpoint
packets trigger different types of authentication first. For more information,
see "Configuring triple authentication."

To enable any endpoint packets to trigger
MAC authentication first, use this feature.

A port can run authentication processes concurrently
for multiple authentication methods. The failure of one authentication does not
affect the processes for other authentication methods. However, if an endpoint
passes one authentication on a port, the device handles processes for other
authentication methods on the port as follows:

·     If the endpoint passes MAC authentication, the
device generates a MAC authentication user entry on the port and continues to
perform 802.1X authentication for the endpoint on the port. However, the device
cannot continue Web authentication for the endpoint on the port. 

¡     If
the endpoint passes 802.1X authentication after MAC authentication, the device
generates an 802.1X user entry for the endpoint on the port. The 802.1X user
entry overwrites the MAC authentication user entry. 

¡     If
the endpoint does not pass 802.1X authentication after MAC authentication, the
MAC authentication user entry is retained on the port. The endpoint can trigger
802.1X authentication again, but it cannot trigger Web authentication.

·     If the endpoint fails MAC authentication but
passes 802.1X or Web authentication, the device immediately stops all authentication
methods on the port except the one the endpoint has passed. In addition, the device
can no longer trigger authentication processes for the stopped authentication
methods for the endpoint on the port.

#### Restrictions and guidelines

This feature causes users that are being
authenticated to fail authentication. The users must retrigger authentication
to come online. As a best practice to avoid users failing to come online, use
this feature with caution.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter interface view.

interface interface-type interface-number

**3\.**Configure the trigger order for
authentication methods on the port as MAC authentication, 802.1X
authentication, and Web authentication.

port-security triple-auth-order mac-dot1x-web

By default, the authentication that is
triggered first depends on the type of packets sent from endpoints in a triple
authentication environment.

## Specifying a preauthentication domain for port security users on a port

#### About this task

A preauthentication domain accommodates 802.1X,
Web authentication, and MAC authentication users that have not performed authentication.
A preauthentication domain is applicable to the following scenarios:

·     A user accesses the network for the first time.
This scenario is applicable only to 802.1X and Web authentication users.

·     A user fails authentication, but no Auth-Fail
domain is configured.

·     No server is reachable, but the critical domain
is not configured.

When a port is configured with a
preauthentication domain, authentication users that access that port will be
assigned authorization attributes (including ACL, microsegment, VSI, and VLAN)
in the preauthentication domain after they are assigned to the
preauthentication domain. They can access only network resources permitted in
the preauthentication domain. If they pass authentication, AAA will assign new
authorization information to them.

#### Restrictions and guidelines

If the ACL, microsegment, VSI, and VLAN
authorization settings in the current preauthentication domain have changes,
the changes take effect only on users that are assigned to the
preauthentication domain after the changes are made. Users that have been
assigned to the preauthentication domain before the changes are made still use
the original settings.

On a port, port security preauthentication
domain is mutually exclusive with the following settings:

·     802.1X guest VLAN, guest VSI, Auth-Fail VLAN, Auth-Fail
VSI, critical VLAN, critical VSI, critical microsegment, and critical profile
settings.

·     MAC authentication guest VLAN, guest VSI, critical
VLAN, critical VSI, critical microsegment, and critical profile settings.

·     Web authentication Auth-Fail VLAN settings. 

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter interface view.

interface interface-type interface-number

**3\.**Specify a preauthentication domain for port
security users.

port-security pre-auth domain isp-name

By default, no preauthentication domain
is specified for port security users on a port.

## Specifying a domain for port security users redirected to an unavailable URL

#### About this task

During user authentication, if the Web
server specified by the redirect URL is unavailable, users cannot be redirected
to the Web authentication page on the Web server. As a result, the users cannot
come online. To allow users to access the resources in an ISP domain when the
redirect URL is unavailable, use this feature to specify that ISP domain for the
users.

#### Restrictions and guidelines

This feature is applicable only to MAC
authentication and Web authentication users.

This feature is mutually exclusive with the
following 802.1X, MAC authentication, and Web authentication settings:

·     Guest VLAN and VSI settings.

·     Auth-Fail VLAN and VSI settings.

·     Critical VLAN, VSI, and microsegment settings.

·     Critical profile settings. 

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter interface view.

interface interface-type interface-number

**3\.**Specify a domain for port security users
redirected to an unavailable URL.

port-security url-unavailable domain isp-name

By default, no domain is specified for port
security users redirected to an unavailable URL.

## Configuring static users for port access authentication

### Configuring a static user range for port access authentication

#### About this task

Typically, administrators assign static IP
addresses to dumb terminals such as printers. For such users, you can configure
them as static users to facilitate flexible authentication. After you configure
a dumb terminal as a static user, the device can use the static user
information such as the user IP address as the username to perform
authentication for the dumb terminal on the user's access interface. For this
purpose, make sure 802.1X, Web authentication, or MAC authentication is enabled
on the user's access interface.

The device uses the following rules to
handle static users:

·     If two users have different IP addresses and MAC
addresses, the users are different static user. They both can come online.

·     If a user that has the same MAC address as an
online static user attempts to come online, the device does not trigger
authentication for the new user, regardless of whether the users use the same
IP address. To update the IP addresses of static users, use the port-security
static-user update-ip enable command.

·     If users that have the same IP address but
different MAC addresses come online, the device handles the users as follows:

¡     If a
MAC address has been bound to the IP address, the user that has the bound MAC
address can come online. Users with other MAC addresses cannot trigger
authentication to come online.

¡     If
no MAC address is bound to the IP address, the device allows all users that
have IP addresses belonging to a static user IP range to trigger authentication to come online.

#### Restrictions and guidelines

Modification to a static user range does
not affect static users that are already online. The modification takes effect
only on static users that will come online.

In the public network or the same VPN
instance, the IP address ranges for all static user ranges cannot overlap.

When the usernames of static users are
their IP or MAC addresses, do not enable RESTful server-assisted automatic MAC
authentication user recovery. If you enable RESTful server-assisted automatic MAC
authentication user recovery, the device will recover static users as MAC
authentication users after the device reboots or recovers from a failure. For
more information about RESTful server-assisted automatic MAC authentication
user recovery, see "Configuring MAC authentication."

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Configure a static user range for port
access authentication.

port-security static-user { ip \| ipv6 } start-ip-address \[ end-ip-address ] \[ vpn-instance
vpn-instance-name ] \[ domain isp-name \| \[ interface interface-type interface-number \[ detect ] ] vlan vlan-id \| mac mac-address \| keep-online ] \*

By default, no static user ranges are
configured for port access authentication.

**3\.**(Optional.) Configure the username format
used by static users when they come online.

port-security static-user user-name-format { ip-address \| mac-address \| system-name }

By default, the username of a static user
is in the format of SysnameIP, in which Sysname is the name of the access device and IP is the user IP address.

**4\.**(Optional.) Configure the user account
format when MAC addresses of static users are used as their usernames.

port-security static-user user-name-format mac-address { one-section \| { six-section \| three-section } delimiter { colon \| hyphen } } \[ uppercase ] \[ password-with-mac ]

By default, the username of a static user
is in the format of SysnameIP, in which Sysname is the name of the access device and IP is the user IP address.

**5\.**(Optional.) Configure a password for static
users.

port-security static-user password { cipher \| simple } string

By default, no password is configured for
static users.

**6\.**(Optional.) Set the offline detect period
for static users.

port-security static-user timer offline-detect time-value

By default, the offline detect period is
5 minutes for static users.

**7\.**(Optional.) Set the interval at which the
device actively sends ARP packets to trigger authentication for static users.

port-security static-user timer detect-period time-value

By default, the device actively sends ARP
packets to trigger authentication for static users at intervals of 3 minutes.

### Enabling static user IP update

#### About this task

After you use the port-security
static-user command to configure an IP address
range for a static user range, endpoints at IP addresses in the specified IP
address range will come online as static users. If the IP address of an
endpoint changes, the endpoint might send abnormal ARP packets to the access
device when it comes online. The source IP address of these ARP packets does
not belong to the specified IP address range. This issue triggers the device to
update the IP address of the endpoint when static user IP update is enabled.
After address update, the endpoint is no longer a static user. As a result, the
endpoint is logged off.

By default, the device does not update IP
addresses for static users when it receives ARP packets with source IP address
not belonging to the specified IP address range from these users. This setting
prevents the ARP packets from logging off online static users. To trace IP
address changes for endpoints, you can enable static user IP update to allow
the device to update the IP addresses of static users.

#### Restrictions and guidelines

Use static user IP update in conjunction
with DHCP snooping, ARP snooping, DHCPv6 snooping, or ND snooping. To receive
notifications about IP address changes from a snooping module, you must enable
the corresponding snooping feature.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enable static user IP update.

port-security static-user update-ip enable

By default, static user IP update is
disabled.

### Settingthe maximum number of concurrent static users allowed on a port

#### About this task

Set the maximum number of concurrent static
users on a port to prevent the system resources from being overused. When the
maximum number is reached, the port denies subsequent static users.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter interface view.

interface interface-type interface-number

**3\.**Set the maximum number of concurrent static
users allowed on the port.

port-security static-user max-user max-number

By default, a port supports a maximum of 4294967295
concurrent static users.

### Specifying an ACL to match the MAC addresses of static users

#### About this task

Typically, endpoints that match static user
IP ranges come online as static users. However, the device recognizes the
endpoints as MAC authentication users instead of static users in the following
situations:

·     The first packet sent by an endpoint is a Layer
2 packet that does not contain an IP address. In this situation, the packet
triggers MAC authentication first.

·     An endpoint has both IPv4 and IPv6 addresses and
the first packet sent by the endpoint is an IPv6 packet, but only static user
IPv4 ranges are configured on the device. In this situation, the packet
triggers MAC authentication first.

To resolve the issues, configure this
feature to use MAC address as the criterion to match static users. With this
feature, the device allows users that match the specified ACL to trigger
authentication and come online only as static users. The users cannot trigger
other authentication processes.

#### Restrictions and guidelines

The specified ACL must be a Layer 2 ACL.
The ACL can contain only permit rules with the source MAC range criteria.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Specify an ACL to match the MAC addresses of
static users.

port-security static-user match-mac acl acl-number

By default, no ACL is specified to match
the MAC addresses of static users.

## Specifying an IP address and mask for calculating the source IP of ARP detection packets

#### About this task

By default, the device uses 0.0.0.0 as the
source IP address of ARP detection packets. The network might have users that
cannot respond to ARP detection packets with source IP address 0.0.0.0. As a
result, the device inadequately determines that these users have gone offline.
To resolve the issue, use this feature to specify an IP address and mask for calculating
the source IP of ARP detection packets sent to a user in conjunction with the
user's IP address.

The device uses the following formula to calculate
the source IP address of ARP detection packets: source IP \= (user IP \& specified
mask) \| (specified IP \& \~specified mask). The \~mask parameter represents
the reverse of a mask. For example, the reverse mask of 255.255.255.0 is
0.0.0.255. If the IP address of a user is 192.168.8.1/24 and the IP address and
mask specified by using this feature is 1.1.1.11/255.255.255.0, the source IP
address of ARP detection packets is 192.168.8.11/24.

#### Restrictions and guidelines

This feature takes effect only on users
that come online after this feature is configured.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Specify an IP address and mask for calculating
the source IP of ARP detection packets.

port-security packet-detect arp-source-ip factor ip-address { mask \| mask-length }

By default, no IP address or mask is
specified for calculating the source IP of ARP detection packets. The source IP
of ARP detection packets is 0.0.0.0.

## Setting port security timers

#### About this task

Port security supports the following timers
for users that have not performed authentication or that have failed
authentication:

·     Periodic reauthentication timer—Sets the interval at which the device initiates reauthentication
for online 802.1X, Web authentication, or MAC
authentication users in the specified domain.

·     User aging timer—Sets the period that the device must wait before it can remove
online 802.1X, Web authentication, or MAC authentication users from the
specified domain.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Set a port security timer for specific
users.

port-security timer { reauth-period { auth-fail-domain \| preauth-domain } \| user-aging { auth-fail-domain \| critical-domain \| preauth-domain } } time-value

By default, the period for the periodic
reauthentication timer is 600 seconds and the period for the user aging timer
is 23 hours.

## Enabling SNMP notifications for port security

#### About this task

Use this feature to report critical port
security events to an NMS. For port security event notifications to be sent
correctly, you must also configure SNMP on the device. For more information
about SNMP configuration, see Network Management and
Monitoring Configuration Guide.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enable SNMP notifications for port security.

snmp-agent trap enable port-security \[ address-learned \| dot1x-failure \| dot1x-logoff \| dot1x-logon \| intrusion \| mac-auth-failure \| mac-auth-logoff \| mac-auth-logon ] \*

By default, SNMP notifications are disabled
for port security.

## Enabling port security user logging

#### About this task

This feature enables the device to generate
logs about port security users and send the logs to the information center. For
the logs to be output correctly, you must also configure the information center
on the device. For more information about information center configuration, see
Network Management and Monitoring Configuration Guide.

#### Restrictions and guidelines

To prevent excessive port security user log
entries, use this feature only if you need to analyze abnormal port security user
events.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enable port security user logging.

port-security access-user log enable \[
failed-authorization \| mac-learning \| violation \| vlan-mac-limit ] \*

By default, port security user logging is
disabled.

If you do not specify any parameters, this
command enables all types of port security user logs.

## Display and maintenance commands for port security

Execute display commands
in any view:

 

| Task | Command |
| --- | --- |
| Display the port security configuration, operation information, and statistics. | display port-security \[ interface interface-type interface-number ] || Display entries for online port security access users. | display port-security access-user \[ m-lag \[ local \| peer ] ] \[ access-type { dot1x \| mac-auth \| web-auth \| static } \| domain domain-name \| microsegment microsegment-id \| online-type { auth-fail-domain \| critical-domain \| preauth-domain \| success \| url-unavailable-domain } \| slot slot-number ] \* || Display information about blocked MAC addresses. | display port-security mac-address block \[ interface interface-type interface-number ] \[ vlan vlan-id ] \[ count ] || Display information about secure MAC addresses. | display port-security mac-address security \[ interface interface-type interface-number ] \[ vlan vlan-id ] \[ count ] || Display static user configuration information. | display port-security static-user || Display online static users. | display port-security static-user connection \[ \[ m-lag \[ local \| peer ] ] \[ interface interface-type interface-number \| online-type { auth-fail-domain \| critical-domain \| preauth-domain \| success } \| slot slot-number \| user-name user-name ] \| { ip \| ipv6 } ip-address \| mac mac-address ] || Display port security statistics. | display port-security statistics \[ slot slot-number ] || Log off online static users. | reset port-security static-user \[ interface interface-type interface-number \| { ip \| ipv6 } ip-address \| mac mac-address \| online-type { auth-fail-domain \| critical-domain \| preauth-domain \| success } \| user-name user-name ] || Clear port security statistics. | reset port-security statistics |









 

## Port security configuration examples

### Example: Configuring port security in autoLearn mode

#### Network configuration

As shown in [Figure 2](#_Ref324512159), configure
Ten-GigabitEthernet 1/0/1 on
the device to meet the following requirements:

·     Accept up to 64 users without authentication.

·     Be permitted to learn and add MAC addresses as
sticky MAC addresses, and set the secure MAC aging timer to 30 minutes.

·     Stop learning MAC addresses after the number of
secure MAC addresses reaches 64\. If any frame with an unknown MAC address
arrives, intrusion protection starts, and the port shuts down and stays silent
for 30 seconds.

Figure 2 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705400_x_Img_x_png_3_2216068_294551_0.png)

‌

#### Procedure

\# Enable port security.

\<Device\> system-view

\[Device] port-security enable

\# Set the secure MAC aging timer to 30
minutes.

\[Device] port-security timer autolearn
aging 30

\# Set port security's limit on the number
of secure MAC addresses to 64 on Ten-GigabitEthernet 1/0/1.

\[Device] interface ten-gigabitethernet 1/0/1

\[Device-Ten-GigabitEthernet1/0/1] port-security
max-mac-count 64

\# Set the port security mode to autoLearn.

\[Device-Ten-GigabitEthernet1/0/1] port-security
port-mode autolearn

\# Configure the port to be silent for 30
seconds after the intrusion protection feature is triggered.

\[Device-Ten-GigabitEthernet1/0/1] port-security
intrusion-mode disableport-temporarily

\[Device-Ten-GigabitEthernet1/0/1] quit

\[Device] port-security timer
disableport 30

#### Verifying the configuration

\# Verify the port security configuration.

\[Device] display port-security
interface ten-gigabitethernet 1/0/1

Global port security parameters:

   Port security                           :
Enabled

   M-LAG load sharing mode
(criterion)     : Distributed (local)

   M-LAG member's authentication
scope     : Local M-LAG interfaces

   M-LAG member configuration
conflict     : Unknown

   AutoLearn aging time                    :
30 min

   Disableport timeout                     : 30 sec

   Blockmac timeout                        :
180 sec

   MAC move                                :
Denied

   Authorization fail                      :
Online

   NAS-ID profile                          :
Not configured

   Dot1x-failure trap                      :
Disabled

   Dot1x-logon trap                        :
Disabled

   Dot1x-logoff trap                       :
Disabled

   Intrusion trap                          :
Disabled

   Address-learned trap                    :
Disabled

   Mac-auth-failure trap                   :
Disabled

   Mac-auth-logon trap                     :
Disabled

   Mac-auth-logoff trap                    :
Disabled

   Open authentication                     :
Disabled

   Traffic-statistics                      :
Disabled

   User aging period for preauth
domain    : 82800 sec

   User aging period for Auth-Fail
domain  : 82800 sec

   User aging period for critical
domain   : 82800 sec

   Reauth period for preauth
domain        : 600 sec

   Reauth period for Auth-Fail
domain      : 600 sec

   OUI value list                          :

    Index :  1           Value :
123401

 

 Ten-GigabitEthernet1/0/1 is link-up

   Port mode                               : autoLearn

   Pre-auth domain                         :
Not configured

   URL-unavailable
domain                  : Not configured

   NeedToKnow mode                         :
Disabled

   Intrusion protection mode               : DisablePortTemporarily

   Security MAC address attribute

       Learning mode                       :
Sticky

       Aging type                          :
Periodical

   Max secure MAC addresses                : 64

   Current secure MAC addresses            :
0

   Authorization                           :
Permitted

   NAS-ID profile                          :
Not configured

   Free VLANs                              :
Not configured

   Open authentication                     :
Disabled

   MAC-move VLAN check bypass              :
Disabled

The port allows for MAC address learning,
and you can view the number of learned MAC addresses in the Current secure MAC addresses field.

\# Display additional information about the
learned MAC addresses.

\[Device] interface ten-gigabitethernet 1/0/1

\[Device-Ten-GigabitEthernet1/0/1] display this

\#

interface Ten-GigabitEthernet1/0/1

 port-security max-mac-count 64

 port-security port-mode autolearn

 port-security mac-address security sticky
0002-0000-0015 vlan 1

 port-security mac-address security sticky
0002-0000-0014 vlan 1

 port-security mac-address security sticky
0002-0000-0013 vlan 1

 port-security mac-address security sticky
0002-0000-0012 vlan 1

 port-security mac-address security sticky
0002-0000-0011 vlan 1

\#

\[Device-Ten-GigabitEthernet1/0/1] quit

\# Verify that the port security mode changes
to secure after the number of MAC addresses learned
by the port reaches 64\.

\[Device] display port-security
interface ten-gigabitethernet 1/0/1

\# Verify that the port will be disabled for
30 seconds after it receives a frame with an unknown MAC address. (Details not
shown.)

\# After the port is re-enabled, delete
several secure MAC addresses.

\[Device] undo port-security
mac-address security sticky 0002-0000-0015 vlan 1

\[Device] undo port-security
mac-address security sticky 0002-0000-0014 vlan 1

...

\# Verify that the port security mode of the
port changes to autoLearn, and the port can learn
MAC addresses again. (Details not shown.)

### Example: Configuring port security in userLoginWithOUI mode

#### Network configuration

As shown in [Figure 3](#_Ref324513865), a client is connected to the device through Ten-GigabitEthernet 1/0/1. The device
authenticates the client with a RADIUS server in ISP domain sun. If the authentication succeeds, the client is
authorized to access the Internet.

·     The RADIUS server at 192.168.1.2 acts as the
primary authentication server and the secondary accounting server. The RADIUS
server at 192.168.1.3 acts as the secondary authentication server and the
primary accounting server. The shared key for authentication is name, and the shared key for accounting is money.

·     All users use the authentication, authorization,
and accounting methods of ISP domain sun.

·     The RADIUS server response timeout time is 5 seconds.
The maximum number of RADIUS packet retransmission attempts is 5\. The device
sends real-time accounting packets to the RADIUS server at 15-minute intervals,
and sends usernames without domain names to the RADIUS server.

Configure Ten-GigabitEthernet 1/0/1 to allow only one 802.1X user and a user that uses one
of the specified OUI values to be authenticated.

Figure 3 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705401_x_Img_x_png_4_2216068_294551_0.png)

‌

#### Procedure

The following configuration steps cover
some AAA/RADIUS configuration commands. For more information about the
commands, see Security Command Reference.

Make sure the host and the RADIUS server
can reach each other.

**1\.**Configure AAA:

\# Configure a RADIUS scheme named radsun.

\<Device\> system-view

\[Device] radius scheme radsun

\[Device-radius-radsun] primary
authentication 192.168.1.2

\[Device-radius-radsun] primary
accounting 192.168.1.3

\[Device-radius-radsun] secondary
authentication 192.168.1.3

\[Device-radius-radsun] secondary
accounting 192.168.1.2

\[Device-radius-radsun] key
authentication simple name

\[Device-radius-radsun] key accounting
simple money

\[Device-radius-radsun] timer response-timeout
5

\[Device-radius-radsun] retry 5

\[Device-radius-radsun] timer
realtime-accounting 15

\[Device-radius-radsun] user-name-format
without-domain

\[Device-radius-radsun] quit

\# Configure ISP domain sun.

\[Device] domain sun

\[Device-isp-sun]
authentication lan-access radius-scheme radsun

\[Device-isp-sun] authorization
lan-access radius-scheme radsun

\[Device-isp-sun] accounting lan-access
radius-scheme radsun

\[Device-isp-sun] quit

**2\.**Configure 802.1X:

\# Set the 802.1X authentication method to
CHAP. By default, the authentication method for 802.1X is CHAP.

\[Device] dot1x
authentication-method chap

\# Specify ISP domain sun as the
mandatory authentication domain for 802.1X users on Ten-GigabitEthernet 1/0/1.

\[Device] interface ten-gigabitethernet 1/0/1

\[Device-Ten-GigabitEthernet1/0/1] dot1x
mandatory-domain sun

\[Device-Ten-GigabitEthernet1/0/1] quit

**3\.**Configure port security:

\# Enable port security.

\[Device] port-security enable

\# Add five OUI values. (You can add up to
16 OUI values. The port permits only one user matching one of the OUIs to pass
authentication.)

\[Device] port-security oui
index 1 mac-address 1234-0100-1111

\[Device] port-security oui
index 2 mac-address 1234-0200-1111

\[Device] port-security oui
index 3 mac-address 1234-0300-1111

\[Device] port-security oui
index 4 mac-address 1234-0400-1111

\[Device] port-security oui
index 5 mac-address 1234-0500-1111

\# Set the port security mode to
userLoginWithOUI.

\[Device] interface ten-gigabitethernet 1/0/1

\[Device-Ten-GigabitEthernet1/0/1] port-security
port-mode userlogin-withoui

\[Device-Ten-GigabitEthernet1/0/1] quit

#### Verifying the configuration

\# Verify that Ten-GigabitEthernet 1/0/1 allows only one
802.1X user to be authenticated.

\[Device] display port-security
interface ten-gigabitethernet 1/0/1

Global port security parameters:

   Port security                           :
Enabled

   M-LAG load sharing mode
(criterion)     : Distributed (local)

   M-LAG member's authentication
scope     : Local M-LAG interfaces

   M-LAG member configuration
conflict     : Unknown

   AutoLearn aging time                    :
30 min

   Disableport timeout                     :
30 sec

   Blockmac timeout                        :
180 sec

   MAC move                                :
Denied

   Authorization fail                      :
Online

   NAS-ID profile                          :
Not configured

   Dot1x-failure trap                      :
Disabled

   Dot1x-logon trap                        :
Disabled

   Dot1x-logoff trap                       :
Disabled

   Intrusion trap                          :
Disabled

   Address-learned trap                    :
Disabled

   Mac-auth-failure trap                   :
Disabled

   Mac-auth-logon trap                     :
Disabled

   Mac-auth-logoff trap                    :
Disabled

   Open authentication                     :
Disabled

   Traffic-statistics                      :
Disabled

   User aging period for preauth
domain    : 82800 sec

   User aging period for Auth-Fail
domain  : 82800 sec

   User aging period for critical
domain   : 82800 sec

   Reauth period for preauth
domain        : 600 sec

   Reauth period for Auth-Fail
domain      : 600 sec

   OUI value list                          :

       Index :  1       Value :
123401

       Index :  2       Value :
123402

       Index :  3       Value :
123403

       Index :  4       Value :
123404

       Index :  5       Value :
123405

 

 Ten-GigabitEthernet1/0/1 is link-up

   Port mode                               :
userLoginWithOUI

   Pre-auth domain                         :
Not configured

   URL-unavailable
domain                  : Not configured

   NeedToKnow mode                         :
Disabled

   Intrusion protection mode               :
NoAction

   Security MAC address attribute 

       Learning mode                       :
Sticky

       Aging type                          :
Periodical

   Max secure MAC addresses                :
Not configured

   Current secure MAC addresses            : 1

   Authorization                           :Permitted

   NAS-ID profile                          :
Not configured

   Free VLANs                              :
Not configured

   Open authentication                     :
Disabled

   MAC-move VLAN check bypass              :
Disabled

\# Display information about the online
802.1X user to verify 802.1X configuration.

\[Device] display dot1x

\# Verify that the port also allows one user
whose MAC address has an OUI among the specified OUIs to pass authentication.

\[Device] display mac-address
interface ten-gigabitethernet 1/0/1

MAC Address     VLAN ID   State         
Port/Nickname            Aging

1234-0300-0011  1        
Learned        XGE1/0/1                 Y

### Example: Configuring port security in macAddressElseUserLoginSecure mode

#### Network configuration

As shown in [Figure 4](#_Ref324752322), a client is connected to the device through Ten-GigabitEthernet 1/0/1. The device
authenticates the client by a RADIUS server in ISP domain sun.
If the authentication succeeds, the client is authorized to access the
Internet.

Configure Ten-GigabitEthernet 1/0/1 of the device to meet the following requirements:

·     Allow more than one MAC authenticated user to
log on.

·     For 802.1X users, perform MAC authentication
first and then, if MAC authentication fails, 802.1X authentication. Allow only
one 802.1X user to log on.

·     Use the MAC address of each user as both the username and password for MAC authentication.
The MAC addresses are in hexadecimal notation with hyphens, and letters are in upper
case.

·     Set the total number of MAC authenticated users
and 802.1X authenticated users to 64\. 

·     Enable NTK (ntkonly
mode) to prevent frames from being sent to unknown MAC addresses.

Figure 4 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705402_x_Img_x_png_5_2216068_294551_0.png)

‌

#### Procedure

Make sure the host and the RADIUS server
can reach each other.

**1\.**Configure RADIUS authentication/accounting
and ISP domain settings. (See "[Example: Configuring port security in userLoginWithOUI
mode](#_Ref145255311).")

**2\.**Configure port security:

\# Enable port security.

\<Device\> system-view

\[Device] port-security enable

\# Use the MAC address of each user as
both the username and password for MAC authentication. The MAC addresses are in
hexadecimal notation with hyphens, and letters are in upper case.

\[Device] mac-authentication
user-name-format mac-address with-hyphen uppercase

\# Specify the MAC authentication domain.

\[Device] mac-authentication domain
sun

\# Set the 802.1X authentication method to
CHAP. By default, the authentication method for 802.1X is CHAP.

\[Device] dot1x
authentication-method chap

\# Set port security's limit on the number
of MAC addresses to 64 on the port.

\[Device] interface ten-gigabitethernet 1/0/1

\[Device-Ten-GigabitEthernet1/0/1] port-security
max-mac-count 64

\# Set the port security mode to macAddressElseUserLoginSecure.

\[Device-Ten-GigabitEthernet1/0/1] port-security
port-mode mac-else-userlogin-secure

\# Specify ISP domain sun as the
mandatory authentication domain for 802.1X users.

\[Device-Ten-GigabitEthernet1/0/1] dot1x
mandatory-domain sun

\# Set the NTK mode of the port to
ntkonly.

\[Device-Ten-GigabitEthernet1/0/1] port-security
ntk-mode ntkonly

\[Device-Ten-GigabitEthernet1/0/1] quit

#### Verifying the configuration

\# Verify the port security configuration.

\[Device] display port-security
interface ten-gigabitethernet 1/0/1

Global port security parameters:

   Port security                           :
Enabled

   M-LAG load sharing mode
(criterion)     : Distributed (local)

   M-LAG member's authentication
scope     : Local M-LAG interfaces

   M-LAG member configuration
conflict     : Unknown

   AutoLearn aging time                    :
30 min

   Disableport timeout                     :
30 sec

   Blockmac timeout                        :
180 sec

   MAC move                                :
Denied

   Authorization fail                      :
Online

   NAS-ID profile                          :
Not configured

   Dot1x-failure trap                      :
Disabled

   Dot1x-logon trap                        :
Disabled

   Dot1x-logoff trap                       :
Disabled

   Intrusion trap                          :
Disabled

   Address-learned trap                    :
Disabled

   Mac-auth-failure trap                   :
Disabled

   Mac-auth-logon trap                     :
Disabled

   Mac-auth-logoff trap                    :
Disabled

   Open authentication                     :
Disabled

   Traffic-statistics                      :
Disabled

   User aging period for preauth
domain    : 82800 sec

   User aging period for Auth-Fail
domain  : 82800 sec

   User aging period for critical
domain   : 82800 sec

   Reauth period for preauth
domain        : 600 sec

   Reauth period for Auth-Fail
domain      : 600 sec

   OUI value list

 

 Ten-GigabitEthernet1/0/1 is link-up

   Port mode                               :
macAddressElseUserLoginSecure

   Pre-auth domain                         :
Not configured

   URL-unavailable
domain                  : Not configured

   NeedToKnow mode                         :
NeedToKnowOnly

   Intrusion protection mode               :
NoAction

   Security MAC address attribute

      Learning mode                        :
Sticky 

      Aging type                           :
Periodical

   Max secure MAC addresses                :
64

   Current secure MAC addresses            :
0

   Authorization                           :
Permitted

   NAS-ID profile                          :
Not configured

   Free VLANs                              :
Not configured

   Open authentication                     :
Disabled

   MAC-move VLAN check bypass              :
Disabled

\# After users pass authentication, display
MAC authentication information. Verify that Ten-GigabitEthernet 1/0/1 allows multiple
MAC authentication users to be authenticated.

\[Device] display mac-authentication
interface ten-gigabitethernet 1/0/1

 Global MAC authentication parameters:

   MAC authentication                  :
Enabled

   Authentication method               :
PAP

   M-LAG member configuration
conflict : Unknown

   User name format                    :
MAC address in uppercase(XX-XX-XX-XX-XX-XX)

           Username                    :
mac

           Password                    :
Not configured

   MAC range accounts                  :
0

          MAC address          Mask             
   Username

   Offline detect period                      :
300 s

   Quiet period                               :
180 s

   Server timeout                             :
100 s

   Reauth period                              :
3600 s

   User aging period for critical
VLAN        : 1000 s

   User aging period for critical
VSI         : 1000 s

   User aging period for guest
VLAN           : 1000 s

   User aging period for guest
VSI            : 1000 s

   User aging period for critical
microsegment: 1000 s

   Temporary user aging period                :
60 s

   Authentication domain                      :
sun

   HTTP proxy port
list                       : Not configured

   HTTPS proxy port
list                      : Not configured

 Online MAC-auth wired users                  : 3

 

 Silent MAC users:

          MAC address       VLAN ID 
From port               Port index

 

Ten-GigabitEthernet1/0/1 is link-up

   MAC authentication                         :
Enabled

   Carry User-IP                              :
Disabled

   Authentication domain                      :
Not configured

   Auth-delay timer                           :
Disabled

   Periodic reauth                            :
Disabled

   Re-auth server-unreachable                 :
Logoff

   Guest VLAN                                 :
Not configured

   Guest VLAN auth-period                     :
30 s

   Critical VLAN                              :
Not configured

   Critical voice VLAN                        :
Disabled

   Host mode                                  :
Single VLAN

   Offline detection                          :
Enabled

   Authentication order                       :
Default

   User aging                                 :
Enabled

   Server-recovery online-user-sync           :
Enabled

 

   Guest VSI                                  :
Not configured

   Guest VSI reauthentication                 :
Enabled

     Guest VSI auth-period                    :
30 s

   Critical VSI                               :
Not configured

   Critical microsegment
ID                   : Not configured

   Critical profile                           :
Not configured

   URL user
logoff                            : No

   Max online users                           :
4294967295

   Max online preauth-domain
users            : 4294967295

   Max online Auth-Fail-domain
users          : 4294967295

   Auth-server-unavailable
escape             : Disabled

   Auto-tag feature                           :
Disabled

   VLAN tag configuration ignoring            :
Disabled

   Authentication attempts                    :
successful 3, failed 7

   Current online users                       : 3

          MAC address       Auth state

          1234-0300-0011    Authenticated

          1234-0300-0012    Authenticated

          1234-0300-0013    Authenticated

\# Display 802.1X authentication information.
Verify that Ten-GigabitEthernet 1/0/1 allows only one
802.1X user to be authenticated.

\[Device] display dot1x interface ten-gigabitethernet 1/0/1

 Global 802.1X parameters:

   802.1X authentication                      :
Enabled

   M-LAG member configuration
conflict        : Unknown

   CHAP authentication                        :
Enabled

   Max-tx period                              :
30 s

   Handshake period                           :
15 s

   Quiet timer                                :
Disabled

       Quiet period                           :
60 s

   Supp timeout                               :
30 s

   Server timeout                             :
100 s

   Reauth period                              :
3600 s

   Unicast-trigger quiet
period               : 0 s

   Max auth requests                          :
2

   User aging period for Auth-Fail
VLAN       : 1000 s

   User aging period for Auth-Fail
VSI        : 1000 s

   User aging period for critical
VLAN        : 1000 s

   User aging period for critical
VSI         : 1000 s

   User aging period for guest
VLAN           : 1000 s

   User aging period for guest
VSI            : 1000 s

   EAD assistant function                     :
Disabled

       EAD timeout                            :
30 min

   Domain delimiter                           :
@

 Online 802.1X wired users                    : 1

 

 Ten-GigabitEthernet1/0/1  is link-up

   802.1X authentication                :
Enabled

   Handshake                            :
Enabled

   Handshake reply                      :
Disabled

   Handshake security                   :
Disabled

   Unicast trigger                      :
Disabled

   Periodic reauth                      :
Disabled

   Port role                            :
Authenticator

   Authorization mode                   :
Auto

   Port access control                  :
MAC-based

   Multicast trigger                    :
Enabled

   Mandatory auth domain                :
sun

   Guest VLAN                           :
Not configured

   Auth-Fail VLAN                       :
Not configured

   Critical VLAN                        :
Not configured

   Critical voice VLAN                  :
Disabled

   Add Guest VLAN delay                 :
Disabled

   Re-auth server-unreachable           :
Logoff

   Max online users                     :
4294967295

   Max online preauth-domain
users      : 4294967295

   Max online Auth-Fail-domain users 
  : 4294967295

   User IP freezing                     :
Disabled

   Reauth period                        :
60 s

   Send Packets Without Tag             :
Disabled

   Max Attempts Fail Number             :
0

   Guest VSI                            :
Not configured

   Auth-Fail VSI                        :
Not configured

   Critical VSI                         :
Not configured

   Add Guest VSI delay                  :
Disabled

   Critical microsegment
ID             : Not configured

   Critical profile                     :
Not configured

   User aging                           :
Enabled

   Server-recovery online-user-sync     :
Enabled

   Auth-Fail EAPOL                      :
Disabled

   Critical EAPOL                       :
Disabled

   Discard duplicate EAPOL-Start        :
No

   Auth-server-unavailable
escape       : Disabled

 

   EAPOL packets: Tx 16331, Rx 102

   Sent EAP Request/Identity packets
: 16316

        EAP Request/Challenge
packets: 6

        EAP Success packets: 4

        EAP Failure packets: 5

   Received EAPOL Start packets : 6

            EAPOL LogOff packets: 2

            EAP Response/Identity
packets : 80

            EAP Response/Challenge
packets: 6

            Error packets: 0

   Online 802.1X users: 1

          MAC address         Auth
state

          0002-0000-0011      Authenticated

\# Verify that frames with an unknown
destination MAC address, multicast address, or broadcast address are discarded.
(Details not shown.)

### Example: Configuring port security for M-LAG

#### Network configuration

As shown in [Figure 5](#_Ref61884211), Device
A and Device B authenticate the host users to the Internet. Configure Device A
and Device B as follows:

·     Use RADIUS servers to
perform authentication, authorization, and accounting for the users.

·     Form an M-LAG system for
node redundancy.

·     Set the port security mode to macAddressWithRadius on their M-LAG interfaces. Set the authentication load sharing mode to
distributed local mode for users attached to the M-LAG interfaces.

·     To make sure the source IP address of the RADIUS
packets sent for users attached to one member device does not change after a
node failover, configure the following settings on each member device: 

¡     Configure
one source IP address for local users and one for users attached to the peer
member device. On each member device, use the active and standby M-LAG virtual
IP addresses as the local and
peer source IP addresses of the outgoing RADIUS packets, respectively.

Figure 5 Network diagram

 ![](https://resource.h3c.com/en/202407/12/20240712_11705403_x_Img_x_png_6_2216068_294551_0.png)

‌

| Device | Interface | M-LAG interface | IP address |
| --- | --- | --- | --- |
| Device A (M-LAG1) | XGE1/0/1 | Keepalive interface | 1.1.1.2/24 || BAGG1 (XGE1/0/2) | Peer-link interface | \- || BAGG2 (XGE1/0/4) | M-LAG interface | \- || BAGG3 (XGE1/0/3) | M-LAG interface | \- || VLAN-interface 2 | \- | 10.1.1.1/24 || Device B (M-LAG2) | XGE1/0/1 | Keepalive interface | 1.1.1.1/24 || BAGG1 (XGE1/0/2) | Peer-link interface | \- || BAGG2 (XGE1/0/4) | M-LAG interface | \- || BAGG3 (XGE1/0/3) | M-LAG interface | \- || VLAN-interface 2 | \- | \- |










‌

#### Restrictions and guidelines

The configuration procedure in this example
uses some AAA and RADIUS commands. For more information about these commands,
see Security Command Reference.

The configuration procedure in this example
uses some M-LAG commands. For more information about these commands, see Layer 2—LAN Switching Command Reference.

#### Prerequisites

Make sure the host and RADIUS servers can
reach each other.

Configure the RADIUS servers and add user
accounts. Make sure the RADIUS servers can provide authentication,
authorization, and accounting services.

#### Procedure

**1\.**Configure Device A:

¡     Configure
AAA:

\# Configure RADIUS scheme radsun.

\<DeviceA\> system-view

\[DeviceA] radius scheme radsun

\[DeviceA-radius-radsun] primary
authentication 10.1.1.3

\[DeviceA-radius-radsun] primary
accounting 10.1.1.4

\[DeviceA-radius-radsun] secondary
authentication 10.1.1.4

\[DeviceA-radius-radsun] secondary
accounting 10.1.1.3

\[DeviceA-radius-radsun] key
authentication simple name

\[DeviceA-radius-radsun] key
accounting simple money

\[DeviceA-radius-radsun] timer response-timeout
5

\[DeviceA-radius-radsun] retry
5

\[DeviceA-radius-radsun] timer realtime-accounting
15

\[DeviceA-radius-radsun] user-name-format
without-domain

\# Specify 10.1.1.1 as the source IP
address of outgoing RADIUS packets for users attached to the local M-LAG
interfaces. Specify 10.1.1.2 as the source IP address of outgoing RADIUS
packets for users attached to the peer M-LAG interfaces.

\[DeviceA-radius-radsun] nas-ip
m-lag local 10.1.1.1

\[DeviceA-radius-radsun] nas-ip
m-lag peer 10.1.1.2

\[DeviceA-radius-radsun] quit

\# Configure ISP domain sun.

\[DeviceA] domain sun

\[DeviceA-isp-sun]
authentication lan-access radius-scheme radsun

\[DeviceA-isp-sun]
authorization lan-access radius-scheme radsun

\[DeviceA-isp-sun] accounting lan-access
radius-scheme radsun

\[DeviceA-isp-sun] quit

¡     Configure
M-LAG settings:

\# Configure M-LAG system settings.

\[DeviceA] m-lag system-mac
1-1-1

\[DeviceA] m-lag system-number
1

\[DeviceA] m-lag system-priority
123

\# Configure the destination and source IP
addresses of keepalive packets.

\[DeviceA] m-lag keepalive ip destination
1.1.1.1 source 1.1.1.2

\# Configure port Ten-GigabitEthernet 1/0/1 to operate in
Layer 3 mode and configure the source IP address of keepalive packets as the IP
address of this port.

\[DeviceA] interface ten-gigabitethernet 1/0/1

\[DeviceA-Ten-GigabitEthernet1/0/1] port link-mode
route

\[DeviceA-Ten-GigabitEthernet1/0/1] ip address 1.1.1.2 24

\[DeviceA-Ten-GigabitEthernet1/0/1] quit

\# Exclude Ten-GigabitEthernet 1/0/1 (keepalive
interface) from being shut down upon detection of multi-active collisions.

\[DeviceA] m-lag mad exclude interface
ten-gigabitethernet 1/0/1

\# Create Layer 2 aggregate interface Bridge-Aggregation 1 and set the aggregation mode to dynamic.

\[DeviceA] interface
bridge-aggregation 1

\[DeviceA-Bridge-Aggregation1] link-aggregation
mode dynamic

\[DeviceA-Bridge-Aggregation1]
quit

\# Assign Ten-GigabitEthernet 1/0/2 to aggregation
group 1\.

\[DeviceA] interface ten-gigabitethernet 1/0/2

\[DeviceA-Ten-GigabitEthernet1/0/2] port
link-aggregation group 1

\[DeviceA-Ten-GigabitEthernet1/0/2] quit

\# Configure Layer 2 aggregate interface Bridge-Aggregation 1 as a peer-link interface.

\[DeviceA] interface
bridge-aggregation 1

\[DeviceA-Bridge-Aggregation1]
port m-lag intra-portal-port 1

\[DeviceA-Bridge-Aggregation1]
quit

\# On the VLAN interface, specify 10.1.1.1
as the M-LAG virtual IPv4 address in active state and specify 10.1.1.2 as the M-LAG
virtual IPv4 address in standby state.

\[DeviceA] vlan 2

\[DeviceA-vlan2] quit

\[DeviceA] interface vlan-interface
2

\[DeviceA-Vlan-interface2] port
m-lag virtual-ip 10.1.1.1 24 active virtual-mac 11-1-1

\[DeviceA-Vlan-interface2] port
m-lag virtual-ip 10.1.1.2 24 standby virtual-mac 11-1-2

\[DeviceA-Vlan-interface2] quit

\# Create Layer 2 aggregate interface Bridge-Aggregation 2 and set the aggregation mode to dynamic.

\[DeviceA] interface
bridge-aggregation 2

\[DeviceA-Bridge-Aggregation2] link-aggregation
mode dynamic

\[DeviceA-Bridge-Aggregation2]
quit

\# Assign Ten-GigabitEthernet 1/0/4 to aggregation
group 2\.

\[DeviceA] interface ten-gigabitethernet 1/0/4

\[DeviceA-Ten-GigabitEthernet1/0/4] port
link-aggregation group 2

\[DeviceA-Ten-GigabitEthernet1/0/4] quit

\# Assign Bridge-Aggregation 2 to M-LAG
group 2\.

\[DeviceA] interface
bridge-aggregation 2

\[DeviceA-Bridge-Aggregation2]
port m-lag group 2

\[DeviceA-Bridge-Aggregation2] port
access vlan 2

\[DeviceA-Bridge-Aggregation2]
quit

\# Create Layer 2 aggregate interface Bridge-Aggregation 3 and set the aggregation mode to dynamic.

\[DeviceA] interface
bridge-aggregation 3

\[DeviceA-Bridge-Aggregation3] link-aggregation
mode dynamic

\[DeviceA-Bridge-Aggregation3]
quit

\# Assign Ten-GigabitEthernet 1/0/3 to aggregation
group 3\.

\[DeviceA] interface ten-gigabitethernet 1/0/3

\[DeviceA-Ten-GigabitEthernet1/0/3] port
link-aggregation group 3

\[DeviceA-Ten-GigabitEthernet1/0/3] quit

\# Assign Bridge-Aggregation 3 to M-LAG
group 3\.

\[DeviceA] interface
bridge-aggregation 3

\[DeviceA-Bridge-Aggregation3]
port m-lag group 3

\[DeviceA-Bridge-Aggregation3]
quit

¡     Configure
port security settings:

\# Enable port security.

\[DeviceA] port-security enable

\# Use the MAC address of each user as
both the username and password for MAC authentication. The MAC addresses are in
hyphenated hexadecimal notation, with letters in upper case.

\[DeviceA] mac-authentication
user-name-format mac-address with-hyphen uppercase

\# Configure domain sun as the global
MAC authentication domain on the device.

\[DeviceA] mac-authentication domain
sun

\# Set the port security mode to macAddressWithRadius on Bridge-Aggregation 3\.

\[DeviceA] interface
bridge-aggregation 3

\[DeviceA-Bridge-Aggregation3] port-security
port-mode mac-authentication

\# Configure domain sun as the MAC
authentication domain on Bridge-Aggregation 3\.

\[DeviceA-Bridge-Aggregation3] mac-authentication
domain sun

\[DeviceA-Bridge-Aggregation3]
quit

**2\.**Configure Device B:

¡     Configure
AAA settings:

\# Configure RADIUS scheme radsun.

\<DeviceB\> system-view

\[DeviceB] radius scheme radsun

\[DeviceB-radius-radsun] primary
authentication 10.1.1.3

\[DeviceB-radius-radsun] primary
accounting 10.1.1.4

\[DeviceB-radius-radsun] secondary
authentication 10.1.1.4

\[DeviceB-radius-radsun] secondary
accounting 10.1.1.3

\[DeviceB-radius-radsun] key
authentication simple name

\[DeviceB-radius-radsun] key
accounting simple money

\[DeviceB-radius-radsun] timer response-timeout
5

\[DeviceB-radius-radsun] retry
5

\[DeviceB-radius-radsun] timer
realtime-accounting 15

\[DeviceB-radius-radsun] user-name-format
without-domain

\# Specify 10.1.1.2 as the source IP
address of outgoing RADIUS packets for users attached to the local M-LAG
interfaces. Specify 10.1.1.1 as the source IP address of outgoing RADIUS
packets for users attached to the peer M-LAG interfaces.

\[DeviceB-radius-radsun] nas-ip
m-lag local 10.1.1.2

\[DeviceB-radius-radsun] nas-ip
m-lag peer 10.1.1.1

\[DeviceB-radius-radsun] quit

\# Configure ISP domain sun.

\[DeviceB] domain sun

\[DeviceB-isp-sun]
authentication lan-access radius-scheme radsun

\[DeviceB-isp-sun]
authorization lan-access radius-scheme radsun

\[DeviceB-isp-sun] accounting lan-access
radius-scheme radsun

\[DeviceB-isp-sun] quit

¡     Configure
M-LAG settings:

\# Configure M-LAG system settings.

\[DeviceB] m-lag system-mac
1-1-1

\[DeviceB] m-lag system-number 2

\[DeviceB] m-lag system-priority
123

\# Configure the destination and source IP
addresses of keepalive packets.

\[DeviceB] m-lag keepalive ip destination
1.1.1.2 source 1.1.1.1

\# Configure Ten-GigabitEthernet 1/0/1 to operate in
Layer 3 mode and configure the source IP address of keepalive packets as the IP
address of this port. 

\[DeviceB] interface ten-gigabitethernet 1/0/1

\[DeviceB-Ten-GigabitEthernet1/0/1] port link-mode
route

\[DeviceB-Ten-GigabitEthernet1/0/1] ip address 1.1.1.1 24

\[DeviceB-Ten-GigabitEthernet1/0/1] quit

\# Exclude Ten-GigabitEthernet 1/0/1 (keepalive
interface) from being shut down upon detection of multi-active collisions.

\[DeviceB] m-lag mad exclude interface
ten-gigabitethernet 1/0/1

\# Create Layer 2 aggregate interface Bridge-Aggregation 1 and set the aggregation mode to dynamic.

\[DeviceB] interface
bridge-aggregation 1

\[DeviceB-Bridge-Aggregation1]
link-aggregation mode dynamic

\[DeviceB-Bridge-Aggregation1]
quit

\# Assign Ten-GigabitEthernet 1/0/2 to aggregation
group 1\.

\[DeviceB] interface ten-gigabitethernet 1/0/2

\[DeviceB-Ten-GigabitEthernet1/0/2] port
link-aggregation group 1

\[DeviceB-Ten-GigabitEthernet1/0/2] quit

\# Configure Layer 2 aggregate interface Bridge-Aggregation 1 as a peer-link interface.

\[DeviceB] interface
bridge-aggregation 1

\[DeviceB-Bridge-Aggregation1]
port m-lag intra-portal-port 1

\[DeviceB-Bridge-Aggregation1]
quit

\# Create VLAN-interface 2\. On the VLAN
interface, specify 10.1.1.2 as the M-LAG virtual IPv4 address in active state
and specify 10.1.1.1 as the M-LAG virtual IPv4 address in standby state.

\[DeviceB] vlan 2

\[DeviceB-vlan2] quit

\[DeviceB] interface vlan-interface
2

\[DeviceB-Vlan-interface2] port
m-lag virtual-ip 10.1.1.2 24 active virtual-mac 11-1-2

\[DeviceB-Vlan-interface2] port
m-lag virtual-ip 10.1.1.1 24 standby virtual-mac 11-1-1

\[DeviceB-Vlan-interface2] quit

\# Create Layer 2 aggregate interface Bridge-Aggregation 2 and set the aggregation mode to dynamic.

\[DeviceB] interface
bridge-aggregation 2

\[DeviceB-Bridge-Aggregation2]
link-aggregation mode dynamic

\[DeviceB-Bridge-Aggregation2]
quit

\# Assign Ten-GigabitEthernet 1/0/4 to aggregation
group 2\.

\[DeviceB] interface ten-gigabitethernet 1/0/4

\[DeviceB-Ten-GigabitEthernet1/0/4] port
link-aggregation group 2

\[DeviceB-Ten-GigabitEthernet1/0/4] quit

\# Assign Bridge-Aggregation 2 to M-LAG
group 2\.

\[DeviceB] interface
bridge-aggregation 2

\[DeviceB-Bridge-Aggregation2]
port m-lag group 2

\[DeviceB-Bridge-Aggregation2]
port access vlan 2

\[DeviceB-Bridge-Aggregation2]
quit

\# Create Layer 2 aggregate interface Bridge-Aggregation 3 and set the aggregation mode to dynamic.

\[DeviceB] interface
bridge-aggregation 3

\[DeviceB-Bridge-Aggregation3]
link-aggregation mode dynamic

\[DeviceB-Bridge-Aggregation3]
quit

\# Assign Ten-GigabitEthernet 1/0/3 to aggregation
group 3\.

\[DeviceB] interface ten-gigabitethernet 1/0/3

\[DeviceB-Ten-GigabitEthernet1/0/3] port
link-aggregation group 3

\[DeviceB-Ten-GigabitEthernet1/0/3] quit

\# Assign Bridge-Aggregation 3 to M-LAG
group 3\.

\[DeviceB] interface
bridge-aggregation 3

\[DeviceB-Bridge-Aggregation3]
port m-lag group 3

\[DeviceB-Bridge-Aggregation3]
quit

¡     Configure
port security settings:

\# Enable port security.

\[DeviceB] port-security enable

\# Use the MAC address of each user as
both the username and password for MAC authentication. The MAC addresses are in
hyphenated hexadecimal notation, with letters in upper case.

\[DeviceB] mac-authentication user-name-format
mac-address with-hyphen uppercase

\# Configure domain sun as the global
MAC authentication domain on the device.

\[DeviceB] mac-authentication domain
sun

\# Set the port security mode to macAddressWithRadius on Layer 2 aggregate interface Bridge-Aggregation 3.

\[DeviceB] interface
bridge-aggregation 3

\[DeviceB-Bridge-Aggregation3] port-security
port-mode mac-authentication

\# Configure domain sun as the MAC
authentication domain on Bridge-Aggregation 3\.

\[DeviceB-Bridge-Aggregation3] mac-authentication
domain sun

\[DeviceB-Bridge-Aggregation3]
quit

**3\.**Configure Device C:

\# Create Layer 2 aggregate interface Bridge-Aggregation 1 and set the aggregation mode to dynamic.

\<DeviceC\> system-view

\[DeviceC] interface
bridge-aggregation 1

\[DeviceC-Bridge-Aggregation1] link-aggregation
mode dynamic

\[DeviceC-Bridge-Aggregation1]
quit

\# Assign Ten-GigabitEthernet 1/0/1 and Ten-GigabitEthernet 1/0/2 to aggregation
group 1\.

\[DeviceC] interface range ten-gigabitethernet 1/0/1 to ten-gigabitethernet 1/0/2

\[DeviceC-if-range] port
link-aggregation group 1

\[DeviceC-if-range] quit

**4\.**Configure Device D:

\# Create Layer 2 aggregate interface Bridge-Aggregation 1 and set the aggregation mode to dynamic.

\<DeviceD\> system-view

\[DeviceD] interface
bridge-aggregation 1

\[DeviceD-Bridge-Aggregation1]
link-aggregation mode dynamic

\[DeviceD-Bridge-Aggregation1]
quit

\# Assign Ten-GigabitEthernet 1/0/1 and Ten-GigabitEthernet 1/0/2 to aggregation
group 1\.

\[DeviceD] interface range ten-gigabitethernet 1/0/1 to ten-gigabitethernet 1/0/2

\[DeviceD-if-range] port
link-aggregation group 1

\[DeviceD-if-range] quit

#### Verifying the configuration

**1\.**On Device A:

\# Display M-LAG summary information on Device
A to verify that Device A and Device B have formed an M-LAG system.

\[DeviceA] display m-lag summary

Flags: A \-- Aggregate
interface down, B \-- No peer M-LAG interface configured

       C \-- Configuration
consistency check failed

 

Peer-link interface: BAGG1

Peer-link interface state
(cause): UP

Keepalive link state (cause):
UP

 

                     M-LAG
interface information

M-LAG IF      M-LAG group 
Local state (cause)  Peer state  Remaining down time(s)

BAGG2         2            UP                  
UP          \-

BAGG3         3            UP                  
UP          \-

\# Display port security settings on Device
A.

\[DeviceA] display
port-security interface bridge-aggregation 3

Global port security
parameters:

   Port
security                           : Enabled

   M-LAG load sharing mode (criterion)     : Distributed (local)

   M-LAG member's authentication scope     : Local M-LAG interfaces

   M-LAG member configuration
conflict     : Not conflicted

   AutoLearn aging
time                    : 0 min

   Disableport
timeout                     : 20 sec

   Blockmac
timeout                        : 180 sec

   MAC
move                                : Denied

   Authorization
fail                      : Online

   NAS-ID
profile                          : Not configured

   Dot1x-failure
trap                      : Disabled

   Dot1x-logon
trap                        : Disabled

   Dot1x-logoff
trap                       : Disabled

   Intrusion
trap                          : Disabled

   Address-learned
trap                    : Disabled

   Mac-auth-failure
trap                   : Disabled

   Mac-auth-logon
trap                     : Disabled

   Mac-auth-logoff
trap                    : Disabled

   Open
authentication                     : Disabled

  
Traffic-statistics                      : Enabled

   User aging period for
preauth domain    : 82800 sec

   User aging period for Auth-Fail
domain  : 82800 sec

   User aging period for
critical domain   : 82800 sec

   Reauth period for preauth
domain        : 600 sec

   Reauth period for Auth-Fail
domain      : 600 sec

   OUI value
list                          :

 Bridge-Aggregation3 is
link-up

   Port
mode                               : macAuthentication

   Pre-auth
domain                         : Not configured

   URL-unavailable
domain                  : Not configured

   NeedToKnow
mode                         : Disabled

   Intrusion protection
mode               : NoAction

   Max secure MAC
addresses                : Not configured

  
Authorization                           : Permitted

   NAS-ID
profile                          : Not configured

   Free
VLANs                              : Not configured

   Open
authentication                     : Disabled

   MAC-move VLAN check
bypass              : Disabled

\# After users pass authentication, display
MAC authentication information on Device A.

\[DeviceA] display
mac-authentication interface bridge-aggregation 3

 Global MAC authentication
parameters:

   MAC
authentication                  : Enabled

   Authentication
method               : CHAP

   M-LAG member configuration conflict : Not conflicted

   Username
format                     : MAC address in lowercase(xxxxxxxxxxxx)

          
Username                    : mac

          
Password                    : Not configured

   MAC range
accounts                  : 0

          MAC address         
Mask                 Username

 

   Offline detect
period                      : 60 s

   Quiet
period                               : 60 s

   Server
timeout                             : 100 s

   Reauth
period                              : 3600 s

   User aging period for
critical VLAN        : 1000 s

   User aging period for
critical VSI         : 1000 s

   User aging period for guest
VLAN           : 1000 s

   User aging period for guest
VSI            : 1000 s

   User aging period for
critical microsegment: 1000 s

   Temporary user aging
period                : 60 s

   Authentication
domain                      : test

   HTTP proxy port
list                       : Not configured

   HTTPS proxy port
list                      : Not configured

   Max number of silent MACs                 
: 1024 (per slot)

 Online MAC-auth wired
users                  : 10

 

 Silent MAC users:

          MAC address      
VLAN ID  From port               Port index

 

 Bridge-Aggregation3 is
link-up

   MAC
authentication                         : Enabled

   Carry
User-IP                              : Disabled

   Authentication
domain                      : test

   Auth-delay
timer                           : Disabled

   Periodic
reauth                            : Disabled

   Re-auth
server-unreachable                 : Logoff

   Guest
VLAN                                 : Not configured

   Guest VLAN
reauthentication                : Enabled

     Guest VLAN
auth-period                   : 30 s

   Critical
VLAN                              : Not configured

   Critical voice
VLAN                        : Disabled

   Host
mode                                  : Single VLAN

   Offline
detection                          : Enabled

   Authentication order                       :
Default

   User
aging                                 : Enabled

   Server-recovery
online-user-sync           : Disabled

 

   Guest
VSI                                  : Not configured

   Guest VSI
reauthentication                 : Enabled

     Guest VSI
auth-period                    : 30 s

   Critical
VSI                               : Not configured

   Critical microsegment
ID                   : Not configured

   Critical
profile                           : Not configured

   URL user
logoff                            : No

   Auto-tag
feature                           : Disabled

   VLAN tag configuration
ignoring            : Disabled

   Max online
users                           : 4294967295

   Max online preauth-domain
users            : 4294967295

   Max online Auth-Fail-domain
users          : 4294967295

   Auth-server-unavailable
escape             : Disabled

   Authentication
attempts                    : successful 6, failed 3174

   Current online users                       : 10

          MAC
address       Auth state

         
0000-0000-0001    Authenticated

         
0000-0000-0002    Authenticated

         
0000-0000-0003    Authenticated

         
0000-0000-0004    Authenticated

          0000-0000-0005    Authenticated

         
0000-0000-0006    Authenticated

         
0000-0000-0007    Authenticated

         
0000-0000-0008    Authenticated

         
0000-0000-0009    Authenticated

         
0000-0000-000a    Authenticated

**2\.**On Device B:

\# Display M-LAG summary information on
Device B to verify that Device A and Device B have formed an M-LAG system.

\[DeviceB] display m-lag summary

Flags: A \-- Aggregate
interface down, B \-- No peer M-LAG interface configured

       C \-- Configuration
consistency check failed

 

Peer-link interface: BAGG1

Peer-link interface state
(cause): UP

Keepalive link state (cause):
UP

                     M-LAG
interface information

M-LAG IF      M-LAG group 
Local state (cause)  Peer state  Remaining down time(s)

BAGG2         2            UP                  
UP          \-

BAGG3         3            UP                  
UP          \-

\# Display port security settings on Device
B.

\[DeviceB]display port-security
interface bridge-aggregation 3

Global port security
parameters:

   Port
security                           : Enabled

   M-LAG load sharing mode (criterion)     : Distributed (local)

   M-LAG member's authentication scope     : Local M-LAG interfaces

   M-LAG member configuration conflict     : Not conflicted

   AutoLearn aging
time                    : 0 min

   Disableport
timeout                     : 20 sec

   Blockmac
timeout                        : 180 sec

   MAC move                                :
Denied

   Authorization
fail                      : Online

   NAS-ID
profile                          : Not configured

   Dot1x-failure
trap                      : Disabled

   Dot1x-logon
trap                        : Disabled

   Dot1x-logoff
trap                       : Disabled

   Intrusion
trap                          : Disabled

   Address-learned
trap                    : Disabled

   Mac-auth-failure
trap                   : Disabled

   Mac-auth-logon
trap                     : Disabled

   Mac-auth-logoff
trap                    : Disabled

   Open
authentication                     : Disabled

  
Traffic-statistics                      : Enabled

   User aging period for
preauth domain    : 82800 sec

   User aging period for
Auth-Fail domain  : 82800 sec

   User aging period for
critical domain   : 82800 sec

   Reauth period for preauth
domain        : 600 sec

   Reauth period for Auth-Fail
domain      : 600 sec

   OUI value
list                          :

 

 Bridge-Aggregation3 is
link-up

   Port
mode                               : macAuthentication

   Pre-auth
domain                         : Not configured

   URL-unavailable
domain                  : Not configured

   NeedToKnow
mode                         : Disabled

   Intrusion protection
mode               : NoAction

   Max secure MAC
addresses                : Not configured

  
Authorization                           : Permitted

   NAS-ID
profile                          : Not configured

   Free VLANs                             
: Not configured

   Open
authentication                     : Disabled

   MAC-move VLAN check
bypass              : Disabled

\# After users pass authentication, display
MAC authentication information on Device B. Verify that Device B and Device A
have the same user authentication information.

\[DeviceB] display
mac-authentication interface bridge-aggregation 3

 Global MAC authentication
parameters:

   MAC
authentication                  : Enabled

   Authentication method               :
CHAP

   M-LAG member configuration conflict : Not conflicted

   Username
format                     : MAC address in lowercase(xxxxxxxxxxxx)

          
Username                    : mac

          
Password                    : Not configured

   MAC range
accounts                  : 0

          MAC address         
Mask                 Username

   Offline detect
period                      : 60 s

   Quiet
period                               : 60 s

   Server
timeout                             : 100 s

   Reauth
period                              : 3600 s

   User aging period for
critical VLAN        : 1000 s

   User aging period for
critical VSI         : 1000 s

   User aging period for guest
VLAN           : 1000 s

   User aging period for guest
VSI            : 1000 s

   User aging period for
critical microsegment: 1000 s

   Temporary user aging
period                : 60 s

   Authentication
domain                      : test

   HTTP proxy port list                      
: Not configured

   HTTPS proxy port
list                      : Not configured

   Max number of silent
MACs                  : 1024 (per slot)

 Online MAC-auth wired
users                  : 10

 

 Silent MAC users:

          MAC address      
VLAN ID  From port               Port index

 

 Bridge-Aggregation3 is
link-up

   MAC
authentication                         : Enabled

   Carry
User-IP                              : Disabled

   Authentication
domain                      : test

   Auth-delay
timer                           : Disabled

   Periodic
reauth                            : Disabled

   Re-auth
server-unreachable                 : Logoff

   Guest
VLAN                                 : Not configured

   Guest VLAN reauthentication               
: Enabled

     Guest VLAN
auth-period                   : 30 s

   Critical
VLAN                              : Not configured

   Critical voice
VLAN                        : Disabled

   Host
mode                                  : Single VLAN

   Offline
detection                          : Enabled

   Authentication
order                       : Default

   User
aging                                 : Enabled

   Server-recovery online-user-sync          
: Disabled

 

   Guest
VSI                                  : Not configured

   Guest VSI
reauthentication                 : Enabled

     Guest VSI
auth-period                    : 30 s

   Critical
VSI                               : Not configured

   Critical microsegment
ID                   : Not configured

   Critical
profile                           : Not configured

   URL user
logoff                            : No

   Auto-tag
feature                           : Disabled

   VLAN tag configuration
ignoring            : Disabled

   Max online
users                           : 4294967295

   Max online preauth-domain
users            : 4294967295

   Max online Auth-Fail-domain
users          : 4294967295

   Auth-server-unavailable
escape             : Disabled

   Authentication
attempts                    : successful 20, failed 3099

   Current online
users                       : 10

          MAC
address       Auth state

         
0000-0000-0001    Authenticated

          0000-0000-0002    Authenticated

         
0000-0000-0003    Authenticated

         
0000-0000-0004    Authenticated

         
0000-0000-0005    Authenticated

         
0000-0000-0006    Authenticated

         
0000-0000-0007    Authenticated

          0000-0000-0008    Authenticated

         
0000-0000-0009    Authenticated

         
0000-0000-000a    Authenticated

## Troubleshooting port security

### Cannot set the port security mode

#### Symptom

Cannot set the port security mode for a
port.

#### Analysis

For a port operating in a port security
mode other than noRestrictions, you cannot change the port security mode by
using the port-security port-mode command.

#### Solution

To resolve the issue:

**1\.**Set the port security mode to noRestrictions.

\[Device-Ten-GigabitEthernet1/0/1] undo port-security
port-mode

**2\.**Set a new port security mode for the port,
for example, autoLearn.

\[Device-Ten-GigabitEthernet1/0/1] port-security
port-mode autolearn

**3\.**If the issue persists, contact H3C Support.

### Cannot configure secure MAC addresses

#### Symptom

Cannot configure secure MAC addresses.

#### Analysis

No secure MAC address can be configured on
a port operating in a port security mode other than autoLearn.

#### Solution

To resolve the issue:

**1\.**Set the port security mode to autoLearn.

\[Device-Ten-GigabitEthernet1/0/1] undo port-security
port-mode

\[Device-Ten-GigabitEthernet1/0/1] port-security
max-mac-count 64

\[Device-Ten-GigabitEthernet1/0/1] port-security
port-mode autolearn

\[Device-Ten-GigabitEthernet1/0/1] port-security
mac-address security 1-1-2 vlan 1

**2\.**If the issue persists, contact H3C Support.

