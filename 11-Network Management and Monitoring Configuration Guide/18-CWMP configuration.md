
# Configuring CWMP

## About CWMP

CPE WAN Management Protocol (CWMP), also
called "TR-069," is a DSL Forum technical specification for remote
management of network devices.

The protocol was initially designed to
provide remote autoconfiguration through a server for large numbers of
dispersed end-user devices in a network. CWMP can be used on different types of
networks, including Ethernet.

### CWMP network framework

[Figure 1](#_Ref371696623) shows a basic CWMP network framework.

Figure 1 CWMP network framework

![](https://resource.h3c.com/en/202407/12/20240712_11705786_x_Img_x_png_0_2216123_294551_0.png)

‌

A basic CWMP network includes the following
network elements:

·     ACS—Autoconfiguration server, the management device in the network.

·     CPE—Customer premises equipment, the managed device in the network.

·     DNS server—Domain name system server. CWMP defines that the ACS and the CPE
use URLs to identify and access each other. DNS is used to resolve the URLs.

·     DHCP server—Assigns ACS attributes along with IP addresses to CPEs when the
CPEs are powered on. DHCP server is optional in CWMP. With a DHCP server, you
do not need to configure ACS attributes manually on each CPE. The CPEs can contact
the ACS automatically when they are powered on for the first time.

The device is operating as a CPE in the
CWMP framework.

### Basic CWMP functions

You can autoconfigure and upgrade CPEs in
bulk from the ACS.

#### Autoconfiguration

You can create configuration files for
different categories of CPEs on the ACS. Based on the device models and serial
numbers of the CPEs, the ACS verifies the categories of the CPEs and issues the
associated configuration to them.

The following are methods available for the
ACS to issue configuration to the CPE:

·     Transfers the configuration file to the CPE, and
specifies the file as the next-startup configuration file. At a reboot, the CPE
starts up with the ACS-specified configuration file.

·     Runs the configuration in the CPE's RAM. The
configuration takes effect immediately on the CPE. For the running
configuration to survive a reboot, you must save the configuration on the CPE.

#### CPE software management

The ACS can manage CPE software upgrade.

When the ACS finds a software version
update, the ACS notifies the CPE to download the software image file from a
specific location. The location can be the URL of the ACS or an independent
file server.

If the CPE successfully downloads the
software image file and the file is validated, the CPE notifies the ACS of a
successful download. If the CPE fails to download the software image file or
the file is invalidated, the CPE notifies the ACS of an unsuccessful download.

#### Data backup

The ACS can require the CPE to upload a
configuration file or log file to a specific location. The destination location
can be the ACS or a file server.

#### CPE status and performance monitoring

The ACS can monitor the status and
performance of CPEs. [Table 1](#_Ref376274859) shows
the available CPE status and performance objects for the ACS to monitor.

Table 1 CPE status and performance objects
available for the ACS to monitor

| Category | Objects | Remarks |
| --- | --- | --- |
| Device information | Manufacturer  ManufacturerOUI  SerialNumber  HardwareVersion  SoftwareVersion | N/A || Operating status and information | DeviceStatus  UpTime | N/A || Configuration file | ConfigFile | Local configuration file stored on CPE for upgrade. The ACS can issue configuration to the CPE by transferring a configuration file to the CPE or running the configuration in CPE's RAM. || CWMP settings | ACS URL | URL address of the ACS to which the CPE initiates a CWMP connection. This object is also used for main/backup ACS switchover. || ACS username  ACS password | When the username and password of the ACS are changed, the ACS changes the ACS username and password on the CPE to the new username and password.  When a main/backup ACS switchover occurs, the main ACS also changes the ACS username and password to the backup ACS username and password. || PeriodicInformEnable | Whether to enable or disable the periodic Inform feature. || PeriodicInformInterval | Interval for periodic connection from the CPE to the ACS for configuration and software update. || PeriodicInformTime | Scheduled time for connection from the CPE to the ACS for configuration and software update. || ConnectionRequestURL (CPE URL) | N/A || ConnectionRequestUsername (CPE username)  ConnectionRequestPassword (CPE password) | CPE username and password for authentication from the ACS to the CPE. |










‌

### How CWMP works

#### RPC methods

CWMP uses remote procedure call (RPC)
methods for bidirectional communication between CPE and ACS. The RPC methods
are encapsulated in HTTP or HTTPS.

[Table 2](#_Ref376275394) shows the primary RPC methods used in CWMP.

Table 2 RPC methods

| RPC method | Description |
| --- | --- |
| Get | The ACS obtains the values of parameters on the CPE. || Set | The ACS modifies the values of parameters on the CPE. || Inform | The CPE sends an Inform message to the ACS for the following purposes:  ·     Initiates a connection to the ACS.  ·     Reports configuration changes to the ACS.  ·     Periodically updates CPE settings to the ACS. || Download | The ACS requires the CPE to download a configuration or software image file from a specific URL for software or configuration update. || Upload | The ACS requires the CPE to upload a file to a specific URL. || Reboot | The ACS reboots the CPE remotely for the CPE to complete an upgrade or recover from an error condition. |






‌

#### Autoconnect between ACS and CPE

The CPE automatically initiates a
connection to the ACS when one of the following events occurs:

·     ACS URL change. The CPE initiates a connection request
to the new ACS URL.

·     CPE startup. The CPE initiates a connection to
the ACS after the startup.

·     Timeout of the periodic Inform interval. The CPE
re-initiates a connection to the ACS at the Inform interval.

·     Expiration of the scheduled connection
initiation time. The CPE initiates a connection to the ACS at the scheduled
time.

#### CWMP connection establishment

Step 1 through step 5 in [Figure 2](#_Ref463699356) show
the procedure of establishing a connection between the CPE and the ACS.

**1\.**After obtaining the basic ACS parameters,
the CPE initiates a TCP connection to the ACS.

**2\.**If HTTPS is used, the CPE and the ACS initialize
SSL for a secure HTTP connection.

**3\.**The CPE sends an Inform message in HTTPS to
initiate a CWMP session.

**4\.**After the CPE passes authentication, the ACS
returns an Inform response to establish the session.

**5\.**After sending all requests, the CPE sends an
empty HTTP post message.

Figure 2 CWMP connection establishment

![](https://resource.h3c.com/en/202407/12/20240712_11705787_x_Img_x_png_1_2216123_294551_0.png)

‌

#### Main/backup ACS switchover

Typically, two ACSs are used in a CWMP
network for consecutive monitoring on CPEs. When the main ACS needs to reboot,
it points the CPE to the backup ACS. Step 6 through step 11 in [Figure 3](#_Ref376425761) show
the procedure of a main/backup ACS switchover.

**1\.**Before the main ACS reboots, it queries the
ACS URL set on the CPE.

**2\.**The CPE replies with its ACS URL setting.

**3\.**The main ACS sends a Set request to change the
ACS URL on the CPE to the backup ACS URL.

**4\.**After the ACS URL is modified, the CPE sends
a response.

**5\.**The main ACS sends an empty HTTP message to
notify the CPE that it has no other requests.

**6\.**The CPE closes the connection, and then initiates
a new connection to the backup ACS URL.

Figure 3 Main and backup ACS switchover

![](https://resource.h3c.com/en/202407/12/20240712_11705795_x_Img_x_png_2_2216123_294551_0.png)

‌

## Restrictions and guidelines: CWMP configuration

CWMP is available only in IPv4 networks. If
you configure CWMP in an IPv6 network, the feature does not take effect.

You can configure ACS and CPE attributes
from the CPE's CLI, the DHCP server, or the ACS. For an attribute, the CLI- and
ACS-assigned values have higher priority than the DHCP-assigned value. The CLI-
and ACS-assigned values overwrite each other, whichever is assigned later.

This document only describes configuring
ACS and CPE attributes from the CLI and DHCP server. For more information about
configuring and using the ACS, see ACS documentation.

## CWMP tasksat a glance

To configure CWMP, perform the following
tasks:

**1\.**[Enabling CWMP from the CLI](#_Ref376439037)

You can also enable CWMP from a DHCP
server.

**2\.**[Configuring ACS attributes](#_Ref371424724)

**a.**[Configuring the preferred ACS attributes](#_Ref376699884)

**b.**(Optional.) [Configuring the default ACS attributes
from the CLI](#_Ref376700076)

**3\.**[Configuring CPE attributes](#_Ref371424746)

**a.**[Specifying an SSL client policy for
HTTPS connection to ACS](#_Ref462403076)

This task is required when the ACS uses
HTTPS for secure access.

**b.**(Optional.) [Configuring ACS authentication
parameters](#_Ref371424754)

**c.**(Optional.) [Configuring the provision code](#_Ref376536627)

**d.**(Optional.) [Configuring the CWMP connection
interface](#_Ref371424761)

**e.**(Optional.) [Configuring autoconnect parameters](#_Ref371424765)

**f.**(Optional.) [Setting the close-wait timer](#_Ref376536657)

## Enabling CWMP from the CLI

**1\.**Enter system view.

system-view

**2\.**Enter CWMP view.

cwmp

**3\.**Enable CWMP.

cwmp enable

By default, CWMP is disabled.

## Configuring ACS attributes

### About ACS attributes

You can configure two sets of ACS
attributes for the CPE: preferred and default.

·     The preferred ACS attributes are configurable
from the CPE's CLI, the DHCP server, and ACS.

·     The default ACS attributes are configurable only
from the CLI.

If the preferred ACS attributes are not
configured, the CPE uses the default ACS attributes for connection establishment.

### Configuring the preferred ACS attributes

#### Assigning ACS attributes from the DHCP server

The DHCP server in a CWMP network assigns the
following information to CPEs:

·     IP addresses for the CPEs.

·     DNS server address.

·     ACS URL and ACS login authentication
information.

This section introduces how to use DHCP
option 43 to assign the ACS URL and ACS login authentication username and
password. For more information about DHCP and DNS, see Layer
3—IP Services Configuration Guide.

If the DHCP server is an H3C device, you
can configure DHCP option 43 by using the option 43 hex 01length URL
username password command.

·     length—A hexadecimal number that indicates the total length of the length, URL, username, and password
arguments, including the spaces between these arguments. No space is allowed
between the 01 keyword and the length value.

·     URL—ACS URL.

·     username—Username for the CPE to authenticate to the ACS.

·     password—Password for the CPE to authenticate to the ACS.

 

|  | NOTE:  The ACS URL, username and password must use the hexadecimal format and be space separated. |
| --- | --- |

‌

The following example configures the ACS
address as http://169.254.76.31:7547/acs, username
as 1234, and password as 5678:

\<Sysname\> system-view

\[Sysname] dhcp server ip-pool 0

\[Sysname-dhcp-pool-0] option 43 hex 0127687474703A2F2F3136392E3235342E37362E33313A373534372F61637320313233342035363738

Table 3 Hexadecimal forms of the ACS attributes

| Attribute | Attribute value | Hexadecimal form |
| --- | --- | --- |
| Length | 39 characters | 27 || ACS URL | http://169.254.76.31:7547/acs | 687474703A2F2F3136392E3235342E37362E33313A373534372F61637320  NOTE:  The two ending digits (20) represent the space. || ACS connect username | 1234 | 3132333420  NOTE:  The two ending digits (20) represent the space. || ACS connect password | 5678 | 35363738 |




‌

#### Configuring the preferred ACSattributes from the CLI

**1\.**Enter system view.

system-view

**2\.**Enter CWMP view.

cwmp

**3\.**Configure the preferred ACS URL.

cwmp acs url url

By default, no preferred ACS URL has been
configured.

**4\.**Configure the username for authentication to
the preferred ACS URL.

cwmp acs username username

By default, no username has been
configured for authentication to the preferred ACS URL.

**5\.**(Optional.) Configure the password for
authentication to the preferred ACS URL.

cwmp acs password { cipher \| simple } string

By default, no password has been
configured for authentication to the preferred ACS URL.

### Configuring the default ACS attributes from the CLI

**1\.**Enter system view.

system-view

**2\.**Enter CWMP view.

cwmp

**3\.**Configure the default ACS URL.

cwmp acs default url url

By default, no default ACS URL has been
configured.

**4\.**Configure the username for authentication to
the default ACS URL.

cwmp acs default username username

By default, no username has been
configured for authentication to the default ACS URL.

**5\.**(Optional.) Configure the password for
authentication to the default ACS URL.

cwmp acs default password { cipher \| simple } string

By default, no password has been configured
for authentication to the default ACS URL.

## Configuring CPE attributes

### About CPE attributes

You can configure
the following CPE attributes only from the CPE's CLI.

·     CWMP connection interface.

·     Maximum number of connection retries.

·     SSL client policy for HTTPS connection to ACS.

For other CPE attribute values, you can
assign them to the CPE from the CPE's CLI or the ACS. The CLI- and ACS-assigned
values overwrite each other, whichever is assigned later.

### Specifying an SSL client policy for HTTPS connection to ACS

#### About this task

This task is required when the ACS uses
HTTPS for secure access. CWMP uses HTTP or HTTPS for data transmission. When
HTTPS is used, the ACS URL begins with https://.
You must specify an SSL client policy for the CPE to authenticate the ACS for
HTTPS connection establishment.

#### Prerequisites

Before you perform this task, first create an
SSL client policy. For more information about configuring SSL client policies,
see Security Configuration Guide.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter CWMP view.

cwmp

**3\.**Specify an SSL client policy.

ssl client-policy policy-name

By default, no SSL client policy is
specified.

### Configuring ACS authentication parameters

#### About this task

To protect the CPE against unauthorized access,
configure a CPE username and password for ACS authentication. When an ACS
initiates a connection to the CPE, the ACS must provide the correct username
and password.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter CWMP view.

cwmp

**3\.**Configure the username for authentication to
the CPE.

cwmp cpe username username

By default, no username has been configured
for authentication to the CPE.

**4\.**(Optional.) Configure the password for
authentication to the CPE.

cwmp cpe password { cipher \| simple } string

By default, no password has been
configured for authentication to the CPE.

The password setting is optional. You can
specify only a username for authentication.

### Configuring the provision code

#### About this task

The ACS can use the provision code to
identify services assigned to each CPE. For correct configuration deployment,
make sure the same provision code is configured on the CPE and the ACS. For
information about the support of your ACS for provision codes, see the ACS
documentation.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter CWMP view.

cwmp

**3\.**Configure the provision code.

cwmp cpe provision-code provision-code

The default provision code is PROVISIONINGCODE.

### Configuring the CWMP connection interface

#### About this task

The CWMP connection interface is the
interface that the CPE uses to communicate with the ACS. To establish a CWMP
connection, the CPE sends the IP address of this interface in the Inform
messages, and the ACS replies to this IP address.

Typically, the CPE selects the CWMP
connection interface automatically. If the CWMP connection interface is not the
interface that connects the CPE to the ACS, the CPE fails to establish a CWMP
connection with the ACS. In this case, you need to manually set the CWMP
connection interface.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter CWMP view.

cwmp

**3\.**Specify the interface that connects to the
ACS as the CWMP connection interface.

cwmp cpe connect interface interface-type
interface-number

By default, no CWMP connection interface
is specified.

### Configuring autoconnect parameters

#### About this task

You can configure the CPE to connect to the
ACS periodically, or at a scheduled time for configuration or software update.

The CPE retries a connection automatically
when one of the following events occurs:

·     The CPE fails to connect to the ACS. The CPE
considers a connection attempt as having failed when the close-wait timer
expires. This timer starts when the CPE sends an Inform request. If the CPE
fails to receive a response before the timer expires, the CPE resends the
Inform request.

·     The connection is disconnected before the
session on the connection is completed.

To protect system resources, limit the
number of retries that the CPE can make to connect to the ACS.

#### Configuring the periodicInform feature

**1\.**Enter system view.

system-view

**2\.**Enter CWMP view.

cwmp

**3\.**Enable the periodic Inform feature.

cwmp cpe inform interval enable

By default, this function is disabled.

**4\.**Set the Inform interval.

cwmp cpe inform interval interval

By default, the CPE sends an Inform
message to start a session every 600 seconds.

#### Scheduling aconnection initiation

**1\.**Enter system view.

system-view

**2\.**Enter CWMP view.

cwmp

**3\.**Schedule a connection initiation.

cwmp cpe inform time time

By default, no connection initiation has
been scheduled.

#### Setting the maximum number of connection retries

**1\.**Enter system view.

system-view

**2\.**Enter CWMP view.

cwmp

**3\.**Set the maximum number of connection
retries.

cwmp cpe connect retry retries

By default, the CPE retries a failed
connection until the connection is established.

### Setting the close-wait timer

#### About this task

The close-wait timer specifies the
following:

·     The maximum amount of time the CPE waits for the
response to a session request. The CPE determines that its session attempt has
failed when the timer expires.

·     The amount of time the connection to the ACS can
be idle before it is terminated. The CPE terminates the connection to the ACS
if no traffic is sent or received before the timer expires.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter CWMP view.

cwmp

**3\.**Set the close-wait timer.

cwmp cpe wait timeout seconds

By default, the close-wait timer is 30
seconds.

## Display and maintenance commands for CWMP

Execute display commands
in any view.

 

| Task | Command |
| --- | --- |
| Display CWMP configuration. | display cwmp configuration || Display the current status of CWMP. | display cwmp status |


‌

## CWMP configuration examples

### Example: Configuring CWMP

#### Network configuration

As shown in [Figure 4](#_Ref479683998), use H3C
IMC BIMS as the ACS to bulk-configure the devices (CPEs), and assign ACS
attributes to the CPEs from the DHCP server. 

The configuration files for the CPEs in
equipment rooms A and B are configure1.cfg and configure2.cfg, respectively.

Figure 4 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705796_x_Img_x_png_3_2216123_294551_0.png)

‌

[Table 4](#_Ref479684006) shows
the ACS attributes for the CPEs to connect to the ACS.

Table 4 ACS attributes

| Item | Setting |
| --- | --- |
| Preferred ACS URL | http://10.185.10.41:9090 || ACS username | admin || ACS password | 12345 |



‌

[Table 5](#_Ref479684030) lists
serial numbers of the CPEs.

Table 5 CPE list

| Room | Device | Serial number |
| --- | --- | --- |
| A | CPE 1 | 210231A95YH10C000045 || CPE 2 | 210235AOLNH12000010 || CPE 3 | 210235AOLNH12000015 || B | CPE 4 | 210235AOLNH12000017 || CPE 5 | 210235AOLNH12000020 || CPE 6 | 210235AOLNH12000022 |






‌

#### Configuring the ACS

Figures in this section are for
illustration only.

To configure the ACS:

**1\.**Log in to the ACS:

**a.**Launch a Web browser on the ACS
configuration terminal.

**b.**In the address bar of the Web browser, enter
the ACS URL and port number. This example uses http://10.185.10.41:8080/imc.

**c.**On the login page, enter the ACS login username
and password, and then click Login.

**2\.**Create a CPE group for each equipment room:

**a.**Select Service
\> BIMS \> CPE Group
from the top navigation bar.

The CPE Group page appears.

Figure 5 CPE
Group page

![](https://resource.h3c.com/en/202407/12/20240712_11705797_x_Img_x_png_4_2216123_294551_0.png)

**a.**Click Add.

**b.**Enter a username, and then click OK.

Figure 6 Adding a
CPE group

![](https://resource.h3c.com/en/202407/12/20240712_11705798_x_Img_x_png_5_2216123_294551_0.png)

**a.**Repeat the previous two steps to create a
CPE group for CPEs in Room B.

**3\.**Add CPEs to the CPE group for each equipment
room:

**a.**Select Service
\> BIMS \> Resource
Management \> Add CPE from the top navigation
bar.

**b.**On the Add CPE
page, configure the following parameters:

\-     Authentication Type—Select ACS UserName.

\-     CPE Name—Enter a
CPE name.

\-     ACS Username—Enter
admin.

\-     ACS Password Generated—Select Manual Input.

\-     ACS Password—Enter
a password for ACS authentication.

\-     ACS Confirm Password—Re-enter the password.

\-     CPE Model—Select
the CPE model.

\-     CPE Group—Select
the CPE group.

Figure 7 Adding a
CPE

![](https://resource.h3c.com/en/202407/12/20240712_11705799_x_Img_x_png_6_2216123_294551_0.png)

 

**a.**Click OK.

**b.**Verify that the CPE has been added
successfully from the All CPEs page.

Figure
8 Viewing CPEs

![](https://resource.h3c.com/en/202407/12/20240712_11705800_x_Img_x_png_7_2216123_294551_0.png)

‌

**a.**Repeat the previous steps to add CPE 2 and
CPE 3 to the CPE group for Room A, and add CPEs in Room B to the CPE group for
Room B.

**4\.**Configure a configuration template for each
equipment room:

**a.**Select Service
\> BIMS \> Configuration
Management \> Configuration Templates from
the top navigation bar.

Figure
9 Configuration Templates page

![](https://resource.h3c.com/en/202407/12/20240712_11705801_x_Img_x_png_8_2216123_294551_0.png)

‌

**a.**Click Import.

**b.**Select a source configuration file, select Configuration Segment as the template type, and then
click OK.

The created configuration template will
be displayed in the Configuration
Template list after a successful file import.

 

| IMPORTANT | IMPORTANT:  If the first command in the configuration template file is system-view, make sure no characters exist in front of the command. |
| --- | --- |

‌

Figure
10 Importing a configuration template

![](https://resource.h3c.com/en/202407/12/20240712_11705788_x_Img_x_png_10_2216123_294551_0.png)

‌

Figure
11 Configuration Template list

![](https://resource.h3c.com/en/202407/12/20240712_11705789_x_Img_x_png_11_2216123_294551_0.png)

‌

**a.**Repeat the previous steps to configure a
configuration template for Room B.

**5\.**Add software library entries:

**a.**Select Service
\> BIMS \> Configuration Management
\> Software Library from the top navigation bar.

Figure 12 Software
Library page

![](https://resource.h3c.com/en/202407/12/20240712_11705790_x_Img_x_png_12_2216123_294551_0.png)

‌

**a.**Click Import.

**b.**Select a source file, and then click OK.

Figure
13 Importing CPE software

![](https://resource.h3c.com/en/202407/12/20240712_11705791_x_Img_x_png_13_2216123_294551_0.png)

‌

**a.**Repeat the previous steps to add software
library entries for CPEs of different models.

**6\.**Create an auto-deployment task for each
equipment room:

**a.**Select Service \> BIMS
\> Configuration Management \> Deployment Guide from the
top navigation bar.

Figure
14 Deployment Guide

![](https://resource.h3c.com/en/202407/12/20240712_11705792_x_Img_x_png_14_2216123_294551_0.png)

‌

**a.**Click By CPE Model from
the Auto Deployment Configuration field.

**b.**Select a configuration template, select Startup Configuration from the File
Type to be Deployed list, and click Select Model
to select CPEs in Room A. Then, click OK.

You can search for CPEs by CPE group.

Figure
15 Auto deployment configuration

![](https://resource.h3c.com/en/202407/12/20240712_11705793_x_Img_x_png_15_2216123_294551_0.png)

‌

**a.**Click OK on the Auto Deploy Configuration page.

Figure
16 Operation result

![](https://resource.h3c.com/en/202407/12/20240712_11705794_x_Img_x_png_16_2216123_294551_0.png)

‌

**a.**Repeat the previous steps to add a
deployment task for CPEs in Room B. 

#### Configuring the DHCP server

In this example, an H3C device is operating
as the DHCP server.

**1\.**Configure an IP address pool to assign IP
addresses and DNS server address to the CPEs. This example uses subnet 10.185.10.0/24
for IP address assignment.

\# Enable DHCP.

\<DHCP\_server\>
system-view

\[DHCP\_server] dhcp enable

\# Enable DHCP server on VLAN-interface 1\.

\[DHCP\_server] interface vlan-interface
1

\[DHCP\_server-Vlan-interface1]
dhcp select server

\[DHCP\_server-Vlan-interface1]
quit

\# Exclude the DNS server address
10.185.10.60 and the ACS IP address 10.185.10.41 from dynamic allocation.

\[DHCP\_server] dhcp server
forbidden-ip 10.185.10.41

\[DHCP\_server] dhcp server
forbidden-ip 10.185.10.60

\# Create DHCP address pool 0\.

\[DHCP\_server] dhcp server
ip-pool 0

\# Assign subnet 10.185.10.0/24 to the
address pool, and specify the DNS server address 10.185.10.60 in the address
pool.

\[DHCP\_server-dhcp-pool-0]
network 10.185.10.0 mask 255.255.255.0

\[DHCP\_server-dhcp-pool-0]
dns-list 10.185.10.60

**2\.**Configure DHCP Option 43 to contain the ACS
URL, username, and password in hexadecimal format.

\[DHCP\_server-dhcp-pool-0]
option 43 hex 013B687474703A2F2F6163732E64617461626173653A393039302F616373207669636B79203132333435

#### Configuring the DNS server

Map http://acs.database:9090 to http://10.185.1.41:9090
on the DNS server. For more information about DNS configuration, see DNS server
documentation.

#### Connecting the CPEs to the network

\# Connect CPE 1 to the network, and then
power on the CPE. (Details not shown.)

\# Log in to CPE 1 and configure its VLAN-interface
2 to use DHCP for IP address acquisition and assign interface Ten-GigabitEthernet 1/0/1 to VLAN 2\. 

\<CPE1\> system-view

\[CPE1] vlan 2

\[CPE1-vlan2] quit

\[CPE1] interface ten-gigabitethernet 1/0/1

\[CPE1-Ten-GigabitEthernet1/0/1] port access vlan 2

\[CPE1-Ten-GigabitEthernet1/0/1] quit

\[CPE1] interface vlan-interface 2

\[CPE1-Vlan-interface2] ip address
dhcp-alloc

\# Repeat the previous steps to configure the
other CPEs.

#### Verifying the configuration

\# Execute the display
current-configuration command to verify that the
running configurations on CPEs are the same as the configurations issued by the
ACS.

 

