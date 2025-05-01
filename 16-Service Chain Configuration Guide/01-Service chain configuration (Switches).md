
# Configuring service chains

## Overview

Service chain is a forwarding technology
used to guide network traffic through service nodes. It is based on the Overlay
technology and combines the software defined network (SDN) centralized
management theory. You can configure service chains by using a virtual
converged framework controller (VCFC).

A switch in a service chain can act as a
proxy node or an access point.

## Service chain functions

Service chain implements the following
functions:

·     Decoupling the tenant logical network and the
physical network, and separating the control plane from the forwarding plane.

·     Service resource allocation and deployment on
demand with no physical topology restrictions.

·     Dynamic creation and automatic deployment of
network function virtualization (NFV) resource pools.

·     Tenant-specific service arrangement and
modification without affecting the physical topology and other tenants.

## Node types

A service chain can have the following
types of nodes:

·     Proxy node—A switch that uses service chain policies deployed by a VCFC to
determine whether to forward a packet into a service chain.

·     Access point—A switch that acts as a VXLAN tunnel end point (VTEP). It uses
routing policies deployed by the VCFC to verify whether a packet is qualified
to enter a service chain. If the packet is qualified, the access point
encapsulates the packet into a VXLAN packet.

·     Service node—A physical device or a NFV device that applies services to the received
traffic. A service chain can contain multiple service nodes.

## Nodes supported by the switches

A switch can act as a proxy node or an
access point, but it cannot act as a service node.

## Using a switch as a proxy node

### Network framework

[Figure 1](#_Ref448321389) shows the framework of a service chain network with a switch acting
as a proxy node.

Figure 1 Network framework

![](https://resource.h3c.com/en/202407/12/20240712_11706049_x_Img_x_png_0_2216152_294551_0.png)

 

### How it works

The VCFC deploys service chain policies to
the proxy node based on different tenant applications. The proxy node uses the
service chain policies to perform packet forwarding and service chain
processing as follows:

**1\.**When the proxy node receives an IP packet,
it uses a service chain policy to verify whether the packet is qualified to
enter a service chain. 

¡     If
the packet is qualified, the proxy node forwards the packet to a service node.

¡     If
the packet is not qualified, the proxy node forwards the packet without service
chain processing.

**2\.**When the service node receives the packet,
it processes the packet and then forwards it back to the proxy node.

**3\.**When the proxy node receives the packet, it verifies
whether the packet is qualified to enter another service chain. 

This procedure is repeated so that the
packet can be processed by different service nodes.

## Using a switch as an access point

### Network framework

[Figure 2](#_Ref448150608) shows the framework of a service chain network with switches acting
as access points.

Figure 2 Network framework

![](https://resource.h3c.com/en/202407/12/20240712_11706050_x_Img_x_png_1_2216152_294551_0.png)

 

### Packet format

[Figure 3](#_Ref419463945) shows the format of a VXLAN packet that carries service chain
information.

Figure 3 Packet format

![](https://resource.h3c.com/en/202407/12/20240712_11706051_x_Img_x_png_2_2216152_294551_0.png)

 

A service chain uses the following fields
in the VXLAN header to identify packets:

·     Flags—When the S bit is set to 1, the Service chain field is valid. When the S bit is set to 0, the Service chain
field is invalid.

·     Service chain—A 24-bit field that includes the D bit
and service path ID. When the D bit is set to 0,
the packet is a forward packet. When the D bit is
set to 1, the packet is a reverse packet. The 23-bit service path ID is used to
identify a service chain.

### How it works

The VCFC deploys routing policies to access
points and service nodes based on different tenant applications. The access
points and the service nodes use the routing policies to perform packet
forwarding and service chain processing as follows:

**1\.**When an access point receives an IP packet,
it uses the routing policy to verify whether the packet is qualified to enter a
service chain. 

¡     If
the packet is qualified, the access point encapsulates the packet into a VXLAN
packet and adds service chain information to the VXLAN header. The packet is then
forwarded to a service node.

¡     If the
packet is not qualified, the access point forwards the packet without service
chain processing.

**2\.**When the service node receives the VXLAN
packet, it performs the following tasks:

**a.**Decapsulates the packet.

**b.**Performs a local lookup for a service chain
matching the service path ID in the VXLAN header of the packet. If a match is
found, the service node applies the service to the packet.

**c.**Encapsulates the packet into a VXLAN packet
and adds service chain information to the VXLAN header of the packet according
to the routing policy.

**d.**Forwards the packet to the next service
node.

**3\.**After the last service node completes
processing the packet, it encapsulates the packet into a VXLAN packet without
adding service chain information to the VXLAN header. The packet is then
forwarded to the peer access point.

## Configuring a service chain

You can configure a service chain only by
using a VCFC. For more information, see the VCFC configuration guide.

 

