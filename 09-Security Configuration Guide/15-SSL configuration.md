
# Configuring SSL

## About SSL

Secure Sockets Layer (SSL) is a cryptographic
protocol that provides communication security for TCP-based application layer
protocols such as HTTP. SSL has been widely used in applications such as e-business
and online banking to provide secure data transmission over the Internet.

### SSL securityservices

SSL provides the following security
services:

·     Privacy—SSL uses a symmetric encryption algorithm to encrypt data. It uses the
asymmetric key algorithm of RSA to encrypt the key used by the symmetric
encryption algorithm. For more information about RSA, see "Managing public
keys." 

·     Authentication—SSL uses certificate-based digital signatures to authenticate the
SSL server and client. The SSL server and client obtain digital certificates
through PKI. For more information about PKI and digital certificates, see "Configuring
PKI."

·     Integrity—SSL uses the message authentication code (MAC) to verify message
integrity. It uses a MAC algorithm and a key to transform a message of any
length to a fixed-length message. Any change to the original message will
result in a change to the calculated fixed-length message. As shown in [Figure 1](#_Ref207695352), the
message integrity verification process is as follows:

**a.**The sender uses a MAC algorithm and a key to
calculate a MAC value for a message. Then, it appends the MAC value to the
message and sends the message to the receiver.

**b.**The receiver uses the same key and MAC
algorithm to calculate a MAC value for the received message, and compares it
with the MAC value appended to the message.

**c.**If the two MAC values match, the receiver
considers the message intact. Otherwise, the receiver considers that the
message was tampered with and it discards the message.

Figure 1 MAC algorithm diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705458_x_Img_x_png_0_2216076_294551_0.png)

 

### SSL protocol stack

The SSL protocol stack includes the following
protocols:

·     SSL record protocol at the lower layer.

·     SSL handshake protocol, SSL change cipher spec
protocol, and SSL alert protocol at the upper layer.

Figure 2 SSL protocol stack

