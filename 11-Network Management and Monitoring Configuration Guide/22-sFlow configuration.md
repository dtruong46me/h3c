
# Configuring sFlow

## About sFlow

sFlow is a traffic monitoring technology. 

As shown in [Figure 1](#_Ref261533275), the
sFlow system involves an sFlow agent embedded in a device and a remote sFlow
collector. The sFlow agent collects interface counter information and packet
information and encapsulates the sampled information in sFlow packets. When the
sFlow packet buffer is full, or the aging timer (fixed to 1 second) expires,
the sFlow agent performs the following actions:

·     Encapsulates the sFlow packets in the UDP
datagrams. 

·     Sends the UDP datagrams to the specified sFlow
collector. 

The sFlow collector analyzes the information
and displays the results. One sFlow collector can monitor multiple sFlow agents.

sFlow provides the following sampling
mechanisms:

·     Flow sampling—Obtains packet information. 

·     Counter sampling—Obtains interface counter information. 

sFlow can use flow sampling and counter
sampling at the same time.

Figure 1 sFlow system

![](https://resource.h3c.com/en/202407/12/20240712_11705826_x_Img_x_png_0_2216127_294551_0.png)

‌

## Protocols and standards

·     RFC 3176, InMon
Corporation's sFlow: A Method for Monitoring Traffic in Switched and Routed
Networks

·     sFlow.org, sFlow Version 5

## Configuring basic sFlow information

#### Restrictions and guidelines

As a best practice, manually configure an
IP address for the sFlow agent. The device periodically checks whether the
sFlow agent has an IP address. If the sFlow agent does not have an IP address,
the device automatically selects an IPv4 address for the sFlow agent but does
not save the IPv4 address in the configuration file.

Only one IP address can be configured for
the sFlow agent on the device, and a newly configured IP address overwrites the
existing one.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Configure an IP address for the sFlow agent.

sflow agent { ip ipv4-address \| ipv6 ipv6-address }

By default, no IP address is configured
for the sFlow agent.

**3\.**Configure the sFlow collector information.

sflow collector collector-id \[ vpn-instance vpn-instance-name ] { ip ipv4-address \| ipv6 ipv6-address } \[ datagram-size size \| description string \| dscp dscp-value \| port port-number \| time-out seconds ] \*

By default, no sFlow collector
information is configured.

**4\.**Specify the source IP address of sFlow
packets.

sflow source { ip ipv4-address \| ipv6 ipv6-address } \*

By default, the source IP address is
determined by routing.

## Configuring flow sampling

#### About this task

Perform this task to configure flow
sampling on an Ethernet interface. The sFlow agent performs the following tasks:

**1\.**Samples packets on that interface according
to the configured parameters.

**2\.**Encapsulates the packets into sFlow packets.

**3\.**Encapsulates the sFlow packets in the UDP
packets and sends the UDP packets to the specified sFlow collector.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter Layer 2 Ethernet interface view or
Layer 3 Ethernet interface view.

interface interface-type
interface-number

**3\.**(Optional.) Set the flow sampling mode.

sflow sampling-mode random

By default, random sampling is used.

**4\.**Enable flow sampling and specify the number
of packets out of which flow sampling samples a packet on the interface.

sflow sampling-rate rate

By default, flow sampling is disabled.

As a best practice, set the sampling
interval to 2n that is greater than or equal to
8192, for example, 32768\.

**5\.**(Optional.) Set the maximum number of bytes
(starting from the packet header) that flow sampling can copy per packet.

sflow flow max-header length

The default setting is 128 bytes.

As a best practice, use the default
setting.

**6\.**Specify the sFlow instance and sFlow
collector for flow sampling.

sflow flow \[ instance instance-id ] collector collector-id

By default, no sFlow instance or sFlow
collector is specified for flow sampling.

## Configuring counter sampling

#### About this task

Perform this task to configure counter
sampling on an Ethernet interface. The sFlow agent performs the following tasks:

**1\.**Periodically collects the counter
information on that interface.

**2\.**Encapsulates the counter information into
sFlow packets.

**3\.**Encapsulates the sFlow packets in the UDP
packets and sends the UDP packets to the specified sFlow collector.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter Layer 2 Ethernet interface view or
Layer 3 Ethernet interface view.

interface interface-type
interface-number

**3\.**Enable counter sampling and set the counter
sampling interval.

sflow counter interval interval

By default, counter sampling is disabled.

**4\.**Specify the sFlow instance and sFlow
collector for counter sampling.

sflow counter \[ instance instance-id ] collector collector-id

By default, no sFlow instance or sFlow
collector is specified for counter sampling.

## Display and maintenance commands for sFlow

Execute display
commands in any view. 

 

| Task | Command |
| --- | --- |
| Display sFlow configuration. | display sflow |

‌

## sFlow configuration examples

### Example: Configuring sFlow

#### Network configuration

As shown in [Figure 2](#_Ref43196687), perform
the following tasks:

·     Configure flow sampling in random mode and
counter sampling on Ten-GigabitEthernet 1/0/1 of the device to
monitor traffic on the port. 

·     Configure the device to send sampled information
in sFlow packets through Ten-GigabitEthernet 1/0/3 to the sFlow
collector.

Figure 2 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705827_x_Img_x_png_1_2216127_294551_0.png)

‌

#### Procedure

**1\.**Configure VLANs, assign Ethernet interfaces
to VLANs, and configure IP addresses and subnet masks for VLAN interfaces, as
shown in [Figure 2](#_Ref43196687).
(Details not shown.)

**2\.**Configure the sFlow agent and configure
information about the sFlow collector:

\# Configure the IP address for the sFlow
agent.

\<Device\> system-view

\[Device] sflow agent ip 3.3.3.1

\# Configure information about the sFlow
collector. Specify the sFlow collector ID as 1, IP address as 3.3.3.2, port
number as 6343 (default), and description as netserver.

\[Device] sflow collector 1 ip 3.3.3.2
description netserver

**3\.**Configure counter sampling:

\# Enable counter sampling and set the
counter sampling interval to 120 seconds on Ten-GigabitEthernet 1/0/1.

\[Device] interface ten-gigabitethernet 1/0/1

\[Device-Ten-GigabitEthernet1/0/1] sflow counter
interval 120

\# Specify sFlow collector 1 for counter
sampling.

\[Device-Ten-GigabitEthernet1/0/1] sflow counter
collector 1

**4\.**Configure flow sampling:

\# Enable flow sampling and set the flow sampling
mode to random and sampling interval to 32767\.

\[Device-Ten-GigabitEthernet1/0/1] sflow sampling-mode
random

\[Device-Ten-GigabitEthernet1/0/1] sflow sampling-rate
32767

\# Specify sFlow collector 1 for flow
sampling.

\[Device-Ten-GigabitEthernet1/0/1] sflow flow collector
1

#### Verifying the configuration

\# Verify the following items:

·     Ten-GigabitEthernet 1/0/1 enabled with sFlow is active.

·     The counter sampling interval is 120 seconds. 

·     The flow sampling interval is 32767 (one packet
is sampled from every 32767 packets). 

\[Device-Ten-GigabitEthernet1/0/1] display sflow

sFlow datagram version: 5

Global information:

Agent IP: 3.3.3.1(CLI)

Source address:

Collector information:

ID    IP              Port 
Aging      Size VPN-instance Description       Dscp

1     3.3.3.2         6343 
N/A        1400              netserver         0

Port counter sampling information:

Interface   Instance   CID  
Interval(s)

XGE1/0/1    1          1     120

Port flow sampling information:

Interface   Instance   FID   MaxHLen
Rate       Mode      Status

XGE1/0/1       1          1     128    
32767      Random    Active

## Troubleshooting sFlow

### The remote sFlow collector cannot receive sFlow packets

#### Symptom

The remote sFlow collector cannot receive
sFlow packets.

#### Analysis

The possible reasons include:

·     The sFlow collector is not specified.

·     sFlow is not configured on the interface.

·     The IP address of the sFlow collector specified
on the sFlow agent is different from that of the remote sFlow collector.

·     No IP address is configured for the Layer 3
interface that sends sFlow packets.

·     An IP address is configured for the Layer 3 interface
that sends sFlow packets. However, the UDP datagrams with this source IP
address cannot reach the sFlow collector.

·     The physical link between the device and the
sFlow collector fails.

·     The sFlow collector is bound to a non-existent
VPN. 

·     The length of an sFlow packet is less than the
sum of the following two values:

¡     The length
of the sFlow packet header. 

¡     The number
of bytes that flow sampling can copy per packet.

#### Solution

To resolve the problem:

**1\.**Use the display sflow command to verify that sFlow is correctly configured.

**2\.**Verify that a correct IP address is
configured for the device to communicate with the sFlow collector.

**3\.**Verify that the physical link between the
device and the sFlow collector is up.

**4\.**Verify that the VPN bound to the sFlow
collector already exists.

**5\.**Verify that the length of an sFlow packet is
greater than the sum of the following two values: 

¡     The length
of the sFlow packet header. 

¡     The number
of bytes (as a best practice, use the default setting) that flow sampling can
copy per packet.

