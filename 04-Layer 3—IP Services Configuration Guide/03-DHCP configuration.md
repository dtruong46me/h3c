
# DHCP overview

## DHCP network model

The Dynamic Host Configuration Protocol
(DHCP) provides a framework to assign configuration information to network
devices.

[Figure 1](#_Ref317236258) shows
a typical DHCP application scenario where the DHCP clients and the DHCP server
reside on the same subnet. The DHCP clients can also obtain configuration
parameters from a DHCP server on another subnet through a DHCP relay agent. For
more information about the DHCP relay agent, see "[Configuring the DHCP relay agent](#_Ref116619430)." 

Figure 1 A
typical DHCP application

![](https://resource.h3c.com/en/202407/12/20240712_11704501_x_Img_x_png_0_2215993_294551_0.png)

‌

## DHCP address allocation

### Allocation mechanisms

DHCP supports the following allocation mechanisms:

·     Static allocation—The network administrator assigns an IP address to a client, such
as a WWW server, and DHCP conveys the assigned address to the client.

·     Automatic allocation—DHCP assigns a permanent IP address to a client.

·     Dynamic allocation—DHCP assigns an IP address to a client for a limited period of time,
which is called a lease. Most DHCP clients obtain their addresses in this way.

### IP address allocation process

Figure 2 IP address allocation process

![](https://resource.h3c.com/en/202407/12/20240712_11704502_x_Img_x_png_1_2215993_294551_0.png)

‌

As shown in[Figure 2](#_Ref178669987), a
DHCP server assigns an IP address to a DHCP client in the following process:

**1\.**The client broadcasts a DHCP-DISCOVER
message to locate a DHCP server.

**2\.**Each DHCP server offers configuration parameters
such as an IP address to the client in a DHCP-OFFER message. The sending mode
of the DHCP-OFFER is determined by the flag field in the DHCP-DISCOVER message.
For more information, see "[DHCP message format](#_Ref163635617)."

**3\.**If the client receives multiple offers, it
accepts the first received offer, and broadcasts it in a DHCP-REQUEST message
to formally request the IP address. (IP addresses offered by other DHCP servers
can be assigned to other clients.)

**4\.**All DHCP servers receive the DHCP-REQUEST
message. However, only the server selected by the client does one of the
following operations:

¡     Returns
a DHCP-ACK message to confirm that the IP address has been allocated to the
client.

¡     Returns
a DHCP-NAK message to deny the IP address allocation.

After receiving the DHCP-ACK message, the
client verifies the following details before using the assigned IP address:

·     The assigned IP address is not in use. To verify
this, the client broadcasts a gratuitous ARP packet. The assigned IP address is
not in use if no response is received within the specified time.

·     The assigned IP address is not on the same
subnet as any IP address in use on the client. 

Otherwise, the client sends a DHCP-DECLINE
message to the server to request an IP address again.

### IP address lease extension

A dynamically assigned IP address has a
lease. When the lease expires, the IP address is reclaimed by the DHCP server.
To continue using the IP address, the client must extend the lease duration.

When about half of the lease duration
elapses, the DHCP client unicasts a DHCP-REQUEST to the DHCP server to extend
the lease. Depending on the availability of the IP address, the DHCP server
returns one of the following messages:

·     A DHCP-ACK unicast confirming that the client's
lease duration has been extended.

·     A DHCP-NAK unicast denying the request.

If the client receives no reply, it
broadcasts another DHCP-REQUEST message for lease extension when about seven-eighths
of the lease duration elapses. Again, depending on the availability of the IP
address, the DHCP server returns either a DHCP-ACK unicast or a DHCP-NAK
unicast.

## DHCP message format

[Figure 3](#_Ref149638108) shows
the DHCP message format. DHCP uses some of the fields in significantly
different ways. The numbers in parentheses indicate the size of each field in bytes.

Figure 3 DHCP message format

![](https://resource.h3c.com/en/202407/12/20240712_11704530_x_Img_x_png_2_2215993_294551_0.png)

‌

·     op—Message type defined in options field. 1 \= REQUEST, 2 \= REPLY

·     htype, hlen—Hardware address type and length
of the DHCP client.

·     hops—Number of relay agents a request message traveled.

·     xid—Transaction ID, a random number chosen by the client to identify an
IP address allocation.

·     secs—Filled in by the client, the number of seconds elapsed since the
client began address acquisition or renewal process. This field is reserved and
set to 0\.

·     flags—The leftmost bit is defined as the BROADCAST (B) flag. If this flag
is set to 0, the DHCP server sent a reply back by unicast. If this flag is set
to 1, the DHCP server sent a reply back by broadcast. The remaining bits of the
flags field are reserved for future use.

·     ciaddr—Client IP address if the client has an IP address that is valid and
usable. Otherwise, set to zero. (The client does not use this field to request
an IP address to lease.)

·     yiaddr—Your IP address. It is an IP address assigned by the DHCP server to
the DHCP client.

·     siaddr—Server IP address, from which the client obtained configuration
parameters.

·     giaddr—Gateway IP address. It is the IP address of the first relay agent to
which a request message travels.

·     chaddr—Client hardware address.

·     sname—Server host name, from which the client obtained configuration
parameters.

·     file—Boot file (also called system software image) name and path
information, defined by the server to the client.

·     options—Optional parameters field that is variable in length. Optional
parameters include the message type, lease duration, subnet mask, domain name
server IP address, and WINS IP address.

## DHCP options

DHCP extends the message format as an
extension to BOOTP for compatibility. DHCP uses the options field to carry
information for dynamic address allocation and provide additional configuration
information for clients.

Figure 4 DHCP
option format

![](https://resource.h3c.com/en/202407/12/20240712_11704542_x_Img_x_png_3_2215993_294551_0.png)

‌

## Common DHCP options

The following are common DHCP options:

·     Option 3—Router option. It specifies the
gateway address to be assigned to the clients.

·     Option 6—DNS server option. It specifies the
DNS server IP address to be assigned to the clients.

·     Option 33—Static route option. It specifies a
list of classful static routes (the destination addresses in these static
routes are classful) that a client should add into its routing table. If both
Option 33 and Option 121 exist, Option 33 is ignored.

·     Option 51—IP address lease option.

·     Option 53—DHCP message type option. It
identifies the type of the DHCP message.

·     Option 55—Parameter request list option. It is
used by a DHCP client to request specified configuration parameters. The option
includes values that correspond to the parameters requested by the client.

·     Option 60—Vendor class identifier option. A
DHCP client uses this option to identify its vendor. A DHCP server uses this
option to distinguish DHCP clients, and assigns IP addresses to them.

·     Option 66—TFTP server name option. It
specifies the TFTP server domain name to be assigned to the clients.

·     Option 67—Boot file name option. It specifies
the boot file name to be assigned to the client. 

·     Option 121—Classless route option. It
specifies a list of classless static routes (the destination addresses in these
static routes are classless) that a client should add into its routing table. If
both Option 33 and Option 121 exist, Option 33 is ignored.

·     Option 150—TFTP server IP address option. It specifies the TFTP server IP
address to be assigned to the clients.

For more information about DHCP options,
see RFC 2132 and RFC 3442\.

## Custom DHCP options

Some options, such as Option 43, Option 82,
and Option 184, have no standard definitions in RFC 2132\.

### Vendor-specific option (Option 43\)

#### Option 43 function

DHCP servers and clients use Option 43 to exchange
vendor-specific configuration information. 

The DHCP client can obtain the following
information through Option 43:

·     ACS parameters, including the ACS URL, username,
and password.

·     Service provider identifier, which is acquired
by the CPE from the DHCP server and sent to the ACS for selecting
vender-specific configurations and parameters. For more information about CPE
and ACS, see Network Management and Monitoring
Configuration Guide.

·     PXE server address, which is used to obtain the boot
file or other control information from the PXE server.

#### Option 43 format

Figure 5 Option 43 format

![](https://resource.h3c.com/en/202407/12/20240712_11704543_x_Img_x_png_4_2215993_294551_0.png)

‌

Network
configuration parameters are carried in different sub-options of Option 43 as
shown in[Figure 5](#_Ref162259442).

·     Sub-option type—The field value can be 0x01 (ACS parameter sub-option), 0x02 (service
provider identifier sub-option), or 0x80 (PXE server address sub-option).

·     Sub-option length—Excludes the sub-option type and sub-option length fields.

·     Sub-option value—The value format varies by sub-option.

#### Sub-option value field format

·     ACS parameter sub-option
value field—Includes the ACS URL, username, and
password separated by spaces (hexadecimal number 20\) as shown in [Figure 6](#_Ref213054505).

Figure 6 ACS parameter sub-option value field

![](https://resource.h3c.com/en/202407/12/20240712_11704544_x_Img_x_png_5_2215993_294551_0.png)

‌

·     Service provider identifier
sub-option value field—Includes the service
provider identifier.

·     PXE server address
sub-option value field—Includes the PXE server
type that can only be 0, the server number that indicates the number of PXE
servers contained in the sub-option, and server IP addresses, as shown in [Figure 7](#_Ref162260257).

Figure 7 PXE server address sub-option value field

![](https://resource.h3c.com/en/202407/12/20240712_11704552_x_Img_x_png_6_2215993_294551_0.png)

‌

### Relay agent option (Option 82\)

Option 82 is the relay agent option. It
records the location information about the DHCP client. When a DHCP relay agent
or DHCP snooping device receives a client's request, it adds Option 82 to the
request and sends it to the server.

The administrator can use Option 82 to locate
the DHCP client and further implement security control and accounting. The DHCP
server can use Option 82 to provide individual configuration policies for the
clients.

Option 82 can include a maximum of 255
sub-options and must include a minimum of one sub-option. Option 82 supports the
following sub-options: sub-option 1 (Circuit ID), sub-option 2 (Remote ID),
sub-option 5 (Link Selection), and sub-option 9 (Vendor-Specific). Option 82
has no standard definition. Its padding formats vary by vendor.

·     Circuit ID has the following padding modes:

¡     String padding mode—Includes a
character string specified by the user.

¡     Normal padding mode—Includes the VLAN
ID and interface number of the interface that receives the client's request.

¡     Verbose padding mode—Includes the
access node identifier specified by the user, and the VLAN ID, interface number
and interface type of the interface that receives the client's request.

·     Remote ID has the following padding modes:

¡     String padding mode—Includes a
character string specified by the user.

¡     Normal padding mode—Includes the MAC
address of the DHCP relay agent interface or the MAC address of the DHCP
snooping device that receives the client's request.

¡     Sysname padding mode—Includes the name
of the device. To set the device name, use the sysname command in
system view.

·     The Link Selection sub-option carries one of the
following IP addresses:

¡     Gateway
address in the AAA authorized address pool or the address pool bound to the
relay interface.

¡     IP
address of the relay interface.

If you use the dhcp relay
source-address command, you must enable the DHCP
relay agent to support Option 82 by using the dhcp relay
information enable command. This sub-option will
then be included in Option 82\. 

·     The Vendor-Specific sub-option supports only the
bas padding mode. The padding content includes the user-configured access node
identifier and the VLAN ID, interface number, and interface type of the
interface that receives the client's request. This sub-option is supported only
on DHCP snooping devices.

### Option 184

Option 184 is a reserved option. You can
define the parameters in the option as needed. The device supports Option 184 carrying
voice related parameters, so a DHCP client with voice functions can get voice
parameters from the DHCP server.

Option 184 has the following sub-options:

·     Sub-option 1—Specifies the IP address of the primary network calling processor.
The primary processor acts as the network calling control source and provides
program download services. For Option 184, you must define sub-option 1 to make
other sub-options take effect.

·     Sub\-option 2—Specifies
the IP address of the backup network calling processor. DHCP clients contact the
backup processor when the primary one is unreachable.

·     Sub\-option 3—Specifies
the voice VLAN ID and the result whether the DHCP client takes this VLAN as the
voice VLAN.

·     Sub\-option 4—Specifies
the failover route that includes the IP address and the number of the target
user. A SIP VoIP user uses this IP address and number to directly establish a
connection to the target SIP user when both the primary and backup calling
processors are unreachable.

## Protocols and standards

·     RFC 2131, Dynamic
Host Configuration Protocol

·     RFC 2132, DHCP Options
and BOOTP Vendor Extensions

·     RFC 1542, Clarifications
and Extensions for the Bootstrap Protocol

·     RFC 3046, DHCP Relay
Agent Information Option

·     RFC 3442, The
Classless Static Route Option for Dynamic Host Configuration Protocol (DHCP)
version 4

 

