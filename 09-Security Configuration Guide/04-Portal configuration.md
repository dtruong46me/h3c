
# Configuring portal authentication

## About portal authentication

Portal authenticationcontrols user
access to networks. Portal authenticates a user by the username and password
the user enters on a portal authentication page. Typically, portal
authentication is deployed on the access layer and vital data entries.

In a portal-enabled network, users can
actively initiate portal authentication by visiting the authentication website
provided by the portal Web server. Or, they are redirected to the portal
authentication page for authentication when they visit other websites.

The device supports Portal 1.0, Portal 2.0,
and Portal 3.0.

### Advantages of portal authentication

Portal authentication has the following
advantages:

·     Allows users to perform authentication through a
Web browser without installing client software.

·     Provides ISPs with diversified management
choices and extended functions. For example, the ISPs can place advertisements,
provide community services, and publish information on the authentication page.

·     Supports multiple authentication modes. For
example, re-DHCP authentication implements a flexible address assignment scheme
and saves public IP addresses. Cross-subnet authentication can authenticate
users who reside in a different subnet than the access device.

### Extended portal functions

By forcing patching and anti-virus
policies, extended portal functions help hosts to defend against viruses. Portal
supports the following extended functions:

·     Security check—Detects after authentication whether or not a user host installs
anti-virus software, virus definition file, unauthorized software, and
operating system patches.

·     Resource access restriction—Allows an authenticated user to access certain network resources
such as the virus server and the patch server. Users can access more network resources
after passing security check.

Security check must cooperate with the IMC
security policy server and the iNode client.

### Portal system

A typical portal system consists of these basic
components: authentication client, access device, portal authentication server,
portal Web server, AAA server, and security policy server.

Figure 1 Portal
system

