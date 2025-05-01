
# Configuring PKI

## About PKI

Public Key Infrastructure (PKI) is an
asymmetric key infrastructure to encrypt and decrypt data for securing network
services.

PKI uses digital certificates to distribute
and employ public keys, and provides network communication and e-commerce with
security services such as user authentication, data confidentiality, and data
integrity. For more information about public keys, see "Managing public
keys."

### PKI terminology

#### Digital certificate

A digital certificate is an electronic document
signed by a CA that binds a public key with the identity of its owner. 

A digital certificate includes the following
information:

·     Issuer name (name of the CA that issued the
certificate).

·     Subject name (name of the individual or group to
which the certificate is issued).

·     Identity information of the subject.

·     Subject's public key.

·     Signature of the CA.

·     Validity period.

A digital certificate must comply with the
international standards of ITU-T X.509, of which X.509 v3 is the most commonly
used.

This chapter covers the following types of
certificates:

·     CA certificate—Certificate of a CA. Multiple CAs in a PKI system form a CA tree,
with the root CA at the top. The root CA generates a self-signed certificate,
and each lower level CA holds a CA certificate issued by the CA immediately
above it. The chain of these certificates forms a chain of trust.

·     Registration authority (RA)
certificate—Certificate issued by a CA to an RA.
RAs act as proxies for CAs to process enrollment requests in a PKI system.

·     Local certificate—Certificate of a local PKI entity, which contains the entity's
public key. Local certificates include the following types:

¡     CA-signed certificate—Digital
certificate signed and issued by a CA to the local PKI entity.

¡     Self-signed certificate—Digital
certificate signed and issued by the local PKI entity (the device) itself. When
the device cannot request a local certificate from the CA, it can sign and
issue a local certificate for communication with the peer to provide services. Using
this type of certificate is simple in configuration but has low security. You
can use self-signed certificates in scenarios with low security requirements.

·     Peer certificate—CA-signed digital certificate of a peer, which contains the peer's
public key. 

#### Fingerprint of root CA certificate

Each root CA certificate has a unique
fingerprint, which is the hash value of the certificate content. The
fingerprint of a root CA certificate can be used to authenticate the validity
of the root CA.

#### Certificate revocation list

A certificate revocation list (CRL) is a
list of serial numbers for certificates that have been revoked. A CRL is created
and signed by the CA that originally issued the certificates.

The CA publishes CRLs periodically to
revoke certificates. Entities that are associated with the revoked certificates
should not be trusted.

The CA must revoke a certificate when any
of the following conditions occurs:

·     The certificate subject name is changed.

·     The private key is compromised.

·     The association between the subject and CA is
changed. For example, when an employee terminates employment with an
organization. 

#### CA policy

A CA policy is a set of criteria that a CA
follows to process certificate requests, to issue and revoke certificates, and to
publish CRLs. Typically, a CA advertises its policy in a certification practice
statement (CPS). You can obtain a CA policy through out-of-band means such as
phone, disk, and email. Make sure you understand the CA policy before you select
a trusted CA for certificate request because different CAs might use different policies.

### PKI architecture

