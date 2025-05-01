
# Managing configuration files

## About configuration file management

You can manage configuration files from the
CLI or the BootWare menu. The following information explains how to manage
configuration files from the CLI.

A configuration file saves a set of commands
for configuring software features on the device. You can save any configuration
to a configuration file so the configuration can survive a reboot. You can also
back up configuration files to a host for future use.

### Configuration types

#### Initial configuration

Initial configuration is the collection of
initial default settings for the configuration commands in software. 

The device starts up with the initial
configuration if you access the BootWare menu and select the Skip Current System Configuration option. In this
situation, the device might also be described as starting up with empty
configuration.

No commands are available to display the initial
configuration. To view the initial default settings for the configuration
commands, see the Default sections in the command references.

#### Factory defaults

Factory defaults are custom basic settings that
came with the device. Factory defaults vary by device models and might differ
from the initial default settings for the commands.

The device starts up with the factory
defaults if no next-startup configuration files are available.

To display the factory defaults, use the display
default-configuration command.

#### Startup configuration

The device uses startup configuration to
configure software features during startup. After the device starts up, you can
specify the configuration file to be loaded at the next startup. This
configuration file is called the next-startup configuration file. The
configuration file that has been loaded is called the current startup
configuration file. 

You can display the startup configuration
by using one of the following methods:

·     To display the contents of the current startup
configuration file, execute the display current-configuration command before changing the configuration after the device reboots.

·     To display the contents of the next-startup
configuration file, use the display saved-configuration command.

·     Use the display startup command to display names of the current startup configuration file
and next-startup configuration files. Then, you can use the more command to display the contents of the specified startup
configuration file.

#### Running configuration

The running configuration includes unchanged
startup settings and new settings. The running configuration is stored in
memory and is cleared at a device reboot or power off. To use the running
configuration after a power cycling or reboot, save it to a configuration file.

To display the running configuration, use
the
display current-configuration command.

### Configuration file types and file selection process at startup

When you save the configuration, the system
saves the settings to a .cfg configuration file and to an .mdb file.

·     A .cfg configuration file is a human-readable
text file and its contents can be displayed by using the more command. Configuration files you specify for saving the configuration
must use the .cfg extension.

·     An .mdb file is a user-inaccessible binary file
that has the same name as the .cfg file. The device loads an .mdb file faster
than loading a .cfg file.

At startup, the device uses the following
procedure to identify the configuration file to load:

