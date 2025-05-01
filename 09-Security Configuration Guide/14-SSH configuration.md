
# Configuring SSH

## About SSH

Secure Shell
(SSH) is a network security protocol. Using encryption and authentication, SSH
can implement secure remote access and file transfer over an insecure network.

SSH uses the typical client-server model to
establish a channel for secure data transfer based on TCP.

SSH includes two versions: SSH1.x and SSH2.0
(hereinafter referred to as SSH1 and SSH2), which are not compatible. SSH2 is
better than SSH1 in performance and security.

### SSH applications

The device supports the following SSH
applications:

·     Secure Telnet—Stelnet provides secure and reliable network terminal access
services. Through Stelnet, a user can securely log in to a remote server.
Stelnet can protect devices against attacks, such as IP spoofing and plain text
password interception. The device can act as an Stelnet server or an Stelnet
client.

·     Secure File Transfer
Protocol—Based on SSH2, SFTP uses SSH connections
to provide secure file transfer. The device can act as an SFTP server, allowing
a remote user to log in to the SFTP server for secure file management and transfer.
The device can also act as an SFTP client, enabling a user to log in from the
device to a remote device for secure file transfer.

·     Secure Copy—Based on SSH2, SCP offers a secure method to copy files. The device
can act as an SCP server, allowing a user to log in to the device for file
upload and download. The device can also act as an SCP client, enabling a user
to log in from the device to a remote device for secure file transfer.

·     NETCONF over SSH—Based on SSH2, it enables users to securely log in to the device
through SSH and perform NETCONF operations on the device through the
NETCONF-over-SSH connections. The device can act only as a NETCONF-over-SSH server.
For more information about NETCONF, see Network
Management and Monitoring Configuration Guide.

When acting as an SSH server or client, the
device supports the following SSH versions:

·     When acting as an Stelnet, SFTP, or SCP server,
the device supports both SSH2 and SSH1 in non-FIPS mode and only SSH2 in FIPS
mode. 

·     When acting as an SSH client, the device
supports only SSH2.

·     When acting as a NETCONF-over-SSH server, the device
supports only SSH2.

### How SSH works

This section uses SSH2 as an example to describe
the stages to establish an SSH session.

Table 1 Stages to establish an SSH session

| Stages | Description |
| --- | --- |
| Connection establishment | The SSH server listens to connection requests on port 22\. After a client initiates a connection request, the server and the client establish a TCP connection. || Version negotiation | The two parties determine a version to use. || Algorithm negotiation | SSH supports multiple algorithms. Based on the local algorithms, the two parties negotiate the following algorithms:  ·     Key exchange algorithm for generating session keys.  ·     Encryption algorithm for encrypting data.  ·     Public key algorithm for the digital signature and authentication.  ·     HMAC algorithm for protecting data integrity. || Key exchange | The two parties use the DH exchange algorithm to dynamically generate the session keys and session ID.  ·     The session keys are used for protecting data transfer.  ·     The session ID is used for identifying the SSH connection.  In this stage, the client also authenticates the server. || Authentication | The SSH server authenticates the client in response to the client's authentication request. || Session request | After passing the authentication, the client sends a session request to the server to request the establishment of a session (or request the Stelnet, SFTP, SCP, or NETCONF service). || Interaction | After the server grants the request, the client and the server start to communicate with each other in the session.  In this stage, you can paste commands in text format and execute them at the CLI. The text pasted at one time must be no more than 2000 bytes. As a best practice to ensure the correct execution of commands, paste commands that are in the same view.  To execute commands of more than 2000 bytes, save the commands in a configuration file, upload the file to the server through SFTP, and use it to restart the server. |







‌

### SSHauthentication methods

This section describes authentication
methods that are supported by the device when it acts as an SSH server.

#### Password authentication

The SSH server authenticates a client
through the AAA mechanism. The password authentication process is as follows:

**1\.**The client sends the server an
authentication request that includes the encrypted username and password. 

**2\.**The server performs the following operations:

**a.**Decrypts the request to get the username and
password in plain text.

**b.**Verifies the username and password locally
or through remote AAA authentication.

**c.**Informs the client of the authentication
result.

If the remote AAA server requires the user to
enter a password for secondary authentication, it send the SSH server an
authentication response carrying a prompt. The prompt is transparently
transmitted to the client to notify the user to enter a specific password. When
the user enters the correct password, the AAA sever examines the password validity.
If the password is valid, the SSH server returns an authentication success message
to the client.

SSH1 clients do not support secondary
password authentication initiated by the AAA server.

For more information about AAA, see "Configuring
AAA."

#### Keyboard-interactive authentication

In keyboard-interactive authentication, the
remote authentication server and user exchanges information for authentication
as follows:

**1\.**The remote authentication server sends a
prompt to the SSH server in an authentication response.

The prompt indicates
the information required to be provided by the user.

**2\.**The SSH server transparently transmits the
prompt to the client terminal.

**3\.**The user enters the required information as
prompted.

This process repeats multiple times if the
remote authentication server requires more interactive information. The remote
authentication server returns an authentication success message after the user
provides all required interactive information.

If the remote authentication server does
not require interactive information, the keyboard-interactive authentication
process is the same as the password authentication.

#### Publickey authentication

The server authenticates a client by
verifying the digital signature of the client. The publickey authentication
process is as follows:

**1\.**The client sends the server a publickey
authentication request that includes the username, public key, and public key
algorithm name.

If the digital certificate of the client
is required in authentication, the client also encapsulates the digital
certificate in the authentication request. The digital certificate carries the
public key information of the client.

**2\.**The server verifies the client's public key.

¡     If
the public key is invalid, the server informs the client of the authentication
failure.

¡     If
the public key is valid, the server requests the digital signature of the
client. After receiving the signature, the server uses the public key to verify
the signature and informs the client of the authentication result.

When acting as an SSH server, the device supports
using the public key algorithms DSA, ECDSA, and RSA to verify digital signatures.

When acting as an SSH client, the device supports
using the public key algorithms DSA, ECDSA, and RSA to generate digital
signatures.

For more information about public key configuration,
see "Managing public keys."

#### Password-publickey authentication

The server requires SSH2 clients to pass
both password authentication and publickey authentication. However, an SSH1 client
only needs to pass either authentication.

#### Any authentication

The server requires clients to pass keyboard-interactive
authentication, password authentication, or publickey authentication. Success
with any one authentication method is sufficient to connect to the server.

### SSH support for Suite B

