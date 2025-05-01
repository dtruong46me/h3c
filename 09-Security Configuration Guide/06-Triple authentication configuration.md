
# Configuring triple authentication

## About triple authentication

Triple authentication enables an access
port to perform Web, MAC, and 802.1X authentication. A terminal can access the
network if it passes one type of authentication. For more information about
802.1X authentication, MAC authentication, and Web authentication, see
"Configuring 802.1X authentication", "Configuring MAC
authentication", and "Configuring Web authentication."

### Typical network of triple authentication

Triple authentication is suitable for a LAN
that comprises terminals that require different authentication services, as
shown in [Figure 1](#_Ref248406006). The triple authentication-enabled access port can perform MAC
authentication for the printer, 802.1X authentication for the PC installed with the 802.1X client, and Web authentication for the Web
user. 

Figure 1 Triple authentication network
diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705393_x_Img_x_png_0_2216067_294551_0.png)

‌

### Triple authentication mechanism

The three types of authentication are
triggered by different packets:

·     The access port performs MAC authentication for
a terminal when it receives an ARP or DHCP broadcast packet from the terminal
for the first time. If the terminal passes MAC authentication, the terminal can
access the network. If the MAC authentication fails, the access port performs 802.1X
or Web authentication.

·     The access port performs 802.1X authentication
when it receives an EAP packet from an 802.1X client or a third-party client.
If the unicast trigger feature of 802.1X is enabled on the access port, any
packet from the client can trigger an 802.1X authentication. 

·     The access port performs Web authentication when
it receives an HTTP packet from a terminal.

If a terminal triggers different types of authentication,
the authentications are processed at the same time. The failure of one type of
authentication does not affect the others. When a terminal passes one type of
authentication, the other types of authentication are processed as follows:

·     If the terminal first passes MAC authentication,
Web authentication is terminated immediately, but 802.1X authentication will
proceed. If the terminal also passes 802.1X authentication, the 802.1X authentication
information will overwrite the MAC authentication information for the terminal.
If the terminal fails 802.1X authentication, the user stays online as a MAC
authentication user, and only 802.1X authentication can be triggered again.

·     If the terminal first passes 802.1X or Web
authentication, the other types of authentication are terminated immediately
and cannot be triggered again. 

### Triple authentication support for VLAN assignment

#### Authorization VLAN

After a user passes authentication, the authentication
server assigns an authorization VLAN to the access port for the user. The user can
then access the network resources in the authorized VLAN.

#### Authentication failure VLAN

The access port adds a user to an
authentication failure VLAN configured on the port after the user fails
authentication.

·     For an 802.1X authentication user—Adds the user
to the Auth-Fail VLAN configured for 802.1X authentication.

·     For a Web authentication user—Adds the user to the
Auth-Fail VLAN configured for Web authentication.

·     For a MAC authentication user—Adds the user to
the guest VLAN configured for MAC authentication.

If a user in the MAC authentication guest
VLAN fails 802.1X authentication, the user is moved to the 802.1X Auth-Fail
VLAN.

#### Server-unreachable VLAN

If a user fails authentication due to the
unreachable server, the access port adds the user to an server-unreachable
VLAN.

·     For an 802.1X authentication user—Adds the user
to the critical VLAN configured for 802.1X authentication.

·     For a Web authentication user—Adds the user to the
Auth-Fail VLAN configured for Web authentication.

·     For a MAC authentication user—Adds the user to
the critical VLAN configured for MAC authentication.

The access port supports configuring all
types of server-unreachable VLANs at the same time. A user is added to the server-unreachable
VLAN as follows:

·     If the user does not undergo 802.1X
authentication, the user is added to the server-unreachable VLAN configured for
the last authentication.

·     If the user in the Web Auth-Fail VLAN or MAC
authentication critical VLAN also fails 802.1X authentication, the user is added
to the 802.1X authentication critical VLAN.

### Triple authentication support for ACL authorization

After a user passes authentication, the authentication
server assigns an authorization ACL to the access port for the user. The access
port uses the ACL to filter traffic for the user.

To use ACL authorization, you must specify
authorization ACLs on the authentication server and configure the ACLs on the
access device. You can change the user's access authorization by changing the
authorization ACL on the authentication server or changing rules of the
authorization ACL on the access device. 

