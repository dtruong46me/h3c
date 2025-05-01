
# Configuring SQA

## About SQA

Service quality analysis (SQA) enables the
device to identify SIP- or H.323-based multimedia traffic and preferentially forward
this traffic to ensure multimedia service quality. Use this feature to decrease
multimedia stuttering and improve user experience.

## Restrictions and guidelines: SQA configuration

SQA does not support analysis of encrypted
SIP or H.323 protocol packets or fragments.

## SQA task at a glance

To configure SQA, perform the following
tasks:

**1\.**Configure SQA

Perform one of the following tasks
according to the type of the VoIP protocol used.

¡     [Configuring SIP-based SQA](#_Ref470366916)

¡     [Configuring H.323-based SQA](#_Ref68602041)

**2\.**(Optional.) [Re-marking a priority of VoIP protocol
packets](#_Ref68602395)

## Configuring SIP-based SQA

#### Restrictions and guidelines

Make sure SQA uses different port numbers
to listen for SIP and H.323 packets.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter SQA view.

sqa

**3\.**Enable SIP-based SQA.

sqa-sip enable

By default, SIP-based SQA is disabled.

**4\.**Specify the SIP listening port number.

sqa-sip port port-number

By default, the SIP listening port number
is 5060\.

Make sure the SIP listening port number
on the device is the same as that on the SIP server.

**5\.**(Optional.) Specify an IP address range for SIP-based
SQA.

sqa-sip filter address start-address
end-address

By default, no IP address range is
specified for SIP-based SQA. The device performs SQA on all SIP packets.

After this command is executed, the
device performs SQA only on SIP calls in the specified IP address range.

If you execute this command multiple
times, the most recent configuration takes effect.

## Configuring H.323-based SQA

#### Restrictions and guidelines

Make sure SQA uses different port numbers
to listen for SIP and H.323 packets.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter SQA view.

sqa

**3\.**Enable H.323-based SQA.

sqa-h323 enable

By default, H.323-based SQA is disabled.

**4\.**Specify the H.255 listening port number.

sqa-h225 port port-number

By default, the H.255 listening port
number is 1720\.

Make sure the H.255 listening port number
on the device is the same as that on the H.323 server.

**5\.**(Optional.) Specify an IP address range for H.323-based
SQA.

sqa-h323 filter address start-address
end-address

By default, no IP address range is
specified for H.323-based SQA. The device performs SQA on all H.323 packets.

After this command is executed, the
device performs SQA only on H.323 calls in the specified IP address range.

If you execute this command multiple
times, the most recent configuration takes effect.

## Re-marking a priority of VoIP protocol packets

#### About this task

To cooperate with QoS to schedule
application packets of different forwarding priorities, perform this task to re-mark
a priority of VoIP packets.

#### Restrictions and guidelines

You can use the remark { sip \| h323 } dot1p dot1p-value command or the remark dot1p command to
re-mark the 802.1p priority of VoIP packets. If you configure both of the
commands, the 802.1p priority set by using the remark { sip \| h323 } dot1p dot1p-value command takes effect.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter SQA view.

sqa

**3\.**Re-marks a priority of VoIP protocol
pakcets.

remark { sip \| h323 } { dot1p dot1p-value \| dscp { dscp-name \| dscp-value } }

By default, the device does not re-mark
the DSCP priority of VoIP protocol packets. Whether the device re-marks the
802.1p priority of VoIP protocol packets depends on the SQA configuration.

¡     If
SIP or H.323-based SQA is enabled, the device re-marks the 802.1p priority of
VoIP protocol packets to the default value (0).

¡     If
SIP or H.323-based SQA is disabled, the device does not re-mark the 802.1p
priority of VoIP protocol packets.

## Display and maintenance commands for SQA

Execute display
commands in any view .

 

| Task | Command |
| --- | --- |
| Display SIP or H.323 call information. | display sqa { h323 \| sip } call \[ \[ call-id call-id ] verbose ] || Display SIP or H.323 call statistics. | display sqa { h323 \| sip } call-statistics |


 

## SQA configuration examples

### Example: Configuring SIP-based SQA

#### Network configuration

As shown in [Figure 1](#_Ref17818206), the SIP
server installed with third-party VoIP server software acts as both the SIP
proxy server and SIP registrar to manage SIP UA registration and SIP calls.
Host A and Host B installed with third-party client software can place calls to
each other.

Configure SIP-based SQA on Device A, Device
B, and Device C to optimize multimedia traffic of SIP calls to provide high-quality
multimedia services.

Figure 1 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705853_x_Img_x_png_0_2216134_294551_0.png)

 

#### Prerequisites

Assign IP addresses to interfaces, and make
sure the devices can reach each other.

#### Procedure

**1\.**Configure the SIP server:

Install third-party VoIP server software,
register UAs, and specify the usernames (phone numbers) and passwords for the
clients. (Details not shown.)

**2\.**Configure PC A and PC B:

Install third-party VoIP client software,
specify the IP address of the SIP server, and configure the username (phone
number), password, and other parameters on each PC. (Details not shown.)

**3\.**Configure Device A:

\# Enable SIP-based
SQA.

\<DeviceA\> system-view

\[DeviceA] sqa

\[DeviceA-sqa] sqa-sip enable

\# Specify 5066 as
the SIP listening port number. (Make sure this SIP listening port number is the
same as that on the SIP server.)

\[DeviceA-sqa] sqa-sip port
5066

\# Specify an IP
address range for SIP-based SQA. (Make sure Host A's IP address is included in
the range.)

\[DeviceA-sqa] sqa-sip filter
address 6.159.107.200 6.159.107.210

\[DeviceA-sqa] quit

\[DeviceA] quit

**4\.**Configure Device B and Device C:

\# Configure Device B and Device C in the
same way as Device A is configured. (Details not shown.)

#### Verifying the configuration

\# Place a call from Host A to Host B.
(Details not shown.)

\# Display brief information about all SIP
calls on Device A.

\<DeviceA\> display sqa sip call

Caller                 Callee                 CallId

6.159.107.207:49172   
6.159.108.51:52410     3101326658

6.159.107.203:49172   
6.159.108.47:52410     4530332933

6.159.107.208:49172   
6.159.108.52:52410     4445702693

6.159.107.206:49172    6.159.108.50:52410    
8263542841

6.159.107.201:49172   
6.159.108.45:52410     4752123310

6.159.107.200:49172   
6.159.108.44:52410     99462146

The output shows that Device A has
performed SQA on the call that Host A placed to Host B.

\# Display detailed information about the
SIP call that Host A placed to Host B.

\<DeviceA\> display sqa sip call
call-id 99462146 verbose

Call ID: 99462146

  Caller information:

    IP: 6.159.107.200      Port:
49172    MAC: a036-9fd4-b5bd

    Tag: [\[email protected]](/cdn-cgi/l/email-protection)

  Callee information:

    IP: 6.159.108.44       Port: 52410 
  MAC: a036-9fd4-b5bc

    Tag: [\[email protected]](/cdn-cgi/l/email-protection)

  Type: Audio     VLAN: 1               StartTime:
2019-7-14 15:40:39

  MOS(F): 4       MOS(R): 4

  Forward flow (octets): 4793344       
Forward flow (packets): 4681

  Reverse flow (octets): 3810304       
Reverse flow (packets): 3721

The output shows that the MOS value for
both originating and returning traffic is high, which indicates the quality of
the audio service is high.

### Example: Configuring H.323-based SQA

#### Network configuration

As shown in [Figure 2](#_Ref43458513), the H.323
server installed with third-party VoIP server software acts as both the H.323 proxy
server and H.323 registrar to manage H.323 user registration and H.323 calls.
Host A and Host B installed with third-party client software can place calls to
each other.

Configure H.323-based SQA on Device A,
Device B, and Device C to optimize multimedia traffic of H.323 calls to provide
high-quality multimedia services.

Figure 2 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705854_x_Img_x_png_1_2216134_294551_0.png)

 

#### Prerequisites

Assign IP addresses to interfaces, and make
sure the devices can reach each other.

#### Procedure

**1\.**Configure the H.323 proxy
server:

Install third-party VoIP server software,
and specify the usernames (phone numbers) and passwords for the clients.
(Details not shown.)

**2\.**Configure PC A and PC B:

Install third-party VoIP client software,
specify the IP address of the H.323 server, and configure the username (phone
number), password, and other parameters on each PC. (Details not shown.)

**3\.**Configure Device A:

\# Enable H.323-based SQA.

\<DeviceA\> system-view

\[DeviceA] sqa

\[DeviceA-sqa] sqa-h323 enable

\# Specify 1720 as the H.323 listening
port number. (Make sure this H.323 listening port number is the same as that on
the H.323 server.)

\[DeviceA-sqa] sqa-h225 port 1720

\# Specify an IP address range for SQA.
(Make sure Host A's IP address is included in the range.)

\[DeviceA-sqa] sqa-h323 filter
address 6.159.107.200 6.159.107.210

\[DeviceA-sqa] quit

\[DeviceA] quit

**4\.**Configure Device B and Device C:

\# Configure Device B and Device C in the
same way as Device A is configured. (Details not shown.)

#### Verifying the configuration

\# Place a call from Host A to Host B.
(Details not shown.)

\# Display brief information about all H.323
calls on Device A.

\<DeviceA\> display sqa h323 call

Caller                Callee                CallId

6.159.107.207:49172  
6.159.108.51:52410    42910c03-e31c-1910-9a63-000c29209aa9

6.159.107.203:49172   6.159.108.47:52410   
3e33ecbd-6f1d-1910-8b52-6805ca5d1208

6.159.107.208:49172  
6.159.108.52:52410    01a788fe-e21c-1910-8ee7-000c29209aa9

6.159.107.206:49172  
6.159.108.50:52410    4e9516ff-e21c-1910-8f98-000c29209aa9

6.159.107.200:49172   6.159.108.44:52410    703b6fff-e21c-1910-9f51-000c29209aa9

The output shows that Device A has
performed SQA on the call that Host A placed to Host B.

\# Display detailed information about the H.323
call that Host A placed to Host B.

\<DeviceA\> display sqa h323 call
call-id 703b6fff-e21c-1910-9f51-000c29209aa9 verbose

Call ID: 703b6fff-e21c-1910-9f51-000c29209aa9

  Caller information:

    IP: 6.159.107.200      Port: 49172 
  MAC: a036-9fd4-b5bd

  Callee information:

    IP: 6.159.108.44       Port: 52410 
  MAC: a036-9fd4-b5bc

  GUID: 703b6fff-e21c-1910-9f61-000c29209aa9

  Protocol: TCP(6)

  Session ID: 2

  Type: Audio          Vlan: 1       
  StartTime: 2019-7-14 15:40:39

  MOS(F): 4       MOS(R): 4

  Forward flow (octets): 4793344       
Forward flow (packets): 4681

  Reverse flow (octets): 3810304       
Reverse flow (packets): 3721

The output shows that the MOS value for
both originating and returning traffic is high, which indicates the quality of
the audio service is high.

 

