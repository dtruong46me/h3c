
# Configuring NQA

## About NQA

Network quality analyzer (NQA) allows you
to measure network performance, verify the service levels for IP services and
applications, and troubleshoot network problems.

### NQA operating mechanism

An NQA operation contains a set of
parameters such as the operation type, destination IP address, and port number to
define how the operation is performed. Each NQA operation is identified by the
combination of the administrator name and the operation tag. You can configure
the NQA client to run the operations at scheduled time periods.

As shown in [Figure 1](#_Ref317061159),
the NQA source device (NQA client) sends data to the NQA destination device by
simulating IP services and applications to measure network performance.

All types of NQA operations require the NQA
client, but only the TCP, UDP echo, UDP jitter, and voice operations require
the NQA server. The NQA operations for services that are already provided by
the destination device such as FTP do not need the NQA server. You can configure
the NQA server to listen and respond to specific IP addresses and ports to meet
various test needs.

Figure 1 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705662_x_Img_x_png_0_2216108_294551_0.png)

‌

After starting an NQA operation, the NQA client
periodically performs the operation at the interval specified by using the frequency command.

You can set the number of probes the NQA
client performs in an operation by using the probe count command. For the voice and path jitter operations, the NQA client
performs only one probe per operation and the probe count command is not available.

### Collaboration with Track

NQA can collaborate with the Track module
to notify application modules of state or performance changes so that the
application modules can take predefined actions.

The NQA \+ Track collaboration
is available for the following application modules:

The NQA \+ Track collaboration is available
for the following application modules:

·     VRRP.

·     Static routing.

·     Policy-based routing.

·     Traffic redirecting.

·     Smart Link. 

The following describes how a static route destined
for 192.168.0.88 is monitored through collaboration:

**1\.**NQA monitors the reachability to
192.168.0.88.

**2\.**When 192.168.0.88 becomes unreachable, NQA notifies
the Track module of the change.

**3\.**The Track module notifies the static routing
module of the state change.

**4\.**The static routing module sets the static
route to invalid according to a predefined action.

For more information about collaboration, see
High Availability Configuration Guide.

### Threshold monitoring

Threshold monitoring enables the NQA client
to take a predefined action when the NQA operation performance metrics violate
the specified thresholds.