### Triple authentication support for onlineuser detection

You can configure the following features to
detect the online status of users:

·     Enable online user detection for Web
authentication users.

·     Enable the online user handshake or periodic online
user reauthentication feature for 802.1X users.

·     Enable offline detection for MAC authentication users.

## Restrictions and guidelines: Triple authentication

In triple authentication, 802.1X
authentication must use the MAC-based
access control method.

If Web authentication is enabled on a port,
configure the subnets of the server-unreachable VLANs of the port as Web
authentication-free subnets. This ensures that an authentication-failed user
can access the server-unreachable VLAN.

Do not configure both Web
authentication-free IPs and 802.1X free IPs. If you do so, only 802.1X free IPs
take effect.

Web authentication cannot be configured in
conjunction with the following settings:

·     Guest VLAN, Auth-Fail VLAN, and critical VLAN of
802.1 X authentication.

·     Guest VLAN and critical VLAN of MAC
authentication.

## Triple authentication tasks at a glance

Choose the following tasks as needed:

·     Configure 802.1X authentication

For more information, see "Configuring
802.1X."

·     Configure MAC authentication

For more information, see "Configuring
MAC authentication."

·     Configure Web authentication

For more information, see "Configuring
Web authentication."

In triple authentication, if the above
three authentication methods are configured, use the port-security
triple-auth-order mac-dot1x-web command to
specify the authentication order as MAC authentication, 802.1X authentication,
and Web authentication. For more information about the port-security triple-auth-order
mac-dot1x-web command, see port security
commands in Security Command Reference.

## Triple authentication configuration examples

### Example: Configuring basic triple authentication

#### Network configuration

As shown in [Figure 2](#_Ref351193370),
the terminals are connected to the device to access the IP network. Configure
triple authentication on the device's Layer 2 interface that connects to the
terminals. A terminal passing one of the three authentication methods, 802.1X
authentication, Web authentication, and MAC authentication, can access the IP
network. 

·     Assign IP addresses on subnet 192.168.1.0/24 to the
terminals.

·     Use the remote RADIUS server to perform
authentication, authorization, and accounting. Configure the device to send
usernames carrying no ISP domain names to the RADIUS server.

·     Configure the local Web authentication server on
the device to use listening IP address 4.4.4.4. Configure the device to send a
default authentication page to the Web user and forward authentication data by using
HTTP.

Figure 2 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705394_x_Img_x_png_1_2216067_294551_0.png) 

‌

#### Procedure

**1\.**Make sure that the terminals, the server,
and the device can reach each other. (Details not shown.)

**2\.**Configure the RADIUS server to provide
normal authentication, authorization, and accounting for users. In this
example, configure the following on the RADIUS server:

¡     An
802.1X user with username userdot.

¡     A
Web authentication user with username userpt.

¡     A
MAC authentication user with a username and password both being the MAC address
of the printer f07d6870725f.

**3\.**Configure Web authentication:

\# Configure VLANs and IP addresses for
the VLAN interfaces, and add ports to specific VLANs. (Details not shown.)

\# Create an HTTP-based local portal Web
service, and specify file defaultfile.zip as the default authentication page file of the local portal Web service.
(Make sure the authentication page file already exists in the root directory of
the storage medium on the device.)

\<Device\> system-view

\[Device] portal
local-web-server http

\[Device-portal-local-websvr-http]
default-logon-page defaultfile.zip

\[Device-portal-local-websvr-http]
quit

\# Assign IP address 4.4.4.4 to Loopback
0\.

\[Device] interface loopback 0

\[Device-LoopBack0] ip address
4.4.4.4 32

\[Device-LoopBack0] quit

\# Create a Web authentication server named
webserver and enter its view.

\[Device] web-auth server
webserver

\# Configure the redirection URL for the
Web authentication server as http://4.4.4.4/portal/.

\[Device-web-auth-server-webserver]
url http://4.4.4.4/portal/

\# Specify 4.4.4.4 as the IP address and 80
as the port number of the Web authentication server.

\[Device-web-auth-server-webserver]
ip 4.4.4.4 port 80

\[Device-web-auth-server-webserver]
quit 

\# Enable Web authentication on Ten-GigabitEthernet 1/0/1 and specify Web
authentication server webserver on the interface.

\[Device] interface ten-gigabitethernet 1/0/1

