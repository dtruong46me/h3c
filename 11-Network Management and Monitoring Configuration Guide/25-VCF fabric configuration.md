
# Configuring VCF fabric

## About VCF fabric

Based on OpenStack Networking (Neutron),
the Virtual Converged Framework (VCF) solution provides virtual network
services from Layer 2 to Layer 7 for cloud tenants. This solution breaks the boundaries
between the network, cloud management, and terminal platforms and transforms the
IT infrastructure to a converged framework to accommodate all applications. It
also implements automated topology discovery and automated deployment of underlay
networks and overlay networks to reduce the administrators' workload and speed
up network deployment and upgrade.

### VCF fabric topology

#### Topology for a 2-layer data center VCF fabric

In a 2-layer data center VCF fabric, a
device has one of the following roles:

·     Spine node—Connects to leaf nodes.

·     Leaf node—Connects to servers.

·     Border node—Located at the border of a VCF fabric to provide access to the
external network.

Spine nodes and leaf nodes form a large Layer
2 network, which can be a VLAN, a VXLAN with a centralized IP gateway, or a
VXLAN with distributed IP gateways. For more information about centralized IP gateways
and distributed IP gateways, see VXLAN Configuration
Guide.

Figure 1 Topology for a 2-layer data centerVCF fabric

![](https://resource.h3c.com/en/202407/12/20240712_11705837_x_Img_x_png_0_2216130_294551_0.png)

 

#### Topology for a 3-layer data center VCF fabric

In a 3-layer data center VCF fabric, a
device has one of the following roles:

·     Spine node—Connects to aggregate nodes.

·     Aggregate node—Resides on the distribution layer and is located between leaf nodes
and spine nodes.

·     Leaf node—Connects to servers.

·     Border node—Located at the border of a VCF fabric to provide access to the
external network.

OSPF runs on the Layer 3 networks between
the spine and aggregate nodes and between the aggregate and leaf nodes. VXLAN is
used to set up the Layer 2 overlay network.

Figure 2 Topology for a 3-layer data center VCF fabric

![](https://resource.h3c.com/en/202407/12/20240712_11705838_x_Img_x_png_1_2216130_294551_0.png)

 

#### Topology for a 4-layer data center VCF fabric

In a 4-layer data center VCF fabric, a
device has one of the following roles:

·     Spine node—Connects to aggregate nodes.

·     Aggregate node—Located between leaf nodes and spine nodes.

·     Leaf node—Located between aggregate nodes and access nodes.

·     Access node—An access device, which connects to an upstream leaf node and a
downstream server.

·     Border node—Located at the border of a VCF fabric to provide access to the
external network.

OSPF runs on the Layer 3 networks between
the spine and aggregate nodes and between the aggregate and leaf nodes. VXLAN
is used to set up the Layer 2 overlay network.

Figure 3 Topology for a 4-layer data center VCF fabric

![](https://resource.h3c.com/en/202407/12/20240712_11705839_x_Img_x_png_2_2216130_294551_0.png)

 

#### VCF fabric topology for a campus network

In a campus VCF fabric, a device has one of
the following roles:

·     Spine node—Connects to leaf nodes.

·     Leaf node—Connects to access nodes. 

·     Access node—Connects to an upstream leaf node and downstream terminal devices.
Cascading of access nodes is supported.

·     Border node—Located at the border of a VCF fabric to provide access to the
external network.

Spine nodes and leaf nodes form a large
Layer 2 network, which can be a VLAN, a VXLAN with a centralized IP gateway, or
a VXLAN with distributed IP gateways. For more information about centralized IP
gateways and distributed IP gateways, see VXLAN
Configuration Guide.

Figure 4 VCF fabric topology for a campus
network

![](https://resource.h3c.com/en/202407/12/20240712_11705840_x_Img_x_png_3_2216130_294551_0.png)

 

### Neutron overview

#### Neutron concepts and components

Neutron is a component in OpenStack
architecture. It provides networking services for VMs, manages virtual network
resources (including networks, subnets, DHCP, virtual routers), and creates an
isolated virtual network for each tenant. Neutron provides a unified network
resource model, based on which VCF fabric is implemented.

The following are basic concepts in
Neutron:

·     Network—A virtual object that can be created. It provides an independent network
for each tenant in a multitenant environment. A network is equivalent to a
switch with virtual ports which can be dynamically created and deleted.

·     Subnet—An address pool that contains a group of IP addresses. Two different
subnets communicate with each other through a router.

·     Port—A connection port. A router or a VM connects to a network through a
port.

·     Router—A virtual router that can be created and deleted. It performs
routing selection and data forwarding.

Neutron has the following components:

·     Neutron server—Includes the daemon process neutron-server and multiple plug-ins (neutron-\*-plugin).
The Neutron server provides an API and forwards the API calls to the configured
plugin. The plug-in maintains configuration data and relationships between
routers, networks, subnets, and ports in the Neutron database.

·     Plugin agent (neutron-\*-agent)—Processes data packets on virtual networks. The choice of plug-in
agents depends on Neutron plug-ins. A plug-in agent interacts with the Neutron
server and the configured Neutron plug-in through a message queue.

·     DHCP agent (neutron-dhcp-agent)—Provides DHCP services for tenant networks.

·     L3 agent (neutron-l3-agent)—Provides Layer 3 forwarding services to enable inter-tenant
communication and external network access.

#### Neutron deployment

Neutron needs to be deployed on servers and
network devices.

[Table 1](#_Ref480363412) shows
Neutron deployment on a server.

Table 1 Neutron deployment on a server

| Node | Neutron components |
| --- | --- |
| Controller node | ·     Neutron server  ·     Neutron DB  ·     Message server (such as RabbitMQ server)  ·     ML2 Driver || Network node | ·     neutron-openvswitch-agent  ·     neutron-dhcp-agent || Compute node | ·     neutron-openvswitch-agent  ·     LLDP |



 

[Table 2](#_Ref480363474) shows
Neutron deployments on a network device.

Table 2 Neutron deployments on a network
device

| Network type | Network device | Neutron components |
| --- | --- | --- |
| Centralized VXLAN IP gateway deployment | Spine | ·     neutron-l2-agent  ·     neutron-l3-agent || Leaf | neutron-l2-agent || Distributed VXLAN IP gateway deployment | Spine | N/A || Leaf | ·     neutron-l2-agent  ·     neutron-l3-agent |




 

Figure 5 Example of Neutron deployment for centralized gateway deployment

![](https://resource.h3c.com/en/202407/12/20240712_11705841_x_Img_x_png_4_2216130_294551_0.png)

 

Figure 6 Example of Neutron deployment for distributed gateway deployment

![](https://resource.h3c.com/en/202407/12/20240712_11705842_x_Img_x_png_5_2216130_294551_0.png)

### Automated VCF fabric deployment

VCF provides the following features to ease
deployment:

·     Automated topology discovery.

In a VCF fabric, each device uses LLDP to
collect local topology information from directly-connected peer devices. The
local topology information includes connection interfaces, roles, MAC
addresses, and management interface addresses of the peer devices. If multiple
spine nodes exist in an IPv4 VCF fabric, the master spine node collects the
topology for the entire network. In an IPv6 VCF fabric, the controller collects
the topology automatically.

·     Automated underlay network deployment.

Automated underlay network deployment sets
up a Layer 3 underlay network (a physical Layer 3 network) for users. It is
implemented by automatically executing configurations (such as IRF configuration
and Layer 3 reachability configurations) in user-defined template files.

·     Automated overlay network deployment.

Automated overlay network deployment sets
up an on-demand and application-oriented overlay network (a virtual network
built on top of the underlay network). It is implemented by automatically obtaining
the overlay network configuration (including VXLAN and EVPN configuration) from
the Neutron server.

### Process of automated VCF fabric deployment

The device finishes automated VCF fabric
deployment as follows:

**1\.**Starts up without loading configuration and
then obtains an IP address, the IP address of the TFTP server, and a template
file name from the DHCP server.

**2\.**Determines the name of the template file to
be downloaded based on the device role and the template file name obtained from
the DHCP server. For example, 1\_leaf.template
represents a template file for leaf nodes.

**3\.**Downloads the template file from the TFTP
server.

**4\.**Parses the template file and performs the
following operations:

¡     Deploys
static configurations that are independent from the VCF fabric topology.

¡     Deploys
dynamic configurations according to the VCF fabric topology.

The topology process notifies the
automation process of creation, deletion, and status change of neighbors. Based
on the topology information, the automation process completes role discovery, IRF
fabric setup, and automatic aggregation.

### Template file

A template file contains the following contents:

·     System-predefined variables—The variable names cannot be edited, and the variable values are
set by the VCF topology discovery feature.

·     User-defined variables—The variable names and values are defined by the user. These
variables include the username and password used to establish a connection with
the RabbitMQ server, network type, and so on. The following are examples of
user-defined variables:

¡     IPv4:

#USERDEF

\_underlayIPRange \=
10.100.0.0/16

\_master\_spine\_mac \=
1122-3344-5566

\_username \= aaa

\_password \= hello12345

\_rbacUserRole \= network-admin

\_neutron\_username \= openstack

\_neutron\_password \= 12345678

\_neutron\_ip \= 172.16.1.136

\_loghost\_ip \= 172.16.1.136

\_network\_type \=
centralized-vxlan

…

¡     IPv6:

#USERDEF

\_username \= aaa

\_password \= hello12345

\_rbacUserRole \= network-admin

\_loghost\_ip \= 200:0:0:0:0:0:0:210

\_network\_type \=
distributed-vxlan

\_OOB \= True

\_lagg\_enable \= false

\_lagg\_mode \= dynamic

…

·     Static configurations—Static configurations are independent from the VCF fabric topology
and can be directly executed. The following are examples of static
configurations:

#STATICCFG

\#

 clock timezone beijing add
08:00:00

\#

 lldp global enable

\#

 stp global enable

\#

·     Dynamic configurations—Dynamic configurations are dependent on the VCF fabric topology. The
device first obtains the topology information through LLDP and then executes
dynamic configurations. The following are examples of dynamic configurations:

\#

interface $$\_underlayIntfDown

 port link-mode route

 ip address unnumbered
interface LoopBack0

 ospf 1 area 0.0.0.0

 ospf network-type p2p

 lldp management-address
arp-learning

 lldp tlv-enable basic-tlv
management-address-tlv interface LoopBack0 

\#

## Restrictions: Hardware compatibility with VCF fabric

The following switch series do not support
VCF fabric:

·     S6520X-SI

·     S6520-SI

·     S5000-EI

·     MS4600

## VCF fabric task at a glance

To configure a VCF fabric, perform the
following tasks:

·     [Configuring automated VCF fabric deployment](#_Ref480377533)

No tasks are required to be made on the
device for automated VCF fabric deployment. However, you must make related
configuration on the DHCP server and the TFTP server so the device can download
and parse a template file to complete automated VCF fabric deployment.

·     (Optional.) Adjust VCF fabric deployment

If the device cannot obtain or parse the
template file to complete automated VCF fabric deployment, choose the following
tasks as needed:

¡     [Enabling VCF fabric topology discovery](#_Ref480365476)

¡     [Configuring automated underlay network
deployment](#_Ref480377534)

¡     [Configuring automated overlay network
deployment](#_Ref452209127)

## Configuring automated VCF fabricdeployment

#### Restrictions and guidelines

On a campus network, links between two
access nodes cascaded through GigabitEthernet interfaces and links between leaf
nodes and access nodes are automatically aggregated. For links between spine
nodes and leaf nodes, the trunk permit vlan command
is automatically executed.

On a campus network where multiple leaf
nodes that have an access node attached form an IRF fabric, make sure only one
link exists between each leaf node and its connected access node.

Do not perform link migration when devices
in the VCF fabric are in the process of coming online or powering down after
the automated VCF fabric deployment finishes. A violation might cause
link-related configuration fails to update.

The version format of a template file for automated
VCF fabric deployment is x.y. Only the x part is
examined during a version compatibility check. For successful automated
deployment, make sure x in the version of the
template file to be used is not greater than x in
the supported version. To display the supported version of the template file
for automated VCF fabric deployment, use the display
vcf-fabric underlay template-version command.

If the template file does not include IRF
configurations, the device does not save the configurations after executing all
configurations in the template file. To save the configurations, use the save command.

Two devices with the same role can
automatically set up an IRF fabric only when the IRF physical interfaces on the
devices are connected.

Two IRF member devices in an IRF fabric use
the following rules to elect the IRF master during automated VCF fabric
deployment:

·     If the uptime of both devices is shorter than
two hours, the device with the higher bridge MAC address becomes the IRF
master.

·     If the uptime of one device is equal to or
longer than two hours, that device becomes the IRF master.

·     If the uptime of both devices are equal to or
longer than two hours, the IRF fabric cannot be set up. You must manually
reboot one of the member devices. The rebooted device will become the IRF
subordinate.

If the IRF member ID of a device is not 1,
the IRF master might reboot during automatic IRF fabric setup.

#### Procedure

**1\.**Finish the underlay network planning (such
as IP address assignment, reliability design, and routing deployment) based on
user requirements.

**2\.**Configure the DHCP server.

Configure the IP address of the device,
the IP address of the TFTP server, and names of template files saved on the
TFTP server. For more information, see the user manual of the DHCP server.

**3\.**Configure the TFTP server.

Create template files and save the
template files to the TFTP server.

The H3C DR1000 ADDC or ADCAM network
management software can automatically create template files and save the files
to the TFTP server. If no H3C DR1000 ADDC or ADCAM is available on the network,
you must manually create template files and save the files to the TFTP server.
For more information about template files, see "[Template file](#_Ref480372983)."

**4\.**(Optional.) Configure the NTP server.

**5\.**Connect the device to the VCF fabric and
start the device.

After startup, the device uses a
management Ethernet interface or VLAN-interface 1 to connect to the fabric
management network. Then, it downloads the template file corresponding to its
device role and parses the template file to complete automated VCF fabric
deployment.

**6\.**(Optional.) Save the deployed configuration.

If the template file does not include IRF
configurations, the device will not save the configurations after executing all
configurations in the template file. To save the configurations, use the save command. For more information about this command, see configuration
file management commands in Fundamentals Command Reference.

## Enabling VCF fabric topology discovery

**1\.**Enter system view.

system-view

**2\.**Enable LLDP globally.

lldp global enable

By default, LLDP is disabled globally.

You must enable LLDP globally before you
enable VCF fabric topology discovery, because the device needs LLDP to collect
topology data of directly-connected devices.

**3\.**Enable VCF fabric topology discovery.

vcf-fabric topology enable

By default, VCF fabric topology discovery
is disabled.

## Configuring automated underlay network deployment

### Restrictions and guidelines

After automated underlay network deployment
starts, you cannot use the CLI commands to modify the configuration. A
violation might cause automated deployment failure. If you want to modify the
configuration, first cancel or pause automated deployment. Additionally, do not
start automated deployment after the modification. To cancel automated
deployment, use the undo vcf-fabric underlay autoconfigure command. To pause automated deployment, use the vcf-fabric underlay
pause command.

The underlay network can be an IPv4 or IPv6
network.

### Specify the template file for automated underlay network deployment

**1\.**Enter system view.

system-view

**2\.**Specify the template file for automated
underlay network deployment.

vcf-fabric underlay autoconfigure template

By default, no template file is specified
for automated underlay network deployment.

### Specifying the role of the device in the VCF fabric

#### About this task

Perform this task to change the role of the
device in the VCF fabric.

#### Restrictions and guidelines

If the device completes automated underlay
network deployment by automatically downloading and parsing a template file,
reboot the device after you change the device role. In this way, the device can
obtain the template file corresponding to the new role and complete the automated
underlay network deployment.

To use devices that have come online after
automated deployment to form an IRF fabric, make sure all member devices in the
IRF fabric have the same VCF fabric role.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Specify the role of the device in the VCF
fabric.

vcf-fabric role { access \| aggr \| leaf \| spine }

By default, the device is a leaf node.

**3\.**Return to system view.

quit

**4\.**Reboot the device.

reboot

For the new role to take effect, you must
reboot the device.

### Configuring the device as a master spine node

#### About this task

If multiple spine nodes exist on an IPv4
VCF fabric, you must configure a device as the master spine node to collect the
topology for the entire VCF fabric network.

#### Restrictions and guidelines

In an IPv6 VCF fabric, the controller
collects the topology automatically. You do not need to specify a master spine
node.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Configure the device as a master spine node.

vcf-fabric spine-role master

By default, the device is not a master
spine node.

### Setting the NETCONF username and password

#### About this task

During automated underlay network deployment,
a spine node establishes NETCONF sessions with other devices in the underlay
network to collect the global topology. If a user changes the username or
password on a device in the undelay network, the NETCONF session between the
device and the spine node is disconnected. Thun, the spine node fails to sense
the topology change. To resolve the issue, perform this task to change the
NETCONF username or password on the spine node.

#### Restrictions and guidelines

This feature is available on spine nodes.
Changing the NETCONF username or password will disconnect NETCONF sessions
between the spine node and leaf nodes. If multiple spine nodes exist in the VCF
fabric, changing the NETCONF username or password on the master spine node will
also disconnect NETCONF sessions between the master spine node and other spine
nodes.

Make sure all devices in the underlay
network use the same username and password for NETCONF session establishment.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Set the NETCONF username.

vcf-fabric underlay netconf-username username

By default, the device uses the NETCONF
username defined in the template file for automated VCF fabric deployment.

**3\.**Set the NETCONF password.

vcf-fabric underlay netconf-password { cipher \| simple } string

By default, the device uses the NETCONF password
defined in the template file for automated VCF fabric deployment.

### Pausing automated underlay network deployment

#### About this task

If you pause automated underlay network
deployment, the VCF fabric will save the current status of the device. It will
not respond to new LLDP events, set up the IRF fabric, aggregate links, or
discover uplink or downlink interfaces.

Perform this task if all devices in the VCF
fabric complete automated deployment and new devices are to be added to the VCF
fabric.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Pause automated underlay network deployment.

vcf-fabric underlay pause

By default, automated underlay network deployment
is not paused.

## Configuringautomated overlay network deployment

### Restrictions and guidelines for automated overlay network deployment

If the network type is VLAN or VXLAN with a
centralized IP gateway, perform this task on both the spine node and the leaf
nodes.

If the network type is VXLAN with
distributed IP gateways, perform this task on leaf nodes.

As a best practice, do not perform any of
the following tasks while the device is communicating with a RabbitMQ server:

·     Change the source IPv4 address for the device to
communicate with RabbitMQ servers.

·     Bring up or shut down a port connected to the
RabbitMQ server.

If you do so, it will take the CLI a long
time to respond to the l2agent enable, undo l2agent enable, l3agent enable, or undo l3agent
enable command.

Automated overlay network deployment is not
supported on aggregate or access nodes.

VCF does not support automating IPv6 overlay
networks.

### Automated overlay network deployment tasks at a glance

To configure automated overlay network
deployment, perform the following tasks:

**1\.**[Configuring parameters for the device to
communicate with RabbitMQ servers](#_Ref480219537)

**2\.**[Specifying the network type](#_Ref480219543)

**3\.**[Enabling L2 agent](#_Ref480219547)

On a VLAN network or a VXLAN network with
a centralized IP gateway, perform this task on both spine nodes and leaf nodes.

On a VXLAN network with distribute IP
gateways, perform this task only on leaf nodes.

**4\.**[Enabling L3 agent](#_Ref480219551)

On a VLAN network or a VXLAN network with
a centralized IP gateway, perform this task only on spine nodes.

On a VXLAN network with distribute IP
gateways, perform this task only on leaf nodes.

**5\.**[Configuring the border node](#_Ref480219555)

Perform this task only when the device is
the border node.

**6\.**(Optional.) [Enabling local proxy ARP](#_Ref480219559)

**7\.**(Optional.) [Configuring the MAC address of VSI
interfaces](#_Ref480219563)

### Prerequisites for automated overlay network deployment

Before you configure automated overlay
network deployment, you must complete the following tasks:

**1\.**Install OpenStack Neutron components and
plugins on the controller node in the VCF fabric.

**2\.**Install OpenStack Nova components, openvswitch,
and neutron-ovs-agent on compute nodes in the VCF fabric.

**3\.**Make sure LLDP and automated VCF fabric
topology discovery are enabled.

### Configuring parameters for the device to communicate with RabbitMQ servers

#### About this task

In the VCF fabric, the device communicates
with the Neutron server through RabbitMQ servers. You must specify the IP
address, login username, login password, and listening port for the device to
communicate with RabbitMQ servers.

#### Restrictions and guidelines

Make sure the RabbitMQ server settings on
the device are the same as those on the controller node. If the durable
attribute of RabbitMQ queues is set on the Neutron server, you must enable
creation of RabbitMQ durable queues on the device so that RabbitMQ queues can
be correctly created.

When you set the RabbitMQ server parameters
or remove the settings, make sure the routes between the device and the
RabbitMQ server is reachable. Otherwise, the CLI does not respond until the TCP
connection between the device and the RabbitMQ server is terminated.

Multiple virtual hosts might exist on the
RabbitMQ server. Each virtual host can independently provide RabbitMQ services
for the device. For the device to correctly communicate with the Neutron
server, specify the same virtual host on the device and the Neutron server.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enable Neutron and enter Neutron view.

neutron

By default, Neutron is disabled.

**3\.**Specify the IPv4 address, port number, and
MPLS L3VPN instance of a RabbitMQ server.

rabbit host ip ipv4-address \[ port port-number ] \[ vpn-instance vpn-instance-name ]

By default, no IPv4 address or MPLS L3VPN
instance of a RabbitMQ server is specified, and the port number of a RabbitMQ
server is 5672\.

**4\.**Specify the source IPv4 address for the
device to communicate with RabbitMQ servers.

rabbit source-ip ipv4-address \[ vpn-instance
vpn-instance-name ]

By default, no source IPv4 address is
specified for the device to communicate with RabbitMQ servers. The device
automatically selects a source IPv4 address through the routing protocol to communicate
with RabbitMQ servers.

**5\.**(Optional.) Enable creation of RabbitMQ
durable queues.

rabbit durable-queue enable

By default, RabbitMQ non-durable queues
are created.

**6\.**Configure the username for the device to
establish a connection with a RabbitMQ server.

rabbit user username

By default, the device uses username guest to
establish a connection with a RabbitMQ server.

**7\.**Configure the password for the device to
establish a connection with a RabbitMQ server.

rabbit password { cipher \| plain } string

By default, the device uses plaintext
password guest to establish a connection with a RabbitMQ server.

**8\.**Specify a virtual host to provide RabbitMQ
services.

rabbit virtual-host hostname

By default, the virtual host / provides
RabbitMQ services for the device.

**9\.**Specify the username and password for the
device to deploy configurations through RESTful.

restful user username password { cipher \| plain } password

By default, no username or password is
configured for the device to deploy configurations through RESTful.

### Specifying the network type

#### About this task

After you change the network type of the
VCF fabric where the device resides, Neutron deploys new configuration to all
devices according to the new network type.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter Neutron view.

neutron

**3\.**Specify the network type.

network-type {
centralized-vxlan \|
distributed-vxlan \| vlan }

By default, the network type is VLAN.

### Enabling L2 agent

#### About this task

Layer 2 agent (L2 agent) responds to
OpenStack events such as network creation, subnet creation, and port creation.
It deploys Layer 2 networking to provide Layer 2 connectivity within a virtual
network and Layer 2 isolation between different virtual networks

#### Restrictions and guidelines

On a VLAN network or a VXLAN network with a
centralized IP gateway, perform this task on both spine nodes and leaf nodes.

On a VXLAN network with distribute IP
gateways, perform this task only on leaf nodes.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter Neutron view.

neutron

**3\.**Enable the L2 agent.

l2agent enable

By default, the L2 agent is disabled.

### Enabling L3 agent

#### About this task

Layer 3 agent (L3 agent) responds to
OpenStack events such as virtual router creation, interface creation, and
gateway configuration. It deploys the IP gateways to provide Layer 3 forwarding
services for VMs.

#### Restrictions and guidelines

On a VLAN network or a VXLAN network with a
centralized IP gateway, perform this task only on spine nodes.

On a VXLAN network with distribute IP
gateways, perform this task only on leaf nodes.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter Neutron view.

neutron

**3\.**Enable the L3 agent.

L3agent enable

By default, the L3 agent is disabled.

### Configuring the border node

#### About this task

On a VXLAN network with a centralized IP
gateway or on a VLAN network, configure a spine node as the border node. On a
VXLAN network with distributed IP gateways, configure a leaf node as the
border.

You can use the following methods to
configure the IP address of the border gateway:

·     Manually specify the IP address of the border
gateway.

·     Enable the border node service on the border
gateway and create the external network and routers on the OpenStack Dashboard.
Then, VCF fabric automatically deploys the routing configuration to the device
to implement connectivity between tenant networks and the external network.

If the manually specified IP address is
different from the IP address assigned by VCF fabric, the IP address assigned
by VCF fabric takes effect.

The border node connects to the external
network through an interface which belongs to the global VPN instance. For the
traffic from the external network to reach a tenant network, the border node
needs to add the routes of the tenant VPN instance into the routing table of
the global VPN instance. You must configure export route targets of the tenant
VPN instance as import route targets of the global VPN instance. This setting
enables the global VPN instance to import routes of the tenant VPN instance.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter Neutron view.

neutron

**3\.**Enable the border node service.

border enable

By default, the device is not a border
node.

**4\.**(Optional.) Specify the IPv4 address of the
border gateway.

gateway ip ipv4-address

By default, the IPv4 address of the border
gateway is not specified.

**5\.**Configure export route targets for a tenant
VPN instance.

vpn-target target export-extcommunity

By default, no export route targets are
configured for a tenant VPN instance.

**6\.**(Optional.) Configure import route targets
for a tenant VPN instance.

vpn-target target import-extcommunity

By default, no import route targets are
configured for a tenant VPN instance.

### Enabling local proxy ARP

#### About this task

This feature enables the device to use the
MAC address of VSI interfaces to answer ARP requests for MAC addresses of VMs
on a different site from the requesting VMs.

#### Restrictions and guidelines

Perform this task only on leaf nodes on a
VXLAN network with distributed IP gateways.

This configuration takes effect on VSI
interfaces that are created after the proxy-arp enable command is executed. It does not take effect on existing VSI
interfaces.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter Neutron view.

neutron

**3\.**Enable local proxy ARP.

proxy-arp enable

By default, local proxy ARP is disabled.

### Configuring the MAC address of VSI interfaces

#### About this task

After you perform this task, VCF fabric
assigns the MAC address to all VSI interfaces newly created by automated
overlay network deployment on the device.

#### Restrictions and guidelines

Perform this task only on leaf nodes on a
VXLAN network with distributed IP gateways.

This configuration takes effect only on VSI
interfaces newly created after this command is executed.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter Neutron view.

neutron

**3\.**Configure the MAC address of VSI interfaces.

vsi-mac mac-address

By default, no MAC address is configured
for VSI interfaces.

## Display and maintenance commands for VCF fabric

Execute display
commands in any view.

 

| Task | Command |
| --- | --- |
| Display the role of the device in the VCF fabric. | display vcf-fabric role || Display VCF fabric topology information. | display vcf-fabric topology || Display information about automated underlay network deployment. | display vcf-fabric underlay autoconfigure || Display the supported version and the current version of the template file for automated VCF fabric provisioning. | display vcf-fabric underlay template-version |




 

 

