
# Configuring IP forwarding basic settings

## About FIB table

A device uses the FIB table to make packet
forwarding decisions.

A device selects optimal routes from the
routing table, and puts them into the FIB table. Each FIB entry specifies the
next hop IP address and output interface for packets destined for a specific
subnet or host.

For more information about the routing
table, see Layer 3—IP Routing Configuration Guide.

Use the display fib command to display the FIB table. The following example displays
the entire FIB table.

\<Sysname\> display fib

 

Destination count: 8 FIB entry count:
8

 

Flag:

  U:Usable   G:Gateway   H:Host  
B:Blackhole   D:Dynamic   S:Static

  R:Relay     F:FRR

 

Destination/Mask   Nexthop        
Flag     OutInterface/Token       Label

0.0.0.0/32         127.0.0.1      
UH       InLoop0                  Null

127.0.0.0/8        127.0.0.1      
U        InLoop0                  Null

127.0.0.0/32       127.0.0.1      
UH       InLoop0                  Null

127.0.0.1/32       127.0.0.1      
UH       InLoop0                  Null

127.255.255.255/32 127.0.0.1      
UH       InLoop0                  Null

224.0.0.0/4        0.0.0.0        
UB       NULL0                    Null

224.0.0.0/24       0.0.0.0        
UB       NULL0                    Null

255.255.255.255/32 127.0.0.1      
UH       InLoop0                  Null

A FIB entry
includes the following items:

·     Destination—Destination IP address.

·     Mask—Network mask. The mask and the destination address identify the
destination network. A logical AND operation between the destination address
and the network mask yields the address of the destination network. For
example, if the destination address is 192.168.1.40 and the mask 255.255.255.0,
the address of the destination network is 192.168.1.0. A network mask includes a
certain number of consecutive 1s. It can be expressed in dotted decimal format
or by the number of the 1s.

·     Nexthop—IP address of the next hop.

·     Flag—Route flag.

·     OutInterface—Output interface.

·     Token—MPLS Label Switched Path index number.

·     Label—Inner label.

## Saving the IP forwarding entries to a file

#### Restrictions and guidelines

The feature automatically creates the file
if you specify a nonexistent file. If the file already exists, this feature
overwrites the file content.

This feature triggers one-time saving of
the IP forwarding entries.

To automatically save the IP forwarding
entries periodically, configure a schedule for the device to automatically run
the ip
forwarding-table save command. For information
about scheduling a task, see Fundamentals Configuration
Guide.

#### Procedure

To save the IP forwarding entries to a file,
execute the following command in any view:

ip forwarding-table save filename filename

## Forwarding specific packets received from VXLAN tunnels in hardware

#### About this task

By default, the device forwards ARP and MLD
packets received from VXLAN tunnels to the CPU for processing when acting as a
VTEP in a distributed EVPN gateway network. If a large number of such packets
are received, packet loss might occur because of software rate limit, which
might cause service exceptions on downlink devices.

To resolve this issue, you can enable the
device to forward ARP and MLD packets received from VXLAN tunnels in hardware
without delivering them to the CPU.

#### Restrictions and guidelines

Traffic forwarding issues might occur if
you use this command together with either of the following commands:

·     flooding disable all all-direction

·     flooding disable broadcast all-direction

To make sure the device can forward traffic
correctly, perform the following steps before you enable this feature:

**1\.**Check the configuration for the flooding
disable all all-direction and flooding
disable broadcast all-direction commands.

**2\.**If either command exists, do the following:

**a.**Execute the undo flooding disable
command to enable flooding on the device.

**b.**Execute the flooding
disable all command to disable the device from
flooding any types of traffic received from ACs to VXLAN tunnel interfaces of
the VSI.

**c.**Configure port isolation for the ACs to
confine their flooding traffic.

To ensure access to slient devices, such as
dumb terminals, you must also execute the undo mac-address static source-check enable
command to disable the static source check
feature. If you cannot identify whether silient devices exist, disable static
source check as a best practice.

#### Procedure

**3\.**Enter system view.

system-view

**4\.**Enable hardware forwarding for specific
packets received from VXLAN tunnels.

forwarding vxlan-packet inner-protocol { ipv4 \| ipv6 } \*

By default, packets received from VXLAN
tunnels are delivered to the CPU for processing.

## Display and maintenance commands for FIB table

Execute display
commands in any view.

 

| Task | Command |
| --- | --- |
| Display FIB entries. | display fib \[ vpn-instance vpn-instance-name ] \[ ip-address \[ mask \| mask-length ] ] |

‌

