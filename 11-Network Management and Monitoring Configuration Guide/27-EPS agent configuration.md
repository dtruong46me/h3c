
# Configuring EPS agent

## About EPS and EPS agent

H3C iMC Endpoints Profiling System (EPS)
detects, identifies, and monitors network-wide endpoints, including cameras,
PCs, servers, switches, routers, firewalls, APs, printers, and ATMs. New
endpoints and abnormal endpoints can be timely discovered and identified.

As shown in [Figure 1](#_Ref519094215), an EPS
system contains the EPS server, scanners, and endpoints.

Figure 1 EPS network architecture

![](https://resource.h3c.com/en/202407/12/20240712_11705847_x_Img_x_png_0_2216132_294551_0.png)

 

#### EPS server

The EPS server is a server installed with
the iMC EPS component. It acts as the console to assign instructions to
scanners, receive scan results, compare scan results with baselines, and
support change auditing.

#### Scanner

A scanner is a device installed with iMC
EScan and enabled with EPS agent. The scanner scans and manages endpoints by
following the instruction of the EPS server and reports scan results to the EPS
server.

EPS agent is based on the Comware platform
and integrates the EPS scanner function. This chapter describes how to
configure EPS agent.

#### Endpoint

An endpoint refers to a device scanned by a
scanner. EPS supports user-defined endpoint types and the following
system-defined endpoint types: camera, PC, server, switch, router, firewall,
AP, printer, ATM, unknown, and other. 

## Restrictions: Hardware compatibility with EPS agent

The device does not support scanning
endpoints connected to the gateway. If the console assigns a gateway-based scan
task, no endpoints will be scanned.

## Configuring EPS agent

#### About this task

After you enable EPS agent for the scanner
on a slot, the scanner scans and manages connected, reachable endpoints in its
ARP table by following the instructions of the EPS server.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Configure EPS server parameters

eps server ip-address port port-number \[ key { cipher \| simple } key-value ] \[ log-level level-value ] \[ use-server-setting
]

By default, EPS server parameters are not
configured.

**3\.**Enable EPS agent.

eps agent enable \[ slot slot-number ]

By default, EPS agent is disabled.

## Display and maintenance commands forEPS agent

Execute display commands
in any view.

 

| Task | Command |
| --- | --- |
| Display scanned endpoint information. | display eps agent discovery \[ slot slot-number ] |

 

## EPS agent configuration examples

### Example: Configuring EPS agent

#### Network configuration

As shown in [Figure 2](#_Ref43287158),
configure the scanner to enable it to scan endpoints based on the instruction
of the EPS server. 

Figure 2 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705848_x_Img_x_png_1_2216132_294551_0.png)

‌

#### Procedure

\# Specify 20.1.1.2 and 4500 as the IP
address and port number of the EPS server, respectively.

\<Sysname\> system-view

\[Sysname] eps server 20.1.1.2 port 4500

\# Enable EPS agent on slot 1\.

\[Sysname] eps agent enable slot 1

#### Verifying the configuration

\# Display EPS scanner information.

\[Sysname] display eps agent

EPS agent information:

  Slot       Port number

  1          12000

 

EPS server information:

  IP address        : 20.1.1.2

  Port number       : 4500

  Cipher key        :
$c$3$rGn\+\+lJ/vHnkk1oQDAKX9NLIz\+4vXONZXCRTpQ\=\=

  Log level         : 5

  Use server setting: Yes

\# Verify that the scanned information is
displayed on slot 1 after the EPS server issues a scan instruction.

\[Sysname] display eps agent discovery
slot 1

IP-Address        MAC-Address       
OS-Type  OS-ver   Vendor    Product

100.100.100.100  cc:ef:48:9b:5f:c1
IOS       12.X     Cisco     C3560E

