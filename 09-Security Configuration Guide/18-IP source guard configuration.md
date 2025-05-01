
# Configuring IP source guard

## About IPSG

IP source guard (IPSG) prevents spoofing
attacks by using an IPSG binding table to filter out illegitimate packets. This
feature is typically configured on user-side interfaces.

### IPSG operating mechanism

The IPSG binding table contains bindings
that bind IP address, MAC address, VLAN, or any combinations. IPSG uses the
bindings to match an incoming packet. If a match is found, the packet is
forwarded. If no match is found, the packet is discarded.

IPSG is a per-interface packet filter. Configuring
this feature on one interface does not affect packet forwarding on another
interface.

IPSG bindings can be static or dynamic.

As shown in [Figure 1](#_Ref231725897), IPSG
forwards only the packets that match an IPSG binding.

Figure 1 IPSGapplication

![](https://resource.h3c.com/en/202407/12/20240712_11705463_x_Img_x_png_0_2216079_294551_0.png) 

 

### Static IPSG bindings

Static IPSG bindings are configured
manually. They are suitable for scenarios where few hosts exist on a LAN and their
IP addresses are manually configured. For example, you can configure a static IPSG
binding on an interface that connects to a server. This binding allows the interface
to receive packets only from the server.

Static IPSG bindings on an interface implement
the following functions:

·     Filter incoming IPv4 or IPv6 packets on the interface.

·     Cooperate with ARP attack detection in IPv4 for
user validity checking. For information about ARP attack detection, see "Configuring
ARP attack protection." 

·     Cooperate with ND attack detection in IPv6 for
user validity checking. For information about ND attack detection, see "Configuring
ND attack defense."

Static IPSG bindings can be global or
interface-specific. 

·     Global static binding—Binds the IP address and MAC address in system view. The binding takes
effect on all interfaces to filter packets for user spoofing attack prevention.

·     Interface-specific static
binding—Binds the IP address, MAC address, VLAN,
or any combination of the items in interface view. The binding takes effect
only on the interface to check the validity of users who are attempting to
access the interface.

### Dynamic IPSGbindings

IPSG automatically obtains user information
from other modules to generate dynamic bindings. A dynamic IPSG binding can contain
MAC address, IPv4 or IPv6 address, VLAN tag, ingress interface, and binding
type. The binding type identifies the source module for the binding, such as
DHCP snooping, DHCPv6 snooping, DHCP relay agent, or DHCPv6 relay agent.

For example, DHCP-based IPSG bindings are suitable
for scenarios where hosts on a LAN obtain IP addresses through DHCP. IPSG is
configured on the DHCP server, the DHCP snooping device, or the DHCP relay
agent. It generates dynamic bindings based on the client bindings on the DHCP
server, the DHCP snooping entries, or the DHCP relay entries. IPSG allows only
packets from the DHCP clients to pass through.

#### Dynamic IPv4SG

Dynamic bindings generated based on
different source modules are for different usages:

 

| Interface types | Source modules | Binding usage |
| --- | --- | --- |
| Layer 2 Ethernet interface | DHCP snooping  802.1X | Packet filtering. || ARP snooping | For cooperation with modules (such as the MFF module) to provide security services. || Layer 3 Ethernet interface  VLAN interface | DHCP relay agent | Packet filtering. || DHCP server | For cooperation with modules (such as the authorized ARP module) to provide security services. |




‌

For more information about 802.1X, see
"Configuring 802.1X." For information about ARP snooping, DHCP
snooping, DHCP relay, and DHCP server, see Layer 3—IP
Services Configuration Guide.

#### Dynamic IPv6SG

Dynamic IPv6SG bindings generated based on different
source modules are for different usages:

 

| Interface types | Source modules | Binding usage |
| --- | --- | --- |
| Layer 2 Ethernet interface | DHCPv6 snooping  ND snooping | Packet filtering. || 802.1X || Layer 3 Ethernet interface  VLAN interface | DHCPv6 relay agent | Packet filtering. |



‌

For more information about DHCPv6 snooping,
see Layer 3—IP Services Configuration Guide. For
more information about ND snooping, see IPv6 basics configuration in Layer 3—IP Services Configuration Guide. For more
information about DHCPv6 relay agent, see Layer 3—IP
Services Configuration Guide. For more information about DHCPv6 server,
see Layer 3—IP Services Configuration Guide.

### IPSG bindings synchronized by routing protocols

In addition to static and dynamic IPSG
bindings, the device also supports IPSG bindings synchronized by routing
protocols (such as BGP) from remote devices. These bindings are called remote
IPSG bindings. By default, the remote IPSG bindings do not have interface
information and cannot be used for packet filtering. When users roam from
remote devices to the local device, the device learns interface information for
the remote IPSG bindings through ARP or ND entries of the users. Then, the
device converts the remote IPSG bindings into local IPSG bindings and uses
these bindings for packet filtering.

For more information about BGP, see BGP
overview in Layer 3—IP Routing Configuration Guide.

## IPSG tasks at a glance

To configure IPv4SG, perform the following
tasks:

**1\.**[Enabling IPv4SG](#_Ref312247426)

**2\.**(Optional.) [Configuring a static IPv4SG binding](#_Ref350860025)

**3\.**(Optional.) [Excluding IPv4 packets from IPSG
filtering](#_Ref455766502)

**4\.**(Optional.) [Enabling IPv4SG alarming](#_Ref29209102)

To configure IPv6SG, perform the following tasks:

**1\.**[Enabling IPv6SG](#_Ref484095381)

**2\.**(Optional.) [Configuring a static IPv6SG binding](#_Ref350860057)

**3\.**(Optional.) [Setting the maximum number of IPv6SG
bindings on an interface](#_Ref456716912)

**4\.**(Optional.) [Enabling IPv6SG alarming](#_Ref29209108)

## Configuring the IPv4SG feature

### Enabling IPv4SG

#### About this task

When you enable IPSG on an interface, the
static and dynamic IPSG are both enabled.

·     Static IPv4SG uses static bindings configured by
using the ip source binding command. For more information, see "[Configuring a static IPv4SG binding](#_Ref350860079)."

·     Dynamic IPv4SG generates dynamic bindings from
related source modules. IPv4SG uses the bindings to filter incoming IPv4
packets based on the matching criteria specified in the ip verify source command.

In a VLAN enabled with IPv4SG, you can
configure an interface as an IPv4SG trusted port if you can determine that no
attack sources are connected to the interface. IPv4SG does not filter incoming
packets on that trusted interface.

#### Restrictions and guidelines

To implement dynamic IPv4SG, make sure 802.1X,
ARP snooping, DHCP snooping, DHCP relay agent, or DHCP server operates correctly
on the network.

You can enable IPv4SG on an interface or in
a VLAN. The interface-specific configuration applies to this interface. The
VLAN-specific configuration applies to all Layer 2 Ethernet interfaces that
belong to this VLAN.

Do not configure IPv4SG both in a VLAN and
on an Ethernet interface that belongs to the VLAN. Before you perform one of
the configurations, make sure the other configuration does not exist.

After you enable IPv4SG on an interface,
statistics collection for incoming Layer 3 packets does not take effect. For
more information about Layer 3 packet statistics collection, see IP performance
optimization configuration in Layer 3—IP Services
Configuration Guide. 

#### Enabling IPv4SG on an interface

**1\.**Enter system view.

system-view

**2\.**Enter interface view.

interface interface-type
interface-number

The following interface types are
supported:

¡     Layer
2 Ethernet interface.

¡     Layer
2 aggregate interface.

¡     Layer
3 Ethernet interface.

¡     Layer
3 Ethernet subinterface.

¡     Layer
3 aggregate interface.

¡     Layer
3 aggregate subinterface.

¡     VLAN
interface. 

**3\.**Enable the IPv4SG feature.

ip verify source { ip-address \| ip-address mac-address \| mac-address }

By default, the IPv4SG feature is
disabled on an interface.

#### Enabling IPv4SG in a VLAN

**1\.**Enter system view.

system-view

**2\.**Enter VLAN view.

vlan vlan-id

**3\.**Enable IPv4SG in the VLAN.

ip verify source { ip-address \| ip-address mac-address \| mac-address }

By default, IPv4SG is disabled in a VLAN.

**4\.**(Optional.) Execute the following commands
in sequence to configure an IPv4SG trusted port.

**a.**Return to system view.

quit

**b.**Enter the view of a Layer 2 Ethernet
interface in the VLAN.

interface interface-type interface-number

**c.**Configure the interface as an IPv4SG trusted
port.

ip verify source trust

### Configuring a static IPv4SGbinding

#### About this task

You can configure global static and interface-specific
static IPv4SG bindings. Interface-specific static and dynamic bindings take
priority over global static bindings. An interface first uses the static and
dynamic bindings on the interface to match packets. If no match is found, the
interface uses the global bindings.

#### Restrictions and guidelines

Global static bindings take effect on all
interfaces on the device.

To configure a static IPv4SG binding for
the ARP attack detection feature, make sure the following conditions are met:

·     The ip-address ip-address option, the mac-address mac-address option, and the vlan vlan-id option must be specified.

·     ARP attack detection must be enabled for the
specified VLAN.

#### Configuring a global static IPv4SG binding

**1\.**Enter system view.

system-view

**2\.**Configure a global static IPv4SG binding.

ip source binding ip-address ip-address mac-address mac-address

#### Configuring a static IPv4SG binding on an interface

**1\.**Enter system view.

system-view

**2\.**Enter interface view.

interface interface-type
interface-number

The following interface types are
supported:

¡     Layer
2 Ethernet interface.

¡     Layer
2 aggregate interface.

¡     Layer
3 Ethernet interface.

¡     Layer
3 Ethernet subinterface.

¡     Layer
3 aggregate interface.

¡     Layer
3 aggregate subinterface.

¡     VLAN
interface. 

**3\.**Configure a static IPv4SG binding.

ip source binding { ip-address ip-address \| ip-address ip-address mac-address mac-address \| mac-address mac-address } \[ vlan vlan-id ]

You can configure the same static IPv4SG
binding on different interfaces.

### Excluding IPv4 packets from IPSG filtering

#### About this task

By default, IPv4SG processes all incoming
IPv4 packets on an interface and discards the packets that do not match IPSG
bindings. To allow specific IPv4 packets that do not match any IPSG binding to
pass through the interface, you can specify source items of the packets for
IPSG filtering exemption. All IPv4 packets with the specified source items are
forwarded without being processed by IPSG.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Exclude IPv4 packets with the specified
source items from IPSG filtering.

ip verify source exclude vlan start-vlan-id \[ to end-vlan-id ]

By default, no excluded source items are
configured.

You can execute this command multiple
times to specify multiple excluded VLANs. The specified excluded VLANs cannot
overlap.

### Enabling IPv4SG alarming

#### About this task

This feature monitors the number of packets
dropped by IPv4SG per second. When the packet dropping rate reaches or exceeds
the alarm threshold, the device generates an alarm log. When the packet
dropping rate drops below the alarm threshold, the device generates an
alarm-cleared log.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter interface view.

interface interface-type interface-number

**3\.**Enable IPv4SG alarming on the interface.

ip verify source alarm \[ alarm-threshold ]

By default, IPv4SG alarming is disabled
on an interface.

## Configuring the IPv6SG feature

### Enabling IPv6SG

#### About this task

When you enable IPv6SG on an interface, the
static and dynamic IPv6SG are both enabled.

·     Static IPv6SG uses static bindings configured by
using the ipv6 source binding
command. For more information, see "[Configuring a static IPv6SG binding](#_Ref350860108)."

·     Dynamic IPv6SG generates dynamic bindings from
related source modules. IPv6SG uses the bindings to filter incoming IPv6
packets based on the matching criteria specified in the ipv6 verify source command.

In a VLAN enabled with IPv6SG, you can
configure an interface as an IPv6SG trusted port if you can determine that no
attack sources are connect to the interface. IPv6SG does not filter incoming
packets on that trusted interface.

#### Restrictions and guidelines

To implement dynamic IPv6SG, make sure DHCPv6
snooping, DHCPv6 relay agent, or ND snooping operates correctly on the network.

You can enable IPv6SG on an interface or in
a VLAN. The interface-specific configuration applies to this interface. The
VLAN-specific configuration applies to all Layer 2 Ethernet interfaces that
belong to this VLAN.

Do not configure IPv6SG both in a VLAN and
on an Ethernet interface that belongs to the VLAN. Before you perform one of
the configurations, make sure the other configuration does not exist.

#### Enabling IPv6SG on an interface

**1\.**Enter system view.

system-view

**2\.**Enter interface view.

interface interface-type
interface-number

The following interface types are
supported:

¡     Layer
2 Ethernet interface.

¡     Layer
2 aggregate interface.

¡     Layer
3 Ethernet interface. 

¡     Layer
3 Ethernet subinterface.

¡     Layer
3 aggregate interface.

¡     Layer
3 aggregate subinterface.

¡     VLAN
interface. 

**3\.**Enable the IPv6SG feature.

ipv6 verify source { ip-address \| ip-address mac-address \| mac-address }

By default, the IPv6SG feature is
disabled on an interface.

#### Enabling IPv6SG in a VLAN

**1\.**Enter system view.

system-view

**2\.**Enter VLAN view.

vlan vlan-id

**3\.**Enable IPv6SG in the VLAN.

ipv6 verify source { ip-address \| ip-address mac-address \| mac-address }

By default, IPv6SG is disabled in a VLAN.

**4\.**(Optional.) Execute the following commands
in sequence to configure an IPv6SG trusted port.

**a.**Return to system view.

quit

**b.**Enter the view of a Layer 2 Ethernet
interface in the VLAN.

interface interface-type interface-number

**c.**Configure the interface as an IPv6SG trusted
port.

ipv6 verify source trust

### Configuring a staticIPv6SG binding

#### About this task

You can configure global static and interface-specific
static IPv6SG bindings. Interface-specific static and dynamic bindings take
priority over global static bindings. An interface first uses the static and
dynamic bindings on the interface to match packets. If no match is found, the
interface uses the global bindings.

#### Restrictions and guidelines

Global static bindings take effect on all
interfaces on the device.

To configure a static IPv6SG binding for
the ND attack detection feature, the vlan vlan-id option must be specified, and ND attack detection must be enabled
for the specified VLAN.

#### Configuring a global static IPv6SG binding

**1\.**Enter system view.

system-view

**2\.**Configure a global static IPv6SG binding.

ipv6 source binding ip-address ipv6-address mac-address mac-address

#### Configuring a static IPv6SG binding on an interface

**1\.**Enter system view.

system-view

**2\.**Enter interface view.

interface interface-type
interface-number

The following interface types are
supported:

¡     Layer
2 Ethernet interface.

¡     Layer
2 aggregate interface.

¡     Layer
3 Ethernet interface.

¡     Layer
3 Ethernet subinterface.

¡     Layer
3 aggregate interface.

¡     Layer
3 aggregate subinterface.

¡     VLAN
interface. 

**3\.**Configure a static IPv6SG binding.

ipv6 source binding { ip-address ipv6-address \| ip-address ipv6-address mac-address mac-address \| mac-address mac-address } \[ vlan vlan-id ]

You can configure the same static IPv6SG
binding on different interfaces.

### Setting the maximum number of IPv6SG bindings on an interface

#### About this task

You can set the maximum number of IPv6SG
bindings on an interface to limit the total number of static and dynamic IPv6SG
bindings on the interface. If the upper limit is reached, no more IPv6SG
bindings can be added on the interface.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter Layer 2 Ethernet interface view.

interface interface-type
interface-number

**3\.**Set the maximum number of IPv6SG bindings on
the interface.

ipv6 verify source max-entries number

By default, the maximum number of IPv6SG
bindings is not limited on an interface.

### Enabling IPv6SG alarming

#### About this task

This feature enables the device to monitor
the number of packets dropped by IPv6SG per second. When the packet dropping
rate reaches or exceeds the alarm threshold, the device generates an alarm log.
When the packet dropping rate drops below the alarm threshold, the device
generates an alarm-cleared log.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter interface view.

interface interface-type interface-number

**3\.**Enable IPv6SG alarming on the interface.

ipv6 verify source alarm \[ alarm-threshold ]

By default, IPv6SG alarming is disabled
on an interface.

## Display and maintenance commands for IPSG

Execute display
commands in any view and reset commands in user
view.

 

| Task | Command |
| --- | --- |
| Display IPv4SG bindings. | display ip source binding \[ static \| \[ vpn-instance vpn-instance-name ] \[ arp-snooping-vlan \| arp-snooping-vsi \| dhcp-relay \| dhcp-server \| dhcp-snooping \| dot1x \| remote ] ] \[ ip-address ip-address ] \[ mac-address mac-address ] \[ vlan vlan-id ] \[ interface interface-type interface-number ] \[ slot slot-number ] || Display local IPv4SG bindings that can be synchronized by routing protocols. | display ip source binding-local \[ interface interface-type interface-number ] \[ dhcp-relay ] \[ ip-address ip-address ] \[ mac-address mac-address ] \[ vlan vlan-id ] \[ slot slot-number ] || Display remote IPv4SG bindings synchronized by routing protocols. | display ip source binding-remote \[ router-id router-id ] \[ dhcp-relay ] \[ ip-address ip-address ] \[ mac-address mac-address ] \[ vlan vlan-id ] \[ slot slot-number ] || Display statistics about local and remote IPv4SG bindings that routing protocols synchronize. | display ip source binding statistics || Display source items that have been configured to be excluded from IPSG filtering. | display ip verify source excluded \[ vlan start-vlan-id \[ to end-vlan-id ] ] \[ slot slot-number ] || Display IPv6SG address bindings. | display ipv6 source binding \[ static \| \[ vpn-instance vpn-instance-name ] \[ dhcpv6-relay \| dhcpv6-server \| dhcpv6-snooping \| dot1x \| nd-snooping-vlan \| nd-snooping-vsi \| remote ] ] \[ ip-address ipv6-address ] \[ mac-address mac-address ] \[ vlan vlan-id ] \[ interface interface-type interface-number ] \[ slot slot-number ] || Display local IPv6SG bindings that can be synchronized by routing protocols. | display ipv6 source binding-local \[ interface interface-type interface-number ] \[ dhcpv6-relay \| nd-snooping-vlan ] \[ ip-address ipv6-address ] \[ mac-address mac-address ] \[ vlan vlan-id ] \[ slot slot-number ] || Display remote IPv6SG bindings synchronized by routing protocols. | display ipv6 source binding-remote \[ router-id router-id ] \[ dhcpv6-relay \| nd-snooping-vlan] \[ ip-address ipv6-address ] \[ mac-address mac-address ] \[ vlan vlan-id ] \[ slot slot-number ] || Display IPv6SG prefix bindings. | display ipv6 source binding pd \[ vpn-instance vpn-instance-name ] \[ prefix prefix/prefix-length ] \[ mac-address mac-address ] \[ vlan vlan-id ] \[ interface interface-type interface-number ] \[ slot slot-number ] || Display statistics about local and remote IPv6SG bindings that routing protocols synchronize. | display ipv6 source binding statistics |










 

## IPSG configuration examples

### Example: Configuring static IPv4SG

#### Network configuration

As shown in [Figure 2](#_Ref131999610), all
hosts use static IP addresses.

Configure static IPv4SG bindings on Device A
and Device B to meet the following requirements:

·     All interfaces of Device A allow IP packets from
Host A to pass.

·     Ten-GigabitEthernet 1/0/1 of Device A allows IP packets from Host B to pass.

Figure 2 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705464_x_Img_x_png_1_2216079_294551_0.png)

‌

#### Procedure

\# Configure IP addresses for the interfaces.
(Details not shown.)

\# Enable IPv4SG on Ten-GigabitEthernet 1/0/2.

\<DeviceA\> system-view

\[DeviceA] interface ten-gigabitethernet 1/0/2

\[DeviceA-Ten-GigabitEthernet1/0/2] ip verify source
ip-address mac-address

\[DeviceA-Ten-GigabitEthernet1/0/2] quit

\# Configure a static IPv4SG binding for
Host A.

\[DeviceA] ip source binding ip-address
192.168.0.1 mac-address 0001-0203-0406

\# Enable IPv4SG on Ten-GigabitEthernet 1/0/1.

\[DeviceA] interface ten-gigabitethernet 1/0/1

\[DeviceA-Ten-GigabitEthernet1/0/1] ip verify source
ip-address mac-address

\# On Ten-GigabitEthernet 1/0/1, configure a
static IPv4SG binding for Host B.

\[DeviceA-Ten-GigabitEthernet1/0/1] ip source binding
mac-address 0001-0203-0407

\[DeviceA-Ten-GigabitEthernet1/0/1] quit

#### Verifying the configuration

\# Verify that the static IPv4SG bindings
are configured successfully on Device A.

\<DeviceA\> display ip source binding
static

Total entries found: 2

IP address      MAC address   
Interface                VLAN Type

192.168.0.1     0001-0203-0406 N/A                      N/A 
Static

N/A             0001-0203-0407 XGE1/0/1            
    N/A  Static

### Example: Configuring DHCP snooping-based dynamic IPv4SG

#### Network configuration

As shown in [Figure 3](#_Ref213841642), the
host (the DHCP client) obtains an IP address from the DHCP server. Perform the
following tasks:

·     Enable DHCP snooping on the device to make sure
the DHCP client obtains an IP address from the authorized DHCP server. To
generate a DHCP snooping entry for the DHCP client, enable recording of client
information in DHCP snooping entries.

·     Enable dynamic IPv4SG on Ten-GigabitEthernet 1/0/1 to filter incoming packets
by using the IPv4SG bindings generated based on DHCP snooping entries. Only
packets from the DHCP client are allowed to pass.

Figure 3 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705465_x_Img_x_png_2_2216079_294551_0.png)

‌

#### Procedure

**1\.**Configure the DHCP server.

For information about DHCP server
configuration, see Layer 3—IP Services Configuration
Guide.

**2\.**Configure the device:

\# Configure IP addresses for the
interfaces. (Details not shown.)

\# Enable DHCP snooping.

\<Device\> system-view

\[Device] dhcp snooping enable

\# Configure Ten-GigabitEthernet 1/0/2 as a trusted interface.

\[Device] interface ten-gigabitethernet 1/0/2

\[Device-Ten-GigabitEthernet1/0/2] dhcp snooping trust

\[Device-Ten-GigabitEthernet1/0/2] quit

\# Enable IPv4SG on Ten-GigabitEthernet 1/0/1 and verify the
source IP address and MAC address for dynamic IPSG.

\[Device] interface ten-gigabitethernet 1/0/1

\[Device-Ten-GigabitEthernet1/0/1] ip verify source
ip-address mac-address

\# Enable recording of client information
in DHCP snooping entries on Ten-GigabitEthernet 1/0/1.

\[Device-Ten-GigabitEthernet1/0/1] dhcp snooping
binding record

\[Device-Ten-GigabitEthernet1/0/1] quit

#### Verifying the configuration

\# Display dynamic IPv4SG bindings generated
based on DHCP snooping entries.

\[Device] display ip source binding
dhcp-snooping

Total entries found: 1

IP address      MAC address   
Interface                VLAN Type

192.168.0.1     0001-0203-0406 XGE1/0/1            
    1    DHCP snooping

Ten-GigabitEthernet 1/0/1 will filter
packets based on the IPv4SG binding.

### Example: Configuring DHCP relay agent-based dynamic IPv4SG

#### Network configuration

As shown in [Figure 4](#_Ref242872600), DHCP
relay agent is enabled on the switch. The host obtains an IP address from the
DHCP server through the DHCP relay agent. 

Enable dynamic IPv4SG on VLAN-interface 100
to filter incoming packets by using the IPv4SG bindings generated based on DHCP
relay entries.

Figure 4 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705466_x_Img_x_png_3_2216079_294551_0.png)

 

#### Procedure

**1\.**Configure dynamic IPv4SG:

\# Configure IP addresses for the
interfaces. (Details not shown.)

\# Enable IPv4SG on VLAN-interface 100 and
verify the source IP address and MAC address for dynamic IPSG.

\<Switch\> system-view

\[Switch] interface vlan-interface
100

\[Switch-Vlan-interface100] ip verify
source ip-address mac-address

\[Switch-Vlan-interface100]
quit

**2\.**Configure the DHCP relay agent:

\# Enable the DHCP service.

\[Switch] dhcp enable

\# Enable recording DHCP relay entries.

\[Switch] dhcp relay client-information
record

\# Configure VLAN-interface 100 to operate
in DHCP relay mode.

\[Switch] interface vlan-interface
100

\[Switch-Vlan-interface100]
dhcp select relay

\# Specify the IP address of the DHCP
server.

\[Switch-Vlan-interface100] dhcp
relay server-address 10.1.1.1

\[Switch-Vlan-interface100]
quit

#### Verifying the configuration

\# Display dynamic IPv4SG bindings generated
based on DHCP relay entries.

\[Switch] display ip source binding
dhcp-relay

Total entries found: 1

IP address      MAC address   
Interface                VLAN Type

192.168.0.1     0001-0203-0406
Vlan100                  100  DHCP relay

VLAN-interface 100 will filter packets
based on the IPv4SG binding.

### Example: Configuring static IPv6SG

#### Network configuration

As shown in [Figure 5](#_Ref242872419), configure
a static IPv6SG binding on Ten-GigabitEthernet 1/0/1 of the device to allow
only IPv6 packets from the host to pass.

Figure 5 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705467_x_Img_x_png_4_2216079_294551_0.png)

‌

#### Procedure

\# Enable IPv6SG on Ten-GigabitEthernet 1/0/1.

\<Device\> system-view

\[Device] interface ten-gigabitethernet 1/0/1

\[Device-Ten-GigabitEthernet1/0/1] ipv6 verify source
ip-address mac-address

\# On Ten-GigabitEthernet 1/0/1, configure a
static IPv6SG binding for the host.

\[Device-Ten-GigabitEthernet1/0/1] ipv6 source
binding ip-address 2001::1 mac-address 0001-0202-0202

\[Device-Ten-GigabitEthernet1/0/1] quit

#### Verifying the configuration

\# Verify that the static IPv6SG binding is
configured successfully on the device.

\[Device] display ipv6 source binding
static

Total entries found: 1

IPv6 address         MAC address   
Interface               VLAN Type

2001::1              0001-0202-0202 XGE1/0/1  
             N/A  Static

### Example: Configuring DHCPv6 snooping-based dynamic IPv6SG address bindings

#### Network configuration

As shown in [Figure 6](#_Ref242866562),
the host (the DHCPv6 client) obtains an IP address from the DHCPv6 server.
Perform the following tasks:

·     Enable DHCPv6 snooping on the device to make
sure the DHCPv6 client obtains an IPv6 address from the authorized DHCPv6
server. To generate a DHCPv6 snooping entry for the DHCPv6 client, enable
recording of client information in DHCPv6 snooping entries.

·     Enable dynamic IPv6SG on Ten-GigabitEthernet 1/0/1 to filter incoming packets
by using the IPv6SG bindings generated based on DHCPv6 snooping entries. Only
packets from the DHCPv6 client are allowed to pass.

Figure 6 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705468_x_Img_x_png_5_2216079_294551_0.png)

‌

#### Procedure

**1\.**Configure DHCPv6 snooping:

\# Enable DHCPv6 snooping globally.

\<Device\> system-view

\[Device] ipv6 dhcp snooping
enable

\# Configure Ten-GigabitEthernet 1/0/2 as a trusted interface.

\[Device] interface ten-gigabitethernet 1/0/2

\[Device-Ten-GigabitEthernet1/0/2] ipv6 dhcp snooping
trust

\[Device-Ten-GigabitEthernet1/0/2] quit

**2\.**Enable IPv6SG:

\# Enable IPv6SG on Ten-GigabitEthernet 1/0/1 and verify the
source IP address and MAC address for dynamic IPv6SG.

\[Device] interface ten-gigabitethernet 1/0/1

\[Device-Ten-GigabitEthernet1/0/1] ipv6 verify source
ip-address mac-address

\# Enable recording of client information
in DHCPv6 snooping entries on Ten-GigabitEthernet 1/0/1.

\[Device-Ten-GigabitEthernet1/0/1] ipv6 dhcp snooping
binding record

\[Device-Ten-GigabitEthernet1/0/1] quit

#### Verifying the configuration

\# Display dynamic IPv6SG bindings generated
based on DHCPv6 snooping entries.

\[Device] display ipv6 source binding
dhcpv6-snooping

Total entries found: 1

IPv6 address         MAC address   
Interface               VLAN Type

2001::1              040a-0000-0001 XGE1/0/1      
         1    DHCPv6 snooping

Ten-GigabitEthernet 1/0/1 will filter
packets based on the IPv6SG binding.

### Example: Configuring DHCPv6 snooping-based dynamic IPv6SG prefix bindings

#### Network configuration

As shown in [Figure 7](#_Ref483992517), the
host (the DHCPv6 client) obtains an IPv6 prefix from the DHCPv6 server. Perform
the following tasks:

·     Enable DHCPv6 snooping on the device to make
sure the DHCPv6 client obtains an IPv6 prefix from the authorized DHCPv6 server.
To generate a DHCPv6 snooping prefix entry for the DHCPv6 client, enable recording
IPv6 prefix information in DHCPv6 snooping entries.

·     Enable dynamic IPv6SG on Ten-GigabitEthernet 1/0/1 to filter incoming packets
by using the IPv6SG bindings generated based on DHCPv6 snooping prefix entries.
Only packets from the DHCPv6 client are allowed to pass.

Figure 7 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705469_x_Img_x_png_6_2216079_294551_0.png)

‌

#### Procedure

**1\.**Configure DHCPv6 snooping.

\# Enable DHCPv6 snooping globally.

\<Device\> system-view

\[Device] ipv6 dhcp snooping
enable

\# Configure Ten-GigabitEthernet 1/0/2 as a trusted interface.

\[Device] interface ten-gigabitethernet 1/0/2

\[Device-Ten-GigabitEthernet1/0/2] ipv6 dhcp snooping
trust

\[Device-Ten-GigabitEthernet1/0/2] quit

\# Enable recording DHCPv6 snooping prefix
entries on Ten-GigabitEthernet 1/0/1.

\[Device] interface ten-gigabitethernet 1/0/1

\[Device-Ten-GigabitEthernet1/0/1] ipv6 dhcp snooping
pd binding record

**2\.**Enable IPv6SG.

\# Enable IPv6SG on Ten-GigabitEthernet 1/0/1 and verify the
source IP address and MAC address for dynamic IPv6SG.

\[Device-Ten-GigabitEthernet1/0/1] ipv6 verify source
ip-address mac-address

\[Device-Ten-GigabitEthernet1/0/1] quit

#### Verifying the configuration

\# Display dynamic IPv6SG bindings generated
based on DHCPv6 snooping entries.

\[Device] display ipv6 source binding
pd

Total entries found: 1

IPv6 address         MAC address   
Interface               VLAN Type

2001:410:1::/48      0010-9400-0004 XGE1/0/1               
1

Ten-GigabitEthernet 1/0/1 will filter
packets based on the IPv6SG binding.

### Example: Configuring DHCPv6 relay agent-based dynamic IPv6SG

#### Network configuration

As shown in [Figure 8](#_Ref422151046),
DHCPv6 relay agent is enabled on the switch. The clients obtain IPv6 addresses from
the DHCPv6 server through the DHCPv6 relay agent.

Enable dynamic IPv6SG on VLAN-interface 3
to filter incoming packets by using the IPv6SG bindings generated based on
DHCPv6 relay entries.

Figure 8 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705470_x_Img_x_png_7_2216079_294551_0.png)

 

#### Procedure

**1\.**Configure the DHCPv6 relay agent:

\# Create VLAN 2 and VLAN 3, assign
interfaces to the VLANs, and specify IP addresses for VLAN-interface 2 and VLAN-interface
3\. (Details not shown.)

\# Enable the DHCPv6 relay agent on
VLAN-interface 3\.

\[Switch] interface vlan-interface
3

\[Switch-Vlan-interface3] ipv6 dhcp
select relay

\# Enable recording of DHCPv6 relay
entries on the interface.

\[Switch-Vlan-interface3] ipv6
dhcp relay client-information record

\# Specify the DHCPv6 server address 2::2 on
the relay agent.

\[Switch-Vlan-interface3] ipv6 dhcp
relay server-address 2::2

\[Switch-Vlan-interface3] quit

**2\.**Enable IPv6SG on VLAN-interface 3 and verify
the source IP address and MAC address for dynamic IPv6SG.

\<Switch\> system-view

\[Switch] interface vlan-interface
3

\[Switch-Vlan-interface3] ipv6 verify
source ip-address mac-address

\[Switch-Vlan-interface3] quit

#### Verifying the configuration

\# Display dynamic IPv6SG bindings generated
based on DHCPv6 relay entries.

\[Switch] display ipv6 source binding
dhcpv6-relay

Total entries found: 1

IP Address      MAC Address   
Interface                VLAN Type

1::2            0001-0203-0406 Vlan3 
                  3    DHCPv6 relay

VLAN-interface 3 will filter packets based
on the IPv6SG binding.

 

