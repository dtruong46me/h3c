
# Configuring multicast VLANs

## Multicast VLAN feature

As shown in [Figure 1](#_Ref356198394), Host
A, Host B, and Host C are in three different VLANs and the same multicast
group. When Switch A (Layer 3 device) receives multicast data for that group,
it sends three copies of the multicast data to Switch B (Layer 2 device). This
occupies a large amount of bandwidth and increases the burden on the Layer 3
device.

Figure 1 Multicast transmission without the multicast VLAN feature

![](https://resource.h3c.com/en/202407/12/20240712_11704951_x_Img_x_png_0_2216033_294551_0.png)

 

After a multicast VLAN is configured on Switch
B, Switch A sends only one copy of the multicast data to the multicast VLAN on Switch
B. This method saves network bandwidth and lessens the burden on the Layer 3
device.

## Multicast VLAN implementations

Multicast VLANs include sub-VLAN-based
multicast VLANs and port-based multicast VLANs.

### Sub-VLAN-based multicast VLAN

As shown in [Figure 2](#_Ref206476555):

·     Host A, Host B, and Host C are in VLAN 2 through
VLAN 4, respectively. 

·     On Switch B, VLAN 10 is a multicast VLAN. VLAN 2
through VLAN 4 are sub-VLANs of VLAN 10\.

·     IGMP snooping is enabled for the multicast VLAN
and its sub-VLANs.

Figure 2 Sub-VLAN-based multicast VLAN

![](https://resource.h3c.com/en/202407/12/20240712_11704952_x_Img_x_png_1_2216033_294551_0.png)

 

IGMP snooping manages router ports in the
multicast VLAN and member ports in each sub-VLAN. When Switch A receives
multicast data from the multicast source, it sends only one copy of the
multicast data to the multicast VLAN on Switch B. Then, Switch B sends a separate
copy to each sub-VLAN in the multicast VLAN.

### Port-based multicast VLAN

As shown in [Figure 3](#_Ref206476575):

·     Host A, Host B, and Host C are in VLAN 2 through
VLAN 4, respectively.

·     On Switch B, VLAN 10 is a multicast VLAN. All
the user ports are hybrid ports and are assigned to VLAN 10\.

·     IGMP snooping is enabled for the multicast VLAN and
VLAN 2 through VLAN 4\.

Figure 3 Port-based multicast VLAN

![](https://resource.h3c.com/en/202407/12/20240712_11704953_x_Img_x_png_2_2216033_294551_0.png)

 

IGMP snooping manages the router ports and member
ports in the multicast VLAN. When Switch A receives multicast data from the
multicast source, it sends only one copy of the multicast data to the multicast
VLAN on Switch B. Switch B sends a separate copy to each user port in the
multicast VLAN.

## Restrictions and guidelines: Multicast VLAN configuration

The VLAN to be configured as a multicast
VLAN must exist.

If you have configured both a sub-VLAN-based
multicast VLAN and a port-based multicast VLAN on a device, the port-based
multicast VLAN configuration takes effect.

Do not configure a multicast VLAN on a
device that is enabled with IP multicast routing. Otherwise, the system
displays errors. Do not enable IP multicast routing on a device that is configured
with multicast VLANs. Otherwise, multicast forwarding exceptions occur.

Do not configure a VLAN as a multicast VLAN
or a multicast sub-VLAN if the VLAN interface is enabled with PIM or IGMP.
Otherwise, the system displays errors. Do not enable PIM or IGMP on a VLAN
interface if the VLAN interface belongs to a multicast VLAN or a multicast
sub-VLAN. Otherwise, multicast forwarding exceptions occur.

The multicast VLAN feature does not take
effect on secondary VLANs. As a best practice, do not configure the multicast
VLAN feature for secondary VLANs. For more information about secondary VLANs,
see Layer 2—LAN Switching Configuration
Guide.

## Configuring a sub-VLAN-based multicast VLAN

#### Restrictions and guidelines

The VLANs to be configured as sub-VLANs of a
multicast VLAN must exist and cannot be multicast VLANs or sub-VLANs of any
other multicast VLAN.

#### Prerequisites

Before you configure a sub-VLAN-based
multicast VLAN, you must complete the following tasks:

·     Create VLANs as required.

·     Enable IGMP snooping for the VLAN to be
configured as the multicast VLAN and for the VLANs to be configured as
sub-VLANs.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Configure a VLAN as a multicast VLAN and
enter multicast VLAN view.

multicast-vlan vlan-id

By default, a VLAN is not a multicast
VLAN.

**3\.**Assign VLANs to the multicast VLAN as
sub-VLANs.

subvlan vlan-list

## Configuring a port-based multicast VLAN

#### Restrictions and guidelines

You can assign user ports to a multicast
VLAN in multicast VLAN view or assign a user port to a multicast VLAN in
interface view. These configurations have the same priority.

A user port can belong to only one
multicast VLAN.

#### Prerequisites

Before you configure a port-based multicast
VLAN, you must complete the following tasks:

·     Create VLANs as required.

·     Enable IGMP snooping for the VLAN to be
configured as the multicast VLAN.

·     Enable IGMP snooping for all the VLANs that
contain multicast receivers.

·     Configure the attributes of user ports. Make
sure the ports can forward packets from the VLAN to be configured as the
multicast VLAN and send the packets with the VLAN tag removed. For more
information about configuring port attributes, see VLAN configuration in Layer 2—LAN Switching Configuration Guide.

#### Assigning user ports to a multicast VLAN in multicast VLAN view

**1\.**Enter system view.

system-view

**2\.**Configure a VLAN as a multicast VLAN and
enter multicast VLAN view.

multicast-vlan vlan-id

By default, a VLAN is not a multicast
VLAN.

**3\.**Assign ports to the multicast VLAN.

port interface-list

#### Assigning user ports to a multicast VLAN in interface view

**1\.**Enter system view.

system-view

**2\.**Configure a VLAN as a multicast VLAN and
enter multicast VLAN view.

multicast-vlan vlan-id

By default, a VLAN is not a multicast
VLAN.

**3\.**Return to system view.

quit

**4\.**Enter Layer 2 interface view.

¡     Enter
Layer 2 Ethernet interface view.

interface interface-type
interface-number

¡     Enter Layer 2 aggregate interface view.

interface bridge-aggregation interface-number

**5\.**Assign the port to the multicast VLAN.

port multicast-vlan vlan-id

By default, a port does not belong to any
multicast VLAN.

## Setting the maximum number of multicast VLAN forwarding entries

#### About this task

You can set the maximum number of multicast
VLAN forwarding entries on the device. When the upper limit is reached, the
device does not create multicast VLAN forwarding entries until some entries age
out or are manually removed.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Set the maximum number of multicast VLAN
forwarding entries.

multicast-vlan entry-limit limit

By default, the maximum number of
multicast VLAN forwarding entries is 4000\.

## Display and maintenance commands for multicast VLAN

Executedisplay commands in any view and reset
commands in user view.

 

| Task | Command |
| --- | --- |
| Display information about multicast VLANs. | display multicast-vlan \[ vlan-id ] || Display information about multicast VLAN forwarding entries. | display multicast-vlan forwarding-table \[ group-address \[ mask { mask-length \| mask } ] \| source-address \[ mask { mask-length \| mask } ] \| slot slot-number \| subvlan vlan-id \| vlan vlan-id ] \* || Display information about multicast groups in multicast VLANs. | display multicast-vlan group \[ source-address \| group-address \| slot slot-number \| verbose \| vlan vlan-id ] \* || Clear multicast groups in multicast VLANs. | reset multicast-vlan group \[ source-address \[ mask { mask-length \| mask } ] \| group-address \[ mask { mask-length \| mask } ] \| vlan vlan-id ] \* |




 

## Multicast VLAN configuration examples

### Example: Configuring sub-VLAN-based multicast VLAN

#### Network configuration

As shown in [Figure 4](#_Ref156128396):

·     Layer 3 device Switch A runs IGMPv2 and acts as
the IGMP querier. Layer 2 device Switch B runs IGMPv2 snooping.

·     The multicast source sends multicast data to
multicast group 224.1.1.1. Receivers Host A, Host B, and Host C belong to VLAN
2, VLAN 3, and VLAN 4, respectively.

Configure a sub-VLAN-based multicast VLAN
on Switch B to meet the following requirements:

·     Switch A sends the multicast data to Switch B
through the multicast VLAN.

·     Switch B forwards the multicast data to the
receivers in different user VLANs.

Figure 4 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11704954_x_Img_x_png_3_2216033_294551_0.png)

‌

#### Procedure

**1\.**Configure Switch A:

\# Enable IP multicast routing.

\<SwitchA\> system-view

\[SwitchA] multicast routing

\[SwitchA-mrib] quit

\# Create VLAN 20, and assign Ten-GigabitEthernet 1/0/2 to the VLAN.

\[SwitchA] vlan 20

\[SwitchA-vlan20] port ten-gigabitethernet 1/0/2

\[SwitchA-vlan20] quit

\# Assign an IP address to VLAN-interface
20, and enable PIM-DM on the interface.

\[SwitchA] interface
vlan-interface 20

\[SwitchA-Vlan-interface20] ip
address 1.1.1.2 24

\[SwitchA-Vlan-interface20] pim
dm

\[SwitchA-Vlan-interface20]
quit

\# Create VLAN 10\.

\[SwitchA] vlan 10

\[SwitchA-vlan10] quit

\# Configure Ten-GigabitEthernet 1/0/1 as a hybrid port,
and assign the port to VLAN 10 as a tagged VLAN member.

\[SwitchA] interface ten-gigabitethernet 1/0/1

\[SwitchA-Ten-GigabitEthernet1/0/1] port link-type
hybrid

\[SwitchA-Ten-GigabitEthernet1/0/1] port hybrid vlan 10
tagged

\[SwitchA-Ten-GigabitEthernet1/0/1] quit

\# Assign an IP address to VLAN-interface
10, and enable IGMP on the interface.

\[SwitchA] interface
vlan-interface 10

\[SwitchA-Vlan-interface10] ip
address 10.110.1.1 24

\[SwitchA-Vlan-interface10]
igmp enable

\[SwitchA-Vlan-interface10]
quit

**2\.**Configure Switch B:

\# Enable the IGMP snooping feature.

\<SwitchB\> system-view

\[SwitchB] igmp-snooping

\[SwitchB-igmp-snooping] quit

\# Create VLAN 2, assign Ten-GigabitEthernet 1/0/2 to the VLAN, and enable
IGMP snooping for the VLAN.

\[SwitchB] vlan 2

\[SwitchB-vlan2] port ten-gigabitethernet 1/0/2

\[SwitchB-vlan2] igmp-snooping
enable

\[SwitchB-vlan2] quit

\# Create VLAN 3, assign Ten-GigabitEthernet 1/0/3 to the VLAN, and enable
IGMP snooping in the VLAN.

\[SwitchB] vlan 3

\[SwitchB-vlan3] port ten-gigabitethernet 1/0/3

\[SwitchB-vlan3] igmp-snooping
enable

\[SwitchB-vlan3] quit

\# Create VLAN 4, assign Ten-GigabitEthernet 1/0/4 to the VLAN, and enable
IGMP snooping in the VLAN.

\[SwitchB] vlan 4

\[SwitchB-vlan4] port ten-gigabitethernet 1/0/4

\[SwitchB-vlan4] igmp-snooping enable

\[SwitchB-vlan4] quit

\# Create VLAN 10, and enable IGMP snooping
for the VLAN.

\[SwitchB] vlan 10

\[SwitchB-vlan10] igmp-snooping
enable

\[SwitchB-vlan10] quit

\# Configure Ten-GigabitEthernet 1/0/1 as a hybrid port, and
assign the port to VLAN 10 as a tagged VLAN member.

\[SwitchB] interface ten-gigabitethernet 1/0/1

\[SwitchB-Ten-GigabitEthernet1/0/1] port link-type
hybrid

\[SwitchB-Ten-GigabitEthernet1/0/1] port hybrid vlan 10
tagged

\[SwitchB-Ten-GigabitEthernet1/0/1] quit

\# Configure VLAN 10 as a multicast VLAN,
and assign VLAN 2 through VLAN 4 as sub-VLANs to multicast VLAN 10\.

\[SwitchB] multicast-vlan 10

\[SwitchB-mvlan-10] subvlan 2
to 4

\[SwitchB-mvlan-10] quit

#### Verifying the configuration

\# Display information about all multicast
VLANs on Switch B.

\[SwitchB] display multicast-vlan

Total 1 multicast VLANs.

 

Multicast VLAN 10:

  Sub-VLAN list(3 in total):

     2-4

  Port list(0 in total):

\# Display information about multicast
groups in multicast VLANs on Switch B.

\[SwitchB] display multicast-vlan
group

Total 1 entries.

 

Multicast VLAN 10: Total 1 entries.

  (0.0.0.0, 224.1.1.1)

    Sub-VLANs (3 in total):

      VLAN 2

      VLAN 3

      VLAN 4

The output shows that multicast group
224.1.1.1 belongs to multicast VLAN 10\. Multicast VLAN 10 contains sub-VLANs
VLAN 2 through VLAN 4\. Switch B will replicate the multicast data of VLAN 10 to
VLAN 2 through VLAN 4\.

### Example: Configuring port-based multicast VLAN

#### Network configuration

As shown in [Figure 5](#_Ref206476934):

·     Layer 3 device Switch A runs IGMPv2 and acts as
the IGMP querier. Layer 2 device Switch B runs IGMPv2 snooping. 

·     The multicast source sends multicast data to
multicast group 224.1.1.1. Receivers Host A, Host B, and Host C belong to VLAN
2, VLAN 3, and VLAN 4, respectively.

Configure a port-based multicast VLAN on
Switch B to meet the following requirements:

·     Switch A sends multicast data to Switch B
through the multicast VLAN.

·     Switch B forwards the multicast data to the
receivers in different user VLANs.

Figure 5 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11704955_x_Img_x_png_4_2216033_294551_0.png)

‌

#### Procedure

**1\.**Configure Switch A:

\# Enable IP multicast routing.

\<SwitchA\> system-view

\[SwitchA] multicast routing

\[SwitchA-mrib] quit

\# Create VLAN 20, and assign Ten-GigabitEthernet 1/0/2 to the VLAN.

\[SwitchA] vlan 20

\[SwitchA-vlan20] port ten-gigabitethernet 1/0/2

\[SwitchA-vlan20] quit

\# Assign an IP address to VLAN-interface
20, and enable PIM-DM on the interface.

\[SwitchA] interface
vlan-interface 20

\[SwitchA-Vlan-interface20] ip
address 1.1.1.2 24

\[SwitchA-Vlan-interface20] pim
dm

\[SwitchA-Vlan-interface20]
quit

\# Create VLAN 10, and assign Ten-GigabitEthernet 1/0/1 to the VLAN.

\[SwitchA] vlan 10

\[SwitchA-vlan10] port ten-gigabitethernet 1/0/1

\[SwitchA-vlan10] quit

\# Assign an IP address to VLAN-interface
10, and enable IGMP on the interface.

\[SwitchA] interface
vlan-interface 10

\[SwitchA-Vlan-interface10] ip
address 10.110.1.1 24

\[SwitchA-Vlan-interface10]
igmp enable

\[SwitchA-Vlan-interface10]
quit

**2\.**Configure Switch B:

\# Enable the IGMP snooping feature.

\<SwitchB\> system-view

\[SwitchB] igmp-snooping

\[SwitchB-igmp-snooping] quit

\# Create VLAN 10, assign Ten-GigabitEthernet 1/0/1 to the VLAN, and
enable IGMP snooping for the VLAN.

\[SwitchB] vlan 10

\[SwitchB-vlan10] port ten-gigabitethernet 1/0/1

\[SwitchB-vlan10] igmp-snooping
enable

\[SwitchB-vlan10] quit

\# Create VLAN 2, and enable IGMP snooping
for the VLAN.

\[SwitchB] vlan 2

\[SwitchB-vlan2] igmp-snooping
enable

\[SwitchB-vlan2] quit

\# Create VLAN 3, and enable IGMP snooping
for the VLAN.

\[SwitchB] vlan 3

\[SwitchB-vlan3] igmp-snooping
enable

\[SwitchB-vlan3] quit

\# Create VLAN 4, and enable IGMP snooping
for the VLAN.

\[SwitchB] vlan 4

\[SwitchB-vlan4] igmp-snooping
enable

\[SwitchB-vlan4] quit

\# Configure Ten-GigabitEthernet 1/0/2 as a hybrid port,
and configure VLAN 2 as the PVID of the hybrid port.

\[SwitchB] interface ten-gigabitethernet 1/0/2

\[SwitchB-Ten-GigabitEthernet1/0/2] port link-type
hybrid

\[SwitchB-Ten-GigabitEthernet1/0/2] port hybrid pvid
vlan 2

\# Assign Ten-GigabitEthernet 1/0/2 to VLAN 2 and VLAN
10 as an untagged VLAN member.

\[SwitchB-Ten-GigabitEthernet1/0/2] port hybrid vlan 2
untagged

\[SwitchB-Ten-GigabitEthernet1/0/2] port hybrid vlan 10
untagged

\[SwitchB-Ten-GigabitEthernet1/0/2] quit

\# Configure Ten-GigabitEthernet 1/0/3 as a hybrid port,
and configure VLAN 3 as the PVID of the hybrid port.

\[SwitchB] interface ten-gigabitethernet 1/0/3

\[SwitchB-Ten-GigabitEthernet1/0/3] port link-type
hybrid

\[SwitchB-Ten-GigabitEthernet1/0/3] port hybrid pvid
vlan 3

\# Assign Ten-GigabitEthernet 1/0/3 to VLAN 3 and VLAN
10 as an untagged VLAN member.

\[SwitchB-Ten-GigabitEthernet1/0/3] port hybrid vlan 3
untagged

\[SwitchB-Ten-GigabitEthernet1/0/3] port hybrid vlan 10
untagged

\[SwitchB-Ten-GigabitEthernet1/0/3] quit

\# Configure Ten-GigabitEthernet 1/0/4 as a hybrid port,
and configure VLAN 4 as the PVID of the hybrid port.

\[SwitchB] interface ten-gigabitethernet 1/0/4

\[SwitchB-Ten-GigabitEthernet1/0/4] port link-type
hybrid

\[SwitchB-Ten-GigabitEthernet1/0/4] port hybrid pvid
vlan 4

\# Assign Ten-GigabitEthernet 1/0/4 to VLAN 4 and VLAN
10 as an untagged VLAN member.

\[SwitchB-Ten-GigabitEthernet1/0/4] port hybrid vlan 4
untagged

\[SwitchB-Ten-GigabitEthernet1/0/4] port hybrid vlan 10
untagged

\[SwitchB-Ten-GigabitEthernet1/0/4] quit

\# Configure VLAN 10 as a multicast VLAN.

\[SwitchB] multicast-vlan 10

\# Assign Ten-GigabitEthernet 1/0/2 and Ten-GigabitEthernet 1/0/3 to VLAN 10\.

\[SwitchB-mvlan-10] port ten-gigabitethernet 1/0/2 to ten-gigabitethernet 1/0/3

\[SwitchB-mvlan-10] quit

\# Assign Ten-GigabitEthernet 1/0/4 to VLAN 10\.

\[SwitchB] interface ten-gigabitethernet 1/0/4

\[SwitchB-Ten-GigabitEthernet1/0/4] port multicast-vlan
10

\[SwitchB-Ten-GigabitEthernet1/0/4] quit

#### Verifying the configuration

\# Display information about multicast VLANs
on Switch B.

\[SwitchB] display multicast-vlan

Total 1 multicast VLANs.

 

Multicast VLAN 10:

  Sub-VLAN list(0 in total):

  Port list(3 in total):

    XGE1/0/2

    XGE1/0/3

    XGE1/0/4

\# Display dynamic IGMP snooping forwarding
entries on Switch B.

\[SwitchB] display igmp-snooping group

Total 1 entries.

 

VLAN 10: Total 1 entries.

  (0.0.0.0, 224.1.1.1)

    Host ports (3 in total):

      XGE1/0/2        (00:03:23)

      XGE1/0/3        (00:04:07)

      XGE1/0/4        (00:04:16)

The output shows that IGMP snooping
maintains the user ports in the multicast VLAN (VLAN 10). Switch B will forward
the multicast data of VLAN 10 through these user ports.

