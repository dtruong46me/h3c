
# Configuring MAC authentication

## About MAC authentication

MAC authentication controls network access
by authenticating source MAC addresses on a port. The feature does not require client
software, and users do not have to enter a username and password for network
access. The device initiates a MAC authentication process when it detects an
unknown source MAC address on a MAC authentication-enabled port. If the MAC
address passes authentication, the user can access authorized network
resources. If the authentication fails, the device marks the MAC address as a
silent MAC address, drops the packet, and starts a quiet timer. The device
drops all subsequent packets from the MAC address within the quiet time. The quiet
mechanism avoids repeated authentication during a short time.

### User account policies

MAC authentication supports the following
user account policies:

·     Global user account
policy, which can be either of the following
options: 

¡     MAC-based user account policy—One MAC-based
user account for each user. As
shown in [Figure 1](#_Ref457317451), the
access device uses the source MAC addresses in packets as the usernames and
passwords of users for MAC authentication. This policy is suitable for an insecure
environment. 

 

|  | NOTE:  MAC-based user account policy also supports configuring a password shared by all MAC-based user accounts. |
| --- | --- |

 

¡     Shared user account policy—One shared user account for all users. You specify one username and
password (which are not necessarily a MAC address) for all MAC authentication
users on the access device, as shown in [Figure 2](#_Ref457318758). This
policy is suitable for a secure environment.

·     MAC range-specific user account
policy—One shared user account for users in a specific
MAC address range. You specify one username and password (which are not
necessarily a MAC address) for users in a specific MAC address range on the
access device, as shown in [Figure 3](#_Ref22370797). For
example, you can specify a username and password for users with a specific OUI
for MAC authentication.

 

| IMPORTANT | IMPORTANT:  ·     You can use global and MAC range-specific user account policies together. For users in a MAC address range, the MAC range-specific user account settings have higher priority than the global user account settings.  ·     If a RADIUS server is used for MAC authentication, you must create the user accounts on the RADIUS server based on the user account policy on the access device. |
| --- | --- |

 

Figure 1 MAC-based user account policy

![](https://resource.h3c.com/en/202407/12/20240712_11705325_x_Img_x_png_1_2216064_294551_0.png)

 

Figure 2 Shared user account policy (global)

![](https://resource.h3c.com/en/202407/12/20240712_11705336_x_Img_x_png_2_2216064_294551_0.png)

 

Figure 3 Shared user account policy (specific
to MAC address ranges)

![](https://resource.h3c.com/en/202407/12/20240712_11705340_x_Img_x_png_3_2216064_294551_0.png)

‌

### Authenticationmethods

You can perform MAC authentication on the
access device (local authentication) or through a RADIUS server.

For more information about configuring
local authentication and RADIUS authentication, see "Configuring AAA."

#### RADIUS authentication

If MAC-based accounts are used, the access
device by default sends the source MAC address of a packet as the username and
password to the RADIUS server for authentication. If a password is configured
for MAC-based accounts, the access device sends the configured password as the
password to the RADIUS server.

If a shared account is used, the access
device sends the shared account username and password to the RADIUS server for
authentication.

The access device and the RADIUS server use
Password Authentication Protocol (PAP) or Challenge Handshake Authentication
Protocol (CHAP) for communication.

#### Local authentication

If MAC-based accounts are used, the access
device by default uses the source MAC address of a packet as the username and
password to search the local account database for a match. If a password is
configured for MAC-based accounts, the device uses the configured password to
search the local account database for a match.

If a shared account is used, the access
device uses the shared account username and password to search the local account
database for a match.

### VLAN assignment

#### Authorization VLAN

The authorization VLAN controls the access
of a MAC authentication user to authorized network resources. The device
supports authorization VLANs assigned locally or by a remote server.

 

| IMPORTANT | IMPORTANT:  Only remote servers can assign tagged authorization VLANs. |
| --- | --- |

‌

#### Remote VLAN authorization

In remote VLAN authorization, you must
configure an authorization VLAN for a user on the remote server. After the user
authenticates to the server, the server assigns authorization VLAN information
to the device. Then, the device assigns the user access port to the
authorization VLAN as a tagged or untagged member.

The device supports assignment of the
following authorization VLAN information by the remote server:

·     VLAN ID.

·     VLAN name, which must be the same as the VLAN
description on the access device.

·     A string of VLAN IDs and VLAN names.

In the string, some VLANs are represented
by their IDs, and some VLANs are represented by their names.

·     VLAN group name.

For more information about VLAN groups,
see Layer 2—LAN Switching Configuration Guide.

·     VLAN ID with a suffix of t
or u.

The t and u suffixes require the device to
assign the access port to the VLAN as a tagged or untagged member,
respectively. For example, 2u indicates assigning the port to VLAN 2 as an untagged member.

If a VLAN name or VLAN group name is
assigned, the device converts the information into a VLAN ID before VLAN assignment.

 

| IMPORTANT | IMPORTANT:  For a VLAN represented by its VLAN name to be assigned successfully, you must make sure the VLAN has been created on the device.  To assign VLAN IDs with suffixes, make sure the user access port is a hybrid or trunk port. |
| --- | --- |

‌

| IMPORTANT | IMPORTANT:  To ensure a successful assignment, the authorization VLANs assigned by the remote server cannot be any of the following types:  ·     Dynamically learned VLANs.  ·     Reserved VLANs.  ·     Super VLANs.  ·     Private VLANs. |
| --- | --- |

‌

If the server assigns a group of VLANs, the
access device selects a VLAN as described in [Table 1](#_Ref480207385).

Table 1 Authorization VLAN selection from a
group of VLANs

| VLAN information | Authorization VLAN selection |
| --- | --- |
| VLANs by IDs  VLANs by names  VLAN group name | The device selects an authorization VLAN from the VLAN group for a user according to the following rules:  ·     On a hybrid port with MAC-based VLAN enabled:  ¡     If the port does not have online users, the device selects the VLAN with the lowest ID.  ¡     If the port has online users, the device selects the VLAN that has the fewest online users. If two VLANs have the same number of online MAC authentication users, the device selects the VLAN with the lower ID.  ·     On an access, trunk, or MAC-based VLAN disabled hybrid port:  ¡     If the port does not have online users, the device selects the VLAN with the lowest ID.  ¡     If the port has online users, the device examines the VLAN group for the VLAN of the online users. If the VLAN is found, the VLAN is assigned to the user as the authorization VLAN. If the VLAN is not found, VLAN authorization fails. || VLAN IDs with suffixes | **1\.**The device selects the leftmost VLAN ID without a suffix, or the leftmost VLAN ID suffixed by u as an untagged VLAN, whichever is more leftmost.  **2\.**The device assigns the untagged VLAN to the port as the PVID, and it assigns the remaining as tagged VLANs. If no untagged VLAN is assigned, the PVID of the port does not change. The port permits traffic from these tagged and untagged VLANs to pass through.  For example, the authentication server sends the string 1u 2t 3 to the access device for a user. The device assigns VLAN 1 as an untagged VLAN and all remaining VLANs (including VLAN 3\) as tagged VLANs. VLAN 1 becomes the PVID. |


‌

#### Local VLAN authorization

To perform local VLAN authorization for a user, specify the VLAN ID
in the authorization attribute list of the local user account for that user.
For each local user, you can specify only one authorization VLAN ID.The user access port is assigned to the VLAN as an untagged member.

 

| IMPORTANT | IMPORTANT:  Local VLAN authorization does not support assignment of tagged VLANs. |
| --- | --- |

‌

For more information about local user
configuration, see "Configuring AAA."

#### Authorization VLAN manipulation on a MAC authentication-enabled port

[Table 2](#_Ref480205467)
describes the way the network access device handles authorization VLANs (except
for the VLANs specified with suffixes) for MAC authenticated users on a port.

Table 2 VLAN manipulation

| Port type | VLAN manipulation |
| --- | --- |
| ·     Access port  ·     Trunk port  ·     Hybrid port with MAC-based VLAN disabled | ·     The device assigns the port to the first authenticated user's authorization VLAN and sets the VLAN as the PVID if that authorization VLAN has the untagged attribute.  ·     If the authorization VLAN has the tagged attribute, the device assigns the port to the authorization VLAN without changing its PVID.  NOTE:  The tagged attribute is supported only on trunk and hybrid ports. || Hybrid port with MAC-based VLAN enabled | The device maps the MAC address of each user to its own authorization VLAN regardless of whether the port is a tagged member. The PVID of the port does not change. |


 

| IMPORTANT | IMPORTANT:  ·     If the users are attached to a port whose link type is access, make sure the authorization VLAN assigned by the server has the untagged attribute. VLAN assignment will fail if the server issues a VLAN that has the tagged attribute.  ·     When you assign VLANs to users attached to a trunk port or a MAC-based VLAN disabled hybrid port, make sure there is only one untagged VLAN. If a different untagged VLAN is assigned to a subsequent user, the user cannot pass authentication.  ·     As a best practice to enhance network security, do not use the port hybrid vlan command to assign a hybrid port to an authorization VLAN as a tagged member. |
| --- | --- |

 

The VLAN assigned by the server to a user
as an authorization VLAN might have been configured on the user access port but
with a different tagging mode. For example, the server assigns an authorization
VLAN with the tagged attribute, but the same VLAN configured on the port has the
untagged attribute. In this situation, the VLAN settings that take effect on
the user depend on the link type of the port.

·     If the link type of the port is access or trunk,
the authorization VLAN settings assigned by the server always take effect on
the user as long as the user is online. After the user goes offline, the VLAN
settings on the port take effect.

·     If the link type of the port is hybrid, the VLAN
settings configured on the port take effect. For example, the server assigns
VLAN 30 with the untagged attribute to a user on the hybrid port. However, VLAN
30 has been configured on the port with the tagged attribute by using the port hybrid
vlan tagged command. Then, the VLAN has the
tagged attribute on the port.

For a MAC authenticated user to access the
network on a hybrid port when no authorization VLAN is configured for the user,
perform one of the following tasks:

·     If the port receives tagged authentication
packets from the user in a VLAN, use the port hybrid vlan command to configure the port as a tagged member in the VLAN.

·     If the port receives untagged authentication
packets from the user in a VLAN, use the port hybrid vlan command to configure the port as an untagged member in the VLAN.

#### Guest VLAN

The MAC authentication guest VLAN on a port
accommodates users that have failed MAC authentication for any reason other
than server unreachable. For example, the VLAN accommodates users with invalid
passwords entered.

You can deploy a limited set of network
resources in the MAC authentication guest VLAN. For example, a software server
for downloading software and system patches.

A hybrid port is always assigned to a MAC
authentication guest VLAN as an untagged member. After the assignment, do not
reconfigure the port as a tagged member in the VLAN.

The device reauthenticates users in the MAC
authentication guest VLAN at a specific interval. [Table 3](#_Ref371509002)
shows the way that the network access device handles guest VLANs for MAC
authentication users.

Table 3 VLAN manipulation

| Authentication status | VLAN manipulation |
| --- | --- |
| A user in the MAC authentication guest VLAN fails MAC authentication. | The user is still in the MAC authentication guest VLAN. || A user in the MAC authentication guest VLAN passes MAC authentication. | The device remaps the MAC address of the user to the authorization VLAN assigned by the authentication server.  If no authorization VLAN is configured for the user on the authentication server, the device remaps the MAC address of the user to the PVID of the port. |


 

#### Critical VLAN

The MAC authentication critical VLAN on a
port accommodates users that have failed MAC authentication because no RADIUS authentication
servers are reachable. Users in a MAC authentication critical VLAN can access only
network resources in the critical VLAN.

The critical VLAN feature takes effect when
MAC authentication is performed only through RADIUS servers. If a MAC
authentication user fails local authentication after RADIUS authentication, the
user is not assigned to the critical VLAN. For more information about the authentication
methods, see "Configuring AAA."

[Table 4](#_Ref371505471) shows
the way that the network access device handles critical VLANs for MAC
authentication users.

Table 4 VLAN manipulation

| Authentication status | VLAN manipulation |
| --- | --- |
| A user fails MAC authentication because all the RADIUS servers are unreachable. | The device maps the MAC address of the user to the MAC authentication critical VLAN.  The user is still in the MAC authentication critical VLAN if the user fails MAC reauthentication because all the RADIUS servers are unreachable.  If no MAC authentication critical VLAN is configured, the device maps the MAC address of the user to the PVID of the port. || A user in the MAC authentication guest VLAN fails authentication because all the RADIUS servers are unreachable. | The user remains in the MAC authentication guest VLAN. || A user in the MAC authentication critical VLAN fails MAC authentication for any reason other than server unreachable. | If a guest VLAN has been configured, the device maps the MAC address of the user to the guest VLAN.  If no guest VLAN is configured, the device maps the MAC address of the user to the PVID of the port. || A user in the MAC authentication critical VLAN passes MAC authentication. | The device remaps the MAC address of the user to the authorization VLAN assigned by the authentication server.  If no authorization VLAN is configured for the user on the authentication server, the device remaps the MAC address of the user to the PVID of the access port. |




 

#### Critical voice VLAN

The MAC authentication critical voice VLAN
on a port accommodates MAC authentication voice users that have failed
authentication because none of the RADIUS servers in their ISP domain are
reachable.

The critical voice VLAN feature takes
effect when MAC authentication is performed only through RADIUS servers. If a MAC
authentication voice user fails local authentication after RADIUS
authentication, the user is not assigned to the critical voice VLAN. For more
information about the authentication methods, see "Configuring AAA."

[Table 5](#_Ref456862503)
shows the way that the network access device handles critical voice VLANs for
MAC authentication voice users.

Table 5 VLAN manipulation

| Authentication status | VLAN manipulation |
| --- | --- |
| A voice user fails MAC authentication because all the RADIUS servers are unreachable. | The device maps the MAC address of the voice user to the MAC authentication critical voice VLAN.  The voice user is still in the MAC authentication critical voice VLAN if the voice user fails MAC reauthentication because all the RADIUS servers are unreachable.  If no MAC authentication critical voice VLAN is configured, the device maps the MAC address of the voice user to the PVID of the port. || A voice user in the MAC authentication critical voice VLAN fails MAC authentication for any reason other than server unreachable. | If a guest VLAN has been configured, the device maps the MAC address of the voice user to the guest VLAN.  If no guest VLAN is configured, the device maps the MAC address of the voice user to the PVID of the port. || A voice user in the MAC authentication critical voice VLAN passes MAC authentication. | The device remaps the MAC address of the voice user to the authorization VLAN assigned by the authentication server.  If no authorization VLAN is configured for the voice user on the authentication server, the device remaps the MAC address of the voice user to the PVID of the access port. |



 

### VSI manipulation

#### MAC authentication support for VXLANs

As shown in [Figure 4](#_Ref462394570), when the
device acts as both a VXLAN VTEP and a NAS, users' service information cannot
be identified by VLANs. To resolve this issue, you must configure the RADIUS
server to assign VSIs to MAC authenticated users. The NAS will map a user's
traffic to the VXLAN that is associated with the user's authorization VSI. The
mapping criteria include the user's access VLAN, access port, and MAC address.

For information about VSIs and VXLANs, see VXLAN Configuration Guide.

Figure 4 VXLAN
network diagramfor MAC authentication

![](https://resource.h3c.com/en/202407/12/20240712_11705346_x_Img_x_png_9_2216064_294551_0.png)

 

#### Authorization VSI

An authorization VSI is associated with a
VXLAN that has network resources inaccessible to unauthenticated users.

MAC authentication supports remote VSI
authorization. If the VTEP does not receive authorization VSI information for a
MAC authentication user from the remote server, the user cannot access
resources in any VXLAN after passing authentication. If the VTEP receives
authorization VSI information for the user from the remote server, it performs
the following operations:

·     Dynamically creates an Ethernet service instance
according to the user's access port, VLAN, and MAC address. 

·     Maps the Ethernet service instance to the
authorization VSI.

The user then can access resources in the
VXLAN associated with the authorization VSI.

For information about dynamic creation of Ethernet
service instances, see VXLAN configuration Guide.

#### Guest VSI

The MAC authentication guest VSI on a port accommodates
users that have failed MAC authentication for any reason other than server
unreachable. For example, the VSI accommodates users with invalid passwords
entered.

You can deploy a limited set of network
resources in the VXLAN that is associated with the MAC authentication guest
VSI. For example, a software server for downloading software and system patches.

[Table 6](#_Ref462397929)
shows the way that the VTEP handles guest VSIs for MAC authentication users.

Table 6 VSI manipulation

| Authentication status | VSI manipulation |
| --- | --- |
| A user fails MAC authentication for any reason other than server unreachable. | The VTEP maps the user's MAC address and access VLAN to the MAC authentication guest VSI. || A user in the MAC authentication guest VSI fails MAC authentication for any reason other than server unreachable. | The user is still in the MAC authentication guest VSI. || A user in the MAC authentication guest VSI passes MAC authentication. | The VTEP remaps the user's MAC address and access VLAN to the authorization VSI assigned by the authentication server. |



 

#### Critical VSI

The MAC authentication critical VSI on a
port accommodates users that have failed MAC authentication because no RADIUS authentication
servers are reachable. Users in a MAC authentication critical VSI can access only
network resources in the VXLAN associated with this VSI.

The critical VSI feature takes effect when MAC
authentication is performed only through RADIUS servers. If a MAC
authentication user fails local authentication after RADIUS authentication, the
user is not assigned to the critical VSI. For more information about the
authentication methods, see "Configuring AAA."

[Table 7](#_Ref462398068)
shows the way that the VTEP handles critical VSIs for MAC authentication users.

Table 7 VSI manipulation

| Authentication status | VSI manipulation |
| --- | --- |
| A user fails MAC authentication because all the RADIUS servers are unreachable. | The VTEP maps the user's MAC address and access VLAN to the MAC authentication critical VSI.  The user is still in the MAC authentication critical VSI if the user fails MAC reauthentication because all the RADIUS servers are unreachable.  If no MAC authentication critical VSI is configured, the device logs off the user. || A user in the MAC authentication critical VSI fails MAC authentication for any reason other than server unreachable. | If a guest VSI has been configured, the VTEP maps the user's MAC address and access VLAN to the guest VSI.  If no guest VSI is configured, the VTEP logs off the user. || A user in the MAC authentication guest VSI fails authentication because all the RADIUS servers are unreachable. | The user remains in the MAC authentication guest VSI. || A user in the MAC authentication critical VSI passes MAC authentication. | The VTEP remaps the user's MAC address and access VLAN to the authorization VSI assigned by the authentication server. |




 

### Microsegment assignment

‌

| IMPORTANT | IMPORTANT:  Only the S5560X-HI switch series and S6520X-HI switch series support this feature. |
| --- | --- |

‌

#### Authorization microsegment

The microsegmentation feature provides
group-based traffic control by applying policy to groups of traffic that has
same attributes. A microsegment is a group of traffic that matches the same
criteria. The RADIUS server can assign a microsegment to a user after that user
passes MAC authentication, Then, the device performs microsegment-based traffic
filtering for that user on the user's access port to make sure the user can
access only the network resources available for the microsegment.

For more information about microsegmentation,
see "Configuring microsegmentation."

 

| IMPORTANT | IMPORTANT:  The following restrictions apply to MAC authentication on a microsegmented network:  ·     The server must assign an authorization ACL, redirect URL, and microsegment to a MAC user if that user must pass Web authentication.  ·     If the server assigns both an authorization ACL and an authorization microsegment to a MAC authentication user that does not require Web authentication, only the authorization microsegment takes effect. |
| --- | --- |

 

#### Critical microsegment

The MAC authentication critical microsegment
on a port accommodates users that have failed MAC authentication because no RADIUS
authentication servers are reachable. Users in a MAC authentication critical microsegment
can access only network resources in the critical microsegment. The
microsegment can belong to a VLAN or VXLAN.

The critical microsegment feature takes
effect when MAC authentication is performed only through RADIUS servers. If a MAC
authentication user fails local authentication after RADIUS authentication, the
user is not assigned to the critical microsegment. For more information about
the authentication methods, see "Configuring AAA."

The access device assigns or removes a MAC
authentication user to or from the critical microsegment depending on their
authentication status, as shown in [Table 8](#_Ref43379805).

Table 8 VSI manipulation

| Authentication status | Microsegment manipulation |
| --- | --- |
| A user fails MAC authentication because all the RADIUS servers are unreachable. | The access device maps the MAC address of the user to the MAC authentication critical microsegment.  The MAC address of the user is still in the MAC authentication critical microsegment if the user fails MAC reauthentication because all the RADIUS servers are unreachable. || A user in the MAC authentication critical microsegment fails MAC authentication for any reason other than server unreachable. | The access device removes the MAC address of the user from the MAC authentication critical microsegment. || A user in the MAC authentication critical microsegment passes MAC authentication. | The access device maps the MAC address of the user to the authorization microsegment assigned by the authentication server. |



 

### ACL assignment

You can specify an authorization ACL for a
MAC authentication user on a remote server or the access device to control the
user's access to network resources. After the user passes MAC authentication,
the server or access device assigns the authorization ACL to the user access
port. Then, the port permits or drops the matching traffic for the user
depending on the rules in the ACL. 

The device supports assignment of static
and dynamic authorization ACLs. 

·     Assignment of static authorization
ACLs—Static ACLs can be assigned by a RADIUS server or the access device. When the server or
access device assigns a static ACL to a user, it assigns only the ACL number. You
must manually create the ACL and configure its rules on the access device.

To change the access permissions of a
user, use one of the following methods:

¡     Modify
ACL rules in the authorization ACL on the access device.

¡     Assign
another ACL to the user as the authorization ACL from the RADIUS server or the
access device.

Static ACLs and their rules can be
manually deleted from the access device.

·     Assignment of dynamic authorization
ACLs—Dynamic ACLs and their rules are
automatically deployed by a RADIUS server, which are not configurable on the
access device. Dynamic ACLs can only be named ACLs. After the device receives a
server-deployed dynamic ACL and its rules, it automatically creates the ACL and
configures its rules.

If a dynamic ACL assigned by the server
to a user has the same name as a static ACL, the dynamic ACL cannot be issued
and the user cannot come online.

A dynamic ACL and its rules are
automatically deleted from the access device after all its users go offline.

Dynamic ACLs and their rules cannot be manually modified or deleted on the access device.
To display information about dynamic ACLs and their rules, use the display
mac-authentication connection or display acl command.

‌

| IMPORTANT | IMPORTANT:  The supported authorization ACLs include the following types:  ·     Basic ACLs, which are numbered in the range of 2000 to 2999\.  ·     Advanced ACLs, which are numbered in the range of 3000 to 3999\.  ·     Layer 2 ACLs, which are numbered in the range of 4000 to 4999\.  For an authorization ACL to take effect, make sure the ACL exists with rules and none of the rules contains the counting, established, fragment, source-mac, cos, dest-mac, lsap, vxlan, or logging keyword.  For more information about ACLs, see ACL and QoS Command Reference. |
| --- | --- |

‌

‌

| IMPORTANT | IMPORTANT:  If the server assigns both an authorization ACL and microsegment to a MAC authentication user on a microsegmented network, only the authorization microsegment takes effect. This restriction applies only to the 5560X-HI switch series and 6520X-HI switch series. |
| --- | --- |

‌

### User profile assignment

You can specify a user profile in the user
account for a MAC authentication user on the authentication server to control the
user's access to network resources. After the user passes MAC authentication,
the authentication server assigns the user profile to the user to filter
traffic for this user. 

The authentication server can be the local
access device or a RADIUS server. In either case, the server only specifies the
user profile name. You must configure the user profile on the access device.

To change the user's access permissions, use
one of the following methods:

·     Modify the user profile configuration on the
access device.

·     Specify another user profile for the user on the
authentication server.

For more information about user profiles,
see "Configuring user profiles."

### Redirect URL assignment

The device supports the URL attribute
assigned by a RADIUS server. During MAC authentication, the HTTP or HTTPS
requests of a user are redirected to the Web interface specified by the
server-assigned URL attribute. After the user passes the Web authentication,
the RADIUS server records the MAC address of the MAC authentication user and
uses a DM (Disconnect Message) to log off the user. When the user initiates MAC
authentication again, it will pass the authentication and come online
successfully.

By default, the device listens to port 6654
for HTTPS requests to be redirected. To change the redirect listening port
number, see configuring HTTP redirect in Layer 3—IP
Services Configuration Guide.

### CAR attribute assignment

The device can use the CAR attributes
assigned through RADIUS extended attributes to control the access rates of
online MAC authenticated users. For information about extended RADIUS
attributes, see "Configuring AAA."

The following CAR attributes are available:

·     Input-Peak-Rate—Peak rate of inbound traffic in bps.

·     Input-Average-Rate—Average rate of inbound traffic in bps.

·     Output-Peak-Rate—Peak rate of outbound traffic in bps.

·     Output-Average-Rate—Average rate of outbound traffic in bps.

If the server assigns CAR attributes for
controlling both the peak and average rates, the device implements double-rate
traffic policing on user traffic. If the server does not assign the Input-Peak-Rate
or Output-Peak-Rate attribute, the device implements single-rate traffic
policing on user traffic. For more information about traffic policing, see QoS
configuration in ACL and QoS Configuration Guide.

### Blackhole MAC attribute assignment

The device supports the blackhole MAC
attribute assigned by the RADIUS authentication server through CoA messages for
users that have passed MAC authentication. Upon receiving a CoA message that
contains the blackhole MAC attribute for a user that has passed MAC
authentication, the device performs the following operations:

**1\.**Logs off the user.

**2\.**Marks the MAC address of the user as a
silent MAC address and starts a quiet timer for the MAC address.

The quiet timer is 10 minutes and is not
user configurable. The device drops all packets from the MAC address after the
quiet timer starts, and it will not authenticate the MAC address until the
quiet timer expires.

To display silent MAC addresses, use the display
mac-authentication command.

### MAC authentication support for critical profiles

If all RADIUS servers in a RADIUS scheme
are not reachable for user authentication, users will be unable to get
authenticated to come online. To make sure users can access certain resources
in this situation, you can specify a critical profile for them on their access
port.

For users in a VPN instance, the resources
defined for that VPN instance in a critical profile take precedence over the
default resources in the critical profile.

### Periodic MAC reauthentication

Periodic MAC reauthentication tracks the
connection status of online users, and updates the authorization attributes
assigned by the RADIUS server. The attributes include the ACL and VLAN.

The device reauthenticates online MAC
authentication users at the periodic reauthentication interval when the periodic
MAC reauthentication feature is enabled. The interval is controlled by a timer
and the timer is user configurable. A change to the periodic reauthentication
timer applies to online MAC authentication users only after the old timer
expires and the MAC authentication users pass authentication.

The server-assigned RADIUS Session-Timeout
(attribute 27\) and Termination-Action (attribute 29\) attributes together can
affect the periodic MAC reauthentication feature. To display the
server-assigned Session-Timeout and Termination-Action attributes, use the display
mac-authentication connection command.

·     If the termination action is to log off users, periodic
MAC reauthentication takes effect only when the periodic reauthentication timer
is shorter than the session timeout timer. If the session timeout timer is
shorter, the device logs off online authenticated users when the session
timeout timer expires.

·     If the termination action is to reauthenticate
users, the periodic MAC reauthentication configuration on the device cannot
take effect. The device reauthenticates online MAC authentication users after
the server-assigned session timeout timer expires.

If no session timeout timer is assigned by
the server, whether the device performs periodic MAC reauthentication depends
on the periodic MAC reauthentication configuration on the device. Support for
the assignment of Session-Timeout and Termination-Action attributes depends on
the server model.

With the RADIUS DAS feature enabled, the
device immediately reauthenticates a user upon receiving a CoA message that
carries the reauthentication attribute from a RADIUS authentication server. In
this case, reauthentication will be performed regardless of whether periodic
MAC reauthentication is enabled on the device. For more information about
RADIUS DAS configuration, see "Configuring AAA."

By default, the device logs off online MAC
authentication users if no server is reachable for MAC reauthentication. The
keep-online feature keeps authenticated MAC authentication users online when no
server is reachable for MAC reauthentication.

The VLANs assigned to an online user before
and after reauthentication can be the same or different.

### RESTful server-assisted MAC authentication user recovery

#### Applicable scenario

After online dumb terminals such as
printers go offline, they cannot reauthenticate to come online until after they
send a service packet. This might cause the dumb terminals to stay offline for
a long time after the device or their attached interface module reboots or
after their attached interfaces fail.

To avoid this situation, you can configure
the device to work with the authentication server and a RESTful server to trigger
MAC reauthentication for dumb terminals or retain their online state.

 

|  | NOTE:  This feature is available only when you use IMC server as the authentication server and the RESTful server. |
| --- | --- |

 

#### Operating mechanism

The following information describes how RESTful
server-assisted MAC authentication user recovery works: 

**1\.**The authentication server includes the shutdown-keep-online proprietary attribute in the
authorization attributes assigned to a dumb terminal after it passes
authentication.

**2\.**After receiving the authentication result
from the authentication server, the device allows the dumb terminal user to
come online and marks it as a dumb terminal user.

**3\.**When a device or interface issue occurs, the
device handles the dumb terminal user as follows:

¡     If
the interface is physical down, the device retains the online state of the dumb
terminal user.

¡     If
the device or the interface module reboots or if the interface recovers from a
failure, the device obtains MAC authentication user information from the RESTful
server to perform MAC reauthentication. 

## Restrictions and guidelines: MAC authentication configuration

When you configure MAC authentication on an
interface, follow these restrictions and guidelines:

·     MAC authentication is supported only on Layer 2
Ethernet interfaces and Layer 2 aggregate interfaces.

·     If you add a Layer 2 Ethernet interface to an
aggregation group, the MAC authentication settings configured on it will not
take effect until it is removed from the aggregation group.

·     Do not delete a Layer 2 aggregate interface if
the interface has online MAC authentication users.

·     Do not change the link type of a port when the MAC
authentication guest VLAN or critical VLAN on the port has users.

On a port, the MAC authentication critical
profile settings are mutually exclusive with the critical VSI, critical VLAN,
and critical microsegment settings for MAC authentication. 

On a port, the MAC authentication critical
profile settings are mutually exclusive with the 802.1X critical VSI, critical
VLAN, and critical microsegment settings.

When you configure MAC authentication VLANs
or VSIs, follow these restrictions and guidelines:

·     If the server assigns both an authorization VSI
and authorization VLAN to a user, the device uses only the authorization VLAN.

·     On a port, the following MAC authentication
settings are mutually exclusive:

¡     Guest
and critical VLAN settings.

¡     Guest
and critical VSI settings.

¡     Critical
microsegment settings. 

·     On a port, the MAC authentication guest and critical VLAN settings are mutually
exclusive with the 802.1X
guest, Auth-Fail, and critical VSI settings.

·     On a port, the MAC authentication guest and critical VSI settings are mutually
exclusive with the 802.1X
guest, Auth-Fail, and critical VLAN settings.

·     To ensure a successful authentication, you must
configure the authentication server to assign authorization VLANs or VSIs to
the MAC authentication users
attached to a port in the following situations:

¡     If
the MAC authentication-enabled port is configured with the guest VLAN or critical VLAN, configure the
authentication server to assign authorization VLANs to MAC authentication
users.

¡     If
the MAC authentication-enabled port is configured with the guest VSI or
critical VSI, configure the authentication server to assign authorization VSIs
to MAC authentication users.

¡     If
both 802.1X and MAC authentication are configured on the port, be careful with
the VSI settings of the two authentication features. 

\-     You must configure VSI authorization for the MAC authentication
users if the port has an Auth-Fail, guest, or critical VSI for 802.1X
authentication. 

\-     Likewise, you must configure VSI authorization for the 802.1X users
if the port has a guest or critical VSI for MAC authentication.

·     As a best practice, do not use Web
authentication in conjunction with any of the following MAC authentication
settings on a port:

¡     Guest
VLAN and critical VLAN settings.

¡     Guest
VSI and critical VSI settings.

¡     Critical
microsegment settings.

·     As a best practice, do not use port security
preauthentication domain in conjunction with any of the following MAC
authentication settings on a port:

¡     Guest
VLAN and critical VLAN settings.

¡     Guest
VSI and critical VSI settings.

¡     Critical
microsegment settings.

¡     Critical
profile settings.

If the MAC address that has failed
authentication is a static MAC address or a MAC address that has passed any
security authentication, the device does not mark the MAC address as a silent
address.

To ensure a successful HTTPS redirect for
users who have received a server-assigned redirect URL, make sure VLAN
interfaces or VSI interfaces exist for the VLANs or VSIs that transport their
packets, respectively. 

When RADIUS DAS is enabled, an M-LAG system
does not support shutting down or rebooting the access ports of MAC
authentication users or reauthenticating the users through CoA messages. For
more information about RADIUS DAS, see "Configuring AAA."

## MAC authentication tasks at a glance

To configure MAC authentication, perform
the following tasks:

**1\.**[Enabling MAC authentication](#_Ref475020194)

**2\.**Configure basic MAC authentication features

¡     [Specifying a MAC authentication method](#_Ref264041450)

¡     [Specifying a MAC authentication domain](#_Ref514665630)

¡     [Configuring user account policy](#_Ref22375149)

¡     (Optional.) [Configuring MAC authentication timers](#_Ref318735015)

¡     (Optional.) [Configuring periodic MAC
reauthentication](#_Ref531781703)

**3\.**(Optional.) Configuring MAC authentication
VLAN assignment

¡     [Configuring a MAC authentication guest VLAN](#_Ref475020391)

¡     [Configuring a MAC authentication
critical VLAN](#_Ref475020395)

¡     [Enabling the MAC authentication critical
voice VLAN feature](#_Ref475020398)

**4\.**(Optional.) Configuring MAC authentication VSI assignment

¡     [Configuring a MAC authentication guest VSI](#_Ref462403947)

¡     [Configuring a MAC authentication
critical VSI](#_Ref522367625)

**5\.**(Optional.) [Specifying a critical profile for MAC
authentication users on a port](#_Ref68682854)

**6\.**(Optional.) Configuring other MAC authentication
features

¡     [Configuring a MAC authentication critical
microsegment](#_Ref43412764)

¡     [Configuring unauthenticated MAC
authentication user aging](#_Ref121413440)

¡     [Configuring MAC authentication offline
detection](#_Ref514665671)

¡     [Configuring packet detection for MAC
authentication](#_Ref114154007)

¡     [Enabling online MAC authentication users
to escape from offline detection and stay online when no reachable RADIUS
authentication servers are available](#_Ref98319502)

¡     [Enabling online user synchronization for
MAC authentication](#_Ref532296048)

¡     [Setting the maximum number of concurrent
MAC authentication users on a port](#_Ref318735020)

¡     [Enabling MAC authentication multi-VLAN
mode on a port](#_Ref456875350)

Perform this task to not reauthenticate
online users when VLAN changes occur on a port.

¡     [Configuring MAC authentication delay](#_Ref342297461)

¡     [Including user IP addresses in MAC authentication
requests](#_Ref480212013)

¡     [Enabling parallel MAC authentication and
802.1X authentication](#_Ref480212021)

¡     [Configuring RESTful server-assisted MAC
authentication user recovery](#_Ref39096772)

¡     [Tracking the availability of the Web
authentication server at a redirect URL](#_Ref98319554)

¡     [Configuring Web proxy ports for URL
redirection in MAC authentication](#_Ref29475161)

¡     [Logging off MAC authentication users](#_Ref536194797)

¡     [Enabling MAC authentication user logging](#_Ref480985356)

## Prerequisites for MAC authentication

Before you configure MAC authentication,
complete the following tasks:

**1\.**Make sure the port security feature is
disabled. For more information about port security, see "Configuring port
security."

**2\.**Configure an ISP domain and specify an AAA
method. For more information, see "Configuring AAA."

¡     For
local authentication, you must also create local user accounts (including
usernames and passwords) and specify the lan-access
service for local users.

¡     For RADIUS
authentication, make sure the device and the RADIUS server can reach each other
and create user accounts on the RADIUS server. If you are using MAC-based
accounts, make sure the username and password for each account are the same as
the MAC address of each MAC authentication user.

## Enabling MAC authentication

#### Restrictions and guidelines

For MAC authentication to take effect on a
port, you must enable this feature globally and on the port.

MAC authentication cannnot take effect on a
port if the device has run out of ACL resources when you perform either of the
following operations:

·     Enable MAC authentication on the port while MAC
authentication has been enabled globally.

·     Enable MAC authentication globally in system
while MAC authentication has been enabled on the port.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enable MAC authentication globally.

mac-authentication

By default, MAC authentication is
disabled globally.

**3\.**Enter interface view.

interface interface-type interface-number

**4\.**Enable MAC authentication on the port.

mac-authentication

By default, MAC authentication is
disabled on a port.

## Specifying a MAC authentication method

#### About this task

RADIUS-based MAC authentication supports
the following authentication methods:

·     PAP—Transports usernames and passwords in plain text. The authentication
method applies to scenarios that do not require high security.

·     CHAP—Transports usernames in plain text and passwords in encrypted form
over the network. CHAP is more secure than PAP.

#### Restrictions and guidelines

The device must use the same authentication
method as the RADIUS server.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Specify an authentication method for MAC
authentication.

mac-authentication authentication-method { chap \| pap }

By default, the device uses PAP for MAC
authentication.

## Specifying a MAC authentication domain

#### About this task

By default, MAC authentication users are in
the system default authentication domain. To implement different access
policies for users, you can use one of the following methods to specify
authentication domains for MAC authentication users:

·     Specify a global authentication domain in system
view. This domain setting applies to all ports enabled with MAC authentication.

·     Specify an authentication domain for an
individual port in interface view. 

MAC authentication chooses an authentication
domain for users on a port in this order: the port-specific domain, the global
domain, and the default domain. For more information about authentication
domains, see "Configuring AAA."

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Specify an authentication domain for MAC
authentication users.

¡     In
system view:

mac-authentication domain domain-name

¡     In
interface view:

interface interface-type
interface-number

mac-authentication domain domain-name

By default, the system default authentication
domain is used for MAC authentication users.

## Configuring user accountpolicy

#### Restrictions and guidelines

For users in a MAC address range, the MAC
address range-specific user account has higher priority than the global user
account settings.

You can configure a maximum of 16 MAC
address ranges and must make sure the MAC address ranges do not overlap. 

If you configure user account settings
multiple times for the same MAC address range, the most recent configuration
overwrites the previous configuration.

The MAC range-specific accounts apply only to
unicast MAC addresses. 

·     If you specify a MAC address range that contains
only multicast MAC addresses, execution of this command will fail. 

·     If you specify a MAC address range that contains
both unicast and multicast MAC addresses, the command takes effect only on
unicast MAC addresses.

The all-zero MAC address is invalid for MAC
authentication. Users with the all-zero MAC address cannot pass MAC
authentication.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Configure the global MAC authentication user
account policy.

¡     Use
one MAC-based user account for each user.

mac-authentication user-name-format mac-address \[ { with-hyphen \[ separator colon ] \| without-hyphen } \[ lowercase \| uppercase ] ] \[ password { cipher \| simple } string ]

¡     Use
one shared user account for all users.

mac-authentication user-name-format fixed \[ account name ] \[ password { cipher \| simple } string ]

By default, the device uses the MAC
address of each user as both the username and password for MAC authentication. The
MAC addresses are in hexadecimal notation without hyphens, and letters are in lower
case.

**3\.**Specify one shared user account specific to a
MAC address range.

mac-authentication mac-range-account mac-address mac-address mask { mask \| mask-length } account name password { cipher \| simple } string

By default, no username or password is configured
specific to a MAC address range. The global user account policy applies to the
users.

## Configuring MAC authentication timers

#### About this task

MAC authentication uses the following
timers:

·     Offline detection timer—Sets the interval that the device must wait for traffic from a user before the device determines that the
user is idle. If the device has
not received traffic from a user before the timer expires, the device logs off that
user and requests the accounting server to stop accounting for the user. This timer takes effect only when the MAC authentication offline detection feature
is enabled. 

As a best practice, set the MAC address
aging timer to the same value as the offline detection timer. This operation
prevents a MAC authenticated user from being logged off within the offline
detection interval because of
MAC address entry expiration.

·     Quiet timer—Sets the interval that the device must wait before the device can
perform MAC authentication for a user that has failed MAC authentication. All
packets from the MAC address are dropped during the quiet time. This quiet
mechanism prevents repeated authentication from affecting system performance.

·     Server timeout timer—Sets the interval that the device waits for a response from a
RADIUS server before the device determines that the RADIUS server is unavailable.
If the timer expires during MAC authentication, the user fails MAC
authentication.

·     Aging timer for temporary MAC
authentication users—Sets the aging timer for
temporary MAC authentication users. This timer starts when a user passes MAC
authentication on a port operating in macAddressAndUserLoginSecureExt
mode. If the device has not received 802.1X protocol packets from the MAC
authenticated user on that port before the timer expires, the device determines
that the user has failed authentication. Then, the device logs off the user. For
more information about the port security mode, see "Configuring port security."

#### Restrictions and guidelines

To avoid forced logoff before the server
timeout timer expires, set the server timeout timer to a value that is lower
than or equal to the product of the following values:

·     The maximum number of RADIUS packet transmission
attempts set by using the retry command in RADIUS
scheme view.

·     The RADIUS server response timeout timer set by
using the timer response-timeout command in RADIUS
scheme view.

For information about setting the maximum
number of RADIUS packet transmission attempts and the RADIUS server response timeout
timer, see "Configuring AAA."

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Configure MAC authentication timers.

mac-authentication timer { offline-detect offline-detect-value \| quiet quiet-value \| server-timeout server-timeout-value \| temporary-user-aging
aging-time-value }

The default settings are as follows:

¡     The
offline detection timer is 300
seconds.

¡     The
quiet timer is 60 seconds.

¡     The
server timeout timer is 100 seconds.

¡     The
temporary user aging timer is 60 seconds.

## Configuring periodic MAC reauthentication

#### Restrictions and guidelines

The device selects a periodic
reauthentication timer for MAC reauthentication in the following order:

**1\.**Server-assigned reauthentication timer.

**2\.**Port-specific reauthentication timer.

**3\.**Global reauthentication timer.

**4\.**Default reauthentication timer.

Modification to the MAC authentication
domain, MAC authentication method, or user account format setting does not
affect the reauthentication of online MAC authentication users. The modified
setting takes effect only on MAC authentication users that come online after
the modification.

If periodic reauthentication is triggered
for a user while that user is waiting for online synchronization, the system
performs online synchronization and does not perform reauthentication for the
user.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Set the periodic MAC reauthentication timer.

¡     Set
a global periodic reauthentication timer.

mac-authentication timer reauth-period reauth-period-value

The default setting is 3600 seconds.

¡     Execute
the following commands in sequence to set a port-specific periodic
reauthentication timer:

interface interface-type
interface-number

mac-authentication timer reauth-period reauth-period-value

quit

By default, no periodic MAC reauthentication
timer is set on a port. The port uses the global periodic MAC reauthentication
timer.

**3\.**Enter interface view.

interface interface-type
interface-number

**4\.**Enable periodic MAC reauthentication.

mac-authentication re-authenticate

By default, periodic MAC reauthentication
is disabled on a port.

**5\.**(Optional.) Enable the keep-online feature
for MAC authenticated users on the port.

mac-authentication re-authenticate server-unreachable
keep-online

By default, the keep-online feature is
disabled. The device logs off online MAC authentication users if no server is
reachable for MAC reauthentication.

In a fast-recovery network, you can use
the keep-online feature to prevent MAC authentication users from coming online
and going offline frequently.

## Configuring a MAC authentication guest VLAN

### Specifying a MAC authentication guest VLAN on a port

#### Restrictions and guidelines

When you configure the MAC authentication guest
VLAN on a port, follow the guidelines in [Table 9](#_Ref476909550).

Table 9 Relationships of the MAC
authentication guest VLAN with other security features

| Feature | Relationship description | Reference |
| --- | --- | --- |
| Quiet feature of MAC authentication | The MAC authentication guest VLAN feature has higher priority.  When a user fails MAC authentication, the user can access the resources in the guest VLAN. The user's MAC address is not marked as a silent MAC address. | See "[Configuring MAC authentication timers](#_Ref318735015)." || Super VLAN | You cannot specify a VLAN as both a super VLAN and a MAC authentication guest VLAN. | See Layer 2—LAN Switching Configuration Guide. || Port intrusion protection | The guest VLAN feature has higher priority than the block MAC action but lower priority than the shutdown port action of the port intrusion protection feature. | See "Configuring port security." |



 

#### Prerequisites

Before you configure the MAC authentication
guest VLAN on a port, complete the following tasks:

·     Create the VLAN to be specified as the MAC
authentication guest VLAN.

·     Configure the port as a hybrid port, and
configure the VLAN as an untagged member on the port.

·     Enable MAC-based VLAN on the port.

For information about VLAN configuration,
see Layer 2—LAN Switching Configuration Guide.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter interface view.

interface interface-type interface-number

**3\.**Specify the MAC authentication guest VLAN on
the port.

mac-authentication guest-vlan guest-vlan-id

By default, no MAC authentication guest
VLAN is specified on a port.

You can configure only one MAC
authentication guest VLAN on a port. The MAC authentication guest VLANs on
different ports can be different.

### Enabling guest VLAN reauthentication in MAC authentication

#### About this task

The guest VLAN reauthentication feature of
MAC authentication enables the device to reauthenticate users in the MAC
authentication guest VLAN on a port at reauthentication intervals.

By default, this feature is enabled. You can
disable it as needed to suppress excessive authentication failure log messages,
which might occur when a network issue results in a large number of
reauthentication failures.

If guest VLAN reauthentication is disabled
on a port, the device does not reauthenticate users in the MAC authentication
guest VLAN on the port. The guest VLAN users will stay in the guest VLAN until
they age out. To control guest VLAN user aging functionality, use the mac-authentication
unauthenticated-user aging enable command. To
configure the aging timer, use the mac-authentication timer user-aging
guest-vlan aging-time-value command. 

#### Restrictions and guidelines

As a best practice, set the
reauthentication interval to a value greater than 30 seconds if the number of
concurrent MAC authentication users on a port is likely to exceed 300\.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter interface view.

interface interface-type
interface-number

**3\.**Enable the guest VLAN reauthentication
feature of MAC authentication on the port.

mac-authentication guest-vlan re-authenticate

By default, the guest VLAN reauthentication
feature of MAC authentication is enabled on a port.

**4\.**Set the authentication interval for users in
the MAC authentication guest VLAN.

mac-authentication guest-vlan auth-period period-value

The default setting is 30 seconds.

## Configuring a MAC authentication critical VLAN

#### Restrictions and guidelines

When you configure the MAC authentication
critical VLAN on a port, follow the guidelines in [Table 10](#_Ref476909680).

Table 10 Relationships of the MAC
authentication critical VLAN with other security features

| Feature | Relationship description | Reference |
| --- | --- | --- |
| Quiet feature of MAC authentication | The MAC authentication critical VLAN feature has higher priority.  When a user fails MAC authentication because no RADIUS authentication server is reachable, the user can access the resources in the critical VLAN. The user's MAC address is not marked as a silent MAC address. | See "[Configuring MAC authentication timers](#_Ref318735015)." || Super VLAN | You cannot specify a VLAN as both a super VLAN and a MAC authentication critical VLAN. | See Layer 2—LAN Switching Configuration Guide. || Port intrusion protection | The critical VLAN feature has higher priority than the block MAC action but lower priority than the shutdown port action of the port intrusion protection feature. | See "Configuring port security." |



 

#### Prerequisites

Before you configure the MAC authentication
critical VLAN on a port, complete the following tasks:

·     Create the VLAN to be specified as the MAC
authentication critical VLAN.

·     Configure the port as a hybrid port, and
configure the VLAN as an untagged member on the port.

·     Enable MAC-based VLAN on the port.

For information about VLAN configuration,
see Layer 2—LAN Switching Configuration Guide.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter interface view.

interface interface-type interface-number

**3\.**Specify the MAC authentication critical VLAN
on the port.

mac-authentication critical vlan critical-vlan-id \[ url-user-logoff ]

By default, no MAC authentication
critical VLAN is specified on a port.

You can configure only one MAC
authentication critical VLAN on a port. The MAC authentication critical VLANs
on different ports can be different.

## Enabling the MAC authentication critical voice VLAN feature

#### Prerequisites

Before you enable the MAC authentication
critical voice VLAN feature on a port, complete the following tasks:

·     Enable LLDP both globally and on the port.

The device uses LLDP to identify voice
users. For information about LLDP, see Layer 2—LAN
Switching Configuration Guide.

·     Enable voice VLAN on the port.

For information about voice VLANs, see Layer 2—LAN Switching Configuration Guide.

·     Specify a MAC authentication critical VLAN on
the port. This setting ensures that a voice user is assigned to the critical
VLAN if it has failed authentication for unreachability of RADIUS servers
before the device recognizes it as a voice user. If a MAC authentication
critical VLAN is not available, the voice user might be logged off instead.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter interface view.

interface interface-type interface-number

**3\.**Enable the MAC authentication critical voice
VLAN feature on a port.

mac-authentication critical-voice-vlan

By default, the MAC authentication
critical voice VLAN feature is disabled on a port.

## Configuring a MAC authentication guest VSI

### Specifying a MAC authentication guest VSI on a port

#### Restrictions and guidelines

The MAC authentication guest VSI feature
has higher priority than the quiet feature of MAC authentication. When a user
fails MAC authentication, the user can access the resources in the guest VSI.
The user's MAC address is not marked as a silent MAC address.

You can configure only one MAC
authentication guest VSI on a port. The MAC authentication guest VSIs on
different ports can be different.

#### Prerequisites

To ensure that the MAC authentication guest
VSI on a port can take effect, complete the following tasks:

·     Enable L2VPN.

·     Create the VSI to be specified as the MAC
authentication guest VSI, and create a VXLAN for the VSI.

·     Enable MAC-based traffic match mode for dynamic Ethernet service instances
on the port.

For more information, see VXLAN Configuration Guide.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter interface view.

interface interface-type interface-number

**3\.**Specify the MAC authentication guest VSI on
the port.

mac-authentication guest-vsi guest-vsi-name

By default, no MAC authentication guest
VSI exists on a port.

### Enabling guest VSI reauthentication in MAC authentication

#### About this task

The guest VSI reauthentication feature of
MAC authentication enables the device to reauthenticate users in the MAC
authentication guest VSI on a port at reauthentication intervals.

You can enable guest VSI reauthentication
by using the mac-authentication guest-vsi re-authenticate command or disable the feature by using the undo form of the command. 

Typically, you disable this feature to
suppress excessive authentication failure log messages, which might occur when
a network issue results in a large number of reauthentication failures.

If guest VSI reauthentication is disabled
on a port, the device does not reauthenticate users in the MAC authentication
guest VSI on the port. The guest VSI users will stay in the guest VSI until
they age out. To configure the aging timer, use the mac-authentication
timer user-aging guest-vsi aging-time-value command. 

#### Restrictions and guidelines

As a best practice, set the
reauthentication interval to a value greater than 30 seconds if the number of
concurrent MAC authentication users on a port is likely to exceed 300\.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter interface view.

interface interface-type
interface-number

**3\.**Enable the guest VSI reauthentication
feature of MAC authentication on the port.

mac-authentication guest-vsi re-authenticate

By default, the guest VSI reauthentication
feature of MAC authentication is enabled on a port.

**4\.**Set the authentication interval for users in
the MAC authentication guest VSI.

mac-authentication guest-vsi auth-period period-value

The default setting is 30 seconds.

## Configuring a MAC authentication critical VSI

#### Restrictions and guidelines

The MAC authentication critical VSI feature
has higher priority than the quiet feature of MAC authentication. When a user
fails MAC authentication, the user can access the resources in the critical
VSI. The user's MAC address is not marked as a silent MAC address.

You can configure only one MAC
authentication critical VSI on a port. The MAC authentication critical VSIs on
different ports can be different.

#### Prerequisites

To ensure that the MAC authentication
critical VSI on a port can take effect, complete the following tasks:

·     Enable L2VPN.

·     Create the VSI to be specified as the MAC authentication
critical VSI, and create a VXLAN for the VSI.

·     Enable MAC-based traffic match mode for dynamic Ethernet service instances
on the port.

For more information, see VXLAN Configuration Guide.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter interface view.

interface interface-type interface-number

**3\.**Specify the MAC authentication critical VSI
on the port.

mac-authentication critical vsi critical-vsi-name
\[
url-user-logoff ]

By default, no MAC authentication
critical VSI exists on a port.

## Specifying a critical profile for MAC authentication users on a port

#### About this task

A critical profile specifies a limited set
of resources accessible to MAC authentication users when they cannot get
authenticated because none of the applicable RADIUS servers is available.

To make sure the MAC authentication users
attached to a port can access the resources in a critical profile in the server
unavailable situation, perform this task. 

#### Restrictions and guidelines

You can specify only one critical profile for
MAC authentication on a port. The critical profiles for MAC authentication on
different ports can be different.

On an interface, the critical profile
settings are mutually exclusive with the critical VSI, critical VLAN, and critical
microsegment settings for MAC authentication. 

For more information, see the command reference
and "[Restrictions and guidelines: MAC
authentication configuration](#_Ref82011937)."

#### Prerequisites

As a best practice, configure the critical
profile before you perform this task. For more information about configuring critical
profiles, see "Configuring AAA."

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter interface view.

interface interface-type interface-number

**3\.**Specify a critical profile for MAC authentication users on the port.

mac-authentication critical profile profile-name

By default, no critical profile is
specified for MAC authentication users on a port.

## Configuring a MAC authentication critical microsegment

#### Hardware and feature compatibility

Only the S5560X-HI switch series and S6520X-HI
switch series support this feature.

#### Restrictions and guidelines

The MAC authentication critical
microsegment feature has higher priority than the MAC authentication quiet
feature. If the device adds a MAC address to a critical microsegment, it will
not add that MAC address to the quiet MAC address list.

You can configure only one MAC
authentication critical microsegment on a port. The MAC authentication critical
microsegments on different ports can be the same or different.

#### Prerequisites

For the critical microsegment you specify
for MAC authentication on a port to take effect, you must create that
microsegment. For more information about microsegments, see "Configuring microsegmentation."

To specify a VSI to match user packets in
VSIs, perform the following tasks:

·     Enable L2VPN.

·     Enable MAC-based traffic match mode for dynamic
Ethernet service instances on the port.

For more information, see VXLAN Configuration Guide.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter interface view.

interface interface-type interface-number

**3\.**Specify the MAC authentication critical microsegment
on the port.

mac-authentication critical microsegment microsegment-id \[ vsi vsi-name ] \[ url-user-logoff
]

By default, no MAC authentication
critical microsegment is configured on a port.

## Configuring unauthenticated MAC authentication user aging

#### About this task

Unauthenticated MAC authentication user
aging applies to users added to the following locations because they have not
been authenticated or have failed authentication:

·     MAC authentication guest or critical VLANs.

·     MAC authentication guest or critical VSIs.

·     MAC authentication critical microsegments.

When a user in one of those locations ages
out, the device removes the user from the location and deletes the MAC address
entry for the user from the access port.

For users in one of those locations on one
port to be authenticated successfully and come online on another port, enable this
feature. In any other scenarios, disable this feature as a best practice. 

#### Restrictions and guidelines

Users removed from an access control
segment upon aging timer expiration will be unable to access the network
resources for that segment until after another authentication is triggered. As
a best practice, use this feature on a port only if you want to have its unauthenticated
users to be authenticated and come online on a different port. 

Only the S5560X-HI switch series and
S6520X-HI switch series support critical microsegments. 

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Set the user aging timer for a type of MAC
authentication VLAN, VSI, or microsegment. 

mac-authentication timer user-aging { critical-microsegment \| critical-vlan \| critical-vsi \| guest-vlan \| guest-vsi } aging-time-value

By default, the user aging timer is 1000
seconds for all applicable types of MAC authentication VLANs, VSIs, and
microsegments. 

**3\.**Enter interface view.

interface interface-type interface-number

**4\.**Enable unauthenticated MAC authentication
user aging. 

mac-authentication unauthenticated-user aging enable

By default, unauthenticated MAC
authentication user aging is enabled. 

## Configuring MAC authentication offline detection

#### About this task

Enable MAC authentication offline detection
to detect idle users on a port. If the port has not received traffic from a
user when the offline detection timer expires, the device logs off that user
and requests the accounting server to stop accounting for the users. For information
about setting the offline detection timer in system view, see "[Configuring MAC authentication timers](#_Ref456867909)."

Disabling this feature disables the device
from inspecting the online user status.

In addition to port-based MAC
authentication offline detection, you can configure offline detection
parameters on a per-user basis, as follows:

·     Set an offline detection timer specific to a user and control whether to use the ARP
snooping or ND snooping table to determine the offline state of the user.

¡     If
the ARP snooping or ND snooping table is used, the device searches the ARP
snooping or ND snooping table before it checks for traffic from the user within
the detection interval. If a matching ARP snooping or ND snooping entry is
found, the device resets the offline detection timer and the user stays online. If the offline detection timer expires because the device has not
found a matching snooping entry for the user or received traffic from the user,
the device disconnects the user.

¡     If
the ARP or ND snooping table is not used, the device disconnects the user if it
has not received traffic from that user before the offline detection timer expires.

When disconnecting the user, the device also
notifies the RADIUS server (if any) to stop user accounting.

·     Skip offline detection for the user. You can
choose this option if the user is a dumb terminal. A dumb terminal might fail
to come online again after it is logged off by the offline detection feature.

The device uses the offline detection
settings for a user in the following sequence:

**1\.**User-specific offline detection settings.

**2\.**Offline detection settings assigned to the
user by the RADIUS server. The settings include the offline detection timer, use of the ARP or ND snooping
table in offline detection, and whether to ignore offline detection.

**3\.**Port-based offline detection settings.

#### Restrictions and guidelines

When MAC authentication offline detection
is enabled on a port, do not set the dynamic MAC entry aging time to a value
that is greater than the default MAC authentication offline detection interval
(300 seconds). To set the dynamic MAC entry aging time, use the mac-address
timer aging seconds command.

For the user-specific offline detection
feature to take effect on a user, make sure the MAC authentication offline
detection feature is enabled on the user's access port. 

The user-specific offline detection
settings take effect on the online users immediately after they are configured.

If you enable MAC authentication offline
detection on a Layer 2 aggregate interface, delay exists for the device to log
off an idle user.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**(Optional.) Configure MAC authentication
offline detection for a user.

mac-authentication offline-detect mac-address mac-address { ignore \| timer offline-detect-value \[ check-arp-or-nd-snooping ] }

By default, offline detection settings
configured on access ports take effect and the offline detection timer set in system view is used.

**3\.**Enter interface view.

interface interface-type interface-number

**4\.**Enable MAC authentication offline detection.

mac-authentication offline-detect enable

By default, MAC authentication offline
detection is enabled on a port.

## Configuring packet detection for MAC authentication

#### About this task

When packet detection for MAC
authentication is enabled on a port, the device sends detection packets to MAC
authentication users connected to that port at offline detection intervals set
by using the offline detection timer. If the device does not receive a response
from a user after it has made the maximum packet transmission attempts within an
offline detection interval, it logs off that user and requests the RADIUS server
to stop accounting for the user.

When packet detection for MAC
authentication and MAC authentication offline detection are both enabled, the
device processes a MAC authentication user as follows:

·     If MAC authentication offline detection
determines that a user is online, the device does not send detection packets to
that user.

·     If MAC authentication offline detection
determines that a user is offline, the device does not immediately logs off that
user. Instead, the device sends a detection packet to that user. It will log
off that user if it does not receive a response from that user after it has
made the maximum packet transmission attempts within an offline detection
interval.

MAC authentication uses ARP request packets
to detect the online status of IPv4 users and uses NS packets to detect the
online status of IPv6 users.

Packet detection adopts the principle of
counting prior to judging. The device decreases the detection attempts (packet
transmission attempts) by 1 only after it determines that it does not receive a
response from a user. The device stops the detection process when the number of
detection attempts becomes 0\. The duration from the time when the user sends
the last packet to the time when the user is logged off is calculated by using
the following formula: duration \= (retries \+ 1\)
\* T \+ X. [Figure 5](#_Ref112937582)
shows the packet detection process. In this example, the device sends a
detection packet to a MAC authentication user for a maximum of two times.

Figure 5 Network diagram for packet
detection process

![](https://resource.h3c.com/en/202407/12/20240712_11705330_x_Img_x_png_14_2216064_294551_0.png)

 

#### Restrictions and guidelines

To ensure that the device is aware of user IP
address changes, enable ARP snooping and ND snooping in conjunction with packet
detection for MAC authentication. If you do not enable ARP snooping or ND snooping,
the device is unaware of user IP address changes. As a result, the device still
sends detection packets to the users' original IP addresses and falsely log off
these users.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Set the offline detection timer.

mac-authentication timer
offline-detect offline-detect-value

By default, the offline detection timer
expires in 300 seconds.

**3\.**Enter interface view.

interface interface-type
interface-number

**4\.**Enable packet detection for MAC
authentication.

mac-authentication packet-detect enable

By default, packet detection for MAC
authentication is disabled.

**5\.**Set the maximum number of attempts for
sending a detection packet to a MAC authentication user.

mac-authentication packet-detect retry retries

By default, the device sends a detection
packet to a MAC authentication user for a maximum of two times.

## Enabling online MAC authentication users to escape from offline detection and stay online when no reachable RADIUS authentication servers are available

#### About this task

The offline detection feature for MAC
authentication logs off an online MAC authentication user if it has not
detected traffic from that user before the offline-detect timer expires. To
prevent online MAC authentication users on a port from being forced offline by
the offline detection feature and stay online when none of the RADIUS servers
in their ISP domain is reachable, perform this task.

#### Restrictions and guidelines

Use this feature only when RADIUS scheme
authentication is configured for the MAC authentication users on a port. If
local or no authentication is also configured, offline detection might be
undesirably disabled for MAC authentication users that come online through
local authentication or without authentication.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter interface view.

interface interface-type interface-number

**3\.**Enable online MAC authentication users to
escape from offline detection and stay online when no reachable RADIUS
authentication servers are available.

mac-authentication auth-server-unavailable escape

By default, online MAC authentication
users cannot escape from offline detection (if enabled) when no reachable
RADIUS authentication servers are available.

## Enabling online user synchronization for MAC authentication

#### About this task

| IMPORTANT | IMPORTANT:  This feature takes effect only when the device uses an IMC RADIUS server to authenticate MAC authentication users. |
| --- | --- |

‌

To ensure that the RADIUS server maintains
the same online MAC authentication user information as the device after the
server state changes from unreachable to reachable, use this feature.

This feature synchronizes online MAC
authentication user information between the device and the RADIUS server when
the RADIUS server state is detected having changed from unreachable to
reachable.

When synchronizing online MAC
authentication user information on a port with the RADIUS server, the device
initiates MAC authentication in turn for each authenticated online MAC
authentication user to the RADIUS server. 

If synchronization fails for an online
user, the device logs off that user unless the failure occurs because the
server has become unreachable again.

#### Restrictions and guidelines

The amount of time required to complete
online user synchronization increases as the number of online users grows. This
might result in an increased delay for new MAC authentication users and users
in the critical VLAN or VSI to authenticate or reauthenticate to the RADIUS
server and come online.

To have this feature take effect, you must
use it in conjunction with the RADIUS server status
detection feature, which is configurable with the radius-server
test-profile command. For more information about
the RADIUS server status detection feature, see "Configuring AAA."

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter interface view.

interface interface-type interface-number

**3\.**Enable online user synchronization for MAC
authentication.

mac-authentication server-recovery online-user-sync

By default, online user synchronization for
MAC authentication is disabled.

## Setting the maximum number of concurrent MAC authentication users on a port

#### About this task

Perform this task to prevent the system
resources from being overused.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter interface view.

interface interface-type interface-number

**3\.**Set the maximum number of concurrent MAC
authentication users on the port.

mac-authentication max-user \[ preauth-domain \| auth-fail-domain ] max-number

The default setting is 4294967295.

## Enabling MAC authentication multi-VLAN mode on a port

#### About this task

By default, MAC authentication single-VLAN
mode applies on a port. In this mode, traffic from an online user cannot be
sent in different VLANs on a port without service interruption. To accommodate
applications that are sensitive to delay or service interruption in a
multi-VLAN environment, for example, IP phones, enable MAC authentication
multi-VLAN mode. 

In multi-VLAN mode, the port forwards
traffic from a user in different VLANs without reauthentication if the user has
been authenticated and come online in any VLAN on the port. Free of
reauthentication, traffic from an online user can be sent in different VLANs without
delay or service interruption.

In single-VLAN mode, the port
reauthenticates an online user when traffic received from that user contains a
VLAN tag different from the VLAN in which the user was authenticated. The
authentication process differs depending on the MAC move setting in port
security and the authorization VLAN assignment status, as follows: 

·     If no authorization VLAN has been assigned to
the online user, the device first logs off the user and then reauthenticates
the user in the new VLAN.

·     If the online user has
been assigned an authorization VLAN, the device handles the user depending on
the MAC move setting in port security. 

¡     If
MAC move is disabled in port security, the user cannot pass authentication and
come online from the new VLAN until after it goes offline from the port.

¡     If
MAC move is enabled in port security, the user can pass authentication on the
new VLAN and come online without having to first go offline from the port.
After the user passes authentication on the new VLAN, the original
authentication session of the user is deleted from the port.

To enable the port security MAC move
feature, use the port-security mac-move permit command.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter interface view.

interface interface-type
interface-number

**3\.**Enable MAC authentication multi-VLAN mode.

mac-authentication host-mode multi-vlan

By default, MAC authentication operates
in single-VLAN mode on a port.

## Configuring MAC authentication delay

#### About this task

When both 802.1X authentication and MAC
authentication are enabled on a port, you can delay MAC authentication so that
802.1X authentication is preferentially triggered. 

If no 802.1X authentication is triggered or
802.1X authentication fails within the delay period, the port continues to
process MAC authentication.

#### Restrictions and guidelines

Do not set the port security mode to mac-else-userlogin-secure or mac-else-userlogin-secure-ext
when you use MAC authentication delay. The delay does not take effect on a port
in either of the two modes. For more information about port security modes, see
"Configuring port security."

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter interface view.

interface interface-type interface-number

**3\.**Enable MAC authentication delay and set the
delay timer.

mac-authentication timer auth-delay time

By default, MAC authentication delay is
disabled.

## Including user IP addresses in MAC authentication requests

#### About this task

| IMPORTANT | IMPORTANT:  This feature can only operate in conjunction with an IMC server. |
| --- | --- |

‌

To avoid IP conflicts that result from
changes to static IP addresses, use this feature on a port that has MAC
authentication users with static IP addresses. 

This feature adds user IP addresses to the MAC
authentication requests sent to the authentication server. When MAC
authentication is triggered for a user, the device checks the user's IP address
for invalidity.

·     If the IP address is valid, the device sends a
MAC authentication request with the IP address included.

·     If the IP address is not a valid host IP address
or the triggering packet does not contain an IP address, the device does not
initiate MAC authentication.

·     If the packet is a DHCP packet with a source IP
address of all zeros, the
device sends a MAC authentication request without including the IP address. In
this case, the IMC server does not examine the user IP address when it performs
authentication.

Upon receipt of the authentication request
that includes a user's IP address, the IMC server compares the user's IP and
MAC addresses with its local IP-MAC mappings. 

·     If an exact match is found or if no match is
found, the user passes MAC authentication.
In the latter case, the server creates an IP-MAC mapping for the user.

·     If a mapping is found for the MAC address but
the IP addresses do not match, the user fails the MAC authentication.

#### Restrictions and guidelines

Do not use this feature in conjunction with
the MAC authentication guest VLAN or guest VSI on a port. The device cannot
perform MAC authentication for a user once that user is added to the MAC
authentication guest VLAN or guest VSI. 

You can specify an ACL to identify source
IP addresses that can or cannot trigger MAC authentication. When you configure
the ACL, follow these guidelines:

·     The specified ACL number represents an IPv4 ACL
and an IPv6 ACL with the same number. For example, if the ACL number is 2000,
you specify both IPv4 ACL 2000 and IPv6 ACL 2000\. The IPv4 ACL and the IPv6 ACL
will be used to process IPv4 packets and IPv6 packets, respectively.

·     Use permit rules to identify source IP addresses
that are valid for MAC authentication. Use deny rules to identify source IP
addresses that cannot trigger MAC authentication.

·     In the rules, only the action keyword (permit or
deny) and the source IP match criteria can take effect.

·     As a best practice, configure a deny rule to
exclude the IPv6 IP addresses that start with fe80 from triggering MAC
authentication. 

·     If you configure permit rules, add a deny all rule at the bottom of the ACL.

 

| IMPORTANT | IMPORTANT:  If the user host is configured with IPv6, the device might receive packets that contain an IPv6 link-local address, which starts with fe80. MAC authentication failure or incorrect MAC-IP binding will occur if this address is used in MAC authentication. To avoid these issues, configure an ACL to exclude the IPv6 IP addresses that start with fe80. |
| --- | --- |

‌

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter interface view.

interface interface-type
interface-number

**3\.**Include user IP addresses in MAC
authentication requests.

mac-authentication carry user-ip \[ exclude-ip acl acl-number ]

By default, a MAC authentication request
does not include the user IP address.

## Enabling parallel MAC authentication and 802.1X authentication

#### About this task

This feature enables a port to perform MAC
authentication in parallel with 802.1X authentication when it receives a packet
from an unknown MAC address. 

This feature applies to the following
situation in which MAC authentication can be performed only after 802.1X
authentication is completed: 

·     The port is configured with both 802.1X
authentication and MAC authentication and performs MAC-based access control for
802.1X authentication.

·     The port is enabled with the 802.1X unicast
trigger.

With parallel authentication enabled, the
port performs MAC authentication while sending a unicast EAP-Request/Identity
packet to trigger 802.1 authentication when it receives a packet from an
unknown MAC address.

If MAC authentication succeeds before 802.1X authentication is
completed, the port is assigned to the MAC authentication authorization VLAN or
VSI. When 802.1X authentication completes, the device manipulates VLAN
assignment depending on the authentication result, as follows:

·     If 802.1X authentication fails, the MAC
authentication result takes effect.

·     If 802.1X authentication succeeds, the device manipulates
VLAN or VSI assignment based on the 802.1X authentication result.

For the port to perform MAC authentication
before it is assigned to the 802.1X guest VLAN or VSI, you must also enable new
MAC-triggered 802.1X guest VLAN or VSI assignment delay. For information about
new MAC-triggered 802.1X guest VLAN or VSI assignment delay, see
"Configuring 802.1X."

#### Restrictions and guidelines

To configure both 802.1X authentication and
MAC authentication on a port, use one of the following methods:

·     Enable the 802.1X and MAC authentication
features separately on the port.

·     Enable port security on the port. The port
security mode must be userlogin-secure-or-mac or userlogin-secure-or-mac-ext.

For information about port security mode
configuration, see "Configuring port security."

For the parallel authentication feature to work correctly, do not enable
MAC authentication delay on the port. This feature will delay MAC
authentication after 802.1X authentication is triggered.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter interface view.

interface interface-type
interface-number

**3\.**Enable parallel MAC authentication and
802.1X authentication on the port.

mac-authentication parallel-with-dot1x

By default, this feature is disabled.

## Configuring RESTful server-assisted MAC authentication user recovery

#### About this task

RESTful server-assisted MAC authentication
user recovery supports the following methods:

·     Automatic recovery—The device automatically obtains MAC authentication user
information and performs a reauthentication after the device or an interface
module reboots or after an interface recovers from a failure. This feature
recovers the online state of MAC authenticated users quickly without waiting
for packets from the MAC authentication users to trigger a reauthentication. It
is helpful when the network has a large number of dumb terminals or the
operation of dumb terminals is important for services.

·     Manual recovery—You trigger a recovery manually. Then, the device obtains MAC
authentication user information from the RESTful server and performs
reauthentication. This method is helpful when automatic recovery fails to
recover the online state of all MAC authenticated users because of link
flapping.

With this feature, you can configure a
maximum of 16 profiles for MAC authentication user recovery. Each profile
contains a set of parameters for accessing a RESTful server and the URI from
which you obtain MAC authentication user information.

#### Restrictions and guidelines

To use this feature, you must use an IMC
server to act as both authentication and RESTful servers.

This feature is mutually exclusive with the
RADIUS accounting-on feature. Do not use the two features together. For more
information about the RADIUS accounting-on feature, see "Configuring AAA."

To ensure that the MAC authentication user
entries obtained from the RESTful server contain user IP addresses, configure
the device to include user IP addresses in the MAC authentication requests sent
to the authentication server. For more information about this feature, see
"[Including user IP addresses in MAC authentication
requests](#_Ref480212013)."

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Configure a MAC authentication user-recovery
profile:

**a.**Create a profile for MAC authentication user
recovery and enter its view.

mac-authentication user-recovery-profile profile-name

**b.**Configure the IP address and port number of the
RESTful server.

server-address { ip ipv4-address \| ipv6 ipv6-address } \[ port port-number ] \[ vpn-instance vpn-instance-name ]

By default, no RESTful servers are
configured.

**c.**Configure the username and password for
accessing the RESTful server.

login-name username \[ password { cipher \| simple } string ]

By default, no username or password is
configured for accessing the RESTful server.

**d.**Configure the NAS IP address used by the
device to communicate with the RESTful server.

nas-ip { ipv4-address \| ipv6 ipv6-address }

By default, no NAS IP address is
configured for the device to communicate with the RESTful server.

The NAS IP address must be the same as that
configured in the authentication RADIUS scheme for the MAC authentication users.

**e.**Specify the URI for obtaining MAC
authentication user information from the RESTful server.

uri uri-string

By default, no URIs are specified.

The URI can only be imcrs/uam/online/notAgingMuteTerminal. Other URIs cannot take effect.

**3\.**Return to system view.

quit

**4\.**Enable automatic MAC authentication user
recovery.

mac-authentication auto-recover-user profile profile-name

By default, automatic MAC authentication
user recovery is disabled.

**5\.**Return to user view.

quit

**6\.**(Optional.) Manually recover MAC
authentication users.

mac-authentication recover-user profile profile-name \[ interface interface-type
interface-number

## Tracking the availability of the Web authentication server at a redirect URL

#### About this task

By default, the device does not track the
availability of the Web authentication server at the redirect URL assigned to
MAC authentication users. On the device, the state of the Web authentication server
will always be active, regardless of its availability. To have the MAC
authentication module log off MAC authentication users that have been assigned
a redirect URL promptly when the Web authentication server at that URL becomes
unavailable, associate that redirect URL with a track entry to detect its
connectivity.

As a best practice, configure the track
entry associated with the redirect URL to collaborate with an HTTP NQA
operation for detection. For more information about Track, see High Availability Configuration Guide. For more
information about NQA, see Network Management and
Monitoring Configuration Guide.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Associate a redirect URL for MAC
authentication users with a track entry.

mac-authentication redirect-url url-string track track-entry-number

By default, no redirect URLs for MAC
authentication users are associated with a track entry.

## Configuring Web proxy ports for URL redirection in MAC authentication

#### About this task

By default, the device redirects the HTTP
or HTTPS requests from a MAC authenticated user to the authorized URL only if
the requests are sent from a browser with Web proxy disabled. The device
discards these requests if they are sent from a browser configured with a Web proxy.

To prevent the HTTP or HTTPS requests from
being discarded, add the TCP port numbers of the Web proxy for HTTP and HTTPS
to the device. These TCP port numbers are called Web proxy ports in this
document.

#### Restrictions and guidelines

You can configure a maximum of 64 Web proxy
ports for MAC authenticated users.

Specify different Web proxy port numbers
for HTTP and HTTPS requests.

Adding or removing a Web proxy port will
cause the device to log off all online MAC authenticated users that have been assigned
an authorization redirect URL.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Specify a Web proxy port used by MAC
authentication users for HTTP or HTTPS.

mac-authentication web-proxy { http \| https } port port-number

By default, no Web proxy ports are specified.

## Logging off MAC authentication users

#### About this task

Perform this task to log off specified MAC
authentication users and clear information about these users from the device.
These users must perform MAC authentication to come online again.

#### Procedure

To log off MAC authentication users,
execute the following command in user view:

reset mac-authentication access-user \[ interface interface-type interface-number \| mac mac-address
\| microsegment
microsegment-id \|  online-type { auth-fail-domain \| critical-domain \| preauth-domain \| success \| url-unavailable-domain
} \| username username \| vlan vlan-id \| vsi vsi-name ]

## Enabling MAC authentication user logging

#### About this task

This feature enables the device to generate
logs about MAC authentication users and send the logs to the information
center. For the logs to be output correctly, you must also configure the
information center on the device. For more information about information center
configuration, see Network Management and Monitoring Configuration
Guide.

#### Restrictions and guidelines

To prevent excessive MAC authentication
user log entries, use this feature only if you need to analyze abnormal MAC
authentication user logins or logouts.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enable MAC authentication user logging.

mac-authentication access-user log enable \[ failed-login \| logoff \|
successful-login ] \*

By default, MAC authentication user
logging is disabled.

If you do not specify any parameters,
this command enables all types of MAC authentication user logs.

## Display and maintenance commands for MAC authentication

‌

| IMPORTANT | IMPORTANT:  Only the S5560X-HI switch series and S6520X-HI switch series support the microsegment-related commands and parameters. |
| --- | --- |

‌

Execute display commands
in any view and reset commands in user
view.

 

| Task | Command |
| --- | --- |
| Display MAC authentication information. | display mac-authentication \[ interface interface-type interface-number ] || Display MAC authentication connections. | display mac-authentication connection \[ open ] \[ \[ m-lag \[ local \| peer ] ] \[ interface interface-type interface-number \| online-type { auth-fail-domain \| critical-domain \| preauth-domain \| success \| url-unavailable-domain } \| slot slot-number \| user-name user-name ] \| user-mac mac-address ] || Display the MAC addresses of MAC authentication users in a type of MAC authentication microsegment, VLAN, or VSI. | display mac-authentication mac-address { critical-microsegment \| critical-vlan \| critical-vsi \| guest-vlan \| guest-vsi } \[ interface interface-type interface-number ] || Clear MAC authentication statistics. | reset mac-authentication statistics \[ interface interface-type interface-number ] || Remove users from the MAC authentication critical VLAN on a port. | reset mac-authentication critical vlan interface interface-type interface-number \[ mac-address mac-address ] || Remove users from the MAC authentication critical voice VLAN on a port. | reset mac-authentication critical-voice-vlan interface interface-type interface-number \[ mac-address mac-address ] || Remove users from the MAC authentication critical microsegment on a port. | reset mac-authentication critical microsegment interface interface-type interface-number \[ mac-address mac-address ] || Remove users from the MAC authentication guest VLAN on a port. | reset mac-authentication guest-vlan interface interface-type interface-number \[ mac-address mac-address ] || Remove users from the MAC authentication critical VSI on a port. | reset mac-authentication critical vsi interface interface-type interface-number \[ mac-address mac-address ] || Remove users from the MAC authentication guest VSI on a port. | reset mac-authentication guest-vsi interface interface-type interface-number \[ mac-address mac-address ] |










 

## MAC authentication configuration examples

### Example: Configuring local MAC authentication

#### Network configuration

As shown in [Figure 6](#_Ref340244784), the
device performs local MAC authentication on Ten-GigabitEthernet 1/0/1 to control
Internet access of users.

Configure the device to meet the following
requirements:

·     Detect whether a user has gone offline every 180
seconds.

·     Deny a user for 180 seconds if the user fails MAC
authentication.

·     Authenticate all users in ISP domain bbb.

·     Use the MAC address of each user as both the
username and password for authentication. The MAC addresses are in hexadecimal
notation with hyphens, and letters are in lower case.

Figure 6 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705335_x_Img_x_png_19_2216064_294551_0.png)

‌

#### Procedure

\# Add a network access local user. In this
example, configure both the username and password as Host A's MAC address 08-00-27-12-34-56.


\<Device\> system-view

\[Device] local-user 08-00-27-12-34-56
class network

\[Device-luser-network-08-00-27-12-34-56]
password simple 08-00-27-12-34-56

\# Specify the LAN access service for the user.

\[Device-luser-network-08-00-27-12-34-56]
service-type lan-access

\[Device-luser-network-08-00-27-12-34-56]
quit

\# Configure ISP domain bbb to perform local authentication for LAN users.

\[Device] domain bbb

\[Device-isp-bbb] authentication lan-access
local

\[Device-isp-bbb] quit

\# Enable MAC authentication on Ten-GigabitEthernet 1/0/1.

\[Device] interface ten-gigabitethernet 1/0/1

\[Device-Ten-GigabitEthernet1/0/1] mac-authentication

\[Device-Ten-GigabitEthernet1/0/1] quit

\# Specify ISP domain bbb
as the MAC authentication domain.

\[Device] mac-authentication domain bbb

\# Configure MAC authentication timers.

\[Device] mac-authentication timer offline-detect
180

\[Device] mac-authentication timer quiet
180

\# Use the MAC address of each user as both
the username and password for MAC authentication. The MAC addresses are in hexadecimal
notation with hyphens, and letters are in lower case.

\[Device] mac-authentication user-name-format
mac-address with-hyphen lowercase

\# Enable MAC authentication globally.

\[Device] mac-authentication

#### Verifying the configuration

\# Display MAC authentication settings and
statistics to verify your configuration.

\[Device] display mac-authentication

 Global MAC authentication
parameters:

   MAC authentication                         :
Enabled

   Authentication method                      :
PAP

   M-LAG member configuration
conflict        : Unknown

   User name format                           :
MAC address in lowercase(xx-xx-xx-xx-xx-xx)

           Username                           :
mac

           Password                           :
Not configured

   Offline detect period                      :
180 s

   Quiet period                               :
180 s

   Server timeout                             :
100 s

   Reauth period                              :
3600 s

   User aging period for critical
VLAN        : 1000 s

   User aging period for critical
VSI         : 1000 s

   User aging period for guest
VLAN           : 1000 s

   User aging period for guest
VSI            : 1000 s

   User aging period for critical microsegment:
1000 s

   Authentication domain                      :
bbb

   HTTP proxy port
list                       : Not configured

   HTTPS proxy port
list                      : Not configured

 Online MAC-auth wired users                  :
1

 

 Silent MAC users:

          MAC address       VLAN ID 
From port               Port index

          00e0-fc11-1111    8        XGE1/0/1           
    1

 

 Ten-GigabitEthernet1/0/1 is link-up

   MAC authentication                         :
Enabled

   Carry User-IP                              :
Disabled

   Authentication domain                      :
Not configured

   Auth-delay timer                           :
Disabled

   Periodic reauth                            :
Disabled

   Re-auth server-unreachable                 :
Logoff

   Guest VLAN                                 :
Not configured

   Guest VLAN reauthentication                :
Enabled

     Guest VLAN auth-period                   :
30 s

   Critical VLAN                              :
Not configured

   Critical voice VLAN                        :
Disabled

   Host mode                                  :
Single VLAN

   Offline detection                          :
Enabled

   Authentication order                       :
Default

   User aging                                 :
Enabled

   Server-recovery online-user-sync         
 : Enabled

 

   Guest VSI                                  :
Not configured

   Guest VSI reauthentication                 :
Enabled

     Guest VSI auth-period                    :
30 s

   Critical VSI                               :
Not configured

   Critical microsegment ID                   :
Not configured

   URL user logoff                            :
No

   Auto-tag feature                           :
Disabled

   VLAN tag configuration ignoring         
  : Disabled

   Max online users                           :
4294967295

   Max online preauth-domain
users            : 4294967295

   Max online Auth-Fail-domain
users          : 4294967295

   Auth-server-unavailable
escape             : Enabled

   Authentication attempts                    :
successful 1, failed 0

   Current online users                       :
1

          MAC address       Auth
state

          0800-2712-3456   
Authenticated

The output shows that Host A has passed MAC
authentication and has come online. Host B failed MAC authentication and its
MAC address is marked as a silent MAC address.

### Example: Configuring RADIUS-based MAC authentication

#### Network configuration

As shown in [Figure 7](#_Ref342643477), the
device uses RADIUS servers to perform authentication, authorization, and
accounting for users. The RADIUS servers use the CHAP authentication method.

To control user access to the Internet by
MAC authentication, perform the following tasks:

·     Enable MAC authentication globally and on Ten-GigabitEthernet 1/0/1.

·     Configure the device to use CHAP for MAC
authentication.

·     Configure the device to detect whether a user
has gone offline every 180 seconds. 

·     Configure the device to deny a user for 180
seconds if the user fails MAC authentication.

·     Configure all users to belong to ISP domain bbb.

·     Use a shared user account for all users, with
username aaa and password 123456.

Figure 7 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705337_x_Img_x_png_20_2216064_294551_0.png)

‌

#### Procedure

Make sure the RADIUS servers and the access
device can reach each other.

**1\.**Configure the RADIUS servers to provide
authentication, authorization, and accounting services. Create a shared account
with username aaa and password 123456 for MAC authentication users. (Details not shown.)

**2\.**Configure RADIUS-based MAC authentication on
the device:

\# Configure a RADIUS scheme.

\<Device\> system-view

\[Device] radius scheme 2000

\[Device-radius-2000] primary
authentication 10.1.1.1 1812

\[Device-radius-2000] primary
accounting 10.1.1.2 1813

\[Device-radius-2000] key
authentication simple abc

\[Device-radius-2000] key
accounting simple abc

\[Device-radius-2000]
user-name-format without-domain

\[Device-radius-2000] quit

\# Specify CHAP as the authentication
method for MAC authentication.

\[Device] mac-authentication
authentication-method chap

\# Apply the RADIUS scheme to ISP domain bbb for
authentication, authorization, and accounting.

\[Device] domain bbb

\[Device-isp-bbb] authentication
default radius-scheme 2000

\[Device-isp-bbb] authorization
default radius-scheme 2000

\[Device-isp-bbb] accounting default
radius-scheme 2000

\[Device-isp-bbb] quit

\# Enable MAC authentication on Ten-GigabitEthernet 1/0/1.

\[Device] interface ten-gigabitethernet 1/0/1

\[Device-Ten-GigabitEthernet1/0/1] mac-authentication

\[Device-Ten-GigabitEthernet1/0/1] quit

\# Specify the MAC authentication domain
as ISP domain bbb.

\[Device] mac-authentication domain
bbb

\# Set MAC authentication timers.

\[Device] mac-authentication timer
offline-detect 180

\[Device] mac-authentication timer
quiet 180

\# Specify username aaa and password
123456 in plain text for the account shared by MAC authentication users.

\[Device] mac-authentication user-name-format
fixed account aaa password simple 123456

\# Enable MAC authentication globally.

\[Device] mac-authentication

#### Verifying the configuration

\# Verify the MAC authentication configuration.

\[Device] display mac-authentication

 Global MAC authentication parameters:

   MAC authentication                         :
Enabled

   Authentication method                      :
CHAP

   M-LAG member configuration
conflict        : Unknown

   Username format                            :
Fixed account

           Username                           :
aaa

           Password                           :
\*\*\*\*\*\*

   Offline detect period                      :
180 s

   Quiet period                               :
180 s

   Server timeout                             :
100 s

   Reauth period                              :
3600 s

   User aging period for critical
VLAN        : 1000 s

   User aging period for critical
VSI         : 1000 s

   User aging period for guest
VLAN           : 1000 s

   User aging period for guest
VSI            : 1000 s

   User aging period for critical microsegment:
1000 s

   Authentication domain                      :
bbb

   HTTP proxy port
list                       : Not configured

   HTTPS proxy port
list                      : Not configured

 Online MAC-auth wired users                  :
1

 

 Silent MAC users:

          MAC address       VLAN ID 
From port               Port index

 

 Ten-GigabitEthernet1/0/1  is link-up

   MAC authentication                         :
Enabled

   Carry User-IP                              :
Disabled

   Authentication domain                      :
Not configured

   Auth-delay timer                           :
Disabled

   Periodic reauth                            :
Disabled

   Re-auth server-unreachable         
       : Logoff

   Guest VLAN                                 :
Not configured

   Guest VLAN reauthentication         
      : Enabled

     Guest VLAN auth-period                   :
30 s

   Critical VLAN                              :
Not configured

   Critical voice VLAN                        :
Disabled

   Host mode                                  :
Single VLAN

   Offline detection                          :
Enabled

   Authentication order                       :
Default

   User aging                                 :
Enabled

   Server-recovery online-user-sync         
 : Enabled

 

   Guest VSI                                  :
Not configured

   Guest VSI reauthentication                 :
Enabled

     Guest VSI auth-period                    :
30 s

   Critical VSI                               :
Not configured

   Critical microsegment ID                   :
Not configured

   URL user logoff                            :
No

   Auto-tag feature                           :
Disabled

   VLAN tag configuration ignoring         
  : Disabled

   Max online users                           :
4294967295

   Max online preauth-domain
users            : 4294967295

   Max online Auth-Fail-domain
users          : 4294967295

   Auth-server-unavailable
escape             : Enabled

   Authentication attempts                    :
successful 1, failed 0

   Current online users                       :
1

          MAC address       Auth state

          0800-2712-3456    Authenticated

### Example: Configuring ACL assignment for MAC authentication

#### Network configuration

As shown in [Figure 8](#_Ref158436006), configure
the device to meet the following requirements:

·     Use RADIUS servers to perform authentication,
authorization, and accounting for users.

·     Perform MAC authentication on Ten-GigabitEthernet 1/0/1 to control Internet
access.

·     Use the MAC address of each user as both the
username and password for MAC authentication. The MAC addresses are in hexadecimal
notation with hyphens, and letters are in lower case.

·     Use an ACL to deny authenticated users to access
the FTP server at 10.0.0.1.

Figure 8 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705338_x_Img_x_png_21_2216064_294551_0.png)

‌

#### Procedure

Make sure the RADIUS servers and the access
device can reach each other.

**1\.**Configure the RADIUS servers:

\# Configure the RADIUS
servers to provide authentication, authorization, and accounting services.
(Details not shown.)

\# Add a user account with 08-00-27-12-34-56 as both the username and password on each RADIUS server. (Details
not shown.)

\# Specify ACL 3000 as the authorization
ACL for the user account. (Details not shown.)

**2\.**Configure ACL 3000 to deny packets destined
for 10.0.0.1 on the device.

\<Device\> system-view

\[Device] acl advanced 3000

\[Device-acl-ipv4-adv-3000] rule
0 deny ip destination 10.0.0.1 0

\[Device-acl-ipv4-adv-3000]
quit

**3\.**Configure RADIUS-based MAC authentication on
the device:

\# Configure a RADIUS scheme.

\[Device] radius scheme 2000

\[Device-radius-2000] primary
authentication 10.1.1.1 1812

\[Device-radius-2000] primary
accounting 10.1.1.2 1813

\[Device-radius-2000] key
authentication simple abc

\[Device-radius-2000] key
accounting simple abc

\[Device-radius-2000]
user-name-format without-domain

\[Device-radius-2000] quit

\# Apply the RADIUS scheme to an ISP
domain for authentication, authorization, and accounting.

\[Device] domain bbb

\[Device-isp-bbb] authentication
default radius-scheme 2000

\[Device-isp-bbb] authorization
default radius-scheme 2000

\[Device-isp-bbb] accounting default
radius-scheme 2000

\[Device-isp-bbb] quit

\# Specify the ISP domain for MAC
authentication.

\[Device] mac-authentication domain
bbb

\# Use the MAC address of each user as
both the username and password for MAC authentication. The MAC addresses are in
hexadecimal notation with hyphens, and letters are in lower case.

\[Device] mac-authentication
user-name-format mac-address with-hyphen lowercase

\# Enable MAC authentication on Ten-GigabitEthernet 1/0/1.

\[Device] interface ten-gigabitethernet 1/0/1

\[Device-Ten-GigabitEthernet1/0/1] mac-authentication

\[Device-Ten-GigabitEthernet1/0/1] quit

\# Enable MAC authentication globally. 

\[Device] mac-authentication

#### Verifying the configuration

\# Verify the MAC authentication configuration.

\[Device] display mac-authentication

 Global MAC authentication parameters:

   MAC authentication                         :
Enable

   Authentication method                      :
PAP

   M-LAG member configuration
conflict        : Unknown

   Username
format                            : MAC address in lowercase(xx-xx-xx-xx-xx-xx)

           Username                           :
mac

           Password                           :
Not configured

   Offline detect period                      :
300 s

   Quiet period                               :
60 s

   Server timeout                     
       : 100 s

   Reauth period                              :
3600 s

   User aging period for critical
VLAN        : 1000 s

   User aging period for critical
VSI         : 1000 s

   User aging period for guest
VLAN           : 1000 s

   User aging period for guest
VSI            : 1000 s

   User aging period for critical microsegment:
1000 s

   Authentication domain                      :
bbb

   HTTP proxy port
list                       : Not configured

   HTTPS proxy port
list                      : Not configured

 Online MAC-auth wired users                  :
1

 

 Silent MAC users:

          MAC address       VLAN ID 
From port               Port index

 

 Ten-GigabitEthernet1/0/1  is link-up

   MAC authentication                         :
Enabled

   Carry User-IP                              :
Disabled

   Authentication domain                      :
Not configured

   Auth-delay timer                           :
Disabled

   Periodic reauth                            :
Disabled

   Re-auth server-unreachable                 :
Logoff

   Guest VLAN                                 :
Not configured

   Guest VLAN reauthentication                :
Enabled

     Guest VLAN auth-period                   :
30 s

   Critical VLAN                              :
Not configured

   Critical voice VLAN                        :
Disabled

   Host mode                                  :
Single VLAN

   Offline detection                          :
Enabled

   Authentication order                       :
Default

   User aging                                 :
Enabled

   Server-recovery online-user-sync         
 : Enabled

 

   Guest VSI                                  :
Not configured

   Guest VSI reauthentication         
       : Enabled

     Guest VSI auth-period                    :
30 s

   Critical VSI                               :
Not configured

   Critical microsegment ID                   :
Not configured

   URL user logoff                            :
No

   Auto-tag feature                           :
Disabled

   VLAN tag configuration ignoring         
  : Disabled

   Max online users                           :
4294967295

   Max online preauth-domain
users            : 4294967295

   Max online Auth-Fail-domain
users          : 4294967295

   Auth-server-unavailable
escape             : Enabled

   Authentication attempts                    :
successful 1, failed 0

   Current online users                       :
1

          MAC address       Auth
state

          08-00-2712-3456    Authenticated

\# Verify that you cannot ping the FTP
server from the host.

C:\\\>ping 10.0.0.1

 

Pinging 10.0.0.1 with 32 bytes of
data:

 

Request timed out.

Request timed out.

Request timed out.

Request timed out.

 

Ping statistics for 10.0.0.1:

   Packets: Sent \= 4, Received \= 0,
Lost \= 4 (100% loss),

The output shows that ACL 3000 has been
assigned to Ten-GigabitEthernet 1/0/1 to deny access to
the FTP server.

### Example: Configuring MAC authentication authorization VSI assignment

#### Network configuration

As shown in [Figure 9](#_Ref462400959),
configure the device to meet the following requirements:

·     Use RADIUS servers to perform authentication,
authorization, and accounting for users.

·     Perform MAC authentication on Ten-GigabitEthernet 1/0/1 to control Internet
access.

·     Configure the RADIUS server to assign VSI bbb to the host when the host passes MAC authentication.

·     Authenticate all users in ISP domain 2000.

·     Use the MAC address of each user as both the
username and password for MAC authentication. The MAC addresses are in hexadecimal
notation with hyphens, and letters are in lower case.

Figure 9 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705339_x_Img_x_png_22_2216064_294551_0.png)

‌

#### Procedure

Make sure the RADIUS servers and the access
device can reach each other.

**1\.**Configure the RADIUS servers:

\# Configure the RADIUS servers to provide
authentication, authorization, and accounting services. (Details not shown.)

\# Add a user account with d4-85-64-be-c6-3e as both the username and password on each RADIUS server. (Details
not shown.)

\# Specify VSI bbb as the
authorization VSI for the user account. (Details not shown.)

 

|  | NOTE:  If an ADCAM server is used for authentication and authorization, configure VSIs on the server. The server will assign these VSIs to the device. You do not need to configure VSIs on the device. |
| --- | --- |

 

**2\.**Configure RADIUS-based MAC authentication on
the device:

\# Configure a RADIUS scheme.

\<Device\> system-view

\[Device] radius scheme bbb

\[Device-radius-bbb] primary
authentication 10.1.1.1

\[Device-radius-bbb] primary
accounting 10.1.1.2

\[Device-radius-bbb] key
authentication simple bbb

\[Device-radius-bbb] key
accounting simple bbb

\[Device-radius-bbb]
user-name-format without-domain

\[Device-radius-bbb] quit

\# Apply the RADIUS scheme to ISP domain 2000 for
authentication, authorization, and accounting.

\[Device] domain 2000

\[Device-isp-2000] authentication
lan-access radius-scheme bbb

\[Device-isp-2000] authorization
lan-access radius-scheme bbb

\[Device-isp-2000] accounting
lan-access radius-scheme bbb

\[Device-isp-2000] quit

\# Enable MAC authentication on Ten-GigabitEthernet 1/0/1.

\[Device] interface ten-gigabitethernet 1/0/1

\[Device-Ten-GigabitEthernet1/0/1] mac-authentication

\# Enable the MAC match mode for dynamic
Ethernet service instances on Ten-GigabitEthernet 1/0/1.

\[Device-Ten-GigabitEthernet1/0/1] mac-based ac

\[Device-Ten-GigabitEthernet1/0/1] quit

\# Enable L2VPN.

\[Device] l2vpn enable

\# Create a VSI named bbb and the
associated VXLAN.

\[Device] vsi bbb

\[Device-vsi-bbb] vxlan 5

\[Device-vsi-bbb-vxlan-5] quit

\# Specify the ISP domain for MAC
authentication.

\[Device] mac-authentication domain
2000

\# Use the MAC address of each user as
both the username and password for MAC authentication. The MAC addresses are in
hexadecimal notation with hyphens, and letters are in lower case.

\[Device] mac-authentication
user-name-format mac-address with-hyphen lowercase

\# Enable MAC authentication globally. 

\[Device] mac-authentication

#### Verifying the configuration

\# Verify that VSI bbb
is assigned to the MAC authentication user after the user passes
authentication.

\[Device] display mac-authentication
connection

Total connections: 1

Slot ID: 1

User MAC address: d485-64be-c63e

Access interface: Ten-GigabitEthernet1/0/1

Username: d4-85-64-be-c6-3e

User access state: Successful

Authentication domain: 2000

IPv4 address: 192.168.1.1

IPv6 address:
2000:0:0:0:1:2345:6789:abcd

Initial VLAN: 1

Authorization untagged VLAN: N/A

Authorization tagged VLAN: N/A

Authorization VSI: bbb

Authorization microsegment ID: N/A

Authorization ACL ID: N/A

Authorization user profile: N/A

Authorization CAR:

  Average input rate: 102400 bps

  Peak input rate: 204800 bps

  Average output rate: 102400 bps

  Peak output rate: 204800 bps

Authorization URL: N/A

Termination action: N/A

Session timeout period: N/A

Offline detection: 300 sec
(server-assigned)

Online from: 2016/06/13 09:06:37

Online duration: 0h 0m 35s

\# Verify that a dynamic AC is created for
MAC address d485-64be-c63e.

\[Device] display l2vpn forwarding ac
verbose

VSI Name: bbb

  Interface: XGE1/0/1  Service Instance: 1

    Link ID      : 0

    Access Mode  : VLAN

    Encapsulation: untagged

    Type         : Dynamic
(MAC-based)

    MAC address  : d485-64be-c63e

