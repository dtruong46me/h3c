
# EVPN overview

Ethernet Virtual Private Network (EVPN) is
a Layer 2 VPN technology that provides both Layer 2 and Layer 3 connectivity
between distant network sites across an IP network. EVPN uses MP-BGP in the
control plane and Virtual eXtensible LAN (VXLAN) in the data plane. EVPN is
typically used in data centers for multitenant services.

## EVPN VXLAN

As shown in [Figure 1](#_Ref14783831), EVPN
VXLAN uses the VXLAN technology for traffic forwarding in the data plane. The
transport edge devices assign VMs to different VXLANs, and then forward traffic
at Layer 2 between sites for VMs by using VXLAN tunnels. The transport edge
devices are VXLAN tunnel endpoints (VTEPs). All EVPN VXLAN processing is
performed on VTEPs

To provide Layer 3 connectivity between
subnets of a tenant and between the EVPN VXLAN network and external networks,
you can deploy EVPN gateways.

For more information about EVPN VXLAN, see
"Configuring EVPN VXLAN."

Figure 1 EVPN VXLAN network model

![](https://resource.h3c.com/en/202407/12/20240712_11705975_x_Img_x_png_0_2216146_294551_0.png)

 

## EVPN benefits

EVPN provides the following benefits:

·     Configuration automation—MP-BGP automates VTEP discovery, VXLAN tunnel establishment, and
VXLAN tunnel assignment to ease deployment.

·     Separation of the control
plane and the data plane—EVPN uses MP-BGP to
advertise host reachability information in the control plane and uses VXLAN to
forward traffic in the data plane. 

·     Integrated routing and bridging
(IRB)—MP-BGP advertises both Layer 2 and Layer 3
host reachability information to provide optimal forwarding paths and minimize
flooding in an EVPN VXLAN network.

·     Point-to-point and point-to-multipoint
connection—Layer 2 frames are transmitted
transparently across the IP transport network between sites after they are
encapsulated into VXLAN packets.

## Layered transport network

As shown in [Figure 2](#_Ref452367763),
typically the EVPN transport network uses a layered structure. On the transport
network, leaf nodes act as VTEPs to provide VXLAN services, and spine nodes perform
forwarding for VXLAN traffic based on the outer IP header. If all VTEPs and
transport network devices of an EVPN network belong to the same AS, the spine
nodes can act as route reflectors (RRs) to reflect routes between the VTEPs. In
this scenario, the spine nodes advertise and receive BGP EVPN routes, but do
not perform VXLAN encapsulation and de-encapsulation. 

Figure 2 Layered transport network

![](https://resource.h3c.com/en/202407/12/20240712_11705976_x_Img_x_png_1_2216146_294551_0.png)

 

## MP-BGP extension for EVPN

To support EVPN, MP-BGP introduces the EVPN
subsequent address family under the L2VPN address family and the following network
layer reachability information (BGP EVPN routes):

·     Ethernet auto-discovery route—Advertises ES information in multihomed sites.

·     MAC/IP advertisement route—Advertises MAC reachability information and host route information
(host ARP or ND information). 

·     Inclusive multicast Ethernet
tag (IMET) route—Advertises VTEP and VXLAN
mappings for automating VTEP discovery, VXLAN tunnel establishment, and VXLAN
tunnel assignment in an EVPN VXLAN network. 

·     Ethernet segment route—Advertises ES and VTEP mappings.

·     IP prefix advertisement
route—Advertises BGP IPv4 or IPv6 unicast routes
as IP prefixes.

·     Selective multicast
Ethernet tag (SMET) route—Advertises IGMP
multicast group information among VTEPs in an EVPN network. A VTEP advertises
an SMET route only when receiving a membership report for an IGMP multicast
group for the first time. The VTEP does not advertise an SMET route if
subsequent membership reports for the multicast group use the same IGMP version
as the first membership report.

·     IGMP join synch route—Advertises IGMP membership reports among redundant VTEPs for an ES.

·     IGMP leave synch route—Advertises IGMP leave group messages for withdrawal of IGMP join
synch routes among redundant VTEPs for an ES.

MP-BGP uses the route distinguisher (RD)
field to differentiate BGP EVPN routes of different VSIs and uses route targets
to control the advertisement and acceptance of BGP EVPN routes. MP-BGP supports
the following types of route targets:

·     Export target—A VTEP sets the export targets for BGP EVPN routes learned from the
local site before advertising them to remote VTEPs.

·     Import target—A VTEP checks the export targets of BGP EVPN routes received from
remote VTEPs. The VTEP imports the BGP EVPN routes only when their export
targets match the local import targets.

## RD and route target selection of BGP EVPN routes

As shown in [Table 1](#_Ref459999730), you can
configure RDs and route targets for BGP EVPN routes in multiple views.

Table 1 Supported views for RD and route
target configuration

| Item | Views |
| --- | --- |
| RD | ·     EVPN instance view  ·     VSI EVPN instance view  ·     VPN instance view  ·     Public instance view || Route targets | ·     EVPN instance view  ·     VSI EVPN instance view  ·     VPN instance view  ·     VPN instance IPv4 address family view  ·     VPN instance IPv6 address family view  ·     VPN instance EVPN view  ·     Public instance view  ·     Public instance IPv4 address family view  ·     Public instance IPv6 address family view  ·     Public instance EVPN view  NOTE:  Route targets configured in VPN instance view apply to IPv4 VPN, IPv6 VPN, and EVPN. Route targets configured in IPv4 address family view apply only to IPv4 VPN. Route targets configured in IPv6 address family view apply only to IPv6 VPN. Route targets configured in VPN instance EVPN view apply only to EVPN. Route targets configured in IPv4 address family view, IPv6 address family view, or VPN instance EVPN view take precedence over those in VPN instance view. The precedence order for different views of a VPN instance also applies to the views of the public instance. |


 

The device selects RDs and route targets
for BGP EVPN routes by using the following rules:

·     Ethernet auto-discovery routes—The device uses the RD and route targets configured in VSI EVPN
instance view when advertising the routes. The device uses the route targets
configured in VSI EVPN instance view when accepting the routes.

·     IMET routes and MAC/IP
advertisement routes that contain only MAC addresses—The device uses the RD and route targets configured in VSI EVPN
instance view when advertising. The device uses the route targets configured in
VSI EVPN instance view when accepting the routes.

·     MAC/IP advertisement routes
that contain ARP or ND information—The device
uses the following settings when advertising the routes:

¡     RD
and export route targets configured in VSI EVPN instance view.

¡     Export
route targets configured for EVPN on a VPN instance or the public instance (VPN
instance view, EVPN view of a VPN instance or the public instance, and public
instance view).

The device uses the import route targets
configured for EVPN on a VPN instance or the public instance when accepting the
routes.

·     ES routes—The device uses the RD and export route targets configured in VSI
EVPN instance view when advertising the routes. The device uses the import
route targets configured in VSI EVPN instance view when accepting the routes.

·     IP prefix advertisement
routes—The device uses the route targets
configured for the IPv4 or IPv6 address family on a VPN instance or the public
instance when advertising and accepting the routes.

 

