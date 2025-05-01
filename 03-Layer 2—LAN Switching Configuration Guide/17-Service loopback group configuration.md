
# Configuring service loopback groups

## About service loopback groups

A service loopback
group contains one or multiple Ethernet ports for looping packets sent out by
the device back to the device. This feature must work with other features, such
as GRE. Member ports in a service loopback group are load balanced.

A service loopback group provides one of
the following services:

·     Tunnel—Supports unicast tunnel services.

·     Multicast tunnel—Supports multicast tunnel services.

·     Multiport—Supports multiport ARP services.

·     VSI gateway—Supports VSI gateway services.

## Restrictions and guidelines: Service loopback group configuration

When you add member ports to a service
loopback group, follow these restrictions and guidelines:

·     Make sure the ports support the service type of
the service loopback group and are not members of any other service loopback
group.

When you apply service loopback groups to features,
follow these restrictions and guidelines:

·     A service loopback group
can process services only after it is used by other features. You can apply a
service loopback group to other features once the group is created. One service
loopback group can be used by multiple features. 

·     You cannot change the service type of a service
loopback group.

·     Do not delete a service loopback group that is
being used by a feature.

## Configuring a service loopback group

**1\.**Enter system view.

system-view

**2\.**Create a service loopback group and specify
its service type.

service-loopback group group-id type { { multicast-tunnel \| tunnel } \* \| multiport \| vsigateway }

You can configure only one service
loopback group for a service type.

**3\.**Enter Layer 2 Ethernet interface view.

interface interface-type interface-number

**4\.**Assign the port to the service loopback
group.

port service-loopback group group-id

By default, a port does not belong to a
service loopback group.

 

| CAUTION | CAUTION:  ·     The configuration on a port is removed when it is assigned to a service loopback group. Make sure you fully understand the impact on your network when you assign a port to a service loopback group.  ·     Do not assign a physical interface to the service loopback group if that interface is the only member interface of an IRF port. Doing so will split the IRF fabric, because the assignment operation removes the binding of the physical interface and IRF port.  ·     For correct traffic processing, make sure a service loopback group has a minimum of one member port when it is being used by a feature. |
| --- | --- |

 

You can assign a maximum of 32 ports to a
service loopback group. 

## Display and maintenance commands for service loopback groups

Execute display commands
in any view.

 

| Task | Command |
| --- | --- |
| Display information about service loopback groups. | display service-loopback group \[ group-id ] |

 

## Service loopback group configuration examples

### Example: Configuring a service loopback group

#### Network configuration

All Ethernet ports on the device support unicast
tunnel services. Assign Ten-GigabitEthernet 1/0/1 through Ten-GigabitEthernet 1/0/3 to a service
loopback group to loop GRE packets sent out by the device back to the device.

#### Procedure

 

\# Create service loopback group 1, and
specify its service type as tunnel.

\<Sysname\> system-view

\[Sysname] service-loopback group 1 type
tunnel

\# Assign Ten-GigabitEthernet 1/0/1 through Ten-GigabitEthernet 1/0/3 to service loopback
group 1\.

\[Sysname] interface ten-gigabitethernet 1/0/1

\[Sysname-Ten-GigabitEthernet1/0/1] port service-loopback
group 1

All configurations on the interface
will be lost. Continue?\[Y/N]:y \[Sysname-Ten-GigabitEthernet1/0/1] quit

\[Sysname] interface ten-gigabitethernet 1/0/2

\[Sysname-Ten-GigabitEthernet1/0/2] port service-loopback
group 1

All configurations on the interface
will be lost. Continue?\[Y/N]:y \[Sysname-Ten-GigabitEthernet1/0/2] quit

\[Sysname] interface ten-gigabitethernet 1/0/3

\[Sysname-Ten-GigabitEthernet1/0/3] port service-loopback
group 1

All configurations on the interface
will be lost. Continue?\[Y/N]:y \[Sysname-Ten-GigabitEthernet1/0/3] quit

\# Create the interface Tunnel 1 and set it
to GRE mode. The interface will automatically use service loopback group 1\.

\[Sysname] interface tunnel 1 mode gre

\[Sysname-Tunnel1]

 

