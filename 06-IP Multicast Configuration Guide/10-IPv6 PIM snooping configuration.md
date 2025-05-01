
# Configuring IPv6 PIM snooping

## About IPv6 PIM snooping

IPv6 PIM snooping runs on Layer 2 devices. It
works with MLD snooping to analyze received IPv6 PIM messages, and adds the
ports that are interested in specific multicast data to an IPv6 PIM snooping
routing entry. In this way, the multicast data can be forwarded to only the ports
that are interested in the data.

Figure 1 Multicast packet transmission without or with IPv6 PIM snooping

![](https://resource.h3c.com/en/202407/12/20240712_11705020_x_Img_x_png_0_2216039_294551_0.png)

 

As shown in[Figure 1](#_Ref114842101),
Source 1 sends multicast data to multicast group G1, and Source 2 sends
multicast data to multicast group G2. Receiver 1 belongs to G1, and Receiver 2
belongs to G2. The Layer 2 switch's interfaces that connect to the IPv6
PIM-capable routers are in the same VLAN.

·     When the Layer 2 switch only runs MLD snooping, it
performs the following actions:

**a.**Maintains the router ports according to the
received IPv6 PIM hello messages that IPv6 PIM-capable routers send.

**b.**Floods all other types of received IPv6 PIM
messages except PIM hello messages in the VLAN.

**c.**Forwards all multicast data to all router
ports in the VLAN. 

Each IPv6 PIM-capable router in the VLAN,
whether interested in the multicast data or not, can receive all multicast data
and all IPv6 PIM messages except IPv6 PIM hello messages.

·     When the Layer 2 switch runs both MLD snooping
and IPv6 PIM snooping, it performs the following actions:

**a.**Examines whether an IPv6 PIM router is interested
in the multicast data destined for a multicast group according to the received
IPv6 PIM messages that the router sends. 

**b.**Adds only the ports that connect to the
router and are interested in the data to an IPv6 PIM snooping routing entry.  

**c.**Forwards IPv6 PIM messages and the multicast
data only to the router according to the multicast forwarding entry, which
saves network bandwidth.

For more information about MLD snooping and
the router port, see "Configuring MLD snooping."

## Restrictions and guidelines: IPv6 PIM snooping configuration

As a best practice, do not configure IPv6 PIM
snooping for secondary VLANs because IPv6 PIM snooping does not take effect on
secondary VLANs. For more information about secondary VLANs, see Layer 2—LAN Switching Configuration Guide.

Make sure the maximum size of an IPv6 PIM
join or prune message is smaller than the path MTU of devices connected to IPv6
PIM snooping devices. Otherwise, fragmented IPv6 PIM join or prune messages
prevent IPv6 PIM snooping from correctly forwarding IPv6 multicast data. For
more information about setting the maximum size of a join or prune message, see
"Configuring IPv6 PIM."

After you enable IPv6 PIM snooping for a
VLAN, IPv6 PIM snooping takes effect only on ports that belong to the VLAN.

## IPv6 PIM snooping tasks at a glance

To configure IPv6 PIM snooping, perform the
following tasks:

**1\.**[Enabling IPv6 PIM snooping](#_Ref474344649)

**2\.**(Optional.) [Setting the aging time for global ports
after a master/subordinate switchover](#_Ref487531360)

¡     [Setting the aging time for global
neighbor ports](#_Ref487531680)

¡     [Setting the aging time for global
downstream ports and global router ports](#_Ref487531802)

## Enabling IPv6 PIM snooping

**1\.**Enter system view.

system-view

**2\.**Enable the MLD snooping feature and enter
MLD -snooping view.

mld-snooping

By default, MLD snooping is disabled.

For more information about this command,
see IP Multicast Command Reference.

**3\.**Return to system view.

quit

**4\.**Enter VLAN view.

vlan vlan-id

**5\.**Enable MLD snooping for the VLAN.

mld-snooping enable

By default, MLD snooping is disabled in a
VLAN.

For more information about this command,
see IP Multicast Command Reference.

**6\.**Enable IPv6 PIM snooping for the VLAN.

ipv6 pim-snooping enable

By default, IPv6 PIM snooping is disabled
in a VLAN.

## Setting the aging time for global ports after a master/subordinate switchover

### About global ports

A global port is a virtual port on the master
device, such as a Layer 2 aggregate interface. A global port that acts as a
neighbor port, downstream port, or router port is called a global neighbor
port, global downstream port, and global router port, respectively.

Perform this task to decrease Layer 2 IPv6 multicast
data interruption caused by the aging of IPv6 PIM snooping entries after a
master/subordinate switchover.

### Restrictions and guidelines

For a global neighbor port, the set aging
time does not take effect when the port receives an IPv6 PIM hello message after
a master/subordinate switchover. The aging time for the port is determined by
the aging time in the IPv6 PIM hello message.

For a global router port or global downstream
port, the set aging time does not take effect when the port receives an IPv6
PIM join message after a master/subordinate switchover. The aging time for the
port is determined by the aging time in the IPv6 PIM join message.

### Setting the aging time for global neighbor ports

**1\.**Enter system view.

system-view

**2\.**Enter VLAN view.

vlan vlan-id

**3\.**Set the aging time for global neighbor ports
after a master/subordinate switchover.

pim-snooping graceful-restart neighbor-aging-time seconds

By default, the aging time for global
neighbor ports after a master/subordinate switchover is 105 seconds.

### Setting the aging time for global downstream ports and global router ports

**1\.**Enter system view.

system-view

**2\.**Enter VLAN view.

vlan vlan-id

**3\.**Set the aging time for global downstream
ports and global router ports after a master/subordinate switchover.

pim-snooping graceful-restart join-aging-time seconds

By default, the aging time for downstream
ports and global router ports after a master/subordinate switchover is 210
seconds.

## Display and maintenance commands for IPv6 PIM snooping

Executedisplay commands in any view and reset
commands in user view.

 

| Task | Command |
| --- | --- |
| Display IPv6 PIM snooping neighbor information. | display ipv6 pim-snooping neighbor \[ vlan vlan-id ] \[ slot slot-number ] \[ verbose ] || Display IPv6 PIM snooping router port information. | display ipv6 pim-snooping router-port \[ vlan vlan-id ] \[ slot slot-number ] \[ verbose ] || Display IPv6 PIM snooping routing entries. | display ipv6 pim-snooping routing-table \[ vlan vlan-id ] \[ slot slot-number ] \[ verbose ] || Display statistics for the IPv6 PIM messages learned through IPv6 PIM snooping. | display ipv6 pim-snooping statistics || Clear statistics for the IPv6 PIM messages learned through IPv6 PIM snooping. | reset ipv6 pim-snooping statistics |





 

## IPv6 PIM snooping configuration examples

### Example: Configuring IPv6 PIM snooping

#### Network configuration

As shown in [Figure 2](#_Ref153341191):

·     OSPFv3 runs on the network.

·     Source 1 and Source 2 send IPv6 multicast data
to IPv6 multicast groups FF1E::101 and FF2E::101, respectively.

·     Receiver 1 and Receiver 2 belong to IPv6
multicast groups FF1E::101 and FF2E::101, respectively.

·     Router C and Router D run MLD on Ten-GigabitEthernet 1/0/1. 

·     Router A, Router B, Router C, and Router D run
IPv6 PIM-SM. Ten-GigabitEthernet 1/0/2 on Router A acts
as a C-BSR and a C-RP.

Configure MLD snooping and IPv6 PIM
snooping on Switch A. Then, Switch A forwards IPv6 PIM protocol packets and
IPv6 multicast data packets only to routers that are connected to receivers.

Figure 2 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705021_x_Img_x_png_1_2216039_294551_0.png) 

‌

#### Prerequisites

Assign an IPv6 address and prefix length to
each interface as shown in [Figure 2](#_Ref153341191), and
then configure OSPFv3 on the routers.

#### Procedure

**1\.**Configure Router A:

\# Enable IPv6 multicast routing.

\<RouterA\> system-view

\[RouterA] ipv6 multicast routing

\[RouterA-mrib6] quit

\# Enable IPv6 PIM-SM on each interface.

\[RouterA] interface ten-gigabitethernet 1/0/1

\[RouterA-Ten-GigabitEthernet1/0/1] ipv6 pim sm

\[RouterA-Ten-GigabitEthernet1/0/1] quit

\[RouterA] interface ten-gigabitethernet 1/0/2

\[RouterA-Ten-GigabitEthernet1/0/2] ipv6 pim sm

\[RouterA-Ten-GigabitEthernet1/0/2] quit

\# Configure Ten-GigabitEthernet 1/0/2 as a C-BSR and a
C-RP.

\[RouterA] ipv6 pim

\[RouterA-pim6] c-bsr 1001::1

\[RouterA-pim6] c-rp 1001::1

\[RouterA-pim6] quit

**2\.**Configure Router B:

\# Enable IPv6 multicast routing.

\<RouterB\> system-view

\[RouterB] ipv6 multicast routing

\[RouterB-mrib6] quit

\# Enable IPv6 PIM-SM on each interface.

\[RouterB] interface ten-gigabitethernet 1/0/1

\[RouterB-Ten-GigabitEthernet1/0/1] ipv6 pim sm

\[RouterB-Ten-GigabitEthernet1/0/1] quit

\[RouterB] interface ten-gigabitethernet 1/0/2

\[RouterB-Ten-GigabitEthernet1/0/2] ipv6 pim sm

\[RouterB-Ten-GigabitEthernet1/0/2] quit

**3\.**Configure Router C:

\# Enable IPv6 multicast routing.

\<RouterC\> system-view

\[RouterC] ipv6 multicast routing

\[RouterC-mrib6] quit

\# Enable MLD on Ten-GigabitEthernet 1/0/1.

\[RouterC] interface ten-gigabitethernet 1/0/1

\[RouterC-Ten-GigabitEthernet1/0/1] mld enable

\[RouterC-Ten-GigabitEthernet1/0/1] quit

\# Enable IPv6 PIM-SM on Ten-GigabitEthernet 1/0/2.

\[RouterC] interface ten-gigabitethernet 1/0/2

\[RouterC-Ten-GigabitEthernet1/0/2] ipv6 pim sm

\[RouterC-Ten-GigabitEthernet1/0/2] quit

**4\.**Configure Router D:

\# Enable IPv6 multicast routing.

\<RouterD\> system-view

\[RouterD] ipv6 multicast routing

\[RouterD-mrib6] quit

\# Enable MLD on Ten-GigabitEthernet 1/0/1.

\[RouterD] interface ten-gigabitethernet 1/0/1

\[RouterD-Ten-GigabitEthernet1/0/1] mld enable

\[RouterD-Ten-GigabitEthernet1/0/1] quit

\# Enable IPv6 PIM-SM on Ten-GigabitEthernet 1/0/2.

\[RouterD] interface ten-gigabitethernet 1/0/2

\[RouterD-Ten-GigabitEthernet1/0/2] ipv6 pim sm

\[RouterD-Ten-GigabitEthernet1/0/2] quit

**5\.**Configure Switch A:

\# Enable the MLD snooping feature.

\<SwitchA\> system-view

\[SwitchA] mld-snooping

\[SwitchA-mld-snooping] quit

\# Create VLAN 100, and assign Ten-GigabitEthernet 1/0/1 through Ten-GigabitEthernet 1/0/4 to the VLAN.

\[SwitchA] vlan 100

\[SwitchA-vlan100] port ten-gigabitethernet 1/0/1 to ten-gigabitethernet 1/0/4

\# Enable MLD snooping and IPv6 PIM
snooping for VLAN 100\.

\[SwitchA-vlan100] mld-snooping
enable

\[SwitchA-vlan100] ipv6 pim-snooping
enable

\[SwitchA-vlan100] quit

#### Verifying the configuration

\# On Switch A, display IPv6 PIM snooping
neighbor information for VLAN 100\.

\[SwitchA] display ipv6 pim-snooping
neighbor vlan 100

Total 4 neighbors.

 

VLAN 100: Total 4 neighbors.

  FE80::1

    Ports (1 in total):

      XGE1/0/1                  (00:32:43)

  FE80::2

   Ports (1 in total):

      XGE1/0/2                  (00:32:43)

  FE80::3

    Ports (1 in total):

      XGE1/0/3                  (00:32:43)

  FE80::4

    Ports (1 in total):

      XGE1/0/4                  (00:32:43)

The output shows that Router A, Router B,
Router C, and Router D are IPv6 PIM snooping neighbors.

\# On Switch A, display IPv6 PIM snooping
routing entries for VLAN 100\.

\[SwitchA] display ipv6 pim-snooping
routing-table vlan 100

Total 2 entries.

FSM flag: NI-no info, J-join,
PP-prune pending

 

VLAN 100: Total 2 entries.

  (\*, FF1E::101)

    Upstream neighbor: FE80::1

      Upstream ports (1 in total):

        XGE1/0/1

      Downstream ports (1 in total):

        XGE1/0/3

          Expires: 00:03:01, FSM: J

  (\*, FF2E::101)

    Upstream neighbor: FE80::2

      Upstream ports (1 in total):

        XGE1/0/2

      Downstream ports (1 in total):

        XGE1/0/4

          Expires: 00:03:01, FSM: J

The output shows the following information:

·     Switch A will forward the multicast data
intended for IPv6 multicast group FF1E::101 to only Router C.

·     Switch A will forward the multicast data
intended for IPv6 multicast group FF2E::101 to only Router D.

## Troubleshooting IPv6 PIM snooping

This section
describes common IPv6 PIM snooping problems and how to troubleshoot them.

### IPv6 PIM snooping does not work on a Layer 2 device

#### Symptom

IPv6 PIM snooping does not work on a Layer
2 device.

#### Solution

To resolve the problem:

**1\.**Use the display current-configuration command to display information about MLD snooping and IPv6 PIM
snooping.

**2\.**If MLD snooping is not enabled, enable the MLD
snooping feature, and then enable MLD snooping and IPv6 PIM snooping for the
VLAN.

**3\.**If IPv6 PIM snooping is not enabled, enable
IPv6 PIM snooping for the VLAN.

**4\.**If the problem persists, contact H3C Support.