**1\.**The device searches for a valid .cfg
next-startup configuration file. For more information about the file selection
rules, see "[Next-startup configuration file
redundancy](#_Ref476746452)."

**2\.**If a valid .cfg next-startup configuration
file is found, the device searches for an .mdb file that has the same name and checksum
as the .cfg file.

**3\.**If a matching .mdb file is found, the device
starts up with the .mdb file. If none is found, the device starts up with the
.cfg file.

If no .cfg next-startup configuration files
are available, the device starts up with the factory defaults.

Unless otherwise stated, the term
"configuration file" in this document refers to a .cfg configuration
file.

### Next-startup configuration file redundancy

You can specify one main next-startup configuration
file and one backup next-startup configuration file for redundancy.

At startup, the device tries to select the .cfg
startup configuration in the following order:

**1\.**The main next-startup configuration file.

**2\.**The backup next-startup configuration file
if the main next-startup configuration file does not exist or is corrupt.

If no next-startup configuration files are
available, the device starts up with the factory defaults.

### Configuration file content organization and format

| IMPORTANT | IMPORTANT:  To run on the device, a configuration file must meet the content and format requirements. To ensure a successful configuration load, rollback, or restoration, use a configuration file created on the device. If you edit the configuration file, make sure all edits are compliant with the requirements. |
| --- | --- |

‌

Configuration restoration or rollback might
fail for a set of interdependent commands if the operation fails for one of
them.

A configuration file must meet the
following requirements:

·     All commands are saved in their complete form.

·     No command lines contain invalid characters.

 

| IMPORTANT | IMPORTANT:  Some command lines (for example, thesysname command) cannot contain question marks (?) or horizontal tabs (\\t). If the system loads a configuration file that contains the sysname abc?? command line at startup, the system will ignore the command line and use the default system name. If the system uses that configuration file to roll back or restore the configuration, the system name will not be rolled back or restored. |
| --- | --- |

‌

·     Commands are sorted into sections by different command
views, including system view, interface views, protocol views, and user line
views.

·     Two adjacent sections are separated by a pound
sign (#).

·     The configuration file ends with the word return.

The following is a sample configuration
file excerpt:

\#

local-user root class manage

 password hash
$h$6$Twd73mLrN8O2vvD5$Cz1vgdpR4KoTiRQNE9pg33gU14Br2p1VguczLSVyJLO2huV5Syx/LfDIf8ROLtVErJ/C31oq2rFtmNuyZf4STw\=\=

 service-type ssh telnet terminal

 authorization-attribute user-role
network-admin

 authorization-attribute user-role
network-operator

\#

interface Vlan-interface1

 ip address 192.168.1.84
255.255.255.0

\#

### Configuration rollback

Configuration rollback allows you to replace
the running configuration with the configuration in a configuration file
without rebooting the device. You can use this feature for the following
purposes:

·     Reverting to a previous configuration state.

·     Adapting the running configuration to different
network environments.

## FIPS compliance

The device supports the FIPS mode that
complies with NIST FIPS 140-2 requirements. Support for features, commands, and
parameters might differ in FIPS mode and non-FIPS mode. For more information
about FIPS mode, see Security Configuration Guide.

## Enabling configuration encryption

#### About this task

Configuration encryption enables the device
to encrypt a startup configuration file automatically when it saves the running
configuration.

#### Restrictions and guidelines

After a configuration file is encrypted by
using this feature on the device, only devices that use the same encryption
algorithm for configuration encryption can decrypt the encrypted configuration
file. 

You cannot use the more command to view the contents of an encrypted configuration file.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enable configuration encryption.

configuration encrypt { private-key \| public-key }

By default, configuration encryption is
disabled.

## Assigning a user exclusive write access to the configuration

#### About this task

In a multiuser environment, configuration
conflicts or service exceptions might occur if multiple users are configuring
the device.

To make sure only one user can change the
running configuration, assign that user exclusive write access to the
configuration. This setting locks the configuration. Then, all other users will
have only read access to the configuration until the configuration is unlocked.


#### Restrictions and guidelines

To allow concurrent write accesses to the
configuration, you must unlock the configuration by executing the undo
configuration exclusive by-user-name user-name command. The user-name argument must
be the username used to lock the configuration. To obtain this username, use
the display
configuration exclusive by-user-name command.

#### Prerequisites

To perform this task, you must log in to
the system through scheme authentication. Scheme authentication requires a
username and password.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Assign a user exclusive write access to the
configuration. 

configuration exclusive by-user-name user-name

By default, the configuration is not
locked. No users have exclusive write access to the configuration.

## Disabling automatic system-wide next-startup configuration file operations

#### About this task

By default,automatic
system-wide next-startup configuration file operations are enabled. The system performs the following operations on all IRF subordinate
devices in addition to the master device:

·     Saves the running configuration to the next\-startup configuration
file on each member device when you execute the save \[ safely ] \[ backup \| main ] \[ force ] \[ changed ] command.

·     Deletes the next-startup configuration file on
each member device when you execute the reset saved-configuration command.

If you disable automatic system-widenext-startup
configuration file operations, the system saves the
running configuration or deletes the next-startup configuration file only on
the master device.

Automatic
system-wide operations ensure start-up configuration file consistency between
the master and subordinate devices. However, a system-wide operation takes more
time than an operation performed only on the master device. In addition, the
amount of time required to complete a system-wide configuration operation
increases as the amount of configuration data grows.

If you are disabling automatic system-wide operations
for faster configuration saving, be aware that the next-startup configuration
files will be inconsistent between the master device and the subordinate
devices.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Disable automatic system-wide next-startup
configuration file operations.

undo standby auto-update config

By default, next-startup configuration
file operations are automatically synchronized across the entire system.

## Saving the running configuration

#### About this task

When you save the running configuration to
a .cfg configuration file, you can specify the file as a next-startup
configuration file or not.

If you are specifying the file as a .cfg next-startup
configuration file, use one of the following methods to save the configuration:

·     Fast mode—Use the save command without the safely keyword. In
this mode, the device directly overwrites the target next-startup configuration
file. If a reboot or power failure occurs during this process, the next-startup
configuration file is lost. You must specify a new startup configuration file
after the device reboots (see "[Specifying a next-startup configuration
file](#_Ref493096025)").

·     Safe mode—Use the save command with the safely keyword. Safe
mode is slower than fast mode, but more secure. In safe mode, the system saves the
configuration in a temporary file and starts overwriting the target
next-startup configuration file after the save operation is complete. If a
reboot or power failure occurs during the save operation, the next-startup
configuration file is still retained. Use the safe mode if the power source is
not reliable or you are remotely configuring the device.

#### Restrictions and guidelines

To prevent the loss of next-startup
configuration, make sure no one reboots or power cycles the device while the
device is saving the running configuration.

When an IRF member device splits from the
IRF fabric, its settings are retained in memory but removed from the running
configuration on the IRF fabric. Saving the running configuration before the
IRF fabric recovers will remove the member device's settings from the
next-startup configuration file.

If you have saved the running configuration
before the member device rejoins the IRF fabric, perform the following steps to
restore the member device settings to the next-startup configuration file:

**1\.**Resolve the split issue.

**2\.**Reboot the member device to rejoin the IRF
fabric.

**3\.**After the member device rejoins the IRF
fabric, execute the display current-configuration command to verify that the member device's settings have been
restored from memory to the running configuration.

**4\.**Save the running configuration to the
next-startup configuration file on the IRF fabric.

 

| IMPORTANT | IMPORTANT:  To ensure a successful configuration restoration, make sure the IRF fabric has not rebooted after the member device left. |
| --- | --- |

‌

By default, the save \[ safely ] \[ backup \| main ] \[ force ] \[ changed ] command saves the configuration to all IRF member devices. To save
the configuration only to the master device, disable automatic system-wide
next-startup configuration file operations. For more information, see "[Disabling automatic system-wide
next-startup configuration file operations](#_Ref522347369)."

When an expansion interface card is removed
from the system, its settings are retained in memory but removed from the
running configuration on the system. Saving the running configuration before
installing the replacement card will remove the card's settings from the
next-startup configuration file.

If you have saved the running configuration
after removing a card, perform the following steps to restore the card settings
to the next-startup configuration file:

**1\.**Install the replacement card.

**2\.**After the replacement card comes online,
execute the display current-configuration command to verify that the card's settings have been automatically
restored from memory to the running configuration.

**3\.**Save the running configuration to the
next-startup configuration file.

 

| IMPORTANT | IMPORTANT:  To ensure a successful configuration restoration, make sure the system has not rebooted after the card was removed. |
| --- | --- |

‌

#### Procedure

To save the running configuration, perform
one of the following tasks in any view:

·     Save the running configuration to a
configuration file without specifying the file as a next-startup configuration
file.

save file-url \[ all \| slot slot-number ]

·     Save the running configuration to a
configuration file in the root directory of the storage medium, and specify the
file as a next-startup configuration file.

save \[ safely ] \[ backup \| main ] \[ force ] \[ changed ]

As a best practice, specify the safely keyword for reliable configuration saving.

 

| CAUTION | CAUTION:  Use the save or save binary-only command with caution. This command will overwrite the settings in the target configuration file. Carefully read the messages generated on the device when you use this command and make sure you fully understand the impact of this command when you execute it. |
| --- | --- |

‌

## Comparing configurations for their differences

#### About this task

You can compare configuration files or compare
a configuration file with the running configuration for their differences.

If you specify the next-startup
configuration for a comparison, the system selects the next-startup
configuration file to be compared with in the following order:

**1\.**The main next-startup configuration file.

**2\.**The backup next-startup configuration file if
the main next-startup configuration file is unavailable.

If both configuration files are
unavailable, the system displays a message indicating that no next-startup configuration
files exist.

#### Procedure

To compare configurations for their differences,
perform one of the following tasks in any view:

·     Display the differences that a configuration
file, the running configuration, or the next-startup configuration has as
compared with the specified source configuration file. 

display diff configfile file-name-s { configfile file-name-d \| current-configuration
\| startup-configuration
}

·     Display the differences that a configuration
file or next-startup configuration has as compared with the running
configuration.

display diff current-configuration { configfile file-name-d \| startup-configuration
}

·     Display the differences that a configuration
file has as compared with the next-startup configuration.

display diff startup-configuration configfile file-name-d

·     Display the differences that the running
configuration has as compared with the next-startup configuration. 

¡     Method
1:

display diff startup-configuration current-configuration

¡     Method 2:

display current-configuration diff

## Configuring configuration rollback

### Configuration rollback tasks at a glance

To configure configuration rollback,
perform the following tasks:

**1\.**[Setting configuration archive parameters](#_Ref474251406)

**2\.**[Archiving the running configuration](#_Ref454958961)

¡     [Enabling automatic configuration archiving](#_Ref454958896)

¡     [Manually archiving the running configuration](#_Ref474251439)

**3\.**[Rolling back configuration](#_Ref326684791)

### Setting configuration archive parameters

#### About this task

Before archiving the running configuration,
you must set a file directory and file name prefix for configuration archives. 

The archive directory can be located on the
local device or on a remote SCP server.

If you use local archiving, configuration archives
are named in the format of prefix\_serial number.cfg, for example, archive\_1.cfg
and archive\_2.cfg. The serial number is
automatically assigned from 1 to 1000, increasing by 1\. After the serial number
reaches 1000, it restarts from 1\.

If you change the file directory or file
name prefix on the local device, the following events occur:

·     The old configuration archives change to common
configuration files.

·     The configuration archive counter is reset. The serial number for new configuration
archives starts at 1\.

·     The display archive configuration command no longer displays the old configuration archives.

The configuration archive counter does not
restart when you delete configuration archives from the archive directory.
However, if the device reboots after all configuration archives have been
deleted, the configuration archive counter restarts. The serial number for new
configuration archives starts at 1\.

If you archive the running configuration to
a remote SCP server, configuration archives are named in the format of prefix\_YYYYMMDD\_HHMMSS.cfg (for example, archive\_20170526\_203430.cfg). The YYYYMMDD\_HHMMSS string represents the device system date and
time when the configuration archive was saved.

If you change the file directory or file
name prefix on the remote SCP server, the display archive configuration command no longer displays the old configuration archives saved
before the change.

#### Restrictions and guidelines for setting configuration archive parameters

Local archiving (the archive configuration
location command) and remote archiving (the archive
configuration server command) are mutually
exclusive. You cannot use the two features at the same time.

 

| IMPORTANT | IMPORTANT:  In FIPS mode, the device does not support archiving the running configuration to a remote SCP server. |
| --- | --- |

‌

With local configuration archiving, the
system deletes the oldest archive to make room for the new archive after the
maximum number of configuration archives is reached.

The following information applies to remote
configuration archiving: 

·     The maximum number of configuration archives on
a remote SCP server depends on the SCP server setting and is not restricted by
the archive
configuration max command.

·     If the remote SCP server already contains an
archive file with the same name as the new configuration archive file being transferred,
the new archive file overwrites the existing file. To avoid an undesirable
overwrite, back up the
configuration archives on the SCP server to a different directory.

The undo archive
configuration location command removes the local
configuration archive directory and file name prefix settings, but it does not
delete the configuration archives on the device. The command also performs the
following operations:

·     Disables both the manual and automatic
configuration archiving features.

·     Restores the default settings for the archive
configuration interval and archive
configuration max commands.

·     Clears the configuration archive information
displayed by using the display archive configuration command.

The undo archive configuration server command removes the remote configuration archive directory and file
name prefix settings, but it does not delete the configuration archives on the
server. The command also performs the following operations:

·     Disables both the manual and automatic
configuration archiving features.

·     Restores the default setting for the archive
configuration interval command.

·     Clears the configuration archive information
displayed by using the display archive configuration command.

#### Configuring local archiving parameters

**1\.**Enter system view.

system-view

**2\.**Set the directory and file name prefix for
archiving the running configuration to the local device.

archive configuration location directory filename-prefix filename-prefix

By default, no path or file name prefix
is set for configuration archives on the device, and the system does not regularly
save configuration.

In an IRF fabric, the configuration
archive directory must already exist on the master device and cannot include a
member ID.

**3\.**(Optional.) Set the maximum number of configuration
archives.

archive configuration max file-number

The default number is 5\.

Change the setting depending on the
amount of storage available on the device.

#### Configuring remote archiving parameters

**1\.**Enter system view.

system-view

**2\.**Set the directory and file name prefix for
archiving the running configuration on a remote SCP server.

archive configuration server scp { ipv4-address \| ipv6 ipv6-address } \[ port port-number ] \[ vpn-instance vpn-instance-name ] \[ directory directory ] filename-prefix
filename-prefix

By default, no path or file name prefix
is set for archiving the running configuration to a remote SCP server.

**3\.**Configure the username and password for accessing
the SCP server.

a.     Configure the username.

archive configuration server user user-name

By default, no username is configured for
accessing the SCP server.

**b.**Configure the password.

archive configuration server password { cipher \| simple } string

By default, no password is configured for
accessing the SCP server. 

Make sure the username and password are
the same as the SCP account settings on the SCP server. 

### Archiving the running configuration

#### About this task

The following are methods for archiving the
running configuration:

·     Automatic configuration
archiving—The system automatically archives the
running configuration at intervals as configured.

·     Manual configuration
archiving—You can disable automatic configuration archiving and manually archive
the running configuration if the configuration will not be changed very often. You
can also use this method before performing complicated configuration tasks. If
the configuration attempt fails, you can use the archive for configuration
recovery.

#### Restrictions and guidelines for archiving the running configuration

If you use local archiving, the configuration
archive feature saves the running configuration only on the master device. To
make sure the system can archive the running configuration after a
master/subordinate switchover, create the configuration archive directory on
all IRF members.

If a local archiving has started based on
the existing archive parameters when an archive parameter is changed, the
archive will still be retained in the old directory. However, the display archive
configuration command will not display the record
about this archiving.

When you modify parameters (for example,
the username or password) for remote archiving, make sure the changes are
consistent between the device and the server. A manual or automatic remote
archiving will fail if it has started before you change the device and the
server settings to be consistent.

#### Enabling automatic configuration archiving

**1\.**Enter system view.

system-view

**2\.**Enable automatic configuration archiving and
set the archiving interval.

archive configuration interval interval

By default, automatic configuration archiving
is disabled.

#### Manually archiving the running configuration

Manually archive the running configuration
in user view.

archive configuration

### Rolling back configuration

#### About this task

The configuration rollback feature compares
the running configuration against the specified replacement configuration file
and handles configuration differences as follows:

·     If a command in the running configuration is not
in the replacement file, the rollback feature executes the undo form of the command.

·     If a command in the replacement file is not in
the running configuration, the rollback feature adds the command to the running
configuration.

·     If a command has different settings in the
running configuration and the replacement file, the rollback feature replaces
the running command setting with the setting in the replacement file.

#### Restrictions and guidelines

To ensure a successful rollback, do not install
or remove expansion interface cards or perform a master/subordinate switchover while
the system is rolling back the configuration. 

The configuration rollback feature might
fail to reconfigure some commands in the running configuration for one of the
following reasons:

·     A command cannot be undone because prefixing the
undo keyword to the command does not result in a valid undo command. For example, if the undo form
designed for the A \[B] C command is undo A C, the configuration rollback feature cannot undo the A B C command. This is because the system does not recognize the undo A B C command.

·     A command (for example, a hardware-dependent
command) cannot be deleted, overwritten, or undone due to system restrictions.

·     The commands in different views are dependent on
each other.

·     Commands or command settings that the device
does not support cannot be added to the running configuration.

Make sure the replacement configuration
file is created by using the configuration archive feature or the save command on the local device. If the configuration file is not
created on the local device, make sure the command lines in the configuration
file are fully compatible with the local device.

If the replacement configuration file is
encrypted, make sure the device can decrypt it.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Roll the running configuration back to the
configuration defined by a configuration file.

configuration replace file filename

 

| CAUTION | CAUTION:  The configuration rollback feature replaces the running configuration with the configuration in a configuration file without rebooting the device. This operation will cause settings not in the replacement configuration file to be lost, which might cause service interruption. When you perform configuration rollback, make sure you fully understand its impact on your network. |
| --- | --- |

 

The specified configuration file must be saved
on the local system.

## Configuring configuration commit delay

#### About this task

This feature enables the system to
automatically remove the settings you made during a configuration commit delay
interval if you have not manually committed them.

You specify the configuration commit delay
interval by using the configuration commit delay timer. The system creates a rollback
point to record the configuration status when you start the configuration
commit delay timer. The settings you made during the configuration commit delay
interval takes effect immediately. However, these settings will be removed
automatically if you have not manually committed them before the configuration
commit delay timer expires. Then, the system returns to the configuration
status when the configuration commit delay timer started. 

This feature prevents a misconfiguration
from causing the inability to access the device and is especially useful when
you configure the device remotely. 

#### Restrictions and guidelines

When you use this feature, follow these
restrictions and guidelines:

·     In a multi-user context, make sure no one else
is configuring the device.

·     To avoid unexpected errors, do not perform any
operations during the configuration rollback.

·     You can reconfigure the configuration commit
delay timer before it expires to shorten or extend the commit delay interval. However,
the rollback point will not change.

·     The configuration commit delay feature is a
one-time setting. The feature is disabled with the rollback point removed when
the commit delay timer expires or after a manual commit operation is performed.
To use this feature again, you must restart the timer.

#### Procedure

**1\.**Enter system view.

system-view

**2\.**Enable the configuration commit delay
feature and start the commit delay timer.

configuration commit delay delay-time

**3\.**(Optional.) Commit the settings configured
after the commit delay timer started.

configuration commit

## Specifying a next-startup configuration file

#### Restrictions and guidelines

| CAUTION | CAUTION:  Using the undo startup saved-configuration command can cause an IRF split after the IRF fabric or an IRF member reboots. When you execute this command, make sure you understand its impact on your network. |
| --- | --- |

‌

As a best practice, specify different files
as the main and backup next-startup configuration files. 

The undo startup saved-configuration command changes the attribute of the main or backup next-startup configuration
file to NULL instead of deleting the file.

#### Prerequisites

In an IRF fabric, make sure the specified
configuration file is valid and has been saved to the root directory of a storage
medium on each member device. In addition, make sure the storage media are the
same type across all IRF member devices.

#### Procedure

**1\.**Specify a next-startup configuration file. Choose
one of the following methods:

¡     Execute
the following command in user view to specify a next-startup configuration file:

startup saved-configuration cfgfile \[ backup \| main ]

By default, no next-startup configuration
files are specified. 

¡     Execute
the following command in any view to save the running configuration to a file
and specify the file as a next-startup configuration file:

save \[ safely ] \[ backup \| main ] \[ force ]

For more information about this command,
see "[Saving the running configuration](#_Ref329076179)."

If you do not specify the backup or main keyword, this
command specifies the configuration file as the main next-startup configuration
file.

**2\.**(Optional.) Verify the configuration. Use
one of the following commands in any view:

¡     Display
the names of the configuration files for this startup and the next startup.

display startup

¡     Display
the contents of the configuration file for the next system startup.

display saved-configuration

## Backing up and restoring the main next-startup configuration file

### About backing up and restoring the main next-startup configuration file

You can back up the main next-startup
configuration file to a TFTP server or restore the main next-startup
configuration file from a TFTP server.

### Restrictions and guidelines for configuration backup and restoration

Configuration backup and restoration are
not supported in FIPS mode.

### Prerequisites for configuration backup and restoration

Before you back up or restore the main
next-startup configuration file, perform the following tasks: 

·     Make sure the following requirements are met:

¡     The
server is reachable.

¡     The
server is enabled with TFTP service.

¡     You
have read and write permissions to the server.

·     For configuration backup, use the display startup command to verify that the main next-startup configuration file has
been specified in user view. If no next-startup configuration file has been
specified or the specified configuration file does not exist, the backup
operation will fail.

### Backing up the main next-startup configuration file to a TFTP server

To back up the main next-startup
configuration file to a TFTP server, execute the following command in user view:

backup startup-configuration to { ipv4-server \| ipv6 ipv6-server } \[ dest-filename ] \[ vpn-instance
vpn-instance-name ]

### Restoring the main next-startup configuration file from a TFTP server

**1\.**Restore the main next-startup
configuration file from a TFTP server in user view.

restore startup-configuration from { ipv4-server \| ipv6 ipv6-server } src-filename \[ vpn-instance
vpn-instance-name ]

**2\.**(Optional.) Verify that the specified
configuration file has been set as the main next-startup configuration file. Use
one of the following commands in any view:

¡     Display
the names of the configuration files for this startup and the next startup.

display startup

¡     Display the contents of the configuration file for the next system
startup.

display saved-configuration

## Deleting a next-startup configuration file

#### About this task

You can perform this task to delete a next-startup
configuration file.

If both the main and backup next-startup
configuration files are deleted, the device uses the factory defaults at the
next startup.

To delete a file that is set as both main
and backup next-startup configuration files, you must execute both the reset saved-configuration backup command and the reset
saved-configuration main command. Using only one of the commands removes the specified file
attribute instead of deleting the file. 

For example, if the reset
saved-configuration backup command is executed, the backup next-startup configuration file
setting is set to NULL. However, the file is still used as the main file. To
delete the file, you must also execute the reset saved-configuration main command.

#### Procedure

To delete a next-startup configuration file,
execute the following command in user view:

reset saved-configuration \[ backup \| main ]

 

| CAUTION | CAUTION:  By default, this task permanently deletes a next-startup configuration file from all IRF member devices. To delete the configuration file only from the master device, disable automatic system-wide next-startup configuration file operations. For more information, see "[Disabling automatic system-wide next-startup configuration file operations](#_Ref522347369)." |
| --- | --- |

 

If you do not specify the backup or main keyword, the main
next-startup configuration is deleted.

## Display and maintenance commands for configuration files

Execute display commands
in any view and reset commands in user
view.

 

| Task | Command |
| --- | --- |
| Display configuration archive information. | display archive configuration || Display the running configuration. | display current-configuration \[ \[ configuration \[ module-name ] \| exclude-provision \| interface \[ interface-type \[ interface-number ] ] ] \| slot slot-number ] \[ by-section { begin \| exclude \| include } regular-expression ] || Display the differences that the running configuration has as compared with the next-startup configuration. | display current-configuration diff || Display the factory defaults. | display default-configuration || Display the differences between configurations. | ·     display diff configfile file-name-s { configfile file-name-d \| current-configuration \| startup-configuration }  ·     display diff current-configuration { configfile file-name-d \| startup-configuration }  ·     display diff startup-configuration { configfile file-name-d \| current-configuration } || Display the contents of the configuration file for the next system startup. | display saved-configuration \[ by-section { begin \| exclude \| include } regular-expression ] || Display the names of the configuration files for this startup and the next startup. | display startup || Display the valid configuration in the current view. | display this \[ all ] \[ by-section { begin \| exclude \| include } regular-expression ] || Display information about user-based configuration locking. | display configuration exclusive by-user-name || Delete next-startup configuration files. | reset saved-configuration \[ backup \| main ] |










‌

