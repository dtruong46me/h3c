
# Configuring the packet capture

## About packet capture

The packet capture feature captures
incoming packets on the device. The feature displays the captured packets on
the terminal in real time, and allows you to save the captured packets to a
.pcap file for future analysis. Packet capture can read both .pcap and .pcapng
files. 

## Building a capture filter rule

### Filter rule elements

Packet capture supports using a capture
filter rule to filter packets to be captured or using a display filter rule to
filter packets to be displayed.

A filter rule is represented by a filter
expression. A filter expression contains a keyword string or multiple keyword
strings that are connected by operators.

Keywords include the following types:

·     Qualifiers—Fixed keyword strings. To use a qualifier, you must enter the
qualifier literally as shown.

·     Variables—Values assigned in the required format.

Operators include the following types:

·     Logical operators—Perform logical
operations, such as the AND operation.

·     Arithmetic operators—Perform arithmetic
operations, such as the ADD operation.

·     Relational operators—Indicate the
relation between keyword strings. For example, the \= operator indicates equality.

For more information about capture and
display filters, go to the following websites:

·     <http://wiki.wireshark.org/CaptureFilters>

·     <http://wiki.wireshark.org/DisplayFilters>

### Capture filter rule keywords

#### Qualifiers

Table 1 Qualifiers for capture filter rules

| Category | Description | Examples |
| --- | --- | --- |
| Protocol | Matches a protocol.  If you do not specify a protocol qualifier, the filter matches any supported protocols. | ·     arp—Matches ARP.  ·     icmp—Matches ICMP.  ·     ip—Matches IPv4.  ·     ip6—Matches IPv6.  ·     tcp—Matches TCP.  ·     udp—Matches UDP. || Direction | Matches packets based on its source or destination location (an IP address or port number).  If you do not specify a direction qualifier, the src or dst qualifier applies. For example, port 23 is equivalent to src or dst port 23. | ·     src—Matches the source IP address field.  ·     dst—Matches the destination IP address field.  ·     src or dst—Matches the source or destination IP address field. || Type | Specifies the direction type.  The host qualifier applies if you do not specify any type qualifier. For example, src 2.2.2.2 is equivalent to src host 2.2.2.2. | ·     host—Matches the IP address of a host.  ·     net—Matches an IP subnet.  ·     port—Matches a service port number.  ·     portrange—Matches a service port range. || Others | Any other qualifiers than the previously described qualifiers. | ·     broadcast—Matches broadcast packets.  ·     multicast—Matches multicast and broadcast packets.  ·     less—Matches packets that are less than or equal to a specific size.  ·     greater—Matches packets that are greater than or equal to a specific size.  ·     len—Matches the packet length.  ·     vlan—Matches VLAN packets. |




 

#### Variables

A capture filter variable must be modified
by one or more qualifiers. 

The broadcast, multicast, and all protocol qualifiers cannot modify
variables. The other qualifiers must be followed by variables.

Table 2 Variable types for capture filter
rules

| Variable type | Description | | Examples |
| --- | --- | --- | --- |
| Integer | Represented in binary, octal, decimal, or hexadecimal notation. | | The port 23 expression matches traffic sent to or from port number 23\. || Integer range | Represented by hyphenated integers. | | The portrange 100-200 expression matches traffic sent to or from any ports in the range of 100 to 200\. || IPv4 address | Represented in dotted decimal notation. | | The src 1.1.1.1 expression matches traffic sent from the IPv4 host at 1.1.1.1. || IPv6 address | Represented in colon hexadecimal notation. | | The dst host 1::1 expression matches traffic sent to the IPv6 host at 1::1. || IPv4 subnet | Represented by an IPv4 network ID or an IPv4 address with a mask. | | Both of the following expressions match traffic sent to or from the IPv4 subnet 1.1.1.0/24:  ·     src 1.1.1.  ·     src net 1.1.1.0/24. || IPv6 network segment | | Represented by an IPv6 address with a prefix length. | The dst net 1::/64 expression matches traffic sent to the IPv6 network 1::/64. ||  |  |  |  |







 

### Capture filter rule operators

#### Logical operators

Logical operators are left associative.
They group from left to right. The not operator has
the highest priority. The and and or operators have the same priority.

Table 3 Logical operators for capture filter
rules

