
# DHCPv6 overview

DHCPv6 provides a
framework to assign IPv6 prefixes, IPv6 addresses, and other configuration
parameters to hosts.

## DHCPv6 address/prefix assignment

An address/prefix assignment process involves
two or four messages.

### Rapid assignment involving two messages

As shown in[Figure 1](#_Ref322962317),
rapid assignment operates in the following steps:

**1\.**The DHCPv6 client sends to the DHCPv6 server
a Solicit message that contains a Rapid Commit option to prefer rapid
assignment.

**2\.**If the DHCPv6 server supports rapid
assignment, it responds with a Reply message containing the assigned IPv6
address/prefix and other configuration parameters. If the DHCPv6 server does
not support rapid assignment, [Assignment involving four messages](#_Ref234837862) is performed.

Figure 1 Rapid assignment involving two
messages

![](https://resource.h3c.com/en/202407/12/20240712_11704659_x_Img_x_png_0_2216008_294551_0.png)

 

### Assignment involving four messages

As shown in [Figure 2](#_Ref318459457), four-message
assignment operates using the following steps:

**1\.**The DHCPv6 client sends a Solicit message to
request an IPv6 address/prefix and other configuration parameters.

**2\.**The DHCPv6 server responds with an Advertise
message that contains the assignable address/prefix and other configuration
parameters if either of the following conditions exists:

¡     The
Solicit message does not contain a Rapid Commit option.

¡     The
DHCPv6 server does not support rapid assignment even though the Solicit message
contains a Rapid Commit option.

**3\.**The DHCPv6 client might receive multiple
Advertise messages offered by different DHCPv6 servers. It selects an offer
according to the receiving sequence and server priority, and sends a Request
message to the selected server for confirmation.

**4\.**The DHCPv6 server sends a Reply message to
the client, confirming that the address/prefix and other configuration
parameters are assigned to the client.

Figure 2 Assignment involving four messages

![](https://resource.h3c.com/en/202407/12/20240712_11704660_x_Img_x_png_1_2216008_294551_0.png)

 

## Address/prefix lease renewal

An IPv6 address/prefix assigned by a DHCPv6
server has a valid lifetime. After the valid lifetime expires, the DHCPv6
client cannot use the IPv6 address/prefix. To use the IPv6 address/prefix, the
DHCPv6 client must renew the lease time.

Figure 3 Using
the Renew message for address/prefix leaserenewal

![](https://resource.h3c.com/en/202407/12/20240712_11704671_x_Img_x_png_2_2216008_294551_0.png)

 

As shown in [Figure 3](#_Ref317510631), at
T1, the DHCPv6 client sends a Renew message to the DHCPv6 server. The
recommended value of T1 is half the preferred lifetime. The DHCPv6 server
responds with a Reply message, informing the client whether the lease is
renewed.

Figure 4 Using
the Rebind message for address/prefix leaserenewal

![](https://resource.h3c.com/en/202407/12/20240712_11704680_x_Img_x_png_3_2216008_294551_0.png)

 

As shown in [Figure 4](#_Ref317510540):

·     If the DHCPv6 client does not receive a response
from the DHCPv6 server after sending a Renew message at T1, it multicasts a
Rebind message to all DHCPv6 servers at T2. Typically, the value of T2 is 0.8
times the preferred lifetime.

·     The DHCPv6 server responds with a Reply message,
informing the client whether the lease is renewed.

·     If the DHCPv6 client does not receive a response
from any DHCPv6 server before the valid lifetime expires, the client stops
using the address/prefix.

For more information about the valid
lifetime and the preferred lifetime, see "Configuring basic IPv6 settings."

## Stateless DHCPv6

Stateless DHCPv6 enables a device that has obtained an IPv6 address/prefix to get
other configuration parameters from a DHCPv6 server.

The device performs stateless DHCPv6 if an
RA message with the following flags is received from the router during
stateless address autoconfiguration:

·     The managed address configuration flag (M flag)
is set to 0\.

·     The other stateful configuration flag (O flag)
is set to 1\.

Figure 5 Stateless DHCPv6 operation

![](https://resource.h3c.com/en/202407/12/20240712_11704681_x_Img_x_png_4_2216008_294551_0.png)

 

As shown in [Figure 5](#_Ref166667484),
stateless DHCPv6 operates in the following steps:

**1\.**The DHCPv6 client sends an Information-request
message to the multicast address of all DHCPv6 servers and DHCPv6 relay agents.
The Information-request message contains an Option Request option that specifies
the requested configuration parameters.

**2\.**The DHCPv6 server returns to the client a
Reply message containing the requested configuration parameters.

**3\.**The client checks the Reply message. If the
obtained configuration parameters match those requested in the
Information-request message, the client uses these parameters to complete configuration.
If not, the client ignores the configuration parameters. If the client receives
multiple replies with configuration parameters matching those requested in the
Information-request message, it uses the first received reply.

## DHCPv6 options

### Option 18

Option 18, also called the interface-ID
option, is used by the DHCPv6 relay agent to determine the interface to use to
forward RELAY-REPLY message.

The DHCPv6 snooping device adds Option 18
to the received DHCPv6 request message before forwarding it to the DHCPv6
server. The server then assigns IP address to the client based on the client information
in Option 18\.

Figure 6 Option 18 format

![](https://resource.h3c.com/en/202407/12/20240712_11704682_x_Img_x_png_5_2216008_294551_0.png)

 

[Figure 6](#_Ref459734008) shows
the Option 18 format, which includes the following fields:

·     Option code—Option code. The value is 18\.

·     Option length—Size of the option data.

·     Port index—Port that receives the DHCPv6 request from the client. 

·     VLAN ID—ID of the outer VLAN.

·     Second VLAN ID—ID of the inner VLAN. This field is optional. If the received
DHCPv6 request does not contain a second VLAN, Option 18 also does not contain
it.

·     DUID—DUID of the DHCPv6 client.

### Option 37

Option 37, also called the remote-ID
option, is used to identify the client.

The DHCPv6 snooping device adds Option 37
to the received DHCPv6 request message before forwarding it to the DHCPv6
server. This option provides client information about address allocation.

Figure 7 Option 37 format

![](https://resource.h3c.com/en/202407/12/20240712_11704683_x_Img_x_png_6_2216008_294551_0.png)

 

[Figure 7](#_Ref459734143) shows
the Option 37 format, which includes the following fields:

·     Option code—Option code. The value is 37\.

·     Option length—Size of the option data.

·     Enterprise number—Enterprise number.

·     Port index—Port to which the client is attached. 

·     VLAN ID—ID of the outer VLAN.

·     Second VLAN ID—ID of the inner VLAN. This field is optional. Option 37 does not
contain this information if the received DHCPv6 request does not contain a
second VLAN ID.

·     DUID—DUID information to be sent to DHCP servers. This information is
configurable with the ipv6 dhcp relay remote-id duid command.

### Option 79

Option 79, also called the client link-layer
address option, is used to record the MAC address of the DHCPv6 client. The
first relay agent that a DHCPv6 request passes learns the MAC address of the
client and encapsulates this address into Option 79 in the Relay-Forward message
for the request. The DHCPv6 server verifies the client or assigns IPv6
address/prefix to the client based on the MAC address of the client.

Figure 8 Option
79 format

![](https://resource.h3c.com/en/202407/12/20240712_11704684_x_Img_x_png_7_2216008_294551_0.png)

 

[Figure 8](#_Ref517462412) shows
the Option 79 format, which includes the following fields:

·     Option code—Option code. The value is 79\.

·     Option length—Size of the option data.

·     Link-layer type—Link-layer address type of the client.

·     Link-layer address—Link-layer address of the client.

## Protocols and standards

·     RFC 3736, Stateless
Dynamic Host Configuration Protocol (DHCP) Service for IPv6

·     RFC 3315, Dynamic Host
Configuration Protocol for IPv6 (DHCPv6)

·     RFC 2462, IPv6 Stateless
Address Autoconfiguration

·     RFC 3633, IPv6
Prefix Options for Dynamic Host Configuration Protocol (DHCP) version 6

·     RFC 6939, Client
Link-Layer Address Option in DHCPv6

