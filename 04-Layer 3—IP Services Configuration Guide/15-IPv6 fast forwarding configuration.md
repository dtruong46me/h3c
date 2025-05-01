
# Configuring IPv6 fast forwarding

## About IPv6 fast forwarding

Fast forwarding reduces route lookup time
and improves packet forwarding efficiency by using a high-speed cache and
data-flow-based technology. It identifies a data flow by using the following
fields:

·     Source IPv6 address.

·     Destination IPv6 address.

·     Source port number.

·     Destination port number.

·     Protocol number.

After a flow's first packet is forwarded
through the routing table, fast forwarding creates an entry and uses the entry
to forward subsequent packets of the flow.

## Configuring the aging time for IPv6 software fast forwarding entries

#### About this task

The IPv6 fast forwarding table uses an
aging timer for each forwarding entry. If an entry is not updated before the
timer expires, the device deletes the entry. If an entry has a hit within the
aging time, the aging timer restarts.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Set the aging time for IPv6 fast forwarding
entries on the software side.

ipv6 fast-forwarding aging-time aging-time

By default, the aging time is 30 seconds
for IPv6 fast forwarding entries on the software side.

## Configuring IPv6 fast forwarding load sharing for software

#### About this task

IPv6 software fast forwarding load sharing
enables the device to identify a data flow by using the packet information.

If IPv6 fast forwarding load sharing is
disabled on the software side, the device identifies a data flow by the packet information
and the input interface.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Configure IPv6 fast forwarding load sharing
on the software side. Choose one option as needed:

¡     Enable
IPv6 fast forwarding load sharing on the software side.

Ipv6 fast-forwarding load-sharing

¡     Disable
IPv6 fast forwarding load sharing on the software side.

undo ipv6 fast-forwarding load-sharing

By default, IPv6 fast forwarding load
sharing is enabled on the software side.

## Display and maintenance commands for IPv6 fast forwarding

Execute display commands
in any view and reset commands in user
view.

 

| Task | Command |
| --- | --- |
| Display the aging time of the IPv6 fast forwarding entries. | display ipv6 fast-forwarding aging-time || Display IPv6 fast forwarding entries. | display ipv6 fast-forwarding cache \[ ipv6-address ] \[ slot slot-number ] || Clear the IPv6 fast forwarding table. | reset ipv6 fast-forwarding cache \[ slot slot-number ] |



‌

