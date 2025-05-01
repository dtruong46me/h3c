
# Configuring basic IPv6settings

## About IPv6

IPv6, also called IP next generation
(IPng), was designed by the IETF as the successor to IPv4. One significant
difference between IPv6 and IPv4 is that IPv6 increases the IP address size
from 32 bits to 128 bits.

### IPv6 features

#### Simplified header format

IPv6 removes several IPv4 header fields or moves
them to the IPv6 extension headers to reduce the length of the basic IPv6 packet
header. The basic IPv6 packet header has a fixed length of 40 bytes to simplify
IPv6 packet handling and improve forwarding efficiency. Although the IPv6
address size is four times the IPv4 address size, the basic IPv6 packet header size
is only twice the size of the option-less IPv4 packet header.

Figure 1 IPv4 packet header format and basic IPv6 packet header format

![](https://resource.h3c.com/en/202407/12/20240712_11704646_x_Img_x_png_0_2216007_294551_0.png)

‌

#### Larger address space

IPv6 can provide 3.4 x 1038 addresses to meet the requirements of hierarchical
address assignment for both public and private networks.

#### Hierarchical address structure

IPv6 uses a hierarchical address structure
to speed up route lookup and reduce the IPv6 routing table size through route aggregation.

#### Address autoconfiguration

To simplify host configuration, IPv6
supports stateful and stateless address autoconfiguration.

·     Stateful address autoconfiguration enables a
host to acquire an IPv6 address and other configuration information from a server
(for example, a DHCPv6 server). For more information about DHCPv6 server, see "Configuring
the DHCPv6 server."

·     Stateless address autoconfiguration enables a host
to automatically generate an IPv6 address and other configuration information by
using its link-layer address and the prefix information advertised by a router.

To communicate with other hosts on the same
link, a host automatically generates a link-local address based on its
link-layer address and the link-local address prefix (FE80::/10).

#### Built-in security

IPv6 defines extension headers to support
IPsec. IPsec provides end-to-end security and enhances interoperability among different
IPv6 applications.

#### QoS support

The Flow Label field in the IPv6 header
allows the device to label the packets of a specific flow for special handling.

#### Enhanced neighbor discovery mechanism

The IPv6 neighbor discovery protocol uses a
group of ICMPv6 messages to manage information exchange among neighboring nodes
on the same link. The group of ICMPv6 messages replaces ARP messages, ICMPv4 router
discovery messages, and ICMPv4 redirect messages and provides a series of other
functions.

#### Flexible extension headers

IPv6 eliminates the Options field in the
header and introduces optional extension headers to provide scalability and
improve efficiency. The Options field in the IPv4 packet header contains a
maximum of 40 bytes, whereas the IPv6 extension headers are restricted to the
maximum size of IPv6 packets.

### IPv6 addresses

#### IPv6 address format

An IPv6 address is represented as a set of
16-bit hexadecimals separated by colons (:). An IPv6 address is divided into
eight groups, and each 16-bit group is represented by four hexadecimal numbers,
for example, 2001:0000:130F:0000:0000:09C0:876A:130B.

To simplify the representation of IPv6
addresses, you can handle zeros in IPv6 addresses by using the following
methods:

·     The leading zeros in each group can be removed.
For example, the above address can be represented in a shorter format as
2001:0:130F:0:0:9C0:876A:130B.

·     If an IPv6 address contains one or more
consecutive groups of zeros, they can be replaced by a double colon (::). For
example, the above address can be represented in the shortest format as
2001:0:130F::9C0:876A:130B.

 

| IMPORTANT | IMPORTANT:  A double colon can appear once or not at all in an IPv6 address. This limit allows the device to determine how many zeros the double colon represents and correctly convert it to zeros to restore a 128-bit IPv6 address. |
| --- | --- |

‌

An IPv6 address consists of an address
prefix and an interface ID, which are equivalent to the network ID and the host
ID of an IPv4 address.

An IPv6 address prefix is written in
IPv6-address/prefix-length notation. The prefix-length is a decimal number
indicating how many leftmost bits of the IPv6 address are in the address
prefix.

#### IPv6 address types

IPv6 addresses include
the following types:

·     Unicast address—An identifier for a single interface, similar to an IPv4 unicast
address. A packet sent to a unicast address is delivered to the interface
identified by that address.

·     Multicast address—An identifier for a set of
interfaces (typically belonging to different nodes), similar to an IPv4
multicast address. A packet sent to a multicast address is delivered to all
interfaces identified by that address.

Broadcast addresses are replaced by
multicast addresses in IPv6.

·     Anycast address—An identifier for a set of
interfaces (typically belonging to different nodes). A packet sent to an
anycast address is delivered to the nearest interface among the interfaces
identified by that address. The nearest interface is chosen according to the routing
protocol's measure of distance.

The type of an IPv6 address is designated
by the first several bits, called the format prefix.

Table 1 Mappings between address types and
format prefixes

| Type | | Format prefix (binary) | IPv6 prefix ID |
| --- | --- | --- | --- |
| Unicast address | Unspecified address | 00...0 (128 bits) | ::/128 || Loopback address | 00...1 (128 bits) | ::1/128 || Link-local address | 1111111010 | FE80::/10 || Global unicast address | Other forms | N/A || Multicast address | | 11111111 | FF00::/8 || Anycast address | | Anycast addresses use the unicast address space and have the identical structure of unicast addresses. | |






‌

#### Unicast addresses

Unicast addresses include global unicast
addresses, link-local unicast addresses, the loopback address, and the
unspecified address.

·     Global unicast addresses—Equivalent to public IPv4 addresses, global unicast addresses are provided
for Internet service providers. This type of address allows for prefix aggregation
to restrict the number of global routing entries.

·     Link-local addresses—Used for communication among link-local nodes for neighbor
discovery and stateless autoconfiguration. Packets with link-local source or
destination addresses are not forwarded to other links.

·     A loopback address—0:0:0:0:0:0:0:1 (or ::1). It has the same function as the loopback
address in IPv4. It cannot be assigned to any physical interface. A node uses
this address to send an IPv6 packet to itself.

·     An unspecified address—0:0:0:0:0:0:0:0 (or ::). It cannot be assigned to any node. Before
acquiring a valid IPv6 address, a node fills this address in the source address
field of IPv6 packets. The unspecified address cannot be used as a destination
IPv6 address.

#### Multicast addresses

IPv6 multicast addresses listed in[Table 2](#_Ref136502705) are
reserved for special purposes.

Table 2 Reserved
IPv6 multicast addresses

| Address | Application |
| --- | --- |
| FF01::1 | Node-local scope all-nodes multicast address. || FF02::1 | Link-local scope all-nodes multicast address. || FF01::2 | Node-local scope all-routers multicast address. || FF02::2 | Link-local scope all-routers multicast address. |




‌

Multicast addresses also include
solicited-node addresses. A node uses a solicited-node multicast address to
acquire the link-layer address of a neighboring node on the same link and to
detect duplicate addresses. Each IPv6 unicast or anycast address has a corresponding
solicited-node address. The format of a solicited-node multicast address is FF02:0:0:0:0:1:FFXX:XXXX.
FF02:0:0:0:0:1:FF is fixed and consists of 104 bits, and XX:XXXX is the last 24
bits of an IPv6 unicast address or anycast address.

#### EUI-64 address-based interface identifiers

An interface identifier is 64 bits long and
uniquely identifies an interface on a link.

On an IEEE 802 interface (such as a VLAN
interface), the interface identifier is derived from the link-layer address (typically
a MAC address) of the interface. The MAC address is 48 bits long. 

To obtain an EUI-64 address-based interface
identifier, follow these steps: 

**1\.**Insert the 16-bit binary number
1111111111111110 (hexadecimal value of FFFE) behind the 24th high-order bit of
the MAC address.

**2\.**Invert the universal/local (U/L) bit (the
seventh high-order bit). This operation makes the interface identifier have the
same local or global significance as the MAC address.

Figure 2 Converting a MAC address into an
EUI-64 address-based interface identifier

![](https://resource.h3c.com/en/202407/12/20240712_11704650_x_Img_x_png_2_2216007_294551_0.png)

‌

On a tunnel interface, the lower 32 bits of
the EUI-64 address-based interface identifier are the source IPv4 address of
the tunnel interface. The higher 32 bits of the EUI-64 address-based interface
identifier of an ISATAP tunnel interface are 0000:5EFE, whereas those of other
tunnel interfaces are all zeros. For more information about tunnels, see "Configuring
tunneling."

On an interface of another type (such as a
serial interface), the EUI-64 address-based interface identifier is generated
randomly by the device.

### IPv6 path MTU discovery

The links that a packet passes from a source
to a destination can have different MTUs, among which the minimum MTU is the
path MTU. If a packet exceeds the path MTU, the source end fragments the packet
to reduce the processing pressure on intermediate devices and to use network
resources effectively.

A source end uses path MTU discovery to find
the path MTU to a destination, as shown in [Figure 3](#_Ref457221903).

**1\.**The source host sends a packet no largerthan its MTU to the
destination host.

**2\.**If the MTU of an intermediate device's
output interface is smaller than the packet, the device performs the following operations:

¡     Discards
the packet. 

¡     Returns
an ICMPv6 error message containing the interface MTU to the source host.

**3\.**Upon receiving the ICMPv6 error message, the
source host performs the following operations:

¡     Uses
the returned MTU to limit the packet size.

¡     Performs
fragmentation.

¡     Sends
the fragments to the destination host.

**4\.**Step 2 and step 3 are repeated until the
destination host receives the packet. In this way, the source host finds the minimum
MTU of all links in the path to the destination host.

Figure 3 Path MTU discovery process

![](https://resource.h3c.com/en/202407/12/20240712_11704651_x_Img_x_png_3_2216007_294551_0.png)

‌

### IPv6 transition technologies

IPv6 transition technologies enable
communication between IPv4 and IPv6 networks.

#### Dual stack

Dual stack is the most direct transition approach.
A network node that supports both IPv4 and IPv6 is a dual-stack node. A dual-stack
node configured with an IPv4 address and an IPv6 address can forward both IPv4
and IPv6 packets. An application that supports both IPv4 and IPv6 prefers IPv6
at the network layer. 

Dual stack is suitable for communication
between IPv4 nodes or between IPv6 nodes. It is the basis of all transition
technologies. However, it does not solve the IPv4 address depletion issue
because each dual-stack node must have a globally unique IPv4 address. 

#### Tunneling

Tunneling uses one network protocol to
encapsulate the packets of another network protocol and transfers them over the
network. For more information about tunneling, see "Configuring tunneling."

#### 6PE

6PE enables communication between isolated
IPv6 networks over an IPv4 backbone network.

6PE adds labels to the IPv6 routing
information about customer networks and advertises the information into the IPv4
backbone network over internal Border Gateway Protocol (IBGP) sessions. IPv6
packets are labeled and forwarded over tunnels on the backbone network. The
tunnels can be GRE tunnels or MPLS LSPs.

Figure 4 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11704652_x_Img_x_png_4_2216007_294551_0.png)

‌

6PE is a highly efficient solution. When an
ISP wants to utilize the existing IPv4/MPLS network to provide IPv6 traffic
switching, it only needs to upgrade the PE routers. In addition, the operation
risk of 6PE is very low.

### Protocols and standards

·     RFC 1881, IPv6 Address
Allocation Management

·     RFC 1887, An Architecture
for IPv6 Unicast Address Allocation

·     RFC 1981, Path MTU
Discovery for IP version 6

·     RFC 2375, IPv6 Multicast
Address Assignments

·     RFC 2460, Internet
Protocol, Version 6 (IPv6) Specification

·     RFC 2464, Transmission of
IPv6 Packets over Ethernet Networks

·     RFC 2526, Reserved IPv6
Subnet Anycast Addresses

·     RFC 3307, Allocation
Guidelines for IPv6 Multicast Addresses

·     RFC 4191, Default Router
Preferences and More-Specific Routes

·     RFC 4291, IP Version 6
Addressing Architecture

·     RFC 4443, Internet
Control Message Protocol (ICMPv6) for the Internet Protocol Version 6 (IPv6)
Specification

·     RFC 4862, IPv6
Stateless Address Autoconfiguration

## Configuring an IPv6 global unicast address

### About IPv6 global unicast address

Use one of the following methods to configure
an IPv6 global unicast address for an interface:

·     EUI-64 IPv6 address—The IPv6 address prefix of the interface is manually configured,
and the interface ID is generated automatically by the interface.

·     Manual configuration—The IPv6 global unicast address is manually configured.

·     Stateless address
autoconfiguration—The IPv6 global unicast
address is generated automatically based on the address prefix information contained
in the RA message.

·     Prefix-specific address autoconfiguration—The IPv6 global unicast address is generated automatically based on
the prefix specified by its ID. The prefix can be manually configured or obtained
through DHCPv6. 

You can configure multiple IPv6 global
unicast addresses on an interface.

Manually configured global unicast addresses
(including EUI-64 IPv6 addresses) take precedence over automatically generated
ones. If you manually configure a global unicast address with the same address
prefix as an existing global unicast address on an interface, the manually
configured one takes effect. However, it does not overwrite the automatically
generated address. If you delete the manually configured global unicast
address, the device uses the automatically generated one.

### Restrictions and guidelines

An interface ID is used in EUI-64 IPv6
address configuration and stateless address autoconfiguration. The interface ID
is derived from the MAC address of the interface. If the MAC address changes,
the interface ID, global unicast address, and link-local address will also
change. This will cause the entry table to rebuild for some protocols, such as MLD, IPv6 PIM, and OSPFv3.
If you do not want this situation to occur, use other IPv6 address
configuration methods that do not use MAC addresses, for example, manually
specify an IPv6 address.

### Generating an EUI-64 IPv6 address

**1\.**Enter system view.

system-view 

**2\.**Enter interface view.

interface interface-type interface-number 

**3\.**Configure an EUI-64 IPv6 address on the
interface.

ipv6 address { ipv6-address prefix-length \| ipv6-address/prefix-length } eui-64

By default, no EUI-64 IPv6 address is configured
on an interface.

### Manually assigning an IPv6 global unicast address

**1\.**Enter system view.

system-view 

**2\.**Enter interface view.

interface interface-type interface-number 

**3\.**Assign an IPv6 global unicast address to the
interface.

ipv6 address { ipv6-address prefix-length \| ipv6-address/prefix-length }

By default, no IPv6 global unicast
address is configured on an interface.

### Stateless address autoconfiguration

#### About this task

Stateless address autoconfiguration enables
an interface to automatically generate an IPv6 global unicast address by using
the address prefix in the received RA message and the interface ID. On an IEEE
802 interface (such as an Ethernet interface or a VLAN interface), the
interface ID is generated based on the interface's MAC address and is globally
unique. An attacker can exploit this rule to identify the sending device
easily.

To fix the vulnerability, you can configure
the temporary address feature. With this feature, an IEEE 802 interface
generates the following addresses:

·     Public IPv6 address—Includes the address prefix in the RA message and a fixed interface
ID generated based on the MAC address of the interface.

·     Temporary IPv6 address—Includes the address prefix in the RA message and a random
interface ID generated through MD5.

You can also configure the interface to preferentially
use the temporary IPv6 address as the source address of sent packets. When the
valid lifetime of the temporary IPv6 address expires, the interface deletes the
address and generates a new one. This feature enables the system to send
packets with different source addresses through the same interface. If the
temporary IPv6 address cannot be used because of a DAD conflict, the public
IPv6 address is used.

The preferred lifetime and valid lifetime
for a temporary IPv6 address are determined as follows:

·     The preferred lifetime of a temporary IPv6
address takes the smaller of the following values: 

¡     The
preferred lifetime of the address prefix in the RA message.

¡     The
preferred lifetime configured for temporary IPv6 addresses minus DESYNC\_FACTOR
(a random number in the range of 0 to 600 seconds).

·     The valid lifetime of a temporary IPv6 address
takes the smaller of the following values: 

¡     The
valid lifetime of the address prefix.

¡     The
valid lifetime configured for temporary IPv6 addresses.

#### Restrictions and guidelines

If the IPv6 prefix in the RA message is not
64 bits long, stateless address autoconfiguration fails to generate an IPv6
global unicast address.

To generate a temporary address, an
interface must be enabled with stateless address autoconfiguration. Temporary
IPv6 addresses do not overwrite public IPv6 addresses, so an interface can have
multiple IPv6 addresses with the same address prefix but different interface
IDs.

If an interface fails to generate a public
IPv6 address because of a prefix conflict or other reasons, it does not
generate any temporary IPv6 address.

Executing the undo ipv6
address auto command on an interface deletes all
IPv6 global unicast addresses and link-local addresses that are automatically
generated on the interface.

#### Enabling stateless address autoconfiguration

**1\.**Enter system view.

system-view

**2\.**Enter interface view.

interface interface-type interface-number

**3\.**Enable stateless address autoconfiguration
on an interface, so that the interface can automatically generate a global
unicast address.

ipv6 address auto

By default, the stateless address
autoconfiguration feature is disabled on an interface.

#### Configuring the temporary address feature and preferentially using the temporary IPv6 address as the source address of outgoing packets

**1\.**Enter system view.

system-view

**2\.**Enable the temporary IPv6 address feature.

ipv6 temporary-address \[ valid-lifetime preferred-lifetime ]

By default, the temporary IPv6 address
feature is disabled.

**3\.**Enable the system to preferentially use the
temporary IPv6 address as the source address of the outgoing packets.

ipv6 prefer temporary-address

By default, the system does not preferentially
use the temporary IPv6 address as the source address of the outgoing packets.

### Configuring prefix-specific address autoconfiguration

**1\.**Enter system view.

system-view

**2\.**Configure an IPv6 prefix.

Choose one option as needed:

¡     Configure a static IPv6 prefix.

ipv6 prefix prefix-number ipv6-prefix/prefix-length

By default, no static IPv6 prefixes
exist.

¡     Use
DHCPv6 to obtain a dynamic IPv6 prefix.

For more information about IPv6 prefix acquisition,
see "Configuring the DHCPv6 client." 

**3\.**Enter interface view.

interface interface-type interface-number

**4\.**Specify an IPv6 prefix for an interface to
automatically generate an IPv6 global unicast address and advertise the prefix.

ipv6 address prefix-number
sub-prefix/prefix-length

By default, no IPv6 prefix is specified
for the interface to automatically generate an IPv6 global unicast address.

## Configuring an IPv6 link-local address

### About IPv6 link-local address

Configure IPv6 link-local addresses using one
of the following methods:

·     Automatic generation—The device automatically generates a link-local address for an
interface according to the link-local address prefix (FE80::/10) and the
link-layer address of the interface.

·     Manual assignment—Manually configure an IPv6 link-local address for an interface.

### Restrictions and guidelines

After you configure an IPv6 global unicast
address for an interface, the interface automatically generates a link-local
address. This link-local address is the same as the one generated by using the ipv6 address
auto link-local command. If a link-local address
is manually assigned to an interface, this manual assigned link-local address
takes effect. If the manually assigned link-local address is deleted, the
automatically generated link-local address takes effect.

Using the undo ipv6
address auto link-local command on an interface deletes
only the link-local address generated by the ipv6 address
auto link-local command. If the interface has an
IPv6 global unicast address, it still has a link-local address. If the interface
has no IPv6 global unicast address, it has no link-local address.

An interface can have only one link-local
address. As a best practice, use the automatic generation method to avoid
link-local address conflicts. If both the automatic generation and manual
assignment methods are used, the manual assignment takes precedence.

·     If you first use automatic generation and then
manual assignment, the manually assigned link-local address overwrites the
automatically generated one.

·     If you first use manual assignment and then
automatic generation, both of the following occur:

¡     The
link-local address is still the manually assigned one.

¡     The
automatically generated link-local address does not take effect. If you delete
the manually assigned address, the automatically generated link-local address takes
effect.

An interface ID is used for generating the
link-local address for an interface. On an IEEE 802 interface (such as an
Ethernet interface or a VLAN interface), the interface ID is derived from the MAC
address of the interface. If the MAC address changes, the interface ID, global
unicast address, and link-local address will also change. This will cause the
entry table to rebuild for some protocols, such as MLD, IPv6 PIM, and OSPFv3.
If you do not want this situation to occur, use other IPv6 address
configuration methods that do not use MAC addresses, for example, manually
specify a link-local address.

### Configuring automatic generation of an IPv6 link-local address for an interface

**1\.**Enter system view.

system-view 

**2\.**Enter interface view.

interface interface-type interface-number 

**3\.**Configure the interface to automatically
generate an IPv6 link-local address.

ipv6 address auto link-local 

By default, no link-local address is
configured on an interface.

After an IPv6 global unicast address is
configured on the interface, a link-local address is generated automatically.

### Manually assigning an IPv6 link-local address to an interface

**1\.**Enter system view.

system-view 

**2\.**Enter interface view.

interface interface-type interface-number 

**3\.**Manually assign an IPv6 link-local address
to the interface.

ipv6 address { ipv6-address \[ prefix-length ] \| ipv6-address/prefix-length } link-local

By default, no link-local address is
configured on an interface.

## Configuring an IPv6 anycast address

**4\.**Enter system view.

system-view 

**5\.**Enter interface view.

interface interface-type interface-number 

**6\.**Configure an IPv6 anycast address.

ipv6 address { ipv6-address prefix-length \| ipv6-address/prefix-length } anycast 

By default, no IPv6 anycast address is configured
on an interface.

## Configuring path MTU discovery

### Setting the interface MTU for IPv6 packets

#### About this task

If the size of a packet exceeds the MTU of
the sending interface, the device discards the packet. If the device is an
intermediate device, it also sends the source host an ICMPv6 Packet Too Big
message with the MTU of the sending interface. The source host fragments the
packets according to the MTU. To avoid this situation, set a proper interface
MTU.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter interface view.

interface interface-type interface-number

**3\.**Set the interface MTU for IPv6 packets.

ipv6 mtu size

By default, no interface MTU is set.

### Setting a static path MTU for an IPv6 address

#### About this task

You can set a static path MTU for an IPv6
address. Before sending a packet to the IPv6 address, the device compares the output
interface MTU with the static path MTU. If the packet size exceeds the smaller
one of the two values, the device fragments the packet according to the smaller
value. After sending the fragmented packets, the device dynamically finds the
path MTU to a destination host (see "[IPv6 path MTU discovery](#_Ref146098454)").

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Set a static path MTU for an IPv6 address.

ipv6 pathmtu \[ vpn-instance vpn-instance-name ] ipv6-address value

By default, no path MTU is set for any
IPv6 address.

### Setting the aging time for dynamicpath MTUs

#### About this task

After the device dynamically discovers the
path MTU to a destination host (see "[IPv6 path MTU discovery](#_Ref146098454)"), it performs the following operations:

·     Sends packets to the destination host based on
this path MTU.

·     Starts the aging timer for this path MTU.

When the aging timer expires, the device
removes the dynamic path MTU and discovers the path MTU again. 

#### Restrictions and guidelines

The aging time is invalid for a static path
MTU.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Set the aging time for dynamic path MTUs.

ipv6 pathmtu age age-time

The default setting is 10 minutes.

## Controlling sending ICMPv6messages

### Configuring the rate limit for ICMPv6 error messages

#### About this task

To avoid sending excessive ICMPv6 error
messages within a short period that might cause network congestion, you can
limit the rate at which ICMPv6 error messages are sent. A token bucket algorithm
is used with one token representing one ICMPv6 error message.

A token is placed in the bucket at intervals
until the maximum number of tokens that the bucket can hold is reached.

A token is removed from the bucket when an
ICMPv6 error message is sent. When the bucket is empty, ICMPv6 error messages are
not sent until a new token is placed in the bucket.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Set the bucket size and the interval for
tokens to arrive in the bucket for ICMPv6 error messages.

ipv6 icmpv6 error-interval interval \[ bucketsize ]

By default, the bucket allows a maximum
of 10 tokens. A token is placed in the bucket at an interval of 100
milliseconds.

To disable the ICMPv6 rate limit, set the
interval to 0 milliseconds.

### Enabling replying to multicast echorequests

**1\.**Enter system view.

system-view

**2\.**Enable replying to multicast echo requests.

ipv6 icmpv6 multicast-echo-reply enable

By default, this feature is disabled.

### Enabling sending ICMPv6 destination unreachable messages

#### About this task

The device sends the source the following ICMPv6
destination unreachable messages:

·     ICMPv6 No Route to
Destination message—A packet to be forwarded does
not match any route.

·     ICMPv6 Communication with
Destination Administratively Prohibited message—An
administrative prohibition is preventing successful communication with the
destination. This is typically caused by a firewall or an ACL on the device.

·     ICMPv6 Beyond Scope of
Source Address message—The destination is beyond
the scope of the source IPv6 address. For example, a packet's source IPv6
address is a link-local address, and its destination IPv6 address is a global
unicast address.

·     ICMPv6 Address Unreachable
message—The device fails to resolve the link
layer address for the destination IPv6 address of a packet.

·     ICMPv6 Port Unreachable
message—No port process on the destination
device exists for a received UDP packet.

#### Restrictions and guidelines

An ICMPv6 destination unreachable message
indicates that the destination is not reachable from the source device. Attackers
can launch malicious attacks to make the device generate incorrect ICMPv6
destination unreachable messages, which will affect the function of the
network. To protect the network from malicious attacks and decrease unnecessary
network traffic, you can disable the sending of ICMPv6 destination unreachable messages.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enable sending ICMPv6 destination
unreachable messages.

ipv6 unreachables enable

By default, the device does not send ICMPv6 destination unreachable
messages.

### Enabling sending ICMPv6 time exceeded messages

#### About this task

The device sends the source ICMPv6 time exceeded
messages as follows:

·     If a received packet is not destined for the
device and its hop limit is 1, the device sends an ICMPv6 hop limit exceeded in
transit message to the source.

·     Upon receiving the first fragment of an IPv6 datagram
destined for the device, the device starts a timer. If the timer expires before
all fragments arrive, the device sends an ICMPv6 fragment reassembly time
exceeded message to the source.

#### Restrictions and guidelines

If the device receives large numbers of
malicious packets, its performance degrades greatly because it must send back
ICMP time exceeded messages. To prevent such attacks, disable sending ICMPv6 time
exceeded messages.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enable sending ICMPv6 time exceeded
messages.

ipv6 hoplimit-expires enable

By default, the device sends ICMPv6 time exceeded
messages.

### Enabling sending ICMPv6 redirect messages

#### About this task

Upon receiving a packet from a host, the
device sends an ICMPv6 redirect message to inform the host of a better next hop
when the following conditions are met:

·     The interface receiving the packet is the
interface forwarding the packet.

·     The selected route is not created or modified by
any ICMPv6 redirect messages.

·     The selected route is not a default route.

·     The forwarded packet does not contain the
routing extension header.

The ICMPv6 redirect feature simplifies host
management by enabling hosts that hold few routes to optimize their routing
table gradually. However, to avoid adding too many routes on hosts, this
feature is disabled by default.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enable sending ICMPv6 redirect messages.

ipv6 redirects enable

By default, the device does not send
ICMPv6 redirect messages.

### Specifying the source address for unsolicited ICMPv6 packets

#### About this task

Perform this task to specify the source
IPv6 address for outgoing ping echo requests and ICMPv6 error messages. It is a
good practice to specify the IPv6 address of a loopback interface as the source
IPv6 address. This feature helps users easily locate the sending device.

#### Restrictions and guidelines

For ICMPv6 echo requests, the source IPv6
address specified in the ping ipv6 command has
higher priority than the source IPv6 address specified in the ipv6 icmpv6
source command.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Specify a source IPv6 address for unsolicited
ICMPv6 packets.

ipv6 icmpv6 source \[ vpn-instance vpn-instance-name
] ipv6-address

By default, no source address is
specified for unsolicited ICMPv6 packets.

### Specifying the source IPv6 address for outgoing ICMPv6replies

#### About this task

To help receiver hosts identify sources of
ICMPv6 replies, you can specify the IPv6 address of a loopback interface or a
local interface as the source IPv6 address for ICMPv6 replies.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Specify a source IPv6 address for ICMPv6 replies.

ipv6 icmpv6 reply source \[ vpn-instance vpn-instance-name ] { address ipv6-address \| interface interface-type \[ interface-number ] }

By default, no source IP address is
specified for outgoing ICMPv6 replies.

## Enabling Layer 3 packet statistics collection

#### About this task

With this feature enabled on an interface,
the device counts incoming and outgoing IPv6 packets on the interface. To
display the collected statistics, execute the display interface command. 

#### Restrictions and guidelines

When the interface is processing a large
number of packets, Layer 3 packet statistics collection will cause high CPU usage
and degrade the forwarding performance. If the statistics are not necessary,
disable this feature to ensure the device performance.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter interface view.

interface interface-type interface-number

**3\.**Enable Layer 3 packet statistics collection.

statistics l3-packet enable \[ inbound \| outbound ]

By default, Layer 3 packet statistics
collection is disabled.

## Enabling IPv6 local fragment reassembly

#### About this task

Use this feature on a multichassis IRF
fabric to improve fragment reassembly efficiency. By default, all IPv6
fragments are delivered to the master for reassembly. With this feature
enabled, a subordinate performs fragment reassembly for an IPv6 packet destined for the IRF fabric if
it receives fragments of that packet.

#### Restrictions and guidelines

This feature fails to reassemble an IPv6 packet if fragments of the
packet are received by different subordinates.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enable IPv6 local fragment reassembly.

ipv6 reassemble local enable

By default, IPv6 local fragment reassembly
is disabled.

 

## Enabling IPv6 FIB entry consistency check

#### About this task

Packet drops or incorrect forwarding might
occur when the IPv6 FIB entries in hardware are inconsistent with FIB entries
configured in software. To prevent these issues, enable IPv6 FIB entry
consistency check.

This feature compares all IPv6 FIB entries
in software with the IPv6 FIB entries in hardware regularly. If the device
detects an inconsistency, the device performs the following tasks:

·     Generates a log.

·     Updates the IPv6 FIB entry in hardware with the
IPv6 FIB entry in software.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enable IPv6 FIB entry consistency check.

ipv6 fib consistency-check enable

By default, IPv6 FIB entry consistency
check is disabled.

## Enabling source MAC unknown IPv6 packets to trigger ND snooping

#### About this task

WLANs allow mobile clients to roam from one
AP to another with their IPv6 addresses unchanged. The device might receive
source MAC unknown packets from a client that has roamed to one of the APs
attached to the device.

By default, the device learns the MAC
address of an unknown packet, and then forwards the packet. However, it does
not generate an ND entry. Packets returned to the client will be unable to
reach the device. 

To solve this issue, you can enable this
feature to send an NS message out the interface on which a source MAC unknown IPv6
packet is received. 

The NS message contains the following
address information:

·     The target IPv6 address is the source IPv6
address of the data packet. 

·     The destination IPv6 address is the IPv6 address
of the multicast group to which the target IPv6 address belongs. 

·     The destination MAC address is the multicast MAC
address for the multicast group. 

When the device receives an NA message in
response to the NS message, the AP generates an ND entry.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter interface view.

interface interface-type interface-number

**3\.**Enable source MAC unknown IPv6 packets to
trigger ND snooping.

ipv6 nd unknown-source-mac-probing enable

By default, source MAC unknown IPv6
packets cannot trigger ND snooping.

## Display and maintenance commands for IPv6 basics

Execute display
commands in any view and reset commands in user
view.

For information about the display tcp
statistics, display udp statistics, reset tcp statistics,
and reset
udp statistics command, see the IP performance commands
in Layer 3—IP Services Command Reference.

 

| Task | Command |
| --- | --- |
| Display IPv6 FIB entries. | display ipv6 fib \[ vpn-instance vpn-instance-name ]  \[ ipv6-address \[ prefix-length ] ] || Display ICMPv6 traffic statistics. | display ipv6 icmp statistics \[ slot slot-number ] || Display IPv6 information about the interface. | display ipv6 interface \[ interface-type \[ interface-number ] ] \[ brief \[ description ] ] || Display IPv6 prefix information about the interface. | display ipv6 interface interface-type interface-number prefix || Display the IPv6 path MTU information. | display ipv6 pathmtu \[ vpn-instance vpn-instance-name ] { ipv6-address \| { all \| dynamic \| static } \[ count ] } || Display the IPv6 prefix information. | display ipv6 prefix \[ prefix-number ] || Display brief information about IPv6 RawIP connections. | display ipv6 rawip \[ slot slot-number  ] || Display detailed information about IPv6 RawIP connections. | display ipv6 rawip verbose \[ slot slot-number \[ pcb pcb-index ] ] || Display IPv6 and ICMPv6 packet statistics. | display ipv6 statistics \[ slot slot-number ] || Display brief information about IPv6 TCP connections. | display ipv6 tcp \[ slot slot-number ] || Display detailed information about IPv6 TCP connections. | display ipv6 tcp verbose \[ slot slot-number \[ pcb pcb-index ] ] || Display brief information about IPv6 UDP connections. | display ipv6 udp \[ slot slot-number ] || Display detailed information about IPv6 UDP connections. | display ipv6 udp verbose \[ slot slot-number \[ pcb pcb-index ] ] || Display IPv6 TCP traffic statistics. | display tcp statistics \[ slot slot-number ] || Display IPv6 UDP traffic statistics. | display udp statistics \[ slot slot-number ] || Clear path MTUs. | reset ipv6 pathmtu { all \| dynamic \| static } || Clear IPv6 and ICMPv6 packet statistics. | reset ipv6 statistics \[ slot slot-number ] || Clear IPv6 TCP traffic statistics. | reset tcp statistics || Clear IPv6 UDP traffic statistics. | reset udp statistics |



















‌

## Basic IPv6 settings configuration examples

### Example: Configuring basic IPv6 settings

#### Network configuration

As shown in [Figure 5](#_Ref261526011), a host,
Switch A, and Switch B are connected through Ethernet ports. Add the Ethernet
ports to corresponding VLANs. Configure IPv6 addresses for the VLAN interfaces
and verify that they are connected. Switch B can reach the host.

Enable IPv6 on the host to automatically obtain
an IPv6 address through IPv6 ND.

Figure 5 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11704653_x_Img_x_png_5_2216007_294551_0.png)

‌

#### Procedure

This example assumes that the VLAN
interfaces have been created on the switches.

**1\.**Configure Switch A:

\# Specify a global unicast address for VLAN-interface
2\.

\<SwitchA\> system-view

\[SwitchA] interface
vlan-interface 2

\[SwitchA-Vlan-interface2] ipv6
address 3001::1/64

\[SwitchA-Vlan-interface2] quit

\# Specify a global unicast address for
VLAN-interface 1, and allow it to advertise RA messages (no interface
advertises RA messages by default).

\[SwitchA] interface
vlan-interface 1

\[SwitchA-Vlan-interface1] ipv6
address 2001::1/64

\[SwitchA-Vlan-interface1] undo
ipv6 nd ra halt

\[SwitchA-Vlan-interface1] quit

**2\.**Configure Switch B:

\# Configure a global unicast address for VLAN-interface
2\.

\<SwitchB\> system-view

\[SwitchB] interface
vlan-interface 2

\[SwitchB-Vlan-interface2] ipv6
address 3001::2/64

\[SwitchB-Vlan-interface2] quit

\# Configure an IPv6 static route with
destination IPv6 address 2001::/64 and next hop address 3001::1.

\[SwitchB] ipv6 route-static 2001::
64 3001::1

**3\.**Configure the host:

Enable IPv6 for the host to automatically
obtain an IPv6 address through IPv6 ND.

\# Display neighbor information for Ten-GigabitEthernet 1/0/2 on Switch A.

\[SwitchA] display ipv6
neighbors interface ten-gigabitethernet 1/0/2

Type: S-Static    D-Dynamic   
O-Openflow     R-Rule    IS-Invalid static

IPv6 address              MAC
address    VLAN/VSI   Interface     State T  Aging

FE80::215:E9FF:FEA6:7D14      0015-e9a6-7d14
 1    XGE1/0/2       STALE D 1238

2001::15B:E0EA:3524:E791     
0015-e9a6-7d14  1    XGE1/0/2       STALE D 1248

The output shows that the IPv6 global
unicast address that Host obtained is 2001::15B:E0EA:3524:E791.

#### Verifying the configuration

\# Display the IPv6 interface settings on
Switch A. All IPv6 global unicast addresses configured on the interface are
displayed.

\[SwitchA] display ipv6 interface
vlan-interface 2

Vlan-interface2 current state: UP

Line protocol current state: UP

IPv6 is enabled, link-local address
is FE80::20F:E2FF:FE00:2

  Global unicast address(es):

    3001::1, subnet is 3001::/64

  Joined group address(es):

    FF02::1

    FF02::2

    FF02::1:FF00:1

    FF02::1:FF00:2

  MTU is 1500 bytes

  ND DAD is enabled, number of DAD
attempts: 1

  ND reachable time is 1200000 milliseconds

  ND retransmit interval is 1000
milliseconds

  Hosts use stateless autoconfig for
addresses

IPv6 Packet statistics:

  InReceives:                   
25829

  InTooShorts:                   0

  InTruncatedPkts:               0

  InHopLimitExceeds:             0

  InBadHeaders:                  0

  InBadOptions:                  0

  ReasmReqds:                    0

  ReasmOKs:                      0

  InFragDrops:                   0

  InFragTimeouts:                0

  OutFragFails:                  0

  InUnknownProtos:               0

  InDelivers:                    47

  OutRequests:                   89

  OutForwDatagrams:              48

  InNoRoutes:                    0

  InTooBigErrors:                0

  OutFragOKs:                    0

  OutFragCreates:                0

  InMcastPkts:                   6

  InMcastNotMembers:            
25747

  OutMcastPkts:                  48

  InAddrErrors:                  0

  InDiscards:                    0

  OutDiscards:                   0

\[SwitchA] display ipv6 interface
vlan-interface 1

Vlan-interface1 current state: UP

Line protocol current state: UP

IPv6 is enabled, link-local address
is FE80::20F:E2FF:FE00:1C0

  Global unicast address(es):

    2001::1, subnet is 2001::/64

  Joined group address(es):

    FF02::1

    FF02::2

    FF02::1:FF00:1

    FF02::1:FF00:1C0

  MTU is 1500 bytes

  ND DAD is enabled, number of DAD
attempts: 1

  ND reachable time is 1200000 milliseconds

  ND retransmit interval is 1000
milliseconds

  ND advertised reachable time is 0
milliseconds

  ND advertised retransmit interval
is 0 milliseconds

  ND router advertisements are sent
every 600 seconds

  ND router advertisements live for
1800 seconds

  Hosts use stateless autoconfig for
addresses

IPv6 Packet statistics:

  InReceives:                    272

  InTooShorts:                   0

  InTruncatedPkts:               0

  InHopLimitExceeds:             0

  InBadHeaders:                  0

  InBadOptions:                  0

  ReasmReqds:                    0

  ReasmOKs:                      0

  InFragDrops:                   0

  InFragTimeouts:                0

  OutFragFails:                  0

  InUnknownProtos:               0

  InDelivers:                    159

  OutRequests:                   1012

  OutForwDatagrams:              35

  InNoRoutes:                    0

  InTooBigErrors:                0

  OutFragOKs:                    0

  OutFragCreates:                0

  InMcastPkts:                   79

  InMcastNotMembers:             65

  OutMcastPkts:                  938

  InAddrErrors:                  0

  InDiscards:                    0

  OutDiscards:                   0

\# Display the IPv6 interface settings on Switch
B. All IPv6 global unicast addresses configured on the interface are displayed.

\[SwitchB] display ipv6 interface
vlan-interface 2

Vlan-interface2 current state :UP

Line protocol current state :UP

IPv6 is enabled, link-local address
is FE80::20F:E2FF:FE00:1234

  Global unicast address(es):

    3001::2, subnet is 3001::/64

  Joined group address(es):

    FF02::1

    FF02::2

    FF02::1:FF00:2

    FF02::1:FF00:1234

  MTU is 1500 bytes

  ND DAD is enabled, number of DAD
attempts: 1

  ND reachable time is 1200000 milliseconds

  ND retransmit interval is 1000
milliseconds

  Hosts use stateless autoconfig for
addresses

IPv6 Packet statistics:

  InReceives:                    117

  InTooShorts:                   0

  InTruncatedPkts:               0

  InHopLimitExceeds:             0

  InBadHeaders:                  0

  InBadOptions:                  0

  ReasmReqds:                    0

  ReasmOKs:                      0

  InFragDrops:                   0

  InFragTimeouts:                0

  OutFragFails:                  0

  InUnknownProtos:               0

  InDelivers:                    117

  OutRequests:                   83

  OutForwDatagrams:              0

  InNoRoutes:                    0

  InTooBigErrors:                0

  OutFragOKs:                    0

  OutFragCreates:                0

  InMcastPkts:                   28

  InMcastNotMembers:             0

  OutMcastPkts:                  7

  InAddrErrors:                  0

  InDiscards:                    0

  OutDiscards:                   0

\# Ping Switch A and Switch B on the host,
and ping Switch A and the host on Switch B to verify that they are connected.

 

|  | NOTE:  When you ping a link-local address, use the -i parameter to specify an interface for the link-local address. |
| --- | --- |

‌

\[SwitchB] ping ipv6 -c 1 3001::1

Ping6(56 data bytes) 3001::2 --\> 3001::1,
press CTRL\+C to break

56 bytes from 3001::1, icmp\_seq\=0
hlim\=64 time\=4.404 ms

 

\--- Ping6 statistics for 3001::1 \---

1 packet(s) transmitted, 1 packet(s)
received, 0.0% packet loss

round-trip min/avg/max/std-dev \= 4.404/4.404/4.404/0.000
ms

\[SwitchB] ping ipv6 -c 1
2001::15B:E0EA:3524:E791

Ping6(56 data bytes) 3001::2 --\> 2001::15B:E0EA:3524:E791,
press CTRL\+C to break

56 bytes from
2001::15B:E0EA:3524:E791, icmp\_seq\=0 hlim\=64 time\=5.404 ms

 

\--- Ping6 statistics for 2001::15B:E0EA:3524:E791
\---

1 packet(s) transmitted, 1 packet(s)
received, 0.0% packet loss

round-trip min/avg/max/std-dev \= 5.404/5.404/5.404/0.000
ms

The output shows that Switch B can ping Switch
A and the host. The host can also ping Switch B and Switch A.

 

