
# Configuring UDP helper

## About UDP helper

UDP helper can provide the following packet
conversion services for packets with specific UDP destination port numbers:

·     Convert broadcast to unicast, and forward the
unicast packets to specific destinations.

·     Convert broadcast to multicast, and forward the
multicast packets.

## Configuring UDP helper to convert broadcast to unicast

#### About this task

You can configure UDP helper to convert
broadcast packets with specific UDP port numbers to unicast packets.

Upon receiving a UDP broadcast packet, UDP
helper uses the configured UDP ports to match the UDP destination port number
of the packet.

·     If a match is found, UDP helper duplicates the
packet and modifies the destination IP address of the copy to the configured
unicast address. Then UDP helper forwards the unicast packet to the unicast address.


·     If no match is found, UDP helper does not
process the packet.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enable UDP helper.

udp-helper enable

By default, UDP helper is disabled.

**3\.**Specify a UDP port number for UDP helper.

udp-helper port { port-number \| dns \| netbios\-ds \| netbios-ns \| tacacs \| tftp \| time }

By default, no UDP port numbers are
specified for UDP helper.

Do not set UDP ports 67 and 68 for UDP
helper, because UDP helper cannot forward DHCP broadcast packets.

You can specify a maximum of 256 UDP ports
for UDP helper.

**4\.**Enter interface view.

interface interface-type
interface-number

**5\.**Specify a destination server for UDP helper
to convert broadcast to unicast.

udp-helper server ip-address \[ global \| vpn-instance vpn-instance-name ]

By default, no destination servers are
specified.

Use this command on the interface that
receives broadcast packets.

You can specify a maximum of 20 unicast
and multicast addresses for UDP helper to convert broadcast packets on an
interface.

## Configuring UDP helper to convert broadcast to multicast

#### About this task

You can configure UDP helper to convert broadcast
packets with specific UDP port numbers to multicast packets.

Upon receiving a UDP broadcast packet, UDP
helper uses the configured UDP ports to match the UDP destination port number
of the packet.

·     If a match is found, UDP helper duplicates the
packet and modifies the destination IP address of the copy to the configured multicast
address. Then UDP helper forwards the packet to the multicast group.

·     If no match is found, UDP helper does not
process the packet.

#### Restrictions and guidelines

For an ACL used by UDP helper, if you
specify the vpn-instance keyword
for an ACL rule, the rule takes effect only on VPN packets. If you do not
specify the vpn-instance keyword
for an ACL rule, the rule takes effect only on public network packets.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enable UDP helper.

udp-helper enable

By default, UDP helper is disabled.

**3\.**Specify a UDP port number for UDP helper.

udp-helper port { port-number \| dns \| netbios\-ds \| netbios-ns \| tacacs \| tftp \| time }

By default, no UDP port numbers are specified
for UDP helper.

Do not set UDP ports 67 and 68 for UDP
helper, because UDP helper cannot forward DHCP broadcast packets.

You can specify a maximum of 256 UDP ports
for UDP helper.

**4\.**Enter interface view.

interface interface-type
interface-number

**5\.**Specify a destination multicast address for
UDP helper to convert broadcast to multicast.

udp-helper broadcast-map multicast-address \[ acl acl-number ]

By default, no destination multicast
addresses are specified for UDP helper.

Use this command on the interface that
receives broadcast packets.

You can specify a maximum of 20 unicast
and multicast addresses for UDP helper to convert broadcast packets on an
interface.

## Display and maintenance commands for UDP helper

Execute display command
in any view and reset commands in user
view.

 

| Task | Command |
| --- | --- |
| Display information about broadcast to unicast conversion by UDP helper on an interface. | display udp-helper interface interface-type interface-number || Clear packet statistics for UDP helper. | reset udp-helper statistics |


 

## UDP helper configuration examples

### Example: Configuring UDP helper to convert broadcast to unicast

#### Network configuration