![](https://resource.h3c.com/en/202407/12/20240712_11705348_x_Img_x_png_0_2216065_294551_0.png)

‌

#### Authentication client

An authentication client is a Web browser that
runs HTTP/HTTPS or a user host that runs a portal client. Security check for
the user host is implemented through the interaction between the portal client
and the security policy server. Only the iNode client is supported.

#### Access device

An access device provides access services.
It has the following functions:

·     Redirects all HTTP or HTTPS requests of
unauthenticated users to the portal Web server.

·     Interacts with the portal authentication server
and the AAA server to complete authentication, authorization, and accounting.

·     Allows users that pass portal authentication to
access authorized network resources.

#### Portal server

A portal server collectively refers to a
portal authentication server and portal Web server.

The portal Web server pushes the Web
authentication page to authentication clients and forwards user authentication
information (username and password) to the portal authentication server. The
portal authentication server receives authentication requests from
authentication clients and interacts with the access device to authenticate
users. The portal Web server is typically integrated with the portal
authentication server and it can also be an independent server.

#### AAA server

The AAA server interacts with the access
device to implement authentication, authorization, accounting for portal users.
In a portal system, a RADIUS server can perform authentication, authorization,
accounting for portal users, and an LDAP server can perform authentication for
portal users.

#### Security policy server

The security policy server interacts with
the portal client and the access device for security check and authorization
for users. Only hosts that run portal clients can interact with the security
policy server.

### Portal authentication using a remote portal server

The components of a portal system interact as
follows:

**1\.**An unauthenticated user initiates
authentication by accessing an Internet website through a Web browser. When
receiving the HTTP or HTTPS request, the access device redirects it to the Web authentication
page provided by the portal Web server. The user can also visit the
authentication website to log in. The user must log in through the iNode client
for extended portal functions.

**2\.**The user enters the authentication
information on the authentication page/dialog box and submits the information. The
portal Web server forwards the information to the portal authentication server.
The portal authentication server processes the information and forwards it to
the access device.

**3\.**The access device interacts with the AAA
server to implement authentication, authorization, accounting for the user.

**4\.**If security policies are not imposed on the
user, the access device allows the authenticated user to access networks. 

If security policies are imposed on the
user, the portal client, the access device, and the security policy server interact
to check the user host. If the user passes the security check, the security
policy server authorizes the user to access resources based on the check
result.

### Local portal service

#### System components

As shown in [Figure 2](#_Ref459127174), a
local portal system consists of an authentication client, access device, and
AAA server. The access
device acts as both the portal Web server and the portal authentication server
to provide the local portal Web service for the authentication client. The
authentication client can only be a Web browser, and it cannot be a user host
that runs a portal client. Therefore, extended portal functions are not
supported and no security policy server is required.

Figure 2 System components

![](https://resource.h3c.com/en/202407/12/20240712_11705349_x_Img_x_png_1_2216065_294551_0.png)

‌

#### Portal page customization

The portal system pushes different
authentication pages to portal users at different stages. The local portal service
supports portal page customization. You can customize one or more sets of
authentication pages, compress each set of authentication pages to a .zip file,
and upload the compressed files to the storage medium of the device.

For more information about authentication
page customization, see "[Customizing authentication pages](#_Ref458784657)."

### Portal authentication modes

Portal authentication has three modes:
direct authentication, re-DHCP authentication, and cross-subnet authentication.
In direct authentication and re-DHCP authentication, no Layer 3 forwarding
devices exist between the authentication client and the access device. In
cross-subnet authentication, Layer 3 forwarding devices can exist between the
authentication client and the access device.

#### Direct authentication

A user manually configures a public IP
address or obtains a public IP address through DHCP. Before authentication, the
user can access only the portal Web server and predefined authentication-free
websites. After passing authentication, the user can access other network resources.
The process of direct authentication is simpler than that of re-DHCP
authentication.

#### Re-DHCP authentication

Before a user passes authentication, DHCP
allocates an IP address (a private IP address) to the user. The user can access
only the portal Web server and predefined authentication-free websites. After the
user passes authentication, DHCP reallocates an IP address (a public IP
address) to the user. The user then can access other network resources. No
public IP address is allocated to users who fail authentication. Re-DHCP
authentication saves public IP addresses. For example, an ISP can allocate
public IP addresses to broadband users only when they access networks beyond
the residential community network.

Only the iNode client supports re-DHCP
authentication. IPv6 portal authentication does not support the re-DHCP
authentication mode.

#### Cross-subnet authentication

Cross-subnet authentication is similar to
direct authentication, except it allows Layer 3 forwarding devices to exist
between the authentication client and the access device.

In direct authentication, re-DHCP
authentication, and cross-subnet authentication, a user's IP address uniquely
identifies the user. After a user passes authentication, the access device
generates an ACL for the user based on the user's IP address to control
forwarding of the packets from the user. Because no Layer 3 forwarding device exists
between authentication clients and the access device in direct authentication and
re-DHCP authentication, the access device can learn the user MAC addresses. The
access device can enhance its capability of controlling packet forwarding by
using the learned MAC addresses.

### Portal authentication process

Direct authentication and cross-subnet
authentication share the same authentication process. Re-DHCP authentication
has a different process as it has two address allocation procedures.

#### Direct authentication/cross-subnet authentication process (with CHAP/PAP authentication)

Figure 3 Direct authentication/cross-subnet authentication process

![](https://resource.h3c.com/en/202407/12/20240712_11705360_x_Img_x_png_2_2216065_294551_0.png)

‌

The direct/cross-subnet authentication
process is as follows:

**1\.**A portal user access the Internet through
HTTP or HTTPS, and the HTTP or HTTPS packet arrives at the access device.

¡     If
the packet matches a portal free rule, the access device allows the packet to
pass.

¡     If
the packet does not match any portal-free rule, the access device redirects the
packet to the portal Web server. The portal Web server pushes the Web
authentication page to the user for him to enter his username and password.

**2\.**The portal Web server submits the user
authentication information to the portal authentication server.

**3\.**The portal authentication server and the
access device exchange CHAP messages. This step is skipped for PAP
authentication. The portal authentication server decides the method (CHAP or
PAP) to use.

**4\.**The portal authentication server adds the
username and password into an authentication request packet and sends it to the
access device. Meanwhile, the portal authentication server starts a timer to
wait for an authentication reply packet.

**5\.**The access device and the RADIUS server
exchange RADIUS packets.

**6\.**The access device sends an authentication
reply packet to the portal authentication server to notify authentication
success or failure.

**7\.**The portal authentication server sends an
authentication success or failure packet to the client.

**8\.**If the authentication is successful, the
portal authentication server sends an authentication reply acknowledgment
packet to the access device.

If the client is an iNode client, the
authentication process includes step 9 and step 10 for extended portal
functions. Otherwise the authentication process is complete.

**9\.**The client and the security policy server exchange
security check information. The security policy server detects whether or not the
user host installs anti-virus software, virus definition files, unauthorized
software, and operating system patches.

**10\.**The security policy server authorizes the
user to access certain network resources based on the check result. The access
device saves the authorization information and uses it to control access of the
user.

#### Re-DHCP authentication process (with CHAP/PAP authentication)

Figure 4 Re-DHCP authentication process

![](https://resource.h3c.com/en/202407/12/20240712_11705371_x_Img_x_png_3_2216065_294551_0.png)

‌

The re-DHCP authentication process is as
follows:

Step 1 through step 7 are the same as those
in the direct authentication/cross-subnet authentication process.

**8\.**After receiving the authentication success
packet, the client obtains a public IP address through DHCP. The client then
notifies the portal authentication server that it has a public IP address.

**9\.**The portal authentication server notifies
the access device that the client has obtained a public IP address.

**10\.**The access device detects the IP change of
the client through DHCP and then notifies the portal authentication server that
it has detected an IP change of the client IP.

**11\.**After receiving the IP change notification
packets sent by the client and the access device, the portal authentication
server notifies the client of login success.

**12\.**The portal authentication server sends an IP
change acknowledgment packet to the access device.

Step 13 and step 14 are for extended portal
functions.

**13\.**The client and the security policy server
exchanges security check information. The security policy server detects
whether or not the user host installs anti-virus software, virus definition
files, unauthorized software, and operating system patches.

**14\.**The security policy server authorizes the
user to access certain network resources based on the check result. The access
device saves the authorization information and uses it to control access of the
user.

### Portal support for EAP

To use portal authentication that supports
EAP, the portal authentication server and client must be the IMC portal server
and the iNode portal client. Local portal authentication does not support EAP
authentication.

Compared with username and password based
authentication, digital certificate-based authentication ensures higher
security.

The Extensible Authentication Protocol
(EAP) supports several digital certificate-based authentication methods, for
example, EAP-TLS. Working together with EAP, portal authentication can
implement digital certificate-based user authentication. 

Figure 5 Portal support for EAP working flow
diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705377_x_Img_x_png_4_2216065_294551_0.png)

‌

As shown in [Figure 5](#_Ref458784786), the
authentication client and the portal authentication server exchange EAP
authentication packets. The portal authentication server and the access device
exchange portal authentication packets that carry the EAP-Message attributes.
The access device and the RADIUS server exchange RADIUS packets that carry the
EAP-Message attributes. The RADIUS server that supports the EAP server function
processes the EAP packets encapsulated in the EAP-Message attributes, and
provides the EAP authentication result.

The access device does not process but only
transports EAP-Message attributes between the portal authentication server and
the RADIUS server. Therefore, the access device requires no additional configuration
to support EAP authentication.

### Portal filtering rules

The access device uses portal filtering
rules to control user traffic forwarding.

Based on the configuration and
authentication status of portal users, the device generates the following
categories of portal filtering rules:

·     Category 1—The rule permits user packets that are destined for the portal Web
server and packets that match the portal-free rules to pass through.

·     Category 2—For an authenticated user with no ACL authorized, the rule allows
the user to access any destination network resources. For an authenticated user
with an ACL authorized, the rule allows users to access resources permitted by
the ACL. The device adds the rule when a user comes online and deletes the rule
when the user goes offline.

The device supports the following types
of authorization ACLs:

¡     Basic
ACLs (ACL 2000 to ACL 2999).

¡     Advanced
ACLs (ACL 3000 to ACL 3999).

¡     Layer
2 ACLs (ACL 4000 to ACL 4999).

For an authorization ACL to take effect,
make sure the ACL exists and has ACL rules excluding rules configured with the counting, established, fragment, source-mac, source-ip, or logging keyword. For
more information about ACL rules, see ACL commands in ACL
and QoS Command Reference. 

·     Category 3—The rule redirects all HTTP or HTTPS requests from unauthenticated
users to the portal Web server.

·     Category 4—For direct authentication and cross-subnet authentication, the rule
forbids any user packets to pass through. For re-DHCP authentication, the
device forbids user packets with private source addresses to pass.

After receiving a user packet, the device
compares the packet against the filtering rules from category 1 to category 4\.
Once the packet matches a rule, the matching process completes.

### Portal support for M-LAG

Portal authentication supports M-LAG to
improve high availability of the access device.

As shown in [Figure 6](#_Ref66719177), Device
A and Device B form an M-LAG system. The M-LAG system acts as an access device
to provide redundancy and load sharing. For more information about M-LAG, see Layer 2—LAN Switching Configuration Guide.

In an M-LAG system, after portal users pass
authentication on an M-LAG member device, the M-LAG member device will
synchronize data of the users to the peer member device for backup. When the M-LAG
member device fails, the peer member device can use the backup user data to process
services for the portal users. This ensures user service continuity.

Figure 6 Support of portal forM-LAG

![](https://resource.h3c.com/en/202407/12/20240712_11705378_x_Img_x_png_5_2216065_294551_0.png)

‌

## Restrictions and guidelines: Portal configuration

In portal authentication, the device can redirect
users' HTTPS requests to the portal Web server. You can use one of the
following local certificates for the HTTPS redirect service according to the
security requirements and the configuration complexity:

·     Self-signed certificate—Using this type of certificate is simple in configuration but has
low security. You do not need to associate an SSL server policy with the HTTPS
redirect service and the default SSL parameters are used. However, a self-signed
certificate is not trusted by the browser. When the device redirects HTTPS requests
to the portal Web server, a certificate security warning prompt might appear on
the browser. If you accept the security risks stated in the prompt, you can
ignore the prompt to browse the page.

·     CA-signed certificate—Using this type of certificate is complex in configuration but has
high security. You must obtain a CA certificate, request a local certificate
from the CA, create an SSL server policy (with policy name of https\_redirect), and associate the SSL server policy with
the HTTPS redirect service.

For more information about digital
certificates, see "Configuring PKI". For more information about the
SSL server policy configuration, see "Configuring SSL".

Portal authentication through Web does not
support security check for users. To implement security check, the client must
be the iNode client.

Portal authentication supports NAT
traversal whether it is initiated by a Web client or an iNode client. NAT
traversal must be configured when the portal client is on a private network and
the portal server is on a public network.

## Portal authentication tasks at a glance

To configure portal authentication, perform
the following tasks:

**1\.**Configuring a remote portal service

Perform this task if the access device
does not act as a portal authentication server or portal Web server.

¡     [Configuring a remote portal authentication
server](#_Ref364249677)

¡     [Configuring a portal Web server](#_Ref364249684)

**2\.**Configuring a local portal service

Perform this task if the access device
acts as a portal authentication server and portal Web server.

¡     [Configuring local portal service features](#_Ref476322981)

¡     [Configuring a portal Web server](#_Ref364249684)

**3\.**Enabling portal authentication and specifying
a portal Web server

¡     [Enabling portal authentication on an
interface](#_Ref476211320)

¡     [Specifying a portal Web server on an
interface](#_Ref476211330)

¡     [Enabling portal to support IPv4/IPv6 dual
stack](#_Ref10536851)

Perform this task if dual-stack portal
users exist on the network.

**4\.**(Optional.) [Specifying a preauthentication IP address pool](#_Ref476211412)

**5\.**(Optional.) [Specifying a portal authentication domain](#_Ref476211438)

**6\.**(Optional.) [Controlling portal user access](#_Ref476211452)

¡     [Configuring a portal-free rule](#_Ref144279510)

¡     [Configuring an authentication source subnet](#_Ref144279521)

¡     [Configuring an authentication destination
subnet](#_Ref484078554)

¡     [Configuring support of Web proxy for portal
authentication](#_Ref465767231)

¡     [Checking the issuing of category-2 portal
filtering rules](#_Ref505333230)

¡     [Setting the maximum number of portal users](#_Ref227407576)

¡     [Enabling strict-checking on portal
authorization information](#_Ref383699426)

¡     [Allowing only users with DHCP-assigned IP
addresses to pass portal authentication](#_Ref420395001)

¡     [Enabling portal roaming](#_Ref458785236)

¡     [Configuring the portal fail-permit feature](#_Ref459145322)

**7\.**(Optional.) [Configuring portal detection features](#_Ref459145734)

¡     [Configuring online detection of portal
users](#_Ref459145819)

¡     [Configuring portal authentication server
detection](#_Ref249273649)

¡     [Configuring portal Web server detection](#_Ref344294833)

¡     [Configuring portal user synchronization](#_Ref476211590)

**8\.**(Optional.) Configuring attributes for
portal packets and RADIUS packets

¡     [Configuring portal packet attributes](#_Ref458785304)

You can configure the BAS-IP or BAS-IPv6
attribute for portal packets and specify the device ID.

¡     [Configuring attributes for RADIUS packets](#_Ref458785343)

You can configure the NAS-Port-Id and
NAS-Port-Type attributes and apply a NAS-ID profile to an interface.

**9\.**(Optional.) [Configuring portal authentication to
support M-LAG](#_Ref114039072)

**10\.**(Optional.) Configuring online and offline related
features for portal users

¡     [Logging out online portal users](#_Ref384302826)

¡     [Enabling portal user login/logout logging](#_Ref458785415)

**11\.**(Optional.) Configuring extended portal
authentication features

¡     [Disabling the Rule ARP or ND entry feature
for portal clients](#_Ref476211721)

¡     [Configuring Web redirect](#_Ref458785419)

**12\.**[Obtaining user access information from ARP
or ND entries](#_Ref529458229)

This task is required in a remote Web
authentication network.

## Prerequisites for portal authentication

The portal feature provides a solution for
user identity authentication and security check. To complete user identity
authentication, portal must cooperate with RADIUS.

Before you configure portal, you must
complete the following tasks:

·     The portal authentication server, portal Web
server, and RADIUS server have been installed and configured correctly.

·     To use the re-DHCP portal authentication mode,
make sure the DHCP relay agent is enabled on the access device, and the DHCP
server is installed and configured correctly.

·     The portal client, access device, and servers
can reach each other.

·     To use the remote RADIUS server, configure
usernames and passwords on the RADIUS server, and configure the RADIUS client
on the access device. For information about RADIUS client configuration, see
"Configuring AAA."

·     To implement extended portal functions, install
and configure IMC EAD. Make sure the ACLs configured on the access device
correspond to the isolation ACL and the security ACL on the security policy
server. For installation and configuration about the security policy server,
see IMC EAD Security Policy Help.

## Configuring a remote portal authentication server

#### About this task

With portal authentication enabled, the
device searches for a portal authentication server for a received portal request
packet according to the source IP address and VPN information of the packet.

·     If a matching portal authentication server is
found, the device regards the packet valid and sends an authentication response
packet to the portal authentication server. After a user logs in to the device,
the user interacts with the portal authentication server as needed.

·     If no matching portal authentication server is
found, the device drops the packet.

#### Restrictions and guidelines

Do not delete a portal authentication
server in use. Otherwise, users authenticated by that server cannot log out
correctly.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Create a portal authentication server and
enter its view.

portal server server-name

You can create multiple portal
authentication servers.

**3\.**Specify the IP address of the portal
authentication server.

IPv4:

ip ipv4-address \[ vpn-instance vpn-instance-name
] \[ key { cipher \| simple } string ]

IPv6:

ipv6 ipv6-address \[ vpn-instance vpn-instance-name
] \[ key { cipher \| simple } string ]

**4\.**(Optional.) Set the destination UDP port
number used by the device to send unsolicited portal packets to the portal
authentication server.

port port-number

By default, the UDP port number is 50100\.

This port number must be the same as the
listening port number specified on the portal authentication server.

**5\.**(Optional.) Specify the portal
authentication server type.

server-type { cmcc \| imc }

By default, the portal authentication
server type is IMC.

The specified server type must be the
same as the type of the portal authentication server actually used.

**6\.**(Optional.) Configure the device to
periodically register with the portal authentication server.

server-register \[ interval interval-value
]

By default, the device does not register
with a portal authentication server.

## Configuring a portal Web server

### Portal Web server tasks at a glance

To configure a portal Web server, perform
the following tasks:

**1\.**[Configure basic parameters for a portal Web
server](#_Ref460321580)

**2\.**(Optional.) [Enabling the captive-bypass feature](#_Ref460321583)

**3\.**(Optional.) [Configuring a match rule for URL redirection](#_Ref460321587)

### Configure basic parameters for a portal Web server

**1\.**Enter system view.

system-view

**2\.**Create a portal Web server and enter its
view.

portal web-server server-name

You can create multiple portal Web
servers.

**3\.**Specify the VPN instance to which the portal
Web server belongs.

vpn-instance
vpn-instance-name

By default, the portal Web server belongs
to the public network.

**4\.**Specify the URL of the portal Web server.

url url-string

By default, no URL is specified for a
portal Web server.

**5\.**Configure the parameters to be carried in
the URL when the device redirects it to users.

url-parameter param-name { original-url \| source-address \| source-mac \[ format section { 1 \| 3 \| 6 } { lowercase \| uppercase } ] \[ encryption { aes \| des } key { cipher \| simple } string ] \| value expression }

By default, no redirection URL parameters
are configured.

**6\.**(Optional.) Specify the portal Web server
type.

server-type { cmcc \| imc \| ise \| oauth }

By default, the portal Web server type is
IMC.

This configuration is applicable to only to
the remote portal service.

The specified server type must be the
same as the type of the portal Web server actually used.

### Enabling the captive-bypass feature

#### About this task

By default, the device automatically pushes
the portal authentication page to iOS devices and some Android devices when
they are connected to the network. The captive-bypass feature enables the
device to push the portal authentication page to iOS devices and some Android
devices only when they access the Internet by using browsers.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter portal Web server view.

portal web-server server-name

**3\.**Enable the captive-pass feature.

captive-bypass enable

By default, the captive-bypass feature is
disabled.

### Configuring a match rule for URL redirection

#### About this task

A URL redirection match rule matches HTTP
or HTTPS requests by user-requested URL or User-Agent information, and
redirects the matching requests to the specified redirection URL. Therefore, URL
redirection match rules allow for more flexible URL redirection than the url command. The url command is only
used to redirect HTTP or HTTPS requests from unauthenticated users to the
portal Web server for authentication.

#### Restrictions and guidelines

For a user to successfully access a
redirection URL, configure a portal-free rule to allow HTTP or HTTPS requests
destined for the redirection URL to pass. For information about configuring
portal-free rules, see the portal free-rule
command.

If both the url and if-match commands are
executed, the if-match command takes
priority to perform URL redirection.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter portal Web server view.

portal web-server server-name

**3\.**Configure a match rule for URL redirection.

if-match { original-url url-string redirect-url url-string \[ url-param-encryption { aes \| des } key { cipher \| simple } string ] \| user-agent string redirect-url url-string }

## Configuring local portal service features

### About the local portal service

After a local portal service is configured,
the device acts as the portal Web server and portal authentication server to
perform portal authentication on users. The portal authentication page file is
saved in the root directory of the device.

### Restrictions and guidelines for configuring local portal service features

For an interface to use the local portal service, the URL of the
portal Web server specified for the interface must meet the following
requirements:

·     The IP address in the URL must be the IP address
of a Layer 3 interface (except 127.0.0.1) on the device, and the IP address
must be reachable to portal clients.

·     The URL must be ended with /portal/.
For example: http://1.1.1.1/portal/.

The device provides a default
authentication page file. To use customized authentication pages, you must edit
the authentication pages, compress them to a file, and then upload the file to
the device. On the device, specify the file as the default authentication page
file.

### Customizing authentication pages

#### About this task

Authentication pages are HTML files. Local
portal authentication requires the following authentication pages:

·     Logon page

·     Logon success page

·     Logon failure page

·     Online page

·     System busy page

·     Logoff success page

You must customize the authentication
pages, including the page elements that the authentication pages will use, for
example, back.jpg for authentication page Logon.htm.

Follow the authentication page
customization rules when you edit the authentication page files.

#### File name rules

The names of the main authentication page
files are fixed (see [Table 1](#_Ref459129540)). You
can define the names of the files other than the main authentication page
files. File names and directory names are case insensitive.

Table 1 Main authentication page file names

| Main authentication page | File name |
| --- | --- |
| Logon page | logon.htm || Logon success page | logonSuccess.htm || Logon failure page | logonFail.htm || Online page  Pushed after the user gets online for online notification | online.htm || System busy page  Pushed when the system is busy or the user is in the logon process | busy.htm || Logoff success page | logoffSuccess.htm |






‌

#### Page request rules

The local portal Web server supports only
Get and Post requests.

·     Get requests—Used to get the static files in the authentication pages and allow
no recursion. For example, if file Logon.htm
includes contents that perform Get action on file ca.htm,
file ca.htm cannot include any reference to file Logon.htm.

·     Post requests—Used when users submit username and password pairs, log in, and log
out.

#### Post request attribute rules

**1\.**Observe the following requirements when
editing a form of an authentication page:

¡     An
authentication page can have multiple forms, but there must be one and only one
form whose action is logon.cgi. Otherwise, user
information cannot be sent to the access device.

¡     The
username attribute is fixed as PtUser. The password
attribute is fixed as PtPwd.

¡     The
value of the PtButton attribute is either Logon or Logoff, which indicates
the action that the user requests.

¡     A
logon Post request must contain PtUser, PtPwd, and PtButton
attributes.

¡     A
logoff Post request must contain the PtButton
attribute.

**2\.**Authentication pages logon.htm
and logonFail.htm must contain the logon Post
request.

The following example shows part of the
script in page logon.htm.

\<form action\=logon.cgi
method \= post \>

\<p\>User name:\<input
type\="text" name \= "PtUser"
style\="width:160px;height:22px" maxlength\=64\>

\<p\>Password :\<input
type\="password" name \= "PtPwd"
style\="width:160px;height:22px" maxlength\=32\>

\<p\>\<input type\=SUBMIT
value\="Logon" name \= "PtButton"
style\="width:60px;"
onclick\="form.action\=form.action\+location.search;"\>

\</form\>

**3\.**Authentication pages logonSuccess.htm
and online.htm must contain the logoff Post
request.

The following example shows part of the
script in page online.htm.

\<form action\=logon.cgi
method \= post \>

\<p\>\<input type\=SUBMIT
value\="Logoff" name\="PtButton"
style\="width:60px;"\>

\</form\>

#### Page file compression and saving rules

You must compress the authentication pages
and their page elements into a standard zip file.

·     The name of a zip file can contain only letters,
numbers, and underscores.

·     The authentication pages must be placed in the
root directory of the zip file.

·     Zip files can be transferred to the device
through FTP or TFTP and must be saved in the root directory of the device.

Examples of zip files on the device:

\<Sysname\> dir

Directory of flash:

   1     -rw-      1405  Feb
28 2008 15:53:20   ssid1.zip

   0     -rw-      1405  Feb
28 2008 15:53:31   ssid2.zip

   2     -rw-      1405  Feb
28 2008 15:53:39   ssid3.zip

   3     -rw-      1405  Feb
28 2008 15:53:44   ssid4.zip

2540 KB total (1319 KB free)

#### Redirecting authenticated users to a specific webpage

To make the device automatically redirect authenticated
users to a specific webpage, do the following in logon.htm and
logonSuccess.htm:

**1\.**In logon.htm, set the target attribute of
Form to \_blank.

See the contents in gray:

    \<form method\=post
action\=logon.cgi target\="\_blank"\>

**2\.**Add the function for page loading pt\_init()
to LogonSuccess.htm.

See the contents in gray:

    \<html\>

    \<head\>

    \<title\>LogonSuccess\</title\>

    \<script
type\="text/javascript" language\="javascript"
src\="pt\_private.js"\>\</script\>

    \</head\>

    \<body onload\="pt\_init();"
onbeforeunload\="return pt\_unload();"\>

    ... ...

    \</body\>

\</html\>

### Configuring a local portal Webservice

#### Prerequisites

Before you configure an HTTPS-based local
portal Web service, you must complete the following tasks:

·     Configure a PKI policy, obtain the CA
certificate, and request a local certificate. For more information, see "Configuring
PKI."

·     Configure an SSL server policy, and specify the
PKI domain configured in the PKI policy. 

During SSL connection establishment, the
user browser might display a message that it cannot verify server identity by
certificate. For users to perform portal authentication without checking such a
message, configure an SSL server policy to request a client-trusted certificate
on the device. The name of the policy must be https\_redirect. For more information about
SSL server policy configuration, see "Configuring SSL."

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enable HTTP- or HTTPS-based local portal Web
service and enter its view.

portal local-web-server
{ http \| https ssl-server-policy
policy-name \[ tcp-port port-number ] }

**3\.**Specify the default authentication page file
for the local portal Web service.

default-logon-page filename

By default, the default authentication
page file for a local portal Web service is file defaultfile.zip.

**4\.**(Optional.) Configure the HTTP or HTTPS
listening TCP port for the local portal Web service.

tcp-port port-number

By default, the HTTP service listening
port number is 80 and the HTTPS service listening port number is the TCP port
number set by the portal local-web-server
command.

## Enabling portal authentication on an interface

#### Restrictions and guidelines

When you enable portal authentication on an
interface, follow these restrictions and guidelines:

·     Cross-subnet authentication mode (layer3) does not require Layer 3 forwarding devices between
the access device and the portal authentication clients. However, if a Layer 3
forwarding device exists between the authentication client and the access
device, you must use the cross-subnet portal authentication mode.

·     You can enable both IPv4 portal authentication
and IPv6 portal authentication on an interface.

When you configure re-DHCP portal
authentication on an interface, follow these restrictions and guidelines:

·     Make sure the interface has a valid IP address
before you enable re-DHCP portal authentication on the interface.

·     With re-DHCP portal authentication, configure
authorized ARP on the interface as a best practice to make sure only valid
users can access the network. With authorized ARP configured on the interface,
the interface learns ARP entries only from the users who have obtained a public
address from DHCP.

·     For successful re-DHCP portal authentication,
make sure the BAS-IP or BAS-IPv6 attribute value is the same as the device IP
address specified on the portal authentication server. To configure the
attribute, use the portal { bas-ip \| bas-ipv6 } command.

·     An IPv6 portal server does not support re-DHCP
portal authentication.

Portal authentication supports both HTTP
redirect and HTTPS redirect. The default HTTPS redirect listening port number on
the device is 6654\. To change the HTTPS redirect listening port number, see HTTP
redirect configuration in Layer 3—IP Services
Configuration Guide.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter Layer 3 interface view.

interface interface-type interface-number

**3\.**Enable portal authentication.

IPv4:

portal enable method { direct \| layer3 \| redhcp }

IPv6:

portal ipv6 enable method { direct \| layer3 }

By default, portal authentication on an
interface is disabled.

## Specifying a portal Web server on an interface

#### About this task

With a portal Web server specified on an interface,
the device redirects the HTTP requests of portal users on the interface to the
portal Web server. 

You can specify both an IPv4 portal Web
server and an IPv6 portal Web server on an interface.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter Layer 3 interface view.

interface interface-type interface-number

**3\.**Specify a portal Web server on the
interface.

portal \[ ipv6 ] apply
web-server server-name \[ fail-permit \| secondary ]

By default, no portal Web servers are
specified on an interface.

## Enabling portal to support IPv4/IPv6 dual stack

#### About this task

When portal is enabled to support IPv4/IPv6
dual stack, dual-stack portal users can access both IPv4 and IPv6 networks
after passing one type (IPv4 or IPv6) of portal authentication.

The following describes the mechanism of
how portal supports IPv4/IPv6 dual stack:

**1\.**A portal user passes portal authentication
of one IP stack (the first stack, IPv4 or IPv6). The user can access network
resources of this IP stack.

**2\.**The device records the user's MAC address
and IP address in the portal user entry for the user.

**3\.**Upon receiving a packet of the other IP
stack (the second stack, IPv6 or IPv4) from the user, the device compares the source
MAC address with that in the portal user entry for the user. 

¡     If
the MAC addresses are the same, the device allows the user to access network
resources of the second stack without reauthentication.

¡     If
the MAC addresses are different, the user needs to perform portal
authentication of the second stack.

#### Restrictions and guidelines

This configuration takes effect on an
interface only when both direct IPv4 portal authentication and direct IPv6
portal authentication are enabled on the interface.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter Layer 3 interface view.

interface interface-type interface-number

**3\.**Enable portal to support IPv4/IPv6 dual
stack.

portal dual-stack enable

By default, portal support for IPv4/IPv6
dual stack is disabled. Dual-stack portal users can access only one type (IPv4
or IPv6) of network after passing that type of portal authentication.

## Specifying a preauthentication IP address pool

#### About this task

You must specify a preauthentication IP
address pool on a portal-enabled interface in the following situation:

·     Portal users access the network through a
subinterface of the portal-enabled interface.

·     The subinterface does not have an IP address.

·     Portal users need to obtain IP addresses through
DHCP.

After a user connects to a portal-enabled
interface, the user uses an IP address for portal authentication according to
the following rules:

·     If the interface is configured with a
preauthentication IP address pool, the user uses the following IP address:

¡     If
the client is configured to obtain an IP address automatically through DHCP,
the user obtains an address from the specified IP address pool.

¡     If
the client is configured with a static IP address, the user uses the static IP
address.

·     If the interface has an IP address but no
preauthentication IP pool specified, the user uses the static IP address or the
IP address obtained from a DHCP server.

·     If the interface has no IP address or
preauthentication IP pool specified, the user cannot perform portal
authentication.

After the user passes portal
authentication, the AAA server authorizes an IP address pool for re-assigning an
IP address to the user. If no authorized IP address pool is deployed, the user
continues using the previous IP address.

#### Restrictions and guidelines

This configuration takes effect only when the
direct IPv4 portal authentication is enabled on the interface.

Make sure the specified IP address pool exists
and is complete. Otherwise, the user cannot obtain the IP address and cannot
perform portal authentication.

If the portal user does not perform
authentication or fails to pass authentication, the assigned IP address is
still retained.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter Layer 3 interface view.

interface interface-type interface-number

**3\.**Specify a preauthentication IP address pool
on the interface.

portal \[ ipv6 ] pre-auth ip-pool pool-name

By default, no preauthentication IP
address pool is specified on an interface.

## Specifying a portal authentication domain

### About portal authentication domains

An authentication domain defines a set of
authentication, authorization, and accounting policies. Each portal user
belongs to an authentication domain and is authenticated, authorized, and
accounted in the domain.

With an authentication domain specified on
an interface, the device uses the authentication domain for AAA of portal
users. This allows for flexible portal access control.

### Restrictions and guidelines for specifying a portal authentication domain

The device selects the authentication
domain for a portal user in this order:

**1\.**ISP domain specified for the interface.

**2\.**ISP domain carried in the username.

**3\.**System default ISP domain.

If the chosen domain does not exist on the
device, the device searches for the ISP domain configured to accommodate users
assigned to nonexistent domains. If no such ISP domain is configured, user
authentication fails. For information about ISP domains, see "Configuring
AAA."

For the authorization ACL in the
authentication domain, the following rules apply:

·     If the user traffic matches a rule in the ACL,
the device processes the traffic based on the permit or deny statement of the
rule.

·     If the user traffic does not match any rule in
the ACL, the device permits the traffic. To deny such traffic, configure the
last rule in the ACL to deny all packets by using the rule deny ip command.

·     If the ACL contains rules that specify a source
address, users might not be able to get online. Do not specify a source IPv4,
IPv6, or MAC address when you configure a rule in the ACL.

### Specifying a portal authentication domain on an interface

**1\.**Enter system view.

system-view

**2\.**Enter Layer 3 interface view.

interface interface-type interface-number

**3\.**Specify an portal authentication domain on
the interface.

portal \[ ipv6 ] domain domain-name

By default, no portal authentication domain
is specified on an interface.

You can specify both an IPv4 portal
authentication domain and an IPv6 portal authentication domain on an interface.

## Controlling portal user access

### Configuring a portal-free rule

#### About this task

A portal-free rule allows specified users
to access specified external websites without portal authentication. 

The matching items for a portal-free rule
include the host name, source/destination IP address, TCP/UDP port number, source
MAC address, access interface, and VLAN. Packets matching a portal-free rule
will not trigger portal authentication, so users sending the packets can
directly access the specified external websites.

#### Restrictions and guidelines for configuring a portal-free rule

If you specify both a VLAN and an
interface, the interface must belong to the VLAN. If the interface does not
belong to the VLAN, the portal-free rule does not take effect.

You cannot configure two or more
portal-free rules with the same filtering criteria. Otherwise, the system
prompts that the rule already exists.

Regardless of whether portal authentication
is enabled or not, you can only add or remove a portal-free rule. You cannot
modify it.

#### Configuring an IP-based portal-free rule

**1\.**Enter system view.

system-view

**2\.**Configure an IP-based portal-free rule.

IPv4:

portal free-rule rule-number { destination ip { ipv4-address
{ mask-length \| mask } \| any } \[ tcp tcp-port-number \| udp udp-port-number ] \| source ip { ipv4-address { mask-length \| mask } \| any } \[ tcp tcp-port-number \| udp udp-port-number ] } \* \[ interface interface-type
interface-number ]

IPv6:

portal free-rule rule-number { destination ipv6 { ipv6-address
prefix-length \| any } \[ tcp tcp-port-number \| udp udp-port-number ] \| source ipv6 { ipv6-address prefix-length \| any } \[ tcp tcp-port-number \| udp udp-port-number ] } \* \[ interface interface-type
interface-number ]

#### Configuring a source-based portal-free rule

**1\.**Enter system view.

system-view

**2\.**Configure a source-based portal-free rule.

portal free-rule rule-number source { interface interface-type interface-number \| mac mac-address \| vlan vlan-id } \*

The vlan vlan-id option takes effect only on portal users that access the network
through VLAN interfaces.

#### Configuring a destination-based portal-free rule

**1\.**Enter system view.

system-view

**2\.**Configure a destination-based portal-free
rule.

portal free-rule rule-number destination host-name

Before
you configure destination-based portal-free rules,
make sure a DNS server is deployed on the network.

### Configuring an authentication source subnet

#### About this task

By configuring authentication source subnets,
you specify that only HTTP or HTTPS packets from users on the authentication source
subnets can trigger portal authentication. HTTP or HTTPS packets from users not
in authentication source subnets will be discarded if the packets do not match
any portal-free rules.

#### Restrictions and guidelines

Authentication source subnets are used only
for cross-subnet authentication.

In direct or re-DHCP portal authentication
mode, a portal user and its access interface (portal-enabled) are on the same
subnet. It is not necessary to specify the subnet as the authentication source
subnet.

·     In direct mode, the access device regards the
authentication source subnet as any source IP address.

·     In re-DHCP mode, the access device regards the
authentication source subnet on an interface as the subnet to which the private
IP address of the interface belongs.

If both authentication source subnets and
destination subnets are configured on an interface, only the authentication
destination subnets take effect.

You can configure multiple authentication
source subnets. If the source subnets overlap, the subnet with the largest
address scope (with the smallest mask or prefix) takes effect.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter Layer 3 interface view.

interface interface-type interface-number

**3\.**Configure a portal authentication source
subnet.

IPv4:

portal layer3 source ipv4-network-address { mask-length \| mask }

IPv6:

portal ipv6 layer3 source ipv6-network-address
prefix-length.

By default, no portal authentication
source subnets are configured, and users from any subnets can trigger portal
authentication.

### Configuring an authentication destination subnet

#### About this task

After authentication destination subnets
are configured, the device performs portal authentication on portal users only
when they access the specified subnets (excluding the destination IP addresses
and subnets specified in portal-free rules). Users can access other subnets
without portal authentication.

#### Restrictions and guidelines

If both authentication source subnets and
destination subnets are configured on an interface, only the authentication
destination subnets take effect.

You can configure multiple authentication destination
subnets. If the destination subnets overlap, the subnet with the largest
address scope (with the smallest mask or prefix) takes effect.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter Layer 3 interface view.

interface interface-type interface-number

**3\.**Configure a portal authentication destination
subnet.

IPv4:

portal free-all except destination ipv4-network-address { mask-length \| mask }

IPv6:

portal ipv6 free-all except destination ipv6-network-address prefix-length

By default, no authentication destination
subnets are configured, and users accessing any subnets must pass portal
authentication.

### Configuring support of Web proxy for portal authentication

#### About this task

To allow HTTP requests proxied by a Web
proxy server to trigger portal authentication, specify the port number of the Web
proxy server on the device. If a Web proxy server port is not specified on the
device, HTTP requests proxied by the Web proxy server are dropped, and portal
authentication cannot be triggered.

#### Restrictions and guidelines

If a user's browser uses the Web Proxy
Auto-Discovery (WPAD) protocol to discover Web proxy servers, you must perform
the following tasks on the device:

·     Specify port numbers of the Web proxy servers.

·     Configure portal-free rules to allow user
packets destined for the WPAD server to pass without authentication.

If portal users enable Web proxy in their browsers,
the users must add the IP address of the portal authentication server as a
proxy exception in their browsers. Thus, HTTP packets that the users send to
the portal authentication server will not be sent to Web proxy servers.

You cannot specify Web proxy server port 443
on the device.

You can execute this command multiple times
to specify multiple port numbers of Web proxy servers.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Specify the port number of a Web proxy
server.

portal web-proxy port port-number

By default, no port numbers of Web proxy
servers are specified. Proxied HTTP requests are dropped.

### Checking the issuing of category-2 portal filtering rules

#### About this task

Category-2 portal filtering rules permit
authenticated users to access authorized network resources. By default, the
device allows an authenticated user to come online as long as a member device
has issued a category-2 portal filtering rule for the user. Users coming online
from global interfaces might fail to access network resources because some
member ports might not have category-2 rules for the users. To resolve this
issue, enable the device to check the issuing of category-2 portal filtering
rules. Then, the device allows users to come online only when all member
devices have issued category-2 portal filtering rules for the users.

As a best practice, perform this task when
portal authentication is enabled on a global interface.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enable the device to check the issuing of
category-2 portal filtering rules.

portal user-rule assign-check enable

By default, the device does not check the
issuing of category-2 portal filtering rules.

### Setting the maximum number of portal users

#### About this task

Perform this task to control the total
number of portal users in the system, and the maximum number of IPv4 or IPv6
portal users on an interface.

#### Restrictions and guidelines for setting the maximum number of portal users

Make sure the maximum combined number of
IPv4 and IPv6 portal users specified on all interfaces does not exceed the global
maximum number. Otherwise, the exceeding number of portal users will not be
able to log in to the device.

Make sure the sum of the maximum number of
portal users for IPv4 and IPv6 on an interface does not exceed the maximum
number of all portal users set on the interface. Otherwise, the exceeding number
of portal users cannot come online from the interface.

You can set the maximum number of portal
users in interface view or system view. The maximum number of portal users
allowed on an interface is the smallest value among the following items:

·     Maximum number of IPv4 or IPv6 portal users allowed
on the interface.

·     Maximum number of all portal users allowed on
the interface.

·     Global maximum number of portal users.

#### Setting the global maximum number of portal users

**1\.**Enter system view.

system-view

**2\.**Set the global maximum number of portal
users.

portal max-user max-number

By default, no limit is set on the global
number of portal users.

If you set the global maximum number
smaller than the number of current online portal users on the device, this
configuration still takes effect. The online users are not affected but the
system forbids new portal users to log in.

#### Setting the maximum number of portal users on an interface

**1\.**Enter system view.

system-view

**2\.**Enter Layer 3 interface view.

interface interface-type interface-number

**3\.**Set the maximum number of portal users.

portal { ipv4-max-user \| ipv6-max-user \| max-user } max-number

By default, no limit is set on the number
of portal users on an interface.

If you set the maximum number smaller
than the current number of portal users on an interface, this configuration
still takes effect. The online users are not affected but the system forbids
new portal users to log in from the interface.

### Enabling strict-checking on portal authorization information

#### About this task

The strict checking feature allows a portal
user to stay online only when the authorization information for the user is successfully
deployed.

#### Enabling strict checking on portal authentication information on an interface

**1\.**Enter system view.

system-view

**2\.**Enter Layer 3 interface view.

interface interface-type interface-number

**3\.**Enable strict checking on portal
authorization information.

portal authorization{ acl \| user-profile } strict-checking

 

| CAUTION | CAUTION:  ·     The device logs out a portal user if the authorized ACL or user profile does not exist on the device or fails to be deployed.  ·     You can enable strict checking on the authorized ACL, authorized user profile, or both. If you enable both ACL checking and user profile checking, the user will be logged out if either checking fails. |
| --- | --- |

‌

By default, strict checking on portal authorization
information is disabled on an interface. Portal users stay online even when the
authorized ACL or user profile does not exist or the device fails to deploy the
user profile.

### Allowing only users with DHCP-assigned IP addresses to pass portal authentication

#### About this task

This feature allows only users with
DHCP-assigned IP addresses to pass portal authentication. Use this feature to
ensure that only users with valid IP addresses can access the network.

#### Restrictions and guidelines

This feature takes effect only when the
device acts as both the access device and the DHCP server.

When portal authentication is enabled to
support IPv4/IPv6 dual stack, this feature takes effect on only one IP stack
(the first stack used by portal users for passing authentication. When the portal
users use the second stack to come line, this feature does not take effect on
the users.

Configuration of this feature does not
affect the online portal users.

#### Allowing only users with DHCP-assigned IP addresses to pass portal authentication on an interface

**1\.**Enter system view.

system-view

**2\.**Enter Layer 3 interface view.

interface interface-type interface-number

**3\.**Allow only users with DHCP-assigned IP
addresses to pass portal authentication.

portal \[ ipv6 ] user-dhcp-only

 

| CAUTION | CAUTION:  ·     Users with static IP addresses cannot pass portal authentication to come online.  ·     If this feature is configured, to ensure that IPv6 users can pass portal authentication, disable the temporary IPv6 address feature on terminal devices. Otherwise, IPv6 users will use temporary IPv6 addresses to access the IPv6 network and will fail portal authentication. |
| --- | --- |

‌

By default, both users with IP addresses
obtained through DHCP and users with static IP addresses can pass
authentication to come online.

### Enabling portal roaming

#### About this task

If portal roaming is enabled on a VLAN
interface, an online portal user can access resources from any Layer 2 port in
the VLAN without re-authentication.

If portal roaming is disabled, to access
external network resources from a Layer 2 port different from the current
access port in the VLAN, the user must do the following:

**1\.**Logs out from the current port.

**2\.**Re-authenticates on the new Layer 2 port.

#### Restrictions and guidelines

Portal roaming takes effect only on portal
users logging in from VLAN interfaces. It does not take effect on portal users
logging in from common Layer 3 interface.

You cannot enable portal roaming when
online portal users exist on the device.

For portal roaming to take effect, you must
disable the Rule ARP or ND entry feature by using the undo portal
refresh { arp \| nd } enable
command.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enable portal roaming.

portal roaming enable

By default, portal roaming is disabled.

### Configuring the portal fail-permit feature

#### About this task

Perform this task to configure the portal
fail-permit feature on an interface. When the access device detects that the
portal authentication server or portal Web server is unreachable, it allows
users on the interface to have network access without portal authentication.

If you enable fail-permit for both a portal
authentication server and a portal Web server on an interface, the interface does
the following:

·     Disables portal authentication when either
server is unreachable.

·     Resumes portal authentication when both servers
are reachable.

After portal authentication resumes,
unauthenticated users must pass portal authentication to access the network.
Users who have passed portal authentication before the fail-permit event can
continue accessing the network.

#### Restrictions and guidelines

When portal authentication is enabled to
support IPv4/IPv6 dual stack, this feature takes effect on only one IP stack
(the first stack used by portal users for passing authentication. When the
portal users use the second stack to come line, this feature does not take
effect on the users.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter Layer 3 interface view.

interface interface-type
interface-number

**3\.**Enable portal fail-permit for a portal
authentication server.

portal \[ ipv6 ] fail-permit server server-name

By default, portal fail-permit is
disabled for a portal authentication server.

**4\.**Enable portal fail-permit for portal Web
servers.

portal \[ ipv6 ] fail-permit web-server

By default, portal fail-permit is
disabled for portal Web servers.

## Configuring portal detection features

### Configuring online detection of portal users

#### About this task

Use the online detection feature to quickly
detect abnormal logouts of portal users. Configure ARP or ICMP detection for
IPv4 portal users. Configure ND or ICMPv6 detection for IPv6 portal users.

If the device receives no packets from a
portal user within the idle time, the device detects the user's online status
as follows:

·     ICMP or ICMPv6 detection—Sends ICMP or ICMPv6 requests to the user at configurable intervals
to detect the user status.

¡     If
the device receives a reply within the maximum number of detection attempts, it
considers that the user is online and stops sending detection packets. Then the
device resets the idle timer and repeats the detection process when the timer
expires.

¡     If
the device receives no reply after the maximum number of detection attempts,
the device logs out the user.

·     ARP or ND detection—Sends ARP or ND requests to the user and detects the ARP or ND
entry status of the user at configurable intervals.

¡     If
the ARP or ND entry of the user is refreshed within the maximum number of detection
attempts, the device considers that the user is online and stops detection. Then
the device resets the idle timer and repeats the detection process when the
timer expires.

¡     If
the ARP or ND entry of the user is not refreshed after the maximum number of
detection attempts, the device logs out the user.

#### Restrictions and guidelines

ARP detection and ND detection apply only to direct and re-DHCP
portal authentication. ICMP detection applies to all portal authentication
modes.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter Layer 3 interface view.

interface interface-type interface-number

**3\.**Configure online detection of portal users.

IPv4:

portal user-detect type
{ arp \| icmp } \[ retry retries ] \[ interval interval ] \[ idle time ]

IPv6:

portal ipv6 user-detect type { icmpv6 \| nd } \[ retry retries ] \[ interval interval ] \[ idle time ]

By default, online detection is disabled
for portal users on an interface.

### Configuring portal authentication server detection

#### About this task

During portal authentication, if the
communication between the access device and portal authentication server is
broken, new portal users are not able to log in. Online portal users are not
able to log out normally.

To address this problem, the access device
needs to be able to detect the reachability changes of the portal server
quickly and take corresponding actions to deal with the changes.

The portal authentication server detection feature
enables the device to periodically detect portal packets sent by a portal
authentication server to determine the reachability of the server. If the device
receives a portal packet within a detection timeout (timeout timeout) and the portal
packet is valid, the device considers the portal authentication server to be reachable.
Otherwise, the device considers the portal authentication server to be unreachable.

Portal packets include user login packets,
user logout packets, and heartbeat packets. Heartbeat packets are periodically
sent by a server. By detecting heartbeat packets, the device can detect the
server's actual status more quickly than by detecting other portal packets.

#### Restrictions and guidelines

The portal authentication server detection
feature takes effect only when the device has a portal-enabled interface.

Only the IMC portal authentication server
supports sending heartbeat packets. To test server reachability by detecting
heartbeat packets, you must enable the server heartbeat feature on the IMC
portal authentication server.

You can configure the device to take one or
more of the following actions when the server reachability status changes:

·     Sending a log message, which contains the name,
the current state, and the original state of the portal authentication server.

·     Enabling portal fail-permit. When the portal
authentication server is unreachable, the portal fail-permit feature on an
interface allows users on the interface to have network access. When the server
recovers, it resumes portal authentication on the interface. For more information,
see "[Configuring the portal fail-permit feature](#_Ref459145322)."

·     Make sure the detection timeout configured on
the device is greater than the server heartbeat interval configured on the
portal authentication server.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter portal authentication server view.

portal server server-name

**3\.**Configure portal authentication server
detection.

server-detect \[ timeout timeout ] log

By default, portal authentication server
detection is disabled.

### Configuring portal Web server detection

#### About this task

A portal authentication process cannot
complete if the communication between the access device and the portal Web
server is broken. To address this problem, you can enable portal Web server
detection on the access device.

With the portal Web server detection
feature, the access device simulates a Web access process to initiate a TCP
connection to the portal Web server. If the TCP connection can be established
successfully, the access device considers the detection successful, and the portal
Web server is reachable. Otherwise, it considers the detection to have failed. 

You can configure the following detection
parameters:

·     Detection interval—Interval at which the device detects the server reachability.

·     Maximum number of consecutive
failures—If the number of consecutive detection failures
reaches this value, the access device considers that the portal Web server is
unreachable.

You can configure the device to take one or
more of the following actions when the server reachability status changes:

·     Sending a log message, which contains the name,
the current state, and the original state of the portal Web server.

·     Enabling portal fail-permit. When the portal Web
server is unreachable, the portal fail-permit feature on an interface allows
users on the interface to have network access. When the server recovers, it
resumes portal authentication on the interface. For more information, see
"[Configuring the portal
fail-permit feature](#_Ref459145322)."

#### Restrictions and guidelines

The portal Web server detection feature
takes effect only when the URL of the portal Web server is specified and the
device has a portal-enabled interface.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter portal Web server view.

portal web-server server-name

**3\.**Configure portal Web server detection.

server-detect \[ interval interval ] \[ retry retries ]  log 

By default, portal Web server detection
is disabled.

### Configuring portal user synchronization

#### About this task

Once the access device loses communication
with a portal authentication server, the portal user information on the access
device and that on the portal authentication server might be inconsistent after
the communication resumes. To address this problem, the device provides the portal
user synchronization feature. This feature is implemented by sending and detecting
portal synchronization packets, as follows:

**1\.**The portal authentication server sends the
online user information to the access device in a synchronization packet at the
user heartbeat interval.

The user heartbeat interval is set on the
portal authentication server.

**2\.**Upon receiving the synchronization packet,
the access device compares the users carried in the packet with its own user
list and performs the following operations:

¡     If a
user contained in the packet does not exist on the access device, the access
device informs the portal authentication server to delete the user. The access
device starts the synchronization detection timer (timeout timeout) immediately when a user logs in.

¡     If the
user does not appear in any synchronization packet within a synchronization detection
interval, the access device considers the user does not exist on the portal authentication
server and logs the user out.

#### Restrictions and guidelines

Portal user synchronization requires a
portal authentication server to support the portal user heartbeat function.
Only the IMC portal authentication server supports the portal user heartbeat
function. To implement the portal user synchronization feature, you also need
to configure the user heartbeat function on the portal authentication server. Make
sure the user heartbeat interval configured on the portal authentication server
is not greater than the synchronization detection timeout configured on the
access device.

Deleting a portal authentication server on
the access device also deletes the user synchronization configuration for the
portal authentication server.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter portal authentication server view.

portal server server-name

**3\.**Configure portal user synchronization.

user-sync timeout timeout

By default, portal user synchronization
is disabled.

## Configuring portal packet attributes

### Configuring the BAS-IP or BAS-IPv6 attribute

#### About this task

If the device runs Portal 2.0, the unsolicited
packets sent to the portal authentication server must carry the BAS-IP
attribute. If the device runs Portal 3.0, the unsolicited packets sent to the
portal authentication server must carry the BAS-IP or BAS-IPv6 attribute.

After this attribute is configured, the
source IP address for unsolicited notification portal packets the device sends
to the portal authentication server is the configured BAS-IP or BAS-IPv6
address. If the attribute is not configured, the source IP address of the portal
packets is the IP address of the packet output interface.

#### Restrictions and guidelines

During a re-DHCP portal authentication or
mandatory user logout process, the device sends portal notification packets to
the portal authentication server. For the authentication or logout process to
complete, make sure the BAS-IP/BAS-IPv6 attribute is the same as the device IP
address specified on the portal authentication server.

You must configure the BAS-IP or BAS-IPv6
attribute on a portal authentication-enabled interface if the following
conditions are met:

·     The portal authentication server is an IMC
server.

·     The portal device IP address specified on the
portal authentication server is not the IP address of the portal packet output
interface.

#### Configuring the BAS-IP or BAS-IPv6 attribute on an interface

**1\.**Enter system view.

system-view

**2\.**Enter Layer 3 interface view.

interface interface-type interface-number

**3\.**Configure the BAS-IP or BAS-IPv6 attribute.

IPv4:

portal bas-ip ipv4-address

By default, the BAS-IP attribute of an
IPv4 portal reply packet is the source IPv4 address of the packet. The BAS-IP
attribute of an IPv4 portal notification packet is the IPv4 address of the
packet's output interface.

IPv6:

portal bas-ipv6 ipv6-address

By default, the BAS-IPv6 attribute of an
IPv6 portal reply packet is the source IPv6 address of the packet. The BAS-IPv6
attribute of an IPv6 portal notification packet is the IPv6 address of the
packet's output interface.

### Specifying the device ID

#### About this task

The portal authentication server uses
device IDs to identify the devices that send protocol packets to the portal
server.

#### Restrictions and guidelines

Make sure the configured device ID is
different than any other access devices communicating with the same portal
authentication server.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Specify the device ID.

portal device-id device-id

By default, a device is not configured
with a device ID.

## Configuring attributes for RADIUS packets

### Specifying a format for the NAS-Port-Id attribute

#### About this task

RADIUS servers from different vendors might
require different formats of the NAS-Port-Id attribute in the RADIUS packets.
You can specify the NAS-Port-Id attribute format as required.

The device supports predefined formats
(format 1, 2, 3, and 4). For more information about the formats, see portal
commands in Security Command Reference.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Specify the format for the NAS-Port-Id
attribute.

portal nas-port-id format { 1 \| 2 \| 3 \| 4 }

By default, the format for the NAS-Port-Id
attribute is format 2\.

### Configuring the NAS-Port-Type attribute

#### About this task

The NAS-Port-Type attribute in a RADIUS
request represents the type of a user's access interface.

The access device might not be able to
correctly obtain the type of users' access interfaces when multiple network
devices exist between the access device and the portal client. For the access
device to send the correct access interface type to the RADIUS server, perform
this task to configure the NAS-Port-Type attribute.

#### Restrictions and guidelines

This configuration takes effect only on
portal users that newly come online.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter Layer 3 interface view.

interface interface-type interface-number

**3\.**Configure the NAS-Port-Type attribute carried
in outgoing RADIUS requests on the interface.

portal nas-port-type { 802.11 \| adsl-cap \| adsl-dmt \| async \| cable \| ethernet \| g.3-fax \| hdlc \| idsl \|
isdn-async-v110 \| isdn-async-v120 \| isdn-sync \| piafs \| sdsl \| sync \| virtual \| wireless-other
\| x.25 \| x.75 \| xdsl }

By default, the NAS-Port-Type carried in
outgoing RADIUS requests is Ethernet (attribute value 15).

### Applying a NAS-ID profile to an interface

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

You can apply a NAS-ID profile to a
portal-enabled interface. If no NAS-ID profile is specified on the interface or
no matching NAS-ID is found in the specified profile, the device uses the device
name as the interface NAS-ID.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Create a NAS-ID profile and enter NAS-ID
profile view.

aaa nas-id profile profile-name

For more information about this command,
see Security Command Reference.

**3\.**Configure a NAS ID and VLAN binding in the
profile.

nas-id nas-identifier bind vlan vlan-id

For more information about this command,
see Security Command Reference. Portal access matches
only the inner VLAN ID of QinQ packets. For more information about QinQ, see Layer 2—LAN Switching Configuration Guide.

**4\.**Specify the NAS-ID profile on the interface.

**a.**Return to system view.

quit

**b.**Enter Layer 3 interface view.

interface interface-type interface-number

**c.**Specify the NAS-ID profile on the interface.

portal nas-id-profile profile-name

By default, no NAS-ID profile is specified
for an interface.

## Configuring portal authentication to supportM-LAG

### Prerequisites

Before you configure portal authentication
to support M-LAG, you must complete the following tasks on both the primary and
secondary M-LAG member devices:

·     Enable portal authentication.

·     Configure IPv4 portal authentication or IPv6
portal authentication.

·     Set the portal authentication mode.

·     Enable portal fail-permit for a portal
authentication server.

·     Configure the URL of the portal Web server.

·     Configure portal user roaming.

·     Enable or disable portal authentication to
support IPv4/IPv6 dual stack.

Make sure the above configuration on both
the primary and secondary M-LAG member devices are the same.

### Setting the authentication load sharing mode for portal users on M-LAG interfaces

#### About this task

By default, the primary M-LAG member device
authenticates portal users on all M-LAG interfaces in an M-LAG system. If a
large number of portal users exist, perform this task to set the authentication
load sharing mode to improve authentication efficiency. 

#### Restrictions and guidelines

Do not set the same distribution criterion
for the distributed authentication load sharing mode on both the M-LAG member
devices. A violation might cause that portal users fail to come online.

If online portal users already exist on the
M-LAG network, do not change the authentication load sharing mode.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Set the authentication load sharing mode for
portal users on M-LAG interfaces.

portal m-lag load-sharing-mode { centralized \| distributed { even-ip \| odd-ip } }

By default, the authentication load
sharing mode is centralized for portal users on M-LAG interfaces.

### Configure traffic backup for portal users on M-LAG interfaces

#### About this task

In an M-LAG system, the M-LAG member
devices synchronize traffic of portal users with each other. A M-LAG member
device can periodically back up traffic of portal users to the peer device at
the specified interval. It can also back up portal user traffic to the peer
device if the amount of incremental traffic it has received since the last
backup reaches the backup threshold.

Perform this task to decrease the frequency
of traffic backup among M-LAG member devices.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Set the traffic backup interval and
threshold for portal users on M-LAG interfaces.

portal m-lag traffic backup { interval interval-value \| threshold threshold-value } \*

By default, the traffic backup interval
is 10 minutes and the  traffic backup threshold is 50 MB for portal users on M-LAG
interfaces.

## Logging out online portal users

#### About this task

This feature deletes users that have passed
portal authentication and terminates ongoing portal authentications.

#### Restrictions and guidelines

When the number of online users exceeds
2000, executing the portal delete-user
command takes a few minutes. 

To ensure successful logout of online
users, do not disable portal authentication or perform master/subordinate
device switchover on the portal-enabled interface during the command execution.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Log out online portal users.

portal delete-user { ipv4-address \| all \| auth-type { cloud \| local \| normal } \| interface interface-type interface-number \| ipv6 ipv6-address \| mac mac-address }

## Enabling portal user login/logout logging

#### About this task

This feature logs information about user
login and logout events. The information includes the username, user IP address
and MAC address, user access interface, VLAN, and login result. The logs are
sent to the information center of the device. For the logs to be output
correctly, you must also configure the information center on the device. For
more information about information center configuration, see Network Management and Monitoring Configuration Guide.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enable portal user login/logout logging.

portal log enable

By default, portal user login/logout logging
is disabled.

## Disabling the Rule ARP or ND entryfeaturefor portal clients

#### About this task

When the Rule ARP or ND entry feature is
enabled for portal clients, ARP or ND entries for portal clients are Rule
entries after the clients come online. After the portal clients go offline, the
Rule ARP or ND entries are converted into dynamic entries and deleted when they
age out.

#### Restrictions and guidelines

Enabling or disabling of this feature does
not affect existing Rule/dynamic ARP or ND entries.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Disable the Rule ARP or ND entry feature for
portal clients.

undo portal refresh { arp \| nd } enable

By default, the Rule ARP or ND entry feature
is enabled for portal clients.

## Configuring Web redirect

#### About this task

Web redirect is a simplified portal
feature. With Web redirect, a user does not perform portal authentication but
is directly redirected to the specified URL on the first Web access attempt in a
browser. After the specified redirect interval, the user is redirected from the
visiting website to the specified URL again.

Web redirect can provide ISPs with extended
services. For example, the ISPs can place advertisements and publish
information on the redirected webpage.

#### Restrictions and guidelines

The Web redirect feature takes effect only
on HTTP packets that use the default port number 80\.

If the device supports Etherchannel
interfaces, you can enable both Web redirect and portal authentication on
supporting interfaces. If the device does not support Etherchannel interfaces,
Web redirect does not work when both Web redirect and portal authentication are
enabled on an interface.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter Layer 3 interface view.

interface interface-type interface-number

**3\.**Configure Web redirect.

web-redirect \[ ipv6 ] url url-string \[ interval interval ]

By default, Web redirect is disabled.

## Setting the user synchronization interval for portal authentication using OAuth

#### About this task

If portal authentication uses OAuth, the
device periodically reports user information to the portal authentication
server for user synchronization on the server. To disable user synchronization
from the device to the portal authentication server, set the user
synchronization interval to 0 seconds on the device.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Set the user synchronization interval for
portal authentication using OAuth.

portal oauth user-sync interval interval

By default, the user synchronization
interval is 60 seconds.

## Obtaining user access information from ARP or ND entries

#### About this task

In a remote Web authentication network, the
device cannot obtain user access information from FIB entries upon receiving
portal packets from the portal authentication server. This will cause users to
fail Web authentication.

To resolve this issue, you can perform this
task on the device. When this feature is enabled, the device first attempts to
obtain user access information from ARP or ND entries during Web authentication.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Configure the device to obtain user
information from ARP or ND entries.

portal access-info trust { arp \| nd }

By default, the device obtains user
information from FIB entries.

## Display and maintenance commands for portal

Execute display
commands in any view and the reset
command in user view.

 

| Task | Command |
| --- | --- |
| Display portal configuration and portal running state. | display portal interface interface-type interface-number || Display packet statistics for portal authentication servers. | display portal packet statistics \[ extend-auth-server cloud \| server server-name ] || Display portal rules. | display portal rule { all \| dynamic \| static } interface interface-type interface-number \[ slot slot-number ] || Display portal authentication server information. | display portal server \[ server-name ] || Display session information for portal users or portal-based Web authentication users. | display portal session user-type { portal \| web-auth } || Display portal user information. | display portal user { all \| auth-type { cloud \| local \| normal } \| interface interface-type interface-number \| ip ipv4-address \| ipv6 ipv6-address \| mac mac-address \| pre-auth \[ interface interface-type interface-number \| ip ipv4-address \| ipv6 ipv6-address ] \| username username } \[ brief \| verbose ] || Display portal Web server information. | display portal web-server \[ server-name ] || Display Web redirect rule information. | display web-redirect rule interface interface-type interface-number \[ slot slot-number ] || Clear packet statistics for portal authentication servers. | reset portal packet statistics \[ extend-auth-server cloud \| server server-name ] |









‌

## Portal configuration examples

### Example: Configuring direct portal authentication

#### Network configuration

As shown in [Figure 7](#_Ref304385756), the
host is directly connected to the switch (the access device). The host is
assigned a public IP address either manually or through DHCP. An IMC server acts
as both a portal authentication server and a portal Web server. A RADIUS server
acts as the authentication and accounting server. In this example, the IMC server
runs IMC 7.1 (E0303) and IMC UAM 7.1 (E0304).

Configure direct portal authentication, so
the host can access only the portal server before passing the authentication
and access other network resources after passing the authentication.

Figure 7 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705381_x_Img_x_png_8_2216065_294551_0.png)

‌

#### Prerequisites

·     Configure IP addresses for the host, switch, and
servers as shown in [Figure 7](#_Ref304385756) and make
sure they can reach each other.

·     Configure the RADIUS server correctly to provide
authentication and accounting functions.

#### Configuring the portal server

**1\.**Configure the portal server:

**a.**Log in to IMC and click the User tab.

**b.**Select User Access Policy
\> Portal Service \> Server
from the navigation tree to open the portal server configuration page, as shown
in [Figure 8](#_Ref295232530).

**c.**Configure the portal server parameters as
needed.

This example uses the default settings.

**d.**Click OK.

Figure 8 Portal
server configuration

![](https://resource.h3c.com/en/202407/12/20240712_11705382_x_Img_x_png_9_2216065_294551_0.png)

‌

**2\.**Configure the IP address group:

**a.**Select User Access Policy
\> Portal Service \> IP
Group from the navigation tree to open the portal IP address group
configuration page.

**b.**Click Add to open
the page as shown in [Figure 9](#_Ref295232531). 

**c.**Enter the IP group name.

**d.**Enter the start IP address and end IP
address of the IP group. 

Make sure the host IP address is in the
IP group.

**e.**Select a service group.

This example uses the default group Ungrouped.

**f.**Select Normal
from the Action list.

**g.**Click OK.

Figure 9 Adding
an IP address group

![](https://resource.h3c.com/en/202407/12/20240712_11705350_x_Img_x_png_10_2216065_294551_0.png)

‌

**3\.**Add a portal device:

**a.**Select User Access Policy
\> Portal Service \> Device
from the navigation tree to open the portal device configuration page.

**b.**Click Add to open
the page as shown in [Figure 10](#_Ref295232533).

**c.**Enter device name NAS.

**d.**Enter the IP address of the switch's
interface connected to the host.

**e.**Set whether to support the portal server
heartbeat and user heartbeat functions.

In this example, select No for both Support Server Heartbeat and Support User
Heartbeat.

**f.**Enter the key, which must be the same as
that configured on the switch.

**g.**Select Directly
Connected from the Access Method list.

**h.**Use the default settings for other parameters.

**i.**Click OK.

Figure 10 Adding
a portal device

![](https://resource.h3c.com/en/202407/12/20240712_11705351_x_Img_x_png_11_2216065_294551_0.png)

‌

**4\.**Associate the portal device with the IP
address group:

**a.**As shown in [Figure 11](#_Ref363808237),
click the Port Group Information Management icon ![](https://resource.h3c.com/en/202407/12/20240712_11705352_x_Img_x_png_12_2216065_294551_0.png) for
device NAS to open the port group configuration
page. 

**b.**Click Add to open the page as shown in[Figure 12](#_Ref295232535).

**c.**Enter the port group name.

**d.**Select the configured IP address group. 

The IP address used by the user to access
the network must be within this IP address group.

**e.**Use the default settings for other
parameters.

**f.**Click OK.

Figure 11 Device
list

![](https://resource.h3c.com/en/202407/12/20240712_11705353_x_Img_x_png_13_2216065_294551_0.png)

‌

Figure 12 Adding
a port group

![](https://resource.h3c.com/en/202407/12/20240712_11705354_x_Img_x_png_14_2216065_294551_0.png)

‌

#### Configuring the switch

**1\.**Configure a RADIUS scheme:

\# Create a RADIUS scheme named rs1 and enter its
view.

\<Switch\> system-view

\[Switch] radius scheme rs1

\# Specify the primary authentication
server and primary accounting server, and configure the keys for communication
with the servers.

\[Switch-radius-rs1] primary
authentication 192.168.0.112

\[Switch-radius-rs1] primary
accounting 192.168.0.112

\[Switch-radius-rs1] key
authentication simple radius

\[Switch-radius-rs1] key
accounting simple radius

\# Exclude the ISP domain name from the
username sent to the RADIUS server.

\[Switch-radius-rs1] user-name-format
without-domain

\[Switch-radius-rs1] quit

\# Enable RADIUS session control.

\[Switch] radius
session-control enable

**2\.**Configure an authentication domain:

\# Create an ISP domain named dm1 and enter
its view.

\[Switch] domain dm1

\# Configure AAA methods for the ISP
domain.

\[Switch-isp-dm1] authentication
portal radius-scheme rs1

\[Switch-isp-dm1] authorization
portal radius-scheme rs1

\[Switch-isp-dm1] accounting
portal radius-scheme rs1

\[Switch-isp-dm1] quit

\# Configure domain dm1 as the
default ISP domain. If a user enters the username without the ISP domain name at
login, the authentication and accounting methods of the default domain are used
for the user.

\[Switch] domain default enable
dm1

**3\.**Configure portal authentication:

\# Configure a portal authentication
server.

\[Switch] portal server newpt

\[Switch-portal-server-newpt] ip
192.168.0.111 key simple portal

\[Switch-portal-server-newpt] port
50100

\[Switch-portal-server-newpt]
quit

\# Configure a portal Web server.

\[Switch] portal web-server
newpt

\[Switch-portal-websvr-newpt] url
http://192.168.0.111:8080/portal

\[Switch-portal-websvr-newpt]
quit

\# Enable direct portal authentication on
VLAN-interface 100\.

\[Switch] interface
vlan-interface 100

\[Switch–Vlan-interface100]
portal enable method direct

\# Specify portal Web server newpt on
VLAN-interface 100\.

\[Switch–Vlan-interface100] portal
apply web-server newpt

\# Configure the BAS-IP as 2.2.2.1 for
portal packets sent from VLAN-interface 100 to the portal authentication
server.

\[Switch–Vlan-interface100] portal
bas-ip 2.2.2.1

\[Switch–Vlan-interface100]
quit

#### Verifying the configuration

\# Verify that the portal configuration has
taken effect.

\[Switch] display portal interface vlan-interface
100

 Portal information of Vlan-interface100

     NAS-ID profile: Not configured

     Authorization : Strict checking 

     ACL           : Disabled

     User profile  : Disabled

     Dual stack    : Disabled

     Max users     : Not configured

 IPv4:

     Portal status: Enabled

     Portal authentication method: Direct

     Portal web server: newpt(active)

     Secondary portal Web server: Not
configured

     Portal mac-trigger-server: Not
configured

     Authentication domain: Not
configured

     User-dhcp-only: Disabled

     Pre-auth IP pool: Not configured

     Max users: Not configured

     Bas-ip: 2.2.2.1

     User detection:  Not configured

     Action for server detection:

         Server type    Server
name                        Action 

         \--            
\--                                 \-- 

     Layer3 source network:

         IP address               Mask

 

     Destination authenticate subnet:

         IP address              
Mask

IPv6:

     Portal status: Disabled

     Portal authentication method:
Disabled

     Portal web server: Not
configured

     Secondary portal Web server: Not
configured

     Portal mac-trigger-server: Not
configured

     Authentication domain: Not
configured

     User-dhcp-only: Disabled

     Pre-auth IP pool: Not configured

     Max users: Not configured

     Bas-ipv6: Not configured

     User detection: Not configured

     Action for server detection:

         Server type    Server
name                        Action

         \--            
\--                                 \--

     Layer3 source network:

         IP
address                                        Prefix length

 

     Destination authenticate subnet:


         IP
address                                        Prefix length

A user can perform portal authentication by
using the iNode client or through a Web browser. Before passing the
authentication, the user can access only the authentication page http://192.168.0.111:8080/portal. All Web requests from
the user will be redirected to the authentication page. After passing the
authentication, the user can access other network resources.

\# After the user passes authentication, use
the following command to display information about the portal user.

\[Switch] display portal user
interface vlan-interface 100

 Total portal users: 1

  Username: abc

  Portal server: newpt

  State: Online

  VPN instance: N/A

  MAC                IP                 VLAN  
Interface

  0015-e9a6-7cfe     2.2.2.2          
 100    Vlan-interface100

  Authorization information:

    DHCP IP pool: N/A

    User profile: N/A

    Session group profile: N/A

    ACL number: N/A

    Inbound CAR: N/A

    Outbound CAR: N/A

### Example: Configuring re-DHCP portal authentication

#### Network configuration

As shown in [Figure 13](#_Ref363914363),
the host is directly connected to the switch (the access device). The host
obtains an IP address through the DHCP server. A portal server acts as both a
portal authentication server and a portal Web server. A RADIUS server acts as
the authentication and accounting server.

Configure re-DHCP portal authentication.
Before passing the authentication, the host is assigned a private IP address.
After passing the authentication, the host gets a public IP address and can
access network resources.

Figure 13 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705355_x_Img_x_png_15_2216065_294551_0.png)

‌

#### Restrictions and guidelines

·     For re-DHCP portal authentication, configure a
public address pool (20.20.20.0/24) and a private address pool (10.0.0.0/24) on
the DHCP server. (Details not shown.)

·     For re-DHCP portal authentication:

¡     The switch
must be configured as a DHCP relay agent.

¡     The
portal-enabled interface must be configured with a primary IP address (a public
IP address) and a secondary IP address (a private IP address).

For information about DHCP relay agent
configuration, see Layer 3—IP Services Configuration
Guide.

·     Make sure the IP address of the portal device
added on the portal server is the public IP address (20.20.20.1) of the
switch's interface connecting the host. The private IP address range for the IP
address group associated with the portal device is the private subnet 10.0.0.0/24
where the host resides. The public IP address range for the IP address group is
the public subnet 20.20.20.0/24.

#### Prerequisites

·     Configure IP addresses for the switch and
servers as shown in [Figure 13](#_Ref363914363) and make
sure the host, switch, and servers can reach each other.

·     Configure the RADIUS server correctly to provide
authentication and accounting functions.

#### Procedure

**1\.**Configure a RADIUS scheme:

\# Create a RADIUS scheme named rs1 and enter
its view.

\<Switch\> system-view

\[Switch] radius scheme rs1

\# Specify the primary authentication
server and primary accounting server, and configure the keys for communication
with the servers.

\[Switch-radius-rs1] primary
authentication 192.168.0.113

\[Switch-radius-rs1] primary
accounting 192.168.0.113

\[Switch-radius-rs1] key
authentication simple radius

\[Switch-radius-rs1] key
accounting simple radius

\# Exclude the ISP domain name from the
username sent to the RADIUS server.

\[Switch-radius-rs1] user-name-format
without-domain

\[Switch-radius-rs1] quit

\# Enable RADIUS session control.

\[Switch] radius
session-control enable

**2\.**Configure an authentication domain:

\# Create an ISP domain named dm1 and enter
its view.

\[Switch] domain dm1

\# Configure AAA methods for the ISP
domain.

\[Switch-isp-dm1] authentication
portal radius-scheme rs1

\[Switch-isp-dm1] authorization
portal radius-scheme rs1

\[Switch-isp-dm1] accounting
portal radius-scheme rs1

\[Switch-isp-dm1] quit

\# Configure domain dm1 as the
default ISP domain. If a user enters the username without the ISP domain name
at login, the authentication and accounting methods of the default domain are
used for the user.

\[Switch] domain default enable
dm1

**3\.**Configure DHCP relay and authorized ARP:

\# Configure DHCP relay.

\[Switch] dhcp enable

\[Switch] dhcp relay
client-information record

\[Switch] interface
vlan-interface 100

\[Switch–Vlan-interface100] ip
address 20.20.20.1 255.255.255.0

\[Switch–Vlan-interface100] ip
address 10.0.0.1 255.255.255.0 sub

\[Switch-Vlan-interface100]
dhcp select relay

\[Switch-Vlan-interface100]
dhcp relay server-address 192.168.0.112

\# Enable authorized ARP.

\[Switch-Vlan-interface100] arp
authorized enable

\[Switch-Vlan-interface100]
quit

**4\.**Configure portal authentication:

\# Configure a portal authentication
server.

\[Switch] portal server newpt

\[Switch-portal-server-newpt] ip
192.168.0.111 key simple portal

\[Switch-portal-server-newpt] port
50100

\[Switch-portal-server-newpt]
quit

\# Configure a portal Web server.

\[Switch] portal web-server
newpt

\[Switch-portal-websvr-newpt] url
http://192.168.0.111:8080/portal

\[Switch-portal-websvr-newpt]
quit

\# Enable re-DHCP portal authentication on
VLAN-interface 100\.

\[Switch] interface
vlan-interface 100

\[Switch–Vlan-interface100]
portal enable method redhcp

\# Specify portal Web server newpt on
VLAN-interface 100\.

\[Switch–Vlan-interface100] portal
apply web-server newpt

\# Configure the BAS-IP as 20.20.20.1 for
portal packets sent from VLAN-interface 100 to the portal authentication
server.

\[Switch–Vlan-interface100] portal
bas-ip 20.20.20.1

\[Switch–Vlan-interface100]
quit

#### Verifying the configuration

\# Verify that the portal configuration has
taken effect.

\[Switch] display portal interface vlan-interface
100

 Portal information of
Vlan-interface100

     NAS-ID profile: Not configured

     Authorization : Strict checking

     ACL           : Disabled

     User profile  : Disabled

     Dual stack    : Disabled

     Max users     : Not configured

 IPv4:

     Portal status: Enabled

     Portal authentication method:
Redhcp

     Portal web server: newpt(active)

     Secondary portal Web server: Not
configured

     Portal mac-trigger-server: Not
configured

     Authentication domain: Not
configured

     User-dhcp-only: Disabled

     Pre-auth IP pool: Not configured

     Max users: Not configured

     Bas-ip: 20.20.20.1

     User detection:  Not configured

     Action for server detection:

         Server type    Server
name                        Action 

         \--            
\--                                 \-- 

     Layer3 source network:

         IP address              
Mask

 

     Destination authenticate subnet:

         IP address              
Mask

IPv6:

     Portal status: Disabled

     Portal authentication method:
Disabled

     Portal web server: Not
configured

     Secondary portal Web server: Not
configured

     Portal mac-trigger-server: Not
configured

     Authentication domain: Not
configured

     User-dhcp-only: Disabled

     Pre-auth IP pool: Not configured

     Max users: Not configured

     Bas-ipv6: Not configured

     User detection: Not configured

     Action for server detection:

         Server type    Server
name                        Action

         \--            
\--                                 \--

     Layer3 source network:

         IP address                                        Prefix
length

 

     Destination authenticate subnet:


         IP
address                                        Prefix length 

Before passing the authentication, a user
that uses the iNode client can access only the authentication page http://192.168.0.111:8080/portal. All Web requests from
the user will be redirected to the authentication page. After passing the
authentication, the user can access other network resources.

\# After the user passes authentication, use
the following command to display information about the portal user.

\[Switch] display portal user
interface vlan-interface 100

Total portal users: 1

Username: abc

  Portal server: newpt

  State: Online

  VPN instance: N/A

  MAC                IP                 VLAN  
Interface

  0015-e9a6-7cfe     20.20.20.2        
100    Vlan-interface100

  Authorization information:

    DHCP IP pool: N/A

    User profile: N/A

    Session group profile: N/A

    ACL number: N/A

    Inbound CAR: N/A

    Outbound CAR: N/A

### Example: Configuring cross-subnet portal authentication

#### Network configuration

As shown in [Figure 14](#_Ref363911464), Switch
A supports portal authentication. The host accesses Switch A through Switch B.
A portal server acts as both a portal authentication server and a portal Web
server. A RADIUS server acts as the authentication and accounting server.

Configure Switch A for cross-subnet portal
authentication. Before passing the authentication, the host can access only the
portal Web server. After passing the authentication, the user can access other
network resources.

Figure 14 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705356_x_Img_x_png_16_2216065_294551_0.png)

‌

#### Restrictions and guidelines

Make sure the IP address of the portal
device added on the portal authentication server is the IP address (20.20.20.1)
of the switch's interface connecting the host. The IP address group associated
with the portal device is the subnet of the host (8.8.8.0/24).

#### Prerequisites

·     Configure IP addresses for the switch and
servers as shown in [Figure 14](#_Ref363911464) and make
sure the host, switch, and servers can reach each other.

·     Configure the RADIUS server correctly to provide
authentication and accounting functions.

#### Procedure

**1\.**Configure a RADIUS scheme:

\# Create a RADIUS scheme named rs1 and enter
its view.

\<SwitchA\> system-view

\[SwitchA] radius scheme rs1

\# Specify the primary authentication
server and primary accounting server, and configure the keys for communication with
the servers.

\[SwitchA-radius-rs1] primary
authentication 192.168.0.112

\[SwitchA-radius-rs1] primary
accounting 192.168.0.112

\[SwitchA-radius-rs1] key
authentication simple radius

\[SwitchA-radius-rs1] key
accounting simple radius

\# Exclude the ISP domain name from the
username sent to the RADIUS server.

\[SwitchA-radius-rs1] user-name-format
without-domain

\[SwitchA-radius-rs1] quit

\# Enable RADIUS session control.

\[SwitchA] radius
session-control enable

**2\.**Configure an authentication domain:

\# Create an ISP domain named dm1 and enter
its view.

\[SwitchA] domain dm1

\# Configure AAA methods for the ISP
domain.

\[SwitchA-isp-dm1] authentication
portal radius-scheme rs1

\[SwitchA-isp-dm1] authorization
portal radius-scheme rs1

\[SwitchA-isp-dm1] accounting
portal radius-scheme rs1

\[SwitchA-isp-dm1] quit

\# Configure domain dm1 as the
default ISP domain. If a user enters the username without the ISP domain name
at login, the authentication and accounting methods of the default domain are
used for the user.

\[SwitchA] domain default
enable dm1

**3\.**Configure portal authentication:

\# Configure a portal authentication
server.

\[SwitchA] portal server newpt

\[SwitchA-portal-server-newpt] ip
192.168.0.111 key simple portal

\[SwitchA-portal-server-newpt] port
50100

\[SwitchA-portal-server-newpt]
quit

\# Configure a portal Web server.

\[SwitchA] portal web-server
newpt

\[SwitchA-portal-websvr-newpt] url
http://192.168.0.111:8080/portal

\[SwitchA-portal-websvr-newpt]
quit

\# Enable cross-subnet portal
authentication on VLAN-interface 4\.

\[SwitchA] interface
vlan-interface 4

\[SwitchA–Vlan-interface4]
portal enable method layer3

\# Specify portal Web server newpt on
VLAN-interface 4\.

\[SwitchA–Vlan-interface4] portal
apply web-server newpt

\# Configure the BAS-IP as 20.20.20.1 for
portal packets sent from VLAN-interface 4 to the portal authentication server.

\[SwitchA–Vlan-interface4] portal
bas-ip 20.20.20.1

\[SwitchA–Vlan-interface4] quit

#### Verifying the configuration

\# Verify that the portal configuration has
taken effect.

\[SwitchA] display portal interface
vlan-interface 4

 Portal information of
Vlan-interface4

     NAS-ID profile: Not configured

     Authorization : Strict checking 

     ACL           : Disabled

     User profile  : Disabled

     Dual stack    : Disabled

     Max users     : Not configured

 IPv4:

     Portal status: Enabled

     Portal authentication method: Layer3

     Portal web server: newpt(active)

     Secondary portal Web server: Not
configured

     Portal mac-trigger-server: Not
configured

     Authentication domain: Not
configured

     User-dhcp-only: Disabled

     Pre-auth IP pool: Not configured

     Max users: Not configured

     Bas-ip: 20.20.20.1

     User detection:  Not configured

     Action for server detection:

         Server type    Server
name                        Action 

         \--            
\--                                 \-- 

     Layer3 source network:

         IP address              
Mask

 

     Destination authenticate subnet:

         IP address              
Mask

IPv6:

     Portal status: Disabled

     Portal authentication method:
Disabled

     Portal web server: Not
configured(active)

     Secondary portal Web server: Not
configured

     Portal mac-trigger-server: Not configured

     Authentication domain: Not
configured

     User-dhcp-only: Disabled

     Pre-auth IP pool: Not configured

     Max users: Not configured

     Bas-ipv6: Not configured

     User detection: Not configured

     Action for server detection:

         Server type    Server
name                        Action

         \--            
\--                                 \--

     Layer3 source network:

         IP
address                                        Prefix length

 

     Destination authenticate subnet:


         IP
address                                        Prefix length 

A user can perform portal authentication by
using the iNode client or through a Web browser. Before passing the
authentication, the user can access only the authentication page http://192.168.0.111:8080/portal. All Web requests from
the user will be redirected to the authentication page. After passing the
authentication, the user can access other network resources.

\# After the user passes authentication, use
the following command to display information about the portal user.

\[SwitchA] display portal user
interface vlan-interface 4

Total portal users: 1

Username: abc

  Portal server: newpt

  State: Online

  VPN instance: N/A

  MAC                IP                 VLAN  
Interface

  0000-0000-0000     8.8.8.2          
 4      Vlan-interface4

  Authorization information:

    DHCP IP pool: N/A

    User profile: N/A

    Session group profile: N/A

    ACL number: N/A

    Inbound CAR: N/A

    Outbound CAR: N/A

### Example: Configuring extended direct portal authentication

#### Network configuration

As shown in [Figure 15](#_Ref363913641), the
host is directly connected to the switch (the access device). The host is
assigned a public IP address either manually or through DHCP. A portal server acts
as both a portal authentication server and a portal Web server. A RADIUS server
acts as the authentication and accounting server.

Configure extended direct portal authentication.
If the host fails security check after passing identity authentication, it can
access only subnet 192.168.0.0/24. After passing security check, the host can
access other network resources.

Figure 15 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705357_x_Img_x_png_17_2216065_294551_0.png)

‌

#### Prerequisites

·     Configure IP addresses for the host, switch, and
servers as shown in [Figure 15](#_Ref363913641) and make
sure they can reach each other.

·     Configure the RADIUS server correctly to provide
authentication and accounting functions.

#### Procedure

**1\.**Configure a RADIUS scheme:

\# Create a RADIUS scheme named rs1 and enter
its view.

\<Switch\> system-view

\[Switch] radius scheme rs1

\# Specify the primary authentication
server and primary accounting server, and configure the keys for communication
with the servers.

\[Switch-radius-rs1] primary
authentication 192.168.0.112

\[Switch-radius-rs1] primary
accounting 192.168.0.112

\[Switch-radius-rs1] key
accounting simple radius

\[Switch-radius-rs1] key
authentication simple radius

\[Switch-radius-rs1] user-name-format
without-domain

\# Enable RADIUS session control.

\[Switch] radius
session-control enable

\# Specify a
session-control client with IP address 192.168.0.112 and shared key 12345 in plaintext form.

\[Switch] radius
session-control client ip 192.168.0.112 key simple 12345

**2\.**Configure an authentication domain:

\# Create an ISP domain named dm1 and
enter its view.

\[Switch] domain dm1

\# Configure AAA methods for the ISP
domain.

\[Switch-isp-dm1] authentication
portal radius-scheme rs1

\[Switch-isp-dm1] authorization
portal radius-scheme rs1

\[Switch-isp-dm1] accounting
portal radius-scheme rs1

\[Switch-isp-dm1] quit

\# Configure domain dm1 as the default ISP
domain. If a user enters the username without the ISP domain name at login, the
authentication and accounting methods of the default domain are used for the
user.

\[Switch] domain default enable
dm1

**3\.**Configure ACL 3000 as the isolation ACL and
ACL 3001 as the security ACL.

\[Switch] acl advanced 3000

\[Switch-acl-ipv4-adv-3000]
rule permit ip destination 192.168.0.0 0.0.0.255

\[Switch-acl-ipv4-adv-3000]
rule deny ip

\[Switch-acl-ipv4-adv-3000]
quit

\[Switch] acl advanced 3001

\[Switch-acl-ipv4-adv-3001]
rule permit ip

\[Switch-acl-ipv4-adv-3001]
quit

 

|  | NOTE:  Make sure you specify ACL 3000 as the isolation ACL and ACL 3001 as the security ACL on the security policy server. |
| --- | --- |

‌

**4\.**Configure portal authentication:

\# Configure a portal authentication
server.

\[Switch] portal server newpt

\[Switch-portal-server-newpt] ip
192.168.0.111 key simple portal

\[Switch-portal-server-newpt] port
50100

\[Switch-portal-server-newpt]
quit

\# Configure a portal Web server.

\[Switch] portal web-server
newpt

\[Switch-portal-websvr-newpt] url
http://192.168.0.111:8080/portal

\[Switch-portal-websvr-newpt]
quit

\# Enable direct portal authentication on
VLAN-interface 100\.

\[Switch] interface
vlan-interface 100

\[Switch–Vlan-interface100]
portal enable method direct

\# Specify portal Web server newpt on
VLAN-interface 100\.

\[Switch–Vlan-interface100] portal
apply web-server newpt

\# Configure the BAS-IP as 2.2.2.1 for
portal packets sent from VLAN-interface 100 to the portal authentication
server.

\[Switch–Vlan-interface100] portal
bas-ip 2.2.2.1

\[Switch–Vlan-interface100]
quit

#### Verifying the configuration

\# Verify that the portal configuration has
taken effect.

\[Switch] display portal interface vlan-interface
100

 Portal information of
Vlan-interface100

     NAS-ID profile: Not configured

     Authorization : Strict checking 

     ACL           : Disabled

     User profile  : Disabled

     Dual stack    : Disabled

     Max users     : Not configured

 IPv4:

     Portal status: Enabled

     Portal authentication method: Direct

     Portal web server: newpt(active)

     Secondary portal Web server: Not
configured

     Portal mac-trigger-server: Not
configured

     Authentication domain: Not
configured

     User-dhcp-only: Disabled

     Pre-auth IP pool: Not configured

     Max users: Not configured

     Bas-ip: 2.2.2.1

     User detection:  Not configured

     Action for server detection:

         Server type    Server
name                        Action 

         \--            
\--                                 \-- 

     Layer3 source network:

         IP address              
Mask

 

     Destination authenticate subnet:

         IP address              
Mask

IPv6:

     Portal status: Disabled

     Portal authentication method:
Disabled

     Portal web server: Not
configured

     Secondary portal Web server: Not
configured

     Portal mac-trigger-server: Not
configured

     Authentication domain: Not
configured

     User-dhcp-only: Disabled

     Pre-auth IP pool: Not configured

     Max users: Not configured

     Bas-ipv6: Not configured

     User detection: Not configured

     Action for server detection:

         Server type    Server
name                        Action

         \--            
\--                                 \--

     Layer3 source network:

         IP address                                        Prefix
length

 

     Destination authenticate subnet:


         IP
address                                        Prefix length 

Before passing portal authentication, a
user that uses the iNode client can access only the authentication page http://192.168.0.111:8080/portal. All Web requests from
the user will be redirected to the authentication page.

·     The user can access the resources permitted by
ACL 3000 after passing only identity authentication. 

·     The user can access network resources permitted
by ACL 3001 after passing both identity authentication and security check.

\# After the user passes identity
authentication and security check, use the following command to display
information about the portal user.

\[Switch] display portal user
interface vlan-interface 100

Total portal users: 1

Username: abc

  Portal server: newpt

  State: Online

  VPN instance: N/A

  MAC                IP                 VLAN  
Interface

  0015-e9a6-7cfe     2.2.2.2          
 100    Vlan-interface100

  Authorization information:

    DHCP IP pool: N/A

    User profile: N/A

    Session group profile: N/A

    ACL: 3001

    Inbound CAR: N/A

    Outbound CAR: N/A

### Example: Configuring extended re-DHCP portal authentication

#### Network configuration

As shown in [Figure 16](#_Ref363914302), the
host is directly connected to the switch (the access device). The host obtains
an IP address through the DHCP server. A portal server acts as both a portal
authentication server and a portal Web server. A RADIUS server acts as the authentication
and accounting server.

Configure extended re-DHCP portal
authentication. Before passing portal authentication, the host is assigned a
private IP address. After passing portal identity authentication, the host
obtains a public IP address and accepts security check. If the host fails the
security check, it can access only subnet 192.168.0.0/24. After passing the
security check, the host can access other network resources.

Figure 16 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705358_x_Img_x_png_18_2216065_294551_0.png)

‌

#### Restrictions and guidelines

·     For re-DHCP portal authentication, configure a
public address pool (20.20.20.0/24) and a private address pool (10.0.0.0/24) on
the DHCP server. (Details not shown.)

·     For re-DHCP portal authentication:

¡     The switch
must be configured as a DHCP relay agent.

¡     The
portal-enabled interface must be configured with a primary IP address (a public
IP address) and a secondary IP address (a private IP address).

For information about DHCP relay agent
configuration, see Layer 3—IP Services Configuration
Guide.

·     Make sure the IP address of the portal device
added on the portal server is the public IP address (20.20.20.1) of the
switch's interface connecting the host. The private IP address range for the IP
address group associated with the portal device is the private subnet 10.0.0.0/24
where the host resides. The public IP address range for the IP address group is
the public subnet 20.20.20.0/24.

#### Prerequisites

·     Configure IP addresses for the switch and
servers as shown in [Figure 16](#_Ref363914302) and make
sure the host, switch, and servers can reach each other.

·     Configure the RADIUS server correctly to provide
authentication and accounting functions.

#### Procedure

**1\.**Configure a RADIUS scheme:

\# Create a RADIUS scheme named rs1 and enter
its view.

\<Switch\> system-view

\[Switch] radius scheme rs1

\# Specify the primary authentication
server and primary accounting server, and configure the keys for communication
with the servers.

\[Switch-radius-rs1] primary
authentication 192.168.0.113

\[Switch-radius-rs1] primary
accounting 192.168.0.113

\[Switch-radius-rs1] key
accounting simple radius

\[Switch-radius-rs1] key
authentication simple radius

\[Switch-radius-rs1] user-name-format
without-domain

\# Enable RADIUS session control.

\[Switch] radius
session-control enable

\# Specify a
session-control client with IP address 192.168.0.113 and shared key 12345 in plaintext form.

\[Switch] radius
session-control client ip 192.168.0.113 key simple 12345

**2\.**Configure an authentication domain:

\# Create an ISP domain named dm1 and enter
its view.

\[Switch] domain dm1

\# Configure AAA methods for the ISP
domain.

\[Switch-isp-dm1] authentication
portal radius-scheme rs1

\[Switch-isp-dm1] authorization
portal radius-scheme rs1

\[Switch-isp-dm1] accounting
portal radius-scheme rs1

\[Switch-isp-dm1] quit

\# Configure domain dm1 as the
default ISP domain. If a user enters the username without the ISP domain name
at login, the authentication and accounting methods of the default domain are
used for the user.

\[Switch] domain default enable
dm1

**3\.**Configure ACL 3000 as the isolation ACL and
ACL 3001 as the security ACL.

\[Switch] acl advanced 3000

\[Switch-acl-ipv4-adv-3000]
rule permit ip destination 192.168.0.0 0.0.0.255

\[Switch-acl-ipv4-adv-3000] rule
deny ip

\[Switch-acl-ipv4-adv-3000]
quit

\[Switch] acl advanced 3001

\[Switch-acl-ipv4-adv-3001]
rule permit ip

\[Switch-acl-ipv4-adv-3001]
quit

 

|  | NOTE:  Make sure you specify ACL 3000 as the isolation ACL and ACL 3001 as the security ACL on the security policy server. |
| --- | --- |

‌

**4\.**Configure DHCP relay and authorized ARP:

\# Configure DHCP relay.

\[Switch] dhcp enable

\[Switch] dhcp relay
client-information record

\[Switch] interface
vlan-interface 100

\[Switch–Vlan-interface100] ip
address 20.20.20.1 255.255.255.0

\[Switch–Vlan-interface100] ip
address 10.0.0.1 255.255.255.0 sub

\[Switch-Vlan-interface100]
dhcp select relay

\[Switch-Vlan-interface100]
dhcp relay server-address 192.168.0.112

\# Enable authorized ARP.

\[Switch-Vlan-interface100] arp
authorized enable

\[Switch-Vlan-interface100]
quit

**5\.**Configure portal authentication:

\# Configure a portal authentication
server.

\[Switch] portal server newpt

\[Switch-portal-server-newpt] ip
192.168.0.111 key simple portal

\[Switch-portal-server-newpt] port
50100

\[Switch-portal-server-newpt]
quit

\# Configure a portal Web server.

\[Switch] portal web-server
newpt

\[Switch-portal-websvr-newpt] url
http://192.168.0.111:8080/portal

\[Switch-portal-websvr-newpt]
quit

\# Enable re-DHCP portal authentication on
VLAN-interface 100\.

\[Switch] interface
vlan-interface 100

\[Switch–Vlan-interface100]
portal enable method redhcp

\# Specify portal Web server newpt on
VLAN-interface 100\.

\[Switch–Vlan-interface100] portal
apply web-server newpt

\# Configure the BAS-IP as 20.20.20.1 for
portal packets sent from VLAN-interface 100 to the portal authentication
server.

\[Switch–Vlan-interface100] portal
bas-ip 20.20.20.1

\[Switch–Vlan-interface100]
quit

#### Verifying the configuration

\# Verify that the portal configuration has
taken effect.

\[Switch] display portal interface vlan-interface
100

 Portal information of
Vlan-interface100

     NAS-ID profile: Not configured

     Authorization : Strict checking 

     ACL           : Disabled

     User profile  : Disabled

     Dual stack    : Disabled

     Max users     : Not configured

 IPv4:

     Portal status: Enabled

     Portal authentication method:
Redhcp

     Portal web server: newpt(active)

     Secondary portal Web server: Not
configured

     Portal mac-trigger-server: Not
configured

     Authentication domain: Not
configured

     User-dhcp-only: Disabled

     Pre-auth IP pool: Not configured

     Max users: Not configured

     Bas-ip: 20.20.20.1

     User detection:  Not configured

     Action for server detection:

         Server type    Server
name                        Action 

         \--            
\--                                 \-- 

     Layer3 source network:

         IP address              
Mask

 

     Destination authenticate subnet:

         IP address              
Mask

IPv6:

     Portal status: Disabled

     Portal authentication method:
Disabled

     Portal web server: Not
configured

     Secondary portal Web server: Not
configured

     Portal mac-trigger-server: Not
configured

     Authentication domain: Not
configured

     User-dhcp-only: Disabled

     Pre-auth IP pool: Not configured

     Max users: Not configured

     Bas-ipv6: Not configured

     User detection: Not configured

     Action for server detection:

         Server type    Server
name                        Action

         \--            
\--                                 \--

     Layer3 source network:

         IP
address                                        Prefix length

 

     Destination authenticate subnet:


         IP
address                                        Prefix length 

Before passing portal authentication, a
user that uses the iNode client can access only the authentication page http://192.168.0.111:8080/portal. All Web requests from
the user will be redirected to the authentication page.

·     The user can access the resources permitted by
ACL 3000 after passing only identity authentication. 

·     The user can access network resources permitted
by ACL 3001 after passing both identity authentication and security check.

\# After the user passes identity
authentication and security check, use the following command to display
information about the portal user.

\[Switch] display portal user
interface vlan-interface 100

Total portal users: 1

Username: abc

  Portal server: newpt

  State: Online

  VPN instance: N/A

  MAC                IP                 VLAN  
Interface

  0015-e9a6-7cfe     20.20.20.2        
100    Vlan-interface100

  Authorization information:

    DHCP IP pool: N/A

    User profile: N/A

    Session group profile: N/A

    ACL number: 3001 (active)

    Inbound CAR: N/A

    Outbound CAR: N/A

### Example: Configuring extended cross-subnet portal authentication

#### Network configuration

As shown in [Figure 17](#_Ref363916096), Switch
A supports portal authentication. The host accesses Switch A through Switch B.
A portal server acts as both a portal authentication server and a portal Web
server. A RADIUS server acts as the authentication and accounting server.

Configure Switch A for extended
cross-subnet portal authentication. Before passing portal authentication, the
host can access only the portal server. After passing portal identity
authentication, the host accepts security check. If the host fails the security
check it can access only the subnet 192.168.0.0/24. After passing the security
check, the host can access other network resources.

Figure 17 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705359_x_Img_x_png_19_2216065_294551_0.png)

‌

#### Restrictions and guidelines

Make sure the IP address of the portal
device added on the portal server is the IP address (20.20.20.1) of the
switch's interface connecting the host. The IP address group associated with
the portal device is the subnet of the host (8.8.8.0/24).

#### Prerequisites

·     Configure IP addresses for the switch and
servers as shown in [Figure 17](#_Ref363916096) and make
sure the host, switch, and servers can reach each other.

·     Configure the RADIUS server correctly to provide
authentication and accounting functions.

#### Procedure

**1\.**Configure a RADIUS scheme:

\# Create a RADIUS scheme named rs1 and enter
its view.

\<SwitchA\> system-view

\[SwitchA] radius scheme rs1

\# Specify the primary authentication
server and primary accounting server, and configure the keys for communication
with the servers.

\[SwitchA-radius-rs1] primary
authentication 192.168.0.112

\[SwitchA-radius-rs1] primary
accounting 192.168.0.112

\[SwitchA-radius-rs1] key
accounting simple radius

\[SwitchA-radius-rs1] key
authentication simple radius

\[SwitchA-radius-rs1] user-name-format
without-domain

\# Enable RADIUS session control.

\[SwitchA] radius
session-control enable

\# Specify a
session-control client with IP address 192.168.0.112 and shared key 12345 in plaintext form.

\[SwitchA] radius
session-control client ip 192.168.0.112 key simple 12345

**2\.**Configure an authentication domain:

\# Create an ISP domain named dm1 and enter
its view.

\[SwitchA] domain dm1

\# Configure AAA methods for the ISP
domain.

\[SwitchA-isp-dm1] authentication
portal radius-scheme rs1

\[SwitchA-isp-dm1] authorization
portal radius-scheme rs1

\[SwitchA-isp-dm1] accounting
portal radius-scheme rs1

\[SwitchA-isp-dm1] quit

\# Configure domain dm1 as the
default ISP domain. If a user enters the username without the ISP domain name
at login, the authentication and accounting methods of the default domain are
used for the user.

\[SwitchA] domain default
enable dm1

**3\.**Configure ACL 3000 as the isolation ACL and
ACL 3001 as the security ACL.

\[SwitchA] acl advanced 3000

\[SwitchA-acl-ipv4-adv-3000]
rule permit ip destination 192.168.0.0 0.0.0.255

\[SwitchA-acl-ipv4-adv-3000]
rule deny ip

\[SwitchA-acl-ipv4-adv-3000]
quit

\[SwitchA] acl advanced 3001

\[SwitchA-acl-ipv4-adv-3001]
rule permit ip

\[SwitchA-acl-ipv4-adv-3001]
quit

 

|  | NOTE:  Make sure you specify ACL 3000 as the isolation ACL and ACL 3001 as the security ACL on the security policy server. |
| --- | --- |

‌

**4\.**Configure portal authentication:

\# Configure a portal authentication
server.

\[SwitchA] portal server newpt

\[SwitchA-portal-server-newpt] ip
192.168.0.111 key simple portal

\[SwitchA-portal-server-newpt] port
50100

\[SwitchA-portal-server-newpt]
quit

\# Configure a portal Web server.

\[SwitchA] portal web-server
newpt

\[SwitchA-portal-websvr-newpt] url
http://192.168.0.111:8080/portal

\[SwitchA-portal-websvr-newpt]
quit

\# Enable cross-subnet portal
authentication on VLAN-interface 4\.

\[SwitchA] interface
vlan-interface 4

\[SwitchA–Vlan-interface4]
portal enable method layer3

\# Specify portal Web server newpt on
VLAN-interface 4\.

\[SwitchA–Vlan-interface4] portal
apply web-server newpt

\# Configure the BAS-IP as 20.20.20.1 for
portal packets sent from VLAN-interface 4 to the portal authentication server.

\[SwitchA–Vlan-interface4] portal
bas-ip 20.20.20.1

\[SwitchA–Vlan-interface4] quit

#### Verifying the configuration

\# Verify that the portal configuration has
taken effect.

\[SwitchA] display portal interface
vlan-interface 4

 Portal information of Vlan-interface4

     NAS-ID profile: Not configured

     Authorization : Strict checking 

     ACL           : Disabled

     User profile  : Disabled

     Dual stack    : Disabled

     Max users     : Not configured

 IPv4:

     Portal status: Enabled

     Portal authentication method: Layer3

     Portal web server: newpt(active)

     Secondary portal Web server: Not
configured

     Portal mac-trigger-server: Not
configured

     Authentication domain: Not
configured

     User-dhcp-only: Disabled

     Pre-auth IP pool: Not configured

     Max users: Not configured

     Bas-ip: 20.20.20.1

     User detection:  Not configured

     Action for server detection:

         Server type    Server
name                        Action 

         \--            
\--                                 \-- 

     Layer3 source network:

         IP address              
Mask

 

     Destination authenticate subnet:

         IP address              
Mask

IPv6:

     Portal status: Disabled

     Portal authentication method:
Disabled

     Portal web server: Not
configured(active)

     Secondary portal Web server: Not
configured

     Portal mac-trigger-server: Not
configured

     Authentication domain: Not
configured

     User-dhcp-only: Disabled

     Pre-auth IP pool: Not configured

     Max users: Not configured

     Bas-ip: Not configured

     User detection: Not configured

     Action for server detection:

         Server type    Server
name                        Action

         \--            
\--                                 \--

     Layer3 source network:

         IP
address                                        Prefix length

 

     Destination authenticate subnet:


         IP
address                                        Prefix length 

Before passing portal authentication, a
user that uses the iNode client can access only the authentication page http://192.168.0.111:8080/portal. All Web requests from
the user will be redirected to the authentication page.

·     The user can access the resources permitted by
ACL 3000 after passing only identity authentication. 

·     The user can access network resources permitted
by ACL 3001 after passing both identity authentication and security check.

\# After the user passes identity authentication
and security check, use the following command to display information about the
portal user.

\[SwitchA] display portal user
interface vlan-interface 4

Total portal users: 1

Username: abc

  Portal server: newpt

  State: Online

  VPN instance: N/A

  MAC                IP                 VLAN  
Interface

  0015-e9a6-7cfe     8.8.8.2          
 4    Vlan-interface4

  Authorization information:

    DHCP IP pool: N/A

    User profile: N/A

    Session group profile: N/A

    ACL number: 3001 (active)

    Inbound CAR: N/A

    Outbound CAR: N/A

### Example: Configuring portal server detection and portal user synchronization

#### Network configuration

As shown in [Figure 18](#_Ref364149995), the
host is directly connected to the switch (the access device). The host is
assigned a public IP address either manually or through DHCP. An IMC server acts
as both a portal authentication server and a portal Web server. A RADIUS server
acts as the authentication and accounting server. In this example, the IMC server
runs IMC 7.1 (E0303) and IMC UAM 7.1 (E0304).

·     Configure direct portal authentication on the
switch, so the host can access only the portal server before passing the
authentication and access other network resources after passing the
authentication.

·     Configure the switch to detect the reachability
state of the portal authentication server, send log messages upon state
changes, and disable portal authentication when the authentication server is
unreachable.

·     Configure the switch to synchronize portal user
information with the portal server periodically.

Figure 18 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705361_x_Img_x_png_20_2216065_294551_0.png)

‌

#### Prerequisites

·     Configure IP addresses for the switch and
servers as shown in [Figure 18](#_Ref364149995) and make
sure the host, switch, and servers can reach each other.

·     Configure the RADIUS server correctly to provide
authentication and accounting functions.

#### Configuring the portal server

**1\.**Configure the portal server:

**a.**Log in to IMC and click the User tab.

**b.**Select User Access Policy
\> Portal Service \> Server
from the navigation tree to open the portal server configuration page, as shown
in [Figure 19](#_Ref364250562).

**c.**Configure the portal server parameters as
needed.

This example uses the default settings.

**d.**Click OK.

Figure 19 Portal
server configuration

![](https://resource.h3c.com/en/202407/12/20240712_11705362_x_Img_x_png_21_2216065_294551_0.png)

‌

**2\.**Configure the IP address group:

**a.**Select User Access Policy
\> Portal Service \> IP
Group from the navigation tree to open the portal IP address group
configuration page.

**b.**Click Add to open
the page as shown in [Figure 20](#_Ref364250552). 

**c.**Enter the IP group name.

**d.**Enter the start IP address and end IP
address of the IP group. 

Make sure the host IP address is in the
IP group.

**e.**Select a service group.

This example uses the default group Ungrouped.

**f.**Select Normal
from the Action list.

**g.**Click OK.

Figure 20 Adding
an IP address group

![](https://resource.h3c.com/en/202407/12/20240712_11705363_x_Img_x_png_22_2216065_294551_0.png)

‌

**3\.**Add a portal device:

**a.**Select User Access Policy
\> Portal Service \> Device
from the navigation tree to open the portal device configuration page.

**b.**Click Add to open
the page as shown in [Figure 21](#_Ref364250534).

**c.**Enter device name NAS.

**d.**Enter the IP address of the switch's
interface connected to the host.

**e.**Set whether to support the portal server
heartbeat and user heartbeat functions.

In this example, select Yes for both Support Server Heartbeat and Support User
Heartbeat.

**f.**Enter the key, which must be the same as
that configured on the switch.

**g.**Select Directly
Connected from the Access Method list.

**h.**Use the default settings for other
parameters.

**i.**Click OK.

Figure 21 Adding
a portal device

![](https://resource.h3c.com/en/202407/12/20240712_11705364_x_Img_x_png_23_2216065_294551_0.png)

‌

**4\.**Associate the portal device with the IP
address group:

**a.**As shown in [Figure 22](#_Ref364250482),
click the Port Group Information Management icon ![](https://resource.h3c.com/en/202407/12/20240712_11705365_x_Img_x_png_24_2216065_294551_0.png) for device NAS to open the port group
configuration page. 

**b.**Click Add to open
the page as shown in [Figure 23](#_Ref364250489).

**c.**Enter the port group name.

**d.**Select the configured IP address group. 

The IP address used by the user to access
the network must be within this IP address group.

**e.**Use the default settings for other
parameters.

**f.**Click OK.

Figure 22 Device
list

![](https://resource.h3c.com/en/202407/12/20240712_11705366_x_Img_x_png_25_2216065_294551_0.png)

‌

Figure 23 Adding
a port group

![](https://resource.h3c.com/en/202407/12/20240712_11705367_x_Img_x_png_26_2216065_294551_0.png)

‌

#### Configuring the switch

**1\.**Configure a RADIUS scheme:

\# Create a RADIUS scheme named rs1 and enter
its view.

\<Switch\> system-view

\[Switch] radius scheme rs1

\# Specify the primary authentication
server and primary accounting server, and configure the keys for communication
with the servers.

\[Switch-radius-rs1] primary
authentication 192.168.0.112

\[Switch-radius-rs1] primary
accounting 192.168.0.112

\[Switch-radius-rs1] key
authentication simple radius

\[Switch-radius-rs1] key
accounting simple radius

\# Exclude the ISP domain name from the
username sent to the RADIUS server.

\[Switch-radius-rs1] user-name-format
without-domain

\[Switch-radius-rs1] quit

\# Enable RADIUS session control.

\[Switch] radius
session-control enable

**2\.**Configure an authentication domain:

\# Create an ISP domain named dm1 and enter
its view.

\[Switch] domain dm1

\# Configure AAA methods for the ISP
domain.

\[Switch-isp-dm1] authentication
portal radius-scheme rs1

\[Switch-isp-dm1] authorization
portal radius-scheme rs1

\[Switch-isp-dm1] accounting
portal radius-scheme rs1

\[Switch-isp-dm1] quit

\# Configure domain dm1 as the
default ISP domain. If a user enters the username without the ISP domain name
at login, the authentication and accounting methods of the default domain are
used for the user.

\[Switch] domain default enable
dm1

**3\.**Configure portal authentication:

\# Configure a portal authentication
server.

\[Switch] portal server newpt

\[Switch-portal-server-newpt] ip
192.168.0.111 key simple portal

\[Switch-portal-server-newpt] port
50100

\# Configure reachability detection of the
portal authentication server: set the server detection interval to 40 seconds,
and send log messages upon reachability status changes.

\[Switch-portal-server-newpt]
server-detect timeout 40 log

 

|  | NOTE:  The value of timeout must be greater than or equal to the portal server heartbeat interval. |
| --- | --- |

‌

\# Configure portal user synchronization
with the portal authentication server, and set the synchronization detection
interval to 600 seconds.

\[Switch-portal-server-newpt]
user-sync timeout 600

\[Switch-portal-server-newpt]
quit

 

|  | NOTE:  The value of timeout must be greater than or equal to the portal user heartbeat interval. |
| --- | --- |

‌

\# Configure a portal Web server.

\[Switch] portal web-server
newpt

\[Switch-portal-websvr-newpt] url
http://192.168.0.111:8080/portal

\[Switch-portal-websvr-newpt]
quit

\# Enable direct portal authentication on VLAN-interface
100\.

\[Switch] interface
vlan-interface 100

\[Switch–Vlan-interface100]
portal enable method direct

\# Enable portal fail-permit for the
portal authentication server newpt.

\[Switch–Vlan-interface100]
portal fail-permit server newpt

\# Specify portal Web server newpt on
VLAN-interface 100\.

\[Switch–Vlan-interface100]
portal apply web-server newpt

\# Configure the BAS-IP as 2.2.2.1 for
portal packets sent from VLAN-interface 100 to the portal authentication
server.

\[Switch–Vlan-interface100] portal
bas-ip 2.2.2.1

\[Switch–Vlan-interface100]
quit

#### Verifying the configuration

\# Use the following command to display
information about the portal authentication server.

\[Switch] display portal server newpt

Portal server: newpt

  Type                  : IMC

  IP                    :
192.168.0.111

  VPN instance          : Not
configured

  Port                  : 50100

  Server Detection      : Timeout
40s  Action: log

  User synchronization  : Timeout
600s

  Status                : Up

The Up status
of the portal authentication server indicates that the portal authentication
server is reachable. If the access device detects that the portal
authentication server is unreachable, the Status
field in the command output displays Down. The access
device generates a server unreachable log "Portal server newpt turns down
from up." and disables portal authentication on the access interface, so
the host can access the external network without authentication.

### Example: Configuring direct portal authentication using a local portal Web service

#### Network configuration

As shown in [Figure 24](#_Ref420403998), the host is directly
connected to the switch (the access device). The host is assigned a public IP
address either manually or through DHCP. The switch acts as both a portal
authentication server and a portal Web server. A RADIUS server acts as the authentication
and accounting server.

Configure direct portal authentication on
the switch. Before a user passes portal authentication, the user can access
only the portal Web server. After passing portal authentication, the user can
access other network resources.

Figure 24 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705368_x_Img_x_png_27_2216065_294551_0.png)

‌

#### Prerequisites

·     Configure IP addresses for the host, switch, and
server as shown in [Figure 24](#_Ref420403998)
and make sure they can reach each other.

·     Configure the RADIUS server correctly to provide
authentication and accounting functions.

#### Procedure

**1\.**Configure a RADIUS scheme:

\# Create a RADIUS scheme named rs1 and enter its
view.

\<Switch\> system-view

\[Switch] radius scheme rs1

\# Specify the primary authentication
server and primary accounting server, and configure the keys for communication
with the servers.

\[Switch-radius-rs1] primary
authentication 192.168.0.112

\[Switch-radius-rs1] primary
accounting 192.168.0.112

\[Switch-radius-rs1] key
authentication simple radius

\[Switch-radius-rs1] key
accounting simple radius

\# Exclude the ISP domain name from the
username sent to the RADIUS server.

\[Switch-radius-rs1] user-name-format
without-domain

\[Switch-radius-rs1] quit

\# Enable RADIUS session control.

\[Switch] radius
session-control enable

**2\.**Configure an authentication domain:

\# Create an ISP domain named dm1 and enter
its view.

\[Switch] domain dm1

\# Configure AAA methods for the ISP
domain.

\[Switch-isp-dm1] authentication
portal radius-scheme rs1

\[Switch-isp-dm1] authorization
portal radius-scheme rs1

\[Switch-isp-dm1] accounting
portal radius-scheme rs1

\[Switch-isp-dm1] quit

\# Configure domain dm1 as the
default ISP domain. If a user enters the username without the ISP domain name at
login, the authentication and accounting methods of the default domain are used
for the user.

\[Switch] domain default enable
dm1

**3\.**Configure portal authentication:

\# Configure a portal Web server named newpt and
specify http://2.2.2.1:2331/portal as the URL of the portal Web server. The IP address in the URL must
be the IP address of a Layer 3 interface reachable to portal clients or a
loopback interface (except 127.0.0.1) on the device.

\[Switch] portal web-server
newpt

\[Switch-portal-websvr-newpt] url
http://2.2.2.1:2331/portal

\[Switch-portal-websvr-newpt]
quit

\# Enable direct portal authentication on
VLAN-interface 100\.

\[Switch] interface
vlan-interface 100

\[Switch–Vlan-interface100]
portal enable method direct

\# Specify portal Web server newpt on
VLAN-interface 100\.

\[Switch–Vlan-interface100]
portal apply web-server newpt

\[Switch–Vlan-interface100]
quit

\# Create an HTTP-based local portal Web
service and enter its view.

\[Switch] portal
local-web-server http

\# Specify file defaultfile.zip
as the default authentication page file for the local portal Web service. (Make
sure the file exists under the root directory of the switch.)

\[Switch–portal-local-websvr-http]
default-logon-page defaultfile.zip

\# Set the HTTP listening port number to
2331 for the local portal Web service.

\[Switch–portal-local-webserver-http]
tcp-port 2331

\[Switch–portal-local-websvr-http]
quit

#### Verifying the configuration

\# Verify that the portal configuration has
taken effect.

\[Switch] display portal interface vlan-interface
100

 Portal information of Vlan-interface
100

     Authorization                  
Strict checking 

     ACL                            
Disabled

     User profile                   
Disabled

 IPv4:

     Portal status: Enabled

     Portal authentication method: Direct

     Portal web server: newpt(active)

     Secondary portal Web server: Not
configured

     Portal mac-trigger-server: Not
configured

     Authentication domain: Not
configured

     User-dhcp-only: Disabled

     Pre-auth IP pool: Not configured

     Max users: Not configured

     Bas-ip: Not configured

     User detection:  Not configured

     Action for server detection:

         Server type    Server
name                        Action 

         \--            
\--                                 \-- 

     Layer3 source network:

         IP address              
Mask

 

     Destination authenticate subnet:

         IP address              
Mask

IPv6:

     Portal status: Disabled

     Portal authentication method:
Disabled

     Portal web server: Not configured

     Secondary portal Web server: Not
configured

     Portal mac-trigger-server: Not
configured

     Authentication domain: Not
configured

     User-dhcp-only: Disabled

     Pre-auth IP pool: Not configured

     Max users: Not configured

     Bas-ipv6: Not configured

     User detection: Not configured

     Action for server detection:

         Server type    Server
name                        Action

         \--            
\--                                 \--

     Layer3 source network:

         IP
address                                        Prefix length

 

     Destination authenticate subnet:


         IP
address                                        Prefix length 

A user can perform portal authentication
through a Web page. Before passing the authentication, the user can access only
the authentication page http://2.2.2.1:2331/portal
and all Web requests will be redirected to the authentication page. After
passing the authentication, the user can access other network resources.

\# After the user passes authentication, use
the following command to display information about the portal user.

\[Switch] display portal user
interface vlan-interface 100

Total portal users: 1

Username: abc

  Portal server: newpt

  State: Online

  VPN instance: N/A

  MAC                IP                 VLAN  
Interface

  0015-e9a6-7cfe     2.2.2.2          
 100     Vlan-interface100

  Authorization information:

    DHCP IP pool: N/A

    User profile: N/A

    Session group profile: N/A

    ACL number: N/A

    Inbound CAR: N/A

    Outbound CAR: N/A

### Example: Configuring portal authentication to support M-LAG

#### Network configuration

As shown in [Figure 25](#_Ref66717352), the
hosts access the network through Device C (Layer 2 device). Each host is
assigned a public IP address either manually or through DHCP. 

An IMC server acts as a portal server (portal
authentication server and portal Web server). A RADIUS server acts as the
authentication and accounting server. In this example, the IMC server runs IMC 7.1
(E0303) and IMC UAM 7.1 (E0304).

Configure the devices as follows:

·     Configure direct portal authentication, so the
hosts can access only the portal server before passing the authentication and
access other network resources after passing the authentication.

·     Configure Device A and Device B to form an M-LAG
system to load share traffic. When a device fails, the other device can take
over to ensure service continuity.

·     Set the authentication load sharing mode to
distributed if a large number of portal users exist.

Figure 25 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705369_x_Img_x_png_28_2216065_294551_0.png)

‌

Table 2 Interface and IP address assignment

| Device | Interface | IP address | Device | Interface | IP address || Device A | Vlan-int10 | 2.2.2.1/24 | Device B | Vlan-int10 | 2.2.2.1/24 || Device A | Vlan-int20 | 3.3.3.1/24 | Device B | Vlan-int20 | 3.3.3.1/24 || Device A | XGE1/0/3 | 1.1.1.1/24 | Device B | XGE1/0/3 | 1.1.1.2/24 || Device C | Vlan-int10 | 2.2.2.3/24 | Device D | Vlan-int10 | 3.3.3.3/24 |




| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |

‌

#### Prerequisites

·     Configure IP addresses for the host, switch, and
servers as shown in [Figure 25](#_Ref66717352) and
make sure they can reach each other.

·     Configure the RADIUS server correctly to provide
authentication and accounting functions.

#### Configuring the M-LAG system

**1\.**Configure Device A.

\# Configure M-LAG system settings.

\<DeviceA\> system-view

\[DeviceA] m-lag system-mac
1-1-1

\[DeviceA] m-lag system-number
1

\[DeviceA] m-lag
system-priority 123

\# Configure the destination and source IP
addresses of keepalive packets.

\[DeviceA] m-lag keepalive ip
destination 1.1.1.1 source 1.1.1.2

\# Configure port Ten-GigabitEthernet 1/0/3 to operate in
Layer 3 mode and configure the source IP address of keepalive packets as the IP
address of this port.

\[DeviceA] interface ten-gigabitethernet 1/0/3

\[DeviceA-Ten-GigabitEthernet1/0/3] port link-mode
route

\[DeviceA-Ten-GigabitEthernet1/0/3] ip address 1.1.1.2
24

\[DeviceA-Ten-GigabitEthernet1/0/3] quit

\# Exclude Ten-GigabitEthernet 1/0/3 (keepalive
interface) from being shut down upon detection of multi-active collisions.

\[DeviceA] m-lag mad exclude
interface ten-gigabitethernet 1/0/3

\# Create Layer 2 aggregate interface
Bridge-Aggregation 3 and set the aggregation mode to dynamic.

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

\# Configure Layer 2 aggregate interface Bridge-Aggregation
3 as a trunk port, add the interface to VLAN 10 and VLAN 20, and configure the
interface as a peer-link interface.

\[DeviceA] interface
bridge-aggregation 3

\[DeviceA-Bridge-Aggregation3]
port link-type trunk

\[DeviceA-Bridge-Aggregation3]
port trunk permit vlan 10 20

\[DeviceA-Bridge-Aggregation3]
port m-lag intra-portal-port 1

\[DeviceA-Bridge-Aggregation3]
quit

\# Create Layer 2 aggregate interface
Bridge-Aggregation 4 and set the aggregation mode to dynamic.

\[DeviceA] interface
bridge-aggregation 4

\[DeviceA-Bridge-Aggregation4]
link-aggregation mode dynamic

\[DeviceA-Bridge-Aggregation4]
quit

\# Assign Ten-GigabitEthernet 1/0/4 and Ten-GigabitEthernet 1/0/5 to aggregation
group 4\.

\[DeviceA] interface ten-gigabitethernet 1/0/4

\[DeviceA-Ten-GigabitEthernet1/0/4] port link-aggregation
group 4

\[DeviceA-Ten-GigabitEthernet1/0/4] quit

\[DeviceA] interface ten-gigabitethernet 1/0/5

\[DeviceA-Ten-GigabitEthernet1/0/5] port
link-aggregation group 4

\[DeviceA-Ten-GigabitEthernet1/0/5] quit

\# Add Layer 2 aggregate interface
Bridge-Aggregation 4 to VLAN 10, and configure the interface as an M-LAG
interface.

\[DeviceA] interface
bridge-aggregation 4

\[DeviceA-Bridge-Aggregation4]
port access vlan 10

\[DeviceA-Bridge-Aggregation4]
port m-lag group 4

\[DeviceA-Bridge-Aggregation4]
quit

\# Create Layer 2 aggregate interface
Bridge-Aggregation 5 and set the aggregation mode to dynamic.

\[DeviceA] interface
bridge-aggregation 5

\[DeviceA-Bridge-Aggregation5]
link-aggregation mode dynamic

\[DeviceA-Bridge-Aggregation5]
quit

\# Assign Ten-GigabitEthernet 1/0/6 and Ten-GigabitEthernet 1/0/7 to aggregation
group 5\.

\[DeviceA] interface ten-gigabitethernet 1/0/6

\[DeviceA-Ten-GigabitEthernet1/0/6] port
link-aggregation group 5

\[DeviceA-Ten-GigabitEthernet1/0/6] quit

\[DeviceA] interface ten-gigabitethernet 1/0/7

\[DeviceA-Ten-GigabitEthernet1/0/7] port
link-aggregation group 5

\[DeviceA-Ten-GigabitEthernet1/0/7] quit

\# Add Layer 2 aggregate interface
Bridge-Aggregation 5 to VLAN 10, and configure the interface as an M-LAG
interface.

\[DeviceA] interface
bridge-aggregation 5

\[DeviceA-Bridge-Aggregation5]
port access vlan 20

\[DeviceA-Bridge-Aggregation5]
port m-lag group 5

\[DeviceA-Bridge-Aggregation5]
quit

**2\.**Configure Device B in the same way Device A
is configured.

**3\.**Configure Device C.

\# Create Layer 2 aggregate interface
Bridge-Aggregation 4 and set the aggregation mode to dynamic.

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

\# Add Layer 2 aggregate interface
Bridge-Aggregation 4 to VLAN 100\.

\[DeviceC] interface
bridge-aggregation 4

\[DeviceC-Bridge-Aggregation4]
port access vlan 10

\[DeviceC-Bridge-Aggregation4]
quit

\# Add Ten-GigabitEthernet 1/0/5 through Ten-GigabitEthernet 1/0/7 to VLAN 10\.

\[DeviceC] interface range ten-gigabitethernet 1/0/5 to ten-gigabitethernet 1/0/7

\[DeviceC-if-range] port access
vlan 10

\[DeviceC-if-range] quit

**4\.**Configure Device D in the same way Device C
is configured.

#### Configuring the portal server

**1\.**Configure the portal server:

**a.**Log in to IMC and click the User tab.

**b.**Select User Access
Policy \> Portal Service \> Server from the navigation tree to open the portal server
configuration page, as shown in [Figure 26](#_Ref66718183).

**c.**Configure the portal server parameters as
needed.

This example uses the default settings.

**d.**Click OK.

Figure 26 Portal server configuration

![](https://resource.h3c.com/en/202407/12/20240712_11705370_x_Img_x_png_29_2216065_294551_0.png)

‌

**2\.**Configure the IP address group:

**a.**Select User Access
Policy \> Portal Service \> IP Group from the navigation tree to open the portal IP
address group configuration page.

**b.**Click Add to
open the page as shown in [Figure 27](#_Ref66718197). 

**c.**Enter the IP group name.

**d.**Enter the start IP address and end IP
address of the IP group. 

Make sure the host IP address is in the
IP group.

**e.**Select a service group.

This example uses the default group Ungrouped.

**f.**Select Normal
from the Action list.

**g.**Click OK.

Figure 27 Adding an IP address group

![](https://resource.h3c.com/en/202407/12/20240712_11705372_x_Img_x_png_30_2216065_294551_0.png)

‌

**3\.**Add a portal device:

**a.**Select User Access
Policy \> Portal Service \> Device from the navigation tree to open the portal device
configuration page.

**b.**Click Add to
open the page as shown in [Figure 28](#_Ref66718239).

**c.**Enter device name NAS.

**d.**Enter the IP address of the device's
interface connected to the host.

**e.**Set whether to support the portal server
heartbeat and user heartbeat functions.

In this example, select No for both Support Server Heartbeat and Support User
Heartbeat.

**f.**Enter the key, which must be the same as
that configured on the device.

**g.**Select Directly
Connected from the Access Method list.

**h.**Use the default settings for other
parameters.

**i.**Click OK.

Figure 28 Adding a portal device

![](https://resource.h3c.com/en/202407/12/20240712_11705373_x_Img_x_png_31_2216065_294551_0.png)

‌

**4\.**Associate the portal device with the IP
address group:

**a.**As shown in [Figure 29](#_Ref66718260), click
the Port Group Information Management icon ![](https://resource.h3c.com/en/202407/12/20240712_11705374_x_Img_x_png_32_2216065_294551_0.png) for device NAS to open the port group
configuration page. 

**b.**Click Add to
open the page as shown in [Figure 30](#_Ref66718271).

**c.**Enter the port group name.

**d.**Select the configured IP address group. 

The IP address used by the user to access
the network must be within this IP address group.

**e.**Use the default settings for other
parameters.

**f.**Click OK.

Figure 29 Device list

![](https://resource.h3c.com/en/202407/12/20240712_11705375_x_Img_x_png_33_2216065_294551_0.png)

‌

Figure 30 Adding a port group

![](https://resource.h3c.com/en/202407/12/20240712_11705376_x_Img_x_png_34_2216065_294551_0.png)

‌

#### Configuring portal authentication

**1\.**Configure Device A.

**a.**Configure a RADIUS scheme:

\# Create a RADIUS scheme named rs1 and enter its
view.

\<DeviceA\> system-view

\[DeviceA] radius scheme rs1

\# Specify the primary authentication
server and primary accounting server, and configure the keys for communication
with the servers.

\[DeviceA-radius-rs1] primary authentication 192.168.0.112

\[DeviceA-radius-rs1] primary accounting 192.168.0.112

\[DeviceA-radius-rs1] key authentication simple radius

\[DeviceA-radius-rs1] key accounting simple radius

\# Exclude the ISP domain name from the
username sent to the RADIUS server.

\[DeviceA-radius-rs1] user-name-format without-domain

\[DeviceA-radius-rs1] quit

**b.**Configure an authentication domain:

\# Create an ISP domain named dm1 and enter
its view.

\[DeviceA] domain dm1

\# Configure AAA methods for the ISP
domain.

\[DeviceA-isp-dm1] authentication portal radius-scheme rs1

\[DeviceA-isp-dm1] authorization portal radius-scheme rs1

\[DeviceA-isp-dm1] accounting portal radius-scheme rs1

\[DeviceA-isp-dm1] quit

\# Configure domain dm1 as the
default ISP domain. If a user enters the username without the ISP domain name at
login, the authentication and accounting methods of the default domain are used
for the user.

\[DeviceA] domain default enable dm1

**c.**Configure portal authentication:

\# Configure a portal authentication
server.

\[DeviceA] portal server newpt

\[DeviceA-portal-server-newpt] ip 192.168.0.111 key simple portal

\[DeviceA-portal-server-newpt] port 50100

\[DeviceA-portal-server-newpt] quit

\# Configure a portal Web server.

\[DeviceA] portal web-server newpt

\[DeviceA-portal-websvr-newpt] url http://192.168.0.111:8080/portal

\[DeviceA-portal-websvr-newpt] quit

\# Enable direct portal authentication on
VLAN-interface 100\.

\[DeviceA] interface vlan 10

\[DeviceA–Vlan-interface10] portal enable method direct

\# Specify portal Web server newpt on
VLAN-interface 100\.

\[DeviceA–Vlan-interface10] portal apply web-server newpt

\# Configure the BAS-IP as 2.2.2.1 for
portal packets sent from VLAN-interface 100 to the portal authentication
server.

\[DeviceA–Vlan-interface10] portal bas-ip 2.2.2.1

\[DeviceA–Vlan-interface10] quit

\# Set the authentication load sharing
mode to distributed and set the distribution criterion to even-IP.

\[DeviceA] portal m-lag
load-sharing-mode distributed even-ip

**2\.**Configure Device B.

\# Set the authentication load sharing
mode to distributed and set the distribution criterion to odd-IP.

\[DeviceB] portal m-lag
load-sharing-mode distributed odd-ip

\# Configure other configuration on Device
B in the same way Device A is configured.

#### Verifying the configuration

\# Verify that the portal configuration has
taken effect.

\[DeviceA] display portal interface vlan-interface
10

 Portal information of
Vlan-interface10

     NAS-ID profile: Not configured

     M-LAG Group ID : 4

     M-LAG role     : Master

     Authorization : Strict checking

     ACL           : Disabled

     User profile  : Disabled

     Dual stack    : Disabled

     Max users     : Not configured

 IPv4:

     Portal status: Enabled

     PEER\_SM state: M\_Synced

     Portal authentication method:
Direct

     Portal web server: newpt(active)

     Secondary portal Web server: Not
configured

     Portal mac-trigger-server: Not
configured

     Authentication domain: dm1

     Pre-auth domain: Not configured

     User-dhcp-only: Disabled

     Pre-auth IP pool: Not configured

     Max users: Not configured

     Bas-ip: 2.2.2.1

     User detection: Not configured

     Action for server detection:

         Server type    Server
name                        Action

         \--            
\--                                 \--

     Layer3 source network:

         IP address              
Mask

 

     Destination authentication
subnet:

         IP address              
Mask

 IPv6:

     Portal status: Disabled

     Portal authentication method:
Disabled

     Portal web server: Not
configured

     Secondary portal Web server: Not
configured

     Portal mac-trigger-server: Not
configured

     Authentication domain: Not
configured

     Pre-auth domain: Not configured

     User-dhcp-only: Disabled

     Pre-auth IP pool: Not configured

     Max users: Not configured

     Bas-ipv6: Not configured

     User detection: Not configured

     Action for server detection:

         Server type    Server
name                        Action

         \--            
\--                                 \--

     Layer3 source network:

         IP
address                                        Prefix length

 

     Destination authentication
subnet:

         IP
address                                        Prefix length

A user can perform portal authentication by
using the iNode client or through a Web browser. Before passing the
authentication, the user can access only the authentication page http://192.168.0.111:8080/portal. All Web requests from
the user will be redirected to the authentication page. After passing the
authentication, the user can access other network resources.

\# After the user passes authentication,
display information about the portal user on Device A.

\[DeviceA]
display portal user interface vlan-interface 10 verbose

Total portal
users: 1

Basic:

  Current IP
address: 2.2.2.2

  Original IP
address: 2.2.2.2

  Username:
11

  User ID:
0x10000012

  Access
interface: Vlan-interface10

 
Service-VLAN/Customer-VLAN: 10/-

  MAC
address: 00e0-4c68-8add

  Domain
name: dm1

  VPN
instance: N/A

  Status:
Online

  M-LAG user
state: Active

  Portal
server: N/A

  Portal
authentication method: Direct

AAA:

  Realtime
accounting interval: 0s, retry times: 1

  Idle cut:
N/A

  Session
duration: N/A, remaining: N/A

  Remaining
traffic: N/A

  Login time:
2019-09-16 06:48:26 UTC

 
Accounting-start fail action: Online

 
Accounting-update fail action: Online

  Accounting
quota-out action: Offline

  DHCP IP
pool: N/A

ACL\&QoS\&Multicast:

  Inbound
CAR: N/A

  Outbound
CAR: N/A

  ACL number:
N/A

  User
profile: N/A

  Session
group profile: N/A

  Max
multicast addresses: 4

  User group:
N/A

Flow
statistic:

  Uplink  
packets/bytes: 5/290

  Downlink
packets/bytes: 0/0

Peer flow
statistic:

  Uplink  
packets/bytes: 5/290

  Downlink
packets/bytes: 0/0

\# Display information about the portal user
on Device B.

\[DeviceB] display portal user
interface Vlan-interface 10 verbose

Total portal users: 1

Basic:

  Current IP address: 2.2.2.2

  Original IP address: 2.2.2.2

  Username: 11

  User ID: 0x1000000c

  Access interface: Vlan-interface10

  Service-VLAN/Customer-VLAN: 10/-

  MAC address: 00e0-4c68-8add

  Domain name: dm1

  VPN instance: N/A

  Status: Online

  M-LAG user state: Inactive

  Portal server: N/A

  Portal authentication method:
Direct

AAA:

  Realtime accounting interval: 0s,
retry times: 1

  Idle cut: N/A

  Session duration: N/A, remaining:
N/A

  Remaining traffic: N/A

  Login time: 2019-09-16 06:48:29 UTC

  Accounting-start fail action:
Online

  Accounting-update fail action:
Online

  Accounting quota-out action:
Offline

  DHCP IP pool: N/A

ACL\&QoS\&Multicast:

  Inbound CAR: N/A

  Outbound CAR: N/A

  ACL number: N/A

  User profile: N/A

  Session group profile: N/A

  Max multicast addresses: 4

  User group: N/A

Flow statistic:

  Uplink   packets/bytes: 5/290

  Downlink packets/bytes: 0/0

Peer flow statistic:

  Uplink   packets/bytes: 5/290

  Downlink packets/bytes: 0/0

## Troubleshooting portal

### No portal authentication page is pushed for users

#### Symptom

When a user is redirected to the IMC portal
authentication server, no portal authentication page or error message is
prompted for the user. The login page is blank.

#### Analysis

The key configured on the portal access
device and that configured on the portal authentication server are
inconsistent. As a result, packet verification fails, and the portal
authentication server refuses to push the authentication page.

#### Solution

Use the display this
command in portal authentication server view on the access device to check
whether a key is configured for the portal authentication server.

·     If no key is configured, configure the right
key.

·     If a key is configured, use the ip or ipv6 command in the
portal authentication server view to correct the key, or correct the key
configured for the access device on the portal authentication server.

### Cannot log out portal users on the access device

#### Symptom

You cannot use the portal
delete-user command on the access device to log
out a portal user, but the portal user can log out by clicking the Disconnect button on the portal authentication client.

#### Analysis

When you execute the portal
delete-user command on the access device to log
out a user, the access device sends an unsolicited logout notification message
to the portal authentication server. The destination port number in the logout notification
is the listening port number of the portal authentication server configured on
the access device. If this listening port number is not the actual listening
port number configured on the server, the server cannot receive the notification.
As a result, the server does not log out the user.

When a user uses the Disconnect button on the authentication client to log out, the portal
authentication server sends an unsolicited logout request message to the access
device. The access device uses the source port in the logout request as the
destination port in the logout ACK message. As a result, the portal
authentication server can definitely receive the logout ACK message and log out
the user.

#### Solution

**1\.**Use the display portal server command to display the listening port of the portal authentication
server configured on the access device.

**2\.**Use the portal server command in system view to change the listening port number to the
actual listening port of the portal authentication server.

### Cannot log out portal users on the RADIUS server

#### Symptom

The access device uses the IMC server as
the RADIUS server to perform identity authentication for portal users. You
cannot log out the portal users on the RADIUS server.

#### Analysis

The IMC server uses session control packets
to send disconnection requests to the access device. On the access device, the
listening UDP port for session control packets is disabled by default.
Therefore, the access device cannot receive the portal user logout requests
from the RADIUS server.

#### Solution

On the access device, execute the radius session-control
enable command in system view to enable the
RADIUS session control function.

### Users logged out by the access device still exist on the portal authentication server

#### Symptom

After you log out a portal user on the
access device, the user still exists on the portal authentication server.

#### Analysis

When you execute the portal
delete-user command on the access device to log
out a user, the access device sends an unsolicited logout notification to the
portal authentication server. If the BAS-IP or BAS-IPv6 address carried in the
logout notification is different from the portal device IP address specified on
the portal authentication server, the portal authentication server discards the
logout notification. When sending of the logout notifications times out, the
access device logs out the user. However, the portal authentication server does
not receive the logout notification successfully, and therefore it regards the
user is still online.

#### Solution

Configure the BAS-IP or BAS-IPv6 attribute
on the interface enabled with portal authentication. Make sure the attribute
value is the same as the portal device IP address specified on the portal
authentication server.

### Re-DHCP portal authenticated users cannot log in successfully

#### Symptom

The device performs re-DHCP portal
authentication for users. A user enters the correct username and password, and
the client successfully obtains the private and public IP addresses. However,
the authentication result for the user is failure.

#### Analysis

When the access device detects that the
client IP address is changed, it sends an unsolicited portal packet to notify
of the IP change to the portal authentication server. The portal authentication
server notifies of the authentication success only after it receives the IP
change notification from both the access device and the client.

If the BAS-IP or BAS-IPv6 address carried
in the portal notification packet is different from the portal device IP
address specified on the portal authentication server, the portal
authentication server discards the portal notification packet. As a result, the
portal authentication server considers that the user has failed the
authentication.

#### Solution

Configure the BAS-IP or BAS-IPv6 attribute
on the interface enabled with portal authentication. Make sure the attribute
value is the same as the portal device IP address specified on the portal
authentication server.

