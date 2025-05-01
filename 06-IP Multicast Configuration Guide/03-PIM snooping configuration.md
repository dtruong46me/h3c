
# Configuring PIM snooping

## About PIM snooping

PIM snooping runs on Layer 2 devices. It works
with IGMP snooping to analyze received PIM messages, and adds the ports that
are interested in specific multicast data to a PIM snooping routing entry. In
this way, the multicast data can be forwarded to only the ports that are
interested in the data.

Figure 1 Multicast packet transmission
without or with PIM snooping

![](https://resource.h3c.com/en/202407/12/20240712_11704948_x_Img_x_png_0_2216032_294551_0.png)

 

As shown in [Figure 1](#_Ref350859397),
Source 1 sends multicast data to multicast group G1, and Source 2 sends
multicast data to multicast group G2. Receiver 1 belongs to G1, and Receiver 2
belongs to G2. The Layer 2 switch's interfaces that connect to the PIM routers
are in the same VLAN.

·     When the Layer 2 switch runs only IGMP snooping,
it performs the following actions:

**a.**Maintains the router ports according to the
received PIM hello messages that PIM routers send.

**b.**Floods all other types of received PIM
messages except PIM hello messages in the VLAN.

**c.**Forwards all multicast data to all router
ports in the VLAN. 

Each PIM router in the VLAN, whether
interested in the multicast data or not, can receive all multicast data and all
PIM messages except PIM hello messages.

·     When the Layer 2 switch runs both IGMP snooping
and PIM snooping, it performs the following actions:

**a.**Examines whether a PIM router is interested
in the multicast data addressed to a multicast group according to the received PIM
messages that the router sends.

**b.**Adds only the ports that connect to the
router and are interested in the data to a PIM snooping routing entry. 

**c.**Forwards PIM messages and multicast data to
only the routers that are interested in the data, which saves network
bandwidth.

For more information about IGMP snooping
and the router port, see "Configuring IGMP snooping."

## Restrictions and guidelines: PIM snooping configuration

PIM snooping does not take effect on
secondary VLANs. As a best practice, do not configure PIM snooping for
secondary VLANs. For more information about secondary VLANs, see Layer 2—LAN Switching Configuration Guide.

Make sure the maximum size of a PIM join or
prune message is smaller than the path MTU of devices connected to PIM snooping
devices. Otherwise, fragmented PIM join or prune messages prevent PIM snooping
from correctly forwarding multicast data. For more information about setting
the maximum size of a join or prune message, see "Configuring PIM."

PIM snooping is supported only for PIM-SM
and PIM-SSM. As a best practice, do not configure PIM snooping for PIM-DM and
BIDIR-PIM. For more information about PIM, see "PIM overview."

After you configure PIM snooping for a
VLAN, PIM snooping takes effect only on ports that belong to the VLAN.

## PIM snooping tasks at a glance

To configure PIM snooping, perform the
following tasks:

**1\.**[Enabling PIM snooping](#_Ref474344649)

**2\.**(Optional.) [Setting the aging time for global ports
after a master/subordinate switchover](#_Ref487534585)

¡     [Setting the aging time for global neighbor
ports](#_Ref490390434)

¡     [Setting the aging time for global
downstream ports and global router ports](#_Ref487535017)

## Enabling PIM snooping

#### Restrictions and guidelines

You must enable the IGMP snooping feature and
enable IGMP snooping for a VLAN before you enable PIM snooping for the VLAN.

PIM snooping does not take effect on
sub-VLANs of a multicast VLAN.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enable the IGMP snooping feature and enter
IGMP-snooping view.

igmp-snooping

By default, IGMP snooping is disabled.

For more information about this command,
see IGMP snooping commands in IP Multicast Command
Reference.

**3\.**Return to system view.

quit

**4\.**Enter VLAN view.

vlan vlan-id

**5\.**Enable IGMP snooping for the VLAN.

igmp-snooping enable

By default, IGMP snooping is disabled in
a VLAN.

For more information about this command,
see IP Multicast Command Reference.

**6\.**Enable PIM snooping for the VLAN.

pim-snooping enable

By default, PIM snooping is disabled in a
VLAN.

## Setting the aging time for global ports aftera master/subordinate switchover

### About global ports

A global port is a virtual port on the master
device, such as a Layer 2 aggregate interface. A global port that acts as a
neighbor port, downstream port, or router port is called a global neighbor
port, global downstream port, and global router port, respectively.

Perform this task to decrease Layer 2
multicast data interruption caused by the aging of PIM snooping entries after a
master/subordinate switchover.

### Restrictions and guidelines

For a global neighbor port, the set aging
time does not take effect when the port receives a PIM hello message after a
master/subordinate switchover. The aging time for the port is determined by the
aging time in the PIM hello message.

For a global router port or global downstream
port, the set aging time does not take effect when the port receives a PIM join
message after a master/subordinate switchover. The aging time for the port is
determined by the aging time in the PIM join message.

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

## Display and maintenance commands for PIM snooping

Executedisplay commands in any view and reset
commands in user view. 

 

| Task | Command |
| --- | --- |
| Display PIM snooping neighbor information. | display pim-snooping neighbor \[ vlan vlan-id ] \[ slot slot-number ] \[ verbose ] || Display PIM snooping router port information. | display pim-snooping router-port \[ vlan vlan-id ] \[ slot slot-number ] \[ verbose ] || Display PIM snooping routing entries. | display pim-snooping routing-table \[ vlan vlan-id ] \[ slot slot-number ] \[ verbose ] || Display statistics for the PIM messages learned through PIM snooping. | display pim-snooping statistics || Clear statistics for the PIM messages learned through PIM snooping. | reset pim-snooping statistics |





 

## PIM snooping configuration examples

### Example: Configuring PIM snooping

#### Network configuration

As shown in [Figure 2](#_Ref153341191):

·     OSPF runs on the network.

·     Source 1 and Source 2 send multicast data to
multicast groups 224.1.1.1 and 225.1.1.2, respectively. 

·     Receiver 1 and Receiver 2 belong to multicast
groups 224.1.1.1 and 225.1.1.2,
respectively. 

·     Router C and Router D run IGMP on Ten-GigabitEthernet 1/0/1. Router A, Router
B, Router C, and Router D run PIM-SM. 

·     Ten-GigabitEthernet 1/0/2 on Router A acts as a C-BSR and a C-RP.

Configure IGMP snooping and PIM snooping on
Switch A. Then, Switch A forwards PIM protocol packets and multicast data
packets only to the routers that are connected to receivers.

Figure 2 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11704949_x_Img_x_png_1_2216032_294551_0.png)

‌

#### Prerequisites

Assign an IP address and subnet mask to
each interface as shown in [Figure 2](#_Ref153341191), and configure
OSPF on the routers.

#### Procedure

**1\.**Configure Router A:

\# Enable IP multicast routing.

\<RouterA\> system-view

\[RouterA] multicast routing

\[RouterA-mrib] quit

\# Enable PIM-SM on each interface.

\[RouterA] interface ten-gigabitethernet 1/0/1

\[RouterA-Ten-GigabitEthernet1/0/1] pim sm

\[RouterA-Ten-GigabitEthernet1/0/1] quit

\[RouterA] interface ten-gigabitethernet 1/0/2

\[RouterA-Ten-GigabitEthernet1/0/2] pim sm

\[RouterA-Ten-GigabitEthernet1/0/2] quit

\# Configure Ten-GigabitEthernet 1/0/2 as a C-BSR and a C-RP.

\[RouterA] pim

\[RouterA-pim] c-bsr 10.1.1.1

\[RouterA-pim] c-rp 10.1.1.1

\[RouterA-pim] quit

**2\.**Configure Router B:

\# Enable IP multicast routing.

\<RouterB\> system-view

\[RouterB] multicast routing

\[RouterB-mrib] quit

\# Enable PIM-SM on each interface.

\[RouterB] interface ten-gigabitethernet 1/0/1

\[RouterB-Ten-GigabitEthernet1/0/1] pim sm

\[RouterB-Ten-GigabitEthernet1/0/1] quit

\[RouterB] interface ten-gigabitethernet 1/0/2

\[RouterB-Ten-GigabitEthernet1/0/2] pim sm

\[RouterB-Ten-GigabitEthernet1/0/2] quit

**3\.**Configure Router C:

\# Enable IP multicast routing.

\<RouterC\> system-view

\[RouterC] multicast routing

\[RouterC-mrib] quit

\# Enable IGMP on Ten-GigabitEthernet 1/0/1.

\[RouterC] interface ten-gigabitethernet 1/0/1

\[RouterC-Ten-GigabitEthernet1/0/1] igmp enable

\[RouterC-Ten-GigabitEthernet1/0/1] quit

\# Enable PIM-SM on Ten-GigabitEthernet 1/0/2.

\[RouterC] interface ten-gigabitethernet 1/0/2

\[RouterC-Ten-GigabitEthernet1/0/2] pim sm

\[RouterC-Ten-GigabitEthernet1/0/2] quit

**4\.**Configure Router D:

\# Enable IP multicast routing.

\<RouterD\> system-view

\[RouterD] multicast routing

\[RouterD-mrib] quit

\# Enable IGMP on Ten-GigabitEthernet 1/0/1.

\[RouterD] interface ten-gigabitethernet 1/0/1

\[RouterD-Ten-GigabitEthernet1/0/1] igmp enable

\[RouterD-Ten-GigabitEthernet1/0/1] quit

\# Enable PIM-SM on Ten-GigabitEthernet 1/0/2.

\[RouterD] interface ten-gigabitethernet 1/0/2

\[RouterD-Ten-GigabitEthernet1/0/2] pim sm

\[RouterD-Ten-GigabitEthernet1/0/2] quit

**5\.**Configure Switch A:

\# Enable the IGMP snooping feature.

\<SwitchA\> system-view

\[SwitchA] igmp-snooping

\[SwitchA-igmp-snooping] quit

\# Create VLAN 100, and assign Ten-GigabitEthernet 1/0/1 through Ten-GigabitEthernet 1/0/4 to the VLAN.

\[SwitchA] vlan 100

\[SwitchA-vlan100] port ten-gigabitethernet 1/0/1 to ten-gigabitethernet 1/0/4

\# Enable IGMP snooping and PIM snooping
for VLAN 100\.

\[SwitchA-vlan100] igmp-snooping
enable

\[SwitchA-vlan100] pim-snooping
enable

\[SwitchA-vlan100] quit

#### Verifying the configuration

\# On Switch A, display PIM snooping
neighbor information for VLAN 100\.

\[SwitchA] display pim-snooping neighbor
vlan 100

Total 4 neighbors.

 

VLAN 100: Total 4 neighbors.

  10.1.1.1

    Ports (1 in total):

      XGE1/0/1                 (00:32:43)

  10.1.1.2

    Ports (1 in total):

      XGE1/0/2                 (00:32:43)

  10.1.1.3

    Ports (1 in total):

      XGE1/0/3                 (00:32:43)

  10.1.1.4

    Ports (1 in total):

      XGE1/0/4                 (00:32:43)

The output shows that Router A, Router B,
Router C, and Router D are PIM snooping neighbors.

\# On Switch A, display PIM snooping routing
entries for VLAN 100\.

\[SwitchA] display pim-snooping routing-table
vlan 100

Total 2 entries.

FSM Flag: NI-no info, J-join, PP-prune
pending

 

VLAN 100: Total 2 entries.

  (\*, 224.1.1.1)

    Upstream neighbor: 10.1.1.1

      Upstream Ports (1 in total):

        XGE1/0/1

      Downstream Ports (1 in total):

        XGE1/0/3

          Expires: 00:03:01, FSM: J

  (\*, 225.1.1.2)

    Upstream neighbor: 10.1.1.2

      Upstream Ports (1 in total):

        XGE1/0/2

      Downstream Ports (1 in total):

        XGE1/0/4

          Expires: 00:03:11, FSM: J

The output shows the
following information:

·     Switch A will forward the multicast data
intended for multicast group 224.1.1.1 only to Router C.

·     Switch A will forward the multicast data
intended for multicast group 225.1.1.2 only to Router D.

## Troubleshooting PIM snooping

### PIM snooping does not work on a Layer 2 device

#### Symptom

PIM snooping does not work on a Layer 2
device.

#### Solution

To resolve the problem:

**1\.**Use the display current-configuration command to display information about IGMP snooping and PIM
snooping.

**2\.**If IGMP snooping is not enabled, enable the IGMP
snooping feature, and then enable IGMP snooping and PIM snooping for the VLAN.

**3\.**If PIM snooping is not enabled, enable PIM
snooping for the VLAN.

**4\.**If the problem persists, contact H3C
Support.