| Nonalphanumeric symbol | Alphanumeric symbol | Description |
| --- | --- | --- |
| ! | not | Reverses the result of a condition.  Use this operator to capture traffic that matches the opposite value of a condition.  For example, to capture non-HTTP traffic, use not port 80. || \&\& | and | Joins two conditions.  Use this operator to capture traffic that matches both conditions.  For example, to capture non-HTTP traffic that is sent to or from 1.1.1.1, use host 1.1.1.1 and not port 80. || \|\| | or | Joins two conditions.  Use this operator to capture traffic that matches either of the conditions.  For example, to capture traffic that is sent to or from 1.1.1.1 or 2.2.2.2, use host 1.1.1.1 or host 2.2.2.2. |



 

#### Arithmetic operators

Table 4 Arithmetic operators for capture
filter rules

| Nonalphanumeric symbol | Description |
| --- | --- |
| \+ | Adds two values. || \- | Subtracts one value from another. || \* | Multiplies one value by another. || / | Divides the value on the left by the value on the right. || \& | Returns the result of the bitwise AND operation on two integral values in binary form. || \| | Returns the result of the bitwise OR operation on two integral values in binary form. || \<\< | Performs the bitwise left shift operation on the operand to the left of the operator. The right-hand operand specifies the number of bits to shift. || \>\> | Performs the bitwise right shift operation on the operand to the left of the operator. The right-hand operand specifies the number of bits to shift. || \[ ] | Specifies a byte offset relative to a protocol layer. This offset indicates the byte where the matching begins.  You must enclose the offset value in the brackets and specify a protocol qualifier. For example, ip\[6] matches the seventh byte of payload in IPv4 packets (the byte that is six bytes away from the beginning of the IPv4 payload). |









 

#### Relational operators

Table 5 Relational operators for capture
filter rules

| Nonalphanumeric symbol | Description |
| --- | --- |
| \= | Equal to.  For example, ip\[6]\=0x1c matches an IPv4 packet if its seventh byte of payload is equal to 0x1c. || !\= | Not equal to.  For example, len!\=60 matches a packet if its length is not equal to 60 bytes. || \> | Greater than.  For example, len\>100 matches a packet if its length is greater than 100 bytes. || \< | Less than.  For example, len\<100 matches a packet if its length is less than 100 bytes. || \>\= | Greater than or equal to.  For example, len\>\=100 matches a packet if its length is greater than or equal to 100 bytes. || \<\= | Less than or equal to.  For example, len\<\=100 matches a packet if its length is less than or equal to 100 bytes. |






 

### Capture filter rule expressions

#### Logical expression

Use this type of expression to capture
packets that match the result of logical operations.

Logical expressions contain keywords and
logical operators. For example:

·     not port 23 and not port 22—Captures packets with a port number that is not 23 or 22\. 

·     port 23 or icmp—Captures packets with a port number 23 or ICMP packets.

In a logical expression, a qualifier can
modify more than one variable connected by its nearest logical operator. For
example, to capture packets sourced from IPv4 address 192.168.56.1 or IPv4
network 192.168.27, use either of the following expressions:

·     src 192.168.56.1 or
192.168.27.

·     src 192.168.56.1 or src
192.168.27.

#### The expr relop expr expression

Use this type of expression to capture
packets that match the result of arithmetic operations.

This expression contains keywords, arithmetic
operators (expr), and relational operators (relop). For example, len\+100\>\=200
captures packets that are greater than or equal to 100 bytes.

#### The proto\[expr:size] expression

Use this type of expression to capture
packets that match the result of arithmetic operations on a number of bytes
relative to a protocol layer.

This type of expression contains the
following elements:

·     proto—Specifies a protocol layer.

·     \[]—Performs arithmetic operations on a number of bytes relative to the
protocol layer.

·     expr—Specifies the arithmetic
expression.

·     size—Specifies the byte offset. This
offset indicates the number of bytes relative to the protocol layer. The
operation is performed on the specified bytes. The offset is set to 1 byte if
you do not specify an offset.

For example, ip\[0]\&0xf !\=5 captures an IP packet if
the result of ANDing the first byte with 0x0f is not 5\.

To match a field, you can specify a field
name for expr:size. For example, icmp\[icmptype]\=0x08 captures ICMP packets that
contain a value of 0x08 in the Type field.