[Table 1](#_Ref251085855)
describes the relationships between performance metrics and NQA operation
types.

Table 1 Performance metrics and NQA
operation types

| Performance metric | NQA operation types that can gather the metric |
| --- | --- |
| Probe duration | All NQA operation types except UDP jitter, UDP tracert, path jitter, and voice || Number of probe failures | All NQA operation types except UDP jitter, UDP tracert, path jitter, and voice || Round-trip time | ICMP jitter, UDP jitter, and voice || Number of discarded packets | ICMP jitter, UDP jitter, and voice || One-way jitter (source-to-destination or destination-to-source) | ICMP jitter, UDP jitter, and voice || One-way delay (source-to-destination or destination-to-source) | ICMP jitter, UDP jitter, and voice || Calculated Planning Impairment Factor (ICPIF) (see "[Configuring the voice operation](#_Ref383080080)") | Voice || Mean Opinion Scores (MOS) (see "[Configuring the voice operation](#_Ref383080103)") | Voice |








‌

### NQA templates

An NQA template is a set of parameters (such
as destination address and port number) that defines how an NQA operation is
performed. Features such as load balancing and health monitoring can use the
NQA template to collect statistics.

You can create multiple NQA templates on the
NQA client. Each template must be identified by a unique template name.

## Restrictions and guidelines:NQA configuration

To avoid probe failures, follow these
restrictions and guidelines when configuring the listening ports on the NQA
client and NQA server:

·     Do not specify a well-known port.

·     Make sure the specified port number is not used
by any services on the device.

¡     To
obtain the IPv4 addresses and the port numbers in use on this device, see the Local Addr:port field in the output from the display tcp and display udp commands. 

¡     To
obtain the IPv6 addresses and the port numbers in use on this device, see the LAddr-\>port field in the output from the display ipv6
tcp and display ipv6 udp commands. 

The destination port configured for the
operation (with the destination port
command) on the NQA client must be the same as the listening port configured on
the server. 

For latency operation results to be
accurate, synchronize time between the source and the destination with an
accuracy higher than or equal to the operation result calculation accuracy.

## NQA tasks at a glance

To configure NQA, perform the following
tasks:

**1\.**[Configuring the NQA server](#_Ref345588597)

Perform this task on the destination
device before you configure the TCP, UDP echo, UDP jitter, and voice
operations.

**2\.**[Enabling the NQA client](#_Ref345589392)

**3\.**Configuring NQA operations or NQA templates

Choose the following tasks as needed:

¡     [Configuring NQA operations on the NQA
client](#_Ref345596005)

¡     [Configuring NQA templates on the NQA
client](#_Ref345598023)

After you configure an NQA operation, you
can schedule the NQA client to run the NQA operation.

An NQA template does not run immediately
after it is configured. The template creates and run the NQA operation only
when it is required by the feature to which the template is applied.

## Configuring the NQA server

#### Restrictions and guidelines

To perform TCP, UDP echo, UDP jitter, and
voice operations, you must configure the NQA server on the destination device.
The NQA server listens and responds to requests on the specified IP addresses
and ports.

You can configure multiple TCP or UDP
listening services on an NQA server, where each corresponds to a specific IP
address and port number. 

The IP address, port number, and VPN
instance for a listening service must be unique on the NQA server and match the
configuration on the NQA client.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enable the NQA server.

nqa server enable

By default, the NQA server is disabled.

**3\.**Configure a TCP listening service.

nqa server tcp-connect ip-address
port-number \[ vpn-instance vpn-instance-name ] \[ tos tos ]

This task is required for only TCP and
DLSw operations. For the DLSw operation, the port number for the TCP listening
service must be 2065\.

**4\.**Configure a UDP listening service.

nqa server udp-echo ip-address
port-number \[ vpn-instance vpn-instance-name ] \[ tos tos ]

This task is required for only UDP echo,
UDP jitter, and voice operations.

## Enabling the NQA client

**1\.**Enter system view.

system-view

**2\.**Enable the NQA client.

nqa agent enable

By default, the NQA client is enabled.

The NQA client configuration takes effect
after you enable the NQA client.

## Configuring NQA operations on the NQA client

### NQA operations tasks at a glance

To configure NQA operations, perform the
following tasks:

**1\.**Configuring an NQA operation

¡     [Configuring the ICMP echo operation](#_Ref345595559)

¡     [Configuring the ICMP jitter operation](#_Ref429640656)

¡     [Configuring the DHCP operation](#_Ref326594940)

¡     [Configuring the DNS operation](#_Ref326594942)

¡     [Configuring the FTP operation](#_Ref326594946)

¡     [Configuring the HTTP operation](#_Ref326594950)

¡     [Configuring the UDP jitter operation](#_Ref352779193)

¡     [Configuring the SNMP operation](#_Ref326594959)

¡     [Configuring the TCP operation](#_Ref326594960)

¡     [Configuring the UDP echo operation](#_Ref326594963)

¡     [Configuring the UDP tracert operation](#_Ref382917803)

¡     [Configuring the voice operation](#_Ref383080080)

¡     [Configuring the DLSw operation](#_Ref326594970)

¡     [Configuring the path jitter operation](#_Ref336259244)

**2\.**(Optional.) [Configuring optional parameters for the
NQA operation](#_Ref418696592)

**3\.**(Optional.) [Configuring the collaboration feature](#_Ref326594976)

**4\.**(Optional.) [Configuring threshold monitoring](#_Ref326594981)

**5\.**(Optional.) [Configuring the NQA statistics collection
feature](#_Ref418621995)

**6\.**(Optional.) [Configuring the saving of NQA history
records](#_Ref213583760)

**7\.**[Scheduling the NQA operation on the NQA
client](#_Ref345596155)

### Configuring the ICMP echo operation

#### About this task

The ICMP echo operation measures the
reachability of a destination device. It has the same function as the ping command, but provides more output information. In addition, if
multiple paths exist between the source and destination devices, you can
specify the next hop for the ICMP echo operation.

The ICMP echo operation sends an ICMP echo
request to the destination device per probe.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Create an NQA operation and enter NQA
operation view.

nqa entry admin-name operation-tag

**3\.**Specify the ICMP echo type and enter its
view.

type icmp-echo

**4\.**Specify the destination IP address for ICMP
echo requests.

IPv4:

destination ip ip-address

IPv6:

destination ipv6 ipv6-address

By default, no destination IP address is specified.

**5\.**Specify the source IP address for ICMP echo
requests. Choose one option as needed:

¡     Use
the IP address of the specified interface as the source IP address.

source interface interface-type interface-number

By default, the source IP address of ICMP
echo requests is the primary IP address of their output interface.

The specified source interface must be
up.

¡     Specify
the source IPv4 address.

source ip ip-address

By default, the source IPv4 address of
ICMP echo requests is the primary IPv4 address of their output interface.

The specified source IPv4 address must be
the IPv4 address of a local interface, and the interface must be up. Otherwise,
no probe packets can be sent out. 

¡     Specify
the source IPv6 address.

source ipv6 ipv6-address

By default, the source IPv6 address of
ICMP echo requests is the primary IPv6 address of their output interface.

The specified source IPv6 address must be
the IPv6 address of a local interface, and the interface must be up. Otherwise,
no probe packets can be sent out.

**6\.**Specify the output interface or the next hop
IP address for ICMP echo requests. Choose one option as needed:

¡     Specify
the output interface for ICMP echo requests.

out interface interface-type interface-number

By default, the output interface for ICMP
echo requests is not specified. The NQA client determines the output interface
based on the routing table lookup.

¡     Specify
the next hop IPv4 address.

next-hop ip ip-address

By default, no next hop IPv4 address is specified.


¡     Specify
the next hop IPv6 address.

next-hop ipv6 ipv6-address

By default, no next hop IPv6 address is specified.

**7\.**(Optional.) Set the payload size for each
ICMP echo request.

data-size size

The default payload size is 100 bytes.

**8\.**(Optional.) Specify the payload fill string
for ICMP echo requests.

data-fill string

The default payload fill string is the
hexadecimal string 00010203040506070809. 

### Configuring the ICMP jitter operation

#### About this task

The ICMP jitter operation measures
unidirectional and bidirectional jitters. The operation result helps you to determine
whether the network can carry jitter-sensitive services such as real-time voice
and video services.

The ICMP jitter operation works as follows:

**1\.**The NQA client sends ICMP packets to the
destination device.

**2\.**The destination device time stamps each
packet it receives, and then sends the packet back to the NQA client.

**3\.**Upon receiving the responses, the NQA client
calculates the jitter according to the timestamps. 

The ICMP jitter operation sends a number of
ICMP packets to the destination device per probe. The number of packets to send
is determined by using the probe packet-number
command.

#### Restrictions and guidelines

The display nqa history command does not display the results or statistics of the ICMP
jitter operation. To view the results or statistics of the operation, use the display nqa
result or display nqa statistics command.

Before starting the operation, make sure
the network devices are time synchronized by using NTP. For more information about
NTP, see "Configuring NTP."

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Create an NQA operation and enter NQA
operation view.

nqa entry admin-name operation-tag

**3\.**Specify the ICMP jitter type and enter its
view.

type icmp-jitter

**4\.**Specify the destination IP address for ICMP packets.

destination ip ip-address

By default, no destination IP address is specified.

**5\.**Set the number of ICMP packets sent per
probe.

probe packet-number number

The default setting is 10\.

**6\.**Set the interval for sending ICMP packets.

probe packet-interval interval

The default setting is 20 milliseconds.

**7\.**Specify how long the NQA client waits for a
response from the server before it regards the response times out.

probe packet-timeout timeout

The default setting is 3000 milliseconds.

**8\.**Specify the source IP address for ICMP
packets.

source ip ip-address

By default, the source IP address of ICMP
packets is the primary IP address of their output interface.

The source IP address must be the IP
address of a local interface, and the interface must be up. Otherwise, no ICMP
packets can be sent out.

### Configuring the DHCP operation

#### About this task

The DHCP operation measures whether or not the
DHCP server can respond to client requests. DHCP also measures the amount of
time it takes the NQA client to obtain an IP address from a DHCP server.

The NQA client simulates the DHCP relay
agent to forward DHCP requests for IP address acquisition from the DHCP server.
The interface that performs the DHCP operation does not change its IP address. When
the DHCP operation completes, the NQA client sends a packet to release the
obtained IP address.

The DHCP operation acquires an IP address
from the DHCP server per probe.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Create an NQA operation and enter NQA
operation view.

nqa entry admin-name operation-tag

**3\.**Specify the DHCP type and enter its view.

type dhcp

**4\.**Specify the IP address of the DHCP server as
the destination IP address of DHCP packets.

destination ip ip-address

By default, no destination IP address is
specified.

**5\.**Specify the output interface for DHCP
request packets.

out interface interface-type
interface-number

By default, the NQA client determines the output interface based on the routing
table lookup.

**6\.**Specify the source IP address of DHCP
request packets.

source ip ip-address

By default, the source IP address of DHCP
request packets is the primary IP address of their output interface.

The specified source IP address must be the
IP address of a local interface, and the local interface must be up. Otherwise,
no probe packets can be sent out.

### Configuring the DNS operation

#### About this task

The DNS operation simulates domain name
resolution, and it measures the time for the NQA client to resolve a domain
name into an IP address through a DNS server. The obtained DNS entry is not
saved.

The DNS operation resolves a domain name into
an IP address per probe.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Create an NQA operation and enter NQA
operation view.

nqa entry admin-name operation-tag

**3\.**Specify the DNS type and enter its view.

type dns

**4\.**Specify the IP address of the DNS server as
the destination IP address of DNS packets.

destination ip ip-address

By default, no destination IP address is
specified.

**5\.**Specify the domain name to be translated.

resolve-target domain-name

By default, no domain name is specified.

### Configuring the FTP operation

#### About this task

The FTP operation measures the time for the
NQA client to transfer a file to or download a file from an FTP server.

The FTP operation uploads or downloads a
file from an FTP server per probe.

#### Restrictions and guidelines

To upload (put)
a file to the FTP server, use the filename command
to specify the name of the file you want to upload. The file must exist on the
NQA client.

To download (get)
a file from the FTP server, include the name of the file you want to download
in the url command. The file must exist on the
FTP server. The NQA client does not save the file obtained from the FTP server.

Use a small file for the FTP operation. A
big file might result in transfer failure because of timeout, or might affect
other services because of the amount of network bandwidth it occupies.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Create an NQA operation and enter NQA
operation view.

nqa entry admin-name operation-tag

**3\.**Specify the FTP type and enter its view.

type ftp

**4\.**Specify an FTP login username.

username username

By default, no FTP login username is
specified.

**5\.**Specify an FTP login password.

password { cipher \| simple } string

By default, no FTP login password is
specified.

**6\.**Specify the source IP address for FTP
request packets.

source ip ip-address

By default, the source IP address of FTP
request packets is the primary IP address of their output interface.

The source IP address must be the IP
address of a local interface, and the interface must be up. Otherwise, no FTP
requests can be sent out.

**7\.**Set the data transmission mode.

mode { active \| passive }

The default mode is active.

**8\.**Specify the FTP operation type.

operation { get \| put }

The default FTP operation type is get.

**9\.**Specify the destination URL for the FTP
operation.

url url

By default, no destination URL is
specified for an FTP operation.

Enter the URL in one of the following
formats:

¡     ftp://host/filename.

¡     ftp://host:port/filename.

The filename argument
is required only for the get operation.

**10\.**Specify the name of the file to be uploaded.

filename file-name

By default, no file is specified.

This task is required only for the put operation. 

The configuration
does not take effect for the get
operation.

### Configuring the HTTP operation

#### About this task

The HTTP operation measures the time for the
NQA client to obtain responses from an HTTP server.

The HTTP operation supports the following
operation types:

·     Get—Retrieves data such as a Web page from the HTTP server. 

·     Post—Sends data to the HTTP server for processing.

·     Raw—Sends a user-defined HTTP request to the HTTP server. You must
manually configure the content of the HTTP request to be sent.

The HTTP operation completes the operation
of the specified type per probe.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Create an NQA operation and enter NQA
operation view.

nqa entry admin-name operation-tag

**3\.**Specify the HTTP type and enter its view.

type http

**4\.**Specify the destination URL for the HTTP operation.

url url

By default, no destination URL is
specified for an HTTP operation.

Enter the URL in one of the following
formats:

¡     http://host/resource

¡     http://host:port/resource

**5\.**Specify an HTTP login username.

username username

By default, no HTTP login username is
specified.

**6\.**Specify an HTTP login password.

password { cipher \| simple } string

By default, no HTTP login password is
specified.

**7\.**Specify the HTTP version.

version { v1.0 \| v1.1 }

By default, HTTP 1.0 is used.

**8\.**Specify the HTTP operation type.

operation { get \| post \| raw }

The default HTTP operation type is get.

If you set the operation type to raw, the client pads the content configured in raw request view to the
HTTP request to send to the HTTP server.

**9\.**Configure the HTTP raw request. 

**a.**Enter raw request view.

raw-request

Every time you enter raw request view,
the previously configured raw request content is cleared.

**b.**Enter or paste the request content.

By default, no request content is
configured.

To ensure successful operations, make
sure the request content does not contain command aliases configured by using
the alias
command. For more information about the alias command, see CLI commands in Fundamentals
Command Reference.

**c.**Save the input and return to HTTP operation
view:

quit

This step is required only when the
operation type is set to raw.

**10\.**Specify the source IP address for the HTTP
packets.

source ip ip-address

By default, the source IP address of HTTP
packets is the primary IP address of their output interface.

The source IP address must be the IP
address of a local interface, and the interface must be up. Otherwise, no
request packets can be sent out.

### Configuring the UDP jitter operation

#### About this task

The UDP jitter operation measures
unidirectional and bidirectional jitters. The operation result helps you
determine whether the network can carry jitter-sensitive services such as
real-time voice and video services.

The UDP jitter operation works as follows:

**1\.**The NQA client sends UDP packets to the
destination port.

**2\.**The destination device time stamps each
packet it receives, and then sends the packet back to the NQA client.

**3\.**Upon receiving the responses, the NQA client
calculates the jitter according to the timestamps. 

The UDP jitter operation sends a number of
UDP packets to the destination device per probe. The number of packets to send is
determined by using the probe packet-number
command.

The UDP jitter operation requires both the
NQA server and the NQA client. Before you perform the UDP jitter operation,
configure the UDP listening service on the NQA server. For more information about
UDP listening service configuration, see "[Configuring the NQA server](#_Ref345588597)."

#### Restrictions and guidelines

To ensure successful UDP jitter operations
and avoid affecting existing services, do not perform the operations on
well-known ports from 1 to 1023\.

The display nqa history command does not display the results or statistics of the UDP
jitter operation. To view the results or statistics of the UDP jitter
operation, use the display nqa result or display nqa statistics command.

Before starting the operation, make sure
the network devices are time synchronized by using NTP. For more information
about NTP, see "Configuring NTP."

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Create an NQA operation and enter NQA operation
view.

nqa entry admin-name operation-tag

**3\.**Specify the UDP jitter type and enter its
view.

type udp-jitter

**4\.**Specify the destination IP address for UDP
packets.

destination ip ip-address

By default, no destination IP address is specified.

The destination IP address must be the
same as the IP address of the UDP listening service configured on the NQA
server. To configure a UDP listening service on the server, use the nqa server
udp-echo command.

**5\.**Specify the destination port number for UDP
packets.

destination port port-number

By default, no destination port number is
specified.

The destination port number must be the
same as the port number of the UDP listening service configured on the NQA
server. To configure a UDP listening service on the server, use the nqa server
udp-echo command.

**6\.**Specify the source IP address for UDP
packets.

source ip ip-address

By default, the source IP address of UDP packets
is the primary IP address of their output interface.

The source IP address must be the IP
address of a local interface, and the interface must be up. Otherwise, no UDP
packets can be sent out.

**7\.**Specify the source port number for UDP
packets.

source port port-number

By default, the system automatically
selects an unused port as the source port for UDP packets.

For successful probing, make sure the
source port number specified in the command is not used by any service on the
device. As a best practice, use the default port number.

**8\.**Set the number of UDP packets sent per probe.

probe packet-number number

The default setting is 10\.

**9\.**Set the interval for sending UDP packets.

probe packet-interval interval

The default setting is 20 milliseconds.

**10\.**Specify how long the NQA client waits for a
response from the server before it regards the response times out.

probe packet-timeout timeout

The default setting is 3000 milliseconds.

**11\.**(Optional.) Set the payload size for each
UDP packet.

data-size size

The default payload size is 100 bytes.

**12\.**(Optional.) Specify the payload fill string
for UDP packets.

data-fill string

The default payload fill string is the
hexadecimal string 00010203040506070809. 

### Configuring the SNMP operation

#### About this task

The SNMP operation tests whether the SNMP
service is available on an SNMP agent.

The SNMP operation sends one SNMPv1 packet,
one SNMPv2c packet, and one SNMPv3 packet to the SNMP agent per probe.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Create an NQA operation and enter NQA
operation view.

nqa entry admin-name operation-tag

**3\.**Specify the SNMP type and enter its view.

type snmp

**4\.**Specify the destination address for SNMP
packets.

destination ip ip-address

By default, no destination IP address is specified.

**5\.**Specify the source IP address for SNMP
packets.

source ip ip-address

By default, the source IP address of SNMP
packets is the primary IP address of their output interface.

The source IP address must be the IP
address of a local interface, and the interface must be up. Otherwise, no SNMP
packets can be sent out.

**6\.**Specify the source port number for SNMP
packets.

source port port-number

By default, the system automatically
selects an unused port as the source port for SNMP packets.

For successful probing, make sure the
source port number specified in the command is not used by any service on the
device. As a best practice, use the default port number.

**7\.**Specify the community name carried in the
SNMPv1 and SNMPv2c packets.

community read { cipher \| simple } community-name

By default, the SNMPv1 and SNMPv2c
packets carry community name public.

Make sure the specified community name is
the same as the community name configured on the SNMP agent.

### Configuring the TCP operation

#### About this task

The TCP operation measures the time for the
NQA client to establish a TCP connection to a port on the NQA server.

The TCP operation requires both the NQA
server and the NQA client. Before you perform a TCP operation, configure a TCP
listening service on the NQA server. For more information about the TCP
listening service configuration, see "[Configuring the NQA server](#_Ref345588597)."

The TCP operation sets up a TCP connection per
probe.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Create an NQA operation and enter NQA
operation view.

nqa entry admin-name operation-tag

**3\.**Specify the TCP type and enter its view.

type tcp

**4\.**Specify the destination address for TCP
packets.

destination ip ip-address

By default, no destination IP address is specified.

The destination address must be the same
as the IP address of the TCP listening service configured on the NQA server. To
configure a TCP listening service on the server, use the nqa server
tcp-connect command.

**5\.**Specify the destination port for TCP
packets.

destination port port-number

By default, no destination port number is
configured.

The destination port number must be the
same as the port number of the TCP listening service configured on the NQA
server. To configure a TCP listening service on the server, use the nqa server
tcp-connect command.

**6\.**Specify the source IP address for TCP
packets.

source ip ip-address

By default, the source IP address of TCP packets
is the primary IP address of their output interface.

The source IP address must be the IP
address of a local interface, and the interface must be up. Otherwise, no TCP
packets can be sent out.

### Configuring the UDP echo operation

#### About this task

The UDP echo operation measures the round-trip
time between the client and a UDP port on the NQA server.

The UDP echo operation requires both the
NQA server and the NQA client. Before you perform a UDP echo operation, configure
a UDP listening service on the NQA server. For more information about the UDP
listening service configuration, see "[Configuring the NQA server](#_Ref345588597)."

The UDP echo operation sends a UDP packet to
the destination device per probe.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Create an NQA operation and enter NQA
operation view.

nqa entry admin-name operation-tag

**3\.**Specify the UDP echo type and enter its
view.

type udp-echo

**4\.**Specify the destination address for UDP
packets.

destination ip ip-address

By default, no destination IP address is specified.

The destination address must be the same
as the IP address of the UDP listening service configured on the NQA server. To
configure a UDP listening service on the server, use the nqa server
udp-echo command.

**5\.**Specify the destination port number for UDP
packets.

destination port port-number

By default, no destination port number is
specified.

The destination port number must be the same
as the port number of the UDP listening service configured on the NQA server.
To configure a UDP listening service on the server, use the nqa server
udp-echo command.

**6\.**Specify the source IP address for UDP
packets.

source ip ip-address

By default, the source IP address of UDP packets
is the primary IP address of their output interface.

The source IP address must be the IP
address of a local interface, and the interface must be up. Otherwise, no UDP
packets can be sent out.

**7\.**Specify the source port number for UDP
packets.

source port port-number

By default, the system automatically
selects an unused port as the source port for UDP packets.

For successful probing, make sure the
source port number specified in the command is not used by any service on the
device. As a best practice, use the default port number.

**8\.**(Optional.) Set the payload size for each
UDP packet.

data-size size

The default setting is 100 bytes.

**9\.**(Optional.) Specify the payload fill string
for UDP packets.

data-fill string

The default payload fill string is the
hexadecimal string 00010203040506070809. 

### Configuring theUDP tracert operation

#### About this task

The UDP tracert operation determines the routing
path from the source device to the destination device. 

The UDP tracert operation sends a UDP
packet to a hop along the path per probe.

#### Restrictions and guidelines

The UDP tracert operation is not supported on
IPv6 networks. To determine the routing path that the IPv6 packets traverse
from the source to the destination, use the tracert ipv6
command. For more information about the command, see Network
Management and Monitoring Command Reference.

#### Prerequisites

Before you configure the UDP tracert
operation, you must perform the following tasks:

·     Enable sending ICMP time exceeded messages on
the intermediate devices between the source and destination devices. If the intermediate
devices are H3C devices, use the ip ttl-expires enable command.

·     Enable sending ICMP destination unreachable
messages on the destination device. If the destination device is an H3C device,
use the ip unreachables enable command.

For more information about the ip ttl-expires
enable and ip unreachables enable commands, see Layer 3—IP Services Command
Reference.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Create an NQA operation and enter NQA
operation view.

nqa entry admin-name operation-tag

**3\.**Specify the UDP tracert operation type and
enter its view.

type udp-tracert

**4\.**Specify the destination device for the
operation. Choose one option as needed:

¡     Specify the destination device by its host name.

destination host host-name

By default, no destination host name is
specified.

¡     Specify
the destination device by its IP address.

destination ip ip-address

By default, no destination IP address is
specified.

**5\.**Specify the destination port number for UDP
packets.

destination port port-number

By default, the destination port number
is 33434\.

This port number must be an unused number
on the destination device, so that the destination device can reply with ICMP
port unreachable messages.

**6\.**Specify an output interface for UDP packets.

out interface interface-type
interface-number

By default, the NQA client determines the
output interface based on the routing table lookup.

**7\.**Specify the source IP address for UDP
packets.

¡     Specify
the IP address of the specified interface as the source IP address.

source interface interface-type interface-number

By default, the source IP address of UDP packets
is the primary IP address of their output interface.

¡     Specify
the source IP address.

source ip ip-address

The specified source interface must be
up. The source IP address must be the IP address of a local interface, and the
local interface must be up. Otherwise, no probe packets can be sent out.

**8\.**Specify the source port number for UDP
packets.

source port port-number

By default, the system automatically
selects an unused port as the source port for UDP packets.

For successful probing, make sure the
source port number specified in the command is not used by any service on the
device. As a best practice, use the default port number.

**9\.**Set the maximum number of consecutive probe
failures.

max-failure times

The default setting is 5\.

**10\.**Set the initial TTL value for UDP packets.

init-ttl value

The default setting is 1\.

**11\.**(Optional.) Set the payload size for each
UDP packet.

data-size size

The default setting is 100 bytes.

**12\.**(Optional.) Enable the no-fragmentation
feature.

no-fragment enable

By default, the no-fragmentation feature
is disabled.

### Configuring the voice operation

#### About this task

The voice operation measures VoIP network
performance.

The voice operation works as follows:

**1\.**The NQA client sends voice packets at sending
intervals to the destination device (NQA server).

The voice packets are of one of the
following codec types:

¡     G.711
A-law.

¡     G.711
µ-law.

¡     G.729
A-law.

**2\.**The destination device time stamps each voice
packet it receives and sends it back to the source.

**3\.**Upon receiving the packet, the source device
calculates the jitter and one-way delay based on the timestamp.

The voice operation sends a number of voice
packets to the destination device per probe. The number of packets to send per
probe is determined by using the probe packet-number command.

The following parameters that reflect VoIP
network performance can be calculated by using the metrics gathered by the
voice operation:

·     Calculated Planning
Impairment Factor (ICPIF)—Measures impairment to
voice quality on a VoIP network. It is decided by packet loss and delay. A
higher value represents a lower service quality.

·     Mean Opinion Scores (MOS)—A MOS value can be evaluated by using the ICPIF value, in the range
of 1 to 5\. A higher value represents a higher service quality.

The evaluation of voice quality depends on
users' tolerance for voice quality. For users with higher tolerance for voice
quality, use the advantage-factor
command to set an advantage factor. When the system calculates the ICPIF value,
it subtracts the advantage factor to modify ICPIF and MOS values for voice
quality evaluation.

The voice operation requires both the NQA
server and the NQA client. Before you perform a voice operation, configure a UDP
listening service on the NQA server. For more information about UDP listening service
configuration, see "[Configuring the NQA server](#_Ref345588597)."

#### Restrictions and guidelines

To ensure successful voice operations and
avoid affecting existing services, do not perform the operations on well-known
ports from 1 to 1023\.

The display nqa history command does not display the results or statistics of the voice operation.
To view the results or statistics of the voice operation, use the display nqa
result or display nqa statistics command.

Before starting the operation, make sure
the network devices are time synchronized by using NTP. For more information
about NTP, see "Configuring NTP."

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Create an NQA operation and enter NQA
operation view.

nqa entry admin-name operation-tag

**3\.**Specify the voice type and enter its view.

type voice

**4\.**Specify the destination IP address for voice
packets.

destination ip ip-address

By default, no destination IP address is
configured.

The destination IP address must be the
same as the IP address of the UDP listening service configured on the NQA
server. To configure a UDP listening service on the server, use the nqa server
udp-echo command.

**5\.**Specify the destination port number for
voice packets.

destination port port-number

By default, no destination port number is
configured.

The destination port number must be the
same as the port number of the UDP listening service configured on the NQA
server. To configure a UDP listening service on the server, use the nqa server
udp-echo command.

**6\.**Specify the source IP address for voice
packets.

source ip ip-address

By default, the source IP address of
voice packets is the primary IP address of their output interface.

The source IP address must be the IP
address of a local interface, and the interface must be up. Otherwise, no voice
packets can be sent out.

**7\.**Specify the source port number for voice
packets.

source port port-number

By default, the system automatically
selects an unused port as the source port for voice packets.

For successful probing, make sure the
source port number specified in the command is not used by any service on the
device. As a best practice, use the default port number.

**8\.**Configure the basic voice operation
parameters.

¡     Specify
the codec type.

codec-type { g711a \| g711u \| g729a }

By default, the codec type is G.711
A-law.

¡     Set
the advantage factor for calculating MOS and ICPIF values.

advantage-factor factor

By default, the advantage factor is 0\.

**9\.**Configure the probe parameters for the voice
operation.

¡     Set
the number of voice packets to be sent per probe.

probe packet-number number

The default setting is 1000\.

¡     Set
the interval for sending voice packets.

probe packet-interval interval

The default setting is 20 milliseconds.

¡     Specify
how long the NQA client waits for a response from the server before it regards
the response times out.

probe packet-timeout timeout

The default setting is 5000 milliseconds.

**10\.**Configure the payload parameters.

**a.**Set the payload size for voice packets.

data-size size

By default, the voice packet size varies
by codec type. The default packet size is 172 bytes for G.711A-law and G.711 µ-law codec type,
and 32 bytes for G.729 A-law codec type.

**b.**(Optional.) Specify the payload fill string
for voice packets.

data-fill string

The default payload fill string is the
hexadecimal string 00010203040506070809. 

### Configuring the DLSw operation

#### About this task

The DLSw operation measures the response
time of a DLSw device.

It sets up a DLSw connection to the DLSw
device per probe.

#### Restrictions and guidelines

For the DLSw operation to succeed,
configure the nqa server tcp-connect
command on the NQA server and make sure the port number for the TCP listening service
is 2065\.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Create an NQA operation and enter NQA
operation view.

nqa entry admin-name operation-tag

**3\.**Specify the DLSw type and enter its view.

type dlsw

**4\.**Specify the destination IP address for the
probe packets.

destination ip ip-address

By default, no destination IP address is specified.

**5\.**Specify the source IP address for the probe
packets.

source ip ip-address

By default, the source IP address of the
probe packets is the primary IP address of their output interface.

The source IP address must be the IP
address of a local interface, and the interface must be up. Otherwise, no probe
packets can be sent out.

### Configuring the path jitter operation

#### About this task

The path jitter operation measures the jitter,
negative jitters, and positive jitters from the NQA client to each hop on the
path to the destination.

The path jitter operation performs the
following steps per probe:

**1\.**Obtains the path from the NQA client to the
destination through tracert. A maximum of 64 hops can be detected.

**2\.**Sends a number of ICMP echo requests to each
hop along the path. The number of ICMP echo requests to send is set by using
the probe
packet-number command.

#### Prerequisites

Before you configure the path jitter
operation, you must perform the following tasks:

·     Enable sending ICMP time exceeded messages on
the intermediate devices between the source and destination devices. If the
intermediate devices are H3C devices, use the ip ttl-expires
enable command.

·     Enable sending ICMP destination unreachable
messages on the destination device. If the destination device is an H3C device,
use the ip unreachables enable command.

For more information about the ip ttl-expires
enable and ip unreachables enable commands, see Layer 3—IP Services Command Reference.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Create an NQA operation and enter NQA
operation view.

nqa entry admin-name operation-tag

**3\.**Specify the path jitter type and enter its
view.

type path-jitter

**4\.**Specify the destination IP address for ICMP
echo requests.

destination ip ip-address

By default, no destination IP address is
specified.

**5\.**Specify the source IP address for ICMP echo
requests.

source ip ip-address

By default, the source IP address of ICMP
echo requests is the primary IP address of their output interface.

The source IP address must be the IP
address of a local interface, and the interface must be up. Otherwise, no ICMP
echo requests can be sent out.

**6\.**Configure the probe parameters for the path jitter
operation.

**a.**Set the number of ICMP echo requests to be
sent per probe.

probe packet-number number

The default setting is 10\.

**b.**Set the interval for sending ICMP echo
requests.

probe packet-interval interval

The default setting is 20 milliseconds.

**c.**Specify how long the NQA client waits for a
response from the server before it regards the response times out.

probe packet-timeout timeout

The default setting is 3000 milliseconds.

**7\.**(Optional.) Specify an LSR path.

lsr-path ip-address\&\<1-8\>

By default, no LSR path is specified.

The path jitter operation uses tracert to
detect the LSR path to the destination, and sends ICMP echo requests to each
hop on the LSR path.

**8\.**Perform the path jitter operation only on
the destination address.

target-only

By default, the path jitter operation is
performed on each hop on the path to the destination.

**9\.**(Optional.) Set the payload size for each
ICMP echo request.

data-size size

The default setting is 100 bytes.

**10\.**(Optional.) Specify the payload fill string
for ICMP echo requests.

data-fill string

The default payload fill string is the
hexadecimal string 00010203040506070809. 

### Configuring optional parameters for the NQA operation

#### Restrictions and guidelines

The parameter settings take effect only on
the current operation.

The supported parameters vary by NQA
operation type. For information about supported parameters, see NQA commands in
Network Management and Monitoring Command Reference.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter the view of an existing NQA operation.

nqa entry admin-name operation-tag

**3\.**Specify an NQA operation type and enter its
view.

type { dhcp \| dlsw \| dns \| ftp \| http \| icmp-echo \| icmp-jitter \| path-jitter \| snmp \| tcp \| udp-echo \| udp-jitter \| udp-tracert \| voice }

**4\.**Configure a description for the operation.

description text

By default, no description is configured.

**5\.**Set the interval at which the NQA operation
repeats.

frequency interval

For a voice or path jitter operation, the
default setting is 60000 milliseconds.

For other types of operations, the
default setting is 0 milliseconds, and only one operation is performed.

When the interval expires, but the
operation is not completed or is not timed out, the next operation does not
start.

**6\.**Specify the probe times.

probe count times

In an UDP tracert operation, the NQA
client performs three probes to each hop to the destination by default.

In other types of operations, the NQA
client performs one probe to the destination per operation by default.

This command is not available for the voice
and path jitter operations. Each of these operations performs only one probe.

**7\.**Set the probe timeout time.

probe timeout timeout

The default setting is 3000 milliseconds.

**8\.**Set the maximum number of hops that the
probe packets can traverse.

ttl value

The default setting is 30 for probe
packets of the UDP tracert operation, and is 20 for probe packets of other types
of operations.

**9\.**Set the ToS value in the IP header of the
probe packets.

tos value

The default setting is 0\.

**10\.**Enable the routing table bypass feature.

route-option bypass-route

By default, the routing table bypass feature
is disabled.

This command does not take effect if the
destination address of the NQA operation is an IPv6 address.

**11\.**Specify the VPN instance where the operation
is performed.

vpn-instance vpn-instance-name

By default, the operation is performed on
the public network.

### Configuring the collaboration feature

#### About this task

Collaboration is implemented by associating
a reaction entry of an NQA operation with a track entry. The reaction entry
monitors the NQA operation. If the number of operation failures reaches the
specified threshold, the configured action is triggered.

#### Restrictions and guidelines

The collaboration feature is not available
for the following types of operations:

·     ICMP jitter operation.

·     UDP jitter operation.

·     UDP tracert operation.

·     Voice operation.

·     Path jitter operation.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter the view of an existing NQA operation.

nqa entry admin-name operation-tag

**3\.**Configure a reaction entry.

reaction item-number checked-element probe-fail threshold-type
consecutive consecutive-occurrences action-type trigger-only

You cannot modify the content of an
existing reaction entry.

**4\.**Return to system view.

quit

**5\.**Associate Track with NQA.

For information about the configuration,
see High Availability Configuration Guide.

**6\.**Associate Track with an application module.

For information about the configuration,
see High Availability Configuration Guide.

### Configuring threshold monitoring

#### About this task

This feature allows you to monitor the NQA
operation running status.

An NQA operation supports the following
threshold types:

·     average—If the average value for the monitored performance metric either exceeds
the upper threshold or goes below the lower threshold, a threshold violation
occurs.

·     accumulate—If the total number of times that the monitored performance metric
is out of the specified value range reaches or exceeds the specified threshold,
a threshold violation occurs.

·     consecutive—If the number of consecutive times that the monitored performance
metric is out of the specified value range reaches or exceeds the specified
threshold, a threshold violation occurs. 

Threshold violations for the average or
accumulate threshold type are determined on a per NQA operation basis. The threshold
violations for the consecutive type are determined from the time the NQA operation
starts.

The following actions might be triggered:

·     none—NQA displays results only on the terminal screen. It does not send
traps to the NMS.

·     trap-only—NQA displays results on the terminal screen, and meanwhile it sends
traps to the NMS. 

To send traps to the NMS, the NMS address
must be specified by using the snmp-agent target-host command. For
more information about the command, see Network Management and
Monitoring Command Reference.

·     trigger-only—NQA displays results on the terminal screen, and meanwhile triggers
other modules for collaboration.

In a reaction entry, configure a monitored
element, a threshold type, and an action to be triggered to implement threshold
monitoring.

The state of a reaction entry can be
invalid, over-threshold, or below-threshold.

·     Before an NQA operation starts, the reaction
entry is in invalid state. 

·     If the threshold is violated, the state of the
entry is set to over-threshold. Otherwise, the state of the entry is set to
below-threshold.

#### Restrictions and guidelines

The threshold monitoring feature is not
available for the path jitter operations.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter the view of an existing NQA operation.

nqa entry admin-name operation-tag

**3\.**Enable sending traps to the NMS when
specific conditions are met.

reaction trap { path-change \| probe-failure consecutive-probe-failures \| test-complete \| test-failure
\[ accumulate-probe-failures ] }

By default, no traps are sent to the NMS.

The ICMP jitter, UDP jitter, and voice
operations support only the test-complete keyword.

The following parameters are not
available for the UDP tracert operation:

¡     The probe-failure consecutive-probe-failures
option.

¡     The accumulate-probe-failures
argument.

**4\.**Configure threshold monitoring. Choose the
options to configure as needed:

¡     Monitor
the operation duration.

reaction item-number checked-element probe-duration threshold-type { accumulate
accumulate-occurrences \| average \| consecutive consecutive-occurrences
} threshold-value
upper-threshold lower-threshold \[ action-type { none \| trap-only } ]

This reaction entry is not supported in
the ICMP jitter, UDP jitter, UDP tracert, or voice operations 

¡     Monitor
failure times.

reaction item-number checked-element probe-fail threshold-type { accumulate accumulate-occurrences \| consecutive consecutive-occurrences
} \[ action-type { none \| trap-only } ]

This reaction entry is not supported in
the ICMP jitter, UDP jitter, UDP tracert, or voice operations.

¡     Monitor
the round-trip time.

reaction item-number checked-element rtt threshold-type { accumulate accumulate-occurrences \| average } threshold-value upper-threshold
lower-threshold \[ action-type { none \| trap-only } ]

Only the ICMP jitter, UDP jitter, and
voice operations support this reaction entry.

¡     Monitor
packet loss.

reaction item-number checked-element packet-loss threshold-type accumulate accumulate-occurrences \[ action-type { none \| trap-only } ]

Only the ICMP jitter, UDP jitter, and
voice operations support this reaction entry.

¡     Monitor
the one-way jitter.

reaction item-number checked-element { jitter-ds \| jitter-sd } threshold-type { accumulate accumulate-occurrences \| average } threshold-value upper-threshold
lower-threshold \[ action-type { none \| trap-only } ]

Only the ICMP jitter, UDP jitter, and
voice operations support this reaction entry.

¡     Monitor
the one-way delay.

reaction item-number checked-element { owd-ds \| owd-sd } threshold-value upper-threshold
lower-threshold

Only the ICMP jitter, UDP jitter, and
voice operations support this reaction entry.

¡     Monitor
the ICPIF value.

reaction item-number checked-element icpif threshold-value upper-threshold
lower-threshold \[ action-type { none \| trap-only } ]

Only the voice operation supports this
reaction entry.

¡     Monitor
the MOS value.

reaction item-number checked-element mos threshold-value upper-threshold
lower-threshold \[ action-type { none \| trap-only } ]

Only the voice operation supports this
reaction entry.

The DNS operation does not support the
action of sending trap messages. For the DNS operation, the action type can
only be none.

### Configuring the NQA statistics collection feature

#### About this task

NQA forms statistics within the same
collection interval as a statistics group. To display information about the
statistics groups, use the display nqa statistics
command.

When the maximum number of statistics
groups is reached, the NQA client deletes the oldest statistics group to save a
new one.

A statistics group is automatically deleted
when its hold time expires.

#### Restrictions and guidelines

The NQA statistics collection feature is
not available for the UDP tracert operations.

If you use the frequency command to set the interval to 0 milliseconds for an NQA operation,
NQA does not generate any statistics group for the operation.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter the view of an existing NQA operation.

nqa entry admin-name operation-tag

**3\.**Set the statistics collection interval.

statistics interval interval

The default setting is 60 minutes.

**4\.**Set the maximum number of statistics groups
that can be saved.

statistics max-group number

By default, the NQA client can save a
maximum of two statistics groups for an operation.

To disable the NQA statistics collection
feature, set the number argument to 0\.

**5\.**Set the hold time of statistics groups.

statistics hold-time hold-time

The default setting is 120 minutes.

### Configuring the saving of NQA history records

#### About this task

This task enables the NQA client to save NQA
history records. You can use the display nqa history command to display the NQA history records.

#### Restrictions and guidelines

The NQA history record saving feature is
not available for the following types of operations:

·     ICMP jitter operation.

·     UDP jitter operation.

·     Voice operation.

·     Path jitter operation.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter the view of an existing NQA operation.

nqa entry admin-name operation-tag

**3\.**Enable the saving of history records for the
NQA operation.

history-record enable

By default, this feature is enabled only
for the UDP tracert operation.

**4\.**Set the lifetime of history records.

history-record keep-time keep-time

The default setting is 120 minutes.

A record is deleted when its lifetime is
reached.

**5\.**Set the maximum number of history records
that can be saved.

history-record number number

The default setting is 50\.

When the maximum number of history
records is reached, the system will delete the oldest record to save a new one.

### Scheduling the NQA operation on the NQA client

#### About this task

The NQA operation runs between the specified
start time and end time (the start time plus operation duration). If the specified
start time is ahead of the system time, the operation starts immediately. If both
the specified start and end time are ahead of the system time, the operation does
not start. To display the current system time, use the display clock command.

#### Restrictions and guidelines

You cannot enter the operation type view or
the operation view of a scheduled NQA operation.

A system time adjustment does not affect
started or completed NQA operations. It affects only the NQA operations that
have not started.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Specify the scheduling parameters for an NQA
operation.

nqa schedule admin-name operation-tag start-time { hh:mm:ss \[ yyyy/mm/dd \| mm/dd/yyyy ] \| now } lifetime { lifetime \| forever } \[ recurring ]

## Configuring NQA templates on the NQA client

### Restrictions and guidelines

Some operation parameters for an NQA
template can be specified by the template configuration or the feature (such as
load balancing) that uses the template. When both are specified, the parameters
in the template configuration take effect.

### NQA template tasks at a glance

To configure NQA templates, perform the
following tasks:

**1\.**Perform at least one of the following tasks:

¡     [Configuring the ICMP template](#_Ref398624383)

¡     [Configuring the DNS template](#_Ref398021686)

¡     [Configuring the TCP template](#_Ref398021708)

¡     [Configuring the TCP half open template](#_Ref418612463)

¡     [Configuring the UDP template](#_Ref398624401)

¡     [Configuring the HTTP template](#_Ref398624414)

¡     [Configuring the HTTPS template](#_Ref418617087)

¡     [Configuring the FTP template](#_Ref398021720)

¡     [Configuring the RADIUS template](#_Ref405908280)

¡     [Configuring the SSL template](#_Ref418612491)

**2\.**(Optional.) [Configuring optional parameters for the
NQA template](#_Ref418696489)

### Configuring the ICMP template

#### About this task

A feature that uses the ICMP template performs
the ICMP operation to measure the reachability of a destination device. The
ICMP template is supported on both IPv4 and IPv6 networks.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Create an ICMP template and enter its view.

nqa template icmp name

**3\.**Specify the destination IP address for the
operation.

IPv4:

destination ip ip-address

IPv6:

destination ipv6 ipv6-address

By default, no destination IP address is
configured.

**4\.**Specify the source IP address for ICMP echo
requests. Choose one option as needed:

¡     Use
the IP address of the specified interface as the source IP address.

source interface interface-type
interface-number

By default, the primary IP address of the
output interface is used as the source IP address of ICMP echo requests.

The specified source interface must be up.

¡     Specify
the source IPv4 address.

source ip ip-address

By default, the primary IPv4 address of
the output interface is used as the source IPv4 address of ICMP echo requests.

The specified source IPv4 address must be
the IPv4 address of a local interface, and the interface must be up. Otherwise,
no probe packets can be sent out. 

¡     Specify
the source IPv6 address.

source ipv6 ipv6-address

By default, the primary IPv6 address of
the output interface is used as the source IPv6 address of ICMP echo requests.

The specified source IPv6 address must be
the IPv6 address of a local interface, and the interface must be up. Otherwise,
no probe packets can be sent out.

**5\.**Specify the next hop IP address for ICMP
echo requests.

IPv4:

next-hop ip ip-address

IPv6:

next-hop ipv6 ipv6-address

By default, no IP address of the next hop
is configured.

**6\.**Configure the probe result sending on a
per-probe basis.

reaction trigger per-probe

By default, the probe result is sent to
the feature that uses the template after three consecutive failed or successful
probes.

If you execute the reaction
trigger per-probe and reaction
trigger probe-pass commands multiple times, the
most recent configuration takes effect.

If you execute the reaction
trigger per-probe and reaction
trigger probe-fail commands multiple times, the
most recent configuration takes effect.

**7\.**(Optional.) Set the payload size for each
ICMP request.

data-size size

The default setting is 100 bytes.

**8\.**(Optional.) Specify the payload fill string
for ICMP echo requests.

data-fill string

The default payload fill string is the
hexadecimal string 00010203040506070809. 

### Configuring the DNS template

#### About this task

A feature that uses the DNS template performs
the DNS operation to determine the status of the server. The DNS template is
supported on both IPv4 and IPv6 networks.

In DNS template view, you can specify the
address expected to be returned. If the returned IP addresses include the
expected address, the DNS server is valid and the operation succeeds. Otherwise,
the operation fails.

#### Prerequisites

Create a mapping between the domain name
and an address before you perform the DNS operation. For information about
configuring the DNS server, see documents about the DNS server configuration.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Create a DNS template and enter DNS template
view.

nqa template dns name

**3\.**Specify the destination IP address for the
probe packets.

IPv4:

destination ip ip-address

IPv6:

destination ipv6 ipv6-address

By default, no destination address is
specified.

**4\.**Specify the destination port number for the
probe packets.

destination port port-number

By default, the destination port number
is 53\.

**5\.**Specify the source IP address for the probe
packets.

IPv4:

source ip ip-address

By default, the source IPv4 address of
the probe packets is the primary IPv4 address of their output interface.

The source IPv4 address must be the IPv4
address of a local interface, and the interface must be up. Otherwise, no probe
packets can be sent out. 

IPv6:

source ipv6 ipv6-address

By default, the source IPv6 address of
the probe packets is the primary IPv6 address of their output interface.

The source IPv6 address must be the IPv6 address
of a local interface, and the interface must be up. Otherwise, no probe packets
can be sent out.

**6\.**Specify the source port number for the probe
packets.

source port port-number

By default, the system automatically
selects an unused port as the source port for probe packets.

For successful probing, make sure the
source port number specified in the command is not used by any service on the
device. As a best practice, use the default port number.

**7\.**Specify the domain name to be translated.

resolve-target domain-name

By default, no domain name is specified.

**8\.**Specify the domain name resolution type.

resolve-type { A \| AAAA }

By default, the type is type A.

A type A query resolves a domain name to a
mapped IPv4 address, and a type AAAA query to a mapped IPv6 address. 

**9\.**(Optional.) Specify the IP address that is
expected to be returned.

IPv4:

expect ip ip-address

IPv6:

expect ipv6 ipv6-address

By default, no expected IP address is
specified.

### Configuring the TCP template

#### About this task

A feature that uses the TCP template performs
the TCP operation to test whether the NQA client can establish a TCP connection
to a specific port on the server.

In TCP template view, you can specify the expected
data to be returned. If you do not specify the expected data, the TCP operation
tests only whether the client can establish a TCP connection to the server.

The TCP operation requires both the NQA
server and the NQA client. Before you perform a TCP operation, configure a TCP
listening service on the NQA server. For more information about the TCP
listening service configuration, see "[Configuring the NQA server](#_Ref345588597)."

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Create a TCP template and enter its view.

nqa template tcp name

**3\.**Specify the destination IP address for the
probe packets.

IPv4:

destination ip ip-address

IPv6:

destination ipv6 ipv6-address

By default, no destination IP address is
specified.

The destination address must be the same
as the IP address of the TCP listening service configured on the NQA server. To
configure a TCP listening service on the server, use the nqa server
tcp-connect command.

**4\.**Specify the destination port number for the
operation.

destination port port-number

By default, no destination port number is
specified.

The destination port number must be the
same as the port number of the TCP listening service configured on the NQA
server. To configure a TCP listening service on the server, use the nqa server
tcp-connect command.

**5\.**Specify the source IP address for the probe
packets.

IPv4:

source ip ip-address

By default, the primary IPv4 address of
the output interface is used as the source IPv4 address of the probe packets.

The source IP address must be the IPv4
address of a local interface, and the interface must be up. Otherwise, no probe
packets can be sent out. 

IPv6:

source ipv6 ipv6-address

By default, the primary IPv6 address of
the output interface is used as the source IPv6 address of the probe packets.

The source IPv6 address must be the IPv6
address of a local interface, and the interface must be up. Otherwise, no probe
packets can be sent out.

**6\.**(Optional.) Specify the payload fill string
for the probe packets.

data-fill string

The default payload fill string is the
hexadecimal string 00010203040506070809.

**7\.**(Optional.) Configure the expected data.

expect data expression \[ offset number ]

By default, no expected data is
configured.

The NQA client performs expect data check
only when you configure both the data-fill
and expect-data commands.

### Configuring the TCP half open template

#### About this task

A feature that uses the TCP half open
template performs the TCP half open operation to test whether the TCP service
is available on the server. The TCP half open operation is used when the
feature cannot get a response from the TCP server through an existing TCP
connection.

In the TCP half open operation, the NQA
client sends a TCP ACK packet to the server. If the client receives an RST
packet, it considers that the TCP service is available on the server.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Create a TCP half open template and enter
its view.

nqa template tcphalfopen name

**3\.**Specify the destination IP address of the operation.

IPv4:

destination ip ip-address

IPv6:

destination ipv6 ipv6-address

By default, no destination IP address is
specified.

The destination address must be the same
as the IP address of the TCP listening service configured on the NQA server. To
configure a TCP listening service on the server, use the nqa server
tcp-connect command.

**4\.**Specify the source IP address for the probe
packets.

IPv4:

source ip ip-address

By default, the primary IPv4 address of
the output interface is used as the source IPv4 address of the probe packets.

The source IPv4 address must be the IPv4
address of a local interface, and the interface must be up. Otherwise, no probe
packets can be sent out. 

IPv6:

source ipv6 ipv6-address

By default, the primary IPv6 address of
the output interface is used as the source IPv6 address of the probe packets.

The source IPv6 address must be the IPv6
address of a local interface, and the interface must be up. Otherwise, no probe
packets can be sent out.

**5\.**Specify the next hop IP address for the
probe packets.

IPv4:

next-hop ip ip-address

IPv6:

next-hop ipv6 ipv6-address

By default, the IP address of the next
hop is configured.

**6\.**Configure the probe result sending on a
per-probe basis.

reaction trigger per-probe

By default, the probe result is sent to
the feature that uses the template after three consecutive failed or successful
probes.

If you execute the reaction
trigger per-probe and reaction
trigger probe-pass commands multiple times, the
most recent configuration takes effect.

If you execute the reaction
trigger per-probe and reaction
trigger probe-fail commands multiple times, the
most recent configuration takes effect.

### Configuring the UDP template

#### About this task

A feature that uses the UDP template
performs the UDP operation to test the following items:

·     Reachability of a specific port on the NQA
server.

·     Availability of the requested service on the NQA
server.

In UDP template view, you can specify the
expected data to be returned. If you do not specify the expected data, the UDP
operation tests only whether the client can receive the response packet from
the server.

The UDP operation requires both the NQA
server and the NQA client. Before you perform a UDP operation, configure a UDP
listening service on the NQA server. For more information about the UDP
listening service configuration, see "[Configuring the NQA server](#_Ref345588597)."

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Create a UDP template and enter its view.

nqa template udp name

**3\.**Specify the destination IP address of the
operation.

IPv4:

destination ip ip-address

IPv6:

destination ipv6 ipv6-address

By default, no destination IP address is
specified.

The destination address must be the same
as the IP address of the UDP listening service configured on the NQA server. To
configure a UDP listening service on the server, use the nqa server
udp-echo command.

**4\.**Specify the destination port number for the
operation.

destination port port-number

By default, no destination port number is
specified.

The destination port number must be the
same as the port number of the UDP listening service configured on the NQA
server. To configure a UDP listening service on the server, use the nqa server
udp-echo command.

**5\.**Specify the source IP address for the probe
packets.

IPv4:

source ip ip-address

By default, the primary IPv4 address of
the output interface is used as the source IPv4 address of the probe packets.

The source IP address must be the IPv4
address of a local interface, and the interface must be up. Otherwise, no probe
packets can be sent out. 

IPv6:

source ipv6 ipv6-address

By default, the primary IPv6 address of
the output interface is used as the source IPv6 address of the probe packets.

The source IPv6 address must be the IPv6
address of a local interface, and the interface must be up. Otherwise, no probe
packets can be sent out.

**6\.**Specify the payload fill string for the
probe packets.

data-fill string

The default payload fill string is the
hexadecimal string 00010203040506070809. 

**7\.**(Optional.) Set the payload size for the
probe packets.

data-size size

The default setting is 100 bytes.

**8\.**(Optional.) Configure the expected data.

expect data expression \[ offset number ]

By default, no expected data is
configured.

Expected data check is performed only
when both the data-fill command and
the expect
data command are configured.

### Configuring the HTTP template

#### About this task

A feature that uses the HTTP template performs
the HTTP operation to measure the time it takes the NQA client to obtain data
from an HTTP server.

The expected data is checked only when the data
is configured and the HTTP response contains the Content-Length field in the
HTTP header. 

The status code of the HTTP packet is a three-digit
field in decimal notation, and it includes the status information for the HTTP
server. The first digit defines the class of response.

#### Prerequisites

Before you perform the HTTP operation, you
must configure the HTTP server.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Create an HTTP template and enter its view.

nqa template http name

**3\.**Specify the destination URL for the HTTP template.

url url

By default, no destination URL is
specified for an HTTP template.

Enter the URL in one of the following
formats:

¡     http://host/resource

¡     http://host:port/resource

**4\.**Specify an HTTP login username.

username username

By default, no HTTP login username is
specified.

**5\.**Specify an HTTP login password.

password { cipher \| simple } string

By default, no HTTP login password is specified.

**6\.**Specify the HTTP version.

version { v1.0 \| v1.1 }

By default, HTTP 1.0 is used.

**7\.**Specify the HTTP operation type.

operation { get \| post \| raw }

By default, the HTTP operation type is get.

If you set the operation type to raw, the
client pads the content configured in raw request view to the HTTP request to
send to the HTTP server.

**8\.**Configure the content of the HTTP raw
request. 

**a.**Enter raw request view.

raw-request

Every time you enter raw request view,
the previously configured raw request content is cleared.

**b.**Enter or paste the request content.

By default, no request content is
configured.

To ensure successful operations, make
sure the request content does not contain command aliases configured by using
the alias
command. For more information about the alias command, see CLI commands in Fundamentals
Command Reference.

**c.**Return to HTTP template view.

quit

The system automatically saves the
configuration in raw request view before it returns to HTTP template view.

This step is required only when the
operation type is set to raw.

**9\.**Specify the source IP address for the probe
packets.

IPv4:

source ip ip-address

By default, the primary IPv4 address of
the output interface is used as the source IPv4 address of the probe packets.

The source IPv4 address must be the IPv4
address of a local interface, and the interface must be up. Otherwise, no probe
packets can be sent out. 

IPv6:

source ipv6 ipv6-address

By default, the primary IPv6 address of
the output interface is used as the source IPv6 address of the probe packets.

The source IPv6 address must be the IPv6
address of a local interface, and the interface must be up. Otherwise, no probe
packets can be sent out.

**10\.**(Optional.) Configure the expected status
codes.

expect status status-list

By default, no expected status code is
configured.

**11\.**(Optional.) Configure the expected data.

expect data expression \[ offset number ]

By default, no expected data is
configured.

### Configuring the HTTPS template

#### About this task

A feature that uses the HTTPS template
performs the HTTPS operation to measure the time it takes for the NQA client to
obtain data from an HTTPS server.

The expected data is checked only when the expected
data is configured and the HTTPS response contains the Content-Length field in
the HTTPS header.

The status code of the HTTPS packet is a
three-digit field in decimal notation, and it includes the status information
for the HTTPS server. The first digit defines the class of response.

#### Prerequisites

Before you perform the HTTPS operation,
configure the HTTPS server and the SSL client policy for the SSL client. For
information about configuring SSL client policies, see Security
Configuration Guide.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Create an HTTPS template and enter its view.

nqa template https name

**3\.**Specify the destination URL for the HTTPS template.

url url

By default, no destination URL is
specified for an HTTPS template.

Enter the URL in one of the following
formats:

¡     https://host/resource

¡     https://host:port/resource

**4\.**Specify an HTTPS login username.

username username

By default, no HTTPS login username is
specified.

**5\.**Specify an HTTPS login password.

password { cipher \| simple } string

By default, no HTTPS login password is
specified.

**6\.**Specify an SSL client policy.

ssl-client-policy policy-name

By default, no SSL client policy is
specified.

**7\.**Specify the HTTPS version.

version { v1.0 \| v1.1 }

By default, HTTPS 1.0 is used.

**8\.**Specify the HTTPS operation type.

operation { get \| post \| raw }

By default, the HTTPS operation type is get.

If you set the operation type to raw, the
client pads the content configured in raw request view to the HTTPS request to
send to the HTTPS server.

**9\.**Configure the content of the HTTPS raw
request.

**a.**Enter raw request view.

raw-request

Every time you enter raw request view,
the previously configured raw request content is cleared.

**b.**Enter or paste the request content.

By default, no request content is
configured.

To ensure successful operations, make
sure the request content does not contain command aliases configured by using
the alias command. For more information about the alias command, see CLI commands in Fundamentals
Command Reference.

**c.**Return to HTTPS template view.

quit

The system automatically saves the
configuration in raw request view before it returns to HTTPS template view.

This step is required only when the
operation type is set to raw.

**10\.**Specify the source IP address for the probe
packets.

IPv4:

source ip ip-address

By default, the primary IPv4 address of
the output interface is used as the source IPv4 address of the probe packets.

The source IP address must be the IPv4
address of a local interface, and the interface must be up. Otherwise, no probe
packets can be sent out. 

IPv6:

source ipv6 ipv6-address

By default, the primary IPv6 address of
the output interface is used as the source IPv6 address of the probe packets.

The source IPv6 address must be the IPv6
address of a local interface, and the interface must be up. Otherwise, no probe
packets can be sent out.

**11\.**(Optional.) Configure the expected data.

expect data expression \[ offset number ]

By default, no expected data is
configured.

**12\.**(Optional.) Configure the expected status
codes.

expect status status-list

By default, no expected status code is
configured.

### Configuring the FTP template

#### About this task

A feature that uses the FTP template performs
the FTP operation. The operation measures the time it takes the NQA client to
transfer a file to or download a file from an FTP server.

Configure the username and password for the
FTP client to log in to the FTP server before you perform an FTP operation. For
information about configuring the FTP server, see Fundamentals
Configuration Guide.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Create an FTP template and enter its view.

nqa template ftp name

**3\.**Specify an FTP login username.

username username

By default, no FTP login username is
specified.

**4\.**Specify an FTP login password.

password { cipher \| simple } sting

By default, no FTP login password is
specified.

**5\.**Specify the source IP address for the probe
packets.

IPv4:

source ip ip-address

By default, the primary IPv4 address of
the output interface is used as the source IPv4 address of the probe packets.

The source IP address must be the IPv4
address of a local interface, and the interface must be up. Otherwise, no probe
packets can be sent out. 

IPv6:

source ipv6 ipv6-address

By default, the primary IPv6 address of
the output interface is used as the source IPv6 address of the probe packets.

The source IPv6 address must be the IPv6
address of a local interface, and the interface must be up. Otherwise, no probe
packets can be sent out.

**6\.**Set the data transmission mode.

mode { active \| passive }

The default mode is active.

**7\.**Specify the FTP operation type.

operation { get \| put }

By default, the FTP operation type is get, which
means obtaining files from the FTP server.

**8\.**Specify the destination URL for the FTP
template.

url url

By default, no destination URL is
specified for an FTP template.

Enter the URL in one of the following
formats:

¡     ftp://host/filename.

¡     ftp://host:port/filename.

When you perform the get operation,
the file name is required.

When you perform the put operation,
the filename argument does not take effect, even if it is specified. The file
name for the put operation is determined by using the filename command.

**9\.**Specify the name of a file to be
transferred.

filename filename

By default, no file is specified.

This task is required only for the put operation. 

The configuration does not take effect
for the get operation.

### Configuring the RADIUS template

#### About this task

A feature that uses the RADIUS template
performs the RADIUS authentication operation to check the availability of the
authentication service on the RADIUS server.

The RADIUS authentication operation workflow
is as follows:

**1\.**The NQA client sends an authentication
request (Access-Request) to the RADIUS server. The request includes the username
and the password. The password is encrypted by using the MD5 algorithm and the shared
key.

**2\.**The RADIUS server authenticates the username
and password. 

¡     If
the authentication succeeds, the server sends an Access-Accept packet to the
NQA client. 

¡     If
the authentication fails, the server sends an Access-Reject packet to the NQA
client.

**3\.**The NQA client determines the availability
of the authentication service on the RADIUS server based on the response packet
it received:

¡     If
an Access-Accept packet is received, the authentication service is available on
the RADIUS server. 

¡     If
an Access-Reject packet is received, the authentication service is not available
on the RADIUS server.

#### Prerequisites

Before you configure the RADIUS template, specify
a username, password, and shared key on the RADIUS server. For more information
about configuring the RADIUS server, see AAA in Security
Configuration Guide. 

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Create a RADIUS template and enter its view.

nqa template radius name

**3\.**Specify the destination IP address of the
operation.

IPv4:

destination ip ip-address

IPv6:

destination ipv6 ipv6-address

By default, no destination IP address is
specified.

**4\.**Specify the destination port number for the
operation.

destination port port-number

By default, the destination port number
is 1812\.

**5\.**Specify a username.

username username

By default, no username is specified.

**6\.**Specify a password.

password { cipher \| simple } string

By default, no password is specified.

**7\.**Specify a shared key for secure RADIUS
authentication.

key { cipher \| simple } string

By default, no shared key is specified
for RADIUS authentication.

**8\.**Specify the source IP address for the probe
packets.

IPv4:

source ip ip-address

By default, the primary IPv4 address of
the output interface is used as the source IPv4 address of the probe packets.

The source IP address must be the IPv4
address of a local interface, and the interface must be up. Otherwise, no probe
packets can be sent out. 

IPv6:

source ipv6 ipv6-address

By default, the primary IPv6 address of
the output interface is used as the source IPv6 address of the probe packets.

The source IPv6 address must be the IPv6
address of a local interface, and the interface must be up. Otherwise, no probe
packets can be sent out.

### Configuring the SSL template

#### About this task

A feature that uses the SSL template
performs the SSL operation to measure the time required to establish an SSL
connection to an SSL server.

#### Prerequisites

Before you configure the SSL template, you
must configure the SSL client policy. For information about configuring SSL
client policies, see Security Configuration Guide.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Create an SSL template and enter its view.

nqa template ssl name

**3\.**Specify the destination IP address of the
operation.

IPv4:

destination ip ip-address

IPv6:

destination ipv6 ipv6-address

By default, no destination IP address is
specified.

**4\.**Specify the destination port number for the
operation.

destination port port-number

By default, the destination port number
is not specified.

**5\.**Specify an SSL client policy.

ssl-client-policy policy-name

By default, no SSL client policy is
specified.

**6\.**Specify the source IP address for the probe
packets.

IPv4:

source ip ip-address

By default, the primary IPv4 address of
the output interface is used as the source IPv4 address of the probe packets.

The source IP address must be the IPv4
address of a local interface, and the interface must be up. Otherwise, no probe
packets can be sent out. 

IPv6:

source ipv6 ipv6-address

By default, the primary IPv6 address of
the output interface is used as the source IPv6 address of the probe packets.

The source IPv6 address must be the IPv6
address of a local interface, and the interface must be up. Otherwise, no probe
packets can be sent out.

### Configuring optional parameters for the NQA template

#### Restrictions and guidelines

Unless otherwise specified, the following optional
parameters apply to all types of NQA templates.

The parameter settings take effect only on
the current NQA template.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter the view of an
existing NQA template.

nqa template { dns \| ftp \| http \| https \| icmp \| radius \| ssl \| tcp \| tcphalfopen \| udp } name

**3\.**Configure a description.

description text

By default, no description is configured.

**4\.**Set the interval at which the NQA operation
repeats.

frequency interval

The default setting is 5000 milliseconds.

If the operation is not completed when
the interval expires, the next operation does not start.

**5\.**Set the probe timeout time.

probe timeout timeout

The default setting is 3000 milliseconds.

**6\.**Set the TTL for the probe packets.

ttl value

The default setting is 20\.

This command is not available for the ARP
template.

**7\.**Set the ToS value in the IP header of the
probe packets.

tos value

The default setting is 0\.

This command is not available for the ARP
template.

**8\.**Specify the VPN instance where the operation
is performed.

vpn-instance vpn-instance-name

By default, the operation is performed on
the public network.

**9\.**Set the number of consecutive successful
probes to determine a successful operation event.

reaction trigger probe-pass count

The default setting is 3\.

If the number of consecutive successful
probes for an NQA operation is reached, the NQA client notifies the feature
that uses the template of the successful operation event.

**10\.**Set the number of consecutive probe failures
to determine an operation failure.

reaction trigger probe-fail count

The default setting is 3\.

If the number of consecutive probe
failures for an NQA operation is reached, the NQA client notifies the feature
that uses the NQA template of the operation failure.

## Display and maintenance commands for NQA

Execute display
commands in any view.

 

| Task | Command |
| --- | --- |
| Display history records of NQA operations. | display nqa history \[ admin-name operation-tag ] || Display the current monitoring results of reaction entries. | display nqa reaction counters \[ admin-name operation-tag \[ item-number ] ] || Display the most recent result of the NQA operation. | display nqa result \[ admin-name operation-tag ] || Display NQA server status. | display nqa server status || Display NQA statistics. | display nqa statistics \[ admin-name operation-tag ] |





‌

## NQA configuration examples

For configuration examples of using an NQA
template for a feature, see High Availability
Configuration Guide.

### Example: Configuring the ICMP echo operation

#### Network configuration

As shown in [Figure 2](#_Ref258567816), configure
an ICMP echo operation on the NQA client (Device A) to test the round-trip time
to Device B. The next hop of Device A is Device C.

Figure 2 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705663_x_Img_x_png_1_2216108_294551_0.png)

‌

#### Procedure

\# Assign IP addresses to interfaces, as
shown in [Figure 2](#_Ref258567816). (Details
not shown.)

\# Configure static routes or a routing
protocol to make sure the devices can reach each other. (Details not shown.)

\# Create an ICMP echo operation.

\<DeviceA\> system-view

\[DeviceA] nqa entry admin test1

\[DeviceA-nqa-admin-test1] type
icmp-echo

\# Specify 10.2.2.2 as the destination IP
address of ICMP echo requests.

\[DeviceA-nqa-admin-test1-icmp-echo]
destination ip 10.2.2.2

\# Specify 10.1.1.2 as the next hop. The
ICMP echo requests are sent through Device C to Device B. 

\[DeviceA-nqa-admin-test1-icmp-echo]
next-hop ip 10.1.1.2

\# Configure the ICMP echo operation to
perform 10 probes.

\[DeviceA-nqa-admin-test1-icmp-echo]
probe count 10

\# Set the probe timeout time to 500
milliseconds for the ICMP echo operation.

\[DeviceA-nqa-admin-test1-icmp-echo]
probe timeout 500

\# Configure the ICMP echo operation to
repeat every 5000 milliseconds.

\[DeviceA-nqa-admin-test1-icmp-echo]
frequency 5000

\# Enable saving history records.

\[DeviceA-nqa-admin-test1-icmp-echo] history-record
enable

\# Set the maximum number of history records
to 10\.

\[DeviceA-nqa-admin-test1-icmp-echo] history-record
number 10

\[DeviceA-nqa-admin-test1-icmp-echo]
quit

\# Start the ICMP echo operation.

\[DeviceA] nqa schedule admin test1
start-time now lifetime forever

\# After the ICMP echo operation runs for a
period of time, stop the operation.

\[DeviceA] undo nqa schedule admin test1

#### Verifying the configuration

\# Display the most recent result of the
ICMP echo operation.

\[DeviceA] display nqa result admin
test1

NQA entry (admin admin, tag test1)
test results:

    Send operation times:
10             Receive response times: 10

    Min/Max/Average round trip time:
2/5/3

    Square-Sum of round trip time: 96

    Last succeeded probe time: 2019-08-23
15:00:01.2

  Extended results:

    Packet loss ratio: 0%

    Failures due to timeout: 0

    Failures due to internal error: 0

    Failures due to other errors: 0

\# Display the history records of the ICMP
echo operation.

\[DeviceA] display nqa history admin
test1

NQA entry (admin admin, tag test)
history records:

  Index      Response    
Status           Time

  370        3           
Succeeded        2019-08-23 15:00:01.2

  369        3           
Succeeded        2019-08-23 15:00:01.2

  368        3           
Succeeded        2019-08-23 15:00:01.2

  367        5           
Succeeded        2019-08-23 15:00:01.2

  366        3           
Succeeded        2019-08-23 15:00:01.2

  365        3           
Succeeded        2019-08-23 15:00:01.2

  364        3           
Succeeded        2019-08-23 15:00:01.1

  363        2           
Succeeded        2019-08-23 15:00:01.1

  362        3           
Succeeded        2019-08-23 15:00:01.1

  361        2           
Succeeded        2019-08-23 15:00:01.1

The output shows that the packets sent by
Device A can reach Device B through Device C. No packet loss occurs during the
operation. The minimum, maximum, and average round-trip times are 2, 5, and 3
milliseconds, respectively.

### Example: Configuring the ICMP jitter operation

#### Network configuration

As shown in [Figure 3](#_Ref429638375),
configure an ICMP jitter operation to test the jitter between Device A and
Device B.

Figure 3 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705674_x_Img_x_png_2_2216108_294551_0.png)

‌

#### Procedure

**1\.**Assign IP addresses to interfaces, as shown
in [Figure 3](#_Ref429638375).
(Details not shown.)

**2\.**Configure static routes or a routing
protocol to make sure the devices can reach each other. (Details not shown.)

**3\.**Configure Device A:

\# Create an ICMP jitter operation.

\<DeviceA\> system-view

\[DeviceA] nqa entry admin
test1

\[DeviceA-nqa-admin-test1] type
icmp-jitter

\# Specify 10.2.2.2 as the destination
address for the operation.

\[DeviceA-nqa-admin-test1-icmp-jitter]
destination ip 10.2.2.2

\# Configure the operation to repeat every
1000 milliseconds.

\[DeviceA-nqa-admin-test1-icmp-jitter]
frequency 1000

\[DeviceA-nqa-admin-test1-icmp-jitter]
quit

\# Start the ICMP jitter operation.

\[DeviceA] nqa schedule admin
test1 start-time now lifetime forever

\# After the ICMP jitter operation runs
for a period of time, stop the operation.

\[DeviceA] undo nqa schedule
admin test1

#### Verifying the configuration

\# Display the most recent result of the ICMP
jitter operation.

\[DeviceA] display nqa result admin
test1

NQA entry (admin admin, tag
test1) test results:

    Send operation times:
10             Receive response times: 10 

    Min/Max/Average round trip
time: 1/2/1 

    Square-Sum of round trip
time: 13 

    Last packet received time:
2019-03-09 17:40:29.8 

  Extended results:

    Packet loss ratio: 0% 

    Failures due to timeout: 0

    Failures due to internal
error: 0

    Failures due to other
errors: 0

    Packets out of sequence: 0

    Packets arrived late: 0

  ICMP-jitter results:

   RTT number: 10

    Min positive SD:
0                     Min positive DS: 0

    Max positive SD:
0                     Max positive DS: 0

    Positive SD number:
0                  Positive DS number: 0

    Positive SD sum:
0                     Positive DS sum: 0

    Positive SD average:
0                 Positive DS average: 0

    Positive SD square-sum:
0              Positive DS square-sum: 0

    Min negative SD:
1                     Min negative DS: 2

    Max negative SD:
1                     Max negative DS: 2

    Negative SD number:
1                  Negative DS number: 1

    Negative SD sum:
1                     Negative DS sum: 2

    Negative SD average:
1                 Negative DS average: 2

    Negative SD square-sum:
1              Negative DS square-sum: 4

    SD average: 1                          DS
average: 2

  One way results:

    Max SD delay:
1                        Max DS delay: 2

    Min SD delay:
1                        Min DS delay: 2

    Number of SD delay:
1                  Number of DS delay: 1

    Sum of SD delay:
1                     Sum of DS delay: 2

    Square-Sum of SD delay:
1              Square-Sum of DS delay: 4

    Lost packets for unknown
reason: 0

\# Display the statistics of the ICMP
jitter operation.

\[DeviceA] display nqa
statistics admin test1

NQA entry (admin admin, tag test1)
test statistics:

  NO. : 1

    Start time: 2019-03-09
17:42:10.7 

    Life time: 156 seconds

    Send operation times:
1560           Receive response times: 1560 

    Min/Max/Average round trip
time: 1/2/1 

    Square-Sum of round trip
time: 1563 

  Extended results:

    Packet loss ratio: 0% 

    Failures due to timeout: 0

    Failures due to internal
error: 0

    Failures due to other
errors: 0

    Packets out of sequence: 0

    Packets arrived late: 0

  ICMP-jitter results:

   RTT number: 1560

    Min positive SD:
1                     Min positive DS: 1

    Max positive SD:
1                     Max positive DS: 2

    Positive SD number:
18                 Positive DS number: 46

    Positive SD sum:
18                    Positive DS sum: 49

    Positive SD average:
1                 Positive DS average: 1

    Positive SD square-sum:
18             Positive DS square-sum: 55

    Min negative SD:
1                     Min negative DS: 1

    Max negative SD:
1                     Max negative DS: 2

    Negative SD number:
24                 Negative DS number: 57

    Negative SD sum:
24                    Negative DS sum: 58

    Negative SD average:
1                 Negative DS average: 1

    Negative SD square-sum:
24             Negative DS square-sum: 60

    SD average:
16                         DS average: 2

  One way results:

    Max SD delay:
1                        Max DS delay: 2

    Min SD delay:
1                        Min DS delay: 1

    Number of SD delay:
4                  Number of DS delay: 4

    Sum of SD delay:
4                     Sum of DS delay: 5

    Square-Sum of SD delay:
4              Square-Sum of DS delay: 7

    Lost packets for unknown
reason: 0

### Example: Configuring the DHCP operation

#### Network configuration

As shown in [Figure 4](#_Ref258567852),
configure a DHCP operation to test the time required for Switch A to obtain an
IP address from the DHCP server (Switch B).

Figure 4 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705683_x_Img_x_png_3_2216108_294551_0.png)

‌

#### Procedure

\# Create a DHCP operation.

\<SwitchA\> system-view

\[SwitchA] nqa entry admin test1

\[SwitchA-nqa-admin-test1] type dhcp

\# Specify the DHCP server address
(10.1.1.2) as the destination address.

\[SwitchA-nqa-admin-test1-dhcp] destination
ip 10.1.1.2

\# Enable the saving of history records.

\[SwitchA-nqa-admin-test1-dhcp]
history-record enable

\[SwitchA-nqa-admin-test1-dhcp] quit

\# Start the DHCP operation.

\[SwitchA] nqa schedule admin test1
start-time now lifetime forever

\# After the DHCP operation runs for a
period of time, stop the operation.

\[SwitchA] undo nqa schedule admin
test1

#### Verifying the configuration

\# Display the most recent result of the DHCP
operation.

\[SwitchA] display nqa result admin
test1

NQA entry (admin admin, tag test1)
test results:

    Send operation times:
1              Receive response times: 1

    Min/Max/Average round trip time:
512/512/512

    Square-Sum of round trip time:
262144

    Last succeeded probe time: 2019-11-22
09:56:03.2

  Extended results:

    Packet loss ratio: 0%

    Failures due to timeout: 0

    Failures due to internal error: 0

    Failures due to other errors: 0

\# Display the history records of the DHCP
operation.

\[SwitchA] display nqa history admin
test1

NQA entry (admin admin, tag test1)
history records:

  Index      Response     Status           Time

  1          512         
Succeeded        2019-11-22 09:56:03.2

The output shows that it took Switch A 512 milliseconds
to obtain an IP address from the DHCP server.

### Example: Configuring the DNS operation

#### Network configuration

As shown in [Figure 5](#_Ref258567868), configure
a DNS operation to test whether Device A can perform address resolution through
the DNS server and test the resolution time.

Figure 5 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705684_x_Img_x_png_4_2216108_294551_0.png)

‌

#### Procedure

\# Assign IP addresses to interfaces, as
shown in [Figure 5](#_Ref258567868). (Details
not shown.)

\# Configure static routes or a routing
protocol to make sure the devices can reach each other. (Details not shown.)

\# Create a DNS operation.

\<DeviceA\> system-view

\[DeviceA] nqa entry admin test1

\[DeviceA-nqa-admin-test1] type dns

\# Specify the IP address of the DNS server (10.2.2.2)
as the destination address.

\[DeviceA-nqa-admin-test1-dns]
destination ip 10.2.2.2

\# Specify host.com as
the domain name to be translated.

\[DeviceA-nqa-admin-test1-dns] resolve-target
host.com

\# Enable the saving of history records.

\[DeviceA-nqa-admin-test1-dns] history-record
enable

\[DeviceA-nqa-admin-test1-dns] quit

\# Start the DNS operation.

\[DeviceA] nqa schedule admin test1
start-time now lifetime forever

\# After the DNS operation runs for a period
of time, stop the operation.

\[DeviceA] undo nqa schedule admin test1

#### Verifying the configuration

\# Display the most recent result of the DNS
operation.

\[DeviceA] display nqa result admin
test1

NQA entry (admin admin, tag test1)
test results:

    Send operation times:
1              Receive response times: 1

    Min/Max/Average round trip time:
62/62/62

    Square-Sum of round trip time:
3844

    Last succeeded probe time: 2019-11-10
10:49:37.3

  Extended results:

    Packet loss ratio: 0%

    Failures due to timeout: 0

    Failures due to internal error: 0

    Failures due to other errors: 0

\# Display the history records of the DNS
operation.

\[DeviceA] display nqa history admin test1

NQA entry (admin admin, tag test)
history records:

  Index      Response    
Status           Time

  1          62          
Succeeded        2019-11-10 10:49:37.3

The output
shows that it took Device A 62 milliseconds to translate domain name host.com into an IP address.

### Example: Configuring the FTP operation

#### Network configuration

As shown in [Figure 6](#_Ref258567885), configure
an FTP operation to test the time required for Device A to upload a file to the
FTP server. The login username and password are admin
and systemtest, respectively. The file to be
transferred to the FTP server is config.txt.

Figure 6 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705685_x_Img_x_png_5_2216108_294551_0.png)

‌

#### Procedure

\# Assign IP addresses to interfaces, as
shown in [Figure 6](#_Ref258567885).
(Details not shown.)

\# Configure static routes or a routing
protocol to make sure the devices can reach each other. (Details not shown.)

\# Create an FTP operation.

\<DeviceA\> system-view

\[DeviceA] nqa entry admin test1

\[DeviceA-nqa-admin-test1] type ftp

\# Specify the URL of the FTP server.

\[DeviceA-nqa-admin-test-ftp] url
ftp://10.2.2.2

\# Specify 10.1.1.1 as the source IP
address. 

\[DeviceA-nqa-admin-test1-ftp] source
ip 10.1.1.1

\# Configure the device to upload file config.txt to the FTP server.

\[DeviceA-nqa-admin-test1-ftp]
operation put

\[DeviceA-nqa-admin-test1-ftp]
filename config.txt

\# Set the username to admin
for the FTP operation.

\[DeviceA-nqa-admin-test1-ftp]
username admin

\# Set the password to systemtest
for the FTP operation.

\[DeviceA-nqa-admin-test1-ftp]
password simple systemtest

\# Enable the saving of history records.

\[DeviceA-nqa-admin-test1-ftp]
history-record enable

\[DeviceA-nqa-admin-test1-ftp] quit

\# Start the FTP operation.

\[DeviceA] nqa schedule admin test1
start-time now lifetime forever

\# After the FTP operation runs for a period
of time, stop the operation.

\[DeviceA] undo nqa schedule admin test1

#### Verifying the configuration

\# Display the most recent result of the FTP
operation.

\[DeviceA] display nqa result admin
test1

NQA entry (admin admin, tag test1)
test results:

    Send operation times:
1              Receive response times: 1

    Min/Max/Average round trip time:
173/173/173

    Square-Sum of round trip time:
29929

    Last succeeded probe time: 2019-11-22
10:07:28.6

  Extended results:

    Packet loss ratio: 0%

    Failures due to timeout: 0

    Failures due to disconnect: 0

    Failures due to no connection: 0

    Failures due to internal error: 0

    Failures due to other errors: 0

\# Display the history records of the FTP
operation.

\[DeviceA] display nqa history admin test1

NQA entry (admin admin, tag test1)
history records:

  Index      Response    
Status           Time

  1          173         
Succeeded        2019-11-22 10:07:28.6

The output shows that it took Device A 173
milliseconds to upload a file to the FTP server. 

### Example: Configuring the HTTP operation

#### Network configuration

As shown in [Figure 7](#_Ref258567901), configure
an HTTP operation on the NQA client to test the time required to obtain data
from the HTTP server.

Figure 7 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705686_x_Img_x_png_6_2216108_294551_0.png)

‌

#### Procedure

\# Assign IP addresses to interfaces, as
shown in [Figure 7](#_Ref258567901).
(Details not shown.)

\# Configure static routes or a routing
protocol to make sure the devices can reach each other. (Details not shown.)

\# Create an HTTP operation.

\<DeviceA\> system-view

\[DeviceA] nqa entry admin test1

\[DeviceA-nqa-admin-test1] type http

\# Specify the URL of the HTTP server.

\[DeviceA-nqa-admin-test1-http] url http://10.2.2.2/index.htm

\# Configure the HTTP operation to get data
from the HTTP server.

\[DeviceA-nqa-admin-test1-http]
operation get

\# Configure the operation to use HTTP
version 1.0.

\[DeviceA-nqa-admin-test1-http]
version v1.0

\# Enable the saving of history records.

\[DeviceA-nqa-admin-test1-http] history-record
enable

\[DeviceA-nqa-admin-test1-http] quit

\# Start the HTTP operation.

\[DeviceA] nqa schedule admin test1
start-time now lifetime forever

\# After the HTTP operation runs for a
period of time, stop the operation.

\[DeviceA] undo nqa schedule admin test1

#### Verifying the configuration

\# Display the most recent result of the HTTP
operation.

\[DeviceA] display nqa result admin
test1

NQA entry (admin admin, tag test1)
test results:

    Send operation times:
1              Receive response times: 1

    Min/Max/Average round trip time:
64/64/64

    Square-Sum of round trip time:
4096

    Last succeeded probe time: 2019-11-22
10:12:47.9

  Extended results:

    Packet loss ratio: 0%

    Failures due to timeout: 0

    Failures due to disconnect: 0

    Failures due to no connection: 0

    Failures due to internal error: 0

    Failures due to other errors: 0

\# Display the history records of the HTTP
operation.

\[DeviceA] display nqa history admin test1

NQA entry (admin admin, tag test1)
history records:

  Index      Response    
Status           Time

  1          64           Succeeded       
2019-11-22 10:12:47.9

The output shows that it took Device A 64
milliseconds to obtain data from the HTTP server.

### Example: Configuring the UDP jitter operation

#### Network configuration

As shown in [Figure 8](#_Ref258567920), configure
a UDP jitter operation to test the jitter, delay, and round-trip time between
Device A and Device B.

Figure 8 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705687_x_Img_x_png_7_2216108_294551_0.png)

‌

#### Procedure

**1\.**Assign IP addresses to
interfaces, as shown in [Figure 8](#_Ref258567920).
(Details not shown.)

**2\.**Configure static routes or a routing
protocol to make sure the devices can reach each other. (Details not shown.)

**3\.**Configure Device B:

\# Enable the NQA server.

\<DeviceB\> system-view

\[DeviceB] nqa server enable

\# Configure a listening service to listen
to UDP port 9000 on IP address 10.2.2.2.

\[DeviceB] nqa server udp-echo
10.2.2.2 9000

**4\.**Configure Device A:

\# Create a UDP jitter operation.

\<DeviceA\> system-view

\[DeviceA] nqa entry admin test1

\[DeviceA-nqa-admin-test1] type
udp-jitter

\# Specify 10.2.2.2 as the destination
address of the operation.

\[DeviceA-nqa-admin-test1-udp-jitter]
destination ip 10.2.2.2

\# Set the destination port number to
9000\.

\[DeviceA-nqa-admin-test1-udp-jitter]
destination port 9000

\# Configure the operation to repeat every
1000 milliseconds.

\[DeviceA-nqa-admin-test1-udp-jitter]
frequency 1000

\[DeviceA-nqa-admin-test1-udp-jitter]
quit

\# Start the UDP jitter operation.

\[DeviceA] nqa schedule admin test1
start-time now lifetime forever

\# After the UDP jitter operation runs for
a period of time, stop the operation.

\[DeviceA] undo nqa schedule
admin test1

#### Verifying the configuration

\# Display the most recent result of the UDP
jitter operation.

\[DeviceA] display nqa result admin
test1

NQA entry (admin admin, tag
test1) test results:

    Send operation times:
10             Receive response times: 10

    Min/Max/Average round trip
time: 15/32/17

    Square-Sum of round trip
time: 3235

    Last packet received time:
2019-05-29 13:56:17.6

  Extended results:

    Packet loss ratio: 0%

    Failures due to timeout: 0

    Failures due to internal
error: 0

    Failures due to other
errors: 0

    Packets out of sequence: 0

    Packets arrived late: 0

  UDP-jitter results:

   RTT number: 10

    Min positive SD:
4                     Min positive DS: 1

    Max positive SD:
21                    Max positive DS: 28

    Positive SD number:
5                  Positive DS number: 4

    Positive SD sum:
52                    Positive DS sum: 38

    Positive SD average:
10                Positive DS average: 10

    Positive SD square-sum:
754            Positive DS square-sum: 460

    Min negative SD:
1                     Min negative DS: 6

    Max negative SD:
13                    Max negative DS: 22

    Negative SD number:
4                  Negative DS number: 5

    Negative SD sum:
38                    Negative DS sum: 52

    Negative SD average:
10                Negative DS average: 10

    Negative SD square-sum:
460            Negative DS square-sum: 754

    SD average: 10                         DS
average: 10

  One way results:

    Max SD delay:
15                       Max DS delay: 16

    Min SD delay:
7                        Min DS delay: 7

    Number of SD delay:
10                 Number of DS delay: 10

    Sum of SD delay:
78                    Sum of DS delay: 85

    Square-Sum of SD delay:
666            Square-Sum of DS delay: 787

    SD lost packets:
0                   DS lost packets: 0

    Lost packets for unknown
reason: 0

\# Display the statistics of the UDP
jitter operation.

\[DeviceA] display nqa
statistics admin test1

NQA entry (admin admin, tag
test1) test statistics:

  NO. : 1

    Start time: 2019-05-29
13:56:14.0

    Life time: 47 seconds

    Send operation times:
410            Receive response times: 410

    Min/Max/Average round trip
time: 1/93/19

    Square-Sum of round trip
time: 206176

  Extended results:

    Packet loss ratio: 0%

    Failures due to timeout: 0

    Failures due to internal
error: 0

    Failures due to other
errors: 0

    Packets out of sequence: 0

    Packets arrived late: 0

  UDP-jitter results:

   RTT number: 410

    Min positive SD:
3                     Min positive DS: 1

    Max positive SD:
30                    Max positive DS: 79

    Positive SD number:
186                Positive DS number: 158

    Positive SD sum:
2602                  Positive DS sum: 1928

    Positive SD average:
13                Positive DS average: 12

    Positive SD square-sum:
45304          Positive DS square-sum: 31682

    Min negative SD:
1                     Min negative DS: 1

    Max negative SD:
30                    Max negative DS: 78

    Negative SD number:
181                Negative DS number: 209

    Negative SD sum:
181                   Negative DS sum: 209

    Negative SD average:
13                Negative DS average: 14

    Negative SD square-sum:
46994          Negative DS square-sum: 3030

    SD average:
9                          DS average: 1

  One way results:

    Max SD delay:
46                       Max DS delay: 46

    Min SD delay:
7                        Min DS delay: 7

    Number of SD delay:
410                Number of DS delay: 410

    Sum of SD delay:
3705                  Sum of DS delay: 3891

    Square-Sum of SD delay:
45987          Square-Sum of DS delay: 49393

    SD lost packets:
0                   DS lost packets: 0

    Lost packets for unknown
reason: 0

### Example: Configuring the SNMP operation

#### Network configuration

As shown in [Figure 9](#_Ref258567960), configure
an SNMP operation to test the time the NQA client uses to get a response from the
SNMP agent.

Figure 9 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705688_x_Img_x_png_8_2216108_294551_0.png)

‌

#### Procedure

**1\.**Assign IP addresses to
interfaces, as shown in [Figure 9](#_Ref258567960).
(Details not shown.)

**2\.**Configure static routes or a routing
protocol to make sure the devices can reach each other. (Details not shown.)

**3\.**Configure the SNMP agent (Device B):

\# Set the SNMP version to all.

\<DeviceB\> system-view

\[DeviceB] snmp-agent sys-info
version all

\# Set the read community to public.

\[DeviceB] snmp-agent community
read public

\# Set the write community to private.

\[DeviceB] snmp-agent community
write private

**4\.**Configure Device A:

\# Create an SNMP operation.

\<DeviceA\> system-view

\[DeviceA] nqa entry admin test1

\[DeviceA-nqa-admin-test1] type
snmp 

\# Specify 10.2.2.2 as the destination IP
address of the SNMP operation.

\[DeviceA-nqa-admin-test1-snmp]
destination ip 10.2.2.2

\# Enable the saving of history records.

\[DeviceA-nqa-admin-test1-snmp]
history-record enable

\[DeviceA-nqa-admin-test1-snmp]
quit

\# Start the SNMP operation.

\[DeviceA] nqa schedule admin test1
start-time now lifetime forever

\# After the SNMP operation runs for a
period of time, stop the operation.

\[DeviceA] undo nqa schedule
admin test1

#### Verifying the configuration

\# Display the most recent result of the SNMP
operation.

\[DeviceA] display nqa result admin
test1

NQA entry (admin admin, tag test1)
test results:

    Send operation times:
1              Receive response times: 1

    Min/Max/Average round trip
time: 50/50/50

    Square-Sum of round trip
time: 2500

    Last succeeded probe time:
2019-11-22 10:24:41.1

  Extended results:

    Packet loss ratio: 0%

    Failures due to timeout: 0

    Failures due to internal
error: 0

    Failures due to other
errors: 0

\# Display the history records of the SNMP
operation.

\[DeviceA] display nqa history
admin test1

NQA entry (admin admin, tag test1)
history records:

  Index      Response    
Status           Time

  1          50           Succeeded       
2019-11-22 10:24:41.1

The output shows that it took Device A 50
milliseconds to receive a response from the SNMP agent.

### Example: Configuring the TCP operation

#### Network configuration

As shown in [Figure 10](#_Ref258567978), configure
a TCP operation to test the time required for Device A to establish a TCP
connection with Device B.

Figure 10 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705689_x_Img_x_png_9_2216108_294551_0.png)

‌

#### Procedure

**1\.**Assign IP addresses to
interfaces, as shown in [Figure 10](#_Ref258567978).
(Details not shown.)

**2\.**Configure static routes or a routing
protocol to make sure the devices can reach each other. (Details not shown.)

**3\.**Configure Device B:

\# Enable the NQA server.

\<DeviceB\> system-view

\[DeviceB] nqa server enable

\# Configure a listening service to listen
to TCP port 9000 on IP address 10.2.2.2.

\[DeviceB] nqa server
tcp-connect 10.2.2.2 9000

**4\.**Configure Device A:

\# Create a TCP operation.

\<DeviceA\> system-view

\[DeviceA] nqa entry admin test1

\[DeviceA-nqa-admin-test1] type
tcp

\# Specify 10.2.2.2 as the destination IP
address.

\[DeviceA-nqa-admin-test1-tcp]
destination ip 10.2.2.2

\# Set the destination port number to
9000\.

\[DeviceA-nqa-admin-test1-tcp]
destination port 9000

\# Enable the saving of history records.

\[DeviceA-nqa-admin-test1-tcp] history-record
enable

\[DeviceA-nqa-admin-test1-tcp]
quit

\# Start the TCP operation.

\[DeviceA] nqa schedule admin test1
start-time now lifetime forever

\# After the TCP operation runs for a
period of time, stop the operation.

\[DeviceA] undo nqa schedule
admin test1

#### Verifying the configuration

\# Display the most recent result of the TCP
operation.

\[DeviceA] display nqa result admin
test1

NQA entry (admin admin, tag test1)
test results:

    Send operation times:
1              Receive response times: 1

    Min/Max/Average round trip
time: 13/13/13

    Square-Sum of round trip
time: 169

    Last succeeded probe time:
2019-11-22 10:27:25.1

  Extended results:

    Packet loss ratio: 0%

    Failures due to timeout: 0

    Failures due to
disconnect: 0

    Failures due to no
connection: 0

    Failures due to internal
error: 0

    Failures due to other
errors: 0

\# Display the history records of the TCP
operation.

\[DeviceA] display nqa history
admin test1

NQA entry (admin admin, tag
test1) history records:

  Index      Response    
Status           Time

  1          13          
Succeeded        2019-11-22 10:27:25.1

The output shows that it took Device A 13
milliseconds to establish a TCP connection to port 9000 on the NQA server.

### Example: Configuring the UDP echo operation

#### Network configuration

As shown in [Figure 11](#_Ref258568001), configure
a UDP echo operation on the NQA client to test the round-trip time to Device B.
The destination port number is 8000\.

Figure 11 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705664_x_Img_x_png_10_2216108_294551_0.png)

‌

#### Procedure

**1\.**Assign IP addresses to
interfaces, as shown in [Figure 11](#_Ref258568001).
(Details not shown.)

**2\.**Configure static routes or a routing
protocol to make sure the devices can reach each other. (Details not shown.)

**3\.**Configure Device B:

\# Enable the NQA server.

\<DeviceB\> system-view

\[DeviceB] nqa server enable

\# Configure a listening service to listen
to UDP port 8000 on IP address 10.2.2.2.

\[DeviceB] nqa server udp-echo
10.2.2.2 8000

**4\.**Configure Device A:

\# Create a UDP echo operation.

\<DeviceA\> system-view

\[DeviceA] nqa entry admin test1

\[DeviceA-nqa-admin-test1] type
udp-echo

\# Specify 10.2.2.2 as the destination IP
address.

\[DeviceA-nqa-admin-test1-udp-echo]
destination ip 10.2.2.2

\# Set the destination port number to
8000\.

\[DeviceA-nqa-admin-test1-udp-echo]
destination port 8000

\# Enable the saving of history records.

\[DeviceA-nqa-admin-test1-udp-echo]
history-record enable

\[DeviceA-nqa-admin-test1-udp-echo]
quit

\# Start the UDP echo operation.

\[DeviceA] nqa schedule admin test1
start-time now lifetime forever

\# After the UDP echo operation runs for a
period of time, stop the operation.

\[DeviceA] undo nqa schedule
admin test1

#### Verifying the configuration

\# Display the most recent result of the UDP
echo operation. 

\[DeviceA] display nqa result admin
test1

NQA entry (admin admin, tag test1)
test results:

    Send operation times:
1              Receive response times: 1

    Min/Max/Average round trip
time: 25/25/25

    Square-Sum of round trip
time: 625

    Last succeeded probe time:
2019-11-22 10:36:17.9

  Extended results:

    Packet loss ratio: 0%

    Failures due to timeout: 0

    Failures due to internal
error: 0

    Failures due to other
errors: 0

\# Display the history records of the UDP
echo operation.

\[DeviceA] display nqa history
admin test1

NQA entry (admin admin, tag
test1) history records:

  Index      Response    
Status           Time

  1          25          
Succeeded        2019-11-22 10:36:17.9

The output shows that the round-trip time
between Device A and port 8000 on Device B is 25 milliseconds.

### Example: Configuring the UDP tracert operation

#### Network configuration

As shown in [Figure 12](#_Ref382225657),
configure a UDP tracert operation to determine the routing path from Device A
to Device B.

Figure 12 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705665_x_Img_x_png_11_2216108_294551_0.png)

‌

#### Procedure

**1\.**Assign IP addresses to interfaces, as shown
in [Figure 12](#_Ref382225657).
(Details not shown.)

**2\.**Configure static routes or a routing
protocol to make sure the devices can reach each other. (Details not shown.)

**3\.**Execute the ip ttl-expires
enable command on the intermediate devices and execute
the ip
unreachables enable command on Device B.

**4\.**Configure Device A:

\# Create a UDP tracert operation.

\<DeviceA\> system-view

\[DeviceA] nqa entry admin
test1

\[DeviceA-nqa-admin-test1] type
udp-tracert

\# Specify 10.2.2.2 as the destination IP
address.

\[DeviceA-nqa-admin-test1-udp-tracert]
destination ip 10.2.2.2

\# Set the destination port number to 33434\.

\[DeviceA-nqa-admin-test1-udp-tracert]
destination port 33434

\# Configure Device A to perform three
probes to each hop.

\[DeviceA-nqa-admin-test1-udp-tracert]
probe count 3

\# Set the probe timeout time to 500
milliseconds.

\[DeviceA-nqa-admin-test1-udp-tracert]
probe timeout 500

\# Configure the UDP tracert operation to repeat
every 5000 milliseconds.

\[DeviceA-nqa-admin-test1-udp-tracert]
frequency 5000

\# Specify Ten-GigabitEthernet 1/0/1 as the output
interface for UDP packets.

\[DeviceA-nqa-admin-test1-udp-tracert]
out interface ten-gigabitethernet 1/0/1

\# Enable the no-fragmentation feature.

\[DeviceA-nqa-admin-test1-udp-tracert]
no-fragment enable

\# Set the maximum number of consecutive
probe failures to 6\.

\[DeviceA-nqa-admin-test1-udp-tracert]
max-failure 6

\# Set the TTL value to 1 for UDP packets
in the start round of the UDP tracert operation.

\[DeviceA-nqa-admin-test1-udp-tracert]
init-ttl 1

\# Start the UDP tracert operation.

\[DeviceA] nqa schedule admin
test1 start-time now lifetime forever

\# After the UDP tracert operation runs
for a period of time, stop the operation.

\[DeviceA] undo nqa schedule
admin test1

#### Verifying the configuration

\# Display the most recent result of the UDP
tracert operation.

\[DeviceA] display nqa result
admin test1

NQA entry (admin admin, tag
test1) test results:

    Send operation times: 6             
Receive response times: 6

    Min/Max/Average round trip
time: 1/1/1

    Square-Sum of round trip
time: 1

    Last succeeded probe time:
2019-09-09 14:46:06.2

  Extended results:

    Packet loss in test: 0%

    Failures due to timeout: 0

    Failures due to internal
error: 0

Failures due to other errors:
0

  UDP-tracert results:

    TTL    Hop IP            
Time

    1      3.1.1.1            2019-09-09
14:46:03.2

    2      10.2.2.2           2019-09-09
14:46:06.2

\# Display the history records of the UDP
tracert operation.

\[DeviceA] display nqa history
admin test1

NQA entry (admin admin, tag test1)
history records:

Index      TTL  Response  Hop
IP           Status          Time

1          2    2         10.2.2.2        
Succeeded       2019-09-09 14:46:06.2

1          2    1         10.2.2.2       
 Succeeded       2019-09-09 14:46:05.2

1          2    2         10.2.2.2       
 Succeeded       2019-09-09 14:46:04.2

1          1    1        
3.1.1.1          Succeeded       2019-09-09 14:46:03.2

1          1    2        
3.1.1.1          Succeeded       2019-09-09 14:46:02.2

1          1    1        
3.1.1.1          Succeeded       2019-09-09 14:46:01.2

### Example: Configuring the voice operation

#### Network configuration

As shown in [Figure 13](#_Ref258568022), configure
a voice operation to test jitters, delay, MOS, and ICPIF between Device A and
Device B.

Figure 13 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705666_x_Img_x_png_12_2216108_294551_0.png)

‌

#### Procedure

**1\.**Assign IP addresses to interfaces, as shown
in [Figure 13](#_Ref258568022).
(Details not shown.)

**2\.**Configure static routes or a routing
protocol to make sure the devices can reach each other. (Details not shown.)

**3\.**Configure Device B:

\# Enable the NQA server.

\<DeviceB\> system-view

\[DeviceB] nqa server enable

\# Configure a listening service to listen
to UDP port 9000 on IP address 10.2.2.2.

\[DeviceB] nqa server udp-echo
10.2.2.2 9000

**4\.**Configure Device A:

\# Create a voice operation.

\<DeviceA\> system-view

\[DeviceA] nqa entry admin test1

\[DeviceA-nqa-admin-test1] type
voice

\# Specify 10.2.2.2 as the destination IP
address.

\[DeviceA-nqa-admin-test1-voice]
destination ip 10.2.2.2

\# Set the destination port number to 9000\.

\[DeviceA-nqa-admin-test1-voice]
destination port 9000

\[DeviceA-nqa-admin-test1-voice]
quit

\# Start the voice operation.

\[DeviceA] nqa schedule admin test1
start-time now lifetime forever

\# After the voice operation runs for a
period of time, stop the operation.

\[DeviceA] undo nqa schedule
admin test1

#### Verifying the configuration

\# Display the most recent result of the voice
operation.

\[DeviceA] display nqa result admin
test1

NQA entry (admin admin, tag
test1) test results:

    Send operation times:
1000           Receive response times: 1000

    Min/Max/Average round trip
time: 31/1328/33

    Square-Sum of round trip
time: 2844813

    Last packet received time:
2019-06-13 09:49:31.1

  Extended results:

    Packet loss ratio: 0%

    Failures due to timeout: 0

    Failures due to internal
error: 0

    Failures due to other
errors: 0

Packets out of sequence: 0

    Packets arrived late: 0

  Voice results:

   RTT number: 1000

    Min positive SD:
1                     Min positive DS: 1

    Max positive SD:
204                   Max positive DS: 1297

    Positive SD number:
257                Positive DS number: 259

    Positive SD sum:
759                   Positive DS sum: 1797

    Positive SD average:
2                 Positive DS average: 6

    Positive SD square-sum:
54127          Positive DS square-sum: 1691967

    Min negative SD:
1                     Min negative DS: 1

    Max negative SD:
203                   Max negative DS: 1297

    Negative SD number:
255                Negative DS number: 259

    Negative SD sum:
759                   Negative DS sum: 1796

    Negative SD average:
2                 Negative DS average: 6

    Negative SD square-sum:
53655          Negative DS square-sum: 1691776

    SD average: 2                          DS
average: 6

  One way results:

    Max SD delay:
343                      Max DS delay: 985

    Min SD delay:
343                      Min DS delay: 985

    Number of SD delay: 1                  Number
of DS delay: 1

    Sum of SD delay:
343                   Sum of DS delay: 985

    Square-Sum of SD delay:
117649         Square-Sum of DS delay: 970225

    SD lost packets:
0                   DS lost packets: 0

    Lost packets for unknown
reason: 0

  Voice scores:

    MOS value:
4.38                        ICPIF value: 0

\# Display the statistics of the voice
operation.

\[DeviceA] display nqa
statistics admin test1

NQA entry (admin admin, tag test1)
test statistics:

  NO. : 1

 

    Start time: 2019-06-13
09:45:37.8

    Life time: 331 seconds

    Send operation times:
4000           Receive response times: 4000

    Min/Max/Average round trip
time: 15/1328/32

    Square-Sum of round trip
time: 7160528

  Extended results:

    Packet loss ratio: 0%

    Failures due to timeout: 0

    Failures due to internal
error: 0

    Failures due to other
errors: 0

Packets out of sequence: 0

    Packets arrived late: 0

  Voice results:

   RTT number: 4000

    Min positive SD:
1                     Min positive DS: 1

    Max positive SD:
360                   Max positive DS: 1297

    Positive SD number:
1030               Positive DS number: 1024

    Positive SD sum:
4363                  Positive DS sum: 5423

    Positive SD average:
4                 Positive DS average: 5

    Positive SD square-sum:
497725         Positive DS square-sum: 2254957

    Min negative SD:
1                     Min negative DS: 1

    Max negative SD:
360                   Max negative DS: 1297

    Negative SD number:
1028               Negative DS number: 1022

    Negative SD sum:
1028                  Negative DS sum: 1022

    Negative SD average:
4                 Negative DS average: 5

    Negative SD square-sum:
495901         Negative DS square-sum: 5419

    SD average:
16                         DS average: 2

  One way results:

    Max SD delay:
359                      Max DS delay: 985

    Min SD delay:
0                        Min DS delay: 0

    Number of SD delay:
4                  Number of DS delay: 4

    Sum of SD delay:
1390                  Sum of DS delay: 1079

    Square-Sum of SD delay:
483202         Square-Sum of DS delay: 973651

    SD lost packets:
0                   DS lost packets: 0

    Lost packets for unknown
reason: 0

  Voice scores:

    Max MOS value:
4.38                    Min MOS value: 4.38

    Max ICPIF value:
0                     Min ICPIF value: 0

### Example: Configuring the DLSw operation

#### Network configuration

As shown in [Figure 14](#_Ref258568041), configure
a DLSw operation to test the response time of the DLSw device.

Figure 14 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705667_x_Img_x_png_13_2216108_294551_0.png)

‌

#### Procedure

\# Assign IP addresses to interfaces, as
shown in [Figure 14](#_Ref258568041).
(Details not shown.)

\# Configure static routes or a routing
protocol to make sure the devices can reach each other. (Details not shown.)

\# Create a DLSw operation.

\<DeviceA\> system-view

\[DeviceA] nqa entry admin test1

\[DeviceA-nqa-admin-test1] type dlsw

\# Specify 10.2.2.2 as the destination IP
address.

\[DeviceA-nqa-admin-test1-dlsw]
destination ip 10.2.2.2

\# Enable the saving of history records.

\[DeviceA-nqa-admin-test1-dlsw]
history-record enable

\[DeviceA-nqa-admin-test1-dlsw] quit

\# Start the DLSw operation.

\[DeviceA] nqa schedule admin test1
start-time now lifetime forever

\# After the DLSw operation runs for a
period of time, stop the operation.

\[DeviceA] undo nqa schedule admin test1

#### Verifying the configuration

\# Display the most recent result of the DLSw
operation.

\[DeviceA] display nqa result admin
test1

NQA entry (admin admin, tag test1)
test results:

    Send operation times:
1              Receive response times: 1

    Min/Max/Average round trip time:
19/19/19

    Square-Sum of round trip time:
361

    Last succeeded probe time: 2019-11-22
10:40:27.7

  Extended results:

    Packet loss ratio: 0%

    Failures due to timeout: 0

    Failures due to disconnect: 0

    Failures due to no connection: 0

    Failures due to internal error: 0

    Failures due to other errors: 0

\# Display the history records of the DLSw operation.

\[DeviceA] display nqa history admin
test1

NQA entry (admin admin, tag test1)
history records:

  Index      Response    
Status           Time

  1          19          
Succeeded        2019-11-22 10:40:27.7

The output shows that the response time of
the DLSw device is 19 milliseconds.

### Example: Configuring the path jitter operation

#### Network configuration

As shown in [Figure 15](#_Ref335833853),
configure a path jitter operation to test the round trip time and jitters from Device
A to Device B and Device C.

Figure 15 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705668_x_Img_x_png_14_2216108_294551_0.png)

‌

#### Procedure

\# Assign IP addresses to interfaces, as shown in [Figure 15](#_Ref335833853).
(Details not shown.)

\# Configure static routes or a routing
protocol to make sure the devices can reach each other. (Details not shown.)

\# Execute the ip ttl-expires
enable command on Device B and execute the ip unreachables
enable command on Device C.

\# Create a path jitter operation.

\<DeviceA\> system-view

\[DeviceA] nqa entry admin test1

\[DeviceA-nqa-admin-test1] type
path-jitter

\# Specify 10.2.2.2 as the destination IP
address of ICMP echo requests.

\[DeviceA-nqa-admin-test1-path-jitter]
destination ip 10.2.2.2

\# Configure the path jitter operation to
repeat every 10000 milliseconds.

\[DeviceA-nqa-admin-test1-path-jitter]
frequency 10000

\[DeviceA-nqa-admin-test1-path-jitter]
quit

\# Start the path jitter operation.

\[DeviceA] nqa schedule admin test1
start-time now lifetime forever

\# After the path jitter operation runs for
a period of time, stop the operation.

\[DeviceA] undo nqa schedule admin
test1

#### Verifying the configuration

\# Display the most recent result of the
path jitter operation.

\[DeviceA] display nqa result admin
test1

NQA entry (admin admin, tag test1)
test results:

  Hop IP 10.1.1.2

    Basic Results

      Send operation times:
10             Receive response times: 10

      Min/Max/Average round trip
time: 9/21/14

      Square-Sum of round trip time:
2419

    Extended Results

      Failures due to timeout: 0

      Failures due to internal error:
0

      Failures due to other errors: 0

      Packets out of sequence: 0

      Packets arrived late: 0

    Path-Jitter Results

      Jitter number: 9

        Min/Max/Average jitter:
1/10/4

      Positive jitter number: 6

        Min/Max/Average positive
jitter: 1/9/4

        Sum/Square-Sum positive
jitter: 25/173

      Negative jitter number: 3

        Min/Max/Average negative
jitter: 2/10/6

        Sum/Square-Sum positive
jitter: 19/153

 

  Hop IP 10.2.2.2

    Basic Results

      Send operation times:
10             Receive response times: 10

      Min/Max/Average round trip
time: 15/40/28

      Square-Sum of round trip time: 4493

    Extended Results

      Failures due to timeout: 0

      Failures due to internal error:
0

      Failures due to other errors: 0

      Packets out of sequence: 0

      Packets arrived late: 0

    Path-Jitter Results

      Jitter number: 9

        Min/Max/Average jitter:
1/10/4

      Positive jitter number: 6

        Min/Max/Average positive
jitter: 1/9/4

        Sum/Square-Sum positive
jitter: 25/173

      Negative jitter number: 3

        Min/Max/Average negative
jitter: 2/10/6

        Sum/Square-Sum positive
jitter: 19/153

### Example: Configuring NQA collaboration

#### Network configuration

As shown in [Figure 16](#_Ref173574927), configure
a static route to Switch C with Switch B as the next hop on Switch A. Associate
the static route, a track entry, and an ICMP echo operation to monitor the
state of the static route.

Figure 16 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705669_x_Img_x_png_15_2216108_294551_0.png)

‌

#### Procedure

**1\.**Assign IP addresses to interfaces, as shown
in [Figure 16](#_Ref173574927).
(Details not shown.)

**2\.**On Switch A, configure a static route, and
associate the static route with track entry 1\.

\<SwitchA\> system-view

\[SwitchA] ip route-static
10.1.1.2 24 10.2.1.1 track 1

**3\.**On Switch A, configure an ICMP echo
operation:

\# Create an NQA operation with
administrator name admin and operation tag test1.

\[SwitchA] nqa entry admin test1

\# Configure the NQA operation type as ICMP
echo.

\[SwitchA-nqa-admin-test1] type
icmp-echo

\# Specify 10.2.1.1 as the destination IP
address.

\[SwitchA-nqa-admin-test1-icmp-echo]
destination ip 10.2.1.1

\# Configure the operation to repeat every
100 milliseconds.

\[SwitchA-nqa-admin-test1-icmp-echo]
frequency 100

\# Create reaction entry 1\. If the number
of consecutive probe failures reaches 5, collaboration is triggered.

\[SwitchA-nqa-admin-test1-icmp-echo]
reaction 1 checked-element probe-fail threshold-type consecutive 5 action-type
trigger-only

\[SwitchA-nqa-admin-test1-icmp-echo]
quit

\# Start the ICMP operation.

\[SwitchA] nqa schedule admin test1
start-time now lifetime forever

**4\.**On Switch A, create track entry 1, and associate
it with reaction entry 1 of the NQA operation.

\[SwitchA] track 1 nqa entry
admin test1 reaction 1

#### Verifying the configuration

\# Display information about all the track entries
on Switch A.

\[SwitchA] display track all

Track ID: 1

  State: Positive

  Duration: 0 days 0 hours 0 minutes 0
seconds

  Notification delay: Positive 0,
Negative 0 (in seconds)

  Tracked object:

    NQA entry: admin test1

    Reaction: 1

\# Display brief information about active
routes in the routing table on Switch A.

\[SwitchA] display ip routing-table

 

Destinations : 13        Routes : 13

 

Destination/Mask    Proto  Pre 
Cost         NextHop         Interface

0.0.0.0/32          Direct 0   
0            127.0.0.1       InLoop0

10.1.1.0/24         Static 60  
0            10.2.1.1        Vlan3

10.2.1.0/24         Direct 0   
0            10.2.1.2        Vlan3

10.2.1.0/32         Direct 0   
0            10.2.1.2        Vlan3

10.2.1.2/32         Direct 0   
0            127.0.0.1       InLoop0

10.2.1.255/32       Direct 0    0            10.2.1.2       
Vlan3

127.0.0.0/8         Direct 0   
0            127.0.0.1       InLoop0

127.0.0.0/32        Direct 0   
0            127.0.0.1       InLoop0

127.0.0.1/32        Direct 0   
0            127.0.0.1       InLoop0

127.255.255.255/32  Direct 0   
0            127.0.0.1       InLoop0

224.0.0.0/4         Direct 0   
0            0.0.0.0         NULL0

224.0.0.0/24        Direct 0   
0            0.0.0.0         NULL0

255.255.255.255/32  Direct 0   
0            127.0.0.1       InLoop0

The output shows that the static route with
the next hop 10.2.1.1 is active, and the status of the track entry is positive.

\# Remove the IP address of VLAN-interface 3
on Switch B.

\<SwitchB\> system-view

\[SwitchB] interface vlan-interface 3

\[SwitchB-Vlan-interface3] undo ip
address

\# Display information about all the track entries
on Switch A.

\[SwitchA] display track all

Track ID: 1

  State: Negative

  Duration: 0 days 0 hours 0 minutes 0
seconds

  Notification delay: Positive 0,
Negative 0 (in seconds)

  Tracked object:

    NQA entry: admin test1

    Reaction: 1

\# Display brief information about active
routes in the routing table on Switch A.

\[SwitchA] display ip routing-table

 

Destinations : 12        Routes : 12

 

Destination/Mask    Proto  Pre 
Cost         NextHop         Interface

0.0.0.0/32          Direct 0   
0            127.0.0.1       InLoop0

10.2.1.0/24         Direct 0   
0            10.2.1.2        Vlan3

10.2.1.0/32         Direct 0   
0            10.2.1.2        Vlan3

10.2.1.2/32         Direct 0   
0            127.0.0.1       InLoop0

10.2.1.255/32       Direct 0   
0            10.2.1.2        Vlan3

127.0.0.0/8         Direct 0   
0            127.0.0.1       InLoop0

127.0.0.0/32        Direct 0   
0            127.0.0.1       InLoop0

127.0.0.1/32        Direct 0   
0            127.0.0.1       InLoop0

127.255.255.255/32  Direct 0   
0            127.0.0.1       InLoop0

224.0.0.0/4         Direct 0   
0            0.0.0.0         NULL0

224.0.0.0/24        Direct 0   
0            0.0.0.0         NULL0

255.255.255.255/32  Direct 0   
0            127.0.0.1       InLoop0

The output shows that the static route does
not exist, and the status of the track entry is negative.

### Example: Configuring the ICMP template

#### Network configuration

As shown in [Figure 17](#_Ref346538952), configure
an ICMP template for a feature to perform the ICMP echo operation from Device A
to Device B.

Figure 17 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705670_x_Img_x_png_16_2216108_294551_0.png)

‌

#### Procedure

\# Assign IP addresses to interfaces, as
shown in [Figure 17](#_Ref346538952).
(Details not shown.)

\# Configure static routes or a routing
protocol to make sure the devices can reach each other. (Details not shown.)

\# Create ICMP template icmp.

\<DeviceA\> system-view

\[DeviceA] nqa template icmp icmp

\# Specify 10.2.2.2 as the destination IP
address of ICMP echo requests.

\[DeviceA-nqatplt-icmp-icmp] destination
ip 10.2.2.2

\# Set the probe timeout time to 500
milliseconds for the ICMP echo operation.

\[DeviceA-nqatplt-icmp-icmp] probe timeout
500

\# Configure the ICMP echo operation to
repeat every 3000 milliseconds.

\[DeviceA-nqatplt-icmp-icmp] frequency
3000

\# Configure the NQA client to notify the
feature of the successful operation event if the number of consecutive
successful probes reaches 2\.

\[DeviceA-nqatplt-icmp-icmp] reaction
trigger probe-pass 2

\# Configure the NQA client to notify the
feature of the operation failure if the number of consecutive failed probes
reaches 2\.

\[DeviceA-nqatplt-icmp-icmp] reaction
trigger probe-fail 2

### Example: Configuring the DNS template

#### Network configuration

As shown in [Figure 18](#_Ref346539145), configure
a DNS template for a feature to perform the DNS operation. The operation tests whether
Device A can perform the address resolution through the DNS server.

Figure 18 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705671_x_Img_x_png_17_2216108_294551_0.png)

‌

#### Procedure

\# Assign IP addresses to interfaces, as
shown in [Figure 18](#_Ref346539145).
(Details not shown.)

\# Configure static routes or a routing
protocol to make sure the devices can reach each other. (Details not shown.)

\# Create DNS template dns.

\<DeviceA\> system-view

\[DeviceA] nqa template dns dns

\# Specify the IP address of the DNS server
(10.2.2.2) as the destination IP address.

\[DeviceA-nqatplt-dns-dns] destination
ip 10.2.2.2

\# Specify host.com as
the domain name to be translated.

\[DeviceA-nqatplt-dns-dns] resolve-target
host.com

\# Set the domain name resolution type to
type A.

\[DeviceA-nqatplt-dns-dns]
resolve-type A

\# Specify 3.3.3.3 as the expected IP
address.

\[DeviceA-nqatplt-dns-dns] expect ip
3.3.3.3

\# Configure the NQA client to notify the
feature of the successful operation event if the number of consecutive
successful probes reaches 2\.

\[DeviceA-nqatplt-dns-dns] reaction
trigger probe-pass 2

\# Configure the NQA client to notify the
feature of the operation failure if the number of consecutive failed probes
reaches 2\.

\[DeviceA-nqatplt-dns-dns] reaction
trigger probe-fail 2

### Example: Configuring the TCP template

#### Network configuration

As shown in [Figure 19](#_Ref346539221), configure
a TCP template for a feature to perform the TCP operation. The operation tests
whether Device A can establish a TCP connection to Device B.

Figure 19 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705672_x_Img_x_png_18_2216108_294551_0.png)

‌

#### Procedure

**1\.**Assign IP addresses to interfaces, as shown
in [Figure 19](#_Ref346539221).
(Details not shown.)

**2\.**Configure static routes or a routing
protocol to make sure the devices can reach each other. (Details not shown.)

**3\.**Configure Device B:

\# Enable the NQA server.

\<DeviceB\> system-view

\[DeviceB] nqa server enable

\# Configure a listening service to listen
to TCP port 9000 on IP address 10.2.2.2.

\[DeviceB] nqa server
tcp-connect 10.2.2.2 9000

**4\.**Configure Device A:

\# Create TCP template tcp.

\<DeviceA\> system-view

\[DeviceA] nqa template tcp tcp

\# Specify 10.2.2.2 as the destination IP
address.

\[DeviceA-nqatplt-tcp-tcp]
destination ip 10.2.2.2

\# Set the destination port number to
9000\.

\[DeviceA-nqatplt-tcp-tcp] destination
port 9000

\# Configure the NQA client to notify the
feature of the successful operation event if the number of consecutive
successful probes reaches 2\.

\[DeviceA-nqatplt-tcp-tcp]
reaction trigger probe-pass 2

\# Configure the NQA client to notify the
feature of the operation failure if the number of consecutive failed probes
reaches 2\.

\[DeviceA-nqatplt-tcp-tcp]
reaction trigger probe-fail 2

### Example: Configuring the TCP half open template

#### Network configuration

As shown in [Figure 20](#_Ref418600690),
configure a TCP half open template for a feature to test whether Device B can
provide the TCP service for Device A.

Figure 20 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705673_x_Img_x_png_19_2216108_294551_0.png)

‌

#### Procedure

**1\.**Assign IP addresses to interfaces, as shown in
[Figure 20](#_Ref418600690).
(Details not shown.)

**2\.**Configure static routes or a routing
protocol to make sure the devices can reach each other. (Details not shown.)

**3\.**Configure Device A:

\# Create TCP half open template test.

\<DeviceA\> system-view

\[DeviceA] nqa template
tcphalfopen test

\# Specify 10.2.2.2 as the destination IP
address.

\[DeviceA-nqatplt-tcphalfopen-test]
destination ip 10.2.2.2

\# Configure the NQA client to notify the
feature of the successful operation event if the number of consecutive
successful probes reaches 2\.

\[DeviceA-nqatplt-tcphalfopen-test]
reaction trigger probe-pass 2

\# Configure the NQA client to notify the
feature of the operation failure if the number of consecutive failed probes
reaches 2\.

\[DeviceA-nqatplt-tcphalfopen-test]
reaction trigger probe-fail 2

### Example: Configuring the UDP template

#### Network configuration

As shown in [Figure 21](#_Ref398624434),
configure a UDP template for a feature to perform the UDP operation. The
operation tests whether Device A can receive a response from Device B.

Figure 21 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705675_x_Img_x_png_20_2216108_294551_0.png)

‌

#### Procedure

**1\.**Assign IP addresses to
interfaces, as shown in [Figure 21](#_Ref398624434).
(Details not shown.)

**2\.**Configure static routes or a routing protocol
to make sure the devices can reach each other. (Details not shown.)

**3\.**Configure Device B:

\# Enable the NQA server.

\<DeviceB\> system-view

\[DeviceB] nqa server enable

\# Configure a listening service to listen
to UDP port 9000 on IP address 10.2.2.2.

\[DeviceB] nqa server udp-echo
10.2.2.2 9000

**4\.**Configure Device A:

\# Create UDP template udp.

\<DeviceA\> system-view

\[DeviceA] nqa template udp udp

\# Specify 10.2.2.2 as the destination IP
address.

\[DeviceA-nqatplt-udp-udp]
destination ip 10.2.2.2

\# Set the destination port number to
9000\.

\[DeviceA-nqatplt-udp-udp] destination
port 9000

\# Configure the NQA client to notify the
feature of the successful operation event if the number of consecutive
successful probes reaches 2\.

\[DeviceA-nqatplt-udp-udp]
reaction trigger probe-pass 2

\# Configure the NQA client to notify the
feature of the operation failure if the number of consecutive failed probes
reaches 2\.

\[DeviceA-nqatplt-udp-udp]
reaction trigger probe-fail 2

### Example: Configuring the HTTP template

#### Network configuration

As shown in [Figure 22](#_Ref346539273), configure
an HTTP template for a feature to perform the HTTP operation. The operation tests
whether the NQA client can get data from the HTTP server.

Figure 22 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705676_x_Img_x_png_21_2216108_294551_0.png)

‌

#### Procedure

\# Assign IP addresses to interfaces, as
shown in [Figure 22](#_Ref346539273).
(Details not shown.)

\# Configure static routes or a routing
protocol to make sure the devices can reach each other. (Details not shown.)

\# Create HTTP template http.

\<DeviceA\> system-view

\[DeviceA] nqa template http http

\# Specify http://10.2.2.2/index.htm
as the URL of the HTTP server.

\[DeviceA-nqatplt-http-http] url http://10.2.2.2/index.htm

\# Set the HTTP operation type to get.

\[DeviceA-nqatplt-http-http] operation
get

\# Configure the NQA client to notify the
feature of the successful operation event if the number of consecutive
successful probes reaches 2\.

\[DeviceA-nqatplt-http-http] reaction
trigger probe-pass 2

\# Configure the NQA client to notify the
feature of the operation failure if the number of consecutive failed probes
reaches 2\.

\[DeviceA-nqatplt-http-http] reaction
trigger probe-fail 2

### Example: Configuring the HTTPS template

#### Network configuration

As shown in [Figure 23](#_Ref418601629),
configure an HTTPS template for a feature to test whether the NQA client can get
data from the HTTPS server (Device B).

Figure 23 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705677_x_Img_x_png_22_2216108_294551_0.png)

‌

#### Procedure

\# Assign IP addresses to interfaces, as shown
in [Figure 23](#_Ref418601629).
(Details not shown.)

\# Configure static routes or a routing
protocol to make sure the devices can reach each other. (Details not shown.)

\# Configure an SSL client policy named abc on Device A, and make sure Device A can use the
policy to connect to the HTTPS server. (Details not shown.)

\# Create HTTPS template test.

\<DeviceA\> system-view

\[DeviceA] nqa template https https

\# Specify http://10.2.2.2/index.htm
as the URL of the HTTPS server.

\[DeviceA-nqatplt-https-https] url
https://10.2.2.2/index.htm

\# Specify SSL client policy abc for the HTTPS template.

\[DeviceA-nqatplt-https- https] ssl-client-policy
abc

\# Set the HTTPS operation type to get (the default HTTPS operation type).

\[DeviceA-nqatplt-https-https]
operation get

\# Set the HTTPS version to 1.0 (the default
HTTPS version).

 \[DeviceA-nqatplt-https-https]
version v1.0

\# Configure the NQA client to notify the
feature of the successful operation event if the number of consecutive
successful probes reaches 2\.

\[DeviceA-nqatplt-https-https]
reaction trigger probe-pass 2

\# Configure the NQA client to notify the
feature of the operation failure if the number of consecutive failed probes
reaches 2\.

\[DeviceA-nqatplt-https-https]
reaction trigger probe-fail 2

### Example: Configuring the FTP template

#### Network configuration

As shown in [Figure 24](#_Ref346539315), configure
an FTP template for a feature to perform the FTP operation. The operation tests
whether Device A can upload a file to the FTP server. The login username and
password are admin and systemtest,
respectively. The file to be transferred to the FTP server is config.txt.

Figure 24 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705678_x_Img_x_png_23_2216108_294551_0.png)

‌

#### Procedure

\# Assign IP addresses to interfaces, as
shown in [Figure 24](#_Ref346539315).
(Details not shown.)

\# Configure static routes or a routing
protocol to make sure the devices can reach each other. (Details not shown.)

\# Create FTP template ftp.

\<DeviceA\> system-view

\[DeviceA] nqa template ftp ftp

\# Specify the URL of the FTP server.

\[DeviceA-nqatplt-ftp-ftp] url
ftp://10.2.2.2

\# Specify 10.1.1.1 as the source IP
address.

\[DeviceA-nqatplt-ftp-ftp] source ip
10.1.1.1

\# Configure the device to upload file config.txt to the FTP server.

\[DeviceA-nqatplt-ftp-ftp] operation
put

\[DeviceA-nqatplt-ftp-ftp] filename
config.txt

\# Set the username to admin
for the FTP server login.

\[DeviceA-nqatplt-ftp-ftp] username
admin

\# Set the password to systemtest
for the FTP server login.

\[DeviceA-nqatplt-ftp-ftp] password
simple systemtest

\# Configure the NQA client to notify the
feature of the successful operation event if the number of consecutive
successful probes reaches 2\.

\[DeviceA-nqatplt-ftp-ftp] reaction
trigger probe-pass 2

\# Configure the NQA client to notify the
feature of the operation failure if the number of consecutive failed probes
reaches 2\.

\[DeviceA-nqatplt-ftp-ftp] reaction
trigger probe-fail 2

### Example: Configuring the RADIUS template

#### Network configuration

As shown in [Figure 25](#_Ref405906323),
configure a RADIUS template for a feature to test whether the RADIUS server
(Device B) can provide authentication service for Device A. The username and
password are admin and systemtest,
respectively. The shared key is 123456 for secure RADIUS
authentication.

Figure 25 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705679_x_Img_x_png_24_2216108_294551_0.png)

‌

#### Procedure

\# Assign IP addresses to interfaces, as
shown in [Figure 25](#_Ref405906323).
(Details not shown.)

\# Configure static routes or a routing
protocol to make sure the devices can reach each other. (Details not shown.)

\# Configure the RADIUS server. (Details not
shown.)

\# Create RADIUS template radius.

\<DeviceA\> system-view

\[DeviceA] nqa template radius radius

\# Specify 10.2.2.2 as the destination IP
address of the operation.

\[DeviceA-nqatplt-radius-radius]
destination ip 10.2.2.2

\# Set the username to admin.

\[DeviceA-nqatplt-radius-radius]
username admin

\# Set the password to systemtest.

\[DeviceA-nqatplt-radius-radius]
password simple systemtest

\# Set the shared key to 123456 in plain text for secure RADIUS authentication.

\[DeviceA-nqatplt-radius-radius] key
simple 123456

\# Configure the NQA client to notify the feature
of the successful operation event if the number of consecutive successful
probes reaches 2\.

\[DeviceA-nqatplt-radius-radius]
reaction trigger probe-pass 2

\# Configure the NQA client to notify the
feature of the operation failure if the number of consecutive failed probes
reaches 2\.

\[DeviceA-nqatplt-radius-radius]
reaction trigger probe-fail 2

### Example: Configuring the SSL template

#### Network configuration

As shown in [Figure 26](#_Ref418602704),
configure an SSL template for a feature to test whether Device A can establish
an SSL connection to the SSL server on Device B.

Figure 26 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705680_x_Img_x_png_25_2216108_294551_0.png)

‌

#### Procedure

\# Assign IP addresses to interfaces, as
shown in [Figure 26](#_Ref418602704).
(Details not shown.)

\# Configure static routes or a routing
protocol to make sure the devices can reach each other. (Details not shown.)

\# Configure an SSL client policy named abc on Device A, and make sure Device A can use the policy
to connect to the SSL server on Device B. (Details not shown.)

\# Create SSL template ssl.

\<DeviceA\> system-view

\[DeviceA] nqa template ssl ssl

\# Set the destination IP address and port
number to 10.2.2.2 and 9000,
respectively.

\[DeviceA-nqatplt-ssl-ssl] destination
ip 10.2.2.2

\[DeviceA-nqatplt-ssl-ssl] destination
port 9000

\# Specify SSL client policy abc for the SSL template.

\[DeviceA-nqatplt-ssl-ssl]
ssl-client-policy abc

\# Configure the NQA client to notify the
feature of the successful operation event if the number of consecutive
successful probes reaches 2\.

\[DeviceA-nqatplt-ssl-ssl] reaction
trigger probe-pass 2

\# Configure the NQA client to notify the
feature of the operation failure if the number of consecutive failed probes
reaches 2\.

\[DeviceA-nqatplt-ssl-ssl] reaction
trigger probe-fail 2

 

