
# Using Ansible for automated configuration management

## About Ansible

Ansible is a configuration tool programmed
in Python. It uses SSH to connect to devices.

### Ansible network architecture

As shown in[Figure 1](#_Ref531787089), an
Ansible system consists of the following elements:

·     Manager—A host installed with the Ansible environment. For more information
about the Ansible environment, see Ansible documentation.

·     Managed devices—Devices to be managed. These devices do not need to install any
agent software. They only need to be able to act as an SSH server. The manager
communicates with managed devices through SSH to deploy configuration files.

H3C devices can act as managed devices.

Figure 1 Ansible network architecture

![](https://resource.h3c.com/en/202407/12/20240712_11705755_x_Img_x_png_0_2216117_294551_0.png)

 

### How Ansible works

The following the steps describe how
Ansible works:

**1\.**On the manager, create a configuration file
and specify the destination device.

**2\.**The manager (SSH client) initiates an SSH
connection to the device (SSH server).

**3\.**The manager deploys the configuration file
to the device.

**4\.**After receiving a configuration file from
the manager, the device loads the configuration file.

## Restrictions and guidelines

Not all services modules are configurable
through Ansible. To identify the service modules that you can configure by
using Ansible, access the Comware 7 Python library.

## Configuring the device for management with Ansible

Before you use Ansible to configure the device,
complete the following tasks:

·     Configure a time protocol (NTP or PTP) or
manually configure the system time on the Ansible server and the device to
synchronize their system time. For more information about NTP and PTP configuration,
see Network Management and Monitoring Configuration
Guide.

·     Configure the device as an SSH server. For more
information about SSH configuration, see Security
Configuration Guide.

## Device setup examples for management with Ansible

### Example: Setting up the device for management with Ansible

#### Network configuration

As shown in [Figure 2](#_Ref531794442), enable
SSH server on the device and use the Ansible manager to manage the device over
SSH.

Figure 2 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705756_x_Img_x_png_1_2216117_294551_0.png)

 

#### Prerequisites

Assign IP addresses to the device and
manager so you can access the device from the manager. (Details not shown.)

#### Procedure

**1\.**Configure a time protocol (NTP or PTP) or
manually configure the system time on both the device and manager so they use
the same system time. (Details not shown.)

**2\.**Configure the device as an SSH server:

\# Create local key pairs. (Details not
shown.)

\# Create a local user named abc and set the
password for the user.

\<Device\> system-view

\[Device]local-user abc

\[Device-luser-manage-abc]
password simple 123456TESTplat\&!

\# Assign the network-admin user role to
the user and authorize the user to use SSH, HTTP, and HTTPS services.

\[Device-luser-manage-abc]
authorization-attribute user-role network-admin 

\[Device-luser-manage-abc]
service-type ssh http https

\[Device-luser-manage-abc] quit

\# Enable NETCONF over SSH.

\[Device] netconf ssh server
enable

\# Enable scheme authentication for SSH
login and assign the network-admin user role to the login users.

\[Device] line vty 0 63

\[Device-line-vty0-63]
authentication-mode scheme

\[Device-line-vty0-63]
user-role network-admin

\[Device-line-vty0-63] quit

\# Enable the SSH server.

\[Device] ssh server enable

\# Authorize SSH user abc to use all
service types, including SCP, SFTP, Stelnet, and NETCONF. Set the
authentication method to password.

\[Device] ssh user abc
service-type all authentication-type password

\# Enable the SFTP server or SCP server.

¡     If
the device supports SFTP, enable the SFTP server.

\[Device] sftp server enable

¡     If
the device does not support SFTP, enable the SCP server.

\[Device] scp server enable

#### Procedure

Install Ansible on the manager. Create a
configuration script and deploy the script. For more information, see the
relevant documents.

 

 