#### The vlan vlan\_id expression

Use this type of expression to capture
802.1Q tagged VLAN traffic.

This type of expression contains the vlan vlan\_id keywords and
logical operators. The vlan\_id variable is an
integer that specifies a VLAN ID. For example, vlan 1 and ip
captures IPv4 packets in VLAN 1\.

To capture packets of a VLAN, set a capture
filter as follows:

·     To capture tagged packets that are permitted on
the interface, you must use the vlan vlan\_id expression prior to any other expressions. For example, use the vlan 3 and src 192.168.1.10 and dst 192.168.1.1
expression to capture packets of VLAN 3 that are sent from 192.168.1.10 to 192.168.1.1.

·     After receiving an untagged packet, the device
adds a VLAN tag to the packet header. To capture the packet, add "vlan xx"
to the capture filter expression. For Layer 3 packets, the xx represents the
default VLAN ID of the outgoing interface. For Layer 2 packets, the xx
represents the default VLAN ID of the incoming interface. 

## Building a display filter rule

A display filter rule only identifies the
packets to display. It does not affect which packets to save in a file.

### Display filter rule keywords

#### Qualifiers

Table 6 Qualifiers for display filter rules

| Category | Description | Examples |
| --- | --- | --- |
| Protocol | Matches a protocol.  If you do not specify a protocol qualifier, the filter matches any supported protocols. | ·     eth—Matches Ethernet.  ·     ftp—Matches FTP.  ·     http—Matches HTTP.  ·     icmp—Matches ICMP.  ·     ip—Matches IPv4.  ·     ipv6—Matches IPv6.  ·     tcp—Matches TCP.  ·     telnet—Matches Telnet.  ·     udp—Matches UDP. || Packet field | Matches a field in packets by using a dotted string in the protocol.field\[.level1-subfield]…\[.leveln-subfield] format. | ·     tcp.flags.syn—Matches the SYN bit in the flags field of TCP.  ·     tcp.port—Matches the source or destination port field of TCP. |


 

#### Variables

A packet field qualifier requires a
variable.

Table 7 Variable types for display filter rules

| Variable type | Description |
| --- | --- |
| Integer | Represented in binary, octal, decimal, or hexadecimal notation.  For example, to display IP packets that are less than or equal to 1500 bytes, use one of the following expressions:  ·     ip.len le 1500.  ·     ip.len le 02734.  ·     ip.len le 0x436. || Boolean | This variable type has two values: true or false.  This variable type applies if you use a packet field string alone to identify the presence of a field in a packet.  ·     If the field is present, the match result is true. The filter displays the packet.  ·     If the field is not present, the match result is false. The filter does not display the packet.  For example, to display TCP packets that contain the SYN field, use tcp.flags.syn. || MAC address  (6 bytes) | Uses colons (:), dots (.), or hyphens (-) to break up the MAC address into two or four segments.  For example, to display packets that contain a destination MAC address of ffff.ffff.ffff, use one of the following expressions:  ·     eth.dst\=\=ff:ff:ff:ff:ff:ff.  ·     eth.dst\=\=ff-ff-ff-ff-ff-ff.  ·     eth.dst \=\=ffff.ffff.ffff. || IPv4 address | Represented in dotted decimal notation.  For example:  ·     To display IPv4 packets that are sent to or from 192.168.0.1, use ip.addr\=\=192.168.0.1.  ·     To display IPv4 packets that are sent to or from 129.111.0.0/16, use ip.addr\=\=129.111.0.0/16. || IPv6 address | Represented in colon hexadecimal notation.  For example:  ·     To display IPv6 packets that are sent to or from 1::1, use ipv6.addr\=\=1::1.  ·     To display IPv6 packets that are sent to or from 1::/64, use ipv6.addr\=\=1::/64. || String | Character string.  For example, to display HTTP packets that contain the string HTTP/1.1 for the request version field, use http.request version\=\="HTTP/1.1". |






 

### Display filter rule operators

Logical operators are left associative.
They group from left to right. The \[ ] operator has the highest priority. The not
operator has the highest priority. The and and or operators have the same priority.

#### Logical operators

Table 8 Logical operators for display filter
rules

