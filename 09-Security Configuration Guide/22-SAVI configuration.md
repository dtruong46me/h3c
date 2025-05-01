
# Configuring SAVI

## About SAVI

Source Address Validation Improvement (SAVI)
checks the validity of the source addresses of global unicast IPv6 packets. It implements
the validity check by using the ND snooping, DHCPv6 snooping, ND attack detection,
and IP source guard features. SAVI checks only global unicast addresses and
forwards the packets that pass the validity check. Packets sourced from an
invalid address are dropped.

## SAVI application scenarios

#### DHCPv6-only

The hosts connected to the SAVI-enabled
device obtain addresses only through DHCPv6. DHCPv6 messages, ND messages (RA
and RR messages excluded), and IPv6 data packets are checked based on DHCPv6
snooping entries and static IPv6 source guard binding entries.

#### SLAAC-only

The hosts connected to the SAVI-enabled
device obtain addresses only through Stateless Address Autoconfiguration
(SLAAC). In this scenario, SAVI drops all DHCPv6 messages. Only ND messages and
IPv6 data packets are checked based on DHCPv6 snooping entries and static IPv6
source guard binding entries.

#### DHCPv6\+SLAAC

The hosts connected to the SAVI-enabled device
obtain addresses through DHCPv6 and SLAAC. In this scenario, SAVI checks all DHCPv6
messages, ND messages, and IPv6 data packets based on DHCPv6 snooping entries,
ND snooping entries, and static IPv6 source guard binding entries.

## SAVI tasks at a glance

To configure SAVI, perform the following
tasks:

