
# Using ping, tracert, and system debugging

This chapter
covers ping, tracert, and information about debugging the system.

## Ping

### About ping

Use the ping utility to determine if an address
is reachable.

Ping sends ICMP echo requests
(ECHO-REQUEST) to the destination device. Upon receiving the requests, the
destination device responds with ICMP echo replies (ECHO-REPLY) to the source
device. The source device outputs statistics about the ping operation,
including the number of packets sent, number of echo replies received, and the
round-trip time. You can measure the network performance by analyzing these
statistics.

You can use the ping –r command to display the routers through which ICMP echo requests have
passed. The test procedure of ping –r is
as shown in [Figure 1](#_Ref510515186):

**1\.**The source device (Device A) sends an ICMP echo
request to the destination device (Device C) with the RR option empty.

**2\.**The intermediate device (Device B) adds the
IP address of its outbound interface (1.1.2.1) to the RR option of the ICMP
echo request, and forwards the packet.

**3\.**Upon receiving the request, the destination
device copies the RR option in the request and adds the IP address of its
outbound interface (1.1.2.2) to the RR option. Then the destination device sends
an ICMP echo reply.

**4\.**The intermediate device adds the IP address
of its outbound interface (1.1.1.2) to the RR option in the ICMP echo reply,
and then forwards the reply.

**5\.**Upon receiving the reply, the source device adds
the IP address of its inbound interface (1.1.1.1) to the RR option. The
detailed information of routes from Device A to Device C is formatted as:
1.1.1.1 \<-\> {1.1.1.2; 1.1.2.1} \<-\> 1.1.2.2.

Figure 1 Ping operation

![](01-System%20maintenance%20and%20debugging%20configuration.files/x_Img_x_png_0.png)

‌

### Using a ping command to test network connectivity

Perform the following tasks in any view:

·     Determine if an IPv4 address is reachable.

ping \[ ip ] \[ -a source-ip \| -c count \| -f \| -h ttl \| -i interface-type
interface-number \| -m interval \| -n \| -p pad \| -q \| -r \| -s packet-size \| -t timeout \| -tos tos \| -v \| -vpn-instance vpn-instance-name ] \* host

Increase the timeout time (indicated by
the -t keyword) on a low-speed network.

·     Determine if an IPv6 address is reachable.

ping ipv6 \[ -a source-ipv6 \| -c count \| -i interface-type
interface-number \| -m interval \| -q \| -s packet-size \| -t timeout \| -tc traffic-class
\| -v \| -vpn-instance vpn-instance-name
] \* host

Increase the timeout time (indicated by
the -t keyword) on a low-speed network.

### Example: Using the ping utility

#### Network configuration

As shown in [Figure 2](#_Ref219607724), determine
if Device A and Device C can reach each other.

Figure 2 Network diagram

![](01-System%20maintenance%20and%20debugging%20configuration.files/x_Img_x_png_1.png)

 

#### Procedure

\# Test the connectivity between Device A and
Device C.

\<DeviceA\> ping 1.1.2.2

Ping 1.1.2.2 (1.1.2.2): 56 data bytes,
press CTRL\+C to break

56 bytes from 1.1.2.2: icmp\_seq\=0
ttl\=254 time\=2.137 ms

56 bytes from 1.1.2.2: icmp\_seq\=1
ttl\=254 time\=2.051 ms

56 bytes from 1.1.2.2: icmp\_seq\=2
ttl\=254 time\=1.996 ms

56 bytes from 1.1.2.2: icmp\_seq\=3
ttl\=254 time\=1.963 ms

56 bytes from 1.1.2.2: icmp\_seq\=4
ttl\=254 time\=1.991 ms

 

\--- Ping statistics for 1.1.2.2 \---

5 packet(s) transmitted, 5 packet(s)
received, 0.0% packet loss

round-trip min/avg/max/std-dev \=
1.963/2.028/2.137/0.062 ms

The output shows the following information:

·     Device A sends five ICMP packets to Device C and
Device A receives five ICMP packets.

·     No ICMP packet is lost.

·     The route is reachable.

## Tracert

### About tracert

Tracert (also called Traceroute) enables retrieval
of the IP addresses of Layer 3 devices in the path to a destination. In the
event of network failure, use tracert to test network connectivity and identify
failed nodes.

Figure 3 Tracert
operation

![](01-System%20maintenance%20and%20debugging%20configuration.files/x_Img_x_png_2.png)

 

Tracert uses received ICMP error messages
to get the IP addresses of devices. Tracert works as shown in [Figure 3](#_Ref372116082):

**1\.**The source device sends a UDP packet with a
TTL value of 1 to the destination device. The destination UDP port is not used
by any application on the destination device.

**2\.**The first hop (Device B, the first Layer 3
device that receives the packet) responds by sending a TTL-expired ICMP error
message to the source, with its IP address (1.1.1.2) encapsulated. This way, the
source device can get the address of the first Layer 3 device (1.1.1.2).

**3\.**The source device sends a packet with a TTL
value of 2 to the destination device.

**4\.**The second hop (Device C) responds with a TTL-expired
ICMP error message, which gives the source device the address of the second
Layer 3 device (1.1.2.2).

**5\.**This process continues until a packet sent
by the source device reaches the ultimate destination device. Because no
application uses the destination port specified in the packet, the destination
device responds with a port-unreachable ICMP message to the source device, with
its IP address encapsulated. This way, the source device gets the IP address of
the destination device (1.1.3.2).

**6\.**The source device determines that:

¡     The
packet has reached the destination device after receiving the port-unreachable
ICMP message.

¡     The
path to the destination device is 1.1.1.2 to 1.1.2.2 to 1.1.3.2.

### Prerequisites

Before you use a tracert command, perform
the tasks in this section.

For an IPv4 network:

·     Enable sending of ICMP timeout packets on the intermediate
devices (devices between the source and destination devices). If the
intermediate devices are H3C devices, execute the ip ttl-expires
enable command on the devices. For more information
about this command, see Layer 3—IP Services Command
Reference.

·     Enable sending of ICMP destination unreachable
packets on the destination device. If the destination device is an H3C device,
execute the ip unreachables enable
command. For more information about this command, see Layer
3—IP Services Command Reference.

For an IPv6 network:

·     Enable sending of ICMPv6 timeout packets on the
intermediate devices (devices between the source and destination devices). If
the intermediate devices are H3C devices, execute the ipv6
hoplimit-expires enable command on the devices.
For more information about this command, see Layer 3—IP
Services Command Reference.

·     Enable sending of ICMPv6 destination unreachable
packets on the destination device. If the destination device is an H3C device,
execute the ipv6 unreachables enable command. For more information about this command, see Layer 3—IP Services Command Reference.

### Using a tracert command to identify failed or all nodes in a path

Perform the following tasks in any view:

·     Trace the route to an IPv4 destination.

tracert \[ -a source-ip \| –e \| -f first-ttl \| -m max-ttl \| -p port \| -q packet-number \| -t tos \| -vpn-instance vpn-instance-name \[ -resolve-as { global \| none \| vpn } ] \| -w timeout ] \* host

·     Trace the route to an IPv6 destination.

tracert ipv6 \[ -a source-ipv6 \| –e \| -f first-hop \| -m max-hops \| -p port \| -q packet-number \| -t traffic-class \| -vpn-instance vpn-instance-name \[ -resolve-as { global \| none \| vpn } ] \| -w timeout ] \* host

### Example: Using the tracert utility

#### Network configuration

As shown in [Figure 4](#_Ref308018526),
Device A failed to Telnet to Device C.

Test the network connectivity between Device
A and Device C. If they cannot reach each other, locate the failed nodes in the
network.

Figure 4 Network diagram

![](01-System%20maintenance%20and%20debugging%20configuration.files/x_Img_x_png_3.png)

 

#### Procedure

**1\.**Configure IP addresses for the devices as
shown in [Figure 4](#_Ref308018526).

**2\.**Configure a static route on Device A.

\<DeviceA\> system-view

\[DeviceA] ip route-static
0.0.0.0 0.0.0.0 1.1.1.2

**3\.**Test connectivity between Device A and
Device C.

\[DeviceA] ping 1.1.2.2

Ping 1.1.2.2(1.1.2.2): 56 -data
bytes, press CTRL\+C to break

Request time out

Request time out

Request time out

Request time out

Request time out

 

\--- Ping statistics for 1.1.2.2
\---

5 packet(s) transmitted,0
packet(s) received,100.0% packet loss

The output shows that Device A and Device
C cannot reach each other.

**4\.**Identify failed nodes:

\# Enable sending of ICMP timeout packets
on Device B.

\<DeviceB\> system-view

\[DeviceB] ip ttl-expires
enable

\# Enable sending of ICMP destination
unreachable packets on Device C.

\<DeviceC\> system-view

\[DeviceC] ip unreachables
enable

\# Identify failed nodes.

\[DeviceA] tracert 1.1.2.2

traceroute to 1.1.2.2 (1.1.2.2)
30 hops at most,40 bytes each packet, press CTRL\+C to break

 1  1.1.1.2 (1.1.1.2) 1 ms 2 ms
1 ms

 2  \* \* \*

 3  \* \* \*

 4  \* \* \*

 5

\[DeviceA]

The output shows that Device A can reach
Device B but cannot reach Device C. An error has occurred on the connection
between Device B and Device C.

**5\.**To identify the cause of the issue, execute
the following commands on Device A and Device C:

¡     Execute
the
debugging ip icmp command and verify that Device
A and Device C can send and receive the correct ICMP packets.

¡     Execute
the display
ip routing-table command to verify that Device A
and Device C have a route to each other.

## System debugging

### About system debugging

The device supports debugging for the
majority of protocols and features, and provides debugging information to help users
diagnose errors.

The following switches control the display
of debugging information:

·     Module debugging switch—Controls whether to generate the module-specific debugging
information.

·     Screen output switch—Controls whether to display the debugging information on a certain
screen. Use terminal monitor and terminal logging
level commands to turn on the screen output
switch. For more information about these two commands, see Network Management and Monitoring Command Reference.

As shown in [Figure 5](#_Ref170724172), the
device can provide debugging for the three modules 1, 2, and 3\. The debugging
information can be output on a terminal only when both the module debugging
switch and the screen output switch are turned on.

Debugging information is typically displayed
on a console. You can also send debugging information to other destinations.
For more information, see "Configuring the information center."

Figure 5 Relationship between the module and
screen output switch

![](01-System%20maintenance%20and%20debugging%20configuration.files/x_Img_x_png_4.png)

 

### Configuring debugging

#### Restrictions and guidelines

| CAUTION | CAUTION:  Output of excessive debugging messages increases the CPU usage and downgrades the system performance. To guarantee system performance, enable debugging only for modules that are in an exceptional condition. |
| --- | --- |

 

Enable debugging for modules for
troubleshooting purposes. When debugging is complete, use the undo debugging
all command to disable all the debugging
functions.

#### Procedure

**1\.**Enable debugging for a module.

debugging module-name \[ option ]

By default, debugging is disabled for all modules.

This command is available in user view.

### Display and maintenance commands for debugging

Execute display
commands in any view.

 

| Task | Command |
| --- | --- |
| Display the enabled debugging features. | display debugging \[ module-name ] |

‌

