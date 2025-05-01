
# Configuring tunneling

This chapter describes tunnel interface
configuration. For information about tunnel modes, see the subsequent chapters.

## About tunneling

Tunneling encapsulates the packets of a network
protocol within the packets of a second network protocol and transfers them
over a virtual point-to-point connection. The virtual connection is called a
tunnel. Packets are encapsulated at the tunnel source and de-encapsulated at
the tunnel destination.

## Supported tunneling technologies

Tunneling supports the following
technologies:

·     GRE tunneling. For more
information, see "Configuring GRE."

·     VXLAN tunneling. For more information, see VXLAN Configuration Guide.

·     IPv6 over IPv4 tunneling, IPv4 over IPv4
tunneling, and IPv4/IPv6 over IPv6 tunneling. 

## Restrictions and guidelines: Tunnel interface configuration

Do not specify the
same tunnel source and destination addresses for the tunnel interfaces on the
same device.

On IPv4 and IPv6 VXLAN tunnel interfaces, IP
addresses (assigned by ip address and ipv6 address commands) are meaningless. As a best practice, do not assign IP
addresses to those types of tunnel interfaces. 

## Configuring a tunnel interface

### About tunnel interface configuration

Configure a tunnel interface (Layer 3 virtual
interface) at both ends of a tunnel. The devices use the tunnel interface to
identify, process, and send packets for the tunnel.

### Tunnel interface configuration tasks at a glance

To configure a tunnel interface, perform
the following tasks:

**1\.**[Creating a tunnel interface](#_Ref474251613)

**2\.**(Optional.) [Specifying traffic processing slots for the
tunnel interface](#_Ref474251620)

**3\.**(Optional.) [Configuring parameters for tunneled packets](#_Ref474251635)

**4\.**(Optional.) [Specifying the tunnel destination VPN
instance](#_Ref474251642)

**5\.**(Optional.) [Restoring the default settings of the
tunnel interface](#_Ref474251651)

### Creating a tunnel interface

**1\.**Enter system view.

system-view

**2\.**Create a tunnel interface, specify the tunnel
mode, and enter tunnel interface view.

interface tunnel number mode { gre \[ ipv6 ] \| ipv4-ipv4 \| ipv4-ipv6 \| ipv6-ipv4 \[ 6to4 \| isatap ]\| ipv6-ipv6 \| vxlan \[ ipv6 ] }

For packet tunneling to succeed, the two
ends of a tunnel must use the same tunnel mode.

**3\.**Configure a source address or source
interface for the tunnel interface.

source { ipv4-address \| ipv6-address \| interface-type interface-number }

By default, no source address or source interface
is configured for the tunnel interface.

If you specify a source address, it is
used as the source address of tunneled packets.

If you specify a source interface, the
primary IP address of this interface is used as the source IP address of
tunneled packets.

**4\.**Configure a destination address for the
tunnel interface.

destination { ipv4-address \| ipv6-address }

By default, no destination address is
configured for the tunnel interface.

The tunnel destination address must be
the IP address of the receiving interface on the tunnel peer. It is used as the
destination IP address of tunneled packets.

**5\.**(Optional.) Configure a description for the
interface.

description text

By default, the description for a tunnel
interface is Tunnel number Interface.

**6\.**(Optional.) Set the MTU of the tunnel interface.

mtu size

The default settings are as follows:

¡     If
the tunnel interface has never been up, the MTU is 64000 bytes. 

¡     If
the tunnel interface is up, its MTU is identical to the outgoing interface's
MTU minus the length of the tunnel headers. The outgoing interface is
automatically obtained through routing table lookup based on the tunnel destination
address. 

You can use the ip mtu, ipv6 mtu, or mtu command to set the MTU of a tunnel interface. 

¡     The mtu command applies to both IPv4 and IPv6 tunnel interfaces.

¡     The ip mtu and ipv6 mtu commands have
higher priority than the mtu command on a tunnel
interface.

¡     If all
of the commands are used on a tunnel interface, the ip mtu and ipv6 mtu commands take
effect. After you restore the default settings for the ip mtu and ipv6 mtu commands, the mtu command takes effect.

¡     If none
of the commands is used on a tunnel interface, the MTU of the tunnel interface
is identical to the outgoing interface's MTU minus the length of the tunnel
headers. The outgoing interface is automatically obtained through routing table
lookup based on the tunnel destination address.

**7\.**(Optional.) Set the expected bandwidth for the tunnel interface.

bandwidth bandwidth-value

The default expected bandwidth (in kbps)
is the interface maximum rate divided by 1000\.

The expected bandwidth is an
informational parameter used only by higher-layer protocols for calculation.
You cannot adjust the actual bandwidth of an interface by using this command.

**8\.**Bring up the tunnel interface.

undo shutdown

By default, a tunnel interface is not
administratively down. 

### Specifying traffic processing slots for the tunnel interface

#### About this task

Specify a traffic processing slot if a
feature (for example, IPsec antireplay) requires that all traffic on a tunnel
interface be processed on the same slot.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter tunnel interface view.

interface tunnel number

**3\.**Specify a traffic processing slot for the
tunnel interface.

service slot slot-number

By default, no traffic processing slot is
specified.

### Configuring parameters for tunneled packets

**1\.**Enter system view.

system-view

**2\.**Enter tunnel interface view.

interface tunnel number

**3\.**Set the ToS for tunneled packets.

tunnel tos tos-value

The default setting is the same as the
ToS of the original packets.

**4\.**Set the TTL for tunneled packets.

tunnel ttl ttl-value

The default TTL for tunneled packets is
255\.

### Specifying the tunnel destination VPN instance

#### Restrictions and guidelines

For a tunnel interface to come up, the
tunnel source and destination must belong to the same VPN instance. To specify
a VPN instance for the tunnel source, use the ip binding vpn-instance command on the tunnel source interface. For more information about
this command, see MPLS Command Reference.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter tunnel interface view.

interface tunnel number

**3\.**Specify the VPN instance to which the tunnel
destination belongs.

tunnel vpn-instance vpn-instance-name

By default, the tunnel destination
belongs to the public network.

### Restoring the default settings of the tunnel interface

#### Restrictions and guidelines

| CAUTION | CAUTION:  This operation might interrupt ongoing network services. Make sure you are fully aware of the impact of this operation when you perform it on a live network. |
| --- | --- |

 

This operation might fail to restore the
default settings for some commands for reasons such as command dependencies or
system restrictions. Use the display this
command in interface view to identify these commands. Use their undo forms or follow the command reference to restore their default
settings. If your restoration attempt still fails, follow the error message
instructions to resolve the problem.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter tunnel interface view.

interface tunnel number

**3\.**Restore the default settings of the tunnel
interface.

default

## Display and maintenance commands for tunnel interface configuration

Execute display
commands in any view and reset commands in user
view.

 

| Task | Command | Remarks |
| --- | --- | --- |
| Display information about tunnel interfaces. | display interface \[ tunnel \[ number ] ] \[ brief \[ description \| down ] ] | N/A || Display IPv6 information on tunnel interfaces. | display ipv6 interface \[ tunnel \[ number ] ] \[ brief ] | For more information about this command, see IPv6 basics in Layer 3—IP Services Command Reference. || Clear statistics on tunnel interfaces. | reset counters interface \[ tunnel \[ number ] ] | N/A || Clear IPv6 statistics on tunnel interfaces. | reset ipv6 statistics \[ slot slot-number ] | For more information about this command, see IPv6 basics in Layer 3—IP Services Command Reference. |




‌

## Troubleshooting tunnel interface configuration

### Tunnel interface not up

#### Symptom

A tunnel interface configured with related
parameters such as tunnel source address, tunnel destination address, and
tunnel mode cannot come up.

#### Analysis

The physical interface of the tunnel does
not come up, or the tunnel destination is unreachable.

#### Solution

**1\.**To resolve the problem:

¡     Use
the display
interface or display ipv6 interface command to verify that the physical interface of the tunnel is up.
If the physical interface is down, check the network connection. 

¡     Use
the display
ipv6 routing-table or display ip
routing-table command to verify that the tunnel
destination is reachable. If the route is not available, configure a route to
reach the tunnel destination.

**2\.**If the problem persists, contact H3C
Support.

