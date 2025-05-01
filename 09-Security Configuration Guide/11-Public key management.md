
# Managing public keys

## About public key management

This chapter describes public key
management for the following asymmetric key algorithms:

·     Revest-Shamir-Adleman Algorithm (RSA).

·     Digital Signature Algorithm (DSA).

·     Elliptic Curve Digital Signature Algorithm (ECDSA).

### Asymmetric key algorithm overview

Asymmetric key algorithms are used by
security applications to secure communications between two parties, as shown in
[Figure 1](#_Ref206494247).
Asymmetric key algorithms use two separate keys (one public and one private)
for encryption and decryption. Symmetric key algorithms use only one key.

Figure 1 Encryption and decryption

![](https://resource.h3c.com/en/202407/12/20240712_11705411_x_Img_x_png_0_2216072_294551_0.png)

‌

A key owner can distribute the public key in
plain text on the network but must keep the private key in privacy. It is
mathematically infeasible to calculate the private key even if an attacker
knows the algorithm and the public key.

### Usage of asymmetric key algorithms

Security applications (such as SSH, SSL,
and PKI) use the asymmetric key algorithms for the following purposes:

·     Encryption and decryption—Any public key receiver can use the public key to encrypt
information, but only the private key owner can decrypt the information.

·     Digital signature—The key owner uses the private key to digitally sign information to
be sent. The receiver decrypts the information with the sender's public key to
verify information authenticity.

RSA, DSA, and ECDSA can all perform digital
signature, but only RSA can perform encryption and decryption.

## FIPS compliance

The device supports the FIPS mode that
complies with NIST FIPS 140-2 requirements. Support for features, commands, and
parameters might differ in FIPS mode (see "Configuring FIPS") and
non-FIPS mode.

## Public key management tasks at a glance

To manage public keys, perform the
following tasks:

**1\.**[Creating a local key pair](#_Ref454890380)

**2\.**[Distributing a local host public key](#_Ref483081007)

Choose one of the following tasks:

¡     [Exporting a host public key](#_Ref408998163)

¡     [Displaying a host public key](#_Ref291838381)

To enable the peer device to authenticate
the local device, you must distribute the local device's public key to the peer
device.

**3\.**[Configuring a peer host public key](#_Ref454890390)

Choose one of the following tasks:

¡     [Importing a peer host public key from a
public key file](#_Ref476221090)

¡     [Entering a peer host public key](#_Ref476221096)

To encrypt information sent to a peer
device or authenticate the digital signature of the peer device, you must
configure the peer device's public key on the local device.

**4\.**(Optional.) [Destroying a local key pair](#_Ref454890398)

## Creating a local key pair

#### Restrictions and guidelines

When you create a local key pair, follow
these guidelines:

·     The key algorithm must be the same as required
by the security application.

·     When you create an RSA or DSA key pair, enter an
appropriate key modulus length at the prompt. The longer the key modulus
length, the higher the security, and the longer the key generation time.

When you create an ECDSA key pair, choose
the appropriate elliptic curve. The elliptic curve determines the ECDSA key
length. The longer the key length, the higher the security, and the longer the
key generation time.

See [Table 1](#_Ref513628843) for more
information about key modulus lengths and key lengths.

·     If you do not assign the key pair a name, the
system assigns the default name to the key pair and marks the key pair as default. You can also assign the default name to another
key pair, but the system does not mark the key pair as default.
The key pair name must be unique among all manually named key pairs that use
the same key algorithm. If a name conflict occurs, the system asks whether you
want to overwrite the existing key pair.

·     The key pairs are automatically saved and can
survive system reboots.

Table 1 A comparison of different types of asymmetric key algorithms

| Type | Generated key pairs | Modulus/key length |
| --- | --- | --- |
| RSA | ·     In non-FIPS mode:  ¡     One host key pair, if you specify a key pair name.  ¡     One server key pair and one host key pair, if you do not specify a key pair name. Both key pairs use their default names.  ·     In FIPS mode: One host key pair.  NOTE:  Only SSH 1.5 uses the RSA server key pair. | RSA key modulus length:  ·     In non-FIPS mode: 512 to 4096 bits, 1024 bits by default.  To ensure security, use a minimum of 768 bits.  ·     In FIPS mode: A multiple of 256 bits in the range of 2048 to 4096 bits, 2048 bits by default. || DSA | One host key pair. | DSA key modulus length:  ·     In non-FIPS mode: 512 to 2048 bits, 1024 bits by default.  To ensure security, use a minimum of 768 bits.  ·     In FIPS mode: 2048 bits. || ECDSA | One host key pair. | ECDSA key length:  ·     In non-FIPS mode: 192, 256, 384, or 521 bits.  ·     In FIPS mode: 256, 384, or 521 bits. |



‌

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Create a local key pair.

In non-FIPS mode:

public-key local create { dsa \| ecdsa \[ secp192r1 \| secp256r1 \| secp384r1 \| secp521r1 ] \| rsa } \[ name key-name ]

In FIPS mode:

public-key local create
{ dsa \| ecdsa \[ secp256r1 \| secp384r1 \| secp521r1 ] \| rsa } \[ name key-name ] 

## Distributing a local host public key

### About distribution of local host public keys

You must distribute a local host public key
to a peer device so the peer device can perform the following operations:

·     Use the public key to encrypt information sent
to the local device.

·     Authenticate the digital signature signed by the
local device.

To distribute a local host public key, you
must first export or display the key.

·     Export a host public key:

¡     Export
a host public key to a file.

¡     Export
a host public key to the monitor screen, and then save it to a file.

After the key is exported to a file,
transfer the file to the peer device. On the peer device, import the key from
the file.

·     Display a host public key.

After the key is displayed, record the
key, for example, copy it to an unformatted file. On the peer device, you must
literally enter the key.

### Exporting a host public key

#### Restrictions and guidelines

When you export a host public key, follow
these restrictions and guidelines:

·     If you specify a file name in the command, the
command exports the key to the specified file.

·     If you do not specify a file name, the command
exports the key to the monitor screen. You must manually save the exported key
to a file.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Export a local host public key.

¡     Export
an RSA host public key:

In non-FIPS mode:

public-key local export rsa \[ name key-name ] { openssh \| ssh1 \| ssh2 } \[ filename ]

In FIPS mode:

public-key local export rsa \[ name key-name ] { openssh \| ssh2 } \[ filename ]

¡     Export
an ECDSA host public key.

public-key local export ecdsa \[ name key-name ] { openssh \| ssh2 } \[ filename ]

¡     Export
a DSA host public key.

public-key local export dsa \[ name key-name ] { openssh \| ssh2 } \[ filename ]

### Displaying a host public key

Perform the following tasks in any view:

·     Display local RSA public keys.

display public-key local rsa public \[ name key-name ]

Do not distribute the RSA server public
key serverkey
(default) to a peer device.

·     Display local ECDSA public keys.

display public-key local ecdsa public \[ name key-name ]

·     Display local DSA public keys.

display public-key local dsa public \[ name key-name ]

## Configuring apeer host public key

### About peer host public key configuration

To encrypt information sent to a peer
device or authenticate the digital signature of the peer device, you must configure
the peer device's public key on the local device.

You can configure the peer host public key
by using the following methods:

·     Import the peer host public key from a public
key file (recommended).

·     Manually enter (type or copy) the peer host
public key.

For information about how to obtain the
host public key of a device, see "[Distributing a local host public key](#_Ref483081032)." 

### Restrictions and guidelines for peer host public key configuration

When you configure a peer host public key,
follow these restrictions and guidelines:

·     When you manually enter the peer host public
key, make sure the entered key is in the correct format. To obtain the peer
host public key in the correct format, use the display
public-key local public command to display the
public key on the peer device and record the key. The format of the public key
displayed in any other way might be incorrect. If the key is not in the correct
format, the system discards the key and displays an error message.

·     Always import rather than enter the peer host
public key if you are not sure whether the device supports the format of the
recorded peer host public key.

### Importing a peer host public key from a public key file

#### About this task

Before you perform this task, make sure you
have exported the host public key to a file on the peer device and obtained the
file from the peer device. For information about exporting a host public key,
see "[Exporting a host public key](#_Ref408998163)."

After you import the key, the system
automatically converts the imported public key to a string in the Public Key
Cryptography Standards (PKCS) format.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Import a peer host public key from a public
key file.

public-key peer keyname import sshkey filename

By default, no peer host public keys
exist.

### Entering a peer host public key

#### About this task

Before you perform this task, make sure you
have displayed the key on the peer device and recorded the key. For information
about displaying a host public key, see "[Displaying a host public key](#_Ref291838381)."

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Specify a name for the peer host public key
and enter public key view.

public-key peer keyname

**3\.**Type or copy the key.

You can use spaces and carriage returns,
but the system does not save them.

**4\.**Exit public key view.

peer-public-key end

When you exit public key view, the system
automatically saves the peer host public key.

## Destroying a local key pair

#### About this task

To ensure security, destroy the local key
pair and generate a new key pair in any of the following situations:

·     The local key has leaked. An intrusion event might
occur.

·     The storage media of the device is replaced.

·     The local certificate has expired. For more
information about local certificates, see "Configuring PKI."

#### Procedure

**1\.**Enter system view.

system-view

2\.     Destroy a local key pair.

public-key local destroy { dsa \| ecdsa \| rsa } \[ name key-name ]

## Display and maintenance commands for public keys

Execute display
commands in any view.

 

| Task | Command |
| --- | --- |
| Display local public keys. | display public-key local { dsa \| ecdsa \| rsa } public \[ name key-name ] || Display peer host public keys. | display public-key peer \[ brief \| name publickey-name ] |


‌

## Examples of public key management

### Example:Entering apeer host public key

#### Network configuration

As shown in [Figure 2](#_Ref137616024), to prevent
illegal access, Device B authenticates Device A through a digital signature. Before
configuring authentication parameters on Device B, use the following procedure
to configure the public key of Device A on Device B:

·     Create RSA key pairs on Device A and display the
public keys of the RSA key pairs.

·     Manually specify the RSA host public key of
Device A on Device B.

Figure 2 Network
diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705412_x_Img_x_png_1_2216072_294551_0.png)

‌

#### Procedure

**1\.**Configure Device A:

\# Create local RSA key pairs with the default
names on Device A, and use the default key modulus length (1024 bits).

\<DeviceA\> system-view

\[DeviceA] public-key local
create rsa

The range of public key
modulus is (512 \~ 4096).

If the key modulus is greater
than 512, it will take a few minutes.

Press CTRL\+C to abort.

Input the modulus length
\[default \= 1024]:

Generating Keys...

...

Create the key pair
successfully.

\# Display all local RSA public keys.

\[DeviceA] display public-key
local rsa public

\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=

Key name: hostkey (default)

Key type: RSA

Key length: 1024

Time when key pair created:
16:48:31 2011/05/12

Key code:

  
30819F300D06092A864886F70D010101050003818D0030818902818100DA3B90F59237347B

  
8D41B58F8143512880139EC9111BFD31EB84B6B7C7A1470027AC8F04A827B30C2CAF79242E

  
45FDFF51A9C7E917DB818D54CB7AEF538AB261557524A7441D288EC54A5D31EFAE4F681257

   6D7796490AF87A8C78F4A7E31F0793D8BA06FB95D54EBB9F94EB1F2D561BF66EA27DFD4788

  
CB47440AF6BB25ACA50203010001

\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=

Key name: serverkey (default)

Key type: RSA

Key length: 768

Time when key pair created:
16:48:31 2011/05/12

Key code:

  
307C300D06092A864886F70D0101010500036B003068026100C9451A80F7F0A9BA1A90C7BC

  
1C02522D194A2B19F19A75D9EF02219068BD7FD90FCC2AF3634EEB9FA060478DD0A1A49ACE

  
E1362A4371549ECD85BA04DEE4D6BB8BE53B6AED7F1401EE88733CA3C4CED391BAE633028A

   AC41C80A15953FB22AA30203010001

**2\.**Configure Device B:

\# Enter the host public key of Device A in
public key view. The key must be literally the same as displayed on Device A.

\<DeviceB\> system-view

\[DeviceB] public-key peer
devicea

Enter public key view. Return
to system view with "peer-public-key end" command.

\[DeviceB-pkey-public-key-devicea]30819F300D06092A864886F70D010101050003818D0030818902818100DA3B90F59237347B

\[DeviceB-pkey-public-key-devicea]8D41B58F8143512880139EC9111BFD31EB84B6B7C7A1470027AC8F04A827B30C2CAF79242E

\[DeviceB-pkey-public-key-devicea]45FDFF51A9C7E917DB818D54CB7AEF538AB261557524A7441D288EC54A5D31EFAE4F681257

\[DeviceB-pkey-public-key-devicea]6D7796490AF87A8C78F4A7E31F0793D8BA06FB95D54EBB9F94EB1F2D561BF66EA27DFD4788

\[DeviceB-pkey-public-key-devicea]CB47440AF6BB25ACA50203010001

\# Save the public key and return to
system view.

\[DeviceB-pkey-public-key-devicea]
peer-public-key end

#### Verifying the configuration

\# Verify that the peer host public key
configured on Device B is the same as the key displayed on Device A.

\[DeviceB] display public-key peer
name devicea

 

\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=

Key name: devicea

Key type: RSA

Key length: 1024

Key code:

  
30819F300D06092A864886F70D010101050003818D0030818902818100DA3B90F59237347B

  
8D41B58F8143512880139EC9111BFD31EB84B6B7C7A1470027AC8F04A827B30C2CAF79242E

  
45FDFF51A9C7E917DB818D54CB7AEF538AB261557524A7441D288EC54A5D31EFAE4F681257

  
6D7796490AF87A8C78F4A7E31F0793D8BA06FB95D54EBB9F94EB1F2D561BF66EA27DFD4788

   CB47440AF6BB25ACA50203010001

### Example: Importing a public key from a public key file

#### Network configuration

As shown in [Figure 3](#_Ref197769122), Device
B authenticates Device A through a digital signature. Before configuring
authentication parameters on Device B, use the following procedure to configure
the public key of Device A on Device B:

·     Create RSA key pairs on Device A  and export the
RSA host public key to a file.

·     Import the RSA host public key of Device A from the
public key file to Device B.

Figure 3 Network diagram

![](https://resource.h3c.com/en/202407/12/20240712_11705413_x_Img_x_png_2_2216072_294551_0.png)

‌

#### Procedure

**1\.**Configure Device A:

\# Create local RSA key pairs with the default
names on Device A, and use the default key modulus length (1024 bits).

\<DeviceA\> system-view

\[DeviceA] public-key local
create rsa

The range of public key
modulus is (512 \~ 4096).

If the key modulus is greater
than 512, it will take a few minutes.

Press CTRL\+C to abort.

Input the modulus length
\[default \= 1024]:

Generating Keys...

...

Create the key pair
successfully.

\# Display all local RSA public keys.

\[DeviceA] display public-key
local rsa public

\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=

Key name: hostkey (default)

Key type: RSA

Key length: 1024

Time when key pair created:
16:48:31 2011/05/12

Key code:

  
30819F300D06092A864886F70D010101050003818D0030818902818100DA3B90F59237347B

  
8D41B58F8143512880139EC9111BFD31EB84B6B7C7A1470027AC8F04A827B30C2CAF79242E

  
45FDFF51A9C7E917DB818D54CB7AEF538AB261557524A7441D288EC54A5D31EFAE4F681257

  
6D7796490AF87A8C78F4A7E31F0793D8BA06FB95D54EBB9F94EB1F2D561BF66EA27DFD4788

   CB47440AF6BB25ACA50203010001

\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=

Key name: serverkey (default)

Key type: RSA

Key length: 768

Time when key pair created:
16:48:31 2011/05/12

Key code:

  
307C300D06092A864886F70D0101010500036B003068026100C9451A80F7F0A9BA1A90C7BC

   1C02522D194A2B19F19A75D9EF02219068BD7FD90FCC2AF3634EEB9FA060478DD0A1A49ACE

  
E1362A4371549ECD85BA04DEE4D6BB8BE53B6AED7F1401EE88733CA3C4CED391BAE633028A

  
AC41C80A15953FB22AA30203010001

\# Export the RSA host public key to file devicea.pub.

\[DeviceA] public-key local
export rsa ssh2 devicea.pub

\# Enable the FTP server, create an FTP
user with username ftp and password 123456TESTplat\&!, and configure the FTP user role as network-admin.

\[DeviceA] ftp server enable

\[DeviceA] local-user ftp

\[DeviceA-luser-manage-ftp] password
simple 123456TESTplat\&!

\[DeviceA-luser-manage-ftp] service-type
ftp

\[DeviceA-luser-manage-ftp] authorization-attribute
user-role network-admin

\[DeviceA-luser-manage-ftp]
quit

**2\.**Configure Device B:

\# Use FTP in binary mode to get public
key file devicea.pub from Device A.

\<DeviceB\> ftp 10.1.1.1

Connected to 10.1.1.1 (10.1.1.1).

220 FTP service ready.

User(10.1.1.1:(none)):ftp

331 Password required for ftp.

Password:

230 User logged in.

Remote system type is UNIX.

Using binary mode to transfer
files.

ftp\> binary

200 TYPE is now 8-bit binary

ftp\> get devicea.pub

227 Entering Passive Mode (10,1,1,1,118,252)

150 Accepted data connection

226 File successfully
transferred

301 bytes received in 0.003
seconds (98.0 kbyte/s)

ftp\> quit

221-Goodbye. You uploaded 0
and downloaded 1 kbytes.

221 Logout.

\# Import the host public key from key
file devicea.pub.

\<DeviceB\> system-view

\[DeviceB] public-key peer
devicea import sshkey devicea.pub

#### Verifying the configuration

\# Verify that the peer host public key configured
on Device B is the same as the key displayed on Device A.

\[DeviceB] display public-key peer
name devicea

\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=\=

Key name: devicea

Key type: RSA

Key length: 1024

Key code:

  
30819F300D06092A864886F70D010101050003818D0030818902818100DA3B90F59237347B

  
8D41B58F8143512880139EC9111BFD31EB84B6B7C7A1470027AC8F04A827B30C2CAF79242E

  
45FDFF51A9C7E917DB818D54CB7AEF538AB261557524A7441D288EC54A5D31EFAE4F681257

   6D7796490AF87A8C78F4A7E31F0793D8BA06FB95D54EBB9F94EB1F2D561BF66EA27DFD4788

   CB47440AF6BB25ACA50203010001

