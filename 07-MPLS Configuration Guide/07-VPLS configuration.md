
# Configuring VPLS

## About VPLS

Virtual
Private LAN Service (VPLS) delivers a point-to-multipoint L2VPN service over an
MPLS or IP backbone. The provider backbone emulates a switch to connect all
geographically dispersed sites of each customer network. The backbone is
transparent to the customer sites. The sites can communicate with each other as
if they were on the same LAN.

### Basic VPLS architecture

Figure 1 Basic VPLS architecture

![](https://resource.h3c.com/en/202407/12/20240712_11705155_x_Img_x_png_0_2216051_294551_0.png)

‌

As shown in [Figure 1](#_Ref341972077), the
VPLS architecture mainly includes the following components:

·     CE—A customer edge device is directly connected to the service
provider network.

·     PE—A provider edge device connects one or more CEs to the service
provider network. A PE implements VPN access by mapping and forwarding packets
between private networks and public network tunnels. A PE can be a UPE or NPE
in a hierarchical VPLS.

·     AC—An attachment circuit, physical or virtual, connects a CE and a PE,
such as a VLAN.

·     PW—A pseudowire is a bidirectional virtual connection between two PEs.
An MPLS PW consists of two unidirectional MPLS LSPs in opposite directions.

·     Tunnel—A tunnel can be an LSP tunnel or an MPLS TE tunnel. It carries one
or more PWs over an IP/MPLS backbone. If a PW is carried on an LSP or MPLS TE
tunnel, each packet on the PW contains two labels. The inner label is the PW
label, which identifies the PW and ensures that the packet is forwarded to the
correct VSI. The outer label is the public LSP or MPLS TE tunnel label, which ensures
that the packet is correctly forwarded to the remote PE.

·     VPLS instance—A customer network might contain multiple geographically dispersed
sites (such as site 1 and site 3 in [Figure 1](#_Ref341972077).) The
service provider uses VPLS to connect all the sites to create a single Layer 2
VPN, which is referred to as a VPLS instance. Sites in different VPLS instances
cannot communicate with each other at Layer 2\.

·     VSI—A virtual switch instance provides Layer 2 switching services for a
VPLS instance on a PE. A VSI acts as a virtual switch that has all the functions
of a conventional Ethernet switch, including source MAC address learning, MAC
address aging, and flooding. VPLS uses VSIs to forward Layer 2 data packets in
VPLS instances.

### Creating a PW

In a VPLS network, PEs use PWs to forward
packets among VPN sites.

A PW is created as follow:

**1\.**Specify the address
of the remote PE or configure an auto-discovery protocol to automatically find
the remote PE. BGP is the most commonly used auto-discovery protocol.

**2\.**Specify the incoming label and the outgoing
label for the PW on the two PEs to create the PW. You can also use LDP or BGP
to advertise the PW-label binding to the remote PE to establish the unidirectional
LSPs. The PW is created once the two unidirectional LSPs are created.

### PW types

PEs use PWs to forward packets among VPN
sites. PWs include static PWs, LDP PWs, BGP PWs, and BGP auto-discovery LDP PWs.

#### Static PW

To create a static PW, specify the address
of the remote PE, the incoming label, and the outgoing label. 

#### LDP PW

To create an LDP PW, specify the address of
the remote PE, and use LDP to advertise the PW-label binding to the remote PE.
After the two PEs receive the PW-label binding from each other, they establish an
LDP PW. The FEC type in the LDP message is PWid FEC Element that includes the
PW ID field (FEC 128). The PW ID identifies the PW bound to the PW label. 

#### BGP PW

To create a BGP PW, configure BGP to advertise
label block information to the remote PE. After the two PEs receive label block
information from each other, they use the label block information to calculate
the incoming and outgoing labels and create the BGP PW. A PE also uses the
received label block information to automatically find the remote PE. 

#### BGP auto-discovery LDP PW

To create a BGP auto-discovery LDP PW, configure
BGP to automatically find the remote PE, and use LDP to advertise the PW-label
binding to the remote PE. After the two PEs receive the PW-label binding from
each other, they establish a BGP auto-discovery LDP PW. 

The information advertised by BGP includes
the ID (for example, LSR ID) and VPLS ID of the advertising PE. The receiving
PE compares the received VPLS ID with its own VPLS ID. If the two VPLS IDs are
identical, the two PEs use LDP to establish a PW. If not, the PEs do not
establish a PW. The FEC type in the LDP message is Generalized PWid FEC Element
(FEC 129), which contains the VPLS ID, Source Attachment Individual Identifier (SAII),
and Target Attachment Individual Identifier (TAII). The SAII is the LSR ID of the
advertising PE. The TAII identifies the remote PE and is advertised by the
remote PE. VPLS ID\+SAII\+TAII uniquely identifies a PW in a VPLS instance.

### MAC address learning, aging, and withdrawal

#### Source MAC address learning

VPLS provides reachability through source
MAC learning. A PE maintains a MAC address table for each VSI.

As shown in [Figure 2](#_Ref341972079), a PE learns
source MAC addresses in the following ways:

·     Learning the source MAC addresses of directly connected
sites.

If the source MAC address of a packet
from a CE does not exist in the MAC address table, the PE learns the source MAC
address of the AC connected to the CE.

·     Learning the source MAC addresses of remote
sites connected through PWs.

A VSI regards a PW as a logical Ethernet
interface. If the source MAC address of a packet from a PW does not exist in
the MAC address table, the PE learns the source MAC address on the PW of the
VSI.

Figure 2 Source MAC address learning on a PE

![](https://resource.h3c.com/en/202407/12/20240712_11705156_x_Img_x_png_1_2216051_294551_0.png)

‌

#### MAC address aging

The MAC address table uses an aging timer
for each dynamic MAC address entry. If no packet is received from a MAC address
before the aging timer expires, VPLS deletes the MAC address.

#### MAC address withdrawal

When an AC or a PW goes down, the PE
deletes MAC addresses on the AC or PW. Then it sends an LDP address withdrawal
message to notify all other PEs in the VPLS instance to delete those MAC
addresses.

### Traffic forwarding and flooding

#### Unicast traffic forwarding and flooding

After a PE receives a unicast packet from
an AC, the PE searches the MAC address table of the VSI bound to the AC for
packet forwarding.

·     If a match is found, the PE forwards the packet
according to the matching entry. 

¡     If
the outgoing interface in the entry is a PW, the PE inserts the PW label to the
packet, and adds the public tunnel header to the packet. It then forwards the
packet to the remote PE over the PW. If the PW is carried on an LSP or MPLS TE
tunnel, each packet on the PW contains two labels. The inner label is the PW
label, which identifies the PW and ensures that the packet is forwarded to the
correct VSI. The outer label is the public LSP or MPLS TE tunnel label, which ensures
that the packet is correctly forwarded to the remote PE.

¡     If
the outgoing interface in the entry is a local interface, the PE directly
forwards the packet to the local interface. 

·     If no match is found, the PE floods the packet
to all other ACs and PWs in the VSI.

After a PE receives a unicast packet from a
PW, the PE searches the MAC address table of the VSI bound to the PW for packet
forwarding.

·     If a match is found, the PE forwards the packet through
the outgoing interface in the matching entry.

·     If no match is found, the PE floods the packet
to all ACs in the VSI.

#### Multicast and broadcast traffic forwarding and flooding

After a PE receives a multicast or
broadcast packet from an AC, the PE floods the packet to all other ACs and the
PWs in the VSI bound to the AC.

After a PE receives a multicast or
broadcast packet from a PW, the PE floods the packet to all ACs in the VSI bound
to the PW.

### PW full mesh and split horizon

A Layer 2 network requires a loop
prevention protocol such as STP to avoid loops. However, a loop prevention
protocol on PEs brings management and maintenance difficulties. Therefore, VPLS
uses the following methods to prevent loops:

·     Full mesh—Every two PEs in a VPLS instance must establish a PW. The PWs form
a full mesh among PEs in the VPLS instance.

·     Split horizon—A PE does not forward packets received from a PW to any other PWs
in the same VSI but only forwards those packets to ACs.

### H-VPLS

#### About this task

VPLS requires a full mesh of PWs among all
PEs in a VPLS instance. In a large-scale network, however, a full mesh of PWs causes
very high PW signaling overhead and brings difficulties for network management
and expansion. Hierarchical VPLS (H-VPLS) reduces the number of PWs by dividing
a VPLS network into a backbone domain and edge domains.

Only static PWs and LDP PWs support H-VPLS.

In H-VPLS:

·     An edge domain provides access for a user
network to the backbone domain.

·     The Network Provider Edge (NPE) devices are
fully meshed in the backbone domain. A PW between NPEs is referred to as an
N-PW.

·     A User facing-Provider Edge (UPE) device only
establishes a PW with the neighboring NPE. A PW between a UPE and an NPE is
referred to as a U-PW.

#### H-VPLS access modes

H-VPLS supports the following access modes:

·     MPLS access mode

As shown in [Figure 3](#_Ref149817328), the
edge domain is an MPLS network. The UPE creates a U-PW with NPE 1\. The UPE does
not create PWs to any remote PEs. After receiving a packet from a CE, the UPE
adds the label assigned to the U-PW into the packet and forwards the packet to
NPE 1 through a public tunnel. NPE 1 maps the packet to the VSI that
corresponds to the PW label, and searches the MAC address table of the VSI to
forward the packet.

Figure 3 H-VPLS
using MPLS access

![](https://resource.h3c.com/en/202407/12/20240712_11705157_x_Img_x_png_2_2216051_294551_0.png)

‌

|  | NOTE:  A U-PW created on the NPE must have split horizon disabled because the NPE needs to forward packets between U-PW and N-PW. |
| --- | --- |

‌

·     Ethernet access mode

As shown in [Figure 4](#_Ref341972085), the
edge domain is an Ethernet network. The UPE and NPE 1 establish a point-to-point
Ethernet QinQ connection in between. After the UPE receives a packet from a CE,
it adds an outer VLAN tag into the packet and forwards the packet to NPE 1\. NPE
1 regards the outer VLAN tag as the service provider VLAN tag. It maps the
packet to the VSI that corresponds to the VLAN tag and then searches the MAC
address table of the VSI to forward the packet.

Figure 4 H-VPLS
using Ethernet access

![](https://resource.h3c.com/en/202407/12/20240712_11705158_x_Img_x_png_3_2216051_294551_0.png)

‌

## Restrictions and guidelines: VPLS configuration

Before configuring MPLS, perform the
following tasks:

·     In standalone mode, execute the switch-mode 3 command to switch the operating mode of the device to MPLS, and then
restart the device.

·     In IRF mode, execute the switch-mode 4 command to switch the operating mode of the device to MPLS-IRF, and
then restart the IRF fabric.

A PE does not transmit LACP and LLDP
packets through the VPLS network.

If a PE is enabled with the spanning tree
feature, the PE does not transmit BPDUs through the VPLS network. To enable the
PE to transmit BPDUs through the VPLS network, use the stp transparent
enable command to enable BPDU transparent
transmission for the PE. For more information about the stp transparent
enable command, see STP commands in Layer 2—LAN Switching Command Reference.

## VPLS tasks at a glance

Perform the following configuration tasks
on a PE:

**1\.**[Enabling L2VPN](#_Ref326590658)

**2\.**[Configuring an AC](#_Ref341972233)

**3\.**[Configuring a VSI](#_Ref341972246)

**4\.**[Configuring a PW](#_Ref341972251)

Choose the following tasks as needed:

¡     [Configuring a static PW](#_Ref341972262)

¡     [Configuring an LDP PW](#_Ref341972269)

¡     [Configuring a BGP PW](#_Ref370809825)

¡     [Configuring a BGP auto-discovery LDP PW](#_Ref370809832)

¡     (Optional.)
[Configuring the BGP L2VPN address family](#_Ref471833900)

You can configure this task to control
route advertisement and selection in BGP L2VPN address family when you
configure a BGP PW or BGP auto-discovery LDP PW.

¡     (Optional.)
[Maintaining BGP sessions](#_Ref471833926)

If BGP configuration is changed, you can
reset or soft-reset BGP sessions to validate the new configuration for BGP PWs
and BGP auto-discovery LDP PWs.

**5\.**[Binding an AC to a
VSI](#_Ref357782305)

In an H-VPLS using MPLS access, this task
is not needed on the access NPEs of the UPEs.

**6\.**(Optional.) [Configuring MAC address learning](#_Ref244319138)

**7\.**(Optional.) [Enabling VPLS statistics](#_Ref413681064)

**8\.**(Optional.) [Enabling SNMP notifications for L2VPN PW](#_Ref469320553) 

## Prerequisites for VPLS

To configure a VPLS network, perform the
following tasks:

·     Configure an IGP to ensure IP connectivity
within the backbone.

·     Configure basic MPLS, LDP, or MPLS TE to
establish public tunnels on the backbone network.

## EnablingL2VPN

**1\.**Enter system view.

system-view

**2\.**Enable L2VPN.

l2vpn enable

By default, L2VPN is disabled.

## Configuring an AC

### Configuring an Ethernet service instance

#### About this task

When the PE is connected to a CE through a
Layer 2 Ethernet or Layer 2 aggregate interface, configure an Ethernet service
instance on the interface to match packets from the AC. The Ethernet service
instance provides flexible methods for matching packets to be forwarded in the
VSI.

#### Restrictions and guidelines

For information about configuring the match
criterion of an Ethernet service instance by using the encapsulation command, see VPLS commands in MPLS Command
Reference.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter interface view.

¡     Enter
Layer 2 Ethernet interface view.

interface interface-type interface-number

¡     Enter
Layer 2 aggregate interface view.

interface bridge-aggregation interface-number

**3\.**Create an Ethernet service instance and
enter Ethernet service instance view.

service-instance instance-id

**4\.**Configure a packet match criterion for the
Ethernet service instance.

¡     Match packets with the specified outer VLAN IDs.

encapsulation s-vid vlan-id

¡     Match packets that do not have a VLAN tag.

encapsulation untagged

By default, no packet match criterion is
configured.

## Configuring a VSI

**1\.**Enter system view.

system-view

**2\.**Create a VSI and enter VSI view.

vsi vsi-name 

**3\.**(Optional.) Configure a description for the
VSI.

description text

By default, no description is configured
for a VSI.

**4\.**(Optional.) Set the default PW ID for the
VSI.

default-pw-id default-pw-id

By default, no default PW ID is configured
for the VSI.

**5\.**Set an MTU for the VSI.

mtu size

By default, the MTU of a VSI is 1500
bytes.

**6\.**(Optional.) Enable the VSI.

undo shutdown

By default, a VSI is enabled.

## Configuring a PW

### Configuring a static PW

**1\.**Enter system view.

system-view

**2\.**Enter VSI view.

vsi vsi-name

**3\.**Specify static signaling for PWs, and enter
VSI static view.

pwsignaling static

By default, no PW signaling protocol is
specified.

**4\.**Configure a static PW, and enter VSI static
PW view.

peer ip-address \[ pw-id pw-id ] in-label label-value out-label label-value \[ no-split-horizon \| tunnel-policy tunnel-policy-name ] \*

 

| Parameter | Description || pw-id pw-id | If both the default PW ID in the default-pw-id command and a PW ID in the peer command are configured, the PW ID in the peer command is used. If only the default PW ID is configured, the default PW ID is used. If no default PW ID is configured, you must provide a PW ID in the peer command. || no-split-horizon | You must specify the no-split-horizon keyword to disable split horizon when you configure an NPE to establish a U-PW with a UPE. |


| --- | --- | --- | --- | --- | --- |

‌

### Configuring an LDP PW

#### Prerequisites

Before you configure an LDP PW, enable
global and interface MPLS LDP on the PE. For information about MPLS LDP
configuration, see "Configuring LDP."

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter VSI view.

vsi vsi-name

**3\.**Specify LDP signaling for PWs, and enter VSI
LDP signaling view.

pwsignaling ldp

By default, no PW signaling protocol is
specified.

**4\.**Configure an LDP PW, and enter VSI LDP PW
view.

peer ip-address \[ pw-id pw-id ] \[ no-split-horizon \| tunnel-policy tunnel-policy-name ] \*

 

| Parameter | Description || pw-id pw-id | If both the default PW ID in the default-pw-id command and a PW ID in the peer command are configured, the PW ID in the peer command is used. If only the default PW ID is configured, the default PW ID is used. If no default PW ID is configured, you must provide a PW ID in the peer command. || no-split-horizon | You must specify the no-split-horizon keyword to disable split horizon when you configure an NPE to establish a U-PW with a UPE. |


| --- | --- | --- | --- | --- | --- |

‌

### Configuring a BGP PW

#### Configuring BGP to advertise VPLS label block information

**1\.**Enter system view.

system-view

**2\.**Enable BGP instance and enter BGP instance
view.

bgp as-number \[ instance instance-name ]

By default, BGP is disabled.

For more information about this command,
see BGP in Layer 3—IP Routing Command Reference.

**3\.**Configure the remote PE as a BGP peer.

peer { group-name \| ip-address \[ mask-length ] } as-number as-number

For more information about this command,
see BGP in Layer 3—IP Routing Command Reference.

**4\.**Create the BGP L2VPN address family and
enter BGP L2VPN address family view.

address-family l2vpn

**5\.**Enable BGP to exchange L2VPN information
with the specified peer or peer group.

peer { group-name \| ip-address \[ mask-length ] } enable

By default, BGP cannot exchange L2VPN
information with any peer or peer group.

For more information about this command,
see BGP in Layer 3—IP Routing Command Reference.

**6\.**Enable BGP to exchange label block
information with the specified peer or peer group.

peer { group-name \| ip-address \[ mask-length ] } signaling

By default, BGP can exchange label block
information with a BGP L2VPN peer or peer group.

**7\.**(Optional.) Configure the BGP L2VPN address
family.

For more information, see "[Configuring the BGP L2VPN address family](#_Ref471833900)."

**8\.**(Optional.) Maintain BGP sessions.

For more information, see "[Maintaining BGP sessions](#_Ref471833926)."

#### Creating a BGP PW

**1\.**Enter system view.

system-view

**2\.**Enter VSI view.

vsi vsi-name

**3\.**Configure the VSI to automatically discover
neighbors through BGP and enter auto-discovery VSI view.

auto-discovery bgp

By default, a VSI does not automatically
discover neighbors through BGP.

**4\.**Configure an RD for the auto-discovery VSI.

route-distinguisher route-distinguisher

By default, no RD is configured for the
auto-discovery VSI.

**5\.**Configure route targets for the
auto-discovery VSI.

vpn-target vpn-target\&\<1-8\> \[ both \| export-extcommunity \| import-extcommunity ]

By default, no route targets are
configured for the auto-discovery VSI.

**6\.**(Optional.) Specify a tunnel policy for the
auto-discovery VSI.

tunnel-policy tunnel-policy-name

By default, no tunnel policy is
specified.

**7\.**Use BGP to create a PW to an automatically
discovered remote PE and enter auto-discovery VSI BGP signaling view.

signaling-protocol bgp

By default, no signaling protocol is
specified.

**8\.**Create a local site.

site site-id \[ range range-value ] \[ default-offset default-offset ]

### Configuring a BGP auto-discovery LDP PW

#### Prerequisites for configuring a BGP auto-discovery LDP PW

Before you perform this configuration,
enable MPLS LDP on interfaces and globally. For more information, see
"Configuring LDP."

#### Configuring BGP to advertise VPLS PE information

**1\.**Enter system view.

system-view

**2\.**Enable BGP instance and enter BGP instance
view.

bgp as-number \[ instance instance-name ]

By default, BGP is disabled.

For more information about this command,
see BGP in Layer 3—IP Routing Command Reference.

**3\.**Configure the remote PE as a BGP peer.

peer { group-name \| ip-address \[ mask-length ] } as-number as-number

For more information about this command,
see BGP in Layer 3—IP Routing Command Reference.

**4\.**Create the BGP L2VPN address family and
enter BGP L2VPN address family view.

address-family l2vpn

**5\.**Enable BGP to exchange VPLS PE information
with the specified peer or peer group.

peer { group-name \| ip-address \[ mask-length ] } enable

By default, BGP cannot exchange VPLS PE
information with a peer or peer group.

For more information about this command,
see BGP in Layer 3—IP Routing Command Reference.

**6\.**Enable BGP to exchange VPLS PE information
with the specified peer or peer group.

peer { group-name \| ip-address \[ mask-length ] } auto-discovery
\[ non-standard ]

By default, BGP can exchange VPLS PE
information with a BGP L2VPN peer or peer group by using RFC 6074 MP\_REACH\_NLRI.

**7\.**(Optional.) Configure the BGP L2VPN address
family.

For more information, see "[Configuring the BGP L2VPN address family](#_Ref471833900)."

**8\.**(Optional.) Maintain BGP sessions.

For more information, see "[Maintaining BGP sessions](#_Ref471833926)."

#### Creating a BGP auto-discovery LDP PW

**1\.**Enter system view.

system-view

**2\.**Enter VSI view.

vsi vsi-name

**3\.**Configure the VSI to automatically discover
neighbors through BGP and enter auto-discovery VSI view.

auto-discovery bgp

By default, a VSI does not automatically
discover neighbors through BGP.

**4\.**Configure an RD for the auto-discovery VSI.

route-distinguisher route-distinguisher

By default, no RD is configured for the
auto-discovery VSI.

**5\.**Configure route targets for the
auto-discovery VSI.

vpn-target vpn-target\&\<1-8\> \[ both \| export-extcommunity \| import-extcommunity ]

By default, no route targets are
configured for the auto-discovery VSI.

**6\.**(Optional.) Specify a tunnel policy for the
auto-discovery VSI.

tunnel-policy tunnel-policy-name

By default, no tunnel policy is
specified.

**7\.**Use LDP to create a PW to an automatically
discovered remote PE and enter auto-discovery VSI LDP signaling view.

signaling-protocol ldp

By default, no signaling protocol is
specified.

**8\.**Configure a VPLS ID for the VSI.

vpls-id vpls-id

By default, no VPLS ID is configured.

### Configuring the BGP L2VPN address family

#### About this task

When you configure a BGP PW or a BGP
auto-discovery LDP PW, you can perform this task to control route advertisement
and selection in the BGP L2VPN address family.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter BGP instance view.

bgp as-number \[ instance instance-name ]

**3\.**Enter BGP L2VPN address family view.

address-family l2vpn

**4\.**Permit the local AS number to appear in
routes from the specified peer or peer group and specify the maximum number of
the appearances.

peer { group-name \| ip-address \[ mask-length ] } allow-as-loop \[ number ]

By default, the local AS number is not
allowed in routes from a peer or peer group.

**5\.**Enable route target-based filtering of
incoming BGP L2VPN information. 

policy vpn-target

By default, route target-based filtering
of incoming BGP L2VPN information is enabled.

**6\.**Configure BGP route reflection.

¡     Configure
the device as a route reflector and specify a peer or peer group as its client.

peer { group-name \| ip-address \[ mask-length ] } reflect-client

By default, no route reflector or client
is configured.

¡     Enable
L2VPN information reflection between clients.

reflect between-clients

By default, L2VPN information reflection
is enabled between clients.

¡     Configure
the cluster ID of the route reflector.

reflector cluster-id { cluster-id \| ip-address }

By default, a route reflector uses its
own router ID as the cluster ID.

¡     Configure
the route reflector to filter reflected L2VPN information. 

rr-filter ext-comm-list-number

By default, a route reflector does not
filter reflected L2VPN information.

**7\.**Set the optimal route selection delay timer.

route-select delay delay-value

By default, the optimal route selection
delay timer is 0 seconds, which means optimal route selection is not delayed. For
more information about this command, see BGP commands in Layer 3—IP Routing Command Reference.

### Maintaining BGP sessions

#### About this task

If BGP configuration is changed, you can
reset or soft-reset BGP sessions to validate the new configuration for BGP PWs
and BGP auto-discovery LDP PWs.

A soft reset updates BGP routing
information without tearing down BGP connections. A reset operation updates BGP
routing information by tearing down, and then re-establishing BGP connections. Soft
reset requires that BGP peers have route refresh capability (support the
ROUTE-REFRESH message).

For more information about the commands in
this task, see BGP in Layer 3—IP Routing Command
Reference.

#### Procedure

To maintain BGP sessions, execute one of
the following commands in user view:

·     Soft-reset L2VPN BGP sessions.

refresh bgp \[ instance instance-name ] { ip-address \[ mask-length ] \| all \| external \| group group-name \| internal } { export \| import } l2vpn

·     Reset L2VPN BGP sessions.

reset bgp \[ instance instance-name ] { as-number \| ip-address \[ mask-length ] \| all \| external \| group group-name \| internal } l2vpn

## Binding an AC to a VSI

### About binding an AC to a VSI

On a Layer 2 Ethernet or Layer 2 aggregate
interface, you can create an Ethernet service instance and bind it to a VSI. The
Ethernet service instance matches packets received on the interface. The
matching packets are then forwarded based on the MAC address table of the VSI. An
Ethernet service instance can match all packets, tagged packets, or untagged
packets.

When you bind an AC to a VSI, you can associate
Track with the AC. Then, the AC is up only when one or more of the associated
track entries are positive.

Associating Track with an AC helps
detecting AC failure. For example, when an AC is a VE-L2VPN interface, the
interface will not go down upon a link failure because the interface is a
virtual interface. To resolve the problem, you can associate Track with the AC
to detect failures on the link that connects the PE-agg to the L3VPN or IP backbone.
When a failure occurs on the link, the VE-L2VPN interface is set to down.
Consequently, the PW bound to the AC goes down. If the PW has a backup PW,
traffic can be switched to the backup PW. For more information about VE-L2VPN
interfaces and L2VPN access to L3VPN or IP backbone, see "Configuring
L2VPN access to L3VPN or IP backbone."

### Binding an Ethernet service instance to aVSI

**1\.**Enter system view.

system-view

**2\.**Enter interface view.

¡     Enter Layer 2 Ethernet interface view:

interface interface-type interface-number

¡     Enter Layer 2 aggregate interface view:

interface bridge-aggregation interface-number

**3\.**Create an Ethernet service instance and
enter Ethernet service instance view.

service-instance instance-id

By default, no Ethernet service instances
exist.

**4\.**Bind the Ethernet service instance to a VSI.

xconnect vsi vsi-name \[ access-mode vlan] \[ track track-entry-number\&\<1-3\> ]

By default, an Ethernet service instance
is not bound to any VSI.

## Configuring MAC address learning

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter VSI view.

vsi vsi-name

**3\.**Enable MAC address learning for the VSI.

mac-learning enable

By default, MAC address learning is
enabled for a VSI.

## Enabling VPLS statistics

### Hardware compatibility with VPLS statistics

Only S6520X-HI and S5560X-HI series
switches support this feature.

### Enabling packet statistics for a PW

#### Restrictions and guidelines for enabling packet statistics for a PW

Perform this task to enable packet
statistics for a static PW or an LDP PW. To display packet statistics for the PW,
use the display l2vpn pw verbose command. To
clear packet statistics for the PW, use the reset l2vpn statistics pw command.

#### Enabling packet statistics for a static PW

**1\.**Enter system view.

system-view

**2\.**Enter VSI view.

vsi vsi-name

**3\.**Enter VSI static configuration view.

pwsignaling static

**4\.**Enter VSI static PW view.

peer ip-address \[ pw-id pw-id ] \[ in-label label-value out-label label-value \[ no-split-horizon \| pw-class class-name \| tunnel-policy tunnel-policy-name ] \* ]

**5\.**Enable packet statistics for the PW.

statistics enable

By default, packet statistics is disabled
for PWs created from the CLI, and is enabled for PWs created from the MIB. For
more information about MIBs, see SNMP in Network
Management and Monitoring Configuration Guide.

#### Enabling packet statistics for an LDP PW

**1\.**Enter system view.

system-view

**2\.**Enter VSI view.

vsi vsi-name

**3\.**Enter VSI LDP signaling view.

pwsignaling ldp

**4\.**Enter VSI LDP PW view.

peer ip-address \[ pw-id pw-id ] \[ no-split-horizon \| pw-class class-name \| tunnel-policy tunnel-policy-name
] \*

**5\.**Enable packet statistics for the PW.

statistics enable

By default, packet statistics is disabled
for PWs created from the CLI, and is enabled for PWs from the MIB. For more information
about MIBs, see SNMP in Network Management and Monitoring
Configuration Guide.

### Setting the L2VPN traffic statistics collection interval

#### About this task

Perform this task to set the L2VPN traffic
statistics collection interval for both PWs and ACs.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Set the L2VPN traffic statistics collection
interval.

l2vpn statistics interval interval

By default, the L2VPN traffic statistics
collection interval is 15 minutes.

## Enabling SNMP notifications for L2VPN PW

#### About this task

This feature enables L2VPN to generate SNMP
notifications when PW deletions, PW switchovers, or PW status changes occur. For
L2VPN event notifications to be sent correctly, you must also configure SNMP on
the device. For more information about SNMP configuration, see the network management
and monitoring configuration guide for the device.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enable SNMP notifications for L2VPN PW.

snmp-agent trap enable l2vpn \[ pw-delete \| pw-switch \| pw-up-down ] \*

By default, SNMP notifications for L2VPN
PW are disabled.

## Display and maintenance commands for VPLS

| IMPORTANT | IMPORTANT:  Only S6520X-HI and S5560X-HI series switches support the reset l2vpn statistics pw command. |
| --- | --- |

 

Execute display
commands in any view and reset commands in user
view.

 

| Task | Command || Display BGP L2VPN peer group information. | display bgp \[ instance instance-name ] group l2vpn \[ group-name group-name ] || Display VPLS PE information discovered by BGP. | display bgp \[ instance instance-name ] l2vpn auto-discovery \[ peer ip-address { advertised \| received } \[ statistics ] \| route-distinguisher route-distinguisher \[ pe-address ip-address \[ advertise-info ] ] \| statistics ] || Display VPLS label block information discovered by BGP. | display bgp \[ instance instance-name ] l2vpn signaling \[ peer ip-address { advertised \| received } \[ statistics ] \| route-distinguisher route-distinguisher \[ site-id site-id \[ label-offset label-offset \[ advertise-info ] ] ] \| statistics ] || Display BGP L2VPN peer information. | display bgp \[ instance instance-name ] peer l2vpn \[ ip-address mask-length \| group-name group-name log-info \| ip-address { log-info \| verbose } \| verbose ] || Display BGP L2VPN update group information. | display bgp \[ instance instance-name ] update-group l2vpn \[ ip-address ] || Display information about automatically discovered VPLS PEs. | display l2vpn auto-discovery \[ peer ip-address ] \[ vsi vsi-name ] || Display VPLS label block information. | display l2vpn bgp \[ instance instance-name ] \[ peer ip-address \| local ] \[ vsi vsi-name ] \[ verbose ] || Display L2VPN forwarding information. | display l2vpn forwarding { ac \| pw } \[ vsi vsi-name ] \[ slot slot-number ] \[ verbose ] || Display LDP PW label information. | display l2vpn ldp \[ peer ip-address \[ pw-id pw-id \| vpls-id vpls-id ] \| vsi vsi-name ] \[ verbose ] || Display MAC address table information for one or all VSIs. | display l2vpn mac-address \[ vsi vsi-name ] \[ dynamic ] \[ count \| verbose ] || Display the MAC address move records for all VSIs after the device is started. | display l2vpn mac-address mac-move \[ slot slot-number ] || Display L2VPN PW information. | display l2vpn mac-address \[ vsi vsi-name ] \[ dynamic ] \[ count \| verbose ] || Display Ethernet service instance information. | display l2vpn service-instance \[ interface interface-type interface-number \[ service-instance instance-id ] ] \[ verbose ] || Display VSI information. | display l2vpn vsi \[ name vsi-name ] \[ verbose ] || Reset L2VPN BGP sessions. | reset bgp { as-number \| ip-address \[ mask-length ] \| all \| external \| group group-name \| internal } l2vpn || Clear MAC address entries for one or all VSIs. | reset l2vpn mac-address \[ vsi vsi-name ] || Clear the MAC address move records for all VSIs after the device is started. | reset l2vpn mac-address mac-move || Clear packet statistics for PWs. | reset l2vpn statistics pw \[ vsi vsi-name \[ link link-id ] ] |


















| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |

‌

## VPLS configuration examples

### Example: Configuring static PWs

#### Network configuration

Configure a VSI on each PE, and establish
static PWs between the PEs to interconnect the CEs.

Configure an Ethernet service instance on Ten-GigabitEthernet 1/0/1 to match packets
with an outer VLAN ID of 100 on each PE. Bind the Ethernet service instance to the
VSI to forward the matching packets through the VSI.

Figure 5 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705160_x_Img_x_png_5_2216051_294551_0.png)

‌

#### Procedure

**1\.**Disable the spanning tree feature globally
or map each VLAN to an MSTI. (Details not shown.)

For more information, see spanning tree
in Layer 2—LAN Switching Configuration Guide.

**2\.**Configure PE 1:

\# Configure an LSR ID.

\<PE1\> system-view

\[PE1] interface loopback 0

\[PE1-LoopBack0] ip address
1.1.1.9 32

\[PE1-LoopBack0] quit

\[PE1] mpls lsr-id 1.1.1.9

\# Enable L2VPN.

\[PE1] l2vpn enable

\# Enable global LDP.

\[PE1] mpls ldp

\[PE1-ldp] quit

\# Configure VLAN-interface 20 (the
interface connected to PE 2), and enable LDP on the interface.

\[PE1] interface vlan-interface
20

\[PE1-Vlan-interface20] ip
address 20.1.1.1 24

\[PE1-Vlan-interface20] mpls
enable

\[PE1-Vlan-interface20] mpls
ldp enable

\[PE1-Vlan-interface20] quit

\# Configure VLAN-interface 30 (the
interface connected to PE 3), and enable LDP on the interface.

\[PE1] interface vlan-interface
30

\[PE1-Vlan-interface30] ip
address 30.1.1.1 24

\[PE1-Vlan-interface30] mpls
enable

\[PE1-Vlan-interface30] mpls
ldp enable

\[PE1-Vlan-interface30] quit

\# Configure OSPF for LDP to create LSPs.

\[PE1] ospf

\[PE1-ospf-1] area 0

\[PE1-ospf-1-area-0.0.0.0] network
20.1.1.0 0.0.0.255

\[PE1-ospf-1-area-0.0.0.0] network
30.1.1.0 0.0.0.255

\[PE1-ospf-1-area-0.0.0.0] network
1.1.1.9 0.0.0.0

\[PE1-ospf-1-area-0.0.0.0] quit

\[PE1-ospf-1] quit

\# Create a VSI, specify the peer PEs, and
establish static PWs to the peer PEs.

\[PE1] vsi svc

\[PE1-vsi-svc] pwsignaling
static

\[PE1-vsi-svc-static] peer 2.2.2.9
pw-id 3 in-label 100 out-label 100

\[PE1-vsi-svc-static-2.2.2.9-3]
quit

\[PE1-vsi-svc-static] peer
3.3.3.9 pw-id 3 in-label 200 out-label 200

\[PE1-vsi-svc-static-3.3.3.9-3]
quit

\[PE1-vsi-svc-static] quit

\[PE1-vsi-svc] quit

\# Create Ethernet service instance 10 on Ten-GigabitEthernet 1/0/1 to match packets
with an outer VLAN ID of 100, and bind the Ethernet service instance to the VSI
svc.

\[PE1] interface ten-gigabitethernet 1/0/1

\[PE1-Ten-GigabitEthernet1/0/1] service-instance
10

\[PE1-Ten-GigabitEthernet1/0/1-srv10]
encapsulation s-vid 100

\[PE1-Ten-GigabitEthernet1/0/1-srv10] xconnect vsi
svc

**3\.**Configure PE 2:

\# Configure an LSR ID.

\<PE2\> system-view

\[PE2] interface loopback 0

\[PE2-LoopBack0] ip address
2.2.2.9 32

\[PE2-LoopBack0] quit

\[PE2] mpls lsr-id 2.2.2.9

\# Enable L2VPN.

\[PE2] l2vpn enable

\# Enable global LDP.

\[PE2] mpls ldp

\[PE2-ldp] quit

\# Configure VLAN-interface 20 (the
interface connected to PE 1), and enable LDP on the interface.

\[PE2] interface vlan-interface
20

\[PE2-Vlan-interface20] ip
address 20.1.1.2 24

\[PE2-Vlan-interface20] mpls enable

\[PE2-Vlan-interface20] mpls
ldp enable

\[PE2-Vlan-interface20] quit

\# Configure VLAN-interface 40 (the
interface connected to PE 3), and enable LDP on the interface.

\[PE2] interface vlan-interface
40

\[PE2-Vlan-interface40] ip
address 40.1.1.2 24

\[PE2-Vlan-interface40] mpls
enable

\[PE2-Vlan-interface40] mpls
ldp enable

\[PE2-Vlan-interface40] quit

\# Configure OSPF for LDP to create LSPs.

\[PE2] ospf

\[PE2-ospf-1] area 0

\[PE2-ospf-1-area-0.0.0.0] network
20.1.1.0 0.0.0.255

\[PE2-ospf-1-area-0.0.0.0] network
40.1.1.0 0.0.0.255

\[PE2-ospf-1-area-0.0.0.0] network
2.2.2.9 0.0.0.0

\[PE2-ospf-1-area-0.0.0.0] quit

\[PE2-ospf-1] quit

\# Create a VSI, specify the peer PEs, and
establish static PWs to the peer PEs.

\[PE2] vsi svc

\[PE2-vsi-svc] pwsignaling
static

\[PE2-vsi-svc-static] peer 1.1.1.9
pw-id 3 in-label 100 out-label 100

\[PE2-vsi-svc-static-1.1.1.9-3]
quit

\[PE2-vsi-svc-static] peer 3.3.3.9
pw-id 3 in-label 300 out-label 300

\[PE2-vsi-svc-static-3.3.3.9-3]
quit

\[PE2-vsi-svc-static] quit

\[PE2-vsi-svc] quit

\# Create Ethernet service instance 10 on Ten-GigabitEthernet 1/0/1 to match packets
with an outer VLAN ID of 100, and bind the Ethernet service instance to the VSI
svc.

\[PE2] interface ten-gigabitethernet 1/0/1

\[PE2-Ten-GigabitEthernet1/0/1] service-instance
10

\[PE2-Ten-GigabitEthernet1/0/1-srv10]
encapsulation s-vid 100

\[PE2-Ten-GigabitEthernet1/0/1-srv10] xconnect vsi
svc

**4\.**Configure PE 3:

\# Configure an LSR ID.

\<PE3\> system-view

\[PE3] interface loopback 0

\[PE3-LoopBack0] ip address
3.3.3.9 32

\[PE3-LoopBack0] quit

\[PE3] mpls lsr-id 3.3.3.9

\# Enable L2VPN.

\[PE3] l2vpn enable

\# Enable global LDP.

\[PE3] mpls ldp

\[PE3-ldp] quit

\# Configure VLAN-interface 30 (the
interface connected to PE 1), and enable LDP on the interface.

\[PE3] interface vlan-interface
30

\[PE3-Vlan-interface30] ip
address 30.1.1.3 24

\[PE3-Vlan-interface30] mpls
enable

\[PE3-Vlan-interface30] mpls
ldp enable

\[PE3-Vlan-interface30] quit

\# Configure VLAN-interface 40 (the
interface connected to PE 2), and enable LDP on the interface.

\[PE3] interface vlan-interface
40

\[PE3-Vlan-interface40] ip
address 40.1.1.3 24

\[PE3-Vlan-interface40] mpls
enable

\[PE3-Vlan-interface40] mpls
ldp enable

\[PE3-Vlan-interface40] quit

\# Configure OSPF for LDP to create LSPs.

\[PE3] ospf

\[PE3-ospf-1] area 0

\[PE3-ospf-1-area-0.0.0.0] network
30.1.1.0 0.0.0.255

\[PE3-ospf-1-area-0.0.0.0] network
40.1.1.0 0.0.0.255

\[PE3-ospf-1-area-0.0.0.0] network
3.3.3.9 0.0.0.0

\[PE3-ospf-1-area-0.0.0.0] quit

\[PE3-ospf-1] quit

\# Create a VSI, specify the peer PEs, and
establish static PWs to the peer PEs.

\[PE3] vsi svc

\[PE3-vsi-svc] pwsignaling
static

\[PE3-vsi-svc-static] peer 1.1.1.9
pw-id 3 in-label 200 out-label 200

\[PE3-vsi-svc-static-1.1.1.9-3]
quit

\[PE3-vsi-svc-static] peer 2.2.2.9
pw-id 3 in-label 300 out-label 300

\[PE3-vsi-svc-static-2.2.2.9-3]
quit

\[PE3-vsi-svc-static] quit

\[PE3-vsi-svc] quit

\# Create Ethernet service instance 10 on Ten-GigabitEthernet 1/0/1 to match packets
with an outer VLAN ID of 100, and bind the Ethernet service instance to the VSI
svc.

\[PE3] interface ten-gigabitethernet 1/0/1

\[PE3-Ten-GigabitEthernet1/0/1] service-instance
10

\[PE3-Ten-GigabitEthernet1/0/1-srv10]
encapsulation s-vid 100

\[PE3-Ten-GigabitEthernet1/0/1-srv10] xconnect vsi
svc

#### Verifying the configuration

\# Verify that two static PWs in up state
have been established on PE 1\.

\[PE1] display l2vpn pw verbose

VSI Name: svc

  Peer: 2.2.2.9          PW ID: 3

    Signaling Protocol  : Static

    Link ID             : 8         
PW State : Up

    In Label            : 100        Out
Label: 100

    MTU                 : 1500

    PW Attributes       : Main

    VCCV CC             : \-

    VCCV BFD            : \-

    Flow Label          : \-

    Tunnel Group ID     : 0x160000001

    Tunnel NHLFE IDs    : 1027

    Admin PW            : \-

  Peer: 3.3.3.9          PW ID: 3

    Signaling Protocol  : Static

    Link ID             : 9         
PW State : Up

    In Label            : 200        Out
Label: 200

    MTU                 : 1500

    PW Attributes       : Main

    VCCV CC             : \-

    VCCV BFD            : \-

    Flow Label          : \-

    Tunnel Group ID     : 0x260000002

    Tunnel NHLFE IDs    : 1028

    Admin PW            : \-

### Example: Configuring LDP PWs

#### Network configuration

Configure a VSI on each PE, and establish
LDP PWs between the PEs to interconnect the CEs.

Configure an Ethernet service instance on Ten-GigabitEthernet 1/0/1 to match packets
with an outer VLAN ID of 100 on each PE. Bind the Ethernet service instance to
the VSI to forward the matching packets through the VSI.

Figure 6 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705161_x_Img_x_png_6_2216051_294551_0.png)

‌

#### Procedure

**1\.**Disable the spanning tree feature globally
or map each VLAN to an MSTI. (Details
not shown.)

For more information, see spanning tree
in Layer 2—LAN Switching Configuration Guide.

**2\.**Configure an IGP and public tunnels on each
PE. (Details not shown.)

**3\.**Configure PE 1:

\# Configure basic MPLS. 

\<PE1\> system-view

\[PE1] interface loopback 0

\[PE1-LoopBack0] ip address
1.1.1.9 32

\[PE1-LoopBack0] quit

\[PE1] mpls lsr-id 1.1.1.9

\[PE1] mpls ldp

\[PE1-ldp] quit

\# Enable L2VPN.

\[PE1] l2vpn enable

\# Configure VSI aaa that uses
LDP as the PW signaling protocol, and establish PWs to PE 2 and PE 3\.

\[PE1] vsi aaa

\[PE1-vsi-aaa] pwsignaling ldp

\[PE1-vsi-aaa-ldp] peer 2.2.2.9
pw-id 500

\[PE1-vsi-aaa-ldp-2.2.2.9-500]
quit

\[PE1-vsi-aaa-ldp] peer 3.3.3.9
pw-id 500

\[PE1-vsi-aaa-ldp-3.3.3.9-500]
quit

\[PE1-vsi-aaa-ldp] quit

\[PE1-vsi-aaa] quit

\# Create Ethernet service instance 10 on Ten-GigabitEthernet 1/0/1 to match packets
with an outer VLAN ID of 100, and bind the Ethernet service instance to the VSI
aaa.

\[PE1] interface ten-gigabitethernet 1/0/1

\[PE1-Ten-GigabitEthernet1/0/1] service-instance
10

\[PE1-Ten-GigabitEthernet1/0/1-srv10]
encapsulation s-vid 100

\[PE1-Ten-GigabitEthernet1/0/1-srv10] xconnect vsi
aaa

**4\.**Configure PE 2:

\# Configure basic MPLS. 

\<PE2\> system-view

\[PE2] interface loopback 0

\[PE2-LoopBack0] ip address 2.2.2.9
32

\[PE2-LoopBack0] quit

\[PE2] mpls lsr-id 2.2.2.9

\[PE2] mpls ldp

\[PE2-ldp] quit

\# Enable L2VPN.

\[PE2] l2vpn enable

\# Configure VSI aaa that uses
LDP as the PW signaling protocol, and establish PWs to PE 1 and PE 3\.

\[PE2] vsi aaa

\[PE2-vsi-aaa] pwsignaling ldp

\[PE2-vsi-aaa-ldp] peer 1.1.1.9
pw-id 500

\[PE2-vsi-aaa-ldp-1.1.1.9-500]
quit

\[PE2-vsi-aaa-ldp] peer 3.3.3.9
pw-id 500

\[PE2-vsi-aaa-ldp-3.3.3.9-500]
quit

\[PE2-vsi-aaa-ldp] quit

\[PE2-vsi-aaa] quit

\# Create Ethernet service instance 10 on Ten-GigabitEthernet 1/0/1 to match packets
with an outer VLAN ID of 100, and bind the Ethernet service instance to the VSI
aaa.

\[PE2] interface ten-gigabitethernet 1/0/1

\[PE2-Ten-GigabitEthernet1/0/1] service-instance
10

\[PE2-Ten-GigabitEthernet1/0/1-srv10]
encapsulation s-vid 100

\[PE2-Ten-GigabitEthernet1/0/1-srv10] xconnect vsi
aaa

**5\.**Configure PE 3:

\# Configure basic MPLS. 

\<PE3\> system-view

\[PE3] interface loopback 0

\[PE3-LoopBack0] ip address 3.3.3.9
32

\[PE3-LoopBack0] quit

\[PE3] mpls lsr-id 3.3.3.9

\[PE3] mpls ldp

\[PE3-ldp] quit

\# Enable L2VPN.

\[PE3] l2vpn enable

\# Configure VSI aaa that uses
LDP as the PW signaling protocol, and establish PWs to PE 1 and PE 2\.

\[PE3] vsi aaa

\[PE3-vsi-aaa] pwsignaling ldp

\[PE3-vsi-aaa-ldp] peer 1.1.1.9
pw-id 500

\[PE3-vsi-aaa-ldp-1.1.1.9-500]
quit

\[PE3-vsi-aaa-ldp] peer 2.2.2.9
pw-id 500

\[PE3-vsi-aaa-ldp-2.2.2.9-500]
quit

\[PE3-vsi-aaa-ldp] quit

\[PE3-vsi-aaa] quit

\# Create Ethernet service instance 10 on Ten-GigabitEthernet 1/0/1 to match packets
with an outer VLAN ID of 100, and bind the Ethernet service instance to the VSI
aaa.

\[PE3] interface ten-gigabitethernet 1/0/1

\[PE3-Ten-GigabitEthernet1/0/1] service-instance
10

\[PE3-Ten-GigabitEthernet1/0/1-srv10]
encapsulation s-vid 100

\[PE3-Ten-GigabitEthernet1/0/1-srv10] xconnect vsi
aaa

#### Verifying the configuration

\# Verify that two LDP PWs in up state have
been established on PE 1\.

\[PE1] display l2vpn pw verbose

VSI Name: aaa

  Peer: 2.2.2.9          PW ID: 500

    Signaling Protocol  : LDP

    Link ID             : 8         
PW State : Up

    In Label            : 1279      
Out Label: 1279

    MTU                 : 1500

    PW Attributes       : Main

    VCCV CC             : \-

    VCCV BFD            : \-

    Flow Label          : \-

    Tunnel Group ID     : 0x260000000

    Tunnel NHLFE IDs    : 1028

    Admin PW            : \-

  Peer: 3.3.3.9          PW ID: 500

    Signaling Protocol  : LDP

    Link ID             : 9         
PW State : Up

    In Label            : 1278      
Out Label: 1277

    MTU                 : 1500

    PW Attributes       : Main

    VCCV CC             : \-

    VCCV BFD            : \-

    Flow Label          : \-

    Tunnel Group ID     : 0x360000001

    Tunnel NHLFE IDs    : 1029

    Admin PW            : \-

### Example: Configuring BGP PWs

#### Network configuration

Configure a VSI on each PE, and establish
BGP PWs between the PEs to interconnect CEs.

Configure an Ethernet service instance on Ten-GigabitEthernet 1/0/1 to match packets
with an outer VLAN ID of 100 on each PE. Bind the Ethernet service instance to
the VSI to forward the matching packets through the VSI.

Figure 7 Network diagram

 ![](https://resource.h3c.com/en/202407/12/20240712_11705162_x_Img_x_png_7_2216051_294551_0.png)

‌

#### Procedure

**1\.**Disable the spanning tree feature globally
or map each VLAN to an MSTI. (Details not shown.)

For more information, see spanning tree
in Layer 2—LAN Switching Configuration Guide.

**2\.**Configure an IGP and public tunnels on each
PE. (Details not shown.)

**3\.**Configure PE 1:

\# Configure basic MPLS.

\<PE1\> system-view

\[PE1] interface loopback 0

\[PE1-LoopBack0] ip address
1.1.1.9 32

\[PE1-LoopBack0] quit

\[PE1] mpls lsr-id 1.1.1.9

\[PE1] mpls ldp

\[PE1-ldp] quit

\# Establish IBGP connections to PE 2 and
PE 3, and use BGP to advertise VPLS label block information.

\[PE1] bgp 100

\[PE1-bgp-default] peer 2.2.2.9
as-number 100

\[PE1-bgp-default] peer 2.2.2.9
connect-interface loopback 0

\[PE1-bgp-default] peer 3.3.3.9
as-number 100

\[PE1-bgp-default] peer 3.3.3.9
connect-interface loopback 0

\[PE1-bgp-default] address-family
l2vpn

\[PE1-bgp-default-l2vpn] peer
2.2.2.9 enable

\[PE1-bgp-default-l2vpn] peer
3.3.3.9 enable

\[PE1-bgp-default-l2vpn] quit

\[PE1-bgp-default] quit

\# Enable L2VPN.

\[PE1] l2vpn enable

\# Configure VSI aaa to use BGP
to establish BGP PWs to PE 2 and PE 3\.

\[PE1] vsi aaa

\[PE1-vsi-aaa] auto-discovery
bgp

\[PE1-vsi-aaa-auto] route-distinguisher
1:1

\[PE1-vsi-aaa-auto] vpn-target
1:1

\[PE1-vsi-aaa-auto] signaling-protocol
bgp

\[PE1-vsi-aaa-auto-bgp] site 1
range 10 default-offset 0

\[PE1-vsi-aaa-auto-bgp] quit

\[PE1-vsi-aaa-auto] quit

\[PE1-vsi-aaa] quit

\# Create Ethernet service instance 10 on Ten-GigabitEthernet 1/0/1 to match packets
with an outer VLAN ID of 100, and bind the Ethernet service instance to the VSI
aaa.

\[PE1] interface ten-gigabitethernet 1/0/1

\[PE1-Ten-GigabitEthernet1/0/1] service-instance
10

\[PE1-Ten-GigabitEthernet1/0/1-srv10]
encapsulation s-vid 100

\[PE1-Ten-GigabitEthernet1/0/1-srv10] xconnect vsi
aaa

**4\.**Configure PE 2:

\# Configure basic MPLS.

\<PE2\> system-view

\[PE2] interface loopback 0

\[PE2-LoopBack0] ip address 2.2.2.9
32

\[PE2-LoopBack0] quit

\[PE2] mpls lsr-id 2.2.2.9

\[PE2] mpls ldp

\[PE2-ldp] quit

\# Establish IBGP connections to PE 2 and
PE 3, and use BGP to advertise VPLS label block information.

\[PE2] bgp 100

\[PE2-bgp-default] peer 1.1.1.9
as-number 100

\[PE2-bgp-default] peer 1.1.1.9
connect-interface loopback 0

\[PE2-bgp-default] peer 3.3.3.9
as-number 100

\[PE2-bgp-default] peer 3.3.3.9
connect-interface loopback 0

\[PE2-bgp-default]
address-family l2vpn

\[PE2-bgp-default-l2vpn] peer
1.1.1.9 enable

\[PE2-bgp-default-l2vpn] peer
3.3.3.9 enable

\[PE2-bgp-default-l2vpn] quit

\[PE2-bgp-default] quit

\# Enable L2VPN.

\[PE2] l2vpn enable

\# Configure VSI aaa to use BGP
to establish BGP PWs to PE 1 and PE 3\.

\[PE2] vsi aaa

\[PE2-vsi-aaa] auto-discovery
bgp

\[PE2-vsi-aaa-auto] route-distinguisher
1:1

\[PE2-vsi-aaa-auto] vpn-target
1:1

\[PE2-vsi-aaa-auto] signaling-protocol
bgp

\[PE2-vsi-aaa-auto-bgp] site 2
range 10 default-offset 0

\[PE2-vsi-aaa-auto-bgp] quit

\[PE2-vsi-aaa-auto] quit

\[PE2-vsi-aaa] quit

\# Create Ethernet service instance 10 on Ten-GigabitEthernet 1/0/1 to match packets
with an outer VLAN ID of 100, and bind the Ethernet service instance to the VSI
aaa.

\[PE2] interface ten-gigabitethernet 1/0/1

\[PE2-Ten-GigabitEthernet1/0/1] service-instance
10

\[PE2-Ten-GigabitEthernet1/0/1-srv10]
encapsulation s-vid 100

\[PE2-Ten-GigabitEthernet1/0/1-srv10] xconnect vsi
aaa

**5\.**Configure PE 3:

\# Configure basic MPLS.

\<PE3\> system-view

\[PE3] interface loopback 0

\[PE3-LoopBack0] ip address 3.3.3.9
32

\[PE3-LoopBack0] quit

\[PE3] mpls lsr-id 3.3.3.9

\[PE3] mpls ldp

\[PE3-ldp] quit

\# Establish IBGP connections to PE 1 and
PE 2, and use BGP to advertise VPLS label block information.

\[PE3] bgp 100

\[PE3-bgp-default] peer 1.1.1.9
as-number 100

\[PE3-bgp-default] peer 1.1.1.9
connect-interface loopback 0

\[PE3-bgp-default] peer 2.2.2.9
as-number 100

\[PE3-bgp-default] peer 2.2.2.9
connect-interface loopback 0

\[PE3-bgp-default]
address-family l2vpn

\[PE3-bgp-default-l2vpn] peer
1.1.1.9 enable

\[PE3-bgp-default-l2vpn] peer
2.2.2.9 enable

\[PE3-bgp-default-l2vpn] quit

\[PE3-bgp-default] quit

\# Enable L2VPN.

\[PE3] l2vpn enable

\# Configure VSI aaa to use BGP
to establish BGP PWs to PE 1 and PE 2\.

\[PE3] vsi aaa

\[PE3-vsi-aaa] auto-discovery
bgp

\[PE3-vsi-aaa-auto] route-distinguisher
1:1

\[PE3-vsi-aaa-auto] vpn-target
1:1

\[PE3-vsi-aaa-auto] signaling-protocol
bgp

\[PE3-vsi-aaa-auto-bgp] site 3
range 10 default-offset 0

\[PE3-vsi-aaa-auto-bgp] quit

\[PE3-vsi-aaa-auto] quit

\[PE3-vsi-aaa] quit

\# Create Ethernet service instance 10 on Ten-GigabitEthernet 1/0/1 to match packets
with an outer VLAN ID of 100, and bind the Ethernet service instance to the VSI
aaa.

\[PE3] interface ten-gigabitethernet 1/0/1

\[PE3-Ten-GigabitEthernet1/0/1] service-instance
10

\[PE3-Ten-GigabitEthernet1/0/1-srv10]
encapsulation s-vid 100

\[PE3-Ten-GigabitEthernet1/0/1-srv10] xconnect vsi
aaa

#### Verifying the configuration

\# Verify that two BGP PWs have been
established on PE 1\.

\[PE1] display l2vpn pw verbose

VSI Name: aaa

  Peer: 2.2.2.9          Remote Site:
2

    Signaling Protocol  : BGP

    Link ID             : 9         
PW State : Up

    In Label            : 1295      
Out Label: 1025

    MTU                 : 1500

    PW Attributes       : Main

    VCCV CC             : \-

    VCCV BFD            : \-

    Flow Label          : \-

    Tunnel Group ID     :
0x800000160000001

    Tunnel NHLFE IDs    : 1027

    Admin PW            : \-

  Peer: 3.3.3.9          Remote Site:
3

    Signaling Protocol  : BGP

    Link ID             : 10        
PW State : Up

    In Label            : 1296      
Out Label: 1025

    MTU                 : 1500

    PW Attributes       : Main

    VCCV CC             : \-

    VCCV BFD            : \-

    Flow Label          : \-

    Tunnel Group ID     :
0x800000060000000

    Tunnel NHLFE IDs    : 1026

    Admin PW            : \-

\# Display VPLS label block information
received from PE 2 and PE 3 on PE 1\. 

\[PE1] display l2vpn bgp verbose

VSI Name: aaa

 Remote Site ID     : 2

 Offset             : 0

 RD                 : 1:1

 PW State           : Up

 Encapsulation      : BGP-VPLS

 MTU                : 1500

 Nexthop            : 2.2.2.9

 Local VC Label     : 1295

 Remote VC Label    : 1025

 Link ID            : 9

 Local Label Block  : 1293/10/0

 Remote Label Block : 1024/10/0

 Local Flow Label   : \-

 Remote Flow Label  : \-

 Export Route Target: 1:1

 

 Remote Site ID     : 3

 Offset             : 0

 RD                 : 1:1

 PW State           : Up

 Encapsulation      : BGP-VPLS

 MTU                : 1500

 Nexthop            : 3.3.3.9

 Local VC Label     : 1296

 Remote VC Label    : 1025

 Link ID            : 10

 Local Label Block  : 1293/10/0

 Remote Label Block : 1024/10/0

 Local Flow Label   : \-

 Remote Flow Label  : \-

 Export Route Target: 1:1

### Example: Configuring BGP auto-discovery LDP PWs

#### Network configuration

Configure a VSI on each PE. Use BGP to
discover remote PEs and use LDP to create PWs among PEs so CEs in different
sites of VPN 1 can communicate with each other.

Configure an Ethernet service instance on Ten-GigabitEthernet 1/0/1 to match packets
with an outer VLAN ID of 100 on each PE. Bind the Ethernet service instance to the
VSI to forward the matching packets through the VSI.

Figure 8 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705163_x_Img_x_png_8_2216051_294551_0.png)

‌

#### Procedure

**1\.**Disable the spanning tree feature globally
or map each VLAN to an MSTI. (Details not shown.)

For more information, see spanning tree
in Layer 2—LAN Switching Configuration Guide.

**2\.**Configure an IGP and public tunnels on each
PE. (Details not shown.)

**3\.**Configure PE 1: 

\# Configure basic MPLS.

\<PE1\> system-view

\[PE1] interface loopback 0

\[PE1-LoopBack0] ip address
1.1.1.9 32

\[PE1-LoopBack0] quit

\[PE1] mpls lsr-id 1.1.1.9

\[PE1] mpls ldp

\[PE1-ldp] quit

\# Establish IBGP connections to PE 1 and
PE 2, and use BGP to advertise VPLS PE information.

\[PE1] bgp 100

\[PE1-bgp-default] peer 2.2.2.9
as-number 100

\[PE1-bgp-default] peer 2.2.2.9
connect-interface loopback 0

\[PE1-bgp-default] peer 3.3.3.9
as-number 100

\[PE1-bgp-default] peer 3.3.3.9
connect-interface loopback 0

\[PE1-bgp-default] address-family
l2vpn

\[PE1-bgp-default-l2vpn] peer
2.2.2.9 enable

\[PE1-bgp-default-l2vpn] peer
3.3.3.9 enable

\[PE1-bgp-default-l2vpn] quit

\[PE1-bgp-default] quit

\# Enable L2VPN.

\[PE1] l2vpn enable

\# Configure VSI aaa to use BGP
to discover remote PEs and use LDP to establish LDP PWs to PE 2 and PE 3\.

\[PE1] vsi aaa

\[PE1-vsi-aaa] auto-discovery
bgp

\[PE1-vsi-aaa-auto] route-distinguisher
1:1

\[PE1-vsi-aaa-auto] vpn-target
1:1

\[PE1-vsi-aaa-auto] signaling-protocol
ldp

\[PE1-vsi-aaa-auto-ldp] vpls-id
100:100

\[PE1-vsi-aaa-auto-ldp] quit

\[PE1-vsi-aaa-auto] quit

\[PE1-vsi-aaa] quit

\# Create Ethernet service instance 10 on Ten-GigabitEthernet 1/0/1 to match packets
with an outer VLAN ID of 100, and bind the Ethernet service instance to the VSI
aaa.

\[PE1] interface ten-gigabitethernet 1/0/1

\[PE1-Ten-GigabitEthernet1/0/1] service-instance
10

\[PE1-Ten-GigabitEthernet1/0/1-srv10]
encapsulation s-vid 100

\[PE1-Ten-GigabitEthernet1/0/1-srv10] xconnect vsi
aaa

**4\.**Configure PE 2:

\# Configure basic MPLS.

\<PE2\> system-view

\[PE2] interface loopback 0

\[PE2-LoopBack0] ip address 2.2.2.9
32

\[PE2-LoopBack0] quit

\[PE2] mpls lsr-id 2.2.2.9

\[PE2] mpls ldp

\[PE2-ldp] quit

\# Establish IBGP connections to PE 1 and
PE 3, and use BGP to advertise VPLS PE information.

\[PE2] bgp 100

\[PE2-bgp-default] peer 1.1.1.9
as-number 100

\[PE2-bgp-default] peer 1.1.1.9
connect-interface loopback 0

\[PE2-bgp-default] peer 3.3.3.9
as-number 100

\[PE2-bgp-default] peer 3.3.3.9
connect-interface loopback 0

\[PE2-bgp-default]
address-family l2vpn

\[PE2-bgp-default-l2vpn] peer
1.1.1.9 enable

\[PE2-bgp-default-l2vpn] peer
3.3.3.9 enable

\[PE2-bgp-default-l2vpn] quit

\[PE2-bgp-default] quit

\# Enable L2VPN.

\[PE2] l2vpn enable

\# Configure VSI aaa to use BGP
to discover remote PEs and use LDP to establish LDP PWs to PE 1 and PE 3\.

\[PE2] vsi aaa

\[PE2-vsi-aaa] auto-discovery
bgp

\[PE2-vsi-aaa-auto] route-distinguisher
1:1

\[PE2-vsi-aaa-auto] vpn-target
1:1

\[PE2-vsi-aaa-auto] signaling-protocol
ldp

\[PE2-vsi-aaa-auto-ldp] vpls-id
100:100

\[PE2-vsi-aaa-auto-ldp] quit

\[PE2-vsi-aaa-auto] quit

\[PE2-vsi-aaa] quit

\# Create Ethernet service instance 10 on Ten-GigabitEthernet 1/0/1 to match packets
with an outer VLAN ID of 100, and bind the Ethernet service instance to the VSI
aaa.

\[PE2] interface ten-gigabitethernet 1/0/1

\[PE2-Ten-GigabitEthernet1/0/1] service-instance
10

\[PE2-Ten-GigabitEthernet1/0/1-srv10]
encapsulation s-vid 100

\[PE2-Ten-GigabitEthernet1/0/1-srv10] xconnect vsi
aaa

**5\.**Configure PE 3:

\# Configure basic MPLS.

\<PE3\> system-view

\[PE3] interface loopback 0

\[PE3-LoopBack0] ip address 3.3.3.9
32

\[PE3-LoopBack0] quit

\[PE3] mpls lsr-id 3.3.3.9

\[PE3] mpls ldp

\[PE3-ldp] quit

\# Establish IBGP connections to PE 1 and
PE 2, and use BGP to advertise VPLS PE information.

\[PE3] bgp 100

\[PE3-bgp-default] peer 1.1.1.9
as-number 100

\[PE3-bgp-default] peer 1.1.1.9
connect-interface loopback 0

\[PE3-bgp-default] peer 2.2.2.9
as-number 100

\[PE3-bgp-default] peer 2.2.2.9
connect-interface loopback 0

\[PE3-bgp-default]
address-family l2vpn

\[PE3-bgp-default-l2vpn] peer
1.1.1.9 enable

\[PE3-bgp-default-l2vpn] peer
2.2.2.9 enable

\[PE3-bgp-default-l2vpn] quit

\[PE3-bgp-default] quit

\# Enable L2VPN.

\[PE3] l2vpn enable

\# Configure VSI aaa to use BGP
to discover remote PEs and use LDP to establish LDP PWs to PE 1 and PE 2\.

\[PE3] vsi aaa

\[PE3-vsi-aaa] auto-discovery
bgp

\[PE3-vsi-aaa-auto] route-distinguisher
1:1

\[PE3-vsi-aaa-auto] vpn-target
1:1

\[PE3-vsi-aaa-auto] signaling-protocol
ldp

\[PE3-vsi-aaa-auto-ldp] vpls-id
100:100

\[PE3-vsi-aaa-auto-ldp] quit

\[PE3-vsi-aaa-auto] quit

\[PE3-vsi-aaa] quit

\# Create Ethernet service instance 10 on Ten-GigabitEthernet 1/0/1 to match packets
with an outer VLAN ID of 100, and bind the Ethernet service instance to the VSI
aaa.

\[PE3] interface ten-gigabitethernet 1/0/1

\[PE3-Ten-GigabitEthernet1/0/1] service-instance
10

\[PE3-Ten-GigabitEthernet1/0/1-srv10]
encapsulation s-vid 100

\[PE3-Ten-GigabitEthernet1/0/1-srv10] xconnect vsi
aaa

#### Verifying the configuration

\# Verify that two PWs have been established
on PE 1\.

\[PE1] display l2vpn pw verbose

VSI Name: aaa

  Peer: 2.2.2.9          VPLS ID:
100:100

    Signaling Protocol  : LDP

    Link ID             : 8         
PW State : Up

    In Label            : 1555      
Out Label: 1555

    MTU                 : 1500

    PW Attributes       : Main

    VCCV CC             : \-

    VCCV BFD            : \-

    Flow Label          : \-

    Tunnel Group ID     :
0x800000060000000

    Tunnel NHLFE IDs    : 1029

    Admin PW            : \-

  Peer: 3.3.3.9          VPLS ID:
100:100

    Signaling Protocol  : LDP

    Link ID             : 9         
PW State : Up

    In Label            : 1554      
Out Label: 1416

    MTU                 : 1500

    PW Attributes       : Main

    VCCV CC             : \-

    VCCV BFD            : \-

    Flow Label          : \-

    Tunnel Group ID     :
0x800000160000001

    Tunnel NHLFE IDs    : 1030

    Admin PW            : \-

\# Display LDP PW label information on PE 1\.

\[PE1] display l2vpn ldp verbose

Peer: 2.2.2.9          VPLS ID:
100:100

  VSI Name: aaa

  PW State: Up

  PW Status Communication:
Notification method

  PW ID FEC (Local/Remote):

    Local AII   : (1.1.1.9, 2.2.2.9)

    Remote AII  : (2.2.2.9, 1.1.1.9)

    PW Type     : VLAN/VLAN

    Group ID    : 0/0

    Label       : 1555/1555

    Control Word: Disabled/Disabled

    VCCV CV Type: -/-

    VCCV CC Type: -/-

    MTU         : 1500/1500

    PW Status   : PW forwarding/PW
forwarding

 

Peer: 3.3.3.9          VPLS ID:
100:100

  VSI Name: aaa

  PW State: Up

  PW Status Communication:
Notification method

  PW ID FEC (Local/Remote):

    Local AII   : (1.1.1.9, 3.3.3.9)

    Remote AII  : (3.3.3.9, 1.1.1.9)

    PW Type     : VLAN/VLAN

    Group ID    : 0/0

    Label       : 1554/1416

    Control Word: Disabled/Disabled

    VCCV CV Type: -/-

    VCCV CC Type: -/-

    MTU         : 1500/1500

    PW Status   : PW forwarding/PW
forwarding

### Example: Configuring H-VPLS using MPLS access

#### Network configuration

Configure an H-VPLS network using MPLS
access to avoid full-mesh PW configuration. The H-VPLS uses LDP as the PW
signaling protocol.

Figure 9 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705164_x_Img_x_png_9_2216051_294551_0.png)

‌

#### Procedure

**1\.**Disable the spanning tree feature globally
or map each VLAN to an MSTI. (Details not shown.)

For more information, see spanning tree
in Layer 2—LAN Switching Configuration Guide.

**2\.**Configure an IGP and public tunnels on each
PE. (Details not shown.)

**3\.**Configure UPE:

\# Configure basic MPLS. 

\<UPE\> system-view

\[UPE] interface loopback 0

\[UPE-LoopBack0] ip address
1.1.1.9 32

\[UPE-LoopBack0] quit

\[UPE] mpls lsr-id 1.1.1.9

\[UPE] mpls ldp

\[UPE-ldp] quit

\# Enable L2VPN.

\[UPE] l2vpn enable

\# Configure VSI aaa to use LDP
to establish a U-PW to NPE 1\.

\[UPE] vsi aaa

\[UPE-vsi-aaa] pwsignaling ldp

\[UPE-vsi-aaa-ldp] peer 2.2.2.9
pw-id 500

\[UPE-vsi-aaa-ldp-2.2.2.9-500]
quit

\[UPE-vsi-aaa-ldp] quit

\[UPE-vsi-aaa] quit

\# Create Ethernet service instance 10 on Ten-GigabitEthernet 1/0/1 to match packets
with an outer VLAN ID of 100, and bind the Ethernet service instance to the VSI
aaa.

\[UPE] interface ten-gigabitethernet 1/0/1

\[UPE-Ten-GigabitEthernet1/0/1] service-instance
10

\[UPE-Ten-GigabitEthernet1/0/1-srv10]
encapsulation s-vid 100

\[UPE-Ten-GigabitEthernet1/0/1-srv10] xconnect vsi
aaa

**4\.**Configure NPE 1:

\# Configure basic MPLS. 

\<NPE1\> system-view

\[NPE1] interface loopback 0

\[NPE1-LoopBack0] ip address
2.2.2.9 32

\[NPE1-LoopBack0] quit

\[NPE1] mpls lsr-id 2.2.2.9

\[NPE1] mpls ldp

\[NPE1–ldp] quit

\# Enable L2VPN.

\[NPE1] l2vpn enable

\# Configure VSI aaa that uses
LDP as the PW signaling protocol, establish a U-PW to the UPE, and establish
N-PWs to NPE 2 and NPE 3\. 

\[NPE1] vsi aaa

\[NPE1-vsi-aaa] pwsignaling ldp

\[NPE1-vsi-aaa-ldp] peer 1.1.1.9
pw-id 500 no-split-horizon

\[NPE1-vsi-aaa-ldp-1.1.1.9-500]
quit

\[NPE1-vsi-aaa-ldp] peer 3.3.3.9
pw-id 500

\[NPE1-vsi-aaa-ldp-3.3.3.9-500]
quit

\[NPE1-vsi-aaa-ldp] peer 4.4.4.9
pw-id 500

\[NPE1-vsi-aaa-ldp-4.4.4.9-500]
quit

\[NPE1-vsi-aaa-ldp] quit

\[NPE1-vsi-aaa] quit

**5\.**Configure NPE 2:

\# Configure basic MPLS. 

\<NPE2\> system-view

\[NPE2] interface loopback 0

\[NPE2-LoopBack0] ip address
3.3.3.9 32

\[NPE2-LoopBack0] quit

\[NPE2] mpls lsr-id 3.3.3.9

\[NPE2] mpls ldp

\[NPE2–ldp] quit

\# Enable L2VPN.

\[NPE2] l2vpn enable

\# Configure VSI aaa that uses
LDP as the PW signaling protocol, and establish N-PWs to NPE 1 and NPE 3\.

\[NPE2] vsi aaa

\[NPE2-vsi-aaa] pwsignal ldp

\[NPE2-vsi-aaa-ldp] peer 2.2.2.9
pw-id 500

\[NPE2-vsi-aaa-ldp-2.2.2.9-500]
quit

\[NPE2-vsi-aaa-ldp] peer 4.4.4.9
pw-id 500

\[NPE2-vsi-aaa-ldp-4.4.4.9-500]
quit

\[NPE2-vsi-aaa-ldp] quit

\[NPE2-vsi-aaa] quit

\# Create Ethernet service instance 10 on Ten-GigabitEthernet 1/0/1 to match packets
with an outer VLAN ID of 100, and bind the Ethernet service instance to the VSI
aaa.

\[NPE2] interface ten-gigabitethernet 1/0/1

\[NPE2-Ten-GigabitEthernet1/0/1] service-instance
10

\[NPE2-Ten-GigabitEthernet1/0/1-srv10]
encapsulation s-vid 100

\[NPE2-Ten-GigabitEthernet1/0/1-srv10] xconnect vsi
aaa

\[NPE2-Ten-GigabitEthernet1/0/1-srv10] quit

\[NPE2-Ten-GigabitEthernet1/0/1] quit

**6\.**Configure NPE 3:

\# Configure basic MPLS. 

\<NPE3\> system-view

\[NPE3] interface loopback 0

\[NPE3-LoopBack0] ip address 4.4.4.9
32

\[NPE3-LoopBack0] quit

\[NPE3] mpls lsr-id 4.4.4.9

\[NPE3] mpls ldp

\[NPE3–ldp] quit

\# Enable L2VPN.

\[NPE3] l2vpn enable

\# Configure VSI aaa that uses
LDP as the PW signaling protocol, and establish N-PWs to NPE 1 and NPE 2\.

\[NPE3] vsi aaa

\[NPE3-vsi-aaa] pwsignal ldp

\[NPE3-vsi-aaa-ldp] peer 2.2.2.9
pw-id 500

\[NPE3-vsi-aaa-ldp-2.2.2.9-500]
quit

\[NPE3-vsi-aaa-ldp] peer 3.3.3.9
pw-id 500

\[NPE3-vsi-aaa-ldp-3.3.3.9-500]
quit

\[NPE3-vsi-aaa-ldp] quit

\[NPE3-vsi-aaa] quit

\# Create Ethernet service instance 10 on Ten-GigabitEthernet 1/0/1 to match packets
with an outer VLAN ID of 100, and bind the Ethernet service instance to the VSI
aaa.

\[NPE3] interface ten-gigabitethernet 1/0/1

\[NPE3-Ten-GigabitEthernet1/0/1] service-instance
10

\[NPE3-Ten-GigabitEthernet1/0/1-srv10]
encapsulation s-vid 100

\[NPE3-Ten-GigabitEthernet1/0/1-srv10] xconnect vsi
aaa

\[NPE3-Ten-GigabitEthernet1/0/1-srv10] quit

\[NPE3-Ten-GigabitEthernet1/0/1] quit

#### Verifying the configuration

\# Verify that PWs in up state have been
established on each PE.

\[UPE] display l2vpn pw verbose

VSI Name: aaa

  Peer: 2.2.2.9          PW ID: 500

    Signaling Protocol  : LDP

    Link ID             : 8         
PW State : Up

    In Label            : 1277      
Out Label: 1277

    MTU                 : 1500

    PW Attributes       : Main

    VCCV CC             : \-

    VCCV BFD            : \-

    Flow Label          : \-

    Tunnel Group ID     : 0x460000000

    Tunnel NHLFE IDs    : 1030

    Admin PW            : \-

\[NPE1] display l2vpn pw verbose

VSI Name: aaa

  Peer: 1.1.1.9          PW ID: 500

    Signaling Protocol  : LDP

    Link ID             : 8         
PW State : Up

    In Label            : 1277      
Out Label: 1277

    MTU                 : 1500

    PW Attributes       : Main,
No-split-horizon

    VCCV CC             : \-

    VCCV BFD            : \-

    Flow Label          : \-

    Tunnel Group ID     : 0x460000000

    Tunnel NHLFE IDs    : 1030

    Admin PW            : \-

  Peer: 3.3.3.9          PW ID: 500

    Signaling Protocol  : LDP

    Link ID             : 9         
PW State : Up

    In Label            : 1276      
Out Label: 1275

    MTU                 : 1500

    PW Attributes       : Main

    VCCV CC             : \-

    VCCV BFD            : \-

    Flow Label          : \-

    Tunnel Group ID     : 0x560000001

    Tunnel NHLFE IDs    : 1031

    Admin PW            : \-

  Peer: 4.4.4.9          PW ID: 500

    Signaling Protocol  : LDP

    Link ID             : 10         
PW State : Up

    In Label            : 1278      
Out Label: 1279

    MTU                 : 1500

    PW Attributes       : Main

    VCCV CC             : \-

    VCCV BFD            : \-

    Flow Label          : \-

    Tunnel Group ID     : 0x570000001

    Tunnel NHLFE IDs    : 1032

    Admin PW            : \-

\[NPE2] display l2vpn pw verbose

VSI Name: aaa

  Peer: 2.2.2.9          PW ID: 500

    Signaling Protocol  : LDP

    Link ID             : 8         
PW State : Up

    In Label            : 1275      
Out Label: 1276

    MTU                 : 1500

    PW Attributes       : Main

    VCCV CC             : \-

    VCCV BFD            : \-

    Flow Label          : \-

    Tunnel Group ID     : 0x660000000

    Tunnel NHLFE IDs    : 1031

    Admin PW            : \-

  Peer: 4.4.4.9          PW ID: 500

    Signaling Protocol  : LDP

    Link ID             : 9         
PW State : Up

    In Label            : 1277      
Out Label: 1277

    MTU                 : 1500

    PW Attributes       : Main

    VCCV CC             : \-

    VCCV BFD            : \-

    Flow Label          : \-

    Tunnel Group ID     : 0x670000000

    Tunnel NHLFE IDs    : 1032

    Admin PW            : \-

\[NPE3] display l2vpn pw verbose

VSI Name: aaa

  Peer: 2.2.2.9          PW ID: 500

    Signaling Protocol  : LDP

    Link ID             : 8         
PW State : Up

    In Label            : 1279      
Out Label: 1278

    MTU                 : 1500

    PW Attributes       : Main

    VCCV CC             : \-

    VCCV BFD            : \-

    Flow Label          : \-

    Tunnel Group ID     : 0x660000000

    Tunnel NHLFE IDs    : 1031

    Admin PW            : \-

  Peer: 3.3.3.9          PW ID: 500

    Signaling Protocol  : LDP

    Link ID             : 9         
PW State : Up

    In Label            : 1277      
Out Label: 1277

    MTU                 : 1500

    PW Attributes       : Main

    VCCV CC             : \-

    VCCV BFD            : \-

    Flow Label          : \-

    Tunnel Group ID     : 0x670000000

    Tunnel NHLFE IDs    : 1032

    Admin PW            : \-

 

