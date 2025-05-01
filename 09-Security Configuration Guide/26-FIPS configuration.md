
# Configuring FIPS

## About FIPS

Federal Information Processing Standards (FIPS) was developed by the
National Institute of Standards and Technology (NIST) of the United States.
FIPS specifies the requirements for cryptographic modules.

### FIPS security levels

FIPS 140-2 defines four levels of security,
named Level 1 to Level 4, from low to high. The device supports Level 2\.

Unless otherwise noted, the term "FIPS"
refers to Level-2 FIPS 140-2 in this document.

### FIPS functionality

In FIPS mode, the device has strict
security requirements. It performs self-tests on cryptography modules to verify
that the modules are operating correctly.

A FIPS device also meets the functionality requirements
defined in Network Device Protection Profile (NDPP) of Common Criteria (CC).

### FIPS self-tests

To ensure correct operation of cryptography
modules, FIPS provides self-test mechanisms, including power-up self-tests and
conditional self-tests.

If a power-up self-test fails, the device
where the self-test process exists reboots. If a conditional self-test fails,
the system outputs a self-test failure message.

 

|  | NOTE:  If a self-test fails, contact H3C Support. |
| --- | --- |

‌

#### Power-up self-tests

The power-up self-test examines the
availability of FIPS-allowed cryptographic algorithms.

The device supports the following types of
power-up self-tests:

·     Known-answer test (KAT)

A cryptographic algorithm is run on data
for which the correct output is already known. The calculated output is
compared with the known answer. If they are not identical, the KAT test fails.

·     Pairwise conditional test (PWCT)

¡     Signature and authentication test—The
test is run when a DSA, RSA, or ECDSA asymmetrical key pair is generated. The
system uses the private key to sign the specific data, and then uses the public
key to authenticate the signed data. If the authentication is successful, the
test succeeds.

¡     Encryption and decryption test—The
test is run when an RSA asymmetrical key pair is generated. The system uses the
public key to encrypt a plain text string, and then uses the private key to
decrypt the encrypted text. If the decryption result is the same as the original
plain text string, the test succeeds.