![](https://resource.h3c.com/en/202407/12/20240712_11705459_x_Img_x_png_1_2216076_294551_0.png)

 

The following describes the major functions
of SSL protocols:

·     SSL record protocol—Fragments data received from the upper layer, computes and adds MAC
to the data, and encrypts the data.

·     SSL handshake protocol—Negotiates the cipher suite used for secure communication, authenticates
the server and client, and securely exchanges the keys between the server and
client. The cipher suite that needs to be negotiated includes the symmetric
encryption algorithm, key exchange algorithm, and MAC algorithm.

·     SSL change cipher spec
protocol—Notifies the receiver that subsequent
packets are to be protected based on the negotiated cipher suite and key.

·     SSL alert protocol—Sends alert messages to the receiving party. An alert message
contains the alert severity level and a description.

### SSL protocol versions

SSL protocol versions include SSL 2.0, SSL
3.0, TLS 1.0 (or SSL 3.1), TLS 1.1, and TLS 1.2. Because SSL 3.0 is known to be
insecure, you can disable SSL 3.0 for the SSL server to ensure security.

## FIPS compliance

The device supports the FIPS mode that
complies with NIST FIPS 140-2 requirements. Support for features, commands, and
parameters might differ in FIPS mode (see "Configuring FIPS") and
non-FIPS mode. 

## Restrictions and guidelines: SSL configuration

By default, the SSL server can communicate
with clients running all SSL protocol versions. When the server receives an SSL
2.0 Client Hello message from a client, it notifies the client to use a later
version for communication.

## SSL tasks at a glance

### Configuring the SSL server

To configure SSL, perform the following
tasks:

**1\.**Configuring SSL server settings

¡     [Configuring an SSL server policy](#_Ref156276530)

¡     (Optional.)
[Disabling SSL protocol versions for the
SSL server](#_Ref478043915)

¡     (Optional.) [Disabling SSL session renegotiation](#_Ref478043920)

**2\.**Configuring SSL client settings

[Configuring an SSL client policy](#_Ref101014042)

**3\.**[Specifying a security enhanced level](#_Ref98784294)

## Configuring an SSL server policy

#### About this task

An SSL server policy is a set of SSL
parameters used by the device when the device acts as the SSL server. An SSL
server policy takes effect only after it is associated with an application such
as HTTPS.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Create an SSL server policy and enter its
view.

ssl server-policy policy-name

**3\.**Specify a PKI domain for the SSL server
policy.

pki-domain domain-name

By default, no PKI domain is specified
for an SSL server policy.

If SSL server authentication is required,
you must specify a PKI domain and request a local certificate for the SSL
server in the domain.

For information about configuring a PKI domain,
see "Configuring PKI."

**4\.**Specify the cipher suites that the SSL
server policy supports.

In non-FIPS mode:

ciphersuite{ dhe\_rsa\_aes\_128\_cbc\_sha \| dhe\_rsa\_aes\_128\_cbc\_sha256 \| dhe\_rsa\_aes\_256\_cbc\_sha \| dhe\_rsa\_aes\_256\_cbc\_sha256 \| ecdhe\_ecdsa\_aes\_128\_cbc\_sha256 \| ecdhe\_ecdsa\_aes\_128\_gcm\_sha256 \| ecdhe\_ecdsa\_aes\_256\_cbc\_sha384 \| ecdhe\_ecdsa\_aes\_256\_gcm\_sha384 \| ecdhe\_rsa\_aes\_128\_cbc\_sha256 \| ecdhe\_rsa\_aes\_128\_gcm\_sha256 \| ecdhe\_rsa\_aes\_256\_cbc\_sha384 \| ecdhe\_rsa\_aes\_256\_gcm\_sha384 \| exp\_rsa\_des\_cbc\_sha \| exp\_rsa\_rc2\_md5 \| exp\_rsa\_rc4\_md5 \| rsa\_3des\_ede\_cbc\_sha \| rsa\_aes\_128\_cbc\_sha \| rsa\_aes\_128\_cbc\_sha256 \| rsa\_aes\_256\_cbc\_sha \| rsa\_aes\_256\_cbc\_sha256 \| rsa\_des\_cbc\_sha \| rsa\_rc4\_128\_md5 \| rsa\_rc4\_128\_sha } \*

In FIPS mode:

ciphersuite{ ecdhe\_ecdsa\_aes\_128\_cbc\_sha256 \| ecdhe\_ecdsa\_aes\_256\_cbc\_sha384 \| ecdhe\_ecdsa\_aes\_128\_gcm\_sha256 \| ecdhe\_ecdsa\_aes\_256\_gcm\_sha384 \| ecdhe\_rsa\_aes\_128\_cbc\_sha256 \| ecdhe\_rsa\_aes\_128\_gcm\_sha256 \| ecdhe\_rsa\_aes\_256\_cbc\_sha384 \| ecdhe\_rsa\_aes\_256\_gcm\_sha384 \| rsa\_aes\_128\_cbc\_sha \| rsa\_aes\_128\_cbc\_sha256 \| rsa\_aes\_256\_cbc\_sha \| rsa\_aes\_256\_cbc\_sha256 } \*

By default, an SSL server policy supports
all cipher suites.

**5\.**(Optional.) Set the maximum number of
sessions that the SSL server can cache and the session cache timeout time.

session { cachesize size \| timeout time } \* 

By default, the SSL server can cache a
maximum of 500 sessions, and the session cache timeout time is 3600 seconds.

**6\.**Enable mandatory or optional SSL client authentication.

client-verify { enable \| optional }

By default, SSL client authentication is
disabled. The SSL server does not perform digital certificate-based
authentication on SSL clients.

When authenticating a client by using the
digital certificate, the SSL server verifies the certificate chain presented by
the client. It also verifies that the certificates in the certificate chain
(except the root CA certificate) are not revoked.

**7\.**(Optional.) Enable the SSL server to send
the complete certificate chain to the client during SSL negotiation.

certificate-chain-sending enable

By default, the SSL server sends the
server certificate rather than the complete certificate chain to the client
during negotiation.

## Configuring an SSL client policy

#### About this task

An SSL client policy is a set of SSL
parameters used by the device when the device acts as the SSL client. The SSL client
uses the settings in the client policy to establish a connection to the server.
An SSL client policy takes effect only after it is associated with an
application such as DDNS. For information about DDNS, see the DNS configurtion
in Layer 3—IP Services Configuration
Guide.

#### Restrictions and guidelines

As a best practice to enhance system
security, do not specify SSL 3.0 for the SSL client policy.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Create an SSL client policy and enter its
view.

ssl client-policy policy-name

**3\.**Specify a PKI domain for the SSL client
policy.

pki-domain domain-name

By default, no PKI domain is specified
for an SSL client policy.

If SSL client authentication is required,
you must specify a PKI domain and request a local certificate for the SSL
client in the PKI domain.

For information about configuring a PKI domain,
see "Configuring PKI."

**4\.**Specify the preferred cipher suite for the
SSL client policy.

In non-FIPS mode:

prefer-cipher { dhe\_rsa\_aes\_128\_cbc\_sha
\| dhe\_rsa\_aes\_128\_cbc\_sha256
\| dhe\_rsa\_aes\_256\_cbc\_sha
\| dhe\_rsa\_aes\_256\_cbc\_sha256
\| ecdhe\_ecdsa\_aes\_128\_cbc\_sha256
\| ecdhe\_ecdsa\_aes\_128\_gcm\_sha256 \| ecdhe\_ecdsa\_aes\_256\_cbc\_sha384 \| ecdhe\_ecdsa\_aes\_256\_gcm\_sha384
\| ecdhe\_rsa\_aes\_128\_cbc\_sha256 \| ecdhe\_rsa\_aes\_128\_gcm\_sha256 \| ecdhe\_rsa\_aes\_256\_cbc\_sha384
\| ecdhe\_rsa\_aes\_256\_gcm\_sha384
\| exp\_rsa\_des\_cbc\_sha \| exp\_rsa\_rc2\_md5 \| exp\_rsa\_rc4\_md5 \| rsa\_3des\_ede\_cbc\_sha \| rsa\_aes\_128\_cbc\_sha \|
rsa\_aes\_128\_cbc\_sha256 \| rsa\_aes\_256\_cbc\_sha \| rsa\_aes\_256\_cbc\_sha256 \| rsa\_des\_cbc\_sha \| rsa\_rc4\_128\_md5 \| rsa\_rc4\_128\_sha }

In FIPS mode:

prefer-cipher { ecdhe\_ecdsa\_aes\_128\_cbc\_sha256 \| ecdhe\_ecdsa\_aes\_128\_gcm\_sha256
\| ecdhe\_ecdsa\_aes\_256\_cbc\_sha384
\| ecdhe\_ecdsa\_aes\_256\_gcm\_sha384 \| ecdhe\_rsa\_aes\_128\_cbc\_sha256 \| ecdhe\_rsa\_aes\_128\_gcm\_sha256
\| ecdhe\_rsa\_aes\_256\_cbc\_sha384
\| ecdhe\_rsa\_aes\_256\_gcm\_sha384
\| rsa\_aes\_128\_cbc\_sha \|
rsa\_aes\_128\_cbc\_sha256 \| rsa\_aes\_256\_cbc\_sha \| rsa\_aes\_256\_cbc\_sha256 }

By default: 

¡     In
non-FIPS mode, the preferred cipher suite is rsa\_rc4\_128\_md5.

¡     In
FIPS mode, the preferred cipher suite is rsa\_aes\_128\_cbc\_sha.


**5\.**Specify the SSL protocol version for the SSL
client policy.

In non-FIPS mode:

version { ssl3.0 \| tls1.0 \| tls1.1 \| tls1.2 }

In FIPS mode:

version { tls1.0 \| tls1.1 \| tls1.2 }

By default, an SSL client policy uses TLS
1.0. 

**6\.**Enable the SSL client to authenticate
servers through digital certificates.

server-verify enable

By default, SSL server authentication is
enabled.

## Disabling SSL protocol versions for the SSL server

#### About this task

To enhance system security, you can disable
the SSL server from using specific SSL protocol versions (SSL 3.0, TLS 1.0, and
TLS 1.1) for session negotiation.

#### Restrictions and guidelines

Disabling an SSL protocol version does not
affect the availability of earlier SSL protocol versions. For example, if you
execute the ssl version tls1.1 disable command, TLS 1.1 is disabled but TLS 1.0 is still available for the
SSL server.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Disable SSL protocol versions for the SSL
server.

In non-FIPS mode:

ssl version { ssl3.0 \| tls1.0 \| tls1.1 } \* disable

In FIPS mode:

ssl version { tls1.0 \| tls1.1 } \* disable

By default: 

¡     In
non-FIPS mode, the SSL server supports SSL 3.0, TLS 1.0, TLS 1.1, and TLS 1.2. 

¡     In FIPS
mode, the SSL server supports TLS 1.0, TLS 1.1, and TLS 1.2. 

## Disabling SSL session renegotiation

#### About this task

The SSL session renegotiation feature
enables the SSL client and server to reuse a previously negotiated SSL session
for an abbreviated handshake.

Disabling session renegotiation causes more
computational overhead to the system but it can avoid potential risks.

#### Restrictions and guidelines

Disable SSL session renegotiation only when
explicitly required.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Disable SSL session renegotiation.

ssl renegotiation disable

By default, SSL session renegotiation is
enabled.

## Specifying a security enhanced level

#### About this task

The security enhanced level for the device
can be 1 and 2, and level 2 indicates a higher security level. If the security
enhanced level is set to 2, the following rules apply:

·     SSL client policies and SSL server policies do
not support cipher suites that contain DES, 3DES, MD5, RC4, and RC2.

·     SSL client policies and SSL server policies do
not support SSL protocol versions lower than TLS 1.1.

·     SSL session renegotiation cannot be enabled.

You can specify a security enhanced level
as needed.

#### Restrictions and guidelines

After you change the security enhanced
level, for services associated with SSL policies, such as HTTP and SSL VPN, you
must re-enable these services to update the associated policies.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Specify a security enhanced level for the
device.

security-enhanced level
level-value

By default, the security enhanced level
is 1 for the device. 

## Display and maintenance commands for SSL

Execute display
commands in any view.

 

| Task | Command |
| --- | --- |
| Display SSL client policy information. | display ssl client-policy \[ policy-name ] || Display SSL server policy information. | display ssl server-policy \[ policy-name ] |


 

 

