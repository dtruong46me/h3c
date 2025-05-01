
# Configuring loopback, null, and inloopback interfaces

This chapter describes how to configure a
loopback interface, a null interface, and an inloopback interface.

## About loopback, null, and inloopback interfaces

### About loopback interfaces

A loopback interface is a virtual
interface. The physical layer state of a loopback interface is always up unless
the loopback interface is manually shut down. Because of this benefit, loopback
interfaces are widely used in the following scenarios:

·     Configuring a loopback
interface address as the source address of the IP packets that the device
generates—Because loopback interface addresses
are stable unicast addresses, they are usually used as device identifications.

When you configure a rule on an authentication
or security server, you can configure it to permit or deny packets carrying the
loopback interface address of a device. This simplifies your configuration and
achieves the effect of permitting or denying packets that the device generates.
To use a loopback interface address as the source address of IP packets, make
sure the loopback interface is reachable from the peer by performing routing
configuration. All data packets sent to the loopback interface are considered
packets sent to the device itself, so the device does not forward these
packets.

·     Using a loopback interface
in dynamic routing protocols—With no router ID
configured for a dynamic routing protocol, the system selects the highest
loopback interface IP address as the router ID. In BGP, to avoid interruption
of BGP sessions due to physical port failure, you can use a loopback interface
as the source interface of BGP packets.

### About null interfaces

A null interface is a virtual interface and
is always up, but you cannot use it to forward data packets or configure it
with an IP address or link layer protocol. The null interface provides a
simpler way to filter packets than ACL. You can filter undesired traffic by
transmitting it to a null interface instead of applying an ACL. For example, if
you specify a null interface as the next hop of a static route to a network
segment, any packets routed to the network segment are dropped.

### About inloopback interfaces

An inloopback interface is a virtual
interface created by the system, which cannot be configured or deleted. The physical
layer and link layer protocol states of an inloopback interface are always up. All
IP packets sent to an inloopback interface are considered packets sent to the
device itself and are not forwarded.

## Configuring a loopback interface

**1\.**Enter system view.

system-view 

**2\.**Create a loopback interface and enter
loopback interface view.

interface loopback interface-number 

**3\.**Configure the interface description.

description text 

The default setting is interface name Interface (for example, LoopBack1 Interface).

**4\.**Configure the expected bandwidth of the
loopback interface.

bandwidth bandwidth-value 

By default, the expected bandwidth of a
loopback interface is 0 kbps.

**5\.**Bring up the loopback interface.

undo shutdown 

By default, a loopback interface is up.

## Configuring a null interface

**1\.**Enter system view.

system-view 

**2\.**Enter null interface view.

interface null 0

Interface Null 0 is the default null
interface on the device and cannot be manually created or removed.

Only one null interface, Null 0, is
supported on the device. The null interface number is always 0\. 

**3\.**Configure the interface description.

description text 

The default setting is NULL0 Interface.

## Restoring the default settings for an interface

#### Restrictions and guidelines

| CAUTION | CAUTION:  This feature might interrupt ongoing network services. Make sure you are fully aware of the impact of this feature when you use it on a live network. |
| --- | --- |

‌

This feature might fail to restore the
default settings for some commands because of command dependencies or system
restrictions. You can use the display this command in interface view to check for these commands and perform
their undo forms or follow the command
reference to restore their default settings. If your restoration attempt still
fails, follow the error message to resolve the problem.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter loopback interface view or null
interface view.

¡     interface loopback interface-number

¡     interface null
0

**3\.**Restore the default settings for the interface.

default

## Display and maintenance commands forloopback, null, and inloopback interfaces

Execute display commands
in any view and reset commands in user
view.

 

| Task | Command |
| --- | --- |
| Display information about the inloopback interface. | display interface \[ inloopback \[ 0 ] ] \[ brief \[ description \| down ] ] || Display information about the specified or all loopback interfaces. | display interface \[ loopback \[ interface-number ] ] \[ brief \[ description \| down ] ] || Display information about the null interface. | display interface \[ null \[ 0 ] ] \[ brief \[ description \| down ] ] || Clear the statistics on the specified or all loopback interfaces. | reset counters interface \[ loopback \[ interface-number ] ] || Clear the statistics on the null interface. | reset counters interface \[ null \[ 0 ] ] |





‌

