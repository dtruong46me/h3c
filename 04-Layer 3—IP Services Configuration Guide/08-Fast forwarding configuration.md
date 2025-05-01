
# Configuring fast forwarding

## About fast forwarding

Fast forwarding reduces route lookup time
and improves packet forwarding efficiency by using a high-speed cache and
data-flow-based technology. It identifies a data flow by using the following
fields: source IP address, source port number, destination IP address,
destination port number, and protocol number. After a flow's first packet is
forwarded through the routing table, fast forwarding creates an entry and uses
the entry to forward subsequent packets of the flow.

## Restrictions and guidelines: Fast forwarding configuration

Fast forwarding can process fragmented IP
packets, but it does not fragment IP packets.

## Configuring the aging time for software fast forwarding entries

#### About this task

The fast forwarding table uses an aging
timer for each forwarding entry. If an entry is not updated before the timer
expires, the device deletes the entry. If an entry has a hit within the aging
time, the aging timer restarts.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Configure the aging time for fast forwarding
entries on the software side.

ip fast-forwarding aging-time aging-time

By default, the aging time is 30 seconds for
fast forwarding entries on the software side.

## Configuring fast forwarding load sharing for software

#### About this task

Software fast forwarding load sharing
enables the device to identify a data flow by using the packet information.

If fast forwarding load sharing is disabled
on the software side, the device identifies a data flow by the packet information
and the input interface.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Configure fast forwarding load sharing on
the software side. Choose one option as needed:

¡     Enable
fast forwarding load sharing on the software side.

ip fast-forwarding load-sharing

¡     Disable
fast forwarding load sharing on the software side.

undo ip fast-forwarding load-sharing

By default, fast forwarding load sharing
is enabled on the software side.

## Display and maintenance commands for fast forwarding

Execute display commands
in any view and reset commands in user
view.

 

| Task | Command |
| --- | --- |
| Display the aging time of fast forwarding entries. | display ip fast-forwarding aging-time || Display fast forwarding entries. | display ip fast-forwarding cache \[ ip-address ] \[ slot slot-number ] || Display fast forwarding entries about fragmented packets. | display ip fast-forwarding fragcache \[ ip-address ] \[ slot slot-number ] || Clear the fast forwarding table. | reset ip fast-forwarding cache \[ slot slot-number ] |




 