The power-up
self-test examines the cryptographic algorithms listed in[Table 1](#_Ref468977865).

Table 1 Power-up self-tests list

| Type | Operations |
| --- | --- |
| KAT | Tests the following algorithms:  ·     SHA1, SHA224, SHA256, SHA384, and SHA512.  ·     HMAC-SHA1, HMAC-SHA224, HMAC-SHA256, HMAC-SHA384, and HMAC-SHA512.  ·     AES.  ·     RSA (signature and authentication).  ·     ECDH.  ·     DRBG.  ·     GCM.  ·     GMAC. || PWCT | Tests the following algorithms:  ·     RSA (signature and authentication).  ·     RSA (encryption and decryption).  ·     DSA (signature and authentication).  ·     ECDSA (signature and authentication). |


‌

#### Conditional self-tests

A conditional self-test runs when an
asymmetrical cryptographic module or a random number generator module is
invoked. Conditional self-tests include the following types:

·     PWCT signature and authentication—This test is run when a DSA or RSA asymmetrical key pair is
generated. The system uses the private key to sign the specific data, and then
uses the public key to authenticate the signed data. If the authentication is
successful, the test succeeds.

·     Continuous random number
generator test—Runs when a random number is
generated. The system compares the generated random number with the previously
generated random number. If the two numbers are the same, the test fails. This
test also runs when a DSA or RSA asymmetrical key pair is generated.

## Restrictions and guidelines: FIPS

#### Requirements for key pairs and passwords

Before you reboot the device to enter FIPS
mode, the system automatically removes all key pairs configured in non-FIPS
mode and all FIPS-incompliant digital certificates. FIPS-incompliant digital
certificates are MD5-based certificates with a key modulus length less than
2048 bits. You cannot log in to the device through SSH after the device enters
FIPS mode. To log in to the device in FIPS mode through SSH, log in to the
device through a console port and create a key pair for the SSH server.

The password for entering the device in
FIPS mode must comply with the password control policies, such as password
length, complexity, and aging policy. When the aging timer for a password expires,
the system prompts you to change the password. If you adjust the system time
after the device enters FIPS mode, the login password might expire before the
next login, because the original system time is typically much earlier than the
actual time.

#### Configuration rollback guidelines

Configuration rollback is supported in FIPS
mode and also during a switch between FIPS mode and non-FIPS mode. After a configuration
rollback between FIPS mode and non-FIPS mode, perform the following tasks:

**1\.**Delete the local user and configure a new
local user. Local user attributes include password, user role, and service type.

**2\.**Save the current configuration file.

**3\.**Specify the current configuration file as
the startup configuration file.

**4\.**Reboot the device. The new configuration
takes effect after the reboot. During this process, do not exit the system or
perform other operations.

If a device enters FIPS or non-FIPS mode
through automatic reboot, configuration rollback fails. To support configuration
rollback, you must execute the save command
after the device enters FIPS or non-FIPS mode.

#### IRF compatibility

All devices in an IRF fabric must be
operating in the same mode, whether in FIPS mode or non-FIPS mode.

To enable FIPS mode for an IRF fabric, you
must reboot the entire IRF fabric.

#### Feature changes in FIPS mode

After the system enters FIPS mode, the
following feature changes occur:

·     The user login authentication mode can only be
scheme.

·     The FTP/TFTP server and client are disabled.

·     The Telnet server and client are disabled.

·     The HTTP server is disabled.

·     SNMPv1 and SNMPv2c are disabled. Only SNMPv3 is
available.

·     The SSL server supports only TLS1.0, TLS1.1, and
TLS1.2.

·     The SSH server does not support SSHv1 clients or
DSA key pairs.

·     The generated RSA and DSA key pairs must have a
modulus length of 2048 bits.

When the device acts as a server to
authenticate a client through the public key, the key pair for the client must
also have a modulus length of 2048 bits.

·     The generated ECDSA key pairs must have a
modulus length of more than 256 bits.

When the device acts as a server to authenticate
a client through the public key, the key pair for the client must also have a
modulus length of more than 256 bits.

·     SSH, SNMPv3, IPsec, and SSL do not support DES,
3DES, RC4, or MD5.

·     The password control feature cannot be disabled
globally. The undo password-control enable command does not take effect.

·     An AAA shared key, IKE pre-shared key, or SNMPv3
authentication key must have at least 15 characters and must contain uppercase
and lowercase letters, digits, and special characters.

·     The password for a device management local user
and password for switching user roles must comply with the password control policies.
By default, the password must have at least 15 characters and must contain
uppercase and lowercase letters, digits, and special characters.

## Entering FIPS mode

### About entering FIPS mode

For the device to enter FIPS mode, you can use
one of the following methods:

·     Automatic reboot—The system automatically performs the following operations:

**a.**Prompts you to specify the username and
password for the next login.

**b.**Creates a default FIPS configuration file
named fips-startup.cfg.

**c.**Specifies the default FIPS configuration file
as the startup configuration file.

**d.**Reboots and loads the default FIPS
configuration file to enter the FIPS mode.

·     Manual reboot—You must complete the required configuration tasks and reboot the
device manually.

### Restrictions and guidelines

After you execute the fips mode
enable command, the system prompts you to choose
a reboot method. 

·     If you do not make a choice within 30 seconds or
press Ctrl\+C, the
system enables FIPS mode and waits for you to manually complete the FIPS mode
configuration tasks. You must complete the tasks or execute the undo fips mode
enable command before saving the running
configuration and rebooting the device. 

·     If you select the automatic reboot method, you
can press Ctrl\+C to abort
both the interactive FIPS mode configuration process and the fips mode
enable command.

### Using the automatic reboot method to enter FIPS mode

#### Prerequisites

To ensure login password effectiveness
under the password control policies, set the correct system time.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enable FIPS mode.

fips mode enable

By default, the FIPS mode is disabled.

**3\.**After the reboot method choice prompt
appears, enter Y within 30 minutes.

The system starts the interactive FIPS
mode configuration process.

 

| CAUTION | CAUTION:  System reboot might interrupt ongoing services. Please perform the previous operations with caution. |
| --- | --- |

‌

**4\.**Enter the login username and password as
prompted.

The password must have a minimum of 15 characters
and must contain uppercase and lowercase letters, digits, and special characters.
After you enter the username and password, the device performs the following
operations:

¡     Creates
a device management local user that uses the entered username and password.

¡     Assigns
the user the terminal service and the network-admin user role.

¡     Saves
the running configuration and specifies the configuration file as the startup
configuration file.

¡     Reboots,
loads the startup configuration file, and enters FIPS mode.

To log in to the device, you must enter the
configured username and password. After login, you are identified as the FIPS
mode crypto officer.

### Using the manual reboot method to enter FIPS mode

#### Prerequisites

**1\.**To ensure login password effectiveness under
the password control policies, set the correct system time.

**2\.**Configure the password control feature.

**a.**Enable the password control feature
globally.

**b.**Configure password control policies.

\-     Set the number of character types a password must contain to 4\.

\-     Set the minimum number of characters for each type to one character.

\-     Set the minimum length for a user password to 15 characters.

For more information about the password
control feature, see password control in Security
Configuration Guide.

**3\.**Configure a local user.

¡     Create
a device management local user.

¡     Specify
a password that complies with the password control policies.

¡     Assign
the terminal service to the user.

¡     Assign
the network-admin user role to the user.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enable FIPS mode.

fips mode enable

By default, the FIPS mode is disabled.

**3\.**After the reboot method choice prompt
appears, enter N.

The system enables FIPS mode and waits
for you to complete the FIPS mode configuration tasks. Before rebooting the
device to enter FIPS mode, do not execute any commands except for save and commands used to prepare for entering FIPS mode. If you execute
any other commands, the commands might not take effect.

 

| CAUTION | CAUTION:  ·     When you enable or disable FIPS mode, the system prompts you to select the reboot method before it reboots. System reboot might interrupt ongoing services. Perform this operation with caution.  ·     If you select the manual reboot method to enable FIPS mode, you must manually complete the configurations for entering FIPS mode. Otherwise, you cannot log in to the system correctly. |
| --- | --- |

‌

**4\.**Save the running configuration and specify the
configuration file as the startup configuration file.

**5\.**Delete the .mdb startup configuration file.

When loading a .mdb configuration file, the
device loads all settings in the file. The settings that are not supported in
FIPS mode might affect device operation.

**6\.**Reboot the device.

The device reboots, loads the startup
configuration file, and enters FIPS mode. To log in to the device, you must
enter the configured username and password. After login, you are identified as the
FIPS mode crypto officer.

## Manuallytriggering self-tests

#### About this task

You can manually trigger FIPS self-tests to
verify operation of cryptography modules anytime as required. The triggered
self-tests are the same as the power-up self-tests. 

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Trigger self-tests.

fips self-test

 

| CAUTION | CAUTION:  A successful self-test requires that all cryptographic algorithms pass the self-test. If the self-test fails, the device where the self-test process exists reboots. |
| --- | --- |

‌

## Exiting FIPS mode

#### About this task

After you disable FIPS mode and reboot the
device, the device operates in non-FIPS mode.

For the device to exit FIPS mode, you can
use one of the following reboot methods:

·     Automatic reboot—The system automatically creates a default non-FIPS configuration
file named non-fips-startup.cfg, specifies the file
as the startup configuration file, and reboots to enter non-FIPS mode. You can
log in to the device without providing username or password.

·     Manual reboot—You must manually complete the configuration tasks for entering
non-FIPS mode, and then reboot the device. To log in to the device after the
reboot, you must enter user information as required by the authentication mode
settings.

The following are the default
authentication mode settings:

¡     VTY line—Password authentication.

¡     AUX line—Authentication is disabled. 

You can modify the authentication settings
as needed.

#### Using the automatic reboot method to exit FIPS mode

**1\.**Enter system view.

system-view

**2\.**Disable FIPS mode.

undo fips mode enable

By default, the FIPS mode is disabled.

**3\.**Select the automatic reboot method.

 

| CAUTION | CAUTION:  System reboot might interrupt ongoing services. Please perform the previous operations with caution. |
| --- | --- |

‌

#### Using the manual reboot method to exit FIPS mode

**1\.**Enter system view.

system-view

**2\.**Disable FIPS mode.

undo fips mode enable

By default, the FIPS mode is disabled.

**3\.**Select the manual reboot method.

 

| CAUTION | CAUTION:  System reboot might interrupt ongoing services. Please perform the previous operations with caution. |
| --- | --- |

‌

**4\.**Configure login authentication settings.

¡     If you
logged in to the device through SSH, perform the following tasks without disconnecting
the current user line:

\-     Set the authentication mode to scheme
for VTY lines.

\-     Specify the username and password. If you do not specify the
username or password, the device uses the current username and password.

¡     If
you logged in to the device through a console port, configure login authentication
settings for the current type of user lines as described in the following table:

 

| Current login method | Login authentication requirements |
| --- | --- |
| Scheme | Set the authentication to scheme and specify the username and password. If you do not specify the username or password, the device uses the current username and password. || Password | Set the authentication to password and specify the password. If you do not specify the password, the device uses the current password. || None | Set the authentication to none. |



‌

**5\.**Save the running configuration and specify
the file as the startup configuration file.

**6\.**Delete the .mdb startup configuration file.

**7\.**Reboot the device.

## Display and maintenance commands for FIPS

Execute display commands in any view.

 

| Task | Command |
| --- | --- |
| Display the version number of the device algorithm base. | display crypto version || Display the FIPS mode state. | display fips status |


‌

## FIPS configuration examples

### Example: Entering FIPS mode through automatic reboot

#### Network configuration

Use the automatic reboot method to enter
FIPS mode, and use a console port to log in to the device in FIPS mode.

#### Procedure

\# If you want to
save the current configuration, execute thesave command before you enable FIPS mode.

\# Enable FIPS mode and choose the automatic
reboot method to enter FIPS mode. Set the username to root
and the password to 12345zxcvb!@#$%ZXCVB.

\<Sysname\> system-view

\[Sysname] fips mode enable

FIPS mode change requires a device
reboot. Continue? \[Y/N]:y

Reboot the device automatically?
\[Y/N]:y

The system will create a new startup
configuration file for FIPS mode. After you set the login username and password
for FIPS mode, the device will reboot automatically.

Enter username(1-55 characters):root

Enter password(15-63 characters):

Confirm password:

Waiting for reboot... After reboot,
the device will enter FIPS mode.

#### Verifying the configuration

After the device reboots, enter a username
of root and a password of 12345zxcvb!@#$%ZXCVB.
The system prompts you to configure a new password. After you configure the new
password, the device enters FIPS mode. The new password must be different from
the previous password. It must include at least 15 characters, and contain uppercase
and lowercase letters, digits, and special characters. For more information
about the requirements for the password, see the system output.

Press ENTER to get started.

login: root

Password:

First login or password reset. For
security reason, you need to change your password. Please enter your password.

old password:

new password:

confirm:

Updating user information. Please
wait ... ...

…

\<Sysname\>  

\# Display the FIPS mode state.

\<Sysname\> display fips status

FIPS mode is enabled.

\# Display the default configuration file.

\<Sysname\> more fips-startup.cfg

\#

 password-control enable

\#

local-user root class manage

 service-type terminal

 authorization-attribute user-role
network-admin

\#

 fips mode enable

\#

return

 

\<Sysname\>

### Example: Entering FIPS mode through manual reboot

#### Network configuration

Use the manual reboot method to enter FIPS
mode, and use a console port to log in to the device in FIPS mode.

#### Procedure

\# Enable the password control feature
globally.

\<Sysname\> system-view

\[Sysname] password-control enable

\# Set the number of character types a
password must contain to 4, and set the minimum number of characters for each type
to one character.

\[Sysname] password-control
composition type-number 4 type-length 1

\# Set the minimum length of user passwords to
15 characters.

\[Sysname] password-control length 15

\# Add a local user account for device
management, including a username of test, a
password of 12345zxcvb!@#$%ZXCVB, a user role of network-admin, and a service type of terminal.

\[Sysname] local-user test class
manage

\[Sysname-luser-manage-test] password
simple 12345zxcvb!@#$%ZXCVB

\[Sysname-luser-manage-test]
authorization-attribute user-role network-admin

\[Sysname-luser-manage-test]
service-type terminal

\[Sysname-luser-manage-test] quit

\# Enable FIPS mode, and choose the manual
reboot method to enter FIPS mode.

\[Sysname] fips mode enable

FIPS mode change requires a device
reboot. Continue? \[Y/N]:y

Reboot the device automatically?
\[Y/N]:n

Change the configuration to meet FIPS
mode requirements, save the configuration to the next-startup configuration
file, and then reboot to enter FIPS mode.

\# Save the current configuration to the
root directory of the storage medium, and specify it as the startup
configuration file.

\[Sysname] save

The current configuration will be
written to the device. Are you sure? \[Y/N]:y

Please input the file name(\*.cfg)\[flash:/startup.cfg]

(To leave the existing filename
unchanged, press the enter key):

flash:/startup.cfg exists, overwrite?
\[Y/N]:y

Validating file. Please wait...

Saved the current configuration to
mainboard device successfully.

\[Sysname] quit

\# Delete the startup configuration file in
binary format.

\<Sysname\> delete
flash:/startup.mdb

Delete flash:/startup.mdb?\[Y/N]:y

Deleting file
flash:/startup.mdb...Done.

\# Reboot the device.

\<Sysname\> reboot

#### Verifying the configuration

After the device
reboots, enter a username of test and a password of
12345zxcvb!@#$%ZXCVB. The system prompts you to
configure a new password. After you configure the new password, the device
enters FIPS mode. The new password must be different from the previous
password. It must include at least 15 characters, and contain uppercase and
lowercase letters, digits, and special characters. For more information about the
requirements for the password, see the system output.

Press ENTER to get started.

login: test

Password:

First login or password reset. For
security reason, you need to change your pass

word. Please enter your password.

old password:

new password:

confirm:

Updating user information. Please
wait ... ...

…

\<Sysname\>

\# Display the FIPS mode state.

\<Sysname\> display fips status

FIPS mode is enabled.

### Example: Exiting FIPS mode through automatic reboot

#### Network configuration

After logging in to the device in FIPS mode
through a console port, use the automatic reboot method to exit FIPS mode.

#### Procedure

\# Disable FIPS mode.

\[Sysname] undo fips mode enable

FIPS mode change requires a device
reboot. Continue? \[Y/N]:y

The system will create a new startup
configuration file for non-FIPS mode and then reboot automatically. Continue?
\[Y/N]:y

Waiting for reboot... After reboot,
the device will enter non-FIPS mode.

#### Verifying the configuration

After the device reboots, you can enter the
system.

\<Sysname\>

\# Display the FIPS mode state.

\<Sysname\> display fips status

FIPS mode is disabled.

### Example: Exiting FIPS mode through manual reboot

#### Network configuration

After logging in to the device in FIPS mode
through the console port with username test and
password 12345zxcvb!@#$%ZXCVB, use the manual
reboot method to exit FIPS mode.

#### Procedure

\# Disable FIPS mode.

\[Sysname] undo fips mode enable

FIPS mode change requires a device
reboot. Continue? \[Y/N]:y

The system will create a new startup
configuration file for non-FIPS mode, and then reboot automatically. Continue?
\[Y/N]:n

Change the configuration to meet
non-FIPS mode requirements, save the configuration to the next-startup
configuration file, and then reboot to enter non-FIPS mode.

\# Save the current configuration to the
root directory of the storage medium, and specify it as the startup
configuration file.

\[Sysname] save

The current configuration will be
written to the device. Are you sure? \[Y/N]:y

Please input the file name(\*.cfg)\[flash:/startup.cfg]

(To leave the existing filename
unchanged, press the enter key):

flash:/startup.cfg exists, overwrite?
\[Y/N]:y

Validating file. Please wait...

Saved the current configuration to
mainboard device successfully.

\[Sysname] quit

\# Delete the startup configuration file in
binary format.

\<Sysname\> delete
flash:/startup.mdb

Delete flash:/startup.mdb?\[Y/N]:y

Deleting file
flash:/startup.mdb...Done.

\# Reboot the device.

\<Sysname\> reboot

#### Verifying the configuration

After the device reboots, authentication is
disabled for console login by default. You can press Enter
to enter non-FIPS mode without authentication.

Press ENTER to get started.

login: test

Password:

Last successfully login time:…

…

\<Sysname\>

\# Display the FIPS mode state.

\<Sysname\> display fips status

FIPS mode is disabled.

