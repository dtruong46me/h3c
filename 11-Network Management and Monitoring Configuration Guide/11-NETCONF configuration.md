
# Configuring NETCONF

## About NETCONF

Network Configuration Protocol (NETCONF) is
an XML-based network management protocol. It provides programmable mechanisms
to manage and configure network devices. Through NETCONF, you can configure
device parameters, retrieve parameter values, and collect statistics. For a network
that has devices from vendors, you can develop a NETCONF-based NMS system to
configure and manage devices in a simple and effective way.

### NETCONF structure

NETCONF has the following layers: content
layer, operations layer, RPC layer, and transport protocol layer.

Table 1 NETCONF layers and XML layers

| NETCONF layer | XML layer | Description |
| --- | --- | --- |
| Content | Configuration data, status data, and statistics | Contains a set of managed objects, which can be configuration data, status data, and statistics. For information about the operable data, see the NETCONF XML API reference for the device. || Operations | \<get\>, \<get-config\>, \<edit-config\>… | Defines a set of base operations invoked as RPC methods with XML-encoded parameters. NETCONF base operations include data retrieval operations, configuration operations, lock operations, and session operations. For information about operations supported on the device, see "[Supported NETCONF operations](#_Ref127437998)." || RPC | \<rpc\> and \<rpc-reply\> | Provides a simple, transport-independent framing mechanism for encoding RPCs. The \<rpc\> and \<rpc-reply\> elements are used to enclose NETCONF requests and responses (data at the operations layer and the content layer). || Transport protocol | In non-FIPS mode:  Console, Telnet, SSH, HTTP, HTTPS, and TLS  In FIPS mode:  Console, SSH, HTTPS, and TLS | Provides reliable, connection-oriented, serial data links.  The following transport layer sessions are available in non-FIPS mode:  ·     CLI sessions, including NETCONF over Telnet sessions, NETCONF over SSH sessions, and NETCONF over console sessions.  ·     NETCONF over SOAP sessions, including NETCONF over SOAP over HTTP sessions and NETCONF over SOAP over HTTPS sessions.  The following transport layer sessions are available in FIPS mode:  ·     CLI sessions, including NETCONF over SSH sessions and NETCONF over console sessions.  ·     NETCONF over SOAP over HTTPS sessions. |




 

### NETCONF message format

#### NETCONF

All NETCONF messages are XML-based and
comply with RFC 4741\. An incoming NETCONF message must pass XML schema check
before it can be processed. If a NETCONF message fails XML schema check, the
device sends an error message to the client.  

For information about the NETCONF
operations supported by the device and the operable data, see the NETCONF XML
API reference for the device.

The following example shows a NETCONF
message for getting all parameters of all interfaces on the device:

\<?xml version\="1.0"
encoding\="utf-8"?\>

\<rpc message-id\="100"
xmlns\="urn:ietf:params:xml:ns:netconf:base:1.0"\>

  \<get-bulk\>

    \<filter
type\="subtree"\>

      \<top
xmlns\="http://www.h3c.com/netconf/data:1.0"\>

        \<Ifmgr\>

          \<Interfaces\>

                 \<Interface/\>

          \</Interfaces\>

        \</Ifmgr\>

      \</top\>

    \</filter\>

  \</get-bulk\>

\</rpc\>

#### NETCONF over SOAP

All NETCONF over SOAP messages are
XML-based and comply with RFC 4741\. NETCONF messages are contained in the
\<Body\> element of SOAP messages. NETCONF over SOAP messages also comply
with the following rules:

·     SOAP messages must use the SOAP Envelope
namespaces.

·     SOAP messages must use the SOAP Encoding
namespaces.

·     SOAP messages cannot contain the following information:

¡     DTD
reference.

¡     XML
processing instructions.

The following example shows a NETCONF over
SOAP message for getting all parameters of all interfaces on the device:

\<env:Envelope
xmlns:env\="http://www.w3.org/2003/05/soap-envelope"\>

  \<env:Header\>

    \<auth:Authentication
env:mustUnderstand\="1"
xmlns:auth\="http://www.h3c.com/netconf/base:1.0"\>

     
\<auth:AuthInfo\>800207F0120020C\</auth:AuthInfo\>

    \</auth:Authentication\>

  \</env:Header\>

  \<env:Body\>

    \<rpc
message-id\="100"
xmlns\="urn:ietf:params:xml:ns:netconf:base:1.0"\>

      \<get-bulk\>

        \<filter
type\="subtree"\>

          \<top
xmlns\="http://www.h3c.com/netconf/data:1.0"\>

            \<Ifmgr\>

              \<Interfaces\>

                \<Interface/\>

              \</Interfaces\>

            \</Ifmgr\>

          \</top\>

        \</filter\>

      \</get-bulk\>

    \</rpc\>

  \</env:Body\>

\</env:Envelope\>

### How to use NETCONF