Suite B contains a set of encryption and
authentication algorithms that meet high security requirements. [Table 2](#_Ref430786794) lists
all algorithms in Suite B.

The SSH server and client support using the
X.509v3 certificate for identity authentication in compliance with the algorithm,
negotiation, and authentication specifications defined in RFC 6239\. 

Table 2 Suite B algorithms

| Security level | Key exchange algorithm | Encryption algorithm and HMAC algorithm | Public key algorithm |
| --- | --- | --- | --- |
| 128-bit | ecdh-sha2-nistp256 | AES128-GCM | x509v3-ecdsa-sha2-nistp256 || 192-bit | ecdh-sha2-nistp384 | AES256-GCM | x509v3-ecdsa-sha2-nistp384 || Both | ecdh-sha2-nistp256  ecdh-sha2-nistp384 | AES128-GCM  AES256-GCM | x509v3-ecdsa-sha2-nistp256  x509v3-ecdsa-sha2-nistp384 |



‌

## FIPS compliance

The device supports the FIPS mode that
complies with NIST FIPS 140-2 requirements. Support for features, commands, and
parameters might differ in FIPS mode and non-FIPS mode. For more information
about FIPS mode, see "Configuring FIPS."

## Configuring the device as an SSH server

### SSH server tasks at a glance

To configure an SSH server, perform the
following tasks:

**1\.**[Generating local
key pairs](#_Ref308597666)

**2\.**(Optional.) [Specifying the SSH service port](#_Ref467229655)

**3\.**Enabling the SSH server

¡     [Enabling the Stelnet server](#_Ref274902022)

¡     [Enabling the SFTP server](#_Ref296603801)

¡     [Enabling the SCP server](#_Ref389205122)

¡     [Enabling NETCONF over SSH](#_Ref387687523)

**4\.**[Configuring the
user lines for SSH login](#_Ref389642140)

Required only for Stelnet and
NETCONF-over-SSH servers.

**5\.**[Configuring a client's
host public key](#_Ref157424810)

Required for authentication method publickey, password-publickey, or any.

**6\.**[Configuring an SSH
user](#_Ref157510698)

¡     Required
for authentication method keyboard-interactive, publickey, password-publickey,
or any.

¡     Optional
for the password authentication method.

**7\.**(Optional.) [Configuring the SSH management
parameters](#_Ref157510703)

SSH management settings, such as
authentication and connection control settings, help improve security of SSH
connections.

**8\.**(Optional.) [Specifying a PKI domain for the SSH
server](#_Ref430597147)

**9\.**(Optional.) [Disconnecting SSH sessions](#_Ref480289552)

### Generating local key pairs

#### About this task

The DSA, ECDSA, or RSA key pairs on the SSH
server are required for generating the session keys and session ID in the key
exchange stage. They can also be used by a client to authenticate the server.
When a client authenticates the server, it compares the public key received
from the server with the server's public key that the client saved locally. If
the keys are consistent, the client uses the locally saved server's public key
to decrypt the digital signature received from the server. If the decryption
succeeds, the server passes the authentication.

To support SSH clients that use different
types of key pairs, generate DSA, ECDSA, and RSA key pairs on the SSH server.

·     RSA key pairs—The SSH server generates a server key pair and a host key pair for
RSA. The RSA server key pair is only used in SSH1 to encrypt the session key
for secure transmission of the session key. It is not used in SSH2, because no session
key transmission is required in SSH2.

·     DSA key pair—The SSH server generates only one DSA host key pair. SSH1 does not
support the DSA algorithm.

·     ECDSA key pair—The SSH server generates only one ECDSA host key pair.

#### Restrictions and guidelines

Local DSA, ECDSA, and RSA key pairs for SSH
use default names. You cannot assign names to the key pairs.

If the device does not have RSA key pairs
with default names, it automatically generates one RSA server key pair and one
RSA host key pair when SSH starts. Both key pairs use their default names. The SSH
application starts when you execute an SSH server command on the device.

The key modulus length must be less than
2048 bits when you generate the DSA key pair on the SSH server.

When you generate an ECDSA key pair, you
can generate only a secp256r1 or secp384r1 ECDSA key pair.

The SSH server operating in FIPS mode
supports only ECDSA and RSA key pairs. Do not generate a DSA key pair on the
SSH server in FIPS mode.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Generate local key pairs.

public-key local create { dsa \| ecdsa { secp256r1 \| secp384r1 } \| rsa }

### Specifying the SSH service port

#### About this task

The default port of the SSH service is 22\.
You can specify another port for the SSH service to improve security of SSH
connections.

#### Restrictions and guidelines

If you modify the SSH port number when the
SSH server is enabled, the SSH service is restarted and all SSH connections are
terminated after the modification. SSH users must reconnect to the SSH server
to access the server.

If you set the SSH port to a well-known
port number, the service that uses the well-known port number might fail to
start. Well-known port numbers are in the range of 1 to 1024\.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Specify the SSH service port.

ssh server port port-number

By default, the SSH service port is 22\.

### Enabling the Stelnet server

#### About this task

After you enable the Stelnet server on the
device, a client can log in to the device through Stelnet.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enable the Stelnet server.

ssh server enable

By default, the Stelnet server is disabled.

### Enabling the SFTP server

#### About this task

After you enable the SFTP server on the
device, a client can log in to the device through SFTP.

#### Restrictions and guidelines

When acting as an SFTP server, the device does
not support SFTP connections initiated by SSH1 clients.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enable the SFTP server.

sftp server enable

By default, the SFTP server is disabled.

### Enabling the SCP server

#### About this task

After you enable the SCP server on the
device, a client can log in to the device through SCP.

#### Restrictions and guidelines

When acting as an SCP server, the device
does not support SCP connections initiated by SSH1 clients.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enable the SCP server.

scp server enable

By default, the SCP server is disabled.

### Enabling NETCONF over SSH

#### About this task

After you enable NETCONF over SSH on the
device, a client can perform NETCONF operations on the device through a
NETCONF-over-SSH connection.

#### Restrictions and guidelines

When acting as a server in the
NETCONF-over-SSH connection, the device does not support connection requests
initiated by SSH1 clients.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enable NETCONF over SSH.

netconf ssh server enable

By default, NETCONF over SSH is disabled.

For more information about NETCONF over
SSH commands, see Network Management and Monitoring
Command Reference.

### Configuring the user lines for SSH login

#### About this task

Depending on the SSH application, an SSH
client can be an Stelnet client, SFTP client, SCP client, or NETCONF-over-SSH
client.

Only Stelnet and NETCONF-over-SSH clients
require the user line configuration. The user line configuration takes effect on
the clients at the next login.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter VTY user line view.

line vty number \[ ending-number ]

**3\.**Set the login authentication mode to scheme.

authentication-mode scheme

By default, the authentication mode is password.

For more information about this command, see
Fundamentals Command Reference.

### Configuring a client's host public key

#### About this task

In publickey authentication, the server compares
the SSH username and the client's host public key received from the client with
the locally saved SSH username and the client's host public key. If they are the
same, the server checks the digital signature that the client sends. The client
generates the digital signature by using the private key that is paired with
the client's host public key.

For publickey authentication, password-publickey
authentication, or any authentication, you must perform the following tasks:

**1\.**Configure the client's DSA, ECDSA, or RSA
host public key on the server.

**2\.**Specify the associated host private key on
the client to generate the digital signature.

If the device acts as an SSH client,
specify the public key algorithm on the client. The algorithm determines the
associated host private key for generating the digital signature.

#### Client public key configuration methods

You can configure the client host public
key by using the following methods:

·     Manually enter the content of a client's host
public key on the server.

**a.**Display the host public key on the client
and record the key.

**b.**Type the client's host public key character
by character on the server, or use the copy and paste method.

The manually entered key must be in DER
format without being converted. For the displayed key to meet the requirement
when the client is an H3C device, use the display public-key local public command. The format of the public key displayed in any other way (for
example, by using the public-key local export
command) might be incorrect. If the key is not in correct format, the system discards
the key.

·     Import the client host public key from a public
key file.

**a.**Save the client public key file to the
server. For example, transfer the client public key file to the server in
binary mode through FTP or TFTP.

**b.**Import the client public key from the
locally saved public key file.

During the import process, the server automatically
converts the host public key to a string in PKCS format.

#### Restrictions and guidelines

As a best practice, configure no more than
20 SSH client's host public keys on an SSH server.

Import the client's host public key as a
best practice.

#### Entering a client's host public key

**1\.**Enter system view.

system-view

**2\.**Enter public key view.

public-key peer keyname

**3\.**Configure a client's host public key.

Enter the content of the client's host public
key character by character, or use the copy and paste method.

When you enter the content of a client's
host public key, you can use spaces and carriage returns between characters but
the system does not save them. For more information, see "Managing public keys."

**4\.**Exit public key view and save the key.

peer-public-key end

#### Importing a client's host public key from the public key file

**1\.**Enter system view.

system-view

**2\.**Import a client's public key from the public
key file.

public-key peer keyname import sshkey filename

### Configuring an SSH user

#### About this task

Configure an SSH user and a local user
depending on the authentication method.

·     If the authentication method is publickey, you must create an SSH user and a local user
on the SSH server. The two users must have the same username, so that the SSH
user can be assigned the correct working directory and user role.

·     If the authentication method is password, you must perform one of the following tasks:

¡     For
local authentication, configure a local user on the SSH server.

¡     For
remote authentication, configure an SSH user on a remote authentication server,
for example, a RADIUS server.

You do not need to create an SSH user by
using the ssh user command. However, if you want
to display all SSH users, including the password-only SSH users, for
centralized management, you can use this command to create them. If such an SSH
user has been created, make sure you have specified the correct service type
and authentication method.

·     If the authentication method is keyboard-interactive, password-publickey,
or any, you must create an SSH user on the SSH
server and perform one of the following tasks:

¡     For
local authentication, configure a local user on the SSH server.

¡     For
remote authentication, configure an SSH user on a remote authentication server,
for example, a RADIUS server.

In either case, the local user or the SSH
user configured on the remote authentication server must have the same username
as the SSH user.

For information about configuring local
users and remote authentication, see "Configuring AAA."

#### Restrictions and guidelines

If you change the authentication parameters
for a logged-in SSH user, the change takes effect on the user at the next
login.

When the device operates as an SSH server in
FIPS mode, the device does not support authentication method any or publickey.

For an SFTP or SCP user, the working directory
depends on the authentication method.

·     If the authentication method is publickey or password-publickey,
the working folder is specified by the authorization-attribute command in the associated local user view.

·     If the authentication method is keyboard-interactive or password,
the working directory is authorized by AAA.

For an SSH user, the user role also depends
on the authentication method.

·     If the authentication method is publickey or password-publickey,
the user role is specified by the authorization-attribute command in the associated local user view.

·     If the authentication method is keyboard-interactive or password,
the user role is authorized by AAA.

For all authentication methods except keyboard-interactive
authentication and password authentication, you must specify a client's host
public key or digital certificate.

·     For a client that sends the user's public key information
directly to the server, specify the client's host public key on the server. The
specified public key must already exist. For more information about public
keys, see "[Configuring a client's
host public key](#_Ref302671354)." If you specify multiple
client public keys, the device verifies the user identity by using the public
keys in the order they are specified. The user is valid if the user passes one
public key check.

·     For a client that sends the user's public key
information to the server through a digital certificate, specify the PKI domain
on the server. This PKI domain verifies the client's digital certificate. For
successful verification, the specified PKI domain must have the correct CA
certificate. To specify the PKI domain, use the ssh user or ssh server pki-domain
command. For more information about configuring a PKI domain, see "Configuring
PKI."

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Create an SSH user, and specify the service
type and authentication method.

In non-FIPS mode:

ssh user username service-type { all \| netconf \| scp \| sftp \| stelnet } authentication-type { keyboard-interactive \| password \| { any \| password-publickey \| publickey } \[ assign { pki-domain domain-name \| publickey keyname\&\<1-6\> } ] }

In FIPS mode:

ssh user username service-type { all \| netconf \| scp \| sftp \| stelnet } authentication-type { keyboard-interactive \| password \| password-publickey \[ assign { pki-domain domain-name \| publickey keyname\&\<1-6\> } ] }

An SSH server supports up to 1024 SSH
users.

### Configuring the SSH management parameters

#### Enabling the SSH server to support SSH1 clients

**1\.**Enter system view.

system-view

**2\.**Enable the SSH server to support SSH1
clients.

ssh server compatible-ssh1x enable

By default, the SSH server does not
support SSH1 clients.

This command is not available in FIPS
mode.

#### Enabling SSH algorithm renegotiation and key re-exchange

**1\.**Enter system view.

system-view

**2\.**Enable SSH algorithm renegotiation and key
re-exchange.

ssh server key-re-exchange enable \[ interval interval ]

By default, SSH algorithm renegotiation
and key re-exchange are disabled.

This command is not available in FIPS
mode. 

The command takes effect only on new SSH
connections that are established after the command is configured, and it does
not affect existing SSH connections.

#### Setting the minimum interval for updating the RSA server key pair

**1\.**Enter system view.

system-view

**2\.**Set the minimum interval for updating the
RSA server key pair.

ssh server rekey-interval interval

By default, the device does not update
the RSA server key pair.

This command is not available in FIPS
mode.

This configuration takes effect only on
SSH1 clients.

#### Setting the SSH user authentication timeout timer

**1\.**Enter system view.

system-view

**2\.**Set the SSH user authentication timeout
timer.

ssh server authentication-timeout time-out-value

The default setting is 60 seconds.

Perform this task to prevent malicious
occupation of TCP connections. If a user does not finish the authentication
when the timeout timer expires, the connection cannot be established.

#### Setting the maximum number of SSH authentication attempts

**1\.**Enter system view.

system-view

**2\.**Set the maximum number of SSH authentication
attempts.

ssh server authentication-retries retries

The default setting is 3\.

Perform this task to prevent malicious
hacking of usernames and passwords. If the authentication method is any, the total number of publickey authentication attempts and password
authentication attempts cannot exceed the upper limit.

#### Specifying an SSH login control ACL

**1\.**Enter system view.

system-view

**2\.**Specify an SSH login control ACL.

IPv4:

ssh server acl { advanced-acl-number \| basic-acl-number
\| mac
mac-acl-number }

IPv6:

ssh server ipv6 acl { ipv6 { advanced-acl-number \| basic-acl-number } \| mac
mac-acl-number }

This feature uses an ACL to filter SSH
clients that initiate SSH connections to the server. By default, no ACLs are
specified and all SSH users can initiate SSH connections to the server.

Only SSH users that match the ACL's
permit rules can initiate SSH connections to the server. If the specified ACL
does not exist or the ACL contains no rules, no SSH users can initiate SSH
connections to the server.

This configuration takes effect only on
new SSH connections. It does not affect the existing SSH connections.

#### Enabling logging for SSH login attempts that are denied by the SSH login control ACL

**1\.**Enter system view.

system-view

**2\.**Enable logging for SSH login attempts that
are denied by the SSH login control ACL.

ssh server acl-deny-log enable

By default, logging is disabled for login
attempts that are denied by the SSH login control ACL.

This command enables SSH to generate log
messages for SSH login attempts that are denied by the SSH login control ACL
and send the messages to the information center.

#### Setting the DSCP value in the packets that the SSH server sends to SSH clients

**1\.**Enter system view.

system-view

**2\.**Set the DSCP value in the packets that the
SSH server sends to the SSH clients.

IPv4:

ssh server dscp dscp-value

IPv6:

ssh server ipv6 dscp dscp-value

By default, the DSCP value of SSH packets
is 48\.

The DSCP value of a packet defines the
priority of the packet and affects the transmission priority of the packet. A
bigger DSCP value represents a higher priority.

#### Setting the SFTP connection idle timeout timer

**1\.**Enter system view.

system-view

**2\.**Set the SFTP connection idle timeout timer.

sftp server idle-timeout time-out-value

By default, the SFTP connection idle
timeout is 10 minutes.

When the SFTP connection idle timeout timer
expires, the system automatically tears the connection down and releases the connection
resources.

#### Setting the maximum number of online SSH users

**1\.**Enter system view.

system-view

**2\.**Set the maximum number of online SSH users.

aaa session-limit ssh max-sessions

The default setting is 32\.

When the number of online SSH users
reaches the upper limit, the system denies new SSH connection requests. Changing
the upper limit does not affect online SSH users.

For more information about this command,
see AAA commands in Security Command Reference.

#### Setting the maximum number of records for SSH user login exceptions

**1\.**Enter system view.

system-view

2\.     Set the maximum number of records for SSH
user login exceptions.

ssh exception-record max-number max-number

By default, the maximum number of records
for SSH user login exceptions is 10\.

To view more records for SSH user login
exceptions, you can use this command to set the maximum number of records.

### Specifying a PKI domain for the SSH server

#### About this task

The PKI domain specified for the SSH server
has the following functions:

·     The SSH server uses the PKI domain to send its
certificate to the client in the key exchange stage. 

·     The SSH server uses the PKI domain to
authenticate the client's certificate if no PKI domain is specified for the
client authentication by using the ssh user
command.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Specify a PKI domain for the SSH server.

ssh server pki-domain domain-name

By default, no PKI domain is specified
for the SSH server.

### Disconnecting SSH sessions

#### About this task

The device supports concurrent login
sessions. To avoid an SSH login user interfering with your configuration, you
can disconnect that SSH login user.

#### Procedure

Execute the following command in user view
to disconnect SSH sessions:

free ssh { user-ip { ip-address \| ipv6 ipv6-address } \[ port port-number ] \| user-pid pid-number \| username username }

## Configuring the device as an Stelnet client

### Stelnet client tasks at a glance

To configure an Stelnet client, perform the
following tasks:

**1\.**[Generating local
key pairs](#_Ref406665847)

Only required for authentication method publickey, password-publickey, or any.

**2\.**(Optional.) [Specifying the source IP address for outgoing
SSH packets](#_Ref406750792)

**3\.**[Establishing a
connection to an Stelnet server](#_Ref308598305)

**4\.**(Optional.) [Deleting server public keys saved in the
public key file on the Stelnet client](#_Ref467606671)

**5\.**(Optional.) [Establishing a connection to an Stelnet
server based on Suite B](#_Ref430597342)

### Generating local key pairs

#### About this task

You must generate local key pairs on
Stelnet clients when the Stelnet server uses the publickey,
password-publickey, or any
authentication method.

#### Restrictions and guidelines

Local DSA, ECDSA, and RSA key pairs for SSH
use default names. You cannot assign names to the key pairs.

The key modulus length must be less than
2048 bits when you generate a DSA key pair.

When you generate an ECDSA key pair, you
can generate only a secp256r1 or secp384r1 ECDSA key pair.

The Stelnet client operating in FIPS mode
supports only ECDSA and RSA key pairs.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Generate local key pairs.

public-key local create { dsa \| ecdsa { secp256r1 \| secp384r1 } \| rsa }

### Specifying the source IP address for outgoing SSH packets

#### About this task

After you specify the source IP address for
outgoing SSH packets on an Stelnet client, the client uses the specified IP
address to communicate with the Stelnet server.

#### Restrictions and guidelines

As a best practice, specify the IP address
of a loopback interface as the source address of outgoing SSH packets for the
following purposes:

·     Ensuring the communication between the Stelnet
client and the Stelnet server.

·     Improving the manageability of Stelnet clients
in authentication service.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Specify the source address for outgoing SSH
packets.

IPv4:

ssh client source { interface interface-type
interface-number \| ip ip-address }

By default, an IPv4 Stelnet client uses
the primary IPv4 address of the output interface in the matching route as the
source address of the outgoing SSH packets.

IPv6:

ssh client ipv6 source { interface interface-type interface-number \| ipv6 ipv6-address }

By default, an IPv6 Stelnet client
automatically selects a source IPv6 address for outgoing SSH packets in
compliance with RFC 3484\.

### Establishing a connection to an Stelnet server

#### About this task

Perform this task to enable the Stelnet
client feature on the device and establish a connection to the Stelnet server. You
can specify the public key algorithm and the preferred encryption, HMAC, and
key exchange algorithms to be used during the connection.

To access the server, a client must use the
server's host public key to authenticate the server. As a best practice,
configure the server's host public key on the device in an insecure network. If
the server's host public key is not configured on the client, the client will
notify you to confirm whether to continue with the access.

·     If you choose to continue, the client accesses
the server and downloads the server's host public key. The downloaded public
key will be used to authenticate the server in subsequent accesses.

If the server public key is not specified
the when you connect to the server, the device saves the server public key to
the public key file. It does not save the server public key to the configuration
file.

·     If you choose to not continue, the connection
cannot be established.

#### Restrictions and guidelines for establishing a connection to an Stelnet server

An Stelnet client cannot establish
connections to both IPv4 and IPv6 Stelnet servers.

#### Establishing a connection to an IPv4 Stelnet server

Execute the following command in user view
to establish a connection with an IPv4 Stelnet server:

In non-FIPS mode:

ssh2 server \[ port-number ] \[ vpn-instance
vpn-instance-name ] \[ identity-key { dsa \| ecdsa-sha2-nistp256 \| ecdsa-sha2-nistp384 \| rsa \| { x509v3-ecdsa-sha2-nistp256 \| x509v3-ecdsa-sha2-nistp384 } pki-domain domain-name } \| prefer-compress zlib \| prefer-ctos-cipher { 3des-cbc \| aes128-cbc \| aes128-ctr \| aes128-gcm \| aes192-ctr \| aes256-cbc \| aes256-ctr \| aes256-gcm \| des-cbc \| sm4-cbc } \| prefer-ctos-hmac { md5 \| md5-96 \| sha1 \| sha1-96 \| sha2-256 \| sha2-512 \| sm3 } \| prefer-kex { dh-group-exchange-sha1 \| dh-group1-sha1
\|
dh-group14-sha1 \| ecdh-sha2-nistp256 \| ecdh-sha2-nistp384 } \| prefer-stoc-cipher { 3des-cbc \| aes128-cbc \| aes128-ctr \| aes128-gcm \| aes192-ctr \| aes256-cbc \| aes256-ctr \| aes256-gcm \| des-cbc \| sm4-cbc } \| prefer-stoc-hmac { md5 \| md5-96 \| sha1 \| sha1-96 \| sha2-256 \| sha2-512 \| sm3 } ] \* \[ dscp dscp-value \| escape character \| { public-key keyname \| server-pki-domain domain-name } \| source { interface interface-type interface-number \| ip ip-address } ] \*

In FIPS mode:

ssh2 server \[ port-number ] \[ vpn-instance
vpn-instance-name ] \[ identity-key { ecdsa-sha2-nistp256 \| ecdsa-sha2-nistp384 \| rsa \| { x509v3-ecdsa-sha2-nistp256 \| x509v3-ecdsa-sha2-nistp384 } pki-domain domain-name } \| prefer-compress zlib \| prefer-ctos-cipher { aes128-cbc \| aes128-ctr \| aes128-gcm \| aes192-ctr \| aes256-cbc \| aes256-ctr \| aes256-gcm } \| prefer-ctos-hmac { sha1 \| sha1-96 \| sha2-256 \| sha2-512 } \| prefer-kex { dh-group14-sha1 \| ecdh-sha2-nistp256 \| ecdh-sha2-nistp384 } \| prefer-stoc-cipher { aes128-cbc \| aes128-ctr \| aes128-gcm \| aes192-ctr \| aes256-cbc \| aes256-ctr \| aes256-gcm } \| prefer-stoc-hmac { sha1 \| sha1-96 \| sha2-256 \| sha2-512 } ] \* \[ escape character \| { public-key keyname \| server-pki-domain domain-name } \| source { interface interface-type interface-number \| ip ip-address } ] \*

#### Establishing a connection to an IPv6 Stelnet server

Execute the following command in user view
to establish a connection to an IPv6 Stelnet server:

In non-FIPS mode:

ssh2 ipv6 server \[ port-number ] \[ vpn-instance
vpn-instance-name ] \[ -i interface-type interface-number ] \[ identity-key
{ dsa \| ecdsa-sha2-nistp256 \| ecdsa-sha2-nistp384 \| rsa \| { x509v3-ecdsa-sha2-nistp256 \| x509v3-ecdsa-sha2-nistp384 } pki-domain domain-name } \| prefer-compress zlib \| prefer-ctos-cipher { 3des-cbc \| aes128-cbc \| aes128-ctr \| aes128-gcm \| aes192-ctr \| aes256-cbc \| aes256-ctr \| aes256-gcm \| des-cbc \| sm4-cbc } \| prefer-ctos-hmac { md5 \| md5-96 \| sha1 \| sha1-96 \| sha2-256 \| sha2-512 \| sm3 } \| prefer-kex { dh-group-exchange-sha1 \| dh-group1-sha1
\|
dh-group14-sha1 \| ecdh-sha2-nistp256 \| ecdh-sha2-nistp384 } \| prefer-stoc-cipher { 3des-cbc \| aes128-cbc \| aes128-ctr \| aes128-gcm \| aes192-ctr \| aes256-cbc \| aes256-ctr \| aes256-gcm \| des-cbc \| sm4-cbc } \| prefer-stoc-hmac { md5 \| md5-96 \| sha1 \| sha1-96 \| sha2-256 \| sha2-512 \| sm3 } ] \* \[ dscp dscp-value \| escape character \| { public-key keyname \| server-pki-domain domain-name } \| source { interface interface-type interface-number \| ipv6 ipv6-address } ] \*

In FIPS mode:

ssh2 ipv6 server \[ port-number ] \[ vpn-instance
vpn-instance-name ] \[ -i interface-type interface-number ] \[ identity-key
{ ecdsa-sha2-nistp256 \| ecdsa-sha2-nistp384 \| rsa \| { x509v3-ecdsa-sha2-nistp256 \| x509v3-ecdsa-sha2-nistp384 } pki-domain domain-name } \| prefer-compress zlib \| prefer-ctos-cipher { aes128-cbc \| aes128-ctr \| aes128-gcm \| aes192-ctr \| aes256-cbc \| aes256-ctr \| aes256-gcm } \| prefer-ctos-hmac { sha1 \| sha1-96 \| sha2-256 \| sha2-512 } \| prefer-kex { dh-group14-sha1 \| ecdh-sha2-nistp256 \| ecdh-sha2-nistp384 } \| prefer-stoc-cipher { aes128-cbc \| aes128-ctr \| aes128-gcm \| aes192-ctr \| aes256-cbc \| aes256-ctr \| aes256-gcm } \| prefer-stoc-hmac { sha1 \| sha1-96 \| sha2-256 \| sha2-512 } ] \* \[ escape character \| { public-key keyname \| server-pki-domain domain-name } \| source { interface interface-type interface-number \| ipv6 ipv6-address } ] \*

### Deleting server public keys saved in the public key file on the Stelnet client

#### About this task

When the Stelnet client switches to FIPS
mode but the locally saved server public key does not comply with FIPS, the
client cannot connect to the server. To connect to the server, delete the
server public key saved on the client and make sure a FIPS-compliant public key
has been generated on the server.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Delete server public keys saved in the
public key file on the Stelnet client.

delete ssh client server-public-key \[ server-ip ip-address ]

### Establishing a connection to an Stelnet server based on Suite B

Execute the following command in user view
to establish a connection to an Stelnet server based on Suite B:

IPv4:

ssh2 server \[ port-number ] \[ vpn-instance
vpn-instance-name ] suite-b \[ 128-bit \| 192-bit ] pki-domain domain-name \[ server-pki-domain domain-name ] \[ prefer-compress zlib ] \[ dscp dscp-value \| escape character \| source { interface interface-type interface-number \| ip ip-address } ] \*

IPv6:

ssh2 ipv6 server \[ port-number ] \[ vpn-instance
vpn-instance-name ] \[ -i interface-type interface-number ] suite-b \[ 128-bit \| 192-bit ] pki-domain domain-name \[ server-pki-domain domain-name ] \[ prefer-compress zlib ] \[ dscp dscp-value \| escape character \| source { interface interface-type interface-number \| ipv6 ipv6-address } ] \*

## Configuring the device as an SFTP client

### SFTP client tasksat a glance

To configure an SFTP client, perform the
following tasks:

**1\.**[Generating local
key pairs](#_Ref406665664)

Only required for authentication method publickey, password-publickey, or any.

**2\.**(Optional.) [Specifying the source IP address for
outgoing SFTP packets](#_Ref406750835)

**3\.**[Establishing a
connection to an SFTP server](#_Ref315973770)

**4\.**(Optional.) [Deleting server public keys saved in the
public key file on the SFTP client](#_Ref480289676) 

**5\.**(Optional.) [Establishing a connection to an SFTP server
based on Suite B](#_Ref480289681) 

**6\.**(Optional.) [Working with SFTP directories](#_Ref371610080)

**7\.**(Optional.) [Working with SFTP files](#_Ref296435816)

**8\.**(Optional.) [Displaying help information](#_Ref296435817)

**9\.**(Optional.) [Terminating the connection with the SFTP
server](#_Ref296435820)

### Generating local key pairs

#### About this task

You must generate local key pairs on SFTP
clients when the SFTP server uses the publickey, password-publickey, or any authentication
method.

#### Restrictions and guidelines

Local DSA, ECDSA, and RSA key pairs for SSH
use default names. You cannot assign names to the key pairs.

The key modulus length must be less than
2048 bits when you generate a DSA key pair.

When you generate an ECDSA key pair, you
can generate only a secp256r1 or secp384r1 ECDSA key pair.

The SFTP client operating in FIPS mode
supports only ECDSA and RSA key pairs.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Generate local key pairs.

public-key local create { dsa \| ecdsa { secp256r1 \| secp384r1 } \| rsa }

### Specifying the source IP address for outgoing SFTP packets

#### About this task

After you specify the source IP address for
outgoing SFTP packets on an SFTP client, the client uses the specified IP
address to communicate with the SFTP server.

#### Restrictions and guidelines

As a best practice, specify the IP address
of a loopback interface as the source address of outgoing SFTP packets for the
following purposes:

·     Ensuring the communication between the SFTP client
and the SFTP server.

·     Improving the manageability of SFTP clients in authentication
service.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Specify the source address for outgoing SFTP
packets.

IPv4:

sftp client source { ip ip-address \| interface interface-type interface-number }

By default, an SFTP client uses the
primary IPv4 address of the output interface in the matching route as the
source address of the outgoing SFTP packets.

IPv6:

sftp client ipv6 source
{ ipv6 ipv6-address \| interface interface-type
interface-number }

By default, an IPv6 SFTP client automatically
selects a source IPv6 address for the outgoing SFTP packets in compliance with
RFC 3484\.

### Establishing a connection to an SFTP server

#### About this task

Perform this task to enable the SFTP client
feature on the device and establish a connection to the SFTP server. You can
specify the public key algorithm and the preferred encryption, HMAC, and key
exchange algorithms to be used during the connection.

To access the server, a client must use the
server's host public key to authenticate the server. As a best practice,
configure the server's host public key on the device in an insecure network. If
the server's host public key is not configured on the client, the client will
notify you to confirm whether to continue with the access.

·     If you choose to continue, the client accesses
the server and downloads the server's host public key. The downloaded public
key will be used to authenticate the server in subsequent accesses.

If the server public key is not specified
when you connect to the server, the device saves the server public key to the
public key file. It does not save the server public key to the configuration
file.

·     If you choose to not continue, the connection
cannot be established.

#### Restrictions and guidelines for establishing a connection to an SFTP server

An SFTP client cannot establish connections
to both IPv4 and IPv6 SFTP servers.

#### Establishing a connection to an IPv4 SFTP server

Execute the following command in user view
to establish a connection to an IPv4 SFTP server:

In non-FIPS mode:

sftp server \[ port-number ] \[ vpn-instance vpn-instance-name ] \[ identity-key { dsa \| ecdsa-sha2-nistp256 \| ecdsa-sha2-nistp384 \| rsa \| { x509v3-ecdsa-sha2-nistp256 \| x509v3-ecdsa-sha2-nistp384 } pki-domain domain-name } \| prefer-compress zlib \| prefer-ctos-cipher { 3des-cbc \| aes128-cbc \| aes128-ctr \| aes128-gcm \| aes192-ctr \| aes256-cbc \| aes256-ctr \| aes256-gcm \| des-cbc \| sm4-cbc } \| prefer-ctos-hmac { md5 \| md5-96 \| sha1 \| sha1-96 \| sha2-256 \| sha2-512 \| sm3 } \| prefer-kex { dh-group-exchange-sha1 \| dh-group1-sha1
\|
dh-group14-sha1 \| ecdh-sha2-nistp256 \| ecdh-sha2-nistp384 } \| prefer-stoc-cipher { 3des-cbc \| aes128-cbc \| aes128-ctr \| aes128-gcm \| aes192-ctr \| aes256-cbc \| aes256-ctr \| aes256-gcm \| des-cbc \| sm4-cbc } \| prefer-stoc-hmac { md5 \| md5-96 \| sha1 \| sha1-96 \| sha2-256 \| sha2-512 \| sm3 } ] \* \[ dscp dscp-value \| { public-key keyname \| server-pki-domain domain-name } \| source { interface interface-type interface-number \| ip ip-address } ] \*

In FIPS mode:

sftp server \[ port-number ] \[ vpn-instance vpn-instance-name ] \[ identity-key { ecdsa-sha2-nistp256 \| ecdsa-sha2-nistp384 \| rsa \| { x509v3-ecdsa-sha2-nistp256 \| x509v3-ecdsa-sha2-nistp384 } pki-domain domain-name } \| prefer-compress zlib \| prefer-ctos-cipher { aes128-cbc \| aes128-ctr \| aes128-gcm \| aes192-ctr \| aes256-cbc \| aes256-ctr \| aes256-gcm } \| prefer-ctos-hmac { sha1 \| sha1-96 \| sha2-256 \| sha2-512 } \| prefer-kex { dh-group14-sha1 \| ecdh-sha2-nistp256 \| ecdh-sha2-nistp384 } \| prefer-stoc-cipher { aes128-cbc \| aes128-ctr \| aes128-gcm \| aes192-ctr \| aes256-cbc \| aes256-ctr \| aes256-gcm } \| prefer-stoc-hmac { sha1 \| sha1-96 \| sha2-256 \| sha2-512 } ] \* \[ { public-key keyname \| server-pki-domain domain-name } \| source { interface interface-type interface-number \| ip ip-address } ] \*

#### Establishing a connection to an IPv6 SFTP server

Execute the following command in user view
to establish a connection to an IPv6 SFTP server:

In non-FIPS mode:

sftp ipv6 server \[ port-number ] \[ vpn-instance
vpn-instance-name ] \[ -i interface-type interface-number ] \[ identity-key
{ dsa \| ecdsa-sha2-nistp256 \| ecdsa-sha2-nistp384 \| rsa \| { x509v3-ecdsa-sha2-nistp256 \| x509v3-ecdsa-sha2-nistp384 } pki-domain domain-name } \| prefer-compress zlib \| prefer-ctos-cipher { 3des-cbc \| aes128-cbc \| aes128-ctr \| aes128-gcm \| aes192-ctr \| aes256-cbc \| aes256-ctr \| aes256-gcm \| des-cbc \| sm4-cbc } \| prefer-ctos-hmac { md5 \| md5-96 \| sha1 \| sha1-96 \| sha2-256 \| sha2-512 \| sm3 } \| prefer-kex { dh-group-exchange-sha1 \| dh-group1-sha1
\|
dh-group14-sha1 \| ecdh-sha2-nistp256 \| ecdh-sha2-nistp384 } \| prefer-stoc-cipher { 3des-cbc \| aes128-cbc \| aes128-ctr \| aes128-gcm \| aes192-ctr \| aes256-cbc \| aes256-ctr \| aes256-gcm \| des-cbc \| sm4-cbc } \| prefer-stoc-hmac { md5 \| md5-96 \| sha1 \| sha1-96 \| sha2-256 \| sha2-512 \| sm3 } ] \* \[ dscp dscp-value \| { public-key keyname \| server-pki-domain domain-name } \| source { interface interface-type interface-number \| ipv6 ipv6-address } ] \*

In FIPS mode:

sftp ipv6 server \[ port-number ] \[ vpn-instance
vpn-instance-name ] \[ -i interface-type interface-number ] \[ identity-key
{ ecdsa-sha2-nistp256 \| ecdsa-sha2-nistp384 \| rsa \| { x509v3-ecdsa-sha2-nistp256 \| x509v3-ecdsa-sha2-nistp384 } pki-domain domain-name } \| prefer-compress zlib \| prefer-ctos-cipher { aes128-cbc \| aes128-ctr \| aes128-gcm \| aes192-ctr \| aes256-cbc \| aes256-ctr \| aes256-gcm } \| prefer-ctos-hmac { sha1 \| sha1-96 \| sha2-256 \| sha2-512 } \| prefer-kex { dh-group14-sha1 \| ecdh-sha2-nistp256 \| ecdh-sha2-nistp384 } \| prefer-stoc-cipher { aes128-cbc \| aes128-ctr \| aes128-gcm \| aes192-ctr \| aes256-cbc \| aes256-ctr \| aes256-gcm } \| prefer-stoc-hmac { sha1 \| sha1-96 \| sha2-256 \| sha2-512 } ] \* \[ { public-key keyname \| server-pki-domain domain-name } \| source { interface interface-type interface-number \| ipv6 ipv6-address } ] \*

### Deleting server public keys saved in the public key file on the SFTP client

#### About this task

When the SFTP client switches to FIPS mode
but the locally saved server public key does not comply with FIPS, the client
cannot connect to the server. To connect to the server, delete the server
public key saved on the client and make sure a FIPS-compliant public key has
been generated on the server.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Delete server public keys saved in the
public key file on the SFTP client.

delete ssh client server-public-key \[ server-ip ip-address ]

### Establishing a connection to an SFTP server based on Suite B

Execute the following command in user view
to establish a connection to an SFTP server based on Suite B:

IPv4:

sftp server \[ port-number ] \[ vpn-instance
vpn-instance-name ] suite-b \[ 128-bit \| 192-bit ] pki-domain domain-name \[ server-pki-domain domain-name ] \[ prefer-compress zlib ] \[ dscp dscp-value \| source { interface interface-type interface-number \| ip ip-address } ] \*

IPv6:

sftp ipv6 server \[ port-number ] \[ vpn-instance
vpn-instance-name ] \[ -i interface-type interface-number ] suite-b \[ 128-bit \| 192-bit ] pki-domain domain-name \[ server-pki-domain domain-name ] \[ prefer-compress zlib ] \[ dscp dscp-value \| escape character \| source { interface interface-type interface-number \| ipv6 ipv6-address } ] \*

### Working with SFTP directories

#### About this task

After you establish a connection to an SFTP
server, you can operate directories of the SFTP server.

#### Changing the working directory on the SFTP server

**1\.**Enter SFTP client view.

For more information, see "[Establishing a connection to an SFTP server](#_Ref315973770)."

**2\.**Change the working directory on the SFTP
server.

cd \[ remote-path ]

**3\.**(Optional.) Return to the upper-level
directory.

cdup

#### Displaying the current working directory on the SFTP server

**1\.**Enter SFTP client view.

For more information, see "[Establishing a connection to an SFTP server](#_Ref315973770)."

**2\.**Display the current working directory on the
SFTP server.

pwd

#### Displaying files under a directory

**1\.**Enter SFTP client view.

For more information, see "[Establishing a connection to an SFTP server](#_Ref315973770)."

**2\.**Display files under a directory.

¡     dir \[ -a \| -l ] \[ remote-path ]

¡     ls \[ -a \| -l ] \[ remote-path ]

The dir command
has the same function as the ls command.

#### Changing the name of a directory on the SFTP server

**1\.**Enter SFTP client view.

For more information, see "[Establishing a connection to an SFTP server](#_Ref315973770)."

**2\.**Change the name of a directory on the SFTP
server.

rename oldname newname

#### Creating a new directory on the SFTP server

**1\.**Enter SFTP client view.

For more information, see "[Establishing a connection to an SFTP server](#_Ref315973770)."

**2\.**Create a new directory on the SFTP server.

mkdir remote-path

#### Deleting directories on the SFTP server

**1\.**Enter SFTP client view.

For more information, see "[Establishing a connection to an SFTP server](#_Ref315973770)."

**2\.**Delete one or more directories from the SFTP
server.

rmdir remote-path

### Working with SFTP files

#### About this task

After you establish a connection to an SFTP
server, you can operate files on the SFTP server.

#### Changing the name of a file on the SFTP server

**1\.**Enter SFTP client view.

For more information, see "[Establishing a connection to an SFTP server](#_Ref315973770)."

**2\.**Change the name of a file on the SFTP
server.

rename old-name
new-name

#### Downloading a file from the SFTP server and save it locally

**1\.**Enter SFTP client view.

For more information, see "[Establishing a connection to an SFTP server](#_Ref315973770)."

**2\.**Download a file from the SFTP server and
save it locally.

get remote-file
\[ local-file ]

#### Uploading a local file to the SFTP server

**1\.**Enter SFTP client view.

For more information, see "[Establishing a connection to an SFTP server](#_Ref315973770)."

**2\.**Upload a local file to the SFTP server.

put local-file \[ remote-file ]

#### Display files under a directory

**1\.**Enter SFTP client view.

For more information, see "[Establishing a connection to an SFTP server](#_Ref315973770)."

**2\.**Display files under a directory.

¡     dir \[ -a \| -l ] \[ remote-path ]

¡     ls \[ -a \| -l ] \[ remote-path ]

The dir command
has the same function as the ls command.

#### Deleting a file from the SFTP server

**1\.**Enter SFTP client view.

For more information, see "[Establishing a connection to an SFTP server](#_Ref315973770)."

**2\.**Delete a file from the SFTP server.

¡     delete remote-file

¡     remove remote-file

The delete command
has the same function as the remove command.

### Displaying help information

#### About this task

After you establish a connection to the
SFTP server, you can display the help information of SFTP client commands,
including the command syntax and parameter configuration.

#### Procedure

**1\.**Enter SFTP client view.

For more information, see "[Establishing a connection to an SFTP server](#_Ref315973770)."

**2\.**Display SFTP client command help
information.

¡     help

¡     ?

The help command
has the same function as the ? command.

### Terminating the connection with the SFTPserver

**1\.**Enter SFTP client view.

For more information, see "[Establishing a connection to an SFTP server](#_Ref315973770)."

**2\.**Terminate the connection with the SFTP
server and return to user view.

¡     bye

¡     exit

¡     quit

The three commands have the same function.

## Configuring the device as an SCPclient

### SCP client tasks at a glance

To configure an SCP client, perform the
following tasks:

**1\.**[Generating local
key pairs](#_Ref406674182)

Only required for the publickey, password-publickey, or any authentication method.

**2\.**(Optional.) [Specifying the source IP address for outgoing
SCP packets](#_Ref491441626)

**3\.**[Establishing a
connection to an SCP server](#_Ref406674194)

**4\.**(Optional.) [Deleting server public keys saved in the
public key file on the SCP client](#_Ref480289698) 

**5\.**(Optional.) [Establishing a connection to an SCP
server based on Suite B](#_Ref480289699)

### Generating local key pairs

#### About this task

You must generate local key pairs on SCP
clients when the SCP server uses the publickey, password-publickey, or any authentication
method.

#### Restrictions and guidelines

Local DSA, ECDSA, and RSA key pairs for SSH
use default names. You cannot assign names to the key pairs.

The key modulus length must be less than
2048 bits when you generate a DSA key pair.

When you generate an ECDSA key pair, you
can generate only a secp256r1 or secp384r1 ECDSA key pair.

The SCP client operating in FIPS mode
supports only ECDSA and RSA key pairs.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Generate local key pairs.

public-key local create { dsa \| ecdsa { secp256r1 \| secp384r1 } \| rsa }

### Specifying the source IP address for outgoing SCP packets

#### About this task

After you specify the source IP address for
outgoing SCP packets on an SCP client, the client uses the specified IP address
to communicate with the SCP server.

#### Restrictions and guidelines

As a best practice, specify the IP address
of a loopback interface as the source address of outgoing SCP packets for the
following purposes:

·     Ensuring the communication between the SCP client
and the SCP server.

·     Improving the manageability of SCP clients in authentication
service.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Specify the source address for outgoing SCP
packets.

IPv4:

scp client source { interface interface-type interface-number \| ip ip-address }

By default, an SCP client uses the
primary IPv4 address of the output interface in the matching route as the
source address of the outgoing SCP packets.

IPv6:

scp client ipv6 source { interface interface-type
interface-number \| ipv6 ipv6-address }

By default, an SCP client automatically
selects an IPv6 address as the source address of the outgoing packets in
compliance with RFC 3484\.

### Establishing a connection to an SCP server

#### About this task

Perform this task to enable the SCP client
feature on the device, establish a connection to the SCP server, and transfer
files with the server. You can specify the public key algorithm and the preferred
encryption, HMAC, and key exchange algorithms to be used during the connection.

To access the server, a client must use the
server's host public key to authenticate the server. As a best practice,
configure the server's host public key on the device in an insecure network. If
the server's host public key is not configured on the client, the client will
notify you to confirm whether to continue with the access.

·     If you choose to continue, the client accesses
the server and downloads the server's host public key. The downloaded public
key will be used to authenticate the server in subsequent accesses.

If the server public key is not specified
when you connect to the server, the device saves the server public key to the
public key file. It does not save the server public key to the configuration
file.

·     If you choose to not continue, the connection
cannot be established.

#### Restrictions and guidelines for establishing a connection to an SCP server

An SCP client cannot establish connections
to both IPv4 and IPv6 SCP servers.

#### Establishing a connection to an IPv4 SCP server

Execute the following command in user view
to connect to an IPv4 SCP server, and transfer files with the server:

In non-FIPS mode:

scp server \[ port-number ] \[ vpn-instance
vpn-instance-name ] { put \| get } source-file-name \[ destination-file-name ] \[ identity-key
{ dsa \| ecdsa-sha2-nistp256 \| ecdsa-sha2-nistp384 \| rsa \| { x509v3-ecdsa-sha2-nistp256 \| x509v3-ecdsa-sha2-nistp384 } pki-domain domain-name } \| prefer-compress zlib \| prefer-ctos-cipher { 3des-cbc \| aes128-cbc \| aes128-ctr \| aes128-gcm \| aes192-ctr \| aes256-cbc \| aes256-ctr \| aes256-gcm \| des-cbc \| sm4-cbc } \| prefer-ctos-hmac { md5 \| md5-96 \| sha1 \| sha1-96 \| sha2-256 \| sha2-512 \| sm3 } \| prefer-kex { dh-group-exchange-sha1 \| dh-group1-sha1
\|
dh-group14-sha1 \| ecdh-sha2-nistp256 \| ecdh-sha2-nistp384 } \| prefer-stoc-cipher { 3des-cbc \| aes128-cbc \| aes128-ctr \| aes128-gcm \| aes192-ctr \| aes256-cbc \| aes256-ctr \| aes256-gcm \| des-cbc \| sm4-cbc } \| prefer-stoc-hmac { md5 \| md5-96 \| sha1 \| sha1-96 \| sha2-256 \| sha2-512 \| sm3 } ] \* \[ { public-key keyname \| server-pki-domain domain-name } \| source { interface interface-type interface-number \| ip ip-address } ] \* \[ user username \[ password password \[ no-more-input ] ] ] 

In FIPS mode:

scp server \[ port-number ] \[ vpn-instance
vpn-instance-name ] { put \| get } source-file-name \[ destination-file-name ] \[ identity-key { ecdsa-sha2-nistp256 \| ecdsa-sha2-nistp384 \| rsa \| { x509v3-ecdsa-sha2-nistp256 \| x509v3-ecdsa-sha2-nistp384 } pki-domain domain-name } \| prefer-compress zlib \| prefer-ctos-cipher { aes128-cbc \| aes128-ctr \| aes128-gcm \| aes192-ctr \| aes256-cbc \| aes256-ctr \| aes256-gcm } \| prefer-ctos-hmac { sha1 \| sha1-96 \| sha2-256 \| sha2-512 } \| prefer-kex { dh-group14-sha1 \| ecdh-sha2-nistp256 \| ecdh-sha2-nistp384 } \| prefer-stoc-cipher { aes128-cbc \| aes128-ctr \| aes128-gcm \| aes192-ctr \| aes256-cbc \| aes256-ctr \| aes256-gcm } \| prefer-stoc-hmac { sha1 \| sha1-96 \| sha2-256 \| sha2-512 } ] \* \[ { public-key keyname \| server-pki-domain domain-name } \| source { interface interface-type interface-number \| ip ip-address } ] \* \[ user username \[ password password \[ no-more-input ] ] ]

#### Establishing a connection to an IPv6 SCP server

Execute the following command in user view
to connect to an IPv6 SCP server, and transfer files with the server.

In non-FIPS mode:

scp ipv6 server \[ port-number ] \[ vpn-instance
vpn-instance-name ] \[ -i interface-type interface-number ] { put \| get } source-file-name \[ destination-file-name ] \[ identity-key
{ dsa \| ecdsa-sha2-nistp256 \| ecdsa-sha2-nistp384 \| rsa \| { x509v3-ecdsa-sha2-nistp256 \| x509v3-ecdsa-sha2-nistp384 } pki-domain domain-name } \| prefer-compress zlib \| prefer-ctos-cipher { 3des-cbc \| aes128-cbc \| aes128-ctr \| aes128-gcm \| aes192-ctr \| aes256-cbc \| aes256-ctr \| aes256-gcm \| des-cbc \| sm4-cbc } \| prefer-ctos-hmac { md5 \| md5-96 \| sha1 \| sha1-96 \| sha2-256 \| sha2-512 \| sm3 } \| prefer-kex { dh-group-exchange-sha1 \| dh-group1-sha1
\|
dh-group14-sha1 \| ecdh-sha2-nistp256 \| ecdh-sha2-nistp384 } \| prefer-stoc-cipher { 3des-cbc \| aes128-cbc \| aes128-ctr \| aes128-gcm \| aes192-ctr \| aes256-cbc \| aes256-ctr \| aes256-gcm \| des-cbc \| sm4-cbc } \| prefer-stoc-hmac { md5 \| md5-96 \| sha1 \| sha1-96 \| sha2-256 \| sha2-512 \| sm3 } ] \* \[ { public-key keyname \| server-pki-domain domain-name } \| source { interface interface-type interface-number \| ipv6 ipv6-address } ] \* \[ user username \[ password password \[ no-more-input ] ] ] 

In FIPS mode:

scp ipv6 server \[ port-number ] \[ vpn-instance
vpn-instance-name ] \[ -i interface-type interface-number  ] { put \| get } source-file-name \[ destination-file-name ] \[ identity-key
{ ecdsa-sha2-nistp256 \| ecdsa-sha2-nistp384 \| rsa \| { x509v3-ecdsa-sha2-nistp256 \| x509v3-ecdsa-sha2-nistp384 } pki-domain domain-name } \| prefer-compress zlib \| prefer-ctos-cipher { aes128-cbc \| aes128-ctr \| aes128-gcm \| aes192-ctr \| aes256-cbc \| aes256-ctr \| aes256-gcm } \| prefer-ctos-hmac { sha1 \| sha1-96 \| sha2-256 \| sha2-512 } \| prefer-kex { dh-group14-sha1  \| ecdh-sha2-nistp256 \| ecdh-sha2-nistp384 } \| prefer-stoc-cipher { aes128-cbc \| aes128-ctr \| aes128-gcm \| aes192-ctr \| aes256-cbc \| aes256-ctr \| aes256-gcm } \| prefer-stoc-hmac { sha1 \| sha1-96 \| sha2-256 \| sha2-512 } ] \* \[ { public-key keyname \| server-pki-domain domain-name } \| source { interface interface-type interface-number \| ipv6 ipv6-address } ] \* \[ user username \[ password password \[ no-more-input ] ] ] 

### Deleting server public keys saved in the public key file on the SCP client

#### About this task

When the SCP client switches to FIPS mode
but the locally saved server public key does not comply with FIPS, the client
cannot connect to the server. To connect to the server, delete the server
public key saved on the client and make sure a FIPS-compliant public key has
been generated on the server.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Delete server public keys saved in the
public key file on the SCP client.

delete ssh client server-public-key \[ server-ip ip-address ]

### Establishing a connection to an SCP server based on Suite B

Execute the following command in user view
to establish a connection to an SCP server based on Suite B:

IPv4:

scp server \[ port-number ] \[ vpn-instance
vpn-instance-name ] { put \| get } source-file-name \[ destination-file-name ] suite-b \[ 128-bit \| 192-bit ] pki-domain domain-name \[ server-pki-domain domain-name ] \[ prefer-compress zlib ] \[ source { interface interface-type interface-number \| ip ip-address } ] \* \[ user username \[ password password \[ no-more-input ] ] ]

IPv6:

scp ipv6 server \[ port-number ] \[ vpn-instance
vpn-instance-name ] \[ -i interface-type interface-number ] { put \| get } source-file-name \[ destination-file-name ] suite-b \[ 128-bit \| 192-bit ] pki-domain domain-name \[ server-pki-domain domain-name ] \[ prefer-compress zlib ] \[ source { interface interface-type interface-number \| ipv6 ipv6-address } ] \* \[ user username \[ password password \[ no-more-input ] ] ]

## Specifying algorithms for SSH2

### About algorithms for SSH2

The SSH2 client and server use the
following types of algorithms for algorithm negotiation during the Stelnet,
SFTP, or SCP session establishment:

·     Key exchange algorithms.

·     Public key algorithms.

·     Encryption algorithms.

·     MAC algorithms.

If you specify algorithms, SSH2 uses only
the specified algorithms for algorithm negotiation. The client uses the
specified algorithms to initiate the negotiation, and the server uses the
matching algorithms to negotiate with the client. If multiple algorithms of the
same type are specified, the algorithm specified earlier has a higher priority
during negotiation.

### Specifying key exchange algorithms for SSH2

**1\.**Enter system view.

system-view

**2\.**Specify key exchange algorithms for SSH2.

In non-FIPS mode:

ssh2 algorithm key-exchange { dh-group-exchange-sha1
\| dh-group1-sha1 \| dh-group14-sha1 \| ecdh-sha2-nistp256 \| ecdh-sha2-nistp384 } \*

By default, SSH2 uses the ecdh-sha2-nistp256, ecdh-sha2-nistp384, dh\-group-exchange-sha1, dh-group14-sha1, and dh-group1-sha1 key
exchange algorithms in descending order of priority for algorithm negotiation.

In FIPS mode:

ssh2 algorithm key-exchange { dh-group14-sha1 \| ecdh-sha2-nistp256 \| ecdh-sha2-nistp384 } \*

By default, SSH2 uses the ecdh-sha2-nistp256, ecdh-sha2-nistp384, and
dh-group14-sha1 key exchange algorithms in descending order of priority for
algorithm negotiation.

### Specifying public key algorithms for SSH2

**1\.**Enter system view.

system-view

**2\.**Specify public key algorithms for SSH2.

In non-FIPS mode:

ssh2 algorithm public-key { dsa \| ecdsa-sha2-nistp256 \| ecdsa-sha2-nistp384 \| rsa \| x509v3-ecdsa-sha2-nistp256 \| x509v3-ecdsa-sha2-nistp384 } \*

By default, SSH2 uses the x509v3-ecdsa-sha2-nistp256, x509v3-ecdsa-sha2-nistp384, ecdsa-sha2-nistp256, ecdsa-sha2-nistp384, rsa, and dsa public key algorithms in descending order of priority for algorithm
negotiation.

In FIPS mode:

ssh2 algorithm public-key {
ecdsa-sha2-nistp256 \| ecdsa-sha2-nistp384 \| rsa \| x509v3-ecdsa-sha2-nistp256 \| x509v3-ecdsa-sha2-nistp384 } \*

By default, SSH2 uses the x509v3-ecdsa-sha2-nistp256, x509v3-ecdsa-sha2-nistp384, ecdsa-sha2-nistp256, ecdsa-sha2-nistp384, and rsa public key
algorithms in descending order of priority for algorithm negotiation.

### Specifying encryption algorithms for SSH2

**1\.**Enter system view.

system-view

**2\.**Specify encryption algorithms for SSH2.

In non-FIPS mode:

ssh2 algorithm cipher { 3des-cbc \| aes128-cbc \| aes128-ctr \| aes128-gcm \| aes192-ctr \| aes256-cbc \| aes256-ctr \| aes256-gcm \| des-cbc \| sm4-cbc } \*

By default, SSH2 uses the aes128-ctr, aes192-ctr, aes256-ctr, aes128-gcm, aes256-gcm, aes128-cbc, 3des-cbc, aes256-cbc, des-cbc, and sm4-cbc encryption
algorithms in descending order of priority for algorithm negotiation.

In FIPS mode:

ssh2 algorithm cipher { aes128-cbc \| aes128-ctr \| aes128-gcm \| aes192-ctr \| aes256-cbc \| aes256-ctr \| aes256-gcm } \*

By default, SSH2 uses the aes128-ctr, aes192-ctr, aes256-ctr, aes128-gcm, aes256-gcm, aes128-cbc, and aes256-cbc encryption algorithms in descending order of priority for algorithm
negotiation.

### Specifying MAC algorithms for SSH2

**1\.**Enter system view.

system-view

**2\.**Specify MAC algorithms for SSH2.

In non-FIPS mode:

ssh2 algorithm mac { md5 \| md5-96 \| sha1 \| sha1-96 \| sha2-256 \| sha2-512 \| sm3 } \*

By default, SSH2 uses the sha2-256, sha2-512, sha1, md5, sha1-96, md5-96, and sm3 MAC algorithms in descending order of priority for algorithm
negotiation.

In FIPS mode:

ssh2 algorithm mac { sha1 \| sha1-96 \| sha2-256 \| sha2-512 } \*

By default, SSH2 uses the sha2-256, sha2-512, sha1, and sha1-96 MAC algorithms in descending order of priority for algorithm negotiation.

## Display and maintenance commands for SSH

Execute display
commands in any view.

| Task | Command |
| --- | --- |
| Display the public keys of the local key pairs. | display public-key local { dsa \| ecdsa \| rsa } public \[ name publickey-name ] || Display information about peer public keys. | display public-key peer \[ brief \| name publickey-name ] || Display the source IP address configuration of the SCP client. | display scp client source || Display the source IP address configuration of the SFTP client. | display sftp client source || Display server public key information saved in the public key file on the SSH client. | display ssh client server-public-key \[ server-ip ip-address ] || Display the source IP address configuration of the Stelnet client. | display ssh client source || Display SSH server status or sessions. | display ssh server { session \| status } || Display SSH user information on the SSH server. | display ssh user-information \[ username ] || Display algorithms used by SSH2 in the algorithm negotiation stage. | display ssh2 algorithm || Display records for SSH user login exceptions. | display ssh exception-record |










‌

For more information about the display
public-key local and display
public-key peer commands, see public key
management commands in Security Command Reference.

## Stelnet configuration examples

Unless otherwise noted, devices in the
configuration examples operate in non-FIPS mode.

When the device acts as an Stelnet server
operating in FIPS mode, only ECDSA and RSA key pairs are supported. Do not
generate a DSA key pair on the Stelnet server.

### Example: Configuring the device as an Stelnet server (password authentication)

#### Network configuration

As shown in [Figure 1](#_Ref206494095):

·     The switch acts as the Stelnet server and uses
password authentication to authenticate the Stelnet client. The username and
password of the client are saved on the switch.

·     The host acts as the Stelnet client, using Stelnet client software (SSH2).
After the user on the host logs
in to the switch through
Stelnet, the user can configure and manage the switch as a network administrator.

Figure 1 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705437_x_Img_x_png_0_2216075_294551_0.png)

‌

#### Procedure

**1\.**Configure the Stelnet server:

\# Generate RSA key pairs.

\<Switch\> system-view

\[Switch] public-key local
create rsa

The range of public key
modulus is (512 \~ 4096).

If the key modulus is greater
than 512, it will take a few minutes.

Press CTRL\+C to abort.

Input the modulus length
\[default \= 1024]:

Generating Keys...

........................\+\+\+\+\+\+

...................\+\+\+\+\+\+

..\+\+\+\+\+\+\+\+

............\+\+\+\+\+\+\+\+

Create the key pair
successfully.

\# Generate a DSA key pair.

\[Switch] public-key local
create dsa

The range of public key
modulus is (512 \~ 2048).

If the key modulus is greater
than 512, it will take a few minutes.

Press CTRL\+C to abort.

Input the modulus length
\[default \= 1024]:

Generating Keys...

.\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\*

........\+......\+.....\+......................................\+

...\+.................\+..........\+...\+.

Create the key pair
successfully.

\# Generate an ECDSA key pair.

\[Switch] public-key local
create ecdsa secp256r1

Generating Keys...

.

Create the key pair
successfully.

\# Enable the Stelnet server.

\[Switch] ssh server enable

\# Assign an IP address to VLAN-interface
2\. The Stelnet client uses this address as the destination for SSH connection.

\[Switch] interface vlan-interface
2

\[Switch-Vlan-interface2] ip
address 192.168.1.40 255.255.255.0

\[Switch-Vlan-interface2] quit

\# Set the authentication mode to AAA for
user lines.

\[Switch] line vty 0 63

\[Switch-line-vty0-63]
authentication-mode scheme

\[Switch-line-vty0-63] quit

\# Create a local device management user named
client001.

\[Switch] local-user client001
class manage

\# Set a password for local user client001. 

\[Switch-luser-manage-client001]
password simple 123456TESTplat\&!

\# Authorize local user client001 to use
the SSH service.

\[Switch-luser-manage-client001]
service-type ssh

\# Assign the network-admin user role to local user
client001.

\[Switch-luser-manage-client001]
authorization-attribute user-role network-admin

\[Switch-luser-manage-client001]
quit

\# Create an SSH user named client001.
Specify the service type as stelnet and the authentication method as password for the user.

\[Switch] ssh user client001 service-type
stelnet authentication-type password

**2\.**Establish a connection to the Stelnet
server:

There are different types of Stelnet client
software, such as PuTTY and OpenSSH. This example uses an Stelnet client that
runs PuTTY version 0.58.

To establish a connection to the Stelnet server:

**a.**Launch PuTTY.exe to enter the interface
shown in [Figure 2](#_Ref327342101).

**b.**In the Host Name (or IP
address) field, enter IP address 192.168.1.40
of the Stelnet server.

**c.**Click Open.

Figure 2 Specifying
the host name (or IP address)

![](https://resource.h3c.com/en/202407/12/20240712_11705438_x_Img_x_png_1_2216075_294551_0.png)

‌

**d.**Enter username client001
and the password to log in to the Stelnet server.

### Example: Configuring the device as an Stelnet server (publickey authentication)

#### Network configuration

As shown in [Figure 3](#_Ref303957368):

·     The switch acts as the Stelnet server, and it uses
publickey authentication and the RSA public key algorithm.

·     The host acts as the Stelnet client, using Stelnet client software (SSH2).
After the user on the host logs
in to the switch through
Stelnet, the user can configure and manage the switch as a network administrator.

Figure 3 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705449_x_Img_x_png_2_2216075_294551_0.png)

‌

#### Procedure

In the server configuration, the client's
host public key is required. Use the client software to generate RSA key pairs
on the client before configuring the Stelnet server.

There are different types of Stelnet client
software, such as PuTTY and OpenSSH. This example uses an Stelnet client that
runs PuTTY version 0.58.

The configuration procedure is as follows:

**1\.**Generate RSA key pairs on the Stelnet client:

**a.**Run PuTTYGen.exe on the client, select SSH-2 RSA and click Generate.

Figure
4 Generating a key pair on the client

![](https://resource.h3c.com/en/202407/12/20240712_11705450_x_Img_x_png_3_2216075_294551_0.png)

‌

**b.**Continue moving the mouse during the key
generating process, but do not place the mouse over the green progress bar
shown in [Figure 5](#_Ref303002298). Otherwise, the progress bar stops moving and the key pair generating
progress stops.

Figure 5 Generating
process

![](https://resource.h3c.com/en/202407/12/20240712_11705451_x_Img_x_png_4_2216075_294551_0.png)

‌

**c.**After the key pair is generated, click Save public key to save the public key.

A file saving window appears.

Figure 6 Saving a
key pair on the client

![](https://resource.h3c.com/en/202407/12/20240712_11705452_x_Img_x_png_5_2216075_294551_0.png)

‌

**d.**Enter a file name (key.pub
in this example), and click Save.

**e.**On the page shown in [Figure 6](#_Ref378240352), click Save private key to save the private key.

A confirmation dialog box appears.

**f.**Click Yes.

A file saving window appears.

**g.**Enter a file name (private.ppk
in this example), and click Save.

**h.**Transmit the public key file to the server
through FTP or TFTP. (Details not shown.)

**2\.**Configure the Stelnet server:

\# Generate RSA key pairs.

\<Switch\> system-view

\[Switch] public-key local
create rsa

The range of public key
modulus is (512 \~ 4096).

If the key modulus is greater
than 512, it will take a few minutes.

Press CTRL\+C to abort.

Input the modulus length
\[default \= 1024]:

Generating Keys...

........................\+\+\+\+\+\+

...................\+\+\+\+\+\+

..\+\+\+\+\+\+\+\+

............\+\+\+\+\+\+\+\+

Create the key pair
successfully.

\# Generate a DSA key pair.

\[Switch] public-key local
create dsa

The range of public key
modulus is (512 \~ 2048).

If the key modulus is greater
than 512, it will take a few minutes.

Press CTRL\+C to abort.

Input the modulus length
\[default \= 1024]:

Generating Keys...

.\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\*

........\+......\+.....\+......................................\+

...\+.................\+..........\+...\+

Create the key pair
successfully.

\# Generate an ECDSA key pair.

\[Switch] public-key local
create ecdsa secp256r1

Generating Keys...

.

Create the key pair
successfully.

\# Enable the Stelnet server.

\[Switch] ssh server enable

\# Assign an IP address to VLAN-interface
2\. The Stelnet client uses this IP address as the destination for SSH
connection.

\[Switch] interface vlan-interface
2

\[Switch-Vlan-interface2] ip
address 192.168.1.40 255.255.255.0

\[Switch-Vlan-interface2] quit

\# Set the authentication mode to AAA for
user lines.

\[Switch] line vty 0 63

\[Switch-line-vty0-63] authentication-mode
scheme

\[Switch-line-vty0-63] quit

\# Import the client's public key from the public key file key.pub and name
it switchkey.

\[Switch] public-key peer switchkey
import sshkey key.pub

\# Create an SSH user named client002.
Specify the authentication method as publickey for the user, and assign the
public key switchkey to the user.

\[Switch] ssh user client002
service-type stelnet authentication-type publickey assign publickey switchkey

\# Create a local device management user named
client002.

\[Switch] local-user client002
class manage

\# Authorize local user client002 to use
the SSH service.

\[Switch-luser-manage-client002]
service-type ssh

\# Assign the network-admin user role to local user
client002.

\[Switch-luser-manage-client002]
authorization-attribute user-role network-admin

\[Switch-luser-manage-client002]
quit

**3\.**Specify the private key file and establish a
connection to the Stelnet server:

**a.**Launch PuTTY.exe on the Stelnet client to
enter the interface shown in [Figure 7](#_Ref327342302).

**b.**In the Host Name
(or IP address) field, enter IP address 192.168.1.40 of the Stelnet server.

Figure 7 Specifying
the host name (or IP address)

![](https://resource.h3c.com/en/202407/12/20240712_11705453_x_Img_x_png_6_2216075_294551_0.png)

‌

c.     From the navigation tree, select Connection
\> SSH.

The window shown in [Figure 8](#_Ref346717221)
appears.

**d.**Set Preferred SSH protocol version to 2.

Figure 8 Setting
the preferred SSH version

![](https://resource.h3c.com/en/202407/12/20240712_11705454_x_Img_x_png_7_2216075_294551_0.png)

‌

e.     From the navigation tree, select Connection
\> SSH \> Auth.

The window shown in [Figure 9](#_Ref327342345)
appears.

**f.**Click Browse… to
open the file selection window,
and then select the private key file (private.ppk in
this example).

**g.**Click Open.

Figure 9 Specifying
the private key file

![](https://resource.h3c.com/en/202407/12/20240712_11705455_x_Img_x_png_8_2216075_294551_0.png)

‌

**h.**Entering username client002
to log in to the Stelnet server.

### Example: Configuring the device as an Stelnet client (password authentication)

#### Network configuration

As shown in [Figure 10](#_Ref303002525):

·     Switch B acts as the Stelnet server and uses
password authentication to authenticate the Stelnet client. The username and
password of the client are saved on Switch B.

·     Switch A acts as the Stelnet client. After the user
on Switch A logs in to Switch B through Stelnet,
the user can configure and manage Switch B as a network administrator.

Figure 10 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705456_x_Img_x_png_9_2216075_294551_0.png)

‌

#### Procedure

**1\.**Configure the Stelnet server:

\# Generate RSA key pairs.

\<SwitchB\> system-view

\[SwitchB] public-key local
create rsa

The range of public key
modulus is (512 \~ 4096).

If the key modulus is greater
than 512, it will take a few minutes.

Press CTRL\+C to abort.

Input the modulus length
\[default \= 1024]:

Generating Keys...

........................\+\+\+\+\+\+

...................\+\+\+\+\+\+

..\+\+\+\+\+\+\+\+

............\+\+\+\+\+\+\+\+

Create the key pair
successfully.

\# Generate a DSA key pair.

\[SwitchB] public-key local
create dsa

The range of public key
modulus is (512 \~ 2048).

If the key modulus is greater
than 512, it will take a few minutes.

Press CTRL\+C to abort.

Input the modulus length
\[default \= 1024]:

Generating Keys...

.\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\*

........\+......\+.....\+......................................\+

...\+.................\+..........\+...\+

Create the key pair
successfully.

\# Generate an ECDSA key pair.

\[SwitchB] public-key local
create ecdsa secp256r1

Generating Keys...

.

Create the key pair
successfully.

\# Enable the Stelnet server.

\[SwitchB] ssh server enable

\# Assign an IP address to VLAN-interface
2\. The Stelnet client uses this address as the destination address of the SSH
connection.

\[SwitchB] interface vlan-interface
2

\[SwitchB-Vlan-interface2] ip
address 192.168.1.40 255.255.255.0

\[SwitchB-Vlan-interface2] quit

\# Set the authentication mode to AAA for
user lines.

\[SwitchB] line vty 0 63

\[SwitchB-line-vty0-63]
authentication-mode scheme

\[SwitchB-line-vty0-63] quit

\# Create a local device management user
named client001.

\[SwitchB] local-user client001
class manage

\# Set a password for local user client001.

\[SwitchB-luser-manage-client001]
password simple 123456TESTplat\&!

\# Authorize local user client001 to use
the SSH service.

\[SwitchB-luser-manage-client001]
service-type ssh

\# Assign the network-admin user role to local user client001.

\[SwitchB-luser-manage-client001]
authorization-attribute user-role network-admin

\[SwitchB-luser-manage-client001]
quit

\# Create an SSH user named client001.
Specify the service type as stelnet
and the authentication method as password for the
user.

\[SwitchB] ssh user client001
service-type stelnet authentication-type password

\# Specify public key algorithm dsa for SSH2.

\[SwitchB] ssh2 algorithm
public-key dsa

**2\.**Establish a connection to the Stelnet server:

\# Assign an IP address to VLAN-interface
2\.

\<SwitchA\> system-view

\[SwitchA] interface vlan-interface
2

\[SwitchA-Vlan-interface2] ip
address 192.168.1.56 255.255.255.0

\[SwitchA-Vlan-interface2] quit

\[SwitchA] quit

¡     If
the client does not have the server's host public key, and is connected to the
server for the first time:

\# Establish an SSH connection to server
192.168.1.40. Enter username client001, and then enter y to continue accessing the server without authenticating the server,
and enter y to download and save the server's host public key.

\<SwitchA\> ssh2 192.168.1.40

Username: client001

Press CTRL\+C to abort.

Connecting to 192.168.1.40
port 22\.

The server is not
authenticated. Continue? \[Y/N]:y

Do you want to save the server
public key? \[Y/N]:y

[\[email protected]](/cdn-cgi/l/email-protection)'s
password:

 

Enter a character \~ and a dot
to abort.

 

\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*

\* Copyright (c) 2004-2017 New
H3C Technologies Co., Ltd. All rights reserved.\*

\* Without the owner's prior
written consent,                                 \*

\* no decompiling or
reverse-engineering shall be allowed.                    \*

\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*

 

\<SwitchB\>

After you enter the correct password, you
can access Switch B successfully. At the next connection attempt, the client automatically
authenticates the server by using the server's host public key that is saved on
the client, and you only need to enter the password to log in to Switch B.

¡     If
you configure the server's host public key on the client before establishing a
connection to the server:

\# Use the display public-key
local dsa public command on the server to display
the server's host public key. (Details not shown.)

\# Enter public key view of the client and
copy the host public key of the server to the client.

\[SwitchA] public-key peer key1

Enter public key view. Return
to system view with "peer-public-key end" command.

\[SwitchA-pkey-public-key-key1]308201B73082012C06072A8648CE3804013082011F0281810

0D757262C4584C44C211F18BD96E5F0

\[SwitchA-pkey-public-key-key1]61C4F0A423F7FE6B6B85B34CEF72CE14A0D3A5222FE08CECE

65BE6C265854889DC1EDBD13EC8B274

\[SwitchA-pkey-public-key-key1]DA9F75BA26CCB987723602787E922BA84421F22C3C89CB9B0

6FD60FE01941DDD77FE6B12893DA76E

\[SwitchA-pkey-public-key-key1]EBC1D128D97F0678D7722B5341C8506F358214B16A2FAC4B3

68950387811C7DA33021500C773218C

\[SwitchA-pkey-public-key-key1]737EC8EE993B4F2DED30F48EDACE915F0281810082269009E

14EC474BAF2932E69D3B1F18517AD95

\[SwitchA-pkey-public-key-key1]94184CCDFCEAE96EC4D5EF93133E84B47093C52B20CD35D02

492B3959EC6499625BC4FA5082E22C5

\[SwitchA-pkey-public-key-key1]B374E16DD00132CE71B020217091AC717B612391C76C1FB2E

88317C1BD8171D41ECB83E210C03CC9

\[SwitchA-pkey-public-key-key1]B32E810561C21621C73D6DAAC028F4B1585DA7F42519718CC

9B09EEF0381840002818000AF995917

\[SwitchA-pkey-public-key-key1]E1E570A3F6B1C2411948B3B4FFA256699B3BF871221CC9C5D

F257523777D033BEE77FC378145F2AD

\[SwitchA-pkey-public-key-key1]D716D7DB9FCABB4ADBF6FB4FDB0CA25C761B308EF53009F71

01F7C62621216D5A572C379A32AC290

\[SwitchA-pkey-public-key-key1]E55B394A217DA38B65B77F0185C8DB8095522D1EF044B465E

8716261214A5A3B493E866991113B2D

\[SwitchA-pkey-public-key-key1]485348

\[SwitchA-pkey-public-key-key1]
peer-public-key end

\# Specify public key algorithm dsa for SSH2.

\[SwitchA] ssh2 algorithm
public-key dsa

\[SwitchA] quit

\# Establish an SSH connection to the
server, and specify the host public key of the server.

\<SwitchA\> ssh2
192.168.1.40 public-key key1

Username: client001

Press CTRL\+C to abort.

Connecting to 192.168.1.40
port 22\.

[\[email protected]](/cdn-cgi/l/email-protection)'s
password:

Enter a character \~ and a dot
to abort.

 

\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*

\* Copyright (c) 2004-2017 New
H3C Technologies Co., Ltd. All rights reserved.\*

\* Without the owner's prior
written consent,                                 \*

\* no decompiling or reverse-engineering
shall be allowed.                    \*

\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*

 

\<SwitchB\>

After you enter the correct username and
password, you can successfully log in to Switch B.

¡     If
the client already has the server's host public key:

\# Establish an SSH connection to server
192.168.1.40. 

\<SwitchA\> ssh2 192.168.1.40

Username: client001

Press CTRL\+C to abort.

Connecting to 192.168.1.40
port 22\.

[\[email protected]](/cdn-cgi/l/email-protection)'s
password:

Enter a character \~ and a dot
to abort.

\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*

\* Copyright (c) 2004-2017 New
H3C Technologies Co., Ltd. All rights reserved.\*

\* Without the owner's prior
written consent,                                 \*

\* no decompiling or
reverse-engineering shall be allowed.                    \*

\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*

\<SwitchB\>

After you enter the correct username and
password, you can successfully log in to Switch B.

### Example: Configuring the device as an Stelnet client (publickey authentication)

#### Network configuration

As shown in [Figure 11](#_Ref303085991):

·     Switch B acts as the Stelnet server, and it uses
publickey authentication and the DSA public key algorithm.

·     Switch A acts as the Stelnet client. After the user
on Switch A logs in to Switch B through Stelnet, the user can configure and manage Switch B as a network administrator.

Figure 11 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705439_x_Img_x_png_10_2216075_294551_0.png)

‌

#### Procedure

In the server configuration, the client's
host public key is required. Generate a DSA key pair on the client before
configuring the Stelnet server.

**1\.**Configure the Stelnet client:

\# Assign an IP address to VLAN-interface
2\.

\<SwitchA\> system-view

\[SwitchA] interface vlan-interface
2

\[SwitchA-Vlan-interface2] ip
address 192.168.1.56 255.255.255.0

\[SwitchA-Vlan-interface2] quit

\# Generate a DSA key pair.

\[SwitchA] public-key local
create dsa

The range of public key
modulus is (512 \~ 2048).

If the key modulus is greater
than 512, it will take a few minutes.

Press CTRL\+C to abort.

Input the modulus length
\[default \= 1024]:

Generating Keys...

.\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\*

........\+......\+.....\+......................................\+

...\+.................\+..........\+...\+

Create the key pair
successfully.

\# Export the DSA host public key to a public key file named key.pub.

\[SwitchA] public-key local
export dsa ssh2 key.pub

\[SwitchA] quit

\# Transmit the public key file key.pub to the
server through FTP or TFTP. (Details not shown.)

**2\.**Configure the Stelnet server:

\# Generate RSA key pairs.

\<SwitchB\> system-view

\[SwitchB] public-key local
create rsa

The range of public key
modulus is (512 \~ 4096\)

If the key modulus is greater
than 512, it will take a few minutes.

Press CTRL\+C to abort.

Input the modulus length
\[default \= 1024]:

Generating Keys...

........................\+\+\+\+\+\+

...................\+\+\+\+\+\+

..\+\+\+\+\+\+\+\+

............\+\+\+\+\+\+\+\+

Create the key pair
successfully.

\# Generate a DSA key pair.

\[SwitchB] public-key local
create dsa

The range of public key
modulus is (512 \~ 2048).

If the key modulus is greater
than 512, it will take a few minutes.

Press CTRL\+C to abort.

Input the modulus length
\[default \= 1024]:

Generating Keys...

.\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\*

........\+......\+.....\+......................................\+

...\+.................\+..........\+...\+

Create the key pair
successfully.

\# Generate an ECDSA key pair.

\[SwitchB] public-key local
create ecdsa secp256r1

Generating Keys...

.

Create the key pair
successfully.

\# Enable the Stelnet server.

\[SwitchB] ssh server enable

\# Assign an IP address to VLAN-interface
2\. The Stelnet client uses this address as the destination address for SSH
connection.

\[SwitchB] interface vlan-interface
2

\[SwitchB-Vlan-interface2] ip
address 192.168.1.40 255.255.255.0

\[SwitchB-Vlan-interface2] quit

\# Set the authentication mode to AAA for
user lines.

\[SwitchB] line vty 0 63

\[SwitchB-line-vty0-63]
authentication-mode scheme

\[SwitchB-line-vty0-63] quit

\# Import the peer public key from the public key file key.pub, and
name it switchkey.

\[SwitchB] public-key peer switchkey
import sshkey key.pub

\# Create an SSH user named client002.
Specify the authentication method as publickey for the user. Assign the
public key switchkey to the user.

\[SwitchB] ssh user client002
service-type stelnet authentication-type publickey assign publickey switchkey

\# Create a local device management user named
client002.

\[SwitchB] local-user client002
class manage

\# Authorize local user client002 to use
the SSH service.

\[SwitchB-luser-manage-client002]
service-type ssh

\# Assign the network-admin user role to local user
client002.

\[SwitchB-luser-manage-client002]
authorization-attribute user-role network-admin

\[SwitchB-luser-manage-client002]
quit

**3\.**Establish an SSH connection to the Stelnet
server.

\<SwitchA\> ssh2
192.168.1.40 identity-key dsa

Username: client002

Press CTRL\+C to abort.

Connecting to 192.168.1.40
port 22\.

The server is not
authenticated. Continue? \[Y/N]:y

Do you want to save the server
public key? \[Y/N]:n

Enter a character \~ and a dot
to abort.

 

\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*

\* Copyright (c) 2004-2017 New
H3C Technologies Co., Ltd. All rights reserved.\*

\* Without the owner's prior
written consent,                                 \*

\* no decompiling or
reverse-engineering shall be allowed.                    \*

\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*

 

\<SwitchB\>

After you enter username client002 and
then enter y to continue accessing the server, you can log in to the server successfully.

### Example: Configuring Stelnet based on 128-bit Suite B algorithms

#### Network configuration

As shown in [Figure 12](#_Ref452974329):

·     Switch B acts as the Stelnet Suite B server (SSH2),
and it uses publickey authentication to authenticate the Stelnet client.

·     Switch A acts as an Stelnet Suite B client (SSH2).
After the user on Switch A logs in to Switch B through the Stelnet Suite B client
software, the user can configure and manage Switch B as an administrator.

Figure 12 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705440_x_Img_x_png_11_2216075_294551_0.png)

‌

#### Procedure

**1\.**Generate the client's certificate and the
server's certificate. (Details not shown.)

You must first configure the certificates
of the server and the client because they are required for identity
authentication between the two parties.

In this example, the server's certificate
file is ssh-server-ecdsa256.p12 and the client's certificate file is ssh-client-ecdsa256.p12.

**2\.**Configure the Stelnet client:

You can modify the pkix version of the
client software OpenSSH to support Suite B. This example uses an H3C switch as
an Stelnet client.

\# Upload the server's certificate file ssh-server-ecdsa256.p12 and the client's certificate file ssh-client-ecdsa256.p12 to the
Stelnet client through FTP or TFTP. (Details not shown.)

\# Create a PKI domain named server256 for
verifying the server's certificate and enter its view.

\<SwitchA\> system-view

\[SwitchA] pki domain server256

\# Disable CRL checking.

\[SwitchA-pki-domain-server256]
undo crl check enable

\[SwitchA-pki-domain-server256]
quit

\# Import local certificate file ssh-server-ecdsa256.p12 to PKI domain server256.

\[SwitchA] pki import domain server256
p12 local filename ssh-server-ecdsa256.p12

The system is going to save
the key pair. You must specify a key pair name, which is a case-insensitive
string of 1 to 64 characters. Valid characters include a to z, A to Z, 0 to 9,
and hyphens (-).

Please enter the key pair
name\[default name: server256]:

\# Display information about local certificates
in PKI domain server256.

\[SwitchA] display pki
certificate domain server256 local

Certificate:

    Data:

        Version: 3 (0x2)

        Serial Number: 3 (0x3)

    Signature Algorithm:
ecdsa-with-SHA256

        Issuer: C\=CN,
ST\=Beijing, L\=Beijing, O\=H3C, OU\=Software, CN\=SuiteB CA

        Validity

            Not Before: Aug 21
08:39:51 2015 GMT

            Not After : Aug 20
08:39:51 2016 GMT

        Subject: C\=CN,
ST\=Beijing, O\=H3C, OU\=Software, CN\=SSH Server secp256

        Subject Public Key
Info:

            Public Key
Algorithm: id-ecPublicKey

                Public-Key:
(256 bit)

                pub:

                   
04:a2:b4:b4:66:1e:3b:d5:50:50:0e:55:19:8d:52:

                   
6d:47:8c:3d:3d:96:75:88:2f:9a:ba:a2:a7:f9:ef:

                    0a:a9:20:b7:b6:6a:90:0e:f8:c6:de:15:a2:23:81:

                   
3c:9e:a2:b7:83:87:b9:ad:28:c8:2a:5e:58:11:8e:

                   
c7:61:4a:52:51

                ASN1 OID:
prime256v1

                NIST CURVE:
P-256

        X509v3 extensions:

            X509v3 Basic
Constraints:

                CA:FALSE

            Netscape Comment:

                OpenSSL
Generated Certificate

            X509v3 Subject Key
Identifier:

               
08:C1:F1:AA:97:45:19:6A:DA:4A:F2:87:A1:1A:E8:30:BD:31:30:D7

            X509v3 Authority
Key Identifier:

               
keyid:5A:BE:85:49:16:E5:EB:33:80:25:EB:D8:91:50:B4:E6:3E:4F:B8:22

 

    Signature Algorithm:
ecdsa-with-SHA256

        
30:65:02:31:00:a9:16:e9:c1:76:f0:32:fc:4b:f9:8f:b6:7f:

         31:a0:9f:de:a7:cc:33:29:27:2c:71:2e:f9:0d:74:cb:25:c9:

        
00:d2:52:18:7f:58:3f:cc:7e:8b:d3:42:65:00:cb:63:f8:02:

        
30:01:a2:f6:a1:51:04:1c:61:78:f6:6b:7e:f9:f9:42:8d:7c:

        
a7:bb:47:7c:2a:85:67:0d:81:12:0b:02:98:bc:06:1f:c1:3c:

         9b:c2:1b:4c:44:38:5a:14:b2:48:63:02:2b

\# Create a PKI domain named client256 for
the client's certificate and enter its view.

\[SwitchA] pki domain client256

\# Disable CRL checking.

\[SwitchA-pki-domain-client256]
undo crl check enable

\[SwitchA-pki-domain-client256]
quit

\# Import local certificate file ssh-client-ecdsa256.p12 to PKI domain client256.

\[SwitchA] pki import domain
client256 p12 local filename ssh-client-ecdsa256.p12

The system is going to save
the key pair. You must specify a key pair name, which is a case-insensitive
string of 1 to 64 characters. Valid characters include a to z, A to Z, 0 to 9,
and hyphens (-).

Please enter the key pair
name\[default name: client256]:

\# Display information about local certificates
in PKI domain client256.

\[SwitchA] display pki
certificate domain client256 local

Certificate:

    Data:

        Version: 3 (0x2)

        Serial Number: 4 (0x4)

    Signature Algorithm:
ecdsa-with-SHA256

        Issuer: C\=CN,
ST\=Beijing, L\=Beijing, O\=H3C, OU\=Software, CN\=SuiteB CA

        Validity

            Not Before: Aug 21
08:41:09 2015 GMT

            Not After : Aug 20
08:41:09 2016 GMT

        Subject: C\=CN,
ST\=Beijing, O\=H3C, OU\=Software, CN\=SSH Client secp256

        Subject Public Key
Info:

            Public Key
Algorithm: id-ecPublicKey

                Public-Key:
(256 bit)

                pub:

                   
04:da:e2:26:45:87:7a:63:20:e7:ca:7f:82:19:f5:

                   
96:88:3e:25:46:f8:2f:9a:4c:70:61:35:db:e4:39:

                   
b8:38:c4:60:4a:65:28:49:14:32:3c:cc:6d:cd:34:

                   
29:83:84:74:a7:2d:0e:75:1c:c2:52:58:1e:22:16:

                   
12:d0:b4:8a:92

                ASN1 OID:
prime256v1

                NIST CURVE:
P-256

        X509v3 extensions:

            X509v3 Basic
Constraints:

                CA:FALSE

            Netscape Comment:

                OpenSSL
Generated Certificate

            X509v3 Subject Key
Identifier:

               
1A:61:60:4D:76:40:B8:BA:5D:A1:3C:60:BC:57:98:35:20:79:80:FC

            X509v3 Authority
Key Identifier:

               
keyid:5A:BE:85:49:16:E5:EB:33:80:25:EB:D8:91:50:B4:E6:3E:4F:B8:22

 

    Signature Algorithm:
ecdsa-with-SHA256

        
30:66:02:31:00:9a:6d:fd:7d:ab:ae:54:9a:81:71:e6:bb:ad:

        
5a:2e:dc:1d:b3:8a:bf:ce:ee:71:4e:8f:d9:93:7f:a3:48:a1:

        
5c:17:cb:22:fa:8f:b3:e5:76:89:06:9f:96:47:dc:34:87:02:

        
31:00:e3:af:2a:8f:d6:8d:1f:3a:2b:ae:2f:97:b3:52:63:b6:

        
18:67:70:2c:93:2a:41:c0:e7:fa:93:20:09:4d:f4:bf:d0:11:

        
66:0f:48:56:01:1e:c3:be:37:4e:49:19:cf:c6

\# Assign an IP address to VLAN-interface
2\.

\<SwitchA\> system-view

\[SwitchA] interface vlan-interface
2

\[SwitchA-Vlan-interface2] ip
address 192.168.1.56 255.255.255.0

\[SwitchA-Vlan-interface2] quit

**3\.**Configure the Stelnet server:

\# Upload the server's certificate file ssh-server-ecdsa256.p12 and the client's certificate file ssh-client-ecdsa256.p12 to the
Stelnet server through FTP or TFTP. (Details not shown.)

\# Create a PKI domain named client256 for
verifying the client's certificate and import the file of the client's
certificate to this domain. (Details not shown.)

\# Create a PKI domain named server256 for
the server's certificate and import the file of the server's certificate to
this domain. (Details not shown.)

\# Specify Suite B algorithms for
algorithm negotiation.

\<SwitchB\> system-view

\[SwitchB] ssh2 algorithm
key-exchange ecdh-sha2-nistp256

\[SwitchB] ssh2 algorithm
cipher aes128-gcm

\[SwitchB] ssh2 algorithm
public-key x509v3-ecdsa-sha2-nistp256 x509v3-ecdsa-sha2-nistp384

\# Specify server256 as the PKI domain of the
server's certificate.

\[SwitchB] ssh server
pki-domain server256

\# Enable the Stelnet server.

\[SwitchB] ssh server enable

\# Assign an IP address to VLAN-interface
2\.

\[SwitchB] interface vlan-interface
2

\[SwitchB-Vlan-interface2] ip
address 192.168.1.40 255.255.255.0

\[SwitchB-Vlan-interface2] quit

\# Set the authentication mode to AAA for
user lines.

\[SwitchB] line vty 0 63

\[SwitchB-line-vty0-63]
authentication-mode scheme

\[SwitchB-line-vty0-63] quit

\# Create a local device management user
named client001. Authorize the user to use the SSH service and assign the network-admin
user role to the user.

\[SwitchB] local-user client001
class manage

\[SwitchB-luser-manage-client001]
service-type ssh

\[SwitchB-luser-manage-client001]
authorization-attribute user-role network-admin

\[SwitchB-luser-manage-client001]
quit

\# Create an SSH user named client001.
Specify the publickey authentication method for the user and specify client256 as the
PKI domain for verifying the client's certificate.

\[SwitchB] ssh user client001
service-type stelnet authentication-type publickey assign pki-domain client256

**4\.**Establish an SSH connection to the Stelnet
server based on the 128-bit Suite B algorithms:

\# Establish an SSH connection to the server
at 192.168.1.40.

\<SwitchA\>  ssh2 192.168.1.40
suite-b 128-bit pki-domain client256 server-pki-domain server256

Username: client001

Press CTRL\+C to abort.

Connecting to 192.168.1.40
port 22\.

Enter a character \~ and a dot
to abort.

 

\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*

\* Copyright (c) 2004-2017 New
H3C Technologies Co., Ltd. All rights reserved.\*

\* Without the owner's prior
written consent,                                 \*

\* no decompiling or
reverse-engineering shall be allowed.                    \*

\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*

 

\<SwitchB\>

## SFTP configuration examples

Unless otherwise noted, devices in the
configuration examples operate in non-FIPS mode.

When the device acts as an SFTP server
operating in FIPS mode, only ECDSA and RSA key pairs are supported. Do not
generate a DSA key pair on the SFTP server.

### Example: Configuring the device as an SFTP server (password authentication)

#### Network configuration

As shown in [Figure 13](#_Ref182890183):

·     The switch acts as the SFTP server and uses
password authentication to authenticate the SFTP client. The username and
password of the client are saved on the switch.

·     The host acts as the SFTP client. After the user on the client logs in to
the switch through SFTP, the
user can perform file management and transfer operations on the switch as a network administrator.

Figure 13 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705441_x_Img_x_png_12_2216075_294551_0.png)

‌

#### Procedure

**1\.**Configure the SFTP server:

\# Generate RSA key pairs.

\<Switch\> system-view

\[Switch] public-key local
create rsa

The range of public key
modulus is (512 \~ 4096).

If the key modulus is greater
than 512, it will take a few minutes.

Press CTRL\+C to abort.

Input the modulus length
\[default \= 1024]:

Generating Keys...

........................\+\+\+\+\+\+

...................\+\+\+\+\+\+

..\+\+\+\+\+\+\+\+

............\+\+\+\+\+\+\+\+

Create the key pair
successfully.

\# Generate a DSA key pair.

\[Switch] public-key local
create dsa

The range of public key
modulus is (512 \~ 2048).

If the key modulus is greater
than 512, it will take a few minutes.

Press CTRL\+C to abort.

Input the modulus length
\[default \= 1024]:

Generating Keys...

.\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\*

........\+......\+.....\+......................................\+

...\+.................\+..........\+...\+

Create the key pair
successfully.

\# Generate an ECDSA key pair.

\[Switch] public-key local
create ecdsa secp256r1

Generating Keys...

.

Create the key pair
successfully.

\# Enable the SFTP server.

\[Switch] sftp server enable

\# Assign an IP address to VLAN-interface 2\.
The client uses this address as the destination for SSH connection.

\[Switch] interface vlan-interface
2

\[Switch-Vlan-interface2] ip
address 192.168.1.45 255.255.255.0

\[Switch-Vlan-interface2] quit

\# Create a local device management user named
client002.

\[Switch] local-user client002
class manage

\# Set a password for local user client002.

\[Switch-luser-manage-client002]
password simple 123456TESTplat\&!

\# Authorize local user client002 to use
the SSH service.

\[Switch-luser-manage-client002]
service-type ssh

\# Assign the network-admin user role and working
directory flash:/ to local user client002.

\[Switch-luser-manage-client002]
authorization-attribute user-role network-admin work-directory flash:/

\[Switch-luser-manage-client002]
quit

\# Create an SSH user named client002.
Specify the authentication method as password and service type as sftp for the
user.

\[Switch] ssh user client002
service-type sftp authentication-type password

**2\.**Establish a connection between the SFTP
client and the SFTP server:

This example uses an SFTP client that
runs PSFTP of PuTTy version 0.58. PSFTP supports only password authentication.

To establish a connection to the SFTP
server:

**a.**Run the psftp.exe
to launch the client interface shown in [Figure 14](#_Ref182890202),
and enter the following command:

open 192.168.1.45

**b.**Enter username client002
and the password to log in to the SFTP server.

Figure 14 SFTP
client interface

![](https://resource.h3c.com/en/202407/12/20240712_11705442_x_Img_x_png_13_2216075_294551_0.png)

‌

### Example: Configuring the device as an SFTP client (publickey authentication)

#### Network configuration

As shown in [Figure 15](#_Ref206494375):

·     Switch B acts as the SFTP server, and it uses publickey
authentication and the RSA public key algorithm.

·     Switch A acts as the SFTP client. After the user on
Switch A logs in to Switch B through SFTP, the user can perform file management and transfer
operations on Switch B as a network administrator.

Figure 15 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705443_x_Img_x_png_14_2216075_294551_0.png)

‌

#### Procedure

In the server configuration, the client's
host public key is required. Generate RSA key pairs on the client before
configuring the SFTP server.

**1\.**Configure the SFTP client:

\# Assign an IP address to VLAN-interface 2\.

\<SwitchA\> system-view

\[SwitchA] interface vlan-interface
2

\[SwitchA-Vlan-interface2] ip
address 192.168.0.2 255.255.255.0

\[SwitchA-Vlan-interface2] quit

\# Generate RSA key pairs.

\[SwitchA] public-key local
create rsa

The range of public key size
is (512 \~ 4096).

If the key modulus is greater
than 512, it will take a few minutes.

Press CTRL\+C to abort.

Input the modulus length
\[default \= 1024]:

Generating Keys...

........................\+\+\+\+\+\+

...................\+\+\+\+\+\+

..\+\+\+\+\+\+\+\+

............\+\+\+\+\+\+\+\+

Create the key pair
successfully.

\# Export the host public key to a public key file named pubkey.

\[SwitchA] public-key local
export rsa ssh2 pubkey

\[SwitchA] quit

\# Transmit the public key file pubkey to the
server through FTP or TFTP. (Details not shown.)

**2\.**Configure the SFTP server:

\# Generate RSA key pairs.

\<SwitchB\> system-view

\[SwitchB] public-key local
create rsa

The range of public key size
is (512 \~ 4096).

If the key modulus is greater
than 512, it will take a few minutes.

Press CTRL\+C to abort.

Input the modulus length
\[default \= 1024]:

Generating Keys...

........................\+\+\+\+\+\+

...................\+\+\+\+\+\+

..\+\+\+\+\+\+\+\+

............\+\+\+\+\+\+\+\+

Create the key pair
successfully.

\# Generate a DSA key pair.

\[SwitchB] public-key local
create dsa

The range of public key size
is (512 \~ 2048).

If the key modulus is greater
than 512, it will take a few minutes.

Press CTRL\+C to abort.

Input the modulus length
\[default \= 1024]:

Generating Keys...

.\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\*

........\+......\+.....\+......................................\+

...\+.................\+..........\+...\+

Create the key pair
successfully.

\# Generate an ECDSA key pair.

\[SwitchB] public-key local
create ecdsa secp256r1

Generating Keys...

.

Create the key pair
successfully.

\# Enable the SFTP server.

\[SwitchB] sftp server enable

\# Assign an IP address to VLAN-interface
2\. The SSH client uses this address as the destination for SSH connection.

\[SwitchB] interface vlan-interface
2

\[SwitchB-Vlan-interface2] ip
address 192.168.0.1 255.255.255.0

\[SwitchB-Vlan-interface2] quit

\# Import the peer public key from the public key file pubkey, and name
it switchkey.

\[SwitchB] public-key peer switchkey
import sshkey pubkey

\# Create an SSH user named client001.
Specify the service type as sftp and the authentication method as publickey for the user. Assign the
public key switchkey to the user.

\[SwitchB] ssh user client001
service-type sftp authentication-type publickey assign publickey switchkey

\# Create a local device management user named
client001.

\[SwitchB] local-user client001
class manage

\# Authorize local user client001 to use
the SSH service.

\[SwitchB-luser-manage-client001]
service-type ssh

\# Assign the network-admin user role and working
directory flash:/ to local user client001.

\[SwitchB-luser-manage-client001]
authorization-attribute user-role network-admin work-directory flash:/

\[SwitchB-luser-manage-client001]
quit

**3\.**Establish a connection to the SFTP server:

\# Establish a connection to the SFTP
server and enter SFTP client view.

\<SwitchA\> sftp
192.168.0.1 identity-key rsa

Username: client001

Press CTRL\+C to abort.

Connecting to 192.168.0.1 port
22\.

The server is not
authenticated. Continue? \[Y/N]:y

Do you want to save the server
public key? \[Y/N]:n

sftp\>

\# Display files under the current
directory of the server, delete file z, and verify the result.

sftp\> dir -l

-rwxrwxrwx   1 noone   
nogroup      1759 Aug 23 06:52 config.cfg

-rwxrwxrwx   1 noone   
nogroup       225 Aug 24 08:01 pubkey2

-rwxrwxrwx   1 noone   
nogroup       283 Aug 24 07:39 pubkey

drwxrwxrwx   1 noone   
nogroup         0 Sep 01 06:22 new

-rwxrwxrwx   1 noone   
nogroup       225 Sep 01 06:55 pub

-rwxrwxrwx   1
noone    nogroup         0 Sep 01 08:00 z

sftp\> delete z

Removing /z

sftp\> dir -l

-rwxrwxrwx   1 noone   
nogroup      1759 Aug 23 06:52 config.cfg

-rwxrwxrwx   1 noone   
nogroup       225 Aug 24 08:01 pubkey2

-rwxrwxrwx   1 noone   
nogroup       283 Aug 24 07:39 pubkey

drwxrwxrwx   1 noone   
nogroup         0 Sep 01 06:22 new

-rwxrwxrwx   1 noone   
nogroup       225 Sep 01 06:55 pub

\# Add a directory named new1 and verify the
result.

sftp\> mkdir new1

sftp\> dir -l

-rwxrwxrwx   1 noone   
nogroup      1759 Aug 23 06:52 config.cfg

-rwxrwxrwx   1 noone   
nogroup       225 Aug 24 08:01 pubkey2

-rwxrwxrwx   1 noone   
nogroup       283 Aug 24 07:39 pubkey

drwxrwxrwx   1 noone   
nogroup         0 Sep 01 06:22 new

-rwxrwxrwx   1 noone   
nogroup       225 Sep 01 06:55 pub

drwxrwxrwx   1
noone    nogroup         0 Sep 02 06:30 new1

\# Change the name of directory new1 to new2 and verify
the result.

sftp\> rename new1 new2

sftp\> dir -l

-rwxrwxrwx   1 noone   
nogroup      1759 Aug 23 06:52 config.cfg

-rwxrwxrwx   1 noone   
nogroup       225 Aug 24 08:01 pubkey2

-rwxrwxrwx   1 noone   
nogroup       283 Aug 24 07:39 pubkey

drwxrwxrwx   1 noone   
nogroup         0 Sep 01 06:22 new

-rwxrwxrwx   1 noone   
nogroup       225 Sep 01 06:55 pub

drwxrwxrwx   1
noone    nogroup         0 Sep 02 06:33 new2

\# Download file pubkey2 from the
server and save it as a local file named public.

sftp\> get pubkey2 public

Fetching / pubkey2 to public

/pubkey2                                 
100% 225     1.4KB/s   00:00

\# Upload the local file pu to the
server, save it as puk, and verify the result.

sftp\> put pu puk

Uploading pu to / puk

sftp\> dir -l

-rwxrwxrwx   1 noone   
nogroup      1759 Aug 23 06:52 config.cfg

-rwxrwxrwx   1 noone   
nogroup       225 Aug 24 08:01 pubkey2

-rwxrwxrwx   1 noone   
nogroup       283 Aug 24 07:39 pubkey

drwxrwxrwx   1 noone   
nogroup         0 Sep 01 06:22 new

drwxrwxrwx   1 noone   
nogroup         0 Sep 02 06:33 new2

-rwxrwxrwx   1 noone   
nogroup       283 Sep 02 06:35 pub

-rwxrwxrwx   1
noone    nogroup       283 Sep 02 06:36 puk

sftp\>

\# Exit SFTP client view.

sftp\> quit

\<SwitchA\>

### Example: Configuring SFTP based on 192-bit Suite B algorithms

#### Network configuration

As shown in [Figure 16](#_Ref452974554):

·     Switch B acts as the SFTP Suite B server (SSH2),
and it uses publickey authentication to authenticate the SFTP client.

·     Switch A acts as an SFTP Suite B client (SSH2). After
the user on Switch A logs in to Switch B based on the SFTP Suite B client
software, the user can manage and transfer files on Switch B as an
administrator.

Figure 16 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705444_x_Img_x_png_15_2216075_294551_0.png)

‌

#### Procedure

**1\.**Generate the client's certificate and the
server's certificate. (Details not shown.)

You must first configure the certificates
of the server and the client because they are required for identity
authentication between the two parties.

In this example, the server's certificate
file is ssh-server-ecdsa384.p12 and the client's certificate file is ssh-client-ecdsa384.p12.

**2\.**Configure the SFTP client:

You can modify the pkix version of the
client software OpenSSH to support Suite B. This example uses an H3C switch as
an SFTP client.

\# Upload the server's certificate file ssh-server-ecdsa384.p12 and the client's certificate file ssh-client-ecdsa384.p12 to the SFTP
client through FTP or TFTP. (Details not shown.)

\# Create a PKI domain named server384 for
verifying the server's certificate and enter its view.

\<SwitchA\> system-view

\[SwitchA] pki domain server384

\# Disable CRL checking.

\[SwitchA-pki-domain-server384]
undo crl check enable

\[SwitchA-pki-domain-server384]
quit

\# Import local certificate file ssh-server-ecdsa384.p12 to PKI domain server384.

\[SwitchA] pki import domain server384
p12 local filename ssh-server-ecdsa384.p12

The system is going to save
the key pair. You must specify a key pair name, which is a case-insensitive
string of 1 to 64 characters. Valid characters include a to z, A to Z, 0 to 9,
and hyphens (-).

Please enter the key pair
name\[default name: server384]:

\# Display information about local certificates
in PKI domain server384.

\[SwitchA] display pki
certificate domain server384 local

Certificate:

    Data:

        Version: 3 (0x2)

        Serial Number: 1 (0x1)

    Signature Algorithm:
ecdsa-with-SHA384

        Issuer: C\=CN,
ST\=Beijing, L\=Beijing, O\=H3C, OU\=Software, CN\=SuiteB CA

        Validity

            Not Before: Aug 20
10:08:41 2015 GMT

            Not After : Aug 19
10:08:41 2016 GMT

        Subject: C\=CN,
ST\=Beijing, O\=H3C, OU\=Software, CN\=ssh server

        Subject Public Key
Info:

            Public Key
Algorithm: id-ecPublicKey

                Public-Key:
(384 bit)

                pub:

                   
04:4a:33:e5:99:8d:49:45:a7:a3:24:7b:32:6a:ed:

                   
b6:36:e1:4d:cc:8c:05:22:f4:3a:7c:5d:b7:be:d1:

                   
e6:9e:f0:ce:95:39:ca:fd:a0:86:cd:54:ab:49:60:

                   
10:be:67:9f:90:3a:18:e2:7d:d9:5f:72:27:09:e7:

                    bf:7e:64:0a:59:bb:b3:7d:ae:88:14:94:45:b9:34:

                   
d2:f3:93:e1:ba:b4:50:15:eb:e5:45:24:31:10:c7:

                   
07:01:f9:dc:a5:6f:81

                ASN1 OID:
secp384r1

                NIST CURVE:
P-384

        X509v3 extensions:

            X509v3 Basic
Constraints:

                CA:FALSE

            Netscape Comment:

                OpenSSL
Generated Certificate

            X509v3 Subject Key
Identifier:

               
10:16:64:2C:DA:C1:D1:29:CD:C0:74:40:A9:70:BD:62:8A:BB:F4:D5

            X509v3 Authority
Key Identifier:

               
keyid:5A:BE:85:49:16:E5:EB:33:80:25:EB:D8:91:50:B4:E6:3E:4F:B8:22

 

    Signature Algorithm:
ecdsa-with-SHA384

        
30:65:02:31:00:80:50:7a:4f:c5:cd:6a:c3:57:13:7f:e9:da:

         c1:72:7f:45:30:17:c2:a7:d3:ec:73:3d:5f:4d:e3:96:f6:a3:

        
33:fb:e4:b9:ff:47:f1:af:9d:e3:03:d2:24:53:40:09:5b:02:

        
30:45:d1:bf:51:fd:da:22:11:90:03:f9:d4:05:ec:d6:7c:41:

        
fc:9d:a1:fd:5b:8c:73:f8:b6:4c:c3:41:f7:c6:7f:2f:05:2d:

         37:f8:52:52:26:99:28:97:ac:6e:f9:c7:01

\# Create a PKI domain named client384 for
the client's certificate and enter its view.

\[SwitchA] pki domain client384

\# Disable CRL checking.

\[SwitchA-pki-domain-client384]
undo crl check enable

\[SwitchA-pki-domain-client384]
quit

\# Import local certificate file ssh-client-ecdsa384.p12 to PKI domain client384.

\[SwitchA] pki import domain
client384 p12 local filename ssh-client-ecdsa384.p12

The system is going to save
the key pair. You must specify a key pair name, which is a case-insensitive
string of 1 to 64 characters. Valid characters include a to z, A to Z, 0 to 9,
and hyphens (-).

Please enter the key pair
name\[default name: client384]:

\# Display information about local certificates
in PKI domain client384.

\[SwitchA]display pki
certificate domain client384 local

Certificate:

    Data:

        Version: 3 (0x2)

        Serial Number: 2 (0x2)

    Signature Algorithm:
ecdsa-with-SHA384

        Issuer: C\=CN,
ST\=Beijing, L\=Beijing, O\=H3C, OU\=Software, CN\=SuiteB CA

        Validity

            Not Before: Aug 20
10:10:59 2015 GMT

            Not After : Aug 19
10:10:59 2016 GMT

        Subject: C\=CN,
ST\=Beijing, O\=H3C, OU\=Software, CN\=ssh client

        Subject Public Key
Info:

            Public Key
Algorithm: id-ecPublicKey

                Public-Key:
(384 bit)

                pub:

                   
04:85:7c:8b:f4:7a:36:bf:74:f6:7c:72:f9:08:69:

                   
d0:b9:ac:89:98:17:c9:fc:89:94:43:da:9a:a6:89:

                   
41:d3:72:24:9b:9a:29:a8:d1:ba:b4:e5:77:ba:fc:

                    df:ae:c6:dd:46:72:ab:bc:d1:7f:18:7d:54:88:f6:

                   
b4:06:54:7e:e7:4d:49:b4:07:dc:30:54:4b:b6:5b:

                   
01:10:51:6b:0c:6d:a3:b1:4b:c9:d9:6c:d6:be:13:

                   
91:70:31:2a:92:00:76

                ASN1 OID:
secp384r1

                NIST CURVE:
P-384

        X509v3 extensions:

            X509v3 Basic
Constraints:

                CA:FALSE

            Netscape Comment:

                OpenSSL
Generated Certificate

            X509v3 Subject Key
Identifier:

               
BD:5F:8E:4F:7B:FE:74:03:5A:D1:94:DB:CA:A7:82:D6:F7:78:A1:B0

            X509v3 Authority
Key Identifier:

               
keyid:5A:BE:85:49:16:E5:EB:33:80:25:EB:D8:91:50:B4:E6:3E:4F:B8:22

 

    Signature Algorithm:
ecdsa-with-SHA384

        
30:66:02:31:00:d2:06:fa:2c:0b:0d:f0:81:90:01:c3:3d:bf:

        
97:b3:79:d8:25:a0:e2:0e:ed:00:c9:48:3e:c9:71:43:c9:b4:

        
2a:a6:0a:27:80:9e:d4:0f:f2:db:db:5b:40:b1:a9:0a:e4:02:

        
31:00:ee:00:e1:07:c0:2f:12:3f:88:ea:fe:19:05:ef:56:ca:

        
33:71:75:5e:11:c9:a6:51:4b:3e:7c:eb:2a:4d:87:2b:71:7c:

        
30:64:fe:14:ce:06:d5:0a:e2:cf:9a:69:19:ff

\# Assign an IP address to VLAN-interface
2\.

\[SwitchA] interface vlan-interface
2

\[SwitchA-Vlan-interface2] ip
address 192.168.0.2 255.255.255.0

\[SwitchA-Vlan-interface2] quit

\[SwitchA] quit

**3\.**Configure the SFTP server:

\# Upload the server's certificate file ssh-server-ecdsa384.p12 and the client's certificate file ssh-client-ecdsa384.p12 to the SFTP
server through FTP or TFTP. (Details not shown.)

\# Create a PKI domain named client384 for
verifying the client's certificate and import the file of the client's
certificate to this domain. (Details not shown.)

\# Create a PKI domain named server384 for
the server's certificate and import the file of the server's certificate to
this domain. (Details not shown.)

\# Specify Suite B algorithms for
algorithm negotiation.

\[SwitchB] ssh2 algorithm
key-exchange ecdh-sha2-nistp384

\[SwitchB] ssh2 algorithm
cipher aes256-gcm

\[SwitchB] ssh2 algorithm
public-key x509v3-ecdsa-sha2-nistp384

\# Specify server384 as the PKI domain of the
server's certificate.

\[SwitchB] ssh server
pki-domain server384

\# Enable the SFTP server.

\[SwitchB] sftp server enable

\# Assign an IP address to VLAN-interface
2\.

\[SwitchB] interface vlan-interface
2

\[SwitchB-Vlan-interface2] ip
address 192.168.0.1 255.255.255.0

\[SwitchB-Vlan-interface2] quit

\# Set the authentication mode to AAA for
user lines.

\[SwitchB] line vty 0 63

\[SwitchB-line-vty0-63]
authentication-mode scheme

\[SwitchB-line-vty0-63] quit

\# Create a local device management user named
client001. Authorize the user to use the SSH service and assign the network-admin
user role to the user.

\[SwitchB] local-user client001
class manage

\[SwitchB-luser-manage-client001]
service-type ssh

\[SwitchB-luser-manage-client001]
authorization-attribute user-role network-admin

\[SwitchB-luser-manage-client001]
quit

\# Create an SSH user named client001.
Specify the publickey authentication method for the user and specify client384 as the
PKI domain for verifying the client's certificate.

\[SwitchB] ssh user client001
service-type sftp authentication-type publickey assign pki-domain client384

**4\.**Establish an SFTP connection to the SFTP
server based on the 192-bit Suite B algorithms:

\# Establish an SFTP connection to the
server at 192.168.0.1.

\<SwitchA\> sftp 192.168.0.1
suite-b 192-bit pki-domain client384 server-pki-domain server384

Username: client001

Press CTRL\+C to abort.

Connecting to 192.168.0.1 port
22\.

sftp\>

## SCP configuration examples

Unless otherwise noted, devices in the
configuration examples operate in non-FIPS mode.

When the device acts as an SCP server
operating in FIPS mode, only ECDSA and RSA key pairs are supported. Do not
generate a DSA key pair on the SCP server.

### Example: Configuring SCP with password authentication

#### Network configuration

As shown in [Figure 17](#_Ref309755611):

·     Switch B acts as the SCP server and uses
password authentication to authenticate the SCP client. The client's username
and password are saved on Switch B.

·     Switch A acts as the SCP client. After the user on Switch A logs in to Switch B through SCP, the user can transfer files between switches as a network administrator.

Figure 17 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705445_x_Img_x_png_16_2216075_294551_0.png)

‌

#### Procedure

**1\.**Configure the SCP server:

\# Generate RSA key pairs.

\<SwitchB\> system-view

\[SwitchB] public-key local
create rsa

The range of public key
modulus is (512 \~ 4096).

If the key modulus is greater
than 512, it will take a few minutes.

Press CTRL\+C to abort.

Input the modulus length
\[default \= 1024]:

Generating Keys...

........................\+\+\+\+\+\+

...................\+\+\+\+\+\+

..\+\+\+\+\+\+\+\+

............\+\+\+\+\+\+\+\+

Create the key pair
successfully.

\# Generate a DSA key pair.

\[SwitchB] public-key local
create dsa

The range of public key
modulus is (512 \~ 2048).

If the key modulus is greater
than 512, it will take a few minutes.

Press CTRL\+C to abort.

Input the modulus length
\[default \= 1024]:

Generating Keys...

.\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\*

........\+......\+.....\+......................................\+

...\+.................\+..........\+...\+.

Create the key pair
successfully.

\# Generate an ECDSA key pair.

\[SwitchB] public-key local
create ecdsa secp256r1

Generating Keys...

.

Create the key pair
successfully.

\# Enable the SCP server.

\[SwitchB] scp server enable

\# Configure an IP address for
VLAN-interface 2\. The client uses this address as the destination for SCP
connection.

\[SwitchB] interface vlan-interface
2

\[SwitchB-Vlan-interface2] ip
address 192.168.0.1 255.255.255.0

\[SwitchB-Vlan-interface2] quit

\# Create a local device management user named
client001.

\[SwitchB] local-user client001
class manage

\# Set a password for local user client001.

\[SwitchB-luser-manage-client001]
password simple 123456TESTplat\&!

\# Authorize local user client001 to use
the SSH service.

\[SwitchB-luser-manage-client001]
service-type ssh

\# Assign the network-admin user role to local user
client001.

\[SwitchB-luser-manage-client001]
authorization-attribute user-role network-admin

\[SwitchB-luser-manage-client001]
quit

\# Create an SSH user named client001.
Specify the service type as scp and the authentication method as password for the user.

\[SwitchB] ssh user client001
service-type scp authentication-type password

**2\.**Configure an IP address for VLAN-interface 2
on the SCP client.

\<SwitchA\> system-view

\[SwitchA] interface vlan-interface
2

\[SwitchA-Vlan-interface2] ip
address 192.168.0.2 255.255.255.0

\[SwitchA-Vlan-interface2] quit

\[SwitchA] quit

**3\.**Connect to the SCP server, download file remote.bin from the server, and save it as a local file
named local.bin.

\<SwitchA\> scp 192.168.0.1
get remote.bin local.bin

Username: client001

Press CTRL\+C to abort.

Connecting to 192.168.0.1 port
22\.

The server is not
authenticated. Continue? \[Y/N]:y

Do you want to save the server
public key? \[Y/N]:n

[\[email protected]](/cdn-cgi/l/email-protection)’s
password:

remote.bin                                      
100% 2875     2.8KB/s   00:00

### Example: Configuring SCP file transfer with a Linux SCP client

#### Network configuration

As shown in [Figure 18](#_Ref77517581), the
switch acts as the SCP server and uses password authentication to authenticate
the SCP client. The client's username and password are saved on the switch.

The device acts as the SCP client. After
the user on the device logs in to the switch through SCP, the user can transfer
files with the switch as a network administrator.

Figure 18 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705446_x_Img_x_png_17_2216075_294551_0.png)

‌

#### Procedure

**1\.**Configure the SCP server:

\# Generate an RSA key pair.

\<Switch\> system-view

\[Switch] public-key local
create rsa

The range of public key
modulus is (512 \~ 4096).

If the key modulus is greater
than 512, it will take a few minutes.

Press CTRL\+C to abort.

Input the modulus length
\[default \= 1024]:

Generating Keys...

........................\+\+\+\+\+\+

...................\+\+\+\+\+\+

..\+\+\+\+\+\+\+\+

............\+\+\+\+\+\+\+\+

Create the key pair
successfully.

\# Generate a DSA key pair.

\[Switch] public-key local
create dsa

The range of public key
modulus is (512 \~ 2048).

If the key modulus is greater
than 512, it will take a few minutes.

Press CTRL\+C to abort.

Input the modulus length
\[default \= 1024]:

Generating Keys...

.\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\*

........\+......\+.....\+......................................\+

...\+.................\+..........\+...\+.

Create the key pair
successfully.

\# Generate an ECDSA key pair.

\[Switch] public-key local
create ecdsa secp256r1

Generating Keys...

.

Create the key pair successfully.

\# Enable the SCP server.

\[Switch] scp server enable

\# Configure an IP address for VLAN
interface 2\. The SCP client uses this IP address as the destination address of
the SCP connection.

\[Switch] interface vlan-interface
2

\[Switch-Vlan-interface2] ip
address 192.168.0.1 255.255.255.0

\[Switch-Vlan-interface2] quit

\# Create a local device management user named
client001. Set a password for the user, authorize the user to use the SSH
service, assign the network-admin user role to the user, and specify the working directory as flash:/.

\[Switch] local-user client001
class manage

\[Switch-luser-manage-client001]
password simple 123456TESTplat\&!

\[Switch-luser-manage-client001]
service-type ssh

\[Switch-luser-manage-client001]
authorization-attribute user-role network-admin work-directory flash:/

\[Switch-luser-manage-client001]
quit

\# Configure an SSH user named client001,
specify the SCP service for the SSH user, and set the authentication method as
password. (This step is optional.)

\[Switch] ssh user client001
service-type scp authentication-type password

**2\.**Configure the SCP client:

The following uses the SCP client running
on a Linux system as an example:

\# Connect to the SCP server, download the
remote.bin file from the server, and save it as a local file named local.bin.

admin@device:\~# scp [\[email protected]](/cdn-cgi/l/email-protection):remote.bin
local.bin

[\[email protected]](/cdn-cgi/l/email-protection)'s
password:

remote.bin                                                                  
100%   15

     0.0KB/s   00:00

### Example: Configuring SCP based on Suite B algorithms

#### Network configuration

As shown in [Figure 19](#_Ref435026049):

·     Switch B acts as the SCP Suite B server (SSH2), and
it uses publickey authentication to authenticate the SCP client.

·     Switch A acts as an SCP Suite B client (SSH2). After
the user on Switch A logs in to Switch B through the SCP Suite B client
software, the user can transfer files between switches as a network
administrator.

Figure 19 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705447_x_Img_x_png_18_2216075_294551_0.png)

‌

#### Procedure

**1\.**Generate the client's certificates and the
server's certificates. (Details not shown.)

You must first configure the certificates
of the server and the client because they are required for identity
authentication between the two parties.

In this example, the server's certificate
files are ssh-server-ecdsa256.p12 and ssh-server-ecdsa384.p12. The client's certificate files are ssh-client-ecdsa256.p12 and ssh-client-ecdsa384.p12.

**2\.**Configure the SCP client:

You can modify the pkix version of the
client software OpenSSH to support Suite B. This example uses an H3C switch as
an SCP client.

\# Upload the server's certificate files (ssh-server-ecdsa256.p12 and ssh-server-ecdsa384.p12) and the client's certificate files (ssh-client-ecdsa256.p12 and ssh-client-ecdsa384.p12) to the SCP client through FTP or TFTP. (Details not shown.)

\# Create a PKI domain named server256 for
verifying the server's certificate ecdsa256 and enter its view.

\<SwitchA\> system-view

\[SwitchA] pki domain server256

\# Disable CRL checking.

\[SwitchA-pki-domain-server256]
undo crl check enable

\[SwitchA-pki-domain-server256]
quit

\# Import local certificate file ssh-server-ecdsa256.p12 to PKI domain server256.

\[SwitchA] pki import domain server256
p12 local filename ssh-server-ecdsa256.p12

The system is going to save
the key pair. You must specify a key pair name, which is a case-insensitive
string of 1 to 64 characters. Valid characters include a to z, A to Z, 0 to 9,
and hyphens (-).

Please enter the key pair
name\[default name: server256]:

\# Display information about local certificates
in PKI domain server256.

\[SwitchA] display pki
certificate domain server256 local

Certificate:

    Data:

        Version: 3 (0x2)

        Serial Number: 3 (0x3)

    Signature Algorithm:
ecdsa-with-SHA256

        Issuer: C\=CN,
ST\=Beijing, L\=Beijing, O\=H3C, OU\=Software, CN\=SuiteB CA

        Validity

            Not Before: Aug 21
08:39:51 2015 GMT

            Not After : Aug 20
08:39:51 2016 GMT

        Subject: C\=CN,
ST\=Beijing, O\=H3C, OU\=Software, CN\=SSH Server secp256

        Subject Public Key
Info:

            Public Key
Algorithm: id-ecPublicKey

                Public-Key:
(256 bit)

                pub:

                   
04:a2:b4:b4:66:1e:3b:d5:50:50:0e:55:19:8d:52:

                   
6d:47:8c:3d:3d:96:75:88:2f:9a:ba:a2:a7:f9:ef:

                   
0a:a9:20:b7:b6:6a:90:0e:f8:c6:de:15:a2:23:81:

                   
3c:9e:a2:b7:83:87:b9:ad:28:c8:2a:5e:58:11:8e:

                   
c7:61:4a:52:51

                ASN1 OID:
prime256v1

                NIST CURVE:
P-256

        X509v3 extensions:

            X509v3 Basic
Constraints:

                CA:FALSE

            Netscape Comment:

                OpenSSL
Generated Certificate

            X509v3 Subject Key
Identifier:

               
08:C1:F1:AA:97:45:19:6A:DA:4A:F2:87:A1:1A:E8:30:BD:31:30:D7

            X509v3 Authority
Key Identifier:

               
keyid:5A:BE:85:49:16:E5:EB:33:80:25:EB:D8:91:50:B4:E6:3E:4F:B8:22

 

    Signature Algorithm:
ecdsa-with-SHA256

        
30:65:02:31:00:a9:16:e9:c1:76:f0:32:fc:4b:f9:8f:b6:7f:

        
31:a0:9f:de:a7:cc:33:29:27:2c:71:2e:f9:0d:74:cb:25:c9:

        
00:d2:52:18:7f:58:3f:cc:7e:8b:d3:42:65:00:cb:63:f8:02:

        
30:01:a2:f6:a1:51:04:1c:61:78:f6:6b:7e:f9:f9:42:8d:7c:

        
a7:bb:47:7c:2a:85:67:0d:81:12:0b:02:98:bc:06:1f:c1:3c:

        
9b:c2:1b:4c:44:38:5a:14:b2:48:63:02:2b

\# Create a PKI domain named client256 for
the client's certificate ecdsa256 and enter its view.

\[SwitchA] pki domain client256

\# Disable CRL checking.

\[SwitchA-pki-domain-client256]
undo crl check enable

\[SwitchA-pki-domain-client256]
quit

\# Import local certificate file ssh-client-ecdsa256.p12 to PKI domain client256.

\[SwitchA] pki import domain
client256 p12 local filename ssh-client-ecdsa256.p12

The system is going to save
the key pair. You must specify a key pair name, which is a case-insensitive
string of 1 to 64 characters. Valid characters include a to z, A to Z, 0 to 9,
and hyphens (-).

Please enter the key pair
name\[default name: client256]:

\# Display information about local certificates
in PKI domain client256.

\[SwitchA] display pki
certificate domain client256 local

Certificate:

    Data:

        Version: 3 (0x2)

        Serial Number: 4 (0x4)

    Signature Algorithm:
ecdsa-with-SHA256

        Issuer: C\=CN,
ST\=Beijing, L\=Beijing, O\=H3C, OU\=Software, CN\=SuiteB CA

        Validity

            Not Before: Aug 21
08:41:09 2015 GMT

            Not After : Aug 20
08:41:09 2016 GMT

        Subject: C\=CN,
ST\=Beijing, O\=H3C, OU\=Software, CN\=SSH Client secp256

        Subject Public Key
Info:

            Public Key
Algorithm: id-ecPublicKey

                Public-Key:
(256 bit)

                pub:

                   
04:da:e2:26:45:87:7a:63:20:e7:ca:7f:82:19:f5:

                   
96:88:3e:25:46:f8:2f:9a:4c:70:61:35:db:e4:39:

                   
b8:38:c4:60:4a:65:28:49:14:32:3c:cc:6d:cd:34:

                   
29:83:84:74:a7:2d:0e:75:1c:c2:52:58:1e:22:16:

                    12:d0:b4:8a:92

                ASN1 OID:
prime256v1

                NIST CURVE:
P-256

        X509v3 extensions:

            X509v3 Basic
Constraints:

                CA:FALSE

            Netscape Comment:

                OpenSSL
Generated Certificate

            X509v3 Subject Key
Identifier:

               
1A:61:60:4D:76:40:B8:BA:5D:A1:3C:60:BC:57:98:35:20:79:80:FC

            X509v3 Authority
Key Identifier:

               
keyid:5A:BE:85:49:16:E5:EB:33:80:25:EB:D8:91:50:B4:E6:3E:4F:B8:22

 

    Signature Algorithm:
ecdsa-with-SHA256

        
30:66:02:31:00:9a:6d:fd:7d:ab:ae:54:9a:81:71:e6:bb:ad:

        
5a:2e:dc:1d:b3:8a:bf:ce:ee:71:4e:8f:d9:93:7f:a3:48:a1:

        
5c:17:cb:22:fa:8f:b3:e5:76:89:06:9f:96:47:dc:34:87:02:

        
31:00:e3:af:2a:8f:d6:8d:1f:3a:2b:ae:2f:97:b3:52:63:b6:

        
18:67:70:2c:93:2a:41:c0:e7:fa:93:20:09:4d:f4:bf:d0:11:

        
66:0f:48:56:01:1e:c3:be:37:4e:49:19:cf:c6

\# Create a PKI domain named server384 for
verifying the server's certificate ecdsa384 and enter its view.

\[SwitchA] pki domain server384

\# Disable CRL checking.

\[SwitchA-pki-domain-server384]
undo crl check enable

\[SwitchA-pki-domain-server384]
quit

\# Import local certificate file ssh-server-ecdsa384.p12 to PKI domain server384.

\[SwitchA] pki import domain server384
p12 local filename ssh-server-ecdsa384.p12

The system is going to save
the key pair. You must specify a key pair name, which is a case-insensitive
string of 1 to 64 characters. Valid characters include a to z, A to Z, 0 to 9,
and hyphens (-).

Please enter the key pair
name\[default name: server384]:

\# Display information about local certificates
in PKI domain server384.

\[SwitchA] display pki
certificate domain server384 local

Certificate:

    Data:

        Version: 3 (0x2)

        Serial Number: 1 (0x1)

    Signature Algorithm:
ecdsa-with-SHA384

        Issuer: C\=CN,
ST\=Beijing, L\=Beijing, O\=H3C, OU\=Software, CN\=SuiteB CA

        Validity

            Not Before: Aug 20
10:08:41 2015 GMT

            Not After : Aug 19
10:08:41 2016 GMT

        Subject: C\=CN, ST\=Beijing,
O\=H3C, OU\=Software, CN\=ssh server

        Subject Public Key
Info:

            Public Key
Algorithm: id-ecPublicKey

                Public-Key:
(384 bit)

                pub:

                   
04:4a:33:e5:99:8d:49:45:a7:a3:24:7b:32:6a:ed:

                    b6:36:e1:4d:cc:8c:05:22:f4:3a:7c:5d:b7:be:d1:

                   
e6:9e:f0:ce:95:39:ca:fd:a0:86:cd:54:ab:49:60:

                   
10:be:67:9f:90:3a:18:e2:7d:d9:5f:72:27:09:e7:

                   
bf:7e:64:0a:59:bb:b3:7d:ae:88:14:94:45:b9:34:

                   
d2:f3:93:e1:ba:b4:50:15:eb:e5:45:24:31:10:c7:

                   
07:01:f9:dc:a5:6f:81

                ASN1 OID:
secp384r1

                NIST CURVE:
P-384

        X509v3 extensions:

            X509v3 Basic
Constraints:

                CA:FALSE

            Netscape Comment:

                OpenSSL
Generated Certificate

            X509v3 Subject Key
Identifier:

               
10:16:64:2C:DA:C1:D1:29:CD:C0:74:40:A9:70:BD:62:8A:BB:F4:D5

            X509v3 Authority
Key Identifier:

                keyid:5A:BE:85:49:16:E5:EB:33:80:25:EB:D8:91:50:B4:E6:3E:4F:B8:22

 

    Signature Algorithm:
ecdsa-with-SHA384

        
30:65:02:31:00:80:50:7a:4f:c5:cd:6a:c3:57:13:7f:e9:da:

        
c1:72:7f:45:30:17:c2:a7:d3:ec:73:3d:5f:4d:e3:96:f6:a3:

         33:fb:e4:b9:ff:47:f1:af:9d:e3:03:d2:24:53:40:09:5b:02:

        
30:45:d1:bf:51:fd:da:22:11:90:03:f9:d4:05:ec:d6:7c:41:

        
fc:9d:a1:fd:5b:8c:73:f8:b6:4c:c3:41:f7:c6:7f:2f:05:2d:

        
37:f8:52:52:26:99:28:97:ac:6e:f9:c7:01

\# Create a PKI domain named client384 for
the client's certificate ecdsa384 and enter its view.

\[SwitchA] pki domain client384

\# Disable CRL checking.

\[SwitchA-pki-domain-client384]
undo crl check enable

\[SwitchA-pki-domain-client384]
quit

\# Import local certificate file ssh-client-ecdsa384.p12 to PKI domain client384.

\[SwitchA] pki import domain
client384 p12 local filename ssh-client-ecdsa384.p12

The system is going to save
the key pair. You must specify a key pair name, which is a case-insensitive
string of 1 to 64 characters. Valid characters include a to z, A to Z, 0 to 9,
and hyphens (-).

Please enter the key pair
name\[default name: client384]:

\# Display information about local certificates
in PKI domain client384.

\[SwitchA] display pki
certificate domain client384 local

Certificate:

    Data:

        Version: 3 (0x2)

        Serial Number: 2 (0x2)

    Signature Algorithm:
ecdsa-with-SHA384

        Issuer: C\=CN,
ST\=Beijing, L\=Beijing, O\=H3C, OU\=Software, CN\=SuiteB CA

        Validity

            Not Before: Aug 20
10:10:59 2015 GMT

            Not After : Aug 19
10:10:59 2016 GMT

        Subject: C\=CN,
ST\=Beijing, O\=H3C, OU\=Software, CN\=ssh client

        Subject Public Key
Info:

            Public Key
Algorithm: id-ecPublicKey

                Public-Key:
(384 bit)

                pub:

                   
04:85:7c:8b:f4:7a:36:bf:74:f6:7c:72:f9:08:69:

                   
d0:b9:ac:89:98:17:c9:fc:89:94:43:da:9a:a6:89:

                   
41:d3:72:24:9b:9a:29:a8:d1:ba:b4:e5:77:ba:fc:

                   
df:ae:c6:dd:46:72:ab:bc:d1:7f:18:7d:54:88:f6:

                   
b4:06:54:7e:e7:4d:49:b4:07:dc:30:54:4b:b6:5b:

                   
01:10:51:6b:0c:6d:a3:b1:4b:c9:d9:6c:d6:be:13:

                   
91:70:31:2a:92:00:76

                ASN1 OID:
secp384r1

                NIST CURVE:
P-384

        X509v3 extensions:

            X509v3 Basic
Constraints:

                CA:FALSE

            Netscape Comment:

                OpenSSL
Generated Certificate

            X509v3 Subject Key
Identifier:

                BD:5F:8E:4F:7B:FE:74:03:5A:D1:94:DB:CA:A7:82:D6:F7:78:A1:B0

            X509v3 Authority
Key Identifier:

               
keyid:5A:BE:85:49:16:E5:EB:33:80:25:EB:D8:91:50:B4:E6:3E:4F:B8:22

 

    Signature Algorithm:
ecdsa-with-SHA384

         30:66:02:31:00:d2:06:fa:2c:0b:0d:f0:81:90:01:c3:3d:bf:

        
97:b3:79:d8:25:a0:e2:0e:ed:00:c9:48:3e:c9:71:43:c9:b4:

        
2a:a6:0a:27:80:9e:d4:0f:f2:db:db:5b:40:b1:a9:0a:e4:02:

        
31:00:ee:00:e1:07:c0:2f:12:3f:88:ea:fe:19:05:ef:56:ca:

         33:71:75:5e:11:c9:a6:51:4b:3e:7c:eb:2a:4d:87:2b:71:7c:

        
30:64:fe:14:ce:06:d5:0a:e2:cf:9a:69:19:ff

\# Assign an IP address to VLAN-interface
2\.

\[SwitchA] interface vlan-interface
2

\[SwitchA-Vlan-interface2] ip
address 192.168.0.2 255.255.255.0

\[SwitchA-Vlan-interface2] quit

**3\.**Configure the SCP server:

\# Upload the server's certificate files (ssh-server-ecdsa256.p12 and ssh-server-ecdsa384.p12) and the client's certificate files (ssh-client-ecdsa256.p12 and ssh-client-ecdsa384.p12) to the SCP server through FTP or TFTP. (Details not shown.)

\# Create a PKI domain named client256 for
verifying the client's certificate ecdsa256 and import the file of this
certificate to this domain. Create a PKI domain named server256 for
the server's certificate ecdsa256 and import the file of this certificate to this domain. (Details not
shown.)

\# Create a PKI domain named client384 for
verifying the client's certificate ecdsa384 and import the file of this
certificate to this domain. Create a PKI domain named server384 for
the server's certificate ecdsa384 and import the file of this certificate to this domain. (Details not
shown.)

\# Specify Suite B algorithms for
algorithm negotiation.

\<SwitchB\> system-view

\[SwitchB] ssh2 algorithm
key-exchange ecdh-sha2-nistp256 ecdh-sha2-nistp384

\[SwitchB] ssh2 algorithm
cipher aes128-gcm aes256-gcm

\[SwitchB] ssh2 algorithm
public-key x509v3-ecdsa-sha2-nistp256 x509v3-ecdsa-sha2-nistp384

\# Enable the SCP server.

\[SwitchB] scp server enable

\# Assign an IP address to VLAN-interface
2\.

\[SwitchB] interface vlan-interface
2

\[SwitchB-Vlan-interface2] ip
address 192.168.0.1 255.255.255.0

\[SwitchB-Vlan-interface2] quit

\# Set the authentication mode to AAA for
user lines.

\[SwitchB] line vty 0 63

\[SwitchB-line-vty0-63]
authentication-mode scheme

\[SwitchB-line-vty0-63] quit

\# Create a local device management user
named client001. Authorize the user to use the SSH service and assign the network-admin
user role to the user.

\[SwitchB] local-user client001
class manage

\[SwitchB-luser-manage-client001]
service-type ssh

\[SwitchB-luser-manage-client001]
authorization-attribute user-role network-admin

\[SwitchB-luser-manage-client001]
quit

\# Create a local device management user
named client002. Authorize the user to use the SSH service and assign the network-admin user
role to the user.

\[SwitchB] local-user client002
class manage

\[SwitchB-luser-manage-client002]
service-type ssh

\[SwitchB-luser-manage-client002]
authorization-attribute user-role network-admin

\[SwitchB-luser-manage-client002]
quit

**4\.**Establish an SCP connection to the SCP
server:

¡     Based
on the 128-bit Suite B algorithms:

\# Specify server256 as the PKI domain of the
server's certificate.

\[SwitchB]ssh server pki-domain
server256

\# Create an SSH user client001.
Specify the publickey authentication method for the user and specify client256 as the
PKI domain for verifying the client's certificate.

\[SwitchB] ssh user client001
service-type scp authentication-type publickey assign pki-domain client256

\# Establish an SCP connection to the SCP
server at 192.168.0.1 based on the 128-bit Suite B algorithms.

\<SwitchA\> scp 192.168.0.1
get src.cfg suite-b 128-bit pki-domain client256 server-pki

-domain server256

Username: client001

Press CTRL\+C to abort.

Connecting to 192.168.0.1 port
22\.

src.cfg                                      
100% 4814     4.7KB/s   00:00

\<SwitchA\>

¡     Based
on the 192-bit Suite B algorithms:

\# Specify server384 as the PKI domain of the
server's certificate.

\[SwitchB] ssh server
pki-domain server384

\# Create an SSH user client002.
Specify the publickey authentication method for the user and specify client384 as the
PKI domain for verifying the client's certificate.

\[Switch] ssh user client002
service-type scp authentication-type publickey assign pki-domain client384

\# Establish an SCP connection to the SCP
server at 192.168.0.1 based on the 192-bit Suite B algorithms.

\<SwitchA\> scp 192.168.0.1
get src.cfg suite-b 192-bit pki-domain client384 server-pki

-domain server384

Username: client002

Press CTRL\+C to abort.

Connecting to 192.168.0.1 port
22\.

src.cfg                                      
100% 4814     4.7KB/s   00:00

\<SwitchA\>

## NETCONF over SSH configuration examples

Unless otherwise noted, devices in the
configuration examples are in non-FIPS mode.

When the device acts as a NETCONF-over-SSH
server operating in FIPS mode, only ECDSA and RSA key pairs are supported. Do
not generate a DSA key pair on the NETCONF-over-SSH server.

### Example: Configuring NETCONF over SSH with password authentication

#### Network configuration

As shown in [Figure 20](#_Ref392695568):

·     The switch acts as the NETCONF-over-SSH server
and uses password authentication to authenticate the client. The client's
username and password are saved on the switch.

·     The host acts as the NETCONF-over-SSH client, using SSH2 client software.
After the user on the host logs
in to the switch through NETCONF
over SSH, the user can perform NETCONF operations on the switch as a network administrator.

Figure 20 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705448_x_Img_x_png_19_2216075_294551_0.png)

‌

#### Procedure

\# Generate RSA key pairs.

\<Switch\> system-view

\[Switch] public-key local create rsa

The range of public key modulus is (512
\~ 4096).

If the key modulus is greater than
512, it will take a few minutes.

Press CTRL\+C to abort.

Input the modulus length \[default \=
1024]:

Generating Keys...

........................\+\+\+\+\+\+

...................\+\+\+\+\+\+

..\+\+\+\+\+\+\+\+

............\+\+\+\+\+\+\+\+

Create the key pair successfully.

\# Generate a DSA key pair.

\[Switch] public-key local create dsa

The range of public key modulus is
(512 \~ 2048).

If the key modulus is greater than
512, it will take a few minutes.

Press CTRL\+C to abort.

Input the modulus length \[default \=
1024]:

Generating Keys...

.\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\+\*

........\+......\+.....\+......................................\+

...\+.................\+..........\+...\+.

Create the key pair successfully.

\# Generate an ECDSA key pair.

\[Switch] public-key local create
ecdsa secp256r1

Generating Keys...

.

Create the key pair successfully.

\# Enable NETCONF over SSH.

\[Switch] netconf ssh server enable

\# Configure an IP address for
VLAN-interface 2\. The client uses this address as the destination for
NETCONF-over-SSH connection.

\[Switch] interface vlan-interface 2

\[Switch-Vlan-interface2] ip address 192.168.1.40
255.255.255.0

\[Switch-Vlan-interface2] quit

\# Set the authentication mode to AAA for
user lines.

\[Switch] line vty 0 63

\[Switch-line-vty0-63] authentication-mode
scheme

\[Switch-line-vty0-63] quit

\# Create a local device management user named
client001.

\[Switch] local-user client001 class
manage

\# Set a password for local user client001.

\[Switch-luser-manage-client001]
password simple 123456TESTplat\&!

\# Authorize local user client001 to use the SSH service.

\[Switch-luser-manage-client001] service-type
ssh

\# Assign the network-admin
user role to local user client001.

\[Switch-luser-manage-client001] authorization-attribute
user-role network-admin

\[Switch-luser-manage-client001] quit

\# Create an SSH user named client001. Specify the service type as NETCONF and the authentication method as password for the user.

\[Switch] ssh user client001
service-type netconf authentication-type password

#### Verifying the configuration

\# Verify that you can perform NETCONF
operations after logging in to the switch. (Details not shown.)

 