| Nonalphanumeric  symbol | Alphanumeric  symbol | Description |
| --- | --- | --- |
| \[ ] | No alphanumeric symbol is available. | Used with protocol qualifiers. For more information, see "[The proto\[…] expression](#_Ref398628068)." || ! | not | Displays packets that do not match the condition connected to this operator. || \&\& | and | Joins two conditions.  Use this operator to display traffic that matches both conditions. || \|\| | or | Joins two conditions.  Use this operator to display traffic that matches either of the conditions. |




 

#### Relational operators

Table 9 Relational operators for display
filter rules

| Nonalphanumeric  symbol | Alphanumeric  symbol | Description |
| --- | --- | --- |
| \=\= | eq | Equal to.  For example, ip.src\=\=10.0.0.5 displays packets with the source IP address as 10.0.0.5. || !\= | ne | Not equal to.  For example, ip.src!\=10.0.0.5 displays packets whose source IP address is not 10.0.0.5. || \> | gt | Greater than.  For example, frame.len\>100 displays frames with a length greater than 100 bytes. || \< | lt | Less than.  For example, frame.len\<100 displays frames with a length less than 100 bytes. || \>\= | ge | Greater than or equal to.  For example, frame.len ge 0x100 displays frames with a length greater than or equal to 256 bytes. || \<\= | le | Less than or equal to.  For example, frame.len le 0x100 displays frames with a length less than or equal to 256 bytes. |






 

### Display filter rule expressions

#### Logical expression

Use this type of expression to display
packets that match the result of logical operations.

Logical expressions contain keywords and
logical operators. For example, ftp or icmp displays
all FTP packets and ICMP packets.

#### Relational expression

Use this type of expression to display
packets that match the result of comparison operations. 

Relational expressions contain keywords and
relational operators. For example, ip.len\<\=28
displays IP packets that contain a value of 28 or fewer bytes in the length
field.

#### Packet field expression

Use this type of expression to display
packets that contain a specific field.

Packet field expressions contain only
packet field strings. For example, tcp.flags.syn
displays all TCP packets that contain the SYN bit field.

#### The proto\[…] expression

Use this type of expression to display
packets that contain specific field values.

This type of expression contains the
following elements:

·     proto—Specifies a protocol layer or
packet field. 

·     \[…]—Matches a
number of bytes relative to a protocol layer or packet field. Values for the
bytes to be matched must be a hexadecimal integer string. The expression in
brackets can use the following formats:

¡     \[n:m]—Matches a total of m bytes after an offset of n
bytes from the beginning of the specified protocol layer or field. To match
only 1 byte, you can use both \[n] and \[n:1] formats. For example, eth.src\[0:3]\=\=00:00:83 matches an Ethernet frame
if the first three bytes of its source MAC address are 0x00, 0x00, and 0x83.
The eth.src\[2]
\=\= 83 expression matches an Ethernet frame if
the third byte of its source MAC address is 0x83.

¡     \[n\-m]—Matches a total of (m-n\+1) bytes, starting from the (n\+1)th byte relative to the beginning of the specified
protocol layer or packet field. For example, eth.src\[1-2]\=\=00:83 matches an Ethernet frame if
the second and third bytes of its source MAC address are 0x00 and 0x83,
respectively.

## Restrictions and guidelines: Packet capture

To capture packets forwarded through chips,
first configure a traffic behavior to mirror the traffic to the CPU. 

To capture packets forwarded by the CPU,
enable packet capture directly.

The packet capture feature can capture only
packets of a frame length equal to or less than 9196 bytes. If the frame length
of a packet exceeds 9196 bytes, the system does not capture the packet.

## Installing feature image

**1\.**Install the packet capture feature image by
using boot-loader or install commands. For more information about the commands, see Fundamentals Command Reference.

**2\.**Log out and then log in to the device again.


## Configuring feature image-based packet capture

### Restrictions and guidelines

After configuring feature image-based
packet capture, you cannot configure any other commands at the CLI until the capture
finishes or is stopped.

There might be a delay for the capture to
stop because of heavy traffic.

### Saving captured packets to a file

To configure feature image-based packet
capture and save the captured packets to a file, execute the following command
in user view:

packet-capture interface interface-type interface-number
\[ capture-filter capt-expression
\| limit-captured-frames limit \| limit-frame-size bytes \| autostop filesize kilobytes \| autostop duration seconds \| autostop files numbers \| capture-ring-buffer filesize kilobytes \| capture-ring-buffer duration seconds \| capture-ring-buffer files numbers ] \* write filepath \[ raw \| { brief \| verbose } ] \*

### Displaying specific captured packets

To configure feature image-based packet
capture and display specific packet data, execute the following command in user
view:

packet-capture interface interface-type interface-number
\[ capture-filter capt-expression
\| display-filter disp-expression
\| limit-captured-frames limit \| limit-frame-size bytes \| autostop duration seconds ] \* \[ raw \| { brief \| verbose } ] \*

## Stopping packet capture

#### About this task

Use this task to manually stop packet
capture.

#### Procedure

To stop feature image-based packet capture,
press Ctrl\+C. 

## Displaying the contents in a packet file

#### About this task

Use this task to display the contents of a
.pcap or .pcapng file on the device. Alternatively, you can transfer the file
to a PC and use Wireshark to display the file content.

#### Restrictions and guidelines

To stop displaying the contents, press Ctrl\+C.

#### Procedure

To display the contents in a local packet
file, execute the following command in user view:

packet-capture read filepath \[ display-filter disp-expression ] \[ raw \| { brief \| verbose } ] \*

## Packet capture configuration examples

### Example: Configuring feature image-based packet capture

#### Network configuration

As shown in [Figure 1](#_Ref501440370), capture
incoming IP packets of VLAN 3 on Layer 2 interface Ten-GigabitEthernet 1/0/1 that meet the
following conditions:

·     Sent from 192.168.1.10 or 192.168.1.11 to 192.168.1.1.

·     Forwarded through the CPU or chips.

Figure 1 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705835_x_Img_x_png_0_2216129_294551_0.png)

‌

#### Procedure

**1\.**Install the packet capture feature.

\# Identify versions of the boot and
system images running on the device. Prepare a packet capture feature image
that is compatible with the current boot and system images.

\<Device\> display version

H3C Comware Software, Version
7.1.070, Demo 01

Copyright (c) 2004-2019 New
H3C Technologies Co., Ltd. All rights reserved.

H3C XXX uptime is 0 weeks, 0
days, 5 hours, 33 minutes

Last reboot reason : Cold
reboot

Boot image: flash:/boot-01.bin

Boot image version: 7.1.070,
Demo 01

  Compiled Oct 20 2019
16:00:00

System image: flash:/system-01.bin

System image version: 7.1.070,
Demo 01

  Compiled Oct 20 2019
16:00:00

...

\# Download the packet capture feature
image to the device. In this example, download the packet-capture-01.bin
image stored on the TFTP server at 192.168.1.1.

\<Device\> tftp
192.168.1.1 get packet-capture-01.bin

Press CTRL\+C to abort.

  % Total    % Received %
Xferd  Average Speed   Time    Time     Time  Current

                                
Dload  Upload   Total   Spent    Left  Speed

100 11.3M    0 11.3M    0    
0   155k      0 --:--:--  0:01:14 --:--:--  194k

Writing file...Done. 

\# Install the packet capture feature
image on all IRF member devices and commit the software change. In this
example, there are two IRF member devices.

\<Device\> install activate
feature flash:/packet-capture-01.bin slot 1

Verifying the file
flash:/packet-capture-01.bin on slot 1....Done.

Identifying the upgrade
methods....Done.

Upgrade summary according to
following table:

 

flash:/packet-capture-01.bin

  Running Version            
New Version

  None                       
Demo 01

 

  Slot                        Upgrade
Way

  1                          
Service Upgrade

Upgrading software images to
compatible versions. Continue? \[Y/N]:y

This operation might take
several minutes, please wait....................Done.

\<Device\> install activate
feature flash:/packet-capture-01.bin slot 2

Verifying the file
flash:/packet-capture-01.bin on slot 2....Done.

Identifying the upgrade
methods....Done.

Upgrade summary according to
following table:

 

flash:/packet-capture-01.bin

  Running Version            
New Version

  None                       
Demo 01

 

  Slot                       
Upgrade Way

  2                          
Service Upgrade

Upgrading software images to
compatible versions. Continue? \[Y/N]:y

This operation might take
several minutes, please wait....................Done.

\<Device\> install commit