You can use NETCONF to manage and configure
the device by using the methods in [Table 2](#_Ref360010220).

Table 2 NETCONF methods for configuring the
device

| Configuration tool | Login method | Remarks |
| --- | --- | --- |
| CLI | ·     Console port  ·     SSH  ·     Telnet | To perform NETCONF operations, copy valid NETCONF messages to the CLI in XML view. || Custom user interface | N/A | This method requires a NETCONF over SOAP session or NETCONF over SSH session with the device. To use a NETCONF over SOAP session, you must enable NETCONF over SOAP. Then, NETCONF messages will be encapsulated in SOAP messages for transmission. |


 

### Protocols and standards

·     RFC 3339, Date and
Time on the Internet: Timestamps

·     RFC 4741, NETCONF
Configuration Protocol

·     RFC 4742, Using
the NETCONF Configuration Protocol over Secure SHell (SSH)

·     RFC 4743, Using
NETCONF over the Simple Object Access Protocol (SOAP)

·     RFC 5277, NETCONF
Event Notifications

·     RFC 5381,
Experience of Implementing NETCONF over SOAP

·     RFC 5539, NETCONF
over Transport Layer Security (TLS)

·     RFC 6241, Network
Configuration Protocol

## FIPS compliance

The device supports the FIPS mode that
complies with NIST FIPS 140-2 requirements. Support for features, commands, and
parameters might differ in FIPS mode (see Security
Configuration Guide) and non-FIPS mode.

## NETCONF tasks at a glance

To configure NETCONF, perform the following
tasks:

**1\.**[Establishing a NETCONF session](#_Ref478560314)

**a.**(Optional.) [Setting NETCONF session attributes](#_Ref478560320)

**b.**[Establishing NETCONF over SOAP sessions](#_Ref478560383)

**c.**[Establishing NETCONF over SSH sessions](#_Ref478560335)

**d.**[Establishing NETCONF over Telnet or
NETCONF over console sessions](#_Ref478560394)

**e.**[Exchanging capabilities](#_Ref478560400)

**2\.**(Optional.) [Retrieving device configuration
information](#_Ref465071579)

¡     [Retrieving device configuration and
state information](#_Ref464728277)

¡     [Retrieving non-default settings](#_Ref478560441)

¡     [Retrieving NETCONF information](#_Ref464725547)

¡     [Retrieving YANG file content](#_Ref478560452)

¡     [Retrieving NETCONF session information](#_Ref478560454)

**3\.**(Optional.) [Filtering data](#_Ref464728293)

·     [Table-based filtering](#_Ref478560491)

·     [Column-based filtering](#_Ref478560495)

**4\.**(Optional.) [Locking or unlocking the running
configuration](#_Ref478486643)

**a.**[Locking the running configuration](#_Ref478560512)

**b.**[Unlocking the running configuration](#_Ref478560520)

**5\.**(Optional.) [Modifying the configuration](#_Ref478560530)

**6\.**(Optional.) Managing configuration files

¡     [Saving the running configuration](#_Ref464728321)

¡     [Loading the configuration](#_Ref464728329)

¡     [Rolling back the configuration](#_Ref464728335)

**7\.**(Optional.) [Performing CLI operations through
NETCONF](#_Ref464728353)

**8\.**(Optional.) [Subscribing to events](#_Ref464728359)

¡     [Subscribing to syslog events](#_Ref480465483)

¡     [Subscribing to events monitored by NETCONF](#_Ref480465494)

¡     [Subscribing to events reported by modules](#_Ref480465497)

¡     [Canceling an event subscription](#_Ref17991681)

**9\.**(Optional.) [Terminating NETCONF sessions](#_Ref478560634)

**10\.**(Optional.) [Returning to the CLI](#_Ref478560639)

## Establishing a NETCONF session

### Restrictions and guidelines for NETCONF session establishment

After a NETCONF session is established, the
device automatically sends its capabilities to the client. You must send the
capabilities of the client to the device before you can perform any other
NETCONF operations.

Before performing a NETCONF operation, make
sure no other users are configuring or managing the device. If multiple users
simultaneously configure or manage the device, the result might be different
from what you expect.

You can use the aaa
session-limit command to set the maximum number
of NETCONF sessions that the device can support. If the upper limit is reached,
new NETCONF users cannot access the device. For information about this command,
see AAA in Security Configuration Guide.

### Setting NETCONF session attributes

#### About this task

NETCONF supports the following types of
namespaces:

·     Common namespace—The common namespace is shared by all modules. In a packet that
uses the common namespace, the namespace is indicated in the \<top\>
element, and the modules are listed under the \<top\> element. 

Example:

\<rpc
message-id\="100"
xmlns\="urn:ietf:params:xml:ns:netconf:base:1.0"\>

   \<get-bulk\>

      \<filter
type\="subtree"\>

         \<top
xmlns\="http://www.h3c.com/netconf/data:1.0"\>

           
\<Ifmgr\>

              
\<Interfaces\>

               \</Interfaces\>

            \</Ifmgr\>

         \</top\>

      \</filter\>

   \</get-bulk\>

\</rpc\>

·     Module-specific namespace—Each module has its own namespace. A packet that uses a
module-specific namespace does not have the \<top\> element. The namespace
follows the module name.

Example:

\<rpc
message-id\="100"
xmlns\="urn:ietf:params:xml:ns:netconf:base:1.0"\>

   \<get-bulk\>

      \<filter
type\="subtree"\>

            \<Ifmgr
xmlns\="http://www.h3c.com/netconf/data:1.0-Ifmgr"\>

              
\<Interfaces\>

              
\</Interfaces\>

            \</Ifmgr\>

      \</filter\>

   \</get-bulk\>

\</rpc\>

The common namespace is incompatible with
module-specific namespaces. To set up a NETCONF session, the device and the
client must use the same type of namespaces. By default, the common namespace is
used. If the client does not support the common namespace, use this feature to
configure the device to use module-specific namespaces.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Set the NETCONF session idle timeout time.

netconf { agent \| soap } idle-timeout
minute

 

| Keyword | Description |
| --- | --- |
| agent | Specifies the following sessions:  ·     NETCONF over SSH sessions.  ·     NETCONF over Telnet sessions.  ·     NETCONF over console sessions.  By default, the idle timeout time is 0, and the sessions never time out. || soap | Specifies the following sessions:  ·     NETCONF over SOAP over HTTP sessions.  ·     NETCONF over SOAP over HTTPS sessions.  The default setting is 10 minutes. |


 

**3\.**Enable NETCONF logging.

netconf log source { all \| { agent \| soap } \* } { protocol-operation
{ all \| { action \| config \| get \| set \| session \| syntax \| others } \* } \| row-operation \| verbose }

By default, the device logs only row
operations for \<action\> and \<edit-config\> operations. NETCONF
logging is disabled for all the other types of operations.

**4\.**Configure NETCONF to use module-specific
namespaces.

netconf capability specific-namespace

By default, the common namespace is used.

For the setting to take effect, you must
reestablish the NETCONF session.

### Establishing NETCONF over SOAP sessions

#### About this task

You can use a custom user interface to
establish a NETCONF over SOAP session to the device and perform NETCONF
operations. NETCONF over SOAP encapsulates NETCONF messages into SOAP messages
and transmits the SOAP messages over HTTP or HTTPS.

#### Restrictions and guidelines

You can add an authentication domain to the
\<UserName\> parameter of a SOAP request. The authentication domain takes
effect only on the current request.

The mandatory authentication domain
configured by using the netconf soap domain
command takes precedence over the authentication domain specified in the
\<UserName\> parameter of a SOAP request.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enable NETCONF over SOAP.

In non-FIPS mode:

netconf soap { http \| https } enable

In FIPS mode:

netconf soap https enable

By default, the NETCONF over SOAP feature
is disabled.

**3\.**Set the DSCP value for NETCONF over SOAP
packets.

In non-FIPS mode:

netconf soap { http \| https } dscp dscp-value

In FIPS mode:

netconf soap https dscp dscp-value

By default, the DSCP value is 0 for
NETCONF over SOAP packets.

**4\.**Use an IPv4 ACL to control NETCONF over SOAP
access.

In non-FIPS mode:

netconf soap { http \| https } acl { ipv4-acl-number
\| name ipv4-acl-name }

In FIPS mode:

netconf soap https acl { ipv4-acl-number
\| name ipv4-acl-name }

By default, no IPv4 ACL is applied to
control NETCONF over SOAP access.

Only clients permitted by the IPv4 ACL
can establish NETCONF over SOAP sessions.

**5\.**Specify a mandatory authentication domain for NETCONF users.

netconf soap domain domain-name

By default, no mandatory authentication
domain is specified for NETCONF users. For information about authentication
domains, see Security Configuration Guide.

**6\.**Use the custom user interface to establish a
NETCONF over SOAP session with the device.

For information about the custom user
interface, see the user guide for the interface.

### Establishing NETCONF over SSH sessions

#### Prerequisites

Before establishing a NETCONF over SSH
session, make sure the custom user interface can access the device through SSH.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enable NETCONF over SSH.

netconf ssh server enable

By default, NETCONF over SSH is disabled.

**3\.**Specify the listening port for NETCONF over
SSH packets.

netconf ssh server port
port-number

By default, the listening port number is
830\.

**4\.**Use the custom user interface to establish a
NETCONF over SSH session with the device. For information about the custom user
interface, see the user guide for the interface.

### Establishing NETCONF over Telnet or NETCONF over console sessions

#### Restrictions and guidelines

To ensure the format correctness of a
NETCONF message, do not enter the message manually. Copy and paste the message.

While the device is performing a NETCONF
operation, do not perform any other operations, such as pasting a NETCONF
message or pressing Enter.

For the device to identify NETCONF
messages, you must add end mark ]]\>]]\> at the
end of each NETCONF message. Examples in this document do not necessarily have
this end mark. Do add the end mark in actual operations.

#### Prerequisites

To establish a NETCONF over Telnet session
or a NETCONF over console session, first log in to the device through Telnet or
the console port.

#### Procedure

To enter XML view, execute the following
command in user view:

xml

If the XML view prompt appears, the NETCONF
over Telnet session or NETCONF over console session is established
successfully.

### Exchanging capabilities

#### About this task

After a NETCONF session is established, the
device sends its capabilities to the client. You must use a hello message to
send the capabilities of the client to the device before you can perform any
other NETCONF operations.

#### Hello message from the device to the client

\<?xml version\="1.0"
encoding\="UTF-8"?\>\<hello
xmlns\="urn:ietf:params:xml:ns:netconf:base:1.0"\>\<capabilities\>...\</capabilities\>\<session-id\>1\</session-id\>\</hello\>]]\>]]\>

The \<capabilities\> element carries
the capabilities supported by the device. The supported capabilities vary by
device model.

The \<session-id\> element carries the
unique ID assigned to the NETCONF session.

#### Hello message from the client to the device

After receiving the hello message from the
device, copy the following hello message to notify the device of the
capabilities supported by the client:

\<hello
xmlns\="urn:ietf:params:xml:ns:netconf:base:1.0"\>

  \<capabilities\>

    \<capability\>

     *capability-set*

   \</capability\>

 \</capabilities\>

\</hello\>

 

| Item | Description |
| --- | --- |
| capability-set | Specifies a set of capabilities supported by the client.  Use the \<capability\> and \</capability\> tags to enclose each user-defined capability set. |

 

## Retrieving device configuration information

### Restrictions and guidelines for device configuration retrieval

As a best practice to ensure retrieval
performance, limit the number of data entries for each \<get-bulk\> or
\<get\> operation to 1000\. If the number of data entries to retrieve is
higher than 1000, use the \<get-bulk\> operation as a best practice.

During a \<get\>, \<get-bulk\>,
\<get-config\>, or \<get-bulk-config\> operation, NETCONF replaces
unidentifiable characters in the retrieved data with question marks (?) before
sending the data to the client. If the process for a relevant module is not
started yet, the operation returns the following message:

\<?xml version\="1.0"?\>

\<rpc-reply
message-id\="100"
xmlns\="urn:ietf:params:xml:ns:netconf:base:1.0"\>

  \<data/\>

\</rpc-reply\>

The
\<get\>\<netconf-state/\>\</get\> operation does not support data
filtering.

For more information about the NETCONF
operations, see the NETCONF XML API references for the device.

### Retrieving device configuration and state information

You can use the following NETCONF
operations to retrieve device configuration and state information:

·     \<get\> operation—Retrieves all device configuration and state information that
matches the specified conditions.

·     \<get-bulk\> operation—Retrieves data entries starting from the data entry next to the one
with the specified index. One data entry contains a device configuration entry
and a state information entry. The returned output does not include the index
information.

The \<get\> message and
\<get-bulk\> message share the following format:

\<?xml version\="1.0"
encoding\="UTF-8"?\>

\<rpc message-id\="100"
xmlns\="urn:ietf:params:xml:ns:netconf:base:1.0"\>

  \<getoperation\>

    \<filter\>

      \<top
xmlns\="http://www.h3c.com/netconf/data:1.0"\>

          Specify the module,
submodule, table name, and column name

      \</top\>

    \</filter\>

  \</getoperation\>

\</rpc\>

 

| Item | Description |
| --- | --- |
| getoperation | Operation name, get or get-bulk. || filter | Specifies the filtering conditions, such as the module name, submodule name, table name, and column name.  ·     If you specify a module name, the operation retrieves the data for the specified module. If you do not specify a module name, the operation retrieves the data for all modules.  ·     If you specify a submodule name, the operation retrieves the data for the specified submodule. If you do not specify a submodule name, the operation retrieves the data for all submodules.  ·     If you specify a table name, the operation retrieves the data for the specified table. If you do not specify a table name, the operation retrieves the data for all tables.  ·     If you specify only the index column, the operation retrieves the data for all columns. If you specify the index column and any other columns, the operation retrieves the data for the index column and the specified columns. |


 

A \<get-bulk\> message can carry the count and index attributes. 

 

| Item | Description |
| --- | --- |
| index | Specifies the index.  If you do not specify this item, the index value starts with 1 by default. || count | Specifies the data entry quantity.  The count attribute complies with the following rules:  ·     The count attribute can be placed in the module node and table node. In other nodes, it cannot be resolved.  ·     When the count attribute is placed in the module node, a descendant node inherits this count attribute if the descendant node does not contain the count attribute.  ·     The \<get-bulk\> operation retrieves all the rest data entries starting from the data entry next to the one with the specified index if either of the following conditions occurs:  ¡     You do not specify the count attribute.  ¡     The number of matching data entries is less than the value of the count attribute. |


 

The following \<get-bulk\> message
example specifies the count and index attributes:

\<?xml version\="1.0"
encoding\="UTF-8"?\>

\<rpc message-id\="100"
xmlns\="urn:ietf:params:xml:ns:netconf:base:1.0"
xmlns:xc\="http://www.h3c.com/netconf/base:1.0"\>

  \<get-bulk\>

    \<filter
type\="subtree"\>

      \<top
xmlns\="http://www.h3c.com/netconf/data:1.0"
xmlns:base\="http://www.h3c.com/netconf/base:1.0"\>

        \<Syslog\>

          \<Logs xc:count\="5"\>

            \<Log\>

              \<Index\>10\</Index\>

           \</Log\>

             \</Logs\>

        \</Syslog\>

      \</top\>

    \</filter\>

  \</get-bulk\>

\</rpc\>

When retrieving interface information, the
device cannot identify whether an integer value for the \<IfIndex\> element
represents an interface name or index. When retrieving VPN instance
information, the device cannot identify whether an integer value for the
\<vrfindex\> element represents a VPN name or index. To resolve the issue,
you can use the valuetype attribute to specify the
value type.

The valuetype
attribute has the following values:

 

| Value | Description |
| --- | --- |
| name | The element carries a name. || index | The element carried an index. || auto | Default value. The device uses the value of the element as an index for information matching. If no match is found, the device uses the value as a name for information matching. |



 

The following example specifies an
index-type value for the \<IfIndex\> element:

\<rpc
xmlns\="urn:ietf:params:xml:ns:netconf:base:1.0"\>

  \<getoperation\>

    \<filter\>

      \<top
xmlns\="http://www.h3c.com/netconf/config:1.0"
xmlns:base\="http://www.h3c.com/netconf/base:1.0"\>

       \<VLAN\>

        \<TrunkInterfaces\>

          \<Interface\>

            \<IfIndex base:valuetype\="index"\>1\</IfIndex\>

          \</Interface\>

        \</TrunkInterfaces\>

       \</VLAN\>

      \</top\>

    \</filter \>

  \</getoperation\>

\</rpc\>

If the \<get\> or \< get-bulk\> operation succeeds, the
device returns the retrieved data in the following format:

\<?xml version\="1.0"?\>

\<rpc-reply
message-id\="100"
xmlns\="urn:ietf:params:xml:ns:netconf:base:1.0"\>

  \<data\>

     Device state and configuration data

  \</data\>

\</rpc-reply\>

### Retrieving non-default settings

The \<get-config\> and
\<get-bulk-config\> operations are used to retrieve all non-default
settings. The \<get-config\> and \<get-bulk-config\> messages can
contain the \<filter\> element for filtering data. 

The \<get-config\> and
\<get-bulk-config\> messages are similar. The following is a
\<get-config\> message example:

\<?xml version\="1.0"?\>

\<rpc message-id\="100"
xmlns\="urn:ietf:params:xml:ns:netconf:base:1.0"\>

  \<get-config\>

    \<source\>

      \<running/\>

    \</source\>

    \<filter\>

      \<top
xmlns\="http://www.h3c.com/netconf/config:1.0"\>

          Specify the module
name, submodule name, table name, and column name

      \</top\>

    \</filter\>

  \</get-config\>

\</rpc\>

If the \<get-config\> or
\<get-bulk-config\> operation succeeds, the device returns the retrieved
data in the following format:

\<?xml version\="1.0"?\>

\<rpc-reply
message-id\="100" xmlns\="urn:ietf:params:xml:ns:netconf:base:1.0"\>

  \<data\>

    Data matching the specified
filter

  \</data\>

\</rpc-reply\>

### Retrieving NETCONF information

Use the
\<get\>\<netconf-state/\>\</get\> message to retrieve NETCONF
information.

\# Copy the following text to the client to
retrieve NETCONF information:

\<?xml version\="1.0"
encoding\="UTF-8"?\>

\<rpc message-id\="m-641"
xmlns\="urn:ietf:params:xml:ns:netconf:base:1.0"\>

  \<get\>

    \<filter type\='subtree'\>

      \<netconf-state
xmlns\='urn:ietf:params:xml:ns:yang:ietf-netconf-monitoring'\>

         \<getType/\>

      \</netconf-state\>

    \</filter\>

  \</get\>

\</rpc\>

If you do not specify a value for getType, the retrieval operation retrieves all NETCONF
information.

The value for getType
can be one of the following operations:

 

| Operation | Description |
| --- | --- |
| capabilities | Retrieves device capabilities. || datastores | Retrieves databases from the device. || schemas | Retrieves the list of the YANG file names from the device. || sessions | Retrieves session information from the device. || statistics | Retrieves NETCONF statistics. |





 

If the
\<get\>\<netconf-state/\>\</get\> operation succeeds, the device
returns a response in the following format:

\<?xml version\="1.0"?\>

\<rpc-reply
message-id\="100"
xmlns\="urn:ietf:params:xml:ns:netconf:base:1.0"\>

  \<data\>

     Retrieved NETCONF information

  \</data\>

\</rpc-reply\>

### Retrieving YANG file content

YANG files save the NETCONF operations
supported by the device. You can identify the supported operations by
retrieving and analyzing the content of YANG files.

YANG files are integrated in the device
software and are named in the format of yang\_identifier@yang\_version.yang.
You cannot view the YANG file names by executing the dir command. For information about retrieving the YANG file names, see
"[Retrieving NETCONF information](#_Ref464725547)."

\# Copy the following text to the client to
retrieve the YANG file named [\[email protected]](/cdn-cgi/l/email-protection):

\<rpc message-id\="100"
xmlns\="urn:ietf:params:xml:ns:netconf:base:1.0"\>

  \<get-schema xmlns\='urn:ietf:params:xml:ns:yang:ietf-netconf-monitoring'\>

    \<identifier\>syslog-data\</identifier\>

    \<version\>2019-01-01\</version\>

    \<format\>yang\</format\>

  \</get-schema\>

\</rpc\>

If the \<get-schema\> operation
succeeds, the device returns a response in the following format:

\<?xml version\="1.0"?\>

\<rpc-reply
message-id\="100"
xmlns\="urn:ietf:params:xml:ns:netconf:base:1.0"\>

  \<data\>

     Content of the specified YANG file

  \</data\>

\</rpc-reply\>

### Retrieving NETCONF session information

Use the \<get-sessions\> operation to
retrieve NETCONF session information of the device.

\# Copy the following message to the client
to retrieve NETCONF session information from the device:

\<?xml version\="1.0"
encoding\="UTF-8"?\>

\<rpc message-id\="100"
xmlns\="urn:ietf:params:xml:ns:netconf:base:1.0"\>

  \<get-sessions/\>

\</rpc\>

If the \<get-sessions\> operation
succeeds, the device returns a response in the following format:

\<?xml version\="1.0"
encoding\="UTF-8"?\>

\<rpc-reply
message-id\="100"
xmlns\="urn:ietf:params:xml:ns:netconf:base:1.0"\>

  \<get-sessions\>

    \<Session\>

      \<SessionID\>Configuration session ID\</SessionID\>

      \<Line\>Line information\</Line\>

      \<UserName\>Name of the user creating the session\</UserName\>

      \<Since\>Time when the session was created\</Since\>

      \<LockHeld\>Whether the session holds a lock\</LockHeld\>

    \</Session\>

  \</get-sessions\>

\</rpc-reply\>

### Example: Retrieving a data entry for the interface table

#### Network configuration

Retrieve a data entry for the interface
table.

#### Procedure

\# Enter XML view.

\<Sysname\> xml

\# Notify the device of the NETCONF
capabilities supported on the client.

\<hello
xmlns\="urn:ietf:params:xml:ns:netconf:base:1.0"\>

  \<capabilities\>

   
\<capability\>urn:ietf:params:netconf:base:1.0\</capability\>

  \</capabilities\>

\</hello\>

\# Retrieve a data entry for the interface
table.

\<rpc message-id\="100"
xmlns\="urn:ietf:params:xml:ns:netconf:base:1.0"
xmlns:web\="urn:ietf:params:xml:ns:netconf:base:1.0"\>

  \<get-bulk\>

    \<filter
type\="subtree"\>

      \<top
xmlns\="http://www.h3c.com/netconf/data:1.0"
xmlns:web\="http://www.h3c.com/netconf/base:1.0"\>

        \<Ifmgr\>

          \<Interfaces web:count\="1"\>

          \</Interfaces\>

        \</Ifmgr\>

      \</top\>

    \</filter\>

  \</get-bulk\>

\</rpc\>

#### Verifying the configuration

If the client receives the following text,
the \<get-bulk\> operation is successful:

\<rpc-reply
xmlns\="urn:ietf:params:xml:ns:netconf:base:1.0"
xmlns:web\="urn:ietf:params:xml:ns:netconf:base:1.0"
message-id\="100"\>

  \<data\>

    \<top
xmlns\="http://www.h3c.com/netconf/data:1.0"\>

      \<Ifmgr\>

        \<Interfaces\>

          \<Interface\>

           
\<IfIndex\>3\</IfIndex\>

            \<Name\>Ten-GigabitEthernet1/0/2\</Name\>

           
\<AbbreviatedName\>XGE1/0/2\</AbbreviatedName\>

           
\<PortIndex\>3\</PortIndex\>

           
\<ifTypeExt\>22\</ifTypeExt\>

           
\<ifType\>6\</ifType\>

            \<Description\>Ten-GigabitEthernet1/0/2 Interface\</Description\>

           
\<AdminStatus\>2\</AdminStatus\>

           
\<OperStatus\>2\</OperStatus\>

           
\<ConfigSpeed\>0\</ConfigSpeed\>

           
\<ActualSpeed\>100000\</ActualSpeed\>

           
\<ConfigDuplex\>3\</ConfigDuplex\>

           
\<ActualDuplex\>1\</ActualDuplex\>

          \</Interface\>

        \</Interfaces\>

      \</Ifmgr\>

    \</top\>

  \</data\>

\</rpc-reply\>

### Example: Retrieving non-default configuration data

#### Network configuration

Retrieve all non-default configuration
data.

#### Procedure

\# Enter XML view.

\<Sysname\> xml

\# Notify the device of the NETCONF
capabilities supported on the client.

\<hello
xmlns\="urn:ietf:params:xml:ns:netconf:base:1.0"\>

  \<capabilities\>

    \<capability\>

           
urn:ietf:params:netconf:base:1.0

    \</capability\>

  \</capabilities\>

\</hello\>

\# Retrieve all non-default configuration
data.

\<rpc message-id\="100"
xmlns\="urn:ietf:params:xml:ns:netconf:base:1.0"\>

  \<get-config\>

    \<source\>

      \<running/\>

    \</source\>

  \</get-config\>

\</rpc\>

#### Verifying the configuration

If the client receives the following text,
the \<get-config\> operation is successful:

\<rpc-reply xmlns\="urn:ietf:params:xml:ns:netconf:base:1.0"
xmlns:web\="urn:ietf:params:xml:ns:netconf:base:1.0"
message-id\="100"\>

    \<data\>

        \<top
xmlns\="http://www.h3c.com/netconf/config:1.0"\>

            \<Ifmgr\>

               
\<Interfaces\>

                    \<Interface\>

                       
\<IfIndex\>1307\</IfIndex\>

                       
\<Shutdown\>1\</Shutdown\>

                   
\</Interface\>

                   
\<Interface\>

                       
\<IfIndex\>1308\</IfIndex\>

                       
\<Shutdown\>1\</Shutdown\>

                   
\</Interface\>

                   
\<Interface\>

                       
\<IfIndex\>1309\</IfIndex\>

                       
\<Shutdown\>1\</Shutdown\>

                   
\</Interface\>

                   
\<Interface\>

                        \<IfIndex\>1311\</IfIndex\>

                           
\<VlanType\>2\</VlanType\>

                   
\</Interface\>

                   
\<Interface\>

                       
\<IfIndex\>1313\</IfIndex\>

                           
\<VlanType\>2\</VlanType\>

                   
\</Interface\>

               
\</Interfaces\>

            \</Ifmgr\>

            \<Syslog\>

                \<LogBuffer\>

                   
\<BufferSize\>120\</BufferSize\>

               
\</LogBuffer\>

            \</Syslog\>

            \<System\>

                \<Device\>

                   
\<SysName\>Sysname\</SysName\>

                   
\<TimeZone\>

                       
\<Zone\>\+11:44\</Zone\>

                       
\<ZoneName\>beijing\</ZoneName\>

                   
\</TimeZone\>

                \</Device\>

            \</System\>

        \</top\>

    \</data\>

\</rpc-reply\>

### Example: Retrieving syslog configuration data

#### Network configuration

Retrieve configuration data for the Syslog
module.

#### Procedure

\# Enter XML view.

\<Sysname\> xml

\# Notify the device of the NETCONF
capabilities supported on the client.

\<hello
xmlns\="urn:ietf:params:xml:ns:netconf:base:1.0"\>

  \<capabilities\>

    \<capability\>

           
urn:ietf:params:netconf:base:1.0

    \</capability\>

  \</capabilities\>

\</hello\>

\# Retrieve configuration data for the
Syslog module.

\<rpc message-id\="100"
xmlns\="urn:ietf:params:xml:ns:netconf:base:1.0"\>

  \<get-config\>

    \<source\>

      \<running/\>

    \</source\>

    \<filter
type\="subtree"\>

      \<top
xmlns\="http://www.h3c.com/netconf/config:1.0"\>

        \<Syslog/\>

      \</top\>

    \</filter\>

  \</get-config\>

\</rpc\>

#### Verifying the configuration

If the client receives the following text,
the \<get-config\> operation is successful:

\<?xml version\="1.0"
encoding\="UTF-8"?\>

\<rpc-reply
xmlns\="urn:ietf:params:xml:ns:netconf:base:1.0"
message-id\="100"\>

    \<data\>

        \<top
xmlns\="http://www.h3c.com/netconf/config:1.0"\>

            \<Syslog\>

                   
\<LogBuffer\>

                       
\<BufferSize\>120\</BufferSize\>

                   
\</LogBuffer\>

            \</Syslog\>

        \</top\>

    \</data\>

\</rpc-reply\>

### Example: Retrieving NETCONF session information

#### Network configuration

Get NETCONF session information.

#### Procedure

\# Enter XML view.

\<Sysname\> xml

\# Copy the following message to the client
to exchange capabilities with the device:

\<hello
xmlns\="urn:ietf:params:xml:ns:netconf:base:1.0"\>

  \<capabilities\>

    \<capability\>

           
urn:ietf:params:netconf:base:1.0

    \</capability\>

  \</capabilities\>

\</hello\>

\# Copy the following message to the client
to get the current NETCONF session information on the device:

\<?xml version\="1.0"
encoding\="UTF-8"?\>

\<rpc message-id\="100"
xmlns\="urn:ietf:params:xml:ns:netconf:base:1.0"\>

  \<get-sessions/\>

\</rpc\>

#### Verifying the configuration

If the client receives a message as
follows, the operation is successful:

\<?xml version\="1.0"
encoding\="UTF-8"?\>

\<rpc-reply
xmlns\="urn:ietf:params:xml:ns:netconf:base:1.0"
message-id\="100"\>

    \<get-sessions\>

        \<Session\>

            \<SessionID\>1\</SessionID\>

           
\<Line\>vty0\</Line\>

           
\<UserName\>\</UserName\>

           
\<Since\>2019-01-07T00:24:57\</Since\>

           
\<LockHeld\>false\</LockHeld\>

        \</Session\>

    \</get-sessions\>

\</rpc-reply\>

The output shows the following information:

·     The session ID of an existing NETCONF session is
1\. 

·     The login user type is vty0.

·     The login time is 2019-01-07T00:24:57.

·     The user does not hold the lock of the
configuration.

## Filtering data

### About data filtering

You can define a filter to filter
information when you perform a \<get\>, \<get-bulk\>,
\<get-config\>, or \<get-bulk-config\> operation. Data filtering
includes the following types:

·     Table-based filtering—Filters table information.

·     Column-based filtering—Filters information for a single column.

### Restrictions and guidelines for data filtering

For table-based filtering to take effect,
you must configure table-based filtering before column-based filtering.

### Table-based filtering

#### About this task

The namespace is http://www.h3c.com/netconf/base:1.0.
The attribute name is filter. For information about
the support for table-based match, see the NETCONF XML API references.

\# Copy the following text to the client to
retrieve the longest data with IP address 1.1.1.0
and mask length 24 from the IPv4 routing table:

\<rpc message-id\="100"
xmlns\="urn:ietf:params:xml:ns:netconf:base:1.0"
xmlns:h3c\="http://www.h3c.com/netconf/base:1.0"\>

  \<get\>

    \<filter
type\="subtree"\>

      \<top
xmlns\="http://www.h3c.com/netconf/data:1.0"\>

        \<Route\>

         \<Ipv4Routes\>

           \<RouteEntry
h3c:filter\="IP 1.1.1.0 MaskLen 24 longer"/\>

         \</Ipv4Routes\>

        \</Route\>

      \</top\>

    \</filter\>

  \</get\>

\</rpc\>

#### Restrictions and guidelines

To use table-based filtering, specify a
match criterion for the filter row attribute.

### Column-based filtering

#### About this task

Column-based filtering includes full match
filtering, regular expression match filtering, and conditional match filtering.
Full match filtering has the highest priority and conditional match filtering
has the lowest priority. When more than one filtering criterion is specified,
the one with the highest priority takes effect.

#### Full match filtering

You can specify an element value in an XML
message to implement full match filtering. If multiple element values are
provided, the system returns the data that matches all the specified values. 

\# Copy the following text to the client to
retrieve configuration data of all interfaces in UP state: 

\<rpc message-id\="100"
xmlns\="urn:ietf:params:xml:ns:netconf:base:1.0"\>

  \<get\>

    \<filter
type\="subtree"\>

      \<top
xmlns\="http://www.h3c.com/netconf/data:1.0"\>

        \<Ifmgr\>

          \<Interfaces\>

            \<Interface\>

              \<AdminStatus\>1\</AdminStatus\>

            \</Interface\>

          \</Interfaces\>

        \</Ifmgr\>

      \</top\>

    \</filter\>

  \</get\>

\</rpc\>

You can also specify an attribute name that
is the same as a column name of the current table at the row to implement full
match filtering. The system returns only configuration data that matches this
attribute name. The XML message equivalent to the above element-value-based
full match filtering is as follows:

\<rpc message-id\="100"
xmlns\="urn:ietf:params:xml:ns:netconf:base:1.0"\>

  \<get\>

    \<filter
type\="subtree"\>

      \<top
xmlns\="http://www.h3c.com/netconf/data:1.0"xmlns:data\="http://www.h3c.com/netconf/data:1.0"\>

        \<Ifmgr\>

          \<Interfaces\>

            \<Interface data:AdminStatus\="1"/\>

          \</Interfaces\>

        \</Ifmgr\>

      \</top\>

    \</filter\>

  \</get\>

\</rpc\>

The above examples show that both
element-value-based full match filtering and attribute-name-based full match
filtering can retrieve the same index and column information for all interfaces
in up state.

#### Regular expression match filtering

To implement a complex data filtering with
characters, you can add a regExp attribute for a
specific element. 

The supported data types include integer,
date and time, character string, IPv4 address, IPv4 mask, IPv6 address, MAC
address, OID, and time zone.

\# Copy the following text to the client to
retrieve the descriptions of interfaces, of which all the characters must be
upper-case letters from A to Z:

\<rpc message-id\="100"
xmlns\="urn:ietf:params:xml:ns:netconf:base:1.0"
xmlns:h3c\="http://www.h3c.com/netconf/base:1.0"\>

  \<get-config\>

    \<source\>

      \<running/\>

    \</source\>

    \<filter
type\="subtree"\>

      \<top
xmlns\="http://www.h3c.com/netconf/config:1.0"\>

        \<Ifmgr\>

          \<Interfaces\>

            \<Interface\>

              \<Description h3c:regExp\="^\[A-Z]\*$"/\>

            \</Interface\>

          \</Interfaces\>

        \</Ifmgr\>

      \</top\>

    \</filter\>

  \</get-config\>

\</rpc\>

#### Conditional match filtering

To implement a complex data filtering with
digits and character strings, you can add a match attribute for a specific element. [Table 3](#_Ref464730722) lists
the conditional match operators.

Table 3 Conditional match operators

| Operation | Operator | Remarks |
| --- | --- | --- |
| More than | match\="more:value" | More than the specified value. The supported data types include date, digit, and character string. || Less than | match\="less:value" | Less than the specified value. The supported data types include date, digit, and character string. || Not less than | match\="notLess:value" | Not less than the specified value. The supported data types include date, digit, and character string. || Not more than | match\="notMore:value" | Not more than the specified value. The supported data types include date, digit, and character string. || Equal | match\="equal:value" | Equal to the specified value. The supported data types include date, digit, character string, OID, and BOOL. || Not equal | match\="notEqual:value" | Not equal to the specified value. The supported data types include date, digit, character string, OID, and BOOL. || Include | match\="include:string" | Includes the specified string. The supported data types include only character string. || Not include | match\="exclude:string" | Excludes the specified string. The supported data types include only character string. || Start with | match\="startWith:string" | Starts with the specified string. The supported data types include character string and OID. || End with | match\="endWith:string" | Ends with the specified string. The supported data types include only character string. |










 

\# Copy the following text to the client to
retrieve extension information about the entity whose CPU usage is more than
50%:

\<rpc message-id\="100"
xmlns\="urn:ietf:params:xml:ns:netconf:base:1.0" xmlns:h3c\="http://www.h3c.com/netconf/base:1.0"\>

  \<get\>

    \<filter
type\="subtree"\>

      \<top
xmlns\="http://www.h3c.com/netconf/data:1.0"\>

        \<Device\>

          \<ExtPhysicalEntities\>

            \<Entity\>

              \<CpuUsage h3c:match\="more:50"\>\</CpuUsage\>

            \</Entity\>

         
\</ExtPhysicalEntities\>

        \</Device\>

      \</top\>

    \</filter\>

  \</get\>

\</rpc\>

### Example: Filtering data with regular expression match

#### Network configuration

Retrieve all data including Gig
in the Description column of the Interfaces table
under the Ifmgr module.

#### Procedure

\# Enter XML view.

\<Sysname\> xml

\# Notify the device of the NETCONF
capabilities supported on the client.

\<hello
xmlns\="urn:ietf:params:xml:ns:netconf:base:1.0"\>

  \<capabilities\>

    \<capability\>

           
urn:ietf:params:netconf:base:1.0

    \</capability\>

  \</capabilities\>

\</hello\>

\# Retrieve all data including Gig in the Description column
of the Interfaces table under the Ifmgr module.

\<?xml version\="1.0"?\>

\<rpc message-id\="100"
xmlns\="urn:ietf:params:xml:ns:netconf:base:1.0"
xmlns:h3c\="http://www.h3c.com/netconf/base:1.0"\>

  \<get\>

    \<filter
type\="subtree"\>

      \<top xmlns\="http://www.h3c.com/netconf/data:1.0"\>

        \<Ifmgr\>

          \<Interfaces\>

            \<Interface\>

              \<Description h3c:regExp\="(Gig)\+"/\>

            \</Interface\>

          \</Interfaces\>

        \</Ifmgr\>

      \</top\>

    \</filter\>

  \</get\>

\</rpc\>

#### Verifying the configuration

If the client receives the following text,
the operation is successful:

\<?xml version\="1.0"
encoding\="UTF-8"?\>

\<rpc-reply
xmlns\="urn:ietf:params:xml:ns:netconf:base:1.0"
xmlns:h3c\="http://www.h3c.com/netconf/base:1.0" message-id\="100"\>

    \<data\>

        \<top
xmlns\="http://www.h3c.com/netconf/data:1.0"\>

            \<Ifmgr\>

                \<Interfaces\>

                    \<Interface\>

                       
\<IfIndex\>2681\</IfIndex\>

                        \<Description\>Ten-GigabitEthernet1/0/1 Interface\</Description\>

                   
\</Interface\>

                    \<Interface\>

                       
\<IfIndex\>2685\</IfIndex\>

                        \<Description\>Ten-GigabitEthernet1/0/2 Interface\</Description\>

                   
\</Interface\>

                    \<Interface\>

                       
\<IfIndex\>2689\</IfIndex\>

                        \<Description\>Ten-GigabitEthernet1/0/3 Interface\</Description\>

                   
\</Interface\>

                 \<Interface\>

            \</Ifmgr\>

        \</top\>

    \</data\>

\</rpc-reply\>

### Example: Filtering data by conditional match

#### Network configuration

Retrieve data in the Name
column with the ifindex value not less than 5000 in the Interfaces table
under the Ifmgr module.

#### Procedure

\# Enter XML view.

\<Sysname\> xml

\# Notify the device of the NETCONF
capabilities supported on the client.

\<hello
xmlns\="urn:ietf:params:xml:ns:netconf:base:1.0"\>

  \<capabilities\>

    \<capability\>

           
urn:ietf:params:netconf:base:1.0

    \</capability\>

  \</capabilities\>

\</hello\>

\# Retrieve data in the Name column with the ifindex value not less than 5000 in
the Interfaces table under the Ifmgr module.

\<rpc message-id\="100"
xmlns\="urn:ietf:params:xml:ns:netconf:base:1.0"
xmlns:h3c\="http://www.h3c.com/netconf/base:1.0"\>

  \<get\>

    \<filter
type\="subtree"\>

      \<top
xmlns\="http://www.h3c.com/netconf/data:1.0"\>

        \<Ifmgr\>

          \<Interfaces\>

            \<Interface\>

              \<IfIndex h3c:match\="notLess:5000"/\>

              \<Name/\>

            \</Interface\>

          \</Interfaces\>

        \</Ifmgr\>

      \</top\>

    \</filter\>

  \</get\>

\</rpc\>

#### Verifying the configuration

If the client receives the following text,
the operation is successful:

\<?xml version\="1.0"
encoding\="UTF-8"?\>

\<rpc-reply
xmlns\="urn:ietf:params:xml:ns:netconf:base:1.0"
xmlns:h3c\="http://www.h3c.com/netconf/base:1.0"
message-id\="100"\>

    \<data\>

        \<top
xmlns\="http://www.h3c.com/netconf/data:1.0"\>

            \<Ifmgr\>

                \<Interfaces\>

                    \<Interface\>

                        \<IfIndex\>7241\</IfIndex\>

                        \<Name\>NULL0\</Name\>

                   
\</Interface\>

                \</Interfaces\>

            \</Ifmgr\>

        \</top\>

    \</data\>

\</rpc-reply\>

## Locking or unlocking the running configuration

### About configuration locking and unlocking

Multiple methods are available for
configuring the device, such as CLI, NETCONF, and SNMP. Before configuring,
managing, or troubleshooting the device, you can lock the configuration to
prevent other users from changing the device configuration. After you lock the
configuration, only you can perform \<edit-config\> operations to change
the configuration or unlock the configuration. Other users can only read the
configuration.

If you close your NETCONF session, the
system unlocks the configuration. You can also manually unlock the configuration.

### Restrictions and guidelines for configuration locking and unlocking

The \<lock\> operation locks the
running configuration of the device. You cannot use it to lock the
configuration for a specific module.

### Locking the running configuration

\# Copy the following text to the client to
lock the running configuration:

\<?xml version\="1.0"
encoding\="UTF-8"?\>

  \<rpc message-id\="100"
xmlns\="urn:ietf:params:xml:ns:netconf:base:1.0"\>

    \<lock\>

      \<target\>

        \<running/\>

      \</target\>

    \</lock\>

  \</rpc\>

If the \<lock\> operation succeeds, the
device returns a response in the following format:

\<?xml version\="1.0"
encoding\="UTF-8"?\>

  \<rpc-reply
message-id\="100"
xmlns\="urn:ietf:params:xml:ns:netconf:base:1.0"\>

     \<ok/\>

\</rpc-reply\>

### Unlocking the running configuration

\# Copy the following text to the client to
unlock the running configuration:

\<?xml version\="1.0"
encoding\="UTF-8"?\>

  \<rpc message-id\="100"
xmlns\="urn:ietf:params:xml:ns:netconf:base:1.0"\>

    \<unlock\>

      \<target\>

        \<running/\>

      \</target\>

    \</unlock\>

  \</rpc\>

If the \<unlock\> operation succeeds,
the device returns a response in the following format:

\<?xml version\="1.0"
encoding\="UTF-8"?\>

    \<rpc-reply
message-id\="100"
xmlns\="urn:ietf:params:xml:ns:netconf:base:1.0"\>

      \<ok/\>

\</rpc-reply\>

### Example: Locking the running configuration

#### Network configuration

Lock the device configuration so other
users cannot change the device configuration.

#### Procedure

\# Enter XML view.

\<Sysname\> xml

\# Notify the device of the NETCONF
capabilities supported on the client.

\<hello
xmlns\="urn:ietf:params:xml:ns:netconf:base:1.0"\>

    \<capabilities\>

        \<capability\>

           
urn:ietf:params:netconf:base:1.0

        \</capability\>

    \</capabilities\>

\</hello\>

\# Lock the configuration.

\<?xml version\="1.0"
encoding\="UTF-8"?\>

  \<rpc message-id\="100"
xmlns\="urn:ietf:params:xml:ns:netconf:base:1.0"\>

    \<lock\>

      \<target\>

        \<running/\>

      \</target\>

    \</lock\>

  \</rpc\>

#### Verifying the configuration

If the client receives the following
response, the \<lock\> operation is successful:

\<?xml version\="1.0"
encoding\="UTF-8"?\>

    \<rpc-reply
message-id\="100"
xmlns\="urn:ietf:params:xml:ns:netconf:base:1.0"\>

      \<ok/\>

\</rpc-reply\>

If another client sends a lock request, the
device returns the following response:

\<?xml version\="1.0"
encoding\="UTF-8"?\>

\<rpc-reply
message-id\="100"
xmlns\="urn:ietf:params:xml:ns:netconf:base:1.0"\>

\<rpc-error\>

 
\<error-type\>protocol\</error-type\>

  \<error-tag\>lock-denied\</error-tag\>

 
\<error-severity\>error\</error-severity\>

  \<error-message
xml:lang\="en"\> Lock failed because the NETCONF lock is held by
another session.\</error-message\>

  \<error-info\>

   
\<session-id\>1\</session-id\>

  \</error-info\>

  \</rpc-error\>

\</rpc-reply\>

The output shows that the \<lock\>
operation failed. The client with session ID 1 is holding the lock,

## Modifying the configuration

### About the \<edit-config\> operation

The \<edit-config\> operation includes the following
operations:  merge, create, replace, remove, delete, default-operation,
error-option, test-option, and incremental. For more information about the
operations, see "[Supported NETCONF operations](#_Ref127437999)."

### Procedure

\# Copy the following text to perform the
\<edit-config\> operation:

\<?xml version\="1.0"?\>

\<rpc message-id\="100"
xmlns\="urn:ietf:params:xml:ns:netconf:base:1.0"\>

  \<edit-config\>

    \<target\>\<running\>\</running\>\</target\>

    \<error-option\>

       error-option

    \</error-option\>

    \<config\>

      \<top
xmlns\="http://www.h3c.com/netconf/config:1.0"\>

        Specify
the module name, submodule name, table name, and column name

      \</top\>

    \</config\>

  \</edit-config\>

\</rpc\>

The \<error-option\> element indicates
the action to be taken in response to an error that occurs during the
operation. It has the following values:

 

| Value | Description |
| --- | --- |
| stop-on-error | Stops the \<edit-config\> operation. || continue-on-error | Continues the \<edit-config\> operation. || rollback-on-error | Rolls back the configuration to the configuration before the \<edit-config\> operation was performed.  By default, an \<edit-config\> operation cannot be performed while the device is rolling back the configuration. If the rollback time exceeds the maximum time that the client can wait, the client determines that the \<edit-config\> operation has failed and performs the operation again. Because the previous rollback is not completed, the operation triggers another rollback. If this process repeats itself, CPU and memory resources will be exhausted and the device will reboot.  To allow an \<edit-config\> operation to be performed during a configuration rollback, perform an \<action\> operation to change the value of the DisableEditConfigWhenRollback attribute to false. |



 

If the \<edit-config\> operation
succeeds, the device returns a response in the following format:

\<?xml version\="1.0"\>

\<rpc-reply
message-id\="100" xmlns\="urn:ietf:params:xml:ns:netconf:base:1.0"\>

  \<ok/\>

\</rpc-reply\>

You can also perform the \<get\>
operation to verify that the current element value is the same as the value
specified through the \<edit-config\> operation.

### Example: Modifying the configuration

#### Network configuration

Change the log buffer size for the Syslog
module to 512\.

#### Procedure

\# Enter XML view.

\<Sysname\> xml

\# Notify the device of the NETCONF
capabilities supported on the client.

\<hello xmlns\="urn:ietf:params:xml:ns:netconf:base:1.0"\>

  \<capabilities\>

   
\<capability\>urn:ietf:params:netconf:base:1.0\</capability\>

  \</capabilities\>

\</hello\>

\# Change the log buffer size for the Syslog
module to 512\.

\<rpc message-id\="100" xmlns\="urn:ietf:params:xml:ns:netconf:base:1.0"
xmlns:web\="urn:ietf:params:xml:ns:netconf:base:1.0"\>

  \<edit-config\>

    \<target\>

      \<running/\>

    \</target\>

    \<config\>

      \<top
xmlns\="http://www.h3c.com/netconf/config:1.0"
web:operation\="merge"\>

        \<Syslog\>

          \<LogBuffer\>

            \<BufferSize\>512\</BufferSize\>

          \</LogBuffer\>

        \</Syslog\>

      \</top\>

    \</config\>

  \</edit-config\>

\</rpc\>

#### Verifying the configuration

If the client receives the following text,
the \<edit-config\> operation is successful:

\<?xml version\="1.0"
encoding\="UTF-8"?\>

\<rpc-reply
message-id\="100"
xmlns\="urn:ietf:params:xml:ns:netconf:base:1.0"\>

    \<ok/\>

\</rpc-reply\>

## Saving the running configuration

### About the \<save\> operation

A \<save\> operation saves the running
configuration to a configuration file and specifies the file as the main
next-startup configuration file.

### Restrictions and guidelines

The \<save\> operation is resource
intensive. Do not perform this operation when system resources are heavily
occupied.

### Procedure

\# Copy the following text to the client:

\<?xml version\="1.0"
encoding\="UTF-8"?\>

\<rpc message-id\="100"
xmlns\="urn:ietf:params:xml:ns:netconf:base:1.0"\>

  \<save
OverWrite\="false" Safely\="false"\>

    \<file\>Configuration
file name\</file\>

  \</save\>

\</rpc\>

 

| Item | Description |
| --- | --- |
| file | Specifies a .cfg configuration file by its name. The name must start with the storage medium name.  If you specify the file column, a file name is required.  If the Binary-only attribute is false, the device saves the running configuration to both the text and binary configuration files.  ·     If the specified .cfg file does not exist, the device creates the binary and text configuration files to save the running configuration.  ·     If you do not specify the file column, the device saves the running configuration to the text and binary next-startup configuration files. || OverWrite | Determines whether to overwrite the specified file if the file already exists. The following values are available:  ·     true—Overwrite the file.  ·     false—Do not overwrite the file. The running configuration cannot be saved, and the system displays an error message.  The default value is true. || Safely | Determines whether to save the configuration file in safe mode or fast mode.  Available values:  ·     true—Saves the configuration in safe mode. In this mode, the system saves the configuration in a temporary file and starts overwriting the target next-startup configuration file after the save operation is complete. If a disruptive event such as a reboot or power failure occurs during the save operation, the device can still start up with the original next-startup configuration file.  ·     false—Saves the configuration in fast mode. In this mode, the system directly overwrites the target next-startup configuration file. If a disruptive event such as a reboot, power failure, out-of-memory, out-of-storage event occurs during this process, the next-startup configuration file is lost. You must specify a new startup configuration file after the device reboots.  By default, the Safely attribute is set to false if you do not specify a value for it.  Safe mode is slower than fast mode, but more secure. As a best practice, use the safe mode if the power source is not reliable or you are remotely configuring the device. |



 

If the \<save\> operation succeeds, the
device returns a response in the following format:

\<?xml version\="1.0"
encoding\="UTF-8"?\>

    \<rpc-reply message-id\="100"
xmlns\="urn:ietf:params:xml:ns:netconf:base:1.0"\>

      \<ok/\>

\</rpc-reply\>

### Example: Saving the running configuration

#### Network configuration

Save the running configuration to the config.cfg file.

#### Procedure

\# Enter XML view.

\<Sysname\> xml

\# Notify the device of the NETCONF
capabilities supported on the client.

\<hello xmlns\="urn:ietf:params:xml:ns:netconf:base:1.0"\>

  \<capabilities\>

    \<capability\>

           
urn:ietf:params:netconf:base:1.0

    \</capability\>

  \</capabilities\>

\</hello\>

\# Save the running configuration of the
device to the config.cfg file.

\<?xml version\="1.0"
encoding\="UTF-8"?\>

\<rpc message-id\="100"
xmlns\="urn:ietf:params:xml:ns:netconf:base:1.0"\>

  \<save\>

    \<file\>config.cfg\</file\>

  \</save\>

\</rpc\>

#### Verifying the configuration

If the client receives the following
response, the \<save\> operation is successful:

\<?xml version\="1.0"
encoding\="UTF-8"?\>

    \<rpc-reply
message-id\="100"
xmlns\="urn:ietf:params:xml:ns:netconf:base:1.0"\>

      \<ok/\>

\</rpc-reply\>

## Loading the configuration

### About the \<load\> operation

The \<load\> operation merges the
configuration from a configuration file into the running configuration as
follows:

·     Loads settings that do not exist in the running
configuration.

·     Overwrites settings that already exist in the
running configuration.

### Restrictions and guidelines

When you perform a \<load\> operation,
follow these restrictions and guidelines:

·     The \<load\> operation is resource
intensive. Do not perform this operation when the system resources are heavily
occupied.

·     Some settings in a configuration file might
conflict with the existing settings. For the settings in the file to take
effect, delete the existing conflicting settings, and then load the
configuration file.

### Procedure

\# Copy the following text to the client to
load a configuration file for the device:

\<?xml version\="1.0"
encoding\="UTF-8"?\>

\<rpc message-id\="100"
xmlns\="urn:ietf:params:xml:ns:netconf:base:1.0"\>

  \<load\>

   \<file\>Configuration file name\</file\>

  \</load\>

\</rpc\>

The configuration file name must start with
the storage media name and end with the .cfg
extension.

If the \<load\> operation succeeds, the
device returns a response in the following format:

\<?xml version\="1.0"
encoding\="UTF-8"?\>

    \<rpc-reply message-id\="100"
xmlns\="urn:ietf:params:xml:ns:netconf:base:1.0"\>

      \<ok/\>

\</rpc-reply\>

## Rolling back the configuration

### Restrictions and guidelines

The \<rollback\> operation is resource
intensive. Do not perform this operation when the system resources are heavily
occupied.

By default, an \<edit-config\>
operation cannot be performed while the device is rolling back the configuration.
To allow an \<edit-config\> operation to be performed during a
configuration rollback, perform an \<action\> operation to change the value
of the DisableEditConfigWhenRollback attribute to false.

### Rolling back the configuration based on a configuration file

\# Copy the following text to the client to
roll back the running configuration to the configuration in a configuration
file:

\<?xml version\="1.0"
encoding\="UTF-8"?\>

\<rpc message-id\="100"
xmlns\="urn:ietf:params:xml:ns:netconf:base:1.0"\>

  \<rollback\>

    \<file\>Specify the
configuration file name\</file\>

  \</rollback\>

\</rpc\>

If the \<rollback\> operation succeeds,
the device returns a response in the following format:

\<?xml version\="1.0"
encoding\="UTF-8"?\>

    \<rpc-reply
message-id\="100"
xmlns\="urn:ietf:params:xml:ns:netconf:base:1.0"\>

      \<ok/\>

\</rpc-reply\>

### Rolling back the configuration based on a rollback point

#### About this task

You can roll back the running configuration
based on a rollback point when one of the following situations occurs:

·     A NETCONF client sends a rollback request.

·     The NETCONF session idle time is longer than the
rollback idle timeout time.

·     A NETCONF client is unexpectedly disconnected
from the device.

#### Restrictions and guidelines

Multiple users might simultaneously
configure the device. As a best practice, lock the system before rolling back
the configuration to prevent other users from modifying the running
configuration.

#### Procedure

**1\.**Lock the running configuration. For more
information, see "[Locking or unlocking the running
configuration](#_Ref478486643)."

**2\.**Enable configuration rollback based
on a rollback point.

\# Copy the following text to the client
to perform a \<save-point\>/\<begin\> operation:

\<rpc
message-id\="100" xmlns\="urn:ietf:params:xml:ns:netconf:base:1.0"\>

      \<save-point\>

        \<begin\>

         
\<confirm-timeout\>100\</confirm-timeout\>

       \</begin\>

      \</save-point\>

\</rpc\>

 

| Item | Description |
| --- | --- |
| confirm-timeout | Specifies the rollback idle timeout time in the range of 1 to 65535 seconds. The default is 600 seconds. This item is optional. |

 

If the \<save-point/begin\> operation
succeeds, the device returns a response in the following format:

\<rpc-reply
message-id\="100"
xmlns\="urn:ietf:params:xml:ns:netconf:base:1.0"\>

  \<data\>

    \<save-point\>

       \<commit\>

         
\<commit-id\>1\</commit-id\>

       \</commit\>

    \</save-point\>

  \</data\>

\</rpc-reply\>

**3\.**Modify the running configuration. For more information, see "[Modifying the configuration](#_Ref478560530)."

**4\.**Mark the rollback point.

The system supports a maximum of 50
rollback points. If the limit is reached, specify the force attribute
for the \<save-point\>/\<commit\> operation to overwrite the earliest
rollback point.

\# Copy the following text to the client
to perform a \<save-point\>/\<commit\> operation:

\<rpc
message-id\="100"
xmlns\="urn:ietf:params:xml:ns:netconf:base:1.0"\>

  \<save-point\>

    \<commit\>

      \<label\>SUPPORT
VLAN\<label\>

      \<comment\>vlan 1 to
100 and interfaces.\</comment\>

     \</commit\>

  \</save-point\>

\</rpc\>

The \<label\> and \<comment\>
elements are optional.

If the \<save-point\>/\<commit\>
operation succeeds, the device returns a response in the following format:

\<rpc-reply
message-id\="100"
xmlns\="urn:ietf:params:xml:ns:netconf:base:1.0"\>

  \<data\>

    \<save-point\>

       \<commit\>

         
\<commit-id\>2\</commit-id\>

       \</commit\>

    \</save-point\>

  \</data\>

\</rpc-reply\>

**5\.**Retrieve the rollback point configuration records.

The following text shows the message
format for a \<save-point/get-commits\> request:

\<rpc
message-id\="100"
xmlns\="urn:ietf:params:xml:ns:netconf:base:1.0"\>

  \<save-point\>

    \<get-commits\>

      \<commit-id/\>

      \<commit-index/\>

      \<commit-label/\>

    \</get-commits\>

  \</save-point\>

\</rpc\>

Specify the \<commit-id/\>,
\<commit-index/\>, or \<commit-label/\> element to retrieve the
specified rollback point configuration records. If no element is specified, the
operation retrieves records for all rollback point settings.

\# Copy the following text to the client
to perform a \<save-point\>/\<get-commits\> operation:

\<rpc
message-id\="100"
xmlns\="urn:ietf:params:xml:ns:netconf:base:1.0"\>

  \<save-point\>

    \<get-commits\>

     
\<commit-label\>SUPPORT VLAN\</commit-label\>

    \</get-commits\>

  \</save-point\>

\</rpc\>

If the \<save-point/get-commits\>
operation succeeds, the device returns a response in the following format:

\<rpc-reply
message-id\="100"
xmlns\="urn:ietf:params:xml:ns:netconf:base:1.0"\>

   \<data\>

      \<save-point\>

        
\<commit-information\>

          
\<CommitID\>2\</CommitID\>

          
\<TimeStamp\>Sun Jan 1 11:30:28 2019\</TimeStamp\>

          
\<UserName\>test\</UserName\>

          
\<Label\>SUPPORT VLAN\</Label\>

        
\</commit-information\>

    \</save-point\>

  \</data\>

\</rpc-reply\>

**6\.**Retrieve the configuration data
corresponding to a rollback point.

The following text shows the message
format for a \<save-point\>/\<get-commit-information\> request:

\<rpc
message-id\="100" xmlns\="urn:ietf:params:xml:ns:netconf:base:1.0"\>

  \<save-point\>

   
\<get-commit-information\>

      
\<commit-information\>

         \<commit-id/\>

         \<commit-index/\>

         \<commit-label/\>

      
\</commit-information\>

      
\<compare-information\>

         \<commit-id/\>

         \<commit-index/\>

         \<commit-label/\>

      
\</compare-information\>

   
\</get-commit-information\>

  \</save-point\>

\</rpc\>

Specify one of the following elements:
\<commit-id/\>, \<commit-index/\>, and \<commit-label/\>. The
\<compare-information\> element is optional.

 

| Item | Description |
| --- | --- |
| commit-id | Uniquely identifies a rollback point. || commit-index | Specifies 50 most recently configured rollback points. The value of 0 indicates the most recently configured one and 49 indicates the earliest configured one. || commit-label | Specifies a unique label for a rollback point. || get-commit-information | Retrieves the configuration data corresponding to the most recently configured rollback point. |




 

\# Copy the following text to the client
to perform a \<save-point\>/\<get-commit-information\> operation:

\<rpc
message-id\="100"
xmlns\="urn:ietf:params:xml:ns:netconf:base:1.0"\>

  \<save-point\>

   
\<get-commit-information\>

              
\<commit-information\>

                 
\<commit-label\>SUPPORT VLAN\</commit-label\>

              
\</commit-information\>

    \</get-commit-information\>

  \</save-point\>

\</rpc\>

If the
\<save-point/get-commit-information\> operation succeeds, the device
returns a response in the following format:

\<rpc-reply
message-id\="100"
xmlns\="urn:ietf:params:xml:ns:netconf:base:1.0"\>

   \<data\>

     \<save-point\>

       
\<commit-information\>

           \<content\>

              …

              interface vlan 1

              …

           \</content\>

       
\</commit-information\>

     \</save-point\>

   \</data\>

\</rpc-reply\>

**7\.**Roll back the configuration based on a
rollback point.

The configuration can also be
automatically rolled back based on the most recently configured rollback point
when the NETCONF session idle timer expires.

\# Copy the
following text to the client to perform a \<save-point\>/\<rollback\> operation:

\<rpc
message-id\="100"
xmlns\="urn:ietf:params:xml:ns:netconf:base:1.0"\>

  \<save-point\>

    \<rollback\>

      \<commit-id/\>

      \<commit-index/\>

      \<commit-label/\>

    \</rollback\>

  \</save-point\>

\</rpc\>

Specify one of the following elements:
\<commit-id/\>, \<commit-index/\>, and \<commit-label/\>. If no
element is specified, the operation rolls back configuration based on the most
recently configured rollback point.

 

| Item | Description |
| --- | --- |
| commit-id | Uniquely identifies a rollback point. || commit-index | Specifies 50 most recently configured rollback points. The value of 0 indicates the most recently configured one and 49 indicates the earliest configured one. || commit-label | Specifies the unique label of a rollback point. |



 

If the \<save-point/rollback\> operation
succeeds, the device returns a response in the following format:

\<rpc-reply
message-id\="100"
xmlns\="urn:ietf:params:xml:ns:netconf:base:1.0"\>

  \<ok\>\</ok\>

\</rpc-reply\>

**8\.**End the rollback configuration.

\# Copy the
following text to the client to perform a \<save-point\>/\<end\>
operation:

\<rpc
message-id\="100"
xmlns\="urn:ietf:params:xml:ns:netconf:base:1.0"\>

  \<save-point\>

    \<end/\>

  \</save-point\>

\</rpc\>

If the \<save-point/end\> operation
succeeds, the device returns a response in the following format:

\<rpc-reply
message-id\="100"
xmlns\="urn:ietf:params:xml:ns:netconf:base:1.0"\>

   \<ok/\>

\</rpc-reply\>

**9\.**Unlock the configuration. For more
information, see "[Locking or unlocking the running configuration](#_Ref478486643)."

## Performing CLI operations through NETCONF

### About CLI operations through NETCONF

You can enclose command lines in XML
messages to configure the device.

### Restrictions and guidelines

Performing CLI operations through NETCONF
is resource intensive. As a best practice, do not perform the following tasks:

·     Enclose multiple command lines in one XML
message.

·     Use NETCONF to perform a CLI operation when
other users are performing NETCONF CLI operations.

### Procedure

\# Copy the following text to the client to
execute the commands:

\<?xml version\="1.0"
encoding\="UTF-8"?\>

\<rpc message-id\="100"
xmlns\="urn:ietf:params:xml:ns:netconf:base:1.0"\>

  \<CLI\>

    \<Execution\>

      Commands

    \</Execution\>

  \</CLI\>

\</rpc\>

The \<Execution\> element can contain
multiple commands, with one command on one line. 

If the CLI operation succeeds, the device
returns a response in the following format:

\<?xml version\="1.0"
encoding\="UTF-8"?\>

\<rpc-reply
message-id\="100" xmlns\="urn:ietf:params:xml:ns:netconf:base:1.0"\>

  \<CLI\>

    \<Execution\>

      \<!\[CDATA\[Responses to
the commands]]\>

    \</Execution\>

  \</CLI\>

\</rpc-reply\>

### Example: Performing CLI operations

#### Network configuration

Send the display vlan command to the device.

#### Procedure

\# Enter XML view.

\<Sysname\> xml

\# Notify the device of the NETCONF
capabilities supported on the client.

\<hello
xmlns\="urn:ietf:params:xml:ns:netconf:base:1.0"\>

  \<capabilities\>

    \<capability\>

           
urn:ietf:params:netconf:base:1.0

    \</capability\>

  \</capabilities\>

\</hello\>

\# Copy the following text to the client to
execute the display vlan command:

\<?xml version\="1.0"
encoding\="UTF-8"?\>

\<rpc message-id\="100"
xmlns\="urn:ietf:params:xml:ns:netconf:base:1.0"\>

  \<CLI\>

    \<Execution\>

          display vlan

    \</Execution\>

  \</CLI\>

\</rpc\>

#### Verifying the configuration

If the client receives the following text,
the operation is successful:

\<?xml version\="1.0"
encoding\="UTF-8"?\>

\<rpc-reply
message-id\="100"
xmlns\="urn:ietf:params:xml:ns:netconf:base:1.0"\>

  \<CLI\>

    \<Execution\>\<!\[CDATA\[

\<Sysname\>display vlan

 Total VLANs: Problem 12
1                                                                 

 The VLANs
include:                                                             

 1(default)

   ]]\>

   \</Execution\>

  \</CLI\>

\</rpc-reply\>

## Subscribing to events

### About event subscription

When an event takes place on the device,
the device sends information about the event to NETCONF clients that have
subscribed to the event.

### Restrictions and guidelines

Event subscription is not supported for
NETCONF over SOAP sessions.

A subscription takes effect only on the
current session. It is canceled when the session is terminated.

If you do not specify the event stream to
be subscribed to, the device sends syslog event notifications to the NETCONF
client.

### Subscribing to syslog events

\# Copy the following message to the client
to complete the subscription:

\<?xml version\="1.0"
encoding\="UTF-8"?\>

\<rpc message-id\="100"
xmlns\="urn:ietf:params:xml:ns:netconf:base:1.0"\>

  \<create-subscription
xmlns\="urn:ietf:params:xml:ns:netconf:notification:1.0"\>

    \<stream\>NETCONF\</stream\>

      \<filter\>

        \<event
xmlns\="http://www.h3c.com/netconf/event:1.0"\>

          \<Code\>code\</Code\>

            \<Group\>group\</Group\>

              \<Severity\>severity\</Severity\>

        \</event\>

      \</filter\>

      \<startTime\>start-time\</startTime\>

      \<stopTime\>stop-time\</stopTime\>

   \</create-subscription\>

\</rpc\>

 

| Item | Description |
| --- | --- |
| stream | Specifies the event stream. The name for the syslog event stream is NETCONF. || event | Specifies the event. For information about the events to which you can subscribe, see the system log message references for the device. || code | Specifies the mnemonic symbol of the log message. || group | Specifies the module name of the log message. || severity | Specifies the severity level of the log message. || start-time | Specifies the start time of the subscription. || stop-time | Specifies the end time of the subscription. |







 

If the subscription succeeds, the device
returns a response in the following format:

\<?xml version\="1.0"
encoding\="UTF-8"?\>

\<rpc-reply
message-id\="100"
xmlns:netconf\="urn:ietf:params:xml:ns:netconf:base:1.0"\>

    \<ok/\>

\</rpc-reply\>

If the subscription fails, the device
returns an error message in the following format:

\<?xml version\="1.0"
encoding\="UTF-8"?\>

\<rpc-reply
message-id\="100"
xmlns\="urn:ietf:params:xml:ns:netconf:base:1.0"\>

\<rpc-error\>

   \<error-type\>error-type\</error-type\>

   \<error-tag\>error-tag\</error-tag\>

   \<error-severity\>error-severity\</error-severity\>

   \<error-message
xml:lang\="en"\>error-message\</error-message\>

\</rpc-error\>

\</rpc-reply\>

For more information about error messages,
see RFC 4741\.

### Subscribing to events monitored by NETCONF

After you subscribe to events as described
in this section, NETCONF regularly polls the subscribed events and sends the
events that match the subscription condition to the NETCONF client.

\# Copy the following message to the client
to complete the subscription:

\<?xml version\="1.0"
encoding\="UTF-8"?\>

\<rpc message-id\="100"
xmlns\="urn:ietf:params:xml:ns:netconf:base:1.0"\>

\<create-subscription
xmlns\='urn:ietf:params:xml:ns:netconf:notification:1.0'\>

  \<stream\>NETCONF\_MONITOR\_EXTENSION\</stream\>

  \<filter\>

    \<NetconfMonitor
xmlns\='http://www.h3c.com/netconf/monitor:1.0'\>

      \<XPath\>XPath\</XPath\>

      \<Interval\>interval\</Interval\>

      \<ColumnConditions\>

        \<ColumnCondition\>

          \<ColumnName\>ColumnName\</ColumnName\>

          \<ColumnValue\>ColumnValue\</ColumnValue\>

          \<ColumnCondition\>ColumnCondition\</ColumnCondition\> 

        \</ColumnCondition\>

      \</ColumnConditions\>

     
\<MustIncludeResultColumns\>

        \<ColumnName\>columnName\</ColumnName\>

     
\</MustIncludeResultColumns\>

  \</NetconfMonitor\>

  \</filter\>

\<startTime\>start-time\</startTime\>

\<stopTime\>stop-time\</stopTime\>

\</create-subscription\>

\</rpc\>

 

| Item | Description |
| --- | --- |
| stream | Specifies the event stream. The name for the event stream is NETCONF\_MONITOR\_EXTENSION. || NetconfMonitor | Specifies the filtering information for the event. || XPath | Specifies the path of the event in the format of ModuleName\[/SubmoduleName]/TableName. || interval | Specifies the interval for NETCONF to obtain events that matches the subscription condition. The value range is 1 to 4294967 seconds. The default value is 300 seconds. || ColumnName | Specifies the name of a column in the format of \[GroupName.]ColumnName. || ColumnValue | Specifies the baseline value. || ColumnCondition | Specifies the operator:  ·     more.  ·     less.  ·     notLess.  ·     notMore.  ·     equal.  ·     notEqual.  ·     include.  ·     exclude.  ·     startWith.  ·     endWith.  Choose an operator according to the type of the baseline value. || start-time | Specifies the start time of the subscription. || stop-time | Specifies the end time of the subscription. |









 

If the subscription succeeds, the device
returns a response in the following format:

\<?xml version\="1.0"
encoding\="UTF-8"?\>

\<rpc-reply
message-id\="100" xmlns:netconf\="urn:ietf:params:xml:ns:netconf:base:1.0"\>

    \<ok/\>

\</rpc-reply\>

### Subscribing to events reported by modules

After you subscribe to events as described
in this section, the specified modules report subscribed events to NETCONF.
NETCONF sends the events to the NETCONF client.

\# Copy the following message to the client
to complete the subscription:

\<?xml version\="1.0"
encoding\="UTF-8"?\>

\<rpc message-id\="100"
xmlns\="urn:ietf:params:xml:ns:netconf:base:1.0"
xmlns:xs\="http://www.h3c.com/netconf/base:1.0"\>

\<create-subscription
xmlns\="urn:ietf:params:xml:ns:netconf:notification:1.0"\>

\<stream\>XXX\_STREAM\</stream\>

   \<filter
type\="subtree"\>

\<event xmlns\="http://www.h3c.com/netconf/event:1.0/xxx-features-list-name:1.0"\>

       \<ColumnName xs:condition\="Condition"\>value\</ColumnName\>

\</event\>

\</filter\>

\<startTime\>start-time\</startTime\>

\<stopTime\>stop-time\</stopTime\>

\</create-subscription\>

\</rpc\>

 

| Item | Description |
| --- | --- |
| stream | Specifies the event stream. Supported event streams vary by device model. || event | Specifies the event name. An event stream includes multiple events. The events use the same namespaces as the event stream. || ColumnName | Specifies the name of a column. || Condition | Specifies the operator:  ·     more.  ·     less.  ·     notLess.  ·     notMore.  ·     equal.  ·     notEqual.  ·     include.  ·     exclude.  ·     startWith.  ·     endWith.  Choose an operator according to the type of the baseline value. || value | Specifies the baseline value for the column. || start-time | Specifies the start time of the subscription. || stop-time | Specifies the end time of the subscription. |







 

If the subscription succeeds, the device
returns a response in the following format:

\<?xml version\="1.0" encoding\="UTF-8"?\>

\<rpc-reply
message-id\="100"
xmlns:netconf\="urn:ietf:params:xml:ns:netconf:base:1.0"\>

    \<ok/\>

\</rpc-reply\>

### Canceling an event subscription

\# Copy the following message to the client
to cancel a subscription:

\<rpc message-id\="100"
xmlns\="urn:ietf:params:xml:ns:netconf:base:1.0"\>

  \<cancel-subscription
xmlns\="urn:ietf:params:xml:ns:netconf:notification:1.0"\>

    \<stream\>XXX\_STREAM\</stream\>

  \</cancel-subscription\>

\</rpc\>

 

| Item | Description |
| --- | --- |
| stream | Specifies the event stream. |

 

If the cancelation succeeds, the device
returns a response in the following format:

\<?xml version\="1.0"
encoding\="UTF-8"?\>

\<rpc-reply xmlns\="urn:ietf:params:xml:ns:netconf:base:1.0"
message-id\="100"\>

    \<ok/\>

\</rpc-reply\>

If the subscription to be canceled does not
exist, the device returns an error message in the following format:

\<?xml version\="1.0"
encoding\="UTF-8"?\>

\<rpc-reply message-id\="100"
xmlns\="urn:ietf:params:xml:ns:netconf:base:1.0"\>

\<rpc-error\>

  
\<error-type\>error-type\</error-type\>

  
\<error-tag\>error-tag\</error-tag\>

  
\<error-severity\>error-severity\</error-severity\>

\<error-message
xml:lang\="en"\>The subscription stream to be canceled doesn't exist: Stream name\=XXX\_STREAM.\</error-message\>

\</rpc-error\>

\</rpc-reply\>

### Example: Subscribing to syslog events

#### Network configuration

Configure a client to subscribe to syslog
events with no time limitation. After the subscription, all events on the
device are sent to the client before the session between the device and client
is terminated.

#### Procedure

\# Enter XML view.

\<Sysname\> xml

\# Notify the device of the NETCONF
capabilities supported on the client.

\<hello
xmlns\="urn:ietf:params:xml:ns:netconf:base:1.0"\>

  \<capabilities\>

    \<capability\>

           
urn:ietf:params:netconf:base:1.0

    \</capability\>

  \</capabilities\>

\</hello\>

\# Subscribe to syslog events with no time
limitation.

\<rpc message-id\="100"
xmlns\="urn:ietf:params:xml:ns:netconf:base:1.0"\>

  \<create-subscription
xmlns\="urn:ietf:params:xml:ns:netconf:notification:1.0"\>

   
\<stream\>NETCONF\</stream\>

  \</create-subscription\>

\</rpc\>

#### Verifying the configuration

\# If the client receives the following
response, the subscription is successful:

\<?xml version\="1.0"
encoding\="UTF-8"?\>

\<rpc-reply
xmlns\="urn:ietf:params:xml:ns:netconf:base:1.0"
message-id\="100"\>

    \<ok/\>

\</rpc-reply\>

\# When another client (192.168.100.130)
logs in to the device, the device sends a notification to the client that has
subscribed to all events:

\<?xml version\="1.0"
encoding\="UTF-8"?\>

\<notification
xmlns\="urn:ietf:params:xml:ns:netconf:notification:1.0"\>

    \<eventTime\>2019-01-04T12:30:52\</eventTime\>

    \<event
xmlns\="http://www.h3c.com/netconf/event:1.0"\>

        \<Group\>SHELL\</Group\>

       
\<Code\>SHELL\_LOGIN\</Code\>

        \<Slot\>1\</Slot\>

       
\<Severity\>Notification\</Severity\>

        \<context\>VTY logged in
from 192.168.100.130.\</context\>

    \</event\>

\</notification\>

## Terminating NETCONF sessions

### About NETCONF session termination

NETCONF allows one client to terminate the
NETCONF sessions of other clients. A client whose session is terminated returns
to user view.

### Procedure

\# Copy the following message to the client
to terminate a NETCONF session:

\<rpc message-id\="100"
xmlns\="urn:ietf:params:xml:ns:netconf:base:1.0"\>

  \<kill-session\>

    \<session-id\>

      Specified session-ID

    \</session-id\>

  \</kill-session\>

\</rpc\>

If the \<kill-session\> operation
succeeds, the device returns a response in the following format:

\<?xml version\="1.0"
encoding\="UTF-8"?\>

    \<rpc-reply
message-id\="100"
xmlns\="urn:ietf:params:xml:ns:netconf:base:1.0"\>

      \<ok/\>

\</rpc-reply\>

### Example: Terminating another NETCONF session

#### Network configuration

The user whose session's ID is 1 terminates
the session with session ID 2\.

#### Procedure

\# Enter XML view.

\<Sysname\> xml

\# Notify the device of the NETCONF
capabilities supported on the client.

\<hello
xmlns\="urn:ietf:params:xml:ns:netconf:base:1.0"\>

  \<capabilities\>

    \<capability\>

            urn:ietf:params:netconf:base:1.0

    \</capability\>

  \</capabilities\>

\</hello\>

\# Terminate the session with session ID 2\.

\<rpc
message-id\="100"xmlns\="urn:ietf:params:xml:ns:netconf:base:1.0"\>

  \<kill-session\>

    \<session-id\>2\</session-id\>

  \</kill-session\>

\</rpc\>

#### Verifying the configuration

If the client receives the following text,
the NETCONF session with session ID 2 has been terminated, and the client with
session ID 2 has returned from XML view to user view:

\<?xml version\="1.0"
encoding\="UTF-8"?\>

  \<rpc-reply message-id\="100"
xmlns\="urn:ietf:params:xml:ns:netconf:base:1.0"\>

    \<ok/\>

\</rpc-reply\>

## Returning to the CLI

#### Restrictions and guidelines

Before returning from XML view to the CLI,
you must first complete capability exchange between the device and the client.

#### Procedure

\# Copy the following text to the client to
return from XML view to the CLI:

\<rpc message-id\="100"
xmlns\="urn:ietf:params:xml:ns:netconf:base:1.0"\>

   \<close-session/\>

\</rpc\>

When the device receives the close-session
request, it sends the following response and returns to CLI's user view:

\<?xml version\="1.0"
encoding\="UTF-8"?\>

\<rpc-reply
message-id\="100"
xmlns\="urn:ietf:params:xml:ns:netconf:base:1.0"\>

   \<ok/\>

\</rpc-reply\>

 

## Display andmaintenance commands for NETCONF

Execute display
commands in any view and reset commands in user
view.

 

| Task | Command || Display current NETCONF service status and global NETCONF service statistics. | display netconf service || Display NETCONF session status and statistics. | display netconf session || Clear NETCONF service statistics. | reset netconf service statistics || Clear NETCONF session statistics. | reset netconf session statistics |




| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |

‌