**1\.**[Enabling SAVI](#_Ref456804621)

**2\.**[Configuring IPv6 source guard](#_Ref456804624)

**3\.**[Configuring DHCPv6 snooping](#_Ref456804627)

**4\.**[Configuring ND parameters](#_Ref456804633)

**5\.**(Optional.) [Setting the entry deletion delay](#_Ref456804637)

**6\.**(Optional.) [Enabling packet spoofing logging and
filtering entry logging](#_Ref59205917)

## Enabling SAVI

**1\.**Enter system view.

system-view

**2\.**Enable SAVI.

ipv6 savi strict

By default, SAVI is disabled.

## Configuring IPv6 source guard

**1\.**Enable IPv6 source guard on an interface.

**2\.**(Optional.) Configure static IPv6SG
bindings.

For more information about IPv6 source
guard configuration, see "Configuring IP source guard."

## Configuring DHCPv6 snooping

#### Restrictions and guidelines

Enable only DHCPv6 snooping for the
SLAAC-only scenario.

#### Procedure

**1\.**Enable DHCPv6 snooping.

**2\.**Specify DHCPv6 snooping trusted ports.

**3\.**Enable recording client information in DHCPv6
snooping entries.

For more information about DHCPv6 snooping configuration,
see Layer 3—IP Services Configuration Guide.

## Configuring ND parameters

#### Restrictions and guidelines

Enable only ND attack detection for the
DHCPv6-only scenario.

#### Procedure

**1\.**Enable ND snooping for
global unicast addresses.

For more information about ND
snooping, see IPv6 basics in Layer 3—IP Services Configuration Guide.

**2\.**Enable ND attack detection.

For more information about ND attack
detection, see "Configuring ND attack defense."

**3\.**Specify ND trusted ports.

For more information about ND trusted
ports, see "Configuring ND attack defense."

## Setting the entry deletion delay

#### About this task

The entry deletion delay is the period of
time that the device waits before deleting the DHCPv6 snooping entries and ND
snooping entries for a down port.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Set the entry deletion delay.

ipv6 savi down-delay delay-time

By default, the entry deletion delay is
30 seconds.

## Enabling packet spoofing logging and filtering entry logging

#### About this task

Packet spoofing logging enables the device
to generate log messages for the spoofed packets detected by SAVI.

Filtering entries are effective bindings
used for filtering IPv6 packets by the source IPv6 address. Filtering entry
logging enables the device to generate log messages for filtering entries. A
log message contains the IPv6 address, MAC address, VLAN, and interface of a
filtering entry.

The device sends packet spoofing and filtering
entry log messages to the information center. With the information center, you
can set log message filtering and output rules, including output destinations.
For more information about using the information center, see Network Management and Monitoring Configuration Guide.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enable packet spoofing logging.

ipv6 savi log enable spoofing-packet \[ interval interval \| total-number number ] \*

By default, packet spoofing logging is
disabled.

**3\.**Enable filtering entry logging.

ipv6 savi log enable filter-entry

By default, filtering entry logging is
disabled.

## SAVI configuration examples

### Example: Configuring DHCPv6-onlySAVI

#### Network configuration

As shown in[Figure 1](#_Ref441758638), configure
SAVI on the switch to meet the following requirements:

·     Clients obtain IPv6 addresses only through DHCPv6.

·     SAVI checks the source addresses of DHCPv6 messages,
ND messages (RA and RR messages excluded), and IPv6 data packets on Ten-GigabitEthernet 1/0/2 and Ten-GigabitEthernet 1/0/3.

Figure 1 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705490_x_Img_x_png_0_2216083_294551_0.png)

‌

#### Procedure

\# Enable
SAVI.

\<Switch\> system-view

\[Switch] ipv6 savi strict

\# Assign Ten-GigabitEthernet 1/0/1 through Ten-GigabitEthernet 1/0/3 to VLAN 2\.

\[Switch] vlan 2

\[Switch-vlan2] port ten-gigabitethernet 1/0/1 ten-gigabitethernet 1/0/2 ten-gigabitethernet 1/0/3

\[Switch-vlan2] quit

\# Enable DHCPv6 snooping.

\[Switch] ipv6 dhcp snooping enable

\# Configure Ten-GigabitEthernet 1/0/1 as a DHCPv6
snooping trusted port.

\[Switch] interface ten-gigabitethernet 1/0/1

\[Switch-Ten-GigabitEthernet1/0/1] ipv6 dhcp snooping
trust

\[Switch-Ten-GigabitEthernet1/0/1] quit

\# Enable recording DHCPv6 snooping entries
on Ten-GigabitEthernet 1/0/2
and Ten-GigabitEthernet 1/0/3.

\[Switch] interface ten-gigabitethernet 1/0/2

\[Switch-Ten-GigabitEthernet1/0/2] ipv6 dhcp snooping
binding record

\[Switch-Ten-GigabitEthernet1/0/2] quit

\[Switch] interface ten-gigabitethernet 1/0/3

\[Switch-Ten-GigabitEthernet1/0/3] ipv6 dhcp snooping
binding record

\[Switch-Ten-GigabitEthernet1/0/3] quit

\# Enable ND attack detection.

\[Switch] vlan 2

\[Switch-vlan2] ipv6 nd detection
enable

\[Switch-vlan2] quit

\# Enable IPv6 source guard on Ten-GigabitEthernet 1/0/2 and Ten-GigabitEthernet 1/0/3.

\[Switch] interface ten-gigabitethernet 1/0/2

\[Switch-Ten-GigabitEthernet1/0/2] ipv6 verify source
ip-address mac-address

\[Switch-Ten-GigabitEthernet1/0/2] quit

\[Switch] interface ten-gigabitethernet 1/0/3

\[Switch-Ten-GigabitEthernet1/0/3] ipv6 verify source
ip-address mac-address

\[Switch-Ten-GigabitEthernet1/0/3] quit

### Example: Configuring SLAAC-only SAVI

#### Network configuration

As shown in[Figure 2](#_Ref441763834), configure
SAVI on Switch B to meet the following requirements:

·     Hosts obtain IPv6 addresses only through SLAAC.

·     DHCPv6 messages are dropped on Ten-GigabitEthernet 1/0/1 through Ten-GigabitEthernet 1/0/3 in VLAN 2\.

·     SAVI checks the source addresses of ND messages
and IPv6 data packets on Ten-GigabitEthernet 1/0/1 and Ten-GigabitEthernet 1/0/2.

Figure 2 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705491_x_Img_x_png_1_2216083_294551_0.png)

‌

#### Procedure

\# Enable
SAVI.

\<SwitchB\> system-view

\[SwitchB] ipv6 savi strict

\# Assign Ten-GigabitEthernet 1/0/1 through Ten-GigabitEthernet 1/0/3 to VLAN 2\.

\[SwitchB] vlan 2

\[SwitchB-vlan2] port ten-gigabitethernet 1/0/1 ten-gigabitethernet 1/0/2 ten-gigabitethernet 1/0/3

\[SwitchB-vlan2] quit

\# Enable ND snooping for global unicast
addresses in VLAN 2\.

\[SwitchB] vlan 2

\[SwitchB-vlan2] ipv6 nd snooping
enable global

\# Enable ND attack detection for VLAN 2\.

\[SwitchB-vlan2] ipv6 nd detection
enable

\[SwitchB-vlan2] quit

\# Enable DHCPv6 snooping.

\[SwitchB] ipv6 dhcp snooping enable

\# Configure Ten-GigabitEthernet 1/0/3 as an ND trusted
port.

\[SwitchB] interface ten-gigabitethernet 1/0/3

\[SwitchB-Ten-GigabitEthernet1/0/3] ipv6 nd detection
trust

\[SwitchB-Ten-GigabitEthernet1/0/3] quit

\# Enable IPv6 source guard on Ten-GigabitEthernet 1/0/1 and Ten-GigabitEthernet 1/0/2.

\[SwitchB] interface ten-gigabitethernet 1/0/1

\[SwitchB-Ten-GigabitEthernet1/0/1] ipv6 verify source
ip-address mac-address

\[SwitchB-Ten-GigabitEthernet1/0/1] quit

\[SwitchB] interface ten-gigabitethernet 1/0/2

\[SwitchB-Ten-GigabitEthernet1/0/2] ipv6 verify source
ip-address mac-address

\[SwitchB-Ten-GigabitEthernet1/0/2] quit

### Example: Configuring DHCPv6\+SLAAC SAVI

#### Network configuration

As shown in[Figure 3](#_Ref441764111), configure
SAVI on Switch B to meet the following requirements:

·     Hosts obtain IP addresses through DHCPv6 or
SLAAC. 

·     SAVI checks the source addresses of DHCPv6
messages, ND messages, and IPv6 data packets on Ten-GigabitEthernet 1/0/3 through Ten-GigabitEthernet 1/0/5.

Figure 3 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705492_x_Img_x_png_2_2216083_294551_0.png)

‌

#### Procedure

\# Enable SAVI.

\<SwitchB\> system-view

\[SwitchB] ipv6 savi strict

\# Assign Ten-GigabitEthernet 1/0/1 through Ten-GigabitEthernet 1/0/5 to VLAN 2\.

\[SwitchB] vlan 2

\[SwitchB-vlan2] port ten-gigabitethernet 1/0/1 ten-gigabitethernet 1/0/2 ten-gigabitethernet 1/0/3 ten-gigabitethernet 1/0/4 ten-gigabitethernet 1/0/5

\# Enable DHCPv6 snooping.

\[SwitchB] ipv6 dhcp snooping enable

\# Enable recording DHCPv6 snooping entries
on Ten-GigabitEthernet 1/0/3
through Ten-GigabitEthernet 1/0/5.

\[SwitchB] interface ten-gigabitethernet 1/0/3

\[SwitchB-Ten-GigabitEthernet1/0/3] ipv6 dhcp snooping
binding record

\[SwitchB-Ten-GigabitEthernet1/0/3] quit

\[SwitchB] interface ten-gigabitethernet 1/0/4

\[SwitchB-Ten-GigabitEthernet1/0/4] ipv6 dhcp snooping
binding record

\[SwitchB-Ten-GigabitEthernet1/0/4] quit

\[SwitchB] interface ten-gigabitethernet 1/0/5

\[SwitchB-Ten-GigabitEthernet1/0/5] ipv6 dhcp snooping
binding record

\[SwitchB-Ten-GigabitEthernet1/0/5] quit

\# Configure Ten-GigabitEthernet 1/0/1 as a DHCPv6
snooping trusted port.

\[SwitchB] interface ten-gigabitethernet 1/0/1

\[SwitchB-Ten-GigabitEthernet1/0/1] ipv6 dhcp snooping
trust

\[SwitchB-Ten-GigabitEthernet1/0/1] quit

\# Enable ND snooping for global unicast
addresses in VLAN 2\. 

\[SwitchB] vlan 2

\[SwitchB-vlan2] ipv6 nd snooping
enable global

\# Enable ND attack detection for VLAN 2\.

\[SwitchB-vlan2] ipv6 nd detection
enable

\[SwitchB-vlan2] quit

\# Configure Ten-GigabitEthernet 1/0/2 as an ND trusted
port.

\[SwitchB] interface ten-gigabitethernet 1/0/2

\[SwitchB-Ten-GigabitEthernet1/0/2] ipv6 nd detection
trust

\[SwitchB-Ten-GigabitEthernet1/0/2] quit

\# Enable IPv6 source guard on Ten-GigabitEthernet 1/0/3 through Ten-GigabitEthernet 1/0/5.

\[SwitchB] interface ten-gigabitethernet 1/0/3

\[SwitchB-Ten-GigabitEthernet1/0/3] ipv6 verify source
ip-address mac-address

\[SwitchB-Ten-GigabitEthernet1/0/3] quit

\[SwitchB] interface ten-gigabitethernet 1/0/4

\[SwitchB-Ten-GigabitEthernet1/0/4] ipv6 verify source
ip-address mac-address

\[SwitchB-Ten-GigabitEthernet1/0/4] quit

\[SwitchB] interface ten-gigabitethernet 1/0/5

\[SwitchB-Ten-GigabitEthernet1/0/5] ipv6 verify source
ip-address mac-address

 