\[Device–Ten-GigabitEthernet1/0/1] web-auth enable
apply server webserver

\[Device–Ten-GigabitEthernet1/0/1] quit

**4\.**Configure 802.1X authentication:

\# Enable 802.1X authentication globally.

\[Device] dot1x

\# Enable 802.1X authentication (MAC-based
access control required) on Ten-GigabitEthernet 1/0/1.

\[Device] interface ten-gigabitethernet 1/0/1 

\[Device–Ten-GigabitEthernet1/0/1] dot1x port-method
macbased

\[Device–Ten-GigabitEthernet1/0/1] dot1x

\[Device–Ten-GigabitEthernet1/0/1] quit

**5\.**Configure MAC authentication:

\# Enable MAC authentication globally.

\[Device] mac-authentication 

\# Enable MAC authentication on Ten-GigabitEthernet 1/0/1.

\[Device] interface ten-gigabitethernet 1/0/1

\[Device–Ten-GigabitEthernet1/0/1] mac-authentication

\[Device–Ten-GigabitEthernet1/0/1] quit

**6\.**Configure a RADIUS scheme:

\# Create a RADIUS scheme named rs1.

\[Device] radius scheme rs1

\# Specify the primary authentication and accounting
servers and keys.

\[Device-radius-rs1] primary
authentication 1.1.1.2

\[Device-radius-rs1] primary
accounting 1.1.1.2

\[Device-radius-rs1] key
authentication simple radius

\[Device-radius-rs1] key
accounting simple radius

\# Specify usernames sent to the RADIUS
server to carry no domain names.

\[Device-radius-rs1]
user-name-format without-domain

\[Device-radius-rs1] quit

**7\.**Configure an ISP domain:

\# Create an ISP domain named triple.

\[Device] domain triple

\# Configure the domain to use RADIUS scheme
rs1 for authentication, authorization and accounting of LAN access
users.

\[Device-isp-triple]
authentication lan-access radius-scheme rs1

\[Device-isp-triple]
authorization lan-access radius-scheme rs1

\[Device-isp-triple] accounting
lan-access radius-scheme rs1

\[Device-isp-triple] quit

\# Configure domain triple as the
default domain. If a username entered by a user includes no ISP domain name,
the AAA method of the default domain is used.

\[Device] domain default enable
triple

#### Verifying the configuration

**1\.**Verify that the Web user can pass Web authentication.

\# On the Web user terminal, use a Web
browser to access an external network and then enter the correct username and
password on the authentication page http://4.4.4.4/portal/logon.html.
(Details not shown.) 

\# Display information about online Web
authentication users.

\[Device] display web-auth user

  Total online web-auth users:
1

User Name: localuser

  MAC address: acf1-df6c-f9ad

  Access interface: Ten-GigabitEthernet1/0/1

  Initial VLAN: 8

  Authorization VLAN: N/A

  Authorization ACL ID: N/A

  Authorization user profile:
N/A 

**2\.**Verify that the printer can pass MAC
authentication.

\# Connect the printer to the network.
(Details not shown.)

\# Display information about online MAC authentication
users.

\[Device] display
mac-authentication connection

Total connections: 1

Slot ID: 1

User MAC address:
f07d-6870-725f

Access interface: Ten-GigabitEthernet1/0/1

Username: f07d6870725f

User access state: Successful

Authentication domain: triple

Initial VLAN: 8

Authorization untagged VLAN: N/A

Authorization tagged VLAN: N/A

Authorization VSI: N/A

Authorization ACL ID: N/A

Authorization user profile:
N/A

Authorization CAR: N/A

Authorization URL: N/A

Termination action: Default

Session timeout period: N/A

Online from: 2015/01/04
18:01:43

Online duration: 0h 0m 2s

**3\.**Verify that the 802.1X client can pass
802.1X authentication.

\# On the 802.1X client, initiate 802.1X
authentication and then enter the correct username and password. (Details not
shown.)

\# Display information about online 802.1X
users.

\[Device] display dot1x connection

Total connections: 1

Slot ID: 1

User MAC address:
7446-a091-84fe

Access interface: Ten-GigabitEthernet1/0/1

Username: userdot

User access state: Successful

Authentication domain: triple

IPv4 address: 192.168.1.2

Authentication method: CHAP