As shown in [Figure 1](#_Ref259692618), configure
UDP helper to convert broadcast to unicast on VLAN-interface 1 of Switch A.
This feature enables Switch A to forward broadcast packets with UDP destination
port number 55 to the destination server 10.2.1.1/16.

Figure 1 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11704643_x_Img_x_png_0_2216006_294551_0.png)

 

#### Procedure

Make sure Switch A can reach the subnet 10.2.0.0/16.

\# Enable UDP helper.

\[SwitchA] System-view

\[SwitchA] udp-helper enable

\# Enable the UDP port 55 for UDP helper.

\[SwitchA] udp-helper port 55

\# Specify the destination server 10.2.1.1 for
UDP helper to convert broadcast to unicast on VLAN-interface 1\.

\[SwitchA] interface vlan-interface 1

\[SwitchA-Vlan-interface1] ip address
10.110.1.1 16

\[SwitchA-Vlan-interface1] udp-helper
server 10.2.1.1

#### Verifying the configuration

\# Display information about broadcast to
unicast conversion by UDP helper on VLAN-interface 1\.

\[SwitchA-Vlan-interface1] display
udp-helper interface vlan-interface 1

Interface                Server VPN
instance           Server address Packets sent

Vlan-interface1          N/A                          
10.2.1.1       5

### Example: Configuring UDP helper to convert broadcast to multicast

#### Network configuration

As shown in [Figure 2](#_Ref369786635),
VLAN-interface 1 of Switch B can receive multicast packets destined to
225.1.1.1. 

Configure UDP helper to convert broadcast
to multicast on VLAN-interface 1 of Switch A. This feature enables Switch A to
forward broadcast packets with UDP destination port number 55 to the multicast
group 225.1.1.1.

Figure 2 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11704644_x_Img_x_png_1_2216006_294551_0.png)

 

#### Procedure

Make sure Switch A can reach the subnet 10.2.0.0/16.

**1\.**Configure Switch A:

\# Enable UDP helper.

\<SwitchA\> system-view

\[SwitchA] udp-helper enable

\# Enable the UDP port 55 for UDP helper.

\[SwitchA] udp-helper port 55

\# Configure UDP helper to convert
broadcast packets to multicast packets destined for 225.1.1.1. 

\[SwitchA] interface vlan-interface
1

\[SwitchA-Vlan-interface1] ip
address 10.110.1.1 16

\[SwitchA-Vlan-interface1] udp-helper
broadcast-map 225.1.1.1

\[SwitchA-Vlan-interface1] quit

\# Enable IP multicast routing globally.

\[SwitchA] multicast routing 

\[SwitchA-mrib] quit

Enable PIM-DM on VLAN-interface 1\.

\[SwitchA] interface vlan-interface
1

\[SwitchA-Vlan-interface1] pim
dm

\[SwitchA-Vlan-interface1] quit

\# Enable PIM-DM and IGMP on VLAN-interface
2\.

\[SwitchA] interface
vlan-interface 2

\[SwitchA-Vlan-interface2] pim
dm

\[SwitchA-Vlan-interface2] igmp
enable

\# Configure VLAN-interface 2 as a static
member of multicast group 225.1.1.1.

\[SwitchA-Vlan-interface2] igmp
static-group 225.1.1.1

**2\.**Configure Switch B:

\# Enable IP multicast routing globally.

\<SwitchB\> system-view

\[SwitchB] multicast routing

\[SwitchB-mrib] quit

\# Enable PIM-DM and IGMP on VLAN-interface
1\.

\[SwitchB] interface vlan-interface 1

\[SwitchB-Vlan-interface1] pim dm

\[SwitchB-Vlan-interface1] igmp enable

\# Configure VLAN-interface 1 as a static
member of multicast group 225.1.1.1.

\[SwitchB-Vlan-interface1] igmp static-group 225.1.1.1

#### Verifying the configuration

Verify that you can capture multicast packets
from Switch A on Switch B.

