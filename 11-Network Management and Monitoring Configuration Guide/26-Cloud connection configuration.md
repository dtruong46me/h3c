
# Configuring cloud connections

## About cloud connections

A cloud connection is a management
tunnel established between a local device and the H3C cloud server. It enables
you to manage the local device from the cloud server without accessing the
network where the device resides.

### Multiple subconnections

After a local device establishes a
connection with the cloud server, service modules on the local device can
establish multiple subconnections with the microservices on the cloud server. These
subconnections are independent from each other and provide separate
communication channels for different services. This mechanism avoids interference
among different services.

### Cloud connection establishment

The cloud connection is established as
follows:

**1\.**The device sends an authentication request
to the cloud server.

**2\.**The cloud server sends an authentication
success packet to the device.

The device passes the authentication only
if the serial number of the device has been added to the cloud server. If the
authentication fails, the cloud server sends an authentication failure packet
to the device.

**3\.**The device sends a registration request to
the cloud server.

**4\.**The cloud server sends a registration
response to the device.

The registration response contains the uniform
resource locator (URL) used to establish a cloud connection.

**5\.**The device uses the URL to send a handshake
request (changing the protocol from HTTP to WebSocket) to the cloud server.

**6\.**The cloud server sends a handshake response
to the device to finish establishing the cloud connection.

**7\.**The device sends a subconnection URL request
to the cloud server.

**8\.**The cloud server sends a response that
contains the microservice list and all subconnection URLs to the device.

**9\.**The service module registers on the cloud
connection management module.

**10\.**The cloud connection management module sends
the corresponding URLs to the service module.

**11\.**The service module establishes
subconnections with the cloud server.

Figure 1 Establishing a cloud connection

![](https://resource.h3c.com/en/202407/12/20240712_11705844_x_Img_x_png_0_2216131_294551_0.png)

‌

## Configuring a cloud connection

#### About this task

For a device to establish a cloud
connection to the cloud server, perform either of the following tasks:

·     Specify the domain name of the cloud server on
the device through CLI.

·     Configure the device as a DHCP client and the cloud
server as the DHCP server. The device obtains the IP address of the DHCP server
and parses the option 253 field in the DHCP packets to obtain the domain name
of the cloud server. For more information about the option 253 field, see DHCP
configuration in Layer—3
IP Services Configuration Guide.

To establish cloud connections to the cloud
server, a password is required. A device can use either of the following
methods to obtain the password for establishing cloud connections to the cloud
server:

·     Execute the cloud-management server password
command on the device to specify the password for establishing cloud
connections to the cloud server.

·     Configure the device as a DHCP client and the cloud
server as the DHCP server. The device obtains the IP address of the DHCP server
and parses the option 252 field in the DHCP packets to obtain the password for
connection to the cloud server. For more information about the option 252
field, see DHCP configuration in Layer 3—IP Services
Configuration Guide.

After establishing the cloud connection,
the local device sends keepalive packets to the cloud server periodically. If
the device does not receive a response from the cloud server within three keepalive
intervals, the device sends a registration request to re-establish the cloud
connection.

#### Restrictions and guidelines

The domain name obtained through DHCP has a
higher priority than the domain name configured manually.

If a device obtains the domain name of the
cloud server through DHCP after establishing a cloud connection to the cloud
server with the manually configured domain name, the device performs the
following tasks:

·     If the automatically obtained and manually
configured domain names are identical, the device retains the cloud connection.

·     If the automatically obtained and manually
configured domain names are different, the device tears down the cloud
connection and then establishes a cloud connection to the cloud server with the
automatically obtained domain name.

The password obtained through DHCP has a
higher priority than the password configured manually.

If a device obtains the password for
connection to the cloud server through DHCP after establishing a cloud
connection to the cloud server with the manually configured password, the
device performs the following tasks:

·     If the automatically obtained and manually
configured passwords are identical, the device retains the cloud connection.

·     If the automatically obtained and manually configured
passwords are different, the device tears down the cloud connection and then
establishes a cloud connection to the cloud server with the automatically
obtained password.

#### Prerequisites

Add the serial number of the device to be
managed to the cloud server.

Configure DNS to ensure that the domain
name of the cloud server can be translated into an IP address.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Configure the domain name of the cloud
server.

¡     Configure the IPv4 domain name of the cloud server.

cloud-management
server \[ domain domain-name \[ vpn-instance vpn-instance-name ] \[ source-ip ip-address ] \| port port-number ]

¡     Configure the IPv6 domain name of the cloud server.

cloud-management
server ipv6 domain domain-name \[ vpn-instance vpn-instance-name ] \[ source-ip ip-address ]

By default, the domain name of the cloud
server is not configured.

**3\.**(Optional) Set the keepalive interval.

cloud-management
keepalive interval

By default, the keepalive interval is 180
seconds.

**4\.**(Optional) Specify the TCP port number used
to establish cloud connections.

cloud-management
server port port-number

By default, the TCP port number used to
establish cloud connections is 443\.

**5\.**(Optional.) Set the password for
establishing cloud connections to the cloud server.

cloud-management
server password { cipher \| simple } string

By default, no password is set for
establishing cloud connections to the cloud server.

## Unbinding the device from the Oasisserver

#### About this task

A device can be registered on the Oasis
server by only one user.

To register a device that has been
registered by another user, you need to take the following steps:

**1\.**Obtain a verification code for device
unbinding from the Oasis server.

**2\.**Execute the command on the device for
sending the verification code to the Oasis server.

**3\.**Register the device on the Oasis server.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Send the verification code for device
unbinding to the Oasis server.

cloud-management
unbinding-code code

## Display and maintenance commands for cloud connections

Execute display commands in any view and reset
commands in user view.

 

| Task | Command |
| --- | --- |
| Display cloud connection state information. | display cloud-management state || Re-establish the cloud connection to the cloud server. | reset cloud-management tunnel |


‌

## Cloud connection configuration examples

### Example: Configuring a cloud connection

#### Network configuration

As shown in [Figure 2](#_Ref486942521),
configure the device to establish a cloud connection with the cloud server.

Figure 2 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705845_x_Img_x_png_1_2216131_294551_0.png)

‌

#### Procedure

**1\.**Configure IP addresses for interfaces, and
configure a routing protocol to ensure that the devices can reach each other.
(Details not shown.)

**2\.**Log in to the cloud server to add the serial
number of the device to the server. (Details not shown.)

The IP address of the cloud server is 10.1.1.1/24
and the domain name is cloud.com.

**3\.**Configure the domain name of the cloud
server as cloud.com.

\<Device\> system-view

\[Device] cloud-management
server domain cloud.com

\# Map IP address 10.1.1.1 to host name cloud.com.

\[Device] ip host cloud.com
10.1.1.1

#### Verifying the configuration

\# Verify that the device and the cloud
server have established a cloud connection.

\[Device] display cloud-management
state

Cloud connection state   :
Established

Device state             : Request\_success

Cloud server address     : 10.1.1.1

Cloud server domain name : cloud.com

Cloud server port        : 443

Connected at             : Wed Jan  27
14:18:40 2019

Duration                 : 00d 00h 02m
01s

Process state            : Message received

Failure reason           : N/A