Initial VLAN: 8

Authorization untagged VLAN: N/A

Authorization tagged VLAN
list: N/A

Authorization VSI: N/A

Authorization ACL ID: N/A

Authorization user profile:
N/A

Authorization CAR: N/A

Authorization URL: N/A

Termination action: Default

Session timeout period: N/A

Online from: 2015/01/04
18:13:01

Online duration: 0h 0m 14s

### Example: Configuring triple authentication to support authorization VLAN and authentication failure VLAN

#### Network configuration

As shown in [Figure 3](#_Ref249759145), the
terminals are connected to the device to access the IP network. Configure
triple authentication on the device's Layer 2 interface connected to the
terminals. A terminal passing one of the three authentication methods, 802.1X
authentication, Web authentication, and MAC authentication, can access the IP
network. 

·     The Web authentication terminal uses DHCP to get
an IP address in 192.168.1.0/24 before authentication and in 3.3.3.0/24 after
passing authentication. If the terminal fails authentication, it requests IP
addresses in 2.2.2.0/24 through DHCP.

You can use the access device or an
attached device as the DHCP server. In this example, the access device (the device)
provides the DHCP service.

·     The 802.1X terminal uses DHCP to get an IP
address in 192.168.1.0/24 before authentication and in 3.3.3.0/24 after passing
authentication. If the terminal fails authentication, it requests IP addresses
in 2.2.2.0/24 through DHCP.

·     After passing authentication, the printer
obtains IP address 3.3.3.111/24 that is bound with its MAC address through
DHCP.

·     Use the remote RADIUS server to perform
authentication, authorization, and accounting. Configure the device to remove
the ISP domain names from usernames sent to the RADIUS server.

·     Configure the local Web authentication server on
the device to use listening IP address 4.4.4.4. Configure the device to forward
authentication data by using HTTP.

·     Configure VLAN 3 as the authorization VLAN.
Users passing authentication are added to this VLAN.

·     Configure VLAN 2 as the authentication failure
VLAN. Users failing authentication are added to this VLAN.

Figure 3 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705395_x_Img_x_png_2_2216067_294551_0.png)

‌

#### Procedure

**1\.**Make sure the terminals, the servers, and
the device can reach each other. (Details not shown.)

**2\.**Configure the RADIUS server to provide
normal authentication, authorization, and accounting for users. In this
example, configure the following on the RADIUS server:

¡     An
802.1X user with username userdot.

¡     A
Web authentication user with username userpt.

¡     A
MAC authentication user with a username and password both being the MAC address
of the printer f07d6870725f. 

¡     An
authorization VLAN (VLAN 3).

**3\.**Configure the IP address of the update server
as an authentication-free IP address.

\<Device\> system-view

\[Device] web-auth free-ip
2.2.2.2 24

**4\.**Configure DHCP:

\# Configure VLANs and IP addresses for
the VLAN interfaces, and add ports to specific VLANs. (Details not shown.)

\# Enable DHCP.

\[Device] dhcp enable

\# Exclude the IP address of the update
server from dynamic address assignment.

\[Device] dhcp server
forbidden-ip 2.2.2.2

\# Configure DHCP address pool 1 to assign
IP addresses and other configuration parameters to clients on subnet 192.168.1.0.

\[Device] dhcp server ip-pool 1

\[Device-dhcp-pool-1] network
192.168.1.0 mask 255.255.255.0

\[Device-dhcp-pool-1] expired
day 0 hour 0 minute 1

\[Device-dhcp-pool-1] gateway-list
192.168.1.1

\[Device-dhcp-pool-1] quit

\# Configure DHCP address pool 2 to assign
IP address and other configuration parameters to clients on subnet 2.2.2.0. 

\[Device] dhcp server ip-pool 2

\[Device-dhcp-pool-2] network 2.2.2.0
mask 255.255.255.0

\[Device-dhcp-pool-2] expired
day 0 hour 0 minute 1

\[Device-dhcp-pool-2] gateway-list
2.2.2.1

\[Device-dhcp-pool-2] quit

\# Configure DHCP address pool 3 to assign
IP address and other configuration parameters to clients on subnet 3.3.3.0. 

\[Device] dhcp server ip-pool 3

\[Device-dhcp-pool-3] network 3.3.3.0
mask 255.255.255.0

