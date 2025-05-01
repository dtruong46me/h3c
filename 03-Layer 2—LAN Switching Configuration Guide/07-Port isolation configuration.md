
# Configuring port isolation

## About port isolation

The port isolation feature
isolates Layer 2 traffic for data privacy and security without using VLANs. 

Ports in an isolation group cannot communicate
with each other. However, they can communicate with ports outside the isolation
group. 

You can configure community VLANs in an
isolation group. Ports in an isolation group can communicate with each other if
they belong to a community VLAN.

As shown in [Figure 1](#_Ref354041640):

·     To isolate Host A from Host C, and Host B from
Host D, assign Port B and Port C on Device A to
isolation group 1\. 

·     To enable Layer 2 communication between Host B
and Host D, specify VLAN 3 as a community VLAN in isolation group 1\. Host B and
Host D belong to VLAN 3\. 

Figure 1 Community VLANs in an isolation
group

![](https://resource.h3c.com/en/202407/12/20240712_11704365_x_Img_x_png_0_2215977_294551_0.png)

‌

## Assigning a port to an isolation group

#### About this task

The device supports multiple isolation
groups, which can be configured manually. The number of ports assigned to an
isolation group is not limited.

#### Restrictions and guidelines

·     You can assign a port to only one isolation
group. If you execute the port-isolate enable group command multiple times, the most recent configuration takes effect.

·     The configuration in Layer 2 Ethernet interface
view applies only to the interface.

·     The configuration in Layer 2 aggregate interface
view applies to the Layer 2 aggregate interface and its aggregation member
ports. If the device fails to apply the configuration to the aggregate
interface, it does not assign any aggregation member port to the isolation
group. If the failure occurs on an aggregation member port, the device skips
the port and continues to assign other aggregation member ports to the
isolation group.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Create an isolation group.

port-isolate group group-id

**3\.**Enter interface view.

¡     Enter
Layer 2 Ethernet interface view.

interface interface-type interface-number

¡     Enter
Layer 2 aggregate interface view.

interface bridge-aggregation interface-number

**4\.**Assign the port to the isolation group.

port-isolate enable group group-id

By default, the port is not in any
isolation group. 

## Configuring community VLANs

#### About this task

You can configure community VLANs in an
isolation group. Ports in an isolation group can communicate with each other if
they belong to a community VLAN.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Create an isolation group. 

port-isolate group group-id

**3\.**Specify the community VLANs.

community-vlan vlan { vlan-id-list \| all }

By default, an isolation group does not
contain any community VLANs. 

## Display and maintenance commands for port isolation

Execute display
commands in any view.

 

| Task | Command |
| --- | --- |
| Display isolation group information. | display port-isolate group \[ group-id ] |

 

## Port isolation configuration examples

### Example:Configuring port isolation

#### Network configuration

As shown in [Figure 2](#_Ref230746859):

·     LAN users Host A, Host B, and Host C are
connected to Ten-GigabitEthernet 1/0/1, Ten-GigabitEthernet 1/0/2, and Ten-GigabitEthernet 1/0/3 on the device,
respectively. 

·     The device connects to the Internet through Ten-GigabitEthernet 1/0/4.

Configure the device to provide Internet
access for the hosts, and isolate them from one another at Layer 2\.

Figure 2 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11704366_x_Img_x_png_1_2215977_294551_0.png)

‌

#### Procedure

\# Create isolation group 2\. 

\<Device\> system-view

\[Device] port-isolate group 2

\# Assign Ten-GigabitEthernet 1/0/1, Ten-GigabitEthernet 1/0/2, and Ten-GigabitEthernet 1/0/3 to isolation group
2\.

\[Device] interface ten-gigabitethernet 1/0/1

\[Device-Ten-GigabitEthernet1/0/1] port-isolate
enable group 2

\[Device-Ten-GigabitEthernet1/0/1] quit

\[Device] interface ten-gigabitethernet 1/0/2

\[Device-Ten-GigabitEthernet1/0/2] port-isolate
enable group 2

\[Device-Ten-GigabitEthernet1/0/2] quit

\[Device] interface ten-gigabitethernet 1/0/3

\[Device-Ten-GigabitEthernet1/0/3] port-isolate
enable group 2

\[Device-Ten-GigabitEthernet1/0/3] quit

#### Verifying the configuration

\# Display information about isolation group
2\. 

\[Device] display port-isolate group 2

 Port isolation group information:

 Group ID: 2

 Group members:

    Ten-GigabitEthernet1/0/1          Ten-GigabitEthernet1/0/2

    Ten-GigabitEthernet1/0/3

 Community VLAN ID: None

The output shows that Ten-GigabitEthernet 1/0/1, Ten-GigabitEthernet 1/0/2, and Ten-GigabitEthernet 1/0/3 are assigned to
isolation group 2\. As a result, Host A, Host B, and Host C are isolated from
one another at layer 2\.

### Example: Configuring community VLANs in port isolation

#### Network configuration

As shown in [Figure 3](#_Ref354054118), the
company branches Site 1 and Site 2 transfer service traffic in VLAN 2 and VLAN
3\. 

Configure port isolation and community
VLANs on the network to meet the following requirements: 

·     All hosts can access the Internet through Device
A. 

·     Host B and Host D can exchange video
conferencing traffic in VLAN 3\. 

·     Other Layer 2 traffic between Device B and
Device C is isolated.

Figure 3 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11704367_x_Img_x_png_2_2215977_294551_0.png)

‌

#### Procedure

**1\.**Configure Device A:

\# Create VLAN 2 and VLAN 3\.

\<DeviceA\> system-view

\[DeviceA] vlan 2 to 3

\# Configure Ten-GigabitEthernet 1/0/2 and Ten-GigabitEthernet 1/0/3 as trunk ports, and
assign them to VLAN 2 and VLAN 3\. 

\[DeviceA] interface ten-gigabitethernet 1/0/2

\[DeviceA-Ten-GigabitEthernet1/0/2] port link-type
trunk

\[DeviceA-Ten-GigabitEthernet1/0/2] port trunk permit
vlan 2 3

\[DeviceA-Ten-GigabitEthernet1/0/2] quit

\[DeviceA] interface ten-gigabitethernet 1/0/3

\[DeviceA-Ten-GigabitEthernet1/0/3] port link-type
trunk

\[DeviceA-Ten-GigabitEthernet1/0/3] port trunk permit
vlan 2 3

\[DeviceA-Ten-GigabitEthernet1/0/3] quit

\# Create isolation group 1\.

\[DeviceA] port-isolate group 1

\[DeviceA-port-isolate-group1]
quit

\# Assign Ten-GigabitEthernet 1/0/2 and Ten-GigabitEthernet 1/0/3 to isolation group 1\.

\[DeviceA] interface ten-gigabitethernet 1/0/2

\[DeviceA-Ten-GigabitEthernet1/0/2] port-isolate enable
group 1

\[DeviceA-Ten-GigabitEthernet1/0/2] quit

\[DeviceA] interface ten-gigabitethernet 1/0/3

\[DeviceA-Ten-GigabitEthernet1/0/3] port-isolate enable
group 1

\[DeviceA-Ten-GigabitEthernet1/0/3] quit

\# Configure VLAN 3 as a community VLAN in
isolation group 1\.

\[DeviceA] port-isolate group 1

\[DeviceA-port-isolate-group1]
community-vlan vlan 3 

\[DeviceA-port-isolate-group1]
quit

**2\.**Configure Device B:

\# Create VLAN 2 and assign Ten-GigabitEthernet 1/0/2 to it.

\<DeviceB\> system-view

\[DeviceB] vlan 2

\[DeviceB-vlan2] port ten-gigabitethernet 1/0/2

\[DeviceB-vlan2] quit

\# Create VLAN 3 and assign Ten-GigabitEthernet 1/0/3 to it.

\[DeviceB] vlan 3

\[DeviceB-vlan3] port ten-gigabitethernet 1/0/3

\[DeviceB-vlan3] quit

\# Configure Ten-GigabitEthernet 1/0/1 as a trunk port,
and assign it to VLAN 2 and VLAN 3\. 

\[DeviceB] interface ten-gigabitethernet 1/0/1

\[DeviceB-Ten-GigabitEthernet1/0/1] port link-type
trunk

\[DeviceB-Ten-GigabitEthernet1/0/1] port trunk permit
vlan 2 3

**3\.**Configure Device C in the same way Device B
is configured. 

#### Verifying the configuration

\# Display information about isolation group
1 on device A. 

\[DeviceA] display port-isolate group
1

 Port-isolate group information:

 Group ID: 1

 Group members:

   Ten-GigabitEthernet1/0/2     Ten-GigabitEthernet1/0/3

 Community VLAN ID: 3

The output shows that:

·     Ten-GigabitEthernet 1/0/2 and Ten-GigabitEthernet 1/0/3 are assigned to
isolation group 1\.

·     VLAN 3 is configured as a community VLAN in the isolation
group. 

