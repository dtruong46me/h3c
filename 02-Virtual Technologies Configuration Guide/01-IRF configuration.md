
# Configuring an IRF fabric

## About IRF

The Intelligent Resilient Framework (IRF) technology
virtualizes multiple physical devices at the same layer into one virtual fabric
to provide data center class availability and scalability. IRF virtualization
technology offers processing power, interaction, unified management, and
uninterrupted maintenance of multiple devices.

### IRF network model

[Figure 1](#_Ref406571749) shows an IRF fabric that has two devices, which appear as a single node
to the upper-layer and lower-layer devices.

Figure 1 IRF application scenario

![](https://resource.h3c.com/en/202412/25/20241225_12647038_x_Img_x_png_0_2328684_294551_0.png)

 

### IRF benefits

IRF provides the following benefits:

·     Simplified topology and
easy management—An IRF fabric appears as one
node and is accessible at a single IP address on the network. You can use this
IP address to log in at any member device to manage all the members of the IRF
fabric. In addition, you do not need to run the spanning tree feature among the
IRF members.

·     1:N redundancy—In an IRF fabric, one member acts as the master to manage and
control the entire IRF fabric. All the other members process services while
backing up the master. When the master fails, all the other member devices
elect a new master from among them to take over without interrupting services.

·     IRF link aggregation—You can assign several physical links between neighboring members
to their IRF ports to create a load-balanced aggregate IRF connection with
redundancy.

·     Multichassis link
aggregation—You can use the Ethernet link
aggregation feature to aggregate the physical links between the IRF fabric and its
upstream or downstream devices across the IRF members.

·     Network scalability and
resiliency—Processing capacity of an IRF fabric
equals the total processing capacities of all the members. You can increase
ports, network bandwidth, and processing capacity of an IRF fabric simply by
adding member devices without changing the network topology.

### Basic concepts

#### IRF member roles

IRF uses two member roles: master and standby
(called subordinate throughout the documentation).

When devices form an IRF fabric, they elect
a master to manage and control the IRF fabric, and all the other devices back
up the master. When the master device fails, the other devices automatically elect
a new master. For more information about master election, see "[Master election](#_Ref463985089)."

#### IRF member ID

An IRF fabric uses member IDs to uniquely identify
and manage its members. This member ID information is included as the first
part of interface numbers and file paths to uniquely identify interfaces and
files in an IRF fabric. Two devices cannot form an IRF fabric if they use the
same member ID. A device cannot join an IRF fabric if its member ID has been
used in the fabric.

#### Member priority

Member priority determines the possibility
of a member device to be elected the master. A member with higher priority is
more likely to be elected the master.

#### IRF port

An IRF port is a logical interface that
connects IRF member devices. Every IRF-capable device has two IRF ports. 

The IRF ports are named IRF-port n/1 and IRF-port n/2,
where n is the member ID of the device. The two
IRF ports are referred to as IRF-port 1 and IRF-port 2\.

To use an IRF port, you must bind a minimum
of one physical interface to it. The physical interfaces assigned to an IRF
port automatically form an aggregate IRF link. An IRF port goes down when all its
IRF physical interfaces are down.

#### IRF physical interface

IRF physical interfaces connect IRF member
devices and must be bound to an IRF port. They forward traffic between member
devices, including IRF protocol packets and data packets that must travel
across IRF member devices.

#### IRF split

IRF split occurs when an IRF fabric breaks
up into multiple IRF fabrics because of IRF link failures, as shown in [Figure 2](#_Ref391645571). The
split IRF fabrics operate with the same IP address. IRF split causes routing
and forwarding problems on the network. To quickly detect a multi-active
collision, configure a minimum of one MAD mechanism (see "[Configuring MAD](#_Ref401218102)").

Figure 2 IRF split

![](https://resource.h3c.com/en/202412/25/20241225_12647039_x_Img_x_png_1_2328684_294551_0.png)

‌

#### IRF merge

IRF merge occurs when two split IRF fabrics
reunite or when two independent IRF fabrics are united, as shown in [Figure 3](#_Ref391645556).

Figure 3 IRF merge

![](https://resource.h3c.com/en/202412/25/20241225_12647050_x_Img_x_png_2_2328684_294551_0.png)

‌

#### MAD

An IRF link failure causes an IRF fabric to
split in two IRF fabrics operating with the same Layer 3 settings, including the
same IP address. To avoid IP address collision and network problems, IRF uses
multi-active detection (MAD) mechanisms to detect the presence of multiple
identical IRF fabrics, handle collisions, and recover from faults.

#### IRF domain ID

One IRF fabric forms one IRF domain. IRF
uses IRF domain IDs to uniquely identify IRF fabrics and prevent IRF fabrics
from interfering with one another.

As shown in [Figure 4](#_Ref463807705), IRF
fabric 1 contains Device A and Device B, and IRF fabric 2 contains Device C and
Device D. Both fabrics use the LACP aggregate links between them for MAD. When
a member device receives an extended LACPDU for MAD, it checks the domain ID to
determine whether the packet is from the local IRF fabric. Then, the member device
can handle the packet correctly.

Figure 4 A network that contains two IRF
domains

![](https://resource.h3c.com/en/202412/25/20241225_12647061_x_Img_x_png_3_2328684_294551_0.png)

‌

### IRF network topology

An IRF fabric can use a daisy-chain or ring
topology. As shown in [Figure 5](#_Ref494360196), a
ring topology is more reliable. In ring topology, the failure of one IRF link
does not cause the IRF fabric to split as in daisy-chain topology. Rather, the
IRF fabric changes to a daisy-chain topology without interrupting network
services. 

Figure 5 Daisy-chain topology vs. ring
topology

![](https://resource.h3c.com/en/202412/25/20241225_12647072_x_Img_x_png_4_2328684_294551_0.png)

‌

### Master election

Master election occurs each time the IRF
fabric topology changes in the following situations:

·     The IRF fabric is established.

·     The master device fails or is removed.

·     The IRF fabric splits.

·     Independent IRF fabrics merge.

 

|  | NOTE:  Master election does not occur when split IRF fabrics merge. For information about the master device of the merged IRF fabric, see "[Failure recovery](#_Ref463890425)." |
| --- | --- |

‌

Master election selects a master in
descending order:

**1\.**Current master, even if a new member has
higher priority.

When an IRF fabric is being formed, all
members consider themselves as the master. This rule is skipped.

**2\.**Member with higher priority.

**3\.**Member with the longest system uptime.

Two members are considered to start up at
the same time if the difference between their startup times is equal to or less
than 10 minutes. For these members, the next tiebreaker applies.

**4\.**Member with the lowest CPU MAC address.

For the setup of a new IRF fabric, the
subordinate devices must reboot to complete the setup after the master
election.

For an IRF merge, devices must reboot if
they are in the IRF fabric that fails the master election.

### Interface naming conventions

A physical interface is numbered in the chassis-number/slot-number/interface-index format.

·     chassis-number—Member ID of the
device. The default value for this argument is 1\. Any change to the member ID
takes effect after a reboot.

·     slot-number—Slot number of the front panel or slot number of interface modules
on the rear panel.

¡     The
slot number of the front panel is fixed at 0\.

¡     The
slot numbers of the interface modules are 1 and 2, from left to right, when you
face the slots. 

·     interface-index—Interface index on
the device. Interface index depends on the number of physical interfaces available
on the device. To identify the index of a physical interface, examine its index
mark on the chassis.

For example, Ten-GigabitEthernet 2/0/1
represents the first fixed physical interface on member device 2\. Set its link
type to trunk, as follows:

\<Sysname\> system-view

\[Sysname] interface ten-gigabitethernet
2/0/1

\[Sysname-Ten-GigabitEthernet2/0/1]
port link-type trunk

### File system naming conventions

On a single-chassis fabric, you can use its
storage device name to access its file system.

On a multichassis IRF fabric, you can use
the storage device name to access the file system of the master. To access the
file system of any other member device, use the name in the slotmember-ID\#storage-device-name format.


For more information about storage device
naming conventions, see Fundamentals Configuration Guide.

For example:

·     To create and access the test folder under the
root directory of the flash memory on the master switch:

\<Master\> mkdir test

Creating directory
flash:/test... Done.

\<Master\> cd test

\<Master\> dir

Directory of flash:/test

The directory is empty.

 

1038336 KB total (690572 KB
free) 

·     To create and access the test
folder under the root directory of the flash memory on member device 3:

\<Master\> mkdir slot3#flash:/test

Creating directory slot3#flash:/test...
Done.

\<Master\> cd slot3#flash:/test

\<Master\> dir

Directory of slot3#flash:/test

The directory is empty.

 

1038336 KB total (690572 KB
free) 

### Configuration synchronization

IRF uses a strict running-configuration
synchronization mechanism. In an IRF fabric, all devices obtain and run the
running configuration of the master. Configuration changes are automatically propagated
from the master to the remaining devices. The configuration files of these
devices are retained, but the files do not take effect. The devices use their
own startup configuration files only after they are removed from the IRF fabric.

As a best practice, back up the
next-startup configuration file on a device before adding the device to an IRF
fabric as a subordinate.

A subordinate device's next-startup
configuration file might be overwritten if the master and the subordinate use
the same file name for their next-startup configuration files. You can use the
backup file to restore the original configuration after removing the
subordinate from the IRF fabric.

For more information about configuration
management, see Fundamentals Configuration Guide.

### Multi-active handling procedure

The multi-active handling procedure
includes detection, collision handling, and failure recovery.

#### Detection

IRF provides MAD mechanisms by extending
LACP, BFD, ARP, and IPv6 ND to detect multi-active collisions. As a best
practice, configure a minimum of one MAD mechanism on an IRF fabric. For more
information about the MAD mechanisms and their application scenarios, see
"[MAD mechanisms](#_Ref393263757)."

For information about LACP, see Ethernet
link aggregation in Layer 2—LAN Switching Configuration
Guide. For information about BFD, see High
Availability Configuration Guide. For information about ARP, see Layer 3—IP Services Configuration Guide. For
information about ND, see IPv6 basics in Layer 3—IP
Services Configuration Guide.

#### Collision handling

When detecting a multi-active collision, MAD
disables all IRF fabrics except one from forwarding data traffic by placing
them in Recovery state. The IRF fabrics placed in Recovery state are called
inactive IRF fabrics. The IRF fabric that continues to forward traffic is
called the active IRF fabric.

BFD MAD and LACP MAD use the following
process to handle a multi-active collision:

**5\.**Compare the number of members in each fabric.


**6\.**Set all fabrics to the Recovery state except
the one that has the most members.

**7\.**Compare the member IDs of their masters if
all IRF fabrics have the same number of members.

**8\.**Set all fabrics to the Recovery state except
the one that has the lowest numbered master.

**9\.**Shut down all common network interfaces in
the Recovery-state fabrics except for the following interfaces:

¡     Interfaces
automatically excluded from being shut down by the system.

¡     Interfaces
specified by using the mad exclude interface command. 

ARP MAD and ND MAD use the following
process to handle a multi-active collision:

**10\.**Compare the member IDs of the masters in the
IRF fabrics.

**11\.**Set all fabrics to the Recovery state except
the one that has the lowest numbered master.

**12\.**Shut down all common network interfaces in
the Recovery-state fabrics except for the following interfaces:

¡     Interfaces
automatically excluded from being shut down by the system.

¡     Interfaces
specified by using the mad exclude interface command. 

#### Failure recovery

To merge two split IRF fabrics, first repair
the failed IRF link and remove the IRF link failure.

When the failed IRF link between two split
IRF fabrics is recovered, all member devices in the inactive IRF fabric automatically
reboot to join the active IRF fabric as subordinate members. The network
interfaces that have been shut down by MAD automatically restore their original
state, as shown in [Figure 6](#_Ref463889842). 

Figure 6 Recovering the IRF fabric

![](https://resource.h3c.com/en/202412/25/20241225_12647074_x_Img_x_png_5_2328684_294551_0.png)

 

If the active IRF fabric fails before the
IRF link is recovered (see [Figure 7](#_Ref463889877)), use
the mad
restore command on the inactive IRF fabric to recover
the inactive IRF fabric. This command brings up all network interfaces that
were shut down by MAD. After the IRF link is repaired, merge the two parts into
a unified IRF fabric.

Figure 7 Active IRF fabric fails before the
IRF link is recovered

![](https://resource.h3c.com/en/202412/25/20241225_12647075_x_Img_x_png_6_2328684_294551_0.png)

‌

### MAD mechanisms

IRF provides MAD mechanisms by extending
LACP, BFD, ARP, and IPv6 ND.

[Table 1](#_Ref393268203) compares the MAD mechanisms and their application scenarios.

Table 1 Comparison of MAD mechanisms

| MAD mechanism | Advantages | Disadvantages | Application scenarios |
| --- | --- | --- | --- |
| [LACP MAD](#_Ref393269492) | ·     Detection speed is fast.  ·     Runs on existing aggregate links without requiring MAD-dedicated physical links or Layer 3 interfaces. | Requires an intermediate device that supports extended LACP for MAD. | Link aggregation is used between the IRF fabric and its upstream or downstream device. || [BFD MAD](#_Ref393269498) | ·     Detection speed is fast.  ·     Intermediate device, if used, can come from any vendor. | Requires MAD dedicated physical links and Layer 3 interfaces, which cannot be used for transmitting user traffic. | ·     No special requirements for network scenarios.  ·     If no intermediate device is used, this mechanism is only suitable for IRF fabrics that have only two members that are geographically close to one another. || [ARP MAD](#_Ref420056425) | ·     No intermediate device is required.  ·     Intermediate device, if used, can come from any vendor.  ·     Does not require MAD dedicated ports. | ·     Detection speed is slower than BFD MAD and LACP MAD.  ·     The spanning tree feature must be enabled if common Ethernet ports are used for ARP MAD links. | Spanning tree-enabled non-link aggregation IPv4 network scenarios if common Ethernet ports are used. || [ND MAD](#_Ref393269512) | ·     No intermediate device is required.  ·     Intermediate device, if used, can come from any vendor.  ·     Does not require MAD dedicated ports. | ·     Detection speed is slower than BFD MAD and LACP MAD.  ·     The spanning tree feature must be enabled if common Ethernet ports are used for ND MAD links. | Spanning tree-enabled non-link aggregation IPv6 network scenarios if common Ethernet ports are used. |




‌

#### LACP MAD

As shown in [Figure 8](#_Ref391645448), LACP
MAD has the following requirements:

·     Every IRF member must have a link with an
intermediate device.

·     All the links form a dynamic link aggregation
group.

·     The intermediate device must be a device that
supports extended LACP for MAD.

The IRF member devices send extended
LACPDUs that convey a domain ID and an active ID (the member ID of the master).
The intermediate device transparently forwards the extended LACPDUs received
from one member device to all the other member devices.

·     If the domain IDs and active IDs sent by all the
member devices are the same, the IRF fabric is integrated.

·     If the extended LACPDUs convey the same domain
ID but different active IDs, a split has occurred. LACP MAD handles this
situation as described in "[Collision handling](#_Ref392231980)."

Figure 8 LACP MAD scenario

![](https://resource.h3c.com/en/202412/25/20241225_12647076_x_Img_x_png_7_2328684_294551_0.png)

‌

#### BFD MAD

BFD MAD detects
multi-active collisions by using BFD.

You can use common or management Ethernet
ports for BFD MAD.

If management Ethernet ports are used, BFD
MAD has the following requirements:

·     Use an intermediate device and make sure each IRF
member device has a BFD MAD link to the intermediate device.

·     Assign each member device a MAD IP address on
the master's management Ethernet port.

If common Ethernet ports are used, BFD MAD
has the following requirements: 

·     If an intermediate device is used, each member
device must have a BFD MAD link to the intermediate device. If no intermediate
device is used, all member devices must have a BFD MAD link to each other. 

·     Assign all ports on the BFD MAD links to the
same VLAN or Layer 3 aggregate interface. Assign each member device a MAD IP
address on the VLAN interface or Layer 3 aggregate interface. 

When you use BFD MAD, follow these
restrictions and guidelines:

·     As a best practice, use an intermediate device
to connect IRF member devices if the IRF fabric has more than two member
devices. A full mesh of IRF members might cause broadcast loops.

·     As a best practice, preferentially use
management Ethernet ports for BFD MAD.

·     The BFD MAD links and BFD MAD VLAN (or Layer 3 aggregate
interface) must be dedicated. Do not use the BFD MAD links or BFD MAD VLAN (or
Layer 3 aggregate interface) for any other purposes. 

·     When you use a Layer 3 aggregate interface for
BFD MAD, make sure its member ports do not exceed the maximum number of
Selected ports allowed for an aggregation group. If the number of member ports
exceeds the maximum number of Selected ports, some member ports cannot become
Selected. BFD MAD will be unable to work correctly and its state will change to
Faulty. For more information about setting the maximum number of Selected ports
for an aggregation group, see Ethernet link aggregation in Layer 2—LAN Switching Configuration Guide. 

 

|  | NOTE:  ·     The MAD addresses identify the member devices and must belong to the same subnet.  ·     Of all management Ethernet ports on an IRF fabric, only the master's management Ethernet port is accessible. |
| --- | --- |

 

[Figure 9](#_Ref409512969) shows
a typical BFD MAD scenario that uses an intermediate device. On the
intermediate device, assign the ports on the BFD MAD links to the same VLAN.

[Figure 10](#_Ref408474927) shows
a typical BFD MAD scenario that does not use an intermediate device.

With BFD MAD, the master attempts to
establish BFD sessions with other member devices by using its MAD IP address as
the source IP address.

·     If the IRF fabric is integrated, only the MAD IP
address of the master takes effect. The master cannot establish a BFD session
with any other member. If you execute the display bfd session command, the state of the BFD sessions is Down.

·     When the IRF fabric splits, the IP addresses of
the masters in the split IRF fabrics take effect. The masters can establish a
BFD session. If you execute the display bfd session command, the state of the BFD session between the two devices is Up.

Figure 9 BFD MAD scenario with an intermediate
device

![](https://resource.h3c.com/en/202412/25/20241225_12647077_x_Img_x_png_8_2328684_294551_0.png)

 

Figure 10 BFD MAD scenario without an intermediate
device

![](https://resource.h3c.com/en/202412/25/20241225_12647078_x_Img_x_png_9_2328684_294551_0.png)

‌

#### ARP MAD

ARP MAD detects multi-active collisions by
using extended ARP packets that convey the IRF domain ID and the active ID.

You can use common or management Ethernet
ports for ARP MAD.

If management Ethernet ports are used, ARP
MAD must work with an intermediate device. Make sure the following requirements
are met:

·     Connect a management Ethernet port on each
member device to the intermediate device. 

·     On the intermediate device, you must assign the
ports used for ARP MAD to the same VLAN.

If common Ethernet ports are used, ARP MAD
can work with or without an intermediate device. Make sure the following
requirements are met: 

·     If an intermediate device is used, connect each
IRF member device to the intermediate device, as shown in [Figure 11](#_Ref391645506). Run
the spanning tree feature between the IRF fabric and the intermediate device.
In this situation, data links can be used.

·     If no intermediate device is used, connect each
IRF member device to all other member devices. In this situation, IRF links
cannot be used for ARP MAD.

Each IRF member compares the domain ID and
the active ID (the member ID of the master) in incoming extended ARP packets
with its domain ID and active ID.

·     If the domain IDs are different, the extended ARP
packet is from a different IRF fabric. The device does not continue to process
the packet with the MAD mechanism.

·     If the domain IDs are the same, the device
compares the active IDs.

¡     If
the active IDs are different, the IRF fabric has split.

¡     If the
active IDs are the same, the IRF fabric is integrated.

Figure 11 ARP MAD scenario

![](https://resource.h3c.com/en/202412/25/20241225_12647040_x_Img_x_png_10_2328684_294551_0.png)

‌

#### ND MAD

ND MAD detects multi-active collisions by
using NS packets to transmit the IRF domain ID and the active ID.

You can use common or management Ethernet
ports for ND MAD.

If management Ethernet ports are used, ND
MAD must work with an intermediate device. Make sure the following requirements
are met:

·     Connect a management Ethernet port on each
member device to the intermediate device. 

·     On the intermediate device, you must assign the
ports used for ND MAD to the same VLAN.

If common Ethernet ports are used, ND MAD
can work with or without an intermediate device. Make sure the following
requirements are met: 

·     If an intermediate device is used, connect each
IRF member device to the intermediate device, as shown in [Figure 12](#_Ref391645521). Run
the spanning tree feature between the IRF fabric and the intermediate device.
In this situation, data links can be used.

·     If no intermediate device is used, connect each
IRF member device to all other member devices. In this situation, IRF links
cannot be used for ND MAD.

Each IRF member device compares the domain
ID and the active ID (the member ID of the master) in incoming NS packets with
its domain ID and active ID.

·     If the domain IDs are different, the NS packet
is from a different IRF fabric. The device does not continue to process the
packet with the MAD mechanism.

·     If the domain IDs are the same, the device
compares the active IDs.

¡     If
the active IDs are different, the IRF fabric has split.

¡     If
the active IDs are the same, the IRF fabric is integrated.

Figure 12 ND MAD scenario

![](https://resource.h3c.com/en/202412/25/20241225_12647041_x_Img_x_png_11_2328684_294551_0.png)

‌

## Restrictions and guidelines: IRF configuration

### Hardware compatibility with IRF

Switches in the S6520X-EI and S6520X-HI
series are divided into four groups. The switches must be in the same group to
form an IRF fabric.

 

| Group | Switches || 1 | ·     S6520X-30QC-EI  ·     S6520X-54QC-EI  ·     S6520X-30HC-EI  ·     S6520X-54HC-EI || 2 | ·     S6520X-30HF-HI  ·     S6520X-54HF-HI || 3 | ·     S6520X-30HF-EI  ·     S6520X-54HF-EI  ·     S6520X-54HC-UPWR-EI || 4 | ·     S6520X-30QC-HI  ·     S6520X-54QC-HI  ·     S6520X-30HC-HI  ·     S6520X-54HC-HI |




| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |

 

Switches in the S6520X-SI series are
divided into two groups. The switches must be in the same group to form an IRF
fabric.

 

| Group | Switches || 1 | ·     S6520X-16ST-SI  ·     S6520X-24ST-SI  ·     S6520X-10XT-SI  ·     S6520X-16XT-SI  ·     S6520X-18C-SI  ·     S6520X-26C-SI  ·     S6520X-26XC-UPWR-SI  ·     S6520X-54XC-UPWR-SI || 2 | ·     S6520X-26MC-SI  ·     S6520X-26MC-UPWR-SI |


| --- | --- | --- | --- | --- | --- |

 

Switches in the S6520-SI series are divided
into two groups. The switches must be in the same group to form an IRF fabric.

 

| Group | Switches || 1 | ·     S6520-16S-SI  ·     S6520-24S-SI  ·     S6520-26Q-SI || 2 | ·     S6520-30SG-SI  ·     S6520-22SG-SI |


| --- | --- | --- | --- | --- | --- |

 

Switches in the S5560X-HI series are
divided into two groups. The switches must be in the same group to form an IRF
fabric.

 

| Group | Switches || 1 | ·     S5560X-34C-HI  ·     S5560X-58C-HI || 2 | ·     S5560X-30F-HI  ·     S5560X-54F-HI |


| --- | --- | --- | --- | --- | --- |

 

An S5000-EI switch can form an IRF fabric
only with switches in the same series.

An MS4600 switch can form an IRF fabric
only with switches in the same series.

### Software requirements for IRF

All IRF member devices must run the same
software image version. Make sure the software auto-update feature is enabled
on all member devices.

### Candidate IRF physical interfaces

Use [Table 2](#_Ref34903794) to
select ports for establishing IRF links. Make sure these ports operate at their
highest rate when they act as IRF physical interfaces.

Table 2 Candidate IRF physical ports on the
switches

| Hardware platforms | Candidate physical IRF interfaces (required port speed) | Restrictions and guidelines |
| --- | --- | --- |
| S6520X-EI switch series  S6520X-HI switch series | Ports on the front panel or on the interface module:  ·     SFP\+ ports (10 Gbps).  ·     SFP28 ports (25 Gbps).  ·     QSFP\+ ports (40 Gbps).  ·     QSFP28 ports (100 Gbps). | You cannot use the SFP\+ or SFP28 breakout interfaces of a QSFP\+ or QSFP28 port as IRF physical interfaces. || S5560X-HI switch series | Ports on the front panel or on the interface module:  ·     SFP\+ ports (10 Gbps).  ·     SFP28 ports (25 Gbps).  ·     QSFP\+ ports (40 Gbps).  ·     5G/2.5G/1000BASE-T autosensing Ethernet ports (5Gbps).  ·     10G/5G/2.5G/1000BASE-T autosensing Ethernet ports (10 Gbps). | You cannot use the SFP\+ breakout interfaces of a QSFP\+ port as IRF physical interfaces. || S6520X-SI switch series | Ports on the front panel or on the interface module:  ·     5G/2.5G/1000BASE-T autosensing Ethernet ports (5 Gbps).  ·     5G/2.5G/1000/100 BASE-T autosensing Ethernet ports (5Gbps).  ·     10G/5G/2.5G/1000BASE-T autosensing Ethernet ports (10 Gbps).  ·     10G/5G/2.5G/1000/100 BASE-T autosensing Ethernet ports (10 Gbps).  ·     10G/1GBase-T Ethernet ports (10 Gbps).  ·     SFP\+ ports (10 Gbps).  ·     SFP28 ports (25 Gbps).  ·     QSFP\+ ports (40 Gbps). | You cannot use the SFP\+ breakout interfaces of a QSFP\+ port as IRF physical interfaces.  You cannot use ports on the LSPM4G4T6P module as IRF physical interfaces. || S6520-SI switch series | ·     SFP\+ ports (10 Gbps).  ·     QSFP\+ ports (40 Gbps). | You cannot use the SFP\+ breakout interfaces of a QSFP\+ port as IRF physical interfaces. || MS4600 switch series  S5000-EI switch series | SFP\+ ports (10 Gbps) | N/A |





 

### Transceiver modules and cables selection for IRF

When you select transceiver modules and
cables, follow these restrictions and guidelines:

·     For 5G/2.5G/1000BASE-T, 5G/2.5G/1000/100 BASE-T,
10G/5G/2.5G/1000BASE-T, and 10G/5G/2.5G/1000/100 BASE-T Ethernet ports, the applicable cables vary by port rate and
connection distance. For more information, see the installation guide for the
device.

·     To connect 10G/1GBase-T Ethernet ports, use Category
6 (or above) twisted-pair cables for a short-distance connection.

·     For SFP\+ ports, use SFP\+ transceiver modules and
fibers for a long-distance connection and use SFP\+ cables for a short-distance
connection.

·     For SFP28 ports, use SFP28 transceiver modules
and fibers for a long-distance connection and use SFP28 cables for a
short-distance connection.

·     For QSFP\+ ports, use QSFP\+ transceiver modules
and fibers for a long-distance connection and use QSFP\+ cables for a
short-distance connection.

·     For QSFP28 ports:

Use the following media for a
long-distance connection:

¡     QSFP28
transceiver modules and fibers.

¡     QSFP\+
transceiver modules and fibers.

Use QSFP28 or QSFP\+ cables for a short-distance
connection.

·     The transceiver modules at the two ends of an
IRF link must be the same type. 

For more information about the transceiver
modules and cables, see the device installation guide and H3C Transceiver Modules User Guide.

 

|  | NOTE:  The transceiver modules and cables available for the device are subject to change over time. For the most up-to-date list of transceiver modules and cables, contact your H3C sales representative. |
| --- | --- |

‌

### IRF port connection

When you connect two neighboring IRF
members, follow these restrictions and guidelines:

·     You must connect the physical interfaces of
IRF-port 1 on one member to the physical interfaces of IRF-port 2 on the other.

·     For high availability, you can bind a maximum of
16 physical interfaces to an IRF port.

Figure 13 Connecting IRF physical interfaces

![](https://resource.h3c.com/en/202412/25/20241225_12647042_x_Img_x_png_12_2328684_294551_0.png)

‌

### IRF physical interface configuration restrictions and guidelines

#### Command configuration restrictions

On a physical interface bound to an IRF
port, you can execute only the following commands:

·     Basic interface commands, including shutdown and description. For more
information about these commands, see Ethernet interface commands in Layer 2—LAN Switching Command Reference. To execute the
shutdown
command on the last IRF physical interface in up
state, make sure the interface is on the master device.

·     The flow-interval command, which sets the statistics polling interval on an interface.
For more information about this command, see Ethernet interface commands in Layer 2—LAN Switching Command Reference.

·     MAC address table configuration commands,
including the mac-address static source-check enable command. In a VXLAN or EVPN network, to ensure successful forwarding
of Layer 3 traffic across member devices, use the undo
mac-address static source-check enable command
on each IRF physical interface. For information about this command, see MAC
address table commands in Layer 2—LAN Switching Command Reference. 

·     LLDP commands, including:

¡     lldp
admin-status.

¡     lldp
check-change-interval.

¡     lldp enable.

¡     lldp
encapsulation snap.

¡     lldp
notification remote-change enable.

¡     lldp tlv-enable.

For more information about these commands,
see LLDP in Layer 2—LAN Switching Command Reference.

·     The mirroring-group
reflector-port command, which specifies the
physical interface as a reflector port for remote mirroring. For more information
about this command, see port mirroring in Network
Management and Monitoring Command Reference.

‌

| IMPORTANT | IMPORTANT:  Do not execute the mirroring-group reflector-port command on an IRF physical interface if that interface is the only member interface of an IRF port. Doing so will split the IRF fabric, because this command also removes the binding of the physical interface and IRF port. |
| --- | --- |

‌

#### Suppressing SNMP notifications of packet drops on IRF physical interfaces

Before an IRF member device forwards a
packet, it examines its forwarding path in the IRF fabric for a loop. If a loop
exists, the device discards the packet on the source interface of the looped
path. This loop elimination mechanism will drop a large number of broadcast
packets on the IRF physical interfaces.

To suppress SNMP notifications of packet
drops that do not require attention, do not monitor packet forwarding on the
IRF physical interfaces.

### Feature compatibility and configuration restrictions with IRF

#### System operating mode

To form an IRF fabric, all member devices
must work in the same system operating mode. To set the system operating mode,
use the switch-mode command. For more
information about the system operating mode, see device management in Fundamentals Configuration Guide. 

#### Routing settings

To form an IRF fabric, all member devices
must use the same settings for the following routing features:

·     Maximum number of ECMP routes (set by using the max-ecmp-num command).

For more information about the routing
features, see basic IP routing in Layer 3—IP Routing Configuration
Guide.

### Configuration rollback restrictions

The configuration rollback feature cannot
roll back the following IRF settings:

·     Member device description (set by using the irf member
description command).

·     Member device priority (set by using the irf member priority command).

·     IRF physical interface and IRF port bindings
(set by using the port group interface
command).

For more information about the
configuration rollback feature, see configuration file management in Fundamentals Configuration Guide.

## IRF tasks at a glance

To configure IRF, perform the following
tasks:

**1\.**[Setting up an IRF fabric](#_Ref475439170)

**2\.**[Configuring MAD](#_Ref401218102)

Configure a
minimum of one MAD mechanism on an IRF fabric. For the MAD compatibility, see
"[MAD mechanism compatibility](#_Ref476931461)."

¡     [Configuring LACP MAD](#_Ref460399689)

¡     [Configuring BFD MAD](#_Ref460399694)

¡     [Configuring ARP MAD](#_Ref460399698)

¡     [Configuring ND MAD](#_Ref460399701)

¡     [Excluding interfaces from the shutdown
action upon detection of multi-active collision](#_Ref459037433)

This feature excludes an interface from the shutdown action for
management or other special purposes when an IRF fabric transits to the
Recovery state.

¡     [Recovering an IRF fabric](#_Ref383518138)

**3\.**(Optional.) [Optimizing IRF settings for an IRF
fabric](#_Ref463984305)

¡     [Configuring a member device description](#_Ref383518048)

¡     [Configuring IRF bridge MAC address
settings](#_Ref509333116)

¡     [Enabling software auto-update for
software image synchronization](#_Ref463988249)

This feature automatically synchronizes
the current software images of the master to devices that are attempting to
join the IRF fabric.

¡     [Setting the IRF link down report delay](#_Ref383518110)

¡     [Removing an interface module that has
IRF physical interfaces](#_Ref499192415)

¡     [Replacing an interface module that has
IRF physical interfaces](#_Ref499192422)

## Planning the IRF fabric setup

Consider the following items when you plan
an IRF fabric:

·     Hardware compatibility and restrictions.

·     IRF fabric size.

·     Master device.

·     Member ID and priority assignment scheme.

·     Fabric topology and cabling scheme.

·     IRF physical interfaces.

## Setting up an IRF fabric

### IRF setup tasks at a glance

To set up an IRF fabric, perform the following
tasks:

**4\.**Configure member IDs, priorities, and
IRF physical interfaces separately. 

**a.**[Assigning a member ID to each IRF member
device](#_Ref485031329)

**b.**(Optional.) [Specifying a priority for each member
device](#_Ref463008477)

**c.**[Binding physical interfaces to IRF ports](#_Ref482966719)

Skip these tasks if you configure member
IDs, priorities, domain ID, and IRF physical interfaces in bulk.

**5\.**[Bulk-configuring basic IRF settings for
a member device](#_Ref463014735)

Skip this task if you configure member
IDs, priorities, domain ID, and IRF physical interfaces separately.

**6\.**[Connecting IRF physical interfaces](#_Ref463008485)

**7\.**[Accessing the IRF fabric](#_Ref463008506)

### Assigning a member ID to each IRF member device

#### Restrictions and guidelines

| CAUTION | CAUTION:  An IRF member ID change can invalidate member ID-related settings and cause data loss. Make sure you fully understand its impact on your live network. |
| --- | --- |

 

To create an IRF fabric, you must assign a
unique IRF member ID to each member device.

The new member ID of a device takes effect
at a reboot. After the device reboots, the settings on all member ID-related
physical resources (including common physical network interfaces) are removed,
regardless of whether you have saved the configuration. 

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Assign a member ID to a member device.

irf member member-id renumber new-member-id

The default IRF member ID is 1\.

 

| CAUTION | CAUTION:  An IRF member ID change can invalidate member ID-related settings and cause data loss. Make sure you fully understand its impact on your live network. |
| --- | --- |

 

**3\.**(Optional.) Save the configuration.

save

If you have bound physical interfaces to
IRF ports or assigned member priority, you must perform this step for these
settings to take effect after the reboot.

**4\.**Return to user view.

quit

**5\.**Reboot the device.

reboot \[ slot slot-number ] \[ force ]

### Specifying a priority for each member device

#### About this task

IRF member priority represents the
possibility for a device to be elected the master in an IRF fabric. A larger
priority value indicates a higher priority.

A change to member priority affects the
election result at the next master election, but it does not cause an immediate
master re-election.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Specify a priority for the device.

irf member member-id priority priority

The default IRF member priority is 1\.

### Binding physical interfaces to IRF ports

#### Restrictions and guidelines

Select qualified physical interfaces as IRF
physical interfaces as described in "[Candidate IRF physical interfaces](#_Ref475779403)."

After binding physical interfaces to IRF
ports for the first time, you must use the irf-port-configuration active command to activate the settings on the IRF ports. 

The system activates the IRF port settings
automatically only in the following situations:

·     The configuration file that the device starts
with contains IRF port bindings.

·     You are adding physical interfaces to an IRF
port (in UP state) after an IRF fabric is formed.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter interface view or interface range
view.

¡     Enter
interface view.

interface interface-type interface-number

¡     Enter
interface range view. Choose one of the following commands:

interface range { interface-type
interface-number \[ to interface-type
interface-number ] } \&\<1-24\>

interface range name name \[ interface { interface-type interface-number \[ to interface-type interface-number ] } \&\<1-24\> ]

To shut down a range of IRF physical
interfaces, enter interface range view.

To shut down one IRF physical interface,
enter its interface view.

**3\.**Shut down the physical interfaces.

shutdown

By default, a physical interface is not
administratively down. 

**4\.**Return to system view.

quit

**5\.**Enter IRF port view.

irf-port member-id/irf-port-number 

**6\.**Bind each physical interface to the IRF port.

port group interface interface-type
interface-number

By default, no physical interfaces are
bound to an IRF port.

Repeat this step to assign multiple
physical interfaces to the IRF port.

**7\.**Return to system view.

quit 

**8\.**Enter interface view or interface range
view.

¡     Enter
interface view.

interface interface-type interface-number

¡     Enter
interface range view. Choose one of the following commands:

interface range { interface-type
interface-number \[ to interface-type
interface-number ] } \&\<1-24\>

interface range name name \[ interface { interface-type interface-number \[ to interface-type interface-number ] } \&\<1-24\> ]

**9\.**Bring up the physical interfaces.

undo shutdown 

**10\.**Return to system view.

quit 

**11\.**Save the configuration.

save 

Activating IRF port configurations causes
IRF merge and reboot. To avoid data loss, save the running configuration to the
startup configuration file before you perform the operation.

**12\.**Activate the IRF port settings.

irf-port-configuration active 

### Bulk-configuring basic IRF settings for a member device

#### About this task

Use the easy IRF feature to bulk-configure
basic IRF settings for a member device, including the member ID, domain ID,
priority, and IRF port bindings.

The easy IRF feature provides the following
configuration methods:

·     Interactive method—Enter the easy-irf command
without parameters. The system will guide you to set the parameters step by
step.

·     Non-interactive method—Enter the easy-irf command with
parameters.

As a best practice, use the interactive
method if you are new to IRF.

#### Restrictions and guidelines

| CAUTION | CAUTION:  ·     Use caution when you change the member ID of an IRF member device. An IRF member ID uniquely identifies a device in an IRF fabric. An IRF member ID change can invalidate member ID-related settings, including interface and file path settings, and cause data loss. Make sure you fully understand its impact on the live network.  ·     The member device reboots immediately after you specify a new member ID for it. Make sure you are aware of the impact of this operation on the network. |
| --- | --- |

 

If you execute the easy-irf command multiple times, the following settings take effect:

·     The most recent settings for the member ID,
domain ID, and priority.

·     IRF port bindings added through repeated executions
of the command. To remove an IRF physical interface from an IRF port, you must
use the undo port group interface command in IRF
port view. 

If you specify IRF physical interfaces by
using the interactive method, you must also follow these restrictions and
guidelines:

·     Do not enter spaces between the interface type
and interface number.

·     Use a comma (,) to separate two physical
interfaces. No spaces are allowed between interfaces.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Bulk-configure basic IRF settings for the
device.

easy-irf \[ member member-id \[ renumber new-member-id ] domain domain-id \[ priority priority ] \[ irf-port1 interface-list1 ] \[ irf-port2 interface-list2 ] ]

Make sure the new member ID is unique in the
IRF fabric to which the device will be added.

### Connecting IRF physical interfaces

Follow the restrictions in "[IRF port connection](#_Ref463891744)" to connect IRF physical interfaces as well as based on the
topology and cabling scheme. The devices perform master election. The member
devices that fail the master election automatically reboot to form an IRF
fabric with the master device.

### Accessing the IRF fabric

The IRF fabric appears as one device after
it is formed. You configure and manage all IRF members at the CLI of the
master. All settings you have made are automatically propagated to the IRF
members.

The following methods are available for accessing
an IRF fabric:

·     Local login—Log in through the console port of any member device. 

·     Remote login—Log in at a Layer 3 interface on any member device by using methods
including Telnet and SNMP.

When you log in to an IRF fabric, you are
placed at the CLI of the master, regardless of at which member device you are
logged in.

For more information, see login configuration
in Fundamentals Configuration Guide.

## Configuring MAD

### Restrictions and guidelines for MAD configuration

#### MAD mechanism compatibility

As a best practice, configure a minimum of
one MAD mechanism on an IRF fabric for prompt IRF split detection. Because MAD
mechanisms use different collision handling processes, follow these
restrictions and guidelines when you configure multiple MAD mechanisms on an
IRF fabric:

·     Do not configure LACP MAD together with ARP MAD or
ND MAD.

·     Do not configure BFD MAD together with ARP MAD
or ND MAD.

#### Assigning IRF domain IDs

An IRF fabric has only one IRF domain ID. You
can change the IRF domain ID by using the following commands: irf domain, mad enable, mad arp enable, or mad nd enable. The IRF
domain IDs configured by using these commands overwrite each other.

If LACP MAD, ARP MAD, or ND MAD runs
between two IRF fabrics, assign each fabric a unique IRF domain ID. (For BFD
MAD, this task is optional.)

#### Actions on interfaces shut down by MAD

To prevent a multi-active collision from
causing network issues, avoid using the undo shutdown command to bring up the interfaces shut down by a MAD mechanism on a
Recovery-state IRF fabric.

### Configuring LACP MAD

**1\.**Enter system view.

system-view

**2\.**Assign a domain ID to the IRF fabric.

irf domain domain-id

The default IRF domain ID is 0\.

 

| CAUTION | CAUTION:  Changing the IRF domain ID of an IRF member device will remove that member device from the IRF fabric. This member device will be unable to exchange IRF protocol packets with the remaining member devices in the IRF fabric. |
| --- | --- |

 

**3\.**Create an aggregate interface and enter
aggregate interface view.

¡     Enter
Layer 2 aggregate interface view.

interface bridge-aggregation interface-number

¡     Enter Layer 3 aggregate interface view.

interface route-aggregation interface-number

Perform this step also on the
intermediate device.

**4\.**Configure the aggregation group to operate
in dynamic aggregation mode.

link-aggregation mode dynamic

By default, an aggregation group operates
in static aggregation mode.

LACP MAD takes effect only on dynamic
aggregate interfaces.

Perform this step also on the
intermediate device.

**5\.**Enable LACP MAD.

mad enable

By default, LACP MAD is disabled.

**6\.**Return to system view.

quit

**7\.**Enter Ethernet interface view or interface
range view.

¡     Enter
Ethernet interface view.

interface interface-type interface-number

¡     Enter
interface range view. Choose one of the following commands:

interface range { interface-type
interface-number \[ to interface-type
interface-number ] } \&\<1-24\>

interface range name name \[ interface { interface-type interface-number \[ to interface-type interface-number ] } \&\<1-24\> ]

To assign a range of ports to the
aggregation group, enter interface range view.

To assign one port to the aggregation
group, enter Ethernet interface view.

**8\.**Assign the Ethernet port or the range of
Ethernet ports to the specified aggregation group.

port link-aggregation group group-id

Multichassis link aggregation is allowed.

Perform this step
also on the intermediate device.

### Configuring BFD MAD

#### Restrictions and guidelines for configuring BFD MAD

As a best practice, use the following
procedure to set up BFD MAD:

**1\.**Choose a BFD MAD
link scheme as described in "[BFD MAD](#_Ref393269498)."

**2\.**Configure BFD MAD.

**3\.**Connect the BFD MAD
links.

When you configure BFD MAD on a VLAN
interface, follow these restrictions and guidelines:

 

| Category | Restrictions and guidelines |
| --- | --- |
| BFD MAD VLAN | ·     Do not enable BFD MAD on VLAN-interface 1\.  ·     If you are using an intermediate device, perform the following tasks:  ¡     On the IRF fabric and the intermediate device, create a VLAN for BFD MAD.  ¡     On the IRF fabric and the intermediate device, assign the ports of BFD MAD links to the BFD MAD VLAN.  ¡     On the IRF fabric, create a VLAN interface for the BFD MAD VLAN.  ·     Make sure the IRF fabrics on the network use different BFD MAD VLANs.  ·     Make sure the BFD MAD VLAN contains only ports on the BFD MAD links. Exclude a port from the BFD MAD VLAN if that port is not on a BFD MAD link. If you have assigned that port to all VLANs by using the port trunk permit vlan all command, use the undo port trunk permit command to exclude that port from the BFD MAD VLAN.  ·     When VSI interfaces exist, follow these restrictions and guidelines:  ¡     On the S6520X-SI, S6520-SI, and MS4600 switch series, VLAN interfaces numbered in the range of 3581 to 4092 cannot be used for BFD MAD.  ¡     On the S6520X-EI switch series, VLAN interfaces numbered in the range of 3069 to 4092 cannot be used for BFD MAD.  ¡     On the S6520X-HI, S5560X-HI, and S5000-EI switch series, VLAN interfaces numbered in the range of 2045 to 4092 cannot be used for BFD MAD.  · || BFD MAD VLAN and feature compatibility | Do not use the BFD MAD VLAN and its member ports for any purpose other than configuring BFD MAD.  ·     Use only the mad bfd enable and mad ip address commands on the BFD MAD-enabled VLAN interface. If you configure other features, both BFD MAD and other features on the interface might run incorrectly.  ·     Disable the spanning tree feature on any Layer 2 Ethernet ports in the BFD MAD VLAN. The MAD feature is mutually exclusive with the spanning tree feature. || MAD IP address | ·     To avoid network issues, only use the mad ip address command to configure IP addresses on the BFD MAD-enabled VLAN interface. Do not configure an IP address by using the ip address command or configure a VRRP virtual address on the BFD MAD-enabled VLAN interface.  ·     Make sure all the MAD IP addresses are on the same subnet. |



‌

When you configure BFD MAD on a Layer 3
aggregate interface, follow these restrictions and guidelines:

 

| Category | Restrictions and guidelines |
| --- | --- |
| BFD MAD-enabled Layer 3 aggregate interface | ·     Make sure the Layer 3 aggregate interface operates in static aggregation mode.  ·     Make sure the member ports in the aggregation group do not exceed the maximum number of Selected ports allowed for an aggregation group. If the number of member ports exceeds the maximum number of Selected ports, some member ports cannot become Selected. BFD MAD will be unable to work correctly and its state will change to Faulty. || BFD MAD VLAN | If a switch is used as the intermediate device, use a VLAN as the BFD MAD VLAN on the intermediate device.  ·     If the intermediate device acts as a BFD MAD intermediate device for multiple IRF fabrics, assign different BFD MAD VLANs to the IRF fabrics.  ·     Assign the ports on the BFD MAD links for the same IRF fabric to the same VLAN. Do not assign the ports to an aggregate interface. If the ports are hybrid ports, make sure these ports are untagged members of their PVIDs.  ·     Do not use the BFD MAD VLAN on the intermediate device for any purposes other than BFD MAD.  ·     Make sure the BFD MAD VLAN on the intermediate device contains only ports on the BFD MAD links. Exclude a port from the BFD MAD VLAN if that port is not on a BFD MAD link. If you have assigned that port to all VLANs by using the port trunk permit vlan all command, use the undo port trunk permit command to exclude that port from the BFD MAD VLAN. || BFD MAD-enabled Layer 3 aggregate interface and feature compatibility | Use only the mad bfd enable and mad ip address commands on the BFD MAD-enabled interface. If you configure other features, both BFD MAD and other features on the interface might run incorrectly. || MAD IP address | ·     To avoid network issues, only use the mad ip address command to configure IP addresses on the BFD MAD-enabled interface. Do not configure an IP address by using the ip address command or configure a VRRP virtual address on the BFD MAD-enabled interface.  ·     Make sure all the MAD IP addresses are on the same subnet. |




‌

When you configure BFD MAD on a management
Ethernet port, follow these restrictions and guidelines:

 

| Category | Restrictions and guidelines |
| --- | --- |
| Management Ethernet ports for BFD MAD | Connect a management Ethernet port on each IRF member device to the common Ethernet ports on the intermediate device. || BFD MAD VLAN | ·     On the intermediate device, create a VLAN for BFD MAD, and assign the ports used for BFD MAD to the VLAN. On the IRF fabric, you do not need to assign the management Ethernet ports to the VLAN.  ·     Make sure the IRF fabrics on the network use different BFD MAD VLANs.  ·     Make sure the BFD MAD VLAN on the intermediate device contains only ports on the BFD MAD links. || MAD IP address | ·     Use the mad ip address command instead of the ip address command to configure MAD IP addresses on the BFD MAD-enabled management Ethernet ports.  ·     Make sure all the MAD IP addresses are on the same subnet. |



‌

#### Configuring BFD MAD on a VLAN interface

**1\.**Enter system view.

system-view 

**2\.**(Optional.) Assign a domain ID to the IRF
fabric.

irf domain domain-id 

By default, the domain ID of an IRF
fabric is 0\.

 

| CAUTION | CAUTION:  Changing the IRF domain ID of an IRF member device will remove that member device from the IRF fabric. This member device will be unable to exchange IRF protocol packets with the remaining member devices in the IRF fabric. |
| --- | --- |

 

**3\.**Create a VLAN dedicated to BFD MAD.

vlan vlan-id 

By default, only VLAN 1 exists.

Do not enable BFD MAD on VLAN-interface
1\.

Perform this step also on the
intermediate device (if any).

**4\.**Return to system view.

quit 

**5\.**Enter Ethernet interface view or interface
range view.

¡     Enter
Ethernet interface view.

interface interface-type interface-number

¡     Enter
interface range view. Choose one of the following commands:

interface range { interface-type
interface-number \[ to interface-type
interface-number ] } \&\<1-24\>

interface range name name \[ interface { interface-type interface-number \[ to interface-type interface-number ] } \&\<1-24\> ]

To assign a range of ports to the BFD MAD
VLAN, enter interface range view.

To assign one port to the BFD MAD VLAN,
enter Ethernet interface view.

**6\.**Assign the port or the range of ports to the
BFD MAD VLAN.

¡     Assign
the ports to the VLAN as access ports.

port access vlan vlan-id 

¡     Assign the ports to the VLAN as trunk ports.

port trunk permit vlan vlan-id

¡     Assign the ports to the VLAN as hybrid ports.

port hybrid vlan vlan-id { tagged \| untagged }

The link type of BFD MAD ports can be
access, trunk, or hybrid.

The default link type of a port is
access.

Perform this step also on the
intermediate device (if any).

**7\.**Return to system view.

quit 

**8\.**Enter VLAN interface view.

interface vlan-interface
vlan-interface-id 

**9\.**Enable BFD MAD.

mad bfd enable 

By default, BFD MAD is disabled.

**10\.**Assign a MAD IP address to a member device
on the VLAN interface.

mad ip address ip-address { mask \| mask-length } member member-id 

By default, no MAD IP addresses are
configured on any VLAN interfaces.

Repeat this step to assign a MAD IP
address to each member device on the VLAN interface.

#### Configuring BFD MAD on a Layer 3 aggregate interface

**1\.**Enter system view.

system-view

**2\.**(Optional.) Assign a domain ID to the IRF
fabric.

irf domain domain-id

By default, the domain ID of an IRF
fabric is 0\.

 

| CAUTION | CAUTION:  Changing the IRF domain ID of an IRF member device will remove that member device from the IRF fabric. This member device will be unable to exchange IRF protocol packets with the remaining member devices in the IRF fabric. |
| --- | --- |

 

**3\.**Create a Layer 3 aggregate interface for BFD
MAD.

interface route-aggregation interface-number

**4\.**Return to system view.

quit

**5\.**Enter interface view or interface range
view.

¡     Enter
Ethernet interface view.

interface interface-type interface-number

¡     Enter
interface range view. Choose one of the following commands:

interface range { interface-type
interface-number \[ to interface-type
interface-number ] } \&\<1-24\>

interface range name name \[ interface { interface-type interface-number \[ to interface-type interface-number ] } \&\<1-24\> ]

To assign a range of ports to the aggregation
group for the aggregate interface, enter interface range view.

To assign one port to the aggregation
group for the aggregate interface, enter Ethernet interface view.

**6\.**Assign the port or the range of ports to the
aggregation group for the aggregate interface.

port link-aggregation group number

**7\.**Return to system view.

quit

**8\.**Enter Layer 3 aggregate interface view.

interface route-aggregation interface-number

**9\.**Enable BFD MAD.

mad bfd enable

By default, BFD MAD is disabled.

**10\.**Assign a MAD IP address to a member device
on the Layer 3 aggregate interface.

mad ip address ip-address { mask \| mask-length } member member-id

By default, no MAD IP addresses are
configured on aggregate interfaces.

Repeat this step to assign a MAD IP
address to each member device on the aggregate interface.

#### Configuring BFD MAD on a management Ethernet port

**1\.**Enter system view.

system-view 

**2\.**(Optional.) Assign a domain ID to the IRF
fabric.

irf domain domain-id 

By default, the domain ID of an IRF
fabric is 0\.

 

| CAUTION | CAUTION:  Changing the IRF domain ID of an IRF member device will remove that member device from the IRF fabric. This member device will be unable to exchange IRF protocol packets with the remaining member devices in the IRF fabric. |
| --- | --- |

 

**3\.**Enter management Ethernet interface view.

interface m-gigabitethernet interface-number 

Of all management Ethernet ports on an
IRF fabric, only the master's management Ethernet port is accessible.

**4\.**Enable BFD MAD.

mad bfd enable 

By default, BFD MAD is disabled.

**5\.**Assign a MAD IP address to each member
device.

mad ip address ip-address { mask \| mask-length } member member-id 

By default, no MAD IP addresses are
configured.

### Configuring ARP MAD

#### Restrictions and guidelines for configuring ARP MAD

As a best practice, use the following
procedure to set up ARP MAD:

**1\.**Choose an ARP MAD link scheme as described
in "[ARP MAD](#_Ref420056425)."

**2\.**Configure ARP MAD.

**3\.**Connect the ARP MAD links.

When you configure ARP MAD on a VLAN
interface, follow these restrictions and guidelines:

 

| Category | Restrictions and guidelines |
| --- | --- |
| ARP MAD VLAN | ·     Do not enable ARP MAD on VLAN-interface 1\.  ·     If you are using an intermediate device, perform the following tasks:  ¡     On the IRF fabric and the intermediate device, create a VLAN for ARP MAD.  ¡     On the IRF fabric and the intermediate device, assign the ports of ARP MAD links to the ARP MAD VLAN.  ¡     On the IRF fabric, create a VLAN interface for the ARP MAD VLAN.  ·     Do not use the ARP MAD VLAN for any other purposes. || ARP MAD and feature configuration | If an intermediate device is used, make sure the following requirements are met:  ·     Run the spanning tree feature between the IRF fabric and the intermediate device to ensure that there is only one ARP MAD link in forwarding state. For more information about the spanning tree feature and its configuration, see Layer 2—LAN Switching Configuration Guide.  ·     Enable the IRF fabric to change its bridge MAC address as soon as the address owner leaves.  ·     If the intermediate device is also an IRF fabric, assign the two IRF fabrics different domain IDs for correct split detection. |


‌

When you configure ARP MAD on a management
Ethernet port, follow these restrictions and guidelines:

 

| Category | Restrictions and guidelines |
| --- | --- |
| Management Ethernet ports for ARP MAD | Connect a management Ethernet port on each member device to the common Ethernet ports on the intermediate device. || ARP MAD VLAN | On the intermediate device, create a VLAN for ARP MAD, and assign the ports used for ARP MAD to the VLAN. On the IRF fabric, you do not need to assign the management Ethernet ports to the VLAN. || ARP MAD and feature configuration | ·     Enable the IRF fabric to change its bridge MAC address as soon as the address owner leaves.  ·     If the intermediate device is also an IRF fabric, assign the two IRF fabrics different domain IDs for correct split detection. |



‌

#### Configuring ARP MAD on a VLAN interface

**1\.**Enter system view.

system-view 

**2\.**Assign a domain ID to the IRF fabric.

irf domain domain-id 

The default IRF domain ID is 0\.

 

| CAUTION | CAUTION:  Changing the IRF domain ID of an IRF member device will remove that member device from the IRF fabric. This member device will be unable to exchange IRF protocol packets with the remaining member devices in the IRF fabric. |
| --- | --- |

 

**3\.**Configure the IRF bridge MAC address to
change as soon as the address owner leaves.

undo irf mac-address persistent 

By default, the IRF bridge MAC address remains
unchanged for 6 minutes after the address owner leaves.

 

| CAUTION | CAUTION:  IRF bridge MAC address change will cause transient traffic disruption. |
| --- | --- |

 

**4\.**Create a VLAN dedicated to ARP MAD.

vlan vlan-id 

By default, only VLAN 1 exists.

Do not configure ARP MAD on
VLAN-interface 1\.

Perform this task also on the
intermediate device (if any).

**5\.**Return to system view.

quit 

**6\.**Enter Ethernet interface view or interface
range view.

¡     Enter
Ethernet interface view.

interface interface-type interface-number

¡     Enter
interface range view. Choose one of the following commands:

interface range { interface-type
interface-number \[ to interface-type
interface-number ] } \&\<1-24\>

interface range name name \[ interface { interface-type interface-number \[ to interface-type interface-number ] } \&\<1-24\> ]

To assign a range of ports to the ARP MAD
VLAN, enter interface range view.

To assign one port to the ARP MAD VLAN,
enter Ethernet interface view.

**7\.**Assign the port or the range of ports to the
ARP MAD VLAN.

¡     Assign the ports to the VLAN as access ports.

port access vlan vlan-id

¡     Assign the ports to the VLAN as trunk ports.

port trunk permit vlan vlan-id

¡     Assign the ports to the VLAN as hybrid ports.

port hybrid vlan vlan-id { tagged \| untagged }

The link type of ARP MAD ports can be
access, trunk, or hybrid.

The default link type of a port is access.

Perform this task also on the
intermediate device (if any).

**8\.**Return to system view.

quit 

**9\.**Enter VLAN interface view.

interface vlan-interface vlan-interface-id 

**10\.**Assign the interface an IP address.

ip address ip-address { mask \| mask-length }

By default, no IP addresses are assigned
to any VLAN interfaces.

**11\.**Enable ARP MAD.

mad arp enable

By default, ARP MAD is disabled.

#### Configuring ARP MAD on a management Ethernet port

**1\.**Enter system view.

system-view 

**2\.**Assign a domain ID to the IRF fabric.

irf domain domain-id 

The default IRF domain ID is 0\.

 

| CAUTION | CAUTION:  Changing the IRF domain ID of an IRF member device will remove that member device from the IRF fabric. This member device will be unable to exchange IRF protocol packets with the remaining member devices in the IRF fabric. |
| --- | --- |

 

**3\.**Configure the IRF bridge MAC address to
change as soon as the address owner leaves.

undo irf mac-address persistent 

By default, the IRF bridge MAC address
remains unchanged for 6 minutes after the address owner leaves.

 

| CAUTION | CAUTION:  IRF bridge MAC address change will cause transient traffic disruption. |
| --- | --- |

 

**4\.**Enter management Ethernet interface view.

interface m-gigabitethernet interface-number 

Of all management Ethernet ports on an
IRF fabric, only the master's management Ethernet port is accessible.

**5\.**Assign an IP address to the management
Ethernet port.

ip address ip-address { mask \| mask-length }

By default, no IP addresses are
configured.

**6\.**Enable ARP MAD.

mad arp enable

By default, ARP MAD is disabled.

### Configuring ND MAD

#### Restrictions and guidelines for configuring ND MAD

As a best practice, use the following
procedure to set up ND MAD:

**1\.**Choose an ND MAD link scheme as described in
"[ND MAD](#_Ref393269512)."

**2\.**Configure ND MAD.

**3\.**Connect the ND MAD links.

When you configure ND MAD on a VLAN
interface, follow these restrictions and guidelines:

 

| Category | Restrictions and guidelines |
| --- | --- |
| ND MAD VLAN | ·     Do not enable ND MAD on VLAN-interface 1\.  ·     If you are using an intermediate device, perform the following tasks:  ¡     On the IRF fabric and the intermediate device, create a VLAN for ND MAD.  ¡     On the IRF fabric and the intermediate device, assign the ports of ND MAD links to the ND MAD VLAN.  ¡     On the IRF fabric, create a VLAN interface for the ND MAD VLAN.  ·     Do not use the ND MAD VLAN for any other purposes. || ND MAD and feature configuration | If an intermediate device is used, make sure the following requirements are met:  ·     Run the spanning tree feature between the IRF fabric and the intermediate device to ensure that there is only one ND MAD link in forwarding state. For more information about the spanning tree feature and its configuration, see Layer 2—LAN Switching Configuration Guide.  ·     Enable the IRF fabric to change its bridge MAC address as soon as the address owner leaves.  ·     If the intermediate device is also an IRF fabric, assign the two IRF fabrics different domain IDs for correct split detection. |


‌

When you configure ND MAD on a management
Ethernet port, follow these restrictions and guidelines:

 

| Category | Restrictions and guidelines |
| --- | --- |
| Management Ethernet ports for ND MAD | Connect a management Ethernet port on each member device to the common Ethernet ports on the intermediate device. || ND MAD VLAN | On the intermediate device, create a VLAN for ND MAD, and assign the ports used for ND MAD to the VLAN. On the IRF fabric, you do not need to assign the management Ethernet ports to the VLAN. || ND MAD and feature configuration | ·     Enable the IRF fabric to change its bridge MAC address as soon as the address owner leaves.  ·     If the intermediate device is also an IRF fabric, assign the two IRF fabrics different domain IDs for correct split detection. |



‌

#### Configuring ND MAD on a VLAN interface

**1\.**Enter system view.

system-view

**2\.**Assign a domain ID to the IRF fabric.

irf domain domain-id

The default IRF domain ID is 0\.

 

| CAUTION | CAUTION:  Changing the IRF domain ID of an IRF member device will remove that member device from the IRF fabric. This member device will be unable to exchange IRF protocol packets with the remaining member devices in the IRF fabric. |
| --- | --- |

 

**3\.**Configure the IRF bridge MAC address to
change as soon as the address owner leaves.

undo irf mac-address persistent

By default, the IRF bridge MAC address
remains unchanged for 6 minutes after the address owner leaves the fabric.

 

| CAUTION | CAUTION:  IRF bridge MAC address change will cause transient traffic disruption. |
| --- | --- |

 

**4\.**Create a VLAN dedicated to ND MAD.

vlan vlan-id

By default, only VLAN 1 exists.

Do not configure ND MAD on VLAN-interface
1\.

Perform this task also on the
intermediate device (if any).

**5\.**Return to system view.

quit

**6\.**Enter Ethernet interface view or interface
range view.

¡     Enter
Ethernet interface view.

interface interface-type interface-number

¡     Enter
interface range view. Choose one of the following commands:

interface range { interface-type
interface-number \[ to interface-type
interface-number ] } \&\<1-24\>

interface range name name \[ interface { interface-type interface-number \[ to interface-type interface-number ] } \&\<1-24\> ]

To assign a range of ports to the ND MAD
VLAN, enter interface range view.

To assign one port to the ND MAD VLAN,
enter Ethernet interface view.

**7\.**Assign the port or the range of ports to the
ND MAD VLAN.

¡     Assign
the ports to the VLAN as access ports.

port access vlan vlan-id

¡     Assign the ports to the VLAN as trunk ports.

port trunk permit vlan vlan-id

¡     Assign the ports to the VLAN as hybrid ports.

port hybrid vlan vlan-id { tagged \| untagged }

The link type of ND MAD ports can be
access, trunk, or hybrid.

The default link type of a port is
access.

Perform this task also on the
intermediate device (if any).

**8\.**Return to system view.

quit

**9\.**Enter VLAN interface view.

interface vlan-interface interface-number

**10\.**Assign the interface an IPv6 address.

ipv6 address {
ipv6-address/prefix-length \| ipv6-address
prefix-length }

By default, no IPv6 addresses are
assigned to a VLAN interface.

**11\.**Enable ND MAD.

mad nd enable

By default, ND MAD is disabled.

#### Configuring ND MAD on a management Ethernet port

**1\.**Enter system view.

system-view 

**2\.**Assign a domain ID to the IRF fabric.

irf domain domain-id 

The default IRF domain ID is 0\.

 

| CAUTION | CAUTION:  Changing the IRF domain ID of an IRF member device will remove that member device from the IRF fabric. This member device will be unable to exchange IRF protocol packets with the remaining member devices in the IRF fabric. |
| --- | --- |

 

**3\.**Configure the IRF bridge MAC address to
change as soon as the address owner leaves.

undo irf mac-address persistent 

By default, the IRF bridge MAC address
remains unchanged for 6 minutes after the address owner leaves the fabric.

 

| CAUTION | CAUTION:  IRF bridge MAC address change will cause transient traffic disruption. |
| --- | --- |

 

**4\.**Enter management Ethernet interface view.

interface m-gigabitethernet interface-number 

Of all management Ethernet ports on an
IRF fabric, only the master's management Ethernet port is accessible.

**5\.**Assign an IPv6 address to the management
Ethernet port.

ipv6 address {
ipv6-address/pre-length \| ipv6 address pre-length
}

By default, no IPv6 addresses are
assigned to a management Ethernet port.

**6\.**Enable ND MAD.

mad nd enable

By default, ND MAD is disabled.

### Excluding interfaces from the shutdown action upon detection of multi-active collision

#### About this task

When an IRF fabric transits to the Recovery
state, the system automatically excludes the following network interfaces from
being shut down:

·     IRF physical interfaces.

·     Interfaces used for BFD MAD.

·     Member interfaces of an aggregate interface if
the aggregate interface is excluded from being shut down.

You can exclude an interface from the
shutdown action for management or other special purposes. For example:

·     Exclude a port from the shutdown action so you
can Telnet to the port for managing the device.

·     Exclude a VLAN interface and its Layer 2 ports
from the shutdown action so you can log in through the VLAN interface.

#### Restrictions and guidelines

If the Layer 2 ports of a VLAN interface
are distributed on multiple member devices, the exclusion operation might introduce
IP collision risks. The VLAN interface might be up on both active and inactive
IRF fabrics.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Configure an interface to not shut down when
the IRF fabric transits to the Recovery state.

mad exclude interface interface-type
interface-number

By default, all network interfaces on a
Recovery-state IRF fabric are shut down, except for the network interfaces automatically
excluded by the system.

### Recovering an IRF fabric

#### About this task

If the active IRF fabric fails before the
IRF link is recovered, perform this task on the inactive IRF fabric to recover
the inactive IRF fabric for traffic forwarding. The manual recovery operation brings
up all interfaces that were shut down by MAD on the inactive IRF fabric.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Recover the inactive IRF fabric.

mad restore

## Optimizing IRF settings for an IRF fabric

### Configuring a member device description

**1\.**Enter system view.

system-view 

**2\.**Configure a description for a member device.

irf member member-id description text 

By default, no member device description
is configured.

### Configuring IRF bridge MAC address settings

#### About this task

The bridge MAC address of a system must be
unique on a switched LAN. IRF bridge MAC address identifies an IRF fabric by
Layer 2 protocols (for example, LACP) on a switched LAN.

By default, an IRF fabric uses the bridge
MAC address of the master as the IRF bridge MAC address. After the master
leaves, the IRF bridge MAC address persists for a period of time or permanently
depending on the IRF bridge MAC persistence setting. When the IRF bridge MAC
persistence timer expires, the IRF fabric uses the bridge MAC address of the
current master as the IRF bridge MAC address.

In special occasions that require a fixed
special IRF bridge MAC address, you can specify that MAC address as the IRF
bridge MAC address. For example, when you replace an IRF fabric as a whole, you
can configure the new IRF fabric with the IRF bridge MAC address of the
existing IRF fabric before the replacement to minimize service interruption.

The IRF bridge MAC persistence setting does
not take effect on the manually specified IRF bridge MAC address.

If IRF fabric merge occurs, IRF determines
the IRF bridge MAC address of the merged IRF fabric as follows:

**1\.**When IRF fabrics merge, IRF ignores the IRF
bridge MAC addresses and checks
the bridge MAC address of each member device in the IRF fabrics. IRF merge
fails if any two member devices have the same bridge MAC address.

**2\.**After IRF fabrics merge, the merged IRF
fabric uses the bridge MAC address of the merging IRF fabric that won the
master election as the IRF bridge MAC address.

#### Restrictions and guidelines for IRF bridge MAC address configuration

| CAUTION | CAUTION:  Bridge MAC address change will cause transient traffic disruption. |
| --- | --- |

‌

When you configure IRF bridge MAC persistence,
follow these restrictions and guidelines:

·     If ARP MAD or ND MAD is used with the spanning
tree feature, you must disable IRF bridge MAC persistence by using the undo irf
mac-address persistent command. In addition, do
not specify a MAC address as the IRF bridge MAC address. 

·     If the IRF fabric has multichassis aggregate links, do not use the undo irf
mac-address persistent command. Use of this
command might cause traffic disruption.

When you specify a MAC address as the IRF
bridge MAC address, follow these restrictions and guidelines:

·     Do not specify any of the following MAC
addresses as the IRF bridge MAC address:

¡     Static
MAC addresses.

¡     Dynamic
MAC addresses.

¡     Blackhole
MAC addresses.

¡     Multiport
unicast MAC addresses.

·     IRF reserves the IRF bridge MAC address and its
subsequent higher 127 MAC addresses. These MAC addresses cannot be configured
as any types of MAC addresses listed above.

#### Configuring IRF bridge MAC persistence

**1\.**Enter system view.

system-view

**2\.**Configure IRF bridge MAC persistence.

¡     Retain the bridge MAC address permanently even if the address owner has
left the fabric.

irf mac-address persistent always

¡     Retain the bridge MAC address for 6 minutes after the address owner
leaves the fabric. 

irf mac-address persistent timer

¡     Change the bridge MAC address as soon as the address owner leaves
the fabric.

undo irf mac-address persistent

By default, the IRF bridge MAC address
remains unchanged for 6 minutes after the address owner leaves the fabric. 

The irf mac-address persistent timer command avoids unnecessary bridge MAC address changes caused by
device reboot, transient link failure, or purposeful link disconnection.

#### Specifying a MAC address as the IRF bridge MAC address

**1\.**Enter system view.

system-view

**2\.**Specify a MAC address as the IRF bridge MAC
address.

irf mac-address mac-address

By default, an IRF fabric uses the bridge
MAC address of the master as the IRF bridge MAC address.

 

| CAUTION | CAUTION:  IRF bridge MAC address change will cause transient traffic disruption. |
| --- | --- |

 

If an IRF fabric splits after you
configure the IRF bridge MAC address, both the split IRF fabrics use the
configured bridge MAC address as the IRF bridge MAC address.

### Enabling software auto-update for software image synchronization

#### About this task

The software auto-update feature automatically
synchronizes the current software images of the master to devices that are
attempting to join the IRF fabric.

To join an IRF fabric, a device must use
the same software images as the master in the fabric.

When you add a device to the IRF fabric,
software auto-update compares the startup software images of the device with the
current software images of the IRF master. If the two sets of images are
different, the device automatically performs the following operations:

**1\.**Downloads the current software images of the
master.

**2\.**Sets the downloaded images as its main
startup software images.

**3\.**Reboots with the new software images to
rejoin the IRF fabric.

You must manually update the new device
with the software images running on the IRF fabric if software auto-update is
disabled.

#### Restrictions and guidelines

To ensure a successful software auto-update
in a multi-user environment, prevent anyone from rebooting member devices
during the auto-update process. To inform administrators of the auto-update
status, configure the information center to output the status messages to
configuration terminals (see Network Management and
Monitoring Configuration Guide).

Make sure the device you are adding to the
IRF fabric has sufficient storage space for the new software images.

If sufficient storage space is not available,
the device automatically deletes the current software images. If the reclaimed
space is still insufficient, the device cannot complete the auto-update. You
must reboot the device, and then access the Boot menu to delete files.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enable software auto-update.

irf auto-update enable

By default, software auto-update is
enabled.

### Setting the IRF link down report delay

#### About this task

To prevent frequent IRF splits and merges during
link flapping, configure the IRF ports to delay reporting link down events.

An IRF port does not report a link down
event to the IRF fabric immediately after its link changes from up to down. If
the IRF link state is still down when the delay is reached, the port reports
the change to the IRF fabric.

IRF ports do not delay link up events. They
report the link up event immediately after the IRF link comes up.

#### Restrictions and guidelines

Make sure the IRF link down report delay is
shorter than the heartbeat or hello timeout settings of upper-layer protocols (for
example, CFD and OSPF). If the report delay is longer than the timeout setting
of a protocol, unnecessary recalculations might occur.

Set the delay to 0 seconds in the following
situations:

·     The IRF fabric requires a fast
master/subordinate or IRF link switchover.

·     The RRPP, BFD, or GR feature is used.

·     You want to shut down an IRF physical interface
or reboot an IRF member device. (After you complete the operation, reconfigure the
delay depending on the network condition.)

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Set the IRF link down report delay.

irf link-delay interval

The default IRF link down report delay is
4 seconds. 

### Removing an interface module that has IRF physical interfaces

To remove an interface module that provides
IRF physical interfaces:

**1\.**Perform one of the following tasks to eliminate
temporary packet loss:

¡     Remove
cables from the IRF physical interfaces on the module.

¡     Shut
down the IRF physical interfaces on the module by using the shutdown command.

**2\.**Remove the module.

### Replacing an interface module that has IRF physical interfaces

#### Replacing the old module with a different model replacement module

**1\.**Shut down the IRF physical interfaces on the
old module by using the shutdown command.

**2\.**Remove the IRF port bindings that contain
the physical interfaces.

**3\.**Remove the old module, and then install the
replacement module.

**4\.**Verify that the replacement module has been
correctly installed by using the display device command.

**5\.**Reconfigure the IRF port bindings, as
described in "[Binding physical interfaces to IRF ports](#_Ref463008489)."

**6\.**Activate the IRF port settings by using the irf-port-configuration
active command. 

You can skip this step if the IRF port is
in UP state when you add bindings.

#### Replacing the old module with the same model replacement module

**1\.**Shut down the IRF physical interfaces on the
old module by using the shutdown command.

**2\.**Remove the old module, and then install the
replacement module.

**3\.**Verify that the replacement module has been
correctly installed by using the display device command.

Bring up the physical interfaces by using
the undo
shutdown command after the interface module
completes startup.

## Display and maintenance commands for IRF

Execute display commands
in any view.

 

| Task | Command |
| --- | --- |
| Display information about all IRF members. | display irf || Display the IRF fabric topology. | display irf topology || Display IRF link information. | display irf link || Display IRF configuration. | display irf configuration || Display MAD configuration. | display mad \[ verbose ] |





‌

## IRF configuration examples

The IRF configuration examples show how to
set up IRF fabrics that use different MAD mechanisms.

### Example: Configuring an IRF fabric with LACP MAD enabled on an aggregate interface

#### Network configuration

As shown in [Figure 14](#_Ref482458077), set
up a four-chassis IRF fabric at the access layer of the enterprise network.

Configure LACP MAD on the multichassis aggregation
to Device E, which supports extended LACP.

Figure 14 Network diagram

 ![](https://resource.h3c.com/en/202412/25/20241225_12647063_x_Img_x_png_31_2328684_294551_0.png)

‌

#### Procedure

**1\.**Configure settings for IRF setup on Device
A:

\# Shut down the physical interfaces used
for IRF links. In this example, the physical interfaces are shut down in batch.
For more information, see Layer 2—LAN Switching
Configuration Guide.

\<Sysname\> system-view

\[Sysname] interface range ten-gigabitethernet 1/0/1 to ten-gigabitethernet 1/0/4

\[Sysname-if-range] shutdown

\[Sysname-if-range] quit 

\# Bind Ten-GigabitEthernet 1/0/1 and Ten-GigabitEthernet 1/0/2 to IRF-port 1/1.

\[Sysname] irf-port 1/1

\[Sysname-irf-port1/1] port
group interface ten-gigabitethernet 1/0/1

\[Sysname-irf-port1/1] port
group interface ten-gigabitethernet 1/0/2

\[Sysname-irf-port1/1] quit

\# Bind Ten-GigabitEthernet 1/0/3 and Ten-GigabitEthernet 1/0/4 to IRF-port 1/2.

\[Sysname] irf-port 1/2

\[Sysname-irf-port1/2] port
group interface ten-gigabitethernet 1/0/3

\[Sysname-irf-port1/2] port
group interface ten-gigabitethernet 1/0/4

\[Sysname-irf-port1/2] quit

\# Bring up the physical interfaces and
save the configuration.

\[Sysname] interface range ten-gigabitethernet 1/0/1 to ten-gigabitethernet 1/0/4

\[Sysname-if-range] undo
shutdown

\[Sysname-if-range] quit

\[Sysname] save

\# Activate the IRF port configuration.

\[Sysname] irf-port-configuration
active

**2\.**Configure settings for IRF setup on Device B:

\# Change the member ID of Device B to 2
and reboot the device to have the change take effect.

\<Sysname\> system-view

\[Sysname] irf member 1
renumber 2

Renumbering the member ID may
result in configuration change or loss. Continue? \[Y/N]:y

\[Sysname] quit

\<Sysname\> reboot

\# Connect Device B to Device A as shown
in [Figure 14](#_Ref482458077),
and log in to Device B. (Details not shown.)

\# Shut down the physical interfaces for
IRF links.

\<Sysname\> system-view

\[Sysname] interface range ten-gigabitethernet 2/0/1 to ten-gigabitethernet 2/0/4

\[Sysname-if-range] shutdown

\[Sysname-if-range] quit 

\# Bind Ten-GigabitEthernet 2/0/1 and Ten-GigabitEthernet 2/0/2 to IRF-port 2/1.

\[Sysname] irf-port 2/1

\[Sysname-irf-port2/1] port
group interface ten-gigabitethernet 2/0/1

\[Sysname-irf-port2/1] port
group interface ten-gigabitethernet 2/0/2

\[Sysname-irf-port2/1] quit

\# Bind Ten-GigabitEthernet 2/0/3 and Ten-GigabitEthernet 2/0/4 to IRF-port 2/2.

\[Sysname] irf-port 2/2

\[Sysname-irf-port2/2] port
group interface ten-gigabitethernet 2/0/3

\[Sysname-irf-port2/2] port
group interface ten-gigabitethernet 2/0/4

\[Sysname-irf-port2/2] quit

\# Bring up the physical interfaces and
save the configuration.

\[Sysname] interface range ten-gigabitethernet 2/0/1 to ten-gigabitethernet 2/0/4

\[Sysname-if-range] undo
shutdown

\[Sysname-if-range] quit

\[Sysname] save

\# Activate the IRF port configuration.

\[Sysname] irf-port-configuration
active

The two devices perform master election,
and the one that has lost the election reboots to form an IRF fabric with the
master.

**3\.**Configure settings for IRF setup on Device C:

\# Change the member ID of Device C to 3
and reboot the device to have the change take effect.

\<Sysname\> system-view

\[Sysname] irf member 1
renumber 3

Renumbering the member ID may
result in configuration change or loss. Continue? \[Y/N]:y

\[Sysname] quit

\<Sysname\> reboot

\# Connect Device C to Device A as shown
in [Figure 14](#_Ref482458077), and
log in to Device C. (Details not shown.)

\# Shut down the physical interfaces for
IRF links.

\<Sysname\> system-view

\[Sysname] interface range ten-gigabitethernet 3/0/1 to ten-gigabitethernet 3/0/4

\[Sysname-if-range] shutdown

\[Sysname-if-range] quit 

\# Bind Ten-GigabitEthernet 3/0/1 and Ten-GigabitEthernet 3/0/2 to IRF-port 3/1.

\[Sysname] irf-port 3/1

\[Sysname-irf-port3/1] port
group interface ten-gigabitethernet 3/0/1

\[Sysname-irf-port3/1] port
group interface ten-gigabitethernet 3/0/2

\[Sysname-irf-port3/1] quit

\# Bind Ten-GigabitEthernet 3/0/3 and Ten-GigabitEthernet 3/0/4 to IRF-port 3/2.

\[Sysname] irf-port 3/2

\[Sysname-irf-port3/2] port
group interface ten-gigabitethernet 3/0/3

\[Sysname-irf-port3/2] port
group interface ten-gigabitethernet 3/0/4

\[Sysname-irf-port3/2] quit

\# Bring up the physical interfaces and
save the configuration.

\[Sysname] interface range ten-gigabitethernet 3/0/1 to ten-gigabitethernet 3/0/4

\[Sysname-if-range] undo
shutdown

\[Sysname-if-range] quit

\[Sysname] save

\# Activate the IRF port configuration. 

\[Sysname] irf-port-configuration
active

Device C reboots to join the IRF fabric.

**4\.**Configure settings for IRF setup on Device D:

\# Change the member ID of Device D to 4
and reboot the device to have the change take effect.

\<Sysname\> system-view

\[Sysname] irf member 1
renumber 4

Renumbering the member ID may
result in configuration change or loss. Continue? \[Y/N]:y

\[Sysname] quit

\<Sysname\> reboot

\# Connect Device D to Device B and Device
C as shown in [Figure 14](#_Ref482458077),
and log in to Device D. (Details not shown.)

\# Shut down the physical interfaces.

\<Sysname\> system-view

\[Sysname] interface range ten-gigabitethernet 4/0/1 to ten-gigabitethernet 4/0/4

\[Sysname-if-range] shutdown

\[Sysname-if-range] quit 

\# Bind Ten-GigabitEthernet 4/0/1 and Ten-GigabitEthernet 4/0/2 to IRF-port 4/1.

\[Sysname] irf-port 4/1

\[Sysname-irf-port4/1] port
group interface ten-gigabitethernet 4/0/1

\[Sysname-irf-port4/1] port
group interface ten-gigabitethernet 4/0/2

\[Sysname-irf-port4/1] quit

\# Bind Ten-GigabitEthernet 4/0/3 and Ten-GigabitEthernet 4/0/4 to IRF-port 4/2.

\[Sysname] irf-port 4/2

\[Sysname-irf-port4/2] port
group interface ten-gigabitethernet 4/0/3

\[Sysname-irf-port4/2] port
group interface ten-gigabitethernet 4/0/4

\[Sysname-irf-port4/2] quit

\# Bring up the physical interfaces and
save the configuration.

\[Sysname] interface range ten-gigabitethernet 4/0/1 to ten-gigabitethernet 4/0/4

\[Sysname-if-range] undo
shutdown

\[Sysname-if-range] quit

\[Sysname] save

\# Activate the IRF port configuration. 

\[Sysname] irf-port-configuration
active

Device D reboots to join the IRF fabric.
A four-chassis IRF fabric is formed.

**5\.**Configure LACP MAD on the IRF fabric:

\# Set the domain ID of the IRF fabric to
1\.

\<Sysname\> system-view

\[Sysname] irf domain 1

\# Create a dynamic aggregate interface
and enable LACP MAD.

\[Sysname] interface bridge-aggregation
2

\[Sysname-Bridge-Aggregation2] link-aggregation
mode dynamic

\[Sysname-Bridge-Aggregation2] mad
enable

You need to assign a domain ID
(range: 0-4294967295)

\[Current domain ID is: 1]:

The assigned domain ID is: 1

\[Sysname-Bridge-Aggregation2]
quit

\# Assign Ten-GigabitEthernet 1/0/5, Ten-GigabitEthernet 2/0/5, Ten-GigabitEthernet 3/0/5, and Ten-GigabitEthernet 4/0/5 to the aggregate
interface.

\[Sysname] interface range ten-gigabitethernet 1/0/5 ten-gigabitethernet 2/0/5 ten-gigabitethernet 3/0/5 ten-gigabitethernet 4/0/5

\[Sysname-if-range] port
link-aggregation group 2

\[Sysname-if-range] quit

**6\.**Configure Device E as the intermediate
device for LACP MAD:

 

| CAUTION | CAUTION:  If the intermediate device is also an IRF fabric, assign the two IRF fabrics different domain IDs for correct split detection. False detection causes IRF split. |
| --- | --- |

 

\# Create a dynamic aggregate interface.

\<Sysname\> system-view

\[Sysname] interface bridge-aggregation
2

\[Sysname-Bridge-Aggregation2] link-aggregation
mode dynamic

\[Sysname-Bridge-Aggregation2]
quit

\# Assign Ten-GigabitEthernet 1/0/1, Ten-GigabitEthernet 1/0/2, Ten-GigabitEthernet 1/0/3, and Ten-GigabitEthernet 1/0/4 to the aggregate
interface.

\[Sysname] interface range ten-gigabitethernet 1/0/1 to ten-gigabitethernet 1/0/4

\[Sysname-if-range] port link-aggregation
group 2

\[Sysname-if-range] quit

### Example: Configuring an IRF fabric with BFD MAD enabled on a VLAN interface

#### Network configuration

As shown in [Figure 15](#_Ref482458386), set
up a four-chassis IRF fabric at the distribution layer of the enterprise
network.

·     Configure BFD MAD on the IRF fabric and set up
BFD MAD links between each member device and the intermediate device.

·     Disable the spanning tree feature on the ports
used for BFD MAD, because the two features conflict with each other.

Figure 15 Network diagram

 ![](https://resource.h3c.com/en/202412/25/20241225_12647065_x_Img_x_png_33_2328684_294551_0.png)

‌

#### Procedure

**1\.**Configure settings for IRF setup on Device
A:

\# Shut down the physical interfaces used
for IRF links.

\<Sysname\> system-view

\[Sysname] interface range ten-gigabitethernet 1/0/1 to ten-gigabitethernet 1/0/4

\[Sysname-if-range] shutdown

\[Sysname-if-range] quit 

\# Bind Ten-GigabitEthernet 1/0/1 and Ten-GigabitEthernet 1/0/2 to IRF-port 1/1.

\[Sysname] irf-port 1/1

\[Sysname-irf-port1/1] port
group interface ten-gigabitethernet 1/0/1

\[Sysname-irf-port1/1] port
group interface ten-gigabitethernet 1/0/2

\[Sysname-irf-port1/1] quit

\# Bind Ten-GigabitEthernet 1/0/3 and Ten-GigabitEthernet 1/0/4 to IRF-port 1/2.

\[Sysname] irf-port 1/2

\[Sysname-irf-port1/2] port
group interface ten-gigabitethernet 1/0/3

\[Sysname-irf-port1/2] port
group interface ten-gigabitethernet 1/0/4

\[Sysname-irf-port1/2] quit

\# Bring up the physical interfaces and
save the configuration.

\[Sysname] interface range ten-gigabitethernet 1/0/1 to ten-gigabitethernet 1/0/4

\[Sysname-if-range] undo shutdown

\[Sysname-if-range] quit

\[Sysname] save

\# Activate the IRF port configuration. 

\[Sysname] irf-port-configuration
active

**2\.**Configure settings for IRF setup on Device B:

\# Change the member ID of Device B to 2
and reboot the device to have the change take effect.

\<Sysname\> system-view

\[Sysname] irf member 1
renumber 2

Renumbering the member ID may
result in configuration change or loss. Continue? \[Y/N]:y

\[Sysname] quit

\<Sysname\> reboot

\# Connect Device B to Device A as shown
in [Figure 15](#_Ref482458386),
and log in to Device B. (Details not shown.)

\# Shut down the physical interfaces used
for IRF links.

\<Sysname\> system-view

\[Sysname] interface range ten-gigabitethernet 2/0/1 to ten-gigabitethernet 2/0/4

\[Sysname-if-range] shutdown

\[Sysname-if-range] quit 

\# Bind Ten-GigabitEthernet 2/0/1 and Ten-GigabitEthernet 2/0/2 to IRF-port 2/1.

\[Sysname] irf-port 2/1

\[Sysname-irf-port2/1] port
group interface ten-gigabitethernet 2/0/1

\[Sysname-irf-port2/1] port
group interface ten-gigabitethernet 2/0/2

\[Sysname-irf-port2/1] quit

\# Bind Ten-GigabitEthernet 2/0/3 and Ten-GigabitEthernet 2/0/4 to IRF-port 2/2.

\[Sysname] irf-port 2/2

\[Sysname-irf-port2/2] port
group interface ten-gigabitethernet 2/0/3

\[Sysname-irf-port2/2] port
group interface ten-gigabitethernet 2/0/4

\[Sysname-irf-port2/2] quit

\# Bring up the physical interfaces and
save the configuration.

\[Sysname] interface range ten-gigabitethernet 2/0/1 to ten-gigabitethernet 2/0/4

\[Sysname-if-range] undo
shutdown

\[Sysname-if-range] quit

\[Sysname] save

\# Activate the IRF port configuration. 

\[Sysname] irf-port-configuration
active

The two devices perform master election,
and the one that has lost the election reboots to form an IRF fabric with the
master. 

**3\.**Configure settings for IRF setup on Device C:

\# Change the member ID of Device C to 3
and reboot the device to have the change take effect.

\<Sysname\> system-view

\[Sysname] irf member 1
renumber 3

Renumbering the member ID may
result in configuration change or loss. Continue? \[Y/N]:y

\[Sysname] quit

\<Sysname\> reboot

\# Connect Device C to Device A as shown
in [Figure 15](#_Ref482458386), and
log in to Device C. (Details not shown.)

\# Shut down the physical interfaces used
for IRF links.

\<Sysname\> system-view

\[Sysname] interface range ten-gigabitethernet 3/0/1 to ten-gigabitethernet 3/0/4

\[Sysname-if-range] shutdown

\[Sysname-if-range] quit 

\# Bind Ten-GigabitEthernet 3/0/1 and Ten-GigabitEthernet 3/0/2 to IRF-port 3/1.

\[Sysname] irf-port 3/1

\[Sysname-irf-port3/1] port
group interface ten-gigabitethernet 3/0/1

\[Sysname-irf-port3/1] port
group interface ten-gigabitethernet 3/0/2

\[Sysname-irf-port3/1] quit

\# Bind Ten-GigabitEthernet 3/0/3 and Ten-GigabitEthernet 3/0/4 to IRF-port 3/2.

\[Sysname] irf-port 3/2

\[Sysname-irf-port3/2] port
group interface ten-gigabitethernet 3/0/3

\[Sysname-irf-port3/2] port
group interface ten-gigabitethernet 3/0/4

\[Sysname-irf-port3/2] quit

\# Bring up the physical interfaces and
save the configuration.

\[Sysname] interface range ten-gigabitethernet 3/0/1 to ten-gigabitethernet 3/0/4

\[Sysname-if-range] undo
shutdown

\[Sysname-if-range] quit

\[Sysname] save

\# Activate the IRF port configuration. 

\[Sysname] irf-port-configuration
active

Device C reboots to join the IRF fabric.

**4\.**Configure settings for IRF setup on Device D:

\# Change the member ID of Device D to 4
and reboot the device to have the change take effect.

\<Sysname\> system-view

\[Sysname] irf member 1
renumber 4

Renumbering the member ID may
result in configuration change or loss. Continue? \[Y/N]:y

\[Sysname] quit

\<Sysname\> reboot

\# Connect Device D to Device B and Device
C as shown in [Figure 15](#_Ref482458386),
and log in to Device D. (Details not shown.)

\# Shut down the physical interfaces used
for IRF links.

\<Sysname\> system-view

\[Sysname] interface range ten-gigabitethernet 4/0/1 to ten-gigabitethernet 4/0/4

\[Sysname-if-range] shutdown

\[Sysname-if-range] quit 

\# Bind Ten-GigabitEthernet 4/0/1 and Ten-GigabitEthernet 4/0/2 to IRF-port 4/1.

\[Sysname] irf-port 4/1

\[Sysname-irf-port4/1] port
group interface ten-gigabitethernet 4/0/1

\[Sysname-irf-port4/1] port
group interface ten-gigabitethernet 4/0/2

\[Sysname-irf-port4/1] quit

\# Bind Ten-GigabitEthernet 4/0/3 and Ten-GigabitEthernet 4/0/4 to IRF-port 4/2.

\[Sysname] irf-port 4/2

\[Sysname-irf-port4/2] port
group interface ten-gigabitethernet 4/0/3

\[Sysname-irf-port4/2] port
group interface ten-gigabitethernet 4/0/4

\[Sysname-irf-port4/2] quit

\# Bring up the physical interfaces and
save the configuration.

\[Sysname] interface range ten-gigabitethernet 4/0/1 to ten-gigabitethernet 4/0/4

\[Sysname-if-range] undo
shutdown

\[Sysname-if-range] quit

\[Sysname] save

\# Activate the IRF port configuration. 

\[Sysname] irf-port-configuration
active

Device D reboots to join the IRF fabric.
A four-chassis IRF fabric is formed.

**5\.**Configure BFD MAD on the IRF fabric:

\# Create VLAN 3, and add Ten-GigabitEthernet 1/0/5, Ten-GigabitEthernet 2/0/5, Ten-GigabitEthernet 3/0/5, and Ten-GigabitEthernet 4/0/5 to VLAN 3\.

\[Sysname] vlan 3

\[Sysname-vlan3] port ten-gigabitethernet 1/0/5 ten-gigabitethernet 2/0/5 ten-gigabitethernet 3/0/5 ten-gigabitethernet 4/0/5

\[Sysname-vlan3] quit

\# Create VLAN-interface 3, and configure a
MAD IP address for each member device on the VLAN interface.

\[Sysname] interface vlan-interface
3

\[Sysname-Vlan-interface3] mad
bfd enable

\[Sysname-Vlan-interface3] mad
ip address 192.168.2.1 24 member 1

\[Sysname-Vlan-interface3] mad
ip address 192.168.2.2 24 member 2

\[Sysname-Vlan-interface3] mad
ip address 192.168.2.3 24 member 3

\[Sysname-Vlan-interface3] mad
ip address 192.168.2.4 24 member 4

\[Sysname-Vlan-interface3] quit

\# Disable the spanning tree feature on Ten-GigabitEthernet 1/0/5, Ten-GigabitEthernet 2/0/5, Ten-GigabitEthernet 3/0/5, and Ten-GigabitEthernet 4/0/5.

\[Sysname] interface range ten-gigabitethernet 1/0/5 ten-gigabitethernet 2/0/5 ten-gigabitethernet 3/0/5 ten-gigabitethernet 4/0/5

\[Sysname-if-range] undo stp
enable

\[Sysname-if-range] quit

**6\.**Configure Device E as the intermediate
device for BFD MAD:

\# Create VLAN 3, and assign Ten-GigabitEthernet 1/0/1, Ten-GigabitEthernet 1/0/2, Ten-GigabitEthernet 1/0/3, and Ten-GigabitEthernet 1/0/4 to VLAN 3 for forwarding
BFD MAD packets.

\<DeviceE\> system-view

\[DeviceE] vlan 3

\[DeviceE-vlan3] port ten-gigabitethernet 1/0/1 to ten-gigabitethernet 1/0/4

\[DeviceE-vlan3] quit

### Example: Configuring an IRF fabric with ARP MAD enabled on a VLAN interface

#### Network configuration

As shown in [Figure 16](#_Ref482458454), set
up a four-chassis IRF fabric in the enterprise network.

·     Configure ARP MAD on the IRF fabric and use the
links connected to Device E for transmitting ARP MAD packets.

·     To prevent loops, run the spanning tree feature
between Device E and the IRF fabric.

Figure 16 Network diagram

 ![](https://resource.h3c.com/en/202412/25/20241225_12647066_x_Img_x_png_34_2328684_294551_0.png)

‌

#### Procedure

**1\.**Configure settings for IRF setup on Device
A:

\# Shut down the physical interfaces used
for IRF links.

\<Sysname\> system-view

\[Sysname] interface range ten-gigabitethernet 1/0/1 to ten-gigabitethernet 1/0/4

\[Sysname-if-range] shutdown

\[Sysname-if-range] quit

\# Bind Ten-GigabitEthernet 1/0/1 and Ten-GigabitEthernet 1/0/2 to IRF-port 1/1.

\[Sysname] irf-port 1/1

\[Sysname-irf-port1/1] port
group interface ten-gigabitethernet 1/0/1

\[Sysname-irf-port1/1] port
group interface ten-gigabitethernet 1/0/2

\[Sysname-irf-port1/1] quit

\# Bind Ten-GigabitEthernet 1/0/3 and Ten-GigabitEthernet 1/0/4 to IRF-port 1/2.

\[Sysname] irf-port 1/2

\[Sysname-irf-port1/2] port
group interface ten-gigabitethernet 1/0/3

\[Sysname-irf-port1/2] port
group interface ten-gigabitethernet 1/0/4

\[Sysname-irf-port1/2] quit

\# Bring up the physical interfaces and
save the configuration.

\[Sysname] interface range ten-gigabitethernet 1/0/1 to ten-gigabitethernet 1/0/4

\[Sysname-if-range] undo
shutdown

\[Sysname-if-range] quit

\[Sysname] save

\# Activate the IRF port configuration. 

\[Sysname] irf-port-configuration
active

**2\.**Configure settings for IRF setup on Device B:

\# Change the member ID of Device B to 2
and reboot the device to have the change take effect.

\<Sysname\> system-view

\[Sysname] irf member 1
renumber 2

Renumbering the member ID may
result in configuration change or loss. Continue? \[Y/N]:y

\[Sysname] quit

\<Sysname\> reboot

\# Connect Device B to Device A as shown
in [Figure 16](#_Ref482458454),
and log in to Device B. (Details not shown.)

\# Shut down the physical interfaces used
for IRF links.

\<Sysname\> system-view

\[Sysname] interface range ten-gigabitethernet 2/0/1 to ten-gigabitethernet 2/0/4

\[Sysname-if-range] shutdown

\[Sysname-if-range] quit 

\# Bind Ten-GigabitEthernet 2/0/1 and Ten-GigabitEthernet 2/0/2 to IRF-port 2/1.

\[Sysname] irf-port 2/1

\[Sysname-irf-port2/1] port
group interface ten-gigabitethernet 2/0/1

\[Sysname-irf-port2/1] port group
interface ten-gigabitethernet 2/0/2

\[Sysname-irf-port2/1] quit

\# Bind Ten-GigabitEthernet 2/0/3 and Ten-GigabitEthernet 2/0/4 to IRF-port 2/2.

\[Sysname] irf-port 2/2

\[Sysname-irf-port2/2] port
group interface ten-gigabitethernet 2/0/3

\[Sysname-irf-port2/2] port
group interface ten-gigabitethernet 2/0/4

\[Sysname-irf-port2/2] quit

\# Bring up the physical interfaces and
save the configuration.

\[Sysname] interface range ten-gigabitethernet 2/0/1 to ten-gigabitethernet 2/0/4

\[Sysname-if-range] undo
shutdown

\[Sysname-if-range] quit

\[Sysname] save

\# Activate the IRF port configuration. 

\[Sysname] irf-port-configuration
active

The two devices perform master election,
and the one that has lost the election reboots to form an IRF fabric with the
master. 

**3\.**Configure settings for IRF setup on Device C:

\# Change the member ID of Device C to 3
and reboot the device to have the change take effect.

\<Sysname\> system-view

\[Sysname] irf member 1
renumber 3

Renumbering the member ID may
result in configuration change or loss. Continue? \[Y/N]:y

\[Sysname] quit

\<Sysname\> reboot

\# Connect Device C to Device A as shown
in [Figure 16](#_Ref482458454), and
log in to Device C. (Details not shown.)

\# Shut down the physical interfaces used
for IRF links.

\<Sysname\> system-view

\[Sysname] interface range ten-gigabitethernet 3/0/1 to ten-gigabitethernet 3/0/4

\[Sysname-if-range] shutdown

\[Sysname-if-range] quit 

\# Bind Ten-GigabitEthernet 3/0/1 and Ten-GigabitEthernet 3/0/2 to IRF-port 3/1.

\[Sysname] irf-port 3/1

\[Sysname-irf-port3/1] port
group interface ten-gigabitethernet 3/0/1

\[Sysname-irf-port3/1] port
group interface ten-gigabitethernet 3/0/2

\[Sysname-irf-port3/1] quit

\# Bind Ten-GigabitEthernet 3/0/3 and Ten-GigabitEthernet 3/0/4 to IRF-port 3/2.

\[Sysname] irf-port 3/2

\[Sysname-irf-port3/2] port
group interface ten-gigabitethernet 3/0/3

\[Sysname-irf-port3/2] port
group interface ten-gigabitethernet 3/0/4

\[Sysname-irf-port3/2] quit

\# Bring up the physical interfaces and
save the configuration.

\[Sysname] interface range ten-gigabitethernet 3/0/1 to ten-gigabitethernet 3/0/4

\[Sysname-if-range] undo
shutdown

\[Sysname-if-range] quit

\[Sysname] save

\# Activate the IRF port configuration. 

\[Sysname] irf-port-configuration
active

Device C reboots to join the IRF fabric.

**4\.**Configure settings for IRF setup on Device D:

\# Change the member ID of Device D to 4
and reboot the device to have the change take effect.

\<Sysname\> system-view

\[Sysname] irf member 1
renumber 4

Renumbering the member ID may
result in configuration change or loss. Continue? \[Y/N]:y

\[Sysname] quit

\<Sysname\> reboot

\# Connect Device D to Device B and Device
C as shown in [Figure 16](#_Ref482458454),
and log in to Device D. (Details not shown.)

\# Shut down the physical interfaces used
for IRF links.

\<Sysname\> system-view

\[Sysname] interface range ten-gigabitethernet 4/0/1 to ten-gigabitethernet 4/0/4

\[Sysname-if-range] shutdown

\[Sysname-if-range] quit

\# Bind Ten-GigabitEthernet 4/0/1 and Ten-GigabitEthernet 4/0/2 to IRF-port 4/1.

\[Sysname] irf-port 4/1

\[Sysname-irf-port4/1] port
group interface ten-gigabitethernet 4/0/1

\[Sysname-irf-port4/1] port
group interface ten-gigabitethernet 4/0/2

\[Sysname-irf-port4/1] quit

\# Bind Ten-GigabitEthernet 4/0/3 and Ten-GigabitEthernet 4/0/4 to IRF-port 4/2.

\[Sysname] irf-port 4/2

\[Sysname-irf-port4/2] port
group interface ten-gigabitethernet 4/0/3

\[Sysname-irf-port4/2] port
group interface ten-gigabitethernet 4/0/4

\[Sysname-irf-port4/2] quit

\# Bring up the physical interfaces and
save the configuration.

\[Sysname] interface range ten-gigabitethernet 4/0/1 to ten-gigabitethernet 4/0/4

\[Sysname-if-range] undo
shutdown

\[Sysname-if-range] quit

\[Sysname] save

\# Activate the IRF port configuration. 

\[Sysname] irf-port-configuration
active

Device D reboots to join the IRF fabric.
A four-chassis IRF fabric is formed.

**5\.**Configure ARP MAD on the IRF fabric:

\# Enable the spanning tree feature
globally. Map the ARP MAD VLAN to MSTI 1 in the MST region.

\<Sysname\> system-view

\[Sysname] stp global enable

\[Sysname] stp region-configuration

\[Sysname-mst-region]
region-name arpmad

\[Sysname-mst-region] instance
1 vlan 3

\[Sysname-mst-region] active
region-configuration

\[Sysname-mst-region] quit

\# Configure the IRF fabric to change its bridge
MAC address as soon as the address owner leaves.

\[Sysname] undo irf mac-address
persistent

\# Set the domain ID of the IRF fabric to
1\.

\[Sysname] irf domain 1

\# Create VLAN 3, and assign Ten-GigabitEthernet 1/0/5, Ten-GigabitEthernet 2/0/5, Ten-GigabitEthernet 3/0/5, and Ten-GigabitEthernet 4/0/5 to VLAN 3\.

\[Sysname] vlan 3

\[Sysname-vlan3] port ten-gigabitethernet 1/0/5 ten-gigabitethernet 2/0/5 ten-gigabitethernet 3/0/5 ten-gigabitethernet 4/0/5

\[Sysname-vlan3] quit

\# Create VLAN-interface 3, assign it an
IP address, and enable ARP MAD on the interface.

\[Sysname] interface vlan-interface
3

\[Sysname-Vlan-interface3] ip
address 192.168.2.1 24

\[Sysname-Vlan-interface3] mad arp
enable

You need to assign a domain ID
(range: 0-4294967295)

\[Current domain ID is: 1]:

The assigned domain ID is: 1

**6\.**Configure Device E as the intermediate
device for ARP MAD:

 

| CAUTION | CAUTION:  If the intermediate device is also in an IRF fabric, assign the two IRF fabrics different domain IDs for correct split detection. False detection causes IRF split. |
| --- | --- |

 

\# Enable the spanning tree feature
globally. Map the ARP MAD VLAN to MSTI 1 in the MST region.

\<DeviceE\> system-view

\[DeviceE] stp global enable

\[DeviceE] stp
region-configuration

\[DeviceE-mst-region]
region-name arpmad

\[DeviceE-mst-region] instance
1 vlan 3

\[DeviceE-mst-region] active
region-configuration

\[DeviceE-mst-region] quit

\# Create VLAN 3, and assign Ten-GigabitEthernet 1/0/1, Ten-GigabitEthernet 1/0/2, Ten-GigabitEthernet 1/0/3, and Ten-GigabitEthernet 1/0/4 to VLAN 3 for
forwarding ARP MAD packets.

\[DeviceE] vlan 3

\[DeviceE-vlan3] port ten-gigabitethernet 1/0/1 to ten-gigabitethernet 1/0/4

\[DeviceE-vlan3] quit

### Example: Configuring an IRF fabric with ND MAD enabled on a VLAN interface

#### Network configuration

As shown in [Figure 17](#_Ref482458512), set
up a four-chassis IRF fabric in the IPv6 enterprise network.

·     Configure ND MAD on the IRF fabric and use the
links connected to Device E for transmitting ND MAD packets.

·     To prevent loops, run the spanning tree feature
between Device E and the IRF fabric.

Figure 17 Network diagram

 ![](https://resource.h3c.com/en/202412/25/20241225_12647068_x_Img_x_png_36_2328684_294551_0.png)

‌

#### Procedure

**1\.**Configure settings for IRF setup on Device
A:

\# Shut down the physical interfaces used
for IRF links.

\<Sysname\> system-view

\[Sysname] interface range ten-gigabitethernet 1/0/1 to ten-gigabitethernet 1/0/4

\[Sysname-if-range] shutdown

\[Sysname-if-range] quit

\# Bind Ten-GigabitEthernet 1/0/1 and Ten-GigabitEthernet 1/0/2 to IRF-port 1/1.

\[Sysname] irf-port 1/1

\[Sysname-irf-port1/1] port
group interface ten-gigabitethernet 1/0/1

\[Sysname-irf-port1/1] port
group interface ten-gigabitethernet 1/0/2

\[Sysname-irf-port1/1] quit

\# Bind Ten-GigabitEthernet 1/0/3 and Ten-GigabitEthernet 1/0/4 to IRF-port 1/2.

\[Sysname] irf-port 1/2

\[Sysname-irf-port1/2] port
group interface ten-gigabitethernet 1/0/3

\[Sysname-irf-port1/2] port
group interface ten-gigabitethernet 1/0/4

\[Sysname-irf-port1/2] quit

\# Bring up the physical interfaces and
save the configuration.

\[Sysname] interface range ten-gigabitethernet 1/0/1 to ten-gigabitethernet 1/0/4

\[Sysname-if-range] undo
shutdown

\[Sysname-if-range] quit

\[Sysname] save

\# Activate the IRF port configuration. 

\[Sysname] irf-port-configuration
active

**2\.**Configure settings for IRF setup on Device B:

\# Change the member ID of Device B to 2
and reboot the device to have the change take effect.

\<Sysname\> system-view

\[Sysname] irf member 1
renumber 2

Renumbering the member ID may
result in configuration change or loss. Continue? \[Y/N]:y

\[Sysname] quit

\<Sysname\> reboot

\# Connect Device B to Device A as shown
in [Figure 17](#_Ref482458512),
and log in to Device B. (Details not shown.)

\# Shut down the physical interfaces used
for IRF links.

\<Sysname\> system-view

\[Sysname] interface range ten-gigabitethernet 2/0/1 to ten-gigabitethernet 2/0/4

\[Sysname-if-range] shutdown

\[Sysname-if-range] quit

\# Bind Ten-GigabitEthernet 2/0/1 and Ten-GigabitEthernet 2/0/2 to IRF-port 2/1.

\[Sysname] irf-port 2/1

\[Sysname-irf-port2/1] port
group interface ten-gigabitethernet 2/0/1

\[Sysname-irf-port2/1] port
group interface ten-gigabitethernet 2/0/2

\[Sysname-irf-port2/1] quit

\# Bind Ten-GigabitEthernet 2/0/3 and Ten-GigabitEthernet 2/0/4 to IRF-port 2/2.

\[Sysname] irf-port 2/2

\[Sysname-irf-port2/2] port
group interface ten-gigabitethernet 2/0/3

\[Sysname-irf-port2/2] port
group interface ten-gigabitethernet 2/0/4

\[Sysname-irf-port2/2] quit

\# Bring up the physical interfaces and
save the configuration.

\[Sysname] interface range ten-gigabitethernet 2/0/1 to ten-gigabitethernet 2/0/4

\[Sysname-if-range] undo
shutdown

\[Sysname-if-range] quit

\[Sysname] save

\# Activate the IRF port configuration. 

\[Sysname] irf-port-configuration
active

The two devices perform master election,
and the one that has lost the election reboots to form an IRF fabric with the
master. 

**3\.**Configure settings for IRF setup on Device C:

\# Change the member ID of Device C to 3
and reboot the device to have the change take effect.

\<Sysname\> system-view

\[Sysname] irf member 1
renumber 3

Renumbering the member ID may
result in configuration change or loss. Continue? \[Y/N]:y

\[Sysname] quit

\<Sysname\> reboot

\# Connect Device C to Device A as shown
in [Figure 17](#_Ref482458512),
and log in to Device C. (Details not shown.)

\# Shut down the physical interfaces used
for IRF links.

\<Sysname\> system-view

\[Sysname] interface range ten-gigabitethernet 3/0/1 to ten-gigabitethernet 3/0/4

\[Sysname-if-range] shutdown

\[Sysname-if-range] quit

\# Bind Ten-GigabitEthernet 3/0/1 and Ten-GigabitEthernet 3/0/2 to IRF-port 3/1.

\[Sysname] irf-port 3/1

\[Sysname-irf-port3/1] port
group interface ten-gigabitethernet 3/0/1

\[Sysname-irf-port3/1] port
group interface ten-gigabitethernet 3/0/2

\[Sysname-irf-port3/1] quit

\# Bind Ten-GigabitEthernet 3/0/3 and Ten-GigabitEthernet 3/0/4 to IRF-port 3/2.

\[Sysname] irf-port 3/2

\[Sysname-irf-port3/2] port
group interface ten-gigabitethernet 3/0/3

\[Sysname-irf-port3/2] port
group interface ten-gigabitethernet 3/0/4

\[Sysname-irf-port3/2] quit

\# Bring up the physical interfaces and
save the configuration.

\[Sysname] interface range ten-gigabitethernet 3/0/1 to ten-gigabitethernet 3/0/4

\[Sysname-if-range] undo
shutdown

\[Sysname-if-range] quit

\[Sysname] save

\# Activate the IRF port configuration. 

\[Sysname] irf-port-configuration
active

Device C reboots to join the IRF fabric.

**4\.**Configure settings for IRF setup on Device D:

\# Change the member ID of Device D to 4
and reboot the device to have the change take effect.

\<Sysname\> system-view

\[Sysname] irf member 1
renumber 4

Renumbering the member ID may
result in configuration change or loss. Continue? \[Y/N]:y

\[Sysname] quit

\<Sysname\> reboot

\# Connect Device D to Device B and Device
C as shown in [Figure 17](#_Ref482458512), and
log in to Device D. (Details not shown.)

\# Shut down the physical interfaces used
for IRF links.

\<Sysname\> system-view

\[Sysname] interface range ten-gigabitethernet 4/0/1 to ten-gigabitethernet 4/0/4

\[Sysname-if-range] shutdown

\[Sysname-if-range] quit

\# Bind Ten-GigabitEthernet 4/0/1 and Ten-GigabitEthernet 4/0/2 to IRF-port 4/1.

\[Sysname] irf-port 4/1

\[Sysname-irf-port4/1] port
group interface ten-gigabitethernet 4/0/1

\[Sysname-irf-port4/1] port
group interface ten-gigabitethernet 4/0/2

\[Sysname-irf-port4/1] quit

\# Bind Ten-GigabitEthernet 4/0/3 and Ten-GigabitEthernet 4/0/4 to IRF-port 4/2.

\[Sysname] irf-port 4/2

\[Sysname-irf-port4/2] port
group interface ten-gigabitethernet 4/0/3

\[Sysname-irf-port4/2] port
group interface ten-gigabitethernet 4/0/4

\[Sysname-irf-port4/2] quit

\# Bring up the physical interfaces and
save the configuration.

\[Sysname] interface range ten-gigabitethernet 4/0/1 to ten-gigabitethernet 4/0/4

\[Sysname-if-range] undo
shutdown

\[Sysname-if-range] quit

\[Sysname] save

\# Activate the IRF port configuration. 

\[Sysname] irf-port-configuration
active

Device D reboots to join the IRF fabric.
A four-chassis IRF fabric is formed.

**5\.**Configure ND MAD on the IRF fabric:

\# Enable the spanning tree feature
globally. Map the ND MAD VLAN to MSTI 1 in the MST region.

\<Sysname\> system-view

\[Sysname] stp global enable

\[Sysname] stp
region-configuration

\[Sysname-mst-region]
region-name ndmad

\[Sysname-mst-region] instance
1 vlan 3

\[Sysname-mst-region] active
region-configuration

\[Sysname-mst-region] quit

\# Configure the IRF fabric to change its bridge
MAC address as soon as the address owner leaves.

\[Sysname] undo irf mac-address
persistent

\# Set the domain ID of the IRF fabric to
1\.

\[Sysname] irf domain 1

\# Create VLAN 3, and add Ten-GigabitEthernet 1/0/5, Ten-GigabitEthernet 2/0/5, Ten-GigabitEthernet 3/0/5, and Ten-GigabitEthernet 4/0/5 to VLAN 3\.

\[Sysname] vlan 3

\[Sysname-vlan3] port ten-gigabitethernet 1/0/5 ten-gigabitethernet 2/0/5 ten-gigabitethernet 3/0/5 ten-gigabitethernet 4/0/5

\[Sysname-vlan3] quit

\# Create VLAN-interface 3, assign it an
IPv6 address, and enable ND MAD on the interface.

\[Sysname] interface vlan-interface
3

\[Sysname-Vlan-interface3] ipv6
address 2001::1 64

\[Sysname-Vlan-interface3] mad
nd enable

You need to assign a domain ID
(range: 0-4294967295)

\[Current domain ID is: 1]:

The assigned domain ID is: 1

**6\.**Configure Device E as the intermediate
device for ND MAD:

 

| CAUTION | CAUTION:  If the intermediate device is also in an IRF fabric, assign the two IRF fabrics different domain IDs for correct split detection. False detection causes IRF split. |
| --- | --- |

 

\# Enable the spanning tree feature
globally. Map the ND MAD VLAN to MSTI 1 in the MST region.

\<DeviceE\> system-view

\[DeviceE] stp global enable

\[DeviceE] stp
region-configuration

\[DeviceE-mst-region]
region-name ndmad

\[DeviceE-mst-region] instance
1 vlan 3

\[DeviceE-mst-region] active
region-configuration

\[DeviceE-mst-region] quit

\# Create VLAN 3, and add Ten-GigabitEthernet 1/0/1, Ten-GigabitEthernet 1/0/2, Ten-GigabitEthernet 1/0/3, and Ten-GigabitEthernet 1/0/4 to VLAN 3 for
forwarding ND MAD packets.

\[DeviceE] vlan 3

\[DeviceE-vlan3] port ten-gigabitethernet 1/0/1 to ten-gigabitethernet 1/0/4

\[DeviceE-vlan3] quit

### Example: Configuring an IRF fabric with ARP MAD and management IP configured on the same management Ethernet port

‌

| IMPORTANT | IMPORTANT:  This example is also applicable to a two-chassis IRF fabric running ND MAD, except that you must configure ND MAD rather than ARP MAD. |
| --- | --- |

‌

#### Network configuration

As shown in [Figure 18](#_Ref32399016), use
Device A and Device B to set up a two-chassis IRF fabric.

Use management Ethernet port M-GigabitEthernet
0/0/0 to perform ARP MAD for IRF split detection and to provide device access
for management purposes.

Configure management IP addresses for the
IRF fabric and each member device on the management port to meet the following
requirements:

·     The administrator can use the same management IP
address to access the IRF fabric before and after an active/subordinate
switchover.

·     The administrator can access each member device
for failure recovery through their respective management IP addresses after the
IRF fabric splits.

Figure 18 Network diagram

![](https://resource.h3c.com/en/202412/25/20241225_12647071_x_Img_x_png_39_2328684_294551_0.png)

‌

#### Restrictions and guidelines

When you configure management IP addresses,
follow these restrictions and guidelines:

·     To make sure you can access the IRF fabric at
the same IP address after a master/subordinate switchover, use the ip address ip-address { mask-length \| mask } command to configure a management IP address on the management
port, in this example, on M-GigabitEthernet 0/0/0. You can use this address to
access the IRF fabric as long as the fabric has not split. This address might
be inaccesible for an IP conflict after the IRF fabric splits, because it
exists on all member devices. In the context of IRF, this address is called a
global management IP address.

·     To access each member device for failure
recovery after the IRF fabric splits, use the ip address ip-address { mask-length \| mask } irf-member member-id
command to configure a management IP address for each member device on the
management port, in this example, on M-GigabitEthernet 0/0/0. In the context of
IRF, this address is called a member-specific management IP address.

·     Make sure the management IP addresses assigned
to all IRF member devices on the same management Ethernet port belong to the
same subnet. Make sure the management IP addresses assigned to an IRF member
device on different management Ethernet ports belong to different subnets.

·     You must manually make sure the management IP
addresses configured on the subordinate devices will not cause IP address
conflicts on the network. When the IRF fabric is running correctly, only the IP
addresses of the management Ethernet ports on the master device take effect.
The management IP addresses of the management Ethernet ports on the subordinate
devices do not take effect. If a management IP address on a subordinate device
conflicts with an IP address in the network, the system cannot detect the IP
conflict, causing network issues after an IRF split. 

 

|  | NOTE:  When the IRF fabric is integrated, you can access the IRF fabric by using either the global management IP address or the management IP address specific to the master device. As a best practice to prevent the management IP address from changing upon a master/subordinate switchover, use the global management IP address as long as the IRF fabric is integrated. |
| --- | --- |

 

When you configure ARP MAD on a management
Ethernet port, follow the restrictions and guidelines in "[Configuring ARP MAD](#_Ref61818601)."

#### Setting up the IRF fabric

**1\.**Configure settings for IRF setup on Device
A:

\# Shut down the physical interfaces used
for IRF connection. This example uses Ten-GigabitEthernet 1/0/1 and Ten-GigabitEthernet 1/0/2 for IRF connection.

\<Sysname\> system-view

\[Sysname] interface range ten-gigabitethernet 1/0/1 to ten-gigabitethernet 1/0/2

\[Sysname-if-range] shutdown

\[Sysname-if-range] quit

\# Bind Ten-GigabitEthernet 1/0/1 and Ten-GigabitEthernet 1/0/2 to IRF-port 1/2.

\[Sysname] irf-port 1/2

\[Sysname-irf-port1/2] port
group interface ten-gigabitethernet 1/0/1

\[Sysname-irf-port1/2] port
group interface ten-gigabitethernet 1/0/2

\[Sysname-irf-port1/2] quit

\# Bring up the physical interfaces.

\[Sysname] interface range ten-gigabitethernet 1/0/1 to ten-gigabitethernet 1/0/2

\[Sysname-if-range] undo
shutdown

\[Sysname-if-range] quit

\# Save the running configuration to the
next-startup configuration file.

\[Sysname] save

\# Activate the IRF port configuration.

\[Sysname] irf-port-configuration
active

**2\.**Configure settings for IRF setup on Device
B:

\# Assign member ID 2 to Device B, and
reboot the device to have the change take effect.

\<Sysname\> system-view

\[Sysname] irf member 1
renumber 2

Renumbering the member ID may
result in configuration change or loss. Continue? \[Y/N]:y

\[Sysname] quit

\<Sysname\> reboot

\# Shut down the physical interfaces used
for IRF connection. This example uses Ten-GigabitEthernet 2/0/1 and Ten-GigabitEthernet 2/0/2 for IRF connection.

\<Sysname\> system-view

\[Sysname] interface range ten-gigabitethernet 2/0/1 to ten-gigabitethernet 2/0/2

\[Sysname-if-range] shutdown

\[Sysname-if-range] quit

\# Bind Ten-GigabitEthernet 2/0/1 and Ten-GigabitEthernet 2/0/2 to IRF-port 2/1.

\[Sysname] irf-port 2/1

\[Sysname-irf-port2/1] port
group interface ten-gigabitethernet 2/0/1

\[Sysname-irf-port2/1] port
group interface ten-gigabitethernet 2/0/2

\[Sysname-irf-port2/1] quit

\# Bring up the physical interfaces.

\[Sysname] interface range ten-gigabitethernet 2/0/1 to ten-gigabitethernet 2/0/2

\[Sysname-if-range] undo
shutdown

\[Sysname-if-range] quit

\# Save the running configuration to the
next-startup configuration file.

\[Sysname] save

\# Connect Device B to Device A, as shown
in [Figure 18](#_Ref32399016).

\# Activate the IRF port configuration.

\[Sysname]
irf-port-configuration active

Device A and Device B perform master
election. The device that has failed master election automatically reboots to
form an IRF fabric with the other device. A two-chassis IRF fabric is formed.

#### Configuring management IP addresses

\# Assign IP address 192.168.1.1/24 to the
IRF fabric on management Ethernet port M-GigabitEthernet 0/0/0. 

\<Sysname\> system-view

\[Sysname] interface
m-gigabitethernet 0/0/0

\[Sysname-M-GigabitEthernet0/0/0]
ip address 192.168.1.1 24

\# On management Ethernet port M-GigabitEthernet
0/0/0, assign IP addresses 192.168.1.101/24 and 192.168.1.102/24 to IRF member
device 1 (Device A) and IRF member device 2 (Device B), respectively. After the
IRF fabric splits, the administrator can use IP addresses 192.168.1.101/24 and 192.168.1.102/24
to log in to Device A and Device B, respectively.

\[Sysname-M-GigabitEthernet0/0/0]
ip address 192.168.1.101 24 irf-member 1

\[Sysname-M-GigabitEthernet0/0/0]
ip address 192.168.1.102 24 irf-member 2

\[Sysname-M-GigabitEthernet0/0/0]
quit

\# Exclude management Ethernet port M-GigabitEthernet
0/0/0 from being shut down by MAD upon detection of multi-active collisions.

\[Sysname] mad exclude
interface m-gigabitethernet 0/0/0

#### Configuring ARP MAD

**1\.**Configure the IRF fabric:

\# Configure the IRF fabric to change its
bridge MAC address as soon as the address owner leaves.

\[Sysname] undo irf mac-address persistent

\# Set the domain ID of the IRF fabric to
1\.

\[Sysname] irf domain 1

\# Enable ARP MAD on management Ethernet
port M-GigabitEthernet 0/0/0.

\[Sysname] interface m-gigabitethernet
0/0/0

\[Sysname-M-GigabitEthernet0/0/0]
mad arp enable

You need to assign a domain ID
(range: 0-4294967295)

\[Current domain ID is: 1]:

The assigned domain ID is: 1

\[Sysname-M-GigabitEthernet0/0/0]
quit

\# Save the running configuration to the
next-startup configuration file.

\[Sysname] save

**2\.**Configure Device C (a switch that supports
ARP) as the intermediate device for ARP MAD:

 

| CAUTION | CAUTION:  If the intermediate device is also in an IRF fabric, assign the two IRF fabrics different domain IDs for correct split detection. False detection causes IRF split. |
| --- | --- |

 

\# Create VLAN 100\.

\<DeviceC\> system-view

\[DeviceC] vlan 100

\# Assign GigabitEthernet 1/0/1 and GigabitEthernet
1/0/2 to VLAN 100 for forwarding ARP MAD packets.

\[DeviceC-vlan100] port
gigabitethernet 1/0/1 to gigabitethernet 1/0/2

\[DeviceC-vlan100] quit

#### Verifying the configuration

\# Disconnect the IRF links between Device A
and Device B. Verify that you can use 192.168.1.101 and 192.168.1.102 to log in
to Device A and Device B, respectively. (Details not shown.)

\# Execute the display mad
verbose command on Device A and Device B to
verify that MAD is correctly functioning, as follows:

·     The Multi-active recovery
state field displays Yes on one device and
displays No on the other device.

·     On each device, the IRF physical interfaces and
management Ethernet port M-GigabitEthernet 0/0/0 are excluded from being shut
down by MAD.

\#  Execute the display
interface brief command on Device A and Device B
to verify that the MAD shutdown action is performed correctly, as follows:

·     On the device in Recovery state, all network
interfaces have been shut down by MAD except the IRF physical interfaces and
management Ethernet port M-GigabitEthernet 0/0/0.

·     On the device not in Recovery state, no network
interfaces are shut down by MAD.

 