\[Device-dhcp-pool-3] expired
day 0 hour 0 minute 1

\[Device-dhcp-pool-3] gateway-list
3.3.3.1

\[Device-dhcp-pool-3] quit

\# Configure DHCP address pool 4, and bind
the printer's MAC address f07d-6870-725f to IP address 3.3.3.111/24 in this
address pool.

\[Device] dhcp server ip-pool 4

\[Device-dhcp-pool-4] static-bind
ip-address 3.3.3.111 mask 255.255.255.0 client-identifier f07d-6870-725f

\[Device-dhcp-pool-4] quit

**5\.**Configure Web authentication:

\# Create an HTTP-based local portal Web
service, and specify file defaultfile.zip as the default authentication page file of the local portal Web service.
(Make sure the authentication page file already exists in the root directory of
the storage medium on the device.)

\[Device] portal local-web-server
http

\[Device-portal-local-websvr-http]
default-logon-page defaultfile.zip

\[Device-portal-local-websvr-http]
quit

\# Assign IP address 4.4.4.4 to Loopback
0\.

\[Device] interface loopback 0

\[Device-LoopBack0] ip address 4.4.4.4
32

\[Device-LoopBack0] quit

\# Create a Web authentication server
named webserver.

\[Device] web-auth server webserver

\# Configure the redirection URL of the
Web authentication server as http://4.4.4.4/portal/.

\[Device-web-auth-server-webserver]
url http://4.4.4.4/portal/

\# Specify 4.4.4.4 as the IP address and 80
as the port number of the Web authentication server.

\[Device-web-auth-server-webserver]
ip 4.4.4.4 port 80

\[Device-web-auth-server-webserver]
quit

\# Configure the IP address of the update server as an authentication-free
IP address.

\[Device] web-auth free-ip
2.2.2.2 24

\# Enable Web authentication on Ten-GigabitEthernet 1/0/1 and specify VLAN 2
as the Auth-Fail VLAN.

\[Device] interface ten-gigabitethernet 1/0/1

\[Device–Ten-GigabitEthernet1/0/1] port link-type
hybrid

\[Device–Ten-GigabitEthernet1/0/1] mac-vlan enable

\[Device–Ten-GigabitEthernet1/0/1] web-auth enable
apply server webserver

\[Device–Ten-GigabitEthernet1/0/1] web-auth auth-fail
vlan 2

\[Device–Ten-GigabitEthernet1/0/1] quit

**6\.**Configure 802.1X authentication:

\# Enable 802.1X authentication globally.

\[Device] dot1x

\# Enable 802.1X authentication (MAC-based
access control required) on Ten-GigabitEthernet 1/0/1, and specify VLAN
2 as the Auth-Fail VLAN.

\[Device] interface ten-gigabitethernet 1/0/1

\[Device–Ten-GigabitEthernet1/0/1] dot1x port-method macbased

\[Device–Ten-GigabitEthernet1/0/1] dot1x

\[Device–Ten-GigabitEthernet1/0/1] dot1x auth-fail
vlan 2

\[Device–Ten-GigabitEthernet1/0/1] quit

**7\.**Configure MAC authentication:

\# Enable MAC authentication globally.

\[Device] mac-authentication

\# Enable MAC authentication on Ten-GigabitEthernet 1/0/1, and specify VLAN 2
as the guest VLAN.

\[Device] interface ten-gigabitethernet 1/0/1

\[Device–Ten-GigabitEthernet1/0/1] mac-authentication

\[Device–Ten-GigabitEthernet1/0/1] mac-authentication
guest-vlan 2

\[Device–Ten-GigabitEthernet1/0/1] quit

**8\.**Configure a RADIUS scheme:

\# Create a RADIUS scheme named rs1.

\[Device] radius scheme rs1

\# Specify the primary authentication and accounting
servers and keys.

\[Device-radius-rs1] primary
authentication 1.1.1.2

\[Device-radius-rs1] primary
accounting 1.1.1.2

\[Device-radius-rs1] key
authentication simple radius

\[Device-radius-rs1] key
accounting simple radius

\# Specify usernames sent to the RADIUS
server to carry no domain names.

\[Device-radius-rs1] user-name-format
without-domain

\[Device-radius-rs1] quit

**9\.**Configure an ISP domain:

\# Create an ISP domain named triple.

