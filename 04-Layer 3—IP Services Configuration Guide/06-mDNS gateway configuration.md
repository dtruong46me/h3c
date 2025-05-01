
# Configuring mDNS gateway

## About mDNS gateway

A Multicast Domain Name System (mDNS) gateway
can be deployed in a large-scale network to enable inter-VLAN or inter-VSI mDNS
service discovery. It records a list of available services in the network, responds
to service queries from mDNS clients, and performs active mDNS service
discovery as well as access control.

### mDNS network model

As shown in [Figure 1](#_Ref45218650), an mDNS
network contains mDNS endpoint devices, mDNS service providers, the mDNS
gateway, and mDNS relays.

Figure 1 mDNS networking model

![](https://resource.h3c.com/en/202407/12/20240712_11704631_x_Img_x_png_0_2216000_294551_0.png)

 

#### mDNS endpoint device

An mDNS endpoint device obtains services
from mDNS service providers. Examples are iPhones and iPads.

#### mDNS service provider

An mDNS service provider provides services
for mDNS endpoint devices. Examples are mDNS-capable printers and Apple TVs.

#### mDNS gateway

The mDNS gateway records available services
advertised by mDNS service providers and responds to queries from mDNS endpoint
devices. With mDNS gateway enabled, Device B in [Figure 1](#_Ref45218650) can act
as the mDNS gateway to help mDNS endpoint devices discover mDNS service
providers in a different VLAN from the endpoint devices.

#### mDNS relay

An mDNS relay ensures successful mDNS
communication when mDNS endpoint devices and mDNS service providers are in
different subnets.

### mDNS gateway functionalities

The mDNS gateway provides the following functionalities
based on mDNS policies:

·     Reply mDNS queries.

·     Discover mDNS services.

An mDNS gateway policy defines user VLAN or
VSI and service VLAN or VSI mappings to ensure that endpoint devices in
specific VLANs or VSIs can access only service providers in specific service VLANs
or VSIs.

#### Replying mDNS queries

Upon receiving mDNS queries from mDNS endpoint
devices, the gateway checks its service list and sends responses that carry
service information to the endpoint devices if matches are found.

Figure 2 Replying mDNS queries

![](https://resource.h3c.com/en/202407/12/20240712_11704632_x_Img_x_png_1_2216000_294551_0.png)

 

As shown in [Figure 2](#_Ref45354071), the
mDNS gateway replies mDNS queries in a VLAN-separated network as follows:

**1\.**mDNS service providers broadcast mDNS service
responses to notify services.

**2\.**Upon receiving the responses, the mDNS
gateway creates an mDNS service entry that records information such as service
instance name and service type.

**3\.**The mDNS endpoint device sends an mDNS query
to the mDNS gateway.

**4\.**The mDNS gateway searches its service list
for the requested service.

¡     If a
match is found, the mDNS gateway sends a response to the endpoint device
directly.

¡     If
no match is found, the mDNS gateway starts service discovery in the
corresponding service VLANs of the user VLAN in which the endpoint device
resides.

**5\.**The mDNS endpoint device obtains information
about the service provider from the response.

#### Discovering mDNS services

The mDNS gateway discovers mDNS services by
listening to mDNS service responses sent by mDNS service providers and then records
information about the services as entries. Service providers can broadcast
service responses actively or reply service responses upon requests.

After receiving a query to a service that
is not in the service list, the mDNS gateway forwards the query in the
corresponding service VLANs of the user VLAN. Upon receiving the query, service
providers that provide the service will send responses.

Figure 3 Discovering mDNS services

![](https://resource.h3c.com/en/202407/12/20240712_11704633_x_Img_x_png_2_2216000_294551_0.png)

 

As shown in [Figure 3](#_Ref45355315), the
mDNS gateway discovers services as follows:

**1\.**The mDNS gateway receives a query for the
printing service from the mDNS endpoint device (iPad).

**2\.**The mDNS gateway searches its service list
for the printing service and no match is found. 

**3\.**The mDNS gateway forwards the mDNS query to
the corresponding service VLANs.

**4\.**Upon receiving the query, the printer sends
an mDNS response.

**5\.**Upon receiving the response, the mDNS
gateway records information about the service and sends a response to the mDNS
endpoint device.

### Protocols and standards

RFC 6762, Multicast
DNS

## mDNS gateway tasks at a glance

To configure the mDNS gateway, perform the
following tasks:

**1\.**[Enabling mDNS gateway](#_Ref45532088)

**2\.**(Optional.) [Configuring an mDNS gateway policy](#_Ref45532092)

**3\.**(Optional.) [Specifying the permitted service types for
entry learning](#_Ref45532096)

**4\.**(Optional.) [Specifying trusted mDNS devices](#_Ref45532099)

**5\.**(Optional.) [Specifying the source address for mDNS packets](#_Ref45532103)

**6\.**(Optional.) [Enabling mDNS gateway periodic probing](#_Ref45532107)

## Enabling mDNS gateway

**1\.**Enter system view.

system-view

**2\.**Enable mDNS gateway.

mdns gateway enable

By default, mDNS gateway
is disabled.

## Configuring an mDNS gateway policy

#### Restrictions and guidelines

With mDNS gateway policies configured, the
mDNS gateway processes only the queries of mDNS endpoint devices in user VLANs
or VSIs configured in the policies.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Create an mDNS gateway policy and enter its view.

mdns gateway policy policy-name

**3\.**Specify service VLANs.

service vlan vlan-id-list

By default, no
service VLAN is specified.

**4\.**Specify service VSIs.

service vsi vsi-name

By default, no
service VSI is specified.

**5\.**Specify user VLANs.

user vlan vlan-id-list

By default, no
user VLAN is specified.

**6\.**Specify user VSIs.

user vsi vsi-name

By default, no
user VSI is specified.

## Specifying the permitted service types for entry learning

#### About this task

By default, the mDNS gateway can learn all
mDNS services of a predefined type, which can be memory-consuming if a large
number of service providers exist. You can perform this task to specify the
permitted service types that can be learned by the mDNS gateway for the gateway
to learn only required services.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Specify a permitted service type for entry
learning.

mdns gateway permit service-type { predefined-service-type \| user-defined
user-defined-service-type } \[ max-entry-count count ]

By default, the
mDNS gateway can learn all mDNS services of a pre-defined type.

## Specifying trusted mDNS devices

#### About this task

To prevent security issues and resource
waste caused by attacks performed by rogue mDNS devices, you can specify trustworthy
mDNS relays as trusted mDNS devices. With trusted devices specified, the mDNS gateway
processes only unicast queries from the trusted mDNS devices.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Specify a trusted mDNS device.

mdns gateway trust device-ip ip-address \[ vpn-instance vpn-instance-name ]

By default, no trusted
device is specified.

## Specifying the source address for mDNS packets

### About specifying the source address for mDNS packets

For responses destined for endpoint devices
and mDNS queries and probe queries sent within a VLAN or VSI, the default
source IP address is the primary IP address of the VLAN interface or VSI
interface. If no IP address is configured for the VLAN interface or VSI
interface, the mDNS gateway cannot send out the queries.

If the mDNS gateway is connected to a large
number of VLANs or VSIs, configuring an IP address to each VLAN interface or
VSI interface wastes IP address resources. You can use this command to specify
a specific IP address for the mDNS gateway to use.

### Specifying the source address for mDNS packets in a VLAN

**1\.**Enter system view.

system-view

**2\.**Enter VLAN view.

vlan vlan-id

**3\.**Specify the source address for mDNS packets.

mdns gateway source ip { interface interface-type
interface-number \| ip-address }

By default, the source IP address is the primary IP address of the VLAN interface.

### Specifying the source address for mDNS packets in a VSI

**1\.**Enter system view.

system-view

**2\.**Enter VSI view.

vsi vsi-id

**3\.**Specify the source address for mDNS packets.

mdns gateway source ip { interface interface-type
interface-number \| ip-address }

By default, the source IP address is the primary IP address of the VSI interface.

## Enabling mDNS gateway periodic probing

### About mDNS gateway periodic probing

mDNS service providers broadcast their
services at network association. For the mDNS gateway to discover services offered
by service providers that accessed the network earlier than the gateway, enable
mDNS gateway periodic probing.

This feature enables the mDNS gateway to
send mDNS probe queries at intervals. Each mDNS service provider sends a
response to the gateway after receiving the query. The mDNS gateway can learn
the latest service information of all mDNS service providers in the network in
a timely manner.

### Enabling mDNS gateway periodic probing in a VLAN

**1\.**Enter system view.

system-view

**2\.**Enter VLAN view.

vlan vlan-id

**3\.**Enable mDNS gateway periodic probing.

mdns gateway probe interval interval

By default, mDNS
gateway periodic probing is disabled.

### Enabling mDNS gateway periodic probing in a VSI

**1\.**Enter system view.

system-view

**2\.**Enter VSI view.

vsi vsi-id

**3\.**Enable mDNS gateway periodic probing.

mdns gateway probe interval interval

By default, mDNS
gateway periodic probing is disabled.

## Display and maintenance commands for mDNS relay

Execute display
commands in any view and reset commands in user
view.

 

| Task | Command || Display mDNS gateway configuration. | display mdns gateway || Display mDNS gateway policy information. | display mdns gateway policy \[ policy-name ] || Display information about services learned by the mDNS gateway. | display mdns gateway service { all \| predefined-service-type \| user-defined user-defined-service-type \| vlan vlan-id \| vsi vsi-name } || Display mDNS gateway packet statistics. | display mdns gateway statistics || Clear information about services learned by the mDNS gateway. | reset mdns gateway service { all \| predefined-service-type \| user-defined user-defined-service-type \| vlan vlan-id \| vsi vsi-name } || Clear mDNS gateway packet statistics. | reset mdns gateway statistics |






| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |

 

## mDNS gateway configuration examples

### Example: Configuring mDNS gateway

#### Network configuration

As shown in [Figure 4](#_Ref528936991), a
campus network uses mDNS to provide services to both teachers and students. Configure
mDNS gateway to allow students to access only the printer and allow teachers to
access both the Apple TV and the printer.

Enable mDNS gateway periodic probing for
the gateway to update mDNS services in time.

Figure 4 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11704634_x_Img_x_png_3_2216000_294551_0.png)

 

#### Procedure

**1\.**Enable mDNS gateway.

\<Device\> system-view

\[Device] mdns gateway enable

**2\.**Create an mDNS gateway policy named student.

\[Device] mdns gateway policy
student

**3\.**Specify VLAN 10 as a user VLAN and VLAN 30
as a service VLAN.

\[Device-mdns-gateway-policy-student]
user vlan 10

\[Device-mdns-gateway-policy-student]
service vlan 30

\[Device-mdns-gateway-policy-student]
quit

**4\.**Create an mDNS gateway policy named teacher.

\[Device] mdns gateway policy teacher

**5\.**Specify VLAN 20 as a user VLAN and VLANs 30
and 40 as service VLANs.

\[Device-mdns-gateway-policy-teacher]
user vlan 20

\[Device-mdns-gateway-policy-teacher]
service vlan 30 40

\[Device-mdns-gateway-policy-teacher]
quit

**6\.**Permit service types airplay, raop, and printer in entry
learning.

\[Device] mdns gateway permit
service-type airplay

\[Device] mdns gateway permit
service-type raop

\[Device] mdns gateway permit
service-type printer

**7\.**Specify the source address for mDNS packets
in VLAN 10\.

\[Device] vlan 10

\[Device-vlan10] mdns gateway
source ip 192.168.1.1

\[Device-vlan10] quit

**8\.**Specify the source address for mDNS packets
in VLAN 20\.

\[Device] vlan 20

\[Device-vlan20] mdns gateway
source ip 192.168.2.1

\[Device-vlan20] quit

**9\.**Enable mDNS gateway periodic probing in VLAN
30 and set the probing interval to 600 seconds.

\[Device] vlan 30

\[Device-vlan30] mdns gateway
probe interval 600

\[Device-vlan30] quit

**10\.**Enable mDNS gateway periodic probing in VLAN
40 and set the probing interval to 600 seconds.

\[Device] vlan 40

\[Device-vlan40] mdns gateway
probe interval 600

\[Device-vlan40] quit

#### Verifying the configuration

\# Display mDNS gateway configuration.

\[Device] display mdns gateway

mDNS gateway status: Enabled

mDNS gateway source IP:

  VLAN 10: 192.168.1.1

  VLAN 20: 192.168.2.1

mDNS gateway probe is enabled in the
following VLANs: 30, 40

mDNS gateway probe is enabled in the
following VSIs:

\# Display information about services
learned by the mDNS gateway.

\[Device] display mdns gateway service
all

Instance name          Service type
VLAN/VSI name TTL        Pro IP

Apple TV               airplay     
40            120        TCP 192.168.2.100

Apple TV               raop        
40            120        TCP 192.168.2.100

Printer               
printer      30            120        TCP 192.168.1.100

 

Total mDNS gateway services: 3

\# Verify that students can access only the
printer and teachers can access both the Apple TV and the printer.

## Troubleshooting mDNS gateway

### Failure to receive packets from an mDNS relay

#### Symptom

The mDNS gateway cannot receive mDNS
packets from an mDNS relay.

#### Analysis

Possible reasons are:

·     The mDNS gateway feature is not enabled.

·     Trusted mDNS devices are configured, but the mDNS relay is not a trusted
device.

#### Solution

To resolve this issue:

**1\.**Execute the display mdns gateway command to view current configuration. If mDNS gateway is not enabled, execute the mdns gateway
enable command to enable this feature.

**2\.**Execute the display current-configuration command to view trusted mDNS devices. If the
mDNS relay is not in the list,
execute the mdns gateway trust device-ip command to specify the mDNS relay as a
trusted device.