This operation will take
several minutes, please wait.......................Done.

\# Log out and then log in to the device
again so you can execute the packet-capture interface and packet-capture read commands.

**2\.**Apply a QoS policy to the incoming direction
of Ten-GigabitEthernet 1/0/1 to
capture packets from 192.168.1.10 or 192.168.1.11 to 192.168.1.1 that are
forwarded through chips.

\# Create an IPv4 advanced ACL to match packets
that are sent from 192.168.1.10 or 192.168.1.11 to 192.168.1.1.

\<Device\> system-view

\[Device] acl advanced 3000

\[Device-acl-ipv4-adv-3000]
rule permit ip source 192.168.1.10 0 destination 192.168.1.1 0

\[Device-acl-ipv4-adv-3000]
rule permit ip source 192.168.1.11 0 destination 192.168.1.1 0

\[Device-acl-ipv4-adv-3000]
quit

\# Configure a traffic behavior to mirror
traffic to the CPU.

\[Device] traffic behavior
behavior1

\[Device-behavior-behavior1]
mirror-to cpu

\[Device-behavior-behavior1]
quit

\# Configure a traffic class to use the
ACL to match traffic.

\[Device] traffic classifier
classifier1

\[Device-classifier-class1]
if-match acl 3000

\[Device-classifier-class1]
quit

\# Configure a QoS policy. Associate the traffic
class with the traffic behavior.

\[Device] qos policy user1

\[Device-qospolicy-user1]
classifier classifier1 behavior behavior1

\[Device-qospolicy-user1] quit

\# Apply the QoS policy to the incoming direction
of Ten-GigabitEthernet 1/0/1.

\[Device] interface ten-gigabitethernet 1/0/1

\[Device-Ten-GigabitEthernet1/0/1] qos apply policy
user1 inbound

\[Device-Ten-GigabitEthernet1/0/1] quit

\[Device] quit

**3\.**Enable packet capture.

\# Capture incoming traffic on Ten-GigabitEthernet 1/0/1. Set the maximum
number of captured packets to 10\. Save the captured packets to the flash:/a.pcap
file. 

\<Device\> packet-capture
interface ten-gigabitethernet 1/0/1 capture-filter
"vlan 3 and src 192.168.1.10 or 192.168.1.11 and dst 192.168.1.1"
limit-captured-frames 10 write flash:/a.pcap

Capturing on 'Ten-GigabitEthernet1/0/1'

10

#### Verifying the configuration

\# Telnet to 192.168.1.1 from 192.168.1.10.
(Details not shown.)

\# Display the contents in the packet file
on the device.

\<Device\> packet-capture read
flash:/a.pcap

  1   0.000000 192.168.1.10 -\>
192.168.1.1 TCP 62 6325 \> telnet \[SYN] Seq\=0 Win\=65535 Len\=0 MSS\=1460
SACK\_PERM\=1

  2   0.000061 192.168.1.10 -\>
192.168.1.1 TCP 60 6325 \> telnet \[ACK] Seq\=1 Ack\=1 Win\=65535 Len\=0

  3   0.024370 192.168.1.10 -\>
192.168.1.1 TELNET 60 Telnet Data ...

  4   0.024449 192.168.1.10 -\>
192.168.1.1 TELNET 78 Telnet Data ...

  5   0.025766 192.168.1.10 -\>
192.168.1.1 TELNET 65 Telnet Data ...

  6   0.035096 192.168.1.10 -\>
192.168.1.1 TELNET 60 Telnet Data ...

  7   0.047317 192.168.1.10 -\>
192.168.1.1 TCP 60 6325 \> telnet \[ACK] Seq\=42 Ack\=434 Win\=65102 Len\=0

  8   0.050994 192.168.1.10 -\>
192.168.1.1 TCP 60 6325 \> telnet \[ACK] Seq\=42 Ack\=436 Win\=65100 Len\=0

  9   0.052401 192.168.1.10 -\>
192.168.1.1 TCP 60 6325 \> telnet \[ACK] Seq\=42 Ack\=438 Win\=65098 Len\=0

 10   0.057736 192.168.1.10 -\>
192.168.1.1 TCP 60 6325 \> telnet \[ACK] Seq\=42 Ack\=440 Win\=65096 Len\=0

 

 

 