\[Device] domain triple

\# Configure the domain to use RADIUS scheme
rs1 for authentication, authorization and accounting of LAN access users.


\[Device-isp-triple] authentication
lan-access radius-scheme rs1

\[Device-isp-triple] authorization
lan-access radius-scheme rs1

\[Device-isp-triple] accounting
lan-access radius-scheme rs1

\[Device-isp-triple] quit

\# Configure domain triple as the
default domain. If a username entered by a user includes no ISP domain name,
the AAA methods of the default domain is used.

\[Device] domain default enable
triple

#### Verifying the configuration

**1\.**Verify that the Web user can pass Web
authentication.

\# On the Web user terminal, use a Web
browser to access an external network and then enter the correct username and
password on the authentication page http://4.4.4.4/portal/logon.html. (Details
not shown.)

\# Use the display
web-auth user command to display information
about online users.

\[Device] display web-auth user

  Total online web-auth users:
1

 

User Name: userpt

  MAC address: 6805-ca17-4a0b

  Access interface: Ten-GigabitEthernet1/0/1

  Initial VLAN: 8

  Authorization VLAN: 3

  Authorization ACL ID: N/A

  Authorization user profile:
N/A

**2\.**Verify that the printer can pass MAC
authentication.

\# Connect the printer to the network.
(Details not shown.)

\# Display information about online MAC
authentication users.

\[Device] display
mac-authentication connection

Total connections: 1

Slot ID: 1

User MAC address:
f07d-6870-725f

Access interface: Ten-GigabitEthernet1/0/1

Username: f07d6870725f

User access state: Successful

Authentication domain: triple

Initial VLAN: 8

Authorization untagged VLAN: 3

Authorization tagged VLAN: N/A

Authorization VSI: N/A

Authorization ACL ID: N/A

Authorization user profile:
N/A

Authorization CAR: N/A

Authorization URL: N/A

Termination action: Default

Session timeout period: N/A

Online from: 2015/01/04
18:01:43

Online duration: 0h 0m 2s

**3\.**Verify that the 802.1X user can pass 802.1X
authentication.

\# On the 802.1X client, initiate 802.1X authentication
and enter the correct username and password. (Details not shown.)

\# Display information about online 802.1X
users.

\[Device] display dot1x connection

Total connections: 1

Slot ID: 1

User MAC address:
7446-a091-84fe

Access interface: Ten-GigabitEthernet1/0/1

Username: userdot

User access state: Successful

Authentication domain: triple

IPv4 address: 3.3.3.3

Authentication method: CHAP

Initial VLAN: 8

Authorization untagged VLAN: 3

Authorization tagged VLAN
list: N/A

Authorization VSI: N/A

Authorization ACL ID: N/A

Authorization user profile:
N/A

Authorization CAR: N/A

Authorization URL: N/A

Termination action: Default

Session timeout period: N/A

Online from: 2015/01/04
18:13:01

Online duration: 0h 0m 14s

**4\.**Verify that users that pass authentication
have been assigned authorization VLANs.

\# Display MAC-VLAN entries of online
users.

\[Device] display mac-vlan all

  The following MAC VLAN
addresses exist:

  S:Static  D:Dynamic

  MAC ADDR        
MASK             VLAN ID   PRIO   STATE

 
\--------------------------------------------------------

  6805-ca17-4a0b  
ffff-ffff-ffff   3         0      D

  f07d-6870-725f  
ffff-ffff-ffff   3         0      D

  7446-a091-84fe  
ffff-ffff-ffff   3         0      D

  Total MAC VLAN address
count:3 

**5\.**Verify that online users have been assigned
IP addresses.

\[Device] display dhcp server
ip-in-use

IP address      
Client-identifier/    Lease expiration          Type

                  Hardware
address

 3.3.3.111       
01f0-7d68-7072-5f     Jan  4 18:14:17 2015      Auto:(C)

 3.3.3.2         
0168-05ca-174a-0b     Jan  4 18:15:01 2015      Auto:(C)

 3.3.3.3         
0174-46a0-9184-fe     Jan  4 18:15:03 2015      Auto:(C)

**6\.**When a terminal fails authentication, it is
added to VLAN 2\. You can use the previous display commands to display the MAC-VLAN
entry and IP address of the terminal. (Details not shown.)

