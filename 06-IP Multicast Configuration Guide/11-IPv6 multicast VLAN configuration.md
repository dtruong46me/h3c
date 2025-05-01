
# Configuring IPv6 multicast VLANs

## IPv6 multicast VLAN feature

As shown in [Figure 1](#_Ref356198394), Host
A, Host B, and Host C are in different VLANs and the same IPv6 multicast group.
When Switch A (Layer 3 device) receives IPv6 multicast data for that group, it forwards
three copies of the data to Switch B (Layer 2 device). This occupies a large
amount of bandwidth and increases the burden on the Layer 3 device.

Figure 1 Multicast transmission without the IPv6 multicast VLAN feature

![](https://resource.h3c.com/en/202407/12/20240712_11705023_x_Img_x_png_0_2216040_294551_0.png)

 

After an IPv6 multicast VLAN is configured
on Switch B, Switch A sends one copy of the IPv6 multicast data to the IPv6
multicast VLAN on Switch B. This saves network bandwidth and lessens the burden
on the Layer 3 device.

## IPv6 multicast VLAN methods

IPv6 multicast VLANs support sub-VLAN-based
IPv6 multicast VLANs and port-based IPv6 multicast VLANs.

### Sub-VLAN-based IPv6 multicast VLAN

As shown in [Figure 2](#_Ref206476555):

·     Host A, Host B, and Host C are in VLAN 2 through
VLAN 4, respectively. 

·     On Switch B, VLAN 10 is an IPv6 multicast VLAN. VLAN
2 through VLAN 4 are sub-VLANs of VLAN 10\.

·     MLD snooping is enabled for the multicast VLAN
and its sub-VLANs.

Figure 2 Sub-VLAN-based multicast VLAN

![](https://resource.h3c.com/en/202407/12/20240712_11705024_x_Img_x_png_1_2216040_294551_0.png)

 

MLD snooping manages router ports in the IPv6
multicast VLAN and member ports in each sub-VLAN. When Switch A receives IPv6
multicast data from the IPv6 multicast source, it sends only one copy of the
IPv6 multicast data to the IPv6 multicast VLAN on Switch B. Then, Switch B sends
a separate copy to each sub-VLAN of the IPv6 multicast VLAN.

### Port-based IPv6 multicast VLAN

As shown in [Figure 3](#_Ref206476575):

·     Host A, Host B, and Host C are in VLAN 2 through
VLAN 4, respectively. All the user ports (ports with attached hosts) on Switch
B are hybrid ports. 

·     On Switch B, VLAN 10 is an IPv6 multicast VLAN.
All the user ports are assigned to VLAN 10\.

·     MLD snooping is enabled for the IPv6 multicast
VLAN and its sub-VLANs.

Figure 3 Port-based IPv6 multicast VLAN

![](https://resource.h3c.com/en/202407/12/20240712_11705025_x_Img_x_png_2_2216040_294551_0.png)

 

MLD snooping manages the router ports and member
ports in the IPv6 multicast VLAN. When Switch A receives IPv6 multicast data
from the IPv6 multicast source, it sends only one copy of the IPv6 multicast
data to the IPv6 multicast VLAN on Switch B. Then, Switch B sends a separate
copy to each user port in the IPv6 multicast VLAN.

## Restrictions and guidelines: IPv6 multicast VLAN configuration

The VLAN to be configured as an IPv6
multicast VLAN must exist.

If you have configured both a sub-VLAN-based
IPv6 multicast VLAN and a port-based IPv6 multicast VLAN on a device, the
port-based IPv6 multicast VLAN configuration takes effect.

Do not configure an IPv6 multicast VLAN on
a device that is enabled with IPv6 multicast routing. Otherwise, the system
displays errors. Do not enable IPv6 multicast routing on a device that is
configured with IPv6 multicast VLANs. Otherwise, IPv6 multicast forwarding
exceptions occur.

Do not configure a VLAN as an IPv6
multicast VLAN or an IPv6 sub-VLAN if the VLAN interface is enabled with IPv6
PIM or MLD. Otherwise, the system displays errors. Do not enable IPv6 PIM or
MLD on a VLAN interface if the VLAN interface belongs to an IPv6 multicast VLAN
or an IPv6 multicast sub-VLAN. Otherwise, IPv6 multicast forwarding exceptions
occur.

The IPv6 multicast VLAN feature does not
take effect on secondary VLANs. As a best practice, do not configure the IPv6
multicast VLAN feature for secondary VLANs. For more information about
secondary VLAN, see Layer 2—LAN Switching Configuration
Guide.

## Configuring a sub-VLAN-based IPv6 multicast VLAN

#### Restrictions and guidelines

The VLANs to be configured as sub-VLANs of
an IPv6 multicast VLAN must exist and cannot be IPv6 multicast VLANs or
sub-VLANs of any other IPv6 multicast VLANs.

#### Prerequisites

Before you configure a sub-VLAN-based IPv6
multicast VLAN, you must complete the following tasks:

·     Create VLANs as required.

·     Enable MLD snooping for the VLAN to be
configured as the IPv6 multicast VLAN, and for the VLANs to be configured as
sub-VLANs.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Configure a VLAN as an IPv6 multicast VLAN
and enter IPv6 multicast VLAN view.

ipv6 multicast-vlan vlan-id

By default, a VLAN is not an IPv6
multicast VLAN.

**3\.**Assign VLANs to the IPv6 multicast VLAN as
sub-VLANs.

subvlan vlan-list

By default, an IPv6 multicast VLAN does
not have any sub-VLANs.

## Configuring a port-based IPv6 multicast VLAN

#### Restrictions and guidelines

You can assign user ports to an IPv6
multicast VLAN in IPv6 multicast VLAN view or assign a user port to an IPv6
multicast VLAN in interface view.

A user port can belong to only one IPv6
multicast VLAN.

#### Prerequisites

Before you configure a port-based IPv6
multicast VLAN, you must complete the following tasks:

·     Create VLANs as required.

·     Enable MLD snooping for the VLAN to be
configured as the IPv6 multicast VLAN.

·     Enable MLD snooping for all the VLANs that
contain the multicast receivers.

·     Configure the attributes of user ports. Make
sure the ports can forward packets from the VLAN to be configured as the IPv6 multicast
VLAN and send the packets with the VLAN tag removed. For more information about
configuring port attributes, see VLAN configuration in Layer
2—LAN Switching Configuration Guide.

#### Assigning user ports to an IPv6 multicast VLAN in IPv6 multicast VLAN view

**1\.**Enter system view.

system-view

**2\.**Configure an IPv6 VLAN as an IPv6 multicast
VLAN and enter IPv6 multicast VLAN view.

ipv6 multicast-vlan vlan-id

By default, a VLAN is not an IPv6
multicast VLAN.

**3\.**Assign ports to the IPv6 multicast VLAN as
user ports.

port interface-list

#### Assigning user ports to an IPv6 multicast VLAN in interface view

**1\.**Enter system view.

system-view

**2\.**Configure an IPv6 VLAN as an IPv6 multicast
VLAN and enter IPv6 multicast VLAN view.

ipv6 multicast-vlan vlan-id

By default, a VLAN is not an IPv6 multicast
VLAN.

**3\.**Return to system view.

quit

**4\.**Enter Layer 2 interface view.

¡     Enter
Layer 2 Ethernet interface view.

interface interface-type
interface-number

¡     Enter
Layer 2 aggregate interface view.

interface bridge-aggregation interface-number

**5\.**Assign the port to the IPv6 multicast VLAN
as a user port.

ipv6 port multicast-vlan vlan-id

By default, a port does not belong to any
IPv6 multicast VLAN.

## Setting the maximum number of IPv6 multicast VLAN forwarding entries

#### About this task

You can set the maximum number of IPv6
multicast VLAN forwarding entries on the device. When the upper limit is
reached, the device does not create IPv6 multicast VLAN forwarding entries
until some entries age out or are manually removed.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Set the maximum number of IPv6 multicast
VLAN forwarding entries.

ipv6 multicast-vlan entry-limit limit

By default, the maximum number of IPv6
multicast VLAN forwarding entries is 4000\.

## Display and maintenance commands for IPv6 multicast VLANs

Executedisplay commands in any
view and reset commands in user view.

 

| Task | Command || Display information about IPv6 multicast VLANs. | display ipv6 multicast-vlan \[ vlan-id ] || Display IPv6 multicast VLAN forwarding entries. | display ipv6 multicast-vlan forwarding-table \[ ipv6-source-address \[ prefix-length ] \| ipv6-group-address \[ prefix-length ] \| slot slot-number \| subvlan vlan-id \| vlan vlan-id ] \* || Display information about information about IPv6 multicast group entries in IPv6 multicast VLANs. | display ipv6 multicast-vlan group \[ ipv6-source-address \| ipv6-group-address \| slot slot-number \| verbose \| vlan vlan-id ] \* || Clear IPv6 multicast group entries in IPv6 multicast VLANs. | reset ipv6 multicast-vlan group \[ ipv6-group-address \[ prefix-length ] \| ipv6-source-address \[ prefix-length ] \| vlan vlan-id ] \* |




| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |

## IPv6 multicast VLAN configuration examples

### Example: Configuring sub-VLAN-based IPv6 multicast VLAN

#### Network configuration

As shown in [Figure 4](#_Ref156128396):

·     Layer 3 device Switch A runs MLD and acts as the
MLD querier. Layer 2 device Switch B runs MLDv1 snooping. 

·     The IPv6 multicast source sends IPv6 multicast
data to IPv6 multicast group FF1E::101. Receivers Host A, Host B, and Host C
belong to VLAN 2, VLAN 3, and VLAN 4, respectively.

Configure a sub-VLAN-based IPv6 multicast
VLAN on Switch B to meet the following requirements:

·     Switch A sends the IPv6 multicast data to Switch
B through the IPv6 multicast VLAN.

·     Switch B forwards the IPv6 multicast data to the
receivers in different user VLANs.

Figure 4 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705026_x_Img_x_png_3_2216040_294551_0.png)

‌

#### Procedure

**1\.**Configure Switch A:

\# Enable IPv6 multicast routing.

\<SwitchA\> system-view

\[SwitchA] ipv6 multicast routing

\[SwitchA-mrib6] quit

\# Create VLAN 20, and assign Ten-GigabitEthernet 1/0/2 to the VLAN.

\[SwitchA] vlan 20

\[SwitchA-vlan20] port ten-gigabitethernet 1/0/2

\[SwitchA-vlan20] quit

\# Assign an IPv6 address to VLAN-interface
20, and enable IPv6 PIM-DM on the interface.

\[SwitchA] interface
vlan-interface 20

\[SwitchA-Vlan-interface20] ipv6
address 1::2 64

\[SwitchA-Vlan-interface20] ipv6
pim dm

\[SwitchA-Vlan-interface20]
quit

\# Create VLAN 10\. 

\[SwitchA] vlan 10

\[SwitchA-vlan10] quit

\# Configure Ten-GigabitEthernet 1/0/1 as a hybrid port, and
assign the port to VLAN 10 as a tagged VLAN member.

\[SwitchA] interface ten-gigabitethernet 1/0/1

\[SwitchA-Ten-GigabitEthernet1/0/1] port link-type
hybrid

\[SwitchA-Ten-GigabitEthernet1/0/1] port hybrid vlan 10
tagged

\[SwitchA-Ten-GigabitEthernet1/0/1] quit

\# Assign an IPv6 address to VLAN-interface
10, and enable MLD on the interface.

\[SwitchA] interface
vlan-interface 10

\[SwitchA-Vlan-interface10] ipv6
address 2001::1 64

\[SwitchA-Vlan-interface10] mld
enable

\[SwitchA-Vlan-interface10]
quit

**2\.**Configure Switch B:

\# Enable the MLD snooping feature.

\<SwitchB\> system-view

\[SwitchB] mld-snooping

\[SwitchB-mld-snooping] quit

\# Create VLAN 2, assign Ten-GigabitEthernet 1/0/2 to the VLAN, and enable
MLD snooping for the VLAN.

\[SwitchB] vlan 2

\[SwitchB-vlan2] port ten-gigabitethernet 1/0/2

\[SwitchB-vlan2] mld-snooping
enable

\[SwitchB-vlan2] quit

\# Create VLAN 3, assign Ten-GigabitEthernet 1/0/3 to the VLAN, and enable
MLD snooping for the VLAN.

\[SwitchB] vlan 3

\[SwitchB-vlan3] port ten-gigabitethernet 1/0/3

\[SwitchB-vlan3] mld-snooping
enable

\[SwitchB-vlan3] quit

\# Create VLAN 4, assign Ten-GigabitEthernet 1/0/4 to the VLAN, and enable
MLD snooping for the VLAN.

\[SwitchB] vlan 4

\[SwitchB-vlan4] port ten-gigabitethernet 1/0/4

\[SwitchB-vlan4] mld-snooping
enable

\[SwitchB-vlan4] quit

\# Create VLAN 10, and enable MLD snooping
for the VLAN.

\[SwitchB] vlan 10

\[SwitchB-vlan10] mld-snooping
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

\# Configure VLAN 10 as an IPv6 multicast VLAN,
and assign VLAN 2 through VLAN 4 as sub-VLANs to multicast VLAN 10\.

\[SwitchB] ipv6 multicast-vlan
10

\[SwitchB-ipv6-mvlan-10]
subvlan 2 to 4

\[SwitchB-ipv6-mvlan-10] quit

#### Verifying the configuration

\# Display information about all IPv6
multicast VLANs on Switch B.

\[SwitchB] display ipv6 multicast-vlan

Total 1 IPv6 multicast VLANs.

 

IPv6 multicast VLAN 10:

  Sub-VLAN list(3 in total):

     2-4

  Port list(0 in total):

\# Display information about IPv6 multicast
groups in IPv6 multicast VLANs on Switch B.

\[SwitchB] display ipv6 multicast-vlan
group

Total 1 entries.

 

IPv6 multicast VLAN 10: Total 1
entries.

  (::, FF1E::101)

    Sub-VLANs (3 in total):

      VLAN 2

      VLAN 3

      VLAN 4

The output shows that IPv6 multicast group
FF1E::101 belongs to IPv6 multicast VLAN 10\. IPv6 multicast VLAN 10 contains
sub-VLANs VLAN 2 through VLAN 4\. Switch B will replicate the IPv6 multicast
data of VLAN 10 to VLAN 2 through VLAN 4\.

### Example: Configuring port-based IPv6 multicast VLAN

#### Network configuration

As shown in [Figure 5](#_Ref206476934): 

·     Layer 3 device Switch A runs MLDv1 and acts as
the MLD querier. Layer 2 device Switch B runs MLDv1 snooping.

·     The IPv6 multicast source sends IPv6 multicast
data to IPv6 multicast group FF1E::101. Receivers Host A, Host B, and Host C
belong to VLAN 2, VLAN 3, and VLAN 4, respectively.

Configure a port-based IPv6 multicast VLAN
on Switch B to meet the following requirements:

·     Switch A sends IPv6 multicast data to Switch B
through the IPv6 multicast VLAN.

·     Switch B forwards the IPv6 multicast data to the
receivers in different user VLANs.

Figure 5 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705027_x_Img_x_png_4_2216040_294551_0.png)

‌

#### Procedure

**1\.**Configure Switch A:

\# Enable IPv6 multicast routing.

\<SwitchA\> system-view

\[SwitchA] ipv6 multicast
routing

\[SwitchA-mrib6] quit

\# Create VLAN 20, and assign Ten-GigabitEthernet 1/0/2 to the VLAN.

\[SwitchA] vlan 20

\[SwitchA-vlan20] port ten-gigabitethernet 1/0/2

\[SwitchA-vlan20] quit

\# Assign an IPv6 address to VLAN-interface
20, and enable IPv6 PIM-DM on the interface.

\[SwitchA] interface
vlan-interface 20

\[SwitchA-Vlan-interface20] ipv6
address 1::2 64

\[SwitchA-Vlan-interface20] ipv6
pim dm

\[SwitchA-Vlan-interface20]
quit

\# Create VLAN 10, and assign Ten-GigabitEthernet 1/0/1 to the VLAN.

\[SwitchA] vlan 10

\[SwitchA-vlan10] port ten-gigabitethernet 1/0/1

\[SwitchA-vlan10] quit

\# Assign an IPv6 address to VLAN-interface
10, and enable MLD on the interface.

\[SwitchA] interface
vlan-interface 10

\[SwitchA-Vlan-interface10] ipv6
address 2001::1 64

\[SwitchA-Vlan-interface10] mld
enable

\[SwitchA-Vlan-interface10]
quit

**2\.**Configure Switch B:

\# Enable the MLD snooping feature.

\<SwitchB\> system-view

\[SwitchB] mld-snooping

\[SwitchB-mld-snooping] quit

\# Create VLAN 10, assign Ten-GigabitEthernet 1/0/1 to the VLAN, and
enable MLD snooping for the VLAN.

\[SwitchB] vlan 10

\[SwitchB-vlan10] port ten-gigabitethernet 1/0/1

\[SwitchB-vlan10] mld-snooping
enable

\[SwitchB-vlan10] quit

\# Create VLAN 2, and enable MLD snooping
for the VLAN.

\[SwitchB] vlan 2

\[SwitchB-vlan2] mld-snooping
enable

\[SwitchB-vlan2] quit

\# Create VLAN 3, and enable MLD snooping
for the VLAN.

\[SwitchB] vlan 3

\[SwitchB-vlan3] mld-snooping
enable

\[SwitchB-vlan3] quit

\# Create VLAN 4, and enable MLD snooping
for the VLAN.

\[SwitchB] vlan 4

\[SwitchB-vlan4] mld-snooping
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

\# Configure VLAN 10 as an IPv6 multicast
VLAN.

\[SwitchB] ipv6 multicast-vlan
10

\# Assign Ten-GigabitEthernet 1/0/2 and Ten-GigabitEthernet 1/0/3 to VLAN 10\.

\[SwitchB-ipv6-mvlan-10] port ten-gigabitethernet 1/0/2 to ten-gigabitethernet 1/0/3

\[SwitchB-ipv6-mvlan-10] quit

\# Assign Ten-GigabitEthernet 1/0/4 to VLAN 10\.

\[SwitchB] interface ten-gigabitethernet 1/0/4

\[SwitchB-Ten-GigabitEthernet1/0/4] ipv6 port
multicast-vlan 10

\[SwitchB-Ten-GigabitEthernet1/0/4] quit

#### Verifying the configuration

\# Display information about IPv6 multicast
VLANs on Switch B.

\[SwitchB] display ipv6 multicast-vlan

Total 1 IPv6 multicast VLANs.

 

IPv6 multicast VLAN 10:

  Sub-VLAN list(0 in total):

  Port list(3 in total):

    XGE1/0/2

    XGE1/0/3

    XGE1/0/4

\# Display dynamic MLD snooping forwarding
entries on Switch B.

\[SwitchB] display mld-snooping group

Total 1 entries.

 

VLAN 10: Total 1 entries.

  (::, FF1E::101)

    Host ports (3 in total):

      XGE1/0/2         (00:03:23)

      XGE1/0/3         (00:04:07)

      XGE1/0/4         (00:04:16)

The output shows that MLD snooping maintains
the user ports in the multicast VLAN (VLAN 10). Switch B will forward the IPv6 multicast
data of VLAN 10 through these user ports.

