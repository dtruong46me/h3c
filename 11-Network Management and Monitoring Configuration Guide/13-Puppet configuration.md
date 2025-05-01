
# Configuring Puppet

## About Puppet

Puppet is an open-source configuration management tool. It provides
the Puppet language. You can use the Puppet language to create configuration manifests
and save them to a server. You can then use the server for centralized
configuration enforcement and management.

### Puppet network framework

Figure 1 Puppet network framework

![](https://resource.h3c.com/en/202407/12/20240712_11705758_x_Img_x_png_0_2216118_294551_0.png)

 

As shown in [Figure 1](#_Ref433124802),
Puppet operates in a client/server network framework. In the framework, the
Puppet master (server) stores configuration manifests for Puppet agents
(clients). The Puppet agents establish SSL connections to the Puppet master to obtain
their respective latest configurations.

#### Puppet master

The Puppet master runs the Puppet daemon
process to listen to requests from Puppet agents, authenticates Puppet agents,
and sends configurations to Puppet agents on demand. 

For information about installing and
configuring a Puppet master, access the official Puppet website at <https://puppetlabs.com>/.

#### Puppet agent

H3C devices support Puppet 3.7.3 agent. The
following is the communication process between a Puppet agent and the Puppet
master:

**1\.**The Puppet agent sends an authentication request
to the Puppet master.

**2\.**The Puppet agent checks with the Puppet
master for the authentication result periodically (every two minutes by default).
Once the Puppet agent passes the authentication, a connection is established to
the Puppet master.

**3\.**After the connection is established, the
Puppet agent sends a request to the Puppet master periodically (every 30
minutes by default) to obtain the latest configuration.

**4\.**After obtaining the latest configuration,
the Puppet agent compares the configuration with its running configuration. If
a difference exists, the Puppet agent overwrites its running configuration with
the newly obtained configuration.

**5\.**After overwriting the running configuration,
the Puppet agent sends a feedback to the Puppet master.

### Puppet resources

A Puppet resource is a unit of
configuration. Puppet uses manifests to store resources.

Puppet manages types of resources. Each resource
has a type, a title, and one or more attributes. Every attribute has a value.
The value specifies the state desired for the resource. You can specify the
state of a device by setting values for attributes regardless of how the device
enters the state. The following resource example shows how to configure a
device to create VLAN 2 and configure the description for VLAN 2\. 

netdev\_vlan{'vlan2':

 ensure \=\> undo\_shutdown,

 id \=\> 2,

 description \=\> 'sales-private',

 require \=\> Netdev\_device\['device'],

 }

The following are the resource type and
title:

·     netdev\_vlan—Type of the resource. The netdev\_vlan type
resources are used for VLAN configuration.

·     vlan2—Title of the resource. The title is the unique identifier of the resource.

The example contains the following
attributes:

·     ensure—Creates, modifies, or deletes a VLAN. To create a VLAN, set the
attribute value to undo\_shutdown. To delete a VLAN,
set the attribute value to shutdown.

·     id—Specifies a VLAN by its ID. In this example, VLAN 2 is specified.

·     description—Configures the description for the VLAN. In this example, the
description for VLAN 2 is sales-private.

·     require—Indicates that the resource depends on another resource (specified
by resource type and title). In this example, the resource depends on a netdev\_device type resource titled device.

For information about resource types
supported by Puppet, see "[Puppet resources](#_Ref433288608)."

## Restrictions and guidelines: Puppet configuration

The Puppet master cannot run a lower Puppet
version than Puppet agents.

## Prerequisites for Puppet

Before configuring Puppet on the device, complete
the following tasks on the device:

·     Enable NETCONF over SSH. The Puppet master sends
configuration information to Puppet agents through NETCONF over SSH connections.
For information about NETCONF over SSH, see "Configuring NETCONF."

·     Configure SSH login. Puppet agents communicate with
the Puppet master through SSH. For information about SSH login, see Fundamentals Configuration Guide.

·     For successful communication, verify that the
Puppet master and agents use the same system time. You can manually set the
same system time for the Puppet master and agents or configure them to use a time
synchronization protocol such as NTP. For more information about the time
synchronization protocols, see "Configuring NTP."

## Starting Puppet

### Configuring resources

**1\.**Install and configure the Puppet master.

**2\.**Create manifests for Puppet agents on the
Puppet master. 

For more information, see the Puppet master
installation and configuration guides.

### Configuring a Puppet agent

**1\.**Enter system view.

system-view

**2\.**Start Puppet.

third-part-process start name puppet arg agent
--certname\=certname --server\=server

By default, Puppet is shut down.

 

| Parameter | Description |
| --- | --- |
| --certname\=certname | Specifies the address of the Puppet agent. || --server\=server | Specifies the address of the Puppet master. |


 

After the Puppet process starts up, the
Puppet agent sends an authentication request to the Puppet master. For more
information about the third-part-process start command, see "Monitoring and maintaining processes".

### Signing a certificate for the Puppet agent

To sign a certificatefor the Puppet agent, execute the puppet cert
sign certname command on the Puppet master.

After the signing operation succeeds, the
Puppet agent establishes a connection to the Puppet master and requests configuration
information from the Puppet master.

## Shutting down Puppet on the device

#### Prerequisites

Execute the display process
all command to identify the ID of the Puppet
process. This command displays information about all processes on the device.
Check the following fields:

·     THIRD—This field displays Y for a third-party
process.

·     PID—Process ID.

·     COMMAND—This field displays puppet /opt/ruby/bin/pu
for the Puppet process.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Shut down Puppet.

third-part-process stop pid pid-list

For more information about the third-part-process
stop command, see "Monitoring and
maintaining processes".

## Puppet configuration examples

### Example: Configuring Puppet

#### Network configuration

As shown in [Figure 2](#_Ref433204157), the device is connected to thePuppet
master. Use Puppet to configure the device to perform the following operations:

·     Set the SSH login username and password to user and passwd, respectively.

·     Create VLAN 3\.

Figure 2 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705759_x_Img_x_png_1_2216118_294551_0.png)

 

#### Procedure

**1\.**Configure SSH login and enable NETCONF over
SSH on the device. (Details not shown.)

**2\.**On the Puppet master, create the modules/custom/manifests directory in the /etc/puppet/ directory for storing configuration
manifests.

$ mkdir -p
/etc/puppet/modules/custom/manifests

**3\.**Create configuration manifest init.pp in the /etc/puppet/modules/custom/manifests
directory as follows:

netdev\_device{'device':

 ensure \=\> undo\_shutdown,

 username \=\> 'user',

 password \=\> 'passwd',

 ipaddr \=\> '1.1.1.1',

 }

netdev\_vlan{'vlan3':

 ensure \=\> undo\_shutdown,

 id \=\> 3,

 require \=\> Netdev\_device\['device'],

 }

**4\.**Start Puppet on the device.

\<PuppetAgent\>
system-view

\[PuppetAgent]
third-part-process start name puppet arg agent --certname\=1.1.1.1 --server\=1.1.1.2

**5\.**Configure the Puppet master to authenticate
the request from the Puppet agent.

$ puppet cert sign 1.1.1.1

After passing the authentication, the
Puppet agent requests the latest configuration for it from the Puppet master.