A PKI system consists of PKI entities, CAs,
RAs and a certificate/CRL repository, as shown in [Figure 1](#_Ref375998946).

Figure 1 PKIarchitecture

![](https://resource.h3c.com/en/202407/12/20240712_11705415_x_Img_x_png_0_2216073_294551_0.png)

‌

#### PKI entity

A PKI entity is an end user using PKI certificates.
The PKI entity can be an operator, an organization, a device like a router or a
switch, or a process running on a computer. PKI entities use SCEP to
communicate with the CA or RA.

#### CA

Certification authority that grants and
manages certificates. A CA issues certificates, defines the certificate validity
periods, and revokes certificates by publishing CRLs.

#### RA

Registration authority, which offloads the
CA by processing certificate enrollment requests. The RA accepts certificate
requests, verifies user identity, and determines whether to ask the CA to issue
certificates. 

The RA is optional in a PKI system. In
cases when there is security concern over exposing the CA to direct network
access, it is advisable to delegate some of the tasks to an RA. Then, the CA can
concentrate on its primary tasks of signing certificates and CRLs.

#### Certificate/CRL repository

A certificate distribution point that stores
certificates and CRLs, and distributes these certificates and CRLs to PKI entities.
It also provides the query function. A PKI repository can be a directory server
using the LDAP or HTTP protocol, of which LDAP is commonly used.

### Retrieval, usage, and maintenance of a digital certificate

The following workflow describes the
retrieval, usage, and maintenance of a digital certificate. This example uses a
CA which has an RA to process certificate enrollment requests.

**1\.**A PKI entity generates an asymmetric key
pair and submits a certificate request to the RA.

The certificate request contains the
public key and its identity information.

**2\.**The RA verifies the identity of the entity
and sends a digital signature containing the identity information and the
public key to the CA.

**3\.**The CA verifies the digital signature,
approves the request, and issues a certificate.

**4\.**After receiving the certificate from the CA,
the RA sends the certificate to the certificate repository
and notifies the PKI entity that the certificate has been issued.

**5\.**The PKI entity obtains the certificate from
the certificate repository.

**6\.**To establish a secure connection for
communication, two PKI entities exchange local certificates to authenticate
each other. The connection can be established only if both entities verify that
the peer's certificate is valid.

**7\.**You can remove the local certificate of a
PKI entity and request a new one when any of the following conditions occur:

¡     The
local certificate is about to expire.

¡     The
certificate's private key is compromised.

### PKI applications

The PKI technology can meet security
requirements of online transactions. As an infrastructure, PKI has a wide range
of applications. H3C's PKI system can provide certificate management for IPsec
and SSL.

The following are some application
examples.

#### VPN

A VPN is a private data communication
network built on the public communication infrastructure. A VPN can use network
layer security protocols (for example, IPsec) in conjunction with PKI-based
encryption and digital signature technologies for confidentiality.

#### Secure emails

PKI can address the email requirements for
confidentiality, integrity, authentication, and non-repudiation. A common
secure email protocol is Secure/Multipurpose Internet Mail Extensions (S/MIME),
which is based on PKI and allows for transfer of encrypted mails with
signature.

#### Web security

PKI can be used in the SSL handshake phase to
verify the identities of the communicating parties by digital certificates.

## FIPS compliance

The device supports the FIPS mode that
complies with NIST FIPS 140-2 requirements. Support for features, commands, and
parameters might differ in FIPS mode (see "Configuring FIPS") and
non-FIPS mode.

## PKI tasks at a glance

To configure PKI, perform the following
tasks:

**1\.**[Configuring a PKI entity](#_Ref306786006)

**2\.**[Configuring a PKI domain](#_Ref477963274)

**3\.**(Optional.) [Specifying the storage path for
certificates and CRLs](#_Ref477963275)

**4\.**[Requesting a certificate](#_Ref477963276)

Choose one of the following tasks:

¡     [Enabling the automatic online certificate
request mode](#_Ref458880674)

¡     [Manually submitting an online certificate
request](#_Ref458880575)

¡     [Manually submitting a certificate request
in offline mode](#_Ref458695640)

**5\.**(Optional.) [Aborting a certificate request](#_Ref324259749)

**6\.**(Optional.) [Obtaining certificates](#_Ref324259756)

You can obtain the CA certificate, local
certificates, and peer certificates related to a PKI domain from a CA and save
them locally for higher lookup efficiency. 

**7\.**(Optional.) [Verifying PKI certificates](#_Ref156987301)

**8\.**(Optional.) [Exporting certificates](#_Ref324259792)

**9\.**(Optional.) [Removing a certificate](#_Ref324259795)

**10\.**(Optional.) [Configuring a certificate-based access
control policy](#_Ref324259800)

Certificate-based access control policies
allow you to authorize access to a device (for example, an HTTPS server) based
on the attributes of an authenticated client's certificate.

**11\.**[Enabling local certificate expiration
notification](#_Ref81984827)

## Configuring a PKI entity

#### About this task

A certificate applicant uses an entity to provide
its identity information to a CA. A valid PKI entity must include one or more
of following identity categories:

·     Distinguished name (DN) of the entity, which
further includes the common name, country code, locality, organization, unit in
the organization, and state. If you configure the DN for an entity, a common
name is required.

·     FQDN of the entity.

·     IP address of the entity.

#### Restrictions and guidelines

Follow these restrictions and guidelines
when you configure a PKI entity:

·     Whether the identity categories are required or
optional depends on the CA policy. Follow the CA policy to configure the entity
settings. For example, if the CA policy requires the entity DN, but you
configure only the IP address, the CA rejects the certificate request from the
entity.

·     The SCEP add-on on the Windows 2000 CA server
has restrictions on the data length of a certificate request. If a request from
a PKI entity exceeds the data length limit, the CA server does not respond to the
certificate request. In this case, you can use an out-of-band means to submit
the request. Other types of CA servers, such as RSA servers and OpenCA servers,
do not have such restrictions.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Create a PKI entity and enter its view.

pki entity entity-name

**3\.**Set a common name for the entity.

common-name common-name-sting

By default, the common name is not set.

**4\.**Set the country code of the entity.

country country-code-string

By default, the country code is not set.

**5\.**Set the locality of the entity.

locality locality-name

By default, the locality is not set.

**6\.**Set the organization of the entity.

organization org-name

By default, the organization is not set.

**7\.**Set the unit of the entity in the
organization.

organization-unit org-unit-name

By default, the unit is not set.

**8\.**Set the state where the entity resides.

state state-name

By default, the state is not set.

**9\.**Set the FQDN of the entity. 

fqdn fqdn-name-string

By default, the FQDN is not set.

**10\.**Configure the IP address of the entity.

ip { ip-address \| interface interface-type
interface-number }

By default, the IP address is not
configured.

## Configuring a PKI domain

### About PKI domain

A PKI domain contains enrollment
information for a PKI entity. It is locally significant and is intended only
for use by other applications like IKE and SSL.

### PKI domain tasks at a glance

To configure a PKI domain, perform the
following tasks:

**1\.**[Creating a PKI domain](#_Ref477963935)

**2\.**[Specifying the trusted CA](#_Ref477963946)

**3\.**[Specifying the PKI entity name](#_Ref477963951)

**4\.**[Specifying the certificate request
reception authority](#_Ref477964068)

**5\.**[Specifying the certificate request URL](#_Ref477964845)

**6\.**(Optional.) [Setting the SCEP polling interval and
maximum polling attempts](#_Ref477964084)

**7\.**[Specifying the LDAP server](#_Ref477964105)

This task is required when either of the
following conditions is met:

¡     The device must obtain certificates from the CA by using the LDAP
protocol.

¡     An LDAP URL which does not contain the host name of the LDAP server is
specified as the CRL repository URL.

**8\.**[Specifying the fingerprint for root CA
certificate verification](#_Ref508649101)

This step is required if the auto
certificate request mode is configured in the PKI domain. 

If the manual certificate request mode is
configured, you can skip this step and manually verify the fingerprint
displayed during verification of the root CA certificate.

**9\.**[Specifying the key pair for certificate
request](#_Ref477964114)

**10\.**(Optional.) [Specifying the intended purpose for the
certificate](#_Ref477964120)

**11\.**(Optional.) [Specifying the source IP address for PKI
protocol packets](#_Ref477964124)

### Creating a PKI domain

**1\.**Enter system view.

system-view

**2\.**Create a PKI domain and enter its view.

pki domain domain-name

### Specifying the trusted CA

#### About this task

The PKI domain must have a CA certificate
before you can request a local certificate. To obtain a CA certificate, the
trusted CA name must be specified. The trusted CA name uniquely identifies the
CA to be used if multiple CAs exist on the CA server.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter PKI domain view.

pki domain domain-name

**3\.**Specify the trusted CA name.

ca identifier name

By default, no trusted CA name is
specified.

### Specifying the PKI entity name

**1\.**Enter system view.

system-view

**2\.**Enter PKI domain view.

pki domain domain-name

**3\.**Specify the PKI entity name.

certificate request entity entity-name

By default, no PKI entity name is
specified.

### Specifying the certificate requestreception authority

**1\.**Enter system view.

system-view

**2\.**Enter PKI domain view.

pki domain domain-name

**3\.**Specify the certificate request reception
authority.

certificate request from { ca \| ra }

By default, no certificate request
reception authority is specified.

### Specifying the certificate request URL

**1\.**Enter system view.

system-view

**2\.**Enter PKI domain view.

pki domain domain-name

**3\.**Specify the URL of the certificate request
reception authority to which the device sends certificate requests.

certificate request url url-string \[ vpn-instance vpn-instance-name ]

By default, the certificate request URL
is not specified.

### Setting the SCEP polling interval and maximum polling attempts

**1\.**Enter system view.

system-view

**2\.**Enter PKI domain view.

pki domain domain-name

**3\.**Set the SCEP polling interval and maximum
number of polling attempts.

certificate request polling { count count \| interval interval }

By default, the device polls the CA
server for the certificate request status every 20 minutes. The maximum number
of polling attempts is 50\.

### Specifying the LDAP server

**1\.**Enter system view.

system-view

**2\.**Enter PKI domain view.

pki domain domain-name

**3\.**Specify the LDAP server.

ldap-server host hostname \[ port port-number ] \[ vpn-instance vpn-instance-name ]

By default, no LDAP server is specified. 

### Specifying the fingerprint for root CA certificate verification

**1\.**Enter system view.

system-view

**2\.**Enter PKI domain view.

pki domain domain-name

**3\.**Configure the fingerprint for verifying the
root CA certificate.

In non-FIPS mode:

root-certificate fingerprint { md5 \| sha1 } string

In FIPS mode:

root-certificate fingerprint sha1 string

By default, no fingerprint is configured.


### Specifying the key pair for certificate request

#### Restrictions and guidelines

You can specify a nonexistent key pair for
certificate request. The PKI entity automatically creates the key pair before
submitting a certificate request.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter PKI domain view.

pki domain domain-name

**3\.**Specify the key pair for certificate
request.

¡     Specify
an RSA key pair.

public-key rsa { { encryption name encryption-key-name \[ length key-length ] \| signature name signature-key-name \[ length key-length ] } \* \| general name key-name \[ length key-length ] }

¡     Specify
an ECDSA key pair.

In non-FIPS mode:

public-key ecdsa name key-name \[ secp192r1 \| secp256r1 \| secp384r1 \| secp521r1 ]

In FIPS mode:

public-key ecdsa name key-name \[ secp256r1 \| secp384r1 \| secp521r1 ]

¡     Specify
a DSA key pair.

public-key dsa name key-name \[ length key-length ]

By default, no key pair is specified.

### Specifying the intended purpose for the certificate

#### About this task

An issued certificate contains the
extensions which restrict the usage of the certificate to specific purposes.
You can specify the intended purposes for a certificate, which will be included
in the certificate request sent to the CA. However, the actual extensions
contained in an issued certificate depend on the CA policy, and they might be
different from those specified in the PKI domain. Whether an application will
use the certificate during authentication depends on the application's policy.

Supported certificate extensions include:

·     ike—Certificates carrying this extension can be used by IKE peers.

·     ssl-client—Certificates carrying this extension can be used by SSL clients.

·     ssl-server—Certificates carrying this extension can be used by SSL servers.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter PKI domain view.

pki domain domain-name

**3\.**Specify the intended use for the
certificate.

usage { ike \| ssl-client \| ssl-server } \*

By default, the certificate can be used
by all supported applications, including IKE, SSL client, and SSL server.

### Specifying the source IP address for PKI protocol packets

#### About this task

This task is required if the CA policy
requires that the CA server accept certificate requests from a specific IP
address or subnet. 

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter PKI domain view.

pki domain domain-name

**3\.**Specify a source IP address for the PKI
protocol packets.

IPv4:

source ip { ip-address \| interface interface-type interface-number }

IPv6:

source ipv6 { ipv6-address \| interface interface-type interface-number }

By default, the source IP address of PKI
protocol packets is the IP address of their outgoing interface.

## Specifying the storage path for certificates and CRLs

#### About this task

The device has a default storage path for
certificates and CRLs. You can change the storage path and specify different
paths for the certificates and CRLs.

After you change the storage path for
certificates or CRLs, the certificate files and CRL files in the original path
are moved to the new path. Certificate files use the .cer or .p12 file
extension and CRL files use the .crl file extension.

#### Restrictions and guidelines

If you change the storage path, save the configuration
before you reboot or shut down the device to avoid loss of certificates or
CRLs.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Specify the storage path for certificates
and CRLs.

pki storage { certificates \| crls } dir-path

By default, the device stores
certificates and CRLs in the PKI directory on the storage media of the device.

## Requesting a certificate

### About certificate request configuration

To request a certificate, a PKI entity must
provide its identity information and public key to a CA.

A certificate request can be submitted to a
CA in offline or online mode.

·     Offline mode—A certificate request is submitted by using an out-of-band method,
such as phone, disk, or email.

·     Online mode—A certificate request can be automatically or manually submitted to
a CA through the Simple Certificate Enrollment Protocol (SCEP).

### Restrictions and guidelines for certificate request configuration

When you request a local certificate in a
PKI domain, follow these restrictions and guidelines:

·     To prevent an existing local certificate from
becoming invalid, do not perform the following tasks:

¡     Create
a key pair with the same name as the key pair contained in the certificate.

To create a key pair, use the public-key
local create command.

¡     Destroy
the key pair contained in the certificate.

To destroy a key pair, use the public-key
local destroy command.

·     To manually request a new certificate in a PKI
domain that already has a local certificate, use the following procedure: 

**a.**Use the pki delete-certificate command to delete the existing local certificate.

**b.**Use the public-key local create command to generate a new key pair.

**c.**Manually submit a certificate request.

·     A PKI domain can have local certificates using
only one type of cryptographic algorithms (DSA, ECDSA, or RSA). If DSA or ECDSA
is used, a PKI domain can have only one local certificate. If RSA is used, a
PKI domain can have one local certificate for signature, and one local
certificate for encryption.

### Prerequisites for certificate request configuration

Make sure the device is time synchronized
with the CA server. If the device is not time synchronized with the CA server,
the certificate request might fail because the certificate might be considered
to be outside of the validity period. For information about configuring the
system time, see Fundamentals Configuration Guide.

### Enabling the automatic online certificate request mode

#### About this task

In auto request mode, a PKI entity with no
local certificates automatically submits a certificate request to the CA when
an application works with the PKI entity. For example, when IKE negotiation
uses a digital signature for identity authentication, but no local certificate
is available, the entity automatically submits a certificate request. It saves
the certificate locally after obtaining the certificate from the CA.

A CA certificate must be present before you
request a local certificate. If no CA certificate exists in the PKI domain, the
PKI entity automatically obtains a CA certificate before sending a certificate
request.

#### Restrictions and guidelines

In auto request mode, the device does not
automatically request a new certificate if the current certificate is about to
expire or has expired, which might cause service interruptions.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter PKI domain view.

pki domain domain-name

**3\.**Enable the automatic online certificate
request mode.

certificate request mode auto \[ password { cipher \| simple } string ]

By default, the manual request mode
applies.

If the CA policy requires a password for
certificate revocation, specify the password in this command.

### Manually submitting an online certificate request

#### About this task

In manual request mode, you must execute
the pki
request-certificate domain command to request a
local certificate in a PKI domain. The certificate will be saved in the domain
after it is obtained from the CA.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter PKI domain view.

pki domain domain-name

**3\.**Set the certificate request mode to manual.

certificate request mode manual

By default, the manual request mode
applies. 

**4\.**Return to system view.

quit

**5\.**Obtain a CA certificate.

See "[Obtaining certificates](#_Ref324259756)."

This step is required if the PKI domain
does not have a CA certificate. The CA certificate is used to verify the
authenticity and validity of the obtained local certificate.

**6\.**Manually submit an SCEP certificate request.

pki request-certificate domain domain-name \[ password password ]

This command is not saved in the
configuration file.

If the CA policy requires a password for
certificate revocation, specify the password in this command.

### Manually submitting a certificate request in offline mode

#### About this task

Use this method if the CA does not support
SCEP or if a network connection between the device and CA is not possible.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enter PKI domain view.

pki domain domain-name

**3\.**Set the certificate request mode to manual.

certificate request mode manual

By default, the manual request mode
applies. 

**4\.**Return to system view.

quit

**5\.**Obtain the CA certificate.

See "[Obtaining certificates](#_Ref324259756)."

This step is required if the PKI domain
does not have a CA certificate. The CA certificate is used to verify the
authenticity and validity of the obtained local certificate.

**6\.**Print the certificate request in PKCS10
format on the terminal or save the certificate request to a PKCS10 file.

pki request-certificate domain domain-name pkcs10 \[ filename filename ]

This command is not saved in the
configuration file.

**7\.**Transfer certificate request information to
the CA by using an out-of-band method.

**8\.**Transfer the issued local certificate from
the CA to the local device by using an out-of-band method.

**9\.**Import the local certificate to the PKI
domain. 

pki import domain domain-name { der local filename filename \| p12 local
filename filename \| pem local \[ filename filename ] }

## Aborting a certificate request

#### About this task

Before the CA issues a certificate, you can
abort a certificate request and change its parameters, such as the common name,
country code, or FQDN. You can use the display pki certificate request-status command to display the status of a certificate request.

Alternatively, you also can remove a PKI
domain to abort the associated certificate request.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Abort a certificate request.

pki abort-certificate-request domain domain-name

This command is not saved in the
configuration file. 

## Obtaining certificates

#### About this task

You can obtain the CA certificate, local
certificates, and peer certificates related to a PKI domain from a CA and save
them locally for higher lookup efficiency. To do so, use either the offline
mode or the online mode:

·     In offline mode, obtain the certificates by an
out-of-band means like FTP, disk, or email, and then import them locally. Use
this mode when the CRL repository is not specified, the CA server does not
support SCEP, or the CA server generates the key pair for the certificates.

·     In online mode, you can obtain the CA
certificate through SCEP and obtain local certificates or peer certificates
through LDAP.

#### Restrictions and guidelines

Follow these restrictions and guidelines
when obtain certificates from a CA

·     If a CA certificate already exists locally, you
cannot obtain it again in online mode. If you want to obtain a new CA
certificate, use the pki delete-certificate
command to delete the existing CA certificate and local certificates first.

·     If local or peer certificates already exist, you
can obtain new local or peer certificates to overwrite the existing ones. If
RSA is used, a PKI domain can have two local certificates, one for signature
and the other for encryption.

·     If CRL checking is enabled, obtaining a
certificate triggers CRL checking. If the certificate to be obtained has been
revoked, the certificate cannot be obtained.

·     The device compares the validity period of a
certificate with the local system time to determine whether the certificate is
valid. Make sure the system time of the device is synchronized with the CA
server.

#### Prerequisites

·     Before you obtain local or peer certificates in
online mode, make sure an LDAP server is correctly configured in the PKI
domain.

·     Before you import certificates in offline mode,
complete the following tasks:

¡     Use
FTP or TFTP to upload the certificate files to the storage media of the device.


If FTP or TFTP is not available, display
and copy the contents of a certificate to a file on the device. Make sure the
certificate is in PEM format because only certificates in PEM format can be
imported.

¡     Before
you import a local certificate or peer certificate, obtain the CA certificate chain
that signs the certificate.

This step is required only if the CA
certificate chain is neither available in the PKI domain nor contained in the certificate
to be imported.

¡     Before
you import a local certificate that contains an encrypted key pair, contact the
CA administrator to obtain the password required for importing the certificate.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Obtain certificates. 

¡     Import
certificates in offline mode.

pki import domain domain-name { der { ca \| local \| peer } filename filename \| p12 local
filename filename \| pem { ca \| local \| peer } \[ filename filename ] }

¡     Obtain
certificates in online mode.

pki retrieve-certificate domain domain-name { ca \| local \| peer entity-name }

This command is not saved in the
configuration file. 

## Verifying PKI certificates

### About certification verification

A certificate is automatically verified
when it is requested, obtained, or used by an application. If the certificate
expires, if it is not issued by a trusted CA, or if it is revoked, the
certificate cannot be used You can also manually verify a certificate.

You can enable or disable CRL checking in a
PKI domain. CRL checking checks whether a certificate is in the CRL. If it is,
the certificate has been revoked and its home entity is not trusted.

To use CRL checking, a CRL must be
obtained from a CRL repository. The device selects a CRL repository in the
following order:

**1\.**CRL repository specified in the PKI domain
by using the crl url command.

**2\.**CRL repository in the certificate that is
being verified.

**3\.**CRL repository in the CA certificate or CRL
repository in the upper-level CA certificate if the certificate being verified
is a CA certificate

If no CRL repository is found after the
selection process, the device obtains the CRL through SCEP. In this scenario,
the CA certificate and the local certificates must have been obtained.

A certificate fails CRL checking in the
following situations:

·     A CRL cannot be obtained during CRL checking of the
certificate.

·     CRL checking verifies that the certificate has
been revoked.

### Restrictions and guidelines for certificate verification

When verifying the CA certificate of a PKI
domain, the system needs to verify all the certificates in the CA certificate
chain. To ensure a successful certificate verification process, the device must
have all the PKI domains to which the CA certificates in the certificate chain
belong.

The system verifies the CA certificates in
the CA certificate chain as follows:

**1\.**Identifies the parent certificate of the
lowest-level certificate.

Each CA certificate contains an issuer
field that identifies the parent CA that issued the certificate.

**2\.**Locates the PKI domain to which the parent
certificate belongs.

**3\.**Performs CRL checking in the PKI domain to check
whether the parent certificate has been revoked. If it has been revoked, the
certificate cannot be used.

This step will not be performed when CRL
checking is disabled in the PKI domain.

**4\.**Repeats the previous steps for upper-level
certificates in the CA certificate chain until the root CA certificate is
reached.

**5\.**Verifies that each CA certificate in the
certificate chain is issued by the named parent CA, starting from the root CA.

### Verifying certificates with CRL checking

**1\.**Enter system view.

system-view

**2\.**Enter PKI domain view.

pki domain domain-name

**3\.**(Optional.) Specify the URL of the CRL
repository.

crl url url-string \[ vpn-instance vpn-instance-name
]

By default, the URL of the CRL repository
is not specified. 

**4\.**Enable CRL checking.

crl check enable

By default, CRL checking is enabled.

**5\.**Return to system view.

quit

**6\.**Obtain the CA certificate.

See "[Obtaining certificates](#_Ref324259756)."

The PKI domain must have a CA certificate
before you can verify certificates in it.

**7\.**(Optional.) Obtain the CRL and save it
locally. 

pki retrieve-crl domain domain-name

To verify a non-root CA certificate and
local certificates, the device automatically retrieves the CRL if the PKI
domain has no CRL.

The newly obtained CRL overwrites the old
one, if any.

The obtained CRL is issued by a CA in the
CA certificate chain stored in the PKI domain.

**8\.**Manually verify the validity of the
certificates.

pki validate-certificate domain domain-name { ca \| local }

### Verifying certificates without CRL checking

**1\.**Enter system view.

system-view

**2\.**Enter PKI domain view.

pki domain domain-name

**3\.**Disable CRL checking.

undo crl check enable

By default, CRL checking is enabled.

**4\.**Return to system view.

quit

**5\.**Obtain a CA certificate for the PKI domain.

See "[Obtaining certificates](#_Ref324259756)."

The PKI domain must have a CA certificate
before you can verify certificates in it.

**6\.**Manually verify the certificate validity.

pki validate-certificate domain domain-name { ca \| local }

This command is not saved in the
configuration file. 

## Exporting certificates

#### About this task

You can export the CA certificate and the
local certificates in a PKI domain to certificate files. The exported certificate
files can then be imported back to the device or other PKI applications.

#### Restrictions and guidelines

To export all certificates in PKCS12
format, the PKI domain must have a minimum of one local certificate. If the PKI
domain does not have any local certificates, the certificates in the PKI domain
cannot be exported.

If you do not specify a file name when you
export a certificate in PEM format, this command displays the certificate
content on the terminal.

When you export a local certificate with
RSA key pairs to a file, the certificate file name might be different from the
file name specified in the command. The actual certificate file name depends on
the purpose of the key pair contained in the certificate. For more information
about the file naming rule, see the pki export
command in Security Command Reference.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Export certificates.

¡     Export
certificates in DER format.

pki export domain domain\-name der { all \| ca \| local } filename filename

¡     Export
certificates in PKCS12 format.

pki export domain domain\-name p12 { all \| local } passphrase p12-key filename filename

¡     Export
certificates in PEM format.

pki export domain domain\-name pem { { all \| local } \[ { 3des-cbc \| aes-128-cbc \| aes-192-cbc \| aes-256-cbc \| des-cbc } pem-key ] \| ca } \[ filename filename ]

## Removing a certificate

#### About this task

You can remove certificates from a PKI
domain in the following situations:

·     Remove a CA certificate, local certificate, or
peer certificate if the certificate has expired or is about to expire.

·     Remove a local certificate if the certificate's private
key is compromised, or if you want to request a new local certificate to
replace the existing one.

#### Restrictions and guidelines

After you remove the CA certificate, the
system automatically removes the local certificates, peer certificates, and
CRLs from the domain. 

To remove a local certificate and request a
new certificate, perform the following tasks:

**1\.**Remove the local certificate.

**2\.**Use the public-key local destroy command to destroy the existing
local key pair.

**3\.**Use the public-key local create command to generate a new key pair.

**4\.**Request a new certificate.

For more information about the  public-key
local destroy and public-key
local create commands, see Security Command Reference.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Remove a certificate.

pki delete-certificate domain domain\-name { ca \| local \| peer \[ serial serial-num ] }

If you use the peer keyword without specifying a serial number, this command removes
all peer certificates. 

## Configuring a certificate-based access control policy

### About certificate-based access control policies

Certificate-based access control
policies allow you to authorize access to a device (for example, an HTTPS
server) based on the attributes of an authenticated client's certificate. 

#### Access control rules and certificate attribute groups

A certificate-based access control policy is
a set of access control rules (permit or deny statements), each associated with
a certificate attribute group. A certificate attribute group contains multiple attribute
rules, each defining a matching criterion for an attribute in the certificate issuer
name, subject name, or alternative subject name field.

#### Certificate matching mechanism

If a certificate matches all attribute
rules in a certificate attribute group associated with an access control rule,
the system determines that the certificate matches the access control rule. In
this scenario, the match process stops, and the system performs the access
control action defined in the access control rule. 

The following conditions describe how a certificate-based
access control policy verifies the validity of a certificate:

·     If a certificate matches a permit statement, the
certificate passes the verification.

·     If a certificate matches a deny statement or
does not match any statements in the policy, the certificate is regarded
invalid.

·     If a statement is associated with a non-existing
attribute group, or the attribute group does not have attribute rules, the
certificate matches the statement. 

·     If the certificate-based access control policy specified
for a security application (for example, HTTPS) does not exist, all certificates
in the application pass the verification.

### Procedure

**1\.**Enter system view.

system-view

**2\.**Create a certificate attribute group and
enter its view.

pki certificate attribute-group group-name

**3\.**Configure an attribute rule for issuer name,
subject name, or alternative subject name. 

attribute id { alt-subject-name { fqdn \| ip } \| { issuer-name
\| subject-name } { dn \| fqdn \| ip } } { ctn \| equ \| nctn \| nequ} attribute-value

By default, not attribute rules are
configured. 

**4\.**Return to system view.

quit

**5\.**Create a certificate-based access control
policy and enter its view.

pki certificate access-control-policy policy-name

By default, no certificate-based access
control policies exist.

**6\.**Create a certificate access control rule.

rule \[ id ] { deny \| permit } group-name

By default, no certificate access control
rules are configured, and all certificates can pass the verification.

You can create multiple certificate
access control rules for a certificate-based access control policy.

## Enabling local certificate expiration notification

#### About this task

After this feature is enabled, the system
checks the validity date for local certificates every other hour. When a local
certificate is about to expire in 30 days (included) or has expired, the system
sends a notification log message for the certificate every other day.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enable local certificate expiration
notification.

pki certificate logging {
local-will-expire \| local-has-expired } enable

By default, local certificate expiration
notification is disabled.

## Display and maintenance commands for PKI

Execute display commands
in any view.

 

| Task | Command |
| --- | --- |
| Display certificate-based access control policy information. | display pki certificate access-control-policy \[ policy-name ] || Display certificate attribute group information. | display pki certificate attribute-group \[ group-name ] || Display the contents of a certificate. | display pki certificate domain domain-name { ca \| local \| peer \[ serial serial-num ] } || Display certificate request status. | display pki certificate request-status \[ domain domain-name ] || Display locally stored CRLs in a PKI domain. | display pki crl domain domain-name |





‌

## PKI configuration examples

You can use different software
applications, such as Windows server, RSA Keon, and OpenCA, to act as the CA
server.

If you use Windows server or OpenCA, you
must install the SCEP add-on for Windows server or enable SCEP for OpenCA. In either
case, when you configure a PKI domain, you must use the certificate
request from ra command to specify the RA to
accept certificate requests.

If you use RSA Keon, the SCEP add-on is not
required. When you configure a PKI domain, you must use the certificate
request from ca command to specify the CA to
accept certificate requests.

### Example: Requesting a certificate from an RSA Keon CA server

#### Network configuration

Configure the PKI entity (the device) to
request a local certificate from the CA server.

Figure 2 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705416_x_Img_x_png_1_2216073_294551_0.png)

‌

#### Configuring the RSA Keon CA server

**1\.**Create a CA server named myca:

In this example, you must configure these
basic attributes on the CA server:

¡     Nickname—Name of the trusted CA.

¡     Subject DN—DN attributes of the CA,
including the common name (CN), organization unit (OU), organization (O), and country
(C).

You can use the default values for other
attributes.

**2\.**Configure extended attributes:

Configure
parameters in theJurisdiction
Configuration section on the management page of
the CA server:

¡     Select
the correct extension profiles.

¡     Enable
the SCEP autovetting function to enable the CA server to automatically approve
certificate requests without manual intervention. 

¡     Specify
the IP address list for SCEP autovetting.

#### Configuring the device

**1\.**Synchronize the system time of the device
with the CA server for the device to correctly request certificates or obtain
CRLs. (Details not shown.)

**2\.**Create an entity named aaa
and set the common name to Device.

\<Device\> system-view

\[Device] pki entity aaa

\[Device-pki-entity-aaa] common-name
Device

\[Device-pki-entity-aaa] quit

**3\.**Configure a PKI domain:

\# Create a PKI domain named torsa and enter
its view.

\[Device] pki domain torsa

\# Specify the name of the trusted CA. The
setting must be the same as CA name configured on the CA server. This example
uses myca.

\[Device-pki-domain-torsa] ca
identifier myca

\# Configure the URL of the CA server. The
URL format is http://host:port/Issuing Jurisdiction ID, where Issuing Jurisdiction ID
is a hexadecimal string generated on the CA server.

\[Device-pki-domain-torsa]
certificate request url http://1.1.2.22:446/80f6214aa8865301d07929ae481c7ceed99f95bd

\# Configure the device to send
certificate requests to ca.

\[Device-pki-domain-torsa] certificate
request from ca

\# Set the PKI entity name to aaa.

\[Device-pki-domain-torsa] certificate
request entity aaa

\# Specify the URL of the CRL repository.

\[Device-pki-domain-torsa] crl
url ldap://1.1.2.22:389/CN\=myca

\# Specify a 1024-bit general-purpose RSA
key pair named abc for certificate request.

\[Device-pki-domain-torsa] public-key
rsa general name abc length 1024

\[Device-pki-domain-torsa] quit

**4\.**Generate the RSA key pair.

\[Device] public-key local
create rsa name abc

The range of public key
modulus is (512 \~ 4096).

If the key modulus is greater
than 512,it will take a few minutes.

Press CTRL\+C to abort.

Input the modulus length
\[default \= 1024]:

Generating Keys...

..........................\+\+\+\+\+\+

.....................................\+\+\+\+\+\+

Create the key pair
successfully.

**5\.**Request a local certificate:

\# Obtain the CA certificate and save it
locally.

\[Device] pki retrieve-certificate
domain torsa ca

The trusted CA's finger print
is:

    MD5  fingerprint:EDE9 0394
A273 B61A F1B3 0072 A0B1 F9AB

    SHA1 fingerprint: 77F9
A077 2FB8 088C 550B A33C 2410 D354 23B2 73A8

Is the finger print
correct?(Y/N):y

Retrieved the certificates
successfully.

\# Submit a certificate request manually
and set the certificate revocation password to 1111. The certificate revocation password
is required when an RSA Keon CA server is used.

\[Device] pki request-certificate
domain torsa password 1111

Start to request general
certificate ...

……

Request certificate of domain
torsa successfully

#### Verifying the configuration

\# Display information about the local certificate
in PKI domain torsa.

\[Device] display pki certificate
domain torsa local

Certificate:

    Data:

        Version: 3 (0x2)

        Serial Number:

           
15:79:75:ec:d2:33:af:5e:46:35:83:bc:bd:6e:e3:b8

        Signature Algorithm:
sha1WithRSAEncryption

        Issuer: CN\=myca

        Validity

            Not Before: Jan  6
03:10:58 2013 GMT

            Not After : Jan  6
03:10:58 2014 GMT

        Subject: CN\=Device

        Subject Public Key Info:

            Public Key Algorithm:
rsaEncryption

                Public-Key: (1024
bit)

                Modulus:

                    00:ab:45:64:a8:6c:10:70:3b:b9:46:34:8d:eb:1a:

                   
a1:b3:64:b2:37:27:37:9d:15:bd:1a:69:1d:22:0f:

                   
3a:5a:64:0c:8f:93:e5:f0:70:67:dc:cd:c1:6f:7a:

                   
0c:b1:57:48:55:81:35:d7:36:d5:3c:37:1f:ce:16:

                    7e:f8:18:30:f6:6b:00:d6:50:48:23:5c:8c:05:30:

                   
6f:35:04:37:1a:95:56:96:21:95:85:53:6f:f2:5a:

                   
dc:f8:ec:42:4a:6d:5c:c8:43:08:bb:f1:f7:46:d5:

                   
f1:9c:22:be:f3:1b:37:73:44:f5:2d:2c:5e:8f:40:

                   
3e:36:36:0d:c8:33:90:f3:9b

                Exponent: 65537
(0x10001)

        X509v3 extensions:

            X509v3 CRL Distribution
Points:

 

                Full Name:

                  DirName: CN \= myca

 

    Signature Algorithm:
sha1WithRSAEncryption

       
b0:9d:d9:ac:a0:9b:83:99:bf:9d:0a:ca:12:99:58:60:d8:aa:

       
73:54:61:4b:a2:4c:09:bb:9f:f9:70:c7:f8:81:82:f5:6c:af:

       
25:64:a5:99:d1:f6:ec:4f:22:e8:6a:96:58:6c:c9:47:46:8c:

        f1:ba:89:b8:af:fa:63:c6:c9:77:10:45:0d:8f:a6:7f:b9:e8:

       
25:90:4a:8e:c6:cc:b8:1a:f8:e0:bc:17:e0:6a:11:ae:e7:36:

       
87:c4:b0:49:83:1c:79:ce:e2:a3:4b:15:40:dd:fe:e0:35:52:

       
ed:6d:83:31:2c:c2:de:7c:e0:a7:92:61:bc:03:ab:40:bd:69:

        1b:f5

To display detailed information about the
CA certificate, use the display pki certificate domain command.

### Example: Requesting a certificate from a Windows Server 2003 CA server

#### Network configuration

Configure the PKI entity (the device) to
request a local certificate from a Windows Server 2003 CA server. 

Figure 3 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705417_x_Img_x_png_2_2216073_294551_0.png)

‌

#### Configuring the Windows Server 2003 CA server

**1\.**Install the certificate service component:

**a.**Select Control Panel
\> Add or Remove Programs from the start menu.

**b.**Select Add/Remove
Windows Components \> Certificate Services.

**c.**Click Next to
begin the installation.

**d.**Set the CA name. In this example, set the CA
name to myca.

**2\.**Install the SCEP add-on:

By default, Windows Server 2003 does not
support SCEP. You must install the SCEP add-on on the server for a PKI entity
to register and obtain a certificate from the server. After the SCEP add-on
installation is complete, you will see a URL. Specify this URL as the certificate
request URL on the device.

**3\.**Modify the certificate service attributes:

**a.**Select Control Panel
\> Administrative Tools \> Certificate Authority from the start menu.

If the certificate service component and
SCEP add-on have been installed successfully, there should be two certificates
issued by the CA to the RA.

**b.**Right-click the CA server in the navigation
tree and select Properties \> Policy Module.

**c.**Click Properties,
and then select Follow the settings in the certificate
template, if applicable. Otherwise, automatically issue the certificate.

**4\.**Modify the Internet information services
attributes:

**a.**Select Control Panel
\> Administrative Tools \> Internet Information Services (IIS) Manager from the
start menu.

**b.**Select Web Sites
from the navigation tree.

**c.**Right-click Default Web
Site and select Properties \> Home Directory.

**d.**Specify the path for certificate service in
the Local path field.

**e.**Specify a unique TCP port number for the
default website to avoid conflict with existing services. This example uses port
8080\.

#### Configuring the device

**1\.**Synchronize the device's system time with
the CA server for the device to correctly request certificates. (Details not
shown.)

**2\.**Create an entity named aaa
and set the common name to test.

\<Device\> system-view

\[Device] pki entity aaa

\[Device-pki-entity-aaa] common-name
test

\[Device-pki-entity-aaa] quit

**3\.**Configure a PKI domain:

\# Create a PKI domain named winserver and
enter its view.

\[Device] pki domain winserver

\# Set the name of the trusted CA to myca.

\[Device-pki-domain-winserver] ca
identifier myca

\# Configure the certificate request URL. The
URL format is http://host:port/certsrv/mscep/mscep.dll, where host:port is the host IP address and port number of the
CA server.

\[Device-pki-domain-winserver] certificate
request url http://4.4.4.1:8080/certsrv/mscep/mscep.dll

\# Configure the device to send
certificate requests to ra.

\[Device-pki-domain-winserver] certificate
request from ra

\# Set the PKI entity name to aaa.

\[Device-pki-domain-winserver] certificate
request entity aaa

\# Configure a 1024-bit general-purpose
RSA key pair named abc for certificate request.

\[Device-pki-domain-winserver]
public-key rsa general name abc length 1024

\[Device-pki-domain-winserver]
quit

**4\.**Generate RSA key pair abc.

\[Device] public-key local
create rsa name abc

The range of public key
modulus is (512 \~ 4096).

If the key modulus is greater
than 512,it will take a few minutes.

Press CTRL\+C to abort.

Input the modulus length
\[default \= 1024]:

Generating Keys...

..........................\+\+\+\+\+\+

.....................................\+\+\+\+\+\+

Create the key pair
successfully.

**5\.**Request a local certificate:

\# Obtain the CA certificate and save it
locally.

\[Device] pki retrieve-certificate
domain winserver ca

The trusted CA's finger print
is:

    MD5  fingerprint:766C D2C8
9E46 845B 4DCE 439C 1C1F 83AB

    SHA1 fingerprint:97E5 DDED
AB39 3141 75FB DB5C E7F8 D7D7 7C9B 97B4

Is the finger print correct?(Y/N):y

Retrieved the certificates
successfully.

\# Submit a certificate request manually.

\[Device] pki request-certificate
domain winserver

Start to request general
certificate ...

…

Request certificate of domain winserver
successfully

#### Verifying the configuration

\# Display information about the local
certificate in PKI domain winserver.

\[Device] display pki certificate
domain winserver local

Certificate:

    Data:

        Version: 3 (0x2)

        Serial Number:

            
(Negative)01:03:99:ff:ff:ff:ff:fd:11

        Signature Algorithm:
sha1WithRSAEncryption

        Issuer: CN\=sec

        Validity

            Not Before: Dec 24
07:09:42 2012 GMT

            Not After : Dec 24
07:19:42 2013 GMT

        Subject: CN\=test

        Subject Public Key Info:

            Public Key Algorithm:
rsaEncryption

                Public-Key: (2048
bit)

                Modulus:

                   
00:c3:b5:23:a0:2d:46:0b:68:2f:71:d2:14:e1:5a:

                   
55:6e:c5:5e:26:86:c1:5a:d6:24:68:02:bf:29:ac:

                   
dc:31:41:3f:5d:5b:36:9e:53:dc:3a:bc:0d:11:fb:

                   
d6:7d:4f:94:3c:c1:90:4a:50:ce:db:54:e0:b3:27:

                   
a9:6a:8e:97:fb:20:c7:44:70:8f:f0:b9:ca:5b:94:

                   
f0:56:a5:2b:87:ac:80:c5:cc:04:07:65:02:39:fc:

                   
db:61:f7:07:c6:65:4c:e4:5c:57:30:35:b4:2e:ed:

                   
9c:ca:0b:c1:5e:8d:2e:91:89:2f:11:e3:1e:12:8a:

                   
f8:dd:f8:a7:2a:94:58:d9:c7:f8:1a:78:bd:f5:42:

                   
51:3b:31:5d:ac:3e:c3:af:fa:33:2c:fc:c2:ed:b9:

                   
ee:60:83:b3:d3:e5:8e:e5:02:cf:b0:c8:f0:3a:a4:

                   
b7:ac:a0:2c:4d:47:5f:39:4b:2c:87:f2:ee:ea:d0:

                   
c3:d0:8e:2c:80:83:6f:39:86:92:98:1f:d2:56:3b:

                   
d7:94:d2:22:f4:df:e3:f8:d1:b8:92:27:9c:50:57:

                   
f3:a1:18:8b:1c:41:ba:db:69:07:52:c1:9a:3d:b1:

                   
2d:78:ab:e3:97:47:e2:70:14:30:88:af:f8:8e:cb:

                    68:f9:6f:07:6e:34:b6:38:6a:a2:a8:29:47:91:0e:

                    25:39

                Exponent: 65537
(0x10001)

        X509v3 extensions:

            X509v3 Key Usage:

                Digital Signature,
Non Repudiation, Key Encipherment, Data Encipherment

            X509v3 Subject Key
Identifier:

               
C9:BB:D5:8B:02:1D:20:5B:40:94:15:EC:9C:16:E8:9D:6D:FD:9F:34

            X509v3 Authority Key
Identifier:

               
keyid:32:F1:40:BA:9E:F1:09:81:BD:A8:49:66:FF:F8:AB:99:4A:30:21:9B

 

            X509v3 CRL Distribution
Points:

 

                Full Name:

                 
URI:file://\\\\g07904c\\CertEnroll\\sec.crl

 

            Authority Information
Access:

                CA Issuers \-
URI:http://gc/CertEnroll/gc\_sec.crt

                CA Issuers \-
URI:file://\\\\gc\\CertEnroll\\gc\_sec.crt

 

            1.3.6.1.4.1.311.20.2:

               
.0.I.P.S.E.C.I.n.t.e.r.m.e.d.i.a.t.e.O.f.f.l.i.n.e

    Signature Algorithm:
sha1WithRSAEncryption

       
76:f0:6c:2c:4d:bc:22:59:a7:39:88:0b:5c:50:2e:7a:5c:9d:

       
6c:28:3c:c0:32:07:5a:9c:4c:b6:31:32:62:a9:45:51:d5:f5:

       
36:8f:47:3d:47:ae:74:6c:54:92:f2:54:9f:1a:80:8a:3f:b2:

       
14:47:fa:dc:1e:4d:03:d5:d3:f5:9d:ad:9b:8d:03:7f:be:1e:

       
29:28:87:f7:ad:88:1c:8f:98:41:9a:db:59:ba:0a:eb:33:ec:

       
cf:aa:9b:fc:0f:69:3a:70:f2:fa:73:ab:c1:3e:4d:12:fb:99:

       
31:51:ab:c2:84:c0:2f:e5:f6:a7:c3:20:3c:9a:b0:ce:5a:bc:

       
0f:d9:34:56:bc:1e:6f:ee:11:3f:7c:b2:52:f9:45:77:52:fb:

       
46:8a:ca:b7:9d:02:0d:4e:c3:19:8f:81:46:4e:03:1f:58:03:

       
bf:53:c6:c4:85:95:fb:32:70:e6:1b:f3:e4:10:ed:7f:93:27:

       
90:6b:30:e7:81:36:bb:e2:ec:f2:dd:2b:bb:b9:03:1c:54:0a:

       
00:3f:14:88:de:b8:92:63:1e:f5:b3:c2:cf:0a:d5:f4:80:47:

       
6f:fa:7e:2d:e3:a7:38:46:f6:9e:c7:57:9d:7f:82:c7:46:06:

       
7d:7c:39:c4:94:41:bd:9e:5c:97:86:c8:48:de:35:1e:80:14:

        02:09:ad:08

To display detailed information about the
CA certificate, use the display pki certificate domain command.

### Example: Requesting a certificate from an OpenCA server

#### Network configuration

Configure the PKI entity (the device) to
request a local certificate from the CA server.

Figure 4 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705418_x_Img_x_png_3_2216073_294551_0.png)

‌

#### Configuring the OpenCA server

Configure the OpenCA server as instructed
in related manuals. (Details not shown.)

Make sure the version of the OpenCA server
is later than version 0.9.2 because the earlier versions do not support SCEP.

#### Configuring the device

**1\.**Synchronize the device's system time with the
CA server for the device to correctly request certificates. (Details not
shown.)

**2\.**Create a PKI entity named aaa and configure the common name, country code,
organization name, and OU for the entity.

\<Device\> system-view

\[Device] pki entity aaa

\[Device-pki-entity-aaa] common-name
rnd

\[Device-pki-entity-aaa] country
CN

\[Device-pki-entity-aaa] organization
test

\[Device-pki-entity-aaa] organization-unit
software

\[Device-pki-entity-aaa] quit

**3\.**Configure a PKI domain:

\# Create a PKI domain named openca and enter
its view.

\[Device] pki domain openca

\# Set the name of the trusted CA to myca.

\[Device-pki-domain-openca] ca
identifier myca

\# Configure the certificate request URL.
The URL is in the format http://host/cgi-bin/pki/scep, where host is the host IP address of the OpenCA server.

\[Device-pki-domain-openca]
certificate request url http://192.168.222.218/cgi-bin/pki/scep

\# Configure the device to send
certificate requests to the RA.

\[Device-pki-domain-openca] certificate
request from ra

\# Specify PKI entity aaa for
certificate request.

\[Device-pki-domain-openca] certificate
request entity aaa

\# Configure a 1024-bit general-purpose RSA
key pair named abc for certificate request.

\[Device-pki-domain-openca]
public-key rsa general name abc length 1024

\[Device-pki-domain-openca]
quit

**4\.**Generate RSA key pair abc.

\[Device] public-key local
create rsa name abc

The range of public key
modulus is (512 \~ 4096).

If the key modulus is greater
than 512,it will take a few minutes.

Press CTRL\+C to abort.

Input the modulus length
\[default \= 1024]:

Generating Keys...

..........................\+\+\+\+\+\+

.....................................\+\+\+\+\+\+

Create the key pair
successfully.

**5\.**Request a local certificate:

\# Obtain the CA certificate and save it
locally.

\[Device] pki
retrieve-certificate domain openca ca

The trusted CA's finger print
is:

    MD5  fingerprint:5AA3 DEFD
7B23 2A25 16A3 14F4 C81C C0FA

    SHA1 fingerprint:9668 4E63
D742 4B09 90E0 4C78 E213 F15F DC8E 9122

Is the finger print
correct?(Y/N):y

Retrieved the certificates
successfully.

\# Submit a certificate request manually.

\[Device] pki request-certificate
domain openca

Start to request general
certificate ...

…

Request certificate of domain openca
successfully

#### Verifying the configuration

\# Display information about the local certificate
in PKI domain openca.

\[Device] display pki certificate
domain openca local

Certificate:

    Data:

        Version: 3 (0x2)

        Serial Number:

           
21:1d:b8:d2:e4:a9:21:28:e4:de

        Signature Algorithm:
sha256WithRSAEncryption

        Issuer: C\=CN, L\=shangdi,
ST\=pukras, O\=OpenCA Labs, OU\=mysubUnit, CN\=sub-ca, DC\=pki-subdomain,
DC\=mydomain-sub, DC\=com

        Validity

            Not Before: Jun 30
09:09:09 2011 GMT

            Not After : May  1 09:09:09
2012 GMT

        Subject: CN\=rnd, O\=test,
OU\=software, C\=CN

        Subject Public Key Info:

            Public Key Algorithm:
rsaEncryption

                Public-Key: (1024
bit)

                Modulus:

                   
00:b8:7a:9a:b8:59:eb:fc:70:3e:bf:19:54:0c:7e:

                   
c3:90:a5:d3:fd:ee:ff:c6:28:c6:32:fb:04:6e:9c:

                   
d6:5a:4f:aa:bb:50:c4:10:5c:eb:97:1d:a7:9e:7d:

                   
53:d5:31:ff:99:ab:b6:41:f7:6d:71:61:58:97:84:

                    37:98:c7:7c:79:02:ac:a6:85:f3:21:4d:3c:8e:63:

                   
8d:f8:71:7d:28:a1:15:23:99:ed:f9:a1:c3:be:74:

                   
0d:f7:64:cf:0a:dd:39:49:d7:3f:25:35:18:f4:1c:

                   
59:46:2b:ec:0d:21:1d:00:05:8a:bf:ee:ac:61:03:

                   
6c:1f:35:b5:b4:cd:86:9f:45

                Exponent: 65537
(0x10001)

        X509v3 extensions:

            X509v3 Basic Constraints:

                CA:FALSE

            Netscape Cert Type:

                SSL Client, S/MIME

            X509v3 Key Usage:

                Digital Signature,
Non Repudiation, Key Encipherment

            X509v3 Extended Key
Usage:

                TLS Web Client
Authentication, E-mail Protection, Microsoft Smartcardlogin

Netscape Comment:

                User Certificate of
OpenCA Labs

            X509v3 Subject Key
Identifier:

               
24:71:C9:B8:AD:E1:FE:54:9A:EA:E9:14:1B:CD:D9:45:F4:B2:7A:1B

            X509v3 Authority Key
Identifier:

               
keyid:85:EB:D5:F7:C9:97:2F:4B:7A:6D:DD:1B:4D:DD:00:EE:53:CF:FD:5B

 

            X509v3 Issuer Alternative
Name:

                DNS:[\[email protected]](/cdn-cgi/l/email-protection),
DNS:, IP Address:192.168.154.145, IP Address:192.168.154.138

            Authority Information
Access:

                CA Issuers \-
URI:http://192.168.222.218/pki/pub/cacert/cacert.crt

                OCSP \-
URI:http://192.168.222.218:2560/

                1.3.6.1.5.5.7.48.12 \-
URI:http://192.168.222.218:830/

 

            X509v3 CRL Distribution
Points:

 

                Full Name:

                 
URI:http://192.168.222.218/pki/pub/crl/cacrl.crl

 

    Signature Algorithm:
sha256WithRSAEncryption

       
5c:4c:ba:d0:a1:35:79:e6:e5:98:69:91:f6:66:2a:4f:7f:8b:

       
0e:80:de:79:45:b9:d9:12:5e:13:28:17:36:42:d5:ae:fc:4e:

        ba:b9:61:f1:0a:76:42:e7:a6:34:43:3e:2d:02:5e:c7:32:f7:

       
6b:64:bb:2d:f5:10:6c:68:4d:e7:69:f7:47:25:f5:dc:97:af:

       
ae:33:40:44:f3:ab:e4:5a:a0:06:8f:af:22:a9:05:74:43:b6:

       
e4:96:a5:d4:52:32:c2:a8:53:37:58:c7:2f:75:cf:3e:8e:ed:

        46:c9:5a:24:b1:f5:51:1d:0f:5a:07:e6:15:7a:02:31:05:8c:

       
03:72:52:7c:ff:28:37:1e:7e:14:97:80:0b:4e:b9:51:2d:50:

       
98:f2:e4:5a:60:be:25:06:f6:ea:7c:aa:df:7b:8d:59:79:57:

       
8f:d4:3e:4f:51:c1:34:e6:c1:1e:71:b5:0d:85:86:a5:ed:63:

        1e:08:7f:d2:50:ac:a0:a3:9e:88:48:10:0b:4a:7d:ed:c1:03:

       
9f:87:97:a3:5e:7d:75:1d:ac:7b:6f:bb:43:4d:12:17:9a:76:

       
b0:bf:2f:6a:cc:4b:cd:3d:a1:dd:e0:dc:5a:f3:7c:fb:c3:29:

       
b0:12:49:5c:12:4c:51:6e:62:43:8b:73:b9:26:2a:f9:3d:a4:

        81:99:31:89

To display detailed information about the
CA certificate, use the display pki certificate domain command.

### Example: Configuring IKE negotiation with RSA digital signature from a Windows Server 2003 CA server

#### Network configuration

As shown in the network diagram, an IPsec
tunnel is required to be established between Device A and Device B. The IPsec
tunnel protects the traffic between Host A on subnet 10.1.1.0/24 and Host B on
subnet 1.1.1.0/24.

Device A and Device B use IKE to set up
SAs, and the IKE proposal uses RSA digital signature for identity
authentication.

Device A and Device B use the same CA.

Figure 5 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705419_x_Img_x_png_4_2216073_294551_0.png)

‌

#### Configuring the Windows Server 2003 CA server

See "[Example: Requesting a certificate from a
Windows Server 2003 CA server](#_Ref324342344)."

#### Configuring Device A

\# Configure a PKI entity.

\<DeviceA\> system-view

\[DeviceA] pki entity en

\[DeviceA-pki-entity-en] ip 2.2.2.1

\[DeviceA-pki-entity-en] common-name devicea

\[DeviceA-pki-entity-en] quit

\# Configure a PKI domain.

\[DeviceA] pki domain 1

\[DeviceA-pki-domain-1] ca identifier CA1

\[DeviceA-pki-domain-1] certificate
request url http://1.1.1.100/certsrv/mscep/mscep.dll

\[DeviceA-pki-domain-1] certificate
request entity en

\[DeviceA-pki-domain-1] ldap-server host
1.1.1.102

\# Configure the device to send certificate
requests to ra.

\[DeviceA-pki-domain-1] certificate
request from ra

\# Configure a 1024-bit general-purpose RSA
key pair named abc for certificate request.

\[DeviceA-pki-domain-1] public-key rsa
general name abc length 1024

\[DeviceA-pki-domain-1] quit

\# Generate the RSA key pair.

\[DeviceA] public-key local create rsa
name abc

The range of public key modulus is
(512 \~ 4096).

If the key modulus is greater than
512,it will take a few minutes.

Press CTRL\+C to abort.

Input the modulus length \[default \=
1024]:

Generating Keys...

..........................\+\+\+\+\+\+

.....................................\+\+\+\+\+\+

Create the key pair successfully.

\# Obtain the CA certificate and save it
locally.

\[DeviceA] pki retrieve-certificate
domain 1 ca

\# Submit a certificate request manually.

\[DeviceA] pki request-certificate domain
1

\# Create IKE proposal 1, and configure the
authentication method as RSA digital signature.

\[DeviceA] ike proposal 1

\[DeviceA-ike-proposal-1]
authentication-method rsa-signature

\[DeviceA-ike-proposal-1] quit

\# Specify the PKI domain used in IKE
negotiation for IKE profile peer.

\[DeviceA] ike profile peer

\[DeviceA-ike-profile-peer] certificate
domain 1

#### Configuring Device B

\# Configure a PKI entity.

\<DeviceB\> system-view

\[DeviceB] pki entity en

\[DeviceB-pki-entity-en] ip 3.3.3.1

\[DeviceB-pki-entity-en] common-name deviceb

\[DeviceB-pki-entity-en] quit

\# Configure a PKI domain. 

\[DeviceB] pki domain 1

\[DeviceB-pki-domain-1] ca identifier CA1

\[DeviceB-pki-domain-1] certificate
request url http://1.1.1.100/certsrv/mscep/mscep.dll

\[DeviceB-pki-domain-1] certificate
request entity en

\[DeviceB-pki-domain-1] ldap-server host
1.1.1.102

\# Configure the device to send certificate
requests to ra.

\[DeviceB-pki-domain-1] certificate
request from ra

\# Configure a 1024-bit general-purpose RSA
key pair named abc for certificate request.

\[DeviceB-pki-domain-1] public-key rsa
general name abc length 1024

\[DeviceB-pki-domain-1] quit

\# Generate the RSA key pair.

\[DeviceB] public-key local create rsa
name abc

The range of public key modulus is
(512 \~ 4096).

If the key modulus is greater than
512,it will take a few minutes.

Press CTRL\+C to abort.

Input the modulus length \[default \=
1024]:

Generating Keys...

..........................\+\+\+\+\+\+

.....................................\+\+\+\+\+\+

Create the key pair successfully.

\# Obtain the CA certificate and save it
locally.

\[DeviceB] pki retrieve-certificate
domain 1 ca

The trusted CA's finger print is:

    MD5  fingerprint:5C41 E657 A0D6
ECB4 6BD6 1823 7473 AABC

    SHA1 fingerprint:1616 E7A5 D89A
2A99 9419 1C12 D696 8228 87BC C266

Is the finger print correct?(Y/N):y

Retrieved the certificates
successfully.

\# Submit a certificate request manually.

\[DeviceB] pki request-certificate domain
1

Start to request general certificate
...

...

Certificate requested successfully.

\# Create IKE proposal 1, and configure the
authentication method as RSA digital signature.

\[DeviceB] ike proposal 1

\[DeviceB-ike-proposal-1]
authentication-method rsa-signature

\[DeviceB-ike-proposal-1] quit

\# Specify the PKI domain used in IKE
negotiation for IKE profile peer.

\[DeviceB] ike profile peer

\[DeviceB-ike-profile-peer] certificate
domain 1

The configurations are for IKE negotiation
with RSA digital signature. For information about how to configure IPsec SAs to
be set up, see "Configuring IPsec."

### Example: Importing and exporting certificates

#### Network configuration

As shown in [Figure 6](#_Ref385354764), Device
B will replace Device A in the network. PKI domain exportdomain
on Device A has two local certificates containing the private key and one CA
certificate. To make sure the certificates are still valid after Device B replaces
Device A, copy the certificates on Device A to Device B as follows:

**1\.**Export the certificates in PKI domain exportdomain on Device A to .pem certificate files.

During the export, encrypt the private
key in the local certificates using 3DES\_CBC with the password 11111\.

**2\.**Transfer the certificate files from Device A
to Device B through the FTP host.

**3\.**Import the certificate files to PKI domain importdomain on Device B.

Figure 6 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705420_x_Img_x_png_5_2216073_294551_0.png)

‌

#### Procedure

**1\.**Export the certificates on Device A:

\# Export the CA certificate to a .pem
file.

\<DeviceA\> system-view

\[DeviceA] pki export domain
exportdomain pem ca filename pkicachain.pem

\# Export the local certificate to a file
named pkilocal.pem in PEM format, and use 3DES\_CBC to encrypt the private key with the
password 111111.

\[DeviceA] pki export domain
exportdomain pem local 3des-cbc 111111 filename pkilocal.pem

Now, Device A has three certificate files
in PEM format:

¡     A CA
certificate file named pkicachain.pem. 

¡     A
local certificate file named pkilocal.pem-signature,
which contains the private key for signature.

¡     A
local certificate file named pkilocal.pem-encryption,
which contains the private key for encryption.

\# Display local certificate file pkilocal.pem-signature.

\[DeviceA] quit

\<DeviceA\> more pkilocal.pem-signature

Bag Attributes

    friendlyName:

    localKeyID: 90 C6 DC 1D 20
49 4F 24 70 F5 17 17 20 2B 9E AC 20 F3 99 89

subject\=/C\=CN/O\=OpenCA Labs/OU\=Users/CN\=subsign
11

issuer\=/C\=CN/L\=shangdi/ST\=pukras/O\=OpenCA
Labs/OU\=docm/CN\=subca1

-----BEGIN CERTIFICATE-----

MIIEgjCCA2qgAwIBAgILAJgsebpejZc5UwAwDQYJKoZIhvcNAQELBQAwZjELMAkG

…

-----END CERTIFICATE-----

Bag Attributes

    friendlyName:

    localKeyID: 90 C6 DC 1D 20
49 4F 24 70 F5 17 17 20 2B 9E AC 20 F3 99 89

Key Attributes: \<No
Attributes\>

-----BEGIN ENCRYPTED PRIVATE
KEY-----

MIICxjBABgkqhkiG9w0BBQ0wMzAbBgkqhkiG9w0BBQwwDgQIZtjSjfslJCoCAggA

…

-----END ENCRYPTED PRIVATE
KEY-----

\# Display local certificate file pkilocal.pem-encryption.

\<DeviceA\> more pkilocal.pem-encryption

Bag Attributes

    friendlyName:

    localKeyID: D5 DF 29 28 C8
B9 D9 49 6C B5 44 4B C2 BC 66 75 FE D6 6C C8

subject\=/C\=CN/O\=OpenCA
Labs/OU\=Users/CN\=subencr 11

issuer\=/C\=CN/L\=shangdi/ST\=pukras/O\=OpenCA
Labs/OU\=docm/CN\=subca1

-----BEGIN CERTIFICATE-----

MIIEUDCCAzigAwIBAgIKCHxnAVyzWhIPLzANBgkqhkiG9w0BAQsFADBmMQswCQYD

…

-----END CERTIFICATE-----

Bag Attributes

    friendlyName:

    localKeyID: D5 DF 29 28 C8
B9 D9 49 6C B5 44 4B C2 BC 66 75 FE D6 6C C8

Key Attributes: \<No
Attributes\>

-----BEGIN ENCRYPTED PRIVATE
KEY-----

MIICxjBABgkqhkiG9w0BBQ0wMzAbBgkqhkiG9w0BBQwwDgQI7H0mb4O7/GACAggA

…

-----END ENCRYPTED PRIVATE
KEY-----

**2\.**Download certificate files pkicachain.pem, pkilocal.pem-signature,
and pkilocal.pem-encryption from Device A to the
host through FTP. (Details not shown.)

**3\.**Upload certificate files pkicachain.pem, pkilocal.pem-signature,
and pkilocal.pem-encryption from the host to Device
B through FTP. (Details not shown.)

**4\.**Import the certificate files to Device B:

\# Disable CRL checking. (You can
configure CRL checking as required. This example assumes CRL checking is not
required.)

\<DeviceB\> system-view

\[DeviceB] pki domain
importdomain

\[DeviceB-pki-domain-importdomain]
undo crl check enable

\# Specify RSA key pair sign for
signature and RSA key pair encr for encryption.

\[DeviceB-pki-domain-importdomain]
public-key rsa signature name sign encryption name encr

\[DeviceB-pki-domain-importdomain]
quit

\# Import CA certificate file pkicachain.pem
in PEM format to the PKI domain.

\[DeviceB] pki import domain
importdomain pem ca filename pkicachain.pem

\# Import local certificate file pkilocal.pem-signature in PEM format to the PKI domain. The certificate file contains a key
pair.

\[DeviceB] pki import domain
importdomain pem local filename pkilocal.pem-signature

Please input the
password:\*\*\*\*\*\*

\# Import local certificate file pkilocal.pem-encryption in PEM format to the PKI domain. The certificate file contains a key
pair.

\[DeviceB] pki import domain
importdomain pem local filename pkilocal.pem-encryption

Please input the
password:\*\*\*\*\*\*

\# Display the imported local certificate information
on Device B.

\[DeviceB] display pki
certificate domain importdomain local

Certificate:

    Data:

        Version: 3 (0x2)

        Serial Number:

           
98:2c:79:ba:5e:8d:97:39:53:00

        Signature Algorithm:
sha256WithRSAEncryption

        Issuer: C\=CN,
L\=shangdi, ST\=pukras, O\=OpenCA Labs, OU\=docm, CN\=subca1

        Validity

            Not Before: May 26
05:56:49 2011 GMT

            Not After : Nov 22
05:56:49 2012 GMT

        Subject: C\=CN,
O\=OpenCA Labs, OU\=Users, CN\=subsign 11

        Subject Public Key
Info:

            Public Key
Algorithm: rsaEncryption

                Public-Key:
(1024 bit)

                Modulus:

                   
00:9f:6e:2f:f6:cb:3d:08:19:9a:4a:ac:b4:ac:63:

                   
ce:8d:6a:4c:3a:30:19:3c:14:ff:a9:50:04:f5:00:

                   
ee:a3:aa:03:cb:b3:49:c4:f8:ae:55:ee:43:93:69:

                   
6c:bf:0d:8c:f4:4e:ca:69:e5:3f:37:5c:83:ea:83:

                   
ad:16:b8:99:37:cb:86:10:6b:a0:4d:03:95:06:42:

                   
ef:ef:0d:4e:53:08:0a:c9:29:dd:94:28:02:6e:e2:

                   
9b:87:c1:38:2d:a4:90:a2:13:5f:a4:e3:24:d3:2c:

                    bf:98:db:a7:c2:36:e2:86:90:55:c7:8c:c5:ea:12:

                   
01:31:69:bf:e3:91:71:ec:21

                Exponent:
65537 (0x10001)

        X509v3 extensions:

            X509v3 Basic
Constraints:

                CA:FALSE

            Netscape Cert
Type:

                SSL Client,
S/MIME

            X509v3 Key Usage:

                Digital
Signature, Non Repudiation

            X509v3 Extended
Key Usage:

                TLS Web Client
Authentication, E-mail Protection, Microsoft Smartcardlogin

            Netscape Comment:

                User
Certificate of OpenCA Labs

            X509v3 Subject Key
Identifier:

               
AA:45:54:29:5A:50:2B:89:AB:06:E5:BD:0D:07:8C:D9:79:35:B1:F5

            X509v3 Authority
Key Identifier:

               
keyid:70:54:40:61:71:31:02:06:8C:62:11:0A:CC:A5:DB:0E:7E:74:DE:DD

 

            X509v3 Subject
Alternative Name:

                email:[\[email protected]](/cdn-cgi/l/email-protection)

            X509v3 Issuer Alternative
Name:

                DNS:[\[email protected]](/cdn-cgi/l/email-protection),
DNS:, IP Address:1.1.2.2, IP Address:2.2.1.1

            Authority
Information Access:

                CA Issuers \-
URI:http://titan/pki/pub/cacert/cacert.crt

                OCSP \-
URI:http://titan:2560/

               
1.3.6.1.5.5.7.48.12 \- URI:http://titan:830/

 

            X509v3 CRL
Distribution Points:

 

                Full Name:

                 
URI:http://192.168.40.130/pki/pub/crl/cacrl.crl

 

    Signature Algorithm:
sha256WithRSAEncryption

       
18:e7:39:9a:ad:84:64:7b:a3:85:62:49:e5:c9:12:56:a6:d2:

       
46:91:53:8e:84:ba:4a:0a:6f:28:b9:43:bc:e7:b0:ca:9e:d4:

       
1f:d2:6f:48:c4:b9:ba:c5:69:4d:90:f3:15:c4:4e:4b:1e:ef:

       
2b:1b:2d:cb:47:1e:60:a9:0f:81:dc:f2:65:6b:5f:7a:e2:36:

        29:5d:d4:52:32:ef:87:50:7c:9f:30:4a:83:de:98:8b:6a:c9:

       
3e:9d:54:ee:61:a4:26:f3:9a:40:8f:a6:6b:2b:06:53:df:b6:

       
5f:67:5e:34:c8:c3:b5:9b:30:ee:01:b5:a9:51:f9:b1:29:37:

       
02:1a:05:02:e7:cc:1c:fe:73:d3:3e:fa:7e:91:63:da:1d:f1:

        db:28:6b:6c:94:84:ad:fc:63:1b:ba:53:af:b3:5d:eb:08:b3:

       
5b:d7:22:3a:86:c3:97:ef:ac:25:eb:4a:60:f8:2b:a3:3b:da:

       
5d:6f:a5:cf:cb:5a:0b:c5:2b:45:b7:3e:6e:39:e9:d9:66:6d:

       
ef:d3:a0:f6:2a:2d:86:a3:01:c4:94:09:c0:99:ce:22:19:84:

        2b:f0:db:3e:1e:18:fb:df:56:cb:6f:a2:56:35:0d:39:94:34:

       
6d:19:1d:46:d7:bf:1a:86:22:78:87:3e:67:fe:4b:ed:37:3d:

        d6:0a:1c:0b

 

Certificate:

    Data:

        Version: 3 (0x2)

        Serial Number:

           
08:7c:67:01:5c:b3:5a:12:0f:2f

        Signature Algorithm:
sha256WithRSAEncryption

        Issuer: C\=CN,
L\=shangdi, ST\=pukras, O\=OpenCA Labs, OU\=docm, CN\=subca1

        Validity

            Not Before: May 26
05:58:26 2011 GMT

            Not After : Nov 22
05:58:26 2012 GMT

        Subject: C\=CN,
O\=OpenCA Labs, OU\=Users, CN\=subencr 11

        Subject Public Key
Info:

            Public Key
Algorithm: rsaEncryption

                Public-Key:
(1024 bit)

                Modulus:

                   
00:db:26:13:d3:d1:a4:af:11:f3:6d:37:cf:d0:d4:

                   
48:50:4e:0f:7d:54:76:ed:50:28:c6:71:d4:48:ae:

                   
4d:e7:3d:23:78:70:63:18:33:f6:94:98:aa:fa:f6:

                   
62:ed:8a:50:c6:fd:2e:f4:20:0c:14:f7:54:88:36:

                   
2f:e6:e2:88:3f:c2:88:1d:bf:8d:9f:45:6c:5a:f5:

                   
94:71:f3:10:e9:ec:81:00:28:60:a9:02:bb:35:8b:

                   
bf:85:75:6f:24:ab:26:de:47:6c:ba:1d:ee:0d:35:

                   
75:58:10:e5:e8:55:d1:43:ae:85:f8:ff:75:81:03:

                   
8c:2e:00:d1:e9:a4:5b:18:39

                Exponent:
65537 (0x10001)

        X509v3 extensions:

            X509v3 Basic
Constraints:

                CA:FALSE

            Netscape Cert
Type:

                SSL Server

            X509v3 Key Usage:

                Key Encipherment,
Data Encipherment

            Netscape Comment:

                VPN Server of
OpenCA Labs

            X509v3 Subject Key
Identifier:

               
CC:96:03:2F:FC:74:74:45:61:38:1F:48:C0:E8:AA:18:24:F0:2B:AB

            X509v3 Authority
Key Identifier:

               
keyid:70:54:40:61:71:31:02:06:8C:62:11:0A:CC:A5:DB:0E:7E:74:DE:DD

 

            X509v3 Subject
Alternative Name:

                email:[\[email protected]](/cdn-cgi/l/email-protection)

            X509v3 Issuer
Alternative Name:

                DNS:[\[email protected]](/cdn-cgi/l/email-protection),
DNS:, IP Address:1.1.2.2, IP Address:2.2.1.1

            Authority
Information Access:

                CA Issuers \-
URI:http://titan/pki/pub/cacert/cacert.crt

                OCSP \-
URI:http://titan:2560/

                1.3.6.1.5.5.7.48.12
\- URI:http://titan:830/

 

            X509v3 CRL
Distribution Points:

 

                Full Name:

                 
URI:http://192.168.40.130/pki/pub/crl/cacrl.crl

 

    Signature Algorithm:
sha256WithRSAEncryption

        53:69:66:5f:93:f0:2f:8c:54:24:8f:a2:f2:f1:29:fa:15:16:

       
90:71:e2:98:e3:5c:c6:e3:d4:5f:7a:f6:a9:4f:a2:7f:ca:af:

       
c4:c8:c7:2c:c0:51:0a:45:d4:56:e2:81:30:41:be:9f:67:a1:

       
23:a6:09:50:99:a1:40:5f:44:6f:be:ff:00:67:9d:64:98:fb:

        72:77:9e:fd:f2:4c:3a:b2:43:d8:50:5c:48:08:e7:77:df:fb:

       
25:9f:4a:ea:de:37:1e:fb:bc:42:12:0a:98:11:f2:d9:5b:60:

       
bc:59:72:04:48:59:cc:50:39:a5:40:12:ff:9d:d0:69:3a:5e:

       
3a:09:5a:79:e0:54:67:a0:32:df:bf:72:a0:74:63:f9:05:6f:

        5e:28:d2:e8:65:49:e6:c7:b5:48:7d:95:47:46:c1:61:5a:29:

       
90:65:45:4a:88:96:e4:88:bd:59:25:44:3f:61:c6:b1:08:5b:

       
86:d2:4f:61:4c:20:38:1c:f4:a1:0b:ea:65:87:7d:1c:22:be:

       
b6:17:17:8a:5a:0f:35:4c:b8:b3:73:03:03:63:b1:fc:c4:f5:

       
e9:6e:7c:11:e8:17:5a:fb:39:e7:33:93:5b:2b:54:72:57:72:

       
5e:78:d6:97:ef:b8:d8:6d:0c:05:28:ea:81:3a:06:a0:2e:c3:

        79:05:cd:c3

To display detailed information about the
CA certificate, use the display pki certificate domain command.

## Troubleshooting PKI configuration

This section
provides troubleshooting information for common problems with PKI.

### Failed to obtain the CA certificate

#### Symptom

The CA certificate cannot be obtained.

#### Analysis

·     The network connection is down, for example,
because the network cable is damaged or the connectors have bad contact.

·     No trusted CA is specified.

·     The certificate request URL is incorrect or not specified.

·     The system time of the device is not synchronized
with the CA server.

·     The CA server does not accept the source IP
address specified in the PKI domain, or no source IP address is specified. 

·     The fingerprint of the root CA certificate is
illegal.

#### Solution

**1\.**Fix the network connection problems, if any.

**2\.**Configure the trusted CA and all other
required parameters in the PKI domain.

**3\.**Use the ping command
to verify that the CA server is reachable.

**4\.**Synchronize the system time of the device with
the CA server.

**5\.**Specify the correct source IP address that
the CA server can accept. For the correct settings, contact the CA
administrator.

**6\.**Verify the fingerprint of the CA certificate
on the CA server.

**7\.**If the problem persists, contact H3C
Support.

### Failed to obtain local certificates

#### Symptom

The local certificates can be obtained.

#### Analysis

·     The network connection is down.

·     The PKI domain does not have a CA certificate before
you submit the local certificate request.

·     The LDAP server is not configured or is incorrectly
configured.

·     No key pair is specified for certificate request
in the PKI domain, or the specified key pair does not match the one contained
in the local certificates to the obtained.

·     No PKI entity is configured in the PKI domain, or
the PKI entity configuration is incorrect.

·     CRL checking is enabled, but the PKI domain does
not have a CRL and cannot obtain one.

·     The CA server does not accept the source IP
address specified in the PKI domain, or no source IP address is specified. 

·     The system time of the device is not
synchronized with the CA server.

#### Solution

**1\.**Fix the network connection problems, if any..

**2\.**Obtain or import the CA certificate.

**3\.**Configure the correct LDAP server parameters.

**4\.**Specify the key pair for certificate
request, or remove the existing key pair, specify a new key pair, and submit a local
certificate request again. 

**5\.**Check the registration policy on the CA or
RA, and make sure the attributes of the PKI entity meet the policy
requirements.

**6\.**Obtain the CRL from the CRL repository. 

**7\.**Specify the correct source IP address that
the CA server can accept. For the correct settings, contact the CA
administrator.

**8\.**Synchronize the system time of the device with
the CA server.

**9\.**If the problem persists, contact H3C
Support.

### Failed to request local certificates

#### Symptom

Local certificate requests cannot be
submitted.

#### Analysis

·     The network connection is down, for example, because
the network cable is damaged or the connectors have bad contact.

·     The PKI domain does not have a CA certificate before
the local certificate request is submitted.

·     The certificate request URL is incorrect or is not
specified.

·     The certificate request reception authority is incorrect
or is not specified.

·     Required PKI entity parameters are not
configured or are incorrectly configured.

·     No key pair is specified in the PKI domain for
certificate request, or the key pair is changed during a certificate request
process.

·     Exclusive certificate request applications are
running in the PKI domain.

·     The CA server does not accept the source IP
address specified in the PKI domain, or no source IP address is specified. 

·     The system time of the device is not
synchronized with the CA server.

#### Solution

**1\.**Fix the network connection problems, if any.

**2\.**Obtain or import the CA certificate.

**3\.**Use the ping command
to verify that the registration server is reachable.

**4\.**Use the certificate request from command to specify the correct certificate request reception
authority.

**5\.**Configure the PKI entity parameters as required
by the registration policy on the CA or RA.

**6\.**Specify the key pair for certificate
request, or remove the existing key pair, specify a new key pair, and submit a local
certificate request again. 

**7\.**Use the pki abort-certificate-request domain command to abort the certificate request.

**8\.**Specify the correct source IP address that
the CA server can accept. For the correct settings, contact the CA
administrator.

**9\.**Synchronize the system time of the device with
the CA server.

**10\.**If the problem persists, contact H3C
Support.

### Failed to obtain CRLs

#### Symptom

CRLs cannot be obtained.

#### Analysis

·     The network connection is down, for example,
because the network cable is damaged or the connectors have bad contact.

·     The PKI domain does not have a CA certificate before
you try to obtain CRLs.

·     The URL of the CRL repository is not configured
and cannot be obtained from the CA certificate or local certificates in the PKI
domain.

·     The specified URL of the CRL repository is
incorrect.

·     The device tries to obtain CRLs through SCEP, but
it experiences the following problems:

¡     The
PKI domain does not have local certificates.

¡     The
key pairs in the certificates have been changed.

¡     The
PKI domain has incorrect URL for certificate request.

·     The CRL repository uses LDAP for CRL
distribution. However, the IP address or host name of the LDAP server is
neither contained in the CRL repository URL nor configured in the PKI domain.

·     The CA does not issue CRLs.

·     The CA server does not accept the source IP
address specified in the PKI domain, or no source IP address is specified. 

#### Solution

**1\.**Fix the network connection problems, if any.

**2\.**Obtain or import the CA certificate.

**3\.**If the URL of the CRL repository cannot be
obtained, verify that the following conditions exist:

¡     The URL
for certificate request is valid.

¡     A
local certificate has been successfully obtained.

¡     The
local certificate contains a public key that matches the locally stored key
pair.

**4\.**Make sure the LDAP server address is
contained in the CRL repository URL, or is configured in the PKI domain.

**5\.**Make sure the CA server support publishing
CRLs.

**6\.**Specify a correct source IP address that the
CA server can accept. For the correct settings, contact the CA administrator.

**7\.**If the problem persists, contact H3C
Support.

### Failed to import the CA certificate

#### Symptom

The CA certificate cannot be imported.

#### Analysis

·     CRL checking is enabled, but the device does not
have a CRL in the PKI domain and cannot obtain one.

·     The specified format in which the CA certificate
file is to be imported does not match actual certificate file format.

#### Solution

**1\.**Use the undo crl check enable command to disable CRL checking in the PKI domain.

**2\.**Make sure the format of the imported file is
correct.

**3\.**If the problem persists, contact H3C
Support.

### Failed to import the local certificate

#### Symptom

The local certificate cannot be imported.

#### Analysis

·     The PKI domain does not have a CA certificate,
and the local certificate file to be imported does not contain the CA
certificate chain.

·     CRL checking is enabled, but the device does not
have a CRL in the PKI domain and cannot obtain one.

·     The specified format in which the local
certificate file is to be imported does not match actual certificate file
format.

·     The device and the certificate do not have the local
key pair.

·     The certificate has been revoked.

·     The certificate is out of the validity period.

·     The system time is incorrect.

#### Solution

**1\.**Obtain or import the CA certificate.

**2\.**Use the undo crl check enable command to disable CRL checking, or obtain the correct CRL before
you import certificates.

**3\.**Make sure the format of the file to be
imported is correct.

**4\.**Make sure the certificate file contains the
private key.

**5\.**Make sure the certificate is not revoked.

**6\.**Make sure the certificate is valid.

**7\.**Configure the correct system time for the
device.

**8\.**If the problem persists, contact H3C
Support.

### Failed to export certificates

#### Symptom

Certificates cannot be exported.

#### Analysis

·     The PKI domain does not have local certificates
when you export all certificates in PKCS12 format.

·     The specified export path does not exist.

·     The specified export path is illegal.

·     The public key of the local certificate to be
exported does not match the public key of the key pair configured in the PKI
domain.

·     The storage space of the device is full.

#### Solution

**1\.**Obtain or request local certificates first.

**2\.**Use the mkdir command
to create the required path.

**3\.**Specify a correct export path.

**4\.**Configure the correct key pair in the PKI
domain.

**5\.**Clear up the storage space of the device.

**6\.**If the problem persists, contact H3C
Support.

### Failed to set the storage path

#### Symptom

The storage path for certificates or CRLs
cannot be set.

#### Analysis

·     The specified storage path does not exist.

·     The specified storage path is illegal.

·     The storage space of the device is full.

#### Solution

**1\.**Use the mkdir command
to create the path.

**2\.**Specify a valid storage path for
certificates or CRLs.

**3\.**Clear up the storage space of the device.

**4\.**If the problem persists, contact H3C
Support.

